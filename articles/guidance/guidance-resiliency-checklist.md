<properties
   pageTitle="Fleksibilitet tjekliste | Microsoft Azure"
   description="Tjekliste, der indeholder en vejledning til fleksibilitet problemstillinger under design."
   services=""
   documentationCenter="na"
   authors="petertaylor9999"
   manager="christb"
   editor=""
   tags=""/>

<tags
   ms.service="best-practice"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="10/24/2016"
   ms.author="petertay"/>

# <a name="azure-resiliency-guidance-resiliency-checklist"></a>Azure fleksibilitet vejledning: fleksibilitet tjekliste

Designe dit program af tolerance kræver planlægning af og afhjælpe fejl funktionsmåder, der kan opstå forskellige. Gennemgå elementerne i denne tjekliste mod dit programdesign for at gøre det mere robust.

## <a name="requirements"></a>Krav

- **Definere kundens tilgængelighedskrav.** Kunden har tilgængelighedskrav til komponenterne i dit program, og det kan påvirke dit program design. Få aftale fra din kunde for tilgængelighed mål af hvert tekststykke dit program, ellers dit design ikke kan opfylder kundens forventninger. Yderligere oplysninger finder du i afsnittet [definere dine krav til fleksibilitet](guidance-resiliency-overview.md#defining-your-resiliency-requirements) af [designe tolerant programmer til Azure](guidance-resiliency-overview.md) dokumentet.

## <a name="failure-mode-analysis"></a>Manglende tilstand analyse

- **Udføre en manglende tilstand analyse (FMA) for dit program.** FMA er en proces i forbindelse med opbygning fleksibilitet i et program tidligt i designfasen. Formålet med en FMA omfatter:  

    - Identificere, hvilke typer fejl kan opleve et program. 
    - Registrere potentielle effekter og påvirkningen af hver type af fejl på programmet.
    - Identificere gendannelse strategier. 

    Kan finde flere oplysninger under [designe tolerant programmer til Azure: fejl tilstand analysis][fma].  

## <a name="application"></a>Program

- **Installere flere forekomster af tjenester.** Services mislykkes milepælsdatoer, og hvis dit program afhænger af en enkelt forekomst af en tjeneste den milepælsdatoer mislykkedes også. Hvis du vil klargøre flere forekomster til [Azure App Service](../app-service/app-service-value-prop-what-is.md), Vælg en [App Service planlægge](../app-service/azure-web-sites-web-hosting-plans-in-depth-overview.md) , der indeholder flere forekomster. Konfigurere hver af dine roller for at bruge [flere forekomster](../cloud-services/cloud-services-choose-me.md#scaling-and-management)til Azure Cloud Services. [Virtuelle Azure-computere (FOS)](../virtual-machines/virtual-machines-windows-about.md), sikre dig, at din VM arkitektur indeholder mere end én VM og, at hver VM er inkluderet i en [tilgængelighed angive][availability-sets].   

- **Bruge en belastningsjustering til at distribuere anmodninger.** Belastningsjustering distribuerer anmodninger om dit program til forekomster af sund tjenesten ved at fjerne beskadiget forekomster fra rotation. Hvis din tjeneste bruger Azure App Service eller Azure Cloud Services, er det allerede Indlæs afstemmes for dig. Men hvis dit program bruger Azure FOS, skal du klargør belastningsjustering. Få vist [Azure justering af belastning](../load-balancer/load-balancer-overview.md) oversigten for at få flere oplysninger. 

- **Konfigurere Azure programmet Gateways, hvis du vil bruge flere forekomster.** Afhængigt af dit program krav være en [Azure Application Gateway](../application-gateway/application-gateway-introduction.md) bedre egnet til at distribuere anmodninger om til din programtjenester. Dog er enkelt forekomster af tjenesten Application Gateway ingen garanti for ved en SERVICENIVEAUAFTALE så det er muligt, at dit program kunne mislykkes, hvis forekomsten Application Gateway mislykkes. Klargøring af mere end én det mellemstore eller større Application Gateway-forekomst for at garantere tilgængeligheden af tjenesten under vilkårene for [SERVICENIVEAUAFTALE](https://azure.microsoft.com/support/legal/sla/application-gateway/v1_0/).

- **Brug tilgængelighed sæt for hvert niveau i programmet**. Placere din forekomster i en [tilgængelighed angive] [ availability-sets] garanterer forbindelse til mindst én forekomst af VM i henhold til [SERVICENIVEAUAFTALE](https://azure.microsoft.com/support/legal/sla/virtual-machines/v1_2/). Hvis din FOS ikke er i et tilgængelighed sæt, du ikke har garanterer beskytte dem, og det er muligt, at de kan alle mislykkes eller opdateres samtidigt. 

- **Overvej at implementere dit program på tværs af flere områder.** Hvis dit program er installeret til et enkelt område, i hændelsen sjældne det hele område bliver utilgængeligt, dit program udgår også. Det kan være kan accepteres under vilkårene for dit program SERVICENIVEAUAFTALE. Hvis det er tilfældet, kan du overveje at implementere dit program og tjenester på tværs af flere områder. En installation af flere områder kan bruge en aktiv-aktiv mønster (distribuere anmodninger på tværs af flere aktive forekomster) eller en aktiv / passiv mønster (bevarelse af en forekomst af "Varm" i reserver, i tilfælde af den primære forekomst mislykkes). Vi anbefaler, at du installerer flere forekomster af din programtjenester på tværs af internationale par. Du kan finde flere oplysninger, se [Business løbende og genoprettelse efter nedbrud (BCDR): Azure parvis områder](../best-practices-availability-paired-regions.md).

- **Implementere fleksibilitet mønstre for fjernstyring, hvor det er relevant.** Hvis dit program afhænger af kommunikation mellem remote services, mislykkes milepælsdatoer kommunikationsstien. Hvis der er flere fejl, kan de resterende sunde forekomster af din programtjenester overvældet anmodninger. Der er flere mønstre nyttigt til håndtering af almindelige fejl, herunder timeout mønsteret, [Prøv igen mønster][retry-pattern], [afbryder] [ circuit-breaker] mønster og andre. Se [designe tolerant programmer til Azure](guidance-resiliency-overview.md#implementing-resiliency-strategies)kan finde flere oplysninger. 

- **Brug Autoskalering til at besvare øges i belastning.** Hvis dit program ikke er konfigureret til at skalere ud automatisk, mens du indlæse øges, kan det skyldes, at din programtjenester mislykkes, hvis de mættes med anmodninger. Yderligere oplysninger finder du følgende:

    - Generel: [skalerbarhed tjekliste](../best-practices-scalability-checklist.md) 
    - Azure App Service: [skalere forekomst Tæl manuelt eller automatisk][app-service-autoscale]
    - Cloud Services: [Sådan automatisk skala en skybaseret tjeneste][cloud-service-autoscale]
    - Virtuelle maskiner: [automatisk skalering og virtuelt skala angiver][vmss-autoscale]


- **Implementere asynkrone handlinger, når det er muligt.** Synkron handlinger kan monopolisere ressourcer og blokere andre handlinger, mens kalderen venter på at fuldføre processen. Designe hver del af dit program til at tillade asynkrone handlinger, når det er muligt. Finde flere oplysninger om, hvordan du implementere asynkron programmering i C# [asynkron Programming med asynkrone og venter på at blive][asynchronous-c-sharp].

- **Brug Azure trafik Manager til at omdirigere trafikken til dit program til forskellige områder.**  [Azure trafik Manager] [ traffic-manager] udfører justering af belastning på niveauet for DNS- og kan omdirigere trafikken til forskellige områder, der er baseret på [trafik routing] [ traffic-manager-routing] afskrivningsmetode, som du angiver og tilstand for dit program slutpunkter. 

- **Konfigurere og teste sundhed sonder til Indlæs balancere og trafik ledere.** Sikre, at din sundhed logik kontrollerer vigtige dele af systemet og besvarer sundhed sonder korrekt.

    - Tilstanden sonder til [Azure trafik Manager] [ traffic-manager] og [Azure justering af belastning] [ load-balancer] et bestemt formål. Til trafik Manager sundhed efterprøvning af af bestemmer, om overføres til et andet område. For en belastningsjustering bestemmer den, om du vil fjerne en VM fra rotation.      

    - En trafik Manager efterprøvning af af, skal din sundhed slutpunkt se kritiske afhængigheder, der er implementeret i samme region, og hvis fejl skal udløse en failover til et andet område.  

    - For en belastningsjustering skal sundhed slutpunktet rapportere tilstanden af VM. Ikke omfatte andre niveauer eller eksterne tjenester. Ellers medfører en fejl, der opstår uden for VM belastning fjerne VM fra rotation.

    - Du kan finde en vejledning i at implementere sundhedsovervågning i programmet, [Sundhed slutpunkt overvågning mønster](https://msdn.microsoft.com/library/dn589789.aspx).

- **Overvåge tredjepartstjenester.** Hvis dit program har afhængigheder af tredjepartstjenester, identificere, hvor og hvordan disse tredjepartstjenester kan mislykkes, og hvad effekt fejl skal være på dit program. En tredjepartstjenester må ikke indeholde overvågning og diagnosticering, så det er vigtigt at logge af din starter af dem og koordinere dem med dit program tilstand og diagnosticering logføring ved hjælp af et entydigt id. Se yderligere oplysninger om bedste fremgangsmåder til overvågning og diagnosticering [overvågnings- og diagnosticering vejledning] i[ monitoring-and-diagnostics-guidance] dokument.

- **Sørg for, at du bruge en hvilken som helst tredjepartstjenester giver en SERVICENIVEAUAFTALE.** Hvis dit program afhænger af en tredjeparts-tjeneste, men fra tredjepart giver ingen garanti for tilgængelighed i form af en SERVICENIVEAUAFTALE, kan dit program tilgængelighed også sikres. Din SERVICENIVEAUAFTALE er kun så godt som komponenten mindst tilgængelig i dit program.


## <a name="data-management"></a>Datastyring

- **Forstå gentagelse metoder til dit program datakilder.** Programmets data bliver gemt i forskellige datakilder og har forskellige tilgængelighedskrav. Evaluere gentagelse metoder for hver type datalagring i Azure, herunder [Azure lagerplads gentagelse](../storage/storage-redundancy.md) og [SQL aktive geografisk-databasereplikering](../sql-database/sql-database-geo-replication-overview.md) for at sikre, at dit program datakrav er opfyldt.

- **Sørg for, at ingen enkelt brugerkonto har adgang til både fremstilling og sikkerhedskopiering af data.** Dine data sikkerhedskopier er beskadiget, hvis en enkelt brugerkonto har tilladelse til at skrive både fremstilling og sikkerhedskopiering kilder. En hacker kunne bevidst slettes alle dine data, mens en almindelig bruger kan komme til at slette den. Designe dit program tilladelse til at begrænse tilladelserne for hver brugerkonto, så kun de brugere, der kræver skriveadgang har skriveadgang og det er kun at fremstilling eller sikkerhedskopi, men ikke begge dele.

- **Dokument, din datakilde mislykkes og mislykkes tilbage proces og teste den.** I de tilfælde hvor datakilden mislykkes catastrophically nødt en human operator til at følge et sæt af dokumenterede instruktioner for at skifte til en ny datakilde. Hvis dokumenterede trinnene har fejl, kan en operator kan ikke korrekt følge dem og mislykkes over ressourcen. Jævnligt teste instruktion trinnene for at bekræfte, at en operator, følge dem er kunne korrekt mislykkes og mislykkes tilbage datakilden.

- **Validere dine data sikkerhedskopier.** Kontrollér jævnligt, er de sikkerhedskopierede data, som du forventer ved at køre et script til at validere dataintegritet, skemaet og forespørgsler. Der er ingen ide har en sikkerhedskopi, hvis den ikke er praktisk til at gendanne dine datakilder. Log og rapportere eventuelle uoverensstemmelser, så tjenesten sikkerhedskopiering kan repareres.

- **Overvej at bruge en lagerplads kontotype, som er geografisk overflødige.** Data gemt i en Azure-lager konto replikeres altid lokalt. Der er dog flere gentagelse strategier til at vælge mellem, når en lagerplads konto er klargjort. Vælg [Azure-læseadgang geografisk overflødige lager (RA-GRS)](../storage/storage-redundancy.md#read-access-geo-redundant-storage) at beskytte dine programmet data mod sjældne store og små bogstaver, når et hele område bliver utilgængeligt.

    > [AZURE.NOTE] For FOS, ikke stole på RA-GRS gentagelse gendanne VM diske (Virtuelle filer). I stedet bruge [Azure sikkerhedskopi][azure-backup].   

## <a name="operations"></a>Handlinger

- **Implementere overvågning og advarer bedste fremgangsmåder i dit program.** Uden stort overvågning, diagnosticering og advarer findes der ingen måde at registrere fejl i dit program og besked en operator for at rette dem. Du kan finde flere oplysninger om bedste fremgangsmåder, se [overvågnings- og diagnosticering vejledning] [ monitoring-and-diagnostics-guidance] dokument.

- **Måle fjernopkald statistik og foretage oplysninger tilgængelige for programmet teamet.**  Hvis du ikke kan registrere og rapportere fjernopkald statistik i realtid og en nem måde at gennemgå disse oplysninger, der operations-teamet ikke har en omgående visning i tilstanden for dit program. Problemer i tjenesterne, og hvis du kun måling gennemsnitlige fjernopkald gang, du ikke har nok oplysninger til at få vist. Opsummer fjernopkald målepunkter som ventetid, overførselshastighed og fejl i 99 og 95 fraktiler. Udføre statistiske analyser på målepunkter til at afdække fejl, der opstår inden for hver fraktil.

- **Registrere antallet af midlertidige undtagelser og forsøg over en passende tidsramme.** Hvis du ikke kan registrere og overvåge midlertidige undtagelser og gentagne forsøg over tid, er det muligt, at et problem eller en fejl kan være skjult af dit program forsøg. Det vil sige, hvis din overvågning og registrering kun viser lykkes eller mislykkes handlingen, skjules fakultet, som handlingen havde forsøges flere gange på grund af undtagelser. En tendens til stigende undtagelser over tid angiver, at tjenesten er har et problem og mislykkes muligvis. Finde flere oplysninger, [Prøv igen service specifikke vejledninger][retry-service-guidance].

- **Implementere et system med tidlig varsling, der advarer en operator.** Identificere KPI'er indikatorer for dit program sundhed, som midlertidige undtagelser og remote call ventetid, og angive relevante tærskelværdi for hver af dem. Sende en besked til handlinger, når grænseværdien nås. Angiv tærsklerne på niveauer, der identificerer problemer, før de bliver kritiske og besvares gendannelse.

- **Dokument udgivelsesproces for dit program.** Uden detaljerede release Procesdokumentation, kan en operator installere en forkert opdatering eller forkert konfigurere indstillinger for dit program. Klart definere og dokumentere release-proces, og sikre, at den er tilgængelig for hele gruppen Handlinger. Bedste fremgangsmåder til tolerant installation af dit program gennemgås i [tolerant installation] [ guidance-resilient-deployment] sektion i dokumentet fleksibilitet vejledning.

- **Sørg for, at mere end én person i teamet er modtager oplæring til at overvåge programmet og udføre en manuel gendannelse trin.** Hvis du kun har en enkelt operator på den gruppe, der kan overvåge programmet og starte gendannelse trin, bliver vedkommende for fejl fra ét sted. Undervise flere personer på registrering og gendannelse, og Sørg for, at der er altid mindst én aktive når som helst.

- **Automatisere dit program installationsprocessen.** Hvis medarbejderne handlinger er påkrævet for at installere programmet manuelt, kan human fejl medføre installationen mislykkes. Du kan finde flere oplysninger om bedste fremgangsmåder til at automatisere programmet installation, under [robust installation] [ guidance-resilient-deployment] sektion i dokumentet fleksibilitet vejledning.

- **Designe din udgivelsesproces at maksimere tilgængeligheden af programmet.** Hvis din udgivelsesproces kræver services til at gå offline under installationen, bliver dit program ikke tilgængelig, indtil de finder igen er online. Bruge [blå/grøn](http://martinfowler.com/bliki/BlueGreenDeployment.html) eller [Kanariske slip](http://martinfowler.com/bliki/CanaryRelease.html) installation metode til at installere programmet til fremstilling. Begge af disse metoder involverer anvender din release kode sammen med fremstilling kode, så brugerne af release kode kan blive omdirigeret til fremstilling kode i tilfælde af en fejl. Få mere at vide under [tolerant installation] [ guidance-resilient-deployment] sektion i dokumentet fleksibilitet vejledning.

- **Log og overvåge dit program installationer.** Hvis du bruger midlertidigt installation teknikker som blå/grøn eller Kanariske versioner, der vil være mere end én version af dit program, der kører i fremstilling. Hvis der opstår et problem, skal er det vigtigt at finde ud af, hvilken version af dit program forårsager et problem. Implementere en robust logføring strategi for at hente den ønskede version-specifikke oplysninger som muligt. 

- **Sørg for, at dit program ikke kører sig i forhold til [Azure abonnement begrænser](../azure-subscription-service-limits.md).** Azure abonnementer har begrænsninger på visse ressourcetyper, som antallet af grupper, antal kerner og antallet af lagerplads konti.  Hvis dine krav til programmet overskrider Azure abonnement begrænsninger, kan du oprette en anden Azure-abonnement og klargøring tilstrækkelige ressourcer der.

- **Sørg for, at dit program ikke kører sig i forhold til [hver tjeneste begrænser](../azure-subscription-service-limits.md).** Individuelle Azure tjenester har forbrug begrænsninger &mdash; for eksempel begrænser på lager, overførselshastighed, antal forbindelser, anmodninger sekundet og anden metrik. Dit program mislykkes, hvis den forsøger at bruge ressourcer ud over disse grænser. Dette kan medføre nedetid variere den benyttede og muligt for brugere, der påvirkes. 

    Afhængigt af bestemte tjenesten og dine krav til programmet på computeren, kan du ofte undgå disse begrænsninger ved at skalere (for eksempel, vælge et andet priser niveau) eller skalering ud (tilføjelse af nye forekomster).  

- **Designe dit program lagerplads krav til falder inden for Azure lagerplads skalerbarhed og ydeevne destinationer.** Azure-lager er udviklet til at fungere i foruddefinerede skalerbarhed og ydeevne destinationer, så du skal designe dit program tilladelse til at bruge lagerplads i disse mål. Hvis du overskrider disse mål påvirkes dit program lagerplads (throttling). Klargør yderligere lagerplads konti for at løse dette problem. Hvis du kører sig i forhold til grænsen for lagerplads konto, du klargør yderligere Azure-abonnementer og derefter klargøre yderligere lagerplads konti der. Se [Azure lagerplads skalerbarhed og ydeevne destinationer](../storage/storage-scalability-targets.md)kan finde flere oplysninger.

- **Vælg den rigtige VM størrelse for dit program.** Måle den faktiske CPU, hukommelse, disk og i/o-af din FOS fremstilling og kontrollere, at du har valgt VM størrelsen er tilstrækkelige. Hvis ikke, kan dit program opleve kapacitetsproblemer, som FOS løse deres begrænsninger. VM størrelser er beskrevet mere detaljeret i [størrelser for virtuelle maskiner i Azure](../virtual-machines/virtual-machines-windows-sizes.md) dokumentet.

- **Afgøre, om dit program arbejdsbyrde er stabil eller varierende over tid.** Hvis din arbejdsbyrde varierer over tid, angiver Brug Azure VM skala til automatisk skala antallet af VM forekomster. Ellers skal nødt du til at øge eller mindske antallet af FOS manuelt. Få vist [Virtuelt skala sæt oversigt](../virtual-machine-scale-sets/virtual-machine-scale-sets-overview.md)kan finde flere oplysninger.

- **Du kan vælge den rigtige webtjenesten for Azure SQL-Database.** Hvis dit program bruger Azure SQL-Database, kan du sikre dig, at du har markeret den relevante webtjenesten. Hvis du vælger et trin, ikke der kunne håndtere krav til dit program databasen transaktion enhed (DTU), sænkes dataforbrug dine. Du kan finde flere oplysninger om at vælge den korrekte serviceaftale, under den [Indstillinger for SQL-Database og ydeevne: forstå, hvad der er tilgængeligt i hver webtjenesten](../sql-database/sql-database-service-tiers.md) dokument. 

- **Har en rollback plan til installation.** Det er muligt, at installationen af programmet kan mislykkes og medføre, at programmet til utilgængelige. Designe en rollback proces for at gå tilbage til en sidste kendte gode version og minimere nedetid. Se den [tolerant installation] [ guidance-resilient-deployment] sektion i dokumentet fleksibilitet vejledning for at få flere oplysninger. 

- **Oprette en proces til at arbejde med Azure support.** Hvis processen for at kontakte [Azure understøtter](https://azure.microsoft.com/support/plans/) ikke er angivet, før du kontakter support behov, vil nedetid forlænges som supportprocessen navigeret for første gang. Medtage processen for at kontakte support og Eskalering problemer som en del af dit program fleksibilitet fra begyndelsen.

- **Sørg for, at dit program ikke bruger mere end det maksimale antal lagerplads konti per abonnement.** Azure kan maksimalt 200 lagerplads konti per abonnement. Hvis programmet kræver mere lagerplads brugerkonti, end er tilgængelige i dit abonnement, er du nødt til at oprette et nyt abonnement og oprette ekstra lagerplads konti der. Se [Azure-abonnement og begrænsninger for tjenesten, kvoter, og begrænsninger](../azure-subscription-service-limits.md#storage-limits)kan finde flere oplysninger.

- **Sikre, at dit program ikke overstiger skalerbarhed destinationer om en virtuelt disk.** En Azure IaaS VM understøtter vedhæfte et antal datadisce afhængigt af flere faktorer, herunder VM størrelse og lagerplads kontotype. Hvis dit program overskrider skalerbarhed destinationer om en virtuelt disk, klargøre ekstra lagerplads konti og oprette de virtuelle maskine der. Få mere at vide under [Azure lagerplads skalerbarhed og ydeevne destinationer] (… / storage/storage-scalability-targets.md#scalability-targets-for-virtual-machine-disks)

## <a name="test"></a>Test

- **Udføre failover og failback test for dit program.** Hvis du ikke har fuldt testet failover og failback, være du ikke sikker på, at de afhængige tjenester i dit program kommer tilbage i en synkroniseret måde under nedbrud. Sørg for, at dit program afhængige tjenester failover og fail tilbage i den rigtige rækkefølge.

- **Udføre tests for dit program-fejl – indsættelse.** Dit program kan mislykkes på mange forskellige årsager, såsom certifikat udløb konsumption af systemressourcer i en VM eller mislykkede forsøg på lagerplads. Test dit program i et miljø, så tæt som muligt med fremstilling og ved at simulere eller udløser reelle følgende mislykkede forsøg. For eksempel Slet certifikater, kunstigt forbruge systemressourcer, eller Slet en lagerplads kilde. Bekræft dit program mulighed for at gendanne fra alle typer fejl, alene og sammen. Kontrollere, at mislykkede forsøg ikke ud eller overlappende via dit system.

- **Kør test i fremstilling ved hjælp af begge korte og reelle brugerdata.** Test og er sjældent identiske, så det er vigtigt at bruge blå/grøn eller en Kanariske installation og teste dit program i fremstilling. Dette kan du teste dit program i fremstilling reelle belastning og sikre, at den fungerer som forventet, når installeret fuldt ud.

## <a name="security"></a>Sikkerhed

- **Implementere programmet beskyttelse mod fordelt nægtelse af service (DDoS)-angreb.** Azure services er beskyttet mod DDos angreb på netværk lag. Azure kan dog beskytte mod programlag angreb, fordi det er svært at skelne mellem SAND brugeranmodninger fra hacker anmodninger. Du kan finde flere oplysninger om, hvordan du kan beskytte mod programlag DDoS angreb, i afsnittet "Beskyttelse mod DDoS" i [Microsoft Azure netværkssikkerhed](http://download.microsoft.com/download/C/A/3/CA3FC5C0-ECE0-4F87-BF4B-D74064A00846/AzureNetworkSecurity_v3_Feb2015.pdf) (PDF-overførsel).

- **Implementere princippet bag mindste tilladelse til adgang til programmets ressourcer.** Standard for adgang til programmets ressourcer skal være så restriktiv som muligt. Give højere niveau tilladelser på grundlag af godkendelse. Give meget lemfældige adgang til dit program ressourcer som standard kan medføre en person utilsigtet eller bevidst sletter ressourcer. Azure giver [Rollebaseret adgangskontrol](../active-directory/role-based-access-built-in-roles.md) for at administrere brugerrettigheder, men det er vigtigt at kontrollere Mindste rettighed tilladelser til andre ressourcer, som har deres egne tilladelser systemer såsom SQL Server. 

## <a name="telemetry"></a>Telemetri

- **Log telemetridata, mens programmet kører i produktionsmiljøet.** Registrere oplysninger om robust telemetri, mens programmet kører i produktionsmiljøet, eller du har ikke tilstrækkelige oplysninger til at diagnosticere årsagen til problemer, mens det aktivt fungerer brugere. Yderligere oplysninger findes på listen logføring bedste fremgangsmåder er tilgængelig i [overvågnings- og diagnosticering vejledning] [ monitoring-and-diagnostics-guidance] dokument.

- **Implementere logføring ved hjælp af en asynkron mønster.** Hvis der synkron logføring operationer, kan din programkode blokeres. Sørg for, at din logføring handlinger anvendes som asynkrone handlinger. 

- **Koordinere logdata på tværs af tjenesten grænser.** I et typisk n lag til computeren, kan en bruger anmodning om gennemgang af flere service grænser. For eksempel er en anmodning om et kommer typisk i web-lag og overføres til niveauet i business og til sidst bevaret i data lag. I mere komplekse scenarier, kan en brugeranmodning distribueres til mange forskellige services og butikker. Sørg for, at logføringssystemet svarer opkald på tværs af tjenesten, så du kan spore anmodningen i hele dit program.

##  <a name="azure-resources"></a>Azure ressourcer 

- **Brug Azure ressourcestyring skabeloner til klargøring ressourcer.** Ressourcestyring skabeloner gør det nemmere at automatisere installationer via PowerShell eller Azure Kli, der fører til en mere pålidelig installationsprocessen. Få mere at vide under [Oversigt over Azure ressourcestyring][resource-manager].

- **Give ressourcer sigende navne.** Give ressourcer et meningsfuldt navn gør det nemmere at finde en bestemt ressource og forstå sin rolle. Du kan finde flere oplysninger, se [anbefalede navngivningskonventioner for Azure ressourcer](guidance-naming-conventions.md) 

- **Brug rollebaseret adgangskontrol (RBAC)**. Brug RBAC til at styre adgangen til Azure ressourcer, som du installerer. RBAC kan du tildele roller tilladelse til medlemmer af teamet DevOps for at forhindre utilsigtet sletning eller ændringer til udløst ressourcer. Du kan finde flere oplysninger, se [komme i gang med administration af adgang i portalen Azure](../active-directory/role-based-access-control-what-is.md) 

- **Bruge ressourcelåse for kritiske ressourcer, som FOS.** Ressourcelåse forhindre en operator i kommer til at slette en ressource. Du kan finde flere oplysninger, se [Lås ressourcer med Azure ressourcestyring](../resource-group-lock-resources.md) 

- **Internationale værdipar.** Når du installerer på to områder, kan du vælge områder fra den samme internationale par. I tilfælde af en bred afbrydelse prioriteres gendannelse af ét område af alle par. Nogle af tjenesterne som geografisk overflødige lagerplads giver automatisk gentagelse til parvis område. Du kan finde flere oplysninger, se [Business løbende og genoprettelse efter nedbrud (BCDR): Azure parvis områder](../best-practices-availability-paired-regions.md) 

- **Arranger ressourcegrupper ved at funktionen og livscyklus**.  Generelt, skal en ressourcegruppe indeholder ressourcer, der deler den samme livscyklus. Det gør det nemmere at administrere installationer, slette test installationer og tildele adgangsrettigheder, reducere risikoen for, at et produktionsmiljø ved et uheld slettes eller redigeres. Opret separate ressourcegrupper for fremstilling, udvikling, og test miljøer. Sætte ressourcer for hvert område i separate ressourcegrupper i et område med flere installation. Det gør det nemmere at geninstallere ét område uden at påvirke de andre regioner. 

## <a name="azure-services"></a>Azure-tjenester 

Følgende tjekliste elementer gælder for bestemte tjenester i Azure.

###  <a name="app-service"></a>App Service 

- **Brug Standard eller Premium niveau.** Disse lag understøtter midlertidige pladser og automatiske sikkerhedskopier. Få mere at vide under [Oversigt over dybden planer for Azure App Service](../app-service/azure-web-sites-web-hosting-plans-in-depth-overview.md) 

- **Undgå skalering op eller ned.** I stedet, Vælg et niveau og forekomst størrelse, der opfylder dine ydeevnekrav til under typisk indlæsning, og klik derefter [Skaler ud](../app-service-web/web-sites-scale.md) forekomster til at håndtere ændringer i trafik lydstyrken. Skalering kan op og ned udløse en genstart programmet.  

- **Gem konfiguration som app-indstillinger.** Brug indstillinger til at holde konfigurationsindstillinger som app. Angiv indstillingerne i dine ressourcestyring skabeloner eller ved hjælp af PowerShell, så du kan anvende dem som en del af en automatiseret udrulning / opdatere processen, som er mere pålidelig. Se [konfigurere webapps i Azure App Service](../app-service-web/web-sites-configure.md)kan finde flere oplysninger. 

- **Oprette separate App Service-planer til fremstilling og test.** Brug ikke pladser på din installationen produktionsmiljø til test.  Alle apps i den samme App serviceaftale dele de samme VM forekomster. Hvis du placerer fremstilling og test installationer i samme plan, kan det have en negativ påvirker installationen i et produktionsmiljø. Indlæse test kan for eksempel forringe webstedet direkte fremstilling. Når du indsætter test installationer i en separat plan, isolere du dem fra versionen af.  

- **Separat Onlines fra nettet API'er**. Hvis din løsning er både en front end- og web API, kan du overveje at decomposing dem separat App Service desktopprogrammer. Dette design gør det nemmere at opdele løsningen ved arbejdsbyrde. Du kan køre WebApp og API i separat App Service-planer, så de kan skaleres uafhængigt af hinanden. Hvis du ikke har brug for dette niveau af skalerbarhed på første, kan du installere apps til samme plan og flytte dem ind i separate planer senere, hvis det er nødvendigt.

- **Undgå at bruge funktionen App Service sikkerhedskopiering til at sikkerhedskopiere Azure SQL-databaser.** I stedet bruge [SQL-Database automatisk sikkerhedskopiering][sql-backup]. App Service sikkerhedskopi eksporterer databasen til en SQL .bacpac fil, der koster DTUs.  

- **Installere en midlertidig slot.** Oprette en installation plads til midlertidige. Installere programmet opdateringer til den midlertidige plads, og Bekræft installationen, før Skift til fremstilling. Dette mindsker risikoen for en forkert opdatering i fremstilling. Det sikrer også, at alle forekomster er varmet op, før der skiftet til fremstilling. Mange programmer har en betydeligt varmer op og kolde starttidspunktet. Se [konfigurere arrangere miljøer til webapps i Azure App Service](../app-service-web/web-sites-staged-publishing.md)kan finde flere oplysninger. 

-  **Oprette en installation slot indeholde den sidste kendte fungerende (LKG) installation.** Når du installerer en opdatering til fremstilling, kan du flytte den forrige produktionsmiljø i LKG slotten. Det gør det nemmere at annullere en forkert installation. Hvis du oplever problemer senere, kan du hurtigt vende tilbage til LKG versionen. Du kan finde flere oplysninger, se [Azure referencearkitektur: grundlæggende webprogram](guidance-web-apps-basic.md). 

- **Aktivere logføring af diagnosticering**, herunder programmet logføring og web server logføring. Logføring er vigtige for overvågning og diagnosticering. Se [aktivere logføring for webapps i Azure App Service diagnosticering](../app-service-web/web-sites-enable-diagnostic-log.md)

- **Log på blob storage**. Det gør det nemmere at indsamle og analysere dataene. 

- **Oprette en separat lagerplads konto for logfiler.** Brug ikke den samme konto lagerplads til logfiler og -programmet data. Dette hjælper med at forhindre, at logføring til at reducere programmets ydeevne. 

- **Overvågning af ydeevnen.** Brug en overvågning tjeneste såsom [Nye Relic](http://newrelic.com/) eller [Program indsigt](../application-insights/app-insights-overview.md) til skærm programmet ydeevne og funktionalitet, under indlæsning af ydeevnen.  Overvågning af ydeevnen giver dig realtid indsigt i programmet. Det gør det muligt at diagnosticere problemer og udføre Rodårsagsanalyse antal mislykkede forsøg. 


###  <a name="application-gateway"></a>Application Gateway 

- **Klargøring af mindst to forekomster.** Installere Application Gateway med mindst to forekomster. En enkelt forekomst er en enkelt udgangspunktet for fejl. Bruge to eller flere forekomster til redundans og skalerbarhed. For at opnå [SERVICENIVEAUAFTALE](https://azure.microsoft.com/support/legal/sla/application-gateway/v1_0/), skal du klargøre to eller flere forekomster af mellemstore eller større. 

### <a name="azure-search"></a>Azure søgning

- **Klargøring af mere end én replika.** Bruge mindst to replikaer til Læs høj tilgængelighed eller tre for skrivebeskyttet høj tilgængelighed.

- **Konfigurere indeks til flere områder installationer.** Hvis du har en installation af flere områder, kan du overveje at dine indstillinger for løbende i indeksering.

    - Hvis datakilden er geografisk replikerede, peg hver indekseringsprogram for hver internationale Azure søgetjeneste kildereplikaen dens lokale data.  

    - Hvis datakilden ikke er geografisk replikerede, peg flere indeks på den samme datakilde, så Azure Søg tjenester i flere områder løbende og uafhængigt indeksere fra datakilden. Du kan finde flere oplysninger, se [Azure Søg ydeevne og optimering overvejelser][search-optimization].

###  <a name="azure-storage"></a>Azure-lager 

- **Programmet data skal du bruge læseadgang geografisk overflødige lagerplads (RA-GRS).** RA-GRS lagerplads kopieres dataene til en sekundær område og giver skrivebeskyttet adgang fra den sekundære område. Hvis der er en lagerplads afbrydelse i den primære region, kan programmet læse data fra den sekundære område. Du kan finde yderligere oplysninger finder [Azure-lager gentagelse](../storage/storage-redundancy.md).

- **For VM disk, bruge Premium-lagerplads** Kan finde flere oplysninger under [Premium lagerplads: High-Performance lagerplads til Azure virtuelt arbejdsbelastninger](../storage/storage-premium-storage.md).

- **Oprette en sikkerhedskopi kø i et andet område for kø lagerplads.** For kø lagerplads har en skrivebeskyttet replika begrænset brug, fordi du ikke kan kø eller annullering i kø elementer. I stedet, oprette en sikkerhedskopi kø på en lagerplads konto i et andet område. Hvis der er en lagerplads afbrydelse, kan programmet bruge Sikkerhedskopiering køen, indtil det primære område bliver tilgængelig igen. Så programmet kan stadig behandle nye anmodninger.  

###  <a name="documentdb"></a>DocumentDB 

- **Gentage databasen på tværs af områder.** Med en konto med flere områder har databasen DocumentDB én Skriv område og flere Læs områder. Hvis der er en fejl i området, skrive, kan du læse fra en anden replika. Klient-SDK håndterer dette automatisk. Du kan også mislykkes over Skriv område til et andet område. Du kan finde yderligere oplysninger finder [fordel data globalt med DocumentDB](../documentdb/documentdb-distribute-data-globally.md).


###  <a name="sql-database"></a>SQL-Database 

- **Brug Standard eller Premium niveau.** Disse lag giver en længere punkt-in-time Gendan periode (35 dage). Se [Indstillinger for SQL-Database og ydeevne](../sql-database/sql-database-service-tiers.md)kan finde flere oplysninger.

- **Aktivere overvågning af SQL-Database.** Overvågning kan bruges til at diagnosticere skadelige angreb eller human fejl. Yderligere oplysninger finder du se [Introduktion til SQL database overvågning](../sql-database/sql-database-auditing-get-started.md). 

- **Brug aktive geografisk-gentagelse** Brug aktive geografisk-gentagelse til at oprette et læsevenligt sekundært i et andet område.  Hvis din primære database mislykkes, eller blot skal gøres tilgængelige offline, skal du udføre en manuel failover til den sekundære database.  Indtil du ikke, forbliver den sekundære database skrivebeskyttet tilstand.  Du kan finde yderligere oplysninger finder [SQL aktive geografisk-databasereplikering](../sql-database/sql-database-geo-replication-overview.md). 

- **Brug sharding**. Overvej at bruge sharding til at dele databasen vandret. Sharding kan give fejl isolationsniveauet. Du kan finde flere oplysninger [Skalering ud med Azure SQL-Database](../sql-database/sql-database-elastic-scale-introduction.md). 

- **Brug punkt-in-time Gendan til at gendanne fra mennesker fejl.**  Punkt-in-time Gendan returnerer din database til et tidligere tidspunkt. Du kan finde flere oplysninger, se [gendanne Azure SQL-database ved hjælp af automatiseret databasesikkerhedskopier][sql-restore].

- **Brug geografisk-Gendan til at gendanne fra en tjeneste afbrydelse.** Geografisk gendannelse gendanner en database fra en geografisk overflødige sikkerhedskopi.  Du kan finde flere oplysninger, se [gendanne Azure SQL-database ved hjælp af automatiseret databasesikkerhedskopier][sql-restore].


###  <a name="sql-server-running-in-a-vm"></a>SQL Server (kører i et VM)

- **Gentage databasen.** Bruge SQL Server altid på tilgængelighed grupper til at gentage databasen. Giver høj tilgængelighed, hvis en SQL Server-forekomst mislykkes. Du kan finde flere oplysninger, se [flere oplysninger...](guidance-compute-n-tier-vm.md) 

- **Sikkerhedskopiere databasen**. Hvis du allerede bruger [Azure sikkerhedskopi](https://azure.microsoft.com/documentation/services/backup/) til at sikkerhedskopiere dine FOS, kan du overveje at bruge [Azure sikkerhedskopi til SQL Server arbejdsmængder ved hjælp af DPM](../backup/backup-azure-backup-sql.md). Med denne metode er der en ekstra administratorrolle for organisationen og en samlet gendannelse procedure for FOS og SQL Server. Ellers skal bruge [SQL Server administrerede Backup til Microsoft Azure](https://msdn.microsoft.com/library/dn449496.aspx). 


###  <a name="traffic-manager"></a>Trafik Manager 

- **Udføre manuel failback.** Når du har en trafik Manager failover, udføre manuel failback i stedet for automatisk ned tilbage. Kontroller, at alle programmet delsystemer er sund før ned tilbage.  Ellers kan du oprette en situation, hvor programmet spejlvendes frem og tilbage mellem datacentre. Du kan finde yderligere oplysninger finder [Kører FOS i flere områder](guidance-compute-multiple-datacenters.md).

- **Opret et sundhed efterprøvning af af slutpunkt**. Oprette et brugerdefineret slutpunkt, som oplyser om den overordnede status af programmet. Dette gør det muligt for trafik Manager mislykkes, hvis ikke alle kritiske vej, ikke kun front end. Slutpunktet skal returnere en HTTP-fejlkode, hvis en hvilken som helst kritiske afhængighed er beskadiget eller er utilgængelig. Rapport ikke fejl i ikke-kritisk services, men. Ellers kan sundhed efterprøvning af af udløse failover, når det ikke er nødvendigt, oprette forkerte forekomster. Se [trafik Manager slutpunkt overvågning og failover](../traffic-manager/traffic-manager-monitoring.md)kan finde flere oplysninger.


###  <a name="virtual-machines"></a>Virtuelle maskiner 

- **Undgå at køre en fremstilling arbejdsbyrde på en enkelt VM.** En enkelt VM installation er ikke tolerant for planlagt eller ikke-planlagt vedligeholdelse. I stedet placere flere FOS i en tilgængelighed eller VM skala sæt med belastningsjustering foran. For at opnå SERVICENIVEAUAFTALEN, skal være installeret mindst to virtuelle maskiner i det samme sæt tilgængelighed. Se [Oversigt over virtuelt skala-sæt](../virtual-machine-scale-sets/virtual-machine-scale-sets-overview.md)kan finde flere oplysninger. 

- **Angiv angive, når du klargør VM er ledige.** Der findes i øjeblikket ingen måde at føje en ressourcestyring VM til en tilgængelighed, angives, efter VM er klargjort. Når du tilføjer en ny VM til en eksisterende tilgængelig Angiv, Sørg for at oprette en NIC for VM, og Tilføj NIC til puljen back end-adressen på justering af belastning. Ellers omdirigere ikke belastning netværkstrafik til pågældende VM. 

- **Placer hvert program niveau i en separat tilgængelighed angive.** I et N-delt program ikke sætte FOS fra forskellige niveauer i det samme sæt tilgængelighed. FOS i et sæt tilgængelighed er placeret på tværs af et domæner (FDs) og opdatere domæner (UD). For at få redundans fordelen FDs og UDs, skal hver VM i sættet tilgængelighed dog ikke kunne håndtere samme klienten anmoder om. 

- **Vælg den rigtige VM størrelse, der er baseret på krav til ydeevne.** Når du flytter en eksisterende arbejdsbyrde til Azure, kan du starte med den VM størrelse, der bedst svarer til dine lokale servere. Måle ydeevnen for den faktiske arbejdsbyrde i forhold CPU, hukommelse og diskplads IOP'ER og derefter tilpasse størrelsen, hvis det er nødvendigt. Dette hjælper med at sikre, at programmet fungerer som forventet i et skybaseret miljø. Også, hvis du har brug for flere netværkskort, være opmærksom på NIC grænsen for hver størrelse. 

- **Brug premium lagerplads virtuelle.** Azure Premium lagerplads understøtter høj ydeevne og lav ventetid disk. Kan finde flere oplysninger under [Premium lagerplads: High-Performance lagerplads til Azure virtuelt arbejdsbelastninger](../storage/storage-premium-storage.md) Vælg en VM størrelse, der understøtter premium lagerplads. 

- **Oprette en separat lagerplads konto til hver VM.** Markere de virtuelle harddiske for én VM til en separat lagerplads-konto. Dette hjælper med at undgå at nå IOP'ER grænser for lagerplads konti. Se [Azure lagerplads skalerbarhed og ydeevne destinationer](../storage/storage-scalability-targets.md)kan finde flere oplysninger. Men hvis du anvender et stort antal FOS, være opmærksom på per abonnement grænsen for lagerplads konti. Se [lagergrænser](../azure-subscription-service-limits.md#storage-limits).

- **Opret en separat lagerplads konto til diagnosticeringslogfiler**. Diagnosticeringslogfiler ikke skrive til den samme lagerplads konto som til at undgå logføring af diagnostik påvirker IOP'ER om en VM disk virtuelle harddiske. En standardkonto lokalt overflødige lagerplads (LRS) er tilstrækkelige til diagnosticeringslogfiler. 

- **Installere programmer på en datadisk, ikke OS disken.** Ellers kan du når størrelsesgrænsen disk. 

- **Bruge Azure sikkerhedskopi til at sikkerhedskopiere FOS.** Sikkerhedskopier beskyttelse mod utilsigtet tab af data. Du kan finde yderligere oplysninger finder [Beskytte Azure FOS med en gendannelse services samling af legitimationsoplysninger](../backup/backup-azure-vms-first-look-arm.md).

- **Aktivere diagnosticeringslogfiler**, herunder grundlæggende tilstand målepunkter, infrastruktur logfiler og [Start diagnosticering][boot-diagnostics]. Start diagnosticering kan hjælpe dig med at diagnosticere en startfejl, hvis din VM bliver i en ikke kan startes fra. Du kan finde flere oplysninger, se [Oversigt over Azure diagnosticeringslogfiler][diagnostics-logs].

- **Brug filtypenavnet AzureLogCollector**. (Windows FOS kun.) Dette lokalnummer aggregerer Azure-platformen logfiler og overfører dem til Azure-lager, uden operatoren fra en fjernplacering Sådan logger du ind på VM. Du kan finde yderligere oplysninger finder [AzureLogCollector filtypenavn](../virtual-machines/virtual-machines-windows-log-collector-extension.md).


###  <a name="virtual-network"></a>Virtuelt netværk 

- **Whitelist eller blok tilføje offentlige IP-adresser, en NSG til undernettet.** Bloker adgang fra hackere, eller Tillad adgang kun fra brugere, som har rettigheder til at få adgang til programmet.  

- **Oprette en brugerdefineret sundhed efterprøvning af af.** Indlæse belastningsjusteringstjenesten sundhed sonder kan du teste HTTP eller TCP. Hvis en VM kører en HTTP-server, er HTTP efterprøvning af af en bedre indikator for systemtilstand status end en TCP efterprøvning af af. En HTTP efterprøvning af af, at bruge et brugerdefineret slutpunkt, som oplyser den overordnede status for det program, herunder alle kritiske afhængigheder. Få mere at vide under [Oversigt over Azure justering af belastning](../load-balancer/load-balancer-overview.md).

- **Ikke blokere sundhed efterprøvning af af.** Indlæse belastningsjusteringstjenesten sundhed efterprøvning af af sendes fra en kendte IP-adresse, 168.63.129.16. Ikke blokere trafik til eller fra denne IP-adresse i en hvilken som helst firewall politikker eller netværk sikkerhed gruppe (NSG) regler. Blokering af sundhed efterprøvning af af medfører, at justering af belastning fjerne VM fra rotation. 

- **Aktivere logføring af justering af belastning.** Loggene viser hvor mange FOS på back end-ikke modtager netværkstrafik på grund af mislykkede efterprøvning af af svar. Du kan finde yderligere oplysninger finder [Log analyser af Azure justering af belastning](../load-balancer/load-balancer-monitor-log.md).


<!-- links -->
[app-service-autoscale]: ../monitoring-and-diagnostics/insights-how-to-scale.md
[asynchronous-c-sharp]:https://msdn.microsoft.com/library/mt674882.aspx
[availability-sets]:../virtual-machines/virtual-machines-windows-manage-availability.md
[azure-backup]: https://azure.microsoft.com/documentation/services/backup/
[boot-diagnostics]: https://azure.microsoft.com/blog/boot-diagnostics-for-virtual-machines-v2/
[circuit-breaker]: https://msdn.microsoft.com/library/dn589784.aspx
[cloud-service-autoscale]: ../cloud-services/cloud-services-how-to-scale.md
[diagnostics-logs]: ../monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs.md
[fma]: guidance-resiliency-failure-mode-analysis.md
[guidance-resilient-deployment]: guidance-resiliency-overview.md#resilient-deployment
[load-balancer]: load-balancer/load-balancer-overview.md
[monitoring-and-diagnostics-guidance]: ../best-practices-monitoring.md
[resource-manager]: ../azure-resource-manager/resource-group-overview.md
[retry-pattern]: https://msdn.microsoft.com/library/dn589788.aspx
[retry-service-guidance]: ../best-practices-retry-service-specific.md
[search-optimization]: ../search/search-performance-optimization.md
[sql-backup]: ../sql-database/sql-database-automated-backups.md
[sql-restore]: ../sql-database/sql-database-recovery-using-backups.md
[traffic-manager]: ../traffic-manager/traffic-manager-overview.md
[traffic-manager-routing]: ../traffic-manager/traffic-manager-routing-methods.md
[vmss-autoscale]: ../virtual-machine-scale-sets/virtual-machine-scale-sets-autoscale-overview.md
