<properties
   pageTitle="Indlæse data fra SQL Server til Azure SQL Data Warehouse (bcp) | Microsoft Azure"
   description="Til en lille datastørrelse, du bruger bcp til at eksportere data fra SQL Server til flade filer og importere dataene direkte til Azure SQL Data Warehouse."
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
   ms.date="06/30/2016"
   ms.author="lodipalm;barbkess;sonyama"/>


# <a name="load-data-from-sql-server-into-azure-sql-data-warehouse-flat-files"></a>Indlæse data fra SQL Server til Azure SQL Data Warehouse (flad filer)

> [AZURE.SELECTOR]
- [SSIS](sql-data-warehouse-load-from-sql-server-with-integration-services.md)
- [PolyBase](sql-data-warehouse-load-from-sql-server-with-polybase.md)
- [BCP](sql-data-warehouse-load-from-sql-server-with-bcp.md)

Du kan bruge værktøjet bcp kommandolinjen eksportere data fra SQL Server, og derefter indlæse det direkte til Azure SQL Data Warehouse for små datasæt.

I dette selvstudium skal du bruge bcp til:

- Eksportere en tabel fra fra SQL Server ved hjælp af bcp ud kommandoen (eller oprette et simpelt eksempel-fil)
- Importere tabellen fra en flad fil til SQL Data Warehouse.
- Oprette statistik på indlæst dataene.

>[AZURE.VIDEO loading-data-into-azure-sql-data-warehouse-with-bcp]

## <a name="before-you-begin"></a>Inden du går i gang

### <a name="prerequisites"></a>Forudsætninger

Hvis du vil gennemgå dette selvstudium, skal du:

- En SQL Data Warehouse database
- Værktøjet bcp kommandolinjen installeret
- Værktøjet sqlcmd kommandolinjen installeret

Du kan hente af bcp og sqlcmd funktionerne fra [Microsoft Download Center][].

### <a name="data-in-ascii-or-utf-16-format"></a>Data i ASCII- eller UTF-16-format

Hvis du prøver dette selvstudium med dine egne data, skal dine data til brug af ASCII- eller UTF-16 kodningen da bcp ikke understøtter UTF-8. 

PolyBase understøtter UTF-8, men endnu understøtter ikke UTF-16. Bemærk, at hvis du vil kombinere bcp med PolyBase du til at transformere data til UTF-8, efter at den er eksporteret fra SQL Server. 


## <a name="1-create-a-destination-table"></a>1. Opret en destinationstabellen

Definere en tabel i SQL-datalager, der skal være destinationstabellen til afkrydsningsfeltet Indlæs. Kolonnerne i tabellen skal svare til dataene i hver række i datafilen.

Åbn en kommandoprompt for at oprette en tabel, og bruge sqlcmd.exe til at køre følgende kommando:


```sql
sqlcmd.exe -S <server name> -d <database name> -U <username> -P <password> -I -Q "
    CREATE TABLE DimDate2
    (
        DateId INT NOT NULL,
        CalendarQuarter TINYINT NOT NULL,
        FiscalQuarter TINYINT NOT NULL
    )
    WITH
    (
        CLUSTERED COLUMNSTORE INDEX,
        DISTRIBUTION = ROUND_ROBIN
    );
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

## <a name="4-create-statistics"></a>4. oprette statistik

SQL Data Warehouse understøtter endnu ikke er understøttelse af Opret automatisk eller automatisk opdatering statistik. For at få den bedste ydeevne i forespørgslen, er det vigtigt at oprette statistik på alle kolonner i alle tabeller, efter den første belastning, eller efter en hvilken som helst væsentlige ændringer i dataene. Du kan finde en detaljeret forklaring af statistik [Statistik][]. 

Kør følgende kommando for at oprette statistik på nyligt indlæst tabellen.

```sql
sqlcmd.exe -S <server name> -d <database name> -U <username> -P <password> -I -Q "
    create statistics [DateId] on [DimDate2] ([DateId]);
    create statistics [CalendarQuarter] on [DimDate2] ([CalendarQuarter]);
    create statistics [FiscalQuarter] on [DimDate2] ([FiscalQuarter]);
"
```

## <a name="5-export-data-from-sql-data-warehouse"></a>5. eksportere data fra SQL Data Warehouse
Sjove, kan du eksportere de data, du lige har indlæst tilbage af SQL Data Warehouse.  Kommandoen til at eksportere er den samme som eksportere fra SQL Server.

Der er dog forskel i resultaterne. Da data, der er gemt i distribuerede steder i SQL Data Warehouse, når du eksporterer data skriver hver beregningsnode den data til outputfilen. Rækkefølgen af dataene i outputfilen der sandsynligvis er anderledes end rækkefølgen af dataene i filen.

### <a name="export-a-table-and-compare-exported-results"></a>Eksportere en tabel og sammenligne eksporterede resultater

Åbn en kommandoprompt for at se de eksporterede data, og kører denne kommando ved hjælp af din egen parametre. Servernavn er navnet på din Azure logiske SQL Server.

```sql
bcp DimDate2 out C:\Temp\DimDate2_export.txt -S <Server Name> -d <Database Name> -U <Username> -P <password> -q -c -t ','
```
Du kan kontrollere dataene blev eksporteret korrekt ved at åbne den nye fil. Dataene i filen skal svare til teksten nedenfor, men vil sandsynligvis blive sorteret i en anden rækkefølge:

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

### <a name="export-the-results-of-a-query"></a>Eksportere resultaterne i en forespørgsel

Du kan bruge funktionen **queryout** i bcp eksportere resultaterne af en forespørgsel i stedet for at eksportere hele tabellen. 

## <a name="next-steps"></a>Næste trin
Du kan finde en oversigt over indlæsning, [Indlæs data til SQL Data Warehouse][].
Du kan finde flere tip til udvikling, [SQL Data Warehouse udvikling oversigt][].
Se [Oversigt over tabel][] eller [CREATE TABLE syntaksen][] kan finde flere oplysninger om at oprette en tabel på SQL Data Warehouse.

<!--Image references-->

<!--Article references-->

[Indlæse data til SQL Data Warehouse]: ./sql-data-warehouse-overview-load.md
[Oversigt over SQL Data Warehouse udvikling]: ./sql-data-warehouse-overview-develop.md
[Oversigt over tabel]: ./sql-data-warehouse-tables-overview.md
[Statistik]: ./sql-data-warehouse-tables-statistics.md

<!--MSDN references-->
[bcp]: https://msdn.microsoft.com/library/ms162802.aspx
[Syntaksen for CREATE TABLE]: https://msdn.microsoft.com/library/mt203953.aspx

<!--Other Web references-->
[Microsoft Download Center]: https://www.microsoft.com/download/details.aspx?id=36433
