<properties
   pageTitle="Overvåge arbejdsbelastningen ved hjælp af DMVs | Microsoft Azure"
   description="Lær at overvåge arbejdsbelastningen ved hjælp af DMVs."
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
   ms.date="10/08/2016"
   ms.author="sonyama;barbkess"/>

# <a name="monitor-your-workload-using-dmvs"></a>Overvåge arbejdsbelastningen ved hjælp af DMVs

I denne artikel beskrives, hvordan du bruger dynamiske Management visninger (DMVs) til at overvåge arbejdsbelastningen og undersøge udførelse af forespørgsler i Azure SQL-datalager.

## <a name="permissions"></a>Tilladelser

Hvis du vil forespørge DMVs i denne artikel, skal du enten ZOOMINDSTILLING DATABASE eller det markerede KONTROLELEMENT tilladelse. Udstedelse ZOOMINDSTILLING DATABASE er som regel den foretrukne tilladelse som det er meget mere restriktivt.

```sql
GRANT VIEW DATABASE STATE TO myuser;
```

## <a name="monitor-connections"></a>Overvåge forbindelser

Alle logon til SQL Data Warehouse er logget på [sys.dm_pdw_exec_sessions][].  Denne DMV indeholder de sidste 10.000 logon.  Session_id er den primære nøgle og tildeles sekventielt for hvert nyt logon.

```sql
-- Other Active Connections
SELECT * FROM sys.dm_pdw_exec_sessions where status <> 'Closed' and session_id <> session_id();
```

## <a name="monitor-query-execution"></a>Overvåge udførelse af forespørgsel

Alle forespørgsler, der udføres på SQL Data Warehouse er logget på [sys.dm_pdw_exec_requests][].  Denne DMV indeholder de sidste 10.000 forespørgsler udføres.  Request_id entydigt identificerer hver forespørgsel og er den primære nøgle for denne DMV.  Request_id tildeles sekventielt for hver ny forespørgsel og indledes med QID, som står for forespørgsel-ID.  Forespørgsler denne DMV for en given session_id viser alle forespørgsler til en given logon.

>[AZURE.NOTE] Lagrede procedurer bruge flere anmode om id'er.  Anmode om id'er tildeles i rækkefølge. 

Her er trinnene følge for at undersøge forespørgsel udførelse af planer og sluttidspunkter for en bestemt forespørgsel.

### <a name="step-1-identify-the-query-you-wish-to-investigate"></a>TRIN 1: Identificere den forespørgsel, du vil undersøge

```sql
-- Monitor active queries
SELECT * 
FROM sys.dm_pdw_exec_requests 
WHERE status not in ('Completed','Failed','Cancelled')
  AND session_id <> session_id()
ORDER BY submit_time DESC;

-- Find top 10 queries longest running queries
SELECT TOP 10 * 
FROM sys.dm_pdw_exec_requests 
ORDER BY total_elapsed_time DESC;

-- Find a query with the Label 'My Query'
-- Use brackets when querying the label column, as it it a key word
SELECT  *
FROM    sys.dm_pdw_exec_requests
WHERE   [label] = 'My Query';
```

Fra de foregående forespørgselsresultater, **note ID'ET anmode om** i din forespørgsel, du vil undersøge.

Er der i kø forespørgsler i tilstanden **afbrudt** på grund af på dokumentsammenfald begrænsninger. Disse forespørgsler vises også i forespørgslen sys.dm_pdw_waits venter med en type af UserConcurrencyResourceType. Se [på Dokumentsammenfald og arbejdsbelastningen][] for flere oplysninger om begrænsninger på dokumentsammenfald. Forespørgsler kan også vente på, andre årsager f.eks til objektet låse.  Hvis din forespørgsel, der venter for en ressource, se [Investigating forespørgsler, der venter på ressourcer][] længere nede i denne artikel.

Bruge [etiket][] til at tildele en kommentar til din forespørgsel, der kan søges i visningen sys.dm_pdw_exec_requests for at forenkle opslag i en forespørgsel i tabellen sys.dm_pdw_exec_requests.

```sql
-- Query with Label
SELECT *
FROM sys.tables
OPTION (LABEL = 'My Query')
;
```

### <a name="step-2-investigate-the-query-plan"></a>TRIN 2: Undersøge forespørgselsplanen

Du kan bruge ID'ET anmode om at hente forespørgslens fordelt SQL (DSQL) plan fra [sys.dm_pdw_request_steps][].

```sql
-- Find the distributed query plan steps for a specific query.
-- Replace request_id with value from Step 1.

SELECT * FROM sys.dm_pdw_request_steps
WHERE request_id = 'QID####'
ORDER BY step_index;
```

Når en plan for DSQL tager længere tid end forventet, kan årsagen kan være en kompleks plan med mange DSQL trin eller blot en enkelt trin tager lang tid.  Hvis planen er mange trin med flere flytning, kan du overveje at optimere din tabel salgsdistributioner for at reducere flytning af data. [Tabel fordeling][] artikel forklarer, hvorfor data skal flyttes til at løse en forespørgsel og beskriver nogle fordeling strategier for at minimere flytning af data.

Undersøge yderligere oplysninger om et enkelt trin, kolonnen *operation_type* i forespørgselstrin længerevarende og notere **Trin indeks**:

- Fortsætte med trin 3a for **SQL-handlinger**: OnOperation, RemoteOperation, ReturnOperation.
- Fortsætte med trin 3 for **flytning af Data i handlinger**: ShuffleMoveOperation BroadcastMoveOperation TrimMoveOperation, PartitionMoveOperation, MoveOperation, og CopyOperation.

