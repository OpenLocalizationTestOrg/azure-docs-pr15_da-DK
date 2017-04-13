<properties
    pageTitle="Sende pushmeddelelser til Chrome-apps med Azure meddelelse Hubs | Microsoft Azure"
    description="Lær at bruge Azure meddelelse Hubs til at sende pushmeddelelser til en Chrome App."
    services="notification-hubs"
    keywords="Mobile pushmeddelelser, pushmeddelelser, push meddelelse chrome pushmeddelelser"
    documentationCenter=""
    authors="ysxu"
    manager="erikre"
    editor=""/>

<tags
    ms.service="notification-hubs"
    ms.workload="mobile"
    ms.tgt_pltfrm="mobile-chrome"
    ms.devlang="JavaScript"
    ms.topic="hero-article"
    ms.date="10/03/2016"
    ms.author="yuaxu"/>

# <a name="send-push-notifications-to-chrome-apps-with-azure-notification-hubs"></a>Sende pushmeddelelser til Chrome-apps med Azure meddelelse Hubs

[AZURE.INCLUDE [notification-hubs-selector-get-started](../../includes/notification-hubs-selector-get-started.md)]

Dette emne beskrives, hvordan du bruger Azure meddelelse Hubs til at sende pushmeddelelser til en Chrome-App, som vises i forbindelse med Google Chrome-browseren. I dette selvstudium skal du oprette en Chrome-app, der modtager pushmeddelelser ved hjælp af [Google Cloud Messaging (GCM)](https://developers.google.com/cloud-messaging/). 

>[AZURE.NOTE] For at fuldføre dette selvstudium skal skal du have en active Azure-konto. Hvis du ikke har en konto, kan du oprette en gratis prøveversion konto på blot et par minutter. Yderligere oplysninger finder du [Azure gratis prøveversion](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Fen-us%2Fdocumentation%2Farticles%notification-hubs-chrome-get-started%2F).

Selvstudiet vejleder dig gennem disse grundlæggende trin til at aktivere pushbeskeder:

* [Aktivere Google Cloud-beskeder](#register)
* [Konfigurere din meddelelse-hub](#configure-hub)
* [Oprette forbindelse din Chrome App til hubben meddelelse](#connect-app)
* [Sende en meddelelse om opslagsnål til din Chrome App](#send)
* [Yderligere funktioner og -funktioner](#next-steps)

>[AZURE.NOTE] Chrome app pushmeddelelser er ikke meddelelser om generisk i browseren – de er specifikke for browser udvidelsesmuligheder model (se [Oversigt over Chrome Apps] få mere at vide). Ud over skrivebordet browseren køre Chrome apps på mobile (Android og iOS) via Apache Cordova. Se [Chrome-Apps på Mobile] for at få mere at vide.

Konfiguration af GCM og Azure meddelelse Hubs er identisk med konfiguration af til Android, da [Google Cloud Messaging for Chrome] frarådes og den samme GCM understøtter nu både Android-enheder og Chrome forekomster.

##<a id="register"></a>Aktivere Google Cloud-beskeder

1. Gå til webstedet [Google Cloud-konsollen] , log på med legitimationsoplysningerne Google-konto, og klik derefter på knappen **Opret projekt** . Giver et passende **Projektnavn**, og klik derefter på knappen **Opret** .

    ![Google Cloud konsol – Opret projekt][1]

2. Noter **Projektnummer** på siden **projekter** for det projekt, som du lige har oprettet. Du kan bruge dette som **GCM afsender-ID** i Chrome App til at registrere med GCM.

    ![Google Cloud Console - projektnummer][2]

3. I venstre rude, klik på **API'er & auth**, og rul derefter ned, og klik på indstillingen for at aktivere **Google Cloud Messaging til Android**. Du behøver ikke at aktivere **Google Cloud Messaging for Chrome**.

    ![Google Cloud Console - Server-tasten][3]

4. Klik på **legitimationsoplysninger**i venstre rude, > **Oprette ny nøgle** > **Server nøgle** > **Opret**.

    ![Google Cloud Console - legitimationsoplysninger][4]

5. Noter på serveren, **API-nøgle**. Du skal konfigurere dette i din meddelelse hub derefter for at aktivere den til at sende pushmeddelelser til GCM.

    ![Google Cloud Console - API-nøgle][5]

##<a id="configure-hub"></a>Konfigurere din meddelelse-hub

[AZURE.INCLUDE [notification-hubs-portal-create-new-hub](../../includes/notification-hubs-portal-create-new-hub.md)]


&emsp;&emsp;6. Vælg **Meddelelse tjenester** og derefter **Google (GCM)**i bladet **Indstillinger** . Angiv API-nøgle, og Gem.

&emsp;&emsp;![Azure meddelelse Hubs - Google (GCM)](./media/notification-hubs-android-get-started/notification-hubs-gcm-api.png)

##<a id="connect-app"></a>Oprette forbindelse din Chrome App til hubben meddelelse

Din meddelelse hub nu er konfigureret til at arbejde med GCM, og du har forbindelse strengene til at registrere din app for at både sende og modtage pushmeddelelser. LK

###<a name="create-a-new-chrome-app"></a>Oprette en ny Chrome-App

Eksemplet nedenfor er baseret på [Chrome App GCM eksempel] og bruger den anbefalede måde at oprette en Chrome App. Vi fremhæver de trin, især er relateret til Azure meddelelse Hubs. 

>[AZURE.NOTE] Vi anbefaler, at du henter kilden for denne Chrome App fra [Chrome App meddelelse Hub eksempel].

Chrome-Appen er oprettet via JavaScript, og du kan bruge en af dine foretrukne word-redaktører til at oprette den. Nedenfor finder du hvordan denne Chrome App ser ud.

![Google Chrome App][15]

1. Oprette en mappe, og kald den `ChromePushApp`. Naturligvis navnet er vilkårlig – Hvis du navngive den noget andet, skal du kontrollere du erstatte stien i kode obligatorisk målgrupper.

2. Hent [kryptering js bibliotek] i den mappe, du oprettede i det andet trin. Biblioteksmappen skal indeholde to undermapper: `components` og `rollups`.

3. Oprette en `manifest.json` fil. Alle Chrome Apps understøttes af en manifestfil, der indeholder app-metadata, og de fleste det vigtigste er, alle tilladelser, der er tildelt til app, når brugeren installerer det.

        {
          "name": "NH-GCM Notifications",
          "description": "Chrome platform app.",
          "manifest_version": 2,
          "version": "0.1",
          "app": {
            "background": {
              "scripts": ["background.js"]
            }
          },
          "permissions": ["gcm", "storage", "notifications", "https://*.servicebus.windows.net/*"],
          "icons": { "128": "gcm_128.png" }
        }

    Meddelelse om den `permissions` element, der angiver, at denne Chrome App vil kunne modtage pushmeddelelser fra GCM. Det skal også angive den Azure meddelelse Hubs URI hvor Chrome App vil gøre et RESTEN opkald til at registrere.
    Vores eksempel app også bruger en ikonfil `gcm_128.png`, som du vil finde på den kilde, bruges igen fra den oprindelige GCM prøve. Du kan erstatte det til et billede, der passer til [ikonet kriterier](https://developer.chrome.com/apps/manifest/icons).

4. Oprette en fil kaldet `background.js` med følgende kode:

        // Returns a new notification ID used in the notification.
        function getNotificationId() {
          var id = Math.floor(Math.random() * 9007199254740992) + 1;
          return id.toString();
        }

        function messageReceived(message) {
          // A message is an object with a data property that
          // consists of key-value pairs.

          // Concatenate all key-value pairs to form a display string.
          var messageString = "";
          for (var key in message.data) {
            if (messageString != "")
              messageString += ", "
            messageString += key + ":" + message.data[key];
          }
          console.log("Message received: " + messageString);

          // Pop up a notification to show the GCM message.
          chrome.notifications.create(getNotificationId(), {
            title: 'GCM Message',
            iconUrl: 'gcm_128.png',
            type: 'basic',
            message: messageString
          }, function() {});
        }

        var registerWindowCreated = false;

        function firstTimeRegistration() {
          chrome.storage.local.get("registered", function(result) {

            registerWindowCreated = true;
            chrome.app.window.create(
              "register.html",
              {  width: 520,
                 height: 500,
                 frame: 'chrome'
              },
              function(appWin) {}
            );
          });
        }

        // Set up a listener for GCM message event.
        chrome.gcm.onMessage.addListener(messageReceived);

        // Set up listeners to trigger the first-time registration.
        chrome.runtime.onInstalled.addListener(firstTimeRegistration);
        chrome.runtime.onStartup.addListener(firstTimeRegistration);

    Dette er den fil, vises vinduet Chrome App HTML (**register.html**) og også definerer handler **messageReceived** for at håndtere indgående opslagsnål meddelelsen.

5. Oprette en fil kaldet `register.html` -dette definerer Brugergrænsefladen af Chrome App. 

   >[AZURE.NOTE] Dette eksempel bruger **CryptoJS v3.1.2**. Hvis du har hentet en anden version af biblioteket, skal du kontrollere du korrekt erstatte versionen i den `src` sti.

        <html>

        <head>
        <title>GCM Registration</title>
        <script src="register.js"></script>
        <script src="CryptoJS v3.1.2/rollups/hmac-sha256.js"></script>
        <script src="CryptoJS v3.1.2/components/enc-base64-min.js"></script>
        </head>

        <body>

        Sender ID:<br/><input id="senderId" type="TEXT" size="20"><br/>
        <button id="registerWithGCM">Register with GCM</button>
        <br/>
        <br/>
        <br/>

        Notification Hub Name:<br/><input id="hubName" type="TEXT" style="width:400px"><br/><br/>
        Connection String:<br/><textarea id="connectionString" type="TEXT" style="width:400px;height:60px"></textarea>

        <br/>

        <button id="registerWithNH" disabled="true">Register with Azure Notification Hubs</button>

        <br/>
        <br/>

        <textarea id="console" type="TEXT" readonly style="width:500px;height:200px;background-color:#e5e5e5;padding:5px"></textarea>

        </body>

        </html>

6. Oprette en fil kaldet `register.js` med koden nedenfor. Denne fil angiver scriptet bag `register.html`. Chrome Apps tillader ikke udførelse af indbygget, så du behøver at oprette en separat sikkerhedskopiering script til din brugergrænseflade.

        var registrationId = "";
        var hubName        = "", connectionString = "";
        var originalUri    = "", targetUri = "", endpoint = "", sasKeyName = "", sasKeyValue = "", sasToken = "";

        window.onload = function() {
           document.getElementById("registerWithGCM").onclick = registerWithGCM;  
           document.getElementById("registerWithNH").onclick = registerWithNH;
           updateLog("You have not registered yet. Please provider sender ID and register with GCM and then with  Notification Hubs.");
        }

        function updateLog(status) {
          currentStatus = document.getElementById("console").innerHTML;
          if (currentStatus != "") {
            currentStatus = currentStatus + "\n\n";
          }

          document.getElementById("console").innerHTML = currentStatus  + status;
        }

        function registerWithGCM() {
          var senderId = document.getElementById("senderId").value.trim();
          chrome.gcm.register([senderId], registerCallback);

          // Prevent register button from being clicked again before the registration finishes.
          document.getElementById("registerWithGCM").disabled = true;
        }

        function registerCallback(regId) {
          registrationId = regId;
          document.getElementById("registerWithGCM").disabled = false;

          if (chrome.runtime.lastError) {
            // When the registration fails, handle the error and retry the
            // registration later.
            updateLog("Registration failed: " + chrome.runtime.lastError.message);
            return;
          }

          updateLog("Registration with GCM succeeded.");
          document.getElementById("registerWithNH").disabled = false;

          // Mark that the first-time registration is done.
          chrome.storage.local.set({registered: true});
        }

        function registerWithNH() {
          hubName = document.getElementById("hubName").value.trim();
          connectionString = document.getElementById("connectionString").value.trim();
          splitConnectionString();
          generateSaSToken();
          sendNHRegistrationRequest();
        }

        // From http://msdn.microsoft.com/library/dn495627.aspx
        function splitConnectionString()
        {
          var parts = connectionString.split(';');
          if (parts.length != 3)
          throw "Error parsing connection string";

          parts.forEach(function(part) {
            if (part.indexOf('Endpoint') == 0) {
            endpoint = 'https' + part.substring(11);
            } else if (part.indexOf('SharedAccessKeyName') == 0) {
            sasKeyName = part.substring(20);
            } else if (part.indexOf('SharedAccessKey') == 0) {
            sasKeyValue = part.substring(16);
            }
          });

          originalUri = endpoint + hubName;
        }

        function generateSaSToken()
        {
          targetUri = encodeURIComponent(originalUri.toLowerCase()).toLowerCase();
          var expiresInMins = 10; // 10 minute expiration

          // Set expiration in seconds.
          var expireOnDate = new Date();
          expireOnDate.setMinutes(expireOnDate.getMinutes() + expiresInMins);
          var expires = Date.UTC(expireOnDate.getUTCFullYear(), expireOnDate
            .getUTCMonth(), expireOnDate.getUTCDate(), expireOnDate
            .getUTCHours(), expireOnDate.getUTCMinutes(), expireOnDate
            .getUTCSeconds()) / 1000;
          var tosign = targetUri + '\n' + expires;

          // Using CryptoJS.
          var signature = CryptoJS.HmacSHA256(tosign, sasKeyValue);
          var base64signature = signature.toString(CryptoJS.enc.Base64);
          var base64UriEncoded = encodeURIComponent(base64signature);

          // Construct authorization string.
          sasToken = "SharedAccessSignature sr=" + targetUri + "&sig="
                          + base64UriEncoded + "&se=" + expires + "&skn=" + sasKeyName;
        }

        function sendNHRegistrationRequest()
        {
          var registrationPayload =
          "<?xml version=\"1.0\" encoding=\"utf-8\"?>" +
          "<entry xmlns=\"http://www.w3.org/2005/Atom\">" +
              "<content type=\"application/xml\">" +
                  "<GcmRegistrationDescription xmlns:i=\"http://www.w3.org/2001/XMLSchema-instance\" xmlns=\"http://schemas.microsoft.com/netservices/2010/10/servicebus/connect\">" +
                      "<GcmRegistrationId>{GCMRegistrationId}</GcmRegistrationId>" +
                  "</GcmRegistrationDescription>" +
              "</content>" +
          "</entry>";

          // Update the payload with the registration ID obtained earlier.
          registrationPayload = registrationPayload.replace("{GCMRegistrationId}", registrationId);

          var url = originalUri + "/registrations/?api-version=2014-09";
          var client = new XMLHttpRequest();

          client.onload = function () {
            if (client.readyState == 4) {
              if (client.status == 200) {
                updateLog("Notification Hub Registration succesful!");
                updateLog(client.responseText);
              } else {
                updateLog("Notification Hub Registration did not succeed!");
                updateLog("HTTP Status: " + client.status + " : " + client.statusText);
                updateLog("HTTP Response: " + "\n" + client.responseText);
              }
            }
          };

          client.onerror = function () {
                updateLog("ERROR - Notification Hub Registration did not succeed!");
          }

          client.open("POST", url, true);
          client.setRequestHeader("Content-Type", "application/atom+xml;type=entry;charset=utf-8");
          client.setRequestHeader("Authorization", sasToken);
          client.setRequestHeader("x-ms-version", "2014-09");

          try {
              client.send(registrationPayload);
          }
          catch(err) {
              updateLog(err.message);
          }
        }

    Ovenstående scriptet har følgende vigtige parametre:
    - **window.OnLoad** definerer knappen, og klik på hændelserne af de to knapper på Brugergrænsefladen. En registrerer med GCM, og den anden bruger den registrering-ID, der returneres efter registrering med GCM til at registrere med Azure meddelelse Hubs.
    - **updateLog** er den funktion, der gør det muligt for os til at håndtere simple logføringsfunktioner.
    - **registerWithGCM** er valgt som første knappen, og klik på, hvilket gør det `chrome.gcm.register` kald til GCM til at registrere den aktuelle forekomst af Chrome App.
    - **registerCallback** er Tilbagekaldsfunktionen, der kaldes, når GCM registrering opkaldet returnerer.
    - **registerWithNH** er den anden knap, og klik på handler, som registrerer med besked om Hubs. Det får `hubName` og `connectionString` (som brugeren har angivet) og crafts meddelelse Hubs registrering REST-API opkaldet.
    - **splitConnectionString** og **generateSaSToken** er hjælpere, der repræsenterer JavaScript implementeringen af en SaS token oprettelse af proces, der skal bruges i alle REST-API-opkald. Du kan finde flere oplysninger under [Almindelige begreber](http://msdn.microsoft.com/library/dn495627.aspx).
    - **sendNHRegistrationRequest** er den funktion, der giver en HTTP RESTEN ringe til Azure meddelelse hubber.
    - **registrationPayload** definerer registrering XML-data. Se [Oprette registrering NH REST-API]kan finde flere oplysninger. Vi opdaterer registrering-ID i det med hvad vi har modtaget fra GCM.
    - **klient** er en forekomst af **XMLHttpRequest** , som vi bruger til at foretage en HTTP POST-anmodning. Bemærk, at vi opdaterer den `Authorization` sidehoved med `sasToken`. Vellykket gennemførelse af dette opkald vil registrere forekomsten Chrome App med Azure meddelelse Hubs.


Den overordnede mappestruktur for dette projekt skal ligne dette:     ![Google Chrome App - mappestrukturen][21]

###<a name="set-up-and-test-your-chrome-app"></a>Konfigurere og teste din Chrome App

1. Åbn browseren Chrome. Åbn **Chrome filtypenavne** og Aktivér **udviklertilstand**.

    ![Google Chrome - Aktivér udviklertilstand][16]

2. Klik på **indlæse udpakkede lokalnummer** , og Naviger til den mappe, hvor du har oprettet filerne. Du kan også du kan også bruge **Chrome Apps og filtypenavne udviklingsværktøjet**. Dette værktøj er en Chrome App i sig selv (installeret fra Chrome Web Store) og indeholder avanceret fejlfinding funktioner til din Chrome App udvikling.

    ![Google Chrome - indlæse udpakkede lokalnummer][17]

3. Hvis Appen Chrome er oprettet uden fejl, får du vist din Chrome App vises.

    ![Google Chrome - Chrome App-visning][18]

4. Angiv **Projektnummer** , du tidligere har fået fra **Google Cloud Console** som ID, afsender, og klik på **Registrer med GCM**. Du skal se meddelelsen **registrering med GCM lykkedes.**

    ![Google Chrome - Chrome App tilpasning][19]

5. Angiv din **Meddelelse Hub navn** og **DefaultListenSharedAccessSignature** , som du har hentet fra portalen tidligere, og klik på **Registrer med Azure meddelelse Hub**. Du skal se meddelelsen **registreringen af Hub vellykket!** og oplysninger om registrering svaret, som indeholder den Azure meddelelse Hubs registrering-id.

    ![Google Chrome - angive oplysninger om Hub][20]  

##<a name="send"></a>Sende en meddelelse til din Chrome App

Til testformål, sender vi Chrome pushmeddelelser ved hjælp af en .NET konsol programmet på computeren. 

>[AZURE.NOTE] Du kan sende pushmeddelelser med besked om Hubs fra en hvilken som helst backend-version via vores offentlige <a href="http://msdn.microsoft.com/library/windowsazure/dn223264.aspx">RESTEN interface</a>. Se vores [dokumentation portal](https://azure.microsoft.com/documentation/services/notification-hubs/) til flere eksempler på tværs af platforme.

1. Vælg **Ny** og derefter **Project**i menuen **filer** i Visual Studio. Klik på **Windows** og **Console programmet**under **Visual C#**, og klik derefter på **OK**.  Dette opretter et nyt console programmet projekt.

2. Klik på **Bibliotek Package Manager** og derefter **Pakke Manager-konsollen**i menuen **Funktioner** . Dette viser pakke Manager-konsollen.

3. I vinduet console skal du udføre følgende kommando:

        Install-Package Microsoft.Azure.NotificationHubs

    Dette indsætter en reference til Azure Service Bus SDK med <a href="http://nuget.org/packages/  WindowsAzure.ServiceBus/">WindowsAzure.ServiceBus NuGet pakke</a>.

4. Åbn `Program.cs` og tilføje følgende `using` sætning:

        using Microsoft.Azure.NotificationHubs;

5. I den `Program` klasse, skal du tilføje følgende metode:

        private static async void SendNotificationAsync()
        {
            NotificationHubClient hub = NotificationHubClient.CreateClientFromConnectionString("<connection string with full access>", "<hub name>");
            String message = "{\"data\":{\"message\":\"Hello Chrome from Azure Notification Hubs\"}}";
            await hub.SendGcmNativeNotificationAsync(message);
        }

    Sørg for at erstatte den `<hub name>` pladsholder med navnet på den meddelelse-hub, der vises i [portalen](https://portal.azure.com) i din meddelelse Hub blade. Erstat også pladsholderen forbindelse streng med forbindelsesstrengen kaldet `DefaultFullSharedAccessSignature` , som du hentede i sektionen meddelelse hub konfiguration.

    >[AZURE.NOTE] Sørg for, at du bruger forbindelsesstrengen med **fuld** adgang, ikke **lytte** adgang. **Lyt** access-forbindelsesstrengen give ikke tilladelser til at sende pushmeddelelser.

5. Tilføj følgende opkald i den `Main` metode:

         SendNotificationAsync();
         Console.ReadLine();
         
6. Sørg for, at Chrome kører, og Kør programmet console.

7. Du bør se nedenstående meddelelse pop op på computeren.

    ![Google Chrome - meddelelse][13]

8. Du kan også få vist alle dine meddelelser ved hjælp af vinduet Chrome beskeder på proceslinjen (i Windows) når Chrome kører.

    ![Google Chrome - beskeder liste][14]

>[AZURE.NOTE] Du behøver ikke at Chrome App kører eller åbne i browseren (selvom selve browseren Chrome skal køre). Du kan også få en samlet visning af alle meddelelser i vinduet Chrome beskeder.

## <a name="next-steps"> </a>Næste trin

Lær mere om meddelelse Hubs i [Meddelelse Hubs oversigt].

Referere til [Azure meddelelse Hubs Giv brugerne] selvstudiet for at målrette bestemte brugere. 

Hvis du vil inddele dine brugere ved interesse grupper, kan du følge selvstudiet [Azure meddelelse Hubs varmeste nyheder] .

<!-- Images. -->
[1]: ./media/notification-hubs-chrome-get-started/GoogleConsoleCreateProject.PNG
[2]: ./media/notification-hubs-chrome-get-started/GoogleProjectNumber.png
[3]: ./media/notification-hubs-chrome-get-started/EnableGCM.png
[4]: ./media/notification-hubs-chrome-get-started/CreateServerKey.png
[5]: ./media/notification-hubs-chrome-get-started/ServerKey.png
[6]: ./media/notification-hubs-chrome-get-started/CreateNH.png
[7]: ./media/notification-hubs-chrome-get-started/NHNamespace.png
[8]: ./media/notification-hubs-chrome-get-started/NamespaceConfigure.png
[9]: ./media/notification-hubs-chrome-get-started/NHConfigure.png
[10]: ./media/notification-hubs-chrome-get-started/NHConfigureGCM.png
[11]: ./media/notification-hubs-chrome-get-started/NHDashboard.png
[12]: ./media/notification-hubs-chrome-get-started/NHConnString.png
[13]: ./media/notification-hubs-chrome-get-started/ChromeNotification.png
[14]: ./media/notification-hubs-chrome-get-started/ChromeNotificationWindow.png
[15]: ./media/notification-hubs-chrome-get-started/ChromeApp.png
[16]: ./media/notification-hubs-chrome-get-started/ChromeExtensions.png
[17]: ./media/notification-hubs-chrome-get-started/ChromeLoadExtension.png
[18]: ./media/notification-hubs-chrome-get-started/ChromeAppLoaded.png
[19]: ./media/notification-hubs-chrome-get-started/ChromeAppGCM.png
[20]: ./media/notification-hubs-chrome-get-started/ChromeAppNH.png
[21]: ./media/notification-hubs-chrome-get-started/FinalFolderView.png

<!-- URLs. -->
[Chrome App meddelelse Hub eksempel]: https://github.com/Azure/azure-notificationhubs-samples/tree/master/PushToChromeApps
[Google Cloud-konsollen]: http://cloud.google.com/console
[Azure Classic Portal]: https://manage.windowsazure.com/
[Meddelelse om Hubs oversigt]: notification-hubs-push-notification-overview.md
[Chrome-Apps oversigt]: https://developer.chrome.com/apps/about_apps
[Chrome App GCM eksempel]: https://github.com/GoogleChrome/chrome-app-samples/tree/master/samples/gcm-notifications
[Installable Web Apps]: https://developers.google.com/chrome/apps/docs/
[Chrome-Apps på Mobile]: https://developer.chrome.com/apps/chrome_apps_on_mobile
[Oprette registrering NH REST-API]: http://msdn.microsoft.com/library/azure/dn223265.aspx
[kryptering js bibliotek]: http://code.google.com/p/crypto-js/
[GCM with Chrome Apps]: https://developer.chrome.com/apps/cloudMessaging
[Google Cloud Messaging for Chrome]: https://developer.chrome.com/apps/cloudMessagingV1
[Azure meddelelse Hubs give brugere besked]: notification-hubs-aspnet-backend-windows-dotnet-wns-notification.md
[Azure meddelelse Hubs seneste nyheder]: notification-hubs-windows-notification-dotnet-push-xplat-segmented-wns.md
