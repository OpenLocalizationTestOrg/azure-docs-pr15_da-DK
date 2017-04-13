<properties
    pageTitle="Komme i gang med besked om Hubs til Xamarin.Android apps | Microsoft Azure"
    description="I dette selvstudium lærer du at bruge Azure meddelelse Hubs til at sende pushmeddelelser til et Xamarin Android-program."
    authors="ysxu"
    manager="erikre"
    editor=""
    services="notification-hubs"
    documentationCenter="xamarin"/>

<tags
    ms.service="notification-hubs"
    ms.workload="mobile"
    ms.tgt_pltfrm="mobile-xamarin-android"
    ms.devlang="dotnet"
    ms.topic="hero-article"
    ms.date="06/29/2016"
    ms.author="yuaxu"/>

# <a name="get-started-with-notification-hubs-with-xamarin-for-android"></a>Komme i gang med besked om hubber med Xamarin til Android

[AZURE.INCLUDE [notification-hubs-selector-get-started](../../includes/notification-hubs-selector-get-started.md)]

##<a name="overview"></a>Oversigt

Dette selvstudium viser, hvordan du bruger Azure meddelelse Hubs til at sende pushmeddelelser til et Xamarin.Android program.
Du vil oprette en tom Xamarin.Android-app, der modtager pushmeddelelser ved hjælp af Google Cloud Messaging (GCM). Når du er færdig, skal du kunne bruge din meddelelse hub transmittere pushmeddelelser til alle de enheder, der kører din app. Færdig koden er tilgængelig i [NotificationHubs app] [ GitHub] eksempel.

Dette selvstudium viser simple broadcast scenarie på ved hjælp af besked om Hubs.


## <a name="before-you-begin"></a>Inden du går i gang

[AZURE.INCLUDE [notification-hubs-hero-slug](../../includes/notification-hubs-hero-slug.md)]

