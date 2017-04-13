<properties
    pageTitle="Sende pushmeddelelser til iOS med Azure meddelelse Hubs | Microsoft Azure"
    description="I dette selvstudium lærer du at bruge Azure meddelelse Hubs til at sende pushmeddelelser til iOS programmet."
    services="notification-hubs"
    documentationCenter="ios"
    keywords="Push-meddelelse, pushmeddelelser, ios push-beskeder"
    authors="ysxu"
    manager="erikre"
    editor=""/>

<tags
    ms.service="notification-hubs"
    ms.workload="mobile"
    ms.tgt_pltfrm="mobile-ios"
    ms.devlang="objective-c"
    ms.topic="hero-article"
    ms.date="10/03/2016"
    ms.author="yuaxu"/>

# <a name="sending-push-notifications-to-ios-with-azure-notification-hubs"></a>Sende pushmeddelelser til iOS med Azure meddelelse Hubs

[AZURE.INCLUDE [notification-hubs-selector-get-started](../../includes/notification-hubs-selector-get-started.md)]

##<a name="overview"></a>Oversigt

> [AZURE.NOTE] For at fuldføre dette selvstudium skal skal du have en active Azure-konto. Hvis du ikke har en konto, kan du oprette en gratis prøveversion konto på blot et par minutter. Yderligere oplysninger finder du [Azure gratis prøveversion](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Fen-us%2Fdocumentation%2Farticles%2Fnotification-hubs-ios-get-started).

