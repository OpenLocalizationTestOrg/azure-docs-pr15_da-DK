<properties
    pageTitle="Sådan foretages fejlfinding en Node.js WebApp i Azure App Service"
    description="Lær at foretage fejlfinding af en Node.js WebApp i Azure App Service."
    tags="azure-portal"
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

# <a name="how-to-debug-a-nodejs-web-app-in-azure-app-service"></a>Sådan foretages fejlfinding en Node.js WebApp i Azure App Service

Azure leverer indbyggede diagnosticering for at hjælpe med fejlfinding af Node.js programmer [Azure App Service](http://go.microsoft.com/fwlink/?LinkId=529714) Web Apps som vært. I denne artikel lærer du, hvordan du kan aktivere logføring af stdout og stderr, få vist oplysninger om fejlen i browseren, og hvordan du downloader og får vist logfiler.

Diagnosticering til Node.js programmer hostes på Azure leveres af [IISNode]. Mens denne artikel beskrives de mest almindelige indstillinger til at indsamle diagnosticeringsoplysninger, giver det ikke en komplet oversigt til at arbejde med IISNode. Se [Vigtige oplysninger om IISNode] på GitHub kan finde flere oplysninger om at arbejde med IISNode.

<a id="enablelogging"></a>
## <a name="enable-logging"></a>Aktivere logføring

Som standard registrerer en App Service-WebApp kun diagnostiske oplysninger om installationer, som når du installerer en online ved hjælp af ciffer. Disse oplysninger er nyttig, hvis du oplever problemer under installationen, som en fejl, når du installerer et modul, der refereres til i **package.json**, eller hvis du bruger et brugerdefineret installation script.

Hvis du vil aktivere logføring af stdout og stderr, skal du oprette en **IISNode.yml** -fil i roden af programmets Node.js og gør følgende:

    loggingEnabled: true

Dette gør det muligt for logføring af stderr og stdout fra dit Node.js program.

Filen **IISNode.yml** kan også bruges til at styre, om fulde fejl eller udvikler fejl returneres til browseren, når der opstår fejl. For at aktivere udvikler fejl skal du tilføje følgende linje i filen **IISNode.yml** :

    devErrorsEnabled: true

Når denne indstilling er aktiveret, returnerer IISNode de sidste 64 KB af oplysninger, der sendes til stderr i stedet for en venlig fejlmeddelelse, som "Der opstod en intern serverfejl".

> [AZURE.NOTE] Mens devErrorsEnabled er nyttig, når diagnosticere problemer under udvikling, kan så det i et produktionsmiljø medføre udvikling fejl, der sendes til slutbrugere.

Hvis filen **IISNode.yml** ikke allerede findes i dit program, skal du genstarte din online efter udgivelse det opdaterede program. Hvis du blot ændrer indstillinger i en eksisterende **IISNode.yml** -fil, der tidligere er udgivet, der der kræves ingen genstart.

> [AZURE.NOTE] Hvis din online blev oprettet ved hjælp af Azure kommandolinjeparametre værktøjer eller Azure PowerShell-cmdletter, oprettes der automatisk en standard **IISNode.yml** fil.

Vælg WebApp i [Azure Portal](https://portal.azure.com)for at genstarte WebApp, og klik derefter på **Genstart** knappen:

![Genstart knappen][restart-button]

Hvis Azure kommandolinjeparametre værktøjerne er installeret i dit udviklingsmiljø, kan du bruge følgende kommando for at genstarte WebApp:

    azure site restart [sitename]

> [AZURE.NOTE] Mens loggingEnabled og devErrorsEnabled er mest almindeligt brugte IISNode.yml konfiguration af indstillinger for registrering af diagnostiske oplysninger, kan IISNode.yml bruges til at konfigurere forskellige indstillinger for din værtsmiljø. Du kan finde en komplet liste over konfigurationsindstillingerne filen [iisnode_schema.xml](https://github.com/tjanczuk/iisnode/blob/master/src/config/iisnode_schema.xml) .

<a id="viewlogs"></a>
## <a name="accessing-logs"></a>Få adgang til logfiler

Du kan få adgang til diagnosticeringslogfiler på tre måder Ved hjælp af den FTP File Transfer Protocol (), hente et Zip-arkiv, eller som en direkte opdateret strøm af log (også kaldet en hale). Hente Zip-arkivet af logfilerne eller få vist den direkte stream, der kræver Azure kommandolinjeparametre værktøjerne. Disse kan installeres ved hjælp af følgende kommando:

    npm install azure-cli -g

Når installeret, kan værktøjerne, der åbnes ved hjælp af kommandoen 'azure'. Værktøjerne kommandolinjen skal først være konfigureret til at bruge dit Azure-abonnement. Finde oplysninger om, hvordan du udfører denne opgave, de **Sådan hentes og importere publicere indstillinger** afsnit i artiklen [Sådan Brug feltet Azure kommandolinjeparametre værktøjer](../xplat-cli-connect.md) .

###<a name="ftp"></a>FTP

Til at få adgang til de diagnostiske oplysninger via FTP skal du gå til [Azure-portalen](https://portal.azure.com), Vælg din online, og derefter vælge **DASHBOARD**. I sektionen **Hurtige links** indeholder linkene **FTP-DIAGNOSTICERINGSLOGFILER** og **FTPS DIAGNOSTICERINGSLOGFILER** adgang til de logfiler, der bruger FTP-protokollen.

> [AZURE.NOTE] Hvis du ikke har tidligere konfigureret brugernavn og din adgangskode til FTP- eller installation, kan du gøre det fra siden til administration af **Hurtig start** ved at vælge **konfigurere installation legitimationsoplysninger**.

FTP-URL-adresse, der returneres i dashboardet er på den **LogFiles** mappe, som skal indeholde følgende underordnede mapper:

* [Installationsmetode](web-sites-deploy.md) - Hvis du bruger en installationsmetode som ciffer, en mappe med samme navn oprettes og skal indeholde oplysninger i forbindelse med installationer.

* nodejs - Stdout og stderr oplysninger, der er hentet fra alle forekomster af dit program (når loggingEnabled er sand.)

###<a name="zip-archive"></a>Zip-arkiv

For at hente en Zip-arkiv med diagnosticeringslogfilerne, skal du bruge følgende kommando fra Azure kommandolinjeparametre værktøjer:

    azure site log download [sitename]

Dette vil hente en **diagnostics.zip** i den aktuelle mappe. Dette arkiv indeholder følgende mappestrukturen:

* installationer - en logfil over oplysninger om installation af dit program

* LogFiles

    * [Installationsmetode](web-sites-deploy.md) - Hvis du bruger en installationsmetode som ciffer, en mappe med samme navn oprettes og skal indeholde oplysninger i forbindelse med installationer.

    * nodejs - Stdout og stderr oplysninger, der er hentet fra alle forekomster af dit program (når loggingEnabled er sand.)

###<a name="live-stream-tail"></a>Live stream (hale)

Brug følgende kommando fra Azure kommandolinjeparametre værktøjer for at få vist en direkte visning af diagnosticering logoplysninger:

    azure site log tail [sitename]

Dette vil returnere en stream log hændelser, der er opdateret, efterhånden som de opstår på serveren. Denne strøm returnerer installationsoplysninger samt oplysninger om stdout og stderr (når loggingEnabled er sand.)

<a id="nextsteps"></a>
## <a name="next-steps"></a>Næste trin

Du har lært hvordan du aktiverer og få adgang til diagnosticeringsoplysninger til Azure i denne artikel. Mens disse oplysninger er nyttige i forstå problemer, der opstår med programmet, kan den pege på et problem med et modul, du bruger, eller versionen af Node.js, der bruges af App-tjenesten Web Apps er anderledes end den bruges i dit eget udviklingsmiljø.

Du kan finde oplysninger i arbejde med moduler på Azure [Ved hjælp af Node.js moduler med Azure-programmer](../nodejs-use-node-modules-azure-apps.md).

For oplysninger om angivelse af en Node.js version for dit program, kan du se [angive en Node.js version i et Azure-program].

Du kan finde flere oplysninger, se også [Node.js Developer Center](/develop/nodejs/).

## <a name="whats-changed"></a>Hvad er ændret
* Finde en vejledning til ændring fra websteder til App-tjenesten: [Azure App Service og dets indvirkning på eksisterende Azure Services](http://go.microsoft.com/fwlink/?LinkId=529714)

>[AZURE.NOTE] Hvis du vil komme i gang med Azure App Service før tilmelding til en Azure-konto, skal du gå til [Prøve App Service](http://go.microsoft.com/fwlink/?LinkId=523751), hvor du straks kan oprette en forbigående starter WebApp i App-tjeneste. Ingen kreditkort, der kræves. ingen forpligtelser.

[IISNode]: https://github.com/tjanczuk/iisnode
[Vigtige oplysninger om IISNode]: https://github.com/tjanczuk/iisnode#readme
[How to Use The Azure Command-Line Interface]: ../xplat-cli-install.md
[Using Node.js Modules with Azure Applications]: ../nodejs-use-node-modules-azure-apps.md
[Angive en Node.js version i et Azure-program]: ../nodejs-specify-node-version-azure-apps.md

[restart-button]: ./media/web-sites-nodejs-debug/restartbutton.png
 
