<properties
    pageTitle="Introduktion til Azure AD logge på og logge af ved hjælp af node.js"
    description="Sådan oprettes en node.js Express MVC Web App, der integreres med Azure AD til logon."
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
    ms.date="08/15/2016"
    ms.author="brandwe"/>

# <a name="nodejs-web-app-sign-in--sign-out-with-azure-ad"></a>NodeJS Web App-logon og logge ud med Azure AD


Her skal vi bruge Passport for at:

- Log brugeren på appen med Azure AD.
- Få vist nogle oplysninger om brugeren.
- Log brugeren af app'en.

**Passport** er godkendelse programmer til Node.js. Meget fleksible og modul, Passport kan være ubemærket, flyttes til alle Express-baserede eller Resitify webprogram. Et omfattende sæt af strategier understøtter godkendelse ved hjælp af et brugernavn og adgangskode, Facebook, Twitter og mere. Vi har udviklet en strategi for Microsoft Azure Active Directory. Vi vil installere dette modul og derefter tilføje Microsoft Azure Active Directory `passport-azure-ad` plug-in'en.

Før du gør dette, skal du:

1. Registrere en app.
2. Konfigurere din app til at bruge Passport-azure-ad strategi.
3. Bruge Passport udstede logon og hvor anmodninger til Azure AD.
4. Udskrive data om brugeren.

