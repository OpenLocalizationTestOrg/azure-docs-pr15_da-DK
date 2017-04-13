<properties
    pageTitle="Sådan Brug iOS SDK til Azure Mobile-Apps"
    description="Sådan Brug iOS SDK til Azure Mobile-Apps"
    services="app-service\mobile"
    documentationCenter="ios"
    authors="ysxu"
    manager="yochayk"
    editor=""/>

<tags
    ms.service="app-service-mobile"
    ms.workload="mobile"
    ms.tgt_pltfrm="mobile-ios"
    ms.devlang="objective-c"
    ms.topic="article"
    ms.date="10/01/2016"
    ms.author="yuaxu"/>

# <a name="how-to-use-ios-client-library-for-azure-mobile-apps"></a>Sådan Brug iOS klientbibliotek til Azure Mobile-Apps

[AZURE.INCLUDE [app-service-mobile-selector-client-library](../../includes/app-service-mobile-selector-client-library.md)]

Denne vejledning lærer du at udføre almindelige scenarier, der bruger den seneste [Azure Mobile-Apps iOS SDK][1]. Hvis du er ny bruger af Azure Mobile-Apps, første fuldført [Azure Mobile Apps Hurtig Start] til at oprette en back-end, oprette en tabel, og Hent et færdigbyggede iOS Xcode projekt. I denne vejledning der, fokuserer vi på klientsiden iOS SDK. Hvis du vil vide mere om serversiden SDK for backend-version, skal du se Server SDK HOWTOs.

## <a name="reference-documentation"></a>Referencedokumentation

Referencedokumentation for iOS-klienten SDK findes her: [Azure Mobile-Apps iOS referencen Client][2].

## <a name="supported-platforms"></a>Understøttede platforme

IOS SDK understøtter mål-C projekter, Swift 2.2 projekter og Swift 2.3 projekter til iOS versioner 8.0 eller nyere.

"Server-flow" godkendelse bruger en webvisning for præsenteret Brugergrænsefladen.  Hvis enheden ikke til at præsentere en WebView UI, og derefter en anden metode til godkendelse er påkrævet, som ikke er omfattet af produktet.  
Dette SDK er derfor ikke egnet til type af værdikontrol eller på samme måde begrænset enheder.

##<a name="Setup"></a>Installation og forudsætninger

Denne vejledning forudsætter, at du har oprettet en back-end med en tabel. Denne vejledning forudsætter, at tabellen har det samme skema som tabellerne i disse selvstudier. Denne vejledning antages det også, at i din kode, skal du henvise til `MicrosoftAzureMobile.framework` og importere `MicrosoftAzureMobile/MicrosoftAzureMobile.h`.

##<a name="create-client"></a>Sådan: oprette klient

For at få adgang til en Azure Mobile-Apps backend-version i dit projekt, oprette en `MSClient`. Erstatte `AppUrl` med app URL-adresse. Du kan lade `gatewayURLString` og `applicationKey` tom. Hvis du oprette en gateway til godkendelse, skal du udfylde `gatewayURLString` med gateway URL-adresse.

**Mål-C**:

```
MSClient *client = [MSClient clientWithApplicationURLString:@"AppUrl"];
```

**Swift**:

```
let client = MSClient(applicationURLString: "AppUrl")
```


##<a name="table-reference"></a>Sådan: oprette tabelreference

Til access eller opdatere data, oprette en reference til back end-tabellen. Erstatte `TodoItem` med navnet på tabellen

**Mål-C**:

```
MSTable *table = [client tableWithName:@"TodoItem"];
```

**Swift**:

```
let table = client.tableWithName("TodoItem")
```


##<a name="querying"></a>Sådan: søge Data

Hvis du vil oprette en databaseforespørgsel forespørgsel på `MSTable` objekt. Følgende forespørgsel får alle elementerne i `TodoItem` og logfører teksten i hvert element.

**Mål-C**:

```
[table readWithCompletion:^(MSQueryResult *result, NSError *error) {
        if(error) { // error is nil if no error occured
                NSLog(@"ERROR %@", error);
        } else {
                for(NSDictionary *item in result.items) { // items is NSArray of records that match query
                        NSLog(@"Todo Item: %@", [item objectForKey:@"text"]);
                }
        }
}];
```

**Swift**:

```
table.readWithCompletion { (result, error) in
    if let err = error {
        print("ERROR ", err)
    } else if let items = result?.items {
        for item in items {
            print("Todo Item: ", item["text"])
        }
    }
}
```

##<a name="filtering"></a>Sådan: Filter returnerede Data

Hvis du vil filtrere resultater, er der mange forskellige indstillinger.

Hvis du vil filtrere ved hjælp af et prædikat, skal du bruge en `NSPredicate` og `readWithPredicate`. Følgende filtre returnerede data for at finde kun ufuldstændig opgaveliste elementer.

**Mål-C**:

