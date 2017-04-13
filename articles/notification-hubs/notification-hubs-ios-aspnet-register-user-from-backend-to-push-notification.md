<properties
    pageTitle="Registrere den aktuelle bruger pushmeddelelser ved hjælp af Web API | Microsoft Azure"
    description="Lær, hvordan du anmoder om opslagsnål meddelelse registrering i en iOS-app med Azure meddelelse Hubs, når registeration er udført af ASP.NET Web API."
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
    ms.date="06/29/2016"
    ms.author="yuaxu"/>

# <a name="register-the-current-user-for-push-notifications-by-using-aspnet"></a>Registrere den aktuelle bruger pushmeddelelser ved hjælp af ASP.NET

> [AZURE.SELECTOR]
- [iOS](notification-hubs-ios-aspnet-register-user-from-backend-to-push-notification.md)



##<a name="overview"></a>Oversigt

Dette emne beskrives, hvordan du anmoder om registreringen af push med Azure meddelelse Hubs, når registrering er udført af ASP.NET Web API. Dette emne udvider selvstudiet [Informer brugerne med besked om Hubs]. Du skal allerede har fuldført de nødvendige trin i dette selvstudium til at oprette godkendte mobile tjenesten. Du kan finde flere oplysninger om Informer brugerne scenarie, [Informer brugerne med besked om Hubs].

##<a name="update-your-app"></a>Opdatere din app  

1. Tilføj følgende komponenter i din MainStoryboard_iPhone.storyboard fra objektbiblioteket:

    + **Navn**: "Push til bruger med besked om Hubs"
    + **Navn**: "InstallationId"
    + **Navn**: "Brugere"
    + **Tekstfelt**: "Brugere"
    + **Navn**: "Adgangskode"
    + **Tekstfelt**: "Adgangskode"
    + **Knappen**: "Logon"

    På dette tidspunkt ser storyboardet ud som følger:

    ![][0]

2. I editoren assistent oprette udtag af switched kontrolelementerne og ringe til dem, forbinde tekstfelterne med Vis Controller (stedfortræder) og oprette en **handling** for knappen **logon** .

    ![][1]

    Filen BreakingNewsViewController.h bør nu indeholde følgende kode:

        @property (weak, nonatomic) IBOutlet UILabel *installationId;
        @property (weak, nonatomic) IBOutlet UITextField *User;
        @property (weak, nonatomic) IBOutlet UITextField *Password;

        - (IBAction)login:(id)sender;

5. Opret en klasse, kaldet **DeviceInfo**, og kopier den følgende kode i sektionen brugergrænseflade på filen DeviceInfo.h:

        @property (readonly, nonatomic) NSString* installationId;
        @property (nonatomic) NSData* deviceToken;

6. Kopier den følgende kode i sektionen implementering af filen DeviceInfo.m:

            @synthesize installationId = _installationId;

            - (id)init {
                if (!(self = [super init]))
                    return nil;

                NSUserDefaults *defaults = [NSUserDefaults standardUserDefaults];
                _installationId = [defaults stringForKey:@"PushToUserInstallationId"];
                if(!_installationId) {
                    CFUUIDRef newUUID = CFUUIDCreate(kCFAllocatorDefault);
                    _installationId = (__bridge_transfer NSString *)CFUUIDCreateString(kCFAllocatorDefault, newUUID);
                    CFRelease(newUUID);

                    //store the install ID so we don't generate a new one next time
                    [defaults setObject:_installationId forKey:@"PushToUserInstallationId"];
                    [defaults synchronize];
                }

                return self;
            }

            - (NSString*)getDeviceTokenInHex {
                const unsigned *tokenBytes = [[self deviceToken] bytes];
                NSString *hexToken = [NSString stringWithFormat:@"%08X%08X%08X%08X%08X%08X%08X%08X",
                                      ntohl(tokenBytes[0]), ntohl(tokenBytes[1]), ntohl(tokenBytes[2]),
                                      ntohl(tokenBytes[3]), ntohl(tokenBytes[4]), ntohl(tokenBytes[5]),
                                      ntohl(tokenBytes[6]), ntohl(tokenBytes[7])];
                return hexToken;
            }

