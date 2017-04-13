<properties
    pageTitle="Føje pushmeddelelser til din Xamarin.Forms app | Microsoft Azure"
    description="Lær at bruge Azure services til at sende flere platforme pushmeddelelser til dine Xamarin.Forms apps."
    services="app-service\mobile"
    documentationCenter="xamarin"
    authors="ysxu"
    manager="dwrede"
    editor=""/>

<tags
    ms.service="app-service-mobile"
    ms.workload="mobile"
    ms.tgt_pltfrm="mobile-xamarin"
    ms.devlang="dotnet"
    ms.topic="article"
    ms.date="10/12/2016"
    ms.author="yuaxu"/>

# <a name="add-push-notifications-to-your-xamarinforms-app"></a>Føje pushmeddelelser til din Xamarin.Forms-app

[AZURE.INCLUDE [app-service-mobile-selector-get-started-push](../../includes/app-service-mobile-selector-get-started-push.md)]

##<a name="overview"></a>Oversigt

I dette selvstudium, kan du tilføje opslagsnål beskeder for alle projekter udløste fra [Xamarin.Forms Hurtig start](app-service-mobile-xamarin-forms-get-started.md) , så der sendes en push-meddelelse til alle klienter på tværs af platforme, hver gang en post er indsat.

Hvis du ikke bruger hentede Hurtig start server projektet, skal du pakken opslagsnål meddelelse filtypenavn. Du kan få flere oplysninger i [arbejde med .NET back end-serveren SDK til Azure Mobile-Apps](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md) .

##<a name="prerequisites"></a>Forudsætninger

