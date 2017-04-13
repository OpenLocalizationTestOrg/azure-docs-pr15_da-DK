<properties
    pageTitle="Oprette en Universal Windows Platform (UWP), der bruger på Mobile-Apps | Microsoft Azure"
    description="Følge dette selvstudium for at komme i gang med at bruge Azure-mobilappen en back-end for Universal Windows Platform (UWP) app udvikling i C#, Visual Basic eller JavaScript."
    services="app-service\mobile"
    documentationCenter="windows"
    authors="adrianhall"
    manager="erikre"
    editor=""/>

<tags
    ms.service="app-service-mobile"
    ms.workload="mobile"
    ms.tgt_pltfrm="mobile-windows"
    ms.devlang="dotnet"
    ms.topic="hero-article"
    ms.date="10/01/2016"
    ms.author="adrianha"/>

#<a name="create-a-windows-app"></a>Oprette en Windows-app

[AZURE.INCLUDE [app-service-mobile-selector-get-started](../../includes/app-service-mobile-selector-get-started.md)]

##<a name="overview"></a>Oversigt

Dette selvstudium viser, hvordan du føjer en skybaseret back end-tjeneste til en Universal Windows Platform (UWP)-app. Se [Hvad er Mobile-Apps](app-service-mobile-value-prop.md)kan finde flere oplysninger. Følgende er skærmbillederne fra færdige app:

![Fuldført-programmet på computeren](./media/app-service-mobile-windows-store-dotnet-get-started/mobile-quickstart-completed-desktop.png)   
Kører på en computer. 

![Færdige telefonappen](./media/app-service-mobile-windows-store-dotnet-get-started/mobile-quickstart-completed.png)  
Kører på en telefon

Fuldfør dette selvstudium er en betingelse for alle andre Mobile-App selvstudier til UWP apps. 

##<a name="prerequisites"></a>Forudsætninger

For at fuldføre dette selvstudium skal bruge du følgende:

* En active Azure-konto. Hvis du ikke har en konto, kan du tilmelde dig en Azure-prøveversion og få op til 10 gratis mobilapps, der kan du stadig bruge selv når din prøveversion udløber. Yderligere oplysninger finder du [Azure gratis prøveversion](https://azure.microsoft.com/pricing/free-trial/).

* [Visual Studio Community 2015] eller en nyere version.

>[AZURE.NOTE] Hvis du vil Introduktion til Azure App Service, før du tilmelder dig en Azure-konto, skal du gå til [Prøve App Service](https://tryappservice.azure.com/?appServiceName=mobile). Der, skal du straks kan oprette en forbigående starter mobilapp i App Service – ingen kreditkort er påkrævet, og ingen forpligtelser.

##<a name="create-a-new-azure-mobile-app-backend"></a>Oprette en ny Azure Mobile-App back-end

Følg disse trin for at oprette en ny Mobile-App back-end.

[AZURE.INCLUDE [app-service-mobile-dotnet-backend-create-new-service](../../includes/app-service-mobile-dotnet-backend-create-new-service.md)]

Du har nu klargjort en back-end til Azure Mobile-App, der kan anvendes af programmerne mobilklient. Derefter skal du vil downloade en et serverprojekt med en enkel "opgaveliste liste" backend-version og publicere det i Azure.

## <a name="configure-the-server-project"></a>Konfigurere server projektet

[AZURE.INCLUDE [app-service-mobile-configure-new-backend.md](../../includes/app-service-mobile-configure-new-backend.md)]

##<a name="download-and-run-the-client-project"></a>Downloade og køre klient project

Når du har konfigureret din Mobile-App backend-version, kan du oprette en ny klient-app eller ændre en eksisterende app til at oprette forbindelse til Azure. I dette afsnit, skal hente du et projekt i UWP app skabelon, der er tilpasset til at oprette forbindelse til din Mobile-App backend-version.

1. Klik på **Opret en ny app**tilbage i bladet **Hurtig start** for din Mobile-App back end- > **Hent**, og derefter udtrække den komprimerede projektfiler til din lokale computer.

    ![Hent Windows Hurtig start project](./media/app-service-mobile-windows-store-dotnet-get-started/mobile-app-windows-quickstart.png)

3. (Valgfrit) Føj UWP app projekt til den samme løsning som server projektet. Det gør det nemmere at foretage fejlfinding og test både appen og back end-i den samme løsning i Visual Studio, hvis du vælger at gøre dette. Hvis du vil tilføje et UWP app projekt til løsningen, skal du bruge Visual Studio 2015 eller en nyere version.

4. Tryk på F5 for at installere og køre appen med appen UWP som projektets start.

5. Skriv beskrivende tekst, som *færdigt selvstudiet*, i feltet **Indsæt en TodoItem** tekst i app, og klik derefter på **Gem**.

    ![Fuldført skrivebordet i Windows Hurtig start](./media/app-service-mobile-windows-store-dotnet-get-started/mobile-quickstart-startup.png)

    Dette sender en anmodning om INDLÆG til den nye mobilapp backend-version, der er placeret i Azure.

6. (Valgfrit) Stoppe app, og genstarte den på en anden enhed eller mobile emulator.

    ![Windows Hurtig start fuldført telefon](./media/app-service-mobile-windows-store-dotnet-get-started/mobile-quickstart-completed.png)

    Bemærk, at data gemt fra det forrige trin er indlæst fra Azure, når appen UWP starter. 

##<a name="next-steps"></a>Næste trin

* [Føje godkendelse til din app](app-service-mobile-windows-store-dotnet-get-started-users.md)  
  Lær at godkende brugere af din app til en identitetsudbyder.

* [Føje pushmeddelelser til din app](app-service-mobile-windows-store-dotnet-get-started-push.md)  
  Lær at føje opslagsnål beskeder understøtter til din app og konfigurere din Mobile-App back-end for at bruge Azure meddelelse Hubs til at sende pushmeddelelser.

* [Aktivere offlinesynkronisering for din app](app-service-mobile-windows-store-dotnet-get-started-offline-data.md)  
  Lær at føje offline-understøttelse din app, ved hjælp af en Mobile-App backend. Offlinesynkronisering gør det muligt for slutbrugere til at interagere med en mobilapp&mdash;visning, tilføjelse eller redigering af data&mdash;selv, når der er ingen netværksforbindelse.

<!-- Anchors. -->
<!-- Images. -->
<!-- URLs. -->
[Mobile App SDK]: http://go.microsoft.com/fwlink/?LinkId=257545
[Azure portal]: https://portal.azure.com/
[Visual Studio Community 2015]: https://go.microsoft.com/fwLink/p/?LinkID=534203
