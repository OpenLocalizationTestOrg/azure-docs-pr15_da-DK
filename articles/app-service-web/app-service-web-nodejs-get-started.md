<properties
    pageTitle="Komme i gang med Node.js webapps i Azure App Service"
    description="Lær, hvordan du installerer et Node.js program til en WebApp i Azure App-tjeneste."
    services="app-service\web"
    documentationCenter="nodejs"
    authors="cephalin"
    manager="wpickett"
    editor=""/>

<tags
    ms.service="app-service-web"
    ms.workload="web"
    ms.tgt_pltfrm="na"
    ms.devlang="nodejs"
    ms.topic="get-started-article"
    ms.date="07/01/2016"
    ms.author="cephalin"/>

# <a name="get-started-with-nodejs-web-apps-in-azure-app-service"></a>Komme i gang med Node.js webapps i Azure App Service

[AZURE.INCLUDE [tabs](../../includes/app-service-web-get-started-nav-tabs.md)]

Dette selvstudium viser, hvordan du opretter et enkelt [Node.js] -program og installere den på [Azure App Service] fra kommandolinjen miljø, som cmd.exe eller bash. Vejledningen i dette selvstudium kan anvendes på alle operativsystemer, der kan køre Node.js.

>[AZURE.INCLUDE [app-service-linux](../../includes/app-service-linux.md)] 

<a name="prereq"></a>
## <a name="prerequisites"></a>Forudsætninger

- [Node.js]
- [Bower]
- [Yeoman]
- [Ciffer]
- [Azure CLI]
- En Microsoft Azure-konto. Hvis du ikke har en konto, kan du [tilmelde dig en gratis prøveversion] eller [aktivere dine Visual Studio abonnement fordele].

## <a name="create-and-deploy-a-simple-nodejs-web-app"></a>Oprette og installere en simpel Node.js WebApp

1. Åbn kommandolinjen terminal efter eget valg og installere den [Express generator for Yeoman].

        npm install -g generator-express

2. `CD`til en arbejdsmappe og oprette en hurtig app ved hjælp af følgende syntaks:

        yo express
        
    Vælg de følgende indstillinger, når du bliver bedt om:  

    `? Would you like to create a new directory for your project?`**Ja**  
    `? Enter directory name`**{programnavn}**  
    `? Select a version to install:`**MVC**  
    `? Select a view engine to use:`**Jade**  
    `? Select a css preprocessor to use (Sass Requires Ruby):`**Ingen**  
    `? Select a database to use:`**Ingen**  
    `? Select a build tool to use:`**Grunt**

3. `CD`til din nye app rodmappe og starte den at sikre, at der køres i dit udviklingsmiljø:

        npm start

    I din browser skal du gå til <http://localhost:3000> at sikre dig, at du kan se Express hjemmeside. Når du har bekræftet din app kører korrekt, kan du bruge `Ctrl-C` at stoppe den.
    
