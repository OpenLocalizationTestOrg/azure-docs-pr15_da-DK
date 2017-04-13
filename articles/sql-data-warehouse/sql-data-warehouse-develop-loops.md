<properties
   pageTitle="Løkker i SQL datawarehouse | Microsoft Azure"
   description="Tip til Transact-SQL løkker og erstatte markører i Azure SQL Data Warehouse til udvikling af løsninger."
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

# <a name="loops-in-sql-data-warehouse"></a>Løkker i SQL datawarehouse
SQL Data Warehouse understøtter [MENS][] løkke til gentagen afvikling sætningen dokumentkomponenter. Dette vil fortsat for så længe de angivne betingelser er opfyldt, eller indtil koden specifikt ophører løkke ved hjælp af den `BREAK` nøgleord. Løkker er særligt nyttige til at erstatte markører, der er defineret i SQL-koden. Heldigvis Læs næsten alle markører, der er skrevet i SQL-koden er så spoler vi frem, kun række. [MENS] løkker er derfor et godt alternativ, hvis du kan finde dig selv behøver at erstatte en.

## <a name="leveraging-loops-and-replacing-cursors-in-sql-data-warehouse"></a>Udnytte løkker og erstatte markører i SQL Data Warehouse
Men før dykning i afsnit først skal du bede dig selv følgende spørgsmål: "Kan denne markør igen skrives bruge sæt baseret handlinger?". I mange tilfælde svaret bliver Ja og er ofte den bedste fremgangsmåde. Handlingen sæt baseret ofte udfører betydeligt hurtigere end en gentaget, række ad gangen metode.

Hurtigt frem skrivebeskyttede markører kan nemt erstattet med en skaber løkker konstruktion. Nedenfor er et simpelt eksempel. Eksempel på denne kode Opdaterer statistikken for hver tabel i databasen. Vi er kunne udføre hver kommando i rækkefølge som gentager tabellerne i feltet Gentag.

Først skal oprette en midlertidig tabel, der indeholder et entydigt række tal, der bruges til at identificere de enkelte sætninger:

```
CREATE TABLE #tbl
WITH
( DISTRIBUTION = ROUND_ROBIN
)
AS
SELECT  ROW_NUMBER() OVER(ORDER BY (SELECT NULL)) AS Sequence
,       [name]
,       'UPDATE STATISTICS '+QUOTENAME([name]) AS sql_code
FROM    sys.tables
;
```

Andet, initialiseret variablerne, der kræves for at udføre løkken:

```
DECLARE @nbr_statements INT = (SELECT COUNT(*) FROM #tbl)
,       @i INT = 1
;
```

Nu løkke over sætninger, der udfører dem én ad gangen:

```
WHILE   @i <= @nbr_statements
BEGIN
    DECLARE @sql_code NVARCHAR(4000) = (SELECT sql_code FROM #tbl WHERE Sequence = @i);
    EXEC    sp_executesql @sql_code;
    SET     @i +=1;
END
```

Til sidst slippe den midlertidige tabel, der er oprettet i det første trin

```
DROP TABLE #tbl;
```


<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->

## <a name="next-steps"></a>Næste trin
Du kan finde flere tip til udvikling, [udvikling oversigt][].

<!--Image references-->

<!--Article references-->
[Oversigt over udvikling]: sql-data-warehouse-overview-develop.md

<!--MSDN references-->
[STYKKE TID]: https://msdn.microsoft.com/library/ms178642.aspx


<!--Other Web references-->
