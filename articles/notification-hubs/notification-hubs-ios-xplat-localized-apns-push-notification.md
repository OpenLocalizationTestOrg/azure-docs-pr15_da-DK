<properties
    pageTitle="Meddelelse om Hubs lokaliseret Bryd nyheder selvstudium til iOS"
    description="Lær at bruge Azure Service Bus meddelelse Hubs til at sende oversatte seneste nyheder meddelelser (iOS)."
    services="notification-hubs"
    documentationCenter="ios"
    authors="ysxu"
    manager="erikre"
    editor=""/>

<tags
    ms.service="notification-hubs"
    ms.workload="mobile"
    ms.tgt_pltfrm="ios"
    ms.devlang="objective-c"
    ms.topic="article"
    ms.date="10/03/2016"
    ms.author="yuaxu"/>

# <a name="use-notification-hubs-to-send-localized-breaking-news-to-ios-devices"></a>Brug Hubs besked om at sende oversatte seneste nyheder til iOS-enheder

> [AZURE.SELECTOR]
- [Windows Store C#](notification-hubs-windows-store-dotnet-xplat-localized-wns-push-notification)
- [iOS](notification-hubs-ios-xplat-localized-apns-push-notification)


##<a name="overview"></a>Oversigt

Dette emne beskrives, hvordan du bruger funktionen [skabeloner](notification-hubs-templates-cross-platform-push-messages.md) i Azure meddelelse Hubs til at transmittere seneste nyheder meddelelser, der er blevet oversat ved sprog og enhed. I dette selvstudium starter du med iOS-app, der er oprettet i [Brug meddelelse Hubs sende seneste nyheder]. Når du er færdig, vil du kunne registrere for kategorier, du er interesseret i, skal du angive et sprog, som skal modtage meddelelserne, og modtage kun pushmeddelelser for de valgte kategorier på det pågældende sprog.


Der er to dele dette scenarie:

- iOS app kan klienten enheder til at angive et sprog, og at abonnere på forskellige seneste nyhedskategorier.

- back end-udsender meddelelser, ved hjælp af **skabelonen** og **mærke** feautres af Azure meddelelse Hubs.



##<a name="prerequisites"></a>Forudsætninger

Du skal allerede har fuldført selvstudiet [Brug meddelelse Hubs sende seneste nyheder] og har den kode, der er tilgængelige, fordi dette selvstudium bygger direkte på denne kode.

Visual Studio 2012 eller nyere er valgfrit.



##<a name="template-concepts"></a>Skabelon begreber

I [Brug meddelelse Hubs sende seneste nyheder] indbygget du en app, bruges **mærker** til at abonnere på meddelelser for forskellige nyhedskategorier.
Mange apps, men målrette flere markeder og kræver lokalisering. Det betyder, at indholdet af meddelelserne selv skal være lokaliseret og leveret til det korrekte sæt enheder.
I dette emne får du vist Sådan bruges funktionen **skabelon** i meddelelse Hubs til at levere nemt oversatte seneste nyheder beskeder.

Bemærk: en metode til at sende oversatte meddelelser er at oprette flere versioner af hver kode. For eksempel for at understøtte engelsk, fransk og Mandarin, vi har brug for tre forskellige mærker for verden nyheder: "world_en", "world_fr" og "world_ch". Vi herefter har til at sende en oversat version af internationale nyheder til hver af disse koder. Vi bruger skabeloner til at undgå udbredelsen af mærker og kravet om at sende flere meddelelser i dette emne.

Skabeloner er en metode til at angive, hvordan en bestemt enhed skal modtage en besked på et højt niveau. Skabelonen angiver formatet nøjagtige data ved at referere til egenskaber, der er en del af den meddelelse, som din app back end. I dette tilfælde har vi sender en landestandard agnostic meddelelse, der indeholder alle understøttede sprog:

    {
        "News_English": "...",
        "News_French": "...",
        "News_Mandarin": "..."
    }

Derefter sikrer vi, at registrere enheder med en skabelon, der refererer til den korrekte egenskab. For eksempel vil en iOS-app, som ønsker at registrere for franske nyheder registrere følgende:

    {
        aps:{
            alert: "$(News_French)"
        }
    }

Skabeloner er en meget effektiv funktion, du kan læse mere om i vores [skabeloner](notification-hubs-templates-cross-platform-push-messages.md) artikel.

##<a name="the-app-user-interface"></a>App-brugergrænseflade

Vi vil nu ændre appen Bryd nyheder, du oprettede i emnet [Brug meddelelse Hubs sende seneste nyheder] til at sende oversatte seneste nyheder ved hjælp af skabeloner.


Tilføje et segmenteret kontrolelement med de tre sprog, som vi understøtter i din MainStoryboard_iPhone.storyboard: engelsk, fransk og Mandarin.

![][13]

Sørg for at tilføje en IBOutlet i din ViewController.h, som vist nedenfor:

![][14]

##<a name="building-the-ios-app"></a>Bygge iOS-app


1. Føje metoden *retrieveLocale* i din Notification.h og ændre store og abonnere metoder, som vist nedenfor:

        - (void) storeCategoriesAndSubscribeWithLocale:(int) locale categories:(NSSet*) categories completion: (void (^)(NSError* error))completion;

        - (void) subscribeWithLocale:(int) locale categories:(NSSet*) categories completion:(void (^)(NSError *))completion;

        - (NSSet*) retrieveCategories;

        - (int) retrieveLocale;

    Ændre metoden *storeCategoriesAndSubscribe* ved at tilføje parameteren landestandard og gemme den i bruger som standard i din Notification.m:

        - (void) storeCategoriesAndSubscribeWithLocale:(int) locale categories:(NSSet *)categories completion:(void (^)(NSError *))completion {
            NSUserDefaults* defaults = [NSUserDefaults standardUserDefaults];
            [defaults setValue:[categories allObjects] forKey:@"BreakingNewsCategories"];
            [defaults setInteger:locale forKey:@"BreakingNewsLocale"];
            [defaults synchronize];

            [self subscribeWithLocale: locale categories:categories completion:completion];
        }

    Derefter ændre metoden *abonnere* for at medtage de internationale indstillinger:

        - (void) subscribeWithLocale: (int) locale categories:(NSSet *)categories completion:(void (^)(NSError *))completion{
            SBNotificationHub* hub = [[SBNotificationHub alloc] initWithConnectionString:@"<connection string>" notificationHubPath:@"<hub name>"];

            NSString* localeString;
            switch (locale) {
                case 0:
                    localeString = @"English";
                    break;
                case 1:
                    localeString = @"French";
                    break;
                case 2:
                    localeString = @"Mandarin";
                    break;
            }

            NSString* template = [NSString stringWithFormat:@"{\"aps\":{\"alert\":\"$(News_%@)\"},\"inAppMessage\":\"$(News_%@)\"}", localeString, localeString];

            [hub registerTemplateWithDeviceToken:self.deviceToken name:@"localizednewsTemplate" jsonBodyTemplate:template expiryTemplate:@"0" tags:categories completion:completion];
        }

    Bemærk, hvordan vi bruger nu metode *registerTemplateWithDeviceToken*i stedet for *registerNativeWithDeviceToken*. Vi har give skabelonen json og også et navn til skabelonen (som vores måske til at registrere forskellige skabeloner), når vi registrerer efter en skabelon. Sørg for at registrere dine kategorier som mærker, som vi vil sikre dig at modtage notifciations for disse nyheder.

    Tilføje en metode til at hente den valgte landestandard fra standardindstillingerne bruger:

        - (int) retrieveLocale {
            NSUserDefaults* defaults = [NSUserDefaults standardUserDefaults];

            int locale = [defaults integerForKey:@"BreakingNewsLocale"];

            return locale < 0?0:locale;
        }

2. Nu hvor vi har ændret vores beskeder klasse, har vi at sikre dig, at vores ViewController gør brug af den nye UISegmentControl. Tilføj følgende linje i *viewDidLoad* metoden til at sørge for at vise de internationale indstillinger, der er valgt i øjeblikket:

        self.Locale.selectedSegmentIndex = [notifications retrieveLocale];

    Ændre dit opkald til *storeCategoriesAndSubscribe* følger i din *abonnere* metode:

        [notifications storeCategoriesAndSubscribeWithLocale: self.Locale.selectedSegmentIndex categories:[NSSet setWithArray:categories] completion: ^(NSError* error) {
            if (!error) {
                UIAlertView *alert = [[UIAlertView alloc] initWithTitle:@"Notification" message:
                                      @"Subscribed!" delegate:nil cancelButtonTitle:
                                      @"OK" otherButtonTitles:nil, nil];
                [alert show];
            } else {
                NSLog(@"Error subscribing: %@", error);
            }
        }];

3. Til sidst skal skal du opdatere metoden *didRegisterForRemoteNotificationsWithDeviceToken* i din AppDelegate.m, så du kan opdatere din tilmelding korrekt, når din app starter. Ændre dit opkald til metoden *abonnere* på meddelelser med følgende:

        NSSet* categories = [self.notifications retrieveCategories];
        int locale = [self.notifications retrieveLocale];
        [self.notifications subscribeWithLocale: locale categories:categories completion:^(NSError* error) {
            if (error != nil) {
                NSLog(@"Error registering for notifications: %@", error);
            }
        }];

##<a name="optional-send-localized-template-notifications-from-net-console-app"></a>(valgfrit) Sende meddelelser om oversatte skabeloner fra .NET console app.

[AZURE.INCLUDE [notification-hubs-localized-back-end](../../includes/notification-hubs-localized-back-end.md)]



##<a name="optional-send-localized-template-notifications-from-the-device"></a>(valgfrit) Sende meddelelser om oversatte skabeloner fra enheden

Hvis du ikke har adgang til Visual Studio, eller vil kun teste sende meddelelserne oversatte skabelon direkte fra app på enheden.  Du kan simple tilføje parametrene oversatte skabelon til den `SendNotificationRESTAPI` metode, du har defineret i den tidligere selvstudium.

        - (void)SendNotificationRESTAPI:(NSString*)categoryTag
        {
            NSURLSession* session = [NSURLSession sessionWithConfiguration:[NSURLSessionConfiguration
                                     defaultSessionConfiguration] delegate:nil delegateQueue:nil];

            NSString *json;

            // Construct the messages REST endpoint
            NSURL* url = [NSURL URLWithString:[NSString stringWithFormat:@"%@%@/messages/%@", HubEndpoint,
                                               HUBNAME, API_VERSION]];

            // Generated the token to be used in the authorization header.
            NSString* authorizationToken = [self generateSasToken:[url absoluteString]];

            //Create the request to add the template notification message to the hub
            NSMutableURLRequest *request = [NSMutableURLRequest requestWithURL:url];
            [request setHTTPMethod:@"POST"];

            // Add the category as a tag
            [request setValue:categoryTag forHTTPHeaderField:@"ServiceBusNotification-Tags"];

            // Template notification
            json = [NSString stringWithFormat:@"{\"messageParam\":\"Breaking %@ News : %@\","
                    \"News_English\":\"Breaking %@ News in English : %@\","
                    \"News_French\":\"Breaking %@ News in French : %@\","
                    \"News_Mandarin\":\"Breaking %@ News in Mandarin : %@\","
                    categoryTag, self.notificationMessage.text,
                    categoryTag, self.notificationMessage.text,  // insert English localized news here
                    categoryTag, self.notificationMessage.text,  // insert French localized news here
                    categoryTag, self.notificationMessage.text]; // insert Mandarin localized news here

            // Signify template notification format
            [request setValue:@"template" forHTTPHeaderField:@"ServiceBusNotification-Format"];

            // JSON Content-Type
            [request setValue:@"application/json;charset=utf-8" forHTTPHeaderField:@"Content-Type"];

            //Authenticate the notification message POST request with the SaS token
            [request setValue:authorizationToken forHTTPHeaderField:@"Authorization"];

            //Add the notification message body
            [request setHTTPBody:[json dataUsingEncoding:NSUTF8StringEncoding]];

            // Send the REST request
            NSURLSessionDataTask* dataTask = [session dataTaskWithRequest:request
                       completionHandler:^(NSData *data, NSURLResponse *response, NSError *error)
               {
               NSHTTPURLResponse* httpResponse = (NSHTTPURLResponse*) response;
                   if (error || httpResponse.statusCode != 200)
                   {
                       NSLog(@"\nError status: %d\nError: %@", httpResponse.statusCode, error);
                   }
                   if (data != NULL)
                   {
                       //xmlParser = [[NSXMLParser alloc] initWithData:data];
                       //[xmlParser setDelegate:self];
                       //[xmlParser parse];
                   }
               }];

            [dataTask resume];
        }




## <a name="next-steps"></a>Næste trin

Du kan finde flere oplysninger om brug af skabeloner, i:

- [Give brugerne besked med besked om Hubs: ASP.NET]
- [Give brugerne besked med besked om Hubs: Mobile-tjenester]






<!-- Images. -->

[13]: ./media/notification-hubs-ios-send-localized-breaking-news/ios_localized1.png
[14]: ./media/notification-hubs-ios-send-localized-breaking-news/ios_localized2.png






<!-- URLs. -->
[How To: Service Bus Notification Hubs (iOS Apps)]: http://msdn.microsoft.com/library/jj927168.aspx
[Brug meddelelse Hubs til at sende seneste nyheder]: /manage/services/notification-hubs/breaking-news-ios
[Mobile Service]: /develop/mobile/tutorials/get-started
[Give brugerne besked med besked om Hubs: ASP.NET]: /manage/services/notification-hubs/notify-users-aspnet
[Give brugerne besked med besked om Hubs: Mobile-tjenester]: /manage/services/notification-hubs/notify-users
[Submit an app page]: http://go.microsoft.com/fwlink/p/?LinkID=266582
[My Applications]: http://go.microsoft.com/fwlink/p/?LinkId=262039
[Live SDK for Windows]: http://go.microsoft.com/fwlink/p/?LinkId=262253
[Get started with Mobile Services]: /develop/mobile/tutorials/get-started/#create-new-service
[Get started with data]: /develop/mobile/tutorials/get-started-with-data-ios
[Get started with authentication]: /develop/mobile/tutorials/get-started-with-users-ios
[Get started with push notifications]: /develop/mobile/tutorials/get-started-with-push-ios
[Push notifications to app users]: /develop/mobile/tutorials/push-notifications-to-users-ios
[Authorize users with scripts]: /develop/mobile/tutorials/authorize-users-in-scripts-ios
[JavaScript and HTML]: ../get-started-with-push-js.md

[Windows Developer Preview registration steps for Mobile Services]: ../mobile-services-windows-developer-preview-registration.md
[wns object]: http://go.microsoft.com/fwlink/p/?LinkId=260591
[Notification Hubs Guidance]: http://msdn.microsoft.com/library/jj927170.aspx
[Notification Hubs How-To for iOS]: http://msdn.microsoft.com/library/jj927168.aspx
