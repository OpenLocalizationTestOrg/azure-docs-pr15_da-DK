<properties
   pageTitle="Indlæse data fra csv-fil til Azure SQL Databaase (bcp) | Microsoft Azure"
   description="Bruger bcp til at importere data til Azure SQL-Database til en lille datastørrelse."
   services="sql-database"
   documentationCenter="NA"
   authors="CarlRabeler"
   manager="jhubbard"
   editor=""/>

<tags
   ms.service="sql-database"
   ms.devlang="NA"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="09/13/2016"
   ms.author="carlrab"/>


# <a name="load-data-from-csv-into-azure-sql-data-warehouse-flat-files"></a>Indlæse data fra CSV til Azure SQL Data Warehouse (flad filer)

Du kan bruge værktøjet bcp kommandolinjen til at importere data fra en CSV-fil til Azure SQL-Database.

## <a name="before-you-begin"></a>Inden du går i gang

### <a name="prerequisites"></a>Forudsætninger

Hvis du vil gennemgå dette selvstudium, skal du:

- En logisk Azure SQL Database-server og database
- Værktøjet bcp kommandolinjen installeret
- Værktøjet sqlcmd kommandolinjen installeret

Du kan hente af bcp og sqlcmd funktionerne fra [Microsoft Download Center][].

### <a name="data-in-ascii-or-utf-16-format"></a>Data i ASCII- eller UTF-16-format

Hvis du prøver dette selvstudium med dine egne data, skal dine data til brug af ASCII- eller UTF-16 kodningen da bcp ikke understøtter UTF-8. 

## <a name="1-create-a-destination-table"></a>1. Opret en destinationstabellen

Definere en tabel i SQL-Database som destinationstabellen. Kolonnerne i tabellen skal svare til dataene i hver række i datafilen.

Åbn en kommandoprompt for at oprette en tabel, og bruge sqlcmd.exe til at køre følgende kommando:


```sql
sqlcmd.exe -S <server name> -d <database name> -U <username> -P <password> -I -Q "
    CREATE TABLE DimDate2
    (
        DateId INT NOT NULL,
        CalendarQuarter TINYINT NOT NULL,
        FiscalQuarter TINYINT NOT NULL
    )
    ;
"
```


## <a name="2-create-a-source-data-file"></a>2. Opret en kilde-datafil

Åbn Notesblok og kopiere efterfølgende linjer i data til en ny tekstfil, og derefter gemme filen på din lokale temp-biblioteket, C:\Temp\DimDate2.txt. Disse data er i ASCII-format.

```
20150301,1,3
20150501,2,4
20151001,4,2
20150201,1,3
20151201,4,2
20150801,3,1
20150601,2,4
20151101,4,2
20150401,2,4
20150701,3,1
20150901,3,1
20150101,1,3
```

(Valgfrit) Åbn en kommandoprompt for at eksportere dine egne data fra en SQL Server-database, og Kør følgende kommando. Erstat tabelnavn, servernavn, databasenavn, brugernavn og din adgangskode med dine egne oplysninger.

```sql
bcp <TableName> out C:\Temp\DimDate2_export.txt -S <ServerName> -d <DatabaseName> -U <Username> -P <Password> -q -c -t ','
```

## <a name="3-load-the-data"></a>3. Indlæs data
Åbn en kommandoprompt for at indlæse dataene, og Kør følgende kommando, erstatter værdierne for servernavn, Database navn, brugernavn og adgangskode med dine egne oplysninger.

```sql
bcp DimDate2 in C:\Temp\DimDate2.txt -S <ServerName> -d <DatabaseName> -U <Username> -P <password> -q -c -t  ','
```

Brug denne kommando til at bekræfte, at dataene er blevet indlæst korrekt

```sql
sqlcmd.exe -S <server name> -d <database name> -U <username> -P <password> -I -Q "SELECT * FROM DimDate2 ORDER BY 1;"
```

Resultatet ser sådan ud:

DateId |CalendarQuarter |FiscalQuarter
----------- |--------------- |-------------
20150101 |1 |3
20150201 |1 |3
20150301 |1 |3
20150401 |2 |4
20150501 |2 |4
20150601 |2 |4
20150701 |3 |1
20150801 |3 |1
20150801 |3 |1
20151001 |4 |2
20151101 |4 |2
20151201 |4 |2


## <a name="next-steps"></a>Næste trin

For at overføre en SQL Server-database kan du se [overførsel af SQL Server-database](sql-database-cloud-migrate.md).

<!--MSDN references-->
[bcp]: https://msdn.microsoft.com/library/ms162802.aspx
[CREATE TABLE syntax]: https://msdn.microsoft.com/library/mt203953.aspx

<!--Other Web references-->
[Microsoft Download Center]: https://www.microsoft.com/download/details.aspx?id=36433
