<properties
    pageTitle="Alle emner til SQL Data Warehouse tjenesten | Microsoft Azure"
    description="Liste over alle emner til Azure-tjenesten med navnet SQL Data Warehouse, der findes på http://azure.microsoft.com/documentation/articles/, titel og beskrivelse."
    services="sql-data-warehouse"
    documentationCenter=""
    authors="barbkess"
    manager="jhubbard"
    editor="MightyPen"/>

<tags
    ms.service="sql-data-warehouse"
    ms.workload="sql-data-warehouse"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/05/2016"
    ms.author="barbkess"/>


# <a name="all-topics-for-azure-sql-data-warehouse-service"></a>Alle emner til Azure SQL Data Warehouse tjeneste

I dette emne beskrives hver emne, der gælder direkte til tjenesten **SQL Data Warehouse** af Azure. Du kan søge denne webside til nøgleord ved hjælp af **Ctrl + F**, søge efter emner af aktuelle interesse.




## <a name="new"></a>Ny

| &nbsp; | Titel | Beskrivelse |
| --: | :-- | :-- |
| 1 | [SQL Data Warehouse sikkerhedskopier](sql-data-warehouse-backups.md) | Få mere at vide om SQL Data Warehouse indbyggede databasesikkerhedskopier, der gør det muligt at gendanne en Azure SQL Data Warehouse til et gendannelsespunkt eller et andet geografisk område. |


## <a name="updated-articles-sql-data-warehouse"></a>Opdaterede artikler, SQL Data Warehouse

I dette afsnit beskrives artikler, som er blevet opdateret for nylig, hvor opdateringen blev stor eller betydeligt. For hver opdaterede artikel vises en grov kodestykke den tilføjede tabsbeløb teksts. Artiklerne er blevet opdateret inden for datointervallet af **2016-08-22** til **2016-10-05**.

| &nbsp; | Artikel | Tekstopdateringer, kodestykke | Opdateret, da |
| --: | :-- | :-- | :-- |
| 2 | [Indlæse data fra Azure blob-lager til SQL Data Warehouse (PolyBase)](sql-data-warehouse-load-from-azure-blob-storage-with-polybase.md) | /-For at spore byte og filer Vælg r.command, s.request_id, r.status, antal (særskilt input_name) som nbr_files, sum (s.bytes_processed) / 1024/1024 som gb_processed fra sys.dm_pdw_exec_requests r indre joinforbindelse sys.dm_pdw_dms_external_work s på r.request_id = s.request_id hvor r. etiket = ' CTAS: Indlæs sikringsofficer. DimProduct ' eller r. etiket = ' CTAS: Indlæs sikringsofficer. FactOnlineSales' GROUP BY r.command s.request_id r.status ORDER BY nbr_files desc, og gb_processed desc;  | 2016-09-07 |
| 3 | [SQL Data Warehouse Gendan](sql-data-warehouse-restore-database-overview.md) | **Kan jeg gendanne et midlertidigt afbrudt datawarehouse?** Hvis du vil gendanne et datalager, der er stoppet midlertidigt, skal du først finde den online igen. Når datawarehouse er igen er online, har du syv dage efter gendannelsespunkter at vælge mellem. **Gendanne til et overflødige geografisk område** Hvis du bruger den geografisk overflødige lagerplads, kan du gendanne datawarehouse til dit parvis datacenter i et andet geografisk område. Datawarehouse genoprettes fra den seneste daglige sikkerhedskopi. **Gendanne tidslinje** Du kan gendanne en database til en hvilken som helst gendannelsespunkt i de seneste syv dage. Snapshots start alle fire til otte timer og er tilgængelige for syv dage. Når et øjebliksbillede er ældre end syv dage, det udløber, og dets gendannelsespunkt er ikke længere tilgængelig. **Gendanne omkostninger** Lagerplads gebyr for gendannede datawarehouse faktureres med Azure Premium lager hastighed. Hvis du holder markøren et gendannede datawarehouse, kan du betaler for lagerplads på Azure Premium lager rente. Fordelen ved afbrydelsen er du ikke er gebyr | 29-09-2016 |





## <a name="get-started"></a>Komme i gang

