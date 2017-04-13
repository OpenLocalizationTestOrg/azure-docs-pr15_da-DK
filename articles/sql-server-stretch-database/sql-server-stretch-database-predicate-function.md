<properties
    pageTitle="Markere rækker til at overføre ved hjælp af funktionen et filter (Stræk Database) | Microsoft Azure"
    description="Lær at markere rækker til at overføre ved hjælp af funktionen et filter."
    services="sql-server-stretch-database"
    documentationCenter=""
    authors="douglaslMS"
    manager="jhubbard"
    editor=""/>

<tags
    ms.service="sql-server-stretch-database"
    ms.workload="data-management"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="06/28/2016"
    ms.author="douglasl"/>

# <a name="select-rows-to-migrate-by-using-a-filter-function-stretch-database"></a>Markere rækker til at overføre ved hjælp af funktionen et filter (Stræk Database)

Hvis du gemmer kolde data i en separat tabel, kan du konfigurere Stræk Database for at overføre hele tabellen. Hvis tabellen indeholder både varm- og koldtvandssystemer data, på den anden side, kan du angive funktionen et filter for at markere rækkerne, der skal overføres. Prædikatet filter er en indbygget tabel\-værdi af funktion. Dette emne beskrives det, hvordan du kan skrive en indbygget tabel\-værdi funktionen til at markere rækker til at overføre.

>   [AZURE.NOTE] Hvis du angiver en filter-funktion, som udfører dårligt, udfører dataoverførsel også dårligt. Stræk Database gælder filterfunktionen for tabellen ved hjælp af operatoren CROSS anvende.

Hvis du ikke angiver et filterfunktionen, overføres hele tabellen.

Når du kører den aktivere Database Stræk guiden, kan du overføre en hel tabel, eller du kan angive en enkelt filter funktion i guiden. Hvis du vil bruge en anden type af filterfunktionen til at markere rækker til at overføre, kan du gøre et af følgende ting.

-   Afslutte guiden, og kør sætningen ALTER TABLE til at aktivere Stræk for tabellen og til at angive funktionen et filter.

-   Køre sætningen ALTER TABLE for at angive funktionen et filter, efter du har afsluttet guiden.

Syntaksen for at tilføje en funktion i ALTER TABLE er beskrevet senere i dette emne.

## <a name="basic-requirements-for-the-filter-function"></a>Grundlæggende krav til filterfunktionen
Tabellen indbygget\-valgmuligheder funktion, der kræves til et Stræk Database filter prædikat ser ud som i følgende eksempel.

```tsql
CREATE FUNCTION dbo.fn_stretchpredicate(@column1 datatype1, @column2 datatype2 [, ...n])
RETURNS TABLE
WITH SCHEMABINDING
AS
RETURN  SELECT 1 AS is_eligible
        WHERE <predicate>
```
Parametre for funktionen skal være identifikationskoder for kolonner fra tabellen.

Skemabinding er påkrævet for at forhindre kolonner, der bruges af filterfunktionen mod at blive fjernet eller ændret.

### <a name="return-value"></a>Returværdi
Hvis funktionen returnerer en ikke\-tomt resultat rækken er berettiget til at blive overført. Ellers \- det vil sige, hvis funktionen ikke returnere et resultat \- rækken ikke er berettiget til at blive overført.

### <a name="conditions"></a>Betingelser
Den &lt; *prædikat* &gt; kan bestå af en enkelt betingelse eller flere betingelser, der er blevet kædet sammen med operatoren AND logiske.

```
<predicate> ::= <condition> [ AND <condition> ] [ ...n ]
```
Hver betingelse kan også bestå af en enkelt betingelse enkle eller flere enkle betingelser, der er blevet kædet sammen med operatoren OR logiske.

```
<condition> ::= <primitive_condition> [ OR <primitive_condition> ] [ ...n ]
```

### <a name="primitive-conditions"></a>Enkle betingelser
En enkle betingelse kan gøre et af følgende sammenligninger.

```
<primitive_condition> ::=
{
<function_parameter> <comparison_operator> constant
| <function_parameter> { IS NULL | IS NOT NULL }
| <function_parameter> IN ( constant [ ,...n ] )
}
```

