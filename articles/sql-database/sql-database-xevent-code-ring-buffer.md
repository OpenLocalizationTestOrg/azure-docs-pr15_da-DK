<properties 
    pageTitle="XEvent Ring bufferen kode for SQL-Database | Microsoft Azure" 
    description="Indeholder et eksempel på Transact-SQL-kode, der er foretaget nemt og hurtig ved hjælp af Ring bufferen destinationen, i Azure SQL-Database." 
    services="sql-database" 
    documentationCenter="" 
    authors="MightyPen" 
    manager="jhubbard" 
    editor="" 
    tags=""/>


<tags 
    ms.service="sql-database" 
    ms.workload="data-management" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="08/23/2016" 
    ms.author="genemi"/>


# <a name="ring-buffer-target-code-for-extended-events-in-sql-database"></a>Ring til bufferen target kode for udvidede begivenheder i SQL-Database

[AZURE.INCLUDE [sql-database-xevents-selectors-1-include](../../includes/sql-database-xevents-selectors-1-include.md)]

Du vil et eksempel på fuldført kode for den nemmeste hurtige måde at capture og rapport oplysninger for en udvidet begivenhed under en test. Den nemmeste mål for udvidede begivenhed data er [Ring bufferen mål](http://msdn.microsoft.com/library/ff878182.aspx).


I dette emne vises et eksempel på Transact-SQL-kode, som:


1. Opretter en tabel med data til at vise med.

2. Opretter en session for en eksisterende udvidede begivenhed, nemlig **sqlserver.sql_statement_starting**.
    - Hændelsen er begrænset til SQL-sætninger, der indeholder en bestemt opdatering streng: **sætningen som '% opdatering tabEmployee %'**.
    - Vælger at sende output fra en begivenhed til en destination af typen Ring bufferen, nemlig **package0.ring_buffer**.

3. Starter begivenhed sessionen.

4. Udsteder et par enkle SQL UPDATE-sætning.

5. Problemer, en SQL SELECT til at hente begivenhed output fra Ring bufferen.
    - **sys.dm_xe_database_session_targets** og andre dynamiske management visninger (DMVs) er sammenføjet.

6. Stopper begivenhed sessionen.

7. Udelader Ring bufferen destinationen, hvis du vil frigive sine ressourcer.

8. Udelader begivenhed sessionen og tabellen demo.


## <a name="prerequisites"></a>Forudsætninger


- En Azure-konto og abonnement. Du kan tilmelde dig en [gratis prøveversion](https://azure.microsoft.com/pricing/free-trial/).


- En database, kan du oprette en tabel i.
 - Du kan også kan du [oprette en **AdventureWorksLT** demonstrationsdatabase](sql-database-get-started.md) i minutter.


- SQL Server Management Studio (ssms.exe), ideelt den seneste månedlige opdatering version. Du kan hente de seneste ssms.exe fra:
 - Emne med titlen [Hente SQL Server Management Studio](http://msdn.microsoft.com/library/mt238290.aspx).
 - [Et direkte link til overførslen.](http://go.microsoft.com/fwlink/?linkid=616025)


## <a name="code-sample"></a>Eksempel på kode


Følgende eksempel på Ring til bufferen kode kan køres på Azure SQL-Database eller Microsoft SQL Server med meget mindre ændring. Forskellen er tilstedeværelsen af noden 'foran' navn på nogle dynamic management visninger (DMVs), bruges i FROM-delsætningen i trin 5. Eksempel:

- sys.dm_xe**foran**_session_targets
- sys.dm_xe_session_targets


&nbsp;


```
GO
----  Transact-SQL.
---- Step set 1.

SET NOCOUNT ON;
GO


IF EXISTS
    (SELECT * FROM sys.objects
        WHERE type = 'U' and name = 'tabEmployee')
BEGIN
    DROP TABLE tabEmployee;
END
GO


CREATE TABLE tabEmployee
(
    EmployeeGuid         uniqueIdentifier   not null  default newid()  primary key,
    EmployeeId           int                not null  identity(1,1),
    EmployeeKudosCount   int                not null  default 0,
    EmployeeDescr        nvarchar(256)          null
);
GO


INSERT INTO tabEmployee ( EmployeeDescr )
    VALUES ( 'Jane Doe' );
GO

---- Step set 2.


IF EXISTS
    (SELECT * from sys.database_event_sessions
        WHERE name = 'eventsession_gm_azuresqldb51')
BEGIN
    DROP EVENT SESSION eventsession_gm_azuresqldb51
        ON DATABASE;
END
GO


CREATE
    EVENT SESSION eventsession_gm_azuresqldb51
    ON DATABASE
    ADD EVENT
        sqlserver.sql_statement_starting
            (
            ACTION (sqlserver.sql_text)
            WHERE statement LIKE '%UPDATE tabEmployee%'
            )
    ADD TARGET
        package0.ring_buffer
            (SET
                max_memory = 500   -- Units of KB.
            );
GO

---- Step set 3.


ALTER EVENT SESSION eventsession_gm_azuresqldb51
    ON DATABASE
    STATE = START;
GO

---- Step set 4.


SELECT 'BEFORE_Updates', EmployeeKudosCount, * FROM tabEmployee;

UPDATE tabEmployee
    SET EmployeeKudosCount = EmployeeKudosCount + 102;

UPDATE tabEmployee
    SET EmployeeKudosCount = EmployeeKudosCount + 1015;

SELECT 'AFTER__Updates', EmployeeKudosCount, * FROM tabEmployee;
GO

---- Step set 5.


SELECT
    se.name                      AS [session-name],
    ev.event_name,
    ac.action_name,
    st.target_name,
    se.session_source,
    st.target_data,
    CAST(st.target_data AS XML)  AS [target_data_XML]
FROM
               sys.dm_xe_database_session_event_actions  AS ac

    INNER JOIN sys.dm_xe_database_session_events         AS ev  ON ev.event_name = ac.event_name
        AND CAST(ev.event_session_address AS BINARY(8)) = CAST(ac.event_session_address AS BINARY(8))

    INNER JOIN sys.dm_xe_database_session_object_columns AS oc
         ON CAST(oc.event_session_address AS BINARY(8)) = CAST(ac.event_session_address AS BINARY(8))

    INNER JOIN sys.dm_xe_database_session_targets        AS st
         ON CAST(st.event_session_address AS BINARY(8)) = CAST(ac.event_session_address AS BINARY(8))

    INNER JOIN sys.dm_xe_database_sessions               AS se
         ON CAST(ac.event_session_address AS BINARY(8)) = CAST(se.address AS BINARY(8))
WHERE
        oc.column_name = 'occurrence_number'
    AND
        se.name        = 'eventsession_gm_azuresqldb51'
    AND
        ac.action_name = 'sql_text'
ORDER BY
    se.name,
    ev.event_name,
    ac.action_name,
    st.target_name,
    se.session_source
;
GO

---- Step set 6.


ALTER EVENT SESSION eventsession_gm_azuresqldb51
    ON DATABASE
    STATE = STOP;
GO

---- Step set 7.


ALTER EVENT SESSION eventsession_gm_azuresqldb51
    ON DATABASE
    DROP TARGET package0.ring_buffer;
GO

---- Step set 8.


DROP EVENT SESSION eventsession_gm_azuresqldb51
    ON DATABASE;
GO

DROP TABLE tabEmployee;
GO
```


&nbsp;


## <a name="ring-buffer-contents"></a>Ring til bufferen indhold


Vi bruges ssms.exe til at køre kodeeksemplet.


For at få vist resultaterne, vi har klikket på cellen under kolonnen sidehoved **target_data_XML**.

Derefter i resultatruden vi har klikket på cellen under kolonnen sidehoved **target_data_XML**. Denne Klik oprettet en anden fil fane i ssms.exe som indholdet af resultatcellen vises, som XML.


Output er vist i følgende blok. Det ser lang tid, men det er blot to **<event>** elementer.


&nbsp;


```
<RingBufferTarget truncated="0" processingTime="0" totalEventsProcessed="2" eventCount="2" droppedCount="0" memoryUsed="1728">
  <event name="sql_statement_starting" package="sqlserver" timestamp="2015-09-22T15:29:31.317Z">
    <data name="state">
      <type name="statement_starting_state" package="sqlserver" />
      <value>0</value>
      <text>Normal</text>
    </data>
    <data name="line_number">
      <type name="int32" package="package0" />
      <value>7</value>
    </data>
    <data name="offset">
      <type name="int32" package="package0" />
      <value>184</value>
    </data>
    <data name="offset_end">
      <type name="int32" package="package0" />
      <value>328</value>
    </data>
    <data name="statement">
      <type name="unicode_string" package="package0" />
      <value>UPDATE tabEmployee
    SET EmployeeKudosCount = EmployeeKudosCount + 102</value>
    </data>
    <action name="sql_text" package="sqlserver">
      <type name="unicode_string" package="package0" />
      <value>
---- Step set 4.


SELECT 'BEFORE_Updates', EmployeeKudosCount, * FROM tabEmployee;

UPDATE tabEmployee
    SET EmployeeKudosCount = EmployeeKudosCount + 102;

UPDATE tabEmployee
    SET EmployeeKudosCount = EmployeeKudosCount + 1015;

SELECT 'AFTER__Updates', EmployeeKudosCount, * FROM tabEmployee;
</value>
    </action>
  </event>
  <event name="sql_statement_starting" package="sqlserver" timestamp="2015-09-22T15:29:31.327Z">
    <data name="state">
      <type name="statement_starting_state" package="sqlserver" />
      <value>0</value>
      <text>Normal</text>
    </data>
    <data name="line_number">
      <type name="int32" package="package0" />
      <value>10</value>
    </data>
    <data name="offset">
      <type name="int32" package="package0" />
      <value>340</value>
    </data>
    <data name="offset_end">
      <type name="int32" package="package0" />
      <value>486</value>
    </data>
    <data name="statement">
      <type name="unicode_string" package="package0" />
      <value>UPDATE tabEmployee
    SET EmployeeKudosCount = EmployeeKudosCount + 1015</value>
    </data>
    <action name="sql_text" package="sqlserver">
      <type name="unicode_string" package="package0" />
      <value>
---- Step set 4.


SELECT 'BEFORE_Updates', EmployeeKudosCount, * FROM tabEmployee;

UPDATE tabEmployee
    SET EmployeeKudosCount = EmployeeKudosCount + 102;

UPDATE tabEmployee
    SET EmployeeKudosCount = EmployeeKudosCount + 1015;

SELECT 'AFTER__Updates', EmployeeKudosCount, * FROM tabEmployee;
</value>
    </action>
  </event>
</RingBufferTarget>
```


#### <a name="release-resources-held-by-your-ring-buffer"></a>Slip ressourcer, som din Ring bufferen


Når du er færdig med din Ring bufferen, kan du fjerne det og slip sine ressourcer udstedelse en **ændre** ud som følger:


```
ALTER EVENT SESSION eventsession_gm_azuresqldb51
    ON DATABASE
    DROP TARGET package0.ring_buffer;
GO
```


Definitionen af sessionen begivenhed er opdateret, men ikke slettet. Senere kan du tilføje en anden forekomst af bufferen Ring til sessionen begivenhed:


```
ALTER EVENT SESSION eventsession_gm_azuresqldb51
    ON DATABASE
    ADD TARGET
        package0.ring_buffer
            (SET
                max_memory = 500   -- Units of KB.
            );
```


## <a name="more-information"></a>Få mere at vide


Emnet med den primære for udvidede begivenheder i Azure SQL-Database er:


- [Udvidet begivenhed overvejelser i SQL-Database](sql-database-xevent-db-diff-from-svr.md), som står i kontrast nogle aspekter af udvidede hændelser, der er forskellige Azure SQL-Database eller Microsoft SQL Server.


Andre emner i kode eksempel til udvidede hændelser er tilgængelige på linkene nedenfor. Du skal dog regelmæssigt se et eksempel for at se, om prøven er beregnet til Microsoft SQL Server kontra Azure SQL-Database. Derefter kan du bestemme, om mindre ændringer er nødvendige for at køre eksemplet.


- Eksempel på kode til Azure SQL-Database: [begivenhedsfil target kode for udvidede begivenheder i SQL-Database](sql-database-xevent-code-event-file.md)


<!--
('lock_acquired' event.)

- Code sample for SQL Server: [Determine Which Queries Are Holding Locks](http://msdn.microsoft.com/library/bb677357.aspx)
- Code sample for SQL Server: [Find the Objects That Have the Most Locks Taken on Them](http://msdn.microsoft.com/library/bb630355.aspx)
-->
