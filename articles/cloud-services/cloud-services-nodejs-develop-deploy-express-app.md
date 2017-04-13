<properties 
    pageTitle="Web App med Express (Node.js) | Microsoft Azure" 
    description="Et selvstudium, der bygger på skyen service selvstudiet, og viser, hvordan du bruger modulet Express." 
    services="cloud-services" 
    documentationCenter="nodejs" 
    authors="rmcmurray" 
    manager="wpickett" 
    editor=""/>

<tags 
    ms.service="cloud-services" 
    ms.workload="tbd" 
    ms.tgt_pltfrm="na" 
    ms.devlang="nodejs" 
    ms.topic="article" 
    ms.date="08/11/2016" 
    ms.author="robmcm"/>






# <a name="build-a-nodejs-web-application-using-express-on-an-azure-cloud-service"></a>Opbygge et Node.js webprogram ved hjælp af Express på en Azure skybaseret tjeneste

Node.js omfatter et minimum af funktionalitet i core runtime.
Udviklere bruger ofte 3 part moduler til at give ekstra funktionalitet, når udvikling af et Node.js program. Du skal oprette et nyt program, der bruger modulet [Express][] , som indeholder en MVC ramme til oprettelse af Node.js webprogrammer i dette selvstudium.

Et skærmbillede af det færdige program er nedenfor:

![En webbrowser, viser Velkommen til Express i Azure](./media/cloud-services-nodejs-develop-deploy-express-app/node36.png)

##<a name="create-a-cloud-service-project"></a>Oprette en skybaseret tjeneste projekt

Udfør følgende trin for at oprette et nyt skyen service projekt med navnet 'expressapp':

1. Søge efter **Windows PowerShell**fra **Menuen Start** eller **Startskærmbilledet**. Til sidst skal du højreklikke på **Windows PowerShell** , og vælg **Kør som Administrator**.

    ![Azure PowerShell-ikon](./media/cloud-services-nodejs-develop-deploy-express-app/azure-powershell-start.png)

    [AZURE.INCLUDE [install-dev-tools](../../includes/install-dev-tools.md)]

2. Skifte til den **c:\\node** directory og derefter angive følgende kommandoer til at oprette en ny løsning med navnet **expressapp** og en web rolle med navnet **WebRole1**:

        PS C:\node> New-AzureServiceProject expressapp
        PS C:\Node\expressapp> Add-AzureNodeWebRole
        PS C:\Node\expressapp> Set-AzureServiceProjectRole WebRole1 Node 0.10.21

    > [AZURE.NOTE] **Tilføj AzureNodeWebRole** som standard bruger en ældre version af Node.js. Sætningen **Sæt AzureServiceProjectRole** får Azure bruge v0.10.21 af Node.  Bemærk parametrene, der er store og små bogstaver.  Du kan kontrollere den korrekte version af Node.js er valgt ved at markere egenskaben **programmer** i **WebRole1\package.json**.

##<a name="install-express"></a>Installere Express

1. Installere Express generator ved at udstede følgende kommando:

        PS C:\node\expressapp> npm install express-generator -g

    Output fra kommandoen npm bør se nogenlunde sådan resultatet nedenfor. 

    ![Windows PowerShell viser output fra npm installere hurtig kommando.](./media/cloud-services-nodejs-develop-deploy-express-app/express-g.png)

2. Skifte til mappen **WebRole1** og bruge kommandoen udtrykkelig til at oprette et nyt program:

        PS C:\node\expressapp\WebRole1> express

    Du bliver bedt om at overskrive tidligere programmet. Angiv **y** , eller klik på **Ja** for at fortsætte. Express genererer app.js filen og en mappestrukturen opbygning af dit program.

    ![Output fra kommandoen udtrykkelig](./media/cloud-services-nodejs-develop-deploy-express-app/node23.png)


5.  Skriv følgende kommando for at installere yderligere afhængigheder, der er defineret i filen package.json skal:

        PS C:\node\expressapp\WebRole1> npm install

    ![Output fra npm installere kommandoen](./media/cloud-services-nodejs-develop-deploy-express-app/node26.png)

6.  Brug følgende kommando til at kopiere filen **placering/www** til **server.js**. Dette er så skytjenesten kan finde indgangspunktet til dette program.

        PS C:\node\expressapp\WebRole1> copy bin/www server.js

    Når denne kommando er fuldført, bør du have en **server.js** fil i mappen WebRole1.

7.  Ændre **server.js** for at fjerne en af de '.' tegn fra følgende linje.

        var app = require('../app');

    Linjen skal vises som følger efter du har foretaget denne ændring.

        var app = require('./app');

    Denne ændring er påkrævet da vi har flyttet filen (tidligere **placering/www**,) til den samme mappe som den app-fil, der kræves. Når du har foretaget denne ændring, gemme filen **server.js** .

8.  Brug følgende kommando til at køre programmet i Azure emulatoren:

        PS C:\node\expressapp\WebRole1> Start-AzureEmulator -launch

    ![En webside, der indeholder Velkommen til express.](./media/cloud-services-nodejs-develop-deploy-express-app/node28.png)

## <a name="modifying-the-view"></a>Ændre visningen

Nu ændre visningen for at få vist meddelelsen "Velkommen til Express i Azure".

1.  Skriv følgende kommando for at åbne filen index.jade:

        PS C:\node\expressapp\WebRole1> notepad views/index.jade

    ![Indholdet af filen index.jade.](./media/cloud-services-nodejs-develop-deploy-express-app/getting-started-19.png)

    Jade er standardprogrammet visning bruges af Express programmer. Du kan finde flere oplysninger om Jade visning-program, [http://jade-lang.com][].

2.  Ændre den sidste linje tekst ved at tilføje **i Azure**.

    ![Filen index.jade den sidste linje læser: p Velkommen til \#{titel} i Azure](./media/cloud-services-nodejs-develop-deploy-express-app/node31.png)

3.  Gem filen, og Afslut Notesblok.

4.  Opdatere din browser, og du kan se ændringerne.

    ![Et browservindue, siden indeholder Velkommen til Express i Azure](./media/cloud-services-nodejs-develop-deploy-express-app/node32.png)

Brug cmdlet'en **Stop AzureEmulator** til at stoppe emulatoren efter tester programmet.

##<a name="publishing-the-application-to-azure"></a>Publicering af programmet til Azure

I vinduet Azure PowerShell Brug cmdlet'en **Publicer AzureServiceProject** til at installere programmet til en skybaseret tjeneste

    PS C:\node\expressapp\WebRole1> Publish-AzureServiceProject -ServiceName myexpressapp -Location "East US" -Launch

Når handlingen installationen er fuldført, åbnes og viser websiden din browser.

![En webbrowser, der viser siden Express. URL-adressen angiver det nu er hostet på Azure.](./media/cloud-services-nodejs-develop-deploy-express-app/node36.png)

## <a name="next-steps"></a>Næste trin

Du kan finde yderligere oplysninger finder [Node.js Developer Center](/develop/nodejs/).

  [Node.js Web Application]: http://www.windowsazure.com/develop/nodejs/tutorials/getting-started/
  [Express]: http://expressjs.com/
  [http://jade-lang.com]: http://jade-lang.com

 
