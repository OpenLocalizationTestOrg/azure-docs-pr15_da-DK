<properties
pageTitle="Indeksering JSON BLOB med Azure Søg blob indekseringsprogram"
description="Indeksering JSON BLOB med Azure Søg blob indekseringsprogram"
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
ms.date="07/26/2016"
ms.author="eugenesh" />

# <a name="indexing-json-blobs-with-azure-search-blob-indexer"></a>Indeksering JSON BLOB med Azure Søg blob indekseringsprogram 

I denne artikel viser, hvordan du konfigurerer søgning Azure blob indekseringsprogram at udtrække strukturerede indhold fra blob, der indeholder JSON.

## <a name="scenarios"></a>Scenarier

Som standard fortolker [Søg Azure blob indekseringsprogram](search-howto-indexing-azure-blob-storage.md) JSON BLOB som et enkelt afsnit med tekst. Ofte, vil du bevare strukturen i din JSON-dokumenter. For eksempel givet JSON-dokument 

    { 
        "article" : {
            "text" : "A hopefully useful article explaining how to parse JSON blobs",
            "datePublished" : "2016-04-13" 
            "tags" : [ "search", "storage", "howto" ]    
        }
    }

du måske at sortere i en Azure Søg i dokument med "tekst", "datePublished" og "koder" felter.

Når din BLOB indeholder en **matrix med JSON-objekter**, kan du også vil hvert element i matrixen at blive en separat Azure Søg i dokument. For eksempel givet en blob med denne JSON:  

    [
        { "id" : "1", "text" : "example 1" },
        { "id" : "2", "text" : "example 2" },
        { "id" : "3", "text" : "example 3" }
    ]

Du kan udfylde dit Azure søgeindeks med 3 separate dokumenter, hver med felterne "-id" og "tekst". 

> [AZURE.IMPORTANT] Denne funktionalitet er i øjeblikket i Vis udskrift. Det er kun tilgængelig i den REST-API ved hjælp af version **2015-02-28-Preview**. Skal du huske, skal du se et eksempel API'er er beregnet til test og evaluering, og der ikke bør benyttes i fremstilling miljøer. 

## <a name="setting-up-json-indexing"></a>Konfiguration af JSON indeksering

Indeksere JSON blob, skal du angive den `parsingMode` konfigurationsparameter til `json` (for at indeksere hver blob som et enkelt dokument) eller `jsonArray` (Hvis din BLOB indeholder en JSON matrix): 

    {
      "name" : "my-json-indexer",
      ... other indexer properties
      "parameters" : { "configuration" : { "parsingMode" : "json" | "jsonArray" } }
    }

Hvis det er nødvendigt, Brug **tilknytninger** til at vælge egenskaberne for kildedokumentet JSON bruges til at udfylde søgeindekset dine mål.  Dette er beskrevet i detaljeret nedenfor. 

