<properties
    pageTitle="Stream Analytics: Realtid bedrageri registrering | Microsoft Azure"
    description="Lær, hvordan du opretter en realtid svindel registrering løsning med Stream analyser. Bruge en begivenhed hub til behandling af realtid hændelse."
    keywords="anomali registrering, bedrageri registrering, realtid anomali registrering"
    services="stream-analytics"
    documentationCenter=""
    authors="jeffstokes72"
    manager="jhubbard"
    editor="cgronlun" />

<tags
    ms.service="stream-analytics"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="data-services"
    ms.date="09/26/2016"
    ms.author="jeffstok" />



# <a name="get-started-using-azure-stream-analytics-real-time-fraud-detection"></a>Introduktion til brug af Azure Stream Analytics: realtid svindel registrering

Lær, hvordan du opretter en til en komplet løsning til registrering af realtid svindel med Azure Stream Analytics. Sætter begivenheder i Azure begivenhed Hubs, skrive Stream Analytics forespørgsler til sammenlægning eller advarer og sende resultaterne til en output sink til at få indsigt over data med realtid behandling. Realtid anomali registrering for telekommunikation forklares, men eksempel metode lige er velegnet til andre typer svindel registrering som Scenarier med kreditkort eller identitet identitetstyveri.

Stream Analytics er en komplet administreret tjeneste, som indeholder lav ventetid meget tilgængelige SVG, og kompleks begivenhed behandling over streaming data i skyen. Se [Introduktion til Azure Stream Analytics](stream-analytics-introduction.md)kan finde flere oplysninger.


## <a name="scenario-telecommunications-and-sim-fraud-detection-in-real-time"></a>Scenarie: Registrering telekommunikation og SIM svindel i realtid

Et telekommunikation firma har en stor mængde data for indgående opkald. Virksomheden skal bruge følgende fra dens data:

* Reducere data til et håndterbare beløb og få indsigt i kunde brugen over tid og på tværs af geografiske områder.
* Registrere SIM svindel (flere opkald fra den samme identitet rundt på samme tid, men i geografisk forskellige steder) i realtid, så virksomheden kan nemt besvare den ved besked om kunder eller lukke tjenesten.

Vedtaget Internet af ting (IoT) scenarier har massevis af telemetri eller data fra sensorer. Kunder vil sammenlægge data eller modtage beskeder om afvigelser i realtid.

## <a name="prerequisites"></a>Forudsætninger

