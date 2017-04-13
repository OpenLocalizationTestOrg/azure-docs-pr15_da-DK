
<properties
    pageTitle="Tilføje HTTP + Swagger handling i logik apps | Microsoft Azure"
    description="Oversigt over HTTP + Swagger handling og handlinger"
    services=""
    documentationCenter=""
    authors="jeffhollan"
    manager="erikre"
    editor=""
    tags="connectors"/>

<tags
   ms.service="logic-apps"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="07/18/2016"
   ms.author="jehollan"/>

# <a name="get-started-with-the-http--swagger-action"></a>Komme i gang med HTTP + Swagger handling

Med HTTP + Swagger handling, kan du oprette en førsteklasses forbindelse til en hvilken som helst RESTEN slutpunkt gennem et [Swagger dokument](https://swagger.io). Du kan også udvide en logik app for at ringe til en hvilken som helst RESTEN slutpunkt med en førsteklasses logik App Designer-oplevelse.

At komme i gang med HTTP + Swagger handling i en logik app skal du se [oprette en ny logik app](../app-service-logic/app-service-logic-create-a-logic-app.md).

---

## <a name="use-http--swagger-as-a-trigger-or-an-action"></a>Brug HTTP + Swagger som en udløser eller en handling

HTTP- + Swagger udløser og handling fungerer på samme måde som [http-handling](connectors-native-http.md) , men give en bedre designoplevelse ved at vise udformningen af API og output i designer fra den [Swagger metadata](https://swagger.io). Desuden kan du bruge HTTP + Swagger som en udløser. Hvis du vil implementere en afstemning udløser, bør det følge mønstret afstemning, der er beskrevet i [oprette en brugerdefineret API til brug med logik apps](../app-service-logic/app-service-logic-create-api-app.md#polling-triggers).

[Lær mere om logik app udløsere og handlinger.](connectors-overview.md)

Her er et eksempel på, hvordan til brug på HTTP + Swagger handlingen som en handling i en arbejdsproces i en logik app.

1. Vælg knappen **Nyt trin** .
2. Vælg **Tilføj en handling**.
3. Skriv **swagger** til listen HTTP + Swagger handling i søgefeltet handling.

    ![Vælg HTTP + Swagger handling](./media/connectors-native-http-swagger/using-action-1.png)

4. Skriv URL-adressen for et Swagger dokument:
    - Hvis du vil arbejde fra logik App Designer, URL-adressen skal være et HTTPS slutpunkt og har aktiveret CORS.
    - Hvis dokumentet Swagger ikke opfylder dette krav, kan du bruge [Azure-lager med CORS aktiveret](#hosting-swagger-from-storage) til at gemme dokumentet.
5. Klik på **Næste** for at læse og gengive fra Swagger dokumentet.
6. Tilføj eventuelle parametre, der kræves til HTTP opkaldet.

    ![Fuldført HTTP-handling](./media/connectors-native-http-swagger/using-action-2.png)

1. Klik på **Gem** i det øverste venstre hjørne af værktøjslinjen, og din logik app, begge Gem og Publicer (aktivere).

### <a name="host-swagger-from-azure-storage"></a>Host Swagger fra Azure-lager

Du måske at referere til et Swagger dokument, der ikke er hostet eller, der ikke opfylder sikkerhed og tværs origin krav til designer. For at løse dette problem, kan du gemme dokumentet Swagger i Azure-lager og aktiverer CORS til at referere til dokumentet.  

Her er trinnene for at oprette, konfigurere og gemme Swagger dokumenter i Azure-lager:

1. [Opret en Azure lagerplads konto med Azure Blob-lager](../storage/storage-create-storage-account.md). (For at gøre dette skal du angive tilladelser til **offentlig adgang**.)
2. Aktivere CORS på blob. Du kan bruge [denne PowerShell-script](https://github.com/logicappsio/EnableCORSAzureBlob/blob/master/EnableCORSAzureBlob.ps1) til at konfigurere denne indstilling, automatisk.
3. Overføre filen Swagger til blob. Du kan gøre dette fra [Azure-portalen](https://portal.azure.com) eller fra et værktøj som [Azure Storage Explorer](http://storageexplorer.com/).
1. Henvise til en HTTPS-link til dokumentet i Azure Blob-lager. (Linket følger formatet `https://*storageAccountName*.blob.core.windows.net/*container*/*filename*`.)



## <a name="technical-details"></a>Tekniske detaljer

Følgende er nogle oplysninger for de udløsere og handlinger, som denne HTTP + Swagger forbindelse understøtter.

## <a name="http--swagger-triggers"></a>HTTP- + Swagger udløsere

En udløser er en begivenhed, der kan bruges til at starte arbejdsprocessen, der er defineret i en logik app. [Lær mere om udløsere.](connectors-overview.md) HTTP- + Swagger forbindelse har én udløser.

|Udløser|Beskrivelse|
|---|---|
|HTTP- + Swagger|Foretage en HTTP opkald og returnere svar indholdet|

## <a name="http--swagger-actions"></a>HTTP- + Swagger handlinger

En handling er en handling, der udføres af arbejdsprocessen, der er defineret i en logik app. [Lær mere om handlinger.](connectors-overview.md) HTTP- + Swagger forbindelse med en mulig handling.

|Handling|Beskrivelse|
|---|---|
|HTTP- + Swagger|Foretage en HTTP opkald og returnere svar indholdet|

### <a name="action-details"></a>Handlingsdetaljer

HTTP- + Swagger forbindelse leveres med en mulig handling. Følgende er oplysninger om hver af handlingerne, deres krævede og valgfrie input felter og de tilsvarende output detaljer, som er knyttet til deres brugen.

#### <a name="http--swagger"></a>HTTP- + Swagger

Foretage en udgående HTTP-anmodning med hjælp af Swagger metadata.
En stjerne (*) betyder, at et obligatorisk felt.

|Vist navn|Egenskabsnavn|Beskrivelse|
|---|---|---|
|Metode *|metode|HTTP-verbum til brug.|
|URI *|URI|URI-adressen for HTTP-anmodningen.|
|Sidehoveder|sidehoveder|Et JSON objekt HTTP-headere medtage.|
|Brødteksten|brødteksten|HTTP-anmodningsteksten.|
|Godkendelse|godkendelse|Godkendelse skal bruges til anmodning om. Du [kan få flere oplysninger under HTTP](./connectors-native-http.md#authentication).|

**Output detaljer**

HTTP-svar

|Egenskabsnavn|Datatype|Beskrivelse|
|---|---|---|
|Sidehoveder|objekt|Svar sidehoveder|
|Brødteksten|objekt|Response-objektet|
|Statuskode|heltal|HTTP-statuskode|

### <a name="http-responses"></a>HTTP-svar

Når du foretager opkald til forskellige handlinger, kan du få bestemte svar. Følgende er en tabel, der skitserer tilsvarende svar og beskrivelser.

|Navn|Beskrivelse|
|---|---|
|200|Ok|
|202|Accepteret|
|400|Forkert anmodning|
|401|Uautoriseret|
|403|Forbudt|
|404|Blev ikke fundet|
|500|Intern serverfejl. Der opstod ukendt fejl.|

---

## <a name="next-steps"></a>Næste trin

Prøv den platform og [oprette en logik app](../app-service-logic/app-service-logic-create-a-logic-app.md) nu. Du kan udforske andre tilgængelige forbindelserne i logik apps ved at kigge på vores [liste over API'er](apis-list.md).
