<properties
   pageTitle="Fejlfinding i forbindelse med Azure SQL datawarehouse | Microsoft Azure"
   description="Fejlfinding i forbindelse med Azure SQL datawarehouse."
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="sonyam"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="08/30/2016"
   ms.author="sonyama;barbkess"/>

# <a name="troubleshooting-azure-sql-data-warehouse"></a>Fejlfinding i forbindelse med Azure SQL datawarehouse

I dette emne beskrives nogle af de mest almindelige fejlfinding i forbindelse med spørgsmål vi høre fra vores kunder.

## <a name="connecting"></a>Oprette forbindelse

| Problem                              | Opløsning                                      |
| :----------------------------------| :---------------------------------------------- |
| Logon mislykkedes for brugeren NT AUTHORITY\ANONYMOUS LOGON. (Microsoft SQL Server, fejl: 18456) | Denne fejl opstår, når en AAD bruger forsøger at oprette forbindelse til master databasen, men har ikke en bruger i master.  Du kan løse dette problem du enten angive et SQL-datalager, du vil oprette forbindelse til samtidig forbindelse eller føje brugeren til master databasen.  Se [Oversigt over sikkerhed][] artikel for at få mere at vide.|
|Serveren vigtigste "Bemærkninger" er ikke kunne få adgang til databasen "master" under den aktuelle sikkerhedskontekst. Kan ikke åbne databasen med standard. Logon mislykkedes. Logon mislykkedes for bruger 'Bemærkninger'. (Microsoft SQL Server, fejl: 916) | Denne fejl opstår, når en AAD bruger forsøger at oprette forbindelse til master databasen, men har ikke en bruger i master.  Du kan løse dette problem du enten angive et SQL-datalager, du vil oprette forbindelse til samtidig forbindelse eller føje brugeren til master databasen.  Se [Oversigt over sikkerhed][] artikel for at få mere at vide.|
| CTAIP fejl                        | Denne fejl kan opstå, når et logon er blevet oprettet, på master SQL server-database, men ikke i SQL Data Warehouse-databasen.  Hvis du støder på denne fejl, kan du se nærmere på artiklen [Oversigt over sikkerhed][] .  I denne artikel forklares det, hvordan du opretter oprette en logon og bruger på master og hvordan du opretter en bruger i SQL Data Warehouse databasen.|
| Blokeret af Firewall                |Azure SQL-databaser er beskyttet af server og database niveau firewalls at sikre, at kun kendte IP-adresser, der har adgang til en database. Disse firewalls er sikre som standard, hvilket betyder, at du skal aktivere eksplicit og IP-adresse eller adresseområde, før du kan oprette forbindelse.  For at konfigurere din firewall til access skal du følge trinnene i [Konfigurer server firewall adgang til din klient IP-adresse][] i [artikel Provisioning instruktioner][].|
| Kan ikke oprette forbindelse med værktøjet eller -driver | SQL Data Warehouse anbefaler at bruge [SSMS][], [SSDT til Visual Studio 2015][]eller [sqlcmd][] til at søge i dine data. Du kan finde flere oplysninger om drivere og oprette forbindelse til SQL Data Warehouse [drivere til Azure SQL Data Warehouse][] og [oprette forbindelse til Azure SQL Data Warehouse][] artikler.|


## <a name="tools"></a>Værktøjer

| Problem                              | Opløsning                                      |
| :----------------------------------| :---------------------------------------------- |
| Visual Studio objekt explorer mangler AAD brugere | Dette er et kendt problem.  Få vist brugerne i [sys.database_principals][]som en løsning.  Se [godkendelse til Azure SQL Data Warehouse][] mere at vide om brug af Azure Active Directory med SQL Data Warehouse.|

## <a name="performance"></a>Ydeevne

|  Problem                             | Opløsning                                      |
| :----------------------------------| :---------------------------------------------- |
| Fejlfinding af forespørgsel ydeevne  | Hvis du forsøger at foretage fejlfinding af en bestemt forespørgsel, kan du starte med at [lære at overvåge dine forespørgsler][].|
| Dårlig forespørgselsydeevne og planer er ofte et resultat af manglende statistik   | Den mest almindelige årsag til dårlig ydeevne er manglende statistik på dine tabeller.  Se [Maintaining tabel statistik] [ Statistics] oplysninger om, hvordan du opretter statistik og hvorfor de er vigtige for ydeevnen.|
| Lav på dokumentsammenfald / forespørgsler i kø   | Forstå [arbejdsbelastningen management][] er vigtigt for at forstå, hvordan du saldo hukommelsesallokering med på dokumentsammenfald.|
| Sådan implementerer de bedste fremgangsmåder    | Bedst Placer begynde at få forskellige måder at forbedre Forespørgselsydeevnen i er [SQL Data Warehouse bedste fremgangsmåder][] artikel.|
| Hvordan du kan forbedre ydeevnen med skalering  | Nogle gange er løsningen til forbedring af ydeevnen blot tilføje mere beregne power i dine forespørgsler ved at [skalere dit SQL Data Warehouse][].|
| Dårlig forespørgselsydelse som et resultat af dårlig indeks kvalitet | Nogle gange forespørgsler kan bliver langsommere på grund af [dårlig columnstore indeks kvalitet][].  I denne artikel finder du kan finde flere oplysninger, og hvordan du [genopbygger indekser til at forbedre kvaliteten på målgruppen][].|

