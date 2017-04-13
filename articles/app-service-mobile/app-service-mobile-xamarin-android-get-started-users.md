<properties
    pageTitle="Introduktion til godkendelse til Mobile-Apps i Xamarin Android"
    description="Lær at bruge Mobile-Apps til at godkende brugere af din Xamarin Android app gennem en række forskellige identitetsudbydere, herunder AAD, Google, Facebook, Twitter og Microsoft."
    services="app-service\mobile"
    documentationCenter="xamarin"
    authors="adrianhall"
    manager="dwrede"
    editor=""/>

<tags
    ms.service="app-service-mobile"
    ms.workload="mobile"
    ms.tgt_pltfrm="mobile-xamarin-android"
    ms.devlang="dotnet"
    ms.topic="article"
    ms.date="10/01/2016"
    ms.author="adrianha"/>

# <a name="add-authentication-to-your-xamarinandroid-app"></a>Føje godkendelse til din Xamarin.Android-app

[AZURE.INCLUDE [app-service-mobile-selector-get-started-users](../../includes/app-service-mobile-selector-get-started-users.md)]

Dette emne beskrives, hvordan til at godkende brugere af en Mobile-App fra klientprogrammet. I dette selvstudium, kan du tilføje godkendelse til hurtig start projektet ved hjælp af en identitetsudbyder, der understøttes af Azure Mobile-Apps. Når der er blevet godkendt og autoriserede i Mobile-Appen, vises værdien bruger-ID.

Dette selvstudium er baseret på Mobile-App Hurtig start. Du skal også først fuldføre selvstudiet [oprette en Xamarin.Android app]. Hvis du ikke bruger hentede Hurtig start server projektet, skal du føje pakken til godkendelse lokalnummer til projektet. Du kan finde flere oplysninger om server lokalnummer pakker, under [arbejde med .NET back end-serveren SDK til Azure Mobile-Apps](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md).

##<a name="register"></a>Registrere din app til godkendelse og konfigurere App Services

[AZURE.INCLUDE [app-service-mobile-register-authentication](../../includes/app-service-mobile-register-authentication.md)]

##<a name="permissions"></a>Begrænse tilladelser på godkendte brugere

[AZURE.INCLUDE [app-service-mobile-restrict-permissions-dotnet-backend](../../includes/app-service-mobile-restrict-permissions-dotnet-backend.md)]

I Visual Studio eller Xamarin Studio skal du køre klientprojektet på en enhed eller emulator. Kontrollér, at en ikke-afviklet undtagelse med en statuskode af 401 (uautoriseret) opstår, når programmet starter. Dette sker, fordi app forsøger at få adgang til din Mobile-App backend-version som en ikke-godkendte bruger. Tabellen *TodoItem* nu kræver godkendelse.

Derefter skal opdateres du klient-app til anmodning om ressourcer fra Mobile-App back-end med en godkendt bruger.

##<a name="add-authentication"></a>Føje godkendelse til appen

Appen opdateres Hvis brugerne skal trykke på knappen **Log på** og godkende før data vises.

1. Føj følgende kode til klassen **TodoActivity** :

        // Define a authenticated user.
        private MobileServiceUser user;
        private async Task<bool> Authenticate()
        {
                var success = false;
                try
                {
                    // Sign in with Facebook login using a server-managed flow.
                    user = await client.LoginAsync(this,
                        MobileServiceAuthenticationProvider.Facebook);
                    CreateAndShowDialog(string.Format("you are now logged in - {0}",
                        user.UserId), "Logged in!");

                    success = true;
                }
                catch (Exception ex)
                {
                    CreateAndShowDialog(ex, "Authentication failed");
                }
                return success;
        }

        [Java.Interop.Export()]
        public async void LoginUser(View view)
        {
            // Load data only after authentication succeeds.
            if (await Authenticate())
            {
                //Hide the button after authentication succeeds.
                FindViewById<Button>(Resource.Id.buttonLoginUser).Visibility = ViewStates.Gone;

                // Load the data.
                OnRefreshItemsSelected();
            }
        }

    Dette opretter en ny metode til at godkende en bruger og en metode handler til en ny **logge på** knap. Brugeren i ovenstående eksempelkode er godkendt ved hjælp af et Facebook-logon. En dialogboks bruges til at vise det bruger-ID, der aktiverer efter godkendelse.

    > [AZURE.NOTE] Hvis du bruger en identitetsudbyder end Facebook, ændre den værdi, der overføres til **LoginAsync** over til en af følgende: _MicrosoftAccount_, _Twitter_, _Google_eller _WindowsAzureActiveDirectory_.

3. I metoden **OnCreate** slettes eller kommentar fra følgende linje af kode:

        OnRefreshItemsSelected ();

4. Tilføj følgende *LoginUser* knappen definitionen før knappen eksisterende *Husk* i filen Activity_To_Do.axml:

        <Button
            android:id="@+id/buttonLoginUser"
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:onClick="LoginUser"
            android:text="@string/login_button_text" />

5. Tilføj følgende element i filen Strings.xml ressourcer:

        <string name="login_button_text">Sign in</string>

6. I Visual Studio eller Xamarin Studio skal køre klientprojektet på en enhed eller emulator og logge på med din valgte identitetsudbyder.

    Når du er logget på korrekt, appen vises dit login-ID og listen over elementer, opgaveliste, og du kan foretage opdateringer til dataene.


<!-- URLs. -->
[Oprette en Xamarin.Android-app]: app-service-mobile-xamarin-android-get-started.md
