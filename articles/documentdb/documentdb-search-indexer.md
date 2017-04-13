<properties
    pageTitle="Oprette forbindelse DocumentDB med Azure søgning ved hjælp af indeks | Microsoft Azure"
    description="I denne artikel viser, hvordan du bruger til at Azure Søg indekseringsprogram med DocumentDB som datakilde."
    services="documentdb"
    documentationCenter=""
    authors="dennyglee"
    manager="jhubbard"
    editor="mimig"/>

<tags
    ms.service="documentdb"
    ms.devlang="rest-api"
    ms.topic="article"
    ms.tgt_pltfrm="NA"
    ms.workload="data-services"
    ms.date="07/08/2016"
    ms.author="denlee"/>

#<a name="connecting-documentdb-with-azure-search-using-indexers"></a>Oprette forbindelse DocumentDB med Azure søgning ved hjælp af indeks

Hvis du vil implementere gode Søg oplevelser over dataene DocumentDB, kan du bruge Azure Søg indekseringsprogram til DocumentDB! I denne artikel vil vi viser dig, hvordan du integrerer Azure DocumentDB med Azure søgning uden at skrive en kode for at bevare indeksering infrastruktur!

For at konfigurere dette, du har til [konfiguration af en søgning Azure-konto](../search/search-create-service-portal.md) (du ikke behøver at opgradere til standard søgning), og kald derefter [Azure Search REST-API](https://msdn.microsoft.com/library/azure/dn798935.aspx) for at oprette en DocumentDB **datakilde** og et **indekseringsprogram** til denne datakilde.

I rækkefølge send anmodninger om at interagere med REST API'er, kan du bruge [Postman](https://www.getpostman.com/), [Fiddler](http://www.telerik.com/fiddler)eller en anden funktion af din præference.


##<a id="Concepts"></a>Azure Søg indekseringsprogram begreber

Azure søgning understøtter oprettelse og administration af data datakilder (herunder DocumentDB) og indeks, der fungerer i forhold til de pågældende datakilder.

En **datakilde** angiver, hvilke data der skal indekseres, legitimationsoplysninger for at få adgang til data og politikker til at aktivere Azure søgning til at identificere effektivt ændringer i data (som ændret eller slettet dokumenter i din af websteder). Datakilden er defineret som en uafhængig ressource, så den kan bruges af flere indeks.

Et **indekseringsprogram** beskrives, hvordan dataene løber fra din datakilde til en destination søgeindekset. Du skal planlægge på at oprette et indeks for hver target indekset og kilde kombination. Selvom du kan have flere indeks håndskrevet tekst til det samme indeks, Skriv et indekseringsprogram kan kun i et enkelt indeks. Et indekseringsprogram der bruges til at:

- Udføre en enkelt kopi af data til at udfylde et indeks.
- Synkronisere et indeks med ændringer i datakilden på en tidsplan. Planen er en del af indekseringsprogram definitionen.
- Kalde efter behov opdateringer til et indeks efter behov.

##<a id="CreateDataSource"></a>Trin 1: Oprette en datakilde

Udsted en HTTP POST anmodning om at oprette en ny datakilde i din Azure Search-tjenesten, herunder følgende anmodning overskrifterne.

    POST https://[Search service name].search.windows.net/datasources?api-version=[api-version]
    Content-Type: application/json
    api-key: [Search service admin key]

Den `api-version` er påkrævet. Gyldige værdier omfatter `2015-02-28` eller en nyere version. Besøg [API versioner i Azure Søg](../search/search-api-versions.md) for at få vist alle understøttede søge-API-versioner.

Brødteksten for din anmodning indeholder kilde datadefinitionsforespørgsel, som skal indeholde følgende felter:

- **navn**: Vælg et navn til at repræsentere databasen DocumentDB.

- **type**: Brug `documentdb`.

- **legitimationsoplysninger**:

    - **connectionString**: påkrævet. Du kan angive forbindelsesoplysninger til databasen Azure DocumentDB i følgende format:`AccountEndpoint=<DocumentDB endpoint url>;AccountKey=<DocumentDB auth key>;Database=<DocumentDB database id>`

- **objektbeholder**:

    - **navn**: påkrævet. Angive id for samlingen DocumentDB der skal indekseres.

    - **forespørgsel**: valgfrit. Du kan angive en forespørgsel for at udjævne en vilkårlig JSON-dokument til en flad skema, der kan indeksere Azure søgning.

- **dataChangeDetectionPolicy**: valgfrit. Se nedenstående [Data ændres registrering politik](#DataChangeDetectionPolicy) .

- **dataDeletionDetectionPolicy**: valgfrit. Se [politik for registrering af Data sletningen](#DataDeletionDetectionPolicy) nedenfor.

Se et [eksempel anmodningsteksten](#CreateDataSourceExample)nedenfor.

###<a id="DataChangeDetectionPolicy"></a>Indsamle ændrede dokumenter

Formålet med en politik for registrering af ændringer er at identificere elementer med ændrede data effektivt. Den eneste understøttede politik er i øjeblikket, den `High Water Mark` politik ved hjælp af den `_ts` senest har ændret tidsstempel egenskab, der leveres af DocumentDB - der er angivet på følgende måde:

    {
        "@odata.type" : "#Microsoft.Azure.Search.HighWaterMarkChangeDetectionPolicy",
        "highWaterMarkColumnName" : "_ts"
    }

Du skal også tilføje `_ts` i prognosen og `WHERE` delsætningen for forespørgslen. Eksempel:

    SELECT s.id, s.Title, s.Abstract, s._ts FROM Sessions s WHERE s._ts >= @HighWaterMark


###<a id="DataDeletionDetectionPolicy"></a>Indsamle slettede dokumenter

Når rækker er slettet fra kildetabellen, skal du slette rækkerne fra søgeindekset samt. Formålet med en politik for registrering af sletningen er at identificere elementer med slettede data effektivt. Den eneste understøttede politik er i øjeblikket, den `Soft Delete` politik (sletningen er markeret med et flag af samme slags), som er angivet på følgende måde:

    {
        "@odata.type" : "#Microsoft.Azure.Search.SoftDeleteColumnDeletionDetectionPolicy",
        "softDeleteColumnName" : "the property that specifies whether a document was deleted",
        "softDeleteMarkerValue" : "the value that identifies a document as deleted"
    }

> [AZURE.NOTE] Du skal medtage egenskaben softDeleteColumnName i din SELECT-delsætning, hvis du bruger en brugerdefineret projicering.

###<a id="CreateDataSourceExample"></a>Anmode om brødteksten eksempel

I følgende eksempel oprettes en datakilde med en brugerdefineret forespørgsel og politik tip:

    {
        "name": "mydocdbdatasource",
        "type": "documentdb",
        "credentials": {
            "connectionString": "AccountEndpoint=https://myDocDbEndpoint.documents.azure.com;AccountKey=myDocDbAuthKey;Database=myDocDbDatabaseId"
        },
        "container": {
            "name": "myDocDbCollectionId",
            "query": "SELECT s.id, s.Title, s.Abstract, s._ts FROM Sessions s WHERE s._ts > @HighWaterMark"
        },
        "dataChangeDetectionPolicy": {
            "@odata.type": "#Microsoft.Azure.Search.HighWaterMarkChangeDetectionPolicy",
            "highWaterMarkColumnName": "_ts"
        },
        "dataDeletionDetectionPolicy": {
            "@odata.type": "#Microsoft.Azure.Search.SoftDeleteColumnDeletionDetectionPolicy",
            "softDeleteColumnName": "isDeleted",
            "softDeleteMarkerValue": "true"
        }
    }

###<a name="response"></a>Svar

Du modtager en HTTP 201 oprettet svar, hvis datakilden blev oprettet.

##<a id="CreateIndex"></a>Trin 2: Oprette et indeks

Oprette et destinationsprogram Azure søgeindekset, hvis du ikke allerede har et. Du kan gøre dette fra [Azure Portal brugergrænseflade](../search/search-create-index-portal.md) eller ved hjælp af [Opret indeks API](https://msdn.microsoft.com/library/azure/dn798941.aspx).

    POST https://[Search service name].search.windows.net/indexes?api-version=[api-version]
    Content-Type: application/json
    api-key: [Search service admin key]


Sørg for, skemaet for dine mål indeks er kompatibel med skemaet for kilde JSON dokumenter eller din brugerdefineret forespørgsel projicering output.

>[AZURE.NOTE] For partitioneret samlinger, nøglen standard dokument er Documentdb's `_rid` ejendom, der bliver omdøbt til `rid` i Azure Søg. Desuden Documentdb's `_rid` værdier indeholder tegn, der er ugyldige i Azure Søg taster. derfor den `_rid` værdier er Base64-kodet.

###<a name="figure-a-mapping-between-json-data-types-and-azure-search-data-types"></a>Figur A: tilknytning mellem JSON-datatyper og Azure Søg-datatyper

| JSON-DATATYPE|   KOMPATIBLE TARGET INDEKS FELTTYPER|
|---|---|
|Boolesk|Edm.Boolean, Edm.String|
|Tal, der ligner heltal|Edm.Int32, Edm.Int64, Edm.String|
|Tal, der ser sådan flydende punkter|Edm.Double, Edm.String|
|Streng|Edm.String|
|Matrixer af primitiv skriver fx "a", "b", "c" |Collection(EDM.String)|
|Strenge, der ligner datoer| Edm.DateTimeOffset, Edm.String|
|GeoJSON objekter fx {"type": "Punkt", "koordinater": [lang, lat]} | Edm.GeographyPoint |
|Andre JSON-objekter|I/T.|

###<a id="CreateIndexExample"></a>Anmode om brødteksten eksempel

I følgende eksempel oprettes et indeks med et id og beskrivelse felt:

    {
       "name": "mysearchindex",
       "fields": [{
         "name": "id",
         "type": "Edm.String",
         "key": true,
         "searchable": false
       }, {
         "name": "description",
         "type": "Edm.String",
         "filterable": false,
         "sortable": false,
         "facetable": false,
         "suggestions": true
       }]
     }

###<a name="response"></a>Svar

Du modtager en HTTP 201 oprettet svar, hvis indekset blev oprettet.

##<a id="CreateIndexer"></a>Trin 3: Oprette et indeks

Du kan oprette et nyt indeks i en Azure Search-tjenesten ved hjælp af en anmodning om en HTTP POST med følgende overskrifterne.

    POST https://[Search service name].search.windows.net/indexers?api-version=[api-version]
    Content-Type: application/json
    api-key: [Search service admin key]

Brødteksten for din anmodning indeholder indekseringsprogram definition, som skal indeholde følgende felter:

- **navn**: påkrævet. Navnet på indekseringsprogrammet.

- **datakildenavn**: påkrævet. Navnet på en eksisterende datakilde.

- **targetIndexName**: påkrævet. Navnet på et eksisterende indeks.

- **tidsplan**: valgfrit. Se nedenstående [indeksering tidsplan](#IndexingSchedule) .

###<a id="IndexingSchedule"></a>Kører indeks på en tidsplan

Et indekseringsprogram kan eventuelt angive en tidsplan. Hvis der findes en tidsplan, kører indekseringstjenesten med jævne mellemrum som planlagt. Planlæg har følgende egenskaber:

- **interval**: påkrævet. En varighedsværdi, der angiver et interval eller perioden for indekseringsprogram kører. Det mindste tilladte interval er 5 minutter. den længste er én dag. Det skal være formateret som en XSD "dayTimeDuration" værdi (et begrænset undersæt af en [ISO 8601 varighed](http://www.w3.org/TR/xmlschema11-2/#dayTimeDuration) værdi). Mønsteret for dette er: `P(nD)(T(nH)(nM))`. Eksempler: `PT15M` for hver 15 minutter viser `PT2H` for alle Office-dokumenter.

- **Starttidspunkt**: påkrævet. En UTC datetime, der angiver, hvor indekseringsprogrammet skal begynde kører.

###<a id="CreateIndexerExample"></a>Anmode om brødteksten eksempel

I følgende eksempel oprettes et indeks, kopierer data fra samlingen refereres til af den `myDocDbDataSource` datakilde til den `mySearchIndex` indeks i en plan, starter på Jan 1 2015 UTC og kører hver time.

    {
        "name" : "mysearchindexer",
        "dataSourceName" : "mydocdbdatasource",
        "targetIndexName" : "mysearchindex",
        "schedule" : { "interval" : "PT1H", "startTime" : "2015-01-01T00:00:00Z" }
    }

###<a name="response"></a>Svar

Du modtager en HTTP 201 oprettet svar, hvis indekseringsprogrammet blev oprettet.

##<a id="RunIndexer"></a>Trin 4: Køre et indekseringsprogram

Ud over kører med jævne mellemrum efter en tidsplan, kan et indekseringsprogram også kaldes efter behov ved at udstede følgende HTTP POST-anmodningen:

    POST https://[Search service name].search.windows.net/indexers/[indexer name]/run?api-version=[api-version]
    api-key: [Search service admin key]

###<a name="response"></a>Svar

Du modtager en HTTP 202 accepteret svar, hvis indekseringstjenesten startede korrekt.

##<a name="GetIndexerStatus"></a>Trin 5: Få indekseringsprogram status

Du kan udstede en HTTP GET-anmodning til at hente den aktuelle status og udførelse af oversigt over et indekseringsprogram:

    GET https://[Search service name].search.windows.net/indexers/[indexer name]/status?api-version=[api-version]
    api-key: [Search service admin key]

###<a name="response"></a>Svar

Du får vist en HTTP 200 OK svar, som returneres sammen med helhed svar, der indeholder oplysninger om overordnede indekseringsprogram sundhedsstatus, sidste indekseringsprogram aktiveringen samt oversigten over seneste indekseringsprogram starter (hvis den findes).

Svaret skal ligne følgende:

    {
        "status":"running",
        "lastResult": {
            "status":"success",
            "errorMessage":null,
            "startTime":"2014-11-26T03:37:18.853Z",
            "endTime":"2014-11-26T03:37:19.012Z",
            "errors":[],
            "itemsProcessed":11,
            "itemsFailed":0,
            "initialTrackingState":null,
            "finalTrackingState":null
         },
        "executionHistory":[ {
            "status":"success",
             "errorMessage":null,
            "startTime":"2014-11-26T03:37:18.853Z",
            "endTime":"2014-11-26T03:37:19.012Z",
            "errors":[],
            "itemsProcessed":11,
            "itemsFailed":0,
            "initialTrackingState":null,
            "finalTrackingState":null
        }]
    }

Oversigt over udførelse af indeholder op til de 50 seneste færdige udførelser, som er sorteret i omvendt kronologisk rækkefølge (så seneste udførelse af kommer først i svaret).

##<a name="NextSteps"></a>Næste trin

Tillykke! Du har lige lært, hvordan du integrerer Azure DocumentDB med Azure søgning ved hjælp af indekseringstjenesten til DocumentDB.

 - For at lære, hvordan mere Azure DocumentDB skal du se [DocumentDB service side](https://azure.microsoft.com/services/documentdb/).

 - Se [søgeside service](https://azure.microsoft.com/services/search/)for at få mere for at vide hvordan mere om Azure søgning.
