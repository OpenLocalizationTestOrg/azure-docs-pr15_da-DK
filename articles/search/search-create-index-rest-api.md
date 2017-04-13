<properties
    pageTitle="Oprette en Azure søgeindekset ved hjælp af REST-API | Microsoft Azure | Hostet skyen search-tjenesten"
    description="Oprette et indeks i kode ved hjælp af Azure Søg HTTP REST-API."
    services="search"
    documentationCenter=""
    authors="ashmaka"
    manager="jhubbard"
    editor=""
    tags="azure-portal"/>

<tags
    ms.service="search"
    ms.devlang="rest-api"
    ms.workload="search"
    ms.topic="get-started-article"
    ms.tgt_pltfrm="na"
    ms.date="08/29/2016"
    ms.author="ashmaka"/>

# <a name="create-an-azure-search-index-using-the-rest-api"></a>Oprette en Azure søgeindekset ved hjælp af REST-API
> [AZURE.SELECTOR]
- [Oversigt](search-what-is-an-index.md)
- [Portal](search-create-index-portal.md)
- [.NET](search-create-index-dotnet.md)
- [RESTEN](search-create-index-rest-api.md)


I denne artikel fører dig gennem processen med at oprette en Azure [indeks](https://msdn.microsoft.com/library/azure/dn798941.aspx) ved hjælp af Azure Search REST-API.

Før følge denne vejledning og oprette et indeks, skal bør du allerede har [oprettet en Azure Search-tjenesten](search-create-service-portal.md).

For at oprette en Azure søgeindekset ved hjælp af REST-API, vil du udstede en enkelt HTTP POST-anmodning til din Azure Search-tjenesten URL-adressen slutpunkt. Indeksdefinitionen for din skal indgå i anmodningsteksten som korrekt udformet JSON-indhold.


## <a name="i-identify-your-azure-search-services-admin-api-key"></a>Jeg. Identificere dine Azure Search-tjenesten administrator api-nøgle
Nu hvor du har klargjort en Azure Search-tjenesten, kan du udstede HTTP-anmodninger mod din tjeneste URL-adressen slutpunktet ved hjælp af REST-API. Men *alle* API anmodninger skal indeholde den api-nøgle, der blev oprettet til søgetjenesten du klargjort. Hav tillid til, på grundlag per anmodning mellem programmet sende anmodningen og den tjeneste, der håndterer det fastlægger har en gyldig nøgle.

1. Hvis du vil finde din tjeneste api-taster skal du logge på [Azure-portalen](https://portal.azure.com/)
2. Gå til din Azure Search-tjenesten blade
3. Klik på ikonet "Taster"

Din tjeneste har *administrator nøgler* og *forespørgsel nøgler*.

 - Primære og sekundære *administrator taster* give fulde rettigheder til alle handlinger, herunder også muligheden for at administrere tjenesten, oprette og slette indeks, indeks og datakilder. Der findes to nøgler, så du kan fortsætte med at bruge en sekundær nøgle, hvis du beslutter dig for at genoprette den primære nøgle og omvendt.
 - Din *forespørgsel taster* give skrivebeskyttet adgang til indeks og dokumenter, og fordeles typisk til klientprogrammer, der udsteder search-anmodninger.

I forbindelse med oprettelse af et indeks, du kan bruge enten produktnøglen primær eller sekundær administrator.

## <a name="ii-define-your-azure-search-index-using-well-formed-json"></a>II. Definere dine Azure søgeindekset ved hjælp af korrekt udformet JSON
En enkelt HTTP POST-anmodning til din tjeneste skal oprette indekset. Brødteksten i HTTP POST anmodningen skal indeholde et enkelt JSON-objekt, der definerer din Azure søgeindekset.

1. Den første egenskab for dette JSON objekt er navnet på indekset.
2. Den anden egenskab for dette JSON objekt er en JSON-matrix med navnet `fields` , der indeholder et separat JSON-objekt for hvert felt i indekset. Hver af disse JSON objekter indeholder flere navne/værdipar for hvert af de feltattributter, herunder "navn", "type"; osv.

Det er vigtigt, at du holder din søgning brugernes oplevelse og business behov huske, når du designer dit indeks, som hvert felt skal være tildelt de [korrekte attributter](https://msdn.microsoft.com/library/azure/dn798941.aspx). Disse attributter kontrollere, hvilke søge funktioner (filtrering, faceting, sortering søgning i hele teksten, osv.) gælder for hvilke felter. Standard bliver til en hvilken som helst attribut, du ikke angiver, at aktivere tilsvarende søgefunktionen, medmindre du slår den.

I dette eksempel har vi navnet vores indeks "hoteller" og brugerdefinerede vores felter på følgende måde:

```JSON
{
    "name": "hotels",  
    "fields": [
        {"name": "hotelId", "type": "Edm.String", "key": true, "searchable": false, "sortable": false, "facetable": false},
        {"name": "baseRate", "type": "Edm.Double"},
        {"name": "description", "type": "Edm.String", "filterable": false, "sortable": false, "facetable": false},
        {"name": "description_fr", "type": "Edm.String", "filterable": false, "sortable": false, "facetable": false, "analyzer": "fr.lucene"},
        {"name": "hotelName", "type": "Edm.String", "facetable": false},
        {"name": "category", "type": "Edm.String"},
        {"name": "tags", "type": "Collection(Edm.String)"},
        {"name": "parkingIncluded", "type": "Edm.Boolean", "sortable": false},
        {"name": "smokingAllowed", "type": "Edm.Boolean", "sortable": false},
        {"name": "lastRenovationDate", "type": "Edm.DateTimeOffset"},
        {"name": "rating", "type": "Edm.Int32"},
        {"name": "location", "type": "Edm.GeographyPoint"}
    ]
}
```

Vi har omhyggeligt valgt indeksattributter for hvert felt, der er baseret på hvordan vi synes, at de skal bruges i et program. For eksempel `hotelId` vises en entydig nøgle, som personer, søgning efter hoteller sandsynligvis ikke ved, så vi deaktivere søgning i hele teksten for det pågældende felt ved at angive `searchable` til `false`, som gemmer plads i indekset.

Vær opmærksom på ét felt i indekset af typen `Edm.String` skal være angivet som feltet 'key'.

Ovenstående indeksdefinition bruger et brugerdefineret sprog analyzer for den `description_fr` felt, da det er beregnet til at gemme fransk tekst. Se [sproget, der understøtter emne på MSDN](https://msdn.microsoft.com/library/azure/dn879793.aspx) samt den tilsvarende [blogindlæg](https://azure.microsoft.com/blog/language-support-in-azure-search/) for flere oplysninger om sprog programmer til analyse.

## <a name="iii-issue-the-http-request"></a>III. Problem med HTTP-anmodningen
1. Brug af indeksdefinitionen for din som anmodningsteksten, udstede anmodning om en HTTP POST til dit Azure Søg slutpunkt URL-adressen. URL-adressen, skal du sørge for at bruge tjenestenavnet på din som værtsnavn og placere korrekte `api-version` som en forespørgselsparameter streng (den aktuelle API-version er `2015-02-28` på tidspunktet for publicering af dette dokument).
2. Angiv i overskrifterne anmodning på `Content-Type` som `application/json`. Du skal også give din tjeneste administrator nøgle, som du identificerede i trin I i den `api-key` sidehoved.


Du skal angive dine egne service navn og api nøgle for at udstede anmodningen nedenfor:


    POST https://[service name].search.windows.net/indexes?api-version=2015-02-28
    Content-Type: application/json
    api-key: [api-key]


For en vellykket anmodning, skal du får vist statuskode 201 (oprettet). Du kan finde flere oplysninger om oprettelse af et indeks via REST-API, skal du gå til API reference på [MSDN](https://msdn.microsoft.com/library/azure/dn798941.aspx). Du kan finde flere oplysninger om andre HTTP statuskoder, der kan returneres i tilfælde af mislykket, [HTTP status-koder (Azure søgning)](https://msdn.microsoft.com/library/azure/dn798925.aspx).

Når du er færdig med et indeks og ønsker at slette den, lige udstede en HTTP slette anmodning. Dette er for eksempel, hvordan vi vil slette indekset "hoteller":

    DELETE https://[service name].search.windows.net/indexes/hotels?api-version=2015-02-28
    api-key: [api-key]


## <a name="next"></a>Næste
Når du har oprettet en Azure søgeindekset, vil du være klar til at [overføre dit indhold i indekset](search-what-is-data-import.md) så du kan begynde at søge efter dine data.
