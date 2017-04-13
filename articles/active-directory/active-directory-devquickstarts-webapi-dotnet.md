<properties
    pageTitle="Azure AD .NET Introduktion | Microsoft Azure"
    description="Sådan oprettes et .NET MVC Web API, der integreres med Azure AD for godkendelse og autorisation."
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


# <a name="protect-a-web-api-using-bearer-tokens-from-azure-ad"></a>Beskytte en Web API ved hjælp af bæreren tokens ud fra Azure AD

[AZURE.INCLUDE [active-directory-devguide](../../includes/active-directory-devguide.md)]

Hvis du opretter et program, der giver adgang til beskyttede ressourcer, du skal vide, hvordan du kan beskytte disse ressourcer fra uberettiget access.
Azure AD gør det enkle og nemt at beskytte en web API bruger OAuth bæreren 2.0 Access Tokens med kun nogle få kodelinjer.

I Asp.NET webapps, kan du udføre dette med Microsofts implementering af OWIN community-driven program inkluderet i .NET Framework 4.5.  Her vi bruger OWIN til at oprette en "Opgavelisten" web API, som:
-   Angiver, hvilke API er beskyttet.
-   Kontrollerer, at Web API-kald indeholder en gyldig adgang Token.

Før du gør dette, skal du:

1. Registrere et program med Azure AD
2. Konfigurere din app til at bruge OWIN godkendelse pipeline.
3. Konfigurere en klientprogrammet til at ringe til at gøre listen Web API

