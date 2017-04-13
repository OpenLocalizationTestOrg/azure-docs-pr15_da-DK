<properties
    pageTitle="Azure Active Directory B2C | Microsoft Azure"
    description="Sådan oprettes et webprogram, der har logon, tilmeldingsprocessen og profil styring ved hjælp af Azure Active Directory B2C."
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

# <a name="azure-ad-b2c-build-a-net-web-app"></a>Azure AD-B2C: Oprette en .NET WebApp

<!-- TODO [AZURE.INCLUDE [active-directory-b2c-devquickstarts-web-switcher](../../includes/active-directory-b2c-devquickstarts-web-switcher.md)]-->

Du kan føje funktioner til administration af effektive selvbetjening identitet ved hjælp af Azure Active Directory (Azure AD) B2C, til din online i et par korte trin. Denne artikel omhandler, hvordan du opretter en .NET Model-View-Controller (MVC) WebApp, der indeholder bruger tilmelding, logon og profil administration. Appen indeholder understøttelse af tilmelding og log på ved hjælp af et brugernavn eller mail og ved hjælp af konti til sociale som Facebook og Google.

## <a name="get-an-azure-ad-b2c-directory"></a>Få et Azure AD B2C directory

Før du kan bruge Azure AD B2C, skal du oprette en mappe eller lejer. En mappe er en beholder for alle dine brugere, apps, grupper og meget mere.  Hvis du ikke allerede har et, [oprette en mappe med B2C](active-directory-b2c-get-started.md) inden du kan fortsætte i denne vejledning.

## <a name="create-an-application"></a>Oprette et program

Derefter skal du oprette en app i B2C-biblioteket. Dette giver Azure AD-oplysninger, som det skal sikkert kommunikere med din app. Hvis du vil oprette en app, skal du følge [disse instruktioner](active-directory-b2c-app-registration.md).  Sørg for at:

- Medtage en **web app/web API** i programmet.
- Angiv `https://localhost:44316/` som en **omdirigere URI**. Det er standard URL-adressen for denne eksempel-kode.
- Kopiere hele vejen ned **Program-ID** , der er tildelt din app.  Du skal bruge den senere.

[AZURE.INCLUDE [active-directory-b2c-devquickstarts-v2-apps](../../includes/active-directory-b2c-devquickstarts-v2-apps.md)]

## <a name="create-your-policies"></a>Oprette din politikker

