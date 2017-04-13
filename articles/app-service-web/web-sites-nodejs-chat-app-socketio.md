<properties
    pageTitle="Oprette et Node.js chat-program med Socket.IO i Azure App Service"
    description="Et selvstudium, der viser ved hjælp af socket.io i en node.js online hostes på Azure."
    services="app-service\web"
    documentationCenter="nodejs"
    authors="rmcmurray"
    manager="wpickett"
    editor=""/>

<tags
    ms.service="app-service-web"
    ms.workload="web"
    ms.tgt_pltfrm="na"
    ms.devlang="nodejs"
    ms.topic="article"
    ms.date="08/11/2016"
    ms.author="robmcm"/>

# <a name="create-a-nodejs-chat-application-with-socketio-in-azure-app-service"></a>Oprette et Node.js chat-program med Socket.IO i Azure App Service

Socket.IO indeholder realtid kommunikationen mellem node.js server og kunder ved hjælp af WebSockets. Det understøtter også tilbagefald til andre transporter (såsom lang afstemning), der fungerer med ældre browsere. Dette selvstudium fører dig igennem vært for et Socket.IO baseret chat program som en Azure WebApp, og viser, hvordan du skalere det program, bruger [Azure Redis Cache]. Du kan finde flere oplysninger om Socket.IO, <http://socket.io/>.

> [AZURE.NOTE] Fremgangsmåderne i denne opgave anvende [App Service Web Apps]; for Cloud Services, kan du se [opbygge et Node.js Chat program med Socket.IO på en Azure skybaseret tjeneste].

## <a name="download-the-chat-example"></a>Hente i chat-eksempel

Vi bruger eksemplet chat fra [Socket.IO GitHub lager]for dette projekt. Udfør følgende trin for at hente eksemplet og føje det til det projekt, du tidligere har oprettet.

1.  Hente en [ZIP- eller GZ arkiveret version] af projektet Socket.IO (version 1.3.5 blev brugt til dette dokument)

1.  Udtrække arkiv og Kopiér den **eksempler\\chat** mappe til en ny placering. For eksempel ** \\node\\chat**.

## <a name="modify-appjs-and-install-modules"></a>Ændre app.js og installere moduler

1.  Omdøb filen **index.js** til **app.js**. Dette giver mulighed for Azure til at registrere, at dette er et Node.js program.

1.  Åbn filen **app.js** i et tekstredigeringsprogram. Ændre den linje, der indeholder `var io = require('../..')(server);` som vist nedenfor:

        var express = require('express');
        var app = express();
        var server = require('http').createServer(app);
        // var io = require('../..')(server);
        // New:
        var io = require('socket.io')(server);
        var port = process.env.PORT || 3000;

1. Åbn filen **package.json** og tilføje en reference til socket.io under `dependencies`, som vist nedenfor:

        "dependencies": {
          "express": "3.4.8",
          "socket.io": "1.3.5"
        }

1. Fra kommandolinjen, Skift til den ** \\node\\chat** directory og bruge npm at installere de moduler, der kræves af dette program:

        npm install

    Modulerne Derved installeres til en undermappe med navnet **node_modules**.

## <a name="create-an-azure-web-app"></a>Oprette en Azure WebApp

Følg disse trin for at oprette en Azure-WebApp, aktivere ciffer publicering og derefter aktivere WebSocket understøttelse af WebApp.

> [AZURE.NOTE] For at fuldføre dette selvstudium skal have du en Azure konto. Hvis du ikke har en konto, kan du oprette en gratis prøveversion konto på blot et par minutter. Yderligere oplysninger finder du <a href="http://www.windowsazure.com/pricing/free-trial/?WT.mc_id=A7171371E" target="_blank">Azure gratis prøveversion</a>.

1. Installer Azure kommandolinjen (Azure CLI) og oprette forbindelse til dit Azure-abonnement. Se [installere og konfigurere Azure CLI](../xplat-cli-install.md).

1. Hvis dette er første gang konfigurerer et lager i Azure, skal du oprette logonoplysninger. Skriv følgende kommando fra CLI Azure:

        azure site deployment user set [username] [password]

1. Ændre til den ** \\node\chat** directory og brug følgende kommando til at oprette en ny Azure web app og et lokalt lager ciffer. Denne kommando opretter også et ciffer remote navngivet 'azure'.

        azure site create mysitename --git

    Du skal erstatte 'mysitename' med et entydigt navn til din online.

1. Udfør de eksisterende filer til det lokale lager ved hjælp af følgende kommandoer:

        git add .
        git commit -m "Initial commit"

