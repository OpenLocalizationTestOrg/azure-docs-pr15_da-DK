<properties
   pageTitle="Fleksibilitet for gendannelse af tab af en teknisk vejledning Azure område | Microsoft Azure"
   description="Artikel på forstå og designe tolerant, meget tilgængelige fejl tolerant programmer samt planlægning af nedbrud"
   services=""
   documentationCenter="na"
   authors="adamglick"
   manager="saladki"
   editor=""/>

<tags
   ms.service="resiliency"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="08/18/2016"
   ms.author="aglick"/>

#<a name="azure-resiliency-technical-guidance-recovery-from-a-region-wide-service-disruption"></a>Azure fleksibilitet teknisk vejledning: gendannelse fra et område hele tjenesteforstyrrelse

Azure er opdelt fysisk og logisk i enheder, der kaldes områder. Et område består af en eller flere datacentre i nærheden af. Samtidig med denne skriver har Azure 24 områder over hele verden.

Det er muligt, funktioner og derefter på en hel område kan være utilgængelige, for eksempel på grund af mislykkede forsøg på netværk i sjældne tilfælde. Eller faciliteter kan gå tabt helt, for eksempel på grund af neutralt nedbrud. Dette afsnit beskrives funktionerne i Azure til oprettelse af programmer, der er fordelt over områder. Sådan fordeling hjælper med at minimere muligheden for, at en fejl i ét område, kan det påvirke andre områder.

##<a name="cloud-services"></a>Skytjenester

###<a name="resource-management"></a>Ressourcestyring

Du kan distribuere Beregn forekomster på tværs af områder ved at oprette en separat skybaseret tjeneste i hvert område for destinationen, og derefter publicere installationspakken til hver skybaseret tjeneste. Bemærk, at distribuere trafik på tværs af skytjenester i forskellige områder skal være implementeret af programmet udvikleren eller med en trafik management-tjenesten.

Fastlægge antallet af forekomster af ekstra rolle at installere på forhånd til nedbrud er et vigtigt aspekt planlægning af kapacitet. Har du en fuld størrelse sekundær installation sikrer, at kapacitet allerede er tilgængelige, når det er nødvendigt; Dette fordobles dog effektivt omkostningerne. Et almindeligt mønster er at have en lille, sekundær installation, lige stort nok til at køre kritiske tjenester. Denne lille sekundær installation er en god ide, begge reservere kapacitet, og test af konfigurationen af den sekundære miljø.

>[AZURE.NOTE]Kvoten for abonnement er det ingen garanti kapacitet. Kvoten er blot en kreditkontrol grænse. For at garantere kapacitet, der skal defineres det påkrævede antal roller i modellen, service, og rollerne, der skal installeres.

###<a name="load-balancing"></a>Justering af belastning

