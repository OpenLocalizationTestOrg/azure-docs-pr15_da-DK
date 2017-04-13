<properties
    pageTitle="Azure meddelelse Hubs RTF opslagsnål"
    description="Lær at sende omfattende pushmeddelelser til en iOS-app fra Azure. Kodeeksempler, der er skrevet i mål-C og C#."
    documentationCenter="ios"
    services="notification-hubs"
    authors="ysxu"
    manager="erikre"
    editor=""/>

<tags
    ms.service="notification-hubs"
    ms.workload="mobile"
    ms.tgt_pltfrm="ios"
    ms.devlang="objective-c"
    ms.topic="article"
    ms.date="06/29/2016"
    ms.author="yuaxu"/>

#<a name="azure-notification-hubs-rich-push"></a>Azure meddelelse Hubs RTF opslagsnål


##<a name="overview"></a>Oversigt

For at involvere brugerne med Chat omfattende indhold, et program måske push mere end almindelig tekst. Disse meddelelser hæve bruger interaktioner og præsentere indhold som URL-adresser, lyde, billeder/kuponer og meget mere. Dette selvstudium bygger på emnet [Giv brugerne besked](notification-hubs-aspnet-backend-ios-apple-apns-notification.md) , og viser, hvordan du kan sende pushmeddelelser, der benytter overførsler (for eksempel billede).


Dette selvstudium er kompatibelt med iOS 7 og 8.

  ![][IOS1]

På et højt niveau:

1. App backend-version:
    - Gemmer RTF-data (i dette tilfælde billede) i back end-database og lokale lager
    - Sender ID for denne omfattende meddelelse til enheden
2. App på enheden:
    - Kontakter backend-version, anmoder om de omfattende data med-ID'ET, der modtages
    - Sender meddelelser om brugere på enheden, når hentning af data er fuldført, og viser data, med det samme, når brugere trykke for at få mere at vide


## <a name="webapi-project"></a>WebAPI Project

1. Åbn det **AppBackend** projekt, du oprettede i selvstudiet [Giv brugerne besked](notification-hubs-aspnet-backend-ios-apple-apns-notification.md) i Visual Studio.
2. Få et billede, du gerne vil give brugerne besked med, og Placer det i en **img** mappe i project-biblioteket.
3. Klik på **Vis alle filer** i Solution Explorer, og højreklik på mappen for at **Medtage i Project**.
4. Med et billede, der er markeret, kan du ændre dens opbygge handling i vinduet Egenskaber for **Integreret**ressource.

    ![][IOS2]

5. Tilføj følgende i **Notifications.cs**, ved hjælp af sætning:

        using System.Reflection;

