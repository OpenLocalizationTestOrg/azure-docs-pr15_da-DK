<properties
    pageTitle="Node.js API-app i Azure App Service | Microsoft Azure"
    description="Lær at oprette en Node.js RESTful API og installere den på en API-app i Azure App Service."
    services="app-service\api"
    documentationCenter="node"
    authors="bradygaster"
    manager="wpickett"
    editor=""/>

<tags
    ms.service="app-service-api"
    ms.workload="web"
    ms.tgt_pltfrm="na"
    ms.devlang="node"
    ms.topic="get-started-article"
    ms.date="05/26/2016"
    ms.author="rachelap"/>

# <a name="build-a-nodejs-restful-api-and-deploy-it-to-an-api-app-in-azure"></a>Opbygge en Node.js RESTful API og anvende den til en API-app i Azure

[AZURE.INCLUDE [app-service-api-get-started-selector](../../includes/app-service-api-get-started-selector.md)]

Dette selvstudium viser, hvordan du opretter en simpel [Node.js](http://nodejs.org) API og installerer det til en [API app](app-service-api-apps-why-best-platform.md) i [Azure App Service](../app-service/app-service-value-prop-what-is.md) ved hjælp af [ciffer](http://git-scm.com). Du kan bruge en hvilken som helst operativsystem, som kan køre Node.js, og du skal gøre alt dit arbejde, ved hjælp af kommandolinjeværktøjer som cmd.exe eller bash.

## <a name="prerequisites"></a>Forudsætninger

1. Microsoft Azure-konto ([åbner en gratis konto](https://azure.microsoft.com/pricing/free-trial/))
1. [Node.js](http://nodejs.org) installeret (i dette eksempel forudsætter, at du har Node.js version 4.2.2)
2. [Ciffer](https://git-scm.com/) installeret
1. [GitHub](https://github.com/) konto

Mens App Service understøtter mange måder til at anvende din kode til en API-app, dette selvstudium viser metoden ciffer og forudsætter, at du har grundlæggende kendskab til at arbejde med ciffer. Du kan finde oplysninger om andre installationsmetoder [Implementer din app til Azure App-tjenesten](../app-service-web/web-sites-deploy.md).

## <a name="get-the-sample-code"></a>Få eksempelkoden

1. Åbn en kommando linje brugergrænseflade, der kan køre Node.js og ciffer kommandoer.

1. Gå til en mappe, du kan bruge til en lokal ciffer lager og Klon [GitHub lager, der indeholder eksempelkoden](https://github.com/Azure-Samples/app-service-api-node-contact-list).

        git clone https://github.com/Azure-Samples/app-service-api-node-contact-list.git

    Eksempel API indeholder to slutpunkter: en Get-anmodning til `/contacts` returnerer en liste over navne og mailadresser i JSON-format, mens `/contacts/{id}` returnerer kun den markerede kontaktperson.

## <a name="scaffold-auto-generate-nodejs-code-based-on-swagger-metadata"></a>Scaffold (automatisk genererer) Node.js kode baseret på Swagger metadata

[Swagger](http://swagger.io/) er et filformat til metadata, der beskriver en RESTful API. Azure App Service har [indbygget understøttelse af Swagger metadata](app-service-api-metadata.md). Dette afsnit af selvstudiet modeller en API udvikling arbejdsproces, du opretter Swagger metadata først og bruge den til at scaffold (automatisk genererer) server kode for API. 

>[AZURE.NOTE] Hvis du ikke kan se, hvordan du scaffold Node.js kode fra en Swagger metadata-fil, kan du springe dette afsnit. Hvis du vil installere lige eksempelkode til en ny API-app, skal du gå direkte til afsnittet [oprette en API-app i Azure](#createapiapp) .

### <a name="install-and-execute-swaggerize"></a>Installere og udføre Swaggerize

1. Udføre følgende kommandoer for at installere **yo** og **generator swaggerize** NPM modulerne globalt.

        npm install -g yo
        npm install -g generator-swaggerize

    Swaggerize er et værktøj, som genererer server kode for en API, der er beskrevet i filen Swagger metadata. Den Swagger-fil, du skal bruge hedder *api.json* og er placeret i mappen *start* af lager du klonet.

2. Gå til mappen *start* , og udfør derefter de `yo swaggerize` kommandoen. Swaggerize er Bed en række spørgsmål.  Angiv "ContactList" for **stien til swagger dokument**til, **Hvad du skal ringe til dette projekt**, Angiv "api.json", og Angiv "express" for **Express, glade, eller Restify**.

        yo swaggerize

    ![Swaggerize kommandolinjen](media/app-service-api-nodejs-api-app/swaggerize-command-line.png)
    
    **Bemærk**: Hvis du støder på en fejl i dette trin, er næste trin beskrives det, hvordan at løse problemet.

    Swaggerize opretter en mappe i programmet, scaffolds programmer og af konfigurationsfiler, og genererer en **package.json** fil. Hurtig visning program bruges til at generere Swagger Hjælp-siden.  

3. Hvis den `swaggerize` kommandoen mislykkes med en "uventet token" eller "ugyldig escape-sekvens" fejl, ret årsagen til fejlen ved at redigere filen genererede *package.json* . I den `regenerate` linje `scripts`, ændre omvendt skråstreg, der står foran *api.json* til en skråstreg, så linjen, der ser ud som i følgende eksempel:

        "regenerate": "yo swaggerize --only=handlers,models,tests --framework express --apiPath config/api.json"

1. Gå til den mappe, der indeholder den scaffolded kode (i dette tilfælde undermappen */start/ContactList* ).

1. Køre `npm install`.
    
        npm install
        
2. Installere modulet **jsonpath** NPM. 

        npm install --save jsonpath
        
    ![Jsonpath installation](media/app-service-api-nodejs-api-app/jsonpath-install.png)

1. Installere modulet **swaggerize brugergrænsefladen** NPM. 

        npm install --save swaggerize-ui
        
    ![Swaggerize brugergrænseflade installation](media/app-service-api-nodejs-api-app/swaggerize-ui-install.png)

### <a name="customize-the-scaffolded-code"></a>Tilpasse scaffolded koden

1. Kopiere mappen **bibliotek** fra mappen **start** i mappen **ContactList** oprettet af scaffolder. 

1. Erstat koden i filen **handlers/contacts.js** med følgende kode. 

    Denne kode bruger de JSON-data, der er gemt i filen **lib/contacts.json** , som leveres af **lib/contactRepository.js**. Den nye contacts.js kode reagerer på HTTP-anmodninger om at hente alle kontaktpersoner og returnere dem som en JSON-data. 

        'use strict';
        
        var repository = require('../lib/contactRepository');
        
        module.exports = {
            get: function contacts_get(req, res) {
                res.json(repository.all())
            }
        };

1. Erstat koden i filen **handlers/contacts/{id}.js** med fofllowing koden. 

        'use strict';

        var repository = require('../../lib/contactRepository');
        
        module.exports = {
            get: function contacts_get(req, res) {
                res.json(repository.get(req.params['id']));
            }    
        };

1. Erstat koden i **server.js** med følgende kode. 

    Ændringer i filen server.js er fremhævet med kommentarer, så du kan se de ændringer, der udføres. 

        'use strict';

        var port = process.env.PORT || 8000; // first change

        var http = require('http');
        var express = require('express');
        var bodyParser = require('body-parser');
        var swaggerize = require('swaggerize-express');
        var swaggerUi = require('swaggerize-ui'); // second change
        var path = require('path');

        var app = express();

        var server = http.createServer(app);

        app.use(bodyParser.json());

        app.use(swaggerize({
            api: path.resolve('./config/api.json'), // third change
            handlers: path.resolve('./handlers'),
            docspath: '/swagger' // fourth change
        }));

        // change four
        app.use('/docs', swaggerUi({
          docs: '/swagger'  
        }));

        server.listen(port, function () { // fifth and final change
        });

### <a name="test-with-the-api-running-locally"></a>Test med API kører lokalt

1. Aktivere serveren ved hjælp af den eksekverbare fil kommandolinjen i Node.js. 

        node server.js

1. Når du navigerer til **http://localhost:8000/kontakter**, du kan se JSON output fra listen over kontakter (eller du bliver bedt om at hente den, afhængigt af din browser). 

    ![Alle kontakter Api-opkald](media/app-service-api-nodejs-api-app/all-contacts-api-call.png)

1. Når du navigerer til **http://localhost:8000/kontakter/2**, får du vist den kontakt, der er repræsenteret af id den pågældende værdi.

    ![Bestemte kontakter Api opkald](media/app-service-api-nodejs-api-app/specific-contact-api-call.png)

1. Datatypen Swagger JSON leveres **via/swagger** slutpunktet:

    ![Kontakter Swagger Json](media/app-service-api-nodejs-api-app/contacts-swagger-json.png)

1. Brugergrænsefladen Swagger leveres via **/docs** slutpunkt. Du kan bruge RTF HTML-klientfunktioner til at teste din API i Brugergrænsefladen Swagger.

    ![Swagger brugergrænseflade](media/app-service-api-nodejs-api-app/swagger-ui.png)

## <a id="createapiapp"></a>Oprette en ny API-App

I dette afsnit kan du bruge Azure portal til at oprette en ny API App i Azure. Denne API app repræsenterer de Beregn ressourcer, der giver Azure for at køre din kode. I senere afsnit skal du installere din kode til den nye API-app.

1. Gå til [Azure-portalen](https://portal.azure.com/). 

1. Klik på **Ny > Web + Mobile > API App**. 

    ![Ny API app-portalen](media/app-service-api-nodejs-api-app/new-api-app-portal.png)

4. Angiv en **App-navn** , der er entydige i *azurewebsites.net* domænet, såsom NodejsAPIApp plus et tal for at gøre det entydigt. 

    Hvis navnet er eksempelvis `NodejsAPIApp`, vil være URL-adressen `nodejsapiapp.azurewebsites.net`.

    Hvis du angiver et navn, som en anden person allerede har brugt, vises der et rødt udråbstegn til højre.

6. Klik på **Ny**i rullemenuen **Ressourcegruppe** , og skriv derefter i **Ny ressource gruppenavn** "NodejsAPIAppGroup" eller et andet navn Hvis du foretrækker. 

    En [ressourcegruppe](../azure-resource-manager/resource-group-overview.md) er en samling af Azure ressourcer som API apps, databaser og FOS. Det er bedst at oprette en ny ressourcegruppe, fordi, der gør det nemt at slette alle de Azure ressourcer, som du opretter i dette selvstudium i ét trin i dette selvstudium.

4. Klik på **App Service plan/placering**, og klik derefter på **Opret ny**.

    ![Oprette App-serviceaftale](./media/app-service-api-nodejs-api-app/newappserviceplan.png)

    I følgende trin, kan du oprette en App-serviceaftale til den nye ressourcegruppe. En App-serviceaftale angiver de Beregn ressourcer, der kører din API-app på. Eksempelvis hvis du vælger det gratis niveau, kører din API-app på delt FOS, mens for nogle betalt niveauer, der køres på dedikeret FOS. Du kan finde oplysninger om App Service planer [App Service planer oversigt](../app-service/azure-web-sites-web-hosting-plans-in-depth-overview.md).

5. Angiv "NodejsAPIAppPlan" eller et andet navn, hvis du foretrækker, i bladet **App Service planlægge** .

5. Vælg den placering, der er tættest på du i rullelisten **placering** .

    Denne indstilling angiver, hvilke Azure datacenter din app kører i. Du kan vælge en hvilken som helst område og den gøre ikke en chatanmodning forskel dette selvstudium. Men et fremstilling app, du vil din server skal være så tæt som muligt på de klienter, der har adgang til den for at minimere [Ventetid](http://www.bing.com/search?q=web%20latency%20introduction&qs=n&form=QBRE&pq=web%20latency%20introduction&sc=1-24&sp=-1&sk=&cvid=eefff99dfc864d25a75a83740f1e0090).

5. Klik på **priser niveau > Vis alle > F1 gratis**.

    Dette selvstudium skal give det gratis priser niveau tilstrækkelige ydeevne.

    ![Vælge Frigør priser niveau](./media/app-service-api-nodejs-api-app/selectfreetier.png)

6. Klik på **OK**i bladet **App Service planlægge** .

7. Klik på **Opret**i bladet **API App** .

## <a name="set-up-your-new-api-app-for-git-deployment"></a>Konfigurere din nye API-app til installation af ciffer

Du skal installere din kode til API-app ved at trykke på anvendelser til et ciffer lager i Azure App-tjeneste. I dette afsnit af selvstudiet, skal oprette du legitimationsoplysninger og ciffer lager i Azure, som du skal bruge til installation.  

1. Når din API app er blevet oprettet, skal du klikke på **App Services > {din API app}** fra portalen hjemmeside. 

    På portalen viser **API App** og **Indstillinger** blade.

    ![Portalen API app og indstillinger for blade](media/app-service-api-nodejs-api-app/portalapiappblade.png)

1. Rul ned til sektionen **publicering** i bladet **Indstillinger** , og klik derefter på **installation legitimationsoplysninger**.
 
3. Angive et brugernavn og din adgangskode i bladet **angive installation legitimationsoplysninger** , og klik derefter på **Gem**.

    Du skal bruge disse legitimationsoplysninger for at publicere din Node.js kode til din API-app. 

    ![Installation legitimationsoplysninger](media/app-service-api-nodejs-api-app/deployment-credentials.png)

1. Bladet **Indstillinger** , klik på **installation kilde > Vælg kilde > lokale ciffer lager**, klik derefter på **OK**.

    ![Oprette ciffer Repo](media/app-service-api-nodejs-api-app/create-git-repo.png)

1. Når din ciffer lager er blevet oprettet blade ændringer for at få vist dine aktive installationer. Da lageret er nye, har du ingen aktive installationer på listen. 

    ![Ingen aktive installationer](media/app-service-api-nodejs-api-app/no-active-deployments.png)

1. Kopiér URL-adresse med ciffer lager. Gå til bladet for din nye App API for at gøre dette, og kig på **Essentials** del af bladet. Bemærk **ciffer Klon URL-adressen** i sektionen **Essentials** . Når du peger på denne URL-adresse, kan du se et ikon i højre side, som skal kopiere URL-adressen til din Udklipsholder. Klik på dette ikon for at kopiere URL-adressen.

    ![Få ciffer URL-adressen fra portalen](media/app-service-api-nodejs-api-app/get-the-git-url-from-the-portal.png)

    **Bemærk**: Du skal bruge den ciffer Klon URL-adresse i næste afsnit så sørg for at gemme det et sted, hvor for øjeblikket.

Nu hvor du har en API-App med et ciffer lager sikkerhedskopiere den, kan du push kode til lageret skal installeres koden til API-app. 

## <a name="deploy-your-api-code-to-azure"></a>Anvende din API-kode til Azure

I dette afsnit, du opretter en lokal ciffer lager, der indeholder din server-kode for API, og klik derefter du skubbe din kode fra lageret til lager i Azure, du oprettede tidligere.

1. Kopiér den `ContactList` mappe til en placering, du kan bruge til en ny lokal ciffer lager. Hvis du har den første del af selvstudiet, kopiere `ContactList` fra den `start` mappe. Ellers kan kopiere `ContactList` fra den `end` mappe.

1. Gå til den nye mappe i din kommandoen stregværktøj, og derefter udføre følgende kommando for at oprette en ny lokal ciffer lager. 

        git init

     ![Nye lokale ciffer Repo](media/app-service-api-nodejs-api-app/new-local-git-repo.png)

1. Udfør følgende kommando for at tilføje et ciffer remote til din API app lager. 

        git remote add azure YOUR_GIT_CLONE_URL_HERE

    **Bemærk**: Erstat strengen "YOUR_GIT_CLONE_URL_HERE" med dine egne ciffer Klon URL-adresse, du kopierede tidligere. 

1. Udføre følgende kommandoer til at oprette en disposition, der indeholder alle i din kode. 

        git add .
        git commit -m "initial revision"

    ![Ciffer Anvend Output](media/app-service-api-nodejs-api-app/git-commit-output.png)

1. Udføre kommandoen til at overføre din kode til Azure. Når du bliver spurgt om en adgangskode, Angiv den, du oprettede tidligere i portalen Azure.

        git push azure master

    Dette udløser en installation for at din API-app.  

1. Gå tilbage til bladet **installationer** for din API-app i browseren, og du kan se, at installationen bliver til virkelighed. 

    ![Installation, sker der](media/app-service-api-nodejs-api-app/deployment-happening.png)

    Samtidigt, afspejler linje kommandogrænseflade status for din installation, mens den foregår. 

    ![Node Js installation sker](media/app-service-api-nodejs-api-app/node-js-deployment-happening.png)

    Når installationen er fuldført, afspejler bladet **installationer** vellykket udrulning af ændringerne kode til din API-App. 

## <a name="test-with-the-api-running-in-azure"></a>Test med API kører i Azure
 
3. Du kan kopiere **URL-adresse** i afsnittet **Essentials** i din API App blade. 

    ![Installationen er fuldført](media/app-service-api-nodejs-api-app/deployment-completed.png)

1. Ved hjælp af en REST-API klient som Postman eller Fiddler (eller din webbrowser), Angiv URL-adressen til dine kontakter API-kald, som er den `/contacts` slutpunkt for din API-app. URL-adressen bliver`https://{your API app name}.azurewebsites.net/contacts`

    Når du udsteder en GET-anmodning til dette slutpunkt, får du JSON output fra din API-app.

    ![Postman nå Api](media/app-service-api-nodejs-api-app/postman-hitting-api.png)

2. I en browser skal du gå til den `/docs` slutpunkt til at teste Swagger Brugergrænsefladen, når det køres i Azure.

Nu hvor du har fortløbende levering wired op, kan du ændre kode og anvende dem til Azure blot ved at trykke på anvendelser til din Azure ciffer lager.

## <a name="next-steps"></a>Næste trin

Du har på dette tidspunkt er blevet oprettet en API App og installeret Node.js API kode til den. De næste selvstudium viser hvordan du kan [bruge API apps fra JavaScript klienter ved hjælp af CORS](app-service-api-cors-consume-javascript.md).
