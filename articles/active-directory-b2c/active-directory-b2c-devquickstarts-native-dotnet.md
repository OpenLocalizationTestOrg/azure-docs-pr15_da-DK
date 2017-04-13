<properties
    pageTitle="Azure Active Directory B2C | Microsoft Azure"
    description="Sådan oprettes en Windows-programmet, der indeholder logon, tilmeldingsprocessen og profil styring ved hjælp af Azure Active Directory B2C."
    services="active-directory-b2c"
    documentationCenter=".net"
    authors="dstrockis"
    manager="mbaldwin"
    editor=""/>

<tags
    ms.service="active-directory-b2c"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="dotnet"
    ms.topic="article"
    ms.date="07/22/2016"
    ms.author="dastrock"/>

# <a name="azure-ad-b2c-build-a-windows-desktop-app"></a>Azure AD-B2C: Oprette en Windows-programmet

Du kan føje funktioner til administration af effektive selvbetjening identitet ved hjælp af Azure Active Directory (Azure AD) B2C, til din-programmet i et par korte trin. I denne artikel viser dig, hvordan du opretter en .NET Windows Presentation Foundation (WPF) "opgaveliste" app, der indeholder bruger tilmelding, logon og administration af profil. Appen vil indeholde understøttelse af tilmelding og log på ved hjælp af et brugernavn eller mail. Det kan også indeholde understøttelse af tilmelding og log på ved hjælp af konti til sociale som Facebook og Google.

## <a name="get-an-azure-ad-b2c-directory"></a>Få et Azure AD B2C directory

Før du kan bruge Azure AD B2C, skal du oprette en mappe eller lejer.  En mappe er en beholder for alle dine brugere, apps, grupper og meget mere. Hvis du ikke allerede har et, [oprette en mappe med B2C](active-directory-b2c-get-started.md) inden du kan fortsætte i denne vejledning.

## <a name="create-an-application"></a>Oprette et program

Derefter skal du oprette en app i B2C-biblioteket. Dette giver Azure AD-oplysninger, som det skal sikkert kommunikere med din app. Hvis du vil oprette en app, skal du følge [disse instruktioner](active-directory-b2c-app-registration.md).  Sørg for at:

- Medtage en **Oprindelig klient** i programmet.
- Kopiere den **omdirigere URI** `urn:ietf:wg:oauth:2.0:oob`. Det er standard URL-adressen for denne eksempel-kode.
- Kopiere **Program-ID** , der er tildelt din app. Du skal bruge den senere.

[AZURE.INCLUDE [active-directory-b2c-devquickstarts-v2-apps](../../includes/active-directory-b2c-devquickstarts-v2-apps.md)]

## <a name="create-your-policies"></a>Oprette din politikker

