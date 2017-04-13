<properties
    pageTitle="Sådan bruger du Azure Table storage fra Node.js | Microsoft Azure"
    description="Gem strukturerede data i skyen ved hjælp af Azure Table storage, et NoSQL datalager."
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


# <a name="how-to-use-azure-table-storage-from-nodejs"></a>Sådan bruger du Azure Table storage fra Node.js

[AZURE.INCLUDE [storage-selector-table-include](../../includes/storage-selector-table-include.md)]
<br/>
[AZURE.INCLUDE [storage-try-azure-tools-queues](../../includes/storage-try-azure-tools-tables.md)]

## <a name="overview"></a>Oversigt

Dette emne viser, hvordan til at udføre almindelige scenarier ved hjælp af tjenesten Azure-tabel i et Node.js til computeren.

Kodeeksempler i dette emne forudsætter, at du allerede har et Node.js program. Du kan finde oplysninger om, hvordan du opretter en Node.js program i Azure, du ser en af disse emner:

- [Oprette en Node.js WebApp i Azure App Service](../app-service-web/web-sites-nodejs-develop-deploy-mac.md)
- [Opbygge og anvende en Node.js web-app til Azure ved hjælp af WebMatrix](../app-service-web/web-sites-nodejs-use-webmatrix.md)
- [Opbygge og anvende et Node.js program til en skybaseret tjeneste til Azure](../cloud-services/cloud-services-nodejs-develop-deploy-app.md) (ved hjælp af Windows PowerShell)


[AZURE.INCLUDE [storage-table-concepts-include](../../includes/storage-table-concepts-include.md)]

[AZURE.INCLUDE [storage-create-account-include](../../includes/storage-create-account-include.md)]


## <a name="configure-your-application-to-access-azure-storage"></a>Konfigurere dit program tilladelse til at få adgang til Azure-lager

Hvis du vil bruge Azure-lager, skal du i Azure lagerplads SDK for Node.js, som indeholder et sæt af nemmere biblioteker, kommunikerer med lagerplads RESTEN tjenester.

### <a name="use-node-package-manager-npm-to-install-the-package"></a>Bruge Node pakke Manager (NPM) til at installere pakken

1.  Brug en kommandolinjen som **PowerShell** (Windows), **Terminal** (Mac) eller **Bash** (Unix), og Naviger til den mappe, hvor du har oprettet dit program.

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

Føj følgende kode til toppen af filen **server.js** i dit program:

    var azure = require('azure-storage');

## <a name="set-up-an-azure-storage-connection"></a>Konfigurere en forbindelse til Azure-lager

Modulet azure læser miljøvariabler AZURE\_LAGERPLADS\_konto og AZURE\_LAGERPLADS\_ACCESS\_TASTEN eller AZURE\_LAGERPLADS\_forbindelse\_streng oplysninger, der kræves for at oprette forbindelse til kontoen Azure-lager. Hvis variablerne miljø ikke er angivet, skal du angive kontooplysninger, når du ringer til **TableService**.

