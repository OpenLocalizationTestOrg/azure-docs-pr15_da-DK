<properties
    pageTitle="Meddelelse om Hubs varmeste nyheder selvstudie - Android"
    description="Lær, hvordan du bruger Azure Service Bus meddelelse Hubs til at sende seneste nyheder meddelelser til Android-enheder."
    services="notification-hubs"
    documentationCenter="android"
    authors="ysxu"
    manager="erikre"
    editor=""/>

<tags
    ms.service="notification-hubs"
    ms.workload="mobile"
    ms.tgt_pltfrm="mobile-android"
    ms.devlang="java"
    ms.topic="article"
    ms.date="06/29/2016" 
    ms.author="yuaxu"/>


# <a name="use-notification-hubs-to-send-breaking-news"></a>Brug meddelelse Hubs til at sende seneste nyheder

[AZURE.INCLUDE [notification-hubs-selector-breaking-news](../../includes/notification-hubs-selector-breaking-news.md)]

##<a name="overview"></a>Oversigt

Dette emne beskrives, hvordan du bruger Azure meddelelse Hubs til at udsende seneste nyheder meddelelser til en Android-app. Når du er færdig, skal du kunne tilmelde Bryd nyhedskategorier, du er interesseret i, og modtage kun pushmeddelelser for disse kategorier. Dette scenario er et almindeligt mønster for mange apps, hvor meddelelser skal sendes til de grupper af brugere, der har tidligere er erklæret interesse i dem, fx RSS-læseren, apps til musikelskere osv.

Broadcast scenarier er aktiveret ved at medtage en eller flere _mærker_ , når du opretter en registrering i hubben meddelelse. Når meddelelser er sendt til et mærke, får alle enheder, der har registreret til mærket meddelelsen. Fordi mærker er blot strenge, har de ikke klargøres på forhånd. Se [meddelelse Hubs Routing og mærke udtryk](notification-hubs-tags-segment-push-message.md)kan finde flere oplysninger om mærker.


##<a name="prerequisites"></a>Forudsætninger

Dette emne, der er baseret på den app, du oprettede i [komme i gang med besked om Hubs][get-started]. Før du starter selvstudiet, du skal allerede har udført [komme i gang med besked om Hubs][get-started].

##<a name="add-category-selection-to-the-app"></a>Tilføje valg af kategori på appen

Det første trin er at tilføje elementer på Brugergrænsefladen på dit eksisterende primære aktivitet, der gør det muligt at vælge kategorier til at registrere. De kategorier, der er valgt som en bruger er gemt på enheden. Når programmet starter, oprettes en enhed registrering i din meddelelse hubben med de valgte kategorier som mærker.

1. Åbn filen res/layout/activity_main.xml, og erstatte indhold med følgende:

        <LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
            xmlns:tools="http://schemas.android.com/tools"
            android:layout_width="match_parent"
            android:layout_height="match_parent"
            android:paddingBottom="@dimen/activity_vertical_margin"
            android:paddingLeft="@dimen/activity_horizontal_margin"
            android:paddingRight="@dimen/activity_horizontal_margin"
            android:paddingTop="@dimen/activity_vertical_margin"
            tools:context="com.example.breakingnews.MainActivity"
            android:orientation="vertical">

                <CheckBox
                    android:id="@+id/worldBox"
                    android:layout_width="wrap_content"
                    android:layout_height="wrap_content"
                    android:text="@string/label_world" />
                <CheckBox
                    android:id="@+id/politicsBox"
                    android:layout_width="wrap_content"
                    android:layout_height="wrap_content"
                    android:text="@string/label_politics" />
                <CheckBox
                    android:id="@+id/businessBox"
                    android:layout_width="wrap_content"
                    android:layout_height="wrap_content"
                    android:text="@string/label_business" />
                <CheckBox
                    android:id="@+id/technologyBox"
                    android:layout_width="wrap_content"
                    android:layout_height="wrap_content"
                    android:text="@string/label_technology" />
                <CheckBox
                    android:id="@+id/scienceBox"
                    android:layout_width="wrap_content"
                    android:layout_height="wrap_content"
                    android:text="@string/label_science" />
                <CheckBox
                    android:id="@+id/sportsBox"
                    android:layout_width="wrap_content"
                    android:layout_height="wrap_content"
                    android:text="@string/label_sports" />
                <Button
                    android:layout_width="wrap_content"
                    android:layout_height="wrap_content"
                    android:onClick="subscribe"
                    android:text="@string/button_subscribe" />
        </LinearLayout>

