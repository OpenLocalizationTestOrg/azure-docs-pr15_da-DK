<properties 
    pageTitle="Konfigurere webapps i Azure App Service" 
    description="Sådan konfigureres en web-app i Azure App Services" 
    services="app-service\web" 
    documentationCenter="" 
    authors="rmcmurray" 
    manager="wpickett" 
    editor=""/>

<tags 
    ms.service="app-service" 
    ms.workload="na" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="08/11/2016" 
    ms.author="robmcm"/>

# <a name="configure-web-apps-in-azure-app-service"></a>Konfigurere webapps i Azure App Service #

Dette emne forklares det, hvordan du konfigurerer et online ved hjælp af [Azure-portalen].

[AZURE.INCLUDE [app-service-web-to-api-and-mobile](../../includes/app-service-web-to-api-and-mobile.md)] 

## <a name="application-settings"></a>Indstillinger for et webprogram

1. Åbn bladet web App i [Azure-portalen].
2. Klik på **alle indstillinger**.
3. Klik på **Indstillinger for et webprogram**.

![Indstillinger for et webprogram][configure01]

Bladet **Programindstillinger** har indstillinger for grupperet under flere kategorier.

### <a name="general-settings"></a>Generelle indstillinger

**Framework versioner**. Angive disse indstillinger, hvis din app bruger et disse strukturer: 

- **.NET Framework**: Angiv versionen af .NET framework. 
- **PHP**: angive PHP version eller **fra** at deaktivere PHP. 
- **Java**: Vælg Java version eller **fra** at deaktivere Java. Brug indstillingen **Web objektbeholder** til at vælge mellem Tomcat og Jetty versioner.
- **Python**: Vælg Python version eller **fra** at deaktivere Python.

Aktivering af Java for din app deaktiverer indstillingerne .NET, PHP og Python tekniske årsager.

<a name="platform"></a>
**Platform**. Markerer om din online kører i et 32-bit eller 64-bit-miljø. 64-bit-miljø kræver Basic eller Standard tilstand. Frigør og delt funktionsmåder Kør altid i et 32-bit-miljø.

**Web Sockets**. Angive **til** at aktivere WebSocket protokollen; Hvis din online bruger [ASP.NET SignalR] eller [socket.io]f.eks.

<a name="alwayson"></a>
**Altid på**. Webapps er som standard fjernet, hvis de er ledige for nogle tidsperiode. Dette gør det muligt at reservere ressourcer. I tilstanden Basic eller Standard, kan du aktivere **Altid på** at holde appen indlæst hele tiden. Hvis din app kører fortløbende web job, du skal aktivere **Altid på**eller web-job kan ikke køre pålideligt.

**Administrerede Pipeline Version**. Angiver den IIS [pipelinetilstand]. Lad dette være angivet til integreret (standard), medmindre du har en ældre app, der kræver en ældre version af IIS.

**Automatisk Ombyt**. Hvis du aktiverer automatisk Byt om til en installation slot, Ombyt App Service automatisk WebApp til fremstilling når du klikker en opdatering til denne slot. Få mere at vide under [installation til at arrangere pladser til webapps i Azure App Service] (web-websteder – midlertidigt-publishing.md).

### <a name="debugging"></a>Fejlfinding

**Ekstern fejlfinding**. Gør det muligt for ekstern fejlfinding. Når aktiveret, kan du bruge remote fejlfindingsværktøjet i Visual Studio til at oprette forbindelse direkte til din online. Ekstern fejlfinding, forbliver aktiveret for 48 timer. 

### <a name="app-settings"></a>App-indstillinger

Dette afsnit indeholder navn/værdi-par, som du web app indlæses under start. 

- .NET Apps, skal disse indstillinger er tilføjet i din konfiguration af .NET `AppSettings` på kørselstidspunktet, tilsidesætte eksisterende indstillinger. 

- PHP, Python, Java og Node programmer kan få adgang til disse indstillinger som miljøvariabler på kørselstidspunktet. For hver app-indstilling oprettes to miljøvariabler; en med det navn, der er angivet af app indstillingspost, og den anden med præfikset APPSETTING_. Begge indeholde den samme værdi.

