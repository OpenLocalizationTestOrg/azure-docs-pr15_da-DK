<properties
   pageTitle="Datatyper for tabeller i SQL Data Warehouse | Microsoft Azure"
   description="Introduktion til datatyper for Azure SQL Data Warehouse tabeller."
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
   ms.date="06/29/2016"
   ms.author="jrj;barbkess;sonyama"/>

# <a name="data-types-for-tables-in-sql-data-warehouse"></a>Datatyper for tabeller i SQL datalager

> [AZURE.SELECTOR]
- [Oversigt][]
- [Datatyper][]
- [Distribuere][]
- [Indeks][]
- [Partition][]
- [Statistik][]
- [Midlertidige][]

De mest almindeligt brugte SQL Data Warehouse understøtter datatyper.  Nedenfor finder du en liste over de datatyper, der understøttes af SQL Data Warehouse.  Yderligere oplysninger om datatypen understøttelse skal du se [oprette tabel][].

|**Understøttede datatyper**|||
|---|---|---|
[bigint][]|[decimal][]|[smallint][]|
[binært tal][]|[slæk][]|[Access][]|
[bit][]|[heltal][]|[sysName][]|
[tegn][]|[penge][]|[tid][]|
[dato][]|[nchar][]|[tinyint][]|
[dato og klokkeslæt][]|[nvarchar][]|[Entydigt id][]|
[datetime2][]|[reelt tal][]|[varbinary][]|
[DateTimeOffset][]|[smalldatetime][]|[varchar][]|


## <a name="data-type-best-practices"></a>Datatypen bedste fremgangsmåder

 Når du definerer din kolonnetyper, bliver ved hjælp af de mindste datatype, som understøtter dine data ydeevnen forespørgsel. Dette er især vigtigt for tegn og VARCHAR kolonner. Hvis den længste værdi i en kolonne er på 25 tegn, derefter definere dine kolonne som VARCHAR(25). Undgå at definere alle tegnkolonner til en stor standardlængde. Definere desuden kolonner som VARCHAR, når det er det hele, der skal bruges i stedet for at bruge [NVARCHAR][].  Brug NVARCHAR(4000) eller VARCHAR(8000), når det er muligt i stedet for NVARCHAR(MAX) eller nvarchar(Max),varchar(Max).

## <a name="polybase-limitation"></a>Polybase begrænsning

Hvis du bruger Polybase for at indlæse dine tabeller, definere dine tabeller, så den maksimale mulige rækkestørrelse, herunder fuld længden af variabel længde kolonner, ikke overstiger 32.767 byte.  Mens du kan definere en række med variabel længdedata, som kan overskrider denne bredde og indlæse rækker med BCP, kan du ikke bruger Polybase til at indlæse disse data.  Polybase understøttelse af bred rækker, føjes snart.

## <a name="unsupported-data-types"></a>Ikke-understøttede datatyper

Hvis du overfører databasen fra en anden SQL-platform som Azure SQL-Database, som du overfører, kan der opstå nogle datatyper, der ikke understøttes på SQL Data Warehouse.  Nedenfor finder du ikke-understøttede datatyper samt nogle alternativer, du kan bruge i stedet for ikke-understøttede datatyper.

|Datatype|Løsning|
|---|---|
|[geometri][]|[varbinary][]|
|[Geografi][]|[varbinary][]|
|[hierarchyid][]|[nvarchar][] (4000)|
|[Billede af][ntext,text,image]|[varbinary][]|
|[tekst][ntext,text,image]|[varchar][]|
|[ntext][ntext,text,image]|[nvarchar][]|
|[sql_variant][]|Kraftigt skrevet Opdel kolonne i flere kolonner.|
|[tabel][]|Konvertere til midlertidige tabeller.|
|[tidsstempel][]|Arbejde med kode for at bruge [datetime2][] og `CURRENT_TIMESTAMP` funktionen.  Kun konstanter understøttes som standard derfor current_timestamp ikke kan defineres som standardbegrænsning. Hvis du vil flytte række version værdier fra en tidsstemplet indtastede kolonne skal du bruge [BINÆRE][](8) eller [VARBINARY][BINÆRE](8) til ikke null-værdier eller NULL række version værdier.|
|[XML][]|[varchar][]|
|[Brugerdefinerede typer][]|konvertere tilbage til deres oprindelige datatyper, hvor det er muligt|
|standardværdier|standardværdier understøtter konstanter og kun konstanter.  Ikke-deterministisk udtryk eller funktioner, såsom `GETDATE()` eller `CURRENT_TIMESTAMP`, understøttes ikke.|

