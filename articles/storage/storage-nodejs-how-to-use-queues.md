<properties
    pageTitle="Sådan bruger du kø lagerplads fra Node.js | Microsoft Azure"
    description="Lær, hvordan du bruger tjenesten Azure kø til at oprette og slette køer, og Indsæt, hente og slette meddelelser. Eksempler, der er skrevet i Node.js."
    services="storage"
    documentationCenter="nodejs"
    authors="robinsh"
    manager="carmonm"
    editor="tysonn"/>

<tags
    ms.service="storage"
    ms.workload="storage"
    ms.tgt_pltfrm="na"
    ms.devlang="nodejs"
    ms.topic="article"
    ms.date="08/11/2016"
    ms.author="robinsh"/>


# <a name="how-to-use-queue-storage-from-nodejs"></a>Sådan bruger du kø lagerplads fra Node.js

[AZURE.INCLUDE [storage-selector-queue-include](../../includes/storage-selector-queue-include.md)]
<br/>
[AZURE.INCLUDE [storage-try-azure-tools-queues](../../includes/storage-try-azure-tools-queues.md)]

## <a name="overview"></a>Oversigt

Denne vejledning viser, hvordan du udfører almindelige scenarier ved hjælp af tjenesten Microsoft Azure kø. Eksemplerne skrives ved hjælp af Node.js API. De scenarier, der er omfattet omfatter **indsættelse**, **visning**, **hente**og **slette** beskeder i kø samt **oprettelse og sletning af køer**.

[AZURE.INCLUDE [storage-queue-concepts-include](../../includes/storage-queue-concepts-include.md)]

[AZURE.INCLUDE [storage-create-account-include](../../includes/storage-create-account-include.md)]

## <a name="create-a-nodejs-application"></a>Oprette et Node.js program

Oprette en tom Node.js-program. Du kan finde instruktioner, der opretter et Node.js program, [Opret en Node.js WebApp i Azure App Service], [opbygge og anvende et Node.js program til en skybaseret tjeneste til Azure] ved hjælp af Windows PowerShell eller [opbygge og anvende en Node.js web-app til Azure ved hjælp af Web Matrix].

## <a name="configure-your-application-to-access-storage"></a>Konfigurere dit program til Access-lager

Hvis du vil bruge Azure lagerplads, skal du i Azure lagerplads SDK for Node.js, som indeholder et sæt af nemmere biblioteker, kommunikerer med lagerplads RESTEN tjenester.

### <a name="use-node-package-manager-npm-to-obtain-the-package"></a>Brug Node pakke Manager (NPM) til at hente pakken

1.  Bruge en kommandolinjen som **PowerShell** (Windows), **Terminal** (Mac) eller **Bash** (Unix), gå til den mappe, hvor du har oprettet din Northwind.

2.  Skriv **npm installere azure-lager** i kommandovinduet. Output fra kommandoen ligner i følgende eksempel.

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

3.  Du kan manuelt køre kommandoen **ls** at kontrollere, at en **node\_moduler** mappe blev oprettet. I denne mappe kan du finde pakken **azure-lager** , som indeholder de biblioteker, du har brug for at få adgang til lagerplads.

### <a name="import-the-package"></a>Importere pakken

Bruger Notesblok eller et andet tekstredigeringsprogram, skal du tilføje følgende til toppen filen **server.js** af programmet, hvor du vil bruge lagerplads:

    var azure = require('azure-storage');

## <a name="setup-an-azure-storage-connection"></a>Konfiguration af forbindelse til Azure-lager

Modulet azure læser miljøvariabler AZURE\_LAGERPLADS\_konto og AZURE\_LAGERPLADS\_ACCESS\_nøgle eller AZURE\_LAGERPLADS\_forbindelse\_streng oplysninger, der kræves for at oprette forbindelse til kontoen Azure-lager. Hvis variablerne miljø ikke er angivet, skal du angive kontooplysninger, når du ringer til **createQueueService**.

