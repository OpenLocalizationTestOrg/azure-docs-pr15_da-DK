<properties
    pageTitle="Introduktion til Azure Mobile aftale til Windows Phone Silverlight apps"
    description="Lær at bruge Azure Mobile aftale med analyser og push-beskeder til Windows Phone Silverlight apps."
    services="mobile-engagement"
    documentationCenter="windows"
    authors="piyushjo"
    manager="dwrede"
    editor="" />

<tags
    ms.service="mobile-engagement"
    ms.workload="mobile"
    ms.tgt_pltfrm="mobile-windows-phone"
    ms.devlang="dotnet"
    ms.topic="hero-article"
    ms.date="08/19/2016"
    ms.author="piyushjo" />

# <a name="get-started-with-azure-mobile-engagement-for-windows-phone-silverlight-apps"></a>Introduktion til Azure Mobile aftale til Windows Phone Silverlight apps

[AZURE.INCLUDE [Hero tutorial switcher](../../includes/mobile-engagement-hero-tutorial-switcher.md)]

Dette emne beskrives, hvordan du bruger Azure Mobile aftale til at forstå din appforbrug og sende pushmeddelelser til segmenteret brugere af en Windows Phone Silverlight-program.
Dette selvstudium viser simple broadcast scenariet ved hjælp af Mobile aftale. I det, kan du oprette en tom Windows Phone Silverlight-app, som indsamler grundlæggende data og modtager pushmeddelelser ved hjælp af Microsoft opslagsnål meddelelse Service (MPNS).

> [AZURE.NOTE] Hvis du målretter Windows Phone 8.1 (ikke-Silverlight), kan du se [Windows Universal selvstudium](mobile-engagement-windows-store-dotnet-get-started.md).

Dette selvstudium kræver følgende:

+ Visual Studio 2013
+ [MicrosoftAzure.MobileEngagement] Nuget pakke

> [AZURE.NOTE] For at fuldføre dette selvstudium skal skal du have en active Azure-konto. Hvis du ikke har en konto, kan du oprette en gratis prøveversion konto på blot et par minutter. Yderligere oplysninger finder du [Azure gratis prøveversion](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Fen-us%2Fdocumentation%2Farticles%2Fmobile-engagement-windows-phone-get-started).

##<a id="setup-azme"></a>Konfigurere Mobile aftale til din Windows Phone app

[AZURE.INCLUDE [Create Mobile Engagement App in Portal](../../includes/mobile-engagement-create-app-in-portal-new.md)]

##<a id="connecting-app"></a>Oprette forbindelse din app til Mobile aftale back-end

Dette selvstudium indeholder en "grundlæggende integration", som er sættet minimale, der kræves for at indsamle data og sende en meddelelse om opslagsnål. Fuldstændig integration dokumentation kan findes i [Mobile aftale Windows Phone SDK-integration](mobile-engagement-windows-phone-sdk-overview.md)

Vi vil oprette en grundlæggende app med Visual Studio til at vise integrationen.

###<a name="create-a-new-windows-phone-silverlight-project"></a>Oprette et nyt Windows Phone Silverlight-projekt

Følgende trin forudsætter brugen af Visual Studio 2015, selvom trinene er de samme i tidligere versioner af Visual Studio. 

1. Start Visual Studio, og vælg **Nyt projekt**i **startskærmen** .

2. Vælg **Windows 8**i pop op-vinduet, -> **Windows Phone** -> **Tom App (Windows Phone Silverlight)**. Skriv app **navn** og **løsningsnavn**, og klik derefter på **OK**.

    ![][1]

3. Du kan vælge at målrette **Windows Phone 8.0** eller **Windows Phone 8.1**.

Du har nu oprettet en ny Windows Phone Silverlight-app, hvor integrerer vi Azure Mobile aftale SDK.

###<a name="connect-your-app-to-the-mobile-engagement-backend"></a>Oprette forbindelse din app til Mobile aftale back-end

1. Installere pakken [MicrosoftAzure.MobileEngagement] nuget i projektet.

2. Åbn `WMAppManifest.xml` (under mappen egenskaber), og Sørg for, at følgende er erklæret (Tilføj dem, hvis de ikke) i den `<Capabilities />` mærke:

        <Capability Name="ID_CAP_NETWORKING" />
        <Capability Name="ID_CAP_IDENTITY_DEVICE" />

    ![][2]

