<properties
    pageTitle="Azure Mobile aftale iOS SDK oversigt | Microsoft Azure"
    description="Seneste opdateringer og procedurer til iOS SDK til Azure Mobile aftale"
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
    ms.topic="article"
    ms.date="09/14/2016"
    ms.author="piyushjo" />

#<a name="ios-sdk-for-azure-mobile-engagement"></a>iOS SDK til Azure Mobile aftale

Start her for at få alle oplysninger om, hvordan du integrerer Azure Mobile aftale i en iOS-App. Hvis du vil gerne Prøv det første, skal du gøre vores [15 minutter selvstudium](mobile-engagement-ios-get-started.md).

Klik for at se de [SDK indhold](mobile-engagement-ios-sdk-content.md)

##<a name="integration-procedures"></a>Integration af procedurer
1. Start her: [hvordan du integrerer Mobile aftale i din iOS-app](mobile-engagement-ios-integrate-engagement.md)

2. Til meddelelser: [hvordan du integrerer rækkevidde (meddelelser om) i din iOS-app](mobile-engagement-ios-integrate-engagement-reach.md)

3. Mærke plan implementering: [Sådan bruges de avancerede Mobile aftale mærkning API i din iOS-app](mobile-engagement-ios-use-engagement-api.md)


##<a name="release-notes"></a>Produktbemærkninger

### <a name="400-09122016"></a>4.0.0 (09-12/2016)

-   Fast meddelelse ikke actioned på iOS 10-enheder.
-   Fraråde XCode 7.

Skal du se den [komplette produktbemærkninger](mobile-engagement-ios-release-notes.md) til tidligere version

##<a name="upgrade-procedures"></a>Opgradere procedurer

Hvis du har allerede integreret en ældre version af aftale i dit program, har du overveje følgende punkter, når du opgraderer SDK.

Du kan være nødvendigt at følge flere procedurer, hvis du ikke har set flere versioner af SDK se den komplette [Opgradere procedurer](mobile-engagement-ios-upgrade-procedure.md).

For hver ny version af SDK skal du først erstatte (fjerne og importere igen i xcode) mapperne EngagementSDK og EngagementReach.

###<a name="from-300-to-400"></a>Fra 3.0.0 til 4.0.0

### <a name="xcode-8"></a>XCode 8
XCode 8 er obligatorisk begyndende fra version 4.0.0 af SDK.

