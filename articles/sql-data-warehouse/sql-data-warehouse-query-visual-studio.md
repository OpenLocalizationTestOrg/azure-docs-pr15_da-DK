<properties
   pageTitle="Forespørgsel Azure SQL datawarehouse (Visual Studio) | Microsoft Azure"
   description="Forespørgsel SQL datawarehouse med Visual Studio."
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="sonyam"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="06/16/2016"
   ms.author="sonyama;barbkess"/>

# <a name="query-azure-sql-data-warehouse-visual-studio"></a>Forespørgsel Azure SQL datawarehouse (Visual Studio)

> [AZURE.SELECTOR]
- [Power BI](sql-data-warehouse-get-started-visualize-with-power-bi.md)
- [Azure Machine Learning](sql-data-warehouse-get-started-analyze-with-azure-machine-learning.md)
- [Visual Studio](sql-data-warehouse-query-visual-studio.md)
- [sqlcmd](sql-data-warehouse-get-started-connect-sqlcmd.md) 

Brug af Visual Studio til forespørgsel Azure SQL Data Warehouse på blot nogle få minutter. Denne metode bruger filtypenavnet SQL Server Data Tools (SSDT) i Visual Studio. 

## <a name="prerequisites"></a>Forudsætninger

Hvis du vil bruge dette selvstudium, skal du:

+ En eksisterende SQL datawarehouse. For at oprette en skal du se [oprette et SQL Data Warehouse][].
+ SSDT til Visual Studio. Hvis du har Visual Studio, har du formentlig allerede dette. Installationsvejledning og indstillinger under [installation af Visual Studio og SSDT][].
+ Det fulde SQL servernavn. For at finde, skal du se [oprette forbindelse til SQL Data Warehouse][].

## <a name="1-connect-to-your-sql-data-warehouse"></a>1. oprette forbindelse til din SQL datawarehouse

1. Åbne Visual Studio 2013 eller 2015.
2. Åbn SQL Server Object Explorer. Vælg **Vis**for at gøre dette, > **SQL Server Object Explorer**.

    ![SQL Server Object Explorer][1]

3. Klik på ikonet **Tilføj SQL Server** .

    ![Tilføje SQL Server][2]

4. Udfyld felterne i guiden Opret forbindelse til Server-vinduet.

    ![Oprette forbindelse til Server][3]

    - **Servernavn**. Angiv **servernavnet** tidligere identificeret.
    - **Godkendelse**. Markér **SQL Server-godkendelse** eller **integreret Active Directory-godkendelse**.
    - **Brugernavn** og **din adgangskode**. Angiv brugernavn og din adgangskode, hvis SQL Server-godkendelse er valgt ovenfor.
    - Klik på **Opret forbindelse**.

5. For at udforske, udvide din Azure SQL-server. Du kan få vist de databaser, der er knyttet til serveren. Udvid AdventureWorksDW for at se tabellerne i din eksempeldatabasen.

    ![Udforske AdventureWorksDW][4]

## <a name="2-run-a-sample-query"></a>2. Kør en eksempelforespørgsel

Nu, hvor der er oprettet en forbindelse til databasen, Lad os skrive en forespørgsel.

1. Højreklik på databasen i SQL Server Object Explorer.

2. Vælg **Ny forespørgsel**. Der åbnes et nyt vindue i forespørgslen.

    ![Ny forespørgsel][5]

3. Kopiér denne TSQL forespørgsel i forespørgselsvinduet:

    ```sql
    SELECT COUNT(*) FROM dbo.FactInternetSales;
    ```

4. Kør forespørgslen. Klik på den grønne pil for at gøre dette, eller brug følgende genvej: `CTRL` + `SHIFT` + `E`.

    ![Kør forespørgsel][6]

5. Se på forespørgselsresultaterne. I dette eksempel har tabellen FactInternetSales 60398 rækker.

    ![Resultater af en forespørgsel][7]

## <a name="next-steps"></a>Næste trin

Nu, hvor du kan oprette forbindelse og forespørgsel, kan du prøve at [visualisere data med PowerBI][].

For at konfigurere dit miljø til Azure Active Directory-godkendelse, skal du se [Godkend til SQL Data Warehouse][].

<!--Arcticles-->
[Oprette forbindelse til SQL datawarehouse]: sql-data-warehouse-connect-overview.md
[Oprette et SQL datawarehouse]: sql-data-warehouse-get-started-provision.md
[Installation af Visual Studio og SSDT]: sql-data-warehouse-install-visual-studio.md
[Godkende SQL datawarehouse]: sql-data-warehouse-authentication.md
[visualisere data med PowerBI]: sql-data-warehouse-get-started-visualize-with-power-bi.md  

<!--Other-->
[Azure portal]: https://portal.azure.com

<!--Image references-->

[1]: media/sql-data-warehouse-query-visual-studio/open-ssdt.png
[2]: media/sql-data-warehouse-query-visual-studio/add-server.png
[3]: media/sql-data-warehouse-query-visual-studio/connection-dialog.png
[4]: media/sql-data-warehouse-query-visual-studio/explore-sample.png
[5]: media/sql-data-warehouse-query-visual-studio/new-query2.png
[6]: media/sql-data-warehouse-query-visual-studio/run-query.png
[7]: media/sql-data-warehouse-query-visual-studio/query-results.png
