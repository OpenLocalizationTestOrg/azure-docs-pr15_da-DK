<properties
    pageTitle="Introduktion til Android-Apps Azure Mobile aftale"
    description="Lær at bruge Azure Mobile aftale med analyser og push-beskeder til Android-apps."
    services="mobile-engagement"
    documentationCenter="android"
    authors="piyushjo"
    manager="erikre"
    editor="" />

<tags
    ms.service="mobile-engagement"
    ms.workload="mobile"
    ms.tgt_pltfrm="mobile-android"
    ms.devlang="Java"
    ms.topic="hero-article"
    ms.date="08/10/2016"
    ms.author="piyushjo;ricksal" />

# <a name="get-started-with-azure-mobile-engagement-for-android-apps"></a>Introduktion til Azure Mobile aftale til Android-apps

[AZURE.INCLUDE [Hero tutorial switcher](../../includes/mobile-engagement-hero-tutorial-switcher.md)]

Dette emne viser, hvordan du bruger Azure Mobile aftale for at forstå din appforbrug og hvordan du kan sende pushmeddelelser for segmenteret brugere af et Android-program.
Dette selvstudium viser simple broadcast scenariet ved hjælp af Mobile aftale. I det, kan du oprette en tom Android app, som indsamler grundlæggende data og modtager pushmeddelelser ved hjælp af Google Cloud Messaging (GCM).

## <a name="prerequisites"></a>Forudsætninger

