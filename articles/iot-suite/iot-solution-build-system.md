<properties
    pageTitle="MyDriving Azure IoT eksempel: oprette den | Microsoft Azure"
    description="Oprette en app, som er en omfattende demonstration af, hvordan du udvikle et IoT system ved hjælp af Microsoft Azure, herunder Stream Analytics, Machine Learning, f.eks begivenhed."
    services=""
    documentationCenter=".net"
    suite=""
    authors="harikmenon"
    manager="douge"/>

<tags
    ms.service="multiple"
    ms.workload="tbd"
    ms.tgt_pltfrm="ibiza"
    ms.devlang="dotnet"
    ms.topic="article"
    ms.date="03/25/2016"
    ms.author="harikm"/>


# <a name="build-and-deploy-the-mydriving-solution-to-your-environment"></a>Opbygge og anvende MyDriving løsningen til dit miljø

MyDriving er en Internet af ting (IoT), som samler data fra din bil, behandler den ved hjælp af maskine læring og præsenterer dem på din mobiltelefon. Back-end består af en række tjenester, som Microsoft Azure. Klienterne, der kan være Android-, iOS- eller Windows 10 telefoner.

Vi oprettede MyDriving løsningen for at give dig en jumpstart i at oprette dine egne IoT system. Fra [MyDriving lager på GitHub](https://github.com/Azure-Samples/MyDriving), kan du få Azure ressourcestyring scripts til at installere den back end-arkitektur til dine egne Azure-konto. Fra det pågældende tidspunkt, kan du omkonfigurere de forskellige tjenester, ændrer forespørgsler for at så den passer til dine egne data, og så videre. Du kan finde disse scripts – sammen med kode for mobilappen, Azure App Service API projektet og mere – i MyDriving-lager.

Hvis du ikke har prøvet appen endnu, kan du se på [få Introduktion vejledning](iot-solution-get-started.md).

Der findes en detaljeret oversigt over arkitekturen i [MyDriving oversigtsvejledning](http://aka.ms/mydrivingdocs). Oprette et lignende projekt i oversigt, der er flere stykker, som vi har konfigureret, og, du vil angive op til:

* En **klient app** kører på Android-, iOS- og Windows 10 telefoner. Vi bruger Xamarin platformen til at dele meget af den kode, der er lagret på GitHub under `src/MobileApp`. Appen faktisk udfører to forskellige funktioner:
 * Den videresender telemetri fra den indbyggede diagnosticering (OBD) enhed og fra sin egen placering tjeneste til systemets skyen back-end.
 * Det er en brugergrænseflade, hvor brugerne kan forespørge om deres registrerede køreture.
* En **skybaseret tjeneste** ingests vej forretningsrejse dataene i realtid og behandler den. Primære arbejdet for oprettelse af denne tjeneste er at vælge, standardelementet og ledningsomrids af en række forskellige Azure tjenester. Nogle af delene kræver scripts filter og proces indgående dataene. Vi bruger en skabelon til Azure ressourcestyring til at konfigurere alle dele.
* En **mobile service app** er webtjeneste bag bruger interface en del af appen enhed. Den primære job er forespørgsler til databasen med lagrede, behandlede data. Koden er på GitHub under `src/MobileAppService`.
* **Visual Studio med Xamarin** er vores udviklingsmiljø. Xamarin, som findes både som en komponent i Visual Studio og som en separat integreret udviklingsmiljø (IDE), der bruges til at opbygge koden på tværs af platforme enhed. For at opbygge iOS-kode, er det nødvendigt at have en forekomst af Xamarin, der kører på en OS X-computer. Hvis det er nødvendigt, kan den køres som en agent, administreres fra Visual Studio.
* **Test af enhed** enhed Apps udføres i Xamarin Test skyen.
* **GitHub** er det lager, hvor vi gemme kode, scripts og skabeloner.
* **Visual Studio Team Services** er en skybaseret tjeneste, der bruges til at administrere fortløbende build og test af online-tjenesten og enhed.
* **HockeyApp** bruges til at distribuere versioner af koden enhed. Også indsamler nedbrud og brugsrapporter og feedback fra brugere.
* **Visual Studio programmet indsigt** overvåger mobile webtjenesten.

Så lad os se, hvordan vi konfigurere over dette. Bemærk, at mange af trinnene er valgfrit.

## <a name="sign-up-for-accounts"></a>Tilmelde sig konti

-   [Visual Studio-Udviklingscenter Essentials](https://www.visualstudio.com/products/visual-studio-dev-essentials-vs.aspx). Dette gratis program giver nem adgang til mange udviklerværktøjer og tjenesterne, herunder Visual Studio, Visual Studio Team Services og Azure. Du får en 25/ måned kredit Azure for 12 måneder. Den indeholder også abonnementer til Pluralsight uddannelse og Xamarin University. Du kan også tilmelde dig separat gratis niveauer af [Azure](https://azure.com) og [Visual Studio Team Services](https://www.visualstudio.com/products/visual-studio-team-services-vs.aspx), men disse leverer ikke Azure kredit.

-   [HockeyApp](https://rink.hockeyapp.net/) (valgfrit), for at administrere test fordelingen af mobilapps og indsamling af telemetri.

-   [Xamarin](https://xamarin.com/) (påkrævet), for at opbygge mobilappen og køre fejlfinding kører og tester på [Xamarin Test skyen](https://xamarin.com/test-cloud).

-   [GitHub](https://github.com/Azure-Samples/MyDriving/) (valgfrit), til at oprette gratis offentlige lagre til din egen programkode (private lagre er betalt). Du kan også bruge den grundlæggende plan i Visual Studio Team Services til private lagre.

-   [Power BI](https://powerbi.microsoft.com/) (valgfrit), til at oprette omfattende visualiseringer af data på tværs af hele systemet.

> [AZURE.NOTE] Du behøver ikke en GitHub konto for at få adgang til MyDriving koden i [GitHub MyDriving-lager](https://github.com/Azure-Samples/MyDriving).

## <a name="install-development-tools"></a>Installere værktøjer til udvikling af

Følgende opsætning er til udvikling af løsningen fuld: en iOS-, Android, og Windows 10 Mobile på tværs af platforme app, med en Azure tilbage slutningen.

Som alternativ kan du bruge Xamarin Studio på Mac eller Windows til at udvikle apps til mobil, hvis du ikke arbejder på Azure tilbage slutningen.

Der findes en [længere beskrivelse af denne konfiguration](https://msdn.microsoft.com/library/mt613162.aspx).

### <a name="windows-development-machine"></a>Windows udvikling maskine

Værktøjet central på Windows er Visual Studio til at arbejde med MyDriving appen til Android og Windows, App-API'EN project og microservice filtypenavne.

Xamarin, ciffer, emulatorer og andre nyttige komponenter er alle integreret med Visual Studio.

Installation:

-   [Visual Studio-2015 med Xamarin](https://www.visualstudio.com/products/visual-studio-community-vs) (en hvilken som helst edition – Community er gratis).

-   [SQLite til Universal Windows-Platform](https://visualstudiogallery.msdn.microsoft.com/4913e7d5-96c9-4dde-a1a1-69820d615936). Kræves for at opbygge Windows 10 Mobile koden.

-   [Azure SDK til Visual Studio-2015](https://go.microsoft.com/fwlink/?linkid=518003&clcid=0x409). Giver dig SDK til kørsel af apps i Azure, sammen med kommandolinjen værktøjer til administration af Azure.

-   [Azure Service-strukturen SDK](http://www.microsoft.com/web/handlers/webpi.ashx?command=getinstallerredirect&appid=MicrosoftAzure-ServiceFabric). Kræves for at opbygge filtypenavnet [microservice](../service-fabric/service-fabric-get-started.md) .

Sørg også for, at du har de rette Visual Studio-udvidelser. Kontrollér, at under **værktøjer**, du ser **Android, iOS, Xamarin …**. Hvis ikke, Åbn Kontrolpanel, og vælg derefter **programmer og funktioner** > **Microsoft** > **Visual Studio 2015** > **Rediger**. Vælg under **på tværs af platforme udvikling**, **C\#/.Net (Xamarin)**. Mens du er der, kan du kontrollere, at **Ciffer til Windows** er installeret.

### <a name="mac-development-machine"></a>Mac udvikling maskine

Mac (Yosemite eller nyere) er påkrævet, hvis du vil udvikle til iOS. Selvom vi bruge Visual Studio med Xamarin på Windows til at udvikle og administrere al kode, bruger Xamarin en agent, der er installeret på en Mac for at opbygge og logge på iOS-kode.

![Udvikle på Windows og udbygges med Mac](./media/iot-solution-build-system/image1.png)

(Som et alternativ kan du bruge Xamarin Studio direkte på Mac du udvikler apps på tværs af platforme.)

Du behøver Mac, hvis du ikke vil medtage iOS som en destination platform.

Installation:

-   [Xamarin Studio til iOS](https://developer.xamarin.com/guides/ios/getting_started/installation/mac/). Du kan også konfigurere Visual Studio og Xamarin på en Mac, der kører en Windows virtuel maskine. Se [konfiguration, installation og kontrol af Mac-brugere](https://msdn.microsoft.com/library/mt488770.aspx) på MSDN.

-   [Værktøjer til Azure udvikling](https://azure.microsoft.com/downloads/) (valgfrit).

Aktivere ekstern logon til Mac. Åbn **Systemindstillinger** > **Deling**, og vælg derefter **Remote Login**.

Når du åbner en iOS-projekt i Visual Studio på Windows, dig den plug-in'en Xamarin beder om ID Mac.

## <a name="fetch-the-github-repository"></a>Hente GitHub lager

Hente en lokal kopi af [GitHub MyDriving lager](https://github.com/Azure-Samples/MyDriving) ved hjælp af knappen **Download ZIP** i GitHub, Visual Studio eller en anden ciffer klient.

Udpakke filen til en mappe med et kort stinavn, som C:\\kode.

Du kan også, hvis du vil holde dig opdateret med eller bidrage til vores kode, klone lageret på følgende måde:

**ciffer Klon https://github.com/Azure-Samples/MyDriving.git**

## <a name="get-a-bing-maps-api-key"></a>Få en Bing maps API-nøgle

[Tilmeld dig en Bing Maps API-nøgle](https://msdn.microsoft.com/library/ff428642.aspx).

Du skal erstatte dette i linje 22 i `src/MobileApps/MyDriving/MyDriving.Utils/Logger.cs`.



## <a name="build-the-demo-app"></a>Opbygge appen demo

Åbne disse løsninger i Visual Studio:

-   src\MobileApps\MyDriving.SLN

-   src\MobileAppService\MyDrivingService.SLN

-   src\Extensions\ServiceFabric\VINLookUpApplication\VINLookUpApplication.SLN

Du får instruktionerne til at:

-   Have tillid til nogle potentielt upålidelige projekter. Vælg for at åbne dem, hvis du vil gå frem.

-   Angive udviklertilstand, hvis du arbejder på et nyt Windows 10-computer.

-   Angive dine Xamarin legitimationsoplysninger.

-   Oprette forbindelse til Xamarin Mac. Hvis du ikke har en Mac, skal du højreklikke på iOS projektet i Visual Studio, og vælg derefter **Fjern projekt**.

Genopbygge løsningen.

Hvis du har problemer med at opbygge, skal du prøve løsningerne til Strict, som vi har fundet:

-   *VINLookupApplication project kan ikke indlæses*: Sørg for, at du har installeret [Azure SDK til Visual Studio-2015](https://go.microsoft.com/fwlink/?linkid=518003&clcid=0x409).

-   *Tjenesten strukturen project ikke opbygge*: Opret interface projekter først, og Sørg for, at du har installeret Service-strukturen SDK.

-   *Android-appen ikke opbygge*:

    -   Åbn **Funktioner** > **Android** > **Android SDK Manager**, og Sørg for, at den Android 6 (API 23) / SDK Platform er installeret.

    -   Slet denne mappe, og derefter genopbygge:<br/>
        `%LocalAppData%\Xamarin\zips`

## <a name="get-to-know-the-code"></a>Bliv fortrolig med koden

I løsningen finder du:

-   Azure filtypenavne: Service struktur.

-   Azure HDInsight: Scripts til at behandle forretningsrejse data i Azure.

-   Mobilapps: Enhed apps.

-   MobileAppsService/MyDrivingService: Internettet tilbage afsluttes.

-   Power BI: Den ønskede rapportdefinition.

-   Scripts:

    -   Ressourcestyring: skabeloner til at oprette de Azure ressourcer.

    -   PowerShell: Scripts til at køre ressourcestyring skabeloner.

    -   Azure SQL-Database: Fejlfinding databaser.

-   SQL-Database: CreateTables: skema definitioner.

-   Azure Stream Analytics: Forespørgsler, der transformerer indgående datastrømmen.

## <a name="run-the-apps-in-development-mode"></a>Køre apps i udviklingstilstand

Tage skridt for at køre apps, baseret på den enhed, du bruger:

-  Back-end: angive MyDrivingService som Start project, og tryk på F5 for at køre back end-webtjenesten. En browservisning af listen API, åbnes.

-  Mobilklienter: de [mobilapps udvikles på Xamarin](https://developer.xamarin.com/guides/cross-platform/deployment,_testing,_and_metrics/debugging_with_xamarin/).
 -  Android: For oplysninger, se [Fejlfinding Android i Xamarin](http://developer.xamarin.com/guides/android/deployment,_testing,_and_metrics/debugging_with_xamarin_android/).

 -  iOS: Yderligere oplysninger finder du [fejlfinding i iOS](http://developer.xamarin.com/guides/ios/deployment,_testing,_and_metrics/debugging_in_xamarin_ios/).

 -  Windows Phone: Yderligere oplysninger finder du [Xamarin + Windows Phone](https://developer.xamarin.com/guides/cross-platform/windows/phone/).

## <a name="upload-the-mobile-app-to-hockeyapp"></a>Overføre mobilappen til HockeyApp

HockeyApp håndterer fordelingen af din Android-, iOS- eller Windows-app til at teste brugere, underrette brugerne af nye versioner. Den indsamler også nyttig nedbrud rapporter, bruger feedback med skærmbilleder, og brug målepunkter.

[Start ved at uploade](http://support.hockeyapp.net/kb/app-management-2/how-to-create-a-new-app) din app build. Derefter logge på [HockeyApp](https://rink.hockeyapp.net) fra computeren udvikling. Klik på **Ny App**på dashboardet til udvikler, og træk derefter de indbyggede filer ind i vinduet. (Senere, kan du automatisere din build tjeneste for at gøre dette.)

Nu er du i dashboardet app.

![Fanen Oversigt på dashboardet til app](./media/iot-solution-build-system/image2.png)

Gentag processen for hver platform, som din app kører på. Derefter kan du gøre følgende:

-  Du kan bruge [app-ID](http://support.hockeyapp.net/kb/app-management-2/how-to-find-the-app-id) fra dashboardet for at sende nedbrud data og feedback fra din app. Opdater id'er i src/MobileApps/MyDriving/MyDriving.Utils/Logger.cs i MyDriving.

-  [Inviter testbrugere](http://support.hockeyapp.net/kb/app-management-2/how-to-invite-beta-testers). Du får en URL-adressen til rekruttering testere brugere. De skal kunne tilmelde sig dit team, kan du hente appen og sender dig feedback.

-  Hvis du foretrækker en mere åbent betaversion, kan du angive fordelingen for offentligheden. Klik på **Administrer App** > **fordeling** > **hente = offentlige**. Nu alle kan hente din app og sender dig feedback, og de får vist en meddelelse, når du skriver en ny version. Du kan få nogle nedbrud rapporter fra dem for.

    ![Teams på dashboardet](./media/iot-solution-build-system/image3.png)

-  [Linket nedbrud rapporter til Visual Studio Team Services](http://support.hockeyapp.net/kb/third-party-bug-trackers-services-and-webhooks/how-to-use-hockeyapp-with-visual-studio-team-services-vsts-or-team-foundation-server-tfs). Klik på **Administrer App** > **Visual Studio Team Services**. HockeyApp kan automatisk oprette arbejdselementer i Team Services, når der er nedbrud rapporter, eller når der modtages feedback.

Få mere at vide på [HockeyApp websted](https://hockeyapp.net).

## <a name="test-the-mobile-app-on-xamarin-test-cloud"></a>Teste mobilappen på Xamarin Test skyen

[Xamarin Test skyen](https://developer.xamarin.com/guides/testcloud/introduction-to-test-cloud/) automatiserer Brugergrænsefladen test på reelle enheder i skyen. Ved hjælp af NUnit framework, kan du skrive test, der kører din app via brugergrænsefladen.

Hvis du vil bruge Xamarin, skal indarbejder du [Xamarin.UITests](https://developer.xamarin.com/guides/testcloud/uitest/intro-to-uitest/) SDK i din app, som leveres som en NuGet pakke. Du finder i demo-app, og den følger, når du opretter nye testprojekter med Xamarin skabeloner.

![Sådan finder du i tværs af platforme SDK på grænsefladen](./media/iot-solution-build-system/image4.png)

Et eksempel testprojekt er inkluderet i appen i lageret. Se under [src](https://github.com/Azure-Samples/MyDriving/tree/master/src)/MobileApps/[MyDriving](https://github.com/Azure-Samples/MyDriving/tree/master/src/MobileApps/MyDriving)/MyDriving.UITests/ [MyDriving](https://github.com/Azure-Samples/MyDriving/tree/master/src/MobileAppService).

Hvis du bruger et Visual Studio Team Services build, er det nemt at skrive Xamarin UI test af enhed og køre dem som en del af din build.

## <a name="deploy-azure-services"></a>Installere Azure services

For at udføre en automatisk installation af Azure services og Team Services build services skal referere til den detaljerede vejledning i **scripts/README.md**.

Microsoft Azure leverer en lang række forskellige tjenester, du kan bruge til at opbygge skyen programmer. Selvom mange kan bruges enkeltvis (såsom App Service/Web Apps), er de i sig bedst, når de er forbundet til et integreret system have, at vi bruger i MyDriving formular.

Det er muligt at oprette og forbinde Azure services manuelt, men det er meget hurtigere og mere pålidelig bruge Azure ressourcestyring skabeloner. [Ressourceleder, der](../azure-resource-manager/resource-group-overview.md) bruges til automatiserer installation af en løsning ressourcer og foretage samkøringslinjerne mellem dem.

Du finder skabelonen til MyDriving system i GitHub lager under [scripts/ARM](https://github.com/Azure-Samples/MyDriving/tree/master/scripts/ARM). Det giver en omfattende og kortfattet visning af hvordan de forskellige tjenester i vores arkitektur er forbundet. Vi forklarer alle disse i detaljer i [MyDriving oversigtsvejledning](http://aka.ms/mydrivingdocs), men du kan lære mange lige ved at læse gennem selve skabelonen.

> [AZURE.NOTE] Mest Azure tjenester har en tilknyttet omkostninger, afhængigt af det priser niveau. Hvis du er ny bruger Azure, kan du [prøve den gratis](https://azure.microsoft.com/free/). Men hvis du ikke planlægger at bruge visse komponenter i MyDriving-systemet, Sørg for at fjerne dem for at undgå af foretage omkostninger. Afsnittet "Anslå funktionsdygtige omkostninger" senere i denne artikel indeholder en oversigt over typiske serviceudgifter.

### <a name="edit-the-template"></a>Rediger skabelonen

Hvis du vil tilpasse installationen, måske at fjerne unødvendige komponenter eller for at tilføje andre først tage en kopier af scenarie\_complete.params.json og scenarie\_complete.json, til at foretage ændringer.

Du kan bruge dette scenario\_complete.params.json fil at overskrive forskellige standardværdier, som tjenesten SKU eller typen lagerplads gentagelse, som det er beskrevet i følgende tabel. Standardværdierne Vælg de laveste omkostninger indstillinger.

| **Parameter**         | **Beskrivelse**                | **Standardværdi** |
|--------|---------|-------|
| IoT Hub SKU           | Trin til Azure IoT Hub-tjenesten | F1                |
| Lagerplads kontotype  | Lagerplads gentagelse type       | Standard LRS      |
| SQL-tjenesten mål | På dokumentsammenfald slot forbrug   | DW100             |
| Hosting Plan SKU      | Serviceaftale til App-tjenesten   | F1                |

I scenarie\_complete.json:

-   Søg efter "baseName", og Omdøb den til et navn, du foretrækker.

-   Søg efter "Oprette". Hver af disse sektioner opretter en ressource.

-   Angiv sqlServerAdminLogin og sqlServerAdminPassword til passende værdier.

-   Før du sletter en sektion, som opretter en ressource, kan du undersøge, om det har efterfølgende ved at søge efter navnet et andet sted i filen. Bemærk, at hver sektion, som opretter en tjeneste omfatter en *dependsOn* sektion, der viser dets afhængigheder.

Her er, hvad skabelonen konfigurerer. Der er detaljer i [Oversigtsvejledning](http://aka.ms/mydrivingdocs).

| **Tjenesten**                 | **Beskrivelse og detaljer**  
|---|----
| Lagerplads konti            | Skabelonen opretter tre konti:                                                                                                                                                                       
|| -En SQL-database, der modtager aggregeret telemetri fra Stream analyser og fungerer som sikkerhedskopiering store Azure App Service tabeller, der viser disse data gennem API slutpunkter.                      
|| -Blob-lager, der indsamles historiske data fra et andet Stream Analytics-job, der skal behandles af HDInsight.                                                                                         
|| -En SQL-database, der modtager resultater behandlet af HDInsight til brug med Power BI.                                                                                                                 
| Azure IoT Hub                     | Opretter en tovejsforbindelse til hver forbundne enhed. I løsningen MyDriving fungerer mobilappen som et felt gateway til at sende data til Azure IoT Hub. Azure IoT Hub fungerer derefter som input til Stream analyser. |
| Azure begivenhed Hubs                   | Output for et Stream Analytics-job, køer output til filtypenavne, der er oprettet med Azure Service struktur.                                                                                               
| Azure SQL datawarehouse          |                                                                                                                                                                                                            
| Streame Analytics-job | Tilslut input og output med en forespørgsel, der bruges til at samle både realtid og historiske data for Appen Service API'er, Azure Machine Learning, filtypenavne og Power BI.                               
| Machine Learning arbejdsområde  | Omfatter forsøg, R kode og API-tjenesten.                                                                                                                                                              
| Azure Data Factory                | Planlagt Machine Learning omskoling.                                                                                                                                                                     
| Tjenesten strukturen hosting plan | For filtypenavne.                                                                                                                                                                                            
| App Service ("-Mobilappen")  | Vært Mobile Apps API projektet, der indeholder slutpunkter til mobilappen. API koden skal installeres til App-tjenesten fra Visual Studio.                                                         
| Regler for påmindelser                 | Sender du mail Hvis app svar angiver mislykkede forsøg.                                                                                                                                            
| Programmet indsigt        | Til overvågning af ydeevnen af API'er i App-tjeneste. Du skal konfigurere forbindelsen i Visual Studio.                                                                                          
| Azure vigtige samling                   | For at gemme web service klynge certifikat.                                                                                                                                                                

### <a name="run-the-template"></a>Køre skabelonen

I **scripts/README.md**findes der detaljerede anvisninger til kørsel af skabelonen.

Hvis du vil klargøre alle gør disse tjenester på din egen Azure-konto ved hjælp af scriptet et af følgende:

-   Bruge PowerShell:

    ```

    cd scripts/PowerShell;
    deploy.ps1 *location* *resourceGroupName*
    ```

 -   *placering* er [Azure placering](https://azure.microsoft.com/regions/), såsom `North Europe` eller `West US`. Brug `Get-AzureLocation` til at finde en liste over tilgængelige placeringer.

 -   *resourceGroupName* er det navn, du vil give til den gruppe, som alle ressourcerne skal tilhøre. Når du er færdig med ressourcerne, kan du slette dem alle sammen ved at slette denne gruppe.

-   Køre DeploymentScripts/Bash/deploy.sh med fest.

-   Åbn, og opbygge Visual Studio-løsning DeploymentScripts/VS/DeployARM.sln.

Bemærk, at hver gang skabelonen, der kører, programmet opretter en ny række ressourcer med nye navne. For at slette ressourcerne, skal du gå til portalen og slette ressourcegruppen.

Hvis scriptet af en eller anden grund ikke lykkes, kan du køre den igen.

Scriptet giver dig mulighed for at konfigurere fortløbende integration i Visual Studio Team Services. Hvis du har konfigureret et Team Services-projekt, du har en URL-adresse: https://yourAccountName.visualstudio.com. Angiv den fulde URL, når du bliver spurgt. Du kan give det et nyt eller eksisterende navn for et Team Services-projekt.

## <a name="set-up-build-and-test-definitions-in-visual-studio-team-services"></a>Konfigurere build og teste definitioner i Visual Studio Team Services

Vi bruger Team Services på dette projekt hovedsageligt til dens build og teste funktioner. Men det understøtter også fremragende samarbejde, som Opgavestyring med kanban-områder, kode Gennemse integreret med opgaver og versionsstyring, og gated opbygger. Det nemt at integrere med andre funktioner som GitHub, Xamarin, HockeyApp og naturligvis Visual Studio. Du kan få adgang til det, via brugergrænsefladen på internettet eller via Visual Studio, der er mere praktisk på en hvilken som helst tidspunkt.

Trinnene i build og frigivelse definitioner bruge en lang række plug-in'en tjenester, der er tilgængelige i Team Services [Marketplace](https://marketplace.visualstudio.com/VSTS). Ud over grundlæggende funktioner til at køre kommandoen linjer eller kopiere filer, der ikke er tjenester, der kalde builds ved Xamarin, Android og andre leverandører og, oprette forbindelse til HockeyApp.

![Opbygge indstillinger i Team Services](./media/iot-solution-build-system/image5.png)

### <a name="build-definitions"></a>Opbygge definitioner

Vi har build definitioner for hvert af de vigtigste mål. Vi har også variationer til funktion og den simple lineære regression test. Der giver os:

-   MyDriving.Services (back end-web app til mobilappen)

-   MyDriving.Xamarin.Android

    -   MyDriving.Xamarin.Android-funktion

    -   MyDriving.Xamarin.Android Regression

-   MyDriving.Xamarin.iOS

    -   MyDriving.Xamarin.iOS-funktion

    -   MyDriving.Xamarin.iOS Regression

-   MyDriving.Xamarin.UWP

    -   MyDriving.Xamarin.UWP-funktion

    -   MyDriving.Xamarin.UWP Regression

Hvis du vil se alle detaljer for vores konfiguration, skal du i afsnittet 4.7 i [MyDriving oversigtsvejledning](http://aka.ms/mydrivingdocs)"Build og Release konfiguration." De følger det samme generelle mønster. Script:

1.  Gendanner pakken NuGet. Vi placere ikke kompilerede kode i lageret, så de første trin for hver build er at gendanne de nødvendige NuGet pakker.

2.  Aktiverer licensen. Opret udføres i skyen, så hvor vi har brug for en licens – især for tjenesten Xamarin build – vi har til at aktivere vores licens på den aktuelle build-computer. Derefter deaktivere vi den umiddelbart bagefter for at tillade, at det skal bruges på en anden computer.

3.  Opbygger ved hjælp af tjenesten relevante. Vi bruger Xamarin builds til apps til mobil og Visual Studio opbygger til back end-webtjenesten.

4.  Opbygger test.

5.  Kører test. Vi Kør mobilapp test i Xamarin Test skyen.

6.  Udgiver build resultatet til destinationen.

Udløser til de primære builds er angivet til fortløbende integration. Opret kører det vil sige, hver gang kode er markeret i til den overordnede gren.

![Brugergrænseflade, hvor udløseren er angivet til fortløbende integration](./media/iot-solution-build-system/image6.png)

### <a name="release-definitions"></a>Slip definitioner

Release definitioner er konfigureret på næsten samme måde.

For webtjenesten konfigurere vi installation som en Azure WebApp:

![Grænsefladen til at konfigurere installation som en Azure WebApp](./media/iot-solution-build-system/image7.png)

Og vi release udløser fortløbende installation. Det vil sige, hver indtjekning efterfulgt af et fuldført build medfører en opdatering til WebApp.

![Brugergrænsefladen for at release udløser til at fortløbende installation](./media/iot-solution-build-system/image8.png)

For mobilapps Installer vi HockeyApp:

![Grænseflade til implementering af en mobilapp til HockeyApp](./media/iot-solution-build-system/image9.png)

## <a name="explore-telemetry-by-using-application-insights"></a>Udforske telemetri ved hjælp af programmet indsigt

[Programmet indsigt](../application-insights/app-insights-overview.md) indsamler telemetri om den ydeevne og brugen af dine webtjenester. Programmet indsigt SDK sender telemetri fra tjenesten til ressourcen programmet indsigt i Azure.

Gå til programmet indsigt ressourcen, som skabelonen, der er konfigureret. Der, kan du undersøge diagrammer af ydeevnen for dit [Mobile App Service project](https://github.com/Azure-Samples/MyDriving/tree/master/src/MobileAppService). De vises server mødeindkaldelser og svar gange, fejl, og undtagelse tæller. Der er også diagrammer afhængighed svar gange – det vil sige, opkald til databasen og at REST API'er som Machine Learning. Hvis der er en hvilken som helst ydelsesproblemer, vil du kunne se, hvilken del af dit system forårsager dem.

![Ydeevnen diagrameksemplet](./media/iot-solution-build-system/image11.png)

Hvis du har en webtjeneste, du opretter manuelt, er det nemt at få de samme diagrammer. Klik på **Funktioner**på bladet web service > **filtypenavne** > **Tilføj**. Vælg **programmet indsigt**.

![Grænsefladen til valg af program indsigt at få diagrammerne](./media/iot-solution-build-system/image12.png)

Funktionen virker ved instrumentering dit program med programmet indsigt SDK.

Du kan tilføje brugerdefinerede telemetri (eller instrument et program, der kører et sted uden for Azure) ved [at tilføje programmet indsigt SDK](../application-insights/app-insights-asp-net.md) på udviklingstid. Dette er nyttigt til log metrisk, der afhænger af programmet, som brugernes gennemsnitlige forretningsrejse længde eller samlede kørsel. Højreklik på projektet i Visual Studio, og vælg derefter **Tilføj programmet indsigt**.

![Grænsefladen til at vælge Tilføj programmet indsigt tilføje brugerdefinerede telemetri](./media/iot-solution-build-system/image10.png)

Programmet indsigt send beskeder om mails, hvis det registrerer usædvanlige antal fejl ved svar. Du kan også oprette dine egne beskeder på forskellige former for statistik, som får svar.

Lige for at sikre dig, at din webtjeneste er altid op og kører, kan du konfigurere [tilgængelighed test](../application-insights/app-insights-monitor-web-app-availability.md). Disse test pinge webstedet fra forskellige steder over hele verden hver 15 minutter. Igen, får du vist en mail, hvis der synes at være et problem.

## <a name="estimate-operational-costs"></a>Anslå funktionsdygtige omkostninger

Det er bemærkelsesværdigt billigt til at køre en app som denne lille skaleres. Mange af tjenesterne har gratis basis lag, så udvikling og mindre drift omkostninger meget lille. Og naturligvis din egen apps behøver ikke at bruge alle de funktioner, der er vist i MyDriving.

Her er et overslag over vores omkostninger i at konfigurere udvikling konfigurationen for MyDriving. Vi Bemærk også nogle alternativer, som vi har *ikke* brug. Disse oplysninger kan være nyttig, når du vurderer dine egne omkostninger.

Vi antager, at:

-   En gruppe af højst fem (plus vægt interessenter).

-   Køre til om en måned.

-   100 brugere med fire rejser dagen.

>[AZURE.NOTE] Hvis du er ny bruger af Azure, er der en [gratis konto](https://azure.microsoft.com/free/).

| **Tjenesten/komponent**  | **Noter** | **Omkostninger/måned** |
|--------|--------|----------------|
| [Visual Studio 2015 Community](https://www.visualstudio.com/products/visual-studio-community-vs) med [Xamarin](https://visualstudiogallery.msdn.microsoft.com/dcd5b7bd-48f0-4245-80b6-002d22ea6eee) <br/>På tværs af platforme Udviklingscenter miljø| Visual Studio Community. (Skal [Visual Studio Professional](https://www.visualstudio.com/vs-2015-product-editions) til [Xamarin.Forms](https://xamarin.com/forms)til at designe på tværs af platforme fra en enkelt kodebase).  | $0   |
| [Azure IoT Hub](https://azure.microsoft.com/pricing/details/iot-hub/) <br/>Tovejs dataforbindelse til enheder | 8.000 meddelelser + 0,5 KB/meddelelse gratis. | $0             |
| [Stream Analytics](https://azure.microsoft.com/pricing/details/stream-analytics/)  <br/>   Store mængder stream databehandling                                                                                                                                                              | Gebyr på $0.031 per streaming enhed i timen, mens aktiveret. Du kan vælge antallet af streaming enheder, du vil; mere skalere. | $23            |
| [Machine Learning](https://azure.microsoft.com/documentation/services/machine-learning/)<br/> Tilpasset svar                                                                                                                                                                              |  $10/plads/måned. <br/>                                                                                                                                                                                 + 3-timers forsøg \* $1 / eksperimentere time. <br/>                                                                                                                                                           + 3.5-timers API CPU \* $2 / fremstilling CPU time. <br/>                                                                                                                                                          API CPU-tid antages 5 min/dag omskoling, men dette vil stige med mere indtastede data.                   <br/>                                                                                                                                                                     + 2 min/dag vundne for at behandle 400 rejser/dag.  | 20            |
| [App Service](https://azure.microsoft.com/pricing/details/app-service/)  <br/> Vært for mobile back-end                                                                                                                                                                              | Niveau B1 – fremstilling webapps. | $56            |
| [Visual Studio Team Services](https://azure.microsoft.com/pricing/details/visual-studio-team-services/)  <br/> Opbygge enhedstest og versionsstyring; Opgavestyring | Privat supportmedarbejdere fem brugere.| $0             |
| [Programmet indsigt](https://azure.microsoft.com/pricing/details/application-insights/) <br/>Overvågning af ydeevnen for og brugen af webtjenester og websteder| Gratis niveau.  | $0             |
| [HockeyApp](http://hockeyapp.net/pricing/) <br/> Fordeling af beta apps samt indsamling af feedback, brugen og nedbrud data                                                                                                                                      | To gratis apps for nye brugere.<br/> 30/ måned derefter.  | $0    |
| [Xamarin](https://store.xamarin.com/)<br/> Kode på en ensartet platform til flere enheder | Gratis prøveversion. <br/>25/ måned derefter.| $0    |
| [SQL-Database](https://azure.microsoft.com/pricing/details/sql-database/) til Azure-App| Grundlæggende niveau; enkelt databasemodel. | $5             |
| [Tjenesten struktur](https://azure.microsoft.com/pricing/details/service-fabric/) (valgfrit)  | Køre en lokal klynge. | $0             |
| [Power BI](https://powerbi.microsoft.com/pricing/)<br/> Alsidige viser og behandling af streamet og statiske data| Gratis niveau: 1 GB, 10.000 rækker/time, daglig opdatering. <br/> $10/brugernavn/måned for [højere begrænsninger](https://powerbi.microsoft.com/documentation/powerbi-power-bi-pro-content-what-is-it/), flere indstillinger til forbindelse, samarbejde.    | $0             |
| [Lagerplads](https://azure.microsoft.com/pricing/details/storage/)   | L (lokalt overflødige) &lt; 100 G $0.024/GB.  | $3             |
| [Data Factory](https://azure.microsoft.com/pricing/details/data-factory/)                                                                                                                       | 0.60 per aktivitet \* (8-5 FOC).| $2             |
| [HDInsight](https://azure.microsoft.com/pricing/details/hdinsight/) <br/>  Efter behov klynge i daglig omskoling   | Tre A3 noder på $0.32/ time for 1 time dagligt * 31 dage. | 30            |
| [Begivenhed Hubs](https://azure.microsoft.com/pricing/details/event-hubs/)  | Grundlæggende med $11/ måned overførselshastighed enhed + $0.028 vandindtrængen. | $11            |
| OBD dongle  || $12            |
| **Total**|    | **$157**       |

Du kan finde flere oplysninger i:

-   Oversigt over [Azure service kvoter og -begrænsninger](../azure-subscription-service-limits.md#iot-hub-limits)

-   Azure [priser Lommeregner](https://azure.microsoft.com/pricing/calculator/)

## <a name="send-us-your-feedback"></a>Send os din feedback

Da vi oprettede MyDriving for at hjælpe jumpstart dine egne IoT systemer, vil vi selvfølgelig gerne høre fra dig om, hvordan det fungerer. Fortæl os, hvis:

-  Der opstår problemer eller udfordringer.

-  Der findes et lokalnummer punkt, der gør det mere passende til scenariet.

-  Du finder en mere effektiv metode til at udføre bestemte behov.

-  Du har andre forslag til forbedring af MyDriving eller denne dokumentation.

For at give feedback, du arkiverer [problem på GitHub] eller skrive en kommentar nedenfor (da-DK edition).

Vi glæder os til at høre fra dig!

## <a name="next-steps"></a>Næste trin

Vi anbefaler [MyDriving referencevejledning over kommandoer](http://aka.ms/mydrivingdocs), som er en fyldestgørende beskrivelse af designet af systemet og dens komponenter.
