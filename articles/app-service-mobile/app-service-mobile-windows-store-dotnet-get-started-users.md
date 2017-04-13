<properties
    pageTitle="Føje godkendelse til din app Universal Windows Platform (UWP) | Azure-Mobilapps"
    description="Lær at bruge Azure App Service Mobile-Apps til at godkende brugere af Universal Windows Platform (UWP) appen med forskellige identitetsudbydere, herunder: AAD, Google, Facebook, Twitter og Microsoft."
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
    ms.topic="article"
    ms.date="10/01/2016"
    ms.author="adrianha"/>

# <a name="add-authentication-to-your-windows-app"></a>Føje godkendelse til din Windows-app

[AZURE.INCLUDE [app-service-mobile-selector-get-started-users](../../includes/app-service-mobile-selector-get-started-users.md)]

Dette emne viser, hvordan du føje skybaseret godkendelse til din mobile-app. I dette selvstudium føjer du godkendelse til projektets Universal Windows Platform (UWP) Hurtig start til Mobile-Apps ved hjælp af en identitetsudbyder, der understøttes af Azure App Service. Når der er blevet godkendt og godkendt af din Mobile-App backend-version, vises værdien bruger-ID.

Dette selvstudium er baseret på Mobile-Apps Hurtig start. Først skal du udføre selvstudiet [komme i gang med Mobile-Apps](app-service-mobile-windows-store-dotnet-get-started.md).

##<a name="register"></a>Registrere din app til godkendelse og konfigurere tjenesten App

[AZURE.INCLUDE [app-service-mobile-register-authentication](../../includes/app-service-mobile-register-authentication.md)]

##<a name="permissions"></a>Begrænse tilladelser på godkendte brugere

[AZURE.INCLUDE [app-service-mobile-restrict-permissions-dotnet-backend](../../includes/app-service-mobile-restrict-permissions-dotnet-backend.md)]

Nu kan du kontrollere, at anonym adgang til din backend-version er deaktiveret. Med UWP app projektet angive som opstart projektet, installere og køre appen; Kontrollér, at en ikke-afviklet undtagelse med en statuskode af 401 (uautoriseret) opstår, når programmet starter. Dette sker, fordi app forsøger at få adgang til din Mobile App kode som en ikke-godkendte bruger, men tabellen *TodoItem* nu kræver godkendelse.

Derefter skal vil du opdatere appen for at godkende brugere inden du anmoder om ressourcer fra din App-tjeneste.

##<a name="add-authentication"></a>Føje godkendelse til appen

1. I UWP app project filen MainPage.cs og føje følgende kodestykke til klassen MainPage:
    
        // Define a member variable for storing the signed-in user. 
        private MobileServiceUser user;

        // Define a method that performs the authentication process
        // using a Facebook sign-in. 
        private async System.Threading.Tasks.Task<bool> AuthenticateAsync()
        {
            string message;
            bool success = false;
            try
            {
                // Change 'MobileService' to the name of your MobileServiceClient instance.
                // Sign-in using Facebook authentication.
                user = await App.MobileService
                    .LoginAsync(MobileServiceAuthenticationProvider.Facebook);
                message =
                    string.Format("You are now signed in - {0}", user.UserId);

                success = true;
            }
            catch (InvalidOperationException)
            {
                message = "You must log in. Login Required";
            }

            var dialog = new MessageDialog(message);
            dialog.Commands.Add(new UICommand("OK"));
            await dialog.ShowAsync();
            return success;
        }

    Denne kode godkender brugeren med et Facebook-logon. Hvis du bruger en identitetsudbyder end Facebook, kan du ændre værdien af **MobileServiceAuthenticationProvider** over til værdien for din udbyder.

3. Kommentar ud eller slette opkaldet til metoden **ButtonRefresh_Click** (eller metoden **InitLocalStoreAsync** ) i eksisterende **OnNavigatedTo** metode Tilsidesæt. Dette forhindrer, at dataene indlæses, før brugeren er godkendt. Dernæst skal tilføje du en **Log på** knappen til den app, der udløser godkendelse.

4. Føje følgende kodestykke til klassen MainPage:

        private async void ButtonLogin_Click(object sender, RoutedEventArgs e)
        {
            // Login the user and then load data from the mobile app.
            if (await AuthenticateAsync())
            {
                // Switch the buttons and load items from the mobile app.
                ButtonLogin.Visibility = Visibility.Collapsed;
                ButtonSave.Visibility = Visibility.Visible;
                //await InitLocalStoreAsync(); //offline sync support.
                await RefreshTodoItems();
            }
        }
        
5. Åbn filen MainPage.xaml project, Find det element, der definerer knappen **Gem** og erstatte den med følgende kode:

        <Button Name="ButtonSave" Visibility="Collapsed" Margin="0,8,8,0" 
                Click="ButtonSave_Click">
            <StackPanel Orientation="Horizontal">
                <SymbolIcon Symbol="Add"/>
                <TextBlock Margin="5">Save</TextBlock>
            </StackPanel>
        </Button>
        <Button Name="ButtonLogin" Visibility="Visible" Margin="0,8,8,0" 
                Click="ButtonLogin_Click" TabIndex="0">
            <StackPanel Orientation="Horizontal">
                <SymbolIcon Symbol="Permissions"/>
                <TextBlock Margin="5">Sign in</TextBlock> 
            </StackPanel>
        </Button>

9. Tryk på F5 for at køre app, skal du klikke på knappen **Log på** og logge på appen med dine valgte identitetsudbyder. Når din logonadresse er gået igennem, app'en kører uden fejl, og du er i stand til at forespørge din backend-version og foretage opdateringer til data.


##<a name="tokens"></a>Store godkendelse tokenet på klienten

Det forrige eksempel viste en standard-logon, som kræver klienten til at kontakte både identitetsudbyder og App-tjenesten, hver gang appen startes. Ikke alene er denne metode ineffektiv, du kan køre i brugen-relaterer problemer skal mange kunder forsøger at starte app på samme tid. En bedre fremgangsmåde er at cachen godkendelse tokenet returneres af din App tjeneste og forsøger at bruge dette første inden du bruger en udbyder-baserede logon.

>[AZURE.NOTE]Du kan gemme i cachen tokenet udstedt af App tjenester, uanset om du bruger klient-administreret eller service-administreret godkendelse. Dette selvstudium bruger service-administreret godkendelse.

[AZURE.INCLUDE [mobile-windows-universal-dotnet-authenticate-app-with-token](../../includes/mobile-windows-universal-dotnet-authenticate-app-with-token.md)]

##<a name="next-steps"></a>Næste trin

Nu hvor du har fuldført selvstudiet basisgodkendelse, skal du overveje fortsætter på en af følgende selvstudier:

+ [Føje pushmeddelelser til din app](app-service-mobile-windows-store-dotnet-get-started-push.md)  
  Lær at føje opslagsnål beskeder understøtter til din app og konfigurere din Mobile-App back-end for at bruge Azure meddelelse Hubs til at sende pushmeddelelser.

+ [Aktivere offlinesynkronisering for din app](app-service-mobile-windows-store-dotnet-get-started-offline-data.md)  
  Lær at føje offline-understøttelse din app, ved hjælp af en Mobile-App backend. Offlinesynkronisering gør det muligt for slutbrugere til at interagere med en mobilapp&mdash;visning, tilføjelse eller redigering af data&mdash;selv, når der er ingen netværksforbindelse.


<!-- URLs. -->
[Get started with your mobile app]: app-service-mobile-windows-store-dotnet-get-started.md