### <a name="connection-strings"></a>Forbindelsesstrenge

Forbindelsesstrenge for sammenkædede ressourcer. 

Til .NET-apps er tilføjet strengene forbindelse til din konfiguration af .NET `connectionStrings` indstillinger på kørselstidspunktet, tilsidesætte eksisterende poster, hvor tasten er lig med den sammenkædede databasenavn. 

Disse indstillinger vil være tilgængelige som miljøvariabler på kørselstidspunktet, præfikset forbindelsestypen til PHP, Python, Java og Node-programmer. De variable præfikser miljø er som følger: 

- SQL Server:`SQLCONNSTR_`
- MySQL:`MYSQLCONNSTR_`
- SQL-Database:`SQLAZURECONNSTR_`
- Brugerdefineret:`CUSTOMCONNSTR_`

For eksempel, hvis en MySql-forbindelsesstreng blev navnet `connectionstring1`, der opnås adgang via miljøvariablen `MYSQLCONNSTR_connectionString1`.

### <a name="default-documents"></a>Standarddokumenter

Standarddokumentet er den webside, der vises i roden URL-adressen for et websted.  Den første tilsvarende fil på listen bruges. 

Webapps kan bruge moduler, distribution baseret på URL-adresse i stedet for fungerer statisk indhold, hvorefter der er ingen standard dokumenter som f.eks.    

### <a name="handler-mappings"></a>Handler tilknytninger

Brug dette område til at tilføje brugerdefineret script processorer for at håndtere anmodninger om bestemte filtypenavnet. 

- **Filtypenavn**. Filtypenavnet skal håndteres, som *.php eller handler.fcgi. 
- **Script Processor sti**. Den absolutte sti til script-processor. Anmodninger om til filer, der svarer til filtypenavnet behandles af script-processor. Brug stien `D:\home\site\wwwroot` til at referere til din app rodmappen.
- **Yderligere argumenter**. Valgfri kommandolinjeargumenter for script-processor 


### <a name="virtual-applications-and-directories"></a>Virtuelle programmer og -mapper 
 
For at konfigurere virtuelle programmer og mapper skal du angive hver virtuel mappe og dens tilsvarende fysiske sti i forhold til webstedet roden. Du kan eventuelt vælge **programmet** afkrydsningsfeltet for at markere en virtuel mappe som et program.


## <a name="enabling-diagnostic-logs"></a>Aktivere diagnosticeringslogfiler

Sådan aktiveres diagnosticeringslogge:

1. Klik på **alle indstillinger**i bladet for din online.
2. Klik på **diagnosticeringslogfiler**. 

Indstillinger til at skrive diagnosticeringslogfiler fra et webprogram, der understøtter logføring: 

- **Logføring af programmet**. Skriver programmet logfiler til filsystemet. Logføring varer i en periode på 12 timer. 

**Niveau**. Når programmet logføring er aktiveret, angiver denne indstilling mængden oplysninger, der er registreret (fejl, advarsel, oplysninger eller detaljeret).

**Webserver logføring**. Logfiler gemmes i filformatet W3C udvidet logon. 

**Detaljerede fejlmeddelelser**. Gemmer fejlmeddelelser detaljerede i .htm-filer. Filerne, gemmes under /LogFiles/DetailedErrors. 

**Mislykket anmodning sporing**. Logfiler over mislykkede anmodninger til XML-filer. Filerne, gemmes under /LogFiles/W3SVC*xxx*, hvor xxx er et entydigt id. Denne mappe indeholder en XSL-fil og en eller flere XML-filer. Sørg for at hente filen XSL, fordi den indeholder funktioner til formatering og filtrere indholdet af XML-filer.

For at få vist logfilerne, skal du oprette FTP-legitimationsoplysninger, som følger:

1. Klik på **alle indstillinger**i bladet for din online.
2. Klik på **installation legitimationsoplysninger**.
3. Angive et brugernavn og adgangskode.
4. Klik på **Gem**.

