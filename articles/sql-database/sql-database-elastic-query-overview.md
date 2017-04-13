<properties
    pageTitle="Azure SQL-Database elastiske database forespørgselsoversigt | Microsoft Azure"
    description="Oversigt over funktionen elastiske forespørgsel"    
    services="sql-database"
    documentationCenter=""  
    manager="jhubbard"
    authors="torsteng"/>

<tags
    ms.service="sql-database"
    ms.workload="sql-database"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="04/27/2016"
    ms.author="torsteng" />

# <a name="azure-sql-database-elastic-database-query-overview-preview"></a>Azure SQL-Database elastiske database forespørgselsoversigt (preview)

Funktionen elastiske database forespørgsel (i eksemplet) gør det muligt at køre en Transact-SQL-forespørgsel, der strækker sig over flere databaser i Azure SQL Database (SQLDB). Det kan du udføre tværs databaseforespørgsler at få adgang til remote tabeller, og til at oprette forbindelse fra Microsoft og tredjeparter værktøjer (Excel, PowerBI, Tableau, osv.) til at forespørge på tværs af niveauer af data med flere databaser. Med denne funktion kan du tilpasse ud af forespørgsler til store data niveauer i SQL-Database og visualisere resultaterne i business intelligence (BI) rapporter.

## <a name="documentation"></a>Dokumentation