## <a name="system-management"></a>Systemadministration af

|  Problem                             | Opløsning                                      |
| :----------------------------------| :---------------------------------------------- |
| Meddelelse 40847: Kunne ikke udføre handlingen, fordi serveren vil overskride kvoten tilladte Database transaktion enhed af 45000. | Enten reducere [DWU][] af den database, du forsøger at oprette eller [anmode om en kvote forøgelse][].|
| Ved at undersøge udnyttelse af pladsen    | Se [tabellen størrelser][] at forstå mellemrum anvendelsen af dit system.|
| Hjælp til administration af tabeller          | Få vist [tabel oversigt] [ Overview] artikel for at få hjælp til at administrere dine tabeller.  I denne artikel indeholder også links til flere emner som [tabel-datatyper][Data types], [distribuere en tabel][Distribute], [indeksering en tabel][Index], [partitionering en tabel][Partition], [Maintaining tabel statistik] [ Statistics] og [midlertidige tabeller][Temporary].|

## <a name="polybase"></a>Polybase

|  Problem                             | Opløsning                                      |
| :----------------------------------| :---------------------------------------------- |
| UTF-8-fejl                        |  Understøtter i øjeblikket PolyBase kun indlæsning datafiler, der er blevet UTF-8-kodet.  Se [arbejde omkring kravet PolyBase UTF-8][] for at få vejledning til, hvordan du kan løse denne begrænsning.|
| Indlæse mislykkes på grund af store rækker   | Understøttelse af store række er i øjeblikket ikke tilgængelig for Polybase.  Det betyder, at hvis tabellen indeholder nvarchar(Max),varchar(Max), NVARCHAR(MAX) eller VARBINARY(MAX), eksterne tabeller ikke kan bruges til at indlæse dine data.  Indlæse for store rækker er i øjeblikket kun understøttes via Azure Data Factory (med BCP), Azure Stream Analytics, SSIS, BCP eller klassen .NET SQLBulkCopy. PolyBase understøttelse af store rækker tilføjes i en senere version.|
| BCP indlæsning af tabel med Maks datatypen går ned | Der er et kendt problem, som kræver, at nvarchar(Max),varchar(Max), NVARCHAR(MAX) eller VARBINARY(MAX) skal placeres i slutningen af tabellen i visse scenarier.  Prøv at flytte dine Maks kolonner til slutningen af tabellen.|

## <a name="differences-from-sql-database"></a>Forskelle fra SQL-Database

|  Problem                             | Opløsning                                      |
| :----------------------------------| :---------------------------------------------- |
| Ikke-understøttede funktioner i SQL-Database  | Se [ikke-understøttede tabelfunktioner][].|
| Ikke-understøttede datatyper i SQL-Database  | Se [ikke-understøttede datatyper][].|
| DELETE og UPDATE begrænsninger      | Se [opdatere løsninger][], [slette løsninger][] og [Ved hjælp af CTAS til at løse ikke-understøttede Opdater og Slet syntaks][].  |
| FLET sætningen understøttes ikke   | Se [FLETTE løsninger][].|
| Lagret procedure begrænsninger       | Se [lagrede procedure begrænsninger][] at forstå nogle af begrænsningerne for lagrede procedurer.|
| Brugerdefinerede funktioner understøtter ikke SELECT-sætninger | Dette er en aktuel begrænsning af vores brugerdefinerede funktioner.  Se [Oprette funktionen][] til syntaksen vi understøtter.   |

## <a name="next-steps"></a>Næste trin

Hvis du er blev ikke kan finde en løsning på problemet ovenfor, her er nogle andre ressourcer, kan du prøve.

- [Blogge]
- [Anmode om funktioner]
- [Videoer]
- [KAT team blogge]
- [Oprette supportbilletter]
- [MSDN-forum]
- [Stable overløb-forum]
- [Twitter]

<!--Image references-->

