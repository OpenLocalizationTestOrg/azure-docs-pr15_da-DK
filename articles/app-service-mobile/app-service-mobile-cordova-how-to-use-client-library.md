<properties
    pageTitle="Sådan bruger du Apache Cordova plug-in til Azure-Mobilapps"
    description="Sådan bruger du Apache Cordova plug-in til Azure-Mobilapps"
    services="app-service\mobile"
    documentationCenter="javascript"
    authors="adrianhall"
    manager="erikre"
    editor=""/>

<tags
    ms.service="app-service-mobile"
    ms.workload="mobile"
    ms.tgt_pltfrm="mobile-html"
    ms.devlang="javascript"
    ms.topic="article"
    ms.date="10/01/2016"
    ms.author="adrianha"/>

# <a name="how-to-use-apache-cordova-client-library-for-azure-mobile-apps"></a>Sådan bruger du Apache Cordova klient bibliotek til Azure-Mobilapps

[AZURE.INCLUDE [app-service-mobile-selector-client-library](../../includes/app-service-mobile-selector-client-library.md)]

Denne vejledning lærer du at udføre almindelige scenarier, der bruger den seneste [Apache Cordova plug-in til Azure Mobile-Apps]. Hvis du er ny bruger af Azure Mobile-Apps, første fuldført [Azure Mobile Apps Hurtig Start] til at oprette en back-end, oprette en tabel, og Hent et færdigbyggede Apache Cordova projekt. I denne vejledning der, fokuserer vi på klientsiden Apache Cordova plug-in'et.

## <a name="supported-platforms"></a>Understøttede platforme

Dette SDK understøtter Apache Cordova v6.0.0 og nyere på iOS, Android og Windows enheder.  Platform support er som følger:

* Android API 19-24 (kitkat-operativsystem gennem Nougat)
* iOS 8.0 og senere versioner.
* Windows Phone 8.0
* Windows Phone 8.1
* Universal Windows-Platform

##<a name="Setup"></a>Installation og forudsætninger

Denne vejledning forudsætter, at du har oprettet en back-end med en tabel. Denne vejledning forudsætter, at tabellen har det samme skema som tabellerne i disse selvstudier. Denne vejledning antages det også, at du har føjet Apache Cordova plug-in'et til din kode.  Hvis du ikke har gjort det, kan du tilføje Apache Cordova plug-in'et til dit projekt på kommandolinjen:

```
cordova plugin add cordova-plugin-ms-azure-mobile-apps
```

Du kan finde flere oplysninger om at oprette [din første Apache Cordova-app], deres dokumentation.

[AZURE.INCLUDE [app-service-mobile-html-js-library.md](../../includes/app-service-mobile-html-js-library.md)]


##<a name="auth"></a>Sådan: godkende brugere

Azure App Service understøtter kontrollere og godkende app brugere ved hjælp af forskellige eksterne identitetsudbydere: Facebook, Google, Microsoft-Account og Twitter. Du kan angive tilladelser på tabeller for at begrænse adgangen til bestemte operationer til kun godkendte brugere. Du kan også bruge identiteten af godkendte brugere for at implementere reglerne i serverscripts. Du kan finde yderligere oplysninger finder selvstudiet [Introduktion til godkendelse] .

Når du bruger godkendelse i en Apache Cordova-app, skal følgende Cordova plug-ins være tilgængelige:

* [cordova-plug-in-enhed]
* [cordova-plug-in-inappbrowser]

To godkendelse flyder understøttes: et server flow og en klient flow.  Server strømmen yder den nemmeste godkendelse oplevelse, som den er afhængig af brugergrænsefladen udbyderens internettet godkendelse. Klient strømmen giver mulighed for bedre integration med enhed-specifikke funktioner såsom single-sign-on som den er afhængig af providerspecifikke enhed-specifikke SDK'er.

[AZURE.INCLUDE [app-service-mobile-html-js-auth-library.md](../../includes/app-service-mobile-html-js-auth-library.md)]

###<a name="configure-external-redirect-urls"></a>Sådan: konfigurere din Mobile-App-tjeneste til eksterne Omdiriger URL-adresser.

Flere typer Apache Cordova programmer bruge en tilbagekobling funktion til at håndtere OAuth UI flyder.  OAuth UI flyder på localhost medføre problemer, da godkendelsestjenesten kun ved, hvordan du kan udnytte din tjeneste som standard.  Eksempler på problematisk OAuth UI flyder omfatter:

- Ripple emulatoren.
- Live Genindlæs med ioniseret.
- Kører mobile back-end lokalt
- Køre den mobile backend-version i en anden Azure App tjeneste end én at give godkendelse.

Følg disse instruktioner for at føje din lokale indstillinger til konfigurationen:

1. Log på [Azure-portalen]
2. Vælg **alle de ressourcer** eller **App tjenester** , og klik derefter på navnet på din Mobile-App.
3. Klik på **Funktioner**
4. Klik på **ressourcen Stifinder** i menuen OBSERVE, og klik derefter på **Gå**.  Et nyt vindue eller åbnes.
5. Udvid **config**, **authsettings** noder for webstedet på navigationslinjen til venstre.
6. Klik på **Rediger**
7. Se efter "allowedExternalRedirectUrls" element.  Det kan være angivet til null eller en matrix med værdier.  Ændre værdien til følgende værdi:

         "allowedExternalRedirectUrls": [
             "http://localhost:3000",
             "https://localhost:3000"
         ],

    Erstat URL-adresserne med URL-adresserne på din tjeneste.  Som eksempler kan nævnes "http://localhost:3000" (for tjenesten Node.js eksempel), eller "http://localhost:4400" (for tjenesten Ripple).  Disse URL-adresser er dog eksempler - din situation, herunder til de tjenester, der er nævnt i eksemplerne, kan være forskellige.
8. Klik på knappen **Læse-og skriveadgang** i øverste højre hjørne af skærmen.
9. Klik på grønne **PLACERE** knappen.

Indstillingerne gemmes på dette tidspunkt.  Ikke lukker browservinduet indtil er færdig med indstillingerne for lagring af.
Også føje disse tilbagekobling URL-adresser til CORS indstillingerne for din App Service:

1. Log på [Azure-portalen]
2. Vælg **alle de ressourcer** eller **App tjenester** , og klik derefter på navnet på din Mobile-App.
3. Bladet indstillinger åbnes automatisk.  Hvis det ikke er tilfældet, skal du klikke på **Alle indstillinger**.
4. Klik på **CORS** under menuen API.
5. Skriv URL-adressen, som du vil tilføje i feltet, og tryk Enter.
6. Angiv yderligere URL-adresser, som det er nødvendigt.
7. Klik på **Gem** for at gemme indstillingerne.

Det tager cirka 10-15 sekunder, før de nye indstillinger træder i kraft.

##<a name="register-for-push"></a>Sådan: Tilmeld dig Pushmeddelelser

Installere [phonegap-plug-in-push] for at håndtere pushmeddelelser.  Denne plug-in kan nemt tilføjes ved hjælp af den `cordova plugin add` kommando på kommandolinjen eller via ciffer plug-in installationsprogrammet i Visual Studio.  Følgende kode i din Apache Cordova app registrerer enheden til pushbeskeder:

```
var pushOptions = {
    android: {
        senderId: '<from-gcm-console>'
    },
    ios: {
        alert: true,
        badge: true,
        sound: true
    },
    windows: {
    }
};
pushHandler = PushNotification.init(pushOptions);

pushHandler.on('registration', function (data) {
    registrationId = data.registrationId;
    // For cross-platform, you can use the device plugin to determine the device
    // Best is to use device.platform
    var name = 'gcm'; // For android - default
    if (device.platform.toLowerCase() === 'ios')
        name = 'apns';
    if (device.platform.toLowerCase().substring(0, 3) === 'win')
        name = 'wns';
    client.push.register(name, registrationId);
});

pushHandler.on('notification', function (data) {
    // data is an object and is whatever is sent by the PNS - check the format
    // for your particular PNS
});

pushHandler.on('error', function (error) {
    // Handle errors
});
```

Brug af besked om Hubs SDK til at sende pushmeddelelser fra serveren.  Send aldrig pushmeddelelser direkte fra klienter. Hvis du gør det kan bruges til at udløse manglende adgang til service-angreb på meddelelse hubber eller PNS.  PNS kunne forbyde din trafik som et resultat af aflytning.

<!-- URLs. -->
[Azure-portalen]: https://portal.azure.com
[Azure Mobile Apps Hurtig Start]: app-service-mobile-cordova-get-started.md
[Introduktion til godkendelse]: app-service-mobile-cordova-get-started-users.md
[Add authentication to your app]: app-service-mobile-cordova-get-started-users.md

[Apache Cordova plug-in til Azure-Mobilapps]: https://www.npmjs.com/package/cordova-plugin-ms-azure-mobile-apps
[din første Apache Cordova-app]: http://cordova.apache.org/#getstarted
[phonegap-facebook-plugin]: https://github.com/wizcorp/phonegap-facebook-plugin
[phonegap-plug-in-opslagsnål]: https://www.npmjs.com/package/phonegap-plugin-push
[cordova-plug-in-enhed]: https://www.npmjs.com/package/cordova-plugin-device
[cordova-plug-in-inappbrowser]: https://www.npmjs.com/package/cordova-plugin-inappbrowser
[Query object documentation]: https://msdn.microsoft.com/en-us/library/azure/jj613353.aspx
