<properties 
    pageTitle="Ved hjælp af PM2 konfiguration til NodeJS i Webapps på Linux | Microsoft Azure" 
    description="Ved hjælp af PM2 konfiguration til NodeJS i Webapps på Linux" 
    keywords="Azure-app-tjenesten, online, nodejs, pm2, linux, oss"
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

# <a name="using-pm2-configuration-for-nodejs-in-web-apps-on-linux"></a>Ved hjælp af PM2 konfiguration til Node.js i Webapps på Linux

Hvis du angiver programmet stablen til Node.js Web Apps på Linux, får du mulighed for at angive en Node.js Start-fil, som vist i billedet nedenfor.

![][1]

Du kan bruge det til enten

-   Angive startscript til din Node.js app (for eksempel: /bin/server.js)
-   Angiver PM2 konfigurationsfil skal bruges til din Node.js app (for eksempel: /foo/process.json)

 >[AZURE.NOTE] Hvis du vil Node processerne til automatisk at genstarte, når bestemte filer er ændret, skal du bruge PM2 konfiguration. Ellers genstart dit program ikke, når den modtager meddelelser om ændringer fra ting som sammenhængende installation, når din programkode ændres.

Du kan kontrollere Node.js [proces fil dokumentation](http://pm2.keymetrics.io/docs/usage/application-declaration/) for alle indstillingerne, men nedenfor er et eksempel på hvad du vil bruge som process.json filen

        {
          "name"        : "worker",
          "script"      : "/bin/server.js",
          "instances"   : 1,
          "merge_logs"  : true,
          "log_date_format" : "YYYY-MM-DD HH:mm Z",
          "watch": ["/bin/server.js", "foo.txt"],
          "watch_options": {
            "followSymlinks": true,
            "usePolling"   : true,
            "interval"    : 5
          }
        }

Vigtige ting, du skal være opmærksom på denne konfiguration er 

-   Egenskaben "script" angiver programmets start script.
-   Egenskaben "forekomster" Angiver, hvor mange forekomster af node processen til start. Hvis du kører dit program på større i VM, der har flere kerner, vil du maksimere dine ressourcer ved at angive en højere værdi her.
-   Matrixen "Se" Angiver alle filer, hvis ændringer du vil Genstart din node processer.
-   For "watch_options" skal du i øjeblikket angive "usePolling" som SAND på grund af den måde, dit indhold for programmet på computeren er tilsluttet.


## <a name="next-steps"></a>Næste trin ##

* [Hvad er App Service på Linux?](./app-service-linux-intro.md)

<!--Image references-->
[1]: ./media/app-service-linux-using-nodejs-pm2/nodejs-startup-file.png