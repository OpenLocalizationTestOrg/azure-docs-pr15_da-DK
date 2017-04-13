<properties
    pageTitle="Azure AD Windows Store Introduktion | Microsoft Azure"
    description="Sådan oprettes en Windows Store-program, der integreres med Azure AD til logon og Azure AD-opkald beskyttet API'er ved hjælp af OAuth."
    services="active-directory"
    documentationCenter="windows"
    authors="dstrockis"
    manager="mbaldwin"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="mobile-windows-store"
    ms.devlang="dotnet"
    ms.topic="article"
    ms.date="09/16/2016"
    ms.author="dastrock"/>


# <a name="integrate-azure-ad-with-a-windows-store-app"></a>Integrere Azure AD med et Windows Store-App

[AZURE.INCLUDE [active-directory-devquickstarts-switcher](../../includes/active-directory-devquickstarts-switcher.md)]

[AZURE.INCLUDE [active-directory-devguide](../../includes/active-directory-devguide.md)]

Hvis du udvikler en app til Windows Store, gør det Azure AD, enkle og nemt for dig at godkende dine brugere med deres Active Directory-konti.  Det kan også programmet sikkert bruge en hvilken som helst web API, der er beskyttet af Azure AD, som Office 365 API'er eller Azure API.

Azure AD indeholder til Windows Store programmer, der skal have adgang til beskyttede ressourcer, Active Directory Authentication Library eller ADAL.  ADALS eneste formål i liv er at gøre det lettere for din app til at få adgangstokens.  For at vise lige hvor let det er kan her vi udvikle en "Directory brugerne" Windows Store app, som:

