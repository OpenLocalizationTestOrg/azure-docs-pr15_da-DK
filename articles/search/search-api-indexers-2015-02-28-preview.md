<properties 
pageTitle="Indekseringsprogram handlinger (Azure Search-tjenesten REST-API: 2015-02-28-Preview) | Azure søgeresultater API" 
description="Indekseringsprogram handlinger (Azure Search-tjenesten REST-API: 2015-02-28-Preview)" 
services="search" 
documentationCenter="" 
authors="chaosrealm" 
manager="pablocas"
editor="" />

<tags 
ms.service="search" 
ms.devlang="rest-api" 
ms.workload="search" 
ms.topic="article"  
ms.tgt_pltfrm="na" 
ms.date="09/07/2016" 
ms.author="eugenesh" />

#<a name="indexer-operations-azure-search-service-rest-api-2015-02-28-preview"></a>Indekseringsprogram handlinger (Azure Search-tjenesten REST-API: 2015-02-28-Preview)#

> [AZURE.NOTE] I denne artikel beskrives indeks i [2015-02-28-Preview REST-API](search-api-2015-02-28-preview.md). Denne API version tilføjer preview-versioner af Azure Blob-lager indekseringsprogram med udtrækning af dokumentet og Azure Table Storage indekseringsprogram samt andre forbedringer. API understøtter også alment tilgængelig (GA) indeks, herunder indeks til Azure SQL-Database, SQL Server på Azure FOS og Azure DocumentDB.

## <a name="overview"></a>Oversigt ##

Azure søgning kan integrere direkte med nogle almindelige datakilder, fjerne er nødvendigheden at skrive programkode for at indeksere dine data. Hvis du vil konfigurere dette op, kan du ringe API'EN Azure Søg for at oprette og administrere **indeks** og **datakilder**. 

Et **indeks** er en ressource, der forbinder datakilder med target søgeindeks. Et indekseringsprogram bruges på følgende måder: 

- Udføre en enkelt kopi af data til at udfylde et indeks.
- Synkronisere et indeks med ændringer i datakilden på en tidsplan. Planen er en del af indekseringsprogram definitionen.
- Kalde efter behov at opdatere et indeks, efter behov. 

