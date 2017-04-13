<properties
    pageTitle="Sådan bruges Fiddler til at evaluere og teste Azure Search REST API'er | Microsoft Azure | Hostet skyen search-tjenesten"
    description="Du kan bruge Fiddler for en kode ledig fremgangsmåde til at bekræfte Azure Søg i tilgængelighed og prøver REST API'er."
    services="search"
    documentationCenter=""
    authors="HeidiSteen"
    manager="mblythe"
    editor=""/>

<tags
    ms.service="search"
    ms.devlang="rest-api"
    ms.workload="search"
    ms.topic="get-started-article"
    ms.tgt_pltfrm="na"
    ms.date="10/17/2016"
    ms.author="heidist"/>

# <a name="use-fiddler-to-evaluate-and-test-azure-search-rest-apis"></a>Brug Fiddler til at evaluere og teste Azure Search REST API'er
> [AZURE.SELECTOR]
- [Oversigt](search-query-overview.md)
- [Search Explorer](search-explorer.md)
- [Fiddler](search-fiddler.md)
- [.NET](search-query-dotnet.md)
- [RESTEN](search-query-rest-api.md)

I denne artikel beskrives, hvordan du bruger Fiddler, [hentes gratis fra Telerik](http://www.telerik.com/fiddler), problem HTTP-anmodninger til, og Vis svar ved hjælp af Azure Search REST-API, uden at skrive en programkode. Azure Søg-administreres fuldt og hostet skyen søgetjenesten på Microsoft Azure, nemt programmeres via .NET og REST API'er. Azure søgetjenesten REST API'er er beskrevet på [MSDN](https://msdn.microsoft.com/library/azure/dn798935.aspx).

I de følgende trin, skal du oprette et indeks, overføre dokumenter, forespørge indekset og derefter forespørgsel systemet for oplysninger om tjenesten.

For at fuldføre disse trin, du skal bruge en Azure Search-tjenesten og `api-key`. Se [oprette en Azure søgetjenesten i portalen](search-create-service-portal.md) for oplysninger om, hvordan du kommer i gang.

## <a name="create-an-index"></a>Oprette et indeks

1. Start Fiddler. Deaktiver **Registrere trafik** til at skjule overflødige HTTP aktivitet, der er ikke-relaterede til den aktuelle opgave i menuen **filer** .

3. Under fanen **Komponist** får du udforme en anmodning, der ligner følgende skærmbillede.

    ![][1]

2. Vælg **PLACERE**.

3. Angiv en URL-adresse, der angiver URL-adressen, anmodning om attributter og api-version. Et par oplysninger skal være opmærksom på:
   + Bruge HTTPS som præfiks.
   + Anmode om attributten er "/ indeks/hoteller". Det fortæller Søg til at oprette et indeks med navnet 'hoteller'.
   + API-version er små bogstaver, angivet som "? api-version = 2015-02-28". API versioner er vigtige, fordi Azure Søg installerer opdateringer jævnligt. I sjældne tilfælde kan kan en tjenesteopdatering til medføre en seneste ændring til API. Derfor kræver Azure Søg en api-version på hver enkelt anmodning, så du er i fuld kontrol over hvilken af skærmene bruges.

    Den fulde URL-adresse skal ligne eksemplet nedenfor.

            https://my-app.search.windows.net/indexes/hotels?api-version=2015-02-28

4.  Angiv anmodning om sidehovedet erstatte host og api-nøgle med værdier, der er gyldige for din tjeneste.

            User-Agent: Fiddler
            host: my-app.search.windows.net
            content-type: application/json
            api-key: 1111222233334444

5.  Indsæt de felter, der udgør indeksdefinitionen i anmode om brødtekst.
            
             {
            "name": "hotels",  
            "fields": [
              {"name": "hotelId", "type": "Edm.String", "key":true, "searchable": false},
              {"name": "baseRate", "type": "Edm.Double"},
              {"name": "description", "type": "Edm.String", "filterable": false, "sortable": false, "facetable": false},
              {"name": "hotelName", "type": "Edm.String"},
              {"name": "category", "type": "Edm.String"},
              {"name": "tags", "type": "Collection(Edm.String)"},
              {"name": "parkingIncluded", "type": "Edm.Boolean"},
              {"name": "smokingAllowed", "type": "Edm.Boolean"},
              {"name": "lastRenovationDate", "type": "Edm.DateTimeOffset"},
              {"name": "rating", "type": "Edm.Int32"},
              {"name": "location", "type": "Edm.GeographyPoint"}
             ]
            }

6.  Klik på **Udfør**.

I et par sekunder, skal du se en HTTP 201 svar på listen session, der angiver indekset blev oprettet.

Hvis du får vist HTTP 504 skal du kontrollere URL-adressen angiver HTTPS. Hvis du ser HTTP 400 eller 404, kan du se anmodningsteksten for at bekræfte, at der ikke er nogen kopiere indsætte fejl. En HTTP 403 angiver typisk et problem med api-nøglen (enten en ugyldig nøgle eller syntaksen problemer med hvordan api-nøgle er angivet).

## <a name="load-documents"></a>Indlæsning af dokumenter

Under fanen **Komponist** ser din anmodning om at sende dokumenter ud som følger. Brødteksten i anmodningen indeholder dataene, søgning til 4 hoteller.

   ![][2]

1. Vælg **Send**.

2.  Angive en URL-adresse, der starter med HTTPS, efterfulgt af din URL-adressen, efterfulgt af "/indexes/ < indexname >/dokumenter/indeks? api-version = 2015-02-28". Den fulde URL-adresse skal ligne eksemplet nedenfor.

            https://my-app.search.windows.net/indexes/hotels/docs/index?api-version=2015-02-28

3.  Anmode om sidehoved skal være den samme som før. Husk, at du har erstattet host og api-nøgle med værdier, der er gyldige for din tjeneste.

            User-Agent: Fiddler
            host: my-app.search.windows.net
            content-type: application/json
            api-key: 1111222233334444

4.  Anmode om brødteksten indeholder fire de dokumenter, der skal føjes til hoteller indekset.

            {
            "value": [
            {
                "@search.action": "upload",
                "hotelId": "1",
                "baseRate": 199.0,
                "description": "Best hotel in town",
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
                "@search.action": "upload",
                "hotelId": "3",
                "baseRate": 279.99,
                "description": "Surprisingly expensive",
                "hotelName": "Dew Drop Inn",
                "category": "Bed and Breakfast",
                "tags": ["charming", "quaint"],
                "parkingIncluded": true,
                "smokingAllowed": false,
                "lastRenovationDate": null,
                "rating": 4,
                "location": { "type": "Point", "coordinates": [-122.33207, 47.60621] }
              },
              {
                "@search.action": "upload",
                "hotelId": "4",
                "baseRate": 220.00,
                "description": "This could be the one",
                "hotelName": "A Hotel for Everyone",
                "category": "Basic hotel",
                "tags": ["pool", "wifi"],
                "parkingIncluded": true,
                "smokingAllowed": false,
                "lastRenovationDate": null,
                "rating": 4,
                "location": { "type": "Point", "coordinates": [-122.12151, 47.67399] }
              }
             ]
            }

8.  Klik på **Udfør**.

I et par sekunder, skal du se en HTTP 200 svar på listen session. Dette angiver dokumenterne, der blev oprettet. Hvis du får en 207, kunne mindst ét dokument overføre. Hvis du får en 404, har du en syntaksfejl i et sidehoved eller i mødeindkaldelsen.

## <a name="query-the-index"></a>Forespørgsel indekset

Nu, hvor et indeks og dokumenter, der indlæses, kan du udstede dem forespørgsler.  Under fanen **Komponist** ser en **GET** -kommando, som forespørger din tjeneste nogenlunde sådan følgende skærmbillede.

   ![][3]

1.  Vælg **få**.

2.  Angive en URL-adresse, der starter med HTTPS, efterfulgt af din URL-adressen, efterfulgt af "/indexes/ < indexname > / dokumenter?" efterfulgt af forespørgselsparametre. Brug følgende URL-adressen, erstatte værtsnavn eksempel med en, der er gyldige for din tjeneste, som eksempel.
    
            https://my-app.search.windows.net/indexes/hotels/docs?search=motel&facet=category&facet=rating,values:1|2|3|4|5&api-version=2015-02-28

    Denne forespørgsel søger på ordet "motel" og henter grene kategorier til bedømmelser.

3.  Anmode om sidehoved skal være den samme som før. Husk, at du har erstattet host og api-nøgle med værdier, der er gyldige for din tjeneste.

            User-Agent: Fiddler
            host: my-app.search.windows.net
            content-type: application/json
            api-key: 1111222233334444

Svarkoden skal være 200, og svar output skal ligne følgende skærmbillede.

   ![][4]

Følgende eksempel forespørgsel er fra [søgeindekset handling (Azure Søg API)](http://msdn.microsoft.com/library/dn798927.aspx) på MSDN. Mange af eksempel forespørgslerne i dette emne indeholder mellemrum, der ikke er tilladt i Fiddler. Erstat hver plads med en + tegnet før indsættelse i forespørgslen en streng, før du forsøger forespørgslen i Fiddler.

**Før mellemrum erstattes:**

        GET /indexes/hotels/docs?search=*&$orderby=lastRenovationDate desc&api-version=2015-02-28

**Når mellemrum erstattes med +:**

        GET /indexes/hotels/docs?search=*&$orderby=lastRenovationDate+desc&api-version=2015-02-28

## <a name="query-the-system"></a>Forespørgsel systemet

Du kan også forespørge systemet for at få dokument tæller og lagerplads forbrug. Under fanen **Komponist** anmodningen ser nogenlunde sådan følgende og svaret vil returnere en optælling for antallet af dokumenter og meget plads der bruges.

 ![][5]

1.  Vælg **få**.

2.  Angive en URL-adresse, der indeholder din URL-adressen, efterfulgt af "/ indeks/hoteller/statistik? api-version = 2015-02-28":

            https://my-app.search.windows.net/indexes/hotels/stats?api-version=2015-02-28

3.  Angiv anmodning om sidehovedet erstatte host og api-nøgle med værdier, der er gyldige for din tjeneste.

            User-Agent: Fiddler
            host: my-app.search.windows.net
            content-type: application/json
            api-key: 1111222233334444

4.  Lad anmodningsteksten stå tomt.

5.  Klik på **Udfør**. Du bør se en HTTP 200 statuskode på listen session. Vælg et element, der er sendt til din kommando.

6.  Klik på fanen **inspektioner** , klik på fanen **sidehoveder** , og vælg derefter JSON-formatet. Du bør se dokumentets Tæl og lagerplads størrelse (i KB).

## <a name="next-steps"></a>Næste trin

Se [administrere dine søgetjenesten på Azure](search-manage.md) for en uden brug af kode tilgang til at administrere og ved hjælp af Azure søgning.


<!--Image References-->
[1]: ./media/search-fiddler/AzureSearch_Fiddler1_PutIndex.png
[2]: ./media/search-fiddler/AzureSearch_Fiddler2_PostDocs.png
[3]: ./media/search-fiddler/AzureSearch_Fiddler3_Query.png
[4]: ./media/search-fiddler/AzureSearch_Fiddler4_QueryResults.png
[5]: ./media/search-fiddler/AzureSearch_Fiddler5_QueryStats.png
