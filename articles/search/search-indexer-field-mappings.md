<properties
pageTitle="Felttilknytninger i Azure Søg indeks"
description="Konfigurere Azure Søg indekseringsprogram felttilknytninger for at tage højde for forskelle i feltnavne og repræsentationer af data"
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
ms.date="10/17/2016"
ms.author="eugenesh" />

# <a name="field-mappings-in-azure-search-indexers"></a>Felttilknytninger i Azure Søg indeks

Når du bruger Azure Søg indeks, kan du sommetider finde dig selv i situationer, hvor dataene input helt ikke overens med skemaet til destinationen indekset. I disse tilfælde kan du bruge **tilknytninger** til at transformere dataene til den ønskede figur. 

Nogle situationer, hvor felttilknytninger er nyttige:
 
- Datakilden er et felt `_id`, men Azure Søg ikke tillader, at, hvis feltnavne begynder med et understregningstegn. Tilknytte en felter kan man "omdøbe" et felt. 
- Du vil udfylde flere felter i indekset med de samme data kildedata, for eksempel, fordi du vil anvende forskellige programmer til analyse til disse felter. Tilknytninger kan du "deler sig" et datafelt kilde.
- Du har brug for til Base64 kode eller afkode dine data. Felttilknytninger understøtter flere **tilknytning funktioner**, herunder funktioner til Base64 kodningen og kodning.   


> [AZURE.IMPORTANT] Feltet tilknytninger funktionalitet er i øjeblikket, i Vis udskrift. Det er kun tilgængelig i den REST-API ved hjælp af version **2015-02-28-Preview**. Skal du huske, skal du se et eksempel API'er er beregnet til test og evaluering, og der ikke bør benyttes i fremstilling miljøer.

## <a name="setting-up-field-mappings"></a>Konfiguration af tilknytninger

