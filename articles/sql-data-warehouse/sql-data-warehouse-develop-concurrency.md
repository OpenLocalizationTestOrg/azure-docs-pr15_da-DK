<properties
   pageTitle="På dokumentsammenfald og arbejdsbelastningen management i SQL Data Warehouse | Microsoft Azure"
   description="Forstå på dokumentsammenfald og arbejdsbelastningen management i Azure SQL Data Warehouse til udvikling af løsninger."
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
   ms.date="09/27/2016"
   ms.author="sonyama;barbkess;jrj"/>

# <a name="concurrency-and-workload-management-in-sql-data-warehouse"></a>På dokumentsammenfald og arbejdsbelastningen management i SQL Data Warehouse

For at give mere forudsigelige ydeevne skaleres Microsoft Azure SQL Data Warehouse hjælper med at styrer du på dokumentsammenfald niveauer og Ressourceallokeringer som hukommelse og CPU prioritering. I denne artikel introducerer dig til begreberne på dokumentsammenfald og arbejdsbelastningen management, som oplyser om, hvordan begge funktioner er gennemført, og hvordan du kan styre dem i dit datawarehouse. Administration af SQL Data Warehouse arbejdsbelastning er beregnet på at yde support til flere brugere miljøer. Det er ikke beregnet til flere lejer arbejdsmængder.

## <a name="concurrency-limits"></a>Begrænsninger på dokumentsammenfald

SQL Data Warehouse tillader op til 1.024 samtidige forbindelser. Alle 1.024 forbindelser kan sende forespørgsler samtidigt. Hvis du vil optimere overførselshastighed kan SQL Data Warehouse kø nogle forespørgsler for at sikre, at hver forespørgsel modtager en minimale hukommelse bonus. Queuing optræder i forespørgsel kørselstid. Ved kø forespørgsler når på dokumentsammenfald begrænsninger er nået, kan SQL Data Warehouse øge samlede gennemløb ved at sikre, at aktive forespørgsler få adgang til alvorligt nødvendige hukommelsesressourcer.  

Begrænsninger på dokumentsammenfald bestemmes af to begreber: *samtidige forespørgsler* og *på dokumentsammenfald pladser*. For en forespørgsel for at udføre, skal den udføre i både forespørgsel på dokumentsammenfald grænsen og på dokumentsammenfald slot fordelingen.

