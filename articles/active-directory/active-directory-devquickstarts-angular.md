<properties
    pageTitle="Azure AD-AngularJS Introduktion | Microsoft Azure"
    description="Sådan oprettes en vinklet JS enkelt side-program, der integreres med Azure AD til logon og Azure AD-opkald beskyttet API'er ved hjælp af OAuth."
    services="active-directory"
    documentationCenter=""
    authors="dstrockis"
    manager="mbaldwin"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="javascript"
    ms.topic="article"
    ms.date="09/16/2016"
    ms.author="dastrock"/>


# <a name="securing-angularjs-single-page-apps-with-azure-ad"></a>Sikring af AngularJS enkelt side-Apps med Azure AD

[AZURE.INCLUDE [active-directory-devquickstarts-switcher](../../includes/active-directory-devquickstarts-switcher.md)]

[AZURE.INCLUDE [active-directory-devguide](../../includes/active-directory-devguide.md)]

Azure AD gør det enkle og nemt for dig at føje log in, log af og sikre OAuth API opkald til dine apps enkelt side.  Det gør det muligt for din app til at godkende brugere med deres Active Directory-konti og bruge en hvilken som helst web API, der er beskyttet af Azure AD, som Office 365 API'er eller Azure API.

Azure AD indeholder til javascript-programmer, der kører i en browser, Active Directory Authentication Library eller adal.js.  Adal.js's eneste formål i liv er at gøre det lettere for din app til at få adgangstokens.  For at vise lige hvor let det er skal her vi oprettes et AngularJS til at gøre listen program, der:

- Brugeren logges appen med Azure AD som identitetsudbyder.
- Viser nogle oplysninger om brugeren.
- Sikkert opkald på app til at gøre listen API'EN ved hjælp af bæreren tokens ud fra AAD.
- Logger brugeren af app'en.

Hvis du vil basere fuldført arbejde programmet, skal du:

2. Registrere dit program med Azure AD.
3. Installere ADAL og konfigurere SPA.
5. Brug ADAL til at sikre sider i SPA.

