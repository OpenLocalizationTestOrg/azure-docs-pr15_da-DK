<properties
pageTitle="Azure Active Directory v2.0 .NET oprindelige App | Microsoft Azure"
description="Sådan oprettes en oprindelig .NET-app, der logger på med begge personlig Microsoft-Account brugere og arbejds- eller skolekonto konti."
services="active-directory"
documentationCenter=""
authors="dstrockis"
manager="mbaldwin"
editor=""/>

<tags
ms.service="active-directory"
ms.workload="identity"
ms.tgt_pltfrm="na"
ms.devlang="dotnet"
ms.topic="article"
ms.date="07/30/2016"
ms.author="dastrock; vittorib"/>

# <a name="add-sign-in-to-a-windows-desktop-app"></a>Føje-logon til en Windows-skrivebord app

Med den v2.0 slutpunktet, du kan hurtigt tilføje godkendelse til programmerne på skrivebordet med understøttelse af begge personlige Microsoft-konti og arbejds- eller skolekonto konti.  Gør det også muligt din app til sikker kommunikere med en back end-web api samt [Microsoft Graph](https://graph.microsoft.io) og nogle af de [Office 365 Unified API'er](https://www.msdn.com/office/office365/howto/authenticate-Office-365-APIs-using-v2).

> [AZURE.NOTE] Ikke alle Azure Active Directory (AD) scenarier og funktioner, der understøttes af v2.0 slutpunkt.  For at bestemme, hvis du skal bruge v2.0 slutpunkt skal du læse om [v2.0 begrænsninger](active-directory-v2-limitations.md).

[.NET oprindelige apps, der kører på en enhed](active-directory-v2-flows.md#mobile-and-native-apps), skal indeholder Azure AD Microsoft identitet Authentication Library eller MSAL.  MSALS eneste formål i liv er at gøre det lettere for din app til at hente tokens for opkald til webtjenester.  For at vise lige hvor let det er kan her vi udvikle en .NET WPF opgaveliste app, som:

- Logger brugeren og får adgang til tokens ved hjælp af [OAuth 2.0 godkendelse protocol](active-directory-v2-protocols.md#oauth2-authorization-code-flow).
- Sikker kalder en back-end opgaveliste webtjeneste, der er også sikret med OAuth 2.0.
- Logger brugeren ud.

## <a name="download-sample-code"></a>Hente eksempelkode

Koden for dette selvstudium vedligeholdes [GitHub](https://github.com/AzureADQuickStarts/AppModelv2-NativeClient-DotNet).  Hvis du vil følge med, kan du [hente den app skelet som en .zip](https://github.com/AzureADQuickStarts/AppModelv2-NativeClient-DotNet/archive/skeleton.zip) eller Klon skelet:

    git clone --branch skeleton https://github.com/AzureADQuickStarts/AppModelv2-NativeClient-DotNet.git

Den færdige app er angivet i slutningen af dette selvstudium samt.

## <a name="register-an-app"></a>Registrere en app
Oprette en ny app på [apps.dev.microsoft.com](https://apps.dev.microsoft.com/?referrer=https://azure.microsoft.com/documentation/articles&deeplink=/appList), eller Følg disse [detaljeret vejledning](active-directory-v2-app-registration.md).  Sørg for at:

- Kopiér ned på **Program-Id** , tildelt til din app, du skal bruge den snart.
- Tilføj **Mobile** platformen til din app.

## <a name="install--configure-msal"></a>Installere og konfigurere MSAL
Nu hvor du har en app, der er registreret hos Microsoft, kan du installere MSAL og skrive din identitet-relaterede kode.  I rækkefølge for MSAL lov til at kommunikere v2.0 slutpunktet, skal du give den nogle oplysninger om din app registrering.

-   Start med at tilføje MSAL til TodoListClient projektet ved hjælp af konsollen Package Manager.

```
PM> Install-Package Microsoft.Identity.Client -ProjectName TodoListClient -IncludePrerelease
```

-   Åbn i project TodoListClient `app.config`.  Erstatte værdier af elementer i det `<appSettings>` afsnit for at afspejle de værdier, du indtaster i portalen app registrering.  Din kode refererer disse værdier, når den anvender MSAL.
    -   Den `ida:ClientId` er din app, du har kopieret fra portalen **Program-Id** .

- Åbn i project TodoList Service- `web.config` i roden af projektet.  
    - Erstatte den `ida:Audience` værdi med det samme **Program-Id** fra portalen.

## <a name="use-msal-to-get-tokens"></a>Brug MSAL til at hente tokens
Grundlæggende princippet bag MSAL er, når din app skal et adgangstoken, skal du blot opkald `app.AcquireToken(...)`, og MSAL gør resten.  

-   I den `TodoListClient` project, Åbn `MainWindow.xaml.cs` og Find den `OnInitialized(...)` metode.  Det første trin er initialiseret din app `PublicClientApplication` -MSALS primære klasse, der repræsenterer oprindelige programmer.  Dette er, hvor du overfører MSAL koordinaterne der skal til at kommunikere med Azure AD og angiver, hvordan til cachen tokens.

```C#
protected override async void OnInitialized(EventArgs e)
{
        base.OnInitialized(e);

        app = new PublicClientApplication(new FileCache());
        AuthenticationResult result = null;
        ...
}
```

- Når programmet starter, vil vi Kontrollér, og hvis brugeren allerede er logget på appen.  Men vi kan ikke aktivere en logon-Brugergrænsefladen endnu – vi foretager brugeren, skal du klikke på "Log på" for at gøre dette.  Også i den `OnInitialized(...)` metode:

```C#
// As the app starts, we want to check to see if the user is already signed in.
// You can do so by trying to get a token from MSAL, using the method
// AcquireTokenSilent.  This forces MSAL to throw an exception if it cannot
// get a token for the user without showing a UI.
try
{
    result = await app.AcquireTokenSilentAsync(new string[] { clientId });
    // If we got here, a valid token is in the cache - or MSAL was able to get a new oen via refresh token.
    // Proceed to fetch the user's tasks from the TodoListService via the GetTodoList() method.
    
    SignInButton.Content = "Clear Cache";
    GetTodoList();
}
catch (MsalException ex)
{
    if (ex.ErrorCode == "user_interaction_required")
    {
        // If user interaction is required, the app should take no action,
        // and simply show the user the sign in button.
    }
    else
    {
        // Here, we catch all other MsalExceptions
        string message = ex.Message;
        if (ex.InnerException != null)
        {
            message += "Inner Exception : " + ex.InnerException.Message;
        }
        MessageBox.Show(message);
    }
}

```

- Hvis brugeren ikke er logget på, og de skal du klikke på knappen "Log på", ønsker vi at kalde en login Brugergrænsefladen og brugeren skal angive deres legitimationsoplysninger.  Implementere logon knappen handleren:

```C#
private async void SignIn(object sender = null, RoutedEventArgs args = null)
{
        // TODO: Sign the user out if they clicked the "Clear Cache" button

// If the user clicked the 'Sign-In' button, force
// MSAL to prompt the user for credentials by using
// AcquireTokenAsync, a method that is guaranteed to show a prompt to the user.
// MSAL will get a token for the TodoListService and cache it for you.

AuthenticationResult result = null;
try
{
    result = await app.AcquireTokenAsync(new string[] { clientId });
    SignInButton.Content = "Clear Cache";
    GetTodoList();
}
catch (MsalException ex)
{
    // If MSAL cannot get a token, it will throw an exception.
    // If the user canceled the login, it will result in the
    // error code 'authentication_canceled'.

    if (ex.ErrorCode == "authentication_canceled")
    {
        MessageBox.Show("Sign in was canceled by the user");
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


}
```

- Hvis brugeren korrekt tegn på, MSAL, modtage og cachelagre et token for dig, og du kan fortsætte med at ringe på `GetTodoList()` metode uden bekymringer.  Alle, der er tilbage for at få en brugers opgaver er at implementere den `GetTodoList()` metode.

```C#
private async void GetTodoList()
{

AuthenticationResult result = null;
try
{
    // Here, we try to get an access token to call the TodoListService
    // without invoking any UI prompt.  AcquireTokenSilentAsync forces
    // MSAL to throw an exception if it cannot get a token silently.


    result = await app.AcquireTokenSilentAsync(new string[] { clientId });
}
catch (MsalException ex)
{
    // MSAL couldn't get a token silently, so show the user a message
    // and let them click the Sign-In button.

    if (ex.ErrorCode == "user_interaction_required")
    {
        MessageBox.Show("Please sign in first");
        SignInButton.Content = "Sign In";
    }
    else
    {
        // In any other case, an unexpected error occurred.

        string message = ex.Message;
        if (ex.InnerException != null)
        {
            message += "Inner Exception : " + ex.InnerException.Message;
        }
        MessageBox.Show(message);
    }

    return;
}

// Once the token has been returned by MSAL,
// add it to the http authorization header,
// before making the call to access the To Do list service.

httpClient.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Bearer", result.Token);


        ...
...


- When the user is done managing their To-Do List, they may finally sign out of the app by clicking the "Clear Cache" button.

```C#
private async void SignIn(object sender = null, RoutedEventArgs args = null)
{
        // If the user clicked the 'clear cache' button,
        // clear the MSAL token cache and show the user as signed out.
        // It's also necessary to clear the cookies from the browser
        // control so the next user has a chance to sign in.

        if (SignInButton.Content.ToString() == "Clear Cache")
        {
                TodoList.ItemsSource = string.Empty;
                app.UserTokenCache.Clear(app.ClientId);
                ClearCookies();
                SignInButton.Content = "Sign In";
                return;
        }

        ...
```

## <a name="run"></a>Kør

Tillykke! Du har nu en arbejde .NET WPF-app, der har mulighed for at godkende brugere og sikkert opkald Web API'er ved hjælp af OAuth 2.0.  Kør dine begge projekter, og log på med en personlig Microsoft-konto eller arbejds-eller skolekonto.  Føje opgaver til brugerens opgaveliste.  Logge af og logge på igen, som en anden bruger til at få vist deres opgaveliste.  Luk app, og kør den igen.  Bemærk, hvordan brugerens session forbliver intakt - der sker, fordi app cachelagres tokens i en lokal fil.

MSAL gør det nemt at inkorporere almindelige identitet funktioner i din app, ved hjælp af både personlige og konti.  Den sig tager af alle ændret arbejdet for dig - cache-styring, OAuth-protokollen support, præsenterer brugeren med et login brugergrænseflade, opdatere udløbet tokens og meget mere.  Alt, du virkelig vil vide, er et enkelt API-kald, `app.AcquireTokenAsync(...)`.

Til reference kan på gennemført prøveoverførsel (uden din konfigurationsværdier), [der er angivet en .zip her](https://github.com/AzureADQuickStarts/AppModelv2-NativeClient-DotNet/archive/complete.zip), eller du klone den fra GitHub:

```git clone --branch complete https://github.com/AzureADQuickStarts/AppModelv2-NativeClient-DotNet.git```

## <a name="next-steps"></a>Næste trin

Nu kan du flytte til mere avancerede emner.  Vil du måske at prøve:

- [Sikring af TodoListService Web API til v2.0 slutpunktet](active-directory-v2-devquickstarts-dotnet-api.md)

Yderligere ressourcer, se:  

- [Guiden v2.0 udvikler >>](active-directory-appmodel-v2-overview.md)
- [StackOverflow "msal" mærke >>](http://stackoverflow.com/questions/tagged/msal)

## <a name="get-security-updates-for-our-products"></a>Hente sikkerhedsopdateringer til vores produkter

Vi anbefaler, at finde meddelelser om når sikkerhedshændelser forekommer, ved at besøge [denne side](https://technet.microsoft.com/security/dd252948) og abonnere på vejledende sikkerhedsadvarsler.
