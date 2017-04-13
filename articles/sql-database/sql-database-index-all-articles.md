<properties
    pageTitle="Alle emner til SQL-Database tjenesten | Microsoft Azure"
    description="Liste over alle emner til Azure-tjenesten med navnet SQL-Database, der findes på http://azure.microsoft.com/documentation/articles/, titel og beskrivelse."
    services="sql-database"
    documentationCenter=""
    authors="MightyPen"
    manager="jhubbard"
    editor="MightyPen"/>

<tags
    ms.service="sql-database"
    ms.workload="sql-database"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/05/2016"
    ms.author="genemi"/>


# <a name="all-topics-for-azure-sql-database-service"></a>Alle emner til Azure SQL Database-tjenesten

I dette emne beskrives hver emne, der gælder direkte til tjenesten **SQL-Database** til Azure. Du kan søge denne webside til nøgleord ved hjælp af **Ctrl + F**, søge efter emner af aktuelle interesse.




## <a name="new"></a>Ny

| &nbsp; | Titel | Beskrivelse |
| --: | :-- | :-- |
| 1 | [Daxko/CSI bruges Azure at fremskynde sin udviklingscyklus og for at forbedre computerens kundeservice og ydelse](sql-database-implementation-daxko.md) | Få mere at vide om, hvordan Daxko/CSI anvender SQL-Database at fremskynde sin udviklingscyklus og for at forbedre computerens kundeservice og ydelse |
| 2 | [Azure giver GEP global rækkevidde og større effektivitet](sql-database-implementation-gep.md) | Få mere at vide om, hvordan GEP anvender SQL-Database til at nå mere globale kunder og opnå større effektivitet |
| 3 | [Med Azure, er SnelStart hurtigt udvidet dens business services med en hastighed på 1.000 nye Azure SQL-databaser månedsbasis](sql-database-implementation-snelstart.md) | Få mere at vide om, hvordan SnelStart anvender SQL-Database til hurtigt udvidet dens business services med en hastighed på 1.000 nye Azure SQL-databaser månedsbasis |
| 4 | [Umbraco bruger Azure SQL-Database til hurtigt klargøring og skalering af tusindvis af lejere i skyen](sql-database-implementation-umbraco.md) | Få mere at vide om, hvordan Umbraco anvender SQL-Database til at klargøre hurtigt og skala tjenester for tusindvis af lejere i skyen |
| 5 | [Administrere Azure SQL-Database med PowerShell](sql-database-manage-powershell.md) | Azure SQL-Database management med PowerShell. |
| 6 | [SSMS support til Azure AD MFA med SQL-Database og SQL Data Warehouse](sql-database-ssms-mfa-authentication.md) | Bruge flere tages godkendelse med SSMS for SQL-Database og SQL datawarehouse. |
| 7 | [Forklarer Database transaktion enheder (DTUs) og elastiske Database transaktion enheder (eDTUs)](sql-database-what-is-a-dtu.md) | Forstå, hvad en Azure SQL-Database er transaktion opgaver. |


## <a name="updated-articles-sql-database"></a>Opdaterede artikler, SQL-Database

I dette afsnit beskrives artikler, som er blevet opdateret for nylig, hvor opdateringen blev stor eller betydeligt. For hver opdaterede artikel vises en grov kodestykke den tilføjede tabsbeløb teksts. Artiklerne er blevet opdateret inden for datointervallet af **2016-08-22** til **2016-10-05**.

