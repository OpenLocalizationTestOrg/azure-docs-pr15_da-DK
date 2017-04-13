<properties
pageTitle="OneDrive til virksomheder | Microsoft Azure"
description="Opret logik apps med Azure App service. Oprette forbindelse til OneDrive for Business til at administrere dine filer. Du kan udføre forskellige handlinger som upload, opdatere, få og slette filer."
services="logic-apps"   
documentationCenter=".net,nodejs,java"  
authors="msftman"   
manager="erikre"    
editor=""
tags="connectors" />

<tags
ms.service="logic-apps"
ms.devlang="multiple"
ms.topic="article"
ms.tgt_pltfrm="na"
ms.workload="integration"
ms.date="08/18/2016"
ms.author="deonhe"/>

# <a name="get-started-with-the-onedrive-for-business-connector"></a>Introduktion til OneDrive for Business connector

Oprette forbindelse til OneDrive for Business til at administrere dine filer. Du kan udføre forskellige handlinger som upload, opdatere, få og slette filer.

>[AZURE.NOTE] Denne version i denne artikel gælder for logik apps 2015-08-01-skema prøveversionen. 

Du kan komme i gang ved at oprette en logik app nu, i [oprette en logik app](../app-service-logic/app-service-logic-create-a-logic-app.md).

## <a name="triggers-and-actions"></a>Udløsere og handlinger

OneDrive for Business connector kan bruges som en handling den har trigger(s). Alle forbindelser understøtter data i JSON og XML-formater. 

 OneDrive for Business connector har følgende handling(er) og/eller trigger(s) tilgængelige:

### <a name="onedrive-for-business-actions"></a>OneDrive for Business-handlinger
Du kan udføre disse handling(er):

