<properties 
    pageTitle="Bygge bro mellem iOS webvisning med oprindelige Mobile aftale iOS SDK" 
    description="Beskriver, hvordan du opretter en bro mellem webvisning kører Javascript og den oprindelige Mobile aftale iOS SDK"      
    services="mobile-engagement" 
    documentationCenter="mobile" 
    authors="piyushjo" 
    manager="erikre" 
    editor="" />

<tags 
    ms.service="mobile-engagement" 
    ms.workload="mobile" 
    ms.tgt_pltfrm="mobile-ios" 
    ms.devlang="objective-c" 
    ms.topic="article" 
    ms.date="08/19/2016" 
    ms.author="piyushjo" />

#<a name="bridge-ios-webview-with-native-mobile-engagement-ios-sdk"></a>Bygge bro mellem iOS webvisning med oprindelige Mobile aftale iOS SDK

> [AZURE.SELECTOR]
- [Android bro](mobile-engagement-bridge-webview-native-android.md)
- [iOS bro](mobile-engagement-bridge-webview-native-ios.md)

Nogle mobilapps er designet som en hybrid-app, hvor selve appen er udviklet ved hjælp af indbyggede iOS mål-C udvikling, men nogle af eller endda alle skærmbillederne gengives i en iOS webvisning. Du kan stadig bruge Mobile aftale iOS SDK i disse apps, og dette selvstudium beskrives, hvordan du gør dette. 

Der er to måder at opnå dette, selvom begge er udokumenterede:

- Først et er beskrevet på dette [link](http://stackoverflow.com/questions/9826792/how-to-invoke-objective-c-method-from-javascript-and-send-back-data-to-javascrip) , der omfatter registrering af en `UIWebViewDelegate` på webvisning og produkter og-samme-annullering en placering ændring, der er udført i Javascript. 
- Andet er en baseret på denne [WWDC 2013 session](https://developer.apple.com/videos/play/wwdc2013/615), en metode, som er renere end den første linje, og som vi skal følge for denne vejledning. Bemærk, at denne metode fungerer kun på iOS7 og derover. 

Følg nedenstående trin, til iOS bygge bro mellem eksempel:

1. Først, skal du sikre dig, at du har gennemgået vores [Introduktion – selvstudium](mobile-engagement-ios-get-started.md) til at integrere Mobile aftale iOS SDK i din hybrid-app. Du kan eventuelt også aktivere test logføring på følgende måde, så du kan se metoderne SDK, som vi udløse dem fra webvisning. 
    
        - (BOOL)application:(UIApplication *)application didFinishLaunchingWithOptions:(NSDictionary *)launchOptions {
           ....
            [EngagementAgent setTestLogEnabled:YES];
           ....
        }

2. Nu Sørg for, at din hybrid app har en skærm med en webvisning på den. Du kan føje den til den `Main.storyboard` af appen. 

3. Knytte denne webvisning til din **ViewController** ved at klikke og trække webvisning fra visningen Controller scenen til den `ViewController.h` redigere skærmen, så den placeres lige nedenfor på `@interface` linje. 

4. Når du gør dette, åbnes en dialogboks beder om et navn. Angiv navnet som **webvisning**. Din `ViewController.h` fil skal se ud som følgende:

        #import <UIKit/UIKit.h>
        #import "EngagementViewController.h"
        
        @interface ViewController : EngagementViewController
        @property (strong, nonatomic) IBOutlet UIWebView *webView;
        
        @end

5. Vi opdaterer den `ViewController.m` filen senere, men først skal vi oprette bro filen som opretter en slikpapir over nogle ofte anvendte Mobile aftale iOS SDK metoder. Oprette en ny sidehoved fil kaldet **EngagementJsExports.h** som bruger den `JSExport` ordning, der er beskrevet i ovennævnte [session](https://developer.apple.com/videos/play/wwdc2013/615) til at vise de oprindelige iOS metoder. 

        #import <Foundation/Foundation.h>
        #import <JavaScriptCore/JavascriptCore.h>
        
        @protocol EngagementJsExports <JSExport>
        
        + (void) sendEngagementEvent:(NSString*) name :(NSString*)extras;
        + (void) startEngagementJob:(NSString*) name :(NSString*)extras;
        + (void) endEngagementJob:(NSString*) name;
        + (void) sendEngagementError:(NSString*) name :(NSString*)extras;
        + (void) sendEngagementAppInfo:(NSString*) appInfo;
        
        @end

        @interface EngagementJs : NSObject <EngagementJsExports>

        @end

6. Derefter skal du oprette den anden del af filen bro. Oprette en fil kaldet **EngagementJsExports.m** som skal indeholde implementeringen oprettelse af de faktiske indpakningsmaterialet ved at ringe til Mobile aftale iOS SDK metoder. Også Bemærk, at vi parsing af den `extras` der overføres fra webvisning javascript og lægge, til en `NSMutableDictionary` objekt, der skal overføres med aftale SDK metode opkald.  

        #import <UIKit/UIKit.h>
        #import "EngagementAgent.h"
        #import "EngagementJsExports.h"
        
        @implementation EngagementJs
        
        +(void) sendEngagementEvent:(NSString*)name :(NSString*)extras {
           NSMutableDictionary* extrasInput = [self ParseExtras:extras];
           [[EngagementAgent shared] sendEvent:name extras:extrasInput];
        }
        
        + (void) startEngagementJob:(NSString*) name :(NSString*)extras {
           NSMutableDictionary* extrasInput = [self ParseExtras:extras];
           [[EngagementAgent shared] startJob:name extras:extrasInput];
        }
        
        + (void) endEngagementJob:(NSString*) name {
           [[EngagementAgent shared] endJob:name];
        }
        
        + (void) sendEngagementError:(NSString*) name :(NSString*)extras {
           NSMutableDictionary* extrasInput = [self ParseExtras:extras];
           [[EngagementAgent shared] sendError:name extras:extrasInput];
        }
        
        + (void) sendEngagementAppInfo:(NSString*) appInfo {
           NSMutableDictionary* appInfoInput = [self ParseExtras:appInfo];
           [[EngagementAgent shared] sendAppInfo:appInfoInput];
        }
        
        + (NSMutableDictionary*) ParseExtras:(NSString*) input {
           NSData *data = [input dataUsingEncoding:NSUTF8StringEncoding];
           NSError* error = nil;
           NSMutableDictionary* extras = [NSJSONSerialization JSONObjectWithData:data options:0 error:&error];
           
           return extras;
        }
        
        @end

5. Nu vi vende tilbage til **ViewController.m** og opdatere den med følgende kode: 
        
        #import <JavaScriptCore/JavaScriptCore.h>
        #import "ViewController.h"
        #import "EngagementJsExports.h"
        
        @interface ViewController ()
        
        @end
        
        @implementation ViewController
        
        - (void)viewDidLoad
        {
           self.webView.delegate = self;
           [super viewDidLoad];
           [self loadWebView];
        }
        
        - (void)loadWebView {
           NSBundle* mainBundle = [NSBundle mainBundle];
           NSURL* htmlPage = [mainBundle URLForResource:@"LocalPage" withExtension:@"html"];
           
           NSURLRequest* urlReq = [NSURLRequest requestWithURL:htmlPage];
           [self.webView loadRequest:urlReq];
        }
        
        - (void)webViewDidFinishLoad:(UIWebView*)wv
        {
           JSContext* context = [wv valueForKeyPath:@"documentView.webView.mainFrame.javaScriptContext"];
           
           context[@"EngagementJs"] = [EngagementJs class];
        }
        
        - (void)webView:(UIWebView*)wv didFailLoadWithError:(NSError*)error
        {
           NSLog(@"Error for WEBVIEW: %@", [error description]);
        }
        
        - (void)didReceiveMemoryWarning {
           [super didReceiveMemoryWarning];
           // Dispose of any resources that can be recreated.
        }
        
        @end

6. Bemærk følgende punkter om filen **ViewController.m** :

    - I den `loadWebView` metode, vi indlæser en lokal HTML-fil med navnet **LocalPage.html** Hvis vi vil gennemgå næste kode. 
    - I den `webViewDidFinishLoad` metode, vi fanger den `JsContext` og knytte klasse vores slikpapir til den. Dette kan ringe til vores slikpapir SDK metoder ved hjælp af håndtaget **EngagementJs** fra webvisning. 

7. Oprette en fil med navnet **LocalPage.html** med følgende kode:

        <!doctype html>
        <html>
           <head>
               <style type='text/css'>
                   html { font-family:Helvetica; color:#222; }
                   h1 { color:steelblue; font-size:22px; margin-top:16px; }
                   h2 { color:grey; font-size:14px; margin-top:18px; margin-bottom:0px; }
               </style>
               
               <script type="text/javascript">
               
               window.onerror = function(err)
               {
                   alert('window.onerror: ' + err);
               }
               
               function send(inputId)
               {
                   var input = document.getElementById(inputId);
                   if(input)
                   {
                       var value = input.value;
                       // Example of how extras info can be passed with the Engagement logs
                       var extras = '{"CustomerId":"MS290011"}';
                   }
                   
                   if(value && value.length > 0)
                   {
                       switch(inputId)
                       {
                           case "event":
                           EngagementJs.sendEngagementEvent(value, extras);
                           break;
                           
                           case "job":
                           EngagementJs.startEngagementJob(value, extras);
                           window.setTimeout(
                           function(){
                               EngagementJs.endEngagementJob(value);
                           }, 10000 );
                           break;
        
                           case "error":
                           EngagementJs.sendEngagementError(value, extras);
                           break;
                           
                           case "appInfo":
                           var appInfo = '{"customer_name":"' + value + '"}';
                           EngagementJs.sendEngagementAppInfo(appInfo);
                           break;
                       }
                   }
               }
               </script>
               
           </head>
           <body>
               <h1>Bridge Tester</h1>
               
               <div id='engagement'>
                   
                   <br/>
                   <h2>Event</h2>
                   <input type="text" id="event" size="35">
                   <button onclick="send('event')">Send</button>
                   
                   <br/>
                   <h2>Job</h2>
                   <input type="text" id="job" size="35">
                   <button onclick="send('job')">Send</button>
                   
                   <br/>
                   <h2>Error</h2>
                   <input type="text" id="error" size="35">
                   <button onclick="send('error')">Send</button
                   
                   <br/>
                   <h2>AppInfo</h2>
                   <input type="text" id="appInfo" size="35">
                   <button onclick="send('appInfo')">Send</button>
               
               </div>
           </body>
        </html>

8. Bemærk følgende punkter om HTML-filen ovenfor:

    -   Den indeholder et sæt af input bokse, hvor du kan angive data, der skal bruges som navne for din begivenhed, Job, fejl, parser AppInfo. Når du klikker på knappen ud for den, foretages et opkald til det Javascript, der ringer til sidst metoderne fra filen bro til at overføre opkaldet til Mobile aftale iOS SDK. 
    -   Vi mærkning på nogle statisk ekstra oplysninger til de hændelser, job og lige fejl til at vise, hvordan dette kan ske. Denne ekstra oplysninger er sendt som en JSON streng, der, hvis du ser i den `EngagementJsExports.m` fil, er parset og overføres sammen med at sende begivenheder job, fejl. 
    -   Et Mobile aftale Job er startet med det navn, du angiver i inputfeltet, kører i 10 sekunder og Luk. 
    -   Der overføres en parser appinfo Mobile aftale eller mærke med 'customer_name' som nøglen statisk og den værdi, du har indtastet i input som værdien for mærket. 
 
9. Køre appen, og du får vist følgende. Nu et navn for en test ud som følger, og klik på **Send** ud for den. 

    ![][1]

10. Hvis du går til fanen **skærm** af din app og udseende under **begivenheder -> detaljer**, får du nu vist begivenheden vises sammen med statisk app-oplysninger, som vi sender. 

    ![][2]

<!-- Images. -->
[1]: ./media/mobile-engagement-bridge-webview-native-ios/sending-event.png
[2]: ./media/mobile-engagement-bridge-webview-native-ios/event-output.png
