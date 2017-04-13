<properties
   pageTitle="Indlæse eksempeldata i SQL Data Warehouse | Microsoft Azure"
   description="Indlæse eksempeldata i SQL Data Warehouse"
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
   ms.date="08/16/2016"
   ms.author="lodipalm;barbkess;sonyama"/>

#<a name="load-sample-data-into-sql-data-warehouse"></a>Indlæse eksempeldata i SQL Data Warehouse

Følg disse enkle trin for at indlæse og forespørgsel Adventure Works eksempeldatabasen. Disse scripts først bruge sqlcmd til at køre SQL som opretter tabeller og visninger. Når tabeller, der er oprettet bruge scriptene bcp indlæse data.  Hvis du ikke allerede har sqlcmd og bcp installeret, skal du følge disse links, at [installere bcp][] og [installere sqlcmd][].

##<a name="load-sample-data"></a>Indlæse eksempeldata

1. Hent [Adventure Works eksempler på Scripts til SQL Data Warehouse][] zip-filen.

2. Udpak filerne fra hentede zip til en mappe på din lokale computer.

3. Redigere udpakkede fil aw_create.bat og angive følgende variabler findes øverst i filen.  Sørg for at forlade ingen mellemrum mellem den "=" og parameteren.  Nedenfor finder du eksempler på, hvordan dine ændringer kan se.

    ```
    server=mylogicalserver.database.windows.net
    user=mydwuser
    password=Mydwpassw0rd
    database=mydwdatabase
    ```

4. Kør den redigerede aw_create.bat fra en Windows-cmd prompt.  Sørg for, at du er i den mappe, hvor du har gemt din redigerede version af aw_create.bat.
Dette script vil...
    * Slip en hvilken som helst Adventure Works tabeller eller visninger, der allerede findes i databasen
    * Oprette Adventure Works tabeller og visninger
    * Indlæse tabellerne Adventure Works ved hjælp af bcp
    * Validere rækkeantal for hver Adventure Works-tabel
    * Indsaml statistik om hver kolonne for hver Adventure Works-tabel


##<a name="query-sample-data"></a>Forespørgsel eksempeldata

Når du har indlæst nogle eksempeldata i dine SQL Data Warehouse, kan du hurtigt udføre et par forespørgsler.  Hvis du vil køre en forespørgsel, oprette forbindelse til databasen nyoprettede Adventure Works i Azure SQL DW ved hjælp af Visual Studio og SSDT, som beskrevet i dokumentet [forespørgsler med Visual Studio][] .

Eksempel på simpel select-sætning til alle oplysninger på medarbejderne:

```sql
SELECT * FROM DimEmployee;
```

Eksempel på en mere kompleks forespørgsel ved hjælp af konstruktioner som gruppe ved for at se det samlede beløb for alle salg for hver dag:

```sql
SELECT OrderDateKey, SUM(SalesAmount) AS TotalSales
FROM FactInternetSales
GROUP BY OrderDateKey
ORDER BY OrderDateKey;
```

Eksempel på en skal du MARKERE med en WHERE-delsætning til at filtrere ordrer fra før en bestemt dato:

```
SELECT OrderDateKey, SUM(SalesAmount) AS TotalSales
FROM FactInternetSales
WHERE OrderDateKey > '20020801'
GROUP BY OrderDateKey
ORDER BY OrderDateKey;
```

SQL Data Warehouse understøtter næsten alle T-SQL-konstruktioner som SQL Server understøtter.  Eventuelle forskelle er beskrevet i vores [overføre kode][] dokumentation.

## <a name="next-steps"></a>Næste trin
Nu hvor du har haft mulighed for at prøve nogle forespørgsler med eksempeldata, kan du se, hvordan du kan [udvikle][], [indlæse][]eller [overføre][] til SQL Data Warehouse.

<!--Image references-->

<!--Article references-->
[overføre]: sql-data-warehouse-overview-migrate.md
[udvikle]: sql-data-warehouse-overview-develop.md
[indlæse]: sql-data-warehouse-overview-load.md
[forespørgsel med Visual Studio]: sql-data-warehouse-query-visual-studio.md
[overføre kode]: sql-data-warehouse-migrate-code.md
[installere bcp]: sql-data-warehouse-load-with-bcp.md
[installere sqlcmd]: sql-data-warehouse-get-started-connect-sqlcmd.md

<!--Other Web references-->
[Adventure Works eksempelscripts til SQL datawarehouse]: https://migrhoststorage.blob.core.windows.net/sqldwsample/AdventureWorksSQLDW2012.zip
