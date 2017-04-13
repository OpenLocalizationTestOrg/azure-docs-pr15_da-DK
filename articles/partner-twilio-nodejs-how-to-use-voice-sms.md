<properties 
    pageTitle="Brug af Twilio til tale, VoIP og SMS-beskeder i Azure" 
    description="Lær at foretage et telefonopkald og sende en SMS-meddelelse med tjenesten Twilio API på Azure. Kodeeksempler, der er skrevet i Node.js." 
    services="" 
    documentationCenter="nodejs" 
    authors="devinrader" 
    manager="wpickett" 
    editor=""/>

<tags 
    ms.service="multiple" 
    ms.workload="na" 
    ms.tgt_pltfrm="na" 
    ms.devlang="nodejs" 
    ms.topic="article" 
    ms.date="11/25/2014" 
    ms.author="wpickett"/>


# <a name="using-twilio-for-voice-voip-and-sms-messaging-in-azure"></a>Brug af Twilio til tale, VoIP og SMS-beskeder i Azure

Denne vejledning viser, hvordan du opretter apps, kommunikerer med Twilio og node.js på Azure.

<a id="whatis"/>
## <a name="what-is-twilio"></a>Hvad er Twilio?

Twilio er en API-platform, der gør det nemt for udviklere at foretage og modtage telefonopkald, sende og modtage tekstmeddelelser og integrere VoIP-opkald til browserbaserede og Oprindelig mobilprogrammer.  Lad os gennemgå hvordan det fungerer før dykning.

### <a name="receiving-calls-and-text-messages"></a>Modtage opkald og SMS-beskeder

Twilio gør det muligt for udviklere til at [købe programmeres telefonnumre] [ purchase_phone] som kan bruges til at både sende og modtage opkald og SMS-beskeder.  Når et Twilio tal modtager et indgående opkald eller tekst, sender Twilio dit webprogram en HTTP POST eller GET-anmodning, hvor du bliver spurgt vejledning om, hvordan du håndterer et opkald eller tekst.  Din server reagerer på Twilios HTTP-anmodning med [TwiML][twiml], et simpelt sæt af XML-koder, der indeholder oplysninger om, hvordan du skal håndtere et opkald eller tekst.  Vi vil se eksempler på TwiML i et øjeblik.

### <a name="making-calls-and-sending-text-messages"></a>Opkald og sende SMS-beskeder

Ved at gøre HTTP-anmodninger til Twilio web service API, udviklere sende SMS-beskeder eller starte udgående telefonopkald.  For udgående opkald angive udvikleren også en URL-adresse, der returnerer TwiML vejledning i, hvordan du skal håndtere udgående opkald, når den er tilsluttet.

### <a name="embedding-voip-capabilities-in-ui-code-javascript-ios-or-android"></a>Integrere VoIP-funktioner i Brugergrænsefladen kode (JavaScript, iOS eller Android)

Twilio indeholder en klientsiden SDK, som kan lave en hvilken som helst pc webbrowser, iOS-app eller Android-appen til VoIP-telefon.  I denne artikel fokuserer vi på Sådan bruger du VoIP ringe i browseren.  Ud over Twilio JavaScript SDK kører i browseren, skal en serversiden program (vores node.js program) bruges til at udstede et "egenskab token" JavaScript-klienten.  Du kan læse mere om at bruge VoIP med node.js [på Twilio Udviklingscenter blog][voipnode].

<a id="signup"/>
## <a name="sign-up-for-twilio-microsoft-discount"></a>Tilmeld dig til Twilio (Microsoft rabat)

Før du bruger Twilio tjenester, skal du [tilmelde dig en konto]første[signup].  Microsoft Azure kunder får en særlig rabat - [skal du sørge for at tilmelde dig her][signup]!

<a id="azuresite"/>
## <a name="create-and-deploy-a-nodejs-azure-website"></a>Oprette og installere et node.js Azure-websted

Derefter skal du oprette et node.js websted, der kører på Azure.  [Officielle dokumentationen til at udføre dette er placeret her][azure_new_site].  På et højt niveau, skal du udføre følgende:

* Tilmelding til en Azure-konto, hvis du ikke allerede har et
* Brug af i Azure-Administrationskonsol til at oprette et nyt websted
* Tilføje understøttelse af datakilde (vi antages, at du har brugt ciffer)
* Oprettelse af en fil `server.js` med et enkelt node.js webprogram
* Implementering af dette enkle program til Azure

<a id="twiliomodule"/>
## <a name="configure-the-twilio-module"></a>Konfigurere modulet Twilio

Dernæst skal begynder vi at skrive et simpelt node.js program, som gør brug af Twilio API.  Før vi begynder, skal vi konfigurere vores Twilio kontolegitimationsoplysninger.  

### <a name="configuring-twilio-credentials-in-system-environment-variables"></a>Konfiguration af Twilio legitimationsoplysninger i System-miljøvariabler

For at gøre godkendte anmodninger på Twilio back-end, skal vi vores konto SID og auth token, der fungerer som det brugernavn og adgangskode, der er angivet for vores Twilio-konto. Den sikreste måde at konfigurere disse til brug med modulet node i Azure er via system miljøvariabler, som du kan angive direkte i Azure administrationskonsollen.

Vælg webstedet node.js, og klik på linket "KONFIGURER".  Hvis du ruller ned lidt, får du vist et område, hvor du kan angive egenskaber for konfiguration for dit program.  Angiv dine Twilio-kontolegitimationsoplysninger ([findes i dashboardet Twilio][twilio_dashboard]) som vist - Sørg for, at navngive dem "TWILIO_ACCOUNT_SID" og "TWILIO_AUTH_TOKEN" henholdsvis:

![Azure-administrationskonsollen][azure-admin-console]

Når du har konfigureret disse variabler, genstart dit program i konsollen Azure.

### <a name="declaring-the-twilio-module-in-packagejson"></a>Erklære modulet Twilio i package.json

Derefter skal vi oprette en package.json for at kunne administrere afhængigheder vores node modul via [npm].  Oprette en fil med navnet "package.json" på det samme niveau som den "server.js"-fil, du oprettede i Azure/node.js selvstudiet.  I denne fil, Placer følgende:

  {"navn": "program-navn", "version": "0.0.1", "Privat": SAND, "scripts": {"start": "node server"}, "afhængigheder": {"udtrykkelig": "3.1.0", "ejs": "*", "twilio": "*"}}

Dette erklærer modulet twilio som en afhængighed samt populære [udtrykkelig web framework] [ express] og EJS skabelon-programmet.  OK, nu vi er klar – Lad os skrive kode!

<a id="makecall"/>
## <a name="make-an-outbound-call"></a>Foretage en udgående opkald

Lad os oprette en simpel formular, der kan foretage et opkald til et tal, vi vælger.  Åbn server.js, og indtast følgende kode.  Bemærk, hvor der står "CHANGE_ME" - sætte navnet på webstedet azure der:

    // Module dependencies
    var express = require('express'), 
      path = require('path'), 
      http = require('http'), 
      twilio = require('twilio');

    // Create Express web application
    var app = express();

    // Express configuration
    app.configure(function(){
      app.set('port', process.env.PORT || 3000);
      app.set('views', __dirname + '/views');
      app.set('view engine', 'ejs');
      app.use(express.favicon());
      app.use(express.logger('dev'));
      app.use(express.bodyParser());
      app.use(express.methodOverride());
      app.use(app.router);
      app.use(express.static(path.join(__dirname, 'public')));
    });
    app.configure('development', function(){
      app.use(express.errorHandler());
    });

    // Render an HTML user interface for the application's home page
    app.get('/', function(request, response) {
      response.render('index');
    });

    // Handle the form POST to place a call
    app.post('/call', function(request, response) {
      var client = twilio();
      client.makeCall({
          // make a call to this number
          to:request.body.number,

          // Change to a Twilio number you bought - see:
          // https://www.twilio.com/user/account/phone-numbers/incoming
          from:'+15558675309',

          // A URL in our app which generates TwiML
          // Change "CHANGE_ME" to your app's name
          url:'https://CHANGE_ME.azurewebsites.net/outbound_call'
      }, function(error, data) {
          // Go back to the home page
          response.redirect('/');
      });
    });

    // Generate TwiML to handle an outbound call
    app.post('/outbound_call', function(request, response) {
      var twiml = new twilio.TwimlResponse();

      // Say a message to the call's receiver 
      twiml.say('hello - thanks for checking out Twilio and Azure', {
          voice:'woman'
      });

      response.set('Content-Type', 'text/xml');
      response.send(twiml.toString());
    });

    // Start server
    http.createServer(app).listen(app.get('port'), function(){
      console.log("Express server listening on port " + app.get('port'));
    });

