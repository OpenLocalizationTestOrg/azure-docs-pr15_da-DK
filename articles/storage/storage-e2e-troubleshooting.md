<properties
    pageTitle="Til en komplet fejlfinding ved hjælp af Azure Lagringsmål og logføring, AzCopy og meddelelse Analyzer | Microsoft Azure"
    description="En demonstration af til slut fejlfinding i forbindelse med Azure lagerplads Analytics, AzCopy og Microsoft meddelelse Analyzer selvstudium"
    services="storage"
    documentationCenter="dotnet"
    authors="robinsh"
    manager="carmonm"/>

<tags
    ms.service="storage"
    ms.workload="storage"
    ms.tgt_pltfrm="na"
    ms.devlang="dotnet"
    ms.topic="article"
    ms.date="08/03/2016"
    ms.author="robinsh"/>


# <a name="end-to-end-troubleshooting-using-azure-storage-metrics-and-logging-azcopy-and-message-analyzer"></a>Til slut fejlfinding ved hjælp af Azure Lagringsmål og logføring, AzCopy og analyse af meddelelse

[AZURE.INCLUDE [storage-selector-portal-e2e-troubleshooting](../../includes/storage-selector-portal-e2e-troubleshooting.md)]

## <a name="overview"></a>Oversigt

Diagnosticering og fejlfinding er en vigtige kompetence til at opbygge og understøttende klientprogrammer med Microsoft Azure-lager. På grund af af fordelt funktionsmåden i et Azure-program, kan det være mere kompleks end i traditionelle miljøer at diagnosticering og fejlfinding i forbindelse med fejl og problemer med ydeevnen.

I dette selvstudium vi viser, hvordan du identificerer klienten visse fejl, der kan påvirke ydeevnen og fejlfinding i forbindelse med disse fejl fra til slut ved hjælp af værktøjer, der leveres af Microsoft og Azure-lager, for at optimere klientprogrammet.

Dette selvstudium indeholder et praktisk udforskning af en fejlfinding til slut-scenarie. Du kan finde en dybdegående grundlæggende guide til fejlfinding i forbindelse med Azure lagerplads programmer [skærm, diagnosticere, og foretage fejlfinding af Microsoft Azure-lager](storage-monitoring-diagnosing-troubleshooting.md).

## <a name="tools-for-troubleshooting-azure-storage-applications"></a>Værktøjer til fejlfinding i forbindelse med Azure-lager-programmer

For at foretage fejlfinding af klientprogrammer ved hjælp af Microsoft Azure-lager, kan du bruge en kombination af værktøjer til at afgøre, hvornår der opstod et problem, og hvad er årsagen til problemet muligvis. Disse funktioner omfatter:

- **Azure-lager analyser**. [Azure lagerplads Analytics](http://msdn.microsoft.com/library/azure/hh343270.aspx) indeholder målepunkter og logføring til Azure-lager.
    - **Lagringsmål** sporer transaktion målepunkter og kapacitet målepunkter for kontoen lagerplads. Brug af målepunkter, kan du se hvordan dit program klarer sig efter en række forskellige mål. Se [Lagerplads Analytics målepunkter tabelskema](http://msdn.microsoft.com/library/azure/hh343264.aspx) kan finde flere oplysninger om typerne målepunkter registreres i lagerplads analyser.

    - **Lagerplads logføring** logger hver anmodning Azure-lager-tjenester til en logfil over serversiden. Loggen registrerer detaljerede data for hver anmodning, herunder handlingen er udført, status for handlingen, og ventetid oplysninger. Se [Lagerplads Analytics Log Format](http://msdn.microsoft.com/library/azure/hh343259.aspx) kan finde flere oplysninger om de data, og svar, der skrives til loggene af lagerplads analyser.

> [AZURE.NOTE] Lagerplads konti med en gentagelse af typen af Zone overflødige lagerplads (ZRS) har ikke målepunkter eller muligheden for logføring aktiveret på nuværende tidspunkt. 

- **Azure-portalen**. Du kan konfigurere målepunkter og logføring til kontoen lagerplads på [Azure-portalen](https://portal.azure.com). Du kan også få vist diagrammer og grafer, der viser, hvordan dit program klarer sig over tid og konfigurere beskeder for at give dig besked, hvis dit program udfører anderledes end forventet for en bestemt metrikværdi.

    Du kan finde oplysninger om konfiguration af overvågning på portalen Azure i [skærm en lagerplads konto i portalen Azure](storage-monitor-storage-account.md) .

- **AzCopy**. Server logfiler til Azure-lager er gemt som blob, så du kan bruge AzCopy til at kopiere log BLOB til en lokal mappe til analyse ved hjælp af Microsoft meddelelse Analyzer. Se [overføre data med AzCopy kommandolinjeparametre Utility](storage-use-azcopy.md) kan finde flere oplysninger om AzCopy.

- **Microsoft Message Analyzer**. Meddelelse Analyzer er et værktøj, der forbruger logfiler og viser logdata i et visuelt format, der gør det nemt at filtrere, søgning og gruppere logdata til nyttige sæt, du kan bruge til at analysere fejl og problemer med ydeevnen. Se [Microsoft meddelelse Analyzer operativsystem Guide](http://technet.microsoft.com/library/jj649776.aspx) kan finde flere oplysninger om meddelelsen Analyzer.

## <a name="about-the-sample-scenario"></a>Om dette eksempel-scenario

Dette selvstudium skal vil vi undersøge et scenarie, hvor Azure lagringsmål angiver en lav procent succes sats for et program, der kalder Azure-lager. Lav procent succes rente metrisk (vist som **PercentSuccess** i [Azure Portal](https://portal.azure.com) og i tabellerne målepunkter) registrerer handlinger, der udføres, men, der returnerer en HTTP-statuskode, der er større end 299. Disse handlinger registreres i logfilerne serversiden lagerplads med statussen transaktion **ClientOtherErrors**. Kan få flere oplysninger om lav procent succes metrisk under [målepunkter vise lav PercentSuccess eller analytics logposter have handlinger med transaktion status for ClientOtherErrors](storage-monitoring-diagnosing-troubleshooting.md#metrics-show-low-percent-success).

Azure lagerplads handlinger kan returnere HTTP statuskoder større end 299 som en del af deres normal funktionalitet. Men disse fejl i nogle tilfælde angiver, at du muligvis ikke kunne optimere klientprogrammet til forbedret ydeevne.

I dette scenarie skal vi overveje en lav procent succes sats skal være noget under 100%. Du kan vælge et andet metriske niveau, men efter dine behov. Vi anbefaler, i test af dit program, du opretter en oprindelig plan tolerance på din KPI'er målepunkter. For eksempel du kan finde ud af, baseret på tests, at dit program skal have en ensartet procent succes rente 90% eller 85%. Hvis dataene målepunkter viser, at programmet afviger fra det pågældende tal og derefter kan du undersøge, hvad der kan forårsage stigningen.

I vores eksempel scenario, når vi har oprettet, er procent succes rente metrisk under 100%, vil vi undersøge logfiler for at finde de fejl, koordinere til målene, og bruge dem til at finde ud af, hvad der er årsag den nederste procentvise succes rente. Vi vil se specifikt på fejl i området 400. Derefter vil vi nærmere undersøge 404 (blev ikke fundet) fejl.

### <a name="some-causes-of-400-range-errors"></a>Nogle årsager til fejl 400-område

Eksemplerne nedenfor viser et udvalg af nogle 400-området fejl for anmodninger mod Azure Blob-lager og deres mulige årsager. En af disse fejl samt fejl i området 300 og 500 interval, kan bidrage til en lav procent succes sats.

Bemærk, at listerne nedenfor langt fra fuldført. Se [Status og fejlkoder](http://msdn.microsoft.com/library/azure/dd179382.aspx) på MSDN få mere at vide om generelle Azure-lager fejl og om fejl, der er specifikke for hver af lagerplads tjenester.

**Status 404 (blev ikke fundet) kodeeksempler**

Opstår, når handlingen Læs mod en objektbeholder eller blob mislykkes, fordi den blob eller objektbeholder ikke blev fundet.

- Opstår, hvis en objektbeholder eller blob er blevet slettet af en anden klient før denne anmodning.
- Opstår, hvis du bruger et API-kald, som opretter den eller de blob efter kontrolleres, om den findes. CreateIfNotExists APIs ringe op i afsnit først at kontrollere, om eksistensen af beholder eller blob; Hvis den ikke findes, returneres en 404-fejl, og derefter en anden læg-opkald foretages skrive objektbeholder eller blob.

**Status kode 409 (konflikt)-eksempler**

- Opstår, hvis du bruger en oprette API til at oprette en ny objektbeholder eller blob, uden først kontrollerer, om, og der allerede findes en objektbeholder eller blob med dette navn.
- Opstår, hvis en objektbeholder slettes, og du forsøger at oprette en ny objektbeholder med samme navn, før sletningen er fuldført.
- Opstår, hvis du angiver en rettighed på en objektbeholder eller blob, og der findes allerede en rettighed Præsenter.

**Statuskode 412 (tilstanden mislykkedes) eksempler**

- Opstår, når betingelsen angivet af en betinget header ikke er opfyldt.
- Opstår, når det bortlease-ID på objektbeholder eller blob ikke svarer til det angivne bortlease-ID.

## <a name="generate-log-files-for-analysis"></a>Oprette logfiler til analyse

I dette selvstudium bruger vi meddelelse Analyzer til at arbejde med tre forskellige typer logfiler, selvom du kan vælge at arbejde med en af følgende:

- **Serverens logfil**, som oprettes, når du aktiverer logføring af Azure-lager. Serverens logfil indeholder data om hver operation, kaldet mod en af tjenesterne Azure-lager - blob, kø, tabel og filer. Serverens logfil angiver handlingen, som hedder og hvilke statuskode var returnerede samt andre oplysninger om anmodningen og svaret.
- **.NET klient log**, som oprettes, når du aktiverer logføring på klientsiden fra i dit .NET-program. Klient loggen indeholder detaljerede oplysninger om, hvordan klienten laver anmodningen og modtager og behandler svaret.
- **Http-logfilen til sporing af netværk**, som indsamler data på HTTP/HTTPS og svar data, herunder til handlinger mod Azure-lager. I dette selvstudium skal vi oprette netværkssporing via meddelelse Analyzer.

### <a name="configure-server-side-logging-and-metrics"></a>Konfigurere logføring på serversiden og målepunkter

Først skal skal vi konfigurere logføring af Azure-lager og statistik, så vi har data fra klientprogrammet til at analysere. Du kan konfigurere logføring og målepunkter på forskellige måder – via [Azure-portalen](https://portal.azure.com), ved hjælp af PowerShell, eller fra et program. Se [aktivere Lagringsmål og få vist målepunkter Data](http://msdn.microsoft.com/library/azure/dn782843.aspx) og [lagerplads aktivering af logføring og få adgang til Log Data](http://msdn.microsoft.com/library/azure/dn782840.aspx) på MSDN for at få oplysninger om konfiguration af logføring og målepunkter.

**Via Azure-portalen**

Hvis du vil konfigurere logføring og omfanget af din lagerplads konto ved hjælp af [Azure-portalen](https://portal.azure.com), skal du følge vejledningen på [skærmen en lagerplads konto i portalen Azure](storage-monitor-storage-account.md).

> [AZURE.NOTE] Det er ikke muligt at angive minut målepunkter ved hjælp af portalen Azure. Men, anbefales det, du konfigurere dem til at udføre dette selvstudium, og til ved at undersøge problemer med ydeevnen med dit program. Du kan angive minut målepunkter ved hjælp af PowerShell, som vist nedenfor, eller fra et program ved hjælp af biblioteket lagerplads klient.
>
> Bemærk, at portalen Azure ikke vise minut målepunkter kun hver time målepunkter.

**Via PowerShell**

For at komme i gang med PowerShell til Azure skal se, [hvordan du installerer og konfigurerer Azure PowerShell](../powershell-install-configure.md).

1. Brug cmdlet'en [Tilføj AzureAccount](http://msdn.microsoft.com/library/azure/dn722528.aspx) til at føje din Azure brugerkonto til vinduet PowerShell:

    ```
    Add-AzureAccount
    ```

2. Skriv den mailadresse og adgangskode er knyttet til din konto, i **logge på Microsoft Azure** -vinduet. Azure godkender og gemmer legitimationsoplysninger for oplysningerne, og derefter lukker vinduet.
3. Angive standardkontoen lagerplads til kontoen lagerplads du bruger til selvstudiet ved at udføre disse kommandoer i vinduet PowerShell:

    ```
    $SubscriptionName = 'Your subscription name'
    $StorageAccountName = 'yourstorageaccount'
    Set-AzureSubscription -CurrentStorageAccountName $StorageAccountName -SubscriptionName $SubscriptionName
    ```

4. Aktivere logføring af lagerplads til Blob-tjenesten:

    ```
    Set-AzureStorageServiceLoggingProperty -ServiceType Blob -LoggingOperations Read,Write,Delete -PassThru -RetentionDays 7 -Version 1.0
    ```
5. Aktivere lagringsmål til Blob-tjenesten, og Sørg for at angive **- MetricsType** til `Minute`:

    ```
    Set-AzureStorageServiceMetricsProperty -ServiceType Blob -MetricsType Minute -MetricsLevel ServiceAndApi -PassThru -RetentionDays 7 -Version 1.0
    ```

### <a name="configure-net-client-side-logging"></a>Konfigurere .NET logføring på klientsiden

For at konfigurere logføring på klientsiden til et .NET-program skal du aktivere .NET diagnosticering i programmets konfigurationsfil (web.config eller app.config). Se [klientsiden logføring med biblioteket .NET lagerplads klienten](http://msdn.microsoft.com/library/azure/dn782839.aspx) og [klientsiden logføring med Microsoft Azure lagerplads SDK til Java](http://msdn.microsoft.com/library/azure/dn782844.aspx) på MSDN for at få oplysninger.

Klientsiden loggen indeholder detaljerede oplysninger om, hvordan klienten laver anmodningen og modtager og behandler svaret.

Biblioteket lagerplads klient gemmer klientsiden logdata på den placering, der er angivet i programmets konfigurationsfil (web.config eller app.config).

### <a name="collect-a-network-trace"></a>Indsamle en netværkssporing

Du kan bruge meddelelse Analyzer til at indsamle en HTTP/HTTPS netværkssporing, mens der kører klientprogrammet. Meddelelse Analyzer bruger [Fiddler](http://www.telerik.com/fiddler) på back end. Før du indsamle netværkssporing, anbefaler vi, at du konfigurerer Fiddler for at registrere ikke-krypteret HTTPS-trafik:

1. Installere [Fiddler](http://www.telerik.com/download/fiddler).
2. Start Fiddler.
2. Vælg **Funktioner | Indstillinger for fiddler**.
3. Kontrollér, at **Registrere HTTPS tilslutter** og **Dekryptere HTTPS-trafikken** både er markeret, i dialogboksen Indstillinger, som vist nedenfor.

![Konfigurere indstillinger for Fiddler](./media/storage-e2e-troubleshooting/fiddler-options-1.png)

I dette selvstudium skal indsamling og gemme en netværkssporing først i meddelelsen Analyzer og derefter oprette en analyse session for at analysere sporet og logfiler. Sådan indsamles en netværkssporing i meddelelsen Analyzer:

1. Vælg **fil i meddelelsen Analyzer | Hurtig sporing | Ikke-krypteret HTTPS**.
2. Sporet begynder med det samme. Vælg **Stop** stoppe sporingen, så vi kan konfigurere det til sporing af lager trafik kun.
3. Vælg **Rediger** for at redigere Sporingssessionen.
4. Vælg linket **Konfigurer** til højre for **Microsoft-Pef-WebProxy** ETW udbyderen.
5. Klik på fanen **Provider** i dialogboksen **Avancerede indstillinger** .
6. Angiv din lagerplads slutpunkter, adskilt af mellemrum i feltet **Hostname Filter** . Du kan for eksempel angive slutpunkterne på følgende måde: ændre `storagesample` til navnet på din lagerplads konto:

    ```
    storagesample.blob.core.windows.net storagesample.queue.core.windows.net storagesample.table.core.windows.net
    ```

7. Lukke dialogboksen, og klik på **Genstart** for at begynde at indsamle sporingen med filteret hostname på plads, så kun Azure-lager netværkstrafik er inkluderet i sporingen.

>[AZURE.NOTE] Når du er færdig med indsamling af din netværkssporing, anbefales det, at du er kommet de indstillinger, du har ændret i Fiddler til dekryptere HTTPS-trafikken. Fravælg **Registrere HTTPS tilslutter** og **Dekryptere HTTPS-trafikken** afkrydsningsfelterne i dialogboksen Indstillinger for Fiddler.

Under [Brug af netværksfunktioner sporing](http://technet.microsoft.com/library/jj674819.aspx) på Technet for at få flere oplysninger.

## <a name="review-metrics-data-in-the-azure-portal"></a>Gennemse målepunkter data i portalen Azure

Når dit program har kørt for et stykke tid, kan du gennemgå de målepunkter diagrammer, der vises i [Azure Portal](https://portal.azure.com) for at se, hvordan din tjeneste er udført. Først skal du gå til kontoen lagerplads på portalen Azure og føje et diagram for **Succes procentdel** metrikværdi.

I portalen Azure ser du nu **Succes procentdel** i overvågning diagrammet sammen med en hvilken som helst anden metrik, du har tilføjet. I den dette scenario vi kan undersøge ud ved at analysere loggene i meddelelsen Analyzer, procent succes rente er nogenlunde under 100%.

Få mere at vide om at tilføje målepunkter i siden overvågnings se [Sådan: føje målepunkter til tabellen målepunkter](storage-monitor-storage-account.md#how-to-add-metrics-to-the-metrics-table).

> [AZURE.NOTE] Det kan tage noget tid til dine målepunkter data skal vises i portalen Azure, når du har aktiveret lagringsmål. Dette skyldes, at hver time omfanget af forrige time ikke vises i portalen Azure, indtil den aktuelle time er udløbet. Desuden vises minut målepunkter aktuelt ikke i portalen Azure. Så afhængigt af, når du aktiverer målepunkter, det kan tage op til to timer at se målepunkter data.

## <a name="use-azcopy-to-copy-server-logs-to-a-local-directory"></a>Brug AzCopy til at kopiere server logfiler til en lokal mappe

Azure-lager skriver server logdata til BLOB, mens målepunkter skrives til tabeller. Log BLOB findes i den velkendte `$logs` objektbeholder til kontoen lagerplads. Log BLOB navngives hierarkisk efter år, måned, dag og time, så du nemt kan finde området af tid, du vil undersøge. For eksempel i den `storagesample` beholderen til log BLOB til 01-02-2015 fra 8-9 am-konto er `https://storagesample.blob.core.windows.net/$logs/blob/2015/01/08/0800`. De enkelte BLOB i denne beholder er navngivet sekventielt, begynder med `000000.log`.

Du kan bruge værktøjet AzCopy kommandolinjen til at hente disse serversiden logfiler til en placering på dit valg på din lokale computer. For eksempel du kan bruge følgende kommando for at hente logfilerne for blob handlinger, der er foretaget på 2 januar 2015 til mappen `C:\Temp\Logs\Server`; erstatte `<storageaccountname>` med navnet på kontoen lager og `<storageaccountkey>` med din kontonøgle i access:

    AzCopy.exe /Source:http://<storageaccountname>.blob.core.windows.net/$logs /Dest:C:\Temp\Logs\Server /Pattern:"blob/2015/01/02" /SourceKey:<storageaccountkey> /S /V

AzCopy kan hentes på siden [Azure-overførsler](https://azure.microsoft.com/downloads/) . Få mere at vide om brug af AzCopy, kan du se [overføre data med AzCopy kommandolinjeparametre Utility](storage-use-azcopy.md).

Yderligere oplysninger om overførsel serversiden logfiler i [hente lagerplads logføring logdata](http://msdn.microsoft.com/library/azure/dn782840.aspx#DownloadingStorageLogginglogdata).

## <a name="use-microsoft-message-analyzer-to-analyze-log-data"></a>Brug Microsoft meddelelse Analyzer til at analysere logdata

Microsoft meddelelse Analyzer er et værktøj til hentning, vise og analysere protocol messaging trafik, begivenheder og andre system- eller meddelelser i fejlfinding og diagnosticeringsværktøjer scenarier. Meddelelse Analyzer også gør det muligt at indlæse, samle og analysere data fra logfilen og gemmes sporingsfiler. Se [Microsoft meddelelse Analyzer operativsystem Guide](http://technet.microsoft.com/library/jj649776.aspx)kan finde flere oplysninger om meddelelsen Analyzer.

Meddelelse Analyzer indeholder Aktiver til Azure-lager, der hjælper dig med at analysere server, klienten og netværk logfiler. I dette afsnit beskriver vi får Sådan bruges disse værktøjer til at løse problemet for lav procent succes i loggene lagerplads.

### <a name="download-and-install-message-analyzer-and-the-azure-storage-assets"></a>Hente og installere meddelelse Analyzer og Azure lagerplads Aktiver

1. Hente [Meddelelse Analyzer](http://www.microsoft.com/download/details.aspx?id=44226) fra Microsoft Download Center, og kør installationsprogrammet.
2. Start meddelelse Analyzer.
3. Vælg **Aktiv Manager**i menuen **Funktioner** . Markere **overførsler**i dialogboksen **Aktiv Manager** , og derefter filtrere på **Azure-lager**. Du får vist Azure lagerplads aktiver, som vist i billedet nedenfor.
4. Klik på **Synkroniser alle vises elementer** for at installere de Azure lagerplads aktiver. De tilgængelige Aktiver omfatter:
    - **Azure lagerplads farve regler:** Azure reglerne for lagring af farve gør det muligt at definere særlige filtre, der bruger farve, tekst og typografi til at fremhæve meddelelser, der indeholder bestemte oplysninger i en sporing.
    - **Azure lagerplads diagrammer:** Azure lagerplads diagrammer er foruddefinerede diagrammer, log grafikdata server. Bemærk, at hvis du vil bruge Azure-lager diagrammer på nuværende tidspunkt, du kan kun indlæse serverens logfil i gitteret analyse.
    - **Azure lagerplads Parsers:** Azure-lager parsers Fortolk Azure-lager klienten, server og HTTP logfiler for at få dem vist i gitteret analyse.
    - **Azure lagerplads filtre:** Azure lagerplads filtre er foruddefinerede kriterier, du kan bruge til at søge i dine data i gitteret analyse.
    - **Azure lagerplads Vis layout:** Azure lagerplads Vis layout er foruddefinerede kolonnelayout og grupperinger i gitteret analyse.
4. Genstart meddelelse Analyzer, når du har installeret aktiverne.

![Meddelelse Analyzer aktiv Manager](./media/storage-e2e-troubleshooting/mma-start-page-1.png)

> [AZURE.NOTE] Installere alle de Azure-lager Aktiver vises med henblik på dette selvstudium.

### <a name="import-your-log-files-into-message-analyzer"></a>Importere dine logfiler til analyse af meddelelse

Du kan importere alle dine gemte logfiler (serversiden, klientsiden og netværk) i en enkelt session i Microsoft meddelelse Analyzer til analyse.

1. Klik på **Ny Session**i menuen **filer** i Microsoft meddelelse Analyzer, og derefter klikke på **Tom Session**. Angiv et navn til din analyse session i dialogboksen **Ny Session** . Klik på knappen **filer** i panelet **Oplysninger om sessioner** .
1. For at indlæse netværksdata sporing genereret meddelelse Analyzer, klik på **Tilføj filer**, gå til den placering, hvor du gemte filen .matp fra sessionen web sporing, Vælg fil, .matp og klikke på **Åbn**.
1. For at indlæse dataene i serversiden logfilen, klik på **Tilføj filer**, gå til den placering, hvor du har hentet serversiden logfilerne, Vælg logfiler for det tidsrum, du vil analysere og klikke på **Åbn**. I panelet **Oplysninger om sessioner** , Angiv derefter **Konfigurationen af tekst Log** rullemenuen for hver serversiden logfil til **AzureStorageLog** til at sikre, at Microsoft meddelelse Analyzer kan parses logfilen korrekt.
1. For at indlæse dataene i klientsiden logfilen, klik på **Tilføj filer**, gå til den placering, hvor du gemte klientsiden logfilerne, Vælg de logfiler, du vil analysere og klikke på **Åbn**. I panelet **Oplysninger om sessioner** , Angiv derefter **Konfigurationen af tekst Log** rullemenuen for hver klientsiden logfil til **AzureStorageClientDotNetV4** til at sikre, at Microsoft meddelelse Analyzer kan parses logfilen korrekt.
1. Klik på **Start** i dialogboksen **Ny Session** for at indlæse og analysere dataene i logfilen. Logdata vises i gitteret meddelelse Analyzer analyse.

Billedet herunder viser en eksempel-session, der er konfigureret med en server, klienten og netværk sporingslogfiler.

![Konfigurere meddelelse Analyzer Session](./media/storage-e2e-troubleshooting/configure-mma-session-1.png)

Bemærk, at meddelelsen Analyzer indlæser logfiler i hukommelsen. Hvis du har en stor mængde logdata, vil du filtrere den for at få den bedste ydeevne fra meddelelse Analyzer.

Først skal fastlægge en tidsramme, som du er interesseret i at gennemgå og holde denne tidsramme så lille som mulig. I mange tilfælde vil du gennemgå en periode på minutter eller timer på de fleste. Importere det mindste antal logfiler, der kan opfylder dine behov.

Hvis du stadig har en stor mængde logdata, kan du vil angive en session filter til at filtrere dataene log, inden du indlæse den. Vælg knappen **bibliotek** til at vælge en foruddefineret filter i feltet **Session Filter** for eksempel Vælg **Global tid Filter I** filtrene til Azure-lager på filter på et tidsinterval. Derefter kan du redigere filterkriterierne for at angive start- og slutdatoer tidsstempel for det interval, du vil have vist. Du kan også filtrere efter en bestemt statuskode; for eksempel kan du vælge at indlæse kun logposter hvor Statuskoden er 404.

Du kan finde flere oplysninger om at importere logdata i Microsoft meddelelse Analyzer i [Hentning af meddelelsesdata](http://technet.microsoft.com/library/dn772437.aspx) på TechNet.

### <a name="use-the-client-request-id-to-correlate-log-file-data"></a>Brug klient anmodnings-ID til at koordinere logfildata fra en fil

Azure-lager klient biblioteket genererer automatisk et entydigt klient anmodnings-ID for hver anmodning. Denne værdi skrives til loggen klient, serverens logfil og netværkssporing, så du kan bruge til at koordinere data på tværs af alle tre logfiler i meddelelsen Analyzer. Se [klient anmodnings-ID](storage-monitoring-diagnosing-troubleshooting.md#client-request-id) for yderligere oplysninger om klientanmodningen-id.

I nedenstående afsnit beskrives det, hvordan du bruger layoutet forudkonfigurerede og brugerdefinerede visninger til at koordinere og gruppere data baseret på klienten anmodnings-id'et.

### <a name="select-a-view-layout-to-display-in-the-analysis-grid"></a>Vælge et layout for visning skal vises i gitteret analyse

De lagerplads Aktiver for meddelelse Analyzer omfatter Azure lagerplads Vis layout, som er forudkonfigurerede visninger, som du kan bruge til at vise dine data med nyttige grupperinger og kolonner til forskellige scenarier. Du kan også oprette brugerdefineret visning layout og gemme dem til senere brug.

Billedet herunder viser menuen **Vis Layout** , tilgængelige ved at vælge **Vis Layout** fra værktøjslinjen båndet. Vis layout til Azure-lager grupperes under noden **Azure-lager** i menuen. Du kan søge efter `Azure Storage` få vist forskellige layout kun i søgefeltet til at filtrere på Azure-lager. Du kan også vælge stjernen ud for et Vis layout for at gøre det til en favorit og få den vist øverst i menuen.

![Få vist menuen Layout](./media/storage-e2e-troubleshooting/view-layout-menu.png)

Til at begynde med, Vælg **grupperede ved ClientRequestID og modul**. Denne visning layout grupper log data fra alle tre logfiler først efter klient anmodnings-ID, derefter efter kilde logfil (eller **modul** i meddelelsen Analyzer). Du kan analysere ned i et bestemt klient anmodnings-ID med denne visning og se data fra alle tre logfiler for klientanmodningen-id.

Billedet nedenfor viser denne layoutvisning anvendt på log eksempeldata, med et undersæt af kolonner, der vises. Du kan se, til et bestemt klient anmodnings-ID, analyse gitteret viser data fra den klient log, serverens logfil og netværkssporing.

![Azure-lager Vis Layout](./media/storage-e2e-troubleshooting/view-layout-client-request-id-module.png)

>[AZURE.NOTE] Forskellige logfiler har forskellige kolonner, så når data fra flere logfiler vises i gitteret analyse, nogle kolonner er ikke må indeholde data for en bestemt række. For eksempel på billedet ovenfor viser klienten log rækker ikke data for **tidsstempel**, **TimeElapsed**, **kilde**og **Destination** kolonner, fordi disse kolonner ikke findes i klienten loggen, men der findes i netværkssporing. På samme måde, kolonnen **tidsstempel** viser tidsstempel data fra serverens logfil, men vises der ingen data for de **TimeElapsed**, **kilde**og **Destination** kolonner, som ikke er en del af serverens logfil.

Ud over at bruge Azure-lager Vis layout, kan du også definere og gemme din egen visning layout. Du kan vælge andre ønskede felter til gruppering af data og gemme grupperingen som en del af et brugerdefineret layout.

### <a name="apply-color-rules-to-the-analysis-grid"></a>Anvende farve regler til gitteret analyse

De lagerplads Aktiver også indeholde farve regler, der giver en visuel betyder, at identificere forskellige typer fejl i gitteret analyse. De foruddefinerede farve regler gælder for HTTP-fejl, så de vises kun for server log og netværk sporing.

Hvis du vil anvende farve regler, Marker de **Regler, farve** på værktøjslinjen båndet. Du får vist Azure-lager farve regler i menuen. I dette selvstudium skal du vælge **Klient fejl (statuskode mellem 400 og 499)**, som vist i billedet nedenfor.

![Azure-lager Vis Layout](./media/storage-e2e-troubleshooting/color-rules-menu.png)

Ud over at bruge Azure-lager farve regler, kan du også definere og gemme dine egne farve regler.

### <a name="group-and-filter-log-data-to-find-400-range-errors"></a>Gruppere og filtere logge data til at finde fejl 400-område

Dernæst skal vi gruppere og filtrere dataene i logfilen for at finde alle fejl i området 400.

1. Find den **statuskode** kolonne i gitteret analyse, højreklik på kolonneoverskriften, og vælg **gruppe**.
2. Næste, gruppe på kolonnen **ClientRequestId** . Du ser, at dataene i gitteret Analysis er nu organiseret efter statuskode og klienten anmodnings-id'et.
1. Vise vinduet visningsfilter værktøj, hvis den ikke allerede vises. På båndet værktøjslinjen vælge **Værktøjet Windows**derefter **Visningsfilter**.
2. Føje følgende filterkriterierne til vinduet **Visningsfilter** for at filtrere dataene i logfilen for at få vist kun 400-området fejl, og klik på **Anvend**:

        (AzureStorageLog.StatusCode >= 400 && AzureStorageLog.StatusCode <=499) || (HTTP.StatusCode >= 400 && HTTP.StatusCode <= 499)

Billedet nedenfor viser resultaterne af denne gruppering og filter. Udvide feltet **ClientRequestID** under gruppering for statuskode 409, for eksempel viser en handling, som udløste statuskoden.

![Azure-lager Vis Layout](./media/storage-e2e-troubleshooting/400-range-errors1.png)

Når du har anvendt dette filter, kan du se, at rækker fra klient logfilen er udeladt, som klienten log ikke inkluderer en **statuskode** kolonne. Hvis du vil begynde med gennemgår vi server og netværk spore logfiler til at finde 404 fejl, og derefter vi kommer tilbage til klient logfilen for at se de klienthandlinger, ført til dem.

>[AZURE.NOTE] Du kan filtrere på kolonnen **statuskode** og stadig få vist data fra alle tre logfiler, herunder klient loggen, hvis du føjer et udtryk til det filter, der indeholder logposter, hvor Statuskoden er null. Hvis du vil oprette denne filterudtryk, bruge:
>
> <code>&#42;StatusCode >= 400 or !&#42;StatusCode</code>
>
> Dette filter returnerer alle rækker fra klienten log og kun rækker fra serverens logfil og HTTP log hvor Statuskoden er større end 400. Hvis du anvender den visning layoutet grupperet efter klient anmodnings-ID og modulet, kan du søge eller rulle gennem logposter til at finde dem, hvor alle tre logfiler repræsenteres.   

### <a name="filter-log-data-to-find-404-errors"></a>Filtrere logdata til at finde 404-fejl

De lagerplads Aktiver medtage foruddefinerede filtre, som du kan bruge til at indskrænke logdata for at finde fejl eller tendenser, du leder efter. Dernæst skal vi vil anvende to foruddefinerede filtre: én, der filtrerer server og netværk spore logfiler for 404 fejl og én, der filtrerer dataene på et angivet tidsinterval.

1. Vise vinduet visningsfilter værktøj, hvis den ikke allerede vises. På båndet værktøjslinjen vælge **Værktøjet Windows**derefter **Visningsfilter**.
2. I vinduet visningsfilter Vælg **bibliotek**og søge efter `Azure Storage` filtre til at finde Azure-lager. Vælg filteret efter **404 (blev ikke fundet) meddelelser i alle logfiler**.
3. Få vist menuen **biblioteket** igen, og Find og vælg **Globalt tid Filter**.
4. Redigere tidsstempler vises i filteret til det område, du vil have vist. Dette hjælper med at begrænse dataområdet til at analysere.
5. Filteret skal ligne eksemplet nedenfor. Klik på **Anvend** for at anvende filteret til gitteret analyse.

        ((AzureStorageLog.StatusCode == 404 || HTTP.StatusCode == 404)) And
        (#Timestamp >= 2014-10-20T16:36:38 and #Timestamp <= 2014-10-20T16:36:39)

![Azure-lager Vis Layout](./media/storage-e2e-troubleshooting/404-filtered-errors1.png)

### <a name="analyze-your-log-data"></a>Analysere dine logdata

Nu, hvor du har grupperet og filtreret dine data, kan du undersøge detaljerne for individuelle anmodninger, som genererede 404 fejl. Data, der er grupperet efter klient anmodnings-ID, og derefter efter log kilde i det aktuelle layout i visningen. Da vi filtrerer på anmodninger om hvor feltet statuskode indeholder 404, ser vi kun server og netværksdata for sporing, ikke dataene i klienten logfilen.

Billedet herunder viser en bestemt anmodning, hvor en 404 resulterer i en handling af få Blob, fordi blob ikke fandtes. Bemærk, at nogle kolonner er blevet fjernet fra standardvisningen for at få vist de relevante data.

![Filtrerede Server og netværk spore logfiler](./media/storage-e2e-troubleshooting/server-filtered-404-error.png)

Derefter skal vi koordinere denne klient anmodnings-ID med klient logdataene for at se, hvilke handlinger, der er kunden finder, når fejlen er der sket. Du kan få vist en ny analyse gittervisning for denne session for at få vist de klient logdata, som åbnes i en anden fane:

1. Først skal du kopiere værdien i feltet **ClientRequestId** til Udklipsholder. Du kan gøre dette ved at markere en række, at finde feltet **ClientRequestId** , at højreklikke på værdien for data og vælge **Kopiér 'ClientRequestId'**.
1. Vælg **Nye Fremviser**på båndet værktøjslinjen og derefter vælge **Analysis gitter** for at åbne en ny fane. Den nye fane viser alle data i dine logfiler, uden at gruppere, filtrere eller farve regler.
2. Vælg **Vis Layout**på båndet værktøjslinjen og derefter vælge **Alle .NET klient kolonner** under afsnittet **Azure-lager** . Denne visning layout vises data fra klienten log samt loggene server og netværk sporing. Som standard er den sorteret på kolonnen **MessageNumber** .
3. Dernæst skal søge i klienten logfilen for klient anmodnings-id'et. Vælg **Find meddelelser**på båndet værktøjslinjen og derefter angive et brugerdefineret filter på klienten anmodnings-ID i feltet **Søg** . Brug følgende syntaks for filteret, angive dine egne klient anmodnings-ID:

        *ClientRequestId == "398bac41-7725-484b-8a69-2a9e48fc669a"

Meddelelse Analyzer finder og markerer den første post i logfilen, hvor søgekriterierne stemmer overens med klient anmodnings-id'et. Log klient er der flere poster for hver klient anmodnings-ID, så du overveje at gruppere dem i feltet **ClientRequestId** for at gøre det nemmere at se dem alle sammen. Billedet herunder viser alle meddelelser i klienten logfilen for den angivne klient anmodnings-id'et.

![Klient log viser 404-fejl](./media/storage-e2e-troubleshooting/client-log-analysis-grid1.png)

Brug af de data, der vises i visningen layout i disse to faner, kan du analysere anmodning om data for at finde ud af, hvad har forårsaget fejlen. Du kan også se anmodninger, foran denne dem for at se, hvis en tidligere begivenhed kan har ført til 404-fejl. For eksempel kan du gennemgå logposter klienten på computeren, før denne klient anmodnings-ID til at afgøre, om blob er blevet slettet, eller hvis fejlen var på grund af klientprogrammet kalder en CreateIfNotExists API på en objektbeholder eller blob. I klienten loggen, kan du finde den blob adresse i feltet **Beskrivelse** . disse oplysninger vises i den server, og netværk sporingslogger, i feltet **Oversigt** .

Når du kender adressen på den blob, der resulterer i 404-fejl, kan du undersøge yderligere. Hvis du søger logposter for andre meddelelser, der er knyttet til handlinger på den samme blob, kan du kontrollere, om klienten slettet objektet.

## <a name="analyze-other-types-of-storage-errors"></a>Analysere andre typer fejl for lager

Nu hvor du er vant til at bruge meddelelse Analyzer til at analysere logdataene, kan du analysere andre typer af fejl ved hjælp af Vis layout, regler for farve og søgning/filtrering. Nedenstående tabeller viser nogle problemer, du kan støde på, og du kan bruge til at finde dem filterkriterierne. Se [Filtrere meddelelsesdata](http://technet.microsoft.com/library/jj819365.aspx)kan finde flere oplysninger om bygning af filtre og den meddelelse Analyzer filtrering sprog.

|    For at undersøge...                                                                                               |    Brug filterudtryk...                                                                                                                                                                                                                                        |    Udtryk, der gælder for Log (klient, Server, netværk, alle)    |
|------------------------------------------------------------------------------------------------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|------------------------------------------------------------------|
|    Uventede forsinkelser i levering af meddelelser på en kø                                                              |    AzureStorageClientDotNetV4.Description indeholder "Retrying mislykkedes handling".                                                                                                                                                                                |    Klient                                                        |
|    HTTP stigning i PercentThrottlingError                                                                       |    HTTP. Response.StatusCode == 500 & #124; & #124; HTTP. Response.StatusCode == 503                                                                                                                                                                                          |    Netværk                                                       |
|    Øge i PercentTimeoutError                                                                               |    HTTP. Response.StatusCode == 500                                                                                                                                                                                                                             |    Netværk                                                       |
|    Øge i PercentTimeoutError (alle)                                                                         |    * Statuskode == 500                                                                                                                                                                                                                                          |    Alle                                                           |
|    Øge i PercentNetworkError                                                                               |    AzureStorageClientDotNetV4.EventLogEntry.Level < 2                                                                                                                                                                                                          |    Klient                                                        |
|    HTTP 403 (forbudt) meddelelser                                                                                 |    HTTP. Response.StatusCode == 403                                                                                                                                                                                                                             |    Netværk                                                       |
|    HTTP 404 (ikke fundet) meddelelser                                                                                 |    HTTP. Response.StatusCode == 404                                                                                                                                                                                                                             |    Netværk                                                       |
|    404 (alle)                                                                                                     |    * Statuskode == 404                                                                                                                                                                                                                                          |    Alle                                                           |
|    Delt Access signatur (SAS) godkendelse problem                                                             |    AzureStorageLog.RequestStatus == "SASAuthorizationError"                                                                                                                                                                                                     |    Netværk                                                       |
|    HTTP 409 (konflikt) meddelelser                                                                                  |    HTTP. Response.StatusCode == 409                                                                                                                                                                                                                             |    Netværk                                                       |
|    409 (alle)                                                                                                     |    * Statuskode == 409                                                                                                                                                                                                                                          |    Alle                                                           |
|    Lav PercentSuccess eller analytics logposter have handlinger med transaktionsstatus af ClientOtherErrors    |    AzureStorageLog.RequestStatus == "ClientOtherError"                                                                                                                                                                                                         |    Server                                                        |
|    Nagle advarsel                                                                                               |    ((AzureStorageLog.EndToEndLatencyMS-AzureStorageLog.ServerLatencyMS) > (AzureStorageLog.ServerLatencyMS * 1,5)) og (AzureStorageLog.RequestPacketSize < 1460) og (AzureStorageLog.EndToEndLatencyMS - AzureStorageLog.ServerLatencyMS > = 200)        |    Server                                                        |
|    Tidsinterval i logfilerne Server og netværk                                                                    |    #Tidsstempel > = 2014-10-20T16:36:38 og #Timestamp < = 2014-10-20T16:36:39                                                                                                                                                                                     |    Server, netværk                                               |
|    Tidsinterval i Server-logfilerne                                                                                |    AzureStorageLog.Timestamp > = 2014-10-20T16:36:38 og AzureStorageLog.Timestamp < = 2014-10-20T16:36:39                                                                                                                                                     |    Server                                                        |


## <a name="next-steps"></a>Næste trin

Du kan finde flere oplysninger om fejlfinding til slut-scenarier i Azure-lager, disse ressourcer:

- [Overvåge, diagnosticere og fejlfinding i forbindelse med Microsoft Azure-lager](storage-monitoring-diagnosing-troubleshooting.md)
- [Lagerplads Analytics](http://msdn.microsoft.com/library/azure/hh343270.aspx)
- [Overvåge en lagerplads konto i Azure-portalen](storage-monitor-storage-account.md)
- [Overføre data med AzCopy kommandolinjeparametre Utility](storage-use-azcopy.md)
- [Microsoft Message Analyzer operativsystem vejledning](http://technet.microsoft.com/library/jj649776.aspx)
