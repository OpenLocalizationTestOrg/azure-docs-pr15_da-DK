<properties
    pageTitle="Effektiv listen forespørgsler i Azure batchen | Microsoft Azure"
    description="Øge ydeevnen ved at filtrere dine forespørgsler, når der anmodes om oplysninger om batchen ressourcer, som grupper, job, opgaver og beregne noder."
    services="batch"
    documentationCenter=".net"
    authors="mmacy"
    manager="timlt"
    editor="" />

<tags
    ms.service="batch"
    ms.devlang="multiple"
    ms.topic="article"
    ms.tgt_pltfrm="vm-windows"
    ms.workload="big-compute"
    ms.date="10/25/2016"
    ms.author="marsma" />

# <a name="query-the-azure-batch-service-efficiently"></a>Forespørgsel tjenesten Azure batchen effektivt

Her kan du lære, hvordan at øge Azure batchen programmets ydeevne ved at reducere mængden data, der returneres af tjenesten, når du forespørge job, opgaver og beregne noder med [Batchen .NET] [ api_net] bibliotek.

Næsten alle batchen programmer skal udføre nogle typer overvågning eller en anden handling, som forespørger tjenesten batchen ofte med jævne mellemrum. For eksempel for at finde ud af, om der er en hvilken som helst i kø opgaver, der er tilbage i et job, skal du hente data på hver opgave i jobbet. Du kan se status for knuderne i din gruppe, skal du henter data på hver node i puljen. I denne artikel beskrives det, hvordan du kan udføre disse forespørgsler i den mest effektive måde.

## <a name="meet-the-detaillevel"></a>Opfylde DetailLevel

I en fremstilling batchen programmet, kan objekter som job, opgaver og Beregn noder tallet i tusinder. Hvis du vil anmode om oplysninger om disse ressourcer, skal en potentielt stor mængde data "cross netværket" fra tjenesten batchen i dit program på hver forespørgsel. Ved at begrænse antallet af elementer og typen af oplysninger, der returneres af en forespørgsel, kan du øge hastigheden af dine forespørgsler, og derfor ydeevnen for dit program.

Denne [Batchen .NET] [ api_net] API kodestykke viser en liste over *hver* opgave, der er knyttet til en sag, sammen med *alle* af egenskaberne for hver opgave:

```csharp
// Get a collection of all of the tasks and all of their properties for job-001
IPagedEnumerable<CloudTask> allTasks =
    batchClient.JobOperations.ListTasks("job-001");
```

Du kan udføre en meget mere effektiv listen forespørgsel, men ved at anvende en "detaljeringsniveauet" til din forespørgsel. Det gør du ved angivelse af en [ODATADetailLevel] [ odata] objekt mod [JobOperations.ListTasks] [ net_list_tasks] metode. Denne kodestykke returnerer kun-ID, kommandolinjen og Beregn node oplysninger egenskaber for fuldførte opgaver:

```csharp
// Configure an ODATADetailLevel specifying a subset of tasks and
// their properties to return
ODATADetailLevel detailLevel = new ODATADetailLevel();
detailLevel.FilterClause = "state eq 'completed'";
detailLevel.SelectClause = "id,commandLine,nodeInfo";

// Supply the ODATADetailLevel to the ListTasks method
IPagedEnumerable<CloudTask> completedTasks =
    batchClient.JobOperations.ListTasks("job-001", detailLevel);
```

