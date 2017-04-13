<properties
    pageTitle="Komme i gang med forudkonfigurerede løsninger | Microsoft Azure"
    description="Følg dette selvstudium for at se, hvordan du installerer en Azure IoT pakke forudkonfigureret løsning."
    services=""
    suite="iot-suite"
    documentationCenter=""
    authors="dominicbetts"
    manager="timlt"
    editor=""/>

<tags
     ms.service="iot-suite"
     ms.devlang="na"
     ms.topic="hero-article"
     ms.tgt_pltfrm="na"
     ms.workload="na"
     ms.date="08/16/2016"
     ms.author="dobett"/>

# <a name="tutorial-get-started-with-the-preconfigured-solutions"></a>Selvstudium: Introduktion til de forudkonfigurerede løsninger

## <a name="introduction"></a>Introduktion

Azure IoT Suite [forudkonfigurerede løsninger] [ lnk-preconfigured-solutions] kombinere flere Azure IoT tjenester for at levere til slut løsninger, der implementerer almindelige forretningsscenarier IoT. Løsningen *remote overvågning* forudkonfigureret opretter forbindelse til og overvåger dine enheder. Du kan bruge løsningen til at analysere strømmen af data fra dine enheder og forbedre business resultater ved at gøre processer svare automatisk på denne strøm af data.

Dette selvstudium viser, hvordan du klargør remote overvågning forudkonfigurerede løsningen. Det også vejleder dig gennem de grundlæggende funktioner i remote overvågning løsningen. Du kan få adgang til mange af disse funktioner via dashboardet løsning, der installerer sammen med den forudkonfigurerede løsning:

![Remote overvågning forudkonfigureret løsning dashboard][img-dashboard]

For at fuldføre dette selvstudium skal have du et aktivt Azure abonnement.

> [AZURE.NOTE]  Hvis du ikke har en konto, kan du oprette en gratis prøveversion konto på blot et par minutter. Yderligere oplysninger finder du [Azure gratis prøveversion][lnk_free_trial].

[AZURE.INCLUDE [iot-suite-provision-remote-monitoring](../../includes/iot-suite-provision-remote-monitoring.md)]

## <a name="view-the-solution-dashboard"></a>Få vist dashboardet løsning

Dashboardet løsning gør det muligt at administrere udløst løsningen. For eksempel kan du se telemetri, tilføje enheder og konfigurere regler.

1.  Når den klargøring er fuldført, og feltet for din forudkonfigurerede løsning angiver **klar**, skal du klikke på **Start** for at åbne din remote overvågning løsning portal i en ny fane.

    ![Start forudkonfigurerede løsningen][img-launch-solution]

2.  Som standard viser portalen løsning *løsning dashboard*. Du kan vælge andre visninger ved hjælp af menuen til venstre.

    ![Remote overvågning forudkonfigureret løsning dashboard][img-dashboard]

Dashboard viser følgende oplysninger:

- Kortet viser placeringen af hver enhed, der er forbundet med løsningen. Når du første gang løsningen, er der fire simuleret enheder. Simuleret enhederne er implementeret som Azure WebJobs, og løsningen bruger Bing Maps API til at afbilde oplysninger på kortet.
- Panelet **Telemetri oversigt** plottes fugtindhold og temperatur telemetri fra en bestemt enhed i nær realtid og viser aggregere data som maksimale, minimum, og den gennemsnitlige fugtighed.
- Panelet **Alarm oversigt** viser seneste alarm hændelser, når værdien telemetri har overskredet grænseværdi for en. Du kan definere dine egne alarmer ud over de eksempler, der er oprettet af den forudkonfigurerede løsning.

## <a name="view-the-device-list"></a>Få vist listen over enheder

Listen over enheder viser alle de enheder, der er registreret i løsningen. Du få vist og redigere enhedsmetadata for, tilføje eller fjerne enheder og sende kommandoer til enheder.

1.  Klik på **enheder** i venstre menu for at få vist på *Enhedslisten* for denne løsning.

    ![Liste over enheder i dashboard][img-devicelist]

2.  Enhedslisten viser, at der er fire simuleret enheder, der er oprettet af klargøring processen.

3.  Klik på en enhed på Enhedslisten for at få vist detaljer for enhed.

    ![Detaljer for enhed i dashboard][img-devicedetails]

