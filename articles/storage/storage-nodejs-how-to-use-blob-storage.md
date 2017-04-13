<properties
    pageTitle="Sådan bruger du Blob-lager fra Node.js | Microsoft Azure"
    description="Gemme ustrukturerede data i skyen med Azure Blob-lager (objekt storage)."
    services="storage"
    documentationCenter="nodejs"
    authors="tamram"
    manager="carmonm"
    editor="tysonn"/>

<tags
    ms.service="storage"
    ms.workload="storage"
    ms.tgt_pltfrm="na"
    ms.devlang="nodejs"
    ms.topic="article"
    ms.date="08/11/2016"
    ms.author="tamram"/>



# <a name="how-to-use-blob-storage-from-nodejs"></a>Sådan bruger du Blob-lager fra Node.js

[AZURE.INCLUDE [storage-selector-blob-include](../../includes/storage-selector-blob-include.md)]
<br/>
[AZURE.INCLUDE [storage-try-azure-tools-queues](../../includes/storage-try-azure-tools-blobs.md)]

## <a name="overview"></a>Oversigt

I denne artikel beskrives, hvordan til at udføre almindelige scenarier ved hjælp af Blob-lager. Eksemplerne skrives via Node.js API. Scenarier dækket omfatte, hvordan du overfører, liste, hente og slette BLOB.

[AZURE.INCLUDE [storage-blob-concepts-include](../../includes/storage-blob-concepts-include.md)]

[AZURE.INCLUDE [storage-create-account-include](../../includes/storage-create-account-include.md)]

## <a name="create-a-nodejs-application"></a>Oprette et Node.js program

Du kan finde oplysninger om, hvordan du opretter en Node.js-program, [Opret en Node.js WebApp i Azure App Service], [opbygge og anvende et Node.js program til en skybaseret tjeneste til Azure] - ved hjælp af Windows PowerShell eller [opbygge og anvende en Node.js web-app til Azure ved hjælp af Web Matrix].

## <a name="configure-your-application-to-access-storage"></a>Konfigurere dit program tilladelse til at få adgang til lager

Hvis du vil bruge Azure lagerplads, skal du i Azure lagerplads SDK for Node.js, som indeholder et sæt af nemmere biblioteker, kommunikerer med lagerplads RESTEN tjenester.

### <a name="use-node-package-manager-npm-to-obtain-the-package"></a>Brug Node pakke Manager (NPM) til at hente pakken

1.  Bruge en kommandolinjen som **PowerShell** (Windows), **Terminal** (Mac) eller **Bash** (Unix) til at gå til den mappe, hvor du har oprettet din Northwind.

2.  Skriv **npm installere azure-lager** i kommandovinduet. Output fra kommandoen ligner følgende kodeeksempel.

        azure-storage@0.5.0 node_modules\azure-storage
        +-- extend@1.2.1
        +-- xmlbuilder@0.4.3
        +-- mime@1.2.11
        +-- node-uuid@1.4.3
        +-- validator@3.22.2
        +-- underscore@1.4.4
        +-- readable-stream@1.0.33 (string_decoder@0.10.31, isarray@0.0.1, inherits@2.0.1, core-util-is@1.0.1)
        +-- xml2js@0.2.7 (sax@0.5.2)
        +-- request@2.57.0 (caseless@0.10.0, aws-sign2@0.5.0, forever-agent@0.6.1, stringstream@0.0.4, oauth-sign@0.8.0, tunnel-agent@0.4.1, isstream@0.1.2, json-stringify-safe@5.0.1, bl@0.9.4, combined-stream@1.0.5, qs@3.1.0, mime-types@2.0.14, form-data@0.2.0, http-signature@0.11.0, tough-cookie@2.0.0, hawk@2.3.1, har-validator@1.8.0)

3.  Du kan manuelt køre kommandoen **ls** at kontrollere, at en **node\_moduler** mappe blev oprettet. Find pakken **azure-lager** , som indeholder de biblioteker, du har brug for at få adgang til lager i denne mappe.

