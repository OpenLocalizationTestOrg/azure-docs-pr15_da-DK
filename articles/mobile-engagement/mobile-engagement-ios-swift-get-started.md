<properties
    pageTitle="Introduktion til Azure Mobile aftale til iOS i Swift | Microsoft Azure"
    description="Lær at bruge Azure Mobile aftale med analyser og Push-beskeder til iOS Apps."
    services="mobile-engagement"
    documentationCenter="mobile"
    authors="piyushjo"
    manager="erikre"
    editor="" />

<tags
    ms.service="mobile-engagement"
    ms.workload="mobile"
    ms.tgt_pltfrm="mobile-ios"
    ms.devlang="swift"
    ms.topic="hero-article"
    ms.date="09/20/2016"
    ms.author="piyushjo" />

# <a name="get-started-with-azure-mobile-engagement-for-ios-apps-in-swift"></a>Introduktion til Azure Mobile aftale til iOS-Apps i Swift

[AZURE.INCLUDE [Hero tutorial switcher](../../includes/mobile-engagement-hero-tutorial-switcher.md)]

Dette emne beskrives, hvordan du bruger Azure Mobile aftale til at forstå din appforbrug og sende pushmeddelelser til segmenteret brugere til iOS programmet.
I dette selvstudium, kan du oprette en tom iOS-app, der indsamler grundlæggende data og modtager pushmeddelelser ved hjælp af Apple Push meddelelse System (APNS).

Dette selvstudium kræver følgende:

+ XCode 8, som du kan installere fra din MAC App Store
+ [Mobile aftale iOS SDK]
+ Push-besked om certifikat (.p12), som du kan hente på din Apple-Udviklercenter

> [AZURE.NOTE] Dette selvstudium bruger Swift version 3.0. 

Fuldfør dette selvstudium er en betingelse for alle andre Mobile aftale selvstudier til iOS-apps.

> [AZURE.NOTE] For at fuldføre dette selvstudium skal skal du have en active Azure-konto. Hvis du ikke har en konto, kan du oprette en gratis prøveversion konto på blot et par minutter. Yderligere oplysninger finder du [Azure gratis prøveversion](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Fen-us%2Fdocumentation%2Farticles%2Fmobile-engagement-ios-swift-get-started).

##<a id="setup-azme"></a>Konfiguration af Mobile aftale til din iOS-app

[AZURE.INCLUDE [Create Mobile Engagement App in Portal](../../includes/mobile-engagement-create-app-in-portal-new.md)]

##<a id="connecting-app"></a>Oprette forbindelse din app til Mobile aftale back-end

Dette selvstudium indeholder en "grundlæggende integration", som er sættet minimale, der kræves for at indsamle data og sende en meddelelse om opslagsnål. Fuldstændig integration dokumentation kan findes i [Mobile aftale iOS SDK integration](mobile-engagement-ios-sdk-overview.md)

Vi vil oprette en grundlæggende app med XCode til at vise integration:

###<a name="create-a-new-ios-project"></a>Oprette et nyt projekt i iOS

[AZURE.INCLUDE [Create a new iOS Project](../../includes/mobile-engagement-create-new-ios-app.md)]

###<a name="connect-your-app-to-mobile-engagement-backend"></a>Oprette forbindelse din app til Mobile aftale backend-version

1. Hente [Mobile aftale iOS SDK]
2. Udtrække de. tar.gz fil til en mappe på computeren
3. Højreklik på projektet, og vælg "Føje filer til..."

    ![][1]

4. Naviger til den mappe, hvor du har pakket den SDK, og vælg den `EngagementSDK` mappe skal du trykke på OK.

    ![][2]

5. Åbn den `Build Phases` fanen og i den `Link Binary With Libraries` menuen Tilføj strukturer, som vist nedenfor:

    ![][3]

8. Oprette et broer sidehoved for at kunne bruge i SDK målsætning C API'er ved at vælge Filer > Ny > Filer > iOS > kilde > Sidehoved fil.

    ![][4]

9. Rediger filen broer sidehoved fremvise Mobile aftale mål-C-kode til din hurtig kode, skal du tilføje følgende importen:

        /* Mobile Engagement Agent */
        #import "AEModule.h"
        #import "AEPushMessage.h"
        #import "AEStorage.h"
        #import "EngagementAgent.h"
        #import "EngagementTableViewController.h"
        #import "EngagementViewController.h"
        #import "AEUserNotificationHandler.h"
        #import "AEIdfaProvider.h"

10. Sørg for mål-C broer sidehoved Opret konfiguration under Swift compileren - oprettelse af kode har en sti til denne overskrift under opbygge indstillinger. Her er et eksempel på stien: **$(SRCROOT)/MySuperApp/MySuperApp-Bridging-Header.h (afhængigt af stien)**

    ![][6]

11. Gå tilbage til portalen Azure i din app *Forbindelsesoplysninger* side og kopiere forbindelsesstrengen

    ![][5]

12. Nu indsætte forbindelsesstrengen i den `didFinishLaunchingWithOptions` stedfortræder

        func application(_ application: UIApplication, didFinishLaunchingWithOptions launchOptions: [UIApplicationLaunchOptionsKey: Any]?) -> Bool
        {
            [...]
                EngagementAgent.init("Endpoint={YOUR_APP_COLLECTION.DOMAIN};SdkKey={YOUR_SDK_KEY};AppId={YOUR_APPID}")
            [...]
        }

##<a id="monitor"></a>Aktivere overvågning i realtid

