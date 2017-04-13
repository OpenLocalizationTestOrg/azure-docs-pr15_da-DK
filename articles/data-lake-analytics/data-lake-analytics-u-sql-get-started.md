<properties
   pageTitle="Udvikle U-SQL-scripts ved hjælp af Data sø Tools til Visual Studio | Azure"
   description="Få mere at vide, hvordan du installerer Data sø Tools til Visual Studio, hvordan du udvikler og U-SQL-testscripts. "
   services="data-lake-analytics"
   documentationCenter=""
   authors="edmacauley"
   manager="jhubbard"
   editor="cgronlun"/>

<tags
   ms.service="data-lake-analytics"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data"
   ms.date="05/16/2016"
   ms.author="edmaca"/>

# <a name="tutorial-get-started-with-azure-data-lake-analytics-u-sql-language"></a>Selvstudium: Introduktion til Azure Data sø Analytics U-SQL-sprog

U-SQL er et sprog, der samler fordelene ved SQL med udtryksfuld styrken af din egen programkode til at behandle alle data på en hvilken som helst skala. U-SQL SVG distribueret forespørgsel egenskab gør det muligt at effektivt analysere data i store og på tværs af relationelle butikker som Azure SQL-Database.  Det gør det muligt at behandle ustrukturerede data ved at anvende skemaet på læse, indsætte brugerdefineret logik og Udfs og omfatter udvidelsesmuligheder af for at aktivere fint kornet styring over, hvordan du kan udføre skaleres til brug. Hvis du vil vide mere om designfilosofi bag U-SQL, skal du se denne [Visual Studio blogindlæg](https://blogs.msdn.microsoft.com/visualstudio/2015/09/28/introducing-u-sql-a-language-that-makes-big-data-processing-easy/).

Der er nogle forskelle fra ANSI SQL eller T-SQL. Eksempelvis har dens nøgleord som Vælg være med store bogstaver.

Det er type system og udtryk sprog i select-delsætninger, hvor prædikaterne osv er i C#.
Det betyder datatyperne er C#-typer og datatyperne Brug C# NULL semantik og sammenligning handlinger i et prædikat Følg C# syntaks (f.eks., en == "foo").  Det betyder også, at værdierne er fuld .NET-objekter, så du kan nemt bruge en metode til at betjene på objektet (f.eks "f o o o". Split(' ')).

Du kan finde yderligere oplysninger finder [U-SQL-Reference](http://go.microsoft.com/fwlink/p/?LinkId=691348).

###<a name="prerequisites"></a>Forudsætninger

Du skal fuldføre [Selvstudium: udvikle U-SQL-scripts ved hjælp af Data sø Tools til Visual Studio](data-lake-analytics-data-lake-tools-get-started.md).

I dette selvstudium skal kørte du et Data sø Analytics-job med følgende U-SQL-script:

    @searchlog =
        EXTRACT UserId          int,
                Start           DateTime,
                Region          string,
                Query           string,
                Duration        int?,
                Urls            string,
                ClickedUrls     string
        FROM "/Samples/Data/SearchLog.tsv"
        USING Extractors.Tsv();

    OUTPUT @searchlog   
        TO "/output/SearchLog-first-u-sql.csv"
    USING Outputters.Csv();

Dette script har ikke en hvilken som helst transformation trin. Den læser fra kildefilen kaldet **SearchLog.tsv**, schematizes den og skriver rækkesættet i en fil kaldet **SearchLog-første-u-sql.csv**igen.

Bemærk spørgsmålstegnet ud for datatypen for feltet varighed. Det betyder, at feltet varighed kan være null.

Nogle begreber og nøgleord, der findes i scriptet:

- **Rækkesættet variabler**: hver søgeudtryk, der giver et rækkesæt kan tildeles til en variabel. U-SQL følger T-SQL variable naming mønsteret, for eksempel **@searchlog** i scriptet.
    Bemærk, at opgaven ikke gennemtvinge udførelse af. Det blot brugernavne udtrykket og giver dig mulighed for at der opstår mere komplekse udtryk.
- **UDTRÆKKE** giver dig mulighed for at definere et skema på læst. Skemaet er angivet af et kolonnenavn og C# type navn par hver kolonne. Den anvender en såkaldt **udtrækning**, for eksempel **Extractors.Tsv()** til at uddrage tsv filer. Du kan udvikle brugerdefinerede optrækkere.
- **OUTPUT** tager et rækkesæt og serializes den. Outputters.Csv() output en kommasepareret fil i den angivne placering. Du kan også udvikle brugerdefinerede Outputters.
- Bemærk de to stier er relative stier. Du kan også bruge absolutte stier.  For eksempel

        adl://<ADLStorageAccountName>.azuredatalakestore.net:443/Samples/Data/SearchLog.tsv

    Få adgang til filerne i de sammenkædede lagerplads konti skal du bruge absolutte sti.  Syntaksen for filer, der er gemt i sammenkædede Azure-lager-konto er:

        wasb://<BlobContainerName>@<StorageAccountName>.blob.core.windows.net/Samples/Data/SearchLog.tsv

    >[AZURE.NOTE] Azure Blob-objektbeholder med offentlige BLOB eller offentlige beholdere adgangstilladelser understøttes ikke i øjeblikket.

## <a name="use-scalar-variables"></a>Bruge skalær variabler

Du kan bruge skalær variabler samt for at gøre dit script vedligeholdelse nemmere. Det forrige U-SQL-script kan også skrives som følgende:

    DECLARE @in  string = "/Samples/Data/SearchLog.tsv";
    DECLARE @out string = "/output/SearchLog-scalar-variables.csv";

    @searchlog =
        EXTRACT UserId          int,
                Start           DateTime,
                Region          string,
                Query           string,
                Duration        int?,
                Urls            string,
                ClickedUrls     string
        FROM @in
        USING Extractors.Tsv();

    OUTPUT @searchlog   
        TO @out
        USING Outputters.Csv();

## <a name="transform-rowsets"></a>Transformere rækkesæt

Brug **Vælg** til at transformere rækkesæt:

    @searchlog =
        EXTRACT UserId          int,
                Start           DateTime,
                Region          string,
                Query           string,
                Duration        int?,
                Urls            string,
                ClickedUrls     string
        FROM "/Samples/Data/SearchLog.tsv"
        USING Extractors.Tsv();

    @rs1 =
        SELECT Start, Region, Duration
        FROM @searchlog
    WHERE Region == "en-gb";

    OUTPUT @rs1   
        TO "/output/SearchLog-transform-rowsets.csv"
        USING Outputters.Csv();

WHERE-delsætningen bruger [C# boolesk udtryk](https://msdn.microsoft.com/library/6a71f45d.aspx). Du kan bruge C#-udtrykssproget til dit eget udtryk og funktioner. Du kan selv udføre mere komplekse filtrering ved at kombinere dem med logiske konjunktioner (og) og disjunctions (ORs).

Følgende script bruger metoden DateTime.Parse() og en sammen.

    @searchlog =
        EXTRACT UserId          int,
                Start           DateTime,
                Region          string,
                Query           string,
                Duration        int?,
                Urls            string,
                ClickedUrls     string
        FROM "/Samples/Data/SearchLog.tsv"
        USING Extractors.Tsv();

    @rs1 =
        SELECT Start, Region, Duration
        FROM @searchlog
    WHERE Region == "en-gb";

    @rs1 =
        SELECT Start, Region, Duration
        FROM @rs1
        WHERE Start >= DateTime.Parse("2012/02/16") AND Start <= DateTime.Parse("2012/02/17");

    OUTPUT @rs1   
        TO "/output/SearchLog-transform-datatime.csv"
        USING Outputters.Csv();

Bemærk, at den anden forespørgsel arbejder på resultatet af det første rækkesæt og dermed er resultatet en sammensætning af de to filtre. Du kan også genbruge et variabelnavn, og navnene er fastsat lexically.

## <a name="aggregate-rowsets"></a>Sammenlæg rækkesæt

U-SQL giver dig med velkendte **ORDER BY**, **GROUP BY** og sammenlægninger.

Følgende forespørgsel finder den samlede varighed per område og derefter skriver øverst 5 varighed i rækkefølge.

U-SQL rækkesæt bevarer ikke deres rækkefølge til den næste forespørgsel. Dermed, hvis du vil sortere output, skal du tilføje ORDER BY i OUTPUT oplysninger som vist nedenfor:

    DECLARE @outpref string = "/output/Searchlog-aggregation";
    DECLARE @out1    string = @outpref+"_agg.csv";
    DECLARE @out2    string = @outpref+"_top5agg.csv";

    @searchlog =
        EXTRACT UserId          int,
                Start           DateTime,
                Region          string,
                Query           string,
                Duration        int?,
                Urls            string,
                ClickedUrls     string
        FROM "/Samples/Data/SearchLog.tsv"
        USING Extractors.Tsv();

    @rs1 =
        SELECT
            Region,
            SUM(Duration) AS TotalDuration
        FROM @searchlog
    GROUP BY Region;

    @res =
    SELECT *
    FROM @rs1
    ORDER BY TotalDuration DESC
    FETCH 5 ROWS;

    OUTPUT @rs1
        TO @out1
        ORDER BY TotalDuration DESC
        USING Outputters.Csv();
    OUTPUT @res
        TO @out2
        ORDER BY TotalDuration DESC
        USING Outputters.Csv();

U-SQL ORDER BY-delsætning skal kombineres med Hent-delsætningen i en Vælg udtryk.

HAR du U-SQL-delsætning kan bruges til at begrænse output til grupper, der opfylder betingelsen, HAVING:

    @searchlog =
        EXTRACT UserId          int,
                Start           DateTime,
                Region          string,
                Query           string,
                Duration        int?,
                Urls            string,
                ClickedUrls     string
        FROM "/Samples/Data/SearchLog.tsv"
        USING Extractors.Tsv();

    @res =
        SELECT
            Region,
            SUM(Duration) AS TotalDuration
        FROM @searchlog
    GROUP BY Region
    HAVING SUM(Duration) > 200;

    OUTPUT @res
        TO "/output/Searchlog-having.csv"
        ORDER BY TotalDuration DESC
        USING Outputters.Csv();

## <a name="join-data"></a>Deltage i data

U-SQL indeholder almindelige joinforbindelse operatorer som indre JOINFORBINDELSE, venstre/højre/fuldstændig ydre JOINFORBINDELSE, med JOIN og deltage i ikke kun tabeller, men en hvilken som helst rækkesæt (selvom de produceret fra filer).

Følgende script sammenkæder searchlog sammen med en annonce indtryk log og giver os reklamer for forespørgselsstrengen for en given dato.

    @adlog =
        EXTRACT UserId int,
                Ad string,
                Clicked int
        FROM "/Samples/Data/AdsLog.tsv"
        USING Extractors.Tsv();

    @join =
        SELECT a.Ad, s.Query, s.Start AS Date
        FROM @adlog AS a JOIN <insert your DB name>.dbo.SearchLog1 AS s
                        ON a.UserId == s.UserId
        WHERE a.Clicked == 1;

    OUTPUT @join   
        TO "/output/Searchlog-join.csv"
        USING Outputters.Csv();


U-SQL understøtter kun ANSI kompatibel join-syntaksen: Rowset1 JOINFORBINDELSE Rowset2 til prædikat. Gamle syntaksen af fra Rowset1 Rowset2 hvor prædikat understøttes ikke.
Prædikatet i en JOINFORBINDELSE skal være en lighedstype: deltage i og uden et udtryk. Hvis du vil bruge et udtryk, skal du føje det til et tidligere rækkesæt select-delsætning. Hvis du vil gøre en anden sammenligning, kan du flytte den til WHERE-delsætningen.


## <a name="create-databases-table-valued-functions-views-and-tables"></a>Oprette databaser, tabelværdifunktioner, visninger og tabeller

U-SQL giver dig mulighed at anvende data i forbindelse med en database og -Skemafiler. Så behøver du ikke at læse fra altid eller skrive til filer.

Hver U-SQL-script kører med en standarddatabase (master) og standardskemaet (DBO) som sin standardkontekst. Du kan oprette din egen database og/eller skemaet. For at ændre konteksten, skal du bruge sætningen **bruge** til at ændre konteksten.


### <a name="create-a-table-valued-function-tvf"></a>Oprette en tabelfunktion (TVF)

I det forrige U-SQL-script gentages du med UDPAK læse fra den samme kildefil. U-SQL tabelfunktion gør det muligt at omfatter dataene til genbrug i fremtiden.   

Følgende script opretter en TVF kaldet *Searchlog()* i standarddatabasen og skemaet:

    DROP FUNCTION IF EXISTS Searchlog;

    CREATE FUNCTION Searchlog()
    RETURNS @searchlog TABLE
    (
                UserId          int,
                Start           DateTime,
                Region          string,
                Query           string,
                Duration        int?,
                Urls            string,
                ClickedUrls     string
    )
    AS BEGIN
    @searchlog =
        EXTRACT UserId          int,
                Start           DateTime,
                Region          string,
                Query           string,
                Duration        int?,
                Urls            string,
                ClickedUrls     string
        FROM "/Samples/Data/SearchLog.tsv"
    USING Extractors.Tsv();
    RETURN;
    END;

Følgende script viser, hvordan du bruger den TVF, der er defineret i det forrige script:

    @res =
        SELECT
            Region,
            SUM(Duration) AS TotalDuration
        FROM Searchlog() AS S
    GROUP BY Region
    HAVING SUM(Duration) > 200;

    OUTPUT @res
        TO "/output/SerachLog-use-tvf.csv"
        ORDER BY TotalDuration DESC
        USING Outputters.Csv();

### <a name="create-views"></a>Oprette visninger

Hvis du kun har én forespørgselsudtryk, du vil abstrakt og ikke vil standardelementet det, kan du oprette en visning i stedet for en tabelfunktion.

Følgende script opretter en visning kaldet *SearchlogView* i standarddatabasen og skemaet:

    DROP VIEW IF EXISTS SearchlogView;

    CREATE VIEW SearchlogView AS  
        EXTRACT UserId          int,
                Start           DateTime,
                Region          string,
                Query           string,
                Duration        int?,
                Urls            string,
                ClickedUrls     string
        FROM "/Samples/Data/SearchLog.tsv"
    USING Extractors.Tsv();

Følgende script viser ved hjælp af den definerede visning:

    @res =
        SELECT
            Region,
            SUM(Duration) AS TotalDuration
        FROM SearchlogView
    GROUP BY Region
    HAVING SUM(Duration) > 200;

    OUTPUT @res
        TO "/output/Searchlog-use-view.csv"
        ORDER BY TotalDuration DESC
        USING Outputters.Csv();

### <a name="create-tables"></a>Oprette tabeller

Ligesom til relationsdatabase tabel, U-SQL dig mulighed at oprette en tabel med en foruddefineret skema eller oprette en tabel, og at er skemaet fra den forespørgsel, der udfylder tabellen (også kaldet oprette tabellen AS Vælg eller CTAS).

Følgende script opretter en database og to tabeller:

    DROP DATABASE IF EXISTS SearchLogDb;
    CREATE DATABASE SeachLogDb
    USE DATABASE SearchLogDb;

    DROP TABLE IF EXISTS SearchLog1;
    DROP TABLE IF EXISTS SearchLog2;

    CREATE TABLE SearchLog1 (
                UserId          int,
                Start           DateTime,
                Region          string,
                Query           string,
                Duration        int?,
                Urls            string,
                ClickedUrls     string,

                INDEX sl_idx CLUSTERED (UserId ASC)
                    PARTITIONED BY HASH (UserId)
    );

    INSERT INTO SearchLog1 SELECT * FROM master.dbo.Searchlog() AS s;

    CREATE TABLE SearchLog2(
        INDEX sl_idx CLUSTERED (UserId ASC)
                PARTITIONED BY HASH (UserId)
    ) AS SELECT * FROM master.dbo.Searchlog() AS S; // You can use EXTRACT or SELECT here


### <a name="query-tables"></a>Forespørgselstabeller

Over datafiler, kan du forespørge tabellerne (oprettet i det forrige script) på samme måde som du forespørgsel. I stedet for at oprette et rækkesæt ved hjælp af UDPAK, kan nu du bare referere til tabelnavnet.

Transformer scriptet du tidligere har brugt ændres for at læse fra tabellerne:

    @rs1 =
        SELECT
            Region,
            SUM(Duration) AS TotalDuration
        FROM SearchLogDb.dbo.SearchLog2
    GROUP BY Region;

    @res =
        SELECT *
        FROM @rs1
        ORDER BY TotalDuration DESC
        FETCH 5 ROWS;

    OUTPUT @res
        TO "/output/Searchlog-query-table.csv"
        ORDER BY TotalDuration DESC
        USING Outputters.Csv();

Bemærk, at du i øjeblikket ikke kan køre en skal du MARKERE i en tabel i det samme script som scriptet sted, hvor du opretter tabellen.


##<a name="conclusion"></a>Konklusion

Hvad er dækket i selvstudiet er kun en lille del af U-SQL. På grund af omfanget af dette selvstudium, kan det omfatte alt, f.eks.:

- Brug CROSS gælder for Pak dele af strenge, matrixer og kort til rækker.
- Betjene partitioneret datasæt (filsæt og partitioneret tabeller).
- Udvikle brugerdefinerede operatorer som optrækkere, outputters, processorer, brugerdefinerede aggregatorer i C#.
- Brug U-SQL windowing-funktioner.
- Administrere U-SQL-koden med visninger, tabelværdifunktioner og lagrede procedurer.
- Køre brugerdefineret kode på din behandling noder.
- Oprette forbindelse til Azure SQL-databaser og federate forespørgsler på tværs af dem og dataene U-SQL og Azure Data sø.

## <a name="see-also"></a>Se også

- [Oversigt over Microsoft Azure Data sø Analytics](data-lake-analytics-overview.md)
- [Udvikle U-SQL-scripts ved hjælp af Data sø Tools til Visual Studio](data-lake-analytics-data-lake-tools-get-started.md)
- [Bruge U-SQL-vinduet-funktioner til Azure Data sø Analytics-job](data-lake-analytics-use-window-functions.md)
- [Overvåge og foretage fejlfinding af Azure Data sø Analytics job ved hjælp af Azure-portalen](data-lake-analytics-monitor-and-troubleshoot-jobs-tutorial.md)

## <a name="let-us-know-what-you-think"></a>Fortæl os, hvad du mener

- [Sende en anmodning om funktion](http://aka.ms/adlafeedback)
- [Få hjælp i forummerne](http://aka.ms/adlaforums)
- [Give feedback på U-SQL](http://aka.ms/usqldiscuss)