```
// Create a predicate that finds items where complete is false
NSPredicate * predicate = [NSPredicate predicateWithFormat:@"complete == NO"];
// Query the TodoItem table
[table readWithPredicate:predicate completion:^(MSQueryResult *result, NSError *error) {
        if(error) {
                NSLog(@"ERROR %@", error);
        } else {
                for(NSDictionary *item in result.items) {
                        NSLog(@"Todo Item: %@", [item objectForKey:@"text"]);
                }
        }
}];
```

**Swift**:

```
// Create a predicate that finds items where complete is false
let predicate =  NSPredicate(format: "complete == NO")
// Query the TodoItem table
table.readWithPredicate(predicate) { (result, error) in
    if let err = error {
        print("ERROR ", err)
    } else if let items = result?.items {
        for item in items {
            print("Todo Item: ", item["text"])
        }
    }
}
```

##<a name="query-object"></a>Sådan: bruge MSQuery

At udføre en kompleks forespørgsel (herunder sortering og sideinddeling) skal du oprette en `MSQuery` objekt direkte eller ved hjælp af et prædikat:

**Mål-C**:

```
MSQuery *query = [table query];
MSQuery *query = [table queryWithPredicate: [NSPredicate predicateWithFormat:@"complete == NO"]];
```

**Swift**:

```
let query = table.query()
let query = table.queryWithPredicate(NSPredicate(format: "complete == NO"))
```

`MSQuery`kan du styre flere forespørgsel funktionsmåder.

* Angive rækkefølgen af resultater
* Begrænse hvilke felter der skal gå tilbage
* Begrænse antallet af poster for at vende tilbage
* Angive samlede antal svar
* Angiv brugerdefinerede forespørgselsparametre streng i anmodning
* Anvende flere funktioner

Udføre en `MSQuery` forespørgsel ved at ringe til `readWithCompletion` på objektet.

## <a name="sorting"></a>Sådan: sortere Data med MSQuery

Hvis du vil sortere resultaterne, Lad os se på et eksempel. Hvis du vil sortere efter feltet "tekst" i stigende rækkefølge, og derefter efter 'fuldført' faldende, kalde `MSQuery` som vist her:

**Mål-C**:

```
[query orderByAscending:@"text"];
[query orderByDescending:@"complete"];
[query readWithCompletion:^(MSQueryResult *result, NSError *error) {
        if(error) {
                NSLog(@"ERROR %@", error);
        } else {
                for(NSDictionary *item in result.items) {
                        NSLog(@"Todo Item: %@", [item objectForKey:@"text"]);
                }
        }
}];
```

**Swift**:

```
query.orderByAscending("text")
query.orderByDescending("complete")
query.readWithCompletion { (result, error) in
    if let err = error {
        print("ERROR ", err)
    } else if let items = result?.items {
        for item in items {
            print("Todo Item: ", item["text"])
        }
    }
}
```


## <a name="selecting"></a><a name="parameters"></a>Sådan: begrænse felter og udvide forespørgselsparametre streng med MSQuery

Hvis du vil begrænse felter der skal returneres i en forespørgsel, skal du angive navnene på felterne i egenskaben **selectFields** . I dette eksempel returnerer kun tekst og færdige felter:

**Mål-C**:

```
query.selectFields = @[@"text", @"complete"];
```

**Swift**:

```
query.selectFields = ["text", "complete"]
```

Medtage yderligere parametre for forespørgselsstreng i server anmodningen (for eksempel, fordi en brugerdefineret serverscript bruger dem), skal du udfylde `query.parameters` som vist her:

**Mål-C**:

```
query.parameters = @{
    @"myKey1" : @"value1",
    @"myKey2" : @"value2",
};
```

**Swift**:

```
query.parameters = ["myKey1": "value1", "myKey2": "value2"]
```

## <a name="paging"></a>Sådan: konfigurere sidestørrelse

Med Azure Mobile-Apps styrer sidestørrelsen antallet af poster, der er trukket ad gangen fra back end-tabeller. Et kald til `pull` data vil derefter batch data, baseret på denne sidestørrelse, indtil der er ingen flere poster til at trække op.

Det er muligt at konfigurere en sidestørrelse, ved hjælp af **MSPullSettings** , som vist nedenfor. Siden standardstørrelsen er 50, og eksemplet nedenfor, ændres det til 3.

Du kan konfigurere en anden sidestørrelse for at forbedre ydeevnen. Hvis du har et stort antal små dataposter, reducerer en høj sidestørrelse antallet af server kørsler. 

Denne indstilling styrer kun sidestørrelse på klientsiden. Hvis klienten beder om at få en større sidestørrelse end Mobile-Apps back-end understøtter, er det maksimale antal backend-version er konfigureret til at understøtte maksimalt sidestørrelsen. 

Denne indstilling er også _antallet_ af poster, ikke _bytestørrelse_.

