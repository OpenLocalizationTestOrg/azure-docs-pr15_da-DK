<properties
    pageTitle="Introduktion til Mobile-Apps ved hjælp af Xamarin.Forms"
    description="Følge dette selvstudium for at komme i gang med Azure Mobile-Apps til Xamarin.Forms udvikling"
    services="app-service\mobile"
    documentationCenter="xamarin"
    authors="adrianhall"
    manager="erikre"
    editor=""/>

<tags
    ms.service="app-service-mobile"
    ms.workload="mobile"
    ms.tgt_pltfrm="mobile-xamarin"
    ms.devlang="dotnet"
    ms.topic="hero-article"
    ms.date="10/01/2016"
    ms.author="adrianha"/>

#<a name="create-a-xamarinforms-app"></a>Oprette en Xamarin.Forms-app

[AZURE.INCLUDE [app-service-mobile-selector-get-started](../../includes/app-service-mobile-selector-get-started.md)]

##<a name="overview"></a>Oversigt

Dette selvstudium viser, hvordan du føjer en skybaseret back end-tjeneste til en Xamarin.Forms mobilapp ved hjælp af en Azure Mobile-App backend. Du skal oprette en ny Mobile-App back-end såvel en enkel _Opgaveliste listen_ Xamarin.Forms app, der gemmer app-data i Azure.

Fuldfør dette selvstudium er en betingelse for alle andre Mobile-Apps selvstudier til Xamarin.Forms.

##<a name="prerequisites"></a>Forudsætninger

For at fuldføre dette selvstudium skal bruge du følgende:

* En active Azure-konto. Hvis du ikke har en konto, du kan tilmelde dig en prøveversion Azure og få op til gratis 10 Mobile-Apps, kan du stadig bruge selv når din prøveversion udløber. Yderligere oplysninger finder du [Azure gratis prøveversion](https://azure.microsoft.com/pricing/free-trial/).

* Visual Studio med Xamarin. Se [konfigurere og installere til Visual Studio og Xamarin](https://msdn.microsoft.com/library/mt613162.aspx) for vejledning. 

* En Mac med Xcode v7.0 eller nyere og Xamarin Studio Community installeret. Se [konfigurere og installere til Visual Studio og Xamarin](https://msdn.microsoft.com/library/mt613162.aspx) og [konfiguration, installation og kontrol af Mac-brugere](https://msdn.microsoft.com/library/mt488770.aspx) (MSDN).
 
>[AZURE.NOTE] Hvis du vil komme i gang med Azure App Service før tilmelding til en Azure-konto, skal du gå til [Prøve App Service](https://tryappservice.azure.com/?appServiceName=mobile), hvor du straks kan oprette en forbigående starter Mobile-App i App-tjeneste. Ingen kreditkort, der kræves. ingen forpligtelser.

## <a name="create-a-new-azure-mobile-app-backend"></a>Oprette en ny Azure Mobile-App back-end

Følg disse trin for at oprette en ny Mobile-App back-end.

[AZURE.INCLUDE [app-service-mobile-dotnet-backend-create-new-service](../../includes/app-service-mobile-dotnet-backend-create-new-service.md)]


Du har nu klargjort en back-end til Azure Mobile-App, der kan anvendes af programmerne mobilklient. Derefter skal du vil downloade en et serverprojekt med en enkel "opgaveliste liste" backend-version og publicere det i Azure.

## <a name="configure-the-server-project"></a>Konfigurere server projektet

Følg nedenstående trin for at konfigurere server projektet for at bruge enten Node.js eller .NET backend-version.

[AZURE.INCLUDE [app-service-mobile-configure-new-backend](../../includes/app-service-mobile-configure-new-backend.md)]

##<a name="download-and-run-the-xamarinforms-solution"></a>Downloade og køre Xamarin.Forms løsningen

Her kan du have et par valgmuligheder. Du kan hente løsningen til en Mac og åbne den i Xamarin Studio, eller du kan hente løsningen til en Windows-computer og åbne den i Visual Studio fra en netværksforbundet Mac opbygning af iOS-app. Hvis du har brug for mere detaljerede oplysninger om Xamarin konfiguration scenarierne, skal du se [konfigurere og installere til Visual Studio og Xamarin](https://msdn.microsoft.com/library/mt613162.aspx) .

Lad os fortsætte:

 1. På din Mac eller på din Windows-computer, skal du åbne [Azure Portal] i et browservindue.
 2. Klik på bladet indstillinger for din Mobile-App, **Komme i gang** (under mobil) > **Xamarin.Forms**. Klik på **Opret en ny app** under trin 3, hvis den ikke allerede er valgt.  Klik derefter på knappen **Hent** .

    Dette henter et projekt, der indeholder en klientprogrammet, der er tilsluttet din-mobilappen. Gem den komprimerede projektfil til din lokale computer, og noter, hvor du gemmer den.

 3. Udtrække det projekt, du har hentet, og derefter åbne den i Xamarin Studio eller Visual Studio.

    ![][9]

    ![][8]


##<a name="optional-run-the-ios-project"></a>(Valgfrit) Køre iOS project

Dette afsnit er til at køre iOS Xamarin projektet til iOS-enheder. Hvis du ikke arbejder med iOS-enheder, kan du springe dette afsnit.

####<a name="in-xamarin-studio"></a>I Xamarin Studio

1. Højreklik på iOS projektet, og klik derefter på **Angiv som Start projekt**.
2. Klik på **Start fejlfinding** for at opbygge projektet og starte appen i iPhone emulatoren i menuen **Run** .

####<a name="in-visual-studio"></a>I Visual Studio
1. Højreklik på iOS projektet, og klik derefter på **Benyt som Start projekt**.
2. Klik på menuen **Build** **Configuration Manager**.
3. Marker afkrydsningsfelterne **Byg** og **Implementer** af iOS projektet i dialogboksen **Configuration Manager** .
4. Tryk på **F5** for at opbygge projektet og starte appen i iPhone emulatoren.

    >[AZURE.NOTE] Hvis du har problemer med at oprette, understøtter køre NuGet pakke manager og opdatering til den nyeste version af Xamarin pakker. Nogle gange kan projekterne i Hurtig start mellemliggende i blive opdateret til senest.    

Skriv beskrivende tekst, som _Få mere at vide Xamarin_ i appen, og klik derefter på den **+** knappen.

![][10]

Dette sender en anmodning om INDLÆG til den nye mobilapp backend-version Azure som vært. Data fra anmodningen er indsat i tabellen TodoItem. Elementer, der er gemt i tabellen, der returneres af mobilapp back-end, og data, der vises på listen.

>[AZURE.NOTE]
> Du finder den kode, der har adgang til din mobilapp backend-version i TodoItemManager.cs C#-fil med bærbare klasse bibliotek projektet af din løsning.

##<a name="optional-run-the-android-project"></a>(Valgfrit) Kør Android projektet

Dette afsnit er til kørsel af Xamarin droid projektet til Android. Hvis du ikke arbejder med Android-enheder, kan du springe dette afsnit.

####<a name="in-xamarin-studio"></a>I Xamarin Studio

1. Højreklik på Android projektet, og klik derefter på **Angiv som Start projekt**.
2. Klik på **Start fejlfinding** for at opbygge projektet og starte appen på en Android emulator i menuen **Run** .

####<a name="in-visual-studio"></a>I Visual Studio
1. Højreklik på projektets Android (Droid), og klik derefter på **Benyt som Start projekt**.
4. Klik på menuen **Build** **Configuration Manager**.
5. Marker afkrydsningsfelterne **Byg** og **Implementer** af Android projektet i dialogboksen **Configuration Manager** .
6. Tryk på **F5** for at opbygge projektet og starte appen på en Android emulator.

    >[AZURE.NOTE] Hvis du har problemer med at oprette, understøtter køre NuGet pakke manager og opdatering til den nyeste version af Xamarin pakker. Nogle gange kan projekterne i Hurtig start mellemliggende i blive opdateret til senest.    


Skriv beskrivende tekst, som _Få mere at vide Xamarin_ i appen, og klik derefter på den **+** knappen.

![][11]

Dette sender en anmodning om INDLÆG til den nye mobilapp backend-version Azure som vært. Data fra anmodningen er indsat i tabellen TodoItem. Elementer, der er gemt i tabellen, der returneres af mobilapp back-end, og data, der vises på listen.

> [AZURE.NOTE]
> Du finder den kode, der har adgang til din mobilapp backend-version i TodoItemManager.cs C#-fil med bærbare klasse bibliotek projektet af din løsning.


##<a name="optional-run-the-windows-project"></a>(Valgfrit) Kør Windows projektet


Dette afsnit er til kørsel af Xamarin WinApp projektet til Windows-enheder. Hvis du ikke arbejder med Windows-enheder, kan du springe dette afsnit.


####<a name="in-visual-studio"></a>I Visual Studio
1. Højreklik på en Windows-projekter, og klik derefter på **Benyt som Start projekt**.
4. Klik på menuen **Build** **Configuration Manager**.
5. Marker afkrydsningsfelterne **Byg** og **Implementer** af Windows-projekt, du har valgt i dialogboksen **Configuration Manager** .
6. Tryk på **F5** for at opbygge projektet og starte appen i en Windows-emulator.

    >[AZURE.NOTE] Hvis du har problemer med at oprette, understøtter køre NuGet pakke manager og opdatering til den nyeste version af Xamarin pakker. Nogle gange kan projekterne i Hurtig start mellemliggende i blive opdateret til senest.    


Skriv beskrivende tekst, som _Få mere at vide Xamarin_ i appen, og klik derefter på den **+** knappen.

Dette sender en anmodning om INDLÆG til den nye mobilapp backend-version Azure som vært. Data fra anmodningen er indsat i tabellen TodoItem. Elementer, der er gemt i tabellen, der returneres af mobilapp back-end, og data, der vises på listen.

![][12]

> [AZURE.NOTE]
> Du finder den kode, der har adgang til din mobilapp backend-version i TodoItemManager.cs C#-fil med bærbare klasse bibliotek projektet af din løsning.

##<a name="next-steps"></a>Næste trin

* [Føje godkendelse til din app](app-service-mobile-xamarin-forms-get-started-users.md)  
Lær at godkende brugere af din app til en identitetsudbyder.

* [Føje pushmeddelelser til din app](app-service-mobile-xamarin-forms-get-started-push.md)  
Lær at føje opslagsnål beskeder understøtter til din app og konfigurere din Mobile-App back-end for at bruge Azure meddelelse Hubs til at sende pushmeddelelser.

* [Aktivere offlinesynkronisering for din app](app-service-mobile-xamarin-forms-get-started-offline-data.md)  
  Lær at føje offline-understøttelse din app, ved hjælp af en Mobile-App backend. Offlinesynkronisering gør det muligt for slutbrugere til at interagere med en mobilapp&mdash;visning, tilføjelse eller redigering af data&mdash;selv, når der er ingen netværksforbindelse.

* [Sådan bruges administrerede klienten til Azure Mobile-Apps](app-service-mobile-dotnet-how-to-use-client-library.md)  
Få mere at vide om at arbejde med administrerede klienten SDK i din Xamarin app. 


<!-- Anchors. -->
[Getting started with mobile app backends]:#getting-started
[Create a new mobile app backend]:#create-new-service
[Next Steps]:#next-steps


<!-- Images. -->
[6]: ./media/app-service-mobile-xamarin-forms-get-started/xamarin-forms-quickstart.png
[8]: ./media/app-service-mobile-xamarin-forms-get-started/xamarin-forms-quickstart-vs.png
[9]: ./media/app-service-mobile-xamarin-forms-get-started/xamarin-forms-quickstart-xs.png
[10]: ./media/app-service-mobile-xamarin-forms-get-started/mobile-quickstart-startup-ios.png
[11]: ./media/app-service-mobile-xamarin-forms-get-started/mobile-quickstart-startup-android.png
[12]: ./media/app-service-mobile-xamarin-forms-get-started/mobile-quickstart-startup-windows.png


<!-- URLs. -->
[Visual Studio Professional 2013]: https://go.microsoft.com/fwLink/p/?LinkID=257546
[Mobile app SDK]: http://go.microsoft.com/fwlink/?LinkId=257545
[Azure-portalen]: https://portal.azure.com/

