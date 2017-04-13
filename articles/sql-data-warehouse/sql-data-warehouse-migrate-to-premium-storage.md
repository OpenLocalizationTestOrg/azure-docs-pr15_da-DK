<properties
   pageTitle="Overføre din eksisterende Azure SQL Data Warehouse til premium-lager | Microsoft Azure"
   description="Vejledning til at overføre en eksisterende SQL Data Warehouse til premium-lager"
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="happynicolle"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="08/24/2016"
   ms.author="nicw;barbkess;sonyama"/>

# <a name="migration-to-premium-storage-details"></a>Overførsel til Premium lagerplads detaljer
SQL Data Warehouse introduceret senest [Premium lagerplads for større ydeevne forudsigelighed][].  Vi er nu klar til at overføre eksisterende Data lagre aktuelt på Standard lagerplads til Premium-lager.  Læs videre for at få flere oplysninger om, hvordan og hvornår der sker automatisk overførsler og hvordan du kan overføre selv hvis du foretrækker at styre, hvornår nedetiden forekommer.

Hvis du har mere end én Data Warehouse, kan du bruge [automatisk overførsel tidsplan][] nedenfor til at afgøre, hvornår det skal også overføres.

## <a name="determine-storage-type"></a>Se lagerplads type
Hvis du har oprettet en DW før datoerne nedenfor, bruger du aktuelt Standard lagerplads.  Hver Data Warehouse på Standard lagerplads, der er underlagt automatisk overførsel har en meddelelse om på øverst del af bladet Data Warehouse i [Azure-portalen][] , hvor der står "*en kommende opgraderingen til premium-lager kræver en afbrydelse.  Få mere at vide mere ->*. "

| **Område**          | **DW oprettet før denne dato**   |
| :------------------ | :-------------------------------- |
| Australien øst      | Premium lagerplads endnu ikke tilgængelig |
| Australien Sydøst | 5 august 2016                    |
| Brasilien syd        | 5 august 2016                    |
| Canada Central      | Maj 25 2016                      |
| Canada øst         | Maj 26 2016                      |
| Centrale USA          | Maj 26 2016                      |
| Kina øst          | Premium lagerplads endnu ikke tilgængelig |
| Kina nord         | Premium lagerplads endnu ikke tilgængelig |
| Østasien           | Maj 25 2016                      |
| Indtastning af østasiatiske USA             | Maj 26 2016                      |
| Indtastning af østasiatiske US2            | Maj 27 2016                      |
| Indien Central       | Maj 27 2016                      |
| Indien syd         | Maj 26 2016                      |
| Indien vest          | Premium lagerplads endnu ikke tilgængelig |
| Japan øst          | 5 august 2016                    |
| Japan vest          | Premium lagerplads endnu ikke tilgængelig |
| Nord centrale USA    | Premium lagerplads endnu ikke tilgængelig |
| Nord Europe        | 5 august 2016                    |
| Syd centrale USA    | Maj 27 2016                      |
| Sydøstasien      | Maj 24 2016                      |
| Vest Europe         | Maj 25 2016                      |
| Vest centrale USA     | August 26 2016                   |
| Vest USA             | Maj 26 2016                      |
| Vest US2            | August 26 2016                   |

## <a name="automatic-migration-details"></a>Automatisk overførsel detaljer
Som standard vil vi overføre databasen for dig under 6 pm og 6 am i dit område lokal tid under [automatisk overførsel tidsplan][] nedenfor.  Din eksisterende Data Warehouse bliver ubrugelig under overførslen.  Vi anslå, at overførslen tager omkring en time om TB storage per Data Warehouse.  Vi vil også sikre, at du ikke betale under en hvilken som helst del af den automatiske migrering.

> [AZURE.NOTE] Du vil ikke kunne bruge din eksisterende Data Warehouse under overførslen.  Når overførslen er fuldført, bliver dine Data Warehouse igen er online.

Nedenstående oplysninger er trin, Microsoft tager på dine vegne at gennemføre overflytningen og kræver ikke en hvilken som helst deltagelse fra din side.  Forestil dig, at din eksisterende DW på Standard lagerplads i øjeblikket har navnet "MyDW." i dette eksempel

1.  Microsoft omdøber "MyDW" til "MyDW_DO_NOT_USE_ [tidsstempel]"
2.  Microsoft afbrydes "MyDW_DO_NOT_USE_ [tidsstempel]."  I dette tidsrum tages en sikkerhedskopi.  Hvis vi støder på problemer under denne proces, kan du se flere pause/CV'er.
3.  Microsoft opretter en ny DW med navnet "MyDW" på Premium lagerplads fra sikkerhedskopien taget i trin 2.  "MyDW" vises ikke indtil, når gendannelsen er fuldført.
4.  Når gendannelsen er fuldført, returnerer "MyDW" til den samme DWUs og er stoppet midlertidigt eller aktive tilstand, den var, før overførslen.
5.  Når overførslen er fuldført, sletter Microsoft "MyDW_DO_NOT_USE_ [tidsstempel]"
    
