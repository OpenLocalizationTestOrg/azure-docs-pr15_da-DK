<properties
    pageTitle="Azure Active Directory B2C | Microsoft Azure"
    description="Sådan oprettes et webprogram, der kalder en web API ved hjælp af Azure Active Directory B2C."
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

# <a name="azure-ad-b2c-call-a-web-api-from-a-net-web-app"></a>Azure AD-B2C: Ringe til en web API fra en .NET WebApp

Ved hjælp af Azure Active Directory (Azure AD) B2C, kan du tilføje effektive selvbetjening identitet administrationsfunktioner til din online og web API'er i et par korte trin. Denne artikel omhandler, hvordan du opretter en .NET Model-View-Controller (MVC) "opgaveliste" WebApp, der kalder en web API ved hjælp af bæreren tokens

I denne artikel omhandler ikke, hvordan du kan implementere logon, tilmelding og profil styring med Azure AD B2C. Det fokuserer på opkald web API'er, når brugeren allerede er godkendt. Hvis du ikke allerede har gjort det, skal du starte med den [.NET web app Introduktion – selvstudium](active-directory-b2c-devquickstarts-web-dotnet.md) at lære de grundlæggende regler for Azure AD B2C.

## <a name="get-an-azure-ad-b2c-directory"></a>Få et Azure AD B2C directory

Før du kan bruge Azure AD B2C, skal du oprette en mappe eller lejer.  En mappe er en objektbeholder til alle dine brugere, apps, grupper og mere.  Hvis du ikke allerede har et, [oprette en mappe med B2C](active-directory-b2c-get-started.md) inden du kan fortsætte i denne vejledning.

## <a name="create-an-application"></a>Oprette et program

Derefter skal du oprette en app i B2C-biblioteket. Dette giver Azure AD-oplysninger, som det skal sikkert kommunikere med din app. I dette tilfælde repræsenteres både WebApp og web API af et enkelt **Program-ID**, fordi de indeholder én logiske app. Hvis du vil oprette en app, skal du følge [disse instruktioner](active-directory-b2c-app-registration.md). Sørg for at:

- Medtage en **web app/web API** i programmet.
- Angiv `https://localhost:44316/` som en **URL-adresse til svar**. Det er standard URL-adressen for denne eksempel-kode.
- Kopiere **Program-ID** , der er tildelt din app. Du skal også dette senere.

[AZURE.INCLUDE [active-directory-b2c-devquickstarts-v2-apps](../../includes/active-directory-b2c-devquickstarts-v2-apps.md)]

## <a name="create-your-policies"></a>Oprette din politikker