| &nbsp; | Titel | Beskrivelse |
| --: | :-- | :-- |
| 4 | [Godkendelse til Azure SQL datawarehouse](sql-data-warehouse-authentication.md) | Azure Active Directory (AAD) og SQL Server-godkendelse til Azure SQL Data Warehouse. |
| 5 | [Bedste fremgangsmåder til Azure SQL Data Warehouse](sql-data-warehouse-best-practices.md) | Anbefalinger og bedste fremgangsmåder, du bør vide, som du udvikling af løsninger til Azure SQL Data Warehouse. Disse hjælper dig med at blive fuldført. |
| 6 | [Drivere til Azure SQL datawarehouse](sql-data-warehouse-connection-strings.md) | Forbindelsesstrenge og drivere til SQL Data Warehouse |
| 7 | [Oprette forbindelse til Azure SQL datawarehouse](sql-data-warehouse-connect-overview.md) | Sådan finder du serveren streng navn og forbindelse til din til Azure SQL Data Warehouse |
| 8 | [Analysere data med Azure Machine Learning](sql-data-warehouse-get-started-analyze-with-azure-machine-learning.md) | Bruge Azure maskine læ til at oprette en skønnet maskine læ model, der er baseret på data, der er gemt i Azure SQL Data Warehouse. |
| 9 | [Forespørgsel Azure SQL Data Warehouse (sqlcmd)](sql-data-warehouse-get-started-connect-sqlcmd.md) | Forespørgsler Azure SQL Data Warehouse med sqlcmd kommandolinjeparametre Utility. |
| 10 | [Oprette en SQL Data Warehouse database ved hjælp af Transact-SQL (TSQL)](sql-data-warehouse-get-started-create-database-tsql.md) | Lær, hvordan du opretter en Azure SQL Data Warehouse med TSQL |
| 11 | [Sådan oprettes en supportbilletter til SQL Data Warehouse](sql-data-warehouse-get-started-create-support-ticket.md) | Sådan oprettes en supportbilletter i Azure SQL-datalager. |
| 12 | [Indlæse Data med Azure Data Factory](sql-data-warehouse-get-started-load-with-azure-data-factory.md) | Lær, hvordan du indlæser data med Azure Data Factory |
| 13 | [Indlæse data med PolyBase i SQL Data Warehouse](sql-data-warehouse-get-started-load-with-polybase.md) | Få mere at vide, hvad PolyBase er, og hvornår det skal bruges til opbevaring scenarier-data. |
| 14 | [Oprette en SQL Azure datawarehouse](sql-data-warehouse-get-started-provision.md) | Lær, hvordan du opretter en Azure SQL Data Warehouse i portalen Azure |
| 15 | [Oprette SQL Data Warehouse ved hjælp af PowerShell](sql-data-warehouse-get-started-provision-powershell.md) | Oprette SQL Data Warehouse ved hjælp af PowerShell |
| 16 | [Visualisere data med Power BI](sql-data-warehouse-get-started-visualize-with-power-bi.md) | Visualisere SQL Data Warehouse data med Power BI |
| 17 | [Forespørgsel Azure SQL datawarehouse (Visual Studio)](sql-data-warehouse-query-visual-studio.md) | Forespørgsel SQL datawarehouse med Visual Studio. |



## <a name="develop"></a>Udvikle

