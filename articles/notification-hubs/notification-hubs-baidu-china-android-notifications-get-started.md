<properties
    pageTitle="Introduktion til Azure meddelelse Hubs ved hjælp af Baidu | Microsoft Azure"
    description="I dette selvstudium lærer du at bruge Azure meddelelse Hubs til pushmeddelelser til Android-enheder ved hjælp af Baidu."
    services="notification-hubs"
    documentationCenter="android"
    authors="ysxu"
    manager="dwrede"
    editor=""/>

<tags
    ms.service="notification-hubs"
    ms.devlang="java"
    ms.topic="hero-article"
    ms.tgt_pltfrm="mobile-baidu"
    ms.workload="mobile"
    ms.date="08/19/2016"
    ms.author="yuaxu"/>

# <a name="get-started-with-notification-hubs-using-baidu"></a>Komme i gang med besked om Hubs ved hjælp af Baidu

[AZURE.INCLUDE [notification-hubs-selector-get-started](../../includes/notification-hubs-selector-get-started.md)]

##<a name="overview"></a>Oversigt

Baidu skyen push er en kinesisk skybaseret tjeneste, du kan bruge til at sende pushmeddelelser til mobilenheder. Denne tjeneste er især nyttig i Kina, hvor levere pushmeddelelser på Android er kompleks på grund af tilstedeværelsen af forskellige app gemmer og push-tjenesterne, ud over tilgængeligheden af Android-enheder, der typisk ikke har forbindelse til GCM (Google Cloud Messaging).

##<a name="prerequisites"></a>Forudsætninger

Dette selvstudium kræver følgende:

+ Android SDK (vi antager, at du vil bruge Eklipse), som du kan hente fra <a href="http://go.microsoft.com/fwlink/?LinkId=389797">Android websted</a>
+ [Mobile-tjenester Android SDK]
+ [Baidu opslagsnål Android SDK]

>[AZURE.NOTE] For at fuldføre dette selvstudium skal skal du have en active Azure-konto. Hvis du ikke har en konto, kan du oprette en gratis prøveversion konto på blot et par minutter. Yderligere oplysninger finder du [Azure gratis prøveversion](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Fen-us%2Fdocumentation%2Farticles%2Fnotification-hubs-baidu-get-started%2F).


##<a name="create-a-baidu-account"></a>Oprette en Baidu-konto

Hvis du vil bruge Baidu, skal du have en Baidu konto. Hvis du allerede har et, log på [Baidu portal] og gå videre til næste trin. Ellers kan se nedenstående vejledning om, hvordan du opretter en Baidu-konto.  

1. Gå til [Baidu portal] , og klik på linket**登录**(**Login**). Klik på**立即注册**for at starte registreringsprocessen konto.

    ![][1]

2. Angiv de nødvendige oplysninger – telefon/e-mail-adresse, adgangskode og bekræftelse kode – og klik på **tilmelding**.

    ![][2]

3. Du skal sendes en mail til den mailadresse, du har angivet med et link til at aktivere kontoen Baidu.

    ![][3]

4. Log på din mailkonto, Åbn Baidu aktivering e-mailen, og klik på aktiveringslinket for at aktivere kontoen Baidu.

    ![][4]

Når du har en aktiveret Baidu-konto, skal du logge på [Baidu portal].

##<a name="register-as-a-baidu-developer"></a>Registrere som en Baidu udvikler

1. Når du har logget på [Baidu portal], skal du klikke på**更多 >>** (**mere**).

    ![][5]

2. Rul ned i sektionen**站长与开发者服务 (Webmasterværktøjer og Developer Services)** , og klik på**百度开放云平台**(**Baidu åbne skyen platform**).

    ![][6]

3. Klik på**开发者服务**(**Developer Services**) i øverste højre hjørne på den næste side.

    ![][7]

4. Klik på**注册开发者**(**Registreret udviklere**) fra menuen i øverste højre hjørne på den næste side.

    ![][8]

5. Angiv dit navn, en beskrivelse og et mobiltelefonnummer til at modtage en bekræftelse SMS-besked, og klik derefter på**送验证码**(**Sende Verifikationskoden**). Bemærk, at i udenlandske telefonnumre, skal du skrive landekoden i parenteser. For eksempel for et tal i USA, vil det være **(1) 1234567890**.

    ![][9]

6. Derefter skal du modtager en SMS-besked med en bekræftelse tal, som vist i følgende eksempel:

    ![][10]