-   Sammenligne en funktionsparameter til et konstant udtryk. For eksempel `@column1 < 1000`.

    Her er et eksempel, der kontrollerer, om værdien af *en datokolonne* er &lt; 1/1/2016.

    ```tsql
    CREATE FUNCTION dbo.fn_stretchpredicate(@column1 datetime)
    RETURNS TABLE
    WITH SCHEMABINDING
    AS
    RETURN  SELECT 1 AS is_eligible
            WHERE @column1 < CONVERT(datetime, '1/1/2016', 101)
    GO

    ALTER TABLE stretch_table_name SET ( REMOTE_DATA_ARCHIVE = ON (
        FILTER_PREDICATE = dbo.fn_stretchpredicate(date),
        MIGRATION_STATE = OUTBOUND
    ) )
    ```

-   Anvende operatoren IS NULL eller er ikke NULL til en funktionsparameter for.

-   Bruge operatoren IN til at sammenligne en funktionsparameter til en liste over konstante værdier.

    Her er et eksempel, der kontrollerer, om værdien af en *sending\_status* kolonne er `IN (N'Completed', N'Returned', N'Cancelled')`.

    ```tsql
    CREATE FUNCTION dbo.fn_stretchpredicate(@column1 nvarchar(15))
    RETURNS TABLE
    WITH SCHEMABINDING
    AS
    RETURN  SELECT 1 AS is_eligible
            WHERE @column1 IN (N'Completed', N'Returned', N'Cancelled')
    GO

    ALTER TABLE table1 SET ( REMOTE_DATA_ARCHIVE = ON (
        FILTER_PREDICATE = dbo.fn_stretchpredicate(shipment_status),
        MIGRATION_STATE = OUTBOUND
    ) )
    ```

### <a name="comparison-operators"></a>Sammenligningsoperatorer
De følgende sammenligningsoperatorer understøttes.

`<, <=, >, >=, =, <>, !=, !<, !>`

```
<comparison_operator> ::= { < | <= | > | >= | = | <> | != | !< | !> }
```

### <a name="constant-expressions"></a>Konstant udtryk
De konstanter, der bruges i en filter-funktion kan være et deterministisk udtryk, der kan evalueres, når du definerer funktionen. Konstant udtryk kan indeholde følgende ting.

-   Konstanter. For eksempel `N’abc’, 123`.

-   Algebraiske udtryk. For eksempel `123 + 456`.

-   Deterministisk funktioner. For eksempel `SQRT(900)`.

-   Deterministisk konverteringer, der bruger CAST eller Konverter. For eksempel `CONVERT(datetime, '1/1/2016', 101)`.

### <a name="other-expressions"></a>Andre udtryk
Du kan bruge BETWEEN og ikke BETWEEN operatorer, hvis funktionen resulterende overholder de regler, der er beskrevet her, når du har erstattet operatoren BETWEEN og ikke BETWEEN med udtryk svarer og og eller.

Du kan ikke bruge underforespørgsler eller ikke\-deterministisk funktioner som funktionerne RAND() eller GETDATE().

## <a name="add-a-filter-function-to-a-table"></a>Føje funktionen et filter til en tabel
Føje funktionen et filter til en tabel ved at køre sætningen **ALTER TABLE** og angive en eksisterende tabel indbygget\-værdi funktionen som værdien af den **FILTER\_PRÆDIKAT** parameter. Eksempel:

```tsql
ALTER TABLE stretch_table_name SET ( REMOTE_DATA_ARCHIVE = ON (
    FILTER_PREDICATE = dbo.fn_stretchpredicate(column1, column2),
    MIGRATION_STATE = <desired_migration_state>
) )
```
Når funktionen er bundet til tabellen som et prædikat, er følgende ting er sande.

-   Næste gang dataoverførsel forekommer, kun de rækker, som funktionen returnerer en ikke\-tom værdi er overført.

-   De kolonner, der bruges af funktionen er skema bundet. Du kan ikke ændre disse kolonner, som en tabel ved hjælp af funktionen som dens filter prædikat.

