<properties
    pageTitle="Azure AD .NET Introduktion | Microsoft Azure"
    description="Sådan oprettes en .NET Windows Desktop-program, der integreres med Azure AD til logon og Azure AD-opkald beskyttet API'er ved hjælp af OAuth."
    services="active-directory"
    documentationCenter=".net"
    authors="dstrockis"
    manager="mbaldwin"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="dotnet"
    ms.topic="article"
    ms.date="09/16/2016"
    ms.author="dastrock"/>


# <a name="integrate-azure-ad-into-a-windows-desktop-wpf-app"></a>Integrere Azure AD i en Windows pc WPF-App

[AZURE.INCLUDE [active-directory-devquickstarts-switcher](../../includes/active-directory-devquickstarts-switcher.md)]

[AZURE.INCLUDE [active-directory-devguide](../../includes/active-directory-devguide.md)]

Hvis du udvikler en på computeren, gør det Azure AD, enkle og nemt for dig at godkende dine brugere med deres Active Directory-konti.  Det kan også programmet sikkert bruge en hvilken som helst web API, der er beskyttet af Azure AD, som Office 365 API'er eller Azure API.

Azure AD indeholder til .NET oprindelige klienter, der skal have adgang til beskyttede ressourcer, Active Directory Authentication Library eller ADAL.  ADALS eneste formål i liv er at gøre det lettere for din app til at få adgangstokens.  For at vise lige hvor let det er skal her vi oprettes et .NET WPF opgaveliste til computeren, som:

