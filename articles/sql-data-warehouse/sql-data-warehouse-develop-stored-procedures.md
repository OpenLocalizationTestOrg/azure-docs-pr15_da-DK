<properties
   pageTitle="Lagrede procedurer i SQL Data Warehouse | Microsoft Azure"
   description="Tip til brug af lagrede procedurer i Azure SQL Data Warehouse til udvikling af løsninger."
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
   ms.date="06/30/2016"
   ms.author="jrj;barbkess;sonyama"/>

# <a name="stored-procedures-in-sql-data-warehouse"></a>Lagrede procedurer i SQL Data Warehouse

SQL Data Warehouse understøtter mange af de Transact-SQL-funktioner, der findes i SQL Server. Der er vigtigere skala bestemte funktioner, som vi vil udnytte for at maksimere ydeevnen for din løsning.

Hvis du vil bevare er skalering og ydeevnen af SQL Data Warehouse der dog også nogle funktioner og funktionalitet, som har funktionsmæssige forskelle og andre, der ikke understøttes.

I denne artikel beskrives det, hvordan du kan implementere lagrede procedurer i SQL Data Warehouse.

## <a name="introducing-stored-procedures"></a>Introduktion til lagrede procedurer
Lagrede procedurer er en god metode til indlejring af SQL-koden; gemme den tæt på dine data i et datalager. Ved encapsulating koden i lettere håndterbare enheder hjælpe lagrede procedurer udviklere med modularize deres løsninger lette større genbrug af kode. Hver enkelt lagret procedure kan også acceptere parametre for at gøre dem endnu mere fleksible.

SQL Data Warehouse indeholder en forenklet og strømlinet lagret procedure implementering. Største forskellen sammenlignet med SQL Server er, at den lagrede procedure ikke er udfyldt kompilerede kode. Vi arbejder generelt mindre pågældende med kompilering tiden på lagre data. Det er mere vigtigt, at den lagrede procedurekode korrekt optimeret, når operativsystem mod store datamængder. Målet er at gemme timer, minutter og sekunder ikke millisekunder. Det er derfor mere praktisk at betragte lagrede procedurer som objektbeholdere for SQL-logik.     

Når SQL Data Warehouse udfører den lagrede procedure parses SQL-sætningerne, oversatte og optimeret på kørselstidspunktet. Under denne proces konverteres hver sætning til fordelt forespørgsler. SQL-koden, der faktisk køres i forhold til dataene, der er anderledes til forespørgslen sendes.

## <a name="nesting-stored-procedures"></a>Indlejring gemt procedurer
Når lagrede procedurer ringe til andre lagrede procedurer eller udføre dynamisk sql, og klik derefter på indre lagrede procedure eller kode aktivering siges at være indlejret.

SQL Data Warehouse understøtter maksimalt 8 indlejringsniveauer. Dette er en smule anderledes til SQL Server. Indlejre niveau i SQL Server er 32.

Øverste niveau lagret procedure opkaldet passer for at indlejre niveau 1

```sql
EXEC prc_nesting
```
Hvis den lagrede procedure giver også en anden EKSEKVERBAR ringe vil dette øge niveauet indlejre til 2
```sql
CREATE PROCEDURE prc_nesting
AS
EXEC prc_nesting_2  -- This call is nest level 2
GO
EXEC prc_nesting
```
Hvis den anden procedure udfører nogle dynamisk sql vil dette øge niveauet indlejre til 3
```sql
CREATE PROCEDURE prc_nesting_2
AS
EXEC sp_executesql 'SELECT 'another nest level'  -- This call is nest level 2
GO
EXEC prc_nesting
```

Note SQL Data Warehouse understøtter ikke i øjeblikket @@NESTLEVEL. Du skal holde en styr på dine indlejre niveau dig selv. Det sandsynligvis, du vil ramme grænsen 8 indlejre niveau, men hvis du ikke gør du skal igen arbejde med din kode og "udjævne" det så det passer ind i denne grænse.

## <a name="insertexecute"></a>INDSÆT.. UDFØRE
SQL Data Warehouse tillader ikke, at du kan bruge resultatsættet af en lagret procedure med en INSERT-sætning. Der findes en alternativ metode, du kan bruge.

Se følgende artikel på [midlertidige tabeller] til et eksempel på, hvordan du gør dette.

## <a name="limitations"></a>Begrænsninger

Der er nogle aspekter af Transact-SQL-lagrede procedurer, som ikke er implementeret i SQL Data Warehouse.

De er:

- midlertidigt lagrede procedurer
- opstilling med tal eller lagrede procedurer
- udvidede lagrede procedurer
- CLR gemt procedurer
- krypteringsindstilling for
- indstillingen gentagelse
- tabelværdier parametre
- skrivebeskyttet parametre
- standardparametre
- udførelse af kontekster
- returnere sætning

## <a name="next-steps"></a>Næste trin
Du kan finde flere tip til udvikling, [udvikling oversigt][].

<!--Image references-->

<!--Article references-->
[midlertidige tabeller]: ./sql-data-warehouse-tables-temporary.md#modularizing-code
[Oversigt over udvikling]: ./sql-data-warehouse-overview-develop.md

<!--MSDN references-->
[nest level]: https://msdn.microsoft.com/library/ms187371.aspx

<!--Other Web references-->
