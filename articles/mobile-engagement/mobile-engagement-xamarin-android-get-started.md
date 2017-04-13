<properties
    pageTitle="Introduktion til Azure Mobile aftale for Xamarin.Android"
    description="Lær at bruge Azure Mobile aftale med analyser og Push-beskeder til Xamarin.Android Apps."
    services="mobile-engagement"
    documentationCenter="xamarin"
    authors="piyushjo"
    manager="erikre"
    editor="" />

<tags
    ms.service="mobile-engagement"
    ms.workload="mobile"
    ms.tgt_pltfrm="mobile-xamarin-android"
    ms.devlang="dotnet"
    ms.topic="hero-article"
    ms.date="06/16/2016"
    ms.author="piyushjo" />

# <a name="get-started-with-azure-mobile-engagement-for-xamarinandroid-apps"></a>Introduktion til Azure Mobile aftale til Xamarin.Android Apps

[AZURE.INCLUDE [Hero tutorial switcher](../../includes/mobile-engagement-hero-tutorial-switcher.md)]

Dette emne viser, hvordan du bruger Azure Mobile aftale for at forstå din appforbrug og hvordan du kan sende pushmeddelelser for segmenteret brugere af et Xamarin.Android program.
Dette selvstudium viser simple broadcast scenariet ved hjælp af Mobile aftale. I det, kan du oprette en tom Xamarin.Android-app, som indsamler grundlæggende data og modtager pushmeddelelser ved hjælp af Google Cloud Messaging (GCM).

Dette selvstudium kræver følgende:

