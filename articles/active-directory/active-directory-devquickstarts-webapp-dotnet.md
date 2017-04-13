<properties
    pageTitle="Azure AD .NET Introduktion | Microsoft Azure"
    description="Sådan oprettes en .NET MVC Web App, der integreres med Azure AD til logon."
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

# <a name="aspnet-web-app-sign-in--sign-out-with-azure-ad"></a>ASP.NET Web App Log på og log af med Azure AD

[AZURE.INCLUDE [active-directory-devguide](../../includes/active-directory-devguide.md)]

Azure AD gør det enkle og nemt at udlicitere din online identitet management, give enkelt logon og hvor med kun nogle få linjer af kode.  I Asp.NET webapps, kan du udføre dette med Microsofts implementering af OWIN community-driven program inkluderet i .NET Framework 4.5.  Her skal vi bruge OWIN til:
-   Log brugeren på appen med Azure AD som identitetsudbyder.
-   Få vist nogle oplysninger om brugeren.
-   Log brugeren af app'en.

Før du gør dette, skal du:

1. Registrere et program med Azure AD
2. Konfigurere din app til at bruge OWIN godkendelse pipeline.
3. Brug OWIN til at udstede logon og hvor anmodninger til Azure AD.
4. Udskrive data om brugeren.

At komme i gang skal [hente app skelet](https://github.com/AzureADQuickStarts/WebApp-OpenIdConnect-DotNet/archive/skeleton.zip) eller [hente gennemført prøveoverførsel](https://github.com/AzureADQuickStarts/WebApp-OpenIdConnect-DotNet/archive/complete.zip).  Du skal også en Azure AD-lejer som til at registrere dit program.  Hvis du ikke allerede har et, [se, hvordan du få en](active-directory-howto-tenant.md).

## <a name="1--register-an-application-with-azure-ad"></a>*1. registrere et program med Azure AD*
Hvis du vil aktivere din app til at godkende brugere, skal du først registrere et nyt program i din lejer.

- Log på administrationsportalen Azure.
- Klik på **Active Directory**i til venstre i navigationen.
- Vælg den lejer, hvor du vil registrere programmet.
- Klik på fanen **programmer** , og klik på Tilføj i den nederste skuffe.
- Følg anvisningerne, og oprette et nyt **webprogram og/eller WebAPI**.
    - **Navnet** på programmet, som beskriver dit program for slutbrugere
    -   **Sign-On URL-adresse** er den grundlæggende URL-adressen for din app.  Det skelet standard er `https://localhost:44320/`.
    - **App ID URI** er et entydigt id for dit program.  Konferencen er at bruge `https://<tenant-domain>/<app-name>`, f.eks.`https://contoso.onmicrosoft.com/my-first-aad-app`
- Når du har færdiggjort registrering, Tildel AAD din app et entydigt klient-id.  Du skal bruge denne værdi i næste afsnit, så kopiere det fra fanen Konfigurer.

## <a name="2-set-up-your-app-to-use-the-owin-authentication-pipeline"></a>*2. konfigurere din app til at bruge OWIN godkendelse pipeline*
Her skal vi konfigurere OWIN programmer for at bruge OpenID forbinde authentication protocol.  OWIN bruges til at udstede logon og hvor anmodninger, administrere brugerens session og få oplysninger om brugeren, bl.a.

-   For at starte skal du føje OWIN program NuGet pakker til projektet ved hjælp af konsollen Package Manager.

```
PM> Install-Package Microsoft.Owin.Security.OpenIdConnect
PM> Install-Package Microsoft.Owin.Security.Cookies
PM> Install-Package Microsoft.Owin.Host.SystemWeb
```

-   Føje en OWIN Start klasse til projektet kaldet `Startup.cs` højre Klik på projektet--> **Tilføj** --> **Nyt element** --> Søg efter "OWIN".  OWIN programmer skal aktivere den `Configuration(...)` metode, når din app starter.
-   Ændre erklæringen af klasse til `public partial class Startup` -vi har allerede implementeret en del af kurset for dig i en anden fil.  I den `Configuration(...)` metode, foretage et opkald til ConfgureAuth(...) til at konfigurere godkendelse for din online  

```C#
public partial class Startup
{
    public void Configuration(IAppBuilder app)
    {
        ConfigureAuth(app);
    }
}
```

-   Åbn filen `App_Start\Startup.Auth.cs` og implementere den `ConfigureAuth(...)` metode.  De parametre, du giver i `OpenIDConnectAuthenticationOptions` skal fungere som koordinater for din app til at kommunikere med Azure AD.  Skal du også konfigurere Cookiegodkendelse – OpenID forbinde-program bruger cookies under overfladen.

```C#
public void ConfigureAuth(IAppBuilder app)
{
    app.SetDefaultSignInAsAuthenticationType(CookieAuthenticationDefaults.AuthenticationType);

    app.UseCookieAuthentication(new CookieAuthenticationOptions());

    app.UseOpenIdConnectAuthentication(
        new OpenIdConnectAuthenticationOptions
        {
            ClientId = clientId,
            Authority = authority,
            PostLogoutRedirectUri = postLogoutRedirectUri,
        });
}
```

-   Til sidst skal åbne den `web.config` filen er i roden af projektet, og angiv din konfigurationsværdier i den `<appSettings>` sektion.
    -   Din app `ida:ClientId` er den Guid, du har kopieret fra portalen Azure i trin 1.
    -   Den `ida:Tenant` er navnet på din Azure AD-lejer, f.eks. "contoso.onmicrosoft.com".
    -   Din `ida:PostLogoutRedirectUri` angiver til Azure AD, hvor en bruger skal omdirigeres, når en hvor anmodning er fuldført.

## <a name="3-use-owin-to-issue-sign-in-and-sign-out-requests-to-azure-ad"></a>*3. Brug OWIN til at udstede logon og hvor anmodninger til Azure AD*
Din app er nu konfigureret korrekt til at kommunikere med Azure AD ved hjælp af OpenID forbinde godkendelse-protokollen.  OWIN har taget sig af alle grimme detaljerne i oprette godkendelse meddelelser, validere tokens ud fra Azure AD og vedligeholdelse brugersession.  Alle, der forbliver på er ved at give brugerne en metode til at logge på og logge af.

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
public void SignOut()
{
    // Send an OpenID Connect sign-out request.
    HttpContext.GetOwinContext().Authentication.SignOut(
        OpenIdConnectAuthenticationDefaults.AuthenticationType, CookieAuthenticationDefaults.AuthenticationType);
}
```

-   Åbn nu `Views\Shared\_LoginPartial.cshtml`.  Dette er, hvor du vil vise brugeren links til logon og logge af din app, og udskrive på brugerens navn i en visning.

```HTML
@if (Request.IsAuthenticated)
{
    <text>
        <ul class="nav navbar-nav navbar-right">
            <li class="navbar-text">
                Hello, @User.Identity.Name!
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

## <a name="4--display-user-information"></a>*4. vise brugeroplysninger*
Når godkendelse af brugere med OpenID forbinde, returnerer Azure AD en id_token til det program, der indeholder "krav" eller påstande om brugeren.  Du kan bruge disse krav til at tilpasse din app:

- Åbn den `Controllers\HomeController.cs` fil.  Du kan få adgang til brugerens krav på dine enheder via den `ClaimsPrincipal.Current` sikkerhed primært objekt.

```C#
public ActionResult About()
{
    ViewBag.Name = ClaimsPrincipal.Current.FindFirst(ClaimTypes.Name).Value;
    ViewBag.ObjectId = ClaimsPrincipal.Current.FindFirst("http://schemas.microsoft.com/identity/claims/objectidentifier").Value;
    ViewBag.GivenName = ClaimsPrincipal.Current.FindFirst(ClaimTypes.GivenName).Value;
    ViewBag.Surname = ClaimsPrincipal.Current.FindFirst(ClaimTypes.Surname).Value;
    ViewBag.UPN = ClaimsPrincipal.Current.FindFirst(ClaimTypes.Upn).Value;

    return View();
}
```

Til sidst skal oprette og køre din app!  Hvis du ikke allerede har gjort det, nu er det tid til at oprette en ny bruger i din lejer med en *. onmicrosoft.com-domæne.  Log på med brugeren, og se, hvordan brugerens identitet afspejles i den øverste navigationslinje.  Logge af og logge på igen, som en anden bruger i din lejer.  Hvis du føler dig særligt ambitiøse, Registrer og køre en anden forekomst af dette program (med sin egen clientId), og se se enkelt – Log på i aktion.

Til reference gennemført prøveoverførsel (uden din konfigurationsværdier) [er angivet her](https://github.com/AzureADQuickStarts/WebApp-OpenIdConnect-DotNet/archive/complete.zip).  

Nu kan du flytte til mere avancerede emner.  Vil du måske at prøve:

[Secure Web API med Azure AD >>](active-directory-devquickstarts-webapi-dotnet.md)

[AZURE.INCLUDE [active-directory-devquickstarts-additional-resources](../../includes/active-directory-devquickstarts-additional-resources.md)]
