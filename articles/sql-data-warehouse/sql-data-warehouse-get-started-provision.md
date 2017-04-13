<properties
   pageTitle="Oprette et SQL Data Warehouse i portalen Azure | Microsoft Azure"
   description="Lær, hvordan du opretter en Azure SQL Data Warehouse i portalen Azure"
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="barbkess"
   manager="jhubbard"
   editor=""
   tags="azure-sql-data-warehouse"/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="hero-article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="08/25/2016"
   ms.author="barbkess;lodipalm;sonyama"/>

# <a name="create-an-azure-sql-data-warehouse"></a>Oprette en SQL Azure datawarehouse

> [AZURE.SELECTOR]
- [Azure-portalen](sql-data-warehouse-get-started-provision.md)
- [TSQL](sql-data-warehouse-get-started-create-database-tsql.md)
- [PowerShell](sql-data-warehouse-get-started-provision-powershell.md)

Dette selvstudium bruger portalen Azure til at oprette en SQL-datalager, der indeholder en AdventureWorksDW eksempeldatabasen.


## <a name="prerequisites"></a>Forudsætninger

For at komme i gang skal bruge du:

- **Azure-konto**: Besøg [Azure gratis prøveversion][] eller [MSDN Azure kredit][] for at oprette en konto.
- **Azure SQL server**: se [oprette en logisk server Azure SQL-Database ved hjælp af Azure portal][] for at få flere oplysninger.

> [AZURE.NOTE] Oprette et SQL Data Warehouse resultere i en ny fakturerbar tjeneste.  Se [SQL Data Warehouse priser][] for at få flere oplysninger.

## <a name="create-a-sql-data-warehouse"></a>Oprette et SQL datawarehouse

1. Log på [Azure-portalen](https://portal.azure.com).

2. Klik på **+ Ny** > **Data + lagerplads** > **SQL datawarehouse**.

    ![Oprette](./media/sql-data-warehouse-get-started-provision/create-sample.gif)

3. I bladet **SQL Data Warehouse** Udfyld oplysningerne behov og derefter trykke på Opret for at oprette.

    ![Oprette database](./media/sql-data-warehouse-get-started-provision/create-database.png)

    - **Server**: Vi anbefaler, at du først markerer din server.  

    - **Databasenavn**: det navn, der bruges til at referere til SQL Data Warehouse.  Det skal være entydigt på serveren.
    
    - **Ydeevnen**: Vi anbefaler, at du starter med 400 [DWUs][DWU]. Du kan flytte skyderen til venstre eller højre for at tilpasse ydeevnen for dit datawarehouse eller skalere op eller ned efter oprettelse af.  Hvis du vil have mere for at vide om DWUs skal du se vores dokumentation på [Skalering](./sql-data-warehouse-manage-compute-overview.md) eller vores [priser siden][SQL Data Warehouse priser]. 

    - **Abonnement**: Vælg det [abonnement] , som denne SQL Data Warehouse kan faktureres til.

    - **Ressourcegruppe**: [ressourcegrupper] [ Resource group] er udviklet til at hjælpe dig med at administrere en samling af Azure ressourcer beholdere. Lær mere om [grupper](../azure-resource-manager/resource-group-overview.md).

    - **Vælg kilde**: Klik på **Vælg kilde** > **eksempel**. Azure udfylder automatisk indstillingen **Vælg eksempel** med AdventureWorksDW.

> [AZURE.NOTE] Standard sortering for et SQL Data Warehouse er SQL_Latin1_General_CP1_CI_AS. Hvis der er brug for en anden sortering, kan [T-SQL][] bruges til at oprette databasen med en anden sortering.

4. Klik på **Opret** for at oprette SQL Data Warehouse.

5. Vent et par minutter. Når dit datawarehouse er klar, skal du tilbage til [Azure-portalen](https://portal.azure.com). Du kan finde dit SQL Data Warehouse på dit dashboard, der er angivet under SQL-databaser eller i ressourcegruppen, som du brugte til at oprette den. 

    ![portalen visning](./media/sql-data-warehouse-get-started-provision/database-portal-view.png)

[AZURE.INCLUDE [SQL Database create server](../../includes/sql-database-create-new-server-firewall-portal.md)] 

## <a name="next-steps"></a>Næste trin

Nu hvor du har oprettet et SQL Data Warehouse, du er klar til at [oprette forbindelse](./sql-data-warehouse-connect-overview.md) og begynde forespørgsler.

For at indlæse data i SQL Data Warehouse, se [indlæsning af oversigt](./sql-data-warehouse-overview-load.md).

Hvis du forsøger at overføre en eksisterende database til SQL Data Warehouse, se [Oversigt over dataoverførsel](./sql-data-warehouse-overview-migrate.md) eller bruge [Overførsel Utility](./sql-data-warehouse-migrate-migration-utility.md).

Firewallregler kan også konfigureres ved hjælp af Transact-SQL. Se [sp_set_firewall_rule][] og [sp_set_database_firewall_rule][]kan finde flere oplysninger.

Det er også en god ide at kigge på de [bedste fremgangsmåder][].

<!--Article references-->
[Oprette en logisk server Azure SQL-Database ved hjælp af Azure portal]: ../sql-database/sql-database-get-started.md#create-an-azure-sql-database-logical-server
[Create an Azure SQL Database logical server with PowerShell]: ../sql-database/sql-database-get-started-powershell.md#database-setup-create-a-resource-group-server-and-firewall-rule
[resource groups]: ../resource-group-template-deploy-portal.md
[Bedste fremgangsmåder]: sql-data-warehouse-best-practices.md
[DWU]: sql-data-warehouse-overview-what-is.md#data-warehouse-units
[abonnement]: ../azure-glossary-cloud-terminology.md#subscription
[resource group]: ../azure-glossary-cloud-terminology.md#resource-group
[T-SQL]: ./sql-data-warehouse-get-started-create-database-tsql.md
 
<!--MSDN references-->
[sp_set_firewall_rule]: https://msdn.microsoft.com/library/dn270017.aspx
[sp_set_database_firewall_rule]: https://msdn.microsoft.com/library/dn270010.aspx

<!--Other Web references-->
[SQL Data Warehouse priser]: https://azure.microsoft.com/pricing/details/sql-data-warehouse/
[Azure gratis prøveversion]: https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A261C142F
[MSDN Azure kredit]: https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A261C142F

