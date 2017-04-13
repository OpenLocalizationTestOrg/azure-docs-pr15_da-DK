<properties
    pageTitle="Azure AD v2.0 .NET Web API | Microsoft Azure"
    description="Sådan oprettes et .NET MVC Web Api, der accepterer tokens ud fra begge personlig Microsoft-Account og arbejds- eller skolekonto konti."
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
    ms.date="10/10/2016"
    ms.author="dastrock"/>

# <a name="secure-an-mvc-web-api"></a>Sikre en MVC web API

Du kan beskytte en Web API ved hjælp af [OAuth 2.0](active-directory-v2-protocols.md#oauth2-authorization-code-flow) access tokens, så brugerne med både personlige Microsoft-konto og arbejde med Azure Active Directory v2.0 slutpunktet, eller skole konti til sikker access Web-API'EN.

> [AZURE.NOTE]
    Ikke alle Azure Active Directory scenarier og funktioner, der understøttes af v2.0 slutpunkt.  For at bestemme, hvis du skal bruge v2.0 slutpunkt skal du læse om [v2.0 begrænsninger](active-directory-v2-limitations.md).

I ASP.NET web API'er, kan du udføre dette med Microsofts OWIN programmer er inkluderet i .NET Framework 4.5.  Her skal vi bruge OWIN til at oprette et "Opgavelisten" MVC Web API, der gør det muligt for klienter mulighed for at oprette og læse opgaver fra en bruger opgaveliste.  World Wide web API validerer, indgående anmodninger indeholder en gyldig adgangstoken og afvise anmodninger, der ikke valideres på en beskyttet rute.  Dette eksempel blev oprettet ved hjælp af Visual Studio-2015.

## <a name="download"></a>Download
Koden for dette selvstudium vedligeholdes [GitHub](https://github.com/AzureADQuickStarts/AppModelv2-WebAPI-DotNet).  Hvis du vil følge med, kan du [hente den app skelet som en .zip](https://github.com/AzureADQuickStarts/AppModelv2-WebAPI-DotNet/archive/skeleton.zip) eller klone skelet:

```
git clone --branch skeleton https://github.com/AzureADQuickStarts/AppModelv2-WebAPI-DotNet.git
```

Appen skelet indeholder alle dele af et kode for en enkelt API, men der mangler alle de identitet-relaterede. Hvis du ikke vil følge med, kan du i stedet klone eller [hente gennemført prøveoverførsel](https://github.com/AzureADQuickStarts/AppModelv2-WebAPI-DotNet/archive/skeleton.zip).

```
git clone https://github.com/AzureADQuickStarts/AppModelv2-WebAPI-DotNet.git
```

## <a name="register-an-app"></a>Registrere en app
Oprette en ny app på [apps.dev.microsoft.com](https://apps.dev.microsoft.com/?referrer=https://azure.microsoft.com/documentation/articles&deeplink=/appList), eller Følg disse [detaljeret vejledning](active-directory-v2-app-registration.md).  Sørg for at:

- Kopiér ned på **Program-Id** , tildelt til din app, du skal bruge den snart.

Denne visual studio-løsning indeholder også en "TodoListClient", som er en simpel WPF-app.  TodoListClient bruges til at vise, hvordan en bruger tegn på, og hvordan en klient kan udstede anmodninger til din Web API.  I dette tilfælde repræsenteres både TodoListClient og TodoListService af den samme app.  Hvis du vil konfigurere TodoListClient, skal du også:

- Tilføj **Mobile** platformen til din app.


## <a name="install-owin"></a>Installere OWIN

Nu hvor du har registreret en app, skal du konfigurere din app til at kommunikere med v2.0 slutpunkt for at validere indgående anmodninger og tokens.

- For at starte skal du åbne løsningen og føje OWIN program NuGet pakker til TodoListService projektet ved hjælp af konsollen Package Manager.

```
PM> Install-Package Microsoft.Owin.Security.OAuth -ProjectName TodoListService
PM> Install-Package Microsoft.Owin.Security.Jwt -ProjectName TodoListService
PM> Install-Package Microsoft.Owin.Host.SystemWeb -ProjectName TodoListService
```

## <a name="configure-oauth-authentication"></a>Konfigurere OAuth-godkendelse

- Føje en OWIN Start klasse til TodoListService projektet kaldet `Startup.cs`.  Højre Klik på projektet--> **Tilføj** --> **Nyt element** --> Søg efter "OWIN".  OWIN programmer skal aktivere den `Configuration(…)` metode, når din app starter.
- Ændre erklæringen af klasse til `public partial class Startup` -vi har allerede implementeret en del af kurset for dig i en anden fil.  I den `Configuration(…)` metode, foretage et opkald til ConfgureAuth(...) til at konfigurere godkendelse for din online.

```C#
public partial class Startup
{
    public void Configuration(IAppBuilder app)
    {
        ConfigureAuth(app);
    }
}
```

- Åbn filen `App_Start\Startup.Auth.cs` og implementere den `ConfigureAuth(…)` -metoden, som vil konfigurere Web API til at acceptere tokens fra v2.0 slutpunkt.

```C#
public void ConfigureAuth(IAppBuilder app)
{
        var tvps = new TokenValidationParameters
        {
                // In this app, the TodoListClient and TodoListService
                // are represented using the same Application Id - we use
                // the Application Id to represent the audience, or the
                // intended recipient of tokens.

                ValidAudience = clientId,

                // In a real applicaiton, you might use issuer validation to
                // verify that the user's organization (if applicable) has
                // signed up for the app.  Here, we'll just turn it off.

                ValidateIssuer = false,
        };

        // Set up the OWIN pipeline to use OAuth 2.0 Bearer authentication.
        // The options provided here tell the middleware about the type of tokens
        // that will be recieved, which are JWTs for the v2.0 endpoint.

        // NOTE: The usual WindowsAzureActiveDirectoryBearerAuthenticaitonMiddleware uses a
        // metadata endpoint which is not supported by the v2.0 endpoint.  Instead, this
        // OpenIdConenctCachingSecurityTokenProvider can be used to fetch & use the OpenIdConnect
        // metadata document.

        app.UseOAuthBearerAuthentication(new OAuthBearerAuthenticationOptions
        {
                AccessTokenFormat = new Microsoft.Owin.Security.Jwt.JwtFormat(tvps, new OpenIdConnectCachingSecurityTokenProvider("https://login.microsoftonline.com/common/v2.0/.well-known/openid-configuration")),
        });
}
```

- Nu kan du bruge `[Authorize]` attributter for at beskytte dine enheder og handlinger med OAuth 2.0 bæreren godkendelse.  Dekorere den `Controllers\TodoListController.cs` klasse med en godkende mærke.  Dette tvinger brugeren til at logge på, før du få adgang til denne side.

```C#
[Authorize]
public class TodoListController : ApiController
{
```

- Når en autoriserede opkalds korrekt kalder en af de `TodoListController` API'er, handlingen, der muligvis skal have adgang til oplysninger om kalderen.  OWIN giver adgang til krav i bærertoken via den `ClaimsPrincpal` objekt.  

```C#
public IEnumerable<TodoItem> Get()
{
    // You can use the ClaimsPrincipal to access information about the
    // user making the call.  In this case, we use the 'sub' or
    // NameIdentifier claim to serve as a key for the tasks in the data store.

    Claim subject = ClaimsPrincipal.Current.FindFirst(ClaimTypes.NameIdentifier);

    return from todo in todoBag
           where todo.Owner == subject.Value
           select todo;
}
```

-   Til sidst skal åbne den `web.config` filen er i roden af TodoListService projektet, og angiv din konfigurationsværdier i den `<appSettings>` sektion.
  - Din `ida:Audience` er **Program-Id** for den app, du har angivet i portalen.

## <a name="configure-the-client-app"></a>Konfigurere klient-app
Før du kan se tjenesten opgaveliste i handling, skal du konfigurere den opgaveliste listen klient, så den kan hente tokens fra v2.0 slutpunkt og foretage opkald til tjenesten.

- Åbn i project TodoListClient `App.config` og angive din konfigurationsværdier i den `<appSettings>` sektion.
  - Din `ida:ClientId` program-Id du har kopieret fra portalen.

Til sidst skal du rydde, opbygge og køre hvert projekt!  Du har nu et .NET MVC Web API, der accepterer tokens ud fra begge personlige Microsoft-konti og arbejds- eller skolekonto konti.  Log på TodoListClient, og kald webstedet api til at føje opgaver til brugerens opgaveliste.

Til reference kan på gennemført prøveoverførsel (uden din konfigurationsværdier), [der er angivet en .zip her](https://github.com/AzureADQuickStarts/AppModelv2-WebAPI-DotNet/archive/complete.zip), eller du klone den fra GitHub:

```git clone --branch complete https://github.com/AzureADQuickStarts/AppModelv2-WebAPI-DotNet.git```

## <a name="next-steps"></a>Næste trin
Nu kan du flytte til flere emner.  Vil du måske at prøve:

[Ringe til en Web API fra en WebApp >>](active-directory-v2-devquickstarts-webapp-webapi-dotnet.md)

Yderligere ressourcer, se:
- [Guiden v2.0 udvikler >>](active-directory-appmodel-v2-overview.md)
- [StackOverflow "azure-active directory" mærke >>](http://stackoverflow.com/questions/tagged/azure-active-directory)

## <a name="get-security-updates-for-our-products"></a>Hente sikkerhedsopdateringer til vores produkter

Vi anbefaler, at finde meddelelser om når sikkerhedshændelser forekommer, ved at besøge [denne side](https://technet.microsoft.com/security/dd252948) og abonnere på vejledende sikkerhedsadvarsler.
