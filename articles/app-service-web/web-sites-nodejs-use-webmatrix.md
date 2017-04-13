<properties 
    pageTitle="Opbygge og anvende en Node.js web-app til Azure ved hjælp af WebMatrix" 
    description="Et selvstudium, som lærer du at bruge WebMatrix til at udvikle et Node.js til computeren og installerer det til Azure App Service Web Apps." 
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


# <a name="build-and-deploy-a-nodejs-web-app-to-azure-using-webmatrix"></a>Opbygge og anvende en Node.js web-app til Azure ved hjælp af WebMatrix

Dette selvstudium viser, hvordan du bruger WebMatrix at udvikle et Node.js til computeren og installerer det til [Azure App Service](http://go.microsoft.com/fwlink/?LinkId=529714) Web Apps. WebMatrix er et gratis web development værktøj fra Microsoft, der indeholder alt det, du har brug for til udvikling af et websted eller web app. WebMatrix indeholder flere funktioner, som gør det nemt at bruge Node.js, herunder kode fuldførelse, færdigbyggede skabeloner og editor understøttelse af Jade, mindre, og CoffeeScript. Lær mere om [WebMatrix](https://www.microsoft.com/web/webmatrix/).

Når denne vejledning, har du en Node.js WebApp, der kører i Azure App Service.
 
Et skærmbillede af det færdige program er nedenfor:

![Azure node-websted][webmatrix-node-completed]

[AZURE.INCLUDE [create-account-and-websites-note](../../includes/create-account-and-websites-note.md)]

>[AZURE.NOTE] Hvis du vil komme i gang med Azure App Service før tilmelding til en Azure-konto, skal du gå til [Prøve App Service](http://go.microsoft.com/fwlink/?LinkId=523751), hvor du straks kan oprette en forbigående starter WebApp i App-tjeneste. Ingen kreditkort, der kræves. ingen forpligtelser.

## <a name="sign-into-azure"></a>Log på Azure

Følg disse trin for at oprette en web-app i Azure App Service.

1. Start WebMatrix
2. Hvis dette er første gang har du brugt WebMatrix, bliver du bedt om at logge på Azure.  Ellers kan du klikke på knappen **Log på** og vælg **Tilføj konto**.  Vælg for at **logge på** med din Microsoft-Account.

    ![Tilføj konto][addaccount]

3. Hvis du har tilmeldt sig til en Azure-konto, kan du logge på med din Microsoft-Account:

    ![Log på Azure][signin]  


## <a name="create-a-site-using-a-built-in-template-for-azure"></a>Oprette et websted med en indbygget skabelon til Azure

1. Klik på knappen **Ny** på startskærmen, og vælg **Skabelongalleriet** for at oprette et nyt websted fra skabelongalleriet på:

    ![Nyt websted fra skabelongalleriet][sitefromtemplate]

2. Vælg **Node** i dialogboksen **websted fra skabelon** , og vælg derefter **Hurtigt websted**. Til sidst skal du klikke på **Næste**. Hvis du mangler en hvilken som helst forudsætninger for **Express** webstedsskabelonen, bliver du bedt om at installere dem.

    ![Vælg udtrykkelig skabelon][webmatrix-templates]

3. Hvis du er logget på Azure, har du nu mulighed for at oprette en App Service WebApp til dit lokale websted.  Vælg et entydigt navn, og vælg det datacenter, hvor du vil have din App Service online, der skal oprettes: 

    ![Oprette webstedet på Azure][nodesitefromtemplateazure]
    
4. Når WebMatrix er færdig med at opbygge det lokale websted og oprette App Service web-app, vises WebMatrix IDE.

    ![WebMatrix ide][webmatrix-ide]

##<a name="publish-your-application-to-azure"></a>Publicere dit program til Azure

1. WebMatrix, klik på **Publicer** fra **Home** båndet for at få vist dialogboksen **Publicer Preview** for webstedet.

    ![publicere preview][webmatrix-node-publishpreview]

2. Klik på **Fortsæt**. Når publicering er fuldført, vises URL-adressen for App Service WebApp i bunden af WebMatrix IDE

    ![publicere fuldført][webmatrix-publish-complete]

3. Klik på linket for at åbne App Service WebApp i din browser.

    ![Express online][webmatrix-node-express-site]

##<a name="modify-and-republish-your-application"></a>Ændre og genudgive dit program

Du kan nemt ændre og genudgive dit program. Her får du en enkel ændre til overskriften i i filen **index.jade** og genudgive programmet.

1. Vælg **filer**i WebMatrix, og udvid derefter mappen **visninger** . Åbn filen **index.jade** ved at dobbeltklikke på den.

    ![WebMatrix visning index.jade][webmatrix-modify-index]

2. Ændre linjen i afsnittet til følgende:

        p Welcome to #{title} with WebMatrix on Azure!

3. Gem ændringerne, og klik derefter på ikonet Publicer. Til sidst skal klikke på **Fortsæt** i dialogboksen **Publicer Preview** , og vent på en opdatering til publiceres.

    ![publicere preview][webmatrix-republish]

4. Når publicering er færdig, skal du bruge linket skal returneres, når Publicer processen er fuldført at se den opdaterede App Service WebApp.

    ![Azure node WebApp][webmatrix-node-completed]

##<a name="next-steps"></a>Næste trin

Hvis du vil vide mere om de versioner af Node.js, der følger med Azure, og hvordan du angiver versionen, der skal bruges sammen med dit program, kan du se [angive en Node.js version i et Azure-program](../nodejs-specify-node-version-azure-apps.md).

Hvis du støder på problemer med programmet, når den er blevet installeret på Azure, se, [hvordan du fejlfinding af en Node.js WebApp i Azure App Service](web-sites-nodejs-debug.md) kan finde oplysninger om diagnosticering problemet.

## <a name="whats-changed"></a>Hvad er ændret
* Finde en vejledning til ændring fra websteder til App-tjenesten: [Azure App Service og dets indvirkning på eksisterende Azure Services](http://go.microsoft.com/fwlink/?LinkId=529714)

[WebMatrix WebSite]: http://www.microsoft.com/click/services/Redirect2.ashx?CR_CC=200106398
[WebMatrix for Azure]: http://go.microsoft.com/fwlink/?LinkID=253622&clcid=0x409

[webmatrix-node-completed]: ./media/web-sites-nodejs-use-webmatrix/webmatrix-node-complete.png
[webmatrix-templates]: ./media/web-sites-nodejs-use-webmatrix/webmatrix-templates.png

[webmatrix-node-publishpreview]: ./media/web-sites-nodejs-use-webmatrix/webmatrix-publishpreview.png

[webmatrix-ide]: ./media/web-sites-nodejs-use-webmatrix/webmatrix-ide.png
[webmatrix-publish-complete]: ./media/web-sites-nodejs-use-webmatrix/webmatrix-publish-complete.png
[webmatrix-node-express-site]: ./media/web-sites-nodejs-use-webmatrix/webmatrix-express-webiste.png
[webmatrix-modify-index]: ./media/web-sites-nodejs-use-webmatrix/webmatrix-node-edit.png
[webmatrix-republish]: ./media/web-sites-nodejs-use-webmatrix/webmatrix-republish.png
[addaccount]: ./media/web-sites-nodejs-use-webmatrix/webmatrix-add-account.png
[signin]: ./media/web-sites-nodejs-use-webmatrix/webmatrix-sign-in.png
[sitefromtemplate]: ./media/web-sites-nodejs-use-webmatrix/webmatrix-site-from-template.png
[nodesitefromtemplateazure]: ./media/web-sites-nodejs-use-webmatrix/webmatrix-node-site-azure.png
 