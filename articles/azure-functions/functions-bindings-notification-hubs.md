<properties
    pageTitle="Azure funktioner meddelelse Hub binding | Microsoft Azure"
    description="Forstå, hvordan du bruger Azure meddelelse Hub binding i Azure funktioner."
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
    ms.date="10/27/2016"
    ms.author="wesmc"/>

# <a name="azure-functions-notification-hub-output-binding"></a>Azure funktioner meddelelse Hub output binding

[AZURE.INCLUDE [functions-selector-bindings](../../includes/functions-selector-bindings.md)]

I denne artikel forklares det, hvordan du konfigurerer og kode Azure meddelelse Hub bindinger i Azure funktioner. 

[AZURE.INCLUDE [intro](../../includes/functions-bindings-intro.md)] 

Din funktioner kan sende pushmeddelelser ved hjælp af en konfigureret Azure meddelelse Hub med et par kodelinjer. Azure meddelelse hubben skal dog være konfigureret til Platform beskeder tjenester (PNS) du vil bruge. Se [Kom i gang med besked om Hubs](../notification-hubs/notification-hubs-windows-store-dotnet-get-started-wns-push-notification.md) kan finde flere oplysninger om konfiguration af en Azure meddelelse Hub og udvikling af en klientprogrammer, registrerer til at modtage meddelelser og klikke på din target klient platform øverst.

I meddelelser, du sender kan være oprindelige meddelelser eller skabelon beskeder. Oprindelige meddelelser målrette en bestemt meddelelse platform, som er konfigureret i den `platform` egenskab for outputbinding. En meddelelse om skabelon kan bruges til at målrette flere platforme.   

## <a name="notification-hub-output-binding-properties"></a>Meddelelse om hub output binding egenskaber

Filen function.json indeholder følgende egenskaber:

- `name`: Variabelnavn anvendes i funktionen kode til hub meddelelsen om opgaven.
- `type`: skal være indstillet til *"notificationHub"*.
- `tagExpression`: Mærke udtryk giver dig mulighed at angive, at meddelelser leveres til et antal enheder, der har registreret til at modtage meddelelser, der svarer til udtrykket mærke.  Du kan finde yderligere oplysninger finder [Routing og mærke udtryk](../notification-hubs/notification-hubs-tags-segment-push-message.md).
- `hubName`: Navnet på den meddelelse hub ressource i portalen Azure.
- `connection`: Denne forbindelsesstreng skal være en **Application Setting** forbindelsesstreng, der er angivet til *DefaultFullSharedAccessSignature* værdien for din meddelelse hub.
- `direction`: skal være indstillet til *"ud"*. 
- `platform`: Egenskaben platform angiver den meddelelse platform din meddelelse destinationer. Skal være en af følgende værdier:
    - `template`: Dette er standard platformen, hvis egenskaben platform udelades fra outputbinding. Skabelon meddelelser kan bruges til at målrette alle platforme, der er konfigureret på Azure meddelelse hubben. Du kan finde flere oplysninger om brug af skabeloner generelt at sende flere platforme meddelelser med en Azure meddelelse Hub, [skabeloner](../notification-hubs/notification-hubs-templates-cross-platform-push-messages.md).
    - `apns`: Apple-Pushmeddelelsestjeneste. Du kan finde flere oplysninger om konfiguration af meddelelse-hub til APNS og modtager meddelelsen i et klient-app, se [sender pushmeddelelser til iOS med Azure meddelelse Hubs](../notification-hubs/notification-hubs-ios-apple-push-notification-apns-get-started.md) 
    - `adm`: [Amazon enhed Messaging](https://developer.amazon.com/device-messaging). Du kan finde flere oplysninger om konfiguration af meddelelse-hub til ADM og modtager meddelelsen i et Kindle app, se [Introduktion til meddelelse Hubs til Kindle apps](../notification-hubs/notification-hubs-kindle-amazon-adm-push-notification.md) 
    - `gcm`: [Google Cloud-beskeder](https://developers.google.com/cloud-messaging/). Firebase skyen Messaging, som er den nye version af GCM, understøttes også. Du kan finde flere oplysninger om konfiguration af meddelelse-hub til GCM/FCM og modtage meddelelsen i en Android-klient-app, se [sender pushmeddelelser til Android med Azure meddelelse Hubs](../notification-hubs/notification-hubs-android-push-notification-google-fcm-get-started.md)
    - `wns`: [Windows Push meddelelse Services](https://msdn.microsoft.com/en-us/windows/uwp/controls-and-patterns/tiles-and-notifications-windows-push-notification-services--wns--overview) målretning af Windows-platforme. Windows Phone 8.1 eller nyere er også understøttet af WNS. Du kan finde flere oplysninger om konfiguration af meddelelse-hub til WNS og modtager meddelelsen i et Universal Windows Platform (UWP)-app i [Introduktion til meddelelse Hubs for Windows Universal Platform Apps](../notification-hubs/notification-hubs-windows-store-dotnet-get-started-wns-push-notification.md)
    - `mpns`: [Microsoft-Pushmeddelelsestjeneste](https://msdn.microsoft.com/en-us/library/windows/apps/ff402558.aspx). Denne platform understøtter Windows Phone 8 og tidligere Windows Phone-platforme. Finde flere oplysninger om konfiguration af meddelelse-hub til MPNS og modtager meddelelsen i en Windows Phone app [sender pushmeddelelser med Azure meddelelse Hubs på Windows Phone](../notification-hubs/notification-hubs-windows-mobile-push-notifications-mpns.md)
 
Eksempel på function.json:

    {
      "bindings": [
        {
          "name": "notification",
          "type": "notificationHub",
          "tagExpression": "",
          "hubName": "my-notification-hub",
          "connection": "MyHubConnectionString",
          "platform": "gcm",
          "direction": "out"
        }
      ],
      "disabled": false
    }

## <a name="notification-hub-connection-string-setup"></a>Meddelelse om hub forbindelse streng konfiguration

Hvis du vil bruge en meddelelse om hub output, indbinding, skal du konfigurere forbindelsesstrengen for hubben. Dette kan gøres på fanen *integrere* ved at vælge din meddelelse hub eller oprette en ny. 

Du kan tilføje en forbindelsesstreng for en eksisterende hub også manuelt ved at tilføje en forbindelsesstreng for *DefaultFullSharedAccessSignature* til din meddelelse-hub. Forbindelsesstrengen indeholder din funktionen access tilladelse til at sende meddelelser. *DefaultFullSharedAccessSignature* forbindelse strengværdi kan åbnes fra knappen **taster** i den primære blade af din meddelelse hub ressource i portalen Azure. Du kan manuelt tilføje en forbindelsesstreng for din hub, skal du følge nedenstående trin: 

1. Klik på bladet **funktionen app** af portalen Azure **funktionen App Indstillinger > Gå til App Tjenesteindstillinger**.

2. Klik på **Indstillinger for et webprogram**i bladet **Indstillinger** .

3. Rul ned til sektionen **strenge** , og Tilføj et navngivet element for *DefaultFullSharedAccessSignature* værdi for din meddelelse hub. Ændre det til **brugerdefineret**.
4. Henvise til dine forbindelsesnavn streng i output bindinger. Svarer til **MyHubConnectionString** bruges i eksemplet ovenfor.

## <a name="native-notification-examples"></a>Oprindelig meddelelse eksempler

#### <a name="apns-native-notifications-with-c-queue-triggers"></a>APNS oprindelige meddelelser med C#-udløsere

I dette eksempel viser, hvordan bruge typer, der er defineret i [Microsoft Azure meddelelse Hubs bibliotek](https://www.nuget.org/packages/Microsoft.Azure.NotificationHubs/) til at sende en oprindelig APNS meddelelse. 

    #r "Microsoft.Azure.NotificationHubs"
    #r "Newtonsoft.Json"
    
    using System;
    using Microsoft.Azure.NotificationHubs;
    using Newtonsoft.Json;
    
    public static async Task Run(string myQueueItem, IAsyncCollector<Notification> notification, TraceWriter log)
    {
        log.Info($"C# Queue trigger function processed: {myQueueItem}");
    
        // In this example the queue item is a new user to be processed in the form of a JSON string with 
        // a "name" value.
        //
        // The JSON format for a native APNS notification is ...
        // { "aps": { "alert": "notification message" }}  

        log.Info($"Sending APNS notification of a new user");   
        dynamic user = JsonConvert.DeserializeObject(myQueueItem);  
        string apnsNotificationPayload = "{\"aps\": {\"alert\": \"A new user wants to be added (" + 
                                            user.name + ")\" }}";
        log.Info($"{apnsNotificationPayload}");
        await notification.AddAsync(new AppleNotification(apnsNotificationPayload));        
    }

#### <a name="gcm-native-notifications-with-c-queue-triggers"></a>GCM oprindelige meddelelser med C#-udløsere

I dette eksempel viser, hvordan bruge typer, der er defineret i [Microsoft Azure meddelelse Hubs bibliotek](https://www.nuget.org/packages/Microsoft.Azure.NotificationHubs/) til at sende en oprindelig GCM meddelelse. 

    #r "Microsoft.Azure.NotificationHubs"
    #r "Newtonsoft.Json"
    
    using System;
    using Microsoft.Azure.NotificationHubs;
    using Newtonsoft.Json;
    
    public static async Task Run(string myQueueItem, IAsyncCollector<Notification> notification, TraceWriter log)
    {
        log.Info($"C# Queue trigger function processed: {myQueueItem}");
    
        // In this example the queue item is a new user to be processed in the form of a JSON string with 
        // a "name" value.
        //
        // The JSON format for a native GCM notification is ...
        // { "data": { "message": "notification message" }}  

        log.Info($"Sending GCM notification of a new user");    
        dynamic user = JsonConvert.DeserializeObject(myQueueItem);  
        string gcmNotificationPayload = "{\"data\": {\"message\": \"A new user wants to be added (" + 
                                            user.name + ")\" }}";
        log.Info($"{gcmNotificationPayload}");
        await notification.AddAsync(new GcmNotification(gcmNotificationPayload));       
    }

#### <a name="wns-native-notifications-with-c-queue-triggers"></a>WNS oprindelige meddelelser med C#-udløsere

I dette eksempel vises, hvordan du bruger datatyper, der er defineret i [Microsoft Azure meddelelse Hubs bibliotek](https://www.nuget.org/packages/Microsoft.Azure.NotificationHubs/) til at sende en oprindelig WNS toastbeskeden. 

    #r "Microsoft.Azure.NotificationHubs"
    #r "Newtonsoft.Json"
    
    using System;
    using Microsoft.Azure.NotificationHubs;
    using Newtonsoft.Json;
    
    public static async Task Run(string myQueueItem, IAsyncCollector<Notification> notification, TraceWriter log)
    {
        log.Info($"C# Queue trigger function processed: {myQueueItem}");
    
        // In this example the queue item is a new user to be processed in the form of a JSON string with 
        // a "name" value.
        //
        // The XML format for a native WNS toast notification is ...
        // <?xml version="1.0" encoding="utf-8"?>
        // <toast>
        //   <visual>
        //     <binding template="ToastText01">
        //       <text id="1">notification message</text>
        //     </binding>
        //   </visual>
        // </toast>

        log.Info($"Sending WNS toast notification of a new user");  
        dynamic user = JsonConvert.DeserializeObject(myQueueItem);  
        string wnsNotificationPayload = "<?xml version=\"1.0\" encoding=\"utf-8\"?>" +
                                        "<toast><visual><binding template=\"ToastText01\">" +
                                            "<text id=\"1\">" + 
                                                "A new user wants to be added (" + user.name + ")" + 
                                            "</text>" +
                                        "</binding></visual></toast>";

        log.Info($"{wnsNotificationPayload}");
        await notification.AddAsync(new WindowsNotification(wnsNotificationPayload));       
    }


## <a name="template-notification-examples"></a>Skabelon meddelelse eksempler

#### <a name="template-example-for-nodejs-timer-triggers"></a>Eksempel på skabelon for Node.js timer udløsere 

I dette eksempel sender en besked til en [skabelon registrering](../notification-hubs/notification-hubs-templates-cross-platform-push-messages.md) , der indeholder `location` og `message`.

    module.exports = function (context, myTimer) {
        var timeStamp = new Date().toISOString();
       
        if(myTimer.isPastDue)
        {
            context.log('Node.js is running late!');
        }
        context.log('Node.js timer trigger function ran!', timeStamp);  
        context.bindings.notification = {
            location: "Redmond",
            message: "Hello from Node!"
        };
        context.done();
    };

#### <a name="template-example-for-f-timer-triggers"></a>Eksempel på skabelon for F # timer udløsere

I dette eksempel sender en besked til en [skabelon registrering](../notification-hubs/notification-hubs-templates-cross-platform-push-messages.md) , der indeholder `location` og `message`.

    let Run(myTimer: TimerInfo, notification: byref<IDictionary<string, string>>) =
        notification = dict [("location", "Redmond"); ("message", "Hello from F#!")]


#### <a name="template-example-using-an-out-parameter"></a>Eksempel på skabelon ved hjælp af en ud-parameter 

I dette eksempel sender en besked til en [skabelon registrering](../notification-hubs/notification-hubs-templates-cross-platform-push-messages.md) , der indeholder en `message` pladsholder i skabelonen.

    using System;
    using System.Threading.Tasks;
    using System.Collections.Generic;
     
    public static void Run(string myQueueItem,  out IDictionary<string, string> notification, TraceWriter log)
    {
        log.Info($"C# Queue trigger function processed: {myQueueItem}");
        notification = GetTemplateProperties(myQueueItem);
    }
     
    private static IDictionary<string, string> GetTemplateProperties(string message)
    {
        Dictionary<string, string> templateProperties = new Dictionary<string, string>();
        templateProperties["message"] = message;
        return templateProperties;
    }

#### <a name="template-example-with-asynchronous-function"></a>Eksempel på skabelon med asynkron funktionen

Hvis du bruger asynkron kode, er ud-parametre ikke tilladt. I dette tilfælde bruger `IAsyncCollector` til at returnere din skabelon for. Følgende kode er et asynkron eksempel af ovenstående kode. 

    using System;
    using System.Threading.Tasks;
    using System.Collections.Generic;
    
    public static async Task Run(string myQueueItem, IAsyncCollector<IDictionary<string,string>> notification, TraceWriter log)
    {
        log.Info($"C# Queue trigger function processed: {myQueueItem}");
    
        log.Info($"Sending Template Notification to Notification Hub");
        await notification.AddAsync(GetTemplateProperties(myQueueItem));    
    }
    
    private static IDictionary<string, string> GetTemplateProperties(string message)
    {
        Dictionary<string, string> templateProperties = new Dictionary<string, string>();
        templateProperties["user"] = "A new user wants to be added : " + message;
        return templateProperties;
    }

#### <a name="template-example-using-json"></a>Eksempel på skabelon brug af JSON 

I dette eksempel sender en besked til en [skabelon registrering](../notification-hubs/notification-hubs-templates-cross-platform-push-messages.md) , der indeholder en `message` pladsholder i skabelonen ved hjælp af en gyldig JSON-streng.

    using System;
     
    public static void Run(string myQueueItem,  out string notification, TraceWriter log)
    {
        log.Info($"C# Queue trigger function processed: {myQueueItem}");
        notification = "{\"message\":\"Hello from C#. Processed a queue item!\"}";
    }


#### <a name="template-example-using-notification-hubs-library-types"></a>Eksempel på skabelon brug af besked om Hubs bibliotek typer

I dette eksempel vises, hvordan du bruger datatyper, der er defineret i [Microsoft Azure meddelelse Hubs bibliotek](https://www.nuget.org/packages/Microsoft.Azure.NotificationHubs/). 


    #r "Microsoft.Azure.NotificationHubs"

    using System;
    using System.Threading.Tasks;
    using Microsoft.Azure.NotificationHubs;
     
    public static void Run(string myQueueItem,  out Notification notification, TraceWriter log)
    {
       log.Info($"C# Queue trigger function processed: {myQueueItem}");
       notification = GetTemplateNotification(myQueueItem);
    }

    private static TemplateNotification GetTemplateNotification(string message)
    {
        Dictionary<string, string> templateProperties = new Dictionary<string, string>();
        templateProperties["message"] = message;
        return new TemplateNotification(templateProperties);
    }

## <a name="next-steps"></a>Næste trin

[AZURE.INCLUDE [next steps](../../includes/functions-bindings-next-steps.md)]
