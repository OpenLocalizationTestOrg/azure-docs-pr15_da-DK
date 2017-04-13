<properties
    pageTitle="Azure Active Directory B2C | Microsoft Azure"
    description="Sådan oprettes et webprogram, der har tilmelding, log på, og brug af Azure Active Directory B2C til nulstilling af adgangskode."
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

# <a name="azure-ad-b2c-sign-up--sign-in-in-a-aspnet-web-app"></a>Azure AD-B2C: Tilmelding og log på i en ASP.NET Web App

<!-- TODO [AZURE.INCLUDE [active-directory-b2c-devquickstarts-web-switcher](../../includes/active-directory-b2c-devquickstarts-web-switcher.md)]-->

Du kan føje funktioner til administration af effektive selvbetjening identitet ved hjælp af Azure Active Directory (Azure AD) B2C, til din online i et par korte trin. Denne artikel omhandler, hvordan du opretter en ASP.NET WebApp, der indeholder bruger tilmelding, log på, og nulstilling af adgangskode. Appen indeholder understøttelse af tilmelding og log på ved hjælp af et brugernavn eller mail og ved hjælp af konti til sociale som Facebook og Google.

Dette selvstudium, der er forskellig fra [vores andre .NET web-selvstudium](active-directory-b2c-devquickstarts-web-dotnet.md) i, at den bruger en [har tilmeldt dig eller logge på for](active-directory-b2c-reference-policies.md#create-a-sign-up-or-sign-in-policy) at give brugerregistrering og logge på med en enkelt knap i stedet for to (én for tilmelding og én til logon).  I en nutshell, en Tilmeld eller Log på politik giver brugerne mulighed at logge på med en eksisterende konto Hvis de har en, eller Opret en ny, hvis det er deres første gang ved hjælp af appen.

## <a name="get-an-azure-ad-b2c-directory"></a>Få et Azure AD B2C directory

Før du kan bruge Azure AD B2C, skal du oprette en mappe eller lejer. En mappe er en beholder for alle dine brugere, apps, grupper og meget mere.  Hvis du ikke allerede har et, [oprette en mappe med B2C](active-directory-b2c-get-started.md) inden du kan fortsætte i denne vejledning.

## <a name="create-an-application"></a>Oprette et program

Derefter skal du oprette en app i B2C-biblioteket. Dette giver Azure AD-oplysninger, som det skal sikkert kommunikere med din app. Hvis du vil oprette en app, skal du følge [disse instruktioner](active-directory-b2c-app-registration.md).  Sørg for at:

- Medtage en **web app/web API** i programmet.
- Angiv `https://localhost:44316/` som en **omdirigere URI**. Det er standard URL-adressen for denne eksempel-kode.
- Kopiere hele vejen ned **Program-ID** , der er tildelt din app.  Du skal bruge den senere.

[AZURE.INCLUDE [active-directory-b2c-devquickstarts-v2-apps](../../includes/active-directory-b2c-devquickstarts-v2-apps.md)]

## <a name="create-your-policies"></a>Oprette din politikker

I Azure AD B2C defineres hver brugeroplevelsen af en [politik](active-directory-b2c-reference-policies.md). Eksempel på denne kode indeholder to identitet oplevelser: **tilmelding og logge på**og **nulstilling af adgangskode**.  Du skal oprette én politik af hver type, som beskrevet i [politik referenceartikel](active-directory-b2c-reference-policies.md). Når du opretter to politikker, skal du:

- Vælg **Bruger-ID tilmelding** eller **mail om tilmelding til en** i bladet identitet udbydere.
- Vælg det **viste navn** og andre tilmelding attributter i din tilmelding og log på politik.
- Vælg **visningsnavnet** kravet som et program krav i alle politikker. Du kan vælge samt andre krav.
- Kopiere og indsætte **navn** for hver politik, du har oprettet. Du skal have politik navne senere.

[AZURE.INCLUDE [active-directory-b2c-devquickstarts-policy](../../includes/active-directory-b2c-devquickstarts-policy.md)]

Når du opretter din to politikker, er du klar til at opbygge din app.

## <a name="download-the-code-and-configure-authentication"></a>Hente koden og konfigurere godkendelse

Kode for denne eksempel [vedligeholdes GitHub](https://github.com/AzureADQuickStarts/B2C-WebApp-OpenIdConnect-DotNet-SUSI). For at oprette eksemplet, mens du skriver, kan du [hente det skelet projekt som en .zip-fil](https://github.com/AzureADQuickStarts/B2C-WebApp-OpenIdConnect-DotNet-SUSI/archive/skeleton.zip). Du kan også klone skelet:

```
git clone --branch skeleton https://github.com/AzureADQuickStarts/B2C-WebApp-OpenIdConnect-DotNet-SUSI.git
```

Den færdige eksempel findes også [som en .zip-fil](https://github.com/AzureADQuickStarts/B2C-WebApp-OpenIdConnect-DotNet-SUSI/archive/complete.zip) eller på den `complete` gren i den samme lager.

Når du har hentet eksempelkoden, skal du åbne filen Visual Studio .sln at komme i gang.

Din app kommunikerer med Azure AD B2C ved at sende HTTP-godkendelse af anmodninger, som angiver den ønsker at udføre som en del af anmodningen politikken. Du kan bruge Microsofts OWIN bibliotek for .NET webprogrammer til at sende OpenID forbinde anmodninger om godkendelse, udføre politikker, administrere brugersessioner og meget mere.

For at starte skal du føje OWIN program NuGet pakker til projektet ved hjælp af Visual Studio pakke Manager-konsollen.

```
Install-Package Microsoft.Owin.Security.OpenIdConnect
Install-Package Microsoft.Owin.Security.Cookies
Install-Package Microsoft.Owin.Host.SystemWeb
Install-Package System.IdentityModel.Tokens.Jwt
```

Derefter skal du åbne den `web.config` filen er i roden af projektet, og angiv din app konfiguration værdierne i den `<appSettings>` sektion, erstatter værdierne under med dine egne.  Du kan lade den `ida:RedirectUri` og `ida:AadInstance` værdier, som er, være uændret.

```
<configuration>
  <appSettings>

    ...

    <add key="ida:Tenant" value="fabrikamb2c.onmicrosoft.com" />
    <add key="ida:ClientId" value="90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6" />
    <add key="ida:AadInstance" value="https://login.microsoftonline.com/{0}{1}{2}" />
    <add key="ida:RedirectUri" value="https://localhost:44316/" />
    <add key="ida:SusiPolicyId" value="b2c_1_susi" />
    <add key="ida:PasswordResetPolicyId" value="b2c_1_reset" />
  </appSettings>
...
```

[AZURE.INCLUDE [active-directory-b2c-tenant-name](../../includes/active-directory-b2c-devquickstarts-tenant-name.md)]

Føj en OWIN Start klasse i projektet, kaldet `Startup.cs`. Højreklik på projektet, Vælg **Tilføj** og **Nyt element**, og Søg efter "OWIN." Ændre erklæringen af klasse til `public partial class Startup`. Vi har implementeret en del af kurset for dig i en anden fil. OWIN programmer skal aktivere den `Configuration(...)` metode, når din app starter. I denne metode kan du foretage et opkald til `ConfigureAuth(...)`, hvor du konfigurere godkendelse for din app.

```C#
// Startup.cs

public partial class Startup
{
    public void Configuration(IAppBuilder app)
    {
        ConfigureAuth(app);
    }
}
```

Åbn filen `App_Start\Startup.Auth.cs` og implementere den `ConfigureAuth(...)` metode.  De parametre, du giver i `OpenIdConnectAuthenticationOptions` fungere som koordinater for din app til at kommunikere med Azure AD. Du skal også konfigurere Cookiegodkendelse. Den OpenID forbinde program bruger cookies til at vedligeholde sessioner, blandt andet.

```C#
// App_Start\Startup.Auth.cs

public partial class Startup
{
    // App config settings
    private static string clientId = ConfigurationManager.AppSettings["ida:ClientId"];
    private static string aadInstance = ConfigurationManager.AppSettings["ida:AadInstance"];
    private static string tenant = ConfigurationManager.AppSettings["ida:Tenant"];
    private static string redirectUri = ConfigurationManager.AppSettings["ida:RedirectUri"];

    // B2C policy identifiers
    public static string SusiPolicyId = ConfigurationManager.AppSettings["ida:SusiPolicyId"];
    public static string PasswordResetPolicyId = ConfigurationManager.AppSettings["ida:PasswordResetPolicyId"];

    public void ConfigureAuth(IAppBuilder app)
    {
        app.SetDefaultSignInAsAuthenticationType(CookieAuthenticationDefaults.AuthenticationType);

        app.UseCookieAuthentication(new CookieAuthenticationOptions());

        // Configure OpenID Connect middleware for each policy
        app.UseOpenIdConnectAuthentication(CreateOptionsFromPolicy(PasswordResetPolicyId));
        app.UseOpenIdConnectAuthentication(CreateOptionsFromPolicy(SusiPolicyId));

    }

    private Task OnSecurityTokenValidated(SecurityTokenValidatedNotification<OpenIdConnectMessage, OpenIdConnectAuthenticationOptions> notification)
    {
        // If you wanted to keep some local state in the app (like a db of signed up users),
        // you could use this notification to create the user record if it does not already
        // exist.

        return Task.FromResult(0);
    }

    private OpenIdConnectAuthenticationOptions CreateOptionsFromPolicy(string policy)
    {
        return new OpenIdConnectAuthenticationOptions
        {
            // For each policy, give OWIN the policy-specific metadata address, and
            // set the authentication type to the id of the policy
            MetadataAddress = String.Format(aadInstance, tenant, policy),
            AuthenticationType = policy,

            // These are standard OpenID Connect parameters, with values pulled from web.config
            ClientId = clientId,
            RedirectUri = redirectUri,
            PostLogoutRedirectUri = redirectUri,
            Notifications = new OpenIdConnectAuthenticationNotifications
            {
                AuthenticationFailed = AuthenticationFailed,
                SecurityTokenValidated = OnSecurityTokenValidated,
            },
            Scope = "openid",
            ResponseType = "id_token",

            // This piece is optional - it is used for displaying the user's name in the navigation bar.
            TokenValidationParameters = new TokenValidationParameters
            {
                NameClaimType = "name",
            },
        };
    }
}
...
```

## <a name="send-authentication-requests-to-azure-ad"></a>Sende anmodninger om godkendelse til Azure AD
Din app er nu konfigureret korrekt til at kommunikere med Azure AD B2C ved hjælp af OpenID forbinde godkendelse-protokollen.  OWIN har taget sig af alle oplysninger om oprette godkendelse meddelelser, validere tokens ud fra Azure AD og vedligeholdelse brugersession.  Alt, forbliver er at starte hver brugers flow.

Når en bruger vælger **Login** eller **har glemt din adgangskode?** i web app, aktiveres den tilknyttede handling i `Controllers\AccountController.cs`. Du kan bruge indbyggede OWIN metoder til at udløse politikken højre i begge tilfælde:

```C#
// Controllers\AccountController.cs

public void Login()
{
    if (!Request.IsAuthenticated)
    {
        // To execute a policy, you simply need to trigger an OWIN challenge.
        // You can indicate which policy to use by specifying the policy id as the AuthenticationType
        HttpContext.GetOwinContext().Authentication.Challenge(
            new AuthenticationProperties() { RedirectUri = "/" }, Startup.SusiPolicyId);
    }
}

public void ResetPassword()
{
    if (!Request.IsAuthenticated)
    {
        HttpContext.GetOwinContext().Authentication.Challenge(
        new AuthenticationProperties() { RedirectUri = "/" }, Startup.PasswordResetPolicyId);
    }
}
```

Under udførelse af Tilmeld dig eller logge på politikken, har brugeren mulighed for at klikke på en **har glemt din adgangskode?** link.  I så fald sender Azure AD B2C din app en bestemt fejlmeddelelse, der angiver, at den skal udføre en adgangskode nulstille politik.  Du kan hente denne fejl i `Startup.Auth.cs` ved hjælp af den `AuthenticationFailed` meddelelse:

```C#
// Used for avoiding yellow-screen-of-death TODO
private Task AuthenticationFailed(AuthenticationFailedNotification<OpenIdConnectMessage, OpenIdConnectAuthenticationOptions> notification)
{
    notification.HandleResponse();

    if (notification.ProtocolMessage.ErrorDescription != null && notification.ProtocolMessage.ErrorDescription.Contains("AADB2C90118"))
    {
        // If the user clicked the reset password link, redirect to the reset password route
        notification.Response.Redirect("/Account/ResetPassword");
    }
    else if (notification.Exception.Message == "access_denied")
    {
        // If the user canceled the sign in, redirect back to the home page
        notification.Response.Redirect("/");
    }
    else
    {
        notification.Response.Redirect("/Home/Error?message=" + notification.Exception.Message);
    }

    return Task.FromResult(0);
}
```


Ud over eksplicit aktivering af en politik, kan du bruge en `[Authorize]` mærket i dine enheder, der udføres en politik, hvis brugeren ikke er logget på. Åbn `Controllers\HomeController.cs` og tilføje den `[Authorize]` mærke for at krav controller.  OWIN, kan du markere sidst politik er konfigureret hvornår den `[Authorize]` mærke ramme.

```C#
// Controllers\HomeController.cs

// You can use the Authorize decorator to execute a certain policy if the user is not already signed into the app.
[Authorize]
public ActionResult Claims()
{
  ...
```

Du kan også bruge OWIN til at logge af brugeren fra app. In `Controllers\AccountController.cs`:  

```C#
// Controllers\AccountController.cs

public void Logout()
{
    // To sign out the user, you should issue an OpenIDConnect sign out request.
    if (Request.IsAuthenticated)
    {
        IEnumerable<AuthenticationDescription> authTypes = HttpContext.GetOwinContext().Authentication.GetAuthenticationTypes();
        HttpContext.GetOwinContext().Authentication.SignOut(authTypes.Select(t => t.AuthenticationType).ToArray());
        Request.GetOwinContext().Authentication.GetAuthenticationTypes();
    }
}
```

## <a name="display-user-information"></a>Få vist brugeroplysninger
Når du godkende brugere ved hjælp af OpenID forbinde, returnerer Azure AD et ID-token til appen, der indeholder **krav**. Dette er påstande om brugeren. Du kan bruge krav til at tilpasse din app.  

Åbn den `Controllers\HomeController.cs` fil. Du kan få adgang til bruger krav på dine enheder via den `ClaimsPrincipal.Current` sikkerhed primært objekt.

```C#
// Controllers\HomeController.cs

[Authorize]
public ActionResult Claims()
{
    Claim displayName = ClaimsPrincipal.Current.FindFirst(ClaimsPrincipal.Current.Identities.First().NameClaimType);
    ViewBag.DisplayName = displayName != null ? displayName.Value : string.Empty;
    return View();
}
```

Du kan få adgang til ethvert krav, som dit program modtager på samme måde.  En liste over alle de krav, modtager app er tilgængelig for dig på siden **krav** .

## <a name="run-the-sample-app"></a>Køre appen eksempel

Til sidst skal kan du oprette og køre din app. Tilmelde dig til appen ved hjælp af en e-mail-adresse eller bruger navn. Log af og logge på igen som den samme bruger. Rediger brugerens profil. Log af og tilmelde dig som en anden bruger. Bemærk, at oplysningerne vises under fanen **krav** svarer til de oplysninger, du konfigurerede på din politikker.

## <a name="add-social-idps"></a>Tilføj sociale IDPs

På nuværende tidspunkt understøtter appen kun bruger tilmelding og log på ved hjælp af **lokale konti**. Dette er gemt i mappen B2C konti, der bruger et brugernavn og adgangskode. Ved hjælp af Azure AD B2C, kan du tilføje understøttelse af andre **identitetsudbydere** (IDPs) uden at ændre nogen af din kode.

For at føje sociale IDPs til din app, skal du starte ved at følge vejledningerne i følgende artikler. For hver IDP, du vil understøtte, skal du registrere et program i dette system og få en klient-ID.

- [Konfigurere Facebook som en IDP](active-directory-b2c-setup-fb-app.md)
- [Konfigurere Google som en IDP](active-directory-b2c-setup-goog-app.md)
- [Konfigurere Amazon som en IDP](active-directory-b2c-setup-amzn-app.md)
- [Konfigurere LinkedIn som en IDP](active-directory-b2c-setup-li-app.md)

Når du føjer identitetsudbydere til adresselisten B2C, skal du redigere hver af dine tre politikker til at medtage de nye IDPs, som beskrevet i [politik referenceartikel](active-directory-b2c-reference-policies.md). Når du gemmer dine politikker, kan du køre appen igen.  Du bør se de nye IDPs tilføjet som logon og tilmelding indstillinger i hver af din identitet oplever.

Du kan eksperimentere med din politikker og se virkningen på din eksempel-app. Tilføje eller fjerne IDPs, manipulere programmet krav eller ændre tilmelding attributter. Eksperimentere, indtil du kan se, hvordan politikker, anmodninger om godkendelse og OWIN knytter sammen.

Til reference på gennemført prøveoverførsel (uden din konfigurationsværdier), [der er angivet en .zip-fil](https://github.com/AzureADQuickStarts/B2C-WebApp-OpenIdConnect-DotNet-SUSI/archive/complete.zip). Du kan også klone fra GitHub:

```
git clone --branch complete https://github.com/AzureADQuickStarts/B2C-WebApp-OpenIdConnect-DotNet-SUSI.git
```

<!--

## Next steps

You can now move on to more advanced B2C topics. You might try:

[Call a web API from a web app]()

[Customize the UX for a B2C app]()

-->