| &nbsp; | Titel | Beskrivelse |
| --: | :-- | :-- |
| 18 | [Optimering af transaktioner for SQL Data Warehouse](sql-data-warehouse-develop-best-practices-transactions.md) | Bedste praksis vejledning til at skrive effektiv transaktion opdateringer i Azure SQL Data Warehouse |
| 19 | [På dokumentsammenfald og arbejdsbelastningen management i SQL Data Warehouse](sql-data-warehouse-develop-concurrency.md) | Forstå på dokumentsammenfald og arbejdsbelastningen management i Azure SQL Data Warehouse til udvikling af løsninger. |
| 20 | [Oprette tabel som Vælg (CTAS) i SQL datawarehouse](sql-data-warehouse-develop-ctas.md) | Tip til kodningssprog med tabellen Opret som select-sætning (CTAS) i Azure SQL Data Warehouse til udvikling af løsninger. |
| 21 | [Dynamisk SQL i SQL datawarehouse](sql-data-warehouse-develop-dynamic-sql.md) | Tip til brug af dynamisk SQL i Azure SQL Data Warehouse til udvikling af løsninger. |
| 22 | [Gruppere efter indstillinger i SQL Data Warehouse](sql-data-warehouse-develop-group-by-options.md) | Tip til brug af gruppe af indstillinger i Azure SQL Data Warehouse til udvikling af løsninger. |
| 23 | [Bruge etiketter til instrument forespørgsler i SQL Data Warehouse](sql-data-warehouse-develop-label.md) | Tip til brug af navne til instrument forespørgsler i Azure SQL Data Warehouse til udvikling af løsninger. |
| 24 | [Løkker i SQL datawarehouse](sql-data-warehouse-develop-loops.md) | Tip til Transact-SQL løkker og erstatte markører i Azure SQL Data Warehouse til udvikling af løsninger. |
| 25 | [Lagrede procedurer i SQL Data Warehouse](sql-data-warehouse-develop-stored-procedures.md) | Tip til brug af lagrede procedurer i Azure SQL Data Warehouse til udvikling af løsninger. |
| 26 | [Transaktioner i SQL datawarehouse](sql-data-warehouse-develop-transactions.md) | Tip til brug af transaktioner i Azure SQL Data Warehouse til udvikling af løsninger. |
| 27 | [Brugerdefinerede skemaer i SQL Data Warehouse](sql-data-warehouse-develop-user-defined-schemas.md) | Tip til brug af Transact-SQL skemaer i Azure SQL Data Warehouse til udvikling af løsninger. |
| 28 | [Tildele variabler i SQL Data Warehouse](sql-data-warehouse-develop-variable-assignment.md) | Tip til at tildele Transact-SQL variabler i Azure SQL Data Warehouse til udvikling af løsninger. |
| 29 | [Visninger i SQL datawarehouse](sql-data-warehouse-develop-views.md) | Tip til brug af Transact-SQL-visninger i Azure SQL Data Warehouse til udvikling af løsninger. |
| 30 | [Designbeslutninger og koder teknikker til SQL Data Warehouse](sql-data-warehouse-overview-develop.md) | Udvikling begreber, designbeslutninger, anbefalinger og koder teknikker til SQL Data Warehouse. |



## <a name="manage"></a>Administrere

| &nbsp; | Titel | Beskrivelse |
| --: | :-- | :-- |
| 31 | [Administrere Beregn power i Azure SQL Data Warehouse (oversigt)](sql-data-warehouse-manage-compute-overview.md) | Ydeevnen Skaler ud-funktioner i Azure SQL Data Warehouse. Skalere ud ved at justere DWUs eller stoppe og fortsætte Beregn ressourcer for at gemme omkostninger. |
| 32 | [Administrere Beregn power i Azure SQL Data Warehouse (Azure portal)](sql-data-warehouse-manage-compute-portal.md) | Azure portalen opgaver til at administrere beregne power. Skala beregne ressourcer ved at justere DWUs. Eller pause og CV beregne ressourcer for at gemme omkostninger. |
| 33 | [Administrere Beregn power i Azure SQL Data Warehouse (PowerShell)](sql-data-warehouse-manage-compute-powershell.md) | PowerShell opgaver til at administrere beregne power. Skala beregne ressourcer ved at justere DWUs. Eller pause og CV beregne ressourcer for at gemme omkostninger. |
| 34 | [Administrere Beregn power i Azure SQL Data Warehouse (RESTEN)](sql-data-warehouse-manage-compute-rest-api.md) | PowerShell opgaver til at administrere beregne power. Skala beregne ressourcer ved at justere DWUs. Eller pause og CV beregne ressourcer for at gemme omkostninger. |
| 35 | [Administrere Beregn power i Azure SQL Data Warehouse (T-SQL)](sql-data-warehouse-manage-compute-tsql.md) | Transact-SQL (T-SQL) opgaver til skalering af ydeevne ved at justere DWUs. Gem omkostninger ved at skalere tilbage i top-perioder. |
| 36 | [Overvåge arbejdsbelastningen ved hjælp af DMVs](sql-data-warehouse-manage-monitor.md) | Lær at overvåge arbejdsbelastningen ved hjælp af DMVs. |
| 37 | [Administrer databaser i Azure SQL Data Warehouse](sql-data-warehouse-overview-manage.md) | Oversigt over administration af SQL Data Warehouse databaser. Indeholder værktøjer til administration, DWUs og skalering af ydeevne, fejlfinding i forbindelse med forespørgselsydelse, om oprettelse af god sikkerhedspolitikker og gendanne en database fra databeskadigelse eller fra en internationale afbrydelse. |
| 38 | [Overvåge brugerforespørgsler i Azure SQL Data Warehouse](sql-data-warehouse-overview-manage-user-queries.md) | Oversigt over de overvejelser, bedste fremgangsmåder og opgaver til at overvåge brugerforespørgsler i Azure SQL Data Warehouse |
| 39 | [SQL Data Warehouse Gendan](sql-data-warehouse-restore-database-overview.md) | Oversigt over databaseindstillinger Gendan for at gendanne en database i Azure SQL Data Warehouse. |
| 40 | [Gendanne en SQL Azure datawarehouse (Portal)](sql-data-warehouse-restore-database-portal.md) | Azure portalen opgaver for at gendanne en Azure SQL Data Warehouse. |
| 41 | [Gendanne en SQL Azure datawarehouse (PowerShell)](sql-data-warehouse-restore-database-powershell.md) | PowerShell opgaver for at gendanne en Azure SQL Data Warehouse. |
| 42 | [Gendanne en SQL Azure datawarehouse (REST-API)](sql-data-warehouse-restore-database-rest-api.md) | REST-API opgaver for at gendanne en Azure SQL Data Warehouse. |