Du kan ikke slippe tabellen indbygget\-værdi funktionen som en tabel ved hjælp af funktionen som dens filter prædikat.

>   [AZURE.NOTE] For at forbedre ydeevnen for filterfunktionen, du Opret et indeks til de kolonner, der bruges af funktionen.

### <a name="passing-column-names-to-the-filter-function"></a>Overføre kolonnenavne til filterfunktionen
Når du tildeler funktionen et filter til en tabel, du Angiv kolonnenavnene overføres til filterfunktionen med et enkelt dele navn. Hvis du angiver et tre dele navn, når du overfører kolonnen regnearksnavne, efterfølgende forespørgsler på Stræk\-aktiveret tabel mislykkes.

Eksempelvis hvis du angiver et tre dele kolonnenavn, som vist i følgende eksempel, sætningen kører korrekt, men efterfølgende forespørgsler tabellen mislykkes.

```tsql
ALTER TABLE SensorTelemetry
  SET ( REMOTE_DATA_ARCHIVE = ON (
    FILTER_PREDICATE=dbo.fn_stretchpredicate(dbo.SensorTelemetry.ScanDate),
    MIGRATION_STATE = OUTBOUND )
  )
```

I stedet angive filterfunktionen med et enkelt dele kolonnenavn, som vist i følgende eksempel.

```tsql
ALTER TABLE SensorTelemetry
  SET ( REMOTE_DATA_ARCHIVE = ON  (
    FILTER_PREDICATE=dbo.fn_stretchpredicate(ScanDate),
    MIGRATION_STATE = OUTBOUND )
  )
```

## <a name="addafterwiz"></a>Tilføje en filter funktion efter køre guiden  

