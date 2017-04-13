<properties
   pageTitle="Dynamisk SQL i SQL datawarehouse | Microsoft Azure"
   description="Tip til brug af dynamisk SQL i Azure SQL Data Warehouse til udvikling af løsninger."
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

# <a name="dynamic-sql-in-sql-data-warehouse"></a>Dynamisk SQL i SQL datawarehouse
Når udvikling programkode til SQL Data Warehouse du muligvis bruge dynamisk sql til at levere fleksibel, generisk og modul løsninger. SQL Data Warehouse understøtter ikke blob-datatyper på nuværende tidspunkt. Dette kan begrænse størrelsen af din strenge, som blob typer omfatter både nvarchar(Max),varchar(Max) og nvarchar(max) typer. Hvis du har brugt disse filtyper i din programkode, når du opbygger en meget store strenge, skal du bryde koden i stykker og bruge sætningen EKSEKVERBAR i stedet.

Et simpelt eksempel:

```sql
DECLARE @sql_fragment1 VARCHAR(8000)=' SELECT name '
,       @sql_fragment2 VARCHAR(8000)=' FROM sys.system_views '
,       @sql_fragment3 VARCHAR(8000)=' WHERE name like ''%table%''';

EXEC( @sql_fragment1 + @sql_fragment2 + @sql_fragment3);
```

Hvis strengen er kort kan du bruge [sp_executesql][] som normalt.

> [AZURE.NOTE] Sætninger, der udføres som dynamisk SQL vil stadig være underlagt alle TSQL valideringsregler.

## <a name="next-steps"></a>Næste trin
Du kan finde flere tip til udvikling, [udvikling oversigt][].

<!--Image references-->

<!--Article references-->
[Oversigt over udvikling]: sql-data-warehouse-overview-develop.md

<!--MSDN references-->
[sp_executesql]: https://msdn.microsoft.com/library/ms188001.aspx

<!--Other Web references-->
