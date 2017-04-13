<properties 
   pageTitle="Bruge U-SQL-vinduet-funktioner til Azure Data sø Aanlytics job | Azure" 
   description="Lær at bruge U-SQL-vinduet. " 
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


# <a name="using-u-sql-window-functions-for-azure-data-lake-analytics-jobs"></a>Bruge U-SQL-vinduet-funktioner til Azure Data sø Analytics-job  

Vinduet funktioner blev introduceret til ISO/ANSI SQL-Standard i 2003. U-SQL fastsætter et undersæt af vinduet funktioner, som defineret af ANSI SQL-Standard.

Vinduet funktion bruges til at gøre beregning i sæt af rækker kaldet *windows*. Windows er defineret af delsætningen OVER. Vinduet funktioner Løs nogle nøglescenarier på en meget effektiv måde.

Denne vejledning learning bruger to eksempel datasæt til at vejlede dig gennem nogle eksempel på et scenario, hvor du kan anvende vinduet funktioner. Du kan finde yderligere oplysninger finder [U-SQL-reference](http://go.microsoft.com/fwlink/p/?LinkId=691348).

Funktionerne vinduet er inddelt i: 

- [Sammenlægning rapporteringsfunktioner](#reporting-aggregation-functions), som SUMMEN eller AVG
- [Rangordne funktioner](#ranking-functions), som DENSE_RANK, rækkenummer, NTILE og RANG
- [Analytisk funktioner](#analytic-functions)som kumulative fordelingsfunktion, fraktiler eller opretter adgang til data fra en tidligere række i den samme resultatsættet uden brug af en selv-joinforbindelse

**Forudsætninger for:**

- Gennemgå følgende to selvstudier:

    - [Introduktion til brug af Azure Data sø Tools til Visual Studio](data-lake-analytics-data-lake-tools-get-started.md).
    - [Introduktion til brug af U-SQL for Azure Data sø Analytics sager](data-lake-analytics-u-sql-get-started.md).
- Oprette en Data sø analytisk konto, som beskrevet i [gang med at bruge Azure Data sø Tools til Visual Studio](data-lake-analytics-data-lake-tools-get-started.md).
- Oprette et Visual Studio U-SQL-projekt, som beskrevet i [gang med at bruge U-SQL for Azure Data sø Analytics sager](data-lake-analytics-u-sql-get-started.md).

## <a name="sample-datasets"></a>Eksempel datasæt

Dette selvstudium bruger to datasæt:

- QueryLog 

    QueryLog repræsenterer en liste over hvad personer søger efter i søgemaskine. Hver forespørgsel loggen indeholder:
    
        - Query - What the user was searching for.
        - Latency - How fast the query came back to the user in milliseconds.
        - Vertical - What kind of content the user was interested in (Web links, Images, Videos).
    
    Kopiere og indsætte følgende scrip i projektet U-SQL til oprettelse af QueryLog rækkesættet:
    
        @querylog = 
            SELECT * FROM ( VALUES
                ("Banana"  , 300, "Image" ),
                ("Cherry"  , 300, "Image" ),
                ("Durian"  , 500, "Image" ),
                ("Apple"   , 100, "Web"   ),
                ("Fig"     , 200, "Web"   ),
                ("Papaya"  , 200, "Web"   ),
                ("Avocado" , 300, "Web"   ),
                ("Cherry"  , 400, "Web"   ),
                ("Durian"  , 500, "Web"   ) )
            AS T(Query,Latency,Vertical);
    
    Dataene gemmes i praksis sandsynligvis i en datafil. Du kan få adgang til dataene inde i en tabulatorsepareret fil ved hjælp af følgende kode: 
    
        @querylog = 
        EXTRACT 
            Query    string, 
            Latency  int, 
            Vertical string
        FROM "/Samples/QueryLog.tsv"
        USING Extractors.Tsv();

- Medarbejdere

    Medarbejder datasættet indeholder følgende felter:
   
        - EmpID - Employee ID.
        - EmpName  Employee name.
        - DeptName - Department name. 
        - DeptID - Deparment ID.
        - Salary - Employee salary.

    Kopiere og indsætte følgende script i projektet U-SQL for construcint medarbejdere rækkesættet:

        @employees = 
            SELECT * FROM ( VALUES
                (1, "Noah",   "Engineering", 100, 10000),
                (2, "Sophia", "Engineering", 100, 20000),
                (3, "Liam",   "Engineering", 100, 30000),
                (4, "Emma",   "HR",          200, 10000),
                (5, "Jacob",  "HR",          200, 10000),
                (6, "Olivia", "HR",          200, 10000),
                (7, "Mason",  "Executive",   300, 50000),
                (8, "Ava",    "Marketing",   400, 15000),
                (9, "Ethan",  "Marketing",   400, 10000) )
            AS T(EmpID, EmpName, DeptName, DeptID, Salary);
    
    Følgende sætning demonstrerer oprette rækkesættet ved at hente det fra en datafil.
    
        @employees = 
        EXTRACT 
            EmpID    int, 
            EmpName  string, 
            DeptName string, 
            DeptID   int, 
            Salary   int
        FROM "/Samples/Employees.tsv"
        USING Extractors.Tsv();

Når du tester eksemplerne i selvstudium, skal du medtage rækkesættet definitioner. U-SQL kræver, at du kan definere kun den rækkesæt, der bruges. Nogle eksempler skal kun én rækkesættet.

Du skal også tilføje følgende sætning for at sende resultatet rækkesættet til en datafil:

    OUTPUT @result TO "/wfresult.csv" 
        USING Outputters.Csv();
 
 De fleste af eksemplerne bruge variablen kaldet **@result** for resultaterne.

## <a name="compare-window-functions-to-grouping"></a>Sammenligne vinduet funktioner til gruppering

Windowing og gruppering objekter vedrører ved også forskellige. Det er nyttigt at forstå denne relation.

### <a name="use-aggregation-and-grouping"></a>Brug sammenlægning og gruppering

Følgende forespørgsel bruger en sammenlægning til at beregne det samlede løn for alle medarbejdere:

    @result = 
        SELECT 
            SUM(Salary) AS TotalSalary
        FROM @employees;
    
>[AZURE.NOTE] Flere oplysninger om afprøvning og kontrol output, under [Introduktion til brug af U-SQL for Azure Data sø Analytics sager](data-lake-analytics-u-sql-get-started.md).

Resultatet er en enkelt række med en enkelt kolonne. 165000 $ er summen af af værdien løn fra hele tabellen. 

|TotalSalary
|-----------
|165000

>[AZURE.NOTE] Hvis du er ny bruger af windows-funktioner, er det er praktisk at huske tallene i output.  

Følgende sætning Brug GROUP BY-delsætningen til at beregne det samlede salery for hver afdeling:

    @result=
        SELECT DeptName, SUM(Salary) AS SalaryByDept
        FROM @employees
        GROUP BY DeptName;

Resultaterne er:

|DeptName|SalaryByDept
|--------|------------
|Ingeniørarbejde|60000
|HR|30000
|Administrerende|50000
|Marketing|25000

Summen af kolonnen SalaryByDept er $165000, som svarer til beløbet i det sidste script.
 
I begge disse tilfælde er antallet af der færre output rækker end input rækker:
 
- Uden GROUP BY sammenlægning, skjules alle rækker i en enkelt række. 
- Med GROUP BY, der ikke er N output rækker, hvor N er antallet af entydige værdier, der vises i dataene, i dette tilfælde får du 4 rækker i output.

###  <a name="use-a-window-function"></a>Bruge en funktion i vinduet

Delsætningen OVER i det følgende eksempel er tom. Dette definerer "vinduet" for at medtage alle rækker. SUMMEN i dette eksempel anvendes på OVER-delsætning, som det står foran.

Du kan læse denne forespørgsel som: "Sum af løn over et vindue af alle rækker".

    @result=
        SELECT
            EmpName,
            SUM(Salary) OVER( ) AS SalaryAllDepts
        FROM @employees;

I modsætning til GROUP BY er der så mange output rækker som input rækker: 

|EmpName|TotalAllDepts
|-------|--------------------
|Noah|165000
|Sophia|165000
|Liam|165000
|Emma|165000
|Jacob|165000
|Olivia|165000
|Mason|165000
|Ava|165000
|Ethan|165000


Værdien af 165000 (summen af alle lønninger) er placeret i hver række med output. Samlet kommer fra "vindue" af alle rækker, så den indeholder alle lønninger. 

Det næste eksempel viser, hvordan at afgrænse "vinduet" for at få vist alle medarbejdere, afdelingen og den samlede løn for afdelingen. PARTITION som føjes til delsætningen OVER.

    @result=
    SELECT
        EmpName, DeptName,
        SUM(Salary) OVER( PARTITION BY DeptName ) AS SalaryByDept
    FROM @employees;

Resultaterne er:

|EmpName|DeptName|SalaryByDep
|-------|--------|-------------------
|Noah|Ingeniørarbejde|60000
|Sophia|Ingeniørarbejde|60000
|Liam|Ingeniørarbejde|60000
|Mason|Administrerende|50000
|Emma|HR|30000
|Jacob|HR|30000
|Olivia|HR|30000
|Ava|Marketing|25000
|Ethan|Marketing|25000

Igen, der er det samme antal input rækker som output rækker. Men hver række har en samlet løn for den tilsvarende afdeling.




## <a name="reporting-aggregation-functions"></a>Sammenlægning rapporteringsfunktioner

Vinduet funktioner understøtter også følgende aggregeringerne:

- TÆL
- SUM
- MIN
- MAKS
- AVG
- STDAFV
- VARIANS

Syntaks:

    <AggregateFunction>( [DISTINCT] <expression>) [<OVER_clause>]

Bemærk! 

- Som standard ignorerer aggregatfunktioner, undtagen Tæl, null-værdier.
- Når der er angivet aggregatfunktioner sammen med delsætningen OVER, er delsætningen ORDER BY ikke tilladt i delsætningen OVER.

### <a name="use-sum"></a>Brug SUM

I følgende eksempel adderes en samlet løn efter afdeling til hver input række:
 
    @result=
        SELECT 
            *,
            SUM(Salary) OVER( PARTITION BY DeptName ) AS TotalByDept
        FROM @employees;

Her er et output:

|EmpID|EmpName|DeptName|DeptID|Løn|TotalByDept
|-----|-------|--------|------|------|-----------
|1|Noah|Ingeniørarbejde|100|10000|60000
|2|Sophia|Ingeniørarbejde|100|20000|60000
|3|Liam|Ingeniørarbejde|100|30000|60000
|7|Mason|Administrerende|300|50000|50000
|4|Emma|HR|200|10000|30000
|5|Jacob|HR|200|10000|30000
|6|Olivia|HR|200|10000|30000
|8|Ava|Marketing|400|15000|25000
|9|Ethan|Marketing|400|10000|25000

### <a name="use-count"></a>Brug TÆL

I følgende eksempel adderes et ekstra felt til hver række til at vise de samlede antal medarbejdere i hver afdeling.

    @result =
        SELECT *, 
            COUNT(*) OVER(PARTITION BY DeptName) AS CountByDept 
        FROM @employees;

Resultat:

|EmpID|EmpName|DeptName|DeptID|Løn|CountByDept
|-----|-------|--------|------|------|-----------
|1|Noah|Ingeniørarbejde|100|10000|3
|2|Sophia|Ingeniørarbejde|100|20000|3
|3|Liam|Ingeniørarbejde|100|30000|3
|7|Mason|Administrerende|300|50000|1
|4|Emma|HR|200|10000|3
|5|Jacob|HR|200|10000|3
|6|Olivia|HR|200|10000|3
|8|Ava|Marketing|400|15000|2
|9|Ethan|Marketing|400|10000|2


### <a name="use-min-and-max"></a>Brug MIN og MAX

I følgende eksempel adderes et ekstra felt til hver række til at vise de laveste løn for hver afdeling:

    @result =
        SELECT 
            *,
            MIN(Salary) OVER( PARTITION BY DeptName ) AS MinSalary
        FROM @employees;

Resultaterne:

|EmpID|EmpName|DeptName|DeptID|Løn|MinSalary
|-----|-------|--------|------|-------------|----------------
|1|Noah|Ingeniørarbejde|100|10000|10000
|2|Sophia|Ingeniørarbejde|100|20000|10000
|3|Liam|Ingeniørarbejde|100|30000|10000
|7|Mason|Administrerende|300|50000|50000
|4|Emma|HR|200|10000|10000
|5|Jacob|HR|200|10000|10000
|6|Olivia|HR|200|10000|10000
|8|Ava|Marketing|400|15000|10000
|9|Ethan|Marketing|400|10000|10000

Erstatte MIN med Maks og derefter Prøv det.


## <a name="ranking-functions"></a>Rangordne funktioner

Rangeringsmodel funktioner returnerer en rangeringsmodel værdi (et langt) for hver række i hver partition som defineret af PARTITION ved og OVER delsætninger. Rækkefølgen af pladsen styres af den ORDER BY i delsætningen OVER.

Følgende understøttes rangordne funktioner:

- RANG
- DENSE_RANK 
- NTILE
- RÆKKENUMMER

**Syntaks:**

    [ RANK() | DENSE_RANK() | ROW_NUMBER() | NTILE(<numgroups>) ]
        OVER (
            [PARTITION BY <identifier, > …[n]]
            [ORDER BY <identifier, > …[n] [ASC|DESC]] 
    ) AS <alias>

- Delsætningen ORDER BY er valgfrit for rangering funktioner. Hvis ORDER BY angives bestemmer rækkefølgen af rangorden. Hvis ORDER BY ikke er angivet, og derefter U-SQL tildeler værdier baseret på rækkefølgen læser post. Derfor føre til ikke deterministisk værdi af række tal, plads eller tæt plads i tilfælde order by-delsætningen er ikke blev angivet.
- NTILE kræver et udtryk, der evalueres til et positivt heltal. Dette nummer Angiver antallet grupper, hvor hver partition skal opdeles. Dette id bruges kun sammen med NTILE rangordne funktionen. 

Du kan finde flere oplysninger om delsætningen OVER [U-SQL-reference]().

Rækkenummer, RANG og DENSE_RANK nummerere rækker i et vindue. I stedet for dække dem separat, er det mere intuitiv til at se, hvordan de reagerer på den samme input.

    @result =
    SELECT 
        *,
        ROW_NUMBER() OVER (PARTITION BY Vertical ORDER BY Latency) AS RowNumber,
        RANK() OVER (PARTITION BY Vertical ORDER BY Latency) AS Rank, 
        DENSE_RANK() OVER (PARTITION BY Vertical ORDER BY Latency) AS DenseRank 
    FROM @querylog;
        
Bemærk OVER delsætninger er identiske. Resultat:

|Forespørgsel|Ventetid: heltal|Lodret|Rækkenr|Rang|DenseRank
|-----|-----------|--------|--------------|---------|--------------
|Bananer|300|Billede af|1|1|1
|Kirsebær|300|Billede af|2|1|1
|Durian|500|Billede af|3|3|2
|Apple|100|Web|1|1|1
|Fig|200|Web|2|2|2
|Papaya|200|Web|3|2|2
|Fig|300|Web|4|4|3
|Kirsebær|400|Web|5|5|4
|Durian|500|Web|6|6|5

### <a name="rownumber"></a>RÆKKENUMMER

I hvert vindue (lodret, billede eller Web), række tal øges med 1 sorteret efter ventetid.  

![U-SQL-vinduet funktionen rækkenummer](./media/data-lake-analytics-use-windowing-functions/u-sql-windowing-function-row-number-result.png)

### <a name="rank"></a>RANG

Forskellig fra ROW_NUMBER() RANK() tager højde for værdien af den ventetid, som er angivet i delsætningen ORDER BY for vinduet.

RANG starter med (1,1,3), fordi de første to værdier for ventetid er den samme. Derefter er den næste værdi 3, fordi værdien ventetid er gået videre til 500. Tasten Peg er, at selvom dublerede værdier, der får den samme rang, rangordensnummeret "springer over" til den næste rækkenummer værdi. Du kan se dette mønster Gentag med sekvens (2,2,4) i Web-lodret.

![U-SQL-vinduet funktionen plads](./media/data-lake-analytics-use-windowing-functions/u-sql-windowing-function-rank-result.png)

### <a name="denserank"></a>DENSE_RANK
    
DENSE_RANK er ligesom RANGERE bortset fra det ikke "springe" til den næste rækkenummer i stedet for det går til det næste nummer i rækkefølgen. Bemærk serier (1,1,2) og (2,2,3) i stikprøven.

![U-SQL-vinduet funktionen DENSE_RANK](./media/data-lake-analytics-use-windowing-functions/u-sql-windowing-function-dense-rank-result.png)

### <a name="remarks"></a>Bemærkninger

- Hvis ORDER BY ikke er angivet end rangordne funktionen anvendes på rækkesættet uden en hvilken som helst rækkefølge. Det vil resultere i ikke deterministisk funktionsmåde på hvordan rangordne funktionen anvendes
- Der er ingen garanti, de rækker, der returneres af en forespørgsel med rækkenummer sorteres præcis det samme med hver udførelse af medmindre følgende betingelser er opfyldt.

    - Værdierne i kolonnen partitioneret er entydige.
    - Værdierne i kolonnerne ORDER BY er entydige.
    - Kombinationer af værdierne for partition kolonne og ORDER BY-kolonner er entydige.

### <a name="ntile"></a>NTILE

NTILE fordeles rækker i en sorteret partition til et angivet antal grupper. Grupperne er nummererede, startende med en. 


I følgende eksempel opdeler sæt rækker i hver partition (lodret) i 4 grupper i den rækkefølge, forespørgselsventetid, og returnerer gruppen antallet for hver række. 

Billede af lodret har 3 rækker, og som derfor det har 3 grupper. 

Web lodret har 6 rækker, og de to ekstra rækker fordeles på de første to grupper. Der er grunden til, at der er 2 rækker i gruppen 1 og 2, og kun 1 række i gruppere 3 og Gruppér 4.  

    @result =
        SELECT 
            *,
            NTILE(4) OVER(PARTITION BY Vertical ORDER BY Latency) AS Quartile   
        FROM @querylog;
        
Resultaterne:

|Forespørgsel|Ventetid|Lodret|Kvartil
|-----|-----------|--------|-------------
|Bananer|300|Billede af|1
|Kirsebær|300|Billede af|2
|Durian|500|Billede af|3
|Apple|100|Web|1
|Fig|200|Web|1
|Papaya|200|Web|2
|Fig|300|Web|2
|Kirsebær|400|Web|3
|Durian|500|Web|4

NTILE kræver en parameter ("numgroups"). Numgroups er et positivt heltal eller længe konstant udtryk, der angiver antallet af grupper, hvor hver partition skal opdeles. 

- Hvis antallet af rækker i partitionen er jævnt divideres med numgroups har grupperne samme størrelse. 
- Hvis antallet af rækker i en partition ikke kan deles med numgroups, medfører dette grupper af to størrelser, der er forskellige ved et medlem. Større grupper kommer før mindre grupper i den rækkefølge, der er angivet af delsætningen OVER. 

Eksempel:

- 100 rækker, der er inddelt i 4 grupper: [25, 25, 25, 25]
- 102 rækker devided i 4 grupper: [26, 26, 25, 25]


### <a name="top-n-records-per-partition-via-rank-denserank-or-rownumber"></a>Øverste N poster per Partition via RANG, DENSE_RANK eller rækkenummer

Mange brugere vil vælge kun ØVERSTE n rækker hver gruppe. Dette er ikke muligt med den traditionelle GROUP BY. 

Du har fået vist i følgende eksempel i starten af afsnittet rangering funktioner. Det vises ikke øverste N-poster for hver partition:

    @result =
    SELECT 
        *,
        ROW_NUMBER() OVER (PARTITION BY Vertical ORDER BY Latency) AS RowNumber,
        RANK() OVER (PARTITION BY Vertical ORDER BY Latency) AS Rank,
        DENSE_RANK() OVER (PARTITION BY Vertical ORDER BY Latency) AS DenseRank
    FROM @querylog;

Resultaterne:

|Forespørgsel|Ventetid|Lodret|Rang|DenseRank|Rækkenr
|-----|-----------|--------|---------|--------------|--------------
|Bananer|300|Billede af|1|1|1
|Kirsebær|300|Billede af|1|1|2
|Durian|500|Billede af|3|2|3
|Apple|100|Web|1|1|1
|Fig|200|Web|2|2|2
|Papaya|200|Web|2|2|3
|Fig|300|Web|4|3|4
|Kirsebær|400|Web|5|4|5
|Durian|500|Web|6|5|6

### <a name="top-n-with-dense-rank"></a>ØVERSTE N med tæt RANG

I følgende eksempel returnerer de øverste 3 poster fra hver gruppe med ikke er huller i den fortløbende plads nummerering af rækker i hver windowing partition.

    @result =
    SELECT 
        *,
        DENSE_RANK() OVER (PARTITION BY Vertical ORDER BY Latency) AS DenseRank
    FROM @querylog;
    
    @result = 
        SELECT *
        FROM @result
        WHERE DenseRank <= 3;

Resultaterne:

|Forespørgsel|Ventetid|Lodret|DenseRank
|-----|-----------|--------|--------------
|Bananer|300|Billede af|1
|Kirsebær|300|Billede af|1
|Durian|500|Billede af|2
|Apple|100|Web|1
|Fig|200|Web|2
|Papaya|200|Web|2
|Fig|300|Web|3

### <a name="top-n-with-rank"></a>ØVERSTE N med RANG

    @result =
        SELECT 
            *,
            RANK() OVER (PARTITION BY Vertical ORDER BY Latency) AS Rank
        FROM @querylog;
    
    @result = 
        SELECT *
        FROM @result
        WHERE Rank <= 3;

Resultaterne:    

|Forespørgsel|Ventetid|Lodret|Rang
|-----|-----------|--------|---------
|Bananer|300|Billede af|1
|Kirsebær|300|Billede af|1
|Durian|500|Billede af|3
|Apple|100|Web|1
|Fig|200|Web|2
|Papaya|200|Web|2


### <a name="top-n-with-rownumber"></a>ØVERSTE N med rækkenummer

    @result =
        SELECT 
            *,
            ROW_NUMBER() OVER (PARTITION BY Vertical ORDER BY Latency) AS RowNumber
        FROM @querylog;
    
    @result = 
        SELECT *
        FROM @result
        WHERE RowNumber <= 3;

Resultaterne:   
    
|Forespørgsel|Ventetid|Lodret|Rækkenr
|-----|-----------|--------|--------------
|Bananer|300|Billede af|1
|Kirsebær|300|Billede af|2
|Durian|500|Billede af|3
|Apple|100|Web|1
|Fig|200|Web|2
|Papaya|200|Web|3

### <a name="assign-globally-unique-row-number"></a>Tildele globalt entydige rækkenummeret

Det er ofte nyttigt at tildele et globalt entydigt tal til hver række. Det er nemt (og mere effektivt end ved hjælp af et reduktionsrør) med funktionerne rangering.

    @result =
        SELECT 
            *,
            ROW_NUMBER() OVER () AS RowNumber
        FROM @querylog;

<!-- ################################################### -->
## <a name="analytic-functions"></a>Analytisk funktioner

Analytisk funktion bruges til at forstå fordeling af værdier i windows. De mest almindelige scenarie til brug af analytisk funktioner er beregning af fraktiler.

**Understøttede analytisk vinduet funktioner**

- CUME_DIST 
- PERCENT_RANK
- PERCENTILE_CONT
- PERCENTILE_DISC

### <a name="cumedist"></a>CUME_DIST  

CUME_DIST beregner den relative placering af en bestemt værdi i en gruppe af værdier. Det beregner procentdelen af forespørgsler, der har en ventetid, der er mindre end eller lig med den aktuelle forespørgselsventetid i den samme lodret. Hvis du allerede har stigende rækkefølge, CUME_DIST R er antallet af rækker med værdier, der er mindre end eller lig med værdien af R, til en række R divideret med antallet af rækker, der evalueres i resultatsættet partition eller forespørgsel. CUME_DIST returnerer tal i området 0 < x < = 1.

**Syntaksen for**

    CUME_DIST() 
        OVER (
            [PARTITION BY <identifier, > …[n]]
            ORDER BY <identifier, > …[n] [ASC|DESC] 
    ) AS <alias>

I følgende eksempel bruges funktionen CUME_DIST til at beregne ventetid fraktilen for hver forespørgsel i en lodret. 

    @result=
        SELECT 
            *,
            CUME_DIST() OVER(PARTITION BY Vertical ORDER BY Latency) AS CumeDist
        FROM @querylog;

Resultaterne:
    
|Forespørgsel|Ventetid|Lodret|CumeDist
|-----|-----------|--------|---------------
|Durian|500|Billede af|1
|Bananer|300|Billede af|0.666666666666667
|Kirsebær|300|Billede af|0.666666666666667
|Durian|500|Web|1
|Kirsebær|400|Web|0.833333333333333
|Fig|300|Web|0.666666666666667
|Fig|200|Web|0,5
|Papaya|200|Web|0,5
|Apple|100|Web|0.166666666666667

Der er 6 rækker i den partition, hvor Partitionsnøgle er "Web" (4. række og ned):

- Der er 6 rækker med den værdi, der er lig med eller mindre end 500, så CUME_DIST er lig med 6/6 = 1
- Der er 5 rækker med den værdi, der er lig med eller lavere end 400, så CUME_DIST er lig med 5/6 = 0,83
- Der er 4 rækker med den værdi, der er lig med eller mindre end 300, så CUME_DIST er lig med 4/6 = 0.66
- Der er 3 rækker med den værdi, der er lig med eller mindre end 200, så CUME_DIST er lig med 3/6 = 0,5. Der findes to rækker med samme ventetid værdi.
- Der er 1 række med den værdi, der er lig med eller mindre end 100, så CUME_DIST er lig med 1/6 = 0,16. 


**Brug noter:**

- Bindingsværdier (Tie) evalueres altid til den samme kumulative fordelingsfunktion værdi.
- NULL-værdier behandles som de laveste mulige værdier.
- Du skal angive delsætningen ORDER BY for at beregne CUME_DIST.
- CUME_DIST ligner funktionen PERCENT_RANK

Bemærk: Delsætningen ORDER BY er ikke tilladt, hvis SELECT-sætningen ikke efterfølges af OUTPUT. Dermed bestemmer ORDER BY-delsætning i sætningen OUTPUT visningsrækkefølgen af gældende rækkesættet.


### <a name="percentrank"></a>PERCENT_RANK

PERCENT_RANK beregner den relative rang for en række i en gruppe af rækker. PERCENT_RANK bruges til at evaluere den relative placering af en værdi i et eller partition. Området af værdier, der returneres af PERCENT_RANK er større end 0 og mindre end eller lig med 1. I modsætning til CUME_DIST er PERCENT_RANK altid 0 for den første række.
    
**Syntaksen for**

    PERCENT_RANK() 
        OVER (
            [PARTITION BY <identifier, > …[n]]
            ORDER BY <identifier, > …[n] [ASC|DESC] 
        ) AS <alias>

**Noter**

- Den første række i en hvilken som helst angive har en PERCENT_RANK på 0.
- NULL-værdier behandles som de laveste mulige værdier.
- Du skal angive delsætningen ORDER BY for at beregne PERCENT_RANK.
- CUME_DIST ligner funktionen PERCENT_RANK 


I følgende eksempel bruges funktionen PERCENT_RANK til at beregne ventetid fraktilen for hver forespørgsel i en lodret. 

Delsætningen PARTITION BY er angivet til partition rækker i resultatsættet af en lodret. Delsætningen ORDER BY i delsætningen OVER ordrer rækkerne i hver partition. 

Den værdi, der er returneret af funktionen PERCENT_RANK repræsenterer rangen for de forespørgsler ventetid i en lodret som en procentdel. 


    @result=
        SELECT 
            *,
            PERCENT_RANK() OVER(PARTITION BY Vertical ORDER BY Latency) AS PercentRank
        FROM @querylog;

Resultaterne:

|Forespørgsel|Ventetid: heltal|Lodret|PROCENTPLADS
|-----|-----------|--------|------------------
|Bananer|300|Billede af|0
|Kirsebær|300|Billede af|0
|Durian|500|Billede af|1
|Apple|100|Web|0
|Fig|200|Web|0,2
|Papaya|200|Web|0,2
|Fig|300|Web|0,6
|Kirsebær|400|Web|0,8
|Durian|500|Web|1

### <a name="percentilecont--percentiledisc"></a>PERCENTILE_CONT & PERCENTILE_DISC

Disse to funktioner beregner fraktiler baseret på en fortløbende eller dedikeret fordeling af kolonneværdier.

**Syntaksen for**

    [PERCENTILE_CONT | PERCENTILE_DISC] ( numeric_literal ) 
        WITHIN GROUP ( ORDER BY <identifier> [ ASC | DESC ] )
        OVER ( [ PARTITION BY <identifier,>…[n] ] ) AS <alias>

**numeric_literal** - fraktil til at beregne. Værdien skal ligge mellem 0,0 og 1,0.

I GRUPPEN (SORTÉR efter <identifier> [ASC | DESC]) - angiver en liste over numeriske værdier til at sortere og beregne fraktilen. Kun én kolonne id er tilladt. Udtrykket skal evalueres til en numerisk type. Andre datatyper er ikke tilladt. Standardsorteringsrækkefølgen stigende.

OVER ([PARTITION efter < id, >... [n]]) - opdeler input rækkesættet i partitioner ud fra tasten partition, som funktionen fraktil anvendes. Du kan finde flere oplysninger, se RANGORDNE afsnit i dette dokument.
Bemærk: Alle nulværdier i datasættet, ignoreres.

**PERCENTILE_CONT** beregner en fraktil, der er baseret på en fortløbende fordeling af kolonneværdien for. Resultatet er interpoleret og kan ikke være lig med nogen af de specifikke værdier i kolonnen. 

**PERCENTILE_DISC** beregner den fraktil, der er baseret på en dedikeret fordeling af kolonneværdier. Resultatet er lig med en bestemt værdi i kolonnen. Det vil sige, PERCENTILE_DISC, i modsætning til PERCENTILE_CONT, returnerer altid en faktisk (oprindelige input) værdi.

Du kan se, hvordan begge fungerer i eksemplet herunder hvilke forsøg at finde medianen (fraktil = 0,50) værdi for ventetid i hver lodret

    @result = 
        SELECT 
            Vertical, 
            Query,
            PERCENTILE_CONT(0.5) 
                WITHIN GROUP (ORDER BY Latency)
                OVER ( PARTITION BY Vertical ) AS PercentileCont50,
            PERCENTILE_DISC(0.5) 
                WITHIN GROUP (ORDER BY Latency) 
                OVER ( PARTITION BY Vertical ) AS PercentileDisc50 
        
        FROM @querylog;

Resultaterne:

|Forespørgsel|Ventetid: heltal|Lodret|PercentileCont50|PercentilDisc50
|-----|-----------|--------|-------------------|----------------
|Bananer|300|Billede af|300|300
|Kirsebær|300|Billede af|300|300
|Durian|500|Billede af|300|300
|Apple|100|Web|250|200
|Fig|200|Web|250|200
|Papaya|200|Web|250|200
|Fig|300|Web|250|200
|Kirsebær|400|Web|250|200
|Durian|500|Web|250|200


For PERCENTILE_CONT fordi værdier kan være interpoleret, er medianen for web 250 selvom ingen forespørgslen i webdelen lodret havde en forsinkelse på 250. 

PERCENTILE_DISC ikke interpolere værdier, så medianen for Web er 200 - som er en faktisk værdien findes i feltet Indtast rækker.











## <a name="see-also"></a>Se også

- [Oversigt over Microsoft Azure Data sø Analytics](data-lake-analytics-overview.md)
- [Komme i gang med Data sø analyser ved hjælp af Azure-portalen](data-lake-analytics-get-started-portal.md)
- [Komme i gang med Data sø analyser ved hjælp af Azure PowerShell](data-lake-analytics-get-started-powershell.md)
- [Udvikle U-SQL-scripts ved hjælp af Data sø Tools til Visual Studio](data-lake-analytics-data-lake-tools-get-started.md)
- [Brug Azure Data sø Analytics interaktive selvstudier](data-lake-analytics-use-interactive-tutorials.md)
- [Analysere websted logfiler, der bruger Azure Data sø Analytics](data-lake-analytics-analyze-weblogs.md)
- [Introduktion til Azure Data sø Analytics U-SQL-sprog](data-lake-analytics-u-sql-get-started.md)
- [Administrere Azure Data sø analyser ved hjælp af Azure-portalen](data-lake-analytics-manage-use-portal.md)
- [Administrere Azure Data sø analyser ved hjælp af Azure PowerShell](data-lake-analytics-manage-use-powershell.md)
- [Overvåge og foretage fejlfinding af Azure Data sø Analytics job ved hjælp af Azure-portalen](data-lake-analytics-monitor-and-troubleshoot-jobs-tutorial.md)