> [AZURE.NOTE] Disse indstillinger heller ikke overført som en del af overførslen:
> 
>   -  Overvågning på niveauet for Database skal aktiveres igen
>   -  Firewallregler på niveauet for **Database** skal være tilføjes.  Firewallregler på **serverniveau** påvirkes ikke.

### <a name="automatic-migration-schedule"></a>Automatisk overførsel tidsplan
Automatisk overførsler sker fra 6 pm – 6 am (lokal tid per område) under følgende afbrydelse tidsplanen.

| **Område**          | **Anslået startdato**     | **Anslået slutdato**       |
| :------------------ | :--------------------------- | :--------------------------- |
| Australien øst      | Ikke fastsat endnu           | Ikke fastsat endnu           |
| Australien Sydøst | August 10 2016              | August 24 2016              |
| Brasilien syd        | August 10 2016              | August 24 2016              |
| Canada Central      | Juni 23 2016                | 1 juli 2016                 |
| Canada øst         | Juni 23 2016                | 1 juli 2016                 |
| Centrale USA          | Juni 23 2016                | 4 juli 2016                 |
| Kina øst          | Ikke fastsat endnu           | Ikke fastsat endnu           |
| Kina nord         | Ikke fastsat endnu           | Ikke fastsat endnu           |
| Østasien           | Juni 23 2016                | 1 juli 2016                 |
| Indtastning af østasiatiske USA             | Juni 23 2016                | Juli 11 2016                |
| Indtastning af østasiatiske US2            | Juni 23 2016                | Juli 8 2016                 |
| Indien Central       | Juni 23 2016                | 1 juli 2016                 |
| Indien syd         | Juni 23 2016                | 1 juli 2016                 |
| Indien vest          | Ikke fastsat endnu           | Ikke fastsat endnu           |
| Japan øst          | August 10 2016              | August 24 2016              |
| Japan vest          | Ikke fastsat endnu           | Ikke fastsat endnu           |
| Nord centrale USA    | Ikke fastsat endnu           | Ikke fastsat endnu           |
| Nord Europe        | August 10 2016              | August 31 2016              |
| Syd centrale USA    | Juni 23 2016                | 2 juli 2016                 |
| Sydøstasien      | Juni 23 2016                | 1 juli 2016                 |
| Vest Europe         | Juni 23 2016                | Juli 8 2016                 |
| Vest centrale USA     | August 14 2016              | August 31 2016              |
| Vest USA             | Juni 23 2016                | Juli 7 2016                 |
| Vest US2            | August 14 2016              | August 31 2016              |

## <a name="self-migration-to-premium-storage"></a>Selvstændig overførsel til Premium-lager
Hvis du vil have til at styre, hvornår din nedetid foretages, kan du bruge følgende trin til at overføre en eksisterende Data Warehouse på Standard lagerplads til Premium-lager.  Hvis du vælger at overføre præsentation, du skal fuldføre selvstændig overførslen, før automatiske overførslen begynder i det pågældende område for at undgå risikoen for automatisk overførslen medfører en konflikt (se [automatiske overførsel tidsplan][]).

### <a name="self-migration-instructions"></a>Selvstændig overførsel vejledning
Hvis du vil have til at styre din nedetid, kan du overføre dine Data Warehouse præsentation ved hjælp af sikkerhedskopiering/gendannelse.  Gendan del af overførslen forventes at tage omkring en time om TB storage per DW.  Hvis du vil beholde det samme navn, når overførslen er fuldført, skal du følge fremgangsmåden til [trin til at omdøbe under overflytningen][]. 

1.  [Afbryd][] din DW som tager en automatisk sikkerhedskopiering
2.  [Gendanne][] fra din seneste øjebliksbillede
3.  Slette din eksisterende DW på Standard lagerplads. **Hvis du ikke at udføre dette trin, skal du betale for begge DWs.**

> [AZURE.NOTE] Disse indstillinger heller ikke overført som en del af overførslen:
> 
>   -  Overvågning på niveauet for Database skal aktiveres igen
>   -  Firewallregler på niveauet for **Database** skal være tilføjes.  Firewallregler på **serverniveau** påvirkes ikke.

#### <a name="optional-steps-to-rename-during-migration"></a>Valgfrit: trin til at omdøbe under overførsel 
To databaser på den samme logiske server kan ikke have samme navn. SQL Data Warehouse understøtter nu mulighed for at omdøbe en DW.

