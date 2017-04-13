<properties
    pageTitle="Sende pushmeddelelser til Android med Azure meddelelse Hubs og Firebase skyen Messaging | Microsoft Azure"
    description="I dette selvstudium lærer du at bruge Azure meddelelse Hubs og Firebase skyen Messaging til pushmeddelelser til Android-enheder."
    services="notification-hubs"
    documentationCenter="android"
    keywords="Push-beskeder, opslagsnål meddelelse, android opslagsnål meddelelse, fcm, firebase messaging i skyen"
    authors="ysxu"
    manager="erikre"
    editor=""/>
<tags
    ms.service="notification-hubs"
    ms.workload="mobile"
    ms.tgt_pltfrm="mobile-android"
    ms.devlang="java"
    ms.topic="hero-article"
    ms.date="07/14/2016"
    ms.author="yuaxu"/>

# <a name="sending-push-notifications-to-android-with-azure-notification-hubs"></a>Sende pushmeddelelser til Android med Azure meddelelse Hubs

[AZURE.INCLUDE [notification-hubs-selector-get-started](../../includes/notification-hubs-selector-get-started.md)]

##<a name="overview"></a>Oversigt

> [AZURE.IMPORTANT] I dette emne beskrives pushmeddelelser med Google Firebase skyen Messaging (FCM). Hvis du stadig bruger Google Cloud Messaging (GCM) skal du se [sender pushmeddelelser til Android med Azure meddelelse hubber og GCM](notification-hubs-android-push-notification-google-gcm-get-started.md).

Dette selvstudium viser, hvordan du bruger Azure meddelelse Hubs og Firebase skyen Messaging til at sende pushmeddelelser til Android programmet.
Du vil oprette en tom Android app, der modtager pushmeddelelser ved hjælp af Firebase skyen Messaging (FCM).



[AZURE.INCLUDE [notification-hubs-hero-slug](../../includes/notification-hubs-hero-slug.md)]

