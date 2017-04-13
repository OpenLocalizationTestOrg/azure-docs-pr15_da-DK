<properties
   pageTitle="Bruge etiketter til instrument forespørgsler i SQL Data Warehouse | Microsoft Azure"
   description="Tip til brug af navne til instrument forespørgsler i Azure SQL Data Warehouse til udvikling af løsninger."
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

# <a name="use-labels-to-instrument-queries-in-sql-data-warehouse"></a>Bruge etiketter til instrument forespørgsler i SQL Data Warehouse
SQL Data Warehouse understøtter en som repræsenterer kaldet forespørgsel etiketter. Før du skifter til en hvilken som helst dybde Lad os se på et eksempel på et:

```sql
SELECT *
FROM sys.tables
OPTION (LABEL = 'My Query Label')
;
```

Den sidste linje koder strengen 'Min forespørgsel etiket' til forespørgslen. Dette er særligt nyttige, som etiketten er forespørgsel-kan gennem DMVs. Dette giver os en metode til at finde frem til problemet forespørgsler samt til at identificere status via en ETL Kør.

En god navngivningsregel kan virkelig her. For eksempel noget i stil ' projekt: PROCEDURE: SÆTNING: KOMMENTAR ' vil hjælpe med at identificere entydigt forespørgslen i blandt al kode i versionsstyring.

Du kan bruge følgende forespørgsel, der bruger visningerne dynamisk administration for at søge efter navn:

```sql
SELECT  *
FROM    sys.dm_pdw_exec_requests r
WHERE   r.[label] = 'My Query Label'
;
```

> [AZURE.NOTE] Det er vigtigt, at du ombryde kantede parenteser eller dobbelte anførselstegn omkring etiketten word, når forespørgsler. Etiket er et reserveret ord og vil forårsaget en fejl, hvis det ikke er afgrænset.


## <a name="next-steps"></a>Næste trin
Du kan finde flere tip til udvikling, [udvikling oversigt][].

<!--Image references-->

<!--Article references-->
[Oversigt over udvikling]: sql-data-warehouse-overview-develop.md

<!--MSDN references-->

<!--Other Web references-->
