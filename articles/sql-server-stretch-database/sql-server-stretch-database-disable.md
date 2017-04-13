<properties
    pageTitle="Deaktivere Stræk Database og få vist igen fjerndata | Microsoft Azure"
    description="Lær, hvordan du deaktiverer Stræk Database til en tabel, og du kan også få igen, eksterne data."
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
    ms.date="08/05/2016"
    ms.author="douglasl"/>

# <a name="disable-stretch-database-and-bring-back-remote-data"></a>Deaktivere Stræk Database og få vist igen eksterne data

For at deaktivere Stræk Database til en tabel skal du vælge **Stræk** for en tabel i SQL Server Management Studio. Vælg derefter en af følgende indstillinger.

-   **Deaktiver | Hente data tilbage fra Azure**. Kopiér dataene til tabellen fra Azure tilbage til SQL Server, derefter deaktivere Stræk Database for tabellen. Denne handling medfører omkostningerne for overførsel af data, og den kan ikke annulleres.

-   **Deaktiver | Efterlade data i Azure**. Deaktivere Stræk Database for tabellen.  Afbryde de eksterne data til tabellen i Azure.

Du kan også bruge Transact\-SQL til at deaktivere Stræk Database til en tabel eller til en database.

Når du deaktiverer Stræk Database til en tabel, data overførsel Stop og forespørgselsresultater ikke længere at medtage resultater fra tabellen remote.

Hvis du blot vil Peg dataoverførsel, skal du se [Pause og CV Stræk Database](sql-server-stretch-database-pause.md).

