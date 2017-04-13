<properties
    pageTitle="Data overføre Azure søgning ved hjælp af .NET SDK | Microsoft Azure | Hostet skyen search-tjenesten"
    description="Lær at overføre data til et indeks i Azure søgning ved hjælp af .NET SDK."
    services="search"
    documentationCenter=""
    authors="brjohnstmsft"
    manager="jhubbard"
    editor=""
    tags=""/>

<tags
    ms.service="search"
    ms.devlang="dotnet"
    ms.workload="search"
    ms.topic="get-started-article"
    ms.tgt_pltfrm="na"
    ms.date="08/29/2016"
    ms.author="brjohnst"/>

# <a name="upload-data-to-azure-search-using-the-net-sdk"></a>Overføre data til Azure søgning ved hjælp af .NET SDK
> [AZURE.SELECTOR]
- [Oversigt](search-what-is-data-import.md)
- [.NET](search-import-data-dotnet.md)
- [RESTEN](search-import-data-rest-api.md)

I denne artikel viser dig, hvordan du bruger [Azure Søg .NET SDK](https://msdn.microsoft.com/library/azure/dn951165.aspx) til at importere data til en Azure søgeindekset.

Før du begynder denne gennemgang, skal du allerede har [oprettet en Azure søgeindekset](search-what-is-an-index.md). I denne artikel antages det også, at du allerede har oprettet en `SearchServiceClient` objekt, som vist i [oprette en Azure søgeindekset ved hjælp af .NET SDK](search-create-index-dotnet.md#CreateSearchServiceClient).

Bemærk, at alle eksempelkode i denne artikel er skrevet i C#. Du kan finde den fulde kilde koden [på GitHub](http://aka.ms/search-dotnet-howto).

For at overføre dokumenter til dit indeks ved hjælp af .NET SDK, skal du:

  1. Oprette en `SearchIndexClient` objekt til at oprette forbindelse til dit søgeindeks.
  2. Oprette en `IndexBatch` der indeholder dokumenterne, der er tilføjet, ændret eller slettet.
  3. Ringe til den `Documents.Index` metode til din `SearchIndexClient` til at sende den `IndexBatch` til dit søgeindeks.

## <a name="i-create-an-instance-of-the-searchindexclient-class"></a>Jeg. Oprette en forekomst af klassen SearchIndexClient
Hvis du vil importere data til dit indeks ved hjælp af Azure Søg .NET SDK, skal du oprette en forekomst af den `SearchIndexClient` klasse. Du kan oprette denne forekomst dig selv, men det er nemmere Hvis du allerede har en `SearchServiceClient` forekomst til at ringe til dens `Indexes.GetClient` metode. For eksempel her er, hvordan du vil hente en `SearchIndexClient` for indekset med navnet "hoteller" fra en `SearchServiceClient` med navnet `serviceClient`:

```csharp
SearchIndexClient indexClient = serviceClient.Indexes.GetClient("hotels");
```

> [AZURE.NOTE] I et typisk søgeprogram håndteres indeks administration og populationen af en separat komponent fra søgeforespørgsler. `Indexes.GetClient`er praktisk for at udfylde et indeks, fordi det sparer dig for at give en anden `SearchCredentials`. Det gør dette ved at overføre nøglen admin, som du brugte til at oprette den `SearchServiceClient` til den nye `SearchIndexClient`. Men i del af dit program, der udfører forespørgsler, er det bedre at oprette den `SearchIndexClient` direkte så kan du sende i en forespørgsel nøgle i stedet for en administrator-nøgle. Dette er i overensstemmelse med [princippet bag mindste tilladelse](https://en.wikipedia.org/wiki/Principle_of_least_privilege) og hjælper med at gøre programmet mere sikker. Du kan få mere at vide om administrator nøgler og forespørgsel nøgler i [Azure Search REST-API reference på MSDN](https://msdn.microsoft.com/library/azure/dn798935.aspx).

`SearchIndexClient`har en `Documents` egenskab. Denne egenskab giver alle de metoder, du vil tilføje, ændre, slette eller forespørgsel dokumenter i indekset.

## <a name="ii-decide-which-indexing-action-to-use"></a>II. Beslutte, hvilke indeksering handling, der skal bruge
Hvis du vil importere data ved hjælp af .NET SDK, skal du pakke af dine data i en `IndexBatch` objekt. En `IndexBatch` indkapsler en samling af `IndexAction` objekter, hver især omfatter et dokument og en egenskab, der fortæller Azure søgning, hvad du skal udføre på det pågældende dokument (Overfør, Flet, Slet osv.). Afhængigt af hvilken af de under handlinger du vælger, kun nogle af felterne skal medtages for hvert dokument:

Handling | Beskrivelse | Det er nødvendigt felter for hvert dokument | Noter
--- | --- | --- | ---
`Upload` | En `Upload` handling, der ligner en "upsert" hvor dokumentet skal indsættes, hvis den er ny og opdateret/erstattet Hvis den findes. | tast samt eventuelle andre felter, du vil angive | Når du opdaterer/erstatter et eksisterende dokument, en hvilken som helst felt, der ikke er angivet i anmodningen får det felt, der er angivet til `null`. Dette sker, selvom feltet tidligere var angivet til en ikke-null-værdi.
`Merge` | Opdaterer et eksisterende dokument med de angivne felter. Hvis dokumentet ikke findes i indekset, mislykkes fletningen. | tast samt eventuelle andre felter, du vil angive | Et felt, du angiver i en fletning erstatter det eksisterende felt i dokumentet. Dette omfatter felter af typen `DataType.Collection(DataType.String)`. For eksempel, hvis dokumentet indeholder et felt `tags` med værdi `["budget"]` og du udfører en brevfletning med værdi `["economy", "pool"]` for `tags`, den sidste værdi af den `tags` vil feltet være `["economy", "pool"]`. Det kan ikke `["budget", "economy", "pool"]`.
`MergeOrUpload` | Denne handling opfører sig som `Merge` Hvis et dokument med den givne nøgle allerede findes i indekset. Hvis dokumentet ikke findes, det opfører sig som `Upload` med et nyt dokument. | tast samt eventuelle andre felter, du vil angive | -
`Delete` | Fjerner det angivne dokument fra indekset. | kun nøgle | Eventuelle felter, du angiver bortset fra det nøglefelt, blive ignoreret. Hvis du vil fjerne et enkelt felt fra et dokument, skal du bruge `Merge` i stedet og blot du indstille feltet eksplicit til null.

Du kan angive, hvilken handling, du vil bruge med de forskellige statiske metoder til den `IndexBatch` og `IndexAction` klasser, som vist i næste afsnit.

## <a name="iii-construct-your-indexbatch"></a>III. Oprette din IndexBatch
Nu hvor du ved, hvilke handlinger til at udføre på dine dokumenter, du er klar til at oprette den `IndexBatch`. I eksemplet nedenfor viser, hvordan du opretter en gruppe med et par forskellige handlinger. Bemærk, at vores eksempel bruger en brugerdefineret klasse kaldet `Hotel` , der er tilknyttet et dokument i "hoteller" indekset.

```csharp
var actions =
    new IndexAction<Hotel>[]
    {
        IndexAction.Upload(
            new Hotel()
            {
                HotelId = "1",
                BaseRate = 199.0,
                Description = "Best hotel in town",
                DescriptionFr = "Meilleur hôtel en ville",
                HotelName = "Fancy Stay",
                Category = "Luxury",
                Tags = new[] { "pool", "view", "wifi", "concierge" },
                ParkingIncluded = false,
                SmokingAllowed = false,
                LastRenovationDate = new DateTimeOffset(2010, 6, 27, 0, 0, 0, TimeSpan.Zero),
                Rating = 5,
                Location = GeographyPoint.Create(47.678581, -122.131577)
            }),
        IndexAction.Upload(
            new Hotel()
            {
                HotelId = "2",
                BaseRate = 79.99,
                Description = "Cheapest hotel in town",
                DescriptionFr = "Hôtel le moins cher en ville",
                HotelName = "Roach Motel",
                Category = "Budget",
                Tags = new[] { "motel", "budget" },
                ParkingIncluded = true,
                SmokingAllowed = true,
                LastRenovationDate = new DateTimeOffset(1982, 4, 28, 0, 0, 0, TimeSpan.Zero),
                Rating = 1,
                Location = GeographyPoint.Create(49.678581, -122.131577)
            }),
        IndexAction.MergeOrUpload(
            new Hotel()
            {
                HotelId = "3",
                BaseRate = 129.99,
                Description = "Close to town hall and the river"
            }),
        IndexAction.Delete(new Hotel() { HotelId = "6" })
    };

var batch = IndexBatch.New(actions);
```

I dette tilfælde vi bruger `Upload`, `MergeOrUpload`, og `Delete` som vores søgning handlinger, som angivet af metoderne med navnet på den `IndexAction` klasse.

Forudsætter, at dette eksempel "hoteller" indeks allerede er udfyldt med et antal dokumenter. Bemærk hvordan vi ikke har til at angive alle de mulige dokumentfelter, når du bruger `MergeOrUpload` , og hvordan vi kun angivet nøglen dokument (`HotelId`) når du bruger `Delete`.

Bemærk også, at du kun kan medtage op til 1000 dokumenter i en enkelt indeksering anmodning.

> [AZURE.NOTE] I dette eksempel vi anvende forskellige handlinger på forskellige dokumenter. Hvis du vil udføre de samme handlinger på tværs af alle dokumenter i batchen, i stedet for opkald `IndexBatch.New`, kan du bruge de andre statiske metoder af `IndexBatch`. For eksempel du kunne oprette navne ved at ringe til `IndexBatch.Merge`, `IndexBatch.MergeOrUpload`, eller `IndexBatch.Delete`. Disse metoder have en samling af dokumenter (objekter af typen `Hotel` i dette eksempel) i stedet for `IndexAction` objekter.

## <a name="iv-import-data-to-the-index"></a>IV. Importere data til indekset
Nu hvor du har en initialiseret `IndexBatch` objekt, du kan sende det til indekset ved at ringe til `Documents.Index` på din `SearchIndexClient` objekt. Følgende eksempel viser, hvordan du kan ringe til `Index`, samt som nogle ekstra trin, du skal udføre:

```csharp
try
{
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

Console.WriteLine("Waiting for documents to be indexed...\n");
Thread.Sleep(2000);
```

Bemærk de `try` / `catch` omkring opkaldet til den `Index` metode. Produkter blokering håndterer en vigtige fejl sag for indeksering. Hvis din Azure søgetjenesten mislykkes indeksere nogle af dokumenterne i batchen, en `IndexBatchException` er udløst af `Documents.Index`. Dette kan ske, hvis du indekserer dokumenter, mens din tjeneste er meget belastet. **Vi anbefaler eksplicit håndtering af dette tilfælde i din kode.** Du kan forsinke og prøv derefter igen indeksering de dokumenter, der mislykkedes, eller du kan logge og fortsætte som stikprøvernes fungerer, eller du kan gøre noget andet afhængigt af dit program data konsistens krav.

Til sidst skal kode i eksemplet ovenfor forsinkelser i to sekunder. Indeksering sker asynkront i din Azure Search-tjenesten, så eksempelprogrammet skal vente et øjeblik at sikre, at dokumenterne, der er tilgængelige til søgning. Forsinkelser således er typisk kun nødvendigt i demoer, test og eksempler på programmer.

<a name="HotelClass"></a>
### <a name="how-the-net-sdk-handles-documents"></a>Hvordan .NET SDK håndterer dokumenter

Du måske spekulerer du over hvordan Azure Søg .NET SDK er kunne overføre forekomster af en brugerdefineret klasse som `Hotel` til indekset. For at besvare dette spørgsmål, Lad os se på de `Hotel` klasse, som er tilknyttet indeks-skema, der er defineret i [oprette en Azure søgeindekset ved hjælp af .NET SDK](search-create-index-dotnet.md#DefineIndex):

```csharp
[SerializePropertyNamesAsCamelCase]
public partial class Hotel
{
    public string HotelId { get; set; }

    public double? BaseRate { get; set; }

    public string Description { get; set; }

    [JsonProperty("description_fr")]
    public string DescriptionFr { get; set; }

    public string HotelName { get; set; }

    public string Category { get; set; }

    public string[] Tags { get; set; }

    public bool? ParkingIncluded { get; set; }

    public bool? SmokingAllowed { get; set; }

    public DateTimeOffset? LastRenovationDate { get; set; }

    public int? Rating { get; set; }

    public GeographyPoint Location { get; set; }

    // ToString() method omitted for brevity...
}
```

Det første til Bemærk er, som hver offentlige egenskab for `Hotel` svarer til et felt i indeksdefinitionen af, men med en afgørende forskel: navnet på hvert felt starter med et små bogstav ("kameler sag"), mens du navnet på hver offentlige egenskab for `Hotel` starter med et bogstav med store bogstaver ("Pascal sag"). Dette er et almindelige scenarie i .NET-programmer, der udfører data binding hvor target skemaet er uden for kontrol af udvikleren af programmet. I stedet for at skulle overskride .NET navngive retningslinjer ved at gøre egenskaben navne kameler-sag, du kan se i SDK skal tilknyttes kameler små og store bogstaver automatisk med Egenskabsnavnene på `[SerializePropertyNamesAsCamelCase]` attributter.

> [AZURE.NOTE] Azure Søg .NET SDK bruger biblioteket [NewtonSoft JSON.NET](http://www.newtonsoft.com/json/help/html/Introduction.htm) sekventielt og deserialisere din objekter i brugerdefinerede modeller til og fra JSON. Hvis det er nødvendigt, kan du tilpasse denne serialisering. Du kan finde flere oplysninger i [opgradering til Azure Søg .NET SDK version 1.1](search-dotnet-sdk-migration.md#WhatsNew). Et eksempel på dette er brugen af den `[JsonProperty]` attributten i den `DescriptionFr` egenskab i ovenstående eksempelkode.

Den anden vigtig ting omkring den `Hotel` klasse er datatyperne for de offentlige egenskaber. .NET typer af disse egenskaber knyttes til deres tilsvarende felt datatyper i indeksdefinitionen af. For eksempel på `Category` streng egenskaben kort til den `category` felt, som er af typen `DataType.String`. Der er lignende type tilknytninger mellem `bool?` og `DataType.Boolean`, `DateTimeOffset?` og `DataType.DateTimeOffset`osv. De særlige regler for type tilknytningen er beskrevet med den `Documents.Get` metode på [MSDN](https://msdn.microsoft.com/library/azure/dn931291.aspx).

Denne mulighed for at bruge dit eget klasser som dokumenter fungerer i begge retninger Du kan også hente søgeresultater og have SDK automatisk deserialisere dem til en type efter eget valg, som vist i [Næste artikel](search-query-dotnet.md).

> [AZURE.NOTE] Azure Søg .NET SDK understøtter også dynamisk skrevet dokumenter ved hjælp af den `Document` klasse, som er en nøgleværdi/tilknytning af feltnavne til feltværdier. Dette er nyttigt i situationer, hvor du ikke kender indeks skemaet på design tidspunkt, eller hvis det er upraktisk at bindes til specifikke klasser. Alle metoder i SDK, håndtere dokumenter har overloads, der arbejder med den `Document` klasse samt kraftigt skrevet overloads, fører en generisk typeparameter. Kun disse bruges i eksempelkoden i denne artikel. Du kan få mere at vide om de `Document` klasse [på MSDN](https://msdn.microsoft.com/library/azure/microsoft.azure.search.models.document.aspx).

**En vigtig bemærkning om datatyper**

Når du designer dit eget model klasser skal tilknyttes en Azure søgeindekset, anbefales det erklære egenskaber for værdityper som `bool` og `int` skal være null-værdi (for eksempel `bool?` i stedet for `bool`). Hvis du bruger en ikke-null-værdi egenskab, skal du **sikre** , at ingen dokumenter i indekset indeholder en null-værdi til det tilsvarende felt. Hverken SDK eller Azure søgetjenesten hjælper dig med at gennemtvinge dette.

Dette er ikke en hypotetiske problem: Forestil dig et scenarie, hvor du kan føje et nyt felt til et eksisterende indeks, der er af typen `DataType.Int32`. Når du har opdateret indeksdefinitionen, har alle dokumenter en null-værdi for det nye felt (da alle typer er null-værdi i Azure søgning). Hvis du derefter bruger en model klasse med en ikke-null-værdi `int` egenskab for det pågældende felt, får du en `JsonSerializationException` sådan ud, når du forsøger at hente dokumenter:

    Error converting value {null} to type 'System.Int32'. Path 'IntValue'.

Derfor anbefaler vi, at du bruger null-værdi typer i din model klasser som en bedste fremgangsmåde.

## <a name="next"></a>Næste
Når du har udfyldt din Azure søgeindekset, bliver du klar til at starte udstedelse forespørgsler til at søge efter dokumenter. Du kan finde oplysninger i [Forespørgsel dine Azure-søgeindekset](search-query-overview.md) .
