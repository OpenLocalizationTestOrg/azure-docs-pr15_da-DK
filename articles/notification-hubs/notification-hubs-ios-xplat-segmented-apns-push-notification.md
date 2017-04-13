<properties
    pageTitle="Meddelelse om Hubs seneste nyheder selvstudium - iOS"
    description="Lær, hvordan du bruger Azure Service Bus meddelelse Hubs til at sende seneste nyheder meddelelser til iOS-enheder."
    services="notification-hubs"
    documentationCenter="ios"
    authors="ysxu"
    manager="erikre"
    editor=""/>

<tags
    ms.service="notification-hubs"
    ms.workload="mobile"
    ms.tgt_pltfrm="mobile-ios"
    ms.devlang="objective-c"
    ms.topic="article"
    ms.date="06/29/2016"
    ms.author="yuaxu"/>

# <a name="use-notification-hubs-to-send-breaking-news"></a>Brug meddelelse Hubs til at sende seneste nyheder

[AZURE.INCLUDE [notification-hubs-selector-breaking-news](../../includes/notification-hubs-selector-breaking-news.md)]


##<a name="overview"></a>Oversigt

Dette emne beskrives, hvordan du bruger Azure meddelelse Hubs til at udsende seneste nyheder meddelelser til en iOS-app. Når du er færdig, skal du kunne tilmelde Bryd nyhedskategorier, du er interesseret i, og modtage kun pushmeddelelser for disse kategorier. Dette scenario er et almindeligt mønster for mange apps, hvor meddelelser skal sendes til de grupper af brugere, der har tidligere er erklæret interesse i dem, fx RSS-læseren, apps til musikelskere osv.

Broadcast scenarier er aktiveret ved at medtage en eller flere _mærker_ , når du opretter en registrering i den meddelelse hub. Når meddelelser er sendt til et mærke, får alle enheder, der har registreret til mærket meddelelsen. Fordi mærker er blot strenge, har de ikke klargøres på forhånd. Se [meddelelse Hubs Routing og mærke udtryk](notification-hubs-tags-segment-push-message.md)kan finde flere oplysninger om mærker.


##<a name="prerequisites"></a>Forudsætninger

Dette emne, der er baseret på den app, du oprettede i [komme i gang med besked om Hubs][get-started]. Før du starter selvstudiet, du skal allerede har udført [komme i gang med besked om Hubs][get-started].

##<a name="add-category-selection-to-the-app"></a>Tilføje valg af kategori på appen

Det første trin er at tilføje elementer på Brugergrænsefladen til din eksisterende storyboard, der gør det muligt at vælge kategorier til at registrere. De kategorier, der er valgt som en bruger er gemt på enheden. Når programmet starter, oprettes en enhed registrering i din meddelelse hubben med de valgte kategorier som mærker.

1. Tilføj følgende komponenter i din MainStoryboard_iPhone.storyboard fra objektbiblioteket:
    + Et navn med "Bryd Nyheder" tekst
    + Etiketter med kategoritekster "Verden", "Politik", "Business", "Teknologi", "Videnskab", "Sports"
    + Seks parametre, en hver kategori, angive hver switch **tilstand** skal være **fra** som standard.
    + En knap med navnet "Abonner"

    Storyboardet skal se ud som følger:

    ![][3]

2. I editoren assistent Opret udtag for alle parametre, og kald dem "WorldSwitch", "PoliticsSwitch", "BusinessSwitch", "TechnologySwitch", "ScienceSwitch", "SportsSwitch"


3. Oprette en handling for knappen kaldet "abonnere". Din ViewController.h skal indeholde følgende:

        @property (weak, nonatomic) IBOutlet UISwitch *WorldSwitch;
        @property (weak, nonatomic) IBOutlet UISwitch *PoliticsSwitch;
        @property (weak, nonatomic) IBOutlet UISwitch *BusinessSwitch;
        @property (weak, nonatomic) IBOutlet UISwitch *TechnologySwitch;
        @property (weak, nonatomic) IBOutlet UISwitch *ScienceSwitch;
        @property (weak, nonatomic) IBOutlet UISwitch *SportsSwitch;

        - (IBAction)subscribe:(id)sender;