Koden for dette selvstudium vedligeholdes [GitHub](https://github.com/AzureADQuickStarts/WebApp-OpenIDConnect-NodeJS).  Hvis du vil følge med, kan du [hente den app skelet som en .zip](https://github.com/AzureADQuickStarts/WebApp-OpenIDConnect-NodeJS/archive/skeleton.zip) eller klone skelet:

```git clone --branch skeleton https://github.com/AzureADQuickStarts/WebApp-OpenIDConnect-NodeJS.git```

Det færdige program er angivet i slutningen af dette selvstudium samt.

## <a name="1-register-an-app"></a>1. Registrer en App
- Log på administrationsportalen Azure.
- Klik på **Active Directory**i til venstre i navigationen.
- Vælg den lejer, hvor du vil registrere programmet.
- Klik på fanen **programmer** , og klik på Tilføj i den nederste skuffe.
- Følg anvisningerne, og oprette et nyt **webprogram og/eller WebAPI**.
    - **Navnet** på programmet, som beskriver dit program for slutbrugere
    -   **Sign-On URL-adresse** er den grundlæggende URL-adressen for din app.  Det skelet standard er ' http://localhost:3000/auth/openid/ENTER ".
    - **App ID URI** er et entydigt id for dit program.  Konferencen er at bruge `https://<tenant-domain>/<app-name>`, f.eks.`https://contoso.onmicrosoft.com/my-first-aad-app`
- Når du har færdiggjort registrering, Tildel AAD din app et entydigt klient-id.  Du skal bruge denne værdi i næste afsnit, så kopiere det fra fanen Konfigurer.

## <a name="2-add-pre-requisities-to-your-directory"></a>2. tilføje pre requisities på adresselisten

Ændre mapper til rodmappen Hvis det ikke allerede er der fra kommandolinjen, og Kør følgende kommandoer:

- `npm install express`
- `npm install ejs`
- `npm install ejs-locals`
- `npm install restify`
- `npm install mongoose`
- `npm install bunyan`
- `npm install assert-plus`
- `npm install passport`

- Desuden skal du bruge vores `passport-azure-ad` samt:

- `npm install passport-azure-ad`

Dette installerer bibliotekerne, der afhænger af passport-azure-ad.

## <a name="3-set-up-your-app-to-use-the-passport-node-js-strategy"></a>3. konfigurere din app til at bruge passport-node-js strategi
Her skal vi konfigurere Express programmer for at bruge OpenID forbinde authentication protocol.  Passport bruges til at udstede logon og hvor anmodninger, administrere brugerens session og få oplysninger om brugeren, bl.a.

-   For at starte, åbne den `config.js` filen er i roden af projektet, og angiv din app konfiguration af værdier i den `exports.creds` sektion.
    -   Den `clientID:` er det **Program-Id** , der er tildelt til din app i portalen registrering.
    -   Den `returnURL` er **Omdirigere Uri** , du har angivet i portalen.
    - Den `clientSecret` er Hemmeligheden bag du har oprettet i portalen

- Næste gang åbner `app.js` filen er i roden af projekt, og Tilføj følgende opkaldet til at aktivere den `OIDCStrategy` strategi, der følger med`passport-azure-ad`


```JavaScript
var OIDCStrategy = require('passport-azure-ad').OIDCStrategy;

// add a logger

var log = bunyan.createLogger({
    name: 'Microsoft OIDC Example Web Application'
});
```

- Derefter kan du bruge den strategi, vi blot refereres til at håndtere anmodninger vores logon

```JavaScript
// Use the OIDCStrategy within Passport. (Section 2) 
// 
//   Strategies in passport require a `validate` function, which accept
//   credentials (in this case, an OpenID identifier), and invoke a callback
//   with a user object.
passport.use(new OIDCStrategy({
    callbackURL: config.creds.returnURL,
    realm: config.creds.realm,
    clientID: config.creds.clientID,
    clientSecret: config.creds.clientSecret,
    oidcIssuer: config.creds.issuer,
    identityMetadata: config.creds.identityMetadata,
    skipUserProfile: config.creds.skipUserProfile,
    responseType: config.creds.responseType,
    responseMode: config.creds.responseMode
  },
  function(iss, sub, profile, accessToken, refreshToken, done) {
    if (!profile.email) {
      return done(new Error("No email found"), null);
    }
    // asynchronous verification, for effect...
    process.nextTick(function () {
      findByEmail(profile.email, function(err, user) {
        if (err) {
          return done(err);
        }
        if (!user) {
          // "Auto-registration"
          users.push(profile);
          return done(null, profile);
        }
        return done(null, user);
      });
    });
  }
));
```
Passport bruger et lignende mønster for alle det er strategier (Twitter, Facebook, osv.), som alle strategi forfattere overholde. Kigge på strategien får du vist vi sende dem en function(), der indeholder et token og udført som parametre. Strategien dutifully kommer tilbage til os når alle den er arbejde. Når det er tilfældet skal vi gemme brugeren og stash tokenet, så vi ikke behøver at bede om det igen.


> [AZURE.IMPORTANT] 
Ovenstående kode tager alle brugere, der sker der med godkende til vores server. Dette kaldes også automatisk registrering. I fremstilling servers uønskede at lade alle uden at skulle dem gennemfører en registreringsprocessen, du beslutter dig for. Dette er som regel den mønster, du ser i consumer apps, der gør det muligt at registrere med Facebook, men Spørg dig til at udfylde yderligere oplysninger. Hvis dette ikke var en Northwind, er vi kunne lige ud for mail fra det token objekt, der er returneret og stillede dem til at udfylde yderligere oplysninger. Da dette er en testserver vi blot føje dem til databasen i hukommelsen.

- Næste, Lad os tilføje de metoder, der giver os til at holde styr på den gemte i brugere efter behov ved Passport. Dette omfatter serialisering og deserialisering af brugerens oplysninger:

```JavaScript

// Passport session setup. (Section 2)

//   To support persistent login sessions, Passport needs to be able to
//   serialize users into and deserialize users out of the session.  Typically,
//   this will be as simple as storing the user ID when serializing, and finding
//   the user by ID when deserializing.
passport.serializeUser(function(user, done) {
  done(null, user.email);
});

passport.deserializeUser(function(id, done) {
  findByEmail(id, function (err, user) {
    done(err, user);
  });
});

// array to hold logged in users
var users = [];

var findByEmail = function(email, fn) {
  for (var i = 0, len = users.length; i < len; i++) {
    var user = users[i];
   log.info('we are using user: ', user);
    if (user.email === email) {
      return fn(null, user);
    }
  }
  return fn(null, null);
};
```

- Næste, Lad os tilføje kode for at indlæse den udtrykkelig program. Her kan du se vi bruge standard /views og /routes mønster, der Express indeholder.

```JavaScript

// configure Express (Section 2)

var app = express();


app.configure(function() {
  app.set('views', __dirname + '/views');
  app.set('view engine', 'ejs');
  app.use(express.logger());
  app.use(express.methodOverride());
  app.use(cookieParser());
  app.use(expressSession({ secret: 'keyboard cat', resave: true, saveUninitialized: false }));
  app.use(bodyParser.urlencoded({ extended : true }));
  // Initialize Passport!  Also use passport.session() middleware, to support
  // persistent login sessions (recommended).
  app.use(passport.initialize());
  app.use(passport.session());
  app.use(app.router);
  app.use(express.static(__dirname + '/../../public'));
});

```

- Lad os tilføje til sidst skal de omdirigerer, vil afleverer faktisk login anmodninger om til den `passport-azure-ad` program:

```JavaScript

// Our Auth routes (Section 3)

// GET /auth/openid
//   Use passport.authenticate() as route middleware to authenticate the
//   request.  The first step in OpenID authentication will involve redirecting
//   the user to their OpenID provider.  After authenticating, the OpenID
//   provider will redirect the user back to this application at
//   /auth/openid/return
app.get('/auth/openid',
  passport.authenticate('azuread-openidconnect', { failureRedirect: '/login' }),
  function(req, res) {
    log.info('Authentication was called in the Sample');
    res.redirect('/');
  });

// GET /auth/openid/return
//   Use passport.authenticate() as route middleware to authenticate the
//   request.  If authentication fails, the user will be redirected back to the
//   login page.  Otherwise, the primary route function function will be called,
//   which, in this example, will redirect the user to the home page.
app.get('/auth/openid/return',
  passport.authenticate('azuread-openidconnect', { failureRedirect: '/login' }),
  function(req, res) {
    log.info('We received a return from AzureAD.');
    res.redirect('/');
  });

// POST /auth/openid/return
//   Use passport.authenticate() as route middleware to authenticate the
//   request.  If authentication fails, the user will be redirected back to the
//   login page.  Otherwise, the primary route function function will be called,
//   which, in this example, will redirect the user to the home page.
app.post('/auth/openid/return',
  passport.authenticate('azuread-openidconnect', { failureRedirect: '/login' }),
  function(req, res) {
    log.info('We received a return from AzureAD.');
    res.redirect('/');
  });
  ```

## <a name="4-use-passport-to-issue-sign-in-and-sign-out-requests-to-azure-ad"></a>4. Brug Passport til at udstede logon og hvor anmodninger til Azure AD

Din app er nu konfigureret korrekt til at kommunikere med v2.0 slutpunktet ved hjælp af OpenID forbinde godkendelse-protokollen.  `passport-azure-ad`har taget sig af alle grimme detaljerne i oprette godkendelse meddelelser, validere tokens ud fra Azure AD og vedligeholdelse brugersession.  Alt, forbliver er at give brugerne en metode til at logge på Log af og indsamle flere oplysninger om den bruger logget på.

- Først skal giver mulighed for at tilføje standard, logon, konto og log metoder til at vores `app.js` fil:

```JavaScript

//Routes (Section 4)

app.get('/', function(req, res){
  res.render('index', { user: req.user });
});

app.get('/account', ensureAuthenticated, function(req, res){
  res.render('account', { user: req.user });
});

app.get('/login',
  passport.authenticate('azuread-openidconnect', { failureRedirect: '/login' }),
  function(req, res) {
    log.info('Login was called in the Sample');
    res.redirect('/');
});

app.get('/logout', function(req, res){
  req.logout();
  res.redirect('/');
});

```

-   Lad os gennemgå disse detaljerede oplysninger om:
    -   Den `/` distribuere omdirigerer til visningen index.ejs, der passerer brugeren i anmodningen (hvis den findes)
    - Den `/account` distribuere vil første ***sikre, at vi er godkendt*** (vi implementere, nedenfor) og derefter overføre brugeren i anmodningen, så vi kan få flere oplysninger om brugeren.
    - Den `/login` distribuere ringer vores azuread openidconnect godkenderen fra `passport-azuread` og hvis, som ikke lykkes er omdirigerer brugeren tilbage til /login
    - Den `/logout` vil blot opkald på logout.ejs (og distribuere) som rydder cookies og derefter returnere brugeren tilbage til index.ejs


- Til den sidste del af `app.js`, Lad os tilføje metoden EnsureAuthenticated, der bruges i `/account` ovenfor.

```JavaScript

// Simple route middleware to ensure user is authenticated. (Section 4)

//   Use this route middleware on any resource that needs to be protected.  If
//   the request is authenticated (typically via a persistent login session),
//   the request will proceed.  Otherwise, the user will be redirected to the
//   login page.
function ensureAuthenticated(req, res, next) {
  if (req.isAuthenticated()) { return next(); }
  res.redirect('/login')
}
```

- Til sidst skal faktisk oprette selve serveren i `app.js`:

```JavaScript

app.listen(3000);

```


## <a name="5-create-the-views-and-routes-in-express-to-display-our-user-in-the-website"></a>5. oprette visninger og omdirigerer i express til at vise vores bruger på webstedet

Vi har vores `app.js` fuldført. Nu vi skal blot føje omdirigerer og visninger, som viser de oplysninger, vi få adgang til den bruger, samt håndtaget på `/logout` og `/login` omdirigerer vi har oprettet.

- Oprette den `/routes/index.js` distribuere under rodmappen.

```JavaScript
/*
 * GET home page.
 */

exports.index = function(req, res){
  res.render('index', { title: 'Express' });
};
```

- Oprette den `/routes/user.js` distribuere under rodmappen

```JavaScript
/*
 * GET users listing.
 */

exports.list = function(req, res){
  res.send("respond with a resource");
};
```

Disse enkle omdirigerer overfører kun langs anmodningen til vores visninger, herunder brugeren, hvis de forefindes.

- Oprette den `/views/index.ejs` visningen under rodmappen. Dette er en enkelt side, som kan ringe til vores logon og log metoder og gør det muligt at fange kontooplysninger. Meddelelse om, at vi kan bruge betinget `if (!user)` som brugeren, der overføres via i anmodningen er dokumentation, der er logget på bruger.

```JavaScript
<% if (!user) { %>
    <h2>Welcome! Please log in.</h2>
    <a href="/login">Log In</a>
<% } else { %>
    <h2>Hello, <%= user.displayName %>.</h2>
    <a href="/account">Account Info</a></br>
    <a href="/logout">Log Out</a>
<% } %>
```

- Oprette den `/views/account.ejs` få vist under rodmappen, så vi kan få vist yderligere oplysninger, der `passport-azuread` er indsat i brugeranmodningen.

```Javascript
<% if (!user) { %>
    <h2>Welcome! Please log in.</h2>
    <a href="/login">Log In</a>
<% } else { %>
<p>displayName: <%= user.displayName %></p>
<p>givenName: <%= user.name.givenName %></p>
<p>familyName: <%= user.name.familyName %></p>
<p>UPN: <%= user._json.upn %></p>
<p>Profile ID: <%= user.id %></p>
<p>Full Claimes</p>
<%- JSON.stringify(user) %>
<p></p>
<a href="/logout">Log Out</a>
<% } %>
```

- Til sidst, Lad os gøre ud ret ved at tilføje et layout. Oprette den ' / views/layout.ejs' visningen under rodmappen

```HTML

<!DOCTYPE html>
<html>
    <head>
        <title>Passport-OpenID Example</title>
    </head>
    <body>
        <% if (!user) { %>
            <p>
            <a href="/">Home</a> | 
            <a href="/login">Log In</a>
            </p>
        <% } else { %>
            <p>
            <a href="/">Home</a> | 
            <a href="/account">Account</a> | 
            <a href="/logout">Log Out</a>
            </p>
        <% } %>
        <%- body %>
    </body>
</html>
```

Til sidst skal oprette og køre din app! 

Køre `node app.js` og gå til`http://localhost:3000`


Logge på med en personlig Microsoft-Account eller arbejds-eller skolekonto, og se, hvordan brugerens identitet afspejles i listen /account.  Du har nu en WebApp sikret ved hjælp af branche standard protokoller, der kan godkende brugere med begge deres personlige og arbejds-eller skolekonto konti.

Til reference kan på gennemført prøveoverførsel (uden din konfigurationsværdier), [der er angivet en .zip her](https://github.com/AzureADQuickStarts/WebApp-OpenIDConnect-NodeJS/archive/complete.zip), eller du klone den fra GitHub:

```git clone --branch complete https://github.com/AzureADQuickStarts/WebApp-OpenIDConnect-NodeJS.git```


Nu kan du flytte til mere avancerede emner.  Vil du måske at prøve:

[Secure Web API med Azure AD >>](active-directory-devquickstarts-webapi-nodejs.md)

[AZURE.INCLUDE [active-directory-devquickstarts-additional-resources](../../includes/active-directory-devquickstarts-additional-resources.md)]
