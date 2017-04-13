<properties
    pageTitle="Azure AD-B2C | Microsoft Azure"
    description="Sådan oprettes en .NET Web API ved hjælp af Azure Active Directory B2C, sikret ved hjælp af OAuth 2.0 access tokens til godkendelse."
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
    ms.topic="hero-article"
    ms.date="07/22/2016"
    ms.author="dastrock"/>

# <a name="azure-active-directory-b2c-build-a-net-web-api"></a>Azure Active Directory-B2C: Oprette en .NET web API

<!-- TODO [AZURE.INCLUDE [active-directory-b2c-devquickstarts-web-switcher](../../includes/active-directory-b2c-devquickstarts-web-switcher.md)]-->

Du kan sikre en web API ved hjælp af OAuth 2.0 access tokens med Azure Active Directory (Azure AD) B2C. Disse tokens Tillad, at din klient-apps, der bruger Azure AD B2C til at godkende API. I denne artikel beskrives, hvordan du opretter et .NET Model-View-Controller (MVC) "opgaveliste" API, der gør det muligt for brugerne at CRUD opgaver. World Wide web API er beskyttet med Azure AD B2C og tillader kun godkendte brugere for at administrere deres opgaveliste.

## <a name="create-an-azure-ad-b2c-directory"></a>Oprette en mappe til Azure AD B2C

Før du kan bruge Azure AD B2C, skal du oprette en mappe eller lejer. En mappe er en beholder for alle dine brugere, apps, grupper og meget mere. Hvis du ikke allerede har et, [oprette en mappe med B2C](active-directory-b2c-get-started.md) inden du kan fortsætte i denne vejledning.

## <a name="create-an-application"></a>Oprette et program

Derefter skal du oprette en app i B2C-biblioteket. Dette giver Azure AD-oplysninger, som det skal sikkert kommunikere med din app. Hvis du vil oprette en app, skal du følge [disse instruktioner](active-directory-b2c-app-registration.md). Sørg for at:

- Medtage en **WebApp** eller **web API** i programmet.
- Brug den **omdirigere-id'et uniform resource** `https://localhost:44316/` til WebApp. Dette er standardplaceringen af web app-klienten til denne eksempel-kode.
- Kopiere **program-ID** , der er tildelt din app. Du skal bruge den senere.

 [AZURE.INCLUDE [active-directory-b2c-devquickstarts-v2-apps](../../includes/active-directory-b2c-devquickstarts-v2-apps.md)]

## <a name="create-your-policies"></a>Oprette din politikker

