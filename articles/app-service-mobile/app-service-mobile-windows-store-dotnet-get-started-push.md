<properties
    pageTitle="Føje pushmeddelelser til din app Universal Windows Platform (UWP) | Azure-Mobilapps"
    description="Lær at bruge Azure App Service Mobile-Apps og Azure meddelelse Hubs til at sende pushmeddelelser til din app Universal Windows Platform (UWP)."
    services="app-service\mobile,notification-hubs"
    documentationCenter="windows"
    authors="ysxu"
    manager="dwrede"
    editor=""/>

<tags
    ms.service="app-service-mobile"
    ms.workload="mobile"
    ms.tgt_pltfrm="mobile-windows"
    ms.devlang="dotnet"
    ms.topic="article"
    ms.date="10/12/2016"
    ms.author="yuaxu"/>

# <a name="add-push-notifications-to-your-windows-app"></a>Føje pushmeddelelser til din Windows-app

[AZURE.INCLUDE [app-service-mobile-selector-get-started-push](../../includes/app-service-mobile-selector-get-started-push.md)]

##<a name="overview"></a>Oversigt

I dette selvstudium føjer du pushmeddelelser til projektet [Windows Hurtig start](app-service-mobile-windows-store-dotnet-get-started.md) , så der sendes en push-besked til enheden, hver gang en post er indsat.

Hvis du ikke bruger hentede Hurtig start server projektet, skal du pakken opslagsnål meddelelse filtypenavn. Du kan få flere oplysninger i [arbejde med .NET back end-serveren SDK til Azure Mobile-Apps](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md) .

##<a name="configure-hub"></a>Konfigurere en besked om Hub

[AZURE.INCLUDE [app-service-mobile-configure-notification-hub](../../includes/app-service-mobile-configure-notification-hub.md)]

##<a name="register-your-app-for-push-notifications"></a>Registrere din app til pushmeddelelser

Du har brug at sende din app til Windows Store, og derefter konfigurere projektet server til at integrere med Windows besked om tjenester (WNS) til at sende opslagsnål.

1. Højreklik på UWP app projektet i Visual Studio Solution Explorer, skal du klikke på **Store** > **Knytte App med Store …**. 

    ![Knytte app til Windows Store](./media/app-service-mobile-windows-store-dotnet-get-started-push/notification-hub-associate-uwp-app.png)
    
2. Klik på **Næste**i guiden, logge på med din Microsoft-konto, Skriv et navn for din app i **Reserver en ny app-navn**og derefter klikke på **Reserver**.

3. Når app registrering er blevet oprettet, Vælg appnavnet på det nye, skal du klikke på **Næste**og klik derefter på **knytte**. Dette indsætter de nødvendige oplysninger for Windows Store registrering programmanifestet.  

