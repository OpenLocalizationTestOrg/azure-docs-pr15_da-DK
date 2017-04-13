<properties
    pageTitle="I hukommelsen OLTP forbedrer SQL txn performance | Microsoft Azure"
    description="Vedtager i hukommelsen OLTP til at forbedre transaktions ydeevne i en eksisterende SQL-database."
    services="sql-database"
    documentationCenter=""
    authors="jodebrui"
    manager="jhubbard"
    editor="MightyPen"/>


<tags
    ms.service="sql-database"
    ms.workload="data-management"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/03/2016"
    ms.author="jodebrui"/>


# <a name="use-in-memory-oltp-preview-to-improve-your-application-performance-in-sql-database"></a>Brug i hukommelsen OLTP (preview) til at forbedre din programmets ydeevne i SQL-Database

[I hukommelsen OLTP](sql-database-in-memory.md) kan bruges til at forbedre ydeevnen for OLTP arbejdsbelastningen i [Premium](sql-database-service-tiers.md) Azure SQL-databaser uden at øge ydeevnen.

Følg disse trin for at indføre i hukommelsen OLTP i din eksisterende database.

## <a name="step-1-ensure-your-premium-database-supports-in-memory-oltp"></a>Trin 1: Kontrollér databasen Premium understøtter i hukommelsen OLTP

Premium databaser, der er oprettet i November 2015 eller nyere understøtter funktionen i hukommelse. Du kan kontrollere, om databasen Premium understøtter funktionen i hukommelse ved at køre følgende Transact-SQL-sætning. I hukommelsen understøttes, hvis det returnerede resultat er 1 (ikke 0):

```
SELECT DatabasePropertyEx(Db_Name(), 'IsXTPSupported');
```

*XTP* står for *Yderste transaktion behandling*

Hvis din eksisterende database skal flyttes til en ny version 12 Premium-database, kan du bruge en af følgende fremgangsmåder til at eksportere og derefter importere dine data.

#### <a name="export-steps"></a>Eksportere trin

Eksportere databasen fremstilling til en bacpac ved hjælp af enten:

