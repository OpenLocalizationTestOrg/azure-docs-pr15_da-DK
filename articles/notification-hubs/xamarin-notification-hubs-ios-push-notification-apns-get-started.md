<properties
    pageTitle="iOS Push-beskeder med meddelelse om Hubs til Xamarin apps | Microsoft Azure"
    description="I dette selvstudium lærer du at bruge Azure meddelelse Hubs til at sende pushmeddelelser til et Xamarin iOS-program."
    services="notification-hubs"
    keywords="IOS push-beskeder, opslagsnål meddelelser push-beskeder, push-meddelelse"
    documentationCenter="xamarin"
    authors="ysxu"
    manager="erikre"
    editor=""/>

<tags
    ms.service="notification-hubs"
    ms.workload="mobile"
    ms.tgt_pltfrm="mobile-xamarin-ios"
    ms.devlang="dotnet"
    ms.topic="hero-article"
    ms.date="06/29/2016"
    ms.author="yuaxu"/>

# <a name="ios-push-notifications-with-notification-hubs-for-xamarin-apps"></a>iOS Push-beskeder med meddelelse om Hubs til Xamarin apps

[AZURE.INCLUDE [notification-hubs-selector-get-started](../../includes/notification-hubs-selector-get-started.md)]

##<a name="overview"></a>Oversigt
> [AZURE.IMPORTANT] For at fuldføre dette selvstudium skal skal du have en active Azure-konto. Hvis du ikke har en konto, kan du oprette en gratis prøveversion konto på blot et par minutter. Yderligere oplysninger finder du [Azure gratis prøveversion](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A643EE910&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Fen-us%2Fdocumentation%2Farticles%2Fpartner-xamarin-notification-hubs-ios-get-started).

