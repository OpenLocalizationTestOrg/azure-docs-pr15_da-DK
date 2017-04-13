<properties
   pageTitle="Overvågning Azure SQL-Database ved hjælp af dynamiske Management visninger | Microsoft Azure"
   description="Lær at registrere og diagnosticere almindelige problemer med ydeevnen ved hjælp af dynamiske management visninger til at overvåge Microsoft Azure SQL-Database."
   services="sql-database"
   documentationCenter=""
   authors="CarlRabeler"
   manager="jhubbard"
   editor=""
   tags=""/>

<tags
   ms.service="sql-database"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="data-management"
   ms.date="09/20/2016"
   ms.author="carlrab"/>

# <a name="monitoring-azure-sql-database-using-dynamic-management-views"></a>Overvågning Azure SQL-Database ved hjælp af dynamiske management visninger

Microsoft Azure SQL Database gør det muligt for et undersæt af dynamiske management visninger til at diagnosticere problemer med ydeevnen, som kan skyldes blokerede eller længerevarende forespørgsler, ressource flaskehalse, dårlig forespørgselsplaner osv. Dette emne indeholder oplysninger om, hvordan du registrerer almindelige problemer med ydeevnen ved hjælp af dynamiske management visninger.

SQL-Database understøtter delvist tre kategorier af dynamiske management visninger:

- Databasen beslægtede dynamiske management visninger.
- Udførelse af beslægtede dynamiske management visninger.
- Transaktions-relaterede dynamiske management visninger.

Se [dynamisk Management visninger og funktioner (Transact-SQL)](https://msdn.microsoft.com/library/ms188754.aspx) i SQL Server bøger Online kan finde detaljerede oplysninger om administration af dynamiske visninger.

## <a name="permissions"></a>Tilladelser

Forespørgsler en dynamisk visning kræver **DATABASE ZOOMINDSTILLING** tilladelser i SQL-Database. Tilladelse til at **Få vist DATABASE STATE** returnerer oplysninger om alle objekter i den aktuelle database.
Hvis du vil give tilladelse til at **Få vist DATABASE STATE** til en bestemt databasebruger, skal du køre følgende forespørgsel:

```GRANT VIEW DATABASE STATE TO database_user; ```

I en forekomst af lokal SQL Server returnerer dynamiske management visninger server tilstandsoplysninger. De returnerer oplysninger om den aktuelle logiske database kun i SQL-Database.

## <a name="calculating-database-size"></a>Beregning af databasestørrelse

Følgende forespørgsel returnerer størrelsen på databasen (i megabyte):

```
-- Calculates the size of the database.
SELECT SUM(reserved_page_count)*8.0/1024
FROM sys.dm_db_partition_stats;
GO
```

Følgende forespørgsel returnerer størrelsen af de enkelte objekter (i megabyte) i databasen:

```
-- Calculates the size of individual database objects.
SELECT sys.objects.name, SUM(reserved_page_count) * 8.0 / 1024
FROM sys.dm_db_partition_stats, sys.objects
WHERE sys.dm_db_partition_stats.object_id = sys.objects.object_id
GROUP BY sys.objects.name;
GO
```

## <a name="monitoring-connections"></a>Overvågning af forbindelser

Du kan bruge visningen [sys.dm_exec_connections](https://msdn.microsoft.com/library/ms181509.aspx) til at hente oplysninger om forbindelser til en bestemt Azure SQL-databaseserver og detaljerne for hver forbindelse. Desuden er visningen [sys.dm_exec_sessions](https://msdn.microsoft.com/library/ms176013.aspx) nyttigt, når hente oplysninger om alle aktive brugerforbindelser og interne opgaver.
Følgende forespørgsel henter oplysninger om den aktuelle forbindelse:

```
SELECT
    c.session_id, c.net_transport, c.encrypt_option,
    c.auth_scheme, s.host_name, s.program_name,
    s.client_interface_name, s.login_name, s.nt_domain,
    s.nt_user_name, s.original_login_name, c.connect_time,
    s.login_time
FROM sys.dm_exec_connections AS c
JOIN sys.dm_exec_sessions AS s
    ON c.session_id = s.session_id
WHERE c.session_id = @@SPID;
```

> [AZURE.NOTE] Når du kører **sys.dm_exec_requests** og **sys.dm_exec_sessions visninger**, hvis du har **DATABASE ZOOMINDSTILLING** tilladelse på databasen, se du alle udføre sessioner på databasen. Ellers kan se du den aktuelle session.

## <a name="monitoring-query-performance"></a>Overvåge Forespørgselsydeevnen for

Langsomt eller længe kører forespørgsler kan forbruge betydeligt systemressourcer. Dette afsnit viser, hvordan du bruger dynamiske management visninger til at registrere et par almindelige problemer forespørgsel med ydeevnen. En ældre, men stadig nyttige reference til fejlfinding, er i [Foretage fejlfinding af problemer med ydeevnen i SQL Server 2008](http://download.microsoft.com/download/D/B/D/DBDE7972-1EB9-470A-BA18-58849DB3EB3B/TShootPerfProbs2008.docx) artiklen på Microsoft TechNet.

### <a name="finding-top-n-queries"></a>Finde øverste N forespørgsler

I følgende eksempel returnerer oplysninger om de mest populære fem forespørgsler, sorteret efter Gennemsnitlig CPU-tid. I dette eksempel samler forespørgsler efter deres forespørgsel hash-værdi, så logisk tilsvarende forespørgsler er grupperet efter deres akkumuleret Ressourceforbrug.

```
SELECT TOP 5 query_stats.query_hash AS "Query Hash",
    SUM(query_stats.total_worker_time) / SUM(query_stats.execution_count) AS "Avg CPU Time",
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
```

### <a name="monitoring-blocked-queries"></a>Overvågning blokerede forespørgsler

Langsom eller længerevarende forespørgsler kan bidrage til unødvendig Ressourceforbrug og være vigtigt af blokerede forespørgsler. Årsagen til blokering kan være dårlig programmet design, forkert forespørgselsplaner, manglende nyttige indeks osv. Du kan bruge visningen sys.dm_tran_locks til at få oplysninger om den aktuelle låsning aktivitet i dine Azure SQL-Database. For eksempel kode, se [sys.dm_tran_locks (Transact-SQL)](https://msdn.microsoft.com/library/ms190345.aspx) i SQL Server bøger Online.

### <a name="monitoring-query-plans"></a>Overvågning forespørgselsplaner

En ineffektiv forespørgselsplan kan også øge CPU-forbrug. I følgende eksempel bruges visningen [sys.dm_exec_query_stats](https://msdn.microsoft.com/library/ms189741.aspx) til at bestemme, hvilken forespørgsel bruger den mest akkumuleret CPU.

```
SELECT
    highest_cpu_queries.plan_handle,
    highest_cpu_queries.total_worker_time,
    q.dbid,
    q.objectid,
    q.number,
    q.encrypted,
    q.[text]
FROM
    (SELECT TOP 50
        qs.plan_handle,
        qs.total_worker_time
    FROM
        sys.dm_exec_query_stats qs
    ORDER BY qs.total_worker_time desc) AS highest_cpu_queries
    CROSS APPLY sys.dm_exec_sql_text(plan_handle) AS q
ORDER BY highest_cpu_queries.total_worker_time DESC;
```

## <a name="see-also"></a>Se også

[Introduktion til SQL-Database](sql-database-technical-overview.md)
