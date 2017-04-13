<properties
    pageTitle="Azure AD-Xamarin Introduktion | Microsoft Azure"
    description="Sådan oprettes en Xamarin-program, der integreres med Azure AD til logon og Azure AD-opkald beskyttet API'er ved hjælp af OAuth."
    services="active-directory"
    documentationCenter="xamarin"
    authors="dstrockis"
    manager="mbaldwin"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="mobile-xamarin"
    ms.devlang="dotnet"
    ms.topic="article"
    ms.date="09/16/2016"
    ms.author="dastrock"/>


# <a name="integrate-azure-ad-into-a-xamarin-app"></a>Integrere Azure AD i en Xamarin-App

[AZURE.INCLUDE [active-directory-devquickstarts-switcher](../../includes/active-directory-devquickstarts-switcher.md)]

[AZURE.INCLUDE [active-directory-devguide](../../includes/active-directory-devguide.md)]

Xamarin kan du skrive mobilapps i C#, som kan køre på iOS-, Android- og Windows (mobilenheder og pc'er). Hvis du opretter en app ved hjælp af Xamarin, gør det Azure AD, enkle og nemt for dig at godkende dine brugere med deres Active Directory-konti. Det kan også programmet sikkert bruge en hvilken som helst web API, der er beskyttet af Azure AD, som Office 365 API'er eller Azure API.

Azure AD indeholder til Xamarin apps, der skal have adgang til ressourcer, der er beskyttet, Active Directory Authentication Library eller ADAL. ADALS eneste formål i liv er at gøre det lettere for din app til at få adgangstokens. For at vise lige hvor let det er kan her vi udvikle en "Directory brugerne" app, som:

-   Kører på iOS, Android, Windows-skrivebord, Windows Phone og Windows Store.
- Anvender en enkelt bærbare klassebibliotek (PCL) til at godkende brugere og få tokens til Azure AD Graph API
-   Søger i en mappe til brugere med en given UPN.

Hvis du vil basere fuldført arbejde programmet, skal du:

2. Konfigurere din Xamarin udviklingsmiljø
2. Registrere dit program med Azure AD.
3. Installere og konfigurere ADAL.
5. Brug ADAL til at få tokens fra Azure AD.

