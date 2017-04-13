<properties
    pageTitle="Azure App Service, virtuelle maskiner, Service-strukturen og Cloud Services sammenligning | Microsoft Azure"
    description="Få mere at vide, hvordan du kan vælge mellem Azure App Service, virtuelle maskiner, Service-strukturen og Cloud Services for vært webprogrammer."
    services="app-service\web, virtual-machines, cloud-services"
    documentationCenter=""
    authors="tdykstra"
    manager="wpickett"
    editor="jimbe"/>

<tags
    ms.service="app-service-web"
    ms.workload="web"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="07/07/2016"
    ms.author="tdykstra"/>

# <a name="azure-app-service-virtual-machines-service-fabric-and-cloud-services-comparison"></a>Azure App Service, virtuelle maskiner, Service-strukturen og Cloud Services sammenligning

## <a name="overview"></a>Oversigt

Azure får flere muligheder for at host websteder: [Azure App Service][], [virtuelle maskiner][], [Service-strukturen][]og [Cloud Services][]. I denne artikel hjælper dig med at forstå indstillingerne, og vælg det rigtige for dit program.

Azure-App-tjenesten er det bedste valg for de fleste webapps. Installation og administration er integreret i platformen, websteder kan skaleres til hurtigt for at håndtere høj trafik indlæse og indbyggede Indlæs justering og trafik manager giver høj tilgængelighed. Du kan flytte eksisterende websteder til Azure App Service nemt med et [online overførselsværktøjet](https://www.migratetoazure.net/), bruge en open source-app fra galleriet med programmet eller oprette et nyt websted ved hjælp af framework og værktøjer efter eget valg. Funktionen [WebJobs][] gør det nemt at føje baggrundsjob behandling af din App Service web App.

Tjenesten struktur er et godt valg, hvis du opretter en ny app eller skrive en eksisterende app for at bruge en microservice arkitektur igen. Apps, som kører på en delt samling af maskiner, kan starte fra bunden og vokser til massive Skaler med hundredvis eller tusindvis af computere efter behov. Med høj sikkerhed services gør det nemt at konsekvent og sikkert gemme app-tilstand, og Service-strukturen administrerer automatisk tjenesten partitionering, skalering og tilgængelighed for dig.  Tjenesten strukturen understøtter også WebAPI med åbne Web-grænseflade til .NET (OWIN) og ASP.NET Core.  Sammenlignet med App Service, giver Service-strukturen også mere kontrol over eller direkte adgang til den underliggende struktur. Du kan remote til dine servere eller konfigurere server Start opgaver. Cloud Services ligner Service-strukturen i graden af kontrolelementet kontra brugervenlig, men det er nu en ældre tjeneste og Service-strukturen anbefales til udvikling af nye.

Hvis du har et eksisterende program, der kræver væsentlige ændringer til at køre i App Service eller Service-strukturen, kan du vælge virtuelle maskiner for at forenkle overførsel til skyen. Men korrekt konfiguration, sikring af og vedligeholde FOS kræver meget mere tid og IT-ekspertise sammenlignet med Azure App Service og tjenesten struktur. Hvis du overvejer virtuelle Azure-computere, skal du tage hensyn til de løbende vedligeholdelse indsats, der kræves til programrettelse, opdatere og administrere dit VM-miljø. Azure virtuelle maskiner er infrastruktur som-en-tjenesten (IaaS), mens App Service og Service-strukturen er Platform som-en-tjenesten (Paas). 

## <a name="features"></a>Sammenligning af funktioner

I følgende sammenlignes funktionerne i App Service, Cloud Services, virtuelle maskiner og Service-strukturen kan hjælpe dig med at sikre det bedste valg. Du kan finde aktuelle oplysninger om SERVICENIVEAUAFTALE for hver indstilling [Azure serviceaftaler niveau](/support/legal/sla/).

Funktion|App Service (online)|Cloud Services (web roller)|Virtuelle maskiner|Tjenesten struktur|Noter
---|---|---|---|---|---
Næsten øjeblikkeligt installation|X|||X|Implementere et program eller en opdatering til programmet til en skybaseret tjeneste, eller oprette en VM, det tager flere minutter mindst; implementere et program til en WebApp tager sekunder.
Skalere op til større maskiner uden genimplementeringen|X|||X|
Web serverforekomster dele indhold og konfiguration, hvilket betyder, at du ikke behøver at geninstallere eller omkonfigurere, som du skalere.|X|||X|
Flere installation miljøer (fremstilling og midlertidige)|X|X||X|Tjenesten strukturen kan du have flere miljøer til dine apps eller til at anvende forskellige versioner af din app side om side.
Administration af opdateringer automatisk OS|X|X|||Automatiske opdateringer til OS er planlagt for en fremtidig Service-strukturen slip.
Problemfri platform skifte (let flytte mellem 32-bit og 64-bit)|X|X|||
Installere kode med CIFFER, FTP|X||X||
Installere kode med Web installere|X||X||Cloud Services understøtter brugen af Web installere skal installeres opdateringer til individuelle rolle forekomster. Men du kan ikke bruge den til indledende installation af en rolle, og hvis du bruger Web installere til en opdatering du skal installere separat på hver forekomst af en rolle. Flere forekomster er påkrævet, at du er kvalificeret til skyen Service SERVICENIVEAUAFTALE for fremstilling miljøer.
WebMatrix support|X||X||
Adgang til tjenester som Service Bus, lagring, SQL-Database|X|X|X|X|
Host internettet eller web services niveau i en med flere lag arkitektur|X|X|X|X|
Host midterste niveau af en med flere lag arkitektur|X|X|X|X|App Service webapps kan nemt hoste REST-API midterste niveau, og funktionen [WebJobs](http://go.microsoft.com/fwlink/?linkid=390226) kan være vært for behandling job i baggrunden. Du kan køre WebJobs i et dedikeret websted at opnå uafhængige skalerbarhed til niveauet. Funktionen Vis [API apps](../app-service-api/app-service-api-apps-why-best-platform.md) indeholder endnu flere funktioner til værtstjenester RESTEN.
Integreret MySQL som en tjeneste-understøttelse|X|X|X||Cloud Services kan integrere MySQL som en tjeneste til Cleardbs tilbud, men ikke som en del af arbejdsprocessen Azure-portalen.
Understøttelse af ASP.NET, klassisk ASP Node.js PHP, og Python|X|X|X|X|Tjenesten strukturen understøtter oprettelse af en front end web ved hjælp af [ASP.NET 5](../service-fabric/service-fabric-add-a-web-frontend.md) , eller du kan installere en hvilken som helst type af programmet (Node.js, Java osv.) som [Gæst eksekverbare](../service-fabric/service-fabric-deploy-existing-app.md).
Skalere ud af, at flere forekomster uden genimplementeringen|X|X|X|X|Virtuelle maskiner kan skalere ud af, at flere forekomster, men de tjenester, der kører på dem skal skrives til at håndtere denne skala ud. Du skal konfigurere belastningsjustering for at distribuere anmodninger på tværs af maskiner, og oprette en forbindelse gruppe for at undgå samtidig genstarter af alle forekomster på grund af vedligeholdelse eller hardware mislykkede forsøg.
Understøttelse af SSL|X|X|X|X|SSL for brugerdefinerede domænenavne understøttes kun til App Service webapps for Basic og Standard tilstand. Se [konfigurere et SSL-certifikat for en Azure-websted](../app-service-web/web-sites-configure-ssl-certificate.md)for at få oplysninger om at bruge SSL med webapps.
Visual Studio-integration|X|X|X|X|
Ekstern fejlfinding|X|X|X||
Installere kode med TFS|X|X|X|X|
Netværkets isolation med [Azure virtuelt netværk](/services/virtual-network/)|X|X|X|X|Se også [Azure websteder virtuelt netværksintegration](/blog/2014/09/15/azure-websites-virtual-network-integration/)
Understøttelse af [Azure trafik Manager](/services/traffic-manager/)|X|X|X|X|
Integreret slutpunkt overvågning|X|X|X||
Stationære fjernadgang til servere||X|X|X|
Installere en brugerdefineret MSI||X|X|X|Service-strukturen gør det muligt at være vært for en hvilken som helst eksekverbare fil som [Gæst eksekverbare](../service-fabric/service-fabric-deploy-existing-app.md) , eller du kan installere en app på FOS.
Muligheden for at definere/udføre opstart opgaver||X|X|X|
Kan lytte til ETW begivenheder||X|X|X|

## <a name="scenarios"></a>Scenarier og anbefalinger

Her er nogle almindelige programmet scenarier med anbefalinger som hvilke Azure web vært indstilling kan være mest relevante for hver.

- [Jeg har brug for en web front-end med baggrund behandling og database back end-til at køre business-programmer, der er integreret med på en lokal aktiver.](#onprem)
- [Jeg har brug for en pålidelig metode til at være vært for mit virksomhedens websted, der kan skaleres godt og tilbud globale nå.](#corp)
- [Jeg har en IIS6 program, der kører på Windows Server 2003.](#iis6)
- [Jeg er ejere af mindre virksomheder, og jeg har brug for en billig måde at være vært for Mit websted, men med fremtidig vækst huske.](#smallbusiness)
- [Jeg er et websted eller grafisk designer, og jeg vil designe og opbygge websteder for mine kunder.](#designer)
- [Jeg overføre mit med flere lag program med en front end web til skyen.](#multitier)
- [Mit program afhænger meget tilpassede Windows eller Linux-miljøer, og jeg vil flytte den til skyen.](#custom)
- [Mit websted bruger open source-software, og jeg vil placere den i Azure.](#oss)
- [Jeg har et line of business-program, der skal oprettes forbindelse til virksomhedens netværk.](#lob)
- [Jeg vil have en REST-API eller webtjeneste til mobilklienter.](#mobile)


### <a id="onprem"></a>Jeg har brug for en web front-end med baggrund behandling og database back end-til at køre business-programmer, der er integreret med på en lokal aktiver.

Azure App Service er en god løsning til komplekse business-programmer. Det gør det muligt at udvikle apps, der skalere automatisk på en ikke-opgjorte platform indlæsning, er sikret med Active Directory og oprette forbindelse til din lokale ressourcer. Det gør administration af disse nemt via en verdensklasse portal og API'er apps, og gør muligt at få indsigt i hvordan kunder bruger dem med app indsigt værktøjer. Funktionen [Webjobs][] kan du køre baggrundsprocesser og opgaver som en del af din web-niveauet, mens du hybrid forbindelsen og VNET funktioner gør det nemt at oprette forbindelse igen til lokale ressourcer. Azure App Service indeholder tre 9 SERVICENIVEAUAFTALE til Onlines og gør det muligt at:

* Køre programmerne pålideligt på en selvreparerende, automatisk rettelse skyen platform.
* Skalere automatisk på tværs af et globalt netværk af datacentre.
* Sikkerhedskopiere og gendanne for nedbrud.
* Er ISO, SOC2 og PCI kompatibel.
* Integrere med Active Directory

### <a id="corp"></a>Jeg har brug for en pålidelig metode til at være vært for mit virksomhedens websted, der kan skaleres godt og tilbud globale nå.

Azure App Service er en god løsning som vært for virksomhedens websteder. Det gør det muligt for Onlines skalere hurtigt og nemt at imødekomme behov på tværs af et globalt netværk af datacentre. Det giver adgang til lokale rækkevidde, fejltolerance og intelligent trafik administration. Alt i en platform, der leverer verdensklasse administrationsværktøjer, giver dig mulighed at få indsigt i tilstand og websted trafik hurtigt og nemt. Azure App Service indeholder tre 9 SERVICENIVEAUAFTALE til Onlines og gør det muligt at:

* Køre dine websteder pålideligt på en selvreparerende, automatisk rettelse skyen platform.
* Skalere automatisk på tværs af et globalt netværk af datacentre.
* Sikkerhedskopiere og gendanne for nedbrud.
* Administrere logfiler og trafik med integrerede værktøjer.
* Er ISO, SOC2 og PCI kompatibel.
* Integrere med Active Directory

### <a id="iis6"></a>Jeg har en IIS6 program, der kører på Windows Server 2003.

Azure App Service gør det nemt at undgå infrastrukturomkostningerne i forbindelse med overflytning ældre IIS6 programmer. Microsoft har oprettet [brugervenlige værktøjer og detaljerede overførsel vejledning](https://www.movemetowebsites.net/) , der gør det muligt at kontrollere kompatibiliteten og identificere eventuelle ændringer, der skal udføres. Integration med Visual Studio, TFS og almindelige CMS værktøjer gør det nemt at implementere IIS6 programmer direkte til skyen. Når installeret, indeholder portalen Azur robust administrationsværktøjer, der gør det muligt at skalere til at administrere omkostninger og op til møde har behov for det er nødvendigt. Med overførselsværktøjet kan du:

* Hurtigt og nemt overføre din ældre Windows Server 2003-webprogrammet til skyen.
* Vælge for at lade dit vedhæftede SQL-database lokalt for at oprette en hybrid-program.
* Automatisk flytte din SQL-database sammen med dit ældre program.

### <a id="smallbusiness"></a>Jeg er ejere af mindre virksomheder, og jeg har brug for en billig måde at være vært for Mit websted, men med fremtidig vækst huske.

Azure App Service er en god løsning til dette scenario, fordi du kan begynde at bruge den gratis og derefter tilføjer flere funktioner, når du har brug for. Hver gratis WebApp leveres med et domæne, der leveres af Azure (*your_company*. azurewebsites.net), og platformen omfatter integrerede værktøjer til installation og administration, samt et galleri med programmet, som gør det nemt at komme i gang. Der findes mange andre tjenester og skaleringsindstillinger, der tillader webstedet for at udvikle med øget brugernes behov. Med Azure App Service kan du:

- Begynder med det gratis niveau og derefter skalere op efter behov.
- Brug af galleriet programmet til hurtigt for at oprette populære webprogrammer, som WordPress.
- Tilføje ekstra Azure tjenester og funktioner i dit program, efter behov.
- Beskytte din online med HTTPS.

### <a id="designer"></a>Jeg er et websted eller grafisk designer, og jeg vil designe og opbygge websteder for mine kunder

Til webudviklere og designere, Azure App Service integreres let med en række forskellige værktøjer og strukturer, indeholder installation understøttelse af ciffer og FTP- og tæt integreret med værktøjer og tjenester som Visual Studio og SQL-Database. Med App-tjenesten kan du:

- Bruge kommandolinjen værktøjer til [automatiserede opgaver][scripting].
- Arbejde med populære sprog som [.net][dotnet], [PHP][], [Node.js][nodejs], og [Python][].
- Vælg tre forskellige skalering niveauer for skalering til meget høj kapacitet.
- Integrere med andre Azure tjenester, såsom [SQL-Database][sqldatabase], [Service Bus] [ servicebus] og [lagerplads][]eller partner tilbud fra [Azure Store][azurestore], som MySQL og MongoDB.
- Integrere med funktioner som Visual Studio, ciffer, WebMatrix, WebDeploy, TFS og FTP.

### <a id="multitier"></a>Jeg overføre mit med flere lag program med en front end web til skyen

Hvis du kører en programmet på computeren med flere lag, som en webserver, der opretter forbinder til en database, er Azure App tjenesten en god indstilling, der tilbyder tæt integration med Azure SQL-Database. Og du kan bruge funktionen WebJobs til kørsel af back end-processer.

Hvis du har brug for mere kontrol med server-miljø, som muligheden for at remote til din server eller konfigurere server Start opgaver, skal du vælge Service-strukturen for en eller flere af dine niveauer.

Hvis du vil bruge din egen computer billede eller køre serversoftware eller tjenester, du ikke kan konfigureres på Service-strukturen, skal du vælge virtuelle maskiner for en eller flere af dine niveauer.

### <a id="custom"></a>Mit program afhænger meget tilpassede Windows eller Linux-miljøer, og jeg vil flytte den til skyen.

Hvis programmet kræver komplekse installation eller konfiguration af software og operativsystemet, er virtuelle maskiner sandsynligvis den bedste løsning. Med virtuelle maskiner kan du:

- Brug galleriet Virtual Machine til at starte med et operativsystem, som Windows eller Linux, og derefter tilpasse den efter dine krav til programmet.
- Oprette og overføre et brugerdefineret billede af en eksisterende lokal server til at køre på en virtuel maskine i Azure.

### <a id="oss"></a>Mit websted bruger open source-software, og jeg vil placere den i Azure

Hvis din Åbn kilde framework understøttes på App-tjenesten, sprog og rammer, der er nødvendige for dit program er konfigureret for dig automatisk. App Service kan du:

- Brug af mange populære Åbn kilde sprog, som [.NET][dotnet], [PHP][], [Node.js][nodejs], og [Python][].
- Konfigurere WordPress, Drupal, Umbraco, DNN og mange andre tredjeparts-webprogrammer.
- Overføre et eksisterende program eller oprette en ny fra galleriet programmet på computeren.

Hvis din Åbn kilde framework ikke understøttes på App-tjenesten, kan du køre den på en af de andre Azure web vært indstillinger. Med virtuelle maskiner, installere og konfigurere softwaren på billedet maskine, som kan være Windows eller Linux-baserede.

### <a id="lob"></a>Jeg har et line of business-program, der skal oprettes forbindelse til virksomhedens netværk

Hvis du vil oprette en line of business-program, kan webstedet kræver direkte adgang til tjenester eller data på virksomhedens netværk. Dette er muligt på App Service, Service-strukturen og virtuelle maskiner ved hjælp af [Azure virtuelle Netværkstjeneste](/services/virtual-network/). Du kan bruge [funktionen til integration af VNET](https://azure.microsoft.com/blog/2014/09/15/azure-websites-virtual-network-integration/), hvilket gør det muligt for dine Azure programmer til at køre som hvis det var på virksomhedens netværk på App-tjenesten.

### <a id="mobile"></a>Jeg vil have en REST-API eller webtjeneste til mobilklienter

HTTP-baserede webtjenester gør det muligt at understøtte en lang række klienter, herunder mobilklienter. Rammer som ASP.NET Web API integreres med Visual Studio til at gøre det nemmere at oprette og bruge RESTEN tjenester.  Disse tjenester vises fra en web slutpunkt, så det er muligt at bruge et websted, der er vært for metode på Azure til at understøtte dette scenario. App Service er dog et godt valg for vært REST API'er. Med App-tjenesten kan du:

- Hurtigt oprette en [mobilapp](../app-service-mobile/app-service-mobile-value-prop.md) eller [API app](../app-service-api/app-service-api-apps-why-best-platform.md) til at hoste HTTP-webtjeneste i en af Azures globalt fordelt datacentre.
- Overføre eksisterende tjenester eller oprette nye.
- Opnå SERVICENIVEAUAFTALE for tilgængelighed med en enkelt forekomst eller skalere ud for flere dedikeret computere.
- Brug publicerede webstedet for at levere REST API'er til en hvilken som helst HTTP-klienter, herunder mobilklienter.

> [AZURE.NOTE]
> Hvis du vil komme i gang med Azure App Service før tilmelding til en konto, skal du gå til <a href="https://trywebsites.azurewebsites.net/">https://trywebsites.azurewebsites.net</a>, hvor du kan straks oprette en forbigående starter app i Azure App Service gratis. Ingen kreditkort er påkrævet, ingen forpligtelser.

## <a id="nextsteps"></a>Næste trin

Du kan finde flere oplysninger om de tre web hosting indstillinger, skal du se [Introduktion til Azure](../fundamentals-introduction-to-azure.md).

For at komme i gang med de indstillinger, du vælger for dit program skal du se følgende ressourcer:

* [Azure App Service](/documentation/services/app-service/)
* [Azure-Skytjenester](/documentation/services/cloud-services/)
* [Azure virtuelle maskiner](/documentation/services/virtual-machines/)
* [Tjenesten struktur](/documentation/services/service-fabric)

<!-- URL List -->

[Azure App Service]: /services/app-service/
[Cloud Services]: http://go.microsoft.com/fwlink/?LinkId=306052
[Virtuelle maskiner]: http://go.microsoft.com/fwlink/?LinkID=306053
[Tjenesten struktur]: /services/service-fabric
[ClearDB]: http://www.cleardb.com/
[WebJobs]: http://go.microsoft.com/fwlink/?linkid=390226&clcid=0x409
[Configuring an SSL certificate for an Azure Website]: http://www.windowsazure.com/develop/net/common-tasks/enable-ssl-web-site/
[azurestore]: http://www.windowsazure.com/gallery/store/
[scripting]: http://www.windowsazure.com/documentation/scripts/?services=web-sites
[dotnet]: http://www.windowsazure.com/develop/net/
[nodejs]: http://www.windowsazure.com/develop/nodejs/
[PHP]: http://www.windowsazure.com/develop/php/
[Python]: http://www.windowsazure.com/develop/python/
[servicebus]: http://www.windowsazure.com/documentation/services/service-bus/
[sqldatabase]: http://www.windowsazure.com/documentation/services/sql-database/
[Lagerplads]: http://www.windowsazure.com/documentation/services/storage/

<!-- IMG List -->

[ChoicesDiagram]: ./media/choose-web-site-cloud-service-vm/Websites_CloudServices_VMs_3.png
