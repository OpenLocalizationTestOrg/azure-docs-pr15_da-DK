<properties
   pageTitle="Gendanne en SQL Azure datawarehouse (REST-API) | Microsoft Azure"
   description="REST-API opgaver for at gendanne en Azure SQL Data Warehouse."
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="Lakshmi1812"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="09/21/2016"
   ms.author="lakshmir;barbkess;sonyama"/>

# <a name="restore-an-azure-sql-data-warehouse-rest-api"></a>Gendanne en SQL Azure datawarehouse (REST-API)

> [AZURE.SELECTOR]
- [Oversigt][]
- [Portal][]
- [PowerShell][]
- [RESTEN][]

I denne artikel lærer du, hvordan du gendanner en Azure SQL Data Warehouse ved hjælp af REST-API.

## <a name="before-you-begin"></a>Inden du går i gang

**Bekræft din DTU kapacitet.** Hver SQL Data Warehouse er placeret på en SQL server (fx myserver.database.windows.net), som har en standard DTU kvote.  Før du kan gendanne et SQL Data Warehouse, skal du kontrollere, at den SQL server har tilstrækkelig resterende DTU kvote for den database, ved at blive gendannet. For at lære til at beregne DTU behov eller anmode om flere DTU, skal du se [anmode om en ændring af DTU kvote][].

## <a name="restore-an-active-or-paused-database"></a>Gendanne en aktiv eller er stoppet midlertidigt database

Gendanne en database:

1. Hente en liste over database gendannelsespunkter ved hjælp af handlingen få Database Gendan punkter.
2. Starte gendannelsen ved hjælp af handlingen [Opret database gendanne anmodning][] .
3. Spore status for din Gendan ved hjælp af handlingen [Database driftsstatus][] .

>[AZURE.NOTE] Når gendannelsen er fuldført, kan du konfigurere databasen gendannet ved følgende [konfigurere databasen efter genoprettelsen][].

## <a name="restore-a-deleted-database"></a>Gendanne en slettet database

Gendanne en slettet database:

1.  Liste over alle dine backup slettede databaser ved hjælp af handlingen [listen backup mistede databaser][] .
2.  Se detaljerne for den slettede database, du vil gendanne ved hjælp af handlingen [få backup mistede af databasen][] .
3.  Starte gendannelsen ved hjælp af handlingen [Opret database gendanne anmodning][] .
4.  Spore status for din Gendan ved hjælp af handlingen [Database driftsstatus][] .

>[AZURE.NOTE] For at konfigurere din database, når gendannelsen er fuldført, skal du se [konfigurere databasen efter genoprettelse][]. 


## <a name="next-steps"></a>Næste trin
For at få mere for at vide om business løbende funktionerne i Azure SQL Database-versioner, skal du læse [Azure SQL-Database business løbende oversigt][].

<!--Image references-->

<!--Article references-->
[Azure SQL-Database business løbende oversigt]: ./sql-database-business-continuity.md
[Anmode om en ændring af DTU kvote]: ./sql-data-warehouse-get-started-create-support-ticket.md#request-quota-change
[Konfigurere din database efter genoprettelse]: ./sql-database-disaster-recovery.md#configure-your-database-after-recovery
[How to install and configure Azure PowerShell]: ./powershell-install-configure.md
[Oversigt]: ./sql-data-warehouse-restore-database-overview.md
[Portal]: ./sql-data-warehouse-restore-database-portal.md
[PowerShell]: ./sql-data-warehouse-restore-database-powershell.md
[RESTEN]: ./sql-data-warehouse-restore-database-rest-api.md

<!--MSDN references-->
[Oprette anmodning om gendannelse af database]: https://msdn.microsoft.com/library/azure/dn509571.aspx
[Database driftsstatus]: https://msdn.microsoft.com/library/azure/dn720371.aspx
[Få backup mistede af databasen]: https://msdn.microsoft.com/library/azure/dn509574.aspx
[Listen backup sluppet databaser]: https://msdn.microsoft.com/library/azure/dn509562.aspx
[Restore-AzureRmSqlDatabase]: https://msdn.microsoft.com/library/mt693390.aspx

<!--Other Web references-->
[Azure Portal]: https://portal.azure.com/
[Microsoft Web Platform Installer]: https://aka.ms/webpi-azps
