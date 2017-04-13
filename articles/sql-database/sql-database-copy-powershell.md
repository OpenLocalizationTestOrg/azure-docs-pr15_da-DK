<properties 
    pageTitle="Kopiere en Azure SQL-database ved hjælp af PowerShell | Microsoft Azure" 
    description="Oprette kopi af en Azure SQL-database ved hjælp af PowerShell" 
    services="sql-database"
    documentationCenter=""
    authors="stevestein"
    manager="jhubbard"
    editor=""/>

<tags
    ms.service="sql-database"
    ms.devlang="NA"
    ms.date="09/08/2016"
    ms.author="sstein"
    ms.workload="data-management"
    ms.topic="article"
    ms.tgt_pltfrm="NA"/>


# <a name="copy-an-azure-sql-database-using-powershell"></a>Kopiere en Azure SQL-database ved hjælp af PowerShell


> [AZURE.SELECTOR]
- [Oversigt](sql-database-copy.md)
- [Azure-portalen](sql-database-copy-portal.md)
- [PowerShell](sql-database-copy-powershell.md)
- [T-SQL](sql-database-copy-transact-sql.md)

Denne artikel beskrives, hvordan du kopierer en SQL-database med PowerShell til den samme server til en anden server eller kopiere en database til en [elastiske database puljen](sql-database-elastic-pool.md). Kopieringen af databasen bruger Cmdletten [Ny AzureRmSqlDatabaseCopy](https://msdn.microsoft.com/library/mt603644.aspx) . 


For at fuldføre i denne artikel skal bruge du følgende:

- Azure SQL-database (en database til at kopiere). Hvis du ikke har en SQL-database, kan du oprette én følge trinnene i denne artikel: [oprette din første Azure SQL-Database](sql-database-get-started.md).
- Den seneste version af Azure PowerShell. Du kan finde detaljerede oplysninger, se, [hvordan du installerer og konfigurerer Azure PowerShell](../powershell-install-configure.md).


Mange nye funktioner i SQL-Database understøttes kun, når du bruger [Azure ressourcestyring implementeringsmodel](../azure-resource-manager/resource-group-overview.md), så Ark6 [Azure SQL Database PowerShell-cmdlet'er](https://msdn.microsoft.com/library/azure/mt574084.aspx) til Ressourcestyring. Den eksisterende klassisk implementeringsmodel [Azure SQL-Database (klassisk) cmdletter](https://msdn.microsoft.com/library/azure/dn546723.aspx) understøttes af hensyn til bagudkompatibiliteten, men vi anbefaler, at du bruger ressourcestyring cmdletter.


>[AZURE.NOTE] Afhængigt af størrelsen på databasen tage kopieringen lidt tid at fuldføre.


## <a name="copy-a-sql-database-to-the-same-server"></a>Kopiere en SQL-database til den samme server

Hvis du vil oprette kopien på den samme server, udelader de `-CopyServerName` parameter (eller angive den til den samme server).

    New-AzureRmSqlDatabaseCopy -ResourceGroupName "resourcegroup1" -ServerName "server1" -DatabaseName "database1" -CopyDatabaseName "database1_copy"

## <a name="copy-a-sql-database-to-a-different-server"></a>Kopiere en SQL-database til en anden server

Hvis du vil oprette kopien på en anden server, skal du medtage den `-CopyServerName` parameter og angive den til en anden server. *Kopiér* serveren skal allerede findes. Hvis det er i en anden ressourcegruppe og derefter skal du også medtage den `-CopyResourceGroupName` parameter.

    New-AzureRmSqlDatabaseCopy -ResourceGroupName "resourcegroup1" -ServerName "server1" -DatabaseName "database1" -CopyServerName "server2" -CopyDatabaseName "database1_copy"


## <a name="copy-a-sql-database-into-an-elastic-database-pool"></a>Kopiere en SQL-database til en elastiske database puljen

Hvis du vil oprette en kopi af en SQL-database i en gruppe, skal du angive den `-ElasticPoolName` -parameter til en eksisterende gruppe.

    New-AzureRmSqlDatabaseCopy -ResourceGroupName "resourcegoup1" -ServerName "server1" -DatabaseName "database1" -CopyResourceGroupName "poolResourceGroup" -CopyServerName "poolServer1" -CopyDatabaseName "database1_copy" -ElasticPoolName "poolName"


## <a name="resolve-logins"></a>Løse logon

For at løse logon, når kopieringen er fuldført, skal du se [løse logon](sql-database-copy-transact-sql.md#resolve-logins-after-the-copy-operation-completes)


## <a name="example-powershell-script"></a>Eksempel PowerShell-script

Følgende script antages det, at alle ressourcegrupper,-servere og puljen allerede findes (erstatte de variable værdier med dine eksisterende ressourcer). Alt, hvad der skal være, med undtagelse af kopien af databasen.

    # Sign in to Azure and set the subscription to work with
    # ------------------------------------------------------
    $SubscriptionId = "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
    Add-AzureRmAccount
    Set-AzureRmContext -SubscriptionId $SubscriptionId
    
    
    # SQL database source (the existing database to copy)
    # ---------------------------------------------------
    $sourceDbName = "db1"
    $sourceDbServerName = "server1"
    $sourceDbResourceGroupName = "rg1"
    
    # SQL database copy (the new db to be created)
    # --------------------------------------------
    $copyDbName = "db1_copy"
    $copyDbServerName = "server2"
    $copyDbResourceGroupName = "rg2"
    
    # Copy a database to the same server
    # ----------------------------------
    New-AzureRmSqlDatabaseCopy -ResourceGroupName $sourceDbResourceGroupName -ServerName $sourceDbServerName -DatabaseName $sourceDbName -CopyDatabaseName $copyDbName
    
    # Copy a database to a different server
    # -------------------------------------
    New-AzureRmSqlDatabaseCopy -ResourceGroupName $sourceDbResourceGroupName -ServerName $sourceDbServerName -DatabaseName $sourceDbName -CopyResourceGroupName $copyDbResourceGroupName -CopyServerName $copyDbServerName -CopyDatabaseName $copyDbName
    
    # Copy a database into an elastic database pool
    # ---------------------------------------------
    $poolName = "pool1"
    
    New-AzureRmSqlDatabaseCopy -ResourceGroupName $sourceDbResourceGroupName -ServerName $sourceDbServerName -DatabaseName $sourceDbName -CopyResourceGroupName $copyDbResourceGroupName -CopyServerName $copyDbServerName -ElasticPoolName $poolName -CopyDatabaseName $copyDbName



    

## <a name="next-steps"></a>Næste trin

- Se [kopiere en Azure SQL-database](sql-database-copy.md) til en oversigt over kopierer en Azure SQL-Database.
- Se [kopiere Azure SQL-database ved hjælp af portalen Azure](sql-database-copy-portal.md) til at kopiere en database ved hjælp af portalen Azure.
- Se [kopi Azure SQL-database ved hjælp af T-SQL](sql-database-copy-transact-sql.md) til at kopiere en database ved hjælp af Transact-SQL.
- Se, [hvordan du administrerer Azure SQL Databasesikkerhed efter nedbrud](sql-database-geo-replication-security-config.md) til at få mere at vide om administration af brugere og -logon, når du kopierer en database til en anden logiske server.


## <a name="additional-resources"></a>Yderligere ressourcer

- [Ny AzureRmSqlDatabase](https://msdn.microsoft.com/library/mt603644.aspx)
- [Get-AzureRmSqlDatabaseActivity](https://msdn.microsoft.com/library/mt603687.aspx)
- [Administrere logon](sql-database-manage-logins.md)
- [Oprette forbindelse til SQL-Database med SQL Server Management Studio og udføre en stikprøve T-SQL-forespørgsel](sql-database-connect-query-ssms.md)
- [Eksportere databasen til en BACPAC](sql-database-export.md)
- [Oversigt over Business løbende](sql-database-business-continuity.md)
- [SQL-Database dokumentation](https://azure.microsoft.com/documentation/services/sql-database/)
- [Azure SQL Database PowerShell-Cmdlet Reference](https://msdn.microsoft.com/library/mt574084.aspx)
