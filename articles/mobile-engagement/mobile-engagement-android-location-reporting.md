<properties
    pageTitle="Placering rapporterer til Azure Mobile aftale Android SDK"
    description="Beskriver, hvordan du konfigurerer placering rapporterer til Azure Mobile aftale Android SDK"
    services="mobile-engagement"
    documentationCenter="mobile"
    authors="piyushjo"
    manager="erikre"
    editor="" />

<tags
    ms.service="mobile-engagement"
    ms.workload="mobile"
    ms.tgt_pltfrm="mobile-android"
    ms.devlang="Java"
    ms.topic="article"
    ms.date="08/12/2016"
    ms.author="piyushjo;ricksal" />

# <a name="location-reporting-for-azure-mobile-engagement-android-sdk"></a>Placering rapporterer til Azure Mobile aftale Android SDK

> [AZURE.SELECTOR]
- [Android](mobile-engagement-android-integrate-engagement.md)

Dette emne beskrives, hvordan du gør placering rapporterer til din Android-program.

## <a name="prerequisites"></a>Forudsætninger

[AZURE.INCLUDE [Prereqs](../../includes/mobile-engagement-android-prereqs.md)]

## <a name="location-reporting"></a>Placering rapportering

Hvis du vil placeringer, der skal rapporteres, du vil tilføje et par linjer af konfigurationen af (mellem den `<application>` og `</application>` mærker).

### <a name="lazy-area-location-reporting"></a>Fløde området placering rapportering

Fløde område for placering af rapportering aktiverer rapportering land, region og lokalitet, der er knyttet til enheder. Denne type placering rapportering bruger kun netværksplaceringer (baseret på celle ID eller WIFI). Området enhed rapporteres højst én gang hver enkelt session. GPS bruges aldrig, og dermed denne type placering rapport har lav indflydelse på batteriet.

Rapporteret områder bruges til at beregne geografiske statistik om brugere, sessioner, hændelser og fejl. De kan også bruges som kriterium i rækkevidde kampagner.

Du aktiverer fløde område for placering af rapportering ved hjælp af konfigurationen nævnt tidligere i denne fremgangsmåde:

    EngagementConfiguration engagementConfiguration = new EngagementConfiguration();
    engagementConfiguration.setConnectionString("Endpoint={appCollection}.{domain};AppId={appId};SdkKey={sdkKey}");
    engagementConfiguration.setLazyAreaLocationReport(true);
    EngagementAgent.getInstance(this).init(engagementConfiguration);

Du skal også angive en placering tilladelse. Denne kode bruger ``COARSE`` tilladelse:

    <uses-permission android:name="android.permission.ACCESS_COARSE_LOCATION"/>

Hvis din app kræver det, kan du bruge ``ACCESS_FINE_LOCATION`` i stedet.

### <a name="real-time-location-reporting"></a>Realtid placering rapportering

Realtid placering rapportering giver mulighed for rapportering bredde- og længdegrader, der er knyttet til enheder. Som standard bruger denne type placering rapportering kun netværksplaceringer, baseret på celle ID eller Wi-Fi. Rapportering er kun aktivt, når programmet kører i forgrunden (for eksempel i en session).

Realtid placeringer er *ikke* bruges til at beregne statistik. De kun bruges til at tillade brug af realtid geografisk-indhegning \<rækkevidde-målgruppe-geofencing\> kriterium i rækkevidde kampagner.

For at aktivere realtid placering rapportering skal du føje en linje af kode til sted, hvor du angive forbindelsesstrengen aftale i starteren aktivitet. Resultatet ser ud som følger:

    EngagementConfiguration engagementConfiguration = new EngagementConfiguration();
    engagementConfiguration.setConnectionString("Endpoint={appCollection}.{domain};AppId={appId};SdkKey={sdkKey}");
    engagementConfiguration.setRealtimeLocationReport(true);
    EngagementAgent.getInstance(this).init(engagementConfiguration);

        You also need to specify a location permission. This code uses ``COARSE`` permission:

            <uses-permission android:name="android.permission.ACCESS_COARSE_LOCATION"/>

        If your app requires it, you can use ``ACCESS_FINE_LOCATION`` instead.

#### <a name="gps-based-reporting"></a>GPS baseret rapportering