- [Eksportere](sql-database-export.md) funktionaliteten i [portalen](https://portal.azure.com/).

- Funktionen **Eksportér Data lag programmet** i en [opdateret SSMS.exe](http://msdn.microsoft.com/library/mt238290.aspx) (SQL Server Management Studio).
 1. Udvid noden **databaser** i **Object Explorer**.
 2. Højreklik på din database node.
 3. Klik på **opgaver** > **eksportere Data lag programmet**.
 4. Betjene guidens vindue, der vises.


#### <a name="import-steps"></a>Importere trin

Importere bacpac til en ny Premium-database.

1. I Azure- [portal](https://portal.azure.com/)
 - Gå til serveren.
 - Vælg indstillingen [Indlæs Database](sql-database-import.md) .
 - Vælg en Premium priser niveau.

2. Bruge SSMS til at importere bacpac:
 - Højreklik på noden **databaser** i **Object Explorer**.
 - Klik på **Importér Data lag program**.
 - Betjene guidens vindue, der vises.


## <a name="step-2-identify-objects-to-migrate-to-in-memory-oltp"></a>Trin 2: Identificere objekter for at overføre til i hukommelsen OLTP

SSMS indeholder en **Oversigt over transaktion ydeevne Analysis** rapport, som du kan køre mod en database med en aktiv arbejdsbelastningen. Rapporten identificerer tabeller og lagrede procedurer, der er kandidater til overførsel til i hukommelsen OLTP.

I SSMS til at generere rapporten:
- Højreklik på din database node i **Object Explorer**.
- Klik på **rapporter** > **standardrapporter** > **transaktion oversigt over analyse af ydeevne**.

Du kan finde yderligere oplysninger finder [fastlægge, hvis en tabel eller gemt Procedure skal være overført til i hukommelsen OLTP](http://msdn.microsoft.com/library/dn205133.aspx).


## <a name="step-3-create-a-comparable-test-database"></a>Trin 3: Opret en tilsvarende testdatabase

Antag, at rapporten angiver databasen har en tabel, der ville få glæde af den er konverteret til en tabel, der er optimeret til hukommelse. Vi anbefaler, at du først afprøve for at bekræfte angivelse ved at teste.

Du har brug for en testkopi af databasen fremstilling. Test databasen skal være på det samme niveau for tjenesten niveau som fremstilling databasen.

For at lette test, at tilpasse databasen test på følgende måde:

1. Oprette forbindelse til testdatabase ved hjælp af SSMS.

2. Angiv databaseindstillingen, som vist i følgende T-SQL-sætning for at undgå, at du behøver indstillingen med (SNAPSHOT) i forespørgsler:
```
ALTER DATABASE CURRENT
    SET
        MEMORY_OPTIMIZED_ELEVATE_TO_SNAPSHOT = ON;
```


## <a name="step-4-migrate-tables"></a>Trin 4: Overføre tabeller

Du skal oprette og udfylde en hukommelse optimeret kopi af den tabel, du vil teste. Du kan oprette den ved hjælp af enten:

- Praktiske hukommelse optimering guiden i SSMS.
- Manuel T-SQL.


#### <a name="memory-optimization-wizard-in-ssms"></a>Hukommelse optimering guiden i SSMS

For at bruge denne indstilling for overførsel:

1. Oprette forbindelse til databasen test med SSMS.

2. Højreklik på tabellen i **Object Explorer**, og klik derefter på **Hukommelse optimering Advisor**.
 - Guiden **Tabel hukommelse redskab til optimering af Advisor** vises.

3. Klik på **validering af overførsel** (eller knappen **Næste** ) for at se, om tabellen har en ikke-understøttede funktioner, der ikke understøttes i hukommelse optimeret tabeller i guiden. Du kan finde flere oplysninger i:
 - *Hukommelse optimering tjekliste* i [Hukommelse optimering Advisor](http://msdn.microsoft.com/library/dn284308.aspx).
 - [Transact-SQL konstruktioner ikke understøttes af i hukommelsen OLTP](http://msdn.microsoft.com/library/dn246937.aspx).
 - [Skifte til i hukommelsen OLTP](http://msdn.microsoft.com/library/dn247639.aspx).

4. Hvis tabellen har ingen ikke-understøttede funktioner, kan advisor udføre faktisk skemaet og dataoverførsel for dig.


#### <a name="manual-t-sql"></a>Manuel T-SQL

For at bruge denne indstilling for overførsel:

1. Oprette forbindelse til databasen test ved hjælp af SSMS (eller en lignende utility).

2. Få den komplette T-SQL-script for tabellen og de tilhørende indeks.
 - Højreklik på din tabelnode i SSMS.
 - Klik på **Script tabel som** > **Opret for at** > **nye forespørgselsvinduet**.

3. I vinduet script tilføje med (MEMORY_OPTIMIZED = ON) til sætningen CREATE TABLE.

4. Hvis der er et GRUPPERET indeks, kan du ændre det til NONCLUSTERED.

5. Omdøbe den eksisterende tabel ved hjælp af SP_RENAME.

6. Oprette den nye hukommelse optimeret kopi af tabellen ved at køre scriptet redigerede CREATE TABLE.

7. Kopiér dataene til din hukommelse optimeret tabel ved hjælp af Indsæt... VÆLG * TIL:
    
```
INSERT INTO <new_memory_optimized_table>
        SELECT * FROM <old_disk_based_table>;
```


## <a name="step-5-optional-migrate-stored-procedures"></a>Trin 5 (valgfrit): overføre lagrede procedurer

Funktionen i hukommelse kan også redigere en lagret procedure for forbedret ydeevne.


### <a name="considerations-with-natively-compiled-stored-procedures"></a>Overvejelser i forbindelse med med oprindeligt kompileret lagrede procedurer

En indbygget kompileret lagret procedure skal have følgende indstillinger på dens T-SQL med-delsætning:

- NATIVE_COMPILATION

- SCHEMABINDING: betydning tabeller, den lagrede procedure ikke kan have deres kolonnedefinitioner af ændret på nogen måde, der kan påvirke den lagrede procedure, medmindre du slipper den lagrede procedure.


Et oprindelige modul skal bruge en stor [ATOMISK dokumentkomponenter](http://msdn.microsoft.com/library/dn452281.aspx) til håndtering af. Der er ingen rolle for en eksplicit BEGYNDER transaktion eller ROLLBACK transaktion. Hvis din kode registrerer en overtrædelse af en forretningsregel, kan det afslutte atomisk blokering med en [UDLØS](http://msdn.microsoft.com/library/ee677615.aspx) sætning.


### <a name="typical-create-procedure-for-natively-compiled"></a>Typisk CREATE PROCEDURE for indbygget kompileret

T-SQL til at oprette en indbygget kompileret lagret procedure er typisk svarende til følgende skabelon:

```
CREATE PROCEDURE schemaname.procedurename
    @param1 type1, …
    WITH NATIVE_COMPILATION, SCHEMABINDING
    AS
        BEGIN ATOMIC WITH
            (TRANSACTION ISOLATION LEVEL = SNAPSHOT,
            LANGUAGE = N'your_language__see_sys.languages'
            )
        …
        END;
```

- For TRANSACTION_ISOLATION_LEVEL er ØJEBLIKSBILLEDE den hyppigste værdi for de oprindeligt kompileret lagrede procedurer. Men et undersæt af de andre værdier understøttes også:
 - GENTAGET LÆSNING
 - SERIALISERBAR


- Værdien sprog skal være til stede i visningen sys.languages.


### <a name="how-to-migrate-a-stored-procedure"></a>Sådan overføres en lagret procedure

Trinnene for overførsel er:


1. Få CREATE PROCEDURE scriptet til den almindelige fortolket lagrede procedure.

2. Omskrivning dens sidehoved, så det svarer til den forrige skabelon.

3. Fastslå, om den lagrede procedure T-SQL-koden bruger de funktioner, der ikke understøttes for indbygget kompileret lagrede procedurer. Implementere løsninger, hvis det er nødvendigt.
 - Yderligere oplysninger finder du [Problemer med at overførsel oprindeligt kompileret lagrede procedurer](http://msdn.microsoft.com/library/dn296678.aspx).

4. Omdøb den gamle lagrede procedure ved hjælp af SP_RENAME. Eller du bare oprette.

5. Køre scriptet redigerede oprette PROCEDURE T-SQL.


## <a name="step-6-run-your-workload-in-test"></a>Trin 6: Køre arbejdsbelastningen i test

Køre en arbejdsbyrde i databasen test, der ligner arbejdsbelastningen, der kører i databasen fremstilling. Dette vil vise den forøgelse i ydeevnen opfyldes af brugen af funktionen i hukommelsen for tabeller og lagrede procedurer.

Vigtigste attributter for arbejdsbelastningen er:

- Antallet af samtidige forbindelser.

- Læse-og skriveadgang-bredde-forhold.


For at skræddersy og køre test arbejdsbelastningen, skal du overveje at bruge værktøjet praktiske ostress.exe, som vist i [her](sql-database-in-memory.md).


For at minimere netværksventetid skal du køre din test i den samme Azure geografiske område, hvor databasen findes.


## <a name="step-7-post-implementation-monitoring"></a>Trin 7: Efter implementering overvågning

Overveje at overvåge ydeevnen virkningerne af din i hukommelse installationer fremstilling:

- [Overvåge Hukommelseslager](sql-database-in-memory-oltp-monitoring.md).

- [Overvågning Azure SQL-Database ved hjælp af dynamiske management visninger](sql-database-monitoring-with-dmvs.md)


## <a name="related-links"></a>Relaterede links

- [Hukommelse OLTP (i hukommelsen optimering)](http://msdn.microsoft.com/library/dn133186.aspx)

- [Introduktion til oprindeligt kompileret lagrede procedurer](http://msdn.microsoft.com/library/dn133184.aspx)

- [Hukommelse optimering Advisor](http://msdn.microsoft.com/library/dn284308.aspx)

