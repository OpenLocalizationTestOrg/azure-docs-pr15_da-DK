<properties
    pageTitle="Azure funktioner begivenhed Hub bindinger | Microsoft Azure"
    description="Forstå, hvordan du bruger Azure begivenhed Hub bindinger i Azure funktioner."
    services="functions"
    documentationCenter="na"
    authors="wesmc7777"
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
    ms.date="10/17/2016"
    ms.author="wesmc"/>

# <a name="azure-functions-event-hub-bindings"></a>Azure funktioner begivenhed Hub bindinger

[AZURE.INCLUDE [functions-selector-bindings](../../includes/functions-selector-bindings.md)]

I denne artikel beskrives det, hvordan du konfigurerer og kode [Azure begivenhed Hub](../event-hubs/event-hubs-overview.md) bindinger til Azure-funktioner. Azure funktioner understøtter udløser og output bindinger for Azure begivenhed Hubs.

[AZURE.INCLUDE [intro](../../includes/functions-bindings-intro.md)] 

Hvis du er ny bruger af Azure begivenhed Hubs, kan du få vist [Azure begivenhed Hub oversigt](../event-hubs/event-hubs-overview.md).

## <a name="azure-event-hub-trigger-binding"></a>Azure begivenhed Hub udløse binding

Udløse en Azure begivenhed Hub kan bruges til at svare på en begivenhed, der er sendt til en begivenhed hub begivenhed stream. Du skal have læseadgang til begivenhed-hub til at konfigurere en udløser binding.

#### <a name="functionjson-for-event-hub-trigger-binding"></a>Function.JSON til begivenhed Hub udløse binding

Filen *function.json* for en Azure begivenhed Hub udløser angiver følgende egenskaber:

- `type`: Skal være indstillet til *eventHubTrigger*.
- `name`: Det variable navn, der bruges i funktionen kode for begivenhed hub meddelelsen. 
- `direction`: Skal være indstillet til *i*. 
- `path`: Navnet på begivenhed hub.
- `consumerGroup`: Dette er en valgfri egenskab, der bruges til at angive [consumer gruppe](../event-hubs-overview.md#consumer-groups) , der bruges til at abonnere på begivenheder i hubben. Hvis den udelades, den `$Default` consumer gruppe bruges. 
- `connection`: Navnet på en app-indstilling, der indeholder forbindelsesstrengen til det navneområde, begivenhed hub er placeret i. Kopiere forbindelsesstrengen ved at klikke på knappen **Forbindelsesoplysninger** for navneområdet, ikke selve hubben begivenhed.  Forbindelsesstrengen skal have mindst læsetilladelser at aktivere udløseren.

        {
          "bindings": [
            {
              "type": "eventHubTrigger",
              "name": "myEventHubMessage",
              "direction": "in",
              "path": "MyEventHub",
              "connection": "myEventHubReadConnectionString"
            }
          ],
          "disabled": false
        }

#### <a name="azure-event-hub-trigger-c-example"></a>Azure begivenhed Hub udløser C#-eksempel
 
Ved hjælp af den ovenstående eksempel function.json bliver begivenhed meddelelsens brødtekst logget på med den C# funktionen kode nedenfor:
 
    using System;
    
    public static void Run(string myEventHubMessage, TraceWriter log)
    {
        log.Info($"C# Event Hub trigger function processed a message: {myEventHubMessage}");
    }

#### <a name="azure-event-hub-trigger-f-example"></a>Azure begivenhed Hub udløser F # eksempel

Ved hjælp af den ovenstående eksempel function.json bliver begivenhed meddelelsens brødtekst logget på med den F # funktion kode nedenfor:

    let Run(myEventHubMessage: string, log: TraceWriter) =
        log.Info(sprintf "F# eventhub trigger function processed work item: %s" myEventHubMessage)

#### <a name="azure-event-hub-trigger-nodejs-example"></a>Azure begivenhed Hub udløser Node.js eksempel
 
Ved hjælp af den ovenstående eksempel function.json bliver begivenhed meddelelsens brødtekst logget på med den Node.js funktion kode nedenfor:
 
    module.exports = function (context, myEventHubMessage) {
        context.log('Node.js eventhub trigger function processed work item', myEventHubMessage);    
        context.done();
    };


## <a name="azure-event-hub-output-binding"></a>Azure begivenhed Hub output binding

En Azure begivenhed Hub outputbinding bruges til at skrive begivenheder til en begivenhed hub begivenhed stream. Du skal have send tilladelse til en begivenhed hub til at skrive begivenheder til den. 

#### <a name="functionjson-for-event-hub-output-binding"></a>Function.JSON til begivenhed Hub output binding

Filen *function.json* for en Azure begivenhed Hub output binding angiver følgende egenskaber:

- `type`: Skal være indstillet til *eventHub*.
- `name`: Det variable navn, der bruges i funktionen kode for begivenhed hub meddelelsen. 
- `path`: Navnet på begivenhed hub.
- `connection`: Navnet på en app-indstilling, der indeholder forbindelsesstrengen til det navneområde, begivenhed hub er placeret i. Kopiere forbindelsesstrengen ved at klikke på knappen **Forbindelsesoplysninger** for navneområdet, ikke selve hubben begivenhed.  Forbindelsesstrengen skal have send tilladelser til at sende meddelelsen til begivenhed Hub strømmen.
- `direction`: Skal være indstillet til *ud*. 

        {
          "type": "eventHub",
          "name": "outputEventHubMessage",
          "path": "myeventhub",
          "connection": "MyEventHubSend",
          "direction": "out"
        }


#### <a name="azure-event-hub-c-code-example-for-output-binding"></a>Azure begivenhed Hub C# kodeeksempel output indbinding
 
Følgende C# eksempel funktionen kode demonstrerer, skrive en begivenhed til en begivenhed Hub begivenhed stream. Dette eksempel repræsenterer begivenhed hubben output binding ovenstående anvendt på en C# timer udløser.  
 
    using System;
    
    public static void Run(TimerInfo myTimer, out string outputEventHubMessage, TraceWriter log)
    {
        String msg = $"TimerTriggerCSharp1 executed at: {DateTime.Now}";
    
        log.Verbose(msg);   
        
        outputEventHubMessage = msg;
    }

#### <a name="azure-event-hub-f-code-example-for-output-binding"></a>Azure begivenhed Hub F # kodeeksempel output indbinding

Følgende F # eksempel funktionen kode demonstrerer, skrive en begivenhed til en begivenhed Hub begivenhed stream. Dette eksempel repræsenterer begivenhed hubben output binding ovenstående anvendt på en C# timer udløser.

    let Run(myTimer: TimerInfo, outputEventHubMessage: byref<string>, log: TraceWriter) =
        let msg = sprintf "TimerTriggerFSharp1 executed at: %s" DateTime.Now.ToString()
        log.Verbose(msg);
        outputEventHubMessage <- msg;

#### <a name="azure-event-hub-nodejs-code-example-for-output-binding"></a>Eksempel på Azure begivenhed Hub Node.js kode output indbinding
 
Følgende Node.js eksempel funktionen kode demonstrerer, skrive en begivenhed til en begivenhed Hub begivenhed stream. Dette eksempel repræsenterer begivenhed hubben output binding ovenstående anvendt på en Node.js timer udløser.  
 
    module.exports = function (context, myTimer) {
        var timeStamp = new Date().toISOString();
        
        if(myTimer.isPastDue)
        {
            context.log('TimerTriggerNodeJS1 is running late!');
        }

        context.log('TimerTriggerNodeJS1 function ran!', timeStamp);   
        
        context.bindings.outputEventHubMessage = "TimerTriggerNodeJS1 ran at : " + timeStamp;
    
        context.done();
    };

## <a name="next-steps"></a>Næste trin

[AZURE.INCLUDE [next steps](../../includes/functions-bindings-next-steps.md)]
