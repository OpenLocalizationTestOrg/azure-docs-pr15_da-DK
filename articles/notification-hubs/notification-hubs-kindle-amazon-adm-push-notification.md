<properties
    pageTitle="Introduktion til Azure meddelelse Hubs til Kindle apps | Microsoft Azure"
    description="I dette selvstudium lærer du at bruge Azure meddelelse Hubs til at sende pushmeddelelser til et Kindle program."
    services="notification-hubs"
    documentationCenter=""
    authors="ysxu"
    manager="erikre"
    editor=""/>

<tags
    ms.service="notification-hubs"
    ms.workload="mobile"
    ms.tgt_pltfrm="mobile-kindle"
    ms.devlang="Java"
    ms.topic="hero-article"
    ms.date="06/29/2016"
    ms.author="yuaxu"/>

# <a name="get-started-with-notification-hubs-for-kindle-apps"></a>Komme i gang med besked om Hubs til Kindle apps

[AZURE.INCLUDE [notification-hubs-selector-get-started](../../includes/notification-hubs-selector-get-started.md)]

##<a name="overview"></a>Oversigt

Dette selvstudium viser, hvordan du bruger Azure meddelelse Hubs til at sende pushmeddelelser til et Kindle program.
Du vil oprette en tom Kindle app, der modtager pushmeddelelser ved hjælp af Amazon enhed Messaging (ADM).

##<a name="prerequisites"></a>Forudsætninger

Dette selvstudium kræver følgende:

+ Få Android SDK (vi antager, at du vil bruge Eklipse) fra <a href="http://go.microsoft.com/fwlink/?LinkId=389797">Android websted</a>.
+ Følg trinnene i <a href="https://developer.amazon.com/appsandservices/resources/development-tools/ide-tools/tech-docs/01-setting-up-your-development-environment">Indstilling af dine udviklingsmiljø</a> til at konfigurere dit udviklingsmiljø til Kindle.

##<a name="add-a-new-app-to-the-developer-portal"></a>Tilføje en ny app til portalen udvikler

1. Opret først en app på [Amazon udvikler portal].

    ![][0]

2. Kopiere den **programtasten**.

    ![][1]

3. Klik på navnet på din app i portalen, og klik derefter på fanen **Enhed Messaging** .

    ![][2]

4. Klik på **Opret en ny sikkerhedsprofil**, og derefter oprette en ny sikkerhedsprofil (for eksempel **TestAdm sikkerhedsprofil**). Klik derefter på **Gem**.

    ![][3]

5. Klik på **Sikkerhedsprofiler** for at få vist den sikkerhedsprofil, som du lige har oprettet. Kopiere **Klient-ID** og **Klienten hemmeligt** værdierne til senere brug.

    ![][4]

## <a name="create-an-api-key"></a>Oprette en API-nøgle

1. Åbn en kommandoprompt med administratorrettigheder.
2. Gå til mappen Android SDK.
3. Skriv følgende kommando:

        keytool -list -v -alias androiddebugkey -keystore ./debug.keystore

    ![][5]

4.  Skriv **android**for adgangskode **keystore** .

5.  Kopiere **MD5** fingeraftrykket.
6.  Klik på **Android/Kindle** tilbage i portalen udvikler, under fanen **meddelelser** , og skriv navnet på pakken til din app (for eksempel **com.sample.notificationhubtest**) og værdien **MD5** , og klik **Generer API-nøgle**.

## <a name="add-credentials-to-the-hub"></a>Tilføj legitimationsoplysninger til hub

Tilføj klient hemmeligt og klient-ID til fanen **konfiguration** af din meddelelse hub i portalen.

## <a name="set-up-your-application"></a>Konfigurere dit program

> [AZURE.NOTE] Når du opretter et program, skal du bruge mindst API niveau 17.

Føje ADM-biblioteker til projektet Eklipse:

1. For at hente biblioteket ADM, [hente SDK]. Udtrække SDK zip-filen.
2. Højreklik på dit projekt i Eklipse, og klik derefter på **Egenskaber**. Vælg **Java opbygge sti** til venstre, og vælg derefter fanen **biblioteker **øverst. Klik på **Tilføj eksterne Jar**, og Vælg fil, `\SDK\Android\DeviceMessaging\lib\amazon-device-messaging-*.jar` fra den mappe, hvor du har pakket Amazon SDK.
3. Hent NotificationHubs Android SDK (link).
4. Udpakke pakken, og træk derefter filen `notification-hubs-sdk.jar` til den `libs` mappe i Eklipse.

