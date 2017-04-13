<properties
   pageTitle="Transaktioner i SQL datawarehouse | Microsoft Azure"
   description="Tip til brug af transaktioner i Azure SQL Data Warehouse til udvikling af løsninger."
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
   ms.date="07/31/2016"
   ms.author="jrj;barbkess;sonyama"/>

# <a name="transactions-in-sql-data-warehouse"></a>Transaktioner i SQL datawarehouse

Som du forventer, understøtter SQL Data Warehouse transaktioner som en del af data warehouse arbejdsbelastningen. For at sikre ydeevnen for SQL Data Warehouse vedligeholdes skaleres er nogle funktioner dog begrænset sammenlignet med SQL Server. I denne artikel fremhæves forskellene og viser en liste over de andre. 

## <a name="transaction-isolation-levels"></a>Isolationsniveauer
SQL Data Warehouse implementerer SURT transaktioner. Isolation af fra transaktions support er dog begrænset til `READ UNCOMMITTED` og det kan ikke ændres. Du kan implementere et antal kodningssprog metoder til at forhindre snavset læser data, hvis dette er et problem for dig. De mest populære metoder udnytte både CTAS og tabel partition skifte (ofte kaldet skyderen vinduet mønstret) for at forhindre brugere i at oprette forespørgsler til data, der er stadig være forberedt. Visninger, der allerede filtrerer dataene, er også en populære tilgang.  

## <a name="transaction-size"></a>Transaktionsstørrelse
En enkelt data ændring transaktion er begrænset størrelse. Grænsen på anvendes i dag "per fordeling". Den samlede fordeling kan derfor beregnes ved at gange grænsen med fordeling antallet. Til tilnærmet det maksimale antal rækker i posteringen dividere kappen fordeling med den samlede størrelse af hver række. Overvej at tage et gennemsnit kolonnelængde i stedet for at bruge den maksimale størrelse for variabel længde kolonner.

Der er foretaget i tabellen nedenfor følgende forudsætninger:

* Der opstod en lige fordelingen af data 
* Den gennemsnitlige række længde er 250 byte

| [DWU][]    | Afslutter per fordeling (GiB) | Antallet af fordeling | Maks transaktionsstørrelse (GiB) | # Rækker per fordeling | Det maksimale antal rækker hver transaktion |
| ------ | -------------------------- | ----------------------- | -------------------------- | ----------------------- | ------------------------ |
| DW100  |  1                         | 60                      |   60                       |   4,000,000             |    240,000,000           |
| DW200  |  1,5                       | 60                      |   90                       |   6,000,000             |    360,000,000           |
| DW300  |  2.25                      | 60                      |  135                       |   9,000,000             |    540,000,000           |
| DW400  |  3                         | 60                      |  180                       |  12,000,000             |    720,000,000           |
| DW500  |  3,75                      | 60                      |  225                       |  15.000.000             |    900,000,000           |
| DW600  |  4.5                       | 60                      |  270                       |  18,000,000             |  1,080,000,000           |
| DW1000 |  7.5                       | 60                      |  450                       |  30,000,000             |  1,800,000,000           |
| DW1200 |  9                         | 60                      |  540                       |  36,000,000             |  2,160,000,000           |
| DW1500 | 11,25                      | 60                      |  675                       |  45,000,000             |  2,700,000,000           |
| DW2000 | 15                         | 60                      |  900                       |  60.000.000             |  3,600,000,000           |
| DW3000 | 22,5                       | 60                      |  1,350                     |  90,000,000             |  5,400,000,000           |
| DW6000 | 45                         | 60                      |  2,700                     | 180,000,000             | 10,800,000,000           |

Størrelsesgrænsen transaktion anvendes hver transaktion eller handling. Den anvendes ikke på tværs af alle samtidige transaktioner. Derfor er hver transaktion tilladt at skrive denne mængde data i logfilen. 

Du kan optimere og minimere mængden data, der er skrevet til loggen, se artiklen [transaktioner bedste fremgangsmåder][] .

> [AZURE.WARNING] Den maksimale transaktionsstørrelse kan kun opnås for HASH eller ROUND_ROBIN distribueret tabeller, hvor spredningen af dataene er lige. Hvis posteringen skriver data i en skæv måde til fordelingerne er grænsen sandsynligvis ringes før den maksimale transaktionsstørrelse.
<!--REPLICATED_TABLE-->

## <a name="transaction-state"></a>Tilstanden for posteringen
SQL Data Warehouse bruger funktionen XACT_STATE() til at rapportere en mislykkedes transaktion med værdien -2. Det betyder, at posteringen mislykkes og er markeret med henblik rollback kun

> [AZURE.NOTE] Brug af-2 af funktionen XACT_STATE til angivelse af en mislykket transaktion repræsenterer forskellige funktionsmåde til SQL Server. SQL Server bruger værdien -1 til at repræsentere en ophævelse committable transaktion. SQL Server kan acceptere nogle fejl i en transaktion uden at skulle være markeret som ophævelse committable. For eksempel `SELECT 1/0` ville medfører en fejl, men ikke tvinge en transaktion i en ophævelse committable tilstand. SQL Server giver også læser i ophævelse committable transaktion. Dog kan SQL Data Warehouse du ikke gøre dette. Hvis der opstår fejl i en SQL Data Warehouse transaktion den vil automatisk blive indsat tilstanden-2, og du vil ikke kunne gøre alle yderligere Vælg sætninger, indtil sætningen er blevet annulleret. Det er derfor vigtigt at kontrollere, at din programmet kode for at se, hvis den bruger XACT_STATE(), som du kan være nødvendigt at foretage ændringer af kode.

