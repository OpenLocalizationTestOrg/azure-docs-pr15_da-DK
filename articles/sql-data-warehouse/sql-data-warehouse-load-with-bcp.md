<properties
   pageTitle="Bruge bcp indlæse data til SQL Data Warehouse | Microsoft Azure"
   description="Få mere at vide, hvilke bcp er, og hvornår det skal bruges til opbevaring scenarier-data."
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="twounder"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="10/10/2016"
   ms.author="mausher;barbkess;sonyama"/>


# <a name="load-data-with-bcp"></a>Indlæse data med bcp

> [AZURE.SELECTOR]
- [Redgate](sql-data-warehouse-load-with-redgate.md)  
- [Data Factory](sql-data-warehouse-get-started-load-with-azure-data-factory.md)  
- [PolyBase](sql-data-warehouse-get-started-load-with-polybase.md)  
- [BCP](sql-data-warehouse-load-with-bcp.md)


**[BCP][]** er et kommandolinjen flere Indlæs værktøj, der gør det muligt at flytte data mellem SQL Server, datafiler og SQL Data Warehouse. Bruge bcp til at importere stort antal rækker i SQL Data Warehouse tabeller eller eksportere data fra SQL Server-tabeller i datafiler. Undtagen når bruges sammen med indstillingen queryout, kræver bcp ingen kendskab til Transact-SQL.

BCP er en hurtig og nem måde at flytte mindre datasæt til og fra en SQL Data Warehouse database. Den nøjagtige mængde data, der anbefales til Indlæs/Udpak via bcp afhænger af du netværksforbindelse til Azure datacentret.  Generelt dimensionstabeller kan indlæses og udtrukket let med bcp, bcp anbefales dog ikke for indlæsning af eller udtrække store datamængder.  Polybase er det anbefalede værktøj til indlæsning og hente store datamængder på samme måde som et bedre job udnytte stort omfang parallel behandling arkitekturen i SQL Data Warehouse.

Med bcp kan du:

- Bruge et enkelt kommandolinjen til at indlæse data til SQL Data Warehouse.
- Bruge en simpel kommandolinjen til at udtrække data fra SQL Data Warehouse.

Dette selvstudium lærer du at:

- Importere data til en tabel med bcp i kommandoen
- Eksportere data fra en tabel uisng bcp ud kommandoen

>[AZURE.VIDEO loading-data-into-azure-sql-data-warehouse-with-bcp]

## <a name="prerequisites"></a>Forudsætninger

Hvis du vil gennemgå dette selvstudium, skal du:

- En SQL Data Warehouse database
- Værktøjet bcp kommandolinjen installeret
- Værktøjet SQLCMD kommandolinjen installeret

>[AZURE.NOTE] Du kan hente af bcp og sqlcmd funktionerne fra [Microsoft Download Center][].

## <a name="import-data-into-sql-data-warehouse"></a>Importere data til SQL Data Warehouse

I dette selvstudium skal du oprette en tabel i Azure SQL Data Warehouse og importere data i tabellen.

### <a name="step-1-create-a-table-in-azure-sql-data-warehouse"></a>Trin 1: Oprette en tabel i Azure SQL Data Warehouse

Bruge sqlcmd til at køre følgende forespørgsel for at oprette en tabel i din forekomst fra en kommandoprompt:

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

>[AZURE.NOTE] Se [Oversigt over tabel][] eller [CREATE TABLE syntaks][] for flere oplysninger om oprettelse af en tabel på SQL Data Warehouse og de indstillinger, der er tilgængelige i delsætningen med.

### <a name="step-2-create-a-source-data-file"></a>Trin 2: Opret en kilde-datafil

Åbn Notesblok og kopiere efterfølgende linjer i data til en ny tekstfil, og derefter gemme filen på din lokale temp-biblioteket, C:\Temp\DimDate2.txt.

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

> [AZURE.NOTE] Det er vigtigt at huske, bcp.exe ikke understøtter UTF-8 filkodning. Brug ASCII-filer eller UTF-16 kodet filer, når du bruger bcp.exe.

