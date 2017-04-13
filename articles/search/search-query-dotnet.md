<properties
    pageTitle="Forespørgsel din Azure søgeindekset ved hjælp af .NET SDK | Microsoft Azure | Hostet skyen search-tjenesten"
    description="Oprette en søgeforespørgsel i Azure Søg, og brug søgeparametre til at filtrere og sortere søgeresultaterne."
    services="search"
    manager="jhubbard"
    documentationCenter=""
    authors="brjohnstmsft"
/>

<tags
    ms.service="search"
    ms.devlang="dotnet"
    ms.workload="search"
    ms.topic="get-started-article"
    ms.tgt_pltfrm="na"
    ms.date="08/29/2016"
    ms.author="brjohnst"/>

# <a name="query-your-azure-search-index-using-the-net-sdk"></a>Forespørgsel dit Azure søgeindeks ved hjælp af .NET SDK
> [AZURE.SELECTOR]
- [Oversigt](search-query-overview.md)
- [Portal](search-explorer.md)
- [.NET](search-query-dotnet.md)
- [RESTEN](search-query-rest-api.md)

I denne artikel viser dig, hvordan at forespørge på et indeks ved hjælp af [Azure Søg .NET SDK](https://msdn.microsoft.com/library/azure/dn951165.aspx).

Før du begynder denne gennemgang, skal du allerede har [oprettet en Azure søgeindekset](search-what-is-an-index.md) og [udfyldes med data](search-what-is-data-import.md).

Bemærk, at alle eksempelkode i denne artikel er skrevet i C#. Du kan finde den fulde kilde koden [på GitHub](http://aka.ms/search-dotnet-howto).

## <a name="i-identify-your-azure-search-services-query-api-key"></a>Jeg. Identificere dine Azure Search-tjenesten forespørgsel api-nøgle
Nu hvor du har oprettet en Azure søgeindekset, er du næsten klar til at stille spørgsmål ved hjælp af .NET SDK. Du skal først hente en af de forespørgsel api-taster, der er oprettet for søgetjenesten du klargjort. .NET SDK sender denne api-nøgle på hver anmodning til din tjeneste. Hav tillid til, på grundlag per anmodning mellem programmet sende anmodningen og den tjeneste, der håndterer det fastlægger har en gyldig nøgle.

1. Hvis du vil finde din tjeneste api-taster skal du logge på [Azure-portalen](https://portal.azure.com/)
2. Gå til din Azure Search-tjenesten blade
3. Klik på ikonet "Taster"

Din tjeneste har *administrator nøgler* og *forespørgsel nøgler*.

  - Primære og sekundære *administrator taster* give fulde rettigheder til alle handlinger, herunder også muligheden for at administrere tjenesten, oprette og slette indeks, indeks og datakilder. Der findes to nøgler, så du kan fortsætte med at bruge en sekundær nøgle, hvis du beslutter dig for at genoprette den primære nøgle og omvendt.
  - Din *forespørgsel taster* give skrivebeskyttet adgang til indeks og dokumenter, og fordeles typisk til klientprogrammer, der udsteder search-anmodninger.

Du kan bruge en af dine forespørgsel nøgler med henblik på forespørgsler et indeks. Din administrator taster kan også bruges til forespørgsler, men du skal bruge en forespørgsel nøgle i din programkode, som dette bedre følger [princippet bag mindste tilladelse](https://en.wikipedia.org/wiki/Principle_of_least_privilege).

## <a name="ii-create-an-instance-of-the-searchindexclient-class"></a>II. Oprette en forekomst af klassen SearchIndexClient
For at udstede forespørgsler med Azure Søg .NET SDK, skal du oprette en forekomst af den `SearchIndexClient` klasse. Denne klasse har flere konstruktører. Det, du vil tager din søgning service navn, indeksnavn og en `SearchCredentials` objekt som parametre. `SearchCredentials`ombrydes api-nøgle.

Nedenstående kode opretter en ny `SearchIndexClient` "hoteller" indekset (oprettet i [oprette en Azure søgeindekset ved hjælp af .NET SDK](search-create-index-dotnet.md)) ved hjælp af værdier for search-tjenestenavnet og api-nøgle, der er gemt i programmets konfigurationsfil (`app.config` eller `web.config`):

```csharp
string searchServiceName = ConfigurationManager.AppSettings["SearchServiceName"];
string queryApiKey = ConfigurationManager.AppSettings["SearchServiceQueryApiKey"];

SearchIndexClient indexClient = new SearchIndexClient(searchServiceName, "hotels", new SearchCredentials(queryApiKey));
```

`SearchIndexClient`har en `Documents` egenskab. Denne egenskab giver alle de metoder, du har brug for at forespørge på Azure søgeindeks.

## <a name="iii-query-your-index"></a>III. Forespørgsel indekset
Søge med .NET SDK er så enkelt som opkald på `Documents.Search` metode på din `SearchIndexClient`. Denne metode tager et par parametre, herunder søgeteksten, sammen med en `SearchParameters` objekt, der kan bruges til at indskrænke forespørgslen yderligere.

#### <a name="types-of-queries"></a>Typer af forespørgsler
De to primære [forespørgselstyper](search-query-overview.md#types-of-queries) du oftest bruger er `search` og `filter`. A `search` forespørgsel søger efter en eller flere udtryk i _alle søgefelterne i indekset_ . A `filter` forespørgsel evaluerer et boolesk udtryk over alle _filtrerbare_ felter i et indeks.

Både søgninger og filtre udføres ved hjælp af den `Documents.Search` metode. En søgeforespørgsel kan overføres i den `searchText` parameter, mens et filterudtryk kan overføres i den `Filter` egenskab for den `SearchParameters` klasse. Hvis du vil filtrere uden at søge, kun overfører `"*"` for den `searchText` parameter. Hvis du vil søge efter uden filtrering, du skal bare lade den `Filter` egenskaben Fjern, eller ikke overføre i en `SearchParameters` forekomst overhovedet.

#### <a name="example-queries"></a>Eksempel forespørgsler

Følgende eksempelkode viser et par forskellige måder at forespørge på "hoteller" indeks, der er defineret i [oprette en Azure søgeindekset ved hjælp af .NET SDK](search-create-index-dotnet.md#DefineIndex). Bemærk, at de dokumenter, der er returneret med søgeresultaterne forekomster af det `Hotel` klasse, som blev defineret i [Dataimport i Azure søgning ved hjælp af .NET SDK](search-import-data-dotnet.md#HotelClass). Eksempelkoden gør brug af en `WriteDocuments` metode til at få vist søgeresultater til konsollen. Denne metode er beskrevet i næste afsnit.

```csharp
SearchParameters parameters;
DocumentSearchResult<Hotel> results;

Console.WriteLine("Search the entire index for the term 'budget' and return only the hotelName field:\n");

parameters =
    new SearchParameters()
    {
        Select = new[] { "hotelName" }
    };

results = indexClient.Documents.Search<Hotel>("budget", parameters);

WriteDocuments(results);

Console.Write("Apply a filter to the index to find hotels cheaper than $150 per night, ");
Console.WriteLine("and return the hotelId and description:\n");

parameters =
    new SearchParameters()
    {
        Filter = "baseRate lt 150",
        Select = new[] { "hotelId", "description" }
    };

results = indexClient.Documents.Search<Hotel>("*", parameters);

WriteDocuments(results);

Console.Write("Search the entire index, order by a specific field (lastRenovationDate) ");
Console.Write("in descending order, take the top two results, and show only hotelName and ");
Console.WriteLine("lastRenovationDate:\n");

parameters =
    new SearchParameters()
    {
        OrderBy = new[] { "lastRenovationDate desc" },
        Select = new[] { "hotelName", "lastRenovationDate" },
        Top = 2
    };

results = indexClient.Documents.Search<Hotel>("*", parameters);

WriteDocuments(results);

Console.WriteLine("Search the entire index for the term 'motel':\n");

parameters = new SearchParameters();
results = indexClient.Documents.Search<Hotel>("motel", parameters);

WriteDocuments(results);
```

## <a name="iv-handle-search-results"></a>IV. Håndtere søgeresultater
Den `Documents.Search` metode returnerer en `DocumentSearchResult` objekt, der indeholder resultatet af forespørgslen. Eksemplet ovenfor bruges en metode med navnet `WriteDocuments` til at få vist søgeresultaterne for at konsollen:

```csharp
private static void WriteDocuments(DocumentSearchResult<Hotel> searchResults)
{
    foreach (SearchResult<Hotel> result in searchResults.Results)
    {
        Console.WriteLine(result.Document);
    }

    Console.WriteLine();
}
```

Her er, hvordan resultatet ser ud for forespørgsler i det forrige afsnit, hvis "hoteller" indeks er udfyldt med eksempeldata i [Dataimport i Azure søgning ved hjælp af .NET SDK](search-import-data-dotnet.md):

```
Search the entire index for the term 'budget' and return only the hotelName field:

Name: Roach Motel

Apply a filter to the index to find hotels cheaper than $150 per night, and return the hotelId and description:

ID: 2   Description: Cheapest hotel in town
ID: 3   Description: Close to town hall and the river

Search the entire index, order by a specific field (lastRenovationDate) in descending order, take the top two results, and show only hotelName and lastRenovationDate:

Name: Fancy Stay        Last renovated on: 6/27/2010 12:00:00 AM +00:00
Name: Roach Motel       Last renovated on: 4/28/1982 12:00:00 AM +00:00

Search the entire index for the term 'motel':

ID: 2   Base rate: 79.99        Description: Cheapest hotel in town     Description (French): Hôtel le moins cher en ville      Name: Roach Motel       Category: Budget        Tags: [motel, budget]   Parking included: yes   Smoking allowed: yes    Last renovated on: 4/28/1982 12:00:00 AM +00:00 Rating: 1/5     Location: Latitude 49.678581, longitude -122.131577

```

Ovenstående eksempelkode bruger konsollen til at få vist søgeresultater. Du skal på samme måde at vise søgeresultater i dit eget program. Se [dette eksempel på GitHub](https://github.com/Azure-Samples/search-dotnet-getting-started/tree/master/DotNetSample) for et eksempel på, hvordan til at gengive søgeresultater i en ASP.NET MVC-baserede web application.
