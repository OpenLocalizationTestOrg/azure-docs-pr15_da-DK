<properties
    pageTitle="Administrere Azure SQL-Database med PowerShell | Microsoft Azure"
    description="Azure SQL-Database management med PowerShell."
    services="sql-database"
    documentationCenter=""
    authors="stevestein"
    manager="jhubbard"
    editor="monicar"/>

<tags
    ms.service="sql-database"
    ms.workload="data-management"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/13/2016"
    ms.author="sstein"/>

# <a name="manage-azure-sql-database-with-powershell"></a>Administrere Azure SQL-Database med PowerShell


> [AZURE.SELECTOR]
- [Azure-portalen](sql-database-manage-portal.md)
- [Transact-SQL (SSMS)](sql-database-manage-azure-ssms.md)
- [PowerShell](sql-database-manage-powershell.md)

Dette emne viser de PowerShell-cmdletter, der bruges til at udføre mange opgaver i Azure SQL-Database. Du kan finde en komplet liste [Azure SQL Database-cmdletter](https://msdn.microsoft.com/library/mt574084.aspx).


## <a name="create-a-resource-group"></a>Oprette en ressourcegruppe

Oprette en ressourcegruppe til vores SQL-Database og relaterede Azure ressourcer med [Ny AzureRmResourceGroup](https://msdn.microsoft.com/library/azure/mt759837.aspx) cmdlet.

```
$resourceGroupName = "resourcegroup1"
$resourceGroupLocation = "northcentralus"
New-AzureRmResourceGroup -Name $resourceGroupName -Location $resourceGroupLocation
```

Se [Bruge Azure PowerShell med Azure ressourcestyring](../powershell-azure-resource-manager.md)kan finde flere oplysninger.
Et eksempel på script, under [oprette en SQL-database PowerShell-script](sql-database-get-started-powershell.md#create-a-sql-database-powershell-script).

## <a name="create-a-sql-database-server"></a>Oprette en SQL-databaseserver

Oprette en SQL-databaseserver med [Ny AzureRmSqlServer](https://msdn.microsoft.com/library/azure/mt603715.aspx) cmdlet. Erstat *server1* med navnet på din server. Servernavne skal være entydig på tværs af alle servere, der Azure SQL-Database. Hvis servernavnet er allerede taget, får du en fejl. Denne kommando kan tage flere minutter at gennemføre. Ressourcegruppen skal allerede findes i dit abonnement.

```
$resourceGroupName = "resourcegroup1"

$sqlServerName = "server1"
$sqlServerVersion = "12.0"
$sqlServerLocation = "northcentralus"
$serverAdmin = "loginname"
$serverPassword = "password" 
$securePassword = ConvertTo-SecureString –String $serverPassword –AsPlainText -Force
$creds = New-Object –TypeName System.Management.Automation.PSCredential –ArgumentList $serverAdmin, $securePassword
    

$sqlServer = New-AzureRmSqlServer -ServerName $sqlServerName `
 -SqlAdministratorCredentials $creds -Location $sqlServerLocation `
 -ResourceGroupName $resourceGroupName -ServerVersion $sqlServerVersion
```

Du kan finde flere oplysninger, kan du se [Hvad er SQL-Database](sql-database-technical-overview.md). Et eksempel på script, under [oprette en SQL-database PowerShell-script](sql-database-get-started-powershell.md#create-a-sql-database-powershell-script).


## <a name="create-a-sql-database-server-firewall-rule"></a>Oprette en SQL-Database server firewallregel

Oprette en firewallregel for at få adgang til serveren med [Ny AzureRmSqlServerFirewallRule](https://msdn.microsoft.com/library/azure/mt603860.aspx) cmdlet. Kør følgende kommando, erstatter start- og IP-adresserne med gyldige værdier til kunden. Ressourcegruppe og server, skal allerede findes i dit abonnement.

```
$resourceGroupName = "resourcegroup1"
$sqlServerName = "server1"

$firewallRuleName = "firewallrule1"
$firewallStartIp = "0.0.0.0"
$firewallEndIp = "255.255.255.255"

New-AzureRmSqlServerFirewallRule -ResourceGroupName $resourceGroupName `
 -ServerName $sqlServerName -FirewallRuleName $firewallRuleName `
 -StartIpAddress $firewallStartIp -EndIpAddress $firewallEndIp
```

Oprette en regel for at give andre Azure services adgang til din server, og både den `-StartIpAddress` og `-EndIpAddress` til **0.0.0.0**. Denne firewallregel speciel tillader alle Azure trafik til at få adgang til serveren.

Du kan finde yderligere oplysninger finder [Azure SQL Database-firewallen](https://msdn.microsoft.com/library/azure/ee621782.aspx). Et eksempel på script, under [oprette en SQL-database PowerShell-script](sql-database-get-started-powershell.md#create-a-sql-database-powershell-script).


## <a name="create-a-sql-database-blank"></a>Oprette en SQL-database (tomt)

Oprette en database med [Ny AzureRmSqlDatabase](https://msdn.microsoft.com/library/azure/mt619339.aspx) cmdlet. Ressourcegruppe og server, skal allerede findes i dit abonnement. 

```
$resourceGroupName = "resourcegroup1"
$sqlServerName = "server1"

$databaseName = "database1"
$databaseEdition = "Standard"
$databaseServiceLevel = "S0"

$currentDatabase = New-AzureRmSqlDatabase -ResourceGroupName $resourceGroupName `
 -ServerName $sqlServerName -DatabaseName $databaseName `
 -Edition $databaseEdition -RequestedServiceObjectiveName $databaseServiceLevel
```

Du kan finde flere oplysninger, kan du se [Hvad er SQL-Database](sql-database-technical-overview.md). Et eksempel på script, under [oprette en SQL-database PowerShell-script](sql-database-get-started-powershell.md#create-a-sql-database-powershell-script).


## <a name="change-the-performance-level-of-a-sql-database"></a>Ændre computerens præstationsniveau en SQL-database

Tilpasse din database op eller ned med cmdlet'en [Set-AzureRmSqlDatabase](https://msdn.microsoft.com/library/azure/mt619433.aspx) til. Ressourcegruppe, server og database skal allerede findes i dit abonnement. Angiv den `-RequestedServiceObjectiveName` til et enkelt mellemrum (som i følgende kodestykke) til grundlæggende niveau. Angive den til *S0*, *S1*, *P1*, *P6*, osv., som det foregående eksempel til andre niveauer.

```
$resourceGroupName = "resourcegroup1"
$sqlServerName = "server1"

$databaseName = "database1"
$databaseEdition = "Basic"
$databaseServiceLevel = " "

Set-AzureRmSqlDatabase -ResourceGroupName $resourceGroupName `
 -ServerName $sqlServerName -DatabaseName $databaseName `
 -Edition $databaseEdition -RequestedServiceObjectiveName $databaseServiceLevel
```

Kan finde flere oplysninger under [Indstillinger for SQL-Database og ydeevne: forstå, hvad der er tilgængeligt i hver webtjenesten](sql-database-service-tiers.md). Du kan finde et eksempel på script, [eksempel PowerShell-script til at ændre service niveau og ydeevne niveauet for SQL-database](sql-database-scale-up-powershell.md#sample-powershell-script-to-change-the-service-tier-and-performance-level-of-your-sql-database).

## <a name="copy-a-sql-database-to-the-same-server"></a>Kopiere en SQL-database til den samme server

Kopiere en SQL-database til den samme server med [Ny AzureRmSqlDatabaseCopy](https://msdn.microsoft.com/library/azure/mt603644.aspx) cmdlet. Angiv den `-CopyServerName` og `-CopyResourceGroupName` med de samme værdier som kilde database server og ressource gruppen.

```
$resourceGroupName = "resourcegroup1"
$sqlServerName = "server1"
$databaseName = "database1"

$copyDatabaseName = "database1_copy"
$copyServerName = $sqlServerName
$copyResourceGroupName = $resourceGroupName

New-AzureRmSqlDatabaseCopy -DatabaseName $databaseName `
 -ServerName $sqlServerName -ResourceGroupName $resourceGroupName `
 -CopyDatabaseName $copyDatabaseName -CopyServerName $sqlServerName `
 -CopyResourceGroupName $copyResourceGroupName
```

Yderligere oplysninger finder du se [kopiere en Azure SQL-Database](sql-database-copy.md). Et eksempel på script, finder du [kopiere en SQL-database PowerShell-script](sql-database-copy-powershell.md#example-powershell-script).


## <a name="delete-a-sql-database"></a>Slette en SQL-database

Slette en SQL-database med Cmdletten [Fjern AzureRmSqlDatabase](https://msdn.microsoft.com/library/azure/mt619368.aspx) . Ressourcegruppe, server og database skal allerede findes i dit abonnement.

```
$resourceGroupName = "resourcegroup1"
$sqlServerName = "server1"
$databaseName = "database1"

Remove-AzureRmSqlDatabase -DatabaseName $databaseName `
 -ServerName $sqlServerName -ResourceGroupName $resourceGroupName
```

## <a name="delete-a-sql-database-server"></a>Slette en SQL-databaseserver

Slette en server med Cmdletten [Fjern AzureRmSqlServer](https://msdn.microsoft.com/library/azure/mt603488.aspx) .

```
$resourceGroupName = "resourcegroup1"
$sqlServerName = "server1"

Remove-AzureRmSqlServer -ServerName $sqlServerName -ResourceGroupName $resourceGroupName
```

## <a name="create-and-manage-elastic-database-pools-using-powershell"></a>Oprette og administrere elastiske database grupper ved hjælp af PowerShell

Se [oprette en ny gruppe elastiske database med PowerShell](sql-database-elastic-pool-create-powershell.md)få mere at vide om oprettelse af elastiske database grupper ved hjælp af PowerShell.

Få mere at vide om administration af elastiske database grupper ved hjælp af PowerShell se [skærm og administrere en elastiske database puljen med PowerShell](sql-database-elastic-pool-manage-powershell.md).



## <a name="related-information"></a>Relaterede oplysninger

- [Azure SQL Database-cmdletter](https://msdn.microsoft.com/library/azure/mt574084.aspx)
- [Reference til Azure Cmdlet](https://msdn.microsoft.com/library/azure/dn708514.aspx)
