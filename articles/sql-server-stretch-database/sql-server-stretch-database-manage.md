<properties
    pageTitle="Administrere og foretage fejlfinding af Stræk Database | Microsoft Azure"
    description="Lær at administrere og foretage fejlfinding af Stræk Database."
    services="sql-server-stretch-database"
    documentationCenter=""
    authors="douglaslMS"
    manager="jhubbard"
    editor=""/>

<tags
    ms.service="sql-server-stretch-database"
    ms.workload="data-management"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="06/27/2016"
    ms.author="douglasl"/>

# <a name="manage-and-troubleshoot-stretch-database"></a>Administrere og foretage fejlfinding af Stræk Database

Administrere og foretage fejlfinding af Stræk Database, skal du bruge værktøjer og metoder, der er beskrevet i dette emne.

## <a name="manage-local-data"></a>Administrere lokale data

### <a name="LocalInfo"></a>Få oplysninger om lokale databaser og tabeller, der er aktiveret for Stræk Database
Åbne katalog visninger **sys.databases** og **sys.tables** at se oplysninger om Stræk\-aktiveret SQL Server-databaser og tabeller. Flere oplysninger i [sys.databases (Transact-SQL)](https://msdn.microsoft.com/library/ms178534.aspx) og [sys.tables (Transact-SQL)](https://msdn.microsoft.com/library/ms187406.aspx).

Se hvor meget plads en Stræk\-aktiveret tabellen bruger i SQL Server, Kør følgende sætning.

 ```tsql
USE <Stretch-enabled database name>;
GO
EXEC sp_spaceused '<Stretch-enabled table name>', 'true', 'LOCAL_ONLY';
GO
 ```
## <a name="manage-data-migration"></a>Administrere dataoverførsel

### <a name="check-the-filter-function-applied-to-a-table"></a>Kontrollere filterfunktionen anvendes på en tabel
Åbne visningen katalog **sys.remote\_data\_arkiv\_tabeller** og kontrollere værdien af den **filter\_prædikat** kolonne til at identificere den funktion, der bruger Stræk Database til at markere rækker til at overføre. Hvis værdien er tom, er hele tabellen berettiget til at blive overført. Flere oplysninger i [sys.remote_data_archive_tables (Transact-SQL)](https://msdn.microsoft.com/library/dn935003.aspx) , og [Vælg rækker til at overføre ved hjælp af funktionen et filter](sql-server-stretch-database-predicate-function.md).

### <a name="Migration"></a>Kontrollere status for dataoverførsel
Vælg **opgaver | Stræk | Overvåge** til en database i SQL Server Management Studio til at overvåge dataoverførsel i Stræk Database skærm. Du finder flere oplysninger i [skærm og foretage fejlfinding af dataoverførsel (Stræk Database)](sql-server-stretch-database-monitor.md).

Åbne visningen dynamiske management **sys.dm\_db\_rda\_overførsel\_status** at se, hvor mange navne og datarækker overflyttet.

### <a name="Firewall"></a>Fejlfinding i forbindelse med overflytning af data
Om fejlfinding forslag finder du [skærm og foretage fejlfinding af dataoverførsel (Stræk Database)](sql-server-stretch-database-monitor.md).

## <a name="manage-remote-data"></a>Administrere eksterne data

### <a name="RemoteInfo"></a>Få oplysninger om remote databaser og tabeller, der bruges af Stræk Database
Åbn visningerne katalog **sys.remote\_data\_arkiv\_databaser** og **sys.remote\_data\_arkiv\_tabeller** at se oplysninger om de eksterne databaser og tabeller, hvor overførte data er gemt. Flere oplysninger i [sys.remote_data_archive_databases (Transact-SQL)](https://msdn.microsoft.com/library/dn934995.aspx) og [sys.remote_data_archive_tables (Transact-SQL)](https://msdn.microsoft.com/library/dn935003.aspx).

For at se, hvor meget plads bruger en Stræk aktiverede tabel i Azure, Kør følgende sætning.

 ```tsql
USE <Stretch-enabled database name>;
GO
EXEC sp_spaceused '<Stretch-enabled table name>', 'true', 'REMOTE_ONLY';
GO
 ```

### <a name="delete-migrated-data"></a>Slette overførte data  
Hvis du vil slette data, der er allerede blevet overført til Azure, skal du følge trinnene beskrevet i [sys.sp_rda_reconcile_batch](https://msdn.microsoft.com/library/mt707768.aspx).

## <a name="manage-table-schema"></a>Administrere tabelskema

### <a name="dont-change-the-schema-of-the-remote-table"></a>Ikke ændre skemaet for tabellen remote
Ikke ændre skemaet for en ekstern Azure tabel, der er knyttet til en SQL Server-tabel, der er konfigureret til Stræk Database. Især Undlad at ændre navnet eller datatypen for en kolonne. Funktionen Stræk Database gør forskellige forudsætninger om skemaet i tabellen ekstern i forhold til skemaet for SQL Server-tabellen. Hvis du ændrer remote skemaet, stopper Stræk Database, der arbejder for den ændrede tabel.

### <a name="reconcile-table-columns"></a>Afstemme tabelkolonner  
Hvis du har slettet kolonner ved et uheld kommer fra tabellen remote, køre **sp_rda_reconcile_columns** for at føje kolonner til tabellen remote, der findes i Stræk\-aktiveret SQL Server-tabel, men ikke i tabellen remote. Du kan finde flere oplysninger om [sys.sp_rda_reconcile_columns](https://msdn.microsoft.com/library/mt707765.aspx).  

  > [!IMPORTANT] Når **sp_rda_reconcile_columns** genskaber kolonner, som du slettes ved et uheld kommer fra tabellen remote, gendanner ikke de data, der allerede findes i de slettede kolonner.

**sp_rda_reconcile_columns** ikke slette kolonner fra tabellen remote, der findes i tabellen remote, men ikke i Stræk\-aktiveret SQL Server-tabel. Hvis der er kolonner i tabellen remote Azure, der ikke længere findes i Stræk\-aktiveret SQL Server tabellen disse ekstra kolonner forhindrer ikke Stræk Database i at fungere normalt. Du kan eventuelt fjerne de ekstra kolonner manuelt.  

## <a name="manage-performance-and-costs"></a>Administrere ydeevne og omkostninger  

### <a name="troubleshoot-query-performance"></a>Fejlfinding i forbindelse med forespørgselsydelse
Forespørgsler, der indeholder Stræk\-aktiverede tabeller der forventes til at udføre langsommere end de gjorde før tabellerne er aktiveret for Stræk. Hvis forespørgselsydelse forringer betydeligt, kan du gennemgå følgende mulige problemer.

-   Er din Azure server i et andet geografisk område end SQL Server? Konfigurere Azure serveren til at være i samme geografiske område som din SQL Server for at reducere netværksventetid.

-   Dit netværk betingelser muligvis har nedsat. Du kan kontakte din netværksadministrator for at få oplysninger om seneste problemer eller udfald.

### <a name="increase-azure-performance-level-for-resource-intensive-operations-such-as-indexing"></a>Øge Azure ydeevneniveau for ressource\-intensivt handlinger som indeksering
Når du opretter, genopbygge eller omorganisere et indeks i en stor tabel, der er konfigureret til Stræk Database, og du forventer at skulle tunge forespørgsler overførte data i Azure i denne periode, kan du overveje at øge tilsvarende Azure fjerndatabasen præstationsniveau for varigheden af handlingen. Du finder flere oplysninger om ydeevne og priser i [SQL Server Stræk Database priser](https://azure.microsoft.com/pricing/details/sql-server-stretch-database/).

### <a name="you-cant-pause-the-sql-server-stretch-database-service-on-azure"></a>Du kan ikke afbrydes midlertidigt tjenesten SQL Server-Stræk Database på Azure  
 Sørg for, at du vælger den relevante ydeevne og prisniveau. Hvis du øge ydeevneniveau midlertidigt for en ressource\-intensivt handling gendanne den til det forrige niveau, når handlingen er fuldført. Du finder flere oplysninger om ydeevne og priser i [SQL Server Stræk Database priser](https://azure.microsoft.com/pricing/details/sql-server-stretch-database/).  

## <a name="change-the-scope-of-queries"></a>Ændre omfanget af forespørgsler  
 Forespørgsler Stræk\-aktiveret tabeller returnerer både lokale og eksterne data som standard. Du kan ændre omfanget af forespørgsler for alle forespørgsler ved alle brugere eller kun en enkelt forespørgsel af en administrator.  

### <a name="change-the-scope-of-queries-for-all-queries-by-all-users"></a>Ændre omfanget af forespørgsler for alle forespørgsler ved alle brugere  
 For at ændre omfanget af alle forespørgsler ved alle brugere skal du køre den lagrede procedure **sys.sp_rda_set_query_mode**. Du kan reducere omfanget for at forespørge lokale data, deaktivere alle forespørgsler eller gendanne standardindstillingen. Du kan finde flere oplysninger om [sys.sp_rda_set_query_mode](https://msdn.microsoft.com/library/mt703715.aspx).  

### <a name="queryHints"></a>Ændre omfanget af forespørgsler til en enkelt forespørgsel af en administrator  
 Hvis du vil ændre omfanget af en enkelt forespørgsel af et medlem af rollen db_owner, skal du tilføje den * *med \( REMOTE_DATA_ARCHIVE_OVERRIDE = *værdi* \)** forespørgsel tip til SELECT-sætningen. REMOTE_DATA_ARCHIVE_OVERRIDE forespørgsel tip kan have følgende værdier.  
 -   **LOCAL_ONLY**. Forespørge lokale data.  

 -   **REMOTE_ONLY**. Forespørge eksterne data.  

 -   **STAGE_ONLY**. Forespørgsel kun dataene i tabellen, hvor Stræk Database faser rækker berettiget til overførsel og bevarer overførte rækker for den angivne periode efter overførslen. Tip denne forespørgsel er den eneste måde at forespørge på den midlertidige tabel.  

For eksempel returnerer følgende forespørgsel lokale resultater.  

 ```tsql  
 USE <Stretch-enabled database name>;
 GO
 SELECT * FROM <Stretch_enabled table name> WITH (REMOTE_DATA_ARCHIVE_OVERRIDE = LOCAL_ONLY) WHERE ... ;
 GO
```  

## <a name="adminHints"></a>Foretage administrative opdateringer og sletninger  
 Som standard, du ikke kan opdatere eller Slet rækker, der er berettiget til overførsel eller rækker, der er allerede blevet overført, i en Stræk\-aktiveret tabel. Når du skal løse et problem, et medlem af rollen db_owner kan køre handlingen opdatering eller sletning ved at tilføje den * *med \( REMOTE_DATA_ARCHIVE_OVERRIDE = *værdi* \)** forespørgsel tip til sætningen. REMOTE_DATA_ARCHIVE_OVERRIDE forespørgsel tip kan have følgende værdier.  
 -   **LOCAL_ONLY**. Opdatere eller slette lokale data.  

 -   **REMOTE_ONLY**. Opdatere eller slette eksterne data.  

 -   **STAGE_ONLY**. Opdatere eller slette kun dataene i tabellen, hvor Stræk Database faser rækker berettiget til overførsel og bevarer overførte rækker for den angivne periode efter overførslen.  

## <a name="see-also"></a>Se også

[Overvåge Stræk Database](sql-server-stretch-database-monitor.md)

[Sikkerhedskopiering Stræk-kompatible databaser](sql-server-stretch-database-backup.md)

[Gendanne Stræk-kompatible databaser](sql-server-stretch-database-restore.md)
