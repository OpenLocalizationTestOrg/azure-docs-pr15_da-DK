<properties
    pageTitle="Azure AD v2.0 AngularJS Introduktion | Microsoft Azure"
    description="Sådan oprettes en vinklet JS enkelt side-app, der logger på brugere med begge personlige Microsoft-konti og arbejds- eller skolekonto konti."
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


# <a name="add-sign-in-to-an-angularjs-single-page-app---nodejs"></a>Føje-logon til en AngularJS enkelt side app - NodeJS

I denne artikel tilføjer vi logge på med Microsoft drevet konti til en AngularJS-app ved hjælp af Azure Active Directory v2.0 slutpunkt. v2.0 slutpunktet gør det muligt at udføre en enkelt integration i din app og godkende brugere med både personlige og arbejde/skole-konti.

Dette eksempel er en enkel opgaveliste enkelt side app, der gemmer opgaver i en back-end REST-API, skrives på NodeJS og sikret ved hjælp af OAuth bæreren tokens fra Azure AD.  Appen AngularJS bruger vores Åbn kilde JavaScript godkendelse bibliotek [adal.js](https://github.com/AzureAD/azure-activedirectory-library-for-js) til at håndtere processen hele logge og få fat på tokens for opkald til REST-API.  Det samme mønster kan anvendes for at godkende til andre REST API'er, som [Microsoft Graph](https://graph.microsoft.com) eller Azure ressourcestyring API'er.

> [AZURE.NOTE]
    Ikke alle Azure Active Directory scenarier og funktioner, der understøttes af v2.0 slutpunkt.  For at bestemme, hvis du skal bruge v2.0 slutpunkt skal du læse om [v2.0 begrænsninger](active-directory-v2-limitations.md).

## <a name="download"></a>Download

For at komme i gang, skal du downloade og installere [node.js](https://nodejs.org).  Derefter kan du klone eller [hente](https://github.com/AzureADQuickStarts/AppModelv2-SinglePageApp-AngularJS-NodeJS/archive/skeleton.zip) en skelet app:

```
git clone --branch skeleton https://github.com/AzureADQuickStarts/AppModelv2-SinglePageApp-AngularJS-NodeJS.git
```

Appen skelet indeholder alle dele af et kode til en enkelt AngularJS-app, men der mangler alle de identitet-relaterede.  Hvis du ikke vil følge med, kan du i stedet klone eller [hente](https://github.com/AzureADQuickStarts/AppModelv2-SinglePageApp-AngularJS-NodeJS/archive/complete.zip) gennemført prøveoverførsel.

```
git clone https://github.com/AzureADSamples/SinglePageApp-AngularJS-NodeJS.git
```

## <a name="register-an-app"></a>Registrere en app

Først skal oprette en app til [App registrering Portal](https://apps.dev.microsoft.com/?referrer=https://azure.microsoft.com/documentation/articles&deeplink=/appList), eller Følg disse [detaljeret vejledning](active-directory-v2-app-registration.md).  Sørg for at:

- Tilføje **Web** platform til din app.
- Angiv den korrekte **Omdirigere URI**. Standard for dette eksempel er `http://localhost:8080`.
- Lad afkrydsningsfeltet **Tillad Implicit dataflow** er aktiveret. 

Kopiér ned **Program-ID** , der er tildelt din app, du skal bruge den kort. 

## <a name="install-adaljs"></a>Installere adal.js
For at starte skal du gå til project-du har hentet og installere adal.js.  Hvis du har [bower](http://bower.io/) installeret, kan du kun kører denne kommando.  For en hvilken som helst afhængighed version uoverensstemmelser blot vælge den nyere version.
```
bower install adal-angular#experimental
```

Alternativt kan du manuelt hente [adal.js](https://raw.githubusercontent.com/AzureAD/azure-activedirectory-library-for-js/experimental/dist/adal.min.js) og [adal angular.js](https://raw.githubusercontent.com/AzureAD/azure-activedirectory-library-for-js/experimental/dist/adal-angular.min.js).  Tilføje begge filer på `app/lib/adal-angular-experimental/dist` directory.

Nu åbne projektet i din foretrukne teksteditor, og Indlæs adal.js i slutningen af siden brødteksten:

```html
<!--index.html-->

...

<script src="App/bower_components/dist/adal.min.js"></script>
<script src="App/bower_components/dist/adal-angular.min.js"></script>

...
```

## <a name="set-up-the-rest-api"></a>Konfigurere REST-API

Mens vi konfigurerer ting, du kan få back end-REST-API til at fungere.  I en kommandoprompt skal du installere alle de nødvendige pakker ved at køre (Sørg for, at du er i mappen på øverste niveau af projektet):

```
npm install
```

Nu åbner `config.js` og erstatte den `audience` værdi:

```js
exports.creds = {
     
     // TODO: Replace this value with the Application ID from the registration portal
     audience: '<Your-application-id>',
     
     ...
}
```

REST-API bruge denne værdi til at validere tokens den modtager fra vinklet app på AJAX-anmodninger.  Bemærk, at denne enkle REST-API lagrer data i hukommelsen – så hver gang stoppe med at serveren, mister du alle tidligere oprettet opgaver.

Det er hele tiden vi bruger omhandler, hvordan REST-API fungerer.  Velkommen til at se nærmere på kode, men hvis du vil have mere at vide om sikring af web API'er med Azure AD se [i denne artikel](active-directory-v2-devquickstarts-node-api.md). 

## <a name="sign-users-in"></a>Log brugere i
Tid til at skrive identitet kode.  Du har måske allerede bemærket, adal.js indeholder en udbyder af AngularJS, som afspilles fint med vinklet routing mekanismer.  Start med at føje modulet adal til appen:

```js
// app/scripts/app.js

angular.module('todoApp', ['ngRoute','AdalAngular'])
.config(['$routeProvider','$httpProvider', 'adalAuthenticationServiceProvider',
 function ($routeProvider, $httpProvider, adalProvider) {

...
```

Du kan nu har initialiseret den `adalProvider` med dit program-ID:

```js
// app/scripts/app.js

...

adalProvider.init({
        
        // Use this value for the public instance of Azure AD
        instance: 'https://login.microsoftonline.com/', 
        
        // The 'common' endpoint is used for multi-tenant applications like this one
        tenant: 'common',
        
        // Your application id from the registration portal
        clientId: '<Your-application-id>',
        
        // If you're using IE, uncommment this line - the default HTML5 sessionStorage does not work for localhost.
        //cacheLocation: 'localStorage',
         
    }, $httpProvider);
```

Gode, nu adal.js har alle de oplysninger, der skal sikre din app og logge brugere.  For at tvinge logon til en bestemt rute i appen, er alle det tager én linje af kode:

```js
// app/scripts/app.js

...

}).when("/TodoList", {
    controller: "todoListCtrl",
    templateUrl: "/static/views/TodoList.html",
    requireADLogin: true, // Ensures that the user must be logged in to access the route
})

...
```

Nu, når en bruger klikker på `TodoList` link, adal.js automatisk omdirigerer til Azure AD til logon Hvis det er nødvendigt.  Du kan også direkte sende logge på og logge anmodninger ved aktivering af adal.js i dine enheder:

```js
// app/scripts/homeCtrl.js

angular.module('todoApp')
// Load adal.js the same way for use in controllers and views   
.controller('homeCtrl', ['$scope', 'adalAuthenticationService','$location', function ($scope, adalService, $location) {
    $scope.login = function () {
        
        // Redirect the user to sign in
        adalService.login();
        
    };
    $scope.logout = function () {
        
        // Redirect the user to log out    
        adalService.logOut();
    
    };
...
```

## <a name="display-user-info"></a>Få vist brugeroplysninger
Nu, hvor brugeren er logget på, skal du sandsynligvis at få adgang til logget på brugerens godkendelsesdata i dit program.  Adal.js viser disse oplysninger for dig i den `userInfo` objekt.  For at få adgang til dette objekt i en visning, du først føje adal.js til rod omfanget af den tilsvarende controller:

```js
// app/scripts/userDataCtrl.js

angular.module('todoApp')
// Load ADAL for use in view
.controller('userDataCtrl', ['$scope', 'adalAuthenticationService', function ($scope, adalService) {}]);
```

Og du kan tale om direkte på `userInfo` objekt i visningen: 

```html
<!--app/views/UserData.html-->

...

    <!--Get the user's profile information from the ADAL userInfo object-->
    <tr ng-repeat="(key, value) in userInfo.profile">
        <td>{{key}}</td>
        <td>{{value}}</td>
    </tr>
...
```

Du kan også bruge den `userInfo` objekt til at bestemme, hvis brugeren er logget på, eller ej.

```html
<!--index.html-->

...

    <!--Use the ADAL userInfo object to show the right login/logout button-->
    <ul class="nav navbar-nav navbar-right">
        <li><a class="btn btn-link" ng-show="userInfo.isAuthenticated" ng-click="logout()">Logout</a></li>
        <li><a class="btn btn-link" ng-hide="userInfo.isAuthenticated" ng-click="login()">Login</a></li>
    </ul>
...
```

## <a name="call-the-rest-api"></a>Ringe til REST-API
Til sidst, er det tid til at få nogle tokens og ringe REST-API for at oprette, læse, opdatere og slette opgaver.  Godt ved du hvad?  Du behøver at gøre *en ting*.  Adal.js sig automatisk tager af få cachelagring og opdatere tokens.  Det kan også tage højde for vedhæfte disse tokens til udgående AJAX-anmodninger, du sender til REST-API.  

Hvordan nøjagtigt fungerer det? Det er alle sammen takket være Magien ved [AngularJS interceptors](https://docs.angularjs.org/api/ng/service/$http), som giver mulighed for adal.js til at transformere udgående og indgående http-meddelelser.  Desuden forudsætter adal.js, at sende anmodninger til det samme domæne som vinduet skal bruge tokens, der er beregnet til det samme program-ID som AngularJS app.  Det er derfor brugte vi det samme program-ID i begge vinklet app og i NodeJS REST-API.  Selvfølgelig, du kan tilsidesætte denne funktionsmåde og fortælle adal.js for at hente tokens til andre REST API'er, hvis det er nødvendigt - men standardindstillingerne Benyt for dette simple scenario.

Her er et kodestykke, der viser, hvor nemt det er at sende anmodninger med bæreren tokens fra Azure AD:

```js
// app/scripts/todoListSvc.js

...
return $http.get('/api/tasks');
...
```

Tillykke!  Din app til Azure AD integreret enkelt side er nu færdig.  Gå frem, tage sløjfe.  Det kan godkende brugere, sikkert opkald dens back-end REST-API med OpenID Opret forbindelse og få grundlæggende oplysninger om brugeren.  Af feltet understøtter alle brugere med en personlig Microsoft-Account eller en arbejds-eller skolekonto fra Azure AD.  Prøv appen ved at køre:

```
node server.js
```

Gå til i en browser `http://localhost:8080`.  Log på med en personlig Microsoft-konto eller en arbejds-eller skolekonto-konto.  Føje opgaver til brugerens opgaveliste, og log af.  Prøv at logge på med anden typen konto. Hvis du har brug for en Azure AD-lejer til at oprette arbejds-eller skolekonto brugere [lære at få en her](active-directory-howto-tenant.md) (det er gratis).

Fortsætte med at lære mere om den v2.0 slutpunktet, gå tilbage til vores [v2.0 udvikler vejledning](active-directory-appmodel-v2-overview.md).  Yderligere ressourcer, se:

- [Azure-eksempler på GitHub >>](https://github.com/Azure-Samples)
- [Azure AD i stablen overløbsområde >>](http://stackoverflow.com/questions/tagged/azure-active-directory)
- Azure AD-dokumentation på [Azure.com >>](https://azure.microsoft.com/documentation/services/active-directory/)

## <a name="get-security-updates-for-our-products"></a>Hente sikkerhedsopdateringer til vores produkter

Vi anbefaler, at finde meddelelser om når sikkerhedshændelser forekommer, ved at besøge [denne side](https://technet.microsoft.com/security/dd252948) og abonnere på vejledende sikkerhedsadvarsler.
