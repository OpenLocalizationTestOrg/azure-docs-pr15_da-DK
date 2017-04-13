<properties
   pageTitle="Tildele variabler i SQL Data Warehouse | Microsoft Azure"
   description="Tip til at tildele Transact-SQL variabler i Azure SQL Data Warehouse til udvikling af løsninger."
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

# <a name="assign-variables-in-sql-data-warehouse"></a>Tildele variabler i SQL Data Warehouse
Variabler i SQL Data Warehouse angives ved hjælp af den `DECLARE` sætning eller det `SET` sætning.

Alle af følgende er helt korrekt måder til at angive en variabel værdi:

## <a name="setting-variables-with-declare"></a>Angive variabler med DECLARE

Initialisering af variabler med DECLARE er et af de mest fleksible måder at angive en variabel værdi i SQL Data Warehouse.

```sql
DECLARE @v  int = 0
;
```

Du kan også bruge DECLARE til at angive mere end én variabel ad gangen. Du kan ikke bruge `SELECT` eller `UPDATE` at gøre dette:

```sql
DECLARE @v  INT = (SELECT TOP 1 c_customer_sk FROM Customer where c_last_name = 'Smith')
,       @v1 INT = (SELECT TOP 1 c_customer_sk FROM Customer where c_last_name = 'Jones')
;
```

Du kan ikke initialisere og bruger en variabel i samme DECLARE-sætningen. Til at illustrere punkt i eksemplet nedenfor er **ikke** tilladt som @p1 er både initialiseret og bruges i den samme sætning. Dette medfører en fejl.

```sql
DECLARE @p1 int = 0
,       @p2 int = (SELECT COUNT (*) FROM sys.types where is_user_defined = @p1 )
;
```

## <a name="setting-values-with-set"></a>Indstille værdier med sæt
Sæt er en meget almindelig metode til at angive en enkelt variabel.

Alle eksemplerne nedenfor er gyldig måder at indstille en variabel med sæt:

```sql
SET     @v = (Select max(database_id) from sys.databases);
SET     @v = 1;
SET     @v = @v+1;
SET     @v +=1;
```

Du kan kun angive én variabel ad gangen med sæt. Men, som kan ses ovenfor er sammensat operatorer tilladte.

## <a name="limitations"></a>Begrænsninger
Du kan ikke bruge Vælg eller opdatere for variable tildeling.


## <a name="next-steps"></a>Næste trin
Du kan finde flere tip til udvikling, [udvikling oversigt][].

<!--Image references-->

<!--Article references-->
[Oversigt over udvikling]: sql-data-warehouse-overview-develop.md

<!--MSDN references-->

<!--Other Web references-->
