<properties
    pageTitle="Introduktion til Azure Mobile aftale til online | Microsoft Azure"
    description="Lær at bruge Azure Mobile aftale med analyser og push-beskeder for Web Apps."
    services="mobile-engagement"
    documentationCenter="Mobile"
    authors="piyushjo"
    manager="erikre"
    editor="" />

<tags
    ms.service="mobile-engagement"
    ms.workload="mobile"
    ms.tgt_pltfrm="na"
    ms.devlang="js"
    ms.topic="hero-article"
    ms.date="06/01/2016"
    ms.author="piyushjo" />

# <a name="get-started-with-azure-mobile-engagement-for-web-apps"></a>Introduktion til Azure Mobile aftale til Web Apps

[AZURE.INCLUDE [Hero tutorial switcher](../../includes/mobile-engagement-hero-tutorial-switcher.md)]

Dette emne viser, hvordan du bruger Azure Mobile aftale til at forstå din online brugen.

Dette selvstudium kræver følgende:

+ Visual Studio 2015 eller en anden editor efter eget valg
+ [Web SDK](http://aka.ms/P7b453) 

Denne Web SDK er i Vis udskrift og kun understøtter Analytics i øjeblikket og understøtter ikke afsendelse browser eller i app'en pushmeddelelser endnu. 

> [AZURE.NOTE] For at fuldføre dette selvstudium skal skal du have en active Azure-konto. Hvis du ikke har en konto, kan du oprette en gratis prøveversion konto på blot et par minutter. Yderligere oplysninger finder du [Azure gratis prøveversion](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Fen-us%2Fdocumentation%2Farticles%2Fmobile-engagement-web-app-get-started).

##<a name="setup-mobile-engagement-for-your-web-app"></a>Konfiguration af Mobile aftale for din online

[AZURE.INCLUDE [Create Mobile Engagement App in Portal](../../includes/mobile-engagement-create-app-in-portal-new.md)]

##<a id="connecting-app"></a>Oprette forbindelse din app til Mobile aftale back-end

Dette selvstudium indeholder en "grundlæggende integration," som er sættet minimale, der kræves for at indsamle data.

Vi opretter en grundlæggende online med Visual Studio til at vise integrationen, selvom du kan følge trinnene til et webprogram, der er oprettet uden for Visual Studio også. 

###<a name="create-a-new-web-app"></a>Oprette en Web App

Følgende trin forudsætter brugen af Visual Studio 2015, selvom trinene er de samme i tidligere versioner af Visual Studio. 

1. Start Visual Studio, og vælg **Nyt projekt**i **startskærmen** .

2. Vælg **Web**i pop op-vinduet, -> **ASP.Net-webprogrammet**. Udfyld app, **navn**, **placering** og **løsningsnavn**, og klik derefter på **OK**.

3. Vælg **tom** under **ASP.Net 4.5 skabeloner** **Vælg en skabelon** pop-up-vinduet, og klik på **OK**. 

Du har nu oprettet et nyt tomt online projekt, hvor integrerer vi Azure Mobile aftale Web SDK.

###<a name="connect-your-app-to-mobile-engagement-backend"></a>Oprette forbindelse din app til Mobile aftale backend-version

1. Oprette en ny mappe med navnet **javascript** i din løsning og tilføje Web SDK JS fil **azure-engagement.js** i den. 

2. Tilføje en ny fil kaldet **main.js** i denne javascript-mappe med følgende kode. Sørg for at opdatere forbindelsesstrengen. Dette `azureEngagement` objekt bruges til at få adgang til Web SDK metoder. 

        var azureEngagement = {
            debug: true,
            connectionString: 'xxxxx'
        };

    ![Visual Studio med js filer][1]

##<a name="enable-real-time-monitoring"></a>Aktivere overvågning i realtid

For at begynde at sende data og sikre, at brugerne er aktiv, skal du sende mindst én aktivitet til Mobile aftale back-end. En aktivitet i konteksten af en WebApp er en webside. 

1. Oprette en ny side, der hedder **home.html** i din løsning, og den angives som startsiden for din online. 
2. Medtag de to javascripts vi tilføjet tidligere i denne side ved at tilføje følgende i mærket brødtekst. 

        <script type="text/javascript" src="javascript/main.js"></script>
        <script type="text/javascript" src="javascript/azure-engagement.js"></script>

3. Opdatere mærket brødtekst for at ringe Engagementagent's `startActivity` metode
        
        <body onload="engagement.agent.startActivity('Home')">

4. Her er, hvordan din **home.html** ser ud
        
        <html>
        <head>
            ...
        </head>
        <body onload="engagement.agent.startActivity('Home')">
            <script type="text/javascript" src="javascript/main.js"></script>
            <script type="text/javascript" src="javascript/azure-engagement.js"></script>
        </body>
        </html>

##<a name="connect-app-with-real-time-monitoring"></a>Oprette forbindelse app med overvågning i realtid

[AZURE.INCLUDE [Connect app with real-time monitoring](../../includes/mobile-engagement-connect-app-with-monitor.md)]

![][2]

##<a name="extend-analytics"></a>Udvide analytics

Her er tilgængelige med Web SDK, som du kan bruge til analytics alle metoder:

1. Aktiviteter/websider:

        engagement.agent.startActivity(name);
        engagement.agent.endActivity();

2. Begivenheder
        
        engagement.agent.sendEvent(name, extras);

3. Fejl

        engagement.agent.sendError(name, extras);

4. Job

        engagement.agent.startJob(name);
        engagement.agent.endJob(name);

<!-- Images. -->
[1]: ./media/mobile-engagement-web-app-get-started/visual-studio-solution-js.png
[2]: ./media/mobile-engagement-web-app-get-started/session.png

