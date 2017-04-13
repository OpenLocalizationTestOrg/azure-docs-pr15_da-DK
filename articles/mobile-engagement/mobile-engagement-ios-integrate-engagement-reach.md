<properties
    pageTitle="Azure Mobile aftale iOS SDK når Integration | Microsoft Azure"
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

#<a name="how-to-integrate-engagement-reach-on-ios"></a>Hvordan du integrerer aftale når på iOS

Du skal følge integration fremgangsmåden i artiklen [Sådan integrere aftale på iOS dokument](mobile-engagement-ios-integrate-engagement.md) før følge denne vejledning.

Denne dokumentation kræver XCode 8. Hvis du virkelig er afhængige af XCode 7 kan du bruge [iOS aftale SDK v3.2.4](https://aka.ms/r6oouh). Der er et kendt fejl på denne tidligere version, mens du kører på iOS 10-enheder: systemmeddelelser er ikke actioned. At løse dette er du nødt til at implementere frarådede API `application:didReceiveRemoteNotification:` i din app uddelegere på følgende måde:

    - (void)application:(UIApplication*)application
    didReceiveRemoteNotification:(NSDictionary*)userInfo
    {
        [[EngagementAgent shared] applicationDidReceiveRemoteNotification:userInfo fetchCompletionHandler:nil];
    }

> [AZURE.IMPORTANT] **Vi anbefaler ikke denne løsning** som dette problem kan ændre i en hvilken som helst kommende (lige underordnede) iOS opgradering, fordi denne iOS API frarådes. Du skal skifte til XCode 8 så tidligt som muligt.

### <a name="enable-your-app-to-receive-silent-push-notifications"></a>Aktivere din app til at modtage uovervåget Push-beskeder

[AZURE.INCLUDE [mobile-engagement-ios-silent-push](../../includes/mobile-engagement-ios-silent-push.md)]

##<a name="integration-steps"></a>Integration trin

### <a name="embed-the-engagement-reach-sdk-into-your-ios-project"></a>Integrere aftale når SDK i projektet iOS

-   Tilføj rækkevidde sdk i projektet Xcode. Gå til i Xcode, **Project \> Føj til project** , og vælg den `EngagementReach` mappe.

### <a name="modify-your-application-delegate"></a>Ændre dit program stedfortræder

-   Importere modulet aftale når øverst i implementeringsfilen:

        [...]
        #import "AEReachModule.h"

-   I metode `applicationDidFinishLaunching:` eller `application:didFinishLaunchingWithOptions:`, oprette et modul, rækkevidde og sende dem til din eksisterende aftale initialisering linje:

        - (BOOL)application:(UIApplication *)application didFinishLaunchingWithOptions:(NSDictionary *)launchOptions {
          AEReachModule* reach = [AEReachModule moduleWithNotificationIcon:[UIImage imageNamed:@"icon.png"]];
          [EngagementAgent init:@"Endpoint={YOUR_APP_COLLECTION.DOMAIN};SdkKey={YOUR_SDK_KEY};AppId={YOUR_APPID}" modules:reach, nil];
          [...]

          return YES;
        }

-   Ændre **'icon.png'** streng med det ønskede som din meddelelsesikon billede navn.
-   Hvis du vil bruge indstillingen *Opdater badge værdi* i rækkevidde kampagner, eller hvis du vil bruge oprindelige opslagsnål \<SaaS/rækkevidde API/kampagnens format/oprindelig opslagsnål\> kampagner, skal du lade rækkevidde modul administrere badge ikonet selve (det vil automatisk fjerne programmet badge og også nulstille den værdi, der er gemt ved aftale, hver gang programmet er startet eller foregrounded). Det gør du ved at tilføje følgende linje efter rækkevidde modul initialisering:

        [reach setAutoBadgeEnabled:YES];

-   Hvis du vil håndtere rækkevidde data opslagsnål, skal du lade stedfortræderen programmet er i overensstemmelse med de `AEReachDataPushDelegate` protocol. Du kan tilføje følgende linje efter rækkevidde modul initialisering:

        [reach setDataPushDelegate:self];

-   Og du kan implementere metoderne `onDataPushStringReceived:` og `onDataPushBase64ReceivedWithDecodedBody:andEncodedBody:` i dit program stedfortræder:

        -(BOOL)didReceiveStringDataPushWithCategory:(NSString*)category body:(NSString*)body
        {
           NSLog(@"String data push message with category <%@> received: %@", category, body);
           return YES;
        }

        -(BOOL)didReceiveBase64DataPushWithCategory:(NSString*)category decodedBody:(NSData *)decodedBody encodedBody:(NSString *)encodedBody
        {
           NSLog(@"Base64 data push message with category <%@> received: %@", category, encodedBody);
           // Do something useful with decodedBody like updating an image view
           return YES;
        }

### <a name="category"></a>Kategori

Parameteren kategori er valgfri, når du opretter en Data Push kampagnens og giver mulighed for at filtrere data skubber. Dette er nyttigt, hvis du vil push forskellige typer af `Base64` data og ønsker at identificere deres type før parsing af dem.

**Programmet er nu klar til at modtage og få vist rækkevidde indhold!**

##<a name="how-to-receive-announcements-and-polls-at-any-time"></a>Hvordan du kan modtage meddelelser og afstemninger når som helst

Aftale kan sende rækkevidde beskeder til dine slutbrugere, når som helst ved hjælp af Apple Push besked om tjenesten.

Hvis du vil aktivere denne funktion, har du til at forberede dit program til Apple push-beskeder og ændre dit program stedfortræder.

### <a name="prepare-your-application-for-apple-push-notifications"></a>Forberede dit program til Apple push-beskeder

Følg vejledningen: [Sådan forberedes dit program for Apple Push-beskeder](https://developer.apple.com/library/ios/documentation/IDEs/Conceptual/AppDistributionGuide/AddingCapabilities/AddingCapabilities.html#//apple_ref/doc/uid/TP40012582-CH26-SW6)

### <a name="add-the-necessary-client-code"></a>Tilføj den nødvendige klient-kode

*På dette tidspunkt skal dit program have et registreret Apple push certifikat i aftale frontend.*

Hvis den ikke allerede er gjort skal du registrere dit program du modtager pushbeskeder.

* Importere den `User Notification` framework:

        #import <UserNotifications/UserNotifications.h>

* Tilføj følgende linje, når programmet starter (typisk i `application:didFinishLaunchingWithOptions:`):

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

Derefter skal du give aftale tokenet enhed, der returneres af Apple-servere. Dette er gjort i metoden med navnet `application:didRegisterForRemoteNotificationsWithDeviceToken:` i dit program stedfortræder:

    - (void)application:(UIApplication*)application didRegisterForRemoteNotificationsWithDeviceToken:(NSData*)deviceToken
    {
        [[EngagementAgent shared] registerDeviceToken:deviceToken];
    }

Endelig skal have du til at informere aftale SDK, når dit program modtager en besked om remote. Det gør du kalder metoden `applicationDidReceiveRemoteNotification:fetchCompletionHandler:` i dit program stedfortræder:

    - (void)application:(UIApplication*)application didReceiveRemoteNotification:(NSDictionary*)userInfo fetchCompletionHandler:(void (^)(UIBackgroundFetchResult result))handler
    {
        [[EngagementAgent shared] applicationDidReceiveRemoteNotification:userInfo fetchCompletionHandler:handler];
    }

> [AZURE.NOTE] Metoden ovenfor introduceres i iOS 7. Hvis du målretter iOS < 7, skal du huske at implementere metode `application:didReceiveRemoteNotification:` i dit program stedfortræder og viderestilling af opkald `applicationDidReceiveRemoteNotification` på EngagementAgent ved at overføre nul i stedet for den `handler` argument:

    - (void)application:(UIApplication*)application
    didReceiveRemoteNotification:(NSDictionary*)userInfo
    {
        [[EngagementAgent shared] applicationDidReceiveRemoteNotification:userInfo fetchCompletionHandler:nil];
    }

> [AZURE.IMPORTANT] Som standard-aftale når kontrolelementer på completionHandler. Hvis du vil besvare manuelt på `handler` blokere i din kode, kan du overføre nul den `handler` argument og kontrollere at færdiggøre blokere dig selv. Se den `UIBackgroundFetchResult` type for en liste over mulige værdier.


### <a name="full-example"></a>Fuld eksempel

Her er en fuld eksempel på integration:

    #pragma mark -
    #pragma mark Application lifecycle

    - (BOOL)application:(UIApplication*)application didFinishLaunchingWithOptions:(NSDictionary*)launchOptions
    {
      /* Reach module */
      AEReachModule* reach = [AEReachModule moduleWithNotificationIcon:[UIImage imageNamed:@"icon.png"]];
      [reach setAutoBadgeEnabled:YES];

      /* Engagement initialization */
      [EngagementAgent init:@"Endpoint={YOUR_APP_COLLECTION.DOMAIN};SdkKey={YOUR_SDK_KEY};AppId={YOUR_APPID}" modules:reach, nil];
      [[EngagementAgent shared] setPushDelegate:self];

      /* Views */
      [window addSubview:[tabBarController view]];
      [window makeKeyAndVisible];

      [application registerForRemoteNotificationTypes:UIRemoteNotificationTypeAlert|UIRemoteNotificationTypeBadge|UIRemoteNotificationTypeSound];
      return YES;
    }

    - (void)application:(UIApplication*)application didRegisterForRemoteNotificationsWithDeviceToken:(NSData*)deviceToken
    {
      [[EngagementAgent shared] registerDeviceToken:deviceToken];
    }

    - (void)application:(UIApplication *)application didReceiveRemoteNotification:(NSDictionary *)userInfo fetchCompletionHandler:(void (^)(UIBackgroundFetchResult result))handler
    {
        [[EngagementAgent shared] applicationDidReceiveRemoteNotification:userInfo fetchCompletionHandler:handler];
    }

### <a name="if-you-have-your-own-unusernotificationcenterdelegate-implementation"></a>Hvis du har dit eget UNUserNotificationCenterDelegate implementering

SDK indeholder også sin egen implementering af UNUserNotificationCenterDelegate-protokollen. Det bruges af SDK til at overvåge livscyklussen for aftale beskeder på enheder, der kører på iOS 10 eller nyere. Hvis SDK registrerer dine stedfortrædere, der skal ikke bruges sin egen implementering, fordi der kan være kun én UNUserNotificationCenter stedfortræder i programmet. Det betyder, at du nødt til at føje aftale logik til din egen stedfortræderen.

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

##<a name="how-to-customize-campaigns"></a>Hvordan du tilpasser kampagner

### <a name="notifications"></a>Beskeder

Der findes to typer beskeder: system og i app'en beskeder.

Systemmeddelelser håndteres af iOS og kan ikke tilpasses.

I app-meddelelser er foretaget af en visning, der er tilføjes dynamisk til det aktuelle programvindue. Dette kaldes en meddelelse om overlejring. Meddelelse om overlejrer er gode til en hurtig integration, fordi de ikke kræver muligt at ændre en visning i dit program.

#### <a name="layout"></a>Layout

Hvis du vil ændre udseendet af dine i app-meddelelser, kan du bare redigere filen `AENotificationView.xib` til dine behov, så længe du holde mærke værdier og typer af de eksisterende subviews.

Som standard præsenteres i app-meddelelser i bunden af skærmen. Hvis du foretrækker at få dem vist øverst på skærmen, redigere den medfølgende `AENotificationView.xib` og ændre den `AutoSizing` egenskab for den primære visning, så den kan holdes øverst i dets superview.

#### <a name="categories"></a>Kategorier

Når du ændrer det medfølgende layout, kan du ændre udseendet af alle dine meddelelser. Kategorier kan du angive forskellige målrettede ser (muligvis funktionsmåder) til meddelelser. Du kan angive en kategori, når du opretter en rækkevidde campaign. Husk på, at kategorier kan du også tilpasse meddelelser og afstemninger, der er beskrevet senere i dette dokument.

Hvis du vil registrere en kategori handler til dine meddelelser, skal du tilføje et opkald, når modulet rækkevidde er initialiseret.

    AEReachModule* reach = [AEReachModule moduleWithNotificationIcon:[UIImage imageNamed:@"icon.png"]];
    [reach registerNotifier:myNotifier forCategory:@"my_category"];
    ...

`myNotifier`skal være en forekomst af et objekt, der overholder protokollen `AENotifier`.

Du kan implementere metoderne protocol ved dig selv, eller du kan vælge at reimplement klassen eksisterende `AEDefaultNotifier` som udfører de fleste af arbejdet allerede.

Hvis du vil omdefinere visningen besked for en bestemt kategori, kan du følge dette eksempel:

    #import "AEDefaultNotifier.h"
    #import "AENotificationView.h"
    @interface MyNotifier : AEDefaultNotifier
    @end

    @implementation MyNotifier

    -(NSString*)nibNameForCategory:(NSString*)category
    {
      return "MyNotificationView";
    }

    @end

Dette simple eksempel på kategori forudsætter, at du har en fil med navnet `MyNotificationView.xib` i din primære programmet samlet. Hvis metoden ikke er kan finde en tilsvarende `.xib`, vises meddelelsen ikke og aftale output vil blive sendt en meddelelse i konsollen.

Filen medfølgende nib skal overholde de følgende regler:

-   Det skal kun indeholde én visning.
-   Subviews skal være af samme type som dem i den angivne nib-fil med navnet`AENotificationView.xib`
-   Subviews bør have de samme mærker som dem i det medfølgende nib fil med navnet`AENotificationView.xib`

> [AZURE.TIP] Kun kopiere medfølgende nib-fil, kaldet `AENotificationView.xib`, og begynder at arbejde derfra. Men vær forsigtig, visningen i denne nib-fil er knyttet til klassen `AENotificationView`. Denne klasse omdefineret metoden `layoutSubViews` til at flytte og tilpasse størrelsen på dens subviews alt efter konteksten. Du overveje at erstatte den med en `UIView` eller du brugerdefineret visning klasse.

Hvis du har brug for mere tilpasning af meddelelser (Hvis du vil for eksempel at indlæse visningen direkte fra koden), det anbefales at se nærmere på den angivne datakilde kode og klasse dokumentation for `Protocol ReferencesDefaultNotifier` og `AENotifier`.

Bemærk, at du kan bruge den samme anmelder for flere kategorier.

Du kan også omdefineret standard notifier således:

    AEReachModule* reach = [AEReachModule moduleWithNotificationIcon:[UIImage imageNamed:@"icon.png"]];
    [reach registerNotifier:myNotifier forCategory:kAEReachDefaultCategory];

##### <a name="notification-handling"></a>Håndtering af meddelelser

Når du bruger standardkategorien, nogle livscyklus metoder er navnet på den `AEReachContent` objekt til rapport statistik og opdatere tilstanden kampagnens:

-   Når meddelelsen vises i programmet, skal den `displayNotification` metode kaldes (som rapporter statistik) ved `AEReachModule` Hvis `handleNotification:` returnerer `YES`.
-   Hvis meddelelsen er lukket, den `exitNotification` metode kaldes, statistikken rapporteres og næste kampagner kan nu behandles.
-   Hvis meddelelsen er markeret, `actionNotification` er kaldet statistikken rapporteres og den tilknyttede handling er udført.

Hvis din implementering af `AENotifier` tilsidesætte standardfunktionsmåden, du skal ringe til disse livscyklus metoder ved dig selv. Følgende eksempler viser nogle tilfælde, hvor standardfunktionen er tilsidesat:

-   Du ikke kan udvide `AEDefaultNotifier`, f.eks. du implementeret håndtering af kategori fra bunden.
-   Du overrode `prepareNotificationView:forContent:`, skal du sørge for at tilknytte mindst `onNotificationActioned` eller `onNotificationExited` til en af dine U.I-kontrolelementer.

> [AZURE.WARNING] Hvis `handleNotification:` udløses en undtagelse, indholdet slettes og `drop` er kaldet dette rapporteres i statistik og næste kampagner kan nu behandles.

#### <a name="include-notification-as-part-of-an-existing-view"></a>Medtage meddelelse som en del af en eksisterende visning

Overlejrer er gode til at få en hurtig integration, men kan nogle gange ikke være praktisk, eller få indflydelse på uønskede.

Hvis du ikke er tilfreds med overlejring systemet i nogle af dine visninger, kan du tilpasse det til disse visninger.

Du kan vælge at medtage vores meddelelse layout i dine eksisterende visninger. Hvis du vil gøre det, er der to implementering typografier:

1.  Tilføje visningen meddelelse ved hjælp af brugergrænsefladen builder

    -   Åbn *brugergrænseflade Builder*
    -   Placere en 320 x 60 (eller 768 x 60, hvis du er på iPad) `UIView` sted, hvor du vil meddelelsen skal vises
    -   Angive mærke værdien for denne visning til: **36822491**

2.  Tilføje visningen meddelelse fra et program. Tilføj kun følgende kode, når visningen er blevet initialiseret:

        UIView* notificationView = [[UIView alloc] initWithFrame:CGRectMake(0, 0, 320, 60)]; //Replace x and y coordinate values to your needs.
        notificationView.tag = NOTIFICATION_AREA_VIEW_TAG;
        [self.view addSubview:notificationView];

`NOTIFICATION_AREA_VIEW_TAG`Makrohandlingen kan findes i `AEDefaultNotifier.h`.

> [AZURE.NOTE] Standard notifier registrerer automatisk, meddelelse om layout er inkluderet i denne visning og kan ikke tilføje en overlejring af den.

### <a name="announcements-and-polls"></a>Meddelelser og afstemninger

#### <a name="layouts"></a>Layout

Du kan ændre filerne `AEDefaultAnnouncementView.xib` og `AEDefaultPollView.xib` så længe du holde mærke værdier og typer af de eksisterende subviews.

#### <a name="categories"></a>Kategorier

##### <a name="alternate-layouts"></a>Alternative layout

Sådan meddelelser, kan kampagnens kategori bruges til at har alternative layout til dine meddelelser og afstemninger.

Hvis du vil oprette en kategori til en meddelelse, skal du udvide **AEAnnouncementViewController** og registrere den, når modulet rækkevidde er blevet initialiseret:

    AEReachModule* reach = [AEReachModule moduleWithNotificationIcon:[UIImage imageNamed:@"icon.png"]];
    [reach registerAnnouncementController:[MyCustomAnnouncementViewController class] forCategory:@"my_category"];

> [AZURE.NOTE] Hver gang en bruger kan klikker på en meddelelse til en meddelelse med kategorien "min\_kategori", din registrerede Vis controller (i så fald `MyCustomAnnouncementViewController`) skal være initialiseret ved at ringe til metoden `initWithAnnouncement:` og visningen vil være føjet til det aktuelle programvinduet.

I din implementering af den `AEAnnouncementViewController` klasse, er du nødt til at læse egenskaben `announcement` initialiseret din subviews. Overvej eksemplet herunder, hvor to etiketter initialiseret ved hjælp af `title` og `body` egenskaberne for den `AEReachAnnouncement` klasse:

    -(void)loadView
    {
        [super loadView];

        UILabel* titleLabel = [[UILabel alloc] initWithFrame:CGRectMake(10, 20, 300, 60)];
        titleLabel.font = [UIFont systemFontOfSize:32.0];
        titleLabel.text = self.announcement.title;

        UILabel* bodyLabel = [[UILabel alloc] initWithFrame:CGRectMake(10, 20, 300, 60)];
        bodyLabel.font = [UIFont systemFontOfSize:24.0];
        bodyLabel.text = self.announcement.body;

        [self.view addSubview:titleLabel];
        [self.view addSubview:bodyLabel];
    }

Hvis du ikke vil indlæse dine visninger af dig selv, men du blot ønsker at genbruge standardlayoutet Vis meddelelse, kan du blot foretage din brugerdefineret visning controller udvider klassen medfølgende `AEDefaultAnnouncementViewController`. I så fald duplikere filen nib `AEDefaultAnnouncementView.xib` og Omdøb den, så den kan indlæses af din brugerdefineret visning controller (for en controller med navnet `CustomAnnouncementViewController`, skal du ringe til filen nib `CustomAnnouncementView.xib`).

Hvis du vil erstatte standardkategorien af meddelelser, skal du blot registrere enheden brugerdefineret visning for den kategori, der er defineret i `kAEReachDefaultCategory`:

    [reach registerAnnouncementController:[MyCustomAnnouncementViewController class] forCategory:kAEReachDefaultCategory];

Afstemninger kan tilpasses på samme måde:

    AEReachModule* reach = [AEReachModule moduleWithNotificationIcon:[UIImage imageNamed:@"icon.png"]];
    [reach registerPollController:[MyCustomPollViewController class] forCategory:@"my_category"];

Denne tid, det medfølgende `MyCustomPollViewController` skal udvide `AEPollViewController`. Eller du kan vælge at udvide fra standard controlleren: `AEDefaultPollViewController`.

> [AZURE.IMPORTANT] Glem ikke at ringe til enten `action` (`submitAnswers:` ved brugerdefinerede afstemning Vis enheder) eller `exit` metode før visning controller er lukket. Ellers skal statistik flyttes ikke (det vil sige ingen analytics på kampagnens) og flere det vigtigste er næste kampagner får ikke besked, før ansøgningsprocedure genstartes.

##### <a name="implementation-example"></a>Eksempel på implementering

I denne implementering er visningen Brugerdefineret meddelelse indlæst fra en ekstern xib fil.

Ud for avancerede meddelelse opsætning anbefales det at kigge på kildekode af standard implementeringen.

`CustomAnnouncementViewController.h`

    //Interface
    @interface CustomAnnouncementViewController : AEAnnouncementViewController {
      UILabel* titleLabel;
      UITextView* descTextView;
      UIWebView* htmlWebView;
      UIButton* okButton;
      UIButton* cancelButton;
    }

    @property (nonatomic, retain) IBOutlet UILabel* titleLabel;
    @property (nonatomic, retain) IBOutlet UITextView* descTextView;
    @property (nonatomic, retain) IBOutlet UIWebView* htmlWebView;
    @property (nonatomic, retain) IBOutlet UIButton* okButton;
    @property (nonatomic, retain) IBOutlet UIButton* cancelButton;

    -(IBAction)okButtonClicked:(id)sender;
    -(IBAction)cancelButtonClicked:(id)sender;

`CustomAnnouncementViewController.m`

    //Implementation
    @implementation CustomAnnouncementViewController
    @synthesize titleLabel;
    @synthesize descTextView;
    @synthesize htmlWebView;
    @synthesize okButton;
    @synthesize cancelButton;

    -(id)initWithAnnouncement:(AEReachAnnouncement*)anAnnouncement
    {
      self = [super initWithNibName:@"CustomAnnouncementViewController" bundle:nil];
      if (self != nil) {
        self.announcement = anAnnouncement;
      }
      return self;
    }

    - (void) dealloc
    {
      [titleLabel release];
      [descTextView release];
      [htmlWebView release];
      [okButton release];
      [cancelButton release];
      [super dealloc];
    }

    - (void)viewDidLoad {
      [super viewDidLoad];

      /* Init announcement title */
      titleLabel.text = self.announcement.title;

      /* Init announcement body */
      if(self.announcement.type == AEAnnouncementTypeHtml)
      {
        titleLabel.hidden = YES;
        htmlWebView.hidden = NO;
        [htmlWebView loadHTMLString:self.announcement.body baseURL:[NSURL URLWithString:@"http://localhost/"]];
      }
      else
      {
        titleLabel.hidden = NO;
        htmlWebView.hidden = YES;
        descTextView.text = self.announcement.body;
      }

      /* Set action button label */
      if([self.announcement.actionLabel length] > 0)
        [okButton setTitle:self.announcement.actionLabel forState:UIControlStateNormal];

      /* Set exit button label */
      if([self.announcement.exitLabel length] > 0)
        [cancelButton setTitle:self.announcement.exitLabel forState:UIControlStateNormal];
    }

    #pragma mark Actions

    -(IBAction)okButtonClicked:(id)sender
    {
        [self action];
    }

    -(IBAction)cancelButtonClicked:(id)sender
    {
        [self exit];
    }

    @end
