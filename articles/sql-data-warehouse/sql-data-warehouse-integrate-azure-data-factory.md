<properties
   pageTitle="Bruge Azure Data Factory med SQL datawarehouse | Microsoft Azure"
   description="Tip til brug af Azure Data Factory (ADF) med Azure SQL Data Warehouse til udvikling af løsninger."
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="lodipalm"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="08/08/2016"
   ms.author="lodipalm;barbkess;sonyama"/>

# <a name="use-azure-data-factory-with-sql-data-warehouse"></a>Bruge Azure Data Factory med SQL datawarehouse

Azure Data Factory indeholder en fuldt administreret metode til orchestrating overførsel af data og udførelse af lagrede procedurer på SQL Data Warehouse.  Dette, så du nemmere opsætning og tidsplan komplekse udtrække transformere og Indlæs (ETL) procedurer med SQL Data Warehouse. Du kan finde en mere komplet oversigt over Azure Data Factory, [Azure Data Factory dokumentation][].

## <a name="data-movement"></a>Flytning af data

Azure Data Factory gør det muligt for flytning af data mellem både lokale datakilder og forskellige Azure-tjenester.  Overordnede, aktuelle integration med Azure Data Factory understøtter flytning af data til og fra følgende steder:

+ Azure blob-lager
+ Azure SQL-Database
+ Lokal SQL Server
+ SQL Server på IaaS

Finde oplysninger om, hvordan du konfigurerer en kopi aktivitet se [kopiere data med Azure Data Factory][]

## <a name="stored-procedures"></a>Lagrede procedurer
 På samme måde, den kan bruges til at planlægge dataoverførsel, kan Azure Data Factory også bruges til at dirigerer udførelse af lagrede procedurer.  Dette giver mere komplekse rørledninger skal oprettes og udvider Azure Data Factory mulighed for at udnytte mulighederne i computational SQL Data Warehouse.

## <a name="next-steps"></a>Næste trin
Se [Oversigt over integration af SQL Data Warehouse][]for en oversigt over integration.
Du kan finde flere tip til udvikling, [SQL Data Warehouse udvikling oversigt][].

<!--Image references-->

<!--Article references-->

[Kopiere data med Azure Data Factory]: ../data-factory/data-factory-data-movement-activities.md
[Oversigt over SQL Data Warehouse udvikling]: ./sql-data-warehouse-overview-develop.md
[Oversigt over integration af SQL Data Warehouse]: ./sql-data-warehouse-overview-integrate.md

<!--MSDN references-->

<!--Other Web references-->
[Azure Data Factory-dokumentation]:https://azure.microsoft.com/documentation/services/data-factory/

