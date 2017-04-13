<properties
    pageTitle="Introduktion til Azure Mobile aftale til iOS i målsætning C | Microsoft Azure"
    description="Lær at bruge Azure Mobile aftale med analyser og push-beskeder til iOS-apps."
    services="mobile-engagement"
    documentationCenter="mobile"
    authors="piyushjo"
    manager="erikre"
    editor="" />

<tags
    ms.service="mobile-engagement"
    ms.workload="mobile"
    ms.tgt_pltfrm="mobile-ios"
    ms.devlang="objective-c"
    ms.topic="hero-article"
    ms.date="10/05/2016"
    ms.author="piyushjo" />

# <a name="get-started-with-azure-mobile-engagement-for-ios-apps-in-objective-c"></a>Introduktion til Azure Mobile aftale til iOS-apps i målsætning C

[AZURE.INCLUDE [Hero tutorial switcher](../../includes/mobile-engagement-hero-tutorial-switcher.md)]

Dette emne beskrives, hvordan du bruger Azure Mobile aftale til at forstå din appforbrug og sende pushmeddelelser til segmenteret brugere til iOS programmet.
I dette selvstudium, kan du oprette en tom iOS-app, der indsamler grundlæggende data og modtager pushmeddelelser ved hjælp af Apple Push meddelelse System (APNS).

Dette selvstudium kræver følgende:

+ XCode 8, som du kan installere fra din MAC App Store
+ [Mobile aftale iOS SDK]

Fuldfør dette selvstudium er en betingelse for alle andre Mobile aftale selvstudier til iOS-apps.

> [AZURE.NOTE] For at fuldføre dette selvstudium skal skal du have en active Azure-konto. Hvis du ikke har en konto, kan du oprette en gratis prøveversion konto på blot et par minutter. Yderligere oplysninger finder du [Azure gratis prøveversion](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Fen-us%2Fdocumentation%2Farticles%2Fmobile-engagement-ios-get-started).

##<a id="setup-azme"></a>Konfiguration af Mobile aftale til din iOS-app

[AZURE.INCLUDE [Create Mobile Engagement App in Portal](../../includes/mobile-engagement-create-app-in-portal-new.md)]

##<a id="connecting-app"></a>Oprette forbindelse din app til Mobile aftale back-end

Dette selvstudium indeholder en "grundlæggende integration", som er sættet minimale, der kræves for at indsamle data og sende en meddelelse om opslagsnål. Fuldstændig integration dokumentation kan findes i [Mobile aftale iOS SDK integration](mobile-engagement-ios-sdk-overview.md)

Vi vil oprette en grundlæggende app med XCode til at vise integrationen.

###<a name="create-a-new-ios-project"></a>Oprette et nyt projekt i iOS

[AZURE.INCLUDE [Create a new iOS Project](../../includes/mobile-engagement-create-new-ios-app.md)]

###<a name="connect-your-app-to-the-mobile-engagement-backend"></a>Oprette forbindelse din app til Mobile aftale back-end

1. Hent [Mobile aftale iOS SDK].
2. Udtrække de. tar.gz fil til en mappe på computeren.
3. Højreklik på projektet, og vælg derefter **Tilføj filer til**.

    ![][1]

4. Naviger til den mappe, hvor du udpakkede SDK, Vælg den `EngagementSDK` mappe, og derefter trykke på **OK**.

    ![][2]

5. Åbn fanen **Opbygge faser** , og Tilføj strukturer i menuen **Link binær med biblioteker** , som vist nedenfor:

    ![][3]

6. Gå tilbage til portalen Azure din app **Forbindelsesoplysninger** på siden, og Kopiér forbindelsesstrengen.

    ![][4]

7. Tilføj følgende linje af kode i filen **AppDelegate.m** .

        #import "EngagementAgent.h"

8. Nu indsætte forbindelsesstrengen i den `didFinishLaunchingWithOptions` uddelegere.

        - (BOOL)application:(UIApplication *)application didFinishLaunchingWithOptions:(NSDictionary *)launchOptions
        {
            [...]   
            [EngagementAgent init:@"Endpoint={YOUR_APP_COLLECTION.DOMAIN};SdkKey={YOUR_SDK_KEY};AppId={YOUR_APPID}"];
            [...]
        }

9. `setTestLogEnabled`er en valgfri erklæring, der gør det muligt for SDK logfiler for dig at identificere problemer. 

##<a id="monitor"></a>Aktivere overvågning i realtid

For at begynde at sende data og sikre, at brugerne er aktiv, skal du sende mindst ét skærmbillede (aktivitet) til Mobile aftale back-end.

1. Åbn filen **ViewController.h** og importere **EngagementViewController.h**:

    `# import "EngagementViewController.h"`

2. Erstat nu overklasse af grænsefladen **ViewController** ved `EngagementViewController`:

    `@interface ViewController : EngagementViewController`

##<a id="monitor"></a>Oprette forbindelse app med overvågning i realtid

