<properties
   pageTitle="Administrere Beregn power i Azure SQL Data Warehouse (PowerShell) | Microsoft Azure"
   description="PowerShell opgaver til at administrere beregne power. Skala beregne ressourcer ved at justere DWUs. Eller pause og CV beregne ressourcer for at gemme omkostninger."
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="barbkess"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="06/13/2016"
   ms.author="barbkess;sonyama"/>

# <a name="manage-compute-power-in-azure-sql-data-warehouse-powershell"></a>Administrere Beregn power i Azure SQL Data Warehouse (PowerShell)

> [AZURE.SELECTOR]
- [Oversigt](sql-data-warehouse-manage-compute-overview.md)
- [Portal](sql-data-warehouse-manage-compute-portal.md)
- [PowerShell](sql-data-warehouse-manage-compute-powershell.md)
- [RESTEN](sql-data-warehouse-manage-compute-rest-api.md)
- [TSQL](sql-data-warehouse-manage-compute-tsql.md)


Skala ydeevne ved at skalere ud beregne ressourcer og hukommelse til at overholde de ændrede krav for din arbejdsbyrde. Gem omkostninger ved at skalere tilbage ressourcer under ikke-spidsbelastning eller stoppe Beregn helt. 

Denne samling af opgaver bruger Azure portalen for at:

- Skala Beregn
- Pause Beregn
- CV Beregn

Hvis du vil vide mere om dette, kan du se [Administrer beregne oversigt][].


## <a name="before-you-begin"></a>Inden du går i gang

### <a name="install-the-latest-version-of-azure-powershell"></a>Installere den nyeste version af Azure PowerShell

> [AZURE.NOTE]  Hvis du vil bruge Azure PowerShell med SQL Data Warehouse, du har brug for Azure PowerShell version 1.0.3 eller større.  At bekræfte din aktuelle version køre kommandoen **Get-modul - ListAvailable-navn Azure**. Du kan installere den nyeste version fra [Microsoft Web Platform Installer][].  Se, [hvordan du installerer og konfigurerer Azure PowerShell][]kan finde flere oplysninger.

### <a name="get-started-with-azure-powershell-cmdlets"></a>Introduktion til Azure PowerShell-cmdlet'er

At komme i gang:

1. Åbn Azure PowerShell. 
2. På PowerShell bliver spurgt, skal du køre disse kommandoer til at logge på til Azure ressourcestyring og vælg dit abonnement.

    ```PowerShell
    Login-AzureRmAccount
    Get-AzureRmSubscription
    Select-AzureRmSubscription -SubscriptionName "MySubscription"
    ```

<a name="scale-performance-bk"></a>
<a name="scale-compute-bk"></a>

## <a name="scale-compute-power"></a>Skala Beregn power

[AZURE.INCLUDE [SQL Data Warehouse scale DWUs description](../../includes/sql-data-warehouse-scale-dwus-description.md)]

Brug af [Sæt AzureRmSqlDatabase][] PowerShell-cmdlet til at ændre DWUs. I følgende eksempel indstiller formålet service niveau til DW1000 for databasen MySQLDW som hostes på serveren minserver. 

```Powershell
Set-AzureRmSqlDatabase -DatabaseName "MySQLDW" -ServerName "MyServer" -RequestedServiceObjectiveName "DW1000"
```

<a name="pause-compute-bk"></a>

## <a name="pause-compute"></a>Pause Beregn

[AZURE.INCLUDE [SQL Data Warehouse pause description](../../includes/sql-data-warehouse-pause-description.md)]

Brug cmdlet'en [Pause-AzureRmSqlDatabase][] for at afbryde en database. I følgende eksempel afbrydes en database med navnet Database02 hostes på en server, der kaldes Server01. Serveren er i en Azure ressourcegruppe med navnet ResourceGroup1. 

> [AZURE.NOTE] Bemærk, at hvis serveren er foo.database.windows.net, bruge "foo" som servernavn - i PowerShell-cmdlet'er.

```Powershell
Suspend-AzureRmSqlDatabase –ResourceGroupName "ResourceGroup1" `
–ServerName "Server01" –DatabaseName "Database02"
```
En variation dette næste eksempel henter databasen til objektet $database. Det derefter pipes [Pause AzureRmSqlDatabase][]objektet. Resultaterne er gemt i objektet resultDatabase. Den sidste kommando viser resultaterne.

```Powershell
$database = Get-AzureRmSqlDatabase –ResourceGroupName "ResourceGroup1" `
–ServerName "Server01" –DatabaseName "Database02"
$resultDatabase = $database | Suspend-AzureRmSqlDatabase
$resultDatabase
```

<a name="resume-compute-bk"></a>

## <a name="resume-compute"></a>CV Beregn

[AZURE.INCLUDE [SQL Data Warehouse resume description](../../includes/sql-data-warehouse-resume-description.md)]

Brug [CV-AzureRmSqlDatabase][] cmdlet til at starte en database. I følgende eksempel starter en database med navnet Database02 hostes på en server, der kaldes Server01. Serveren er i en Azure ressourcegruppe med navnet ResourceGroup1. 

```Powershell
Resume-AzureRmSqlDatabase –ResourceGroupName "ResourceGroup1" `
–ServerName "Server01" -DatabaseName "Database02"
```

En variation dette næste eksempel henter databasen til objektet $database. Derefter pipes objektet [CV-AzureRmSqlDatabase][] og gemmer resultatet i $resultDatabase. Den sidste kommando viser resultaterne.

```Powershell
$database = Get-AzureRmSqlDatabase –ResourceGroupName "ResourceGroup1" `
–ServerName "Server01" –DatabaseName "Database02"
$resultDatabase = $database | Resume-AzureRmSqlDatabase
$resultDatabase
```

<a name="next-steps-bk"></a>

## <a name="next-steps"></a>Næste trin

Du kan finde andre administrationsopgaver [Oversigt over administration][].

<!--Image references-->

<!--Article references-->
[Service capacity limits]: ./sql-data-warehouse-service-capacity-limits.md
[Oversigt over administration]: ./sql-data-warehouse-overview-manage.md
[Hvordan du installerer og konfigurerer Azure PowerShell]: ./powershell-install-configure.md
[Administrere Beregn oversigt]: ./sql-data-warehouse-manage-compute-overview.md

<!--MSDN references-->
[CV-AzureRmSqlDatabase]: https://msdn.microsoft.com/library/mt619347.aspx
[Suspender AzureRmSqlDatabase]: https://msdn.microsoft.com/library/mt619337.aspx
[Angiv AzureRmSqlDatabase]: https://msdn.microsoft.com/library/mt619433.aspx

<!--Other Web references-->
[Microsoft Web Platform Installer]: https://aka.ms/webpi-azps
[Azure portal]: http://portal.azure.com/
