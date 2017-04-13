<properties
    pageTitle="Sådan bruger du JavaScript SDK til Azure-Mobilapps"
    description="Sådan bruges v til Azure Mobile-Apps"
    services="app-service\mobile"
    documentationCenter="javascript"
    authors="adrianhall"
    manager="erikre"
    editor=""/>

<tags
    ms.service="app-service-mobile"
    ms.workload="mobile"
    ms.tgt_pltfrm="html"
    ms.devlang="javascript"
    ms.topic="article"
    ms.date="10/01/2016"
    ms.author="adrianha"/>

# <a name="how-to-use-the-javascript-client-library-for-azure-mobile-apps"></a>Hvordan du bruger biblioteket JavaScript-klienten til Azure-Mobilapps

[AZURE.INCLUDE [app-service-mobile-selector-client-library](../../includes/app-service-mobile-selector-client-library.md)]

Denne vejledning lærer du at udføre almindelige scenarier ved hjælp af seneste [JavaScript SDK til Azure Mobile-Apps]. Hvis du er ny bruger af Azure Mobile-Apps, skal du først udføre [Azure Mobile Apps Hurtig Start] for at oprette en back-end og oprette en tabel. I denne vejledning der, fokuserer vi på ved hjælp af den mobile backend i HTML/JavaScript webprogrammer.

## <a name="supported-platforms"></a>Understøttede platforme

Vi har begrænset understøttelse af browsere til den aktuelle og seneste version af de vigtigste browsere: Google Chrome, Microsoft Edge, Microsoft Internet Explorer og Mozilla Firefox.  Vi forventer SDK, til at fungere sammen med enhver relativt moderne browser.

Pakken distribueres som et Universal JavaScript-modul, så det understøtter globals, AMD, og CommonJS formater.

##<a name="Setup"></a>Installation og forudsætninger

Denne vejledning forudsætter, at du har oprettet en back-end med en tabel. Denne vejledning forudsætter, at tabellen har det samme skema som tabellerne i disse selvstudier.

Installerer Azure Mobile Apps JavaScript SDK kan udføres den `npm` kommandoen:

```
npm install azure-mobile-apps-client --save
```

Når installeret, skal biblioteket er placeret i `node_modules/azure-mobile-apps-client/dist/MobileServices.Web.min.js`.  Kopiér denne fil til dit web område.

```
<script src="path/to/MobileServices.Web.min.js"></script>
```

Biblioteket kan også bruges som et ES2015 modul i CommonJS miljøer som Browserify og Webpack og som en AMD bibliotek.  Eksempel:

```
# For ECMAScript 5.1 CommonJS
var WindowsAzure = require('azure-mobile-apps-client');
# For ES2015 modules
import * as WindowsAzure from 'azure-mobile-apps-client';
```

[AZURE.INCLUDE [app-service-mobile-html-js-library](../../includes/app-service-mobile-html-js-library.md)]

##<a name="auth"></a>Sådan: godkende brugere

Azure App Service understøtter kontrollere og godkende app brugere ved hjælp af forskellige eksterne identitetsudbydere: Facebook, Google, Microsoft-Account og Twitter. Du kan angive tilladelser på tabeller for at begrænse adgangen til bestemte operationer til kun godkendte brugere. Du kan også bruge identiteten af godkendte brugere for at implementere reglerne i serverscripts. Du kan finde yderligere oplysninger finder selvstudiet [Introduktion til godkendelse] .

To godkendelse flyder understøttes: et server flow og en klient flow.  Server strømmen yder den nemmeste godkendelse oplevelse, som den er afhængig af brugergrænsefladen udbyderens internettet godkendelse. Klient strømmen giver mulighed for bedre integration med enhed-specifikke funktioner såsom single-sign-on som den er afhængig af providerspecifikke SDK'er.

[AZURE.INCLUDE [app-service-mobile-html-js-auth-library](../../includes/app-service-mobile-html-js-auth-library.md)]

###<a name="configure-external-redirect-urls"></a>Sådan: konfigurere din Mobile-App-tjeneste til eksterne Omdiriger URL-adresser.

Flere typer JavaScript programmer bruge en tilbagekobling funktion til at håndtere OAuth UI flyder.  Disse funktioner omfatter:

* Kører din tjeneste lokalt
* Ved hjælp af direkte Genindlæs med ioniseret Framework
* Omdirigere til App-tjenesten til godkendelse. 

Kører lokalt kan medføre problemer, fordi som standard App Service godkendelse er kun konfigureret til at tillade adgang fra din Mobile-App backend-version. Brug følgende trin til at ændre indstillingerne for App-tjenesten for at aktivere godkendelse, mens du kører server lokalt:

1. Log på [Azure-portalen]
2. Gå til din Mobile-App backend-version.
3. Vælg **ressource explorer** i menuen **Værktøjer til udvikling af** .
4. Klik på **Gå** for at åbne ressource explorer til din Mobile-App back end-i en ny fane eller et vindue.
5. Udvid **config** > **authsettings** node for din app.
6. Klik på knappen **Rediger** for at aktivere redigering af ressourcen.
7. Find det element, **allowedExternalRedirectUrls** , der skal være null. Føje din URL-adresser i en matrix:

         "allowedExternalRedirectUrls": [
             "http://localhost:3000",
             "https://localhost:3000"
         ],

    Erstatte URL-adresser i matrixen med URL-adresserne på din tjeneste, som i dette eksempel er `http://localhost:3000` for den lokale Node.js eksempel-tjeneste. Du kan også bruge `http://localhost:4400` for tjenesten Ripple eller nogle andre URL-adresse, afhængigt af hvordan din app er konfigureret.

8. Øverst på siden, skal du klikke på **Læse-og skriveadgang**, og klik derefter på **PLACER** for at gemme dine opdateringer.

Du skal også føje de samme tilbagekobling URL-adresser til CORS whitelist indstillinger:

1. Gå tilbage til [Azure-portalen].
2. Gå til din Mobile-App backend-version.
3. Klik på **CORS** i menuen **API** .
4. Angiv hver URL-adressen i tekstfeltet tomme **Tilladt baggrunden** .  Der oprettes et nyt tekstfelt.
5. Klik på **Gem**
    
Når backend-version er opdateret, vil du kunne bruge de nye tilbagekobling URL-adresser i din app.

<!-- URLs. -->
[Azure Mobile Apps Hurtig Start]: app-service-mobile-cordova-get-started.md
[Introduktion til godkendelse]: app-service-mobile-cordova-get-started-users.md
[Add authentication to your app]: app-service-mobile-cordova-get-started-users.md

[Azure-portalen]: https://portal.azure.com/
[JavaScript SDK til Azure-Mobilapps]: https://www.npmjs.com/package/azure-mobile-apps-client
[Query object documentation]: https://msdn.microsoft.com/en-us/library/azure/jj613353.aspx

