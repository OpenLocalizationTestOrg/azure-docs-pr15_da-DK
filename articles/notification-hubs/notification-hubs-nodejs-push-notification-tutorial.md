<properties
    pageTitle="Afsendelse af pushmeddelelser med Azure meddelelse Hubs og Node.js"
    description="Lær at bruge meddelelse Hubs til at sende pushmeddelelser fra en Node.js."
    keywords="Push-meddelelse, opslagsnål notifications,node.js opslagsnål ios opslagsnål"
    services="notification-hubs"
    documentationCenter="nodejs"
    authors="ysxu"
    manager="dwrede"
    editor=""/>

<tags
    ms.service="notification-hubs"
    ms.workload="mobile"
    ms.tgt_pltfrm="na"
    ms.devlang="javascript"
    ms.topic="article"
    ms.date="10/25/2016"
    ms.author="yuaxu"/>

# <a name="sending-push-notifications-with-azure-notification-hubs-and-nodejs"></a>Afsendelse af pushmeddelelser med Azure meddelelse Hubs og Node.js
[AZURE.INCLUDE [notification-hubs-backend-how-to-selector](../../includes/notification-hubs-backend-how-to-selector.md)]

##<a name="overview"></a>Oversigt

> [AZURE.IMPORTANT] For at fuldføre dette selvstudium skal skal du have en active Azure-konto. Hvis du ikke har en konto, kan du oprette en gratis prøveversion konto på blot et par minutter. Yderligere oplysninger finder du [Azure gratis prøveversion](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A643EE910&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Fen-us%2Fdocumentation%2Farticles%2Fnotification-hubs-nodejs-how-to-use-notification-hubs).

Denne vejledning viser dig, hvordan til at sende pushmeddelelser ved hjælp af Azure meddelelse Hubs direkte fra en Node.js. 

De scenarier, der er dækket omfatter at sende pushmeddelelser til programmer på følgende platforme:

* Android
* iOS
* Windows Phone
* Universal Windows-Platform 