### <a name="step-3-connect-and-import-the-data"></a>Trin 3: Opret forbindelse og importere data
Ved hjælp af bcp, kan du oprette forbindelse og importere data med følgende kommando erstatter værdierne efter behov:

```sql
bcp DimDate2 in C:\Temp\DimDate2.txt -S <Server Name> -d <Database Name> -U <Username> -P <password> -q -c -t  ','
```

Du kan kontrollere dataene er blevet indlæst ved at køre følgende forespørgsel ved hjælp af sqlcmd:

```sql
sqlcmd.exe -S <server name> -d <database name> -U <username> -P <password> -I -Q "SELECT * FROM DimDate2 ORDER BY 1;"
```

Dette bør returnerer følgende resultater:

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

### <a name="step-4-create-statistics-on-your-newly-loaded-data"></a>Trin 4: Oprette statistik på dine nyligt indlæst data

Azure SQL Data Warehouse understøtter endnu ikke er support automatisk oprette eller automatisk opdatering statistik. Hvis du vil have den bedste ydeevne fra dine forespørgsler, det er vigtigt, at statistik oprettes på alle kolonner i alle tabeller efter den første belastning eller væsentlige ændringer forekommer i dataene. Finde en detaljeret forklaring af statistik under emnet [Statistik][] i gruppen udvikling af emner. Nedenfor finder du en hurtig eksempel på, hvordan du opretter statistik på den tabled indlæst i dette eksempel

Udføre følgende oprette STATISTIK sætninger fra en meddelelse om sqlcmd:

```sql
sqlcmd.exe -S <server name> -d <database name> -U <username> -P <password> -I -Q "
    create statistics [DateId] on [DimDate2] ([DateId]);
    create statistics [CalendarQuarter] on [DimDate2] ([CalendarQuarter]);
    create statistics [FiscalQuarter] on [DimDate2] ([FiscalQuarter]);
"
```

## <a name="export-data-from-sql-data-warehouse"></a>Eksportere data fra SQL Data Warehouse
I dette selvstudium skal du oprette en datafil fra en tabel i SQL Data Warehouse. Vi vil eksportere de data, vi oprettede over til en ny datafil kaldet DimDate2_export.txt.

### <a name="step-1-export-the-data"></a>Trin 1: Eksportere dataene

Ved hjælp af værktøjet bcp, kan du oprette forbindelse og Eksportér data ved hjælp af følgende kommando erstatter værdierne efter behov:

```sql
bcp DimDate2 out C:\Temp\DimDate2_export.txt -S <Server Name> -d <Database Name> -U <Username> -P <password> -q -c -t ','
```
Du kan kontrollere dataene blev eksporteret korrekt ved at åbne den nye fil. Dataene i fil, skal stemme overens teksten nedenfor:

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

>[AZURE.NOTE] På grund af hvilke distribuerede systemer, data rækkefølgen muligvis ikke den samme på tværs af SQL Data Warehouse databaser. En anden mulighed er at bruge funktionen **queryout** af bcp til at skrive en forespørgsel Udpak i stedet for at eksportere hele tabellen.

## <a name="next-steps"></a>Næste trin
Du kan finde en oversigt over indlæsning, [Indlæs data til SQL Data Warehouse][].
Du kan finde flere tip til udvikling, [SQL Data Warehouse udvikling oversigt][].

<!--Image references-->

<!--Article references-->

[Indlæse data til SQL Data Warehouse]: ./sql-data-warehouse-overview-load.md
[Oversigt over SQL Data Warehouse udvikling]: ./sql-data-warehouse-overview-develop.md
[Oversigt over tabel]: ./sql-data-warehouse-tables-overview.md
[Statistik]: ./sql-data-warehouse-tables-statistics.md

<!--MSDN references-->
[BCP]: https://msdn.microsoft.com/library/ms162802.aspx
[Syntaksen for CREATE TABLE]: https://msdn.microsoft.com/library/mt203953.aspx

<!--Other Web references-->
[Microsoft Download Center]: https://www.microsoft.com/download/details.aspx?id=36433