4. Oprette en ny **Kakao trykke klasse** kaldet `Notifications`. Kopier den følgende kode i sektionen brugergrænseflade på filen Notifications.h:

        @property NSData* deviceToken;

        - (id)initWithConnectionString:(NSString*)listenConnectionString HubName:(NSString*)hubName;

        - (void)storeCategoriesAndSubscribeWithCategories:(NSArray*)categories
                    completion:(void (^)(NSError* error))completion;

        - (NSSet*)retrieveCategories;

        - (void)subscribeWithCategories:(NSSet*)categories completion:(void (^)(NSError *))completion;

5. Tilføje følgende Importér direktivet på Notifications.m:

        #import <WindowsAzureMessaging/WindowsAzureMessaging.h>

6. Kopier den følgende kode i sektionen implementering af filen Notifications.m.

        SBNotificationHub* hub;

        - (id)initWithConnectionString:(NSString*)listenConnectionString HubName:(NSString*)hubName{

            hub = [[SBNotificationHub alloc] initWithConnectionString:listenConnectionString
                                        notificationHubPath:hubName];

            return self;
        }

        - (void)storeCategoriesAndSubscribeWithCategories:(NSSet *)categories completion:(void (^)(NSError *))completion {
            NSUserDefaults* defaults = [NSUserDefaults standardUserDefaults];
            [defaults setValue:[categories allObjects] forKey:@"BreakingNewsCategories"];

            [self subscribeWithCategories:categories completion:completion];
        }


        - (NSSet*)retrieveCategories {
            NSUserDefaults* defaults = [NSUserDefaults standardUserDefaults];

            NSArray* categories = [defaults stringArrayForKey:@"BreakingNewsCategories"];

            if (!categories) return [[NSSet alloc] init];
            return [[NSSet alloc] initWithArray:categories];
        }


        - (void)subscribeWithCategories:(NSSet *)categories completion:(void (^)(NSError *))completion
        {
           //[hub registerNativeWithDeviceToken:self.deviceToken tags:categories completion: completion];

            NSString* templateBodyAPNS = @"{\"aps\":{\"alert\":\"$(messageParam)\"}}";

            [hub registerTemplateWithDeviceToken:self.deviceToken name:@"simpleAPNSTemplate" 
                jsonBodyTemplate:templateBodyAPNS expiryTemplate:@"0" tags:categories completion:completion];
        }



    Denne klasse bruger lokale lager til at lagre og hente de forskellige kategorier af nyheder, der skal modtage denne enhed. Den indeholder også en metode til at tilmelde dig disse kategorier ved hjælp af en [skabelon](notification-hubs-templates-cross-platform-push-messages.md) registrering.

7. Tilføje en import-sætning for Notifications.h i filen AppDelegate.h og tilføje en egenskab for en forekomst af klassen meddelelser:

        #import "Notifications.h"

        @property (nonatomic) Notifications* notifications;
    

8. Tilføj koden, initialiseret forekomsten beskeder i starten af metoden i metoden **didFinishLaunchingWithOptions** i AppDelegate.m.  
 
    `HUBNAME`og `HUBLISTENACCESS` (defineret i hubinfo.h) skal allerede være den `<hub name>` og `<connection string with listen access>` pladsholdere erstattet med navnet på din meddelelse hub og forbindelsesstrengen for *DefaultListenSharedAccessSignature* , som du hentede tidligere

        self.notifications = [[Notifications alloc] initWithConnectionString:HUBLISTENACCESS HubName:HUBNAME];

    > [AZURE.NOTE] Da legitimationsoplysninger, som er delt med en klient app ikke generelt sikker, skal du kun fordele tasten for at lytte få adgang til din klient-app. Lyt adgang gør det muligt for din app til at registrere for beskeder, men eksisterende registreringer ikke kan ændres, og meddelelser kan blive sendt. Tasten fuld adgang bruges i en sikret back end-tjeneste til at sende meddelelser og ændre eksisterende registreringer.


9. Erstat koden i metoden i metoden **didRegisterForRemoteNotificationsWithDeviceToken** i AppDelegate.m med følgende kode til at overføre enhed tokenet til klassen beskeder. Klassen beskeder udfører registrering til meddelelser med de forskellige kategorier. Hvis brugeren ændrer valg af kategorier, vi kalder den `subscribeWithCategories` metode som svar på **Abonner** -knap til at opdatere dem.

    > [AZURE.NOTE] Fordi kan risiko for enhed tokenet tildelt ved den Apple Push meddelelse Service (APNS), når som helst, skal du registrere til meddelelser ofte for at undgå besked om fejl. I dette eksempel registrerer til besked om hver gang appen startes. Til apps, der udføres ofte, kan mere end en gang om dagen, du sandsynligvis springe registrering Hvis du vil bevare båndbredde mindre end en dag er gået siden forrige registrering.

        self.notifications.deviceToken = deviceToken;

        // Retrieves the categories from local storage and requests a registration for these categories
        // each time the app starts and performs a registration.

        NSSet* categories = [self.notifications retrieveCategories];
        [self.notifications subscribeWithCategories:categories completion:^(NSError* error) {
            if (error != nil) {
                NSLog(@"Error registering for notifications: %@", error);
            }
        }];


    Bemærk, at på dette tidspunkt der skal være ingen anden kode i metoden **didRegisterForRemoteNotificationsWithDeviceToken** .