Dernæst skal oprette en mappe med navnet "visninger" - i denne mappe, oprette en fil med navnet "index.ejs" med følgende indhold:

    <!DOCTYPE html>
    <html>
    <head>
      <title>Twilio Test</title>
      <style>
        input { height:20px; width:300px; font-size:18px; margin:5px; padding:5px; }
      </style>
    </head>
    <body>
      <h1>Twilio Test</h1>
      <form action="/call" method="POST">
          <input placeholder="Enter a phone number" name="number"/>
          <br/>
          <input type="submit" value="Call the number above"/>
      </form>
    </body>
    </html>

Nu, installeres dit websted til Azure, og Åbn din startside.  Du skulle kunne Angiv dit telefonnummer i tekstfeltet, og modtager et opkald fra dit nummer som Twilio!

<a id="sendmessage"/>
## <a name="send-an-sms-message"></a>Send en SMS-meddelelse

Nu, Lad os konfigurere en brugergrænseflade og håndtering af logik formular til at sende en tekstmeddelelse.  Åbn op "server.js", og Føj følgende kode efter det sidste opkald til "app.post":

    app.post('/sms', function(request, response) {
      var client = twilio();
      client.sendSms({
          // send a text to this number
          to:request.body.number,

          // A Twilio number you bought - see:
          // https://www.twilio.com/user/account/phone-numbers/incoming
          from:'+15558675309',

          // The body of the text message
          body: request.body.message
          
      }, function(error, data) {
          // Go back to the home page
          response.redirect('/');
      });
    });

Tilføj en anden formular under den første opgave til at sende et tal og en SMS-besked i "views/index.ejs":

    <form action="/sms" method="POST">
      <input placeholder="Enter a phone number" name="number"/>
      <br/>
      <input placeholder="Enter a message to send" name="message"/>
      <br/>
      <input type="submit" value="Send text to the number above"/>
    </form>

Geninstaller dit program til Azure, og du bør nu kunne sende, formular og sende dig selv (eller en af dine nærmeste venner) en tekstmeddelelse!

<a id="nextsteps"/>
## <a name="next-steps"></a>Næste trin

Du har nu lært grundlæggende om brug af node.js og Twilio til at oprette apps, der kommunikerer.  Men disse eksempler ridse lidt overfladen af hvad der er muligt med Twilio og node.js.  Flere oplysninger ved hjælp af Twilio med node.js, se følgende ressourcer:

* [Officiel modul dokumenter][docs]
* [Selvstudium om VoIP med node.js programmer][voipnode]
* [Votr - en realtid SMS afstemning program med node.js og CouchDB (tre dele)][votr]
* [Par programmering i browseren med node.js][pair]

Vi håber du elsker hackere node.js og Twilio på Azure!

[purchase_phone]: https://www.twilio.com/user/account/phone-numbers/available/local
[twiml]: https://www.twilio.com/docs/api/twiml
[signup]: http://ahoy.twilio.com/azure
[azure_new_site]: /app-service-web/web-sites-nodejs-develop-deploy-mac.md
[twilio_dashboard]: https://www.twilio.com/user/account
[npm]: http://npmjs.org
[express]: http://expressjs.com
[voipnode]: http://www.twilio.com/blog/2013/04/introduction-to-twilio-client-with-node-js.html
[docs]: http://twilio.github.io/twilio-node/
[votr]: http://www.twilio.com/blog/2012/09/building-a-real-time-sms-voting-app-part-1-node-js-couchdb.html
[pair]: http://www.twilio.com/blog/2013/06/pair-programming-in-the-browser-with-twilio.html
[azure-admin-console]: ./media/partner-twilio-nodejs-how-to-use-voice-sms/twilio_1.png



