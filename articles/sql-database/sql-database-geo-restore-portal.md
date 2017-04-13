<properties
    pageTitle="Gendanne en Azure SQL-database fra en automatisk sikkerhedskopiering (Azure portal) | Microsoft Azure"
    description="Gendanne en Azure SQL-database fra en automatisk sikkerhedskopiering (Azure portal)."
    services="sql-database"
    documentationCenter=""
    authors="stevestein"
    manager="jhubbard"
    editor=""/>

<tags
    ms.service="sql-database"
    ms.devlang="NA"
    ms.date="10/18/2016"
    ms.author="sstein"
    ms.workload="NA"
    ms.topic="article"
    ms.tgt_pltfrm="NA"/>


# <a name="restore-an-azure-sql-database-from-an-automatic-backup-using-the-azure-portal"></a>Gendanne en Azure SQL-database fra en automatisk sikkerhedskopiering ved hjælp af portalen Azure


> [AZURE.SELECTOR]
- [Oversigt](sql-database-recovery-using-backups.md#geo-restore)
- [Geografisk-Gendan: PowerShell](sql-database-geo-restore-powershell.md)

I denne artikel beskrives, hvordan til at gendanne databasen fra en [automatisk sikkerhedskopiering](sql-database-automated-backups.md) i en ny server med [Geografisk-Gendan](sql-database-recovery-using-backups/.md#geo-restore) ved hjælp af portalen Azure.

## <a name="select-a-database-to-restore"></a>Vælg en database til at gendanne

Hvis du vil gendanne en database i portalen Azure, skal du udføre følgende trin:

1.  Gå til [Azure-portalen](https://portal.azure.com).
2.  I venstre side af skærmen vælge **+ Ny** > **databaser** > **SQL-Database**:

    ![Gendanne en Azure SQL-database](./media/sql-database-geo-restore-portal/new-sql-database.png)

3.  Vælg **Sikkerhedskopiering** som kilde, og vælg derefter den sikkerhedskopi, du vil gendanne. Angiv et navn på databasen, en server, du vil gendanne databasen til, og klik derefter på **Opret**:
  
    ![Gendanne en Azure SQL-database](./media/sql-database-geo-restore-portal/geo-restore.png)

Overvåge statussen for gendannelsen ved at klikke på ikonet i øverste højre hjørne af siden. 


## <a name="next-steps"></a>Næste trin

- Se [Oversigt over Business løbende](sql-database-business-continuity.md) til en business løbende oversigt og scenarier
- For at få mere for at vide om Azure SQL-Database, der automatisk sikkerhedskopiering, se [SQL-Database automatisk sikkerhedskopiering](sql-database-automated-backups.md)
- For at få mere for at vide om brug af automatiseret sikkerhedskopier til gendannelse, se [gendanne en database fra de service-definerede sikkerhedskopier](sql-database-recovery-using-backups.md)
- For at få mere for at vide om indstillinger for hurtigere genoprettelse, se [Aktiv geografisk replikering](sql-database-geo-replication-overview.md)  
- Hvis du vil lære at bruge automatiske sikkerhedskopier til at arkivere, se [databasekopi](sql-database-copy.md)
