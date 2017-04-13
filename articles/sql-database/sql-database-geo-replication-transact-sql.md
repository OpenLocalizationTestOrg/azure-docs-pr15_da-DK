<properties
    pageTitle="Konfigurere geografisk gentagelse til Azure SQL-Database med Transact-SQL | Microsoft Azure"
    description="Konfigurere geografisk gentagelse til Azure SQL-Database ved hjælp af Transact-SQL"
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
    ms.workload="NA"
    ms.date="10/13/2016"
    ms.author="carlrab"/>

# <a name="configure-geo-replication-for-azure-sql-database-with-transact-sql"></a>Konfigurere geografisk gentagelse til Azure SQL-Database med Transact-SQL

> [AZURE.SELECTOR]
- [Oversigt](sql-database-geo-replication-overview.md)
- [Azure-portalen](sql-database-geo-replication-portal.md)
- [PowerShell](sql-database-geo-replication-powershell.md)
- [T-SQL](sql-database-geo-replication-transact-sql.md)

I denne artikel beskrives, hvordan du konfigurerer aktive geografisk-gentagelse til Azure SQL-Database med Transact-SQL.

For at starte failover ved hjælp af Transact-SQL, skal du se [Påbegynd en planlagt eller uventet sekundær server til Azure SQL-Database med Transact-SQL](sql-database-geo-replication-failover-transact-sql.md).

>[AZURE.NOTE] Aktive geografisk gentagelse (læsbare secondaries) er nu tilgængelig for alle databaser i alle niveauer i tjenesten. I April 2017 typen ikke kan læses sekundær skal udgå og eksisterende ikke kan læses databaser vil automatisk blive opgraderet til læsbare secondaries.

Konfigurere aktive geografisk replikering ved hjælp af Transact-SQL, du skal bruge følgende:

- Et Azure-abonnement.
- En logisk Azure SQL-databaseserver <MyLocalServer> og en SQL-database <MyDB> -den primære database, du vil gentage.
- En eller flere logiske Azure SQL-Database servere < MySecondaryServer(n) > - logiske servere, der vil være de partner-servere, hvor du vil oprette sekundær databaser.
- Et logon, der er DBManager på primært har db_ownership af den lokale database, du vil geografisk-replikeres, og være DBManager på den partner servere, kan du konfigurere geografisk gentagelse.
- SQL Server Management Studio (SSMS)

> [AZURE.IMPORTANT] Det anbefales, at du altid bruger den nyeste version af Management Studio skal være synkroniseret med opdateringer til Microsoft Azure og SQL-Database. [Opdatere SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290.aspx).


## <a name="add-secondary-database"></a>Tilføje sekundær database

