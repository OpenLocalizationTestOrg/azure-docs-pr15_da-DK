<properties
    pageTitle="Introduktion til godkendelse til Mobile-Apps i Xamarin.Forms app | Microsoft Azure"
    description="Lær at bruge Mobile-Apps til at godkende brugere af din Xamarin formularer app gennem en række forskellige identitetsudbydere, herunder AAD, Google, Facebook, Twitter og Microsoft."
    services="app-service\mobile"
    documentationCenter="xamarin"
    authors="adrianhall"
    manager="dwrede"
    editor=""/>

<tags
    ms.service="app-service-mobile"
    ms.workload="mobile"
    ms.tgt_pltfrm="mobile-xamarin"
    ms.devlang="dotnet"
    ms.topic="article"
    ms.date="10/01/2016"
    ms.author="adrianha"/>

# <a name="add-authentication-to-your-xamarinforms-app"></a>Føje godkendelse til din Xamarin.Forms-app

[AZURE.INCLUDE [app-service-mobile-selector-get-started-users](../../includes/app-service-mobile-selector-get-started-users.md)]

##<a name="overview"></a>Oversigt

Dette emne beskrives, hvordan til at godkende brugere af en App-tjenesten Mobile-App fra klientprogrammet. I dette selvstudium, kan du tilføje godkendelse til Xamarin.Forms Hurtig start projektet ved hjælp af en identitetsudbyder, der understøttes af App Service. Når der er blevet godkendt og godkendt af din Mobile-App, vises værdien bruger-ID, og du vil kunne få adgang til begrænsede tabeldata.

##<a name="prerequisites"></a>Forudsætninger

For det bedste resultat med dette selvstudium anbefaler vi, at du først har fuldført selvstudiet [oprette en Xamarin.Forms app](app-service-mobile-xamarin-forms-get-started.md) . Når du har fuldført dette selvstudium, får du et Xamarin.Forms projekt, der er en flere platforme TodoList app.

Hvis du ikke bruger hentede Hurtig start server projektet, skal du føje pakken til godkendelse lokalnummer til projektet. Du kan finde flere oplysninger om server lokalnummer pakker, under [arbejde med .NET back end-serveren SDK til Azure Mobile-Apps](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md).

##<a name="register-your-app-for-authentication-and-configure-app-services"></a>Registrere din app til godkendelse og konfigurere App Services

[AZURE.INCLUDE [app-service-mobile-register-authentication](../../includes/app-service-mobile-register-authentication.md)]

##<a name="restrict-permissions-to-authenticated-users"></a>Begrænse tilladelser på godkendte brugere

[AZURE.INCLUDE [app-service-mobile-restrict-permissions-dotnet-backend](../../includes/app-service-mobile-restrict-permissions-dotnet-backend.md)]


##<a name="add-authentication-to-the-portable-class-library"></a>Føje godkendelse til flytbare klassebibliotek

