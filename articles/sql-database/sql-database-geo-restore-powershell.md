<properties
    pageTitle="Gendanne en Azure SQL-Database fra en geografisk overflødige sikkerhedskopi (PowerShell) | Microsoft Azure"
    description="Gendanne en Azure SQL-Database til en ny server fra en geografisk overflødige sikkerhedskopi"
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

# <a name="restore-an-azure-sql-database-from-a-geo-redundant-backup-by-using-powershell"></a>Gendanne en Azure SQL-Database fra en geografisk overflødige sikkerhedskopi ved hjælp af PowerShell


> [AZURE.SELECTOR]
- [Oversigt](sql-database-recovery-using-backups.md)
- [Geografisk-Gendan: Azure Portal](sql-database-geo-restore-portal.md)

I denne artikel beskrives, hvordan til at gendanne databasen til en ny server ved hjælp af geografisk-Gendan. Dette kan gøres via PowerShell.

[AZURE.INCLUDE [Start your PowerShell session](../../includes/sql-database-powershell.md)]

## <a name="geo-restore-your-database-into-a-standalone-database"></a>Geografisk-Gendan din database til en enkeltstående database

1. Få geografisk overflødige sikkerhedskopien af databasen, som du vil gendanne ved hjælp af [Get-AzureRmSqlDatabaseGeoBackup] (https://msdn.microsoft.com/library/azure/mt693388(v=azure.300\).aspx) cmdlet.

        $GeoBackup = Get-AzureRmSqlDatabaseGeoBackup -ResourceGroupName "resourcegroup01" -ServerName "server01" -DatabaseName "database01"

2. Starte gendannelsen fra geografisk overflødige sikkerhedskopien ved hjælp af [Gendan-AzureRmSqlDatabase] (https://msdn.microsoft.com/library/azure/mt693390(v=azure.300\).aspx) cmdlet.

        Restore-AzureRmSqlDatabase –FromGeoBackup -ResourceGroupName "TargetResourceGroup" -ServerName "TargetServer" -TargetDatabaseName "RestoredDatabase" –ResourceId $GeoBackup.ResourceID -Edition "Standard" -RequestedServiceObjectiveName "S2"


## <a name="geo-restore-your-database-into-an-elastic-database-pool"></a>Geografisk-Gendan din database til en elastiske database puljen

1. Få geografisk overflødige sikkerhedskopien af databasen, som du vil gendanne ved hjælp af [Get-AzureRmSqlDatabaseGeoBackup] (https://msdn.microsoft.com/library/azure/mt693388(v=azure.300\).aspx) cmdlet.

        $GeoBackup = Get-AzureRmSqlDatabaseGeoBackup -ResourceGroupName "resourcegroup01" -ServerName "server01" -DatabaseName "database01"

2. Starte gendannelsen fra geografisk overflødige sikkerhedskopien ved hjælp af [Gendan-AzureRmSqlDatabase] (https://msdn.microsoft.com/library/azure/mt693390(v=azure.300\).aspx) cmdlet. Angiv det ønskede til at gendanne databasen til puljen navn.

        Restore-AzureRmSqlDatabase –FromGeoBackup -ResourceGroupName "TargetResourceGroup" -ServerName "TargetServer" -TargetDatabaseName "RestoredDatabase" –ResourceId $GeoBackup.ResourceID –ElasticPoolName "elasticpool01"  


## <a name="next-steps"></a>Næste trin

- Til en business løbende oversigt og scenarier skal du se [Oversigt over Business løbende](sql-database-business-continuity.md).
- For at få mere for at vide om Azure SQL-Database, der automatisk sikkerhedskopiering, se [SQL-Database automatisk sikkerhedskopiering](sql-database-automated-backups.md).
- For at få mere for at vide om brug af automatiseret sikkerhedskopier til gendannelse, kan du se [gendanne en database fra de service-definerede sikkerhedskopier](sql-database-recovery-using-backups.md).
- Se [Aktiv geografisk replikering](sql-database-geo-replication-overview.md)for at få mere for at vide om indstillinger for hurtigere genoprettelse.  
- Få vist [databasekopi](sql-database-copy.md)for at få mere for at vide om at bruge Automatiseret sikkerhedskopier til at arkivere.