+ [Xamarin Studio](http://xamarin.com/studio). Du kan også bruge Visual Studio med Xamarin, men dette selvstudium bruger Xamarin Studio. Se [konfigurere og installere til Visual Studio og Xamarin](https://msdn.microsoft.com/library/mt613162.aspx)installationsvejledning.
+ [Mobile aftale Xamarin SDK](https://www.nuget.org/packages/Microsoft.Azure.Engagement.Xamarin/)

> [AZURE.NOTE] For at fuldføre dette selvstudium skal skal du have en active Azure-konto. Hvis du ikke har en konto, kan du oprette en gratis prøveversion konto på blot et par minutter. Yderligere oplysninger finder du [Azure gratis prøveversion](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Fen-us%2Fdocumentation%2Farticles%2Fmobile-engagement-xamarin-android-get-started).

##<a id="setup-azme"></a>Konfiguration af Mobile aftale til din Android-appen

[AZURE.INCLUDE [Create Mobile Engagement App in Portal](../../includes/mobile-engagement-create-app-in-portal-new.md)]

##<a id="connecting-app"></a>Oprette forbindelse din app til Mobile aftale back-end

Dette selvstudium indeholder en "grundlæggende integration", som er sættet minimale, der kræves for at indsamle data og sende en meddelelse om opslagsnål. 

Vi vil oprette en grundlæggende app med Xamarin Studio til at vise integrationen.

###<a name="create-a-new-xamarinandroid-project"></a>Oprette et nyt Xamarin.Android projekt

1. Start **Xamarin Studio** gå til **filer** -> **nye** -> **løsning** 

    ![][1]

2. Vælg **Android App** og derefter kontrollere det valgte sprog er **C#** , og klik på **Næste**.

    ![][2]

3. Udfyld **App-navn** og **organisationens id**. Sørg for at markering **Afspille Google-tjenester** , og klik derefter på **Næste**. 

    ![][3]
    
4. Opdater **Projektnavn**, **Løsningsnavn** og **placering** , hvis det er nødvendigt, og klik på **Opret**.

    ![][4]
 
Xamarin Studio opretter den app, som vi vil integrere Mobile aftale. 

###<a name="connect-your-app-to-mobile-engagement-backend"></a>Oprette forbindelse din app til Mobile aftale backend-version

1. Højreklik på mappen **pakker** i vinduerne løsning og vælg **Tilføj pakker**

    ![][5]

2. Søg efter **Microsoft Azure Mobile aftale Xamarin SDK** og føje det til din løsning.  

    ![][6]
   
3. Åbn **MainActivity.cs** , og Tilføj følgende ved hjælp af sætninger:

        using Microsoft.Azure.Engagement;
        using Microsoft.Azure.Engagement.Activity;

4. I den `OnCreate` metode kan du tilføje følgende for at initialiseret forbindelse med Mobile aftale back end. Sørg for at tilføje din **ConnectionString**. 

        EngagementConfiguration engagementConfiguration = new EngagementConfiguration();
        engagementConfiguration.ConnectionString = "YourConnectionStringFromAzurePortal";
        EngagementAgent.Init(engagementConfiguration);

###<a name="add-permissions-and-a-service-declaration"></a>Tilføj tilladelser og en tjeneste-erklæring

1. Åbn filen **Manifest.xml** under mappen egenskaber. Vælg fanen kilde, så du direkte opdatere XML-kilde.
 
2. Føj disse tilladelser til Manifest.xml (som du kan finde under mappen **Egenskaber** ) for projektet, umiddelbart før eller efter de `<application>` mærke:

        <uses-permission android:name="android.permission.INTERNET"/>
        <uses-permission android:name="android.permission.ACCESS_NETWORK_STATE"/>
        <uses-permission android:name="android.permission.WRITE_EXTERNAL_STORAGE"/>
        <uses-permission android:name="android.permission.RECEIVE_BOOT_COMPLETED" />
        <uses-permission android:name="android.permission.VIBRATE" />
        <uses-permission android:name="android.permission.DOWNLOAD_WITHOUT_NOTIFICATION"/>

3. Tilføj følgende mellem den `<application>` og `</application>` mærker for at definere agenttjenesten:

        <service
            android:name="com.microsoft.azure.engagement.service.EngagementService"
            android:exported="false"
            android:label="<Your application name>"
            android:process=":Engagement"/>

4. I den kode, du lige har indsat, skal du erstatte `"<Your application name>"` i etiketten. Dette vises i menuen **Indstillinger** , hvor brugerne kan se de tjenester, der kører på enheden. Du kan tilføje ordet "Service" i, der markerer, f.eks.

###<a name="send-a-screen-to-mobile-engagement"></a>Sende en skærm til Mobile aftale

For at begynde at sende data og sikre, at brugerne er aktiv, skal du sende mindst ét skærmbillede til Mobile aftale back-end. For at gøre dette-Sørg for, at den `MainActivity` nedarver fra `EngagementActivity` i stedet for `Activity`.

    public class MainActivity : EngagementActivity
    
Du kan også, hvis du ikke kan nedarve fra `EngagementActivity` og derefter skal du tilføje `.StartActivity` og `.EndActivity` metoder i `OnResume` og `OnPause` henholdsvis.  

        protected override void OnResume()
            {
                EngagementAgent.StartActivity(EngagementAgentUtils.BuildEngagementActivityName(Java.Lang.Class.FromType(this.GetType())), null);
                base.OnResume();             
            }
    
            protected override void OnPause()
            {
                EngagementAgent.EndActivity();
                base.OnPause();            
            }

##<a id="monitor"></a>Oprette forbindelse app med overvågning i realtid

[AZURE.INCLUDE [Connect app with real-time monitoring](../../includes/mobile-engagement-connect-app-with-monitor.md)]

##<a id="integrate-push"></a>Aktivér pushmeddelelser og i app-beskeder

Mobile aftale kan du interagere med og få adgang til dine brugere med pushmeddelelser og app-beskeder i forbindelse med kampagner. Dette modul kaldes rækkevidde på portalen Mobile aftale.
De følgende afsnit konfigurerer din app til at modtage dem.

[AZURE.INCLUDE [Enable Google Cloud Messaging](../../includes/mobile-engagement-enable-google-cloud-messaging.md)]

[AZURE.INCLUDE [Enable in-app messaging](../../includes/mobile-engagement-android-send-push.md)]

[AZURE.INCLUDE [Send notification from portal](../../includes/mobile-engagement-android-send-push-from-portal.md)]

<!-- Images -->
[1]: ./media/mobile-engagement-xamarin-android-get-started/1.png
[2]: ./media/mobile-engagement-xamarin-android-get-started/2.png
[3]: ./media/mobile-engagement-xamarin-android-get-started/3.png
[4]: ./media/mobile-engagement-xamarin-android-get-started/4.png
[5]: ./media/mobile-engagement-xamarin-android-get-started/5.png
[6]: ./media/mobile-engagement-xamarin-android-get-started/6.png
