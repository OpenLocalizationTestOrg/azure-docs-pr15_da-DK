<properties
    pageTitle="Azure funktioner DocumentDB bindinger | Microsoft Azure"
    description="Forstå, hvordan du bruger Azure DocumentDB bindinger i Azure funktioner."
    services="functions"
    documentationCenter="na"
    authors="christopheranderson"
    manager="erikre"
    editor=""
    tags=""
    keywords="Azure fungerer, funktioner, begivenhed behandling, dynamisk Beregn, ikke-serverbaseret arkitektur"/>

<tags
    ms.service="functions"
    ms.devlang="multiple"
    ms.topic="reference"
    ms.tgt_pltfrm="multiple"
    ms.workload="na"
    ms.date="08/22/2016"
    ms.author="chrande; glenga"/>

# <a name="azure-functions-documentdb-bindings"></a>Azure funktioner DocumentDB bindinger

[AZURE.INCLUDE [functions-selector-bindings](../../includes/functions-selector-bindings.md)]

I denne artikel forklares det, hvordan du konfigurerer og kode Azure DocumentDB bindinger i Azure funktioner. 

[AZURE.INCLUDE [intro](../../includes/functions-bindings-intro.md)] 

## <a id="docdbinput"></a>Azure DocumentDB input binding

Indtast bindinger kan indlæse et dokument fra en DocumentDB af websteder og sende dem direkte til din binding. Dokument-id kan bestemmes ud fra den udløser, aktiveres funktionen. I en C#-funktion sendes eventuelle ændringer, der har foretaget af posten automatisk tilbage til samlingen når funktionen afsluttet korrekt.

#### <a name="functionjson-for-documentdb-input-binding"></a>Function.JSON DocumentDB input indbinding

Filen *function.json* indeholder følgende egenskaber:

- `name`: Variabelnavn anvendes i funktionen kode til dokumentet.
- `type`: skal være indstillet til "documentdb".
- `databaseName`: Den database, der indeholder dokumentet.
- `collectionName`: Den samling, der indeholder dokumentet.
- `id`: Id dokumentet til at hente. Denne egenskab understøtter bindinger svarer til "{queueTrigger}", som anvender strengværdi kø meddelelsens som dokument-id.
- `connection`: Denne streng skal være en Application Setting indstillet til slutpunkt for kontoen DocumentDB. Hvis du vælger din konto fra fanen integrere, oprettes en ny App-indstilling for dig, med et navn, der tager formularen yourAccount_DOCUMENTDB. Hvis du vil oprette manuelt indstillingen App, skal den faktiske forbindelsesstreng skal have følgende form, AccountEndpoint =<Endpoint for your account>; AccountKey =<Your primary access key>;.
- ' retning: skal være indstillet til *"i"*.

Eksempel på *function.json*:
 
    {
      "bindings": [
        {
          "name": "document",
          "type": "documentdb",
          "databaseName": "MyDatabase",
          "collectionName": "MyCollection",
          "id" : "{queueTrigger}",
          "connection": "MyAccount_DOCUMENTDB",     
          "direction": "in"
        }
      ],
      "disabled": false
    }

#### <a name="azure-documentdb-input-code-example-for-a-c-queue-trigger"></a>Eksempel på Azure DocumentDB input kode for en C# kø udløser
 
Ved hjælp af den ovenstående eksempel function.json vil DocumentDB input binding hente dokumentet med det id, der svarer til strengen kø meddelelse og sende dem til parameteren 'dokument'. Hvis dokumentet ikke findes, er parameteren 'dokument' null. Dokumentet opdateres derefter med den nye tekstværdi, når funktionen afsluttet.
 
    public static void Run(string myQueueItem, dynamic document)
    {   
        document.text = "This has changed.";
    }

#### <a name="azure-documentdb-input-code-example-for-an-f-queue-trigger"></a>Eksempel på Azure DocumentDB input kode for en F # kø udløser

Ved hjælp af den ovenstående eksempel function.json vil DocumentDB input binding hente dokumentet med det id, der svarer til strengen kø meddelelse og sende dem til parameteren 'dokument'. Hvis dokumentet ikke findes, er parameteren 'dokument' null. Dokumentet opdateres derefter med den nye tekstværdi, når funktionen afsluttet.

    open FSharp.Interop.Dynamic
    let Run(myQueueItem: string, document: obj) =
        document?text <- "This has changed."

Du skal bruge en `project.json` fil, der bruger NuGet til at angive den `FSharp.Interop.Dynamic` og `Dynamitey` pakker som pakkeafhængigheder, sådan ud:

    {
      "frameworks": {
        "net46": {
          "dependencies": {
            "Dynamitey": "1.0.2",
            "FSharp.Interop.Dynamic": "3.0.0"
          }
        }
      }
    }

Dette vil bruge NuGet til at hente dine afhængigheder og henvise dem i dit script.

#### <a name="azure-documentdb-input-code-example-for-a-nodejs-queue-trigger"></a>Eksempel på Azure DocumentDB input kode for en Node.js kø udløser
 
Ved hjælp af den ovenstående eksempel function.json DocumentDB input binding hentes dokumentet med det id, der svarer til kø meddelelse strengen og Overfør det til den `documentIn` binding egenskab. I Node.js funktioner, der opdaterede dokumenter ikke sendes tilbage til samlingen. Dog kan du overføre input binding direkte til en DocumentDB output binding navngivne `documentOut` til at understøtte opdateringer. Eksempel på denne kode opdaterer egenskaben text af input dokumentet og indstiller dem som output dokumentet.
 
    module.exports = function (context, input) {   
        context.bindings.documentOut = context.bindings.documentIn;
        context.bindings.documentOut.text = "This was updated!";
        context.done();
    };