## <a name="tables-and-indexes"></a>Tabeller og indeks

| &nbsp; | Titel | Beskrivelse |
| --: | :-- | :-- |
| 43 | [Datatyper for tabeller i SQL datalager](sql-data-warehouse-tables-data-types.md) | Introduktion til datatyper for Azure SQL Data Warehouse tabeller. |
| 44 | [Distribuere tabeller i SQL datalager](sql-data-warehouse-tables-distribute.md) | Introduktion til distribution af tabeller i Azure SQL-datalager. |
| 45 | [Indeksering tabeller i SQL datalager](sql-data-warehouse-tables-index.md) | Introduktion til tabel indeksering i Azure SQL Data Warehouse. |
| 46 | [Oversigt over tabeller i SQL datalager](sql-data-warehouse-tables-overview.md) | Introduktion til Azure SQL Data Warehouse tabeller. |
| 47 | [Partitionering tabeller i SQL datalager](sql-data-warehouse-tables-partition.md) | Introduktion til tabel partitionering i Azure SQL-datalager. |
| 48 | [Administration af statistik på tabeller i SQL datalager](sql-data-warehouse-tables-statistics.md) | Introduktion til statistik på tabeller i Azure SQL-datalager. |
| 49 | [Midlertidige tabeller i SQL datalager](sql-data-warehouse-tables-temporary.md) | Introduktion til midlertidige tabeller i Azure SQL-datalager. |



## <a name="integrate"></a>Integrere

| &nbsp; | Titel | Beskrivelse |
| --: | :-- | :-- |
| 50 | [Bruge Azure Data Factory med SQL datawarehouse](sql-data-warehouse-integrate-azure-data-factory.md) | Tip til brug af Azure Data Factory (ADF) med Azure SQL Data Warehouse til udvikling af løsninger. |
| 51 | [Brug Azure Machine Learning med SQL datawarehouse](sql-data-warehouse-integrate-azure-machine-learning.md) | Selvstudium til brug af Azure maskine Learning med Azure SQL Data Warehouse til udvikling af løsninger. |
| 52 | [Bruge Azure Stream Analytics med SQL datawarehouse](sql-data-warehouse-integrate-azure-stream-analytics.md) | Tip til brug af Azure Stream Analytics med Azure SQL Data Warehouse til udvikling af løsninger. |
| 53 | [Bruge Power BI med SQL datawarehouse](sql-data-warehouse-integrate-power-bi.md) | Tip til brug af Power BI med Azure SQL Data Warehouse til udvikling af løsninger. |
| 54 | [Udnytte andre tjenester med SQL Data Warehouse](sql-data-warehouse-overview-integrate.md) | Værktøjer og partnere med løsninger, der integreres med SQL Data Warehouse.  |



## <a name="load"></a>Indlæsning

