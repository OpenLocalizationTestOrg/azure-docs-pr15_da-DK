<properties
    pageTitle="Bruge lagerplads Analytics til at indsamle data logfiler og målepunkter | Microsoft Azure"
    description="Lagerplads Analytics gør det muligt at spore målepunkter data for alle lagerplads tjenester, og at indsamle logge til lagring af Blob, kø og tabel."
    services="storage"
    documentationCenter=""
    authors="robinsh"
    manager="carmonm"
    editor="tysonn"/>

<tags
    ms.service="storage"
    ms.workload="storage"
    ms.tgt_pltfrm="na"
    ms.devlang="dotnet"
    ms.topic="article"
    ms.date="08/03/2016"
    ms.author="robinsh"/>

# <a name="storage-analytics"></a>Lagerplads Analytics

## <a name="overview"></a>Oversigt

Azure-lager Analytics udfører logføring og giver målepunkter data til en lagerplads konto. Du kan bruge disse data til at spore anmodninger, til at analysere tendenser brugen og diagnosticere problemer med kontoen lagerplads.

Hvis du vil bruge lagerplads Analytics, skal du aktivere den individuelt for hver tjeneste, du vil overvåge. Du kan aktivere den fra [Azure-portalen](https://portal.azure.com). Yderligere oplysninger finder du [overvåge en lagerplads konto i portalen Azure](storage-monitor-storage-account.md). Du kan også aktivere lagerplads analyser ved hjælp af programmering via REST-API eller biblioteket klient. Bruge handlingerne [Hent Blob Service egenskaber](https://msdn.microsoft.com/library/hh452239.aspx), [Hent kø Service egenskaber](https://msdn.microsoft.com/library/hh452243.aspx), [Få egenskaber for tabel tjenesten](https://msdn.microsoft.com/library/hh452238.aspx)og [Få egenskaber for tjenesten](https://msdn.microsoft.com/library/mt427369.aspx) til at aktivere lagerplads Analytics for hver tjeneste.

De aggregerede data er gemt i en velkendt blob (for logføring) og velkendte tabeller (for statistik), som kan åbnes ved hjælp af Blob-tjenesten og tabel service API'er.

Lagerplads Analytics har en 20 TB grænse på mængden af lagrede data, som er uafhængig af det samlede loft for kontoen lagerplads. Du kan finde flere oplysninger om fakturering og data opbevaringspolitikker se [lagerplads analyser og fakturering](https://msdn.microsoft.com/library/hh360997.aspx). Se [Azure lagerplads skalerbarhed og ydeevne destinationer](storage-scalability-targets.md)kan finde flere oplysninger om lagergrænser for konto.

Du kan finde en dybdegående guide på ved hjælp af lagerplads analyser og andre værktøjer til at identificere, diagnosticere og foretage fejlfinding af Azure-lager-relaterede problemer, [skærm, diagnosticere, og foretage fejlfinding af Microsoft Azure-lager](storage-monitoring-diagnosing-troubleshooting.md).


## <a name="about-storage-analytics-logging"></a>Om logføring af lagerplads Analytics

Lagerplads Analytics logfører detaljerede oplysninger om vellykkede og mislykkede anmodninger om en lagringstjeneste. Disse oplysninger kan bruges til at overvåge individuelle anmodninger om og diagnosticere problemer med en lagringstjeneste. Anmodninger om er logget på grundlag bedste evne.

Logposter oprettes kun, hvis der er lagerplads tjenestens aktivitet. Eksempelvis hvis en lagerplads firma har aktivitet i dens Blob-tjeneste, men ikke i tabel eller kø services, oprettes kun logfiler for gruppepolitikken tjenesten Blob.

Lagerplads Analytics logføring er ikke tilgængelig for tjenesten Azure-fil.

### <a name="logging-authenticated-requests"></a>Logføring af godkendte anmodninger

Følgende typer af godkendte anmodninger logføres:

- Vellykket anmodninger.

- Det lykkedes ikke anmodninger, herunder timeout, throttling, netværk, autorisation og andre fejl.

- Anmodninger ved hjælp af en delt Access signatur (SAS), herunder mislykkedes og vellykkede anmodninger.

- Anmodninger om at analytics-data.

Anmodninger fra lagerplads Analytics sig selv som log oprettelse eller sletning, er ikke logget. En komplet liste over de registrerede data er beskrevet i emnerne [lagerplads Analytics logget handlinger og statusmeddelelser](https://msdn.microsoft.com/library/hh343260.aspx) og [Lagerplads Analytics Log Format](https://msdn.microsoft.com/library/hh343259.aspx) .

### <a name="logging-anonymous-requests"></a>Logføring anonyme anmodninger
Følgende typer anonyme anmodninger logføres:

- Vellykket anmodninger.

- Serverfejl.

- Timeoutfejl for både klienten og serveren.

- Mislykkede GET-anmodninger med fejlkode 304 (ikke ændret).

Alle andre mislykkedes anonyme anmodninger er ikke logget. En komplet liste over de registrerede data er beskrevet i emnerne [lagerplads Analytics logget handlinger og statusmeddelelser](https://msdn.microsoft.com/library/hh343260.aspx) og [Lagerplads Analytics Log Format](https://msdn.microsoft.com/library/hh343259.aspx) .

### <a name="how-logs-are-stored"></a>Hvordan logfiler er gemt
Alle logfiler er gemt i Bloker BLOB i en objektbeholder med navnet $logs, som oprettes automatisk, når lagerplads Analytics er aktiveret for en lagerplads konto. Objektbeholderen $logs er placeret i blob navneområdet for kontoen lagerplads, for eksempel: `http://<accountname>.blob.core.windows.net/$logs`. Denne objektbeholder kan ikke slettes når lagerplads Analytics er blevet aktiveret, selvom indholdet kan slettes.

>[Azure.NOTE] Objektbeholderen $logs vises ikke, når der udføres en objektbeholder, listen over handling, som metoden [ListContainers](https://msdn.microsoft.com/library/azure/dd179352.aspx) . Det skal være direkte adgang til. For eksempel kan du bruge metoden [ListBlobs](https://msdn.microsoft.com/library/azure/dd135734.aspx) at få adgang til BLOB i den `$logs` beholder.
Som er logget anmodninger, overføre lagerplads Analytics mellemliggende resultater som dokumentkomponenter. Med jævne mellemrum, bliver lagerplads Analytics Udfør disse dokumentkomponenter og gøre dem tilgængelige som en blob.

Dublerede poster kan findes for logfiler, der er oprettet i den samme time. Du kan bestemme, om en post er en dublet ved at markere **RequestId** og **handlingen** tal.

### <a name="log-naming-conventions"></a>Log navngivningskonventioner
Hver log skrives i følgende format.

    <service-name>/YYYY/MM/DD/hhmm/<counter>.log

I følgende tabel beskrives de enkelte attributter i lognavnet.

| Attribut         | Beskrivelse                                                                                                                                                                                   |
|----------------   |--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------   |
| < navnet tjenesten >    | Navnet på tjenesten storage. For eksempel: blob, en tabel eller kø.                                                                                                                          |
| ÅÅÅÅ              | 4-cifret år for logfilen. For eksempel: 2011.                                                                                                                                           |
| MM                | Tocifret måneden for logfilen. For eksempel: 07.                                                                                                                                             |
| DD                | Tocifret måneden for logfilen. For eksempel: 07.                                                                                                                                             |
| HH                | Tocifret timen, der angiver den første time for at loggene i 24-timers UTC-format. For eksempel: 18.                                                                                     |
| mm                | Tocifret tal, der angiver det første minut for logfiler. Denne værdi er ikke understøttet i den aktuelle version af lagerplads analyser og dens værdi være altid 00.     |
| <counter>         | En baseret på nul tæller med seks cifre, der angiver antallet af log BLOB genereres for tjenesten storage i en time tidsperiode. Tælleren starter 000000. For eksempel: 000001.     |

Følgende er en komplet eksempel lognavnet, der kombinerer de tidligere eksempler.

    blob/2011/07/31/1800/000001.log

Følgende er en stikprøve URI, der kan bruges til at få adgang til den forrige log.

    https://<accountname>.blob.core.windows.net/$logs/blob/2011/07/31/1800/000001.log

Når en anmodning om lagerplads er logget, svarer resulterende lognavnet til den time, når den ønskede handling fuldført. Eksempelvis hvis en anmodning om GetBlob blev afsluttet kl 6:30. på 7 31-2011 skrives loggen sammen med præfikset følgende:`blob/2011/07/31/1800/`

### <a name="log-metadata"></a>Logge af metadata
Alle log BLOB er gemt med metadata, der kan bruges til at identificere, hvilke logføring data, der indeholder blob. I følgende tabel beskrives de enkelte metadata attributter.

| Attribut     | Beskrivelse                                                                                                                                                                                                                                                   |
|------------   |-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------    |
| LogType       | I denne artikel beskrives om loggen indeholder oplysninger om at læse, skrive eller slette handlinger. Denne værdi kan indeholde én type eller en kombination af alle tre, adskilt af kommaer. Eksempel 1: skriveadgang. Eksempel 2: Læs, Skriv; Eksempel 3: læse, skrive, slette.    |
| Starttidspunkt     | Den tidligste tid på en post i loggen, i form af åååå-MM-DDThh:mm:ssZ. For eksempel: 2011-07-31T18:21:46Z.                                                                                                                                             |
| Sluttidspunkt       | Det seneste tidspunkt på en post på loggen, i form af åååå-MM-DDThh:mm:ssZ. For eksempel: 2011-07-31T18:22:09Z.                                                                                                                                               |
| LogVersion    | Versionen af logformatet. Den eneste understøttede værdi er i øjeblikket 1.0.                                                                                                                                                                                     |

Følgende liste viser hele eksemplet metadata ved hjælp af de tidligere eksempler.

- LogType = Skriv

- Starttidspunkt = 2011-07-31T18:21:46Z

- Sluttidspunkt = 2011-07-31T18:22:09Z

- LogVersion = 1.0

### <a name="accessing-logging-data"></a>Få adgang til logføring af data

Alle data i den `$logs` objektbeholder kan åbnes ved hjælp af tjenesten Blob API'er, herunder de .NET-API'er, der leveres af Azure administrerede bibliotek. Oprette kontoadministrator lagerplads kan læse og slette logfiler, men kan ikke eller opdatere dem. Både den log metadata og lognavnet kan bruges, når forespørgsler til en logfil. Det er muligt for en given time logfiler, der skal vises uden for rækkefølgen, men metadata, der angiver altid timespan af logposter i en logfil. Derfor kan du bruge en kombination af log navne og metadata, når du søger efter en bestemt logfil.

## <a name="about-storage-analytics-metrics"></a>Om lagerplads Analytics statistik

Lagerplads Analytics kan gemme målepunkter, der indeholder aggregeret transaktion statistik og kapacitet data om anmodninger om til en lagringstjeneste. Transaktioner rapporteres på begge API handlingen niveau og på niveauet for lagerplads tjenesten, og kapacitet rapporteres på tjenesten lagerpladsen. Målepunkter data kan bruges til at analysere service lagerforbrug, diagnosticere problemer med anmodninger mod tjenesten storage samt til at forbedre ydeevnen for programmer, der bruger en tjeneste.

Hvis du vil bruge lagerplads Analytics, skal du aktivere den individuelt for hver tjeneste, du vil overvåge. Du kan aktivere den fra [Azure-portalen](https://portal.azure.com). Yderligere oplysninger finder du [overvåge en lagerplads konto i portalen Azure](storage-monitor-storage-account.md). Du kan også aktivere lagerplads analyser ved hjælp af programmering via REST-API eller biblioteket klient. Bruge handlingerne **Hent Service egenskaber** til at aktivere lagerplads Analytics for hver tjeneste.

### <a name="transaction-metrics"></a>Transaktions målepunkter

Et solidt sæt data er registreret med hver time eller minut intervaller for hver lagringstjeneste og anmodet om API handling, herunder vandindtrængen/udgangspunkt, tilgængelighed, fejl, og kategoriseret anmodning procent. Du kan se en komplet liste over transaktionsdetaljer i emnet [Lagerplads Analytics målepunkter tabel skemaet](https://msdn.microsoft.com/library/hh343264.aspx) .

Transaktionsdata er registreret på to niveauer – serviceniveau og API handlingen niveau. På niveauet for tjenesten ønskede statistik summering af alle API handlinger er skrevet til en tabel-enhed hver time selvom ingen anmodninger om der er foretaget på tjenesten. På niveauet API handlingen skrives statistik kun til et objekt Hvis handlingen blev anmodet om inden for timen.

Hvis du udfører en handling af **GetBlob** på din Blob-tjeneste, der Analytics Lagringsmål eksempelvis log anmodningen og medtage dem i de aggregerede data for både tjenesten Blob samt handlingen **GetBlob** . Men hvis du nogen **GetBlob** handling under timen, et objekt ikke skrives til den `$MetricsTransactionsBlob` for den pågældende operation.

Transaktions målepunkter er registreret til både anmodninger og anmodninger fra lagerplads Analytics selve. For eksempel registreres anmodninger fra lagerplads Analytics til at skrive logfiler og tabel enheder. Du kan finde flere oplysninger om, hvordan disse anmodninger faktureres se [lagerplads analyser og fakturering](https://msdn.microsoft.com/library/hh360997.aspx).

### <a name="capacity-metrics"></a>Kapacitet målepunkter

>[AZURE.NOTE] Kapacitet målepunkter er på nuværende tidspunkt kun tilgængelige for tjenesten Blob. Kapacitet måleredskaber for tjenesten tabel og tjenesten kø vil være tilgængelige i fremtidige versioner af lagerplads analyser.

Kapacitet data er registreret dagligt til en lagerplads konto Blob-tjenesten, og to tabel objekter er skrevet. Ét objekt indeholder statistik for brugerdata, og den anden indeholder statistik om den `$logs` blob objektbeholder bruges af lagerplads analyser. Den `$MetricsCapacityBlob` tabel indeholder følgende statistikker:

- **Kapacitet**: mængden lagerplads, der anvendes af lagerplads firmaets Blob service, i byte.

- **ContainerCount**: antallet blob beholdere i lagerplads firmaets Blob-tjeneste.

- **ObjectCount**: antallet anvendt og tilbageskrivningshandlinger Bloker eller siden BLOB i lagerplads firmaets Blob-tjeneste.

Se [Lagerplads Analytics målepunkter tabelskema](https://msdn.microsoft.com/library/hh343264.aspx)kan finde flere oplysninger om kapacitet målepunkter.

### <a name="how-metrics-are-stored"></a>Hvordan målepunkter er gemt

Alle målepunkter data for hver af lagerplads tjenester er gemt i tre tabeller reserveret for den pågældende tjeneste: én tabel for oplysninger om én tabel til minut oplysninger og en anden tabel for oplysninger om kapacitet. Oplysninger om posteringen og minut består af data og svar, og oplysninger om kapacitet består af data om brug af lagerplads. Målepunkter time, minut målepunkter og kapacitet til en lagerplads konto Blob-tjenesten kan åbnes i tabeller, der er navngivet som beskrevet i følgende tabel.

| Målepunkter niveau                         | Tabelnavne                                                                                                                   | Understøttede versioner                                                                                                                        |
|------------------------------------   |-----------------------------------------------------------------------------------------------------------------------------  |---------------------------------------------------------------------------------------------------------------------------------------------- |
| Hver time statistik, primære placering      |  $MetricsTransactionsBlob <br/>$MetricsTransactionsTable <br/> $MetricsTransactionsQueue                                                  | Versioner før 2013-08-15 kun. Selvom disse navne er stadig understøttes, anbefales det, at du skifte til ved hjælp af nedenstående tabeller.  |
| Hver time statistik, primære placering      | $MetricsHourPrimaryTransactionsBlob <br/>$MetricsHourPrimaryTransactionsTable <br/>$MetricsHourPrimaryTransactionsQueue               | Alle versioner, herunder 2013-08-15.                                                                                                               |
| Minut statistik, primære placering      | $MetricsMinutePrimaryTransactionsBlob <br/>$MetricsMinutePrimaryTransactionsTable <br/>$MetricsMinutePrimaryTransactionsQueue         | Alle versioner, herunder 2013-08-15.                                                                                                               |
| Hver time statistik, sekundær placering    | $MetricsHourSecondaryTransactionsBlob <br/>$MetricsHourSecondaryTransactionsTable <br/>$MetricsHourSecondaryTransactionsQueue         | Alle versioner, herunder 2013-08-15. Læseadgang geografisk overflødige gentagelse skal være aktiveret.                                                    |
| Minut statistik, sekundær placering    | $MetricsMinuteSecondaryTransactionsBlob <br/>$MetricsMinuteSecondaryTransactionsTable <br/>$MetricsMinuteSecondaryTransactionsQueue   | Alle versioner, herunder 2013-08-15. Læseadgang geografisk overflødige gentagelse skal være aktiveret.                                                    |
| Kapacitet (kun Blob service)          | $MetricsCapacityBlob                                                                                                          | Alle versioner, herunder 2013-08-15.                                                                                                               |


Disse tabeller oprettes automatisk, når lagerplads Analytics er aktiveret for en lagerplads konto. Får adgang til via navneområdet for kontoen lagerplads, f.eks.:`https://<accountname>.table.core.windows.net/Tables("$MetricsTransactionsBlob")`

### <a name="accessing-metrics-data"></a>Få adgang til målepunkter data

Alle data i tabellerne målepunkter kan åbnes ved hjælp af tjenesten tabel API'er, herunder de .NET-API'er, der leveres af Azure administrerede bibliotek. Oprette kontoadministrator lagerplads kan læse og slette tabel enheder, men kan ikke eller opdatere dem.

## <a name="billing-for-storage-analytics"></a>Fakturering for lagerplads analyser

Lagerplads Analytics er aktiveret af en lagerplads kontoejeren; det er ikke aktiveret som standard. Alle dataene på målepunkter er skrevet af tjenesterne for en lagerplads konto. Som et resultat er hver skrivehandling, der er udført af lagerplads Analytics fakturerbar. Desuden er mængden lagerplads, der bruges af målepunkter data også fakturerbar.

De følgende handlinger, der udføres af lagerplads Analytics er fakturerbar:

- Anmodninger om at oprette BLOB til logføring. 

- Anmodninger om at oprette tabellen enheder på målepunkter.

Hvis du har konfigureret en opbevaringspolitik for data, er du ikke betale for Sletningstransaktioner, når lagerplads Analytics sletter gamle logføring og målepunkter data. Sletningstransaktioner fra en klient er dog fakturerbar. Se [angive en opbevaringspolitik til lagerplads Analytics Data](https://msdn.microsoft.com/library/azure/hh343263.aspx)kan finde flere oplysninger om opbevaringspolitikker.

### <a name="understanding-billable-requests"></a>Forstå fakturerbar anmodninger

Alle anmodninger om at en konto lagringstjeneste er enten fakturerbar eller ikke-fakturerbar. Lagerplads Analytics logger hver enkelt anmodning til en tjeneste, herunder en statusmeddelelse, der angiver, hvordan anmodningen blev håndteret. På samme måde, gemmer lagerplads Analytics målepunkter til både en tjeneste og handlingerne API til den pågældene tjeneste, herunder de procenter og antallet af visse statusmeddelelser. Disse funktioner kan sammen, hjælper dig med at analysere dine fakturerbar anmodninger om, at forbedre på dit program og diagnosticere problemer med anmodninger om til dine tjenester. Se [Forstå Azure lagerplads fakturering - båndbredde, transaktioner, og kapacitet](http://blogs.msdn.com/b/windowsazurestorage/archive/2010/07/09/understanding-windows-azure-storage-billing-bandwidth-transactions-and-capacity.aspx)kan finde flere oplysninger om fakturering.

Når man kigger på lagerplads Analytics-data, kan du bruge tabellerne i emnet [lagerplads Analytics logget handlinger og statusmeddelelser](https://msdn.microsoft.com/library/azure/hh343260.aspx) for at bestemme, hvilke forespørgsler er fakturerbar. Du kan derefter sammenligne dine logfiler og målepunkter data til statusmeddelelser at se, hvis du blev opkrævet betaling for en bestemt anmodning. Du kan også bruge tabeller i det forrige emne for at undersøge tilgængelighed for en lagringstjeneste eller individuelle API handling.

## <a name="next-steps"></a>Næste trin

### <a name="setting-up-storage-analytics"></a>Konfiguration af lagerplads Analytics
- [Overvåge en lagerplads konto i Azure-portalen](storage-monitor-storage-account.md)
- [Aktivere og konfigurere lagerplads Analytics](https://msdn.microsoft.com/library/hh360996.aspx)

### <a name="storage-analytics-logging"></a>Lagerplads Analytics logføring  
- [Om lagerplads Analytics logføring](https://msdn.microsoft.com/library/hh343262.aspx)
- [Lagerplads Analytics logformat](https://msdn.microsoft.com/library/hh343259.aspx)
- [Lagerplads Analytics logget handlinger og statusmeddelelser](https://msdn.microsoft.com/library/hh343260.aspx)

### <a name="storage-analytics-metrics"></a>Analytics lagringsmål
- [Om Analytics Lagringsmål](https://msdn.microsoft.com/library/hh343258.aspx)
- [Lagerplads Analytics målepunkter tabelskema](https://msdn.microsoft.com/library/hh343264.aspx)
- [Lagerplads Analytics logget handlinger og statusmeddelelser](https://msdn.microsoft.com/library/hh343260.aspx)  