7. Gå til [Windows-Udviklercenter](https://dev.windows.com/en-us/overview), logge på med din Microsoft-konto, skal du klikke på den nye app registrering i **Mine apps**, og derefter udvide **Services** > **Push-beskeder**. 

8. Klik på **Live Services-websted** under **Microsoft Azure Mobile tjenester**på siden **Push-beskeder** .

9. På registreringssiden skal du notere værdien under **programmet hemmeligheder** og **Pakke SID**, som du skal bruge til at konfigurere din mobilapp back-end næste. 

    ![Knytte app til Windows Store](./media/app-service-mobile-windows-store-dotnet-get-started-push/app-service-mobile-uwp-app-push-auth.png)

    > [AZURE.IMPORTANT] Klient hemmeligt og pakke SID er vigtige sikkerhedslegitimationsoplysninger. Undlad at dele disse værdier med alle eller dele dem med din app. **Program-Id** bruges med hemmeligheden til at konfigurere godkendelse af Microsoft-Account.

##<a name="configure-the-backend-to-send-push-notifications"></a>Konfigurere back-end for at sende pushmeddelelser

[AZURE.INCLUDE [app-service-mobile-configure-wns](../../includes/app-service-mobile-configure-wns.md)]

##<a id="update-service"></a>Opdatere serveren for at sende pushmeddelelser

Brug nedenstående fremgangsmåde, der svarer til din back end-projekttype&mdash; [.NET backend-version](#dotnet) eller [Node.js backend-version](#nodejs).

### <a name="dotnet"></a>.NET back end-projekt

1. Skal du højreklikke på server projektet i Visual Studio, og skal du klikke på **Administrer NuGet pakker**, søge efter Microsoft.Azure.NotificationHubs og derefter klikke på **installation**. Derved installeres biblioteket meddelelse Hubs klient.

2. Udvid **enheder**, Åbn TodoItemController.cs, og Tilføj følgende ved hjælp af sætninger:

        using System.Collections.Generic;
        using Microsoft.Azure.NotificationHubs;
        using Microsoft.Azure.Mobile.Server.Config;

3. Tilføj følgende kode i metoden **PostTodoItem** efter opkaldet til **InsertAsync**:

        // Get the settings for the server project.
        HttpConfiguration config = this.Configuration;
        MobileAppSettingsDictionary settings =
            this.Configuration.GetMobileAppSettingsProvider().GetMobileAppSettings();

        // Get the Notification Hubs credentials for the Mobile App.
        string notificationHubName = settings.NotificationHubName;
        string notificationHubConnection = settings
            .Connections[MobileAppSettingsKeys.NotificationHubConnectionString].ConnectionString;

        // Create the notification hub client.
        NotificationHubClient hub = NotificationHubClient
            .CreateClientFromConnectionString(notificationHubConnection, notificationHubName);

        // Define a WNS payload
        var windowsToastPayload = @"<toast><visual><binding template=""ToastText01""><text id=""1"">"
                                + item.Text + @"</text></binding></visual></toast>";
        try
        {
            // Send the push notification.
            var result = await hub.SendWindowsNativeNotificationAsync(windowsToastPayload);

            // Write the success result to the logs.
            config.Services.GetTraceWriter().Info(result.State.ToString());
        }
        catch (System.Exception ex)
        {
            // Write the failure result to the logs.
            config.Services.GetTraceWriter()
                .Error(ex.Message, null, "Push.SendAsync Error");
        }

    Denne kode fortæller hubben besked om at sende en opslagsnål besked, når et nyt element er indsætningspunktet.

4. Genudgive server projektet.

### <a name="nodejs"></a>Node.js back end-projekt

1. Hvis du ikke allerede har gjort det, skal du [hente Hurtig start projektet](app-service-mobile-node-backend-how-to-use-server-sdk.md#download-quickstart) ellers Brug [online editor i portalen Azure](app-service-mobile-node-backend-how-to-use-server-sdk.md#online-editor).

2. Erstatte den eksisterende kode i filen todoitem.js med følgende:

        var azureMobileApps = require('azure-mobile-apps'),
        promises = require('azure-mobile-apps/src/utilities/promises'),
        logger = require('azure-mobile-apps/src/logger');

        var table = azureMobileApps.table();

        table.insert(function (context) {
        // For more information about the Notification Hubs JavaScript SDK,
        // see http://aka.ms/nodejshubs
        logger.info('Running TodoItem.insert');

        // Define the WNS payload that contains the new item Text.
        var payload = "<toast><visual><binding template=\ToastText01\><text id=\"1\">"
                                    + context.item.text + "</text></binding></visual></toast>";

        // Execute the insert.  The insert returns the results as a Promise,
        // Do the push as a post-execute action within the promise flow.
        return context.execute()
            .then(function (results) {
                // Only do the push if configured
                if (context.push) {
                    // Send a WNS native toast notification.
                    context.push.wns.sendToast(null, payload, function (error) {
                        if (error) {
                            logger.error('Error while sending push notification: ', error);
                        } else {
                            logger.info('Push notification sent successfully!');
                        }
                    });
                }
                // Don't forget to return the results from the context.execute()
                return results;
            })
            .catch(function (error) {
                logger.error('Error while running context.execute: ', error);
            });
        });

        module.exports = table;

    Dette sender en WNS toast-besked, der indeholder item.text, når der indsættes en ny opgaveliste vare.

2. Når du redigerer filen på din lokale computer, genpublicere server projektet.

##<a id="update-app"></a>Føje pushmeddelelser til din app

Næste, skal din app tilmelde pushmeddelelser på opstart. Når du har allerede aktiveret godkendelse, skal du kontrollere, at brugeren tegn i før du forsøger at tilmelde dig pushmeddelelser.

1. Åbn filen **App.xaml.cs** projektet og tilføje følgende `using` sætninger:

        using System.Threading.Tasks;
        using Windows.Networking.PushNotifications;

2. Tilføj følgende **InitNotificationsAsync** metode definitionen til **App** -klasse i den samme fil:

        private async Task InitNotificationsAsync()
        {
            // Get a channel URI from WNS.
            var channel = await PushNotificationChannelManager
                .CreatePushNotificationChannelForApplicationAsync();

            // Register the channel URI with Notification Hubs.
            await App.MobileService.GetPush().RegisterAsync(channel.Uri);
        }

    Denne kode henter ChannelURI for appen fra WNS, og derefter registrerer, ChannelURI ved hjælp af din App Service Mobile-App.

3. Tilføje **asynkron** ændring til metode definitionen øverst i hændelseshandler **OnLaunched** i **App.xaml.cs**og tilføje følgende opkaldet til den nye **InitNotificationsAsync** metode, som i følgende eksempel:

        protected async override void OnLaunched(LaunchActivatedEventArgs e)
        {
            await InitNotificationsAsync();

            // ...
        }

    Dette garanterer, at den forbigående ChannelURI er registreret hver gang programmet startes.

4. Genopbygge projektet UWP app. Din app er nu klar til at modtage toast meddelelser.

##<a id="test"></a>Test pushmeddelelser i din app

[AZURE.INCLUDE [app-service-mobile-windows-universal-test-push](../../includes/app-service-mobile-windows-universal-test-push.md)]


##<a id="more"></a>Næste trin

Lær mere om pushbeskeder:

* [Sådan bruges administrerede klienten til Azure Mobile-Apps](app-service-mobile-dotnet-how-to-use-client-library.md#how-to-register-push-templates-to-send-cross-platform-notifications)  
Skabeloner giver dig mulighed for at sende på tværs af platforme skubber og oversatte skubber. Lær at registrere skabeloner.

* [Diagnosticere opslagsnål besked om problemer](../notification-hubs/notification-hubs-push-notification-fixer.md)  
Der er forskellige årsager hvorfor meddelelser muligvis får opgivet eller ikke slutter på enheder. Dette emne beskrives, hvordan til at analysere og finde ud af den egentlige årsag opslagsnål besked om fejl. 

Overvej fortsætter på en af følgende selvstudier:

+ [Føje godkendelse til din app](app-service-mobile-windows-store-dotnet-get-started-users.md)  
  Lær at godkende brugere af din app til en identitetsudbyder.

+ [Aktivere offlinesynkronisering for din app](app-service-mobile-windows-store-dotnet-get-started-offline-data.md)  
  Lær at føje offline-understøttelse din app, ved hjælp af en Mobile-App backend. Offlinesynkronisering gør det muligt for slutbrugere til at interagere med en mobilapp&mdash;visning, tilføjelse eller redigering af data&mdash;selv, når der er ingen netværksforbindelse.

<!-- Anchors. -->

<!-- URLs. -->
[Azure Portal]: https://portal.azure.com/

<!-- Images. -->