At komme i gang skal [hente et skelet projekt](https://github.com/AzureADQuickStarts/NativeClient-MultiTarget-DotNet/archive/skeleton.zip) eller [hente gennemført prøveoverførsel](https://github.com/AzureADQuickStarts/NativeClient-MultiTarget-DotNet/archive/complete.zip). Hver er en løsning i Visual Studio-2013. Du skal også en Azure AD-lejer, hvor du kan oprette brugere og registrere et program. Hvis du ikke allerede har en lejer, [se, hvordan du få en](active-directory-howto-tenant.md).

## <a name="0-set-up-your-xamarin-development-environment"></a>*0. konfigurere din Xamarin udviklingsmiljø*
Da dette selvstudium indeholder projekter til iOS-, Android- og Windows, skal du bruge Visual Studio og Xamarin sammen. Hvis du vil oprette den nødvendige miljø, Følg komplet vejledning i [konfigurere og installere til Visual Studio og Xamarin](https://msdn.microsoft.com/library/mt613162.aspx) på MSDN. Disse instruktioner omfatter materiale, du kan gennemse for at få flere oplysninger om Xamarin, mens du venter til Installer at fuldføre. 

Når du har færdiggjort opsætningen af det er nødvendigt, skal du åbne løsningen i Visual Studio til at komme i gang. Du kan finde seks projekter: fem platform-specifikke projekter og en bærbar klassebibliotek, der skal deles på tværs af alle platforme`DirectorySearcher.cs`

## <a name="1-register-the-directory-searcher-application"></a>*1. registrere programmet Directory brugerne*
For at aktivere din app til at hente tokens, skal du først at registrere den i din Azure AD-lejer og tildele den tilladelse til at få adgang til API Azure AD Graph:

-   Log på [administrationsportalen til af Azure](https://manage.windowsazure.com)
-   Klik på **Active Directory** i til venstre i navigationen
-   Vælg en lejer, til at registrere programmet.
-   Klik på fanen **programmer** , og klik på **Tilføj** i den nederste skuffe.
-   Følg anvisningerne, og oprette et nyt **Oprindelige-klientprogrammet**.
    -   **Navnet** på programmet, som beskriver dit program for slutbrugere
    -   **Omdirigere Uri** er en kombination af farveskema og streng, Azure AD vil bruge til at returnere token svar. Angiv en værdi, f.eks. `http://DirectorySearcher`.
-   Når du har færdiggjort registrering, Tildel AAD din app et entydigt klient-id. Du skal bruge denne værdi i næste afsnit, så kopiere det fra fanen **Konfigurer** .
- Find også sektionen "Tilladelser til andre programmer" **Konfigurer** under fanen. Tilføj tilladelsen **Access den din organisations Directory** under **Delegerede tilladelser**til programmet "Azure Active Directory". Dette vil gøre det muligt at forespørge Graph API til brugere.

## <a name="2-install--configure-adal"></a>*2., installere og konfigurere ADAL*
Nu hvor du har et program i Azure AD, kan du installere ADAL og skrive din identitet-relaterede kode. I rækkefølge for ADAL lov til at kommunikere med Azure AD, skal du give den nogle oplysninger om din app registrering.
-   Starte med at føje ADAL til hvert af projekterne i løsningen ved hjælp af konsollen Package Manager.

`
PM> Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory -ProjectName DirectorySearcherLib
`

`
PM> Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory -ProjectName DirSearchClient-Android
`

`
PM> Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory -ProjectName DirSearchClient-Desktop
`

`
PM> Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory -ProjectName DirSearchClient-iOS
`

`
PM> Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory -ProjectName DirSearchClient-Universal
`

- Du bør Bemærk, at to bibliotek referencer føjes til hvert projekt - PCL del af ADAL og en platform-specifikke del.

-   Åbn i project DirectorySearcherLib `DirectorySearcher.cs`. Ændre klasse medlem værdierne afspejler de værdier, du indtaster i portalen Azure. Din kode refererer disse værdier, når den anvender ADAL.
    -   Den `tenant` er domænet for din Azure AD-lejer, f.eks. contoso.onmicrosoft.com
    -   Den `clientId` er clientId af dit program, du har kopieret fra portalen.
    - Den `returnUri` er den redirectUri, du har angivet i portalen, f.eks. `http://DirectorySearcher`.

## <a name="3--use-adal-to-get-tokens-from-aad"></a>*3. Brug ADAL til at få Tokens fra AAD*
*Almost* alle de app godkendelse logik ligger i `DirectorySearcher.SearchByAlias(...)`. Alle, der er nødvendige i platform-specifikke projekter er at overføre en kontekstafhængig parameter til den `DirectorySearcher` PCL.

- Først skal du åbne `DirectorySearcher.cs` og tilføje en ny parameter til den `SearchByAlias(...)` metode. `IPlatformParameters`er den kontekstafhængige parameter, der indkapsler platform-specifikke objekterne, der ADAL skal udføre godkendelse.

```C#
public static async Task<List<User>> SearchByAlias(string alias, IPlatformParameters parent)
{
```

-   Dernæst skal initialiseret den `AuthenticationContext` -ADAL er primære klasse. Dette er, hvor du overfører ADAL koordinaterne det brug for at kommunikere med Azure AD. Kald derefter `AcquireTokenAsync(...)`, som accepterer den `IPlatformParameters` objekt og skal aktivere godkendelse strømmen nødvendigt at returnere et token til appen.

```C#
...
    AuthenticationResult authResult = null;
    try
    {
        AuthenticationContext authContext = new AuthenticationContext(authority);
        authResult = await authContext.AcquireTokenAsync(graphResourceUri, clientId, returnUri, parent);
    }
    catch (Exception ee)
    {
        results.Add(new User { error = ee.Message });
        return results;
    }
...
```
- `AcquireTokenAsync(...)`først forsøger at returnere et token til den ønskede ressource (Graph API i dette tilfælde) uden at spørge brugeren til at indtaste legitimationsoplysningerne (via cachelagring eller opdatere gamle tokens). Kun, hvis det er nødvendigt, vises den brugeren Azure AD med at logge på siden før indhente anmodede tokenet.


- Derefter kan du knytte adgangstoken til Graph API-anmodning i overskriften tilladelse:

```C#
...
    request.Headers.Authorization = new AuthenticationHeaderValue("Bearer", authResult.AccessToken);
...
```

Det er det hele for den `DirectorySearcher` PCL og din app er identitet-relaterede kode.  Alt, forbliver på er ved at ringe på `SearchByAlias(...)` metode i hver platform visninger, og hvor behov tilføje kode til håndtering af Brugergrænsefladen livscyklus.

####<a name="android"></a>Android:
- I `MainActivity.cs`, tilføje et opkald til `SearchByAlias(...)` Klik på knappen handler:

```C#
List<User> results = await DirectorySearcher.SearchByAlias(searchTermText.Text, new PlatformParameters(this));
```
- Du skal også tilsidesætte de `OnActivityResult` livscyklus metode til at videresende alle godkendelse omdirigerer tilbage til den relevante metode.  ADAL indeholder en hjælper metode til dette i Android:

```C#
...
protected override void OnActivityResult(int requestCode, Result resultCode, Intent data)
{
    base.OnActivityResult(requestCode, resultCode, data);
    AuthenticationAgentContinuationHelper.SetAuthenticationAgentContinuationEventArgs(requestCode, resultCode, data);
}
...
```

####<a name="windows-desktop"></a>Windows-skrivebord:
- I `MainWindow.xaml.cs`, skal du blot foretage et opkald til `SearchByAlias(...)` der passerer en `WindowInteropHelper` på skrivebordet `PlatformParameters` objekt:

```C#
List<User> results = await DirectorySearcher.SearchByAlias(
  SearchTermText.Text,
  new PlatformParameters(PromptBehavior.Auto, this.Handle));
```

####<a name="ios"></a>iOS:
- I `DirSearchClient_iOSViewController.cs`, iOS `PlatformParameters` objekt tager blot en reference til den visning Controller:

```C#
List<User> results = await DirectorySearcher.SearchByAlias(
  SearchTermText.Text,
  new PlatformParameters(PromptBehavior.Auto, this.Handle));
```

####<a name="windows-universal"></a>Windows Universal:
- Åbn i Windows Universal `MainPage.xaml.cs` og implementere den `Search` -metoden, som bruger en hjælper metode i en delt project til at opdatere Brugergrænsefladen efter behov.

```C#
...
    List<User> results = await DirectorySearcherLib.DirectorySearcher.SearchByAlias(SearchTermText.Text, new PlatformParameters(PromptBehavior.Auto, false));
...
```

Tillykke! Du har nu en arbejde Xamarin app, der har mulighed for at godkende brugere og sikkert opkald Web API'er ved hjælp af OAuth 2.0 på tværs af fem forskellige platforme. Hvis du ikke allerede har gjort det, nu er det tid til at udfylde din lejer med nogle brugere. Kør din DirectorySearcher app, og log på med en af disse brugere. Søg efter andre brugere, der er baseret på deres UPN.

ADAL gør det nemt at inkorporere almindelige identitet funktioner i din app. Den sig tager af alle ændret arbejdet for dig - cache-styring, OAuth-protokollen understøttelse af præsentationer brugeren med et logon brugergrænseflade, opdatere udløbet tokens og meget mere. Alt, du virkelig vil vide, er et enkelt API-kald, `authContext.AcquireToken*(…)`.

Til reference gennemført prøveoverførsel (uden din konfigurationsværdier) er angivet [her](https://github.com/AzureADQuickStarts/NativeClient-MultiTarget-DotNet/archive/complete.zip). Du kan nu gå videre til yderligere identitet scenarier. Vil du måske at prøve:

[Sikre en .NET Web API med Azure AD >>](active-directory-devquickstarts-webapi-dotnet.md)

[AZURE.INCLUDE [active-directory-devquickstarts-additional-resources](../../includes/active-directory-devquickstarts-additional-resources.md)]