## <a id="docdboutput"></a>Azure DocumentDB output bindinger

Din funktioner kan skrive JSON dokumenter til en Azure DocumentDB-database ved hjælp af **Azure DocumentDB dokument** output binding. Yderligere oplysninger om Azure DocumentDB Gennemse [Introduktion til DocumentDB](../documentdb/documentdb-introduction.md) og [Introduktion – selvstudium](../documentdb/documentdb-get-started.md).

#### <a name="functionjson-for-documentdb-output-binding"></a>Function.JSON for DocumentDB output binding

Filen function.json indeholder følgende egenskaber:

- `name`: Variable navn, der bruges i funktionen kode for det nye dokument.
- `type`: skal være indstillet til *"documentdb"*.
- `databaseName`: Den database, der indeholder den samling, hvor det nye dokument oprettes.
- `collectionName`: Den samling, hvor det nye dokument oprettes.
- `createIfNotExists`: Dette er en boolesk værdi til at angive, om samlingen oprettes, hvis den ikke findes. Standard er *Falsk*. Årsagen til dette er nye af websteder er oprettet med reserveret overførselshastighed, som har priser konsekvenserne. Få mere at vide, skal du gå til den [priser side](https://azure.microsoft.com/pricing/details/documentdb/).
- `connection`: Denne streng skal være en **Application Setting** indstillet til slutpunkt for kontoen DocumentDB. Hvis du vælger din konto fra fanen **integrere** , der oprettes en ny App-indstilling for dig med et navn, der tager formularen følgende `yourAccount_DOCUMENTDB`. Hvis du vil oprette manuelt indstillingen App, skal den faktiske forbindelsesstreng skal have følgende form, `AccountEndpoint=<Endpoint for your account>;AccountKey=<Your primary access key>;`. 
- `direction`: skal være indstillet til *"ud"*. 
 
Eksempel på function.json:

    {
      "bindings": [
        {
          "name": "document",
          "type": "documentdb",
          "databaseName": "MyDatabase",
          "collectionName": "MyCollection",
          "createIfNotExists": false,
          "connection": "MyAccount_DOCUMENTDB",
          "direction": "out"
        }
      ],
      "disabled": false
    }


#### <a name="azure-documentdb-output-code-example-for-a-nodejs-queue-trigger"></a>Azure DocumentDB output kodeeksempel for en Node.js kø udløser

    module.exports = function (context, input) {
       
        context.bindings.document = {
            text : "I'm running in a Node function! Data: '" + input + "'"
        }   
     
        context.done();
    };

Det nye dokument:

    {
      "text": "I'm running in a Node function! Data: 'example queue data'",
      "id": "01a817fe-f582-4839-b30c-fb32574ff13f"
    }
 

#### <a name="azure-documentdb-output-code-example-for-an-f-queue-trigger"></a>Azure DocumentDB output kodeeksempel for en F # kø udløser

    open FSharp.Interop.Dynamic
    let Run(myQueueItem: string, document: obj) =
        document?text <- (sprintf "I'm running in an F# function! %s" myQueueItem)

#### <a name="azure-documentdb-output-code-example-for-a-c-queue-trigger"></a>Azure DocumentDB output kodeeksempel for en C# kø udløser


    using System;

    public static void Run(string myQueueItem, out object document, TraceWriter log)
    {
        log.Info($"C# Queue trigger function processed: {myQueueItem}");
       
        document = new {
            text = $"I'm running in a C# function! {myQueueItem}"
        };
    }


#### <a name="azure-documentdb-output-code-example-setting-file-name"></a>Azure DocumentDB output kode eksempel indstilling filnavn

Hvis du vil angive navnet på dokumentet i funktionen, skal du blot indstille den `id` værdi.  Eksempelvis hvis JSON indhold til en medarbejder blev blive ignoreret i køen ligner følgende:

    {
      "name" : "John Henry",
      "employeeId" : "123456",
      "address" : "A town nearby"
    }

Du kan bruge den følgende C#-kode i en kø udløser funktion: 
    
    #r "Newtonsoft.Json"
    
    using System;
    using Newtonsoft.Json;
    using Newtonsoft.Json.Linq;
    
    public static void Run(string myQueueItem, out object employeeDocument, TraceWriter log)
    {
        log.Info($"C# Queue trigger function processed: {myQueueItem}");
        
        dynamic employee = JObject.Parse(myQueueItem);
        
        employeeDocument = new {
            id = employee.name + "-" + employee.employeeId,
            name = employee.name,
            employeeId = employee.employeeId,
            address = employee.address
        };
    }

Eller det tilsvarende F #-kode:

    open FSharp.Interop.Dynamic
    open Newtonsoft.Json

    type Employee = {
        id: string
        name: string
        employeeId: string
        address: string
    }

    let Run(myQueueItem: string, employeeDocument: byref<obj>, log: TraceWriter) =
        log.Info(sprintf "F# Queue trigger function processed: %s" myQueueItem)
        let employee = JObject.Parse(myQueueItem)
        employeeDocument <-
            { id = sprintf "%s-%s" employee?name employee?employeeId
              name = employee?name
              employeeId = employee?id
              address = employee?address }

Eksempel på output:

    {
      "id": "John Henry-123456",
      "name": "John Henry",
      "employeeId": "123456",
      "address": "A town nearby"
    }

## <a name="next-steps"></a>Næste trin

[AZURE.INCLUDE [next steps](../../includes/functions-bindings-next-steps.md)]
