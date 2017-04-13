<properties
pageTitle="Indeksering Azure Table Storage med Azure søgning"
description="Lær, hvordan du indeksere data gemt i Azure tabeller med Azure søgning"
services="search"
documentationCenter=""
authors="chaosrealm"
manager="pablocas"
editor="" />

<tags
ms.service="search"
ms.devlang="rest-api"
ms.workload="search" ms.topic="article"  
ms.tgt_pltfrm="na"
ms.date="08/16/2016"
ms.author="eugenesh" />

# <a name="indexing-azure-table-storage-with-azure-search"></a>Indeksering Azure Table Storage med Azure søgning

I denne artikel viser, hvordan du bruger Azure Søg til at indeksere data, der er gemt i Azure-Tabellager. Det nye Azure Søg tabel indekseringsprogram gør denne proces hurtig og problemfri. 

> [AZURE.IMPORTANT] Denne funktionalitet er i øjeblikket i Vis udskrift. Det er kun tilgængeligt i den REST-API ved hjælp af version **2015-02-28-Preview** og i version 2.0-udskrift i .NET SDK. Skal du huske, skal du se et eksempel API'er er beregnet til test og evaluering, og der ikke bør benyttes i fremstilling miljøer.

## <a name="setting-up-azure-table-indexing"></a>Konfiguration af Azure table indeksering