>   [AZURE.NOTE] Deaktivere Stræk Database til en tabel eller til en database sletter ikke remote objektet. Hvis du vil slette tabellen remote eller fjerndatabasen, har du slippe den ved hjælp af administrationsportalen Azure. Remote objekterne fortsætte med at afholde Azure omkostninger, indtil du har slettet dem. Du finder flere oplysninger i [SQL Server Stræk Database priser](https://azure.microsoft.com/pricing/details/sql-server-stretch-database/).

## <a name="disable-stretch-database-for-a-table"></a>Deaktivere Stræk Database til en tabel

### <a name="use-sql-server-management-studio-to-disable-stretch-database-for-a-table"></a>Brug SQL Server Management Studio til at deaktivere Stræk Database til en tabel

1.  Vælg den tabel, hvor du vil deaktivere Stræk Database i SQL Server Management Studio i Object Explorer.

2.  Højre\-skal du klikke på Vælg **Stræk**og derefter vælge en af følgende indstillinger.

    -   **Deaktiver | Hente data tilbage fra Azure**. Kopiér dataene til tabellen fra Azure tilbage til SQL Server, derefter deaktivere Stræk Database for tabellen. Denne kommando kan ikke annulleres.

        >   [AZURE.NOTE] Kopiere dataene til tabellen fra Azure påløber tilbage til SQL Server, omkostningerne for overførsel af data. Du finder flere oplysninger i [Overførsler priser Datadetaljer](https://azure.microsoft.com/pricing/details/data-transfers/).

        Når alle dataene er blevet kopieret fra Azure tilbage til SQL Server, deaktiveres Stræk for tabellen.

    -   **Deaktiver | Efterlade data i Azure**. Deaktivere Stræk Database for tabellen.  Afbryde de eksterne data til tabellen i Azure.

    >   [AZURE.NOTE] Deaktivere Stræk Database til en tabel slettes ikke de eksterne data eller remote tabellen. Hvis du vil slette tabellen remote, har du slippe den ved hjælp af administrationsportalen Azure. Tabellen remote fortsætter med at betale Azure omkostninger, indtil du slette den. Du finder flere oplysninger i [SQL Server Stræk Database priser](https://azure.microsoft.com/pricing/details/sql-server-stretch-database/).

### <a name="use-transact-sql-to-disable-stretch-database-for-a-table"></a>Bruge Transact\-SQL for at deaktivere Stræk Database til en tabel

-   Hvis du vil deaktivere Stræk til en tabel og Kopiér dataene til tabellen fra Azure tilbage til SQL Server, skal du køre følgende kommando. Når alle dataene er blevet kopieret fra Azure tilbage til SQL Server, deaktiveres Stræk for tabellen.

    Denne kommando kan ikke annulleres.

    ```tsql
    USE <Stretch-enabled database name>;
    GO
    ALTER TABLE <Stretch-enabled table name>  
       SET ( REMOTE_DATA_ARCHIVE ( MIGRATION_STATE = INBOUND ) ) ;
    GO
    ```
    >   [AZURE.NOTE] Kopiere dataene til tabellen fra Azure påløber tilbage til SQL Server, omkostningerne for overførsel af data. Du kan finde flere oplysninger om [Overførsler priser Datadetaljer](https://azure.microsoft.com/pricing/details/data-transfers/).

-   For at deaktivere Stræk for en tabel og annullere dataene skal du køre følgende kommando.

    ```tsql
    ALTER TABLE <table_name>
       SET ( REMOTE_DATA_ARCHIVE = OFF_WITHOUT_DATA_RECOVERY ( MIGRATION_STATE = PAUSED ) ) ;
    ```

>   [AZURE.NOTE] Deaktivere Stræk Database til en tabel slettes ikke de eksterne data eller remote tabellen. Hvis du vil slette tabellen remote, har du slippe den ved hjælp af administrationsportalen Azure. Tabellen remote fortsætter med at betale Azure omkostninger, indtil du slette den. Du finder flere oplysninger i [SQL Server Stræk Database priser](https://azure.microsoft.com/pricing/details/sql-server-stretch-database/).

## <a name="disable-stretch-database-for-a-database"></a>Deaktivere Stræk Database til en database
Før du kan deaktivere Stræk Database til en database, har du deaktivere Stræk Database på de enkelte Stræk\-aktiveret tabellerne i databasen.

### <a name="use-sql-server-management-studio-to-disable-stretch-database-for-a-database"></a>Brug SQL Server Management Studio til at deaktivere Stræk Database til en database

1.  Vælg den database, som du vil deaktivere Stræk Database i SQL Server Management Studio i Object Explorer.

2.  Højre\-skal du klikke på og vælg **opgaver**, og vælg derefter **Stræk**, og vælg derefter **Deaktiver**.

>   [AZURE.NOTE] Deaktivere Stræk Database til en database kan ikke slette den eksterne database. Hvis du vil slette fjerndatabasen, har du slippe den ved hjælp af administrationsportalen Azure. Fjerndatabasen fortsætter med at betale Azure omkostninger, indtil du slette den. Du finder flere oplysninger i [SQL Server Stræk Database priser](https://azure.microsoft.com/pricing/details/sql-server-stretch-database/).

### <a name="use-transact-sql-to-disable-stretch-database-for-a-database"></a>Bruge Transact\-SQL for at deaktivere Stræk Database til en database
Kør følgende kommando.

```tsql
ALTER DATABASE <database name>
    SET REMOTE_DATA_ARCHIVE = OFF ;
```

>   [AZURE.NOTE] Deaktivere Stræk Database til en database kan ikke slette den eksterne database. Hvis du vil slette fjerndatabasen, har du slippe den ved hjælp af administrationsportalen Azure. Fjerndatabasen fortsætter med at betale Azure omkostninger, indtil du slette den. Du finder flere oplysninger i [SQL Server Stræk Database priser](https://azure.microsoft.com/pricing/details/sql-server-stretch-database/).

## <a name="see-also"></a>Se også

[ALTER sæt databaseindstillinger (Transact-SQL)](https://msdn.microsoft.com/library/bb522682.aspx)

[Afbryde og genoptage Stræk Database](sql-server-stretch-database-pause.md)