I Azure AD B2C defineres hver brugeroplevelsen af en [politik](active-directory-b2c-reference-policies.md). Eksempel på denne kode indeholder tre identitet oplevelser: tilmelde dig, log på, og Rediger profil. Du skal oprette én politik af hver type, som beskrevet i [politik referenceartikel](active-directory-b2c-reference-policies.md#how-to-create-a-sign-up-policy).

>[AZURE.NOTE] Azure AD-B2C understøtter også en kombinerede Tilmeld dig eller log i politik, der ikke er fremhævet i dette selvstudium.  Tilmeld dig eller Log på politik vises i [selvstudiet tilsvarende](active-directory-b2c-devquickstarts-web-dotnet-susi.md).

Når du opretter de politikker, der er tre, skal du sørge for at:

- Vælg **Bruger-ID tilmelding** eller **mail om tilmelding til en** i bladet identitet udbydere.
- Vælg det **viste navn** og andre tilmelding attributter i din tilmelding til en politik.
- Vælg **visningsnavnet** kravet som et program krav i alle politikker. Du kan vælge samt andre krav.
- Kopiere og indsætte **navn** for hver politik, du har oprettet. Du skal have politik navne senere.

[AZURE.INCLUDE [active-directory-b2c-devquickstarts-policy](../../includes/active-directory-b2c-devquickstarts-policy.md)]

Når du opretter din tre politikker, er du klar til at opbygge din app.  

## <a name="download-the-code-and-configure-authentication"></a>Hente koden og konfigurere godkendelse

Kode for denne eksempel [vedligeholdes GitHub](https://github.com/AzureADQuickStarts/B2C-WebApp-OpenIdConnect-DotNet). For at oprette eksemplet, mens du skriver, kan du [hente det skelet projekt som en .zip-fil](https://github.com/AzureADQuickStarts/B2C-WebApp-OpenIdConnect-DotNet/archive/skeleton.zip). Du kan også klone skelet:

```
git clone --branch skeleton https://github.com/AzureADQuickStarts/B2C-WebApp-OpenIdConnect-DotNet.git
```

Den færdige eksempel findes også [som en .zip-fil](https://github.com/AzureADQuickStarts/B2C-WebApp-OpenIdConnect-DotNet/archive/complete.zip) eller på den `complete` gren i den samme lager.

Når du har hentet eksempelkoden, skal du åbne filen Visual Studio .sln at komme i gang.

Din app kommunikerer med Azure AD B2C ved at sende meddelelser til godkendelse, som angiver den politik, de vil udføre som en del af HTTP-anmodningen. Du kan bruge Microsofts OWIN programmer til at sende OpenID forbinde anmodninger om godkendelse, udføre politikker, administrere brugersessioner og mere til .NET webprogrammer.

For at starte skal du føje OWIN program NuGet pakker til projektet ved hjælp af Visual Studio pakke Manager-konsollen.

```
PM> Install-Package Microsoft.Owin.Security.OpenIdConnect
PM> Install-Package Microsoft.Owin.Security.Cookies
PM> Install-Package Microsoft.Owin.Host.SystemWeb
```

Derefter skal du åbne den `web.config` filen er i roden af projektet, og angiv din app konfiguration værdierne i den `<appSettings>` sektion, erstatter de eksisterende værdier.

```
<configuration>
  <appSettings>
    <add key="webpages:Version" value="3.0.0.0" />
    <add key="webpages:Enabled" value="false" />
    <add key="ClientValidationEnabled" value="true" />
    <add key="UnobtrusiveJavaScriptEnabled" value="true" />
    <add key="ida:Tenant" value="fabrikamb2c.onmicrosoft.com" />
    <add key="ida:ClientId" value="90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6" />
    <add key="ida:AadInstance" value="https://login.microsoftonline.com/{0}/v2.0/.well-known/openid-configuration?p={1}" />
    <add key="ida:RedirectUri" value="https://localhost:44316/" />
    <add key="ida:SignUpPolicyId" value="b2c_1_sign_up" />
    <add key="ida:SignInPolicyId" value="b2c_1_sign_in" />
    <add key="ida:UserProfilePolicyId" value="b2c_1_edit_profile" />
  </appSettings>
...
```

[AZURE.INCLUDE [active-directory-b2c-tenant-name](../../includes/active-directory-b2c-devquickstarts-tenant-name.md)]

Føj en OWIN Start klasse i projektet, kaldet `Startup.cs`. Højreklik på projektet, Vælg **Tilføj** og **Nyt element**, og Søg efter "OWIN." **Sørg for at ændre erklæringen af klasse til `public partial class Startup` **. Vi har implementeret en del af kurset for dig i en anden fil. OWIN programmer skal aktivere den `Configuration(...)` metode, når din app starter. I denne metode kan du foretage et opkald til `ConfigureAuth(...)`, hvor du konfigurere godkendelse for din app.

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
    public static string SignUpPolicyId = ConfigurationManager.AppSettings["ida:SignUpPolicyId"];
    public static string SignInPolicyId = ConfigurationManager.AppSettings["ida:SignInPolicyId"];
    public static string ProfilePolicyId = ConfigurationManager.AppSettings["ida:UserProfilePolicyId"];

    public void ConfigureAuth(IAppBuilder app)
    {
        app.SetDefaultSignInAsAuthenticationType(CookieAuthenticationDefaults.AuthenticationType);

        app.UseCookieAuthentication(new CookieAuthenticationOptions());

        // Configure OpenID Connect middleware for each policy
        app.UseOpenIdConnectAuthentication(CreateOptionsFromPolicy(SignUpPolicyId));
        app.UseOpenIdConnectAuthentication(CreateOptionsFromPolicy(ProfilePolicyId));
        app.UseOpenIdConnectAuthentication(CreateOptionsFromPolicy(SignInPolicyId));
    }

    // Used for avoiding yellow-screen-of-death
    private Task AuthenticationFailed(AuthenticationFailedNotification<OpenIdConnectMessage, OpenIdConnectAuthenticationOptions> notification)
    {
        notification.HandleResponse();
        if (notification.Exception.Message == "access_denied")
        {
            notification.Response.Redirect("/");
        }
        else
        {
            notification.Response.Redirect("/Home/Error?message=" + notification.Exception.Message);
        }

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
```

## <a name="send-authentication-requests-to-azure-ad"></a>Sende anmodninger om godkendelse til Azure AD
Din app er nu konfigureret korrekt til at kommunikere med Azure AD B2C ved hjælp af OpenID forbinde godkendelse-protokollen.  OWIN har taget sig af alle oplysninger om oprette godkendelse meddelelser, validere tokens ud fra Azure AD og vedligeholdelse brugersession.  Alt, forbliver er at starte hver brugers flow.

Når en bruger vælger **tilmelde**, **logge på**eller **Rediger profil** i WebApp, handlingen knyttet er startet i `Controllers\AccountController.cs`. Du kan bruge indbyggede OWIN metoder til at udløse politikken højre i begge tilfælde:

```C#
// Controllers\AccountController.cs

public void SignIn()
{
    if (!Request.IsAuthenticated)
    {
        // To execute a policy, you simply need to trigger an OWIN challenge.
        // You can indicate which policy to use by specifying the policy id as the AuthenticationType
        HttpContext.GetOwinContext().Authentication.Challenge(
            new AuthenticationProperties () { RedirectUri = "/" }, Startup.SignInPolicyId);
    }
}

public void SignUp()
{
    if (!Request.IsAuthenticated)
    {
        HttpContext.GetOwinContext().Authentication.Challenge(
            new AuthenticationProperties() { RedirectUri = "/" }, Startup.SignUpPolicyId);
    }
}


public void Profile()
{
    if (Request.IsAuthenticated)
    {
        HttpContext.GetOwinContext().Authentication.Challenge(
            new AuthenticationProperties() { RedirectUri = "/" }, Startup.ProfilePolicyId);
    }
}
```

Du kan også bruge en `[Authorize]` mærket i dine enheder, der kræver udførelse af en bestemt politik, hvis brugeren ikke er logget på. Åbn `Controllers\HomeController.cs` og tilføje den `[Authorize]` mærke for at krav controller.  OWIN vælger politikken sidste konfigureret til at udføre, når mærket godkende startes.

```C#
// Controllers\HomeController.cs

// You can use the Authorize decorator to execute a policy if the user is not already signed in the app.
[Authorize]
public ActionResult Claims()
{
  ...
```

Du kan også bruge OWIN til at logge af brugeren fra app. In `Controllers\AccountController.cs`:  

```C#
// Controllers\AccountController.cs

public void SignOut()
{
    // To sign out the user, you should issue an OpenIDConnect sign out request
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

Til reference på gennemført prøveoverførsel (uden din konfigurationsværdier), [der er angivet en .zip-fil](https://github.com/AzureADQuickStarts/B2C-WebApp-OpenIdConnect-DotNet/archive/complete.zip). Du kan også klone fra GitHub:

```
git clone --branch complete https://github.com/AzureADQuickStarts/B2C-WebApp-OpenIdConnect-DotNet.git
```

<!--

## Next steps

You can now move on to more advanced B2C topics. You might try:

[Call a web API from a web app]()

[Customize the UX for a B2C app]()

-->