Et **indekseringsprogram** er nyttig, når du vil normalt opdateringer til et indeks. Du kan konfigurere en indbygget tidsplan som en del af en definition af indekseringsprogram eller køre den efter behov ved hjælp af [Køre indekseringsprogram](#RunIndexer). 

En **datakilde** angiver, hvilke data der skal indekseres, legitimationsoplysninger for at få adgang til data og politikker til at aktivere Azure søgning til at identificere ændringer i data (som ændret eller slettet rækker i en databasetabel) effektivt. Det er defineret som en uafhængig ressource, så den kan bruges af flere indeks.

Følgende datakilder understøttes i øjeblikket:

- **Azure SQL-Database** og **SQL Server på Azure FOS**. Du kan finde en bestemt gennemgang [i denne artikel](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers-2015-02-28.md). 
- **Azure DocumentDB**. Du kan finde en bestemt gennemgang [i denne artikel](../documentdb/documentdb-search-indexer.md). 
- **Azure Blob-lager**, herunder følgende dokumentformater: PDF-fil, Microsoft Office (DOCX/dokument XSLX/XLS ppt/PPT, og meddelelse), HTML, XML, ZIP og almindelig tekst-filer (herunder JSON). Du kan finde en bestemt gennemgang [i denne artikel](search-howto-indexing-azure-blob-storage.md).
- **Azure-Tabellager**. Du kan finde en bestemt gennemgang [i denne artikel](search-howto-indexing-azure-tables.md).
     
Vi overvejer tilføje understøttelse af flere datakilder i fremtiden. For at hjælpe os med at prioritere disse beslutninger, skal du give din feedback på [Azure Søg feedback-forummet](http://feedback.azure.com/forums/263029-azure-search).

Se [Grænser](search-limits-quotas-capacity.md) for maksimumbegrænsninger, der er relateret til indekseringsprogram og data kilde ressourcer.

## <a name="typical-usage-flow"></a>Typiske Flow ##

Du kan oprette og administrere indeks og datakilder via enkel HTTP-anmodninger (INDLÆG, GET, er LAGT, Slet) mod en given `data source` eller `indexer` ressource.

Konfiguration af automatisk indeksering er typisk en proces i fire trin:

1. Identificere den datakilde, der indeholder de data, der skal indekseres. Husk på, at Azure søgning ikke understøtter muligvis alle de datatyper, der findes i datakilden. Se [understøttede datatyper](https://msdn.microsoft.com/library/azure/dn798938.aspx) for listen.

2. Oprette en Azure søgeindekset Hvis skema er kompatibel med din datakilde.
  
3. Oprette en Azure Søg datakilde, som beskrevet i [Oprette datakilde](#CreateDataSource).
  
4. Oprette et Azure Søg indekseringsprogram som beskrevet [Oprette indekset](#CreateIndexer).

Du skal planlægge på at oprette et indeks for hver target indekset og kilde kombination. Du kan have flere indeks håndskrevet tekst til det samme indeks, og du kan genbruge den samme datakilde for flere indeks. Men et indekseringsprogram kan kun bruge én datakilde ad gangen, og kan kun skrive til et enkelt indeks. 

Når du har oprettet et indeks, kan du hente udførelse af status ved hjælp af handlingen [Hent indekseringsprogram Status](#GetIndexerStatus) . Du kan også køre et indekseringsprogram til enhver tid (i stedet for eller ud over kører den på en tidsplan med jævne mellemrum) ved hjælp af handlingen [Køre indekseringsprogram](#RunIndexer) .

<!-- MSDN has 2 art files plus a API topic link list -->


## <a name="create-data-source"></a>Oprette datakilde ##

I Azure søgning bruges en datakilde med indeks, give forbindelsesoplysningerne til ad hoc- eller planlagt dataopdatering af et indeks med mål. Du kan oprette en ny datakilde i en Azure Search-tjenesten ved hjælp af en HTTP POST-anmodning.
    
    POST https://[service name].search.windows.net/datasources?api-version=[api-version]
    Content-Type: application/json
    api-key: [admin key]

Du kan også bruge læg og angive navnet på datakilden på URI. Hvis datakilden ikke findes, oprettes den.

    PUT https://[service name].search.windows.net/datasources/[datasource name]?api-version=[api-version]

**Bemærk**: det maksimale antal datakilder tilladt varierer efter priser niveau. Tjenesten gratis giver mulighed for op til 3 datakilder. Standard service kan 50 datakilder. Du kan finde oplysninger i [Tjenesten begrænsninger](search-limits-quotas-capacity.md) .

**Anmode om**

HTTPS er påkrævet for alle serviceanmodninger. **Opret datakilde** anmodningen kan opbygget ved hjælp af et INDLÆG eller læg metode. Når du bruger INDLÆG, skal du angive et datakildenavn i anmodningsteksten sammen med kilde datadefinitionsforespørgsel. Med læg er navnet på en del af URL-adressen. Hvis datakilden ikke findes, oprettes den. Hvis de allerede forefindes, bliver de opdateret til den nye definition. 

Datakildenavnet skal skrives med små bogstaver, starter med et bogstav eller et tal, har ingen skråstreger eller prikker og være mindre end 128 tegn. Resten af navnet kan efter start datakildenavnet med et bogstav eller et tal, og Medtag bogstav, tal og stiplet, så længe stregerne ikke der er sammenhængende. Du kan finde oplysninger i [Naming regler](https://msdn.microsoft.com/library/azure/dn857353.aspx) .

Den `api-version` er påkrævet. Den aktuelle version er `2015-02-28`.

**Anmode om sidehoveder**

Følgende liste beskrives krævede og valgfrie anmodning overskrifterne. 

- `Content-Type`: Der er påkrævet. Angiv denne indstilling til`application/json`
- `api-key`: Der er påkrævet. Den `api-key` bruges til at godkende anmodningen til Search-tjenesten. Det er en strengværdi, der er entydige for din tjeneste. **Opret datakilde** anmodningen skal indeholde en `api-key` sidehoved, der er angivet til din administrator nøgle (i modsætning til en forespørgsel nøgle). 
 
Du skal også navnet på tjenesten at opbygge URL-anmodningen. Du kan få både navnet på tjenesten og `api-key` fra dashboardet tjeneste i [Azure-portalen](https://portal.azure.com/). Se [oprette en søgetjeneste i portalen](search-create-service-portal.md) til sidenavigation Hjælp.

<a name="CreateDataSourceRequestSyntax"></a>
**Syntaksen for anmodning om brødtekst**

Brødteksten i anmodningen indeholder en datakilde definition, som indeholder datakilden, type legitimationsoplysninger for at læse dataene, samt et valgfrit-ændre registrering og data sletningen registrering politikker, der bruges til at identificere effektivt ændret eller slettet data i datakilden, når den bruges med et regelmæssigt planlagte indekseringsprogram. 

Syntaksen til at strukturere anmodning om data er som følger. En eksempel-anmodning leveres yderligere på i dette emne.

    { 
        "name" : "Required for POST, optional for PUT. The name of the data source",
        "description" : "Optional. Anything you want, or nothing at all",
        "type" : "Required. Must be one of 'azuresql', 'documentdb', 'azureblob', or 'azuretable'",
        "credentials" : { "connectionString" : "Required. Connection string for your data source" },
        "container" : { "name" : "Required. The name of the table, collection, or blob container you wish to index" },
        "dataChangeDetectionPolicy" : { Optional. See below for details }, 
        "dataDeletionDetectionPolicy" : { Optional. See below for details }
    }

Anmodningen indeholder følgende egenskaber: 

- `name`: Der er påkrævet. Navnet på datakilden. Et datakildenavn skal kun indeholde små bogstaver, tal eller stiplet, kan ikke starte eller slutte med tankestreger og er begrænset til 128 tegn.
- `description`: En valgfri beskrivelse. 
- `type`: Der er påkrævet. Skal være en af de understøttede datakilder datatyper:
    - `azuresql`-Azure SQL-Database eller SQL Server på Azure FOS
    - `documentdb`-Azure DocumentDB
    - `azureblob`-Azure Blob-lager
    - `azuretable`-Azure-Tabellager
- `credentials`:
    - De påkrævede `connectionString` egenskaben angiver forbindelsesstrengen for datakilden. Formatet af forbindelsesstrengen afhænger af datakildetypen: 
        - Dette er den sædvanlige SQL Server-forbindelsesstreng til Azure SQL. Hvis du bruger portalen Azure til at hente forbindelsesstrengen, skal du bruge den `ADO.NET connection string` indstilling.
        - DocumentDB, forbindelsesstrengen være i følgende format: `"AccountEndpoint=https://[your account name].documents.azure.com;AccountKey=[your account key];Database=[your database id]"`. Alle værdierne, der er påkrævet. Du kan finde dem på [Azure-portalen](https://portal.azure.com/).  
        - Azure Blob og Table Storage ved er dette lagerplads konto forbindelsesstrengen. Formatet, der er beskrevet [her](https://azure.microsoft.com/documentation/articles/storage-configure-connection-string/). HTTPS-protokollen til slutpunkt er påkrævet.  
- `container`, kræves: Angiver dataene, der skal indekseres ved hjælp af den `name` og `query` egenskaber: 
    - `name`, kræves:
        - Azure SQL: Angiver den tabel eller visning. Du kan bruge skema kvalificerede navne, såsom `[dbo].[mytable]`.
        - DocumentDB: Angiver samlingen. 
        - Azure Blob-lager: Angiver objektbeholderen til lagring af.
        - Azure-Tabellager: Angiver navnet på tabellen. 
    - `query`, Valgfrit:
        - DocumentDB: kan du angive en forespørgsel, der samkopieres en vilkårlig JSON dokumentets layout til en flad skema, der kan indeksere Azure søgning.  
        - Azure Blob-lager: kan du angive en virtuel mappe i objektbeholderen blob. For eksempel til blob stien `mycontainer/documents/blob.pdf`, `documents` kan bruges som den virtuelle mappe.
        - Azure-Tabellager: gør det muligt at angive en forespørgsel, der filtrerer sæt af rækker, der skal importeres.
        - Azure SQL: forespørgsel understøttes ikke. Hvis du skal bruge denne funktion, skal du stemme for [denne forslag](https://feedback.azure.com/forums/263029-azure-search/suggestions/9893490-support-user-provided-query-in-sql-indexer)
- Det valgfri `dataChangeDetectionPolicy` og `dataDeletionDetectionPolicy` egenskaber er beskrevet nedenfor.

<a name="DataChangeDetectionPolicies"></a>
**Data Skift registrering politikker**

Formålet med en politik for registrering af ændringer er at identificere elementer med ændrede data effektivt. Understøttede politikker varierer afhængigt af datakildetypen. Nedenstående afsnit beskrives hver politik. 

***Høj vandmærke Skift registrering politik*** 

Brug denne politik, når datakilden indeholder en kolonne eller egenskab, der opfylder følgende kriterier:
 
- Alle indsætter angive en værdi for kolonnen. 
- Alle opdateringer til et element kan også ændre værdien af kolonnen. 
- Værdien af kolonnen øges med hver enkelt ændring.
- Forespørgsler, der bruger en filtreringsdelsætning stil med følgende `WHERE [High Water Mark Column] > [Current High Water Mark Value]` kan udføres effektivt.

For eksempel, når du bruger Azure SQL-datakilder, som en indekserede `rowversion` kolonne er ideel kandidat til brug sammen med med politikken høj vand er markeret. 

Denne politik kan angives som følger:

    { 
        "@odata.type" : "#Microsoft.Azure.Search.HighWaterMarkChangeDetectionPolicy",
        "highWaterMarkColumnName" : "[a row version or last_updated column name]" 
    } 

> [AZURE.NOTE] Når du bruger DocumentDB datakilder, skal du bruge den `_ts` egenskab, der leveres af DocumentDB. 

> [AZURE.NOTE] Når du bruger Azure Blob-datakilder, Azure Søg automatisk bruger en høj vandmærke ret registrering for baseret på en blob senest har ændret tidsstempel; Du behøver ikke at angive en sådan politik dig selv.   

***SQL integreret Skift registrering politik***

Hvis din SQL-database understøtter [registrering af ændringer](https://msdn.microsoft.com/library/bb933875.aspx), anbefaler vi bruger SQL integreret ændre sporingsmetode. Denne politik giver mulighed for den mest effektive registrering af ændringer, og gør det muligt for Azure Søg til at identificere slettede rækker uden at du skal have en eksplicit "bløde Slet" i kolonnen i dit skema.

Integreret registrering understøttes i de følgende versioner af SQL Server-database: 
- SQL Server 2008 R2, hvis du bruger SQL Server på Azure FOS.
- Azure SQL-Database version 12, hvis du bruger Azure SQL-Database.  

Når ved hjælp af SQL integreret ændringsregistrering politik ikke angiver en separat registrering politik for sletning – denne politik har indbygget understøttelse af identificere slettede rækker. 

Denne politik kan kun bruges med tabeller. Det kan ikke bruges med visninger. Du skal aktivere registrering af ændringer til den tabel, du bruger, før du kan bruge denne politik. Se [aktivere og deaktivere registrering af ændringer](https://msdn.microsoft.com/library/bb964713.aspx) til instruktionerne.    
 
Når strukturere **Opret datakilde** anmodningen, SQL integreret Skift sporing politik kan angives som følger:

    { 
        "@odata.type" : "#Microsoft.Azure.Search.SqlIntegratedChangeTrackingPolicy" 
    }

<a name="DataDeletionDetectionPolicies"></a>
**Data sletningen registrering politikker**

Formålet med en politik for registrering af sletningen er at identificere elementer med slettede data effektivt. Den eneste understøttede politik er i øjeblikket, den `Soft Delete` politikken, som giver mulighed for at identificere slettede elementer, der er baseret på værdien af en `soft delete` kolonne eller en egenskab i datakilden. Denne politik kan angives som følger:

    { 
        "@odata.type" : "#Microsoft.Azure.Search.SoftDeleteColumnDeletionDetectionPolicy",
        "softDeleteColumnName" : "the column that specifies whether a row was deleted", 
        "softDeleteMarkerValue" : "the value that identifies a row as deleted" 
    }

**NOTE:** Kun kolonner med streng, heltal eller booleske værdier understøttes. Den værdi, der bruges som `softDeleteMarkerValue` skal være en streng, selvom den tilsvarende kolonne indeholder heltal eller booleske værdier. Hvis den værdi, der vises i datakilden er 1, for eksempel bruge `"1"` som den `softDeleteMarkerValue`.    

<a name="CreateDataSourceRequestExamples"></a>
**Anmode om brødteksten eksempler**

Hvis du vil bruge datakilden med et indeks, der kører på en tidsplan, viser dette eksempel hvordan du kan angive politikker for registrering af ændringer og sletning: 

    { 
        "name" : "asqldatasource",
        "description" : "a description",
        "type" : "azuresql",
        "credentials" : { "connectionString" : "Server=tcp:....database.windows.net,1433;Database=...;User ID=...;Password=...;Trusted_Connection=False;Encrypt=True;Connection Timeout=30;" },
        "container" : { "name" : "sometable" },
        "dataChangeDetectionPolicy" : { "@odata.type" : "#Microsoft.Azure.Search.HighWaterMarkChangeDetectionPolicy", "highWaterMarkColumnName" : "RowVersion" }, 
        "dataDeletionDetectionPolicy" : { "@odata.type" : "#Microsoft.Azure.Search.SoftDeleteColumnDeletionDetectionPolicy", "softDeleteColumnName" : "IsDeleted", "softDeleteMarkerValue" : "true" }
    }

Hvis du kun vil bruge datakilden til enkeltstående kopi af dataene, kan politikkerne, der udelades:

    { 
        "name" : "asqldatasource",
        "description" : "anything you want, or nothing at all",
        "type" : "azuresql",
        "credentials" : { "connectionString" : "Server=tcp:....database.windows.net,1433;Database=...;User ID=...;Password=...;Trusted_Connection=False;Encrypt=True;Connection Timeout=30;" },
        "container" : { "name" : "sometable" }
    } 

**Svar**

For en vellykket anmodning: 201 oprettet. 

<a name="UpdateDataSource"></a>
## <a name="update-data-source"></a>Opdatere datakilde ##

Du kan opdatere en eksisterende datakilde ved hjælp af en HTTP PLACERE anmodning. Angiv navnet på datakilden for at opdatere på anmodningen URI:

    PUT https://[service name].search.windows.net/datasources/[datasource name]?api-version=[api-version]
    Content-Type: application/json
    api-key: [admin key]

Den `api-version` er påkrævet. Den aktuelle version er `2015-02-28`. [Azure Søg versionsstyring](https://msdn.microsoft.com/library/azure/dn864560.aspx) har detaljer og flere oplysninger om alternativ versioner.

Den `api-key` skal være en administrator nøgle (i modsætning til en forespørgsel nøgle). Se afsnittet godkendelse i [Søg Service REST-API](https://msdn.microsoft.com/library/azure/dn798935.aspx) mere at vide om taster. [Opret en søgetjeneste i portalen](search-create-service-portal.md) forklarer, hvordan du får den URL-adressen og vigtige egenskaber, der bruges i anmodningen.

**Anmode om**

Anmodning om brødteksten syntaksen er den samme som for [anmodninger om Opret datakilde](#CreateDataSourceRequestSyntax).

> [AZURE.NOTE] Nogle egenskaber kan ikke opdateres på en eksisterende datakilde. For eksempel kan du ændre typen af en eksisterende datakilde.  

> [AZURE.NOTE] Hvis du ikke vil ændre forbindelsesstrengen for en eksisterende datakilde, kan du angive konstanten `<unchanged>` for forbindelsesstrengen. Det er meget anvendeligt i situationer, hvor du vil opdatere en datakilde, men ikke har direkte adgang til forbindelsesstrengen, da det er sikkerhed-følsomme data.

**Svar**

For en vellykket anmodning: 201 oprettet, hvis en ny datakilde blev oprettet, og 204 intet indhold Hvis en eksisterende datakilde blev opdateret.

<a name="ListDataSource"></a>
## <a name="list-data-sources"></a>Liste over datakilder ##

Handlingen **Liste-datakilder, der** returnerer en liste over datakilderne i din Azure Search-tjenesten. 

    GET https://[service name].search.windows.net/datasources?api-version=[api-version]
    api-key: [admin key]

Den `api-version` er påkrævet. Den aktuelle version er `2015-02-28`. 

Den `api-key` skal være en administrator nøgle (i modsætning til en forespørgsel nøgle). Se afsnittet godkendelse i [Søg Service REST-API](https://msdn.microsoft.com/library/azure/dn798935.aspx) mere at vide om taster. [Opret en søgetjeneste i portalen](search-create-service-portal.md) forklarer, hvordan du får den URL-adressen og vigtige egenskaber, der bruges i anmodningen.

**Svar**

For en vellykket anmodning: 200 OK.

Her er et eksempel svar brødtekst:

    {
      "value" : [
        {
          "name": "datasource1",
          "type": "azuresql",
          ... other data source properties
        }]
    }

Bemærk, at du kan filtrere svaret ned til kun de egenskaber, du er interesseret i. Eksempelvis hvis du vil kun en liste over navne på datakilder, bruge OData `$select` forespørgsel indstilling:

    GET /datasources?api-version=205-02-28&$select=name

I dette tilfælde ville svaret fra eksemplet ovenfor se således ud: 

    {
      "value" : [ { "name": "datasource1" }, ... ]
    }

Dette er en praktisk metode til at gemme båndbredde, hvis du har en masse datakilder i din Search-tjenesten.

<a name="GetDataSource"></a>
## <a name="get-data-source"></a>Få datakilde ##

Handlingen **Få datakilde** henter datakildedefinitionen fra Azure søgning.

    GET https://[service name].search.windows.net/datasources/[datasource name]?api-version=[api-version]
    api-key: [admin key]

Den `api-version` er påkrævet. Den aktuelle version er `2015-02-28`. 

Den `api-key` skal være en administrator nøgle (i modsætning til en forespørgsel nøgle). Se afsnittet godkendelse i [Søg Service REST-API](https://msdn.microsoft.com/library/azure/dn798935.aspx) mere at vide om taster. [Opret en søgetjeneste i portalen](search-create-service-portal.md) forklarer, hvordan du får den URL-adressen og vigtige egenskaber, der bruges i anmodningen.

**Svar**

Statuskode: 200 OK returneres for en vellykket svar.

Svaret ligner eksemplerne i [Opret datakilde eksempel anmodninger](#CreateDataSourceRequestExamples): 

    { 
        "name" : "asqldatasource",
        "description" : "a description",
        "type" : "azuresql",
        "credentials" : { "connectionString" : "Server=tcp:....database.windows.net,1433;Database=...;User ID=...;Password=...;Trusted_Connection=False;Encrypt=True;Connection Timeout=30;" },
        "container" : { "name" : "sometable" },
        "dataChangeDetectionPolicy" : { 
            "@odata.type" : "#Microsoft.Azure.Search.HighWaterMarkChangeDetectionPolicy",
            "highWaterMarkColumnName" : "RowVersion" }, 
        "dataDeletionDetectionPolicy" : { 
            "@odata.type" : "#Microsoft.Azure.Search.SoftDeleteColumnDeletionDetectionPolicy",
            "softDeleteColumnName" : "IsDeleted", 
            "softDeleteMarkerValue" : "true" }
    }

> [AZURE.NOTE] Angiv ikke den `Accept` sidehoved for anmodning om at `application/json;odata.metadata=none` når opkald til denne API som at gøre dette medfører `@odata.type` attribut, der skal udelades fra svaret, og du vil ikke kunne skelne mellem ændring af data og data sletningen politikker for registrering af forskellige typer. 

<a name="DeleteDataSource"></a>
## <a name="delete-data-source"></a>Slette datakilde ##

Handlingen **Slet datakilde** fjerner en datakilde fra din Azure Search-tjenesten.

    DELETE https://[service name].search.windows.net/datasources/[datasource name]?api-version=[api-version]
    api-key: [admin key]

> [AZURE.NOTE] Hvis en hvilken som helst indeks refererer til den datakilde, som du sletter, fortsætter stadig sletningen. Disse indeks kan dog overgang i en fejltilstand på deres næste kørsel.  

Den `api-version` er påkrævet. Den aktuelle version er `2015-02-28`. 

Den `api-key` skal være en administrator nøgle (i modsætning til en forespørgsel nøgle). Se afsnittet godkendelse i [Søg Service REST-API](https://msdn.microsoft.com/library/azure/dn798935.aspx) mere at vide om taster. [Opret en søgetjeneste i portalen](search-create-service-portal.md) forklarer, hvordan du får den URL-adressen og vigtige egenskaber, der bruges i anmodningen.

**Svar**

Statuskode: 204 intet indhold returneres for en vellykket svar.

<a name="CreateIndexer"></a>
## <a name="create-indexer"></a>Oprette indekset ##

Du kan oprette et nyt indeks i en Azure Search-tjenesten ved hjælp af en HTTP POST-anmodning.
    
    POST https://[service name].search.windows.net/indexers?api-version=[api-version]
    Content-Type: application/json
    api-key: [admin key]

Du kan også bruge læg og angive navnet på datakilden på URI. Hvis datakilden ikke findes, oprettes den.

    PUT https://[service name].search.windows.net/indexers/[indexer name]?api-version=[api-version]

> [AZURE.NOTE] Det maksimale antal indeks, der er tilladt varierer efter priser niveau. Tjenesten gratis kan op til 3 indeks. Standard service kan 50 indeks. Du kan finde oplysninger i [Tjenesten begrænsninger](search-limits-quotas-capacity.md) .

Den `api-version` er påkrævet. Den aktuelle version er `2015-02-28`. 

Den `api-key` skal være en administrator nøgle (i modsætning til en forespørgsel nøgle). Se afsnittet godkendelse i [Søg Service REST-API](https://msdn.microsoft.com/library/azure/dn798935.aspx) mere at vide om taster. [Opret en søgetjeneste i portalen](search-create-service-portal.md) forklarer, hvordan du får den URL-adressen og vigtige egenskaber, der bruges i anmodningen.


<a name="CreateIndexerRequestSyntax"></a>
**Syntaksen for anmodning om brødtekst**

Brødteksten i anmodningen indeholder en definition af indekseringsprogram, som angiver datakilden og mål indekset for indeksering, såvel som valgfri indeksering tidsplan og parametre. 


Syntaksen til at strukturere anmodning om data er som følger. En eksempel-anmodning leveres yderligere på i dette emne.

    { 
        "name" : "Required for POST, optional for PUT. The name of the indexer",
        "description" : "Optional. Anything you want, or null",
        "dataSourceName" : "Required. The name of an existing data source",
        "targetIndexName" : "Required. The name of an existing index",
        "schedule" : { Optional. See Indexing Schedule below. },
        "parameters" : { Optional. See Indexing Parameters below. },
        "fieldMappings" : { Optional. See Field Mappings below. },
        "disabled" : Optional boolean value indicating whether the indexer is disabled. False by default.  
    }

**Indekseringsprogram tidsplan**

Et indekseringsprogram kan eventuelt angive en tidsplan. Hvis der findes en tidsplan, kører indekseringstjenesten med jævne mellemrum som planlagt. Planlæg har følgende egenskaber:

- `interval`: Der er påkrævet. En varighedsværdi, der angiver et interval eller perioden for indekseringsprogram kører. Det mindste tilladte interval er 5 minutter. den længste er én dag. Det skal være formateret som en XSD "dayTimeDuration" værdi (et begrænset undersæt af en [ISO 8601 varighed](http://www.w3.org/TR/xmlschema11-2/#dayTimeDuration) værdi). Mønsteret for dette er: `"P[nD][T[nH][nM]]"`. Eksempler: `PT15M` for hver 15 minutter viser `PT2H` for alle Office-dokumenter. 

- `startTime`: Der er påkrævet. En UTC datetime, når indekseringsprogrammet skal begynde at køre. 

**Indekseringsprogram parametre**

Et indekseringsprogram kan eventuelt angive flere parametre, der påvirker funktionsmåden. Alle parametre er valgfrit.  

- `maxFailedItems`: Antallet elementer, der ikke kan indekseres, før en indekseringsprogram Kør betragtes som en fejl. Standardværdien er 0. Oplysninger om emner, der ikke er returneret af handlingen [Hent indekseringsprogram Status](#GetIndexerStatus) . 

- `maxFailedItemsPerBatch`: Antallet elementer, der ikke kan indekseres i hvert batch, før en indekseringsprogram Kør betragtes som en fejl. Standardværdien er 0.

- `base64EncodeKeys`: Angiver om, hvorvidt dokumentet taster skal base-64-kodet. Azure søgning samt restriktioner på tegn, der kan være til stede i et dokumentnøgle. Værdierne i kildedataene kan dog indeholde tegn, der er ugyldige. Hvis det er nødvendigt at indeksere disse værdier som dokument nøgler, dette flag kan være indstillet til sand. Standard er `false`.

- `batchSize`: Angiver antallet af elementer, der læses fra datakilden og indekseret som en enkelt batch for at forbedre ydeevnen. Standard afhænger af datakildetypen: det er 1000 til Azure SQL og DocumentDB og 10 til Azure Blob-lager.

**Felttilknytninger**

Du kan bruge tilknytninger til at knytte et feltnavn i datakilden til et andet feltnavn i target indekset. For eksempel kan du overveje en kildetabel med et felt `_id`. Azure Søg tillader ikke, at et feltnavn starter med et understregningstegn, så feltet skal omdøbes. Dette kan gøres ved hjælp af den `fieldMappings` egenskab for indekseringsprogram på følgende måde: 
    
    "fieldMappings" : [ { "sourceFieldName" : "_id", "targetFieldName" : "id" } ] 

Du kan angive flere tilknytninger: 

    "fieldMappings" : [ 
        { "sourceFieldName" : "_id", "targetFieldName" : "id" },
        { "sourceFieldName" : "_timestamp", "targetFieldName" : "timestamp" },
     ]

Både kilde- og destinationswebsteder feltnavne er case-insensitive.

<a name="FieldMappingFunctions"></a>
***Feltet tilknytning funktioner***

Tilknytninger kan også bruges til at transformere kilde feltværdier ved hjælp af *tilknytning funktioner*.

Kun én funktion understøttes i øjeblikket: `jsonArrayToStringCollection`. Den fortolker et felt, der indeholder en streng, der er formateret som en JSON matrix i et Collection(Edm.String) felt i target indekset. Det er beregnet til brug med Azure SQL indekseringsprogram især, da SQL ikke har datatypen oprindelige samling. Det kan bruges på følgende måde: 

    "fieldMappings" : [ { "sourceFieldName" : "tags", "mappingFunction" : { "name" : "jsonArrayToStringCollection" } } ] 

Hvis kildefeltet indeholder strengen eksempelvis `["red", "white", "blue"]`, derefter destinationsfeltet af typen `Collection(Edm.String)` udfyldes med de tre værdier `"red"`, `"white"` og `"blue"`.

Bemærk, at den `targetFieldName` egenskab er valgfri. Hvis venstre ydre, den `sourceFieldName` værdi bruges. 

<a name="CreateIndexerRequestExamples"></a>
**Anmode om brødteksten eksempler**

I følgende eksempel oprettes et indeks, kopierer data fra den tabel, der refereres til af den `ordersds` datakilde til den `orders` indeks i en plan, starter på Jan 1 2015 UTC og kører hver time. Hver indekseringsprogram aktivering kan udføres, hvis højst 5 elementer ikke kan indekseres i hvert batch, og ikke mere end 10 elementer ikke kan indekseres i alt. 

    {
        "name" : "myindexer",
        "description" : "a cool indexer",
        "dataSourceName" : "ordersds",
        "targetIndexName" : "orders",
        "schedule" : { "interval" : "PT1H", "startTime" : "2015-01-01T00:00:00Z" },
        "parameters" : { "maxFailedItems" : 10, "maxFailedItemsPerBatch" : 5, "base64EncodeKeys": false }
    }

**Svar**

201 oprettet for en vellykket anmodning.


<a name="UpdateIndexer"></a>
## <a name="update-indexer"></a>Opdater indeks ##

Du kan opdatere et eksisterende indekseringsprogram ved hjælp af en HTTP PLACERE anmodning. Angiv navnet på indekseringsprogram at opdatere på anmodningen URI:

    PUT https://[service name].search.windows.net/indexers/[indexer name]?api-version=[api-version]
    Content-Type: application/json
    api-key: [admin key]

Den `api-version` er påkrævet. Den aktuelle version er `2015-02-28`. 

Den `api-key` skal være en administrator nøgle (i modsætning til en forespørgsel nøgle). Se afsnittet godkendelse i [Søg Service REST-API](https://msdn.microsoft.com/library/azure/dn798935.aspx) mere at vide om taster. [Opret en søgetjeneste i portalen](search-create-service-portal.md) forklarer, hvordan du får den URL-adressen og vigtige egenskaber, der bruges i anmodningen.

**Anmode om**

Anmodning om brødteksten syntaksen er den samme som for [anmodninger om oprette indekset](#CreateIndexerRequestSyntax).

**Svar**

For en vellykket anmodning: 201 oprettet, hvis en nye indekseringsprogram blev oprettet, og 204 intet indhold Hvis et eksisterende indekseringsprogram blev opdateret.


<a name="ListIndexers"></a>
## <a name="list-indexers"></a>Listen indeks ##

Handlingen **Listen indeks** returnerer listen over indeks i din Azure Search-tjenesten. 

    GET https://[service name].search.windows.net/indexers?api-version=[api-version]
    api-key: [admin key]


Den `api-version` er påkrævet. Den foreløbige version er `2015-02-28-Preview`. [Azure Søg versionsstyring](https://msdn.microsoft.com/library/azure/dn864560.aspx) har detaljer og flere oplysninger om alternativ versioner.

Den `api-key` skal være en administrator nøgle (i modsætning til en forespørgsel nøgle). Se afsnittet godkendelse i [Søg Service REST-API](https://msdn.microsoft.com/library/azure/dn798935.aspx) mere at vide om taster. [Opret en søgetjeneste i portalen](search-create-service-portal.md) forklarer, hvordan du får den URL-adressen og vigtige egenskaber, der bruges i anmodningen.

**Svar**

For en vellykket anmodning: 200 OK.

Her er et eksempel svar brødtekst:

    {
      "value" : [
      {
        "name" : "myindexer",
        "description" : "a cool indexer",
        "dataSourceName" : "ordersds",
        "targetIndexName" : "orders",
        ... other indexer properties
      }]
    }

Bemærk, at du kan filtrere svaret ned til kun de egenskaber, du er interesseret i. Eksempelvis hvis du vil kun en liste over indekseringsprogram navne, bruge OData `$select` forespørgsel indstilling:

    GET /indexers?api-version=2014-10-20-Preview&$select=name

I dette tilfælde ville svaret fra eksemplet ovenfor se således ud: 

    {
      "value" : [ { "name": "myindexer" } ]
    }

Dette er en praktisk metode til at gemme båndbredde, hvis du har en masse indeks i Search-tjenesten.


<a name="GetIndexer"></a>
## <a name="get-indexer"></a>Få indekseringsprogram ##

Handlingen **Få indekseringsprogram** får indekseringsprogram definitionen fra Azure søgningen.

    GET https://[service name].search.windows.net/indexers/[indexer name]?api-version=[api-version]
    api-key: [admin key]

Den `api-version` er påkrævet. Den foreløbige version er `2015-02-28-Preview`. 

Den `api-key` skal være en administrator nøgle (i modsætning til en forespørgsel nøgle). Se afsnittet godkendelse i [Søg Service REST-API](https://msdn.microsoft.com/library/azure/dn798935.aspx) mere at vide om taster. [Opret en søgetjeneste i portalen](search-create-service-portal.md) forklarer, hvordan du får den URL-adressen og vigtige egenskaber, der bruges i anmodningen.

**Svar**

Statuskode: 200 OK returneres for en vellykket svar.

Svaret ligner eksemplerne i [oprette indekseringsprogram eksempel anmodninger](#CreateIndexerRequestExamples): 

    {
        "name" : "myindexer",
        "description" : "a cool indexer",
        "dataSourceName" : "ordersds",
        "targetIndexName" : "orders",
        "schedule" : { "interval" : "PT1H", "startTime" : "2015-01-01T00:00:00Z" },
        "parameters" : { "maxFailedItems" : 10, "maxFailedItemsPerBatch" : 5, "base64EncodeKeys": false }
    }


<a name="DeleteIndexer"></a>
## <a name="delete-indexer"></a>Slette indekseringsprogram ##

Handlingen **Slet indekseringsprogram** fjerner et indekseringsprogram fra din Azure Search-tjenesten.

    DELETE https://[service name].search.windows.net/indexers/[indexer name]?api-version=[api-version]
    api-key: [admin key]

Når et indekseringsprogram slettes, der går udførelser indekseringsprogram igangværende på det pågældende tidspunkt til fuldførelse, men ingen yderligere udførelser planlægges. Forsøg på at bruge en ikke-eksisterende indekseringsprogram medfører HTTP-statuskode 404 ikke fundet. 
 
Den `api-version` er påkrævet. Den foreløbige version er `2015-02-28-Preview`. 

Den `api-key` skal være en administrator nøgle (i modsætning til en forespørgsel nøgle). Se afsnittet godkendelse i [Søg Service REST-API](https://msdn.microsoft.com/library/azure/dn798935.aspx) mere at vide om taster. [Opret en søgetjeneste i portalen](search-create-service-portal.md) forklarer, hvordan du får den URL-adressen og vigtige egenskaber, der bruges i anmodningen.

**Svar**

Statuskode: 204 intet indhold returneres for en vellykket svar.

<a name="RunIndexer"></a>
## <a name="run-indexer"></a>Køre indekseringsprogram ##

Ud over kører med jævne mellemrum efter en tidsplan, kan et indekseringsprogram også aktiveres efter behov via handlingen **Køre indekseringsprogram** : 

    POST https://[service name].search.windows.net/indexers/[indexer name]/run?api-version=[api-version]
    api-key: [admin key]

Den `api-version` er påkrævet. Den foreløbige version er `2015-02-28-Preview`. 

Den `api-key` skal være en administrator nøgle (i modsætning til en forespørgsel nøgle). Se afsnittet godkendelse i [Søg Service REST-API](https://msdn.microsoft.com/library/azure/dn798935.aspx) mere at vide om taster. [Opret en søgetjeneste i portalen](search-create-service-portal.md) forklarer, hvordan du får den URL-adressen og vigtige egenskaber, der bruges i anmodningen.

**Svar**

Statuskode: 202 accepterede returneres for en vellykket svar.

<a name="GetIndexerStatus"></a>
## <a name="get-indexer-status"></a>Hente indekseringsprogram Status ##

Handlingen **Hent indekseringsprogram Status** henter aktuelle status og udførelse af oversigten over et indekseringsprogram: 

    GET https://[service name].search.windows.net/indexers/[indexer name]/status?api-version=[api-version]
    api-key: [admin key]


Den `api-version` er påkrævet. Den foreløbige version er `2015-02-28-Preview`. 

Den `api-key` skal være en administrator nøgle (i modsætning til en forespørgsel nøgle). Se afsnittet godkendelse i [Søg Service REST-API](https://msdn.microsoft.com/library/azure/dn798935.aspx) mere at vide om taster. [Opret en søgetjeneste i portalen](search-create-service-portal.md) forklarer, hvordan du får den URL-adressen og vigtige egenskaber, der bruges i anmodningen.

**Svar**

Statuskode: 200 OK for en vellykket svar.

Svar brødteksten indeholder oplysninger om overordnede indekseringsprogram sundhedsstatus, sidste indekseringsprogram aktiveringen samt oversigten over seneste indekseringsprogram starter (hvis den findes). 

Et eksempel svar brødtekst ser sådan ud: 

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

**Indekseringsprogram Status**

Indekseringsprogram status kan være en af følgende værdier:

- `running`Angiver, at indekseringsprogrammet kører normalt. Bemærk, at der er nogle af indekseringsprogram udførelser kan stadig problemer, så det er en god ide at kontrollere den `lastResult` egenskaben samt. 

- `error`Angiver, at indekseringsprogrammet fandt en fejl, der ikke kan rettes uden human brugerinput. Eksempelvis legitimationsoplysningerne for datakilder kan være udløbet eller skemaet af datakilden eller target indekset er blevet ændret i et seneste måde. 

**Indekseringsprogram udførelse af resultat**

Et indekseringsprogram udførelse af resultat indeholder oplysninger om en enkelt indekseringsprogram udførelse af. Seneste resultat fået som den `lastResult` egenskab for indekseringsprogram status. Andre seneste resultater, hvis de findes, returneres som den `executionHistory` egenskab for indekseringsprogram status. 

Indekseringsprogram udførelse af resultatet indeholder følgende egenskaber: 

- `status`: status for en udførelse. Du kan finde oplysninger i [Indekseringsprogram udførelse af Status](#IndexerExecutionStatus) under. 

- `errorMessage`: fejlmeddelelse for en udførelse af mislykkedes. 

- `startTime`: tid i UTC når denne udførelse er startet.

- `endTime`: tid i UTC når denne udførelse af afsluttet. Denne værdi er ikke angivet, hvis udførelsen er stadig er i gang.

- `errors`: en matrix med på elementniveau fejl, hvis en hvilken som helst. Hvert element indeholder en dokumentnøgle (`key` egenskaben) og en fejlmeddelelse (`errorMessage` egenskaben). 

- `itemsProcessed`: antallet fra datakilden elementer (for eksempel tabelrækker), som indekseringsprogrammet forsøgte at indeksere under denne kørsel af. 

- `itemsFailed`: antallet elementer, der opstod en fejl under denne kørsel.  
 
- `initialTrackingState`: altid `null` til første indekseringsprogram udførelse, eller hvis dataene ændres sporing politik er ikke aktiveret for den datakilde, der bruges. Hvis en sådan politik er aktiveret, angiver denne værdi i efterfølgende udførelser den første (laveste) registrering af ændringer behandlet af denne udførelse af værdi. 

- `finalTrackingState`: altid `null` Hvis dataene ændres sporing politik er ikke aktiveret for den datakilde, der bruges. Ellers angiver den seneste (højest) registrering af ændringer er blevet behandlet af denne udførelse af værdi. 

<a name="IndexerExecutionStatus"></a>
**Indekseringsprogram udførelse af Status**

Indekseringsprogram udførelse af status registrerer status for en enkelt indekseringsprogram udførelse af. Det kan indeholde følgende værdier:

- `success`Angiver, at indekseringsprogram udførelsen er fuldført.

- `inProgress`Angiver, at indekseringsprogram udførelsen er i gang. 

- `transientFailure`Angiver, at en indekseringsprogram udførelsen mislykkedes. Se `errorMessage` egenskab for detaljer. Fejlen kan eller ikke kan kræve human brugerinput at løse – for eksempel løse problemer med et skema inkompatibilitet mellem datakilden og mål indekset kræver brugerhandling, mens en midlertidig datakilde nedetid ikke. Indekseringsprogram starter fortsat per tidsplan, hvis der findes. 

- `persistentFailure`Angiver, at indekseringsprogrammet mislykkes på en måde, der kræver human brugerinput. Planlagte indekseringsprogram udførelser stop. Efter tage sig af problemet, kan du bruge nulstille indekseringsprogram API til at genstarte de planlagte udførelser. 

- `reset`Angiver, at indekseringsprogrammet er blevet nulstillet af et opkald for at nulstille indekseringsprogram API (se nedenfor). 

<a name="ResetIndexer"></a>
## <a name="reset-indexer"></a>Nulstille indekseringsprogram ##

Handlingen **Nulstille indekseringsprogram** nulstiller registrering tilstand, der er knyttet til indekseringstjenesten. Dette giver dig mulighed for at udløse fra bunden reindeksere (for eksempel, hvis dine datakildeskemaet er blevet ændret) eller for at ændre politikken data Skift registrering for en datakilde, der er knyttet til indekseringstjenesten.   

    POST https://[service name].search.windows.net/indexers/[indexer name]/reset?api-version=[api-version]
    api-key: [admin key]

Den `api-version` er påkrævet. Den foreløbige version er `2015-02-28-Preview`. 

Den `api-key` skal være en administrator nøgle (i modsætning til en forespørgsel nøgle). Se afsnittet godkendelse i [Søg Service REST-API](https://msdn.microsoft.com/library/azure/dn798935.aspx) mere at vide om taster. [Opret en søgetjeneste i portalen](search-create-service-portal.md) forklarer, hvordan du får den URL-adressen og vigtige egenskaber, der bruges i anmodningen.

**Svar**

Statuskode: 204 intet indhold for en vellykket svar.

## <a name="mapping-between-sql-data-types-and-azure-search-data-types"></a>Tilknytning mellem SQL-datatyper og Azure Søg-datatyper ##

<table style="font-size:12">
<tr>
<td>SQL-datatype</td>  
<td>Tilladt target indeks felttyper</td>
<td>Noter</td>
</tr>
<tr>
<td>bit</td>
<td>Edm.Boolean, Edm.String</td>
<td></td>
</tr>
<tr>
<td>int, smallint, tinyint</td>
<td>Edm.Int32, Edm.Int64, Edm.String</td>
<td></td>
</tr>
<tr>
<td>bigint</td>
<td>Edm.Int64, Edm.String</td>
<td></td>
</tr>
<tr>
<td>reelt tal, slæk</td>
<td>Edm.Double, Edm.String</td>
<td></td>
</tr>
<tr>
<td>Access, penge<br>decimal<br>numeriske
</td>
<td>Edm.String</td>
<td>Azure søgning understøtter ikke konvertere decimal typer til Edm.Double, fordi dette mister præcision
</td>
</tr>
<tr>
<td>CHAR, nchar, varchar, nvarchar</td>
<td>Edm.String<br/>Collection(EDM.String)</td>
<td>Se [Feltet tilknytning af funktioner](#FieldMappingFunctions) i dette dokument for at få oplysninger om, hvordan du transformere en strengkolonne til en Collection(Edm.String)</td>
</tr>
<tr>
<td>smalldatetime datetime datetime2, dato, og datetimeoffset</td>
<td>Edm.DateTimeOffset, Edm.String</td>
<td></td>
</tr>
<tr>
<td>uniqueidentifer</td>
<td>Edm.String</td>
<td></td>
</tr>
<tr>
<td>Geografi</td>
<td>Edm.GeographyPoint</td>
<td>Understøttes kun Geografi forekomster af typen punkt med SRID 4326 (som er standard)</td>
</tr>
<tr>
<td>med navnet ROWVERSION</td>
<td>I/T.</td>
<td>Række-version kolonner kan ikke gemmes i søgeindekset, men de kan bruges til registrering af ændringer</td>
</tr>
<tr>
<td>tid, timespan<br>binær, varbinary, billede,<br>XML, geometri, CLR-datatyper</td>
<td>I/T.</td>
<td>Ikke understøttet</td>
</tr>
</table>

## <a name="mapping-between-json-data-types-and-azure-search-data-types"></a>Tilknytning mellem JSON-datatyper og Azure Søg-datatyper ##

<table style="font-size:12">
<tr>
<td>JSON-datatype</td> 
<td>Tilladt target indeks felttyper</td>
<td>Noter</td>
</tr>
<tr>
<td>Boolesk</td>
<td>Edm.Boolean, Edm.String</td>
<td></td>
</tr>
<tr>
<td>Integreret tal</td>
<td>Edm.Int32, Edm.Int64, Edm.String</td>
<td></td>
</tr>
<tr>
<td>Flydende tal</td>
<td>Edm.Double, Edm.String</td>
<td></td>
</tr>
<tr>
<td>streng</td>
<td>Edm.String</td>
<td></td>
</tr>
<tr>
<td>matrixer af primitiv skriver fx ["a", "b", "c"]</td>
<td>Collection(EDM.String)</td>
<td></td>
</tr>
<tr>
<td>Strenge, der ligner datoer</td>
<td>Edm.DateTimeOffset, Edm.String</td>
<td></td>
</tr>
<tr>
<td>GeoJSON punkt objekter</td>
<td>Edm.GeographyPoint</td>
<td>GeoJSON punkter er JSON objekter i følgende format: {"type": "Punkt", "koordinater": [lang, lat]} </td>
</tr>
<tr>
<td>Andre JSON-objekter</td>
<td>I/T.</td>
<td>Understøttes ikke. Azure søgning understøtter i øjeblikket kun basistyper og streng af websteder</td>
</tr>
</table>