Du kan bruge sætningen **JUSTER DATABASE** til at oprette en sekundær geografisk replikerede database på en partnerserver. Du kan udføre denne sætning på master databasen på den server, der indeholder databasen, der skal replikeres. Den geografisk replikerede database (den "primære database"), har samme navn som den database, replikeres og får som standard samme serviceniveau som den primære database. Sekundær databasen kan være læsbare eller ikke kan læses, og det kan være en enkelt database eller en elastiske databbase. Få mere at vide under [JUSTER DATABASE (Transact-SQL)](https://msdn.microsoft.com/library/mt574871.aspx) og [Service niveauer](sql-database-service-tiers.md).
Når sekundær databasen er oprettet og ud, begynder data replikering asynkront fra den primære database. Følgende fremgangsmåde beskriver, hvordan du konfigurerer geografisk gentagelse med Management Studio. Trin til at oprette ikke kan læses og læsevenligt secondaries, enten med en enkelt database eller en elastiske database, der leveres.

> [AZURE.NOTE] Hvis der findes en database på den angivne partnerserver med samme navn som den primære database kommandoen mislykkes.


### <a name="add-non-readable-secondary-single-database"></a>Tilføje ikke kan læses sekundær (enkelt database)

Brug følgende trin til at oprette en ikke-læsbar sekundær som en enkelt database.

1. Ved hjælp af version 13.0.600.65 eller en nyere version af SQL Server Management Studio.

     > [AZURE.IMPORTANT] Hent den [nyeste](https://msdn.microsoft.com/library/mt238290.aspx) version af SQL Server Management Studio. Det anbefales, at du altid bruger den nyeste version af Management Studio skal være synkroniseret med opdateringer til Azure-portalen.


2. Åbn mappen databaser, Udvid mappen **Systemdatabaser** , skal du højreklikke på **master**og derefter klikke på **Ny forespørgsel**.

3. Bruge sætningen følgende **JUSTER DATABASE** til at lave en lokal database til en geografisk-replikering primære med en sekundær ikke kan læses database på MySecondaryServer1, hvor MySecondaryServer1 er navnet på din fulde server.

        ALTER DATABASE <MyDB>
           ADD SECONDARY ON SERVER <MySecondaryServer1> WITH (ALLOW_CONNECTIONS = NO);

4. Klik på **Udfør** for at køre forespørgslen.


### <a name="add-readable-secondary-single-database"></a>Tilføje læsbare sekundær (enkelt database)
Brug følgende trin til at oprette et læsevenligt sekundært som en enkelt database.

1. Oprette forbindelse til din logiske server Azure SQL-Database i Management Studio.

2. Åbn mappen databaser, Udvid mappen **Systemdatabaser** , skal du højreklikke på **master**og derefter klikke på **Ny forespørgsel**.

3. Bruge sætningen følgende **JUSTER DATABASE** til at lave en lokal database til en geografisk-replikering primære med et læsevenligt sekundær database på en sekundær server.

        ALTER DATABASE <MyDB>
           ADD SECONDARY ON SERVER <MySecondaryServer2> WITH (ALLOW_CONNECTIONS = ALL);

4. Klik på **Udfør** for at køre forespørgslen.



### <a name="add-non-readable-secondary-elastic-database"></a>Tilføje ikke kan læses sekundær (elastiske database)

Brug følgende trin til at oprette en ikke-læsbar sekundær som en elastiske database.

1. Oprette forbindelse til din logiske server Azure SQL-Database i Management Studio.

2. Åbn mappen databaser, Udvid mappen **Systemdatabaser** , skal du højreklikke på **master**og derefter klikke på **Ny forespørgsel**.

3. Bruge sætningen følgende **JUSTER DATABASE** til at lave en lokal database til en geografisk-replikering primære med en sekundær ikke kan læses database på en sekundær server i en elastiske puljen.

        ALTER DATABASE <MyDB>
           ADD SECONDARY ON SERVER <MySecondaryServer3> WITH (ALLOW_CONNECTIONS = NO
           , SERVICE_OBJECTIVE = ELASTIC_POOL (name = MyElasticPool1));

4. Klik på **Udfør** for at køre forespørgslen.



### <a name="add-readable-secondary-elastic-database"></a>Tilføje læsbare sekundær (elastiske database)
Brug følgende trin til at oprette et læsevenligt sekundært som en elastiske database.

1. Oprette forbindelse til din logiske server Azure SQL-Database i Management Studio.

2. Åbn mappen databaser, Udvid mappen **Systemdatabaser** , skal du højreklikke på **master**og derefter klikke på **Ny forespørgsel**.

3. Bruge sætningen følgende **JUSTER DATABASE** til at lave en lokal database til en geografisk-replikering primære med en sekundær læsbare database på en sekundær server i en elastiske puljen.

        ALTER DATABASE <MyDB>
           ADD SECONDARY ON SERVER <MySecondaryServer4> WITH (ALLOW_CONNECTIONS = ALL
           , SERVICE_OBJECTIVE = ELASTIC_POOL (name = MyElasticPool2));

4. Klik på **Udfør** for at køre forespørgslen.



## <a name="remove-secondary-database"></a>Fjerne sekundære database

Du kan bruge sætningen **ALTER DATABASE** til permanent ophæve tilknytningen af replikering mellem en sekundær database og dens primær. Denne sætning er udført på master databasen, som den primære database er placeret. Når relation bliver den sekundære database en almindelig læse / skrive-database. Hvis forbindelsen til sekundær database er brudt kommandoen blev fuldført, men sekundært bliver skrivebeskyttet, når forbindelsen er genoprettet. Få mere at vide under [JUSTER DATABASE (Transact-SQL)](https://msdn.microsoft.com/library/mt574871.aspx) og [Service niveauer](sql-database-service-tiers.md).

Brug følgende trin til at fjerne geografisk replikerede sekundær fra et geografisk gentagelse partnerskab.

1. Oprette forbindelse til din logiske server Azure SQL-Database i Management Studio.

2. Åbn mappen databaser, Udvid mappen **Systemdatabaser** , skal du højreklikke på **master**og derefter klikke på **Ny forespørgsel**.

3. Bruge sætningen følgende **JUSTER DATABASE** til at fjerne en geografisk replikerede sekundær.

        ALTER DATABASE <MyDB>
           REMOVE SECONDARY ON SERVER <MySecondaryServer1>;

4. Klik på **Udfør** for at køre forespørgslen.

## <a name="monitor-geo-replication-configuration-and-health"></a>Konfiguration af skærme geografisk gentagelse og tilstand

Overvågning opgaver omfatter overvågning af geografisk gentagelse konfigurationen og overvåger data gentagelse tilstand.  Du kan bruge visningen **sys.dm_geo_replication_links** dynamisk administration i den overordnede database til at returnere oplysninger om alle eksisterende gentagelse links for hver database på den logiske Azure SQL Database-server. Denne visning indeholder en række for hver af replikering sammenkædningen mellem primære og sekundære databaser. Du kan bruge visningen **sys.dm_replication_link_status** dynamisk administration til at returnere en række for hver Azure SQL-Database, der er i øjeblikket deltager i et gentagelse gentagelse link. Dette omfatter både primære og sekundære databaser. Hvis der findes mere end én sammenhængende gentagelse link til en given primære database, indeholder denne tabel en række for hver af relationer. Visningen er oprettet i alle databaser, herunder den logiske master. Men forespørgsler denne visning i den logiske master returnerer et tomt sæt. Du kan bruge visningen **sys.dm_operation_status** dynamisk administration til at få vist status for alle databasefunktioner, herunder status for gentagelse links. Yderligere oplysninger finder du se [sys.geo_replication_links (Azure SQL-Database)](https://msdn.microsoft.com/library/mt575501.aspx), [sys.dm_geo_replication_link_status (Azure SQL-Database)](https://msdn.microsoft.com/library/mt575504.aspx)og [sys.dm_operation_status (Azure SQL-Database)](https://msdn.microsoft.com/library/dn270022.aspx).

Brug følgende trin til at overvåge et geografisk gentagelse partnerskab.

1. Oprette forbindelse til din logiske server Azure SQL-Database i Management Studio.

2. Åbn mappen databaser, Udvid mappen **Systemdatabaser** , skal du højreklikke på **master**og derefter klikke på **Ny forespørgsel**.

3. Brug følgende sætning for at vise alle databaser med geografisk gentagelse links.

        SELECT database_id, start_date, modify_date, partner_server, partner_database, replication_state_desc, role, secondary_allow_connections_desc FROM [sys].geo_replication_links;

4. Klik på **Udfør** for at køre forespørgslen.
5. Åbn mappen databaser, Udvid mappen **Systemdatabaser** , skal du højreklikke på **MyDB**, og klik derefter på **Ny forespørgsel**.
6. Brug følgende sætning for at vise gentagelse LAG'er og sidste replikering tid af mine sekundær databaser af MyDB.

        SELECT link_guid, partner_server, last_replication, replication_lag_sec FROM sys.dm_geo_replication_link_status

7. Klik på **Udfør** for at køre forespørgslen.
8. Brug følgende sætning til at vise de seneste geografisk gentagelse handlinger, der er knyttet til databasen MyDB.

        SELECT * FROM sys.dm_operation_status where major_resource_id = 'MyDB'
        ORDER BY start_time DESC

9. Klik på **Udfør** for at køre forespørgslen.

## <a name="upgrade-a-non-readable-secondary-to-readable"></a>Opgradere en ikke-læsbar sekundær til læsbare

I April 2017 typen ikke kan læses sekundær skal udgå og eksisterende ikke kan læses databaser vil automatisk blive opgraderet til læsbare secondaries. Hvis du bruger ikke kan læses secondaries i dag og vil opgradere dem for at kunne læses, kan du bruge følgende enkle trin for hver sekundær.

> [AZURE.IMPORTANT] Der findes ingen selvbetjening metode af lokal opgradering af en ikke-læsbar sekundær til læsbare. Hvis du slipper dine kun sekundær, derefter den primære database, forbliver ubeskyttet indtil den nye sekundære er synkroniseret fuldt ud. Hvis dit program SLA kræver, at primært altid er beskyttet, skal du overveje at oprette en parallelle sekundær i en anden server før du anvender opgradering trinnene ovenfor. Bemærk, at hver primær kan have op til 4 sekundær databaser.


1. Først skal du oprette forbindelse til den *sekundære* server og slip ikke kan læses sekundær databasen:  
        
        DROP DATABASE <MyNonReadableSecondaryDB>;

2. Nu oprette forbindelse til den *primære* server og tilføje en ny læsbare sekundær

        ALTER DATABASE <MyDB>
            ADD SECONDARY ON SERVER <MySecondaryServer> WITH (ALLOW_CONNECTIONS = ALL);




## <a name="next-steps"></a>Næste trin

- Hvis du vil vide mere om aktive geografisk-gentagelse, se - [Aktive geografisk-gentagelse](sql-database-geo-replication-overview.md)
- Se [Oversigt over Business løbende](sql-database-business-continuity.md) til en business løbende oversigt og scenarier
