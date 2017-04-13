<properties
   pageTitle="Oprette SQL Data Warehouse ved hjælp af PowerShell | Microsoft Azure"
   description="Oprette SQL Data Warehouse ved hjælp af PowerShell"
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="lodipalm"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="08/25/2016"
   ms.author="lodipalm;barbkess;sonyama"/>

# <a name="create-sql-data-warehouse-using-powershell"></a>Oprette SQL Data Warehouse ved hjælp af PowerShell

> [AZURE.SELECTOR]
- [Azure-portalen](sql-data-warehouse-get-started-provision.md)
- [TSQL](sql-data-warehouse-get-started-create-database-tsql.md)
- [PowerShell](sql-data-warehouse-get-started-provision-powershell.md)

I denne artikel beskrives, hvordan du opretter et SQL Data Warehouse, ved hjælp af PowerShell.

## <a name="prerequisites"></a>Forudsætninger

For at komme i gang skal bruge du:

- **Azure-konto**: Besøg [Azure gratis prøveversion][] eller [MSDN Azure kredit][] for at oprette en konto.
- **Azure SQL server**: se [Opret en logisk server Azure SQL-Database ved hjælp af Azure Portal][] eller [Opret en logisk server Azure SQL-Database med PowerShell][] få mere at vide.
- **Ressourcegruppe**: enten bruge den samme ressourcegruppe som din Azure SQL-server, eller se, [hvordan du opretter en ressourcegruppe][].
- **PowerShell version 1.0.3 eller større**: Du kan kontrollere din version ved at køre **Get-modul - ListAvailable-navn Azure**.  Den nyeste version kan installeres fra [Microsoft Web Platform Installer][].  Se, [hvordan du installerer og konfigurerer Azure PowerShell][]kan finde flere oplysninger om at installere den nyeste version.

> [AZURE.NOTE] Oprette et SQL Data Warehouse kan resultere i en ny fakturerbar tjeneste.  Se [SQL Data Warehouse priser][] for flere oplysninger om priser.

## <a name="create-a-sql-data-warehouse"></a>Oprette et SQL datawarehouse

1. Åbn Windows PowerShell.
2. Kør denne cmdlet til logon til Azure ressourcestyring.

    ```Powershell
    Login-AzureRmAccount
    ```
    
3. Vælg det abonnement, du vil bruge til den aktuelle session.

    ```Powershell
    Get-AzureRmSubscription -SubscriptionName "MySubscription" | Select-AzureRmSubscription
    ```

4.  Oprette databasen. I dette eksempel opretter en database med navnet "mynewsqldw" med tjenesten målsætning niveau "DW400" til serveren med navnet "sqldwserver1", som findes i ressourcegruppen med navnet "mywesteuroperesgp1".

    ```Powershell
    New-AzureRmSqlDatabase -RequestedServiceObjectiveName "DW400" -DatabaseName "mynewsqldw" -ServerName "sqldwserver1" -ResourceGroupName "mywesteuroperesgp1" -Edition "DataWarehouse" -CollationName "SQL_Latin1_General_CP1_CI_AS" -MaxSizeBytes 10995116277760
    ```

Obligatoriske parametre er:

- **RequestedServiceObjectiveName**: mængden [DWU][] du anmoder om.  Understøttede værdier er: DW100, DW200, DW300, DW400, DW500, DW600, DW1000, DW1200, DW1500, DW2000, DW3000 og DW6000.
- **Databasenavn**: navnet på den SQL Data Warehouse, du opretter.
- **Servernavn**: navnet på den server, du bruger til oprettelse af (skal være version 12).
- **ResourceGroupName**: ressourcegruppe, du bruger.  Du kan finde tilgængelige ressource bruge grupper i dit abonnement Get-AzureResource.
- **Edition**: skal være "DataWarehouse" til at oprette et SQL Data Warehouse.

Valgfrie parametre er:

- **CollationName**: standard sorteringen, hvis ikke er angivet er SQL_Latin1_General_CP1_CI_AS.  Sortering kan ikke ændres på en database.
- **MaxSizeBytes**: standard maksimal længde af en database er 10 GB.


Se [Ny AzureRmSqlDatabase][] og [Oprette Database (Azure SQL Data Warehouse)][]kan finde flere oplysninger om indstillingerne parameter.

## <a name="next-steps"></a>Næste trin

Når din SQL Data Warehouse har klargøring kan du vil prøve at [indlæse eksempeldata][] , eller se, hvordan du kan [udvikle][], [indlæse][]eller [overføre][].

Hvis du er interesseret i mere at vide om hvordan du administrerer SQL Data Warehouse fra et program, kan du se vores artikel om, hvordan du bruger [PowerShell-cmdletter og REST API'er][].

<!--Image references-->

<!--Article references-->
[DWU]: ./sql-data-warehouse-overview-what-is.md#data-warehouse-units
[overføre]: ./sql-data-warehouse-overview-migrate.md
[udvikle]: ./sql-data-warehouse-overview-develop.md
[indlæse]: ./sql-data-warehouse-load-with-bcp.md
[indlæsning af eksempeldata]: ./sql-data-warehouse-load-sample-databases.md
[PowerShell-cmdletter og REST API'er]: ./sql-data-warehouse-reference-powershell-cmdlets.md
[firewall rules]: ../sql-database-configure-firewall-settings.md

[Hvordan du installerer og konfigurerer Azure PowerShell]: ../powershell/powershell-install-configure.md
[how to create a SQL Data Warehouse from the Azure Portal]: ./sql-data-warehouse-get-started-provision.md
[Oprette en logisk server Azure SQL-Database ved hjælp af Azure-Portal]: ../sql-database/sql-database-get-started.md#create-an-azure-sql-database-logical-server
[Oprette en logisk server Azure SQL-Database med PowerShell]: ../sql-database/sql-database-get-started-powershell.md#database-setup-create-a-resource-group-server-and-firewall-rule
[Sådan oprettes en ressourcegruppe]: ../resource-group-template-deploy-portal.md#create-resource-group

<!--MSDN references--> 
[MSDN]: https://msdn.microsoft.com/library/azure/dn546722.aspx
[Ny AzureRmSqlDatabase]: https://msdn.microsoft.com/library/mt619339.aspx
[Oprette Database (Azure SQL-datalager)]: https://msdn.microsoft.com/library/mt204021.aspx

<!--Other Web references-->
[Microsoft Web Platform Installer]: https://aka.ms/webpi-azps
[SQL Data Warehouse priser]: https://azure.microsoft.com/pricing/details/sql-data-warehouse/
[Azure gratis prøveversion]: https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A261C142F
[MSDN Azure kredit]: https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A261C142F
