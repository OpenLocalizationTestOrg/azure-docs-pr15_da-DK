<properties
   pageTitle="Designe beslutninger og koder teknikker til SQL Data Warehouse udvikling | Microsoft Azure"
   description="Udvikling begreber, designbeslutninger, anbefalinger og koder teknikker til SQL Data Warehouse."
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="jrowlandjones"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="08/16/2016"
   ms.author="jrj;barbkess;sonyama"/>

# <a name="design-decisions-and-coding-techniques-for-sql-data-warehouse"></a>Designbeslutninger og koder teknikker til SQL Data Warehouse

Se nærmere på følgende udvikling artikler for bedre at forstå vigtige designbeslutninger, anbefalinger og koder teknikker til SQL Data Warehouse.

## <a name="key-design-decisions"></a>Vigtige designbeslutninger
I følgende artikler fremhæve nogle af de vigtigste begreber og designbeslutninger skal du forstå til udvikling af lageret distribuerede data ved hjælp af SQL Data Warehouse:

- [forbindelser][]
- [på dokumentsammenfald][]
- [transaktioner][]
- [brugerdefinerede skemaer][]
- [tabel fordeling][]
- [tabelindeks][]
- [tabel partitioner][]
- [CTAS][]
- [Statistik][]

## <a name="development-recommendations-and-coding-techniques"></a>Udvikling anbefalinger og koder teknikker
Disse artikler fremhæve bestemte koder teknikker, tip og anbefalinger til udvikling af dine SQL Data Warehouse:

- [lagrede procedurer][]
- [etiketter][]
- [visninger][]
- [midlertidige tabeller][]
- [dynamisk SQL][]
- [løkker][]
- [gruppere efter indstillinger][]
- [variabelt tildeling][]

## <a name="next-steps"></a>Næste trin
Når du har gennemgået udvikling artiklerne nærmere via siden [Transact-SQL-reference][] for at få flere oplysninger om understøttede syntaksen til SQL Data Warehouse.

<!--Image references-->

<!--Article references-->
[på dokumentsammenfald]: ./sql-data-warehouse-develop-concurrency.md
[forbindelser]: ./sql-data-warehouse-connect-overview.md
[CTAS]: ./sql-data-warehouse-develop-ctas.md
[dynamisk SQL]: ./sql-data-warehouse-develop-dynamic-sql.md
[gruppere efter indstillinger]: ./sql-data-warehouse-develop-group-by-options.md
[etiketter]: ./sql-data-warehouse-develop-label.md
[løkker]: ./sql-data-warehouse-develop-loops.md
[Statistik]: ./sql-data-warehouse-tables-statistics.md
[lagrede procedurer]: ./sql-data-warehouse-develop-stored-procedures.md
[tabel fordeling]: ./sql-data-warehouse-tables-distribute.md
[tabelindeks]: ./sql-data-warehouse-tables-index.md
[tabel partitioner]: ./sql-data-warehouse-tables-partition.md
[midlertidige tabeller]: ./sql-data-warehouse-tables-temporary.md
[transaktioner]: ./sql-data-warehouse-develop-transactions.md
[brugerdefinerede skemaer]: ./sql-data-warehouse-develop-user-defined-schemas.md
[variabelt tildeling]: ./sql-data-warehouse-develop-variable-assignment.md
[visninger]: ./sql-data-warehouse-develop-views.md
[Transact-SQL-reference]: ./sql-data-warehouse-overview-reference.md

<!--MSDN references-->
[renaming objects]: https://msdn.microsoft.com/library/mt631611.aspx

<!--Other Web references-->
