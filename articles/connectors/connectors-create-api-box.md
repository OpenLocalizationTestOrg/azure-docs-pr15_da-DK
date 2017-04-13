<properties
    pageTitle="Tilføj feltet forbindelsen til din logik Apps | Microsoft Azure"
    description="Oversigt over feltet forbindelsen med REST-API parametre"
    services=""
    documentationCenter="" 
    authors="MandiOhlinger"
    manager="erikre"
    editor=""
    tags="connectors"/>

<tags
   ms.service="multiple"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na" 
   ms.date="08/18/2016"
   ms.author="mandia"/>

# <a name="get-started-with-the-box-connector"></a>Komme i gang med connector boks
Opretter forbindelse til, og oprette filer og Slet filer. 

>[AZURE.NOTE] Denne version i denne artikel gælder for logik apps 2015-08-01-skema prøveversionen.

Med feltet kan du:

- Opbyg din virksomhed flow baseret på de data, du får fra feltet. 
- Bruge udløsere, når en fil oprettes eller opdateres.
- Brug af handlinger, der kopiere en fil, skal du slette en fil og meget mere. Disse handlinger får du respons, og foretag derefter output tilgængelig for andre handlinger. For eksempel, når en fil ændres i feltet, kan du gøre denne fil og sende det ved hjælp af Office 365.

For at tilføje en handling i logik apps skal du se [oprette en logik app](../app-service-logic/app-service-logic-create-a-logic-app.md).

## <a name="triggers-and-actions"></a>Udløsere og handlinger
Feltet indeholder følgende udløser og handlinger.

| Udløsere | Handlinger|
| --- | --- |
|<ul><li>Når der oprettes en fil</li><li>Når en fil er blevet ændret</li></ul> | <ul><li>Opret fil</li><li>Når der oprettes en fil</li><li>Kopiere fil</li><li>Slette filer</li><li>Udtrække arkiv til mappe</li><li>Få filindhold ved hjælp af id</li><li>Få filindhold ved hjælp af sti</li><li>Få filens metadata ved hjælp af id</li><li>Få filmetadata ved hjælp af sti</li><li>Opdatere fil</li><li>Når en fil er blevet ændret</li></ul>

Alle forbindelser understøtter data i JSON og XML-formater.

## <a name="create-a-connection-to-box"></a>Oprette en forbindelse til feltet
Når du tilføjer denne forbindelse til din logik apps, skal du tillade logik apps opretter forbindelse til din.

>[AZURE.INCLUDE [Steps to create a connection to box](../../includes/connectors-create-api-box.md)]

Når du opretter forbindelsen, kan du angive egenskaber for. **REST-API reference** i dette emne beskrives disse egenskaber.

>[AZURE.TIP] Du kan bruge denne samme feltet forbindelse i andre logik apps.

## <a name="swagger-rest-api-reference"></a>Swagger REST-API reference
Gælder for version: 1.0.

### <a name="create-file"></a>Opret fil
Overfører en fil til feltet.  
```POST: /datasets/default/files```

| Navn|Datatype|Påkrævet|Placeret i|Standardværdi|Beskrivelse|
| ---|---|---|---|---|---|
|Mappesti|streng|Ja|forespørgsel|Ingen |Stien til at overføre filen til feltet|
|Navn|streng|Ja|forespørgsel|Ingen |Navnet på filen for at oprette i feltet|
|brødteksten|String(Binary) |Ja|brødteksten|Ingen |Indholdet af filen for at overføre til feltet|

#### <a name="response"></a>Svar
|Navn|Beskrivelse|
|---|---|
|200|Ok|
|standard|Mislykkedes.|


### <a name="when-a-file-is-created"></a>Når der oprettes en fil
Aktiverer et flow, når der oprettes en ny fil i en mappe i feltet.  
```GET: /datasets/default/triggers/onnewfile```

| Navn|Datatype|Påkrævet|Placeret i|Standardværdi|Beskrivelse|
| ---|---|---|---|---|---|
|folderId|streng|Ja|forespørgsel|Ingen |Entydigt id for mappen i feltet|

#### <a name="response"></a>Svar
|Navn|Beskrivelse|
|---|---|
|200|Ok|
|standard|Mislykkedes.|


### <a name="copy-file"></a>Kopiere fil
Kopierer en fil til.  
```POST: /datasets/default/copyFile```

| Navn|Datatype|Påkrævet|Placeret i|Standardværdi|Beskrivelse|
| ---|---|---|---|---|---|
|kilde|streng|Ja|forespørgsel|Ingen |URL-adressen til kildefilen|
|destination|streng|Ja|forespørgsel| Ingen|Destination filstien i feltet, herunder target filnavn|
|overskrive|Boolesk værdi|Nej|forespørgsel| Ingen|Overskriver destinationsfilen, hvis er angivet til "true"|

#### <a name="response"></a>Svar
|Navn|Beskrivelse|
|---|---|
|200|Ok|
|standard|Mislykkedes.|


### <a name="delete-file"></a>Slette filer
Sletter en fil fra feltet.  
```DELETE: /datasets/default/files/{id}```


| Navn|Datatype|Påkrævet|Placeret i|Standardværdi|Beskrivelse|
| ---|---|---|---|---|---|
|id|streng|Ja|sti|Ingen |Entydigt id for filen for at slette fra feltet|

#### <a name="response"></a>Svar
|Navn|Beskrivelse|
|---|---|
|200|Ok|
|standard|Mislykkedes.|


### <a name="extract-archive-to-folder"></a>Udtrække arkiv til mappe
Henter en arkivfilen til en anden mappe i feltet (eksempel: .zip).  
```POST: /datasets/default/extractFolderV2```

