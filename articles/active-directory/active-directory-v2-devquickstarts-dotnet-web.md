<properties
    pageTitle="Azure AD v2.0 .NET online | Microsoft Azure"
    description="Sådan oprettes en .NET MVC Web App, der logger på med begge personlig Microsoft-Account brugere og arbejds- eller skolekonto konti."
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

# <a name="add-sign-in-to-an-net-mvc-web-app"></a>Føje-logon til en .NET MVC WebApp

Med v2.0 slutpunktet, kan du hurtigt tilføje godkendelse til din online med understøttelse af begge personlige Microsoft-konti og arbejds- eller skolekonto konti.  I ASP.NET webapps, kan du udføre dette med Microsofts OWIN programmer er inkluderet i .NET Framework 4.5.

> [AZURE.NOTE]
    Ikke alle Azure Active Directory scenarier og funktioner, der understøttes af v2.0 slutpunkt.  For at bestemme, hvis du skal bruge v2.0 slutpunkt skal du læse om [v2.0 begrænsninger](active-directory-v2-limitations.md).

 Her skal vi oprette en WebApp, der bruger OWIN til at logge brugeren på, vises nogle oplysninger om brugeren, og brugeren af app'en.
 
 ## <a name="download"></a>Download
Koden for dette selvstudium vedligeholdes [GitHub](https://github.com/AzureADQuickStarts/AppModelv2-WebApp-OpenIdConnect-DotNet).  Hvis du vil følge med, kan du [hente den app skelet som en .zip](https://github.com/AzureADQuickStarts/AppModelv2-WebApp-OpenIdConnect-DotNet/archive/skeleton.zip) eller klone skelet:

```git clone --branch skeleton https://github.com/AzureADQuickStarts/AppModelv2-WebApp-OpenIdConnect-DotNet.git```

Den færdige app er angivet i slutningen af dette selvstudium samt.

## <a name="register-an-app"></a>Registrere en app
Oprette en ny app på [apps.dev.microsoft.com](https://apps.dev.microsoft.com/?referrer=https://azure.microsoft.com/documentation/articles&deeplink=/appList), eller Følg disse [detaljeret vejledning](active-directory-v2-app-registration.md).  Sørg for at:

- Kopiér ned på **Program-Id** , tildelt til din app, du skal bruge den snart.
- Tilføje **Web** platform til din app.
- Angiv den korrekte **Omdirigere URI**. Omdiriger uri angiver til Azure AD hvor godkendelse svar skal rettes - standarden for dette selvstudium er `https://localhost:44326/`.

## <a name="install--configure-owin-authentication"></a>Installere og konfigurere OWIN godkendelse
Her skal vi konfigurere OWIN programmer for at bruge OpenID forbinde authentication protocol.  OWIN bruges til at udstede logon og hvor anmodninger, administrere brugerens session og få oplysninger om brugeren, bl.a.

-   For at starte, åbne den `web.config` filen er i roden af projektet, og angiv din app konfiguration af værdier i den `<appSettings>` sektion.
    -   Den `ida:ClientId` er **Program-Id** , der er tildelt til din app i portalen registrering.
    -   Den `ida:RedirectUri` er **Omdirigere Uri** , du har angivet i portalen.

-   Dernæst skal du tilføje OWIN program NuGet pakker til projektet ved hjælp af konsollen Package Manager.

```
PM> Install-Package Microsoft.Owin.Security.OpenIdConnect
PM> Install-Package Microsoft.Owin.Security.Cookies
PM> Install-Package Microsoft.Owin.Host.SystemWeb
```

-   Tilføje en "OWIN Start klasse" til projektet kaldet `Startup.cs` højre Klik på projektet--> **Tilføj** --> **Nyt element** --> Søg efter "OWIN".  OWIN programmer skal aktivere den `Configuration(...)` metode, når din app starter.
-   Ændre erklæringen af klasse til `public partial class Startup` -vi har allerede implementeret en del af kurset for dig i en anden fil.  I den `Configuration(...)` metode, foretage et opkald til ConfigureAuth(...) til at konfigurere godkendelse for din online  

```C#
[assembly: OwinStartup(typeof(Startup))]

namespace TodoList_WebApp
{
    public partial class Startup
    {
        public void Configuration(IAppBuilder app)
        {
            ConfigureAuth(app);
        }
    }
}
```

-   Åbn filen `App_Start\Startup.Auth.cs` og implementere den `ConfigureAuth(...)` metode.  De parametre, du giver i `OpenIdConnectAuthenticationOptions` skal fungere som koordinater for din app til at kommunikere med Azure AD.  Skal du også konfigurere Cookiegodkendelse – OpenID forbinde-program bruger cookies under overfladen.

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
                                     RedirectUri = redirectUri,
                                     Scope = "openid email profile",
                                     ResponseType = "id_token",
                                     PostLogoutRedirectUri = redirectUri,
                                     TokenValidationParameters = new TokenValidationParameters
                                     {
                                             ValidateIssuer = false,
                                     },
                                     Notifications = new OpenIdConnectAuthenticationNotifications
                                     {
                                             AuthenticationFailed = OnAuthenticationFailed,
                                     }
                             });
             }
