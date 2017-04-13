<properties
   pageTitle="Udforske Azure SQL Database selvstudier | Microsoft Azure"
   description="Få mere at vide om SQL-Database funktioner og egenskaber"
   keywords=""
   services="sql-database"
   documentationCenter=""
   authors="CarlRabeler"
   manager="jhubbard"
   editor=""/>

<tags
   ms.service="sql-database"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-management"
   ms.date="08/24/2016"
   ms.author="carlrab"/>
   
# <a name="explore-azure-sql-database-tutorials"></a>Udforske Azure SQL Database selvstudier

Nedenstående link fører dig til en oversigt over hver vises funktionsområder og et enkelt trin ved start selvstudium for hvert område. Løsning-fastsat Hurtig start, der viser, se brug af SQL-Database i en komplet løsning, der er baseret på virkelighedens scenarier [Azure SQL Database løsning hurtig starter](sql-database-solution-quick-starts.md).

## <a name="using-sql-server-management-studio"></a>Brug af SQL Server Management Studio

I følgende selvstudier lærer du om brug af SQL Server Management Studio til at administrere og forespørge Azure SQL-Database.


> [AZURE.IMPORTANT] Det anbefales, at du altid bruger den nyeste version af Management Studio skal være synkroniseret med opdateringer til Microsoft Azure og SQL-Database. [Opdatere SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290.aspx).


