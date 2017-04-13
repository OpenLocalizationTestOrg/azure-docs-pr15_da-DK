<properties
   pageTitle="Opgradere til Azure Søg .NET SDK version 1.1 | Microsoft Azure | Hostet skyen search-tjenesten"
   description="Opgradere til Azure Søg .NET SDK version 1.1"
   services="search"
   documentationCenter=""
   authors="brjohnstmsft"
   manager="pablocas"
   editor=""/>

<tags
   ms.service="search"
   ms.devlang="dotnet"
   ms.workload="search"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.date="08/15/2016"
   ms.author="brjohnst"/>

# <a name="upgrading-to-the-azure-search-net-sdk-version-20-preview"></a>Opgradere til Azure Søg .NET SDK version 2.0-eksempel

Hvis du bruger version 1.1 eller ældre af [Azure Søg .NET SDK](https://msdn.microsoft.com/library/azure/dn951165.aspx), hjælpe i denne artikel dig med at opgradere dit program tilladelse til at bruge den næste overordnet version 2.0-preview.

Se, [hvordan du bruger Azure søgning fra et program i .NET](search-howto-dotnet-sdk.md)for en mere generelle gennemgang af SDK, herunder eksempler.

Version 2.0-eksempelvisning af Azure Søg .NET SDK indeholder nogle ændringer fra tidligere versioner. Dette er hovedsageligt mindre, så ændre din kode skal kræver kun minimale indsats. Se [trin til at opgradere](#UpgradeSteps) til vejledning i at ændre din kode til at bruge den nye SDK-version.

> [AZURE.NOTE] Hvis du bruger version 0,13 udskrift eller ældre, bør du opgradere til version 1.1 første og derefter opgradere til 2.0-preview. Se [Tillæg: trin til at opgradere til version 1.1](#UpgradeStepsV1) vejledning.

<a name="WhatsNew"></a>
## <a name="whats-new-in-version-20-preview"></a>Hvad er nyt i version 2.0-eksempel

Version 2.0-preview er den første version af Azure Søg .NET SDK målrette en prøveversion af Azure Search REST API'ER, specifikt 2015-02-28-preview. Dette gør det muligt at bruge mange preview-funktioner i Azure søgning fra et .NET-program, herunder følgende:

- [Brugerdefinerede programmer til analyse](https://aka.ms/customanalyzers)
- [Azure Blob-lager](search-howto-indexing-azure-blob-storage.md) og [Azure Table Storage](search-howto-indexing-azure-tables.md) indekseringsprogram support
- Indekseringsprogram tilpasning via [tilknytninger](search-indexer-field-mappings.md)
- ETags support for at aktivere fejlsikret samtidige opdatering af indeks definitioner, indeks og datakilder
- Understøttelse af .NET Core og .NET Portable profil 111

<a name="UpgradeSteps"></a>
## <a name="steps-to-upgrade"></a>Trin til opgradering

Først skal opdatere din NuGet reference til `Microsoft.Azure.Search` ved hjælp af konsollen NuGet Package Manager eller ved at højreklikke på dit projekt-referencer og vælge "Administrere NuGet pakker..." i Visual Studio. Sørg for, at du aktiverer foreløbige pakker ved at vælge "Omfatter foreløbig version" i NuGet "Administrere pakker" vindue i Visual Studio eller ved hjælp af den `-IncludePrerelease` skifte, hvis du bruger NuGet pakke Manager-konsollen.

Når NuGet har hentet nye pakker og deres afhængigheder, genopbygge projektet. Afhængigt af hvordan din kode er struktureret, kan det genopbygge korrekt. Hvis det er tilfældet, er du klar til at gå!

Hvis din build mislykkes, skal du får vist fejlen build ud som følger:

    Program.cs(31,45,31,86): error CS0266: Cannot implicitly convert type 'Microsoft.Azure.Search.ISearchIndexClient' to 'Microsoft.Azure.Search.SearchIndexClient'. An explicit conversion exists (are you missing a cast?)

Næste trin er at rette fejlen build. Se [Bryd ændringer i version 2.0-preview](#ListOfChanges) få mere at vide om Hvad får fejlen, og hvordan at løse problemet.

Du kan få vist yderligere build advarsler relateret til forældede metoder eller egenskaber. Advarsler, der indeholder oplysninger om, hvad du skal bruge i stedet for funktionen frarådede. Eksempelvis hvis programmet bruger den `SearchRequestOptions.RequestId` egenskab, skal du får en advarsel, hvor der står`"This property is deprecated. Please use ClientRequestId instead."`

Når du har rettet buildfejl, kan du foretage ændringer i dit program for at kunne udnytte nye funktioner, hvis du ønsker. Nye funktioner i SDK gennemgås i [Hvad er nyt i version 2.0-preview](#WhatsNew).

<a name="ListOfChanges"></a>
## <a name="breaking-changes-in-version-20-preview"></a>Bryd ændringer i version 2.0-eksempel

Der er kun én seneste ændring i version 2.0-preview, som kan kræve kodeændringer ud over at genopbygge dit program.

### <a name="indexesgetclient-return-type"></a>Indexes.GetClient returtype.

Den `Indexes.GetClient` metode har en ny returtype. Tidligere, returneres `SearchIndexClient`, men det er blevet ændret til `ISearchIndexClient` i version 2.0-preview. Dette er understøtter kunder, vil mock på `GetClient` metode til test af enhed ved at returnere en mock implementering af `ISearchIndexClient`.

#### <a name="example"></a>Eksempel

Hvis din kode ser sådan ud:

```csharp
SearchIndexClient indexClient = serviceClient.Indexes.GetClient("hotels");
```

Du kan ændre det til denne indstilling for at løse eventuelle buildfejl:

```csharp
ISearchIndexClient indexClient = serviceClient.Indexes.GetClient("hotels");
```

## <a name="conclusion"></a>Konklusion
Hvis du har brug for flere oplysninger om brugen af Azure Søg .NET SDK, kan du se vores senest opdaterede [vejledning](search-howto-dotnet-sdk.md).

Vi Velkommen din feedback om SDK. Hvis du støder på problemer, Velkommen til at bede om hjælp til os på [Azure Søg MSDN-forum](https://social.msdn.microsoft.com/Forums/azure/en-US/home?forum=azuresearch). Hvis du finder en fejl, kan du sende et problem i [Azure .NET SDK GitHub lager](https://github.com/Azure/azure-sdk-for-net/issues). Sørg for, at der indsættes tegn foran dit problemtitel med "Søg SDK:".

Tak for ved hjælp af Azure søgning!

<a name="UpgradeStepsV1"></a>
## <a name="appendix-steps-to-upgrade-to-version-11"></a>Tillæg: Trin til at opgradere til version 1.1 

> [AZURE.NOTE] Dette afsnit gælder kun for brugere af den Azure Søg .NET SDK version 0,13 preview og ældre versioner.

Først skal opdatere din NuGet reference til `Microsoft.Azure.Search` ved hjælp af konsollen NuGet Package Manager eller ved at højreklikke på dit projekt-referencer og vælge "Administrere NuGet pakker..." i Visual Studio.

Når NuGet har hentet nye pakker og deres afhængigheder, genopbygge projektet.

Hvis du tidligere har brugt version 1.0.0-preview, 1.0.1-preview eller 1.0.2-preview, Opret skal følge efter, og du er klar til at gå!

Hvis du tidligere har brugt version 0.13.0-preview eller ældre, skal du se afsnittet oprette fejl som følger:

    Program.cs(137,56,137,62): error CS0117: 'Microsoft.Azure.Search.Models.IndexBatch' does not contain a definition for 'Create'
    Program.cs(137,99,137,105): error CS0117: 'Microsoft.Azure.Search.Models.IndexAction' does not contain a definition for 'Create'
    Program.cs(146,41,146,54): error CS1061: 'Microsoft.Azure.Search.IndexBatchException' does not contain a definition for 'IndexResponse' and no extension method 'IndexResponse' accepting a first argument of type 'Microsoft.Azure.Search.IndexBatchException' could be found (are you missing a using directive or an assembly reference?)
    Program.cs(163,13,163,42): error CS0246: The type or namespace name 'DocumentSearchResponse' could not be found (are you missing a using directive or an assembly reference?)

Næste trin er at rette en buildfejl én ad gangen. De fleste kræver, at ændre nogle klasse og metode navne, der er blevet omdøbt i SDK. [Liste over bryde ændringer i version 1.1](#ListOfChangesV1) indeholder en liste over ændringerne navn.

Hvis du bruger tilpassede klasser model dine dokumenter, og disse klasser har egenskaber for ikke-null-værdi enkle typer (for eksempel `int` eller `bool` i C#), der er en rettelse i 1.1-versionen af i SDK, som skal du være opmærksom på. Du kan finde flere oplysninger i [fejlrettelser i version 1.1](#BugFixesV1) .

Til sidst, når du har rettet buildfejl, kan du foretage ændringer i dit program for at kunne udnytte nye funktioner, hvis du vil.

<a name="ListOfChangesV1"></a>
### <a name="list-of-breaking-changes-in-version-11"></a>Liste over bryde ændringer i version 1.1

Følgende liste er sorteret efter sandsynligheden for, at ændringen vil påvirke din programkode.

#### <a name="indexbatch-and-indexaction-changes"></a>IndexBatch og IndexAction ændringer

`IndexBatch.Create`er blevet omdøbt til `IndexBatch.New` og ikke længere har en `params` argument. Du kan bruge `IndexBatch.New` for batches, blande forskellige typer handlinger (fletter, sletter osv.). Desuden, der er nye statiske metoder til at oprette batches hvor alle handlinger er den samme: `Delete`, `Merge`, `MergeOrUpload`, og `Upload`.

`IndexAction`ikke længere har offentlige konstruktører og dens egenskaber kan ikke ændres nu. Du skal bruge de nye statiske metoder til at oprette handlinger til forskellige formål: `Delete`, `Merge`, `MergeOrUpload`, og `Upload`. `IndexAction.Create`er blevet fjernet. Hvis du har brugt overbelastning, der tager kun et dokument, skal du sørge for at bruge `Upload` i stedet.

##### <a name="example"></a>Eksempel

Hvis din kode ser sådan ud:

    var batch = IndexBatch.Create(documents.Select(doc => IndexAction.Create(doc)));
    indexClient.Documents.Index(batch);

Du kan ændre det til denne indstilling for at løse eventuelle buildfejl:

    var batch = IndexBatch.New(documents.Select(doc => IndexAction.Upload(doc)));
    indexClient.Documents.Index(batch);

Hvis du vil, kan du yderligere forenkle den til dette:

    var batch = IndexBatch.Upload(documents);
    indexClient.Documents.Index(batch);

#### <a name="indexbatchexception-changes"></a>IndexBatchException ændringer

Den `IndexBatchException.IndexResponse` egenskab er blevet omdøbt til `IndexingResults`, og typen er nu `IList<IndexingResult>`.

##### <a name="example"></a>Eksempel

Hvis din kode ser sådan ud:

    catch (IndexBatchException e)
    {
        Console.WriteLine(
            "Failed to index some of the documents: {0}",
            String.Join(", ", e.IndexResponse.Results.Where(r => !r.Succeeded).Select(r => r.Key)));
    }

Du kan ændre det til denne indstilling for at løse eventuelle buildfejl:

    catch (IndexBatchException e)
    {
        Console.WriteLine(
            "Failed to index some of the documents: {0}",
            String.Join(", ", e.IndexingResults.Where(r => !r.Succeeded).Select(r => r.Key)));
    }

<a name="OperationMethodChanges"></a>
#### <a name="operation-method-changes"></a>Handlingen metode ændringer

Hver handling i Azure Søg .NET SDK der vises som et sæt af metode overloads for synkron og asynkron ringer. Signaturer og factoring af disse metode overloads er ændret i version 1.1.

For eksempel vises handlingen "Få indeks statistik" i ældre versioner af SDK disse signaturer:

In `IIndexOperations`:

    // Asynchronous operation with all parameters
    Task<IndexGetStatisticsResponse> GetStatisticsAsync(
        string indexName,
        CancellationToken cancellationToken);

In `IndexOperationsExtensions`:

    // Asynchronous operation with only required parameters
    public static Task<IndexGetStatisticsResponse> GetStatisticsAsync(
        this IIndexOperations operations,
        string indexName);

    // Synchronous operation with only required parameters
    public static IndexGetStatisticsResponse GetStatistics(
        this IIndexOperations operations,
        string indexName);

Metode signaturer for den samme handling i version 1.1 ser sådan ud:

In `IIndexesOperations`:

    // Asynchronous operation with lower-level HTTP features exposed
    Task<AzureOperationResponse<IndexGetStatisticsResult>> GetStatisticsWithHttpMessagesAsync(
        string indexName,
        SearchRequestOptions searchRequestOptions = default(SearchRequestOptions),
        Dictionary<string, List<string>> customHeaders = null,
        CancellationToken cancellationToken = default(CancellationToken));

In `IndexesOperationsExtensions`:

    // Simplified asynchronous operation
    public static Task<IndexGetStatisticsResult> GetStatisticsAsync(
        this IIndexesOperations operations,
        string indexName,
        SearchRequestOptions searchRequestOptions = default(SearchRequestOptions),
        CancellationToken cancellationToken = default(CancellationToken));

    // Simplified synchronous operation
    public static IndexGetStatisticsResult GetStatistics(
        this IIndexesOperations operations,
        string indexName,
        SearchRequestOptions searchRequestOptions = default(SearchRequestOptions));

Starter med version 1.1, arrangerer Azure Søg .NET SDK handlingen metoder anderledes:

 - Valgfrie parametre er nu tilpasset som standard parametre hellere end yderligere metode overloads. Dette reducerer antallet af metode overloads sommetider dramatisk.
 - Metoderne lokalnummer skjule nu en masse overflødige oplysninger om HTTP fra kalderen. For eksempel ældre versioner af SDK returneres et svar objekt med en HTTP-statuskode, som du ofte ikke har brug for at kontrollere, fordi handlingen metoder Udløs `CloudException` for en hvilken som helst statuskode, der angiver en fejl. De nye lokalnummer metoder returnerer lige modelobjekter, så du ikke selv at fjerne dem i din kode.
 - Grundlæggende grænseflader derimod nu visning metoder, der giver dig bedre kontrol på niveauet for HTTP, hvis du vil have den. Du kan nu overfører i HTTP-headere skal medtages i anmodninger om og den nye `AzureOperationResponse<T>` returtypen giver dig direkte adgang til den `HttpRequestMessage` og `HttpResponseMessage` for handlingen. `AzureOperationResponse`er defineret i den `Microsoft.Rest.Azure` navneområde og erstatter `Hyak.Common.OperationResponse`.

#### <a name="scoringparameters-changes"></a>ScoringParameters ændringer

En ny klasse med navnet `ScoringParameter` er blevet tilføjet i de seneste SDK at gøre det nemmere at angive parametre til pointsystemet profiler i en søgeforespørgsel. Tidligere på `ScoringProfiles` egenskab for den `SearchParameters` klasse blev skrevet som `IList<string>`; Nu er skrevet som `IList<ScoringParameter>`.

##### <a name="example"></a>Eksempel

Hvis din kode ser sådan ud:

    var sp = new SearchParameters();
    sp.ScoringProfile = "jobsScoringFeatured";      // Use a scoring profile
    sp.ScoringParameters = new[] { "featuredParam-featured", "mapCenterParam-" + lon + "," + lat };

Du kan ændre det til denne indstilling for at løse eventuelle buildfejl: 

    var sp = new SearchParameters();
    sp.ScoringProfile = "jobsScoringFeatured";      // Use a scoring profile
    sp.ScoringParameters =
        new[]
        {
            new ScoringParameter("featuredParam", new[] { "featured" }),
            new ScoringParameter("mapCenterParam", GeographyPoint.Create(lat, lon))
        };

#### <a name="model-class-changes"></a>Model klasse ændringer

På grund af signaturen ændres beskrevet i [handlingen metode ændres](#OperationMethodChanges), mange klasser i den `Microsoft.Azure.Search.Models` navneområde er blevet omdøbt eller fjernet. Eksempel:

 - `IndexDefinitionResponse`er blevet erstattet af`AzureOperationResponse<Index>`
 - `DocumentSearchResponse`er blevet omdøbt til`DocumentSearchResult`
 - `IndexResult`er blevet omdøbt til`IndexingResult`
 - `Documents.Count()`Returnerer en `long` med dokumentantal i stedet for en`DocumentCountResponse`
 - `IndexGetStatisticsResponse`er blevet omdøbt til`IndexGetStatisticsResult`
 - `IndexListResponse`er blevet omdøbt til`IndexListResult`

Opsummere, `OperationResponse`-afledte klasser, som fandtes kun for at ombryde et objekt i modellen er blevet fjernet. De resterende klasser har haft deres suffiks, der er ændret fra `Response` til `Result`.

##### <a name="example"></a>Eksempel

Hvis din kode ser sådan ud:

    IndexerGetStatusResponse statusResponse = null;

    try
    {
        statusResponse = _searchClient.Indexers.GetStatus(indexer.Name);
    }
    catch (Exception ex)
    {
        Console.WriteLine("Error polling for indexer status: {0}", ex.Message);
        return;
    }

    IndexerExecutionResult lastResult = statusResponse.ExecutionInfo.LastResult;

Du kan ændre det til denne indstilling for at løse eventuelle buildfejl:

    IndexerExecutionInfo status = null;

    try
    {
        status = _searchClient.Indexers.GetStatus(indexer.Name);
    }
    catch (Exception ex)
    {
        Console.WriteLine("Error polling for indexer status: {0}", ex.Message);
        return;
    }

    IndexerExecutionResult lastResult = status.LastResult;

##### <a name="response-classes-and-ienumerable"></a>Svar klasser og IEnumerable

En ekstra ændring, der kan påvirke din kode er, at svar klasser, hold samlinger ikke længere implementere `IEnumerable<T>`. I stedet kan du åbne egenskaben samling direkte. For eksempel, hvis din kode ser sådan ud:

    DocumentSearchResponse<Hotel> response = indexClient.Documents.Search<Hotel>(searchText, sp);
    foreach (SearchResult<Hotel> result in response)
    {
        Console.WriteLine(result.Document);
    }

Du kan ændre det til denne indstilling for at løse eventuelle buildfejl:

    DocumentSearchResult<Hotel> response = indexClient.Documents.Search<Hotel>(searchText, sp);
    foreach (SearchResult<Hotel> result in response.Results)
    {
        Console.WriteLine(result.Document);
    }

##### <a name="important-note-for-web-applications"></a>Vigtig bemærkning til webprogrammer

Hvis du har et webprogram, der serializes `DocumentSearchResponse` direkte for at sende søgeresultater til browseren, skal du ændre din kode eller resultaterne vil ikke sekventielt korrekt. For eksempel, hvis din kode ser sådan ud:

    public ActionResult Search(string q = "")
    {
        // If blank search, assume they want to search everything
        if (string.IsNullOrWhiteSpace(q))
            q = "*";

        return new JsonResult
        {
            JsonRequestBehavior = JsonRequestBehavior.AllowGet,
            Data = _featuresSearch.Search(q)
        };
    }

Du kan ændre den ved at få den `.Results` egenskab for søgning svaret at løse Søg resultatet gengivelse:

    public ActionResult Search(string q = "")
    {
        // If blank search, assume they want to search everything
        if (string.IsNullOrWhiteSpace(q))
            q = "*";

        return new JsonResult
        {
            JsonRequestBehavior = JsonRequestBehavior.AllowGet,
            Data = _featuresSearch.Search(q).Results
        };
    }

Er du nødt til at søge efter disse tilfælde i din kode selv; **Compileren ikke advarer dig,** fordi `JsonResult.Data` er af typen `object`.

#### <a name="cloudexception-changes"></a>CloudException ændringer

Den `CloudException` klasse er flyttet fra den `Hyak.Common` navneområde til den `Microsoft.Rest.Azure` navneområde. Desuden dens `Error` egenskab er blevet omdøbt til `Body`.

#### <a name="searchserviceclient-and-searchindexclient-changes"></a>SearchServiceClient og SearchIndexClient ændringer

Typen den `Credentials` egenskab er ændret fra `SearchCredentials` til dens grundlæggende klasse `ServiceClientCredentials`. Hvis du har brug at få adgang til den `SearchCredentials` af en `SearchIndexClient` eller `SearchServiceClient`, skal du bruge den nye `SearchCredentials` egenskab.

I tidligere versioner af SDK, `SearchServiceClient` og `SearchIndexClient` havde konstruktører, der tog en `HttpClient` parameter. Disse er blevet erstattet med konstruktører, der kræver en `HttpClientHandler` og en matrix med `DelegatingHandler` objekter. Det gør det nemmere at installere brugerdefinerede programmer for at behandle allerede HTTP-anmodninger, hvis det er nødvendigt.

Til sidst skal de konstruktører, der tog en `Uri` og `SearchCredentials` er blevet ændret. For eksempel, hvis du har kode, der ser sådan ud:

    var client =
        new SearchServiceClient(
            new SearchCredentials("abc123"),
            new Uri("http://myservice.search.windows.net"));

Du kan ændre det til denne indstilling for at løse eventuelle buildfejl:

    var client =
        new SearchServiceClient(
            new Uri("http://myservice.search.windows.net"),
            new SearchCredentials("abc123"));

Også Bemærk, at typen af parameteren legitimationsoplysninger er blevet ændret til `ServiceClientCredentials`. Dette er sandsynligvis ikke vil påvirke din kode siden `SearchCredentials` er afledt af `ServiceClientCredentials`.

#### <a name="passing-a-request-id"></a>Overføre et anmodnings-ID

I ældre versioner af SDK, kan du angive et anmodnings-ID på den `SearchServiceClient` eller `SearchIndexClient` og den vil være inkluderet i hver anmodning REST-API. Dette er nyttigt til fejlfinding af problemer med din search-tjenesten, hvis du vil kontakte support. Det er dog mere praktisk til at angive et entydigt anmodnings-ID for hver operation i stedet for at bruge det samme ID for alle handlinger. Derfor den `SetClientRequestId` metoder til `SearchServiceClient` og `SearchIndexClient` er blevet fjernet. I stedet kan du overføre et anmodnings-ID til hver enkelt metode handlingen via det valgfri `SearchRequestOptions` parameter.

> [AZURE.NOTE] I en senere version af SDK, så vi vil tilføje en ny funktion til at angive et anmodnings-ID globalt på klienten objekter, der stemmer overens med den fremgangsmåde, der bruges af andre Azure SDK'er.

#### <a name="example"></a>Eksempel

Hvis du har kode, der ser sådan ud:

    client.SetClientRequestId(Guid.NewGuid());
    ...
    long count = client.Documents.Count();

Du kan ændre det til denne indstilling for at løse eventuelle buildfejl:

    long count = client.Documents.Count(new SearchRequestOptions(requestId: Guid.NewGuid()));

#### <a name="interface-name-changes"></a>Ændringer af brugergrænsefladen navn

Handlingen interface gruppenavne har ændret så den svarer til det tilsvarende egenskabsnavn:

 - Typen `ISearchServiceClient.Indexes` er blevet omdøbt fra `IIndexOperations` til `IIndexesOperations`.
 - Typen `ISearchServiceClient.Indexers` er blevet omdøbt fra `IIndexerOperations` til `IIndexersOperations`.
 - Typen `ISearchServiceClient.DataSources` er blevet omdøbt fra `IDataSourceOperations` til `IDataSourcesOperations`.
 - Typen `ISearchIndexClient.Documents` er blevet omdøbt fra `IDocumentOperations` til `IDocumentsOperations`.

Denne ændring vil sandsynligvis at påvirke din kode, medmindre du har oprettet mocks af disse grænseflader til testformål.

<a name="BugFixesV1"></a>
### <a name="bug-fixes-in-version-11"></a>Fejlrettelser i version 1.1

Der opstod en fejl i ældre versioner af Azure Søg .NET SDK relation til serialisering brugerdefinerede model klasser. Fejlen kan opstå, hvis du har oprettet en brugerdefineret model klasse med en egenskab for en ikke-null-værdi værditype.

#### <a name="steps-to-reproduce"></a>Trin til at genskabe

Oprette en brugerdefineret model klasse med en egenskab for ikke-null-værdi værditype. For eksempel tilføje en offentlig `UnitCount` egenskab af typen `int` i stedet for `int?`.

Hvis du indekserer et dokument med af standardværdien for typen (for eksempel, 0 for `int`), vil feltet være null i Azure Søg. Hvis du senere søge efter dokumentet, på `Search` opkald bliver Udløs `JsonSerializationException` vist meddelelsen, som den ikke kan konvertere `null` til `int`.

Desuden fungerer filtre muligvis ikke som forventet, da null blev skrevet til indekset i stedet for den ønskede værdi.

#### <a name="fix-details"></a>Løse detaljer

Vi har løst problemet i version 1.1 af SDK. Nu, hvis du har en model klasse således:

    public class Model
    {
        public string Key { get; set; }

        public int IntValue { get; set; }
    }

og du angive `IntValue` til 0, den pågældende værdi er nu korrekt serialiseret som 0 på kablet og gemmes som 0 i indekset. Afrund aktivering også fungerer som forventet.

Der er en potentiel problem skal være opmærksom på med denne metode: Hvis du bruger en modeltype med en ikke-null-værdi egenskab, du skal **sikre** , at ingen dokumenter i indekset indeholder en null-værdi til det tilsvarende felt. Hverken SDK eller Azure Search REST-API hjælper dig med at gennemtvinge dette.

Dette er ikke en hypotetiske problem: Forestil dig et scenarie, hvor du kan føje et nyt felt til et eksisterende indeks, der er af typen `Edm.Int32`. Når du har opdateret indeksdefinitionen, har alle dokumenter en null-værdi for det nye felt (da alle typer er null-værdi i Azure søgning). Hvis du derefter bruger en model klasse med en ikke-null-værdi `int` egenskab for det pågældende felt, får du en `JsonSerializationException` sådan ud, når du forsøger at hente dokumenter:

    Error converting value {null} to type 'System.Int32'. Path 'IntValue'.

Derfor stadig anbefales det, at du bruger null-værdi typer i din model klasser som en bedste fremgangsmåde.

Du kan finde flere oplysninger om denne fejl og fix [problemet på GitHub](https://github.com/Azure/azure-sdk-for-net/issues/1063).