```

## <a name="send-authentication-requests"></a>Sende anmodninger om godkendelse
Din app er nu konfigureret korrekt til at kommunikere med v2.0 slutpunktet ved hjælp af OpenID forbinde godkendelse-protokollen.  OWIN har taget sig af alle grimme detaljerne i oprette godkendelse meddelelser, validere tokens ud fra Azure AD og vedligeholdelse brugersession.  Alle, der forbliver på er ved at give brugerne en metode til at logge på og logge af.

- Du kan bruge Godkend mærker i dine enheder til at kræve, der bruger logger på, før en bestemt side.  Åbn `Controllers\HomeController.cs`, og Tilføj den `[Authorize]` mærke for at om controller.

```C#
[Authorize]
public ActionResult About()
{
  ...
```

-   Du kan også bruge OWIN til at udstede anmodninger om godkendelse fra direkte i din kode.  Åbn `Controllers\AccountController.cs`.  Udstede OpenID forbinde udfordring og hvor anmodninger henholdsvis i handlingerne SignIn() og SignOut().

```C#
public void SignIn()
{
    // Send an OpenID Connect sign-in request.
    if (!Request.IsAuthenticated)
    {
        HttpContext.GetOwinContext().Authentication.Challenge(new AuthenticationProperties { RedirectUri = "/" }, OpenIdConnectAuthenticationDefaults.AuthenticationType);
    }
}

// BUGBUG: Ending a session with the v2.0 endpoint is not yet supported.  Here, we just end the session with the web app.  
public void SignOut()
{
    // Send an OpenID Connect sign-out request.
    HttpContext.GetOwinContext().Authentication.SignOut(CookieAuthenticationDefaults.AuthenticationType);
    Response.Redirect("/");
}
```

-   Åbn nu `Views\Shared\_LoginPartial.cshtml`.  Dette er, hvor du vil vise brugeren links til logon og logge af din app, og udskrive på brugerens navn i en visning.

```HTML
@if (Request.IsAuthenticated)
{
    <text>
        <ul class="nav navbar-nav navbar-right">
            <li class="navbar-text">

                @*The 'preferred_username' claim can be used for showing the user's primary way of identifying themselves.*@

                Hello, @(System.Security.Claims.ClaimsPrincipal.Current.FindFirst("preferred_username").Value)!
            </li>
            <li>
                @Html.ActionLink("Sign out", "SignOut", "Account")
            </li>
        </ul>
    </text>
}
else
{
    <ul class="nav navbar-nav navbar-right">
        <li>@Html.ActionLink("Sign in", "SignIn", "Account", routeValues: null, htmlAttributes: new { id = "loginLink" })</li>
    </ul>
}
```

## <a name="display-user-information"></a>Få vist brugeroplysninger
Når godkendelse af brugere med OpenID forbinde, returnerer v2.0 slutpunktet en id_token til appen, der indeholder [krav](active-directory-v2-tokens.md#id_tokens)eller påstande om brugeren.  Du kan bruge disse krav til at tilpasse din app:

- Åbn den `Controllers\HomeController.cs` fil.  Du kan få adgang til brugerens krav på dine enheder via den `ClaimsPrincipal.Current` sikkerhed primært objekt.

```C#
[Authorize]
public ActionResult About()
{
    ViewBag.Name = ClaimsPrincipal.Current.FindFirst("name").Value;

    // The object ID claim will only be emitted for work or school accounts at this time.
    Claim oid = ClaimsPrincipal.Current.FindFirst("http://schemas.microsoft.com/identity/claims/objectidentifier");
    ViewBag.ObjectId = oid == null ? string.Empty : oid.Value;

    // The 'preferred_username' claim can be used for showing the user's primary way of identifying themselves
    ViewBag.Username = ClaimsPrincipal.Current.FindFirst("preferred_username").Value;

    // The subject or nameidentifier claim can be used to uniquely identify the user
    ViewBag.Subject = ClaimsPrincipal.Current.FindFirst("http://schemas.xmlsoap.org/ws/2005/05/identity/claims/nameidentifier").Value;

    return View();
}
```

## <a name="run"></a>Kør

Til sidst skal oprette og køre din app!   Logge på med en personlig Microsoft-Account eller arbejds-eller skolekonto, og se, hvordan brugerens identitet afspejles i den øverste navigationslinje.  Du har nu en WebApp sikret ved hjælp af branche standard protokoller, der kan godkende brugere med begge deres personlige og arbejds-eller skolekonto konti.

Til reference kan på gennemført prøveoverførsel (uden din konfigurationsværdier), [der er angivet en .zip her](https://github.com/AzureADQuickStarts/AppModelv2-WebApp-OpenIdConnect-DotNet/archive/complete.zip), eller du klone den fra GitHub:

```git clone --branch complete https://github.com/AzureADQuickStarts/AppModelv2-WebApp-OpenIdConnect-DotNet.git```

## <a name="next-steps"></a>Næste trin

Nu kan du flytte til mere avancerede emner.  Vil du måske at prøve:

[Sikre en Web API med den v2.0 slutpunktet >>](active-directory-devquickstarts-webapi-dotnet.md)

Yderligere ressourcer, se:
- [Guiden v2.0 udvikler >>](active-directory-appmodel-v2-overview.md)
- [StackOverflow "azure-active directory" mærke >>](http://stackoverflow.com/questions/tagged/azure-active-directory)

## <a name="get-security-updates-for-our-products"></a>Hente sikkerhedsopdateringer til vores produkter

Vi anbefaler, at finde meddelelser om når sikkerhedshændelser forekommer, ved at besøge [denne side](https://technet.microsoft.com/security/dd252948) og abonnere på vejledende sikkerhedsadvarsler.