| Navn|Datatype|Påkrævet|Placeret i|Standardværdi|Beskrivelse|
| ---|---|---|---|---|---|
|kilde|streng|Ja|forespørgsel| |Stien til arkivfilen|
|destination|streng|Ja|forespørgsel| |Stien i feltet til at udtrække indholdet arkiv|
|overskrive|Boolesk værdi|Nej|forespørgsel| |Overskriver at destinationsfilen, hvis er angivet til "true"|

#### <a name="response"></a>Svar
|Navn|Beskrivelse|
|---|---|
|200|Ok|
|standard|Mislykkedes.|


### <a name="get-file-content-using-id"></a>Få filindhold ved hjælp af id
Henter filindholdet fra feltet med-id.  
```GET: /datasets/default/files/{id}/content```

| Navn|Datatype|Påkrævet|Placeret i|Standardværdi|Beskrivelse|
| ---|---|---|---|---|---|
|id|streng|Ja|sti|Ingen |Entydigt id for filen i feltet|

#### <a name="response"></a>Svar
|Navn|Beskrivelse|
|---|---|
|200|Ok|
|standard|Mislykkedes.|


### <a name="get-file-content-using-path"></a>Få filindhold ved hjælp af sti
Henter filindholdet fra feltet med sti.  
```GET: /datasets/default/GetFileContentByPath```

| Navn|Datatype|Påkrævet|Placeret i|Standardværdi|Beskrivelse|
| ---|---|---|---|---|---|
|sti|streng|Ja|forespørgsel|Ingen |Entydige stien til filen i feltet|

#### <a name="response"></a>Svar
|Navn|Beskrivelse|
|---|---|
|200|Ok|
|standard|Mislykkedes.|


### <a name="get-file-metadata-using-id"></a>Få filens metadata ved hjælp af id
Henter filens metadata fra feltet med fil-id.  
```GET: /datasets/default/files/{id}```

| Navn|Datatype|Påkrævet|Placeret i|Standardværdi|Beskrivelse|
| ---|---|---|---|---|---|
|id|streng|Ja|sti| Ingen|Entydigt id for filen i feltet|

#### <a name="response"></a>Svar
|Navn|Beskrivelse|
|---|---|
|200|Ok|
|standard|Mislykkedes.|


### <a name="get-file-metadata-using-path"></a>Få filmetadata ved hjælp af sti
Henter filens metadata fra feltet med sti.  
```GET: /datasets/default/GetFileByPath```

| Navn|Datatype|Påkrævet|Placeret i|Standardværdi|Beskrivelse|
| ---|---|---|---|---|---|
|sti|streng|Ja|forespørgsel|Ingen |Entydige stien til filen i feltet|

#### <a name="response"></a>Svar
|Navn|Beskrivelse|
|---|---|
|200|Ok|
|standard|Mislykkedes.|


### <a name="update-file"></a>Opdatere fil
Opdaterer en fil i feltet.  
```PUT: /datasets/default/files/{id}```

| Navn|Datatype|Påkrævet|Placeret i|Standardværdi|Beskrivelse|
| ---|---|---|---|---|---|
|id|streng|Ja|sti| Ingen|Entydigt id for den fil, der opdaterer i feltet|
|brødteksten|String(Binary) |Ja|brødteksten|Ingen |Indholdet af den fil, der opdaterer i feltet|

#### <a name="response"></a>Svar
|Navn|Beskrivelse|
|---|---|
|200|Ok|
|standard|Mislykkedes.|


### <a name="when-a-file-is-modified"></a>Når en fil er blevet ændret
Aktiverer et flow, når en fil ændres i en mappe i feltet.  
```GET: /datasets/default/triggers/onupdatedfile```

| Navn|Datatype|Påkrævet|Placeret i|Standardværdi|Beskrivelse|
| ---|---|---|---|---|---|
|folderId|streng|Ja|forespørgsel|Ingen |Entydigt id for mappen i feltet|

#### <a name="response"></a>Svar
|Navn|Beskrivelse|
|---|---|
|200|Ok|
|standard|Mislykkedes.|


## <a name="object-definitions"></a>Objektdefinitioner

#### <a name="datasetsmetadata"></a>DataSetsMetadata

|Egenskabsnavn | Datatype | Påkrævet|
|---|---|---|
|tabelformat|ikke defineret|Nej|
|BLOB|ikke defineret|Nej|

#### <a name="tabulardatasetsmetadata"></a>TabularDataSetsMetadata

|Egenskabsnavn | Datatype |Påkrævet|
|---|---|---|
|kilde|streng|Nej|
|vist navn|streng|Nej|
|urlEncoding|streng|Nej|
|tableDisplayName|streng|Nej|
|tablePluralName|streng|Nej|

#### <a name="blobdatasetsmetadata"></a>BlobDataSetsMetadata

|Egenskabsnavn | Datatype |Påkrævet|
|---|---|---|
|kilde|streng|Nej|
|vist navn|streng|Nej|
|urlEncoding|streng|Nej|

#### <a name="blobmetadata"></a>BlobMetadata

|Egenskabsnavn | Datatype |Påkrævet|
|---|---|---|
|Id|streng|Nej|
|Navn|streng|Nej|
|Vist navn|streng|Nej|
|Sti|streng|Nej|
|LastModified|streng|Nej|
|Størrelse|heltal|Nej|
|Medietype|streng|Nej|
|IsFolder|Boolesk værdi|Nej|
|ETag|streng|Nej|
|FileLocator|streng|Nej|

## <a name="next-steps"></a>Næste trin

[Opret en logik app](../app-service-logic/app-service-logic-create-a-logic-app.md).
