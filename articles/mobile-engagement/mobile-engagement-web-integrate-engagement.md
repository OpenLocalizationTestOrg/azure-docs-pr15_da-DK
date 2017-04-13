<properties
    pageTitle="Azure Mobile aftale Web SDK integration | Microsoft Azure"
    description="De seneste opdateringer og procedurer til Azure Mobile aftale Web SDK"
    services="mobile-engagement"
    documentationCenter="mobile"
    authors="piyushjo"
    manager="erikre"
    editor="" />

<tags
    ms.service="mobile-engagement"
    ms.workload="mobile"
    ms.tgt_pltfrm="web"
    ms.devlang="js"
    ms.topic="article"
    ms.date="02/29/2016"
    ms.author="piyushjo" />

#<a name="integrate-azure-mobile-engagement-in-a-web-application"></a>Integrere Azure Mobile aftale i et webprogram

> [AZURE.SELECTOR]
- [Windows Universal](mobile-engagement-windows-store-integrate-engagement.md)
- [Windows Phone Silverlight](mobile-engagement-windows-phone-integrate-engagement.md)
- [iOS](mobile-engagement-ios-integrate-engagement.md)
- [Android](mobile-engagement-android-integrate-engagement.md)

Procedurerne i denne artikel beskriver den nemmeste måde at aktivere analyser og overvågning funktioner i Azure Mobile aftale i din webprogram.

Følg trinnene for at aktivere de log rapporter, der skal bruges til at beregne alle statistik om brugere, sessioner, aktiviteter, går ned og technicals. For program-afhængige statistik, som begivenheder, fejl og job, skal du aktivere log rapporter ved hjælp af Azure Mobile aftale API manuelt. Få mere at vide Lær [at bruge de avancerede Mobile aftale mærkning API i et webprogram](mobile-engagement-web-use-engagement-api.md).

## <a name="introduction"></a>Introduktion

