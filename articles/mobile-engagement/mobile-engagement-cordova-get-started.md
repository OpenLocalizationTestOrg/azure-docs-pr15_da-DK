<properties
    pageTitle="Introduktion til Azure Mobile aftale for Cordova/Phonegap"
    description="Lær at bruge Azure Mobile aftale med analyser og Push-beskeder til Cordova/Phonegap apps."
    services="mobile-engagement"
    documentationCenter="Mobile"
    authors="piyushjo"
    manager="dwrede"
    editor="" />

<tags
    ms.service="mobile-engagement"
    ms.workload="mobile"
    ms.tgt_pltfrm="mobile-phonegap"
    ms.devlang="js"
    ms.topic="hero-article" 
    ms.date="08/19/2016"
    ms.author="piyushjo" />

# <a name="get-started-with-azure-mobile-engagement-for-cordovaphonegap"></a>Introduktion til Azure Mobile aftale for Cordova/Phonegap

[AZURE.INCLUDE [Hero tutorial switcher](../../includes/mobile-engagement-hero-tutorial-switcher.md)]

Dette emne beskrives, hvordan du bruger Azure Mobile aftale til at forstå din appforbrug og sende pushmeddelelser til segmenteret brugere til en mobil-applikation udviklet med Cordova.

I dette selvstudium skal vi oprette en tom Cordova-app fra Mac og derefter integrere Mobile aftale SDK. Det indsamler grundlæggende analytics-data og modtager pushmeddelelser med Apple Push meddelelse System (APNS) til iOS og Google Cloud Messaging (GCM) til Android. Vi vil installerer det på en iOS eller Android-enhed til test. 

> [AZURE.NOTE] For at fuldføre dette selvstudium skal skal du have en active Azure-konto. Hvis du ikke har en konto, kan du oprette en gratis prøveversion konto på blot et par minutter. Yderligere oplysninger finder du [Azure gratis prøveversion](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Fen-us%2Fdocumentation%2Farticles%2Fmobile-engagement-cordova-get-started).

Dette selvstudium kræver følgende:

+ XCode, som du kan installere fra Mac App Store (til implementeringen i iOS)
+ [Android SDK og Emulator](http://developer.android.com/sdk/installing/index.html) (til implementeringen i Android)
+ Push-besked om certifikat (.p12), kan du hente fra Apple-Udviklercenter for APNS
+ GCM projektnummer, som du kan hente fra din Google udvikler konsol GCM
+ [Mobile aftale Cordova plug-in](https://www.npmjs.com/package/cordova-plugin-ms-azure-mobile-engagement)

> [AZURE.NOTE] Du kan finde kildekoden og den vigtige oplysninger om Cordova-plug-in på [Github](https://github.com/Azure/azure-mobile-engagement-cordova)

##<a id="setup-azme"></a>Konfiguration af Mobile aftale for din Cordova-app

[AZURE.INCLUDE [Create Mobile Engagement App in Portal](../../includes/mobile-engagement-create-app-in-portal-new.md)]

##<a id="connecting-app"></a>Oprette forbindelse din app til Mobile aftale back-end

Dette selvstudium indeholder en "grundlæggende integration" som er den minimale angive påkrævet for at indsamle data og sende en meddelelse om opslagsnål. 

Vi vil oprette en grundlæggende app med Cordova til at vise integration:

###<a name="create-a-new-cordova-project"></a>Oprette et nyt Cordova projekt

1. Start *Terminal* vindue på din Mac-computer, og Skriv følgende som opretter et nyt projekt i Cordova fra standardskabelonen. Sørg for, at profilen publicering du efterhånden bruge til at installere din iOS-app bruger 'com.mycompany.myapp' som App ID. 

        $ cordova create azme-cordova com.mycompany.myapp
        $ cd azme-cordova

2. Udfør følgende for at konfigurere dit projekt til **iOS** og køre i iOS Simulator:

        $ cordova platform add ios 
        $ cordova run ios

3. Udføre følgende for at konfigurere dit projekt til **Android** og køre i Android emulatoren. Sørg for, at indstillingerne for Android SDK Emulator har dets mål som Google APIs (Google Inc.) med CPU'EN / ABI som Google APIs ARM.  

        $ cordova platform add android
        $ cordova run android

4.  Tilføj Cordova Console plug-in'et. 

        $ cordova plugin add cordova-plugin-console 

###<a name="connect-your-app-to-mobile-engagement-backend"></a>Oprette forbindelse din app til Mobile aftale backend-version

1. Du kan installere plug-in'en til Azure Mobile aftale Cordova samtidig med at de variable værdier for at konfigurere plug-in'et:

        cordova plugin add cordova-plugin-ms-azure-mobile-engagement    
            --variable AZME_IOS_CONNECTION_STRING=<iOS Connection String> 
            --variable AZME_IOS_REACH_ICON=... (icon name WITH extension) 
            --variable AZME_ANDROID_CONNECTION_STRING=<Android Connection String> 
            --variable AZME_ANDROID_REACH_ICON=... (icon name WITHOUT extension)       
            --variable AZME_ANDROID_GOOGLE_PROJECT_NUMBER=... (From your Google Cloud console for sending push notifications) 
            --variable AZME_ACTION_URL =... (URL scheme which triggers the app for deep linking)
            --variable AZME_ENABLE_NATIVE_LOG=true|false
            --variable AZME_ENABLE_PLUGIN_LOG=true|false

*Android når ikon* : skal være navnet på ressourcen uden et lokalnummer eller drawable præfiks (ex: mynotificationicon), og ikonfilen skal kopieres til projektet android (platforme/android/rejser/drawable)

*iOS når ikon* : skal være navnet på ressourcen med dens filtypenavn (ex: mynotificationicon.png), og ikonfilen skal være føjet til projektet iOS med XCode (ved hjælp af menuen Filer)

##<a id="monitor"></a>Aktivere overvågning i realtid

1. Rediger **www/js/index.js** for at tilføje opkaldet til Mobile aftale erklære en ny aktivitet én gang hændelsen *deviceReady* modtages i Cordova project –.

         onDeviceReady: function() {
                Engagement.startActivity("myPage",{});
            }

2. Køre programmet:
        
    - **Til iOS**
    
        I `Terminal` vinduet Start din app i en ny forekomst af Simulator ved at udføre følgende:

            cordova run ios

    - **Til Android**
        
        I `Terminal` vinduet Start din app i en ny forekomst af emulator ved at udføre følgende:

            cordova run android

3. Du kan se følgende i loggene console:

        [Engagement] Agent: Session started
        [Engagement] Agent: Activity 'myPage' started
        [Engagement] Connection: Established
        [Engagement] Connection: Sent: appInfo
        [Engagement] Connection: Sent: startSession
        [Engagement] Connection: Sent: activity name='myPage'

##<a id="monitor"></a>Oprette forbindelse app med overvågning i realtid

[AZURE.INCLUDE [Connect app with real-time monitoring](../../includes/mobile-engagement-connect-app-with-monitor.md)]

##<a id="integrate-push"></a>Aktivere Push-beskeder og i app-beskeder

Mobile aftale kan du interagere med dine brugere, der bruger Push-beskeder og app-beskeder i forbindelse med kampagner. Dette modul kaldes rækkevidde på portalen Mobile aftale.
De følgende afsnit kan konfigurere din app for at hente dem.

###<a name="configure-push-credentials-for-mobile-engagement"></a>Konfigurere opslagsnål legitimationsoplysninger til Mobile aftale

Hvis du vil tillade Mobile aftale til at sende Push-beskeder på dine vegne, skal du give den adgang til din Apple iOS certifikat eller GCM Server API-nøgle. 
    
1. Gå til din Mobile aftale portal. Sikre, at du er i den app, vi bruger til dette projekt og derefter klikke på knappen **Udnyt** nederst:
    
    ![][1]
    
2. Du kan lande på indstillingssiden i din aftale Portal. Der skal du klikke på den **Oprindelige Push** sektion:
    
    ![][2]

3. Konfigurere iOS certifikat/GCM Server API-nøgle

    **[iOS]**

    en. Vælg din .p12, overføre den, og skriv din adgangskode:
    
    ![][3]

    **[Android]**

    en. Klik på redigeringsikonet foran **API-nøgle** i sektionen Indstillinger for GCM og i pop-up-vinduet, der vises, indsætte tasten GCM Server og klikke på **OK**. 
        
    ![][4]

###<a name="enable-push-notifications-in-the-cordova-app"></a>Aktivér pushmeddelelser i appen Cordova

Redigere **www/js/index.js** for at tilføje opkaldet til Mobile aftale til at anmode om pushmeddelelser og erklære en handler:

     onDeviceReady: function() {
           Engagement.initializeReach(  
                // on OpenUrl  
                function(_url) {   
                alert(_url);   
                });  
            Engagement.startActivity("myPage",{});  
        }

###<a name="run-the-app"></a>Køre appen

**[iOS]**

1. Vi bruger XCode til at opbygge og anvende appen på kameraet, for at teste pushmeddelelser, da iOS kan kun pushmeddelelser til en faktisk enhed. Gå til den placering, hvor projektet Cordova er oprettet, og gå til **...\platforms\ios** placering. Åbn den oprindelige .xcodeproj fil i XCode. 
    
2. Opbygge og anvende Cordova appen til iOS-enhed, der bruger den konto, som har den klargøring profil, der indeholder det certifikat, du lige har overført til portalen Mobile aftale og App-Id, hvilke matcher det, du har angivet under oprettelse af appen Cordova. Du kan tjekke *Samlet id* i din **ressourcer\*-info.plist** fil i XCode til at matche det. 

3. Standard iOS pop-up-vinduet vises på enheden siger, at app anmoder om tilladelse til at sende meddelelser. Give tilladelse. 

**[Android]**

Du kan blot bruge emulatoren til at køre Android-appen, som GCM beskeder understøttes på Android emulatoren. 

    cordova run android

##<a id="send"></a>Sende en meddelelse til din app

Vi vil nu oprette en simpel opslagsnål meddelelse kampagnens, der sender en opslagsnål til din app, der kører på enheden:

1. Gå til fanen **FAT** i din Mobile aftale-portal

2. Klik på **Ny meddelelse** for at oprette din opslagsnål kampagnens

    ![][6]

3. Give input for at oprette din kampagnens **[Android]**
    
    - Angiv et **navn** til din campaign. 
    - Vælg den **Leveringstype** som *systemmeddelelse* *enkel*
    - Vælg **leveringstidspunktet** som *"alle klokkeslætsformat"*
    - Angiv en **Titel** til din anmeldelse, bliver den første linje i opslagsnål.
    - Giver dig en **meddelelse** til din meddelelse, der fungerer som meddelelsens brødtekst. 

    ![][11]

4. Give input for at oprette din kampagnens **[iOS]**

    - Angiv et **navn** til din campaign. 
    - Vælg **leveringstidspunktet** som *"ikke kun app"*
    - Angiv en **Titel** til din anmeldelse, bliver den første linje i opslagsnål.
    - Giver dig en **meddelelse** til din meddelelse, der fungerer som meddelelsens brødtekst. 
 
    ![][12]

5. Rul ned, og i afsnittet indhold Vælg **besked kun**

    ![][8]

6. [Valgfrit] Du kan også angive en handling URL-adresse. Sørg for, at det bruger en URL-skema, der er angivet under konfigurationen af plug-innen **AZME\_OMDIRIGERE\_URL-adressen** variabel fx *myapp://test*.  

7. Du er færdig, angive den mest grundlæggende kampagnens mulige. Rul ned igen nu, og klik på knappen **Opret** for at gemme dine campaign.

8. Til sidst **Aktivér** din kampagnens
    
    ![][10]

9. Du bør nu se en opslagsnål meddelelse på din enhed eller emulator som en del af denne campaign. 

##<a id="next-steps"></a>Næste trin
[Oversigt over alle metoder, der er tilgængelige med Cordova Mobile aftale SDK](https://github.com/Azure/azure-mobile-engagement-cordova)

<!-- Images. -->

[1]: ./media/mobile-engagement-cordova-get-started/engage-button.png
[2]: ./media/mobile-engagement-cordova-get-started/engagement-portal.png
[3]: ./media/mobile-engagement-cordova-get-started/native-push-settings.png
[4]: ./media/mobile-engagement-cordova-get-started/api-key.png
[6]: ./media/mobile-engagement-cordova-get-started/new-announcement.png
[8]: ./media/mobile-engagement-cordova-get-started/campaign-content.png
[10]: ./media/mobile-engagement-cordova-get-started/campaign-activate.png
[11]: ./media/mobile-engagement-cordova-get-started/campaign-first-params-android.png
[12]: ./media/mobile-engagement-cordova-get-started/campaign-first-params-ios.png