[AZURE.INCLUDE [Connect app with real-time monitoring](../../includes/mobile-engagement-connect-app-with-monitor.md)]

##<a id="integrate-push"></a>Aktivér pushmeddelelser og i app-beskeder

Mobile aftale gør det muligt at interagere med dine brugere og Tag med pushmeddelelser og app-beskeder i forbindelse med kampagner. Dette modul kaldes rækkevidde på portalen Mobile aftale.
De følgende afsnit konfigurere din app til at modtage dem.

### <a name="enable-your-app-to-receive-silent-push-notifications"></a>Aktivere din app til at modtage uovervåget Push-beskeder

[AZURE.INCLUDE [mobile-engagement-ios-silent-push](../../includes/mobile-engagement-ios-silent-push.md)]  

### <a name="add-the-reach-library-to-your-project"></a>Føje biblioteket rækkevidde til projektet

1. Højreklik på dit projekt.
2. Vælg **Tilføj fil til**.
3. Gå til den mappe, hvor du udpakkede SDK.
4. Vælg den `EngagementReach` mappe.
5. Klik på **Tilføj**.

### <a name="modify-your-application-delegate"></a>Ændre dit program stedfortræder

1. Importere modulet aftale når tilbage i **AppDeletegate.m** fil.

        #import "AEReachModule.h"
        #import <UserNotifications/UserNotifications.h>

2. I den `application:didFinishLaunchingWithOptions` metode, oprette et modul, rækkevidde og sende dem til din eksisterende aftale initialisering linje:

        - (BOOL)application:(UIApplication *)application didFinishLaunchingWithOptions:(NSDictionary *)launchOptions {
            AEReachModule * reach = [AEReachModule moduleWithNotificationIcon:[UIImage imageNamed:@"icon.png"]];
            [EngagementAgent init:@"Endpoint={YOUR_APP_COLLECTION.DOMAIN};SdkKey={YOUR_SDK_KEY};AppId={YOUR_APPID}" modules:reach, nil];
            [...]
            return YES;
        }

###<a name="enable-your-app-to-receive-apns-push-notifications"></a>Aktivere din app til at modtage APNS Push-beskeder

1. Tilføj følgende linje til den `application:didFinishLaunchingWithOptions` metode:

        if (NSFoundationVersionNumber >= NSFoundationVersionNumber_iOS_8_0)
        {
            if (NSFoundationVersionNumber > NSFoundationVersionNumber_iOS_9_x_Max)
            {
                [UNUserNotificationCenter.currentNotificationCenter requestAuthorizationWithOptions:(UNAuthorizationOptionBadge | UNAuthorizationOptionSound | UNAuthorizationOptionAlert) completionHandler:^(BOOL granted, NSError * _Nullable error) {}];
            }else
            {
                [application registerUserNotificationSettings:[UIUserNotificationSettings settingsForTypes:(UIUserNotificationTypeBadge | UIUserNotificationTypeSound | UIUserNotificationTypeAlert)   categories:nil]];
            }
            [application registerForRemoteNotifications];
        }
        else
        {
            [application registerForRemoteNotificationTypes:(UIRemoteNotificationTypeBadge | UIRemoteNotificationTypeSound | UIRemoteNotificationTypeAlert)];
        }

2. Tilføje den `application:didRegisterForRemoteNotificationsWithDeviceToken` metode på følgende måde:

        - (void)application:(UIApplication *)application didRegisterForRemoteNotificationsWithDeviceToken:(NSData *)deviceToken
        {
            [[EngagementAgent shared] registerDeviceToken:deviceToken];
            NSLog(@"Registered Token: %@", deviceToken);
        }

3. Tilføje den `didFailToRegisterForRemoteNotificationsWithError` metode på følgende måde:

        - (void)application:(UIApplication*)application didFailToRegisterForRemoteNotificationsWithError:(NSError*)error
        {
           
           NSLog(@"Failed to get token, error: %@", error);
        }

4. Tilføje den `didReceiveRemoteNotification:fetchCompletionHandler` metode på følgende måde:

        - (void)application:(UIApplication *)application didReceiveRemoteNotification:(NSDictionary *)userInfo fetchCompletionHandler:(void (^)(UIBackgroundFetchResult result))handler
        {
            [[EngagementAgent shared] applicationDidReceiveRemoteNotification:userInfo fetchCompletionHandler:handler];
        }

[AZURE.INCLUDE [mobile-engagement-ios-send-push-push](../../includes/mobile-engagement-ios-send-push.md)]

<!-- URLs. -->
[Mobile aftale iOS SDK]: http://aka.ms/qk2rnj

<!-- Images. -->
[1]: ./media/mobile-engagement-ios-get-started/xcode-add-files.png
[2]: ./media/mobile-engagement-ios-get-started/xcode-select-engagement-sdk.png
[3]: ./media/mobile-engagement-ios-get-started/xcode-build-phases.png
[4]: ./media/mobile-engagement-ios-get-started/app-connection-info-page.png