2. Åbn filen res/values/strings.xml og Tilføj følgende linjer:

        <string name="button_subscribe">Subscribe</string>
        <string name="label_world">World</string>
        <string name="label_politics">Politics</string>
        <string name="label_business">Business</string>
        <string name="label_technology">Technology</string>
        <string name="label_science">Science</string>
        <string name="label_sports">Sports</string>

    Din main_activity.xml grafiske layout ser nu sådan ud:

    ![][A1]

3. Opret nu en klasse **meddelelser** i den samme pakke som klasse **MainActivity** .

        import java.util.HashSet;
        import java.util.Set;

        import android.content.Context;
        import android.content.SharedPreferences;
        import android.os.AsyncTask;
        import android.util.Log;
        import android.widget.Toast;

        import com.google.android.gms.gcm.GoogleCloudMessaging;
        import com.microsoft.windowsazure.messaging.NotificationHub;

        public class Notifications {
            private static final String PREFS_NAME = "BreakingNewsCategories";
            private GoogleCloudMessaging gcm;
            private NotificationHub hub;
            private Context context;
            private String senderId;

            public Notifications(Context context, String senderId, String hubName, 
                                    String listenConnectionString) {
                this.context = context;
                this.senderId = senderId;
        
                gcm = GoogleCloudMessaging.getInstance(context);
                hub = new NotificationHub(hubName, listenConnectionString, context);
            }

            public void storeCategoriesAndSubscribe(Set<String> categories)
            {
                SharedPreferences settings = context.getSharedPreferences(PREFS_NAME, 0);
                settings.edit().putStringSet("categories", categories).commit();
                subscribeToCategories(categories);
            }

            public Set<String> retrieveCategories() {
                SharedPreferences settings = context.getSharedPreferences(PREFS_NAME, 0);
                return settings.getStringSet("categories", new HashSet<String>());
            }

            public void subscribeToCategories(final Set<String> categories) {
                new AsyncTask<Object, Object, Object>() {
                    @Override
                    protected Object doInBackground(Object... params) {
                        try {
                            String regid = gcm.register(senderId);
        
                            String templateBodyGCM = "{\"data\":{\"message\":\"$(messageParam)\"}}";
        
                            hub.registerTemplate(regid,"simpleGCMTemplate", templateBodyGCM, 
                                categories.toArray(new String[categories.size()]));
                        } catch (Exception e) {
                            Log.e("MainActivity", "Failed to register - " + e.getMessage());
                            return e;
                        }
                        return null;
                    }
        
                    protected void onPostExecute(Object result) {
                        String message = "Subscribed for categories: "
                                + categories.toString();
                        Toast.makeText(context, message,
                                Toast.LENGTH_LONG).show();
                    }
                }.execute(null, null, null);
            }

        }

    Denne klasse anvender det lokale lager til lagring af de forskellige kategorier af nyheder, er at modtage denne enhed. Den indeholder også metoder til at tilmelde dig disse kategorier.


4. Fjerne din private felter til **NotificationHub** og **GoogleCloudMessaging**i klasse **MainActivity** , og Tilføj et felt til **meddelelser**:

        // private GoogleCloudMessaging gcm;
        // private NotificationHub hub;
        private Notifications notifications;

