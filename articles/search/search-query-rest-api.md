<properties
    pageTitle="Forespørgsel din Azure søgeindekset ved hjælp af REST-API | Microsoft Azure | Hostet skyen search-tjenesten"
    description="Oprette en søgeforespørgsel i Azure Søg, og brug søgeparametre til at filtrere og sortere søgeresultaterne."
    services="search"
    documentationCenter=""
    manager="jhubbard"
    authors="ashmaka"
/>

<tags
    ms.service="search"
    ms.devlang="na"
    ms.workload="search"
    ms.topic="get-started-article"
    ms.tgt_pltfrm="na"
    ms.date="08/29/2016"
    ms.author="ashmaka"/>

# <a name="query-your-azure-search-index-using-the-rest-api"></a>Forespørgsel dit Azure søgeindeks ved hjælp af REST-API
> [AZURE.SELECTOR]
- [Oversigt](search-query-overview.md)
- [Portal](search-explorer.md)
- [.NET](search-query-dotnet.md)
- [RESTEN](search-query-rest-api.md)

I denne artikel viser dig, hvordan at forespørge på et indeks ved hjælp af [Azure Search REST-API](https://msdn.microsoft.com/library/azure/dn798935.aspx).

Før du begynder denne gennemgang, skal du allerede har [oprettet en Azure søgeindekset](search-what-is-an-index.md) og [udfyldes med data](search-what-is-data-import.md).

## <a name="i-identify-your-azure-search-services-query-api-key"></a>Jeg. Identificere dine Azure Search-tjenesten forespørgsel api-nøgle
En vigtig komponent i hver Søgehandling mod Azure Search REST-API er *api-nøgle* , der er oprettet for den tjeneste, du har klargjort. Hav tillid til, på grundlag per anmodning mellem programmet sende anmodningen og den tjeneste, der håndterer det fastlægger har en gyldig nøgle.

1. Hvis du vil finde din tjeneste api-taster skal du logge på [Azure-portalen](https://portal.azure.com/)
2. Gå til din Azure Search-tjenesten blade
3. Klik på ikonet "Taster"

Din tjeneste har *administrator nøgler* og *forespørgsel nøgler*.

 - Primære og sekundære *administrator taster* give fulde rettigheder til alle handlinger, herunder også muligheden for at administrere tjenesten, oprette og slette indeks, indeks og datakilder. Der findes to nøgler, så du kan fortsætte med at bruge en sekundær nøgle, hvis du beslutter dig for at genoprette den primære nøgle og omvendt.
 - Din *forespørgsel taster* give skrivebeskyttet adgang til indeks og dokumenter, og fordeles typisk til klientprogrammer, der udsteder search-anmodninger.

Du kan bruge en af dine forespørgsel nøgler med henblik på forespørgsler et indeks. Din administrator taster kan også bruges til forespørgsler, men du skal bruge en forespørgsel nøgle i din programkode, som dette bedre følger [princippet bag mindste tilladelse](https://en.wikipedia.org/wiki/Principle_of_least_privilege).

## <a name="ii-formulate-your-query"></a>II. Udforme din forespørgsel
Der er to måder at [søge ved hjælp af REST-API indekset](https://msdn.microsoft.com/library/azure/dn798927.aspx). En metode er at udsende en anmodning om en HTTP POST hvor din forespørgselsparametre defineres i et JSON objekt i anmodningsteksten. Anden vej er at udsende en HTTP GET-anmodning, hvor din forespørgselsparametre defineres i URL-anmodningen. Bemærk, at INDLÆG har flere [afslappet langs begrænsninger](https://msdn.microsoft.com/library/azure/dn798927.aspx) på størrelsen af forespørgselsparametre end få. Derfor anbefaler vi ved hjælp af INDLÆG, medmindre du har særlige tilfælde, hvor ved hjælp af GET ville være mere praktisk.

For både POST og GET, skal du angive dit *navn på tjeneste*, *indeksnavn*og den korrekte *API-version* (den aktuelle API-version er `2015-02-28` på tidspunktet for publicering af dette dokument) i URL-anmodningen. Til GET bliver *forespørgselsstreng* i slutningen af URL-adressen, hvor du kan angive parametrene til forespørgslen. Få nedenfor URL-format:

    https://[service name].search.windows.net/indexes/[index name]/docs?[query string]&api-version=2015-02-28

Formatet for INDLÆG er det samme, men med kun api-version i streng forespørgselsparametre.



#### <a name="example-queries"></a>Eksempel forespørgsler

Her er et par eksempel forespørgsler på et indeks med navnet "hoteller". Disse forespørgsler vises i både få og INDLÆG format.

Søge i hele indekset for ordet 'budget' og kun returnere de `hotelName` felt:

```
GET https://[service name].search.windows.net/indexes/hotels/docs?search=budget&$select=hotelName&api-version=2015-02-28

POST https://[service name].search.windows.net/indexes/hotels/docs/search?api-version=2015-02-28
{
    "search": "budget",
    "select": "hotelName"
}
```

Anvende et filter til indekset for at finde hoteller billigere end 150 per NAT. og returnere den `hotelId` og `description`:

```
GET https://[service name].search.windows.net/indexes/hotels/docs?search=*&$filter=baseRate lt 150&$select=hotelId,description&api-version=2015-02-28

POST https://[service name].search.windows.net/indexes/hotels/docs/search?api-version=2015-02-28
{
    "search": "*",
    "filter": "baseRate lt 150",
    "select": "hotelId,description"
}
```

Søge i hele indekset rækkefølge efter et bestemt felt (`lastRenovationDate`) tager to bedste resultaterne i faldende rækkefølge, og viser kun `hotelName` og `lastRenovationDate`:

```
GET https://[service name].search.windows.net/indexes/hotels/docs?search=*&$top=2&$orderby=lastRenovationDate desc&$select=hotelName,lastRenovationDate&api-version=2015-02-28

POST https://[service name].search.windows.net/indexes/hotels/docs/search?api-version=2015-02-28
{
    "search": "*",
    "orderby": "lastRenovationDate desc",
    "select": "hotelName,lastRenovationDate",
    "top": 2
}
```

## <a name="iii-submit-your-http-request"></a>III. Send din anmodning om HTTP
Nu hvor du har udarbejdet din forespørgsel som en del af din HTTP-anmodning om URL-adresse (for få) eller brødteksten (for INDLÆG), kan du definere din anmodning om sidehoveder og sende din forespørgsel.

#### <a name="request-and-request-headers"></a>Anmodning om og anmodning om sidehoveder
Du skal definere to anmodning om brevhoveder for GET eller tre for INDLÆG:
1. Den `api-key` sidehoved skal være indstillet til tasten forespørgsel, du fandt i trin jeg ovenfor. Bemærk, at du kan også bruge en administrator nøgle som den `api-key` sidehoved, men det anbefales, at du bruger en forespørgsel nøgle, som den udelukkende giver skrivebeskyttet adgang til indeks og dokumenter.
2. Den `Accept` sidehoved skal være indstillet til `application/json`.
3. For INDLÆG kun den `Content-Type` sidehoved bør også være indstillet til `application/json`.

Se nedenfor for en HTTP GET-anmodning til at søge på "hoteller" indeks ved hjælp af Azure Search REST API'ER, ved hjælp af en simpel forespørgsel, der søger efter ordet "motel":

```
GET https://[service name].search.windows.net/indexes/hotels/docs?search=motel&api-version=2015-02-28
Accept: application/json
api-key: [query key]
```

Her er den samme forespørgsel i eksempel denne tid ved hjælp af HTTP POST:

```
POST https://[service name].search.windows.net/indexes/hotels/docs/search?api-version=2015-02-28
Content-Type: application/json
Accept: application/json
api-key: [query key]

{
    "search": "motel"
}
```

En vellykket forespørgsel medfører en statuskode af `200 OK` og søgeresultaterne returneres som JSON i brødteksten svar. Her er, hvordan resultatet for den ovenstående forespørgsel til at ligne, hvis "hoteller" indeks er udfyldt med eksempeldata i [Dataimport i Azure søgning ved hjælp af REST-API](search-import-data-rest-api.md) (Bemærk, at der er formateret JSON for klarhed).

```JSON
{
    "value": [
        {
            "@search.score": 0.59600675,
            "hotelId": "2",
            "baseRate": 79.99,
            "description": "Cheapest hotel in town",
            "description_fr": "Hôtel le moins cher en ville",
            "hotelName": "Roach Motel",
            "category": "Budget",
            "tags":["motel", "budget"],
            "parkingIncluded": true,
            "smokingAllowed": true,
            "lastRenovationDate": "1982-04-28T00:00:00Z",
            "rating": 1,
            "location": {
                "type": "Point",
                "coordinates": [-122.131577, 49.678581],
                "crs": {
                    "type":"name",
                    "properties": {
                        "name": "EPSG:4326"
                    }
                }
            }
        }
    ]
}
```

Hvis du vil have mere for at vide, skal du gå til afsnittet "Svar" i [Søg i dokumenter](https://msdn.microsoft.com/library/azure/dn798927.aspx). Du kan finde flere oplysninger om andre HTTP statuskoder, der kan returneres i tilfælde af mislykket, [HTTP status-koder (Azure søgning)](https://msdn.microsoft.com/library/azure/dn798925.aspx).