Du kan tilføje tilknytninger, når du opretter en ny indekseringsprogram ved hjælp af [Opret indekseringsprogram](search-api-indexers-2015-02-28-preview.md#create-indexer) -API'EN. Du kan administrere tilknytninger på et indeksering indekseringsprogram ved hjælp af [Update indekseringsprogram](search-api-indexers-2015-02-28-preview.md#update-indexer) -API'EN. 

Tilknytte en felter består af 3 dele: 

1. A `sourceFieldName`, som repræsenterer et felt i datakilden. Denne egenskab er påkrævet. 

2. En valgfri `targetFieldName`, som repræsenterer et felt i dit søgeindeks. Hvis udelades, bruges det samme navn som datakilde. 

3. En valgfri `mappingFunction`, som kan transformere dataene ved hjælp af en af de mange foruddefinerede funktioner. Den komplette liste over funktioner er [nedenfor](#mappingFunctions).

Felterne tilknytninger føjes til den `fieldMappings` matrix på indekseringsprogram definition. 

For eksempel er her, hvordan du kan tilpasse forskelle i feltnavne: 

```JSON

PUT https://[service name].search.windows.net/indexers/myindexer?api-version=[api-version]
Content-Type: application/json
api-key: [admin key]
{
    "dataSourceName" : "mydatasource",
    "targetIndexName" : "myindex",
    "fieldMappings" : [ { "sourceFieldName" : "_id", "targetFieldName" : "id" } ] 
} 
```

Et indekseringsprogram kan have flere tilknytninger. Eksempelvis her er hvordan du kan "deler sig" et felt:

```JSON

"fieldMappings" : [ 
    { "sourceFieldName" : "text", "targetFieldName" : "textStandardEnglishAnalyzer" },
    { "sourceFieldName" : "text", "targetFieldName" : "textSoundexAnalyzer" }, 
] 
```

> [AZURE.NOTE] Azure søgningen anvender case-insensitive sammenligning til at løse felt og funktionen navnene i felttilknytninger. Det er praktisk (du ikke behøver at hente alle hus højre), men det betyder, at din datakilde eller indeks ikke må indeholde felter, der er forskellige kun af store og små bogstaver.  

<a name="mappingFunctions"></a>
## <a name="field-mapping-functions"></a>Feltet tilknytning funktioner

Disse funktioner understøttes i øjeblikket: 

- [base64Encode](#base64EncodeFunction)
- [base64Decode](#base64DecodeFunction)
- [extractTokenAtPosition](#extractTokenAtPositionFunction)
- [jsonArrayToStringCollection](#jsonArrayToStringCollectionFunction)

<a name="base64EncodeFunction"></a>
### <a name="base64encode"></a>base64Encode 

Udfører *URL-adressen sikker* Base64 kodning input strengens. Antager, at input er UTF-8-kodet. 

#### <a name="sample-use-case"></a>Eksempel på Brug store og små bogstaver 

Kun URL-adressen sikker tegn kan vises i en Azure Søg dokumentnøgle (fordi kunder skal kunne dokumentet ved hjælp af API opslag for eksempel en adresse). Hvis dine data indeholder URL-adresse-usikre tegn, og du vil bruge til at udfylde en nøgle-feltet i dit søgeindeks, Brug denne funktion.   

#### <a name="example"></a>Eksempel 

```JSON

"fieldMappings" : [ 
  { 
    "sourceFieldName" : "Path", 
    "targetFieldName" : "UrlSafePath",
    "mappingFunction" : { "name" : "base64Encode" } 
  }] 
```

<a name="base64DecodeFunction"></a>
### <a name="base64decode"></a>base64Decode

Udfører Base64 kodning input strengens. Input antages til et *URL-adressen sikker* Base64-kodet streng. 

#### <a name="sample-use-case"></a>Eksempel på Brug store og små bogstaver 

BLOB brugerdefinerede metadata værdierne skal være ASCII-kodet. Du kan bruge Base64 kodning til at repræsentere vilkårlig Unicode-strenge i blob brugerdefinerede metadata. For at gøre søgningen giver mening, kan du dog Brug denne funktion lave kodede data tilbage til "normalt" strenge, når udfylde dit søgeindeks.  

#### <a name="example"></a>Eksempel 

```JSON

"fieldMappings" : [ 
  { 
    "sourceFieldName" : "Base64EncodedMetadata", 
    "targetFieldName" : "SearchableMetadata",
    "mappingFunction" : { "name" : "base64Decode" } 
  }] 
```

<a name="extractTokenAtPositionFunction"></a>
### <a name="extracttokenatposition"></a>extractTokenAtPosition

Opdeler en strengfelt, ved hjælp af et angivet separatortegn, og henter tokenet på den angivne placering i den resulterende Opdel.

Eksempelvis hvis input er `Jane Doe`, `delimiter` er `" "`(mellemrum) og `position` er 0, er resultatet `Jane`; Hvis den `position` er 1, er resultatet `Doe`. Hvis placeringen refererer til et token, der ikke findes, returneres en fejl.

#### <a name="sample-use-case"></a>Eksempel på Brug store og små bogstaver 

Datakilden indeholder en `PersonName` , og du vil indeksere som to separate `FirstName` og `LastName` felter. Du kan bruge denne funktion til at opdele input brug af mellemrumstegn som afgrænseren.

#### <a name="parameters"></a>Parametre

- `delimiter`: en streng, der skal bruges som separator, når du opdeler input strengen.
- `position`: et heltal baseret på nul placeringen af tokenet til at vælge når input strengen er opdelt.    

#### <a name="example"></a>Eksempel

```JSON 

"fieldMappings" : [ 
  { 
    "sourceFieldName" : "PersonName", 
    "targetFieldName" : "FirstName",
    "mappingFunction" : { "name" : "extractTokenAtPosition", "parameters" : { "delimiter" : " ", "position" : 0 } } 
  }, 
  { 
    "sourceFieldName" : "PersonName", 
    "targetFieldName" : "LastName",
    "mappingFunction" : { "name" : "extractTokenAtPosition", "parameters" : { "delimiter" : " ", "position" : 1 } } 
  }] 
```

<a name="jsonArrayToStringCollectionFunction"></a>
### <a name="jsonarraytostringcollection"></a>jsonArrayToStringCollection

Transformerer en streng, der er formateret som en JSON matrix af strenge i en strengmatrix, der kan bruges til at udfylde en `Collection(Edm.String)` i indekset. 

Eksempelvis hvis strengen input er `["red", "white", "blue"]`, derefter destinationsfeltet af typen `Collection(Edm.String)` udfyldes med de tre værdier `red`, `white` og `blue`. For inputværdier, der ikke kan fortolkes som JSON streng matrixer, returneres en fejl. 

#### <a name="sample-use-case"></a>Eksempel på Brug store og små bogstaver

Azure SQL-database ikke har en indbygget datatype, der er tilknyttet naturligt `Collection(Edm.String)` felter i Azure Søg. Formatere dataene som en JSON strengmatrix for at udfylde strenge samling felter, og Brug denne funktion. 

#### <a name="example"></a>Eksempel 

```JSON

"fieldMappings" : [ 
  { "sourceFieldName" : "tags", "mappingFunction" : { "name" : "jsonArrayToStringCollection" } }
] 
```

## <a name="help-us-make-azure-search-better"></a>Hjælp os med at forbedre Azure søgning

Hvis du har anmode om funktioner eller ideer til forbedringer, skal du Henvend dig til os på vores [UserVoice websted](https://feedback.azure.com/forums/263029-azure-search/).