-   Henter få adgang til tokens for opkald til Azure AD Graph API ved hjælp af [OAuth 2.0 godkendelse protocol](https://msdn.microsoft.com/library/azure/dn645545.aspx).
-   Søger i en mappe til brugere med en given UPN.
-   Fortegn brugere ud.

Hvis du vil basere fuldført arbejde programmet, skal du:

2. Registrere dit program med Azure AD.
3. Installere og konfigurere ADAL.
5. Brug ADAL til at få tokens fra Azure AD.

At komme i gang skal [hente et skelet projekt](https://github.com/AzureADQuickStarts/NativeClient-WindowsStore/archive/skeleton.zip) eller [hente gennemført prøveoverførsel](https://github.com/AzureADQuickStarts/NativeClient-WindowsStore/archive/complete.zip).  Hver er en løsning i Visual Studio-2015.  Du skal også en Azure AD-lejer, hvor du kan oprette brugere og registrere et program.  Hvis du ikke allerede har en lejer, [se, hvordan du få en](active-directory-howto-tenant.md).

## <a name="1-register-the-directory-searcher-application"></a>*1. registrere programmet Directory brugerne*
For at aktivere din app til at hente tokens, skal du først at registrere den i din Azure AD-lejer og tildele den tilladelse til at få adgang til API Azure AD Graph:

-   Log på [administrationsportalen til af Azure](https://manage.windowsazure.com)
-   Klik på **Active Directory** i til venstre i navigationen
-   Vælg en lejer, til at registrere programmet.
-   Klik på fanen **programmer** , og klik på **Tilføj** i den nederste skuffe.
-   Følg anvisningerne, og oprette et nyt **Oprindelige-klientprogrammet**.
    -   **Navnet** på programmet, som beskriver dit program for slutbrugere
    -   **Omdirigere Uri** er en kombination af farveskema og streng, Azure AD vil bruge til at returnere token svar.  Indtast en pladsholderværdi for nu, f.eks. `http://DirectorySearcher`.  Denne værdi skal erstatte senere.
-   Når du har færdiggjort registrering, Tildel AAD din app et entydigt klient-id.  Du skal bruge denne værdi i næste afsnit, så kopiere det fra fanen **Konfigurer** .
- Find også sektionen "Tilladelser til andre programmer" **Konfigurer** under fanen.  Tilføj adgangstilladelse **mappe som den bruger, der er logget på** under **Delegerede tilladelser**til programmet "Azure Active Directory".  Dette vil gøre det muligt at forespørge Graph API til brugere.

## <a name="2-install--configure-adal"></a>*2., installere og konfigurere ADAL*
Nu hvor du har et program i Azure AD, kan du installere ADAL og skrive din identitet-relaterede kode.  I rækkefølge for ADAL lov til at kommunikere med Azure AD, skal du give den nogle oplysninger om din app registrering.
-   Start med at tilføje ADAL til DirectorySearcher projektet ved hjælp af konsollen Package Manager.

```
PM> Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory
```

-   Åbn i project DirectorySearcher `MainPage.xaml.cs`.  Erstatte værdier i den `Config Values` område, der skal afspejler de værdier, du indtaster i portalen Azure.  Din kode referencer disse værdier, når den anvender ADAL.
    -   Den `tenant` er domænet for din Azure AD-lejer, f.eks. contoso.onmicrosoft.com
    -   Den `clientId` er clientId af dit program, du har kopieret fra portalen.
-   Nu skal du finde tilbagekald uri til Windows Store app.  Angive et pausepunkt på denne linje i den `MainPage` metode:

```
redirectURI = Windows.Security.Authentication.Web.WebAuthenticationBroker.GetCurrentApplicationCallbackUri();
```
- Oprette en løsningen, at sikre, at alle pakke referencer er gendannet.  Hvis pakker mangler, skal du åbne op Nuget Package Manager og gendanne pakkerne.
- Køre appen, og Kopiér Afsæt værdien af `redirectUri` når pausepunktet er ramme.  Det skal ligne

```
ms-app://s-1-15-2-1352796503-54529114-405753024-3540103335-3203256200-511895534-1429095407/
```

- Erstat værdien af **RedirectUri** med denne værdi tilbage under fanen **Konfigurer** dit program i portalen Azure administration.  

## <a name="3--use-adal-to-get-tokens-from-aad"></a>*3. Brug ADAL til at få Tokens fra AAD*
Grundlæggende princippet bag ADAL er, når din app skal et adgangstoken, det blot opkald `authContext.AcquireToken(…)`, og ADAL gør resten.  

-   Det første trin er initialiseret din app `AuthenticationContext` -ADAL er primære klasse.  Dette er, hvor du overfører ADAL koordinaterne der skal til at kommunikere med Azure AD og angiver, hvordan til cachen tokens.

```C#
public MainPage()
{
    ...

    authContext = new AuthenticationContext(authority);
}
```

- Find nu den `Search(...)` -metoden, som vil blive aktiveret, når brugeren klikker på knappen "Søg" i den app brugergrænseflade.  Denne metode gør en GET-anmodning til Azure AD Graph API til forespørgsel for brugere, hvis UPN begynder med det angivne søgeord.  Men hvis du vil forespørge Graph API, skal du medtage en access_token i den `Authorization` sidehoved for din anmodning - dette er, hvor ADAL kommer.

```C#
private async void Search(object sender, RoutedEventArgs e)
{
    ...
    AuthenticationResult result = null;
    try
    {
        result = await authContext.AcquireTokenAsync(graphResourceId, clientId, redirectURI, new PlatformParameters(PromptBehavior.Auto, false));
    }
    catch (AdalException ex)
    {
        if (ex.ErrorCode != "authentication_canceled")
        {
            ShowAuthError(string.Format("If the error continues, please contact your administrator.\n\nError: {0}\n\nError Description:\n\n{1}", ex.ErrorCode, ex.Message));
        }
        return;
    }
    ...
}
```
- Når din app anmoder om et token ved at ringe til `AcquireTokenAsync(...)`, ADAL vil forsøge at returnere et token uden at spørge brugeren til legitimationsoplysninger.  Hvis ADAL registrerer, at brugeren skal logge på at få et token, viser en dialogboks til logon, indsamling brugerens legitimationsoplysninger og returnere et token efter vellykket bekræftelse.  Hvis ADAL er i stand til at returnere et token for en eller anden grund, den `AuthenticationResult` bliver status for en fejl.

- Nu er det tid at bruge den på access_token, du lige har købt.  Også i den `Search(...)` metode, vedhæfte tokenet til Graph API få anmodningen i overskriften tilladelse:

```C#
// Add the access token to the Authorization Header of the call to the Graph API, and call the Graph API.
httpClient.DefaultRequestHeaders.Authorization = new HttpCredentialsHeaderValue("Bearer", result.AccessToken);

```
- Du kan også bruge den `AuthenticationResult` objekt til at vise oplysninger om brugeren i din app, som det bruger-id:

```C#
// Update the Page UI to represent the signed in user
ActiveUser.Text = result.UserInfo.DisplayableId;
```
- Til sidst, kan du bruge ADAL til at logge brugeren af programmet samt.  Når brugeren klikker på knappen "Log af", vi vil sikre, at det næste opkald til `AcquireTokenAsync(...)` vises et tegn i visningen.  Dette er med ADAL, lige så nemt som at fjerne token cachen:

```C#
private void SignOut()
{
    // Clear session state from the token cache.
    authContext.TokenCache.Clear();

    ...
}
```

Tillykke! Du kan nu har en Windows Store-app, der har mulighed for at godkende brugere, skal du ringe sikkert Web API'er ved hjælp af OAuth 2.0, der fungerer, og få grundlæggende oplysninger om brugeren.  Hvis du ikke allerede har gjort det, nu er det tid til at udfylde din lejer med nogle brugere.  Kør din DirectorySearcher app, og log på med en af disse brugere.  Søg efter andre brugere, der er baseret på deres UPN.  Luk app, og kør den igen.  Bemærk, hvordan brugerens session forbliver intakt.  Log af (ved at højreklikke på for at få vist den nederste linje), og logge på igen som en anden bruger.

ADAL gør det nemt at inkorporere alle disse almindelige identitet-funktioner i dit program.  Det sig tager af alle ændret arbejdet for dig - cache-styring, OAuth-protokollen support, præsenterer brugeren med et login brugergrænseflade, opdatere udløbet tokens og meget mere.  Alt, du virkelig vil vide, er et enkelt API-kald, `authContext.AcquireToken*(…)`.

Til reference gennemført prøveoverførsel (uden din konfigurationsværdier) er angivet [her](https://github.com/AzureADQuickStarts/NativeClient-WindowsStore/archive/complete.zip).  Du kan nu gå videre til yderligere identitet scenarier.  Vil du måske at prøve:

[Sikre en .NET Web API med Azure AD >>](active-directory-devquickstarts-webapi-dotnet.md)

[AZURE.INCLUDE [active-directory-devquickstarts-additional-resources](../../includes/active-directory-devquickstarts-additional-resources.md)]
