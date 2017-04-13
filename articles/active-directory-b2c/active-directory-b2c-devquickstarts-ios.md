<properties
    pageTitle="Azure Active Directory-B2C: Kalde en web API fra en iOS-program ved hjælp af fra tredjepart biblioteker | Microsoft Azure"
    description="I denne artikel viser dig, hvordan du opretter en iOS 'opgaveliste' app, der kalder en Node.js web API ved hjælp af OAuth 2.0 bæreren tokens ved hjælp af et bibliotek fra tredjepart"
    services="active-directory-b2c"
    documentationCenter="ios"
    authors="brandwe"
    manager="mbaldwin"
    editor=""/>

< koder ms.service= "aktiv-directory-b2c" ms.workload="identity" ms.tgt_pltfrm="na" ms.devlang="objectivec" ms.topic= "helt-artikel"

    ms.date="07/26/2016"
    ms.author="brandwe"/>

# <a name="azure-ad-b2c--call-a-web-api-from-an-ios-application-using-a-third-party-library"></a>Azure AD-B2C: Ringe til en web API fra en iOS-program, ved hjælp af et bibliotek fra tredjepart

<!-- TODO [AZURE.INCLUDE [active-directory-b2c-devquickstarts-web-switcher](../../includes/active-directory-b2c-devquickstarts-web-switcher.md)]-->

