<properties
    pageTitle="Introduktion til Azure Mobile aftale for Xamarin.iOS"
    description="Lær at bruge Azure Mobile aftale med analyser og Push-beskeder til Xamarin.iOS Apps."
    services="mobile-engagement"
    documentationCenter="xamarin"
    authors="piyushjo"
    manager="erikre"
    editor="" />

<tags
    ms.service="mobile-engagement"
    ms.workload="mobile"
    ms.tgt_pltfrm="mobile-xamarin-ios"
    ms.devlang="dotnet"
    ms.topic="hero-article"
    ms.date="08/19/2016"
    ms.author="piyushjo" />

# <a name="get-started-with-azure-mobile-engagement-for-xamarinios-apps"></a>Introduktion til Azure Mobile aftale til Xamarin.iOS Apps

[AZURE.INCLUDE [Hero tutorial switcher](../../includes/mobile-engagement-hero-tutorial-switcher.md)]

Dette emne beskrives, hvordan du bruger Azure Mobile aftale til at forstå din appforbrug og sende pushmeddelelser til segmenteret brugere i et Xamarin.iOS til computeren.
I dette selvstudium, kan du oprette en tom Xamarin.iOS-app, som indsamler grundlæggende data og modtager pushmeddelelser ved hjælp af Apple Push meddelelse System (APNS).

Dette selvstudium kræver følgende:

+ [Xamarin Studio](http://xamarin.com/studio). Du kan også bruge Visual Studio med Xamarin, men dette selvstudium bruger Xamarin Studio. Se [konfigurere og installere til Visual Studio og Xamarin](https://msdn.microsoft.com/library/mt613162.aspx)installationsvejledning. 
+ [Mobile aftale Xamarin SDK](https://www.nuget.org/packages/Microsoft.Azure.Engagement.Xamarin/)

> [AZURE.NOTE] For at fuldføre dette selvstudium skal skal du have en active Azure-konto. Hvis du ikke har en konto, kan du oprette en gratis prøveversion konto på blot et par minutter. Yderligere oplysninger finder du [Azure gratis prøveversion](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Fen-us%2Fdocumentation%2Farticles%2Fmobile-engagement-xamarin-ios-get-started).

##<a id="setup-azme"></a>Konfiguration af Mobile aftale til din iOS-app

[AZURE.INCLUDE [Create Mobile Engagement App in Portal](../../includes/mobile-engagement-create-app-in-portal-new.md)]

##<a id="connecting-app"></a>Oprette forbindelse din app til Mobile aftale back-end

Dette selvstudium indeholder en "grundlæggende integration" som er den minimale angive påkrævet for at indsamle data og sende en meddelelse om opslagsnål.

Vi vil oprette en grundlæggende app med Xamarin til at vise integration:

###<a name="create-a-new-xamarinios-project"></a>Oprette et nyt Xamarin.iOS projekt

1. Start Xamarin Studio. Gå til **filer** -> **nye** -> **løsning** 

    ![][1]

2. Vælg **Enkelt visning App**, Sørg for det valgte sprog er **C#** , og klik derefter på **Næste**.

    ![][2]

3. Udfyld **App-navn** og **Organisationens id** , og klik derefter på **Næste**. 

    ![][3]

    > [AZURE.IMPORTANT] Sørg for, at profilen publicering du efterhånden bruge til at installere din iOS-app bruger en App-ID, der svarer nøjagtigt til det samlet-id, du har her. 

4. Opdater **Projektnavn**, **Løsningsnavn** og **placering** , hvis det er nødvendigt, og klik på **Opret**.

    ![][4]
 
Xamarin Studio opretter appen demo, hvori integrerer vi Mobile aftale. 

###<a name="connect-your-app-to-mobile-engagement-backend"></a>Oprette forbindelse din app til Mobile aftale backend-version

1. Højreklik på mappen **pakker** i vinduerne løsning og vælg **Tilføj pakker**

    ![][5]

2. Søg efter **Microsoft Azure Mobile aftale Xamarin SDK** og føje det til din løsning.  

    ![][6]
   
3. Åbn **AppDelegate.cs** , og Tilføj følgende ved hjælp af sætning:

        using Microsoft.Azure.Engagement.Xamarin;

4. Tilføj følgende for at initialiseret forbindelse med Mobile aftale back end-metoden **FinishedLaunching** . Sørg for at tilføje din **ConnectionString**. Denne kode bruges også en dummy **NotificationIcon** som tilføjes af i Mobile aftale SDK, som du vil erstatte. 

        EngagementConfiguration config = new EngagementConfiguration {
                        ConnectionString = "YourConnectionStringFromAzurePortal",
                        NotificationIcon = UIImage.FromBundle("close")
                    };
        EngagementAgent.Init (config);

##<a id="monitor"></a>Aktivere overvågning i realtid

For at begynde at sende data og sikre, at brugerne er aktiv, skal du sende mindst ét skærmbillede til Mobile aftale back-end.

1. Åbn **ViewController.cs** , og Tilføj følgende ved hjælp af sætning:

        using Microsoft.Azure.Engagement.Xamarin;

2. Erstat klassen, `ViewController` nedarver fra `UIViewController` til `EngagementViewController`. 

##<a id="monitor"></a>Oprette forbindelse app med overvågning i realtid

[AZURE.INCLUDE [Connect app with real-time monitoring](../../includes/mobile-engagement-connect-app-with-monitor.md)]

##<a id="integrate-push"></a>Aktivér pushmeddelelser og i app-beskeder

Mobile aftale gør det muligt at interagere med dine brugere og Tag med pushmeddelelser og app-beskeder i forbindelse med kampagner. Dette modul kaldes rækkevidde på portalen Mobile aftale.
De følgende afsnit konfigurere din app til at modtage dem.

### <a name="modify-your-application-delegate"></a>Ændre dit program stedfortræder

1. Åbn **AppDelegate.cs** , og Tilføj følgende ved hjælp af sætning:

        using System; 

2. Nu i den `FinishedLaunching` metode kan du tilføje følgende til at tilmelde dig opslagsnål meddelelser efter`EngagementAgent.init(...)`

        if (UIDevice.CurrentDevice.CheckSystemVersion(8,0))
        {
            var pushSettings = UIUserNotificationSettings.GetSettingsForTypes (
                (UIUserNotificationType.Badge |
                    UIUserNotificationType.Sound |
                    UIUserNotificationType.Alert),
                null);
            UIApplication.SharedApplication.RegisterUserNotificationSettings (pushSettings);
            UIApplication.SharedApplication.RegisterForRemoteNotifications ();
        }
        else
        {
            UIApplication.SharedApplication.RegisterForRemoteNotificationTypes (
                UIRemoteNotificationType.Badge |
                UIRemoteNotificationType.Sound |
                UIRemoteNotificationType.Alert);
        }

3. Til sidst - opdatere eller tilføje følgende metoder:

        public override void DidReceiveRemoteNotification (UIApplication application, NSDictionary userInfo, 
            Action<UIBackgroundFetchResult> completionHandler)
        {
            EngagementAgent.ApplicationDidReceiveRemoteNotification(userInfo, completionHandler);
        }

        public override void RegisteredForRemoteNotifications (UIApplication application, NSData deviceToken)
        {
            // Register device token on Engagement
            EngagementAgent.RegisterDeviceToken(deviceToken);
        }

        public override void FailedToRegisterForRemoteNotifications(UIApplication application, NSError error)
        {
            Console.WriteLine("Failed to register for remote notifications: Error '{0}'", error);
        }

4. Kontrollér, **Samlet id** stemmer overens med **App-ID** , du har i din klargøring profil i Apple-Udviklercenter i filen **Info.plist** i løsningen. 

    ![][7]

5. Sørg for, at du har tjekket **Aktivere baggrund tilstande** og **Eksterne meddelelser**i den samme fil i **Info.plist** . 

    ![][8]

6. Køre appen på den enhed, du har knyttet til denne publicering profil. 

[AZURE.INCLUDE [mobile-engagement-ios-send-push-push](../../includes/mobile-engagement-ios-send-push.md)]

<!-- Images. -->
[1]: ./media/mobile-engagement-xamarin-ios-get-started/new-solution.png
[2]: ./media/mobile-engagement-xamarin-ios-get-started/app-type.png
[3]: ./media/mobile-engagement-xamarin-ios-get-started/configure-project-name.png
[4]: ./media/mobile-engagement-xamarin-ios-get-started/configure-project-confirm.png
[5]: ./media/mobile-engagement-xamarin-ios-get-started/add-nuget.png
[6]: ./media/mobile-engagement-xamarin-ios-get-started/add-nuget-azme.png
[7]: ./media/mobile-engagement-xamarin-ios-get-started/info-plist-confirm-bundle.png
[8]: ./media/mobile-engagement-xamarin-ios-get-started/info-plist-configure-push.png
