<properties
    pageTitle="Kompatibilitet niveau, hvordan du vurdere | Microsoft Azure"
    description="Trin og værktøjer til at afgøre, hvilket kompatibilitetsniveau der passer bedst til din database på Azure SQL-Database eller Microsoft SQL Server"
    services="sql-database"
    documentationCenter=""
    authors="alainlissoir"
    manager="jhubbard"
    editor=""/>

<tags
    ms.service="sql-database"
    ms.workload="data-management"
    ms.devlang="NA"
    ms.tgt_pltfrm="NA"
    ms.topic="article"
    ms.date="08/08/2016"
    ms.author="alainl"/>


# <a name="improved-query-performance-with-compatibility-level-130-in-azure-sql-database"></a>Forbedret forespørgselsydelse med kompatibilitet niveau 130 i Azure SQL-Database


Azure SQL-Database kører transparent hundredvis af tusindvis af databaser på mange forskellige kompatibilitetsniveauer, bevarelse og garanterer bagudkompatibilitet til den tilsvarende version af Microsoft SQL Server for alle sine kunders!

Derfor forhindrer noget kunder, der ændrer en eksisterende databaser til den seneste kompatibilitetsniveau fra der er omfattet af de nye forespørgsel optimering og forespørgsel processorfunktioner. Som en påmindelse om historik er justeringen af SQL-version til standard kompatibilitetsniveauer på følgende måde:

- 100: i SQL Server 2008 og Azure SQL Database V11.
- 110: i SQL Server 2012 og Azure SQL Database V11.
- 120: i SQL Server-2014 og Azure SQL Database version 12.
- 130: i SQL Server 2016 og Azure SQL Database version 12.


> [AZURE.IMPORTANT] Start i **mid juni 2016**i Azure SQL-Database, bliver kompatibilitet Standardniveau 130 i stedet for 120 for **nyligt oprettede** databaser.
> 
> Databaser, der er oprettet før mid juni 2016 vil *ikke* blive påvirket, og bevarer deres aktuelle kompatibilitetsniveau (100, 110 eller 120). Databaser, der overføres fra Azure SQL-Database version V11 til version 12 er ikke vil have deres kompatibilitetsniveau ændres enten.


I denne artikel nærmere vi på fordelene ved kompatibilitetsniveau 130, og hvordan du kan udnytte disse fordele. Vi omhandler de mulige side virkninger på forespørgselsydelse for de eksisterende SQL-programmer.


## <a name="about-compatibility-level-130"></a>Om kompatibilitetsniveau 130


Først, hvis du vil vide det aktuelle niveau for kompatibilitet i databasen, skal du udføre følgende Transact-SQL-sætning.


```
SELECT compatibility_level
    FROM sys.databases
    WHERE name = '<YOUR DATABASE_NAME>’;
```


Før der sker ændringen til niveau 130 for **nyligt** oprettet databaser, Lad os gennemgå hvilke denne ændring drejer sig om gennem eksempler meget grundlæggende forespørgsel, og se, hvor alle kan få glæde af den.

Behandling af forespørgsler i relationsdatabaser kan være meget komplekse og kan medføre med masser af computer videnskab og matematik at forstå de naturlige designvalg og funktionsmåder. I dette dokument, er indholdet blevet bevidst forenklet for at sikre, at alle med nogle mindste teknisk baggrund kan forstå påvirkningen af kompatibilitet niveau ændringen og finde ud af, hvordan det kan være en fordel for programmer.