Mobile-Apps bruges metoden [LoginAsync] lokalnummer på [MobileServiceClient] til at logge på en bruger med App Service godkendelse. Dette eksempel bruger en server-administreret godkendelse rutediagram, der viser udbyderens logon-grænsefladen i appen. Du kan finde flere oplysninger under [Server-administreret godkendelse](app-service-mobile-dotnet-how-to-use-client-library.md#serverflow). For at give en bedre brugeroplevelse i din app, fremstilling, kan du overveje at i stedet ved hjælp af [klient-administreret godkendelse](app-service-mobile-dotnet-how-to-use-client-library.md#clientflow). 

Hvis du vil godkende med et Xamarin.Forms projekt, definerer du en **IAuthenticate** grænseflade i Portable klassebibliotek for appen. Du kan også opdatere den brugergrænseflade, der er defineret i Portable klassebibliotek at tilføje en **logon** -knap, som brugeren klikker på at starte godkendelse. Data er indlæst fra mobilapp back-end efter vellykket godkendelse.

Du skal implementere grænsefladen **IAuthenticate** for hver platform, der understøttes af din app.


1. I Visual Studio eller Xamarin Studio skal åbne App.cs fra projektet med **bærbare** i det navn, som er flytbare klassebibliotek projekt, derefter skal du tilføje følgende `using` sætning:

        using System.Threading.Tasks;

2. Tilføj følgende i App.cs, `IAuthenticate` brugergrænseflade definition umiddelbart før den `App` klasse definition.

        public interface IAuthenticate
        {
            Task<bool> Authenticate();
        }

3. Tilføj følgende statisk medlemmer til klassen **App** initialiseret grænsefladen med en bestemt implementering platform.

        public static IAuthenticate Authenticator { get; private set; }

        public static void Init(IAuthenticate authenticator)
        {
            Authenticator = authenticator;
        }

4. Åbne TodoList.xaml fra bærbare klassebibliotek projektet skal du tilføje følgende **knappen** element i elementet *buttonsPanel* layout efter knappen eksisterende: 

        <Button x:Name="loginButton" Text="Sign-in" MinimumHeightRequest="30" 
            Clicked="loginButton_Clicked"/>

    Denne knap udløser server-administreret godkendelse med din back end-til-mobilappen.

5. Åbne TodoList.xaml.cs fra bærbare klassebibliotek projektet og derefter tilføje følgende felt til den `TodoList` klasse:

        // Track whether the user has authenticated. 
        bool authenticated = false;


6. Erstat metoden **OnAppearing** med følgende kode:

        protected override async void OnAppearing()
        {
            base.OnAppearing();
    
            // Refresh items only when authenticated.
            if (authenticated == true)
            {
                // Set syncItems to true in order to synchronize the data 
                // on startup when running in offline mode.
                await RefreshItems(true, syncItems: false);

                // Hide the Sign-in button.
                this.loginButton.IsVisible = false;
            }
        }

    Dette sikrer, at dataene kun opdateret fra tjenesten, når brugeren er blevet godkendt.

7. Føje følgende handler for hændelsen **Clicked** til klassen **TodoList** :

        async void loginButton_Clicked(object sender, EventArgs e)
        {
            if (App.Authenticator != null)
                authenticated = await App.Authenticator.Authenticate();

            // Set syncItems to true to synchronize the data on startup when offline is enabled.
            if (authenticated == true)
                await RefreshItems(true, syncItems: false);
        }

8. Gemme ændringerne og genopbygge Portable klassebibliotek projektet ved at bekræfte ingen fejl.


##<a name="add-authentication-to-the-android-app"></a>Føje godkendelse til Android-appen

Dette afsnit viser, hvordan du implementere grænsefladen **IAuthenticate** i Android app projektet. Ignorere dette afsnit, hvis du ikke understøtter Android-enheder.

1. Højreklik på **droid** projektet, derefter **angive som Start projekt**i Visual Studio eller Xamarin Studio.

2. Tryk på F5 for at starte projektet i fejlfindingen, og derefter kontrollere, at en ikke-afviklet undtagelse med en statuskode af 401 (uautoriseret) opstår, når programmet starter. Dette sker, fordi access på back-end er begrænset til autoriserede brugere.

3. Åbn MainActivity.cs i Android projektet og tilføje følgende `using` sætninger:

        using Microsoft.WindowsAzure.MobileServices;
        using System.Threading.Tasks;

4. Opdatere klassen **MainActivity** for at implementere grænsefladen **IAuthenticate** på følgende måde:

        public class MainActivity : global::Xamarin.Forms.Platform.Android.FormsApplicationActivity, IAuthenticate


5. Opdatere klassen **MainActivity** ved at tilføje et **MobileServiceUser** felt og en metode til **godkendelse** , som kræves af grænsefladen **IAuthenticate** på følgende måde:

        // Define a authenticated user.
        private MobileServiceUser user;

        public async Task<bool> Authenticate()
        {
            var success = false;
            var message = string.Empty;
            try
            {
                // Sign in with Facebook login using a server-managed flow.
                user = await TodoItemManager.DefaultManager.CurrentClient.LoginAsync(this,
                    MobileServiceAuthenticationProvider.Facebook);
                if (user != null)
                {
                    message = string.Format("you are now signed-in as {0}.",
                        user.UserId);
                    success = true;
                }
            }
            catch (Exception ex)
            {
                message = ex.Message;
            }

            // Display the success or failure message.
            AlertDialog.Builder builder = new AlertDialog.Builder(this);
            builder.SetMessage(message);
            builder.SetTitle("Sign-in result");
            builder.Create().Show();

            return success;
        }


    Hvis du bruger en identitetsudbyder end Facebook, skal du vælge en anden værdi for [MobileServiceAuthenticationProvider].

6. Føj følgende kode til metoden **OnCreate** i klassen **MainActivity** før opkaldet til `LoadApplication()`:

        // Initialize the authenticator before loading the app.
        App.Init((IAuthenticate)this);

    Dette sikrer, at godkenderen er initialiseret før app indlæse.

7. Genopbygge APP'en skal køre den og derefter logge på med udbyderen af godkendelse du vælger og bekræfte, at du kan få adgang til dataene som en godkendt bruger.

##<a name="add-authentication-to-the-ios-app"></a>Tilføje godkendelse til appen iOS

Dette afsnit viser, hvordan du implementere grænsefladen **IAuthenticate** i iOS app projektet. Ignorere dette afsnit, hvis du ikke understøtter iOS-enheder.

1. Højreklik på **iOS** projektet, derefter **angive som Start projekt**i Visual Studio eller Xamarin Studio.

2. Tryk på F5 for at starte projektet i fejlfindingen, og derefter kontrollere, at en ikke-afviklet undtagelse med en statuskode af 401 (uautoriseret) opstår, når programmet starter. Dette sker, fordi access på back-end er begrænset til autoriserede brugere.

4. Åbn AppDelegate.cs i iOS projektet og tilføje følgende `using` sætninger:

        using Microsoft.WindowsAzure.MobileServices;
        using System.Threading.Tasks;

4. Opdatere klassen **AppDelegate** for at implementere grænsefladen **IAuthenticate** på følgende måde:

        public partial class AppDelegate : global::Xamarin.Forms.Platform.iOS.FormsApplicationDelegate, IAuthenticate

5. Opdatere klassen **AppDelegate** ved at tilføje et **MobileServiceUser** felt og en metode til **godkendelse** , som kræves af grænsefladen **IAuthenticate** på følgende måde:

        // Define a authenticated user.
        private MobileServiceUser user;

        public async Task<bool> Authenticate()
        {
            var success = false;
            var message = string.Empty;
            try
            {
                // Sign in with Facebook login using a server-managed flow.
                if (user == null)
                {
                    user = await TodoItemManager.DefaultManager.CurrentClient
                        .LoginAsync(UIApplication.SharedApplication.KeyWindow.RootViewController,
                        MobileServiceAuthenticationProvider.Facebook);
                    if (user != null)
                    {
                        message = string.Format("You are now signed-in as {0}.", user.UserId);
                        success = true;                        
                    }
                }        
            }
            catch (Exception ex)
            {
               message = ex.Message;
            }

            // Display the success or failure message.
            UIAlertView avAlert = new UIAlertView("Sign-in result", message, null, "OK", null);
            avAlert.Show();         

            return success;
        }

    Hvis du bruger en identitetsudbyder end Facebook, skal du vælge en anden værdi for [MobileServiceAuthenticationProvider].

6. Tilføj følgende linje af kode til metoden **FinishedLaunching** før opkaldet til `LoadApplication()`: 

        App.Init(this);

    Dette sikrer, at godkenderen startes, før app, er indlæst.

7. Genopbygge APP'en skal køre den og derefter logge på med udbyderen af godkendelse du vælger og bekræfte, at du kan få adgang til dataene som en godkendt bruger.


##<a name="add-authentication-to-windows-app-projects"></a>Føje godkendelse til Windows app projekter

Dette afsnit viser, hvordan du implementerer grænsefladen **IAuthenticate** i Windows 8.1 og Windows Phone 8.1 app projekter. De samme fremgangsmåde gælder kun for de projekter, Universal Windows Platform (UWP). Ignorere dette afsnit, hvis du ikke understøtter Windows enheder.

1. I Visual Studio, skal du højreklikke på enten **WinApp** eller **WinPhone81** projektet, derefter **angive som Start projekt**.

2. Tryk på F5 for at starte projektet i fejlfindingen, og derefter kontrollere, at en ikke-afviklet undtagelse med en statuskode af 401 (uautoriseret) opstår, når programmet starter. Dette sker, fordi access på back-end er begrænset til autoriserede brugere.

3. Åbn MainPage.xaml.cs til Windows app projektet og tilføje følgende `using` sætninger:

        using Microsoft.WindowsAzure.MobileServices;
        using System.Threading.Tasks;
        using Windows.UI.Popups;
        using <your_Portable_Class_Library_namespace>;

    Erstatte `<your_Portable_Class_Library_namespace>` med navneområdet for din bærbare klassebibliotek.

4. Opdatere klassen **MainPage** for at implementere grænsefladen **IAuthenticate** på følgende måde:

        public sealed partial class MainPage : IAuthenticate


5. Opdatere klassen **MainPage** ved at tilføje et **MobileServiceUser** felt og en metode til **godkendelse** , som kræves af grænsefladen **IAuthenticate** på følgende måde:

        // Define a authenticated user.
        private MobileServiceUser user;

        public async Task<bool> Authenticate()
        {
            string message = string.Empty;
            var success = false;

            try
            {
                // Sign in with Facebook login using a server-managed flow.
                if (user == null)
                {
                    user = await TodoItemManager.DefaultManager.CurrentClient
                        .LoginAsync(MobileServiceAuthenticationProvider.Facebook);
                    if (user != null)
                    {
                        success = true;
                        message = string.Format("You are now signed-in as {0}.", user.UserId);
                    }
                }
                
            }
            catch (Exception ex)
            {
                message = string.Format("Authentication Failed: {0}", ex.Message);
            }

            // Display the success or failure message.
            await new MessageDialog(message, "Sign-in result").ShowAsync();

            return success;
        }


    Hvis du bruger en identitetsudbyder end Facebook, skal du vælge en anden værdi for [MobileServiceAuthenticationProvider].

6. Tilføj følgende linje af kode i parametre for klassen **MainPage** før opkaldet til `LoadApplication()`:

        // Initialize the authenticator before loading the app.
        <your_Portable_Class_Library_namespace>.App.Init(this);
 
    Erstatte `<your_Portable_Class_Library_namespace>` med navneområdet for din bærbare klassebibliotek.  
    Hvis dette er WinApp projektet, kan du springe til trin 8. Næste trin gælder kun for WinPhone81 projektet, hvor du har brug for at fuldføre login tilbagekald.

7. (Valgfrit) Åbn App.xaml.cs i **WinPhone81** app projektet, og Tilføj følgende `using` sætninger:

        using Microsoft.WindowsAzure.MobileServices;
        using <your_Portable_Class_Library_namespace>;

    Erstatte `<your_Portable_Class_Library_namespace>` med navneområdet for din bærbare klassebibliotek.

8.  Føje følgende **OnActivated** metode Tilsidesæt til klassen **App** :

        protected override void OnActivated(IActivatedEventArgs args)
        {
            base.OnActivated(args);

            // We just need to handle activation that occurs after web authentication. 
            if (args.Kind == ActivationKind.WebAuthenticationBrokerContinuation)
            {
                // Get the client and call the LoginComplete method to complete authentication.
                var client = TodoItemManager.DefaultManager.CurrentClient as MobileServiceClient;
                client.LoginComplete(args as WebAuthenticationBrokerContinuationEventArgs);
            }
        }

    Når metoden tilsidesætte allerede findes, skal du tilføje lige betinget koden fra ovenstående kodestykke.

7. Genopbygge APP'en skal køre den og derefter logge på med udbyderen af godkendelse du vælger og bekræfte, at du kan få adgang til dataene som en godkendt bruger.

##<a name="next-steps"></a>Næste trin

Nu hvor du har fuldført selvstudiet basisgodkendelse, skal du overveje fortsætter på en af følgende selvstudier:

+ [Føje pushmeddelelser til din app](app-service-mobile-xamarin-forms-get-started-push.md)  
  Lær at føje opslagsnål beskeder understøtter til din app og konfigurere din Mobile-App back-end for at bruge Azure meddelelse Hubs til at sende pushmeddelelser.

+ [Aktivere offlinesynkronisering for din app](app-service-mobile-xamarin-forms-get-started-offline-data.md)  
  Lær at føje offline-understøttelse din app, ved hjælp af en Mobile-App backend. Offlinesynkronisering gør det muligt for slutbrugere til at interagere med en mobilapp&mdash;visning, tilføjelse eller redigering af data&mdash;selv, når der er ingen netværksforbindelse.

<!-- Images. -->

<!-- URLs. -->
[apns object]: http://go.microsoft.com/fwlink/p/?LinkId=272333
[LoginAsync]: https://msdn.microsoft.com/library/azure/dn268341(v=azure.10).aspx
[MobileServiceClient]: https://msdn.microsoft.com/library/azure/JJ553674(v=azure.10).aspx
[MobileServiceAuthenticationProvider]: https://msdn.microsoft.com/library/azure/jj730936(v=azure.10).aspx

