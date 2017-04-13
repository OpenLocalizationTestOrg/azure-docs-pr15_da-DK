<properties
   pageTitle="SQL Data Warehouse Gendan | Microsoft Azure"
   description="Oversigt over databaseindstillinger Gendan for at gendanne en database i Azure SQL Data Warehouse."
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
   ms.date="09/29/2016"
   ms.author="lakshmir;barbkess;sonyama"/>


# <a name="sql-data-warehouse-restore"></a>SQL Data Warehouse Gendan

> [AZURE.SELECTOR]
- [Oversigt][]
- [Portal][]
- [PowerShell][]
- [RESTEN][]

SQL Data Warehouse indeholder både lokale og geografiske gendanner som en del af dens data warehouse nedbrud nedbrud. Bruge data warehouse sikkerhedskopier til at gendanne dit datawarehouse et gendannelsespunkt i den primære region, eller brug geografisk overflødige sikkerhedskopier til at gendanne til et andet geografisk område. Denne artikel forklares de specifikke oplysninger i gendanne et datawarehouse.

## <a name="what-is-a-data-warehouse-restore"></a>Hvad er en data warehouse gendannelse?

Gendanne et data warehouse er en ny datalager, der er oprettet ud fra en sikkerhedskopi af en eksisterende eller slettede datawarehouse. Gendannede datawarehouse opretter sikkerhedskopierede datawarehouse igen på et bestemt tidspunkt. Da SQL Data Warehouse er et distribueret system, oprettes en data warehouse gendannelse fra mange sikkerhedskopifilerne, der er gemt i Azure BLOB. 

Gendannelse af database er en vigtig del af en hvilken som helst business løbende og genoprettelse efter genoprettelsesstrategi, fordi det igen opretter dine data efter utilsigtet beskadigelse eller sletning.

Du kan finde flere oplysninger i:

-  [SQL Data Warehouse sikkerhedskopier](sql-data-warehouse-backups.md)
-  [Oversigt over Business løbende](../sql-database/sql-database-business-continuity.md)

## <a name="data-warehouse-restore-points"></a>Data warehouse gendannelsespunkter

Som en fordel ved at bruge Azure Premium lager, bruger SQL Data Warehouse Azure lagerplads Blob øjebliksbilleder til at sikkerhedskopiere primære datawarehouse. Hver snapshot har et gendannelsespunkt, der repræsenterer klokkeslæt snapshot i gang. Hvis du vil gendanne et datalager, du vælger et gendannelsespunkt og udstede en Gendan kommando.  

SQL Data Warehouse gendanner altid sikkerhedskopien til et nyt datawarehouse. Du kan enten beholde gendannede datawarehouse og den aktuelle, eller slette en af dem. Hvis du vil erstatte den aktuelle datalager med gendannede datawarehouse, kan du omdøbe den.

Hvis du vil gendanne et slettet eller er stoppet midlertidigt datawarehouse, kan du [oprette en supportbilletter](sql-data-warehouse-get-started-create-support-ticket.md). 

<!-- 
### Can I restore a deleted data warehouse?

Yes, you can restore the last available restore point.

Yes, for the next seven calendar days. When you delete a data warehouse, SQL Data Warehouse actually keeps the data warehouse and its snapshots for seven days just in case you need the data. After seven days, you won't be able to restore to any of the restore points. -->

## <a name="geo-redundant-restore"></a>Geografisk overflødige Gendan

Hvis du bruger den geografisk overflødige lagerplads, kan du gendanne datawarehouse til dit [parvis datacenter](../best-practices-availability-paired-regions.md) i et andet geografisk område. Datawarehouse genoprettes fra den seneste daglige sikkerhedskopi. 

## <a name="restore-timeline"></a>Gendanne tidslinje

Du kan gendanne en database til en hvilken som helst tilgængelige gendannelsespunkt i de seneste syv dage. Snapshots start alle fire til otte timer og er tilgængelige for syv dage. Når et øjebliksbillede er ældre end syv dage, det udløber, og dets gendannelsespunkt er ikke længere tilgængelig.

## <a name="restore-costs"></a>Gendanne omkostninger

Lagerplads gebyr for gendannede datawarehouse faktureres med Azure Premium lager hastighed. 

Hvis du holder markøren et gendannede datawarehouse, kan du betaler for lagerplads på Azure Premium lager rente. Fordelen ved afbrydelsen er du ikke betaler for DWU dataressourcer.

Se [SQL Data Warehouse priser](https://azure.microsoft.com/pricing/details/sql-data-warehouse/)kan finde flere oplysninger om SQL Data Warehouse priser.

## <a name="uses-for-restore"></a>Brug af Gendan

Den primære bruges til data warehouse Gendan er at gendanne data efter utilsigtet tab af data eller beskadigelse.

Du kan også bruge data warehouse Gendan til at bevare en sikkerhedskopi i mere end syv dage. Når sikkerhedskopieringen er gendannet, skal du har datawarehouse online og kan pause på ubestemt tid til at gemme Beregn omkostninger. Er stoppet midlertidigt databasen påløber, lagerplads gebyrer med Azure Premium lager hastighed. 

## <a name="related-topics"></a>Relaterede emner

### <a name="scenarios"></a>Scenarier

- En oversigt til løbende, under [Oversigt over Business løbende](../sql-database/sql-database-business-continuity.md)


<!-- ### Tasks -->

Hvis du vil udføre en data warehouse gendannelse, gendannes ved hjælp af:

- Azure-portalen skal du se [gendanne et datawarehouse ved hjælp af portalen Azure](sql-data-warehouse-restore-database-portal.md)
- PowerShell-cmdletter, se [gendanne et datawarehouse ved hjælp af PowerShell-cmdlet'er](sql-data-warehouse-restore-database-powershell.md)
- PLACER API'er, skal du se [gendanne et datawarehouse ved hjælp af REST API'er](sql-data-warehouse-restore-database-rest-api.md)

<!-- ### Tutorials -->

<!--Image references-->

<!--Article references-->
[Azure SQL Database business continuity overview]: ../sql-database/sql-database-business-continuity.md
[Oversigt]: ./sql-data-warehouse-restore-database-overview.md
[Portal]: ./sql-data-warehouse-restore-database-portal.md
[PowerShell]: ./sql-data-warehouse-restore-database-powershell.md
[RESTEN]: ./sql-data-warehouse-restore-database-rest-api.md

<!--MSDN references-->


<!--Other Web references-->