* [Introduktion til forespørgsler i tværs-database](sql-database-elastic-query-getting-started-vertical.md)
* [Rapportere på tværs af skaleret skyen databaser](sql-database-elastic-query-getting-started.md)
* [Forespørgsel på tværs af delt skyen databaser (vandret opdelt)](sql-database-elastic-query-horizontal-partitioning.md)
* [Forespørgsel på tværs af skyen databaser med forskellige skemaer (opdelt lodret)](sql-database-elastic-query-vertical-partitioning.md)
* [SP\_udføre \_remote](https://msdn.microsoft.com/library/mt703714)


## <a name="why-use-elastic-queries"></a>Hvorfor bruge elastiske forespørgsler?

**Azure SQL-Database**

Forespørgsel på tværs af Azure SQL-databaser helt i T-SQL. Dette giver mulighed for skrivebeskyttede forespørgsler over remote databaser. Dette giver mulighed for aktuelle lokal SQL Server-kunder til at overføre programmer, der bruger tre og four deltidsarbejde navne eller sammenkædet server til SQL-database.

**Tilgængelig på standard niveau** Elastiske forespørgsel understøttes på niveau af ydeevne niveau ud over det Premium ydeevne niveau. I afsnittet Preview begrænsningerne under ydeevne begrænsningerne for lavere niveauer af ydeevne.

**Push-remote databaser**

Elastiske forespørgsler kan nu push SQL-parametre til de eksterne databaser til udførelse af.

**Udførelse af lagret procedure**

Udføre lagrede fjernprocedurekald eller remote funktioner ved hjælp af [sp\_udføre \_remote](https://msdn.microsoft.com/library/mt703714).

**Fleksibilitet**

Eksterne tabeller med elastiske forespørgsel kan nu henvise til remote tabeller med et andet skema eller tabelnavn.

## <a name="elastic-database-query-scenarios"></a>Elastiske database forespørgsel scenarier

Målet er at lette forespørgsel scenarier, hvor flere databaser bidrage rækker i et enkelt overordnede resultat. Forespørgslen kan enten bestå af brugeren eller et program direkte eller indirekte gennem værktøjer, der er forbundet til databasen. Dette er især nyttig, når du opretter rapporter, ved hjælp af kommercielle værktøjerne i BI eller data integration – eller alle programmer, der ikke kan ændres. Med en elastiske forespørgsel, kan du forespørge på tværs af flere databaser ved hjælp af velkendte SQL Server connectivity oplevelse i funktioner som Excel, PowerBI, Tableau eller Cognos.
En elastiske forespørgsel gør det muligt for nem adgang til en hel samling af databaser via forespørgsler, der er udstedt af SQL Server Management Studio eller Visual Studio, og gør det nemmere at tværs af databaser forespørgsler fra enhed Framework eller andre ORM-miljøer. Figur 1 viser et scenarie, hvor et eksisterende skyen program (som bruger [elastiske database klientbibliotek](sql-database-elastic-database-client-library.md)) bygger på en skaleret data niveau, og en elastiske forespørgslen bruges til rapportering i tværs-database.

**Figur 1** Elastiske databaseforespørgsel, der bruges på skaleret data niveau

![Elastiske databaseforespørgsel, der bruges på skaleret data niveau][1]

Kundescenarier, hvor elastiske forespørgsel er er betegnet ved følgende topologier:

* **Lodret partitionering – tværs databaseforespørgsler** (Topologi 1): data, der er opdelt lodret mellem et antal databaser i ét data lag. Typisk findes forskellige typer tabeller på forskellige databaser. Det betyder, at skemaet er anderledes ud på forskellige databaser. For eksempel er alle tabeller for lager på én database, mens alle accounting-relaterede tabeller er i en anden database. Almindelige brug tilfælde med denne topologi kræver en forespørgsel på tværs af eller til at oprette rapporter på tværs af tabeller i flere databaser.
* **Vandret partitionering – Sharding** (Topologi 2): Data opdelt vandret for at distribuere rækker på tværs af en skaleret ud data niveau. Skemaet er identiske på alle deltagende databaser med denne fremgangsmåde. Denne metode kaldes også "sharding". Sharding kan udføres og administrerede webdatabasen (1) elastiske værktøjer biblioteker eller (2) selvsigneret-sharding. En elastiske forespørgsel bruges til at forespørge om og samle rapporter på tværs af mange shards.

> [AZURE.NOTE] Elastiske databaseforespørgsel fungerer bedst for lejlighedsvise reporting scenarier, hvor de fleste af behandling kan udføres på niveau data. Tunge reporting arbejdsbelastninger eller data lager scenarier med mere komplekse forespørgsler, skal du også overveje at bruge [Azure SQL Data Warehouse](https://azure.microsoft.com/services/sql-data-warehouse/).


## <a name="elastic-database-query-topologies"></a>Elastiske Database forespørgsel topologier

### <a name="topology-1-vertical-partitioning--cross-database-queries"></a>Topologi 1: Lodret partitionering – kryds-database forespørgsler

For at starte kodning skal du se [Introduktion til forespørgsel i tværs-database (lodret partitionering)](sql-database-elastic-query-getting-started-vertical.md).

En elastiske forespørgsel kan bruges til at gøre data, der er placeret i en SQLDB database, der er tilgængelige for andre SQLDB databaser. Dette giver mulighed for forespørgsler fra én database til at referere til tabeller i en hvilken som helst anden remote SQLDB database. Det første trin er at definere en ekstern datakilde for hver fjerndatabase. Den eksterne datakilde er defineret i den lokale database, hvorfra du vil have adgang til tabeller, der er placeret på fjerndatabasen. Ikke længere er nødvendige på fjerndatabasen. For typisk lodret leverandør scenarier hvor forskellige databaser har forskellige skemaer kan elastiske forespørgsler bruges til at implementere almindelige brug tilfælde som access at referere til data og kryds-database forespørgsler.

**Referencedata**: topologi 1 bruges til reference datastyring. I nedenstående illustration holdes to tabeller (T1 og T2) med referencedata på en dedikeret database. Ved hjælp af en elastiske forespørgsel, du kan nu få adgang til tabeller T1 og T2 fra en fjernplacering fra andre databaser, som vist i figur. Brug topologi 1, hvis referencetabeller er små eller en fjerndatabase forespørgsler til referencetabel har selektiv prædikaterne.

**Figur 2** Lodret partitionering - ved hjælp af elastiske til forespørgsel referencedata

![Lodret partitionering - ved hjælp af elastiske til forespørgsel referencedata][3]

**Forespørgsler i tværs af databaser**: elastiske forespørger Aktivér brug tilfælde, der kræver forespørgsler på tværs af flere SQLDB databaser. Figur 3 viser fire forskellige databaser: CRM, lager, HR og produkter. Forespørgsler, der er udført på en af databaserne, der skal også have adgang til et eller alle de andre databaser. Ved hjælp af en elastiske forespørgsel, kan du konfigurere databasen for denne sag ved at køre et par enkle DDL-sætninger på hver af de fire databaser. Når denne enkeltstående konfiguration er adgang til en ekstern tabel så enkelt som refererer til en lokal tabel fra dine T-SQL-forespørgsler eller fra BI-værktøjer. Denne metode anbefales, hvis de remote forespørgsler ikke returnerer store resultater.

**Figur 3** Lodret partitionering - ved hjælp af elastiske til forespørgsel på tværs af forskellige databaser

![Lodret partitionering - ved hjælp af elastiske til forespørgsel på tværs af forskellige databaser][4]

### <a name="topology-2-horizontal-partitioning--sharding"></a>Topologi 2: Vandret partitionering – sharding

Ved hjælp af elastiske forespørgsel til at udføre rapportering opgaver over en delt, det vil sige, vandret opdelt, kræver data niveau en [elastiske database shard kort](sql-database-elastic-scale-shard-map-management.md) til at repræsentere databaser i data niveau. Typisk kun et enkelt shard kort bruges i dette scenarie og en dedikeret database med elastiske forespørgsel funktioner fungerer som indgangspunkt til rapportering forespørgsler. Kun denne dedikeret database skal have adgang til shard kortet. Figur 4 illustrerer denne topologi og dens konfiguration med elastiske forespørgsel database og shard kortet. Databaser i data lag kan være en hvilken som helst Azure SQL-Database version eller edition. Du kan finde flere oplysninger om biblioteket elastiske database klienten og oprette shard kort, [Shard tilknytte administration](sql-database-elastic-scale-shard-map-management.md).

**Figur 4** Vandret partitionering - ved hjælp af elastiske forespørgsel til rapportering over delt data niveauer

![Vandret partitionering - ved hjælp af elastiske forespørgsel til rapportering over delt data niveauer][5]

> [AZURE.NOTE] Dedikeret elastiske database forespørgsel databasen skal være en SQL DB version 12-database. Der er ingen begrænsninger for shards sig selv.

For at starte kodning skal du se [Introduktion til elastiske databaseforespørgsel til vandrette opdeling (sharding)](sql-database-elastic-query-getting-started.md).

## <a name="implementing-elastic-database-queries"></a>Implementere elastiske databaseforespørgsler

Trin til at implementere elastiske forespørgsel til de lodrette og vandrette leverandør scenarier er beskrevet i følgende afsnit. De også henvise til mere detaljeret dokumentation for de forskellige leverandør scenarier.

### <a name="vertical-partitioning---cross-database-queries"></a>Lodret partitionering - kryds-database forespørgsler

Følgende trin konfigurere elastiske databaseforespørgsler for lodret leverandør scenarier, der kræver adgang til en tabel, der er placeret på remote SQLDB databaser med det samme skema:

*    [Oprette MASTER KEY](https://msdn.microsoft.com/library/ms174382.aspx) mymasterkey
*    [Oprette databasen fastsat LEGITIMATIONSOPLYSNINGER](https://msdn.microsoft.com/library/mt270260.aspx) mycredential
*    [Opret/slip eksterne DATAKILDE](https://msdn.microsoft.com/library/dn935022.aspx) mydatasource af typen **RDBMS**
*    [Opret/slip ekstern tabel](https://msdn.microsoft.com/library/dn935021.aspx) MinTabel

Når du har kørt DDL-sætninger, kan du åbne tabellen remote "MinTabel", som hvis det var en lokal tabel. Azure SQL-Database automatisk åbner en forbindelse til fjerndatabasen, behandler din anmodning på fjerndatabasen, og returnerer resultatet.
Flere oplysninger om de trin, der kræves for lodret leverandør scenariet kan findes i [elastiske forespørgsel til lodret partitionering](sql-database-elastic-query-vertical-partitioning.md).  

### <a name="horizontal-partitioning---sharding"></a>Vandret partitionering - sharding

Følgende trin konfigurere elastiske databaseforespørgsler for vandret leverandør scenarier, der kræver adgang til et sæt af tabel, der er placeret på (som regel) flere remote SQLDB databaser:

*    [Oprette MASTER KEY](https://msdn.microsoft.com/library/ms174382.aspx) mymasterkey
*    [Oprette databasen fastsat LEGITIMATIONSOPLYSNINGER](https://msdn.microsoft.com/library/mt270260.aspx) mycredential
*    Opret en [shard kort](sql-database-elastic-scale-shard-map-management.md) , der repræsenterer dine data niveau ved hjælp af biblioteket elastiske database klient.   
*    [Opret/slip eksterne DATAKILDE](https://msdn.microsoft.com/library/dn935022.aspx) mydatasource af typen **SHARD_MAP_MANAGER**
*    [Opret/slip ekstern tabel](https://msdn.microsoft.com/library/dn935021.aspx) MinTabel

Når du har udført disse trin, kan du åbne tabellen vandret partitioneret "MinTabel", som hvis det var en lokal tabel. Azure SQL-Database automatisk åbnes flere parallelle forbindelser til eksterne databaserne hvor tabellerne fysisk er gemt, behandler anmodninger på remote databaser, og returnerer resultatet.
Flere oplysninger om de trin, der kræves for vandret leverandør scenariet kan findes i [elastiske forespørgsel for vandret partitionering](sql-database-elastic-query-horizontal-partitioning.md).

## <a name="t-sql-querying"></a>T-SQL-forespørgsler
Når du har defineret dit eksterne datakilder og dine eksterne tabeller, kan du bruge almindelige SQL Server-forbindelsesstrenge til at oprette forbindelse til databaserne, hvor du har defineret dine eksterne tabeller. Du kan derefter køre T-SQL-sætninger over dine eksterne tabeller for denne forbindelse med begrænsninger, der er beskrevet nedenfor. Du kan finde flere oplysninger og eksempler på T-SQL-forespørgsler i dokumentation emner for [partitionering vandret](sql-database-elastic-query-horizontal-partitioning.md) og [lodret partitionering](sql-database-elastic-query-vertical-partitioning.md).

## <a name="connectivity-for-tools"></a>Forbindelse til værktøjer
Du kan bruge almindelige SQL Server-forbindelsesstrenge til at oprette forbindelse dine programmer og værktøjer til integration af BI eller data for databaser, der har eksterne tabeller. Sørg for, at SQL Server understøttes som en datakilde til din værktøj. Når forbindelse, referere til den elastiske forespørgsel database og de eksterne tabeller i den pågældende database, ligesom du ville gøre med en hvilken som helst anden SQL Server-database, du opretter forbindelse til med din værktøjet.

> [AZURE.IMPORTANT] Godkendelse ved hjælp af Azure Active Directory med elastiske forespørgsler understøttes ikke i øjeblikket.

## <a name="cost"></a>Omkostninger

Elastiske forespørgsel er inkluderet i omkostningerne for Azure SQL-Database databaser. Bemærk, at topologier, hvor dine remote databaser er i et andet datacenter end elastiske forespørgsel slutpunktet understøttes, men udgangspunkt data fra eksterne databaser betaler normalt [Azure satser](https://azure.microsoft.com/pricing/details/data-transfers/).

## <a name="preview-limitations"></a>Preview begrænsninger
* Kører forespørgslen første elastiske kan tage op til et par minutter på niveau af ydeevne niveau. Nuværende tidspunkt er nødvendigt at indlæse elastiske forespørgsel-funktioner. indlæsning af ydeevnen forbedrer med højere ydeevne niveauer.
* Scripting af eksterne datakilder eller eksterne tabeller fra SSMS eller SSDT understøttes ikke endnu.
* Importér/Eksportér for SQL DB endnu understøtter ikke eksterne datakilder og eksterne tabeller. Hvis du vil bruge Importér/Eksportér, slip disse objekter før det eksporteres, og derefter oprette dem igen efter import.
* Elastiske databaseforespørgsel understøtter i øjeblikket kun skrivebeskyttet adgang til eksterne tabeller. Du kan dog bruge fuld funktionalitet af T-SQL database i det sted, hvor den eksterne tabel er defineret. Det kan være nyttigt at, f.eks., fastholdes midlertidige resultater, der bruger, f.eks., og vælg < column_list > til < local_table > eller for at oprette lagrede procedurer i databasen elastiske forespørgsel som refererer til eksterne tabeller.
* Med undtagelse af nvarchar(max) understøttes ikke LOB typer i ekstern tabeldefinitioner. Du kan oprette en visning på den eksterne database, der konverterer typen LOB til nvarchar(max), definere eksterne tabellen over visningen i stedet for basistabellen og farvetone det derefter tilbage til den oprindelige LOB type i dine forespørgsler som en løsning.
* Kolonne statistik over eksterne tabeller understøttes ikke i øjeblikket. Tabeller statistik er understøttet, men skal oprettes manuelt.

## <a name="feedback"></a>Feedback
Skal du dele feedback på din oplevelse med elastiske forespørgsler med os på Disqus nedenfor, MSDN-foraene, eller på Stackoverflow. Vi er interesseret i alle typer feedback om tjenesten (fejl, grov kanter, funktion mellemrum).

## <a name="more-information"></a>Få mere at vide

Du kan finde flere oplysninger på tværs af databaser forespørgsler og lodret leverandør scenarier i de dokumenter, der er følgende:

* [Forespørgsler i tværs af databaser og lodret leverandør oversigt](sql-database-elastic-query-vertical-partitioning.md)
* Prøv vores trinvist selvstudium for at få en fuldstændig arbejdseksempel kører i minutter: [Introduktion til forespørgsel i tværs-database (lodret partitionering)](sql-database-elastic-query-getting-started-vertical.md).


Flere oplysninger om vandret partitionering og sharding scenarier findes her:

* [Oversigt over vandret partitionering og sharding](sql-database-elastic-query-horizontal-partitioning.md)
* Prøv vores trinvist selvstudium for at få en fuldstændig arbejdseksempel kører i minutter: [Introduktion til elastiske databaseforespørgsel til vandrette opdeling (sharding)](sql-database-elastic-query-getting-started.md).


[AZURE.INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]

<!--Image references-->
[1]: ./media/sql-database-elastic-query-overview/overview.png
[2]: ./media/sql-database-elastic-query-overview/topology1.png
[3]: ./media/sql-database-elastic-query-overview/vertpartrrefdata.png
[4]: ./media/sql-database-elastic-query-overview/verticalpartitioning.png
[5]: ./media/sql-database-elastic-query-overview/horizontalpartitioning.png

<!--anchors-->
