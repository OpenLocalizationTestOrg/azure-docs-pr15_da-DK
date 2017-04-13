<properties
    pageTitle="Installer din app til Azure App-tjenesten"
    description="Lær at anvende din app til Azure App Service."
    services="app-service"
    documentationCenter=""
    authors="cephalin"
    manager="wpickett"
    editor="mollybos"/>

<tags
    ms.service="app-service"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/06/2016"
    ms.author="cephalin;dariac"/>
    
# <a name="deploy-your-app-to-azure-app-service"></a>Installer din app til Azure App-tjenesten

I denne artikel hjælper dig med at bestemme den bedste måde at installere filer til din online, mobilapp back end- eller API app til [Azure App-tjenesten](http://go.microsoft.com/fwlink/?LinkId=529714), og klik derefter hjælper dig til relevante ressourcer med instruktioner, der er specifikke for den ønskede indstilling.

## <a name="overview"></a>Oversigt over Azure App Service-implementering

Azure App Service fører application framework for dig (ASP.NET, PHP, Node.js osv.). Nogle strukturer er aktiveret som standard under andre, som Java og Python, kan være nødvendigt at aktivere den en enkel markering konfiguration. Desuden kan du tilpasse dit program framework såsom PHP version eller bittæthed af din runtime. Se [konfigurere din app i Azure App Service](web-sites-configure.md)kan finde flere oplysninger.

Da du ikke behøver at bekymre dig om web server eller program framework, implementering af din app til App-tjenesten er et spørgsmål om anvender din kode, binære filer, indhold filer og deres respektive mappestrukturen for [ **/site/wwwroot** directory](https://github.com/projectkudu/kudu/wiki/File-structure-on-azure) i Azure (eller **/websted/wwwroot/App_Data/job/** mappe til WebJobs). App Service understøtter følgende installationsindstillinger: 

- [FTP- eller FTPS](https://en.wikipedia.org/wiki/File_Transfer_Protocol): Brug din foretrukne FTP- eller FTPS aktiveret værktøj til at flytte dine filer til Azure, fra [FileZilla](https://filezilla-project.org) til komplette IDEs som [NetBeans](https://netbeans.org). Dette er udelukkende overførslen en fil. Ingen yderligere tjenester leveres af App-tjeneste, såsom versionsstyring, struktur filstyring osv. 

- [Kudu (ciffer/betyder eller OneDrive/Dropbox)](https://github.com/projectkudu/kudu/wiki/Deployment): bruger [installation program](https://github.com/projectkudu/kudu/wiki) i App-tjeneste. Overføre din kode til Kudu direkte fra en hvilken som helst lager. Kudu indeholder også tilføjede services, når kode rykkes, herunder versionsstyring, pakke Gendan, MSBuild og [web kroge](https://github.com/projectkudu/kudu/wiki/Web-hooks) for fortløbende installation og andre automatiseringsopgaver. Kudu installation program understøtter 3 forskellige typer installation kilder:   
    * Synkronisering af indhold fra OneDrive og Dropbox   
    * Lager-baserede fortløbende installation til automatisk-synkronisering fra GitHub, Bitbucket og Visual Studio Team Services  
    * Lager-baserede-installation med manuel synkronisering fra lokale ciffer  

- [Web installere](http://www.iis.net/learn/publish/using-web-deploy/introduction-to-web-deploy): Implementer kode til App-tjenesten direkte fra din foretrukne Microsoft værktøjer som Visual Studio ved hjælp af den samme værktøjer, der bruges til automatisk installation til IIS-servere. Dette værktøj understøtter kun diff installation, oprette databasen, konvertering af strenge osv. Web Implementer er forskellig fra Kudu i den valgte programmet binære filer er oprettet, inden de installeres til Azure. Svarer til FTP, ingen ekstra tjenester der leveres af App Service.

Værktøjer til udvikling af populære web understøtter en eller flere af disse installationsprocesser. Mens det værktøj, du vælger bestemmer installationsprocesser, kan du udnytte, afhænger af den faktiske DevOps funktionalitet til din rådighed kombinationen af installationsprocessen og de specifikke værktøjer, du vælger. Eksempelvis hvis du udfører Web installere fra [Visual Studio med Azure SDK](#vspros), selvom du ikke få automatisering fra Kudu, får du pakke Gendan og MSBuild automatisering i Visual Studio. 

>[AZURE.NOTE] Disse installationsprocesser ikke faktisk [Klargør Azure ressourcer](../resource-group-template-deploy-portal.md) , der kan være nødvendigt din app. Men de fleste af de sammenkædede artikler viser, hvordan du klargør appen og installere din kode til den til slut. Du kan også finde yderligere indstillinger for klargøring Azure ressourcer i afsnittet [automatisere installation ved hjælp af kommandolinjen værktøjer](#automate) .
     
## <a name="ftp"></a>Installere via FTP ved at kopiere filer til Azure manuelt
Hvis du har brugt til at kopiere manuelt dit webindhold til en webserver, kan du bruge et [FTP-](http://en.wikipedia.org/wiki/File_Transfer_Protocol) værktøj til at kopiere filer, som Windows Stifinder eller [FileZilla](https://filezilla-project.org/).

Medarbejdere med at kopiere filer manuelt er:

- Velkendte og minimale kompleksitet for FTP-værktøj. 
- At vide, hvor, dine filer skal.
- Sikkerhedsmæssige med FTPS.

Ulemper ved at kopiere filer manuelt er:

- Har du vide, hvordan skal installeres filer til de korrekte mapper i App-tjeneste. 
- Ingen versionsstyring til gendannelse når fejl opstår.
- Ingen indbyggede installation historikken for fejlfinding af problemer med installation.
- Potentielle lang installation tidspunkter, da mange FTP-funktioner ikke giver diff kun kopiere og skal du blot kopiere alle filer.  

### <a name="howtoftp"></a>Sådan installeres ved at kopiere filer til Azure manuelt
Kopiere filer til Azure omfatter et par enkle trin:

1. Hvis du allerede har oprettet installation legitimationsoplysninger få forbindelsesoplysningerne FTP-ved at gå til **Indstillinger for** > **Egenskaber**og derefter kopiere værdierne for **FTP/installation bruger**, **FTP-værtsnavn**og **FTPS Host Name**. Skal du kopiere værdien **FTP/installation bruger** bruger som vises af Azure Portal, herunder app-navn for at give stort kontekst til FTP-serveren.
2. Brug forbindelsesoplysningerne du indsamlede for at oprette forbindelse til din app fra din FTP-klient.
3. Kopiere dine filer og deres respektive mappestrukturen til [ **/site/wwwroot** directory](https://github.com/projectkudu/kudu/wiki/File-structure-on-azure) i Azure (eller **/websted/wwwroot/App_Data/job/** mappe til WebJobs).
4. Gå til din app URL-adressen til kontrollere app'en kører korrekt. 

Du kan finde flere oplysninger i følgende ressource:

* [Oprette en PHP MySQL-WebApp og installere ved hjælp af FTP](web-sites-php-mysql-deploy-use-ftp.md).

## <a name="dropbox"></a>Installere ved at synkronisere med en mappe i skyen
Et godt alternativ til at [kopiere filer manuelt](#ftp) bliver synkroniseret filer og mapper til App Service fra en skylagringstjeneste som OneDrive og Dropbox. Synkronisere med en skyen mappe anvender Kudu processen til installation (se [Oversigt over installationsprocesser](#overview)).

Professionelle af synkronisere med en cloud-mappe er:

- Brugervenlighed installation. Tjenester som OneDrive og Dropbox giver computeren Synkroniser klienter, så din lokale arbejdsmappe er også adresselisten installation.
- Installation af et enkelt klik.
- Alle funktioner i Kudu installation engine er tilgængelig (fx pakke Gendan, automatisering).

Ulemper ved at synkronisere med en cloud-mappe er:

- Ingen versionsstyring til gendannelse når fejl opstår.
- Ingen automatiseret udrulning manuel synkronisering er påkrævet.

### <a name="howtodropbox"></a>Sådan installeres ved at synkronisere med en mappe i skyen
[Azure-portalen](https://portal.azure.com), kan du angive en mappe til synkronisering af indhold i dit OneDrive eller Dropbox skylagring, arbejde med din app kode og indhold i den pågældende mappe og synkronisere til App-tjenesten ved at klikke på en knap.

* [Synkroniser indhold fra en skyen mappe til Azure App-tjenesten](app-service-deploy-content-sync.md). 

## <a name="continuousdeployment"></a>Installere løbende fra en skybaseret kilde kontrolelement tjeneste
Hvis dit udviklingsteam bruger en skybaseret kilde kode management (SCM)-tjenesten som [Visual Studio Team Services](http://www.visualstudio.com/), [GitHub](https://www.github.com)eller [BitBucket](https://bitbucket.org/), kan du konfigurere App Service for at integrere med din lager og installere løbende. 

Medarbejdere med at udrulle fra en skybaseret kilde kontrolelement tjeneste er:

- Versionsstyring til at aktivere rollback.
- Mulighed for at konfigurere fortløbende installation for ciffer (og betyder, hvis det er relevant) typer lagre. 
- Branchespecifikke installation, kan installere forskellige forgreninger til forskellige [pladser](web-sites-staged-publishing.md).
- Alle funktioner i Kudu installation engine er tilgængelig (fx installation versionsstyring, rollback, pakke Gendan, automatisering).

Con med at udrulle fra en skybaseret kilde kontrolelement tjeneste er:

- Nogle kendskab til de respektive SCM tjeneste er påkrævet.

###<a name="vsts"></a>Sådan installeres løbende fra en skybaseret kilde kontrolelement tjeneste
Du kan konfigurere fortløbende installation fra GitHub, Bitbucket og Visual Studio Team Services i [Azure-portalen](https://portal.azure.com).

* [Uafbrudt installation til Azure App-tjenesten](app-service-continuous-deployment.md). 

## <a name="localgitdeployment"></a>Installere fra lokale ciffer
Hvis dit udviklingsteam bruger en lokal lokalt kode management (SCM)-tjenesten baseret på ciffer, kan du konfigurere det som en installation kilde til App-tjenesten. 

Medarbejdere med at udrulle fra lokale ciffer er:

- Versionsstyring til at aktivere rollback.
- Branchespecifikke installation, kan installere forskellige forgreninger til forskellige [pladser](web-sites-staged-publishing.md).
- Alle funktioner i Kudu installation engine er tilgængelig (fx installation versionsstyring, rollback, pakke Gendan, automatisering).

Con med at udrulle fra lokale ciffer er:

- Nogle kendskab til de respektive SCM system, der er påkrævet.
- Ingen slå nøgle løsninger til fortløbende installation. 

###<a name="vsts"></a>Hvordan du kan installere fra lokale ciffer
Du kan konfigurere lokale ciffer installation i [Azure-portalen](https://portal.azure.com).

* [Lokale ciffer installation til Azure App-tjenesten](app-service-deploy-local-git.md). 
* [Publicere på Web Apps fra en hvilken som helst ciffer/hg repo](http://blog.davidebbo.com/2013/04/publishing-to-azure-web-sites-from-any.html).  

## <a name="deploy-using-an-ide"></a>Installere ved hjælp af en IDE-enhed
Hvis du allerede bruger [Visual Studio](https://www.visualstudio.com/products/visual-studio-community-vs.aspx) med en [Azure SDK](https://azure.microsoft.com/downloads/)eller andre ide-pakker som [Xcode](https://developer.apple.com/xcode/), [Eklipse](https://www.eclipse.org)og [IntelliJ ide](https://www.jetbrains.com/idea/), kan du installere Azure direkte fra i din IDE. Denne indstilling er velegnet til en enkelt udvikler.

Visual Studio understøtter alle tre installationsprocesser (FTP, ciffer og Web installere), afhængigt af din præference, mens andre IDEs kan installere til App-tjenesten, hvis de har FTP eller ciffer integration (se [Oversigt over installationsprocesser](#overview)).

Medarbejdere med at udrulle ved hjælp af en IDE er:

- Minimere potentielt værktøj til din til slut programmets-livscyklus. Udvikle, fejlfinding, spore og anvende din app til Azure alle uden at flytte uden for din IDE. 

Ulemper ved at installere ved hjælp af en IDE er:

- Tilføjede kompleksitet i værktøj.
- Stadig kræver et system til kontrol af kilde for et teamprojekt.

<a name="vspros"></a>Flere medarbejdere med at udrulle ved hjælp af Visual Studio med Azure SDK er:

- Azure SDK gør Azure ressourcer førsteklasses borgere i Visual Studio. Oprette, slette, redigere, starter, og stop apps, forespørge back end-SQL-database, live-fejlfinding Azure-app, og meget mere. 
- Direkte redigering af kodefiler på Azure.
- Live fejlfinding af apps på Azure.
- Integreret Azure explorer.
- Kun diff installation. 

###<a name="vs"></a>Sådan installeres direkte fra Visual Studio

* [Introduktion til Azure og ASP.NET](web-sites-dotnet-get-started.md). Sådan oprettes og anvende en enkel ASP.NET MVC webprojektet ved hjælp af Visual Studio og Web installere.
* Se, [hvordan du kan installere Azure WebJobs ved hjælp af Visual Studio](websites-dotnet-deploy-webjobs.md). Sådan konfigureres Console programmet projekter, så de implementere som WebJobs.  
* [Installere en sikker ASP.NET MVC 5 app med medlemskab, OAuth, og SQL-Database til Webapps](web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database.md). Sådan oprettes og installere en ASP.NET MVC webprojekt med en SQL-database ved hjælp af Visual Studio, Web implementere og enhed Framework kode første overførsler.
* [ASP.NET webinstallation ved hjælp af Visual Studio](http://www.asp.net/mvc/tutorials/deployment/visual-studio-web-deployment/introduction). En 12-del selvstudium serie, der dækker en mere komplet række installationsopgaver end de andre på denne liste. Nogle funktioner Azure-installation er blevet tilføjet, da selvstudiet blev skrevet, men noter tilføjet senere forklare, hvad der mangler.
* [Implementere en ASP.NET-websted til Azure i Visual Studio 2012 fra et ciffer lager direkte](http://www.dotnetcurry.com/ShowArticle.aspx?ID=881). Beskriver, hvordan du installerer et ASP.NET webprojekt i Visual Studio, ved hjælp af den plug-in'en ciffer for at acceptere koden ciffer og forbindende Azure til ciffer lager. Start i Visual Studio 2013, ciffer support er indbygget og kræver ikke installation af et tilføjelsesprogram.

###<a name="aztk"></a>Sådan installeres ved hjælp af Azure programudviklingsværktøjer til Eklipse og IntelliJ ide

Microsoft gør det muligt at implementere Web Apps på Azure direkte fra Eklipse og IntelliJ via [Azure-værktøjskassen til Eklipse](../azure-toolkit-for-eclipse.md) og [Azure-værktøjskassen til IntelliJ](../azure-toolkit-for-intellij.md). Følgende selvstudier illustrere trinnene, der er involveret i implementeringen enkel en "Hej" verden Web App i Azure ved hjælp af enten IDE:

*  [Oprette en Hej verden WebApp til Azure i Eklipse](./app-service-web-eclipse-create-hello-world-web-app.md). Dette selvstudium viser, hvordan du bruger Azure-værktøjet til Eklipse til at oprette og installere en Hej verden Web App til Azure.
*  [Oprette en Hej verden WebApp til Azure i IntelliJ](./app-service-web-intellij-create-hello-world-web-app.md). Dette selvstudium viser, hvordan du bruger Azure-værktøjet til IntelliJ til at oprette og installere en Hej verden Web App til Azure.


## <a name="automate"></a>Automatisere installation ved hjælp af kommandolinjen værktøjer

* [Automatisere-installation med MSBuild](#msbuild)
* [Kopiere filer med FTP-værktøjer og scripts](#ftp)
* [Automatisere-installation med Windows PowerShell](#powershell)
* [Automatisere-installation med .NET management API](#api)
* [Installere fra Azure kommandolinjen (Azure CLI)](#cli)
* [Implementer fra Web installere kommandolinje](#webdeploy)
* [Bruge FTP-batchen Scripts](http://support.microsoft.com/kb/96269).
 
En anden installation mulighed er at bruge en skybaseret tjeneste, såsom [Ottearmede blæksprutter installere](http://en.wikipedia.org/wiki/Octopus_Deploy). Du kan finde yderligere oplysninger finder [installere ASP.NET-programmer til Azure-websteder](https://octopusdeploy.com/blog/deploy-aspnet-applications-to-azure-websites).

###<a name="msbuild"></a>Automatisere-installation med MSBuild

Hvis du bruger [Visual Studio IDE](#vs) til udvikling, kan du bruge [MSBuild](http://msbuildbook.com/) til at automatisere noget, du kan gøre i din IDE. Du kan konfigurere MSBuild for at bruge [Web installere](#webdeploy) eller [FTP/FTPS](#ftp) til at kopiere filer. Web Implementer kan også automatisere mange andre installation-relaterede opgaver såsom at implementere databaser.

Se følgende ressourcer kan finde flere oplysninger om kommandolinjen installation ved hjælp af MSBuild:

* [ASP.NET webinstallation ved hjælp af Visual Studio: kommandolinjen installation](http://www.asp.net/mvc/tutorials/deployment/visual-studio-web-deployment/command-line-deployment). Tiende i en række selvstudier om installation til Azure ved hjælp af Visual Studio. Viser, hvordan du kan bruge kommandolinjen til at udrulle når konfigurationen af publicere profiler i Visual Studio.
* [I Microsoft Build-program: ved hjælp af MSBuild og Team Foundation Build](http://msbuildbook.com/). Trykt kopi bøger, der indeholder kapitler om, hvordan du bruger MSBuild til installation.

###<a name="powershell"></a>Automatisere-installation med Windows PowerShell

Du kan udføre MSBuild eller FTP-installation funktioner i [Windows PowerShell](http://msdn.microsoft.com/library/dd835506.aspx). Hvis du gør dette, kan du også bruge en samling af Windows PowerShell-cmdletter, som gør det nemt at ringe Azure RESTEN management API.

Yderligere oplysninger finder du se følgende ressourcer:

* [Installere en WebApp, der er knyttet til et GitHub lager](app-service-web-arm-from-github-provision.md)
* [Klargøre en WebApp med en SQL-Database](app-service-web-arm-with-sql-database-provision.md)
* [Klargøre og installere microservices forudsigeligt i Azure](app-service-deploy-complex-application-predictably.md)
* [Bygning reale skyen Apps med Azure - automatisere alt](http://asp.net/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/automate-everything). E-bog kapitel, der forklarer, hvordan vises i e-bog Northwind bruger Windows PowerShell-scripts til at oprette en Azure testmiljø og installere til den. Se afsnittet [ressourcer](http://asp.net/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/automate-everything#resources) for links til yderligere dokumentation til Azure PowerShell.
* [Bruge Windows PowerShell-Scripts til at udgive til Udviklingscenter og testmiljøer](../vs-azure-tools-publishing-using-powershell-scripts.md). Hvordan du bruger Windows PowerShell installation scripts, som genererer Visual Studio.

###<a name="api"></a>Automatisere-installation med .NET management API

Du kan skrive C#-kode for at udføre MSBuild eller FTP-funktioner til installation. Hvis du gør dette, kan du få adgang til Azure management REST-API til at udføre funktioner til administration af websted.

Du kan finde flere oplysninger i følgende ressource:

* [Automatisere alt med Azure Management biblioteker og .NET](http://www.hanselman.com/blog/PennyPinchingInTheCloudAutomatingEverythingWithTheWindowsAzureManagementLibrariesAndNET.aspx). Introduktion til .NET management API og links til flere dokumentation.

###<a name="cli"></a>Installere fra Azure kommandolinjen (Azure CLI)

Du kan bruge kommandolinjen i Windows, Mac eller Linux-computere skal installeres ved hjælp af FTP. Hvis du gør dette, kan du også få adgang til Azure RESTEN management API ved hjælp af Azure CLI.

Du kan finde flere oplysninger i følgende ressource:

* [Azure kommandoen linje værktøjer](/downloads/#cmd-line-tools). Portalen siden i Azure.com for oplysninger om kommandolinjen tool.

###<a name="webdeploy"></a>Implementer fra Web installere kommandolinje

Microsoft-software til installation til IIS, ikke kun indeholder intelligent fil Synkroniser funktioner, men også kan udføre eller koordinere mange andre installation-relaterede opgaver, der ikke kan automatisk, når du bruger FTP er [Web installere](http://www.iis.net/downloads/microsoft/web-deploy) . For eksempel kan Web installere installere en ny database eller databaseopdateringer sammen med din online. Web Implementer kan også minimere den tid, der kræves for at opdatere et eksisterende websted, da det kan intelligent kopiere kun ændrede filer. Microsoft Visual Studio og Team Foundation Server har understøttelse af Web installere indbyggede, men du kan også bruge Web installere direkte fra kommandolinjen til at automatisere installation. Web Implementer kommandoer er meget effektiv, men learning kurven kan være stejle.

Du kan finde flere oplysninger i følgende ressource:

* [Simple Webapps: installation](https://azure.microsoft.com/blog/2014/07/28/simple-azure-websites-deployment/). Blog ved David Ebbo om et værktøj, han skrevet gør det nemmere at bruge Web installere.
* [Web Udrulningsværktøj](http://technet.microsoft.com/library/dd568996). Officiel dokumentation på Microsoft TechNet-webstedet. Dateret men stadig et godt sted at starte.
* [Ved hjælp af Web installere](http://www.iis.net/learn/publish/using-web-deploy). Officiel dokumentation på webstedet Microsoft IIS.NET. Dateret også men et godt sted at starte.
* [ASP.NET webinstallation ved hjælp af Visual Studio: kommandolinjen installation](http://www.asp.net/mvc/tutorials/deployment/visual-studio-web-deployment/command-line-deployment). MSBuild er build-program, der bruges af Visual Studio, og det kan også bruges fra kommandolinjen til at udrulle webprogrammer til Web Apps. Dette selvstudium er en del af en serie, der er især om Visual Studio installation.

##<a name="nextsteps"></a>Næste trin

I nogle scenarier, vil du kunne nemt skifte frem og tilbage mellem en midlertidige og en version af din app. Du kan finde yderligere oplysninger finder [Midlertidigt installation på Web Apps](web-sites-staged-publishing.md).

Har du en plan for sikkerhedskopiering og gendannelse på plads, er en vigtig del af en arbejdsproces for installation. Oplysninger om tjenesten App sikkerhedskopiere og gendanne funktion, skal du se [Web Apps sikkerhedskopier](web-sites-backup.md).  

Finde oplysninger om, hvordan du bruger Azures rollebaseret adgangskontrol til at administrere adgang til App Service installation, i [RBAC og Web App-udgivelse](https://azure.microsoft.com/blog/2015/01/05/rbac-and-azure-websites-publishing/).


 
