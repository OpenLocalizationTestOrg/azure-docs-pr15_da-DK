<properties
   pageTitle="Gruppere efter indstillinger i SQL Data Warehouse | Microsoft Azure"
   description="Tip til brug af gruppe af indstillinger i Azure SQL Data Warehouse til udvikling af løsninger."
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

# <a name="group-by-options-in-sql-data-warehouse"></a>Gruppere efter indstillinger i SQL Data Warehouse

Delsætningen [GROUP BY][] bruges til at indsamle data til et oversigt sæt rækker. Der er også nogle indstillinger, udvide dets funktionalitet, der skal være arbejdet omkring, som de ikke understøttes direkte af Azure SQL Data Warehouse.

Disse indstillinger er
- GRUPPÉR efter med ROLLUP
- GRUPPERING SÆT
- GRUPPÉR efter med kube

## <a name="rollup-and-grouping-sets-options"></a>Opsummering og gruppering sæt indstillinger
Den nemmeste indstilling er at bruge `UNION ALL` i stedet for til at udføre kumulative i stedet for at stole på eksplicitte syntaksen. Resultatet er den samme

Nedenfor vises et eksempel på en gruppe ved hjælp af sætningen den `ROLLUP` indstilling:

```sql
SELECT [SalesTerritoryCountry]
,      [SalesTerritoryRegion]
,      SUM(SalesAmount)             AS TotalSalesAmount
FROM  dbo.factInternetSales s
JOIN  dbo.DimSalesTerritory t       ON s.SalesTerritoryKey       = t.SalesTerritoryKey
GROUP BY ROLLUP (
                        [SalesTerritoryCountry]
                ,       [SalesTerritoryRegion]
                )
;
```

Vi har anmodet om følgende sammenlægninger ved hjælp af aggregeret værdi:
- Lande- og områdekoder
- Land
- Hovedtotal

Erstatte dette skal du bruge `UNION ALL`; angive sammenlægninger påkrævet eksplicit til at returnere de samme resultater:

```sql
SELECT [SalesTerritoryCountry]
,      [SalesTerritoryRegion]
,      SUM(SalesAmount) AS TotalSalesAmount
FROM  dbo.factInternetSales s
JOIN  dbo.DimSalesTerritory t     ON s.SalesTerritoryKey       = t.SalesTerritoryKey
GROUP BY
       [SalesTerritoryCountry]
,      [SalesTerritoryRegion]
UNION ALL
SELECT [SalesTerritoryCountry]
,      NULL
,      SUM(SalesAmount) AS TotalSalesAmount
FROM  dbo.factInternetSales s
JOIN  dbo.DimSalesTerritory t     ON s.SalesTerritoryKey       = t.SalesTerritoryKey
GROUP BY
       [SalesTerritoryCountry]
UNION ALL
SELECT NULL
,      NULL
,      SUM(SalesAmount) AS TotalSalesAmount
FROM  dbo.factInternetSales s
JOIN  dbo.DimSalesTerritory t     ON s.SalesTerritoryKey       = t.SalesTerritoryKey;
```

Til gruppering sæt alle vi skal gøre, er indføre samme hovedstolen, men kun oprette UNION ALL sektioner til sammenlægning niveauerne vi vil se

## <a name="cube-options"></a>Kubeindstillinger
Det er muligt at oprette en gruppe af med KUBEN ved hjælp af metoden UNION ALL. Problemet er, at koden hurtigt kan blive besværligt og uhåndterlige. Du kan bruge denne mere avancerede fremgangsmåde for at reducere dette.

Lad os bruge eksemplet ovenfor.

Det første trin er at definere 'kuben', der definerer meste af sammenlægning, vi vil oprette. Det er vigtigt at notere CROSS JOIN af de to afledte tabeller. Dette opretter alle niveauerne for os. Resten af koden er virkelig der til formatering.

```sql
CREATE TABLE #Cube
WITH
(   DISTRIBUTION = ROUND_ROBIN
,   LOCATION = USER_DB
)
AS
WITH GrpCube AS
(SELECT    CAST(ISNULL(Country,'NULL')+','+ISNULL(Region,'NULL') AS NVARCHAR(50)) as 'Cols'
,          CAST(ISNULL(Country+',','')+ISNULL(Region,'') AS NVARCHAR(50))  as 'GroupBy'
,          ROW_NUMBER() OVER (ORDER BY Country) as 'Seq'
FROM       ( SELECT 'SalesTerritoryCountry' as Country
             UNION ALL
             SELECT NULL
           ) c
CROSS JOIN ( SELECT 'SalesTerritoryRegion' as Region
             UNION ALL
             SELECT NULL
           ) r
)
SELECT Cols
,      CASE WHEN SUBSTRING(GroupBy,LEN(GroupBy),1) = ','
            THEN SUBSTRING(GroupBy,1,LEN(GroupBy)-1)
            ELSE GroupBy
       END AS GroupBy  --Remove Trailing Comma
,Seq
FROM GrpCube;
```

Resultaterne af CTAS kan ses nedenfor:

![][1]

Det andet trin er at angive en måltabellen for at gemme midlertidig resultater:

```sql
DECLARE
 @SQL NVARCHAR(4000)
,@Columns NVARCHAR(4000)
,@GroupBy NVARCHAR(4000)
,@i INT = 1
,@nbr INT = 0
;
CREATE TABLE #Results
(
 [SalesTerritoryCountry] NVARCHAR(50)
,[SalesTerritoryRegion]  NVARCHAR(50)
,[TotalSalesAmount]      MONEY
)
WITH
(   DISTRIBUTION = ROUND_ROBIN
,   LOCATION = USER_DB
)
;
```

Det tredje trin er at gentage over vores kube med kolonner udføre sammenlægning. Forespørgslen skal køres en gang for hver række i tabellen #Cube midlertidige og gemme resultatet i tabellen #Results temp

```sql
SET @nbr =(SELECT MAX(Seq) FROM #Cube);

WHILE @i<=@nbr
BEGIN
    SET @Columns = (SELECT Cols    FROM #Cube where seq = @i);
    SET @GroupBy = (SELECT GroupBy FROM #Cube where seq = @i);

    SET @SQL ='INSERT INTO #Results
              SELECT '+@Columns+'
              ,      SUM(SalesAmount) AS TotalSalesAmount
              FROM  dbo.factInternetSales s
              JOIN  dbo.DimSalesTerritory t  
              ON s.SalesTerritoryKey = t.SalesTerritoryKey
              '+CASE WHEN @GroupBy <>''
                     THEN 'GROUP BY '+@GroupBy ELSE '' END

    EXEC sp_executesql @SQL;
    SET @i +=1;
END
```

Endelig kan vi returnere resultaterne ved at læse blot fra tabellen #Results midlertidige

```sql
SELECT *
FROM #Results
ORDER BY 1,2,3
;
```

Ved at opdele koden i sektioner og oprette en skaber løkker konstruktion bliver koden, administrere og vedligeholde.


## <a name="next-steps"></a>Næste trin
Du kan finde flere tip til udvikling, [udvikling oversigt][].

<!--Image references-->
[1]: media/sql-data-warehouse-develop-group-by-options/sql-data-warehouse-develop-group-by-cube.png

<!--Article references-->
[Oversigt over udvikling]: sql-data-warehouse-overview-develop.md

<!--MSDN references-->
[GRUPPERE EFTER]: https://msdn.microsoft.com/library/ms177673.aspx


<!--Other Web references-->
