<properties
   pageTitle="PowerShell-cmdlet'er til Azure SQL Data Warehouse"
   description="Find den øverste PowerShell-cmdlet'er til Azure SQL Data Warehouse, herunder hvordan du midlertidigt afbryde og fortsætte en database."
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="sonyam"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="08/16/2016"
   ms.author="sonyama;barbkess;mausher"/>

# <a name="powershell-cmdlets-and-rest-apis-for-sql-data-warehouse"></a>PowerShell-cmdletter og REST API'er til SQL Data Warehouse

Mange SQL Data Warehouse administrationsopgaver kan administreres ved hjælp af Azure PowerShell-cmdlet'er eller REST API'er.  Nedenfor vises nogle eksempler på, hvordan du bruger PowerShell-kommandoer til at automatisere almindelige opgaver i dit SQL Data Warehouse.  Se artiklen [Administrer skalerbarhed med RESTEN][]eksempler god RESTEN.

> [AZURE.NOTE]  For at kunne bruge Azure PowerShell med SQL Data Warehouse, du har brug for Azure PowerShell version 1.0.3 eller større.  Du kan kontrollere din version ved at køre **Get-modul - ListAvailable-navn Azure**.  Den nyeste version kan installeres fra [Microsoft Web Platform Installer][].  Se, [hvordan du installerer og konfigurerer Azure PowerShell][]kan finde flere oplysninger om at installere den nyeste version.

## <a name="get-started-with-azure-powershell-cmdlets"></a>Introduktion til Azure PowerShell-cmdlet'er

1. Åbn Windows PowerShell. 
2. På PowerShell bliver spurgt, skal du køre disse kommandoer til at logge på til Azure ressourcestyring og vælg dit abonnement.

    ```PowerShell
    Login-AzureRmAccount
    Get-AzureRmSubscription
    Select-AzureRmSubscription -SubscriptionName "MySubscription"
    ```

## <a name="pause-sql-data-warehouse-example"></a>Eksempel på pause SQL Data Warehouse

Lad markøren hvile en database med navnet "Database02" hostes på en server, der kaldes "Server01."  Serveren er i en Azure ressourcegruppe med navnet "ResourceGroup1." 

```Powershell
Suspend-AzureRmSqlDatabase –ResourceGroupName "ResourceGroup1" –ServerName "Server01" –DatabaseName "Database02"
```
En variation i dette eksempel pipes [Pause AzureRmSqlDatabase][]hentede objektet.  Som et resultat er databasen standset. Den sidste kommando viser resultaterne.

```Powershell
$database = Get-AzureRmSqlDatabase –ResourceGroupName "ResourceGroup1" –ServerName "Server01" –DatabaseName "Database02"
$resultDatabase = $database | Suspend-AzureRmSqlDatabase
$resultDatabase
```

## <a name="start-sql-data-warehouse-example"></a>Start SQL Data Warehouse eksempel

Genoptage driften af en database med navnet "Database02" hostes på en server, der kaldes "Server01." Serveren er indeholdt i en ressourcegruppe med navnet "ResourceGroup1."

```Powershell
Resume-AzureRmSqlDatabase –ResourceGroupName "ResourceGroup1" –ServerName "Server01" -DatabaseName "Database02"
```

En variation i dette eksempel henter en database med navnet "Database02" fra en server, der kaldes "Server01", der er indeholdt i en ressourcegruppe med navnet "ResourceGroup1." Det pipes [CV-AzureRmSqlDatabase][]hentede objektet.

```Powershell
$database = Get-AzureRmSqlDatabase –ResourceGroupName "ResourceGroup1" –ServerName "Server01" –DatabaseName "Database02"
$resultDatabase = $database | Resume-AzureRmSqlDatabase
```

> [AZURE.NOTE] Bemærk, at hvis serveren er foo.database.windows.net, bruge "foo" som servernavn - i PowerShell-cmdlet'er.

## <a name="frequently-used-powershell-cmdlets"></a>Ofte anvendte PowerShell-cmdletter

Disse PowerShell-cmdlet'er bruges ofte med Azure SQL Data Warehouse.

- [Get-AzureRmSqlDatabase][]
- [Get-AzureRmSqlDeletedDatabaseBackup][]
- [Get-AzureRmSqlDatabaseRestorePoints][]
- [Ny AzureRmSqlDatabase][]
- [Fjern AzureRmSqlDatabase][]
- [Gendanne AzureRmSqlDatabase][] 
- [CV-AzureRmSqlDatabase][]
- [Vælg AzureRmSubscription][]
- [Angiv AzureRmSqlDatabase][]
- [Suspender AzureRmSqlDatabase][]

## <a name="next-steps"></a>Næste trin
Du kan finde flere PowerShell eksempler, i:

- [Oprette et SQL Data Warehouse, ved hjælp af PowerShell][]
- [Gendannelse af database][]

Du kan finde en liste over alle opgaver, som kan automatisere med PowerShell [-Cmdletter Azure SQL-Database][].  Du kan finde en liste over opgaver, som kan automatisere med RESTEN [Handlinger til Azure SQL-databaser][].

<!--Image references-->

<!--Article references-->
[Hvordan du installerer og konfigurerer Azure PowerShell]: ./powershell-install-configure.md
[Oprette et SQL Data Warehouse, ved hjælp af PowerShell]: ./sql-data-warehouse-get-started-provision-powershell.md
[Gendannelse af database]: ./sql-data-warehouse-restore-database-powershell.md
[Administrere skalerbarhed med RESTEN]: ./sql-data-warehouse-manage-compute-rest-api.md

<!--MSDN references-->
[Azure SQL Database-cmdletter]: https://msdn.microsoft.com/library/mt574084.aspx
[Handlinger til Azure SQL-databaser]: https://msdn.microsoft.com/library/azure/dn505719.aspx
[Get-AzureRmSqlDatabase]: https://msdn.microsoft.com/library/mt603648.aspx
[Get-AzureRmSqlDeletedDatabaseBackup]: https://msdn.microsoft.com/library/mt693387.aspx
[Get-AzureRmSqlDatabaseRestorePoints]: https://msdn.microsoft.com/library/mt603642.aspx
[Ny AzureRmSqlDatabase]: https://msdn.microsoft.com/library/mt619339.aspx
[Fjern AzureRmSqlDatabase]: https://msdn.microsoft.com/library/mt619368.aspx
[Gendanne AzureRmSqlDatabase]: https://msdn.microsoft.com/library/mt693390.aspx
[CV-AzureRmSqlDatabase]: https://msdn.microsoft.com/library/mt619347.aspx
<!-- It appears that Select-AzureRmSubscription isn't documented, so this points to Select-AzureSubscription -->
[Vælg AzureRmSubscription]: https://msdn.microsoft.com/library/dn722499.aspx
[Angiv AzureRmSqlDatabase]: https://msdn.microsoft.com/library/mt619433.aspx
[Suspender AzureRmSqlDatabase]: https://msdn.microsoft.com/library/mt619337.aspx

<!--Other Web references-->
[Microsoft Web Platform Installer]: https://aka.ms/webpi-azps
