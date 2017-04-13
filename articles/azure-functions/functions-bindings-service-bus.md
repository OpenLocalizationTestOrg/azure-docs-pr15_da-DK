<properties
    pageTitle="Azure funktioner Service Bus udløsere og bindinger | Microsoft Azure"
    description="Forstå, hvordan du bruger Azure Service Bus udløsere og bindinger i Azure funktioner."
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

# <a name="azure-functions-service-bus-triggers-and-bindings-for-queues-and-topics"></a>Azure funktioner Service Bus udløsere og bindinger for køer og emner

[AZURE.INCLUDE [functions-selector-bindings](../../includes/functions-selector-bindings.md)]

I denne artikel forklares det, hvordan du konfigurerer og kode Azure Service Bus udløsere og bindinger i Azure funktioner. 

[AZURE.INCLUDE [intro](../../includes/functions-bindings-intro.md)] 

## <a id="sbtrigger"></a>Service Bus kø eller emne udløser

#### <a name="functionjson"></a>Function.JSON

Filen *function.json* angiver følgende egenskaber.

- `name`: Det variable navn, der bruges i funktionen kode for køen eller emne eller kø eller emne i meddelelsen. 
- `queueName`: For kø udløse kun navnet på den kø til afstemning.
- `topicName`: For emne udløse kun navnet på emnet til afstemning.
- `subscriptionName`: For emne udløse kun på abonnementets navn.
- `connection`: Navnet på en app-indstilling, der indeholder en Service Bus forbindelsesstreng. Forbindelsesstrengen skal være for et Service Bus navneområde, ikke begrænset til en bestemt kø eller emne. Hvis forbindelsesstrengen ikke har administrere rettigheder, skal du angive den `accessRights` egenskab. Hvis du lader `connection` tomme, den udløser eller binding fungerer sammen med standard Service Bus forbindelsesstrengen til den funktion, som er angivet af indstillingen AzureWebJobsServiceBus app.
- `accessRights`: Angiver de tilgængelige for forbindelsesstrengen adgangsrettigheder. Standardværdien er `manage`. Angiv til `listen` Hvis du bruger en forbindelsesstreng, der indeholder ikke administrere tilladelser. Ellers administrere de funktioner, kan du prøve runtime og fail til at udføre handlinger, der kræver rettigheder.
- `type`: Skal være indstillet til *serviceBusTrigger*.
- `direction`: Skal være indstillet til *i*. 

Eksempel på *function.json* for en Service Bus kø udløser:

```json
{
  "bindings": [
    {
      "queueName": "testqueue",
      "connection": "MyServiceBusConnection",
      "name": "myQueueItem",
      "type": "serviceBusTrigger",
      "direction": "in"
    }
  ],
  "disabled": false
}
```

#### <a name="c-code-example-that-processes-a-service-bus-queue-message"></a>C#-kodeeksempel, der behandler meddelelsen Service Bus kø

```csharp
public static void Run(string myQueueItem, TraceWriter log)
{
    log.Info($"C# ServiceBus queue trigger function processed message: {myQueueItem}");
}
```

#### <a name="f-code-example-that-processes-a-service-bus-queue-message"></a>F #-kodeeksempel, der behandler meddelelsen Service Bus kø

```fsharp
let Run(myQueueItem: string, log: TraceWriter) =
    log.Info(sprintf "F# ServiceBus queue trigger function processed message: %s" myQueueItem)
```

#### <a name="nodejs-code-example-that-processes-a-service-bus-queue-message"></a>Eksempel på node.js kode, der behandler meddelelsen Service Bus kø

```javascript
module.exports = function(context, myQueueItem) {
    context.log('Node.js ServiceBus queue trigger function processed message', myQueueItem);
    context.done();
};
```

#### <a name="supported-types"></a>Understøttede datatyper

Service Bus kø meddelelsen kan deserialiseres til en af følgende typer:

* Objekt (fra JSON)
* streng
* bytematrix 
* `BrokeredMessage`(C#) 

#### <a id="sbpeeklock"></a>PeekLock funktionsmåde

Funktioner runtime modtager en meddelelse i `PeekLock` tilstand og opkald `Complete` på den meddelelse, hvis funktionen afsluttes korrekt, eller opkald `Abandon` Hvis funktionen mislykkes. Hvis funktionen kører længere end den `PeekLock` timeout, låsen automatisk fornys.

#### <a id="sbpoison"></a>Behandling af meddelelser

Service Bus understøtter sin egen behandling af meddelelser håndtering af som ikke kan styres eller konfigureret i Azure funktioner konfiguration eller kode. 

#### <a id="sbsinglethread"></a>Fører en enkelt

Som standard funktionerne behandler runtime flere beskeder i kø samtidigt. For at dirigere kørslen om at behandle en enkelt kø eller emne meddelelse på et tidspunkt, Angiv `serviceBus.maxConcurrrentCalls` til 1 i filen *host.json* . Finde oplysninger om filen *host.json* , [Mappestrukturen](functions-reference.md#folder-structure) i udvikler referenceartikel og [host.json](https://github.com/Azure/azure-webjobs-sdk-script/wiki/host.json) i WebJobs.Script lager wiki.

## <a id="sboutput"></a>Service Bus kø eller emne output binding

#### <a name="functionjson"></a>Function.JSON

Filen *function.json* angiver følgende egenskaber.

- `name`: Variabelnavn, der anvendes i funktionen kode til kø eller kø meddelelse. 
- `queueName`: For kø udløse kun navnet på den kø til afstemning.
- `topicName`: For emne udløse kun navnet på emnet til afstemning.
- `subscriptionName`: For emne udløse kun på abonnementets navn.
- `connection`: Samme som for Service Bus udløse.
- `accessRights`: Angiver de tilgængelige for forbindelsesstrengen adgangsrettigheder. Standardværdien er `manage`. Angiv til `send` Hvis du bruger en forbindelsesstreng, der indeholder ikke administrere tilladelser. På anden måde håndtere de funktioner, kan du prøve runtime og fail til at udføre handlinger, der kræver rettigheder, som oprettelse af køer.
- `type`: Skal være indstillet til *serviceBus*.
- `direction`: Skal være indstillet til *ud*. 

Eksempel *function.json* til brug af en timer udløser til at skrive Service Bus kø meddelelser:

```JSON
{
  "bindings": [
    {
      "schedule": "0/15 * * * * *",
      "name": "myTimer",
      "runsOnStartup": true,
      "type": "timerTrigger",
      "direction": "in"
    },
    {
      "name": "outputSbQueue",
      "type": "serviceBus",
      "queueName": "testqueue",
      "connection": "MyServiceBusConnection",
      "direction": "out"
    }
  ],
  "disabled": false
}
``` 

#### <a name="supported-types"></a>Understøttede datatyper

Azure-funktioner kan oprette en Service Bus kø meddelelse fra enhver af følgende typer.

* Objekt (altid opretter en meddelelse, JSON, opretter meddelelsen med et null-objekt, hvis værdien er null, når funktionen stopper)
* streng (opretter en meddelelse, hvis værdien er ikke-null, når funktionen stopper)
* bytematrix (fungerer som streng) 
* `BrokeredMessage`(C#, fungerer som streng)

For at oprette flere meddelelser i en C#-funktion, du kan bruge `ICollector<T>` eller `IAsyncCollector<T>`. Der oprettes en meddelelse, når du ringer til den `Add` metode.

#### <a name="c-code-examples-that-create-service-bus-queue-messages"></a>C# kodeeksempler, der opretter en Service Bus kø meddelelse

```csharp
public static void Run(TimerInfo myTimer, TraceWriter log, out string outputSbQueue)
{
    string message = $"Service Bus queue message created at: {DateTime.Now}";
    log.Info(message); 
    outputSbQueue = message;
}
```

```csharp
public static void Run(TimerInfo myTimer, TraceWriter log, ICollector<string> outputSbQueue)
{
    string message = $"Service Bus queue message created at: {DateTime.Now}";
    log.Info(message); 
    outputSbQueue.Add("1 " + message);
    outputSbQueue.Add("2 " + message);
}
```

#### <a name="f-code-example-that-creates-a-service-bus-queue-message"></a>F #-kodeeksempel, der opretter en Service Bus kø meddelelse

```fsharp
let Run(myTimer: TimerInfo, log: TraceWriter, outputSbQueue: byref<string>) =
    let message = sprintf "Service Bus queue message created at: %s" (DateTime.Now.ToString())
    log.Info(message)
    outputSbQueue = message
```

#### <a name="nodejs-code-example-that-creates-a-service-bus-queue-message"></a>Eksempel på node.js kode, der opretter en Service Bus kø meddelelse

```javascript
module.exports = function (context, myTimer) {
    var timeStamp = new Date().toISOString();
    
    if(myTimer.isPastDue)
    {
        context.log('Node.js is running late!');
    }
    var message = 'Service Bus queue message created at ' + timeStamp;
    context.log(message);   
    context.bindings.outputSbQueueMsg = message;
    context.done();
};
```

## <a name="next-steps"></a>Næste trin

[AZURE.INCLUDE [next steps](../../includes/functions-bindings-next-steps.md)] 
