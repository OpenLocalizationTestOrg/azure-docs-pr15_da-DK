<properties
    pageTitle="Installere en Sails.js WebApp til Azure App-tjenesten"
    description="Lær, hvordan du installerer et Node.js program Azure App Service. Dette selvstudium viser, hvordan du installere en Sails.js WebApp."
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
    ms.topic="article"
    ms.date="09/23/2016"
    ms.author="cephalin"/>

# <a name="deploy-a-sailsjs-web-app-to-azure-app-service"></a>Installere en Sails.js WebApp til Azure App-tjenesten

Dette selvstudium viser, hvordan du installere en app til en Sails.js til Azure App-tjenesten. I processen, kan du glean nogle generel viden om, hvordan du konfigurerer din Node.js app til at køre i App-tjeneste. 

Du bør have kendskab til Sails.js. Dette selvstudium er ikke beregnet til at hjælpe dig med problemer relateret til kører Sail.js Generelt.


## <a name="prerequisites"></a>Forudsætninger

- [Node.js](https://nodejs.org/)
- [Sails.js](http://sailsjs.org/get-started)
- [Ciffer](http://www.git-scm.com/downloads)
- [Azure CLI](../xplat-cli-install.md)
- En Microsoft Azure-konto. Hvis du ikke har en konto, kan du [tilmelde dig en gratis prøveversion](/pricing/free-trial/?WT.mc_id=A261C142F) eller [aktivere dine Visual Studio abonnement fordele](/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A261C142F).

>[AZURE.NOTE] Gå til [Prøve App Service](http://go.microsoft.com/fwlink/?LinkId=523751)for at se Azure App Service i aktion før tilmelding til en Azure-konto. Der skal du straks kan oprette en forbigående starter app i App-tjeneste – ingen kreditkort er påkrævet, ingen forpligtelser.

## <a name="step-1-create-a-sailsjs-app-locally"></a>Trin 1: Oprette en Sails.js app lokalt

Først skal hurtigt oprette en standard Sails.js app i dit udviklingsmiljø ved at følge disse trin:

1. Åbn den kommandolinjen terminal efter eget valg og `CD` til en arbejdsmappe.

2. Oprette en Sails.js-app, og kør den:

        sails new <appname>
        cd <appname>
        sails lift

    Kontrollér, at du kan navigere til standardstartsiden på http://localhost:1377.

## <a name="step-2-create-the-azure-app-resource"></a>Trin 2: Oprette ressourcen Azure-app

Opret derefter App Service ressourcen i Azure. Du skal installere din Sails.js app til den senere.

1. Log på Azure synes godt om så:
1. Ændre til ASM tilstand i den samme terminal, og log på Azure:

        azure config mode asm
        azure login

    Følg prompten for at fortsætte med logon i en browser med en Microsoft-konto, der indeholder dit Azure-abonnement.

2. Kontrollér, at du er stadig i projektet Sails.js rodmappe. Oprette App Service app ressourcen i Azure med et entydigt appnavn til den næste kommando. Din online URL-adressen er http://&lt;Programnavn >. azurewebsites.net.

        azure site create --git <appname>

    Følg bliver bedt om at vælge en Azure område skal installeres til. Hvis du aldrig har konfigureret ciffer/FTP-installation legitimationsoplysninger for abonnementet Azure, skal du også bedt om at oprette dem..

    Når der oprettes App Service app ressource:

    - Sails.js app er ciffer-initialiseret,
    - Din lokale ciffer-initialiseret lager er forbundet med den nye App Service-app som en remote, ciffer serviceprioriteten med navnet "azure", og
    - Og iisnode.yml filen er oprettet i din rodmappe. Du kan bruge denne fil til at konfigurere [iisnode](https://github.com/tjanczuk/iisnode), som App Service bruger til at køre Node.js apps.

## <a name="step-3-configure-and-deploy-your-sailsjs-app"></a>Trin 3: Konfigurere og installere din Sails.js-app

 Arbejde med en Sails.js app i App Service består af tre hovedtrin:

 - Konfigurere din app til det til at køre i App-tjeneste
 - Installere den på App Service
 - Læs stderr og stdout logfiler til fejlfinding af problemer med installation

Følg disse trin:

1. Åbn den nye iisnode.yml-fil i din rodmappe og Tilføj de følgende to linjer:

        loggingEnabled: true
        logDirectory: iisnode

    Logføring er nu aktiveret for iisnode. Du kan finde flere oplysninger om, hvordan det fungerer, kan du se  [få stdout og stderr logfiler fra iisnode](app-service-web-nodejs-get-started.md#iisnodelog).

2. Åbn config/env/production.js for at konfigurere dit produktionsmiljø, og angiv `port` og `hookTimeout`:

        module.exports = {

            // Use process.env.port to handle web requests to the default HTTP port
            port: process.env.port,
            // Increase hooks timout to 30 seconds
            // This avoids the Sails.js error documented at https://github.com/balderdashy/sails/issues/2691
            hookTimeout: 30000,

            ...
        };

    Du kan finde dokumentation for disse indstillinger for søgekonfiguration i  [Sails.js dokumentation](http://sailsjs.org/documentation/reference/configuration/sails-config).

    Derefter skal du kontrollere, at [Grunt](https://www.npmjs.com/package/grunt) er kompatible med Azures netværksdrev. Grunt versioner, der er mindre end 1.0.0 du bruger en forældet [glob](https://www.npmjs.com/package/glob) pakke (mindre end 5.0.14), som ikke understøtter netværksdrev. 

3. Åbn package.json og ændre den `grunt` version til `1.0.0` og fjerne alle `grunt-*` pakker. Din `dependencies` egenskaben ser sådan ud:

        "dependencies": {
            "ejs": "<leave-as-is>",
            "grunt": "1.0.0",
            "include-all": "<leave-as-is>",
            "rc": "<leave-as-is>",
            "sails": "<leave-as-is>",
            "sails-disk": "<leave-as-is>",
            "sails-sqlserver": "<leave-as-is>"
        },

3. Tilføj følgende i package.json, `engines` egenskab, der skal angive Node.js versionen til, vi ønsker.

        "engines": {
            "node": "6.6.0"
        },

6. Gemme ændringerne og afprøve dine ændringer til at sikre, at din app stadig kører lokalt. Du kan slette den `node_modules` mappe og derefter køre:

        npm install
        sails lift

4. Nu, bruge ciffer skal installeres din app til Azure:

        git add .
        git commit -m "<your commit message>"
        git push azure master

5. Til sidst skal startes kun din direkte Azure-app i browseren:

        azure site browse

    Du bør nu se samme Sails.js hjemmeside.
    
    ![](./media/app-service-web-nodejs-sails/sails-in-azure.png)

## <a name="troubleshoot-your-deployment"></a>Fejlfinding i forbindelse med din installation

Hvis programmet Sails.js mislykkes eller anden grund i App-tjeneste, kan du finde loggene stderr til hjælp til fejlfinding af den.
Se [få stdout og stderr logfiler fra iisnode](app-service-web-nodejs-sails.md#iisnodelog)kan finde flere oplysninger.
Hvis det er startet korrekt, skal stdout loggen vise du kender meddelelsen:

                .-..-.

    Sails              <|    .-..-.
    v0.12.4             |\
                        /|.\
                        / || \
                    ,'  |'  \
                    .-'.-==|/_--'
                    `--'-------' 
    __---___--___---___--___---___--___
    ____---___--___---___--___---___--___-__

    Server lifted in `D:\home\site\wwwroot`
    To see your app, visit http://localhost:\\.\pipe\c775303c-0ebc-4854-8ddd-2e280aabccac
    To shut down Sails, press <CTRL> + C at any time.

Du kan styre granularitet loggene stdout i filen [config/log.js](http://sailsjs.org/#!/documentation/concepts/Logging) . 

## <a name="connect-to-a-database-in-azure"></a>Oprette forbindelse til en database i Azure

Hvis du vil oprette forbindelse til en database i Azure, du opretter databasen efter eget valg i Azure, såsom Azure SQL-Database, MySQL, MongoDB, Azure (Redis) cachen, osv., og bruge tilsvarende [datastore adapter](https://github.com/balderdashy/sails#compatibility) til at oprette forbindelse til den. Trinnene i dette afsnit viser, hvordan du opretter forbindelse til en MySQL-database i Azure.

1. Følg den selvstudium [her](../store-php-create-mysql-database.md) for at oprette en MySQL-database i Azure.

2. Fra din kommandolinjen terminal skal du installere MySQL-adapter:

        npm install sails-mysql --save

3. Åbn config/connections.js og tilføje følgende connection-objektet på listen: 

        mySql: {
            adapter: 'sails-mysql',
            user: process.env.dbuser,
            password: process.env.dbpassword,
            host: process.env.dbhost, 
            database: process.env.dbname,
            options: {
                encrypt: true
            }
        },

4. For hver miljøvariablen (`process.env.*`), skal du angive den i App-tjeneste. For at gøre dette skal du køre følgende kommandoer fra din terminal. Alle forbindelsesoplysninger, skal du er i portalen Azure (se [oprette forbindelse til din MySQL-database](../store-php-create-mysql-database.md#connect)).

        azure site appsetting add dbuser="<database user>"
        azure site appsetting add dbpassword="<database password>"
        azure site appsetting add dbhost="<database hostname>"
        azure site appsetting add dbname="<database name>"
        
    Hvordan man anbringer dine indstillinger i indstillinger for Azure app bevarer følsomme data ud af dine versionsstyring (ciffer). Derefter skal konfigurere du din udviklingsmiljø for at bruge de samme forbindelsesoplysninger.

4. Åbn config/local.js og føje forbindelser objektet:

        connections: {
            mySql: {
                user: "<database user>",
                password: "<database password>",
                host: "<database hostname>", 
                database: "<database name>",
            },
        },
    
    Denne konfiguration tilsidesætter indstillingerne i filen config/connections.js til det lokale miljø. Denne fil er udeladt af standard .gitignore i dit projekt, så det ikke bliver gemt i ciffer. Nu er du kunne oprette forbindelse til din MySQL-database, både fra din Azure online og fra din lokale udviklingsmiljø.

4. Åbn config/env/production.js for at konfigurere dit produktionsmiljø, og Tilføj følgende `models` objekt:

        models: {
            connection: 'mySql',
            migrate: 'safe'
        },

4. Åbn config/env/development.js for at konfigurere dit udviklingsmiljø, og Tilføj følgende `models` objekt:

        models: {
            connection: 'mySql',
            migrate: 'alter'
        },

    `migrate: 'alter'`kan du bruge Databasefunktioner overførsel til at oprette og opdatere dine databasetabeller i din MySQL nemt. Dog `migrate: 'safe'` bruges til Azure (fremstilling)-miljø, fordi Sails.js ikke tillader dig at bruge `migrate: 'alter'` i et produktionsmiljø (se  [Sails.js dokumentation](http://sailsjs.org/documentation/concepts/models-and-orm/model-settings)).

4. Fra terminal skal [oprette](http://sailsjs.org/documentation/reference/command-line-interface/sails-generate) en Sails.js [grundrids API](http://sailsjs.org/documentation/concepts/blueprints) , som du normalt ville gøre, derefter køres `sails lift` til at oprette databasen med Sails.js Databasemigrering. Eksempel:

         sails generate api mywidget
         sails lift

    Den `mywidget` model, der genereres af denne kommando er tom, men vi kan bruge det til at vise, at vi har database connectivity.
    Når du kører `sails lift`, programmet opretter manglende tabeller til modellerne din app bruger.

6. Få adgang til grundrids API, du lige har oprettet i browseren. Eksempel:

        http://localhost:1337/mywidget/create
    
    API skal returnere den oprettede post tilbage til dig i browservinduet, hvilket betyder, at databasen er oprettet.

        {"id":1,"createdAt":"2016-09-23T13:32:00.000Z","updatedAt":"2016-09-23T13:32:00.000Z"}

5. Nu, Overfør dine ændringer til Azure, og gå til din app for at sikre, at den fungerer stadig.

        git add .
        git commit -m "<your commit message>"
        git push azure master
        azure site browse

6. Få adgang til grundrids API for din Azure online. Eksempel:

        http://<appname>.azurewebsites.net/mywidget/create

    Hvis API returnerer en anden ny adresse, taler din Azure online til MySQL-database.

## <a name="more-resources"></a>Flere ressourcer

- [Komme i gang med Node.js webapps i Azure App Service](app-service-web-nodejs-get-started.md)
- [Brug af Node.js moduler med Azure-programmer](../nodejs-use-node-modules-azure-apps.md)