For at begynde at sende data og sikre, at brugerne er aktiv, skal du sende mindst ét skærmbillede (aktivitet) til Mobile aftale back-end.

1. Åbn filen **ViewController.swift** og erstatte den grundlæggende klasse af **ViewController** skal være **EngagementViewController**:

    `class ViewController : EngagementViewController {`

##<a id="monitor"></a>Oprette forbindelse app med overvågning i realtid

[AZURE.INCLUDE [Connect app with real-time monitoring](../../includes/mobile-engagement-connect-app-with-monitor.md)]

##<a id="integrate-push"></a>Aktivere Push-beskeder og i app-beskeder

Mobile aftale kan du interagere og Tag med dine brugere med Push-beskeder og i app'en meddelelser i forbindelse med kampagner. Dette modul kaldes rækkevidde på portalen Mobile aftale.
De følgende afsnit kan konfigurere din app for at hente dem.

### <a name="enable-your-app-to-receive-silent-push-notifications"></a>Aktivere din app til at modtage uovervåget Push-beskeder

[AZURE.INCLUDE [mobile-engagement-ios-silent-push](../../includes/mobile-engagement-ios-silent-push.md)]

### <a name="add-the-reach-library-to-your-project"></a>Føje biblioteket rækkevidde til projektet

1. Højreklik på dit projekt
2. Vælg`Add file to ...`
3. Naviger til den mappe, hvor du udpakkede SDK
4. Vælg den `EngagementReach` mappe
5. Klik på Tilføj
6. Rediger filen broer sidehoved for at få vist Mobile aftale mål-C når sidehoveder og tilføje den følgende import:

        /* Mobile Engagement Reach */
        #import "AEAnnouncementViewController.h"
        #import "AEAutorotateView.h"
        #import "AEContentViewController.h"
        #import "AEDefaultAnnouncementViewController.h"
        #import "AEDefaultNotifier.h"
        #import "AEDefaultPollViewController.h"
        #import "AEInteractiveContent.h"
        #import "AENotificationView.h"
        #import "AENotifier.h"
        #import "AEPollViewController.h"
        #import "AEReachAbstractAnnouncement.h"
        #import "AEReachAnnouncement.h"
        #import "AEReachContent.h"
        #import "AEReachDataPush.h"
        #import "AEReachDataPushDelegate.h"
        #import "AEReachModule.h"
        #import "AEReachNotifAnnouncement.h"
        #import "AEReachPoll.h"
        #import "AEReachPollQuestion.h"
        #import "AEViewControllerUtil.h"
        #import "AEWebAnnouncementJsBridge.h"

### <a name="modify-your-application-delegate"></a>Ændre dit program stedfortræder

1. I den `didFinishLaunchingWithOptions` - Opret et rækkevidde modul og sende dem til din eksisterende aftale initialisering linje:

        func application(_ application: UIApplication, didFinishLaunchingWithOptions launchOptions: [UIApplicationLaunchOptionsKey: Any]?) -> Bool 
        {
            let reach = AEReachModule.module(withNotificationIcon: UIImage(named:"icon.png")) as! AEReachModule
            EngagementAgent.init("Endpoint={YOUR_APP_COLLECTION.DOMAIN};SdkKey={YOUR_SDK_KEY};AppId={YOUR_APPID}", modulesArray:[reach])
            [...]
            return true
        }

###<a name="enable-your-app-to-receive-apns-push-notifications"></a>Aktivere din app til at modtage APNS Push-beskeder
1. Tilføj følgende linje til den `didFinishLaunchingWithOptions` metode:

        if #available(iOS 8.0, *)
        {
            if #available(iOS 10.0, *)
            {
                UNUserNotificationCenter.current().requestAuthorization(options: [.alert, .badge, .sound]) { (granted, error) in }
            }else
            {
                let settings = UIUserNotificationSettings(types: [.alert, .badge, .sound], categories: nil)
                application.registerUserNotificationSettings(settings)
            }
            application.registerForRemoteNotifications()
        }
        else
        {
            application.registerForRemoteNotifications(matching: [.alert, .badge, .sound])
        }

2. Tilføje den `didRegisterForRemoteNotificationsWithDeviceToken` metode på følgende måde:

        func application(_ application: UIApplication, didRegisterForRemoteNotificationsWithDeviceToken deviceToken: Data) {
            EngagementAgent.shared().registerDeviceToken(deviceToken)
        }

3. Tilføje den `didReceiveRemoteNotification:fetchCompletionHandler:` metode på følgende måde:

        func application(_ application: UIApplication, didReceiveRemoteNotification userInfo: [AnyHashable : Any], fetchCompletionHandler completionHandler: @escaping (UIBackgroundFetchResult) -> Void) {
            EngagementAgent.shared().applicationDidReceiveRemoteNotification(userInfo, fetchCompletionHandler:completionHandler)
        }

[AZURE.INCLUDE [mobile-engagement-ios-send-push-push](../../includes/mobile-engagement-ios-send-push.md)]

<!-- URLs. -->
[Mobile aftale iOS SDK]: http://aka.ms/qk2rnj

<!-- Images. -->
[1]: ./media/mobile-engagement-ios-get-started/xcode-add-files.png
[2]: ./media/mobile-engagement-ios-get-started/xcode-select-engagement-sdk.png
[3]: ./media/mobile-engagement-ios-get-started/xcode-build-phases.png
[4]: ./media/mobile-engagement-ios-swift-get-started/add-header-file.png
[5]: ./media/mobile-engagement-ios-get-started/app-connection-info-page.png
[6]: ./media/mobile-engagement-ios-swift-get-started/add-bridging-header.png