Forestil dig, at din eksisterende DW på Standard lagerplads i øjeblikket har navnet "MyDW." i dette eksempel

1.  Omdøbe "MyDW" ved hjælp af kommandoen JUSTER DATABASE, som følger til noget "MyDW_BeforeMigration."  Denne kommando afbryder alle eksisterende transaktioner og skal udføres i den overordnede database kan udføres.
```
ALTER DATABASE CurrentDatabasename MODIFY NAME = NewDatabaseName;
```
2.  [Afbryd midlertidigt][] "MyDW_BeforeMigration", som tager en automatisk sikkerhedskopiering
3.  [Gendanne][] fra din seneste snapshot en ny database med det navn, du brugte til at have (ex: "MyDW")
4.  Slet "MyDW_BeforeMigration".  **Hvis du ikke at udføre dette trin, skal du betale for begge DWs.**

> [AZURE.NOTE] Disse indstillinger heller ikke overført som en del af overførslen:
> 
>   -  Overvågning på niveauet for Database skal aktiveres igen
>   -  Firewallregler på niveauet for **Database** skal være tilføjes.  Firewallregler på **serverniveau** påvirkes ikke.

## <a name="next-steps"></a>Næste trin
Vi har også øges antallet af blob-databasefiler i den underliggende arkitektur i dine Data Warehouse med Skift til Premium-lager.  For at maksimere ydeevnen fordelene ved denne ændring, anbefaler vi, at du genopbygger din grupperede Columnstore indeks ved hjælp af følgende script.  Nedenstående script virker ved at gennemtvinge nogle af dine eksisterende data til de ekstra BLOB.  Hvis du tager ingen handling, omfordele dataene naturligt over tid, som du indlæse flere data i dine Data Warehouse tabeller.

**Forudsætninger:**

1.  Datawarehouse bør kunne køre med 1.000 DWUs eller nyere (se [skala Beregn power][])
2.  Bruger afholde scriptet skal være i [mediumrc rolle][] eller højere
    1.  Hvis du vil tilføje en bruger til denne rolle, skal du udføre følgende: 
        1.  ````EXEC sp_addrolemember 'xlargerc', 'MyUser'````

````sql
-------------------------------------------------------------------------------
-- Step 1: Create Table to control Index Rebuild
-- Run as user in mediumrc or higher
--------------------------------------------------------------------------------
create table sql_statements
WITH (distribution = round_robin)
as select 
    'alter index all on ' + s.name + '.' + t.NAME + ' rebuild;' as statement,
    row_number() over (order by s.name, t.name) as sequence
from 
    sys.schemas s
    inner join sys.tables t
        on s.schema_id = t.schema_id
where
    is_external = 0
;
go
 
--------------------------------------------------------------------------------
-- Step 2: Execute Index Rebuilds.  If script fails, the below can be rerun to restart where last left off
-- Run as user in mediumrc or higher
--------------------------------------------------------------------------------

declare @nbr_statements int = (select count(*) from sql_statements)
declare @i int = 1
while(@i <= @nbr_statements)
begin
      declare @statement nvarchar(1000)= (select statement from sql_statements where sequence = @i)
      print cast(getdate() as nvarchar(1000)) + ' Executing... ' + @statement
      exec (@statement)
      delete from sql_statements where sequence = @i
      set @i += 1
end;
go
-------------------------------------------------------------------------------
-- Step 3: Cleanup Table Created in Step 1
--------------------------------------------------------------------------------
drop table sql_statements;
go
````

Hvis du støder på problemer med dine Data Warehouse, [oprette en supportbilletter][] og reference "Overførsel til Premium lagerplads" som mulig årsag.

<!--Image references-->

<!--Article references-->
[automatisk overførsel tidsplan]: #automatic-migration-schedule
[self-migration to Premium Storage]: #self-migration-to-premium-storage
[oprette en supportbilletter]: sql-data-warehouse-get-started-create-support-ticket.md
[Azure paired region]: best-practices-availability-paired-regions.md
[main documentation site]: services/sql-data-warehouse.md
[Afbryd midlertidigt]: sql-data-warehouse-manage-compute-portal.md/#pause-compute
[Gendanne]: sql-data-warehouse-restore-database-portal.md
[trin til at omdøbe under overførsel]: #optional-steps-to-rename-during-migration
[skala Beregn power]: sql-data-warehouse-manage-compute-portal/#scale-compute-power
[mediumrc rolle]: sql-data-warehouse-develop-concurrency/#workload-management

<!--MSDN references-->


<!--Other Web references-->
[Premium lagerplads for større ydeevne forudsigelighed]: https://azure.microsoft.com/en-us/blog/azure-sql-data-warehouse-introduces-premium-storage-for-greater-performance/
[Azure-portalen]: https://portal.azure.com