### <a name="step-3a-investigate-sql-on-the-distributed-databases"></a>TRIN 3a: undersøge SQL på de distribuerede databaser

Brug felterne anmode om ID og indekset trin til at hente oplysninger fra [sys.dm_pdw_sql_requests][], som indeholder udførelse af oplysninger på forespørgselstrin på alle de fordelte databaser.

```sql
-- Find the distribution run times for a SQL step.
-- Replace request_id and step_index with values from Step 1 and 3.

SELECT * FROM sys.dm_pdw_sql_requests
WHERE request_id = 'QID####' AND step_index = 2;
```

Når forespørgselstrin kører, kan [DBCC PDW_SHOWEXECUTIONPLAN][] bruges til at hente den SQL Server anslåede plan fra cachen til SQL Server-plan til det trin, der kører på en bestemt fordeling.

```sql
-- Find the SQL Server execution plan for a query running on a specific SQL Data Warehouse Compute or Control node.
-- Replace distribution_id and spid with values from previous query.

DBCC PDW_SHOWEXECUTIONPLAN(1, 78);
```

### <a name="step-3b-investigate-data-movement-on-the-distributed-databases"></a>TRIN 3: undersøge flytning af data på de distribuerede databaser

Brug felterne anmode om ID og indekset trin til at hente oplysninger om sekunditki til bevægelse, der kører på hver fordeling fra [sys.dm_pdw_dms_workers][].

```sql
-- Find the information about all the workers completing a Data Movement Step.
-- Replace request_id and step_index with values from Step 1 and 3.

SELECT * FROM sys.dm_pdw_dms_workers
WHERE request_id = 'QID####' AND step_index = 2;
```

- Se kolonnen *total_elapsed_time* for at se, hvis en bestemt fordeling tager betydeligt længere tid end andre for flytning af data.
- Du kan finde fordeling længerevarende kolonnen *rows_processed* for at se, om antallet rækker, der flyttes fra fordelingen er væsentlig større end andre. Hvis det er tilfældet, kan det skyldes skævhed af dine underliggende data.

Hvis forespørgslen kører, kan [DBCC PDW_SHOWEXECUTIONPLAN][] bruges til at hente den SQL Server anslåede plan fra cachen til SQL Server-plan til det aktive SQL-trin i en bestemt fordeling.

```sql
-- Find the SQL Server estimated plan for a query running on a specific SQL Data Warehouse Compute or Control node.
-- Replace distribution_id and spid with values from previous query.

DBCC PDW_SHOWEXECUTIONPLAN(55, 238);
```

<a name="waiting"></a>
## <a name="monitor-waiting-queries"></a>Overvåge vente forespørgsler

Hvis du opdager, at din forespørgsel er gået i gang fordi venter på en ressource, er her en forespørgsel, der viser alle ressourcerne, der venter på en forespørgsel.

```sql
-- Find queries 
-- Replace request_id with value from Step 1.

SELECT waits.session_id,
      waits.request_id,  
      requests.command,
      requests.status,
      requests.start_time,  
      waits.type,
      waits.state,
      waits.object_type,
      waits.object_name
FROM   sys.dm_pdw_waits waits
   JOIN  sys.dm_pdw_exec_requests requests
   ON waits.request_id=requests.request_id
WHERE waits.request_id = 'QID####'
ORDER BY waits.object_name, waits.object_type, waits.state;
```

Hvis forespørgslen er aktivt venter på ressourcer fra en anden forespørgsel være tilstanden **AcquireResources**.  Hvis forespørgslen indeholder alle de ressourcer, der er påkrævet, være tilstanden **Granted**.

## <a name="next-steps"></a>Næste trin
Du kan finde flere oplysninger om DMVs i [systemvisninger][] .
Du kan finde flere oplysninger om bedste praksis [SQL Data Warehouse bedste fremgangsmåder][]

<!--Image references-->

<!--Article references-->
[Manage overview]: ./sql-data-warehouse-overview-manage.md
[SQL Data Warehouse bedste fremgangsmåder]: ./sql-data-warehouse-best-practices.md
[Systemvisninger]: ./sql-data-warehouse-reference-tsql-system-views.md
[Tabel fordeling]: ./sql-data-warehouse-tables-distribute.md
[På dokumentsammenfald og arbejdsbelastningen]: ./sql-data-warehouse-develop-concurrency.md
[Ved at undersøge forespørgsler, der venter på ressourcer]: ./sql-data-warehouse-manage-monitor.md#waiting

<!--MSDN references-->
[sys.dm_pdw_dms_workers]: http://msdn.microsoft.com/library/mt203878.aspx
[sys.dm_pdw_exec_requests]: http://msdn.microsoft.com/library/mt203887.aspx
[sys.dm_pdw_exec_sessions]: http://msdn.microsoft.com/library/mt203883.aspx
[sys.dm_pdw_request_steps]: http://msdn.microsoft.com/library/mt203913.aspx
[sys.dm_pdw_sql_requests]: http://msdn.microsoft.com/library/mt203889.aspx
[DBCC PDW_SHOWEXECUTIONPLAN]: http://msdn.microsoft.com/library/mt204017.aspx
[DBCC PDW_SHOWSPACEUSED]: http://msdn.microsoft.com/library/mt204028.aspx
[ETIKET]: https://msdn.microsoft.com/library/ms190322.aspx