### <a name="import-the-package"></a>Importere pakken

Bruger Notesblok eller et andet tekstredigeringsprogram, skal du tilføje følgende til toppen af filen **server.js** af programmet, hvor du vil bruge lagerplads:

    var azure = require('azure-storage');

## <a name="set-up-an-azure-storage-connection"></a>Konfigurere en forbindelse til Azure-lager

Modulet Azure læser miljøvariabler `AZURE_STORAGE_ACCOUNT` og `AZURE_STORAGE_ACCESS_KEY`, eller `AZURE_STORAGE_CONNECTION_STRING`, oplysninger, der kræves for at oprette forbindelse til kontoen Azure-lager. Hvis variablerne miljø ikke er angivet, skal du angive kontooplysninger, når du ringer til **createBlobService**.

Du kan finde et eksempel på indstilling miljøvariabler i [Azure Portal](https://portal.azure.com) for en Azure-WebApp, [Node.js online ved hjælp af tjenesten Azure tabel].

## <a name="create-a-container"></a>Oprette en objektbeholder

Objektet **BlobService** kan du arbejde med beholdere og BLOB. Følgende kode opretter et **BlobService** objekt. Tilføj følgende i øverst del af **server.js**:

    var blobSvc = azure.createBlobService();

> [AZURE.NOTE] Du kan åbne en blob anonymt ved hjælp af **createBlobServiceAnonymous** og give adressen på. For eksempel bruge `var blobSvc = azure.createBlobServiceAnonymous('https://myblob.blob.core.windows.net/');`.

[AZURE.INCLUDE [storage-container-naming-rules-include](../../includes/storage-container-naming-rules-include.md)]

Brug **createContainerIfNotExists**til at oprette en ny objektbeholder. Følgende kodeeksempel opretter en ny objektbeholder med navnet 'mycontainer':

    blobSvc.createContainerIfNotExists('mycontainer', function(error, result, response){
        if(!error){
          // Container exists and is private
        }
    });

Hvis beholderen er nyoprettet, `result.created` er sand. Hvis der findes allerede objektbeholderen, `result.created` er falsk. `response`indeholder oplysninger om den operation, herunder ETag oplysningerne for objektbeholderen.

### <a name="container-security"></a>Objektbeholder sikkerhed

Ny og ubrugt er private som standard og kan ikke få adgang til anonymt. For at gøre objektbeholderen offentlige, så du kan åbne den anonymt, kan du angive den objektbeholder adgangsniveau til **blob** eller **beholder**.

* **blob** - tillader anonyme læseadgang til blob indhold og metadata i denne objektbeholder, men ikke på objektbeholder metadata som listen over alle BLOB i en objektbeholder

* **objektbeholder** - tillader anonyme læseadgang til blob indhold og metadata samt objektbeholder metadata

Følgende kodeeksempel demonstrerer indstille adgangsniveauet til **blob**:

    blobSvc.createContainerIfNotExists('mycontainer', {publicAccessLevel : 'blob'}, function(error, result, response){
        if(!error){
          // Container exists and allows
          // anonymous read access to blob
          // content and metadata within this container
        }
    });

Du kan også ændre adgangsniveauet for en objektbeholder ved hjælp af **setContainerAcl** til at angive adgangsniveauet. Følgende kodeeksempel ændres adgangsniveauet til objektbeholder:

    blobSvc.setContainerAcl('mycontainer', null /* signedIdentifiers */, {publicAccessLevel : 'container'} /* publicAccessLevel*/, function(error, result, response){
      if(!error){
        // Container access level set to 'container'
      }
    });

Resultatet indeholder oplysninger om den operation, herunder den aktuelle **ETag** for objektbeholderen.

### <a name="filters"></a>Filtre

Du kan anvende valgfrit spamfiltrering handlinger på Handlinger, der udføres ved hjælp af **BlobService**. Filtrering handlinger kan medtage logføring, automatisk prøver igen osv. Filtre er objekter, der implementerer en metode til signaturen:

    function handle (requestOptions, next)

Herefter dens forbehandling på indstillingerne for anmodning om kræver metoden til at ringe til "Næste", der passerer et tilbagekald med den følgende signatur:

    function (returnObject, finalCallback, next)

I denne tilbagekald, og efter behandling af returnObject (svar fra anmodningen på serveren), skal tilbagekaldet enten kalde næste, hvis den findes for at fortsætte med at behandle andre filtre eller blot kalde finalCallback for at afslutte tjenesten aktiveringen.

To filtre, der implementerer forsøg er inkluderet med Azure SDK til Node.js, **ExponentialRetryPolicyFilter** og **LinearRetryPolicyFilter**. Følgende opretter en **BlobService** -objekt, der bruger **ExponentialRetryPolicyFilter**:

    var retryOperations = new azure.ExponentialRetryPolicyFilter();
    var blobSvc = azure.createBlobService().withFilter(retryOperations);

## <a name="upload-a-blob-into-a-container"></a>Overføre en blob til en objektbeholder

Der findes tre typer BLOB: blokere blob, siden BLOB og tilføje BLOB. Bloker BLOB Tillad, at du til mere effektivt overføre store data. Tilføje BLOB er optimeret til tilføje handlinger. Siden BLOB er optimeret til læse-og skriveadgang handlinger. Se [forstå Bloker blob, tilføje BLOB og siden BLOB](http://msdn.microsoft.com/library/azure/ee691964.aspx)kan finde flere oplysninger.

### <a name="block-blobs"></a>Bloker BLOB

For at overføre data til en blok blob skal du bruge følgende:

* **createBlockBlobFromLocalFile** - opretter en ny blok blob og overfører indholdet af en fil

* **createBlockBlobFromStream** - opretter en ny blok blob og overfører indholdet af en stream

* **createBlockBlobFromText** - opretter en ny blok blob og overfører indholdet af en streng

* **createWriteStreamToBlockBlob** - giver en Skriv stream til en blok blob

Følgende kodeeksempel uploader indholdet af filen **test.txt** til **myblob**.

    blobSvc.createBlockBlobFromLocalFile('mycontainer', 'myblob', 'test.txt', function(error, result, response){
      if(!error){
        // file uploaded
      }
    });

Den `result` returneres af disse metoder indeholder oplysninger om handlingen, som **ETag** af blob.

### <a name="append-blobs"></a>Tilføje BLOB

For at overføre data til en ny Tilføj blob skal du bruge følgende:

* **createAppendBlobFromLocalFile** - opretter en ny Tilføj blob og overfører indholdet af en fil

* **createAppendBlobFromStream** - opretter en ny Tilføj blob og overfører indholdet af en stream

* **createAppendBlobFromText** - opretter en ny Tilføj blob og overfører indholdet af en streng

* **createWriteStreamToNewAppendBlob** - opretter en ny Tilføj blob og derefter leverer en stream til at skrive til den

Følgende kodeeksempel uploader indholdet af filen **test.txt** til **myappendblob**.

    blobSvc.createAppendBlobFromLocalFile('mycontainer', 'myappendblob', 'test.txt', function(error, result, response){
      if(!error){
        // file uploaded
      }
    });

Hvis du vil tilføje en tekstblok til en eksisterende Tilføj blob, skal du bruge følgende:

* **appendFromLocalFile** - føjer indholdet af en fil til en eksisterende Tilføj blob

* **appendFromStream** - føjer indholdet af en stream til en eksisterende Tilføj blob

* **appendFromText** - føjer indholdet af en streng til en eksisterende Tilføj blob

* **appendBlockFromStream** - føjer indholdet af en stream til en eksisterende Tilføj blob

* **appendBlockFromText** - føjer indholdet af en streng til en eksisterende Tilføj blob

> [AZURE.NOTE] appendFromXXX API'er vil gøre nogle klientsiden validering for at mislykkes hurtigt for at undgå unncessary server opkald. appendBlockFromXXX ikke.

Følgende kodeeksempel uploader indholdet af filen **test.txt** til **myappendblob**.

    blobSvc.appendFromText('mycontainer', 'myappendblob', 'text to be appended', function(error, result, response){
      if(!error){
        // text appended
      }
    });


### <a name="page-blobs"></a>Siden BLOB

For at overføre data til en side blob skal du bruge følgende:

* **createPageBlob** - opretter en ny side blob af en bestemt længde

* **createPageBlobFromLocalFile** - opretter en ny side blob og overfører indholdet af en fil

* **createPageBlobFromStream** - opretter en ny side blob og overfører indholdet af en stream

* **createWriteStreamToExistingPageBlob** - indeholder en Skriv stream til en eksisterende side blob

* **createWriteStreamToNewPageBlob** - opretter en ny side blob og derefter leverer en stream til at skrive til den

Følgende kodeeksempel uploader indholdet af filen **test.txt** til **mypageblob**.

    blobSvc.createPageBlobFromLocalFile('mycontainer', 'mypageblob', 'test.txt', function(error, result, response){
      if(!error){
        // file uploaded
      }
    });

> [AZURE.NOTE] Siden BLOB bestå af 512-byte 'sider'. Du får en fejlmeddelelse, når du overfører data med en størrelse, der ikke er et multiplum af 512.

## <a name="list-the-blobs-in-a-container"></a>Liste over BLOB i en objektbeholder

Du kan få vist BLOB i en objektbeholder, kan du bruge metoden **listBlobsSegmented** . Hvis du vil have til at returnere BLOB med et bestemt præfiks, kan du bruge **listBlobsSegmentedWithPrefix**.

    blobSvc.listBlobsSegmented('mycontainer', null, function(error, result, response){
      if(!error){
          // result.entries contains the entries
          // If not all blobs were returned, result.continuationToken has the continuation token.
      }
    });

Den `result` indeholder en `entries` samling, som er en matrix af objekter, der beskriver hver blob. Hvis alle BLOB ikke kan returneres, den `result` indeholder også en `continuationToken`, som du kan bruge som den anden parameter til at hente flere poster.

## <a name="download-blobs"></a>Hente BLOB

For at hente data fra en blob skal du bruge følgende:

* **getBlobToLocalFile** - skriver blob indholdet til en fil

* **getBlobToStream** - skriver blob indholdet til en stream

* **getBlobToText** - skriver blob indholdet til en streng

* **createReadStream** - indeholder en stream til at læse fra blob

Følgende kodeeksempel demonstrerer brugen af **getBlobToStream** at hente indholdet af **myblob** blob og gemme den til fil, **output.txt** ved hjælp af en stream:

    var fs = require('fs');
    blobSvc.getBlobToStream('mycontainer', 'myblob', fs.createWriteStream('output.txt'), function(error, result, response){
      if(!error){
        // blob retrieved
      }
    });

Den `result` indeholder oplysninger om blob, herunder **ETag** oplysninger.

## <a name="delete-a-blob"></a>Slette en blob

Til sidst, hvis du vil slette en blob, kald **deleteBlob**. Følgende kodeeksempel sletter blob med navnet **myblob**.

    blobSvc.deleteBlob(containerName, 'myblob', function(error, response){
      if(!error){
        // Blob has been deleted
      }
    });

## <a name="concurrent-access"></a>Samtidige adgang

For at understøtte samtidige adgang til en blob fra flere forskellige kunder eller flere forekomster af processen, kan du bruge **ETags** eller **rettigheder**.

* **Etag** - gør det muligt at registrere, blob eller objektbeholder er blevet ændret af en anden proces

* **Bortlease** - gør det muligt at få eksklusive, vedvarende, skrive eller slette adgang til en blob til et bestemt tidsrum

### <a name="etag"></a>ETag

Brug ETags Hvis du vil tillade flere klienter eller forekomster til at skrive til blokering Blob eller siden Blob samtidigt. ETag giver dig mulighed at finde ud af, hvis objektbeholder eller blob er blevet ændret, da du først læser eller oprettet det, hvor du kan undgå at overskrive ændringer, der er anvendt af en anden klient eller proces.

Du kan angive ETag betingelser ved hjælp af det valgfri `options.accessConditions` parameter. Følgende kodeeksempel overførsler kun **test.txt** filen, hvis der allerede findes på blob og har ETag-værdien, der er indeholdt i `etagToMatch`.

    blobSvc.createBlockBlobFromLocalFile('mycontainer', 'myblob', 'test.txt', { accessConditions: { EtagMatch: etagToMatch} }, function(error, result, response){
        if(!error){
        // file uploaded
      }
    });

Når du bruger ETags, er de generelle mønster:

1. Få ETag som et resultat af en Opret, en liste eller et få handling.

2. Udføre en handling, skal du kontrollere, at værdien ETag ikke er ændret.

Hvis værdien er blevet ændret, angiver, at en anden klient eller forekomst ændret blob eller objektbeholder da du anskaffet ETag-værdien.

### <a name="lease"></a>Bortlease

Du kan få fat på en ny rettighed ved hjælp af metoden **acquireLease** , der angiver den blob eller objektbeholder, som du ønsker at hente en rettighed på. Følgende kode får for eksempel en rettighed på **myblob**.

    blobSvc.acquireLease('mycontainer', 'myblob', function(error, result, response){
      if(!error) {
        console.log('leaseId: ' + result.id);
      }
    });

Efterfølgende handlinger på **myblob** skal angive den `options.leaseId` parameter. Lease ID returneres som `result.id` fra **acquireLease**.

> [AZURE.NOTE] Varigheden af rettigheder er som standard uendelig. Du kan angive en ikke-uendelig varighed (mellem 15 og 60 sekunder) ved at indsende den `options.leaseDuration` parameter.

Brug **releaseLease**til at fjerne en rettighed. Hvis du vil bryde en rettighed, men forhindre andre i anmoder om en ny adresse før den oprindelige varighed er udløbet, skal du bruge **breakLease**.

## <a name="work-with-shared-access-signatures"></a>Arbejde med delt adgang signaturer

Delt adgang signaturer (SAS) er en sikker måde at give findelt adgang til BLOB og beholdere uden at angive dit kontonavn til lager eller -nøglerne. Delt adgang signaturer bruges ofte til at give begrænset adgang til dine data, som giver mulighed for en mobilapp at få adgang til BLOB.

> [AZURE.NOTE] Mens du kan også tillade anonym adgang til BLOB, delt adgang signaturer giver dig mulighed at give mere kontrolleret adgang, som du skal generere Sikkerhedstilknytningerne.

En der er tillid til programmet på computeren som en skybaseret tjeneste genererer delt adgang signaturer ved hjælp af **generateSharedAccessSignature** af **BlobService**, og leverer den til et upålidelige eller halvgennemsigtig pålidelig program som en mobilapp. Delt access signaturer genereres ved hjælp af en politik, som beskriver start- og slutdatoer, hvor delt adgang signaturer er gyldige, samt adgangsniveauet, der er tildelt til delt adgang signaturer indehaver.

Følgende kodeeksempel genererer en ny politik for delt adgang, der gør det muligt for delt adgang signaturer indehaver til at udføre Læs handlinger på **myblob** blob og udløber 100 minutter, efter den tid, det er oprettet.

    var startDate = new Date();
    var expiryDate = new Date(startDate);
    expiryDate.setMinutes(startDate.getMinutes() + 100);
    startDate.setMinutes(startDate.getMinutes() - 100);

    var sharedAccessPolicy = {
      AccessPolicy: {
        Permissions: azure.BlobUtilities.SharedAccessPermissions.READ,
        Start: startDate,
        Expiry: expiryDate
      },
    };

    var blobSAS = blobSvc.generateSharedAccessSignature('mycontainer', 'myblob', sharedAccessPolicy);
    var host = blobSvc.host;

Bemærk, at oplysningerne om host skal angives også, som det er nødvendigt, når delt adgang signaturer indehaver forsøger at få adgang til objektbeholderen.

Klientprogrammet derefter bruger delt adgang signaturer med **BlobServiceWithSAS** til at udføre handlinger mod blob. Følgende henter oplysninger om **myblob**.

    var sharedBlobSvc = azure.createBlobServiceWithSas(host, blobSAS);
    sharedBlobSvc.getBlobProperties('mycontainer', 'myblob', function (error, result, response) {
      if(!error) {
        // retrieved info
      }
    });

Da delt adgang signaturer blev oprettet med skrivebeskyttet adgang, hvis er forsøgt at ændre blob, returneres en fejl.

### <a name="access-control-lists"></a>Adgangskontrollister

Du kan også bruge en access kontrolelementet liste (ACL) til at angive access-politikken for SAS. Dette er nyttigt, hvis du vil tillade flere klienter mulighed for at få adgang til en objektbeholder, men ikke forskellige access politikker for hver klient.

En ACL implementeres ved hjælp af en matrix med access politikker, med et ID, der er knyttet til hver politik. Følgende kodeeksempel definerer to politikker, én for bruger '1' og én til 'user2':

    var sharedAccessPolicy = {
      user1: {
        Permissions: azure.BlobUtilities.SharedAccessPermissions.READ,
        Start: startDate,
        Expiry: expiryDate
      },
      user2: {
        Permissions: azure.BlobUtilities.SharedAccessPermissions.WRITE,
        Start: startDate,
        Expiry: expiryDate
      }
    };

Følgende kodeeksempel henter den aktuelle ACL for **mycontainer**og lægger derefter de nye politikker ved hjælp af **setBlobAcl**. Denne metode kan:

    var extend = require('extend');
    blobSvc.getBlobAcl('mycontainer', function(error, result, response) {
      if(!error){
        var newSignedIdentifiers = extend(true, result.signedIdentifiers, sharedAccessPolicy);
        blobSvc.setBlobAcl('mycontainer', newSignedIdentifiers, function(error, result, response){
          if(!error){
            // ACL set
          }
        });
      }
    });

Når ACL er angivet, kan du derefter oprette delt adgang signaturer, der er baseret på ID for en politik. Følgende kodeeksempel opretter nye delt adgang signaturer til 'user2':

    blobSAS = blobSvc.generateSharedAccessSignature('mycontainer', { Id: 'user2' });

## <a name="next-steps"></a>Næste trin

Se følgende ressourcer kan finde flere oplysninger.

-   [Azure-lager SDK for Node API Reference][]
-   [Azure-lager-teamets Blog][]
-   [Azure lagerplads SDK for Node][] lager på GitHub
-   [Node.js Developer Center](/develop/nodejs/)
-   [Overføre data sammen med værktøjet AzCopy kommandolinjen](storage-use-azcopy.md)

[Azure-lager SDK for Node]: https://github.com/Azure/azure-storage-node

[Oprette en Node.js WebApp i Azure App Service]: ../app-service-web/web-sites-nodejs-develop-deploy-mac.md
[Node.js Cloud Service with Storage]: ../cloud-services/storage-nodejs-use-table-storage-cloud-service-app.md
[Node.js online ved hjælp af tjenesten Azure-tabel]: ../app-service-web/storage-nodejs-use-table-storage-web-site.md
[Opbygge og anvende en Node.js WebApp til Azure ved hjælp af internettet Matrix]: ../app-service-web/web-sites-nodejs-use-webmatrix.md
[Using the REST API]: http://msdn.microsoft.com/library/azure/hh264518.aspx
[Azure Portal]: https://portal.azure.com
[Opbygge og anvende et Node.js program til en skybaseret tjeneste til Azure]: ../cloud-services/cloud-services-nodejs-develop-deploy-app.md
[Azure-lager-teamets Blog]: http://blogs.msdn.com/b/windowsazurestorage/
[Azure-lager SDK for Node API Reference]: http://dl.windowsazure.com/nodestoragedocs/index.html
