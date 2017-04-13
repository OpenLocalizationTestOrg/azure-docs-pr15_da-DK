<properties
   pageTitle="Gendanne en SQL Azure datawarehouse (PowerShell) | Microsoft Azure"
   description="PowerShell opgaver for at gendanne en Azure SQL Data Warehouse."
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="Lakshmi1812"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="09/21/2016"
   ms.author="lakshmir;barbkess;sonyama"/>

# <a name="restore-an-azure-sql-data-warehouse-powershell"></a>Gendanne en SQL Azure datawarehouse (PowerShell)

> [AZURE.SELECTOR]
- [Oversigt][]
- [Portal][]
- [PowerShell][]
- [RESTEN][]

I denne artikel lærer du, hvordan du gendanner en Azure SQL Data Warehouse ved hjælp af PowerShell.

## <a name="before-you-begin"></a>Inden du går i gang

**Bekræft din DTU kapacitet.** Hver SQL Data Warehouse er placeret på en SQL server (fx myserver.database.windows.net), som har en standard DTU kvote.  Før du kan gendanne et SQL Data Warehouse, skal du kontrollere, at den SQL server har tilstrækkelig resterende DTU kvote for den database, ved at blive gendannet. For at lære til at beregne DTU behov eller anmode om flere DTU, skal du se [anmode om en ændring af DTU kvote][].

### <a name="install-powershell"></a>Installere PowerShell

For at kunne bruge Azure PowerShell med SQL Data Warehouse, skal du installere Azure PowerShell version 1.0 eller nyere.  Du kan kontrollere din version ved at køre **Get-modul - ListAvailable-navn AzureRM**.  Den nyeste version kan installeres fra [Microsoft Web Platform Installer][].  Se, [hvordan du installerer og konfigurerer Azure PowerShell][]kan finde flere oplysninger om at installere den nyeste version.

## <a name="restore-an-active-or-paused-database"></a>Gendanne en aktiv eller er stoppet midlertidigt database

Hvis du vil gendanne en database fra et øjebliksbillede, skal du bruge [Gendan AzureRmSqlDatabase][] PowerShell-cmdlet.

1. Åbn Windows PowerShell.
2. Oprette forbindelse til kontoen Azure og få vist alle de abonnementer, der er knyttet til din konto.
3. Vælg det abonnement, der indeholder databasen, der skal gendannes.
4. Liste over gendannelsespunkter, for databasen.
5. Vælg det ønskede gendannelsespunkt ved hjælp af RestorePointCreationDate.
6. Gendanne databasen til det ønskede gendannelsespunkt.
7. Kontrollér, at den gendannede database er online.

```Powershell

$SubscriptionName="<YourSubscriptionName>"
$ResourceGroupName="<YourResourceGroupName>"
$ServerName="<YourServerNameWithoutURLSuffixSeeNote>"  # Without database.windows.net
$DatabaseName="<YourDatabaseName>"
$NewDatabaseName="<YourDatabaseName>"

Login-AzureRmAccount
Get-AzureRmSubscription
Select-AzureRmSubscription -SubscriptionName $SubscriptionName

# List the last 10 database restore points
((Get-AzureRMSqlDatabaseRestorePoints -ResourceGroupName $ResourceGroupName -ServerName $ServerName -DatabaseName ($DatabaseName).RestorePointCreationDate)[-10 .. -1]

# Or list all restore points
Get-AzureRmSqlDatabaseRestorePoints -ResourceGroupName $ResourceGroupName -ServerName $ServerName -DatabaseName $DatabaseName

# Get the specific database to restore
$Database = Get-AzureRmSqlDatabase -ResourceGroupName $ResourceGroupName -ServerName $ServerName -DatabaseName $DatabaseName

# Pick desired restore point using RestorePointCreationDate
$PointInTime="<RestorePointCreationDate>"  

# Restore database from a restore point
$RestoredDatabase = Restore-AzureRmSqlDatabase –FromPointInTimeBackup –PointInTime $PointInTime -ResourceGroupName $Database.ResourceGroupName -ServerName $Database.$ServerName -TargetDatabaseName $NewDatabaseName –ResourceId $Database.ResourceID

# Verify the status of restored database
$RestoredDatabase.status

```

>[AZURE.NOTE] Når gendannelsen er fuldført, kan du konfigurere databasen gendannet ved følgende [konfigurere databasen efter genoprettelsen][].


## <a name="restore-a-deleted-database"></a>Gendanne en slettet database

Brug cmdlet'en [Gendan AzureRmSqlDatabase][] for at gendanne en slettet database.