Du kan finde et eksempel på indstilling miljøvariabler i [Azure Portal](https://portal.azure.com) til et websted, der Azure, [Node.js online ved hjælp af tjenesten Azure tabel].

## <a name="how-to-create-a-queue"></a>Sådan: Oprette en kø

Følgende kode opretter et **QueueService** objekt, der gør det muligt at arbejde med køer.

    var queueSvc = azure.createQueueService();

Bruge metoden **createQueueIfNotExists** , der returnerer den angivne kø, hvis det allerede findes eller opretter en ny kø med det angivne navn, hvis den ikke allerede findes.

    queueSvc.createQueueIfNotExists('myqueue', function(error, result, response){
      if(!error){
        // Queue created or exists
      }
    });

Hvis køen oprettes, `result.created` er sand. Hvis køen findes, `result.created` er falsk.

### <a name="filters"></a>Filtre

Valgfri spamfiltrering handlinger kan anvendes på Handlinger, der udføres ved hjælp af **QueueService**. Filtrering handlinger kan medtage logføring, automatisk prøver igen osv. Filtre er objekter, der implementerer en metode til signaturen:

    function handle (requestOptions, next)

Herefter dens forbehandling på indstillingerne for anmodning om kræver metoden til at ringe til "Næste" der passerer et tilbagekald med den følgende signatur:

    function (returnObject, finalCallback, next)

I denne tilbagekald, og efter behandling af returnObject (svar fra anmodningen på serveren), skal tilbagekaldet enten kalde næste, hvis den findes for at fortsætte med at behandle andre filtre eller blot kalde finalCallback ellers for at afslutte op service aktiveringen.

To filtre, der implementerer forsøg er inkluderet med Azure SDK til Node.js, **ExponentialRetryPolicyFilter** og **LinearRetryPolicyFilter**. Følgende opretter en **QueueService** -objekt, der bruger **ExponentialRetryPolicyFilter**:

    var retryOperations = new azure.ExponentialRetryPolicyFilter();
    var queueSvc = azure.createQueueService().withFilter(retryOperations);

## <a name="how-to-insert-a-message-into-a-queue"></a>Sådan: Indsætte en meddelelse i en kø

Hvis du vil indsætte en meddelelse i en kø, skal du bruge metoden **createMessage** til at oprette en ny meddelelse og føje det til køen.

    queueSvc.createMessage('myqueue', "Hello world!", function(error, result, response){
      if(!error){
        // Message inserted
      }
    });

## <a name="how-to-peek-at-the-next-message"></a>Sådan: Kig på den næste meddelelse

Du kan vise meddelelsen foran en kø uden at fjerne den fra køen ved at ringe til metoden **peekMessages** . Som standard smugkig **peekMessages** på en enkelt meddelelse.

    queueSvc.peekMessages('myqueue', function(error, result, response){
      if(!error){
        // Message text is in messages[0].messageText
      }
    });

Den `result` indeholder meddelelsen.

> [AZURE.NOTE] Brug af **peekMessages** , når der er ingen meddelelser i køen ikke returnerer en fejl, men ingen meddelelser bliver ikke returneret.

## <a name="how-to-dequeue-the-next-message"></a>Sådan: Annullering i kø den næste meddelelse

Behandling af en meddelelse er en proces i to faser:

1. Annullering i kø meddelelsen.

2. Slet meddelelsen.

Hvis du vil annullering i kø en meddelelse, du brug **getMessages**. Dette gør meddelelserne usynlig i køen, så ingen andre klienter kan behandle dem. Når din ansøgning har behandlet meddelelsen, ringe **deleteMessage** for at slette den fra køen. I følgende eksempel får en meddelelse, og derefter sletter det:

    queueSvc.getMessages('myqueue', function(error, result, response){
      if(!error){
        // Message text is in messages[0].messageText
        var message = result[0];
        queueSvc.deleteMessage('myqueue', message.messageId, message.popReceipt, function(error, response){
          if(!error){
            //message deleted
          }
        });
      }
    });

> [AZURE.NOTE] Som standard skjult meddelelsen kun til 30 sekunder, hvorefter det er synligt for andre klienter. Du kan angive en anden værdi ved hjælp af `options.visibilityTimeout` med **getMessages**.

> [AZURE.NOTE]
> Brug af **getMessages** , når der er ingen meddelelser i køen ikke returnerer en fejl, men ingen meddelelser bliver ikke returneret.

## <a name="how-to-change-the-contents-of-a-queued-message"></a>Sådan: Ændre indholdet af en meddelelse i køen

Du kan ændre indholdet af en meddelelse direkte i køen ved hjælp af **updateMessage**. I følgende eksempel opdaterer teksten i en meddelelse:

    queueSvc.getMessages('myqueue', function(error, result, response){
      if(!error){
        // Got the message
        var message = result[0];
        queueSvc.updateMessage('myqueue', message.messageId, message.popReceipt, 10, {messageText: 'new text'}, function(error, result, response){
          if(!error){
            // Message updated successfully
          }
        });
      }
    });

## <a name="how-to-additional-options-for-dequeuing-messages"></a>Sådan: Yderligere indstillinger for Sådan flytning meddelelser

Der er to måder, du kan tilpasse hentning af meddelelse fra en kø:

* `options.numOfMessages`-Hente en mængde meddelelser (op til 32).
* `options.visibilityTimeout`– Angiv timeout for en længere eller kortere invisibility.

I følgende eksempel bruges metoden **getMessages** til at få 15 meddelelser i et opkald. Vælg derefter det behandler hver meddelelse ved hjælp af et for-løkke. Invisibility timeouten angives også til fem minutter for alle de meddelelser, der returneres af denne metode.

    queueSvc.getMessages('myqueue', {numOfMessages: 15, visibilityTimeout: 5 * 60}, function(error, result, response){
      if(!error){
        // Messages retreived
        for(var index in result){
          // text is available in result[index].messageText
          var message = result[index];
          queueSvc.deleteMessage(queueName, message.messageId, message.popReceipt, function(error, response){
            if(!error){
              // Message deleted
            }
          });
        }
      }
    });

## <a name="how-to-get-the-queue-length"></a>Sådan: Få længde kø

**GetQueueMetadata** returnerer metadata om køen, herunder tilnærmet antallet af meddelelser, der venter i køen.

    queueSvc.getQueueMetadata('myqueue', function(error, result, response){
      if(!error){
        // Queue length is available in result.approximateMessageCount
      }
    });

## <a name="how-to-list-queues"></a>Sådan: Liste i kø

For at hente en liste over køer skal du bruge **listQueuesSegmented**. For at hente en liste, der er filtreret efter et bestemt præfiks skal du bruge **listQueuesSegmentedWithPrefix**.

    queueSvc.listQueuesSegmented(null, function(error, result, response){
      if(!error){
        // result.entries contains the list of queues
      }
    });

Hvis alle køer ikke kan returneres, `result.continuationToken` kan bruges som den første parameter i **listQueuesSegmented** eller den anden parameter af **listQueuesSegmentedWithPrefix** til at hente flere resultater.

## <a name="how-to-delete-a-queue"></a>Sådan: Slette en kø

Hvis du vil slette en kø og alle de meddelelser, der er indeholdt i den, skal ringe til metoden **deleteQueue** på objektet kø.

    queueSvc.deleteQueue(queueName, function(error, response){
      if(!error){
        // Queue has been deleted
      }
    });

For at rydde alle meddelelser fra en kø uden at slette den, skal du bruge **clearMessages**.

## <a name="how-to-work-with-shared-access-signatures"></a>Sådan: arbejde med delt adgang signaturer

Delte Access signaturer (SAS) er en sikker måde til at give findelt adgang til køer uden at angive dit kontonavn til lager eller -nøglerne. SAS bruges ofte til at give begrænset adgang til din køer, som giver mulighed for en mobilapp til at sende meddelelser.

En der er tillid til programmet på computeren som en skybaseret tjeneste genererer en SAS ved hjælp af **generateSharedAccessSignature** af **QueueService**og leverer den til upålidelige eller halvgennemsigtig der er tillid til programmet. For eksempel en mobilapp. Sikkerhedstilknytningerne er oprettet ved hjælp af en politik, som beskriver de start- og slutdatoer, hvor Sikkerhedstilknytningerne er gyldig, samt det adgangsniveau, der er tildelt til SAS indehaver.

I følgende eksempel oprettes en ny politik for delt adgang, som tillader SAS indehaver at føje meddelelser til køen, og udløber 100 minutter, efter den tid, det er oprettet.

    var startDate = new Date();
    var expiryDate = new Date(startDate);
    expiryDate.setMinutes(startDate.getMinutes() + 100);
    startDate.setMinutes(startDate.getMinutes() - 100);

    var sharedAccessPolicy = {
      AccessPolicy: {
        Permissions: azure.QueueUtilities.SharedAccessPermissions.ADD,
        Start: startDate,
        Expiry: expiryDate
      }
    };

    var queueSAS = queueSvc.generateSharedAccessSignature('myqueue', sharedAccessPolicy);
    var host = queueSvc.host;

Bemærk, at oplysningerne om host skal angives også, som det er nødvendigt, når SAS indehaver forsøger at få adgang til køen.

Klientprogrammet derefter bruger Sikkerhedstilknytningerne med **QueueServiceWithSAS** til at udføre handlinger mod køen. I følgende eksempel opretter forbindelse til køen og opretter en meddelelse.

    var sharedQueueService = azure.createQueueServiceWithSas(host, queueSAS);
    sharedQueueService.createMessage('myqueue', 'Hello world from SAS!', function(error, result, response){
      if(!error){
        //message added
      }
    });

Da Sikkerhedstilknytningerne blev oprettet med Tilføj adgang, hvis der er foretaget et forsøg på at læse, opdatere eller slette meddelelser, returneres en fejl.

### <a name="access-control-lists"></a>Adgangskontrollister

Du kan også bruge en liste over ACL (Access Control) til at angive access-politikken for en SAS. Dette er nyttigt, hvis du vil tillade flere klienter mulighed for at få adgang til køen, men ikke forskellige access politikker for hver klient.

En ACL implementeres ved hjælp af en matrix med access politikker, med et ID, der er knyttet til hver politik. I følgende eksempel definerer to politikker én for 'bruger 1' og én til 'user2':

    var sharedAccessPolicy = {
      user1: {
        Permissions: azure.QueueUtilities.SharedAccessPermissions.PROCESS,
        Start: startDate,
        Expiry: expiryDate
      },
      user2: {
        Permissions: azure.QueueUtilities.SharedAccessPermissions.ADD,
        Start: startDate,
        Expiry: expiryDate
      }
    };

I følgende eksempel henter den aktuelle ACL for **myqueue**, og derefter tilføjer de nye politikker ved hjælp af **setQueueAcl**. Denne metode kan:

    var extend = require('extend');
    queueSvc.getQueueAcl('myqueue', function(error, result, response) {
      if(!error){
        var newSignedIdentifiers = extend(true, result.signedIdentifiers, sharedAccessPolicy);
        queueSvc.setQueueAcl('myqueue', newSignedIdentifiers, function(error, result, response){
          if(!error){
            // ACL set
          }
        });
      }
    });

Når ACL, der er angivet, kan du derefter oprette en SAS baseret på ID for en politik. I følgende eksempel oprettes en ny SAS for 'user2':

    queueSAS = queueSvc.generateSharedAccessSignature('myqueue', { Id: 'user2' });

## <a name="next-steps"></a>Næste trin

Nu hvor du har lært grundlæggende kø lagerplads, skal du følge disse links for at lære mere komplekse lagerplads opgaver.

-   Gå til [Azure-lager-teamets Blog][].
-   Besøg [Azure lagerplads SDK for Node][] lager på GitHub.

  [Azure-lager SDK for Node]: https://github.com/Azure/azure-storage-node
  [using the REST API]: http://msdn.microsoft.com/library/azure/hh264518.aspx
  [Azure Portal]: https://portal.azure.com
  [Oprette en Node.js WebApp i Azure App Service]: ../app-service-web/web-sites-nodejs-develop-deploy-mac.md
  [Node.js Cloud Service with Storage]: ../cloud-services/storage-nodejs-use-table-storage-cloud-service-app.md
  [Node.js online ved hjælp af tjenesten Azure-tabel]: ../app-service-web/storage-nodejs-use-table-storage-web-site.md


  [Queue1]: ./media/storage-nodejs-how-to-use-queues/queue1.png
  [plus-new]: ./media/storage-nodejs-how-to-use-queues/plus-new.png
  [quick-create-storage]: ./media/storage-nodejs-how-to-use-queues/quick-storage.png



  [Opbygge og anvende et Node.js program til en skybaseret tjeneste til Azure]: ../cloud-services/cloud-services-nodejs-develop-deploy-app.md
  [Azure-lager-teamets Blog]: http://blogs.msdn.com/b/windowsazurestorage/
  [Opbygge og anvende en Node.js web-app til Azure ved hjælp af Web Matrix]: ../app-service-web/web-sites-nodejs-use-webmatrix.md
