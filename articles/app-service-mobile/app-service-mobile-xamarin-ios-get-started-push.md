<properties
    pageTitle="Føje pushmeddelelser til din Xamarin.iOS app med Azure App Service"
    description="Lær at bruge Azure App Service til at sende pushmeddelelser til din Xamarin.iOS-app"
    services="app-service\mobile"
    documentationCenter="xamarin"
    authors="ysxu"
    manager="dwrede"
    editor=""/>

<tags
    ms.service="app-service-mobile"
    ms.workload="mobile"
    ms.tgt_pltfrm="mobile-xamarin-ios"
    ms.devlang="dotnet"
    ms.topic="article"
    ms.date="10/12/2016"
    ms.author="yuaxu"/>

# <a name="add-push-notifications-to-your-xamarinios-app"></a>Føje pushmeddelelser til din Xamarin.iOS App

[AZURE.INCLUDE [app-service-mobile-selector-get-started-push](../../includes/app-service-mobile-selector-get-started-push.md)]

##<a name="overview"></a>Oversigt

I dette selvstudium føjer du pushmeddelelser til projektet [Xamarin.iOS Hurtig start](app-service-mobile-xamarin-ios-get-started.md) , så der sendes en push-besked til enheden, hver gang en post er indsat.

Hvis du ikke bruger hentede Hurtig start server projektet, skal du pakken opslagsnål meddelelse filtypenavn. Du kan få flere oplysninger i [arbejde med .NET back end-serveren SDK til Azure Mobile-Apps](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md) .

##<a name="prerequisites"></a>Forudsætninger

* Udføre selvstudiet [Xamarin.iOS Hurtig start](app-service-mobile-xamarin-ios-get-started.md) .

* En fysisk iOS-enhed. Pushmeddelelser, der ikke understøttes af iOS simulator.

##<a name="register-the-app-for-push-notifications-on-apples-developer-portal"></a>Registrere app til pushmeddelelser på Apples udvikler portal

[AZURE.INCLUDE [Enable Apple Push Notifications](../../includes/enable-apple-push-notifications.md)]

##<a name="configure-your-mobile-app-to-send-push-notifications"></a>Konfigurere din Mobile-App for at sende pushmeddelelser

[AZURE.INCLUDE [app-service-mobile-apns-configure-push](../../includes/app-service-mobile-apns-configure-push.md)]

##<a name="update-the-server-project-to-send-push-notifications"></a>Opdatere server projektet for at sende pushmeddelelser

[AZURE.INCLUDE [app-service-mobile-update-server-project-for-push-template](../../includes/app-service-mobile-update-server-project-for-push-template.md)]

##<a name="configure-your-xamarinios-project"></a>Konfigurere projektet Xamarin.iOS

[AZURE.INCLUDE [app-service-mobile-xamarin-ios-configure-project](../../includes/app-service-mobile-xamarin-ios-configure-project.md)]

##<a name="add-push-notifications-to-your-app"></a>Føje pushmeddelelser til din app

1. Tilføj følgende egenskab i **QSTodoService**, så **AppDelegate** kan få fat på mobilklient:

            public MobileServiceClient GetClient {
            get
            {
                return client;
            }
            private set
            {
                client = value;
            }
        }

1. Tilføj følgende `using` sætningen til toppen af filen **AppDelegate.cs** .

        using Microsoft.WindowsAzure.MobileServices;
        using Newtonsoft.Json.Linq;

2. Tilsidesætte hændelsen **FinishedLaunching** i **AppDelegate**:

        public override bool FinishedLaunching(UIApplication application, NSDictionary launchOptions)
        {
            // registers for push for iOS8
            var settings = UIUserNotificationSettings.GetSettingsForTypes(
                UIUserNotificationType.Alert
                | UIUserNotificationType.Badge
                | UIUserNotificationType.Sound,
                new NSSet());

            UIApplication.SharedApplication.RegisterUserNotificationSettings(settings);
            UIApplication.SharedApplication.RegisterForRemoteNotifications();

            return true;
        }

3. Tilsidesætte hændelsen **RegisteredForRemoteNotifications** i den samme fil. I denne kode registrerer du for en enkelt skabelon meddelelse, der skal sendes på tværs af alle understøttede platforme af serveren.

    Du kan finde flere oplysninger om skabeloner med besked om Hubs, [skabeloner](../notification-hubs/notification-hubs-templates-cross-platform-push-messages.md).


        public override void RegisteredForRemoteNotifications(UIApplication application, NSData deviceToken)
        {
            MobileServiceClient client = QSTodoService.DefaultService.GetClient;

            const string templateBodyAPNS = "{\"aps\":{\"alert\":\"$(messageParam)\"}}";

            JObject templates = new JObject();
            templates["genericMessage"] = new JObject
            {
                {"body", templateBodyAPNS}
            };

            // Register for push with your mobile app
            var push = client.GetPush();
            push.RegisterAsync(deviceToken, templates);
        }


4. Derefter tilsidesætte hændelsen **DidReceivedRemoteNotification** :

        public override void DidReceiveRemoteNotification (UIApplication application, NSDictionary userInfo, Action<UIBackgroundFetchResult> completionHandler)
        {
            NSDictionary aps = userInfo.ObjectForKey(new NSString("aps")) as NSDictionary;

            string alert = string.Empty;
            if (aps.ContainsKey(new NSString("alert")))
                alert = (aps [new NSString("alert")] as NSString).ToString();

            //show alert
            if (!string.IsNullOrEmpty(alert))
            {
                UIAlertView avAlert = new UIAlertView("Notification", alert, null, "OK", null);
                avAlert.Show();
            }
        }

Din app er nu opdateret til at understøtte pushmeddelelser.

## <a name="test"></a>Test pushmeddelelser i din app

1. Tryk på knappen **Kør** til at opbygge projektet og starte appen i stand til en iOS-enhed, og klik derefter på **OK** for at acceptere pushbeskeder.

    > [AZURE.NOTE] Du skal eksplicit acceptere pushmeddelelser fra din app. Denne anmodning sker kun, første gang, der kører på app.

2. Skriv en opgave i app, og klik på plustegnet (**+**) ikon.

3. Kontrollér, at en meddelelse er modtaget og derefter klikke på **OK** for at afvise meddelelsen.

4. Gentag trin 2, og med det samme lukker appen og derefter kontrollere, at der vises en besked.

Du har fuldført dette selvstudium.

<!-- Images. -->

<!-- URLs. -->