Hvis du øger sidestørrelse klienten på computeren, [skal du også øge sidestørrelsen på serveren](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md#_how-to-adjust-the-table-paging-size).

**Mål-C**:

```
  MSPullSettings *pullSettings = [[MSPullSettings alloc] initWithPageSize:3];
  [table  pullWithQuery:query queryId:@nil settings:pullSettings
                        completion:^(NSError * _Nullable error) {
                               if(error) {
                    NSLog(@"ERROR %@", error);
                } 
                           }];
```


**Swift**:

```
let pullSettings = MSPullSettings(pageSize: 3)
table.pullWithQuery(query, queryId:nil, settings: pullSettings) { (error) in
    if let err = error {
        print("ERROR ", err)
    } 
}
```

##<a name="inserting"></a>Sådan: indsætte Data

For at indsætte en ny tabelrække skal oprette en `NSDictionary` og kalde `table insert`. Hvis [Dynamisk skema] er aktiveret, mobile-backend-version Azure App Service automatisk genererer nye kolonner, der er baseret på den `NSDictionary`.

Hvis `id` er ikke oplyst, back-end genererer automatisk et nyt entydigt ID. Lave dit eget `id` til at bruge mail adresser, brugernavne, eller din egen brugerdefinerede værdier som-ID. Give dit eget ID kan lette joinforbindelser og forretningsmæssige database logik.

Den `result` indeholder det nye element, der er indsat. Afhængigt af din server logik, skal den muligvis yderligere eller ændrede data sammenlignet med hvad der blev sendt til serveren.

**Mål-C**:

```
NSDictionary *newItem = @{@"id": @"custom-id", @"text": @"my new item", @"complete" : @NO};
[table insert:newItem completion:^(NSDictionary *result, NSError *error) {
    if(error) {
        NSLog(@"ERROR %@", error);
    } else {
        NSLog(@"Todo Item: %@", [result objectForKey:@"text"]);
    }
}];
```

**Swift**:

```
let newItem = ["id": "custom-id", "text": "my new item", "complete": false]
table.insert(newItem) { (result, error) in
    if let err = error {
        print("ERROR ", err)
    } else if let item = result {
        print("Todo Item: ", item["text"])
    }
}
```

##<a name="modifying"></a>Sådan: ændre Data

Redigere et element og viderestilling af opkald for at opdatere en eksisterende række, `update`:

**Mål-C**:

```
NSMutableDictionary *newItem = [oldItem mutableCopy]; // oldItem is NSDictionary
[newItem setValue:@"Updated text" forKey:@"text"];
[table update:newItem completion:^(NSDictionary *result, NSError *error) {
    if(error) {
        NSLog(@"ERROR %@", error);
    } else {
        NSLog(@"Todo Item: %@", [result objectForKey:@"text"]);
    }
}];
```

**Swift**:

```
if let newItem = oldItem.mutableCopy() as? NSMutableDictionary {
    newItem["text"] = "Updated text"
    table2.update(newItem as [NSObject: AnyObject], completion: { (result, error) -> Void in
        if let err = error {
            print("ERROR ", err)
        } else if let item = result {
            print("Todo Item: ", item["text"])
        }
    })
}
```

Du kan også angive række-ID og det opdaterede felt:

**Mål-C**:

```
[table update:@{@"id":@"custom-id", @"text":"my EDITED item"} completion:^(NSDictionary *result, NSError *error) {
    if(error) {
        NSLog(@"ERROR %@", error);
    } else {
        NSLog(@"Todo Item: %@", [result objectForKey:@"text"]);
    }
}];
```

**Swift**:

```
table.update(["id": "custom-id", "text": "my EDITED item"]) { (result, error) in
    if let err = error {
        print("ERROR ", err)
    } else if let item = result {
        print("Todo Item: ", item["text"])
    }
}
```

Som minimum den `id` attributten skal angives, når du foretager opdateringer.

##<a name="deleting"></a>Sådan: slette Data

Hvis du vil slette et element, kalde `delete` med elementet:

**Mål-C**:

```
[table delete:item completion:^(id itemId, NSError *error) {
    if(error) {
        NSLog(@"ERROR %@", error);
    } else {
        NSLog(@"Todo Item ID: %@", itemId);
    }
}];
```

**Swift**:

```
table.delete(newItem as [NSObject: AnyObject]) { (itemId, error) in
    if let err = error {
        print("ERROR ", err)
    } else {
        print("Todo Item ID: ", itemId)
    }
}
```

Du kan også slette ved at give et række-ID:

**Mål-C**:

```
[table deleteWithId:@"37BBF396-11F0-4B39-85C8-B319C729AF6D" completion:^(id itemId, NSError *error) {
    if(error) {
        NSLog(@"ERROR %@", error);
    } else {
        NSLog(@"Todo Item ID: %@", itemId);
    }
}];
```

**Swift**:

```
table.deleteWithId("37BBF396-11F0-4B39-85C8-B319C729AF6D") { (itemId, error) in
    if let err = error {
        print("ERROR ", err)
    } else {
        print("Todo Item ID: ", itemId)
    }
}
```

Som minimum den `id` attribut skal angives, når foretage sletter.

##<a name="customapi"></a>Sådan: ringe brugerdefinerede API

Med en brugerdefineret API, kan du fremvise en hvilken som helst back end-funktionalitet. Den skal ikke knyttes til en tabel handling. Ikke kun kan du opnå mere kontrol over meddelelser, du kan endda læst/angive sidehoveder og ændre formatet svar brødtekst. Hvis du vil lære at oprette en brugerdefineret API på back-end, skal du læse [Brugerdefinerede API'er](app-service-mobile-node-backend-how-to-use-server-sdk.md#work-easy-apis)

Hvis du vil ringe til en brugerdefineret API skal ringe `MSClient.invokeAPI`. Anmodningen og svaret indhold behandles som JSON. Bruge andre medietyper [bruge andre overbelastning af `invokeAPI` ] [ 5].  Gøre en `GET` anmode om i stedet for en `POST` anmode om sæt parameter `HTTPMethod` til `"GET"` og parameter `body` til `nil` (da anmodninger om HENTNING af ikke har meddelelsernes brødtekst.) Hvis din brugerdefinerede API understøtter andre HTTP-verber, skal du ændre `HTTPMethod` korrekt.

**Mål-C**:

```
[self.client invokeAPI:@"sendEmail"
                  body:@{ @"contents": @"Hello world!" }
            HTTPMethod:@"POST"
            parameters:@{ @"to": @"bill@contoso.com", @"subject" : @"Hi!" }
               headers:nil
            completion: ^(NSData *result, NSHTTPURLResponse *response, NSError *error) {
                if(error) {
                    NSLog(@"ERROR %@", error);
                } else {
                    // Do something with result
                }
            }];
```

**Swift**:

```
client.invokeAPI("sendEmail",
            body: [ "contents": "Hello World" ],
            HTTPMethod: "POST",
            parameters: [ "to": "bill@contoso.com", "subject" : "Hi!" ],
            headers: nil)
            {
                (result, response, error) -> Void in
                if let err = error {
                    print("ERROR ", err)
                } else if let res = result {
                          // Do something with result
                }
        }
```

##<a name="templates"></a>Sådan: Registrer opslagsnål skabeloner at sende meddelelser på tværs af platforme

Hvis du vil registrere skabeloner, du overføre skabeloner med din **client.push registerDeviceToken** metode i din klient-app.

**Mål-C**:

```
[client.push registerDeviceToken:deviceToken template:iOSTemplate completion:^(NSError *error) {
    if(error) {
        NSLog(@"ERROR %@", error);
    }
}];
```

**Swift**:

```
    client.push?.registerDeviceToken(NSData(), template: iOSTemplate, completion: { (error) in
        if let err = error {
            print("ERROR ", err)
        }
    })
```

Dine skabeloner er af typen NSDictionary og kan indeholde flere skabeloner i følgende format:

**Mål-C**:

```
NSDictionary *iOSTemplate = @{ @"templateName": @{ @"body": @{ @"aps": @{ @"alert": @"$(message)" } } } };
```

**Swift**:

```
let iOSTemplate = ["templateName": ["body": ["aps": ["alert": "$(message)"]]]]
```

Alle mærker fjernes fra anmodningen om sikkerhed.  Hvis du vil føje mærker til installationer eller skabeloner i installationer, skal du se [arbejde med .NET back end-serveren SDK til Azure Mobile-Apps][4].  Hvis du vil sende meddelelser ved hjælp af disse registrerede skabeloner, arbejde med [Besked om Hubs API'er][3].

##<a name="errors"></a>Sådan: håndtere fejl

Når du ringer til en mobiltelefon, backend Azure App Service-version, fuldførelse blokering indeholder en `NSError` parameter. Når der opstår en fejl, er denne parameter ikke er tom. I din kode, skal du kontrollere denne parameter og håndtere fejl efter behov, som vist i de foregående kodestykker.

Filen [`<WindowsAzureMobileServices/MSError.h>`] [6] definerer konstanterne `MSErrorResponseKey`, `MSErrorRequestKey`, og `MSErrorServerItemKey`. Sådan kommer du flere data, der er relateret til fejlen:

**Mål-C**:

```
NSDictionary *serverItem = [error.userInfo objectForKey:MSErrorServerItemKey];
```

**Swift**:

```
let serverItem = error.userInfo[MSErrorServerItemKey]
```

Desuden definerer filen konstanter hver fejlkode:

**Mål-C**:

```
if (error.code == MSErrorPreconditionFailed) {
```

**Swift**:

```
if (error.code == MSErrorPreconditionFailed) {
```

## <a name="adal"></a>Sådan: godkende brugere med Active Directory Authentication Library

Du kan bruge Active Directory Authentication Library (ADAL) til at logge brugere på dit program ved hjælp af Azure Active Directory. Klient flow godkendelse ved hjælp af en identitetsudbyder SDK er bedst at bruge den `loginWithProvider:completion:` metode.  Klient flow godkendelse giver en mere oprindelige UX funktionalitet og giver mulighed for yderligere tilpasning.

1. Konfigurere din mobilapp backend-version til logon AAD ved at følge [Sådan konfigureres App-tjeneste til logon til Active Directory] [ 7] selvstudium. Sørg for at fuldføre valgfrit til registrering af et native client-program. Til iOS, anbefaler vi, at Omdiriger URI er i formularen `<app-scheme>://<bundle-id>`. Du kan finde flere oplysninger, se [ADAL iOS Hurtig start][8].

2. Installere ADAL ved hjælp af Cocoapods. Redigere din Podfile for at medtage følgende definitionen, erstatte **Din PROJECT** med navnet på projektet Xcode:

        source 'https://github.com/CocoaPods/Specs.git'
        link_with ['YOUR-PROJECT']
        xcodeproj 'YOUR-PROJECT'

   og styreanordning:

        pod 'ADALiOS'

3. Ved hjælp af Terminal skal køre `pod install` fra mappen, der indeholder dit projekt, og Åbn derefter genererede Xcode arbejdsområdet (ikke projektet).

4. Tilføj følgende kode i dit program, efter det sprog, du bruger. Foretag disse erstatninger i hver skal:

    * Erstat **Indsæt-NØGLECENTER-her** med navnet på den lejer, hvori du klargjort dit program. Formatet skal være https://login.windows.net/contoso.onmicrosoft.com. Denne værdi kan kopieres fra fanen domæne i din Azure Active Directory i [Azure klassisk portalen].
    * Erstat **Indsæt-ressource-ID-her** med klient-ID for din back end-til-mobilappen. Du kan få klient-ID fra fanen **Avanceret** under **Azure Active Directory-indstillinger** i portalen.
    * Erstat **Indsæt-klient-ID-her** med brugerens klient-ID, du har kopieret fra den oprindelige klientprogrammet.
    * Erstat **Indsæt-REDIRECT-URI-her** med webstedets _/.auth/login/done_ slutpunkt, ved hjælp af HTTPS-skemaet. Denne værdi skal ligne _https://contoso.azurewebsites.net/.auth/login/done_.

**Mål-C**:

    #import <ADALiOS/ADAuthenticationContext.h>
    #import <ADALiOS/ADAuthenticationSettings.h>
    // ...
    - (void) authenticate:(UIViewController*) parent
               completion:(void (^) (MSUser*, NSError*))completionBlock;
    {
        NSString *authority = @"INSERT-AUTHORITY-HERE";
        NSString *resourceId = @"INSERT-RESOURCE-ID-HERE";
        NSString *clientId = @"INSERT-CLIENT-ID-HERE";
        NSURL *redirectUri = [[NSURL alloc]initWithString:@"INSERT-REDIRECT-URI-HERE"];
        ADAuthenticationError *error;
        ADAuthenticationContext *authContext = [ADAuthenticationContext authenticationContextWithAuthority:authority error:&error];
        authContext.parentController = parent;
        [ADAuthenticationSettings sharedInstance].enableFullScreen = YES;
        [authContext acquireTokenWithResource:resourceId
                                     clientId:clientId
                                  redirectUri:redirectUri
                              completionBlock:^(ADAuthenticationResult *result) {
                                  if (result.status != AD_SUCCEEDED)
                                  {
                                      completionBlock(nil, result.error);;
                                  }
                                  else
                                  {
                                      NSDictionary *payload = @{
                                                                @"access_token" : result.tokenCacheStoreItem.accessToken
                                                                };
                                      [client loginWithProvider:@"aad" token:payload completion:completionBlock];
                                  }
                              }];
    }


**Swift**:

    // add the following imports to your bridging header:
    //      #import <ADALiOS/ADAuthenticationContext.h>
    //      #import <ADALiOS/ADAuthenticationSettings.h>

    func authenticate(parent: UIViewController, completion: (MSUser?, NSError?) -> Void) {
        let authority = "INSERT-AUTHORITY-HERE"
        let resourceId = "INSERT-RESOURCE-ID-HERE"
        let clientId = "INSERT-CLIENT-ID-HERE"
        let redirectUri = NSURL(string: "INSERT-REDIRECT-URI-HERE")
        var error: AutoreleasingUnsafeMutablePointer<ADAuthenticationError?> = nil
        let authContext = ADAuthenticationContext(authority: authority, error: error)
        authContext.parentController = parent
        ADAuthenticationSettings.sharedInstance().enableFullScreen = true
        authContext.acquireTokenWithResource(resourceId, clientId: clientId, redirectUri: redirectUri) { (result) in
                if result.status != AD_SUCCEEDED {
                    completion(nil, result.error)
                }
                else {
                    let payload: [String: String] = ["access_token": result.tokenCacheStoreItem.accessToken]
                    client.loginWithProvider("aad", token: payload, completion: completion)
                }
            }
    }

## <a name="facebook-sdk"></a>Sådan: godkende brugere med Facebook SDK til iOS

Du kan bruge Facebook SDK til iOS for at logge brugere på dit program ved hjælp af Facebook.  Ved hjælp af en klient flow godkendelse er bedst at bruge den `loginWithProvider:completion:` metode.  Klient flow godkendelse giver en mere oprindelige UX funktionalitet og giver mulighed for yderligere tilpasning.

1. Konfigurere din mobilapp backend-version til Facebook-logon ved at følge [Sådan konfigureres App Service til at logge på Facebook] [ 9] selvstudium.

2. Installere Facebook SDK til iOS ved at følge [Facebook SDK til iOS - Introduktion] [ 10] dokumentation. I stedet for at oprette en app, kan du føje iOS-platform til din eksisterende registrering. 

3. Facebooks dokumentation indeholder nogle mål-C-kode i App stedfortræderen. Hvis du bruger **Swift**, kan du bruge følgende oversættelser for AppDelegate.swift:
  
        // Add the following import to your bridging header:
        //      #import <FBSDKCoreKit/FBSDKCoreKit.h>
        
        func application(application: UIApplication, didFinishLaunchingWithOptions launchOptions: [NSObject : AnyObject]?) -> Bool {
            FBSDKApplicationDelegate.sharedInstance().application(application, didFinishLaunchingWithOptions: launchOptions)
            // Add any custom logic here.
            return true
        }

        func application(application: UIApplication, openURL url: NSURL, sourceApplication: String?, annotation: AnyObject?) -> Bool {
            let handled = FBSDKApplicationDelegate.sharedInstance().application(application, openURL: url, sourceApplication: sourceApplication, annotation: annotation)
            // Add any custom logic here.
            return handled
        }

4. Ud over at tilføje `FBSDKCoreKit.framework` til projektet, også tilføje en reference til `FBSDKLoginKit.framework` på samme måde. 

4. Tilføj følgende kode i dit program, efter det sprog, du bruger. 

**Mål-C**:

    #import <FBSDKLoginKit/FBSDKLoginKit.h>
    #import <FBSDKCoreKit/FBSDKAccessToken.h>
    // ...
    - (void) authenticate:(UIViewController*) parent
               completion:(void (^) (MSUser*, NSError*)) completionBlock;
    {       
        FBSDKLoginManager *loginManager = [[FBSDKLoginManager alloc] init];
        [loginManager
         logInWithReadPermissions: @[@"public_profile"]
         fromViewController:parent
         handler:^(FBSDKLoginManagerLoginResult *result, NSError *error) {
             if (error) {
                 completionBlock(nil, error);
             } else if (result.isCancelled) {
                 completionBlock(nil, error);
             } else {
                 NSDictionary *payload = @{
                                           @"access_token":result.token.tokenString
                                           };
                 [client loginWithProvider:@"facebook" token:payload completion:completionBlock];
             }
         }];
    }

**Swift**:

    // Add the following imports to your bridging header:
    //      #import <FBSDKLoginKit/FBSDKLoginKit.h>
    //      #import <FBSDKCoreKit/FBSDKAccessToken.h>
    
    func authenticate(parent: UIViewController, completion: (MSUser?, NSError?) -> Void) {
        let loginManager = FBSDKLoginManager()
        loginManager.logInWithReadPermissions(["public_profile"], fromViewController: parent) { (result, error) in
            if (error != nil) {
                completion(nil, error)
            }
            else if result.isCancelled {
                completion(nil, error)
            }
            else {
                let payload: [String: String] = ["access_token": result.token.tokenString]
                client.loginWithProvider("facebook", token: payload, completion: completion)
            }
        }
    }

## <a name="twitter-fabric"></a>Sådan: godkende brugere med Twitter-strukturen til iOS

Du kan bruge strukturen til iOS for at logge brugere på dit program ved hjælp af Twitter. Klient Flow godkendelse er bedst at bruge den `loginWithProvider:completion:` metode, som den indeholder en mere oprindelige UX funktionalitet og giver mulighed for yderligere tilpasning.

1. Konfigurere din mobilapp backend-version til Twitter-logon ved at følge [Sådan konfigureres App Service til at logge på Twitter](app-service-mobile-how-to-configure-twitter-authentication.md) -selvstudium.

2. Føje struktur til dit projekt ved at følge i dokumentationen til [strukturen til iOS - Introduktion] og konfiguration af TwitterKit.

    > [AZURE.NOTE] Som standard opretter strukturen en Twitter-program for dig. Du kan undgå at oprette et program ved at registrere Consumer nøgle og forbrugere hemmeligt, du har oprettet tidligere ved hjælp af følgende kodestykker.  Alternativt kan du erstatte de Consumer nøgle og forbrugere hemmeligt værdier, du angiver til App-tjenesten med de værdier, du ser i [Strukturen Dashboard]. Hvis du vælger denne indstilling, skal du sørge for at angive tilbagekald URL-adressen til en pladsholderværdi, såsom `https://<yoursitename>.azurewebsites.net/.auth/login/twitter/callback`.

    Hvis du vælger at bruge de hemmeligheder, du oprettede tidligere, kan du tilføje følgende kode til din App stedfortræder:
    
    **Mål-C**:

        #import <Fabric/Fabric.h>
        #import <TwitterKit/TwitterKit.h>
        // ...
        - (BOOL)application:(UIApplication *)application didFinishLaunchingWithOptions:(NSDictionary *)launchOptions
        {
            [[Twitter sharedInstance] startWithConsumerKey:@"your_key" consumerSecret:@"your_secret"];
            [Fabric with:@[[Twitter class]]];
            // Add any custom logic here.
            return YES;
        }
        
    **Swift**:
    
        import Fabric
        import TwitterKit
        // ...
        func application(application: UIApplication, didFinishLaunchingWithOptions launchOptions: [NSObject : AnyObject]?) -> Bool {
            Twitter.sharedInstance().startWithConsumerKey("your_key", consumerSecret: "your_secret")
            Fabric.with([Twitter.self])
            // Add any custom logic here.
            return true
        }
    
3. Tilføj følgende kode i dit program, efter det sprog, du bruger. 

**Mål-C**:

    #import <TwitterKit/TwitterKit.h>
    // ...
    - (void)authenticate:(UIViewController*)parent completion:(void (^) (MSUser*, NSError*))completionBlock
    {
        [[Twitter sharedInstance] logInWithCompletion:^(TWTRSession *session, NSError *error) {
            if (session) {
                NSDictionary *payload = @{
                                            @"access_token":session.authToken,
                                            @"access_token_secret":session.authTokenSecret
                                        };
                [client loginWithProvider:@"twitter" token:payload completion:completionBlock];
            } else {
                completionBlock(nil, error);
            }
        }];
    }

**Swift**:

    import TwitterKit
    // ...
    func authenticate(parent: UIViewController, completion: (MSUser?, NSError?) -> Void) {
        let client = self.table!.client
        Twitter.sharedInstance().logInWithCompletion { session, error in
            if (session != nil) {
                let payload: [String: String] = ["access_token": session!.authToken, "access_token_secret": session!.authTokenSecret]
                client.loginWithProvider("twitter", token: payload, completion: completion)
            } else {
                completion(nil, error)
            }
        }
    }

## <a name="google-sdk"></a>Sådan: godkende brugere med Google-logon SDK til iOS

Du kan bruge i Google-logon SDK til iOS for at logge brugere på dit program ved hjælp af en Google-konto.  Google nyligt offentliggjorte ændringer i deres OAuth sikkerhedspolitikker.  Disse politikændringer kræver brug af Google SDK i fremtiden.

1. Konfigurere din mobilapp backend-version til Google-logon ved at følge [Sådan konfigureres App Service til at logge på Google](app-service-mobile-how-to-configure-google-authentication.md) -selvstudium.

2. Installere i Google SDK til iOS ved at følge i dokumentationen til [Google-logon til iOS - starte integrering](https://developers.google.com/identity/sign-in/ios/start-integrating) . Du kan springe over afsnittet "Godkende med et back end-serveren".

3. Tilføj følgende dine stedfortrædere `signIn:didSignInForUser:withError:` metode efter det sprog, du bruger.

**Mål-C**:

        NSDictionary *payload = @{
                                  @"id_token":user.authentication.idToken,
                                  @"authorization_code":user.serverAuthCode
                                  };
        
        [client loginWithProvider:@"google" token:payload completion:^(MSUser *user, NSError *error) {
            // ...
        }];

**Swift**:

        let payload: [String: String] = ["id_token": user.authentication.idToken, "authorization_code": user.serverAuthCode]
        client.loginWithProvider("google", token: payload) { (user, error) in
            // ...
        }

4. Sørg for, at du også tilføje følgende for at `application:didFinishLaunchingWithOptions:` i din app stedfortræder, erstatte "SERVER_CLIENT_ID" med det samme ID, du brugte til at konfigurere App Service i trin 1.

**Mål-C**:

        [GIDSignIn sharedInstance].serverClientID = @"SERVER_CLIENT_ID";
 
 **Swift**:
 
        GIDSignIn.sharedInstance().serverClientID = "SERVER_CLIENT_ID"

 
 5. Føj følgende kode i dit program i et UIViewController, der implementerer den `GIDSignInUIDelegate` protokol, efter det sprog, du bruger.  Du har logget af, før du være logget på igen, og selvom du ikke behøver at angive dine legitimationsoplysninger igen, der vises en samtykke dialogboks.  Ring kun denne metode, når session Tokenet er udløbet.
 
 **Mål-C**:

        #import <Google/SignIn.h>
        // ...
        - (void)authenticate
        {
                [GIDSignIn sharedInstance].uiDelegate = self;
                [[GIDSignIn sharedInstance] signOut];
                [[GIDSignIn sharedInstance] signIn];
        }
 
 **Swift**:
    
        // ...
        func authenticate() {
            GIDSignIn.sharedInstance().uiDelegate = self
            GIDSignIn.sharedInstance().signOut()
            GIDSignIn.sharedInstance().signIn()
        }
        
<!-- Anchors. -->

[What is Mobile Services]: #what-is
[Concepts]: #concepts
[Setup and Prerequisites]: #Setup
[How to: Create the Mobile Services client]: #create-client
[How to: Create a table reference]: #table-reference
[How to: Query data from a mobile service]: #querying
[Filter returned data]: #filtering
[Sort returned data]: #sorting
[Return data in pages]: #paging
[Select specific columns]: #selecting
[How to: Bind data to the user interface]: #binding
[How to: Insert data into a mobile service]: #inserting
[How to: Modify data in a mobile service]: #modifying
[How to: Authenticate users]: #authentication
[Cache authentication tokens]: #caching-tokens
[How to: Upload images and large files]: #blobs
[How to: Handle errors]: #errors
[How to: Design unit tests]: #unit-testing
[How to: Customize the client]: #customizing
[Customize request headers]: #custom-headers
[Customize data type serialization]: #custom-serialization
[Next Steps]: #next-steps
[How to: Use MSQuery]: #query-object

<!-- Images. -->

<!-- URLs. -->
[Azure Mobile Apps Hurtig Start]: app-service-mobile-ios-get-started.md

[Add Mobile Services to Existing App]: /develop/mobile/tutorials/get-started-data
[Get started with Mobile Services]: /develop/mobile/tutorials/get-started-ios
[Validate and modify data in Mobile Services by using server scripts]: /develop/mobile/tutorials/validate-modify-and-augment-data-ios
[Mobile Services SDK]: https://go.microsoft.com/fwLink/p/?LinkID=266533
[Authentication]: /develop/mobile/tutorials/get-started-with-users-ios
[iOS SDK]: https://developer.apple.com/xcode

[Handling Expired Tokens]: http://go.microsoft.com/fwlink/p/?LinkId=301955
[Live Connect SDK]: http://go.microsoft.com/fwlink/p/?LinkId=301960
[Permissions]: http://msdn.microsoft.com/library/windowsazure/jj193161.aspx
[Service-side Authorization]: mobile-services-javascript-backend-service-side-authorization.md
[Use scripts to authorize users]: /develop/mobile/tutorials/authorize-users-in-scripts-ios
[Dynamisk skema]: http://go.microsoft.com/fwlink/p/?LinkId=296271
[How to: access custom parameters]: /develop/mobile/how-to-guides/work-with-server-scripts#access-headers
[Create a table]: http://msdn.microsoft.com/library/windowsazure/jj193162.aspx
[NSDictionary object]: http://go.microsoft.com/fwlink/p/?LinkId=301965
[ASCII control codes C0 and C1]: http://en.wikipedia.org/wiki/Data_link_escape_character#C1_set
[CLI to manage Mobile Services tables]: ../virtual-machines-command-line-tools.md#Mobile_Tables
[Conflict-Handler]: mobile-services-ios-handling-conflicts-offline-data.md#add-conflict-handling

[Strukturen Dashboard]: https://www.fabric.io/home
[Strukturen til iOS - Introduktion]: https://docs.fabric.io/ios/fabric/getting-started.html
[1]: https://github.com/Azure/azure-mobile-apps-ios-client/blob/master/README.md#ios-client-sdk
[2]: http://azure.github.io/azure-mobile-apps-ios-client/
[3]: https://msdn.microsoft.com/library/azure/dn495101.aspx
[4]: app-service-mobile-dotnet-backend-how-to-use-server-sdk.md#tags
[5]: http://azure.github.io/azure-mobile-services/iOS/v3/Classes/MSClient.html#//api/name/invokeAPI:data:HTTPMethod:parameters:headers:completion:
[6]: https://github.com/Azure/azure-mobile-services/blob/master/sdk/iOS/src/MSError.h
[7]: app-service-mobile-how-to-configure-active-directory-authentication.md
[8]: ../active-directory/active-directory-devquickstarts-ios.md#em1-determine-what-your-redirect-uri-will-be-for-iosem
[9]: app-service-mobile-how-to-configure-facebook-authentication.md
[10]: https://developers.facebook.com/docs/ios/getting-started
