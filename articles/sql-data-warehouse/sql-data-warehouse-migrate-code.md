<properties
   pageTitle="Overføre dine SQL-koden til SQL Data Warehouse | Microsoft Azure"
   description="Tip til at overføre dine SQL-koden til Azure SQL Data Warehouse til udvikling af løsninger."
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
   ms.date="08/02/2016"
   ms.author="lodipalm;barbkess;sonyama;jrj"/>

# <a name="migrate-your-sql-code-to-sql-data-warehouse"></a>Overføre dine SQL-koden til SQL Data Warehouse

Når du overfører din kode fra en anden database til SQL Data Warehouse, skal du sandsynligvis at foretage ændringer i din kodebase. Nogle SQL Data Warehouse funktioner kan forbedre ydeevnen betydeligt, som de er udviklet til at arbejde i en fordelt måde. Men hvis du vil bevare ydeevne og skalering, nogle funktioner er også ikke tilgængelige.

## <a name="common-t-sql-limitations"></a>Almindelige T-SQL-begrænsninger

Følgende liste indeholder en oversigt over de mest almindelige funktion, der ikke understøttes i Azure SQL Data Warehouse. Hyperlinks kommer du til løsninger på ikke-understøttede funktioner:

- [ANSI joinforbindelser opdateringer][]
- [ANSI joinforbindelser på sletter][]
- [Flet-sætning][]
- joinforbindelser i tværs-database
- [markører][]
- [VÆLG.. I][]
- [INDSÆT.. EKSEKVERBAR][]
- output-delsætning
- indbyggede brugerdefinerede funktioner
- med flere sætninger funktioner
- [almindelige tabeludtryk](#Common-table-expressions)
- [rekursiv almindelige tabeludtryk (CTE)] (#Recursive-common-table-expressions-(CTE)
- CLR-funktioner og procedurer
- funktionen $partition
- tabelvariabler til
- tabel værdi parametre
- distribuerede transaktioner
- Anvend / rollback arbejde
- gemme transaktions
- udførelse af kontekster (EXECUTE AS)
- [delsætningen med rollup group by / kube / sæt grupperingsindstillinger][]
- [indlejringsniveauer ud over 8][]
- [opdatere gennem visninger][]
- [Brug af Vælg for variable tildeling][]
- [ingen Maks datatype til dynamisk SQL-strenge][]

Heldigvis kan de fleste af disse begrænsninger arbejde omkring. Forklaringer er angivet i de relevante udvikling artikler, der er nævnt ovenfor.

## <a name="supported-cte-features"></a>Understøttede CTE funktioner

Almindelige tabeludtryk (CTEs) er delvist understøttet i SQL Data Warehouse.  Følgende CTE funktioner understøttes i øjeblikket:

- En CTE kan angives i en SELECT-sætning.
- En CTE kan angives i en sætningen CREATE VIEW.
- En CTE kan angives i en sætning oprette tabel som Vælg (CTAS).
- En CTE kan angives i en sætning oprette REMOTE tabel som Vælg (CRTAS).
- En CTE kan angives i en sætning oprette eksterne tabel som Vælg (CETAS).
- Kan det referere til en ekstern tabel fra en CTE.
- Kan det referere til en ekstern tabel fra en CTE.
- Flere CTE forespørgselsdefinitioner kan defineres i en CTE.

## <a name="cte-limitations"></a>CTE begrænsninger

Almindelige tabeludtryk har nogle begrænsninger i SQL Data Warehouse, herunder:

- En CTE skal efterfølges af en enkelt SELECT-sætning. Indsæt, opdatere, slette, og FLET sætninger understøttes ikke.
- Et almindelige tabeludtryk, der indeholder referencer til sig selv (en rekursiv almindelige tabeludtryk) understøttes ikke (se nedenfor afsnit).
- Når du angiver mere end ét med delsætning i en CTE er ikke tilladt. Eksempelvis hvis en CTE_query_definition indeholder en underforespørgsel, underforespørgslen må ikke indeholde en indlejret med-delsætning, der definerer en anden CTE.
- Delsætningen ORDER BY kan ikke bruges i CTE_query_definition, undtagen, når der er angivet en TOP-delsætning.
- Når en CTE bruges i en sætning, der er en del af en gruppe, skal sætningen, før det efterfølges af semikolon.
- Når den bruges i sætninger, der er udarbejdet af sp_prepare, fungerer CTEs på samme måde som andre SELECT-sætninger i PDW. Men hvis CTEs bruges som en del af CETAS udarbejdet af sp_prepare, funktionsmåden kan udskyde fra SQL Server og andre PDW sætninger på grund af den måde, som er implementeret binding for sp_prepare. Hvis valg, referencer CTE bruger en forkert kolonne, der ikke findes i CTE, sp_prepare overfører uden registrering af fejlen, men fejlen vil være udløst under sp_execute i stedet.

## <a name="recursive-ctes"></a>Rekursive CTEs

Rekursive CTEs understøttes ikke i SQL Data Warehouse.  Migraion af rekursiv CTE kan være noget fuldført og den bedste metode er at opdele den i flere trin. Du kan typisk brug en løkke og udfylde en midlertidig tabel, som du forbedrer over rekursiv midlertidig forespørgsler. Når den midlertidige tabel udfyldes kan du derefter returnere dataene som et enkelt resultatsæt. En lignende fremgangsmåde er blevet brugt til at løse `GROUP BY WITH CUBE` i [delsætningen med rollup group by / kube / sæt grupperingsindstillinger][] artiklen.

## <a name="unsupported-system-functions"></a>Ikke-understøttede systemfunktioner

Der er også nogle systemfunktioner, der ikke understøttes. Nogle af primære dem du kan finde typisk bruges i data opbevaring er:

- NEWSEQUENTIALID()
- @@NESTLEVEL()
- @@IDENTITY()
- @@ROWCOUNT()
- ROWCOUNT_BIG
- ERROR_LINE()

Nogle af disse problemer kan arbejde omkring.

## <a name="rowcount-workaround"></a>@@ROWCOUNTløsning

Du kan arbejde omkring manglende understøttelse af @@ROWCOUNT, Opret en lagret procedure, der henter den sidste rækkeantal fra sys.dm_pdw_request_steps og derefter udføre `EXEC LastRowCount` efter en DML-sætning.

```sql
CREATE PROCEDURE LastRowCount AS
WITH LastRequest as 
(   SELECT TOP 1    request_id
    FROM            sys.dm_pdw_exec_requests
    WHERE           session_id = SESSION_ID()
    AND             resource_class IS NOT NULL
    ORDER BY end_time DESC
),
LastRequestRowCounts as
(
    SELECT  step_index, row_count
    FROM    sys.dm_pdw_request_steps
    WHERE   row_count >= 0
    AND     request_id IN (SELECT request_id from LastRequest)
)
SELECT TOP 1 row_count FROM LastRequestRowCounts ORDER BY step_index DESC
;
```

## <a name="next-steps"></a>Næste trin
En komplet liste over alle understøttede T-SQL-sætninger, se [emner i Transact-SQL][].

<!--Image references-->

<!--Article references-->
[ANSI joinforbindelser opdateringer]: ./sql-data-warehouse-develop-ctas.md#ansi-join-replacement-for-update-statements
[ANSI joinforbindelser på sletter]: ./sql-data-warehouse-develop-ctas.md#ansi-join-replacement-for-delete-statements
[Flet-sætning]: ./sql-data-warehouse-develop-ctas.md#replace-merge-statements
[INDSÆT.. EKSEKVERBAR]: ./sql-data-warehouse-tables-temporary.md#modularizing-code
[Emner i Transact-SQL]: ./sql-data-warehouse-reference-tsql-statements.md

[markører]: ./sql-data-warehouse-develop-loops.md
[VÆLG.. I]: ./sql-data-warehouse-develop-ctas.md#selectinto
[delsætningen med rollup group by / kube / sæt grupperingsindstillinger]: ./sql-data-warehouse-develop-group-by-options.md
[indlejringsniveauer ud over 8]: ./sql-data-warehouse-develop-transactions.md
[opdatere gennem visninger]: ./sql-data-warehouse-develop-views.md
[Brug af Vælg for variable tildeling]: ./sql-data-warehouse-develop-variable-assignment.md
[ingen Maks datatype til dynamisk SQL-strenge]: ./sql-data-warehouse-develop-dynamic-sql.md

<!--MSDN references-->

<!--Other Web references-->