> [AZURE.NOTE] Hvis du virkelig er afhængige af XCode 7 kan du bruge [iOS aftale SDK v3.2.4](https://aka.ms/r6oouh). Der er et kendt fejl på modulet rækkevidde af denne tidligere version, mens du kører på iOS 10-enheder: systemmeddelelser er ikke actioned. At løse dette er du nødt til at implementere frarådede API `application:didReceiveRemoteNotification:` i din app uddelegere på følgende måde:

    - (void)application:(UIApplication*)application
    didReceiveRemoteNotification:(NSDictionary*)userInfo
    {
        [[EngagementAgent shared] applicationDidReceiveRemoteNotification:userInfo fetchCompletionHandler:nil];
    }

> [AZURE.IMPORTANT] **Vi anbefaler ikke denne løsning** som dette problem kan ændre i en hvilken som helst kommende (lige underordnede) iOS opgradering, fordi denne iOS API frarådes. Du skal skifte til XCode 8 så tidligt som muligt.

#### <a name="usernotifications-framework"></a>UserNotifications framework
Skal du tilføje den `UserNotifications` framework i din opbygge faser.

Åbn din project-ruden, og vælg den korrekte destination i Projektstifinder. Åbn fanen **"Opbygning af faser"** derefter, og Tilføj framework i menuen **"Link binær med biblioteker"** `UserNotifications.framework` -Angiv, at hyperlinket som`Optional`

#### <a name="application-push-capability"></a>Programmet opslagsnål egenskab
XCode 8 kan nulstille din app push egenskab, skal du kontrollere den dobbelt den `capability` fanen for det valgte relevante mål.

#### <a name="add-the-new-ios-10-notification-registration-code"></a>Tilføje den nye iOS 10 meddelelse registreringskode
Ældre kodestykket til at registrere appen til beskeder fungerer stadig, men de bruger frarådede API'er, mens du kører på iOS 10. 

Importere den `User Notification` framework:

        #import <UserNotifications/UserNotifications.h>

I dit program stedfortræder `application:didFinishLaunchingWithOptions` metode Erstat:

        if ([application respondsToSelector:@selector(registerUserNotificationSettings:)]) {
            [application registerUserNotificationSettings:[UIUserNotificationSettings settingsForTypes:(UIUserNotificationTypeBadge | UIUserNotificationTypeSound | UIUserNotificationTypeAlert) categories:nil]];
            [application registerForRemoteNotifications];
        }
        else {

            [application registerForRemoteNotificationTypes:(UIRemoteNotificationTypeBadge | UIRemoteNotificationTypeSound | UIRemoteNotificationTypeAlert)];
        }

ved at:

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

#### <a name="if-you-already-have-your-own-unusernotificationcenterdelegate-implementation"></a>Hvis du allerede har dit eget UNUserNotificationCenterDelegate implementering

SDK indeholder sin egen implementering af UNUserNotificationCenterDelegate-protokollen. Det bruges af SDK til at overvåge livscyklussen for aftale beskeder på enheder, der kører på iOS 10 eller nyere. Hvis SDK registrerer dine stedfortrædere, der skal ikke bruges sin egen implementering, fordi der kan være kun én UNUserNotificationCenter stedfortræder i programmet. Det betyder, at du nødt til at føje aftale logik til din egen stedfortræderen.

Der er to måder at opnå dette.

Ved ganske enkelt stedfortræderen for viderestilling af opkald til SDK:

    #import <UIKit/UIKit.h>
    #import "EngagementAgent.h"
    #import <UserNotifications/UserNotifications.h>


    @interface MyAppDelegate : NSObject <UIApplicationDelegate, UNUserNotificationCenterDelegate>
    @end

    @implementation MyAppDelegate

    - (void)userNotificationCenter:(UNUserNotificationCenter *)center willPresentNotification:(UNNotification *)notification withCompletionHandler:(void (^)(UNNotificationPresentationOptions options))completionHandler
    {
      // Your own logic.

      [[EngagementAgent shared] userNotificationCenterWillPresentNotification:notification withCompletionHandler:completionHandler]
    }

    - (void)userNotificationCenter:(UNUserNotificationCenter *)center didReceiveNotificationResponse:(UNNotificationResponse *)response withCompletionHandler:(void(^)())completionHandler
    {
      // Your own logic.

      [[EngagementAgent shared] userNotificationCenterDidReceiveNotificationResponse:response withCompletionHandler:completionHandler]
    }
    @end

Eller ved at nedarvning fra den `AEUserNotificationHandler` klasse

    #import "AEUserNotificationHandler.h"
    #import "EngagementAgent.h"

    @interface CustomUserNotificationHandler :AEUserNotificationHandler
    @end

    @implementation CustomUserNotificationHandler

    - (void)userNotificationCenter:(UNUserNotificationCenter *)center willPresentNotification:(UNNotification *)notification withCompletionHandler:(void (^)(UNNotificationPresentationOptions options))completionHandler
    {
      // Your own logic.

      [super userNotificationCenter:center willPresentNotification:notification withCompletionHandler:completionHandler];
    }

    - (void)userNotificationCenter:(UNUserNotificationCenter *)center didReceiveNotificationResponse: UNNotificationResponse *)response withCompletionHandler:(void(^)())completionHandler
    {
      // Your own logic.

      [super userNotificationCenter:center didReceiveNotificationResponse:response withCompletionHandler:completionHandler];
    }

    @end

> [AZURE.NOTE] Du kan bestemme, om en meddelelse om stammer fra aftale eller ikke ved at overføre sin `userInfo` ordbog til agenten `isEngagementPushPayload:` klasse metode.