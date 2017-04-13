Ressource|Gratis|Delte (Preview)|Grundlæggende|Standard|Premium (Preview)</th>
---|---|---|---|---|---
[Internettet, mobile eller API apps](https://azure.microsoft.com/services/app-service/) per [App Service plan](../articles/app-service/azure-web-sites-web-hosting-plans-in-depth-overview.md)<sup>1</sup>|10|100|Ubegrænset<sup>2</sup>|Ubegrænset<sup>2</sup>|Ubegrænset<sup>2</sup>
[Logik apps](https://azure.microsoft.com/services/app-service/logic/) i [App-serviceaftale](../articles/app-service/azure-web-sites-web-hosting-plans-in-depth-overview.md)</a><sup>1</sup>|10|10|10|20 per core|20 per core
[App-serviceaftale](../articles/app-service/azure-web-sites-web-hosting-plans-in-depth-overview.md)|1 hver område|10 per ressourcegruppe|100 per ressourcegruppe|100 per ressourcegruppe|100 per ressourcegruppe
Beregne forekomsttype|Delt|Delt|Dedikeret<sup>3</sup>|Dedikeret<sup>3</sup>|Dedikeret<sup>3</sup></p>
[Skala ud](../articles/app-service-web/web-sites-scale.md) (maks forekomster)|1 delt|1 delt|3 dedikeret<sup>3</sup>|10 dedikeret<sup>3</sup>|20 dedikeret (50 i ASE)<sup>3,4</sup>
Lagerplads<sup>5</sup>|1 GB<sup>5</sup>|1 GB<sup>5</sup>|10 GB<sup>5</sup>|50 GB<sup>5</sup>|500 GB<sup>4,5</sup></p>
CPU klokkeslæt (kort)<sup>6</sup>|3 minutter|3 minutter|Ubegrænset, løn til standard [satser](https://azure.microsoft.com/pricing/details/app-service/)</a>|Ubegrænset, løn til standard rente|Ubegrænset, løn til standard rente
CPU tid (dag)<sup>6</sup>|60 minutter|240 minutter|Ubegrænset, løn til standard [satser](https://azure.microsoft.com/pricing/details/app-service/)</a>|Ubegrænset, løn til standard rente|Ubegrænset, løn til standard rente
Hukommelse (1 time)|1024 MB til hver App-serviceaftale|1024 MB til hver app|I/T.|I/T.|I/T.
Båndbredde|165 MB|Ubegrænsede, [dataoverførsel satser](https://azure.microsoft.com/pricing/details/data-transfers/) anvende|Ubegrænset, dataoverførsel bosiddende|Ubegrænset, dataoverførsel bosiddende|Ubegrænset, dataoverførsel bosiddende
Programmet arkitektur|32-bit|32-bit|32-bit/64-bit|32-bit/64-bit|32-bit/64-bit
Web Sockets hver forekomst<sup>7</sup>|5|35|350|Ubegrænset|Ubegrænset
Samtidige [fejlfindingen forbindelser](../articles/app-service-web/web-sites-dotnet-troubleshoot-visual-studio.md) i programmet på computeren|1|1|1|5|5
[azurewebsites.NET underdomæne med FTP-/ S og SSL](../articles/app-service-web/web-sites-configure-ssl-certificate.md)|X|X|X|X|X
Understøttelse af [brugerdefinerede domæne](../articles/app-service-web/web-sites-custom-domain-name.md)||X|X|X|X
Brugerdefineret domæne, der [understøtter SSL](../articles/app-service-web/web-sites-configure-ssl-certificate.md)|||Ubegrænset|Ubegrænset, 5 SNI SSL og 1 IP SSL-forbindelser, der er inkluderet|Ubegrænset, 5 SNI SSL og 1 IP SSL-forbindelser, der er inkluderet
Integreret justering af belastning||X|X|X|X
[Altid på](../articles/app-service-web/web-sites-configure.md)|||X|X|X
[Planlagte sikkerhedskopier](../articles/app-service-web/web-sites-backup.md)||||En gang om dagen|Én gang hver 5 minutter<sup>8</sup>
[Automatisk skala](../articles/app-service-web/web-sites-scale.md)|||X|X|X
[WebJobs](../articles/app-service-web/web-sites-create-web-jobs.md) <sup>9</sup>|X|X|X|X|X
[Azure Scheduler](https://azure.microsoft.com/services/scheduler/) support||X|X|X|X
[Slutpunkt overvågning](../articles/app-service-web/web-sites-monitor.md)|||X|X|X
[Midlertidige pladser (Preview)](../articles/app-service-web/web-sites-staged-publishing.md)||||5|20
Brugerdefinerede domæner per app</a>||500|500|500|500
SLA||<p>|99,9%|99.95%<sup>10</sup>|99.95%<sup>10</sup>

<sup>1</sup> Apps og kvoter for lagerplads er per App-serviceaftale, medmindre andet er angivet.  
<sup>2</sup> Det faktiske antal apps, som du kan hoste på disse computere, afhænger af aktiviteten Apps, størrelsen på de maskine forekomster og tilsvarende ressource anvendelsen.  
<sup>3</sup> Dedikeret forekomster kan være forskellige størrelser. Se [App servicepriser](https://azure.microsoft.com/pricing/details/data-transfers/pricing/details/app-service/) for at få flere oplysninger.  
<sup>4</sup> Premium niveau tillader op til 50 beregner forekomster (underlagt tilgængelighed) og 500 GB ledig plads på, når du bruger App Service miljøer og 20 beregne forekomster og 250 GB lagerplads ellers.  
<sup>5</sup> Lagergrænsen er den samlede indhold størrelse på tværs af alle apps i den samme App serviceaftale. Indstillinger for lagring af flere er tilgængelige i [App servicemiljø](../articles/app-service-web/app-service-web-configure-an-app-service-environment.md#storage)  
<sup>6</sup> Disse ressourcer er begrænset af fysiske ressourcer på dedikeret forekomster (forekomst størrelsen og antallet af forekomster).  
<sup>7</sup> Hvis du skalere en app i de grundlæggende trin til to forekomster, har du 350 samtidige forbindelser for hver af de to forekomster.  
<sup>8</sup> Premium niveau kan intervaller for sikkerhedskopiering ned op til hver 5 minutter, når du bruger App Service-miljøer, og 50 gange om dagen på anden måde.  
<sup>9</sup> Køre brugerdefineret eksekverbare filer og/eller scripts efter behov, til en tidsplan eller løbende som en baggrund opgave i din App Service forekomst. Altid er påkrævet for udførelse af fortløbende WebJobs. Azure Scheduler gratis- eller Standard er påkrævet for planlagte WebJobs. Der er ingen foruddefineret grænse på antallet WebJobs, som kan køre i en App Service-forekomst, men der er praktiske begrænsninger, der afhænger af, hvad programkoden forsøger at gøre.   
<sup>10</sup> SLA 99.95%, der er knyttet til installationer, der bruger flere forekomster med Azure trafik Manager konfigureret til failover.  
