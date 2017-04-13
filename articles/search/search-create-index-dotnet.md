<properties
    pageTitle="Oprette en Azure søgeindekset ved hjælp af .NET SDK | Microsoft Azure | Hostet skyen search-tjenesten"
    description="Oprette et indeks i kode ved hjælp af Azure Søg .NET SDK."
    services="search"
    documentationCenter=""
    authors="brjohnstmsft"
    manager="jhubbard"
    editor=""
    tags="azure-portal"/>

<tags
    ms.service="search"
    ms.devlang="dotnet"
    ms.workload="search"
    ms.topic="get-started-article"
    ms.tgt_pltfrm="na"
    ms.date="08/29/2016"
    ms.author="brjohnst"/>

# <a name="create-an-azure-search-index-using-the-net-sdk"></a>Oprette en Azure søgeindekset ved hjælp af .NET SDK
> [AZURE.SELECTOR]
- [Oversigt](search-what-is-an-index.md)
- [Portal](search-create-index-portal.md)
- [.NET](search-create-index-dotnet.md)
- [RESTEN](search-create-index-rest-api.md)


I denne artikel fører dig gennem processen med at oprette en Azure [indeks](https://msdn.microsoft.com/library/azure/dn798941.aspx) ved hjælp af [Azure Søg .NET SDK](https://msdn.microsoft.com/library/azure/dn951165.aspx).

Før følge denne vejledning og oprette et indeks, skal bør du allerede har [oprettet en Azure Search-tjenesten](search-create-service-portal.md).

Bemærk, at alle eksempelkode i denne artikel er skrevet i C#. Du kan finde den fulde kilde koden [på GitHub](http://aka.ms/search-dotnet-howto).

## <a name="i-identify-your-azure-search-services-admin-api-key"></a>Jeg. Identificere dine Azure Search-tjenesten administrator api-nøgle
Nu hvor du har klargjort en Azure Search-tjenesten, er du næsten klar til at udstede anmodninger på din tjenesteslutpunkt ved hjælp af .NET SDK. Du skal først hente en af de administrator api-taster, der er oprettet for søgetjenesten du klargjort. .NET SDK sender denne api-nøgle på hver anmodning til din tjeneste. Hav tillid til, på grundlag per anmodning mellem programmet sende anmodningen og den tjeneste, der håndterer det fastlægger har en gyldig nøgle.

1. Hvis du vil finde din tjeneste api-taster skal du logge på [Azure-portalen](https://portal.azure.com/)
2. Gå til din Azure Search-tjenesten blade
3. Klik på ikonet "Taster"

Din tjeneste har *administrator nøgler* og *forespørgsel nøgler*.

  - Primære og sekundære *administrator taster* give fulde rettigheder til alle handlinger, herunder også muligheden for at administrere tjenesten, oprette og slette indeks, indeks og datakilder. Der findes to nøgler, så du kan fortsætte med at bruge en sekundær nøgle, hvis du beslutter dig for at genoprette den primære nøgle og omvendt.
  - Din *forespørgsel taster* give skrivebeskyttet adgang til indeks og dokumenter, og fordeles typisk til klientprogrammer, der udsteder search-anmodninger.

I forbindelse med oprettelse af et indeks, du kan bruge enten produktnøglen primær eller sekundær administrator.

<a name="CreateSearchServiceClient"></a>
## <a name="ii-create-an-instance-of-the-searchserviceclient-class"></a>II. Oprette en forekomst af klassen SearchServiceClient
Hvis du vil begynde at bruge Azure Søg .NET SDK, skal du oprette en forekomst af den `SearchServiceClient` klasse. Denne klasse har flere konstruktører. Det, du vil tager din søgning service navn og en `SearchCredentials` objekt som parametre. `SearchCredentials`ombrydes api-nøgle.

Nedenstående kode opretter en ny `SearchServiceClient` ved hjælp af værdier for search-tjenestenavnet og api-nøgle, der er gemt i programmets konfigurationsfil (`app.config` eller `web.config`):

```csharp
string searchServiceName = ConfigurationManager.AppSettings["SearchServiceName"];
string adminApiKey = ConfigurationManager.AppSettings["SearchServiceAdminApiKey"];

SearchServiceClient serviceClient = new SearchServiceClient(searchServiceName, new SearchCredentials(adminApiKey));
```

`SearchServiceClient`har en `Indexes` egenskab. Denne egenskab giver alle de metoder, du har brug for at oprette, liste, opdatere eller slette Azure søgeindeks.

> [AZURE.NOTE] Den `SearchServiceClient` klasse administrerer forbindelser til search-tjenesten. For at undgå at åbne for mange forbindelser, skal du forsøger at dele en enkelt forekomst af `SearchServiceClient` i programmet, hvis det er muligt. Dens metoder er tråd sikker til at aktivere disse deling.

<a name="DefineIndex"></a>
## <a name="iii-define-your-azure-search-index-using-the-index-class"></a>III. Definere dine Azure Søg indeks ved hjælp af den `Index` klasse
Et enkelt opkald til den `Indexes.Create` metode opretter indekset. Denne metode tager som en parameter en `Index` objekt, der definerer din Azure søgeindekset. Du skal oprette en `Index` objekt og starte den på følgende måde:

1. Angiv den `Name` egenskab for den `Index` objekt til navnet på indekset.
2. Angiv den `Fields` egenskab for den `Index` objekt til en matrix med `Field` objekter. Hver af de `Field` objekter definerer adfærden for et felt i indekset. Du kan angive navnet på feltet, skal parametre, sammen med datatypen (eller analyzer til strengfelter). Du kan også angive andre egenskaber som `IsSearchable`, `IsFilterable`osv.

Det er vigtigt, at du holder din søgning brugernes oplevelse og business behov huske, når du designer dit indeks som hver `Field` skal tildeles de [relevante egenskaber](https://msdn.microsoft.com/library/azure/dn798941.aspx). Disse egenskaber kontrollere, hvilke søge funktioner (filtrering, faceting, sortering søgning i hele teksten, osv.) gælder for hvilke felter. For en hvilken som helst egenskab, du ikke eksplicit angiver, den `Field` klasse standardindstillinger for at deaktivere den tilsvarende søgefunktion, medmindre du specifikt aktiverer den.

I dette eksempel har vi navnet vores indeks "hoteller" og brugerdefinerede vores felter på følgende måde:

```csharp
var definition = new Index()
{
    Name = "hotels",
    Fields = new[]
    {
        new Field("hotelId", DataType.String)                       { IsKey = true, IsFilterable = true },
        new Field("baseRate", DataType.Double)                      { IsFilterable = true, IsSortable = true, IsFacetable = true },
        new Field("description", DataType.String)                   { IsSearchable = true },
        new Field("description_fr", AnalyzerName.FrLucene),
        new Field("hotelName", DataType.String)                     { IsSearchable = true, IsFilterable = true, IsSortable = true },
        new Field("category", DataType.String)                      { IsSearchable = true, IsFilterable = true, IsSortable = true, IsFacetable = true },
        new Field("tags", DataType.Collection(DataType.String))     { IsSearchable = true, IsFilterable = true, IsFacetable = true },
        new Field("parkingIncluded", DataType.Boolean)              { IsFilterable = true, IsFacetable = true },
        new Field("smokingAllowed", DataType.Boolean)               { IsFilterable = true, IsFacetable = true },
        new Field("lastRenovationDate", DataType.DateTimeOffset)    { IsFilterable = true, IsSortable = true, IsFacetable = true },
        new Field("rating", DataType.Int32)                         { IsFilterable = true, IsSortable = true, IsFacetable = true },
        new Field("location", DataType.GeographyPoint)              { IsFilterable = true, IsSortable = true }
    }
};
```

Vi har valgt egenskabsværdierne omhyggeligt for hver `Field` baseret på hvordan vi synes, at de skal bruges i et program. For eksempel er det sandsynligt, at personer søger efter hoteller er interesseret i nøgleord forekomster på den `description` felt, så vi aktivere søgning i hele teksten for det pågældende felt ved at angive `IsSearchable` til `true`.

Vær opmærksom på ét felt i indekset af typen `DataType.String` skal være angivet som feltet _nøgle_ ved at angive `IsKey` til `true` (se `hotelId` i eksemplet ovenfor).

Ovenstående indeksdefinition bruger et brugerdefineret sprog analyzer for den `description_fr` felt, da det er beregnet til at gemme fransk tekst. Se [sproget, der understøtter emne på MSDN](https://msdn.microsoft.com/library/azure/dn879793.aspx) samt den tilsvarende [blogindlæg](https://azure.microsoft.com/blog/language-support-in-azure-search/) for flere oplysninger om sprog programmer til analyse.

> [AZURE.NOTE]  Bemærk, at ved at overføre `AnalyzerName.FrLucene` i parametre, den `Field` bliver automatisk af typen `DataType.String` og de vil have `IsSearchable` indstillet til `true`.

## <a name="iv-create-the-index"></a>IV. Oprette indekset
Nu hvor du har en initialiseret `Index` objekt, du kan oprette indekset ved at ringe til `Indexes.Create` på din `SearchServiceClient` objekt:

```csharp
serviceClient.Indexes.Create(definition);
```

For en vellykket anmodning returnerer metoden normalt. Hvis der er et problem med anmodningen som en ugyldig parameter, metoden vil Udløs `CloudException`.

Når du er færdig med et indeks og ønsker at slette den, lige opkald på `Indexes.Delete` metode på din `SearchServiceClient`. Dette er for eksempel, hvordan vi vil slette indekset "hoteller":

```csharp
serviceClient.Indexes.Delete("hotels");
```

> [AZURE.NOTE] På eksempelkoden i denne artikel bruger synkron metoder til Azure Søg .NET SDK for enkel. Vi anbefaler, at du bruger de asynkrone metoder i dine egne programmer til at holde dem SVG og svarede. For eksempel i ovenstående eksempler du kan bruge `CreateAsync` og `DeleteAsync` i stedet for `Create` og `Delete`.

## <a name="next"></a>Næste
Når du har oprettet en Azure søgeindekset, vil du være klar til at [overføre dit indhold i indekset](search-what-is-data-import.md) så du kan begynde at søge efter dine data.
