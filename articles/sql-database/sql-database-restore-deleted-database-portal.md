<properties
    pageTitle="Gendanne en slettet Azure SQL-database (Azure portal) | Microsoft Azure"
    description="Gendanne en slettet Azure SQL-database (Azure portal)."
    services="sql-database"
    documentationCenter=""
    authors="stevestein"
    manager="jhubbard"
    editor=""/>

<tags
    ms.service="sql-database"
    ms.devlang="NA"
    ms.date="10/12/2016"
    ms.author="sstein"
    ms.workload="NA"
    ms.topic="article"
    ms.tgt_pltfrm="NA"/>


# <a name="restore-a-deleted-azure-sql-database-using-the-azure-portal"></a>Gendanne en slettet Azure SQL-database ved hjælp af portalen Azure

> [AZURE.SELECTOR]
- [Oversigt](sql-database-recovery-using-backups.md)
- [**Gendanne slettede DB: Portal**](sql-database-restore-deleted-database-portal.md)
- [Gendanne slettede DB: PowerShell](sql-database-restore-deleted-database-powershell.md)

## <a name="select-the-database-to-restore"></a>Vælg databasen, du kan gendanne 

Gendanne en slettet database i portalen Azure:

1.  Klik på **flere tjenester**i [Azure-portalen](https://portal.azure.com), > **SQL-servere**.
3.  Vælg den server, der indeholdt den database, du vil gendanne.
4.  Rul ned til sektionen **Handlinger** på din serverblade, og vælg **slettede databaser**: ![gendanne en Azure SQL-database](./media/sql-database-restore-deleted-database-portal/restore-deleted-trashbin.png)
5.  Vælg den database, du vil gendanne.
6.  Angiv et navn på databasen, og klik på **OK**:

    ![Gendanne en Azure SQL-database](./media/sql-database-restore-deleted-database-portal/restore-deleted.png)


## <a name="next-steps"></a>Næste trin

- Se [Oversigt over Business løbende](sql-database-business-continuity.md) til en business løbende oversigt og scenarier
- For at få mere for at vide om Azure SQL-Database, der automatisk sikkerhedskopiering, se [SQL-Database automatisk sikkerhedskopiering](sql-database-automated-backups.md)
- For at få mere for at vide om brug af automatiseret sikkerhedskopier til gendannelse, se [gendanne en database fra de service-definerede sikkerhedskopier](sql-database-recovery-using-backups.md)
- For at få mere for at vide om indstillinger for hurtigere genoprettelse, se [Aktiv geografisk replikering](sql-database-geo-replication-overview.md)  
- Hvis du vil lære at bruge automatiske sikkerhedskopier til at arkivere, se [databasekopi](sql-database-copy.md)