Den under SQL kan køres på din nuværende SQL-database til at identificere kolonner, der ikke understøttes af Azure SQL Data Warehouse:

```sql
SELECT  t.[name], c.[name], c.[system_type_id], c.[user_type_id], y.[is_user_defined], y.[name]
FROM sys.tables  t
JOIN sys.columns c on t.[object_id]    = c.[object_id]
JOIN sys.types   y on c.[user_type_id] = y.[user_type_id]
WHERE y.[name] IN ('geography','geometry','hierarchyid','image','text','ntext','sql_variant','timestamp','xml')
 AND  y.[is_user_defined] = 1;
```

## <a name="next-steps"></a>Næste trin

Se artiklerne på [Tabel oversigt][Oversigt], [distribuere en tabel][fordel], [indeksering en tabel][indeks], [partitionering en tabel][Partition], [Vedligeholde tabel statistik][Statistik] og [Midlertidige tabeller][midlertidige]for at få mere for at vide.  Få mere at vide om bedste fremgangsmåder for, i [SQL Data Warehouse bedste fremgangsmåder][].

<!--Image references-->

<!--Article references-->
[Oversigt]: ./sql-data-warehouse-tables-overview.md
[Datatyper]: ./sql-data-warehouse-tables-data-types.md
[Distribuere]: ./sql-data-warehouse-tables-distribute.md
[Indeks]: ./sql-data-warehouse-tables-index.md
[Partition]: ./sql-data-warehouse-tables-partition.md
[Statistik]: ./sql-data-warehouse-tables-statistics.md
[Midlertidige]: ./sql-data-warehouse-tables-temporary.md
[SQL Data Warehouse bedste fremgangsmåder]: ./sql-data-warehouse-best-practices.md

<!--MSDN references-->

<!--Other Web references-->
[oprette tabel]: https://msdn.microsoft.com/library/mt203953.aspx
[bigint]: https://msdn.microsoft.com/library/ms187745.aspx
[binært tal]: https://msdn.microsoft.com/library/ms188362.aspx
[bit]: https://msdn.microsoft.com/library/ms177603.aspx
[tegn]: https://msdn.microsoft.com/library/ms176089.aspx
[dato]: https://msdn.microsoft.com/library/bb630352.aspx
[dato og klokkeslæt]: https://msdn.microsoft.com/library/ms187819.aspx
[datetime2]: https://msdn.microsoft.com/library/bb677335.aspx
[DateTimeOffset]: https://msdn.microsoft.com/library/bb630289.aspx
[decimal]: https://msdn.microsoft.com/library/ms187746.aspx
[slæk]: https://msdn.microsoft.com/library/ms173773.aspx
[geometri]: https://msdn.microsoft.com/library/cc280487.aspx
[Geografi]: https://msdn.microsoft.com/library/cc280766.aspx
[hierarchyid]: https://msdn.microsoft.com/library/bb677290.aspx
[heltal]: https://msdn.microsoft.com/library/ms187745.aspx
[penge]: https://msdn.microsoft.com/library/ms179882.aspx
[nchar]: https://msdn.microsoft.com/library/ms186939.aspx
[nvarchar]: https://msdn.microsoft.com/library/ms186939.aspx
[ntext,text,image]: https://msdn.microsoft.com/library/ms187993.aspx
[reelt tal]: https://msdn.microsoft.com/library/ms173773.aspx
[smalldatetime]: https://msdn.microsoft.com/library/ms182418.aspx
[smallint]: https://msdn.microsoft.com/library/ms187745.aspx
[Access]: https://msdn.microsoft.com/library/ms179882.aspx
[sql_variant]: https://msdn.microsoft.com/library/ms173829.aspx
[sysName]: https://msdn.microsoft.com/library/ms186939.aspx
[tabel]: https://msdn.microsoft.com/library/ms175010.aspx
[tid]: https://msdn.microsoft.com/library/bb677243.aspx
[tidsstempel]: https://msdn.microsoft.com/library/ms182776.aspx
[tinyint]: https://msdn.microsoft.com/library/ms187745.aspx
[Entydigt id]: https://msdn.microsoft.com/library/ms187942.aspx
[varbinary]: https://msdn.microsoft.com/library/ms188362.aspx
[varchar]: https://msdn.microsoft.com/library/ms186939.aspx
[XML]: https://msdn.microsoft.com/library/ms187339.aspx
[Brugerdefinerede typer]: https://msdn.microsoft.com/library/ms131694.aspx