-   Henter få adgang til tokens for opkald til Azure AD Graph API ved hjælp af [OAuth 2.0 godkendelse protocol](https://msdn.microsoft.com/library/azure/dn645545.aspx).
-   Søger i en mappe til brugere med en given alias.
-   Fortegn brugere ud.

Hvis du vil basere fuldført arbejde programmet, skal du:

2. Registrere dit program med Azure AD.
3. Installere og konfigurere ADAL.
5. Brug ADAL til at få tokens fra Azure AD.

At komme i gang skal [hente app skelet](https://github.com/AzureADQuickStarts/NativeClient-DotNet/archive/skeleton.zip) eller [hente gennemført prøveoverførsel](https://github.com/AzureADQuickStarts/NativeClient-DotNet/archive/complete.zip).  Du skal også en Azure AD-lejer, hvor du kan oprette brugere og registrere et program.  Hvis du ikke allerede har en lejer, [se, hvordan du få en](active-directory-howto-tenant.md).

## <a name="1-register-the-directorysearcher-application"></a>*1. registrere programmet DirectorySearcher*
For at aktivere din app til at hente tokens, skal du først at registrere den i din Azure AD-lejer og tildele den tilladelse til at få adgang til API Azure AD Graph:

-   Log på administrationsportalen Azure
-   Klik på **Active Directory** i til venstre i navigationen
-   Vælg en lejer, til at registrere programmet.
-   Klik på fanen **programmer** , og klik på **Tilføj** i den nederste skuffe.
-   Følg anvisningerne, og oprette et nyt **Oprindelige-klientprogrammet**.
    -   **Navnet** på programmet, som beskriver dit program for slutbrugere
    -   **Omdirigere Uri** er en kombination af farveskema og streng, Azure AD vil bruge til at returnere token svar.  Angive en bestemt værdi til dit program, f.eks. `http://DirectorySearcher`.
-   Når du har færdiggjort registrering, Tildel AAD din app et entydigt klient-id.  Du skal bruge denne værdi i næste afsnit, så kopiere det fra fanen **Konfigurer** .
- Find også sektionen "Tilladelser til andre programmer" **Konfigurer** under fanen.  Tilføj tilladelsen **Access den din organisations Directory** under **Delegerede tilladelser**til programmet "Azure Active Directory".  Dette vil gøre det muligt at forespørge Graph API til brugere.

## <a name="2-install--configure-adal"></a>*2., installere og konfigurere ADAL*
Nu hvor du har et program i Azure AD, kan du installere ADAL og skrive din identitet-relaterede kode.  I rækkefølge for ADAL lov til at kommunikere med Azure AD, skal du give den nogle oplysninger om din app registrering.
-   Start med at tilføje ADAL til DirectorySearcher projektet ved hjælp af konsollen Package Manager.

```
PM> Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory
```

-   Åbn i project DirectorySearcher `app.config`.  Erstatte værdier af elementer i det `<appSettings>` afsnit for at afspejle de værdier, du indtaster i portalen Azure.  Din kode referencer disse værdier, når den anvender ADAL.
    -   Den `ida:Tenant` er domænet for din Azure AD-lejer, f.eks. contoso.onmicrosoft.com
    -   Den `ida:ClientId` er clientId af dit program, du har kopieret fra portalen.
    -   Den `ida:RedirectUri` er Omdiriger URL-adresse, der er registreret i portalen.

## <a name="3--use-adal-to-get-tokens-from-aad"></a>*3. Brug ADAL til at få Tokens fra AAD*
Grundlæggende princippet bag ADAL er, når din app skal et adgangstoken, det blot opkald `authContext.AcquireTokenAsync(...)`, og ADAL gør resten.  

-   I den `DirectorySearcher` project, Åbn `MainWindow.xaml.cs` og Find den `MainWindow()` metode.  Det første trin er initialiseret din app `AuthenticationContext` -ADAL er primære klasse.  Dette er, hvor du overfører ADAL koordinaterne der skal til at kommunikere med Azure AD og angiver, hvordan til cachen tokens.

```C#
public MainWindow()
{
    InitializeComponent();

    authContext = new AuthenticationContext(authority, new FileCache());

    CheckForCachedToken();
}
```

- Find nu den `Search(...)` -metoden, som vil blive aktiveret, når brugeren cliks "Search" knappen i den app brugergrænseflade.  Denne metode gør en GET-anmodning til Azure AD Graph API til forespørgsel for brugere, hvis UPN begynder med det angivne søgeord.  Men hvis du vil forespørge Graph API, skal du medtage en access_token i den `Authorization` sidehoved for din anmodning - dette er, hvor ADAL kommer.

```C#
private async void Search(object sender, RoutedEventArgs e)
{
    // Validate the Input String
    if (string.IsNullOrEmpty(SearchText.Text))
    {
        MessageBox.Show("Please enter a value for the To Do item name");
        return;
    }

    // Get an Access Token for the Graph API
    AuthenticationResult result = null;
    try
    {
        result = await authContext.AcquireTokenAsync(graphResourceId, clientId, redirectUri, new PlatformParameters(PromptBehavior.Auto));
        UserNameLabel.Content = result.UserInfo.DisplayableId;
        SignOutButton.Visibility = Visibility.Visible;
    }
    catch (AdalException ex)
    {
        // An unexpected error occurred, or user canceled the sign in.
        if (ex.ErrorCode != "access_denied")
            MessageBox.Show(ex.Message);

        return;
    }

    ...
}
```
- Når din app anmoder om et token ved at ringe til `AcquireTokenAsync(...)`, ADAL vil forsøge at returnere et token uden at spørge brugeren til legitimationsoplysninger.  Hvis ADAL registrerer, at brugeren skal logge på at få et token, viser en dialogboks til logon, indsamling brugerens legitimationsoplysninger og returnere et token efter vellykket bekræftelse.  Hvis ADAL er i stand til at returnere et token for en eller anden grund, kan det Udløs en `AdalException`.
- Bemærk, at den `AuthenticationResult` objekt indeholder en `UserInfo` objekt, der kan bruges til at indsamle oplysninger, der kan være nødvendigt din app.  I DirectorySearcher `UserInfo` bruges til at tilpasse den app Brugergrænsefladen med det bruger-id.

- Når brugeren klikker på knappen "Log af", vi vil sikre, at det næste opkald til `AcquireTokenAsync(...)` vil Bed brugeren om at logge på.  Dette er med ADAL, lige så nemt som at fjerne token cachen:

```C#
private void SignOut(object sender = null, RoutedEventArgs args = null)
{
    // Clear the token cache
    authContext.TokenCache.Clear();

    ...
}
```

- Men hvis brugeren ikke klikker på knappen "Log af", vil du bevare brugerens session til næste gang de kører på DirectorySearcher.  Når appen åbner, kan du kontrollere ADAL'S token cache til et eksisterende token og opdatere Brugergrænsefladen i overensstemmelse hermed.  I den `CheckForCachedToken()` metode, foretage et opkald til `AcquireTokenAsync(...)`, der passerer i øjeblikket den `PromptBehavior.Never` parameter.  `PromptBehavior.Never`fortæller ADAL, brugeren ikke skal anmodes om logon, og ADAL bør i stedet udløse en undtagelse, hvis det er ikke kan returnere et token.

```C#
public async void CheckForCachedToken() 
{
    // As the application starts, try to get an access token without prompting the user.  If one exists, show the user as signed in.
    AuthenticationResult result = null;
    try
    {
        result = await authContext.AcquireTokenAsync(graphResourceId, clientId, redirectUri, new PlatformParameters(PromptBehavior.Never));
    }
    catch (AdalException ex)
    {
        if (ex.ErrorCode != "user_interaction_required")
        {
            // An unexpected error occurred.
            MessageBox.Show(ex.Message);
        }

        // If user interaction is required, proceed to main page without singing the user in.
        return;
    }

    // A valid token is in the cache
    SignOutButton.Visibility = Visibility.Visible;
    UserNameLabel.Content = result.UserInfo.DisplayableId;
}
```

Tillykke! Du kan nu har en arbejde .NET WPF-programmer med muligheden for at godkende brugere, skal du ringe sikkert Web API'er ved hjælp af OAuth 2.0, og få grundlæggende oplysninger om brugeren.  Hvis du ikke allerede har gjort det, nu er det tid til at udfylde din lejer med nogle brugere.  Kør din DirectorySearcher app, og log på med en af disse brugere.  Søg efter andre brugere, der er baseret på deres UPN.  Luk app, og kør den igen.  Bemærk, hvordan brugerens session forbliver intakt.  Logge af og logge på igen, som en anden bruger.

ADAL gør det nemt at inkorporere alle disse almindelige identitet-funktioner i dit program.  Det sig tager af alle ændret arbejdet for dig - cache-styring, OAuth-protokollen support, præsenterer brugeren med et login brugergrænseflade, opdatere udløbet tokens og meget mere.  Alt, du virkelig vil vide, er et enkelt API-kald, `authContext.AcquireTokenAsync(...)`.

Til reference gennemført prøveoverførsel (uden din konfigurationsværdier) er angivet [her](https://github.com/AzureADQuickStarts/NativeClient-DotNet/archive/complete.zip).  Du kan nu gå videre til yderligere scenarier.  Vil du måske at prøve:

[Sikre en .NET Web API med Azure AD >>](active-directory-devquickstarts-webapi-dotnet.md)

[AZURE.INCLUDE [active-directory-devquickstarts-additional-resources](../../includes/active-directory-devquickstarts-additional-resources.md)]