| &nbsp; | Artikel | Tekstopdateringer, kodestykke | Opdateret, da |
| --: | :-- | :-- | :-- |
| 8 | [Administrere Azure SQL-Database med PowerShell](sql-database-command-line-tools.md) | Oprette en ressourcegruppe til vores SQL-Database og relaterede Azure ressourcer med Cmdletten ny-AzureRmResourceGroup (https://msdn.microsoft.com/library/azure/mt759837.aspx). *$resourceGroupName = "resourcegroup1" $resourceGroupLocation = "northcentralus" ny AzureRmResourceGroup-navngive $resourceGroupName-placering $resourceGroupLocation* Du kan finde flere oplysninger i bruge PowerShell for Azure med Azure ressourcestyring (… / powershell-azure-ressource-manager.md). Et eksempel på script, under oprette en SQL-database PowerShell-script (sql-database-get-startet-powershell.md create-a-sql-database-powershell-script). **Oprette en SQL-databaseserver** Oprette en SQL-databaseserver med Cmdletten ny-AzureRmSqlServer (https://msdn.microsoft.com/library/azure/mt603715.aspx). Erstat *server1* med navnet på din server. Servernavne skal være entydig på tværs af alle servere, der Azure SQL-Database. Hvis servernavnet er allerede taget, får du en fejl. Denne kommando kan tage flere minutter at gennemføre. Resou | 2016-09-14 |
| 9 | [Kopiere en Azure SQL-database ved hjælp af PowerShell](sql-database-copy-powershell.md) |   SQL-database datakilde (den eksisterende database for at kopiere) – $sourceDbName = "Saldo 1" $sourceDbServerName = "server1" $sourceDbResourceGroupName = "rg1" SQL databasekopi (den nye database skal have oprettet) – $copyDbName = "db1_copy" $copyDbServerName = "server2" $copyDbResourceGroupName = "rg2" Kopiér en database til den samme server – ny AzureRmSqlDatabaseCopy - ResourceGroupName $sourceDbResourceGroupName - servernavn $sourceDbServerName - databasenavn $sourceDbName - CopyDatabaseName $copyDbName kopiere en database til en anden server – ny AzureRmSqlDatabaseCopy - ResourceGroupName $sourceDbResourceGroupName - servernavn $sourceDbServerName - databasenavn $sourceDbName - CopyResourceGroupName $copyDbResourceGroupName - CopyServerName $copyDbServerName - CopyDatabaseName $copyDbName kopiere en database til en elastiske database puljen – $poolName = "pool1" ny AzureRmSqlDatabaseCopy - ResourceGroupName $ sourceDbResourceGroupName - servernavn $sourceDbServerName - databasenavn $sourceDbName - CopyReso | 2016-09-08 |
| 10 | [Oprette en elastiske database puljen med C#](sql-database-elastic-pool-create-csharp.md) |   Console.WriteLine ("Server firewall...");  FirewallRuleGetResponse fwr = CreateOrUpdateFirewallRule (_sqlMgmtClient _resourceGroupName _serverName, _firewallRuleName, _startIpAddress, og _endIpAddress);  Console.WriteLine ("Server firewall:" + fwr. FirewallRule.Id);  Console.WriteLine ("elastiske puljen...");  ElasticPoolCreateOrUpdateResponse epr = CreateOrUpdateElasticDatabasePool (_sqlMgmtClient _resourceGroupName _serverName, _poolName, _poolEdition, _poolDtus, _databaseMinDtus, og _databaseMaxDtus);  Console.WriteLine ("elastiske puljen:" + epr. ElasticPool.Id);  Console.WriteLine("Database...");  DatabaseCreateOrUpdateResponse dbr = CreateOrUpdateDatabase (_sqlMgmtClient, _resourceGroupName, _serverName, _databaseName, _poolName)  Console.WriteLine ("Database:" + dbr. Database.Id);  Console.WriteLine ("Tryk på en tast for at fortsætte...");  Console.ReadKey();  } statisk ResourceGroup CreateOrUpdateResourceGroup(ResourceManagementClient resourceMgmtClient, string subscriptionId, string resourceGroupNa | 2016-09-14 |
| 11 | [PowerShell-script til at identificere databaser, der passer til en elastiske database puljen](sql-database-elastic-pool-database-assessment-powershell.md) | **For elastiske databasen puljen kandidater vi udelade master og nogen databaser, der allerede findes i en gruppe. Du kan tilføje flere databaser til listen udeladt nedenfor efter behov** $ListOfDBs = Get-AzureRmSqlDatabase - servernavn $servername. Split('.') 0 - ResourceGroupName $ResourceGroupName / Where-objekt {$_. Databasenavn - Fondskomitéens ("master") - og $_. CurrentServiceLevelObjectiveName - Fondskomitéens ("ElasticPool")- og $_. CurrentServiceObjectiveName - Fondskomitéens ("ElasticPool")} $outputConnectionString = "datakilde = $outputServerName; integreret sikkerhed = falsk; indledende katalog = $outputdatabaseName; Bruger-Id = $outputDBUsername; Adgangskode = $outputDBpassword "$destinationTableName ="resource_stats_output" **oprette en tabel i output database til målepunkter samling** $sql =" Hvis NOT EXISTS (Vælg * fra sys.objects hvor object_id = OBJECT_ID(N'$($destinationTableName)') og skrive i (N'U ")) Start Opret en tabel $($destinationTableName) (databasenavn varchar(128) langsomme varchar(20) end_time datetime, avg_cpu slæk, og avg_ | 29-09-2016 |
| 12 | [SQL-Database Selvstudium: oprette en SQL-database i minutter ved hjælp af portalen Azure](sql-database-get-started.md) |   ! Ny sql database 1 (. / media/sql-database-get-started/sql-database-new-database-1.png) 3. Klik på **SQL-Database** for at åbne bladet SQL-Database. Indholdet af denne blade varierer afhængigt af antallet af dine abonnementer og din eksisterende objekter (såsom eksisterende servere).  ! Ny sql database 2 (. / media/sql-database-get-started/sql-database-new-database-2.png) 4. Angiv et navn for den første database - såsom "Mine-database" i tekstfeltet **Databasenavn** . Et grønt flueben angiver, at du har angivet et gyldigt navn.  ! Ny sql database 3 (. / media/sql-database-get-started/sql-database-new-database-3.png) 5. Hvis du har flere abonnementer, Vælg et abonnement. 6. Klik på **Opret ny** og angive et navn til dit første ressourcegruppe - såsom "Mine--ressourcegruppe" under **ressourcegruppe**. Et grønt flueben angiver, at du har angivet et gyldigt navn.  ! Ny sql database 4 (. / media/sql-database-get-started/sql-database-new-database-4.png) 7. Under **Vælg kilde* | 2016-09-08 |
| 13 | [Prøv SQL-Database: Brug C# til at oprette en SQL-database med biblioteket SQL-Database til .NET](sql-database-get-started-csharp.md) | **Opret en sikkerhedskonto til at få adgang til ressourcer tjeneste** Følgende PowerShell-script opretter programmet Active Directory (AD) og hovedstolen service, vi har brug for at godkende vores C-app. Scriptet udskriver værdier, der skal bruges i det foregående eksempel C. Oplysninger, skal du se bruge Azure PowerShell til at oprette en tjeneste sikkerhedskonto få adgang til ressourcer (… / ressource-gruppe-godkende-service-principal.md).  Log på Azure.  Tilføje AzureRmAccount Hvis du har flere abonnementer, fjern kommentar fra og angive til det abonnement, du vil arbejde med.  $subscriptionId = "{xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx}" sæt AzureRmContext - SubscriptionId $subscriptionId giver dig disse værdier til din nye AAD-app.  $appName er det viste navn for din app, skal være entydige i mappen.  $uri behøver ikke at være et reelt tal uri.  $secret er en adgangskode, du opretter.  $appName = "{app-navn}" $uri = "http://{app-name}" $secret = "{app-adgangskode}" Opret en AAD app $azureAdApplication = ny AzureRmADApp | 2016-09-23 |
| 14 | [Administrere Azure SQL-databaser ved hjælp af portalen Azure](sql-database-manage-portal.md) | For at få vist eller opdatere indstillingerne for din database, skal du klikke på den ønskede indstilling på bladet SQL-database:! Indstillinger for SQL-database (. / media/sql-database-manage-portal/settings.png) **hvordan finder jeg en SQL-databaser fuldstændige servernavn?** Klik på **Oversigt** på bladet **SQL-database** for at få vist servernavn databaser, og noter servernavnet:! Indstillinger for SQL-database (. / media/sql-database-manage-portal/server-name.png) **hvordan administrerer jeg firewallregler til at styre adgangen til min SQL server og database?** Klik på **Angiv server firewall** på bladet **SQL-database** for at få vist, oprette eller opdatere firewallregler. Yderligere oplysninger finder du se konfigurere en regel for Azure SQL-Database serverniveau firewall ved hjælp af portalen Azure (sql-database-konfigurere-firewall-settings.md). ! regler for Firewall (. / media/sql-database-manage-portal/sql-database-firewall.png) **hvordan ændrer jeg min SQL database-tjenesten niveau eller ydeevne niveau?** Hvis du vil opdatere service niveau eller ydeevne niveauet for en SQL-database, skal du klikke på ** priser niveau (s | 2016-09-20 |





## <a name="get-started"></a>Komme i gang

| &nbsp; | Titel | Beskrivelse |
| --: | :-- | :-- |
| 15 | [Opbygger med flere lejer Apps med Azure SQL-Database med isolationsniveauet og effektivitet](sql-database-build-multi-tenant-apps.md) | Få mere at vide, hvordan opbygger SQL-Database med flere lejer apps |
| 16 | [Oprette forbindelse til SQL-Database med SQL Server Management Studio og udføre en stikprøve T-SQL-forespørgsel](sql-database-connect-query-ssms.md) | Få mere at vide, hvordan du opretter forbindelse til SQL-Database på Azure ved hjælp af SQL Server Management Studio (SSMS). Kør derefter et på eksempelforespørgsel, der bruger Transact-SQL (T-SQL). |
| 17 | [Oprette forbindelse til SQL-Database ved hjælp af .NET (C#)](sql-database-develop-dotnet-simple.md) | Brug eksempelkoden i denne hurtige starter på at oprette et moderne program med C# og sikkerhedskopien af en effektiv relationsdatabase i skyen med Azure SQL-Database. |
| 18 | [Oprette en ny gruppe elastiske database ved hjælp af Azure portal](sql-database-elastic-pool-create-portal.md) | Sådan føjes en SVG elastiske database pulje til konfigurationen af SQL-database til nemmere administration og ressourcedeling på tværs af mange databaser. |
| 19 | [Udforske Azure SQL Database selvstudier](sql-database-explore-tutorials.md) | Få mere at vide om SQL-Database funktioner og egenskaber |
| 20 | [SQL-Database Selvstudium: oprette en SQL-database i minutter ved hjælp af portalen Azure](sql-database-get-started.md) | Lær, hvordan du konfigurerer en SQL-Database logiske server, server firewallregel, SQL-database og eksempeldata. Se også, hvordan du kan oprette forbindelse til klientværktøjer, konfigurere brugere og konfigurere en database firewallregel. |
| 21 | [Azure SQL-Database sikrer og beskytter](sql-database-helps-secures-and-protects.md) | Få mere at vide, hvordan SQL-Database hjælper med at sikre og beskytte |
| 22 | [Azure SQL-Database lærer &amp; tilpasser sig efterhånden](sql-database-learn-and-adapt.md) | Lær, hvordan SQL-Database lærer og tilpasser sig efterhånden |
| 23 | [Vælg en skybaseret SQL Server option: Azure SQL (PaaS)-Database eller SQL Server på Azure FOS (IaaS)](sql-database-paas-vs-sql-server-iaas.md) | Få mere at vide, hvilke skyen SQL Server option passer til dit program: Azure SQL (PaaS)-Database eller SQL Server i skyen på virtuelle Azure-computere. |
| 24 | [Azure SQL Database ubalance på farten](sql-database-scale-on-the-fly.md) | Få mere at vide, hvordan SQL-Database skaleres på farten |
| 25 | [Udforske Azure SQL Database løsning Hurtig start](sql-database-solution-quick-starts.md) | Få mere at vide om Azure SQL Database-løsninger |
| 26 | [Azure SQL-Database, der fungerer i dit miljø](sql-database-works-in-your-environment.md) | Få mere at vide, hvordan SQL-Database hjælper, sikrer og beskytter |



## <a name="build-an-app"></a>Oprette en app

| &nbsp; | Titel | Beskrivelse |
| --: | :-- | :-- |
| 27 | [Fejlfinding i forbindelse med, diagnosticere og forhindre SQL forbindelsesfejl og midlertidige fejl for SQL-Database](sql-database-connectivity-issues.md) | Lær at foretage fejlfinding af diagnosticere og forhindre en SQL-forbindelsesfejl eller midlertidige fejl i Azure SQL-Database.  |
| 28 | [Oprette forbindelse til en SQL-Database med Visual Studio](sql-database-connect-query.md) | Skrive et program i C# til at forespørge og oprette forbindelse til SQL-database. Oplysninger om IP-adresser, strenge, sikker login og gratis Visual Studio. |
| 29 | [Porte ud over 1433 til ADO.NET 4.5 og version 12 SQL-Database](sql-database-develop-direct-route-ports-adonet-v12.md) | Klientforbindelser fra ADO.NET til Azure SQL-Database version 12 nogle gange tilsidesætte proxyen og interagere direkte med databasen. Porte end 1433 bliver vigtige. |
| 30 | [SQL-fejlkoder for SQL-Database-klientprogrammer: Webdatabase forbindelsesfejl og andre problemer](sql-database-develop-error-messages.md) | Få mere at vide om SQL fejlkoder for SQL-Database klientprogrammer som almindelige database forbindelsesfejl, database kopi problemer og generelle fejl.  |
| 31 | [Oprette forbindelse til SQL-Database ved hjælp af PHP i Windows](sql-database-develop-php-simple.md) | Viser et eksempel PHP program, der opretter forbindelse til Azure SQL-Database fra en Windows-klient og indeholder links til de nødvendige software-komponenter, der bruges af klienten. |
| 32 | [Prøv SQL-Database: Brug C# til at oprette en SQL-database med biblioteket SQL-Database til .NET](sql-database-get-started-csharp.md) | Prøv SQL-Database til udvikling af SQL og C# apps, og oprette en Azure SQL-Database med C# ved hjælp af biblioteket SQL-Database til .NET. |
| 33 | [Introduktion til JSON-funktioner i Azure SQL-Database](sql-database-json-features.md) | Azure SQL-Database gør det muligt at analysér, forespørgsel og formatere data i JavaScript Object Notation (JSON) notation. |
| 34 | [Foretage fejlfinding af forbindelsesproblemer til Azure SQL-Database](sql-database-troubleshoot-common-connection-issues.md) | Trin til at identificere og løse almindelige forbindelsesfejl i til Azure SQL-Database. |
| 35 | [Fejlen "Databasen på serveren er ikke tilgængelig i øjeblikket" Når du opretter forbindelse til sql-database](sql-database-troubleshoot-connection.md) | Foretage fejlfinding af databasen på serveren er ikke tilgængelig i øjeblikket fejl, når et program, der opretter forbindelse til SQL-Database. |



## <a name="develop"></a>Udvikle

| &nbsp; | Titel | Beskrivelse |
| --: | :-- | :-- |
| 36 | [Få de påkrævede værdier til godkendelse af et program tilladelse til at få adgang til SQL-Database fra kode](sql-database-client-id-keys.md) | Oprette en tjeneste hovedstolen for at få adgang til SQL-Database fra kode. |
| 37 | [Oprette forbindelse til SQL-Database ved hjælp af Java med JDBC i Windows](sql-database-develop-java-simple.md) | Viser et eksempel på Java kode du kan bruge til at oprette forbindelse til Azure SQL-Database. Udsnittet bruger JDBC, og den kører på en Windows-klientcomputer. |
| 38 | [Oprette forbindelse til SQL-Database ved hjælp af Node.js](sql-database-develop-nodejs-simple.md) | Viser et eksempel på Node.js kode du kan bruge til at oprette forbindelse til Azure SQL-Database. |
| 39 | [SQL-Database udvikling oversigt](sql-database-develop-overview.md) | Få mere at vide om tilgængelige connectivity biblioteker og bedste fremgangsmåder for at oprette forbindelse til SQL Database-programmer. |
| 40 | [Oprette forbindelse til SQL-Database ved hjælp af Python](sql-database-develop-python-simple.md) | Viser et eksempel på Python kode du kan bruge til at oprette forbindelse til Azure SQL-Database. |
| 41 | [Oprette forbindelse til SQL-Database ved hjælp af fonetisk](sql-database-develop-ruby-simple.md) | Give et eksempel på fonetisk kode, du kan køre for at oprette forbindelse til Azure SQL-Database. |
| 42 | [Introduktion til tidsmæssig tabeller i Azure SQL-Database](sql-database-temporal-tables.md) | Lær, hvordan du kommer i gang med at bruge tidsmæssig tabeller i Azure SQL-Database. |



## <a name="performance-and-scale"></a>Ydeevne og skalering

| &nbsp; | Titel | Beskrivelse |
| --: | :-- | :-- |
| 43 | [SQL-Database Advisor](sql-database-advisor.md) | Azure SQL Database Advisor indeholder anbefalinger til din eksisterende SQL-databaser, der kan forbedre Forespørgselsydeevnen for aktuelle. |
| 44 | [SQL-Database Advisor ved hjælp af portalen Azure](sql-database-advisor-portal.md) | Du kan bruge Azure SQL Database Advisor i portalen Azure Gennemse og implementere anbefalinger til din eksisterende SQL-databaser, der kan forbedre Forespørgselsydeevnen for aktuelle. |
| 45 | [Azure SQL-Database benchmark oversigt](sql-database-benchmark-overview.md) | Dette emne beskrives Azure SQL Database Benchmark bruges i måling af ydeevnen for Azure SQL-Database. |
| 46 | [Hvornår skal en elastiske database puljen bruges?](sql-database-elastic-pool-guidance.md) | En elastiske databasen gruppe er en samling af tilgængelige ressourcer, der deles af en gruppe af elastiske databaser. Dette dokument indeholder vejledning til at vurdere egnet ved at bruge en elastiske database puljen for en gruppe af databaser. |
| 47 | [Komme i gang med elastiske Databaseværktøjer](sql-database-elastic-scale-get-started.md) | Grundlæggende gennemgang af elastiske database værktøjer funktion i Azure SQL-Database, herunder nemt for at køre eksempel app. |
| 48 | [SQL-Database ofte stillede spørgsmål](sql-database-faq.md) | Svar på almindelige spørgsmål kunder Bed om skyen databaser og Azure SQL-Database, Microsofts relationsdatabase administrationssystem (RDBMS) og database som en tjeneste i skyen. |
| 49 | [Komme i gang med i hukommelsen (Preview) i SQL-Database](sql-database-in-memory.md) | SQL i hukommelsen teknologier forbedre ydeevnen af transaktioner og analytics arbejdsmængder. Lær at udnytte disse teknologier. |
| 50 | [Brug i hukommelsen OLTP (preview) til at forbedre din programmets ydeevne i SQL-Database](sql-database-in-memory-oltp-migration.md) | Vedtager i hukommelsen OLTP til at forbedre transaktions ydeevne i en eksisterende SQL-database. |
| 51 | [Skærm i hukommelsen OLTP lagerplads](sql-database-in-memory-oltp-monitoring.md) | Vurdering og overvåge XTP hukommelseslager bruger, kapacitet. løse kapacitet fejl 41823 |
| 52 | [Operativsystem den forespørgsel, der er gemt i Azure SQL-Database](sql-database-operate-query-store.md) | Få mere at vide om at bruge den forespørgsel, der er gemt i Azure SQL-Database |
| 53 | [SQL-Database ydeevne indsigt](sql-database-performance.md) | Azure SQL-Database indeholder ydeevne værktøjer for at hjælpe dig med at identificere områder, der kan forbedre Forespørgselsydeevnen for aktuelle. |
| 54 | [Azure SQL-Database og ydeevne for enkelt databaser](sql-database-performance-guidance.md) | I denne artikel kan hjælpe dig med at finde ud af, hvilke webtjenesten kan vælge til dit program. Den anbefaler metoder til at finjustere dit program tilladelse til at få mest muligt ud af Azure SQL-Database. |
| 55 | [Azure SQL Database-forespørgsel ydeevne indsigt](sql-database-query-performance.md) | Forespørgsel overvågning identificerer de fleste CPU-forbrug forespørgsler til en Azure SQL-Database. |
| 56 | [Ændre service niveau og ydeevne niveauet (priser niveau) for en SQL-database](sql-database-scale-up.md) | Ændre webtjenesten og præstationsniveau Azure SQL-database viser, hvordan du skalere SQL-database, op eller ned. Ændre det priser niveau i en Azure SQL-database. |
| 57 | [Overvåge databaseydeevnen for i Azure SQL-Database](sql-database-single-database-monitor.md) | Få mere at vide om indstillinger for overvågning af databasen med Azure værktøjer og administration af dynamiske visninger. |
| 58 | [SQL-Database ydeevne justering tip](sql-database-troubleshoot-performance.md) | Tip til justering i Azure SQL-Database via evaluering og forbedring af ydeevnen. |
| 59 | [Sådan bruges samling til at forbedre ydeevnen i SQL-Database-program](sql-database-use-batching-to-improve-performance.md) | Emnet indeholder dokumentation, der batching Databasefunktioner betydeligt imroves hastigheden og skalerbarhed af programmerne Azure SQL-Database. Selvom disse batching teknikker arbejder til en SQL Server-database, er fokus i denne artikel om Azure. |



## <a name="tools-for-scaling-out"></a>Værktøjer til skalering af

| &nbsp; | Titel | Beskrivelse |
| --: | :-- | :-- |
| 60 | [Designe mønstre for multiprofiler SaaS programmer og Azure SQL-Database](sql-database-design-patterns-multi-tenancy-saas-applications.md) | I denne artikel beskrives de krav og fælles data arkitektur mønstre multiprofiler databasen programmer, der kører i et skybaseret miljø skal overveje, og der er knyttet til disse mønstre forskellige kompromiserne. Det også beskrives, hvordan Azure SQL-Database med dens elastiske grupper og elastiske værktøjer at løse disse krav på en ingen sammensat måde. |
| 61 | [Overføre eksisterende databaser til skala ud](sql-database-elastic-convert-to-use-elastic-tools.md) | Konvertere delt databaser for at bruge elastiske Databaseværktøjer ved at oprette en shard kort manager |
| 62 | [Opbygning af SVG skyen databaser](sql-database-elastic-database-client-library.md) | Opbygge SVG .NET database apps med elastiske database klient-bibliotek |
| 63 | [Tællere i ydeevne for shard kort manager](sql-database-elastic-database-perf-counters.md) | ShardMapManager klasse og data afhængige routing tællere i ydeevne |
| 64 | [Tilføje en shard ved hjælp af elastiske Databaseværktøjer](sql-database-elastic-scale-add-a-shard.md) | Sådan bruges elastiske skala API'er til at føje nye shards til en shard angive. |
| 65 | [Installere en opdelt Flet-tjeneste](sql-database-elastic-scale-configure-deploy-split-and-merge.md) | Opdele og flette med elastiske Databaseværktøjer |
| 66 | [Afhængige distribution af data](sql-database-elastic-scale-data-dependent-routing.md) | Hvordan du bruger klassen ShardMapManager i .NET-apps til data-afhængige routing, en funktion af elastiske databaser til Azure SQL-Database |
| 67 | [Elastiske Databaseværktøjer ofte stillede spørgsmål](sql-database-elastic-scale-faq.md) | Ofte stillede spørgsmål om Azure SQL Database elastiske skala. |
| 68 | [Elastiske Database værktøjer ordliste](sql-database-elastic-scale-glossary.md) | Forklaring af ord, der bruges til elastiske Databaseværktøjer |
| 69 | [Skalering af med Azure SQL-Database](sql-database-elastic-scale-introduction.md) | Software som en tjeneste (SaaS) udviklere kan nemt oprette elastiske, SVG databaser i skyen ved hjælp af disse værktøjer |
| 70 | [Legitimationsoplysninger, der bruges til at få adgang til biblioteket elastiske Database klient](sql-database-elastic-scale-manage-credentials.md) | Sådan angives det rette niveau af legitimationsoplysninger, administrator for at skrivebeskyttet til elastiske database-apps |
| 71 | [Flere shard forespørgsler](sql-database-elastic-scale-multishard-querying.md) | Køre forespørgsler på tværs af shards ved hjælp af biblioteket elastiske database klient. |
| 72 | [At flytte data mellem skaleret skyen databaser](sql-database-elastic-scale-overview-split-and-merge.md) | Beskriver, hvordan du manipulere shards og flytte data via en selvstændig hostet tjeneste elastiske webdatabasen API'er. |
| 73 | [Skalere ud databaser med shard kort manager](sql-database-elastic-scale-shard-map-management.md) | Sådan bruger du ShardMapManager, elastiske database klientbibliotek |
| 74 | [Konfiguration af delt Flet sikkerhed](sql-database-elastic-scale-split-merge-security-configuration.md) | Konfigurere x409 certifikater til kryptering |
| 75 | [Opgradere en app til at bruge det seneste elastiske database klientbibliotek](sql-database-elastic-scale-upgrade-client-library.md) | Opgradere apps og biblioteker ved hjælp af Nuget |
| 76 | [Elastiske Database klientbibliotek med enhed Framework](sql-database-elastic-scale-use-entity-framework-applications-visual-studio.md) | Brug af elastiske Database klientbibliotek og enhed Framework til kodningssprog databaser |
| 77 | [Brug af elastiske database klientbibliotek med Dapper](sql-database-elastic-scale-working-with-dapper.md) | Brug af elastiske database klientbibliotek med Dapper. |
| 78 | [Flere lejer programmer med elastiske Databaseværktøjer og sikkerhed på brugerniveau række](sql-database-elastic-tools-multi-tenant-row-level-security.md) | Lær at bruge elastiske Databaseværktøjer sammen med sikkerhed på brugerniveau række til at oprette et program med en meget SVG data niveau på Azure SQL-Database, der understøtter flere lejer shards. |



## <a name="elastic-jobs"></a>Elastiske job

| &nbsp; | Titel | Beskrivelse |
| --: | :-- | :-- |
| 79 | [Oprette og administrere skalerede ud Azure SQL-databaser ved hjælp af elastiske jobs (preview)](sql-database-elastic-jobs-create-and-manage.md) | Gennemgå oprettelse og administration af et job elastiske database. |
| 80 | [Introduktion til elastiske Database job](sql-database-elastic-jobs-getting-started.md) | Sådan bruger du elastiske database job |
| 81 | [Administrere skaleret skyen databaser](sql-database-elastic-jobs-overview.md) | Illustrerer tjenesten elastiske database job |
| 82 | [Oprette og administrere en SQL-Database elastiske database-job ved hjælp af PowerShell (preview)](sql-database-elastic-jobs-powershell.md) | PowerShell til at administrere grupper Azure SQL-Database |
| 83 | [Oversigt over installation elastiske Database](sql-database-elastic-jobs-service-installation.md) | Gennemgå installation af funktionen elastiske job. |
| 84 | [Fjerne elastiske databasekomponenter job](sql-database-elastic-jobs-uninstall.md) | Sådan fjernes job elastiske databaseværktøjet |



## <a name="elastic-pools"></a>Elastiske grupper

| &nbsp; | Titel | Beskrivelse |
| --: | :-- | :-- |
| 85 | [Hvad er en Azure elastiske puljen?](sql-database-elastic-pool.md) | Administrere hundredvis eller tusindvis af databaser ved hjælp af en gruppe. Én pris for et sæt ydelsesenheder kan være fordelt over en puljen. Flytte databaser ind eller ud på vil. |
| 86 | [Oprette en elastiske database puljen med C#](sql-database-elastic-pool-create-csharp.md) | Bruge C# database udviklingsteknikker til at oprette en SVG elastiske database pulje i Azure SQL-Database, så du kan dele ressourcer på tværs af mange databaser. |
| 87 | [Oprette en ny gruppe elastiske database med PowerShell](sql-database-elastic-pool-create-powershell.md) | Lær at bruge PowerShell til at skala fra Azure SQL-Database ressourcer ved at oprette en SVG elastiske database gruppe for at administrere flere databaser. |
| 88 | [PowerShell-script til at identificere databaser, der passer til en elastiske database puljen](sql-database-elastic-pool-database-assessment-powershell.md) | En elastiske databasen gruppe er en samling af tilgængelige ressourcer, der deles af en gruppe af elastiske databaser. Dette dokument indeholder et Powershell-script til at vurdere egnet ved at bruge en elastiske database puljen for en gruppe af databaser. |
| 89 | [Overvåge og administrere en elastiske database puljen med C#](sql-database-elastic-pool-manage-csharp.md) | Bruge C#-database udviklingsteknikker til at administrere en Azure SQL-Database elastiske databasen puljen. |
| 90 | [Overvåge og administrere en elastiske database puljen ved hjælp af Azure portal](sql-database-elastic-pool-manage-portal.md) | Lær, hvordan du bruger portalen Azure og SQL-Database indbyggede intelligence til at administrere, overvåge og højre-størrelse en SVG elastiske databasen pulje vil optimere databasens ydeevne og administrere omkostninger. |
| 91 | [Overvåge og administrere en elastiske database puljen med PowerShell](sql-database-elastic-pool-manage-powershell.md) | Lær at bruge PowerShell til at administrere en elastiske database puljen. |
| 92 | [Overvåge og administrere en elastiske database puljen med Transact-SQL](sql-database-elastic-pool-manage-tsql.md) | Brug T-SQL til at oprette en Azure SQL-database i en elastiske puljen. Eller brug T-SQL til at flytte datbase og grupper. |
| 93 | [Elastiske database puljen fakturerings- og prisoplysninger](sql-database-elastic-pool-price.md) | Prisoplysninger specifikke for elastiske database grupper. |



## <a name="elastic-query"></a>Elastiske forespørgsel

| &nbsp; | Titel | Beskrivelse |
| --: | :-- | :-- |
| 94 | [Rapportere på tværs af skaleret skyen databaser (preview)](sql-database-elastic-query-getting-started.md) | Sådan bruges på tværs af database databaseforespørgsler |
| 95 | [Komme i gang med kryds-databaseforespørgsler (lodret partitionering) (preview)](sql-database-elastic-query-getting-started-vertical.md) | Sådan bruges elastiske databaseforespørgsel med lodret opdelt databaser |
| 96 | [Rapportering af hele skaleret skyen databaser (preview)](sql-database-elastic-query-horizontal-partitioning.md) | hvordan du konfigurerer elastiske forespørgsler over vandret partitioner |
| 97 | [Azure SQL-Database elastiske databasen forespørgselsoversigt (preview)](sql-database-elastic-query-overview.md) | Oversigt over funktionen elastiske forespørgsel |
| 98 | [Forespørgsel på tværs af skyen databaser med forskellige skemaer (preview)](sql-database-elastic-query-vertical-partitioning.md) | hvordan du konfigurerer tværs databaseforespørgsler over lodret partitioner |



## <a name="elastic-transaction"></a>Elastiske transaktion

| &nbsp; | Titel | Beskrivelse |
| --: | :-- | :-- |
| 99 | [Distribuerede transaktioner på tværs af skyen databaser](sql-database-elastic-transactions-overview.md) | Oversigt over elastiske databasetransaktioner med Azure SQL-Database |



## <a name="backup-and-recovery"></a>Sikkerhedskopiering og gendannelse

| &nbsp; | Titel | Beskrivelse |
| --: | :-- | :-- |
| 100 | [SQL-Database sikkerhedskopier](sql-database-automated-backups.md) | Få mere at vide om SQL-Database, der er indbygget sikkerhedskopier af databaser, der giver dig mulighed at vende tilbage Azure SQL-Database til et tidligere punkt tidspunkt eller kopiere en database til en ny database i en geografisk område (op til 35 dage). |
| 101 | [Oversigt over Forretningskontinuitet med Azure SQL-Database](sql-database-business-continuity.md) | Lær, hvordan Azure SQL-Database understøtter skyen Forretningskontinuitet og gendannelse af databasen og hjælper med at holde vigtige skyen programmer, der kører. |
| 102 | [Sådan gendannes en enkelt tabel fra en sikkerhedskopi af Azure SQL-Database](sql-database-cloud-migrate-restore-single-table-azure-backup.md) | Lær, hvordan du kan gendanne en enkelt tabel fra Azure SQL-Database sikkerhedskopi. |
| 103 | [Designe et program til skyen nedbrud ved hjælp af aktive geografisk-gentagelse i SQL-Database](sql-database-designing-cloud-solutions-for-disaster-recovery.md) | Lær at designe skyen nedbrud gendannelse løsninger til business løbende planlægning med geografisk gentagelse til app sikkerhedskopiering af data med Azure SQL-Database. |
| 104 | [Gendanne en Azure SQL-Database eller failover til et sekundært](sql-database-disaster-recovery.md) | Lær, hvordan du gendanner en database fra en regionale datacenter strømafbrydelse eller fejl med Azure SQL Database aktive geografisk replikering og geografisk-Gendan funktioner. |
| 105 | [Udføre nedbrud gendannelse analysér](sql-database-disaster-recovery-drills.md) | Få mere at vide vejledning og bedste fremgangsmåder til brug af Azure SQL-Database til at udføre nedbrud gendannelse øvelser, der kan hjælpe holde din kommunikations kritiske business programmer tolerant fejl og afbrydelser. |
| 106 | [Nedbrud gendannelse strategier for programmer, der bruger SQL-Database elastiske puljen](sql-database-disaster-recovery-strategies-for-applications-with-elastic-pool.md) | Lær at designe din skyen løsning for nedbrud ved at vælge det rigtige failover mønster. |
| 107 | [Brug af klassen RecoveryManager til at løse problemer med shard kort](sql-database-elastic-database-recovery-manager.md) | Bruge klassen RecoveryManager til at løse problemer med shard kort |
| 108 | [Importere en BACPAC fil for at oprette en Azure SQL-database](sql-database-import.md) | Oprette en Azure SQL-database ved at importere en eksisterende BACPAC-fil. |
| 109 | [Gendanne en Azure SQL-Database til et tidligere tidspunkt ved hjælp af Azure-Portal](sql-database-point-in-time-restore-portal.md) | Gendanne en Azure SQL-Database til et tidligere tidspunkt. |
| 110 | [Gendanne en Azure SQL-Database til et tidligere tidspunkt med PowerShell](sql-database-point-in-time-restore-powershell.md) | Gendanne en Azure SQL-Database til et tidligere tidspunkt |
| 112 | [Gendanne en Azure SQL-database ved hjælp af automatiseret databasesikkerhedskopier](sql-database-recovery-using-backups.md) | Få mere at vide om punkt-in-Time gendannelse, der gør det muligt at annullere en Azure SQL-Database til et tidligere punkt i gang (op til 35 dage). |
| 113 | [Gendanne en slettet Azure SQL-database ved hjælp af portalen Azure](sql-database-restore-deleted-database-portal.md) | Gendanne en slettet Azure SQL-database (Azure Portal). |
| 114 | [Gendanne en slettet Azure SQL-Database ved hjælp af PowerShell](sql-database-restore-deleted-database-powershell.md) | Gendanne en slettet Azure SQL-Database (PowerShell). |
| 115 | [Gendanne en database til et tidligere tidspunkt, gendanne en slettet database eller gendanne fra en data center afbrydelse](sql-database-troubleshoot-backup-and-restore.md) | Lær, hvordan du gendanner en cloud-database fra fejl og afbrydelser ved hjælp af sikkerhedskopier og replikaer i Azure SQL-Database. |



## <a name="migrate"></a>Overføre

| &nbsp; | Titel | Beskrivelse |
| --: | :-- | :-- |
| 116 | [Eksportere en SQL Server-database til en BACPAC fil ved hjælp af SqlPackage](sql-database-cloud-migrate-compatible-export-bacpac-sqlpackage.md) | Microsoft Azure SQL-Database database overførslen eksportere database, skal du eksportere BACPAC fil, sqlpackage |
| 117 | [Eksportere en SQL Server-database til en BACPAC fil ved hjælp af SQL Server Management Studio](sql-database-cloud-migrate-compatible-export-bacpac-ssms.md) | Microsoft Azure SQL-Database database overførslen eksportere database, skal du eksportere BACPAC filer, Eksporter Data niveau-guiden |
| 118 | [Importere til SQL-Database fra en BACPAC fil ved hjælp af SqlPackage](sql-database-cloud-migrate-compatible-import-bacpac-sqlpackage.md) | Microsoft Azure SQL-Database database overførslen importeres database, skal du importere BACPAC-fil, sqlpackage |
| 119 | [Importere fra BACPAC til SQL-Database ved hjælp af SSMS](sql-database-cloud-migrate-compatible-import-bacpac-ssms.md) | Microsoft Azure SQL Database database installerer, database overførslen Indlæs database Eksportér database, guiden Overflytning |
| 120 | [Overføre SQL Server-database til SQL-Database ved hjælp af installere Database til guiden Microsoft Azure-Database](sql-database-cloud-migrate-compatible-using-ssms-migration-wizard.md) | Microsoft Azure SQL-Database database overførslen guiden til Microsoft Azure-Database |
| 121 | [Overføre SQL Server-database til Azure SQL-Database ved hjælp af transaktions gentagelse](sql-database-cloud-migrate-compatible-using-transactional-replication.md) | Microsoft Azure SQL-Database database overførslen importere databasen, transaktions gentagelse |
| 122 | [Find ud af SQL-Database kompatibelt ved hjælp af SqlPackage.exe](sql-database-cloud-migrate-determine-compatibility-sqlpackage.md) | Microsoft Azure SQL Database, overflytning af databasen, SQL-Database kompatibilitet, SqlPackage |
| 123 | [Brug SQL Server Management Studio til at finde ud af SQL-Database kompatibilitet før overførsel til Azure SQL-Database](sql-database-cloud-migrate-determine-compatibility-ssms.md) | Microsoft Azure SQL Database, overflytning af databasen, SQL-Database kompatibilitet, eksportere Data niveau-guiden |
| 124 | [Bruge SQL Azure overførsel guide til at løse SQL Server-database kompatibilitetsproblemer før overførsel til Azure SQL-Database](sql-database-cloud-migrate-fix-compatibility-issues.md) | Microsoft Azure SQL Database, overflytning af databasen, kompatibilitet, guiden SQL Azure overflytning |
| 125 | [Overføre en SQL Server-Database til Azure SQL-Database ved hjælp af SQL Server Data Tools til Visual Studio](sql-database-cloud-migrate-fix-compatibility-issues-ssdt.md) | Microsoft Azure SQL Database, overflytning af databasen, kompatibilitet, SQL Azure overførsel guiden, SSDT |
| 126 | [Løse SQL Server-database kompatibilitetsproblemer med SQL Server Management Studio før overførsel til SQL-Database](sql-database-cloud-migrate-fix-compatibility-issues-ssms.md) | Microsoft Azure SQL Database, overflytning af databasen, kompatibilitet, guiden SQL Azure overflytning |
| 127 | [Arkivere en Azure SQL-database til en BACPAC-fil ved hjælp af PowerShell](sql-database-export-powershell.md) | Arkivere en Azure SQL-database til en BACPAC-fil ved hjælp af PowerShell |
| 128 | [Importere en BACPAC fil for at oprette en Azure SQL-database ved hjælp af PowerShell](sql-database-import-powershell.md) | Importere en BACPAC fil for at oprette en Azure SQL-database ved hjælp af PowerShell |
| 129 | [Indlæse data fra CSV til Azure SQL Data Warehouse (flad filer)](sql-database-load-from-csv-with-bcp.md) | Bruger bcp til at importere data til Azure SQL-Database til en lille datastørrelse. |
| 130 | [Flytte databaser mellem servere, mellem-abonnementer og og Azure](sql-database-troubleshoot-moving-data.md) | Hurtige trin til at kopiere, flytte, og overføre data og databaser i Azure SQL-Database. |



## <a name="move-data-in-and-out"></a>Flytte data ind og ud

| &nbsp; | Titel | Beskrivelse |
| --: | :-- | :-- |
| 131 | [Overførsel af SQL Server-database til SQL-Database i skyen](sql-database-cloud-migrate.md) | Lær, hvordan om lokal SQL Server-database overførsel til Azure SQL-Database i skyen. Brug Overførselsværktøj fra databasen til at teste kompatibilitet før overflytning af databasen. |
| 132 | [Kopiere en Azure SQL-Database](sql-database-copy.md) | Oprette en kopi af en Azure SQL-database |
| 133 | [Arkivere en Azure SQL-database til en BACPAC fil ved hjælp af portalen Azure](sql-database-export.md) | Arkivere en Azure SQL-database til en BACPAC fil ved hjælp af portalen Azure |



## <a name="security"></a>Sikkerhed

| &nbsp; | Titel | Beskrivelse |
| --: | :-- | :-- |
| 134 | [Oprette forbindelse til SQL-Database eller SQL datawarehouse ved hjælp af Azure Active Directory-godkendelse](sql-database-aad-authentication.md) | Få mere at vide, hvordan du opretter forbindelse til SQL-Database ved hjælp af Azure Active Directory-godkendelse. |
| 135 | [Altid krypteret: Beskytte følsomme oplysninger i SQL-Database og gemme dine krypteringsnøgler i Windows certifikat store](sql-database-always-encrypted.md) | Beskytte følsomme oplysninger i dine SQL-database i minutter. |
| 136 | [Altid krypteret: Beskytte følsomme oplysninger i SQL-Database og gemme dine krypteringsnøgler i Azure-tasten samling](sql-database-always-encrypted-azure-key-vault.md) | Beskytte følsomme oplysninger i dine SQL-database i minutter. |
| 137 | [SQL-Database - understøttelse af bagudkompatible klienter og IP-slutpunkt ændringer i forbindelse med overvågning](sql-database-auditing-and-dynamic-data-masking-downlevel-clients.md) | Få mere at vide om understøttelse af SQL-Database bagudkompatible klienter og IP-slutpunkt ændringer i forbindelse med overvågning. |
| 138 | [Konfigurere Azure SQL-Database serverniveau firewallregler ved hjælp af PowerShell](sql-database-configure-firewall-settings-powershell.md) | Lær, hvordan du konfigurerer firewallen for IP-adresser, få adgang til Azure SQL-databaser. |
| 139 | [Konfigurere Azure SQL-Database serverniveau firewallregler ved hjælp af REST-API](sql-database-configure-firewall-settings-rest.md) | Lær, hvordan du konfigurerer firewallen for IP-adresser, få adgang til Azure SQL-databaser. |
| 140 | [Konfigurere ved hjælp af T-SQL Azure SQL-Database serverniveau og database niveau firewallregler](sql-database-configure-firewall-settings-tsql.md) | Lær, hvordan du konfigurerer firewallen for IP-adresser, få adgang til Azure SQL-databaser. |
| 141 | [Komme i gang med SQL-Database dynamiske Data Masking (Azure Portal)](sql-database-dynamic-data-masking-get-started.md) | Hvordan du kan komme i gang med SQL-Database dynamiske Data Masking i portalen Azure |
| 142 | [Komme i gang med SQL-Database dynamiske Data Masking (Azure klassisk Portal)](sql-database-dynamic-data-masking-get-started-portal.md) | Hvordan du kan komme i gang med SQL-Database dynamiske Data Masking i portalen Azure klassisk |
| 143 | [Konfigurere Azure SQL-Database firewallregler \- oversigt](sql-database-firewall-configure.md) | Lær, hvordan du konfigurerer en SQL-database firewall med serverniveau og database niveau firewallregler til at administrere adgang. |
| 144 | [SQL-Database Selvstudium: oprette SQL-database brugerkonti til at få adgang til og administrere en database](sql-database-get-started-security.md) | Lær at oprette brugerkonti til at få adgang til og til at administrere en database. |
| 145 | [SQL-Database godkendelse og autorisation: give adgang](sql-database-manage-logins.md) | Få mere at vide om administration af SQL-Database sikkerhed, specifikt Sådan administreres database access, og log sikkerhed via kontoen serverniveau vigtigste. |
| 146 | [Retningslinjer for Azure SQL-Database sikkerhed og begrænsninger](sql-database-security-guidelines.md) | Få mere at vide om Microsoft Azure SQL Database retningslinjer og begrænsninger i forbindelse med sikkerhed. |
| 147 | [Komme i gang med SQL-Database truslen registrering](sql-database-threat-detection-get-started.md) | Hvordan du kan komme i gang med SQL-Database truslen registrering i portalen Azure |
| 148 | [Hvordan du kan udføre almindelige administrative opgaver såsom nulstilling af administratoradgangskode i Azure SQL-Database](sql-database-troubleshoot-permissions.md) | Beskriver, hvordan til at udføre almindelige administrative opgaver i SQL-Database. For eksempel nulstilling af administratoradgangskode, give og fjerne adgang. |



## <a name="manage-your-database"></a>Administrere din database

| &nbsp; | Titel | Beskrivelse |
| --: | :-- | :-- |
| 149 | [Introduktion til SQL database overvågning](sql-database-auditing-get-started.md) | Introduktion til SQL database overvågning |
| 150 | [Konfigurere en regel for Azure SQL-Database serverniveau firewall ved hjælp af portalen Azure](sql-database-configure-firewall-settings.md) | Lær, hvordan du konfigurerer firewallen for IP-adresser, få adgang til Azure SQL server. |
| 151 | [Kopiere en Azure SQL-Database ved hjælp af portalen Azure](sql-database-copy-portal.md) | Oprette en kopi af en Azure SQL-database |
| 152 | [Administrere Azure SQL-databaser ved hjælp af Azure automatisering](sql-database-manage-automation.md) | Få mere at vide om, hvordan Azure Automation service kan bruges til at administrere Azure SQL-databaser på skala. |
| 153 | [Oversigt: værktøjer til administration for SQL-Database](sql-database-manage-overview.md) | Sammenligner funktioner og indstillinger for administration af Azure SQL-Database |
| 154 | [Overvågning Azure SQL-Database ved hjælp af dynamiske management visninger](sql-database-monitoring-with-dmvs.md) | Lær at registrere og diagnosticere almindelige problemer med ydeevnen ved hjælp af dynamiske management visninger til at overvåge Microsoft Azure SQL-Database. |
| 155 | [Sikring af SQL-Database](sql-database-security.md) | Få mere at vide om Azure SQL-Database og SQL Server sikkerhed, herunder forskellene mellem i skyen og SQL Server lokalt når det drejer sig om godkendelse, godkendelse, forbindelsessikkerhed, kryptering og overholdelse af angivne standarder. |



## <a name="extended-events"></a>Udvidede begivenheder

| &nbsp; | Titel | Beskrivelse |
| --: | :-- | :-- |
| 156 | [Begivenhed fil target kode for udvidede begivenheder i SQL-Database](sql-database-xevent-code-event-file.md) | Indeholder PowerShell og Transact-SQL til en bestående af to faser kodeeksempel, der viser mål begivenhedsfil i en udvidet begivenhed på Azure SQL-Database. Azure-lager er en påkrævet del af dette scenario. |
| 157 | [Ring til bufferen target kode for udvidede begivenheder i SQL-Database](sql-database-xevent-code-ring-buffer.md) | Indeholder et eksempel på Transact-SQL-kode, der er foretaget nemt og hurtig ved hjælp af Ring bufferen destinationen, i Azure SQL-Database. |
| 158 | [Udvidede begivenheder i SQL-Database](sql-database-xevent-db-diff-from-svr.md) | Beskriver udvidede begivenheder (XEvents) i Azure SQL-Database, og hvordan begivenhed sessioner er lidt anderledes end begivenhed sessioner i Microsoft SQL Server. |



## <a name="geo-replication"></a>Geografisk gentagelse

| &nbsp; | Titel | Beskrivelse |
| --: | :-- | :-- |
| 159 | [Starte en planlagt eller uventet failover til Azure SQL-Database ved hjælp af Azure portal](sql-database-geo-replication-failover-portal.md) | Starte en planlagt eller uventet failover for Azure SQL-Database ved hjælp af portalen Azure |
| 160 | [Starte en planlagt eller uventet failover for Azure SQL-Database med PowerShell](sql-database-geo-replication-failover-powershell.md) | Starte en planlagt eller uventet failover for Azure SQL-Database ved hjælp af PowerShell |
| 161 | [Starte en planlagt eller uventet failover for Azure SQL-Database med Transact-SQL](sql-database-geo-replication-failover-transact-sql.md) | Starte en planlagt eller uventet failover for Azure SQL-Database ved hjælp af Transact-SQL |
| 162 | [Oversigt: SQL aktive geografisk-databasereplikering](sql-database-geo-replication-overview.md) | Aktive geografisk replikering gør det muligt at konfigurere 4 kopier af databasen i en af de Azure datacentre. |
| 163 | [Konfigurere geografisk gentagelse til Azure SQL-Database ved hjælp af Azure portal](sql-database-geo-replication-portal.md) | Konfigurere geografisk gentagelse til Azure SQL-Database ved hjælp af portalen Azure |
| 164 | [Konfigurere geografisk gentagelse til Azure SQL-Database med PowerShell](sql-database-geo-replication-powershell.md) | Konfigurere aktive geografisk-gentagelse til Azure SQL-Database ved hjælp af PowerShell |
| 165 | [Sådan administreres Azure SQL-Database sikkerhed efter nedbrud](sql-database-geo-replication-security-config.md) | Dette emne forklares overvejelser om sikkerheden for at administrere sikkerhed efter gendannelse af en database eller en failover. |
| 166 | [Konfigurere geografisk gentagelse til Azure SQL-Database med Transact-SQL](sql-database-geo-replication-transact-sql.md) | Konfigurere geografisk gentagelse til Azure SQL-Database ved hjælp af Transact-SQL |
| 167 | [Geografisk-Gendan en Azure SQL-Database fra en geografisk overflødige sikkerhedskopi ved hjælp af portalen Azure](sql-database-geo-restore-portal.md) | Geografisk-Gendan en Azure SQL-Database fra en geografisk overflødige sikkerhedskopi (Azure Portal). |
| 168 | [Gendanne en Azure SQL-Database fra en geografisk overflødige sikkerhedskopi ved hjælp af PowerShell](sql-database-geo-restore-powershell.md) | Gendanne en Azure SQL-Database til en ny server fra en geografisk overflødige sikkerhedskopi |



## <a name="upgrade"></a>Opgradere

| &nbsp; | Titel | Beskrivelse |
| --: | :-- | :-- |
| 169 | [Forbedret forespørgselsydelse med kompatibilitet niveau 130 i Azure SQL-Database](sql-database-compatibility-level-query-performance-130.md) | Trin og værktøjer til at afgøre, hvilket kompatibilitetsniveau der passer bedst til din database på Azure SQL-Database eller Microsoft SQL Server |
| 170 | [Administrere rullende opgraderinger af skyen programmer, der bruger SQL aktive geografisk-databasereplikering](sql-database-manage-application-rolling-upgrade.md) | Lær, hvordan du bruger Azure SQL-Database geografisk-replikering til at understøtte online opgraderinger af programmets skyen. |
| 171 | [Opgradering til version 12 Azure SQL-Database ved hjælp af portalen Azure](sql-database-upgrade-server-portal.md) | Beskriver, hvordan du opgradere til Azure SQL-Database version 12 herunder hvordan du kan opgradere Web og Business databaser, og hvordan du opgradere en V11 server overføre dens databaser direkte i en elastiske database puljen, ved hjælp af portalen Azure. |
| 172 | [Opgradering til version 12 Azure SQL-Database ved hjælp af PowerShell](sql-database-upgrade-server-powershell.md) | Beskriver, hvordan du opgradere til Azure SQL-Database version 12 herunder hvordan du kan opgradere Web og Business databaser, og hvordan du opgradere en V11 server overføre dens databaser direkte i en elastiske database puljen, ved hjælp af PowerShell. |
| 173 | [Planlægge og forberede dig på at opgradere til SQL-Database version 12](sql-database-v12-plan-prepare-upgrade.md) | I denne artikel beskrives de forberedelser og begrænsninger, der er involveret i forbindelse med opgradering til version version 12 af Azure SQL-Database. |
| 174 | [Hvad er nyt i SQL-Database version 12](sql-database-v12-whats-new.md) | Beskriver, hvorfor business systemer, der bruger Azure SQL-Database i skyen drage fordel ved at opgradere til version version 12 nu. |
| 175 | [Internettet og Business Edition sunset ofte stillede spørgsmål](sql-database-web-business-sunset-faq.md) | Find ud af, når Azure SQL Web og Business databaserne skal udgå og få mere at vide om funktioner og funktionalitet i de nye tjeneste niveauer. |



## <a name="tools"></a>Værktøjer

| &nbsp; | Titel | Beskrivelse |
| --: | :-- | :-- |
| 176 | [Administrere Azure SQL-Database med PowerShell](sql-database-command-line-tools.md) | Azure SQL-Database management med PowerShell. |
| 177 | [SQL-Database Selvstudium: oprette forbindelse Excel til en Azure SQL-database og oprette en rapport](sql-database-connect-excel.md) | Få mere at vide, hvordan du knytter Microsoft Excel til Azure SQL-database i skyen. Importere data til Excel til rapportering og udforske. |
| 178 | [Oprette en SQL-database og udføre almindelige opgaver til opsætning af databasen med PowerShell-cmdlet'er](sql-database-get-started-powershell.md) | Få mere at vide nu til at oprette en SQL-database med PowerShell. Almindelige database konfigurationsopgaver kan administreres via PowerShell-cmdlet'er. |
| 179 | [Introduktion til Azure SQL Data Sync (Preview)](sql-database-get-started-sql-data-sync.md) | Dette selvstudium hjælper dig med at komme i gang med Azure SQL Data Sync (Preview). |
| 180 | [Administrere Azure SQL-Database ved hjælp af SQL Server Management Studio](sql-database-manage-azure-ssms.md) | Lær, hvordan du bruger SQL Server Management Studio til at administrere SQL-Database servere og databaser. |
| 181 | [Administrere Azure SQL-databaser ved hjælp af portalen Azure](sql-database-manage-portal.md) | Lær at bruge Azure-portalen til at administrere en relationel database i skyen ved hjælp af portalen Azure. |
| 182 | [Ændre service niveau og ydeevne niveauet (priser niveau) for en SQL-database med PowerShell](sql-database-scale-up-powershell.md) | Ændre webtjenesten og præstationsniveau Azure SQL-database viser, hvordan du skalere SQL-database op eller ned med PowerShell. Ændre det priser niveau i en Azure SQL-database med PowerShell. |
| 183 | [Bruge SQL Server Management Studio i Azure RemoteApp til at oprette forbindelse til SQL-Database](sql-database-ssms-remoteapp.md) | Brug dette selvstudium til at se, hvordan du bruger SQL Server Management Studio i Azure RemoteApp for sikkerhed og ydeevne, når der oprettes forbindelse til SQL-Database |



## <a name="reference"></a>Reference

| &nbsp; | Titel | Beskrivelse |
| --: | :-- | :-- |
| 184 | [Dataforbindelsesbiblioteker for SQL-Database og SQL Server](sql-database-libraries.md) | Viser minimumversion antallet for hver driver, som klientprogrammer kan bruge til at oprette forbindelse til Azure SQL-Database eller til Microsoft SQL Server. Der findes et link til versionsoplysninger om drivere, der er udgivet via community'et i stedet for Microsoft. |
| 185 | [Azure SQL-Database ressource begrænsninger](sql-database-resource-limits.md) | Denne side beskrives nogle almindelige ressource grænser for Azure SQL-Database. |
| 186 | [Azure SQL Database Transact-SQL-forskelle](sql-database-transact-sql-information.md) | Transact-SQL-sætninger, der understøttes fuldt mindre end i Azure SQL-Database |



## <a name="miscellaneous"></a>Diverse

| &nbsp; | Titel | Beskrivelse |
| --: | :-- | :-- |
| 187 | [Kopiere en Azure SQL-database ved hjælp af PowerShell](sql-database-copy-powershell.md) | Oprette kopi af en Azure SQL-database ved hjælp af PowerShell |
| 188 | [Kopiere en ved hjælp af Transact-SQL Azure SQL-database](sql-database-copy-transact-sql.md) | Oprette kopi af en ved hjælp af Transact-SQL Azure SQL-database |
| 189 | [Retningslinjer for og Azure SQL Database generelt begrænsninger](sql-database-general-limitations.md) | Denne side beskrives nogle generelle begrænsninger til Azure SQL-Database samt områder af interoperabilitet og support. |
| 190 | [SQL-Database priser niveau anbefalinger](sql-database-service-tier-advisor.md) | Når du ændrer priser er niveauer i portalen Azure, priser niveau anbefalinger, forudsat at anbefalet det niveau, der er bedst egnet til kørsel af en eksisterende Azure SQL Database arbejdsbelastningen. Priser niveauer beskriver service niveau og ydeevne niveauet for en SQL-database. |


&nbsp;

<hr/>

<a name="extras_append"></a>

## <a name="extras"></a>Ekstra

<a name="extra_related_articles"></a>

### <a name="related-articles-from-other-azure-services"></a>Relaterede artikler fra andre Azure tjenester

- [Sikkerhedskopiere din Azure App Services app i Azure](../app-service-web/web-sites-backup.md)

<a name="extra_documentation_tools"></a>

### <a name="documentation-tools"></a>Dokumentationsværktøjer

- [Opdateringer offentliggjort til Azure SQL-Database](http://azure.microsoft.com/updates/?service=sql-database)

- [Søge i alle dokumentation typerne Microsoft Azure med filtre](http://azure.microsoft.com/search/documentation/)

<a name="extra_learning_path_graphics"></a>

### <a name="learning-path-graphics"></a>Learning sti grafik

- [Learning sti grafik for alle Microsoft Azure-tjenester](http://azure.microsoft.com/documentation/learning-paths/)

- [Læringssti: Få mere at vide Azure SQL-Database](http://azure.microsoft.com/documentation/learning-paths/sql-database-training-learn-sql-database/)

- [Læringssti: SQL-Database elastiske skala](http://azure.microsoft.com/documentation/learning-paths/sql-database-elastic-scale/)


<!--
AzIxAA.ExtraAppend.sql-database.txt
C:\_MainW\VS15\AzureIndexAllArticles\AzureIndexAllArticles\In-Out\In\

This bullet link is improperly disallowed by publishing automation due to presence of string 'docuXXmentation/arXXticles':
- [Search SQL Database documentation, with filters](http://azure.microsoft.com/docuXXmentation/arXXticles/?service=sql-database)
-->