10. De følgende metoder skal allerede være til stede i AppDelegate.m i at blive fuldført på [Kom i gang med besked om Hubs] [ get-started] selvstudium.  Hvis ikke, skal du tilføje dem.

        -(void)MessageBox:(NSString *)title message:(NSString *)messageText
        {
            UIAlertView *alert = [[UIAlertView alloc] initWithTitle:title message:messageText delegate:self
                cancelButtonTitle:@"OK" otherButtonTitles: nil];
            [alert show];
        }

        - (void)application:(UIApplication *)application didReceiveRemoteNotification:
            (NSDictionary *)userInfo {
            NSLog(@"%@", userInfo);
            [self MessageBox:@"Notification" message:[[userInfo objectForKey:@"aps"] valueForKey:@"alert"]];
        }

    Denne metode håndterer meddelelser, der er modtaget når app'en kører ved at vise en enkelt **UIAlert**.

11. Tilføje en import-sætning for AppDelegate.h i ViewController.m, og kopier den følgende kode i den oprettede XCode **abonnere** metode. Denne kode opdateres meddelelse registrering for at bruge de nye kategori mærker, som brugeren har valgt i brugergrænsefladen.

        ```
        #import "Notifications.h"
        ```

        NSMutableArray* categories = [[NSMutableArray alloc] init];

        if (self.WorldSwitch.isOn) [categories addObject:@"World"];
        if (self.PoliticsSwitch.isOn) [categories addObject:@"Politics"];
        if (self.BusinessSwitch.isOn) [categories addObject:@"Business"];
        if (self.TechnologySwitch.isOn) [categories addObject:@"Technology"];
        if (self.ScienceSwitch.isOn) [categories addObject:@"Science"];
        if (self.SportsSwitch.isOn) [categories addObject:@"Sports"];

        Notifications* notifications = [(AppDelegate*)[[UIApplication sharedApplication]delegate] notifications];

        [notifications storeCategoriesAndSubscribeWithCategories:categories completion: ^(NSError* error) {
            if (!error) {
                [(AppDelegate*)[[UIApplication sharedApplication]delegate] MessageBox:@"Notification" message:@"Subscribed!"];
            } else {
                NSLog(@"Error subscribing: %@", error);
            }
        }];

    Denne metode opretter en **NSMutableArray** af kategorier og bruger klassen **beskeder** til at gemme listen i det lokale lager og registrerer de tilsvarende mærker med din anmeldelse hub. Når kategorier ændres, genskabes registreringen med de nye kategorier.

12. Tilføj følgende kode i **viewDidLoad** metoden til at angive den brugergrænseflade, der er baseret på de tidligere gemt kategorier i ViewController.m.


        // This updates the UI on startup based on the status of previously saved categories.

        Notifications* notifications = [(AppDelegate*)[[UIApplication sharedApplication]delegate] notifications];

        NSSet* categories = [notifications retrieveCategories];

        if ([categories containsObject:@"World"]) self.WorldSwitch.on = true;
        if ([categories containsObject:@"Politics"]) self.PoliticsSwitch.on = true;
        if ([categories containsObject:@"Business"]) self.BusinessSwitch.on = true;
        if ([categories containsObject:@"Technology"]) self.TechnologySwitch.on = true;
        if ([categories containsObject:@"Science"]) self.ScienceSwitch.on = true;
        if ([categories containsObject:@"Sports"]) self.SportsSwitch.on = true;



Appen kan nu gemme et sæt kategorier i det lokale enhed-lager, der bruges til at registrere med hubben besked, når programmet starter.  Brugeren kan ændre markeringen af kategorier på kørselstidspunktet, og klik på den **abonnere** metode for at opdatere registreringen for enheden. Derefter skal vil du opdatere appen for at sende de seneste nyheder meddelelser direkte i selve appen.