Panelet **Detaljer for enhed** indeholder tre sektioner:

- Sektionen **Handlinger** vises de handlinger, du kan udføre på enheden. Hvis du deaktiverer enheden, er det ikke længere tilladelse til at sende telemetri eller modtage kommandoer. Hvis du deaktiverer en enhed, kan du derefter aktivere den igen. Du kan tilføje en regel, der er knyttet til den enhed, der udløser en alarm, når en telemetri værdi er større end en grænseværdi. Du kan også sende en kommando til en enhed. Når en enhed opretter først forbindelse, fortæller den løsningen de kommandoer, den kan svare på.
- Sektionen **Egenskaber for enheden** viser metadataene enhed. Nogle af disse metadata stammer fra selve enheden (såsom producenten), og nogle genereres af løsningen (såsom den oprettede tid). Du kan redigere metadataene enhed her.
- Sektionen **Godkendelse taster** viser de nøgler enheden kan bruge til at godkende med løsningen.

## <a name="send-a-command-to-a-device"></a>Sende en kommando til en enhed

Detaljeruden enhed viser alle de kommandoer, der understøtter en bestemt enhed og gør det muligt for dig om at sende kommandoer til en enhed. Når en enhed første gang starter, sender oplysninger om de kommandoer, der understøtter til løsningen.

1.  Klik på **kommandoer** i ruden enhed detaljer for den valgte enhed.

    ![Enhed kommandoer i dashboard][img-devicecommands]

2.  Vælg **PingDevice** fra listen over kommandoer.

3.  Klik på **kommandoen Send**.

4.  Du kan se status for kommandoen i kommandooversigten.

    ![Kommandostatus i dashboard][img-pingcommand]

Løsningen registrerer status for hver kommando, sender programmet. Resultatet er først **ventende**. Når enheden rapporterer, at det har kørt kommandoen, er resultatet angivet til **succes**.

## <a name="add-a-new-simulated-device"></a>Tilføje en ny simuleret enhed

Når du installerer den forudkonfigurerede løsning, kan du automatisk klargøre de fire eksempel enheder, du kan se på Enhedslisten. Disse enheder er *simuleret enheder* , der kører på en Azure WebJob. Simuleret enheder gør det nemt for dig at eksperimentere med den forudkonfigurerede løsning uden at skulle installere reelt tal, fysiske enheder. Hvis du vil tilslutte en reelle enhed til løsningen, kan du se [forbinde enheden til eksterne overvågning forudkonfigurerede løsning] [ lnk-connect-rm] selvstudium.

Følgende trin viser, hvordan du føjer en simuleret enhed til løsningen:

1.  Gå tilbage til listen over enheder.

2.  Klik på **+ Tilføj A enhed** i nederste venstre hjørne at tilføje en enhed.

    ![Føje en enhed til forudkonfigurerede løsningen][img-adddevice]

3.  Klik på **Tilføj ny** i feltet **Simuleret enhed** .

    ![Angive nye detaljer for enhed i dashboard][img-addnew]
    
    Ud over at oprette en ny simuleret enhed, kan du også tilføje en fysisk enhed, hvis du vælger at oprette en **Brugerdefineret enhed**. Hvis du vil vide mere om at oprette forbindelse fysiske enheder til løsningen, skal du se [forbinde enheden til pakken IoT remote overvågning forudkonfigurerede løsning][lnk-connect-rm].

4.  Vælg **Lad mig definere mine egne enheds-ID**, og Angiv et entydigt enheds-ID navn som **mydevice_01**.

5.  Klik på **Opret**.

    ![Gemme en ny enhed][img-definedevice]

6. I trin 3 i **føje en simuleret enhed**, skal du klikke på **udført** for at vende tilbage til listen over enheder.

7. Du kan se din enhed, der **kører** på Enhedslisten.

    ![Vis nye enhed på listen enhed][img-runningnew]

8. Du kan også få vist simuleret telemetri fra enheden nye på dashboardet til:

    ![Få vist telemetri fra nye enhed][img-runningnew-2]

## <a name="edit-the-device-metadata"></a>Redigere enhedsmetadata for

Når en enhed først opretter forbindelse til løsningen, sender dens metadata til løsningen. Når du redigerer enhedsmetadata via dashboardet løsning, sender de nye metadataværdier til enheden og gemmer de nye værdier i løsning DocumentDB-databasen. Få mere at vide under [enhed identitet registreringsdatabasen og DocumentDB][lnk-devicemetadata].

