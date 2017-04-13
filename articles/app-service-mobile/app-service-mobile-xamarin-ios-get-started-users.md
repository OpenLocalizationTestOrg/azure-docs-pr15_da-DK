<properties
    pageTitle="Introduktion til godkendelse til Mobile-Apps i Xamarin iOS"
    description="Lær at bruge Mobile-Apps til at godkende brugere af din Xamarin iOS app gennem en række forskellige identitetsudbydere, herunder AAD, Google, Facebook, Twitter og Microsoft."
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
    ms.topic="article"
    ms.date="10/01/2016"
    ms.author="adrianha"/>

# <a name="add-authentication-to-your-xamarinios-app"></a>Føje godkendelse til din Xamarin.iOS-app

[AZURE.INCLUDE [app-service-mobile-selector-get-started-users](../../includes/app-service-mobile-selector-get-started-users.md)]

Dette emne beskrives, hvordan til at godkende brugere af en App-tjenesten Mobile-App fra klientprogrammet. I dette selvstudium, kan du tilføje godkendelse til Xamarin.iOS Hurtig start projektet ved hjælp af en identitetsudbyder, der understøttes af App Service. Når der er blevet godkendt og godkendt af din Mobile-App, vises værdien bruger-ID, og du vil kunne få adgang til begrænsede tabeldata.

Først skal du udføre selvstudiet [oprette en Xamarin.iOS app]. Hvis du ikke bruger hentede Hurtig start server projektet, skal du føje pakken til godkendelse lokalnummer til projektet. Du kan finde flere oplysninger om server lokalnummer pakker, under [arbejde med .NET back end-serveren SDK til Azure Mobile-Apps](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md).

##<a name="register-your-app-for-authentication-and-configure-app-services"></a>Registrere din app til godkendelse og konfigurere App Services

[AZURE.INCLUDE [app-service-mobile-register-authentication](../../includes/app-service-mobile-register-authentication.md)]

##<a name="restrict-permissions-to-authenticated-users"></a>Begrænse tilladelser på godkendte brugere

[AZURE.INCLUDE [app-service-mobile-restrict-permissions-dotnet-backend](../../includes/app-service-mobile-restrict-permissions-dotnet-backend.md)]

&nbsp;&nbsp;4. i Visual Studio eller Xamarin Studio, skal du køre klientprojektet på en enhed eller emulator. Kontrollér, at en ikke-afviklet undtagelse med en statuskode af 401 (uautoriseret) opstår, når programmet starter. Konsollen på fejlfindingsværktøjet logføres fejlen. Så i Visual Studio, skal du se fejlen i outputvinduet.

&nbsp;&nbsp;Fejlen uautoriseret sker, fordi app forsøger at få adgang til din Mobile-App backend-version som en ikke-godkendte bruger. Tabellen *TodoItem* nu kræver godkendelse.

Derefter skal opdateres du klient-app til anmodning om ressourcer fra Mobile-App back-end med en godkendt bruger.

##<a name="add-authentication-to-the-app"></a>Føje godkendelse til appen

I dette afsnit, skal redigere du app for at få vist en logonskærmen før visning af data. Når programmet starter, vil ikke ikke oprette forbindelse til din App tjeneste og kan ikke vise data. Når første gang, som udfører brugeren bevægelse Opdater logonskærmen vises. Når logon lykkes på listen over opgaveliste elementer vises.

1. Åbn filen **QSTodoService.cs** i klientprojektet, og Tilføj følgende ved hjælp af sætning og `MobileServiceUser` med accessor til klassen QSTodoService:

    ```
        using UIKit;
    ```

        // Logged in user
        private MobileServiceUser user;
        public MobileServiceUser User { get { return user; } }

2. Tilføje nye metode med navnet **Godkend** til **QSTodoService** med definitionen af følgende:


        public async Task Authenticate(UIViewController view)
        {
            try
            {
                user = await client.LoginAsync(view, MobileServiceAuthenticationProvider.Facebook);
            }
            catch (Exception ex)
            {
                Console.Error.WriteLine (@"ERROR - AUTHENTICATION FAILED {0}", ex.Message);
            }
        }

    >[AZURE.NOTE] Hvis du bruger en identitetsudbyder end en Facebook, ændre den værdi, der overføres til **LoginAsync** over til en af følgende: _MicrosoftAccount_, _Twitter_, _Google_eller _WindowsAzureActiveDirectory_.

3. Åbn **QSTodoListViewController.cs**. Ændre metoden definitionen af **ViewDidLoad** fjerne opkaldet til **RefreshAsync()** nær bunden:

        public override async void ViewDidLoad ()
        {
            base.ViewDidLoad ();

            todoService = QSTodoService.DefaultService;
           await todoService.InitializeStoreAsync ();

           RefreshControl.ValueChanged += async (sender, e) => {
                await RefreshAsync ();
           }

            // Comment out the call to RefreshAsync
            // await RefreshAsync ();
        }


4. Ændre metoden **RefreshAsync** til at godkende Hvis egenskaben **User** er null. Føj følgende kode i toppen af den metode definition:

        // start of RefreshAsync method
        if (todoService.User == null) {
            await QSTodoService.DefaultService.Authenticate (this);
            if (todoService.User == null) {
                Console.WriteLine ("couldn't login!!");
                return;
            }
        }
        // rest of RefreshAsync method

5. Forbindelse til din Xamarin opbygge vært på din Mac skal køre klientprojektet målretning af en enhed eller emulator i Visual Studio eller Xamarin Studio. Kontrollér, at appen vises ingen data.

    Udføre Opdater bevægelse ved at trække ned på listen over elementer, som medfører-logonskærmen vises. Når du har angivet gyldige legitimationsoplysninger, appen vises på listen over opgaveliste elementer, og du kan foretage opdateringer til dataene.


<!-- URLs. -->
[Submit an app page]: http://go.microsoft.com/fwlink/p/?LinkID=266582
[My Applications]: http://go.microsoft.com/fwlink/p/?LinkId=262039
[Oprette en Xamarin.iOS-app]: app-service-mobile-xamarin-ios-get-started.md
