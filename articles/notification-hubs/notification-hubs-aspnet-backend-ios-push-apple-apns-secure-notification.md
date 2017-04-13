<properties
    pageTitle="Azure meddelelse Hubs sikker Push"
    description="Lær at sende sikker pushmeddelelser til en iOS-app fra Azure. Kodeeksempler, der er skrevet i mål-C og C#."
    documentationCenter="ios"
    authors="ysxu"
    manager="erikre"
    editor=""
    services="notification-hubs"/>

<tags
    ms.service="notification-hubs"
    ms.workload="mobile"
    ms.tgt_pltfrm="ios"
    ms.devlang="objective-c"
    ms.topic="article"
    ms.date="06/29/2016"
    ms.author="yuaxu"/>

#<a name="azure-notification-hubs-secure-push"></a>Azure meddelelse Hubs sikker Push

> [AZURE.SELECTOR]
- [Windows Universal](notification-hubs-aspnet-backend-windows-dotnet-wns-secure-push-notification.md)
- [iOS](notification-hubs-aspnet-backend-ios-push-apple-apns-secure-notification.md)
- [Android](notification-hubs-aspnet-backend-android-secure-google-gcm-push-notification.md)


##<a name="overview"></a>Oversigt

Understøttelse af push meddelelse i Microsoft Azure gør muligt at få adgang til en nemt Hvis du vil bruge, flere platforme, skaleret push-infrastruktur, hvilket gør det meget forenkler implementeringen af pushmeddelelser for både forbruger- og enterprise-programmer til mobile platforme.

På grund af lovmæssige eller sikkerhedsbegrænsninger, nogle gange et program kan du medtage noget i meddelelsen, som ikke kan overføres via standard opslagsnål meddelelse infrastruktur. Dette selvstudium beskrives, hvordan du opnå den samme oplevelse ved at sende følsomme oplysninger via en sikker, godkendt forbindelse mellem klientenhed og app back end.

På et højt niveau som strømmen på følgende måde:

1. App-back-end:
    - Gemmer sikker data i back end-databasen.
    - Sender ID'ET for denne meddelelse på enheden (der sendes ingen sikker oplysninger).
2. Appen på enheden, når du modtager meddelelsen:
    - Enheden kontakter den back end-anmoder om sikker data.
    - Appen kan vise data som en meddelelse på enheden.

Det er vigtigt at være opmærksom på, i det foregående flow (og i dette selvstudium), antager vi, at enheden gemmer et godkendelse token i lokale lager, når brugeren logger på. Dette garanterer en helt problemfri oplevelse, som enheden kan hente på beskeden sikker data ved hjælp af dette symbol. Hvis dit program ikke gemme godkendelsestokens på enheden, eller hvis disse tokens kan være udløbet, appen enhed ved modtagelse af meddelelsen skal vise en generisk meddelelse spørge brugeren til at starte appen. Appen derefter godkender brugeren og viser besked om overførslen.

Selvstudiet Secure opslagsnål viser, hvordan du sende en meddelelse om opslagsnål sikkert. Selvstudiet bygger på selvstudium [Giv brugerne besked](notification-hubs-aspnet-backend-ios-apple-apns-notification.md) , så du skal fuldføre trinnene i dette selvstudium først.

> [AZURE.NOTE] Dette selvstudium antages det, at du har oprettet og konfigureret din meddelelse-hub, som beskrevet i [Komme i gang med besked om Hubs (iOS)](notification-hubs-ios-apple-push-notification-apns-get-started.md).

[AZURE.INCLUDE [notification-hubs-aspnet-backend-securepush](../../includes/notification-hubs-aspnet-backend-securepush.md)]

## <a name="modify-the-ios-project"></a>Redigere iOS-projekt

Nu hvor du har ændret din app back-end for at sende lige *id* for en meddelelse, er det nødvendigt at ændre din iOS-app for at håndtere denne meddelelse og ringe tilbage til din back-end for at hente den sikre meddelelse skal vises.

Vi har til at skrive logik for at hente det sikre indhold fra app back-end for at opnå dette mål.

1. Sørg for app registre til uovervåget meddelelser, så det behandler id'et meddelelse sendes fra backend-version i **AppDelegate.m**. Tilføje indstillingen **UIRemoteNotificationTypeNewsstandContentAvailability** i didFinishLaunchingWithOptions:

        [[UIApplication sharedApplication] registerForRemoteNotificationTypes: UIRemoteNotificationTypeAlert | UIRemoteNotificationTypeBadge | UIRemoteNotificationTypeSound | UIRemoteNotificationTypeNewsstandContentAvailability];

2. Tilføj en implementering sektion i din **AppDelegate.m** øverst med følgende erklæring:

        @interface AppDelegate ()
        - (void) retrieveSecurePayloadWithId:(int)payloadId completion: (void(^)(NSString*, NSError*)) completion;
        @end

3. Tilføj derefter i sektionen implementering følgende kode, erstatte pladsholderen `{back-end endpoint}` med slutpunkt for din back end-opnået tidligere:

```
        NSString *const GetNotificationEndpoint = @"{back-end endpoint}/api/notifications";

        - (void) retrieveSecurePayloadWithId:(int)payloadId completion: (void(^)(NSString*, NSError*)) completion;
        {
            // check if authenticated
            ANHViewController* rvc = (ANHViewController*) self.window.rootViewController;
            NSString* authenticationHeader = rvc.registerClient.authenticationHeader;
            if (!authenticationHeader) return;


            NSURLSession* session = [NSURLSession
                                     sessionWithConfiguration:[NSURLSessionConfiguration defaultSessionConfiguration]
                                     delegate:nil
                                     delegateQueue:nil];


            NSURL* requestURL = [NSURL URLWithString:[NSString stringWithFormat:@"%@/%d", GetNotificationEndpoint, payloadId]];
            NSMutableURLRequest* request = [NSMutableURLRequest requestWithURL:requestURL];
            [request setHTTPMethod:@"GET"];
            NSString* authorizationHeaderValue = [NSString stringWithFormat:@"Basic %@", authenticationHeader];
            [request setValue:authorizationHeaderValue forHTTPHeaderField:@"Authorization"];

            NSURLSessionDataTask* dataTask = [session dataTaskWithRequest:request completionHandler:^(NSData *data, NSURLResponse *response, NSError *error) {
                NSHTTPURLResponse* httpResponse = (NSHTTPURLResponse*) response;
                if (!error && httpResponse.statusCode == 200)
                {
                    NSLog(@"Received secure payload: %@", [[NSString alloc] initWithData:data encoding:NSUTF8StringEncoding]);

                    NSMutableDictionary *json = [NSJSONSerialization JSONObjectWithData:data options: NSJSONReadingMutableContainers error: &error];

                    completion([json objectForKey:@"Payload"], nil);
                }
                else
                {
                    NSLog(@"Error status: %ld, request: %@", (long)httpResponse.statusCode, error);
                    if (error)
                        completion(nil, error);
                    else {
                        completion(nil, [NSError errorWithDomain:@"APICall" code:httpResponse.statusCode userInfo:nil]);
                    }
                }
            }];
            [dataTask resume];
        }
```

    This method calls your app back-end to retrieve the notification content using the credentials stored in the shared preferences.

4. Nu har vi at håndtere indgående meddelelser og bruge metoden herover til at hente indholdet, der skal vises. Først skal skal vi aktivere din iOS-app til at køre i baggrunden, når du modtager en meddelelse om opslagsnål. Vælg projektet app på panelet til venstre i **XCode**, og derefter klikker på din primære app destination i sektionen **destinationer** i den midterste rude.

5. Klik på din **Funktioner** under fanen øverst i den midterste rude, og Markér afkrydsningsfeltet **Remote beskeder** .

    ![][IOS1]


6. Tilføj følgende metode til håndtering af pushmeddelelser i **AppDelegate.m** :

        -(void)application:(UIApplication *)application didReceiveRemoteNotification:(NSDictionary *)userInfo fetchCompletionHandler:(void (^)(UIBackgroundFetchResult))completionHandler
        {
            NSLog(@"%@", userInfo);

            [self retrieveSecurePayloadWithId:[[userInfo objectForKey:@"secureId"] intValue] completion:^(NSString * payload, NSError *error) {
                if (!error) {
                    // show local notification
                    UILocalNotification* localNotification = [[UILocalNotification alloc] init];
                    localNotification.fireDate = [NSDate dateWithTimeIntervalSinceNow:0];
                    localNotification.alertBody = payload;
                    localNotification.timeZone = [NSTimeZone defaultTimeZone];
                    [[UIApplication sharedApplication] scheduleLocalNotification:localNotification];

                    completionHandler(UIBackgroundFetchResultNewData);
                } else {
                    completionHandler(UIBackgroundFetchResultFailed);
                }
            }];

        }

    Bemærk, at det er bedst at håndtere tilfælde af manglende sidehoved egenskaben til godkendelse eller afvisning af back end. Den specifikke håndtering af disse tilfælde afhænger hovedsageligt brugeroplevelsen dine mål. En af mulighederne er at få vist en meddelelse med en meddelelse om generisk til at godkende brugeren til at hente den faktiske meddelelse.

## <a name="run-the-application"></a>Køre programmet

Hvis du vil køre programmet, skal du gøre følgende:

1. Køre appen på en fysisk iOS-enhed (push-beskeder ikke fungerer i simulator) i XCode.

2. Angiv et brugernavn og din adgangskode i appen iOS brugergrænseflade. Det kan være en streng, men de skal være den samme værdi.

3. Klik på **Log på**i iOS-appen brugergrænseflade. Klik derefter på **Send opslagsnål**. Du bør se sikker meddelelsen blive vist i din meddelelse center.

[IOS1]: ./media/notification-hubs-aspnet-backend-ios-secure-push/secure-push-ios-1.png