Færdige koden for dette selvstudium kan hentes fra GitHub [her](https://github.com/Azure/azure-notificationhubs-samples/tree/master/Android/GetStartedFirebase).


##<a name="prerequisites"></a>Forudsætninger

> [AZURE.IMPORTANT] For at fuldføre dette selvstudium skal skal du have en active Azure-konto. Hvis du ikke har en konto, kan du oprette en gratis prøveversion konto på blot et par minutter. Yderligere oplysninger finder du [Azure gratis prøveversion](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A643EE910&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Fen-us%2Fdocumentation%2Farticles%2Fnotification-hubs-android-get-started).

- Ud over en aktiv Azure konto nævnt ovenfor, kræver dette selvstudium den seneste version af [Android Studio](http://go.microsoft.com/fwlink/?LinkId=389797).
- Android 2.3 eller nyere til Firebase skyen Messaging.
- Google lager revision 27 eller nyere er påkrævet til Firebase skyen Messaging.
- Google Play Services 9.0.2 eller nyere til Firebase skyen Messaging.
- Fuldfør dette selvstudium er en betingelse for alle andre besked om Hubs selvstudier til Android-apps.


##<a name="create-a-new-android-studio-project"></a>Oprette et nyt Android Studio-projekt

1. Starte et nyt Android Studio-projekt i Android Studio.

    ![Android Studio - projekt](./media/notification-hubs-android-push-notification-google-fcm-get-started/notification-hubs-android-studio-new-project.png)

2. Vælg **Telefon og Tablet** formular faktoren og **Minimum SDK** , som du vil understøtte. Klik derefter på **Næste**.

    ![Android Studio - projekt oprettelse af arbejdsproces](./media/notification-hubs-android-push-notification-google-fcm-get-started/notification-hubs-android-studio-choose-form-factor.png)

3. Vælg **Tom aktivitet** til den primære aktivitet, klik på **Næste**, og klik derefter på **Udfør**.


##<a name="create-a-project-that-supports-firebase-cloud-messaging"></a>Oprette et projekt, der understøtter Firebase skyen Messaging

[AZURE.INCLUDE [notification-hubs-enable-firebase-cloud-messaging](../../includes/notification-hubs-enable-firebase-cloud-messaging.md)]


##<a name="configure-a-new-notification-hub"></a>Konfigurere en ny meddelelse-hub

[AZURE.INCLUDE [notification-hubs-portal-create-new-hub](../../includes/notification-hubs-portal-create-new-hub.md)]

&emsp;&emsp;6. Vælg **Meddelelse tjenester** og derefter **Google (GCM)**i bladet **Indstillinger** af din meddelelse hub. Angiv tasten FCM server, du kopierede tidligere fra [Firebase console](https://firebase.google.com/console/) , og klik på **Gem**.

&emsp;&emsp;![Azure meddelelse Hubs - Google (GCM)](./media/notification-hubs-android-push-notification-google-fcm-get-started/notification-hubs-gcm-api.png)

Din meddelelse hub nu er konfigureret til at arbejde med Firebase skyen Messagin, og du har forbindelse strengene både registrere din app for at sende og modtage pushmeddelelser.

##<a id="connecting-app"></a>Oprette forbindelse din app til hubben meddelelse

###<a name="add-google-play-services-to-the-project"></a>Tilføje Google Play tjenester til projektet

[AZURE.INCLUDE [Add Play Services](../../includes/notification-hubs-android-studio-add-google-play-services.md)]

###<a name="adding-azure-notification-hubs-libraries"></a>Tilføje Azure meddelelse Hubs biblioteker


1. I den `Build.Gradle` arkivere den til **app**, skal du tilføje følgende linjer i sektionen **afhængigheder** .

        compile 'com.microsoft.azure:notification-hubs-android-sdk:0.4@aar'
        compile 'com.microsoft.azure:azure-notifications-handler:1.0.1@aar'

2. Du kan tilføje følgende lager efter sektionen **afhængigheder** .

        repositories {
            maven {
                url "http://dl.bintray.com/microsoftazuremobile/SDK"
            }
        }

### <a name="updating-the-androidmanifestxml"></a>Ved at opdatere AndroidManifest.xml.


1. For at understøtte FCM, skal vi implementere en forekomst-ID lytteren tjeneste i vores kode, der bruges til at [få registrering tokens](https://firebase.google.com/docs/cloud-messaging/android/client#sample-register) ved hjælp af [Google's FirebaseInstanceId API](https://firebase.google.com/docs/reference/android/com/google/firebase/iid/FirebaseInstanceId). I dette selvstudium vi navngiver klassen `MyInstanceIDService`. 
 
    Føje definitionen af følgende tjenesten til filen AndroidManifest.xml indeni den `<application>` mærke. 

        <service android:name=".MyInstanceIDService">
            <intent-filter>
                <action android:name="com.google.firebase.INSTANCE_ID_EVENT"/>
            </intent-filter>
        </service>



2. Når vi har modtaget vores FCM registreringstoken fra FirebaseInstanceId API, vil vi bruge den til at [registrere med Azure meddelelse Hub](notification-hubs-push-notification-registration-management.md). Vi understøtter denne registrering på baggrund ved hjælp af en `IntentService` med navnet `RegistrationIntentService`. Denne tjeneste vil også være ansvarlig for at opdatere vores FCM registreringstoken.
 
    Føje definitionen af følgende tjenesten til filen AndroidManifest.xml indeni den `<application>` mærke. 

        <service
            android:name=".RegistrationIntentService"
            android:exported="false">
        </service>



3. Vi kan også angive en modtager for at modtage meddelelser. Tilføj følgende modtager definitionen i filen AndroidManifest.xml indeni den `<application>` mærke. Erstatte den `<your package>` pladsholder med på din faktiske pakkenavn vises øverst på den `AndroidManifest.xml` fil.

        <receiver android:name="com.microsoft.windowsazure.notifications.NotificationsBroadcastReceiver"
            android:permission="com.google.android.c2dm.permission.SEND">
            <intent-filter>
                <action android:name="com.google.android.c2dm.intent.RECEIVE" />
                <category android:name="<your package name>" />
            </intent-filter>
        </receiver>



4. Tilføj følgende nødvendige FCM relateret tilladelser under den `</application>` mærke. Sørg for at erstatte `<your package>` med navnet på pakken vises øverst på den `AndroidManifest.xml` fil.

    Se [konfigurere en GCM klient-app til Android](https://developers.google.com/cloud-messaging/android/client#manifest) og [overføre en GCM klient App til Android Firebase skyen meddelelser](https://developers.google.com/cloud-messaging/android/android-migrate-fcm#remove_the_permissions_required_by_gcm)kan finde flere oplysninger om disse tilladelser.

        <uses-permission android:name="android.permission.INTERNET"/>
        <uses-permission android:name="android.permission.GET_ACCOUNTS"/>
        <uses-permission android:name="com.google.android.c2dm.permission.RECEIVE" />


### <a name="adding-code"></a>Tilføje kode


1. Udvid **app**til projektvisningen > **src** > **primære** > **java**. Højreklik på mappen pakke under **java**, klik på **Ny**og derefter klikke på **Java-klasse**. Tilføje en ny klasse med navnet `NotificationSettings`. 

    ![Android Studio – nye Java-klasse](./media/notification-hubs-android-push-notification-google-fcm-get-started/notification-hub-android-new-class.png)

    Sørg for at opdatere disse tre pladsholdere i følgende kode for den `NotificationSettings` klasse:
    * **Afsender-id**: feltet du hentede tidligere i fanen **Skyen Messaging** i dine indstillinger for project i [Firebase console](https://firebase.google.com/console/)afsender-Id.
    * **HubListenConnectionString**: **DefaultListenAccessSignature** forbindelsesstrengen til centrum. Du kan kopiere denne forbindelsesstreng ved at klikke på **Access politikker** på bladet **Indstillinger** af din hub på [Azure-portalen].
    * **HubName**: Brug navnet på din meddelelse-hub, der vises i bladet hub på [Azure-portalen].

    `NotificationSettings`kode:

        public class NotificationSettings {
            public static String SenderId = "<Your project number>";
            public static String HubName = "<Your HubName>";
            public static String HubListenConnectionString = "<Enter your DefaultListenSharedAccessSignature connection string>";
        }

2. Brug af ovenstående fremgangsmåde, tilføje en anden nye klasse med navnet `MyInstanceIDService`. Dette er vores implementering af lytteren forekomst-ID.

    Koden for klassen ringer vores `IntentService` til at [opdatere FCM tokenet](https://developers.google.com/instance-id/guides/android-implementation#refresh_tokens) i baggrunden.

        import android.content.Intent;
        import android.util.Log;
        import com.google.firebase.iid.FirebaseInstanceIdService;
        
        
        public class MyInstanceIDService extends FirebaseInstanceIdService {
        
            private static final String TAG = "MyInstanceIDService";
        
            @Override
            public void onTokenRefresh() {
        
                Log.d(TAG, "Refreshing GCM Registration Token");
        
                Intent intent = new Intent(this, RegistrationIntentService.class);
                startService(intent);
            }
        };


3. Føje en anden nye klasse til projektet navnet, `RegistrationIntentService`. Dette er implementering til vores `IntentService` , der håndterer [opdatere FCM tokenet](https://developers.google.com/instance-id/guides/android-implementation#refresh_tokens) og [registrering af med besked om hubben](notification-hubs-push-notification-registration-management.md).

    Brug følgende kode for denne klasse.

        import android.app.IntentService;
        import android.content.Intent;
        import android.content.SharedPreferences;
        import android.preference.PreferenceManager;
        import android.util.Log;        
        import com.google.firebase.iid.FirebaseInstanceId;
        import com.microsoft.windowsazure.messaging.NotificationHub;
        
        public class RegistrationIntentService extends IntentService {
        
            private static final String TAG = "RegIntentService";
        
            private NotificationHub hub;
        
            public RegistrationIntentService() {
                super(TAG);
            }
        
            @Override
            protected void onHandleIntent(Intent intent) {
        
                SharedPreferences sharedPreferences = PreferenceManager.getDefaultSharedPreferences(this);
                String resultString = null;
                String regID = null;
                String storedToken = null;
        
                try {
                    String FCM_token = FirebaseInstanceId.getInstance().getToken();
                    Log.d(TAG, "FCM Registration Token: " + FCM_token);
        
                    // Storing the registration id that indicates whether the generated token has been
                    // sent to your server. If it is not stored, send the token to your server,
                    // otherwise your server should have already received the token.
                    if (((regID=sharedPreferences.getString("registrationID", null)) == null)){
        
                        NotificationHub hub = new NotificationHub(NotificationSettings.HubName,
                                NotificationSettings.HubListenConnectionString, this);
                        Log.d(TAG, "Attempting a new registration with NH using FCM token : " + FCM_token);
                        regID = hub.register(FCM_token).getRegistrationId();
        
                        // If you want to use tags...
                        // Refer to : https://azure.microsoft.com/en-us/documentation/articles/notification-hubs-routing-tag-expressions/
                        // regID = hub.register(token, "tag1,tag2").getRegistrationId();
        
                        resultString = "New NH Registration Successfully - RegId : " + regID;
                        Log.d(TAG, resultString);
        
                        sharedPreferences.edit().putString("registrationID", regID ).apply();
                        sharedPreferences.edit().putString("FCMtoken", FCM_token ).apply();
                    }
        
                    // Check if the token may have been compromised and needs refreshing.
                    else if ((storedToken=sharedPreferences.getString("FCMtoken", "")) != FCM_token) {
        
                        NotificationHub hub = new NotificationHub(NotificationSettings.HubName,
                                NotificationSettings.HubListenConnectionString, this);
                        Log.d(TAG, "NH Registration refreshing with token : " + FCM_token);
                        regID = hub.register(FCM_token).getRegistrationId();
        
                        // If you want to use tags...
                        // Refer to : https://azure.microsoft.com/en-us/documentation/articles/notification-hubs-routing-tag-expressions/
                        // regID = hub.register(token, "tag1,tag2").getRegistrationId();
        
                        resultString = "New NH Registration Successfully - RegId : " + regID;
                        Log.d(TAG, resultString);
        
                        sharedPreferences.edit().putString("registrationID", regID ).apply();
                        sharedPreferences.edit().putString("FCMtoken", FCM_token ).apply();
                    }
        
                    else {
                        resultString = "Previously Registered Successfully - RegId : " + regID;
                    }
                } catch (Exception e) {
                    Log.e(TAG, resultString="Failed to complete registration", e);
                    // If an exception happens while fetching the new token or updating our registration data
                    // on a third-party server, this ensures that we'll attempt the update at a later time.
                }
        
                // Notify UI that registration has completed.
                if (MainActivity.isVisible) {
                    MainActivity.mainActivity.ToastNotify(resultString);
                }
            }
        }


        
4. I din `MainActivity` klasse, skal du tilføje følgende `import` sætninger over erklæringen af klasse.

        import com.google.android.gms.common.ConnectionResult;
        import com.google.android.gms.common.GoogleApiAvailability;
        import com.microsoft.windowsazure.notifications.NotificationsManager;
        import android.content.Intent;
        import android.util.Log;
        import android.widget.TextView;
        import android.widget.Toast;

5. Tilføj følgende privat medlemmer øverst i klassen. Vi bruger disse [kontrollere tilgængeligheden af Google afspille tjenester som anbefalet af Google](https://developers.google.com/android/guides/setup#ensure_devices_have_the_google_play_services_apk).

        public static MainActivity mainActivity;
        public static Boolean isVisible = false;    
        private static final String TAG = "MainActivity";
        private static final int PLAY_SERVICES_RESOLUTION_REQUEST = 9000;

6. I din `MainActivity` klasse, skal du tilføje følgende metode til tilgængeligheden af afspille Google-tjenester. 

        /**
         * Check the device to make sure it has the Google Play Services APK. If
         * it doesn't, display a dialog that allows users to download the APK from
         * the Google Play Store or enable it in the device's system settings.
         */
        private boolean checkPlayServices() {
            GoogleApiAvailability apiAvailability = GoogleApiAvailability.getInstance();
            int resultCode = apiAvailability.isGooglePlayServicesAvailable(this);
            if (resultCode != ConnectionResult.SUCCESS) {
                if (apiAvailability.isUserResolvableError(resultCode)) {
                    apiAvailability.getErrorDialog(this, resultCode, PLAY_SERVICES_RESOLUTION_REQUEST)
                            .show();
                } else {
                    Log.i(TAG, "This device is not supported by Google Play Services.");
                    ToastNotify("This device is not supported by Google Play Services.");
                    finish();
                }
                return false;
            }
            return true;
        }


7. I din `MainActivity` klasse, skal du tilføje følgende kode, som skal tjekke for Google afspille tjenester, inden du ringer din `IntentService` til at få din FCM registrering token og registrere med din anmeldelse hub.

        public void registerWithNotificationHubs()
        {
            if (checkPlayServices()) {
                // Start IntentService to register this application with FCM.
                Intent intent = new Intent(this, RegistrationIntentService.class);
                startService(intent);
            }
        }


8. I den `OnCreate` metode til at den `MainActivity` klasse, skal du tilføje følgende kode for at starte registreringsprocessen, når der er oprettet aktivitet.

        @Override
        protected void onCreate(Bundle savedInstanceState) {
            super.onCreate(savedInstanceState);
            setContentView(R.layout.activity_main);
    
            mainActivity = this;
            NotificationsManager.handleNotifications(this, NotificationSettings.SenderId, MyHandler.class);
            registerWithNotificationHubs();
        }


9. Tilføje disse yderligere metoder til at den `MainActivity` at bekræfte app tilstand og rapportere status i din app.

        @Override
        protected void onStart() {
            super.onStart();
            isVisible = true;
        }
    
        @Override
        protected void onPause() {
            super.onPause();
            isVisible = false;
        }
    
        @Override
        protected void onResume() {
            super.onResume();
            isVisible = true;
        }
    
        @Override
        protected void onStop() {
            super.onStop();
            isVisible = false;
        }
    
        public void ToastNotify(final String notificationMessage) {
            runOnUiThread(new Runnable() {
                @Override
                public void run() {
                    Toast.makeText(MainActivity.this, notificationMessage, Toast.LENGTH_LONG).show();
                    TextView helloText = (TextView) findViewById(R.id.text_hello);
                    helloText.setText(notificationMessage);
                }
            });
        }


10. Den `ToastNotify` metode bruger *"Hej verden"* `TextView` kontrolelement for at rapportere status og meddelelser om vedvarende i appen. Tilføj følgende-id for det pågældende kontrolelement i dit activity_main.xml layout.

        android:id="@+id/text_hello"

11. Næste tilføjer vi en underklasse for vores modtager vi defineret i AndroidManifest.xml. Føje en anden nye klasse til projektet med navnet `MyHandler`.

12. Tilføj følgende Importér udtalelser øverst på `MyHandler.java`:

        import android.app.NotificationManager;
        import android.app.PendingIntent;
        import android.content.Context;
        import android.content.Intent;
        import android.media.RingtoneManager;
        import android.net.Uri;
        import android.os.Bundle;
        import android.support.v4.app.NotificationCompat;
        import com.microsoft.windowsazure.notifications.NotificationsHandler;

13. Tilføj følgende kode for den `MyHandler` klasse, så det er en underklasse af `com.microsoft.windowsazure.notifications.NotificationsHandler`.

    Denne kode tilsidesætter den `OnReceive` metode, så handleren rapporterer meddelelser, der modtages. Handleren også sender push-besked til Android meddelelse manager ved hjælp af den `sendNotification()` metode. Den `sendNotification()` metode skal udføres, når app'en kører ikke, og der modtages en meddelelse.

        public class MyHandler extends NotificationsHandler {
            public static final int NOTIFICATION_ID = 1;
            private NotificationManager mNotificationManager;
            NotificationCompat.Builder builder;
            Context ctx;
        
            @Override
            public void onReceive(Context context, Bundle bundle) {
                ctx = context;
                String nhMessage = bundle.getString("message");
                sendNotification(nhMessage);
                if (MainActivity.isVisible) {
                    MainActivity.mainActivity.ToastNotify(nhMessage);
                }
            }
        
            private void sendNotification(String msg) {
        
                Intent intent = new Intent(ctx, MainActivity.class);
                intent.addFlags(Intent.FLAG_ACTIVITY_CLEAR_TOP);
        
                mNotificationManager = (NotificationManager)
                        ctx.getSystemService(Context.NOTIFICATION_SERVICE);
        
                PendingIntent contentIntent = PendingIntent.getActivity(ctx, 0,
                        intent, PendingIntent.FLAG_ONE_SHOT);
        
                Uri defaultSoundUri = RingtoneManager.getDefaultUri(RingtoneManager.TYPE_NOTIFICATION);
                NotificationCompat.Builder mBuilder =
                        new NotificationCompat.Builder(ctx)
                                .setSmallIcon(R.mipmap.ic_launcher)
                                .setContentTitle("Notification Hub Demo")
                                .setStyle(new NotificationCompat.BigTextStyle()
                                        .bigText(msg))
                                .setSound(defaultSoundUri)
                                .setContentText(msg);
        
                mBuilder.setContentIntent(contentIntent);
                mNotificationManager.notify(NOTIFICATION_ID, mBuilder.build());
            }
        }


14. Klik på **Generer**i Android Studio på menulinjen, > **Genopbygge Project** for at sikre, at ingen fejl findes i din kode.
15. Køre appen på din enhed, og bekræft den registrerer korrekt med besked om hub. 

    > [AZURE.NOTE] Registrering kan mislykkes på den indledende start indtil den `onTokenRefresh()` metode til forekomst-id-tjeneste kaldes. Opdateringen skal oprettes en vellykket registrering med besked om hub.

##<a name="sending-push-notifications"></a>Afsendelse af pushmeddelelser

Du kan teste modtager pushmeddelelser i din app ved at sende dem via [Azure Portal] - udseende til **fejlfinding** under bladet hub, som vist nedenfor.

![Sende Azure meddelelse Hubs - Test](./media/notification-hubs-android-push-notification-google-fcm-get-started/notification-hubs-test-send.png)

[AZURE.INCLUDE [notification-hubs-sending-notifications-from-the-portal](../../includes/notification-hubs-sending-notifications-from-the-portal.md)]

## <a name="optional-send-push-notifications-directly-from-the-app"></a>(Valgfrit) Send pushmeddelelser direkte fra app

>[AZURE.IMPORTANT] I dette eksempel for at sende beskeder fra klient app er angivet til at blive fortrolig kun. Da dette kræver det `DefaultFullSharedAccessSignature` for at være til stede på appen oprettelse af klienten, det udfører din meddelelse hub til risikoen, at en bruger kan få adgang for at sende uautoriseret meddelelser til dine kunder.

Normalt, skal du sende meddelelser ved hjælp af en back end-serveren. I nogle tilfælde vil du kunne sende pushmeddelelser direkte fra klientprogrammet. I dette afsnit beskrives det, hvordan du kan sende beskeder fra klienten på computeren ved hjælp af [Azure meddelelse Hub REST-API](https://msdn.microsoft.com/library/azure/dn223264.aspx).

1. Udvid **App**i Android Studio projektvisning > **src** > **primære** > **rejser** > **layout**. Åbn den `activity_main.xml` layout-fil og klikke på fanen **tekst** for at opdatere tekst indholdet af filerne. Opdatere den med koden nedenfor, som tilføjer nye `Button` og `EditText` kontrolelementer til at sende opslagsnål beskeder til meddelelse-hub. Tilføje denne kode nederst, lige før `</RelativeLayout>`.

        <Button
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:text="@string/send_button"
        android:id="@+id/sendbutton"
        android:layout_centerVertical="true"
        android:layout_centerHorizontal="true"
        android:onClick="sendNotificationButtonOnClick" />

        <EditText
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:id="@+id/editTextNotificationMessage"
        android:layout_above="@+id/sendbutton"
        android:layout_centerHorizontal="true"
        android:layout_marginBottom="42dp"
        android:hint="@string/notification_message_hint" />

2. Udvid **App**i Android Studio projektvisning > **src** > **primære** > **rejser** > **værdier**. Åbn den `strings.xml` filen, og Tilføj strengværdier, der refereres til ved den nye `Button` og `EditText` kontrolelementer. Tilføje disse nederst i filen, lige før `</resources>`.

        <string name="send_button">Send Notification</string>
        <string name="notification_message_hint">Enter notification message text</string>


3. I din `NotificationSetting.java` fil, skal du tilføje følgende indstilling til den `NotificationSettings` klasse.

    Opdater `HubFullAccess` med **DefaultFullSharedAccessSignature** forbindelsesstrengen til centrum. Denne forbindelsesstreng kan kopieres fra [Azure-portalen] ved at klikke på **Access politikker** på bladet **Indstillinger** for din meddelelse hub.

        public static String HubFullAccess = "<Enter Your DefaultFullSharedAccessSignature Connection string>";

4. I din `MainActivity.java` fil, skal du tilføje følgende `import` sætninger ovenfor den `MainActivity` klasse.

        import java.io.BufferedOutputStream;
        import java.io.BufferedReader;
        import java.io.InputStreamReader;
        import java.io.OutputStream;
        import java.net.HttpURLConnection;
        import java.net.URL;
        import java.net.URLEncoder;
        import javax.crypto.Mac;
        import javax.crypto.spec.SecretKeySpec;
        import android.util.Base64;
        import android.view.View;
        import android.widget.EditText;

6. I din `MainActivity.java` fil, tilføje følgende medlemmer i toppen af den `MainActivity` klasse.  

        private String HubEndpoint = null;
        private String HubSasKeyName = null;
        private String HubSasKeyValue = null;

6. Du skal oprette en Software Access signatur (SaS) token for at godkende en Send anmodning om at sende meddelelser til din meddelelse hub. Det gør du ved parsing af de vigtigste data fra forbindelsesstrengen og derefter oprette token SaS som nævnt i den [Almindelige begreber](http://msdn.microsoft.com/library/azure/dn495627.aspx) REST-API reference. Følgende kode er et eksempel på implementering.

    I `MainActivity.java`, tilføje følgende metode til den `MainActivity` klasse til at fortolke din forbindelsesstreng.

        /**
         * Example code from http://msdn.microsoft.com/library/azure/dn495627.aspx
         * to parse the connection string so a SaS authentication token can be
         * constructed.
         *
         * @param connectionString This must be the DefaultFullSharedAccess connection
         *                         string for this example.
         */
        private void ParseConnectionString(String connectionString)
        {
            String[] parts = connectionString.split(";");
            if (parts.length != 3)
                throw new RuntimeException("Error parsing connection string: "
                        + connectionString);
    
            for (int i = 0; i < parts.length; i++) {
                if (parts[i].startsWith("Endpoint")) {
                    this.HubEndpoint = "https" + parts[i].substring(11);
                } else if (parts[i].startsWith("SharedAccessKeyName")) {
                    this.HubSasKeyName = parts[i].substring(20);
                } else if (parts[i].startsWith("SharedAccessKey")) {
                    this.HubSasKeyValue = parts[i].substring(16);
                }
            }
        }


7. I `MainActivity.java`, tilføje følgende metode til den `MainActivity` klasse til at oprette en SaS godkendelse token.

        /**
         * Example code from http://msdn.microsoft.com/library/azure/dn495627.aspx to
         * construct a SaS token from the access key to authenticate a request.
         *
         * @param uri The unencoded resource URI string for this operation. The resource
         *            URI is the full URI of the Service Bus resource to which access is
         *            claimed. For example,
         *            "http://<namespace>.servicebus.windows.net/<hubName>"
         */
        private String generateSasToken(String uri) {
    
            String targetUri;
            String token = null;
            try {
                targetUri = URLEncoder
                        .encode(uri.toString().toLowerCase(), "UTF-8")
                        .toLowerCase();
    
                long expiresOnDate = System.currentTimeMillis();
                int expiresInMins = 60; // 1 hour
                expiresOnDate += expiresInMins * 60 * 1000;
                long expires = expiresOnDate / 1000;
                String toSign = targetUri + "\n" + expires;
    
                // Get an hmac_sha1 key from the raw key bytes
                byte[] keyBytes = HubSasKeyValue.getBytes("UTF-8");
                SecretKeySpec signingKey = new SecretKeySpec(keyBytes, "HmacSHA256");
    
                // Get an hmac_sha1 Mac instance and initialize with the signing key
                Mac mac = Mac.getInstance("HmacSHA256");
                mac.init(signingKey);
    
                // Compute the hmac on input data bytes
                byte[] rawHmac = mac.doFinal(toSign.getBytes("UTF-8"));
    
                // Using android.util.Base64 for Android Studio instead of
                // Apache commons codec
                String signature = URLEncoder.encode(
                        Base64.encodeToString(rawHmac, Base64.NO_WRAP).toString(), "UTF-8");
    
                // Construct authorization string
                token = "SharedAccessSignature sr=" + targetUri + "&sig="
                        + signature + "&se=" + expires + "&skn=" + HubSasKeyName;
            } catch (Exception e) {
                if (isVisible) {
                    ToastNotify("Exception Generating SaS : " + e.getMessage().toString());
                }
            }
    
            return token;
        }



8. I `MainActivity.java`, tilføje følgende metode til den `MainActivity` klasse, der skal håndtere Klik på knappen **Send meddelelse** og sende meddelelse opslagsnål til hubben ved hjælp af de indbyggede REST-API.

        /**
         * Send Notification button click handler. This method parses the
         * DefaultFullSharedAccess connection string and generates a SaS token. The
         * token is added to the Authorization header on the POST request to the
         * notification hub. The text in the editTextNotificationMessage control
         * is added as the JSON body for the request to add a GCM message to the hub.
         *
         * @param v
         */
        public void sendNotificationButtonOnClick(View v) {
            EditText notificationText = (EditText) findViewById(R.id.editTextNotificationMessage);
            final String json = "{\"data\":{\"message\":\"" + notificationText.getText().toString() + "\"}}";
    
            new Thread()
            {
                public void run()
                {
                    try
                    {
                        // Based on reference documentation...
                        // http://msdn.microsoft.com/library/azure/dn223273.aspx
                        ParseConnectionString(NotificationSettings.HubFullAccess);
                        URL url = new URL(HubEndpoint + NotificationSettings.HubName +
                                "/messages/?api-version=2015-01");
    
                        HttpURLConnection urlConnection = (HttpURLConnection)url.openConnection();
    
                        try {
                            // POST request
                            urlConnection.setDoOutput(true);
    
                            // Authenticate the POST request with the SaS token
                            urlConnection.setRequestProperty("Authorization", 
                                generateSasToken(url.toString()));
    
                            // Notification format should be GCM
                            urlConnection.setRequestProperty("ServiceBusNotification-Format", "gcm");
    
                            // Include any tags
                            // Example below targets 3 specific tags
                            // Refer to : https://azure.microsoft.com/en-us/documentation/articles/notification-hubs-routing-tag-expressions/
                            // urlConnection.setRequestProperty("ServiceBusNotification-Tags", 
                            //      "tag1 || tag2 || tag3");
    
                            // Send notification message
                            urlConnection.setFixedLengthStreamingMode(json.length());
                            OutputStream bodyStream = new BufferedOutputStream(urlConnection.getOutputStream());
                            bodyStream.write(json.getBytes());
                            bodyStream.close();
    
                            // Get reponse
                            urlConnection.connect();
                            int responseCode = urlConnection.getResponseCode();
                            if ((responseCode != 200) && (responseCode != 201)) {
                                BufferedReader br = new BufferedReader(new InputStreamReader((urlConnection.getErrorStream())));
                                String line;
                                StringBuilder builder = new StringBuilder("Send Notification returned " +
                                        responseCode + " : ")  ;
                                while ((line = br.readLine()) != null) {
                                    builder.append(line);
                                }

                                ToastNotify(builder.toString());
                            }
                        } finally {
                            urlConnection.disconnect();
                        }
                    }
                    catch(Exception e)
                    {
                        if (isVisible) {
                            ToastNotify("Exception Sending Notification : " + e.getMessage().toString());
                        }
                    }
                }
            }.start();
        }



##<a name="testing-your-app"></a>Test din app

####<a name="push-notifications-in-the-emulator"></a>Push-beskeder i emulatoren

Hvis du vil teste pushmeddelelser i en emulator, skal du kontrollere, at billedet emulator understøtter Google API-niveau, du vælger for din app. Hvis dit billede ikke understøtter oprindelige Google APIs, du vil ender med den **SERVICE\_ikke\_tilgængelige** undtagelse.

Ud over ovenstående, sikre, at du har føjet din Google-konto til din kører emulator under **Indstillinger for** > **konti**. Ellers forsøg på at registrere med GCM kan medføre den **godkendelse\_MISLYKKET** undtagelse.

####<a name="running-the-application"></a>Køre programmet

1. Køre appen, og Bemærk, at-ID, registrering er rapporteret for en vellykket registrering.

    ![Test på Android - kanal registrering](./media/notification-hubs-android-push-notification-google-fcm-get-started/notification-hubs-android-studio-registered.png)

2. Skriv en meddelelse, der skal sendes til alle Android-enheder, der er registreret med hubben.

    ![Test på Android - afsendelse af en meddelelse](./media/notification-hubs-android-push-notification-google-fcm-get-started/notification-hubs-android-studio-set-message.png)

3. Tryk på **Send besked**. De enheder, der kører-appen, vises en `AlertDialog` forekomst med meddelelsen om opgaven opslagsnål. Enheder, der ikke har den app, der kører, men var tidligere er registreret til pushmeddelelser, modtager en meddelelse i Android meddelelse Manager. De kan ses ved at stryge ned fra den øverste venstre hjørne.

    ![Test på Android - beskeder](./media/notification-hubs-android-push-notification-google-fcm-get-started/notification-hubs-android-studio-received-message.png)

##<a name="next-steps"></a>Næste trin

Vi anbefaler selvstudiet [Brug meddelelse Hubs til pushmeddelelser til brugere] , som det næste trin. Det viser dig, hvordan at sende meddelelser fra en ASP.NET backend-version, bruge mærker til at målrette bestemte brugere.

Hvis du vil inddele dine brugere ved interesse grupper, skal du tjekke selvstudiet [Brug meddelelse Hubs sende seneste nyheder] .

Få mere generelle oplysninger om meddelelse Hubs under vores [Meddelelse Hubs vejledning].

<!-- Images. -->



<!-- URLs. -->
[Get started with push notifications in Mobile Services]: ../mobile-services-javascript-backend-android-get-started-push.md  
[Mobile Services Android SDK]: https://go.microsoft.com/fwLink/?LinkID=280126&clcid=0x409
[Referencing a library project]: http://go.microsoft.com/fwlink/?LinkId=389800
[Azure Classic Portal]: https://manage.windowsazure.com/
[Meddelelse om Hubs vejledning]: notification-hubs-push-notification-overview.md
[Bruge meddelelse Hubs til pushmeddelelser til brugere]: notification-hubs-aspnet-backend-gcm-android-push-to-user-google-notification.md
[Brug meddelelse Hubs til at sende seneste nyheder]: notification-hubs-aspnet-backend-android-xplat-segmented-gcm-push-notification.md
[Azure-portalen]: https://portal.azure.com
