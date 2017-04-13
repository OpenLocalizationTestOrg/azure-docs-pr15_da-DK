<properties
    pageTitle="Azure funktioner Twilio binding | Microsoft Azure"
    description="Forstå, hvordan du bruger Twilio bindinger med Azure funktioner."
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
    ms.date="10/20/2016"
    ms.author="wesmc"/>

# <a name="azure-functions-twilio-output-binding"></a>Azure funktioner Twilio output binding

[AZURE.INCLUDE [functions-selector-bindings](../../includes/functions-selector-bindings.md)]

I denne artiklen beskrives, hvordan du konfigurerer og bruger Twilio bindinger med Azure funktioner. 

[AZURE.INCLUDE [intro](../../includes/functions-bindings-intro.md)] 

Azure funktioner understøtter Twilio output bindinger for at aktivere din funktioner til at sende SMS SMS-beskeder med nogle få linjer af kode og en [Twilio](https://www.twilio.com/) -konto. 
 

## <a name="functionjson-for-azure-notification-hub-output-binding"></a>Function.JSON til Azure meddelelse Hub output binding

Filen function.json indeholder følgende egenskaber:

- `name`: Variable navn, der bruges i funktionen kode for SMS Twilio SMS.
- `type`: skal være indstillet til *"twilioSms"*.
- `accountSid`: Denne værdi skal være angivet til navnet på en App-indstilling, der indeholder dine Twilio konto Sid.
- `authToken`: Denne værdi skal være angivet til navnet på en App-indstilling, der indeholder et Twilio godkendelse token.
- `to`: Denne værdi er angivet til det telefonnummer, SMS-tekst, der sendes til.
- `from`: Denne værdi er angivet til det telefonnummer, SMS-tekst, der sendes fra.
- `direction`: skal være indstillet til *"ud"*.
- `body`: Denne værdi kan bruges til at kode hårdt tekstmeddelelsen SMS, hvis du ikke behøver at angive det dynamisk i koden for funktionen. 

 
Eksempel på function.json:

    {
      "type": "twilioSms",
      "name": "message",
      "accountSid": "TwilioAccountSid",
      "authToken": "TwilioAuthToken",
      "to": "+1704XXXXXXX",
      "from": "+1425XXXXXXX",
      "direction": "out",
      "body": "Azure Functions Testing"
    }


## <a name="example-c-queue-trigger-with-twilio-output-binding"></a>Eksempel C# kø udløser med Twilio output binding

#### <a name="synchronous"></a>Synkron

Denne synkron eksempelkode for en Azure-lager kø udløser bruger en ud-parameter til at sende en tekstmeddelelse til en kunde, der placeret ordren.

    #r "Newtonsoft.Json"
    #r "Twilio.Api"

    using System;
    using Newtonsoft.Json;
    using Twilio;

    public static void Run(string myQueueItem, out SMSMessage message,  TraceWriter log)
    {
        log.Info($"C# Queue trigger function processed: {myQueueItem}");
    
        // In this example the queue item is a JSON string representing an order that contains the name of a 
        // customer and a mobile number to send text updates to.
        dynamic order = JsonConvert.DeserializeObject(myQueueItem);
        string msg = "Hello " + order.name + ", thank you for your order.";
    
        // Even if you want to use a hard coded message and number in the binding, you must at least 
        // initialize the SMSMessage variable.
        message = new SMSMessage();

        // A dynamic message can be set instead of the body in the output binding. In this example, we use 
        // the order information to personalize a text message to the mobile number provided for
        // order status updates.
        message.Body = msg;
        message.To = order.mobileNumber;
    }

#### <a name="asynchronous"></a>Asynkron

Denne asynkron eksempelkode for en Azure-lager kø udløser sender en tekstmeddelelse til en kunde, der placeret ordren.

    #r "Newtonsoft.Json"
    #r "Twilio.Api"
     
    using System;
    using Newtonsoft.Json;
    using Twilio;
    
    public static async Task Run(string myQueueItem, IAsyncCollector<SMSMessage> message,  TraceWriter log)
    {
        log.Info($"C# Queue trigger function processed: {myQueueItem}");

        // In this example the queue item is a JSON string representing an order that contains the name of a 
        // customer and a mobile number to send text updates to.
        dynamic order = JsonConvert.DeserializeObject(myQueueItem);
        string msg = "Hello " + order.name + ", thank you for your order.";
    
        // Even if you want to use a hard coded message and number in the binding, you must at least 
        // initialize the SMSMessage variable.
        SMSMessage smsText = new SMSMessage();

        // A dynamic message can be set instead of the body in the output binding. In this example, we use 
        // the order information to personalize a text message to the mobile number provided for
        // order status updates.
        smsText.Body = msg;
        smsText.To = order.mobileNumber;
        
        await message.AddAsync(smsText);
    }


## <a name="example-nodejs-queue-trigger-with-twilio-output-binding"></a>Eksempel Node.js kø udløser med Twilio output binding

I dette eksempel Node.js sender en tekstmeddelelse til en kunde, der placeret ordren.

    module.exports = function (context, myQueueItem) {
        context.log('Node.js queue trigger function processed work item', myQueueItem);
    
        // In this example the queue item is a JSON string representing an order that contains the name of a 
        // customer and a mobile number to send text updates to.
        var msg = "Hello " + myQueueItem.name + ", thank you for your order.";
    
        // Even if you want to use a hard coded message and number in the binding, you must at least 
        // initialize the message binding.
        context.bindings.message = {};
    
        // A dynamic message can be set instead of the body in the output binding. In this example, we use 
        // the order information to personalize a text message to the mobile number provided for
        // order status updates.
        context.bindings.message = {
            body : msg,
            to : myQueueItem.mobileNumber
        };
    
        context.done();
    };

## <a name="next-steps"></a>Næste trin

[AZURE.INCLUDE [next steps](../../includes/functions-bindings-next-steps.md)]
