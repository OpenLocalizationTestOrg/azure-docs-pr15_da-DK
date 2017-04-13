<properties
    pageTitle="Afbryde og genoptage dataoverførsel (Stræk Database) | Microsoft Azure"
    description="Lær at afbryde eller genoptage dataoverførsel til Azure."
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
    ms.date="06/14/2016"
    ms.author="douglasl"/>

# <a name="pause-and-resume-data-migration-stretch-database"></a>Afbryde og genoptage dataoverførsel (Stræk Database)

Vælg **Stræk** til en tabel i SQL Server Management Studio for at afbryde midlertidigt eller genoptage dataoverførsel til Azure, og vælg derefter **Peg** til at afbryde midlertidigt dataoverførsel eller **genoptage** fortsætte med at dataoverførsel. Du kan også bruge Transact\-SQL afbryde midlertidigt eller genoptage dataoverførsel.

Pause dataoverførsel for enkelte tabeller, når du vil foretage fejlfinding af problemer på den lokale server eller maksimere den tilgængelige netværksbåndbredde.

## <a name="pause-data-migration"></a>Pause dataoverførsel

### <a name="use-sql-server-management-studio-to-pause-data-migration"></a>Brug af SQL Server Management Studio til at afbryde midlertidigt dataoverførsel

1.  Vælg Stræk i SQL Server Management Studio i Object Explorer\-aktiveret tabel, du vil afbryde dataoverførsel.

2.  Højre\-skal du klikke på og vælg **Stræk**, og vælg derefter **Pause**.

### <a name="use-transact-sql-to-pause-data-migration"></a>Bruge Transact\-SQL til at afbryde midlertidigt dataoverførsel
Kør følgende kommando.

```tsql
USE <Stretch-enabled database name>;
GO
ALTER TABLE <Stretch-enabled table name>  
    SET ( REMOTE_DATA_ARCHIVE ( MIGRATION_STATE = PAUSED ) ) ;  
GO
```

## <a name="resume-data-migration"></a>CV dataoverførsel

### <a name="use-sql-server-management-studio-to-resume-data-migration"></a>Brug SQL Server Management Studio til at genoptage dataoverførsel

1.  Vælg Stræk i SQL Server Management Studio i Object Explorer\-aktiveret tabel, du vil genoptage dataoverførsel.

2.  Højre\-skal du klikke på og vælg **Stræk**, og vælg derefter **CV**.

### <a name="use-transact-sql-to-resume-data-migration"></a>Bruge Transact\-SQL fortsætte med at dataoverførsel
Kør følgende kommando.

```tsql
USE <Stretch-enabled database name>;
GO
ALTER TABLE <Stretch-enabled table name>   
    SET ( REMOTE_DATA_ARCHIVE ( MIGRATION_STATE = OUTBOUND ) ) ;  
 GO
```

## <a name="check-whether-migration-is-active-or-paused"></a>Kontrollere, om overførsel er aktiv eller er stoppet midlertidigt

### <a name="use-sql-server-management-studio-to-check-whether-migration-is-active-or-paused"></a>Bruge SQL Server Management Studio til at kontrollere, om overførsel er aktiv eller er stoppet midlertidigt
I SQL Server Management Studio skal du åbne **Stræk Database skærm** og kontrollere værdien af kolonnen **Overførsel tilstand** . Du finder flere oplysninger i [skærm og foretage fejlfinding af dataoverførsel](sql-server-stretch-database-monitor.md).

### <a name="use-transact-sql-to-check-whether-migration-is-active-or-paused"></a>Bruge Transact-SQL til at kontrollere, om overførsel er aktiv eller er stoppet midlertidigt
Forespørgsel katalog visning **sys.remote_data_archive_tables** og kontrollere værdien af kolonnen **is_migration_paused** . Du kan finde flere oplysninger om [sys.remote_data_archive_tables](https://msdn.microsoft.com/library/dn935003.aspx).

## <a name="see-also"></a>Se også

[ALTER TABLE (Transact-SQL)](https://msdn.microsoft.com/library/ms190273.aspx)
[skærm og foretage fejlfinding af dataoverførsel](sql-server-stretch-database-monitor.md)
