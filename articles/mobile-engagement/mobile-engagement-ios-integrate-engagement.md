<properties
    pageTitle="Azure Mobile aftale iOS SDK Integration | Microsoft Azure"
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

#<a name="how-to-integrate-engagement-on-ios"></a>Hvordan du kan integrere aftale på iOS

> [AZURE.SELECTOR]
- [Windows Universal](mobile-engagement-windows-store-integrate-engagement.md)
- [Windows Phone Silverlight](mobile-engagement-windows-phone-integrate-engagement.md)
- [iOS](mobile-engagement-ios-integrate-engagement.md)
- [Android](mobile-engagement-android-integrate-engagement.md)

Denne procedure beskriver den nemmeste måde at aktivere aftales analyser og overvågning funktioner i dit iOS-program.

Aftale SDK kræver iOS6 + og Xcode 8: installationsdestinationen for dit program skal være mindst iOS 6.

> [AZURE.NOTE]
> Hvis du virkelig er afhængige af XCode 7 kan du bruge [iOS aftale SDK v3.2.4](https://aka.ms/r6oouh). Der er et kendt fejl på modulet rækkevidde af denne tidligere version, mens kører på iOS 10-enheder se [rækkevidde modul integration](mobile-engagement-ios-integrate-engagement-reach.md) kan finde flere oplysninger. Hvis du vælger at bruge SDK v3.2.4, skal du blot springe den `UserNotifications.framework` importere i næste trin.

Følgende trin er nok til at aktivere rapporten af logfiler, der skal bruges til at beregne alle statistiske oplysninger om brugere, sessioner, aktiviteter, går ned og Technicals. Rapport over logfiler, der skal bruges til at beregne andre statistikker som begivenheder, fejl og job skal udføres manuelt ved hjælp af aftale API (se [Sådan bruges de avancerede Mobile aftale mærkning API i din iOS-app](mobile-engagement-ios-use-engagement-api.md) , fordi disse statistikker er afhænger af programmet.

##<a name="embed-the-engagement-sdk-into-your-ios-project"></a>Integrere aftale SDK i projektet iOS

- Du kan hente iOS SDK fra [her](http://aka.ms/qk2rnj).

- Føje aftale SDK til projektet iOS: i Xcode, højreklik på dit projekt, og vælg **"Føje filer til..."** og vælge den `EngagementSDK` mappe.

- Aftale kræver yderligere rammer, der kan arbejde: Åbn din project-ruden i Projektstifinder, og vælg den korrekte destination. Åbn fanen **"Opbygning af faser"** derefter, og Tilføj disse strukturer i menuen **"Link binær med biblioteker"** :

    -   `UserNotifications.framework`-Angiv, at hyperlinket som`Optional`
    -   `AdSupport.framework`-Angiv, at hyperlinket som`Optional`
    -   `SystemConfiguration.framework`
    -   `CoreTelephony.framework`
    -   `CFNetwork.framework`
    -   `CoreLocation.framework`
    -   `libxml2.dylib`

> [AZURE.NOTE] AdSupport, som kan fjernes. Aftale skal have denne framework til at indsamle IDFA. Dog IDFA af websteder kan deaktiveres \<ios-sdk-aftale-idfa\> i overensstemmelse med den nye Apple politik om dette ID.

##<a name="initialize-the-engagement-sdk"></a>Initialiseret aftale SDK

Du skal ændre dit program stedfortræder:

-   Importere aftale agent øverst i implementeringsfilen:

        [...]
        #import "EngagementAgent.h"

-   Initialiseret aftale i metoden '**applicationDidFinishLaunching:**'eller'**program: didFinishLaunchingWithOptions:**':

        - (BOOL)application:(UIApplication *)application didFinishLaunchingWithOptions:(NSDictionary *)launchOptions
        {
          [...]
          [EngagementAgent init:@"Endpoint={YOUR_APP_COLLECTION.DOMAIN};SdkKey={YOUR_SDK_KEY};AppId={YOUR_APPID}"];
          [...]
        }

##<a name="basic-reporting"></a>Grundlæggende rapportering

### <a name="recommended-method-overload-your-uiviewcontroller-classes"></a>Anbefalet metode: overbelastning din `UIViewController` klasser

For at aktivere rapport over alle de logfiler, der kræves, før aftale til at beregne brugere, sessioner, aktiviteter, går ned og tekniske statistik, kan du blot gøre alle dine `UIViewController` underordnede klasser nedarver fra den `EngagementViewController` klasser (samme regel `UITableViewController`  - \> `EngagementTableViewController`).

**Uden at aftale:**

    #import <UIKit/UIKit.h>

    @interface Tab1ViewController : UIViewController<UITextFieldDelegate> {
      UITextField* myTextField1;
      UITextField* myTextField2;
    }

    @property (nonatomic, retain) IBOutlet UITextField* myTextField1;
    @property (nonatomic, retain) IBOutlet UITextField* myTextField2;

**Med aftale:**

    #import <UIKit/UIKit.h>
    #import "EngagementViewController.h"

    @interface Tab1ViewController : EngagementViewController<UITextFieldDelegate> {
      UITextField* myTextField1;
      UITextField* myTextField2;
    }

    @property (nonatomic, retain) IBOutlet UITextField* myTextField1;
    @property (nonatomic, retain) IBOutlet UITextField* myTextField2;

### <a name="alternate-method-call-startactivity-manually"></a>Alternative metode: ringe `startActivity()` manuelt

Hvis du ikke kan eller ikke vil overbelastning din `UIViewController` klasser, kan du i stedet starte dine aktiviteter ved at ringe til `EngagementAgent`'s metoder direkte.

> [AZURE.IMPORTANT] IOS SDK automatisk opkald på `endActivity()` metode, når programmet er lukket. Det er derfor *meget* anbefales til at ringe til den `startActivity` metode, når aktiviteten på brugeren, ændre, og til *aldrig* Ring til den `endActivity` metode, da denne metode tvinger den aktuelle session skal afsluttes.

##<a name="location-reporting"></a>Placering rapportering

Apple servicebetingelser tillader ikke programmer til at bruge denne funktion til statistik formål kun. Det anbefales derfor til at aktivere placering rapporter kun, hvis dit program også bruge den placering, registrering af anden årsag.

Starter med iOS 8, skal du angive en beskrivelse af hvordan din app bruger placering tjenester ved at angive en streng til tasten [NSLocationWhenInUseUsageDescription] eller [NSLocationAlwaysUsageDescription] i din app Info.plist fil. Hvis du vil placering af rapport i baggrunden med aftale, skal du føje nøglen NSLocationAlwaysUsageDescription. Føje nøglen NSLocationWhenInUseUsageDescription i alle andre tilfælde.

### <a name="lazy-area-location-reporting"></a>Fløde området placering rapportering

Fløde området placering rapportering giver mulighed for at rapportere land, region og lokalitet, der er knyttet til enheder. Denne type placering rapportering bruger kun netværksplaceringer (baseret på celle ID eller WIFI). Området enhed rapporteres højst én gang hver enkelt session. GPS bruges aldrig, og derfor denne type placering rapport er meget få (ikke at sige Nej) påvirkning af batteriet.

Rapporteret områder bruges til at beregne geografiske statistik om brugere, sessioner, hændelser og fejl. De kan også bruges som kriterium i rækkevidde kampagner. Det sidste kendte område, der rapporteres for en enhed kan hentes sammen takket være [Enhed API].

For at aktivere fløde området placering rapportering skal du tilføje følgende linje efter initialisering af aftale agent:

    - (BOOL)application:(UIApplication *)application didFinishLaunchingWithOptions:(NSDictionary *)launchOptions
    {
      [...]
      [[EngagementAgent shared] setLazyAreaLocationReport:YES];
      [...]
    }

### <a name="real-time-location-reporting"></a>Realtid placering rapportering

Realtid placering rapportering giver mulighed for at rapportere bredde- og længdegrader, der er knyttet til enheder. Som standard denne type placering rapportering kun bruger netværksplaceringer (baseret på celle ID eller WIFI) og rapportering er kun aktivt, når programmet kører i forgrunden (det vil sige i en session).

Realtid placeringer er *ikke* bruges til at beregne statistik. De kun bruges til at tillade brug af realtid geografisk-indhegning \<rækkevidde-målgruppe-geofencing\> kriterium i rækkevidde kampagner.

For at aktivere realtid placering rapportering skal du tilføje følgende linje efter initialisering af aftale agent:

    [[EngagementAgent shared] setRealtimeLocationReport:YES];

#### <a name="gps-based-reporting"></a>GPS baseret rapportering

Som standard realtid placering rapportering du kun bruger baseret netværksplaceringer. Hvis du vil aktivere brugen af GPS baseret placeringer (som er langt mere præcise), skal du tilføje:

    [[EngagementAgent shared] setFineRealtimeLocationReport:YES];

#### <a name="background-reporting"></a>Rapportering af baggrund

Som standard er realtid placering rapportering kun aktivt, når programmet kører i forgrunden (det vil sige i en session). Hvis du vil aktivere rapporteringen også i baggrunden, skal du tilføje:

    [[EngagementAgent shared] setBackgroundRealtimeLocationReport:YES withLaunchOptions:launchOptions];

> [AZURE.NOTE] Når programmet kører i baggrunden, kun netværk baseret placeringer rapporteres, selvom du har aktiveret GPS.

Implementering af denne funktion kalder [startMonitoringSignificantLocationChanges] , når programmet er i baggrunden. Vær opmærksom på, at det vil automatisk start igen dit program i baggrunden Hvis du modtager en ny placering begivenhed.

##<a name="advanced-reporting"></a>Avanceret rapportering

Du kan også, hvis du vil rapportere bestemte programhændelser, fejl og job, du skal bruge aftale API gennem metoder til den `EngagementAgent` klasse. Et objekt i klassen kan hente den ved at ringe på `[EngagementAgent shared]` statisk metode.

Aftale API giver mulighed for at bruge alle aftales avancerede funktioner og er gennemgås i artiklen Sådan du kan bruge API'EN aftale på iOS (og i den tekniske dokumentation for den `EngagementAgent` klasse).

##<a name="disable-idfa-collection"></a>Deaktivere IDFA af websteder

Som standard bruger aftale [IDFA] til entydigt identificerer en bruger. Men hvis du ikke bruger reklame et andet sted i appen skal du muligvis afvist af App Store gennemsynsprocessen. IDFA af websteder kan deaktiveres ved at tilføje makroen preprocessor- `ENGAGEMENT_DISABLE_IDFA` i filen pch (eller i den `Build Settings` af dit program). Dette sikrer, at der er ingen referencer til `ASIdentifierManager`, `advertisingIdentifier` eller `isAdvertisingTrackingEnabled` i den nyeste version af programmet.

Integration i filen **prefix.pch** :

    #define ENGAGEMENT_DISABLE_IDFA
    ...

Du kan kontrollere, at samlingen IDFA er deaktiveret korrekt i programmet, ved at markere loggene aftale test. Se Integration teste\<ios-sdk-aftale-test-idfa\> dokumentation for yderligere oplysninger.

##<a name="disable-log-reporting"></a>Deaktivere log rapportering

### <a name="using-a-method-call"></a>Brug af en metode opkald

Hvis du vil aftale til ophøre med at sende logge, kan du ringe:

    [[EngagementAgent shared] setEnabled:NO];

Dette opkald er fast: bruges `NSUserDefaults` til lagring af oplysningerne.

Du kan aktivere log rapportering igen ved at ringe til den samme funktion med `YES`.

### <a name="integration-in-your-settings-bundle"></a>Integration i dine indstillinger for samlet

Du kan også integrere denne indstilling i stedet for opkald til denne funktion, direkte i din eksisterende `Settings.bundle` fil. Strengen `engagement_agent_enabled` skal bruges som en indstillings-id, og det skal være knyttet til en til/fra-switch (`PSToggleSwitchSpecifier`).

Følgende eksempel på `Settings.bundle` viser, hvordan du implementerer den:

    <dict>
        <key>PreferenceSpecifiers</key>
        <array>
            <dict>
                <key>DefaultValue</key>
                <true/>
                <key>Key</key>
                <string>engagement_agent_enabled</string>
                <key>Title</key>
                <string>Log reporting enabled</string>
                <key>Type</key>
                <string>PSToggleSwitchSpecifier</string>
            </dict>
        </array>
        <key>StringsTable</key>
        <string>Root</string>
    </dict>

<!-- URLs. -->
[Enhed API]: http://go.microsoft.com/?linkid=9876094
[NSLocationWhenInUseUsageDescription]:https://developer.apple.com/library/prerelease/ios/documentation/General/Reference/InfoPlistKeyReference/Articles/CocoaKeys.html#//apple_ref/doc/uid/TP40009251-SW26
[NSLocationAlwaysUsageDescription]:https://developer.apple.com/library/prerelease/ios/documentation/General/Reference/InfoPlistKeyReference/Articles/CocoaKeys.html#//apple_ref/doc/uid/TP40009251-SW18
[startMonitoringSignificantLocationChanges]:http://developer.apple.com/library/IOs/#documentation/CoreLocation/Reference/CLLocationManager_Class/CLLocationManager/CLLocationManager.html#//apple_ref/occ/instm/CLLocationManager/startMonitoringSignificantLocationChanges
[IDFA]:https://developer.apple.com/library/ios/documentation/AdSupport/Reference/ASIdentifierManager_Ref/ASIdentifierManager.html#//apple_ref/occ/instp/ASIdentifierManager/advertisingIdentifier
