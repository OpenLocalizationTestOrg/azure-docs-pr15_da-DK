   <properties
   pageTitle="Indlæse data til Azure SQL Data Warehouse | Microsoft Azure"
   description="Lær de almindelige scenarier for indlæsning i SQL Data Warehouse af data. Dette omfatter ved hjælp af PolyBase, Azure blob-lager, flade filer og disk levering. Du kan også bruge tredjepartsværktøjer."
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
   ms.date="07/12/2016"
   ms.author="lodipalm;barbkess;sonyama"/>

# <a name="load-data-into-azure-sql-data-warehouse"></a>Indlæse data til Azure SQL Data Warehouse

En oversigt over scenarie indstillinger og anbefalinger til indlæsning af data til SQL Data Warehouse.

Delen sværeste indlæsning data er som regel klargøre dataene til afkrydsningsfeltet Indlæs. Azure forenkler indlæsning ved at bruge Azure blob-lager som et almindelige datalager for mange af tjenesterne, og brug af Azure Data Factory til dirigerer kommunikation og bevægelse mellem Azure tjenesterne. Disse processer er integreret med PolyBase teknologi, der bruger stort omfang parallel behandling (MPP) for at indlæse data parallelt fra Azure blob-lager til SQL Data Warehouse. 

Selvstudier, der indlæses eksempler på databaser, finder du [indlæse eksempler på databaser][].

## <a name="load-from-azure-blob-storage"></a>Indlæse fra Azure blob-lager
Den hurtigste måde at importere data til SQL Data Warehouse er at bruge PolyBase for at indlæse data fra Azure blob-lager. PolyBase bruger SQL Data Warehouse stort omfang parallel behandling (MPP) design til at indlæse data parallelt fra Azure blob-lager. Hvis du vil bruge PolyBase, kan du bruge T-SQL-kommandoer eller en Azure Data Factory pipeline.

### <a name="1-use-polybase-and-t-sql"></a>1. Brug PolyBase og T-SQL

Oversigt over indlæsning af processen:

2. Formatere dataene som UTF-8, da PolyBase ikke understøtter i øjeblikket UTF-16.
2. Flytte dine data til Azure blob-lager og gemme den i tekstfiler.
3. Konfigurere eksterne objekter i SQL Data Warehouse til at definere placeringen af og formatet af data
4. Køre en T-SQL-kommando til at indlæse data om parallelt til en ny databasetabel.

<!-- 5. Schedule and run a loading job. --> 

Du kan finde en selvstudium [Indlæs data fra Azure blob-lager til SQL Data Warehouse (PolyBase)][].

### <a name="2-use-azure-data-factory"></a>2. Brug Azure Data Factory

For en nemmere måde at bruge PolyBase, kan du oprette en Azure Data Factory rørledning, der bruger PolyBase for at indlæse data fra Azure blob-lager til SQL Data Warehouse. Dette er hurtig til at konfigurere, da du ikke behøver at definere T-SQL-objekter. Hvis du har brug at forespørge på de eksterne data uden at importere dataene, kan du bruge T-SQL. 

Oversigt over indlæsning af processen:

2. Formatere dataene som UTF-8, da PolyBase ikke understøtter i øjeblikket UTF-16.
2. Flytte dine data til Azure blob-lager og gemme den i tekstfiler.
3. Oprette en Azure Data Factory pipeline indtager dataene. Brug indstillingen PolyBase.
4. Planlægge og køre rørledningen.

Du kan finde en selvstudium [Indlæs data fra Azure blob-lager til SQL Data Warehouse (Azure Data Factory)][].


## <a name="load-from-sql-server"></a>Indlæse fra SQL Server
Du kan bruge Integration Services (SSIS), overføre flad filer eller Forsendelsesdato disk, der Microsoft for at indlæse data fra SQL Server til SQL Data Warehouse. Læs på, for at se en oversigt over de forskellige indlæse processer og links til selvstudier.

Se [Oversigt over overførsel][]for at planlægge en fulde dataoverførsel fra SQL Server til SQL Data Warehouse. 

### <a name="use-integration-services-ssis"></a>Brug integrationsservices (SSIS)
Hvis du allerede bruger Integration Services (SSIS) pakker indlæse til SQL Server, kan du opdatere din pakker til at bruge SQL Server som kilde- og SQL Data Warehouse som destination. Det er hurtigt og nemt at gøre, og er et godt valg, hvis du ikke forsøger at overføre din indlæsningsprocessen for at anvende data allerede i skyen. Fordeling er afkrydsningsfeltet Indlæs bliver langsommere end ved hjælp af PolyBase, fordi denne SSIS ikke udfører afkrydsningsfeltet Indlæs parallelt.

Oversigt over indlæsning af processen:

1. Revidere Integration Services-pakke til at pege på SQL Server-forekomsten for kilden og SQL Data Warehouse databasen til destinationen.
2. Overføre dit skema til SQL Data Warehouse, hvis den ikke findes allerede.
3. Skift tilknytningen i dine pakker Brug kun de datatyper, der understøttes af SQL Data Warehouse.
3. Planlægge og køre pakken.

Du kan finde en selvstudium [Indlæs data fra SQL Server til Azure SQL Data Warehouse (SSIS)][].

