<properties
    pageTitle="Komme i gang med kryds-databaseforespørgsler (lodret partitionering) | Microsoft Azure"   
    description="Sådan bruges elastiske databaseforespørgsel med lodret opdelt databaser"
    services="sql-database"
    documentationCenter=""  
    manager="jhubbard"
    authors="torsteng"/>

<tags
    ms.service="sql-database"
    ms.workload="sql-database"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="05/23/2016"
    ms.author="torsteng" />

# <a name="get-started-with-cross-database-queries-vertical-partitioning-preview"></a>Komme i gang med kryds-databaseforespørgsler (lodret partitionering) (preview)

Elastiske databaseforespørgsel (preview) til Azure SQL-Database kan du køre T-SQL-forespørgsler, der strækker sig over flere databaser med et enkelt forbindelsespunkt. Dette emne gælder for [lodret opdelt databaser](sql-database-elastic-query-vertical-partitioning.md).  

Når fuldført, kan du: Lær, hvordan du konfigurerer og bruger en Azure SQL-Database til at udføre forespørgsler, der strækker sig over flere relaterede databaser. 

Du kan finde flere oplysninger om funktionen elastiske database forespørgsel, skal du se [Oversigt over Azure SQL-Database elastiske database forespørgsel](sql-database-elastic-query-overview.md). 

## <a name="create-the-sample-databases"></a>Oprette Eksempeldatabaser

Skal starte med, har vi brug at oprette to databaser, **kunder** og **ordrer**, enten i de samme eller forskellige logiske servere.   

Udføre følgende forespørgsler på **ordrer** databasen til at oprette tabellen **OrderInformation** og input eksempeldataene. 

    CREATE TABLE [dbo].[OrderInformation]( 
        [OrderID] [int] NOT NULL, 
        [CustomerID] [int] NOT NULL 
        ) 
    INSERT INTO [dbo].[OrderInformation] ([OrderID], [CustomerID]) VALUES (123, 1) 
    INSERT INTO [dbo].[OrderInformation] ([OrderID], [CustomerID]) VALUES (149, 2) 
    INSERT INTO [dbo].[OrderInformation] ([OrderID], [CustomerID]) VALUES (857, 2) 
    INSERT INTO [dbo].[OrderInformation] ([OrderID], [CustomerID]) VALUES (321, 1) 
    INSERT INTO [dbo].[OrderInformation] ([OrderID], [CustomerID]) VALUES (564, 8) 

Nu skal udføre følgende forespørgsel på **kunder** databasen til at oprette tabellen **CustomerInformation** og input eksempeldataene. 

    CREATE TABLE [dbo].[CustomerInformation]( 
        [CustomerID] [int] NOT NULL, 
        [CustomerName] [varchar](50) NULL, 
        [Company] [varchar](50) NULL 
        CONSTRAINT [CustID] PRIMARY KEY CLUSTERED ([CustomerID] ASC) 
    ) 
    INSERT INTO [dbo].[CustomerInformation] ([CustomerID], [CustomerName], [Company]) VALUES (1, 'Jack', 'ABC') 
    INSERT INTO [dbo].[CustomerInformation] ([CustomerID], [CustomerName], [Company]) VALUES (2, 'Steve', 'XYZ') 
    INSERT INTO [dbo].[CustomerInformation] ([CustomerID], [CustomerName], [Company]) VALUES (3, 'Lylla', 'MNO') 

## <a name="create-database-objects"></a>Oprette databaseobjekter
### <a name="database-scoped-master-key-and-credentials"></a>Database fastsat master nøgle og legitimationsoplysninger

1. Åbn SQL Server Management Studio eller SQL Server Data Tools i Visual Studio.
2. Oprette forbindelse til databasens ordrer og udføre følgende T-SQL-kommandoer:

        CREATE MASTER KEY ENCRYPTION BY PASSWORD = '<password>'; 
        CREATE DATABASE SCOPED CREDENTIAL ElasticDBQueryCred 
        WITH IDENTITY = '<username>', 
        SECRET = '<password>';  

    "Brugernavn" og "adgangskode" skal være det brugernavn og adgangskode, der bruges til logon til kunder databasen.
    Godkendelse ved hjælp af Azure Active Directory med elastiske forespørgsler understøttes ikke i øjeblikket.

### <a name="external-data-sources"></a>Eksterne datakilder
Hvis du vil oprette en ekstern datakilde, skal du udføre følgende kommando på ordrer databasen: 

    CREATE EXTERNAL DATA SOURCE MyElasticDBQueryDataSrc WITH 
        (TYPE = RDBMS, 
        LOCATION = '<server_name>.database.windows.net', 
        DATABASE_NAME = 'Customers', 
        CREDENTIAL = ElasticDBQueryCred, 
    ) ;

### <a name="external-tables"></a>Eksterne tabeller
Oprette en ekstern tabel på databasen ordrer, som svarer til definitionen af tabellen CustomerInformation:

    CREATE EXTERNAL TABLE [dbo].[CustomerInformation] 
    ( [CustomerID] [int] NOT NULL, 
      [CustomerName] [varchar](50) NOT NULL, 
      [Company] [varchar](50) NOT NULL) 
    WITH 
    ( DATA_SOURCE = MyElasticDBQueryDataSrc) 

## <a name="execute-a-sample-elastic-database-t-sql-query"></a>Udføre en stikprøve elastiske database T-SQL-forespørgsel

Når du har defineret den eksterne datakilde og din eksterne tabeller, du kan nu bruge T-SQL til din eksterne forespørgselstabeller. Udføre denne forespørgsel på ordrer databasen: 

    SELECT OrderInformation.CustomerID, OrderInformation.OrderId, CustomerInformation.CustomerName, CustomerInformation.Company 
    FROM OrderInformation 
    INNER JOIN CustomerInformation 
    ON CustomerInformation.CustomerID = OrderInformation.CustomerID 

## <a name="cost"></a>Omkostninger

Funktionen elastiske database forespørgsel findes i øjeblikket, til omkostningerne for Azure SQL-Database.  

Se [SQL Database priser](/pricing/details/sql-database)for prisoplysninger. 


[AZURE.INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]

<!--Image references-->

<!--anchors-->