Du kan finde et eksempel på indstilling miljøvariabler i [Azure Portal](https://portal.azure.com) til et websted, der Azure, [Node.js online ved hjælp af tjenesten Azure tabel].

## <a name="create-a-table"></a>Oprette en tabel

Følgende kode opretter et **TableService** objekt og bruges til at oprette en ny tabel. Tilføj følgende i øverst del af **server.js**.

    var tableSvc = azure.createTableService();

Opkaldet til **createTableIfNotExists** oprette en ny tabel med det angivne navn, hvis den ikke allerede findes. I følgende eksempel oprettes en ny tabel med navnet 'MinTabel', hvis den ikke allerede findes:

    tableSvc.createTableIfNotExists('mytable', function(error, result, response){
      if(!error){
        // Table exists or created
      }
    });

Den `result.created` bliver `true` Hvis der oprettes en ny tabel, og `false` Hvis der allerede findes i tabellen. Den `response` skal indeholde oplysninger om anmodningen.

### <a name="filters"></a>Filtre

Valgfri spamfiltrering handlinger kan anvendes på Handlinger, der udføres ved hjælp af **TableService**. Filtrering handlinger kan medtage logføring, automatisk prøver igen osv. Filtre er objekter, der implementerer en metode til signaturen:

    function handle (requestOptions, next)

Herefter dens forbehandling på indstillingerne for anmodning om kræver metoden til at ringe til "Næste", der passerer et tilbagekald med den følgende signatur:

    function (returnObject, finalCallback, next)

I denne tilbagekald og efter behandling af returnObject (svar fra anmodningen på serveren), skal tilbagekaldet enten kalde næste, hvis den findes for at fortsætte med at behandle andre filtre eller blot kalde finalCallback ellers for at afslutte tjenesten aktiveringen.

To filtre, der implementerer forsøg er inkluderet med Azure SDK til Node.js, **ExponentialRetryPolicyFilter** og **LinearRetryPolicyFilter**. Følgende opretter en **TableService** -objekt, der bruger **ExponentialRetryPolicyFilter**:

    var retryOperations = new azure.ExponentialRetryPolicyFilter();
    var tableSvc = azure.createTableService().withFilter(retryOperations);

## <a name="add-an-entity-to-a-table"></a>Føje et objekt til en tabel

Hvis du vil tilføje et objekt, skal du først oprette et objekt, der definerer egenskaber for din enhed. Alle objekter skal indeholde en **PartitionKey** og **RowKey**, som er entydige identifikatorer for objektet.

* **PartitionKey** - bestemmer den partition, der er gemt objektet i

* **RowKey** - entydigt identificerer objektet i partitionen

Både **PartitionKey** og **RowKey** skal være strengværdier. Du kan finde flere oplysninger, kan du se [om tabel Service datamodellen](http://msdn.microsoft.com/library/azure/dd179338.aspx).

Følgende er et eksempel på definerer et objekt. Bemærk dette **eksempel** er defineret som en slags **Edm.DateTime**. Angive, hvilken type er valgfrit, og typer skal være udledes Hvis ikke er angivet.

    var task = {
      PartitionKey: {'_':'hometasks'},
      RowKey: {'_': '1'},
      description: {'_':'take out the trash'},
      dueDate: {'_':new Date(2015, 6, 20), '$':'Edm.DateTime'}
    };

> [AZURE.NOTE] Der er også et **tidsstempel** felt for hver post, som er angivet af Azure, når et objekt indsættes eller opdateres.

Du kan også bruge **entityGenerator** til at oprette enheder. I følgende eksempel oprettes den samme opgave enhed ved hjælp af **entityGenerator**.

    var entGen = azure.TableUtilities.entityGenerator;
    var task = {
      PartitionKey: entGen.String('hometasks'),
      RowKey: entGen.String('1'),
      description: entGen.String('take out the trash'),
      dueDate: entGen.DateTime(new Date(Date.UTC(2015, 6, 20))),
    };

Hvis du vil tilføje et objekt i tabellen, du overføre objektet enhed til metoden **insertEntity** .

    tableSvc.insertEntity('mytable',task, function (error, result, response) {
      if(!error){
        // Entity inserted
      }
    });

Hvis handlingen lykkes, `result` skal indeholde [ETag](http://en.wikipedia.org/wiki/HTTP_ETag) i den indsatte post og `response` skal indeholde oplysninger om handlingen.

Eksempel svar:

    { '.metadata': { etag: 'W/"datetime\'2015-02-25T01%3A22%3A22.5Z\'"' } }

> [AZURE.NOTE] Som standard **insertEntity** ikke returnerer objektet indsat som en del af den `response` oplysninger. Hvis du planlægger at udføre andre handlinger på denne enhed eller ønsker at cache oplysninger, kan det være praktisk at har den returneret som en del af den `result`. Du kan gøre dette ved at aktivere **echoContent** på følgende måde:
>
> `tableSvc.insertEntity('mytable', task, {echoContent: true}, function (error, result, response) {...}`

## <a name="update-an-entity"></a>Opdatere et objekt

Der er flere metoder til at opdatere et eksisterende objekt:

* **replaceEntity** - opdaterer en eksisterende enhed ved at erstatte den

* **mergeEntity** - opdaterer en eksisterende enhed ved at flette nye egenskabsværdier til den eksisterende enhed

* **insertOrReplaceEntity** - opdaterer en eksisterende enhed ved at erstatte den. Hvis der findes ingen objekt, der skal indsættes en ny

* **insertOrMergeEntity** - opdaterer en eksisterende enhed ved at flette nye egenskabsværdier til den eksisterende. Hvis der findes ingen objekt, der skal indsættes en ny

I følgende eksempel viser opdatering af et objekt ved hjælp af **replaceEntity**:

    tableSvc.replaceEntity('mytable', updatedTask, function(error, result, response){
      if(!error) {
        // Entity updated
      }
    });

> [AZURE.NOTE] Som standard kontrollerer opdatering af et objekt ikke, hvis du vil se, om dataene opdateres tidligere er blevet ændret af en anden proces. Til at understøtte samtidige opdateringer:
>
> 1. Få ETag for objektet blive opdateret. Dette er returneret som en del af den `response` for en hvilken som helst enhed relateret handling og kan hentes via `response['.metadata'].etag`.
>
> 2. Når du udfører en opdateringshandling på et objekt, kan du tilføje ETag oplysningerne tidligere hentet til det nye objekt. Eksempel:
>
>     `entity2['.metadata'].etag = currentEtag;`
>
> 3. Udføre handlingen update. Hvis objektet er blevet ændret, da du har hentet ETag-værdien, som en anden forekomst af dit program, en `error` returneres om, at betingelsen opdatering, der er angivet i anmodningen ikke blev er opfyldt.

Med **replaceEntity** og **mergeEntity**, hvis den enhed, der opdateres ikke findes, mislykkes derefter handlingen update. Hvis du vil gemme et objekt, uanset om de allerede forefindes derfor, bruge **insertOrReplaceEntity** eller **insertOrMergeEntity**.

Den `result` til vellykket opdatering handlinger skal indeholde **Etag** for den opdaterede enhed.

## <a name="work-with-groups-of-entities"></a>Arbejde med grupper af enheder

Nogle gange giver det mening at sende flere handlinger sammen i en gruppe at sikre, at atomisk behandling af serveren. Brug klassen **TableBatch** til at oprette en gruppe for at udføre dette, og derefter bruge metoden **executeBatch** for **TableService** til at udføre de batchhandlinger.

 I følgende eksempel viser sende to objekter i en gruppe:

    var task1 = {
      PartitionKey: {'_':'hometasks'},
      RowKey: {'_': '1'},
      description: {'_':'Take out the trash'},
      dueDate: {'_':new Date(2015, 6, 20)}
    };
    var task2 = {
      PartitionKey: {'_':'hometasks'},
      RowKey: {'_': '2'},
      description: {'_':'Wash the dishes'},
      dueDate: {'_':new Date(2015, 6, 20)}
    };

    var batch = new azure.TableBatch();

    batch.insertEntity(task1, {echoContent: true});
    batch.insertEntity(task2, {echoContent: true});

    tableSvc.executeBatch('mytable', batch, function (error, result, response) {
      if(!error) {
        // Batch completed
      }
    });

Til vellykket batchen handlinger, `result` skal indeholde oplysninger for hver handling i batchen.

### <a name="work-with-batched-operations"></a>Arbejde med batches handlinger

Handlinger, der er føjet til en gruppe kan undersøges ved at få vist den `operations` egenskab. Du kan også bruge følgende metoder til at fungere sammen med handlinger:

* **Fjern** - rydder alle handlinger fra en gruppe

* **getOperations** - henter en handling fra batchen

* **hasOperations** - returnerer SAND, hvis batchen indeholder handlinger

* **removeOperations** - fjerner en handling

* **størrelse** - returnerer antallet handlinger i batchen

## <a name="retrieve-an-entity-by-key"></a>Hente et objekt med nøglen

Hvis du vil returnere en bestemt enhed, der er baseret på **PartitionKey** og **RowKey**, skal du bruge metoden **retrieveEntity** .

    tableSvc.retrieveEntity('mytable', 'hometasks', '1', function(error, result, response){
      if(!error){
        // result contains the entity
      }
    });

Når denne handling er fuldført, `result` skal indeholde objektet.

## <a name="query-a-set-of-entities"></a>Forespørgsel et sæt af objekter

Hvis du vil forespørge en tabel, du brug objektet **TableQuery** for at opbygge en forespørgselsudtryk ved hjælp af følgende delsætninger:

* **Vælg** - felterne, der skal returneres fra forespørgslen

* **hvor** - where delsætning

    * **og** - en `and` where-betingelse

    * **eller** - en `or` where-betingelse

* **top** - antallet af elementer til at hente


I følgende eksempel oprettes en forespørgsel, der returnerer de øverste fem elementer med en PartitionKey af 'hometasks'.

    var query = new azure.TableQuery()
      .top(5)
      .where('PartitionKey eq ?', 'hometasks');

Da **Vælg** ikke bruges, returneres alle felter. Brug **queryEntities**til at udføre forespørgslen i en tabel. I følgende eksempel bruges denne forespørgsel til at returnere objekter fra 'MinTabel'.

    tableSvc.queryEntities('mytable',query, null, function(error, result, response) {
      if(!error) {
        // query was successful
      }
    });

Hvis det lykkes, `result.entries` skal indeholde en matrix med enheder, der svarer til forespørgslen. Hvis forespørgslen kunne ikke til at returnere alle objekter, `result.continuationToken` er ikke -*Null-værdier* og kan bruges som den tredje parameter i **queryEntities** til at hente flere resultater. Den oprindelige forespørgsel, du brug *null* for parameteren tredje.

### <a name="query-a-subset-of-entity-properties"></a>Forespørgsel et undersæt af egenskaber for enheder

En forespørgsel til en tabel kan hente blot nogle få felter fra en enhed.
Dette reducerer båndbredde og kan forbedre forespørgselsydeevne, især for store enheder. Bruge delsætningen **Vælg** og overføre navnene på felterne, der skal returneres. For eksempel returnerer følgende forespørgsel kun felterne **Beskrivelse** og **eksempel** .

    var query = new azure.TableQuery()
      .select(['description', 'dueDate'])
      .top(5)
      .where('PartitionKey eq ?', 'hometasks');

## <a name="delete-an-entity"></a>Slette et objekt

Du kan slette et objekt med dens partition og række taster. I dette eksempel indeholder objektet **task1** **RowKey** og **PartitionKey** værdierne for enheden, der skal slettes. Vælg derefter objektet, der sendes til metoden **deleteEntity** .

    var task = {
      PartitionKey: {'_':'hometasks'},
      RowKey: {'_': '1'}
    };

    tableSvc.deleteEntity('mytable', task, function(error, response){
      if(!error) {
        // Entity deleted
      }
    });

> [AZURE.NOTE] Overvej at bruge ETags, når du sletter elementer, for at sikre, at elementet ikke er blevet ændret af en anden proces. Se [opdatere et objekt](#update-an-entity) for oplysninger om brug af ETags.

## <a name="delete-a-table"></a>Slette en tabel

Følgende kode sletter en tabel fra en lagerplads-konto.

    tableSvc.deleteTable('mytable', function(error, response){
        if(!error){
            // Table deleted
        }
    });

Hvis du er usikker på, om der findes på tabellen, kan du bruge **deleteTableIfExists**.

## <a name="use-continuation-tokens"></a>Brug fortsættelse tokens

Når du forespørger på tabeller for store mængder resultater, kan du se efter fortsættelse tokens. Der kan være store mængder data tilgængelige for din forespørgsel, som du ikke muligvis opdage, hvis du ikke opretter til at genkende under et fortsættelse token er til stede.

Resultaterne objekt returneres under forespørgsler enheder sæt en `continuationToken` egenskaben når disse token er til stede. Du kan derefter bruge dette når du udfører en forespørgsel til at fortsætte med at flytte på tværs af partition og tabel enheder.

Når forespørgsler, kan der skal findes en parameter for continuationToken mellem forekomsten af forespørgsel objektet og Tilbagekaldsfunktionen:

```
var nextContinuationToken = null;
dc.table.queryEntities(tableName,
    query,
    nextContinuationToken,
    function (error, results) {
        if (error) throw error;

        // iterate through results.entries with results

        if (results.continuationToken) {
            nextContinuationToken = results.continuationToken;
        }

    });
```

Hvis du undersøge de `continuationToken` objekt, du kan finde egenskaber såsom `nextPartitionKey`, `nextRowKey` og `targetLocation`, som kan bruges til at navigere gennem alle resultaterne.

Der er også et eksempel på fortsættelse i Azure-lager Node.js repo på GitHub. Se efter `examples/samples/continuationsample.js`.

## <a name="work-with-shared-access-signatures"></a>Arbejde med delt adgang signaturer

Delt adgang signaturer (SAS) er en sikker måde at give findelt adgang til tabeller uden at angive dit kontonavn til lager eller -nøglerne. SAS bruges ofte til at give begrænset adgang til dine data, som giver mulighed for en mobilapp til forespørgselsposter.

En der er tillid til programmet på computeren som en skybaseret tjeneste genererer en SAS ved hjælp af **generateSharedAccessSignature** af **TableService**, og leverer den til et upålidelige eller halvgennemsigtig pålidelig program som en mobilapp. Sikkerhedstilknytningerne er oprettet ved hjælp af en politik, som beskriver de start- og slutdatoer, hvor Sikkerhedstilknytningerne er gyldig, samt det adgangsniveau, der er tildelt til SAS indehaver.

I følgende eksempel oprettes en ny politik for delt adgang, som tillader SAS indehaver til forespørgsel ("r") tabellen, og udløber 100 minutter, efter den tid, det er oprettet.

    var startDate = new Date();
    var expiryDate = new Date(startDate);
    expiryDate.setMinutes(startDate.getMinutes() + 100);
    startDate.setMinutes(startDate.getMinutes() - 100);

    var sharedAccessPolicy = {
      AccessPolicy: {
        Permissions: azure.TableUtilities.SharedAccessPermissions.QUERY,
        Start: startDate,
        Expiry: expiryDate
      },
    };

    var tableSAS = tableSvc.generateSharedAccessSignature('mytable', sharedAccessPolicy);
    var host = tableSvc.host;

Bemærk, at oplysningerne om host skal angives også, som det er nødvendigt, når SAS indehaver forsøger at få adgang til tabellen.

Klientprogrammet derefter bruger Sikkerhedstilknytningerne med **TableServiceWithSAS** til at udføre handlinger mod en tabel. I følgende eksempel opretter forbindelse til tabellen og udfører en forespørgsel.

    var sharedTableService = azure.createTableServiceWithSas(host, tableSAS);
    var query = azure.TableQuery()
      .where('PartitionKey eq ?', 'hometasks');

    sharedTableService.queryEntities(query, null, function(error, result, response) {
      if(!error) {
        // result contains the entities
      }
    });

Da Sikkerhedstilknytningerne blev oprettet med kun forespørgselsadgang, hvis der er foretaget et forsøg på at indsætte, opdatere eller slette objekter, returneres en fejl.

### <a name="access-control-lists"></a>Adgangskontrollister

Du kan også bruge en liste over ACL (Access Control) til at angive access-politikken for en SAS. Dette er nyttigt, hvis du vil tillade flere klienter mulighed for at få adgang til tabellen, men ikke forskellige access politikker for hver klient.

En ACL implementeres ved hjælp af en matrix med access politikker, med et ID, der er knyttet til hver politik. I følgende eksempel definerer to politikker, én for bruger '1' og én til 'user2':

    var sharedAccessPolicy = {
      user1: {
        Permissions: azure.TableUtilities.SharedAccessPermissions.QUERY,
        Start: startDate,
        Expiry: expiryDate
      },
      user2: {
        Permissions: azure.TableUtilities.SharedAccessPermissions.ADD,
        Start: startDate,
        Expiry: expiryDate
      }
    };

I følgende eksempel henter den aktuelle ACL for **hometasks** tabellen, og derefter tilføjer de nye politikker ved hjælp af **setTableAcl**. Denne metode kan:

    var extend = require('extend');
    tableSvc.getTableAcl('hometasks', function(error, result, response) {
    if(!error){
        var newSignedIdentifiers = extend(true, result.signedIdentifiers, sharedAccessPolicy);
        tableSvc.setTableAcl('hometasks', newSignedIdentifiers, function(error, result, response){
          if(!error){
            // ACL set
          }
        });
      }
    });

Når ACL, der er angivet, kan du derefter oprette en SAS baseret på ID for en politik. I følgende eksempel oprettes en ny SAS for 'user2':

    tableSAS = tableSvc.generateSharedAccessSignature('hometasks', { Id: 'user2' });

## <a name="next-steps"></a>Næste trin

Se følgende ressourcer kan finde flere oplysninger.

-   [Azure-lager-teamets Blog][].
-   [Azure lagerplads SDK for Node][] lager på GitHub.
-   [Node.js Developer Center](/develop/nodejs/)

  [Azure lagerplads SDK for Node]: https://github.com/Azure/azure-storage-node
  [OData.org]: http://www.odata.org/
  [Using the REST API]: http://msdn.microsoft.com/library/azure/hh264518.aspx
  [Azure Portal]: portal.azure.com

  [Node.js Cloud Service]: ../cloud-services-nodejs-develop-deploy-app.md
  [Azure-lager-teamets Blog]: http://blogs.msdn.com/b/windowsazurestorage/
  [Website with WebMatrix]: ../web-sites-nodejs-use-webmatrix.md
  [Node.js Cloud Service with Storage]: ../storage-nodejs-use-table-storage-cloud-service-app.md
  [Node.js online ved hjælp af tjenesten Azure-tabel]: ../storage-nodejs-use-table-storage-web-site.md
  [Create and deploy a Node.js application to an Azure website]: ../web-sites-nodejs-develop-deploy-mac.md
