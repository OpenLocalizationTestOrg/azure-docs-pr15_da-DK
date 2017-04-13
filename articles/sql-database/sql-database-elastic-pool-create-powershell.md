<properties
    pageTitle="Oprette en ny gruppe elastiske database med PowerShell | Microsoft Azure"
    description="Lær at bruge PowerShell til at skala fra Azure SQL-Database ressourcer ved at oprette en SVG elastiske database pulje for at administrere flere databaser."
    services="sql-database"
    documentationCenter=""
    authors="srinia"
    manager="jhubbard"
    editor=""/>

<tags
    ms.service="sql-database"
    ms.devlang="NA"
    ms.topic="get-started-article"
    ms.tgt_pltfrm="powershell"
    ms.workload="data-management"
    ms.date="05/27/2016"
    ms.author="srinia"/>

# <a name="create-a-new-elastic-database-pool-with-powershell"></a>Oprette en ny gruppe elastiske database med PowerShell

> [AZURE.SELECTOR]
- [Azure-portalen](sql-database-elastic-pool-create-portal.md)
- [PowerShell](sql-database-elastic-pool-create-powershell.md)
- [C#](sql-database-elastic-pool-create-csharp.md)


Få mere at vide, hvordan du opretter en [elastiske database puljen](sql-database-elastic-pool.md) ved hjælp af PowerShell-cmdlet'er. 

Almindelige fejlkoder, se [SQL fejlkoder for SQL-Database-klientprogrammer: Webdatabase forbindelsesfejl og andre problemer](sql-database-develop-error-messages.md).

> [AZURE.NOTE] Elastiske grupper er alment tilgængelig (GA) i alle Azure områder undtagen nord Central os og Vest Indien, hvor det er i øjeblikket i Vis udskrift.  GA af elastiske grupper i disse områder vil blive leveret så tidligt som muligt. Desuden understøtter elastiske grupper i øjeblikket ikke databaser ved hjælp af [i hukommelsen OLTP eller i hukommelsen analyser](sql-database-in-memory.md).


Du skal køre Azure PowerShell 1.0 eller nyere. Du kan finde detaljerede oplysninger, se, [hvordan du installerer og konfigurerer Azure PowerShell](../powershell-install-configure.md).

## <a name="create-a-new-pool"></a>Oprette en ny gruppe

Cmdletten [Ny AzureRmSqlElasticPool](https://msdn.microsoft.com/library/azure/mt619378.aspx) opretter en ny gruppe. Værdierne for eDTU per puljen, min og max Dtus er begrænset af værdien fra service niveau (basic, standard eller premium). Se [eDTU og lager filstørrelsesgrænser for elastiske grupper og elastiske databaser](sql-database-elastic-pool.md#eDTU-and-storage-limits-for-elastic-pools-and-elastic-databases).

    New-AzureRmSqlElasticPool -ResourceGroupName "resourcegroup1" -ServerName "server1" -ElasticPoolName "elasticpool1" -Edition "Standard" -Dtu 400 -DatabaseDtuMin 10 -DatabaseDtuMax 100


## <a name="create-a-new-elastic-database-in-a-pool"></a>Oprette en ny elastiske database i en gruppe

Brug cmdlet'en [Ny AzureRmSqlDatabase](https://msdn.microsoft.com/library/azure/mt619339.aspx) og indstille parameteren **ElasticPoolName** til puljen mål. For at flytte en eksisterende database til en gruppe, skal du se [flytte en database til en elastiske puljen](sql-database-elastic-pool-manage-powershell.md#Move-a-database-into-an-elastic-pool).

    New-AzureRmSqlDatabase -ResourceGroupName "resourcegroup1" -ServerName "server1" -DatabaseName "database1" -ElasticPoolName "elasticpool1"

## <a name="create-a-pool-and-populate-it-with-multiple-new-databases"></a>Oprette en gruppe og udfylde den med flere nye databaser 

Oprettelse af et stort antal databaser i en gruppe kan tage tid, når færdig ved hjælp af portalen eller PowerShell-cmdletter, som opretter kun en enkelt database ad gangen. For at automatisere oprettelse af til en ny gruppe, skal du se [CreateOrUpdateElasticPoolAndPopulate ](https://gist.github.com/billgib/d80c7687b17355d3c2ec8042323819ae).   

## <a name="example-create-a-pool-using-powershell"></a>Eksempel: oprette en gruppe ved hjælp af PowerShell 

Dette script opretter en ny Azure ressourcegruppe og en ny server. Når du bliver bedt om det, kan du angive et administratornavn og din adgangskode til den nye server (ikke dine Azure legitimationsoplysninger).

    $subscriptionId = '<your Azure subscription id>'
    $resourceGroupName = '<resource group name>'
    $location = '<datacenter location>'
    $serverName = '<server name>'
    $poolName = '<pool name>'
    $databaseName = '<database name>'

    Login-AzureRmAccount
    Set-AzureRmContext -SubscriptionId $subscriptionId

    New-AzureRmResourceGroup -Name $resourceGroupName -Location $location
    New-AzureRmSqlServer -ResourceGroupName $resourceGroupName -ServerName $serverName -Location $location -ServerVersion "12.0"
    New-AzureRmSqlServerFirewallRule -ResourceGroupName $resourceGroupName -ServerName $serverName -FirewallRuleName "rule1" -StartIpAddress "192.168.0.198" -EndIpAddress "192.168.0.199"

    New-AzureRmSqlElasticPool -ResourceGroupName $resourceGroupName -ServerName $serverName -ElasticPoolName $poolName -Edition "Standard" -Dtu 400 -DatabaseDtuMin 10 -DatabaseDtuMax 100

    New-AzureRmSqlDatabase -ResourceGroupName $resourceGroupName -ServerName $serverName -DatabaseName $databaseName -ElasticPoolName $poolName -MaxSizeBytes 10GB



## <a name="next-steps"></a>Næste trin

- [Administrere din puljen](sql-database-elastic-pool-manage-powershell.md)
- [Opret elastiske job](sql-database-elastic-jobs-overview.md) Elastiske job kan du kører T-SQL-scripts i forhold til en hvilken som helst antal databaser i puljen.
- [Skaler ud med Azure SQL-Database](sql-database-elastic-scale-introduction.md): bruge elastiske Databaseværktøjer til skala ud.

