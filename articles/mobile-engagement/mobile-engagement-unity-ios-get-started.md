<properties
    pageTitle="Introduktion til Azure Mobile aftale til enhed iOS installation"
    description="Lær at bruge Azure Mobile aftale med analyser og Push-beskeder til enhed apps til implementeringen i iOS-enheder."
    services="mobile-engagement"
    documentationCenter="unity"
    authors="piyushjo"
    manager="erikre"
    editor="" />

<tags
    ms.service="mobile-engagement"
    ms.workload="mobile"
    ms.tgt_pltfrm="mobile-unity-ios"
    ms.devlang="dotnet"
    ms.topic="hero-article"
    ms.date="08/19/2016"
    ms.author="piyushjo" />

# <a name="get-started-with-azure-mobile-engagement-for-unity-ios-deployment"></a>Introduktion til Azure Mobile aftale til enhed iOS installation

[AZURE.INCLUDE [Hero tutorial switcher](../../includes/mobile-engagement-hero-tutorial-switcher.md)]

Dette emne beskrives, hvordan du bruger Azure Mobile aftale for at forstå din appforbrug, og hvordan du kan sende pushmeddelelser for opdelt brugere af en enhed program, når du installerer på en iOS-enhed.
Dette selvstudium bruger klassisk enhed Brug et kuglen selvstudium som udgangspunkt. Du skal følge trinnene i dette [selvstudium](mobile-engagement-unity-roll-a-ball.md) , før du fortsætter med den Mobile aftale integration vi vise i selvstudiet nedenfor. 

Dette selvstudium kræver følgende:

+ [Enhed Editor](http://unity3d.com/get-unity)
+ [Mobile aftale enhed SDK](https://aka.ms/azmeunitysdk)
+ XCode Editor

> [AZURE.NOTE] For at fuldføre dette selvstudium skal skal du have en active Azure-konto. Hvis du ikke har en konto, kan du oprette en gratis prøveversion konto på blot et par minutter. Yderligere oplysninger finder du [Azure gratis prøveversion](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Fen-us%2Fdocumentation%2Farticles%2Fmobile-engagement-unity-ios-get-started).

##<a id="setup-azme"></a>Konfiguration af Mobile aftale til din iOS-app

[AZURE.INCLUDE [Create Mobile Engagement App in Portal](../../includes/mobile-engagement-create-app-in-portal-new.md)]

##<a id="connecting-app"></a>Oprette forbindelse din app til Mobile aftale back-end

###<a name="import-the-unity-package"></a>Importere pakken enhed

1. Hent den [Mobile aftale enhed pakke](https://aka.ms/azmeunitysdk) og gemme den på din lokale computer. 

2. Gå til **Aktiver -> Indlæs pakke -> brugerdefineret pakke** og vælge den pakke, du har hentet i ovenstående trin. 

    ![][70] 

3. Kontrollér, at alle filer er markeret, og klik på knappen **Importér** . 

    ![][71] 

4. Når importen er fuldført, vises de importerede SDK-filer i dit projekt.  

    ![][72] 

###<a name="update-the-engagementconfiguration"></a>Opdatere EngagementConfiguration

1. Åbn filen **EngagementConfiguration** script fra SDK mappe, og Opdater den **IOS\_forbindelse\_streng** med den forbindelsesstreng, du tidligere har hentet fra Azure-portalen.  

    ![][73]

2. Gem filen. 

###<a name="configure-the-app-for-basic-tracking"></a>Konfigurere appen til grundlæggende sporing

1. Åbn **PlayerController** scriptet knyttet til Player objektet til redigering. 

2. Tilføj følgende ved hjælp af sætning:

        using Microsoft.Azure.Engagement.Unity;

3. Tilføj følgende til den `Start()` metode
    
        EngagementAgent.Initialize();
        EngagementAgent.StartActivity("Home");

###<a name="deploy-and-run-the-app"></a>Installere og køre appen

1. Forbinde en iOS-enhed til din computer. 

2. Åbn **Filer -> opbygge indstillinger** 

    ![][40]

3. Vælg **iOS** og klik derefter på **Skift Platform**

    ![][41]

    ![][42]

4. Klik på **Indstillinger for Player** og giver et gyldigt samlet-id. 

    ![][53]

5. Klik til sidst på **Opbygge og kør**

    ![][54]

6. Du kan blive bedt om at angive et navn til mappen til at gemme iOS-pakke. 

    ![][43]

7. Hvis alt fungerer fint, bliver kompileret projektet, og du skal åbne den på programmets XCode. 

8. Sørg for, at **samle id** er korrekte i projektet.  

    ![][75]

10. Køre appen på XCode nu, så pakken er installeret på enheden forbundne og skal du se afsnittet game for din enhed på telefonen! 

##<a id="monitor"></a>Oprette forbindelse app med overvågning i realtid

[AZURE.INCLUDE [Connect app with real-time monitoring](../../includes/mobile-engagement-connect-app-with-monitor.md)]

##<a id="integrate-push"></a>Aktivér pushmeddelelser og i app-beskeder

Mobile aftale gør det muligt at interagere med dine brugere og Tag med pushmeddelelser og app-beskeder i forbindelse med kampagner. Dette modul kaldes rækkevidde på portalen Mobile aftale.
Du behøver ikke at gøre yderligere konfiguration i din app til at modtage meddelelser og det er allerede konfigureret til den.

[AZURE.INCLUDE [mobile-engagement-ios-send-push-push](../../includes/mobile-engagement-ios-send-push.md)]

<!-- Images. -->
[40]: ./media/mobile-engagement-unity-ios-get-started/40.png
[41]: ./media/mobile-engagement-unity-ios-get-started/41.png
[42]: ./media/mobile-engagement-unity-ios-get-started/42.png
[43]: ./media/mobile-engagement-unity-ios-get-started/43.png
[53]: ./media/mobile-engagement-unity-ios-get-started/53.png
[54]: ./media/mobile-engagement-unity-ios-get-started/54.png
[70]: ./media/mobile-engagement-unity-ios-get-started/70.png
[71]: ./media/mobile-engagement-unity-ios-get-started/71.png
[72]: ./media/mobile-engagement-unity-ios-get-started/72.png
[73]: ./media/mobile-engagement-unity-ios-get-started/73.png
[74]: ./media/mobile-engagement-unity-ios-get-started/74.png
[75]: ./media/mobile-engagement-unity-ios-get-started/75.png
