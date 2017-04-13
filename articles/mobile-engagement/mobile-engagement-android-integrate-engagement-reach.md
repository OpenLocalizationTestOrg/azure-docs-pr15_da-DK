<properties
    pageTitle="Azure Mobile aftale Android SDK Integration"
    description="Seneste opdateringer og procedurer for Android SDK til Azure Mobile aftale"
    services="mobile-engagement"
    documentationCenter="mobile"
    authors="piyushjo"
    manager="dwrede"
    editor="" />

<tags
    ms.service="mobile-engagement"
    ms.workload="mobile"
    ms.tgt_pltfrm="mobile-android"
    ms.devlang="Java"
    ms.topic="article"
    ms.date="08/19/2016"
    ms.author="piyushjo" />

#<a name="how-to-integrate-engagement-reach-on-android"></a>Hvordan du integrerer aftale rækkevidde på Android-enhed

> [AZURE.IMPORTANT] Du skal følge fremgangsmåden integration er beskrevet i afsnittet Sådan integrere aftale på Android dokument før følge denne vejledning.

##<a name="standard-integration"></a>Standard-integration

Når SDK kræver **Android Support-bibliotek (v4)**.

Den hurtigste måde at føje biblioteket til dit projekt i **Eklipse** `Right click on your project -> Android Tools -> Add Support Library...`.

Hvis du ikke bruger Eklipse, kan du læse vejledningen [her].

Kopiér rækkevidde ressourcefiler fra SDK i dit projekt:

-   Kopiere filer fra den `res/layout` mappe leveret med SDK i den `res/layout` mappe med dit program.
-   Kopiere filer fra den `res/drawable` mappe leveret med SDK i den `res/drawable` mappe med dit program.

Redigere din `AndroidManifest.xml` fil:

-   Tilføj følgende afsnit (mellem den `<application>` og `</application>` mærker):

            <activity android:name="com.microsoft.azure.engagement.reach.activity.EngagementTextAnnouncementActivity" android:theme="@android:style/Theme.Light" android:exported="false">
              <intent-filter>
                <action android:name="com.microsoft.azure.engagement.reach.intent.action.ANNOUNCEMENT"/>
                <category android:name="android.intent.category.DEFAULT" />
                <data android:mimeType="text/plain" />
              </intent-filter>
            </activity>
            <activity android:name="com.microsoft.azure.engagement.reach.activity.EngagementWebAnnouncementActivity" android:theme="@android:style/Theme.Light" android:exported="false">
              <intent-filter>
                <action android:name="com.microsoft.azure.engagement.reach.intent.action.ANNOUNCEMENT"/>
                <category android:name="android.intent.category.DEFAULT" />
                <data android:mimeType="text/html" />
              </intent-filter>
            </activity>
            <activity android:name="com.microsoft.azure.engagement.reach.activity.EngagementPollActivity" android:theme="@android:style/Theme.Light" android:exported="false">
              <intent-filter>
                <action android:name="com.microsoft.azure.engagement.reach.intent.action.POLL"/>
                <category android:name="android.intent.category.DEFAULT" />
              </intent-filter>
            </activity>
            <activity android:name="com.microsoft.azure.engagement.reach.activity.EngagementLoadingActivity" android:theme="@android:style/Theme.Dialog" android:exported="false">
              <intent-filter>
                <action android:name="com.microsoft.azure.engagement.reach.intent.action.LOADING"/>
                <category android:name="android.intent.category.DEFAULT"/>
              </intent-filter>
            </activity>
            <receiver android:name="com.microsoft.azure.engagement.reach.EngagementReachReceiver" android:exported="false">
              <intent-filter>
                <action android:name="android.intent.action.BOOT_COMPLETED"/>
                <action android:name="com.microsoft.azure.engagement.intent.action.AGENT_CREATED"/>
                <action android:name="com.microsoft.azure.engagement.intent.action.MESSAGE"/>
                <action android:name="com.microsoft.azure.engagement.reach.intent.action.ACTION_NOTIFICATION"/>
                <action android:name="com.microsoft.azure.engagement.reach.intent.action.EXIT_NOTIFICATION"/>
                <action android:name="com.microsoft.azure.engagement.reach.intent.action.DOWNLOAD_TIMEOUT"/>
              </intent-filter>
            </receiver>
            <receiver android:name="com.microsoft.azure.engagement.reach.EngagementReachDownloadReceiver">
              <intent-filter>
                <action android:name="android.intent.action.DOWNLOAD_COMPLETE"/>
              </intent-filter>
            </receiver>

