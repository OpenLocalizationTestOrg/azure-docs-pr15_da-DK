<properties 
    pageTitle="Aktivere lagringsmål i portalen Azure | Microsoft Azure" 
    description="Sådan aktiveres lagringsmål for Blob, kø, tabel og filer" 
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

# <a name="enabling-storage-metrics-and-viewing-metrics-data"></a>Aktivere lagringsmål og få vist målepunkter data

[AZURE.INCLUDE [storage-selector-portal-enable-and-view-metrics](../../includes/storage-selector-portal-enable-and-view-metrics.md)]

## <a name="overview"></a>Oversigt

Lagringsmål er som standard ikke aktiveret for din lagerplads tjenester. Du kan aktivere overvågning ved hjælp af enten den [Azure klassisk Portal](https://manage.windowsazure.com), Windows PowerShell, eller fra et program gennem en API-lager.

Når du aktiverer Lagringsmål, skal du vælge en opbevaringsperiode for dataene: denne periode bestemmer for hvor lang tid lagerplads service bevarer målepunkter og gebyrer du for afstand bedt om at gemme dem. Typisk, skal du bruge en kortere opbevaringsperiode på minut målepunkter end hver time målepunkter på grund af den nødvendige på minut målepunkter betydeligt ekstra plads. Du skal vælge en opbevaringsperiode, så du har tilstrækkelig tid til at analysere dataene og hente en hvilken som helst målepunkter, du ønsker at beholde til offline analyse eller rapportering. Husk, at du vil også blive faktureret til at hente målepunkter data fra din lagerplads konto.

## <a name="how-to-enable-storage-metrics-using-the-azure-classic-portal"></a>Sådan aktiveres lagringsmål ved hjælp af portalen Azure klassisk

[Azure klassisk Portal](https://manage.windowsazure.com), kan du bruge siden Konfigurer en lagerplads-konto til at styre Lagringsmål. For overvågning, kan du angive et niveau og en opbevaringsperiode i dage for hver af blob, tabeller og køer. I begge tilfælde er niveauet for et af følgende:

- Fra – Indsamles ingen målepunkter.

- Minimale – Lagringsmål indsamler et grundlæggende sæt målepunkter som vandindtrængen/udgangspunkt, tilgængelighed, forsinkelse og succes procenter, som skal aggregeres til Blob, tabel og kø tjenesterne.

- Detaljeret – Lagringsmål indsamler et komplet sæt mål, som indeholder den samme mål for hver lagerplads API handling ud over service niveau målepunkter. Detaljeret målepunkter aktivere nærmere analyse af problemer, der opstår under programmet handlinger.

Bemærk, at portalen Azure klassisk ikke i øjeblikket muligt at konfigurere minut målepunkter i kontoen lagerplads; Du skal aktivere minut målepunkter ved hjælp af PowerShell eller fra et program.


## <a name="how-to-enable-storage-metrics-using-powershell"></a>Sådan aktiveres lagringsmål ved hjælp af PowerShell

Du kan bruge PowerShell på din lokale computer til at konfigurere Lagringsmål i din lagerplads konto ved hjælp af Azure PowerShell-cmdlet Get-AzureStorageServiceMetricsProperty til at hente de aktuelle indstillinger, og sæt AzureStorageServiceMetricsProperty-cmdlet til at ændre de aktuelle indstillinger.

Cmdlet'erne, der bestemmer Lagringsmål Brug følgende parametre:

- MetricsType mulige værdier er timer og minutter.

- ServiceType mulige værdier er Blob, kø og tabel.

- MetricsLevel mulige værdier er ingen (svarer til deaktiveret i portalen Azure klassisk), Service (svarende til minimale i portalen Azure klassisk) og ServiceAndApi (svarende til detaljeret i portalen Azure klassisk).

For eksempel skifter følgende kommando på minut målepunkter for tjenesten blob i din lagerplads standardkonto med tilbageholdelse periode er indstillet til fem dage:

`Set-AzureStorageServiceMetricsProperty -MetricsType Minute -ServiceType Blob -MetricsLevel ServiceAndApi  -RetentionDays 5`

Følgende kommando henter de aktuelle time målepunkter niveau og opbevaring dage for tjenesten blob i din standardkonto lagerplads:

`Get-AzureStorageServiceMetricsProperty -MetricsType Hour -ServiceType Blob`

Finde oplysninger om, hvordan du konfigurerer Azure PowerShell-cmdlet'er til at fungere sammen med abonnementet Azure og hvordan du vælger lagerplads standardkontoen bruge, under: [hvordan du installerer og konfigurerer Azure PowerShell](../powershell-install-configure.md).

## <a name="how-to-enable-storage-metrics-programmatically"></a>Sådan aktiveres lagringsmål fra et program

Den følgende C# kodestykke viser, hvordan du aktiverer målepunkter og logføring for Blob-tjenesten ved hjælp af biblioteket lagerplads klient til .NET:

    //Parse the connection string for the storage account.
    const string ConnectionString = "DefaultEndpointsProtocol=https;AccountName=account-name;AccountKey=account-key";
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(ConnectionString);

    // Create service client for credentialed access to the Blob service.
    CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

    // Enable Storage Analytics logging and set retention policy to 10 days. 
    ServiceProperties properties = new ServiceProperties();
    properties.Logging.LoggingOperations = LoggingOperations.All;
    properties.Logging.RetentionDays = 10;
    properties.Logging.Version = "1.0";

    // Configure service properties for metrics. Both metrics and logging must be set at the same time.
    properties.HourMetrics.MetricsLevel = MetricsLevel.ServiceAndApi;
    properties.HourMetrics.RetentionDays = 10;
    properties.HourMetrics.Version = "1.0";

    properties.MinuteMetrics.MetricsLevel = MetricsLevel.ServiceAndApi;
    properties.MinuteMetrics.RetentionDays = 10;
    properties.MinuteMetrics.Version = "1.0";

    // Set the default service version to be used for anonymous requests.
    properties.DefaultServiceVersion = "2015-04-05";

    // Set the service properties.
    blobClient.SetServiceProperties(properties);
    
## <a name="viewing-storage-metrics"></a>Få vist lagringsmål

Når du har konfigureret Lagringsmål til at overvåge kontoen lagerplads, registrerer det målene i et sæt af velkendte tabeller i kontoen lagerplads. Du kan bruge siden skærm til kontoen lagerplads på portalen Azure klassisk til at få vist hver time målepunkter, når de bliver tilgængelige i et diagram. På denne side på portalen Azure klassisk kan du:

- Vælg hvilken metrisk afbilde på diagrammet (valget af tilgængelige målepunkter, afhænger af om du vælger detaljeret eller minimale overvågning for tjenesten på siden Konfigurer).


- Vælg tidsintervallet på målepunkter vises i diagrammet.


- Vælge at bruge en absolutte eller relative skala afbilde målene.


- Konfigurer mailbeskeder for at give dig besked, når en bestemt metrikværdi når en bestemt værdi.


Hvis du vil hente målepunkter til længerevarende opbevaring eller til at analysere dem lokalt, skal du bruge et værktøj eller skrive kode for at læse tabellerne. Du skal hente den minut målepunkter til analyse. Tabellerne vises ikke, hvis du har angivet alle tabeller i kontoen lagerplads, men du kan få adgang til dem direkte efter navn. Mange lagerplads-browsing tredjepartsværktøjer er opmærksomme på disse tabeller og giver dig mulighed at få dem vist direkte (se blogindlægget [Microsoft Azure lagerplads stifindere](http://blogs.msdn.com/b/windowsazurestorage/archive/2014/03/11/windows-azure-storage-explorers-2014.aspx) for en liste over tilgængelige værktøjer).

### <a name="hourly-metrics"></a>Hver time målepunkter
- $MetricsHourPrimaryTransactionsBlob
- $MetricsHourPrimaryTransactionsTable
- $MetricsHourPrimaryTransactionsQueue

### <a name="minute-metrics"></a>Minut målepunkter
- $MetricsMinutePrimaryTransactionsBlob
- $MetricsMinutePrimaryTransactionsTable
- $MetricsMinutePrimaryTransactionsQueue

### <a name="capacity"></a>Kapacitet
- $MetricsCapacityBlob

Du kan finde detaljerede oplysninger af skemaerne for disse tabeller på [Lagerplads Analytics målepunkter tabel skemaet](https://msdn.microsoft.com/library/azure/hh343264.aspx). De eksempel rækker under Vis kun et undersæt af de tilgængelige kolonner, men illustrerer nogle vigtige funktioner i den måde, Lagringsmål gemmer disse målepunkter:

| PartitionKey  |       RowKey       |                    Tidsstempel | TotalRequests | TotalBillableRequests | TotalIngress | TotalEgress | Tilgængelighed | AverageE2ELatency | AverageServerLatency | PercentSuccess |
|---------------|:------------------:|-----------------------------:|---------------|-----------------------|--------------|-------------|--------------|-------------------|----------------------|----------------|
| 20140522T1100 |      bruger. Alle      | 2014-05-22T11:01:16.7650250Z | 7             | 7                     | 4003         | 46801       | 100          | 104.4286          | 6.857143             | 100            |
| 20140522T1100 | bruger. QueryEntities | 2014-05-22T11:01:16.7640250Z | 5             | 5                     | 2694         | 45951       | 100          | 143.8             | 7,8                  | 100            |
| 20140522T1100 |  bruger. QueryEntity  | 2014-05-22T11:01:16.7650250Z | 1             | 1                     | 538          | 633         | 100          | 3                 | 3                    | 100            |
| 20140522T1100 | bruger. UpdateEntity  | 2014-05-22T11:01:16.7650250Z | 1             | 1                     | 771          | 217         | 100          | 9                 | 6                    | 100               |

I denne eksempeldataene minut målepunkter bruger tasten partition tiden med minut opløsning. Tasten række identificerer typen oplysninger, der er gemt i rækken, og dette består af to dele af oplysninger, typen access og anmodningstypen:

- Typen access er enten bruger eller system, hvor brugeren refererer til alle anmodninger til tjenesten storage, og system anmodninger fra lagerplads analyser.

- Anmodningstypen er enten tilfælde, hvor det er en oversigt linje, eller den identificerer bestemte API som QueryEntity eller UpdateEntity.


Eksempeldataene ovenfor viser alle poster i en enkelt minut (starter på 11:00 AM), så antallet af anmodninger om QueryEntities plus antallet QueryEntity anmoder om plus antallet af UpdateEntity anmoder om tilføje op til syv, som er totalen vises på rækken bruger: All. På samme måde, kan du drage til slut gennemsnitlig forsinkelse 104.4286 i rækken bruger: alle ved at beregne ((143.8 * 5) + 3 + 9) / 7.

Så Lagringsmål kan automatisk give dig besked om vigtige ændringer i funktionsmåden for dine tjenester uden lagerplads, bør du overveje at konfigurere beskeder i portalen Azure klassisk på siden skærm. Hvis du bruger et lager explorer til at hente disse målepunkter data i et afgrænset format, kan du bruge Microsoft Excel til at analysere dataene. Kan du se blogindlægget [Microsoft Azure lagerplads stifindere](http://blogs.msdn.com/b/windowsazurestorage/archive/2014/03/11/windows-azure-storage-explorers-2014.aspx) for en liste over tilgængelige lagerplads explorer værktøjer.



## <a name="accessing-metrics-data-programmatically"></a>Få adgang til målepunkter data fra et program

Følgende liste viser eksempel C#-kode, der har adgang til minut målene for et område af minutter og viser resultaterne i en konsol vindue. Det bruger biblioteket Azure lagerplads version 4, der indeholder klassen CloudAnalyticsClient, som forenkler adgang til målepunkter tabellerne i lagerplads.

    private static void PrintMinuteMetrics(CloudAnalyticsClient analyticsClient, DateTimeOffset startDateTime, DateTimeOffset endDateTime)
    {
    // Convert the dates to the format used in the PartitionKey
    var start = startDateTime.ToUniversalTime().ToString("yyyyMMdd'T'HHmm");
    var end = endDateTime.ToUniversalTime().ToString("yyyyMMdd'T'HHmm");
    
    var services = Enum.GetValues(typeof(StorageService));
    foreach (StorageService service in services)
    {
    Console.WriteLine("Minute Metrics for Service {0} from {1} to {2} UTC", service, start, end);
    var metricsQuery = analyticsClient.CreateMinuteMetricsQuery(service, StorageLocation.Primary);
    var t = analyticsClient.GetMinuteMetricsTable(service);
    var opContext = new OperationContext();
    var query =
    from entity in metricsQuery
    // Note, you can't filter using the entity properties Time, AccessType, or TransactionType
    // because they are calculated fields in the MetricsEntity class.
    // The PartitionKey identifies the DataTime of the metrics.
    where entity.PartitionKey.CompareTo(start) >= 0 && entity.PartitionKey.CompareTo(end) <= 0 
    select entity;
    
    // Filter on "user" transactions after fetching the metrics from Table Storage.
    // (StartsWith is not supported using LINQ with Azure table storage)
    var results = query.ToList().Where(m => m.RowKey.StartsWith("user"));
    var resultString = results.Aggregate(new StringBuilder(), (builder, metrics) => builder.AppendLine(MetricsString(metrics, opContext))).ToString();
    Console.WriteLine(resultString);
    }
    }
    
    private static string MetricsString(MetricsEntity entity, OperationContext opContext)
    {
    var entityProperties = entity.WriteEntity(opContext);
    var entityString =
    string.Format("Time: {0}, ", entity.Time) +
    string.Format("AccessType: {0}, ", entity.AccessType) +
    string.Format("TransactionType: {0}, ", entity.TransactionType) +
    string.Join(",", entityProperties.Select(e => new KeyValuePair<string, string>(e.Key.ToString(), e.Value.PropertyAsObject.ToString())));
    return entityString;
    
    }




## <a name="what-charges-do-you-incur-when-you-enable-storage-metrics"></a>Hvilke gebyrer der påløber, når du aktiverer lagringsmål?

Skriv anmodninger om at oprette tabellen enheder på målepunkter betaler til de almindelige rente, der gælder for alle Azure-lager handlinger.

Læse- og Slet anmodninger fra en klient til målepunkter data er også fakturerbar til standard rente. Hvis du har konfigureret en opbevaringspolitik for data, er du ikke betale når Azure-lager sletter gamle målepunkter data. Hvis du sletter analytics-data kan, faktureres kontoen for Slet handlinger.

Kapaciteten bruges af tabellerne målepunkter er også fakturerbar: Du kan bruge følgende til at anslå mængden af kapacitet, der bruges til at gemme målepunkter data:

- Hvis hver time en tjeneste anvender hver API i hver tjeneste, derefter gemmes 148KB data hver time i tabellerne målepunkter transaktion Hvis du har aktiveret både service og API niveau oversigt.

- Hvis hver time en tjeneste anvender hver API i hver tjeneste, derefter gemmes 12KB data hver time i tabellerne målepunkter transaktion Hvis du har aktiveret lige serviceniveau oversigt.

- Tabellen kapacitet for blob har to rækker, der tilføjes hver dag (Hvis brugeren har valgt at være i for loggene): Dette betyder, at hver dag størrelsen på tabellen øger med op til cirka 300 byte.

## <a name="next-steps"></a>Næste trin:
[Aktivere lagerplads Analytics logføring og få adgang til logdata](https://msdn.microsoft.com/library/dn782840.aspx)
 