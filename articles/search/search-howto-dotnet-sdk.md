<properties
   pageTitle="Sådan bruger du Azure søgning fra et program i .NET | Microsoft Azure | Hostet skyen search-tjenesten"
   description="Sådan bruger du Azure søgning fra et .NET-program"
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
   ms.date="10/06/2016"
   ms.author="brjohnst"/>

# <a name="how-to-use-azure-search-from-a-net-application"></a>Sådan bruger du Azure søgning fra et .NET-program

I denne artikel er en gennemgang at komme i gang med [Azure Søg .NET SDK](https://msdn.microsoft.com/library/azure/dn951165.aspx). Du kan bruge .NET SDK for at implementere en omfattende søgeoplevelse i dit program ved hjælp af Azure søgning.

## <a name="whats-in-the-azure-search-sdk"></a>Hvad er i Azure søge SDK

SDK består af et klientbibliotek, `Microsoft.Azure.Search`. Det gør det muligt at administrere dit indeks, datakilder og indeks, samt overføre og administrere dokumenter og udføre forespørgsler, alle uden at håndtere oplysninger om HTTP og JSON.

Biblioteket klient definerer klasser som `Index`, `Field`, og `Document`, samt handlinger som `Indexes.Create` og `Documents.Search` på den `SearchServiceClient` og `SearchIndexClient` klasser. Disse klasser er organiseret i de følgende navneområder:

- [Microsoft.Azure.Search](https://msdn.microsoft.com/library/azure/microsoft.azure.search.aspx)
- [Microsoft.Azure.Search.Models](https://msdn.microsoft.com/library/azure/microsoft.azure.search.models.aspx)

Den aktuelle version af Azure Søg .NET SDK er nu alment tilgængelig. Hvis du vil give feedback til os til at inkorporere i den næste version, kan du besøge vores [feedback-side](https://feedback.azure.com/forums/263029-azure-search/).

.NET SDK understøtter version `2015-02-28` af Azure Search REST API'ER, dokumenteret på [MSDN](https://msdn.microsoft.com/library/azure/dn798935.aspx). Denne version omfatter nu understøttelse af Lucene forespørgselssyntaksen og programmer til Microsoft sprog analyse. Nyere funktioner, der er *ikke* en del af denne version, som understøttelse af den `moreLikeThis` søge efter parameter, er der i [Vis udskrift](search-api-2015-02-28-preview.md) og endnu ikke tilgængelig i SDK. Du kan kontrollere tilbage på [Search-tjenesten versionsstyring](https://msdn.microsoft.com/library/azure/dn864560.aspx) for statusopdateringer på enten funktion.

Andre funktioner, der ikke understøttes i denne SDK omfatter:

  - [Administration af handlinger](https://msdn.microsoft.com/library/azure/dn832684.aspx). Administration af handlinger omfatter klargøring Azure Søg tjenester og administrere API taster. Disse der understøttes i et separat Azure Søg .NET Management SDK i fremtiden.

## <a name="upgrading-to-the-latest-version-of-the-sdk"></a>Opgradere til den nyeste version af SDK

Hvis du allerede bruger en ældre version af Azure Søg .NET SDK, og du gerne vil opgradere til den nye alment tilgængelig version, [i denne artikel](search-dotnet-sdk-migration.md) forklares det, hvordan.

## <a name="requirements-for-the-sdk"></a>Krav til SDK

1. Visual Studio 2013 eller Visual Studio-2015.

2. Din egen Azure Search-tjenesten. For at kunne bruge SDK, skal du navnet på din tjeneste og en eller flere API taster. [Opret en tjeneste i portalen](search-create-service-portal.md) kan du følge disse trin.

3. Hent Azure Søg .NET SDK [NuGet pakke](http://www.nuget.org/packages/Microsoft.Azure.Search) ved hjælp af "Administrere NuGet pakker" i Visual Studio. Kun søge efter navnet på pakken `Microsoft.Azure.Search` på NuGet.org.

Azure Søg .NET SDK understøtter målretning af .NET Framework 4.5 samt Windows Store-apps målretning af Windows 8.1 og Windows Phone 8.1-programmer. Silverlight understøttes ikke.

## <a name="core-scenarios"></a>Core-scenarier

Der er flere ting, du skal gøre i søgeprogrammet på. I dette selvstudium gennemgår vi disse grundlæggende scenarier:

- Oprette et indeks
- Udfylde indekset med dokumenter
- Søge efter dokumenter ved hjælp af søgning i hele teksten og filtre

Eksempelkode, der følger efter viser hver enkelt af disse. Velkommen til at bruge kodestykker i dit eget program.

### <a name="overview"></a>Oversigt

Vi vil udforske programmet opretter et nyt indeks, der hedder "hoteller" udfylder det med nogle få dokumenter, og derefter udfører nogle søgeforespørgsler. Her er det primære program, der viser den samlede strøm:

    // This sample shows how to delete, create, upload documents and query an index
    static void Main(string[] args)
    {
        // Put your search service name here. This is the hostname portion of your service URL.
        // For example, if your service URL is https://myservice.search.windows.net, then your
        // service name is myservice.
        string searchServiceName = "myservice";

        string apiKey = "Put your API admin key here.";

        SearchServiceClient serviceClient = new SearchServiceClient(searchServiceName, new SearchCredentials(apiKey));

        Console.WriteLine("{0}", "Deleting index...\n");
        DeleteHotelsIndexIfExists(serviceClient);

        Console.WriteLine("{0}", "Creating index...\n");
        CreateHotelsIndex(serviceClient);

        ISearchIndexClient indexClient = serviceClient.Indexes.GetClient("hotels");

        Console.WriteLine("{0}", "Uploading documents...\n");
        UploadDocuments(indexClient);

        Console.WriteLine("{0}", "Searching documents 'fancy wifi'...\n");
        SearchDocuments(indexClient, searchText: "fancy wifi");

        Console.WriteLine("\n{0}", "Filter documents with category 'Luxury'...\n");
        SearchDocuments(indexClient, searchText: "*", filter: "category eq 'Luxury'");

        Console.WriteLine("{0}", "Complete.  Press any key to end application...\n");
        Console.ReadKey();
    }

Vi gennemgår dette trin for trin. Vi skal først oprette en ny `SearchServiceClient`. Dette objekt kan du administrere indeks. For at oprette en, skal du angive navnet på tjenesten din Azure søgning samt en administrator API-nøgle.

        // Put your search service name here. This is the hostname portion of your service URL.
        // For example, if your service URL is https://myservice.search.windows.net, then your
        // service name is myservice.
        string searchServiceName = "myservice";

        string apiKey = "Put your API admin key here.";

        SearchServiceClient serviceClient = new SearchServiceClient(searchServiceName, new SearchCredentials(apiKey));

> [AZURE.NOTE] Hvis du angiver en forkert nøgle (for eksempel en forespørgsel nøgle hvor en administrator nøgle var nødvendige), den `SearchServiceClient` vil udløse en `CloudException` med fejl meddelelse "Forbudt" første gang du kalder en handling metode på det, f.eks `Indexes.Create`. Hvis dette sker for dig, skal du kontrollere vores API-nøgle.

De næste par linjer ringe metoder til at oprette et indeks med navnet "hoteller", slette den første, hvis den findes allerede. Vi vil gennemgå disse metoder lidt senere.

        Console.WriteLine("{0}", "Deleting index...\n");
        DeleteHotelsIndexIfExists(serviceClient);

        Console.WriteLine("{0}", "Creating index...\n");
        CreateHotelsIndex(serviceClient);

Dernæst skal skal indekset udfyldes. For at gøre dette, skal vi bruge en `SearchIndexClient`. En på to måder: ved at oprette den eller ved at ringe til `Indexes.GetClient` på den `SearchServiceClient`. Vi bruger disse for nemmere.

        ISearchIndexClient indexClient = serviceClient.Indexes.GetClient("hotels");

> [AZURE.NOTE] I et typisk søgeprogram håndteres indeks administration og populationen af en separat komponent fra søgeforespørgsler. `Indexes.GetClient`er praktisk for at udfylde et indeks, fordi det sparer dig for at give en anden `SearchCredentials`. Det gør dette ved at overføre nøglen admin, som du brugte til at oprette den `SearchServiceClient` til den nye `SearchIndexClient`. Men i del af dit program, der udfører forespørgsler, er det bedre at oprette den `SearchIndexClient` direkte så kan du sende i en forespørgsel nøgle i stedet for en administrator-nøgle. Dette er i overensstemmelse med princippet bag mindste tilladelse og hjælper med at gøre programmet mere sikker. Du kan få mere at vide om administrator nøgler og forespørgsel nøgler [her](https://msdn.microsoft.com/library/azure/dn798935.aspx).

Nu hvor vi har en `SearchIndexClient`, vi kan udfylde indekset. Dette er udført af en anden afskrivningsmetode, som vi fører gennem senere.

        Console.WriteLine("{0}", "Uploading documents...\n");
        UploadDocuments(indexClient);

Til sidst skal vi udføre et par søgeforespørgsler og få vist resultaterne, igen ved hjælp af den `SearchIndexClient`:

        Console.WriteLine("{0}", "Searching documents 'fancy wifi'...\n");
        SearchDocuments(indexClient, searchText: "fancy wifi");

        Console.WriteLine("\n{0}", "Filter documents with category 'Luxury'...\n");
        SearchDocuments(indexClient, searchText: "*", filter: "category eq 'Luxury'");

        Console.WriteLine("{0}", "Complete.  Press any key to end application...\n");
        Console.ReadKey();

Hvis du kører dette program med en gyldig serviceaftale navn og API-nøgle, skal output se sådan ud:

    Deleting index...

    Creating index...

    Uploading documents...

    Searching documents 'fancy wifi'...

    ID: 1058-441    Name: Fancy Stay        Category: Luxury        Tags: [pool, view, concierge]
    ID: 956-532     Name: Express Rooms     Category: Budget        Tags: [wifi, budget]

    Filter documents with category 'Luxury'...

    ID: 1058-441    Name: Fancy Stay        Category: Luxury        Tags: [pool, view, concierge]
    ID: 566-518     Name: Surprisingly Expensive Suites     Category: Luxury    Tags: []
    Complete.  Press any key to end application...

Den fulde kildekode af programmet er angivet i slutningen af denne artikel.

Dernæst skal vi tager et nærmere Kig på hver af de metoder, der kaldes af `Main`.

### <a name="creating-an-index"></a>Oprette et indeks

Når du har oprettet en `SearchServiceClient`, næste mulighed `Main` betyder er Slet "hoteller" indeks, hvis den findes allerede. Der er udført af følgende metode:

    private static void DeleteHotelsIndexIfExists(SearchServiceClient serviceClient)
    {
        if (serviceClient.Indexes.Exists("hotels"))
        {
            serviceClient.Indexes.Delete("hotels");
        }
    }

Denne metode bruger den angivne `SearchServiceClient` for at kontrollere, om indekset findes, og hvis det er tilfældet, kan du slette den.

> [AZURE.NOTE] På eksempelkoden i denne artikel bruger synkron metoder til Azure Søg .NET SDK for enkel. Vi anbefaler, at du bruger de asynkrone metoder i dine egne programmer til at holde dem SVG og svarede. For eksempel i metoden ovenfor du kan bruge `ExistsAsync` og `DeleteAsync` i stedet for `Exists` og `Delete`.

Næste, `Main` opretter et nyt "hoteller" indeks ved at ringe til denne metode:

    private static void CreateHotelsIndex(SearchServiceClient serviceClient)
    {
        var definition = new Index()
        {
            Name = "hotels",
            Fields = new[]
            {
                new Field("hotelId", DataType.String)                       { IsKey = true },
                new Field("hotelName", DataType.String)                     { IsSearchable = true, IsFilterable = true },
                new Field("baseRate", DataType.Double)                      { IsFilterable = true, IsSortable = true },
                new Field("category", DataType.String)                      { IsSearchable = true, IsFilterable = true, IsSortable = true, IsFacetable = true },
                new Field("tags", DataType.Collection(DataType.String))     { IsSearchable = true, IsFilterable = true, IsFacetable = true },
                new Field("parkingIncluded", DataType.Boolean)              { IsFilterable = true, IsFacetable = true },
                new Field("lastRenovationDate", DataType.DateTimeOffset)    { IsFilterable = true, IsSortable = true, IsFacetable = true },
                new Field("rating", DataType.Int32)                         { IsFilterable = true, IsSortable = true, IsFacetable = true },
                new Field("location", DataType.GeographyPoint)              { IsFilterable = true, IsSortable = true }
            }
        };

        serviceClient.Indexes.Create(definition);
    }

Denne metode opretter en ny `Index` objekt med en liste over `Field` objekter, der definerer skemaet for det nye indeks. Hvert felt har et navn, datatype og flere attributter, der definerer funktionsmåden søgning. Ud over felter, kan du også tilføje vurdering profiler, suggesters eller CORS indstillinger til indekset (dette er udeladt i eksemplet af pladshensyn). Du kan finde flere oplysninger om objektet Index og de enkelte bestanddele i referencen SDK på [MSDN](https://msdn.microsoft.com/library/azure/microsoft.azure.search.models.index_members.aspx)og i [Azure Search REST-API reference](https://msdn.microsoft.com/library/azure/dn798935.aspx).

### <a name="populating-the-index"></a>Udfylde indekset

Næste trin i `Main` er til at udfylde nyoprettet indekset. Det gør du i følgende metode:

    private static void UploadDocuments(ISearchIndexClient indexClient)
    {
        var documents =
            new Hotel[]
            {
                new Hotel()
                {
                    HotelId = "1058-441",
                    HotelName = "Fancy Stay",
                    BaseRate = 199.0,
                    Category = "Luxury",
                    Tags = new[] { "pool", "view", "concierge" },
                    ParkingIncluded = false,
                    LastRenovationDate = new DateTimeOffset(2010, 6, 27, 0, 0, 0, TimeSpan.Zero),
                    Rating = 5,
                    Location = GeographyPoint.Create(47.678581, -122.131577)
                },
                new Hotel()
                {
                    HotelId = "666-437",
                    HotelName = "Roach Motel",
                    BaseRate = 79.99,
                    Category = "Budget",
                    Tags = new[] { "motel", "budget" },
                    ParkingIncluded = true,
                    LastRenovationDate = new DateTimeOffset(1982, 4, 28, 0, 0, 0, TimeSpan.Zero),
                    Rating = 1,
                    Location = GeographyPoint.Create(49.678581, -122.131577)
                },
                new Hotel()
                {
                    HotelId = "970-501",
                    HotelName = "Econo-Stay",
                    BaseRate = 129.99,
                    Category = "Budget",
                    Tags = new[] { "pool", "budget" },
                    ParkingIncluded = true,
                    LastRenovationDate = new DateTimeOffset(1995, 7, 1, 0, 0, 0, TimeSpan.Zero),
                    Rating = 4,
                    Location = GeographyPoint.Create(46.678581, -122.131577)
                },
                new Hotel()
                {
                    HotelId = "956-532",
                    HotelName = "Express Rooms",
                    BaseRate = 129.99,
                    Category = "Budget",
                    Tags = new[] { "wifi", "budget" },
                    ParkingIncluded = true,
                    LastRenovationDate = new DateTimeOffset(1995, 7, 1, 0, 0, 0, TimeSpan.Zero),
                    Rating = 4,
                    Location = GeographyPoint.Create(48.678581, -122.131577)
                },
                new Hotel()
                {
                    HotelId = "566-518",
                    HotelName = "Surprisingly Expensive Suites",
                    BaseRate = 279.99,
                    Category = "Luxury",
                    ParkingIncluded = false
                }
            };

        try
        {
            var batch = IndexBatch.Upload(documents);
            indexClient.Documents.Index(batch);
        }
        catch (IndexBatchException e)
        {
            // Sometimes when your Search service is under load, indexing will fail for some of the documents in
            // the batch. Depending on your application, you can take compensating actions like delaying and
            // retrying. For this simple demo, we just log the failed document keys and continue.
            Console.WriteLine(
                "Failed to index some of the documents: {0}",
                String.Join(", ", e.IndexingResults.Where(r => !r.Succeeded).Select(r => r.Key)));
        }

        // Wait a while for indexing to complete.
        Thread.Sleep(2000);
    }

Denne metode har fire dele. Først opretter en matrix med `Hotel` objekter, der fungerer som vores indtastede data for at overføre til indekset. Disse data er hårdt kodet til enkel. I dit eget program kommer sandsynligt dine data fra en ekstern datakilde som en SQL-database.

Den anden del opretter en `IndexBatch` der indeholder dokumenterne. Du angiver den handling, du vil anvende på batchen på det tidspunkt, du opretter, i dette tilfælde ved at ringe til `IndexBatch.Upload`. Batchen derefter er overført til Azure søgeindekset af den `Documents.Index` metode.

> [AZURE.NOTE] I dette eksempel vi blot overførsel af dokumenter. Hvis du vil flette ændringer i eksisterende dokumenter eller slette dokumenter, kan du oprette navne ved at ringe til `IndexBatch.Merge`, `IndexBatch.MergeOrUpload`, eller `IndexBatch.Delete` i stedet. Du kan også blande forskellige handlinger i en enkelt gruppe ved at ringe til `IndexBatch.New`, som tager en samling af `IndexAction` objekter, hver især fortæller Azure Søg til at udføre en bestemt handling på et dokument. Du kan oprette hver `IndexAction` med sin egen handling ved at ringe til den tilsvarende metode såsom `IndexAction.Merge`, `IndexAction.Upload`, og så videre.

Den tredje del af denne metode er en blok med produkter, der håndterer en vigtige fejl sag for indeksering. Hvis din Azure søgetjenesten mislykkes indeksere nogle af dokumenterne i batchen, en `IndexBatchException` er udløst af `Documents.Index`. Dette kan ske, hvis du indekserer dokumenter, mens din tjeneste er meget belastet. **Vi anbefaler eksplicit håndtering af dette tilfælde i din kode.** Du kan forsinke og prøv derefter igen indeksering de dokumenter, der mislykkedes, eller du kan logge og fortsætte som stikprøvernes fungerer, eller du kan gøre noget andet afhængigt af dit program data konsistens krav.

Til sidst skal metode forsinkelserne i to sekunder. Indeksering sker asynkront i din Azure Search-tjenesten, så eksempelprogrammet skal vente et øjeblik at sikre, at dokumenterne, der er tilgængelige til søgning. Forsinkelser således er typisk kun nødvendigt i demoer, test og eksempler på programmer.

#### <a name="how-the-net-sdk-handles-documents"></a>Hvordan .NET SDK håndterer dokumenter

Du måske spekulerer du over hvordan Azure Søg .NET SDK er kunne overføre forekomster af en brugerdefineret klasse som `Hotel` til indekset. For at besvare dette spørgsmål, Lad os se på de `Hotel` klasse:

    [SerializePropertyNamesAsCamelCase]
    public class Hotel
    {
        public string HotelId { get; set; }

        public string HotelName { get; set; }

        public double? BaseRate { get; set; }

        public string Category { get; set; }

        public string[] Tags { get; set; }

        public bool? ParkingIncluded { get; set; }

        public DateTimeOffset? LastRenovationDate { get; set; }

        public int? Rating { get; set; }

        public GeographyPoint Location { get; set; }

        public override string ToString()
        {
            return String.Format(
                "ID: {0}\tName: {1}\tCategory: {2}\tTags: [{3}]",
                HotelId,
                HotelName,
                Category,
                (Tags != null) ? String.Join(", ", Tags) : String.Empty);
        }
    }

Det første til Bemærk er, som hver offentlige egenskab for `Hotel` svarer til et felt i indeksdefinitionen af, men med en afgørende forskel: navnet på hvert felt starter med et små bogstav ("kameler sag"), mens du navnet på hver offentlige egenskab for `Hotel` starter med et bogstav med store bogstaver ("Pascal sag"). Dette er et almindelige scenarie i .NET-programmer, der udfører data binding hvor target skemaet er uden for kontrol af udvikleren af programmet. I stedet for at skulle overskride .NET navngive retningslinjer ved at gøre egenskaben navne kameler-sag, du kan se i SDK skal tilknyttes kameler små og store bogstaver automatisk med Egenskabsnavnene på `[SerializePropertyNamesAsCamelCase]` attributter.

> [AZURE.NOTE] Azure Søg .NET SDK bruger biblioteket [NewtonSoft JSON.NET](http://www.newtonsoft.com/json/help/html/Introduction.htm) sekventielt og deserialisere din objekter i brugerdefinerede modeller til og fra JSON. Hvis det er nødvendigt, kan du tilpasse denne serialisering. Du kan finde flere detaljer, [Brugerdefineret serialisering med JSON.NET](#JsonDotNet).
 
Den anden vigtig ting omkring den `Hotel` klasse er datatyperne for de offentlige egenskaber. .NET typer af disse egenskaber knyttes til deres tilsvarende felt datatyper i indeksdefinitionen af. For eksempel på `Category` streng egenskaben kort til den `category` felt, som er af typen `Edm.String`. Der er lignende type tilknytninger mellem `bool?` og `Edm.Boolean`, `DateTimeOffset?` og `Edm.DateTimeOffset`osv. De særlige regler for type tilknytningen er beskrevet med den `Documents.Get` metode på [MSDN](https://msdn.microsoft.com/library/azure/dn931291.aspx).

Denne mulighed for at bruge dit eget klasser som dokumenter fungerer i begge retninger Du kan også hente søgeresultater og have SDK automatisk deserialisere dem til en type efter eget valg, som vi vil se i næste afsnit.

> [AZURE.NOTE] Azure Søg .NET SDK understøtter også dynamisk skrevet dokumenter ved hjælp af den `Document` klasse, som er en nøgleværdi/tilknytning af feltnavne til feltværdier. Dette er nyttigt i situationer, hvor du ikke kender indeks skemaet på design tidspunkt, eller hvis det er upraktisk at bindes til specifikke klasser. Alle metoder i SDK, der omhandler dokumenter har overloads, der arbejder med den `Document` klasse samt kraftigt skrevet overloads, fører en generisk typeparameter. Kun disse bruges i eksempelkoden i dette selvstudium. Du kan få mere at vide om de `Document` klasse [her](https://msdn.microsoft.com/library/azure/microsoft.azure.search.models.document.aspx).

**En vigtig bemærkning om datatyper**

Når du designer dit eget model klasser skal tilknyttes en Azure søgeindekset, anbefales det erklære egenskaber for værdityper som `bool` og `int` skal være null-værdi (for eksempel `bool?` i stedet for `bool`). Hvis du bruger en ikke-null-værdi egenskab, skal du **sikre** , at ingen dokumenter i indekset indeholder en null-værdi til det tilsvarende felt. Hverken SDK eller Azure søgetjenesten hjælper dig med at gennemtvinge dette.

Dette er ikke en hypotetiske problem: Forestil dig et scenarie, hvor du kan føje et nyt felt til et eksisterende indeks, der er af typen `Edm.Int32`. Når du har opdateret indeksdefinitionen, har alle dokumenter en null-værdi for det nye felt (da alle typer er null-værdi i Azure søgning). Hvis du derefter bruger en model klasse med en ikke-null-værdi `int` egenskab for det pågældende felt, får du en `JsonSerializationException` sådan ud, når du forsøger at hente dokumenter:

    Error converting value {null} to type 'System.Int32'. Path 'IntValue'.

Derfor anbefaler vi, du bruger null-værdi typer i din model klasser som en bedste fremgangsmåde.

<a name="JsonDotNet"></a>
#### <a name="custom-serialization-with-jsonnet"></a>Brugerdefineret serialisering med JSON.NET

SDK bruger JSON.NET for serialisering og deserialisering af dokumenter. Du kan tilpasse serialisering og deserialization, hvis det er nødvendigt ved at definere dine egne `JsonConverter` eller `IContractResolver` (se [JSON.NET dokumentation](http://www.newtonsoft.com/json/help/html/Introduction.htm) få mere at vide). Det kan være praktiske, når du vil tilpasse en eksisterende model klasse fra dine programmer til brug med Azure Søg og andre mere avancerede scenarier. Med brugerdefinerede serialisering kan du f.eks.:

 - Medtage eller udelade bestemte egenskaber for klasse model i at blive gemt som dokumentfelter.
 - Knyt mellem egenskabsnavne i din kode og feltnavne i indekset.
 - Oprette brugerdefinerede attributter, der kan bruges til både tilknytte egenskaber til dokumentfelter samt oprettelse af den tilsvarende indeksdefinition.

Du kan finde eksempler på implementering af brugerdefineret serialisering i test af enhed til Azure Søg .NET SDK på GitHub. Et godt udgangspunkt er [denne mappe](https://github.com/Azure/azure-sdk-for-net/tree/AutoRest/src/Search/Search.Tests/Tests/Models). Den indeholder klasser, der anvendes af de brugerdefinerede serialisering test.

### <a name="searching-for-documents-in-the-index"></a>Søge efter dokumenter i indekset

Det sidste trin i eksempelprogrammet er at søge efter nogle dokumenter i indekset. Følgende metode gør dette:

    private static void SearchDocuments(ISearchIndexClient indexClient, string searchText, string filter = null)
    {
        // Execute search based on search text and optional filter
        var sp = new SearchParameters();

        if (!String.IsNullOrEmpty(filter))
        {
            sp.Filter = filter;
        }

        DocumentSearchResult<Hotel> response = indexClient.Documents.Search<Hotel>(searchText, sp);
        foreach (SearchResult<Hotel> result in response.Results)
        {
            Console.WriteLine(result.Document);
        }
    }

Først skal denne metode opretter en ny `SearchParameters` objekt. Det bruges til at angive yderligere indstillinger for forespørgslen som sortering, filtrering, sideopdeling og faceting. I dette eksempel vi kun angive den `Filter` egenskab.

Næste trin er at udføre faktisk søgestrengen. Dette gøres ved hjælp af den `Documents.Search` metode. I dette tilfælde overfører vi søgeteksten skal bruges som en streng, plus de søgeparametre oprettede tidligere. Vi også angive `Hotel` som typeparameteren for `Documents.Search`, som fortæller SDK at deserialisere dokumenter i søgeresultaterne i objekter af typen `Hotel`.

Denne metode gentager til sidst skal listen over alle forekomster i søgeresultaterne, udskrivning af hvert dokument til konsollen.

Lad os se nærmere på, hvordan denne metode kaldes:

    SearchDocuments(indexClient, searchText: "fancy wifi");

    SearchDocuments(indexClient, searchText: "*", filter: "category eq 'Luxury'");

Vi søger i det første opkald for alle dokumenter, der indeholder forespørgslen betingelserne "Smart" eller "wifi". I det andet opkald søgeteksten er indstillet til "*", hvilket betyder, at "finde alt". Du kan finde flere oplysninger om udtryk søgeforespørgselssyntaksen [her](https://msdn.microsoft.com/library/azure/dn798920.aspx).

Det andet opkald bruger et OData `$filter` udtryk, `category eq 'Luxury'`. Dette begrænser søgningen til kun at returnere dokumenter hvor den `category` felt svarer nøjagtigt til strengen "Luksus". Du kan få mere at vide om syntaksen OData, der understøtter Azure Søg [her](https://msdn.microsoft.com/library/azure/dn798921.aspx).

Nu hvor du ved, hvad disse to opkald gøre, skal det være nemmere at se, hvorfor deres output ser sådan ud:

    Searching documents 'fancy wifi'...

    ID: 1058-441    Name: Fancy Stay        Category: Luxury        Tags: [pool, view, concierge]
    ID: 956-532     Name: Express Rooms     Category: Budget        Tags: [wifi, budget]

    Filter documents with category 'Luxury'...

    ID: 1058-441    Name: Fancy Stay        Category: Luxury        Tags: [pool, view, concierge]
    ID: 566-518     Name: Surprisingly Expensive Suites     Category: Luxury    Tags: []

Den første søgning returnerer to dokumenter. Først har "Har lyst til at" i navnet, mens anden er "wifi" i den `tags` felt. Den anden søgning returnerer to dokumenter, der sker er de eneste dokumenter i indekset, der har den `category` felt, der er angivet til "Luksus".

Dette trin er fuldført selvstudiet, men stoppe ikke her. **Næste trin** giver yderligere ressourcer til at lære mere om Azure søgning.

## <a name="next-steps"></a>Næste trin

- Gennemse referencerne til [.NET SDK](https://msdn.microsoft.com/library/azure/dn951165.aspx) og [REST-API](https://msdn.microsoft.com/library/azure/dn798935.aspx) på MSDN.
- Derudover gennem [videoer og andre eksempler og selvstudier](search-video-demo-tutorial-list.md).
- Læs mere om funktioner og egenskaber i denne version af Azure Søg SDK: [Oversigt over Azure-søgning](https://msdn.microsoft.com/library/azure/dn798933.aspx)
- Gennemse [navngivningskonventioner](https://msdn.microsoft.com/library/azure/dn857353.aspx) for at lære om reglerne for navngivning af forskellige objekter.
- Gennemse [understøttede datatyper](https://msdn.microsoft.com/library/azure/dn798938.aspx) i Azure Søg.


## <a name="sample-application-source-code"></a>Eksempel på program-kildekode

Her er den fulde kildekode af eksempelprogrammet bruges i denne gennemgang. Bemærk, at du skal erstatte tjenestenavnet og API vigtige pladsholdere i Program.cs med dine egne værdier, hvis du vil oprette og køre eksemplet.

Program.CS:

```csharp
using System;
using System.Configuration;
using System.Linq;
using System.Threading;
using Microsoft.Azure.Search;
using Microsoft.Azure.Search.Models;
using Microsoft.Spatial;

namespace AzureSearch.SDKHowTo
{
    class Program
    {
        // This sample shows how to delete, create, upload documents and query an index
        static void Main(string[] args)
        {
            // Put your search service name here. This is the hostname portion of your service URL.
            // For example, if your service URL is https://myservice.search.windows.net, then your
            // service name is myservice.
            string searchServiceName = "myservice";

            string apiKey = "Put your API admin key here.";

            SearchServiceClient serviceClient = new SearchServiceClient(searchServiceName, new SearchCredentials(apiKey));

            Console.WriteLine("{0}", "Deleting index...\n");
            DeleteHotelsIndexIfExists(serviceClient);

            Console.WriteLine("{0}", "Creating index...\n");
            CreateHotelsIndex(serviceClient);

            ISearchIndexClient indexClient = serviceClient.Indexes.GetClient("hotels");

            Console.WriteLine("{0}", "Uploading documents...\n");
            UploadDocuments(indexClient);

            Console.WriteLine("{0}", "Searching documents 'fancy wifi'...\n");
            SearchDocuments(indexClient, searchText: "fancy wifi");

            Console.WriteLine("\n{0}", "Filter documents with category 'Luxury'...\n");
            SearchDocuments(indexClient, searchText: "*", filter: "category eq 'Luxury'");

            Console.WriteLine("{0}", "Complete.  Press any key to end application...\n");
            Console.ReadKey();
        }

        private static void DeleteHotelsIndexIfExists(SearchServiceClient serviceClient)
        {
            if (serviceClient.Indexes.Exists("hotels"))
            {
                serviceClient.Indexes.Delete("hotels");
            }
        }

        private static void CreateHotelsIndex(SearchServiceClient serviceClient)
        {
            var definition = new Index()
            {
                Name = "hotels",
                Fields = new[]
                {
                    new Field("hotelId", DataType.String)                       { IsKey = true },
                    new Field("hotelName", DataType.String)                     { IsSearchable = true, IsFilterable = true },
                    new Field("baseRate", DataType.Double)                      { IsFilterable = true, IsSortable = true },
                    new Field("category", DataType.String)                      { IsSearchable = true, IsFilterable = true, IsSortable = true, IsFacetable = true },
                    new Field("tags", DataType.Collection(DataType.String))     { IsSearchable = true, IsFilterable = true, IsFacetable = true },
                    new Field("parkingIncluded", DataType.Boolean)              { IsFilterable = true, IsFacetable = true },
                    new Field("lastRenovationDate", DataType.DateTimeOffset)    { IsFilterable = true, IsSortable = true, IsFacetable = true },
                    new Field("rating", DataType.Int32)                         { IsFilterable = true, IsSortable = true, IsFacetable = true },
                    new Field("location", DataType.GeographyPoint)              { IsFilterable = true, IsSortable = true }
                }
            };

            serviceClient.Indexes.Create(definition);
        }

        private static void UploadDocuments(ISearchIndexClient indexClient)
        {
            var documents =
                new Hotel[]
                {
                    new Hotel()
                    {
                        HotelId = "1058-441",
                        HotelName = "Fancy Stay",
                        BaseRate = 199.0,
                        Category = "Luxury",
                        Tags = new[] { "pool", "view", "concierge" },
                        ParkingIncluded = false,
                        LastRenovationDate = new DateTimeOffset(2010, 6, 27, 0, 0, 0, TimeSpan.Zero),
                        Rating = 5,
                        Location = GeographyPoint.Create(47.678581, -122.131577)
                    },
                    new Hotel()
                    {
                        HotelId = "666-437",
                        HotelName = "Roach Motel",
                        BaseRate = 79.99,
                        Category = "Budget",
                        Tags = new[] { "motel", "budget" },
                        ParkingIncluded = true,
                        LastRenovationDate = new DateTimeOffset(1982, 4, 28, 0, 0, 0, TimeSpan.Zero),
                        Rating = 1,
                        Location = GeographyPoint.Create(49.678581, -122.131577)
                    },
                    new Hotel()
                    {
                        HotelId = "970-501",
                        HotelName = "Econo-Stay",
                        BaseRate = 129.99,
                        Category = "Budget",
                        Tags = new[] { "pool", "budget" },
                        ParkingIncluded = true,
                        LastRenovationDate = new DateTimeOffset(1995, 7, 1, 0, 0, 0, TimeSpan.Zero),
                        Rating = 4,
                        Location = GeographyPoint.Create(46.678581, -122.131577)
                    },
                    new Hotel()
                    {
                        HotelId = "956-532",
                        HotelName = "Express Rooms",
                        BaseRate = 129.99,
                        Category = "Budget",
                        Tags = new[] { "wifi", "budget" },
                        ParkingIncluded = true,
                        LastRenovationDate = new DateTimeOffset(1995, 7, 1, 0, 0, 0, TimeSpan.Zero),
                        Rating = 4,
                        Location = GeographyPoint.Create(48.678581, -122.131577)
                    },
                    new Hotel()
                    {
                        HotelId = "566-518",
                        HotelName = "Surprisingly Expensive Suites",
                        BaseRate = 279.99,
                        Category = "Luxury",
                        ParkingIncluded = false
                    }
                };

            try
            {
                var batch = IndexBatch.Upload(documents);
                indexClient.Documents.Index(batch);
            }
            catch (IndexBatchException e)
            {
                // Sometimes when your Search service is under load, indexing will fail for some of the documents in
                // the batch. Depending on your application, you can take compensating actions like delaying and
                // retrying. For this simple demo, we just log the failed document keys and continue.
                Console.WriteLine(
                    "Failed to index some of the documents: {0}",
                    String.Join(", ", e.IndexingResults.Where(r => !r.Succeeded).Select(r => r.Key)));
            }

            // Wait a while for indexing to complete.
            Thread.Sleep(2000);
        }

        private static void SearchDocuments(ISearchIndexClient indexClient, string searchText, string filter = null)
        {
            // Execute search based on search text and optional filter
            var sp = new SearchParameters();

            if (!String.IsNullOrEmpty(filter))
            {
                sp.Filter = filter;
            }

            DocumentSearchResult<Hotel> response = indexClient.Documents.Search<Hotel>(searchText, sp);
            foreach (SearchResult<Hotel> result in response.Results)
            {
                Console.WriteLine(result.Document);
            }
        }
    }
}
```

Hotel.CS:

```csharp
using System;
using Microsoft.Azure.Search.Models;
using Microsoft.Spatial;

namespace AzureSearch.SDKHowTo
{
    [SerializePropertyNamesAsCamelCase]
    public class Hotel
    {
        public string HotelId { get; set; }

        public string HotelName { get; set; }

        public double? BaseRate { get; set; }

        public string Category { get; set; }

        public string[] Tags { get; set; }

        public bool? ParkingIncluded { get; set; }

        public DateTimeOffset? LastRenovationDate { get; set; }

        public int? Rating { get; set; }

        public GeographyPoint Location { get; set; }

        public override string ToString()
        {
            return String.Format(
                "ID: {0}\tName: {1}\tCategory: {2}\tTags: [{3}]",
                HotelId,
                HotelName,
                Category,
                (Tags != null) ? String.Join(", ", Tags) : String.Empty);
        }
    }
}
```
