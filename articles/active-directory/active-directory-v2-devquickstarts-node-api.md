<properties
    pageTitle="Azure AD v2.0 NodeJS Web API | Microsoft Azure"
    description="Sådan oprettes en NodeJS Web API accepterer tokens ud fra begge personlig Microsoft-Account og arbejds- eller skolekonto konti."
    services="active-directory"
    documentationCenter="nodejs"
    authors="brandwe"
    manager="mbaldwin"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="javascript"
    ms.topic="article"
    ms.date="09/16/2016"
    ms.author="brandwe"/>

# <a name="secure-a-web-api-using-nodejs"></a>Sikre en Web API ved hjælp af node.js

> [AZURE.NOTE]
    Ikke alle Azure Active Directory scenarier og funktioner, der understøttes af v2.0 slutpunkt.  For at bestemme, hvis du skal bruge v2.0 slutpunkt skal du læse om [v2.0 begrænsninger](active-directory-v2-limitations.md).

Du kan beskytte en Web API ved hjælp af [OAuth 2.0](active-directory-v2-protocols.md#oauth2-authorization-code-flow) access tokens, så brugerne med både personlige Microsoft-konto og arbejde med Azure Active Directory v2.0 slutpunktet, eller skole konti til sikker access Web-API'EN.

**Passport** er godkendelse programmer til Node.js. Meget fleksible og modul, Passport kan være ubemærket, flyttes til alle Express-baserede eller Resitify webprogram. Et omfattende sæt af strategier understøtter godkendelse ved hjælp af et brugernavn og adgangskode, Facebook, Twitter og mere. Vi har udviklet en strategi for Microsoft Azure Active Directory. Vi vil installere dette modul og derefter tilføje Microsoft Azure Active Directory `passport-azure-ad` plug-in'en.

## <a name="download"></a>Download
Koden for dette selvstudium vedligeholdes [GitHub](https://github.com/AzureADQuickStarts/AppModelv2-WebAPI-nodejs).  Hvis du vil følge med, kan du [hente den app skelet som en .zip](https://github.com/AzureADQuickStarts/AppModelv2-WebAPI-nodejs/archive/skeleton.zip) eller klone skelet:

```git clone --branch skeleton https://github.com/AzureADQuickStarts/AppModelv2-WebAPI-nodejs.git```

Det færdige program er angivet i slutningen af dette selvstudium samt.


## <a name="1-register-an-app"></a>1. Registrer en app
Oprette en ny app på [apps.dev.microsoft.com](https://apps.dev.microsoft.com/?referrer=https://azure.microsoft.com/documentation/articles&deeplink=/appList), eller Følg disse [detaljeret vejledning](active-directory-v2-app-registration.md).  Sørg for at:

- Kopiér ned på **Program-Id** , tildelt til din app, du skal bruge den snart.
- Tilføj **Mobile** platformen til din app.
- Kopiere hele vejen ned **Omdirigere URI** fra portalen. Du skal bruge standardværdien for `urn:ietf:wg:oauth:2.0:oob`.


## <a name="2-download-nodejs-for-your-platform"></a>2: hente node.js til din platform
For at kunne bruge dette eksempel, skal du have en arbejde installation af Node.js.

Du kan installere Node.js fra [http://nodejs.org](http://nodejs.org).

## <a name="3-install-mongodb-on-to-your-platform"></a>3: Installer MongoDB til din platform

For at kunne bruge dette eksempel, skal du have en arbejde installation af MongoDB. Vi bruger MongoDB til at lave vores REST-API persistant på tværs af forekomster af server.

Du kan installere MongoDB fra [http://mongodb.org](http://www.mongodb.org).

> [AZURE.NOTE] Denne gennemgang antages, at du bruger standard installations- og server slutpunkterne for MongoDB, som på tidspunktet for denne skrivning er: mongodb://localhost

## <a name="4-install-the-restify-modules-in-to-your-web-api"></a>4: installere Restify moduler i til din Web API

Vi skal bruge Resitfy til at oprette vores REST-API. Restify er en minimale og fleksible Node.js programmet ramme, der er afledt af Express, der indeholder et solidt sæt af funktioner til oprettelse af REST API'er oven på Opret forbindelse.

### <a name="install-restify"></a>Installer Restify

Ændre mapper til mappen azuread fra kommandolinjen. Hvis mappen **azuread** ikke findes, oprette den.

`cd azuread`- eller -`mkdir azuread;`

Skriv følgende kommando:

`npm install restify`

Denne kommando installeres Restify.

#### <a name="did-you-get-an-error"></a>Du får en fejlmeddelelse?

Når du bruger npm i nogle operativsystemer, modtager du muligvis en fejl af fejl: EPERM, chmod '/ usr/lokale/bin /..' og en anmodning om at prøve at køre kontoen som administrator. Hvis dette forekommer, kan du bruge kommandoen sudo til at køre npm på et højere rettighedsniveau.

#### <a name="did-you-get-an-error-regarding-dtrace"></a>Du får en fejlmeddelelse om DTrace?

Du kan se nogenlunde sådan ud, når du installerer Restify:

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


Restify tilbyder en effektiv metode til at spore RESTEN opkald ved hjælp af DTrace. Mange operativsystemer har dog ikke DTrace, der er tilgængelige. Du kan ignorere disse fejl.


Output fra denne kommando skal se ud som følger:


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
    └── bunyan@0.22.0(mv@0.0.5)


## <a name="5-install-passportjs-into-your-web-api"></a>5: installere Passport.js til din Web API

[Passport](http://passportjs.org/) er godkendelse programmer til Node.js. Meget fleksible og modul, Passport kan være ubemærket, flyttes til alle Express-baserede eller Resitify webprogram. Et omfattende sæt af strategier understøtter godkendelse ved hjælp af et brugernavn og adgangskode, Facebook, Twitter og mere. Vi har udviklet en strategi for Azure Active Directory. Vi vil installere dette modul og derefter tilføje den Azure Active Directory-strategi plug-in'en.

Ændre mapper til mappen azuread fra kommandolinjen.

Skriv følgende kommando for at installere passport.js

`npm install passport`

Output fra kommandoen skal se ud som følger:

    passport@0.1.17 node_modules\passport
    ├── pause@0.0.1
    └── pkginfo@0.2.3

## <a name="6-add-passport-azure-ad-to-your-web-api"></a>6: føje Passport-Azure-AD til din Web API

Dernæst skal tilføje vi strategi OAuth ved hjælp af passport-azuread, en pakke med strategier, der tilsluttes Passport Azure Active Directory. Vi bruger denne strategi for bæreren Tokens i dette eksempel Rest-API.

> [AZURE.NOTE] Selvom OAuth2 giver en ramme, hvor alle kendte token type kan udstedes, har kun bestemte token fået spredt brug. For at beskytte slutpunkter, der har deaktiveret ud af, for at være bæreren Tokens. Bæreren tokens, er mest almindeligt udstedt type token i OAuth2 og mange installationer forudsætter, at bæreren tokens er den eneste type token udstedt.

Fra kommandolinjen, skifte til mappen azuread

Skriv følgende kommando for at installere Passport.js passport-azure-ad-modulet:

`npm install passport-azure-ad`

Output fra kommandoen skal se ud som følger:

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

## <a name="7-add-mongodb-modules-to-your-web-api"></a>7: føje MongoDB moduler til din Web API

Vi skal bruge MongoDB som vores datastore derfor, vi har brug at installere begge de udbredte plug-in'en til at administrere-modeller og skemaer kaldet Mongoose, samt databasedriveren til MongoDB, også kaldet MongoDB.


* `npm install mongoose`
* `npm install mongodb`

## <a name="8-install-additional-modules"></a>8: installere yderligere moduler

Derefter kan vi installere de resterende påkrævede moduler.


Ændre mapper til mappen **azuread** Hvis det ikke allerede er der fra kommandolinjen:

`cd azuread`


Skriv følgende kommandoer for at installere følgende moduler i mappen node_modules:

* `npm install crypto`
* `npm install assert-plus`
* `npm install posix-getopt`
* `npm install util`
* `npm install path`
* `npm install connect`
* `npm install xml-crypto`
* `npm install xml2js`
* `npm install xmldom`
* `npm install async`
* `npm install request`
* `npm install underscore`
* `npm install grunt-contrib-jshint@0.1.1`
* `npm install grunt-contrib-nodeunit@0.1.2`
* `npm install grunt-contrib-watch@0.2.0`
* `npm install grunt@0.4.1`
* `npm install xtend@2.0.3`
* `npm install bunyan`
* `npm update`


## <a name="9-create-a-serverjs-with-your-dependencies"></a>9: oprette en server.js med dine afhængigheder

Filen server.js vil levere for størstedelen af vores funktioner til vores Web API-server. Vi vil tilføje de fleste af vores kode til denne fil. Du vil refactor funktionalitet i på mindre filer, som separat omdirigerer og enheder til fremstilling formål. Vi vil bruge server.js til denne demo for denne funktionalitet.

Ændre mapper til mappen **azuread** Hvis det ikke allerede er der fra kommandolinjen:

`cd azuread`

Oprette en `server.js` filer i vores foretrukne editor og tilføje følgende oplysninger:

```Javascript
'use strict';
/**
* Module dependencies.
*/
var util = require('util');
var assert = require('assert-plus');
var mongoose = require('mongoose/');
var bunyan = require('bunyan');
var restify = require('restify');
var config = require('./config');
var passport = require('passport');
var OIDCBearerStrategy = require('passport-azure-ad').OIDCStrategy;
```

Gem filen. Vi kommer snart tilbage til den.

## <a name="10-create-a-config-file-to-store-your-azure-ad-settings"></a>10: oprette en konfigurationsfil for at gemme dine Azure AD-indstillinger

Denne kodefil overfører konfigurationsparametrene fra din Azure Active Directory-portalen til Passport.js. Du har oprettet disse værdier, når du har tilføjet Web API til portalen i den første del af denne gennemgang. Vi vil forklarer, hvad der skal indsættes i værdierne i disse parametre, når du har kopieret koden.


Ændre mapper til mappen **azuread** Hvis det ikke allerede er der fra kommandolinjen:

`cd azuread`

Oprette en `config.js` filer i vores foretrukne editor og tilføje følgende oplysninger:

```Javascript
// Don't commit this file to your public repos. This config is for first-run
exports.creds = {
mongoose_auth_local: 'mongodb://localhost/tasklist', // Your mongo auth uri goes here
issuer: 'https://sts.windows.net/**<your application id>**/',
audience: '<your redirect URI>',
identityMetadata: 'https://login.microsoftonline.com/common/.well-known/openid-configuration' // For using Microsoft you should never need to change this.
};

```



### <a name="required-values"></a>Obligatoriske værdier

*IdentityMetadata*: Dette er hvor passport-azure-ad ser ud til dine konfigurationsdata til IdP samt de pågældende taster til at validere JWT tokens. Du vil sandsynligvis ikke ændre det, hvis ved hjælp af Azure Active Directory.

*Målgruppe*: Omdirigeringen URI fra portalen.

> [AZURE.NOTE]
Vi rulle vores taster med hyppige intervaller. Sørg for, at du altid vil hente fra "openid_keys" URL-adressen, og at appen har adgang til internettet.


## <a name="11-add-configuration-to-your-serverjs-file"></a>11: føje konfiguration til filen server.js

Vi har brug at læse disse værdier fra den Config-fil, du lige har oprettet på tværs af vores program. For at gøre dette, vi blot tilføje .config-filen som en nødvendig ressource i vores program og derefter angive de globale variabler dem fra config.js dokumentet

Ændre mapper til mappen **azuread** Hvis det ikke allerede er der fra kommandolinjen:

`cd azuread`

Åbn din `server.js` filer i vores foretrukne editor og tilføje følgende oplysninger:

```Javascript
var config = require('./config');
```
Tilføj derefter en ny sektion til `server.js` med følgende kode:

```Javascript
// We pass these options in to the ODICBearerStrategy.
var options = {
// The URL of the metadata document for your app. We will put the keys for token validation from the URL found in the jwks_uri tag of the in the metadata.
identityMetadata: config.creds.identityMetadata,
issuer: config.creds.issuer,
audience: config.creds.audience
};
// array to hold logged in users and the current logged in user (owner)
var users = [];
var owner = null;
// Our logger
var log = bunyan.createLogger({
name: 'Microsoft Azure Active Directory Sample'
});
```

## <a name="step-12-add-the-mongodb-model-and-schema-information-using-moongoose"></a>Trin 12: Tilføje feltet MongoDB Model og skemaoplysninger ved hjælp af Moongoose

Nu alle forberedelsen vil begynde at betale, som vi vind disse tre filer sammen i en REST-API-tjeneste.

Vi skal bruge MongoDB til at gemme vores opgaver, som beskrevet i ***trin 4***for denne gennemgang.

Hvis du tilbagekalde fra filen config.js, vi oprettede i trin 11, kaldet vi vores database *vha*, som, der var hvad vi placere i slutningen af vores mogoose_auth_local forbindelse URL-adresse. Du behøver ikke at oprette denne database på forhånd i MongoDB, oprettes ud for os ved første kørsel af vores server-program (hvis den ikke allerede findes).

Nu hvor vi har bekræftet serveren, hvad vi vil gerne bruge MongoDB-database, har vi brug at skrive nogle yderligere kode for at oprette modellen og skema for vores server opgaver.

#### <a name="discussion-of-the-model"></a>Beskrivelse af modellen

Vores skema model er meget simpel, og du udvide den efter behov.

NAME - navnet på der er tildelt opgaven. En ***streng***

OPGAVE - opgaven sig selv. En ***streng***

DATO – den dato, hvor en opgave forfalder. En ***dato/klokkeslæt***

FÆRDIGGJORT - Hvis opgaven er fuldført eller ej. En ***boolesk***

#### <a name="creating-the-schema-in-the-code"></a>Oprettelse af skemaet i koden


Ændre mapper til mappen **azuread** Hvis det ikke allerede er der fra kommandolinjen:

`cd azuread`

Åbn din `server.js` filer i vores foretrukne editor og tilføje følgende oplysninger under af konfigurationspost:

```Javascript
// MongoDB setup
// Setup some configuration
var serverPort = process.env.PORT || 8080;
var serverURI = (process.env.PORT) ? config.creds.mongoose_auth_mongohq : config.creds.mongoose_auth_local;
// Connect to MongoDB
global.db = mongoose.connect(serverURI);
var Schema = mongoose.Schema;
log.info('MongoDB Schema loaded');
```
Dette vil oprette forbindelse til serveren MongoDB og hånd et skema objekt til os.

#### <a name="using-the-schema-create-our-model-in-the-code"></a>Bruger skemaet, oprette vores model i koden

Under den kode, du har skrevet ovenfor, skal du tilføje følgende kode:

```Javascript
// Here we create a schema to store our tasks and users. Pretty simple schema for now.
var TaskSchema = new Schema({
owner: String,
task: String,
completed: Boolean,
date: Date
});
// Use the schema to register a model
mongoose.model('Task', TaskSchema);
var Task = mongoose.model('Task');
```
Som du kan se fra koden, kan vi oprette vores skemaet og derefter oprette en model objekt vi vil bruge til at gemme vores data i hele koden, når vi definerer vores ***omdirigerer***.

## <a name="step-13-add-our-routes-for-our-task-rest-api-server"></a>Trin 13: Tilføje vores omdirigerer til vores opgave REST-API-server

Nu hvor vi har en databasemodel til at arbejde med, Lad os tilføje omdirigerer vi vil bruge til vores REST-API-server.

### <a name="about-routes-in-restify"></a>Om omdirigerer i Restify

Omdirigerer arbejde i Restify på nøjagtigt samme måde, de gør brug af Express stablen. Du definerer omdirigerer ved hjælp af den URI, som du mod forventning klientprogrammerne til at ringe til. Som regel, definerer du din omdirigerer i en separat fil. Vores henblik angiver vi vores omdirigerer i filen server.js. Vi anbefaler, at du benytter disse til deres egen fil til fremstilling brug.

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


Dette er mønsteret på det mest grundlæggende niveau. Resitfy (og Express) giver meget dybere functionaltiy som definerer programmet typer og at gøre komplekse routing på tværs af forskellige slutpunkter. Vi vil holde disse omdirigerer meget blot til vores formål.

#### <a name="add-default-routes-to-our-server"></a>Tilføje standard omdirigerer til vores-server

Vi vil nu tilføje de grundlæggende CRUD omdirigerer af oprette, Hent, opdatere og slette.

Ændre mapper til mappen **azuread** Hvis det ikke allerede er der fra kommandolinjen:

`cd azuread`

Åbn din `server.js` filer i vores foretrukne editor og tilføje følgende oplysninger under databaseposter, du har foretaget ovenfor:

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
if (!req.params.task) {
req.log.warn({
params: p
}, 'createTodo: missing task');
next(new MissingTaskError());
return;
}
_task.owner = owner;
_task.task = req.params.task;
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
// Delete a task by name
function removeTask(req, res, next) {
Task.remove({
task: req.params.task,
owner: owner
}, function(err) {
if (err) {
req.log.warn(err,
'removeTask: unable to delete %s',
req.params.task);
next(err);
} else {
log.info('Deleted task:', req.params.task);
res.send(204);
next();
}
});
}
// Delete all tasks
function removeAll(req, res, next) {
Task.remove();
res.send(204);
return next();
}
// Get a specific task based on name
function getTask(req, res, next) {
log.info('getTask was called for: ', owner);
Task.find({
owner: owner
}, function(err, data) {
if (err) {
req.log.warn(err, 'get: unable to read %s', owner);
next(err);
return;
}
res.json(data);
});
return next();
}
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

### <a name="add-some-error-handling-for-the-routes"></a>Tilføje nogle fejlhåndtering for omdirigerer

Det giver mening at tilføje nogle fejlhåndtering, så vi kan kommunikere tilbage til klienten problemet vi er stødt på en måde, den kan forstå.

Føj følgende kode under den kode, du har skrevet ovenfor:

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


## <a name="step-14-create-your-server"></a>Trin 14: Oprette din Server!

Vi har vores database, der er defineret, har vi vores omdirigerer på plads, og den sidste ting at gøre er tilføje vores serverforekomst, der kommer til at administrere vores opkald.

Restify (og Express) har en masse deep tilpasning, du kan gøre til en REST-API-server, men igen vi bruger den mest grundlæggende konfiguration til vores formål.

```Javascript
/**
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
}));
```
## <a name="15-adding-the-routes-without-authentication-for-now"></a>15: tilføje omdirigerer (uden godkendelse for nu)

```Javascript
/// Now the real handlers. Here we just CRUD
/**
/*
/* Each of these handlers are protected by our OIDCBearerStrategy by invoking 'oidc-bearer'
/* in the pasport.authenticate() method. We set 'session: false' as REST is stateless and
/* we don't need to maintain session state. You can experiement removing API protection
/* by removing the passport.authenticate() method like so:
/*
/* server.get('/tasks', listTasks);
/*
**/
server.get('/tasks', listTasks);
server.get('/tasks', listTasks);
server.get('/tasks/:owner', getTask);
server.head('/tasks/:owner', getTask);
server.post('/tasks/:owner/:task', createTask);
server.post('/tasks', createTask);
server.del('/tasks/:owner/:task', removeTask);
server.del('/tasks/:owner', removeTask);
server.del('/tasks', removeTask);
server.del('/tasks', removeAll, function respond(req, res, next) {
res.send(204);
next();
});
// Register a default '/' handler
server.get('/', function root(req, res, next) {
var routes = [
'GET /',
'POST /tasks/:owner/:task',
'POST /tasks (for JSON body)',
'GET /tasks',
'PUT /tasks/:owner',
'GET /tasks/:owner',
'DELETE /tasks/:owner/:task'
];
res.send(200, routes);
next();
});
server.listen(serverPort, function() {
var consoleMessage = '\n Microsoft Azure Active Directory Tutorial';
consoleMessage += '\n +++++++++++++++++++++++++++++++++++++++++++++++++++++';
consoleMessage += '\n %s server is listening at %s';
consoleMessage += '\n Open your browser to %s/tasks\n';
consoleMessage += '+++++++++++++++++++++++++++++++++++++++++++++++++++++ \n';
consoleMessage += '\n !!! why not try a $curl -isS %s | json to get some ideas? \n';
consoleMessage += '+++++++++++++++++++++++++++++++++++++++++++++++++++++ \n\n';
});
```
## <a name="16-before-we-add-oauth-support-lets-run-the-server"></a>16: før vi tilføje OAuth-support, Lad os kører på serveren.

Teste din server før vi tilføje godkendelse

Den nemmeste måde at gøre dette på er ved hjælp af krøllet i en kommandolinje. Før vi gør dette, skal vi et simpelt program, der gør det muligt for os til at fortolke output som JSON. Installer værktøjet i json for at gøre det, når alle eksemplerne nedenfor bruger, der.

`$npm install -g jsontool`

Derved installeres værktøjet JSON globalt. Nu hvor vi har udført, – Lad os lege med serveren:

Først skal du sørge for, at din monogoDB isntance kører..

`$sudo mongod`

Derefter skifte til mappen, og start krøllejern..

`$ cd azuread`
`$ node server.js`

`$ curl -isS http://127.0.0.1:8080 | json`

```Shell
HTTP/1.1 2.0OK
Connection: close
Content-Type: application/json
Content-Length: 171
Date: Tue, 14 Jul 2015 05:43:38 GMT
[
"GET /",
"POST /tasks/:owner/:task",
"POST /tasks (for JSON body)",
"GET /tasks",
"PUT /tasks/:owner",
"GET /tasks/:owner",
"DELETE /tasks/:owner/:task"
]
```

Derefter kan vi tilføje en opgave på denne måde:

`$ curl -isS -X POST http://127.0.0.1:8888/tasks/brandon/Hello`

Svaret skal være:

```Shell
HTTP/1.1 201 Created
Connection: close
Access-Control-Allow-Origin: *
Access-Control-Allow-Headers: X-Requested-With
Content-Type: application/x-www-form-urlencoded
Content-Length: 5
Date: Tue, 04 Feb 2014 01:02:26 GMT
Hello
```
Og vi kan registrere opgaver til Brandon på denne måde:

`$ curl -isS http://127.0.0.1:8080/tasks/brandon/`

Hvis alt dette fungerer ud af, er vi klar til at føje OAuth til REST-API-serveren.

**Du har en REST-API server med MongoDB!**

## <a name="17-add-authentication-to-our-rest-api-server"></a>17: tilføje godkendelse til vores RESTEN API-Server

Nu hvor vi har en igangværende REST-API (congrats, btw!) Lad os komme til nyttige mod Azure AD.

Ændre mapper til mappen **azuread** Hvis det ikke allerede er der fra kommandolinjen:

`cd azuread`

### <a name="1-use-the-oidcbearerstrategy-that-is-included-with-passport-azure-ad"></a>1: Brug den oidcbearerstrategy, der følger med passport-azure-ad

Vi har hidtil opbygget en typisk RESTEN Opgaveliste server, hvor alle slags authorization. Dette er hvor vi starte med at placere, sammen.

Først skal vi brug at angive, at vi vil bruge Passport. Placere denne højre efter din serverkonfiguration:

```Javascript
// Let's start using Passport.js

server.use(passport.initialize()); // Starts passport
server.use(passport.session()); // Provides session support
```

> [AZURE.TIP]
Når du skriver API'er bør du altid sammenkæde dataene til noget entydigt fra det token, brugeren ikke efterligne. Når denne server elementerne Opgaveliste, gemmes de baseret på abonnement-ID for brugeren, i tokenet (kaldet gennem token.sub) som vi har lagt i feltet "ejer". Dette sikrer, at kun den pågældende bruger kan få adgang til sin opgavelister og ingen andre kan få adgang til de opgavelister, der er angivet. Der findes ingen visning i API på "ejer", så en ekstern bruger kan anmode om andres opgavelister, selvom de er godkendt.

Dernæst skal vi bruge den åbne ID forbinde bæreren strategi, der følger med passport-azure-ad. Søge kun på koden nu, vil jeg forklare det kort. Placere dette efter hvad du pated ovenfor:

```Javascript
/**
/*
/* Calling the OIDCBearerStrategy and managing users
/*
/* Passport pattern provides the need to manage users and info tokens
/* with a FindorCreate() method that must be provided by the implementor.
/* Here we just autoregister any user and implement a FindById().
/* You'll want to do something smarter.
**/
var findById = function(id, fn) {
for (var i = 0, len = users.length; i < len; i++) {
var user = users[i];
if (user.sub === id) {
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
log.info('User was added automatically as they were new. Their sub is: ', token.sub);
users.push(token);
owner = token.sub;
return done(null, token);
}
owner = token.sub;
return done(null, user, token);
});
}
);
passport.use(oidcStrategy);
```

Passport bruger et lignende mønster for alle er det strategier (Twitter, Facebook, osv.), som alle strategi forfattere overholde. Kigge på strategien får du vist vi sende dem en function(), der indeholder et token og udført som parametre. Strategien dutifully kommer tilbage til os når alle det er arbejde. Når det er tilfældet skal vi gemme brugeren og stash tokenet, så vi ikke behøver at bede om det igen.

> [AZURE.IMPORTANT]
Ovenstående kode tager alle brugere, der sker der med godkende til vores server. Dette kaldes også automatisk registrering. I fremstilling servers uønskede at lade alle uden at skulle dem gennemfører en registreringsprocessen, du beslutter dig for. Det er normalt det mønster, du ser i consumer apps, der gør det muligt at registrere med Facebook, men derefter bedt om at angive yderligere oplysninger. Hvis dette ikke var et kommandolinjen program, er vi kunne lige ud for mail fra det token objekt, der er returneret og stillede dem til at udfylde yderligere oplysninger. Da dette er en testserver vi blot føje dem til databasen i hukommelsen.

### <a name="2-finally-protect-some-endpoints"></a>2. til sidst skal beskytte nogle slutpunkter

Du kan beskytte slutpunkter ved at angive passport.authenticate() opkaldet med den protokol, du vil bruge.

Lad os redigere vores rute i vores Serverkoden for at gøre noget mere interessant:

```Javascript
server.get('/tasks', passport.authenticate('oidc-bearer', {
session: false
}), listTasks);
server.get('/tasks', passport.authenticate('oidc-bearer', {
session: false
}), listTasks);
server.get('/tasks/:owner', passport.authenticate('oidc-bearer', {
session: false
}), getTask);
server.head('/tasks/:owner', passport.authenticate('oidc-bearer', {
session: false
}), getTask);
server.post('/tasks/:owner/:task', passport.authenticate('oidc-bearer', {
session: false
}), createTask);
server.post('/tasks', passport.authenticate('oidc-bearer', {
session: false
}), createTask);
server.del('/tasks/:owner/:task', passport.authenticate('oidc-bearer', {
session: false
}), removeTask);
server.del('/tasks/:owner', passport.authenticate('oidc-bearer', {
session: false
}), removeTask);
server.del('/tasks', passport.authenticate('oidc-bearer', {
session: false
}), removeTask);
server.del('/tasks', passport.authenticate('oidc-bearer', {
session: false
}), removeAll, function respond(req, res, next) {
res.send(204);
next();
});
```

## <a name="18-run-your-server-application-again-and-ensure-it-rejects-you"></a>18: køre din serverprogrammet igen og sikre, at du afviser

Lad os bruge `curl` igen for at se, om vi nu har OAuth2 beskyttelse mod vores slutpunkter. Vi vil gøre dette før runnning en af vores klient SDK'er mod dette slutpunkt. Overskrifterne returneres skal være nok til at fortælle os, vi er nede højre stien.

Først skal du sørge for, at din monogoDB isntance kører..

    $sudo mongod

Derefter skifte til mappen, og start krøllejern..

    $ cd azuread
    $ node server.js

Prøv et grundlæggende INDLÆG:

`$ curl -isS -X POST http://127.0.0.1:8080/tasks/brandon/Hello`

```Shell
HTTP/1.1 401 Unauthorized
Connection: close
WWW-Authenticate: Bearer realm="Users"
Date: Tue, 14 Jul 2015 05:45:03 GMT
Transfer-Encoding: chunked
```

En 401 er det svar, du leder efter her, som, der angiver, at Passport lag forsøger at omdirigere til godkende slutpunktet, som er præcis det, du vil.


## <a name="congratulations-you-have-a-rest-api-service-using-oauth2"></a>Tillykke! Du har en REST-API-tjeneste, der bruger OAuth2!

Du har gik udstrækning det kan du med denne server uden at bruge en OAuth2 kompatibel klient. Du skal gennemgå en ekstra gennemgang.

Hvis du blot søger efter oplysninger om, at implementere en REST-API ved hjælp af Restify og OAuth2, har du mere end tilstrækkelig kode beholde udvikling af din tjeneste og lære, hvordan du opretter i dette eksempel.

## <a name="next-steps"></a>Næste trin

Til reference kan på gennemført prøveoverførsel (uden din konfigurationsværdier), [der er angivet en .zip her](https://github.com/AzureADQuickStarts/AppModelv2-WebAPI-nodejs/archive/complete.zip), eller du klone den fra GitHub:

```git clone --branch complete https://github.com/AzureADQuickStarts/AppModelv2-WebAPI-nodejs.git```

Nu kan du flytte til mere avancerede emner.  Vil du måske at prøve:

[Sikre en Node.js online ved hjælp af v2.0 slutpunktet >>](active-directory-v2-devquickstarts-node-web.md)

Yderligere ressourcer, se:
- [Guiden v2.0 udvikler >>](active-directory-appmodel-v2-overview.md)
- [StackOverflow "azure-active directory" mærke >>](http://stackoverflow.com/questions/tagged/azure-active-directory)

## <a name="get-security-updates-for-our-products"></a>Hente sikkerhedsopdateringer til vores produkter

Vi anbefaler, at finde meddelelser om når sikkerhedshændelser forekommer, ved at besøge [denne side](https://technet.microsoft.com/security/dd252948) og abonnere på vejledende sikkerhedsadvarsler.