Dette selvstudium viser, hvordan du bruger Azure meddelelse Hubs til at sende pushmeddelelser til iOS programmet. Du vil oprette en tom iOS-app, der modtager pushmeddelelser ved hjælp af [Apple Push Beskedtjeneste (APNs)](https://developer.apple.com/library/ios/documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/Chapters/ApplePushService.html). 

Når du er færdig, skal du kunne bruge din meddelelse hub transmittere pushmeddelelser til alle de enheder, der kører din app.

## <a name="before-you-begin"></a>Inden du går i gang

[AZURE.INCLUDE [notification-hubs-hero-slug](../../includes/notification-hubs-hero-slug.md)]

Færdige koden for dette selvstudium kan findes [på GitHub](https://github.com/Azure/azure-notificationhubs-samples/tree/master/iOS/GetStartedNH/GetStarted). 

##<a name="prerequisites"></a>Forudsætninger

Dette selvstudium kræver følgende:

+ [Mobile-tjenester iOS SDK version 1.2.4]
+ Seneste version af [Xcode]
+ En iOS 8 (eller nyere)-kompatible enhed
+ [Apple udvikler Program](https://developer.apple.com/programs/) medlemskab.

   > [AZURE.NOTE] På grund af konfigurationskrav til pushmeddelelser, skal du installere og teste pushmeddelelser på en fysisk iOS-enhed (iPhone eller iPad) i stedet for iOS Simulator.

Fuldfør dette selvstudium er en betingelse for alle andre besked om Hubs selvstudier til iOS-apps.

[AZURE.INCLUDE [Notification Hubs Enable Apple Push Notifications](../../includes/notification-hubs-enable-apple-push-notifications.md)]

##<a name="configure-your-notification-hub-for-ios-push-notifications"></a>Konfigurere din meddelelse Hub til iOS push-beskeder

Dette afsnit fører dig gennem oprette en ny meddelelse hub og konfigurere godkendelse med APNS ved hjælp af **.p12** push-certifikat, du har oprettet. Hvis du vil bruge en meddelelse om-hub, du allerede har oprettet, kan du gå videre til trin 5.

[AZURE.INCLUDE [notification-hubs-portal-create-new-hub](../../includes/notification-hubs-portal-create-new-hub.md)]

<ol start="6">
<li>
<p>Klik på knappen <b>Besked om tjenester</b> i bladet <b>Indstillinger</b> og derefter vælge <b>Apple (APNS)</b>. Klik på <b>Overfør certifikat</b> , og vælg den <b>.p12</b> -fil, du eksporterede tidligere. Kontrollér, at du også angive den rigtige adgangskode.</p>
<p>Sørg for at vælge <b>sandkassetilstand</b> , da det er for udvikling. Brug kun <b>fremstilling</b> , hvis du vil sende pushmeddelelser til brugere, der har købt din app fra store.</p>
</li>
</ol>
&emsp;&emsp;![Konfigurere APNS Azure-portalen](./media/notification-hubs-ios-get-started/notification-hubs-apple-config.png)

&emsp;&emsp;![Konfigurere APNS certificering Azure-portalen](./media/notification-hubs-ios-get-started/notification-hubs-apple-config-cert.png)



Din meddelelse hub nu er konfigureret til at arbejde med APNS, og du har forbindelse strengene til at registrere din app og sende pushmeddelelser.

##<a name="connect-your-ios-app-to-notification-hubs"></a>Forbinde din iOS-app til meddelelse hubber

1. Oprette et nyt projekt i iOS i Xcode, og vælg skabelonen, **Enkelt visning programmet** .

    ![Xcode - programmet på computeren enkelt visning][8]

2. Når indstillinger for det nye projekt, skal du huske at bruge den samme **Produktnavn** og **Organisationens id** , som du brugte, da du tidligere har angivet samlet-ID på Apple udvikler-portalen.

    ![Xcode - indstillinger for project][11]

3. Klik på dit projektnavn under **destinationer**, klik på fanen **Opbygge indstillinger** og udvid **Kode logge identitet**, og angiv derefter din kode signering identitet under **fejlfinding af**. Skifte **niveauer** fra **grundlæggende** til **alle**, og angiv **Klargøring af profil** til den klargøring profil, som du tidligere har oprettet.

    Hvis du ikke kan se den nye klargøring profil, du oprettede i Xcode, kan du prøve at opdatere profiler til din signerende identitet. Klik på **Xcode** på menulinjen, klik på **Indstillinger**, klik på fanen **konto** , skal du klikke på knappen **Vis detaljer** , skal du klikke på din signerende identitet og derefter klikke på opdateringsknappen i det nederste højre hjørne.

    ![Xcode - klargøring profil][9]

4. Hente [Mobile-tjenester iOS SDK version 1.2.4] og udpakke filen. Højreklik på dit projekt i Xcode, og klik på **Tilføj filer til** muligheden for at tilføje mappen **WindowsAzureMessaging.framework** til projektet Xcode. Vælg **kopiere elementer, hvis det er nødvendigt**, og klik derefter på **Tilføj**.

    >[AZURE.NOTE] Meddelelse om hubs SDK understøtter ikke i øjeblikket bitcode på Xcode 7.  For dit projekt, skal du angive **Aktivér Bitcode** til **Nej** i **Opbygge indstillinger** .

    ![Udpakke Azure SDK][10]

5. Føje en ny sidehoved-fil til projektet med navnet `HubInfo.h`. Denne fil skal indeholde konstanterne til din meddelelse hub.  Tilføj følgende definitioner, og Erstat pladsholderne streng ordret med dit *hub navn* og *DefaultListenSharedAccessSignature* , du tidligere har noteret.

        #ifndef HubInfo_h
        #define HubInfo_h
        
            #define HUBNAME @"<Enter the name of your hub>"
            #define HUBLISTENACCESS @"<Enter your DefaultListenSharedAccess connection string"
        
        #endif /* HubInfo_h */

6. Åbn din `AppDelegate.h` fil tilføje følgende Importér direktiver:

         #import <WindowsAzureMessaging/WindowsAzureMessaging.h> 
         #import "HubInfo.h"
        
7. I din `AppDelegate.m file`, tilføje følgende kode i den `didFinishLaunchingWithOptions` metode baseret på din version af iOS. Denne kode registrerer din enhed håndtaget med APNs:

    Til iOS 8:

        UIUserNotificationSettings *settings = [UIUserNotificationSettings settingsForTypes:UIUserNotificationTypeSound |
                                                UIUserNotificationTypeAlert | UIUserNotificationTypeBadge categories:nil];

        [[UIApplication sharedApplication] registerUserNotificationSettings:settings];
        [[UIApplication sharedApplication] registerForRemoteNotifications];

    Til iOS-versioner før 8:

         [[UIApplication sharedApplication] registerForRemoteNotificationTypes: UIRemoteNotificationTypeAlert | UIRemoteNotificationTypeBadge | UIRemoteNotificationTypeSound];


8. Tilføj følgende fremgangsmåder i den samme fil. Denne kode opretter forbindelse til meddelelse hubben ved hjælp af forbindelsesoplysningerne, du har angivet i HubInfo.h. Derefter får enhed tokenet til meddelelse hub så meddelelse hub kan sende beskeder:

        - (void)application:(UIApplication *)application didRegisterForRemoteNotificationsWithDeviceToken:(NSData *) deviceToken {
            SBNotificationHub* hub = [[SBNotificationHub alloc] initWithConnectionString:HUBLISTENACCESS
                                        notificationHubPath:HUBNAME];

            [hub registerNativeWithDeviceToken:deviceToken tags:nil completion:^(NSError* error) {
                if (error != nil) {
                    NSLog(@"Error registering for notifications: %@", error);
                }
                else {
                    [self MessageBox:@"Registration Status" message:@"Registered"];
                }
            }];
        }

        -(void)MessageBox:(NSString *)title message:(NSString *)messageText
        {
            UIAlertView *alert = [[UIAlertView alloc] initWithTitle:title message:messageText delegate:self
                cancelButtonTitle:@"OK" otherButtonTitles: nil];
            [alert show];
        }


9. Tilføj følgende metode for at få vist en **UIAlert** , hvis meddelelsen er modtaget, mens appen er aktiv i den samme fil:


        - (void)application:(UIApplication *)application didReceiveRemoteNotification: (NSDictionary *)userInfo {
            NSLog(@"%@", userInfo);
            [self MessageBox:@"Notification" message:[[userInfo objectForKey:@"aps"] valueForKey:@"alert"]];
        }

10. Oprette og køre appen på din enhed for at bekræfte, at der er ingen fejl.

## <a name="send-test-push-notifications"></a>Sende test pushmeddelelser


Du kan teste modtage beskeder i din app ved at sende pushmeddelelser i [Azure Portal] via **fejlfinding** under bladet hub (Brug indstillingen *Teste Send* ).

![Azure Portal - Test Send][30]

[AZURE.INCLUDE [notification-hubs-sending-notifications-from-the-portal](../../includes/notification-hubs-sending-notifications-from-the-portal.md)]


## <a name="optional-send-push-notifications-from-the-app"></a>(Valgfrit) Sende pushmeddelelser fra app

>[AZURE.IMPORTANT] I dette eksempel for at sende beskeder fra klient app er angivet til at blive fortrolig kun. Da dette kræver det `DefaultFullSharedAccessSignature` for at være til stede på appen oprettelse af klienten, det udfører din meddelelse hub til risikoen, at en bruger kan få adgang for at sende uautoriseret meddelelser til dine kunder.

Hvis du vil sende pushmeddelelser fra i en app, indeholder dette afsnit et eksempel på, hvordan du gør dette ved hjælp af grænsefladen RESTEN.

1. Åbn i Xcode, `Main.storyboard` og tilføje følgende komponenter i Brugergrænsefladen fra Objektbibliotek for at tillade, at brugeren sende pushmeddelelser i appen:

    - En etiket med ingen etikettekst. Det bruges til at rapportere fejl i afsendelse af meddelelser. Egenskaben **linjer** skal angives til **0** , så det bliver automatisk størrelse er begrænset til højre og venstre margener og øverst del af visningen.
    - Et tekstfelt med **pladsholdertekst, der er angivet til **Angiv meddelelse**** . Begrænse feltet lige under navnet, som vist nedenfor. Angive den visning Controller som udgang stedfortræderen.
    - En knap, med titlen **Sende meddelelse** begrænset lige under tekstfeltet, og i vandret center.

    Visningen skal se ud som følger:

    ![Xcode designer][32]


2. [Tilføj udtag](https://developer.apple.com/library/ios/recipes/xcode_help-IB_connections/chapters/CreatingOutlet.html) for feltet etiket og tekstboks forbundet visningen og opdatere din `interface` definition til at understøtte `UITextFieldDelegate` og `NSXMLParserDelegate`. Tilføje de tre egenskab erklæringer nedenfor for at yde support til ringe REST-API og fortolkning af svaret.

    Filen ViewController.h skal se ud som følger:

        #import <UIKit/UIKit.h>

        @interface ViewController : UIViewController <UITextFieldDelegate, NSXMLParserDelegate>
        {
            NSXMLParser *xmlParser;
        }

        // Make sure these outlets are connected to your UI by ctrl+dragging
        @property (weak, nonatomic) IBOutlet UITextField *notificationMessage;
        @property (weak, nonatomic) IBOutlet UILabel *sendResults;

        @property (copy, nonatomic) NSString *statusResult;
        @property (copy, nonatomic) NSString *currentElement;

        @end

3. Åbn `HubInfo.h` og tilføje følgende konstanter, der skal bruges til at sende beskeder til centrum. Erstat den pladsholder strengkonstant med din faktiske *DefaultFullSharedAccessSignature* forbindelsesstreng.

        #define API_VERSION @"?api-version=2015-01"
        #define HUBFULLACCESS @"<Enter Your DefaultFullSharedAccess Connection string>"

4. Tilføj følgende `#import` sætninger til dit `ViewController.h` fil.

        #import <CommonCrypto/CommonHMAC.h>
        #import "HubInfo.h"

5. I `ViewController.m` føje følgende kode til implementering af brugergrænsefladen. Denne kode kan oversætte dine *DefaultFullSharedAccessSignature* forbindelsesstreng. Som nævnt i [REST-API reference](http://msdn.microsoft.com/library/azure/dn495627.aspx), bruges disse fortolket oplysninger til at oprette et SaS token for **godkendelse** anmodning sidehoved.

        NSString *HubEndpoint;
        NSString *HubSasKeyName;
        NSString *HubSasKeyValue;

        -(void)ParseConnectionString
        {
            NSArray *parts = [HUBFULLACCESS componentsSeparatedByString:@";"];
            NSString *part;

            if ([parts count] != 3)
            {
                NSException* parseException = [NSException exceptionWithName:@"ConnectionStringParseException"
                    reason:@"Invalid full shared access connection string" userInfo:nil];

                @throw parseException;
            }

            for (part in parts)
            {
                if ([part hasPrefix:@"Endpoint"])
                {
                    HubEndpoint = [NSString stringWithFormat:@"https%@",[part substringFromIndex:11]];
                }
                else if ([part hasPrefix:@"SharedAccessKeyName"])
                {
                    HubSasKeyName = [part substringFromIndex:20];
                }
                else if ([part hasPrefix:@"SharedAccessKey"])
                {
                    HubSasKeyValue = [part substringFromIndex:16];
                }
            }
        }

6. I `ViewController.m`, opdatere den `viewDidLoad` metode til at fortolke forbindelsesstrengen, når visningen indlæses. Også tilføje metoderne utility, vist nedenfor, til implementering af brugergrænsefladen.  


        - (void)viewDidLoad
        {
            [super viewDidLoad];
            [self ParseConnectionString];
            [_notificationMessage setDelegate:self];
        }

        -(NSString *)CF_URLEncodedString:(NSString *)inputString
        {
           return (__bridge NSString *)CFURLCreateStringByAddingPercentEscapes(NULL, (CFStringRef)inputString,
                NULL, (CFStringRef)@"!*'();:@&=+$,/?%#[]", kCFStringEncodingUTF8);
        }

        -(void)MessageBox:(NSString *)title message:(NSString *)messageText
        {
            UIAlertView *alert = [[UIAlertView alloc] initWithTitle:title message:messageText delegate:self
                cancelButtonTitle:@"OK" otherButtonTitles: nil];
            [alert show];
        }





7. I `ViewController.m`, føje følgende kode til interface implementering til at generere SaS godkendelse tokenet, der leveres i sidehovedet **tilladelse** som nævnt i [RESTEN API Reference](http://msdn.microsoft.com/library/azure/dn495627.aspx).

        -(NSString*) generateSasToken:(NSString*)uri
        {
            NSString *targetUri;
            NSString* utf8LowercasedUri = NULL;
            NSString *signature = NULL;
            NSString *token = NULL;

            @try
            {
                // Add expiration
                uri = [uri lowercaseString];
                utf8LowercasedUri = [self CF_URLEncodedString:uri];
                targetUri = [utf8LowercasedUri lowercaseString];
                NSTimeInterval expiresOnDate = [[NSDate date] timeIntervalSince1970];
                int expiresInMins = 60; // 1 hour
                expiresOnDate += expiresInMins * 60;
                UInt64 expires = trunc(expiresOnDate);
                NSString* toSign = [NSString stringWithFormat:@"%@\n%qu", targetUri, expires];

                // Get an hmac_sha1 Mac instance and initialize with the signing key
                const char *cKey  = [HubSasKeyValue cStringUsingEncoding:NSUTF8StringEncoding];
                const char *cData = [toSign cStringUsingEncoding:NSUTF8StringEncoding];
                unsigned char cHMAC[CC_SHA256_DIGEST_LENGTH];
                CCHmac(kCCHmacAlgSHA256, cKey, strlen(cKey), cData, strlen(cData), cHMAC);
                NSData *rawHmac = [[NSData alloc] initWithBytes:cHMAC length:sizeof(cHMAC)];
                signature = [self CF_URLEncodedString:[rawHmac base64EncodedStringWithOptions:0]];

                // Construct authorization token string
                token = [NSString stringWithFormat:@"SharedAccessSignature sig=%@&se=%qu&skn=%@&sr=%@",
                    signature, expires, HubSasKeyName, targetUri];
            }
            @catch (NSException *exception)
            {
                [self MessageBox:@"Exception Generating SaS Token" message:[exception reason]];
            }
            @finally
            {
                if (utf8LowercasedUri != NULL)
                    CFRelease((CFStringRef)utf8LowercasedUri);
                if (signature != NULL)
                CFRelease((CFStringRef)signature);
            }

            return token;
        }


8. CTRL + træk fra **Sende meddelelse** knappen til `ViewController.m` til at føje en handling med navnet **SendNotificationMessage** for hændelsen **Tryk ned** . Opdater metode med følgende kode til at sende meddelelsen til brug af REST-API.

        - (IBAction)SendNotificationMessage:(id)sender
        {
            self.sendResults.text = @"";
            [self SendNotificationRESTAPI];
        }

        - (void)SendNotificationRESTAPI
        {
            NSURLSession* session = [NSURLSession
                             sessionWithConfiguration:[NSURLSessionConfiguration defaultSessionConfiguration]
                             delegate:nil delegateQueue:nil];

            // Apple Notification format of the notification message
            NSString *json = [NSString stringWithFormat:@"{\"aps\":{\"alert\":\"%@\"}}",
                                self.notificationMessage.text];

            // Construct the message's REST endpoint
            NSURL* url = [NSURL URLWithString:[NSString stringWithFormat:@"%@%@/messages/%@", HubEndpoint,
                                                HUBNAME, API_VERSION]];

            // Generate the token to be used in the authorization header
            NSString* authorizationToken = [self generateSasToken:[url absoluteString]];

            //Create the request to add the APNs notification message to the hub
            NSMutableURLRequest *request = [NSMutableURLRequest requestWithURL:url];
            [request setHTTPMethod:@"POST"];
            [request setValue:@"application/json;charset=utf-8" forHTTPHeaderField:@"Content-Type"];

            // Signify Apple notification format
            [request setValue:@"apple" forHTTPHeaderField:@"ServiceBusNotification-Format"];

            //Authenticate the notification message POST request with the SaS token
            [request setValue:authorizationToken forHTTPHeaderField:@"Authorization"];

            //Add the notification message body
            [request setHTTPBody:[json dataUsingEncoding:NSUTF8StringEncoding]];

            // Send the REST request
            NSURLSessionDataTask* dataTask = [session dataTaskWithRequest:request
                completionHandler:^(NSData *data, NSURLResponse *response, NSError *error)
            {
                NSHTTPURLResponse* httpResponse = (NSHTTPURLResponse*) response;
                if (error || (httpResponse.statusCode != 200 && httpResponse.statusCode != 201))
                {
                    NSLog(@"\nError status: %d\nError: %@", httpResponse.statusCode, error);
                }
                if (data != NULL)
                {
                    xmlParser = [[NSXMLParser alloc] initWithData:data];
                    [xmlParser setDelegate:self];
                    [xmlParser parse];
                }
            }];
            [dataTask resume];
        }


9. I `ViewController.m`, føje metoden følgende stedfortræder for at understøtte lukke tastaturet for tekstfeltet. CTRL + træk fra tekstfeltet til ikonet Vis Controller i grænsefladen designer til at angive Vis controller som udgang stedfortræderen.

        //===[ Implement UITextFieldDelegate methods ]===

        -(BOOL)textFieldShouldReturn:(UITextField *)textField
        {
            [textField resignFirstResponder];
            return YES;
        }


10. I `ViewController.m`, tilføje fremgangsmåderne stedfortræder for at understøtte parsing af svaret ved hjælp af `NSXMLParser`.

        //===[ Implement NSXMLParserDelegate methods ]===

        -(void)parserDidStartDocument:(NSXMLParser *)parser
        {
            self.statusResult = @"";
        }

        -(void)parser:(NSXMLParser *)parser didStartElement:(NSString *)elementName
            namespaceURI:(NSString *)namespaceURI qualifiedName:(NSString *)qName
            attributes:(NSDictionary *)attributeDict
        {
            NSString * element = [elementName lowercaseString];
            NSLog(@"*** New element parsed : %@ ***",element);

            if ([element isEqualToString:@"code"] | [element isEqualToString:@"detail"])
            {
                self.currentElement = element;
            }
        }

        -(void) parser:(NSXMLParser *)parser foundCharacters:(NSString *)parsedString
        {
            self.statusResult = [self.statusResult stringByAppendingString:
                [NSString stringWithFormat:@"%@ : %@\n", self.currentElement, parsedString]];
        }

        -(void)parserDidEndDocument:(NSXMLParser *)parser
        {
            // Set the status label text on the UI thread
            dispatch_async(dispatch_get_main_queue(),
            ^{
                [self.sendResults setText:self.statusResult];
            });
        }



11. Opbygge projektet, og Bekræft, at der ikke er nogen fejl.


> [AZURE.NOTE] Hvis du støder på fejlen build i Xcode7 om understøttelse af bitcode, skal du ændre **Indstillinger for opbygge** > **Aktivere Bitcode (ENABLE_BITCODE)** til **Nej** i Xcode. Meddelelse om Hubs SDK understøtter i øjeblikket ikke bitcode. 

Du kan finde alle mulige meddelelse overførsler i Apple [Push meddelelse Programming vejledning og lokale].


##<a name="checking-if-your-app-can-receive-push-notifications"></a>Kontrollere, hvis din app kan modtage pushmeddelelser

Hvis du vil teste pushmeddelelser på iOS, skal du installere appen på en fysisk iOS-enhed. Du kan ikke sende Apple pushmeddelelser ved hjælp af iOS Simulator.

1. Køre appen, og Bekræft, at registrering lykkes, og tryk derefter på **OK**.

    ![iOS App Push meddelelse registrering Test][33]

2. Du kan sende en test push-meddelelse fra [Azure-portalen], som beskrevet ovenfor. Hvis du har tilføjet kode til at sende pushmeddelelser i appen, skal du trykke inde i tekstfeltet til at skrive en meddelelse. Tryk derefter på knappen **Send** på tastaturet eller knappen **Send besked** i visningen for at sende meddelelsen om opgaven.

    ![iOS App Push meddelelse sende Test][34]

3. Push-meddelelse sendes til alle enheder, der er registreret for at modtage meddelelserne fra bestemte meddelelser Hub.

    ![iOS App Push meddelelse modtage Test][35]


##<a name="next-steps"></a>Næste trin

I denne enkle eksempel udsendes du pushmeddelelser til alle registrerede iOS-enheder. Vi anbefaler som et næste trin i din viden, som du fortsætte til det [Azure meddelelse Hubs Giv brugerne til iOS med .NET back end-] selvstudium, som fører dig gennem oprettelse af en back-end for at sende pushmeddelelser ved hjælp af mærker. 

Hvis du vil inddele dine brugere ved interesse grupper, kan du desuden flytte på til Selvstudium i [Brug meddelelse Hubs sende seneste nyheder] . 

Du kan finde generelle oplysninger om meddelelse Hubs, [Meddelelse om Hubs vejledning].



<!-- Images. -->

[6]: ./media/notification-hubs-ios-get-started/notification-hubs-configure-ios.png
[8]: ./media/notification-hubs-ios-get-started/notification-hubs-create-ios-app.png
[9]: ./media/notification-hubs-ios-get-started/notification-hubs-create-ios-app2.png
[10]: ./media/notification-hubs-ios-get-started/notification-hubs-create-ios-app3.png
[11]: ./media/notification-hubs-ios-get-started/notification-hubs-xcode-product-name.png

[30]: ./media/notification-hubs-ios-get-started/notification-hubs-test-send.png

[31]: ./media/notification-hubs-ios-get-started/notification-hubs-ios-ui.png
[32]: ./media/notification-hubs-ios-get-started/notification-hubs-storyboard-view.png
[33]: ./media/notification-hubs-ios-get-started/notification-hubs-test1.png
[34]: ./media/notification-hubs-ios-get-started/notification-hubs-test2.png
[35]: ./media/notification-hubs-ios-get-started/notification-hubs-test3.png



<!-- URLs. -->
[Mobile-tjenester iOS SDK version 1.2.4]: http://aka.ms/kymw2g
[Mobile Services iOS SDK]: http://go.microsoft.com/fwLink/?LinkID=266533
[Submit an app page]: http://go.microsoft.com/fwlink/p/?LinkID=266582
[My Applications]: http://go.microsoft.com/fwlink/p/?LinkId=262039
[Live SDK for Windows]: http://go.microsoft.com/fwlink/p/?LinkId=262253

[Get started with Mobile Services]: /develop/mobile/tutorials/get-started-ios
[Azure Classic Portal]: https://manage.windowsazure.com/
[Meddelelse om Hubs vejledning]: http://msdn.microsoft.com/library/jj927170.aspx
[Xcode]: https://go.microsoft.com/fwLink/p/?LinkID=266532
[iOS Provisioning Portal]: http://go.microsoft.com/fwlink/p/?LinkId=272456

[Get started with push notifications in Mobile Services]: ../mobile-services-javascript-backend-ios-get-started-push.md
[Azure meddelelse Hubs Giv brugerne besked til iOS med .NET back end-]: notification-hubs-aspnet-backend-ios-apple-apns-notification.md
[Brug meddelelse Hubs til at sende seneste nyheder]: notification-hubs-ios-xplat-segmented-apns-push-notification.md

[Lokale, og Skub besked om programmering vejledning]: http://developer.apple.com/library/mac/#documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/Chapters/ApplePushService.html#//apple_ref/doc/uid/TP40008194-CH100-SW1
[Azure-portalen]: https://portal.azure.com