At komme i gang skal [hente app skelet](https://github.com/AzureADQuickStarts/SinglePageApp-AngularJS-DotNet/archive/skeleton.zip) eller [hente gennemført prøveoverførsel](https://github.com/AzureADQuickStarts/SinglePageApp-AngularJS-DotNet/archive/complete.zip).  Du skal også en Azure AD-lejer, hvor du kan oprette brugere og registrere et program.  Hvis du ikke allerede har en lejer, [se, hvordan du få en](active-directory-howto-tenant.md).

## <a name="1-register-the-directorysearcher-application"></a>*1. registrere programmet DirectorySearcher*
Hvis du vil aktivere din app til at godkende brugere og få tokens, skal du først registrere den i din Azure AD-lejer:

-   Log på [administrationsportalen til af Azure](https://manage.windowsazure.com)
-   Klik på **Active Directory** i til venstre i navigationen
-   Vælg en lejer, til at registrere programmet.
-   Klik på fanen **programmer** , og klik på **Tilføj** i den nederste skuffe.
-   Følg anvisningerne, og oprette et nyt **webprogram og/eller WebAPI**.
    -   **Navnet** på programmet, som beskriver dit program til slutbrugere.
    -   **Omdirigere Uri** er placering, hvor AAD returnerer tokens.  Standardplaceringen for dette eksempel er`https://localhost:44326/`
-   Når du har færdiggjort registrering, tildele AAD din app et entydigt **Klient-ID**.  Du skal bruge denne værdi i næste afsnit, så kopiere det fra fanen **Konfigurer** .
- Adal.js bruger OAuth implicit strømmen til at kommunikere med Azure AD.  Du skal aktivere implicit strømmen for dit program ved at:
    - Hente programmanifestet ved at klikke på **Administrer manifestet**.
    - Åbn manifestet, og Find den `oauth2AllowImplicitFlow` egenskab. Angive værdien til `true`.
    - Gemme og overføre programmanifestet ved at klikke på **Administrer manifestet** igen.

## <a name="2-install-adal--configure-the-spa"></a>*2. Installer ADAL og konfigurere SPA*
Nu hvor du har et program i Azure AD, kan du installere adal.js og skrive din identitet-relaterede kode.

-   Start med at tilføje adal.js til TodoSPA projektet ved hjælp af konsollen Package Manager:
  - Hente [adal.js](https://raw.githubusercontent.com/AzureAD/azure-activedirectory-library-for-js/master/lib/adal.js) og føje den til den `App/Scripts/` projektmappen.
  - Hente [adal angular.js](https://raw.githubusercontent.com/AzureAD/azure-activedirectory-library-for-js/master/lib/adal-angular.js) og føje den til den `App/Scripts/` projektmappen.
  - Indlæse hvert script før slutningen af den `</body>` i `index.html`:

```js
...
<script src="App/Scripts/adal.js"></script>
<script src="App/Scripts/adal-angular.js"></script>
...
```

-   For den SPA backend-version til at gøre listen API'EN at acceptere tokens fra browseren, skal back-end konfigurationsoplysninger om app registrering. Åbn i project TodoSPA `web.config`.  Erstatte værdier af elementer i det `<appSettings>` afsnit for at afspejle de værdier, du indtaster i portalen Azure.  Din kode refererer disse værdier, når den anvender ADAL.
    -   Den `ida:Tenant` er domænet for din Azure AD-lejer, f.eks. contoso.onmicrosoft.com
    -   Den `ida:Audience` skal være **Klient-ID** for dit program, du har kopieret fra portalen.

## <a name="3--use-adal-to-secure-pages-in-the-spa"></a>*3. Brug ADAL til at sikre sider i SPA*
Adal.js har er udviklet til at integrere med AngularJS distribuere og HTTP-udbydere, som gør det muligt at sikre individuelle visninger i din SPA.

- I `App/Scripts/app.js`, sætter i modulet adal.js:

```js
angular.module('todoApp', ['ngRoute','AdalAngular'])
.config(['$routeProvider','$httpProvider', 'adalAuthenticationServiceProvider',
 function ($routeProvider, $httpProvider, adalProvider) {
...
```
- Du kan nu har initialiseret den `adalProvider` med værdierne, konfiguration af dit program registrering, også i `App/Scripts/app.js`:

```js
adalProvider.init(
  {
      instance: 'https://login.microsoftonline.com/',
      tenant: 'Enter your tenant name here e.g. contoso.onmicrosoft.com',
      clientId: 'Enter your client ID here e.g. e9a5a8b6-8af7-4719-9821-0deef255f68e',
      extraQueryParameter: 'nux=1',
      //cacheLocation: 'localStorage', // enable this for IE, as sessionStorage does not work for localhost.
  },
  $httpProvider
);
```
- Nu til at sikre den `TodoList` i appen, får du kun vist én linje af kode er nødvendig - `requireADLogin`.

```js
...
}).when("/TodoList", {
        controller: "todoListCtrl",
        templateUrl: "/App/Views/TodoList.html",
        requireADLogin: true,
...
```

Du har nu et sikkert enkelt side program med muligheden for at logge brugere og udstede bæreren token beskyttet anmodninger til dens back-end API.  Når en bruger klikker på `TodoList` link, adal.js automatisk omdirigerer til Azure AD for Log på hvis det er nødvendigt.  Desuden vedhæftes adal.js automatisk en access_token ajax anmodninger, der sendes til programmets backend-version.  Ovenstående er de mest mindste nødvendige at opbygge en SPA med adal.js -, men der er en række andre funktioner, der er nyttig i kursteder:

- Du kan angive funktioner i dine enheder, der aktiverer adal.js eksplicit problem Log på, og log af anmodninger om.  In `App/Scripts/homeCtrl.js`:

```js
...
$scope.login = function () {
    adalService.login();
};
$scope.logout = function () {
    adalService.logOut();
};
...
```
- Du kan også til at præsentere brugeroplysninger i Brugergrænsefladen på app.  Tjenesten adal er allerede blevet føjet til den `userDataCtrl` controller, så du kan få adgang til den `userInfo` objekt i den tilknyttede visning `App/Views/UserData.html`:

```js
<p>{{userInfo.userName}}</p>
<p>aud:{{userInfo.profile.aud}}</p>
<p>iss:{{userInfo.profile.iss}}</p>
...
```

- Der er også mange scenarier, hvor du vil gerne vil vide, hvis brugeren er logget på eller ej.  Du kan også bruge den `userInfo` objekt til at indsamle oplysningerne.  Eksempelvis `index.html` kan du vise enten "Logon" eller "Log" knap på baggrund af godkendelse status:

```js
<li><a class="btn btn-link" ng-show="userInfo.isAuthenticated" ng-click="logout()">Logout</a></li>
<li><a class="btn btn-link" ng-hide=" userInfo.isAuthenticated" ng-click="login()">Login</a></li>
```

Tillykke! Din Azure AD integreret enkelt side App er nu færdig.  Det kan godkende brugere, sikkert opkald dens backend-version ved hjælp af OAuth 2.0, og få grundlæggende oplysninger om brugeren.  Hvis du ikke allerede har gjort det, nu er det tid til at udfylde din lejer med nogle brugere.  Kør din til at gøre listen SPA, og log på med en af disse brugere.  Føje opgaver til brugerne, kan du gøre liste, logge af og logge på igen.

Adal.js gør det nemt at inkorporere alle disse almindelige identitet-funktioner i dit program.  Det sig tager af alle ændret arbejdet for dig - cache-styring, OAuth-protokollen support, præsenterer brugeren med et login brugergrænseflade, opdatere udløbet tokens og meget mere.

Til reference gennemført prøveoverførsel (uden din konfigurationsværdier) er angivet [her](https://github.com/AzureADQuickStarts/SinglePageApp-AngularJS-DotNet/archive/complete.zip).  Du kan nu gå videre til yderligere scenarier.  Vil du måske at prøve:

[Ringe til en CORS Web API fra en SPA >>](https://github.com/AzureAdSamples/SinglePageApp-WebAPI-AngularJS-DotNet)

[AZURE.INCLUDE [active-directory-devquickstarts-additional-resources](../../includes/active-directory-devquickstarts-additional-resources.md)]