> [AZURE.IMPORTANT] Når du bruger `json` eller `jsonArray` parsing af tilstand, Azure Søg antager, at alle BLOB i datakilden bliver JSON. Hvis du har brug at understøtte en blanding af JSON og ikke JSON BLOB i den samme datakilde, skal du fortælle os, på [vores UserVoice-websted](https://feedback.azure.com/forums/263029-azure-search).

## <a name="using-field-mappings-to-build-search-documents"></a>Brug af tilknytninger til at opbygge Søg i dokumenter 

I øjeblikket Azure-søgning kan ikke indeksere vilkårlig JSON dokumenter direkte, fordi den understøtter kun enkle datatyper, streng matrixer og GeoJSON punkter. Du kan dog bruge **tilknytninger** til at vælge dele af dokumentet JSON og "løft" dem til på øverste niveau felter i Søg i dokumentet. Få felt tilknytninger grundlæggende om under [tilknytninger Azure Søg indekseringsprogram bygge bro mellem forskellene mellem datakilder og søgeindeks](search-indexer-field-mappings.md).

Kommer tilbage til vores eksempel JSON dokument: 

    { 
        "article" : {
            "text" : "A hopefully useful article explaining how to parse JSON blobs",
            "datePublished" : "2016-04-13" 
            "tags" : [ "search", "storage", "howto" ]    
        }
    }

Antag, at du har en søgeindekset med følgende felter: `text` af typen Edm.String, `date` af typen Edm.DateTimeOffset, og `tags` af typen Collection(Edm.String). For at tilknytte din JSON til den ønskede figur skal du bruge følgende felttilknytninger: 

    "fieldMappings" : [ 
        { "sourceFieldName" : "/article/text", "targetFieldName" : "text" },
        { "sourceFieldName" : "/article/datePublished", "targetFieldName" : "date" },
        { "sourceFieldName" : "/article/tags", "targetFieldName" : "tags" }
    ]

Feltnavnene kilde i sektionen tilknytninger er angivet ved hjælp af [JSON markøren](http://tools.ietf.org/html/rfc6901) notation. Du starte med en skråstreg til at referere til i roden af din JSON-dokument og derefter analysere i den ønskede egenskab (på vilkårlig niveau i indlejring) ved hjælp af videresende skråstreg adskilt sti. 

Du kan også referere til individuelle array-elementer ved hjælp af et indeks med baseret på nul. For eksempel for at vælge det første element i matrixen "koder" fra eksemplet ovenfor, bruge en felttilknytning således:

    { "sourceFieldName" : "/article/tags/0", "targetFieldName" : "firstTag" }

> [AZURE.NOTE] Hvis et kilde feltnavn i et felt tilknytning path refererer til en egenskab, der ikke findes i JSON, ignoreres tilknytningen uden en fejl. Dette er færdig, så vi kan understøtte dokumenter med et andet skema (som er en fælles use case). Fordi der ikke er nogen validering, skal du sørge for at undgå slåfejl i feltet tilknytning specifikationen. 

Hvis dine JSON-dokumenter indeholder kun egenskaber for enkel på øverste niveau, kan du ikke skal tilknytninger overhovedet. Eksempelvis hvis din JSON ser sådan ud, knyttes på øverste niveau egenskaber "tekst", "datePublished" og "koder" direkte til de tilsvarende felter i søgeindekset: 
 
    { 
       "text" : "A hopefully useful article explaining how to parse JSON blobs",
       "datePublished" : "2016-04-13" 
       "tags" : [ "search", "storage", "howto" ]    
    }

## <a name="indexing-nested-json-arrays"></a>Indeksering indlejrede JSON matrixer

Hvad nu, hvis du vil indeksere en matrix med JSON objekter, men denne matrix er indlejret et sted i dokumentet? Du kan vælge egenskaben, der indeholder den matrix ved hjælp af den `documentRoot` konfigurationsegenskab. For eksempel, hvis din BLOB ser sådan ud: 

    { 
        "level1" : {
            "level2" : [
                { "id" : "1", "text" : "Use the documentRoot property" }, 
                { "id" : "2", "text" : "to pluck the array you want to index" },
                { "id" : "3", "text" : "even if it's nested inside the document" }  
            ]
        }
    } 

Brug denne konfiguration indeksere den matrix, der er indeholdt i egenskaben "level2": 

    {
        "name" : "my-json-array-indexer",
        ... other indexer properties
        "parameters" : { "configuration" : { "parsingMode" : "jsonArray", "documentRoot" : "/level1/level2" } }
    }


## <a name="request-examples"></a>Anmode om eksempler

Lægge det alle sammen, er fuldført overførsler eksemplerne her. 

Datakilde: 

    POST https://[service name].search.windows.net/datasources?api-version=2015-02-28-Preview
    Content-Type: application/json
    api-key: [admin key]

    {
        "name" : "my-blob-datasource",
        "type" : "azureblob",
        "credentials" : { "connectionString" : "<my storage connection string>" },
        "container" : { "name" : "my-container", "query" : "optional, my-folder" }
    }   

Indekseringsprogram:

    POST https://[service name].search.windows.net/indexers?api-version=2015-02-28-Preview
    Content-Type: application/json
    api-key: [admin key]

    {
      "name" : "my-json-indexer",
      "dataSourceName" : "my-blob-datasource",
      "targetIndexName" : "my-target-index",
      "schedule" : { "interval" : "PT2H" },
      "parameters" : { "configuration" : { "useJsonParser" : true } }, 
      "fieldMappings" : [ 
        { "sourceFieldName" : "/article/text", "targetFieldName" : "text" },
        { "sourceFieldName" : "/article/datePublished", "targetFieldName" : "date" },
        { "sourceFieldName" : "/article/tags", "targetFieldName" : "tags" }
      ]
    }

## <a name="help-us-make-azure-search-better"></a>Hjælp os med at forbedre Azure søgning

Hvis du har anmode om funktioner eller ideer til forbedringer, skal du Henvend dig til os på vores [UserVoice websted](https://feedback.azure.com/forums/263029-azure-search/).