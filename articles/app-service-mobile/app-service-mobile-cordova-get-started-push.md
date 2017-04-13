<properties
    pageTitle="Føje Pushmeddelelser til Apache Cordova App med Azure-Mobilapps | Azure App Service"
    description="Lær, hvordan du bruger Azure Mobile-Apps til at sende pushmeddelelser til din Apache Cordova app."
    services="app-service\mobile"
    documentationCenter="javascript"
    manager="erikre"
    editor=""
    authors="ysxu"/>

<tags
    ms.service="app-service-mobile"
    ms.workload="mobile"
    ms.tgt_pltfrm="mobile-html"
    ms.devlang="javascript"
    ms.topic="article"
    ms.date="10/01/2016"
    ms.author="yuaxu"/>

# <a name="add-push-notifications-to-your-apache-cordova-app"></a>Føje pushmeddelelser til din Apache Cordova-app

[AZURE.INCLUDE [app-service-mobile-selector-get-started-push](../../includes/app-service-mobile-selector-get-started-push.md)]

## <a name="overview"></a>Oversigt

I dette selvstudium føjer du pushmeddelelser til projektet [Apache Cordova Hurtig start] , så der sendes en push-besked til enheden, hver gang en post er indsat.

Hvis du ikke bruger hentede Hurtig start server projektet, skal du pakken opslagsnål meddelelse filtypenavn. Du kan få flere oplysninger i [arbejde med .NET back end-serveren SDK til Azure Mobile-Apps](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md) .

##<a name="prerequisites"></a>Forudsætninger

Dette selvstudium dækker et Apache Cordova-program, der er udviklet med Visual Studio-2015, der kører på Google Android emulatoren, en Android-enhed, en Windows-enhed og en iOS-enhed.

For at fuldføre dette selvstudium skal bruge du:

