<properties
   pageTitle="Overføre dit skema til SQL Data Warehouse | Microsoft Azure"
   description="Tip til at overføre dit skema til Azure SQL Data Warehouse til udvikling af løsninger."
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
   ms.date="08/25/2016"
   ms.author="jrj;barbkess;sonyama"/>

# <a name="migrate-your-schema-to-sql-data-warehouse"></a>Overføre dit skema til SQL Data Warehouse#

De følgende opsummeringer hjælpe dig med at forstå forskellene mellem SQL Server og SQL Data Warehouse kan hjælpe dig med at overføre din database.

## <a name="table-migration"></a>Overførsel af tabel

Når du overfører dine tabeller, får du vil blive fortrolig med tabelfunktioner SQL Data Warehouse tabeller.  [Oversigt over tabel][] er et godt sted at starte.  I denne artikel introducerer dig til de vigtigste overvejelser, når du opretter en tabel som tabel statistik, distribution, partitionering og indeksering.  Det dækker også nogle [ikke-understøttede tabelfunktioner][] og løsninger.

SQL Data Warehouse understøtter almindelige business-datatyper.  Se en liste over [datatyper][] artikel understøttes og [ikke-understøttede datatyper][].  [Datatyper][] artikel indeholder også en forespørgsel for at identificere [ikke-understøttede datatyper][].  Når du konverterer din datatyper, skal du se på [datatypen bedste fremgangsmåder][].

## <a name="next-steps"></a>Næste trin
Når du har overflyttet din databaseskema til SQL Data Warehouse, kan du gå videre til en af følgende artikler:

- [Overføre dine data][]
- [Overføre din kode][]

Få mere at vide om bedste fremgangsmåder for SQL Data Warehouse, artiklen [bedste fremgangsmåder][] .

<!--Image references-->

<!--Article references-->
[Overføre din kode]: ./sql-data-warehouse-migrate-code.md
[Overføre dine data]: ./sql-data-warehouse-migrate-data.md
[bedste fremgangsmåder]: ./sql-data-warehouse-best-practices.md
[Oversigt over tabel]: ./sql-data-warehouse-tables-overview.md
[ikke-understøttede tabelfunktioner]: ./sql-data-warehouse-tables-overview.md#unsupported-table-features
[datatyper]: ./sql-data-warehouse-tables-data-types.md
[ikke-understøttede datatyper]: ./sql-data-warehouse-tables-data-types.md#unsupported-data-types
[datatypen bedste fremgangsmåder]: ./sql-data-warehouse-tables-data-types.md#data-type-best-practices

<!--MSDN references-->


<!--Other Web references-->