![Angiv installation legitimationsoplysninger][configure03]

Det fulde FTP-brugernavn er "app\username", hvor *appen* er navnet på din online. Brugernavnet, der er angivet i bladet web app under **grundlæggende om**.  

![FTP-installation legitimationsoplysninger][configure02]

## <a name="other-configuration-tasks"></a>Andre konfigurationsopgaver

### <a name="ssl"></a>SSL 

I tilstanden Basic eller Standard, kan du overføre SSL-certifikater til et brugerdefineret domæne. Du kan finde yderligere oplysninger finder [aktivere HTTPS til en WebApp]. 

Klik på **Alle indstillinger**for at få vist dine overførte certifikater, > **brugerdefinerede domæner og SSL**.

### <a name="domain-names"></a>Domænenavne

Tilføje brugerdefinerede domænenavne til din online. Du kan finde yderligere oplysninger finder [konfigurere et brugerdefineret domænenavn til en WebApp i Azure App Service].

Klik på **Alle indstillinger**for at få vist dine domænenavne, > **brugerdefinerede domæner og SSL**.

### <a name="deployments"></a>Installationer

- Konfigurere fortløbende installation. Se [Brug af ciffer til at udrulle Onlines i Azure App-tjeneste](./web-sites-deploy.md).
- Installation pladser. Se [installere på midlertidige miljøer til Webapps i Azure App Service].


Klik på **Alle indstillinger**for at få vist din installation pladser, > **installation pladser**.

### <a name="monitoring"></a>Overvågning

Du kan teste tilgængeligheden af HTTP eller HTTPS slutpunkter fra op til tre geografisk distribueret placeringer i Basic eller Standard-tilstand. En overvågning test mislykkes, hvis HTTP svarkoden er en fejl (4xx eller 5xx) eller svaret tager mere end 30 sekunder. Et slutpunkt betragtes som tilgængelig, hvis de overvågning test lykkes fra de angivne placeringer. 

Du kan finde flere oplysninger, se [Sådan: overvåge web slutpunkt status].

>[AZURE.NOTE] Hvis du vil komme i gang med Azure App Service før tilmelding til en Azure-konto, skal du gå til [Prøve App Service], hvor du straks kan oprette en forbigående starter WebApp i App-tjeneste. Ingen kreditkort, der kræves. ingen forpligtelser.

## <a name="next-steps"></a>Næste trin

- [Konfigurere et brugerdefineret domænenavn i Azure App Service]
- [Aktivere HTTPS for en app i Azure App-tjeneste]
- [Skalere et online i Azure App Service]
- [Overvågning af grundlæggende for Web Apps i Azure App Service]

<!-- URL List -->

[ASP.NET SignalR]: http://www.asp.net/signalr
[Azure-portalen]: https://portal.azure.com/
[Konfigurere et brugerdefineret domænenavn i Azure App Service]: ./web-sites-custom-domain-name.md
[Installer til midlertidige miljøer for Webapps i Azure App Service]: ./web-sites-staged-publishing.md
[Aktivere HTTPS for en app i Azure App-tjeneste]: ./web-sites-configure-ssl-certificate.md
[Sådan: overvåge web slutpunkt status]: http://go.microsoft.com/fwLink/?LinkID=279906
[Overvågning af grundlæggende for Web Apps i Azure App Service]: ./web-sites-monitor.md
[pipelinetilstand]: http://www.iis.net/learn/get-started/introduction-to-iis/introduction-to-iis-architecture#Application
[Skalere et online i Azure App Service]: ./web-sites-scale.md
[socket.IO]: ./web-sites-nodejs-chat-app-socketio.md
[Prøv App Service]: http://go.microsoft.com/fwlink/?LinkId=523751

<!-- IMG List -->

[configure01]: ./media/web-sites-configure/configure01.png
[configure02]: ./media/web-sites-configure/configure02.png
[configure03]: ./media/web-sites-configure/configure03.png