### <a name="use-azcopy-recommended-for--10-tb-data"></a>Brug AZCopy (anbefales til < 10 TB data)
Hvis datastørrelsen på dine er < 10 TB, kan du eksportere data fra SQL Server til flade filer, kopiere filerne til Azure blob-lager og derefter bruge PolyBase til at indlæse dataene i SQL Data Warehouse

Oversigt over indlæsning af processen:

1. Brug værktøjet bcp kommandolinjen til at eksportere data fra SQL Server til flade filer.
2. Bruge værktøjet AZCopy kommandolinjen til at kopiere data fra flade filer til Azure blob-lager.
3. Brug PolyBase indlæse til SQL Data Warehouse.

Du kan finde en selvstudium [Indlæs data fra Azure blob-lager til SQL Data Warehouse (PolyBase)][].

### <a name="use-bcp"></a>Brug bcp
Hvis du har en lille mængde data kan du bruge bcp indlæse direkte til Azure SQL Data Warehouse.

Oversigt over indlæsning af processen:
1. Brug værktøjet bcp kommandolinjen til at eksportere data fra SQL Server til flade filer.
2. Brug bcp for at indlæse data fra flade filer direkte til SQL Data Warehouse.

Du kan finde en selvstudium [Indlæs data fra SQL Server til Azure SQL Data Warehouse (bcp)][].


### <a name="use-importexport-recommended-for--10-tb-data"></a>Bruge Importér/Eksportér (anbefales til > 10 TB data)
Hvis datastørrelsen på dine er > 10 TB, og du vil flytte den til Azure, anbefaler vi, at du bruger vores disk forsendelses service [Importér/Eksportér][]. 

Oversigt over indlæsning af proces
2. Brug værktøjet bcp kommandolinjen til at eksportere data fra SQL Server til flade filer på transferrable diske.
3. Levere disk, der Microsoft.
4. Microsoft indlæser dataene i SQL Data Warehouse

## <a name="load-from-hdinsight"></a>Indlæse fra HDInsight
SQL Data Warehouse understøtter indlæsning data fra HDInsight via PolyBase. Processen er den samme som indlæsning af data fra Azure Blob-lager – brug af PolyBase til at oprette forbindelse til HDInsight at indlæse data. 

### <a name="1-use-polybase-and-t-sql"></a>1. Brug PolyBase og T-SQL

Oversigt over indlæsning af processen:

2. Formatere dataene som UTF-8, da PolyBase ikke understøtter i øjeblikket UTF-16.
2. Flytte dine data til HDInsight og gemme den i tekstfiler, ORC eller Parquet-format.
3. Konfigurere eksterne objekter i SQL Data Warehouse til at definere placeringen af og formatet af dataene.
4. Køre en T-SQL-kommando til at indlæse data om parallelt til en ny databasetabel.

Du kan finde en selvstudium [Indlæs data fra Azure blob-lager til SQL Data Warehouse (PolyBase)][].

## <a name="recommendations"></a>Anbefalinger

Mange af vores partnere har indlæsning af løsninger. Hvis du vil vide mere, skal du se en liste over vores [løsning partnere][]. 

Hvis dataene kommer fra en ikke-relationelle kilde, og du vil indlæse i SQL Data Warehouse skal du transformere i rækker og kolonner, før du indlæser den. Transformerede data nødvendigt ikke at være gemt i en database, og det kan være gemt i tekstfiler.

Oprette statistik på nyligt indlæst data. Azure SQL Data Warehouse understøtter endnu ikke er support automatisk oprette eller automatisk opdatering statistik.  Hvis du vil have den bedste ydeevne fra dine forespørgsler, er det vigtigt at oprette statistik på alle kolonner i alle tabeller efter den første belastning eller væsentlige ændringer forekommer i dataene.  Yderligere oplysninger finder du [Statistik][].


## <a name="next-steps"></a>Næste trin
Du kan finde flere tip til udvikling, [udvikling oversigt][].

<!--Image references-->

<!--Article references-->
[Indlæser data fra Azure blob-lager til SQL Data Warehouse (PolyBase)]: ./sql-data-warehouse-load-from-azure-blob-storage-with-polybase.md
[Indlæser data fra Azure blob-lager til SQL Data Warehouse (Azure Data Factory)]: ./sql-data-warehouse-load-from-azure-blob-storage-with-data-factory.md
[Indlæse data fra SQL Server til Azure SQL Data Warehouse (SSIS)]: ./sql-data-warehouse-load-from-sql-server-with-integration-services.md
[Indlæser data fra SQL Server til Azure SQL Data Warehouse (bcp)]: ./sql-data-warehouse-load-from-sql-server-with-bcp.md
[Load data from SQL Server to Azure SQL Data Warehouse (AZCopy)]: ./sql-data-warehouse-load-from-sql-server-with-azcopy.md

[Indlæse eksempler på databaser]: ./sql-data-warehouse-load-sample-databases.md
[Oversigt over dataoverførsel]: ./sql-data-warehouse-overview-migrate.md
[løsning partnere]: ./sql-data-warehouse-partner-business-intelligence.md
[Oversigt over udvikling]: ./sql-data-warehouse-overview-develop.md
[Statistik]: ./sql-data-warehouse-tables-statistics.md

<!--MSDN references-->

<!--Other Web references-->
[Importér/Eksportér]: https://azure.microsoft.com/documentation/articles/storage-import-export-service/