Hvis du vil indlæse balance kræver trafik på tværs af områder en trafik management-løsning. Azure leverer [Azure trafik Manager](https://azure.microsoft.com/services/traffic-manager/). Du kan også drage fordel af tredjepartstjenester, som giver samme trafik administrationsfunktioner.

###<a name="strategies"></a>Strategier

Mange alternative strategier er tilgængelige for implementering fordelt Beregn på tværs af områder. Disse skal tilpasses til de enkelte virksomhedskrav og omstændigheder af programmet. På et højt niveau kan på fremgangsmåder opdeles i følgende kategorier:

  * __Geninstaller på nedbrud__: med denne tilgang genaktiveres programmet fra bunden, samtidig med nedbrud. Dette er relevant for ikke-kritisk programmer, der ikke kræver gangen gendannelse.

  * __Varm ekstra (aktiv/passiv)__: en sekundær hostet tjeneste er oprettet i et andet område, og roller er installeret for at garantere minimale kapacitet. rollerne, der ikke kan dog modtage fremstilling trafik. Denne metode er nyttig til programmer, der ikke er udviklet til at distribuere trafik på tværs af områder.

  * __Hurtigtast ekstra (aktive)__: programmet er udviklet til at modtage fremstilling Indlæs i flere områder. Skytjenester i hvert område kan være konfigureret til højere kapacitet end påkrævet til nedbrud gendannelse formål. Du kan også skytjenesterne kan skalere ud efter behov, samtidig med en nedbrud og failover. Denne fremgangsmåde kræver væsentlig investering i programmet design, men det har betydelige fordele. Dette omfatter nedre og garanterede gendannelse klokkeslæt, kontinuerlig test af alle gendannelse placeringer, og effektivt brugen af kapacitet.

En fuldstændig beskrivelse af fordelt design er omfattet af dette dokument. Yderligere oplysninger finder du [nedbrud og høj tilgængelighed til Azure-programmer](https://aka.ms/drtechguide).

##<a name="virtual-machines"></a>Virtuelle maskiner

Gendannelse af infrastruktur som en tjeneste (IaaS) virtuelle maskiner (VM'er) ligner platform som en tjeneste (PaaS) beregne gendannelse på mange punkter. Der er vigtige forskelle, men skyldes, at en IaaS VM består af både VM og VM disken.

  * __Brug Azure sikkerhedskopiering for at oprette cross område sikkerhedskopier, der er ensartet programmet__.
  [Azure sikkerhedskopi](https://azure.microsoft.com/services/backup/) gør det muligt for kunder til at oprette programmet ensartet sikkerhedskopier på tværs af flere VM diske og understøtter replikering af sikkerhedskopier på tværs af områder. Du kan gøre dette ved at vælge til geografisk replikeres sikkerhedskopiering samling på tidspunktet for oprettelse af. Bemærk, at replikering af sikkerhedskopien samling skal konfigureres på tidspunktet for oprettelse af. Det kan ikke indstilles senere. Hvis et område går tabt, vil Microsoft gøre sikkerhedskopierne tilgængelige for kunder. Kunder vil kunne gendanne for deres konfigurerede gendannelsespunkter.

  * __Adskille data disken fra operativsystemet disken__. En vigtig overvejelse for IaaS FOS er, at du ikke kan ændre operativsystem disken uden at skulle oprette VM. Dette er ikke et problem, hvis din strategi for gendannelse er at geninstallere efter genoprettelse efter. Det kan dog være et problem, hvis du bruger varm ekstra tilgangen reservere kapacitet. For at implementere dette korrekt, skal du have den korrekte operativsystem disk, der er installeret på begge de primære og sekundære placeringer, og programmet dataene skal gemmes på et separat drev. Hvis det er muligt, bruge en standard operativsystem konfiguration, kan findes på begge placeringer. Efter en failover, skal du derefter vedhæfte drevet data til din eksisterende IaaS FOS i det sekundære DC. Brug AzCopy til at kopiere øjebliksbillede af disken(e) data til et eksternt websted.

  * __Vær opmærksom på potentielle problemer med konsistens efter en geografisk failover af flere VM diske__. VM diske er implementeret som Azure-lager BLOB og har den samme geografisk gentagelse egenskab. Medmindre [Azure sikkerhedskopi](https://azure.microsoft.com/services/backup/) bruges, er der ingen garantier af konsistens på tværs af disk, fordi geografisk replikering er asynkron og kopieres uafhængigt af hinanden. Individuelle VM diske er garanti er i et ensartet nedbrud stat efter en geografisk-failover, men ikke ensartede på tværs af diske. Dette kan medføre problemer i nogle tilfælde (for eksempel i forbindelse med disk striping).

##<a name="storage"></a>Lagerplads

###<a name="recovery-by-using-geo-redundant-storage-of-blob-table-queue-and-vm-disk-storage"></a>Gendannelse ved hjælp af geografisk overflødige lagringen af blob, tabel, kø og VM plads

I Azure, blob, tabeller, forespørgsler og VM er diske alle geografisk replikerede som standard. Dette kaldes at som geografisk overflødige lagerplads (GRS). GRS kopieres lagerplads data til en parvis datacenter hundredvis af langt fra hinanden i et bestemt geografisk område. GRS er udviklet til at angive yderligere holdbarhed, i tilfælde af, at der er overordnede datacenter nedbrud. Microsoft objekter, når der opstår failover og failover er begrænset til overordnede nedbrud, hvor den oprindelige primære placering anses for uoprettelig i et begrundet tidsrum. Det kan være flere dage i visse scenarier. Data replikeres typisk inden for et par minutter, selvom synkroniseringsinterval ikke er endnu omfattet af en serviceniveauaftale.

I tilfælde af en geografisk-failover, der kan ikke ændres til hvordan kontoen der opnås adgang (tasten URL-adresse og konto vil ikke ændre). Lagerplads kontoen, men er i et andet område efter failover. Dette påvirke programmer, der kræver internationale lighed med deres lagerplads-konto. Selv for tjenester og programmer, der ikke kræver en lagerplads konto i det samme datacenter kan i tværs datacenter ventetid og båndbredde gebyrer gode grunde til at flytte trafik til området failover midlertidigt. Dette kan angive i en overordnet nedbrud gendannelse strategi.

Ud over automatiske failover leveres af GRS, har Azure introduceret en tjeneste, som giver dig læseadgang til kopien af dine data på den sekundære lagerplacering. Dette kaldes læseadgang geografisk overflødige lagerplads (RA-GRS).

Du kan finde flere oplysninger om både GRS og RA-GRS lagerplads, [Azure-lager gentagelse](../storage/storage-redundancy.md).

###<a name="geo-replication-region-mappings"></a>Replikering geografisk område tilknytninger:

Det er vigtigt at vide, hvor dine data er geografisk replikerede, for at vide, hvor du vil installere de andre forekomster af dine data, der kræver internationale lighed med din lagerplads. Følgende tabel viser den primære og sekundære placering par:

[AZURE.INCLUDE [paired-region-list](../../includes/paired-region-list.md)]

###<a name="geo-replication-pricing"></a>Geografisk replikering priser:

Geografisk replikering er inkluderet i aktuelle priser for Azure-lager. Dette kaldes geografisk overflødige lagerplads (GRS). Hvis du ikke vil dataene geografisk replikerede kan du deaktivere geografisk replikering for din konto. Dette kaldes lokalt overflødige lagerplads, og den debiteres med rabat sammenlignet med GRS.

###<a name="determining-if-a-geo-failover-has-occurred"></a>Afgøre, hvis der opstod en geografisk-failover

Hvis der opstår en geografisk-failover, vil blive sendt til [Azure Dashboard over tjenestetilstand](https://azure.microsoft.com/status/). Programmer kan implementere et automatiseret middel til at registrere dette, men ved at overvåge geografisk området for deres lagerplads konto. Dette kan bruges til at udløse andre gendannelse handlinger, som aktivering af Beregn ressourcer i det geografisk-område, hvor sin lagerplads flyttes til. Du kan udføre en forespørgsel for denne fra Service API, ved hjælp af [Hent lagerplads konto egenskaber](https://msdn.microsoft.com/library/ee460802.aspx). De relevante egenskaber er:

    <GeoPrimaryRegion>primary-region</GeoPrimaryRegion>
    <StatusOfPrimary>[Available|Unavailable]</StatusOfPrimary>
    <LastGeoFailoverTime>DateTime</LastGeoFailoverTime>
    <GeoSecondaryRegion>secondary-region</GeoSecondaryRegion>
    <StatusOfSecondary>[Available|Unavailable]</StatusOfSecondary>

###<a name="vm-disks-and-geo-failover"></a>VM diske og geografisk belastningsjustering

Som beskrevet i afsnittet på VM diske, er der ingen garantier for data konsistens på tværs af VM diske efter en failover. For at sikre korrekt gennemførelse af sikkerhedskopier, skal et ekstra produkt som Data Protection Manager bruges til at sikkerhedskopiere og gendanne programmet data.

##<a name="database"></a>Database

###<a name="sql-database"></a>SQL-Database

Azure SQL-Database indeholder to typer af gendannelse: geografisk-Gendan og aktive geografisk-gentagelse.

####<a name="geo-restore"></a>Geografisk-Gendan

[Geografisk-Gendan](../sql-database/sql-database-recovery-using-backups.md#geo-restore) er også tilgængelig med Basic, som Standard og Premium databaser. Det giver mulighed for gendannelse standard, når databasen er ikke tilgængelig på grund af en hændelse i det område, hvor databasen er hostet. Svarer til punkt-In-Time gendanne, geografisk-Gendan er afhængig af databasesikkerhedskopier i geografisk overflødige Azure-lager. Den gendanner fra geografisk replikerede sikkerhedskopien, og derfor er tolerant for lagerplads udfald i det primære område. Få mere at vide, kan du se [gendanne en Azure SQL-Database eller failover til et sekundært](../sql-database/sql-database-disaster-recovery.md).

####<a name="active-geo-replication"></a>Aktive geografisk-gentagelse

[Aktive geografisk replikering](../sql-database/sql-database-geo-replication-overview.md) er tilgængelig for alle niveauer i databasen. Det er udviklet til programmer, der har flere tilstanden Gendannelse krav end geografisk-Gendan kan tilbyde. Bruger aktive geografisk-gentagelse, kan du oprette op til fire læsbare secondaries på servere i forskellige områder. Du kan starte failover til en af secondaries. Desuden kan aktive geografisk replikering bruges til at understøtte programmet opgradering eller flytning scenarier, samt til skrivebeskyttet tilstand arbejdsbelastninger justering af belastning. Få mere at vide, se [konfigurere geografisk gentagelse](../sql-database/sql-database-geo-replication-portal.md) og overføres [til den sekundære database](../sql-database/sql-database-geo-replication-failover-portal.md). Se [Design et program til skyen nedbrud ved hjælp af aktive geografisk-gentagelse i SQL-Database](../sql-database/sql-database-designing-cloud-solutions-for-disaster-recovery.md) og [administrere rullende opgraderinger af skyen programmer SQL aktive geografisk-databasereplikering](../sql-database/sql-database-manage-application-rolling-upgrade.md) få mere at vide om, hvordan du design og implementere programmer og programmer opgraderingen uden nedetid.

###<a name="sql-server-on-virtual-machines"></a>SQL Server på virtuelle maskiner

En række indstillinger er tilgængelige for gendannelse og høj tilgængelighed til SQL Server 2012 (eller nyere) kører i virtuelle Azure-computere. Få mere at vide under [høj tilgængelighed og nedbrud til SQL Server på virtuelle Azure-computere](../virtual-machines/virtual-machines-windows-sql-high-availability-dr.md).

##<a name="other-azure-platform-services"></a>Andre Azure-platformstjenester

Når du forsøger at køre skybaseret tjeneste i flere Azure områder, skal du overveje konsekvenserne for hver af dine afhængigheder. Tjenestens vejledning antages det, skal du bruge den samme Azure service i en alternativ Azure datacenter i de følgende afsnit. Dette omfatter både konfiguration og replikering af data opgaver.

>[AZURE.NOTE]I nogle tilfælde kan disse trin hjælpe med at reducere en tjenestens afbrydelse i stedet for en hel datacenter begivenhed. Fra perspektivet, programmet på computeren, kan være en tjenestens afbrydelse lige som at begrænse og vil kræve overførsel midlertidigt tjenesten til et andet Azure område.

###<a name="service-bus"></a>Service Bus

Azure Service Bus bruger et entydigt navneområde, der ikke omfatter Azure områder. Så er det første behov oprette den nødvendige service bus navneområder i det andet område. Der er dog også overvejelser i forbindelse med holdbarheden af i kø meddelelser. Der findes flere strategier til replikering meddelelser på tværs af Azure områder. Du kan finde oplysningerne om disse gentagelse strategier og andre nedbrud gendannelse strategier [bedste praksis for Isolations programmer mod Service Bus udfald og nedbrud](../service-bus-messaging/service-bus-outages-disasters.md). Du kan finde andre tilgængelighed overvejelser [Service Bus (tilgængelighed)](./resiliency-technical-guidance-recovery-local-failures.md#other-azure-platform-services).

###<a name="app-service"></a>App Service

Hvis du vil overføre et Azure App Service-program, som Web Apps eller Mobile-Apps til et sekundært Azure område, skal du have en sikkerhedskopi af det websted, der er tilgængelige for udgivelse. Hvis afbrydelse ikke omfatter det hele Azure datacenter, kan det være muligt at bruge FTP til at hente en nyere sikkerhedskopi af webstedets indhold. Derefter Opret en ny app i det andet område, medmindre du tidligere har gjort her for at reservere kapacitet. Publicere webstedet på det nye område, og foretag eventuelle nødvendige konfigurationsændringer. Disse ændringer kan omfatte database forbindelsesstrenge eller andre områdespecifik indstillinger. Hvis det er nødvendigt, tilføje webstedets SSL-certifikat og ændre DNS CNAME-posten, så det brugerdefinerede domænenavn peger geninstallerede Azure Web App URL-adressen.

###<a name="hdinsight"></a>HDInsight

De data, der er knyttet til HDInsight er gemt som standard i Azure Blob-lager. HDInsight kræver, at en Hadoop klynge behandling MapReduce job skal være placeret samtidig i samme område som kontoen lagerplads, der indeholder de data, der analysere. Hvis du bruger funktionen geografisk gentagelse tilgængelig til Azure-lager, kan du få adgang til dine data i det sekundære område, hvor dataene blev replikeres, hvis en eller anden grund er det primære område ikke længere tilgængelig. Du kan oprette en ny Hadoop-klynge i det område, hvor dataene er blevet replikeres og fortsætte med at behandle den. Du kan finde andre tilgængelighed overvejelser [HDInsight (tilgængelighed)](./resiliency-technical-guidance-recovery-local-failures.md#other-azure-platform-services).

###<a name="sql-reporting"></a>SQL-rapportering

Gendanne fra tab af et Azure område kræver flere forekomster af SQL Reporting i forskellige Azure områder på nuværende tidspunkt. Disse SQL Reporting forekomster skal bruge de samme data og dataene skal have sin egen gendannelse planlægge i tilfælde af nedbrud. Du kan også bevare eksterne sikkerhedskopier af RDL-filen til hver rapport.

###<a name="media-services"></a>Media Services

Azure Media Services har en anden gendannelse fremgangsmåde kodning og streaming. Streaming er typisk flere kritiske under en internationale afbrydelse. For at forberede dette, bør du have en Media Services-konto i to forskellige Azure områder. Kodet indholdet skal være placeret i begge områder. Under en fejl, kan du omdirigerer streaming-trafik til den alternative område. Kodning kan udføres i en hvilken som helst Azure område. Hvis kodning er klokkeslæt-følsomme, for eksempel under behandling af live hændelse, skal du være klar til at sende job til en alternativ datacenter under mislykkede forsøg.

###<a name="virtual-network"></a>Virtuelt netværk

Af konfigurationsfiler indeholder den hurtigste måde at konfigurere et virtuelt netværk i en alternativ Azure område. Når du har konfigureret det virtuelle netværk i det primære Azure region, [eksportere de virtuelle netværksindstillinger](../virtual-network/virtual-networks-create-vnet-classic-portal.md) for det aktuelle netværk til en netværk konfigurationsfil. I tilfælde af en afbrydelse i den primære region, [gendanne det virtuelle netværk](../virtual-network/virtual-networks-create-vnet-classic-portal.md) fra den lagrede konfigurationsfil. Derefter konfigurere andre skytjenester, virtuelle maskiner eller tværs lokale indstillinger til at arbejde med det nye virtuelle netværk.

##<a name="checklists-for-disaster-recovery"></a>Kontrollister til nedbrud

##<a name="cloud-services-checklist"></a>Cloud Services tjekliste

  1. Gennemgå sektionen Cloud Services i dette dokument.
  2. Oprette en strategi for genoprettelse nedbrud i tværs område.
  3. Forstå kompromiser i at reservere kapacitet i alternative områder.
  4. Bruge trafik routing værktøjer, som Azure trafik Manager.

##<a name="virtual-machines-checklist"></a>Virtuelle maskiner tjekliste

  1. Gennemgå sektionen virtuelle maskiner af dette dokument.
  2. Brug [Azure sikkerhedskopi](https://azure.microsoft.com/services/backup/) til at oprette programmet ensartet sikkerhedskopier på tværs af områder.

##<a name="storage-checklist"></a>Tjekliste for lager

  1. Gennemse afsnittet lager i dette dokument.
  2. Deaktiver ikke geografisk replikering lagerplads ressourcer.
  3. Forstå alternative område for geografisk gentagelse i tilfælde af failover.
  4. Oprette brugerdefinerede sikkerhedskopiering strategier for bruger kontrolleres failover strategier.

##<a name="sql-database-checklist"></a>Tjekliste for SQL-Database

  1. Gennemgå sektionen SQL-Database i dette dokument.
  2. Brug [Geografisk-Gendan](../sql-database/sql-database-recovery-using-backups.md#geo-restore) eller [Geografisk gentagelse](../sql-database/sql-database-geo-replication-overview.md) efter behov.

##<a name="sql-server-on-virtual-machines-checklist"></a>SQL Server på virtuelle maskiner tjekliste

  1. Gennemse den SQL Server på virtuelle maskiner afsnit i dette dokument.
  2. Brug tværs område AlwaysOn tilgængelighed grupper eller databasespejling.
  3. Skiftevis Brug Sikkerhedskopiér og Gendan for at blob storage.

##<a name="service-bus-checklist"></a>Service Bus tjekliste

  1. Gennemgå sektionen Service Bus af dette dokument.
  2. Konfigurere et Service Bus navneområde på et andet område.
  3. Overvej brugerdefinerede gentagelse strategier for meddelelser på tværs af områder.

##<a name="app-service-checklist"></a>App Service tjekliste

  1. Gennemse App section i dette dokument.
  2. Bevare Sikkerhedskopier websted uden for det primære område.
  3. Hvis afbrydelse er delvis, du forsøg at hente aktuelle websted med FTP.
  4. Planlæg at implementere webstedet til nye eller eksisterende websted på et andet område.
  5. Planlægge ændringer i konfigurationen til både programmet og DNS CNAME-poster.

##<a name="hdinsight-checklist"></a>HDInsight tjekliste

  1. Gennemse HDInsight del af dette dokument.
  2. Oprette en ny Hadoop-klynge i området med replikerede data.

##<a name="sql-reporting-checklist"></a>SQL-rapportering tjekliste

  1. Gennemgå sektionen SQL rapportering af dette dokument.
  2. Vedligeholde en alternativ SQL Reporting forekomst i et andet område.
  3. Vedligeholde en separat for at gentage destinationen til dette område.

##<a name="media-services-checklist"></a>Media Services tjekliste

  1. Gennemgå sektionen Media Services i dette dokument.
  2. Oprette en Media Services-konto på et andet område.
  3. Kodning af det samme indhold i begge områder til at understøtte streaming failover.
  4. Føje kodning job til et andet område i tilfælde af en tjenesteforstyrrelse.

##<a name="virtual-network-checklist"></a>Tjekliste til virtuelle netværk

  1. Gennemgå sektionen virtuelt netværk i dette dokument.
  2. Brug eksporteres virtuelle netværksindstillinger skal du oprette den igen i et andet område.

##<a name="next-steps"></a>Næste trin

I denne artikel er en del af en serie, fokuseret på [Azure fleksibilitet teknisk vejledning](./resiliency-technical-guidance.md). Næste artikel i denne serie fokuserer på [gendannelse fra en lokal datacenter til Azure](./resiliency-technical-guidance-recovery-on-premises-azure.md).