Redigere din app manifest for at understøtte ADM:

1. Tilføje navneområdet Amazon i manifest rodelementet:


        xmlns:amazon="http://schemas.amazon.com/apk/res/android"

2. Føj tilladelser som det første element under manifest elementet. Du kan erstatte **[Navn på din pakke]** med den pakke, som du brugte til at oprette din app.

        <permission
         android:name="[YOUR PACKAGE NAME].permission.RECEIVE_ADM_MESSAGE"
         android:protectionLevel="signature" />

        <uses-permission android:name="android.permission.INTERNET"/>

        <uses-permission android:name="[YOUR PACKAGE NAME].permission.RECEIVE_ADM_MESSAGE" />

        <!-- This permission allows your app access to receive push notifications
        from ADM. -->
        <uses-permission android:name="com.amazon.device.messaging.permission.RECEIVE" />

        <!-- ADM uses WAKE_LOCK to keep the processor from sleeping when a message is received. -->
        <uses-permission android:name="android.permission.WAKE_LOCK" />

3. Indsæt følgende element som det første underordnede af elementet programmet på computeren. Husk at erstatte **[Navn på din tjeneste]** med navnet på din ADM meddelelseshandler, som du opretter i næste afsnit (herunder pakken), og Erstat **[Navn på din pakke]** med navnet på pakken, som du har oprettet din app.

        <amazon:enable-feature
              android:name="com.amazon.device.messaging"
                     android:required="true"/>
        <service
            android:name="[YOUR SERVICE NAME]"
            android:exported="false" />

        <receiver
            android:name="[YOUR SERVICE NAME]$Receiver" />

            <!-- This permission ensures that only ADM can send your app registration broadcasts. -->
            android:permission="com.amazon.device.messaging.permission.SEND" >

            <!-- To interact with ADM, your app must listen for the following intents. -->
            <intent-filter>
          <action android:name="com.amazon.device.messaging.intent.REGISTRATION" />
          <action android:name="com.amazon.device.messaging.intent.RECEIVE" />

          <!-- Replace the name in the category tag with your app's package name. -->
          <category android:name="[YOUR PACKAGE NAME]" />
            </intent-filter>
        </receiver>

## <a name="create-your-adm-message-handler"></a>Oprette din ADM meddelelseshandler

1. Oprette en ny klasse, der nedarver fra `com.amazon.device.messaging.ADMMessageHandlerBase` , og kald den `MyADMMessageHandler`, som vist i følgende figur:

    ![][6]

2. Tilføj følgende `import` sætninger:

        import android.app.NotificationManager;
        import android.app.PendingIntent;
        import android.content.Context;
        import android.content.Intent;
        import android.support.v4.app.NotificationCompat;
        import com.amazon.device.messaging.ADMMessageReceiver;
        import com.microsoft.windowsazure.messaging.NotificationHub

3. Tilføj følgende kode i klassen, som du har oprettet. Husk at erstatte den hub navn og forbindelse streng (lyt):

        public static final int NOTIFICATION_ID = 1;
        private NotificationManager mNotificationManager;
        NotificationCompat.Builder builder;
        private static NotificationHub hub;
        public static NotificationHub getNotificationHub(Context context) {
            Log.v("com.wa.hellokindlefire", "getNotificationHub");
            if (hub == null) {
                hub = new NotificationHub("[hub name]", "[listen connection string]", context);
            }
            return hub;
        }

        public MyADMMessageHandler() {
                super("MyADMMessageHandler");
            }

            public static class Receiver extends ADMMessageReceiver
            {
                public Receiver()
                {
                    super(MyADMMessageHandler.class);
                }
            }

            private void sendNotification(String msg) {
                Context ctx = getApplicationContext();

             mNotificationManager = (NotificationManager)
                    ctx.getSystemService(Context.NOTIFICATION_SERVICE);

            PendingIntent contentIntent = PendingIntent.getActivity(ctx, 0,
                new Intent(ctx, MainActivity.class), 0);

            NotificationCompat.Builder mBuilder =
                new NotificationCompat.Builder(ctx)
                .setSmallIcon(R.mipmap.ic_launcher)
                .setContentTitle("Notification Hub Demo")
                .setStyle(new NotificationCompat.BigTextStyle()
                         .bigText(msg))
                .setContentText(msg);

            mBuilder.setContentIntent(contentIntent);
            mNotificationManager.notify(NOTIFICATION_ID, mBuilder.build());
        }

