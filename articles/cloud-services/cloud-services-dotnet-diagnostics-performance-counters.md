<properties
   pageTitle="Bruge tællere i ydeevne i Azure diagnosticering | Microsoft Azure"
   description="Brug tællere i ydeevne i Azure skytjenester eller virtuelt til at finde flaskehalse og finjustere ydeevnen."
   services="cloud-services"
   documentationCenter=".net"
   authors="rboucher"
   manager="jwhit"
   editor="tysonn" />
<tags
   ms.service="cloud-services"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="02/29/2016"
   ms.author="robb" />

# <a name="create-and-use-performance-counters-in-an-azure-application"></a>Oprette og bruge tællere i ydeevne i en Azure-program

I denne artikel beskrives fordelene ved og hvordan du kan sætte tællere i ydeevne i din Azure-program. Du kan bruge dem til at indsamle data, finde flaskehalse og finjustere ydeevnen i systemet og programmet.

Tællere i ydeevne, der er tilgængelige for Windows Server, IIS og ASP.NET kan også indsamles og bruges til at bestemme tilstanden for dine Azure web roller, arbejder roller og virtuelle computere. Du kan også oprette og bruge brugerdefinerede ydeevne tællere.  

Du kan undersøge tæller ydelsesdata
1. Direkte på Programværten med værktøjet Performance Monitor adgang til dem via Fjernskrivebord
2. Med System Center Operations Manager ved hjælp af Azure Management Pack
3. Med andre værktøjer til overvågning af, få adgang til diagnosticering dataene overføres til Azure-lager. Finde flere oplysninger i [Store og Vis diagnosticeringsdata i Azure-lager](https://msdn.microsoft.com/library/azure/hh411534.aspx) .  

Du kan finde flere oplysninger om overvågning af ydeevnen for dit program i [Azure klassisk portal](http://manage.azure.com/), se, [hvordan du overvåge Cloud Services](https://www.azure.com/manage/services/cloud-services/how-to-monitor-a-cloud-service/).

Du kan finde yderligere detaljeret vejledning til oprettelse af en logføring og sporing strategi og brug af diagnosticering og andre teknikker til fejlfinding og optimere Azure-programmer, [Foretage fejlfinding af bedste fremgangsmåder til udvikling af Azure-programmer](https://msdn.microsoft.com/library/azure/hh771389.aspx).


## <a name="enable-performance-counter-monitoring"></a>Aktivere overvågning af ydeevnen tæller

Tællere i ydeevne er ikke aktiveret som standard. Dit program eller en Start opgave skal ændre konfigurationen af standard diagnosticering agent for at medtage de bestemte tællere i ydeevne, du vil overvåge for hver rolle forekomst.

### <a name="performance-counters-available-for-microsoft-azure"></a>Tilgængelig for Microsoft Azure tællere i ydeevne

Azure indeholder et undersæt af de tilgængelige tællere i ydeevne til Windows Server, IIS og ASP.NET stablen. I følgende tabel vises nogle af tællerne i ydeevne af særlig interesse for Azure-programmer.

|Tæller kategori: Objekt (forekomst)|Tællernavn      |Reference|
|---|---|---|
|.NET CLR undtagelser (_Global_)|# Exceps udløst / sek.   |Undtagelse tællere i ydeevne|
|.NET CLR hukommelse (_Global_)    |% Tid i globalt katalog            |Hukommelsesydelsestællere i ydeevne|
|ASP.NET                      |Programmet genstarter    |Ydeevnen tællere til ASP.NET|
|ASP.NET                      |Anmode om kørselstid  |Ydeevnen tællere til ASP.NET|
|ASP.NET                      |Afbrudte anmodninger   |Ydeevnen tællere til ASP.NET|
|ASP.NET                      |Arbejder proces genstarter |Ydeevnen tællere til ASP.NET|
|ASP.NET-programmer (__samlede__)|Samlet antal anmodninger        |Ydeevnen tællere til ASP.NET|
|ASP.NET-programmer (__samlede__)|Anmodninger/sek.          |Ydeevnen tællere til ASP.NET|
|ASP.NET v4.0.30319           |Anmode om kørselstid  |Ydeevnen tællere til ASP.NET|
|ASP.NET v4.0.30319           |Ventetid for anmodning       |Ydeevnen tællere til ASP.NET|
|ASP.NET v4.0.30319           |Anmodninger om aktuelle        |Ydeevnen tællere til ASP.NET|
|ASP.NET v4.0.30319           |Anmodninger i kø         |Ydeevnen tællere til ASP.NET|
|ASP.NET v4.0.30319           |Afviste anmodninger       |Ydeevnen tællere til ASP.NET|
|Hukommelse                       |Tilgængelige MB        |Hukommelsesydelsestællere i ydeevne|
|Hukommelse                       |Anvendt byte         |Hukommelsesydelsestællere i ydeevne|
|Processor(_Total)            |% Processortid        |Ydeevnen tællere til ASP.NET|
|TCPv4                        |Mislykkede forbindelser     |TCP-objekt|
|TCPv4                        |Forbindelser |TCP-objekt|
|TCPv4                        |Nulstil forbindelser       |TCP-objekt|
|TCPv4                        |Segmenter sendt/sekund       |TCP-objekt|
|Netværk Interface(*)         |Byte modtaget/sekund      |Netværk Interface objekt|
|Netværk Interface(*)         |Byte sendt/sekund          |Netværk Interface objekt|
|Netværk Interface (Microsoft Virtual Machine Bus netværk Adapter _2)|Byte modtaget/sekund|Netværk Interface objekt|
|Netværk Interface (Microsoft Virtual Machine Bus netværk Adapter _2)|Byte sendt/sekund|Netværk Interface objekt|
|Netværk Interface (Microsoft Virtual Machine Bus netværk Adapter _2)|Byte i alt/sekund|Netværk Interface objekt|

## <a name="create-and-add-custom-performance-counters-to-your-application"></a>Oprette og føje brugerdefinerede ydeevne tællere til dit program

Azure har support for at oprette og redigere brugerdefinerede ydeevne tællere for web roller og arbejder roller. Tællerne kan bruges til at spore og overvåge program-specifikke funktionsmåde. Du kan oprette og slette brugerdefinerede ydeevne tæller kategorier og angivelser fra Start opgave, web rolle eller arbejder rolle med administratorrettigheder.

>[AZURE.NOTE] Kode, der foretager ændringer i brugerdefinerede ydeevne tællere skal have øgede tilladelser til at køre. Hvis koden er i en web rolle eller arbejder rolle, rollen skal indeholde mærket <Runtime executionContext="elevated" /> i filen ServiceDefinition.csdef for rollen initialiseret korrekt.

Du kan sende brugerdefineret tæller ydelsesdata til brug af diagnosticering agent Azure-lager.

Tæller standard ydelsesdata genereres af Azure processer. Brugerdefineret tæller ydelsesdata skal være oprettet af din rolle eller arbejder rolle webprogram. Du kan se [Ydeevne tæller typer](https://msdn.microsoft.com/library/z573042h.aspx) oplysninger om typerne data, der kan være gemt i tællere i brugerdefineret ydeevne. Du kan finde et eksempel, der opretter og angiver brugerdefinerede ydeevne tællerdata i en web rolle i [PerformanceCounters eksempel](http://code.msdn.microsoft.com/azure/) .

## <a name="store-and-view-performance-counter-data"></a>Store og få vist ydeevnen tællerdata

Azure cachelagres ydeevne tællerdata med andre diagnostiske oplysninger. Disse data er tilgængelige for remote overvågning, mens den rolle forekomst kører ved hjælp af adgang til Fjernskrivebord til at få vist værktøjer som Performance-skærm. Hvis du vil bevare dataene uden for forekomsten rolle, overføre diagnosticering agent data til Azure-lager. Grænsen på tæller cachelagrede ydelsesdata kan konfigureres i diagnosticering agent, eller det kan være konfigureret til at være en del af en delt grænse for alle de data, der er diagnosticering. Du kan finde flere oplysninger om at angive størrelsen på bufferen, se [OverallQuotaInMB](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.diagnostics.diagnosticmonitorconfiguration.overallquotainmb.aspx) og [DirectoriesBufferConfiguration](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.diagnostics.directoriesbufferconfiguration.aspx). Se [Store og Vis diagnosticeringsdata i Azure-lager](https://msdn.microsoft.com/library/azure/hh411534.aspx) for en oversigt over konfiguration af diagnosticering agent til at overføre data til en lagerplads konto.

Hver konfigurerede ydeevne tæller forekomst optages med en bestemt udvalg hastighed, og de indsamlede data er overført til kontoen lagerplads, enten ved en anmodning om planlagt filoverførsel eller en anmodning om efter behov filoverførsel. Automatiske overførsler kan planlægges så ofte som en gang i minuttet. Ydelsesdata tæller overføres af diagnosticering agent er gemt i en tabel, WADPerformanceCountersTable, i kontoen lagerplads. I denne tabel kan være adgang til og forespørges med standard Azure lagerplads API metoder. Se [Microsoft Azure PerformanceCounters eksempel](http://code.msdn.microsoft.com/Windows-Azure-PerformanceCo-7d80ebf9) for et eksempel på forespørgsler og visning af ydeevnen tællerdata fra tabellen WADPerformanceCountersTable.

>[AZURE.NOTE] Afhængigt af diagnosticering agent filoverførsel hyppigheden og kø ventetid, kan den seneste ydeevne Tællerdataene i kontoen lagerplads gå nogle minutter forældet.

## <a name="enable-performance-counters-using-diagnostics-configuration-file"></a>Aktivere tællere i ydeevne ved hjælp af diagnosticering konfigurationsfil

Brug følgende fremgangsmåde til at aktivere tællere i ydeevne i Azure programmet.

## <a name="prerequisites"></a>Forudsætninger

Dette afsnittet antages, at du har importeret diagnosticering skærm til dit program og føjet konfigurationsfil diagnosticering til Visual Studio løsningen (diagnostics.wadcfg i SDK 2.4 og nedenfor eller diagnostics.wadcfgx SDK 2,5 og derover). Se trin 1 og 2 i [Aktivere diagnosticering i Azure Cloud Services og virtuelle maskiner](./cloud-services-dotnet-diagnostics.md)) kan finde flere oplysninger.

## <a name="step-1-collect-and-store-data-from-performance-counters"></a>Trin 1: Samle og gemme data fra tællere i ydeevne

Når du har tilføjet filen diagnosticering til Visual Studio-løsning kan du konfigurere indsamling og opbevaring af ydeevnen tællerdata i en Azure-program. Det gør du ved at føje tællere i ydeevne til filen diagnosticering. Diagnosticerings-data, herunder tællere i ydeevne, der først indsamles på forekomsten. Dataene bevares derefter til tabellen WADPerformanceCountersTable i tjenesten Azure-tabel, så du skal også angive kontoen lagerplads i dit program. Hvis du er ved at teste dit program lokalt i emulatoren beregne, kan du også gemme diagnosticering data lokalt i emulatoren lagerplads. Før du gemmer diagnosticering data skal du først gå til [Azure klassisk portal](http://manage.windowsazure.com/) og opretter en lagerplads konto. Den bedste fremgangsmåde er at finde kontoen lagerplads i den samme geografisk placering som dit Azure-program, for at undgå betaling eksterne båndbredde omkostninger og for at reducere ventetid.

### <a name="add-performance-counters-to-the-diagnostics-file"></a>Føje tællere i ydeevne til diagnosticering-fil

Der findes mange tællere, du kan bruge. I følgende eksempel viser flere tællere i ydeevne, som er anbefalet til web og arbejder rolle overvågning.

Åbn filen diagnosticering (diagnostics.wadcfg i SDK 2.4 og nedenfor eller diagnostics.wadcfgx i SDK 2,5 og ovenfor), og Tilføj følgende elementet DiagnosticMonitorConfiguration:

```
    <PerformanceCounters bufferQuotaInMB="0" scheduledTransferPeriod="PT30M">
       <PerformanceCounterConfiguration counterSpecifier="\Memory\Available Bytes" sampleRate="PT30S" />
       <PerformanceCounterConfiguration counterSpecifier="\Processor(_Total)\% Processor Time" sampleRate="PT30S" />

    <!-- Use the Process(w3wp) category counters in a web role -->

       <PerformanceCounterConfiguration counterSpecifier="\Process(w3wp)\% Processor Time" sampleRate="PT30S" />
       <PerformanceCounterConfiguration counterSpecifier="\Process(w3wp)\Private Bytes" sampleRate="PT30S" />
       <PerformanceCounterConfiguration counterSpecifier="\Process(w3wp)\Thread Count" sampleRate="PT30S" />

    <!-- Use the Process(WaWorkerHost) category counters in a worker role.
       <PerformanceCounterConfiguration counterSpecifier="\Process(WaWorkerHost)\% Processor Time" sampleRate="PT30S" />
       <PerformanceCounterConfiguration counterSpecifier="\Process(WaWorkerHost)\Private Bytes" sampleRate="PT30S" />
       <PerformanceCounterConfiguration counterSpecifier="\Process(WaWorkerHost)\Thread Count" sampleRate="PT30S" />
    -->

       <PerformanceCounterConfiguration counterSpecifier="\.NET CLR Interop(_Global_)\# of marshalling" sampleRate="PT30S" />
       <PerformanceCounterConfiguration counterSpecifier="\.NET CLR Loading(_Global_)\% Time Loading" sampleRate="PT30S" />
       <PerformanceCounterConfiguration counterSpecifier="\.NET CLR LocksAndThreads(_Global_)\Contention Rate / sec" sampleRate="PT30S" />
       <PerformanceCounterConfiguration counterSpecifier="\.NET CLR Memory(_Global_)\# Bytes in all Heaps" sampleRate="PT30S" />
       <PerformanceCounterConfiguration counterSpecifier="\.NET CLR Networking(_Global_)\Connections Established" sampleRate="PT30S" />
       <PerformanceCounterConfiguration counterSpecifier="\.NET CLR Remoting(_Global_)\Remote Calls/sec" sampleRate="PT30S" />
       <PerformanceCounterConfiguration counterSpecifier="\.NET CLR Jit(_Global_)\% Time in Jit" sampleRate="PT30S" />
    </PerformanceCounters>
```

Attributten bufferQuotaInMB, der angiver den maksimale mængde systemet lagring af filer, der er tilgængelige for samling datatypen (Azure logfiler, IIS-logfilerne osv.). Standard er 0. Når den tildelte lagerplads nås, slettes den ældste data, som er tilføjet nye data. Summen af alle bufferQuotaInMB egenskaberne skal være større end værdien af attributten OverallQuotaInMB. En mere detaljeret beskrivelse for at afgøre, hvor meget lagerplads vil blive krævet for indsamling af diagnosticering data, i afsnittet konfiguration WAD [fejlfinding bedste](https://msdn.microsoft.com/library/windowsazure/hh771389.aspx)fremgangsmåder til udvikling af Azure-programmer.

Attributten scheduledTransferPeriod, som angiver intervallet mellem planlagte overførsler af data, rundet op til nærmeste minuttet. I følgende eksempler er den indstillet til PT30M (30 minutter). Angive perioden overførsel til en lille værdi, som 1 minut, vil påvirke programmets ydeevne i fremstilling, men kan være nyttige til ser diagnosticering arbejde hurtigt, når du tester. Perioden planlagt overførsel skal være lille nok til at sikre, at diagnosticering data ikke overskrives på forekomsten, men stort nok, ikke påvirker det ydeevnen for dit program.

Attributten counterSpecifier angiver tælleren ydeevnen til at indsamle. Attributten sampleRate angiver den hastighed, hvormed tælleren ydeevne skal være prøver i dette tilfælde 30 sekunder.

Når du har tilføjet de tællere i ydeevne, du vil indsamle, kan du gemme dine ændringer i filen diagnosticering. Derefter skal du angive den konto, lagring, diagnosticering dataene bevares til.

### <a name="specify-the-storage-account"></a>Angive kontoen, lagerplads

Hvis du vil bevare dine diagnosticeringsoplysninger til kontoen Azure-lager, skal du angive en forbindelsesstreng i din service-konfigurationsfil (ServiceConfiguration.cscfg).

Kontoen lagerplads kan angives i filen diagnostics.wadcfgx til Azure SDK 2.5.

>[AZURE.NOTE] Disse instruktioner gælder kun for Azure SDK 2.4 og nedenfor. Kontoen lagerplads kan angives i filen diagnostics.wadcfgx til Azure SDK 2.5.

Sådan opretter du forbindelse strengene:

1. Åbn filen ServiceConfiguration.Cloud.cscfg ved hjælp af din foretrukne teksteditor, og Angiv forbindelsesstrengen for din lagerplads. *Kontonavn* og *AccountKey* værdier findes i Azure klassisk portalen i dashboardet lagerplads konto under Administrer nøgler.

    ```
    <ConfigurationSettings>
       <Setting name="Microsoft.WindowsAzure.Plugins.Diagnostics.ConnectionString" value="DefaultEndpointsProtocol=https;AccountName=<name>;AccountKey=<key>"/>
    </ConfigurationSettings>
    ```
2. Gem filen ServiceConfiguration.Cloud.cscfg.

3. Åbn filen ServiceConfiguration.Local.cscfg og kontrollere, at UseDevelopmentStorage er indstillet til sand.

    ```
    <ConfigurationSettings>
      <Settingname="Microsoft.WindowsAzure.Plugins.Diagnostics.ConnectionString" value="UseDevelopmentStorage=true"/>
    </ConfigurationSettings>
    ```
Nu hvor forbindelse strengene er konfigureret, bevares dit program diagnosticering data til din lagerplads konto, når dit program er installeret.
4. Gemme og opbygge dit projekt og derefter installere programmet.

## <a name="step-2-optional-create-custom-performance-counters"></a>Trin 2: (Valgfrit) Opret brugerdefineret ydeevne tællere

Ud over tællerne i foruddefinerede ydeevne, kan du tilføje din egen brugerdefinerede performance-tællere til at overvåge web eller arbejder roller. Brugerdefineret ydeevne tællere kan bruges til at registrere og overvåge programmet specifikke opførsel og kan oprettes eller slettes i en Start opgave, web rolle eller arbejder rolle med administratorrettigheder.

Azure diagnosticering agent opdaterer ydeevne tæller konfigurationen fra filen .wadcfg et minut efter start.  Hvis du opretter brugerdefinerede ydeevne tællere i metoden OnStart, og Start opgaver tage længere tid end et minut at udføre, der dine brugerdefinerede ydeevne tællere ikke har oprettet når Azure diagnosticering agent forsøger at indlæse dem.  I dette scenarie vises, Azure diagnosticering korrekt registrerer alle diagnosticering data undtagen tællere i din brugerdefinerede ydeevne.  Du kan løse dette problem ved at oprette tællerne i ydeevne i en Start-opgave eller flytte nogle af Start opgaven arbejde til metoden OnStart efter oprettelse af tællerne i ydeevne.

Udfør følgende trin for at oprette en simpel brugerdefinerede ydeevne tæller med navnet "\MyCustomCounterCategory\MyButton1Counter":

1. Åbn den tjeneste definitionsfil (CSDEF) for dit program.
2. Tilføje Runtime element til WebRole eller WorkerRole elementet for at tillade kørsel af med administratorrettigheder:

    ```
    <runtime executioncontext="elevated"/>
    ```
3. Gem filen.
4. Åbn filen diagnosticering (diagnostics.wadcfg i SDK 2.4 og nedenfor eller diagnostics.wadcfgx i SDK 2,5 og ovenfor) og tilføje følgende i DiagnosticMonitorConfiguration 

    ```
    <PerformanceCounters bufferQuotaInMB="0" scheduledTransferPeriod="PT30M">
     <PerformanceCounterConfiguration counterSpecifier="\MyCustomCounterCategory\MyButton1Counter" sampleRate="PT30S"/>
    </PerformanceCounters>
    ```
5. Gem filen.
6. Oprette brugerdefinerede ydeevne tæller kategori i metoden OnStart for din rolle, før aktivering base. OnStart. I følgende C#-eksempel oprettes en brugerdefineret kategori, hvis den ikke allerede findes:

    ```
    public override bool OnStart()
    {
    if (!PerformanceCounterCategory.Exists("MyCustomCounterCategory"))
    {
       CounterCreationDataCollection counterCollection = new CounterCreationDataCollection();

       // add a counter tracking user button1 clicks
       CounterCreationData operationTotal1 = new CounterCreationData();
       operationTotal1.CounterName = "MyButton1Counter";
       operationTotal1.CounterHelp = "My Custom Counter for Button1";
       operationTotal1.CounterType = PerformanceCounterType.NumberOfItems32;
       counterCollection.Add(operationTotal1);

       PerformanceCounterCategory.Create(
         "MyCustomCounterCategory",
         "My Custom Counter Category",
         PerformanceCounterCategoryType.SingleInstance, counterCollection);

       Trace.WriteLine("Custom counter category created.");
    }
    else{
       Trace.WriteLine("Custom counter category already exists.");
    }

    return base.OnStart();
    }
    ```
7. Opdater tællerne i dit program. I følgende eksempel opdaterer en brugerdefineret ydeevne tæller Button1_Click hændelser:

    ```
    protected void Button1_Click(object sender, EventArgs e)
        {
         PerformanceCounter button1Counter = new PerformanceCounter(
           "MyCustomCounterCategory",
           "MyButton1Counter",
           string.Empty,
           false);
         button1Counter.Increment();
        this.Button1.Text = "Button 1 count: " +
           button1Counter.RawValue.ToString();
        }
    ```
8. Gem filen.  

Brugerdefineret ydeevne tællerdata indsamles ved Azure diagnosticering skærmen.

## <a name="step-3-query-performance-counter-data"></a>Trin 3: Forespørgsel tæller ydelsesdata

Når din programmet installeres og kører diagnosticering skærm begynder ved at indsamle tællere i ydeevne og bevare dataene til Azure-lager. Du kan bruge funktioner såsom Server Explorer i Visual Studio, [Azure Storage Explorer](http://azurestorageexplorer.codeplex.com/)eller [Azure diagnosticering Manager](http://www.cerebrata.com/Products/AzureDiagnosticsManager/Default.aspx) ved Cerebrata til at få vist tællere ydelsesdata i tabellen WADPerformanceCountersTable. Du kan også programmeringsmæssigt forespørge tjenesten tabel ved hjælp af [C#](../storage/storage-dotnet-how-to-use-tables.d), [Java](../storage/storage-java-how-to-use-table-storage.md), [Node.js](../storage/storage-nodejs-how-to-use-table-storage.md), [Python](../storage/storage-python-how-to-use-table-storage.md), [fonetisk](../storage/storage-ruby-how-to-use-table-storage.md)eller [PHP](../storage/storage-php-how-to-use-table-storage.md).

I følgende C#-eksempel viser en simpel forespørgsel mod tabellen WADPerformanceCountersTable og gemmer diagnosticering dataene til en CSV-fil. Når tællerne i ydeevne er gemt i en CSV-fil kan du bruge funktionerne graphing i Microsoft Excel eller et andet værktøj til visualisering af dataene. Sørg for at tilføje en reference til Microsoft.WindowsAzure.Storage.dll, som er inkluderet i Azure SDK til .NET oktober 2012 og nyere. Samlingen er installeret til mappen % Program Files%\Microsoft SDKs\Microsoft Azure.NET SDK\version-num\ref\.

```
    using Microsoft.WindowsAzure.Storage;
    using Microsoft.WindowsAzure.Storage.Auth;
    using Microsoft.WindowsAzure.Storage.Table;
    ...

    // Get the connection string. When using Microsoft Azure Cloud Services, it is recommended
    // you store your connection string using the Microsoft Azure service configuration
    // system (*.csdef and *.cscfg files). You can you use the CloudConfigurationManager type
    // to retrieve your storage connection string.  If you're not using Cloud Services, it's
    // recommended that you store the connection string in your web.config or app.config file.
    // Use the ConfigurationManager type to retrieve your storage connection string.

    string connectionString = Microsoft.WindowsAzure.CloudConfigurationManager.GetSetting("StorageConnectionString");
    //string connectionString = System.Configuration.ConfigurationManager.ConnectionStrings["StorageConnectionString"].ConnectionString;

    // Get a reference to the storage account using the connection string.  You can also use the development
    // storage account (Storage Emulator) for local debugging.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(connectionString);
    //CloudStorageAccount storageAccount = CloudStorageAccount.DevelopmentStorageAccount;

    // Create the table client.
    CloudTableClient tableClient = storageAccount.CreateCloudTableClient();

    // Create the CloudTable object that represents the "WADPerformanceCountersTable" table.
    CloudTable table = tableClient.GetTableReference("WADPerformanceCountersTable");

    // Create the table query, filter on a specific CounterName, DeploymentId and RoleInstance.
    TableQuery<PerformanceCountersEntity> query = new TableQuery<PerformanceCountersEntity>()
       .Where(
          TableQuery.CombineFilters(
          TableQuery.GenerateFilterCondition("CounterName", QueryComparisons.Equal, @"\Processor(_Total)\% Processor Time"),
          TableOperators.And,
          TableQuery.CombineFilters(
          TableQuery.GenerateFilterCondition("DeploymentId", QueryComparisons.Equal, "ec26b7a1720447e1bcdeefc41c4892a3"),
          TableOperators.And,
          TableQuery.GenerateFilterCondition("RoleInstance", QueryComparisons.Equal, "WebRole1_IN_0")
          )
       )
    );

    // Execute the table query.
    IEnumerable<PerformanceCountersEntity> result = table.ExecuteQuery(query);

    // Process the query results and build a CSV file.
    StringBuilder sb = new StringBuilder("TimeStamp,EventTickCount,DeploymentId,Role,RoleInstance,CounterName,CounterValue\n");

    foreach (PerformanceCountersEntity entity in result)
    {
       sb.Append(entity.Timestamp + "," + entity.EventTickCount + "," + entity.DeploymentId + ","
          + entity.Role + "," + entity.RoleInstance + "," + entity.CounterName + "," + entity.CounterValue+"\n");
    }

    StreamWriter sw = File.CreateText(@"C:\temp\PerfCounters.csv");
    sw.Write(sb.ToString());
    sw.Close();
```

Enheder knyttes til C# objekter ved hjælp af en brugerdefineret klasse afledt **TableEntity**. Følgende kode definerer en enhed klasse, der repræsenterer en ydeevne tæller i tabellen **WADPerformanceCountersTable** .


    public class PerformanceCountersEntity : TableEntity
    {
       public long EventTickCount { get; set; }
       public string DeploymentId { get; set; }
       public string Role { get; set; }
       public string RoleInstance { get; set; }
       public string CounterName { get; set; }
       public double CounterValue { get; set; }
    }


## <a name="next-steps"></a>Næste trin
[Vis flere artikler om Azure diagnosticering] (.. / azure-diagnostics.md)