- Hente [TelcoGenerator.zip](http://download.microsoft.com/download/8/B/D/8BD50991-8D54-4F59-AB83-3354B69C8A7E/TelcoGenerator.zip) fra Microsoft Download Center
- Valgfrit: Kildekode begivenhed generator fra [GitHub](https://aka.ms/azure-stream-analytics-telcogenerator)

## <a name="create-azure-event-hubs-input-and-consumer-group"></a>Oprette Azure begivenhed Hubs input og forbrugere gruppe

Eksempelprogrammet generere begivenheder og overføre dem til en begivenhed Hubs forekomst til realtid behandling. Tjenesten Bus begivenhed hubber er den foretrukne metode til begivenhed indtagelse for Stream analyser. Du kan få mere at vide om begivenhed Hubs i [dokumentationen til Azure Service Bus](/documentation/services/service-bus/).

Sådan oprettes en begivenhed hub:

1.  [Azure-portalen](https://manage.windowsazure.com/), klik på **Ny** > **APP SERVICES** > **SERVICE BUS** > **BEGIVENHED HUB** > **Hurtig oprettelse**. Angiv et navn, region og ny eller eksisterende navneområde til at oprette en ny begivenhed-hub.  
2.  Som en bedste fremgangsmåde, skal hvert Stream Analytics-job læse fra en enkelt hændelse hub consumer gruppe. Vi fører dig gennem processen med at oprette en gruppe til forbrugere senere. [Lær mere om Consumer grupper](https://msdn.microsoft.com/library/azure/dn836025.aspx). Gå til nyoprettede hændelse-hub, klik på fanen **CONSUMER grupper** , klik på **Opret** nederst på siden og derefter angive et navn til din consumer gruppe for at oprette en gruppe til forbrugere.
3.  Hvis du vil give adgang til begivenhed-hub, skal vi oprette en delt access-politik. Klik på fanen **konfiguration** af din begivenhed hub.
4.  Opret en ny politik, der indeholder **ADMINISTRER** tilladelser under **Delt ACCESS politikker**.

    ![Delt Access politikker, hvor du kan oprette en politik med Administrer tilladelser.](./media/stream-analytics-real-time-fraud-detection/stream-ananlytics-shared-access-policies.png)

5.  Klik på **Gem** nederst på siden.
6.  Gå til **Dashboard**, klik på **FORBINDELSESOPLYSNINGERNE** nederst på siden, og derefter kopiere og gemme forbindelsesoplysningerne.

## <a name="configure-and-start-the-event-generator-application"></a>Konfigurere og starte programmet begivenhed generator

Vi har angivet en klientprogrammet, der kan generere eksempel indgående opkald metadata og push til begivenhed hubber. Brug følgende trin til konfiguration af dette program.  

1.  Hente [TelcoGenerator.zip fil](http://download.microsoft.com/download/8/B/D/8BD50991-8D54-4F59-AB83-3354B69C8A7E/TelcoGenerator.zip), og udpakke til en mappe.

    > [AZURE.NOTE] Windows kan blokere hentede zip-filen. Højreklik på filen, og vælg **Egenskaber**. Hvis du får vist "denne fil stammer fra en anden computer og kan blive blokeret for at beskytte denne computer" meddelelsen, markere afkrydsningsfeltet **Fjern blokering** , og klik derefter på Anvend på zip-filen.

2.  Erstat værdier for Microsoft.ServiceBus.ConnectionString og EventHubName i telcodatagen.exe.config med din begivenhed hub forbindelsesstreng og dit navn.

    Forbindelsesstrengen, som du kopierede fra portalen Azure placerer navnet på forbindelsen nederst. Sørg for at fjerne "; EntityPath =<value>"fra den" føje nøgle = "felt.

3.  Starte programmet. Brug er som følger:

    telcodatagen.exe [#NumCDRsPerHour] [SIM kort svindel sandsynlighed] [#DurationHours]

I følgende eksempel oprettes 1.000 begivenheder med en 20% sandsynlighed for bedrageri i løbet af to timer.

    telcodatagen.exe 1000 .2 2

Du får vist poster, der sendes til din begivenhed hub. Nogle vigtige felter, som vi vil bruge i realtid svindel registrering programmet defineres her:

| Post | Definition |
| ------------- | ------------- |
| CallrecTime | Tidsstemplet for opkaldet starttidspunkt. |
| SwitchNum | Telefon parameter bruges til at forbinde opkaldet. |
| CallingNum | Telefonnummer på kalderen. |
| CallingIMSI | Internationalt bærbar abonnement identitet (IMSI).  Entydigt id for kalderen. |
| CalledNum | Telefonnummer på opkaldsmodtageren. |
| CalledIMSI | Internationalt bærbar abonnement identitet (IMSI).  Entydigt id for opkaldsmodtageren. |


## <a name="create-a-stream-analytics-job"></a>Oprette et Stream Analytics-job
Nu hvor vi har en strøm af telekommunikation begivenheder, kan vi konfigurere et Stream Analytics-job til at analysere disse hændelser i realtid.

### <a name="provision-a-stream-analytics-job"></a>Klargør et Stream Analytics-job

1.  Klik på **Ny**i portalen Azure > **DATATJENESTER** > **STREAM ANALYTICS** > **Hurtig oprettelse**.
2.  Angiv følgende værdier, og klik derefter på **Opret STREAM ANALYTICS sag**:

    * **Jobbet navn**: Skriv et navn til jobbet.

    * **Område**: Vælg det område, hvor du vil køre jobbet. Overvej at placere jobbet og begivenhed hub i samme område til at sikre en bedre ydeevne og sikre, at du ikke skal betale til at overføre data mellem områder.

    * **LAGERPLADS konto**: vælge den konto, Azure-lager, du vil bruge til at lagre overvågningsdata for alle Stream Analytics-job, der kører i dette område. Du har mulighed for at vælge en eksisterende lagerplads-konto eller oprette en ny.

3.  Klik på **STREAM ANALYTICS** i ruden til venstre med listen over værdistrøm Analytics-job.

    ![Ikon for stream Analytics-tjenesten](./media/stream-analytics-real-time-fraud-detection/stream-analytics-service-icon.png)

    Den nye sag vises med statussen **oprettet**. Bemærk, at knappen **START** i bunden af siden er deaktiveret. Du skal konfigurere tingene input, output og forespørgsel, før du kan starte jobbet.

### <a name="specify-job-input"></a>Angive job input
1.  Klik på **input** øverst på siden i Stream Analytics tingene, og klik derefter på **Tilføj INPUT**. Den dialogboks, der åbnes fører dig gennem flere trin til konfiguration af dit input.
2.  Klik på **DATASTREAM**, og klik derefter på knappen til højre.
3.  Klik på **BEGIVENHED-HUB**, og klik derefter på knappen til højre.
4.  Skriv eller Vælg følgende værdier på den tredje side:

    * **INPUT ALIAS**: Angiv et fuldt navn som *CallStream*for opgaven. Bemærk, at du vil bruge dette navn i forespørgslen senere.

    * **BEGIVENHED HUB**: Hvis hubben begivenhed, du har oprettet er i samme abonnement som Stream Analytics jobbet, Vælg det navneområde, begivenhed hub er i.

        Hvis din begivenhed hub er i et andet abonnement, Vælg **Brug begivenhed Hub fra et andet abonnement**, og angiv derefter oplysningerne for **Tjenesten BUS NAVNEOMRÅDE**, **BEGIVENHED HUB navn**, **BEGIVENHED HUB POLITIKKENS navn**, **BEGIVENHED HUB POLITIKNØGLE**og **BEGIVENHED HUB PARTITION Tæl**manuelt.

    * **Navn på denne BEGIVENHED HUB**: Vælg navnet på begivenhed hub.

    * **BEGIVENHED HUB POLITIKKENS navn**: Vælg den begivenhed hub politik, du oprettede tidligere i dette selvstudium.

    * **BEGIVENHED HUB CONSUMER gruppe**: Skriv navnet på den forbruger-gruppe, du oprettede tidligere i dette selvstudium.

5.  Klik på knappen til højre.
6.  Angiv følgende værdier:

    * **BEGIVENHED SERIALISERINGSFUNKTION FORMAT**: JSON
    * **Kodning**: UTF8
7.  Klik på knappen **KONTROLLÉR** til at tilføje denne kilde og Kontrollér, at Stream Analytics kan oprette forbindelse til begivenhed hubben.

### <a name="specify-job-query"></a>Angive job forespørgsel

Stream Analytics understøtter en enkel, deklarative forespørgsel model, der beskriver transformationer til realtid behandling. Hvis du vil vide mere om sproget, skal du se [Azure Stream Analytics Query Language Reference](https://msdn.microsoft.com/library/dn834998.aspx). Dette selvstudium hjælper dig med at redigere og teste flere forespørgsler over din realtid strøm af data fra kaldet.

#### <a name="optional-sample-input-data"></a>Valgfrit: Prøveinputdata
Hvis du vil validere din forespørgsel mod faktiske jobdata, kan du bruge funktionen **EKSEMPELDATA** til at udtrække begivenheder fra din stream og oprette en. JSON fil af hændelser til test.  Følgende trin viser, hvordan du gør dette. Vi har også lavet en eksempelfil [telco.json](https://github.com/Azure/azure-stream-analytics/blob/master/Sample%20Data/telco.json) til testformål.

1.  Vælg dit begivenhed hub input, og klik derefter på **EKSEMPELDATA** i bunden af siden.
2.  I den dialogboks, der åbnes, angive et **STARTTIDSPUNKT** for at starte indsamling af data og en **varighed** til hvor meget yderligere data til at bruge.
3.  Klik på knappen **KONTROLLÉR** for at starte stikprøver, hvor data fra input.  Det kan tage et minut eller to til datafilen skal være produceret.  Når processen er færdig, skal du klikke på **Detaljer**, hente den oprettede. JSON filen, og Gem den.

    ![Hente og gemme behandlede data i en JSON-fil](./media/stream-analytics-real-time-fraud-detection/stream-analytics-download-save-json-file.png)

#### <a name="pass-through-query"></a>Pass-through-forespørgsel

Hvis du ønsker at arkivere hver begivenhed, kan du bruge en pass-through-forespørgsel til at læse alle felterne i data i den begivenhed eller en meddelelse. For at starte skal du gøre en simpel pass-through-forespørgsel, projekter alle felterne i en begivenhed.

1.  Klik på **forespørgsel** fra toppen af siden Stream Analytics job.
2.  Tilføj følgende Kodeeditor:

        SELECT * FROM CallStream

    > [AZURE.IMPORTANT] Sørg for, at navnet på inputkilden svarer til navnet på det input, du tidligere har angivet.

3.  Klik på **Test** under forespørgselseditor.
4.  Angiv en testfil. Bruge en, du har oprettet ved hjælp af de forrige trin, eller brug [telco.json](https://github.com/Azure/azure-stream-analytics/blob/master/Samples/SampleDataFiles/Telco.json).
5.  Klik på knappen **MARKÉR** , og se resultaterne vises under definitionen af forespørgslen.

    ![Definition af forespørgselsresultater](./media/stream-analytics-real-time-fraud-detection/stream-analytics-sim-fraud-output.png)


### <a name="column-projection"></a>Kolonne projicering

Vi vil nu reducere de returnerede felter til en lille gruppe.

1.  Ændre forespørgslen i kodeeditoren:

        SELECT CallRecTime, SwitchNum, CallingIMSI, CallingNum, CalledNum
        FROM CallStream

2.  Klik på **Kør** under query editor for at få vist resultaterne af forespørgslen.

    ![Output i forespørgselseditor.](./media/stream-analytics-real-time-fraud-detection/stream-analytics-query-editor-output.png)

### <a name="count-of-incoming-calls-by-region-tumbling-window-with-aggregation"></a>Antallet af indgående opkald efter område: Tumbling vindue med sammenlægning

For at sammenligne antallet af indgående opkald per område skal bruger vi en [TumblingWindow](https://msdn.microsoft.com/library/azure/dn835055.aspx) til at få antallet af indgående opkald, der er grupperet efter **SwitchNum** hvert 5.

1.  Ændre forespørgslen i kodeeditoren:

        SELECT System.Timestamp as WindowEnd, SwitchNum, COUNT(*) as CallCount
        FROM CallStream TIMESTAMP BY CallRecTime
        GROUP BY TUMBLINGWINDOW(s, 5), SwitchNum

    Denne forespørgsel bruger nøgleordet **Tidsstempel ved** for at angive et tidsstempelfelt i de data, der skal bruges i tidsmæssig beregningen. Hvis dette felt ikke blev angivet, vil handlingen windowing udføres ved hjælp af den tid, som hver enkelt hændelse nået begivenhed hubben. Se ["Modtagelse af tid eller-programmet tid" i Stream analyser forespørgsel Language Reference](https://msdn.microsoft.com/library/azure/dn834998.aspx).

    Bemærk, at du kan få adgang til et tidsstempel til slutningen af hvert vindue ved hjælp af egenskaben **System.Timestamp** .

2.  Klik på **Kør** under query editor for at få vist resultaterne af forespørgslen.

    ![Resultater af en forespørgsel til Timestand ved](./media/stream-analytics-real-time-fraud-detection/stream-ananlytics-query-editor-rerun.png)

### <a name="sim-fraud-detection-with-a-self-join"></a>SIM svindel registrering med en selv-joinforbindelse

Hvis du vil identificere potentielt falske brugen, vil vi se til opkald, der stammer fra den samme bruger, men på forskellige placeringer i mindre end 5 sekunder.  Vi [joinforbindelse](https://msdn.microsoft.com/library/azure/dn835026.aspx) strømmen af opkaldshændelser med sig selv at kontrollere, om disse tilfælde.

1.  Ændre forespørgslen i kodeeditoren:

        SELECT System.Timestamp as Time, CS1.CallingIMSI, CS1.CallingNum as CallingNum1,
        CS2.CallingNum as CallingNum2, CS1.SwitchNum as Switch1, CS2.SwitchNum as Switch2
        FROM CallStream CS1 TIMESTAMP BY CallRecTime
        JOIN CallStream CS2 TIMESTAMP BY CallRecTime
        ON CS1.CallingIMSI = CS2.CallingIMSI
        AND DATEDIFF(ss, CS1, CS2) BETWEEN 1 AND 5
        WHERE CS1.SwitchNum != CS2.SwitchNum

2.  Klik på **Kør** under query editor for at få vist resultaterne af forespørgslen.

    ![Resultater af en forespørgsel i en joinforbindelse](./media/stream-analytics-real-time-fraud-detection/stream-ananlytics-query-editor-join.png)

### <a name="create-output-sink"></a>Oprette output sink

Nu hvor vi har defineret en begivenhed stream, en begivenhed hub input til indtager begivenheder og en forespørgsel for at udføre en transformering over strømmen, er det sidste trin at definere en output sink til opgaven. Vi vil skrive begivenheder til falsk funktionsmåde til Azure Blob-lager.

Brug følgende trin til at oprette en objektbeholder til Blob-lager, hvis du ikke allerede har en.

1.  Brug en eksisterende lagerplads-konto eller oprette en ny firmapost lager ved at klikke på **Ny > DATATJENESTER > LAGERPLADS > Hurtig oprettelse**, og følg vejledningen.
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

## <a name="start-job-for-real-time-processing"></a>Starte job for realtid behandling

Fordi et job input, forespørgsel og output har alle er angivet, er vi klar til at starte Stream Analytics jobbet til realtid svindel registrering.

1.  Klik på **START** nederst på siden fra jobbet **DASHBOARD**.
2.  I den dialogboks, der åbnes, skal du klikke på **jobbet STARTTIDSPUNKT**og derefter klikke på knappen **KONTROLLÉR** nederst i dialogboksen. Jobstatus ændres til **Start** og ændrer kort for at **køre**.

## <a name="view-fraud-detection-output"></a>Vis svindel registrering output

Brug et værktøj som [Azure Storage Explorer](http://storageexplorer.com/) eller [Azure Explorer](http://www.cerebrata.com/products/azure-explorer/introduction) til at få vist falsk begivenheder, mens der skrives til din output i realtid.  

![Registrering af svindel: falsk hændelser vises i realtid](./media/stream-analytics-real-time-fraud-detection/stream-ananlytics-view-real-time-fraudent-events.png)

## <a name="get-support"></a>Få support
For at få hjælp, kan du prøve vores [Azure Stream Analytics-forummet](https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureStreamAnalytics).


## <a name="next-steps"></a>Næste trin

- [Introduktion til Azure Stream Analytics](stream-analytics-introduction.md)
- [Skalere Azure Stream Analytics-job](stream-analytics-scale-jobs.md)
- [Azure Stream Analytics Query Language Reference](https://msdn.microsoft.com/library/azure/dn834998.aspx)
- [Azure Stream Analytics Management RESTEN API Reference](https://msdn.microsoft.com/library/azure/dn835031.aspx)
