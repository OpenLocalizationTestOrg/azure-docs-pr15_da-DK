<properties
    pageTitle="Azure AD-Cordova Introduktion | Microsoft Azure"
    description="Sådan oprettes en Cordova-program, der integreres med Azure AD til logon og Azure AD-opkald beskyttet API'er ved hjælp af OAuth."
    services="active-directory"
    documentationCenter=""
    authors="vibronet"
    manager="mbaldwin"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="javascript"
    ms.topic="article"
    ms.date="09/16/2016"
    ms.author="vittorib"/>

# <a name="integrate-azure-ad-with-an-apache-cordova-app"></a>Integrere Azure AD med en Apache Cordova app

[AZURE.INCLUDE [active-directory-devquickstarts-switcher](../../includes/active-directory-devquickstarts-switcher.md)]

[AZURE.INCLUDE [active-directory-devguide](../../includes/active-directory-devguide.md)]

Apache Cordova gør det muligt for dig at udvikle HTML5/JavaScript-programmer, som kan køre på mobilenheder som færdiglavet oprindelige programmer.
Med Azure AD, kan du føje grade godkendelse virksomhedsfunktioner til dine Cordova-programmer. Du kan forbedre dit program til at understøtte Log på med dine brugeres AD konti, få adgang til Office 365 og Azure API og endda Beskyt opkald til din egen brugerdefinerede Web API sammen takket være et Cordova plug-in ombryde Azure AD oprindelige SDK'er på iOS, Android, Windows Store og Windows Phone.

I dette selvstudium bruger vi Apache Cordova plug-in'et til Active Directory Authentication Library (ADAL) til at forbedre en simpel app med følgende funktioner:

-   Godkende en AD-bruger, og få et token for opkald til Azure AD Graph API med ganske få kodelinjer.
-   Brug dette token til at kalde API'EN Graph for at forespørge denne mappe og få vist resultaterne  
-   Udnytte ADAL token cachen til minimering godkendelse instruktionerne til brugeren.

Før du gør dette, skal du:

2. Registrere et program med Azure AD
2. Føj kode til din app til anmodning om tokens
3. Tilføj kode for at bruge tokenet til at forespørge Graph API og få vist resultaterne.
4. Opret Cordova installation projektet med alle platforme, du vil målrette og Cordova ADAL plug-in'et og test løsningen i emulatorer.

## <a name="0--prerequisites"></a>*0. forudsætninger for*

Til at udføre dette selvstudium, skal du:

- En Azure AD-lejer, hvor du har en konto med rettigheder til udvikling af app
- Et udviklingsmiljø, der er konfigureret til at bruge Apache Cordova  

Hvis du har både allerede konfigurere, skal du fortsætte direkte til trin 1.

Hvis du ikke har en Azure AD-lejer, kan du finde [vejledning i at få en her](active-directory-howto-tenant.md).

Hvis du ikke har Apache Cordova konfigurere på din computer, skal du installere følgende:

- [Ciffer](http://git-scm.com/book/en/v2/Getting-Started-Installing-Git)
- [NodeJS](https://nodejs.org/download/)
- [Cordova CLI](https://cordova.apache.org/) (nemt kan installeres via NPM pakke manager: `npm install -g cordova`)

Bemærk, at de skal fungerer både på PC'EN og til Mac.

Hver destinationsadresse-platform har forskellige forudsætninger.

- At oprette og køre Windows-Tablet PC eller Phone app-version
    - [Visual Studio 2013 til Windows med opdatering 2 eller nyere](http://www.visualstudio.com/downloads/download-visual-studio-vs#d-express-windows-8) (Express eller en anden version).
- Oprette og køre til iOS
    -   Xcode 5.x eller nyere. Kan du hente det på http://developer.apple.com/downloads eller [Mac App Store](http://itunes.apple.com/us/app/xcode/id497799835?mt=12)
    -   [ios-sim](https://www.npmjs.org/package/ios-sim) – gør det muligt at starte iOS apps i iOS Simulator fra kommandolinjen (nemt kan installeres via terminalen: `npm install -g ios-sim`)

- Oprette og køre programmet til Android
    - Installere [Java Development Kit (JDK) 7](http://www.oracle.com/technetwork/java/javase/downloads/jdk7-downloads-1880260.html) eller nyere. Sørg for, at `JAVA_HOME` (miljø Variable) er korrekt sat ifølge JDK installationssti (for eksempel C:\Program Files\Java\jdk1.7.0_75).
    - Installere [Android SDK](http://developer.android.com/sdk/installing/index.html?pkg=tools) og tilføje `<android-sdk-location>\tools` placering (for eksempel C:\tools\Android\android-sdk\tools) til din `PATH` miljø variabel.
    - Åbn Android SDK Manager (for eksempel via terminal: `android`) og installere
    - *Android 5.0.1 (API 21)* platform SDK
    - *Android SDK Build-værktøjer* version 19.1.0 eller nyere
    - *Understøttelse af Android lager* (Extras)

  Android sdk indeholder ikke en hvilken som helst emulator standardforekomst. Oprette en ny ved at køre `android avd` fra terminal og derefter vælge *Opret...* , hvis du vil køre Android-appen på emulator. Anbefalede *Api niveau* er 19 eller højere, se [AVD leder] (http://developer.android.com/tools/help/avd-manager.html) kan finde flere oplysninger om Android emulator og oprettelse af indstillinger.


## <a name="1--register-an-application-with-azure-ad"></a>*1. registrere et program med Azure AD*

Bemærk: dette __trin er valgfrit__. Selvstudiet angivet allerede klargjort værdier, der gør det muligt at se eksemplet i aktion uden at gøre en hvilken som helst klargøring i din egen lejer. Det anbefales dog, du udføre dette trin, og Bliv fortrolig med processen, som den er påkrævet, når du vil oprette dine egne programmer.

Azure AD udsender kun tokens til kendte programmer. Før du kan bruge Azure AD fra din app, skal du oprette en post til den i din lejer.  Til at registrere et nyt program i din lejer

-   Log på [administrationsportalen til af Azure](https://manage.windowsazure.com)
-   Klik på **Active Directory** i til venstre i navigationen
-   Vælg den lejer, hvor du vil registrere programmet.
-   Klik på fanen **programmer** , og klik på **Tilføj** i den nederste skuffe.
-   Følg anvisningerne, og oprette et nyt **Oprindelige klientprogrammet** (, Cordova apps er HTML-baseret trods vi opretter oprindelige klientprogrammet her så `Native Client Application` indstilling skal være markeret, ellers fungerer programmet ikke).
    -   **Navnet** på programmet, som beskriver dit program for slutbrugere
    -   **Omdirigere URI** er URI bruges til at returnere tokens til din app. Angiv `http://MyDirectorySearcherApp`.

Når du har færdiggjort registrering, Tildel AAD din app et entydigt klient-id.  Du skal bruge denne værdi i de næste afsnit: Du kan finde den på fanen **Konfigurer** i det nyoprettede app.

For at køre `DirSearchClient Sample`, give tilladelse til at forespørge _Azure AD Graph API_nyoprettede app:
-   Find sektionen "Tilladelser til andre programmer" **Konfigurer** under fanen.  Tilføj adgangstilladelse **mappe som den bruger, der er logget på** under **Delegerede tilladelser**til programmet "Azure Active Directory".  Dette vil gøre det muligt at forespørge Graph API til brugere.

## <a name="2-clone-the-sample-app-repository-required-for-the-tutorial"></a>*2. Klon eksempel app lager kræves til selvstudiet*

Fra din shell eller kommandolinjen, skal du skrive følgende kommando:

    git clone -b skeleton https://github.com/AzureADQuickStarts/NativeClient-MultiTarget-Cordova.git

## <a name="3-create-the-cordova-app"></a>*3. Opret Cordova-app*

Der findes flere måder at oprette Cordova programmer. I dette selvstudium bruger vi grænsefladen Cordova kommandoen linje (CLI).
Fra din shell eller kommandolinjen, skal du skrive følgende kommando:


     cordova create DirSearchClient --copy-from="NativeClient-MultiTarget-Cordova/DirSearchClient"

Der opretter mappestrukturen og stilladser for Cordova projektet, kopierer indholdet af projektet starter i undermappen www.
Flytte til den nye DirSearchClient-mappe.

    cd .\DirSearchClient

Tilføje et whitelist plug-in, det er nødvendigt til aktivering Graph API.

     cordova plugin add cordova-plugin-whitelist

Dernæst skal tilføje alle de ønskede til at understøtte platforme. For at få et eksempel på arbejde, skal du udføre mindst én af kommandoerne nedenfor. Bemærk, at du ikke kunne emulere iOS på Windows eller Windows/Windows Phone på en Mac.

    cordova platform add android
    cordova platform add ios
    cordova platform add windows

Til sidst, kan du tilføje ADAL for Cordova plug-in til projektet.

    cordova plugin add cordova-plugin-ms-adal

## <a name="3-add-code-to-authenticate-users-and-obtain-tokens-from-aad"></a>*3. Tilføj kode for at godkende brugere og indhente tokens fra AAD*

Det program, du udvikler i dette selvstudium give en bare ben directory søgefunktionen, hvor brugeren kan skrive alias for alle brugere i kataloget og visualisere nogle grundlæggende attributter.  Projektet starter indeholder definitionen af den grundlæggende brugergrænseflade i app (i www/index.html) og stilladser, kabler op grundlæggende app begivenhed F4, bruger interface bindinger og resultater Vis logik (i www/js/index.js). Der er det eneste venstre ydre for dig at tilføje logik implementere identitet opgaver.

Det første, skal du gøre er at introducere i din kode protocol værdierne, der bruges af AAD til at identificere din app og de ressourcer, du tilpasser. Disse værdier bruges til at oprette token anmodninger om senere. Indsætte kodestykke under øverst i filen index.js.

```javascript
    var authority = "https://login.windows.net/common",
    redirectUri = "http://MyDirectorySearcherApp",
    resourceUri = "https://graph.windows.net",
    clientId = "a5d92493-ae5a-4a9f-bcbf-9f1d354067d3",
    graphApiVersion = "2013-11-08";
```

Den `redirectUri` og `clientId` værdier skal svarer til de værdier, der beskriver din app i AAD. Du kan finde dem fra fanen Konfigurer i portalen Azure, som er beskrevet i trin 1 tidligere i dette selvstudium.
Bemærk: Hvis du har valgt til at registrere ikke en ny app i dit eget lejer, kan du blot indsætte forudkonfigurerede værdierne ovenfor er -, kan du se køre eksempel, selvom du bør altid oprette din egen indtastning for dine apps, der er beregnet til fremstilling.

Næste, har vi brug at tilføje den faktiske token anmodning kode. Indsæt følgende kodestykke mellem den `search `og `renderdata `definitioner.

```javascript
    // Shows user authentication dialog if required.
    authenticate: function (authCompletedCallback) {

        app.context = new Microsoft.ADAL.AuthenticationContext(authority);
        app.context.tokenCache.readItems().then(function (items) {
            if (items.length > 0) {
                authority = items[0].authority;
                app.context = new Microsoft.ADAL.AuthenticationContext(authority);
            }
            // Attempt to authorize user silently
            app.context.acquireTokenSilentAsync(resourceUri, clientId)
            .then(authCompletedCallback, function () {
                // We require user credentials so triggers authentication dialog
                app.context.acquireTokenAsync(resourceUri, clientId, redirectUri)
                .then(authCompletedCallback, function (err) {
                    app.error("Failed to authenticate: " + err);
                });
            });
        });

    },
```
Lad os undersøge, der fungerer med opdele den i dens to hoveddele.
Dette eksempel er udviklet til at arbejde med en hvilken som helst lejeradministration, i modsætning for at være bundet til et bestemt. Det bruger "/ almindelige" slutpunktet, som gør det muligt for brugeren at indtaste en konto på godkendelse af tid og dirigerer anmodningen lejeren den tilhører.
Denne første del af metoden inspicerer ADAL cachen at se, om der er allerede et lagrede token - og hvis der er den bruger lejere den stammer fra til igen initialisering af ADAL. Det er nødvendigt for at undgå ekstra anvisningerne, som brug af "/ almindelige" altid resulterer i beder brugeren om at angive en ny konto.
```javascript
        app.context = new Microsoft.ADAL.AuthenticationContext(authority);
        app.context.tokenCache.readItems().then(function (items) {
            if (items.length > 0) {
                authority = items[0].authority;
                app.context = new Microsoft.ADAL.AuthenticationContext(authority);
            }
```
Den anden del af metoden udfører stort tokewn anmodningen.
Den `acquireTokenSilentAsync` metode beder om at ADAL til at returnere et token for den angivne ressource uden at vise en hvilken som helst elevationsmeddelelse Der kan forekomme, hvis cachen allerede har en passende adgangstoken, der er gemt, eller hvis der vises en Opdater-token, som kan bruges til at få en ny adgangstoken uden shwoing en hvilken som helst prompt.
Hvis, forsøger mislykkes, vi gå tilbage `acquireTokenAsync` -som synligt spørger brugeren til at godkende.
```javascript
            // Attempt to authorize user silently
            app.context.acquireTokenSilentAsync(resourceUri, clientId)
            .then(authCompletedCallback, function () {
                // We require user credentials so triggers authentication dialog
                app.context.acquireTokenAsync(resourceUri, clientId, redirectUri)
                .then(authCompletedCallback, function (err) {
                    app.error("Failed to authenticate: " + err);
                });
            });
```
Nu hvor vi har tokenet, kan vi endelig kalde Graph API og udføre søgestrengen vi vil. Indsæt følgende kodestykke lige under det `authenticate` definition.

```javascript
// Makes Api call to receive user list.
    requestData: function (authResult, searchText) {
        var req = new XMLHttpRequest();
        var url = resourceUri + "/" + authResult.tenantId + "/users?api-version=" + graphApiVersion;
        url = searchText ? url + "&$filter=mailNickname eq '" + searchText + "'" : url + "&$top=10";

        req.open("GET", url, true);
        req.setRequestHeader('Authorization', 'Bearer ' + authResult.accessToken);

        req.onload = function(e) {
            if (e.target.status >= 200 && e.target.status < 300) {
                app.renderData(JSON.parse(e.target.response));
                return;
            }
            app.error('Data request failed: ' + e.target.response);
        };
        req.onerror = function(e) {
            app.error('Data request failed: ' + e.error);
        }

        req.send();
    },

```
De første punkt filer leveret barebone UX for at angive en brugers alias i en tekstboks. Denne metode bruger denne værdi til at oprette en forespørgsel, kombineres med adgangstoken, sende det til diagrammet og fortolke resultaterne. Metoden renderData, allerede findes i filen første punkt klarer resultatet skal visualiseres.

## <a name="4-run"></a>*4. Kør*
Din app er endelig klar til at køre! Operativsystem det er meget simpel: Når programmet starter, Skriv i tekstboksen brugeralias på den bruger, du vil slå op - og derefter klikke på knappen. Du bliver bedt om til godkendelse. Når du vellykket bekræftelse og søgning vises attributterne for gennemsøgt brugeren. Efterfølgende kører udføre søgningen uden at vise en hvilken som helst prompt, sammen takket være tilstedeværelse i cachen af tokenet tidligere har købt.
Cement trinnene til at køre appen varierer afhængigt af platform.

####<a name="windows-10"></a>Windows 10:

   Tablet PC:`cordova run windows --archs=x64 -- --appx=uap`

   Mobil (kræver Windows10 Mobile enhed har forbindelse til PC):`cordova run windows --archs=arm -- --appx=uap --phone`

   __Bemærk__: under den første kørsel, kan du blive bedt om at logge på i en udviklerlicens. Du kan finde flere oplysninger i [udviklerlicens](https://msdn.microsoft.com/library/windows/apps/hh974578.aspx) .

####<a name="windows-81-tabletpc"></a>Windows 8.1 Tablet/PC:

   `cordova run windows`

   __Bemærk__: under den første kørsel, kan du blive bedt om at logge på i en udviklerlicens. Du kan finde flere oplysninger i [udviklerlicens](https://msdn.microsoft.com/library/windows/apps/hh974578.aspx) .

####<a name="windows-phone-81"></a>Windows Phone 8.1:

   Sådan køres på forbundne enhed:`cordova run windows --device -- --phone`

   Sådan køres på standard emulator:`cordova emulate windows -- --phone`

   Brug `cordova run windows --list -- --phone` at se alle tilgængelige destinationer og `cordova run windows --target=<target_name> -- --phone` til at køre programmet på bestemte enhed eller emulator (for eksempel `cordova run windows --target="Emulator 8.1 720P 4.7 inch" -- --phone`).

####<a name="android"></a>Android:

   Sådan køres på forbundne enhed:`cordova run android --device`

   Sådan køres på standard emulator:`cordova emulate android`

   __Bemærk__: Sørg for, at du har oprettet emulator forekomst ved hjælp af *AVD Manager* , som det er vist i afsnittet *forudsætninger* .

   Brug `cordova run android --list` at se alle tilgængelige destinationer og `cordova run android --target=<target_name>` til at køre programmet på bestemte enhed eller emulator (for eksempel `cordova run android --target="Nexus4_emulator"`).

####<a name="ios"></a>iOS:

   Sådan køres på forbundne enhed:`cordova run ios --device`

   Sådan køres på standard emulator:`cordova emulate ios`

   __Bemærk__: Sørg for, at du har `ios-sim` pakke installeret for at køre på emulator. Se *forudsætninger* afsnittet for at få mere at vide.

    Use `cordova run ios --list` to see all available targets and `cordova run ios --target=<target_name>` to run application on specific device or emulator (for example,  `cordova run android --target="iPhone-6"`).

Brug `cordova run --help` til at se yderligere opbygge og køre indstillinger.

Til reference gennemført prøveoverførsel (uden din konfigurationsværdier) er angivet [her](https://github.com/AzureADQuickStarts/NativeClient-MultiTarget-Cordova/tree/complete/DirSearchClient).  Du kan nu gå videre til mere avancerede (ok, og mere interessant) scenarier.  Vil du måske at prøve:

[Sikre en Node.js Web API med Azure AD >>](active-directory-devquickstarts-webapi-nodejs.md)

[AZURE.INCLUDE [active-directory-devquickstarts-additional-resources](../../includes/active-directory-devquickstarts-additional-resources.md)]