3. Nu Indsæt forbindelsesstrengen, som du kopierede tidligere for din Mobile aftale app, og Indsæt det i den `Resources\EngagementConfiguration.xml` filer mellem den `<connectionString>` og `</connectionString>` mærker:

    ![][3]

4. I den `App.xaml.cs` fil:

    en. Tilføje den `using` sætning:

            using Microsoft.Azure.Engagement;

    b. Initialiseret SDK i den `Application_Launching` metode:

            private void Application_Launching(object sender, LaunchingEventArgs e)
            {
              EngagementAgent.Instance.Init();
            }

    c. Indsæt følgende i den `Application_Activated`:

            private void Application_Activated(object sender, ActivatedEventArgs e)
            {
               EngagementAgent.Instance.OnActivated(e);
            }

##<a id="monitor"></a>Aktivere overvågning i realtid

For at begynde at sende data og sikre, at brugerne er aktiv, skal du sende mindst ét skærmbillede (aktivitet) til Mobile aftale back-end.

1. Tilføj i MainPage.xaml.cs, den `using` sætning:

        using Microsoft.Azure.Engagement;

2. Erstat den grundlæggende klasse af **MainPage**, der ligger før **PhoneApplicationPage**, med **EngagementPage**.

        class MainPage : EngagementPage 
    
3. I din `MainPage.xml` fil:

    en. Føje til din navneområder erklæringer:

         xmlns:engagement="clr-namespace:Microsoft.Azure.Engagement;assembly=Microsoft.Azure.Engagement.EngagementAgent.WP"

    b. Erstatte `phone:PhoneApplicationPage` i XML-kodenavn med `engagement:EngagementPage`.

##<a id="monitor"></a>Oprette forbindelse app med overvågning i realtid

[AZURE.INCLUDE [Connect app with real-time monitoring](../../includes/mobile-engagement-connect-app-with-monitor.md)]

##<a id="integrate-push"></a>Aktivér pushmeddelelser og i app-beskeder

Mobile aftale kan du interagere og få adgang til dine brugere med Push-beskeder og i app'en meddelelser i forbindelse med kampagner. Dette modul kaldes rækkevidde på portalen Mobile aftale.
De følgende afsnit konfigurere din app til at modtage dem.

###<a name="enable-your-app-to-receive-mpns-push-notifications"></a>Aktivere din app til at modtage MPNS Push-beskeder

Tilføje nye funktioner til din `WMAppManifest.xml` fil:

        ID_CAP_PUSH_NOTIFICATION
        ID_CAP_WEBBROWSERCOMPONENT

   ![][5]

###<a name="initialize-the-reach-sdk"></a>Initialiseret rækkevidde SDK

1. I `App.xaml.cs`, ringe `EngagementReach.Instance.Init();` i funktionen **Application_Launching** højre efter agent initialisering:

        private void Application_Launching(object sender, LaunchingEventArgs e)
        {
           EngagementAgent.Instance.Init();
           EngagementReach.Instance.Init();
        }

2. I `App.xaml.cs`, ringe `EngagementReach.Instance.OnActivated(e);` i funktionen **Application_Activated** højre efter agent initialisering:

        private void Application_Activated(object sender, ActivatedEventArgs e)
        {
           EngagementAgent.Instance.OnActivated(e);
           EngagementReach.Instance.OnActivated(e);
        }

Du er klar. Nu bekræfter vi, at du har sagde korrekt ud af denne grundlæggende integration.

##<a id="send"></a>Sende en meddelelse til din app

[AZURE.INCLUDE [Create Windows Push campaign](../../includes/mobile-engagement-windows-push-campaign.md)]

Du bør nu se en meddelelse på enheden, som vises som en appmeddelelse, hvis app er åben ellers som en toastbeskeden ud som følger: 

![][6]

<!-- URLs. -->
[MicrosoftAzure.MobileEngagement]: http://go.microsoft.com/?linkid=9874664
[Mobile Engagement Windows Phone SDK documentation]: ../mobile-engagement-windows-phone-integrate-engagement/

<!-- Images. -->
[1]: ./media/mobile-engagement-windows-phone-get-started/project-properties.png
[2]: ./media/mobile-engagement-windows-phone-get-started/wmappmanifest-capabilities.png
[3]: ./media/mobile-engagement-windows-phone-get-started/add-connection-string.png
[5]: ./media/mobile-engagement-windows-phone-get-started/reach-capabilities.png
[6]: ./media/mobile-engagement-windows-phone-get-started/push-screenshot.png
