<properties 
    pageTitle="Administrere en SQL-Database med SSMS | Microsoft Azure" 
    description="Lær, hvordan du bruger SQL Server Management Studio til at administrere SQL-Database servere og databaser." 
    services="sql-database" 
    documentationCenter=".net" 
    authors="stevestein" 
    manager="jhubbard" 
    editor="tysonn"/>

<tags 
    ms.service="sql-database" 
    ms.workload="data-management" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="09/29/2016" 
    ms.author="sstein"/>


# <a name="managing-azure-sql-database-using-sql-server-management-studio"></a>Administrere Azure SQL-Database ved hjælp af SQL Server Management Studio 


> [AZURE.SELECTOR]
- [Azure-portalen](sql-database-manage-portal.md)
- [SSMS](sql-database-manage-azure-ssms.md)
- [PowerShell](sql-database-manage-powershell.md)

Du kan bruge SQL Server Management Studio (SSMS) til at administrere Azure SQL-Database servere og databaser. Dette emne fører dig gennem almindelige opgaver med SSMS. Du bør allerede har en server og database, der er oprettet i Azure SQL-Database, inden du går i gang. Se [oprette din første Azure SQL-Database](sql-database-get-started.md) , og [Opret forbindelse og forespørgsel, der bruger SSMS](sql-database-connect-query-ssms.md) kan finde flere oplysninger.

Det anbefales, at du bruger den nyeste version af SSMS, når du arbejder med Azure SQL-Database. 

> [AZURE.IMPORTANT] Brug altid den seneste version af SSMS, fordi det er kontinuerligt forbedret, hvis du vil arbejde med de seneste opdateringer til Azure og SQL-Database. For at få den nyeste version skal du se [Hente SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290.aspx).



## <a name="create-and-manage-azure-sql-databases"></a>Oprette og administrere Azure SQL-databaser

Mens forbindelse til **master** databasen, kan du oprette databaser på serveren og ændre eller slip eksisterende databaser. Følgende trin beskriver, hvordan du udfører flere almindelige database administrationsopgaver gennem Management Studio. Kontrollér, at du har forbindelse til **master** databasen med serverniveau vigtigste logon, du har oprettet, når du konfigurerer din server for at udføre disse opgaver.

For at åbne et forespørgselsvindue i Management Studio skal åbne mappen databaser, Udvid mappen **Systemdatabaser** , skal du højreklikke på **master**og derefter klikke på **Ny forespørgsel**.

