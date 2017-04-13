<properties
    pageTitle="Introduktion til Azure App Service Mobile-Apps til Xamarin.iOS apps | Microsoft Azure"
    description="Følge dette selvstudium for at komme i gang med at bruge Mobile-Apps til udvikling af Xamarin.iOS."
    services="app-service\mobile"
    documentationCenter="xamarin"
    authors="adrianhall"
    manager="dwrede"
    editor=""/>

<tags
    ms.service="app-service-mobile"
    ms.workload="na"
    ms.tgt_pltfrm="mobile-xamarin-ios"
    ms.devlang="dotnet"
    ms.topic="hero-article"
    ms.date="10/01/2016"
    ms.author="adrianha"/>


#<a name="create-a-xamarinios-app"></a>Oprette en Xamarin.iOS-app

[AZURE.INCLUDE [app-service-mobile-selector-get-started](../../includes/app-service-mobile-selector-get-started.md)]

##<a name="overview"></a>Oversigt

Dette selvstudium viser, hvordan du tilføjer en skybaseret back end-tjeneste til en Xamarin.iOS mobilapp med en Azure-mobilappen backend-version.  Du opretter både en ny mobilapp back-end og en enkel _Opgaveliste listen_ Xamarin.iOS app, der gemmer app-data i Azure.

Fuldfør dette selvstudium er en betingelse for alle andre Xamarin.iOS selvstudier om brug af funktionen Mobile-Apps i Azure App-tjeneste.

##<a name="prerequisites"></a>Forudsætninger

For at fuldføre dette selvstudium skal bruge du følgende forudsætninger:

* En active Azure-konto. Hvis du ikke har en konto, kan du tilmelde dig en Azure-prøveversion og få op til 10 gratis mobilapps, der kan du stadig bruge selv når din prøveversion udløber. Yderligere oplysninger finder du [Azure gratis prøveversion](https://azure.microsoft.com/pricing/free-trial/).

* Visual Studio med Xamarin. Se [konfigurere og installere til Visual Studio og Xamarin](https://msdn.microsoft.com/library/mt613162.aspx) for vejledning.

* En Mac med Xcode v7.0 eller nyere og Xamarin Studio Community installeret. Se [konfigurere og installere til Visual Studio og Xamarin](https://msdn.microsoft.com/library/mt613162.aspx) og [konfiguration, installation og kontrol af Mac-brugere](https://msdn.microsoft.com/library/mt488770.aspx) (MSDN).

>[AZURE.NOTE]Hvis du vil Introduktion til Azure App Service, før du tilmelder dig en Azure-konto, skal du gå til [Prøve App Service](https://tryappservice.azure.com/?appServiceName=mobile). Du kan oprette en mobilapp forbigående starter med det samme i App Service – ingen kreditkort er påkrævet, og ingen forpligtelser.

## <a name="create-an-azure-mobile-app-backend"></a>Oprette en Azure Mobile-App backend-version

Følg disse trin for at oprette en Mobile-App back-end.

[AZURE.INCLUDE [app-service-mobile-dotnet-backend-create-new-service](../../includes/app-service-mobile-dotnet-backend-create-new-service.md)]

## <a name="configure-the-server-project"></a>Konfigurere server projektet

Du har nu klargjort en back-end til Azure Mobile-App, der kan anvendes af programmerne mobilklient. Dernæst skal hente en et serverprojekt med en enkel "opgaveliste liste" backend-version og publicere det i Azure.

Følg vejledningen nedenfor for at konfigurere server projektet for at bruge enten Node.js eller .NET backend-version.

[AZURE.INCLUDE [app-service-mobile-configure-new-backend](../../includes/app-service-mobile-configure-new-backend.md)]

## <a name="download-and-run-the-xamarinios-app"></a>Downloade og køre Xamarin.iOS appen

1. Åbn [Azure portal] i et browservindue.

2. Klik på **Introduktion**bladet indstillinger for din Mobile-App > **Xamarin.iOS**. Klik på **Opret en ny app** under trin 3, hvis den ikke allerede er valgt.  Klik derefter på knappen **Hent** .

    Der overføres en klientprogrammet, der opretter forbinder til din mobile backend-version. Gem den komprimerede projektfil til din lokale computer, og noter, hvor du gemmer den.

3. Udtrække det projekt, du har hentet, og derefter åbne den i Xamarin Studio (eller Visual Studio).

    ![][9]

    ![][8]

4. Tryk på F5 for at opbygge projektet og starte appen i iPhone emulatoren.

5. Skriv beskrivende tekst, som _Få mere at vide Xamarin_, i appen, og klik derefter på den **+** knappen.

    ![][10]

    Data fra anmodningen er indsat i tabellen TodoItem. Elementer, der er gemt i tabellen, der returneres af mobilapp back-end, og data, der vises på listen.

>[AZURE.NOTE]Du kan gennemse den kode, der har adgang til din mobilapp back-end for at forespørge og indsætte data i filen QSTodoService.cs C#.

##<a name="next-steps"></a>Næste trin

* [Føje offlinesynkronisering til din app](app-service-mobile-xamarin-ios-get-started-offline-data.md)
* [Føje godkendelse til din app](app-service-mobile-xamarin-ios-get-started-users.md)
* [Føje pushmeddelelser til din Xamarin.Android-app](app-service-mobile-xamarin-ios-get-started-push.md)
* [Sådan bruges administrerede klienten til Azure Mobile-Apps](app-service-mobile-dotnet-how-to-use-client-library.md)

<!-- Anchors. -->
[Getting started with mobile app backends]:#getting-started
[Create a new mobile app backend]:#create-new-service
[Next Steps]:#next-steps

<!-- Images. -->
[6]: ./media/app-service-mobile-xamarin-ios-get-started/xamarin-ios-quickstart.png
[8]: ./media/app-service-mobile-xamarin-ios-get-started/mobile-xamarin-project-ios-vs.png
[9]: ./media/app-service-mobile-xamarin-ios-get-started/mobile-xamarin-project-ios-xs.png
[10]: ./media/app-service-mobile-xamarin-ios-get-started/mobile-quickstart-startup-ios.png

<!-- URLs. -->
[Azure-portalen]: https://portal.azure.com/
