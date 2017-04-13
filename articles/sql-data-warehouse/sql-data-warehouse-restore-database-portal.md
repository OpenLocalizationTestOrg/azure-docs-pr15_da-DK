<properties
   pageTitle="Gendanne en SQL Azure datawarehouse (Portal) | Microsoft Azure"
   description="Azure portalen opgaver for at gendanne en Azure SQL Data Warehouse."
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

# <a name="restore-an-azure-sql-data-warehouse-portal"></a>Gendanne en SQL Azure datawarehouse (Portal)

> [AZURE.SELECTOR]
- [Oversigt][]
- [Portal][]
- [PowerShell][]
- [RESTEN][]

I denne artikel lærer du, hvordan du gendanner en Azure SQL Data Warehouse ved hjælp af portalen Azure.

## <a name="before-you-begin"></a>Inden du går i gang

**Bekræft din DTU kapacitet.** Hver SQL Data Warehouse er placeret på en SQL server (fx myserver.database.windows.net), som har en standard DTU kvote.  Før du kan gendanne et SQL Data Warehouse, skal du kontrollere, at den SQL server har tilstrækkelig resterende DTU kvote for den database, ved at blive gendannet. For at lære til at beregne DTU behov eller anmode om flere DTU, skal du se [anmode om en ændring af DTU kvote][].


## <a name="restore-an-active-or-paused-database"></a>Gendanne en aktiv eller er stoppet midlertidigt database

Gendanne en database:

1. Log på [Azure-portalen][]
2. Vælg **Gennemse** i venstre side af skærmen og derefter vælge **SQL-servere**
    
    ![](./media/sql-data-warehouse-restore-database-portal/01-browse-for-sql-server.png)
    
3. Gå til din server, og vælg den
    
    ![](./media/sql-data-warehouse-restore-database-portal/01-select-server.png)

4. Finde et SQL-datalager, du vil gendanne fra, og vælg det
    
    ![](./media/sql-data-warehouse-restore-database-portal/01-select-active-dw.png)
5. Klik på **Gendan** øverst i bladet Data Warehouse
    
    ![](./media/sql-data-warehouse-restore-database-portal/01-select-restore-from-active.png)

6. Angive et nyt **Databasenavn**
7. Vælg den seneste **Gendan punkt**
    1. Kontrollér, at du vælger det seneste gendannelsespunkt.  Da gendannelsespunkter, vises i UTC, er nogle gange vises standardindstillingen ikke det seneste gendannelsespunkt.
    
    ![](./media/sql-data-warehouse-restore-database-portal/01-restore-blade-from-active.png)

8. Klik på **OK**
9. Gendannelsen database påbegyndes og kan overvåges ved hjælp af **meddelelser**

>[AZURE.NOTE] Når gendannelsen er fuldført, kan du konfigurere databasen gendannet ved følgende [konfigurere databasen efter genoprettelsen][].


## <a name="restore-a-deleted-database"></a>Gendanne en slettet database

Gendanne en slettet database:

1. Log på [Azure-portalen][]
2. Vælg **Gennemse** i venstre side af skærmen og derefter vælge **SQL-servere**
    
    ![](./media/sql-data-warehouse-restore-database-portal/01-browse-for-sql-server.png)

3. Gå til din server, og vælg den
    
    ![](./media/sql-data-warehouse-restore-database-portal/02-select-server.png)

4. Rul ned til sektionen handlinger på din server blade
5. Klik på feltet **Slettet databaser**
    
    ![](./media/sql-data-warehouse-restore-database-portal/02-select-deleted-dws.png)

6. Vælg den slettede database, du vil gendanne
    
    ![](./media/sql-data-warehouse-restore-database-portal/02-select-deleted-dw.png)

7. Angive et nyt **Databasenavn**
    
    ![](./media/sql-data-warehouse-restore-database-portal/02-restore-blade-from-deleted.png)
    
8. Klik på **OK**
9. Gendannelsen database påbegyndes og kan overvåges ved hjælp af **meddelelser**

>[AZURE.NOTE] For at konfigurere din database, når gendannelsen er fuldført, skal du se [konfigurere databasen efter genoprettelse][]. 

## <a name="next-steps"></a>Næste trin
For at få mere for at vide om business løbende funktionerne i Azure SQL Database-versioner, skal du læse [Azure SQL-Database business løbende oversigt][].

<!--Image references-->

<!--Article references-->
[Azure SQL-Database business løbende oversigt]: ./sql-database-business-continuity.md
[Oversigt]: ./sql-data-warehouse-restore-database-overview.md
[Portal]: ./sql-data-warehouse-restore-database-portal.md
[PowerShell]: ./sql-data-warehouse-restore-database-powershell.md
[RESTEN]: ./sql-data-warehouse-restore-database-rest-api.md
[Konfigurere din database efter genoprettelse]: ./sql-database-disaster-recovery.md#configure-your-database-after-recovery
[Anmode om en ændring af DTU kvote]: ./sql-data-warehouse-get-started-create-support-ticket.md#request-quota-change

<!--MSDN references-->

<!--Blog references-->

<!--Other Web references-->
[Azure-portalen]: https://portal.azure.com/
