1. Åbn filen i projektet **app** `AndroidManifest.xml`. Erstat teksten i koden i de næste to trin, _`**my_app_package**`_ med navnet på app-pakke til dit projekt, som er værdien af den `package` attributten for de `manifest` mærke.

2. Tilføje følgende nye tilladelser efter den nuværende `uses-permission` element:

        <permission android:name="**my_app_package**.permission.C2D_MESSAGE"
            android:protectionLevel="signature" />
        <uses-permission android:name="**my_app_package**.permission.C2D_MESSAGE" />
        <uses-permission android:name="com.google.android.c2dm.permission.RECEIVE" />
        <uses-permission android:name="android.permission.GET_ACCOUNTS" />
        <uses-permission android:name="android.permission.WAKE_LOCK" />

3. Føj følgende kode efter den `application` åbne mærke:

        <receiver android:name="com.microsoft.windowsazure.notifications.NotificationsBroadcastReceiver"
                                        android:permission="com.google.android.c2dm.permission.SEND">
            <intent-filter>
                <action android:name="com.google.android.c2dm.intent.RECEIVE" />
                <category android:name="**my_app_package**" />
            </intent-filter>
        </receiver>


4. Åbn filen *ToDoActivity.java*og tilføje følgende importsætning:

        import com.microsoft.windowsazure.notifications.NotificationsManager;


5. Føje følgende privat variabel til klassen: Erstat _`<PROJECT_NUMBER>`_ med det projektnummer, der er tildelt ved Google din app i den foregående fremgangsmåde:

        public static final String SENDER_ID = "<PROJECT_NUMBER>";

6. Ændre definitionen af *MobileServiceClient* fra **privat** til **offentlig statisk**, så det ser nu sådan ud:

        public static MobileServiceClient mClient;

7. Næste vi brug for at tilføje en ny klasse for at håndtere meddelelser. Åbn **src**i Projektstifinder => **primære** => **java** noder og højreklik noden pakke navn: Klik på **Ny**, og klik derefter på **Java-klasse**.

8. I **navnet** type `MyHandler`, klik derefter på **OK**.


    ![](./media/app-service-mobile-android-configure-push/android-studio-create-class.png)


9. Erstat erklæringen af klasse med i filen MyHandler

        public class MyHandler extends NotificationsHandler {


10. Tilføj følgende Importér udtalelser til den `MyHandler` klasse:

        import com.microsoft.windowsazure.notifications.NotificationsHandler;
        import android.app.NotificationManager;
        import android.app.PendingIntent;
        import android.content.Context;
        import android.content.Intent;
        import android.os.AsyncTask;
        import android.os.Bundle;
        import android.support.v4.app.NotificationCompat;


11. Dernæst tilføje dette medlem til den `MyHandler` klasse:

        public static final int NOTIFICATION_ID = 1;


12. I den `MyHandler` klasse, skal du tilføje følgende kode for at tilsidesætte metoden **onRegistered** , som registrerer din enhed med tjenesten mobile meddelelse Hub.

        @Override
        public void onRegistered(Context context,  final String gcmRegistrationId) {
            super.onRegistered(context, gcmRegistrationId);

            new AsyncTask<Void, Void, Void>() {

                protected Void doInBackground(Void... params) {
                    try {
                        ToDoActivity.mClient.getPush().register(gcmRegistrationId);
                        return null;
                    }
                    catch(Exception e) {
                        // handle error             
                    }
                    return null;            
                }
            }.execute();
        }


13. I den `MyHandler` klasse, skal du tilføje følgende kode for at tilsidesætte metoden **onReceive** , som medfører, at meddelelsen vises, når den modtages.

        @Override
        public void onReceive(Context context, Bundle bundle) {
                String msg = bundle.getString("message");

                PendingIntent contentIntent = PendingIntent.getActivity(context,
                        0, // requestCode
                        new Intent(context, ToDoActivity.class),
                        0); // flags

                Notification notification = new NotificationCompat.Builder(context)
                        .setSmallIcon(R.drawable.ic_launcher)
                        .setContentTitle("Notification Hub Demo")
                        .setStyle(new NotificationCompat.BigTextStyle().bigText(msg))
                        .setContentText(msg)
                        .setContentIntent(contentIntent)
                        .build();

                NotificationManager notificationManager = (NotificationManager)
                        context.getSystemService(Context.NOTIFICATION_SERVICE);
                notificationManager.notify(NOTIFICATION_ID, notification);
        }


14. Opdater metoden **onCreate** i klassen *ToDoActivity* til at registrere klassen meddelelse handler tilbage i filen TodoActivity.java. Sørg for at tilføje denne kode, når *MobileServiceClient* er en forekomst.


        NotificationsManager.handleNotifications(this, SENDER_ID, MyHandler.class);

    Din app er nu opdateret til at understøtte pushmeddelelser.