7. Angiv antallet bekræftelse fra meddelelsen i**验证码**(**bekræftelseskode**).

8. Til sidst skal fuldføre udvikler registreringen ved at acceptere Baidu aftalen og klikke på**提交**(**Send**). Du får vist den efterfølgende side på vellykket gennemførelse af registrering:

    ![][11]

##<a name="create-a-baidu-cloud-push-project"></a>Oprette et Baidu skyen push-projekt

Når du opretter et Baidu skyen push-projekt, du modtager din app-ID API-nøgle og hemmeligt nøgle.

1. Når du har logget på [Baidu portal], skal du klikke på**更多 >>** (**mere**).

    ![][5]

2. Rul ned i sektionen**站长与开发者服务**(**Webmasterværktøjer og Developer Services**), og klik på**百度开放云平台**(**Baidu åbne skyen platform**).

    ![][6]

3. Klik på**开发者服务**(**Developer Services**) i øverste højre hjørne på den næste side.

    ![][7]

4. Klik på**云推送**(**Skyen Push**) i afsnittet**云服务**(**Cloud Services**) på den næste side.

    ![][12]

5. Når du er registreret udvikler, får du vist**管理控制台**(**Management Console**) på den øverste menu. Klik på**开发者服务管理**(**udviklere Service Management**).

    ![][13]

6. På den næste side skal du klikke på**创建工程**(**Opret projekt**).

    ![][14]

7. Skriv navnet på et program, og klik på**创建**(**Opret**).

    ![][15]

8. Du får vist en side med **sikkerhedskontrol**, **API-nøgle**og **Hemmeligt nøgle**efter korrekt oprettelse af et Baidu skyen push-projekt. Skal du notere den API-nøgle og hemmeligt nøgle, som vi skal bruge senere.

    ![][16]

9. Konfigurere projektet til pushmeddelelser ved at klikke på**云推送**(**Skyen Push**) i venstre rude.

    ![][31]

10. Klik på knappen**推送设置**(**Push-indstillinger**) på den næste side.

    ![][32]  

11. Tilføj navnet på pakken, du vil være bruger i projektet Android i feltet**应用包名**(**programpakke**), og klik derefter på**保存设置**(**Gem**) på konfigurationssiden.  

    ![][33]

Du får vist den**保存成功!** (**blev gemt!**) meddelelse.

##<a name="configure-your-notification-hub"></a>Konfigurere din meddelelse hub

1. Log på [Klassisk Azure-portalen], og klik derefter på **+ Ny** nederst på skærmen.

2. Klik på **App-tjenester**, skal du klikke på **Service Bus**, skal du klikke på **Meddelelse-Hub**, og klik **Hurtig oprettelse**.

3. Angiv et navn til din **Meddelelse-Hub**, skal du markere det **område** og **Namespace** , hvor denne meddelelse-hub skal oprettes, og klik derefter på **Opret en ny meddelelse-Hub**.  

    ![][17]

4. Klik på det navneområde, hvor du har oprettet din meddelelse-hub, og klik derefter på **Meddelelse Hubs** øverst.

    ![][18]

5. Vælg den meddelelse-hub, du har oprettet, og klik derefter på **Konfigurer** fra den øverste menu.

    ![][19]

6. Rul ned til sektionen **baidu indstillinger for meddelelser** , og Angiv den API-nøgle og hemmeligt nøgle, som du hentede fra konsollen Baidu tidligere for projektet Baidu skyen opslagsnål. Klik på **Gem**.

    ![][20]

7. Klik på fanen **Dashboard** øverst for meddelelse-hub, og klik derefter på **Vis forbindelsesstreng**.

    ![][21]

8. Skal du notere **DefaultListenSharedAccessSignature** og **DefaultFullSharedAccessSignature** fra vinduet **Access forbindelsesoplysninger** .

    ![][22]

##<a name="connect-your-app-to-the-notification-hub"></a>Oprette forbindelse din app til hubben meddelelse

1. Oprette et nyt Android projekt i Eklipse ADT (**fil** > **Ny** > **Android programmet Project**).

    ![][23]

2. Indtast et **Navn** , og Sørg for, at **Minimum påkrævet SDK** versionen er indstillet til **API 16: Android 4.1**.

    ![][24]

3. Klik på **Næste** , og fortsæt efter guiden, indtil vinduet **Opret aktivitet** vises. Sørg for, at **Tomme aktivitet** er markeret, og til sidst Vælg **Udfør** for at oprette et nyt Android program.

    ![][25]

