<properties
   pageTitle="Teknisk vejledning: gendannelse fra lokale fejl i Azure | Microsoft Azure"
   description="Artikel om og designe tolerant, høj tilgængelighed, fejlsikret programmer samt planlægning af nedbrud fokuseret på lokale fejl i Azure."
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

#<a name="azure-resiliency-technical-guidance-recovery-from-local-failures-in-azure"></a>Azure fleksibilitet teknisk vejledning: gendannelse fra lokale fejl i Azure

Der er to primære trusler til programmet tilgængelig:

* Fejlen enheder, som drev og servere
* Fuld udnyttelse af kritiske ressourcer, som Beregn under spidsbelastning indlæsningsbetingelser

Azure leverer en kombination af Ressourcestyring, Elasticitet, justering af belastning og partitionering for at aktivere høj tilgængelighed under disse omstændigheder. Nogle af disse funktioner udføres automatisk for alle Azure-tjenester. I nogle tilfælde skal programmet udvikleren gøre nogle yderligere arbejde kan få glæde af dem.

##<a name="cloud-services"></a>Cloud Services

Azure-Skytjenester består af samlinger af en eller flere web eller arbejder roller. En eller flere forekomster af en rolle kan køre samtidig. Konfigurationen bestemmer antallet af forekomster. Rolle forekomster overvåges og administreres via en komponent, kaldet strukturen controller. Strukturen controller registrerer og besvarer mislykkede forsøg på både hardware og automatisk.

Hver forekomst af rolle kører i sin egen virtual machine (VM) og kommunikerer med dens struktur controller gennem en agent for gæst. Gæst agent indsamler ressource og node, herunder VM brugen, status, logfiler, Ressourceforbrug, undtagelser og manglende betingelser. Strukturen controller forespørger gæst agent kan konfigureres med intervaller, og den genstartes VM, hvis gæst agent ikke svarer. Hvis hardware mislykkes, den tilknyttede struktur controller flytter alle forekomster af berørte rolle til en ny hardware node og omkonfigurerer netværk til at dirigere trafik der.

For at få glæde af disse funktioner, bør udviklere sikre, at alle service roller undgå at gemme tilstand på rolle forekomster. I stedet alle fast skal være adgang til data fra robust lagring, som Azure-lager eller Azure SQL-Database. Dette giver mulighed for en hvilken som helst roller til at håndtere anmodninger. Det betyder også, at rolle forekomster kan gå til enhver tid uden at oprette uoverensstemmelser i tilstanden midlertidige eller fast til tjenesten.

Kravet om at gemme tilstand eksternt til rollerne, der har flere konsekvenser. Det betyder f.eks, at alle relaterede ændringer til en tabel med Azure-lager, hvis det er muligt skal ændres i et enkelt objekt gruppe transaktion. Naturligvis det ikke altid er muligt at foretage alle ændringer i en enkelt transaktion. Du skal tage skal du sikre, at rolle forekomst fejl ikke forårsage problemer, når de afbryde længerevarende handlinger, der strækker sig over to eller flere opdateringer til fast tilstanden for tjenesten. Hvis en anden rolle forsøger at prøve denne handling, skal den forventer at skulle og håndtere de tilfælde, hvor arbejdet delvist blev afsluttet.

For eksempel bør du overveje en tjeneste, som partitioner data på tværs af flere lagre. Hvis en kollega rolle går ned, mens det flytning af en shard, fuldføres muligvis ikke flytning af shard. Eller flytning blive gentaget fra starten af en anden arbejder rolle, mulighed for at forårsage tabte data eller beskadigelse af data. For at undgå problemer, skal længerevarende handlinger være en eller begge af følgende:

 * *Idempotent*: gentaget uden side effekter. Kan kun idempotent, skal en længerevarende handling er den samme effekt uanset hvor mange gange den køres, selvom den er afbrudt under kørsel af.
 * *Trinvist restartable*: kunne fortsætte fra det seneste punkt i fejl. Hvis du vil være trinvist restartable, skal en længerevarende handling bestå af en række mindre atomisk handlinger. Det bør også registrere dens status i robust lagerplads, så hver efterfølgende aktivering samler op, hvor den foregående opgave ikke længere.

