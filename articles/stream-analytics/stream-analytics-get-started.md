<properties
    pageTitle="Komme i gang med Stream Analytics: realtid svindel registrering | Microsoft Azure"
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

Lær, hvordan du opretter en til en komplet løsning til registrering af realtid svindel med Azure Stream Analytics. Sætter begivenheder i en Azure begivenhed-hub, skrive Stream Analytics forespørgsler til sammenlægning eller advarer og sende resultaterne til en output sink til at få indsigt over data med realtid behandling. Realtid anomali registrering for telekommunikation dækkes, men eksempel metode lige er velegnet til andre typer svindel registrering som Scenarier med kreditkort eller identitet identitetstyveri.

Stream Analytics er en komplet administreret tjeneste give lav ventetid, høj tilgængelighed, SVG komplekse begivenhed behandling over streaming data i skyen. Se [Introduktion til Azure Stream Analytics](stream-analytics-introduction.md)kan finde flere oplysninger.


## <a name="scenario-telecommunications-and-sim-fraud-detection-in-real-time"></a>Scenarie: Registrering telekommunikation og SIM svindel i realtid

Et telekommunikation firma har en stor mængde data for indgående opkald. Virksomheden skal bruge følgende fra dens data:
* Lign disse data ned til et håndterbare beløb og få indsigt i kunde brugen med tiden og geografiske områder.
* Registrere SIM svindel (flere opkald kommer fra den samme identitet rundt på samme tid, men i geografisk forskellige steder) i realtid så de nemt kan reagere ved besked om kunder eller lukke tjenesten.

I vedtaget Internet af ting (IoT) scenarier der massevis af telemetri eller føler data der genereres – og kunder vil sammenlægge dem eller beskeden over afvigelser i realtid.

## <a name="prerequisites"></a>Forudsætninger