5. Derefter skal du fjerne initialiseringen af feltet **hub** og metoden **registerWithNotificationHubs** i metoden **onCreate** . Tilføj derefter følgende linjer som initialiseret en forekomst af klassen **beskeder** . 


        protected void onCreate(Bundle savedInstanceState) {
            super.onCreate(savedInstanceState);
            setContentView(R.layout.activity_main);
            MyHandler.mainActivity = this;
    
            NotificationsManager.handleNotifications(this, SENDER_ID,
                    MyHandler.class);
    
            notifications = new Notifications(this, SENDER_ID, HubName, HubListenConnectionString);
    
            notifications.subscribeToCategories(notifications.retrieveCategories());
        }

    `HubName`og `HubListenConnectionString` skal allerede være konfigureret med den `<hub name>` og `<connection string with listen access>` pladsholdere med din anmeldelse hub navn og forbindelsesstrengen for *DefaultListenSharedAccessSignature* , som du tidligere har hentet.

    > [AZURE.NOTE] Da legitimationsoplysninger, som er delt med en klient app ikke generelt sikker, skal du kun fordele tasten for at lytte få adgang til din klient-app. Lyt adgang gør det muligt for din app til at registrere for beskeder, men eksisterende registreringer ikke kan ændres, og meddelelser kan blive sendt. Tasten fuld adgang bruges i en sikret back end-tjeneste til at sende meddelelser og ændre eksisterende registreringer.


6. Tilføj derefter følgende importen og `subscribe` metode til at håndtere knappen Abonner Klik på begivenhed:
        
        import android.widget.CheckBox;
        import java.util.HashSet;
        import java.util.Set;

        public void subscribe(View sender) {
            final Set<String> categories = new HashSet<String>();

            CheckBox world = (CheckBox) findViewById(R.id.worldBox);
            if (world.isChecked())
                categories.add("world");
            CheckBox politics = (CheckBox) findViewById(R.id.politicsBox);
            if (politics.isChecked())
                categories.add("politics");
            CheckBox business = (CheckBox) findViewById(R.id.businessBox);
            if (business.isChecked())
                categories.add("business");
            CheckBox technology = (CheckBox) findViewById(R.id.technologyBox);
            if (technology.isChecked())
                categories.add("technology");
            CheckBox science = (CheckBox) findViewById(R.id.scienceBox);
            if (science.isChecked())
                categories.add("science");
            CheckBox sports = (CheckBox) findViewById(R.id.sportsBox);
            if (sports.isChecked())
                categories.add("sports");

            notifications.storeCategoriesAndSubscribe(categories);
        }

    Denne metode opretter en liste over kategorier og bruger klassen **beskeder** til at gemme listen i det lokale lager og registrere de tilsvarende mærker med din anmeldelse hub. Når kategorier ændres, genskabes registreringen med de nye kategorier.

Din app er nu i stand til at gemme et sæt kategorier i lokale lager på enheden og registrere med hubben besked, når brugeren ændrer markeringen af kategorier.

##<a name="register-for-notifications"></a>Tilmelding til meddelelser

Disse trin Registrer meddelelse hub på starttilstand ved hjælp af de kategorier, der er gemt i lokale lager.

> [AZURE.NOTE] Da registrationId tildelt ved Google Cloud Messaging (GCM) kan ændres til enhver tid, skal du registrere til meddelelser ofte for at undgå besked om fejl. I dette eksempel registrerer til besked om hver gang appen startes. Til apps, der udføres ofte, kan mere end en gang om dagen, du sandsynligvis springe registrering Hvis du vil bevare båndbredde mindre end en dag er gået siden forrige registrering.


1. Føj følgende kode i slutningen af metoden **onCreate** i klassen **MainActivity** :

        notifications.subscribeToCategories(notifications.retrieveCategories());

    Det sikrer, at hver gang appen startes den henter kategorierne fra lokale lager og anmoder om en registeration for disse kategorier. 