Til sidst skal skal alle længerevarende handlinger aktiveres flere gange, indtil de lykkes. For eksempel kan en handling af klargøring være placeret i en Azure kø, og derefter fjernet fra køen ved en kollega rolle kun, når det lykkes. Spildopsamling kan være nødvendigt at oprette oprydning data, der blev afbrudt handlinger.

###<a name="elasticity"></a>Elasticitet

Indledende antallet af forekomster, der kører for hver rolle bestemmes i hver rolle konfiguration. Administratorer skal først konfigurere hver rolle til at køre med to eller flere forekomster, der er baseret på forventede belastning. Men du kan nemt skalere rolle forekomster eller ned som forbrug mønstre ændre. Du kan gøre det manuelt i portalen Azure, eller du kan automatisere processen ved hjælp af Windows PowerShell, Service Management API eller tredjepartsværktøjer. Du kan finde flere oplysninger, se, [hvordan du Autoskalering et program](../cloud-services/cloud-services-how-to-scale.md).

###<a name="partitioning"></a>Partitionering

Azure-strukturen controller bruger to typer partitioner:

* En *opdatere domæne* bruges til at opgradere en tjeneste rolle forekomster i grupper. Azure installerer forekomster af tjenesten til flere opdatering domæner. En lokal opdatering, strukturen controller viser ned alle forekomster i én opdatering domæne, opdaterer dem og genstarter dem derefter inden du går til det næste opdatering domæne. Denne metode forhindrer, at hele tjenesten er ikke tilgængelig under opdateringsprocessen.
* En *fejl domæne* definerer potentielle punkter af hardware-eller netværket. For en hvilken som helst rolle, der indeholder mere end én forekomst, sikrer strukturen controller, forekomster fordeles på tværs af flere fejl domæner for at forhindre isolerede hardwarefejl i forstyrrer tjeneste. Fejl domæner styrer alle påvirkning af server og klynge mislykkede forsøg.