Microsoft identitet platform bruger åbne standarder som OAuth2 og OpenID forbindelse. Dette giver mulighed for udviklere til at udnytte alle biblioteker, de vil du integrere systemet med vores tjenester. Vi har skrevet et par gennemgange som denne til demonstate som hjælp til udviklere ved hjælp af vores platform med andre biblioteker Sådan konfigureres fra tredjepart biblioteker til at oprette forbindelse til Microsoft identitet platform. De fleste biblioteker, der implementerer [RFC6749 OAuth2 specifikationen](https://tools.ietf.org/html/rfc6749) vil kunne oprette forbindelse til Microsoft Identity platform.


Hvis du er ny bruger af OAuth2 eller OpenID forbindelse kan meget af denne eksempel konfiguration ikke gøre meget mening for dig. Vi anbefaler, at du ser på en kort [Oversigt over den protokol, vi har beskrevet her](active-directory-b2c-reference-protocols.md).

> [AZURE.NOTE]
    Nogle funktioner i vores platform, der indeholder et udtryk i disse standarder, som betinget adgang og Intune politik management, skal du bruge vores Åbn kilde Microsoft Azure identitet biblioteker. 
   
Ikke alle Azure Active Directory scenarier og funktioner, der understøttes af B2C platform.  Læs mere om [B2C begrænsninger](active-directory-b2c-limitations.md)for at finde ud af, hvis du skal bruge B2C platformen.


## <a name="get-an-azure-ad-b2c-directory"></a>Få et Azure AD B2C directory

Før du kan bruge Azure AD B2C, skal du oprette en mappe eller lejer. En mappe er en beholder for alle dine brugere, apps, grupper og meget mere. Hvis du ikke har en allerede, fortsætter [oprette en B2C-mappe](active-directory-b2c-get-started.md) , inden du.

## <a name="create-an-application"></a>Oprette et program

Derefter skal du oprette en app i B2C-biblioteket. Dette giver Azure AD-oplysninger, som det skal kommunikere sikkert med din app. Både appen og World Wide web API er repræsenteret af et enkelt **Program-ID** i dette tilfælde, fordi de indeholder én logiske app. Hvis du vil oprette en app, skal du følge [disse instruktioner](active-directory-b2c-app-registration.md). Sørg for at:

- Medtage en **mobil enhed** i programmet.
- Kopiere **Program-ID** , der er tildelt din app. Du skal også dette senere.

[AZURE.INCLUDE [active-directory-b2c-devquickstarts-v2-apps](../../includes/active-directory-b2c-devquickstarts-v2-apps.md)]

## <a name="create-your-policies"></a>Oprette din politikker

I Azure AD B2C defineres hver brugeroplevelsen af en [politik](active-directory-b2c-reference-policies.md). Denne app indeholder én identitet oplevelse: et kombinerede tegn i og tilmelding. Du skal oprette denne politik skal skrives af hver type, som beskrevet i [politik referenceartikel](active-directory-b2c-reference-policies.md#how-to-create-a-sign-up-policy). Når du opretter politikken, skal du sørge for at:

- Vælg det **viste navn** og tilmelding attributter i din politik.
- Vælg de **viste navn** og **Objekt-ID** programmet krav i alle politikker. Du kan vælge samt andre krav.
- Kopiere og indsætte **navn** for hver politik, du har oprettet. Det skal have præfikset `b2c_1_`.  Du skal politikkens navn senere.

[AZURE.INCLUDE [active-directory-b2c-devquickstarts-policy](../../includes/active-directory-b2c-devquickstarts-policy.md)]

Når du har oprettet din politikker, er du klar til at opbygge din app.


## <a name="download-the-code"></a>Hente koden

Koden for dette selvstudium vedligeholdes [GitHub](https://github.com/Azure-Samples/active-directory-ios-native-nxoauth2-b2c).  Hvis du vil følge med, kan du [hente appen som en .zip](https://github.com/Azure-Samples/active-directory-ios-native-nxoauth2-b2c)/archive/master.zip) eller klone det:

```
git clone git@github.com:Azure-Samples/active-directory-ios-native-nxoauth2-b2c.git
```

Eller blot hente den færdige kode og komme i gang: 

```
git clone --branch complete git@github.com:Azure-Samples/active-directory-ios-native-nxoauth2-b2c.git
```

## <a name="download-the-third-party-library-nxoauth2-and-launch-a-workspace"></a>Hente fra tredjepart bibliotek nxoauth2 og Start et arbejdsområde

Vi bruger OAuth2Client fra GitHub, et OAuth2 bibliotek til Mac OS X- og iOS (kakao og kakao touch) til denne gennemgang. Dette bibliotek er baseret på kladde 10 af OAuth2 specifikation. Den implementerer den oprindelige programprofil og understøtter slutbrugerlicensaftale godkendelse slutpunkt. Dette er alle de ting, vi skal bruge i rækkefølge til integrat med Microsoft identitet platform.

### <a name="adding-the-library-to-your-project-using-cocoapods"></a>Tilføje biblioteket til dit projekt ved hjælp af CocoaPods

CocoaPods er leder for afhængighed for Xcode projekter. Den håndterer ovenfor installationstrin automatisk.

```
$ vi Podfile
```
Tilføj følgende denne podfile:

```
 platform :ios, '8.0'
 
 target 'SampleforB2C' do
 
 pod 'NXOAuth2Client'
 
 end
```

Nu indlæse podfile ved hjælp af cocoapods. Dette vil oprette et nyt XCode arbejdsområde, du vil indlæse.

```
$ pod install
...
$ open SampleforB2C.xcworkspace

```

## <a name="the-structure-of-the-project"></a>Strukturen i projektet

Vi har følgende struktur til vores project i skelet konfigurere:

* En **Mastervisning** med en opgaverude
* En **Tilføje opgavevisning** for dataene om den valgte opgave
* En **Login visning** , der gør det muligt for en bruger skal logge på appen.

Vi springer i til forskellige filer i projektet for at tilføje godkendelse. Andre dele af koden som den visuelle kode er ikke germane til identitet og er angivet for dig.

## <a name="create-the-settingsplist-file-for-your-application"></a>Oprette den `settings.plist` fil for dit program

Det er nemmere at konfigurere programmet, hvis vi har et centralt sted til at sætte vores konfiguration af værdier. Det også hjælper dig med at forstå, hvad hver enkelt indstilling betyder i programmet. Vi vil udnytte *Egenskabslisten* som en metode til at angive disse værdier til programmet.

* Opret eller Åbn den `settings.plist` fil `Supporting Files` i arbejdsområdet program

* Angiv i følgende værdier (går vi gennem dem detaljeret snart)

```xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE plist PUBLIC "-//Apple//DTD PLIST 1.0//EN" "http://www.apple.com/DTDs/PropertyList-1.0.dtd">
<plist version="1.0">
<dict>
    <key>accountIdentifier</key>
    <string>B2C_Acccount</string>
    <key>clientID</key>
    <string><client ID></string>
    <key>clientSecret</key>
    <string></string>
    <key>authURL</key>
    <string>https://login.microsoftonline.com/<tenant name>/oauth2/v2.0/authorize?p=<policy name></string>
    <key>loginURL</key>
    <string>https://login.microsoftonline.com/<tenant name>/login</string>
    <key>bhh</key>
    <string>urn:ietf:wg:oauth:2.0:oob</string>
    <key>tokenURL</key>
    <string>https://login.microsoftonline.com/<tenant name>/oauth2/v2.0/token?p=<policy name></string>
    <key>keychain</key>
    <string>com.microsoft.azureactivedirectory.samples.graph.QuickStart</string>
    <key>contentType</key>
    <string>application/x-www-form-urlencoded</string>
    <key>taskAPI</key>
    <string>https://aadb2cplayground.azurewebsites.net</string>
</dict>
</plist>
```

Lad os gå i disse i detaljer.


For `authURL`, `loginURL`, `bhh`, `tokenURL` vil du bemærke, du har brug at udfylde navnet på din lejer. Dette er lejer navnet på din B2C lejer, der er tildelt til dig. For eksempel `kidventusb2c.onmicrosoft.com`. Hvis du bruger vores Åbn kilde Microsoft Azure identitet biblioteker vil vi rullemenu med disse data ved hjælp af vores metadata slutpunkt. Vi har gjort det hårde arbejde af uddrage disse værdier for dig.

[AZURE.INCLUDE [active-directory-b2c-devquickstarts-tenant-name](../../includes/active-directory-b2c-devquickstarts-tenant-name.md)]

Den `keychain` værdi er objektbeholderen, biblioteket NXOAuth2Client vil bruge til at oprette en nøglering for at gemme dine tokens. Hvis du vil have SSO på tværs af flere apps kan du angive den samme nøglering i hver af dine programmer samt anmode om brugen af den pågældende nøglering i din XCode entitements. Dette er dækket i dokumentationen til Apple.

Den `<policy name>` i slutningen af hver URL-adresse er de steder, hvor du vil placere den politik, du oprettede ovenfor. Appen ringer politikkerne afhængigt af strømmen.

Den `taskAPI` er RESTEN slutpunktet vi kalder med din B2C token for enten tilføje opgaver eller eksisterende forespørgselsopgaver. Dette er konfigureret specielt til dette eksempel. Du behøver ikke at ændre det for eksempel til at arbejde.

Resten af disse værdier er påkrævet for at bruge biblioteket og blot oprette steder, hvor du kan udføre værdier for konteksten.

Nu hvor vi har den `settings.plist` fil, der er oprettet, vi brug for at læse den kode.

## <a name="set-up-a-appdata-class-to-read-our-settings"></a>Konfigurere en AppData klasse til at læse vores indstillinger

Lad os en enkelt fil, der kun fortolker vores `settngs.plist` fil vi oprettede ovenfor og gøre disse indstillinger avaialble i fremtiden til en hvilken som helst klasse. Da vi ikke vil oprette en ny kopi af dataene, hver gang en klasse beder om det, skal vi bruge en separat mønster og kun returnere den samme forekomst, der oprettes, hver gang der foretages en anmodning for indstillingerne

* Oprette en `AppData.h` fil:

```objc
#import <Foundation/Foundation.h>

@interface AppData : NSObject

@property(strong) NSString *accountIdentifier;
@property(strong) NSString *taskApiString;
@property(strong) NSString *authURL;
@property(strong) NSString *clientID;
@property(strong) NSString *loginURL;
@property(strong) NSString *bhh;
@property(strong) NSString *keychain;
@property(strong) NSString *tokenURL;
@property(strong) NSString *clientSecret;
@property(strong) NSString *contentType;

+ (id)getInstance;

@end
```

* Oprette en `AppData.m` fil:

```objc
#import "AppData.h"

@implementation AppData

+ (id)getInstance {
  static AppData *instance = nil;
  static dispatch_once_t onceToken;

  dispatch_once(&onceToken, ^{
    instance = [[self alloc] init];

    NSDictionary *dictionary = [NSDictionary
        dictionaryWithContentsOfFile:[[NSBundle mainBundle]
                                         pathForResource:@"settings"
                                                  ofType:@"plist"]];
    instance.accountIdentifier = [dictionary objectForKey:@"accountIdentifier"];
    instance.clientID = [dictionary objectForKey:@"clientID"];
    instance.clientSecret = [dictionary objectForKey:@"clientSecret"];
    instance.authURL = [dictionary objectForKey:@"authURL"];
    instance.loginURL = [dictionary objectForKey:@"loginURL"];
    instance.bhh = [dictionary objectForKey:@"bhh"];
    instance.tokenURL = [dictionary objectForKey:@"tokenURL"];
    instance.keychain = [dictionary objectForKey:@"keychain"];
    instance.contentType = [dictionary objectForKey:@"contentType"];
    instance.taskApiString = [dictionary objectForKey:@"taskAPI"];

  });

  return instance;
}
@end
```

Nu vi nemt kan få vores data ved at ringe til blot `  AppData *data = [AppData getInstance];` i en af vores klasser, som du får vist nedenfor.



## <a name="set-up-the-nxoauth2client-library-in-your-appdelegate"></a>Konfigurere biblioteket NXOAuth2Client i din AppDelegate

Biblioteket NXOAuthClient kræver nogle værdier for at komme i gang. Når du har fuldført du kan bruge tokenet, der er Brugerkonfigurationen til at ringe til REST-API. Da vi vide, at den `AppDelegate` bliver kaldt helst vi indlæse programmet det giver mening, vi placere vores konfiguration af værdier i til den pågældende fil.
* Åbn `AppDelegate.m` fil

* Importere nogle sidehoved-filer, vi vil bruge senere.

```objc
#import "NXOAuth2.h" // the Identity library we are using
#import "AppData.h" // the class we just created we will use to load the settings of our application
```

* Tilføje den `setupOAuth2AccountStore` metode i AppDelegate

Vi har brug at oprette en AccountStore og feed den de data, vi blot læse i fra den `settings.plist` fil.

Der er nogle ting, du skal være opmærksom på angående B2C tjenesten på dette tidspunkt, som får denne kode til flere forstå:


1. Azure AD-B2C bruger *politik* som angivet af parametrene til forespørgslen til at behandle din anmodning. Dette giver mulighed for Azure Active Directory skal fungere som en uafhængig tjeneste lige for dit program. For at give disse ekstra forespørgselsparametre vi skal levere den `kNXOAuth2AccountStoreConfigurationAdditionalAuthenticationParameters:` metode med vores brugerdefinerede politikindstillinger parametre. 

2. Azure AD-B2C bruger områder på næsten samme måde som andre OAuth2-servere. Da brugen af B2C er så meget om godkendelse af en bruger som at få adgang til ressourcer, der kræves nogle områder dog absolut for at fortsætte til fungerer korrekt. Dette er den `openid` omfang. Vores Microsoft identitet SDK'er automatisk at vise den `openid` omfang for dig, så du ikke kan se, i vores SDK konfiguration. Da vi bruger en tredjeparts bibliotek, men skal vi angive dette område.

```objc
- (void)setupOAuth2AccountStore {
  AppData *data = [AppData getInstance]; // The singleton we use to get the settings

  NSDictionary *customHeaders =
      [NSDictionary dictionaryWithObject:@"application/x-www-form-urlencoded"
                                  forKey:@"Content-Type"];

  // Azure B2C needs
  // kNXOAuth2AccountStoreConfigurationAdditionalAuthenticationParameters for
  // sending policy to the server,
  // therefore we use -setConfiguration:forAccountType:
  NSDictionary *B2cConfigDict = @{
    kNXOAuth2AccountStoreConfigurationClientID : data.clientID,
    kNXOAuth2AccountStoreConfigurationSecret : data.clientSecret,
    kNXOAuth2AccountStoreConfigurationScope :
        [NSSet setWithObjects:@"openid", data.clientID, nil],
    kNXOAuth2AccountStoreConfigurationAuthorizeURL :
        [NSURL URLWithString:data.authURL],
    kNXOAuth2AccountStoreConfigurationTokenURL :
        [NSURL URLWithString:data.tokenURL],
    kNXOAuth2AccountStoreConfigurationRedirectURL :
        [NSURL URLWithString:data.bhh],
    kNXOAuth2AccountStoreConfigurationCustomHeaderFields : customHeaders,
    //      kNXOAuth2AccountStoreConfigurationAdditionalAuthenticationParameters:customAuthenticationParameters
  };

  [[NXOAuth2AccountStore sharedStore] setConfiguration:B2cConfigDict
                                        forAccountType:data.accountIdentifier];
}
```
Dernæst skal du sørge for, at du ringer AppDelegate under `didFinishLaunchingWithOptions:` metode. 

```
[self setupOAuth2AccountStore];
```


## <a name="create-a-loginviewcontroller-class-that-we-will-use-to-handle-authentication-requests"></a>Oprette en `LoginViewController` klasse, vi vil bruge til at håndtere anmodninger om godkendelse

Vi bruger en webvisning for konto-logon. Dette gør det muligt for os til at spørge brugeren til flere faktorer som SMS tekstmeddelelse (hvis konfigureret) eller Giv fejlmeddelelser tilbage til brugeren. Her skal vi konfigurere op på webvisning og derefter skrive koden for at håndtere tilbagekald, sker der i webvisning fra tjenesten Microsoft identitet.

* Oprette en `LoginViewController.h` klasse

```objc
@interface LoginViewController : UIViewController <UIWebViewDelegate>
@property(weak, nonatomic) IBOutlet UIWebView *loginView; // Our webview that we will use to do authentication

- (void)handleOAuth2AccessResult:(NSURL *)accessResult; // Allows us to get a token after we've received an Access code.
- (void)setupOAuth2AccountStore; // We will need to add to our OAuth2AccountStore we setup in our AppDelegate
- (void)requestOAuth2Access; // This is where we invoke our webview.
```

Vi opretter hver enkelt af disse metoder nedenfor.

> [AZURE.NOTE] 
    Sørg for, at du binde den `loginView` til den faktiske webvisning, der er placeret mellem storyboardet. Ellers behøver du ikke en webvisning, der kan pop op, når det er tid til at godkende.

* Oprette en `LoginViewController.m` klasse

* Tilføje nogle variabler for at udføre tilstand, som vi godkende

```objc
NSURL *myRequestedUrl; \\ The URL request to Azure Active Directory 
NSURL *myLoadedUrl; \\ The URL loaded for Azure Active Directory
bool loginFlow = FALSE; 
bool isRequestBusy; \\ A way to give status to the thread that the request is still happening
NSURL *authcode; \\ A placeholder for our auth code.
```

* Tilsidesætte webvisning metoderne til at håndtere godkendelse

Vi vil se webvisning funktionsmåden vi vil, når en bruger har brug at logge på som beskrevet herover. Du kan blot klip og Indsæt koden nedenfor.

```objc
- (void)resolveUsingUIWebView:(NSURL *)URL {
  // We get the auth token from a redirect so we need to handle that in the
  // webview.

  if (![NSThread isMainThread]) {
    [self performSelectorOnMainThread:@selector(resolveUsingUIWebView:)
                           withObject:URL
                        waitUntilDone:YES];
    return;
  }

  NSURLRequest *hostnameURLRequest =
      [NSURLRequest requestWithURL:URL
                       cachePolicy:NSURLRequestUseProtocolCachePolicy
                   timeoutInterval:10.0f];
  isRequestBusy = YES;
  [self.loginView loadRequest:hostnameURLRequest];

  NSLog(@"resolveUsingUIWebView ready (status: UNKNOWN, URL: %@)",
        self.loginView.request.URL);
}

- (BOOL)webView:(UIWebView *)webView
    shouldStartLoadWithRequest:(NSURLRequest *)request
                navigationType:(UIWebViewNavigationType)navigationType {
  AppData *data = [AppData getInstance];

  NSLog(@"webView:shouldStartLoadWithRequest: %@ (%li)", request.URL,
        (long)navigationType);

  // The webview is where all the communication happens. Slightly complicated.

  myLoadedUrl = [webView.request mainDocumentURL];
  NSLog(@"***Loaded url: %@", myLoadedUrl);

  // if the UIWebView is showing our authorization URL or consent URL, show the
  // UIWebView control
  if ([request.URL.absoluteString rangeOfString:data.authURL
                                        options:NSCaseInsensitiveSearch]
          .location != NSNotFound) {
    self.loginView.hidden = NO;
  } else if ([request.URL.absoluteString rangeOfString:data.loginURL
                                               options:NSCaseInsensitiveSearch]
                 .location != NSNotFound) {
    // otherwise hide the UIWebView, we've left the authorization flow
    self.loginView.hidden = NO;
  } else if ([request.URL.absoluteString rangeOfString:data.bhh
                                               options:NSCaseInsensitiveSearch]
                 .location != NSNotFound) {
    // otherwise hide the UIWebView, we've left the authorization flow
    self.loginView.hidden = YES;
    [[NXOAuth2AccountStore sharedStore] handleRedirectURL:request.URL];
  } else {
    self.loginView.hidden = NO;
    // read the Location from the UIWebView, this is how Microsoft APIs is
    // returning the
    // authentication code and relation information. This is controlled by the
    // redirect URL we chose to use from Microsoft APIs
    // continue the OAuth2 flow
    // [[NXOAuth2AccountStore sharedStore] handleRedirectURL:request.URL];
  }

  return YES;
}

```

* Skrive kode til at håndtere resultatet af OAuth2 anmodningen

Vi skal kode, der håndterer redirectURL, der kommer tilbage fra webvisning. Hvis det ikke lykkes, forsøger vi igen. I mellemtiden give biblioteket fejl, kan du se i konsollen eller håndtere asyncronously. 

```objc
- (void)handleOAuth2AccessResult:(NSURL *)accessResult {
  // parse the response for success or failure
  if (accessResult)
  // if success, complete the OAuth2 flow by handling the redirect URL and
  // obtaining a token
  {
    [[NXOAuth2AccountStore sharedStore] handleRedirectURL:accessResult];
  } else {
    // start over
    [self requestOAuth2Access];
  }
}
```

* Konfigurere meddelelse virksomheder.

Vi oprette den samme metode, vi har gjort i den `AppDelegate` ovenfor, men denne gang, du skal tilføje nogle `NSNotification`s for at fortælle os, hvad sker der i vores tjeneste. Vi oprette en bruger, der fortæller os når alt er blevet ændret med tokenet. Når vi få tokenet vi returnere brugeren tilbage til den `masterView`.



```objc
- (void)setupOAuth2AccountStore {
  [[NSNotificationCenter defaultCenter]
      addObserverForName:NXOAuth2AccountStoreAccountsDidChangeNotification
                  object:[NXOAuth2AccountStore sharedStore]
                   queue:nil
              usingBlock:^(NSNotification *aNotification) {
                if (aNotification.userInfo) {
                  // account added, we have access
                  // we can now request protected data
                  NSLog(@"Success!! We have an access token.");
                  dispatch_async(dispatch_get_main_queue(), ^{

                    MasterViewController *masterViewController =
                        [self.storyboard
                            instantiateViewControllerWithIdentifier:@"master"];
                    [self.navigationController
                        pushViewController:masterViewController
                                  animated:YES];
                  });
                } else {
                  // account removed, we lost access
                }
              }];

  [[NSNotificationCenter defaultCenter]
      addObserverForName:NXOAuth2AccountStoreDidFailToRequestAccessNotification
                  object:[NXOAuth2AccountStore sharedStore]
                   queue:nil
              usingBlock:^(NSNotification *aNotification) {
                NSError *error = [aNotification.userInfo
                    objectForKey:NXOAuth2AccountStoreErrorKey];
                NSLog(@"Error!! %@", error.localizedDescription);
              }];
}

```
* Tilføje kode, der håndterer brugeren, når en anmodning om startes for Log oprindelig

Lad os oprette en metode, der kaldes, når vi har en anmodning om godkendelse. Dette er den metode, der faktisk opretter en webvisning

```objc
- (void)requestOAuth2Access {
  AppData *data = [AppData getInstance];

  // in order to login to Mircosoft APIs using OAuth2 we must show an embedded
  // browser (UIWebView)
  [[NXOAuth2AccountStore sharedStore]
           requestAccessToAccountWithType:data.accountIdentifier
      withPreparedAuthorizationURLHandler:^(NSURL *preparedURL) {
        // navigate to the URL returned by NXOAuth2Client

        NSURLRequest *r = [NSURLRequest requestWithURL:preparedURL];
        [self.loginView loadRequest:r];
      }];
}
```

* Lad os ringe til sidst skal alle disse metoder, vi har skrevet over hver gang den `LoginViewController` indlæses. Vi gør dette ved at tilføje disse metoder til at vores `viewDidLoad` metode Apple giver os

```objc
  [super viewDidLoad];
  // Do any additional setup after loading the view.

  // OAuth2 Code

  self.loginView.delegate = self;
  [self requestOAuth2Access];
  [self setupOAuth2AccountStore];
  NSURLCache *URLCache =
      [[NSURLCache alloc] initWithMemoryCapacity:4 * 1024 * 1024
                                    diskCapacity:20 * 1024 * 1024
                                        diskPath:nil];
  [NSURLCache setSharedURLCache:URLCache];
```

Du er nu færdig med at oprette den primære måde, som vi skal interagere med vores program til logon. Når vi har logget på, skal vi bruge vores tokens, vi har modtaget. For den pågældende skal vi oprette hjælper kode, der ringer REST API'er til os ved at bruge dette bibliotek.


## <a name="create-a-graphapicaller-class-to-handle-our-requests-to-a-rest-api"></a>Oprette en `GraphAPICaller` klasse til at håndtere vores anmodninger om til en REST-API

Vi har en konfiguration, der indlæses, hver gang vi indlæse vores app. Nu har vi brug at gøre noget med dem, når vi har et token. 

* Oprette en `GraphAPICaller.h` fil

```objc
@interface GraphAPICaller : NSObject <NSURLConnectionDataDelegate>

+ (void)addTask:(Task *)task
completionBlock:(void (^)(bool, NSError *error))completionBlock;

+ (void)getTaskList:(void (^)(NSMutableArray *, NSError *error))completionBlock;

@end
```

Du se fra denne kode, vi vil oprette to metoder: et til at hente opgaverne fra en API og en anden til at føje opgaver til API.

Nu hvor vi har konfigureret vores grænseflade, Lad os tilføje faktiske gennemførelse:

* Oprette en`GraphAPICaller.m file`

```objc
@implementation GraphAPICaller

// 
// Gets the tasks from our REST endpoint we specified in settings
//

+ (void)getTaskList:(void (^)(NSMutableArray *, NSError *))completionBlock

{
  AppData *data = [AppData getInstance];

  NSString *taskURL =
      [NSString stringWithFormat:@"%@%@", data.taskApiString, @"/api/tasks"];

  NXOAuth2AccountStore *store = [NXOAuth2AccountStore sharedStore];
  NSMutableArray *Tasks = [[NSMutableArray alloc] init];

  NSArray *accounts = [store accountsWithAccountType:data.accountIdentifier];
  [NXOAuth2Request performMethod:@"GET"
      onResource:[NSURL URLWithString:taskURL]
      usingParameters:nil
      withAccount:accounts[0]
      sendProgressHandler:^(unsigned long long bytesSend,
                            unsigned long long bytesTotal) {
        // e.g., update a progress indicator
      }
      responseHandler:^(NSURLResponse *response, NSData *responseData,
                        NSError *error) {
        // Process the response
        if (!error) {
          NSDictionary *dataReturned =
              [NSJSONSerialization JSONObjectWithData:responseData
                                              options:0
                                                error:nil];
          NSLog(@"Graph Response was: %@", dataReturned);

          if ([dataReturned count] != 0) {

            for (NSMutableDictionary *theTask in dataReturned) {

              Task *t = [[Task alloc] init];
              t.name = [theTask valueForKey:@"Text"];

              [Tasks addObject:t];
            }
          }

          completionBlock(Tasks, nil);
        } else {
          completionBlock(nil, error);
        }

      }];
}

// 
// Adds a task from our REST endpoint we specified in settings
//

+ (void)addTask:(Task *)task
completionBlock:(void (^)(bool, NSError *error))completionBlock {

  AppData *data = [AppData getInstance];

  NSString *taskURL =
      [NSString stringWithFormat:@"%@%@", data.taskApiString, @"/api/tasks"];

  NXOAuth2AccountStore *store = [NXOAuth2AccountStore sharedStore];
  NSDictionary *params = [self convertParamsToDictionary:task.name];

  NSArray *accounts = [store accountsWithAccountType:data.accountIdentifier];
  [NXOAuth2Request performMethod:@"POST"
      onResource:[NSURL URLWithString:taskURL]
      usingParameters:params
      withAccount:accounts[0]
      sendProgressHandler:^(unsigned long long bytesSend,
                            unsigned long long bytesTotal) {
        // e.g., update a progress indicator
      }
      responseHandler:^(NSURLResponse *response, NSData *responseData,
                        NSError *error) {
        // Process the response
        if (responseData) {
          NSDictionary *dataReturned =
              [NSJSONSerialization JSONObjectWithData:responseData
                                              options:0
                                                error:nil];
          NSLog(@"Graph Response was: %@", dataReturned);

          completionBlock(TRUE, nil);
        } else {
          completionBlock(FALSE, error);
        }

      }];
}

+ (NSDictionary *)convertParamsToDictionary:(NSString *)task {
  NSMutableDictionary *dictionary = [[NSMutableDictionary alloc] init];

  [dictionary setValue:task forKey:@"Text"];

  return dictionary;
}

@end
```

## <a name="run-the-sample-app"></a>Køre appen eksempel

Til sidst skal oprette og køre appen i Xcode. Tilmelde dig eller logge på appen, og oprette opgaver for en bruger, der er logget på. Log af og logge på igen som en anden bruger, og oprette opgaver for den pågældende bruger.

Bemærk, at opgaverne, der er lagrede brugerniveau på API, fordi API henter brugerens identitet fra adgangstoken, den modtager.


## <a name="next-steps"></a>Næste trin

Nu kan du flytte til mere avancerede B2C emner. Du kan prøve:

[Ringe til en Node.js web API fra en Node.js WebApp]()

[Tilpasse UX til en B2C-app]()