1. Åbn Windows PowerShell.
2. Oprette forbindelse til kontoen Azure og få vist alle de abonnementer, der er knyttet til din konto.
3. Vælg det abonnement, der indeholder den slettede database, der skal gendannes.
4. Få den specifikke slettede database.
5. Gendanne slettede databasen.
6. Kontrollér, at den gendannede database er online.

```Powershell
$SubscriptionName="<YourSubscriptionName>"
$ResourceGroupName="<YourResourceGroupName>"
$ServerName="<YourServerNameWithoutURLSuffixSeeNote>"  # Without database.windows.net
$DatabaseName="<YourDatabaseName>"
$NewDatabaseName="<YourDatabaseName>"

Login-AzureRmAccount
Get-AzureRmSubscription
Select-AzureRmSubscription -SubscriptionName $SubscriptionName

# Get the deleted database to restore
$DeletedDatabase = Get-AzureRmSqlDeletedDatabaseBackup -ResourceGroupName $ResourceGroupNam -ServerName $ServerName -DatabaseName $DatabaseName

# Restore deleted database
$RestoredDatabase = Restore-AzureRmSqlDatabase –FromDeletedDatabaseBackup –DeletionDate $DeletedDatabase.DeletionDate -ResourceGroupName $DeletedDatabase.ResourceGroupName -ServerName $DeletedDatabase.ServerName -TargetDatabaseName $NewDatabaseName –ResourceId $DeletedDatabase.ResourceID

# Verify the status of restored database
$RestoredDatabase.status
```

>[AZURE.NOTE] Når gendannelsen er fuldført, kan du konfigurere databasen gendannet ved følgende [konfigurere databasen efter genoprettelsen][].


## <a name="restore-from-an-azure-geographical-region"></a>Gendanne fra en Azure geografisk område

Brug cmdlet'en [Gendan AzureRmSqlDatabase][] for at gendanne en database.

1. Åbn Windows PowerShell.
2. Oprette forbindelse til kontoen Azure og få vist alle de abonnementer, der er knyttet til din konto.
3. Vælg det abonnement, der indeholder databasen, der skal gendannes.
4. Få den database, du vil gendanne.
5. Oprette gendannelse anmodning om databasen.
6. Kontrollér status for databasen geografisk gendannet.

```Powershell
Login-AzureRmAccount
Get-AzureRmSubscription
Select-AzureRmSubscription -SubscriptionName "<Subscription_name>"

# Get the database you want to recover
$GeoBackup = Get-AzureRmSqlDatabaseGeoBackup -ResourceGroupName "<YourResourceGroupName>" -ServerName "<YourServerName>" -DatabaseName "<YourDatabaseName>"

# Recover database
$GeoRestoredDatabase = Restore-AzureRmSqlDatabase –FromGeoBackup -ResourceGroupName "<YourResourceGroupName>" -ServerName "<YourTargetServer>" -TargetDatabaseName "<NewDatabaseName>" –ResourceId $GeoBackup.ResourceID

# Verify that the geo-restored database is online
$GeoRestoredDatabase.status
```

>[AZURE.NOTE] For at konfigurere din database, når gendannelsen er fuldført, skal du se [konfigurere databasen efter genoprettelse][]. 


Gendannet databasen bliver TDE aktiverede, hvis kildedatabasen TDE er aktiveret.


## <a name="next-steps"></a>Næste trin
For at få mere for at vide om business løbende funktionerne i Azure SQL Database-versioner, skal du læse [Azure SQL-Database business løbende oversigt][].

<!--Image references-->

<!--Article references-->
[Azure SQL-Database business løbende oversigt]: sql-database-business-continuity.md
[Anmode om en ændring af DTU kvote]: ./sql-data-warehouse-get-started-create-support-ticket.md#request-quota-change
[Konfigurere din database efter genoprettelse]: ./sql-database-disaster-recovery.md#configure-your-database-after-recovery
[Hvordan du installerer og konfigurerer Azure PowerShell]: powershell-install-configure.md
[Oversigt]: ./sql-data-warehouse-restore-database-overview.md
[Portal]: ./sql-data-warehouse-restore-database-portal.md
[PowerShell]: ./sql-data-warehouse-restore-database-powershell.md
[RESTEN]: ./sql-data-warehouse-restore-database-rest-api.md
[Konfigurere din database efter genoprettelse]: ./sql-database-disaster-recovery.md#configure-your-database-after-recovery

<!--MSDN references-->
[Gendanne AzureRmSqlDatabase]: https://msdn.microsoft.com/library/mt693390.aspx

<!--Other Web references-->
[Azure Portal]: https://portal.azure.com/
[Microsoft Web Platform Installer]: https://aka.ms/webpi-azps
