<properties
   pageTitle="Brugerdefinerede skemaer i SQL Data Warehouse | Microsoft Azure"
   description="Tip til brug af Transact-SQL skemaer i Azure SQL Data Warehouse til udvikling af løsninger."
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
   ms.date="06/14/2016"
   ms.author="jrj;barbkess;sonyama"/>

# <a name="user-defined-schemas-in-sql-data-warehouse"></a>Brugerdefinerede skemaer i SQL Data Warehouse

Traditionelle lagre bruger ofte separate databaser til at oprette programmet grænser baseret på arbejdsbelastningen, domæne eller sikkerhed. En traditionel SQL Server datawarehouse, for eksempel kan indeholde en midlertidig database, en database med data warehouse og nogle data datamarkedet databaser. I denne topologi for fungerer hver database som en arbejdsbelastningen og sikkerhed rammen i arkitekturen.

Derimod kører SQL Data Warehouse hele data warehouse arbejdsbelastningen i én database. Joinforbindelser er ikke tilladt på tværs af databasen. Derfor forventer SQL Data Warehouse, at alle tabeller, der bruges af lageret til at være gemt i én database.

> [AZURE.NOTE] SQL Data Warehouse understøtter ikke cross databaseforespørgsler af enhver type. Derfor skal data warehouse-installationer, udnytte dette mønster revideres.

## <a name="recommendations"></a>Anbefalinger

Dette er anbefalinger til konsolidering af arbejdsbelastninger, sikkerhed, domæne og funktionelle grænser ved hjælp af brugerdefinerede skemaer

1. Bruge én SQL Data Warehouse database til at køre arbejdsbelastningen hele data warehouse
2. Konsolidere dit eksisterende data warehouse miljø for at bruge én SQL Data Warehouse database
3. Udnytte **brugerdefinerede skemaer** til at levere i rammen, der er tidligere implementeret ved hjælp af databaser.

Hvis brugerdefinerede skemaer ikke er blevet brugt tidligere har du en ren tavle. Blot bruge gamle databasenavnet som grundlag for din brugerdefinerede skemaer i SQL Data Warehouse databasen.

Hvis skemaer er blevet brugt har du et par muligheder:

1. Fjerne ældre skemanavne og starte forfra
2. Bevare ældre skemanavne ved at allerede ventende ældre skemanavnet til tabelnavnet
3. Bevare ældre skemanavne ved at implementere visninger over tabellen i et ekstra skema at genskabe den gamle skema struktur.

> [AZURE.NOTE] På første inspektion kan virke indstillingen 3 som indstillingen mest tiltalende ud. Djævel er dog i detaljerne. Visninger er skrivebeskyttet i SQL Data Warehouse. Alle data eller tabelformatering ændringer skal udføres mod basistabellen. Indstillingen 3 introducerer også et lag af visninger i dit system. Du muligvis vil give dette nogle yderligere tanken, hvis du bruger visninger i din arkitektur allerede.


### <a name="examples"></a>Eksempler:

Implementere brugerdefinerede skemaer baseret på databasenavne

```sql
CREATE SCHEMA [stg]; -- stg previously database name for staging database
GO
CREATE SCHEMA [edw]; -- edw previously database name for the data warehouse
GO
CREATE TABLE [stg].[customer] -- create staging tables in the stg schema
(       CustKey BIGINT NOT NULL
,       ...
);
GO
CREATE TABLE [edw].[customer] -- create data warehouse tables in the edw schema
(       CustKey BIGINT NOT NULL
,       ...
);
```

Bevare ældre skemanavne ved udfyldt ventende dem til tabelnavnet. Bruge skemaer til arbejdsbelastningen rammen.

```sql
CREATE SCHEMA [stg]; -- stg defines the staging boundary
GO
CREATE SCHEMA [edw]; -- edw defines the data warehouse boundary
GO
CREATE TABLE [stg].[dim_customer] --pre-pend the old schema name to the table and create in the staging boundary
(       CustKey BIGINT NOT NULL
,       ...
);
GO
CREATE TABLE [edw].[dim_customer] --pre-pend the old schema name to the table and create in the data warehouse boundary
(       CustKey BIGINT NOT NULL
,       ...
);
```

Bevar ældre skemanavne ved hjælp af visninger

```sql
CREATE SCHEMA [stg]; -- stg defines the staging boundary
GO
CREATE SCHEMA [edw]; -- stg defines the data warehouse boundary
GO
CREATE SCHEMA [dim]; -- edw defines the legacy schema name boundary
GO
CREATE TABLE [stg].[customer] -- create the base staging tables in the staging boundary
(       CustKey BIGINT NOT NULL
,       ...
)
GO
CREATE TABLE [edw].[customer] -- create the base data warehouse tables in the data warehouse boundary
(       CustKey BIGINT NOT NULL
,       ...
)
GO
CREATE VIEW [dim].[customer] -- create a view in the legacy schema name boundary for presentation consistency purposes only
AS
SELECT  CustKey
,       ...
FROM    [edw].customer
;
```

> [AZURE.NOTE] Ændringer i skemaet strategi kræver en gennemgang af sikkerhedsmodellen til databasen. I mange situationer kan du muligvis forenkle sikkerhedsmodellen ved at tildele tilladelser på niveauet for skemaet. Hvis der kræves mere detaljerede tilladelser kan du bruge databaseroller.

## <a name="next-steps"></a>Næste trin
Du kan finde flere tip til udvikling, [udvikling oversigt][].

<!--Image references-->

<!--Article references-->
[Oversigt over udvikling]: sql-data-warehouse-overview-develop.md

<!--MSDN references-->

<!--Other Web references-->