Fuldfør dette selvstudium kræver [Android udviklerværktøjer](https://developer.android.com/sdk/index.html), som indeholder det integrerede udviklingsmiljø Android Studio, og den seneste Android platform.

Det kræver også [Mobile aftale Android SDK](https://aka.ms/vq9mfn).

> [AZURE.IMPORTANT] For at fuldføre dette selvstudium skal have du en aktiv Azure konto. Hvis du ikke har en konto, kan du oprette en gratis prøveversion konto på blot et par minutter. Yderligere oplysninger finder du [Azure gratis prøveversion](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Fen-us%2Fdocumentation%2Farticles%2Fmobile-engagement-android-get-started).

## <a name="set-up-mobile-engagement-for-your-android-app"></a>Konfigurere Mobile aftale til din Android-appen

[AZURE.INCLUDE [Create Mobile Engagement App in Portal](../../includes/mobile-engagement-create-app-in-portal-new.md)]

## <a name="connect-your-app-to-the-mobile-engagement-backend"></a>Oprette forbindelse din app til Mobile aftale back-end

Dette selvstudium indeholder en "grundlæggende integration", som er sættet minimale, der kræves for at indsamle data og sende en meddelelse om opslagsnål. Du kan oprette en grundlæggende app med Android Studio til at vise integrationen.

Fuldstændig integration dokumentation kan findes i [Mobile aftale Android SDK integration](mobile-engagement-android-sdk-overview.md).

### <a name="create-an-android-project"></a>Oprette et Android projekt

1. Start **Android Studio**, og vælg i pop op-vinduet, **starte et nyt Android Studio-projekt**.

    ![][1]

2. Angiv et app-navn og virksomhedens domæne. Skal du notere hvad du udfylder, fordi du bruge den senere. Klik på **Næste**.

    ![][2]

3. Vælg destination formular faktor og API niveau, og klik på **Næste**.

    >[AZURE.NOTE] Mobile aftale kræver API niveau 10 mindste (Android 2.3.3).

    ![][3]

4. Vælg **Tom aktivitet** hertil, som er det kun skærmbillede for denne app, og klik på **Næste**.

    ![][4]

5. Til sidst skal du lade standardindstillingerne, som er, og klik på **Udfør**.

    ![][5]

Android Studio opretter nu demo appen, hvor vi integrere Mobile aftale.

### <a name="include-the-sdk-library-in-your-project"></a>Medtage biblioteket SDK i dit projekt

1. Hent [Mobile aftale Android SDK](https://aka.ms/vq9mfn).
2. Udtrække arkivfilen til en mappe på computeren.
3. Identificere biblioteket .jar for den aktuelle version af dette SDK og kopiere det til Udklipsholder.

      ![][6]

4. Gå til sektionen **projekt** (1) og Indsæt .jar i mappen libs (2).

      ![][7]

5. For at indlæse biblioteket skal du synkronisere projektet.

      ![][8]

### <a name="connect-your-app-to-mobile-engagement-backend-with-the-connection-string"></a>Oprette forbindelse din app til Mobile aftale backend-version med forbindelsesstrengen

1. Kopiere de følgende kodelinjer til aktivitet oprettelse (skal udføres på ét sted i dit program, som regel den primære aktivitet). I dette eksempel-program, Åbn MainActivity under src -> hoved -> java mappe og tilføje følgende:

        EngagementConfiguration engagementConfiguration = new EngagementConfiguration();
        engagementConfiguration.setConnectionString("Endpoint={appCollection}.{domain};AppId={appId};SdkKey={sdkKey}");
        EngagementAgent.getInstance(this).init(engagementConfiguration);

2. Løse referencerne ved at trykke på Alt + Enter eller tilføje følgende udtalelser import:

        import com.microsoft.azure.engagement.EngagementAgent;
        import com.microsoft.azure.engagement.EngagementConfiguration;

3. Gå tilbage til portalen Azure klassisk din app **Forbindelsesoplysninger** på siden, og Kopiér **Forbindelsesstrengen**.

      ![][9]

4. Indsætte det i den `setConnectionString` parameter, erstatte hele strengen vist i følgende kode:

        engagementConfiguration.setConnectionString("Endpoint=my-company-name.device.mobileengagement.windows.net;SdkKey=********************;AppId=*********");

### <a name="add-permissions-and-a-service-declaration"></a>Tilføj tilladelser og en tjeneste-erklæring

1. Føj disse tilladelser til Manifest.xml af projektet, umiddelbart før eller efter de `<application>` mærke:

        <uses-permission android:name="android.permission.INTERNET"/>
        <uses-permission android:name="android.permission.ACCESS_NETWORK_STATE"/>
        <uses-permission android:name="android.permission.WRITE_EXTERNAL_STORAGE"/>
        <uses-permission android:name="android.permission.RECEIVE_BOOT_COMPLETED" />
        <uses-permission android:name="android.permission.VIBRATE" />
        <uses-permission android:name="android.permission.DOWNLOAD_WITHOUT_NOTIFICATION"/>

2. Hvis du vil erklære agenttjenesten, tilføje denne kode mellem den `<application>` og `</application>` mærker:

        <service
            android:name="com.microsoft.azure.engagement.service.EngagementService"
            android:exported="false"
            android:label="<Your application name>"
            android:process=":Engagement"/>

3. I den kode, du har indsat, skal du erstatte `"<Your application name>"` i navnet, som vises i menuen **Indstillinger** , hvor du kan se services kører på enheden. Du kan tilføje ordet "Service" i, der markerer, f.eks.

### <a name="send-a-screen-to-mobile-engagement"></a>Sende en skærm til Mobile aftale

For at starte sender data og sikre, at brugerne er aktiv, skal du sende mindst ét skærmbillede (aktivitet) til Mobile aftale back-end.

Gå til **MainActivity.java** og tilføje følgende for at erstatte den grundlæggende klasse af **MainActivity** til **EngagementActivity**:

    public class MainActivity extends EngagementActivity {

> [AZURE.NOTE] Hvis din grundlæggende klasse ikke er *aktivitet*, du høre [Avancerede Android rapportering](mobile-engagement-android-advanced-reporting.md#modifying-your-codeactivitycode-classes) om, hvordan du nedarver fra forskellige klasser.


Skrive en kommentar ud følgende linje i dette scenarie simpelt eksempel:

    // setSupportActionBar(toolbar);

Hvis du vil beholde den `ActionBar` i din app, se [Avancerede Android rapportering](mobile-engagement-android-advanced-reporting.md#modifying-your-codeactivitycode-classes).

## <a name="connect-app-with-real-time-monitoring"></a>Oprette forbindelse app med overvågning i realtid

[AZURE.INCLUDE [Connect app with real-time monitoring](../../includes/mobile-engagement-connect-app-with-monitor.md)]

## <a name="enable-push-notifications-and-in-app-messaging"></a>Aktivér pushmeddelelser og i app-beskeder

Under en kampagnens kan Mobile aftale du interagere med og få adgang til dine brugere med pushmeddelelser og i app-beskeder. Dette modul kaldes rækkevidde på portalen Mobile aftale.
Følgende afsnit konfigurerer din app til at modtage dem.

### <a name="copy-sdk-resources-in-your-project"></a>Kopiere SDK ressourcer i projektet

1. Gå tilbage til dit SDK Hent indhold, og Kopiér mappen **rejser** .

    ![][10]

2. Gå tilbage til Android Studio, Vælg den **primære** mappe på din project-filer, og Indsæt derefter den for at føje ressourcer til projektet.

    ![][11]

[AZURE.INCLUDE [Enable Google Cloud Messaging](../../includes/mobile-engagement-enable-google-cloud-messaging.md)]

[AZURE.INCLUDE [Enable in-app messaging](../../includes/mobile-engagement-android-send-push.md)]

[AZURE.INCLUDE [Send notification from portal](../../includes/mobile-engagement-android-send-push-from-portal.md)]

## <a name="next-steps"></a>Næste trin

Gå til [Android SDK](mobile-engagement-android-sdk-overview.md) at få detaljeret viden om SDK-integration.

<!-- Images. -->
[1]: ./media/mobile-engagement-android-get-started/android-studio-new-project.png
[2]: ./media/mobile-engagement-android-get-started/android-studio-project-props.png
[3]: ./media/mobile-engagement-android-get-started/android-studio-project-props2.png
[4]: ./media/mobile-engagement-android-get-started/android-studio-add-activity.png
[5]: ./media/mobile-engagement-android-get-started/android-studio-activity-name.png
[6]: ./media/mobile-engagement-android-get-started/sdk-content.png
[7]: ./media/mobile-engagement-android-get-started/paste-jar.png
[8]: ./media/mobile-engagement-android-get-started/sync-project.png
[9]: ./media/mobile-engagement-android-get-started/app-connection-info-page.png
[10]: ./media/mobile-engagement-android-get-started/copy-resources.png
[11]: ./media/mobile-engagement-android-get-started/paste-resources.png
