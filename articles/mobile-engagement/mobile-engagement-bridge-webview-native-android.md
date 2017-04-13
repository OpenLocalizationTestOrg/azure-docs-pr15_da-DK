<properties 
    pageTitle="Bro Android webvisning med oprindelige Mobile aftale Android SDK" 
    description="Beskriver, hvordan du opretter en bro mellem webvisning kører Javascript og den oprindelige Mobile aftale Android SDK"      
    services="mobile-engagement" 
    documentationCenter="mobile" 
    authors="piyushjo" 
    manager="erikre" 
    editor="" />

<tags 
    ms.service="mobile-engagement" 
    ms.workload="mobile" 
    ms.tgt_pltfrm="mobile-android" 
    ms.devlang="Java" 
    ms.topic="article" 
    ms.date="08/19/2016" 
    ms.author="piyushjo" />

#<a name="bridge-android-webview-with-native-mobile-engagement-android-sdk"></a>Bro Android webvisning med oprindelige Mobile aftale Android SDK

> [AZURE.SELECTOR]
- [Android bro](mobile-engagement-bridge-webview-native-android.md)
- [iOS bro](mobile-engagement-bridge-webview-native-ios.md)

Nogle mobilapps er designet som en hybrid-app, hvor selve appen er udviklet ved hjælp af indbyggede Android udvikling, men nogle af eller endda alle skærmbillederne gengives i en Android webvisning. Du kan stadig bruge Mobile aftale Android SDK i disse apps, og dette selvstudium beskrives, hvordan du gør dette. Nedenstående eksempelkode er baseret på Android dokumentationen [her](https://developer.android.com/guide/webapps/webview.html#BindingJavaScript). Det beskrives, hvordan denne dokumenterede metode kan bruges til at implementere det samme for Mobile aftale Android SDKS ofte anvendte metoder, så en webvisning fra en hybrid app kan også starte anmodninger om at spore hændelser job fejl, og app-info under rørplan dem via vores Android SDK. 

1. Først, skal du sikre dig, at du har gennemgået vores [Introduktion – selvstudium](mobile-engagement-android-get-started.md) til at integrere Mobile aftale Android SDK i din hybrid-app. Når du gør dette, din `OnCreate` metode ser sådan ud.  
    
        @Override
        protected void onCreate(Bundle savedInstanceState) {
            super.onCreate(savedInstanceState);
            setContentView(R.layout.activity_main);
    
            EngagementConfiguration engagementConfiguration = new EngagementConfiguration();
            engagementConfiguration.setConnectionString("<Mobile Engagement Conn String>");
            EngagementAgent.getInstance(this).init(engagementConfiguration);
        }

2. Nu Sørg for, at din hybrid app har en skærm med en webvisning på den. Koden for den vil være svarende til følgende hvor vi indlæser en lokal HTML filer **Sample.html** i webvisning i den `onCreate` metode på skærmen. 

        private void SetWebView() {
            WebView myWebView = (WebView) findViewById(R.id.webview);
            myWebView.loadUrl("file:///android_asset/Sample.html");
        }

        protected void onCreate(Bundle savedInstanceState) {
            ...
            ...
            SetWebView();
        }

3. Opret nu en bro fil med navnet **WebAppInterface** som opretter en slikpapir over nogle ofte anvendte Mobile aftale Android SDK metoder ved hjælp af den `@JavascriptInterface` metode, der er beskrevet i [Android dokumentation](https://developer.android.com/guide/webapps/webview.html#BindingJavaScript):

        import android.content.Context;
        import android.os.Bundle;
        import android.util.Log;
        import android.webkit.JavascriptInterface;
        
        import com.microsoft.azure.engagement.EngagementAgent;
        
        import org.json.JSONArray;
        import org.json.JSONObject;
        
        public class WebAppInterface {
            Context mContext;
        
            /** Instantiate the interface and set the context */
            WebAppInterface(Context c) {
                mContext = c;
            }
        
            @JavascriptInterface
            public void sendEngagementEvent(String name, String extras ){
                EngagementAgent.getInstance(mContext).sendEvent(name, ParseExtras(extras));
            }
        
            @JavascriptInterface
            public void startEngagementJob(String name, String extras ){
                EngagementAgent.getInstance(mContext).startJob(name, ParseExtras(extras));
            }
        
            @JavascriptInterface
            public void endEngagementJob(String name){
                EngagementAgent.getInstance(mContext).endJob(name);
            }
        
            @JavascriptInterface
            public void sendEngagementError(String name, String extras ){
                EngagementAgent.getInstance(mContext).sendError(name, ParseExtras(extras));
            }
        
            @JavascriptInterface
            public void sendEngagementAppInfo(String appInfo){
                EngagementAgent.getInstance(mContext).sendAppInfo(ParseExtras(appInfo));
            }
        
            public Bundle ParseExtras(String input) {
                Bundle extras = new Bundle();
        
                try {
                    JSONObject jObject = new JSONObject(input);
                    extras.putString(jObject.names().getString(0),
                            jObject.get(jObject.names().getString(0)).toString());
                } catch (Exception e) {
                    e.printStackTrace();
                }
                return extras;
            }
        }  

4. Når vi har oprettet den ovenstående bro-fil, har vi brug at sikre, at det er knyttet til vores webvisning. For at dette kan ske, skal du redigere din `SetWebview` metode, så de ser ud som følger:

        private void SetWebView() {
            WebView myWebView = (WebView) findViewById(R.id.webview);
            myWebView.loadUrl("file:///android_asset/Sample.html");
            WebSettings webSettings = myWebView.getSettings();
            webSettings.setJavaScriptEnabled(true);
            myWebView.addJavascriptInterface(new WebAppInterface(this), "EngagementJs");
        }

5. I ovenstående kodestykket vi kaldet `addJavascriptInterface` knytte vores bro klasse til vores webvisning og også oprettet et håndtag, kaldet **EngagementJs** for at ringe metoderne fra filen bro. 

6. Nu oprette følgende fil kaldet **Sample.html** i dit projekt i en mappe med navnet **Aktiver** , er indlæst i webvisning og hvor vi ringer metoderne fra filen bro.

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
                      log('window.onerror: ' + err);
                    }
        
                    send = function(inputId)
                    {
                        var input = document.getElementById(inputId);
                        if(input)
                        {
                            var value = input.value;
                            // Example of how extras info can be passed with the Engagement logs
                            var extras = '{"CustomerId":"MS290011"}';
        
                            if(value && value.length > 0)
                            {
                                switch(inputId)
                                {
                                    case "event":
                                    EngagementJs.sendEngagementEvent(value, extras);
                                    break;
        
                                    case "job":
                                    EngagementJs.startEngagementJob(value, extras);
                                    window.setTimeout( function()
                                    {
                                      EngagementJs.endEngagementJob(value);
                                    }, 10000 );
                                    break;
        
                                    case "error":
                                    EngagementJs.sendEngagementError(value, extras);
                                    break;
        
                                    case "appInfo":
                                    EngagementJs.sendEngagementAppInfo({"customer_name":value});
                                    break;
                                }
                            }
                        }
                    }
                </script>
            </head>
            <body>
                <h1>Bridge Tester</h1>
                <div id='engagement'>
                    <h2>Event</h2>
                    <input type="text" id="event" size="35">
                    <button onclick="send('event')">Send</button>
        
                    <h2>Job</h2>
                    <input type="text" id="job" size="35">
                    <button onclick="send('job')">Send</button>
        
                    <h2>Error</h2>
                    <input type="text" id="error" size="35">
                    <button onclick="send('error')">Send</button>
        
                    <h2>AppInfo</h2>
                    <input type="text" id="appInfo" size="35">
                    <button onclick="send('appInfo')">Send</button>
                </div>
            </body>
        </html>

8. Bemærk følgende punkter om HTML-filen ovenfor:

    -   Den indeholder et sæt af input bokse, hvor du kan angive data, der skal bruges som navne for din begivenhed, Job, fejl, parser AppInfo. Når du klikker på knappen ud for den, foretages et opkald til det Javascript, der ringer til sidst metoderne fra filen bro til at overføre opkaldet til Mobile aftale Android SDK. 
    -   Vi mærkning på nogle statisk ekstra oplysninger til de hændelser, job og lige fejl til at vise, hvordan dette kan ske. Denne ekstra oplysninger er sendt som en JSON streng, der, hvis du ser i den `WebAppInterface` fil, er parset og indsat i en Android-enhed `Bundle` og sendes sammen med at sende begivenheder job, fejl. 
    -   Et Mobile aftale Job er startet med det navn, du angiver i inputfeltet, kører i 10 sekunder og Luk. 
    -   Der overføres en parser appinfo Mobile aftale eller mærke med 'customer_name' som nøglen statisk og den værdi, du har indtastet i input som værdien for mærket. 
 
9. Køre appen, og du får vist følgende. Nu et navn for en test ud som følger, og klik på **Send** under den. 

    ![][1]

10. Hvis du går til fanen **skærm** af din app og udseende under **begivenheder -> detaljer**, får du nu vist begivenheden vises sammen med statisk app-oplysninger, som vi sender. 

    ![][2]

<!-- Images. -->
[1]: ./media/mobile-engagement-bridge-webview-native-android/sending-event.png
[2]: ./media/mobile-engagement-bridge-webview-native-android/event-output.png
