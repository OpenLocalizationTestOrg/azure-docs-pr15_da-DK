<properties
    pageTitle="Realtid Twitter synspunkt analyse af resultatopgørelse med Stream Analytics | Microsoft Azure"
    description="Lær at bruge Stream Analytics til realtid Twitter synspunkt analyse. Finde en trinvis vejledning fra generering af begivenhed til data i et live dashboard."
    keywords="realtid twitter tendensanalyse, synspunkt analyse, sociale medier analyse og tendens analyse eksempel"
    services="stream-analytics"
    documentationCenter=""
    authors="jeffstokes72"
    manager="jhubbard"
    editor="cgronlun"/>

<tags
    ms.service="stream-analytics"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="big-data"
    ms.date="09/26/2016"
    ms.author="jeffstok"/>


# <a name="social-media-analysis-real-time-twitter-sentiment-analysis-in-azure-stream-analytics"></a>Sociale medier analyse: Twitter-realtid synspunkt analyse i Azure Stream Analytics

Lær, hvordan du opretter en synspunkt analyse løsning på sociale medier analytics ved at levere realtid Twitter begivenheder til Azure begivenhed Hubs. Du kan skrive en Azure Stream Analytics-forespørgsel for at analysere dataene. Du får derefter enten gemme resultater for senere perusal eller bruge et dashboard og [Power BI](https://powerbi.com/) til at give indsigt i realtid.

Sociale medier analytics værktøjer hjælpe organisationer med at forstå cirkulerer emner, det vil sige, emner og indstilling, der har en stor mængde indlæg i sociale medier. Synspunkt analyse, som også kaldes *udtalelse mining*, anvender sociale medier analytics værktøjer til at bestemme holdning mod et produkt, ide osv. Realtid Twitter tendensanalyse er et godt eksempel, fordi mærke abonnementsmodellen gør det muligt at lytte til bestemte nøgleord og udvikle synspunkt analyse på feedet.

## <a name="scenario-sentiment-analysis-in-real-time"></a>Scenarie: Synspunkt analyse i realtid

En virksomhed, der har et websted, nyheder medier er interesseret i at få en fordel i forhold til dens konkurrenter da den indeholder tre webstedets indhold, der er umiddelbart relevante til dens læsere. Firmaet bruger sociale medier analyse på emner, der er relevante for læserne ved at gøre realtid synspunkt analyse på Twitter data. For at identificere cirkulerer emner i realtid på Twitter skal skal virksomheden specifikt realtid analytics om tweet lydstyrken og synspunkt efter vigtige emner. Så egentlig er brug af en synspunkt analyse analytics-program, der er baseret på dette feed sociale medier.

## <a name="prerequisites"></a>Forudsætninger
-   Twitter-konto og [OAuth-adgangstoken](https://dev.twitter.com/oauth/overview/application-owner-access-tokens)
-   [TwitterClient.zip](http://download.microsoft.com/download/1/7/4/1744EE47-63D0-4B9D-9ECF-E379D15F4586/TwitterClient.zip) fra Microsoft Download Center
-   Valgfrit: Kildekode for twitter-klienten fra [GitHub](https://aka.ms/azure-stream-analytics-twitterclient)

## <a name="create-an-event-hub-input-and-a-consumer-group"></a>Oprette en begivenhed hub input og en forbruger gruppe

Eksempelprogrammet generere begivenheder og overføre dem til en begivenhed Hubs forekomst (en begivenhed-hub, til kort). Service Bus begivenhed hubber er den foretrukne metode til begivenhed indtagelse for Stream analyser. Se begivenhed Hubs dokumentation i [Service Bus dokumentation](/documentation/services/service-bus/).

Brug følgende trin til at oprette en begivenhed hub.

1.  Klik på **Ny**i portalen Azure > **APP SERVICES** > **SERVICE BUS** > **BEGIVENHED HUB** > **Hurtig oprettelse**, og angive et navn, region og ny eller eksisterende navneområde.  
2.  Som en bedste fremgangsmåde, skal hvert Stream Analytics-job læse fra en enkelt hændelse Hubs consumer gruppe. Vi fører dig gennem processen med at oprette en gruppe til forbrugere senere. Du kan få mere at vide om consumer grupper på [Azure begivenhed Hubs oversigt](https://msdn.microsoft.com/library/azure/dn836025.aspx). Gå til nyoprettede hændelse-hub, klik på fanen **CONSUMER grupper** , klik på **Opret** nederst på siden og derefter angive et navn til din consumer gruppe for at oprette en gruppe til forbrugere.
3.  Hvis du vil give adgang til begivenhed-hub, skal vi oprette en delt access-politik. Klik på fanen **konfiguration** af din begivenhed hub.
4.  Opret en ny politik under **Delt ACCESS politikker**, med **ADMINISTRER** tilladelser.

    ![Delt Access politikker, hvor du kan oprette en politik med Administrer tilladelser.](./media/stream-analytics-twitter-sentiment-analysis-trends/stream-ananlytics-shared-access-policies.png)

5.  Klik på **Gem** nederst på siden.
6.  Gå til **DASHBOARD**, klik på **FORBINDELSESOPLYSNINGERNE** nederst på siden, og derefter kopiere og gemme forbindelsesoplysningerne. (Brug ikonet kopi, der vises under søgeikonet).

## <a name="configure-and-start-the-twitter-client-application"></a>Konfigurere og starte Twitter-klientprogrammet

Vi har angivet en klientprogrammet, som opretter forbindelse til Twitter data via [Twitter's Streaming API'er](https://dev.twitter.com/streaming/overview) til at indsamle Tweet hændelser om en parameteriseret sæt emner. Værktøjet [Sentiment140](http://help.sentiment140.com/) Åbn kilde bruges til at tildele værdien synspunkt til hver tweet.

- 0 = negative
- 2 = neutrale
- 4 = positive

Derefter publiceres Tweet begivenheder i hubben begivenhed.  

Følg disse trin til konfiguration af programmet:

1.  [Hente TwitterClient løsningen](http://download.microsoft.com/download/1/7/4/1744EE47-63D0-4B9D-9ECF-E379D15F4586/TwitterClient.zip).
2.  Åbn TwitterClient.exe.config, og Erstat oauth_consumer_key, oauth_consumer_secret, oauth_token og oauth_token_secret med Twitter tokens, der indeholder dine værdier.  

    [Trin til at generere et OAuth-adgangstoken](https://dev.twitter.com/oauth/overview/application-owner-access-tokens)  

    Bemærk, at du skal foretage en tom program tilladelse til at oprette et token.  
3.  Erstat værdier for EventHubConnectionString og EventHubName i TwitterClient.exe.config med forbindelsesstrengen og navnet på begivenheden centrum. Forbindelsesstrengen, som du kopierede tidligere får du både forbindelsesstrengen og navnet på begivenheden-hub, så sørg for at adskille dem og placere hvert navn i feltet korrekt. For eksempel kan du overveje følgende forbindelsesstreng:

        Endpoint=sb://your.servicebus.windows.net/;SharedAccessKeyName=yourpolicy;SharedAccessKey=yoursharedaccesskey;EntityPath=yourhub

    Filen TwitterClient.exe.config skal indeholde dine indstillinger som i følgende eksempel:

        add key="EventHubConnectionString" value="Endpoint=sb://your.servicebus.windows.net/;SharedAccessKeyName=yourpolicy;SharedAccessKey=yoursharedaccesskey"
        add key="EventHubName" value="yourhub"

    Det er vigtigt at bemærke, at teksten "EntityPath =" fungerer __ikke__ vises i værdien EventHubName.

4.  *Valgfrit:* Justere nøgleord til at søge efter.  Som standard søger dette program efter "Azure, Skype, XBox, Microsoft, Seattle".  Du kan justere værdierne for **twitter_keywords** i TwitterClient.exe.config, hvis du ønsker.
5.  Køre TwitterClient.exe for at starte dit program. Du får vist Tweet begivenheder med de **CreatedAt**, **emne**og **SentimentScore** værdier, der sendes til din begivenhed hub.

    ![Synspunkt analyse: SentimentScore værdier, der er sendt til en begivenhed hub.](./media/stream-analytics-twitter-sentiment-analysis-trends/stream-analytics-twitter-sentiment-output-to-event-hub.png)

## <a name="create-a-stream-analytics-job"></a>Oprette et Stream Analytics-job

Nu, hvor Tweet hændelser streaming i realtid fra Twitter, kan vi konfigurere et Stream Analytics-job til at analysere disse hændelser i realtid.

### <a name="provision-a-stream-analytics-job"></a>Klargør et Stream Analytics-job

1.  [Azure-portalen](https://manage.windowsazure.com/), klik på **Ny** > **DATATJENESTER** > **STREAM ANALYTICS** > **Hurtig oprettelse**.
2.  Angiv følgende værdier, og klik derefter på **Opret STREAM ANALYTICS sag**:

    * **Jobbet navn**: Skriv et navn til jobbet.
    * **Område**: Vælg det område, hvor du vil køre jobbet. Overvej at placere jobbet og begivenhed hub i samme område til at sikre en bedre ydeevne og sikre, at du ikke skal betale til at overføre data mellem områder.
    * **LAGERPLADS konto**: vælge den konto, Azure-lager, du vil bruge til at lagre overvågningsdata for alle Stream Analytics-job, der kører i dette område. Har du mulighed for at vælge en eksisterende konto lagerplads eller til at oprette en ny.

3.  Klik på **STREAM ANALYTICS** i ruden til venstre med listen over værdistrøm Analytics-job.  
    ![Ikon for stream Analytics-tjenesten](./media/stream-analytics-twitter-sentiment-analysis-trends/stream-analytics-service-icon.png)

    Den nye sag vises med statussen **oprettet**. Bemærk, at knappen **START** i bunden af siden er deaktiveret. Du skal konfigurere tingene input, output og forespørgsel, før du kan starte jobbet.


### <a name="specify-job-input"></a>Angive job input
1.  Klik på **input** fra toppen af siden i Stream Analytics tingene, og klik derefter på **Tilføj INPUT**. Den dialogboks, der åbnes fører dig gennem en række trin til konfiguration af dit input.
2.  Klik på **DATASTREAM**, og klik derefter på knappen til højre.
3.  Klik på **BEGIVENHED-HUB**, og klik derefter på knappen til højre.
4.  Skriv eller Vælg følgende værdier på den tredje side:

    * **INDTAST ALIAS**: Angiv et fuldt navn til jobbet input, som *TwitterStream*. Bemærk, at du skal bruge dette navn i forespørgslen senere.
    **BEGIVENHED HUB**: Hvis hubben begivenhed, du har oprettet er i samme abonnement som Stream Analytics jobbet, Vælg det navneområde, begivenhed hub er i.

        Hvis din begivenhed hub er i et andet abonnement, skal du klikke på **Brug begivenhed Hub fra et andet abonnement**, og angiv derefter oplysningerne for **Tjenesten BUS NAVNEOMRÅDE**, **BEGIVENHED HUB navn**, **BEGIVENHED HUB POLITIKKENS navn**, **BEGIVENHED HUB POLITIKNØGLE**og **BEGIVENHED HUB PARTITION Tæl**manuelt.

    * **Navn på denne BEGIVENHED HUB**: Vælg navnet på begivenhed hub.

    * **BEGIVENHED HUB POLITIKKENS navn**: Vælg den begivenhed hub politik, du oprettede tidligere i dette selvstudium.

    * **BEGIVENHED HUB CONSUMER gruppe**: Skriv navnet på den forbruger-gruppe, du oprettede tidligere i dette selvstudium.
5.  Klik på knappen til højre.
6.  Angiv følgende værdier:

    * **BEGIVENHED SERIALISERINGSFUNKTION FORMAT**: JSON
    * **Kodning**: UTF8

7.  Klik på knappen **KONTROLLÉR** til at tilføje denne kilde og Kontrollér, at Stream Analytics kan oprette forbindelse til begivenhed hubben.

### <a name="specify-job-query"></a>Angive job forespørgsel

Stream Analytics understøtter en enkel, deklarative forespørgsel model, der beskriver transformationer. Hvis du vil vide mere om sproget, skal du se [Azure Stream Analytics Query Language Reference](https://msdn.microsoft.com/library/azure/dn834998.aspx).  Dette selvstudium hjælper dig med at redigere og teste flere forespørgsler på Twitter data.

#### <a name="sample-data-input"></a>Eksempel datainput

For at validere din forespørgsel mod faktiske jobdata, kan du bruge funktionen **EKSEMPELDATA** til at udtrække begivenheder fra din stream og oprette en .json fil af hændelser til test.

1.  Vælg dit begivenhed hub input, og klik derefter på **EKSEMPELDATA** i bunden af siden.
2.  I den dialogboks, der åbnes, angive et **STARTTIDSPUNKT** for at starte indsamling af data og en **varighed** til hvor meget yderligere data til at bruge.
3.  Klik på knappen **Detaljer** , og klik derefter på linket **Klik her** for at hente og gemme filen genererede .json.

#### <a name="pass-through-query"></a>Pass-through-forespørgsel
For at starte skal gør vi en simpel pass-through-forespørgsel, der projekter alle felterne i en begivenhed.

1.  Klik på **forespørgsel** øverst på siden Stream Analytics job.
2.  I kodeeditoren skal du erstatte den oprindelige forespørgselsskabelon med følgende:

        SELECT * FROM TwitterStream

    Sørg for, at navnet på inputkilden svarer til navnet på det input, du tidligere har angivet.

3.  Klik på **Test** under forespørgselseditor.
4.  Gå til .json eksempelfilen.
5.  Klik på knappen **MARKÉR** , og se resultaterne under definitionen af forespørgslen.

    ![Resultaterne vises under definitionen for forespørgsel](./media/stream-analytics-twitter-sentiment-analysis-trends/stream-analytics-sentiment-by-topic.png)

#### <a name="count-of-tweets-by-topic-tumbling-window-with-aggregation"></a>Antallet af tweets efter emne: Tumbling vindue med sammenlægning

Hvis du vil sammenligne antallet af omtaler mellem emner, bruger vi en [TumblingWindow](https://msdn.microsoft.com/library/azure/dn835055.aspx) til at få antallet af omtaler efter emne hvert 5.

1.  Ændre forespørgslen i kodeeditoren:

        SELECT System.Timestamp as Time, Topic, COUNT(*)
        FROM TwitterStream TIMESTAMP BY CreatedAt
        GROUP BY TUMBLINGWINDOW(s, 5), Topic

    Denne forespørgsel bruger nøgleordet **TIDSSTEMPEL ved** for at angive et tidsstempelfelt i de data, der skal bruges i tidsmæssig beregningen. Hvis dette felt ikke blev angivet, vil handlingen windowing udføres ved hjælp af den tid, som hver enkelt hændelse nået begivenhed hubben.  Yderligere oplysninger i afsnittet "Modtagelse af tid eller-programmet tid" i [Stream Analytics forespørgsel Reference](https://msdn.microsoft.com/library/azure/dn834998.aspx).

    Denne forespørgsel også har adgang til et tidsstempel til slutningen af hvert vindue ved hjælp af egenskaben **System.Timestamp** .

2.  Klik på **Kør** under query editor for at få vist resultaterne af forespørgslen.

#### <a name="identify-trending-topics-sliding-window"></a>Identificere cirkulerer emner: skyderen vindue

Hvis du vil identificere cirkulerer emner, vil vi se til emner, der krydser en grænseværdi for omtaler i et bestemt tidsrum. Med henblik på dette selvstudium skal vi se emner, der er nævnt mere end 20 gange i de sidste fem sekunder ved hjælp af en [SlidingWindow](https://msdn.microsoft.com/library/azure/dn835051.aspx).

1.  Ændre forespørgslen i kodeeditoren: Vælg System.Timestamp som tid, emne, Tæl (*) som omtaler fra TwitterStream TIDSSTEMPEL ved CreatedAt gruppe ved SLIDINGWINDOW(s, 5), emne, der har TÆLLE (*) > 20

2.  Klik på **Kør** under query editor for at få vist resultaterne af forespørgslen.

    ![Hvis du flytter vinduet forespørgslens output](./media/stream-analytics-twitter-sentiment-analysis-trends/stream-analytics-query-output.png)

#### <a name="count-of-mentions-and-sentiment-tumbling-window-with-aggregation"></a>Antallet af omtaler og synspunkt: Tumbling vindue med sammenlægning
Den endelige forespørgsel, som vi vil teste bruger **TumblingWindow** til at få antallet af omtaler, gennemsnit, minimum, maksimum og standardafvigelsen for synspunkt karakteren for hvert emne hvert 5.

1.  Ændre forespørgslen i kodeeditoren:

        SELECT System.Timestamp as Time, Topic, COUNT(*), AVG(SentimentScore), MIN(SentimentScore),
        Max(SentimentScore), STDEV(SentimentScore)
        FROM TwitterStream TIMESTAMP BY CreatedAt
        GROUP BY TUMBLINGWINDOW(s, 5), Topic

2.  Klik på **Kør** under query editor for at få vist resultaterne af forespørgslen.
3.  Dette er den forespørgsel, som vi vil bruge til vores dashboard.  Klik på **Gem** nederst på siden.


## <a name="create-output-sink"></a>Oprette output sink

Nu hvor vi har defineret en begivenhed stream, en begivenhed hub input til indtager begivenheder og en forespørgsel for at udføre en transformering over strømmen, er det sidste trin at definere en output sink til opgaven.  Vi vil skrive aggregeret tweet begivenheder fra vores job forespørgsel til Azure Blob-lager.  Du kan også overføre dine resultater til Azure SQL-Database, Azure Table storage eller begivenhed Hubs, afhængigt af det pågældende program skal.

Brug følgende trin til at oprette en objektbeholder til Blob-lager, hvis du ikke allerede har en:

1.  Brug en eksisterende lagerplads-konto eller oprette en ny konto lager ved at klikke på **Ny** > **DATATJENESTER** > **LAGERPLADS** > **Hurtig oprettelse**, og følg derefter vejledningen på skærmen.
2.  Vælg kontoen lagerplads, skal du klikke på **beholdere** øverst på siden, og klik derefter på **Tilføj**.
3.  Angiv et **navn** for din objektbeholder, og angiv **adgang** til **Offentlige Blob**.

## <a name="specify-job-output"></a>Angive job output

1.  Klik på **OUTPUT** øverst på siden i Stream Analytics tingene, og klik derefter på **Tilføj OUTPUT**. Den dialogboks, der åbnes fører dig gennem flere trin til konfiguration af din output.
2.  Klik på **BLOB-lager**, og klik derefter på knappen til højre.
3.  Skriv eller Vælg følgende værdier på den tredje side:

    * **OUTPUT ALIAS**: Angiv et fuldt navn til denne job output.

    * **Abonnement**: Hvis Blob-lager, du har oprettet er i samme abonnement som Stream Analytics jobbet, klikke på **Brug lagerplads konto nuværende abonnement**. Hvis din lagerplads er i et andet abonnement, skal du klikke på **Brug lagerplads konto fra et andet abonnement**, og indtaste oplysninger til **LAGERPLADS konto**, **LAGERPLADS KONTONØGLE**og **OBJEKTBEHOLDER**manuelt.

    * **LAGERPLADS konto**: Vælg navnet på kontoen, lagerplads.

    * **OBJEKTBEHOLDER**: Vælg navnet på objektbeholderen.

    * **PRÆFIKS for FILNAVN**: Skriv en fil præfiks, du vil bruge, når du skriver blob output.

4.  Klik på knappen til højre.
5.  Angiv følgende værdier:
    * **BEGIVENHED SERIALISERINGSFUNKTION FORMAT**: JSON
    * **Kodning**: UTF8
6.  Klik på knappen **KONTROLLÉR** , at tilføje denne kilde og for at bekræfte, at Stream Analytics kan oprette forbindelse til kontoen lagerplads.

## <a name="start-job"></a>Starte job

Fordi et job input, forespørgsel og output har alle er angivet, er vi klar til at starte Stream Analytics-job.

1.  Klik på **START** nederst på siden fra jobbet **DASHBOARD**.
2.  I den dialogboks, der åbnes, skal du klikke på **jobbet STARTTIDSPUNKT**og derefter klikke på knappen **KONTROLLÉR** nederst i dialogboksen. Jobstatus ændres til **Start** og ændrer kort for at **køre**.


## <a name="view-output-for-sentiment-analysis"></a>Vis output til synspunkt analyse

Når tingene kører og behandling realtid Twitter strømmen, kan du vælge, hvordan du vil have vist output til synspunkt analyse. Brug et værktøj som [Azure Storage Explorer](https://azurestorageexplorer.codeplex.com/) eller [Azure Explorer](http://www.cerebrata.com/products/azure-explorer/introduction) til at få vist din sag output i realtid. Her kan du bruge [Power BI](https://powerbi.com/) til at udvide dit program tilladelse til at medtage et brugerdefineret dashboard som i følgende skærmbillede.

![Sociale medier analyse: Stream Analytics synspunkt analyse (udtalelse mining) output i et Power BI-dashboard.](./media/stream-analytics-twitter-sentiment-analysis-trends/stream-analytics-output-power-bi.png)

## <a name="get-support"></a>Få support
For at få hjælp, kan du prøve vores [Azure Stream Analytics-forummet](https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureStreamAnalytics).


## <a name="next-steps"></a>Næste trin

- [Introduktion til Azure Stream Analytics](stream-analytics-introduction.md)
- [Introduktion til brug af Azure Stream Analytics](stream-analytics-get-started.md)
- [Skalere Azure Stream Analytics-job](stream-analytics-scale-jobs.md)
- [Azure Stream Analytics Query Language Reference](https://msdn.microsoft.com/library/azure/dn834998.aspx)
- [Azure Stream Analytics Management RESTEN API Reference](https://msdn.microsoft.com/library/azure/dn835031.aspx)