- Samtidige forespørgsler er de forespørgsler, der er udført på samme tid. SQL Data Warehouse understøtter op til 32 samtidige forespørgsler på større DWU.
- På dokumentsammenfald pladser fordeles baseret på DWU. Hver 100 DWU indeholder 4 på dokumentsammenfald pladser. For eksempel en DW100 allokerer 4 på dokumentsammenfald pladser og DW1000 allokerer 40. Hver forespørgsel bruger en eller flere på dokumentsammenfald pladser, afhænger af den [ressourceklasse](#resource-classes) i forespørgslen. Forespørgsler, der kører i klassen smallrc ressource forbruge én på dokumentsammenfald slot. Forespørgsler, der kører i en højere ressourceklasse forbruge flere på dokumentsammenfald pladser.

Tabellen nedenfor beskrives begrænsningerne for både samtidige forespørgsler og på dokumentsammenfald pladser på forskellige DWU størrelsen.

### <a name="concurrency-limits"></a>Begrænsninger på dokumentsammenfald

|  DWU   | Maks samtidige forespørgsler  | På dokumentsammenfald pladser allokeres |
| :----  | :---------------------: | :-------------------------: |
| DW100  |            4            |                4            |
| DW200  |            8            |                8            |
| DW300  |           12            |               12            |
| DW400  |           16            |               16            |
| DW500  |           20            |               20            |
| DW600  |           24            |               24            |
| DW1000 |           32            |               40            |
| DW1200 |           32            |               48            |
| DW1500 |           32            |               60            |
| DW2000 |           32            |               80            |
| DW3000 |           32            |              120            |
| DW6000 |           32            |              240            |

Når en af disse tærskler er opfyldt, er nye forespørgsler i kø og udføres på grundlag første-i first-out.  Efterhånden som en forespørgsler afsluttes, og antallet af forespørgsler og pladser falder under begrænsningerne, frigives i kø forespørgsler. 

> [AZURE.NOTE]  *Vælg* forespørgsler afholde udelukkende på Administration af dynamiske visninger (DMVs) eller katalog visninger er ikke omfattet af nogen af på dokumentsammenfald grænserne. Du kan overvåge systemet uanset antallet af forespørgsler, der er udført på den.

## <a name="resource-classes"></a>Ressourceklasser

Ressource klasser Hjælp du styre hukommelsesallokering og CPU skifter til en forespørgsel. Du kan tildele fire ressourceklasser for en bruger i form af *databaseroller*. De fire ressourceklasser er **smallrc**, **mediumrc**, **largerc**og **xlargerc**. Brugere i smallrc der får en mindre mængde hukommelse og kan drage fordel af højere på dokumentsammenfald. I modsætning brugere, der er tildelt til xlargerc er givet store mængder hukommelse, og derfor færre deres forespørgsler kan køre samtidig.

Hver bruger er medlem af Ressourceklassen small, smallrc som standard. Fremgangsmåden `sp_addrolemember` bruges til at øge Ressourceklassen og `sp_droprolemember` bruges til at mindske Ressourceklassen. For eksempel vil denne kommando øge Ressourceklassen af loaduser til largerc:

```sql
EXEC sp_addrolemember 'largerc', 'loaduser'
```

Der er en god ide permanent tildele brugere til en ressourceklasse i stedet for at ændre deres ressourceklasser. For eksempel oprette indlæser til grupperet columnstore tabeller højere kvalitet indeks når allokeres mere hukommelse. Oprette en bruger specifikt for indlæsning af data for at sikre, at indlæse har adgang til højere hukommelse, og tildele permanent denne bruger til et højere ressourceklasse.

Der er nogle typer af forespørgsler, der ikke få glæde af en større hukommelsesallokering. Systemet ignorerer deres ressourceallokering klasse og altid køre disse forespørgsler i Ressourceklassen small i stedet. Hvis disse forespørgsler kører altid i Ressourceklassen small, kan de køre når på dokumentsammenfald pladser er under tryk og de kan ikke bruge flere pladser end det er nødvendigt. Du kan få flere oplysninger i [ressource klasse undtagelser](#query-exceptions-to-concurrency-limits) .

Nogle flere oplysninger om ressourceklasse:

- *Ændre rolle* tilladelse er påkrævet til at ændre Ressourceklassen for en bruger.  
- Selvom du kan føje en bruger til en eller flere af de højere ressourceklasser, træder brugere på attributterne for den højeste ressourceklasse, de er tildelt. Det vil sige, hvis en bruger er tildelt til både mediumrc og largerc, er den højere ressourceklasse (largerc) Ressourceklassen, som anvendes.  
- Ressourceklassen for den administrative systembruger kan ikke ændres.

Du kan finde et detaljeret eksempel [ændring bruger ressource klasse eksempel](#changing-user-resource-class-example).

## <a name="memory-allocation"></a>Hukommelsesallokering

Der er fordele og ulemper at øge en brugers ressourceklasse. Øge en ressourceklasse for en bruger kan give deres forespørgsler adgang til flere hukommelsen, hvilket kan medføre forespørgsler udføres hurtigere.  Dog reducere højere ressourceklasser også antallet af samtidige forespørgsler, som kan køre. Dette er forholdet mellem fordeling af store mængder hukommelse til en enkelt forespørgsel eller tillade andre forespørgsler, der skal også hukommelse tildelinger, kan køre samtidig. Hvis en bruger angives høj tildelinger hukommelse til en forespørgsel, har andre brugere ikke adgang til den samme hukommelse til at køre en forespørgsel.

Den følgende tabel knytter hukommelse tildeles hver fordeling af DWU og ressource klasse.

### <a name="memory-allocations-per-distribution-mb"></a>Hukommelse tildelinger per fordeling (MB)

|  DWU   | smallrc | mediumrc | largerc | xlargerc |
| :----- | :-----: | :------: | :-----: | :------: |
| DW100  |   100   |    100   |    200  |    400   |
| DW200  |   100   |    200   |    400  |    800   |
| DW300  |   100   |    200   |    400  |    800   |
| DW400  |   100   |    400   |    800  |  1.600   |
| DW500  |   100   |    400   |    800  |  1.600   |
| DW600  |   100   |    400   |    800  |  1.600   |
| DW1000 |   100   |    800   |  1.600  |  3,200   |
| DW1200 |   100   |    800   |  1.600  |  3,200   |
| DW1500 |   100   |    800   |  1.600  |  3,200   |
| DW2000 |   100   |  1.600   |  3,200  |  6,400   |
| DW3000 |   100   |  1.600   |  3,200  |  6,400   |
| DW6000 |   100   |  3,200   |  6,400  |  12,800  |

Fra den forrige tabel, kan du se, at en forespørgsel, der kører på en DW2000 i klassen xlargerc ressource ville have adgang til 6.400 MB videohukommelse inden for hver af de 60 fordelte databaser.  I SQL datalager er der 60 salgsdistributioner. Derfor, hvis du vil beregne den samlede hukommelsesallokering for en forespørgsel i en given ressourceklasse, ovennævnte værdier skal multipliceres 60.

### <a name="memory-allocations-system-wide-gb"></a>Hukommelse tildelinger systemindstillinger (GB)

|  DWU   | smallrc | mediumrc | largerc | xlargerc |
| :----- | :-----: | :------: | :-----: | :------: |
| DW100  |    6    |    6     |    12   |    23    |
| DW200  |    6    |    12    |    23   |    47    |
| DW300  |    6    |    12    |    23   |    47    |
| DW400  |    6    |    23    |    47   |    94    |
| DW500  |    6    |    23    |    47   |    94    |
| DW600  |    6    |    23    |    47   |    94    |
| DW1000 |    6    |    47    |    94   |   188    |
| DW1200 |    6    |    47    |    94   |   188    |
| DW1500 |    6    |    47    |    94   |   188    |
| DW2000 |    6    |    94    |   188   |   375    |
| DW3000 |    6    |    94    |   188   |   375    |
| DW6000 |    6    |   188    |   375   |   750    |

Fra denne tabel med hele systemet hukommelse tildelinger, du kan se, at en forespørgsel, der kører på en DW2000 i klassen xlargerc ressource er allokeret samlet 375 GB hukommelse (6.400 MB * 60 salgsdistributioner / 1.024 konverteres til GB) over helhed af din SQL Data Warehouse.

## <a name="concurrency-slot-consumption"></a>På dokumentsammenfald slot forbrug

SQL Data Warehouse giver mere hukommelse til forespørgsler, der kører i højere ressourceklasser. Hukommelse er en fast ressource.  Derfor, jo mere hukommelse, der er allokeret forespørgsel, færre samtidige forespørgsler kan udføre. Den følgende tabel reiterates alle de forrige begreber i en enkelt visning, der viser antallet af tilgængelige ved DWU på dokumentsammenfald pladser og pladser consumed ved hver ressourceklasse.

### <a name="allocation-and-consumption-of-concurrency-slots"></a>Allokering og forbrug af pladser på dokumentsammenfald

|  DWU   | Maksimalt antal samtidige forespørgsler  | På dokumentsammenfald pladser allokeres | Pladser, der bruges af smallrc |  Pladser, der bruges af mediumrc |  Pladser, der bruges af largerc |  Pladser, der bruges af xlargerc |
| :----  | :---------------------: | :-------------------------: | :-----: | :------: | :-----: | :------: |
| DW100  |            4            |                4            |    1    |     1    |    2    |    4     |
| DW200  |            8            |                8            |    1    |     2    |    4    |    8     |
| DW300  |           12            |               12            |    1    |     2    |    4    |    8     |
| DW400  |           16            |               16            |    1    |     4    |    8    |   16     |
| DW500  |           20            |               20            |    1    |     4    |    8    |   16     |
| DW600  |           24            |               24            |    1    |     4    |    8    |   16     |
| DW1000 |           32            |               40            |    1    |     8    |   16    |   32     |
| DW1200 |           32            |               48            |    1    |     8    |   16    |   32     |
| DW1500 |           32            |               60            |    1    |     8    |   16    |   32     |
| DW2000 |           32            |               80            |    1    |    16    |   32    |   64     |
| DW3000 |           32            |              120            |    1    |    16    |   32    |   64     |
| DW6000 |           32            |              240            |    1    |    32    |   64    |  128     |


Du kan se SQL Data Warehouse, der løber fra denne tabel, som DW1000 allokerer maksimalt 32 samtidige forespørgsler og alt 40 på dokumentsammenfald pladser. Hvis alle brugere kører i smallrc, vil 32 samtidige forespørgsler tilladt, fordi hver forespørgsel optager 1 på dokumentsammenfald plads. Hvis alle brugere på en DW1000 kørte i mediumrc, hver forespørgsel ville være fordelt 800 MB fordeling for en samlet hukommelsesallokering på 47 GB per forespørgsel, og på dokumentsammenfald ville være begrænset til 5 brugere (40 på dokumentsammenfald pladser / 8 pladser per mediumrc bruger).

## <a name="query-importance"></a>Forespørgsel prioritet

SQL Data Warehouse implementerer ressourceklasser ved hjælp af arbejdsbelastningen grupper. Der er otte arbejdsbelastningen grupper, der styrer funktionaliteten for ressourceklasser på tværs af forskellige DWU størrelsen alt. For en hvilken som helst DWU bruger SQL Data Warehouse kun fire ud af grupperne otte arbejdsbyrde. Dette giver mening, fordi hver arbejdsbelastningen gruppe er tildelt til en af fire ressourceklasser: smallrc, mediumrc, largerc, eller xlargerc. Vigtigheden af at forstå grupperne arbejdsbyrde er, at nogle af disse arbejdsbelastningen grupper er indstillet til højere *prioritet*. Prioritet bruges til CPU planlægning. Forespørgsler, der køres med høj prioritet får tre gange mere CPU-skifter end dem med medium prioritet. Derfor bestemme på dokumentsammenfald slot tilknytninger også CPU prioritet. Når en forespørgsel forbruger 16 eller flere pladser, køres der med høj prioritet.

Følgende tabel viser prioritet tilknytningerne for hver arbejdsbelastningen gruppe.

### <a name="workload-group-mappings-to-concurrency-slots-and-importance"></a>Arbejdsbelastningen gruppetilknytninger til på dokumentsammenfald pladser og prioritet

| Arbejdsbelastningen grupper | På dokumentsammenfald slot tilknytning | MB / fordeling | Prioritet tilknytning |
| :-------------- | :----------------------: | :---------------: | :----------------- |
| SloDWGroupC00   |            1             |         100       |       Mediet       |
| SloDWGroupC01   |            2             |         200       |       Mediet       |
| SloDWGroupC02   |            4             |         400       |       Mediet       |
| SloDWGroupC03   |            8             |         800       |       Mediet       |
| SloDWGroupC04   |           16             |       1.600       |       Høj         |
| SloDWGroupC05   |           32             |       3,200       |       Høj         |
| SloDWGroupC06   |           64             |       6,400       |       Høj         |
| SloDWGroupC07   |          128             |      12,800       |       Høj         |

Fra **allokering og forbrug af på dokumentsammenfald pladser** diagrammet, kan du se, at en DW500 bruger 1, 4, 8 eller 16 på dokumentsammenfald pladser til smallrc, mediumrc, largerc og xlargerc, henholdsvis. Du kan slå disse værdier op i foregående diagrammet for at finde prioritet for hver enkelt ressourceklasse.

### <a name="dw500-mapping-of-resource-classes-to-importance"></a>DW500 tilknytningen af ressourceklasser til prioritet

| Ressourceklasse | Arbejdsbelastningen gruppe | På dokumentsammenfald pladser bruges | MB / fordeling | Prioritet |
| :------------- | :------------- | :--------------------: | :---------------: | :--------- |
| smallrc        | SloDWGroupC00  |           1            |         100       |   Mediet   |
| mediumrc       | SloDWGroupC02  |           4            |         400       |   Mediet   |
| largerc        | SloDWGroupC03  |           8            |         800       |   Mediet   |
| xlargerc       | SloDWGroupC04  |          16            |        1.600      |   Høj     |


Du kan bruge følgende DMV forespørgsel at kigge på forskelle i hukommelse ressourceallokering detaljeret fra perspektivet ressource regulatoren eller til at analysere aktive og historiske brugen af grupperne arbejdsbelastningen, når fejlfinding.

```sql
WITH rg
AS
(   SELECT  
     pn.name                        AS node_name
    ,pn.[type]                      AS node_type
    ,pn.pdw_node_id                 AS node_id
    ,rp.name                        AS pool_name
    ,rp.max_memory_kb*1.0/1024              AS pool_max_mem_MB
    ,wg.name                        AS group_name
    ,wg.importance                  AS group_importance
    ,wg.request_max_memory_grant_percent        AS group_request_max_memory_grant_pcnt
    ,wg.max_dop                     AS group_max_dop
    ,wg.effective_max_dop               AS group_effective_max_dop
    ,wg.total_request_count             AS group_total_request_count
    ,wg.total_queued_request_count          AS group_total_queued_request_count
    ,wg.active_request_count                AS group_active_request_count
    ,wg.queued_request_count                AS group_queued_request_count
    FROM    sys.dm_pdw_nodes_resource_governor_workload_groups wg
    JOIN    sys.dm_pdw_nodes_resource_governor_resource_pools rp    
            ON  wg.pdw_node_id  = rp.pdw_node_id
            AND wg.pool_id      = rp.pool_id
    JOIN    sys.dm_pdw_nodes pn
            ON  wg.pdw_node_id  = pn.pdw_node_id
    WHERE   wg.name like 'SloDWGroup%'
        AND     rp.name = 'SloDWPool'
)
SELECT  pool_name
,       pool_max_mem_MB
,       group_name
,       group_importance
,       (pool_max_mem_MB/100)*group_request_max_memory_grant_pcnt AS max_memory_grant_MB
,       node_name
,       node_type
,       group_total_request_count
,       group_total_queued_request_count
,       group_active_request_count
,       group_queued_request_count
FROM    rg
ORDER BY
    node_name
,   group_request_max_memory_grant_pcnt
,   group_importance
;
```

## <a name="queries-that-honor-concurrency-limits"></a>Forespørgsler, der imødekomme på dokumentsammenfald begrænsninger

De fleste forespørgsler er underlagt ressourceklasser. Disse forespørgsler skal passer ind i begge de samtidige forespørgsel og på dokumentsammenfald slot tærskler. En bruger kan ikke vælge at udelade en forespørgsel fra på dokumentsammenfald slot modellen.

Hvis du vil gentage, imødekomme følgende udtalelser ressourceklasser:

- INDSÆT VÆLG
- OPDATER
- SLET
- MARKERE (når forespørgsler bruger tabeller)
- ALTER INDEKS GENOPBYG
- ALTER INDEKS OMORGANISERE
- ALTER TABEL GENOPBYG
- OPRETTE INDEKS
- OPRETTE GRUPPERET COLUMNSTORE INDEKS
- OPRETTE TABEL SOM VÆLG (CTAS)
- Indlæsning af data
- Data bevægelse, der udføres ved Data bevægelse Service (DMS)

## <a name="query-exceptions-to-concurrency-limits"></a>Forespørgsel undtagelser til på dokumentsammenfald begrænsninger

Nogle forespørgsler kan ikke angives Ressourceklassen, som brugeren har fået tildelt. Disse undtagelser begrænsningerne på dokumentsammenfald foretages, når de hukommelsesressourcer, der kræves for en bestemt kommando er lav, ofte fordi kommandoen er en handling af metadata. Formålet med disse undtagelser er at undgå større hukommelse tildelinger for forespørgsler, der aldrig skal dem. Standardklassen small ressource (smallrc) bruges i disse tilfælde altid uanset klassen faktisk ressource er tildelt til brugeren. For eksempel `CREATE LOGIN` altid kører i smallrc. De ressourcer, der bruges til at udføre denne handling er meget lille, så det ikke giver mening at medtage forespørgslen i på dokumentsammenfald slot modellen.  Disse forespørgsler er også ikke begrænset af 32-bruger på dokumentsammenfald grænse, kan et ubegrænset antal disse forespørgsler kan køre til sessionsgrænsen på 1.024 sessioner.

Følgende udtalelser kan ikke angives ressourceklasser:

- Opret eller fjernelse af tabellen
- ÆNDRE TABEL... Skift, opdelt eller FLETTE PARTITION
- ÆNDRE INDEKS DEAKTIVER
- SLIP INDEKS
- Opret, Opdater eller slip STATISTIK
- AFKORTE TABEL
- ÆNDRE GODKENDELSE
- OPRETTE LOGON
- Opret, ALTER eller DROP USER
- Opret, ALTER eller slip PROCEDURE
- Opret eller slip visning
- INDSÆT VÆRDIER
- Vælg systemvisninger og DMVs
- FORKLAR
- DBCC

<!--
Removed as these two are not confirmed / supported under SQLDW
- CREATE REMOTE TABLE AS SELECT
- CREATE EXTERNAL TABLE AS SELECT
- REDISTRIBUTE
-->

## <a name="change-a-user-resource-class-example"></a>Ændre en bruger ressource klasse-eksempel

1. **Opret login:** Åbn en forbindelse til databasen **masterside** på den SQL server, der er vært for SQL Data Warehouse databasen og udføre følgende kommandoer.

    ```sql
    CREATE LOGIN newperson WITH PASSWORD = 'mypassword';
    CREATE USER newperson for LOGIN newperson;
    ```

    > [AZURE.NOTE] Det er en god ide at oprette en bruger i den overordnede database til Azure SQL Data Warehouse brugere. Oprette en bruger i master gør det muligt for en bruger til at logge på ved hjælp af værktøjer som SSMS uden at angive et navn på databasen.  Det gør det også dem om at bruge objekt Stifinder til at få vist alle databaser på en SQL server.  Du kan finde flere oplysninger om oprettelse og administration af brugere, [Secure en database i SQL Data Warehouse][].

2. **Oprette SQL Data Warehouse bruger:** Åbn en forbindelse til databasens **SQL Data Warehouse** og udføre følgende kommando.

    ```sql
    CREATE USER newperson FOR LOGIN newperson;
    ```

3. **Give tilladelser:** I følgende eksempel giver `CONTROL` på **SQL Data Warehouse** databasen. `CONTROL`i databasen er niveau udgave af db_owner i SQL Server.

    ```sql
    GRANT CONTROL ON DATABASE::MySQLDW to newperson;
    ```

4. **Øge ressourceklasse:** Brug følgende forespørgsel til at tilføje en bruger til en højere arbejdsbelastningen management rolle.

    ```sql
    EXEC sp_addrolemember 'largerc', 'newperson'
    ```

5. **Mindske ressourceklasse:** Brug følgende forespørgsel til at fjerne en bruger fra en arbejdsbelastningen administrationsrolle.

    ```sql
    EXEC sp_droprolemember 'largerc', 'newperson';
    ```

    > [AZURE.NOTE] Det er ikke muligt at fjerne en bruger fra smallrc.

## <a name="queued-query-detection-and-other-dmvs"></a>I kø forespørgsel registrering og andre DMVs

Du kan bruge den `sys.dm_pdw_exec_requests` DMV til at identificere forespørgsler, der venter i en på dokumentsammenfald kø. Forespørgsler, der venter på en på dokumentsammenfald plads har statussen **suspenderet**.

```sql
SELECT   r.[request_id]              AS Request_ID
        ,r.[status]              AS Request_Status
        ,r.[submit_time]             AS Request_SubmitTime
        ,r.[start_time]              AS Request_StartTime
        ,DATEDIFF(ms,[submit_time],[start_time]) AS Request_InitiateDuration_ms
        ,r.resource_class                         AS Request_resource_class
FROM    sys.dm_pdw_exec_requests r;
```

Arbejdsbelastningen management roller kan ses med `sys.database_principals`.

```sql
SELECT  ro.[name]           AS [db_role_name]
FROM    sys.database_principals ro
WHERE   ro.[type_desc]      = 'DATABASE_ROLE'
AND     ro.[is_fixed_role]  = 0;
```

Følgende forespørgsel viser hvilken administratorrolle, der er tildelt hver enkelt bruger.

```sql
SELECT   r.name AS role_principal_name
        ,m.name AS member_principal_name
FROM    sys.database_role_members rm
JOIN    sys.database_principals AS r            ON rm.role_principal_id     = r.principal_id
JOIN    sys.database_principals AS m            ON rm.member_principal_id   = m.principal_id
WHERE   r.name IN ('mediumrc','largerc', 'xlargerc');
```

SQL Data Warehouse har følgende vente typer:

- **LocalQueriesConcurrencyResourceType**: forespørgsler, der sidde uden for på dokumentsammenfald slot framework. DMV forespørgsler og system fungerer som `SELECT @@VERSION` er eksempler på lokale forespørgsler.
- **UserConcurrencyResourceType**: forespørgsler, der sidde i på dokumentsammenfald slot framework. Forespørgsler slutbrugerlicensaftale tabeller repræsenterer eksempler, der skal bruge denne ressourcetype.
- **DmsConcurrencyResourceType**: venter som følge af datahandlinger bevægelse.
- **BackupConcurrencyResourceType**: denne Vent angiver, at en database er sikkerhedskopieres. Den maksimale værdi for denne ressourcetype er 1. Hvis flere sikkerhedskopier har anmodet om på samme tid, vil de andre kø.

Den `sys.dm_pdw_waits` DMV kan bruges til at se de ressourcer, der venter på en anmodning.

```sql
SELECT  w.[wait_id]
,       w.[session_id]
,       w.[type]            AS Wait_type
,       w.[object_type]
,       w.[object_name]
,       w.[request_id]
,       w.[request_time]
,       w.[acquire_time]
,       w.[state]
,       w.[priority]
,   SESSION_ID()            AS Current_session
,   s.[status]          AS Session_status
,   s.[login_name]
,   s.[query_count]
,   s.[client_id]
,   s.[sql_spid]
,   r.[command]         AS Request_command
,   r.[label]
,   r.[status]          AS Request_status
,   r.[submit_time]
,   r.[start_time]
,   r.[end_compile_time]
,   r.[end_time]
,   DATEDIFF(ms,r.[submit_time],r.[start_time])     AS Request_queue_time_ms
,   DATEDIFF(ms,r.[start_time],r.[end_compile_time])    AS Request_compile_time_ms
,   DATEDIFF(ms,r.[end_compile_time],r.[end_time])      AS Request_execution_time_ms
,   r.[total_elapsed_time]
FROM    sys.dm_pdw_waits w
JOIN    sys.dm_pdw_exec_sessions s  ON w.[session_id] = s.[session_id]
JOIN    sys.dm_pdw_exec_requests r  ON w.[request_id] = r.[request_id]
WHERE   w.[session_id] <> SESSION_ID();
```

Den `sys.dm_pdw_resource_waits` DMV viser kun de ressource venter, der er brugt i en given forespørgsel. Ressource ventetiden måler kun den tid, der venter for de ressourcer, der skal gives, i modsætning til signal ventetid, som er den tid, det tager for de underliggende SQL-servere til at planlægge forespørgslen på CPU'EN.

```sql
SELECT  [session_id]
,       [type]
,       [object_type]
,       [object_name]
,       [request_id]
,       [request_time]
,       [acquire_time]
,       DATEDIFF(ms,[request_time],[acquire_time])  AS acquire_duration_ms
,       [concurrency_slots_used]                    AS concurrency_slots_reserved
,       [resource_class]
,       [wait_id]                                   AS queue_position
FROM    sys.dm_pdw_resource_waits
WHERE   [session_id] <> SESSION_ID();
```

Den `sys.dm_pdw_wait_stats` DMV kan bruges til historiske tendensanalyse af venter.

```sql
SELECT  w.[pdw_node_id]
,       w.[wait_name]
,       w.[max_wait_time]
,       w.[request_count]
,       w.[signal_time]
,       w.[completed_count]
,       w.[wait_time]
FROM    sys.dm_pdw_wait_stats w;
```

## <a name="next-steps"></a>Næste trin

Du kan finde flere oplysninger om administration af brugere og sikkerhed, [Secure en database i SQL Data Warehouse][]. Du kan finde flere oplysninger om, hvordan større ressource klasser kan forbedre kvaliteten på grupperet columnstore indeks, skal du se [under genopbygning indeks til at forbedre kvaliteten på målgruppen].

<!--Image references-->

<!--Article references-->
[Sikre en database i SQL Data Warehouse]: ./sql-data-warehouse-overview-manage-security.md
[Genopbygge indeks til at forbedre kvaliteten på segmentet]: ./sql-data-warehouse-tables-index.md#rebuilding-indexes-to-improve-segment-quality
[Sikre en database i SQL Data Warehouse]: ./sql-data-warehouse-overview-manage-security.md

<!--MSDN references-->
[Managing Databases and Logins in Azure SQL Database]:https://msdn.microsoft.com/library/azure/ee336235.aspx

<!--Other Web references-->
