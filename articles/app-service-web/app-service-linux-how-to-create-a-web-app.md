<properties 
    pageTitle="Sådan oprettes en Web-App til App-tjenesten på Linux | Microsoft Azure" 
    description="Web app oprettelse af arbejdsprocessen for App-tjenesten på Linux." 
    keywords="Azure-app-tjenesten, online, linux, oss"
    services="app-service" 
    documentationCenter="" 
    authors="naziml" 
    manager="wpickett" 
    editor=""/>

<tags 
    ms.service="app-service" 
    ms.workload="na" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="10/10/2016" 
    ms.author="naziml"/>

# <a name="create-a-web-app-with-app-service-on-linux"></a>Oprette en Web-App til App-tjenesten på Linux

## <a name="using-the-management-portal-to-create-your-web-app"></a>Ved hjælp af portalen administration til at oprette din online
Du kan begynde at oprette din online på Linux fra [administrationsportalen til](https://portal.azure.com) , som vist i billedet nedenfor.

![][1]

Når du vælger indstillingen nedenfor, får du vist bladet Opret som vist i billedet nedenfor. 

![][2]

-   Navngive din online.
-   Vælge en eksisterende ressourcegruppe eller oprette en ny. (Se områder, der findes i [afsnittet begrænsninger](./app-service-linux-intro.md)).
-   Vælge en eksisterende app-serviceaftale eller oprette en ny en (se app-tjenesten plan noter i det [afsnit](./app-service-linux-intro.md)). 
-   Vælg programmet stablen du vil bruge. Får du at vælge mellem flere versioner af Node.js og PHP. 

Når du har den app, der er oprettet, kan du ændre programmet stablen fra programindstillingerne, som vist i billedet nedenfor.

![][3]

## <a name="deploying-your-web-app"></a>Implementering af din online

Vælge "installationsindstillinger" fra administrationsportalen giver dig mulighed for at bruge lokale et ciffer lager eller et GitHub lager til at installere programmet. Vejledningen er derefter på samme måde til en ikke-Linux WebApp, og du kan følge vejledningen i vores [lokale ciffer installation](./app-service-deploy-local-git.md) eller vores [fortløbende installation](./app-service-continuous-deployment.md) artikel til GitHub.

Du kan også bruge FTP til at overføre dit program til dit websted. Du kan hente FTP-slutpunktet til din online i afsnittet diagnosticering logfiler som vist i billedet nedenfor.

![][4]


## <a name="next-steps"></a>Næste trin ##

* [Hvad er App Service på Linux?](./app-service-linux-intro.md)
* [Ved hjælp af PM2 konfiguration til Node.js i Webapps på Linux](./app-service-linux-using-nodejs-pm2.md)

<!--Image references-->
[1]: ./media/app-service-linux-how-to-create-a-web-app/top-level-create.png
[2]: ./media/app-service-linux-how-to-create-a-web-app/create-blade.png
[3]: ./media/app-service-linux-how-to-create-a-web-app/application-settings-change-stack.png
[4]: ./media/app-service-linux-how-to-create-a-web-app/diagnostic-logs-ftp.png