I dette eksempel scenarie, hvis der er tusindvis af opgaver i sagen, resultaterne fra den anden forespørgsel typisk returneres meget hurtigere end den første linje. Du kan finde flere oplysninger om brug af ODATADetailLevel når listeelementer med batchen .NET API er inkluderet [nedenfor](#efficient-querying-in-batch-net).

> [AZURE.IMPORTANT]
> Vi anbefaler på det kraftigste, du skal *altid* angive et ODATADetailLevel objekt til dine .NET API listen opkald til at sikre maksimal effektivitet og ydeevnen for dit program. Ved at angive et detaljeringsniveau, kan du hjælpe med at sænke tidspunkter for batchen service svar, forbedre udnyttelse af netværk og minimere hukommelsesforbrug af klientprogrammer.

## <a name="filter-select-and-expand"></a>Filtrere, Vælg, og udvid

[Batchen .NET] [ api_net] og [Batchen RESTEN] [ api_rest] API'er giver mulighed for at reducere antallet af elementer, der returneres på en liste, og mængden oplysninger, der returneres for hver. Du kan gøre det ved at angive **filter**skal du **vælge**og **udvide strenge** , når du udfører listen forespørgsler.

### <a name="filter"></a>Filter
Filterstrengen er et udtryk, der reducerer antallet af elementer, der returneres. For eksempel kun de igangværende opgaver for en sag, eller listen kun Beregn noder, der er klar til at køre opgaver.

- Filterstrengen består af et eller flere udtryk med et udtryk, der består af et egenskabsnavn, operator og værdi. De egenskaber, der kan angives er specifikke for hver enhedstype, som du opretter en forespørgsel, som er de operatorer, der understøttes for hver egenskab.
- Flere udtryk kan kombineres ved hjælp af logiske operatorer `and` og `or`.
- I dette eksempel filterlister streng kun kørslen "gengive" opgaver: `(state eq 'running') and startswith(id, 'renderTask')`.

### <a name="select"></a>Vælg
Vælg strengen begrænser de egenskabsværdier, der returneres for hvert element. Du angiver en liste over navne, og kun egenskabsværdier, returneres for elementerne i forespørgselsresultaterne.

- Vælg strengen består af en kommasepareret liste over egenskabsnavne. Du kan angive egenskaber for typen objekttype du forespørger.
- Dette eksempel Vælg streng angiver, at kun tre egenskabsværdier skal returneres for hver opgave: `id, state, stateTransitionTime`.

### <a name="expand"></a>Udvid
Udvid strengen reducerer antallet af API-kald, der kræves for at få visse oplysninger. Når du bruger en udvidelsesikonet streng, du kan finde flere oplysninger om hvert element kan fås med et enkelt API-opkald. I stedet for først at anskaffe på listen over objekter, der anmoder om oplysninger for hvert element på listen skal bruge du en udvidelsesikonet streng til at hente de samme oplysninger i et enkelt API-opkald. Mindre API-kald betyder bedre ydeevne.

- Svarer til den vælge streng, udvid streng kontrolelementerne om bestemte data er inkluderet i listen forespørgselsresultaterne.
- Udvid strengen understøttes kun, når den bruges i listen over job, jobbet tidsplaner, opgaver og grupper. Det understøtter i øjeblikket kun statistiske oplysninger.
- Når alle egenskaber, der er nødvendige, og vælg strengen ikke er angivet, bruges Udvid streng, der *skal* til at få statistikoplysninger. Hvis en Vælg streng bruges til at få et undersæt af egenskaber, derefter `stats` kan angives i Vælg strengen, og udvid strengen ikke skal angives.
- I dette eksempel udvide streng angiver, at der skal returneres statistikoplysninger for hvert element på listen: `stats`.

> [AZURE.NOTE] Når bygning af en af de tre streng forespørgselstyper (filtrere, markere og udvide), skal du sikre dig, at egenskabsnavne og store og små bogstaver svarer, deres REST-API element versionerne. For eksempel, når du arbejder med klassen .NET [CloudTask](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudtask) , skal du angive **tilstand** i stedet for **tilstand**, selvom egenskaben .NET er [CloudTask.State](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudtask.state). Finder du i tabellerne nedenfor for egenskabstilknytninger mellem .NET og REST API'er.

### <a name="rules-for-filter-select-and-expand-strings"></a>Regler for filter, Vælg, og udvid strenge

- Egenskaber navne i filter skal du vælge, og udvid strenge skal vises som [Batchen RESTEN] [ api_rest] API – selv når du bruger [Batchen .NET] [ api_net] eller en af de andre batchen SDK'er.
- Alle egenskabsnavne er store og små bogstaver, men egenskabsværdier er bogstaver.
- Dato og klokkeslæt strenge kan være en af to formater og skal indledes med `DateTime`.

  - W3C-DTF format eksempel:`creationTime gt DateTime'2011-05-08T08:49:37Z'`
  - RFC 1123 format eksempel:`creationTime gt DateTime'Sun, 08 May 2011 08:49:37 GMT'`
- Boolesk strenge er enten `true` eller `false`.
- Hvis der er angivet en ugyldig egenskab eller operator, en `400 (Bad Request)` opstå fejl.

## <a name="efficient-querying-in-batch-net"></a>Effektiv forespørgsler i batchen .NET

I [Batchen .NET] [ api_net] API, [ODATADetailLevel] [ odata] klasse bruges til angivelse af filter skal du vælge og udvide strenge til listen handlinger. Klassen ODataDetailLevel har tre offentlige strengegenskaber, der kan være angivet i parametre, eller Angiv direkte på objektet. Du derefter overføre ODataDetailLevel objektet som en parameter til de forskellige liste operationer som [ListPools][net_list_pools], [ListJobs][net_list_jobs], og [ListTasks][net_list_tasks].

- [ODATADetailLevel][odata]. [FilterClause] [ odata_filter]: Begrænse antallet af elementer, der skal returneres.
- [ODATADetailLevel][odata]. [SelectClause] [ odata_select]: Angive, hvilke egenskabsværdier returneres med hvert element.
- [ODATADetailLevel][odata]. [ExpandClause] [ odata_expand]: Hent data for alle elementer i en enkelt API ringe i stedet for separate opkald for hvert element.

Følgende kodestykke bruger batchen .NET API til at forespørge effektivt tjenesten batchen for statistikken for bestemte grupper. I dette scenarie skal har batchen brugeren både test- og grupper. Test puljen id'er er præfikset "test", og fremstilling puljen id'er er præfikset "Prod.ordre". I kodestykket er *myBatchClient* en initialiseret korrekt forekomst af klassen [BatchClient](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.batchclient) .

```csharp
// First we need an ODATADetailLevel instance on which to set the filter, select,
// and expand clause strings
ODATADetailLevel detailLevel = new ODATADetailLevel();

// We want to pull only the "test" pools, so we limit the number of items returned
// by using a FilterClause and specifying that the pool IDs must start with "test"
detailLevel.FilterClause = "startswith(id, 'test')";

// To further limit the data that crosses the wire, configure the SelectClause to
// limit the properties that are returned on each CloudPool object to only
// CloudPool.Id and CloudPool.Statistics
detailLevel.SelectClause = "id, stats";

// Specify the ExpandClause so that the .NET API pulls the statistics for the
// CloudPools in a single underlying REST API call. Note that we use the pool's
// REST API element name "stats" here as opposed to "Statistics" as it appears in
// the .NET API (CloudPool.Statistics)
detailLevel.ExpandClause = "stats";

// Now get our collection of pools, minimizing the amount of data that is returned
// by specifying the detail level that we configured above
List<CloudPool> testPools =
    await myBatchClient.PoolOperations.ListPools(detailLevel).ToListAsync();
```

> [AZURE.TIP] En forekomst af [ODATADetailLevel] [ odata] , der er konfigureret med Vælg og udvid delsætninger kan også overføres for at få metoder, såsom [PoolOperations.GetPool](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.pooloperations.getpool.aspx), for at begrænse mængden data, der returneres er relevant.

## <a name="batch-rest-to-net-api-mappings"></a>Batchen RESTEN til .NET API tilknytninger

Egenskabsnavne i filter skal du vælge, og udvid strenge *skal* afspejle deres REST-API-versionerne, både i navn og store og små bogstaver. Nedenstående tabeller indeholder tilknytninger mellem .NET og REST-API versionerne.

### <a name="mappings-for-filter-strings"></a>Tilknytninger for filter strenge

- **.NET listen metoder**: hvert af .NET API metoder i denne kolonne accepterer en [ODATADetailLevel] [ odata] objekt som en parameter.
- **RESTEN listen anmodninger**: hver REST-API-side, der er sammenkædet med i denne kolonne indeholder en tabel, der angiver egenskaber og handlinger, som er tilladt i *filter* strenge. Du vil bruge disse egenskabsnavne og handlinger, når du opbygger en [ODATADetailLevel.FilterClause] [ odata_filter] streng.

| .NET listen metoder | RESTEN af listen anmodninger om |
|---|---|
| [CertificateOperations.ListCertificates][net_list_certs] | [Liste over certifikaterne på en konto][rest_list_certs]
| [CloudTask.ListNodeFiles][net_list_task_files] | [Liste over de filer, der er knyttet til en opgave][rest_list_task_files]
| [JobOperations.ListJobPreparationAndReleaseTaskStatus][net_list_jobprep_status] | [Liste over status for jobbet forberedelse og jobbet release opgaver for en sag][rest_list_jobprep_status]
| [JobOperations.ListJobs][net_list_jobs] | [Liste over job på en konto][rest_list_jobs]
| [JobOperations.ListNodeFiles][net_list_nodefiles] | [Liste over filerne på en node][rest_list_nodefiles]
| [JobOperations.ListTasks][net_list_tasks] | [De opgaver, der er knyttet til et job][rest_list_tasks]
| [JobScheduleOperations.ListJobSchedules][net_list_job_schedules] | [Liste over job tidsplaner på en konto][rest_list_job_schedules]
| [JobScheduleOperations.ListJobs][net_list_schedule_jobs] | [Liste over de job, der er knyttet til en tidsplan for job][rest_list_schedule_jobs]
| [PoolOperations.ListComputeNodes][net_list_compute_nodes] | [Liste over Beregn knuderne i en gruppe][rest_list_compute_nodes]
| [PoolOperations.ListPools][net_list_pools] | [Liste over grupper på en konto][rest_list_pools]

### <a name="mappings-for-select-strings"></a>Tilknytninger for Vælg strenge

- **Batchen .NET datatyper**: batchen .NET API typer.
- **REST-API enheder**: hver side i denne kolonne indeholder en eller flere tabeller, hvor der står REST-API egenskabsnavne for typen. Disse egenskabsnavne bruges, når du opbygger *Vælg* strenge. Du vil bruge disse samme egenskabsnavne, når du opbygger en [ODATADetailLevel.SelectClause] [ odata_select] streng.

| Batchen .NET-datatyper | REST-API enheder |
|---|---|
| [Certifikat][net_cert] | [Få oplysninger om et certifikat][rest_get_cert] |
| [CloudJob][net_job] | [Få oplysninger om et job][rest_get_job] |
| [CloudJobSchedule][net_schedule] | [Få oplysninger om en tidsplan for job][rest_get_schedule] |
| [ComputeNode][net_node] | [Få oplysninger om en node][rest_get_node] |
| [CloudPool][net_pool] | [Få oplysninger om en gruppe][rest_get_pool] |
| [CloudTask][net_task] | [Få oplysninger om en opgave][rest_get_task] |

## <a name="example-construct-a-filter-string"></a>Eksempel: oprette en filterstreng

Når du opbygger en streng, der Filtrer for [ODATADetailLevel.FilterClause][odata_filter], se tabellen ovenfor under "Tilknytninger for filter strenge" til søgning efter REST-API dokumentation side, der svarer til handlingen liste, du vil udføre. Du kan finde de filtrerbare egenskaber og deres understøttede operatorer i den første FlereRækker tabel på den pågældende side. Hvis du vil hente alle opgaver, der blev ikke er nul, hvis afslutningskode for eksempel denne række liste over [opgaverne, der er knyttet til et job] [ rest_list_tasks] angiver en streng til gældende egenskaben og tilladte operatorer:

| Egenskaben | Som er tilladt | Type |
| :--- | :--- | :--- |
| `executionInfo/exitCode` | `eq, ge, gt, le , lt` | `Int` |

Dermed, der ville være Filterstrengen til listen over alle opgaver med en undtagen nul afslutningskode:

`(executionInfo/exitCode lt 0) or (executionInfo/exitCode gt 0)`

## <a name="example-construct-a-select-string"></a>Eksempel: oprette en select streng

At opbygge [ODATADetailLevel.SelectClause][odata_select], se tabellen ovenfor under "Tilknytninger for Vælg strenge", og gå til siden REST-API, der svarer til typen objekt, du listen over. Du kan finde de egenskaber, der kan vælges og deres understøttede operatorer i den første FlereRækker tabel på den pågældende side. Hvis du vil hente de-ID og kommandolinjen for hver opgave i en liste, f.eks, du kan finde disse rækker i tabellen gældende på [få oplysninger om en opgave][rest_get_task]:

| Egenskaben | Type | Noter |
| :--- | :--- | :--- |
| `id` | `String` | `The ID of the task.` |
| `commandLine` | `String` | `The command line of the task.` |

Vælg strengen for at medtage det-ID og kommandolinjen sammen med hver vises opgave bliver:

`id, commandLine`

## <a name="code-samples"></a>Kodeeksempler

### <a name="efficient-list-queries-code-sample"></a>Eksempel på effektiv listen forespørgsler-kode

Se [EfficientListQueries] [ efficient_query_sample] eksempelprojekt på GitHub at se, hvordan effektiv listen forespørgsel kan påvirke ydeevnen i et program. C# console programmet opretter og føjer et stort antal opgaver til en sag. Derefter det giver flere opkald til [JobOperations.ListTasks] [ net_list_tasks] metode og gennemløb [ODATADetailLevel] [ odata] objekter, der er konfigureret med forskellige egenskabsværdier for at variere mængden af data, der skal returneres. Det giver output, der ligner følgende:

```
Adding 5000 tasks to job jobEffQuery...
5000 tasks added in 00:00:47.3467587, hit ENTER to query tasks...

4943 tasks retrieved in 00:00:04.3408081 (ExpandClause:  | FilterClause: state eq 'active' | SelectClause: id,state)
0 tasks retrieved in 00:00:00.2662920 (ExpandClause:  | FilterClause: state eq 'running' | SelectClause: id,state)
59 tasks retrieved in 00:00:00.3337760 (ExpandClause:  | FilterClause: state eq 'completed' | SelectClause: id,state)
5000 tasks retrieved in 00:00:04.1429881 (ExpandClause:  | FilterClause:  | SelectClause: id,state)
5000 tasks retrieved in 00:00:15.1016127 (ExpandClause:  | FilterClause:  | SelectClause: id,state,environmentSettings)
5000 tasks retrieved in 00:00:17.0548145 (ExpandClause: stats | FilterClause:  | SelectClause: )

Sample complete, hit ENTER to continue...
```

Som vist i forløbet tid, kan du meget sænke forespørgsel svar gange ved at begrænse egenskaberne og antallet af elementer, der returneres. Du kan finde dette og andre eksempel projekter i [azure-batchen-eksempler] [ github_samples] lager på GitHub.

### <a name="batchmetrics-library-and-code-sample"></a>BatchMetrics bibliotek og kode eksempel

Ud over EfficientListQueries kodeeksemplet ovenfor, kan du finde [BatchMetrics] [ batch_metrics] projekt i [azure-batchen-eksempler] [ github_samples] GitHub lager. Projektets BatchMetrics eksempel viser, hvordan du effektivt overvåge Azure batchen job fremdrift ved hjælp af API'EN batchen.

[BatchMetrics] [ batch_metrics] eksempel indeholder et projekt i .NET klasse bibliotek, kan du medtage i din egen projekter og et enkelt kommandolinjen program til at øve og brugen af biblioteket.

På eksempelprogrammet i projektet viser følgende handlinger:

1. Vælge bestemte attributter for at hente kun de ønskede egenskaber
2. Filtrering på tilstand overgang gange for at hente kun ændringer siden den sidste forespørgsel

For eksempel vises følgende metode i biblioteket BatchMetrics. Returnerer den en ODATADetailLevel, der angiver, at kun den `id` og `state` egenskaber skal der opnås for de enheder, der spørges. Det også angiver, at kun de enheder, hvis tilstand er blevet ændret siden den angivne `DateTime` parameter skal returneres.

```csharp
internal static ODATADetailLevel OnlyChangedAfter(DateTime time)
{
    return new ODATADetailLevel(
        selectClause: "id, state",
        filterClause: string.Format("stateTransitionTime gt DateTime'{0:o}'", time)
    );
}
```

## <a name="next-steps"></a>Næste trin

### <a name="parallel-node-tasks"></a>Parallelle node opgaver

[Maksimere Azure batchen beregne Ressourceforbrug med samtidige node opgaver](batch-parallel-node-tasks.md) er relateret til batchen programmets ydeevne i artiklen. Visse typer arbejdsbelastninger, som kan få glæde af afholde parallelle opgaver på større – men færre – beregne noder. Se [eksempel scenario](batch-parallel-node-tasks.md#example-scenario) i artiklen få at vide om dette formål.

### <a name="batch-forum"></a>Batchen-Forum

[Azure batchen Forum] [ forum] på MSDN er et godt sted at diskutere batchen og Stil spørgsmål om tjenesten. Hoved på over til nyttige "sticky" indlæg, og stil dit spørgsmål, efterhånden som de opstår, mens du opretter dine batchen løsninger.


[api_net]: http://msdn.microsoft.com/library/azure/mt348682.aspx
[api_net_listjobs]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.joboperations.listjobs.aspx
[api_rest]: http://msdn.microsoft.com/library/azure/dn820158.aspx
[batch_metrics]: https://github.com/Azure/azure-batch-samples/tree/master/CSharp/BatchMetrics
[efficient_query_sample]: https://github.com/Azure/azure-batch-samples/tree/master/CSharp/ArticleProjects/EfficientListQueries
[forum]: https://social.msdn.microsoft.com/forums/azure/en-US/home?forum=azurebatch
[github_samples]: https://github.com/Azure/azure-batch-samples
[odata]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.odatadetaillevel.aspx
[odata_ctor]: https://msdn.microsoft.com/library/azure/dn866178.aspx
[odata_expand]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.odatadetaillevel.expandclause.aspx
[odata_filter]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.odatadetaillevel.filterclause.aspx
[odata_select]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.odatadetaillevel.selectclause.aspx

[net_list_certs]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.certificateoperations.listcertificates.aspx
[net_list_compute_nodes]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.pooloperations.listcomputenodes.aspx
[net_list_job_schedules]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.jobscheduleoperations.listjobschedules.aspx
[net_list_jobprep_status]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.joboperations.listjobpreparationandreleasetaskstatus.aspx
[net_list_jobs]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.joboperations.listjobs.aspx
[net_list_nodefiles]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.joboperations.listnodefiles.aspx
[net_list_pools]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.pooloperations.listpools.aspx
[net_list_schedule_jobs]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.jobscheduleoperations.listjobs.aspx
[net_list_task_files]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudtask.listnodefiles.aspx
[net_list_tasks]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.joboperations.listtasks.aspx

[rest_list_certs]: https://msdn.microsoft.com/library/azure/dn820154.aspx
[rest_list_compute_nodes]: https://msdn.microsoft.com/library/azure/dn820159.aspx
[rest_list_job_schedules]: https://msdn.microsoft.com/library/azure/mt282174.aspx
[rest_list_jobprep_status]: https://msdn.microsoft.com/library/azure/mt282170.aspx
[rest_list_jobs]: https://msdn.microsoft.com/library/azure/dn820117.aspx
[rest_list_nodefiles]: https://msdn.microsoft.com/library/azure/dn820151.aspx
[rest_list_pools]: https://msdn.microsoft.com/library/azure/dn820101.aspx
[rest_list_schedule_jobs]: https://msdn.microsoft.com/library/azure/mt282169.aspx
[rest_list_task_files]: https://msdn.microsoft.com/library/azure/dn820142.aspx
[rest_list_tasks]: https://msdn.microsoft.com/library/azure/dn820187.aspx

[rest_get_cert]: https://msdn.microsoft.com/library/azure/dn820176.aspx
[rest_get_job]: https://msdn.microsoft.com/library/azure/dn820106.aspx
[rest_get_node]: https://msdn.microsoft.com/library/azure/dn820168.aspx
[rest_get_pool]: https://msdn.microsoft.com/library/azure/dn820165.aspx
[rest_get_schedule]: https://msdn.microsoft.com/library/azure/mt282171.aspx
[rest_get_task]: https://msdn.microsoft.com/library/azure/dn820133.aspx

[net_cert]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.certificate.aspx
[net_job]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudjob.aspx
[net_node]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.computenode.aspx
[net_pool]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudpool.aspx
[net_schedule]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudjobschedule.aspx
[net_task]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudtask.aspx
