<properties
    pageTitle="Azure AD-iOS Introduktion | Microsoft Azure"
    description="Sådan oprettes en iOS-program, der integreres med Azure AD til logon og Azure AD-opkald beskyttet API'er ved hjælp af OAuth."
    services="active-directory"
    documentationCenter="ios"
    authors="brandwe"
    manager="mbaldwin"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="mobile-ios"
    ms.devlang="objective-c"
    ms.topic="article"
    ms.date="09/16/2016"
    ms.author="brandwe"/>

# <a name="integrate-azure-ad-into-an-ios-app"></a>Integrere Azure AD i en iOS-App

[AZURE.INCLUDE [active-directory-devquickstarts-switcher](../../includes/active-directory-devquickstarts-switcher.md)]

[AZURE.INCLUDE [active-directory-devguide](../../includes/active-directory-devguide.md)] 

Azure AD indeholder Active Directory Authentication Library eller ADAL, til iOS-klienter, der skal have adgang til beskyttede ressourcer.  ADALS eneste formål i liv er at gøre det lettere for din app til at få adgangstokens.  For at vise lige hvor let det er skal her vi oprettes et målsætning C opgaveliste til computeren, der:

-   Henter få adgang til tokens for opkald til Azure AD Graph API ved hjælp af [OAuth 2.0 godkendelse protocol](https://msdn.microsoft.com/library/azure/dn645545.aspx).
-   Søger i en mappe til brugere med en given alias.

Hvis du vil basere fuldført arbejde programmet, skal du:

2. Registrere dit program med Azure AD.
3. Installere og konfigurere ADAL.
5. Brug ADAL til at få tokens fra Azure AD.

At komme i gang skal [hente app skelet](https://github.com/AzureADQuickStarts/NativeClient-iOS/archive/skeleton.zip) eller [hente gennemført prøveoverførsel](https://github.com/AzureADQuickStarts/NativeClient-iOS/archive/complete.zip).  Du skal også en Azure AD-lejer, hvor du kan oprette brugere og registrere et program.  Hvis du ikke allerede har en lejer, [se, hvordan du få en](active-directory-howto-tenant.md).

> [AZURE.TIP] Prøv eksemplet på vores nye [udvikler portal](https://identity.microsoft.com/Docs/iOS) , der kan hjælpe dig med at blive oppe at køre med Azure Active Directory på blot nogle få minutter!  Portalen udvikler fører dig gennem processen med at registrere en app og integrere Azure AD i din kode.  Når du er færdig, får du et enkelt program, der kan godkende brugere i din lejer og en back-end, der kan acceptere tokens og udføre datavalidering. 

## <a name="1-determine-what-your-redirect-uri-will-be-for-ios"></a>*1. bestemme, hvad din omdirigere URI skal være til iOS*

Hvis du vil sikkert Start dine programmer i visse scenarier med SSO kræver vi, at du opretter en **Omdirigere URI** i et bestemt format. En omdirigere URI bruges til at sikre, tokens vender tilbage til det korrekte program, bedt om dem.

IOS-format for en omdirigere URI er:

```
<app-scheme>://<bundle-id>
```

-   **aap farveskemaet** - det er registreret i projektet XCode. Det er, hvordan andre programmer kan ringe dig op. Du kan finde dette under Info.plist -> URL-adressen typer -> URL-id. Du skal oprette en, hvis du ikke allerede har en eller flere konfigureret.
-   **Samlet-id** - dette er den samlet-id, der blev fundet under "identitet" Fjern dine indstillinger for project i XCode.

Der ville være et eksempel på denne kode i Hurtig start: ***msquickstart://com.microsoft.azureactivedirectory.samples.graph.QuickStart***

## <a name="2-register-the-directorysearcher-application"></a>*2. registrere programmet DirectorySearcher*
For at aktivere din app til at hente tokens, skal du først at registrere den i din Azure AD-lejer og tildele den tilladelse til at få adgang til API Azure AD Graph:

-   Log på administrationsportalen Azure
-   Klik på **Active Directory** i til venstre i navigationen
-   Vælg en lejer, til at registrere programmet.
-   Klik på fanen **programmer** , og klik på **Tilføj** i den nederste skuffe.
-   Følg anvisningerne, og oprette et nyt **Oprindelige-klientprogrammet**.
    -   **Navnet** på programmet, som beskriver dit program for slutbrugere
    -   **Omdirigere Uri** er en kombination af farveskema og streng, Azure AD vil bruge til at returnere token svar.  Angive en bestemt værdi i dit program, der er baseret på ovenstående oplysninger.
-   Når du har færdiggjort registrering, Tildel AAD din app et entydigt klient-id.  Du skal bruge denne værdi i næste afsnit, så kopiere det fra fanen **Konfigurer** .
- Find også sektionen "Tilladelser til andre programmer" **Konfigurer** under fanen.  Tilføj tilladelsen **Access den din organisations Directory** under **Delegerede tilladelser**til programmet "Azure Active Directory".  Dette vil gøre det muligt at forespørge Graph API til brugere.

## <a name="3-install--configure-adal"></a>*3. installere og konfigurere ADAL*
Nu hvor du har et program i Azure AD, kan du installere ADAL og skrive din identitet-relaterede kode.  I rækkefølge for ADAL lov til at kommunikere med Azure AD, skal du give den nogle oplysninger om din app registrering.
-   Start med at tilføje ADAL til DirectorySearcher projektet ved hjælp af Cocapods.

```
$ vi Podfile
```
Tilføj følgende denne podfile:

```
source 'https://github.com/CocoaPods/Specs.git'
link_with ['QuickStart']
xcodeproj 'QuickStart'

pod 'ADALiOS'
```

Nu indlæse podfile ved hjælp af cocoapods. Dette vil oprette et nyt XCode arbejdsområde, du vil indlæse.

```
$ pod install
...
$ open QuickStart.xcworkspace
```

-   Åbn filen plist i Hurtig start projektet, `settings.plist`.  Erstatte værdier af elementer i afsnittet for at afspejle de værdier, du indtaster i portalen Azure.  Din kode referencer disse værdier, når den anvender ADAL.
    -   Den `tenant` er domænet for din Azure AD-lejer, f.eks. contoso.onmicrosoft.com
    -   Den `clientId` er clientId af dit program, du har kopieret fra portalen.
    -   Den `redirectUri` er Omdiriger URL-adresse, der er registreret i portalen.

## <a name="4--use-adal-to-get-tokens-from-aad"></a>*4. Brug ADAL til at få Tokens fra AAD*
Grundlæggende princippet bag ADAL er, når din app skal et adgangstoken, skal du blot kalder en completionBlock `+(void) getToken : `, og ADAL gør resten.  

-   I den `QuickStart` project, Åbn `GraphAPICaller.m` og Find den `// TODO: getToken for generic Web API flows. Returns a token with no additional parameters provided.` kommentar i øverst.  Dette er, hvor du overfører ADAL koordinaterne gennem en CompletionBlock til at kommunikere med Azure AD og angiver, hvordan til cachen tokens.

```ObjC
+(void) getToken : (BOOL) clearCache
           parent:(UIViewController*) parent
completionHandler:(void (^) (NSString*, NSError*))completionBlock;
{
    AppData* data = [AppData getInstance];
    if(data.userItem){
        completionBlock(data.userItem.accessToken, nil);
        return;
    }

    ADAuthenticationError *error;
    authContext = [ADAuthenticationContext authenticationContextWithAuthority:data.authority error:&error];
    authContext.parentController = parent;
    NSURL *redirectUri = [[NSURL alloc]initWithString:data.redirectUriString];

    [ADAuthenticationSettings sharedInstance].enableFullScreen = YES;
    [authContext acquireTokenWithResource:data.resourceId
                                 clientId:data.clientId
                              redirectUri:redirectUri
                           promptBehavior:AD_PROMPT_AUTO
                                   userId:data.userItem.userInformation.userId
                     extraQueryParameters: @"nux=1" // if this strikes you as strange it was legacy to display the correct mobile UX. You most likely won't need it in your code.
                          completionBlock:^(ADAuthenticationResult *result) {

                              if (result.status != AD_SUCCEEDED)
                              {
                                  completionBlock(nil, result.error);
                              }
                              else
                              {
                                  data.userItem = result.tokenCacheStoreItem;
                                  completionBlock(result.tokenCacheStoreItem.accessToken, nil);
                              }
                          }];
}

```

- Nu skal vi bruge dette token til at søge efter brugere i diagrammet. Finde den `// TODO: implement SearchUsersList` anbefaler metode gør en GET-anmodning til Azure AD Graph API til forespørgsel for brugere, hvis UPN begynder med det angivne søgeord.  Men hvis du vil forespørge Graph API, skal du medtage en access_token i den `Authorization` sidehoved for din anmodning - dette er, hvor ADAL kommer.

```ObjC
+(void) searchUserList:(NSString*)searchString
                parent:(UIViewController*) parent
       completionBlock:(void (^) (NSMutableArray* Users, NSError* error)) completionBlock
{
    if (!loadedApplicationSettings)
    {
        [self readApplicationSettings];
    }

    AppData* data = [AppData getInstance];

    NSString *graphURL = [NSString stringWithFormat:@"%@%@/users?api-version=%@&$filter=startswith(userPrincipalName, '%@')", data.taskWebApiUrlString, data.tenant, data.apiversion, searchString];


    [self craftRequest:[self.class trimString:graphURL]
                parent:parent
     completionHandler:^(NSMutableURLRequest *request, NSError *error) {

         if (error != nil)
         {
             completionBlock(nil, error);
         }
         else
         {

             NSOperationQueue *queue = [[NSOperationQueue alloc]init];

             [NSURLConnection sendAsynchronousRequest:request queue:queue completionHandler:^(NSURLResponse *response, NSData *data, NSError *error) {

                 if (error == nil && data != nil){

                     NSDictionary *dataReturned = [NSJSONSerialization JSONObjectWithData:data options:0 error:nil];

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
                         s.name =[keyValuePairs valueForKey:@"givenName"];

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
     }];

}

```
- Når din app anmoder om et token ved at ringe til `getToken(...)`, ADAL vil forsøge at returnere et token uden at spørge brugeren til legitimationsoplysninger.  Hvis ADAL registrerer, at brugeren skal logge på at få et token, viser en dialogboks til logon, indsamling brugerens legitimationsoplysninger og returnere et token efter vellykket bekræftelse.  Hvis ADAL er i stand til at returnere et token for en eller anden grund, kan det Udløs en `AdalException`.
- Bemærk, at den `AuthenticationResult` objekt indeholder en `tokenCacheStoreItem` objekt, der kan bruges til at indsamle oplysninger, der kan være nødvendigt din app.  I Hurtig start, `tokenCacheStoreItem` bruges til at bestemme, hvis der allerede er foregået authenitcation.


## <a name="step-5-build-and-run-the-application"></a>Trin 5: Oprette og køre programmet



Tillykke! Du kan nu har et arbejde iOS-program, der har mulighed for at godkende brugere, skal du ringe sikkert Web API'er ved hjælp af OAuth 2.0, og få grundlæggende oplysninger om brugeren.  Hvis du ikke allerede har gjort det, nu er det tid til at udfylde din lejer med nogle brugere.  Kør din app i Hurtig start, og log på med en af disse brugere.  Søg efter andre brugere, der er baseret på deres UPN.  Luk app, og kør den igen.  Bemærk, hvordan brugerens session forbliver intakt.

ADAL gør det nemt at inkorporere alle disse almindelige identitet-funktioner i dit program.  Det sig tager af alle ændret arbejdet for dig - cache-styring, OAuth-protokollen support, præsenterer brugeren med et login brugergrænseflade, opdatere udløbet tokens og meget mere.  Alt, du virkelig vil vide, er et enkelt API-kald, `getToken`.

Til reference gennemført prøveoverførsel (uden din konfigurationsværdier) er angivet [her](https://github.com/AzureADQuickStarts/NativeClient-iOS/archive/complete.zip).  

## <a name="additional-scenarios"></a>Ekstra scenarier
Du kan nu gå videre til yderligere scenarier.  Vil du måske at prøve:

- [Sikre en Node.JS Web API med Azure AD](active-directory-devquickstarts-webapi-nodejs.md)
- Lær, [hvordan du aktiverer SSO i tværs-app på iOS ved hjælp af ADAL](active-directory-sso-ios.md)  

[AZURE.INCLUDE [active-directory-devquickstarts-additional-resources](../../includes/active-directory-devquickstarts-additional-resources.md)]