* En PC med [Visual Studio Community 2015] eller nyere versioner.
* [Visual Studio-værktøjer til Apache Cordova].
* En [aktiv Azure-konto](https://azure.microsoft.com/pricing/free-trial/).
* Et færdigt projekt [Apache Cordova Hurtig start] .
* (Android) En [Google-konto] med en bekræftet mailadresse.
* (iOS) En Apple udvikler Program medlemskab og en iOS-enhed (iOS Simulator understøtter ikke push).
* (Windows) En Windows Store udvikler-konto og en Windows 10-enhed.

##<a name="configure-hub"></a>Konfigurere en besked om hub

[AZURE.INCLUDE [app-service-mobile-configure-notification-hub](../../includes/app-service-mobile-configure-notification-hub.md)]

[Se en video, der viser trinnene i dette afsnit](https://channel9.msdn.com/series/Azure-connected-services-with-Cordova/Azure-connected-services-task-3-Create-azure-notification-hub)

##<a name="update-the-server-project-to-send-push-notifications"></a>Opdatere server projektet for at sende pushmeddelelser

[AZURE.INCLUDE [app-service-mobile-update-server-project-for-push-template](../../includes/app-service-mobile-update-server-project-for-push-template.md)]

##<a name="add-push-to-app"></a>Ændre din Cordova app for at modtage pushmeddelelser

Du skal sikre dig, at projektet Apache Cordova app er klar til at håndtere pushmeddelelser ved at installere Cordova opslagsnål plug-in'et plus en hvilken som helst platform-specifikke push-tjenester.

#### <a name="update-the-cordova-version-in-your-project"></a>Opdatere Cordova version i projektet.

Vi anbefaler, at du opdaterer klientprojektet til Cordova 6.1.1, hvis projektet er konfigureret til brug af en ældre version. Hvis du vil opdatere projektet, skal du højreklikke på config.xml for at åbne konfiguration designer. Vælg fanen platforme og vælg 6.1.1 i tekstfeltet **Cordova CLI** .

Vælg **opbygge**og derefter **Oprette løsning** , der skal opdateres projektet.

#### <a name="install-the-push-plugin"></a>Installere plug-in'et opslagsnål

Apache Cordova programmer kan ikke håndtere enheds- eller netværksproblemer funktioner indbygget.  Disse funktioner, der leveres af plug-ins, der er publiceret på [npm](https://www.npmjs.com/) eller på GitHub.  Den `phonegap-plugin-push` -plug-in bruges til at håndtere netværk pushmeddelelser.

Du kan installere plug-in'en til opslagsnål i en af følgende måder:

**Fra kommandoprompten:**

Du kan udføre følgende kommando:

    cordova plugin add phonegap-plugin-push

**Fra i Visual Studio:**

1.  I Solution Explorer skal du åbne den `config.xml` fil skal du klikke på **plug-ins** > **brugerdefineret**, markere **ciffer** som installationskilden og derefter angive `https://github.com/phonegap/phonegap-plugin-push` som kilde.

    ![](./media/app-service-mobile-cordova-get-started-push/add-push-plugin.png)

2.  Klik på pilen ud for installationskilden.

3. I **SENDER_ID**, hvis du allerede har et numerisk projekt-ID for Google udvikler Console-projekt, kan du tilføje det her. Ellers skal du indtaste en pladsholderværdi, som 777777, og hvis du målretter Android kan du opdatere denne værdi i config.xml senere.

4. Klik på **Tilføj**.

Push-plug-in'en er nu installeret.

####<a name="install-the-device-plugin"></a>Installere plug-in'en til enhed

Følg den samme fremgangsmåde, du brugte til at installere plug-in'et opslagsnål, men du kan finde plug-in'en til enhed på listen Core plug-ins (Klik på **plug-ins** > **Core** til at finde den). Du skal bruge denne plug-in til at hente navnet platform (`device.platform`).

#### <a name="register-your-device-for-push-on-start-up"></a>Registrere din enhed til opslagsnål på opstart

Vi vil som udgangspunkt omfatte minimale kode til Android. Senere, kan vi foretage nogle mindre ændringer til at køre på iOS- eller Windows 10.

1. Tilføje et opkald til **registerForPushNotifications** under tilbagekald logon, eller i bunden af metoden **onDeviceReady** :

        // Login to the service.
        client.login('google')
            .then(function () {
                // Create a table reference
                todoItemTable = client.getTable('todoitem');

                // Refresh the todoItems
                refreshDisplay();

                // Wire up the UI Event Handler for the Add Item
                $('#add-item').submit(addItemHandler);
                $('#refresh').on('click', refreshDisplay);

                    // Added to register for push notifications.
                registerForPushNotifications();

            }, handleError);

    I dette eksempel viser ringe **registerForPushNotifications** , når godkendelse er oprettet, som kan anbefales, når du bruger både pushmeddelelser og godkendelse i din app.

2. Tilføje den nye **registerForPushNotifications** metode på følgende måde:

        // Register for Push Notifications. Requires that phonegap-plugin-push be installed.
        var pushRegistration = null;
        function registerForPushNotifications() {
          pushRegistration = PushNotification.init({
              android: { senderID: 'Your_Project_ID' },
              ios: { alert: 'true', badge: 'true', sound: 'true' },
              wns: {}
          });

        // Handle the registration event.
        pushRegistration.on('registration', function (data) {
          // Get the native platform of the device.
          var platform = device.platform;
          // Get the handle returned during registration.
          var handle = data.registrationId;
          // Set the device-specific message template.
          if (platform == 'android' || platform == 'Android') {
              // Register for GCM notifications.
              client.push.register('gcm', handle, {
                  mytemplate: { body: { data: { message: "{$(messageParam)}" } } }
              });
          } else if (device.platform === 'iOS') {
              // Register for notifications.            
              client.push.register('apns', handle, {
                  mytemplate: { body: { aps: { alert: "{$(messageParam)}" } } }
              });
          } else if (device.platform === 'windows') {
              // Register for WNS notifications.
              client.push.register('wns', handle, {
                  myTemplate: {
                      body: '<toast><visual><binding template="ToastText01"><text id="1">$(messageParam)</text></binding></visual></toast>',
                      headers: { 'X-WNS-Type': 'wns/toast' } }
              });
          }
        });

        pushRegistration.on('notification', function (data, d2) {
          alert('Push Received: ' + data.message);
        });

        pushRegistration.on('error', handleError);
        }

3. (Android) Erstat teksten i ovenstående kode `Your_Project_ID` med numeriske project ID for din app fra [Google udvikler Console].

## <a name="optional-configure-and-run-the-app-on-android"></a>(Valgfrit) Konfigurere og køre appen på Android-enhed

Afslutte dette afsnit for at aktivere pushmeddelelser til Android.

####<a name="enable-gcm"></a>Aktivere Firebase Messaging i skyen

Da vi målretter Google Android platform først, skal du aktivere Firebase skyen Messaging. På samme måde, hvis du er blevet målretning af Microsoft Windows-enheder, skal du aktivere understøttelse af WNS.

[AZURE.INCLUDE [notification-hubs-enable-firebase-cloud-messaging](../../includes/notification-hubs-enable-firebase-cloud-messaging.md)]

####<a name="configure-backend"></a>Konfigurere Mobile-App back-end for at sende opslagsnål anmodninger ved hjælp af FCM

[AZURE.INCLUDE [app-service-mobile-android-configure-push](../../includes/app-service-mobile-android-configure-push.md)]

####<a name="configure-your-cordova-app-for-android"></a>Konfigurere din Cordova app til Android

Åbn config.xml i din Cordova app, og Erstat `Your_Project_ID` med numeriske project ID for din app fra [Google udvikler Console].

        <plugin name="phonegap-plugin-push" version="1.7.1" src="https://github.com/phonegap/phonegap-plugin-push.git">
            <variable name="SENDER_ID" value="Your_Project_ID" />
        </plugin>

Åbn index.js og opdaterer koden for at bruge dit numeriske project-ID.

        pushRegistration = PushNotification.init({
            android: { senderID: 'Your_Project_ID' },
            ios: { alert: 'true', badge: 'true', sound: 'true' },
            wns: {}
        });

####<a name="configure-device"></a>Konfigurere din Android-enhed til USB-fejlfinding

Før du kan installere programmet til din Android-enhed, skal du aktivere USB-fejlfinding.  Udfør følgende trin på din Android-telefon:

1. Gå til **Indstillinger** > **om telefon**og derefter trykke på **Build-nummer** indtil udviklertilstand er aktiveret (om 7 gange).

2. Tilbage i **Indstillinger for** > **Udviklerindstillinger** aktivere **USB fejlfinding**, og forbind din Android-telefon med din udvikling PC med et USB-kabel.

Vi har testet dette ved hjælp af en Google Nexus 5 X-enhed, der kører Android 6.0 (Marshmallow).  Teknikker, der er dog almindelige på tværs af en hvilken som helst moderne Android version.

#### <a name="install-google-play-services"></a>Installere Google Play Services

Push-plug-in'en er baseret på Android Google afspille tjenester for pushmeddelelser.  

1.  Klik på **Funktioner**i **Visual Studio**, > **Android** > **Android SDK Manager**, Udvid mappen **Extras** , og Markér afkrydsningsfeltet for at sikre dig, at hver af de følgende SDK'er er installeret.
    * Android 2.3 eller nyere
    * Google lager revision 27 eller nyere
    * Google Play Services 9.0.2 eller nyere

2.  Klik på **Installer-pakker** , og vent for at fuldføre installationen.

Aktuelt påkrævet ‑biblioteker vises i [installationsdokumentationen til phonegap-plug-in-push].

#### <a name="test-push-notifications-in-the-app-on-android"></a>Test pushmeddelelser i appen på Android

Du kan nu test pushmeddelelser ved at køre appen og indsætte elementer i tabellen TodoItem. Du kan gøre dette fra den samme enhed eller en anden enhed, som du bruger den samme backend-version. Teste din Cordova app på en Android-platform i en af følgende måder:

- **På en fysisk enhed:**  
Tilslut din Android-enhed til computeren udvikling med et USB-kabel.  Vælg **enhed**i stedet for **Google Android Emulator**. Visual Studio skal installere programmet på enheden og køre den.  Derefter kan du arbejde med programmet på enheden.  
Forbedre oplevelsen udvikling.  Skærmdeling programmer som [Mobizen] kan hjælpe dig med at udvikle et program, Android ved projicerer skærmen Android til en webbrowser på din PC.

- **På en Android emulator:**  
Der findes flere konfigurationstrin, der er påkrævet, når der benyttes en emulator.

    Sørg for, at du anvender for eller fejlfinding på en virtuel enhed, der indeholder Google APIs angive som destination, som vist nedenfor i Android virtuelle enhed (AVD) manager.

    ![](./media/app-service-mobile-cordova-get-started-push/google-apis-avd-settings.png)

    Hvis du vil bruge en hurtigere x86 emulator, du [installerer HAXM driver](https://taco.visualstudio.com/en-us/docs/run-app-apache/#HAXM) og konfigurere emulatoren bruge den.

    Tilføje en Google-konto til Android-enhed ved at klikke på **Apps** > **Indstillinger** > **Tilføj konto**og derefter følge instruktionerne for at tilføje en eksisterende Google-konto på enheden (Vi anbefaler, at du bruger en eksisterende konto i stedet for at oprette en ny).

    ![](./media/app-service-mobile-cordova-get-started-push/add-google-account.png)

    Køre appen todolist som før, og Indsæt et nyt element opgaveliste. Denne gang, vises et meddelelsesikon i meddelelsesområdet. Du kan åbne meddelelse skuffe for at få vist den fulde tekst i meddelelsen om.

    ![](./media/app-service-mobile-cordova-get-started-push/android-notifications.png)

##<a name="optional-configure-and-run-on-ios"></a>(Valgfrit) Konfigurere og køre på iOS

Dette afsnit gælder for kører Cordova projektet på iOS-enheder. Hvis du ikke arbejder med iOS-enheder, kan du springe dette afsnit.

####<a name="install-and-run-the-ios-remotebuild-agent-on-a-mac-or-cloud-service"></a>Installere og køre iOS remotebuild agent på en Mac eller skybaseret tjeneste

Før du kan køre en Cordova app på iOS ved hjælp af Visual Studio, kan du gennemgå trinnene i [iOS konfigurationsvejledningen til](http://taco.visualstudio.com/en-us/docs/ios-guide/) at installere og køre remotebuild agent.

Kontrollér, at du kan oprette appen til iOS. Trinnene i guiden konfiguration er påkrævet til at opbygge til iOS fra Visual Studio. Hvis du ikke har en Mac, kan du oprette til iOS brug af remotebuild agent på en tjeneste som MacInCloud. Du finder flere oplysninger i [køre din iOS-app i skyen](http://taco.visualstudio.com/en-us/docs/build_ios_cloud/).

>[AZURE.NOTE] XCode 7 eller nyere er påkrævet for at bruge push-plug-in på iOS.

####<a name="find-the-id-to-use-as-your-app-id"></a>Finde-ID'ET skal bruges som din App-ID

Før du registrere din app til pushmeddelelser, Åbn config.xml i din Cordova app skal du finde den `id` attributten værdi i elementet widget og kopiere den til senere brug. I følgende XML ID'ET er `io.cordova.myapp7777777`.

        <widget defaultlocale="en-US" id="io.cordova.myapp7777777"
        version="1.0.0" windows-packageVersion="1.1.0.0" xmlns="http://www.w3.org/ns/widgets"
            xmlns:cdv="http://cordova.apache.org/ns/1.0" xmlns:vs="http://schemas.microsoft.com/appx/2014/htmlapps">

Senere, bruge dette id, når du opretter en App-ID på Apples udvikler portal. (Hvis du opretter en anden App-ID på portalen udvikler, og du vil bruge, skal du tage et par ekstra trin senere i dette selvstudium til at ændre dette ID i config.xml. ID i elementet widget stemme overens med App-ID på portalen developer.)

####<a name="register-the-app-for-push-notifications-on-apples-developer-portal"></a>Registrere app til pushmeddelelser på Apples udvikler portal

[AZURE.INCLUDE [Enable Apple Push Notifications](../../includes/enable-apple-push-notifications.md)]

[Se en video, der viser lignende trin](https://channel9.msdn.com/series/Azure-connected-services-with-Cordova/Azure-connected-services-task-5-Set-up-apns-for-push)

####<a name="configure-azure-to-send-push-notifications"></a>Konfigurere Azure for at sende pushmeddelelser

[AZURE.INCLUDE [app-service-mobile-apns-configure-push](../../includes/app-service-mobile-apns-configure-push.md)]

####<a name="verify-that-your-app-id-matches-your-cordova-app"></a>Kontrollér, at din App-ID stemmer overens med din Cordova-app

Hvis den App-ID, du oprettede i kontoen Apple udvikler allerede opfylder elementet widget i config.xml ID, kan du springe dette trin. Men hvis id'erne ikke stemmer overens, benytte følgende fremgangsmåde:

1. Slet mappen platforme fra dit projekt.

2. Slet mappen plug-ins fra dit projekt.

3. Slet mappen node_modules fra dit projekt.

4. Opdater attributten id i elementet widget i config.xml bruge App-ID, du oprettede i din Apple udvikler konto.

5. Genopbygge projektet.

#####<a name="test-push-notifications-in-your-ios-app"></a>Test pushmeddelelser i din iOS-app

1. Sørg for, at **iOS** er markeret som mål-installation i Visual Studio, og vælg **enhed** til at køre på din forbundne iOS-enhed.

    Du kan køre på en iOS-enhed har forbindelse til din PC ved hjælp af iTunes. IOS Simulator understøtter ikke pushmeddelelser.

2. Tryk på knappen **Kør** eller **F5** i Visual Studio til at opbygge projektet og starte appen på en iOS-enhed, og klik derefter på **OK** for at acceptere pushbeskeder.

    >[AZURE.NOTE] Du skal eksplicit acceptere pushmeddelelser fra din app. Denne anmodning sker kun, første gang, der kører på app.

3. Skriv en opgave i app, og klik på plustegnet (+) ikon.

4. Kontrollér, at en meddelelse er modtaget og derefter klikke på OK for at afvise meddelelsen.

##<a name="optional-configure-and-run-on-windows"></a>(Valgfrit) Konfigurere og køre på Windows

Dette afsnit gælder for kører Apache Cordova app projektet på Windows 10-enheder (PhoneGap opslagsnål plug-in'et understøttes på Windows 10). Hvis du ikke arbejder med Windows-enheder, kan du springe dette afsnit.

####<a name="register-your-windows-app-for-push-notifications-with-wns"></a>Registrere din Windows-app til pushmeddelelser med WNS

For at bruge indstillingerne for Store i Visual Studio skal du vælge et Windows-mål på listen løsning platforme som **Windows-x64** eller **Windows-x86** (undgå **Windows AnyCPU** for pushmeddelelser).

[AZURE.INCLUDE [app-service-mobile-register-wns](../../includes/app-service-mobile-register-wns.md)]

[Se en video, der viser lignende trin](https://channel9.msdn.com/series/Azure-connected-services-with-Cordova/Azure-connected-services-task-6-Set-up-wns-for-push)

####<a name="configure-the-notification-hub-for-wns"></a>Konfigurere meddelelse hubben til WNS

[AZURE.INCLUDE [app-service-mobile-configure-wns](../../includes/app-service-mobile-configure-wns.md)]

####<a name="configure-your-cordova-app-to-support-windows-push-notifications"></a>Konfigurere din Cordova app for at understøtte Windows pushmeddelelser

Åbn konfiguration designer (højreklik på config.xml og vælge **Vis Designer**), Vælg fanen **Windows** , og vælg **Windows 10** under **Windows Target Version**.

>[AZURE.NOTE] Hvis du bruger en Cordova version før Cordova 5.1.1 (6.1.1 anbefales), skal du også angive flaget Toast stand til at true i config.xml.

For at understøtte opslagsnål beskeder i din standard (fejlfinding) opbygger, Åbn build.json fil. Kopiere "release" konfiguration til konfigurationen af fejlfinding.

        "windows": {
            "release": {
                "packageCertificateKeyFile": "res\\native\\windows\\CordovaApp.pfx",
                "publisherId": "CN=yourpublisherID"
            }
        }

EfterOpdatering ser den foregående kode sådan ud.

    "windows": {
        "release": {
            "packageCertificateKeyFile": "res\\native\\windows\\CordovaApp.pfx",
            "publisherId": "CN=yourpublisherID"
            },
        "debug": {
            "packageCertificateKeyFile": "res\\native\\windows\\CordovaApp.pfx",
            "publisherId": "CN=yourpublisherID"
            }
        }

Udvikle app, og Bekræft, at du har ingen fejl. Du klient-app skal nu registrere til meddelelser fra Mobile-App backend-version. Gentag dette afsnit for alle Windows projekter i din løsning.

####<a name="test-push-notifications-in-your-windows-app"></a>Test pushmeddelelser i din Windows-app

I Visual Studio, skal du sørge for, at en Windows-platform er markeret som mål-installation, som **Windows-x64** eller **Windows-x86**. For at køre appen på en PC med Windows 10 vært Visual Studio skal du vælge **Lokale computer**.

Tryk på knappen Kør til at opbygge projektet og starte appen.

Skriv et navn til en ny todoitem i app, og klik på plustegnet (+) for at tilføje den.

Kontrollér, at en meddelelse er modtaget, når elementet er blevet tilføjet.

##<a name="next-steps"></a>Næste trin

* Læs mere om [Meddelelse Hubs] til at få mere at vide om pushmeddelelser.
* Hvis du ikke allerede har gjort det, kan du stadig selvstudium ved [At tilføje godkendelse] til din Apache Cordova app.

Lær at bruge SDK'er.

* [Apache Cordova SDK]
* [ASP.NET Server SDK]
* [Node.js Server SDK]

<!-- URLs -->
[Tilføje godkendelse]: app-service-mobile-cordova-get-started-users.md
[Apache Cordova Hurtig start]: app-service-mobile-cordova-get-started.md
[authentication]: app-service-mobile-cordova-get-started-users.md
[Work with the .NET backend server SDK for Azure Mobile Apps]: app-service-mobile-dotnet-backend-how-to-use-server-sdk.md
[Google-konto]: http://go.microsoft.com/fwlink/p/?LinkId=268302
[Google udvikler Console]: https://console.developers.google.com/home/dashboard
[phonegap-plug-in-push installation dokumentation]: https://github.com/phonegap/phonegap-plugin-push/blob/master/docs/INSTALLATION.md
[Mobizen]: https://www.mobizen.com/
[Visual Studio Community 2015]: http://www.visualstudio.com/
[Visual Studio-værktøjer til Apache Cordova]: https://www.visualstudio.com/en-us/features/cordova-vs.aspx
[Meddelelse om Hubs]: ../notification-hubs/notification-hubs-push-notification-overview.md
[Apache Cordova SDK]: app-service-mobile-cordova-how-to-use-client-library.md
[ASP.NET Server SDK]: app-service-mobile-dotnet-backend-how-to-use-server-sdk.md
[Node.js Server SDK]: app-service-mobile-node-backend-how-to-use-server-sdk.md