- Hente [TelcoGenerator.zip](http://download.microsoft.com/download/8/B/D/8BD50991-8D54-4F59-AB83-3354B69C8A7E/TelcoGenerator.zip) fra Microsoft Download Center 
- Valgfrit: Kildekode begivenhed generator fra [GitHub](https://github.com/Azure/azure-stream-analytics/tree/master/DataGenerators/TelcoGenerator)

## <a name="create-an-azure-event-hubs-input-and-consumer-group"></a>Oprette en Azure begivenhed Hubs input og forbrugere gruppe

Eksempelprogrammet generere begivenheder og overføre dem til en begivenhed Hub forekomst til realtid behandling. Tjenesten Bus begivenhed Hubs er den foretrukne metode til begivenhed indtagelse for Stream analyser, og du kan lære mere om begivenhed Hubs i [dokumentationen til Azure Service Bus](/documentation/services/service-bus/).

Sådan oprettes en begivenhed Hub:

1.  Klik på **Ny**i [Azure portal](https://manage.windowsazure.com/)  > **App Services** > **Service Bus** > **Begivenhed Hub** > **Hurtig oprettelse**. Angiv et navn, region og ny eller eksisterende navneområde til at oprette en ny begivenhed-Hub.  
2.  Som en bedste fremgangsmåde, skal hvert Stream Analytics-job læse fra en enkelt hændelse Hub Consumer gruppe. Vi fører dig gennem processen med at oprette en forbruger gruppe nedenfor, og du kan [Få mere at vide mere om Consumer grupper](https://msdn.microsoft.com/library/azure/dn836025.aspx). For at oprette en gruppe til forbrugere, gå til nyoprettede begivenhed hubben og klik på fanen **Consumer grupper** , og derefter klikke på **Opret** nederst på siden og Angiv et navn til den Consumer gruppe.
3.  Hvis du vil give adgang til begivenhed-Hub, skal vi oprette en delt access-politik.  Klik på fanen **konfiguration** af din begivenhed Hub.
4.  Opret en ny politik under **Delt Access politikker**, med **Administrer** tilladelser.

    ![Delt Access politikker, hvor du kan oprette en politik med Administrer tilladelser.](./media/stream-analytics-get-started/stream-ananlytics-shared-access-policies.png)

5.  Klik på **Gem** nederst på siden.
6.  Gå til **Dashboard** , og klik på **Forbindelsesoplysningerne** nederst på siden, og derefter kopiere og gemme forbindelsesoplysningerne.

## <a name="configure-and-start-event-generator-application"></a>Konfigurere og starte begivenhed generator program

Vi har angivet en klientprogrammet, der kan generere eksempel indgående opkald metadata og push til begivenhed Hub. Følg trinnene nedenfor for at konfigurere dette program.  

1.  Hent [TelcoGenerator.zip fil](http://download.microsoft.com/download/8/B/D/8BD50991-8D54-4F59-AB83-3354B69C8A7E/TelcoGenerator.zip). Derefter udpakke den til en mappe.

    **Bemærk**: Windows kan blokere hentede zip-filen. Højreklik på filen, og vælg Egenskaber. Hvis meddelelsen "denne fil stammer fra en anden computer og bliver muligvis blokeret for at beskytte denne computer." derefter LAN feltet "Fjern blokering", og klik på Anvend på zip-filen.

2.  Erstat værdier for Microsoft.ServiceBus.ConnectionString og EventHubName i **telcodatagen.exe.config** med din begivenhed Hub forbindelsesstreng og dit navn.

    **Bemærk**: forbindelsesstrengen har kopieret fra Azure portalen steder på navnet på forbindelsen i slutningen. Sørg for at fjerne den "; EntityPath =<value>"= fra nøglen Tilføj felt.

3.  Starte programmet. Brug er som følger:

   telcodatagen.exe [#NumCDRsPerHour] [SIM kort svindel sandsynlighed] [#DurationHours]

I følgende eksempel oprettes 1000 begivenheder med en 20% sandsynlighed for svindel i løbet af to timer.

    telcodatagen.exe 1000 .2 2

Du får vist poster, der sendes til din begivenhed Hub. Nogle vigtige felter, som vi vil bruge i realtid svindel registrering programmet defineres her:

| Post | Definition |
| ------------- | ------------- |
| CallrecTime | Tidsstemplet for opkaldet starttidspunkt. |
| SwitchNum | Telefon parameter bruges til at forbinde opkaldet. |
| CallingNum | Telefonnummer på kalderen. |
| CallingIMSI | Internationalt bærbar abonnement identitet (IMSI).  Entydigt id for kalderen. |
| CalledNum | Telefonnummer på opkaldsmodtageren. |
| CalledIMSI | Internationalt bærbar abonnement identitet (IMSI).  Entydigt id for opkaldsmodtageren. |


## <a name="create-stream-analytics-job"></a>Oprette Stream Analytics-job
Nu hvor vi har en strøm af telekommunikation begivenheder, kan vi konfigurere et Stream Analytics-job til at analysere disse hændelser i realtid.

### <a name="provision-a-stream-analytics-job"></a>Klargør et Stream Analytics-job

1.  I portalen Azure, skal du klikke på **Ny > datatjenester > Stream Analytics > Hurtig oprettelse**.
2.  Angiv følgende værdier, og klik derefter på **Opret Stream Analytics sag**:

    * **Jobbet navn**: Skriv et navn til jobbet.

    * **Område**: Vælg det område, hvor du vil køre jobbet. Overvej at placere jobbet og begivenhed hub i samme område til at sikre en bedre ydeevne og sikre, at du ikke skal betale til at overføre data mellem områder.

    * **Lagerplads konto**: vælge den konto, Azure-lager, du vil bruge til at lagre overvågningsdata for alle Stream Analytics-job i dette område, der kører. Har du mulighed for at vælge en eksisterende konto lagerplads eller til at oprette en ny.

3.  Klik på **Stream Analytics** i ruden til venstre med listen over værdistrøm Analytics-job.

    ![Ikon for stream Analytics-tjenesten](./media/stream-analytics-get-started/stream-analytics-service-icon.png)

4.  Den nye sag vises med statussen **oprettet**. Bemærk, at knappen **Start** i bunden af siden er deaktiveret. Du skal konfigurere tingene input, output og forespørgsel, før du kan starte jobbet.

### <a name="specify-job-input"></a>Angive job input
1.  Klik på **input** fra toppen af siden i Stream Analytics tingene, og klik derefter på **Tilføj Input**. Den dialogboks, der åbnes fører dig gennem en række trin til konfiguration af dit input.
2.  Vælg **datastream**, og klik derefter på knappen til højre.
3.  Vælg **Begivenhed-Hub**, og klik derefter på knappen til højre.
4.  Skriv eller Vælg følgende værdier på den tredje side:

    * **Indtast Alias**: Angiv et fuldt navn for denne sag, der er indtastet som *CallStream*. Bemærk, at du vil bruge dette navn i forespørgslen senere.
    * **Begivenhed Hub**: Hvis begivenheden hubben du har oprettet er i samme abonnement som Stream Analytics jobbet, Vælg det navneområde, begivenhed hub er i.

    Hvis begivenheden-hub er i et andet abonnement, Vælg **Brug begivenhed Hub fra et andet abonnement** , og angiv oplysninger om **Tjenesten Bus Namespace**, **Begivenhed Hub navn**, **Begivenhed Hub politikkens navn**, **Begivenhed Hub politiknøgle**og **Begivenhed Hub Partition Tæl**manuelt.

    * **Navn på denne begivenhed Hub**: Vælg navnet på den begivenhed Hub.

    * **Begivenhed Hub politikkens navn**: Vælg den begivenhed-hub politik har oprettet tidligere i dette selvstudium.

    * **Begivenhed Hub Consumer gruppe**: Skriv gruppen forbrugere, der har oprettet tidligere i dette selvstudium.
5.  Klik på knappen til højre.
6.  Angiv følgende værdier:

    * **Begivenhed serialiseringsfunktion Format**: JSON
    * **Kodning**: UTF8
7.  Klik på knappen Kontrollér, at føje denne kilde og Kontrollér, at Stream Analytics kan oprette forbindelse til begivenhed-hub.

### <a name="specify-job-query"></a>Angive job forespørgsel

Stream Analytics understøtter en enkel, deklarative forespørgsel model til at beskrive transformationer til realtid behandling. Hvis du vil vide mere om sproget, skal du se [Azure Stream Analytics Query Language Reference](https://msdn.microsoft.com/library/dn834998.aspx). Dette selvstudium hjælper dig med at redigere og teste flere forespørgsler over din realtid strøm af data fra kaldet.

#### <a name="optional-sample-input-data"></a>Valgfrit: Prøveinputdata
Hvis du vil validere din forespørgsel mod faktiske jobdata, kan du bruge funktionen **Eksempeldata** til at udtrække begivenheder fra din stream og oprette en. JSON fil af hændelser til test.  Følgende trin viser, hvordan du gør dette, og vi har også lavet en eksempelfil [telco.json](https://github.com/Azure/azure-stream-analytics/blob/master/Sample%20Data/telco.json) til testformål.

1.  Vælg dit begivenhed Hub input, og klik på **Eksempeldata** i bunden af siden.
2.  Angiv et **Starttidspunkt** for at starte indsamling af data fra og en **varighed** til hvor meget yderligere data til at bruge, i dialogboksen.
3.  Klik på knappen Kontrollér for at starte stikprøver, hvor data fra input.  Det kan tage et minut eller to til datafilen skal være produceret.  Når processen er fuldført, skal du klikke på **Detaljer** og hente og gemme den. JSON-fil, der er oprettet.

    ![Hente og gemme behandlede data i en JSON-fil](./media/stream-analytics-get-started/stream-analytics-download-save-json-file.png)

#### <a name="passthrough-query"></a>PASSTHROUGH-forespørgslen

Hvis du ønsker at arkivere hver begivenhed, kan du bruge en passthrough forespørgsel til at læse alle felterne i data i den begivenhed eller en meddelelse. Gøre skal starte med en enkel passthrough forespørgsel pågældende projekter alle felterne i en begivenhed.

1.  Klik på **forespørgsel** fra toppen af siden Stream Analytics job.
2.  Tilføj følgende Kodeeditor:

        SELECT * FROM CallStream

    > Sørg for, at navnet på inputkilden svarer til navnet på det input, du tidligere har angivet.

3.  Klik på **Test** under forespørgselseditor.
4.  Angive en testfil, enten et, du har oprettet ved hjælp af de forrige trin, eller brug [telco.json](https://github.com/Azure/azure-stream-analytics/blob/master/Sample%20Data/telco.json).
5.  Klik på knappen Kontrollér og se resultaterne vises under definitionen af forespørgslen.

    ![Definition af forespørgselsresultater](./media/stream-analytics-get-started/stream-analytics-sim-fraud-output.png)


### <a name="column-projection"></a>Kolonne projicering

Vi vil nu lign ned de returnerede felter til en lille gruppe.

1.  Ændre forespørgslen i kodeeditoren:

        SELECT CallRecTime, SwitchNum, CallingIMSI, CallingNum, CalledNum
        FROM CallStream

2.  Klik på **Kør** under query editor for at få vist resultaterne af forespørgslen.

    ![Output i forespørgselseditor.](./media/stream-analytics-get-started/stream-analytics-query-editor-output.png)

### <a name="count-of-incoming-calls-by-region-tumbling-window-with-aggregation"></a>Antallet af indgående opkald efter område: Tumbling vindue med sammenlægning

Hvis du vil sammenligne mængden pågældende indgående opkald per område skal vi udnytte en [TumblingWindow](https://msdn.microsoft.com/library/azure/dn835055.aspx) for at få antallet af indgående opkald, der er grupperet efter SwitchNum hvert 5.

1.  Ændre forespørgslen i kodeeditoren:

        SELECT System.Timestamp as WindowEnd, SwitchNum, COUNT(*) as CallCount
        FROM CallStream TIMESTAMP BY CallRecTime
        GROUP BY TUMBLINGWINDOW(s, 5), SwitchNum

    Denne forespørgsel bruger nøgleordet **Tidsstempel ved** for at angive et tidsstempelfelt i de data, der skal bruges i tidsmæssig beregningen. Hvis dette felt ikke blev angivet, vil handlingen windowing udføres ved hjælp af den tid, der er modtaget hver enkelt hændelse på begivenheden Hub. Se ["Modtagelse af tid eller-programmet tid" i Stream analyser forespørgsel Language Reference](https://msdn.microsoft.com/library/azure/dn834998.aspx).

    Bemærk, at du kan få adgang til et tidsstempel til slutningen af hvert vindue ved hjælp af egenskaben **System.Timestamp** .

2.  Klik på **Kør** under query editor for at få vist resultaterne af forespørgslen.

    ![Resultater af en forespørgsel til Timestand ved](./media/stream-analytics-get-started/stream-ananlytics-query-editor-rerun.png)

### <a name="sim-fraud-detection-with-a-self-join"></a>SIM svindel registrering med en selv-joinforbindelse

Hvis du vil identificere potentielt falske brugen vil vi se for opkald, der kommer fra den samme bruger, men på forskellige placeringer i mindre end 5 sekunder.  Vi [joinforbindelse](https://msdn.microsoft.com/library/azure/dn835026.aspx) strømmen af opkaldshændelser med sig selv at kontrollere, om disse tilfælde.

1.  Ændre forespørgslen i kodeeditoren:

        SELECT System.Timestamp as Time, CS1.CallingIMSI, CS1.CallingNum as CallingNum1,
        CS2.CallingNum as CallingNum2, CS1.SwitchNum as Switch1, CS2.SwitchNum as Switch2
        FROM CallStream CS1 TIMESTAMP BY CallRecTime
        JOIN CallStream CS2 TIMESTAMP BY CallRecTime
        ON CS1.CallingIMSI = CS2.CallingIMSI
        AND DATEDIFF(ss, CS1, CS2) BETWEEN 1 AND 5
        WHERE CS1.SwitchNum != CS2.SwitchNum

2.  Klik på **Kør** under query editor for at få vist resultaterne af forespørgslen.

    ![Resultater af en forespørgsel i en joinforbindelse](./media/stream-analytics-get-started/stream-ananlytics-query-editor-join.png)

### <a name="create-output-sink"></a>Oprette output sink

Nu hvor vi har defineret en begivenhed stream, en begivenhed-Hub input til indtager begivenheder og en forespørgsel til at udføre en transformering over strømmen, er det sidste trin at definere en output sink til opgaven.  Vi vil skrive begivenheder til falsk funktionsmåde til Blob-lager.

Følg nedenstående trin for at oprette en beholder for Blob-lager, hvis du ikke allerede har en.

1.  Brug en eksisterende lagerplads-konto eller oprette en ny firmapost lager ved at klikke på **Ny > DATATJENESTER > LAGERPLADS > Hurtig oprettelse** og følge instruktionerne.
2.  Vælg kontoen lagerplads, skal du klikke på **beholdere** øverst på siden, og klik derefter på **Tilføj**.
3.  Angiv et **navn** for din objektbeholder og angive dens **adgang** til offentlige Blob.

## <a name="specify-job-output"></a>Angive job output

1.  Klik på **OUTPUT** fra toppen af siden i Stream Analytics tingene, og klik derefter på **Tilføj OUTPUT**. Den dialogboks, der åbnes fører dig gennem en række trin til konfiguration af din output.
2.  Vælg **BLOB-lager**, og klik derefter på knappen til højre.
3.  Skriv eller Vælg følgende værdier på den tredje side:

    * **OUTPUT ALIAS**: Angiv et fuldt navn til denne job output.
    * **Abonnement**: Hvis den Blob-lager, du har oprettet er i samme abonnement som Stream Analytics jobbet, Vælg **Brug lagerplads konto nuværende abonnement**. Hvis din lagerplads er i et andet abonnement, Vælg **Brug lagerplads konto fra et andet abonnement** , og angiv oplysninger for **LAGERPLADS konto**, **LAGERPLADS KONTONØGLE**, skal du **BEHOLDER**manuelt.
    * **LAGERPLADS konto**: Vælg navnet på kontoen, lagerplads.
    * **OBJEKTBEHOLDER**: Vælg navnet på objektbeholderen.
    * **PRÆFIKS for FILNAVN**: Angiv et præfiks, du fil skal bruges, når du skriver blob output.

4.  Klik på knappen til højre.
5.  Angiv følgende værdier:

    * **BEGIVENHED SERIALISERINGSFUNKTION FORMAT**: JSON
    * **Kodning**: UTF8

6.  Klik på knappen Kontrollér, at tilføje denne kilde og for at bekræfte, at Stream Analytics kan oprette forbindelse til kontoen lagerplads.

## <a name="start-job-for-real-time-processing"></a>Starte job for realtid behandling

Da et job input, forespørgsel og output har alle er angivet, er vi klar til at starte Stream Analytics jobbet til realtid svindel registrering.

1.  Klik på **START** nederst på siden fra jobbet **DASHBOARD**.
2.  Vælg **jobbet STARTTIDSPUNKT** i den dialogboks, der vises, og klik derefter på knappen med fluebenet nederst i dialogboksen. Jobstatus ændres til **Start** og snart flytter til **kører**.

## <a name="view-fraud-detection-output"></a>Vis svindel registrering output

Brug et værktøj som [Azure Storage Explorer](https://azurestorageexplorer.codeplex.com/) eller [Azure Explorer](http://www.cerebrata.com/products/azure-explorer/introduction) til at få vist falsk begivenheder, mens der skrives til din output i realtid.  

![Registrering af svindel: falsk hændelser vises i realtid](./media/stream-analytics-get-started/stream-ananlytics-view-real-time-fraudent-events.png)

## <a name="get-support"></a>Få support
For at få hjælp, kan du prøve vores [Azure Stream Analytics-forummet](https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureStreamAnalytics).


## <a name="next-steps"></a>Næste trin

- [Introduktion til Azure Stream Analytics](stream-analytics-introduction.md)
- [Introduktion til brug af Azure Stream Analytics](stream-analytics-get-started.md)
- [Skalere Azure Stream Analytics-job](stream-analytics-scale-jobs.md)
- [Azure Stream Analytics Query Language Reference](https://msdn.microsoft.com/library/azure/dn834998.aspx)
- [Azure Stream Analytics Management RESTEN API Reference](https://msdn.microsoft.com/library/azure/dn835031.aspx)