Hvis du vil indstille og konfigurere et Azure table indekseringsprogram, kan du bruge Azure Search REST-API til at oprette og administrere **indeks** og **datakilder** , som beskrevet i [indekseringsprogram handlinger](https://msdn.microsoft.com/library/azure/dn946891.aspx). Du kan også bruge [version 2.0-udskrift](https://msdn.microsoft.com/library/mt761536%28v=azure.103%29.aspx) af .NET SDK. Fremover, understøttelse af indeksering tabel, føjes til Azure-portalen.

En datakilde angiver, hvilke data der skal indekseres, legitimationsoplysninger, der er behov for at få adgang til data og politikker, der aktiverer Azure søgningen til effektivt at identificere ændringer i data (nye, ændret eller slettet rækker).

Et indekseringsprogram læser data fra en datakilde og indlæses i en målliste søgeindekset.

Sådan opsættes tabel indeksering

1. Oprette en datakilde
    - Angiv den `type` -parameter til`azuretable`
    - Overføre i din lagerplads konto forbindelsesstreng som den `credentials.connectionString` parameter
    - Angiv den tabel navn ved hjælp af den `container.name` parameter
    - Du kan også angive en forespørgsel ved hjælp af den `container.query` parameter. Når det er muligt, at bruge et filter på PartitionKey til den bedste ydelse en anden forespørgsel medfører en fuld tabelscanning, hvilket kan medføre dårlig ydeevne for store tabeller.
2. Oprette en søgeindekset med skemaet, der svarer til kolonner i tabellen, som du vil indeksere. 
3. Oprette indekseringstjenesten ved at oprette forbindelse til datakilden til søgeindekset.

### <a name="create-data-source"></a>Oprette datakilde

    POST https://[service name].search.windows.net/datasources?api-version=2015-02-28-Preview
    Content-Type: application/json
    api-key: [admin key]

    {
        "name" : "table-datasource",
        "type" : "azuretable",
        "credentials" : { "connectionString" : "<my storage connection string>" },
        "container" : { "name" : "my-table", "query" : "PartitionKey eq '123'" }
    }   

Se [Oprette datakilden](search-api-indexers-2015-02-28-preview.md#create-data-source)til flere oplysninger om datakilden oprette API.

### <a name="create-index"></a>Oprette indeks 

    POST https://[service name].search.windows.net/indexes?api-version=2015-02-28
    Content-Type: application/json
    api-key: [admin key]

    {
        "name" : "my-target-index",
        "fields": [
            { "name": "key", "type": "Edm.String", "key": true, "searchable": false },
            { "name": "SomeColumnInMyTable", "type": "Edm.String", "searchable": true }
        ]
    }

Få mere at vide under oprette indekset API, se [Create Index](https://msdn.microsoft.com/library/dn798941.aspx)

### <a name="create-indexer"></a>Oprette indekset 

Til sidst skal oprette den indekseringsprogram, der refererer til datakilden og mål indekset. Eksempel:

    POST https://[service name].search.windows.net/indexers?api-version=2015-02-28-Preview
    Content-Type: application/json
    api-key: [admin key]

    {
      "name" : "table-indexer",
      "dataSourceName" : "table-datasource",
      "targetIndexName" : "my-target-index",
      "schedule" : { "interval" : "PT2H" }
    }

Se [Oprette indekseringsprogram](search-api-indexers-2015-02-28-preview.md#create-indexer)kan finde flere oplysninger om API'EN oprette indekset.

Det er alt, der er til den - tilfreds indeksering!

## <a name="dealing-with-different-field-names"></a>Håndtere forskellige feltnavne

Feltnavne i din eksisterende indeks kan ofte være forskellige fra Egenskabsnavnene i din tabel. Du kan bruge **tilknytninger** tilknytte egenskabsnavne fra tabellen til feltnavne i dit søgeindeks. Hvis du vil vide mere om tilknytninger, kan du se [tilknytninger Azure Søg indekseringsprogram bygge bro mellem forskellene mellem datakilder og søgeindeks](search-indexer-field-mappings.md).

## <a name="handling-document-keys"></a>Håndtering af dokument taster

I Azure Søg identificerer tasten dokument entydigt et dokument. Hver søgeindekset skal have ét nøgle-felt af typen `Edm.String`. Nøgle-feltet er påkrævet for hvert dokument, der bliver føjet til indekset (faktisk det er det eneste påkrævede felt).

Da tabelrækker, der har en sammensat nøgle, Azure Søg genererer en korte felt med navnet `Key` , der er en sammenkædning af partition nøgle og række nøgleværdier. Eksempelvis hvis rækken er PartitionKey er `PK1` og RowKey er `RK1`, derefter `Key` feltets værdi bliver `PK1RK1`. 

> [AZURE.NOTE] Den `Key` værdi kan indeholde tegn, der er ugyldige i dokumentet nøgler, som stiplet. Du kan håndtere ugyldige tegn ved hjælp af den `base64Encode` [felt tilknytning funktionen](search-indexer-field-mappings.md#base64EncodeFunction). Hvis du gør dette, Husk også at bruge URL-adressen sikker Base64 kodning, når der passerer dokument taster i API ringer som opslag.

## <a name="incremental-indexing-and-deletion-detection"></a>Trinvis registrering af indeksering og sletning
 
Når du opretter en tabel indekseringsprogram til at køre en tidsplan, den reindexes kun nye eller opdaterede rækker, som bestemt af en række `Timestamp` værdi. Du behøver ikke at angive en politik til ændring registrering af – trinvise indeksering er aktiveret for dig automatisk. 

Du kan angive, at bestemte dokumenter skal fjernes fra indekset, kan du bruge en strategi for bløde Slet – i stedet for at slette en række ved at tilføje en egenskab for at angive, at det er slettet, og konfigurere en blød sletningen registrering politik på datakilden. For eksempel politikken, som vist nedenfor vil overveje, en række slettes, hvis den har en egenskab `IsDeleted` med værdien `"true"`: 

    PUT https://[service name].search.windows.net/datasources?api-version=2015-02-28-Preview
    Content-Type: application/json
    api-key: [admin key]
    
    {
        "name" : "my-table-datasource",
        "type" : "azuretable",
        "credentials" : { "connectionString" : "<your storage connection string>" },
        "container" : { "name" : "table name", "query" : "query" },
        "dataDeletionDetectionPolicy" : { "@odata.type" : "#Microsoft.Azure.Search.SoftDeleteColumnDeletionDetectionPolicy", "softDeleteColumnName" : "IsDeleted", "softDeleteMarkerValue" : "true" }
    }   


## <a name="help-us-make-azure-search-better"></a>Hjælp os med at forbedre Azure søgning

Hvis du har anmode om funktioner eller ideer til forbedringer, skal du Henvend dig til os på vores [UserVoice websted](https://feedback.azure.com/forums/263029-azure-search/).