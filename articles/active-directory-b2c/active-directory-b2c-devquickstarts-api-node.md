<properties
    pageTitle="Azure AD-B2C: Secure web API ved hjælp af Node.js | Microsoft Azure"
    description="Sådan oprettes en Node.js web API, der accepterer tokens ud fra en B2C lejer"
    services="active-directory-b2c"
    documentationCenter=""
    authors="brandwe"
    manager="mbaldwin"
    editor=""/>

<tags
    ms.service="active-directory-b2c"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="javascript"
    ms.topic="hero-article"
    ms.date="08/30/2016"
    ms.author="brandwe"/>

# <a name="azure-ad-b2c-secure-a-web-api-by-using-nodejs"></a>Azure AD-B2C: Secure web API ved hjælp af Node.js

<!-- TODO [AZURE.INCLUDE [active-directory-b2c-devquickstarts-web-switcher](../../includes/active-directory-b2c-devquickstarts-web-switcher.md)]-->

Du kan sikre en web API ved hjælp af OAuth 2.0 access tokens med Azure Active Directory (Azure AD) B2C. Disse tokens Tillad, at din klient-apps, der bruger Azure AD B2C til at godkende API. I denne artikel beskrives, hvordan du opretter en "opgaveliste" API, så brugerne kan tilføje og få vist opgaver. World Wide web API er beskyttet med Azure AD B2C og tillader kun godkendte brugere for at administrere deres opgaveliste.

> [AZURE.NOTE]  Dette eksempel blev skrevet have forbindelse til ved hjælp af vores [iOS B2C Northwind](active-directory-b2c-devquickstarts-ios.md). Gøre den aktuelle gennemgå først, og derefter følge dette eksempel.

**Passport** er godkendelse programmer til Node.js. Fleksibel og modulær, Passport ubemærket kan installeres i en Express-baserede eller Restify webprogram. Et omfattende sæt af strategier understøtter godkendelse ved hjælp af et brugernavn og adgangskode, Facebook, Twitter og mere. Vi har udviklet en strategi for Azure Active Directory (Azure AD). Du kan installere dette modul og derefter tilføjer Azure AD `passport-azure-ad` plug-in'en.

Hvis du vil gøre dette eksempel, skal du:

1. Registrere et program med Azure AD.
2. Konfigurere dit program tilladelse til at bruge Passports `azure-ad-passport` plug-in'en.
3. Konfigurere en klientprogrammet til at ringe til "opgaveliste" web API.


## <a name="get-an-azure-ad-b2c-directory"></a>Få et Azure AD B2C directory

Før du kan bruge Azure AD B2C, skal du oprette en mappe eller lejer.  En mappe er en beholder for alle brugere, apps, grupper og meget mere.  Hvis du ikke har en allerede, fortsætter [oprette en B2C-mappe](active-directory-b2c-get-started.md) , inden du.

## <a name="create-an-application"></a>Oprette et program

Derefter skal du oprette en app i dit B2C-mappe, der giver Azure AD nogle oplysninger om, at opgaven skal sikkert kommunikere med din app. I dette tilfælde er både klient app og web API repræsenteret af et enkelt **Program-ID**, fordi de indeholder én logiske app. Hvis du vil oprette en app, skal du følge [disse instruktioner](active-directory-b2c-app-registration.md). Sørg for at:

- Medtage et **web app/web api** i programmet på computeren
- Angiv `http://localhost/TodoListService` som en **URL-adresse til svar**. Det er standard URL-adressen for denne eksempel-kode.
- Oprette et **program hemmeligt** for dit program og kopiere den. Du skal bruge disse data senere. Bemærk, at denne værdi skal være [XML escape-tegn](https://www.w3.org/TR/2006/REC-xml11-20060816/#dt-escape) , før du bruge den.
- Kopiere **Program-ID** , der er tildelt din app. Du skal bruge disse data senere.

[AZURE.INCLUDE [active-directory-b2c-devquickstarts-v2-apps](../../includes/active-directory-b2c-devquickstarts-v2-apps.md)]

## <a name="create-your-policies"></a>Oprette din politikker

I Azure AD B2C defineres hver brugeroplevelsen af en [politik](active-directory-b2c-reference-policies.md). Denne app indeholder to identitet oplevelser: Tilmeld dig og logge på. Du skal oprette én politik af hver type, som beskrevet i [politik referenceartikel](active-directory-b2c-reference-policies.md#how-to-create-a-sign-up-policy).  Når du opretter din tre politikker, skal du:

- Vælg det **viste navn** og andre tilmelding attributter i din tilmelding til en politik.
- Vælg de **viste navn** og **Objekt-ID** programmet krav i alle politikker.  Du kan vælge samt andre krav.
- Kopiere **navn** for hver politik ned, når du har oprettet den. Det skal have præfikset `b2c_1_`.  Du skal bruge disse politik navne senere.

[AZURE.INCLUDE [active-directory-b2c-devquickstarts-policy](../../includes/active-directory-b2c-devquickstarts-policy.md)]

Når du har oprettet din tre politikker, er du klar til at opbygge din app.

For at få mere for at vide om, hvordan fungerer politikker i Azure AD B2C, kan du starte med den [.NET web app Introduktion – selvstudium](active-directory-b2c-devquickstarts-web-dotnet.md).

## <a name="download-the-code"></a>Hente koden

Koden for dette selvstudium [vedligeholdes GitHub](https://github.com/AzureADQuickStarts/B2C-WebAPI-NodeJS). For at oprette eksemplet, mens du skriver, kan du [hente et skelet projekt som en .zip-fil](https://github.com/AzureADQuickStarts/B2C-WebAPI-NodeJS/archive/skeleton.zip). Du kan også klone skelet:

```
git clone --branch skeleton https://github.com/AzureADQuickStarts/B2C-WebAPI-NodeJS.git
```

Den færdige app findes også [som en .zip-fil](https://github.com/AzureADQuickStarts/B2C-WebAPI-NodeJS/archive/complete.zip) eller på den `complete` gren i den samme lager.

## <a name="download-nodejs-for-your-platform"></a>Hent Node.js til din platform

For at kunne bruge dette eksempel, skal du en arbejde installation af Node.js. 

Du kan installere Node.js fra [nodejs.org](http://nodejs.org).

## <a name="install-mongodb-for-your-platform"></a>Installere MongoDB til din platform

For at kunne bruge dette eksempel, skal du en arbejde installation af MongoDB. Vi bruger MongoDB til at lave REST-API fast på tværs af forekomster af server.

Du kan installere MongoDB fra [mongodb.org](http://www.mongodb.org).

> [AZURE.NOTE] Denne gennemgang antages, at du bruger standard installations- og server slutpunkterne for MongoDB, som på tidspunktet for denne skrivning er `mongodb://localhost`.

## <a name="install-the-restify-modules-in-your-web-api"></a>Installere Restify moduler på webstedet API

Vi bruger Restify til at opbygge din REST-API. Restify er en minimale og fleksible Node.js programmet ramme, der er afledt af Express. Den har et solidt sæt af funktioner til oprettelse af REST API'er oven på Opret forbindelse.

### <a name="install-restify"></a>Installer Restify

Fra kommandolinjen skal du ændre mappen til `azuread`. Hvis den `azuread` directory ikke findes, skal du oprette den.

`cd azuread`eller`mkdir azuread;`

Skriv følgende kommando:

`npm install restify`

Denne kommando installeres Restify.

#### <a name="did-you-get-an-error"></a>Du får en fejlmeddelelse?

I nogle operativsystemer, når du bruger `npm`, modtager du muligvis fejlen `Error: EPERM, chmod '/usr/local/bin/..'` og en anmodning om, at du kører kontoen, som administrator. Hvis dette problem opstår, kan du bruge den `sudo` kommandoen til at køre `npm` på et højere rettighedsniveau.

#### <a name="did-you-get-a-dtrace-error"></a>Kom fejlen DTrace?

Du kan se noget i retning af denne tekst, når du installerer Restify:

```Shell
clang: error: no such file or directory: 'HD/azuread/node_modules/restify/node_modules/dtrace-provider/libusdt'
make: *** [Release/DTraceProviderBindings.node] Error 1
gyp ERR! build error
gyp ERR! stack Error: `make` failed with exit code: 2
gyp ERR! stack     at ChildProcess.onExit (/usr/local/lib/node_modules/npm/node_modules/node-gyp/lib/build.js:267:23)
gyp ERR! stack     at ChildProcess.EventEmitter.emit (events.js:98:17)
gyp ERR! stack     at Process.ChildProcess._handle.onexit (child_process.js:789:12)
gyp ERR! System Darwin 13.1.0
gyp ERR! command "node" "/usr/local/lib/node_modules/npm/node_modules/node-gyp/bin/node-gyp.js" "rebuild"
gyp ERR! cwd /Volumes/Development HD/azuread/node_modules/restify/node_modules/dtrace-provider
gyp ERR! node -v v0.10.11
gyp ERR! node-gyp -v v0.10.0
gyp ERR! not ok
npm WARN optional dep failed, continuing dtrace-provider@0.2.8
```

Restify giver en effektiv metode til at spore RESTEN opkald ved hjælp af DTrace. Mange operativsystemer har dog ikke DTrace, der er tilgængelige. Du kan ignorere disse fejl.

Output fra kommandoen skal vises ligner denne tekst:

    restify@2.6.1 node_modules/restify
    ├── assert-plus@0.1.4
    ├── once@1.3.0
    ├── deep-equal@0.0.0
    ├── escape-regexp-component@1.0.2
    ├── qs@0.6.5
    ├── tunnel-agent@0.3.0
    ├── keep-alive-agent@0.0.1
    ├── lru-cache@2.3.1
    ├── node-uuid@1.4.0
    ├── negotiator@0.3.0
    ├── mime@1.2.11
    ├── semver@2.2.1
    ├── spdy@1.14.12
    ├── backoff@2.3.0
    ├── formidable@1.0.14
    ├── verror@1.3.6 (extsprintf@1.0.2)
    ├── csv@0.3.6
    ├── http-signature@0.10.0 (assert-plus@0.1.2, asn1@0.1.11, ctype@0.5.2)
    └── bunyan@0.22.0 (mv@0.0.5)

## <a name="install-passport-in-your-web-api"></a>Installere Passport på webstedet API

Fra kommandolinjen skal du ændre mappen til `azuread`, hvis den ikke allerede findes.

Installere Passport ved hjælp af følgende kommando:

`npm install passport`

Output fra kommandoen skal ligne denne tekst:

    passport@0.1.17 node_modules\passport
    ├── pause@0.0.1
    └── pkginfo@0.2.3

## <a name="add-passport-azuread-to-your-web-api"></a>Føje passport azuread til din web API

Dernæst skal tilføje OAuth-strategi ved hjælp af `passport-azuread`, en pakke med strategier, der forbinder Azure AD med Passport. Brug denne strategi for bæreren tokens i stikprøven REST-API.

> [AZURE.NOTE] Selvom OAuth2 giver en ramme, hvor alle kendte token type kan udstedes, har kun bestemte token fået udbredte brug. Tokens for at beskytte slutpunkter er bæreren tokens. Disse typer tokens er den mest udbredte udstedt i OAuth2. Mange installationer forudsætter, at bæreren tokens er den eneste type token udstedt.

Fra kommandolinjen skal du ændre mappen til `azuread`, hvis den ikke allerede findes.

Installere passet `passport-azure-ad` modul, der benytter følgende kommando:

`npm install passport-azure-ad`

Output fra kommandoen skal ligne denne tekst:

``
passport-azure-ad@1.0.0 node_modules/passport-azure-ad
├── xtend@4.0.0
├── xmldom@0.1.19
├── passport-http-bearer@1.0.1 (passport-strategy@1.0.0)
├── underscore@1.8.3
├── async@1.3.0
├── jsonwebtoken@5.0.2
├── xml-crypto@0.5.27 (xpath.js@1.0.6)
├── ursa@0.8.5 (bindings@1.2.1, nan@1.8.4)
├── jws@3.0.0 (jwa@1.0.1, base64url@1.0.4)
├── request@2.58.0 (caseless@0.10.0, aws-sign2@0.5.0, forever-agent@0.6.1, stringstream@0.0.4, tunnel-agent@0.4.1, oauth-sign@0.8.0, isstream@0.1.2, extend@2.0.1, json-stringify-safe@5.0.1, node-uuid@1.4.3, qs@3.1.0, combined-stream@1.0.5, mime-types@2.0.14, form-data@1.0.0-rc1, http-signature@0.11.0, bl@0.9.4, tough-cookie@2.0.0, hawk@2.3.1, har-validator@1.8.0)
└── xml2js@0.4.9 (sax@0.6.1, xmlbuilder@2.6.4)
``

## <a name="add-mongodb-modules-to-your-web-api"></a>Tilføje MongoDB moduler til webstedet API

Dette eksempel bruger MongoDB som dit datalager. Til, installere Mongoose, en udbredte plug-in'en til administration af modeller og skemaer.

* `npm install mongoose`

## <a name="install-additional-modules"></a>Installere yderligere moduler

Dernæst skal installere de resterende påkrævede moduler.

Fra kommandolinjen skal du ændre mappen til `azuread`, hvis den ikke allerede findes:

`cd azuread`

Installere moduler i din `node_modules` directory:

* `npm install assert-plus`
* `npm install ejs`
* `npm install ejs-locals`
* `npm install express`
* `npm install bunyan`


## <a name="create-a-serverjs-file-with-your-dependencies"></a>Oprette en server.js-fil med dine afhængigheder

Den `server.js` fil indeholder fleste af funktioner til din Web API-server. 

Fra kommandolinjen skal du ændre mappen til `azuread`, hvis den ikke allerede findes:

`cd azuread`

Oprette en `server.js` fil i en editor. Tilføj følgende oplysninger:

```Javascript
'use strict';
/**
* Module dependencies.
*/
var fs = require('fs');
var path = require('path');
var util = require('util');
var assert = require('assert-plus');
var mongoose = require('mongoose/');
var bunyan = require('bunyan');
var restify = require('restify');
var config = require('./config');
var passport = require('passport');
var OIDCBearerStrategy = require('passport-azure-ad').BearerStrategy;
```

Gem filen. Du vender tilbage til den senere.

## <a name="create-a-configjs-file-to-store-your-azure-ad-settings"></a>Oprette en config.js fil for at gemme dine Azure AD-indstillinger

Denne kodefil overfører konfigurationsparametrene fra Azure AD-portalen for at den `Passport.js` fil. Du har oprettet disse værdier, når du har tilføjet web API til portalen i den første del af gennemgå. Vi forklarer, hvad der skal indsættes i værdierne i disse parametre, når du har kopieret koden.

Fra kommandolinjen skal du ændre mappen til `azuread`, hvis den ikke allerede findes:

`cd azuread`

Oprette en `config.js` fil i en editor. Tilføj følgende oplysninger:

```Javascript
// Don't commit this file to your public repos. This config is for first-run
exports.creds = {
clientID: <your client ID for this Web API you created in the portal>
mongoose_auth_local: 'mongodb://localhost/tasklist', // Your mongo auth uri goes here
audience: '<your audience URI>', // the Client ID of the application that is calling your API, usually a web API or native client
identityMetadata: 'https://login.microsoftonline.com/<tenant name>/.well-known/openid-configuration', // Make sure you add the B2C tenant name in the <tenant name> area
tenantName:'<tenant name>', 
policyName:'b2c_1_<sign in policy name>' // This is the policy you'll want to validate against in B2C. Usually this is your Sign-in policy (as users sign in to this API)
passReqToCallback: false // This is a node.js construct that lets you pass the req all the way back to any upstream caller. We turn this off as there is no upstream caller.
};

```

[AZURE.INCLUDE [active-directory-b2c-devquickstarts-tenant-name](../../includes/active-directory-b2c-devquickstarts-tenant-name.md)]

### <a name="required-values"></a>Obligatoriske værdier

`clientID`: Klient-ID'ET for dit Web API-program.

`IdentityMetadata`: Dette er hvor `passport-azure-ad` søger efter konfigurationsdataene for identitetsudbyder. Det også ser ud for de pågældende taster til at validere JSON web tokens. 

`audience`: Uniform resource identifier (URI) fra portalen, der identificerer dit opkald program. 

`tenantName`: Din lejer navn (for eksempel **contoso.onmicrosoft.com**).

`policyName`: Den politik, du vil validere tokens kommer til din server. Denne politik skal være den samme politik, du bruger på klientprogrammet til logon.

> [AZURE.NOTE] Brug de samme politikker på tværs af både klienten og serveren konfiguration til vores B2C preview. Hvis du allerede har fuldført en gennemgang og oprettet disse politikker, behøver du ikke gøre det igen. Fordi du har fuldført gennemgang, skal du ikke bør konfigurere nye politikker for klient viser på webstedet.

## <a name="add-configuration-to-your-serverjs-file"></a>Føje konfiguration til filen server.js

At læse værdierne fra den `config.js` -fil, du har oprettet, skal du tilføje den `.config` filer som en nødvendig ressource i programmet, og angiv derefter de globale variabler dem fra den `config.js` dokument.

Fra kommandolinjen skal du ændre mappen til `azuread`, hvis den ikke allerede findes:

`cd azuread`

Åbn den `server.js` fil i en editor. Tilføj følgende oplysninger:

```Javascript
var config = require('./config');
```
Tilføj en ny sektion til `server.js` , der indeholder følgende kode:

```Javascript
// We pass these options in to the ODICBearerStrategy.

var options = {
    // The URL of the metadata document for your app. We put the keys for token validation from the URL found in the jwks_uri tag of the in the metadata.
    identityMetadata: config.creds.identityMetadata,
    clientID: config.creds.clientID,
    tenantName: config.creds.tenantName,
    policyName: config.creds.policyName,
    validateIssuer: config.creds.validateIssuer,
    audience: config.creds.audience,
    passReqToCallback: config.creds.passReqToCallback

};
```

Næste, Lad os tilføje nogle pladsholdere til de brugere, som vi modtager fra vores opkald programmer.

```Javascript
// array to hold logged in users and the current logged in user (owner)
var users = [];
var owner = null;
```

Lad os gå videre og oprette vores logføring for.

```Javascript
// Our logger
var log = bunyan.createLogger({
    name: 'Microsoft Azure Active Directory Sample'
});
```

## <a name="add-the-mongodb-model-and-schema-information-by-using-mongoose"></a>Tilføj MongoDB model og -Skemafiler oplysninger ved hjælp af Mongoose

Den tidligere forberedelse betaler, som du tage disse tre filer sammen i en REST-API-tjeneste.

Brug MongoDB til at gemme dine opgaver, som beskrevet tidligere for denne gennemgang.

I den `config.js` fil kaldet din database **vha**. Med navnet blev også, hvad du placerer i slutningen af den `mongoose_auth_local` forbindelse URL-adresse. Du behøver ikke at oprette databasen på forhånd i MongoDB. Det opretter databasen for dig på den første kørsel af din server-program.

Når du fortælle serveren hvilken MongoDB database du bruger, skal du skrive nogle yderligere kode for at oprette modellen og skema til din Serveropgaver.

### <a name="expand-the-model"></a>Udvide modellen

Denne skema model er enkelt. Du kan udvide det efter behov.

`owner`: Der er tildelt opgaven. Dette objekt er en **streng**.  

`Text`: Opgaven sig selv. Dette objekt er en **streng**.

`date`: Den dato, en opgave forfalder. Dette objekt er en **dato/klokkeslæt**.

`completed`: Hvis opgaven er fuldført. Dette objekt er en **boolesk**.

### <a name="create-the-schema-in-the-code"></a>Oprette skemaet i koden

Fra kommandolinjen skal du ændre mappen til `azuread`, hvis den ikke allerede findes:

`cd azuread`

Åbn den `server.js` fil i en editor. Tilføj følgende oplysninger under af konfigurationspost:

```Javascript
// MongoDB setup
// Setup some configuration
var serverPort = process.env.PORT || 3000; // Note we are hosting our API on port 3000
var serverURI = (process.env.PORT) ? config.creds.mongoose_auth_mongohq : config.creds.mongoose_auth_local;

// Connect to MongoDB
global.db = mongoose.connect(serverURI);
var Schema = mongoose.Schema;
log.info('MongoDB Schema loaded');

// Here we create a schema to store our tasks and users. Pretty simple schema for now.
var TaskSchema = new Schema({
    owner: String,
    Text: String,
    completed: Boolean,
    date: Date
});

// Use the schema to register a model
mongoose.model('Task', TaskSchema);
var Task = mongoose.model('Task');
```
Du først oprette skemaet, og du oprette en model-objekt, du kan bruge til at gemme dine data i hele koden, når du definerer din **omdirigerer**.

## <a name="add-routes-for-your-rest-api-task-server"></a>Tilføje omdirigerer til serverens REST-API opgave

Nu hvor du har en databasemodel til at arbejde med, kan du tilføje omdirigerer du bruger til din REST-API-server.

### <a name="about-routes-in-restify"></a>Om omdirigerer i Restify

Omdirigerer i Restify fungerer på samme måde som de fungerer, når de bruger Express stablen. Du kan definere omdirigerer ved hjælp af den URI, som du mod forventning klientprogrammerne til at ringe til. 

Et typisk mønster for en Restify rute er:

```Javascript
function createObject(req, res, next) {
// do work on Object
_object.name = req.params.object; // passed value is in req.params under object
///...
return next(); // keep the server going
}
....
server.post('/service/:add/:object', createObject); // calls createObject on routes that match this.
```

Restify og Express kan give meget dybere funktionalitet, som definerer programmet typer og at gøre komplekse routing på tværs af forskellige slutpunkter. Med henblik på dette selvstudium Hold vi disse omdirigerer enkelt.

#### <a name="add-default-routes-to-your-server"></a>Tilføj standard omdirigerer til din server

Du kan nu føje de grundlæggende CRUD omdirigerer på **opretter** og **liste** til vores REST-API. Andre omdirigerer kan findes i den `complete` gren i eksemplet.

Fra kommandolinjen skal du ændre mappen til `azuread`, hvis den ikke allerede findes:

`cd azuread`

Åbn den `server.js` fil i en editor. Nedenfor føje databaseposter, du har foretaget ovenfor følgende oplysninger:

```Javascript
/**
 *
 * APIs for our REST Task server
 */

// Create a task

function createTask(req, res, next) {

    // Resitify currently has a bug which doesn't allow you to set default headers
    // This headers comply with CORS and allow us to mongodbServer our response to any origin

    res.header("Access-Control-Allow-Origin", "*");
    res.header("Access-Control-Allow-Headers", "X-Requested-With");

    // Create a new task model, fill it up and save it to Mongodb
    var _task = new Task();

    if (!req.params.Text) {
        req.log.warn({
            params: req.params
        }, 'createTodo: missing task');
        next(new MissingTaskError());
        return;
    }

    _task.owner = owner;
    _task.Text = req.params.Text;
    _task.date = new Date();

    _task.save(function(err) {
        if (err) {
            req.log.warn(err, 'createTask: unable to save');
            next(err);
        } else {
            res.send(201, _task);

        }
    });

    return next();

}
```

```Javascript
/// Simple returns the list of TODOs that were loaded.

function listTasks(req, res, next) {
    // Resitify currently has a bug which doesn't allow you to set default headers
    // This headers comply with CORS and allow us to mongodbServer our response to any origin

    res.header("Access-Control-Allow-Origin", "*");
    res.header("Access-Control-Allow-Headers", "X-Requested-With");

    log.info("listTasks was called for: ", owner);

    Task.find({
        owner: owner
    }).limit(20).sort('date').exec(function(err, data) {

        if (err)
            return next(err);

        if (data.length > 0) {
            log.info(data);
        }

        if (!data.length) {
            log.warn(err, "There is no tasks in the database. Add one!");
        }

        if (!owner) {
            log.warn(err, "You did not pass an owner when listing tasks.");
        } else {

            res.json(data);

        }
    });

    return next();
}
```


#### <a name="add-error-handling-for-the-routes"></a>Tilføje fejlhåndtering for omdirigerer

Tilføj nogle fejlhåndtering, så du kan kommunikere du støder på problemer tilbage til klienten på en måde, som det kan forstå.

Føj følgende kode:

```Javascript
///--- Errors for communicating something interesting back to the client
function MissingTaskError() {
restify.RestError.call(this, {
statusCode: 409,
restCode: 'MissingTask',
message: '"task" is a required parameter',
constructorOpt: MissingTaskError
});
this.name = 'MissingTaskError';
}
util.inherits(MissingTaskError, restify.RestError);
function TaskExistsError(owner) {
assert.string(owner, 'owner');
restify.RestError.call(this, {
statusCode: 409,
restCode: 'TaskExists',
message: owner + ' already exists',
constructorOpt: TaskExistsError
});
this.name = 'TaskExistsError';
}
util.inherits(TaskExistsError, restify.RestError);
function TaskNotFoundError(owner) {
assert.string(owner, 'owner');
restify.RestError.call(this, {
statusCode: 404,
restCode: 'TaskNotFound',
message: owner + ' was not found',
constructorOpt: TaskNotFoundError
});
this.name = 'TaskNotFoundError';
}
util.inherits(TaskNotFoundError, restify.RestError);
```


## <a name="create-your-server"></a>Oprette din server

Du har nu defineret databasen og placere dine omdirigerer på plads. Den sidste ting for dig at gøre er at tilføje den server-forekomst, der administrerer dine opkald.

Restify og Express giver deep tilpasning for en REST-API-server, men vi bruge opsætningen af mest grundlæggende her. 

```Javascript

**
 * Our Server
 */


var server = restify.createServer({
    name: "Microsoft Azure Active Directroy TODO Server",
    version: "2.0.1"
});

// Ensure we don't drop data on uploads
server.pre(restify.pre.pause());

// Clean up sloppy paths like //todo//////1//
server.pre(restify.pre.sanitizePath());

// Handles annoying user agents (curl)
server.pre(restify.pre.userAgentConnection());

// Set a per request bunyan logger (with requestid filled in)
server.use(restify.requestLogger());

// Allow 5 requests/second by IP, and burst to 10
server.use(restify.throttle({
    burst: 10,
    rate: 5,
    ip: true,
}));

// Use the common stuff you probably want
server.use(restify.acceptParser(server.acceptable));
server.use(restify.dateParser());
server.use(restify.queryParser());
server.use(restify.gzipResponse());
server.use(restify.bodyParser({
    mapParams: true
})); // Allows for JSON mapping to REST
server.use(restify.authorizationParser()); // Looks for authorization headers

// Let's start using Passport.js

server.use(passport.initialize()); // Starts passport
server.use(passport.session()); // Provides session support


```
## <a name="add-the-routes-to-the-server-without-authentication"></a>Tilføje omdirigerer til serveren (uden godkendelse)

```Javascript
server.get('/api/tasks', passport.authenticate('oauth-bearer', {
    session: false
}), listTasks);
server.get('/api/tasks', passport.authenticate('oauth-bearer', {
    session: false
}), listTasks);
server.get('/api/tasks/:owner', passport.authenticate('oauth-bearer', {
    session: false
}), getTask);
server.head('/api/tasks/:owner', passport.authenticate('oauth-bearer', {
    session: false
}), getTask);
server.post('/api/tasks/:owner/:task', passport.authenticate('oauth-bearer', {
    session: false
}), createTask);
server.post('/api/tasks', passport.authenticate('oauth-bearer', {
    session: false
}), createTask);
server.del('/api/tasks/:owner/:task', passport.authenticate('oauth-bearer', {
    session: false
}), removeTask);
server.del('/api/tasks/:owner', passport.authenticate('oauth-bearer', {
    session: false
}), removeTask);
server.del('/api/tasks', passport.authenticate('oauth-bearer', {
    session: false
}), removeTask);
server.del('/api/tasks', passport.authenticate('oauth-bearer', {
    session: false
}), removeAll, function respond(req, res, next) {
    res.send(204);
    next();
});


// Register a default '/' handler

server.get('/', function root(req, res, next) {
    var routes = [
        'GET     /',
        'POST    /api/tasks/:owner/:task',
        'POST    /api/tasks (for JSON body)',
        'GET     /api/tasks',
        'PUT     /api/tasks/:owner',
        'GET     /api/tasks/:owner',
        'DELETE  /api/tasks/:owner/:task'
    ];
    res.send(200, routes);
    next();
});
```

```Javascript

server.listen(serverPort, function() {

    var consoleMessage = '\n Microsoft Azure Active Directory Tutorial';
    consoleMessage += '\n +++++++++++++++++++++++++++++++++++++++++++++++++++++';
    consoleMessage += '\n %s server is listening at %s';
    consoleMessage += '\n Open your browser to %s/api/tasks\n';
    consoleMessage += '+++++++++++++++++++++++++++++++++++++++++++++++++++++ \n';
    consoleMessage += '\n !!! why not try a $curl -isS %s | json to get some ideas? \n';
    consoleMessage += '+++++++++++++++++++++++++++++++++++++++++++++++++++++ \n\n';

    //log.info(consoleMessage, server.name, server.url, server.url, server.url);

});

``` 

## <a name="add-authentication-to-your-rest-api-server"></a>Tilføj godkendelse til din REST-API-server

Nu hvor du har en igangværende REST-API-server, kan du gøre det nyttige mod Azure AD.

Fra kommandolinjen skal du ændre mappen til `azuread`, hvis den ikke allerede findes:

`cd azuread`

### <a name="use-the-oidcbearerstrategy-that-is-included-with-passport-azure-ad"></a>Brug den OIDCBearerStrategy, der følger med passport-azure-ad


> [AZURE.TIP]
Når du skriver API'er, bør du altid sammenkæde dataene til noget entydigt fra det token, brugeren ikke efterligne. Når serveren elementerne opgaveliste, betyder det så baseret på **objekt-id** for brugeren i tokenet (kaldet gennem token.oid), som vises i feltet "ejer". Denne værdi sikrer, at kun den pågældende bruger kan få adgang til deres egne opgaveliste elementer. Der findes ingen visning i API af "ejer,", så en ekstern bruger kan anmode om andres opgaveliste elementer, selvom de er godkendt.

Derefter bruger den bæreren strategi, der følger med `passport-azure-ad`.

```Javascript
var findById = function(id, fn) {
    for (var i = 0, len = users.length; i < len; i++) {
        var user = users[i];
        if (user.oid === id) {
            log.info('Found user: ', user);
            return fn(null, user);
        }
    }
    return fn(null, null);
};


var oidcStrategy = new OIDCBearerStrategy(options,
    function(token, done) {
        log.info('verifying the user');
        log.info(token, 'was the token retreived');
        findById(token.sub, function(err, user) {
            if (err) {
                return done(err);
            }
            if (!user) {
                // "Auto-registration"
                log.info('User was added automatically as they were new. Their sub is: ', token.oid);
                users.push(token);
                owner = token.oid;
                return done(null, token);
            }
            owner = token.sub;
            return done(null, user, token);
        });
    }
);

passport.use(oidcStrategy);
```

Passport bruger det samme mønster for alle dens strategier. Du angiver en `function()` , der har `token` og `done` som parametre. Strategien kommer tilbage til dig, når det er gjort alle dens arbejde. Derefter skal du gemme brugeren og gemme tokenet, så du ikke behøver at bede om det igen.

> [AZURE.IMPORTANT]
Ovenstående kode tager alle brugere, der sker der med godkende til din server. Denne proces kaldes også autoregistration. I fremstilling-servere Lad ikke i en hvilken som helst brugere får adgang til API uden at skulle dem gennemfører en registreringsprocessen. Denne proces er som regel den mønster, du ser i consumer apps, der gør det muligt at registrere ved hjælp af Facebook, men derefter bedt om at angive yderligere oplysninger. Hvis dette program ikke et kommandolinjen program, kan vi har pakket e-mailen fra det token objekt, der er returneret og derefter bedt om brugere skal udfylde yderligere oplysninger. Da dette er et eksempel, kan vi føje dem til en database i hukommelsen.



## <a name="run-your-server-application-to-verify-that-it-rejects-you"></a>Køre din server program tilladelse til at bekræfte, at den afviser du

Du kan bruge `curl` til at se, om du nu har OAuth2 beskyttelse mod slutpunkterne. Overskrifterne returneres skal være nok til at fortælle dig, at du er på rette kurs.

Sørge for, at din MongoDB forekomst kører:

    $sudo mongodb

Skifte til mappen, og kør serveren:

    $ cd azuread
    $ node server.js

I et nyt terminal vindue for at køre`curl`

Prøv et grundlæggende INDLÆG:

`$ curl -isS -X POST http://127.0.0.1:3000/api/tasks/brandon/Hello`

```Shell
HTTP/1.1 401 Unauthorized
Connection: close
WWW-Authenticate: Bearer realm="Users"
Date: Tue, 14 Jul 2015 05:45:03 GMT
Transfer-Encoding: chunked
```

Fejlen 401 er det svar, du vil. Det angiver, at Passport lag forsøger at omdirigere til godkende slutpunktet.


## <a name="you-now-have-a-rest-api-service-that-uses-oauth2"></a>Du har en REST-API-tjeneste, der bruger OAuth2 nu

Du har implementeret en REST-API ved hjælp af Restify og OAuth! Du har nu tilstrækkelige kode, så du kan fortsætte med at udvikle din tjeneste og udbygges med dette eksempel. Du er blevet sendt den udstrækning kan du med denne server uden at bruge en OAuth2-kompatible-klient. Brug en yderligere gennemgå som vores [Opret forbindelse til en web API ved hjælp af iOS med B2C](active-directory-b2c-devquickstarts-ios.md) gennemgang af det næste trin.


## <a name="next-steps"></a>Næste trin

Du kan nu flytte til mere avancerede emner, f.eks.:

[Oprette forbindelse til en web API ved hjælp af iOS med B2C](active-directory-b2c-devquickstarts-ios.md)