I SQL Server kan du se en post, der ser sådan ud:

```sql
SET NOCOUNT ON;
DECLARE @xact_state smallint = 0;

BEGIN TRAN
    BEGIN TRY
        DECLARE @i INT;
        SET     @i = CONVERT(INT,'ABC');
    END TRY
    BEGIN CATCH
        SET @xact_state = XACT_STATE();

        SELECT  ERROR_NUMBER()    AS ErrNumber
        ,       ERROR_SEVERITY()  AS ErrSeverity
        ,       ERROR_STATE()     AS ErrState
        ,       ERROR_PROCEDURE() AS ErrProcedure
        ,       ERROR_MESSAGE()   AS ErrMessage
        ;
        
        IF @@TRANCOUNT > 0
        BEGIN
            PRINT 'ROLLBACK';
            ROLLBACK TRAN;
        END

    END CATCH;

IF @@TRANCOUNT >0
BEGIN
    PRINT 'COMMIT';
    COMMIT TRAN;
END

SELECT @xact_state AS TransactionState;
```

Hvis du lader din kode, som er den placeret over derefter får du vist følgende fejlmeddelelse:

Meddelelse 111233, Level 16, tilstand 1, linje 1 111233; Den aktuelle transaktion har afbrudt, og ventende ændringer er blevet annulleret. Årsag: En transaktion i tilstanden kun rollback blev ikke eksplicit annulleret før en DDL, DML eller SELECT-sætning.

Du får også output af funktionerne ERROR_ * ikke.

I SQL Data Warehouse koden, skal være ændret en smule:

```sql
SET NOCOUNT ON;
DECLARE @xact_state smallint = 0;

BEGIN TRAN
    BEGIN TRY
        DECLARE @i INT;
        SET     @i = CONVERT(INT,'ABC');
    END TRY
    BEGIN CATCH
        SET @xact_state = XACT_STATE();
        
        IF @@TRANCOUNT > 0
        BEGIN
            PRINT 'ROLLBACK';
            ROLLBACK TRAN;
        END

        SELECT  ERROR_NUMBER()    AS ErrNumber
        ,       ERROR_SEVERITY()  AS ErrSeverity
        ,       ERROR_STATE()     AS ErrState
        ,       ERROR_PROCEDURE() AS ErrProcedure
        ,       ERROR_MESSAGE()   AS ErrMessage
        ;
    END CATCH;

IF @@TRANCOUNT >0
BEGIN
    PRINT 'COMMIT';
    COMMIT TRAN;
END

SELECT @xact_state AS TransactionState;
```

Den forventede funktionsmåde nu overholdes. Fejlen i posteringen administreres og funktionerne ERROR_ * indeholder værdier, som forventet.

Alle, der er blevet ændret er, at den `ROLLBACK` for posteringen havde til at opstå, før læsning af oplysninger om fejlen i den `CATCH` Bloker.

## <a name="errorline-function"></a>Funktionen Error_Line()
Det er også værd at bemærke, at SQL Data Warehouse ikke implementere eller understøtter funktionen ERROR_LINE(). Hvis du har i din kode, skal du fjerne den for at er kompatibel med SQL Data Warehouse. I stedet bruge forespørgsel etiketter i din kode til at implementere tilsvarende funktionalitet. Se [etiket][] artiklen for at få flere oplysninger om denne funktion.

## <a name="using-throw-and-raiserror"></a>Brug af KASTE og RAISERROR
KASTE er mere moderne implementering for at slå undtagelser i SQL Data Warehouse, men RAISERROR er også understøttet. Der er nogle forskelle, der er værd at betale opmærksomheden på dog.

- Brugerdefinerede fejlmeddelelser tal kan være i området 100.000 150.000 for KASTE
- RAISERROR fejlmeddelelser løses på 50000
- Brug af sys.messages understøttes ikke

## <a name="limitiations"></a>Limitiations
SQL Data Warehouse har nogle andre begrænsninger, der vedrører transaktioner.

De er som følger:

- Ingen distribuerede transaktioner
- Ingen indlejrede transaktioner, der er tilladt
- Ingen Gem punkter, der er tilladt
- Ingen navngivne transaktioner
- Ingen markeret transaktioner
- Ingen understøttelse af DDL som `CREATE TABLE` defineret transaktion i en bruger

## <a name="next-steps"></a>Næste trin
Se [transaktioner bedste fremgangsmåder][]for at få mere for at vide om optimering af transaktioner.  I [SQL Data Warehouse bedste fremgangsmåder][]for at få mere for at vide om andre bedste fremgangsmåder for SQL Data Warehouse.

<!--Image references-->

<!--Article references-->
[DWU]: ./sql-data-warehouse-overview-what-is.md#data-warehouse-units
[development overview]: ./sql-data-warehouse-overview-develop.md
[Transaktioner bedste fremgangsmåder]: ./sql-data-warehouse-develop-best-practices-transactions.md
[SQL Data Warehouse bedste fremgangsmåder]: ./sql-data-warehouse-best-practices.md
[ETIKET]: ./sql-data-warehouse-develop-label.md

<!--MSDN references-->

<!--Other Web references-->
