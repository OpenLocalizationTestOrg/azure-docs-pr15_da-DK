<properties
   pageTitle="Administrere Beregn power i Azure SQL Data Warehouse (RESTEN) | Microsoft Azure"
   description="Transact-SQL (T-SQL) opgaver til skalering af ydeevne ved at justere DWUs. Gem omkostninger ved at skalere tilbage i top-perioder."
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
   ms.date="08/08/2016"
   ms.author="barbkess;sonyama"/>

# <a name="manage-compute-power-in-azure-sql-data-warehouse-t-sql"></a>Administrere Beregn power i Azure SQL Data Warehouse (T-SQL)

> [AZURE.SELECTOR]
- [Oversigt](sql-data-warehouse-manage-compute-overview.md)
- [Portal](sql-data-warehouse-manage-compute-portal.md)
- [PowerShell](sql-data-warehouse-manage-compute-powershell.md)
- [RESTEN](sql-data-warehouse-manage-compute-rest-api.md)
- [TSQL](sql-data-warehouse-manage-compute-tsql.md)


Skala ydeevne ved at skalere ud beregne ressourcer og hukommelse til at overholde de ændrede krav for din arbejdsbyrde. Gem omkostninger ved at skalere tilbage ressourcer under ikke-spidsbelastning eller stoppe Beregn helt. 

Denne samling af opgaver bruger T-SQL til:

- Indstillinger for visningen aktuelle DWU
- Ændre Beregn ressourcer ved at justere DWUs

For at afbryde midlertidigt eller genoptage en database skal du vælge en af de andre platform indstillinger øverst i denne artikel.

Hvis du vil vide mere om dette, kan du se [Administrer beregne power oversigt][].

<a name="current-dwu-bk"></a>

## <a name="view-current-dwu-settings"></a>Indstillinger for visningen aktuelle DWU

Sådan får du vist de aktuelle DWU indstillinger for dine databaser:

1. Åbn SQL Server Object Explorer i Visual Studio-2015.
2. Oprette forbindelse til den overordnede database, der er knyttet til den logiske SQL-databaseserver.
2. Vælg visningen sys.database_service_objectives dynamisk-styring. Her er et eksempel: 

```
SELECT
 db.name [Database],
 ds.edition [Edition],
 ds.service_objective [Service Objective]
FROM
 sys.database_service_objectives ds
 JOIN sys.databases db ON ds.database_id = db.database_id
```

<a name="scale-dwu-bk"></a>
<a name="scale-compute-bk"></a>

## <a name="scale-compute"></a>Skala Beregn

[AZURE.INCLUDE [SQL Data Warehouse scale DWUs description](../../includes/sql-data-warehouse-scale-dwus-description.md)]

Sådan ændrer du DWUs:


1. Oprette forbindelse til den overordnede database, der er knyttet til din logiske SQL-databaseserver.
2. Bruge sætningen [ALTER DATABASE][] TSQL. I følgende eksempel indstiller formålet service niveau til DW1000 for databasen MySQLDW. 

```Sql
ALTER DATABASE MySQLDW
MODIFY (SERVICE_OBJECTIVE = 'DW1000')
;
```

<a name="next-steps-bk"></a>

## <a name="next-steps"></a>Næste trin

Du kan finde andre administrationsopgaver [Oversigt over administration][].

<!--Image references-->

<!--Article references-->
[Service capacity limits]: ./sql-data-warehouse-service-capacity-limits.md
[Oversigt over administration]: ./sql-data-warehouse-overview-manage.md
[Administrere Beregn power oversigt]: ./sql-data-warehouse-manage-compute-overview.md

<!--MSDN references-->

[ÆNDRE DATABASEN]: https://msdn.microsoft.com/library/mt204042.aspx


<!--Other Web references-->

[Azure portal]: http://portal.azure.com/