Dette selvstudium viser, hvordan du bruger Azure meddelelse Hubs til at sende pushmeddelelser til iOS programmet.
Du vil oprette en tom Xamarin.iOS-app, der modtager pushmeddelelser ved hjælp af [Apple-Pushmeddelelsestjeneste (APNs)](https://developer.apple.com/library/ios/documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/Chapters/ApplePushService.html). Når du er færdig, skal du kunne bruge din meddelelse hub transmittere pushmeddelelser til alle de enheder, der kører din app. Færdig koden er tilgængelig i [NotificationHubs app] [ GitHub] eksempel.

Dette selvstudium viser meddelelsen enkel push-broadcast scenariet med besked om Hubs.

##<a name="prerequisites"></a>Forudsætninger

Dette selvstudium kræver følgende:

+ [Xcode 6.0][Install Xcode]
+ En iOS 7.0 (eller nyere) kompatible enhed
+ iOS udvikler Program medlemskab
+ [Xamarin Studio]

   > [AZURE.NOTE] På grund af konfigurationskrav til iOS push-beskeder, skal du installere og teste eksempelprogrammet på en fysisk iOS-enhed (iPhone eller iPad) i stedet for i simulator.

Fuldfør dette selvstudium er en betingelse for alle andre besked om Hubs selvstudier til Xamarin iOS apps.


[AZURE.INCLUDE [Notification Hubs Enable Apple Push Notifications](../../includes/notification-hubs-enable-apple-push-notifications.md)]


##<a name="configure-your-notification-hub"></a>Konfigurere din meddelelse-hub

Dette afsnit fører dig gennem oprette en ny meddelelse hub og konfigurere godkendelse med APNS ved hjælp af **.p12** push-certifikat, du har oprettet. Hvis du vil bruge en meddelelse om-hub, du allerede har oprettet, kan du gå videre til trin 5.

[AZURE.INCLUDE [notification-hubs-portal-create-new-hub](../../includes/notification-hubs-portal-create-new-hub.md)]


<ol start="7">
<li>
<p>Som vi ønsker at konfigurere forbindelsen APNS i portalen Azure, Åbn indstillingerne for meddelelse-Hub, ande Klik på <b>Meddelelse tjenester</b>, og klik derefter på <b>Apple (APNS)</b> element på listen. Når færdig, klik på <b>Overfør certifikat</b> , og vælg det <b>.p12</b> certifikat, du har eksporteret tidligere, samt adgangskoden til certifikatet.</p>
<p>Sørg for at vælge <b>sandkassetilstand</b> , da du skal sende opslagsnål meddelelser i et udviklingsmiljø. Kun bruge indstillingen <b>fremstilling</b> , hvis du vil sende pushmeddelelser til brugere, som allerede har købt din app fra store.</p>
</li>
</ol>
&emsp;&emsp;![](./media/notification-hubs-ios-get-started/notification-hubs-apns.png)

&emsp;&emsp;![](./media/notification-hubs-ios-get-started/notification-hubs-sandbox.png)


Din meddelelse hub nu er konfigureret til at arbejde med APNS, og du har forbindelse strengene til at registrere din app og sende pushmeddelelser.


##<a name="connect-your-app-to-the-notification-hub"></a>Oprette forbindelse din app til hubben meddelelse

#### <a name="create-a-new-project"></a>Oprette et nyt projekt

1. Oprette et nyt projekt i iOS i Xamarin Studio, og vælg **Unified API** > **Enkelt visning programmet** skabelon.

    ![Xamarin Studio – Vælg programtype][31]

2. Tilføje en reference til komponenten Azure Messaging. Højreklik på mappen **komponenter** for dit projekt, og vælg **Få mere komponenter**i visningen løsning. Søge efter komponenten **Azure Messaging** og tilføje komponenten til projektet.

3. Tilføj følgende i **AppDelegate.cs**, ved hjælp af sætning:

        using WindowsAzure.Messaging;

4. Erklære en forekomst af **SBNotificationHub**:

        private SBNotificationHub Hub { get; set; }

5. Oprette en **Constants.cs** klasse med følgende variabler:

        // Azure app-specific connection string and hub path
        public const string ConnectionString = "<Azure connection string>";
        public const string NotificationHubPath = "<Azure hub path>";


6. I **AppDelegate.cs**, opdatere **FinishedLaunching()** , så det svarer til følgende:

        public override bool FinishedLaunching(UIApplication application, NSDictionary launchOptions)
        {
            if (UIDevice.CurrentDevice.CheckSystemVersion (8, 0)) {
                var pushSettings = UIUserNotificationSettings.GetSettingsForTypes (
                       UIUserNotificationType.Alert | UIUserNotificationType.Badge | UIUserNotificationType.Sound,
                       new NSSet ());

                UIApplication.SharedApplication.RegisterUserNotificationSettings (pushSettings);
                UIApplication.SharedApplication.RegisterForRemoteNotifications ();
            } else {
                UIRemoteNotificationType notificationTypes = UIRemoteNotificationType.Alert | UIRemoteNotificationType.Badge | UIRemoteNotificationType.Sound;
                UIApplication.SharedApplication.RegisterForRemoteNotificationTypes (notificationTypes);
            }

            return true;
        }

7. Tilsidesæt metoden **RegisteredForRemoteNotifications()** i **AppDelegate.cs**:

        public override void RegisteredForRemoteNotifications(UIApplication application, NSData deviceToken)
        {
            Hub = new SBNotificationHub(Constants.ConnectionString, Constants.NotificationHubPath);

            Hub.UnregisterAllAsync (deviceToken, (error) => {
                if (error != null)
                {
                    Console.WriteLine("Error calling Unregister: {0}", error.ToString());
                    return;
                }

                NSSet tags = null; // create tags if you want
                Hub.RegisterNativeAsync(deviceToken, tags, (errorCallback) => {
                    if (errorCallback != null)
                        Console.WriteLine("RegisterNativeAsync error: " + errorCallback.ToString());
                });
            });
        }

8. Tilsidesæt metoden **ReceivedRemoteNotification()** i **AppDelegate.cs**:

        public override void ReceivedRemoteNotification(UIApplication application, NSDictionary userInfo)
        {
            ProcessNotification(userInfo, false);
        }

9. Oprette følgende **ProcessNotification()** metode i **AppDelegate.cs**:

        void ProcessNotification(NSDictionary options, bool fromFinishedLaunching)
        {
            // Check to see if the dictionary has the aps key.  This is the notification payload you would have sent
            if (null != options && options.ContainsKey(new NSString("aps")))
            {
                //Get the aps dictionary
                NSDictionary aps = options.ObjectForKey(new NSString("aps")) as NSDictionary;

                string alert = string.Empty;

                //Extract the alert text
                // NOTE: If you're using the simple alert by just specifying
                // "  aps:{alert:"alert msg here"}  ", this will work fine.
                // But if you're using a complex alert with Localization keys, etc.,
                // your "alert" object from the aps dictionary will be another NSDictionary.
                // Basically the JSON gets dumped right into a NSDictionary,
                // so keep that in mind.
                if (aps.ContainsKey(new NSString("alert")))
                    alert = (aps [new NSString("alert")] as NSString).ToString();

                //If this came from the ReceivedRemoteNotification while the app was running,
                // we of course need to manually process things like the sound, badge, and alert.
                if (!fromFinishedLaunching)
                {
                    //Manually show an alert
                    if (!string.IsNullOrEmpty(alert))
                    {
                        UIAlertView avAlert = new UIAlertView("Notification", alert, null, "OK", null);
                        avAlert.Show();
                    }
                }
            }
        }

    > [AZURE.NOTE] Du kan vælge at tilsidesætte **FailedToRegisterForRemoteNotifications()** for at håndtere situationer som ingen netværksforbindelse. Dette er især vigtigt, hvor brugeren kan starte dit program i offlinetilstand (fx fly), og du vil håndtere push-beskeder scenarier, der er specifikke for din app.


10. Køre appen på din enhed.


## <a name="sending-push-notifications"></a>Afsendelse af Pushmeddelelser


Du kan teste modtager pushmeddelelser i din app ved at sende meddelelser i [Azure Portal] via **Teste sende** egenskab i værktøjer, der **fejlfinding** , til højre på meddelelsessiden-hub, som vist i skærmbilledet nedenfor.

![](./media/notification-hubs-ios-get-started/notification-hubs-test-send.png)

Pushmeddelelser sendes normalt via en back end-tjeneste som Mobile-tjenester eller ASP.NET ved hjælp af et kompatibelt bibliotek. Du kan også bruge REST-API direkte til at sende opslagsnål meddelelser, hvis et bibliotek ikke er tilgængelig i scenariet. 

I dette selvstudium skal vi Hold det enkelt og lige demonstrerer test din klient-app ved at sende meddelelser ved hjælp af .NET SDK for meddelelse hubs i et console-program i stedet for en back end-tjeneste. Vi anbefaler selvstudiet [Brug meddelelse Hubs til pushmeddelelser til brugere](notification-hubs-aspnet-backend-ios-apple-apns-notification.md) , som det næste trin for at sende meddelelser fra en ASP.NET backend-version. Dog kan benytte følgende fremgangsmåde bruges til at sende meddelelser:

* **RESTEN Interface**: Du kan understøtte opslagsnål meddelelse på en hvilken som helst back end-platform ved hjælp af [RESTEN interface](http://msdn.microsoft.com/library/windowsazure/dn223264.aspx).

* **Microsoft Azure meddelelse Hubs .NET SDK**: I feltet Nuget pakke Manager til Visual Studio, skal du køre [Installer-pakke Microsoft.Azure.NotificationHubs](https://www.nuget.org/packages/Microsoft.Azure.NotificationHubs/).

* **Node.js** : [Sådan bruger du besked om Hubs fra Node.js](notification-hubs-nodejs-push-notification-tutorial.md).

**Mobile-Apps**: se et eksempel på, hvordan du sender meddelelser fra en back-end til Azure App Service Mobile-Apps, der er integreret med besked om Hubs under [Tilføj pushmeddelelser til din mobile-app](../app-service-mobile/app-service-mobile-ios-get-started-push.md).

* **Java / PHP**: et eksempel på hvordan du kan sende pushmeddelelser ved hjælp af REST API'er, se "Sådan bruger du besked om Hubs fra Java/PHP" ([Java](notification-hubs-java-push-notification-tutorial.md) | [PHP](notification-hubs-php-push-notification-tutorial.md)).


####<a name="optional-send-push-notifications-from-a-net-console-app"></a>(Valgfrit) Sende Pushmeddelelser fra en .NET Console-App

I dette afsnit sender vi pushmeddelelser ved hjælp af en simpel .NET console app. Med henblik på dette eksempel skifter vi til en Windows-udviklingsmiljø, der har Visual Studio, der allerede er installeret.

1. Oprette en ny Visual C# console-program i Visual Studio:

    ![Visual Studio - Opret et nyt console-program][213]

2. Klik på **Funktioner**i Visual Studio, skal du klikke på **NuGet Package Manager**, og klik **Pakke Manager-konsollen**.

    Pakke manager-konsollen bør fremgå forankret til bunden af arbejdsområdet Visual Studio.

3. Angiv den **standard project** til din nye console programmet projekt i vinduet pakke Manager-konsollen, og derefter i vinduet console skal du udføre følgende kommando:

        Install-Package Microsoft.Azure.NotificationHubs

    Dette indsætter en reference til Azure meddelelse Hubs SDK ved hjælp af <a href="http://www.nuget.org/packages/Microsoft.Azure.NotificationHubs/">Microsoft.Azure.Notification Hubs NuGet pakke</a>.

    ![](./media/notification-hubs-windows-store-dotnet-get-started/notification-hub-package-manager.png)


4. Åbn den `Program.cs` filen, og Tilføj følgende `using` -sætning, at sikre, at vi kan bruge funktionerne i din primære klasse og Azure klasser:

        using Microsoft.Azure.NotificationHubs;

3. I din `Program` klasse, skal du tilføje følgende metode (Husk at erstatte **forbindelsesstreng** og **hub navn**):

        private static async void SendNotificationAsync()
        {
            NotificationHubClient hub = NotificationHubClient.CreateClientFromConnectionString("<connection string with full access>", "<hub name>");
            var alert = "{\"aps\":{\"alert\":\"Hello from .NET!\"}}";
            await hub.SendAppleNativeNotificationAsync(alert);
        }

4. Tilføj følgende linjer i din `Main` metode:

         SendNotificationAsync();
         Console.ReadLine();

5. Tryk på F5 for at køre appen. Inden for sekunder, skal du se en push-meddelelse, der vises på enheden. Om du bruger Wi-Fi- eller et mobildatanetværk, Sørg for, at du har en aktiv internetforbindelse på enheden.

Du kan finde alle mulige overførsler i Apple [Push meddelelse Programming vejledning og lokale].

####<a name="optional-send-notifications-from-a-mobile-service"></a>(Valgfrit) Sende meddelelser fra en Mobile Service

I dette afsnit sender vi pushmeddelelser ved hjælp af en mobil tjeneste via en node script.

For at sende en meddelelse ved hjælp af en mobil tjeneste skal du følge [komme i gang med Mobile-tjenester], og derefter:

1. Log på [Klassisk Azure-portalen], og vælg din mobile tjeneste.

2. Vælg fanen **Scheduler** øverst.

    ![Azure klassisk Portal - Opgavestyring][215]

3. Oprette en ny planlagte jobbet, indsætte et navn, og vælg **efter behov**.

    ![Azure klassisk Portal - Opret nyt job][216]

4. Når jobbet oprettes, skal du klikke på navnet på jobbet. Klik derefter på fanen **Script** på linjen øverst.

5. Indsæt følgende script i din scheduler-funktionen. Sørg for at erstatte pladsholderne med din anmeldelse hub navn og forbindelsesstrengen til *DefaultFullSharedAccessSignature* , som du tidligere har hentet. Klik på **Gem**.

        var azure = require('azure');
        var notificationHubService = azure.createNotificationHubService('<Hubname>', '<SAS Full access >');
        notificationHubService.apns.send(
            null,
            {"aps":
                {
                "alert": "Hello from Mobile Services!"
                }
            },
            function (error)
            {
                if (!error) {
                    console.warn("Notification successful");
                }
            }
        );


6. Klik på **Kør én gang** i den nederste linje. Du modtager en besked på din enhed.

##<a name="next-steps"></a>Næste trin

I denne enkle eksempel udsendes du pushmeddelelser til alle dine iOS-enheder. Se selvstudium [Brug meddelelse Hubs til pushmeddelelser til brugere]for at målrette bestemte brugere. Hvis du vil inddele dine brugere ved interesse grupper, kan du læse [Brug meddelelse Hubs sende seneste nyheder]. Lær mere om at bruge meddelelse Hubs i [Meddelelse Hubs vejledning] og i den [Meddelelse Hubs vejledning til iOS].


<!-- Images. -->

[213]: ./media/partner-xamarin-notification-hubs-ios-get-started/notification-hub-create-console-app.png

[215]: ./media/partner-xamarin-notification-hubs-ios-get-started/notification-hub-scheduler1.png
[216]: ./media/partner-xamarin-notification-hubs-ios-get-started/notification-hub-scheduler2.png


[31]: ./media/partner-xamarin-notification-hubs-ios-get-started/notification-hub-create-ios-app.png




<!-- URLs. -->
[Mobile Services iOS SDK]: http://go.microsoft.com/fwLink/?LinkID=266533
[Submit an app page]: http://go.microsoft.com/fwlink/p/?LinkID=266582
[My Applications]: http://go.microsoft.com/fwlink/p/?LinkId=262039
[Live SDK for Windows]: http://go.microsoft.com/fwlink/p/?LinkId=262253

[Introduktion til Mobile-tjenester]: /develop/mobile/tutorials/get-started-xamarin-ios
[Azure klassisk Portal]: https://manage.windowsazure.com/
[Meddelelse om Hubs vejledning]: http://msdn.microsoft.com/library/jj927170.aspx
[Meddelelse om Hubs vejledning til iOS]: http://msdn.microsoft.com/library/jj927168.aspx
[Install Xcode]: https://go.microsoft.com/fwLink/p/?LinkID=266532
[iOS Provisioning Portal]: http://go.microsoft.com/fwlink/p/?LinkId=272456

[Bruge meddelelse Hubs til pushmeddelelser til brugere]: /manage/services/notification-hubs/notify-users-aspnet
[Brug meddelelse Hubs til at sende seneste nyheder]: /manage/services/notification-hubs/breaking-news-dotnet

[Lokale, og Skub besked om programmering vejledning]: http://developer.apple.com/library/mac/#documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/Chapters/ApplePushService.html#//apple_ref/doc/uid/TP40008194-CH100-SW1
[Apple Push Notification Service]: http://go.microsoft.com/fwlink/p/?LinkId=272584

[Azure Mobile Services Component]: http://components.xamarin.com/view/azure-mobile-services/
[GitHub]: http://go.microsoft.com/fwlink/p/?LinkId=331329
[Xamarin Studio]: http://xamarin.com/download
[WindowsAzure.Messaging]: https://github.com/infosupport/WindowsAzure.Messaging.iOS
[Azure-portalen]: https://portal.azure.com