Hvis du vil bruge en funktion, som du ikke kan oprette i guiden **Aktivér Database til Stræk** , kan du køre sætningen ALTER TABLE for at angive en funktion, efter du har afsluttet guiden. Før du kan anvende en funktion, men skal du stoppe overførslen data, der allerede er i gang og få vist igen overførte data. (Se flere oplysninger om hvorfor det er nødvendigt, skal [erstatte en eksisterende filterfunktionen](#replacePredicate).  

1. Skifte retning for overførsel, og at gendanne dataene, der allerede overflytningen. Du kan ikke annullere denne handling, når programmet starter. Der påløber også omkostninger på Azure udgående data med pengeoverførsler \(udgangspunkt\). Du finder flere oplysninger i [hvordan Azure priser fungerer](https://azure.microsoft.com/pricing/details/data-transfers/).  

    ```tsql  
    ALTER TABLE <table name>  
         SET ( REMOTE_DATA_ARCHIVE ( MIGRATION_STATE = INBOUND ) ) ;   
    ```  

2. Vent på overførsel til at afslutte. Du kan kontrollere status i **Stræk Database skærm** fra SQL Server Management Studio, eller du kan forespørge visningen **sys.dm_db_rda_migration_status** . Du finder flere oplysninger i [skærm og foretage fejlfinding af dataoverførsel](sql-server-stretch-database-monitor.md) eller [sys.dm_db_rda_migration_status](https://msdn.microsoft.com/library/dn935017.aspx).  

3. Oprette filterfunktionen, du vil anvende til tabellen.  

4. Føje funktionen til tabellen, og genstart dataoverførsel til Azure.  

    ```tsql  
    ALTER TABLE <table name>  
        SET ( REMOTE_DATA_ARCHIVE  
            (           
                FILTER_PREDICATE = <predicate>,  
                MIGRATION_STATE = OUTBOUND  
            )  
        );   
    ```  

## <a name="filter-rows-by-date"></a>Filtrer rækker efter dato
I følgende eksempel overfører rækker, der er tidligere end 1 januar 2016, hvor **datokolonnen** indeholder en værdi.

```tsql
-- Filter by date
--
CREATE FUNCTION dbo.fn_stretch_by_date(@date datetime2)
RETURNS TABLE
WITH SCHEMABINDING
AS
       RETURN SELECT 1 AS is_eligible WHERE @date < CONVERT(datetime2, '1/1/2016', 101)
GO
```

## <a name="filter-rows-by-the-value-in-a-status-column"></a>Filtrer rækker med værdien i statuskolonnen
I følgende eksempel overfører rækker, hvor **statuskolonnen** indeholder et af de angivne værdier.

```tsql
-- Filter by status column
--
CREATE FUNCTION dbo.fn_stretch_by_status(@status nvarchar(128))
RETURNS TABLE
WITH SCHEMABINDING
AS
       RETURN SELECT 1 AS is_eligible WHERE @status IN (N'Completed', N'Returned', N'Cancelled')
GO
```

## <a name="filter-rows-by-using-a-sliding-window"></a>Filtrere rækker ved hjælp af et glidende vindue
Hvis du vil filtrere rækker ved hjælp af et glidende vindue, skal huske på følgende krav til filterfunktionen.

-   Funktionen skal være deterministisk. Derfor kan du oprette en funktion, der automatisk genberegnes vinduet skyderen i tidens løb.

-   Funktionen bruger skemabinding. Derfor opdatere du ikke blot funktionen "i stedet" hver dag ved at ringe **Ændre funktionen** for at flytte skyderen vinduet.

Start med et filter funktion som i følgende eksempel, som overfører rækker, der er tidligere end 1 januar 2016, hvor kolonnen **systemEndTime** indeholder en værdi.

```tsql
CREATE FUNCTION dbo.fn_StretchBySystemEndTime20160101(@systemEndTime datetime2)
RETURNS TABLE
WITH SCHEMABINDING  
AS  
RETURN SELECT 1 AS is_eligible
  WHERE @systemEndTime < CONVERT(datetime2, '2016-01-01T00:00:00', 101) ;
```

Anvende filterfunktionen til tabellen.

```tsql
ALTER TABLE <table name>
SET (
        REMOTE_DATA_ARCHIVE = ON
                (
                        FILTER_PREDICATE = dbo.fn_StretchBySystemEndTime20160101 (SysEndTime)
                                , MIGRATION_STATE = OUTBOUND
                )
        )
;
```

Når du vil opdatere vinduet skyderen, skal du gøre følgende ting.

1.  Oprette en ny funktion, der angiver det nye skyderne vindue. I følgende eksempel markerer datoer tidligere end januar 2, 2016, i stedet for januar 1 2016.

2.  Erstat funktionen forrige filter med et nyt ved opkald **ALTER TABLE**, som vist i følgende eksempel.

3. Du kan også slippe forrige filterfunktionen, som du ikke længere bruger ved opkald **Slip funktionen**. (Dette trin er ikke vist i eksemplet).

```tsql
BEGIN TRAN
GO
        /*(1) Create new predicate function definition */
        CREATE FUNCTION dbo.fn_StretchBySystemEndTime20160102(@systemEndTime datetime2)
        RETURNS TABLE
        WITH SCHEMABINDING
        AS
        RETURN SELECT 1 AS is_eligible
               WHERE @systemEndTime < CONVERT(datetime2,'2016-01-02T00:00:00', 101)
        GO

        /*(2) Set the new function as the filter predicate */
        ALTER TABLE <table name>
        SET
        (
               REMOTE_DATA_ARCHIVE = ON
               (
                       FILTER_PREDICATE = dbo.fn_StretchBySystemEndTime20160102(SysEndTime),
                       MIGRATION_STATE = OUTBOUND
               )
        )
COMMIT ;
```

## <a name="more-examples-of-valid-filter-functions"></a>Flere eksempler på gyldige filterfunktioner

-   I følgende eksempel kombinerer to enkle betingelser ved hjælp af operatoren AND logiske.

    ```tsql
    CREATE FUNCTION dbo.fn_stretchpredicate((@column1 datetime, @column2 nvarchar(15))
    RETURNS TABLE
    WITH SCHEMABINDING
    AS
    RETURN  SELECT 1 AS is_eligible
      WHERE @column1 < N'20150101' AND @column2 IN (N'Completed', N'Returned', N'Cancelled')
    GO

    ALTER TABLE table1 SET ( REMOTE_DATA_ARCHIVE = ON (
        FILTER_PREDICATE = dbo.fn_stretchpredicate(date, shipment_status),
        MIGRATION_STATE = OUTBOUND
    ) )
    ```

-   I følgende eksempel bruges flere betingelser og en deterministisk konvertering med KONVERTÉR.

    ```tsql
    CREATE FUNCTION dbo.fn_stretchpredicate_example1(@column1 datetime, @column2 int, @column3 nvarchar)
    RETURNS TABLE
    WITH SCHEMABINDING
    AS
    RETURN  SELECT 1 AS is_eligible
        WHERE @column1 < CONVERT(datetime, '1/1/2015', 101)AND (@column2 < -100 OR @column2 > 100 OR @column2 IS NULL)AND @column3 IN (N'Completed', N'Returned', N'Cancelled')
    GO
    ```

-   I følgende eksempel bruges matematiske operatorer og funktioner.

    ```tsql
    CREATE FUNCTION dbo.fn_stretchpredicate_example2(@column1 float)
    RETURNS TABLE
    WITH SCHEMABINDING
    AS
    RETURN  SELECT 1 AS is_eligible
            WHERE @column1 < SQRT(400) + 10
    GO
    ```

-   I følgende eksempel bruges BETWEEN og ikke BETWEEN operatorer. Denne brugen er gyldig, fordi funktionen resulterende overholder de regler, der er beskrevet her, når du har erstattet operatoren BETWEEN og ikke BETWEEN med udtryk svarer og og eller.

    ```tsql
    CREATE FUNCTION dbo.fn_stretchpredicate_example3(@column1 int, @column2 int)
    RETURNS TABLE
    WITH SCHEMABINDING
    AS
    RETURN  SELECT 1 AS is_eligible
            WHERE @column1 BETWEEN 0 AND 100
                AND (@column2 NOT BETWEEN 200 AND 300 OR @column1 = 50)
    GO
    ```
    Funktionen foregående er svarer til følgende funktion, når du har erstattet BETWEEN og ikke BETWEEN operatorer med udtryk svarer og og eller.

    ```tsql
    CREATE FUNCTION dbo.fn_stretchpredicate_example4(@column1 int, @column2 int)
    RETURNS TABLE
    WITH SCHEMABINDING
    AS
    RETURN  SELECT 1 AS is_eligible
            WHERE @column1 >= 0 AND @column1 <= 100AND (@column2 < 200 OR @column2 > 300 OR @column1 = 50)
    GO
    ```

## <a name="examples-of-filter-functions-that-arent-valid"></a>Eksempler på filterfunktioner, der ikke er gyldige

-   Følgende funktion er ugyldig, fordi den indeholder en ikke\-deterministisk konvertering.

    ```tsql
    CREATE FUNCTION dbo.fn_example5(@column1 datetime)
    RETURNS TABLE
    WITH SCHEMABINDING
    AS
    RETURN  SELECT 1 AS is_eligible
            WHERE @column1 < CONVERT(datetime, '1/1/2016')
    GO
    ```

-   Følgende funktion er ugyldig, fordi den indeholder en ikke\-deterministisk funktionskald.

    ```tsql
    CREATE FUNCTION dbo.fn_example6(@column1 datetime)
    RETURNS TABLE
    WITH SCHEMABINDING
    AS
    RETURN  SELECT 1 AS is_eligible
            WHERE @column1 < DATEADD(day, -60, GETDATE())
    GO
    ```

-   Følgende funktion ikke er gyldige, fordi den indeholder en underforespørgsel.

    ```tsql
    CREATE FUNCTION dbo.fn_example7(@column1 int)
    RETURNS TABLE
    WITH SCHEMABINDING
    AS
    RETURN  SELECT 1 AS is_eligible
            WHERE @column1 IN (SELECT SupplierID FROM Supplier WHERE Status = 'Defunct'))
    GO
    ```

-   Følgende funktioner ikke er gyldige fordi udtryk, der bruger algebraiske operatorer eller indbygget\-i funktioner skal evalueres til en konstant når du definerer funktionen. Du må ikke indeholde kolonnereferencer i algebraiske udtryk eller funktionskald.

    ```tsql
    CREATE FUNCTION dbo.fn_example8(@column1 int)
    RETURNS TABLE
    WITH SCHEMABINDING
    AS
    RETURN  SELECT 1 AS is_eligible
            WHERE @column1 % 2 =  0
    GO

    CREATE FUNCTION dbo.fn_example9(@column1 int)
    RETURNS TABLE
    WITH SCHEMABINDING
    AS
    RETURN  SELECT 1 AS is_eligible
            WHERE SQRT(@column1) = 30
    GO
    ```

-   Følgende funktion ikke er gyldige, fordi den ikke overholder de regler, der er beskrevet her, når du har erstattet operatoren BETWEEN med den tilsvarende og udtryk.

    ```tsql
    CREATE FUNCTION dbo.fn_example10(@column1 int, @column2 int)
    RETURNS TABLE
    WITH SCHEMABINDING
    AS
    RETURN  SELECT 1 AS is_eligible
            WHERE (@column1 BETWEEN 1 AND 200 OR @column1 = 300) AND @column2 > 1000
    GO
    ```
    Funktionen foregående er svarer til følgende funktion, når du har erstattet operatoren BETWEEN med den tilsvarende og udtryk. Denne funktion er ikke gyldig, fordi enkle betingelser kan kun bruge operatoren OR logiske.

    ```tsql
    CREATE FUNCTION dbo.fn_example11(@column1 int, @column2 int)
    RETURNS TABLE
    WITH SCHEMABINDING
    AS
    RETURN  SELECT 1 AS is_eligible
            WHERE (@column1 >= 1 AND @column1 <= 200 OR @column1 = 300) AND @column2 > 1000
    GO
    ```

## <a name="how-stretch-database-applies-the-filter-function"></a>Hvordan Stræk Database anvender filterfunktionen
Stræk Database gælder filterfunktionen for tabellen og bestemmer berettiget rækker ved hjælp af operatoren CROSS anvende. Eksempel:

```tsql
SELECT * FROM stretch_table_name CROSS APPLY fn_stretchpredicate(column1, column2)
```
Hvis funktionen returnerer en ikke\-tomt resultat for rækken, rækken er berettiget til at blive overført.

## <a name="replacePredicate"></a>Erstatte en eksisterende filterfunktionen
Du kan erstatte en tidligere angivne filter-funktion ved at køre sætningen **ALTER TABLE** igen og angive en ny værdi for den **FILTER\_PRÆDIKAT** parameter. Eksempel:

```tsql
ALTER TABLE stretch_table_name SET ( REMOTE_DATA_ARCHIVE = ON (
    FILTER_PREDICATE = dbo.fn_stretchpredicate2(column1, column2),
    MIGRATION_STATE = <desired_migration_state>
```
Den nye indbyggede tabel\-valgmuligheder funktionen har følgende krav.

-   Den nye funktion skal være mindre restriktiv end den forrige funktion.

-   Alle de operatorer, som fandtes i den gamle funktion skal findes i den nye funktion.

-   Den nye funktion kan ikke indeholde operatorer, der ikke findes i den gamle funktion.

-   Kan ikke ændre rækkefølgen af operatoren argumenter.

-   Kun konstante værdier, der er en del af en `<, <=, >, >=` sammenligning kan ændres på en måde, der gør funktionen mindre restriktiv.

### <a name="example-of-a-valid-replacement"></a>Eksempel på en gyldig erstatning
Forudsætter, at følgende funktion er prædikatet aktuelle filter.

```tsql
CREATE FUNCTION dbo.fn_stretchpredicate_old (@column1 datetime, @column2 int)
RETURNS TABLE
WITH SCHEMABINDING
AS
RETURN  SELECT 1 AS is_eligible
        WHERE @column1 < CONVERT(datetime, '1/1/2016', 101)
            AND (@column2 < -100 OR @column2 > 100)
GO
```
Funktionen følgende er en gyldig erstatning, fordi den nye dato konstant (som angiver en senere skæringsdatoen) gør funktionen mindre restriktiv.

```tsql
CREATE FUNCTION dbo.fn_stretchpredicate_new (@column1 datetime, @column2 int)
RETURNS TABLE
WITH SCHEMABINDING
AS
RETURN  SELECT 1 AS is_eligible
        WHERE @column1 < CONVERT(datetime, '2/1/2016', 101)
            AND (@column2 < -50 OR @column2 > 50)
GO
```

### <a name="examples-of-replacements-that-arent-valid"></a>Eksempler på erstatninger, der ikke er gyldige
Følgende funktion er ikke en gyldig erstatning, da den nye dato konstant (som angiver en tidligere skæringsdatoen) ikke gøre funktionen mindre restriktiv.

```tsql
CREATE FUNCTION dbo.fn_notvalidreplacement_1 (@column1 datetime, @column2 int)
RETURNS TABLE
WITH SCHEMABINDING
AS
RETURN  SELECT 1 AS is_eligible
        WHERE @column1 < CONVERT(datetime, '1/1/2015', 101)
            AND (@column2 < -100 OR @column2 > 100)
GO
```
Følgende funktion er ikke en gyldig erstatning, fordi en af sammenligningsoperatorer er blevet fjernet.

```tsql
CREATE FUNCTION dbo.fn_notvalidreplacement_2 (@column1 datetime, @column2 int)
RETURNS TABLE
WITH SCHEMABINDING
AS
RETURN  SELECT 1 AS is_eligible
        WHERE @column1 < CONVERT(datetime, '1/1/2016', 101)
            AND (@column2 < -50)
GO
```
Følgende funktion er ikke en gyldig erstatning, fordi der er tilføjet en ny betingelse med operatoren AND logiske.

```tsql
CREATE FUNCTION dbo.fn_notvalidreplacement_3 (@column1 datetime, @column2 int)
RETURNS TABLE
WITH SCHEMABINDING
AS
RETURN  SELECT 1 AS is_eligible
        WHERE @column1 < CONVERT(datetime, '1/1/2016', 101)
            AND (@column2 < -100 OR @column2 > 100)
            AND (@column2 <> 0)
GO
```

## <a name="remove-a-filter-function-from-a-table"></a>Fjerne et filterfunktionen fra en tabel
For at overføre hele tabellen i stedet for markerede rækker kan du fjerne den eksisterende funktion ved at angive **FILTER\_PRÆDIKAT** til null. Eksempel:

```tsql
ALTER TABLE stretch_table_name SET ( REMOTE_DATA_ARCHIVE = ON (
    FILTER_PREDICATE = NULL,
    MIGRATION_STATE = <desired_migration_state>
) )
```
Når du fjerner filterfunktionen, vil alle rækker i tabellen er berettiget til overførsel. Du kan ikke som et resultat, angive funktionen et filter til den samme tabel senere, medmindre du hente tilbage alle dataene til tabellen fra Azure først. Denne begrænsning findes for at undgå den situation, hvor rækker, ikke der er berettiget til overførsel, når du angiver en ny funktion filter er allerede blevet flyttet til Azure.

## <a name="check-the-filter-function-applied-to-a-table"></a>Kontrollere filterfunktionen anvendes på en tabel
For at kontrollere filterfunktionen anvendes på en tabel, skal du åbne visningen katalog **sys.remote\_data\_arkiv\_tabeller** og kontrollere værdien af den **filter\_prædikat** kolonne. Hvis værdien er null, er hele tabellen berettiget til at arkivere. Du kan finde flere oplysninger om [sys.remote_data_archive_tables (Transact-SQL)](https://msdn.microsoft.com/library/dn935003.aspx).

## <a name="security-notes-for-filter-functions"></a>Sikkerhed noter til filterfunktioner  
En beskadiget konto med db_owner rettigheder kan gøre følgende.  

-   Oprette og anvende en tabelfunktion, der forbruger store mængder serverressourcer eller venter på en udvidet periode, hvilket resulterer i en manglende adgang til tjenesten.  

-   Oprette og anvende en tabelfunktion, der gør det muligt at udlede indholdet af en tabel, hvor brugeren er blevet eksplicit nægtet læseadgang.  

## <a name="see-also"></a>Se også

[ÆNDRE tabel (Transact-SQL)](https://msdn.microsoft.com/library/ms190273.aspx)