Færdige koden for dette selvstudium kan findes på GitHub [her](https://github.com/Azure/azure-notificationhubs-samples/tree/master/dotnet/Xamarin/GetStartedXamarinAndroid).



##<a name="prerequisites"></a>Forudsætninger

Dette selvstudium kræver følgende:

+ Visual Studio med Xamarin i Windows eller Xamarin Studio på Mac OS X. fuldført installationsvejledning stemmer [installationen og installere til Visual Studio og Xamarin](https://msdn.microsoft.com/library/mt613162.aspx).
+ Aktive Google-konto
+ [Azure Messaging komponent]
+ [Google Cloud Messaging klient-komponent]

Fuldfør dette selvstudium er en betingelse for alle andre besked om Hubs selvstudier til Xamarin.Android apps.

> [AZURE.IMPORTANT] For at fuldføre dette selvstudium skal skal du have en active Azure-konto. Hvis du ikke har en konto, kan du oprette en gratis prøveversion konto på blot et par minutter. Yderligere oplysninger finder du [Azure gratis prøveversion](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A9C9624B5&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Fen-us%2Fdocumentation%2Farticles%2Fpartner-xamarin-notification-hubs-android-get-started%2F).

##<a name="enable-google-cloud-messaging"></a>Aktivere Google Cloud-beskeder

[AZURE.INCLUDE [mobile-services-enable-Google-cloud-messaging](../../includes/mobile-services-enable-google-cloud-messaging.md)]

##<a name="configure-your-notification-hub"></a>Konfigurere din meddelelse hub

[AZURE.INCLUDE [notification-hubs-portal-create-new-hub](../../includes/notification-hubs-portal-create-new-hub.md)]


<ol start="7">
<li><p>Klik på fanen <b>Konfigurer</b> øverst, Angiv den <b>API-nøgle</b> -værdi, du har hentet i det forrige afsnit, og klik derefter på <b>Gem</b>.</p>
</li>
</ol>
&emsp;&emsp;![](./media/notification-hubs-android-get-started/notification-hub-configure-android.png)


Din meddelelse hub nu er konfigureret til at arbejde med GCM, og du har forbindelse strengene både registrere din app til at modtage meddelelser og sende pushmeddelelser.

##<a name="connect-your-app-to-the-notification-hub"></a>Oprette forbindelse din app til hubben meddelelse

###<a name="create-a-new-project"></a>Oprette et nyt projekt

1. Klik på **Ny løsning**Xamarin Studio, skal du klikke på **Android-Appen**, og klik på **Næste**.

    ![](./media/partner-xamarin-notification-hubs-android-get-started/notification-hub-create-xamarin-android-project1.png)

2. Angiv din **App-navn** og **id**. Klik på **Mål Plaforms** , du vil understøtter, og klik derefter på **Næste** og **oprette**.

    ![](./media/partner-xamarin-notification-hubs-android-get-started/notification-hub-create-xamarin-android-project2.png)


    Dette opretter et nyt Android projekt.

2. Åbn egenskaberne for projektet ved at højreklikke på det nye projekt i visningen løsning og vælge **Indstillinger**. Markér **Android programmet** elementet i afsnittet **oprette** .

    Sørg for, at det første bogstav i dit **pakkenavn** er små bogstaver.

    > [AZURE.IMPORTANT] Det første bogstav i navnet på pakken skal være små bogstaver. Ellers modtager manifest programfejl, når du har registreret dit **BroadcastReceiver** og **IntentFilter** for pushmeddelelser nedenfor.

    ![](./media/partner-xamarin-notification-hubs-android-get-started/notification-hub--xamarin-android-app-options.png)


3. Du kan også angive **Minimum Android version** til en anden API niveau.

4. Du kan også angive **mål Android version** til den andre API-version, du vil målrette (skal være API niveau 8 eller nyere).

Klik på **OK** , og Luk dialogboksen Indstillinger for Project.


###<a name="add-the-required-components-to-your-project"></a>Tilføj de påkrævede komponenter til dit projekt

Google Cloud Messaging klienten tilgængelig på Xamarin komponent Store forenkler understøttelse af pushmeddelelser i Xamarin.Android.

1. Højreklik på mappen komponenter i Xamarin.Android app, og vælg **Få flere komponenter**.

2. Søge efter komponenten **Azure Messaging** og tildele den til projektet.

3. Søge efter komponenten **Google Cloud Messaging klienten** og tildele den til projektet.


###<a name="set-up-notification-hubs-in-your-project"></a>Konfigurere meddelelse hubs i dit projekt

1. Indhent de følgende oplysninger til din Android-hub app og meddelelse:

    - **GoogleProjectNumber**: få denne projektnummer værdi fra oversigten over din app på Google udvikler-portalen. Du har lavet en note for denne værdi tidligere, da du oprettede appen på portalen.
    - **Lyt forbindelsesstreng**: dashboardet i [Klassisk Azure-portalen], klik på **Vis forbindelsesstrenge**. Kopiere forbindelsesstrengen *DefaultListenSharedAccessSignature* for denne værdi.
    - **Hub navn**: Dette er navnet på din hub fra [Azure klassisk Portal]. For eksempel, *mynotificationhub2*.

    Opret en **Constants.cs** klasse for projektet Xamarin og Definer følgende konstant værdier i klassen. Erstat pladsholderne med dine værdier.

        public static class Constants
        {
            public const string SenderID = "<GoogleProjectNumber>"; // Google API Project Number
            public const string ListenConnectionString = "<Listen connection string>";
            public const string NotificationHubName = "<hub name>";
        }

2. Tilføj følgende ved hjælp af sætninger til **MainActivity.cs**:

        using Android.Util;
        using Gcm.Client;

3. Tilføje en forekomst variabel til den `MainActivity` klasse, der bruges til at vise en besked om dialogboksen, når app'en kører:

        public static MainActivity instance;


3. Oprette følgende metode i klassen **MainActivity** :

        private void RegisterWithGCM()
        {
            // Check to ensure everything's set up right
            GcmClient.CheckDevice(this);
            GcmClient.CheckManifest(this);

            // Register for push notifications
            Log.Info("MainActivity", "Registering...");
            GcmClient.Register(this, Constants.SenderID);
        }

4. I den `OnCreate` metode til at **MainActivity.cs**initialiseret den `instance` variabel og tilføje et opkald til `RegisterWithGCM`:

        protected override void OnCreate (Bundle bundle)
        {
            instance = this;

            base.OnCreate (bundle);

            // Set your view from the "main" layout resource
            SetContentView (Resource.Layout.Main);

            // Get your button from the layout resource,
            // and attach an event to it
            Button button = FindViewById<Button> (Resource.Id.myButton);

            RegisterWithGCM();
        }


4. Oprette en ny klasse **MyBroadcastReceiver**.

    > [AZURE.NOTE] Vi fører til at oprette en **BroadcastReceiver** klasse fra bunden nedenfor. Et hurtigt alternativ til at oprette **MyBroadcastReceiver.cs** manuelt er dog henvise til filen **GcmService.cs** findes i eksempel Xamarin.Android projektet inkluderet med [eksempler på NotificationHubs][GitHub]. Duplikere **GcmService.cs** og ændre klassenavne kan være et godt sted at starte samt.

5. Tilføj følgende ved hjælp af sætninger til **MyBroadcastReceiver.cs** (referere til den-komponent og samling, du tilføjede tidligere):

        using System.Collections.Generic;
        using System.Text;
        using Android.App;
        using Android.Content;
        using Android.Util;
        using Gcm.Client;
        using WindowsAzure.Messaging;

5. Tilføj de anmodninger om følgende tilladelser mellem sætningerne **ved hjælp af** og **navneområde** angivelsen i **MyBroadcastReceiver.cs**:

        [assembly: Permission(Name = "@PACKAGE_NAME@.permission.C2D_MESSAGE")]
        [assembly: UsesPermission(Name = "@PACKAGE_NAME@.permission.C2D_MESSAGE")]
        [assembly: UsesPermission(Name = "com.google.android.c2dm.permission.RECEIVE")]

        //GET_ACCOUNTS is needed only for Android versions 4.0.3 and below
        [assembly: UsesPermission(Name = "android.permission.GET_ACCOUNTS")]
        [assembly: UsesPermission(Name = "android.permission.INTERNET")]
        [assembly: UsesPermission(Name = "android.permission.WAKE_LOCK")]

6. **MyBroadcastReceiver.cs**, ændre klassen **MyBroadcastReceiver** så det svarer til følgende:

        [BroadcastReceiver(Permission=Gcm.Client.Constants.PERMISSION_GCM_INTENTS)]
        [IntentFilter(new string[] { Gcm.Client.Constants.INTENT_FROM_GCM_MESSAGE },
            Categories = new string[] { "@PACKAGE_NAME@" })]
        [IntentFilter(new string[] { Gcm.Client.Constants.INTENT_FROM_GCM_REGISTRATION_CALLBACK },
            Categories = new string[] { "@PACKAGE_NAME@" })]
        [IntentFilter(new string[] { Gcm.Client.Constants.INTENT_FROM_GCM_LIBRARY_RETRY },
            Categories = new string[] { "@PACKAGE_NAME@" })]
        public class MyBroadcastReceiver : GcmBroadcastReceiverBase<PushHandlerService>
        {
            public static string[] SENDER_IDS = new string[] { Constants.SenderID };

            public const string TAG = "MyBroadcastReceiver-GCM";
        }

7. Tilføje en anden klasse i **MyBroadcastReceiver.cs** med navnet **PushHandlerService**, der stammer fra **GcmServiceBase**. Sørg for at anvende attributten **Service** på klassen:

        [Service] // Must use the service tag
        public class PushHandlerService : GcmServiceBase
        {
            public static string RegistrationID { get; private set; }
            private NotificationHub Hub { get; set; }

            public PushHandlerService() : base(Constants.SenderID)
            {
                Log.Info(MyBroadcastReceiver.TAG, "PushHandlerService() constructor");
            }
        }


8. **GcmServiceBase** implementerer metoder **OnRegistered()**, **OnUnRegistered()**, **OnMessage()**, **OnRecoverableError()**og **OnError()**. Vores **PushHandlerService** implementeringsklasse skal tilsidesætte disse metoder, og disse metoder vil gælde som svar på interaktion med besked om hubben.


9. Tilsidesæt metoden **OnRegistered()** i **PushHandlerService** ved hjælp af følgende kode:

        protected override void OnRegistered(Context context, string registrationId)
        {
            Log.Verbose(MyBroadcastReceiver.TAG, "GCM Registered: " + registrationId);
            RegistrationID = registrationId;

            createNotification("PushHandlerService-GCM Registered...",
                                "The device has been Registered!");

            Hub = new NotificationHub(Constants.NotificationHubName, Constants.ListenConnectionString,
                                        context);
            try
            {
                Hub.UnregisterAll(registrationId);
            }
            catch (Exception ex)
            {
                Log.Error(MyBroadcastReceiver.TAG, ex.Message);
            }

            //var tags = new List<string>() { "falcons" }; // create tags if you want
            var tags = new List<string>() {};

            try
            {
                var hubRegistration = Hub.Register(registrationId, tags.ToArray());
            }
            catch (Exception ex)
            {
                Log.Error(MyBroadcastReceiver.TAG, ex.Message);
            }
        }

    > [AZURE.NOTE] I ovenstående **OnRegistered()** kode, skal du notere muligheden for at angive mærker til at registrere for bestemte SMS kanaler.

10. Tilsidesæt metoden **OnMessage** i **PushHandlerService** ved hjælp af følgende kode:

        protected override void OnMessage(Context context, Intent intent)
        {
            Log.Info(MyBroadcastReceiver.TAG, "GCM Message Received!");

            var msg = new StringBuilder();

            if (intent != null && intent.Extras != null)
            {
                foreach (var key in intent.Extras.KeySet())
                    msg.AppendLine(key + "=" + intent.Extras.Get(key).ToString());
            }

            string messageText = intent.Extras.GetString("message");
            if (!string.IsNullOrEmpty (messageText))
            {
                createNotification ("New hub message!", messageText);
            }
            else
            {
                createNotification ("Unknown message details", msg.ToString ());
            }
        }

11. Tilføj følgende **createNotification** og **dialogNotify** metoder til **PushHandlerService** for underrette brugerne, når der modtages en meddelelse.

    >[AZURE.NOTE] Meddelelse om design i Android version 5.0 og nyere repræsenterer en betydeligt afgange fra som i tidligere versioner. Hvis du tester dette på Android 5.0 eller nyere, skal app til at køre for at modtage meddelelsen. Du kan finde yderligere oplysninger finder [Android beskeder](http://go.microsoft.com/fwlink/?LinkId=615880).

        void createNotification(string title, string desc)
        {
            //Create notification
            var notificationManager = GetSystemService(Context.NotificationService) as NotificationManager;

            //Create an intent to show UI
            var uiIntent = new Intent(this, typeof(MainActivity));

            //Create the notification
            var notification = new Notification(Android.Resource.Drawable.SymActionEmail, title);

            //Auto-cancel will remove the notification once the user touches it
            notification.Flags = NotificationFlags.AutoCancel;

            //Set the notification info
            //we use the pending intent, passing our ui intent over, which will get called
            //when the notification is tapped.
            notification.SetLatestEventInfo(this, title, desc, PendingIntent.GetActivity(this, 0, uiIntent, 0));

            //Show the notification
            notificationManager.Notify(1, notification);
            dialogNotify (title, desc);
        }

        protected void dialogNotify(String title, String message)
        {

            MainActivity.instance.RunOnUiThread(() => {
                AlertDialog.Builder dlg = new AlertDialog.Builder(MainActivity.instance);
                AlertDialog alert = dlg.Create();
                alert.SetTitle(title);
                alert.SetButton("Ok", delegate {
                    alert.Dismiss();
                });
                alert.SetMessage(message);
                alert.Show();
            });
        }


12. Tilsidesætte abstrakte medlemmer **OnUnRegistered()**, **OnRecoverableError()**og **OnError()** , så din kode kompilerer:

        protected override void OnUnRegistered(Context context, string registrationId)
        {
            Log.Verbose(MyBroadcastReceiver.TAG, "GCM Unregistered: " + registrationId);

            createNotification("GCM Unregistered...", "The device has been unregistered!");
        }

        protected override bool OnRecoverableError(Context context, string errorId)
        {
            Log.Warn(MyBroadcastReceiver.TAG, "Recoverable Error: " + errorId);

            return base.OnRecoverableError (context, errorId);
        }

        protected override void OnError(Context context, string errorId)
        {
            Log.Error(MyBroadcastReceiver.TAG, "GCM Error: " + errorId);
        }


##<a name="run-your-app-in-the-emulator"></a>Køre din app i emulatoren

Hvis du kører denne app i emulatoren, skal du kontrollere, at du bruger en Android virtuelle enhed (AVD), der understøtter Google APIs.

> [AZURE.IMPORTANT] For at modtage pushmeddelelser, skal du konfigurere en Google-konto på din Android virtuelle enhed. (Gå til **Indstillinger** i emulatoren, og klik på **Tilføj konto**.) Kontroller også, at emulatoren har forbindelse til internettet.

>[AZURE.NOTE] Meddelelse om design i Android version 5.0 og nyere repræsenterer en betydeligt afgange fra som i tidligere versioner. Du kan finde yderligere oplysninger finder [Android beskeder](http://go.microsoft.com/fwlink/?LinkId=615880).


1. Klik på **Åbn Android Emulator Manager** **værktøjer**og, Vælg din enhed, og klik derefter på **Rediger**.

    ![][18]

2. Vælg **Google API'er** i **destination**, og klik derefter på **OK**.

    ![][19]

3. Klik på **Kør**på den øverste værktøjslinje, og vælg derefter din app. Dette starter emulatoren og kører appen.

  Appen henter *registrationId* fra GCM og registrerer med besked om hub.

##<a name="send-notifications-from-your-backend"></a>Sende meddelelser fra din backend-version


Du kan teste modtage beskeder i din app ved at sende meddelelser i [Azure klassisk Portal] via fanen fejlfinding på hubben meddelelse, som vist i skærmbilledet nedenfor.

![][30]


Pushmeddelelser sendes normalt i en back end-tjeneste som Mobile-tjenester eller ASP.NET via et kompatible bibliotek. Du kan også bruge REST-API direkte til at sende meddelelser, hvis et bibliotek ikke er tilgængelig for din backend-version.

Her er en liste over nogle andre selvstudier, som du muligvis vil gennemgå til afsendelse af meddelelser:

- ASP.NET: Se [Brug meddelelse Hubs til pushmeddelelser til brugere].
- Azure meddelelse Hubs Java SDK: se, [hvordan du bruger meddelelse Hubs fra Java](notification-hubs-java-push-notification-tutorial.md) for at sende beskeder fra Java. Dette er testet i Eklipse for Android udvikling.
- PHP: Se, [hvordan du bruger meddelelse Hubs fra PHP](notification-hubs-php-push-notification-tutorial.md).


I de næste underafsnit af selvstudiet, kan du sende beskeder ved hjælp af en .NET console-app, og ved hjælp af en mobil tjeneste via en node script.

####<a name="optional-send-notifications-by-using-a-net-app"></a>(Valgfrit) Sende meddelelser ved hjælp af en .NET-app

I dette afsnit skal sender vi meddelelser om ved hjælp af en .NET console-app

1. Oprette et nyt Visual C# console program:

    ![][20]

2. Klik på **Funktioner**i Visual Studio, skal du klikke på **NuGet Package Manager**, og klik **Pakke Manager-konsollen**.

    Dette viser pakke Manager-konsollen i Visual Studio.

3. Angiv den **standard project** til din nye console programmet projekt i vinduet pakke Manager-konsollen, og derefter i vinduet console skal du udføre følgende kommando:

        Install-Package Microsoft.Azure.NotificationHubs

    Dette indsætter en reference til Azure meddelelse Hubs SDK ved hjælp af <a href="http://www.nuget.org/packages/Microsoft.Azure.NotificationHubs/">Microsoft.Azure.Notification Hubs NuGet pakke</a>.

    ![](./media/notification-hubs-windows-store-dotnet-get-started/notification-hub-package-manager.png)

4. Åbn filen Program.cs og tilføje følgende `using` sætning:

        using Microsoft.Azure.NotificationHubs;

5. I din `Program` klasse, skal du tilføje følgende metode. Opdater pladsholderteksten med din *DefaultFullSharedAccessSignature* streng og hub forbindelsesnavn fra [Azure klassisk Portal].

        private static async void SendNotificationAsync()
        {
            NotificationHubClient hub = NotificationHubClient.CreateClientFromConnectionString("<connection string with full access>", "<hub name>");
            await hub.SendGcmNativeNotificationAsync("{ \"data\" : {\"message\":\"Hello from Azure!\"}}");
        }

6. Tilføj følgende linjer i din **Main** metode:

         SendNotificationAsync();
         Console.ReadLine();

7. Tryk på F5 for at køre appen. Du modtager en meddelelse i appen.

    ![][21]

####<a name="optional-send-notifications-by-using-a-mobile-service"></a>(Valgfrit) Sende meddelelser ved hjælp af en mobile service

1. Følg [komme i gang med Mobile-tjenester].

1. Log på [Klassisk Azure-portalen], og vælg din mobile tjeneste.

2. Vælg fanen **Scheduler** øverst.

    ![][22]

3. Oprette en ny planlagte jobbet, indsætte et navn, og vælg **efter behov**.

    ![][23]

4. Når jobbet oprettes, skal du klikke på navnet på jobbet. Klik derefter på fanen **Script** på linjen øverst.

5. Indsæt følgende script i din scheduler-funktionen. Sørg for at erstatte pladsholderne med din anmeldelse hub navn og forbindelsesstrengen til *DefaultFullSharedAccessSignature* , som du tidligere har hentet. Klik på **Gem**.

        var azure = require('azure');
        var notificationHubService = azure.createNotificationHubService('<hub name>', '<connection string>');
        notificationHubService.gcm.send(null,'{"data":{"message" : "Hello from Mobile Services!"}}',
          function (error)
          {
            if (!error) {
               console.warn("Notification successful");
            }
            else
            {
              console.warn("Notification failed" + error);
            }
          }
        );

6. Klik på **Kør én gang** i den nederste linje. Du modtager en toastbeskeden.

##<a name="next-steps"></a>Næste trin

I denne enkle eksempel udsendes du meddelelser til alle dine Android-enheder. Se selvstudium [Brug meddelelse Hubs til pushmeddelelser til brugere]for at målrette bestemte brugere. Hvis du vil inddele dine brugere ved interesse grupper, kan du læse [Brug meddelelse Hubs sende seneste nyheder]. Lær mere om at bruge meddelelse Hubs i [Meddelelse Hubs vejledning] og i den [Meddelelse Hubs vejledning til Android].

<!-- Anchors. -->
[Enable Google Cloud Messaging]: #register
[Configure your Notification Hub]: #configure-hub
[Connecting your app to the Notification Hub]: #connecting-app
[Run your app with the emulator]: #run-app
[Send notifications from your back-end]: #send
[Next steps]:#next-steps

<!-- Images. -->

[11]: ./media/partner-xamarin-notification-hubs-android-get-started/notification-hub-configure-android.png

[13]: ./media/partner-xamarin-notification-hubs-android-get-started/notification-hub-create-xamarin-android-app1.png
[15]: ./media/partner-xamarin-notification-hubs-android-get-started/notification-hub-create-xamarin-android-app3.png

[18]: ./media/partner-xamarin-notification-hubs-android-get-started/notification-hub-create-android-app7.png
[19]: ./media/partner-xamarin-notification-hubs-android-get-started/notification-hub-create-android-app8.png

[20]: ./media/partner-xamarin-notification-hubs-android-get-started/notification-hub-create-console-app.png
[21]: ./media/partner-xamarin-notification-hubs-android-get-started/notification-hub-android-toast.png
[22]: ./media/partner-xamarin-notification-hubs-android-get-started/notification-hub-scheduler1.png
[23]: ./media/partner-xamarin-notification-hubs-android-get-started/notification-hub-scheduler2.png

[30]: ./media/partner-xamarin-notification-hubs-android-get-started/notification-hubs-debug-hub-gcm.png


<!-- URLs. -->
[Submit an app page]: http://go.microsoft.com/fwlink/p/?LinkID=266582
[My Applications]: http://go.microsoft.com/fwlink/p/?LinkId=262039
[Live SDK for Windows]: http://go.microsoft.com/fwlink/p/?LinkId=262253
[Introduktion til Mobile-tjenester]: /develop/mobile/tutorials/get-started-xamarin-android/#create-new-service
[JavaScript and HTML]: /develop/mobile/tutorials/get-started-with-push-js

[Azure klassisk Portal]: https://manage.windowsazure.com/
[wns object]: http://go.microsoft.com/fwlink/p/?LinkId=260591
[Meddelelse om Hubs vejledning]: http://msdn.microsoft.com/library/jj927170.aspx
[Meddelelse om Hubs trinvise vejledninger til Android]: http://msdn.microsoft.com/library/dn282661.aspx

[Bruge meddelelse Hubs til pushmeddelelser til brugere]: /manage/services/notification-hubs/notify-users-aspnet
[Brug meddelelse Hubs til at sende seneste nyheder]: /manage/services/notification-hubs/breaking-news-dotnet
[GCMClient Component page]: http://components.xamarin.com/view/GCMClient
[Xamarin.NotificationHub GitHub page]: https://github.com/SaschaDittmann/Xamarin.NotificationHub
[GitHub]: http://go.microsoft.com/fwlink/p/?LinkId=331329
[Google Cloud Messaging klient-komponent]: http://components.xamarin.com/view/GCMClient/
[Azure Messaging komponent]: http://components.xamarin.com/view/azure-messaging