Flere oplysninger om meddelelse hubs, i afsnittet [Næste trin](#next) .

##<a name="what-are-notification-hubs"></a>Hvad er meddelelse Hubs?

Azure meddelelse Hubs giver en nemt Hvis du vil bruge, flere platforme, SVG infrastruktur for at sende pushmeddelelser til mobilenheder. Finde oplysninger om tjenesten infrastrukturen, på siden [Azure meddelelse Hubs](http://msdn.microsoft.com/library/windowsazure/jj927170.aspx) .

##<a name="create-a-nodejs-application"></a>Oprette et Node.js program

Det første trin i dette selvstudium i at oprette et nyt tomt Node.js program. Flere oplysninger om oprettelse af et Node.js program under [oprette og anvende et Node.js program til Azure-websted][nodejswebsite], [Node.js skytjeneste] [ Node.js Cloud Service] ved hjælp af Windows PowerShell eller [et websted med WebMatrix].

##<a name="configure-your-application-to-use-notification-hubs"></a>Konfigurere dit program du bruger meddelelse Hubs

Hvis du vil bruge Azure meddelelse Hubs, skal du hente og bruge Node.js [azure-pakke](https://www.npmjs.com/package/azure), som indeholder et indbygget sæt hjælper biblioteker, kommunikerer med opslagsnål meddelelse RESTEN tjenester.

### <a name="use-node-package-manager-npm-to-obtain-the-package"></a>Brug Node pakke Manager (NPM) til at hente pakken

1.  Brug en kommandolinjen som **PowerShell** (Windows), **Terminal** (Mac) eller **Bash** (Linux), og Naviger til den mappe, hvor du har oprettet dit tomt program.

2.  Skriv **npm installere azure-sb** i kommandovinduet.

3.  Du kan manuelt køre kommandoen **ls** eller **dir** at kontrollere, at en **node\_moduler** mappe blev oprettet. Find **azure** -pakke, som indeholder de biblioteker, du har brug for at få adgang til hubben meddelelse i denne mappe.

>[AZURE.NOTE] Du kan få mere at vide om at installere NPM på officielle [NPM blog](http://blog.npmjs.org/post/85484771375/how-to-install-npm). 

### <a name="import-the-module"></a>Importere modulet

Bruger en teksteditor, skal du tilføje følgende til toppen af filen **server.js** af programmet:

    var azure = require('azure');

### <a name="setup-an-azure-notification-hub-connection"></a>Konfiguration af forbindelse til Azure meddelelse Hub

Objektet **NotificationHubService** kan du arbejde med besked om hubs. Følgende kode opretter et **NotificationHubService** objekt for nofication hubben med navnet **hubname**. Tilføje den i øverst del af filen **server.js** efter sætningen importere azure modulet:

    var notificationHubService = azure.createNotificationHubService('hubname','connectionstring');

Værdien forbindelse **connectionstring** kan hentes fra [Azure-portalen] ved at benytte følgende fremgangsmåde:

1. Klik på **Gennemse**i den venstre navigationsrude.

2. Vælg **Meddelelse Hubs**, og find hubben, du vil bruge til eksemplet. Du kan referere til [Windows Store Introduktion – selvstudium](notification-hubs-windows-store-dotnet-get-started-wns-push-notification.md) , hvis du har brug for hjælp til at oprette en ny meddelelse-Hub.

3. Vælg **Indstillinger**.

4. Klik på **Access politikker**. Du får vist strenge begge delte og fuld adgang.

![Azure Portal - besked om Hubs](./media/notification-hubs-nodejs-how-to-use-notification-hubs/notification-hubs-portal.png)

> [AZURE.NOTE] Du kan også hente forbindelsesstrengen ved hjælp af Cmdletten **Get-AzureSbNamespace** fra [Azure PowerShell](../powershell-install-configure.md) eller kommandoen **azure sb navneområde show** med [Azure kommandolinjen (Azure CLI)](../xplat-cli-install.md).

##<a name="general-architecture"></a>Generelle Arkitektur

Objektet **NotificationHubService** Fremviser følgende objektforekomster til at sende pushmeddelelser for bestemte enheder og programmer:

* **Android** - bruge objektet **GcmService** , der findes på **notificationHubService.gcm**
* **iOS** - bruge objektet **ApnsService** , som er tilgængelige på **notificationHubService.apns**
* **Windows Phone** - bruge objektet **MpnsService** , der findes på **notificationHubService.mpns**
* **Universel Windows-Platform** - bruge objektet **WnsService** , der findes på **notificationHubService.wns**

### <a name="how-to-send-push-notifications-to-android-applications"></a>Sådan: Send pushmeddelelser til Android-programmer

Objektet **GcmService** indeholder en **sende** afskrivningsmetode, som kan bruges til at sende pushmeddelelser til Android-programmer. Metoden **send** accepterer følgende parametre:

* **Mærker** - mærke-id. Hvis ingen mærke ikke er angivet, sendes meddelelsen til al klienter.
* **Data** - meddelelsens JSON eller rå streng data.
* **Tilbagekald** - Tilbagekaldsfunktionen.

Flere oplysninger om formatet data, i afsnittet **data** i [implementere GCM](http://developer.android.com/google/gcm/server.html#payload) serverdokument.

Følgende kode bruger den **GcmService** -forekomst, der vises af **NotificationHubService** til at sende en push-meddelelse til alle registrerede klienter.

    var payload = {
      data: {
        message: 'Hello!'
      }
    };
    notificationHubService.gcm.send(null, payload, function(error){
      if(!error){
        //notification sent
      }
    });

### <a name="how-to-send-push-notifications-to-ios-applications"></a>Sådan: Send pushmeddelelser til iOS-programmer

Samme som med Android programmer, der er beskrevet ovenfor, objektet **ApnsService** indeholder en **sende** afskrivningsmetode, som kan bruges til at sende pushmeddelelser til iOS-programmer. Metoden **send** accepterer følgende parametre:

* **Mærker** - mærke-id. Hvis ingen mærke ikke er angivet, sendes meddelelsen til alle klienter.
* **Data** - meddelelsens JSON eller streng data.
* **Tilbagekald** - Tilbagekaldsfunktionen.

Få mere at vide formatet data i afsnittet **Besked om overførslen** af [lokale og Push meddelelse Programming Guide](http://developer.apple.com/library/ios/#documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/ApplePushService/ApplePushService.html) dokumentet.

Følgende kode bruger den **ApnsService** -forekomst, der vises af **NotificationHubService** til at sende en meddelelse til alle klienter:

    var payload={
        alert: 'Hello!'
      };
    notificationHubService.apns.send(null, payload, function(error){
      if(!error){
        // notification sent
      }
    });

### <a name="how-to-send-push-notifications-to-windows-phone-applications"></a>Sådan: Send pushmeddelelser til Windows Phone-programmer

Objektet **MpnsService** indeholder en **sende** afskrivningsmetode, som kan bruges til at sende pushmeddelelser til Windows Phone-programmer. Metoden **send** accepterer følgende parametre:

* **Mærker** - mærke-id. Hvis ingen mærke ikke er angivet, sendes meddelelsen til alle klienter.
* **Data** - meddelelsens XML-data.
* **TargetName**  -  `toast` til toast meddelelser. `token`for beskeder.
* **NotificationClass** - prioriteten af meddelelsen. I afsnittet **HTTP Sidehovedelementer** af dokumentet [Push-beskeder fra en server](http://msdn.microsoft.com/library/hh221551.aspx) til gyldige værdier.
* **Indstillinger** – valgfrit anmodning sidehoveder.
* **Tilbagekald** - Tilbagekaldsfunktionen.

En liste over gyldige **TargetName**, **NotificationClass** og sidehoved indstillinger skal du se siden [Push-beskeder fra en server](http://msdn.microsoft.com/library/hh221551.aspx) .

Følgende eksempelkode bruger den **MpnsService** -forekomst, der vises af **NotificationHubService** til at sende en opslagsnål toastbeskeden:

    var payload = '<?xml version="1.0" encoding="utf-8"?><wp:Notification xmlns:wp="WPNotification"><wp:Toast><wp:Text1>string</wp:Text1><wp:Text2>string</wp:Text2></wp:Toast></wp:Notification>';
    notificationHubService.mpns.send(null, payload, 'toast', 22, function(error){
      if(!error){
        //notification sent
      }
    });

### <a name="how-to-send-push-notifications-to-universal-windows-platform-uwp-applications"></a>Sådan: Send pushmeddelelser til Universal Windows Platform (UWP)-programmer

Objektet **WnsService** indeholder en **sende** afskrivningsmetode, som kan bruges til at sende pushmeddelelser til Universal Windows-Platform-programmer.  Metoden **send** accepterer følgende parametre:

* **Mærker** - mærke-id. Hvis ingen mærke ikke er angivet, sendes meddelelsen til alle registrerede klienter.
* **Data** - XML-meddelelse data.
* **Type** - meddelelsestypen.
* **Indstillinger** – valgfrit anmodning sidehoveder.
* **Tilbagekald** - Tilbagekaldsfunktionen.

Se en liste over gyldige typer og anmodning om sidehoveder [Push besked om tjenesten og svar sidehoveder](http://msdn.microsoft.com/library/windows/apps/hh465435.aspx).

Følgende kode bruger den **WnsService** -forekomst, der vises af **NotificationHubService** til at sende en opslagsnål toastbeskeden til en UWP app:

    var payload = '<toast><visual><binding template="ToastText01"><text id="1">Hello!</text></binding></visual></toast>';
    notificationHubService.wns.send(null, payload , 'wns/toast', function(error){
      if(!error){
        // notification sent
      }
    });

## <a name="next-steps"></a>Næste trin

De ovenstående eksempel kodestykker kan du nemt oprette service infrastruktur for at levere pushmeddelelser til en lang række enheder. Nu hvor du har lært grundlæggende om brug af besked om Hubs med node.js, skal du følge disse links for at få flere oplysninger om hvordan du kan udvide disse funktioner yderligere.

-   Se Reference til MSDN for [Azure meddelelse Hubs](https://msdn.microsoft.com/library/azure/jj927170.aspx).
-   Besøg [Azure SDK for Node] lager på GitHub for flere eksempler og implementeringsdetaljer.

  [Azure SDK for Node]: https://github.com/WindowsAzure/azure-sdk-for-node
  [Next Steps]: #nextsteps
  [What are Service Bus Topics and Subscriptions?]: #what-are-service-bus-topics
  [Create a Service Namespace]: #create-a-service-namespace
  [Obtain the Default Management Credentials for the Namespace]: #obtain-default-credentials
  [Create a Node.js Application]: #Create_a_Nodejs_Application
  [Configure Your Application to Use Service Bus]: #Configure_Your_Application_to_Use_Service_Bus
  [How to: Create a Topic]: #How_to_Create_a_Topic
  [How to: Create Subscriptions]: #How_to_Create_Subscriptions
  [How to: Send Messages to a Topic]: #How_to_Send_Messages_to_a_Topic
  [How to: Receive Messages from a Subscription]: #How_to_Receive_Messages_from_a_Subscription
  [How to: Handle Application Crashes and Unreadable Messages]: #How_to_Handle_Application_Crashes_and_Unreadable_Messages
  [How to: Delete Topics and Subscriptions]: #How_to_Delete_Topics_and_Subscriptions
  [1]: #Next_Steps
  [Topic Concepts]: .media/notification-hubs-nodejs-how-to-use-notification-hubs/sb-topics-01.png
  [Azure Classic Portal]: http://manage.windowsazure.com
  [image]: .media/notification-hubs-nodejs-how-to-use-notification-hubs/sb-queues-03.png
  [2]: .media/notification-hubs-nodejs-how-to-use-notification-hubs/sb-queues-04.png
  [3]: .media/notification-hubs-nodejs-how-to-use-notification-hubs/sb-queues-05.png
  [4]: .media/notification-hubs-nodejs-how-to-use-notification-hubs/sb-queues-06.png
  [5]: .media/notification-hubs-nodejs-how-to-use-notification-hubs/sb-queues-07.png
  [SqlFilter.SqlExpression]: http://msdn.microsoft.com/library/windowsazure/microsoft.servicebus.messaging.sqlfilter.sqlexpression.aspx
  [Azure Service Bus Notification Hubs]: http://msdn.microsoft.com/library/windowsazure/jj927170.aspx
  [SqlFilter]: http://msdn.microsoft.com/library/windowsazure/microsoft.servicebus.messaging.sqlfilter.aspx
  [Websted med WebMatrix]: /develop/nodejs/tutorials/web-site-with-webmatrix/
  [Node.js Cloud Service]: ../cloud-services/cloud-services-nodejs-develop-deploy-app.md
[Previous Management Portal]: .media/notification-hubs-nodejs-how-to-use-notification-hubs/previous-portal.png
  [nodejswebsite]: /develop/nodejs/tutorials/create-a-website-(mac)/
  [Node.js Cloud Service with Storage]: /develop/nodejs/tutorials/web-app-with-storage/
  [Node.js Web Application with Storage]: /develop/nodejs/tutorials/web-site-with-storage/
  [Azure-portalen]: https://portal.azure.com