1. Push-filerne til Azure Web Apps-lager med følgende kommando:

        git push azure master

    Når du bliver bedt om det, kan du angive dine legitimationsoplysninger fra trin 2. Du modtager meddelelser om status, som er importeret moduler på serveren. Når denne proces er afsluttet, bruges programmet på din Azure online.

    > [AZURE.NOTE] Under installationen af modulet, kan du opleve fejl, der ' importerede projektet... blev ikke fundet ". Disse kan sikkert ignoreres.

1. Socket.IO bruger WebSockets, som ikke er aktiveret som standard Azure. For at aktivere web sockets skal du bruge følgende kommando:

        azure site set -w

    Hvis du bliver bedt om det, kan du angive navnet på WebApp.

    >[AZURE.NOTE]
    >'Azure websted set -w' kommandoen behandler fungerer kun med version 0.7.4 eller nyere af Azure kommandolinjen. Du kan også aktivere understøttelse af WebSocket ved hjælp af [Azure-portalen](https://portal.azure.com).
    >
    >Klik på web-app fra bladet Web Apps for at aktivere WebSockets ved hjælp af portalen Azure, skal du klikke på **Indstillinger for alle** > **Programindstillinger**. Klik **på**under **Web Sockets**. Klik derefter på **Gem**.

1. Brug følgende kommando for at åbne din webbrowser og gå til hostet online for at få vist WebApp på Azure:

        azure site browse

Din app er nu kører på Azure og kan overføre chatmeddelelser mellem forskellige klienter, der bruger Socket.IO.

## <a name="scale-out"></a>Skalere ud

Socket.IO programmer kan skaleres ud ved hjælp af en __adapter__ til at distribuere meddelelser og begivenheder mellem flere forekomster af tjenesteprogrammer. Selvom der findes flere kort, kan [socket.io redis] kortet nemt bruges med funktionen Azure Redis Cache.

> [AZURE.NOTE] En ekstra krav til skalering af en Socket.IO løsning er understøttelse af sticky sessioner. Sticky sessioner er aktiveret som standard for Azure online via Azure anmode om Routing. Du kan finde yderligere oplysninger finder [Forekomst forbindelse i Azure websteder].

### <a name="create-a-redis-cache"></a>Oprette en Redis cache

Udfør trinnene i [oprette en cache i Azure Redis Cache] for at oprette en ny cache.

> [AZURE.NOTE] Gemme __værtsnavn__ og __primær nøgle__ til din cache, som disse skal bruges i de næste trin.

### <a name="add-the-redis-and-socketio-redis-modules"></a>Tilføje redis og socket.io redis moduler

1. Fra kommandolinjen, Skift til den __ \\node\\chat__ directory og brug følgende kommando.

        npm install socket.io-redis@0.1.4 redis@0.12.1 --save

    > [AZURE.NOTE] De versioner, der er angivet i denne kommando er de versioner, der bruges, når du tester i denne artikel.

1. Redigere filen __app.js__ for at tilføje følgende linjer umiddelbart efter`var io = require('socket.io')(server);`

        var pub = require('redis').createClient(6379,'redishostname', {auth_pass: 'rediskey', return_buffers: true});
        var sub = require('redis').createClient(6379,'redishostname', {auth_pass: 'rediskey', return_buffers: true});

        var redis = require('socket.io-redis');
        io.adapter(redis({pubClient: pub, subClient: sub}));

    Erstatte __redishostname__ og __rediskey__ med værtsnavn og nøgle til din Redis cache.

    Dette opretter en Publicer og abonnere klient til Redis cachen tidligere har oprettet. Klienterne, der bruges med kortet, til at konfigurere Socket.IO for at bruge Redis cachen til at overføre meddelelser og begivenheder mellem forekomster af dit program

    > [AZURE.NOTE] Mens __socket.io redis__ kortet kan kommunikere direkte til Redis, understøtter den aktuelle version ikke godkendelse kræves af Azure Redis cache. Så indledende forbindelsen er oprettet ved hjælp af modulet __redis__ , og derefter klienten der overføres til __socket.io redis__ kortet.
    >
    > Mens Azure Redis Cache understøtter sikre forbindelser ved hjælp af port 6380, understøtter moduler i dette eksempel bruges ikke sikre forbindelser og 14-7-2014. Ovenstående kode bruger standard, usikker port af 6379.

1. Gemme den ændrede __app.js__

### <a name="commit-changes-and-redeploy"></a>Gemme ændringerne og geninstaller

Fra kommandolinjen i den __ \\node\\chat__ directory, bruge følgende kommandoer til at gemme ændringerne og geninstaller programmet.

    git add .
    git commit -m "implementing scale out"
    git push azure master

Når ændringerne er blevet overføres til serveren, kan du tilpasse webstedet på tværs af flere forekomster ved hjælp af følgende kommando.

    azure site scale instances --instances #

Hvor __#__ er antallet af forekomster til at oprette.

Du kan oprette forbindelse til din online fra flere forskellige browsere eller computere at bekræfte, at meddelelser sendes korrekt til alle klienter.

## <a name="troubleshooting"></a>Fejlfinding i forbindelse med

### <a name="connection-limits"></a>Begrænsninger for forbindelse

Azure Web Apps er tilgængelig i flere lagerenheder, som bestemmer de ressourcer, der er tilgængelige for dit websted. Dette omfatter antallet af tilladte WebSocket forbindelser. Se [Web Apps priser siden]kan finde flere oplysninger.

### <a name="messages-arent-being-sent-using-websockets"></a>Meddelelser sendes ikke ved hjælp af WebSockets

Hvis klientbrowsere beholde går tilbage til at forespørge længe i stedet for ved hjælp af WebSockets, kan det skyldes et af følgende.

* **Prøv at begrænse transport til lige WebSockets**

    Hvis Socket.IO bruge WebSockets som SMS transport, skal understøtter både serveren og klienten WebSockets. Hvis ene eller den anden ikke er tilfældet, forhandl Socket.IO en anden transport, som lang afstemning. Standardlisten over transporter, der bruges af Socket.IO er ` websocket, htmlfile, xhr-polling, jsonp-polling`. Du kan gennemtvinge det skal kun bruge WebSockets ved at tilføje følgende kode i filen **app.js** efter den linje, der indeholder `, nicknames = {};`.

        io.configure(function() {
          io.set('transports', ['websocket']);
        });

    > [AZURE.NOTE] Bemærk, at ældre browsere, der ikke understøtter WebSockets ikke vil kunne oprette forbindelse til webstedet, mens ovenstående kode er aktivt, som den begrænser kommunikation til WebSockets kun.

* **Brug SSL**

    WebSockets er afhængig af nogle mindre anvendte HTTP overskrifter, som **opgradere** sidehovedet. Nogle mellemliggende netværksenheder, såsom web proxy, kan fjerne disse overskrifter. For at undgå dette problem, kan du oprette WebSocket forbindelse via SSL.

    En nem måde at udføre dette er at konfigurere Socket.IO til `match origin protocol`. Dette giver Socket.IO til at sikre WebSockets kommunikationen den samme som den oprindelige HTTP/HTTPS-anmodning om websiden. Hvis en browser bruger en HTTPS URL-adresse til at besøge webstedet, sikres efterfølgende WebSocket kommunikation via Socket.IO over SSL.

    For at ændre i dette eksempel for at aktivere denne konfiguration skal du tilføje følgende kode til filen **app.js** efter den linje, der indeholder `, nicknames = {};`.

        io.configure(function() {
          io.set('match origin protocol', true);
        });

* **Bekræfte web.config indstillinger**

    Azure webapps, der hoster Node.js programmer bruge filen **web.config** til at omdirigere indgående anmodninger til programmet Node.js. **Web.config** skal indeholde følgende post for WebSockets kan fungere korrekt med Node.js programmer.

        <webSocket enabled="false"/>

    Dette deaktiverer modulet IIS WebSockets, som omfatter implementeringen af WebSockets og er i konflikt med Node.js bestemte WebSocket moduler som Socket.IO. Hvis denne linje er ikke til stede, eller er indstillet til `true`, det kan være årsagen til, at WebSocket transport ikke fungerer for dit program.

    Node.js programmer indeholder normalt ikke en **web.config** -fil, så Azure websteder automatisk genererer en for Node.js programmer når de er installeret. Da denne fil genereres automatisk på serveren, skal du bruge FTP- eller FTPS URL-adressen til dit websted til at få vist denne fil. Du kan finde FTP- og FTPS URL-adresser for webstedet i portalen klassisk ved at vælge din online, og klik derefter linket **Dashboard** . URL-adresserne vises i sektionen **Oversigt over** .

    > [AZURE.NOTE] Filen **web.config** er kun genereret Azure websteder, hvis dit program ikke indeholder. Hvis du angiver en **web.config** -fil i roden af projektet programmet på computeren, kan den bruges af Azure Web Apps.

    Hvis posten er ikke til stede, eller er indstillet til en værdi på `true`, og derefter skal du oprette en **web.config** i roden af programmets Node.js og angive en værdi af `false`.  Til reference på nedenfor vises en standard- **web.config** for et program, der bruger **app.js** som indgangspunkt.

        <?xml version="1.0" encoding="utf-8"?>
        <!--
             This configuration file is required if iisnode is used to run node processes behind
             IIS or IIS Express.  For more information, visit:

             https://github.com/tjanczuk/iisnode/blob/master/src/samples/configuration/web.config
        -->

        <configuration>
          <system.webServer>
            <!-- Visit http://blogs.msdn.com/b/windowsazure/archive/2013/11/14/introduction-to-websockets-on-windows-azure-web-sites.aspx for more information on WebSocket support -->
            <webSocket enabled="false" />
            <handlers>
              <!-- Indicates that the server.js file is a node.js web app to be handled by the iisnode module -->
              <add name="iisnode" path="app.js" verb="*" modules="iisnode"/>
            </handlers>
            <rewrite>
              <rules>
                <!-- Do not interfere with requests for node-inspector debugging -->
                <rule name="NodeInspector" patternSyntax="ECMAScript" stopProcessing="true">
                  <match url="^app.js\/debug[\/]?" />
                </rule>

                <!-- First we consider whether the incoming URL matches a physical file in the /public folder -->
                <rule name="StaticContent">
                  <action type="Rewrite" url="public{REQUEST_URI}"/>
                </rule>

                <!-- All other URLs are mapped to the node.js web app entry point -->
                <rule name="DynamicContent">
                  <conditions>
                    <add input="{REQUEST_FILENAME}" matchType="IsFile" negate="True"/>
                  </conditions>
                  <action type="Rewrite" url="app.js"/>
                </rule>
              </rules>
            </rewrite>
            <!--
              You can control how Node is hosted within IIS using the following options:
                * watchedFiles: semi-colon separated list of files that will be watched for changes to restart the server
                * node_env: will be propagated to node as NODE_ENV environment variable
                * debuggingEnabled - controls whether the built-in debugger is enabled

              See https://github.com/tjanczuk/iisnode/blob/master/src/samples/configuration/web.config for a full list of options
            -->
            <!--<iisnode watchedFiles="web.config;*.js"/>-->
          </system.webServer>
        </configuration>

    Hvis dit program bruger et indgangspunkt end **app.js**, skal du erstatte alle forekomster af **app.js** med den korrekte indgangspunkt. For eksempel erstatte **app.js** med **server.js**.

>[AZURE.NOTE] Hvis du vil komme i gang med Azure App Service før tilmelding til en Azure-konto, skal du gå til [Prøve App Service], hvor du straks kan oprette en forbigående starter WebApp i App-tjeneste. Ingen kreditkort, der kræves. ingen forpligtelser.

## <a name="next-steps"></a>Næste trin

Du har lært hvordan du opretter en chat-program, der er placeret i en Azure WebApp i dette selvstudium. Du kan også være vært for dette program som en Azure skybaseret tjeneste. Vejledning i, hvordan du gør dette, kan du se [opbygge et Node.js Chat program med Socket.IO på en Azure skybaseret tjeneste].

Du kan finde flere oplysninger, se også [Node.js Developer Center].

## <a name="whats-changed"></a>Hvad er ændret

* Finde en vejledning til ændring fra websteder til App-tjenesten: [Azure App Service og dets indvirkning på eksisterende Azure-tjenester].

<!-- URL List -->

[Azure Redis Cache]: /documentation/services/redis-cache/
[App Service Webapps]: http://go.microsoft.com/fwlink/?LinkId=529714
[Webside Apps priser]: http://go.microsoft.com/fwlink/?LinkId=511643
[Opbygge et Node.js Chat-program med Socket.IO på en Azure skybaseret tjeneste]: ../cloud-services/cloud-services-nodejs-chat-app-socketio.md
[Install and Configure the Azure CLI]: ../xplat-cli-install.md
[Azure App Service og dens indvirkning på eksisterende Azure Services]: http://go.microsoft.com/fwlink/?LinkId=529714
[Node.js Developer Center]: /develop/nodejs/
[Prøv App Service]: http://go.microsoft.com/fwlink/?LinkId=523751
[Forekomst forbindelse i Azure websteder]: https://azure.microsoft.com/blog/2013/11/18/disabling-arrs-instance-affinity-in-windows-azure-web-sites/
[Oprette en cache i Azure Redis Cache]: ../redis-cache/cache-dotnet-how-to-use-azure-redis-cache.md

[socket.IO redis]: https://github.com/socketio/socket.io-redis
[Socket.IO GitHub lager]: https://github.com/socketio/socket.io
[ZIP- eller GZ arkiveret version]: https://github.com/socketio/socket.io/releases

<!-- IMG List -->

[chat-example-view]: ./media/web-sites-nodejs-chat-app-socketio/socketio-2.png
[npm-output]: ./media/web-sites-nodejs-chat-app-socketio/socketio-7.png
[completed-app]: ./media/web-sites-nodejs-chat-app-socketio/websitesocketcomplete.png