6. Opdater klassen hele **meddelelser** med følgende kode. Sørg for at erstatte pladsholderne med din anmeldelse hub legitimationsoplysninger og navn på billedfil.

        public class Notification {
            public int Id { get; set; }
            // Initial notification message to display to users
            public string Message { get; set; }
            // Type of rich payload (developer-defined)
            public string RichType { get; set; }
            public string Payload { get; set; }
            public bool Read { get; set; }
        }

        public class Notifications {
            public static Notifications Instance = new Notifications();

            private List<Notification> notifications = new List<Notification>();

            public NotificationHubClient Hub { get; set; }

            private Notifications() {
                // Placeholders: replace with the connection string (with full access) for your notification hub and the hub name from the Azure Classics Portal
                Hub = NotificationHubClient.CreateClientFromConnectionString("{conn string with full access}",  "{hub name}");
            }

            public Notification CreateNotification(string message, string richType, string payload) {
                var notification = new Notification() {
                    Id = notifications.Count,
                    Message = message,
                    RichType = richType,
                    Payload = payload,
                    Read = false
                };

                notifications.Add(notification);

                return notification;
            }

            public Stream ReadImage(int id) {
                var assembly = Assembly.GetExecutingAssembly();
                // Placeholder: image file name (for example, logo.png).
                return assembly.GetManifestResourceStream("AppBackend.img.{logo.png}");
            }
        }

    >[AZURE.NOTE]  (valgfrit) Se [hvordan du kan integrere og få adgang til ressourcer ved hjælp af Visual C#](http://support.microsoft.com/kb/319292) yderligere oplysninger om hvordan du tilføjer og få projektressourcer.

7. Omdefinere **NotificationsController** med følgende kodestykker i **NotificationsController.cs**. Dette sender et indledende uovervåget omfattende meddelelse id til enhed og giver mulighed for klientsiden hentning af billede:

        // Return http response with image binary
        public HttpResponseMessage Get(int id) {
            var stream = Notifications.Instance.ReadImage(id);

            var result = new HttpResponseMessage(HttpStatusCode.OK);
            result.Content = new StreamContent(stream);
            // Switch in your image extension for "png"
            result.Content.Headers.ContentType = new System.Net.Http.Headers.MediaTypeHeaderValue("image/{png}");

            return result;
        }

        // Create rich notification and send initial silent notification (containing id) to client
        public async Task<HttpResponseMessage> Post() {
            // Replace the placeholder with image file name
            var richNotificationInTheBackend = Notifications.Instance.CreateNotification("Check this image out!", "img",  "{logo.png}");

            var usernameTag = "username:" + HttpContext.Current.User.Identity.Name;

            // Silent notification with content available
            var aboutUser = "{\"aps\": {\"content-available\": 1, \"sound\":\"\"}, \"richId\": \"" + richNotificationInTheBackend.Id.ToString() + "\",  \"richMessage\": \"" + richNotificationInTheBackend.Message + "\", \"richType\": \"" + richNotificationInTheBackend.RichType + "\"}";

            // Send notification to apns
            await Notifications.Instance.Hub.SendAppleNativeNotificationAsync(aboutUser, usernameTag);

            return Request.CreateResponse(HttpStatusCode.OK);
        }

8. Vi vil nu implementere denne app til et websted, Azure igen for at gøre det tilgængeligt fra alle enheder. Højreklik på **AppBackend** projektet, og vælg **Publicer**.

9. Vælg Azure websted som din Udgiv destination. Log på med din Azure-konto og vælge et eksisterende eller nye websted, og noter egenskaben **URL-adressen til destinationen** i fanen **forbindelse** . Vi vil henvise til denne URL-adresse som din *back end-slutpunkt* senere i dette selvstudium. Klik på **Udgiv**.

## <a name="modify-the-ios-project"></a>Redigere iOS-projekt

Nu, hvor du har ændret din app back-end for at sende lige *id* for en meddelelse, kan du ændre din iOS-app for at håndtere dette id og hente omfattende meddelelsen fra din backend-version.

1. Åbne projektet iOS, og Aktivér eksterne meddelelser ved at gå til din primære app destination i sektionen **destinationer** .

2. Klik på **Funktioner**, slå **Baggrund funktionsmåder**, og Markér afkrydsningsfeltet **Remote beskeder** .

    ![][IOS3]

3. Gå til **Main.storyboard**, og Sørg for, at du har en visning Controller (har henvist til som Home Vis Controller i dette selvstudium) fra [Informer bruger](notification-hubs-aspnet-backend-ios-apple-apns-notification.md) selvstudium.

4. Føje en **Navigation Controller** til storyboardet, og tryk på CTRL-træk til Home Vis Controller at gøre det **rod visning** af navigation. Sørg for, at den **Er indledende Vis Controller** attributter inspektion er markeret for den Navigation Controller.

5. Tilføje en **Visning Controller** for at storyboard og tilføje en **Billedvisning**. Dette er den side, brugerne får vist, når de vælge yderligere ved at klikke på notifiication. Storyboardet skal se ud som følger:

    ![][IOS4]

6. Klik på **Startside Vis Controller** i storyboardet, og Sørg for, at det har **homeViewController** som dens **Brugerdefineret klasse** og **Storyboardet ID** under identitet inspektion.

7. Gør det samme for billede af visningen Controller som **imageViewController**.

8. Derefter skal du oprette en ny visning Controller klasse med titlen **imageViewController** til at håndtere Brugergrænsefladen, du lige har oprettet.

9. Tilføj følgende den controller interface erklæringer i **imageViewController.h**. Sørg for at CTRL + træk fra billede storyboardvisningen til disse egenskaber sammenkæde to:

        @property (weak, nonatomic) IBOutlet UIImageView *myImage;
        @property (strong) UIImage* imagePayload;

10. Tilføj følgende i slutningen af **viewDidload**i **imageViewController.m**:

        // Display the UI Image in UI Image View
        [self.myImage setImage:self.imagePayload];

11. Importere den billede controller, du har oprettet i **AppDelegate.m**:

        #import "imageViewController.h"

12. Tilføj en grænseflade sektion med følgende erklæring:

        @interface AppDelegate ()

        @property UIImage* imagePayload;
        @property NSDictionary* userInfo;
        @property BOOL iOS8;

        // Obtain content from backend with notification id
        - (void)retrieveRichImageWithId:(int)richId completion: (void(^)(NSError*)) completion;

        // Redirect to Image View Controller after notification interaction
        - (void)redirectToImageViewWithImage: (UIImage *)img;

        @end

13. I **AppDelegate**, Sørg for, at din app registrerer til uovervåget meddelelser i **program: didFinishLaunchingWithOptions**:

        // Software version
        self.iOS8 = [[UIApplication sharedApplication] respondsToSelector:@selector(registerUserNotificationSettings:)] && [[UIApplication sharedApplication] respondsToSelector:@selector(registerForRemoteNotifications)];

        // Register for remote notifications for iOS8 and previous versions
        if (self.iOS8) {
            NSLog(@"This device is running with iOS8.");

            // Action
            UIMutableUserNotificationAction *richPushAction = [[UIMutableUserNotificationAction alloc] init];
            richPushAction.identifier = @"richPushMore";
            richPushAction.activationMode = UIUserNotificationActivationModeForeground;
            richPushAction.authenticationRequired = NO;
            richPushAction.title = @"More";

            // Notification category
            UIMutableUserNotificationCategory* richPushCategory = [[UIMutableUserNotificationCategory alloc] init];
            richPushCategory.identifier = @"richPush";
            [richPushCategory setActions:@[richPushAction] forContext:UIUserNotificationActionContextDefault];

            // Notification categories
            NSSet* richPushCategories = [NSSet setWithObjects:richPushCategory, nil];


            UIUserNotificationSettings *settings = [UIUserNotificationSettings settingsForTypes:UIUserNotificationTypeSound |
                                                    UIUserNotificationTypeAlert |
                                                    UIUserNotificationTypeBadge
                                                                                     categories:richPushCategories];

            [[UIApplication sharedApplication] registerUserNotificationSettings:settings];
            [[UIApplication sharedApplication] registerForRemoteNotifications];

        }
        else {
            // Previous iOS versions
            NSLog(@"This device is running with iOS7 or earlier versions.");

            [[UIApplication sharedApplication] registerForRemoteNotificationTypes: UIRemoteNotificationTypeAlert | UIRemoteNotificationTypeBadge | UIRemoteNotificationTypeSound | UIRemoteNotificationTypeNewsstandContentAvailability];
        }

        return YES;

14. Erstat i følgende implementering for **programmet: didRegisterForRemoteNotificationsWithDeviceToken** tage storyboardet Brugergrænsefladen ændres til konto:

        // Access navigation controller which is at the root of window
        UINavigationController *nc = (UINavigationController *)self.window.rootViewController;
        // Get home view controller from stack on navigation controller
        homeViewController *hvc = (homeViewController *)[nc.viewControllers objectAtIndex:0];
        hvc.deviceToken = deviceToken;

15. Tilføj derefter de følgende metoder til **AppDelegate.m** til at hente billedet fra din slutpunkt og sende en lokal besked, når hentning er fuldført. Sørg for at erstatte pladsholderen `{backend endpoint}` med din back end-slutpunkt:

        NSString *const GetNotificationEndpoint = @"{backend endpoint}/api/notifications";

        // Helper: retrieve notification content from backend with rich notification id
        - (void)retrieveRichImageWithId:(int)richId completion: (void(^)(NSError*)) completion {
            UINavigationController *nc = (UINavigationController *)self.window.rootViewController;
            homeViewController *hvc = (homeViewController *)[nc.viewControllers objectAtIndex:0];
            NSString* authenticationHeader = hvc.registerClient.authenticationHeader;
            // Check if authenticated
            if (!authenticationHeader) return;

            NSURLSession* session = [NSURLSession
                                     sessionWithConfiguration:[NSURLSessionConfiguration defaultSessionConfiguration]
                                     delegate:nil
                                     delegateQueue:nil];

            NSURL* requestURL = [NSURL URLWithString:[NSString stringWithFormat:@"%@/%d", GetNotificationEndpoint, richId]];
            NSMutableURLRequest* request = [NSMutableURLRequest requestWithURL:requestURL];
            [request setHTTPMethod:@"GET"];
            NSString* authorizationHeaderValue = [NSString stringWithFormat:@"Basic %@", authenticationHeader];
            [request setValue:authorizationHeaderValue forHTTPHeaderField:@"Authorization"];

            NSURLSessionDataTask* dataTask = [session dataTaskWithRequest:request completionHandler:^(NSData *data, NSURLResponse *response, NSError *error) {

                NSHTTPURLResponse* httpResponse = (NSHTTPURLResponse*) response;
                if (!error && httpResponse.statusCode == 200) {
                    // From NSData to UIImage
                    self.imagePayload = [UIImage imageWithData:data];

                    completion(nil);
                }
                else {
                    NSLog(@"Error status: %ld, request: %@", (long)httpResponse.statusCode, error);
                    if (error)
                        completion(error);
                    else {
                        completion([NSError errorWithDomain:@"APICall" code:httpResponse.statusCode userInfo:nil]);
                    }
                }
            }];
            [dataTask resume];
        }

        // Handle silent push notifications when id is sent from backend
        - (void)application:(UIApplication *)application didReceiveRemoteNotification:(NSDictionary *)userInfo fetchCompletionHandler:(void (^)(UIBackgroundFetchResult result))handler {
            self.userInfo = userInfo;
            int richId = [[self.userInfo objectForKey:@"richId"] intValue];
            NSString* richType = [self.userInfo objectForKey:@"richType"];

            // Retrieve image data
            if ([richType isEqualToString:@"img"]) {  
                [self retrieveRichImageWithId:richId completion:^(NSError* error) {
                    if (!error){
                        // Send local notification
                        UILocalNotification* localNotification = [[UILocalNotification alloc] init];

                        // "5" is arbitrary here to give you enough time to quit out of the app and receive push notifications
                        localNotification.fireDate = [NSDate dateWithTimeIntervalSinceNow:5];
                        localNotification.userInfo = self.userInfo;
                        localNotification.alertBody = [self.userInfo objectForKey:@"richMessage"];
                        localNotification.timeZone = [NSTimeZone defaultTimeZone];

                        // iOS8 categories
                        if (self.iOS8) {
                            localNotification.category = @"richPush";
                        }

                        [[UIApplication sharedApplication] scheduleLocalNotification:localNotification];

                        handler(UIBackgroundFetchResultNewData);
                    }
                    else{
                        handler(UIBackgroundFetchResultFailed);
                    }
                }];
            }
            // Add "else if" here to handle more types of rich content such as url, sound files, etc.
        }

16. Håndtere lokale meddelelsen over ved at åbne op billede Vis controller i **AppDelegate.m** med følgende metoder:

        // Helper: redirect users to image view controller
        - (void)redirectToImageViewWithImage: (UIImage *)img {
            UINavigationController *navigationController = (UINavigationController*) self.window.rootViewController;
            UIStoryboard *mainStoryboard = [UIStoryboard storyboardWithName:@"Main"
                                                                     bundle: nil];
            imageViewController *imgViewController = [mainStoryboard instantiateViewControllerWithIdentifier: @"imageViewController"];
            // Pass data/image to image view controller
            imgViewController.imagePayload = img;

            // Redirect
            [navigationController pushViewController:imgViewController animated:YES];
        }

        // Handle local notification sent above in didReceiveRemoteNotification
        - (void)application:(UIApplication *)application didReceiveLocalNotification:(UILocalNotification *)notification {
            if (application.applicationState == UIApplicationStateActive) {
                // Show in-app alert with an extra "more" button
                UIAlertView *alert = [[UIAlertView alloc] initWithTitle:@"Notification" message:notification.alertBody delegate:self cancelButtonTitle:@"OK" otherButtonTitles:@"More", nil];

                [alert show];
            }
            // App becomes active from user's tap on notification
            else {
                [self redirectToImageViewWithImage:self.imagePayload];
            }
        }

        // Handle buttons in in-app alerts and redirect with data/image
        - (void)alertView:(UIAlertView *)alertView clickedButtonAtIndex:(NSInteger)buttonIndex {
            // Handle "more" button
            if (buttonIndex == 1)
            {
                [self redirectToImageViewWithImage:self.imagePayload];
            }
            // Add "else if" here to handle more buttons
        }

        // Handle notification setting actions in iOS8
        - (void)application:(UIApplication *)application handleActionWithIdentifier:(NSString *)identifier forLocalNotification:(UILocalNotification *)notification completionHandler:(void (^)())completionHandler {
            // Handle richPush related buttons
            if ([identifier isEqualToString:@"richPushMore"]) {
                [self redirectToImageViewWithImage:self.imagePayload];
            }
            completionHandler();
        }

## <a name="run-the-application"></a>Køre programmet

1. Køre appen på en fysisk iOS-enhed (push-beskeder ikke fungerer i simulator) i XCode.

2. Angive et brugernavn og adgangskoden til den samme værdi for godkendelse i appen iOS brugergrænseflade, og klik på **Log In**.

3. Klik på **Send opslagsnål** og skal du se afsnittet en i-app besked. Hvis du klikker på **flere**, vil du blive bragt til det billede, du vælger at medtage i din app backend-version.

4. Du kan også klikke på **Send push** og med det samme trykke på knappen startside på din enhed. I et øjeblik modtager du en meddelelse om opslagsnål. Hvis du trykker på den eller klikke på mere, vil du blive bragt til din app og af RTF-billedindhold.


[IOS1]: ./media/notification-hubs-aspnet-backend-ios-rich-push/rich-push-ios-1.png
[IOS2]: ./media/notification-hubs-aspnet-backend-ios-rich-push/rich-push-ios-2.png
[IOS3]: ./media/notification-hubs-aspnet-backend-ios-rich-push/rich-push-ios-3.png
[IOS4]: ./media/notification-hubs-aspnet-backend-ios-rich-push/rich-push-ios-4.png
