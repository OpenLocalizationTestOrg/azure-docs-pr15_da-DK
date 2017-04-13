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

#<a name="how-to-integrate-engagement-on-android"></a>Hvordan du integrerer aftale på Android-enhed

> [AZURE.SELECTOR]
- [Windows Universal](mobile-engagement-windows-store-integrate-engagement.md)
- [Windows Phone Silverlight](mobile-engagement-windows-phone-integrate-engagement.md)
- [iOS](mobile-engagement-ios-integrate-engagement.md)
- [Android](mobile-engagement-android-integrate-engagement.md)

Denne procedure beskriver den nemmeste måde at aktivere aftales analyser og overvågning funktioner i programmet på Android.

> [AZURE.IMPORTANT] Dit mindste Android SDK-API-niveau skal være 10 eller nyere (Android 2.3.3 eller nyere).

Følgende trin er nok til at aktiverer rapport over logfiler, der skal bruges til at beregne alle statistiske oplysninger om brugere, sessioner, aktiviteter, går ned og Technicals. Rapport over logfiler, der skal bruges til at beregne andre statistikker som begivenheder, fejl og job skal udføres manuelt ved hjælp af aftale API (se [Sådan bruges de avancerede Mobile aftale mærkning API i din Android](mobile-engagement-android-use-engagement-api.md) , fordi disse statistikker er afhænger af programmet.

##<a name="embed-the-engagement-sdk-and-service-into-your-android-project"></a>Integrere tjenesten aftale SDK og i projektet Android

Hente Android SDK fra [her](https://aka.ms/vq9mfn) få `mobile-engagement-VERSION.jar` og sætte dem ind i den `libs` mappe på din Android projekt (oprette mappen libs, hvis den ikke findes endnu).

> [AZURE.IMPORTANT]
> Hvis du opretter din programpakke med ProGuard, skal du holde nogle klasser. Du kan bruge den følgende konfiguration kodestykke:
>
>
            -keep public class * extends android.os.IInterface
            -keep class com.microsoft.azure.engagement.reach.activity.EngagementWebAnnouncementActivity$EngagementReachContentJS {
            <methods>;
            }

Angiv din aftale forbindelsesstreng ved at ringe følgende metode starteren aktiviteten:

            EngagementConfiguration engagementConfiguration = new EngagementConfiguration();
            engagementConfiguration.setConnectionString("Endpoint={appCollection}.{domain};AppId={appId};SdkKey={sdkKey}");
            EngagementAgent.getInstance(this).init(engagementConfiguration);

Forbindelsesstrengen for dit program vises på Azure-portalen.

-   Hvis mangler, kan du tilføje følgende Android tilladelser (før den `<application>` mærke):

            <uses-permission android:name="android.permission.INTERNET"/>
            <uses-permission android:name="android.permission.ACCESS_NETWORK_STATE"/>

-   Tilføj følgende afsnit (mellem den `<application>` og `</application>` mærker):

            <service
              android:name="com.microsoft.azure.engagement.service.EngagementService"
              android:exported="false"
              android:label="<Your application name>Service"
              android:process=":Engagement"/>

-   Ændre `<Your application name>` af navnet på dit program.

> [AZURE.TIP] Den `android:label` attribut kan du vælge navnet på tjenesten aftale, der skal vises for slutbrugerne i skærmbilledet "Kører services" i deres telefon. Det anbefales at angive denne attribut til `"<Your application name>Service"` (fx `"AcmeFunGameService"`).

Angive den `android:process` attribut sikrer, at aftale tjenesten kører i sin egen proces (kører aftale i den samme fremgangsmåde, som dit program vil gøre hoved/UI-tråd potentielt mindre svarede).

> [AZURE.NOTE] En hvilken som helst kode, du placerer i `Application.onCreate()` og andre programmer tilbagekald køres for alle programmets processer, herunder tjenesten aftale. Det kan være nødvendigt uønskede side effekter (såsom unødvendige hukommelse tildelinger og tråde i den aftale processen, dublerede broadcast modtagere eller tjenester).

Hvis du tilsidesætte `Application.onCreate()`, det anbefales for at tilføje følgende kodestykke i starten af din `Application.onCreate()` funktionen:

             public void onCreate()
             {
               if (EngagementAgentUtils.isInDedicatedEngagementProcess(this))
                 return;

               ... Your code...
             }

Du kan gøre det samme til `Application.onTerminate()`, `Application.onLowMemory()` og `Application.onConfigurationChanged(...)`.

Du kan også udvide `EngagementApplication` i stedet for at udvide `Application`: tilbagekaldet `Application.onCreate()` betyder kontrollen proces og opkald `Application.onApplicationProcessCreate()` kun hvis den aktuelle proces ikke er et værtstjeneste aftale, de samme regler gælder for de andre tilbagekald.

##<a name="basic-reporting"></a>Grundlæggende rapportering

### <a name="recommended-method-overload-your-activity-classes"></a>Anbefalet metode: overbelastning din `Activity` klasser

For at aktivere rapport over alle de logfiler, der kræves, før aftale til at beregne brugere, sessioner, aktiviteter, går ned og tekniske statistik, skal du kun behøver at gøre alle dine `*Activity` underordnede klasser nedarver fra det tilsvarende `Engagement*Activity` klasser (f.eks. Hvis din ældre aktivitet udvider `ListActivity`, gør det udvider `EngagementListActivity`).

**Uden at aftale:**

            package com.company.myapp;

            import android.app.Activity;
            import android.os.Bundle;

            public class MyApp extends Activity
            {
              @Override
              public void onCreate(Bundle savedInstanceState)
              {
                super.onCreate(savedInstanceState);
                setContentView(R.layout.main);
              }
            }

**Med aftale:**

            package com.company.myapp;

            import com.microsoft.azure.engagement.activity.EngagementActivity;
            import android.os.Bundle;

            public class MyApp extends EngagementActivity
            {
              @Override
              public void onCreate(Bundle savedInstanceState)
              {
                super.onCreate(savedInstanceState);
                setContentView(R.layout.main);
              }
            }

> [AZURE.IMPORTANT] Når du bruger `EngagementListActivity` eller `EngagementExpandableListActivity`, skal du sørge for et opkald til `requestWindowFeature(...);` sker inden opkaldet til `super.onCreate(...);`, ellers nedbrud skal forekomme.

Du kan finde disse klasser i den `src` mappe, og du kan kopiere dem til dit projekt. Klasserne findes også i **JavaDoc**.

### <a name="alternate-method-call-startactivity-and-endactivity-manually"></a>Alternative metode: ringe `startActivity()` og `endActivity()` manuelt

Hvis du ikke kan eller ikke vil overbelastning din `Activity` klasser, kan du i stedet starte og afslutte dine aktiviteter ved at ringe til `EngagementAgent`'s metoder direkte.

> [AZURE.IMPORTANT] Android SDK aldrig ringer på `endActivity()` metode, selvom programmet er lukket (på Android-enhed, programmer er faktisk aldrig lukket). Det er derfor *meget* anbefales til at ringe til den `startActivity()` metode i den `onResume` tilbagekald af *alle* dine aktiviteter og `endActivity()` metode i den `onPause()` tilbagekald af *alle* dine aktiviteter. Dette er den eneste måde at sikre, at sessioner ikke lækkes. Hvis en session er lækket, afbryde tjenesten aftale aldrig forbindelsen aftale back-end (da tjenesten forbliver forbindelsen, så længe en session er ventende).

Her er et eksempel:

            public class MyActivity extends Some3rdPartyActivity
            {
              @Override
              protected void onResume()
              {
                super.onResume();
                String activityNameOnEngagement = EngagementAgentUtils.buildEngagementActivityName(getClass()); // Uses short class name and removes "Activity" at the end.
                EngagementAgent.getInstance(this).startActivity(this, activityNameOnEngagement, null);
              }

              @Override
              protected void onPause()
              {
                super.onPause();
                EngagementAgent.getInstance(this).endActivity();
              }
            }

I dette eksempel minder meget om den `EngagementActivity` klasse og dens varianter, hvis kildekode findes i den `src` mappe.

##<a name="test"></a>Test

Nu skal du kontrollere din integration ved at køre din mobile-app i en emulator eller enhed og ved at bekræfte, at det registrerer en session under fanen skærm.

I næste afsnit er valgfrit.

##<a name="location-reporting"></a>Placering rapportering

Hvis du vil placeringer, der skal rapporteres, du vil tilføje et par linjer af konfigurationen af (mellem den `<application>` og `</application>` mærker).

### <a name="lazy-area-location-reporting"></a>Fløde området placering rapportering

Fløde området placering rapportering giver mulighed for at rapportere land, region og lokalitet, der er knyttet til enheder. Denne type placering rapportering bruger kun netværksplaceringer (baseret på celle ID eller WIFI). Området enhed rapporteres højst én gang hver enkelt session. GPS bruges aldrig, og derfor denne type placering rapport er meget få (ikke at sige Nej) påvirkning af batteriet.

Rapporteret områder bruges til at beregne geografiske statistik om brugere, sessioner, hændelser og fejl. De kan også bruges som kriterium i rækkevidde kampagner.

Hvis du vil aktivere fløde området placering rapportering, kan du gøre det ved hjælp af konfigurationen nævnt tidligere i denne fremgangsmåde:

    EngagementConfiguration engagementConfiguration = new EngagementConfiguration();
    engagementConfiguration.setConnectionString("Endpoint={appCollection}.{domain};AppId={appId};SdkKey={sdkKey}");
    engagementConfiguration.setLazyAreaLocationReport(true);
    EngagementAgent.getInstance(this).init(engagementConfiguration);

Du skal også tilføje tilladelsen følgende, hvis mangler:

            <uses-permission android:name="android.permission.ACCESS_COARSE_LOCATION"/>

Eller du kan fortsætte med at bruge ``ACCESS_FINE_LOCATION`` Hvis du allerede bruger den i programmet.

### <a name="real-time-location-reporting"></a>Realtid placering rapportering

Realtid placering rapportering giver mulighed for at rapportere bredde- og længdegrader, der er knyttet til enheder. Som standard denne type placering rapportering kun bruger netværksplaceringer (baseret på celle ID eller WIFI) og rapportering er kun aktivt, når programmet kører i forgrunden (det vil sige i en session).

Realtid placeringer er *ikke* bruges til at beregne statistik. De kun bruges til at tillade brug af realtid geografisk-indhegning \<rækkevidde-målgruppe-geofencing\> kriterium i rækkevidde kampagner.

Hvis du vil aktivere realtid placering rapportering, kan du gøre det ved hjælp af konfigurationen nævnt tidligere i denne fremgangsmåde:

    EngagementConfiguration engagementConfiguration = new EngagementConfiguration();
    engagementConfiguration.setConnectionString("Endpoint={appCollection}.{domain};AppId={appId};SdkKey={sdkKey}");
    engagementConfiguration.setRealtimeLocationReport(true);
    EngagementAgent.getInstance(this).init(engagementConfiguration);

Du skal også tilføje tilladelsen følgende, hvis mangler:

            <uses-permission android:name="android.permission.ACCESS_COARSE_LOCATION"/>

Eller du kan fortsætte med at bruge ``ACCESS_FINE_LOCATION`` Hvis du allerede bruger den i programmet.

#### <a name="gps-based-reporting"></a>GPS baseret rapportering

Som standard realtid placering rapportering du kun bruger baseret netværksplaceringer. Hvis du vil aktivere brugen af GPS baseret placeringer (som er langt mere præcise), skal du bruge objektet configuration:

    EngagementConfiguration engagementConfiguration = new EngagementConfiguration();
    engagementConfiguration.setConnectionString("Endpoint={appCollection}.{domain};AppId={appId};SdkKey={sdkKey}");
    engagementConfiguration.setRealtimeLocationReport(true);
    engagementConfiguration.setFineRealtimeLocationReport(true);
    EngagementAgent.getInstance(this).init(engagementConfiguration);

Du skal også tilføje tilladelsen følgende, hvis mangler:

            <uses-permission android:name="android.permission.ACCESS_FINE_LOCATION"/>

#### <a name="background-reporting"></a>Rapportering af baggrund

Som standard er realtid placering rapportering kun aktivt, når programmet kører i forgrunden (det vil sige i en session). Brug konfigurationsobjektet til at aktivere rapportering også i baggrunden:

    EngagementConfiguration engagementConfiguration = new EngagementConfiguration();
    engagementConfiguration.setConnectionString("Endpoint={appCollection}.{domain};AppId={appId};SdkKey={sdkKey}");
    engagementConfiguration.setRealtimeLocationReport(true);
    engagementConfiguration.setBackgroundRealtimeLocationReport(true);
    EngagementAgent.getInstance(this).init(engagementConfiguration);

> [AZURE.NOTE] Når programmet kører i baggrunden, kun netværk baseret placeringer rapporteres, selvom du har aktiveret GPS.

Rapporten baggrund placering stoppes, hvis brugeren genstarter enheden, kan du tilføje denne for at gøre det Genstart automatisk ved start:

            <receiver android:name="com.microsoft.azure.engagement.EngagementLocationBootReceiver"
               android:exported="false">
               <intent-filter>
                  <action android:name="android.intent.action.BOOT_COMPLETED" />
               </intent-filter>
            </receiver>

Du skal også tilføje tilladelsen følgende, hvis mangler:

            <uses-permission android:name="android.permission.RECEIVE_BOOT_COMPLETED" />

### <a name="android-m-permissions"></a>Android M tilladelser

Start med Android M, og nogle tilladelser administreres på kørselstidspunktet og skal brugergodkendelse.

Runtime tilladelser kan være deaktiveret som standard for nye app-installationer, hvis du tilpasser Android API niveau 23. Ellers vil det være slået til som standard.

Brugeren kan aktivere/deaktivere disse tilladelser fra menuen enhed indstillinger. Hvis du deaktiverer tilladelser fra systemmenu afbryder baggrundsprocesser af programmet, dette er en systemets funktionsmåde og har ingen indvirkning på muligheden for at modtage opslagsnål i baggrunden.

I forbindelse med Mobile aftale er de tilladelser, der kræver godkendelse på kørselstidspunktet:

- `ACCESS_COARSE_LOCATION`
- `ACCESS_FINE_LOCATION`
- `WRITE_EXTERNAL_STORAGE`(kun når målretning af Android API niveau 23 for denne)

Den eksterne lagerplads bruges kun til rækkevidde overblik funktion. Hvis du finder bede brugere om denne tilladelse til at være forstyrrende, kan du fjerne den Hvis du har brugt den kun for Mobile aftale, men på bekostning af deaktivering af overblik funktion.

Til funktionerne placering, skal du anmode om tilladelser til at bruger ved hjælp af en dialogboks med en standard-systemet. Hvis brugeren godkender, skal du se ``EngagementAgent`` træder ændringen i betragtning i realtid (ellers ændringen behandles næste gang brugeren starter programmet).

Her er en kodeeksempel bruge i en aktivitet af dit program til at anmode om tilladelser og videresende resultatet, hvis den er positiv til ``EngagementAgent``:

    @Override
    public void onCreate(Bundle savedInstanceState)
    {
      /* Other code... */

      /* Request permissions */
      requestPermissions();
    }

    @TargetApi(Build.VERSION_CODES.M)
    private void requestPermissions()
    {
      /* Avoid crashing if not on Android M */
      if (Build.VERSION.SDK_INT >= Build.VERSION_CODES.M)
      {
        /*
         * Request location permission, but this won't explain why it is needed to the user.
         * The standard Android documentation explains with more details how to display a rationale activity to explain the user why the permission is needed in your application.
         * Putting COARSE vs FINE has no impact here, they are part of the same group for runtime permission management.
         */
        if (checkSelfPermission(android.Manifest.permission.ACCESS_FINE_LOCATION) != PackageManager.PERMISSION_GRANTED)
          requestPermissions(new String[] { android.Manifest.permission.ACCESS_FINE_LOCATION }, 0);

        /* Only if you want to keep features using external storage */
        if (checkSelfPermission(android.Manifest.permission.WRITE_EXTERNAL_STORAGE) != PackageManager.PERMISSION_GRANTED)
          requestPermissions(new String[] { android.Manifest.permission.WRITE_EXTERNAL_STORAGE }, 1);
      }
    }

    @Override
    public void onRequestPermissionsResult(int requestCode, String[] permissions, int[] grantResults)
    {
      /* Only a positive location permission update requires engagement agent refresh, hence the request code matching from above function */
      if (requestCode == 0 && grantResults[0] == PackageManager.PERMISSION_GRANTED)
        getEngagementAgent().refreshPermissions();
    }

##<a name="advanced-reporting"></a>Avanceret rapportering

Du kan også, hvis du vil rapportere bestemte programhændelser, fejl og job, du skal bruge aftale API gennem metoder til den `EngagementAgent` klasse. Et objekt i klassen kan være retreived ved at ringe på `EngagementAgent.getInstance()` statisk metode.

Aftale API giver mulighed for at bruge alle aftales avancerede funktioner og er gennemgås i artiklen Sådan bruge API'EN aftale på Android-enhed (og i den tekniske dokumentation for den `EngagementAgent` klasse).

##<a name="advanced-configuration-in-androidmanifestxml"></a>Avanceret konfiguration (i AndroidManifest.xml)

### <a name="wake-locks"></a>Væk låse

Hvis du vil være sikker på, som sendes statistik i realtid, når du bruger Wi-Fi, eller når skærmen er slået fra, kan du tilføje følgende valgfrie tilladelse:

            <uses-permission android:name="android.permission.WAKE_LOCK"/>

### <a name="crash-report"></a>Rapportering af nedbrud

Hvis du vil deaktivere nedbrud rapporter, skal du tilføje denne (mellem den `<application>` og `</application>` mærker):

            <meta-data android:name="engagement:reportCrash" android:value="false"/>

### <a name="burst-threshold"></a>Grænseværdi for burst

Som standard gemmes aftale servicerapporter i realtid. Hvis dit program rapporterer logfiler meget ofte, er det bedre at bufferen logfiler og for at rapportere dem på én gang på almindelig gangen base (dette kaldes "burst-tilstand"). Gør du ved at tilføje denne (mellem den `<application>` og `</application>` mærker):

            <meta-data android:name="engagement:burstThreshold" android:value="{interval between too bursts (in milliseconds)}"/>

Tilstanden burst lidt øge levetiden, men har indflydelse på den aftale skærm: alle sessioner og -job varighed afrundes til grænseværdi for burst (altså sessioner og job kortere end grænseværdi for burst ikke muligvis er synlige). Det anbefales at bruge en burst grænseværdi ikke længere end 30000 (30s).

### <a name="session-timeout"></a>Sessionstimeout

Som standard er en session afsluttet 10 'er efter slutningen af den sidste aktivitet (som normalt forekommer, ved at trykke på tasten Home eller tilbage, ved at angive telefonen inaktiv eller ved at hoppe til et andet program). Dette er at undgå en session opdeling hver gang brugeren Afslut og vende tilbage til programmet meget hurtigt (som kan happen når han vælge et billede, markere en meddelelse, osv.). Du vil ændre denne parameter. Gør du ved at tilføje denne (mellem den `<application>` og `</application>` mærker):

            <meta-data android:name="engagement:sessionTimeout" android:value="{session timeout (in milliseconds)}"/>

##<a name="disable-log-reporting"></a>Deaktivere log rapportering

### <a name="using-a-method-call"></a>Brug af en metode opkald

Hvis du vil aftale at ophøre med at sende logge, kan du ringe:

            EngagementAgent.getInstance(context).setEnabled(false);

Dette opkald er fast: bruges en delt indstillingsfil.

Hvis aftale er aktiv, når du ringer til denne funktion, kan det tage 1 minut for tjenesten stoppe. Men det ikke kan starte tjenesten overhovedet næste gang starter du programmet.

Du kan aktivere log rapportering igen ved at ringe til den samme funktion med `true`.

### <a name="integration-in-your-own-preferenceactivity"></a>Integration i din egen`PreferenceActivity`

Du kan også integrere denne indstilling i stedet for opkald til denne funktion, direkte i din eksisterende `PreferenceActivity`.

Du kan konfigurere aftale at bruge din indstillingsfil (med den ønskede tilstand) i den `AndroidManifest.xml` filen med `application meta-data`:

-   Den `engagement:agent:settings:name` nøgle bruges til at definere navnet på den delte indstillingsfil.
-   Den `engagement:agent:settings:mode` nøgle bruges til at definere tilstanden af den delte indstillingsfil, skal du bruge den samme tilstand som i din `PreferenceActivity`. Tilstanden, der skal overføres som et tal: Hvis du bruger en kombination af konstant flag i din kode, markere den samlede værdi.

Brug af aftale altid den `engagement:key` boolesk nøgle i indstillingsfil til administration af denne indstilling.

Følgende eksempel på `AndroidManifest.xml` viser standardværdierne:

            <application>
                [...]
                <meta-data
                  android:name="engagement:agent:settings:name"
                  android:value="engagement.agent" />
                <meta-data
                  android:name="engagement:agent:settings:mode"
                  android:value="0" />

Du kan tilføje en `CheckBoxPreference` i din præference layout som det følgende:

            <CheckBoxPreference
              android:key="engagement:enabled"
              android:defaultValue="true"
              android:title="Use Engagement"
              android:summaryOn="Engagement is enabled."
              android:summaryOff="Engagement is disabled." />

<!-- URLs. -->
[Device API]: http://go.microsoft.com/?linkid=9876094