1.  Gå tilbage til listen over enheder.

2.  Vælg din nye enhed på **Listen enheder**, og klik derefter på **Rediger** for at redigere **Egenskaber for enheden**:

    ![Redigere enhedsmetadata for][img-editdevice]

3. Rul ned, og foretage en ændring i bredde- og længdegrader vales. Klik derefter på **Gem ændringer til enheden registreringsdatabasen**.

    ![Redigere enhedsmetadata for][img-editdevice2]

4. Gå tilbage til dashboard, placeringen af enhed er blevet ændret på kortet:

    ![Redigere enhedsmetadata for][img-editdevice3]

## <a name="add-a-rule-for-the-new-device"></a>Tilføje en regel for den nye enhed

Der findes ingen regler for den nye enhed, du lige har tilføjet. I dette afsnit, skal tilføje du en regel, der udløser en alarm, når den nye enhed har rapporteret temperaturen overstiger 47 grader. Inden du starter, Bemærk, at historikken for den nye enhed på dashboardet til telemetri viser enhed temperaturen overstiger aldrig 45 grader.

1.  Gå tilbage til listen over enheder.

2.  Vælg din nye enhed på **Listen enheder**, og klik derefter på **Tilføj regel** for at tilføje en regel for enheden.

3. Oprette en regel, der bruger **temperaturen** som datafeltet og bruger **AlarmTemp** som output, når temperaturen overstiger 47 grader:

    ![Tilføj en regel for enhed][img-adddevicerule]

4. Klik på **Gem og få vist regler** for at gemme ændringerne.

5.  Klik på **kommandoer** i ruden enhed detaljer for den nye enhed.

    ![Tilføj en regel for enhed][img-adddevicerule2]

6.  Vælg **ChangeSetPointTemp** fra listen over kommandoer, og angiv **SetPointTemp** 45. Klik derefter på **Kommandoen Send**:

    ![Tilføj en regel for enhed][img-adddevicerule3]

7.  Gå tilbage til løsning dashboard. Efter kort tid vises der en ny post i ruden **Oversigt over Alarm** når temperaturen rapporteret af din nye enhed overstiger grænsen 47 graders:

    ![Tilføj en regel for enhed][img-adddevicerule4]

8. Du kan læse og redigere alle regler for på siden **regler** i dashboardet:

    ![Listen enhed regler][img-rules]

9. Du kan læse og redigere alle de handlinger, der kan udføres som svar på en regel på siden **Handlinger** i dashboardet:

    ![Listen enhed handlinger][img-actions]

> [AZURE.NOTE] Det er muligt at definere handlinger, der kan sende en mail eller SMS som svar på en regel eller integrere med en line of business systemet via en [Logik App][lnk-logic-apps]. Du kan finde flere oplysninger, se, [oprette forbindelse logik App til din Azure IoT pakke Remote overvågning forudkonfigureret løsning][lnk-logicapptutorial].

## <a name="other-features"></a>Andre funktioner

Ved hjælp af portalen løsning, kan du søge til enheder med specifikke egenskaber som model:

![Søge efter en enhed][img-search]

Du kan deaktivere en enhed, og når det er deaktiveret, kan du fjerne den:

![Deaktiver og Fjern en enhed][img-disable]

## <a name="behind-the-scenes"></a>I baggrunden

Når du installerer en forudkonfigureret løsning, opretter installationsprocessen flere ressourcer i Azure abonnement, du har valgt. Du kan få vist disse ressourcer i Azure [portal][lnk-portal]. Installationsprocessen opretter en **ressourcegruppe** med et navn, der er baseret på det navn, du vælger til din forudkonfigurerede løsning:

![Forudkonfigurerede løsning på portalen Azure][img-portal]

Du kan få vist indstillingerne for hver ressource ved at markere den på listen over ressourcer i ressourcegruppen.

Du kan også få vist koden for den forudkonfigurerede løsning. Remote overvågning forudkonfigurerede løsning koden er i [azure-iot-remote-overvågning] [ lnk-rmgithub] GitHub lager:

- Mappen **DeviceAdministration** indeholder koden til dashboard.
- Mappen **Simulator** indeholder kildekode til simuleret enhed.
- Mappen **EventProcessor** indeholder koden til back end-processen, der håndterer indgående telemetri.

