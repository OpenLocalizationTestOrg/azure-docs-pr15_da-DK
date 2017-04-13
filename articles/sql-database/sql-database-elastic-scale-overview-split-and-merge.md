<properties 
    pageTitle="At flytte data mellem skaleret skyen databaser | Microsoft Azure" 
    description="Beskriver, hvordan du manipulere shards og flytte data via en selvstændig hostet tjeneste elastiske webdatabasen API'er." 
    services="sql-database" 
    documentationCenter="" 
    manager="jhubbard" 
    authors="ddove"/>

<tags 
    ms.service="sql-database" 
    ms.workload="sql-database" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="05/27/2016" 
    ms.author="ddove" />

# <a name="moving-data-between-scaled-out-cloud-databases"></a>At flytte data mellem skaleret skyen databaser

Hvis du får en Software som en tjeneste udvikler, og pludselig din app gennemgår enormt behov, skal du tage højde for væksten. Så kan du tilføje flere databaser (shards). Sådan du omfordele dataene til de nye databaser uden at forstyrre dataintegriteten? Brug **opdelt Flet værktøj** til at flytte data fra begrænsede databaser til de nye databaser.  

Værktøjet delt Flet kører som en Azure-webtjeneste. En administrator eller udvikler du bruger værktøjet til at flytte shardlets (data fra en shard) mellem forskellige databaser (shards). Værktøjet anvender shard kort administration til at vedligeholde service metadata-database, og sikre konsistent tilknytninger.

![Oversigt][1]