4. Tilføj følgende kode i den `OnMessage()` metode:

        String nhMessage = intent.getExtras().getString("msg");
        sendNotification(nhMessage);

5. Tilføj følgende kode i den `OnRegistered` metode:

            try {
        getNotificationHub(getApplicationContext()).register(registrationId);
            } catch (Exception e) {
        Log.e("[your package name]", "Fail onRegister: " + e.getMessage(), e);
            }

6.  Tilføj følgende kode i den `OnUnregistered` metode:

            try {
                getNotificationHub(getApplicationContext()).unregister();
            } catch (Exception e) {
                Log.e("[your package name]", "Fail onUnregister: " + e.getMessage(), e);
            }

7. I den `MainActivity` metode, tilføje følgende importsætning:

        import com.amazon.device.messaging.ADM;

8. Føj følgende kode i slutningen af den `OnCreate` metode:

        final ADM adm = new ADM(this);
        if (adm.getRegistrationId() == null)
        {
           adm.startRegister();
        } else {
            new AsyncTask() {
                  @Override
                  protected Object doInBackground(Object... params) {
                     try {                       MyADMMessageHandler.getNotificationHub(getApplicationContext()).register(adm.getRegistrationId());
                     } catch (Exception e) {
                         Log.e("com.wa.hellokindlefire", "Failed registration with hub", e);
                         return e;
                     }
                     return null;
                 }
               }.execute(null, null, null);
        }

## <a name="add-your-api-key-to-your-app"></a>Føje din API-nøgle til din app

1. Oprette en ny fil med navnet **api_key.txt** i dit projekt directory aktiver i Eklipse.
2. Åbn fil, og Kopiér den API-nøgle, som du har oprettet i portalen Amazon udvikler.

## <a name="run-the-app"></a>Køre appen

1. Start emulatoren.
2. I emulatoren, stryge fra toppen og klik på **Indstillinger**, og klik derefter på **Min konto** og registrere med en gyldig Amazon-konto.
3. Køre appen i Eklipse.

> [AZURE.NOTE] Hvis der opstår et problem, kan du se tidspunktet for emulator (eller enhed). Tidsværdien skal være nøjagtige. Hvis du vil ændre klokkeslættet for emulatoren Kindle, kan du køre følgende kommando fra adresselisten Android SDK platform værktøjer:

        adb shell  date -s "yyyymmdd.hhmmss"

## <a name="send-a-message"></a>Sende en meddelelse

Sådan sendes en meddelelse ved hjælp af .NET:

        static void Main(string[] args)
        {
            NotificationHubClient hub = NotificationHubClient.CreateClientFromConnectionString("[conn string]", "[hub name]");

            hub.SendAdmNativeNotificationAsync("{\"data\":{\"msg\" : \"Hello from .NET!\"}}").Wait();
        }

![][7]

<!-- URLs. -->
[Amazon udvikler portal]: https://developer.amazon.com/home.html
[hente SDK]: https://developer.amazon.com/public/resources/development-tools/sdk

[0]: ./media/notification-hubs-kindle-get-started/notification-hub-kindle-portal1.png
[1]: ./media/notification-hubs-kindle-get-started/notification-hub-kindle-portal2.png
[2]: ./media/notification-hubs-kindle-get-started/notification-hub-kindle-portal3.png
[3]: ./media/notification-hubs-kindle-get-started/notification-hub-kindle-portal4.png
[4]: ./media/notification-hubs-kindle-get-started/notification-hub-kindle-portal5.png
[5]: ./media/notification-hubs-kindle-get-started/notification-hub-kindle-cmd-window.png
[6]: ./media/notification-hubs-kindle-get-started/notification-hub-kindle-new-java-class.png
[7]: ./media/notification-hubs-kindle-get-started/notification-hub-kindle-notification.png