| Selvstudium  | Beskrivelse  |
|---|---|---|
| [Oprette forbindelse til Azure SQL-Database ved hjælp af et serverniveau vigtigste logon](sql-database-get-started-security.md#connect-to-azure-sql-database-using-a-server-level-principal-login)| I dette selvstudium lærer du at oprette forbindelse til Azure SQL-Database ved hjælp af et serverniveau vigtigste logon.|
| [Oprette forbindelse til Azure SQL-Database som en bruger](sql-database-get-started-security.md#connect-to-azure-sql-database-as-a-user) | I dette selvstudium lærer du, hvordan du opretter forbindelse til en Azure SQL-database ved hjælp af en database på brugerniveau brugerkonto.|
||||

## <a name="elastic-pools"></a>Elastiske grupper

I følgende selvstudier lærer du om brug af [elastiske grupper](sql-database-elastic-pool.md) til at administrere ydeevne mål for flere databaser, der har mange forskellige og uventede brugsmønstre.

| Selvstudium  | Beskrivelse  |
|---|---|---|
| [Oprette en elastiske puljen](sql-database-elastic-pool-create-portal.md) | I dette selvstudium lærer du at oprette en SVG samling af Azure SQL-databaser. |
| [Overvåge en elastiske database](sql-database-elastic-pool-manage-portal.md#elastic-database-monitoring) | I dette selvstudium lærer du at overvåge en individuelle elastiske database til potentielle problemer. |
| [Tilføje en besked til en ressource, puljen](sql-database-elastic-pool-manage-portal.md#add-an-alert-to-a-pool-resource) | I dette selvstudium lærer du at tilføje regler til ressourcer, som sender mails til en person eller beskeder om strenge til URL-adressen slutpunkter, når ressourcen rammer en grænseværdi for anvendelsen, du har angivet. |
| [Flytte en database til en elastiske puljen](sql-database-elastic-pool-manage-portal.md#move-a-database-into-an-elastic-pool) | I dette selvstudium lærer du at flytte en database til en elastiske puljen. |
| [Flytte en database fra en elastiske puljen](sql-database-elastic-pool-manage-portal.md#move-a-database-out-of-an-elastic-pool) | I dette selvstudium lærer du at flytte en database fra en elastiske puljen. |
| [Ændre indstillinger for ydelse af en samling](sql-database-elastic-pool-manage-portal.md#change-performance-settings-of-a-pool) | I dette selvstudium lærer du at justere ydeevne og lagerplads begrænsninger for en gruppe. |
||||

## <a name="elastic-database-jobs"></a>Elastiske database job

I følgende selvstudier lærer du om brug af [elastiske databasen job](sql-database-elastic-jobs-overview.md).

| Selvstudium  | Beskrivelse  |
|---|---|---|
| [Komme i gang med elastiske Databaseværktøjer](sql-database-elastic-scale-get-started.md) | I dette selvstudium lærer du at bruge funktionerne i elastiske Databaseværktøjer ved hjælp af en simpel delt program. |
| [Introduktion til Azure SQL-Database elastiske job](sql-database-elastic-jobs-getting-started.md)  | I dette selvstudium lærer du, hvordan til at oprette og administrere job, administrere en gruppe af relaterede databaser.  | 
||||

## <a name="elastic-queries"></a>Elastiske forespørgsler

I følgende selvstudier lærer du om at køre [elastiske forespørgsler](sql-database-elastic-query-overview.md). 

| Selvstudium  | Beskrivelse  |
|---|---|---|
| [Forespørgsler på tværs af en vandret partitioneret (delt) database)](sql-database-elastic-query-getting-started.md) | I dette selvstudium lærer du at oprette rapporter ud fra alle databaser i en vandret partitioneret (delt) database ved hjælp af [elastiske forespørgsel](sql-database-elastic-query-overview.md) |
| [Forespørgsler på tværs af en lodret partitioneret database)](sql-database-elastic-query-getting-started-vertical.md#create-database-objects) | I dette selvstudium lærer du at oprette rapporter ud fra alle databaser i en lodret database ved hjælp af [elastiske forespørgsel](sql-database-elastic-query-overview.md) |
| [Overføre en eksisterende database til skala ud](sql-database-elastic-convert-to-use-elastic-tools.md)| I dette selvstudium lærer du vandret skalere (shard) en Azure SQL-database. |
||||

## <a name="performance-optimization"></a>Optimering af ydeevne

I følgende selvstudier lærer du om optimering af [ydeevne over enkelt databaser](sql-database-performance-guidance.md). Se [elastiske grupper](#elastic-pools)til optimering af ydeevnen for flere databaser.

| Selvstudium  | Beskrivelse  |
|---|---|---|
| [Ændre niveauet for tjenesten niveau og ydeevnen af databasen](sql-database-scale-up.md#change-the-service-tier-and-performance-level-of-your-database) | I dette selvstudium lærer du at skalere eller indskrænke ydeevnen for Azure SQL-database ved hjælp af tjenesten niveauer. |
| [SQL-Database Advisor forespørgsel ydeevne indsigt](sql-database-performance.md#performance-overview) | I dette selvstudium lærer du at åbne og bruge SQL-Database Advisor forespørgsel ydeevne indsigt.|
| [SQL-Database Advisor ydeevne anbefalinger](sql-database-advisor.md#viewing-recommendations) | I dette selvstudium lærer du at få vist og anvende SQL Database Advisor ydeevne anbefalinger. |
| [Gennemse øverste CPU forbrug forespørgsler](sql-database-query-performance.md#review-top-cpu-consuming-queries)| I dette selvstudium lærer du at bruge SQL-Database Advisor forespørgsel ydeevne indsigt til at gennemse øverste CPU forbrug forespørgsler.|
| [Få vist oplysninger om individuelle forespørgsel](sql-database-query-performance.md#viewing-individual-query-details)| I dette selvstudium lærer du at bruge SQL-Database Advisor forespørgsel ydeevne indsigt til at få vist oplysninger om ydeevne i individuelle forespørgsel.|
||||

## <a name="sql-database-migration-and-archive"></a>SQL-Database overførsel og arkiv 

I følgende selvstudier lærer du om [overførsel af en eksisterende SQL Server-database til Azure SQL-Database](sql-database-cloud-migrate.md).

| Selvstudium  | Beskrivelse  |
|---|---|---|
| [Registrering af kompatibilitetsproblemer med SQL Server Data Tools til Visual Studio](sql-database-cloud-migrate-fix-compatibility-issues-ssdt.md#detecting-compatibility-issues-using-sql-server-data-tools-for-visual-studio) | I dette selvstudium lærer du at bruge SQL Server Data Tools til Visual Studio til at bestemme Azure SQL-Database kompatibilitet. |
| [Løse kompatibilitetsproblemer med SQL Server Data Tools til Visual Studio](sql-database-cloud-migrate-fix-compatibility-issues-ssdt#fixing-compatibility-issues-using-sql-server-data-tools-for-visual-studio) | I dette selvstudium lærer du at bruge SQL Server Data Tools til Visual Studio til at løse problemer med Azure SQL-Database. |
| [Find ud af SQL-Database kompatibelt ved hjælp af SqlPackage.exe](sql-database-cloud-migrate-determine-compatibility-sqlpackage.md#using-sqlpackageexe) | I dette selvstudium lærer du at bruge værktøjet SQLPackage.exe kommandolinjen til at bestemme Azure SQL-Database kompatibilitet.|
| [Find ud af SQL-Database kompatibelt ved hjælp af SSMS](sql-database-cloud-migrate-determine-compatibility-ssms.md#using-sql-server-management-studio) |I dette selvstudium lærer du at bruge SQL Server Management Studio til at bestemme Azure SQL-Database kompatibilitet.|
| [Overføre SQL Server-database til SQL-Database ved hjælp af installere Database til guiden Microsoft Azure-Database](sql-database-cloud-migrate-compatible-using-ssms-migration-wizard.md#use-the-deploy-database-to-microsoft-azure-database-wizard) | I dette selvstudium lærer du, hvordan du kan overføre en kompatible SQL Server-database til Azure SQL-Database ved hjælp af installere databasen til guiden Microsoft Azure-Database i SQL Server Management Studio.
| [Eksportere en SQL Server-database til en BACPAC-fil ved hjælp af SSMS](sql-database-cloud-migrate-compatible-export-bacpac-ssms.md) | I dette selvstudium lærer du, hvordan du kan eksportere en kompatible SQL Server-database til en BACPAC fil ved hjælp af Data niveau programmet guiden Eksporter i SQL Server Management Studio.|
| [Eksportere en SQL Server-database til en BACPAC fil ved hjælp af SqlPackage](sql-database-cloud-migrate-compatible-export-bacpac-sqlpackage.md) | I dette selvstudium lærer du, hvordan du kan eksportere en kompatible SQL Server-database til en BACPAC fil ved hjælp af værktøjet SQLPackage.exe kommandolinjen.|
| [Importere en BACPAC-fil til Azure SQL-Database ved hjælp af SSMS](sql-database-cloud-migrate-compatible-import-bacpac-ssms.md) | I dette selvstudium lærer du, hvordan du importerer en database til Azure SQL-Database fra en BACPAC-fil ved hjælp af Data niveau program guiden Eksporter i SQL Server Management Studio. |
| [Importere en BACPAC-fil til Azure SQL-Database ved hjælp af SqlPackage](sql-database-cloud-migrate-compatible-import-bacpac-sqlpackage.md#import-from-a-bacpac-file-into-azure-sql-database-using-sqlpackage) | I dette selvstudium lærer du, hvordan du importerer en database til Azure SQL-Database fra en BACPAC fil ved hjælp af værktøjet SQLPackage kommandolinjen. |
| [Importere en BACPAC-fil til Azure SQL-Database ved hjælp af portalen Azure](sql-database-import.md) | I dette selvstudium lærer du, hvordan du importerer en database til Azure SQL-Database fra en BACPAC-fil, der er gemt i en Azure blob ved hjælp af portalen Azure.|
| [Importere en BACPAC-fil til Azure SQL-Database ved hjælp af PowerShell](sql-database-import-powershell.md) | I dette selvstudium lærer du, hvordan du importerer en database til Azure SQL-Database fra en BACPAC fil ved hjælp af PowerShell.|
| [Arkivere Azure SQL-database ved hjælp af portalen Azure](sql-database-export.md#export-your-database) | I dette selvstudium lærer du at arkivere Azure SQL-database til en BACPAC-fil ved hjælp af portalen Azure. |
| [Arkivere Azure SQL-database ved hjælp af PowerShell](sql-database-export-powershell.md) | I dette selvstudium lærer du at arkivere Azure SQL-database til en BACPAC fil ved hjælp af PowerShell. |
| [Kopiere en Azure SQL-database ved hjælp af portalen Azure](sql-database-copy.md#copy-your-sql-database) | I dette selvstudium lærer du at kopiere en Azure SQL-database ved hjælp af portalen Azure. |
| [Kopiere en Azure SQL-database ved hjælp af PowerShell](sql-database-copy-powershell#copy-your-sql-database) | I dette selvstudium lærer du at kopiere en Azure SQL-database ved hjælp af PowerShell. |
| [Kopiere en ved hjælp af Transact-SQL Azure SQL-database](sql-database-copy-transact-sql.md#copy-your-sql-database) | I dette selvstudium lærer du at kopiere en Azure SQL-database ved hjælp af Transact-SQL. |
||||

##<a name="develop"></a>Udvikle

I følgende selvstudier lærer du om [SQL databaseudvikling](sql-database-develop-overview.md) og brug af [connectivity biblioteker](sql-database-libraries.md).

| Selvstudium  | Beskrivelse  |
|---|---|---|
| [Oprette forbindelse til SQL-Database ved hjælp af .NET (C#)](sql-database-develop-dotnet-simple.md) | I dette selvstudium lærer du at oprette forbindelse til Azure SQL-database ved hjælp af C#. |
| [Oprette forbindelse til SQL-Database ved hjælp af Java](sql-database-develop-java-simple.md) | I dette selvstudium lærer du at oprette forbindelse til en Azure SQL-database ved hjælp af Java. |
| [Oprette forbindelse til SQL-Database ved hjælp af Node.js](sql-database-develop-nodejs-simple.md) | I dette selvstudium lærer du at oprette forbindelse til en Azure SQL-database ved hjælp af Node.js. |
| [Oprette forbindelse til SQL-Database ved hjælp af PHP](sql-database-develop-php-simple.md) | I dette selvstudium lærer du at oprette forbindelse til en Azure SQL-database ved hjælp af PHP. |
| [Oprette forbindelse til SQL-Database ved hjælp af Python](sql-database-develop-python-simple.md) | I dette selvstudium lærer du at oprette forbindelse til en Azure SQL-database ved hjælp af Python. |
| [Oprette forbindelse til SQL-Database ved hjælp af fonetisk](sql-database-develop-ruby-simple.md) | I dette selvstudium lærer du at oprette forbindelse til en Azure SQL-database ved hjælp af fonetisk. |
||||
 
## <a name="database-access"></a>Access database

I følgende selvstudier lærer du om [oprettelse og administration af logon og brugere](sql-database-manage-logins.md).

| Selvstudium  | Beskrivelse  |
|---|---|---|
| [Oprette en regel for Azure SQL-Database serverniveau firewall ved hjælp af portalen Azure](sql-database-configure-firewall-settings.md)  | I dette selvstudium lærer du at konfigurere ved hjælp af portalen Azure SQL-Database serverniveau firewall.  |
| [Oprette en database niveau firewallregel ved hjælp af Transact-SQL](sql-database-configure-firewall-settings-tsql.md#database-level-firewall-rules) | I dette selvstudium lærer du, hvordan du opretter en database niveau firewallregel ved hjælp af Transact-SQL.|
| [Administrere serverniveau firewallregler ved hjælp af Transact-SQL](sql-database-configure-firewall-settings-tsql.md#manage-server-level-firewall-rules-through-transact-sql) | I dette selvstudium lærer du, hvordan du administrerer en Azure SQL-Database serverniveau firewall, der bruger Transact-SQL.|
| [Administrere serverniveau firewallregler ved hjælp af PowerShell](sql-database-configure-firewall-settings-powershell.md#manage-firewall-rules-using-powershell) | I dette selvstudium lærer du, hvordan du administrerer en Azure SQL-Database serverniveau firewall, ved hjælp af PowerShell.|
| [Administrere serverniveau firewallregler ved hjælp af REST-API](sql-database-configure-firewall-settings-rest.md#manage-firewall-rules-using-the-service-management-rest-api) | I dette selvstudium lærer du, hvordan du administrerer en Azure SQL-Database serverniveau firewall, der bruger API NULSTILLE.|
| [Oprette forbindelse til Azure SQL-Database ved hjælp af et serverniveau vigtigste logon](sql-database-get-started-security.md#connect-to-azure-sql-database-using-a-server-level-principal-login)| I dette selvstudium lærer du at oprette forbindelse til Azure SQL-Database ved hjælp af et serverniveau vigtigste logon.|
| [Give databaseadgang til et logon] (sql-database-manage-logins.md#granting-database-access-to-a-login() | I dette selvstudium lærer du at give adgang til databasen til et serverniveau logon.|
| [Oprette nye databasebruger ved hjælp af SSMS](sql-database-get-started-security.md#create-new-database-user-using-ssms) | I dette selvstudium lærer du at oprette en ny databasebruger i en eksisterende database ved hjælp af SSMS.|
| [Give nye database db_owner brugertilladelser](sql-database-get-started-security.md#grant-new-database-user-dbowner-permissions) | I dette selvstudium lærer du at give en eksisterende database db_owner brugertilladelser.|
| [Oprette forbindelse til Azure SQL-Database som en bruger](sql-database-get-started-security.md#connect-to-azure-sql-database-as-a-user) | I dette selvstudium lærer du at oprette forbindelse til en Azure SQL-database ved hjælp af en database på brugerniveau brugerkonto.|
||||


## <a name="data-security"></a>Datasikkerhed

I følgende selvstudier lærer du om [sikring af data til Azure SQL-Database](sql-database-security.md). 

| Selvstudium  | Beskrivelse  |
|---|---|---|
| [Aktivere registrering af truslen for databasen ved hjælp af portalen Azure](sql-database-threat-detection-get-started.md#set-up-threat-detection-for-your-database) | I dette selvstudium lærer du at konfigurere truslen registrering i portalen Azure for din database.|
| [Secure Store og små bogstaver data uisng altid krypteret](sql-database-always-encrypted-azure-key-vault.md) | I dette selvstudium skal du bruge guiden altid krypteret til at sikre følsomme oplysninger i en Azure SQL-database.|
| [Sikker senstive data ved hjælp af kryptering af gennemsigtig data](https://msdn.microsoft.com/library/dn948096.aspx)| I dette selvstudium lærer du at bruge kryptering af gennemsigtig data til at sikre senstive data.|
| [Kryptere en kolonne med data](https://msdn.microsoft.com/library/ms179331.aspx)| I dette selvstudium lærer du at kryptere en kolonne med data ved hjælp af Transact-SQL.|
| [Konfigurere dynamiske data Maskering](sql-database-dynamic-data-masking-get-started.md#set-up-dynamic-data-masking-for-your-database-using-the-azure-portal)  | I dette selvstudium lærer du at konfigurere dynamiske data Maskering til Azure SQL-database. |
||||

## <a name="business-continuity-and-query-scale-out"></a>Forretningskontinuitet og forespørgsel skala ud

I følgende selvstudier, vil du lære at bruge [geografisk-Gendan og aktive geografisk-gentagelse](sql-database-business-continuity.md) til reccover fra fejl, Forretningskontinuitet og forespørgsel skala ud.

| Selvstudium  | Beskrivelse  |
|---|---|---|
| [Gendanne en Azure SQL-Database til et tidligere tidspunkt ved hjælp af Azure-Portal](sql-database-point-in-time-restore-portal.md)| I dette selvstudium lærer du at gendanne databasen til et tidligere tidspunkt ved hjælp af portalen Azure.|
| [Gendanne en Azure SQL-Database til et tidligere tidspunkt med PowerShell](sql-database-point-in-time-restore-powershell.md) | I dette selvstudium lærer du at gendanne databasen til et tidligere tidspunkt ved hjælp af PowerShell|
| [Gendanne en slettet Azure SQL-database ved hjælp af portalen Azure](sql-database-restore-deleted-database-portal.md) | I dette selvstudium lærer du at gendanne en slettet database ved hjælp af portalen Azure.|
| [Gendanne en slettet Azure SQL-database ved hjælp af PowerShell](sql-database-restore-deleted-database-powershell.md) | I dette selvstudium lærer du at gendanne en slettet database ved hjælp af PowerShell.|
| [Konfigurere geografisk gentagelse til Azure SQL-Database ved hjælp af portalen Azure](sql-database-geo-replication-portal.md)| I dette selvstudium lærer du at konfigurere aktive geografisk-replikering ved hjælp af portalen Azure.|
| [Konfigurere geografisk gentagelse til Azure SQL-Database ved hjælp af PowerShell](sql-database-geo-replication-powershell.md)| I dette selvstudium lærer du at konfigurere aktive geografisk replikering ved hjælp af PowerShell.|
| [Konfigurere geografisk gentagelse til Azure SQL-Database ved hjælp af Transact-SQL](sql-database-geo-replication-transact-sql.md)| I dette selvstudium lærer du at konfigurere aktive geografisk replikering ved hjælp af Transact-SQL.|
| [Starte en planlagt eller uventet failover for Azure SQL-Database ved hjælp af portalen Azure](sql-database-geo-replication-failover-portal.md) | I dette selvstudium lærer du, hvordan Sådan flytter du til en geografisk replikerede sekundær replika, som ved hjælp af portalen Azure.|
| [Starte en planlagt eller uventet failover for Azure SQL-Database ved hjælp af PowerShell](sql-database-geo-replication-failover-powershell.md) | I dette selvstudium lærer du, hvordan Sådan flytter du til en geografisk replikerede sekundær replika, som ved hjælp af PowerShell.|
| [Starte en planlagt eller uventet failover for Azure SQL-Database ved hjælp af Transact-SQL](sql-database-geo-replication-failover-transact-sql.md) | I dette selvstudium lærer du, hvordan Sådan flytter du til en geografisk replikerede sekundær replika, som ved hjælp af Transact-SQL.|
||||

## <a name="data-sync"></a>Synkronisering af data

I dette selvstudium lærer du om [Synkronisering af Data](http://download.microsoft.com/download/4/E/3/4E394315-A4CB-4C59-9696-B25215A19CEF/SQL_Data_Sync_Preview.pdf).

| Selvstudium  | Beskrivelse  |
|---|---|---| 
| [Introduktion til Azure SQL Data Sync (Preview)](sql-database-get-started-sql-data-sync.md)  | I dette selvstudium lærer du grundlæggende Azure SQL Data Sync ved hjælp af portalen Azure klassisk. |
||||

## <a name="next-steps"></a>Næste trin

[Udforske Azure SQL Database løsning Hurtig start](sql-database-solution-quick-starts.md)
