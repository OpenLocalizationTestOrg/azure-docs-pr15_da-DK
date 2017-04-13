<properties
    pageTitle="Azure AD v2.0 iOS App | Microsoft Azure"
    description="Sådan oprettes en iOS-app, der logger på brugere med begge personlig Microsoft-konto og arbejds- eller skolekonto konti ved hjælp af tredjeparters biblioteker."
    services="active-directory"
    documentationCenter=""
    authors="brandwe"
    manager="mbaldwin"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="mobile-ios"
    ms.devlang="objective-c"
    ms.topic="article"
    ms.date="06/28/2016"
    ms.author="brandwe"/>

# <a name="add-sign-in-to-an-ios-app-using-a-third-party-library-with-graph-api-using-the-v20-endpoint"></a>Føje-logon til en iOS-app, ved hjælp af en tredjepartsudbyder af bibliotek med Graph API ved hjælp af slutpunktet v2.0

Microsoft identitet platform bruger åbne standarder som OAuth2 og OpenID forbindelse. Udviklere kan bruge enhver type bibliotek, de vil du integrere systemet med vores tjenester. For at hjælpe udviklere med at bruge vores platform med andre biblioteker, har vi skrevet et par gennemgange som denne til at vise, hvordan du konfigurerer tredjeparts-biblioteker til at oprette forbindelse til Microsoft identitet platform. De fleste biblioteker, der implementerer [RFC6749 OAuth2 specifikationen](https://tools.ietf.org/html/rfc6749) kan oprette forbindelse til Microsoft identitet platform.

Brugere kan logge på deres organisation og derefter søge efter andre i deres organisation ved hjælp af Graph API med det program, der opretter denne gennemgang.

Hvis du er ny bruger af OAuth2 eller OpenID oprette forbindelse, kan meget af denne eksempel konfiguration ikke giver mening for dig. Vi anbefaler, at du læser [v2.0 protokoller - OAuth 2.0 godkendelse kode dataflow](active-directory-v2-protocols-oauth-code.md) som baggrund.


> [AZURE.NOTE]
    Nogle funktioner i vores platform, som har et udtryk i OAuth2 eller OpenID forbinde standarderne, såsom betinget adgang og administration af Intune politikker, skal du bruge vores Åbn kilde Microsoft Azure identitet biblioteker.

V2.0 slutpunktet understøtter ikke alle Azure Active Directory scenarier og funktioner.

> [AZURE.NOTE]
    For at bestemme, hvis du skal bruge v2.0 slutpunkt skal du læse om [v2.0 begrænsninger](active-directory-v2-limitations.md).

## <a name="download-code-from-github"></a>Hent kode fra GitHub
Koden for dette selvstudium vedligeholdes [GitHub](https://github.com/Azure-Samples/active-directory-ios-native-nxoauth2-v2).  Hvis du vil følge med, kan du [hente den app skelet som en .zip](https://github.com/AzureADQuickStarts/AppModelv2-WebAPI-DotNet/archive/skeleton.zip) eller klone skelet:

```
git clone --branch skeleton git@github.com:Azure-Samples/active-directory-ios-native-nxoauth2-v2.git
```

Du kan også bare hente stikprøvernes og komme i gang:

```
git clone git@github.com:Azure-Samples/active-directory-ios-native-nxoauth2-v2.git
```

## <a name="register-an-app"></a>Registrere en app
Oprette en ny app på [programmet registrering portal](https://apps.dev.microsoft.com/?referrer=https://azure.microsoft.com/documentation/articles&deeplink=/appList), eller Følg de detaljerede trin, [hvordan man kan registrere en app til v2.0 slutpunktet](active-directory-v2-app-registration.md).  Sørg for at:

- Kopiere **Program-Id** , der er tildelt din app, fordi du skal bruge den snart.
- Tilføj **Mobile** platformen til din app.
- Kopiér **Omdirigere URI** fra portalen. Du skal bruge standardværdien for `urn:ietf:wg:oauth:2.0:oob`.


## <a name="download-the-third-party-nxoauth2-library-and-create-a-workspace"></a>Hente biblioteket fra tredjepart NXOAuth2 og oprette et arbejdsområde

I denne gennemgang skal bruge du OAuth2Client fra GitHub, som er et OAuth2 bibliotek til Mac OS X og iOS (kakao og kakao rører). Dette bibliotek er baseret på kladde 10 af OAuth2 specifikation. Den implementerer den oprindelige programprofil og understøtter godkendelse slutpunktet på brugeren. Dette er alle de ting, du skal integreres med Microsoft identitet platforme.

### <a name="add-the-library-to-your-project-by-using-cocoapods"></a>Tilføje biblioteket til dit projekt ved hjælp af CocoaPods

CocoaPods er leder for afhængighed for Xcode projekter. Den håndterer de forrige installationstrin automatisk.

```
$ vi Podfile
```
1. Tilføj følgende denne podfile:

    ```
     platform :ios, '8.0'

     target 'QuickStart' do

     pod 'NXOAuth2Client'

     end
    ```

2. Indlæse podfile ved hjælp af CocoaPods. Dette vil oprette et nyt Xcode arbejdsområde, du vil indlæse.

    ```
    $ pod install
    ...
    $ open QuickStart.xcworkspace
    ```

## <a name="explore-the-structure-of-the-project"></a>Udforske strukturen i projektet

Følgende struktur er konfigureret til vores project i skelet:

- En mastervisning med en UPN-søgning
- En detaljeret visning for dataene, om den valgte bruger
- En logon-visning, hvor en bruger kan logge på appen til at forespørge grafen

Vi vil flytte til forskellige filer i skelet tilføje godkendelse. Andre dele af koden, som den visuelle kode, der ikke vedrører identitet, men er angivet for dig.

## <a name="set-up-the-settingsplst-file-in-the-library"></a>Konfigurere settings.plst filen i biblioteket

-   Åbn i project Hurtig start på `settings.plist` fil. Erstatte værdier af elementer i afsnittet for at afspejle de værdier, du har brugt i portalen Azure. Din kode refererer disse værdier, når den anvender Active Directory Authentication Library.
    -   Den `clientId` er klient-ID'ET for dit program, du kopierede fra portalen.
    -   Den `redirectUri` er Omdiriger URL-adresse, der bruges af portalen.

## <a name="set-up-the-nxoauth2client-library-in-your-loginviewcontroller"></a>Konfigurere biblioteket NXOAuth2Client i din LoginViewController

Biblioteket NXOAuth2Client kræver nogle værdier for at komme i gang. Når du udfører denne opgave, kan du bruge anskaffet tokenet til at ringe til Graph API. Da `LoginView` bliver kaldet helst vi brug for at godkende det giver mening at placere konfiguration af værdier i til den pågældende fil.

- Lad os tilføje nogle værdier til den `LoginViewController.m` fil for at angive kontekst for godkendelse og autorisation. Oplysninger om værdierne Følg koden.

    ```objc
    NSString *scopes = @"openid offline_access User.Read";
    NSString *authURL = @"https://login.microsoftonline.com/common/oauth2/v2.0/authorize";
    NSString *loginURL = @"https://login.microsoftonline.com/common/login";
    NSString *bhh = @"urn:ietf:wg:oauth:2.0:oob?code=";
    NSString *tokenURL = @"https://login.microsoftonline.com/common/oauth2/v2.0/token";
    NSString *keychain = @"com.microsoft.azureactivedirectory.samples.graph.QuickStart";
    static NSString * const kIDMOAuth2SuccessPagePrefix = @"session_state=";
    NSURL *myRequestedUrl;
    NSURL *myLoadedUrl;
    bool loginFlow = FALSE;
    bool isRequestBusy;
    NSURL *authcode;
    ```

Lad os se på oplysninger om koden.

Den første streng er `scopes`.  Den `User.Read` værdi gør det muligt at læse den grundlæggende profil for den bruger, der er logget på.

Du kan få mere at vide om alle de tilgængelige områder på [Microsoft Graph tilladelse områder](https://graph.microsoft.io/docs/authorization/permission_scopes).

For `authURL`, `loginURL`, `bhh`, og `tokenURL`, skal du bruge de værdier, som tidligere. Hvis du bruger Åbn kilden Microsoft Azure identitet biblioteker, rullemenu vi med disse data for dig ved hjælp af vores metadata slutpunkt. Vi har gjort det hårde arbejde af udtrække disse værdier for dig.

Den `keychain` værdi er objektbeholderen, biblioteket NXOAuth2Client vil bruge til at oprette en nøglering for at gemme dine tokens. Hvis du vil have enkeltlogon (SSO) på tværs af flere apps, kan du angive de samme nøglering i hver af dine programmer og anmode om brugen af den pågældende nøglering i din Xcode rettigheder. Det er beskrevet i dokumentationen til Apple.

Resten af disse værdier er påkrævet for at bruge biblioteket og oprette steder, hvor du kan udføre værdier for konteksten.

### <a name="create-a-url-cache"></a>Oprette en URL-adressecache

I `(void)viewDidLoad()`, som kaldes altid, når visningen er indlæst, følgende kode primes en cache til vores brug.

Føj følgende kode:

```objc
- (void)viewDidLoad {
    [super viewDidLoad];
    self.loginView.delegate = self;
    [self setupOAuth2AccountStore];
    [self requestOAuth2Access];
    NSURLCache *URLCache = [[NSURLCache alloc] initWithMemoryCapacity:4 * 1024 * 1024
                                                         diskCapacity:20 * 1024 * 1024
                                                             diskPath:nil];
    [NSURLCache setSharedURLCache:URLCache];

}
```

### <a name="create-a-webview-for-sign-in"></a>Oprette en webvisning til logon

En webvisning kan brugeren flere faktorer som SMS tekstmeddelelse (hvis konfigureret) eller returnere fejlmeddelelser til brugeren. Her skal du konfigurere op på webvisning og derefter skrive koden for at håndtere tilbagekald, sker der i webvisning fra identitet tjenesterne.

```objc
-(void)requestOAuth2Access {
    //to sign in to Microsoft APIs using OAuth2, we must show an embedded browser (UIWebView)
    [[NXOAuth2AccountStore sharedStore] requestAccessToAccountWithType:@"myGraphService"
                                   withPreparedAuthorizationURLHandler:^(NSURL *preparedURL) {
                                       //navigate to the URL returned by NXOAuth2Client

                                       NSURLRequest *r = [NSURLRequest requestWithURL:preparedURL];
                                       [self.loginView loadRequest:r];
                                   }];
}
```

### <a name="override-the-webview-methods-to-handle-authentication"></a>Tilsidesætte webvisning metoderne til at håndtere godkendelse

Hvis du vil se webvisning, hvad der sker, når en bruger skal logge på, som tidligere nævnt, kan du indsætte følgende kode.

```objc
- (void)resolveUsingUIWebView:(NSURL *)URL {

    // We get the auth token from a redirect so we need to handle that in the webview.

    if (![NSThread isMainThread]) {
        [self performSelectorOnMainThread:@selector(resolveUsingUIWebView:) withObject:URL waitUntilDone:YES];
        return;
    }

    NSURLRequest *hostnameURLRequest = [NSURLRequest requestWithURL:URL cachePolicy:NSURLRequestUseProtocolCachePolicy timeoutInterval:10.0f];
    isRequestBusy = YES;
    [self.loginView loadRequest:hostnameURLRequest];

    NSLog(@"resolveUsingUIWebView ready (status: UNKNOWN, URL: %@)", self.loginView.request.URL);
}

- (BOOL)webView:(UIWebView *)webView shouldStartLoadWithRequest:(NSURLRequest *)request navigationType:(UIWebViewNavigationType)navigationType {

    NSLog(@"webView:shouldStartLoadWithRequest: %@ (%li)", request.URL, (long)navigationType);

    // The webview is where all the communication happens. Slightly complicated.

    myLoadedUrl = [webView.request mainDocumentURL];
    NSLog(@"***Loaded url: %@", myLoadedUrl);

    //if the UIWebView is showing our authorization URL or consent URL, show the UIWebView control
    if ([request.URL.absoluteString rangeOfString:authURL options:NSCaseInsensitiveSearch].location != NSNotFound) {
        self.loginView.hidden = NO;
    } else if ([request.URL.absoluteString rangeOfString:loginURL options:NSCaseInsensitiveSearch].location != NSNotFound) {
        //otherwise hide the UIWebView, we've left the authorization flow
        self.loginView.hidden = NO;
    } else if ([request.URL.absoluteString rangeOfString:bhh options:NSCaseInsensitiveSearch].location != NSNotFound) {
        //otherwise hide the UIWebView, we've left the authorization flow
        self.loginView.hidden = YES;
        [[NXOAuth2AccountStore sharedStore] handleRedirectURL:request.URL];
    }
    else {
        self.loginView.hidden = NO;
        //read the Location from the UIWebView, this is how Microsoft APIs is returning the
        //authentication code and relation information. This is controlled by the redirect URL we chose to use from Microsoft APIs
        //continue the OAuth2 flow
       // [[NXOAuth2AccountStore sharedStore] handleRedirectURL:request.URL];
    }

    return YES;

}
```

### <a name="write-code-to-handle-the-result-of-the-oauth2-request"></a>Skrive kode til at håndtere resultatet af OAuth2 anmodningen

Følgende kode håndterer redirectURL, der returnerer fra webvisning. Hvis godkendelse ikke lykkes, forsøger koden igen. Du kan give biblioteket fejlen, som du kan se i konsollen eller håndtere asynkront.

```objc
- (void)handleOAuth2AccessResult:(NSString *)accessResult {

    AppData* data = [AppData getInstance];

    //parse the response for success or failure
     if (accessResult)
    //if success, complete the OAuth2 flow by handling the redirect URL and obtaining a token
     {
         [[NXOAuth2AccountStore sharedStore] handleRedirectURL:accessResult];
    } else {
        //start over
        [self requestOAuth2Access];
    }
}
```

### <a name="set-up-the-oauth-context-called-account-store"></a>Konfigurere konteksten OAuth (kaldet konto store)

Her kan du ringe `-[NXOAuth2AccountStore setClientID:secret:authorizationURL:tokenURL:redirectURL:forAccountType:]` på delte konto store for hver tjeneste, som du ønsker, at programmet skal kunne få adgang til. Kontotypen er en streng, der bruges som et id for en bestemt tjeneste. Fordi du har adgang til API Graph, koden refererer til den som `"myGraphService"`. Du derefter oprette en bruger, der fortæller dig, når noget ændret med tokenet. Når du får tokenet, du vender tilbage brugeren tilbage til den `masterView`.



```objc
- (void)setupOAuth2AccountStore {


        AppData* data = [AppData getInstance];

    [[NXOAuth2AccountStore sharedStore] setClientID:data.clientId
                                             secret:data.secret
                                              scope:[NSSet setWithObject:scopes]
                                   authorizationURL:[NSURL URLWithString:authURL]
                                           tokenURL:[NSURL URLWithString:tokenURL]
                                        redirectURL:[NSURL URLWithString:data.redirectUriString]
                                      keyChainGroup: keychain
                                     forAccountType:@"myGraphService"];

    [[NSNotificationCenter defaultCenter] addObserverForName:NXOAuth2AccountStoreAccountsDidChangeNotification
                                                      object:[NXOAuth2AccountStore sharedStore]
                                                       queue:nil
                                                  usingBlock:^(NSNotification *aNotification) {
                                                      if (aNotification.userInfo) {
                                                          //account added, we have access
                                                          //we can now request protected data
                                                          NSLog(@"Success!! We have an access token.");
                                                          dispatch_async(dispatch_get_main_queue(),^ {

                                                              MasterViewController* masterViewController = [self.storyboard instantiateViewControllerWithIdentifier:@"masterView"];
                                                              [self.navigationController pushViewController:masterViewController animated:YES];
                                                          });
                                                      } else {
                                                          //account removed, we lost access
                                                      }
                                                  }];

    [[NSNotificationCenter defaultCenter] addObserverForName:NXOAuth2AccountStoreDidFailToRequestAccessNotification
                                                      object:[NXOAuth2AccountStore sharedStore]
                                                       queue:nil
                                                  usingBlock:^(NSNotification *aNotification) {
                                                      NSError *error = [aNotification.userInfo objectForKey:NXOAuth2AccountStoreErrorKey];
                                                      NSLog(@"Error!! %@", error.localizedDescription);
                                                  }];
}
```

## <a name="set-up-the-master-view-to-search-and-display-the-users-from-the-graph-api"></a>Konfigurere visningen Master for at søge og få vist brugere fra Graph API

En Master-visning-Controller (MVC)-app, der viser de returnerede data i gitteret er ikke medtaget i denne gennemgang, og mange online selvstudier beskriver, hvordan du opretter en. Denne kode er i filen skelet. Men du har brug for at håndtere et par ting i MVC programmet:

* SKÆRING, når en bruger skriver noget i søgefeltet
* Give et objekt med data tilbage til MasterView, så den kan få vist resultaterne i gitteret

Vi bruger dem nedenfor.

### <a name="add-a-check-to-see-if-youre-logged-in"></a>Tilføje et afkrydsningsfelt for at se, hvis du er logget på

Programmet gør, lille Hvis brugeren ikke er logget på, så det er smart at kontrollere, om der er allerede et token i cachen. Hvis ikke, du omdirigerer til LoginView for brugeren, til at logge på. Hvis du huske, er den bedste måde at foretage handlinger, når en visning indlæses at bruge den `viewDidLoad()` afskrivningsmetode, som Apple giver os.

```objc
- (void)viewDidLoad {
    [super viewDidLoad];


    NXOAuth2AccountStore *store = [NXOAuth2AccountStore sharedStore];
    NSArray *accounts = [store accountsWithAccountType:@"myGraphService"];

        if (accounts.count == 0) {

        dispatch_async(dispatch_get_main_queue(),^ {

            LoginViewController* userSelectController = [self.storyboard instantiateViewControllerWithIdentifier:@"LoginUserView"];
            [self.navigationController pushViewController:userSelectController animated:YES];
        });
        }
```

### <a name="update-the-table-view-when-data-is-received"></a>Opdatere visningen tabel, når der modtages data

Når Graph API returnerer data, vil du have vist dataene. Enkel er her alle i koden for at opdatere tabellen. Du kan kun indsætte de rigtige værdier i din MVC dele af et kode.

```objc
#pragma mark - Table View

- (NSInteger)numberOfSectionsInTableView:(UITableView *)tableView {
    return 1;
}

- (NSInteger)tableView:(UITableView *)tableView numberOfRowsInSection:(NSInteger)section {

        return [upnArray count];
}

- (UITableViewCell *)tableView:(UITableView *)tableView cellForRowAtIndexPath:(NSIndexPath *)indexPath {

    UITableViewCell *cell = [tableView dequeueReusableCellWithIdentifier:@"TaskPrototypeCell" forIndexPath:indexPath];

    if ( cell == nil ) {
        cell = [[UITableViewCell alloc] initWithStyle:UITableViewCellStyleDefault reuseIdentifier:@"TaskPrototypeCell"];
    }

    User *user = nil;
     user = [upnArray objectAtIndex:indexPath.row];


    // Configure the cell
    cell.textLabel.text = user.name;
    [cell setAccessoryType:UITableViewCellAccessoryDisclosureIndicator];

    return cell;
}

```

### <a name="provide-a-way-to-call-the-graph-api-when-someone-types-in-the-search-field"></a>Ikke en metode til at ringe Graph API, når nogen skriver i søgefeltet

Når en bruger skriver en søgning i feltet, skal du shove, til Graph API. Den `GraphAPICaller` klasse, som du vil oprette i den følgende kode, adskiller funktionen opslag fra præsentationen. Nu skal Lad os skrive den kode, der feeds Søg tegn til Graph API. Vi gør dette ved at indsende en metode med navnet `lookupInGraph`, som tager den streng, som vi vil søge efter.

```objc

-(void)lookupInGraph:(NSString *)searchText {
if (searchText.length > 0) {

    };



        [GraphAPICaller searchUserList:searchText completionBlock:^(NSMutableArray* returnedUpns, NSError* error) {
            if (returnedUpns) {


                upnArray = returnedUpns;


            }
            else
            {
                UIAlertView *alertView = [[UIAlertView alloc]initWithTitle:nil message:[[NSString alloc]initWithFormat:@"Error : %@", error.localizedDescription] delegate:nil cancelButtonTitle:@"Retry" otherButtonTitles:@"Cancel", nil];

                [alertView setDelegate:self];

                dispatch_async(dispatch_get_main_queue(),^ {
                    [alertView show];
                });
            }


        }];


}
```

## <a name="write-a-helper-class-to-access-the-graph-api"></a>Skrive en hjælpeklasse for at få adgang til API Graph

Dette er kernen i vores program. Mens resten indsat koden i standard MVC mønster fra Apple, skrive her du programkode for at forespørge diagrammet som brugeren skriver og derefter returnere dataene. Her er koden, og en detaljeret forklaring følger den.

### <a name="create-a-new-objective-c-header-file"></a>Oprette en ny målsætning C sidehoved fil

Navngiv filen `GraphAPICaller.h`, og Føj følgende kode.

```objc
@interface GraphAPICaller : NSObject<NSURLConnectionDataDelegate>

+(void) searchUserList:(NSString*)searchString
       completionBlock:(void (^) (NSMutableArray*, NSError* error))completionBlock;

@end
```

Her kan du se, at en bestemt metode tager en streng, og returnerer en completionBlock. Denne completionBlock, opdateres som du muligvis har gættet, tabellen ved at give et objekt på forhånd data i realtid som bruger søgninger.


### <a name="create-a-new-objective-c-file"></a>Oprette en ny mål C-fil

Navngiv filen `GraphAPICaller.m`, og Tilføj følgende metode.

```objc
+(void) searchUserList:(NSString*)searchString
       completionBlock:(void (^) (NSMutableArray* Users, NSError* error)) completionBlock
{
    if (!loadedApplicationSettings)
    {
        [self readApplicationSettings];
    }

    AppData* data = [AppData getInstance];

    NSString *graphURL = [NSString stringWithFormat:@"%@%@/users", data.graphApiUrlString, data.apiversion];

    NXOAuth2AccountStore *store = [NXOAuth2AccountStore sharedStore];
    NSDictionary* params = [self convertParamsToDictionary:searchString];

    NSArray *accounts = [store accountsWithAccountType:@"myGraphService"];
    [NXOAuth2Request performMethod:@"GET"
                        onResource:[NSURL URLWithString:graphURL]
                   usingParameters:params
                       withAccount:accounts[0]
               sendProgressHandler:^(unsigned long long bytesSend, unsigned long long bytesTotal) {
                   // e.g., update a progress indicator
               }
                   responseHandler:^(NSURLResponse *response, NSData *responseData, NSError *error) {
                       // Process the response
                       if (responseData) {
                           NSError *error;
                           NSDictionary *dataReturned = [NSJSONSerialization JSONObjectWithData:responseData options:0 error:nil];
                           NSLog(@"Graph Response was: %@", dataReturned);

                           // We can grab the top most JSON node to get our graph data.
                           NSArray *graphDataArray = [dataReturned objectForKey:@"value"];

                           // Don't be thrown off by the key name being "value". It really is the name of the
                           // first node. :-)

                           //each object is a key value pair
                           NSDictionary *keyValuePairs;
                           NSMutableArray* Users = [[NSMutableArray alloc]init];

                           for(int i =0; i < graphDataArray.count; i++)
                           {
                               keyValuePairs = [graphDataArray objectAtIndex:i];

                               User *s = [[User alloc]init];
                               s.upn = [keyValuePairs valueForKey:@"userPrincipalName"];
                               s.name =[keyValuePairs valueForKey:@"displayName"];
                               s.mail =[keyValuePairs valueForKey:@"mail"];
                               s.businessPhones =[keyValuePairs valueForKey:@"businessPhones"];
                               s.mobilePhones =[keyValuePairs valueForKey:@"mobilePhone"];


                               [Users addObject:s];
                           }

                           completionBlock(Users, nil);
                       }
                       else
                       {
                           completionBlock(nil, error);
                       }

                   }];
}

```

Lad os gennemgå denne metode detaljeret.

Kernen i denne kode er i den `NXOAuth2Request`, metode, der tager for parametrene, du har allerede angivet i filen settings.plist.

Det første trin er at opbygge højre Graph API opkaldet. Da du ringer `/users`, du angiver, ved at føje det til Graph API ressourcen sammen med versionen. Det giver mening at sætte dem i en fil med eksterne indstillinger, da disse kan ændres, i takt API udvikler sig.


```objc
NSString *graphURL = [NSString stringWithFormat:@"%@%@/users", data.graphApiUrlString, data.apiversion];
```

Derefter skal du angive parametre, som du kan også give Graph API opkaldet. Det er *meget vigtigt* , at du ikke placerer parametrene i slutpunktet ressource, fordi, der er scrubbed for alle ikke-URI opfylder kravene tegn på kørselstidspunktet. Alle forespørgsel kode skal angives i parametre.

```objc

NSDictionary* params = [self convertParamsToDictionary:searchString];
```

Du oplever måske dette kalder en `convertParamsToDictionary` afskrivningsmetode, som du endnu ikke har skrevet. Lad os gøre det nu i slutningen af filen:

```objc
+(NSDictionary*) convertParamsToDictionary:(NSString*)searchString
{
    NSMutableDictionary* dictionary = [[NSMutableDictionary alloc]init];

        NSString *query = [NSString stringWithFormat:@"startswith(givenName, '%@')", searchString];

           [dictionary setValue:query forKey:@"$filter"];



    return dictionary;
}

```
Dernæst skal vi bruge den `NXOAuth2Request` metode til at hente data tilbage fra API i JSON-formatet.

```objc
NSArray *accounts = [store accountsWithAccountType:@"myGraphService"];
    [NXOAuth2Request performMethod:@"GET"
                        onResource:[NSURL URLWithString:graphURL]
                   usingParameters:params
                       withAccount:accounts[0]
               sendProgressHandler:^(unsigned long long bytesSend, unsigned long long bytesTotal) {
                   // e.g., update a progress indicator
               }
                   responseHandler:^(NSURLResponse *response, NSData *responseData, NSError *error) {
                       // Process the response
                       if (responseData) {
                           NSError *error;
                           NSDictionary *dataReturned = [NSJSONSerialization JSONObjectWithData:responseData options:0 error:nil];
                           NSLog(@"Graph Response was: %@", dataReturned);

                           // We can grab the top most JSON node to get our graph data.
                           NSArray *graphDataArray = [dataReturned objectForKey:@"value"];
```

Til sidst, Lad os se på, hvordan du returnere dataene til MasterViewController. Data, der returnerer som serienummererede og skal deserialiseres og indlæses i et objekt, der kan bruge MainViewController. Hertil, skelet har en `User.m/h` -fil, der opretter et brugerobjekt. Du har placeret objekterne, brugerobjektet med oplysninger fra diagrammet.

```objc
                           // We can grab the top most JSON node to get our graph data.
                           NSArray *graphDataArray = [dataReturned objectForKey:@"value"];

                           // Don't be thrown off by the key name being "value". It really is the name of the
                           // first node. :-)

                           //each object is a key value pair
                           NSDictionary *keyValuePairs;
                           NSMutableArray* Users = [[NSMutableArray alloc]init];

                           for(int i =0; i < graphDataArray.count; i++)
                           {
                               keyValuePairs = [graphDataArray objectAtIndex:i];

                               User *s = [[User alloc]init];
                               s.upn = [keyValuePairs valueForKey:@"userPrincipalName"];
                               s.name =[keyValuePairs valueForKey:@"displayName"];
                               s.mail =[keyValuePairs valueForKey:@"mail"];
                               s.businessPhones =[keyValuePairs valueForKey:@"businessPhones"];
                               s.mobilePhones =[keyValuePairs valueForKey:@"mobilePhone"];


                               [Users addObject:s];
```


## <a name="run-the-sample"></a>Køre eksemplet

Hvis du har brugt skelet eller fulgte sammen med denne gennemgang bør nu køre programmet. Start simulator, og klik på **Log på** for at bruge programmet på computeren.

## <a name="get-security-updates-for-our-product"></a>Hente sikkerhedsopdateringer til vores produkt

Vi anbefaler, at finde meddelelser om når sikkerhedshændelser forekommer, ved at besøge [Security TechCenter](https://technet.microsoft.com/security/dd252948) og abonnere på vejledende sikkerhedsadvarsler.
