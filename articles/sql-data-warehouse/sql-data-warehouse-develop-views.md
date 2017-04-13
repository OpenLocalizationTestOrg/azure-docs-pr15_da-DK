<properties
   pageTitle="Visninger i SQL datawarehouse | Microsoft Azure"
   description="Tip til brug af Transact-SQL-visninger i Azure SQL Data Warehouse til udvikling af løsninger."
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
   ms.date="07/01/2016"
   ms.author="jrj;barbkess;sonyama"/>


# <a name="views-in-sql-data-warehouse"></a>Visninger i SQL datawarehouse

Visninger er særligt nyttige i SQL Data Warehouse. De kan bruges i en række forskellige måder at forbedre kvaliteten af din løsning.  I denne artikel fremhæves nogle få eksempler på, hvordan du kan forbedre din løsning med visninger, samt de begrænsninger, der skal betragtes som.

> [AZURE.NOTE] Syntaksen for `CREATE VIEW` gennemgås ikke i denne artikel. Se [CREATE VIEW][] artikel på MSDN oplysningerne reference.

## <a name="architectural-abstraction"></a>Arkitektonisk fremstilling
En meget almindelig programmet mønster er at genskabe tabeller ved hjælp af Opret tabel som Vælg (CTAS) efterfulgt af et objekt, omdøbe mønster, samtidig med indlæsning af data.

Eksemplet herunder tilføjer nye dato poster til en datodimension. Bemærk, hvordan en ny tabble, DimDate_New, oprettes først og derefter omdøbt for at erstatte den oprindelige version af tabellen.

```sql
CREATE TABLE dbo.DimDate_New
WITH (DISTRIBUTION = ROUND_ROBIN
, CLUSTERED INDEX (DateKey ASC)
)
AS
SELECT *
FROM   dbo.DimDate  AS prod
UNION ALL
SELECT *
FROM   dbo.DimDate_stg AS stg
;

RENAME OBJECT DimDate TO DimDate_Old;
RENAME OBJECT DimDate_New TO DimDate;

```

Denne metode kan dog resultere i tabeller, der vises, og at forsvinde fra en brugers visning samt "tabellen findes ikke" fejlmeddelelser. Visninger kan bruges til at give brugere med et ensartet præsentation lag, idet de underliggende objekter er omdøbt. Ved at give brugerne adgang til data via en visninger, betyder, at brugere ikke vil have synligheden af de underliggende tabeller. Dette giver en ensartet brugeroplevelse, mens at sikre, at dataene lager designere kan udvikle datamodellen og maksimere ydeevne ved hjælp af CTAS under indlæsning af processen dataene.    

## <a name="performance-optimization"></a>Optimering af ydeevne
Visninger kan også bruges for at gennemtvinge ydeevne optimeret joinforbindelser mellem tabeller. En visning kan for eksempel inkorporere en overflødige fordelingsnøgle som en del af tilslutter kriterier for at minimere flytning af data.  En anden fordel af en visning kan være tvinge en specifik forespørgsel eller tilslutter tip. Brug af visninger på denne måde garanterer joinforbindelser udføres altid på en optimal måde undgå brug af brugerne kan huske den korrekte konstruktion for deres joinforbindelser.

## <a name="limitations"></a>Begrænsninger
Visninger i SQL Data Warehouse er kun metadata.  Følgende indstillinger er derfor ikke tilgængelige:

-   Der findes ingen skema binding indstilling
-   Grundlæggende tabeller kan ikke opdateres via visningen
-   Kan ikke oprettes visninger over midlertidige tabeller
-   Der er ingen understøttelse af Udvid / NOEXPAND tip
-   Der er ingen indekserede visninger i SQL Data Warehouse


## <a name="next-steps"></a>Næste trin
Du kan finde flere tip til udvikling, [SQL Data Warehouse udvikling oversigt][].
For `CREATE VIEW` syntaksen se [Opret visning][].

<!--Image references-->

<!--Article references-->
[Oversigt over SQL Data Warehouse udvikling]: ./sql-data-warehouse-overview-develop.md

<!--MSDN references-->
[OPRET VISNING]: https://msdn.microsoft.com/en-us/library/ms187956.aspx

<!--Other Web references-->
