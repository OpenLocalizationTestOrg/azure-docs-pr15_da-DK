<properties 
    pageTitle="Administrere en WebApp i Azure App Service" 
    description="Links til ressourcer for at administrere en WebApp i Azure App Service." 
    services="app-service\web" 
    documentationCenter="" 
    authors="erikre" 
    manager="wpickett" 
    editor=""/>

<tags 
    ms.service="app-service-web" 
    ms.workload="web" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="08/24/2016" 
    ms.author="rachelap"/>

# <a name="manage-a-web-app-in-azure-app-service"></a>Administrere en WebApp i Azure App Service

Dette emne indeholder links til ressourcer for at administrere en WebApp i [Azure App Service](http://go.microsoft.com/fwlink/?LinkId=529714). Administration indeholder alle de opgaver, holde din online kørende. 

Levetid af en WebApp udfører du forskellige administrationsopgaver, når du flytter fra indledende installation til normal drift, vedligeholdelse og opdateringer.

Mange web app-administrationsopgaver kan udføres i portalen Azure.

## <a name="before-you-deploy-your-web-app-to-production"></a>Inden du installerer din online til fremstilling

### <a name="choose-a-tier"></a>Vælg et trin

Azure App Service tilbydes i fem niveauer: ledig, delt, Basic, Standard og Premium. Finde oplysninger om funktionerne og priser for hvert niveau, [priser detaljer](/pricing/details/app-service/). 

- [App Service planer](../app-service/azure-web-sites-web-hosting-plans-in-depth-overview.md) kan du gruppere flere webapps under det samme niveau.
- Du kan altid [skifte niveauer](web-sites-scale.md) , når du har oprettet din online.

### <a name="configuration"></a>Konfiguration

Bruge [Azure Portal](https://portal.azure.com/) til at angive forskellige konfigurationsindstillinger. Yderligere oplysninger finder du [Konfigurer webapps i Azure App Service](web-sites-configure.md). Her er en hurtig Tjekliste:

- Vælg **runtime versioner** til .NET, PHP, Java eller Python, hvis det er nødvendigt.
- Aktivere **WebSockets** , hvis din online bruger WebSocket-protokollen. (Dette omfatter apps, der bruger [ASP.NET SignalR](http://www.asp.net/signalr) eller [socket.io](web-sites-nodejs-chat-app-socketio.md)).
- Kører fortløbende web job? Hvis det er tilfældet, skal du aktivere **Altid på**.
- Angive **standarddokument**, som index.html.

Ud over disse grundlæggende konfigurationsindstillinger vil du konfigurere følgende:

- **Secure Socket Layer (SSL)** kryptering. For at bruge SSL med et brugerdefineret domænenavn, skal du få et SSL-certifikat og konfigurere din online for at bruge den. Se [Aktivere HTTPS til en WebApp i Azure App-tjeneste](web-sites-configure-ssl-certificate.md).
- **Brugerdefineret domænenavn.** Din online har automatisk et underdomæne under azurewebsites.net. Du kan knytte et brugerdefineret domænenavn, som contoso.com. Se [konfigurere et brugerdefineret domænenavn i Azure App-tjeneste](web-sites-custom-domain-name.md).

Konfiguration af sprogspecifikke:

- **PHP**: [konfigurere PHP i Azure App Service Webapps](web-sites-php-configure.md).
- **Python**: [konfiguration af Python med Azure App Service Webapps](web-sites-python-configure.md)


## <a name="while-your-web-app-is-running"></a>Mens din online kører

Mens din online kører, vil du kontrollere den er tilgængelig, og at det skaleres til at opfylde bruger trafik. Du kan også kontakte fejlfinding af problemer.

### <a name="monitoring"></a>Overvågning

- Via Azure-portalen kan du [tilføje ydeevne målepunkter](web-sites-monitor.md) som CPU-brug og antallet af klient-anmodninger.
- [Skala din online](web-sites-scale.md) som svar på trafik. Afhængigt af dit niveau, kan du tilpasse antallet af FOS og/eller størrelsen på VM forekomster. I Standard- og Premium lag, kan du også konfigurere Autoskalering, så din online skaleres automatisk, efter en fast tidsplan, eller som svar på indlæse.  
 
### <a name="backups"></a>Sikkerhedskopier

- Indstille [automatisk sikkerhedkopiering](web-sites-backup.md) for din online. Lær mere om sikkerhedskopier i [denne video](https://azure.microsoft.com/documentation/videos/azure-websites-automatic-and-easy-backup/).
- Få mere at vide om indstillinger for [gendannelse af databasen](../sql-database/sql-database-business-continuity.md) i Azure SQL-Database.

### <a name="troubleshooting"></a>Fejlfinding i forbindelse med

- Hvis noget går galt, kan du [foretage fejlfinding af i Visual Studio](web-sites-dotnet-troubleshoot-visual-studio.md#remotedebug), ved hjælp af logge til diagnosticering og fejlfinding direkte i skyen. 
- Uden for Visual Studio er der forskellige måder at indsamle logge til diagnosticering. Se [aktivere logføring for webapps i Azure App Service diagnosticering](web-sites-enable-diagnostic-log.md).
- Se, [hvordan du fejlfinding af en Node.js WebApp i Azure App Service](web-sites-nodejs-debug.md)for Node.js programmer.

### <a name="restoring-data"></a>Gendanne Data

- [Gendanne](web-sites-restore.md) en WebApp, der tidligere blev sikkerhedskopieret.


## <a name="when-you-update-your-web-app"></a>Når du opdaterer din online

Hvis du ikke har aktiveret automatiske sikkerhedskopier, kan du oprette en [sikkerhedskopi manuelt](web-sites-backup.md).

Overvej at bruge [midlertidigt installation](web-sites-staged-publishing.md). Denne indstilling kan du udgive opdateringer til en midlertidig installation, der kører side om side med installationen dit produktionsmiljø. 

Hvis du bruger Visual Studio Team Services, kan du konfigurere fortløbende installation fra versionsstyring:

- [Brug af versionsstyring for Team Foundation (TFVC)](../cloud-services/cloud-services-continuous-delivery-use-vso.md) 
- [Brug af ciffer](../cloud-services/cloud-services-continuous-delivery-use-vso-git.md)
 
<!-- Anchors. -->

[Before you deploy your site to production]: #before-you-deploy-your-site-to-production
[While your website is running]: #while-your-website-is-running
[When you update your website]: #when-you-update-your-website

  