##<a name="optional-sending-tagged-notifications"></a>(valgfrit) Sende mærket beskeder

Hvis du ikke har adgang til Visual Studio, kan du gå videre til næste afsnit og sende meddelelser fra selve appen. Du kan også sende meddelelsen til stort skabelon fra [Azure klassisk Portal] ved hjælp af fanen fejlfinding til din meddelelse hub. 

[AZURE.INCLUDE [notification-hubs-send-categories-template](../../includes/notification-hubs-send-categories-template.md)]


##<a name="optional-send-notifications-from-the-device"></a>(valgfrit) Sende beskeder fra enheden

Normalt meddelelser vil blive sendt af en back end-tjeneste, men du kan sende seneste nyheder beskeder direkte fra app. Du gør dette opdaterer vi den `SendNotificationRESTAPI` afskrivningsmetode, som vi definerede i [komme i gang med besked om Hubs] [ get-started] selvstudium.


1. I ViewController.m update den `SendNotificationRESTAPI` metode som følger, så den accepterer en parameter for mærket kategori og sender meddelelsen til stort [skabelon](notification-hubs-templates-cross-platform-push-messages.md) .

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
            json = [NSString stringWithFormat:@"{\"messageParam\":\"Breaking %@ News : %@\"}",
                    categoryTag, self.notificationMessage.text];

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



2. Opdater handlingen **Send besked** i ViewController.m som vist i den kode, der følger efter. Så den kan sende meddelelserne ved hjælp af hvert mærke enkeltvis og sende til flere platforme.



        - (IBAction)SendNotificationMessage:(id)sender
        {
            self.sendResults.text = @"";

            NSArray* categories = [NSArray arrayWithObjects: @"World", @"Politics", @"Business",
                                    @"Technology", @"Science", @"Sports", nil];

            // Lets send the message as breaking news for each category to WNS, GCM, and APNS
            // using a template.
            for(NSString* category in categories)
            {
                [self SendNotificationRESTAPI:category];
            }
        }



3. Genopbygge projektet, og Sørg for, at du har nogen buildfejl.


##<a name="run-the-app-and-generate-notifications"></a>Køre appen og generere beskeder

1. Tryk på knappen Kør til at opbygge projektet og starte appen. Vælg nogle seneste nyheder indstillinger til at abonnere på og derefter trykke på knappen **Abonner** . Der vises en dialogboks, der angiver meddelelserne abonnerer på.

    ![][1]

    Når du vælger **Abonner**, appen konverterer de valgte kategorier til mærker og anmoder om en anden enhed registrering for de valgte koder fra meddelelse-hubben.

2. Skriv en meddelelse sendes som seneste nyheder og derefter trykke på knappen **Send meddelelse** . Du kan også køre .NET console app for at generere beskeder.

    ![][2]


3. Hver enhed, der er oprettet et abonnement på varmeste nyheder, modtager de seneste nyheder meddelelser, du lige har sendt.



## <a name="next-steps"></a>Næste trin

I dette selvstudium lært vi at transmittere seneste nyheder efter kategori. Overvej fuldfører en af følgende selvstudier, der fremhæver andre avancerede meddelelse Hubs scenarier:

+ **[Brug meddelelse Hubs transmittere oversatte seneste nyheder]**

    Lær at udvide den seneste nyheder app for at aktivere afsendelse oversatte meddelelser.





<!-- Images. -->
[1]: ./media/notification-hubs-ios-send-breaking-news/notification-hub-breakingnews-subscribed.png
[2]: ./media/notification-hubs-ios-send-breaking-news/notification-hub-breakingnews-ios1.png
[3]: ./media/notification-hubs-ios-send-breaking-news/notification-hub-breakingnews-ios2.png








<!-- URLs. -->
[How To: Service Bus Notification Hubs (iOS Apps)]: http://msdn.microsoft.com/library/jj927168.aspx
[Brug meddelelse Hubs transmittere oversatte seneste nyheder]: notification-hubs-ios-xplat-localized-apns-push-notification.md
[Mobile Service]: /develop/mobile/tutorials/get-started
[Notify users with Notification Hubs]: notification-hubs-aspnet-backend-ios-notify-users.md
[Notification Hubs Guidance]: http://msdn.microsoft.com/library/dn530749.aspx
[Notification Hubs How-To for iOS]: http://msdn.microsoft.com/library/jj927168.aspx
[get-started]: /manage/services/notification-hubs/get-started-notification-hubs-ios/
[Azure klassisk Portal]: https://manage.windowsazure.com
