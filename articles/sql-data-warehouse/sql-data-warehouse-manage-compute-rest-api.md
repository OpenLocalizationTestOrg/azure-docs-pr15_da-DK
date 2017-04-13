<properties
   pageTitle="Administrere Beregn power i Azure SQL Data Warehouse (RESTEN) | Microsoft Azure"
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
   ms.date="08/08/2016"
   ms.author="barbkess;sonyama"/>

# <a name="manage-compute-power-in-azure-sql-data-warehouse-rest"></a>Administrere Beregn power i Azure SQL Data Warehouse (RESTEN)

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

<a name="scale-performance-bk"></a>
<a name="scale-compute-bk"></a>

## <a name="scale-compute-power"></a>Skala Beregn power

[AZURE.INCLUDE [SQL Data Warehouse scale DWUs description](../../includes/sql-data-warehouse-scale-dwus-description.md)]

Brug den [Opret eller Opdater Database][] REST-API til at ændre DWUs. I følgende eksempel indstiller formålet service niveau til DW1000 for databasen MySQLDW som hostes på serveren minserver. Serveren er i en Azure ressourcegruppe med navnet ResourceGroup1.

```
PUT https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/ResourceGroup1/providers/Microsoft.Sql/servers/MyServer/databases/MySQLDW?api-version=2014-04-01-preview HTTP/1.1
Content-Type: application/json; charset=UTF-8

{
    "properties": {
        "requestedServiceObjectiveName": DW1000
    }
}
```

<a name="pause-compute-bk"></a>

## <a name="pause-compute"></a>Pause Beregn

[AZURE.INCLUDE [SQL Data Warehouse pause description](../../includes/sql-data-warehouse-pause-description.md)]

For at afbryde en database, du brug API'EN [Peg Database][] RESTEN. I følgende eksempel afbrydes en database med navnet Database02 hostes på en server, der kaldes Server01. Serveren er i en Azure ressourcegruppe med navnet ResourceGroup1.

```
POST https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/ResourceGroup1/providers/Microsoft.Sql/servers/Server01/databases/Database02/pause?api-version=2014-04-01-preview HTTP/1.1
```

<a name="resume-compute-bk"></a>

## <a name="resume-compute"></a>CV Beregn

[AZURE.INCLUDE [SQL Data Warehouse resume description](../../includes/sql-data-warehouse-resume-description.md)]

Brug [CV Database][] REST-API til at starte en database. I følgende eksempel starter en database med navnet Database02 hostes på en server, der kaldes Server01. Serveren er i en Azure ressourcegruppe med navnet ResourceGroup1. 

```
POST https://management.azure.com/subscriptions{subscription-id}/resourceGroups/ResourceGroup1/providers/Microsoft.Sql/servers/Server01/databases/Database02/resume?api-version=2014-04-01-preview HTTP/1.1
```

<a name="next-steps-bk"></a>

## <a name="next-steps"></a>Næste trin

Du kan finde andre administrationsopgaver [Oversigt over administration][].

<!--Image references-->

<!--Article references-->
[Oversigt over administration]: ./sql-data-warehouse-overview-manage.md
[Administrere Beregn oversigt]: ./sql-data-warehouse-manage-compute-overview.md

<!--MSDN references-->
[Pause Database]: https://msdn.microsoft.com/library/azure/mt718817.aspx
[CV-Database]: https://msdn.microsoft.com/library/azure/mt718820.aspx
[Oprette eller opdatere Database]: https://msdn.microsoft.com/library/azure/mt163685.aspx

<!--Other Web references-->

[Azure portal]: http://portal.azure.com/