1. Ændre til ASM tilstand, og log på Azure (du skal [Azure CLI](#prereq)):

        azure config mode asm
        azure login

    Følg prompten for at fortsætte med logon i en browser med en Microsoft-konto, der indeholder dit Azure-abonnement.

2. Kontrollér, at du stadig er i rodmappen på din app og derefter oprette App Service app ressourcen i Azure med et entydigt appnavn til den næste kommando. For eksempel: http://{appname}.azurewebsites.net

        azure site create --git {appname}

    Følg bliver bedt om at vælge en Azure område skal installeres til. Hvis du aldrig har konfigureret ciffer/FTP-installation legitimationsoplysninger for abonnementet Azure, skal du også bedt om at oprette dem..

3. Åbn filen./config/config.js i roden af dit program og ændre fremstilling porten til `process.env.port`; din `production` egenskab i den `config` objekt skal se ud som i følgende eksempel:

        production: {
            root: rootPath,
            app: {
                name: 'express1'
            },
            port: process.env.port,
        }

    Dette betyder, at din Node.js app svare Web anmodninger på standardporten pågældende iisnode lytter.
    
4. Åbn./package.json, og Tilføj den `engines` egenskab til at [angive den ønskede Node.js version](#version).

        "engines": {
            "node": "6.6.0"
        }, 

4. Gemme dine ændringer, og derefter bruge ciffer skal installeres din app til Azure:

        git add .
        git commit -m "{your commit message}"
        git push azure master

    Express generator indeholder allerede en .gitignore-fil, så dine `git push` ikke forbruge båndbredde forsøger at overføre node_modules / mappe.

5. Til sidst skal starte dit live Azure-app i browseren:

        azure site browse

    Du bør nu se din Node.js online kører direkte i Azure App-tjeneste.
    
    ![Eksempel på navigerer til det installerede program.][deployed-express-app]

## <a name="update-your-nodejs-web-app"></a>Opdatere din Node.js online

Hvis du vil foretage opdateringer til din Node.js online, der kører i App-tjeneste, skal du køre `git add`, `git commit`, og `git push` som du gjorde, da du installerede først din online.
     
## <a name="how-app-service-deploys-your-nodejs-app"></a>Hvordan App Service installerer din Node.js-app

Azure App Service bruges [iisnode] til at køre Node.js apps. Azure CLI og Kudu program (ciffer installation) arbejder sammen for at give dig en strømlinet oplevelse, når du udvikle og installere Node.js apps fra kommandolinjen. 

- `azure site create --git`genkender almindelige Node.js mønstret for server.js eller app.js og opretter en iisnode.yml i din rodmappe. Du kan bruge denne fil til at tilpasse iisnode.
- På `git push azure master`, Kudu automatisere følgende installationsopgaver:

    - Hvis package.json er i roden lager, skal du køre `npm install --production`.
    - Oprette en Web.config for iisnode, der peger på start scriptet i package.json (fx server.js eller app.js).
    - Tilpasse Web.config for at klar din app til fejlfinding med Node inspektion.
    
## <a name="use-a-nodejs-framework"></a>Bruge en Node.js ramme

Hvis du bruger en populære Node.js framework såsom [Sails.js] [ SAILSJS] eller [MEAN.js] [ MEANJS] for at udvikle apps, du kan installere dem til App-tjenesten. Populære Node.js strukturer har deres bestemte Strict, og deres pakkeafhængigheder beholde få opdateret. App Service gør imidlertid loggene stdout og stderr tilgængelig for dig, så du kan vide præcis hvad sker der med din app og foretage ændringer i overensstemmelse hermed. Se [få stdout og stderr logfiler fra iisnode](#iisnodelog)kan finde flere oplysninger.

Følgende selvstudier viser dig, hvordan du arbejder med en bestemt ramme i App-tjenesten:

- [Installere en Sails.js WebApp til Azure App-tjenesten]
- [Oprette et Node.js chat-program med Socket.IO i Azure App Service]
- [Sådan bruges io.js med Azure App Service Web Apps]

<a name="version"></a>
## <a name="use-a-specific-nodejs-engine"></a>Bruge et bestemt Node.js program

Du kan se App-tjeneste til at bruge et bestemt Node.js program, som du normalt ville gøre i package.json i arbejdsprocessen typisk.
Eksempel:

    "engines": {
        "node": "6.6.0"
    }, 

Kudu installation program bestemmer, hvilke Node.js program du bruger i følgende rækkefølge:

- Se først på iisnode.yml at se, om `nodeProcessCommandLine` er angivet. Hvis Ja, skal du bruge.
- Dernæst skal se på package.json at se, om `"node": "..."` er angivet i den `engines` objekt. Hvis Ja, skal du bruge.
- Vælg en Node.js standardversion som standard.

>[AZURE.NOTE] Det anbefales, at du eksplicit definerer det ønskede Node.js program. Node.js standardversionen kan ændre, og du kan få fejl i din Azure online, fordi Node.js standardversionen ikke er relevante for din app.

<a name="iisnodelog"></a>
## <a name="get-stdout-and-stderr-logs-from-iisnode"></a>Få stdout og stderr logfiler fra iisnode

For at læse iisnode logge skal du følge disse trin.

> [AZURE.NOTE] Når du har udført disse trin, findes logfiler muligvis ikke, indtil der opstår en fejl.

1. Åbn filen iisnode.yml, som indeholder et Azure CLI.

2. Angive de to følgende parametre: 

        loggingEnabled: true
        logDirectory: iisnode
    
    Sammen, de Fortæl iisnode i App-tjeneste til at sætte dens stdout og stderror output på D:\home\site\wwwroot\**iisnode** directory.

3. Gemme dine ændringer, og derefter overføre ændringerne til Azure med ciffer følgende kommandoer:

        git add .
        git commit -m "{your commit message}"
        git push azure master
   
    iisnode er nu konfigureret. De næste trin viser, hvordan du få adgang til disse logfiler.
     
4. Adgang til konsollen Kudu fejlfinding for din app, som er placeret i din browser:

        https://{appname}.scm.azurewebsites.net/DebugConsole 

    Denne URL-adresse er forskellig fra URL-adressen til web-app ved tilføjelse af "*.scm.*" til DNS-navn. Hvis du udelader pågældende tilføjelse til URL-adressen, får du en 404-fejl.

5. Gå til D:\home\site\wwwroot\iisnode

    ![Navigere til placeringen af logfiler iisnode.][iislog-kudu-console-find]

6. Klik på ikonet **Rediger** for den logfil, du vil læse. Du kan også klikke på **Hent** eller **slette** Hvis du vil have.

    ![Åbning af en iisnode logfilen.][iislog-kudu-console-open]

    Du kan nu se loggen for at hjælpe dig med at foretage fejlfinding af installationen af App Service.
    
    ![At undersøge en iisnode logfil.][iislog-kudu-console-read]

## <a name="debug-your-app-with-node-inspector"></a>Fejlfinding af din app med Node inspektion

Hvis du bruger Node inspektion foretage fejlfinding af dine Node.js apps, kan du bruge den til din direkte App Service-app. Node inspektion er forudinstalleret i iisnode installationen til App-tjenesten. Og hvis du installerer via ciffer, den automatisk oprettede Web.config fra Kudu indeholder allerede alle den konfiguration, skal du aktivere Node inspektion.

Hvis du vil aktivere Node inspektion, skal du følge disse trin:

1. Åbn iisnode.yml i din lager rod, og Angiv følgende parametre: 

        debuggingEnabled: true
        debuggerExtensionDll: iisnode-inspector.dll

3. Gemme dine ændringer, og derefter overføre ændringerne til Azure med ciffer følgende kommandoer:

        git add .
        git commit -m "{your commit message}"
        git push azure master
   
4. Nu bare gå til din app start-fil som angivet af start scriptet i din package.json med/Debug føjet til URL-adressen. For eksempel

        http://{appname}.azurewebsites.net/server.js/debug
    
    Eller,
    
        http://{appname}.azurewebsites.net/app.js/debug

## <a name="more-resources"></a>Flere ressourcer

- [Angive en Node.js version i et Azure-program](../nodejs-specify-node-version-azure-apps.md)
- [Bedste praksis og fejlfinding i forbindelse med vejledning til Node.js programmer på Azure](app-service-web-nodejs-best-practices-and-troubleshoot-guide.md)
- [Sådan foretages fejlfinding en Node.js WebApp i Azure App Service](web-sites-nodejs-debug.md)
- [Brug af Node.js moduler med Azure-programmer](../nodejs-use-node-modules-azure-apps.md)
- [Azure App Service Webapps: Node.js](http://blogs.msdn.com/b/silverlining/archive/2012/06/14/windows-azure-websites-node-js.aspx)
- [Node.js Developer Center](/develop/nodejs/)
- [Komme i gang med webapps i Azure App Service](app-service-web-get-started.md)
- [Udforske konsollen særdeles hemmeligt Kudu fejlfinding]

<!-- URL List -->

[Azure CLI]: ../xplat-cli-install.md
[Azure App Service]: ../app-service/app-service-value-prop-what-is.md
[aktivere dine Visual Studio abonnement fordele]: http://go.microsoft.com/fwlink/?LinkId=623901
[Bower]: http://bower.io/
[Oprette et Node.js chat-program med Socket.IO i Azure App Service]: ./web-sites-nodejs-chat-app-socketio.md
[Installere en Sails.js WebApp til Azure App-tjenesten]: ./app-service-web-nodejs-sails.md
[Udforske konsollen særdeles hemmeligt Kudu fejlfinding]: /documentation/videos/super-secret-kudu-debug-console-for-azure-web-sites/
[Express generator til Yeoman]: https://github.com/petecoop/generator-express
[Ciffer]: http://www.git-scm.com/downloads
[Sådan bruges io.js med Azure App Service Web Apps]: ./web-sites-nodejs-iojs.md
[iisnode]: https://github.com/tjanczuk/iisnode/wiki
[MEANJS]: http://meanjs.org/
[Node.js]: http://nodejs.org
[SAILSJS]: http://sailsjs.org/
[tilmelde dig en gratis prøveversion]: http://go.microsoft.com/fwlink/?LinkId=623901
[web app]: ./app-service-web-overview.md
[Yeoman]: http://yeoman.io/

<!-- IMG List -->

[deployed-express-app]: ./media/app-service-web-nodejs-get-started/deployed-express-app.png
[iislog-kudu-console-find]: ./media/app-service-web-nodejs-get-started/iislog-kudu-console-navigate.png
[iislog-kudu-console-open]: ./media/app-service-web-nodejs-get-started/iislog-kudu-console-open.png
[iislog-kudu-console-read]: ./media/app-service-web-nodejs-get-started/iislog-kudu-console-read.png