4. Sørg for, at **Project opbygge mål** er indstillet korrekt.

    ![][26]

5. Hent filen meddelelse-hubs-0.4.jar under fanen **filer** i [Meddelelse-Hubs-Android-SDK på Bintray](https://bintray.com/microsoftazuremobile/SDK/Notification-Hubs-Android-SDK/0.4). Tilføje filen til mappen **libs** for projektet Eklipse, og Opdater mappen *libs* .

6. Hente og udpakke [Baidu Push Android SDK], Åbn mappen **libs** og derefter kopiere filen **pushservice x.y.z** glas og **armeabi** & **mips** mapper i mappen **libs** på din Android-program.

7. Åbn filen **AndroidManifest.xml** af projektet Android, og Tilføj de tilladelser, der kræves af Baidu SDK.

        <uses-permission android:name="android.permission.INTERNET" />
        <uses-permission android:name="android.permission.READ_PHONE_STATE" />
        <uses-permission android:name="android.permission.ACCESS_NETWORK_STATE" />
        <uses-permission android:name="android.permission.RECEIVE_BOOT_COMPLETED" />
        <uses-permission android:name="android.permission.WRITE_SETTINGS" />
        <uses-permission android:name="android.permission.VIBRATE" />
        <uses-permission android:name="android.permission.WRITE_EXTERNAL_STORAGE" />
        <uses-permission android:name="android.permission.DISABLE_KEYGUARD" />
        <uses-permission android:name="android.permission.ACCESS_COARSE_LOCATION" />
        <uses-permission android:name="android.permission.ACCESS_WIFI_STATE" />
        <uses-permission android:name="android.permission.ACCESS_DOWNLOAD_MANAGER" />
        <uses-permission android:name="android.permission.DOWNLOAD_WITHOUT_NOTIFICATION" />

8. Føje egenskaben **android: navn** til dit **program** element i **AndroidManifest.xml**, erstatter *yourprojectname* (f.eks., **com.example.BaiduTest**). Sørg for, at denne projektnavn svarer til den effekt, du har konfigureret i konsollen Baidu.

        <application android:name="yourprojectname.DemoApplication"

9. Tilføj følgende konfiguration i elementet programmet efter **. MainActivity** aktivitet element, erstatte *yourprojectname* (f.eks., **com.example.BaiduTest**):

        <receiver android:name="yourprojectname.MyPushMessageReceiver">
            <intent-filter>
                <action android:name="com.baidu.android.pushservice.action.MESSAGE" />
                <action android:name="com.baidu.android.pushservice.action.RECEIVE" />
                <action android:name="com.baidu.android.pushservice.action.notification.CLICK" />
            </intent-filter>
        </receiver>

        <receiver android:name="com.baidu.android.pushservice.PushServiceReceiver"
            android:process=":bdservice_v1">
            <intent-filter>
                <action android:name="android.intent.action.BOOT_COMPLETED" />
                <action android:name="android.net.conn.CONNECTIVITY_CHANGE" />
                <action android:name="com.baidu.android.pushservice.action.notification.SHOW" />
            </intent-filter>
        </receiver>

        <receiver android:name="com.baidu.android.pushservice.RegistrationReceiver"
            android:process=":bdservice_v1">
            <intent-filter>
                <action android:name="com.baidu.android.pushservice.action.METHOD" />
                <action android:name="com.baidu.android.pushservice.action.BIND_SYNC" />
            </intent-filter>
            <intent-filter>
                <action android:name="android.intent.action.PACKAGE_REMOVED"/>
                <data android:scheme="package" />
            </intent-filter>
        </receiver>

        <service
            android:name="com.baidu.android.pushservice.PushService"
            android:exported="true"
            android:process=":bdservice_v1"  >
            <intent-filter>
                <action android:name="com.baidu.android.pushservice.action.PUSH_SERVICE" />
            </intent-filter>
        </service>

9. Tilføje en ny klasse, kaldet **ConfigurationSettings.java** til projektet.

    ![][28]

    ![][29]

10. Føj følgende kode til den:

        public class ConfigurationSettings {
                public static String API_KEY = "...";
                public static String NotificationHubName = "...";
                public static String NotificationHubConnectionString = "...";
            }

    Angive værdien af **API_KEY** med hvad du har hentet fra Baidu skyen projektet tidligere, **NotificationHubName** med navnet på din meddelelse hub fra portalen Azure klassisk og **NotificationHubConnectionString** med DefaultListenSharedAccessSignature fra portalen Azure klassisk.

11. Tilføje en ny klasse, kaldet **DemoApplication.java**, og Føj følgende kode til den:

        import com.baidu.frontia.FrontiaApplication;

        public class DemoApplication extends FrontiaApplication {
            @Override
            public void onCreate() {
                super.onCreate();
            }
        }

12. Tilføje en anden nye klasse kaldet **MyPushMessageReceiver.java**, og Tilføj koden nedenfor til den. Dette er den klasse, der håndterer pushmeddelelser, der modtages fra Baidu push-serveren.

        import java.util.List;
        import android.content.Context;
        import android.os.AsyncTask;
        import android.util.Log;
        import com.baidu.frontia.api.FrontiaPushMessageReceiver;
        import com.microsoft.windowsazure.messaging.NotificationHub;

        public class MyPushMessageReceiver extends FrontiaPushMessageReceiver {
            /** TAG to Log */
            public static NotificationHub hub = null;
            public static String mChannelId, mUserId;
            public static final String TAG = MyPushMessageReceiver.class
                    .getSimpleName();

            @Override
            public void onBind(Context context, int errorCode, String appid,
                    String userId, String channelId, String requestId) {
                String responseString = "onBind errorCode=" + errorCode + " appid="
                        + appid + " userId=" + userId + " channelId=" + channelId
                        + " requestId=" + requestId;
                Log.d(TAG, responseString);
                mChannelId = channelId;
                mUserId = userId;

                try {
                 if (hub == null) {
                        hub = new NotificationHub(
                                ConfigurationSettings.NotificationHubName,
                                ConfigurationSettings.NotificationHubConnectionString,
                                context);
                        Log.i(TAG, "Notification hub initialized");
                    }
                } catch (Exception e) {
                   Log.e(TAG, e.getMessage());
                }

                registerWithNotificationHubs();
            }

            private void registerWithNotificationHubs() {
               new AsyncTask<Void, Void, Void>() {
                  @Override
                  protected Void doInBackground(Void... params) {
                     try {
                         hub.registerBaidu(mUserId, mChannelId);
                         Log.i(TAG, "Registered with Notification Hub - '"
                                + ConfigurationSettings.NotificationHubName + "'"
                                + " with UserId - '"
                                + mUserId + "' and Channel Id - '"
                                + mChannelId + "'");
                     } catch (Exception e) {
                         Log.e(TAG, e.getMessage());
                     }
                     return null;
                 }
               }.execute(null, null, null);
            }

            @Override
            public void onSetTags(Context context, int errorCode,
                    List<String> sucessTags, List<String> failTags, String requestId) {
                String responseString = "onSetTags errorCode=" + errorCode
                        + " sucessTags=" + sucessTags + " failTags=" + failTags
                        + " requestId=" + requestId;
                Log.d(TAG, responseString);
            }

            @Override
            public void onDelTags(Context context, int errorCode,
                    List<String> sucessTags, List<String> failTags, String requestId) {
                String responseString = "onDelTags errorCode=" + errorCode
                        + " sucessTags=" + sucessTags + " failTags=" + failTags
                        + " requestId=" + requestId;
                Log.d(TAG, responseString);
            }

            @Override
            public void onListTags(Context context, int errorCode, List<String> tags,
                    String requestId) {
                String responseString = "onListTags errorCode=" + errorCode + " tags="
                        + tags;
                Log.d(TAG, responseString);
            }

            @Override
            public void onUnbind(Context context, int errorCode, String requestId) {
                String responseString = "onUnbind errorCode=" + errorCode
                        + " requestId = " + requestId;
                Log.d(TAG, responseString);
            }

            @Override
            public void onNotificationClicked(Context context, String title,
                    String description, String customContentString) {
                String notifyString = "title=\"" + title + "\" description=\""
                        + description + "\" customContent=" + customContentString;
                Log.d(TAG, notifyString);
            }

            @Override
            public void onMessage(Context context, String message,
                    String customContentString) {
                String messageString = "message=\"" + message + "\" customContentString=" + customContentString;
                Log.d(TAG, messageString);
            }
        }

13. Åbn **MainActivity.java**, og Tilføj følgende metoden **onCreate** :

            PushManager.startWork(getApplicationContext(),
                    PushConstants.LOGIN_TYPE_API_KEY, ConfigurationSettings.API_KEY);

14. Åbn følgende Importér udtalelser øverst:

            import com.baidu.android.pushservice.PushConstants;
            import com.baidu.android.pushservice.PushManager;

##<a name="send-notifications-to-your-app"></a>Sende meddelelser til din app


Du kan hurtigt teste modtage beskeder i din app ved at sende meddelelser i [Azure-portalen](https://portal.azure.com/) ved hjælp af knappen **Test Send** på hubben meddelelse, som vist i skærmbilledet nedenfor.

![](./media/notification-hubs-windows-store-dotnet-get-started/notification-hub-test-send-wns.png)

Pushmeddelelser sendes normalt i en back end-tjeneste som Mobile-tjenester eller ASP.NET ved hjælp af et kompatibelt bibliotek. Du kan også bruge REST-API direkte til at sende meddelelser, hvis et bibliotek ikke er tilgængelig for din back end.

I dette selvstudium skal vi Hold det enkelt og kun viser test din klient-app ved at sende meddelelser ved hjælp af .NET SDK for meddelelse hubs i et console-program i stedet for en back end-tjeneste. Vi anbefaler selvstudiet [Brug meddelelse Hubs til pushmeddelelser til brugere](notification-hubs-aspnet-backend-windows-dotnet-wns-notification.md) , som det næste trin for at sende meddelelser fra en ASP.NET backend-version. Dog kan benytte følgende fremgangsmåde bruges til at sende meddelelser:

* **RESTEN Interface**: Du kan understøtte meddelelse på en hvilken som helst back end-platform ved hjælp af [RESTEN interface](http://msdn.microsoft.com/library/windowsazure/dn223264.aspx).

* **Microsoft Azure meddelelse Hubs .NET SDK**: I feltet Nuget pakke Manager til Visual Studio, skal du køre [Installer-pakke Microsoft.Azure.NotificationHubs](https://www.nuget.org/packages/Microsoft.Azure.NotificationHubs/).

* **Node.js** : [Sådan bruger du besked om Hubs fra Node.js](notification-hubs-nodejs-push-notification-tutorial.md).

* **Mobile-Apps**: se et eksempel på, hvordan du sender meddelelser fra en back-end til Azure App Service Mobile-Apps, der er integreret med besked om Hubs under [Tilføj pushmeddelelser til din mobile-app](../app-service-mobile/app-service-mobile-windows-store-dotnet-get-started-push.md).

* **Java / PHP**: et eksempel på hvordan du kan sende meddelelser ved hjælp af REST API'er, se "Sådan bruger du besked om Hubs fra Java/PHP" ([Java](notification-hubs-java-push-notification-tutorial.md) | [PHP](notification-hubs-php-push-notification-tutorial.md)).

##<a name="optional-send-notifications-from-a-net-console-app"></a>(Valgfrit) Sende beskeder fra en .NET console-app.

I dette afsnit viser vi sender en meddelelse ved hjælp af en .NET console-app.

1. Oprette et nyt Visual C# console program:

    ![][30]

2. Angiv den **standard project** til din nye console programmet projekt i vinduet pakke Manager-konsollen, og derefter i vinduet console skal du udføre følgende kommando:

        Install-Package Microsoft.Azure.NotificationHubs

    Dette indsætter en reference til Azure meddelelse Hubs SDK ved hjælp af <a href="http://www.nuget.org/packages/Microsoft.Azure.NotificationHubs/">Microsoft.Azure.Notification Hubs NuGet pakke</a>.

    ![](./media/notification-hubs-windows-store-dotnet-get-started/notification-hub-package-manager.png)

3. Åbn filen **Program.cs** og tilføje følgende ved hjælp af sætning:

        using Microsoft.Azure.NotificationHubs;

4. I din `Program` klasse, tilføje følgende metode og erstatte *DefaultFullSharedAccessSignatureSASConnectionString* og *NotificationHubName* med de værdier, som du har.

        private static async void SendNotificationAsync()
        {
            NotificationHubClient hub = NotificationHubClient.CreateClientFromConnectionString("DefaultFullSharedAccessSignatureSASConnectionString", "NotificationHubName");
            string message = "{\"title\":\"((Notification title))\",\"description\":\"Hello from Azure\"}";
            var result = await hub.SendBaiduNativeNotificationAsync(message);
        }

5. Tilføj følgende linjer i din **Main** metode:

         SendNotificationAsync();
         Console.ReadLine();

##<a name="test-your-app"></a>Teste din app

Hvis du vil teste denne app med en faktisk telefon, lige Tilslut telefonen til computeren ved hjælp af et USB-kabel. Dette indlæser din app til vedhæftede telefonen.

Klik på **Kør**for at teste denne app med emulatoren på værktøjslinjen Eklipse øverst, og vælg derefter din app. Dette starter emulatoren, og derefter indlæses og køres appen.

Appen henter 'bruger-id' og 'channelId' fra Baidu pushmeddelelsestjeneste og registrerer med besked om hub.

Hvis du vil sende en meddelelse om test kan du bruge fanen fejlfinding i portalen Azure klassisk. Hvis du har oprettet .NET console-programmet til Visual Studio, blot trykke på tasten F5 i Visual Studio til at køre programmet. Programmet sender en meddelelse, der skal vises i øverste meddelelsesområdet på enheden eller emulator.


<!-- Images. -->
[1]: ./media/notification-hubs-baidu-get-started/BaiduRegistration.png
[2]: ./media/notification-hubs-baidu-get-started/BaiduRegistrationInput.png
[3]: ./media/notification-hubs-baidu-get-started/BaiduConfirmation.png
[4]: ./media/notification-hubs-baidu-get-started/BaiduActivationEmail.png
[5]: ./media/notification-hubs-baidu-get-started/BaiduRegistrationMore.png
[6]: ./media/notification-hubs-baidu-get-started/BaiduOpenCloudPlatform.png
[7]: ./media/notification-hubs-baidu-get-started/BaiduDeveloperServices.png
[8]: ./media/notification-hubs-baidu-get-started/BaiduDeveloperRegistration.png
[9]: ./media/notification-hubs-baidu-get-started/BaiduDevRegistrationInput.png
[10]: ./media/notification-hubs-baidu-get-started/BaiduDevRegistrationConfirmation.png
[11]: ./media/notification-hubs-baidu-get-started/BaiduDevConfirmationFinal.png
[12]: ./media/notification-hubs-baidu-get-started/BaiduCloudPush.png
[13]: ./media/notification-hubs-baidu-get-started/BaiduDevSvcMgmt.png
[14]: ./media/notification-hubs-baidu-get-started/BaiduCreateProject.png
[15]: ./media/notification-hubs-baidu-get-started/BaiduCreateProjectInput.png
[16]: ./media/notification-hubs-baidu-get-started/BaiduProjectKeys.png
[17]: ./media/notification-hubs-baidu-get-started/AzureNHCreation.png
[18]: ./media/notification-hubs-baidu-get-started/NotificationHubs.png
[19]: ./media/notification-hubs-baidu-get-started/NotificationHubsConfigure.png
[20]: ./media/notification-hubs-baidu-get-started/NotificationHubBaiduConfigure.png
[21]: ./media/notification-hubs-baidu-get-started/NotificationHubsConnectionStringView.png
[22]: ./media/notification-hubs-baidu-get-started/NotificationHubsConnectionString.png
[23]: ./media/notification-hubs-baidu-get-started/EclipseNewProject.png
[24]: ./media/notification-hubs-baidu-get-started/EclipseProjectCreation.png
[25]: ./media/notification-hubs-baidu-get-started/EclipseBlankActivity.png
[26]: ./media/notification-hubs-baidu-get-started/EclipseProjectBuildProperty.png
[27]: ./media/notification-hubs-baidu-get-started/EclipseBaiduReferences.png
[28]: ./media/notification-hubs-baidu-get-started/EclipseNewClass.png
[29]: ./media/notification-hubs-baidu-get-started/EclipseConfigSettingsClass.png
[30]: ./media/notification-hubs-baidu-get-started/ConsoleProject.png
[31]: ./media/notification-hubs-baidu-get-started/BaiduPushConfig1.png
[32]: ./media/notification-hubs-baidu-get-started/BaiduPushConfig2.png
[33]: ./media/notification-hubs-baidu-get-started/BaiduPushConfig3.png

<!-- URLs. -->
[Mobile-tjenester Android SDK]: https://go.microsoft.com/fwLink/?LinkID=280126&clcid=0x409
[Baidu opslagsnål Android SDK]: http://developer.baidu.com/wiki/index.php?title=docs/cplat/push/sdk/clientsdk
[Azure klassisk Portal]: https://manage.windowsazure.com/
[Baidu portal]: http://www.baidu.com/