|Handling|Beskrivelse|
|--- | ---|
|[GetFileMetadata](connectors-create-api-onedriveforbusiness.md#getfilemetadata)|Henter metadataene i en fil i OneDrive for Business med-id|
|[UpdateFile](connectors-create-api-onedriveforbusiness.md#updatefile)|Opdaterer en fil i OneDrive for Business|
|[DeleteFile](connectors-create-api-onedriveforbusiness.md#deletefile)|Sletter en fil fra OneDrive for Business|
|[GetFileMetadataByPath](connectors-create-api-onedriveforbusiness.md#getfilemetadatabypath)|Henter metadataene i en fil i OneDrive for Business med sti|
|[GetFileContentByPath](connectors-create-api-onedriveforbusiness.md#getfilecontentbypath)|Henter indholdet af en fil i OneDrive for Business med sti|
|[GetFileContent](connectors-create-api-onedriveforbusiness.md#getfilecontent)|Henter indholdet af en fil i OneDrive for Business med-id|
|[CreateFile](connectors-create-api-onedriveforbusiness.md#createfile)|Overfører en fil til OneDrive for Business|
|[CopyFile](connectors-create-api-onedriveforbusiness.md#copyfile)|Kopierer en fil til OneDrive for Business|
|[ListFolder](connectors-create-api-onedriveforbusiness.md#listfolder)|Viser en liste over filer i en OneDrive for Business-mappe|
|[ListRootFolder](connectors-create-api-onedriveforbusiness.md#listrootfolder)|Viser en liste over filer i OneDrive for Business-rodmappe|
|[ExtractFolderV2](connectors-create-api-onedriveforbusiness.md#extractfolderv2)|Henter en mappe til OneDrive for Business|
### <a name="onedrive-for-business-triggers"></a>OneDrive for Business udløsere
Du kan lytte til disse hændelser:

|Udløser | Beskrivelse|
|--- | ---|
|Når der oprettes en fil|Udløser et flow, når der oprettes en ny fil i en OneDrive for Business-mappe|
|Når en fil er blevet ændret|Aktiverer et flow, når en fil er ændret i et OneDrive for Business-mappe|


## <a name="create-a-connection-to-onedrive-for-business"></a>Oprette en forbindelse til OneDrive for Business
Hvis du vil oprette logik apps med OneDrive for Business, skal du først oprette en **forbindelse** og derefter angive detaljerne for følgende egenskaber: 

|Egenskaben| Påkrævet|Beskrivelse|
| ---|---|---|
|Token|Ja|Angive OneDrive for Business-legitimationsoplysninger|
Når du opretter forbindelsen, kan du bruge det til at udføre handlingerne, og lyt til de udløsere, der er beskrevet i denne artikel. 

>[AZURE.INCLUDE [Steps to create a connection to OneDrive for Business](../../includes/connectors-create-api-onedriveforbusiness.md)]

>[AZURE.TIP] Du kan bruge denne forbindelse i andre logik apps.

## <a name="reference-for-onedrive-for-business"></a>Reference til OneDrive for Business
Gælder for version: 1.0

## <a name="getfilemetadata"></a>GetFileMetadata
Få filens metadata via-id: henter metadataene i en fil i OneDrive for Business med-id 

```GET: /datasets/default/files/{id}``` 

| Navn| Datatype|Påkrævet|Placeret i|Standardværdi|Beskrivelse|
| ---|---|---|---|---|---|
|id|streng|Ja|sti|ingen|Angive fil|

#### <a name="response"></a>Svar

|Navn|Beskrivelse|
|---|---|
|200|Ok|
|standard|Mislykkedes.|


## <a name="updatefile"></a>UpdateFile
Opdateringsfil: opdaterer en fil i OneDrive for Business 

```PUT: /datasets/default/files/{id}``` 

| Navn| Datatype|Påkrævet|Placeret i|Standardværdi|Beskrivelse|
| ---|---|---|---|---|---|
|id|streng|Ja|sti|ingen|Angive den fil, der skal opdateres|
|brødteksten| |Ja|brødteksten|ingen|Indholdet af den fil, der opdaterer i OneDrive for Business|

#### <a name="response"></a>Svar

|Navn|Beskrivelse|
|---|---|
|200|Ok|
|standard|Mislykkedes.|


## <a name="deletefile"></a>DeleteFile
Slet filen: Sletter en fil fra OneDrive for Business 

```DELETE: /datasets/default/files/{id}``` 

| Navn| Datatype|Påkrævet|Placeret i|Standardværdi|Beskrivelse|
| ---|---|---|---|---|---|
|id|streng|Ja|sti|ingen|Angive den fil, slette|

#### <a name="response"></a>Svar

|Navn|Beskrivelse|
|---|---|
|200|Ok|
|standard|Mislykkedes.|


## <a name="getfilemetadatabypath"></a>GetFileMetadataByPath
Få filmetadata ved hjælp af sti: henter metadataene i en fil i OneDrive for Business med sti 

```GET: /datasets/default/GetFileByPath``` 

| Navn| Datatype|Påkrævet|Placeret i|Standardværdi|Beskrivelse|
| ---|---|---|---|---|---|
|sti|streng|Ja|forespørgsel|ingen|Entydige stien til filen i OneDrive for Business|

#### <a name="response"></a>Svar

|Navn|Beskrivelse|
|---|---|
|200|Ok|
|standard|Mislykkedes.|


## <a name="getfilecontentbypath"></a>GetFileContentByPath
Få filindhold ved hjælp af sti: Henter indholdet af en fil i OneDrive for Business med sti 

```GET: /datasets/default/GetFileContentByPath``` 

| Navn| Datatype|Påkrævet|Placeret i|Standardværdi|Beskrivelse|
| ---|---|---|---|---|---|
|sti|streng|Ja|forespørgsel|ingen|Entydige stien til filen i OneDrive for Business|

#### <a name="response"></a>Svar

|Navn|Beskrivelse|
|---|---|
|200|Ok|
|standard|Mislykkedes.|


## <a name="getfilecontent"></a>GetFileContent
Få filindhold med-id: Henter indholdet af en fil i OneDrive for Business med-id 

```GET: /datasets/default/files/{id}/content``` 

| Navn| Datatype|Påkrævet|Placeret i|Standardværdi|Beskrivelse|
| ---|---|---|---|---|---|
|id|streng|Ja|sti|ingen|Angive fil|

#### <a name="response"></a>Svar

|Navn|Beskrivelse|
|---|---|
|200|Ok|
|standard|Mislykkedes.|


## <a name="createfile"></a>CreateFile
Oprette fil: overfører en fil til OneDrive for Business 

```POST: /datasets/default/files``` 

| Navn| Datatype|Påkrævet|Placeret i|Standardværdi|Beskrivelse|
| ---|---|---|---|---|---|
|Mappesti|streng|Ja|forespørgsel|ingen|Mappesti at overføre filen til OneDrive for Business|
|Navn|streng|Ja|forespørgsel|ingen|Navnet på filen for at oprette i OneDrive for Business|
|brødteksten| |Ja|brødteksten|ingen|Indholdet af filen for at uploade til OneDrive for Business|

#### <a name="response"></a>Svar

|Navn|Beskrivelse|
|---|---|
|200|Ok|
|standard|Mislykkedes.|


## <a name="copyfile"></a>CopyFile
Kopiere fil: kopierer en fil til OneDrive for Business 

```POST: /datasets/default/copyFile``` 

| Navn| Datatype|Påkrævet|Placeret i|Standardværdi|Beskrivelse|
| ---|---|---|---|---|---|
|kilde|streng|Ja|forespørgsel|ingen|URL-adressen til kildefilen|
|destination|streng|Ja|forespørgsel|ingen|Destination filstien i OneDrive for Business, herunder target filnavn|
|overskrive|Boolesk værdi|Nej|forespørgsel|FALSK|Overskriver destinationsfilen, hvis er angivet til "true"|

#### <a name="response"></a>Svar

|Navn|Beskrivelse|
|---|---|
|200|Ok|
|standard|Mislykkedes.|


## <a name="onnewfile"></a>OnNewFile
Når der oprettes en fil: udløser et flow, når der oprettes en ny fil i en OneDrive for Business-mappe 

```GET: /datasets/default/triggers/onnewfile``` 

| Navn| Datatype|Påkrævet|Placeret i|Standardværdi|Beskrivelse|
| ---|---|---|---|---|---|
|folderId|streng|Ja|forespørgsel|ingen|Angive en mappe|

#### <a name="response"></a>Svar

|Navn|Beskrivelse|
|---|---|
|200|Ok|
|standard|Mislykkedes.|


## <a name="onupdatedfile"></a>OnUpdatedFile
Når en fil er blevet ændret: udløser et flow, når en fil er ændret i et OneDrive for Business-mappe 

```GET: /datasets/default/triggers/onupdatedfile``` 

| Navn| Datatype|Påkrævet|Placeret i|Standardværdi|Beskrivelse|
| ---|---|---|---|---|---|
|folderId|streng|Ja|forespørgsel|ingen|Angive en mappe|

#### <a name="response"></a>Svar

|Navn|Beskrivelse|
|---|---|
|200|Ok|
|standard|Mislykkedes.|


## <a name="listfolder"></a>ListFolder
Liste over filer i mappe: Viser filer på en OneDrive for Business-mappe 

```GET: /datasets/default/folders/{id}``` 

| Navn| Datatype|Påkrævet|Placeret i|Standardværdi|Beskrivelse|
| ---|---|---|---|---|---|
|id|streng|Ja|sti|ingen|Angiv mappen|

#### <a name="response"></a>Svar

|Navn|Beskrivelse|
|---|---|
|200|Ok|
|standard|Mislykkedes.|


## <a name="listrootfolder"></a>ListRootFolder
Listen rodmappen: Viser en liste over filer i OneDrive for Business-rodmappe 

```GET: /datasets/default/folders``` 

Der er ingen parametre til dette opkald
#### <a name="response"></a>Svar

|Navn|Beskrivelse|
|---|---|
|200|Ok|
|standard|Mislykkedes.|


## <a name="extractfolderv2"></a>ExtractFolderV2
Udtrække mappe: uddrager en mappe til OneDrive for Business 

```POST: /datasets/default/extractFolderV2``` 

| Navn| Datatype|Påkrævet|Placeret i|Standardværdi|Beskrivelse|
| ---|---|---|---|---|---|
|kilde|streng|Ja|forespørgsel|ingen|Stien til arkivfilen|
|destination|streng|Ja|forespørgsel|ingen|Stien i OneDrive for Business til at udtrække indholdet arkiv|
|overskrive|Boolesk værdi|Nej|forespørgsel|FALSK|Overskriver at destinationsfilen, hvis er angivet til "true"|

#### <a name="response"></a>Svar

|Navn|Beskrivelse|
|---|---|
|200|Ok|
|standard|Mislykkedes.|


## <a name="object-definitions"></a>Objektdefinitioner 

### <a name="datasetsmetadata"></a>DataSetsMetadata


| Egenskabsnavn | Datatype | Påkrævet |
|---|---|---|
|tabelformat|ikke defineret|Nej |
|BLOB|ikke defineret|Nej |



### <a name="tabulardatasetsmetadata"></a>TabularDataSetsMetadata


| Egenskabsnavn | Datatype | Påkrævet |
|---|---|---|
|kilde|streng|Nej |
|vist navn|streng|Nej |
|urlEncoding|streng|Nej |
|tableDisplayName|streng|Nej |
|tablePluralName|streng|Nej |



### <a name="blobdatasetsmetadata"></a>BlobDataSetsMetadata


| Egenskabsnavn | Datatype | Påkrævet |
|---|---|---|
|kilde|streng|Nej |
|vist navn|streng|Nej |
|urlEncoding|streng|Nej |



### <a name="blobmetadata"></a>BlobMetadata


| Egenskabsnavn | Datatype | Påkrævet |
|---|---|---|
|Id|streng|Nej |
|Navn|streng|Nej |
|Vist navn|streng|Nej |
|Sti|streng|Nej |
|LastModified|streng|Nej |
|Størrelse|heltal|Nej |
|Medietype|streng|Nej |
|IsFolder|Boolesk værdi|Nej |
|ETag|streng|Nej |
|FileLocator|streng|Nej |



### <a name="object"></a>Objekt


| Egenskabsnavn | Datatype | Påkrævet |
|---|---|---|


## <a name="next-steps"></a>Næste trin
[Oprette en logik app](../app-service-logic/app-service-logic-create-a-logic-app.md)