I Azure AD B2C defineres hver brugeroplevelsen af en [politik](active-directory-b2c-reference-policies.md). Klienten i dette eksempel-kode indeholder tre identitet oplevelser: tilmelde dig, log på, og Rediger profil. Du skal oprette en politik for hver type, som beskrevet i [politik referenceartikel](active-directory-b2c-reference-policies.md#how-to-create-a-sign-up-policy). Når du opretter din tre politikker, skal du:

- Vælg enten **Bruger-ID tilmelding** eller **mail om tilmelding til en** i bladet identitet udbydere.
- Vælg **vist navn** og andre tilmelding attributter i din tilmelding til en politik.
- Vælg **visningsnavn** og et **Objekt-ID** krav som programmet krav om hver politik. Du kan vælge samt andre krav.
- Kopiere og indsætte **navn** for hver politik, du har oprettet. Du skal bruge disse politik navne senere.

[AZURE.INCLUDE [active-directory-b2c-devquickstarts-policy](../../includes/active-directory-b2c-devquickstarts-policy.md)]

Når du har oprettet de tre politikker, er du klar til at opbygge din app.

## <a name="download-the-code"></a>Hente koden

Koden for dette selvstudium [vedligeholdes GitHub](https://github.com/AzureADQuickStarts/B2C-WebAPI-DotNet). For at oprette eksemplet, mens du skriver, kan du [hente et skelet projekt som en .zip-fil](https://github.com/AzureADQuickStarts/B2C-WebAPI-DotNet/archive/skeleton.zip). Du kan også klone skelet:

```
git clone --branch skeleton https://github.com/AzureADQuickStarts/B2C-WebAPI-DotNet.git
```

Den færdige app findes også [som en .zip-fil](https://github.com/AzureADQuickStarts/B2C-WebAPI-DotNet/archive/complete.zip) eller på den `complete` gren i den samme lager.

Når du har hentet eksempelkoden, skal du åbne filen Visual Studio .sln at komme i gang. Løsningsfilen indeholder to projekter: `TaskWebApp` og `TaskService`. `TaskWebApp`er en MVC webprogram, som brugeren skal arbejde sammen med. `TaskService`er den app back end-web API, der gemmer hver brugers opgaveliste.

## <a name="configure-the-task-web-app"></a>Konfigurere opgave WebApp

Når en bruger skal arbejde sammen med `TaskWebApp`, klienten sender anmodninger til Azure AD og får tokens, som kan bruges til at ringe tilbage på `TaskService` web API. Hvis du vil logge på brugeren, og få tokens, skal du angive `TaskWebApp` med nogle oplysninger om din app. I den `TaskWebApp` project, åbne den `web.config` filen er i roden af projektet og erstatte værdierne i den `<appSettings>` sektion.  Du kan lade den `AadInstance`, `RedirectUri`, og `TaskServiceUrl` værdier som-er.

```
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
    <add key="api:TaskServiceUrl" value="https://localhost:44332/" />
  </appSettings>
```

I denne artikel omhandler ikke dokumentkomponent på `TaskWebApp` klient.  For at lære at opbygge en online ved hjælp af Azure AD B2C, skal du se [vores .NET web app-selvstudium](active-directory-b2c-devquickstarts-web-dotnet.md).

## <a name="secure-the-api"></a>Secure API

Når du har en klient, der kalder API på vegne af brugere, du kan sikre `TaskService` ved hjælp af OAuth 2.0 bæreren tokens. Din API kan acceptere og validere tokens ved hjælp af Microsofts åbne Web-grænseflade for .NET (OWIN)-bibliotek.

### <a name="install-owin"></a>Installere OWIN
Starte med at installere OWIN OAuth godkendelse pipeline:

```
PM> Install-Package Microsoft.Owin.Security.OAuth -ProjectName TaskService
PM> Install-Package Microsoft.Owin.Security.Jwt -ProjectName TaskService
PM> Install-Package Microsoft.Owin.Host.SystemWeb -ProjectName TaskService
```

### <a name="enter-your-b2c-details"></a>Angiv din B2C detaljer
Åbn den `web.config` fil i roden af den `TaskService` project og erstatte værdier i den `<appSettings>` sektion. Disse værdier der, bruges i hele biblioteket API og OWIN.  Du kan lade den `AadInstance` værdi ikke.

```
  <appSettings>
    <add key="webpages:Version" value="3.0.0.0" />
    <add key="webpages:Enabled" value="false" />
    <add key="ClientValidationEnabled" value="true" />
    <add key="UnobtrusiveJavaScriptEnabled" value="true" />
    <add key="ida:AadInstance" value="https://login.microsoftonline.com/{0}/v2.0/.well-known/openid-configuration?p={1}" />
    <add key="ida:Tenant" value="fabrikamb2c.onmicrosoft.com" />
    <add key="ida:ClientId" value="90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6" />
    <add key="ida:SignUpPolicyId" value="b2c_1_sign_up" />
    <add key="ida:SignInPolicyId" value="b2c_1_sign_in" />
    <add key="ida:UserProfilePolicyId" value="b2c_1_edit_profile" />
  </appSettings>
```

### <a name="add-an-owin-startup-class"></a>Tilføje en OWIN Start klasse
Tilføje en OWIN Start klasse til den `TaskService` projekt, der kaldes `Startup.cs`.  Højreklik på projektet, Vælg **Tilføj** og **Nyt element**, og søg derefter efter OWIN.


```C#
// Startup.cs

// Change the class declaration to "public partial class Startup" - we’ve already implemented part of this class for you in another file.
public partial class Startup
{
    // The OWIN middleware will invoke this method when the app starts
    public void Configuration(IAppBuilder app)
    {
        ConfigureAuth(app);
    }
}
```

### <a name="configure-oauth-20-authentication"></a>Konfigurere godkendelse af OAuth 2.0
Åbn filen `App_Start\Startup.Auth.cs`, og implementere den `ConfigureAuth(...)` metode:

```C#
// App_Start\Startup.Auth.cs

public partial class Startup
{
    // These values are pulled from web.config
    public static string aadInstance = ConfigurationManager.AppSettings["ida:AadInstance"];
    public static string tenant = ConfigurationManager.AppSettings["ida:Tenant"];
    public static string clientId = ConfigurationManager.AppSettings["ida:ClientId"];
    public static string signUpPolicy = ConfigurationManager.AppSettings["ida:SignUpPolicyId"];
    public static string signInPolicy = ConfigurationManager.AppSettings["ida:SignInPolicyId"];
    public static string editProfilePolicy = ConfigurationManager.AppSettings["ida:UserProfilePolicyId"];

    public void ConfigureAuth(IAppBuilder app)
    {   
        app.UseOAuthBearerAuthentication(CreateBearerOptionsFromPolicy(signUpPolicy));
        app.UseOAuthBearerAuthentication(CreateBearerOptionsFromPolicy(signInPolicy));
        app.UseOAuthBearerAuthentication(CreateBearerOptionsFromPolicy(editProfilePolicy));
    }

    public OAuthBearerAuthenticationOptions CreateBearerOptionsFromPolicy(string policy)
    {
        TokenValidationParameters tvps = new TokenValidationParameters
        {
            // This is where you specify that your API only accepts tokens from its own clients
            ValidAudience = clientId,
            AuthenticationType = policy,
        };

        return new OAuthBearerAuthenticationOptions
        {
            // This SecurityTokenProvider fetches the Azure AD B2C metadata & signing keys from the OpenIDConnect metadata endpoint
            AccessTokenFormat = new JwtFormat(tvps, new OpenIdConnectCachingSecurityTokenProvider(String.Format(aadInstance, tenant, policy))),
        };
    }
}
```

### <a name="secure-the-task-controller"></a>Secure opgave controller
Når appen er konfigureret til at bruge OAuth 2.0 godkendelse, du kan sikre din web API ved at tilføje en `[Authorize]` mærke for at den opgave controller. Dette er den controller, hvor alle opgavepanel listen strengmanipulation finder sted, så du skal sikre hele controller på niveauet for klasse. Du kan også tilføje den `[Authorize]` mærke for at individuelle handlinger for flere detaljerede kontrolelement.

```C#
// Controllers\TasksController.cs

[Authorize]
public class TasksController : ApiController
{
    ...
}
```

### <a name="get-user-information-from-the-token"></a>Få brugeroplysninger fra tokenet
`TasksController`gemmer opgaver i en database, hvor hver enkelt opgave har en tilknyttet bruger, der "ejer" opgaven. Ejeren er identificeret med brugerens **objekt-ID**. (Dette er grunden til, at du har brug at tilføje objekt-ID'ET som et program krav i alle dine politikker.)

```C#
// Controllers\TasksController.cs

public IEnumerable<Models.Task> Get()
{
    string owner = ClaimsPrincipal.Current.FindFirst("http://schemas.microsoft.com/identity/claims/objectidentifier").Value;
    IEnumerable<Models.Task> userTasks = db.Tasks.Where(t => t.owner == owner);
    return userTasks;
}
```

## <a name="run-the-sample-app"></a>Køre appen eksempel

Til sidst skal oprette og køre begge `TaskWebApp` og `TaskService`. Tilmelde dig til appen ved hjælp af en mailadresse eller brugernavn. Oprette nogle opgaver på brugerens opgaveliste, og Bemærk, hvordan de er bevaret i API, selv efter at du stop og genstart klienten.

## <a name="edit-your-policies"></a>Redigere din politikker

Når du har beskyttet en API ved hjælp af Azure AD B2C, kan du eksperimentere med politikker for din app og få vist effekterne (eller opnår deraf) på API. Du kan manipulere programmet krav i politikkerne og ændre de brugeroplysninger, der er tilgængelige i web API. En hvilken som helst krav, du tilføjer vil være tilgængelige for din .NET MVC web API i den `ClaimsPrincipal` objekt, som beskrevet tidligere i denne artikel.

<!--

## Next steps

You can now move onto more advanced B2C topics. You may try:

[Call a web API from a web app]()

[Customize the UX of your B2C app]()

-->
