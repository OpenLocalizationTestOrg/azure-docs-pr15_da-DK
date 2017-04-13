<properties
    pageTitle="Azure Mobile aftale iOS SDK opgradere Procedure | Microsoft Azure"
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

#<a name="upgrade-procedures"></a>Opgradere procedurer

Hvis du har allerede integreret en ældre version af aftale i dit program, har du overveje følgende punkter, når du opgraderer SDK.

For hver ny version af SDK skal du først erstatte (fjerne og importere igen i xcode) mapperne EngagementSDK og EngagementReach.

##<a name="from-300-to-400"></a>Fra 3.0.0 til 4.0.0

### <a name="xcode-8"></a>XCode 8
XCode 8 er obligatorisk begyndende fra version 4.0.0 af SDK.

> [AZURE.NOTE] Hvis du virkelig er afhængige af XCode 7 kan du bruge [iOS aftale SDK v3.2.4](https://aka.ms/r6oouh). Der er et kendt fejl på modulet rækkevidde af denne tidligere version, mens du kører på iOS 10-enheder: systemmeddelelser er ikke actioned. At løse dette er du nødt til at implementere frarådede API `application:didReceiveRemoteNotification:` i din app uddelegere på følgende måde:

    - (void)application:(UIApplication*)application
    didReceiveRemoteNotification:(NSDictionary*)userInfo
    {
        [[EngagementAgent shared] applicationDidReceiveRemoteNotification:userInfo fetchCompletionHandler:nil];
    }

> [AZURE.IMPORTANT] **Vi anbefaler ikke denne løsning** som dette problem kan ændre i en hvilken som helst kommende (lige underordnede) iOS opgradering, fordi denne iOS API frarådes. Du skal skifte til XCode 8 så tidligt som muligt.

### <a name="usernotifications-framework"></a>UserNotifications framework
Skal du tilføje den `UserNotifications` framework i din opbygge faser.

Åbn din project-ruden, og vælg den korrekte destination i Projektstifinder. Åbn fanen **"Opbygning af faser"** derefter, og Tilføj framework i menuen **"Link binær med biblioteker"** `UserNotifications.framework` -Angiv, at hyperlinket som`Optional`

### <a name="application-push-capability"></a>Programmet opslagsnål egenskab
XCode 8 kan nulstille din app push egenskab, skal du kontrollere den dobbelt den `capability` fanen for det valgte relevante mål.

### <a name="add-the-new-ios-10-notification-registration-code"></a>Tilføje den nye iOS 10 meddelelse registreringskode
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

### <a name="if-you-already-have-your-own-unusernotificationcenterdelegate-implementation"></a>Hvis du allerede har dit eget UNUserNotificationCenterDelegate implementering

SDK indeholder også sin egen implementering af UNUserNotificationCenterDelegate-protokollen. Det bruges af SDK til at overvåge livscyklussen for aftale beskeder på enheder, der kører på iOS 10 eller nyere. Hvis SDK registrerer dine stedfortrædere, der skal ikke bruges sin egen implementering, fordi der kan være kun én UNUserNotificationCenter stedfortræder i programmet. Det betyder, at du nødt til at føje aftale logik til din egen stedfortræder.

Der er to måder at opnå dette.

Opkald til SDK ved blot at videresende dine stedfortrædere:

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

##<a name="from-200-to-300"></a>Fra 2.0.0 til 3.0.0
Sluppet support til iOS 4.X. Start fra denne version installationsdestinationen for dit program skal være mindst iOS 6.

Hvis du bruger rækkevidde i dit program, skal du tilføje `remote-notification` værdi til den `UIBackgroundModes` matrix i Info.plist filen for at modtage eksterne meddelelser.

Metoden `application:didReceiveRemoteNotification:` skal erstattes af `application:didReceiveRemoteNotification:fetchCompletionHandler:` i programmet stedfortræderen.

"AEPushDelegate.h" frarådes grænseflade, og du skal fjerne alle referencer. Dette omfatter fjerne `[[EngagementAgent shared] setPushDelegate:self]` og stedfortræder metoder fra dit program stedfortræder:

    -(void)willRetrieveLaunchMessage;
    -(void)didFailToRetrieveLaunchMessage;
    -(void)didReceiveLaunchMessage:(AEPushMessage*)launchMessage;

##<a name="from-1160-to-200"></a>Fra 1.16.0 til 2.0.0
Følgende fremgangsmåde beskriver, hvordan til at overføre en SDK-integration fra tjenesten Capptain, som Capptain SAS til en app drevet af Azure Mobile aftale.
Hvis du overfører fra en tidligere version, skal du kontakte webstedet Capptain for at overføre til 1.16 først og derefter anvende følgende fremgangsmåde.

>[AZURE.IMPORTANT] Capptain og Mobile aftale er ikke de samme tjenester, og den fremgangsmåde, der er angivet under fremhæves kun Sådan overføres klient-app. Overføre SDK i appen overføres ikke dine data fra Capptain servere til Mobile aftale servere

### <a name="agent"></a>Agent

Metoden `registerApp:` er blevet erstattet af den nye metode `init:`. Programmet stedfortræderen skal være opdateret i overensstemmelse hermed og bruge forbindelsesstreng:

            - (BOOL)application:(UIApplication *)application didFinishLaunchingWithOptions:(NSDictionary *)launchOptions
            {
              [...]
              [EngagementAgent init:@"YOUR_CONNECTION_STRING"];
              [...]
            }

SmartAd registrering er blevet fjernet fra SDK du kun behøver at fjerne alle forekomster af `AETrackModule` klasse

### <a name="class-name-changes"></a>Klasse navneændringer

Som en del af den rebranding er der flere klasse/filnavne, der skal ændres.

Alle klasser præfikset "CP" omdøbes med "AE" præfiks.

Eksempel:

-   `CPModule.h`er omdøbt til `AEModule.h`.

Alle klasser præfikset "Capptain" omdøbes med "Aftale" præfiks.

Eksempler:

-   Klassen `CapptainAgent` er omdøbt til `EngagementAgent`.
-   Klassen `CapptainTableViewController` er omdøbt til `EngagementTableViewController`.
-   Klassen `CapptainUtils` er omdøbt til `EngagementUtils`.
-   Klassen `CapptainViewController` er omdøbt til `EngagementViewController`.
