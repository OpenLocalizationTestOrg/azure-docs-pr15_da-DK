<properties
   pageTitle="Oprette løsninger, der er integreret med SQL Data Warehouse | Microsoft Azure"
   description="Værktøjer og partnere med løsninger, der integreres med SQL Data Warehouse. "
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
   ms.date="05/31/2016"
   ms.author="lodipalm;barbkess;sonyama"/>

#<a name="leverage-other-services-with-sql-data-warehouse"></a>Udnytte andre tjenester med SQL Data Warehouse
Ud over dens kernefunktioner mulighed SQL Data Warehouse brugere for at udnytte mange af de andre tjenester i Azure sammen med den.  Nærmere betegnet kan er vi i øjeblikket ført trin til at integrere dybt med følgende:

+ Power BI
+ Azure Data Factory
+ Azure Machine Learning
+ Azure Stream Analytics

Vi arbejder på at oprette forbindelse til flere tjenester på tværs af Azure økosystemet.

##<a name="power-bi"></a>Power BI
Integration af Power BI kan du benytte Beregn styrken i SQL Data Warehouse med dynamisk rapportering og visualisering af Power BI. Power BI-integration omfatter i øjeblikket:

+ **Direct Connect**: en mere avanceret forbindelse med logiske pushdown mod SQL Data Warehouse.  Dette giver hurtigere analyse på en større skala.
+ **Åbn i Power BI**: knappen 'Åbn i Power BI' overfører oplysninger om forekomster til Power BI, giver mulighed for en mere problemfri forbindelse.

Se [integrere med Power BI](./sql-data-warehouse-integrate-power-bi.md) eller [Power BI dokumentation](http://blogs.msdn.com/b/powerbi/archive/2015/06/24/exploring-azure-sql-data-warehouse-with-power-bi.aspx) kan finde flere oplysninger.

##<a name="azure-data-factory"></a>Azure Data Factory
Azure Data Factory giver brugerne en administreret platform til at oprette komplekse Udpak Indlæs rørledninger.  SQL Data Warehouse integration med Azure Data Factory omfatter følgende:

+ **Lagrede procedurer**: dirigerer udførelse af lagrede procedurer på SQL Data Warehouse.
+ **Kopiér**: Brug ADF til at flytte data til SQL Data Warehouse.  Denne handling kan bruge ADFS standarddata bevægelse ordning eller PolyBase under overfladen. 

Se [integrere med Azure Data Factory](./sql-data-warehouse-integrate-azure-data-factory.md) eller [Azure Data Factory dokumentation](https://azure.microsoft.com/documentation/services/data-factory/) kan finde flere oplysninger.

##<a name="azure-machine-learning"></a>Azure Machine Learning
Azure Machine Learning er en fuldt administreret analytics-tjeneste, kan brugerne oprette besværlige modeller udnytte en stor mængde skønnet værktøjer.  SQL Data Warehouse understøtter som både kilde- og destination for disse modeller med følgende funktioner:

+ **Læse Data:** Drive modeller skaleres til brug af T-SQL mod SQL Data Warehouse.
+ **Skrive Data:** Acceptere ændringer fra en model til SQL Data Warehouse igen.

Se [integrere med Azure Machine Learning](./sql-data-warehouse-integrate-azure-machine-learning.md) eller [Azure Machine Learning dokumentation](https://azure.microsoft.com/services/machine-learning/) kan finde flere oplysninger.

##<a name="azure-stream-analytics"></a>Azure Stream Analytics
Azure Stream Analytics er en kompleks, fuldstændigt administreret infrastruktur for at behandle og forbruge begivenhed data, der er dannet ud fra Azure begivenhed Hub.  Integration med SQL Data Warehouse giver mulighed for streaming data for at blive effektivt behandlet og gemt sammen med relationsdata aktivere dybere, mere avanceret analyse.  

+ **Job Output:** Sende output fra Stream Analytics job direkte til SQL Data Warehouse.

Se [integrere med Azure Stream Analytics](./sql-data-warehouse-integrate-azure-stream-analytics.md) eller [Azure Stream Analytics dokumentation](https://azure.microsoft.com/documentation/services/stream-analytics/) kan finde flere oplysninger.

<!--Image references-->

<!--Article references-->
[development overview]: sql-data-warehouse-overview-develop/

[Azure Data Factory]: sql-data-warehouse-integrate-azure-data-factory.md
[Azure Machine Learning]: sql-data-warehouse-integrate-azure-machine-learning.md
[Azure Stream Analytics]: sql-data-warehouse-integrate-azure-stream-analytics.md
[Power BI]: sql-data-warehouse-integrate-power-bi.md
[Partners]: sql-data-warehouse-partner-business-intelligence.md

<!--MSDN references-->

<!--Other Web references-->
