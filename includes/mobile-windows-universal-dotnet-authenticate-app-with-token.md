
1. Tilføj følgende **ved hjælp af** sætninger i projektfil MainPage.xaml.cs:

        using System.Linq;      
        using Windows.Security.Credentials;

2. Erstat metoden **AuthenticateAsync** med følgende kode:

        private async System.Threading.Tasks.Task<bool> AuthenticateAsync()
        {
            string message;
            bool success = false;

            // This sample uses the Facebook provider.
            var provider = MobileServiceAuthenticationProvider.Facebook;

            // Use the PasswordVault to securely store and access credentials.
            PasswordVault vault = new PasswordVault();
            PasswordCredential credential = null;

            try
            {
                // Try to get an existing credential from the vault.
                credential = vault.FindAllByResource(provider.ToString()).FirstOrDefault();
            }
            catch (Exception)
            {
                // When there is no matching resource an error occurs, which we ignore.
            }

            if (credential != null)
            {
                // Create a user from the stored credentials.
                user = new MobileServiceUser(credential.UserName);
                credential.RetrievePassword();
                user.MobileServiceAuthenticationToken = credential.Password;

                // Set the user from the stored credentials.
                App.MobileService.CurrentUser = user;

                // Consider adding a check to determine if the token is 
                // expired, as shown in this post: http://aka.ms/jww5vp.

                success = true;
                message = string.Format("Cached credentials for user - {0}", user.UserId);
            }
            else
            {
                try
                {
                    // Login with the identity provider.
                    user = await App.MobileService
                        .LoginAsync(provider);

                    // Create and store the user credentials.
                    credential = new PasswordCredential(provider.ToString(),
                        user.UserId, user.MobileServiceAuthenticationToken);
                    vault.Add(credential);

                    success = true;
                    message = string.Format("You are now logged in - {0}", user.UserId);
                }
                catch (MobileServiceInvalidOperationException)
                {
                    message = "You must log in. Login Required";
                }
            }
            
            var dialog = new MessageDialog(message);
            dialog.Commands.Add(new UICommand("OK"));
            await dialog.ShowAsync();

            return success;
        }

    I denne version af **AuthenticateAsync**forsøger appen at bruge legitimationsoplysninger, der er gemt i **PasswordVault** til at få adgang til tjenesten. En almindelig logon er også udføres, når der ikke er nogen gemte legitimationsoplysninger.

    >[AZURE.NOTE]Et cachelagret token kan være udløbet, og token udløb kan også opstå efter godkendelse, når appen er i brug. For at lære at finde ud af, om et token er udløbet, skal du se [søge efter udløbet godkendelsestokens](http://aka.ms/jww5vp). Du kan finde en løsning til håndtering af godkendelse fejl, der er relateret til udløber tokens [cachelagring og håndtering af udløbet tokens i Azure Mobile Services administrerede SDK](http://blogs.msdn.com/b/carlosfigueira/archive/2014/03/13/caching-and-handling-expired-tokens-in-azure-mobile-services-managed-sdk.aspx)indlægget. 

3. Genstarte appen to gange.

    Bemærk, på den første opstart logge på med udbyderen, der kræves igen. Dog på den anden genstart de cachelagrede legitimationsoplysninger anvendes og logon er tilsidesat. 