Lad os få et hurtigt blik på hvad kompatibilitetsniveauet 130 viser på tabellen.  Du kan finde flere oplysninger i [Ændre DATABASE kompatibilitetsniveau (Transact-SQL)](https://msdn.microsoft.com/library/bb510680.aspx), men her er en kort oversigt:

- Indsæt driften af en Indsæt select-sætning kan være flertrådet eller kan have en parallelle, mens du før handlingen blev single-threaded.
- Hukommelse optimeret tabel og tabel variabler forespørgsler kan nu have parallelle planer, mens du før handlingen blev også single-threaded.
- Statistik for hukommelse optimeret tabel kan nu prøver og bliver automatisk opdateret. Se [Nyheder i Database Engine: I hukommelsen OLTP](https://msdn.microsoft.com/library/bb510411.aspx#InMemory) få mere at vide.
- Batchen tilstand v/s række tilstand ændres med kolonne Store indeks
  - Sorterer på en tabel med en kolonne Store indeks er nu i batch-tilstand.
  - Windowing samlinger betjene nu i tilstanden som TSQL mellemliggende/KUNDEEMNE sætninger.
  - Forespørgsler på kolonne Store tabeller med flere forskellige delsætninger fungere i Batch-tilstand.
  - Forespørgsler, der kører under DOP = 1 eller med en seriel plan også udføre i Batch-tilstand.
- Sidst, kardinalitet skøn forbedringer rent faktisk kommer med kompatibilitetsniveau 120, men niveau 130 vises også disse forbedringer til folk, der kører på et lavere kompatibilitetsniveau (det vil sige 100 eller 110), Flyt til kompatibilitet, og disse kan også drage fordel af Forespørgselsydeevnen for dine programmer.


## <a name="practicing-compatibility-level-130"></a>Øve dig på kompatibilitetsniveau 130


Første Lad os få nogle tabeller, indeks og tilfældige data, der er oprettet, for at øve dig på nogle af disse nye funktioner. Eksemplerne TSQL script kan udføres under SQL Server 2016 eller under Azure SQL-Database. Men når du opretter en Azure SQL-database, Sørg for, at du som minimum en P2 database fordi du skal mindst et par kerner for at tillade multitrådning og derfor få glæde af disse funktioner.


```
-- Create a Premium P2 Database in Azure SQL Database

CREATE DATABASE MyTestDB
    (EDITION=’Premium’, SERVICE_OBJECTIVE=’P2′);
GO

-- Create 2 tables with a column store index on
-- the second one (only available on Premium databases)

CREATE TABLE T_source
    (Color varchar(10), c1 bigint, c2 bigint);

CREATE TABLE T_target
    (c1 bigint, c2 bigint);

CREATE CLUSTERED COLUMNSTORE INDEX CCI ON T_target;
GO

-- Insert few rows.

INSERT T_source VALUES
    (‘Blue’, RAND() * 100000, RAND() * 100000),
    (‘Yellow’, RAND() * 100000, RAND() * 100000),
    (‘Red’, RAND() * 100000, RAND() * 100000),
    (‘Green’, RAND() * 100000, RAND() * 100000),
    (‘Black’, RAND() * 100000, RAND() * 100000);

GO 200

INSERT T_source SELECT * FROM T_source;

GO 10
```


Nu har Lad os se på nogle af de Query Processing-funktioner, der kommer med kompatibilitetsniveau 130.


## <a name="parallel-insert"></a>Parallelle Indsæt


Udførelse af sætningerne TSQL udfører handlingen Indsæt under kompatibilitetsniveau 120 og 130, som henholdsvis udfører handlingen Indsæt i en enkelt sammenkædede model (120), og i en flertrådet model (130).


```
-- Parallel INSERT … SELECT … in heap or CCI
-- is available under 130 only

SET STATISTICS XML ON;

ALTER DATABASE MyTestDB
    SET COMPATIBILITY_LEVEL = 120;
GO 

-- The INSERT part is in serial

INSERT t_target WITH (tablock)
    SELECT C1, COUNT(C2) * 10 * RAND()
        FROM T_source
        GROUP BY C1
    OPTION (RECOMPILE);

ALTER DATABASE MyTestDB
    SET COMPATIBILITY_LEVEL = 130
GO

-- The INSERT part is in parallel

INSERT t_target WITH (tablock)
    SELECT C1, COUNT(C2) * 10 * RAND()
        FROM T_source
        GROUP BY C1
    OPTION (RECOMPILE);

SET STATISTICS XML OFF;
```


Ved at anmode om de faktiske forespørgselsplanen, kigger på dens grafisk repræsentation eller dens XML-indhold, kan du bestemme, hvilke kardinalitet skøn funktionen er på Afspil. Se på planer side om siden i figur 1, kan vi klart se, at kolonnen Store indsætte udførelsen går fra seriel i 120 til parallel i 130. Bemærk også, der faktisk parallelle ændring af ikonet gentagelsesstreng i den 130 plan, der viser to parallelle pile, som illustrerer fakultet der nu gentagelsesstreng udførelsen. Hvis du har store Indsæt handlingerne er fuldført, kan parallel udførelse af sammenkædet med antallet af core, du har til rådighed for databasen, fungere bedre; Afhængigt af din situation op til et 100 gange hurtigere!


*Figur 1: Indsæt handling ændrer fra seriel parallel niveau 130 med kompatibilitet.*


![Figur 1](./media/sql-database-compatibility-level-query-performance-130/figure-1.jpg)


## <a name="serial-batch-mode"></a>SERIEL Batch-tilstand


På samme måde, flytte til kompatibilitetsniveau 130, når behandling datarækker batchbehandling tilstand, som gør det muligt. Batchen tilstand handlinger er først kun tilgængelige, når du har et store kolonneindeks på plads. En gruppe repræsenterer typisk ~ 900 rækker, anvendes en kode logik, der er optimeret til Multifacetteret CPU, højere hukommelse overførselshastighed og direkte bruger den komprimerede data af kolonne Store, når det er muligt. Under disse betingelser: SQL Server 2016 kan behandle ~ 900 rækker ad gangen, i stedet for 1 række på tidspunkt, og nu, den totale faste omkostninger for handlingen deles af hele batchen, at reducere den samlede omkostninger ved række. Denne delte mængden af handlinger, som kombineres med kolonne store komprimering reducerer den ventetid, der er involveret i handlingen Vælg batchen tilstand. Du kan finde flere oplysninger om store kolonne og batch-tilstand med [Columnstore indeks vejledning](https://msdn.microsoft.com/library/gg492088.aspx).


```
-- Serial batch mode execution

SET STATISTICS XML ON;

ALTER DATABASE MyTestDB
    SET COMPATIBILITY_LEVEL = 120;
GO

-- The scan and aggregate are in row mode

SELECT C1, COUNT (C2)
    FROM T_target
    GROUP BY C1
    OPTION (MAXDOP 1, RECOMPILE);
GO

ALTER DATABASE MyTestDB
    SET COMPATIBILITY_LEVEL = 130;
GO 

– The scan and aggregate are in batch mode,
-- and force MAXDOP to 1 to show that batch mode
-- also now works in serial mode.

SELECT C1, COUNT(C2)
    FROM T_target
    GROUP BY C1
    OPTION (MAXDOP 1, RECOMPILE);
GO

SET STATISTICS XML OFF;
```


Som synlig under, på grundlag forespørgslen planer side om side på figur 2, vi kan er se, at tilstanden behandling ændret med kompatibilitetsniveauet for, og, når du kører forespørgslerne i begge kompatibilitetsniveau helt, kan vi se, at det meste af behandlingstiden brugt i række-tilstand (86%) sammenlignet med batch-tilstand (14%), hvor 2 batches er blevet behandlet. Øge datasættet, fordelen øges.


*Figur 2: Vælge handlingen ændringer fra seriel til batch-tilstand med kompatibilitetsniveau 130.*


![Figur 2](./media/sql-database-compatibility-level-query-performance-130/figure-2.jpg)


## <a name="batch-mode-on-sort-execution"></a>Batch-tilstand på sortering af


Ligner ovenfor, men anvendes på en sorteringshandling overgangen fra række-tilstand (kompatibilitetsniveau 120) til batch-tilstand (kompatibilitetsniveau 130) forbedrer ydeevnen for sortering af samme årsager.


```
-- Batch mode on sort execution

SET STATISTICS XML ON;

ALTER DATABASE MyTestDB
    SET COMPATIBILITY_LEVEL = 120;
GO

-- The scan and aggregate are in row mode

SELECT C1, COUNT(C2)
    FROM T_target
    GROUP BY C1
    ORDER BY C1
    OPTION (MAXDOP 1, RECOMPILE);
GO

ALTER DATABASE MyTestDB
    SET COMPATIBILITY_LEVEL = 130;
GO

-- The scan and aggregate are in batch mode,
-- and force MAXDOP to 1 to show that batch mode
-- also now works in serial mode.

SELECT C1, COUNT(C2)
    FROM T_target
    GROUP BY C1
    ORDER BY C1
    OPTION (MAXDOP 1, RECOMPILE);
GO

SET STATISTICS XML OFF;
```


Synlige side om side på figur 3, kan vi se, at sorteringshandling i række tilstand repræsenterer 81% af alle omkostninger, mens tilstanden batchen repræsenterer kun 19% af omkostninger (henholdsvis 81% og 56% i feltet Sortér selve).


*Figur 3: Sorteringshandling ændres fra række til batch-tilstand med kompatibilitetsniveau 130.*


![Figur 3](./media/sql-database-compatibility-level-query-performance-130/figure-3.png)


Tydeligt, disse eksempler kun indeholde tusindvis af rækker, som er ikke noget, når de kigger på de data, der er tilgængelige i de fleste SQL-servere disse dage. Project kun disse mod millioner af rækker i stedet, og dette kan oversætte i flere minutter for udførelse af spare hver dag afventer art af din arbejdsbyrde.


## <a name="cardinality-estimation-ce-improvements"></a>Forbedringer af kardinalitet skøn (CE)


Introduceret med SQL Server-2014, en database, der kører på et kompatibilitetsniveau 120 eller over vil gøre brug af nye kardinalitet skønnet funktionalitet. Grundlæggende, er kardinalitet skøn logik bruges til at bestemme, hvordan SQL server kan udføre en forespørgsel baseret på de estimerede omkostninger. Skønnet beregnes ved hjælp af input fra statistik, der er knyttet til objekter, der er involveret i denne forespørgsel. Næsten, på en overordnet kardinalitet skøn funktioner er række Tæl beregner sammen med oplysninger om fordelingen af værdier, entydige værdi tæller og dublerede tæller opbevares i tabeller og objekter, der refereres til i forespørgslen. Få disse estimater forkert, kan medføre unødvendige disk I/O på grund af utilstrækkelig hukommelse giver (det vil sige TempDB spild) eller en markering af et serielt plan udførelse af over en parallelle udførelse af navngive nogle. Konklusion, der kan medføre forkert beregner med en overordnede ydeevne er forringet af udførelse af forespørgsler. På anden siden bedre beregner, mere nøjagtig beregner fører til bedre forespørgsel udførelser!

Som nævnt før, forespørgsel optimeringer og beregner er noget ud af en kompleks, men hvis du vil vide mere om forespørgselsplaner og kardinalitet estimator, du kan referere til dokumentet på [Optimering af din forespørgselsplaner med SQL Server 2014 kardinalitet Estimator](https://msdn.microsoft.com/library/dn673537.aspx) for en dybere undersøgelse.


## <a name="which-cardinality-estimation-do-you-currently-use"></a>Hvilke kardinalitet skøn du aktuelt bruger?


Til at afgøre, under hvilke kardinalitet skøn dine forespørgsler kører, vi blot bruge forespørgsel eksemplerne nedenfor. Bemærk, at dette første eksempel kører under kompatibilitetsniveau 110, hvilket indebærer brug af funktionerne gamle kardinalitet skøn.


```
-- Old CE

ALTER DATABASE MyTestDB
    SET COMPATIBILITY_LEVEL = 110;
GO

SET STATISTICS XML ON;

SELECT [c1]
    FROM [dbo].[T_target]
    WHERE [c1] > 20000;
GO

SET STATISTICS XML OFF;
```


Når udførelse af er fuldført, skal du klikker på linket XML- og se egenskaberne for den første gentagelsesstreng som vist nedenfor. Bemærk det kaldet CardinalityEstimationModelVersion i øjeblikket er indstillet på 70 navn. Det betyder ikke, at niveauet database kompatibilitet er indstillet til den SQL Server 7.0 version (det er angivet på 110 som synlig i sætningerne TSQL ovenfor), men værdien 70 repræsenterer blot ældre kardinalitet skøn tilgængelige funktioner siden SQL Server 7.0, der havde ingen større ændringer indtil SQL Server-2014 (som følger med et kompatibilitet beskyttelsesniveau 120).


*Figur 4: CardinalityEstimationModelVersion er angivet til 70, når du bruger et kompatibilitetsniveau på 110 eller under.*


![Figur 4](./media/sql-database-compatibility-level-query-performance-130/figure-4.png)


Du kan også ændre kompatibilitetsniveauet for til 130 og deaktivere brugen af funktionen ny kardinalitet skøn ved hjælp af LEGACY_CARDINALITY_ESTIMATION indstillet til ON med [Ændre DATABASE fastsat konfiguration](https://msdn.microsoft.com/library/mt629158.aspx). Dette er præcis det samme som at bruge 110 fra en kardinalitet skøn funktionen synspunkt, mens du bruger den seneste forespørgsel behandling kompatibilitetsniveau. Hvis du gør det, kan du få glæde af den nye forespørgsel behandling af funktioner, der kommer med de seneste kompatibilitet (det vil sige batch-tilstand), men stadig regne gamle kardinalitet skøn funktionaliteten, hvis det er nødvendigt.


```
-- Old CE

ALTER DATABASE MyTestDB
    SET COMPATIBILITY_LEVEL = 130;
GO

ALTER DATABASE
    SCOPED CONFIGURATION
    SET LEGACY_CARDINALITY_ESTIMATION = ON;
GO

SET STATISTICS XML ON;

SELECT [c1]
    FROM [dbo].[T_target]
    WHERE [c1] > 20000;
GO

SET STATISTICS XML OFF;
```


Flytte blot til kompatibilitetsniveauet 120 eller 130 gør det muligt for den nye kardinalitet skøn funktionalitet. I så fald standard CardinalityEstimationModelVersion angives i overensstemmelse hermed 120 eller 130 som synlige nedenfor.


```
-- New CE

ALTER DATABASE MyTestDB
    SET COMPATIBILITY_LEVEL = 130;
GO

ALTER DATABASE
    SCOPED CONFIGURATION
    SET LEGACY_CARDINALITY_ESTIMATION = OFF;
GO

SET STATISTICS XML ON;

SELECT [c1]
    FROM [dbo].[T_target]
    WHERE [c1] > 20000;
GO

SET STATISTICS XML OFF;
```


*Figur 5: CardinalityEstimationModelVersion er angivet til 130, når du bruger et kompatibilitet beskyttelsesniveau 130.*


![Figur 5](./media/sql-database-compatibility-level-query-performance-130/figure-5.jpg)


## <a name="witnessing-the-cardinality-estimation-differences"></a>Overværer kardinalitet skøn forskellene


Nu, Lad os Kør en lidt mere kompleks forespørgsel, der involverer en INNER JOIN med en WHERE-delsætning med nogle prædikaterne, og Lad os se på den række Tæl estimat fra den gamle kardinalitet skøn funktion først.


```
-- Old CE row estimate with INNER JOIN and WHERE clause

ALTER DATABASE MyTestDB
    SET COMPATIBILITY_LEVEL = 130;
GO

ALTER DATABASE
    SCOPED CONFIGURATION
    SET LEGACY_CARDINALITY_ESTIMATION = ON;
GO

SET STATISTICS XML ON;

SELECT T.[c2]
    FROM
                   [dbo].[T_source] S
        INNER JOIN [dbo].[T_target] T  ON T.c1=S.c1
    WHERE
        S.[Color] = ‘Red’  AND
        S.[c2] > 2000  AND
        T.[c2] > 2000
    OPTION (RECOMPILE);
GO

SET STATISTICS XML OFF;
```


Udføre denne forespørgsel effektivt returnerer 200,704 rækker, mens estimatet række med gamle kardinalitet skøn funktionaliteten krav 194,284 rækker. Tydeligt, som dog før disse række antal resultater vil også afhænger af, hvor ofte du kørte forrige eksemplerne, som udfylder eksempeltabellerne igen og igen på hver Kør. Tydeligt, prædikaterne i din forespørgsel har også indflydelse på faktisk skønnet ud over figuren, tabel, data indhold, og hvordan disse data faktisk koordinere med hinanden.


*Figur 6: Række Tæl estimatet er 194,284 eller 6.000 rækker fra fra 200,704 rækkerne forventet.*


![Figur 6](./media/sql-database-compatibility-level-query-performance-130/figure-6.jpg)


På samme måde, Lad os nu udføre den samme forespørgsel med den nye kardinalitet skøn funktionalitet.


```
-- New CE row estimate with INNER JOIN and WHERE clause

ALTER DATABASE MyTestDB
    SET COMPATIBILITY_LEVEL = 130;
GO

ALTER DATABASE
    SCOPED CONFIGURATION
    SET LEGACY_CARDINALITY_ESTIMATION = OFF;
GO

SET STATISTICS XML ON;

SELECT T.[c2]
    FROM
                   [dbo].[T_source] S
        INNER JOIN [dbo].[T_target] T  ON T.c1=S.c1
    WHERE
        S.[Color] = ‘Red’  AND
        S.[c2] > 2000  AND
        T.[c2] > 2000
    OPTION (RECOMPILE);
GO

SET STATISTICS XML OFF;
```


Se på de nedenfor vi nu se, at den række estimat 202,877, eller meget nærmere og højere end gamle kardinalitet skønnet.

*Figur 7: Række Tæl estimatet er nu 202,877 i stedet for 194,284.*


![Figur 7](./media/sql-database-compatibility-level-query-performance-130/figure-7.jpg)


I virkeligheden, resultatsættet er 200,704 rækker (men hele afhænger af, hvor ofte du kørte forespørgsler i de tidligere eksempler, men vigtigere, fordi TSQL bruger sætningen funktionerne RAND(), de faktiske værdier, returneres kan variere fra én Kør til næste). I dette eksempel udfører nye kardinalitet skønnet derfor bedre opgave på beregning antallet af rækker, fordi 202,877 er meget tættere på 200,704, end 194,284! Efternavn, hvis du ændrer WHERE-delsætningen prædikater til lige (i stedet ">" for eksempel), kan det gøre beregner mellem gammelt og nye kardinalitet funktion endnu flere forskellige, afhængigt af hvor mange svarer til du kan få.

Tydeligt, i dette tilfælde der ~ 6000 rækker fra fra faktiske antal repræsenterer ikke en stor mængde data i nogle situationer. TRANSPONER denne indstilling for at millioner af rækker på tværs af flere tabeller og mere komplekse forespørgsler og nogle gange estimatet kan være slået fra ved millioner af rækker og derfor risikoen for valg af den forkerte udførelse af plan eller anmode om utilstrækkelig hukommelse giver, hvilket medfører TempDB spild, og så flere i/o-, er nu meget højere.

Hvis du har mulighed, øvelse denne sammenligning med dit mest enkle forespørgsler og datasæt, og se til dig selv ved hvor meget nogle af de gamle og nye overslag påvirkes, mens nogle kan kun blive flere fra fra faktum eller nogle andre lige blot tættere til rækken faktisk tæller faktisk returnerede i resultatsættene. Hele afhænger af udformningen af dine forespørgsler, Azure SQL database-egenskaber, hvilke og størrelsen af din datasæt, og de tilgængelige om dem statistikker. Hvis du lige har oprettet din Azure SQL Database-forekomst, får query optimizer at opbygge dens viden fra bunden i stedet for at genbruge statistik, der er foretaget af forrige forespørgslen køres. Beregner er derfor meget kontekstafhængig og næsten specifikke for hver server og programmet situation. Det er et vigtigt aspekt skal huske på!


## <a name="some-considerations-to-take-into-account"></a>Nogle overvejelser i forbindelse med at tage i betragtning


Selvom de fleste arbejdsbelastninger ville få glæde af kompatibilitetsniveauet 130, før du indfører kompatibilitetsniveauet for dit produktionsmiljø, har du som 3 muligheder:

1. Du flytter til kompatibilitetsniveau 130, og se, hvordan ting, du udfører. Hvis du oplever nogle regressioner, du lige blot angive kompatibiliteten niveau tilbage til dens oprindelige niveau, eller Behold 130, og kun modsat kardinalitet skønnet tilbage til ældre tilstand (som beskrevet ovenfor, dette alene kan løse problemet).
2. Du test grundigt dine eksisterende programmer lignende fremstilling belastning, finjustere at og validere ydeevnen før du skifter til fremstilling. I tilfælde af problemer samme som ovenfor, du kan altid gå tilbage til det oprindelige kompatibilitetsniveau eller blot tilbageføre kardinalitet skønnet til de ældre tilstand.
3. Valgmuligheden færdig, og den seneste metode til at løse disse spørgsmål, er at udnytte forespørgsel Store. Det er dags anbefalede indstilling! Hjælper analyse af dine forespørgsler under kompatibilitet niveau 120 eller under kontra 130, kan ikke opfordrer vi dig nok til at anvende forespørgsel lager. Forespørgsel Store leveres med den seneste version af Azure SQL-Database version 12, og det er udviklet til at hjælpe dig med fejlfinding af forespørgsel ydeevne. Tænk på forespørgsel Store som en flight dataregistrering for databasen indsamling og præsenterer detaljerede historiske oplysninger om alle forespørgsler. Dette forenkler væsentligt ydeevne forensics mindre tid til at identificere og løse problemer. Du kan finde flere oplysninger i [forespørgsel Store: en flight dataregistrering for databasen](https://azure.microsoft.com/blog/query-store-a-flight-data-recorder-for-your-database/).


På den overordnede, hvis du allerede har et sæt af databaser, der kører på kompatibilitetsniveau 120 eller under, og har planer om at flytte nogle af dem til 130, eller fordi arbejdsbyrden Klargør automatisk nye databaser, der er snart angives som standard til 130, skal du overveje følgende:

- Før du ændrer til kompatibilitetsniveauet i fremstilling, aktivere forespørgsel Store. Du kan referere til at [ændre kompatibilitetstilstand Database og bruge i forespørgslen Store](https://msdn.microsoft.com/library/bb895281.aspx) kan finde flere oplysninger.
- Derefter skal du teste alle vigtige arbejdsmængder ved hjælp af repræsentant data og forespørgsler af et fremstilling-lignende miljø, og Sammenlign ydeevnen fandt og som oplyst forespørgsel Store. Hvis du oplever nogle regressioner, du kan identificere regressed forespørgsler med forespørgsel Store og bruge den plan, at tvinge indstilling fra forespørgsel Store (også kendt planlægge Fastgør). I så fald du endeligt beholde kompatibilitetsniveauet 130, og brug den tidligere forespørgselsplan som foreslåede af forespørgsel butikken.
- Hvis du vil benytte nye funktioner og funktionerne i Azure SQL-Database (der kører SQL Server 2016), men er følsomme over for ændringer, der følger efter kompatibilitet niveau 130, som endelig, kan du overveje at tvinge kompatibilitetsniveauet for tilbage til det niveau, der passer til dine arbejdsbelastningen ved hjælp af en JUSTER DATABASE-sætning. Men først skal du være opmærksom på, at forespørgslen Store planen, Fastgør indstilling er den bedste løsning, fordi du ikke bruger 130 grundlæggende holde på funktionalitet niveau i en ældre version af SQL Server.
- Hvis du har multiprofiler programmer, der strækker sig over flere databaser, kan det være nødvendigt at opdatere klargøring logik i dine databaser for at sikre en ensartet kompatibilitet på tværs af alle databaser gamle og nye klargjort dem. Dit program arbejdsbelastningen ydeevne kan være følsomme til, at nogle databaser, der kører på forskellige kompatibilitetsniveauer, og derfor kompatibilitet niveau konsistens på tværs af alle databaser, der kan være nødvendige for at give den samme oplevelse til dine kunder alle på tværs af tavlen. Bemærk, at det er ikke en opgave, det virkelig afhænger hvordan dit program påvirkes af kompatibilitetsniveauet for.
- Sidst, vedrørende kardinalitet skønnet og ligesom ændre kompatibilitetsniveauet, før du fortsætter i fremstilling, anbefales det at teste din fremstilling arbejdsbyrde under de nye betingelser til at bestemme, hvis dit program fordel af kardinalitet skøn forbedringerne.


## <a name="conclusion"></a>Konklusion


Brug af Azure SQL-Database til at få glæde af alle forbedringer af SQL Server 2016 kan forbedre din forespørgsel udførelser tydeligt. Ligesom-er! Naturligvis som en ny funktion udføres en stort beregning for at finde ud af, de nøjagtige betingelser, under hvilket databasens arbejdsbelastning fungerer bedst. Oplevelse viser, at de fleste arbejdsbelastningen forventes mindst køre transparent under kompatibilitetsniveau 130, mens du udnytter ny forespørgsel behandling af funktioner og nye kardinalitet skøn. Dog realistisk, der er altid nogle undtagelser, og at gøre stort forfaldsdato omhu er en vigtig vurdering til at afgøre, hvor meget du kan få glæde af disse forbedringer. Og igen, forespørgsel Store kan være en god hjælp til at gøre dette!

Som SQL Azure udvikler sig, kan du forvente et kompatibilitetsniveau for 140 i fremtiden. Når tiden er relevant, kan vi starter taler om, hvad dette fremtidige kompatibilitetsniveau 140 vil flytte, ligesom vi som beskrevet her, adgangsniveau kompatibilitet 130 samler i dag.

Nu skal Lad os ikke glemmer, starter juni 2016, Azure SQL-Database ændres kompatibilitet Standardniveau fra 120 130 for nyoprettede databaser. Vær opmærksom på!


## <a name="references"></a>Referencer


- [Hvad er nyt i databaseprogram](https://msdn.microsoft.com/library/bb510411.aspx#InMemory)

- [Blog: Forespørgsel Store: en flight dataregistrering for databasen, ved at Borko Novakovic, juni 8 2016](https://azure.microsoft.com/blog/query-store-a-flight-data-recorder-for-your-database/)

- [ALTER DATABASE kompatibilitetsniveau (Transact-SQL)](https://msdn.microsoft.com/library/bb510680.aspx)

- [ALTER DATABASE FASTSAT KONFIGURATION](https://msdn.microsoft.com/library/mt629158.aspx)

- [Kompatibilitetsniveau 130 for version 12 Azure SQL-Database](https://azure.microsoft.com/updates/compatibility-level-130-for-azure-sql-database-v12/)

- [Optimere din forespørgsel plan med SQL Server 2014 kardinalitet Estimator](https://msdn.microsoft.com/library/dn673537.aspx)

- [Columnstore indeks vejledning](https://msdn.microsoft.com/library/gg492088.aspx)

- [Blog: Forbedret udførelse af forespørgsler med kompatibilitetsniveau 130 i Azure SQL-Database ved Alain Lissoir maj 6 2016](https://blogs.msdn.microsoft.com/sqlserverstorageengine/2016/05/06/improved-query-performance-with-compatibility-level-130-in-azure-sql-database/)



<!--
Improved Query Performance with Compatibility Level 130 in Azure SQL Database

May 6, 2016 by Alain Lissoir (AlainL), on GitHub 'alainlissoir'.

https://blogs.msdn.microsoft.com/sqlserverstorageengine/2016/05/06/improved-query-performance-with-compatibility-level-130-in-azure-sql-database/

..... Now, above.
....................
..... Soon, below?

CAPS / MSDN ideally, but instead on ACom:
.. # Assess effects of latest compatibility level on query performance, how to

sql-database-compatibility-level-query-performance-130.md

genemi = MightyPen , 2016-05-20  Friday  17:00pm
-->