-   Du skal have denne tilladelse til genafspilning systemmeddelelser, der ikke er klikket på ved start (ellers de bevares på disk, men vises ikke længere, skal du virkelig vil medtage dette).

            <uses-permission android:name="android.permission.RECEIVE_BOOT_COMPLETED" />

-   Angive et ikon, der bruges til meddelelser (begge i app og system dem) ved at kopiere og redigere de følgende afsnit (mellem den `<application>` og `</application>` mærker):

            <meta-data android:name="engagement:reach:notification:icon" android:value="<name_of_icon_WITHOUT_file_extension_and_WITHOUT_'@drawable/'>" />

> [AZURE.IMPORTANT] I denne sektion er **obligatorisk** , hvis du har planer om at bruge system beskeder, når du opretter rækkevidde kampagner. Android forhindrer systemmeddelelser uden ikoner i at blive vist. Så hvis du udelader dette afsnit, vil dine slutbrugere ikke kunne modtage dem.

-   Hvis du opretter kampagner med systemmeddelelser ved hjælp af overblik, skal du tilføje følgende tilladelser (efter at den `</application>` mærke) Hvis mangler:

            <uses-permission android:name="android.permission.WRITE_EXTERNAL_STORAGE"/>
            <uses-permission android:name="android.permission.DOWNLOAD_WITHOUT_NOTIFICATION"/>

  -   På Android M, og hvis dit program er beregnet til Android API niveau 23 eller større, ``WRITE_EXTERNAL_STORAGE`` tilladelse kræver brugergodkendelse. Skal du læse [dette afsnit](mobile-engagement-android-integrate-engagement.md#android-m-permissions).

-   For systemmeddelelser kan du også angive i rækkevidde kampagnens, hvis enheden skal ringe/eller vibrationer. Det arbejde, du har at sikre, at du er erklæret tilladelsen følgende (efter at den `</application>` mærke):

            <uses-permission android:name="android.permission.VIBRATE" />

    Uden denne tilladelse Android forhindrer systemmeddelelser i at blive vist, hvis du har markeret ring eller indstillingen vibrate i når kampagnens manager.

-   Hvis du opbygge dit program ved hjælp af **ProGuard** og har fejl, som relaterer til Android Support-bibliotek eller en aftale glas, skal du tilføje følgende linjer til din `proguard.cfg` fil:

            -dontwarn android.**
            -keep class android.support.v4.** { *; }

## <a name="native-push"></a>Oprindelig opslagsnål

Nu hvor du har konfigureret rækkevidde modul, skal du konfigurere oprindelige opslagsnål skal kunne modtage kampagner på enheden.

Vi understøtter to tjenester på Android-enhed:

  - Google Play-enheder: Brug [Google Cloud Messaging] ved at følge vejledningen [Sådan integrere GCM med aftale vejledning](mobile-engagement-android-gcm-integrate.md) .
  - Amazon enheder: Brug [Amazon enhed Messaging] ved at følge vejledningen [Sådan integrere ADM med aftale vejledning](mobile-engagement-android-adm-integrate.md) .

Hvis du vil målrette både Amazon og Google Play enheder, dens muligt at have alt i 1 AndroidManifest.xml/APK for udvikling. Men når du sender til Amazon, de kan afvise dit program, hvis de finder GCM kode.

Du skal bruge flere APKs i så fald.

**Programmet er nu klar til at modtage og få vist rækkevidde kampagner!**

##<a name="how-to-handle-data-push"></a>Sådan håndteres data opslagsnål

### <a name="integration"></a>Integration

Hvis du vil dit program kunne modtage rækkevidde data skubber, du skal oprette en underordnet klasse af `com.microsoft.azure.engagement.reach.EngagementReachDataPushReceiver` og henviser til det i den `AndroidManifest.xml` fil (mellem den `<application>` og/eller `</application>` mærker):

            <receiver android:name="<your_sub_class_of_com.microsoft.azure.engagement.reach.EngagementReachDataPushReceiver>"
              android:exported="false">
              <intent-filter>
                <action android:name="com.microsoft.azure.engagement.reach.intent.action.DATA_PUSH" />
              </intent-filter>
            </receiver>

Og du kan tilsidesætte de `onDataPushStringReceived` og `onDataPushBase64Received` tilbagekald. Her er et eksempel:

            public class MyDataPushReceiver extends EngagementReachDataPushReceiver
            {
              @Override
              protected Boolean onDataPushStringReceived(Context context, String category, String body)
              {
                Log.d("tmp", "String data push message received: " + body);
                return true;
              }

              @Override
              protected Boolean onDataPushBase64Received(Context context, String category, byte[] decodedBody, String encodedBody)
              {
                Log.d("tmp", "Base64 data push message received: " + encodedBody);
                // Do something useful with decodedBody like updating an image view
                return true;
              }
            }

### <a name="category"></a>Kategori

Parameteren kategori er valgfri, når du opretter en Data Push kampagnens og giver mulighed for at filtrere data skubber. Dette er nyttigt, hvis du har flere broadcast modtagere håndtering af forskellige typer data skubber, eller hvis du vil push forskellige typer af `Base64` data og ønsker at identificere deres type før parsing af dem.

### <a name="callbacks-return-parameter"></a>Tilbagekald returnerede parameter

Her er nogle retningslinjer håndtere den returnerede parameter i `onDataPushStringReceived` og `onDataPushBase64Received`:

-   En broadcast modtager skal returnere `null` i tilbagekald, hvis det ikke ved, hvordan du håndterer en opslagsnål data. Du skal bruge kategorien til at afgøre, om din broadcast modtager skal håndtere data opslagsnål eller ej.
-   En af broadcast modtageren skal returnere `true` i tilbagekald, hvis den accepterer data opslagsnål.
-   En af broadcast modtageren skal returnere `false` i tilbagekald, hvis den genkender data opslagsnål, men sletter uanset årsag. For eksempel returnerer `false` når modtager data er ugyldige.
-   Hvis en transmittere modtager returnerer `true` mens en anden en returnerer `false` for de samme data opslagsnål funktionsmåden er ikke defineret, må du aldrig gøre.

Returtypen bruges kun til rækkevidde statistik:

-   `Replied`forøges, hvis en af disse broadcast modtagere returneres enten `true` eller `false`.
-   `Actioned`forøges kun, hvis en af disse broadcast modtagere returneres `true`.

##<a name="how-to-customize-campaigns"></a>Hvordan du tilpasser kampagner

Hvis du vil tilpasse kampagner, kan du ændre de layout, der er angivet i SDK når.

Du skal holde alle de id'er, der bruges i de forskellige layout og bevare typerne af visningerne, bruger et id, især til tekst og billede af visninger. Visse visninger er kun bruges til at skjule eller vise områder, så deres type kan ændres. Kontroller koden, hvis du vil ændre typen af en visning i de medfølgende layout.

### <a name="notifications"></a>Beskeder

Der findes to typer beskeder: system og i app'en meddelelser som bruge forskellige layout filer.

#### <a name="system-notifications"></a>Systemmeddelelser

Hvis du vil tilpasse systemmeddelelser skal du bruge **kategorier**. Du kan springe til [kategorier](#categories).

#### <a name="in-app-notifications"></a>I app-meddelelser

En appmeddelelse er som standard en visning, der er tilføjes dynamisk til den aktuelle aktivitet brugergrænseflade sammen takket være metoden Android `addContentView()`. Dette kaldes en meddelelse om overlejring. Meddelelse om overlejrer er gode til en hurtig integration, fordi de ikke kræver muligt at ændre en hvilken som helst layout i dit program.

Hvis du vil ændre udseendet af din meddelelse overlejrer, kan du bare redigere filen `engagement_notification_area.xml` til dine behov.

> [AZURE.NOTE] Filen `engagement_notification_overlay.xml` er det, der bruges til at oprette en meddelelse om overlejring, den indeholder filen `engagement_notification_area.xml`. Du kan også tilpasse den efter dine behov (f.eks. til placering meddelelsesområdet i masken).

##### <a name="include-notification-layout-as-part-of-an-activity-layout"></a>Medtage meddelelse layout som en del af en aktivitet layout

Overlejrer kan er gode til at få en hurtig integration, men være upraktisk eller have side effekter i særlige tilfælde. Overlejring systemet kan tilpasses på en aktivitetsniveau, hvilket gør det nemt at forhindre, at side effekter til særlige aktiviteter.

Du kan vælge at medtage vores meddelelse layout i din eksisterende layout sammen takket være sætningen Android **medtage** . Følgende er et eksempel på en ændret `ListActivity` layout der indeholder kun en `ListView`.

**Før aftale integration:**

            <?xml version="1.0" encoding="utf-8"?>
            <ListView
              xmlns:android="http://schemas.android.com/apk/res/android"
              android:id="@android:id/list"
              android:layout_width="fill_parent"
              android:layout_height="fill_parent" />

**Efter aftale integration:**

            <?xml version="1.0" encoding="utf-8"?>
            <LinearLayout
              xmlns:android="http://schemas.android.com/apk/res/android"
              android:orientation="vertical"
              android:layout_width="fill_parent"
              android:layout_height="fill_parent">

              <ListView
                android:id="@android:id/list"
                android:layout_width="fill_parent"
                android:layout_height="fill_parent"
                android:layout_weight="1" />

              <include layout="@layout/engagement_notification_area" />

            </LinearLayout>

I dette eksempel tilføjet vi en overordnet beholder, da det oprindelige layout bruges en listevisning som elementet på øverste niveau. Vi også tilføjet `android:layout_weight="1"` lov til at tilføje en visning under en listevisning, der er konfigureret med `android:layout_height="fill_parent"`.

Aftale når SDK registrerer automatisk, meddelelse om layout er inkluderet i denne aktivitet og føjer ikke en overlejring for denne aktivitet.

> [AZURE.TIP] Hvis du bruger en ListActivity i dit program, forhindre en synlig rækkevidde overlejring dig i reagerer på klikkede på elementer i listevisningen længere. Dette er et kendt problem. Vi anbefaler dig for at integrere layoutet meddelelse i din egen liste aktivitet layout som i forrige eksempel for at løse dette problem.

##### <a name="disabling-application-notification-per-activity"></a>Deaktivere programmet meddelelse per aktivitet

Hvis du ikke vil overlejring der skal føjes til din aktivitet, og hvis du ikke indeholder layoutet meddelelse i dit eget layout, kan du deaktivere overlejring for denne aktivitet i den `AndroidManifest.xml` ved at tilføje en `meta-data` sektion som i følgende eksempel:

            <activity android:name="SplashScreenActivity">
              <meta-data android:name="engagement:notification:overlay" android:value="false"/>
            </activity>

#### <a name="categories"></a>Kategorier

Når du ændrer de medfølgende layout, kan du ændre udseendet af alle dine meddelelser. Kategorier kan du angive forskellige målrettede ser (muligvis funktionsmåder) til meddelelser. Du kan angive en kategori, når du opretter en rækkevidde campaign. Husk på, at kategorier kan du også tilpasse meddelelser og afstemninger, der er beskrevet senere i dette dokument.

Hvis du vil registrere en kategori handler til dine meddelelser, skal du tilføje et opkald, når programmet startes.

> [AZURE.IMPORTANT] Læs advarslen om attributten android: proces \<android-sdk-aftale-process\> i hvordan du kan integrere aftale på Android emne før du fortsætter.

I følgende eksempel antages det, du har bekræftet forrige advarslen og bruge en underordnet klasse af `EngagementApplication`:

            public class MyApplication extends EngagementApplication
            {
              @Override
              protected void onApplicationProcessCreate()
              {
                // [...] other init
                EngagementReachAgent reachAgent = EngagementReachAgent.getInstance(this);
                reachAgent.registerNotifier(new MyNotifier(this), "myCategory");
              }
            }

Den `MyNotifier` objekt er implementering af besked om kategori handler. Det er enten en implementering af den `EngagementNotifier` interface eller en sub klasse af standardimplementering: `EngagementDefaultNotifier`.

Bemærk, at den samme anmelder kan håndtere flere kategorier, kan du registrere dem således:

            reachAgent.registerNotifier(new MyNotifier(this), "myCategory", "myAnotherCategory");

Hvis du vil erstatte standard kategori implementering, kan du registrere implementeringen som i følgende eksempel:

            public class MyApplication extends EngagementApplication
            {
              @Override
              protected void onApplicationProcessCreate()
              {
                // [...] other init
                EngagementReachAgent reachAgent = EngagementReachAgent.getInstance(this);
                reachAgent.registerNotifier(new MyNotifier(this), Intent.CATEGORY_DEFAULT); // "android.intent.category.DEFAULT"
              }
            }

Den aktuelle kategori, der bruges i en handler der overføres som en parameter i de fleste metoder, du kan tilsidesætte i `EngagementDefaultNotifier`.

Det er gået enten som en `String` parameter eller indirekte i en `EngagementReachContent` objekt, der har en `getCategory()` metode.

Du kan ændre de fleste af oprettelsen af meddelelse ved omdefinere metoder på `EngagementDefaultNotifier`, til mere avanceret tilpasning Velkommen til at se nærmere på den tekniske dokumentation og på koden.

##### <a name="in-app-notifications"></a>I app-meddelelser

Hvis du kun vil bruge alternative layout for en bestemt kategori, kan du implementere dette som i følgende eksempel:

            public class MyNotifier extends EngagementDefaultNotifier
            {
              public MyNotifier(Context context)
              {
                super(context);
              }

              @Override
              protected int getOverlayLayoutId(String category)
              {
                return R.layout.my_notification_overlay;
              }


              @Override
              public Integer getOverlayViewId(String category)
              {
                return R.id.my_notification_overlay;
              }

              @Override
              public Integer getInAppAreaId(String category)
              {
                return R.id.my_notification_area;
              }
            }

**Eksempel på `my_notification_overlay.xml` :**

            <?xml version="1.0" encoding="utf-8"?>
            <RelativeLayout
              xmlns:android="http://schemas.android.com/apk/res/android"
              android:id="@+id/my_notification_overlay"
              android:layout_width="fill_parent"
              android:layout_height="fill_parent">

              <include layout="@layout/my_notification_area" />

            </RelativeLayout>

Overlejring visnings-id er anderledes end den standard, som du kan se. Det er vigtigt, at hvert enkelt layout bruger et entydigt id for overlejrer.

**Eksempel på `my_notification_area.xml` :**

            <?xml version="1.0" encoding="utf-8"?>
            <merge
              xmlns:android="http://schemas.android.com/apk/res/android"
              android:layout_width="fill_parent"
              android:layout_height="fill_parent">

              <RelativeLayout
                android:id="@+id/my_notification_area"
                android:layout_width="fill_parent"
                android:layout_height="64dp"
                android:layout_alignParentTop="true"
                android:background="#B000">

                <LinearLayout
                  android:orientation="horizontal"
                  android:layout_width="fill_parent"
                  android:layout_height="fill_parent"
                  android:gravity="center_vertical">

                  <ImageView
                    android:id="@+id/engagement_notification_icon"
                    android:layout_width="48dp"
                    android:layout_height="48dp" />

                  <LinearLayout
                    android:id="@+id/engagement_notification_text"
                    android:orientation="vertical"
                    android:layout_width="fill_parent"
                    android:layout_height="fill_parent"
                    android:layout_weight="1"
                    android:gravity="center_vertical">

                    <TextView
                      android:id="@+id/engagement_notification_title"
                      android:layout_width="fill_parent"
                      android:layout_height="wrap_content"
                      android:singleLine="true"
                      android:ellipsize="end"
                      android:textAppearance="@android:style/TextAppearance.Medium" />

                    <TextView
                      android:id="@+id/engagement_notification_message"
                      android:layout_width="fill_parent"
                      android:layout_height="wrap_content"
                      android:maxLines="2"
                      android:ellipsize="end"
                      android:textAppearance="@android:style/TextAppearance.Small" />

                  </LinearLayout>

                  <ImageView
                    android:id="@+id/engagement_notification_image"
                    android:layout_width="wrap_content"
                    android:layout_height="fill_parent"
                    android:adjustViewBounds="true" />

                  <ImageButton
                    android:id="@+id/engagement_notification_close_area"
                    android:visibility="invisible"
                    android:layout_width="wrap_content"
                    android:layout_height="fill_parent"
                    android:src="@android:drawable/btn_dialog"
                    android:background="#0F00" />

                </LinearLayout>

                <ImageButton
                  android:id="@+id/engagement_notification_close"
                  android:layout_width="wrap_content"
                  android:layout_height="fill_parent"
                  android:layout_alignParentRight="true"
                  android:src="@android:drawable/btn_dialog"
                  android:background="#0F00" />

              </RelativeLayout>

            </merge>

Som du kan se, er meddelelse om område visning-id'et anderledes end den standard. Det er vigtigt, at hvert enkelt layout bruger et entydigt id for meddelelse områder.

Dette simple eksempel på kategori gør programmet (eller i app'en) meddelelser, der vises øverst på skærmen. Vi har ikke ændret standard id'erne bruges i meddelelsesområdet sig selv.

Hvis du vil ændre det, du har omdefinere den `EngagementDefaultNotifier.prepareInAppArea` metode. Det anbefales at ser ud på den tekniske dokumentation og på kildekode af `EngagementNotifier` og `EngagementDefaultNotifier` Hvis du ønsker dette niveau af Avanceret tilpasning.

##### <a name="system-notifications"></a>Systemmeddelelser

Ved at forlænge `EngagementDefaultNotifier`, du kan tilsidesætte `onNotificationPrepared` til at ændre de meddelelser, der er udarbejdet af standardimplementering.

Eksempel:

            @Override
            protected boolean onNotificationPrepared(Notification notification, EngagementReachInteractiveContent content)
              throws RuntimeException
            {
              if ("ongoing".equals(content.getCategory()))
                notification.flags |= Notification.FLAG_ONGOING_EVENT;
              return true;
            }

I dette eksempel giver en systemmeddelelse i efter en indhold, der vises som en igangværende hændelse, når kategorien "igangværende" bruges.

Hvis du vil oprette den `Notification` objekt fra bunden, kan du vende tilbage `false` til metode og opkald `notify` dig selv på den `NotificationManager`. Det er tilfældet, er det vigtigt, at du holder en `contentIntent`, en `deleteIntent` og meddelelse id'et bruges af `EngagementReachReceiver`.

Her er en korrekte eksempel på en sådan gennemførelse:

            @Override
            protected boolean onNotificationPrepared(Notification notification, EngagementReachInteractiveContent content) throws RuntimeException
            {
              /* Required fields */
              NotificationCompat.Builder builder = new NotificationCompat.Builder(mContext)
                .setSmallIcon(notification.icon)              // icon is mandatory
                .setContentIntent(notification.contentIntent) // keep content intent
                .setDeleteIntent(notification.deleteIntent);  // keep delete intent

              /* Your customization */
              // builder.set...

              /* Dismiss option can be managed only after build */
              Notification myNotification = builder.build();
              if (!content.isNotificationCloseable())
                myNotification.flags |= Notification.FLAG_NO_CLEAR;

              /* Notify here instead of super class */
              NotificationManager manager = (NotificationManager) mContext.getSystemService(Context.NOTIFICATION_SERVICE);
              manager.notify(getNotificationId(content), myNotification); // notice the call to get the right identifier

              /* Return false, we notify ourselves */
              return false;
            }

##### <a name="notification-only-announcements"></a>Meddelelse om kun meddelelser

For administration af Klik på en meddelelse om Familieforøgelse kun kan tilpasses ved at tilsidesætte `EngagementDefaultNotifier.onNotifAnnouncementIntentPrepared` til at ændre den forberedt `Intent`. Ved hjælp af denne metode kan du nemt finjustere flagene.

For eksempel for at tilføje den `SINGLE_TOP` flag:

            @Override
            protected Intent onNotifAnnouncementIntentPrepared(EngagementNotifAnnouncement notifAnnouncement,
              Intent intent)
            {
              intent.addFlags(Intent.FLAG_ACTIVITY_SINGLE_TOP);
              return intent;
            }

For brugere af ældre aftale, Vær opmærksom på, at systemmeddelelser uden handling URL-adressen nu åbner programmet Hvis den er i baggrunden, så denne metode kan kaldes med en meddelelse uden handling URL-adresse. Du skal overveje, når du tilpasser formålet.

Du kan også implementere `EngagementNotifier.executeNotifAnnouncementAction` fra bunden.

##### <a name="notification-life-cycle"></a>Meddelelse om livscyklus

Når du bruger standardkategorien, nogle livscyklus metoder er navnet på den `EngagementReachInteractiveContent` objekt til rapport statistik og opdatere tilstanden kampagnens:

-   Når meddelelsen vises i programmet på computeren eller sætte på statuslinjen, den `displayNotification` metode kaldes (som rapporter statistik) ved `EngagementReachAgent` Hvis `handleNotification` returnerer `true`.
-   Hvis meddelelsen er lukket, den `exitNotification` metode kaldes, statistikken rapporteres og næste kampagner kan nu behandles.
-   Hvis meddelelsen er markeret, `actionNotification` er kaldet statistikken rapporteres og tilknyttede formålet startes.

Hvis din implementering af `EngagementNotifier` tilsidesætte standardfunktionsmåden, du skal ringe til disse livscyklus metoder ved dig selv. Følgende eksempler viser nogle tilfælde, hvor standardfunktionen er tilsidesat:

-   Du ikke kan udvide `EngagementDefaultNotifier`, f.eks. du implementeret håndtering af kategori fra bunden.
-   For systemmeddelelser, du overrode den `onNotificationPrepared` og at du har redigeret `contentIntent` eller `deleteIntent` i den `Notification` objekt.
-   Til i app'en meddelelser, du overrode `prepareInAppArea`, skal du sørge for at tilknytte mindst `actionNotification` til en af dine U.I-kontrolelementer.

> [AZURE.NOTE] Hvis `handleNotification` udløses en undtagelse, indholdet slettes og `dropContent` hedder. Dette rapporteres i statistik og næste kampagner kan nu behandles.

### <a name="announcements-and-polls"></a>Meddelelser og afstemninger

#### <a name="layouts"></a>Layout

Du kan ændre den `engagement_text_announcement.xml`, `engagement_web_announcement.xml` og `engagement_poll.xml` filer for at tilpasse tekst meddelelser, web meddelelser og afstemninger.

Disse filer dele to almindelige layout til titelområdet og området knappen. Layout til titlen er `engagement_content_title.xml` og bruger eponymous drawable filen til baggrunden. Layout til handling og Afslut knapperne er `engagement_button_bar.xml` og bruger eponymous drawable filen til baggrunden.

I en afstemning, spørgsmål layout og deres valgmuligheder dynamisk er oppumpet ved hjælp af flere gange på `engagement_question.xml` layout-fil til spørgsmålene og `engagement_choice.xml` fil, som valgmulighederne.

#### <a name="categories"></a>Kategorier

##### <a name="alternate-layouts"></a>Alternative layout

Sådan meddelelser, kan kampagnens kategori bruges til at har alternative layout til dine meddelelser og afstemninger.

For eksempel for at oprette en kategori til en tekst-meddelelse, du kan udvide `EngagementTextAnnouncementActivity` og referere til den den `AndroidManifest.xml` fil:

            <activity android:name="com.your_company.MyCustomTextAnnouncementActivity">
              <intent-filter>
                <action android:name="com.microsoft.azure.engagement.reach.intent.action.ANNOUNCEMENT"/>
                <category android:name="my_category" />
                <data android:mimeType="text/plain" />
              </intent-filter>
            </activity>

Bemærk, at kategorien i formålet filtrere bruges til at gøre forskel med standard meddelelse aktiviteter.

Når SDK bruger bruges administrationssiden systemet til at løse den rigtige aktivitet for en bestemt kategori, og det går tilbage i standardkategorien, hvis det lykkedes.

Så har du at implementere `MyCustomTextAnnouncementActivity`, hvis du blot ønsker at ændre layoutet (men bevare samme visning id'erne), du lige har til at definere klassen som i følgende eksempel:

            public class MyCustomTextAnnouncementActivity extends EngagementTextAnnouncementActivity
            {
              @Override
              protected String getLayoutName()
              {
                return "my_text_announcement";  // tell super class to use R.layout.my_text_announcement
              }
            }

Hvis du vil erstatte standardkategorien i tekst meddelelser, skal du bare erstatte `android:name="com.microsoft.azure.engagement.reach.activity.EngagementTextAnnouncementActivity"` ved implementeringen.

Web meddelelser og afstemninger kan tilpasses i en lignende måde.

Web meddelelser du kan udvide `EngagementWebAnnouncementActivity` og erklære din aktivitet på den `AndroidManifest.xml` som i følgende eksempel:

            <activity android:name="com.your_company.MyCustomWebAnnouncementActivity">
              <intent-filter>
                <action android:name="com.microsoft.azure.engagement.reach.intent.action.ANNOUNCEMENT"/>
                <category android:name="my_category" />
                <data android:mimeType="text/html" />    <!-- only difference with text announcements in the intent is the data mime type -->
              </intent-filter>
            </activity>

Afstemninger du kan udvide `EngagementPollActivity` og erklære din i den `AndroidManifest.xml` som i følgende eksempel:

            <activity android:name="com.your_company.MyCustomPollActivity">
              <intent-filter>
                <action android:name="com.microsoft.azure.engagement.reach.intent.action.POLL"/>
                <category android:name="my_category" />
              </intent-filter>
            </activity>

##### <a name="implementation-from-scratch"></a>Implementering fra bunden

Du kan implementere kategorier til din meddelelse (og afstemning) aktiviteter uden at udvide en af de `Engagement*Activity` klasser, der leveres af når SDK. Dette er nyttigt for eksempel hvis du vil definere et layout, der ikke bruger de samme visninger, som de standardlayout.

Ud for avancerede meddelelse opsætning anbefales det at kigge på kildekode af standard implementeringen.

Her er nogle ting at huske på: rækkevidde åbne aktivitet med et bestemt formål (i overensstemmelse med filteret bruges administrationssiden) plus en ekstra parameter, som er det indhold id.

Hvis du vil hente objektet indhold, der indeholder de felter, du har angivet, når du opretter kampagnens på webstedet kan du gøre dette:

            public class MyCustomTextAnnouncement extends EngagementActivity
            {
              private EngagementAnnouncement mContent;

              @Override
              protected void onCreate(Bundle savedInstanceState)
              {
                super.onCreate(savedInstanceState);

                /* Get content */
                mContent = EngagementReachAgent.getInstance(this).getContent(getIntent());
                if (mContent == null)
                {
                  /* If problem with content, exit */
                  finish();
                  return;
                }

                setContentView(R.layout.my_text_announcement);

                /* Configure views by querying fields on mContent */
                // ...
              }
            }

For statistik, skal du rapportere indholdet vises i den `onResume` begivenhed:

            @Override
            protected void onResume()
            {
             /* Mark the content displayed */
             mContent.displayContent(this);
             super.onResume();
            }

Derefter Glem ikke at ringe til enten `actionContent(this)` eller `exitContent(this)` på objektet indhold før aktiviteten går til baggrunden.

Hvis du ikke kan ringe til enten `actionContent` eller `exitContent`, statistik flyttes ikke (det vil sige ingen analytics på kampagnens) og vigtigere, de næste kampagner vil ikke få besked, før ansøgningsprocedure genstartes.

Papirretning eller andre ændringer i konfigurationen kan foretage koden svære at finde ud af, om aktiviteten er i baggrunden eller ej, standard implementeringen sikrer, at indholdet rapporteres som afsluttet, hvis brugeren forlader aktiviteten (enten ved at trykke på `HOME` eller `BACK`), men ikke hvis papirretningen ændres.

Her er den interessante del af implementeringen:

            @Override
            protected void onUserLeaveHint()
            {
              finish();
            }

            @Override
            protected void onPause()
            {
              if (isFinishing() && mContent != null)
              {
                /*
                 * Exit content on exit, this is has no effect if another process method has already been
                 * called so we don't have to check anything here.
                 */
                mContent.exitContent(this);
              }
              super.onPause();
            }

Som du kan se, hvis du har kaldt `actionContent(this)` og derefter færdig aktiviteten, `exitContent(this)` sikkert kan kaldes uden en effekt.

[Her]:http://developer.android.com/tools/extras/support-library.html#Downloading
[Google Cloud-beskeder]:http://developer.android.com/guide/google/gcm/index.html
[Amazon enhed Messaging]:https://developer.amazon.com/sdk/adm.html