I Azure AD B2C defineres hver brugeroplevelsen af en [politik](active-directory-b2c-reference-policies.md). Denne WebApp indeholder tre identitet oplevelser: tilmelde dig, log på, og Rediger profil. Du skal oprette én politik af hver type, som beskrevet i [politik referenceartikel](active-directory-b2c-reference-policies.md#how-to-create-a-sign-up-policy). Når du opretter de politikker, der er tre, skal du sørge for at:

- Vælg det **viste navn** og andre tilmelding attributter i din tilmelding til en politik.
- Vælg de **viste navn** og **Objekt-ID** programmet krav i alle politikker. Du kan vælge samt andre krav.
- Kopiere og indsætte **navn** for hver politik, du har oprettet. Det skal have præfikset `b2c_1_`. Du skal have politik navne senere.

[AZURE.INCLUDE [active-directory-b2c-devquickstarts-policy](../../includes/active-directory-b2c-devquickstarts-policy.md)]

Når du har oprettet din tre politikker, er du klar til at opbygge din app.

Bemærk, at denne artikel ikke omhandler hvordan du bruger de politikker, du lige har oprettet. For at få mere for at vide om, hvordan fungerer politikker i Azure AD B2C, kan du starte med den [.NET web app Introduktion – selvstudium](active-directory-b2c-devquickstarts-web-dotnet.md).

## <a name="download-the-code"></a>Hente koden

Koden for dette selvstudium [vedligeholdes GitHub](https://github.com/AzureADQuickStarts/B2C-WebApp-WebAPI-OpenIDConnect-DotNet). For at oprette eksemplet, mens du skriver, kan du [hente det skelet projekt som en .zip-fil](https://github.com/AzureADQuickStarts/B2C-WebApp-WebAPI-OpenIDConnect-DotNet/archive/skeleton.zip). Du kan også klone skelet:

```
git clone --branch skeleton https://github.com/AzureADQuickStarts/B2C-WebApp-WebAPI-OpenIDConnect-DotNet.git
```

Den færdige app findes også [som en .zip-fil](https://github.com/AzureADQuickStarts/B2C-WebApp-WebAPI-OpenIDConnect-DotNet/archive/complete.zip) eller på den `complete` gren i den samme lager.

Når du har hentet eksempelkoden, skal du åbne filen Visual Studio .sln at komme i gang.

## <a name="configure-the-task-web-app"></a>Konfigurere opgave WebApp

At få `TaskWebApp` for at kommunikere med Azure AD B2C, skal du angive et par almindelige parametre. I den `TaskWebApp` project, åbne den `web.config` filen er i roden af projektet og erstatte værdierne i den `<appSettings>` sektion. Du kan lade den `AadInstance`, `RedirectUri`, og `TaskServiceUrl` værdier, som de er.

```
  <appSettings>
    
    ...
    
    <add key="ida:ClientId" value="90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6" />
    <add key="ida:AadInstance" value="https://login.microsoftonline.com/{0}/v2.0/.well-known/openid-configuration?p={1}" />
    <add key="ida:RedirectUri" value="https://localhost:44316/" />
    <add key="ida:SignUpPolicyId" value="b2c_1_sign_up" />
    <add key="ida:SignInPolicyId" value="b2c_1_sign_in" />
    <add key="ida:UserProfilePolicyId" value="b2c_1_edit_profile" />
    <add key="api:TaskServiceUrl" value="https://aadb2cplayground.azurewebsites.net" />
  </appSettings>
```

[AZURE.INCLUDE [active-directory-b2c-devquickstarts-tenant-name](../../includes/active-directory-b2c-devquickstarts-tenant-name.md)]  <appSettings>
    <add key="webpages:Version" value="3.0.0.0" />
    <add key="webpages:Enabled" value="false" />
    <add key="ClientValidationEnabled" value="true" />
    <add key="UnobtrusiveJavaScriptEnabled" value="true" />
    <add key="ida:Tenant" value="fabrikamb2c.onmicrosoft.com" />
    <add key="ida:ClientId" value="90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6" />
    <add key="ida:ClientSecret" value="E:i~5GHYRF$Y7BcM" />
    <add key="ida:AadInstance" value="https://login.microsoftonline.com/{0}/v2.0/.well-known/openid-configuration?p={1}" />
    <add key="ida:RedirectUri" value="https://localhost:44316/" />
    <add key="ida:SignUpPolicyId" value="b2c_1_sign_up" />
    <add key="ida:SignInPolicyId" value="b2c_1_sign_in" />
    <add key="ida:UserProfilePolicyId" value="b2c_1_edit_profile" />
    <add key="api:TaskServiceUrl" value="https://aadb2cplayground.azurewebsites.net" />
  </appSettings>

## <a name="get-access-tokens-and-call-the-task-api"></a>Få access tokens, og kald opgaven, API

Dette afsnit vil diskutere Sådan bruger du har modtaget under logge på med Azure AD B2C tokenet for at få adgang til en web API, der er også sikret med Azure AD B2C.

I denne artikel omhandler ikke oplysninger om hvordan du sikrer API. Se den [web API Introduktion artikel](active-directory-b2c-devquickstarts-api-dotnet.md)for at lære, hvordan en web API godkender af anmodninger om sikker ved hjælp af Azure AD B2C.

### <a name="save-the-sign-in-token"></a>Gemme logge i token

Først skal godkende brugeren (ved hjælp af en af dine politikker) og modtage et logon-token fra Azure AD B2C.  Hvis du er i tvivl om, hvordan du kan udføre politikker, gå tilbage og prøve den [.NET web app Introduktion – selvstudium](active-directory-b2c-devquickstarts-web-dotnet.md) til at lære de grundlæggende regler for Azure AD B2C.

Åbn filen `App_Start\Startup.Auth.cs`.  Der er en vigtig ændring, skal du foretage de `OpenIdConnectAuthenticationOptions` -skal du angive `SaveSignInToken = true`.

```C#
// App_Start\Startup.Auth.cs

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
        AuthenticationFailed = OnAuthenticationFailed,
    },
    Scope = "openid",
    ResponseType = "id_token",

    TokenValidationParameters = new TokenValidationParameters
    {
        NameClaimType = "name",
        
        // Add this line to reserve the sign in token for later use
        SaveSigninToken = true,
    },
};
```

### <a name="get-a-token-in-the-controllers"></a>Få et token i enheder

Den `TasksController` er ansvarlig for at kommunikere med web API, sende HTTP-anmodninger til API til at læse, oprette og slette opgaver.  Becuase API er sikret med Azure AD B2C, skal du først hente det token, du har gemt i ovenstående trin.

```C#
// Controllers\TasksController.cs

public async Task<ActionResult> Index()
{
    try { 

        var bootstrapContext = ClaimsPrincipal.Current.Identities.First().BootstrapContext as System.IdentityModel.Tokens.BootstrapContext;
        
    ...
}
```

Den `BootstrapContext` indeholder logonsiden token, som du har købt ved at udføre én af dine B2C politikker.

### <a name="read-tasks-from-the-web-api"></a>Læse opgaver fra World Wide web API

Når du har et token, du kan vedhæfte den til HTTP `GET` anmode om i den `Authorization` sidehoved til at ringe til sikker `TaskService`:

```C#
// Controllers\TasksController.cs

public async Task<ActionResult> Index()
{
    try { 

        ...

        HttpClient client = new HttpClient();
        HttpRequestMessage request = new HttpRequestMessage(HttpMethod.Get, serviceUrl + "/api/tasks");

        // Add the token acquired from ADAL to the request headers
        request.Headers.Authorization = new AuthenticationHeaderValue("Bearer", bootstrapContext.Token);
        HttpResponseMessage response = await client.SendAsync(request);

        if (response.IsSuccessStatusCode)
        {
            String responseString = await response.Content.ReadAsStringAsync();
            JArray tasks = JArray.Parse(responseString);
            ViewBag.Tasks = tasks;
            return View();
        }
        else
        {
            // If the call failed with access denied, show the user an error indicating they might need to sign-in again.
            if (response.StatusCode == System.Net.HttpStatusCode.Unauthorized)
            {
                return new RedirectResult("/Error?message=Error: " + response.ReasonPhrase + " You might need to sign in again.");
            }
        }

        return new RedirectResult("/Error?message=An Error Occurred Reading To Do List: " + response.StatusCode);
    }
    catch (Exception ex)
    {
        return new RedirectResult("/Error?message=An Error Occurred Reading To Do List: " + ex.Message);
    }
}

```

### <a name="create-and-delete-tasks-on-the-web-api"></a>Oprette og slette opgaver på World Wide web API

Følg den samme mønster, når du sender `POST` og `DELETE` anmodninger på World Wide web API, ved hjælp af den `BootstrapContext` til at hente logonsiden token. Vi har implementeret handlingen Opret for dig. Du kan prøve at afslutte slettehandlingen i `TasksController.cs`.

## <a name="run-the-sample-app"></a>Køre appen eksempel

Til sidst skal oprette og køre appen. Tilmeld dig og logge på og oprette opgaver for den bruger, der er logget på. Log af og log på som en anden bruger. Oprette opgaver for den pågældende bruger. Bemærk, hvordan opgaverne, der er gemt brugerniveau på API, fordi API henter brugerens identitet fra tokenet den modtager.

Til reference gennemført prøveoverførsel [leveres som en .zip-fil](https://github.com/AzureADQuickStarts/B2C-WebApp-WebAPI-OpenIDConnect-DotNet/archive/complete.zip). Du kan også klone fra GitHub:

```git clone --branch complete https://github.com/AzureADQuickStarts/B2C-WebApp-WebAPI-OpenIDConnect-DotNet.git```

<!--

## Next steps

You can now move on to more advanced B2C topics. You might try:

[Call a web API from a web app]()

[Customize the UX for a B2C app]()

-->