7. Tilføj de følgende separat egenskab i PushToUserAppDelegate.h:

        @property (strong, nonatomic) DeviceInfo* deviceInfo;

8. Tilføj følgende kode i metoden **didFinishLaunchingWithOptions** i PushToUserAppDelegate.m:

        self.deviceInfo = [[DeviceInfo alloc] init];

        [[UIApplication sharedApplication] registerForRemoteNotificationTypes: UIRemoteNotificationTypeAlert | UIRemoteNotificationTypeBadge | UIRemoteNotificationTypeSound];

    Den første linje starter **DeviceInfo** separat. Den anden linje starter registreringen for push meddelelser, som er allerede præsentere, er du allerede har fuldført selvstudiet [Komme i gang med besked om Hubs] .

9. Implementere metode **didRegisterForRemoteNotificationsWithDeviceToken** i din AppDelegate i PushToUserAppDelegate.m, og Føj følgende kode:

        self.deviceInfo.deviceToken = deviceToken;

    Dette angiver enhed token for din anmodning.

    > [AZURE.NOTE] På dette tidspunkt må der ikke være en hvilken som helst anden kode i denne metode. Hvis du allerede har et opkald til metoden **registerNativeWithDeviceToken** , der blev tilføjet, når du har fuldført selvstudiet [Komme i gang med besked om Hubs](/manage/services/notification-hubs/get-started-notification-hubs-ios/) , skal du kommentar fra eller fjerne opkaldet.

10. Tilføj følgende handler metode i filen PushToUserAppDelegate.m:

        - (void)application:(UIApplication *)application didReceiveRemoteNotification:(NSDictionary *)userInfo {
            NSLog(@"%@", userInfo);
            UIAlertView *alert = [[UIAlertView alloc] initWithTitle:@"Notification" message:
                                  [userInfo objectForKey:@"inAppMessage"] delegate:nil cancelButtonTitle:
                                  @"OK" otherButtonTitles:nil, nil];
            [alert show];
        }

     Denne metode viser en advarsel i Brugergrænsefladen, når din app modtager meddelelser, mens den kører.

9. Åbn filen PushToUserViewController.m, og returnere tastaturet i følgende implementering:

        - (BOOL)textFieldShouldReturn:(UITextField *)theTextField {
            if (theTextField == self.User || theTextField == self.Password) {
                [theTextField resignFirstResponder];
            }
            return YES;
        }

9. In metoden **viewDidLoad** i filen PushToUserViewController.m initialiseret etiketten offlineaktivering på følgende måde:

        DeviceInfo* deviceInfo = [(PushToUserAppDelegate*)[[UIApplication sharedApplication]delegate] deviceInfo];
        Self.installationId.text = deviceInfo.installationId;

10. Tilføj følgende egenskaber i brugergrænsefladen i PushToUserViewController.m:

        @property (readonly) NSOperationQueue* downloadQueue;
        - (NSString*)base64forData:(NSData*)theData;

11. Tilføj derefter følgende implementering:

            - (NSOperationQueue *)downloadQueue {
                if (!_downloadQueue) {
                    _downloadQueue = [[NSOperationQueue alloc] init];
                    _downloadQueue.name = @"Download Queue";
                    _downloadQueue.maxConcurrentOperationCount = 1;
                }
                return _downloadQueue;
            }

            // base64 encoding
            - (NSString*)base64forData:(NSData*)theData
            {
                const uint8_t* input = (const uint8_t*)[theData bytes];
                NSInteger length = [theData length];

                static char table[] = "ABCDEFGHIJKLMNOPQRSTUVWXYZabcdefghijklmnopqrstuvwxyz0123456789+/=";

                NSMutableData* data = [NSMutableData dataWithLength:((length + 2) / 3) * 4];
                uint8_t* output = (uint8_t*)data.mutableBytes;

                NSInteger i;
                for (i=0; i < length; i += 3) {
                    NSInteger value = 0;
                    NSInteger j;
                    for (j = i; j < (i + 3); j++) {
                        value <<= 8;

                        if (j < length) {
                            value |= (0xFF & input[j]);
                        }
                    }

                    NSInteger theIndex = (i / 3) * 4;
                    output[theIndex + 0] =                    table[(value >> 18) & 0x3F];
                    output[theIndex + 1] =                    table[(value >> 12) & 0x3F];
                    output[theIndex + 2] = (i + 1) < length ? table[(value >> 6)  & 0x3F] : '=';
                    output[theIndex + 3] = (i + 2) < length ? table[(value >> 0)  & 0x3F] : '=';
                }

                return [[NSString alloc] initWithData:data encoding:NSASCIIStringEncoding];
            }


