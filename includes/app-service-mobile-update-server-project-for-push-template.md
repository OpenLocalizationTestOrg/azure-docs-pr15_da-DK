I dette afsnit, skal opdatere du kode i eksisterende Mobile-Apps back end-projektet til at sende en push-besked, hver gang der tilføjes et nyt element. Dette er drevet af besked om Hubs [skabelon](../articles/notification-hubs/notification-hubs-templates-cross-platform-push-messages.md) funktion, så i tværs af platforme skubber. De forskellige klienter er tilmeldt pushmeddelelser ved hjælp af skabeloner, og en enkelt universal opslagsnål kan få adgang til alle klientplatforme.

Vælg følgende fremgangsmåde, der svarer til din back end-projekttype&mdash; [.NET backend-version](#dotnet) eller [Node.js backend-version](#nodejs).

### <a name="dotnet"></a>.NET back end-projekt
1. I Visual Studio, skal du højreklikke på server projektet og klikke på **Administrer NuGet-pakker**, søge efter `Microsoft.Azure.NotificationHubs`, klik derefter på **Installer**. Derved installeres biblioteket meddelelse Hubs for at sende meddelelser fra din backend-version.

3. Åbn **enheder**i project server > **TodoItemController.cs**, og Tilføj følgende ved hjælp af sætninger:

        using System.Collections.Generic;
        using Microsoft.Azure.NotificationHubs;
        using Microsoft.Azure.Mobile.Server.Config;
    

2. Tilføj følgende kode i metoden **PostTodoItem** efter opkaldet til **InsertAsync**:  

        // Get the settings for the server project.
        HttpConfiguration config = this.Configuration;
        MobileAppSettingsDictionary settings = 
            this.Configuration.GetMobileAppSettingsProvider().GetMobileAppSettings();
        
        // Get the Notification Hubs credentials for the Mobile App.
        string notificationHubName = settings.NotificationHubName;
        string notificationHubConnection = settings
            .Connections[MobileAppSettingsKeys.NotificationHubConnectionString].ConnectionString;

        // Create a new Notification Hub client.
        NotificationHubClient hub = NotificationHubClient
        .CreateClientFromConnectionString(notificationHubConnection, notificationHubName);

        // Sending the message so that all template registrations that contain "messageParam"
        // will receive the notifications. This includes APNS, GCM, WNS, and MPNS template registrations.
        Dictionary<string,string> templateParams = new Dictionary<string,string>();
        templateParams["messageParam"] = item.Text + " was added to the list.";

        try
        {
            // Send the push notification and log the results.
            var result = await hub.SendTemplateNotificationAsync(templateParams);

            // Write the success result to the logs.
            config.Services.GetTraceWriter().Info(result.State.ToString());
        }
        catch (System.Exception ex)
        {
            // Write the failure result to the logs.
            config.Services.GetTraceWriter()
                .Error(ex.Message, null, "Push.SendAsync Error");
        }

    Dette sender en meddelelse om skabelon, der indeholder elementet. Tekst, når et nyt element er indsat.

4. Genudgive server projektet. 

### <a name="nodejs"></a>Node.js back end-projekt

1. Hvis du ikke allerede har gjort det, skal du [hente Hurtig start back end-projektet](app-service-mobile-node-backend-how-to-use-server-sdk.md#download-quickstart) ellers Brug [online editor i portalen Azure](app-service-mobile-node-backend-how-to-use-server-sdk.md#online-editor).

2. Erstatte den eksisterende kode i todoitem.js med følgende:

        var azureMobileApps = require('azure-mobile-apps'),
        promises = require('azure-mobile-apps/src/utilities/promises'),
        logger = require('azure-mobile-apps/src/logger');
    
        var table = azureMobileApps.table();
        
        table.insert(function (context) {
        // For more information about the Notification Hubs JavaScript SDK, 
        // see http://aka.ms/nodejshubs
        logger.info('Running TodoItem.insert');
        
        // Define the template payload.
        var payload = '{"messageParam": "' + context.item.text + '" }';  
        
        // Execute the insert.  The insert returns the results as a Promise,
        // Do the push as a post-execute action within the promise flow.
        return context.execute()
            .then(function (results) {
                // Only do the push if configured
                if (context.push) {
                    // Send a template notification.
                    context.push.send(null, payload, function (error) {
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

    Dette sender en meddelelse om skabelon, der indeholder item.text, når der indsættes et nyt element.

2. Når du redigerer filen på din lokale computer, genpublicere server projektet.