Når du er færdig, kan du slette den forudkonfigurerede løsning fra dit Azure-abonnement på [azureiotsuite.com] [ lnk-azureiotsuite] websted. Dette websted kan du nemt slette alle de ressourcer, der blev klargjort, da du oprettede den forudkonfigurerede løsning.

> [AZURE.NOTE] For at sikre, at du sletter alt i forbindelse med den forudkonfigurerede løsning, skal du slette den på [azureiotsuite.com] [ lnk-azureiotsuite] websted og ikke blot slette ressourcegruppen i portalen.

## <a name="next-steps"></a>Næste trin

Nu hvor du har installeret en fuldt fungerende forudkonfigureret løsning, kan du fortsætte med at komme i gang med IoT pakke ved at læse følgende artikler:

- [Remote overvågning forudkonfigureret løsning gennemgang][lnk-rm-walkthrough]
- [Tilslut din enhed til remote overvågning forudkonfigurerede løsningen][lnk-connect-rm]
- [Tilladelser på webstedet azureiotsuite.com][lnk-permissions]

[img-launch-solution]: media/iot-suite-getstarted-preconfigured-solutions/launch.png
[img-dashboard]: media/iot-suite-getstarted-preconfigured-solutions/dashboard.png
[img-devicelist]: media/iot-suite-getstarted-preconfigured-solutions/devicelist.png
[img-devicedetails]: media/iot-suite-getstarted-preconfigured-solutions/devicedetails.png
[img-devicecommands]: media/iot-suite-getstarted-preconfigured-solutions/devicecommands.png
[img-pingcommand]: media/iot-suite-getstarted-preconfigured-solutions/pingcommand.png
[img-adddevice]: media/iot-suite-getstarted-preconfigured-solutions/adddevice.png
[img-addnew]: media/iot-suite-getstarted-preconfigured-solutions/addnew.png
[img-definedevice]: media/iot-suite-getstarted-preconfigured-solutions/definedevice.png
[img-runningnew]: media/iot-suite-getstarted-preconfigured-solutions/runningnew.png
[img-runningnew-2]: media/iot-suite-getstarted-preconfigured-solutions/runningnew2.png
[img-rules]: media/iot-suite-getstarted-preconfigured-solutions/rules.png
[img-editdevice]: media/iot-suite-getstarted-preconfigured-solutions/editdevice.png
[img-editdevice2]: media/iot-suite-getstarted-preconfigured-solutions/editdevice2.png
[img-editdevice3]: media/iot-suite-getstarted-preconfigured-solutions/editdevice3.png
[img-adddevicerule]: media/iot-suite-getstarted-preconfigured-solutions/addrule.png
[img-adddevicerule2]: media/iot-suite-getstarted-preconfigured-solutions/addrule2.png
[img-adddevicerule3]: media/iot-suite-getstarted-preconfigured-solutions/addrule3.png
[img-adddevicerule4]: media/iot-suite-getstarted-preconfigured-solutions/addrule4.png
[img-actions]: media/iot-suite-getstarted-preconfigured-solutions/actions.png
[img-portal]: media/iot-suite-getstarted-preconfigured-solutions/portal.png
[img-search]: media/iot-suite-getstarted-preconfigured-solutions/solutionportal_07.png
[img-disable]: media/iot-suite-getstarted-preconfigured-solutions/solutionportal_08.png

[lnk_free_trial]: http://azure.microsoft.com/pricing/free-trial/
[lnk-preconfigured-solutions]: iot-suite-what-are-preconfigured-solutions.md
[lnk-azureiotsuite]: https://www.azureiotsuite.com
[lnk-logic-apps]: https://azure.microsoft.com/documentation/services/app-service/logic/
[lnk-portal]: http://portal.azure.com/
[lnk-rmgithub]: https://github.com/Azure/azure-iot-remote-monitoring
[lnk-devicemetadata]: iot-suite-what-are-preconfigured-solutions.md#device-identity-registry-and-documentdb
[lnk-logicapptutorial]: iot-suite-logic-apps-tutorial.md
[lnk-rm-walkthrough]: iot-suite-remote-monitoring-sample-walkthrough.md
[lnk-connect-rm]: iot-suite-connecting-devices.md
[lnk-permissions]: iot-suite-permissions.md
