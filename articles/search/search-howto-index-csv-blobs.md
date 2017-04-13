<properties
pageTitle="Indeksering csv-BLOB med Azure Søg blob indekseringsprogram | Microsoft Azure"
description="Lær, hvordan du indeksere csv-BLOB med Azure søgning"
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
ms.date="07/12/2016"
ms.author="eugenesh" />

# <a name="indexing-csv-blobs-with-azure-search-blob-indexer"></a>Indeksering csv-BLOB med Azure Søg blob indekseringsprogram 

Som standard afgrænset [søgning Azure blob indekseringsprogram](search-howto-indexing-azure-blob-storage.md) fortolker tekst BLOB som et enkelt afsnit med tekst. Dog med blob, der indeholder CSV-data, vil du ofte behandle hver linje i blob som en separat dokument. For eksempel givet følgende afgrænset tekst: 

    id, datePublished, tags
    1, 2016-01-12, "azure-search,azure,cloud" 
    2, 2016-07-07, "cloud,mobile" 

kan du vil analysere i 2 dokumenter, hver med "-id", "datePublished" og "koder" felter.

I denne artikel lærer du at fortolke csv-BLOB med et Søg Azure blob indekseringsprogram. 

> [AZURE.IMPORTANT] Denne funktionalitet er i øjeblikket i Vis udskrift. Det er kun tilgængelig i den REST-API ved hjælp af version **2015-02-28-Preview**. Skal du huske, skal du se et eksempel API'er er beregnet til test og evaluering, og der ikke bør benyttes i fremstilling miljøer. 

## <a name="setting-up-csv-indexing"></a>Konfiguration af csv-indeksering

At indeksere csv-blob, oprette eller opdatere en indekseringsprogram definition med den `delimitedText` parsing af tilstand:  

    {
      "name" : "my-csv-indexer",
      ... other indexer properties
      "parameters" : { "configuration" : { "parsingMode" : "delimitedText", "firstLineContainsHeaders" : true } }
    }

Se [Oprette indekseringsprogram](search-api-indexers-2015-02-28-preview.md#create-indexer)kan finde flere oplysninger om API'EN oprette indekset.

`firstLineContainsHeaders`Angiver, at den første linje (ikke-tomme) i hver blob indeholder overskrifter.
Hvis BLOB ikke indeholder en indledende sidehovedlinje, skal der angives overskrifterne i indekseringsprogram konfigurationen: 

    "parameters" : { "configuration" : { "parsingMode" : "delimitedText", "delimitedTextHeaders" : "id,datePublished,tags" } } 

På nuværende tidspunkt kun den UTF-8 kodning understøttes. Desuden kun komma `','` tegn understøttes som afgrænseren. Hvis du har brug for support til andre kodninger eller afgrænsere, skal du fortælle os, på [vores UserVoice-websted](https://feedback.azure.com/forums/263029-azure-search).

> [AZURE.IMPORTANT] Når du bruger den afgrænsede tekst parsing af tilstand, antages Azure Søg det, at alle BLOB i datakilden bliver CSV. Hvis du har brug at understøtte en blanding af csv- og ikke-csv-BLOB i den samme datakilde, skal du fortælle os, på [vores UserVoice-websted](https://feedback.azure.com/forums/263029-azure-search).

## <a name="request-examples"></a>Anmode om eksempler

Lægge det alle sammen, er her eksemplerne fuldført data. 

Datakilde: 

    POST https://[service name].search.windows.net/datasources?api-version=2015-02-28-Preview
    Content-Type: application/json
    api-key: [admin key]

    {
        "name" : "my-blob-datasource",
        "type" : "azureblob",
        "credentials" : { "connectionString" : "<my storage connection string>" },
        "container" : { "name" : "my-container", "query" : "<optional, my-folder>" }
    }   

Indekseringsprogram:

    POST https://[service name].search.windows.net/indexers?api-version=2015-02-28-Preview
    Content-Type: application/json
    api-key: [admin key]

    {
      "name" : "my-csv-indexer",
      "dataSourceName" : "my-blob-datasource",
      "targetIndexName" : "my-target-index",
      "parameters" : { "configuration" : { "parsingMode" : "delimitedText", "delimitedTextHeaders" : "id,datePublished,tags" } }
    }

## <a name="help-us-make-azure-search-better"></a>Hjælp os med at forbedre Azure søgning

Hvis du har anmode om funktioner eller ideer til forbedringer, skal du Henvend dig til os på vores [UserVoice websted](https://feedback.azure.com/forums/263029-azure-search/).