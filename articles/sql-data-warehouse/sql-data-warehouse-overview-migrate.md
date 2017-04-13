<properties
   pageTitle="Overføre din løsning til SQL Data Warehouse | Microsoft Azure"
   description="Overførsel vejledning til hvilket din løsning på Azure SQL Data Warehouse platform."
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
   ms.date="08/30/2016"
   ms.author="barbkess;jrj;sonyama"/>

# <a name="migrate-your-solution-to-sql-data-warehouse"></a>Overføre din løsning til SQL Data Warehouse

SQL Data Warehouse er et distribueret databasesystem, elastically skaleres til at opfylde dine behov. Hvis du vil bevare både ydeevne og skalering, er ikke alle funktioner i SQL Server implementeret i SQL Data Warehouse. De følgende emner overførsel trykke på nogle af de vigtige faktorer for at overføre din løsning til SQL Data Warehouse. Designe data lagre for skala introducerer forskellige design mønstre og så traditionelle fremgangsmåder ikke altid bedst. Derfor kan det være, at tilpasse din eksisterende løsning sikrer, at du udnytte den distribuerede platform, der leveres af SQL Data Warehouse.

Det er også vigtigt at huske, at SQL Data Warehouse er en platform, der er baseret på Microsoft Azure. Derfor kan en del af din migrering også omfatte overførsel af dine data til skyen. Overførsel af data er et emne i sig selv og skal undersøges omhyggeligt. især som enheder stigning. Overførsel af data og indlæsning af data er dedikeret emner.

## <a name="migration-guidance"></a>Vejledning til overførsel

Kontrollér, at du har læst gennem disse artikler for at sikre, at du forstår nogle af de produkt forskelle og grundlæggende begreber, før gå i gang med din migrering.

- [Overføre dit skema][]
- [Overføre dine data][]
- [Overføre din kode][]

## <a name="next-steps"></a>Næste trin

KAT (kunde vejledende Team) har også nogle gode SQL Data Warehouse vejledning som de udgiver gennem blogge.  Se nærmere på deres artikel [overflytning data til Azure SQL Data Warehouse i praksis][] for at få yderligere vejledning på overførsel.

<!--Image references-->

<!--Article references-->
[Overføre dit skema]: sql-data-warehouse-migrate-schema.md
[Overføre dine data]: sql-data-warehouse-migrate-data.md
[Overføre din kode]: sql-data-warehouse-migrate-code.md


<!--MSDN references-->


<!--Other Web references-->
[Overføre data til Azure SQL Data Warehouse i praksis]: https://blogs.msdn.microsoft.com/sqlcat/2016/08/18/migrating-data-to-azure-sql-data-warehouse-in-practice/
