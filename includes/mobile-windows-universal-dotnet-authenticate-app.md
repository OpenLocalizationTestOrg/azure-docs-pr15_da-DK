
1. Åbn den delte projektfil MainPage.cs og Tilføj følgende kodestykke til klassen MainPage:
    
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

3. Kommentar ud eller slette opkaldet til metoden **RefreshTodoItems** i eksisterende **OnNavigatedTo** metode Tilsidesæt.

    Dette forhindrer, at dataene indlæses, før brugeren er godkendt. Dernæst skal tilføje du en **Log på** knappen til den app, der udløser godkendelse.

4. Føje følgende kodestykke til klassen MainPage:

        private async void ButtonLogin_Click(object sender, RoutedEventArgs e)
        {
            // Login the user and then load data from the mobile app.
            if (await AuthenticateAsync())
            {
                // Hide the login button and load items from the mobile app.
                ButtonLogin.Visibility = Windows.UI.Xaml.Visibility.Collapsed;
                //await InitLocalStoreAsync(); //offline sync support.
                await RefreshTodoItems();
            }
        }
        
5. Åbn projektfil MainPage.xaml i Windows Store app projektet, og Tilføj følgende **knappen** element lige før det element, der definerer knappen **Gem** :

        <Button Name="ButtonLogin" Click="ButtonLogin_Click" 
                        Visibility="Visible">Sign in</Button>

6. I Windows Phone Store app projektet, kan du tilføje følgende **knappen** element i **ContentPanel**efter elementet **tekstfelt** :

        <Button Grid.Row ="1" Grid.Column="1" Name="ButtonLogin" Click="ButtonLogin_Click" 
            Margin="10, 0, 0, 0" Visibility="Visible">Sign in</Button>

8. Åbn den delte App.xaml.cs project-fil, og Føj følgende kode:

        protected override void OnActivated(IActivatedEventArgs args)
        {
            // Windows Phone 8.1 requires you to handle the respose from the WebAuthenticationBroker.
            #if WINDOWS_PHONE_APP
            if (args.Kind == ActivationKind.WebAuthenticationBrokerContinuation)
            {
                // Completes the sign-in process started by LoginAsync.
                // Change 'MobileService' to the name of your MobileServiceClient instance. 
                App.MobileService.LoginComplete(args as WebAuthenticationBrokerContinuationEventArgs);
            }
            #endif

            base.OnActivated(args);
        }

    Hvis metoden **OnActivated** allerede findes, ved at tilføje den `#if...#endif` kode Bloker.

9. Tryk på F5 for at køre Windows Store-app, skal du klikke på knappen **Log på** og logge på appen med dine valgte identitetsudbyder. 

    Når du er logget på korrekt, APP'en skal køre uden fejl, og du bør kunne forespørge din backend-version og foretage opdateringer til data.

10. Højreklik på Windows Phone Store app projektet, skal du klikke på **Angiv som Start projekt**og derefter gentage forrige trin for at kontrollere, at Store på Windows Phone app også kører korrekt.  

 