## <a name="download"></a>Download
[Microsoft.Azure.SqlDatabase.ElasticScale.Service.SplitMerge](http://www.nuget.org/packages/Microsoft.Azure.SqlDatabase.ElasticScale.Service.SplitMerge/)


## <a name="documentation"></a>Dokumentation
1. [Elastiske database opdelt Flet værktøjet selvstudium](sql-database-elastic-scale-configure-deploy-split-and-merge.md)
* [Konfiguration af delt Flet sikkerhed](sql-database-elastic-scale-split-merge-security-configuration.md)
* [Opdel Flet sikkerhedsovervejelser](sql-database-elastic-scale-split-merge-security-configuration.md)
* [Shard kort administration](sql-database-elastic-scale-shard-map-management.md)
* [Overføre eksisterende databaser til skala ud](sql-database-elastic-convert-to-use-elastic-tools.md)
* [Elastiske Databaseværktøjer](sql-database-elastic-scale-introduction.md)
* [Elastiske Database værktøjer ordliste](sql-database-elastic-scale-glossary.md)

## <a name="why-use-the-split-merge-tool"></a>Hvorfor bruge værktøjet delt Flet?

**Fleksibilitet**

Programmer skal strække fleksibelt grænserne i en enkelt Azure SQL DB-database. Bruge værktøjet til at flytte data efter behov for at nye databaser samtidig bevare integritet.

**Opdel kan vokse** 

Du har brug at øge overordnede kapacitet til at håndtere eksplosiv vækst. Gør du ved at oprette ekstra kapacitet ved sharding dataene og distribuere på tværs af trinvist flere databaser, indtil kapacitetsbehov er opfyldt. Dette er en primære eksempel på funktionen 'dele'. 

**Flette for at formindske**

Kapacitet skal Formindsk på grund af sæsonbestemte karakter for en forretning. Værktøjet gør det muligt at indskrænke til færre skalaenheder, når business sænker. Funktionen 'Flet' i tjenesten elastiske skala opdelt Flet dækker dette krav. 

**Administrere hotspots ved at flytte shardlets**

Med flere lejere per database kan tildeling af shardlets til shards medføre kapacitet flaskehalse på nogle shards. Dette kræver igen tildeling shardlets eller flytte optaget shardlets til nye eller mindre udnyttet shards. 

## <a name="concepts--key-features"></a>Begreber og de vigtigste funktioner

**Kunde-hostet tjenester**

Opdel-fletningen er leveret som en kunde-hostet tjeneste. Du skal installere og være vært for tjenesten i dit Microsoft Azure-abonnement. Du kan du hente fra NuGet pakken indeholder en skabelon til konfiguration af til at fuldføre oplysninger til din specifikke installation. Se [delt Flet selvstudium](sql-database-elastic-scale-configure-deploy-split-and-merge.md) få mere at vide. Da tjenesten kører i abonnementet Azure, kan du styre og konfigurere de fleste sikkerhedsaspekter af tjenesten. Standardskabelonen omfatter indstillinger for at konfigurere SSL, certifikat-baseret klientgodkendelse, kryptering for gemte legitimationsoplysninger, DoS beskytte og IP-begrænsninger. Du kan finde flere oplysninger om sikkerhedsaspekter i følgende dokument [delt Flet sikkerhedskonfiguration](sql-database-elastic-scale-split-merge-security-configuration.md).

Standard installeres tjenesten kører med én arbejder og én web rolle. Hver bruger A1 VM størrelsen i Azure Cloud Services. Du kan ikke redigere disse indstillinger, når du installerer pakken, kan du ændre dem efter en vellykket installation i igangværende skytjenesten (via portalen Azure). Bemærk, at rollen arbejder ikke må konfigureres for mere end en enkelt forekomst af tekniske årsager. 

**Integration af shard kort**

Tjenesten opdelt Flet skal arbejde sammen med shard kortet af programmet. Når du bruger tjenesten opdelt Flet at opdele eller flette områder eller til at flytte shardlets mellem shards, beholder tjenesten shard kortet opdateret. For at gøre det, tjenesten opretter forbindelse til shard kort manager-databasen af programmet og vedligeholder områder og tilknytninger som anmodninger om Flet/Opdel/flytning status. Dette sikrer, at shard kortet altid præsenterer en opdateret visning, når opdelt Flet handlinger foregår. Opdele, er Flet og shardlet bevægelse handlinger implementeret ved at flytte en mængde shardlets fra kilde shard til target shard. Under shardlet flytningen af shardlets underlagt den aktuelle batch er markeret som offline på kortet shard og er ikke tilgængelige for data-afhængige routing forbindelser ved hjælp af **OpenConnectionForKey** API. 

**Ensartet shardlet forbindelser**

Når flytning af data starter en ny mængde shardlets, en hvilken som helst shard-kort leveres data-afhængige routing forbindelser til shard lagring af shardlet er aflives og efterfølgende forbindelser fra shard kortet API'er til disse shardlets blokeres, mens flytningen af data er i gang for at undgå uoverensstemmelser. Forbindelser til andre shardlets på den samme shard kan også få afbrudt, men lykkes igen med det samme på Prøv igen. Når batchen er flyttet, shardlets markeres online igen til target shard og kildedataene fjernes fra kilde shard. Tjenesten gennemgår disse trin for hver gruppe, indtil alle shardlets er blevet flyttet. Dette vil føre til flere forbindelse kill operationer i løbet af handlingen Flet/Opdel/Flyt fuldført.  

**Administrere shardlet tilgængelighed**

Begrænse forbindelsen aflivning til det aktuelle sæt shardlets som beskrevet ovenfor begrænser omfanget af utilgængelighed til et sæt af shardlets ad gangen. Dette er den foretrukne over en metode hvor fuldført shard skal forblive offline for alle dens shardlets i løbet af en opdelt eller Flet-handling. Størrelsen på en gruppe, der er defineret som antallet af entydige shardlets til at flytte ad gangen, er en konfigurationsparameter. Det kan være defineret for hver delt og flette operation afhængigt af programmets tilgængelighed og ydeevne behov. Bemærk, at det område, der er låst i shard tilknytningen kan være større end den angivne batchstørrelse. Dette skyldes, at tjenesten henter område størrelsen, så det faktiske antal sharding nøgleværdier i dataene cirka svarer til batchstørrelsen. Dette er vigtigt at huske især for tyndt på forhånd sharding nøgler. 

**Metadata lagerplads**

Tjenesten opdelt Flet bruger en database til at bevare dens status og bevare logfiler under behandling af mødeindkaldelser. Brugeren opretter denne database i deres abonnement og giver forbindelsesstrengen for den i konfigurationsfilen for den tjeneste installation. Administratorer fra brugerens organisation kan også oprette forbindelse til databasen til at gennemgå anmodning om fremskridt og for at undersøge detaljerede oplysninger om eventuelle fejl.

**Sharding-tilstedeværelse**

Tjenesten opdelt Flet skelner mellem (1) delt tabeller, (2) reference tabeller og (3) normal tabeller. Semantik af handlingen Flet/Opdel/Flyt afhænger af typen af tabellen, som bruges og er defineret som følger: 

* **Delt tabeller**: opdelt, Flet og flytning flytte shardlets fra kilde til destination shard. Efter vellykket gennemførelse af overordnede anmodningen,-disse shardlets er ikke længere findes på kilden. Bemærk, at måltabellerne skal findes på destinationen shard og må ikke indeholde data i målområdet før behandling af handlingen. 

* **Referencetabeller**: referencetabeller, opdelingen, flette, og flytte handlinger kopiere data fra kilden til destinationen shard. Bemærk, optræder ingen ændringer på target shard til en bestemt tabel, hvis en hvilken som helst række findes allerede i denne tabel på destinationen. Tabellen skal være tom for en hvilken som helst reference tabel kopieringen til at få behandlet.

* **Andre tabeller**: andre tabeller, der kan være til stede på kilden eller destinationen for en delt og flette handling. Tjenesten opdelt Flet tilsidesætter disse tabeller efter flytning af data eller Kopiér handlinger. Bemærk, at de kan forstyrre disse handlinger i tilfælde af begrænsninger.

Oplysningerne på reference kontra delt tabeller har fået oplyst af **SchemaInfo** API'er på kortet shard. I følgende eksempel illustrerer brugen af disse API'er på en given shard kort manager objekt smm: 

    // Create the schema annotations 
    SchemaInfo schemaInfo = new SchemaInfo(); 

    // Reference tables 
    schemaInfo.Add(new ReferenceTableInfo("dbo", "region")); 
    schemaInfo.Add(new ReferenceTableInfo("dbo", "nation")); 

    // Sharded tables 
    schemaInfo.Add(new ShardedTableInfo("dbo", "customer", "C_CUSTKEY")); 
    schemaInfo.Add(new ShardedTableInfo("dbo", "orders", "O_CUSTKEY")); 

    // Publish 
    smm.GetSchemaInfoCollection().Add(Configuration.ShardMapName, schemaInfo); 

Tabeller 'region' og 'hele landet' er defineret som referencetabeller og vil blive kopieret med Flet/Opdel/Flyt handlinger. 'kunde' og 'ordrer' er tur. defineret som delt tabeller. C_CUSTKEY og O_CUSTKEY fungere som tasten sharding. 

**Referentiel integritet**

Tjenesten opdelt Flet analyserer afhængigheder mellem tabeller og anvender fremmed nøgle-primær nøgle-relationer til fase handlinger til at flytte referencetabeller og shardlets. Generelt kopieres referencetabeller først i afhængighed rækkefølge, og derefter shardlets kopieres i rækkefølgen af deres afhængigheder i hvert batch. Det er nødvendigt, så der er accepteret fremmed nøgle-primær nøgle begrænsninger i target shard, som modtages af de nye data. 

**Shard kort konsistens og eventuel afslutning**

Tjenesten opdelt Flet genoptages handlinger efter en hvilken som helst afbrydelse tilstedeværelse af fejl og har til formål at fuldføre en i anmodninger om status. Der kan dog uoprettelig situationer, f.eks., når target shard mistede eller kompromitteret repareres. Under disse omstændigheder fortsat nogle shardlets, der er meningen skal flyttes er placeret på kilde shard. Tjenesten sikrer, at shardlet tilknytninger opdateres kun, når de nødvendige data blev kopieret til destinationen. Shardlets slettes kun på kilden, når alle deres data er blevet kopieret til destinationen og de tilsvarende tilknytninger er blevet opdateret. Sletningen sker i baggrunden, mens området allerede er online på target shard. Tjenesten opdelt Flet sikrer altid er korrekte i tilknytningen, der er gemt i shard kortet.


## <a name="the-split-merge-user-interface"></a>Opdel Flet brugergrænsefladen

Opdel Flet servicepakke omfatter en kollega rolle og en web rolle. Rollen web bruges til at sende anmodninger om opdelt-fletning i en interaktiv måde. Hovedelementerne i brugergrænsefladen er som følger:

-    Handlingstype: Handlingstype er en alternativknap, der styrer slags udføres af tjenesten for denne anmodning. Du kan vælge mellem opdelingen, flette og flytte scenarier. Du kan også annullere en handling af sendt tidligere. Du kan bruge opdelt, flette og flytte anmodninger om område shard kort. Listen shard knytter kun support flytning.

-    Shard kort: Den næste sektion af anmodning om parametre dækker oplysninger om shard kortet og den database, der er vært for shard kortet. Især, skal du angive navnet på den Azure SQL-databaseserver og database, der er vært shardmap, legitimationsoplysninger for at oprette forbindelse til databasen til shard, og til sidst på navnet på shard kortet. Handlingen accepterer i øjeblikket kun en enkelt række legitimationsoplysninger. Disse legitimationsoplysninger skal have tilladelse til at udføre ændringer til shard kortet samt som brugerdataene på shards.

-    Kildeområde (opdele og flette): en handling af delt og flette behandler et område ved hjælp af den nedre og øvre nøgle. Hvis du vil angive en handling med en ubundet høj nøgleværdi, Markér afkrydsningsfeltet "høj nøgle er Maks" og høj vigtige feltet er tomt. Område nøgleværdier, du angiver behøver ikke at nøjagtigt match en tilknytning og dens grænser i shard kortet. Hvis du ikke angiver et områdegrænser overhovedet tjenesten, at det nærmeste område for dig automatisk. Du kan bruge GetMappings.ps1 PowerShell-script til at hente de aktuelle tilknytninger i et bestemt shard kort.

-    Delt datakilde funktionsmåde (delt): definere sted for at opdele kildeområdet For delt handlinger. Du kan gøre dette ved at indsende sharding nøgle, hvor du vil opdelingen skal forekomme. Brug alternativknappen angive, om du vil den nederste del af området (undtagen tasten opdelt) til at flytte, eller om du vil have den øverste del til at flytte (herunder tasten opdelt).

-    Datakilde Shardlet (Flyt): flytte handlinger er forskellige fra opdele eller sammenflette handlinger, som de ikke kræver et område til at beskrive kilden. En kilde til Flyt er blot identificeret med den sharding nøgleværdi, du vil flytte.

-    Målrette Shard (delt): Når du har angivet oplysningerne på din opdelingen kilde, skal du angive, hvor dataene skal kopieres til ved at indsende Azure SQL Db server og database navn til destinationen.

-    Target område (Flet): Flet handlinger flytte shardlets til en eksisterende shard. Du kan identificere den eksisterende shard ved at indsende område grænserne for det eksisterende område, du vil flette med.

-    Batchstørrelse: Batchstørrelsen styrer antallet af shardlets, der går offline på et tidspunkt under flytning af data. Dette er et heltal, hvor du kan bruge mindre værdier, når du er følsomme lange perioder nedetid for shardlets. Større værdier, øger det tidspunkt, hvor en given shardlet er offline, men kan forbedre ydeevnen.

-    Handlings-Id (annullere): Hvis du har en igangværende handling, der ikke længere er nødvendig, kan du annullere handlingen ved at indsende dens handlings-ID i dette felt. Du kan hente handlings-ID'ET fra tabellen anmodning om status (se afsnittet 8.1) eller fra output i webbrowseren, hvor du har sendt anmodningen.


## <a name="requirements-and-limitations"></a>Krav og begrænsninger 

Den aktuelle implementering af tjenesten opdelt Flet er underlagt følgende krav og begrænsninger: 

* Shards skal findes og registreres i shard kortet, før en opdelt flettehandling på disse shards kan udføres. 

* Tjenesten opretter ikke tabeller eller andre databaseobjekter automatisk som en del af dens handlinger. Det betyder, at skemaet for alle delt tabeller og referencetabeller skal findes på destinationen shard før eventuelle Flet/Opdel/Flyt operationer. Delt tabeller skal især være tomt i det område, hvor nye shardlets er tilføjes af handlingen Flet/Opdel/Flyt. Ellers mislykkes handlingen konsistenskontrollen indledende på target shard. Bemærk også, referencen, kopieres kun dataene, hvis referencen tabel er tom og, der er ingen konsistens garantier med hensyn til andre samtidige skrive handlinger på referencetabellerne. Vi anbefaler, at dette: Når du kører opdelt/Flet handlinger, ingen andre skrivning foretage ændringer i referencetabellerne.

* Tjenesten er baseret på række identitet etableret ved et entydigt indeks eller nøgle, der indeholder sharding nøglen for at forbedre ydeevnen og pålideligheden for store shardlets. Dette giver mulighed for tjenesten til at flytte data i en lige giver flere detaljeoplysninger end bare den sharding nøgleværdi. Dette hjælper med at reducere den maksimale mængde log mellemrum og låse på websteder, der kræves under handlingen. Overvej at oprette et entydigt indeks eller en primær nøgle, herunder tasten sharding på en bestemt tabel, hvis du vil bruge denne tabel med anmodninger om Flet/Opdel/flytning. Tasten sharding skal være kolonnen foranstillet i tasten eller indekset for at forbedre ydeevnen.

* I løbet af behandling af mødeindkaldelser, kan nogle shardlet data være til stede, både på kilde- og target shard. Det er nødvendigt at beskytte mod fejl under shardlet bevægelse. Integration af delt brevfletning med shard kortet sikrer, at forbindelser gennem de data, der er afhængige routing API'er ved hjælp af metoden **OpenConnectionForKey** på kortet shard ikke kan se en hvilken som helst inkonsekvent tilstande. Dog ved oprettelse af forbindelse til kilden eller destinationen shards uden at bruge metoden **OpenConnectionForKey** , være inkonsekvent tilstande synlige når Flet/Opdel/Flyt anmodninger foregår. Disse forbindelser viser muligvis delvis eller dublerede resultater, afhængigt af tidsindstilling eller shard underliggende forbindelsen. Denne begrænsning omfatter i øjeblikket forbindelser, der er foretaget af elastiske skala MFA-Shard-forespørgsler.

* Metadatadatabasen til tjenesten opdelt Flet må ikke deles mellem forskellige roller. For eksempel skal en rolle til opdelt Flet tjenesten kører i midlertidige pege på en anden metadata database end rollen fremstilling.
 

## <a name="billing"></a>Fakturering 

Opdel Flet tjenesten kører som en skybaseret tjeneste i dit Microsoft Azure-abonnement. Derfor gælder gebyrer for skytjenester for din forekomst af tjenesten. Medmindre du ofte udfører Flet/Opdel/Flyt handlinger, anbefaler vi du slette din delte Flet skybaseret tjeneste. Der gemmer omkostninger for kører eller installeret skyen forekomster af tjenesten. Du kan installere igen og start konfigurationen af let runnable, når du har brug at udføre opdele eller sammenflette handlinger. 
 
## <a name="monitoring"></a>Overvågning 
### <a name="status-tables"></a>Status for tabeller 

Tjenesten opdelt Flet giver tabellen **RequestStatus** i metadataene gemme databasen til overvågning af færdige og den løbende anmodninger. Tabellen viser en række for hver delt Flet anmodning, der er blevet sendt til denne forekomst af tjenesten opdelt fletningen. Følgende oplysninger for hver enkelt anmodning får:

* **Tidsstempel**: dato og klokkeslæt, hvor anmodningen blev startet.

* **OperationId**: et GUID, der entydigt identificerer anmodningen. Denne anmodning kan også bruges til at annullere handlingen, mens det er stadig igangværende.

* **Status**: den aktuelle tilstand for din anmodning. Igangværende anmodninger om viser det også den aktuelle fase, hvor anmodningen er.

* **CancelRequest**: et flag, som angiver, om anmodningen blev annulleret.

* **Status**: en procentdel vurdering færdiggørelsesprocent for handlingen. En værdi på 50 angiver, at handlingen er cirka 50% færdig.

* **Detaljer**: en XML-værdi, der indeholder en mere detaljeret statusrapport. Statusrapporten opdateres med jævne mellemrum, efterhånden som sæt af rækker er kopieret fra kilde til destination. I tilfælde af fejl eller undtagelser indeholder denne kolonne også mere detaljerede oplysninger om fejlen.


### <a name="azure-diagnostics"></a>Azure diagnosticering

Tjenesten opdelt Flet bruger Azure diagnosticering baseret på Azure SDK 2,5 til overvågning og diagnosticering. Du kan styre diagnosticering konfiguration, som beskrevet her: [Aktivere diagnosticering i Azure Cloud Services og virtuelle computere](../cloud-services/cloud-services-dotnet-diagnostics.md). Download pakken omfatter to diagnosticering konfigurationer – én for rollen web og én til rollen arbejder. Disse diagnosticering konfigurationer for tjenesten følge vejledningen, fra [Skyen Service grundlæggende i Microsoft Azure](https://code.msdn.microsoft.com/windowsazure/Cloud-Service-Fundamentals-4ca72649). Den indeholder definitioner for at logge tællere i ydeevne, IIS logfiler, Windows-hændelseslogge og opdelte Flet programmets hændelseslogfiler. 

## <a name="deploy-diagnostics"></a>Installere diagnosticering 

For at aktivere overvågning og diagnosticering ved hjælp af konfigurationen af diagnosticering til på internettet og arbejder roller, der leveres af pakken NuGet skal du køre følgende kommandoer ved hjælp af Azure PowerShell: 

    $storage_name = "<YourAzureStorageAccount>" 
    
    $key = "<YourAzureStorageAccountKey" 
    
    $storageContext = New-AzureStorageContext -StorageAccountName $storage_name -StorageAccountKey $key  
    
    
    $config_path = "<YourFilePath>\SplitMergeWebContent.diagnostics.xml" 
    
    $service_name = "<YourCloudServiceName>" 
    
    Set-AzureServiceDiagnosticsExtension -StorageContext $storageContext -DiagnosticsConfigurationPath $config_path -ServiceName $service_name -Slot Production -Role "SplitMergeWeb" 
    
    
    $config_path = "<YourFilePath>\SplitMergeWorkerContent.diagnostics.xml" 
    
    $service_name = "<YourCloudServiceName>" 
    
    Set-AzureServiceDiagnosticsExtension -StorageContext $storageContext -DiagnosticsConfigurationPath $config_path -ServiceName $service_name -Slot Production -Role "SplitMergeWorker" 

Du kan finde flere oplysninger om, hvordan du konfigurerer og implementere diagnosticering indstillinger her: [Aktivere diagnosticering i Azure Cloud Services og virtuelle computere](../cloud-services/cloud-services-dotnet-diagnostics.md). 

## <a name="retrieve-diagnostics"></a>Hente diagnosticering 

Du kan nemt få adgang til din diagnosticering fra Visual Studio Server Stifinder i den Azure del af træet Server Explorer. Åbn en forekomst af Visual Studio, og klik på Vis og Server Explorer i menulinjen. Klik på ikonet Azure til at oprette forbindelse til dit Azure-abonnement. Gå derefter til Azure -> lagerplads -> <your storage account> -> tabeller -> WADLogsTable. Du kan finde yderligere oplysninger finder [Browsing lagerplads ressourcer med Server Explorer](http://msdn.microsoft.com/library/azure/ff683677.aspx). 

![WADLogsTable][2]

WADLogsTable fremhævet i skærmbilledet ovenfor indeholder de detaljerede hændelser fra tjenesten opdelt Flet programmets logfil. Bemærk, at standardkonfigurationen af den hentede pakke er henvender sig til et produktionsmiljø. Det interval, hvormed logfiler og tællere er trukket fra forekomsterne af tjenesten er derfor store (5 minutter). Sænke intervallet for test og udvikling, ved at justere diagnosticeringsindstillingerne for på internettet eller rollen arbejder til dine behov. Højreklik på rollen i Visual Studio Server Explorer (se ovenfor) og derefter tilpasse den overføre periode i dialogboksen for konfigurationsindstillinger diagnosticering: 

![Konfiguration][3]


## <a name="performance"></a>Ydeevne

Generelt er bedre ydeevne forventes fra jo højere flere performant service lag i Azure SQL-Database. Højere EY, CPU og hukommelse tildelinger for de højere service lag få glæde flere kopier og Slet handlinger, der bruger tjenesten opdelt fletningen. Derfor, at øge webtjenesten lige for disse databaser for en defineret, begrænset periode.

Tjenesten udfører også validering forespørgsler som en del af de normale handlinger. Disse validering forespørgsler søger efter uventet tilstedeværelsen af data i målområdet og sikre, at en hvilken som helst handlingen Flet/Opdel/Flyt starter fra en konsistent tilstand. Disse alle forespørgsler fungerer over sharding vigtige områder, der er defineret af omfanget af handlingen og batchstørrelsen leveres som en del af anmodning om definitionen. Disse forespørgsler udføre bedst, når et indeks er Præsenter, der indeholder nøglen sharding som kolonnen foranstillet. 

Desuden kan en entydig egenskab med tasten sharding som kolonnen foranstillet tjeneste til at bruge en optimerede metode, der begrænser Ressourceforbrug med hensyn til logplads og hukommelse. Denne egenskab at entydige kræves for at flytte store størrelser (typisk over 1GB). 

## <a name="how-to-upgrade"></a>Sådan opgraderer du

1. Følg trinnene i [Implementer en opdelt Flet-tjeneste](sql-database-elastic-scale-configure-deploy-split-and-merge.md).
2. Ændre din skyen service konfigurationsfil til installation af delt brevfletning til at afspejle de nye konfiguration af parametre. En ny påkrævet parameter er oplysninger om certifikatet, der bruges til kryptering. En nem måde at gøre dette er at sammenligne nye konfiguration skabelonfilen fra overførslen mod din eksisterende konfiguration. Kontrollér, at du tilføjer indstillingerne for "DataEncryptionPrimaryCertificateThumbprint" og "DataEncryptionPrimary" for både internettet og rollen arbejder.
3. Før du anvender opdateringen til Azure, sikre, at alle i øjeblikket kører opdelt Flet handlinger er færdig. Du kan nemt gøre dette ved at forespørge tabellerne RequestStatus og PendingWorkflows i opdelte Flet metadatadatabasen igangværende anmodninger om.
4. Opdater din eksisterende skyen service-delt-fletning i abonnementet Azure med den nye pakke og din opdaterede service konfigurationsfil.

Du behøver ikke at klargøre en ny database metadata for delt brevfletning til at opgradere. Den nye version opgraderer automatisk din eksisterende metadata-database til den nye version. 

## <a name="best-practices--troubleshooting"></a>Bedste fremgangsmåder og fejlfinding
 
-    Definere en test lejer og øvelse dine vigtigste Flet/Opdel/Flyt handlinger med test-lejer på tværs af flere shards. Sørg for, at alle metadata er defineret korrekt i shard kortet og, handlingerne ikke overskride begrænsninger eller fremmede nøgler.
-    Holde test lejeren datastørrelse over den maksimale datastørrelse i din største lejer at sikre, at du ikke oplever datastørrelse relaterede problemer. Dette hjælper dig med at vurdere en øvre grænse på den tid det tager for at flytte rundt med en enkelt lejer. 
-    Sørg for, at dit skema tillader sletninger. Tjenesten opdelt Flet kræver mulighed for at fjerne data fra datakilden shard, når dataene er blevet kopieret til destinationen. For eksempel **slette udløsere** kan forhindre, at tjenesten slette dataene på kilden og kan forårsage handlinger mislykkes.
-    Tasten sharding bør være kolonnen foranstillet i din primær nøgle eller et entydigt indeksdefinition. Der sikrer den bedste ydeevne for de opdelte eller Flet validering forespørgsler og for de faktiske data bevægelse og sletning handlinger som altid fungerer på sharding vigtige områder.
-    Placere din delt-Flet tjeneste i område og data center, hvor dine databaser er placeret. 

[AZURE.INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]



<!--Anchors-->
<!--Image references-->
[1]:./media/sql-database-elastic-scale-overview-split-and-merge/split-merge-overview.png
[2]:./media/sql-database-elastic-scale-overview-split-and-merge/diagnostics.png
[3]:./media/sql-database-elastic-scale-overview-split-and-merge/diagnostics-config.png
 