<!--Article references-->
[Oversigt over sikkerhed]: ./sql-data-warehouse-overview-manage-security.md
[SSMS]: https://msdn.microsoft.com/library/mt238290.aspx
[SSDT til Visual Studio 2015]: ./sql-data-warehouse-install-visual-studio.md
[Drivere til Azure SQL datawarehouse]: ./sql-data-warehouse-connection-strings.md
[Oprette forbindelse til Azure SQL datawarehouse]: ./sql-data-warehouse-connect-overview.md
[Oprette supportbilletter]: ./sql-data-warehouse-get-started-create-support-ticket.md
[Skalering SQL datawarehouse]: ./sql-data-warehouse-manage-compute-overview.md
[DWU]: ./sql-data-warehouse-overview-what-is.md#data-warehouse-units
[anmode om en kvote forøgelse]: ./sql-data-warehouse-get-started-create-support-ticket.md#request-quota-change 
[Lær, hvordan du overvåge dine forespørgsler]: ./sql-data-warehouse-manage-monitor.md
[Klargøring af instruktioner]: ./sql-data-warehouse-get-started-provision.md
[Konfigurere server firewall adgang til din klient IP-adresse]: ./sql-data-warehouse-get-started-provision.md#create-a-new-azure-sql-server-level-firewall
[SQL Data Warehouse bedste fremgangsmåder]: ./sql-data-warehouse-best-practices.md
[Tabel størrelser]: ./sql-data-warehouse-tables-overview.md#table-size-queries
[Ikke-understøttede tabelfunktioner]: ./sql-data-warehouse-tables-overview.md#unsupported-table-features
[Ikke-understøttede datatyper]: ./sql-data-warehouse-tables-data-types.md#unsupported-data-types
[Overview]: ./sql-data-warehouse-tables-overview.md
[Data types]: ./sql-data-warehouse-tables-data-types.md
[Distribute]: ./sql-data-warehouse-tables-distribute.md
[Index]: ./sql-data-warehouse-tables-index.md
[Partition]: ./sql-data-warehouse-tables-partition.md
[Statistics]: ./sql-data-warehouse-tables-statistics.md
[Temporary]: ./sql-data-warehouse-tables-temporary.md
[Dårlig columnstore indeks kvalitet]: ./sql-data-warehouse-tables-index.md#causes-of-poor-columnstore-index-quality
[Genopbyg indekser for at forbedre kvaliteten af segmentet]: ./sql-data-warehouse-tables-index.md#rebuilding-indexes-to-improve-segment-quality
[Administration af arbejdsbelastning]: ./sql-data-warehouse-develop-concurrency.md
[Brug af CTAS til at løse ikke-understøttede Opdater og Slet syntaks]: ./sql-data-warehouse-develop-ctas.md#using-ctas-to-work-around-unsupported-features
[OPDATERE løsninger]: ./sql-data-warehouse-develop-ctas.md#ansi-join-replacement-for-update-statements
[SLETTE løsninger]: ./sql-data-warehouse-develop-ctas.md#ansi-join-replacement-for-delete-statements
[FLETTE løsninger]: ./sql-data-warehouse-develop-ctas.md#replace-merge-statements
[Lagret procedure begrænsninger]: ./sql-data-warehouse-develop-stored-procedures.md#limitations
[Godkendelse til Azure SQL datawarehouse]: ./sql-data-warehouse-authentication.md
[Arbejde omkring kravet PolyBase UTF-8]: ./sql-data-warehouse-load-polybase-guide.md#working-around-the-polybase-utf-8-requirement

<!--MSDN references-->
[sys.database_principals]: https://msdn.microsoft.com/library/ms187328.aspx
[OPRETTE FUNKTIONEN]: https://msdn.microsoft.com/library/mt203952.aspx
[sqlcmd]: https://azure.microsoft.com/en-us/documentation/articles/sql-data-warehouse-get-started-connect-sqlcmd/

<!--Other Web references-->
[Blogge]: https://azure.microsoft.com/blog/tag/azure-sql-data-warehouse/
[KAT team blogge]: https://blogs.msdn.microsoft.com/sqlcat/tag/sql-dw/
[Anmode om funktioner]: https://feedback.azure.com/forums/307516-sql-data-warehouse
[MSDN-forum]: https://social.msdn.microsoft.com/Forums/home?forum=AzureSQLDataWarehouse
[Stable overløb-forum]: http://stackoverflow.com/questions/tagged/azure-sqldw
[Twitter]: https://twitter.com/hashtag/SQLDW
[Videoer]: https://azure.microsoft.com/documentation/videos/index/?services=sql-data-warehouse

