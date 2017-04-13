<properties
   pageTitle="Visualisere SQL Data Warehouse data med Power BI Microsoft Azure"
   description="Visualisere SQL Data Warehouse data med Power BI"
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="lodipalm"
   manager="barbkess"
   editor="" />

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="06/16/2016"
   ms.author="lodipalm;barbkess;sonyama" />

# <a name="visualize-data-with-power-bi"></a>Visualisere data med Power BI

> [AZURE.SELECTOR]
- [Power BI](sql-data-warehouse-get-started-visualize-with-power-bi.md)
- [Azure Machine Learning](sql-data-warehouse-get-started-analyze-with-azure-machine-learning.md)
- [Visual Studio](sql-data-warehouse-query-visual-studio.md)
- [sqlcmd](sql-data-warehouse-get-started-connect-sqlcmd.md) 

Dette selvstudium viser, hvordan du bruger Power BI til at oprette forbindelse til SQL Data Warehouse og oprette en række basale visualiseringer.

> [AZURE.VIDEO azure-sql-data-warehouse-sample-data-and-powerbi]

## <a name="prerequisites"></a>Forudsætninger

Hvis du vil gennemgå dette selvstudium, skal du:

- Et SQL Data Warehouse forudindlæst med AdventureWorksDW databasen. Se [oprette et SQL Data Warehouse][] for at klargøre, og vælg at indlæse eksempeldataene. Hvis du allerede har et datawarehouse, men ikke har eksempeldata, kan du [indlæse eksempeldata manuelt][].


## <a name="1-connect-to-your-database"></a>1. oprette forbindelse til din database

Åbne Power BI og oprette forbindelse til databasen AdventureWorksDW:

1. Logge på [Azure-portalen][].
2. Klik på **SQL-databaser** , og vælg databasen AdventureWorks SQL Data Warehouse.

    ![Finde din database][1]

3. Klik på knappen 'Åbn i Power BI'.

    ![Power BI-knappen][2]

4. Du bør nu se siden SQL Data Warehouse forbindelsen, der viser din database webadresse. Klik på Næste.

    ![Power BI-forbindelse][3]

6. Angiv dit Azure SQL server-brugernavn og adgangskode, og du vil fuldt have forbindelse til databasen SQL Data Warehouse.

    ![Power BI-logon][4]

7. Når du har logget på Power BI, kan du klikke på AdventureWorksDW datasættet på bladet venstre. Dette åbner databasen.

    ![Power BI Åbn AdventureWorksDW][5]



## <a name="2-create-a-report"></a>2. Opret en rapport

Du er nu klar til at bruge Power BI til at analysere dine AdventureWorksDW eksempeldata. Hvis du vil udføre analysen, har AdventureWorksDW en visning kaldet AggregateSales. Denne visning indeholder nogle af de vigtigste målepunkter til analyse af salget af firmaet.

1. Hvis du vil oprette et kort over salgsbeløb efter postnummer, i ruden højre felter, skal du klikke på visningen AggregateSales for at udvide den. Klik på kolonnerne postnummer og salgsbeløb for at markere dem.

    ![Power BI Vælg AggregateSales][6]

    Power BI registrerer automatisk dette er geografiske data, og Placer det i et kort for dig.

    ![Power BI-kort][7]

2. Dette trin opretter et søjlediagram, der viser salgsbeløb hver kunde indtægter. For at oprette gå til visningen udvidet AggregateSales. Klik på feltet Salgsbeløb. Træk feltet kunde indtægter til venstre, og slip den i akse.

    ![Power BI Vælg akse][8]

    Vi har flyttet det liggende søjlediagram over venstre.

    ![Power BI-linjen][9]

3. Dette trin opretter et kurvediagram, der viser salgsbeløb hver Ordredato. For at oprette gå til visningen udvidet AggregateSales. Klik på salgsbeløb og Ordredato. I de visuelle effekter kolonne, klikke på ikonet kurvediagram. Dette er det første ikon i den anden linje under visualiseringer.

    ![Power BI select-kurvediagram][10]

    Du har nu en rapport, der viser tre forskellige visualiseringer af dataene.

    ![Power BI linje][11]

Du kan gemme din opgavefremdrift når som helst ved at klikke på **filer** og vælge **Gem**.

## <a name="next-steps"></a>Næste trin
Nu hvor vi har givet dig lidt tid at varme op med eksempeldataene, se Sådan [udvikle][], [indlæse][]eller [overføre][]. Eller se nærmere på [Power BI-websted][].

<!--Image references-->
[1]: media/sql-data-warehouse-get-started-visualize-with-power-bi/pbi-find-database.png
[2]: media/sql-data-warehouse-get-started-visualize-with-power-bi/pbi-button.png
[3]: media/sql-data-warehouse-get-started-visualize-with-power-bi/pbi-connect-to-azure.png
[4]: media/sql-data-warehouse-get-started-visualize-with-power-bi/pbi-sign-in.png
[5]: media/sql-data-warehouse-get-started-visualize-with-power-bi/pbi-open-adventureworks.png
[6]: media/sql-data-warehouse-get-started-visualize-with-power-bi/pbi-aggregatesales.png
[7]: media/sql-data-warehouse-get-started-visualize-with-power-bi/pbi-map.png
[8]: media/sql-data-warehouse-get-started-visualize-with-power-bi/pbi-chooseaxis.png
[9]: media/sql-data-warehouse-get-started-visualize-with-power-bi/pbi-bar.png
[10]: media/sql-data-warehouse-get-started-visualize-with-power-bi/pbi-prepare-line.png
[11]: media/sql-data-warehouse-get-started-visualize-with-power-bi/pbi-line.png
[12]: media/sql-data-warehouse-get-started-visualize-with-power-bi/pbi-save.png

<!--Article references-->
[overføre]: sql-data-warehouse-overview-migrate.md
[udvikle]: sql-data-warehouse-overview-develop.md
[indlæse]: sql-data-warehouse-overview-load.md
[indlæse eksempeldata manuelt]: sql-data-warehouse-load-sample-databases.md
[connecting to SQL Data Warehouse]: sql-data-warehouse-integrate-power-bi.md
[Oprette et SQL datawarehouse]: sql-data-warehouse-get-started-provision.md

<!--Other-->
[Azure-portalen]: https://portal.azure.com/
[Power BI-websted]: http://www.powerbi.com/
