<properties
    pageTitle="Gendanne en Azure SQL-Database til et tidligere tidspunkt (PowerShell) | Microsoft Azure"
    description="Gendanne en Azure SQL-Database til et tidligere tidspunkt"
    services="sql-database"
    documentationCenter=""
    authors="stevestein"
    manager="jhubbard"
    editor=""/>

<tags
    ms.service="sql-database"
    ms.devlang="NA"
    ms.topic="article"
    ms.tgt_pltfrm="powershell"
    ms.workload="NA"
    ms.date="07/17/2016"
    ms.author="sstein"/>

# <a name="restore-an-azure-sql-database-to-a-previous-point-in-time-with-powershell"></a>Gendanne en Azure SQL-Database til et tidligere tidspunkt med PowerShell

> [AZURE.SELECTOR]
- [Oversigt](sql-database-recovery-using-backups.md)
- [Punkt-In-Time gendannelse: Azure portal](sql-database-point-in-time-restore-portal.md)

I denne artikel beskrives, hvordan til at gendanne databasen til et tidligere tidspunkt fra [SQL-Database automatisk sikkerhedskopiering](sql-database-automated-backups.md). Du kan gøre dette ved hjælp af PowerShell.

[AZURE.INCLUDE [Start your PowerShell session](../../includes/sql-database-powershell.md)]

## <a name="restore-your-database-to-a-point-in-time-as-a-standalone-database"></a>Gendanne databasen til et punkt i tid som en enkeltstående database

1. Få den database, du vil gendanne ved hjælp af [Get-AzureRmSqlDatabase] (https://msdn.microsoft.com/library/azure/mt603648(v=azure.300\).aspx) cmdlet.

        $Database = Get-AzureRmSqlDatabase -ResourceGroupName "resourcegroup01" -ServerName "server01" -DatabaseName "database01"

2. Gendanne databasen til et punkt i gang ved at bruge [Gendan-AzureRmSqlDatabase] (https://msdn.microsoft.com/library/azure/mt693390(v=azure.300\).aspx) cmdlet.

        Restore-AzureRmSqlDatabase –FromPointInTimeBackup –PointInTime UTCDateTime -ResourceGroupName $Database.ResourceGroupName -ServerName $Database.ServerName -TargetDatabaseName "RestoredDatabase" –ResourceId $Database.ResourceID -Edition "Standard" -ServiceObjectiveName "S2"


## <a name="restore-your-database-to-a-point-in-time-into-an-elastic-database-pool"></a>Gendanne databasen til et tidspunkt i en elastiske database puljen

1. Få den database, du vil gendanne ved hjælp af [Get-AzureRmSqlDatabase] (https://msdn.microsoft.com/library/azure/mt603648(v=azure.300\).aspx) cmdlet.

        $Database = Get-AzureRmSqlDatabase -ResourceGroupName "resourcegroup01" -ServerName "server01" -DatabaseName "database01"

2. Gendanne databasen til et punkt i gang ved at bruge [Gendan-AzureRmSqlDatabase] (https://msdn.microsoft.com/library/azure/mt693390(v=azure.300\).aspx) cmdlet.

        Restore-AzureRmSqlDatabase –FromPointInTimeBackup –PointInTime UTCDateTime -ResourceGroupName $Database.ResourceGroupName -ServerName $Database.ServerName -TargetDatabaseName "RestoredDatabase" –ResourceId $Database.ResourceID –ElasticPoolName "elasticpool01"


## <a name="next-steps"></a>Næste trin

- Se [Oversigt over Business løbende](sql-database-business-continuity.md) til en business løbende oversigt og scenarier
- For at få mere for at vide om Azure SQL-Database, der automatisk sikkerhedskopiering, se [SQL-Database automatisk sikkerhedskopiering](sql-database-automated-backups.md)
- For at få mere for at vide om brug af automatiseret sikkerhedskopier til gendannelse, se [gendanne en database fra de service-definerede sikkerhedskopier](sql-database-recovery-using-backups.md)
- For at få mere for at vide om indstillinger for hurtigere genoprettelse, se [Aktiv geografisk replikering](sql-database-geo-replication-overview.md)  
- Hvis du vil lære at bruge automatiske sikkerhedskopier til at arkivere, se [databasekopi](sql-database-copy.md)