-   Bruge sætningen **CREATE DATABASE** til at oprette en database. Du kan finde yderligere oplysninger finder [Opret DATABASE (SQL-Database)](https://msdn.microsoft.com/library/dn268335.aspx). Følgende sætning opretter en database med navnet **myTestDB** og angiver, at det er en Standard S0 Edition database med en standard maksimal størrelse på 250 GB.

        CREATE DATABASE myTestDB
        (EDITION='Standard',
         SERVICE_OBJECTIVE='S0');

Klik på **Udfør** for at køre forespørgslen.

-   Brug sætningen **JUSTER DATABASE** til at ændre en eksisterende database, for eksempel, hvis du vil ændre navnet og udgave af databasen. Du kan finde flere oplysninger [JUSTER DATABASE (SQL-Database)](https://msdn.microsoft.com/library/ms174269.aspx). Følgende sætning ændrer den database, du oprettede i forrige trin for at ændre edition til Standard S1.

        ALTER DATABASE myTestDB
        MODIFY
        (SERVICE_OBJECTIVE='S1');

-   Brug **Slip databasen** sætningen til at slette en eksisterende database. Du kan finde yderligere oplysninger finder [DROP DATABASE (SQL-Database)](https://msdn.microsoft.com/library/ms178613.aspx). Følgende sætning sletter **myTestDB** databasen, men slippe ikke den nu fordi du skal bruge det til at oprette logon i næste trin.

        DROP DATABASE myTestBase;

-   Master databasen har visningen **sys.databases** , som du kan bruge til at få vist oplysninger om alle databaser. Hvis du vil se alle eksisterende databaser, skal du udføre følgende sætning:

        SELECT * FROM sys.databases;

-   Sætningen **Brug** understøttes ikke i SQL-Database for at skifte mellem databaser. I stedet skal du oprette forbindelse direkte til destinationsdatabasen.

>[AZURE.NOTE] Mange af de Transact-SQL-sætninger, oprette eller ændre en database skal køres i deres egen batchen og kan ikke grupperes med andre Transact-SQL-sætninger. Du kan finde yderligere oplysninger finder sætningen-specifikke oplysninger.

## <a name="create-and-manage-logins"></a>Oprette og administrere logon

**Master** databasen indeholder logon, og hvilke logon har tilladelse til at oprette databaser eller andre logon. Administrere logon ved at oprette forbindelse til den **overordnede** database med serverniveau vigtigste logon, du har oprettet, når du konfigurerer din server. Du kan bruge sætningerne **Oprette logon**, **Ændre LOGIN**eller **Slip-logon** til at udføre forespørgsler i master databasen, der administrerer logon på tværs af hele serveren. Få mere at vide under [administrere databaser og logon i SQL-Database](http://msdn.microsoft.com/library/azure/ee336235.aspx). 


-   Bruge sætningen **Oprette logon** til at oprette et serverniveau logon. Se [Oprette LOGIN (SQL-Database)](https://msdn.microsoft.com/library/ms189751.aspx)kan finde flere oplysninger. Følgende sætning opretter et logon, kaldet **login1**. Erstat **Adgangskode1** med adgangskoden til dit valg.

        CREATE LOGIN login1 WITH password='password1';

-   Bruge sætningen **CREATE USER** til at give tilladelser på elementniveau database. Alle logon skal oprettes i den **overordnede** database. For et logon at oprette forbindelse til en anden database, skal du give den database niveau tilladelser ved hjælp af sætningen **CREATE USER** på den pågældende database. Du kan finde flere oplysninger [CREATE USER (SQL-Database)](https://msdn.microsoft.com/library/ms173463.aspx). 

-   Hvis du vil give login1 tilladelser til en database, kaldet **myTestDB**, skal du benytte følgende fremgangsmåde:

 1.  Højreklik på servernavnet i Object Explorer for at opdatere Object Explorer for at få vist den **myTestDB** database, du har oprettet, og klik derefter på **Opdater**.  

     Hvis du har lukket forbindelsen, kan du oprette forbindelse igen ved at vælge **Forbinde Object Explorer** i menuen Filer.

 2. Højreklik på **myTestDB** database, og vælg **Ny forespørgsel**.

    3.  Udføre følgende sætning mod myTestDB databasen til at oprette databasebruger med navnet **login1User** , der svarer til serverniveau login **login1**.

            CREATE USER login1User FROM LOGIN login1;

-   Brug den **sp\_addrolemember** lagret procedure til at give brugerkontoen det korrekte niveau af tilladelser til databasen. Du kan finde flere oplysninger [sp_addrolemember (Transact-SQL)](http://msdn.microsoft.com/library/ms187750.aspx). Følgende sætning giver **login1User** skrivebeskyttet tilladelser til databasen ved at tilføje **login1User** til den **db\_datareader** rolle.

        exec sp_addrolemember 'db_datareader', 'login1User';    

-   Brug sætningen **ALTER logon** til at ændre et eksisterende logon, for eksempel, hvis du vil ændre adgangskoden til logon. Se [Ændre LOGIN (SQL-Database)](https://msdn.microsoft.com/library/ms189828.aspx)kan finde flere oplysninger. Sætningen **ALTER LOGIN** skal køres mod **master** databasen. Skift tilbage til forespørgselsvinduet, der er forbundet til den pågældende database. Følgende sætning ændrer **login1** logon for at nulstille adgangskoden. Erstat **newPassword** med adgangskoden til dit valg og **oldPassword** med den aktuelle adgangskode til logon.

        ALTER LOGIN login1
        WITH PASSWORD = 'newPassword'
        OLD_PASSWORD = 'oldPassword';

-   Brug **Siden slip LOGIN** sætningen til at slette et eksisterende logon. Hvis du sletter et logon på serverniveau også slettes alle tilknyttede databasebrugerkonti. Du kan finde yderligere oplysninger finder [DROP DATABASE (SQL-Database)](https://msdn.microsoft.com/library/ms178613.aspx). Sætningen **DROP LOGIN** skal køres mod **master** databasen. Sætningen sletter **login1** logon.

        DROP LOGIN login1;

-   Master databasen har den **sys.sql\_logon** få vist, at du kan bruge til at få vist logon. Hvis du vil se alle eksisterende logon, skal du udføre følgende sætning:

        SELECT * FROM sys.sql_logins;

## <a name="monitor-sql-database-using-dynamic-management-views"></a>Overvåge SQL-Database ved hjælp af dynamiske Management visninger

SQL-Database understøtter flere dynamiske management visninger, som du kan bruge til at overvåge en enkelt database. Nogle få eksempler på typen skærm data, du kan hente gennem disse visninger følger. Alle oplysninger om og flere eksempler på brug under [overvågning SQL-Database ved hjælp af dynamiske Management visninger](https://msdn.microsoft.com/library/azure/ff394114.aspx).

-   Forespørgsler en dynamisk visning kræver **DATABASE ZOOMINDSTILLING** tilladelser. Hvis du vil give tilladelse til at **Få vist DATABASE STATE** til en bestemt databasebruger, oprette forbindelse til databasen og udføre følgende sætning i databasen:

        GRANT VIEW DATABASE STATE TO login1User;

-   Beregne database størrelse ved hjælp af den **sys.dm\_db\_partition\_statistik** visning. Den **sys.dm\_db\_partition\_statistik** visning returnerer oplysninger om alle partitioner side og antal rækker i databasen, som du kan bruge til at beregne databasestørrelse. Følgende forespørgsel returnerer størrelsen på databasen i megabyte:

        SELECT SUM(reserved_page_count)*8.0/1024
        FROM sys.dm_db_partition_stats;   

-   Brug den **sys.dm\_eksekverbar\_forbindelser** og **sys.dm\_eksekverbar\_sessioner** visninger til at hente oplysninger om aktuelle brugerforbindelser og interne opgaver, der er knyttet til databasen. Følgende forespørgsel returnerer oplysninger om den aktuelle forbindelse:

        SELECT
            e.connection_id,
            s.session_id,
            s.login_name,
            s.last_request_end_time,
            s.cpu_time
        FROM
            sys.dm_exec_sessions s
            INNER JOIN sys.dm_exec_connections e
              ON s.session_id = e.session_id;

-   Brug den **sys.dm\_eksekverbar\_forespørgsel\_statistik** visning for at hente Sammenlæg statistikker for cachelagret forespørgselsplaner. Følgende forespørgsel returnerer oplysninger om de mest populære fem forespørgsler, sorteret efter Gennemsnitlig CPU-tid.

        SELECT TOP 5 query_stats.query_hash AS "Query Hash",
            SUM(query_stats.total_worker_time), SUM(query_stats.execution_count) AS "Avg CPU Time",
            MIN(query_stats.statement_text) AS "Statement Text"
        FROM
            (SELECT QS.*,
            SUBSTRING(ST.text, (QS.statement_start_offset/2) + 1,
            ((CASE statement_end_offset
                WHEN -1 THEN DATALENGTH(ST.text)
                ELSE QS.statement_end_offset END
                    - QS.statement_start_offset)/2) + 1) AS statement_text
             FROM sys.dm_exec_query_stats AS QS
             CROSS APPLY sys.dm_exec_sql_text(QS.sql_handle) as ST) as query_stats
        GROUP BY query_stats.query_hash
        ORDER BY 2 DESC;
 
 
