<properties
    pageTitle="Data overføre Azure søgning ved hjælp af REST-API | Microsoft Azure | Hostet skyen search-tjenesten"
    description="Lær at overføre data til et indeks i Azure søgning ved hjælp af REST-API."
    services="search"
    documentationCenter=""
    authors="ashmaka"
    manager="jhubbard"
    editor=""
    tags=""/>

<tags
    ms.service="search"
    ms.devlang="rest-api"
    ms.workload="search"
    ms.topic="get-started-article"
    ms.tgt_pltfrm="na"
    ms.date="08/29/2016"
    ms.author="ashmaka"/>

# <a name="upload-data-to-azure-search-using-the-rest-api"></a>Overføre data til Azure søgning ved hjælp af REST-API
> [AZURE.SELECTOR]
- [Oversigt](search-what-is-data-import.md)
- [.NET](search-import-data-dotnet.md)
- [RESTEN](search-import-data-rest-api.md)

I denne artikel viser dig, hvordan du bruger [Azure Search REST-API](https://msdn.microsoft.com/library/azure/dn798935.aspx) til at importere data til en Azure søgeindekset.

Før du begynder denne gennemgang, skal du allerede har [oprettet en Azure søgeindekset](search-what-is-an-index.md).

For at overføre dokumenter til dit indeks ved hjælp af REST-API, vil du udstede anmodning om en HTTP POST til dit indeks URL-adressen slutpunkt. Brødteksten i HTTP-anmodningsteksten er et JSON-objekt, der indeholder dokumenterne for at blive tilføjet, ændret eller slettet.

## <a name="i-identify-your-azure-search-services-admin-api-key"></a>Jeg. Identificere dine Azure Search-tjenesten administrator api-nøgle
Når du udsteder HTTP-anmodninger mod tjenesten ved hjælp af REST-API, skal *hver* API anmodning medtage den api-nøgle, der blev oprettet til søgetjenesten du klargjort. Hav tillid til, på grundlag per anmodning mellem programmet sende anmodningen og den tjeneste, der håndterer det fastlægger har en gyldig nøgle.

1. Hvis du vil finde din tjeneste api-taster skal du logge på [Azure-portalen](https://portal.azure.com/)
2. Gå til din Azure Search-tjenesten blade
3. Klik på ikonet "Taster"

Din tjeneste har *administrator nøgler* og *forespørgsel nøgler*.

  - Primære og sekundære *administrator taster* give fulde rettigheder til alle handlinger, herunder også muligheden for at administrere tjenesten, oprette og slette indeks, indeks og datakilder. Der findes to nøgler, så du kan fortsætte med at bruge en sekundær nøgle, hvis du beslutter dig for at genoprette den primære nøgle og omvendt.
  - Din *forespørgsel taster* give skrivebeskyttet adgang til indeks og dokumenter, og fordeles typisk til klientprogrammer, der udsteder search-anmodninger.

Med henblik på at importere data til et indeks kan du bruge enten produktnøglen primær eller sekundær administrator.

## <a name="ii-decide-which-indexing-action-to-use"></a>II. Beslutte, hvilke indeksering handling, der skal bruge
Når du bruger REST-API, kan du udstede HTTP POST-anmodninger med JSON anmodning organisationer til din Azure Søg indeks slutpunkt URL-adresse. Objektet JSON i HTTP-anmodningsteksten skal indeholde en enkelt JSON-matrix med navnet "værdi", som indeholder JSON objekter, der repræsenterer dokumenter, du vil føje til dit indeks, Opdater eller Slet.

Hvert JSON objekt i matrixen "værdi" repræsenterer et dokument, der skal indekseres. Hver af disse objekter indeholder dokumentets nøgle og angiver den ønskede indeksering handling (Overfør, Flet, Slet osv.). Afhængigt af hvilken af de under handlinger du vælger, kun nogle af felterne skal medtages for hvert dokument:

@search.action | Beskrivelse | Det er nødvendigt felter for hvert dokument | Noter
--- | --- | --- | ---
`upload` | En `upload` handling, der ligner en "upsert" hvor dokumentet skal indsættes, hvis den er ny og opdateret/erstattet Hvis den findes. | tast samt eventuelle andre felter, du vil angive | Når du opdaterer/erstatter et eksisterende dokument, en hvilken som helst felt, der ikke er angivet i anmodningen får det felt, der er angivet til `null`. Dette sker, selvom feltet tidligere var angivet til en ikke-null-værdi.
`merge` | Opdaterer et eksisterende dokument med de angivne felter. Hvis dokumentet ikke findes i indekset, mislykkes fletningen. | tast samt eventuelle andre felter, du vil angive | Et felt, du angiver i en fletning erstatter det eksisterende felt i dokumentet. Dette omfatter felter af typen `Collection(Edm.String)`. For eksempel, hvis dokumentet indeholder et felt `tags` med værdi `["budget"]` og du udfører en brevfletning med værdi `["economy", "pool"]` for `tags`, den sidste værdi af den `tags` vil feltet være `["economy", "pool"]`. Det kan ikke `["budget", "economy", "pool"]`.
`mergeOrUpload` | Denne handling opfører sig som `merge` Hvis et dokument med den givne nøgle allerede findes i indekset. Hvis dokumentet ikke findes, det opfører sig som `upload` med et nyt dokument. | tast samt eventuelle andre felter, du vil angive | -
`delete` | Fjerner det angivne dokument fra indekset. | kun nøgle | Eventuelle felter, du angiver bortset fra det nøglefelt, blive ignoreret. Hvis du vil fjerne et enkelt felt fra et dokument, skal du bruge `merge` i stedet og blot du indstille feltet eksplicit til null.

## <a name="iii-construct-your-http-request-and-request-body"></a>III. Oprette en HTTP-anmodningen og anmodningsteksten
Nu hvor du har indsamlet de nødvendige feltværdier for dine indeks handlinger, er du klar til at oprette de faktiske HTTP-anmodningen og JSON anmodningsteksten for at importere dine data.

#### <a name="request-and-request-headers"></a>Anmodning om og anmodning om sidehoveder
URL-adressen, skal du angive tjenestenavnet på din, indeksnavn ("hoteller" i dette tilfælde) samt den korrekte API-version (den aktuelle API-version er `2015-02-28` på tidspunktet for publicering af dette dokument). Du skal definere den `Content-Type` og `api-key` anmode om sidehoveder. Brug én af din tjeneste administrator taster for disse.

    POST https://[search service].search.windows.net/indexes/hotels/docs/index?api-version=2015-02-28
    Content-Type: application/json
    api-key: [admin key]

#### <a name="request-body"></a>Anmodningsteksten

```JSON
{
    "value": [
        {
            "@search.action": "upload",
            "hotelId": "1",
            "baseRate": 199.0,
            "description": "Best hotel in town",
            "description_fr": "Meilleur hôtel en ville",
            "hotelName": "Fancy Stay",
            "category": "Luxury",
            "tags": ["pool", "view", "wifi", "concierge"],
            "parkingIncluded": false,
            "smokingAllowed": false,
            "lastRenovationDate": "2010-06-27T00:00:00Z",
            "rating": 5,
            "location": { "type": "Point", "coordinates": [-122.131577, 47.678581] }
        },
        {
            "@search.action": "upload",
            "hotelId": "2",
            "baseRate": 79.99,
            "description": "Cheapest hotel in town",
            "description_fr": "Hôtel le moins cher en ville",
            "hotelName": "Roach Motel",
            "category": "Budget",
            "tags": ["motel", "budget"],
            "parkingIncluded": true,
            "smokingAllowed": true,
            "lastRenovationDate": "1982-04-28T00:00:00Z",
            "rating": 1,
            "location": { "type": "Point", "coordinates": [-122.131577, 49.678581] }
        },
        {
            "@search.action": "mergeOrUpload",
            "hotelId": "3",
            "baseRate": 129.99,
            "description": "Close to town hall and the river"
        },
        {
            "@search.action": "delete",
            "hotelId": "6"
        }
    ]
}
```

I dette tilfælde vi bruger `upload`, `mergeOrUpload`, og `delete` som vores søgning handlinger.

Forudsætter, at dette eksempel "hoteller" indeks allerede er udfyldt med et antal dokumenter. Bemærk hvordan vi ikke har til at angive alle de mulige dokumentfelter, når du bruger `mergeOrUpload` , og hvordan vi kun angivet nøglen dokument (`hotelId`) når du bruger `delete`.

Bemærk også, at du kun kan medtage op til 1000 dokumenter (eller 16 MB) i en enkelt indeksering anmodning.

## <a name="iv-understand-your-http-response-code"></a>IV. Forstå din HTTP-svarkode
#### <a name="200"></a>200
Når du har sendt en vellykket indeksering anmodning, du modtager en HTTP-svar med statuskode af `200 OK`. HTTP-svaret JSON meddelelsens bliver på følgende måde:

```JSON
{
    "value": [
        {
            "key": "unique_key_of_document",
            "status": true,
            "errorMessage": null
        },
        ...
    ]
}
```

#### <a name="207"></a>207
En statuskode af `207` der returneres, når mindst ét element ikke blev indekseret. HTTP-svaret JSON brødteksten indeholder oplysninger om de mislykkede dokumenter.

```JSON
{
    "value": [
        {
            "key": "unique_key_of_document",
            "status": false,
            "errorMessage": "The search service is too busy to process this document. Please try again later."
        },
        ...
    ]
}
```

> [AZURE.NOTE] Det betyder ofte, at belastningen på search-tjenesten er at kontakte et punkt, hvor indeksering anmodninger begynder at returnere `503` svar. I dette tilfælde vi det anbefales, at din klientkode tilbage fra, og vent før du prøver igen. Dette giver systemet stykke tid til at gendanne, øge risikoen, fremtidige anmodninger lykkes. Hurtigt forsøg på din anmodninger vil forlænge situationen kun.

#### <a name="429"></a>429
En statuskode af `429` der returneres, når du har overskredet kvoten for antallet af dokumenter i indekset.

#### <a name="503"></a>503
En statuskode af `503` der skal returneres, hvis ingen af elementerne i anmodningen er blevet indekseret. Fejlen betyder, at systemet er meget belastet og din anmodning kan ikke behandles på nuværende tidspunkt.

> [AZURE.NOTE] I dette tilfælde vi det anbefales, at din klientkode tilbage fra, og vent før du prøver igen. Dette giver systemet stykke tid til at gendanne, øge risikoen, fremtidige anmodninger lykkes. Hurtigt forsøg på din anmodninger vil forlænge situationen kun.

Yderligere oplysninger om Dokumenthandlinger og succes/fejl svar, skal du se [tilføje, opdatere, eller Slet dokumenter](https://msdn.microsoft.com/library/azure/dn798930.aspx). Du kan finde flere oplysninger om andre HTTP statuskoder, der kan returneres i tilfælde af mislykket, [HTTP status-koder (Azure søgning)](https://msdn.microsoft.com/library/azure/dn798925.aspx).

## <a name="next"></a>Næste
Når du har udfyldt din Azure søgeindekset, bliver du klar til at starte udstedelse forespørgsler til at søge efter dokumenter. Du kan finde oplysninger i [Forespørgsel dine Azure-søgeindekset](search-query-overview.md) .
