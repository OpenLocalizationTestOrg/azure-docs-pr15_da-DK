<properties
    pageTitle="Gendanne en Azure SQL-database til et tidligere tidspunkt (Azure portal) | Microsoft Azure"
    description="Gendanne en Azure SQL-database til et tidligere tidspunkt."
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


# <a name="restore-an-azure-sql-database-to-a-previous-point-in-time-with-the-azure-portal"></a>Gendanne en Azure SQL-database til et tidligere tidspunkt ved hjælp af Azure portal


> [AZURE.SELECTOR]
- [Oversigt](sql-database-recovery-using-backups.md)
- [Punkt-In-Time Gendan: PowerShell](sql-database-point-in-time-restore-powershell.md)

I denne artikel beskrives, hvordan til at gendanne databasen til et tidligere tidspunkt fra [SQL-Database automatisk sikkerhedskopiering](sql-database-automated-backups.md) ved hjælp af portalen Azure.

## <a name="restore-a-sql-database-to-a-previous-point-in-time"></a>Gendanne en SQL-database til et tidligere tidspunkt

Vælg en database til at gendanne Azure portalen:

1.  Åbn [Azure-portalen](https://portal.azure.com).
2.  I venstre side af skærmen, vælge **flere tjenester** > **SQL-databaser**.
3.  Klik på den database, du vil gendanne.
4.  Vælg **Gendan**øverst på siden din database:

    ![Gendanne en Azure SQL-database](./media/sql-database-point-in-time-restore-portal/restore.png)

5.  Vælg dato og klokkeslæt (i UTC tid) til at gendanne databasen til, på siden **gendanne** , og klik derefter på **OK**:

    ![Gendanne en Azure SQL-database](./media/sql-database-point-in-time-restore-portal/restore-details.png)

## <a name="monitor-the-restore-operation"></a>Overvåge gendannelsen

1. Når du klikker på **OK** i det forrige trin, skal du klikke på ikonet øverst til højre på siden, og klik på meddelelsen **gendannelse af SQL-database** for at få oplysninger.

    ![Gendanne en Azure SQL-database](./media/sql-database-point-in-time-restore-portal/notification-icon.png)

2. Siden gendannelse af SQL-database åbnes med oplysninger om status for gendannelsen. Du kan klikke på linjeelementer få mere at vide:

    ![Gendanne en Azure SQL-database](./media/sql-database-point-in-time-restore-portal/inprogress.png)

 

## <a name="next-steps"></a>Næste trin

- Se [Oversigt over Business løbende](sql-database-business-continuity.md) til en business løbende oversigt og scenarier
- For at få mere for at vide om Azure SQL-Database, der automatisk sikkerhedskopiering, se [SQL-Database automatisk sikkerhedskopiering](sql-database-automated-backups.md)
- For at få mere for at vide om brug af automatiseret sikkerhedskopier til gendannelse, se [gendanne en database fra de service-definerede sikkerhedskopier](sql-database-recovery-using-backups.md)
- For at få mere for at vide om indstillinger for hurtigere genoprettelse, se [Aktiv geografisk replikering](sql-database-geo-replication-overview.md)  
- Hvis du vil lære at bruge automatiske sikkerhedskopier til at arkivere, se [databasekopi](sql-database-copy.md)
