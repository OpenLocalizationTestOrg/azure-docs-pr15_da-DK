<properties
    pageTitle="Oprette forbindelse til SQL-Database med en C#-forespørgsel | Microsoft Azure"
    description="Skrive et program i C# til at forespørge og oprette forbindelse til SQL-database. Oplysninger om IP-adresser, strenge, sikker login og gratis Visual Studio."
    services="sql-database"
    keywords="c# databaseforespørgsel, c#-forespørgsel, der oprettes forbindelse til databasen, SQL C#"
    documentationCenter=""
    authors="stevestein"
    manager="jhubbard"
    editor=""/>

<tags
    ms.service="sql-database"
    ms.workload="data-management"
    ms.tgt_pltfrm="na"
    ms.devlang="dotnet"
    ms.topic="get-started-article"
    ms.date="08/17/2016"
    ms.author="stevestein"/>



# <a name="connect-to-a-sql-database-with-visual-studio"></a>Oprette forbindelse til en SQL-Database med Visual Studio

> [AZURE.SELECTOR]
- [Visual Studio](sql-database-connect-query.md)
- [SSMS](sql-database-connect-query-ssms.md)
- [Excel](sql-database-connect-excel.md)

Lær at oprette forbindelse til en Azure SQL-database i Visual Studio. 

## <a name="prerequisites"></a>Forudsætninger


Hvis du vil oprette forbindelse til en SQL-database ved hjælp af Visual Studio, skal bruge du følgende: 


- En SQL-database til at oprette forbindelse til. I denne artikel bruges eksempeldatabasen **AdventureWorks** . For at få eksempeldatabasen AdventureWorks skal du se [oprette demodatabasen](sql-database-get-started.md).


- Visual Studio 2013 opdatering 4 (eller nyere). Microsoft yder nu Visual Studio Community for at få *gratis*.
 - [Visual Studio Community, overførsel](http://www.visualstudio.com/products/visual-studio-community-vs)
 - [Flere indstillinger for ledig Visual Studio](http://www.visualstudio.com/products/free-developer-offers-vs.aspx)




## <a name="open-visual-studio-from-the-azure-portal"></a>Åbne Visual Studio fra Azure-portalen


1. Log på [Azure-portalen](https://portal.azure.com/).

2. Klik på **Flere tjenester** > **SQL-databaser**
3. Åbn bladet **AdventureWorks** database ved at finde og klikke på *AdventureWorks* databasen.

6. Klik på knappen **værktøjer** i øverst del af bladet database:

    ![Ny forespørgsel. Oprette forbindelse til SQL-databaseserver: SQL Server Management Studio](./media/sql-database-connect-query/tools.png)

7. Klik på **Åbn i Visual Studio** (Hvis Visual Studio skal du, klikke på Hent linket):

    ![Ny forespørgsel. Oprette forbindelse til SQL-databaseserver: SQL Server Management Studio](./media/sql-database-connect-query/open-in-vs.png)


8. Visual Studio åbnes med vinduet **Opret forbindelse til Server** allerede konfigureret til at oprette forbindelse til den server og database, du har valgt i portalen.  (Klik på **Indstillinger for** at bekræfte, at forbindelsen er indstillet til den korrekte database). Skriv din server administratoradgangskode, og klik på **Opret forbindelse**.


    ![Ny forespørgsel. Oprette forbindelse til SQL-databaseserver: SQL Server Management Studio](./media/sql-database-connect-query/connect.png)


8. Hvis du ikke har en firewall regelsæt til din computers IP-adresse, du får en *kan ikke oprette forbindelse* meddelelse her. For at oprette en regel, skal du se [konfigurere en regel for serverniveau firewall Azure SQL-Database](sql-database-configure-firewall-settings.md).


9. Når du har oprettet forbindelse, åbner vinduet **SQL Server Object Explorer** med en forbindelse til din database.

    ![Ny forespørgsel. Oprette forbindelse til SQL-databaseserver: SQL Server Management Studio](./media/sql-database-connect-query/sql-server-object-explorer.png)


## <a name="run-a-sample-query"></a>Køre en eksempelforespørgsel

Nu hvor vi har forbindelse til databasen, viser, hvordan du kører en simpel forespørgsel følgende trin:

2. Højreklik på databasen, og vælg derefter **Ny forespørgsel**.

    ![Ny forespørgsel. Oprette forbindelse til SQL-databaseserver: SQL Server Management Studio](./media/sql-database-connect-query/new-query.png)

3. Kopiér og Indsæt følgende kode i forespørgselsvinduet.

        SELECT
        CustomerId
        ,Title
        ,FirstName
        ,LastName
        ,CompanyName
        FROM SalesLT.Customer;

4. Klik på **Udfør** for at køre forespørgslen:

    ![Succes. Oprette forbindelse til SQL-databaseserver: SVisual Studio](./media/sql-database-connect-query/run-query.png)

## <a name="next-steps"></a>Næste trin

- Åbne SQL-databaser i Visual Studio bruger SQL Server Data Tools. Du kan finde flere oplysninger, [SQL Server Data Tools](https://msdn.microsoft.com/library/hh272686.aspx).
- Hvis du vil oprette forbindelse til en SQL-database ved hjælp af kode, skal du se [forbinde til SQL-Database ved hjælp af .NET (C#)](sql-database-develop-dotnet-simple.md).



