<properties
    pageTitle="Azure AD v2.0 .NET online | Microsoft Azure"
    description="Sådan oprettes en .NET MVC WebApp, opkald web services med personlige Microsoft-konti og arbejde eller skolekonti til logon."
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
    ms.date="10/27/2016"
    ms.author="dastrock"/>

# <a name="calling-a-web-api-from-a-net-web-app"></a>Ringe til en web API fra en .NET WebApp

Du kan hurtigt tilføje godkendelse til din online og web API'er med understøttelse af begge personlige Microsoft-konti og arbejds- eller skolekonto konti med slutpunkt v2.0.  Her skal vi oprette en MVC WebApp, der logger brugere på ved hjælp af OpenID oprette forbindelse, med hjælp fra Microsofts OWIN programmer.  WebApp kan hente OAuth 2.0 access tokens for et websted, api sikret ved OAuth 2.0, der kan oprette, læse og slette i en given bruger "opgaveliste".

Dette selvstudium fokuserer primært på ved hjælp af MSAL til at købe og bruge access tokens i en WebApp, der er beskrevet i fuld [her](active-directory-v2-flows.md#web-apps).  Som forudsætninger, kan du få først se, hvordan du [tilføjer grundlæggende - logon til en WebApp](active-directory-v2-devquickstarts-dotnet-web.md) eller hvordan du [sikrer korrekt web API](active-directory-v2-devquickstarts-dotnet-api.md).

> [AZURE.NOTE]
    Ikke alle Azure Active Directory scenarier og funktioner, der understøttes af v2.0 slutpunkt.  For at bestemme, hvis du skal bruge v2.0 slutpunkt skal du læse om [v2.0 begrænsninger](active-directory-v2-limitations.md).

## <a name="download-sample-code"></a>Hente eksempelkode

Koden for dette selvstudium vedligeholdes [GitHub](https://github.com/AzureADQuickStarts/AppModelv2-WebApp-WebAPI-OpenIdConnect-DotNet).  Hvis du vil følge med, kan du [hente den app skelet som en .zip](https://github.com/AzureADQuickStarts/AppModelv2-WebApp-WebAPI-OpenIdConnect-DotNet/archive/skeleton.zip) eller klone skelet:

```git clone --branch skeleton https://github.com/AzureADQuickStarts/AppModelv2-WebApp-WebAPI-OpenIdConnect-DotNet.git```

Alternativt kan du [hente den færdige app som en .zip](https://github.com/AzureADQuickStarts/AppModelv2-WebApp-WebAPI-OpenIdConnect-DotNet/archive/complete.zip) eller klone færdige app:

```git clone --branch complete https://github.com/AzureADQuickStarts/AppModelv2-WebApp-WebAPI-OpenIdConnect-DotNet.git```

## <a name="register-an-app"></a>Registrere en app
Oprette en ny app på [apps.dev.microsoft.com](https://apps.dev.microsoft.com/?referrer=https://azure.microsoft.com/documentation/articles&deeplink=/appList), eller Følg disse [detaljeret vejledning](active-directory-v2-app-registration.md).  Sørg for at:

- Kopiér ned på **Program-Id** , tildelt til din app, du skal bruge den snart.
- Oprette en **App hemmeligt** af typen **adgangskode** og kopiere hele vejen ned dens værdi til senere
- Tilføje **Web** platform til din app.
- Angiv den korrekte **Omdirigere URI**. Omdiriger uri angiver til Azure AD hvor godkendelse svar skal omdirigeres - standarden for dette selvstudium er `https://localhost:44326/`.


## <a name="install-owin"></a>Installere OWIN
Tilføje OWIN program NuGet pakker til den `TodoList-WebApp` project ved hjælp af konsollen Package Manager.  OWIN programmer bruges til at udstede logon og hvor anmodninger, administrere brugerens session og få oplysninger om brugeren, bl.a.

```
PM> Install-Package Microsoft.Owin.Security.OpenIdConnect -ProjectName TodoList-WebApp
PM> Install-Package Microsoft.Owin.Security.Cookies -ProjectName TodoList-WebApp
PM> Install-Package Microsoft.Owin.Host.SystemWeb -ProjectName TodoList-WebApp
```

## <a name="sign-the-user-in"></a>Logonskærm brugeren
Nu konfigurere OWIN programmer at bruge [OpenID forbinde godkendelse protocol](active-directory-v2-protocols.md#openid-connect-sign-in-flow).  

-   Åbn den `web.config` fil i roden af den `TodoList-WebApp` project, og angiv din app konfiguration af værdier i den `<appSettings>` sektion.
    -   Den `ida:ClientId` er det **Program-Id** , der er tildelt til din app i portalen registrering.
    - Den `ida:ClientSecret` er **App hemmeligt** , du oprettede i portalen registrering.
    -   Den `ida:RedirectUri` er **Omdirigere Uri** , du har angivet i portalen.
- Åbn den `web.config` fil i roden af den `TodoList-Service` project, og Erstat den `ida:Audience` med det samme **Program-Id** som ovenfor.


- Åbn filen `App_Start\Startup.Auth.cs` og tilføje `using` -sætningerne for biblioteker fra ovenfor.
- I den samme fil implementere den `ConfigureAuth(...)` metode.  De parametre, du giver i `OpenIDConnectAuthenticationOptions` skal fungere som koordinater for din app til at kommunikere med Azure AD.

```C#
public void ConfigureAuth(IAppBuilder app)
{
    app.SetDefaultSignInAsAuthenticationType(CookieAuthenticationDefaults.AuthenticationType);

    app.UseCookieAuthentication(new CookieAuthenticationOptions());

    app.UseOpenIdConnectAuthentication(
        new OpenIdConnectAuthenticationOptions
        {

                    // The `Authority` represents the v2.0 endpoint - https://login.microsoftonline.com/common/v2.0 
                    // The `Scope` describes the permissions that your app will need.  See https://azure.microsoft.com/documentation/articles/active-directory-v2-scopes/
                    // In a real application you could use issuer validation for additional checks, like making sure the user's organization has signed up for your app, for instance.

                    ClientId = clientId,
                    Authority = String.Format(CultureInfo.InvariantCulture, aadInstance, "common", "/v2.0 "),
                    Scope = "openid email profile offline_access",
                    RedirectUri = redirectUri,
                    PostLogoutRedirectUri = redirectUri,
                    TokenValidationParameters = new TokenValidationParameters
                    {
                        ValidateIssuer = false,
                    },

                    // The `AuthorizationCodeReceived` notification is used to capture and redeem the authorization_code that the v2.0 endpoint returns to your app.

                    Notifications = new OpenIdConnectAuthenticationNotifications
                    {
                        AuthenticationFailed = OnAuthenticationFailed,
                        AuthorizationCodeReceived = OnAuthorizationCodeReceived,
                    }

        });
}
// ...
```

## <a name="use-msal-to-get-access-tokens"></a>Bruge MSAL for at få adgangstokens
I den `AuthorizationCodeReceived` meddelelser, vi vil bruge [OAuth 2.0 sammen med OpenID oprette forbindelse](active-directory-v2-protocols.md#openid-connect-with-oauth-code-flow) til indløse authorization_code for et adgangstoken til tjenesten opgavepanel liste.  MSAL kan gøre denne proces nemt for dig:

- Start med at installere den foreløbige version af MSAL:

```PM> Install-Package Microsoft.Identity.Client -ProjectName TodoList-WebApp -IncludePrerelease```
- Og tilføje en ny `using` sætning til den `App_Start\Startup.Auth.cs` fil til MSAL.
- Tilføj nu en ny metode på `OnAuthorizationCodeReceived` hændelseshandler.  Denne handler anvender MSAL til at få fat på et adgangstoken til opgavepanel listen API og gemmer tokenet i MSAL'S token cache til senere:

```C#
private async Task OnAuthorizationCodeReceived(AuthorizationCodeReceivedNotification notification)
{
        string userObjectId = notification.AuthenticationTicket.Identity.FindFirst("http://schemas.xmlsoap.org/ws/2005/05/identity/claims/nameidentifier").Value;
        string tenantID = notification.AuthenticationTicket.Identity.FindFirst("http://schemas.microsoft.com/identity/claims/tenantid").Value;
        string authority = String.Format(CultureInfo.InvariantCulture, aadInstance, tenantID, string.Empty);
        ClientCredential cred = new ClientCredential(clientId, clientSecret);

        // Here you ask for a token using the web app's clientId as the scope, since the web app and service share the same clientId.
        app = new ConfidentialClientApplication(Startup.clientId, redirectUri, cred, new NaiveSessionCache(userObjectId, notification.OwinContext.Environment["System.Web.HttpContextBase"] as HttpContextBase)) {};
        var authResult = await app.AcquireTokenByAuthorizationCodeAsync(new string[] { clientId }, notification.Code);
}
// ...
```

- MSAL har en extensible token cache, der kan bruges til at gemme tokens i webapps.  Dette eksempel implementerer den `NaiveSessionCache` som bruger HTTP-session lagerplads til cache tokens.

<!-- TODO: Token Cache article -->


## <a name="call-the-web-api"></a>Ringe til World Wide Web API
Nu er det tid at faktisk bruge den access_token, du har hentet i trin 3.  Åbn online `Controllers\TodoListController.cs` -fil, som gør alle CRUD anmodninger til API'EN opgavepanel liste.

- Du kan bruge MSAL igen til at hente access_tokens fra cachen til MSAL.  Først skal du tilføje en `using` -sætningen for MSAL til denne fil.

    `using Microsoft.Identity.Client;`

- I den `Index` handling, Brug MSAL `AcquireTokenSilentAsync` metode til at få en access_token, der kan bruges til at læse data fra tjenesten opgaveliste:

```C#
// ...
string userObjectID = ClaimsPrincipal.Current.FindFirst("http://schemas.xmlsoap.org/ws/2005/05/identity/claims/nameidentifier").Value;
string tenantID = ClaimsPrincipal.Current.FindFirst("http://schemas.microsoft.com/identity/claims/tenantid").Value;
string authority = String.Format(CultureInfo.InvariantCulture, Startup.aadInstance, tenantID, string.Empty);
ClientCredential credential = new ClientCredential(Startup.clientId, Startup.clientSecret);

// Here you ask for a token using the web app's clientId as the scope, since the web app and service share the same clientId.
app = new ConfidentialClientApplication(Startup.clientId, redirectUri, credential, new NaiveSessionCache(userObjectID, this.HttpContext)){};
result = await app.AcquireTokenSilentAsync(new string[] { Startup.clientId });
// ...
```

- Eksemplet føjer derefter det resulterende token til HTTP GET-anmodning som den `Authorization` hovedet, som bruger tjenesten opgaveliste til at godkende anmodningen.
- Hvis tjenesten opgaveliste returnerer en `401 Unauthorized` svarmeddelelse ved access_tokens i MSAL er blevet ugyldige eller anden grund.  I dette tilfælde skal du slippe en hvilken som helst access_tokens fra cachen til MSAL og vise brugeren en meddelelse, som de kan være nødvendigt at logge på igen, som genstartes token acquisition strømmen.

```C#
// ...
// If the call failed with access denied, then drop the current access token from the cache,
// and show the user an error indicating they might need to sign-in again.
if (response.StatusCode == System.Net.HttpStatusCode.Unauthorized)
{
        app.AppTokenCache.Clear(Startup.clientId);

        return new RedirectResult("/Error?message=Error: " + response.ReasonPhrase + " You might need to sign in again.");
}
// ...
```

- På samme måde, hvis MSAL er i stand til at returnere en access_token for en eller anden grund, skal du bede brugeren om at logge på igen.  Dette er så enkelt som fangst et `MSALException`:

```C#
// ...
catch (MsalException ee)
{
        // If MSAL could not get a token silently, show the user an error indicating they might need to sign in again.
        return new RedirectResult("/Error?message=An Error Occurred Reading To Do List: " + ee.Message + " You might need to log out and log back in.");
}
// ...
```

- På præcist samme `AcquireTokenSilentAsync` opkaldet er implementd i den `Create` og `Delete` handlinger.  Du kan bruge denne MSAL metode til at få access_tokens, når du har brug for dem i din app i webapps.  MSAL sig tager af indhente, cachelagring og opdatere tokens for dig.

Til sidst skal oprette og køre din app!  Logge på med en Microsoft-Account eller en Azure AD-konto, og se, hvordan brugerens identitet afspejles i den øverste navigationslinje.  Tilføje og slette alle elementer fra brugerens opgaveliste til at se OAuth 2.0 sikret API-kald i aktion.  Du har nu et online & web API begge sikret ved hjælp af branche standard protokoller, der kan godkende brugere med begge deres personlige og arbejds-eller skolekonto konti.

Til reference gennemført prøveoverførsel (uden din konfigurationsværdier) [er angivet her](https://github.com/AzureADQuickStarts/AppModelv2-WebApp-WebAPI-OpenIdConnect-DotNet/archive/complete.zip).  

## <a name="next-steps"></a>Næste trin

Yderligere ressourcer, se:
- [Guiden v2.0 udvikler >>](active-directory-appmodel-v2-overview.md)
- [StackOverflow "azure-active directory" mærke >>](http://stackoverflow.com/questions/tagged/azure-active-directory)

## <a name="get-security-updates-for-our-products"></a>Hente sikkerhedsopdateringer til vores produkter

Vi anbefaler, at finde meddelelser om når sikkerhedshændelser forekommer, ved at besøge [denne side](https://technet.microsoft.com/security/dd252948) og abonnere på vejledende sikkerhedsadvarsler.