At komme i gang skal [hente app skelet](https://github.com/AzureADQuickStarts/WebAPI-Bearer-DotNet/archive/skeleton.zip) eller [hente gennemført prøveoverførsel](https://github.com/AzureADQuickStarts/WebAPI-Bearer-DotNet/archive/complete.zip).  Hver er en løsning i Visual Studio-2013.  Du skal også en Azure AD-lejer som til at registrere dit program.  Hvis du ikke allerede har et, [se, hvordan du få en](active-directory-howto-tenant.md).


## <a name="1--register-an-application-with-azure-ad"></a>*1. registrere et program med Azure AD*
Hvis du vil sikre dit program, skal du først at oprette et program i din lejer og Azure AD med et par vigtige dele af oplysninger.

-   Log på [administrationsportalen til af Azure](https://manage.windowsazure.com)
-   Klik på **Active Directory** i til venstre i navigationen
-   Vælg en lejer, til at registrere programmet.
-   Klik på fanen **programmer** , og klik på **Tilføj** i den nederste skuffe.
-   Følg anvisningerne, og oprette et nyt **webprogram og/eller WebAPI**.
    -   **Navnet** på programmet, som beskriver dit program til slutbrugere.  Skriv "For at opgaveliste Service".
    -   **Omdirigere Uri** er en kombination af farveskema og streng, der Azure AD vil bruge til at returnere alle tokens din app, der anmodes om. Angiv `https://localhost:44321/` for denne værdi.
-   Når du har færdiggjort registrering, gå til **Konfigurer** under fanen, og Find **App ID URI** -feltet.  Angiv en lejer-specifikke id for denne værdi, f.eks.`https://contoso.onmicrosoft.com/TodoListService`
- Gemme konfigurationen.  Lad portalen åben – skal du også registrere klientprogrammet kort.

## <a name="2-set-up-your-app-to-use-the-owin-authentication-pipeline"></a>*2. konfigurere din app til at bruge OWIN godkendelse pipeline*

Nu hvor du har registreret et program med Azure AD, skal du konfigurere dit program til at kommunikere med Azure AD for at validere indgående anmodninger og tokens.

-   For at starte skal du åbne løsningen og føje OWIN program NuGet pakker til TodoListService projektet ved hjælp af konsollen Package Manager.

```
PM> Install-Package Microsoft.Owin.Security.ActiveDirectory -ProjectName TodoListService
PM> Install-Package Microsoft.Owin.Host.SystemWeb -ProjectName TodoListService
```

-   Føje en OWIN Start klasse til TodoListService projektet kaldet `Startup.cs`.  Højre Klik på projektet--> **Tilføj** --> **Nyt element** --> Søg efter "OWIN".  OWIN programmer skal aktivere den `Configuration(…)` metode, når din app starter.
-   Ændre erklæringen af klasse til `public partial class Startup` -vi har allerede implementeret en del af kurset for dig i en anden fil.  I den `Configuration(…)` metode, foretage et opkald til ConfgureAuth(...) til at konfigurere godkendelse for din online.

```C#
public partial class Startup
{
    public void Configuration(IAppBuilder app)
    {
        ConfigureAuth(app);
    }
}
```

-   Åbn filen `App_Start\Startup.Auth.cs` og implementere den `ConfigureAuth(…)` metode.  De parametre, du giver i `WindowsAzureActiveDirectoryBearerAuthenticationOptions` skal fungere som koordinater for din app til at kommunikere med Azure AD.

```C#
public void ConfigureAuth(IAppBuilder app)
{
    app.UseWindowsAzureActiveDirectoryBearerAuthentication(
        new WindowsAzureActiveDirectoryBearerAuthenticationOptions
        {
            Audience = ConfigurationManager.AppSettings["ida:Audience"],
            Tenant = ConfigurationManager.AppSettings["ida:Tenant"]
        });
}
```

-   Nu kan du bruge `[Authorize]` attributter for at beskytte dine enheder og handlinger med JWT bæreren godkendelse.  Dekorere den `Controllers\TodoListController.cs` klasse med en godkende mærke.  Dette tvinger brugeren til at logge på, før du få adgang til denne side.

```C#
[Authorize]
public class TodoListController : ApiController
{
```

- Når en autoriserede opkalds korrekt kalder en af de `TodoListController` API'er, handlingen, der muligvis skal have adgang til oplysninger om kalderen.  OWIN giver adgang til krav i bærertoken via den `ClaimsPrincpal` objekt.  
- En almindelig krav til web API'er er at validere "områderne" findes i tokenet – dette sikrer, at brugeren har accepteret at tilladelse til at få adgang til tjenesten opgaveliste liste:

```C#
public IEnumerable<TodoItem> Get()
{
    // user_impersonation is the default permission exposed by applications in AAD
    if (ClaimsPrincipal.Current.FindFirst("http://schemas.microsoft.com/identity/claims/scope").Value != "user_impersonation")
    {
        throw new HttpResponseException(new HttpResponseMessage {
          StatusCode = HttpStatusCode.Unauthorized,
          ReasonPhrase = "The Scope claim does not contain 'user_impersonation' or scope claim not found"
        });
    }
    ...
}
```

-   Til sidst skal åbne den `web.config` filen er i roden af TodoListService projektet, og angiv din konfigurationsværdier i den `<appSettings>` sektion.
  - Den `ida:Tenant` er navnet på din Azure AD-lejer, f.eks. "contoso.onmicrosoft.com".
  - Din `ida:Audience` er App-ID URI på det program, du har angivet i portalen Azure.

## <a name="3--configure-a-client-application--run-the-service"></a>*3. Konfigurer en klientprogrammet og køre tjenesten*
Før du kan se tjenesten opgaveliste i handling, skal du konfigurere den opgaveliste listen klient, så den kan få tokens fra AAD og foretage opkald til tjenesten.

- Gå tilbage til [Azure Management Portal](https://manage.windowsazure.com)
- Oprette et nyt program i din Azure AD-lejer, og vælg **Oprindelige klientprogrammet** i den resulterende prompt.
    -   **Navnet** på programmet, som beskriver dit program for slutbrugere
    -   Angiv `http://TodoListClient/` for værdien **Omdirigere Uri** .
- Når du har færdiggjort registrering, tildele AAD din app et entydigt **Klient-Id**. Du skal bruge denne værdi i de næste trin, så kopiere det fra fanen Konfigurer.
- Find også sektionen "Tilladelser til andre programmer" **Konfigurer** under fanen. Klik på "Tilføj programmet". Vælg "Alle Apps" i rullemenuen "Vis", og klikke på den øverste markering. Find og klik på din til at gøre listen tjeneste, og klikke på den nederste markering for at tilføje programmet. Vælg "Access til at gøre listen Service" på rullelisten "Delegerede tilladelser", og Gem konfigurationen.


- I Visual Studio, skal du åbne `App.config` i TodoListClient project og angive din konfigurationsværdier i den `<appSettings>` sektion.
  - Den `ida:Tenant` er navnet på din Azure AD-lejer, f.eks. "contoso.onmicrosoft.com".
  - Din `ida:ClientId` app-ID, du kopierede fra Azure-portalen.
  - Din `todo:TodoListResourceId` er App-ID URI for at gøre listen tjenesteprogrammet, du har angivet i portalen Azure.

Til sidst skal du rydde, opbygge og køre hvert projekt!  Hvis du ikke allerede har gjort det, nu er det tid til at oprette en ny bruger i din lejer med en *. onmicrosoft.com-domæne.  Log på opgavelisten klienten med den pågældende bruger, og Føj nogle opgaver til brugerens opgavelisten.

Til reference gennemført prøveoverførsel (uden din konfigurationsværdier) er angivet [her](https://github.com/AzureADQuickStarts/WebAPI-Bearer-DotNet/archive/complete.zip).  Du kan nu gå videre til mere yderligere identitet scenarier.

[AZURE.INCLUDE [active-directory-devquickstarts-additional-resources](../../includes/active-directory-devquickstarts-additional-resources.md)]