| &nbsp; | Titel | Beskrivelse |
| --: | :-- | :-- |
| 55 | [Indlæse data fra Azure blob-lager til Azure SQL Data Warehouse (Azure Data Factory)](sql-data-warehouse-load-from-azure-blob-storage-with-data-factory.md) | Lær, hvordan du indlæser data med Azure Data Factory |
| 56 | [Indlæse data fra Azure blob-lager til SQL Data Warehouse (PolyBase)](sql-data-warehouse-load-from-azure-blob-storage-with-polybase.md) | Lær, hvordan du bruger PolyBase til at indlæse data fra Azure blob-lager til SQL Data Warehouse. Indlæse et par tabeller fra offentlige data i Contoso detailsalg Data Warehouse skemaet. |
| 57 | [Indlæse data fra SQL Server til Azure SQL Data Warehouse (AZCopy)](sql-data-warehouse-load-from-sql-server-with-azcopy.md) | Bruger bcp til at eksportere data fra SQL Server til flad filer, AZCopy til at importere data til Azure blob-lager og PolyBase til indtager dataene til Azure SQL Data Warehouse. |
| 58 | [Indlæse data fra SQL Server til Azure SQL Data Warehouse (flad filer)](sql-data-warehouse-load-from-sql-server-with-bcp.md) | Til en lille datastørrelse, du bruger bcp til at eksportere data fra SQL Server til flade filer og importere dataene direkte til Azure SQL Data Warehouse. |
| 59 | [Indlæse data fra SQL Server til Azure SQL Data Warehouse (SSIS)](sql-data-warehouse-load-from-sql-server-with-integration-services.md) | Viser, hvordan du opretter en SQL Server Integration Services (SSIS) pakke for at flytte data fra en lang række datakilder til SQL Data Warehouse. |
| 60 | [Indlæse data med PolyBase i SQL Data Warehouse](sql-data-warehouse-load-from-sql-server-with-polybase.md) | Bruger bcp til at eksportere data fra SQL Server til flad filer, AZCopy til at importere data til Azure blob-lager og PolyBase til indtager dataene til Azure SQL Data Warehouse. |
| 61 | [Vejledning til brug af PolyBase i SQL Data Warehouse](sql-data-warehouse-load-polybase-guide.md) | Retningslinjer for og anbefalinger til brug af PolyBase i SQL Data Warehouse scenarier. |
| 62 | [Indlæse eksempeldata i SQL Data Warehouse](sql-data-warehouse-load-sample-databases.md) | Indlæse eksempeldata i SQL Data Warehouse |
| 63 | [Indlæse data med bcp](sql-data-warehouse-load-with-bcp.md) | Få mere at vide, hvilke bcp er, og hvornår det skal bruges til opbevaring scenarier-data. |
| 64 | [Indlæse data til Azure SQL Data Warehouse](sql-data-warehouse-overview-load.md) | Lær de almindelige scenarier for indlæsning i SQL Data Warehouse af data. Dette omfatter ved hjælp af PolyBase, Azure blob-lager, flade filer og disk levering. Du kan også bruge tredjepartsværktøjer. |



## <a name="migrate"></a>Overføre

| &nbsp; | Titel | Beskrivelse |
| --: | :-- | :-- |
| 65 | [Overføre dine SQL-koden til SQL Data Warehouse](sql-data-warehouse-migrate-code.md) | Tip til at overføre dine SQL-koden til Azure SQL Data Warehouse til udvikling af løsninger. |
| 66 | [Overføre dine Data](sql-data-warehouse-migrate-data.md) | Tip til at overføre dine data til Azure SQL Data Warehouse til udvikling af løsninger. |
| 67 | [Data Warehouse Migration Utility (Preview)](sql-data-warehouse-migrate-migration-utility.md) | Overføre til SQL datawarehouse. |
| 68 | [Overføre dit skema til SQL Data Warehouse](sql-data-warehouse-migrate-schema.md) | Tip til at overføre dit skema til Azure SQL Data Warehouse til udvikling af løsninger. |
| 69 | [Overføre din løsning til SQL Data Warehouse](sql-data-warehouse-overview-migrate.md) | Overførsel vejledning til hvilket din løsning på Azure SQL Data Warehouse platform. |



## <a name="partners"></a>Partnere