12. Kopier den følgende kode i metoden **login** handler, der er oprettet af XCode:

            DeviceInfo* deviceInfo = [(PushToUserAppDelegate*)[[UIApplication sharedApplication]delegate] deviceInfo];

            // build JSON
            NSString* json = [NSString stringWithFormat:@"{\"platform\":\"ios\", \"instId\":\"%@\", \"deviceToken\":\"%@\"}", deviceInfo.installationId, [deviceInfo getDeviceTokenInHex]];

            // build auth string
            NSString* authString = [NSString stringWithFormat:@"%@:%@", self.User.text, self.Password.text];

            NSMutableURLRequest* request = [NSMutableURLRequest requestWithURL:[NSURL URLWithString:@"http://nhnotifyuser.azurewebsites.net/api/register"]];
            [request setHTTPMethod:@"POST"];
            [request setHTTPBody:[json dataUsingEncoding:NSUTF8StringEncoding]];
            [request addValue:[@([json lengthOfBytesUsingEncoding:NSUTF8StringEncoding]) description] forHTTPHeaderField:@"Content-Length"];
            [request addValue:@"application/json" forHTTPHeaderField:@"Content-Type"];
            [request addValue:[NSString stringWithFormat:@"Basic %@",[self base64forData:[authString dataUsingEncoding:NSUTF8StringEncoding]]] forHTTPHeaderField:@"Authorization"];

            // connect with POST
            [NSURLConnection sendAsynchronousRequest:request queue:[self downloadQueue] completionHandler:^(NSURLResponse* response, NSData* data, NSError* error) {
                // add UIAlert depending on response.
                if (error != nil) {
                    NSHTTPURLResponse* httpResponse = (NSHTTPURLResponse*)response;
                    if ([httpResponse statusCode] == 200) {
                        UIAlertView *alert = [[UIAlertView alloc] initWithTitle:@"Back-end registration" message:@"Registration successful" delegate:nil cancelButtonTitle: @"OK" otherButtonTitles:nil, nil];
                        [alert show];
                    } else {
                        NSLog(@"status: %ld", (long)[httpResponse statusCode]);
                    }
                } else {
                    NSLog(@"error: %@", error);
                }
            }];

    Denne metode henter både en installations-ID og kanal for pushmeddelelser og sender den, sammen med enhedstype, til metoden godkendte Web API, der opretter en registrering i meddelelse Hubs. Denne Web API er defineret i [Informer brugerne med besked om Hubs].

Nu, hvor appen klient er blevet opdateret, gå tilbage til [Informer brugerne med besked om Hubs] og opdatere den mobile service for at sende meddelelser ved hjælp af besked om Hubs.

<!-- Anchors. -->

<!-- Images. -->
[0]: ./media/notification-hubs-ios-aspnet-register-user-push-notifications/notification-hub-user-aspnet-ios1.png
[1]: ./media/notification-hubs-ios-aspnet-register-user-push-notifications/notification-hub-user-aspnet-ios2.png

<!-- URLs. -->
[Give brugerne besked med besked om Hubs]: /manage/services/notification-hubs/notify-users-aspnet

[Komme i gang med besked om Hubs]: /manage/services/notification-hubs/get-started-notification-hubs-ios