* IOS, skal du en [Apple udvikler Program medlemskab](https://developer.apple.com/programs/ios/) og en fysisk iOS-enhed fordi [iOS simulator understøtter ikke pushmeddelelser](https://developer.apple.com/library/ios/documentation/IDEs/Conceptual/iOS_Simulator_Guide/TestingontheiOSSimulator.html).

##<a name="configure-hub"></a>Konfigurere en besked om Hub

[AZURE.INCLUDE [app-service-mobile-configure-notification-hub](../../includes/app-service-mobile-configure-notification-hub.md)]

##<a name="update-the-server-project-to-send-push-notifications"></a>Opdatere server projektet for at sende pushmeddelelser

[AZURE.INCLUDE [app-service-mobile-update-server-project-for-push-template](../../includes/app-service-mobile-update-server-project-for-push-template.md)]


##<a name="optional-configure-and-run-the-android-project"></a>(Valgfrit) Konfigurere og køre Android projektet

Afslutte dette afsnit for at aktivere pushmeddelelser for Xamarin.Forms Droid projektet til Android.


###<a name="enable-firebase-cloud-messaging-fcm"></a>Aktivere Firebase skyen Messaging (FCM)

[AZURE.INCLUDE [notification-hubs-enable-firebase-cloud-messaging](../../includes/notification-hubs-enable-firebase-cloud-messaging.md)]

###<a name="configure-the-mobile-app-backend-to-send-push-requests-using-fcm"></a>Konfigurere Mobile-App back-end for at sende opslagsnål anmodninger ved hjælp af FCM

[AZURE.INCLUDE [app-service-mobile-android-configure-push](../../includes/app-service-mobile-android-configure-push.md)]

###<a name="add-push-notifications-to-the-android-project"></a>Føje pushmeddelelser til Android projektet

Med den back end-konfigureret med FCM, kan vi tilføje komponenter og WBS-koder til klienten til at registrere med FCM, registrere til pushmeddelelser med Azure meddelelse Hubs gennem mobilapp back end- og modtage meddelelser.

1. I **Droid** projektet, skal du højreklikke på mappen **komponenter** , skal du klikke på **Få mere komponenter...**, søge efter komponenten **Google Cloud Messaging klient** og føje det til projektet. Denne komponent understøtter pushmeddelelser for et Xamarin Android projekt.


2. Åbn filen MainActivity.cs projektet og tilføje følgende ved hjælp af sætning øverst i filen:

        using Gcm.Client;

3. Du kan tilføje følgende kode til metoden **OnCreate** efter opkaldet til **LoadApplication**:

        try
        {
            // Check to ensure everything's setup right
            GcmClient.CheckDevice(this);
            GcmClient.CheckManifest(this);

            // Register for push notifications
            System.Diagnostics.Debug.WriteLine("Registering...");
            GcmClient.Register(this, PushHandlerBroadcastReceiver.SENDER_IDS);
        }
        catch (Java.Net.MalformedURLException)
        {
            CreateAndShowDialog("There was an error creating the client. Verify the URL.", "Error");
        }
        catch (Exception e)
        {
            CreateAndShowDialog(e.Message, "Error");
        }


4. Tilføje en ny **CreateAndShowDialog** hjælper metode, på følgende måde:

        private void CreateAndShowDialog(String message, String title)
        {
            AlertDialog.Builder builder = new AlertDialog.Builder(this);

            builder.SetMessage (message);
            builder.SetTitle (title);
            builder.Create().Show ();
        }


5. Føj følgende kode til klassen **MainActivity** :

        // Create a new instance field for this activity.
        static MainActivity instance = null;

        // Return the current activity instance.
        public static MainActivity CurrentActivity
        {
            get
            {
                return instance;
            }
        }

    Dette viser den aktuelle forekomst af **MainActivity** , så vi kan udføre på den primære brugergrænsefladetråd.

6. Initialiseret den `instance`, variable i starten af metoden **OnCreate** på følgende måde.

        // Set the current instance of MainActivity.
        instance = this;

2. Føje en ny klassefil til **Droid** projektet med navnet `GcmService.cs`, og Sørg for, at følgende **ved hjælp af** udtalelser findes øverst i filen:

        using Android.App;
        using Android.Content;
        using Android.Media;
        using Android.Support.V4.App;
        using Android.Util;
        using Gcm.Client;
        using Microsoft.WindowsAzure.MobileServices;
        using Newtonsoft.Json.Linq;
        using System;
        using System.Collections.Generic;
        using System.Diagnostics;
        using System.Text;


9. Tilføj følgende anmodninger om tilladelser øverst i filen, efter **ved hjælp af** sætningerne og før angivelsen **navneområde** .

        [assembly: Permission(Name = "@PACKAGE_NAME@.permission.C2D_MESSAGE")]
        [assembly: UsesPermission(Name = "@PACKAGE_NAME@.permission.C2D_MESSAGE")]
        [assembly: UsesPermission(Name = "com.google.android.c2dm.permission.RECEIVE")]
        [assembly: UsesPermission(Name = "android.permission.INTERNET")]
        [assembly: UsesPermission(Name = "android.permission.WAKE_LOCK")]
        //GET_ACCOUNTS is only needed for android versions 4.0.3 and below
        [assembly: UsesPermission(Name = "android.permission.GET_ACCOUNTS")]

10. Tilføj følgende klassedefinitionen navneområdet. 

        [BroadcastReceiver(Permission = Gcm.Client.Constants.PERMISSION_GCM_INTENTS)]
        [IntentFilter(new string[] { Gcm.Client.Constants.INTENT_FROM_GCM_MESSAGE }, Categories = new string[] { "@PACKAGE_NAME@" })]
        [IntentFilter(new string[] { Gcm.Client.Constants.INTENT_FROM_GCM_REGISTRATION_CALLBACK }, Categories = new string[] { "@PACKAGE_NAME@" })]
        [IntentFilter(new string[] { Gcm.Client.Constants.INTENT_FROM_GCM_LIBRARY_RETRY }, Categories = new string[] { "@PACKAGE_NAME@" })]
        public class PushHandlerBroadcastReceiver : GcmBroadcastReceiverBase<GcmService>
        {
            public static string[] SENDER_IDS = new string[] { "<PROJECT_NUMBER>" };
        }

    >[AZURE.NOTE]Erstat **< PROJECT_NUMBER >** med dit projektnummer, du nævnt tidligere.   

11. Erstat klassen tomme **GcmService** med følgende kode, der bruger den nye broadcast modtager:

         [Service]
         public class GcmService : GcmServiceBase
         {
             public static string RegistrationID { get; private set; }

             public GcmService()
                 : base(PushHandlerBroadcastReceiver.SENDER_IDS){}
         }


12. Tilføj følgende kode til klassen **GcmService** , der tilsidesætter hændelseshandler **OnRegistered** og implementerer en **registrere** metode.

        protected override void OnRegistered(Context context, string registrationId)
        {
            Log.Verbose("PushHandlerBroadcastReceiver", "GCM Registered: " + registrationId);
            RegistrationID = registrationId;

            var push = TodoItemManager.DefaultManager.CurrentClient.GetPush();

            MainActivity.CurrentActivity.RunOnUiThread(() => Register(push, null));
        }

        public async void Register(Microsoft.WindowsAzure.MobileServices.Push push, IEnumerable<string> tags)
        {
            try
            {
                const string templateBodyGCM = "{\"data\":{\"message\":\"$(messageParam)\"}}";

                JObject templates = new JObject();
                templates["genericMessage"] = new JObject
                {
                    {"body", templateBodyGCM}
                };

                await push.RegisterAsync(RegistrationID, templates);
                Log.Info("Push Installation Id", push.InstallationId.ToString());
            }
            catch (Exception ex)
            {
                System.Diagnostics.Debug.WriteLine(ex.Message);
                Debugger.Break();
            }
        }

        Note that this code uses the `messageParam` parameter in the template registration. 

13. Føj følgende kode, der implementerer **OnMessage**: 

        protected override void OnMessage(Context context, Intent intent)
        {
            Log.Info("PushHandlerBroadcastReceiver", "GCM Message Received!");

            var msg = new StringBuilder();

            if (intent != null && intent.Extras != null)
            {
                foreach (var key in intent.Extras.KeySet())
                    msg.AppendLine(key + "=" + intent.Extras.Get(key).ToString());
            }

            //Store the message
            var prefs = GetSharedPreferences(context.PackageName, FileCreationMode.Private);
            var edit = prefs.Edit();
            edit.PutString("last_msg", msg.ToString());
            edit.Commit();

            string message = intent.Extras.GetString("message");
            if (!string.IsNullOrEmpty(message))
            {
                createNotification("New todo item!", "Todo item: " + message);
                return;
            }

            string msg2 = intent.Extras.GetString("msg");
            if (!string.IsNullOrEmpty(msg2))
            {
                createNotification("New hub message!", msg2);
                return;
            }

            createNotification("Unknown message details", msg.ToString());
        }

        void createNotification(string title, string desc)
        {
            //Create notification
            var notificationManager = GetSystemService(Context.NotificationService) as NotificationManager;

            //Create an intent to show ui
            var uiIntent = new Intent(this, typeof(MainActivity));

            //Use Notification Builder
            NotificationCompat.Builder builder = new NotificationCompat.Builder(this);

            //Create the notification
            //we use the pending intent, passing our ui intent over which will get called
            //when the notification is tapped.
            var notification = builder.SetContentIntent(PendingIntent.GetActivity(this, 0, uiIntent, 0))
                    .SetSmallIcon(Android.Resource.Drawable.SymActionEmail)
                    .SetTicker(title)
                    .SetContentTitle(title)
                    .SetContentText(desc)

                    //Set the notification sound
                    .SetSound(RingtoneManager.GetDefaultUri(RingtoneType.Notification))

                    //Auto cancel will remove the notification once the user touches it
                    .SetAutoCancel(true).Build();

            //Show the notification
            notificationManager.Notify(1, notification);
        }

    Dette håndterer indgående meddelelser og sende dem til meddelelse manager skal vises.

14. **GcmServiceBase** kræver også, at du at implementere **OnUnRegistered** **VedFejl** handler metoderne og, som du kan gøre på følgende måde:

        protected override void OnUnRegistered(Context context, string registrationId)
        {
            Log.Error("PushHandlerBroadcastReceiver", "Unregistered RegisterationId : " + registrationId);
        }

        protected override void OnError(Context context, string errorId)
        {
            Log.Error("PushHandlerBroadcastReceiver", "GCM Error: " + errorId);
        }

Nu er du klar test pushmeddelelser i appen kører på en Android-enhed eller emulatoren.

###<a name="test-push-notifications-in-your-android-app"></a>Test pushmeddelelser i din Android-app

De første to trin er påkrævet, kun, når du tester på en emulator.

1. Sørg for, at du anvender for eller fejlfinding på en virtuel enhed, der indeholder Google APIs angive som destination, som vist nedenfor i Android virtuelle enhed (AVD) manager.

2. Tilføje en Google-konto til Android-enhed ved at klikke på **Apps** > **Indstillinger** > **Tilføj konto**og derefter følge instruktionerne til at bruge føje en eksisterende Google-konto til kameraet, for at oprette en ny.

1. Højreklik på **Droid** projektet i Visual Studio eller Xamarin Studio, og klik på **Angiv som Start projekt**.

2. Tryk på knappen **Kør** til at opbygge projektet og starte appen på din Android-enhed eller emulator.

3. Skriv en opgave i app, og klik på plustegnet (**+**) ikon.

4. Kontrollér, at en meddelelse er modtaget, når et element er blevet tilføjet.


##<a name="optional-configure-and-run-the-ios-project"></a>(Valgfrit) Konfigurere og køre iOS projektet

Dette afsnit er til at køre iOS Xamarin projektet til iOS-enheder. Hvis du ikke arbejder med iOS-enheder, kan du springe dette afsnit.

[AZURE.INCLUDE [Enable Apple Push Notifications](../../includes/enable-apple-push-notifications.md)]


####<a name="configure-the-notification-hub-for-apns"></a>Konfigurere meddelelse hubben til APNS

[AZURE.INCLUDE [app-service-mobile-apns-configure-push](../../includes/app-service-mobile-apns-configure-push.md)]

Derefter kan du konfigurere iOS project indstilling i Xamarin Studio eller Visual Studio.

[AZURE.INCLUDE [app-service-mobile-xamarin-ios-configure-project](../../includes/app-service-mobile-xamarin-ios-configure-project.md)]


####<a name="add-push-notifications-to-your-ios-app"></a>Føje pushmeddelelser til din iOS-app

1. Åbn AppDelegate.cs i project **iOS** Tilføj sætningen **ved hjælp af** følgende i toppen af filen kode.

        using Newtonsoft.Json.Linq;

4. Tilføj en tilsidesættelse for hændelsen **RegisteredForRemoteNotifications** til at tilmelde dig meddelelser i klassen **AppDelegate** :

        public override void RegisteredForRemoteNotifications(UIApplication application, 
            NSData deviceToken)
        {
            const string templateBodyAPNS = "{\"aps\":{\"alert\":\"$(messageParam)\"}}";

            JObject templates = new JObject();
            templates["genericMessage"] = new JObject
                {
                  {"body", templateBodyAPNS}
                };

            // Register for push with your mobile app
            Push push = TodoItemManager.DefaultManager.CurrentClient.GetPush();
            push.RegisterAsync(deviceToken, templates);
        }

5. Tilføj følgende Tilsidesæt for hændelseshandler **DidReceivedRemoteNotification** også i **AppDelegate**:

        public override void DidReceiveRemoteNotification(UIApplication application, 
            NSDictionary userInfo, Action<UIBackgroundFetchResult> completionHandler)
        {
            NSDictionary aps = userInfo.ObjectForKey(new NSString("aps")) as NSDictionary;

            string alert = string.Empty;
            if (aps.ContainsKey(new NSString("alert")))
                alert = (aps[new NSString("alert")] as NSString).ToString();

            //show alert
            if (!string.IsNullOrEmpty(alert))
            {
                UIAlertView avAlert = new UIAlertView("Notification", alert, null, "OK", null);
                avAlert.Show();
            }
        }

    Denne metode håndterer indgående meddelelser, mens app'en kører.

2. Tilføj følgende kode til metoden **FinishedLaunching** i klassen **AppDelegate** : 

        // Register for push notifications.
        var settings = UIUserNotificationSettings.GetSettingsForTypes(
            UIUserNotificationType.Alert
            | UIUserNotificationType.Badge
            | UIUserNotificationType.Sound,
            new NSSet());

        UIApplication.SharedApplication.RegisterUserNotificationSettings(settings);
        UIApplication.SharedApplication.RegisterForRemoteNotifications();

    Dette gør det muligt for understøttelse af eksterne meddelelser og anmodninger push registrering.

Din app er nu opdateret til at understøtte pushmeddelelser.

####<a name="test-push-notifications-in-your-ios-app"></a>Test pushmeddelelser i din iOS-app

1. Højreklik på iOS projektet, og klik på **Angiv som StartPp projekt**.

2. Tryk på knappen **Kør** eller **F5** i Visual Studio til at opbygge projektet og starte appen på en iOS-enhed, og klik derefter på **OK** for at acceptere pushbeskeder.

    > [AZURE.NOTE] Du skal eksplicit acceptere pushmeddelelser fra din app. Denne anmodning sker kun, første gang, der kører på app.

3. Skriv en opgave i app, og klik på plustegnet (**+**) ikon.

4. Kontrollér, at en meddelelse er modtaget og derefter klikke på **OK** for at afvise meddelelsen.


##<a name="optional-configure-and-run-the-windows-projects"></a>(Valgfrit) Konfigurere og køre Windows-projekter

Dette afsnit er til kørsel af Xamarin.Forms WinApp og WinPhone81 projekter til Windows-enheder. Disse trin understøtter også Universal Windows Platform (UWP) projekter. Hvis du ikke arbejder med Windows-enheder, kan du springe dette afsnit.


####<a name="register-your-windows-app-for-push-notifications-with-wns"></a>Registrere din Windows-app til pushmeddelelser med WNS

[AZURE.INCLUDE [app-service-mobile-register-wns](../../includes/app-service-mobile-register-wns.md)]


####<a name="configure-the-notification-hub-for-wns"></a>Konfigurere meddelelse hubben til WNS

[AZURE.INCLUDE [app-service-mobile-configure-wns](../../includes/app-service-mobile-configure-wns.md)]


####<a name="add-push-notifications-to-your-windows-app"></a>Føje pushmeddelelser til din Windows-app

1. Åbn **App.xaml.cs** i et projekt med Windows i Visual Studio, og Tilføj følgende **ved hjælp af** sætninger.

        using Newtonsoft.Json.Linq;
        using Microsoft.WindowsAzure.MobileServices;
        using System.Threading.Tasks;
        using Windows.Networking.PushNotifications;
        using <your_TodoItemManager_portable_class_namespace>;

    Erstatte `<your_TodoItemManager_portable_class_namespace>` med navneområdet for projektet med bærbare, der indeholder den `TodoItemManager` klasse.
 

2. Tilføj følgende **InitNotificationsAsync** metode i App.xaml.cs: 

        private async Task InitNotificationsAsync()
        {
            var channel = await PushNotificationChannelManager
                .CreatePushNotificationChannelForApplicationAsync();

            const string templateBodyWNS = 
                "<toast><visual><binding template=\"ToastText01\"><text id=\"1\">$(messageParam)</text></binding></visual></toast>";

            JObject headers = new JObject();
            headers["X-WNS-Type"] = "wns/toast";

            JObject templates = new JObject();
            templates["genericMessage"] = new JObject
            {
                {"body", templateBodyWNS},
                {"headers", headers} // Needed for WNS.
            };

            await TodoItemManager.DefaultManager.CurrentClient.GetPush()
                .RegisterAsync(channel.Uri, templates);
        }

    Denne metode får opslagsnål meddelelse om kanalen og registrerer en skabelon for at modtage en skabelon fra din meddelelse hub. En meddelelse om skabelon, der understøtter *messageParam* vil blive leveret til denne klient.

3. Opdatere **OnLaunched** begivenhed handler metode definitionen i App.xaml.cs, ved at tilføje den `async` ændring, derefter tilføje følgende linje af kode i slutningen af metoden: 

        await InitNotificationsAsync();

    Dette sikrer, at push meddelelse registrering oprettes eller opdateres, hver gang appen startes. Det er vigtigt at gøre dette for at sikre, at WNS opslagsnål kanalen altid er aktiv.  

4. Åbn filen **Package.appxmanifest** i Solution Explorer til Visual Studio, og angiv **Toast kan** til **Ja** under **beskeder**.

5. Udvikle app, og Bekræft, at der ingen fejl.  Du klient app bør nu tilmelde dig skabelon beskeder fra Mobile-App back end. Gentag dette afsnit for alle Windows projekter i din løsning.


####<a name="test-push-notifications-in-your-windows-app"></a>Test pushmeddelelser i din Windows-app

1. Højreklik på et Windows-projekt i Visual Studio, og klik på **Angiv som Start projekt**.

2. Tryk på knappen **Kør** til at opbygge projektet og starte appen.

3. Skriv et navn til en ny todoitem i app, og klik på plustegnet (**+**) for at tilføje den.

4. Kontrollér, at en meddelelse er modtaget, når elementet er blevet tilføjet.

##<a name="next-steps"></a>Næste trin

Lær mere om pushbeskeder:

* [Diagnosticere opslagsnål besked om problemer](../notification-hubs/notification-hubs-push-notification-fixer.md)  
Der er forskellige årsager hvorfor meddelelser muligvis får opgivet eller ikke slutter på enheder. Dette emne beskrives, hvordan til at analysere og finde ud af den egentlige årsag opslagsnål besked om fejl. 

Overvej fortsætter på en af følgende selvstudier:

* [Føje godkendelse til din app](app-service-mobile-xamarin-forms-get-started-users.md)  
Lær at godkende brugere af din app til en identitetsudbyder.

* [Aktivere offlinesynkronisering for din app](app-service-mobile-xamarin-forms-get-started-offline-data.md)  
  Lær at føje offline-understøttelse din app, ved hjælp af en Mobile-App backend. Offlinesynkronisering gør det muligt for slutbrugere til at interagere med en mobilapp&mdash;visning, tilføjelse eller redigering af data&mdash;selv, når der er ingen netværksforbindelse.

<!-- Images. -->

<!-- URLs. -->
[Install Xcode]: https://go.microsoft.com/fwLink/p/?LinkID=266532
[Xcode]: https://go.microsoft.com/fwLink/?LinkID=266532
[apns object]: http://go.microsoft.com/fwlink/p/?LinkId=272333