| &nbsp; | Titel | Beskrivelse |
| --: | :-- | :-- |
| 70 | [SQL Data Warehouse business intelligence partnere](sql-data-warehouse-partner-business-intelligence.md) | Lister over tredjeparters business intelligence partnere med løsninger, der understøtter SQL Data Warehouse. |
| 71 | [SQL Data Warehouse data integration partnere](sql-data-warehouse-partner-data-integration.md) | Lister over fra tredjepart partnere med løsninger til integrering af data, der understøtter Azure SQL Data Warehouse. |
| 72 | [SQL Data Warehouse data management partnere](sql-data-warehouse-partner-data-management.md) | Lister over fra tredjepart data management partnere med løsninger, der understøtter SQL Data Warehouse. |



## <a name="reference"></a>Reference

| &nbsp; | Titel | Beskrivelse |
| --: | :-- | :-- |
| 73 | [Referenceemner i til SQL Data Warehouse](sql-data-warehouse-overview-reference.md) | Reference indhold links til SQL Data Warehouse. |
| 74 | [PowerShell-cmdletter og REST API'er til SQL Data Warehouse](sql-data-warehouse-reference-powershell-cmdlets.md) | Find den øverste PowerShell-cmdlet'er til Azure SQL Data Warehouse, herunder hvordan du midlertidigt afbryde og fortsætte en database. |
| 75 | [Sprogelementer](sql-data-warehouse-reference-tsql-language-elements.md) | Liste over links til oplysninger om indhold til de Transact-SQL sprogelementer, der bruges til SQL Data Warehouse. |
| 76 | [Emner i Transact-SQL](sql-data-warehouse-reference-tsql-statements.md) | Links til oplysninger om indhold til i Transact-SQL-emner, der bruges af SQL Data Warehouse. |
| 77 | [Systemvisninger](sql-data-warehouse-reference-tsql-system-views.md) | Links til system visninger indhold til SQL Data Warehouse. |



## <a name="security"></a>Sikkerhed

| &nbsp; | Titel | Beskrivelse |
| --: | :-- | :-- |
| 78 | [SQL datawarehouse - ældre klienter support til overvågning og dynamiske Data Masking](sql-data-warehouse-auditing-downlevel-clients.md) | Få mere at vide om understøttelse af SQL Data Warehouse bagudkompatible klienter til overvågning af data |
| 79 | [Overvågning i Azure SQL datawarehouse](sql-data-warehouse-auditing-overview.md) | Introduktion til overvågning i Azure SQL Data Warehouse |
| 80 | [Komme i gang med gennemsigtig Data kryptering (TDE) i SQL Data Warehouse](sql-data-warehouse-encryption-tde.md) | Gennemsigtige datakryptering (TDE) i SQL datawarehouse |
| 81 | [Komme i gang med gennemsigtig Data kryptering (TDE)](sql-data-warehouse-encryption-tde-tsql.md) | Gennemsigtige datakryptering (TDE) i SQL datawarehouse (T-SQL) |
| 82 | [Sikre en database i SQL Data Warehouse](sql-data-warehouse-overview-manage-security.md) | Tip til sikring af en database i Azure SQL Data Warehouse til udvikling af løsninger. |



## <a name="miscellaneous"></a>Diverse

| &nbsp; | Titel | Beskrivelse |
| --: | :-- | :-- |
| 83 | [Installere Visual Studio-2015 og SSDT til SQL datawarehouse](sql-data-warehouse-install-visual-studio.md) | Installere Visual Studio og SQL Server Development Tools (SSDT) til Azure SQL datawarehouse |
| 84 | [Overførsel til Premium lagerplads detaljer](sql-data-warehouse-migrate-to-premium-storage.md) | Vejledning til at overføre en eksisterende SQL Data Warehouse til premium-lager |
| 85 | [Komme i gang med truslen registrering](sql-data-warehouse-security-threat-detection.md) | Hvordan du kan komme i gang med truslen registrering |
| 86 | [SQL Data Warehouse kapacitet grænser](sql-data-warehouse-service-capacity-limits.md) | Maksimale værdier for forbindelser, databaser, tabeller og forespørgsler til SQL Data Warehouse. |
| 87 | [Fejlfinding i forbindelse med Azure SQL datawarehouse](sql-data-warehouse-troubleshoot.md) | Fejlfinding i forbindelse med Azure SQL datawarehouse. |

