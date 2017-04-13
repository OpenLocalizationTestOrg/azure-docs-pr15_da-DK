<properties
   pageTitle="Oprette et SQL datawarehouse med TSQL | Microsoft Azure"
   description="Lær, hvordan du opretter en Azure SQL Data Warehouse med TSQL"
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="lodipalm"
   manager="barbkess"
   editor=""
   tags="azure-sql-data-warehouse"/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="08/24/2016"
   ms.author="lodipalm;barbkess;sonyama"/>

# <a name="create-a-sql-data-warehouse-database-by-using-transact-sql-tsql"></a>Oprette en SQL Data Warehouse database ved hjælp af Transact-SQL (TSQL)

> [AZURE.SELECTOR]
- [Azure-portalen](sql-data-warehouse-get-started-provision.md)
- [TSQL](sql-data-warehouse-get-started-create-database-tsql.md)
- [PowerShell](sql-data-warehouse-get-started-provision-powershell.md)

I denne artikel beskrives, hvordan du opretter et SQL Data Warehouse, ved hjælp af T-SQL.

## <a name="prerequisites"></a>Forudsætninger

For at komme i gang skal bruge du: 

- **Azure-konto**: Besøg [Azure gratis prøveversion][] eller [MSDN Azure kredit][] for at oprette en konto.
- **Azure SQL server**: se [Opret en logisk server Azure SQL-Database ved hjælp af Azure Portal][] eller [Opret en logisk server Azure SQL-Database med PowerShell][] få mere at vide.
- **Ressourcegruppe**: enten bruge den samme ressourcegruppe som din Azure SQL-server, eller se, [hvordan du opretter en ressourcegruppe][].
- **Miljøet til at køre T-SQL**: Du kan bruge [Visual Studio][Installing Visual Studio and SSDT], [sqlcmd][]eller [SSMS][] til at udføre T-SQL.

> [AZURE.NOTE] Oprette et SQL Data Warehouse kan resultere i en ny fakturerbar tjeneste.  Se [SQL Data Warehouse priser][] for flere oplysninger om priser.

## <a name="create-a-database-with-visual-studio"></a>Oprette en database med Visual Studio

Hvis du er ny bruger af Visual Studio, skal du se artiklen [Forespørgsel Azure SQL Data Warehouse (Visual Studio)][].  Åbn SQL Server Object Explorer i Visual Studio for at starte, og oprette forbindelse til den server, der er vært for SQL Data Warehouse databasen.  Når forbindelse, kan du oprette et SQL Data Warehouse ved at køre følgende SQL-kommando mod **master** databasen.  Denne kommando databasen MySqlDwDb oprettes med en tjeneste mål af DW400 og at databasen kan vokse til en maksimal størrelse på 10 TB.

```sql
CREATE DATABASE MySqlDwDb COLLATE SQL_Latin1_General_CP1_CI_AS (EDITION='datawarehouse', SERVICE_OBJECTIVE = 'DW400', MAXSIZE= 10240 GB);
```

## <a name="create-a-database-with-sqlcmd"></a>Oprette en database med sqlcmd

Du kan også køre den samme kommando med sqlcmd ved at køre følgende ved en kommandoprompt.

```sql
sqlcmd -S <Server Name>.database.windows.net -I -U <User> -P <Password> -Q "CREATE DATABASE MySqlDwDb COLLATE SQL_Latin1_General_CP1_CI_AS (EDITION='datawarehouse', SERVICE_OBJECTIVE = 'DW400', MAXSIZE= 10240 GB)"
```

Standard sorteringen når ikke er angivet er INDSAMLE SQL_Latin1_General_CP1_CI_AS.  Den `MAXSIZE` kan være mellem 250 GB og 240 TB.  Den `SERVICE_OBJECTIVE` kan være mellem DW100 og DW2000 [DWU][].  En liste over alle gyldige værdier, i dokumentationen MSDN til [Oprette databasen][].  Både MAXSIZE og SERVICE_OBJECTIVE kan ændres med en [JUSTER DATABASE][] T-SQL-kommando.  Sorteringen af en database kan ikke ændres efter oprettelse af.   Advarsel skal bruges, når ændring af SERVICE_OBJECTIVE som ændre DWU forårsager en genstart af tjenester, der annullerer alle forespørgsler i flight.  Ændre MAXSIZE genstarter ikke tjenester, som det er kun en enkelt metadata handling.

## <a name="next-steps"></a>Næste trin

Når din SQL Data Warehouse har klargøring du kan [indlæse eksempeldata][] , eller se, hvordan du kan [udvikle][], [indlæse][]eller [overføre][].

<!--Article references-->
[DWU]: ./sql-data-warehouse-overview-what-is.md#data-warehouse-units
[how to create a SQL Data Warehouse from the Azure portal]: sql-data-warehouse-get-started-provision.md
[Forespørgsel Azure SQL datawarehouse (Visual Studio)]: sql-data-warehouse-query-visual-studio.md
[overføre]: sql-data-warehouse-overview-migrate.md
[udvikle]: sql-data-warehouse-overview-develop.md
[indlæse]: sql-data-warehouse-overview-load.md
[indlæse eksempeldata]: sql-data-warehouse-load-sample-databases.md
[Oprette en logisk server Azure SQL-Database ved hjælp af Azure-Portal]: ../sql-database/sql-database-get-started.md#create-an-azure-sql-database-logical-server
[Oprette en logisk server Azure SQL-Database med PowerShell]: ../sql-database/sql-database-get-started-powershell.md#database-setup-create-a-resource-group-server-and-firewall-rule
[Sådan oprettes en ressourcegruppe]: ../resource-group-template-deploy-portal.md#create-resource-group
[Installing Visual Studio and SSDT]: sql-data-warehouse-install-visual-studio.md
[sqlcmd]: sql-data-warehouse-get-started-connect-sqlcmd.md

<!--MSDN references--> 
[OPRETTE DATABASE]: https://msdn.microsoft.com/library/mt204021.aspx
[ÆNDRE DATABASEN]: https://msdn.microsoft.com/library/mt204042.aspx
[SSMS]: https://msdn.microsoft.com/library/mt238290.aspx

<!--Other Web references-->
[SQL Data Warehouse priser]: https://azure.microsoft.com/pricing/details/sql-data-warehouse/
[Azure gratis prøveversion]: https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A261C142F
[MSDN Azure kredit]: https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A261C142F