Som standard realtid placering rapportering du kun bruger netværk-baserede placeringer. Bruge konfigurationsobjektet for at aktivere brugen af GPS-baserede placeringer, som er langt mere præcis:

    EngagementConfiguration engagementConfiguration = new EngagementConfiguration();
    engagementConfiguration.setConnectionString("Endpoint={appCollection}.{domain};AppId={appId};SdkKey={sdkKey}");
    engagementConfiguration.setRealtimeLocationReport(true);
    engagementConfiguration.setFineRealtimeLocationReport(true);
    EngagementAgent.getInstance(this).init(engagementConfiguration);

Du skal også tilføje tilladelsen følgende, hvis mangler:

    <uses-permission android:name="android.permission.ACCESS_FINE_LOCATION"/>

#### <a name="background-reporting"></a>Rapportering af baggrund

Som standard er realtid placering rapportering kun aktivt, når programmet kører i forgrunden (for eksempel i en session). Brug denne konfigurationsobjekt til at aktivere rapporteringen også i baggrunden:

    EngagementConfiguration engagementConfiguration = new EngagementConfiguration();
    engagementConfiguration.setConnectionString("Endpoint={appCollection}.{domain};AppId={appId};SdkKey={sdkKey}");
    engagementConfiguration.setRealtimeLocationReport(true);
    engagementConfiguration.setBackgroundRealtimeLocationReport(true);
    EngagementAgent.getInstance(this).init(engagementConfiguration);

> [AZURE.NOTE] Når programmet kører i baggrunden, kun netværk-baserede placeringer rapporteres, selvom du har aktiveret GPS.

Hvis brugeren genstarter deres enhed, skal stoppes rapporten baggrund placering. For at gøre det Genstart automatisk på starttidspunktet, skal du tilføje denne kode.

    <receiver android:name="com.microsoft.azure.engagement.EngagementLocationBootReceiver"
           android:exported="false">
        <intent-filter>
            <action android:name="android.intent.action.BOOT_COMPLETED" />
        </intent-filter>
    </receiver>

Du skal også tilføje tilladelsen følgende, hvis mangler:

    <uses-permission android:name="android.permission.RECEIVE_BOOT_COMPLETED" />

## <a name="android-m-permissions"></a>Android M tilladelser

Start med Android M, og nogle tilladelser administreres på kørselstidspunktet og skal brugergodkendelse.

Hvis du tilpasser Android API niveau 23, er runtime tilladelser slået fra som standard for nye app-installationer. Ellers er de aktiveret som standard.

Du kan aktivere/deaktivere disse tilladelser fra menuen enhed indstillinger. Hvis du deaktiverer tilladelser fra systemmenuen afbryder baggrundsprocesser af programmet, som er en systemets funktionsmåde, og har ingen indvirkning på muligheden for at modtage opslagsnål i baggrunden.

I forbindelse med Mobile aftale placering rapportering, er de tilladelser, der kræver godkendelse på kørselstidspunktet:

- `ACCESS_COARSE_LOCATION`
- `ACCESS_FINE_LOCATION`

Anmode om tilladelser fra den bruger, der bruger en dialogboks med en standard-systemet. Hvis brugeren har godkendt, om ``EngagementAgent`` træder ændringen i betragtning i realtid. Ellers behandles ændringen næste gang brugeren starter programmet.

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
         * Request location permission, but this doesn't explain why it is needed to the user.
         * The standard Android documentation explains with more details how to display a rationale activity to explain the user why the permission is needed in your application.
         * Putting COARSE vs FINE has no impact here, they are part of the same group for runtime permission management.
         */
        if (checkSelfPermission(android.Manifest.permission.ACCESS_FINE_LOCATION) != PackageManager.PERMISSION_GRANTED)
          requestPermissions(new String[] { android.Manifest.permission.ACCESS_FINE_LOCATION }, 0);

      }
    }

    @Override
    public void onRequestPermissionsResult(int requestCode, String[] permissions, int[] grantResults)
    {
      /* Only a positive location permission update requires engagement agent refresh, hence the request code matching from above function */
      if (requestCode == 0 && grantResults[0] == PackageManager.PERMISSION_GRANTED)
        getEngagementAgent().refreshPermissions();
    }