2. Derefter kan du opdatere den `onStart()` metode til at den `MainActivity` klasse på følgende måde:

    @Overridebeskyttet ugyldig onStart() {super.onStart();      isVisible = SAND;

        Set<String> categories = notifications.retrieveCategories();

        CheckBox world = (CheckBox) findViewById(R.id.worldBox);
        world.setChecked(categories.contains("world"));
        CheckBox politics = (CheckBox) findViewById(R.id.politicsBox);
        politics.setChecked(categories.contains("politics"));
        CheckBox business = (CheckBox) findViewById(R.id.businessBox);
        business.setChecked(categories.contains("business"));
        CheckBox technology = (CheckBox) findViewById(R.id.technologyBox);
        technology.setChecked(categories.contains("technology"));
        CheckBox science = (CheckBox) findViewById(R.id.scienceBox);
        science.setChecked(categories.contains("science"));
        CheckBox sports = (CheckBox) findViewById(R.id.sportsBox);
        sports.setChecked(categories.contains("sports"));
    }

    Dette opdaterer den primære aktivitet, der er baseret på status for tidligere gemt kategorier.

Appen er nu færdig og kan gemme et sæt kategorier i det lokale enhed-lager, der bruges til at registrere med hubben besked, når brugeren ændrer markeringen af kategorier. Vi vil derefter definere en back-end, der kan sende kategori beskeder til denne app.

##<a name="sending-tagged-notifications"></a>Sende mærket beskeder

[AZURE.INCLUDE [notification-hubs-send-categories-template](../../includes/notification-hubs-send-categories-template.md)]

##<a name="run-the-app-and-generate-notifications"></a>Køre appen og generere beskeder

1. Android Studio byg app, og starte den på en enhed eller emulator.

    Bemærk, at app Brugergrænsefladen indeholder et sæt slår, hvor du kan vælge kategorier for at abonnere på.

2. Aktivere en eller flere kategorier skifter, og derefter klikke på **Abonner**.

    Appen konverterer de valgte kategorier til mærker og anmoder om en anden enhed registrering for de valgte mærker fra meddelelse-hubben. De registrerede kategorier returneres og vises i en toastbeskeden.

4. Sende en ny meddelelse ved at køre .NET Console-app.  Du kan også sende mærket skabelon beskeder under fanen fejlfinding af din meddelelse hub i [Azure klassisk Portal].

    Beskeder for de valgte kategorier vises som toast meddelelser.

##<a name="next-steps"></a>Næste trin

I dette selvstudium lært vi at transmittere seneste nyheder efter kategori. Overvej fuldfører en af følgende selvstudier, der fremhæver andre avancerede meddelelse Hubs scenarier:

+ [Brug meddelelse Hubs transmittere oversatte seneste nyheder]

    Lær at udvide den seneste nyheder app for at aktivere afsendelse oversatte meddelelser.





<!-- Images. -->
[A1]: ./media/notification-hubs-aspnet-backend-android-breaking-news/android-breaking-news1.PNG

<!-- URLs.-->
[get-started]: notification-hubs-android-push-notification-google-gcm-get-started.md
[Brug meddelelse Hubs transmittere oversatte seneste nyheder]: /manage/services/notification-hubs/breaking-news-localized-dotnet/
[Notify users with Notification Hubs]: /manage/services/notification-hubs/notify-users
[Mobile Service]: /develop/mobile/tutorials/get-started/
[Notification Hubs Guidance]: http://msdn.microsoft.com/library/jj927170.aspx
[Notification Hubs How-To for Windows Store]: http://msdn.microsoft.com/library/jj927172.aspx
[Submit an app page]: http://go.microsoft.com/fwlink/p/?LinkID=266582
[My Applications]: http://go.microsoft.com/fwlink/p/?LinkId=262039
[Live SDK for Windows]: http://go.microsoft.com/fwlink/p/?LinkId=262253
[Azure klassisk Portal]: https://manage.windowsazure.com
[wns object]: http://go.microsoft.com/fwlink/p/?LinkId=260591
