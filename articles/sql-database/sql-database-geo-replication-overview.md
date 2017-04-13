<properties
    pageTitle="Aktive geografisk-gentagelse til Azure SQL-Database"
    description="Aktive geografisk replikering gør det muligt at konfigurere 4 kopier af databasen i en af de Azure datacentre."
    services="sql-database"
    documentationCenter="na"
    authors="stevestein"
    manager="jhubbard"
    editor="monicar" />


<tags
    ms.service="sql-database"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="NA"
    ms.date="09/26/2016"
    ms.author="sstein" />

# <a name="overview-sql-database-active-geo-replication"></a>Oversigt: SQL aktive geografisk-databasereplikering

Aktive geografisk replikering gør det muligt at konfigurere op til fire læsbare sekundær databaser i samme eller forskellige data center placeringer (områder). Sekundær databaser er tilgængelig til forespørgsler og failover i forbindelse med en data center afbrydelse eller med at oprette forbindelse til den primære database.

>[AZURE.NOTE] Aktive geografisk gentagelse (læsbare secondaries) er nu tilgængelig for alle databaser i alle niveauer i tjenesten. Den ikke kan læse sekundær type skal udgå i April 2017 og eksisterende ikke kan læses databaser vil automatisk blive opgraderet til læsbare secondaries.

 Du kan konfigurere aktive geografisk-replikering ved hjælp af [Azure-portalen](sql-database-geo-replication-portal.md), [PowerShell](sql-database-geo-replication-powershell.md), [Transact-SQL](sql-database-geo-replication-transact-sql.md), eller den [REST-API - Opret eller Opdater Database](https://msdn.microsoft.com/library/azure/mt163685.aspx).

> [AZURE.SELECTOR]
- [Konfigurere: Azure portal](sql-database-geo-replication-portal.md)
- [Konfigurere: PowerShell](sql-database-geo-replication-powershell.md)
- [Konfigurere: T-SQL](sql-database-geo-replication-transact-sql.md)

Hvis for et årsag din primære database opstår, eller blot skal gøres tilgængelige offline, du kan *skifte* til en af dine sekundær databaser. Når failover er aktiveret til en af de sekundære databaser, er alle andre secondaries automatisk sammenkædes med den nye primære.

Du kan skifte til et sekundært ved hjælp af [Azure-portalen](sql-database-geo-replication-failover-portal.md), [PowerShell](sql-database-geo-replication-failover-powershell.md), [Transact-SQL](sql-database-geo-replication-failover-transact-sql.md), [REST-API - planlagt Failover](https://msdn.microsoft.com/ibrary/azure/mt575007.aspx)eller [REST-API - ikke-planlagt Failover](https://msdn.microsoft.com/library/azure/mt582027.aspx).


> [AZURE.SELECTOR]
- [Failover: Azure portal](sql-database-geo-replication-failover-portal.md)
- [Failover: PowerShell](sql-database-geo-replication-failover-powershell.md)
- [Failover: T-SQL](sql-database-geo-replication-failover-transact-sql.md)

Kontrollér godkendelseskrav til din server og database er konfigureret på den nye primære efter failover. Yderligere oplysninger finder du [SQL-Database sikkerhed efter nedbrud](sql-database-geo-replication-security-config.md).


Funktionen aktive geografisk replikering implementerer en funktion for at give database redundans i den samme Microsoft Azure-område eller i forskellige områder (geografisk redundans). Aktive geografisk replikering kopieres asynkront bekræftede transaktioner fra en database til op til fire kopier af databasen på forskellige servere, ved hjælp af læse anvendt øjebliksbillede isolationsniveauet (RCSI) til isolation. Når aktive geografisk replikering er konfigureret, oprettes en sekundær database på den angivne server. Den oprindelige database, bliver den primære database. Den primære database kopieres asynkront bekræftede transaktioner til hver af de sekundære databaser. Kun fuld transaktioner replikeres. Mens du er på et bestemt tidspunkt, den sekundære database kan være en smule bag den primære database, sekundær dataene er garanti for, at du aldrig behøver delvis transaktioner. Du kan finde de frigivne Produktionsordre data på [Oversigt Forretningskontinuitet](sql-database-business-continuity.md).

En af de primære fordele af aktive geografisk-replikering er, at det giver en løsning til databasen niveau nedbrud genoprettelse med lav gendannelse tid. Når du placerer den sekundære database på en server i et andet område, kan du tilføje maksimale spændstighed i dit program. Tværs område redundans gør det muligt for programmer til at gendanne fra en permanent tab af et datacenter i hele eller dele af et datacenter, der er forårsaget af naturkatastrofer, katastrofal human fejl eller skadelige handlinger. I følgende figur vises et eksempel af aktive geografisk-gentagelse konfigureret med en primær i område nord Central os og sekundære i område syd Central os.

![Geografisk gentagelse relation](./media/sql-database-active-geo-replication/geo-replication-relationship.png)

En anden fordel er, at de sekundære databaser kan læses og kan bruges til at offload skrivebeskyttet arbejdsmængder som rapportering job. Hvis du kun vil bruge den sekundære database til justering af belastning, kan du oprette den i det samme område som primært. Oprette en sekundær i samme region, øger ikke programmets spændstighed til alvorlige fejl.  

Andre scenarier, hvor Active geografisk replikering kan bruges, omfatter:

- **Database overførsel**: Du kan bruge aktive geografisk-gentagelse til at overflytte en database fra en server til en anden online med mindste nedetid.
- **Programmet opgraderinger**: Du kan oprette en ekstra sekundær som en fejl tilbage kopi under programmet opgraderinger.

For at opnå reelle Forretningskontinuitet, tilføjelse af database redundans mellem datacentre er kun en del af løsningen. Gendanne et program (service) til en komplet, når opstår nedbrud kræver gendannelse af alle komponenter, der udgør tjenesten og eventuelle afhængige tjenester. Eksempler på disse komponenter omfatte klientsoftware (for eksempel en browser med et brugerdefineret JavaScript), webfrontends, lager og DNS. Det er vigtigt, at alle komponenter er tolerant for de samme fejl og bliver tilgængelige i formålet gendannelse tid (RTO) med dit program. Derfor skal du identificere alle afhængige tjenester og forstå garantier og de giver-funktioner. Derefter skal du udføre passende skridt for at sikre, at din tjeneste funktioner under sekundære af tjenesterne, som den afhænger af. Du kan finde flere oplysninger om design-løsninger til nedbrud, [Designe skyen løsninger til nedbrud gendannelse ved hjælp af aktive geografisk-replikering](sql-database-designing-cloud-solutions-for-disaster-recovery.md).

## <a name="active-geo-replication-capabilities"></a>Aktive geografisk gentagelse-funktioner
Funktionen aktive geografisk replikering indeholder følgende vigtige funktioner:

- **Automatisk asynkron gentagelse**: Du kan kun oprette en sekundær database ved at føje til en eksisterende database. Sekundært kan kun oprettes i en anden Azure SQL Database-server. Når oprettet, udfyldes sekundær databasen med de data, der er kopieret fra den primære database. Denne proces kaldes også forhåndsudfyldning. Når sekundær databasen er oprettet og ud, replikeres opdateringer til den primære database asynkront til sekundær databasen automatisk. Asynkron gentagelse betyder, at transaktioner allokeres på den primære database, før de replikeres til den sekundære database. 

- **Flere sekundære databaser**: to eller flere sekundære databaser øge redundans og niveau for beskyttelse af den primære database og programmet. Hvis der findes flere sekundære databaser, forbliver programmet beskyttet, selv hvis en af de sekundære databaser mislykkes. Hvis der kun er én sekundær database, og det ikke lykkes, vises programmet til større risiko, indtil der oprettes en ny sekundær database.

- **Læsbare sekundær databaser**: et program kan få adgang til en sekundær database til skrivebeskyttede handlinger ved hjælp af de samme eller forskellige sikkerhed principper for bruges til at få adgang til den primære database. Sekundær databaser betjene i øjebliksbillede isolationsniveauet tilstand for at sikre gentagelse af opdateringerne af primært (log genafspilning) ikke er forsinket af forespørgsler, der er udført på sekundært.

>[AZURE.NOTE] Log genafspilning er forsinkede på den sekundære database, hvis der er skemaopdateringer, som den modtager fra primært, der kræver en skema Lås på den sekundære database.

- **Aktive geografisk-gentagelse af elastiske puljen databaser**: aktive geografisk replikering kan konfigureres til en database i en hvilken som helst elastiske database puljen. Sekundær databasen kan være i en anden elastiske database gruppe. Sekundært kan være en elastiske database puljen og omvendt omvendt for almindelige databaser, så længe tjenesten niveauer er de samme. 

- **Konfigurerbar præstationsniveau sekundær databasen**: en sekundær database kan oprettes med lavere ydeevne end primært. Både primære og sekundære databaser skal have samme service niveau. Denne indstilling anbefales ikke for programmer med høj database Skriv aktiviteter, fordi den øget gentagelse mellemliggende øger risikoen for væsentlig datatab efter en failover. Desuden efter failover påvirkes programmets ydeevne før den nye primære er opgraderet til et højere ydeevneniveau. Log EY procentdel diagram på Azure-portalen indeholder en god metode til at anslå den sekundære, der skal bruges til at opretholde gentagelse Indlæs minimale præstationsniveau. Hvis din primære database er P6 eksempelvis (1000 DTU) og dens log EY procent er 50% sekundært skal være mindst P4 (500 DTU). Du kan også hente log EY data ved hjælp af [sys.resource_stats](https://msdn.microsoft.com/library/dn269979.aspx) eller [sys.dm_db_resource_stats]( https://msdn.microsoft.com/library/dn800981.aspx) databasevisninger.  Se [Indstillinger for SQL-Database og ydeevne](sql-database-service-tiers.md)kan finde flere oplysninger om de SQL-Database ydeevneniveauer. 

- **Bruger-kontrolleres failover og failback**: en sekundær database kan eksplicit skiftes til den primære rolle når som helst af programmet eller brugeren. Under et reelt tal afbrydelse skal indstillingen "ikke-planlagt" bruges, som med det samme gør reklame for en sekundær skal være primært. Når den mislykkede primære genopretter og er tilgængelig igen, markerer det gendannede primære som en sekundær systemet automatisk, og finde den opdaterede med den nye primære. På grund af replikering asynkron art, kan en lille mængde data gå tabt under ikke-planlagt failover Hvis en primær mislykkes, før den kopieres de seneste ændringer til sekundært. Når en primær med flere secondaries ikke, systemet automatisk omkonfigurerer gentagelse relationer og links resterende secondaries til den netop overførte primære uden brugerinput. Når den afbrydelse, der forårsagede sekundære afhjulpet, kan det være hensigtsmæssigt at returnere programmet til det primære område. Det gør skal kommandoen failover startes med indstillingen "planlagt". 

- **Holde styr på legitimationsoplysninger og firewallregler synkroniseret**: Vi anbefaler, at du bruger [database firewallregler](sql-database-firewall-configure.md) for geografisk replikerede databaser så disse regler kan replikeres til databasen til at sikre, at alle sekundær databaser har de samme firewallregler som primært. Denne metode fjerner behovet for kunder til at konfigurere og vedligeholde firewallregler på servere, der indeholder både de primære og sekundære databaser manuelt. På samme måde, ved hjælp af [indeholdt databasebrugere](sql-database-manage-logins.md) for dataadgang sikrer, at både primære og sekundære databaser har altid den samme bruger legitimationsoplysninger det under en failover, der er ikke nogen afbrydelser på grund af uoverensstemmelser med logon og adgangskoder. Med tilføjelsen af [Azure Active Directory](../active-directory/active-directory-whatis.md), kan kunder administrere brugeradgang til både primære og sekundære databaser og fjerner behovet for administration af legitimationsoplysninger i databaser helt fra.

## <a name="upgrading-or-downgrading-a-primary-database"></a>Opgradering eller nedgradere en primær database
Du kan opgradere eller nedgradere en primær database til en anden ydeevneniveau (inden for samme service niveau) uden at afbryde nogen sekundær databaser. Når du opgraderer, anbefaler vi, at du opgraderer sekundær databasen først og derefter opgradere primært. Når nedgradere, i omvendt rækkefølge: nedgradere primært først og derefter nedgradere sekundært. 

Sekundær databasen skal være i samme service niveau som primært, så overføre din primære database til en anden webtjenesten kræver, at du enten geografisk gentagelse linket Afslut og omdøbe den sekundære database eller blot slippe den. Derefter overføre primært til den nye webtjenesten og omkonfigurere geografisk gentagelse. Din nye sekundær oprettes automatisk med det samme ydeevneniveau som primært som standard.

## <a name="preventing-the-loss-of-critical-data"></a>Forhindre tab af kritiske data
På grund af lang ventetid af WAN-netværk bruger fortløbende Kopiér en asynkron replikering fungerer. Asynkron gentagelse gør datatab undgås, hvis der opstår fejl. Nogle programmer kan dog kræver ingen tab af data. For at beskytte disse vigtige opdateringer, kan et programudvikler ringe [sp_wait_for_database_copy_sync](https://msdn.microsoft.com/library/dn467644.aspx) system procedure umiddelbart efter gemmer posten. Kalder **sp_wait_for_database_copy_sync** blokke opkald tråd, indtil den sidste anvendt post er blevet replikeres til den sekundære database. Fremgangsmåden skal vente, indtil alle i kø transaktioner er blevet accepteret af sekundær databasen. **sp_wait_for_database_copy_sync** er fastsat til en bestemt fortløbende Kopiér link. Alle brugere med de forbindelse rettigheder til den primære database kan ringe til denne procedure.

>[AZURE.NOTE] Forsinkelsen skyldes en **sp_wait_for_database_copy_sync** procedure Calls kan være betydeligt. Forsinkelsen afhænger af størrelsen på transaktion log længden på tidspunktet, og dette opkald returnerer ikke, indtil hele loggen replikeres. Undgå at ringe denne procedure medmindre absolut nødvendigt.

## <a name="programmatically-managing-active-geo-replication"></a>Administrere aktive geografisk-gentagelse fra et program

Som tidligere nævnt kan aktive geografisk replikering også administreres fra et program ved hjælp af Azure PowerShell og REST-API. I følgende tabel beskrives sæt af tilgængelige kommandoer.

- **Azure ressourcestyring API og rollebaseret sikkerhed**: aktive geografisk-replikering omfatter et sæt [Azure ressourcestyring API'er]( https://msdn.microsoft.com/library/azure/mt163571.aspx) til at administrere, herunder [ressourcestyring Azure-baserede PowerShell-cmdlet'er](sql-database-geo-replication-powershell.md). Disse API'er kræver brug af grupper og understøtter rollebaseret sikkerhed (RBAC). Finde flere oplysninger om, hvordan du implementere access roller [Azure Role-Based adgangskontrol](../active-directory/role-based-access-control-configure.md).

>[AZURE.NOTE] Mange nye funktioner i aktive geografisk-replikering understøttes kun ved hjælp af [Azure ressourcestyring](../azure-resource-manager/resource-group-overview.md) baseret på [Azure SQL REST-API](https://msdn.microsoft.com/library/azure/mt163571.aspx) og [Azure SQL Database PowerShell-cmdlet'er](https://msdn.microsoft.com/library/azure/mt574084.aspx). (Klassisk) REST-API] (https://msdn.microsoft.com/library/azure/dn505719.aspx) og [Azure SQL-Database (klassisk) cmdletter](https://msdn.microsoft.com/library/azure/dn546723.aspx) understøttes af hensyn til bagudkompatibiliteten, så ved hjælp af de ressourcestyring Azure-baserede API'er er anbefalet. 


### <a name="transact-sql"></a>Transact-SQL

|Kommandoen|Beskrivelse|
|-------|-----------|
|[ÆNDRE DATABASE (Azure SQL-Database)](https://msdn.microsoft.com/library/mt574871.aspx)|Bruge Tilføj sekundær til SERVER argument til at oprette en sekundær database til en eksisterende database og starter data gentagelse|
|[ÆNDRE DATABASE (Azure SQL-Database)](https://msdn.microsoft.com/library/mt574871.aspx)|Brug FAILOVER eller FORCE_FAILOVER_ALLOW_DATA_LOSS til at skifte en sekundær database for at være primære at starte failover
|[ÆNDRE DATABASE (Azure SQL-Database)](https://msdn.microsoft.com/library/mt574871.aspx)|Bruge fjerne sekundære på SERVER til at afslutte en replikering af data mellem en SQL-Database og den angivne sekundære database.|
|[sys.geo_replication_links (Azure SQL-Database)](https://msdn.microsoft.com/library/mt575501.aspx)|Returnerer oplysninger om alle eksisterende gentagelse kæder for hver database på den logiske Azure SQL Database-server.|
|[sys.dm_geo_replication_link_status (Azure SQL-Database)](https://msdn.microsoft.com/library/mt575504.aspx)|Henter sidst gentagelse, sidste gentagelse mellemliggende og andre oplysninger om linket gentagelse for en given SQL-database.|
|[sys.dm_operation_status (Azure SQL-Database)](https://msdn.microsoft.com/library/dn270022.aspx)|Viser status for alle databasefunktioner, herunder status for gentagelse links.|
|[sp_wait_for_database_copy_sync (Azure SQL-Database)](https://msdn.microsoft.com/library/dn467644.aspx)|bevirker, at programmet vente, indtil alle bekræftede transaktioner replikeres og bekræftet af den aktive sekundære database.|
||||

### <a name="powershell"></a>PowerShell

|Cmdlet|Beskrivelse|
|------|-----------|
|[Get-AzureRmSqlDatabase](https://msdn.microsoft.com/en-us/library/azure/mt603648.aspx)|Henter en eller flere databaser.|
|[Ny AzureRmSqlDatabaseSecondary](https://msdn.microsoft.com/library/mt603689.aspx)|Opretter en sekundær database til en eksisterende database og starter replikering af data.|
|[Angiv AzureRmSqlDatabaseSecondary](https://msdn.microsoft.com/en-us/library/mt619393.aspx)|Skifter en sekundær database for at være primære at starte failover.|
|[Fjern AzureRmSqlDatabaseSecondary](https://msdn.microsoft.com/en-us/library/mt603457.aspx)|Afslutter replikering af data mellem en SQL-Database og den angivne sekundære database.|
|[Get-AzureRmSqlDatabaseReplicationLink](https://msdn.microsoft.com/library/mt619330.aspx)|Får geografisk gentagelse links mellem Azure SQL-Database og en ressourcegruppe eller SQL Server.|
||||

### <a name="rest-api"></a>REST-API

|API|Beskrivelse|
|---|-----------|
|[Oprette eller Opdater Database (createMode = Gendan)](https://msdn.microsoft.com/library/azure/mt163685.aspx)|Opretter, opdaterer eller gendanner en primær eller en sekundær database.|
|[Få oprette eller opdatere Databasestatus](https://msdn.microsoft.com/library/azure/mt643934.aspx)|Returnerer status under handlingen Opret.|
|[Angive sekundær Database som primær (planlagte Failover)](https://msdn.microsoft.com/ibrary/azure/mt575007.aspx)|Hæve en sekundær database i en geografisk gentagelse partnerskab for at blive den nye primære database.|
|[Angive sekundær Database som primær (ikke-planlagt Failover)](https://msdn.microsoft.com/library/azure/mt582027.aspx)|Gennemtvinge en failover til den sekundære database og angive sekundært som primært.|
|[Hent link til gentagelse](https://msdn.microsoft.com/library/azure/mt600929.aspx)|Henter alle gentagelse links til en given SQL-database i et geografisk gentagelse partnerskab. Den henter de oplysninger, der er synlige i visningen sys.geo_replication_links katalog.|
|[Hent gentagelse Link](https://msdn.microsoft.com/library/azure/mt600778.aspx)|Henter en bestemt gentagelse link til en given SQL-database i et geografisk gentagelse partnerskab. Den henter de oplysninger, der er synlige i visningen sys.geo_replication_links katalog.|
||||



## <a name="next-steps"></a>Næste trin

- Se [Oversigt over Business løbende](sql-database-business-continuity.md) til en business løbende oversigt og scenarier
- Hvis du vil vide mere om Azure SQL-Database, der automatisk sikkerhedskopiering, se [SQL-Database automatisk sikkerhedskopiering](sql-database-automated-backups.md).
- For at få mere for at vide om brug af automatiseret sikkerhedskopier til gendannelse, kan du se [gendanne en database fra de service-definerede sikkerhedskopier](sql-database-recovery-using-backups.md).
- Få vist [databasekopi](sql-database-copy.md)for at få mere for at vide om at bruge Automatiseret sikkerhedskopier til at arkivere.
- Få om af godkendelseskrav til en ny primære server og database under [SQL-Database sikkerhed efter nedbrud](sql-database-geo-replication-security-config.md).