I Azure AD B2C defineres hver brugeroplevelsen af en [politik](active-directory-b2c-reference-policies.md). Eksempel på denne kode indeholder tre identitet oplevelser: tilmelde dig, log på, og Rediger profil. Du skal oprette en politik for hver type, som beskrevet i [politik referenceartikel](active-directory-b2c-reference-policies.md#how-to-create-a-sign-up-policy). Når du opretter de politikker, der er tre, skal du sørge for at:

- Vælg enten **Bruger-ID tilmelding** eller **mail om tilmelding til en** i bladet identitet udbydere.
- Vælg **vist navn** og andre tilmelding attributter i din tilmelding til en politik.
- Vælg **visningsnavn** og et **Objekt-ID** krav som programmet krav om hver politik. Du kan vælge samt andre krav.
- Kopiere og indsætte **navn** for hver politik, du har oprettet. Det skal have præfikset `b2c_1_`.  Du skal bruge disse politik navne senere.

[AZURE.INCLUDE [active-directory-b2c-devquickstarts-policy](../../includes/active-directory-b2c-devquickstarts-policy.md)]

Når du har oprettet de tre politikker, er du klar til at opbygge din app.

## <a name="download-the-code"></a>Hente koden

Koden for dette selvstudium [vedligeholdes GitHub](https://github.com/AzureADQuickStarts/B2C-NativeClient-DotNet). For at oprette eksemplet, mens du skriver, kan du [hente et skelet projekt som en .zip-fil](https://github.com/AzureADQuickStarts/B2C-NativeClient-DotNet/archive/skeleton.zip). Du kan også klone skelet:

```
git clone --branch skeleton https://github.com/AzureADQuickStarts/B2C-NativeClient-DotNet.git
```

Den færdige app findes også [som en .zip-fil](https://github.com/AzureADQuickStarts/B2C-NativeClient-DotNet/archive/complete.zip) eller på den `complete` gren i den samme lager.

Når du har hentet eksempelkoden, skal du åbne filen Visual Studio .sln at komme i gang. Den `TaskClient` projekt er WPF-programmet på computeren, som brugeren skal arbejde sammen med. Med henblik på dette selvstudium kalder en back end-opgave web API, Azure, der gemmer hver brugers opgaveliste som vært.  Du behøver ikke at opbygge web API, vi har allerede den kører for dig.

Se den [web API Introduktion artikel](active-directory-b2c-devquickstarts-api-dotnet.md)for at lære, hvordan en web API godkender af anmodninger om sikker ved hjælp af Azure AD B2C.

## <a name="execute-policies"></a>Udføre politikker
Din app kommunikerer med Azure AD B2C ved at sende meddelelser til godkendelse, som angiver den politik, de vil udføre som en del af HTTP-anmodningen. Til .NET-computerprogrammer, kan du bruge eksempelvisning Microsoft godkendelse bibliotek (MSAL) for at sende OAuth 2.0 godkendelse meddelelser, skal du udføre politikker, og få tokens, der kalder web API'er.

### <a name="install-msal"></a>Installere MSAL
Tilføje MSAL til den `TaskClient` projekt ved hjælp af Visual Studio pakke Manager-konsollen.

```
PM> Install-Package Microsoft.Identity.Client -IncludePrerelease
```

### <a name="enter-your-b2c-details"></a>Angiv din B2C detaljer
Åbn filen `Globals.cs` og erstatte hver af egenskabsværdierne med dine egne. Denne klasse bruges i hele `TaskClient` til ofte anvendte referenceværdier.

```C#
public static class Globals
{
    ...

    // TODO: Replace these five default with your own configuration values
    public static string tenant = "fabrikamb2c.onmicrosoft.com";
    public static string clientId = "90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6";
    public static string signInPolicy = "b2c_1_sign_in";
    public static string signUpPolicy = "b2c_1_sign_up";
    public static string editProfilePolicy = "b2c_1_edit_profile";

    ...
}
```

[AZURE.INCLUDE [active-directory-b2c-devquickstarts-tenant-name](../../includes/active-directory-b2c-devquickstarts-tenant-name.md)]


### <a name="create-the-publicclientapplication"></a>Oprette PublicClientApplication
Den primære klasse af MSAL er `PublicClientApplication`. Denne klasse repræsenterer dit program i Azure AD B2C-systemet. Når initalizes app opretter en forekomst af `PublicClientApplication` i `MainWindow.xaml.cs`. Dette kan bruges i hele vinduet.

```C#
protected async override void OnInitialized(EventArgs e)
{
    base.OnInitialized(e);

    pca = new PublicClientApplication(Globals.clientId)
    {
        // MSAL implements an in-memory cache by default.  Since we want tokens to persist when the user closes the app, 
        // we've extended the MSAL TokenCache and created a simple FileCache in this app.
        UserTokenCache = new FileCache(),
    };
    
    ...
```

### <a name="initiate-a-sign-up-flow"></a>Starte et tilmelding flow
Når en bruger vælger at tegn op, vil du starte en tilmelding rutediagram, der bruger den tilmelding til en politik, du har oprettet. Ved hjælp af MSAL kan du blot opkald `pca.AcquireTokenAsync(...)`. De parametre, du overfører til `AcquireTokenAsync(...)` bestemme, hvilke token, du modtager, politikken anvendes i anmodningen om godkendelse, og meget mere.

```C#
private async void SignUp(object sender, RoutedEventArgs e)
{
    AuthenticationResult result = null;
    try
    {
        // Use the app's clientId here as the scope parameter, indicating that
        // you want a token to the your app's backend web API (represented by
        // the cloud hosted task API).  Use the UiOptions.ForceLogin flag to
        // indicate to MSAL that it should show a sign-up UI no matter what.
        result = await pca.AcquireTokenAsync(new string[] { Globals.clientId },
                string.Empty, UiOptions.ForceLogin, null, null, Globals.authority,
                Globals.signUpPolicy);

        // Upon success, indicate in the app that the user is signed in.
        SignInButton.Visibility = Visibility.Collapsed;
        SignUpButton.Visibility = Visibility.Collapsed;
        EditProfileButton.Visibility = Visibility.Visible;
        SignOutButton.Visibility = Visibility.Visible;

        // When the request completes successfully, you can get user 
        // information from the AuthenticationResult
        UsernameLabel.Content = result.User.Name;

        // After the sign up successfully completes, display the user's To-Do List
        GetTodoList();
    }

    // Handle any exeptions that occurred during execution of the policy.
    catch (MsalException ex)
    {
        if (ex.ErrorCode != "authentication_canceled")
        {
            // An unexpected error occurred.
            string message = ex.Message;
            if (ex.InnerException != null)
            {
                message += "Inner Exception : " + ex.InnerException.Message;
            }

            MessageBox.Show(message);
        }

        return;
    }
}
```

### <a name="initiate-a-sign-in-flow"></a>Starte et logon-flow
Du kan starte et logon flow på samme måde, du starter et tilmelding flow. Når en bruger logger på, skal den samme opkald til MSAL, denne gang ved hjælp af din logon-politik:

```C#
private async void SignIn(object sender = null, RoutedEventArgs args = null)
{
    AuthenticationResult result = null;
    try
    {
        result = await pca.AcquireTokenAsync(new string[] { Globals.clientId },
                    string.Empty, UiOptions.ForceLogin, null, null, Globals.authority,
                    Globals.signInPolicy);
        ...
```

### <a name="initiate-an-edit-profile-flow"></a>Starte en Rediger profil flow
Igen, kan du udføre en politik for Rediger profil på samme måde:

```C#
private async void EditProfile(object sender, RoutedEventArgs e)
{
    AuthenticationResult result = null;
    try
    {
        result = await pca.AcquireTokenAsync(new string[] { Globals.clientId },
                    string.Empty, UiOptions.ForceLogin, null, null, Globals.authority,
                    Globals.editProfilePolicy);
```

I alle disse tilfælde returnerer MSAL enten et token i `AuthenticationResult` eller medfører en undtagelse. Hver gang du får et token fra MSAL, kan du bruge den `AuthenticationResult.User` objekt til at opdatere brugerdata i appen, som Brugergrænsefladen. ADAL gemmer også tokenet til brug i andre dele af programmet.


### <a name="check-for-tokens-on-app-start"></a>Søg efter tokens på app start
Du kan også bruge MSAL at holde styr på brugerens logon tilstand.  Vi vil brugeren skal være logget på, selv efter at de lukker appen og åbne den igen i denne app.  Tilbage i den `OnInitialized` tilsidesætte, skal du bruge MSAL'S `AcquireTokenSilent` metode til at kontrollere, om cachelagret tokens:

```C#
AuthenticationResult result = null;
try
{
    // If the user has has a token cached with any policy, we'll display them as signed-in.
    TokenCacheItem tci = pca.UserTokenCache.ReadItems(Globals.clientId).Where(i => i.Scope.Contains(Globals.clientId) && !string.IsNullOrEmpty(i.Policy)).FirstOrDefault();
    string existingPolicy = tci == null ? null : tci.Policy;
    result = await pca.AcquireTokenSilentAsync(new string[] { Globals.clientId }, string.Empty, Globals.authority, existingPolicy, false);

    SignInButton.Visibility = Visibility.Collapsed;
    SignUpButton.Visibility = Visibility.Collapsed;
    EditProfileButton.Visibility = Visibility.Visible;
    SignOutButton.Visibility = Visibility.Visible;
    UsernameLabel.Content = result.User.Name;
    GetTodoList();
}
catch (MsalException ex)
{
    if (ex.ErrorCode == "failed_to_acquire_token_silently")
    {
        // There are no tokens in the cache.  Proceed without calling the To Do list service.
    }
    else
    {
        // An unexpected error occurred.
        string message = ex.Message;
        if (ex.InnerException != null)
        {
            message += "Inner Exception : " + ex.InnerException.Message;
        }
        MessageBox.Show(message);
    }
    return;
}
```

## <a name="call-the-task-api"></a>Ringe til opgaven API
Du har nu bruges MSAL til at udføre politikker og få tokens.  Når du vil bruge en disse tokens til at ringe til opgaven API, kan du igen bruge MSAL'S `AcquireTokenSilent` metode til at kontrollere, om cachelagret tokens:

```C#
private async void GetTodoList()
{
    AuthenticationResult result = null;
    try
    {
        // Here we want to check for a cached token, independent of whatever policy was used to acquire it.
        TokenCacheItem tci = pca.UserTokenCache.ReadItems(Globals.clientId).Where(i => i.Scope.Contains(Globals.clientId) && !string.IsNullOrEmpty(i.Policy)).FirstOrDefault();
        string existingPolicy = tci == null ? null : tci.Policy;

        // Use AcquireTokenSilent to indicate that MSAL should throw an exception if a token cannot be acquired
        result = await pca.AcquireTokenSilentAsync(new string[] { Globals.clientId }, string.Empty, Globals.authority, existingPolicy, false);

    }
    // If a token could not be acquired silently, we'll catch the exception and show the user a message.
    catch (MsalException ex)
    {
        // There is no access token in the cache, so prompt the user to sign-in.
        if (ex.ErrorCode == "failed_to_acquire_token_silently")
        {
            MessageBox.Show("Please sign up or sign in first");
            SignInButton.Visibility = Visibility.Visible;
            SignUpButton.Visibility = Visibility.Visible;
            EditProfileButton.Visibility = Visibility.Collapsed;
            SignOutButton.Visibility = Visibility.Collapsed;
            UsernameLabel.Content = string.Empty;
        }
        else
        {
            // An unexpected error occurred.
            string message = ex.Message;
            if (ex.InnerException != null)
            {
                message += "Inner Exception : " + ex.InnerException.Message;
            }
            MessageBox.Show(message);
        }

        return;
    }
    ...
```

Når opkaldet til `AcquireTokenSilentAsync(...)` lykkes, og et token findes i cachen, kan du tilføje dette token på `Authorization` overskriften for HTTP-anmodningen. Opgave web API anvender denne overskrift til at godkende anmodningen at læse brugerens opgaveliste:

```C#
    ...
    // Once the token has been returned by MSAL, add it to the http authorization header, before making the call to access the To Do list service.
    httpClient.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Bearer", result.Token);

    // Call the To Do list service.
    HttpResponseMessage response = await httpClient.GetAsync(Globals.taskServiceUrl + "/api/tasks");
    ...
```

## <a name="sign-the-user-out"></a>Log brugeren af
Til sidst, kan du bruge MSAL for at afslutte en brugers session med appen, når brugeren markerer **Log af**.  Når du bruger MSAL, er dette opnås ved at fjerne alle tokens fra cachen til sikkerhedstoken:

```C#
private void SignOut(object sender, RoutedEventArgs e)
{
    // Clear any remnants of the user's session.
    pca.UserTokenCache.Clear(Globals.clientId);

    // This is a helper method that clears browser cookies in the browser control that MSAL uses, it is not part of MSAL.
    ClearCookies();

    // Update the UI to show the user as signed out.
    TaskList.ItemsSource = string.Empty;
    SignInButton.Visibility = Visibility.Visible;
    SignUpButton.Visibility = Visibility.Visible;
    EditProfileButton.Visibility = Visibility.Collapsed;
    SignOutButton.Visibility = Visibility.Collapsed;
    return;
}
```

## <a name="run-the-sample-app"></a>Køre appen eksempel

Til sidst skal oprette og køre eksemplet.  Tilmelde dig til appen ved hjælp af en e-mail-adresse eller bruger navn. Log af og logge på igen som den samme bruger. Rediger brugerens profil. Log af og tilmelde dig ved hjælp af en anden bruger.

## <a name="add-social-idps"></a>Tilføj sociale IDPs

Appen understøtter i øjeblikket kun bruger tilmelding og log på, der bruger **lokale konti**. Dette er gemt i mappen B2C konti, der bruger et brugernavn og adgangskode. Ved hjælp af Azure AD B2C, kan du tilføje understøttelse af andre identitetsudbydere (IDPs) uden at ændre nogen af din kode.

For at føje sociale IDPs til din app, skal du starte ved at følge vejledningerne i følgende artikler. For hver IDP, du vil understøtte, skal du registrere et program i dette system og få en klient-ID.

- [Konfigurere Facebook som en IDP](active-directory-b2c-setup-fb-app.md)
- [Konfigurere Google som en IDP](active-directory-b2c-setup-goog-app.md)
- [Konfigurere Amazon som en IDP](active-directory-b2c-setup-amzn-app.md)
- [Konfigurere LinkedIn som en IDP](active-directory-b2c-setup-li-app.md)

Når du føjer identitetsudbydere til adresselisten B2C, skal du redigere hver af dine tre politikker til at medtage de nye IDPs, som beskrevet i [politik referenceartikel](active-directory-b2c-reference-policies.md). Når du gemmer dine politikker, kan du køre appen igen. Du bør se de nye IDPs tilføjet som logon og tilmelding indstillinger i hver af din identitet oplever.

Du kan eksperimentere med din politikker og overhold virkningen på din eksempel-app. Tilføje eller fjerne IDPs, manipulere programmet krav eller ændre tilmelding attributter. Eksperimentere, indtil du kan se, hvordan politikker, anmodninger om godkendelse og MSAL knytter sammen.

Til reference gennemført prøveoverførsel [leveres som en .zip-fil](https://github.com/AzureADQuickStarts/B2C-NativeClient-DotNet/archive/complete.zip). Du kan også klone fra GitHub:

```git clone --branch complete https://github.com/AzureADQuickStarts/B2C-NativeClient-DotNet.git```
