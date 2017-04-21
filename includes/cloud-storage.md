#<a name="data-management-and-business-analytics"></a>Datastyring og virksomhedsanalyse

Administrere og analysere data i skyen er lige så vigtigt som det er andre steder. For at lade du gør dette, leverer Azure en række teknologier til at arbejde med relationelle og ikke-relationelle data. I denne artikel beskrives de enkelte indstillinger. 

##<a name="table-of-contents"></a>Indholdsfortegnelse      

- [BLOB-lager](#blob)
- [Kører DBMS i en virtuel maskine](#dbinvm)
- [SQL-Database](#sqldb)
    - [Synkronisering af SQL-Data](#datasync)
    - [SQL datarapportering ved hjælp af virtuelle maskiner](#datarpt)
- [Table Storage](#tblstor)
- [Hadoop](#hadoop)

## <a name="blob"></a>BLOB-lager

Ordet "blob" er forkortelse for "binært stort objekt", og den beskriver præcis hvilke blob er: en samling af binære oplysninger. Selvom de er enkel, er BLOB endnu meget nyttigt. [Figur 1](#Fig1) illustrerer de grundlæggende regler for Azure Blob-lager.

<a name="Fig1"></a>![Diagram over BLOB][blobs]
 
**Figur 1: Azure Blob-lager indeholder binære data - BLOB - i beholdere.**

Hvis du vil bruge blob, skal oprette du først en Azure *lagerplads konto*. Som en del af dette skal angive du det Azure datacenter, der skal indeholde de objekter, du opretter ud fra denne konto. Det sted, hvor det er placeret, tilhører hver blob, du opretter nogle beholder i kontoen lagerplads. For at få adgang til en blob indeholder et program en URL-adresse med formularen:

http://&lt;*StorageAccount*&gt;.blob.core.windows.net/&lt;*objektbeholder*&gt;/&lt;*BlobName*&gt;

&lt;*StorageAccount* &gt; er et entydigt id, der er tildelt, når der oprettes en ny konto lagerplads, mens &lt; *objektbeholder* &gt; og &lt; *BlobName* &gt; er navnene på en bestemt objektbeholder og en blob i objektbeholderen. 

Azure indeholder to forskellige typer af BLOB. Valgmulighederne er:

- *Bloker* blob, der hver især kan indeholde op til 200 gigabyte data. Som navnet antyder, er en blok blob opdelt i nogle antal blokke. Hvis der opstår fejl under overførsel af en blok blob, kan retransmission CV med den seneste blokering i stedet for at sende hele blob igen. Bloker BLOB er et helt generelle tilgang til storage, og de er den mest almindeligt anvendte blob type i dag.

- *Siden* blob, som kan være så stor på en terabyte. Siden BLOB er udviklet til RAM, og så hver enkelt er inddelt i nogle antal sider. Et program er gratis at læse og skrive individuelle sider tilfældigt BLOB. I Azure virtuelle maskiner, for eksempel bruge FOS, du opretter siden BLOB som fast lagerplads for både OS disketter og data.

Uanset om du vælger Bloker BLOB eller siden blob, kan programmer har adgang til blob-data i flere forskellige måder. Indstillingerne omfatter følgende:

- Direkte via en RESTful (det vil sige HTTP-baseret) få adgang til protokollen. Både Azure-programmer og eksterne programmer, herunder apps, der kører lokalt, kan bruge denne indstilling.
- Brug af biblioteket Azure lagerplads klient, som indeholder en mere udvikler-venlig grænseflade oven på rå RESTful blob access protocol. Igen skal både Azure-programmer og eksterne programmer kan få adgang til BLOB ved hjælp af dette bibliotek.
- Brug af Azure drev, en indstilling, der giver mulighed for at behandle en side blob som et lokalt drev med et NTFS-filsystem en Azure-program. Programmet, siden blob, der ser ud som et almindeligt Windows-filsystem adgang til dem via standard fil I/O. Faktisk læser og skriver sendes til den underliggende side blob, der implementerer Azure-drev. 

Hvis du vil beskytte mod mislykkede forsøg på hardware og forbedre tilgængelighed, replikeres alle blob på tværs af tre computere i en Azure datacenter. Skrivning til en blob opdaterer alle tre kopier, så nyere læser ikke kan se modstridende resultater. Du kan også angive, at en blob-data skal kopieres til en anden Azure datacenter i samme geografisk, men mindst 500 sømil ikke til stede. Dette kopierer, kaldet *geografisk gentagelse*, sker der i løbet af et par minutter en opdatering med blob, og det er praktisk til nedbrud.

Data i BLOB kan også gøres tilgængelige via Azure *Indhold levering netværk (CDN)*. Ved at cachelagre kopier af blob-data på massevis af servere over hele verden, kan CDN fremskynde adgang til oplysninger, der bruges flere gange. 

Simple, som de er, BLOB er det rigtige valg i mange situationer. Lagring og streaming video og lyd er indlysende eksempler, som er sikkerhedskopier og andre typer af arkivering af data. Udviklere kan også bruge BLOB til at holde alle slags ustrukturerede data de ønsker. Har du en enkle måde til at gemme og få adgang til binære data kan være praktiske overraskende.


## <a name="dbinvm"></a>Kører DBMS i en virtuel maskine

Mange programmer bruger i dag en slags (DBMS) database management system. Relationelle systemer såsom SQL Server er mest anvendte valg, men ikke-relationelle fremgangsmåder, ofte kaldet *NoSQL* teknologier, får mere populært hver dag. Hvis du vil lade skyen programmer bruger disse indstillinger for administration af data, virtuelle Azure-computere kan du køre en DBMS (relationelle eller NoSQL) i en VM. [Figur 2](#Fig2) viser, hvordan det ser ud med SQL Server.

<a name="Fig2"></a>![Diagram over SQL Server på en virtuel maskine][SQLSvr-vm]
 
**Figur 2: Azure virtuelle maskiner kan kører DBMS i en VM med brugerdata, der leveres af BLOB.**

Både udviklere og databaseadministratorer ligner dette scenario meget kører samme softwaren i deres egen datacenter. Næsten alle SQL Server-funktioner kan bruges til forekomsten i eksemplet herunder, og du har fuld administrativ adgang til systemet. Du kan også har ansvaret for administration af databaseserveren, naturligvis, som om den kørte lokalt.

Som [Figur 2](#Fig2) viser, vises dine databaser gemmes på den lokale disk VM serveren kører i. Under dækker, men skrives hver af de pågældende diske til en Azure blob. (Det er svarer til at bruge et SAN i dit eget datacenter med en blob, der meget fungerer som en LUN). Som med en hvilken som helst Azure blob de data, den indeholder replikeres tre gange i et datacenter og, hvis du anmoder om det, geografisk replikeres til en anden datacenter i samme område. Du kan også bruge indstillinger, såsom SQL Server-database spejling af øget pålidelighed. 

En anden måde at bruge SQL Server i en VM er at oprette en hybrid-program, hvor dataene ligger på Azure, mens programmet logik kører i det lokale miljø. Dette kan for eksempel være mening, når programmer, der kører på flere placeringer eller på forskellige mobilenheder skal dele de samme data. For at sikre kommunikationen mellem skyen database og lokale logik enklere, kan en organisation bruge Azure virtuelt netværk til at oprette et virtuelt privat netværk (VPN) forbindelse mellem en Azure datacenter og sin egen lokale datacenter.


## <a name="sqldb"></a>SQL-Database

For mange personer, du kører DBMS i en VM er den første indstilling, der leveres til Husk til administration af strukturerede data i skyen. Det er ikke det eneste valg, dog er heller ikke altid det bedste valg. I nogle tilfælde administration af data ved hjælp af en Platform som en tjeneste (PaaS) fremgangsmåde giver mere mening. Azure leverer en PaaS teknologi kaldet SQL-Database, hvor du kan gøre dette for relationelle data. [Figur 3](#Fig3) illustrerer denne indstilling. 

<a name="Fig3"></a>![Diagram over SQL-Database][SQL-db]
 
**Figur 3: SQL-Database indeholder en delt PaaS relationelle lagerplads tjeneste.**

SQL-Database give ikke hver kunde sin egen fysisk forekomst af SQL Server. I stedet, den udbyder med flere arkitekturer, med en logisk SQL-databaseserver for hver kunde. Alle kunder dele Beregn og lager kapaciteten, som indeholder et tjenesten. Og som med Blob-lager, alle data i SQL-Database er gemt på tre separate computere i en Azure datacenter, hvor dine databaser indbyggede høj tilgængelighed (HA).

Et program ligner SQL-Database meget SQL Server. Programmer kan udstede SQL-forespørgsler mod relationelle tabeller, bruge T-SQL-lagrede procedurer og udføre transaktioner på tværs af flere tabeller. Og fordi programmer få adgang til SQL-Database ved hjælp af tabelformat Data Stream (TDS)-protokollen, den samme protokol, der bruges til at få adgang til SQL Server, de kan arbejde med data ved hjælp af enhed Framework, ADO.NET, JDBC og andre velkendte data access-grænseflader. 

Men fordi SQL-Database er en skybaseret tjeneste, der kører i Azure datacentre, du behøver ikke at administrere alle systemets fysiske aspekter, som på harddisken. Du skal også ikke bekymre dig om opdatering af software eller håndtering af andre lavt niveau administrative opgaver. Hver kundens organisation stadig styrer sin egen databaser, naturligvis, herunder deres skemaer og brugerlogon, men mange af de trivielle administrative opgaver er gjort for dig. 

Mens SQL-Database ligner meget SQL Server til programmer, kan den ikke fungerer præcis det samme som et DBMS, der kører på en fysisk eller virtuelt. Da den kører på delt hardware, varierer dens ydeevne ved indlæsning af alle sine kunders som anbringes på hardwaren. Det betyder, at ydeevnen for sig, en lagret procedure i SQL-Database kan variere fra én dag til næste. 

I dag, kan SQL-Database du oprette en database, hvor du holder op til 150 gigabyte. Hvis du vil arbejde med større databaser, giver tjenesten mulighed for kaldet *sammenslutning*. For at gøre dette, opretter en databaseadministrator to eller flere *sammenslutning medlemmer*, hver især er en separat database med sin egen skema. Data er udbrede disse medlemmer, noget, der ofte kaldes *sharding*, med hvert enkelt medlem, der er tildelt en entydig *sammenslutning nøgle*. Bør fokusere på et program problemer med SQL-forespørgsler mod disse data ved at angive tasten sammenslutning, der identificerer medlemmet sammenslutning forespørgslen. Dette giver mulighed for brug af en traditionel relationelle tilgang med store datamængder. Som altid, er der kompromiser; hverken forespørgsler eller transaktioner kan dække sammenslutning medlemmer, f.eks. Men når en relationel PaaS tjeneste er det bedste valg, og disse kompromiser acceptable, ved hjælp af SQL sammenslutning kan være en god løsning.

SQL-Database kan anvendes af programmer, der kører på Azure eller andre steder, f.eks i dit lokale datacenter. Dette gør det anvendeligt til skyen programmer, der skal relationelle data samt lokale programmer, der kan få glæde af lagring af data i skyen. Et mobile program kan stole på SQL-Database til at administrere delte relationelle data, for eksempel, som fungerer muligvis et lager-program, der kører på flere forhandlere over hele verden.

Overvejelser om med SQL-Database hæver et indlysende (og vigtige) problem: Når skal du kører SQL Server i en VM, og når et bedre valg er SQL-Database? Som du plejer, der er kompromiser, og så, hvordan du håndterer er bedre afhænger af dine behov. 

En enkelt metode til at bekymre dig om det er at få vist SQL-Database som værende for nye programmer, mens SQL Server på en VM er et bedre valg, når du flytter et eksisterende lokalt programmet på computeren til skyen. Det kan også være nyttigt at kigge på denne beslutning på en mere detaljerede måde, men. For eksempel SQL-Database er lettere at bruge, fordi der ikke er minimale konfiguration og administration. Men kører SQL Server i en VM kan have mere forudsigelige ydeevne – det er ikke en delt tjeneste - og understøtter også større uden for organisationsnetværket databaser end SQL-Database. Stadig, SQL-Database indeholder indbyggede gentagelse af både data og behandling, hvorved du en høj tilgængelighed DBMS med meget lidt arbejde effektivt. Selvom SQL Server giver dig mere kontrol og et lidt mere omfattende sæt af indstillinger, er det nemmere at angive op og betydeligt mindre arbejde for at styre SQL-Database.

Endelig er det vigtigt at understrege, SQL-Database ikke er den eneste PaaS data service tilgængelig på Azure. Microsoft-partnere, giver samt andre indstillinger. For eksempel tilbyder ClearDB en MySQL PaaS tilbyder, mens Cloudant sælger valgmuligheden NoSQL. PaaS datatjenester er den rigtige løsning i mange situationer, og så denne metode til datastyring er en vigtig del af Azure.


### <a name="datasync"></a>Synkronisering af SQL-Data

Mens SQL-Database vedligeholde tre kopier af hver database i en enkelt Azure datacenter, gentage ikke den automatisk data mellem Azure datacentre. I stedet kan SQL Data Sync, en tjeneste, som du kan bruge til at gøre dette. [Figur 4](#Fig4) viser, hvordan det ser ud.

<a name="Fig4"></a>![Diagram over SQL data synkronisering][SQL-datasync]
 
**Figur 4: SQL Data Sync synkroniserer data i SQL-Database med data i andre Azure og lokale datacentre.**

Som diagrammet viser, kan SQL Data Sync synkronisere data på tværs af forskellige steder. Antag, at du kører et program i flere Azure datacentre, for eksempel med data, der er gemt i SQL-Database. Du kan bruge SQL Data synkronisering til disse data være synkroniseret. Synkronisering af SQL Data kan også synkronisere data mellem en Azure datacenter og en forekomst af SQL Server, der kører i et lokalt datacenter. Det kan være nyttige til at vedligeholde både en lokal kopi af data, der bruges af programmer i det lokale miljø og skyen kopi bruges af programmer, der kører på Azure. Og selvom den ikke vises i figuren, SQL Data Sync også kan bruges til at synkronisere data mellem SQL-Database og SQL Server, der kører i et VM til Azure eller et andet sted.

Synkronisering kan være tovejs-, og du bestemme nøjagtigt, hvilke data der synkroniseres og hvor ofte det er gjort. (Synkronisering mellem databaser ikke atomisk, men - der er altid mindst nogle forsinkelse). Og men det bruges, konfiguration af synkronisering med SQL Data synkronisering er helt konfiguration indsatsbaseret; der ikke er en kode til at skrive.


### <a name="datarpt"></a>SQL datarapportering ved hjælp af virtuelle maskiner

Når en database indeholder data, vil nogen sandsynligvis oprette rapporter med dataene. Azure kan køre SQL Server Reporting Services (SSRS) i virtuelle Azure-computere, som er funktionelt svarer til at køre lokal SQL Server Reporting Services. Derefter kan du bruge SSRS til at køre rapporter på data, der er gemt i en Azure SQL-Database.  [Figur 5](#Fig5) viser, hvordan processen fungerer.

<a name="Fig5"></a>![Diagram over SQL-rapportering][SQL-report]
 
**Figur 5: SQL Server Reporting Services kører i et virtuelle Azure-computere indeholder reporting services til data i SQL-Database. .**

Før en bruger kan se en rapport, definerer nogen hvordan rapporten skal se ud (trin 1). Med SSRS på en VM dette kan gøres ved hjælp af en af to værktøjer: SQL Server Data Tools, en del af SQL Server 2012 eller dens foregående, Business Intelligence (BI) Development Studio. Som med SSRS, er disse rapportdefinitioner af udtrykt i rapporten Definition Language (RDL). Når RDL-filer til en rapport er blevet oprettet, er de overført til en VM i skyen (trin 2). Den ønskede rapportdefinition er nu klar til brug.

Dernæst skal en bruger af programmet giver adgang til rapporten (trin 3). Programmet overfører denne anmodning til SSRS VM (trin 4), som kontakter SQL-Database eller andre datakilder for at hente de data, der skal bruges (trin 5). SSRS bruges disse data og de relevante RDL-filer til at gengive rapporten (trin 6), derefter returnerer rapporten til programmet (trin 7), som viser den til brugeren (trin 8).

Integrere en rapport i et program, det scenarie, der er vist her, er ikke den eneste indstilling. Du kan også få vist rapporter i Rapportstyring på VM, SharePoint på VM eller i andre måder. Rapporter kan også kombineres med en rapport, der indeholder et link til en anden.

SSRS på en Azure VM giver dig fuld funktionalitet som en reporting løsning i skyen. Rapporter kan bruge alle datakilder, der understøttes af SSRS. Programmer og -rapporter kan indeholde integreret kode eller assemblies til at understøtte brugerdefinerede funktionsmåder. Udførelse af rapporten og gengivelse er hurtig, fordi rapportere serverindhold og program kører sammen på den samme virtuelle server.



## <a name="tblstor"></a>Table Storage

Relationelle data er nyttig i mange situationer, men det er ikke altid det rette valg. Hvis dit program skal hurtige, enkle adgang til meget store mængder løst strukturerede data, for eksempel en relationel database fungerer muligvis ikke godt. En NoSQL teknologi, der sandsynligvis er et bedre valg.

Azure-Tabellager er et eksempel på denne type NoSQL metode. På trods af dets navn understøtter Table Storage ikke standard relationelle tabeller. I stedet, kan det, der kendes som en *nøgle/værdi gemme*, at knytte et sæt data til en bestemt nøgle, og klik derefter at lade en adgang til programmer dataene ved at indsende tasten. [Figur 6](#Fig6) illustrerer de grundlæggende funktioner.

<a name="Fig6"></a>![Diagram over tabellagerplads][SQL-tblstor]
 
**Figur 6: Azure Table Storage er en nøgleværdi/butik, hvor du kan hurtigt, let adgang til store datamængder.**

Sådan BLOB er hver tabel knyttet til en Azure-lager-konto. Tabeller kaldes også meget som BLOB med en URL-adresse i formularen

http://&lt;*StorageAccount*&gt;.table.core.windows.net/&lt;*tabelnavn*&gt;

Som i figur vises, er hver tabel inddelt i nogle antallet af partitioner, hver især kan være gemt på en anden computer. (Dette er en slags sharding, som i SQL-sammenslutning.) Både Azure-programmer og programmer, der kører et andet sted kan få adgang til en tabel ved hjælp af OData-RESTful protokollen eller biblioteket Azure lagerplads klient.

Hver partition i en tabel indeholder nogle antal *enheder*, hver indeholder op til 255 *Egenskaber*. Hver egenskaben har et navn, en type (såsom binært tal, boolesk, dato og klokkeslæt, heltal eller streng) og en værdi. Disse tabeller har ingen fast skema i modsætning til relationelle lagerplads, og så forskellige enheder i den samme tabel kan indeholde egenskaber med forskellige typer. En enhed muligvis bare en strengegenskab, der indeholder et navn, f.eks, mens en anden enhed i den samme tabel har to Int egenskaber, der indeholder en kunde-id-nummer og en kreditværdighed.

For at identificere et bestemt objekt i en tabel, indeholder et program, der enheds-tasten. Tasten består af to dele: en *Partitionsnøgle* , der identificerer en bestemt partition og en *række nøgle* , der identificerer et objekt i denne partition. Klienten anmoder om enheden med Partitionsnøgle A og række 3 for eksempel i [figur 6](#Fig6), og Table Storage returnerer den enhed, herunder alle de egenskaber, den indeholder.

Denne struktur kan være store tabeller – en enkelt tabel kan indeholde op til 100 TB data - og den giver hurtig adgang til de data, de indeholder. Det viser også begrænsninger, men. For eksempel, er der ingen understøttelse af transaktions opdateringer, der strækker sig over tabeller eller lige partitioner i en enkelt tabel. Et sæt opdateringer til en tabel kan kun grupperes i en atomisk transaktion, hvis alle de enheder, der er involveret i den samme partition. Der er også ingen måde at forespørge på en tabel baseret på værdien af dens egenskaber eller er der understøttelse af joinforbindelser på tværs af flere tabeller. Og i modsætning til relationsdatabaser, tabeller har ingen understøttelse af lagrede procedurer.

Azure-Tabellager er et godt valg til programmer, der skal have hurtige og billige adgang til store mængder løst strukturerede data. Et Internetprogram, der gemmer profiloplysninger for mange brugere kan for eksempel bruge tabeller. Hurtig adgang er vigtige i dette tilfælde, og programmet sandsynligvis brug for ikke den fulde styrke i SQL. Giver denne funktion til at få hastighed og størrelse kan nogle gange giver mening, og så Table Storage er kun den rigtige løsning til nogle af de problemer.


## <a name="hadoop"></a>Hadoop

Organisationer har opbygning af data lagre i mange år. Disse samlinger af oplysninger, oftest gemt i relationelle tabeller, give andre personer arbejder med, og Find ud fra data på mange forskellige måder. Med SQL Server, for eksempel er det almindelige til at bruge funktioner såsom SQL Server Analysis Services til at gøre dette.

Men Antag, at du vil gøre analyse af ikke-relationelle data. Dine data kan tage mange formularer: oplysninger fra sensorer eller RFID mærker, logfiler i serverfarme, clickstream data, som webprogrammer, billeder fra medicinsk diagnosticering enheder og meget mere. Disse data kan også være virkelig stor, for stor til at bruges effektivt med et traditionelle datawarehouse. Problemer med stor data sådan ud, sjældne et par år siden, er nu blevet helt almindelige.

Hvis du vil analysere denne type stor data, vores branche i høj grad har konvergeret på en enkelt løsning: open source-teknologi Hadoop. Hadoop kører på en klynge af fysiske eller virtuelle maskiner sprede de data, den fungerer på på tværs af disse computere og behandling af dem parallelt. Flere computere Hadoop har du bruger, jo hurtigere det, der kan udføre det arbejde, det er at gøre.

Denne type problem er den naturlige tilpasning for den offentlige sky. I stedet for vedligeholde en hær af lokale servere, der kan være inaktive meget af den tid, kører Hadoop i skyen, kan du oprette (og lønsatsen for) FOS kun, når du har brug for. Endnu bedre: mere og mere stor data, du vil analysere med Hadoop oprettes i skyen, så du problemer med at flytte den rundt om. Microsoft yder for at hjælpe dig med at udnytte disse synergieffekten, en Hadoop-tjeneste på Azure. [Figur 7](#Fig7) viser de vigtigste komponenter af denne tjeneste.

<a name="Fig7"></a>![Diagram over hadoop][hadoop]

**Figur 7: Hadoop på Azure kører MapReduce job, der behandler data parallelt ved at bruge flere virtuelle computere.**

For at bruge Hadoop på Azure skal bede du først denne skyen platform til at oprette en Hadoop-klynge, der angiver antallet af FOS, du har brug for. Konfigurere en Hadoop klynge selv er ikke-simpel opgave, og så så Azure gøre det for dig giver mening. Når du er færdig med klyngen, du lukker den. Det er ikke nødvendigt at betale for Beregn ressourcer, som du ikke bruger.

Et Hadoop-program, også kaldet en *sag*, bruger en programming model kendt som *MapReduce*. Som i figur vises, kører logik for en MapReduce sag samtidigt på tværs af mange FOS. Databehandlingen parallelt, kan Hadoop analysere data meget hurtigere end enkelt maskine løsninger.

På Azure dataene et job fungerer på MapReduce er typisk informeret blob-lager. I Hadoop, men forventer MapReduce job data skal gemmes i *Hadoop-distribueret fil System (HDFS)*. HDFS minder om Blob-lager på nogle måder den kopieres data på tværs af flere fysiske servere, f.eks. I stedet for duplikere denne funktionalitet, Fremviser Hadoop på Azure Blob-lager gennem API HDFS i stedet som figur viser. Mens logik i en MapReduce sag mener, at det har adgang til almindelige HDFS filer, er jobbet faktisk arbejde med data, som streames til den fra BLOB. Og for at understøtte den sag, hvor flere opgaver kører over de samme data, Hadoop på Azure også tillade kopiere data fra BLOB til fuld HDFS kører i FOS. 

MapReduce job skrives ofte i Java i dag, en metode, der understøtter Hadoop på Azure. Microsoft har også tilføjet understøttelse af oprettelse af MapReduce job på andre sprog, herunder C#, F # og JavaScript. Målet er at give denne stor data teknologi mere nem adgang til en større gruppe af udviklere.

Sammen med HDFS og MapReduce omfatter Hadoop andre teknologier, som personer analysere data uden at skrive et MapReduce job sig selv. For eksempel er gris et overordnet sprog, der er udviklet til at analysere store data, mens Hive tilbyder en SQL-lignende sprog kaldet HiveQL. Både gris og Hive faktisk genererer MapReduce job, der behandler HDFS data, men de skjuler denne kompleksitet fra deres brugere. Begge er angivet med Hadoop på Azure.

Microsoft yder også en HiveQL driver til Excel. Ved hjælp af et Excel-tilføjelsesprogram, kan virksomhedsanalytikere oprette HiveQL forespørgsler (og dermed MapReduce job) direkte fra Excel, og derefter behandle og visualisere resultaterne ved hjælp af PowerPivot og andre Excel-funktioner. Hadoop på Azure omfatter andre teknologier samt, som den maskine, læ biblioteker Mahout, graph mining systemet Pegasus og mere.

Stor dataanalyse er vigtige, og så Hadoop også er vigtige. Ved at indsende Hadoop som en administreret tjeneste på Azure, samt links til velkendte funktioner som Excel, Microsoft har til formål at gøre denne teknologi, der er tilgængelige til en større gruppe af brugere.

Alle typer data er mere alment vigtigt. Det er derfor Azure indeholder en række muligheder for data administration og business analytics. Andet program, du forsøger at oprette, er det sandsynligvis, skal du finde noget i denne skyen platform, der fungerer sammen for dig.

[blobs]: ./media/cloud-storage/Data_01_Blobs.png
[SQLSvr-vm]: ./media/cloud-storage/Data_02_SQLSvrVM.png
[SQL-db]: ./media/cloud-storage/Data_03_SQLdb.png
[SQL-datasync]: ./media/cloud-storage/Data_04_SQLDataSync.png
[SQL-report]: ./media/cloud-storage/Data_05_SQLReporting.png
[SQL-tblstor]: ./media/cloud-storage/Data_06_TblStorage.png
[hadoop]: ./media/cloud-storage/Data_07_Hadoop.png
