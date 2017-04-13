<properties
    pageTitle="Introduktion til Azure-Mobilapps til Xamarin.Android apps"
    description="Følge dette selvstudium for at komme i gang med Azure Mobile-Apps til Xamarin Android udvikling"
    services="app-service\mobile"
    documentationCenter="xamarin"
    authors="adrianhall"
    manager="erikre"
    editor="" />

<tags
    ms.service="app-service-mobile"
    ms.workload="mobile"
    ms.tgt_pltfrm="mobile-xamarin-android"
    ms.devlang="dotnet"
    ms.topic="hero-article"
    ms.date="10/01/2016"
    ms.author="adrianha" />

#<a name="create-a-xamarinandroid-app"></a>Oprette en Xamarin.Android-App

[AZURE.INCLUDE [app-service-mobile-selector-get-started](../../includes/app-service-mobile-selector-get-started.md)]

##<a name="overview"></a>Oversigt

Dette selvstudium viser, hvordan du føjer en skybaseret back end-tjeneste til en Xamarin.Android app. Se [Hvad er Mobile-Apps](app-service-mobile-value-prop.md)kan finde flere oplysninger.

Et skærmbillede fra færdige app er nedenfor:

![][0]

Fuldfør dette selvstudium er en betingelse for alle andre Mobile-Apps selvstudier til Xamarin.Android apps.

##<a name="prerequisites"></a>Forudsætninger

For at fuldføre dette selvstudium skal bruge du følgende forudsætninger:

* En active Azure-konto. Hvis du ikke har en konto, kan du tilmelde dig en prøveversion Azure og få op til 10 gratis Mobile-Apps. Yderligere oplysninger finder du [Azure gratis prøveversion](https://azure.microsoft.com/pricing/free-trial/).

* Visual Studio med Xamarin. Se [konfigurere og installere til Visual Studio og Xamarin](https://msdn.microsoft.com/library/mt613162.aspx) for vejledning.

>[AZURE.NOTE]Hvis du vil komme i gang med Azure App Service før tilmelding til en Azure-konto, skal du gå til [Prøve App Service](https://tryappservice.azure.com/?appServiceName=mobile).  Du kan straks oprette en forbigående starter Mobile-App i App-tjeneste. Ingen kreditkort, der kræves. ingen forpligtelser.

## <a name="create-an-azure-mobile-app-backend"></a>Oprette en Azure Mobile-App backend-version

Følg disse trin for at oprette en Mobile-App back-end.

[AZURE.INCLUDE [app-service-mobile-dotnet-backend-create-new-service](../../includes/app-service-mobile-dotnet-backend-create-new-service.md)]

Du har nu klargjort en back-end til Azure Mobile-App, der kan anvendes af programmerne mobilklient. Dernæst skal hente en et serverprojekt med en enkel "opgaveliste liste" backend-version og publicere det i Azure.

## <a name="configure-the-server-project"></a>Konfigurere server projektet

[AZURE.INCLUDE [app-service-mobile-configure-new-backend.md](../../includes/app-service-mobile-configure-new-backend.md)]

## <a name="download-and-run-the-xamarinandroid-app"></a>Downloade og køre Xamarin.Android appen

1. Klik på knappen **Hent** under **hente og køre projektet Xamarin.Android**skal.

    Gem den komprimerede projektfil til din lokale computer, og noter, hvor du gemmer den.

2. Tryk på **F5** for at opbygge projektet og starte appen.

3. Skriv beskrivende tekst, som _færdigt selvstudiet_ i appen, og klik derefter på knappen **Tilføj** .

    ![][10]

    Data fra anmodningen er indsat i tabellen TodoItem. Elementer, der er gemt i tabellen, der returneres af mobilapp back-end, og data, der vises på listen.

    > [AZURE.NOTE] Du kan gennemse den kode, der har adgang til din mobilapp back-end for at forespørge og indsætte data, som findes i filen ToDoActivity.cs C#.

##<a name="next-steps"></a>Næste trin

* [Føje offlinesynkronisering til din app](app-service-mobile-xamarin-android-get-started-offline-data.md)
* [Føje godkendelse til din app](app-service-mobile-xamarin-android-get-started-users.md)
* [Føje pushmeddelelser til din Xamarin.Android-app](app-service-mobile-xamarin-android-get-started-push.md)
* [Sådan bruges administrerede klienten til Azure Mobile-Apps](app-service-mobile-dotnet-how-to-use-client-library.md)


<!-- Images. -->
[0]: ./media/app-service-mobile-xamarin-android-get-started/mobile-quickstart-completed-android.png
[6]: ./media/app-service-mobile-xamarin-android-get-started/mobile-portal-quickstart-xamarin.png
[8]: ./media/app-service-mobile-xamarin-android-get-started/mobile-xamarin-project-android-vs.png
[9]: ./media/app-service-mobile-xamarin-android-get-started/mobile-xamarin-project-android-xs.png
[10]: ./media/app-service-mobile-xamarin-android-get-started/mobile-quickstart-startup-android.png

<!-- URLs. -->
[Azure Portal]: https://azure.portal.com/
[Visual Studio]: https://go.microsoft.com/fwLink/p/?LinkID=534203