[Azure serviceniveauaftale (SLA)](https://azure.microsoft.com/support/legal/sla/) garanterer, at når to eller flere web rolle forekomster er installeret på forskellige fejl og opgradere domæner, de har eksterne connectivity mindst 99.95 procent af tiden. I modsætning til opdatering domæner findes der ingen måde til at styre antallet fejl domæner. Azure allokerer fejl domæner og distribuerer rolle forekomster på tværs af dem automatisk. At mindst de første to forekomster af hver rolle er placeret i forskellige fejl og opgradere domains for at sikre, at en rolle med mindst to forekomster der opfylder SERVICENIVEAUAFTALEN. Dette er repræsenteret i følgende diagram.

![Forenklet visning af isolation af et domæne](./media/resiliency-technical-guidance-recovery-local-failures/partitioning-1.png)

###<a name="load-balancing"></a>Justering af belastning

Al indgående trafik til en web rolle passerer gennem en uden status belastningsjustering, som distribuerer klient-anmodninger mellem rolle forekomster. Individuelle rolle forekomster har ikke offentlige IP-adresser, og de er ikke direkte tilgængelige fra internettet. Web roller er uden status, så enhver klientanmodning kan også sendes til en hvilken som helst rolle forekomst. Hændelsen [StatusCheck](https://msdn.microsoft.com/library/microsoft.windowsazure.serviceruntime.roleenvironment.statuscheck.aspx) opstår hver 15 sekunder. Du kan bruge det til at angive, om rollen er klar til at modtage trafik, eller om det er optaget og tages af belastningsjustering rotationen.

##<a name="virtual-machines"></a>Virtuelle maskiner

Azure virtuelle maskiner er forskellig fra platform, som en tjeneste (PaaS) beregne roller i mange situationer i forhold til høj tilgængelighed. I nogle tilfælde skal du gøre yderligere arbejde for at sikre høj tilgængelighed.

###<a name="disk-durability"></a>Disken holdbarhed

I modsætning til PaaS rolle forekomster er data, der er gemt på virtuelt drev fast, selvom den virtuelle maskine er flyttet. Azure virtuelle maskiner Brug VM diske, der findes som BLOB i Azure-lager. De data, der er gemt på et virtuelt drev er også meget tilgængelig på grund af karakteristika tilgængelighed af Azure-lager.

Bemærk, at drev D (i Windows FOS) undtagelser til denne regel. Drev D er faktisk fysisk lagerplads på rackserveren, der er vært VM, og dens data, går tabt, hvis VM er flyttet til papirkurven. Drev D er beregnet til kun midlertidige lager. I Linux Fremviser Azure "normalt" (men ikke altid) den lokale midlertidige disk som /dev/sdb Bloker enhed. Det er ofte tilsluttet af Azure Linux Agent som /mnt/resource eller /mnt tilslutningspunkter (kan konfigureres via /etc/waagent.conf).

###<a name="partitioning"></a>Partitionering

Azure forstår oprindeligt niveauer i et PaaS til computeren (web rolle og arbejder rolle) og dermed kan korrekt fordele dem på tværs af fejl og opdatering af domæner. I modsætning skal niveauer i en infrastruktur som et tjenesteprogram (IaaS) være manuelt defineret gennem tilgængelighed sæt. Tilgængelighed sæt der kræves til en SERVICENIVEAUAFTALE under IaaS.

![Tilgængelighed indstiller for Azure virtuelle maskiner](./media/resiliency-technical-guidance-recovery-local-failures/partitioning-2.png)

I det foregående diagram tildeles det Internet Information Services (IIS) niveau (som fungerer som en web app-niveau) og det SQL-niveau (som fungerer som et trin data) til forskellige tilgængelighed sæt. Dette sikrer, at alle forekomster af hvert niveau har hardware redundans ved at distribuere virtuelle maskiner på tværs af et domæner, og at hele niveauer ikke er blevet under en opdatering.

###<a name="load-balancing"></a>Justering af belastning

Hvis FOS skal have trafik fordeles over dem, skal du gruppere FOS i et program og belastning saldoen på tværs af en bestemt TCP eller UDP slutpunkt. Se [belastning virtuelle maskiner](../virtual-machines/virtual-machines-linux-load-balance.md)kan finde flere oplysninger. Hvis FOS modtager input fra en anden kilde (for eksempel en kø ordning), er en belastningsjustering er ikke påkrævet. Justering af belastning anvender en grundlæggende tilstandskontrol til at bestemme, om trafik skal sendes til noden. Du kan også oprette dine egne sonder for at implementere program-specifikke sundhed målepunkter, der bestemmer, om VM skal modtage trafik.

##<a name="storage"></a>Lagerplads

Azure-lager er oprindelige robust data service til Azure. Den indeholder blob, tabel, kø og VM plads. En kombination af replikering og ressourcestyring bruges til at levere høj tilgængelighed i en enkelt datacenter. Azure-lager tilgængeligheden SLA garanterer, at mindst 99,9 procent af tiden:

* Korrekt formateret anmodninger om at tilføje, opdatere, læse og slette data korrekt og korrekt behandles.
* Lagerplads konti har forbindelse til internettet gatewayen.

###<a name="replication"></a>Gentagelse

Azure-lager letter data holdbarhed ved at vedligeholde flere kopier af alle data på forskellige drev på tværs af fuldt uafhængige fysisk lagerundersystemer inden for området. Data replikeres synkront, og alle kopier er anvendt, før Skriv er bekræftet. Azure-lager er på det kraftigste ensartet, hvilket betyder, at læser er garanti for at afspejle de seneste skriver. Kopier af data scannes desuden kontinuerligt for at registrere og reparere bit råd, en ofte overset truslen til integriteten af lagrede data.

Services drage fordel af replikering lige ved hjælp af Azure-lager. Tjenesten udvikler behøver ikke at gøre yderligere arbejde til at gendanne fra en lokal fejl.

###<a name="resource-management"></a>Ressourcestyring

Lagerplads konti, der er oprettet efter maj 2014 kan vokse til op til 500 TB (den tidligere maksimum var 200 TB). Hvis ekstra plads er påkrævet, skal programmer udviklet til at bruge flere lagerplads konti.

###<a name="virtual-machine-disks"></a>Virtuelt diske

Et virtuelt disken er gemt som en side blob i Azure-lager, give den alle de samme holdbarhed og skalerbarhed egenskaber som Blob-lager. Dette design gør dataene på et virtuelt disk fast, selvom den server, der kører VM mislykkes og VM skal genstartes på en anden server.

##<a name="database"></a>Database

###<a name="sql-database"></a>SQL-Database

Azure SQL-Database indeholder database som en tjeneste. Det gør det muligt for programmer til hurtigt at klargøre, indsætte data til og forespørgsel relationsdatabaser. Den indeholder mange af de velkendte SQL Server-funktioner og funktionalitet, mens du abstracting belastning af hardware, konfiguration, retter og fleksibilitet.

>[AZURE.NOTE] Azure SQL-Database giver ikke en funktion fungerer ensartet med SQL Server. Det er beregnet til at opfylde et andet sæt krav – en, der har entydigt er tilpasset til skyen programmer (elastiske skala, database som en tjeneste for at reducere vedligeholdelsesomkostninger, og så videre). Kan finde flere oplysninger under [Vælg en skybaseret SQL Server option: Azure SQL (PaaS)-Database eller SQL Server på Azure FOS (IaaS)](../sql-database/sql-database-paas-vs-sql-server-iaas.md).

####<a name="replication"></a>Gentagelse

Azure SQL-Database indeholder indbyggede fleksibilitet knude niveau fejl. Alle skriver til en database replikeres automatisk til to eller flere baggrund noder via en quorum Anvend teknik. (Primært og sekundære mindst én skal bekræfte, at aktiviteten skrives til transaktionsloggen, før posteringen anses for vellykket og returnerer.) I tilfælde af node fejl skifter databasen automatisk til en af de sekundære replikaer. Derved en midlertidig forbindelsen blev afbrudt for klientprogrammer. Derfor skal alle Azure SQL-Database klienter implementere andre former for håndtering af midlertidige forbindelse. Finde flere oplysninger, [Prøv igen service specifikke vejledninger](../best-practices-retry-service-specific.md).

####<a name="resource-management"></a>Ressourcestyring

Hver database, når du har oprettet, er konfigureret med en øvre grænse. Tilgængelige maksimumstørrelsen er 1 TB (størrelse begrænsninger variere baseret på dine webtjenesten, se [service niveauer og ydeevneniveauer af Azure SQL-databaser](../sql-database/sql-database-resource-limits.md#service-tiers-and-performance-levels). Når en database rammer dens øvre grænse, afviser yderligere INSERT eller UPDATE kommandoer. (Forespørgsels- og slette data er stadig muligt).

Azure SQL-Database bruges i en database, en struktur til at administrere ressourcer. Men i stedet for en struktur controller bruges en ring-topologi til at registrere fejl. Hver replika i en klynge har to tilstødende netværk og er ansvarlig for registrering af når de går. Når en replika går ned, udløse dens tilstødende netværk en agent for konfigureres igen for at oprette den igen på en anden computer. Program (throttling) er angivet til at sikre, at en logisk server ikke brug for mange ressourcer på en computer eller overskrider computerens fysiske begrænsninger.

###<a name="elasticity"></a>Elasticitet

Hvis programmet kræver mere end grænsen 1 TB database, skal den implementere en skala fra fremgangsmåde. Du kan skalere ud med Azure SQL-Database ved manuelt partitionering, også kaldet sharding, data på tværs af flere SQL-databaser. Denne metode skala fra giver mulighed for at opnå vækst næsten lineær omkostninger med målestoksforhold. Elastiske vækst eller kapacitet efter behov kan vokse med stigende omkostninger efter behov, fordi databaser er faktureret baseret på den gennemsnitlige faktiske størrelse bruges om dagen, ikke er baseret på mulige maksimumstørrelse.

##<a name="sql-server-on-virtual-machines"></a>SQL Server på virtuelle maskiner

Ved at installere SQL Server (version 2014 eller nyere) på Azure virtuelle maskiner, kan du drage fordel af de traditionelle tilgængelighed af funktioner i SQL Server. Disse funktioner omfatter AlwaysOn tilgængelighed grupper og databasespejling. Bemærk, at Azure FOS, storage og netværk har forskellige funktionsdygtige egenskaber end et lokalt, ikke virtualiseret IT-infrastruktur. En vellykket implementering af en høj tilgængelighed/nedbrud (HA/DR) SQL Server-løsning i Azure kræver, at du forstå forskellene og designe din løsning til at medtage dem.

###<a name="high-availability-nodes-in-an-availability-set"></a>Høj tilgængelighed knuderne i et sæt tilgængelighed

Når du har implementeret en løsning med høj tilgængelighed i Azure, kan du bruge tilgængeligheden angive i Azure til at placere noderne høj tilgængelighed i separate fejl domæner og opgradere domæner. Rene er tilgængelighed en Azure konceptet. Det er en god fremgangsmåde, som du skal følge for at sikre, at dine databaser er faktisk meget tilgængelige, uanset om du bruger AlwaysOn tilgængelighed grupper, databasespejling eller noget andet. Hvis du ikke følger denne fremgangsmåde, kan du muligvis under den false antagelse, at dit system er meget tilgængelig. Men i virkeligheden, din noder kan alle mislykkes samtidigt fordi de foretager skal placeres i det samme et domæne i Azure område.

Denne anbefaling er ikke opfylder log levering. Som en nedbrud gendannelse funktion, skal du sikre dig, at serverne, der kører i separate Azure områder. Ved definition er disse områder separat fejl domæner.

Azure Cloud Services FOS implementeres via portalen klassisk skal være i det samme sæt tilgængelighed, skal du installere dem i den samme skybaseret tjeneste. FOS installeres via Azure ressourcestyring (den aktuelle portal) har ikke denne begrænsning. For klassisk portal installeret FOS i Azure skybaseret tjeneste, kan kun noder i samme Skytjenesten deltage i det samme sæt tilgængelighed. Desuden skal Cloud Services FOS være i det samme virtuelle netværk til at sikre, at de opretholder deres IP'er når tjenesten reparation. Derved undgår DNS-opdatering afbrydelser.

###<a name="azure-only-high-availability-solutions"></a>Kun Azure: høj tilgængelighed løsninger

Du kan have en høj tilgængelighed løsning til SQL Server-databaser i Azure ved hjælp af AlwaysOn tilgængelighed grupper eller databasespejling.

I det følgende diagram viser arkitekturen i AlwaysOn tilgængelighed grupper, der kører på virtuelle Azure-computere. I dette diagram er taget fra dybdegående artikel om dette emne, [høj tilgængelighed og nedbrud til SQL Server på virtuelle Azure-computere](../virtual-machines/virtual-machines-windows-sql-high-availability-dr.md).

![AlwaysOn tilgængelighed grupper i Microsoft Azure](./media/resiliency-technical-guidance-recovery-local-failures/high_availability_solutions-1.png)

Du kan også automatisk klargøre en AlwaysOn tilgængelighed grupper installation til en komplet på Azure FOS ved hjælp af skabelonen AlwaysOn i portalen Azure. Du kan finde flere oplysninger, se [SQL Server AlwaysOn tilbyder i Microsoft Azure Portal galleri](https://blogs.technet.microsoft.com/dataplatforminsider/2014/08/25/sql-server-alwayson-offering-in-microsoft-azure-portal-gallery/).

I det følgende diagram viser brugen af databasespejling på virtuelle Azure-computere. Det er også taget fra emnet i dybden [høj tilgængelighed og nedbrud til SQL Server på virtuelle Azure-computere](../virtual-machines/virtual-machines-windows-sql-high-availability-dr.md).

![Databasespejling i Microsoft Azure](./media/resiliency-technical-guidance-recovery-local-failures/high_availability_solutions-2.png)

>[AZURE.NOTE] Begge arkitekturer kræver et domænenavn fra domænecontrolleren. Men, med databasespejling er det muligt at bruge servercertifikater til at fjerne behovet for et domænenavn fra domænecontrolleren.

##<a name="other-azure-platform-services"></a>Andre Azure-platformstjenester

Programmer, der bygger på Azure drage fordel af platform-funktioner til at gendanne fra lokale fejl. I nogle tilfælde kan du udføre bestemte handlinger at øge tilgængelighed for din scenarie.

###<a name="service-bus"></a>Service Bus

Hvis du vil reducere mod en midlertidig afbrydelse af Azure Service Bus, kan du overveje at oprette en pålidelig klientsiden kø. Midlertidigt bruger en alternativ, lokale lager metode til lagring af meddelelser, der ikke kan føjes til Service Bus køen. Programmet kan beslutte, hvordan du kan håndtere de midlertidigt lagrede meddelelser, når tjenesten er gendannet. Se [bedste fremgangsmåder til forbedring af ydeevnen ved hjælp af Service Bus formidlet messaging](../service-bus-messaging/service-bus-performance-improvements.md) og [Service Bus (nedbrud)](./resiliency-technical-guidance-recovery-loss-azure-region.md#other-azure-platform-services)kan finde flere oplysninger.

###<a name="hdinsight"></a>HDInsight

De data, der er knyttet til Azure HDInsight gemmes som standard i Azure Blob-lager. Azure-lager angiver høj tilgængelighed og holdbarhed egenskaber for Blob-lager. Behandling af flere-node, der er knyttet til Hadoop MapReduce job optræder på en midlertidig Hadoop-distribueret fil System (HDFS), der er klargjort, når HDInsight brug for det. Resultater fra et MapReduce job gemmes også som standard i Azure Blob-lager, så de behandlede data er pålidelig og forbliver meget tilgængelige, efter at Hadoop klynge er fjernet. Du kan finde flere oplysninger [HDInsight (nedbrud)](./resiliency-technical-guidance-recovery-loss-azure-region.md#other-azure-platform-services).

##<a name="checklists-for-local-failures"></a>Kontrollister til lokale fejl

###<a name="cloud-services"></a>Cloud Services

  1. Gennemgå sektionen Cloud Services i dette dokument.
  2. Konfigurere mindst to forekomster for hver rolle.
  3. Fastholdes tilstand i robust lagring, ikke på rolle forekomster.
  4. Håndtere hændelsen StatusCheck korrekt.
  5. Ombryde relaterede ændringer i transaktioner, når det er muligt.
  6. Kontrollér, at arbejder rolle opgaver er idempotent og restartable.
  7. Fortsæt med at kalde handlinger, indtil de lykkes.
  8. Overvej Autoskalering strategier.

###<a name="virtual-machines"></a>Virtuelle maskiner

  1. Gennemgå sektionen virtuelle maskiner af dette dokument.
  2. Brug ikke drev D for fast lagerplads.
  3. Gruppere computere i et webtjenesten i et sæt tilgængelighed.
  4. Konfigurere justering af belastning og valgfrit sonder.

###<a name="storage"></a>Lagerplads

  1. Gennemse afsnittet lager i dette dokument.
  2. Brug flere lagerplads konti, når data eller båndbredde overstiger kvoter.

###<a name="sql-database"></a>SQL-Database

  1. Gennemgå sektionen SQL-Database i dette dokument.
  2. Implementere en politik for forsøg igen for at håndtere midlertidige fejl.
  3. Du kan bruge partitionering/sharding som en strategi for skala ud.

###<a name="sql-server-on-virtual-machines"></a>SQL Server på virtuelle maskiner

  1. Gennemse den SQL Server på virtuelle maskiner afsnit i dette dokument.
  2. Følg de forrige anbefalinger til virtuelle computere.
  3. Brug af SQL Server høj tilgængelighed funktioner, som AlwaysOn.

###<a name="service-bus"></a>Service Bus

  1. Gennemgå sektionen Service Bus af dette dokument.
  2. Overvej at oprette en pålidelig klientsiden kø som en sikkerhedskopi.

###<a name="hdinsight"></a>HDInsight

  1. Gennemse HDInsight del af dette dokument.
  2. Ingen yderligere tilgængelighed trin, der kræves til lokale fejl.

##<a name="next-steps"></a>Næste trin

I denne artikel er en del af en serie, fokuseret på [Azure fleksibilitet teknisk vejledning](./resiliency-technical-guidance.md). Næste artikel i denne serie er [genoprettelse fra et område hele tjenesteforstyrrelse](./resiliency-technical-guidance-recovery-loss-azure-region.md).
