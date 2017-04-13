<properties
   pageTitle="Forespørgsel Azure SQL Data Warehouse (sqlcmd) | Microsoft Azure"
   description="Forespørgsler Azure SQL Data Warehouse med sqlcmd kommandolinjeparametre Utility."
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
   ms.date="09/06/2016"
   ms.author="barbkess;sonyama"/>

# <a name="query-azure-sql-data-warehouse-sqlcmd"></a>Forespørgsel Azure SQL Data Warehouse (sqlcmd)

> [AZURE.SELECTOR]
- [Power BI](sql-data-warehouse-get-started-visualize-with-power-bi.md)
- [Azure Machine Learning](sql-data-warehouse-get-started-analyze-with-azure-machine-learning.md)
- [Visual Studio](sql-data-warehouse-query-visual-studio.md)
- [sqlcmd](sql-data-warehouse-get-started-connect-sqlcmd.md) 

Denne gennemgang bruger værktøjet [sqlcmd][] kommandolinjen til at forespørge en Azure SQL Data Warehouse.  

## <a name="1-connect"></a>1. Tilslut

Åbn kommandoprompten for at komme i gang med [sqlcmd][], og angiv **sqlcmd** efterfulgt af forbindelsesstrengen for databasen SQL Data Warehouse. Forbindelsesstrengen kræver følgende parametre:

+ **Server (-S):** Server i formularen `<`servernavn`>`. database.windows.net
+ **Database (-d):** Databasenavn.
+ **Aktivér id'er i anførselstegn (-jeg):** Id'er i anførselstegn skal være aktiveret til at oprette forbindelse til en forekomst af SQL Data Warehouse.

Hvis du vil bruge SQL Server-godkendelse, skal du tilføje parametrene brugernavn og adgangskode:

+ **Bruger (-U):** Server-bruger i formularen `<`bruger`>`
+ **Adgangskode (-P):** Adgangskode, der er knyttet til brugeren.

For eksempel kan din forbindelsesstreng se ud som følgende:

```sql
C:\>sqlcmd -S MySqlDw.database.windows.net -d Adventure_Works -U myuser -P myP@ssword -I
```

Hvis du vil bruge Azure Active Directory integreret godkendelse, skal du tilføje parametrene Azure Active Directory:

+ **Azure Active Directory-godkendelse (-G):** bruge Azure Active Directory til godkendelse

For eksempel kan din forbindelsesstreng se ud som følgende:

```sql
C:\>sqlcmd -S MySqlDw.database.windows.net -d Adventure_Works -G -I
```

> [AZURE.NOTE] Du skal [aktivere Azure Active Directory-godkendelse](sql-data-warehouse-authentication.md) til at godkende ved hjælp af Active Directory.

## <a name="2-query"></a>2. forespørgsel

Når du har forbindelse, kan du udstede alle understøttede Transact-SQL-sætninger med forekomsten.  I dette eksempel sendes forespørgsler i interaktiv tilstand.

```sql
C:\>sqlcmd -S MySqlDw.database.windows.net -d Adventure_Works -U myuser -P myP@ssword -I
1> SELECT name FROM sys.tables;
2> GO
3> QUIT
```

Disse næste eksempler viser, hvordan du kan køre dine forespørgsler i batchtilstand ved hjælp af indstillingen -Q eller rørplan dine SQL til sqlcmd.

```sql
sqlcmd -S MySqlDw.database.windows.net -d Adventure_Works -U myuser -P myP@ssword -I -Q "SELECT name FROM sys.tables;"
```

```sql
"SELECT name FROM sys.tables;" | sqlcmd -S MySqlDw.database.windows.net -d Adventure_Works -U myuser -P myP@ssword -I > .\tables.out
```

## <a name="next-steps"></a>Næste trin

Se [dokumentationen til sqlcmd][sqlcmd] yderligere oplysninger om oplysninger om de tilgængelige indstillinger i sqlcmd.

<!--Image references-->

<!--Article references-->

<!--MSDN references--> 
[sqlcmd]: https://msdn.microsoft.com/library/ms162773.aspx
[Azure portal]: https://portal.azure.com

<!--Other Web references-->