[Hente Azure Mobile aftale internettet SDK](http://aka.ms/P7b453).
Mobile aftale Web SDK leveres som en enkelt JavaScript-fil, azure-engagement.js, som du skal medtages i alle sider på dit websted eller web-program.

> [AZURE.IMPORTANT] Før du kører denne script, skal du køre en script eller kode kodestykke, du skriver konfigurere Mobile aftale til dit program.

## <a name="browser-compatibility"></a>Webbrowserkompatibilitet

Mobile aftale Web SDK bruger oprindelige JSON kodningen og kodning, ud over tværs af domæner AJAX-anmodninger (stole på specifikationen W3C CORS). Det er kompatibelt med følgende browsere:

* Microsoft Edge 12 +
* Internet Explorer 10 +
* Firefox 3.5 +
* Chrome 4 +
* Safari 6 +
* Opera 12 +

## <a name="configure-mobile-engagement"></a>Konfigurere Mobile aftale

Oprette et script, som opretter en global `azureEngagement` JavaScript-objekt, som i følgende eksempel. Dette eksempel forudsætter, at dette script er inkluderet i hver side, fordi dit websted kan have multipla sider. I dette eksempel objektet JavaScript hedder `azure-engagement-conf.js`.

    window.azureEngagement = {
      connectionString: 'Endpoint={appCollection}.{domain};AppId={appId};SdkKey={sdkKey}',
      appVersionName: '1.0.0',
      appVersionCode: 1
    };

Den `connectionString` værdi for dit program vises i portalen Azure.

> [AZURE.NOTE] `appVersionName`og `appVersionCode` er valgfrit. Men, anbefales det, at du konfigurerer dem, så analytics kan behandle versionsoplysninger.

## <a name="include-mobile-engagement-scripts-in-your-pages"></a>Medtage Mobile aftale scripts i dine sider
Føje Mobile aftale scripts til dine sider i en af følgende måder:

    <head>
      ...
      <script type="text/javascript" src="azure-engagement-conf.js"></script>
      <script type="text/javascript" src="azure-engagement.js"></script>
      ...
    </head>

Eller dette:

    <body>
      ...
      <script type="text/javascript" src="azure-engagement-conf.js"></script>
      <script type="text/javascript" src="azure-engagement.js"></script>
      ...
    </body>

## <a name="alias"></a>Alias

Når scriptet Mobile aftale Web SDK er indlæst, opretter **aftale** alias for at få adgang til de SDK API'er. Du kan ikke bruge dette alias for at definere SDK konfigurationen. Dette alias bruges som en reference i denne dokumentation.

Bemærk, at hvis standard alias er i konflikt med en anden globale variabel fra din side, kan du omdefinere det i konfigurationen således før du indlæser Mobile aftale Web SDK:

    window.azureEngagement = {
      connectionString: 'Endpoint={appCollection}.{domain};AppId={appId};SdkKey={sdkKey}',
      appVersionName: '1.0.0',
      appVersionCode: 1
      alias:'anotherAlias'
    };

## <a name="basic-reporting"></a>Grundlæggende rapportering

Grundlæggende rapportering i Mobile aftale dækker session niveau statistik, som statistik om brugere, sessioner, aktiviteter og går ned.

### <a name="session-tracking"></a>Session registrering

En session med Mobile aftale er inddelt i en sekvens af aktiviteter, som hver identificeres ved et navn.

I et klassisk websted anbefaler vi, at du angive en anden aktivitet på alle sider på dit websted. For et websted eller web-program, hvor den aktuelle side aldrig ændringer, vil du spore aktiviteterne, på en mindre skala, f.eks inden for siden.

Uanset hvilken mulighed, hvis du vil starte eller ændre den aktuelle brugeraktivitet skal ringe på `engagement.agent.startActivity` funktionen. Eksempel:

    <body onload="yourOnload()">

    <!-- -->

    yourOnload = function() {
      [...]
      engagement.agent.startActivity('welcome');
    };

Mobile aftale serveren afsluttes automatisk en åben session i tre minutter, efter at programmets side er lukket.

Alternativt kan du kan afslutte en session ved at ringe manuelt `engagement.agent.endActivity`. Dette angiver den aktuelle brugeraktivitet til 'Inaktiv'.  Sessionen afsluttes 10 sekunder senere, medmindre et nyt opkald til `engagement.agent.startActivity` genoptages sessionen.

Du kan konfigurere 10 sekunders forsinkelse i objektet global aftale på følgende måde:

    engagement.sessionTimeout = 2000; // 2 seconds
    // or
    engagement.sessionTimeout = 0; // end the session as soon as endActivity is called

> [AZURE.NOTE] Du kan ikke bruge `engagement.agent.endActivity` i den `onunload` tilbagekald fordi du ikke kan foretage AJAX kald på nuværende tidspunkt.

## <a name="advanced-reporting"></a>Avanceret rapportering

Du kan også, hvis du vil rapportere program-specifikke begivenheder, fejl og job, skal du bruge API'EN Mobile aftale. Du få adgang til Mobile aftale API gennem den `engagement.agent` objekt.

Du kan få adgang til alle de avancerede funktioner i Mobile aftale i Mobile aftale API. API gennemgås i artiklen [Sådan bruges de avancerede Mobile aftale mærkning API i et webprogram](mobile-engagement-web-use-engagement-api.md).

## <a name="customize-the-urls-used-for-ajax-calls"></a>Tilpasse de URL-adresser, der bruges til AJAX-opkald

Du kan tilpasse URL-adresser, der benytter Mobile aftale Web SDK. Hvis du vil omdefinere log URL-adressen (SDK slutpunkt for logføring), kan du tilsidesætte konfigurationen således:

    window.azureEngagement = {
      ...
      urls: {
        ...        
        getLoggerUrl: function() {
        return 'someProxy/log';
        }
      }
    };

Hvis din URL-adressen funktioner returnerer en streng, der begynder med `/`, `//`, `http://`, eller `https://`, standard farveskema bruges ikke. Som standard på `https://` farveskema bruges til disse URL-adresser. Hvis du vil tilpasse standard farveskema, tilsidesætte konfigurationen, således:

    window.azureEngagement = {
      ...
      urls: {
        ...      
        scheme: '//'
      }
    };
