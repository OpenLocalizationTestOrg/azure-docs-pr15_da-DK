<properties 
    pageTitle="SQL-syntaks og SQL forespørge efter DocumentDB | Microsoft Azure" 
    description="Få mere at vide om SQL-syntaks, database begreber og SQL-forespørgsler til DocumentDB, en NoSQL database. SQL kan bruges som et JSON forespørgselssprog i DocumentDB." 
    keywords="SQL-syntaks, sql-forespørgsel, sql-forespørgsler, json forespørgselssprog, database begreber og sql-forespørgsler, aggregatfunktioner"
    services="documentdb" 
    documentationCenter="" 
    authors="arramac" 
    manager="jhubbard" 
    editor="monicar"/>

<tags 
    ms.service="documentdb" 
    ms.workload="data-services" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="10/27/2016" 
    ms.author="arramac"/>

# <a name="sql-query-and-sql-syntax-in-documentdb"></a>SQL-forespørgsel og SQL-syntaks i DocumentDB
Microsoft Azure DocumentDB understøtter forespørgsel dokumenter ved hjælp af SQL (Structured Query Language) som en JSON forespørgselssprog. DocumentDB er helt fri for skemaet. I henhold til dens ønske at JSON-datamodel direkte i af-databaseprogram kan automatisk indeksering af JSON dokumenter uden at kræve eksplicitte skema eller oprettelse af sekundære indeks. 

Når du designer forespørgselssproget til DocumentDB havde vi to mål:

-   I stedet for inventing en ny JSON forespørgselssprog, ville vi understøtter SQL. SQL er et af de velkendte og mest populære forespørgselssprog. DocumentDB SQL indeholder en formel programming model til omfattende forespørgsler over JSON dokumenter.
-   Som en JSON dokument database stand til at udføre JavaScript direkte i database engine, ville vi bruge JavaScript programming model som grundlaget for vores forespørgselssprog. DocumentDB SQL rod i JavaScript type system, evaluering af udtryk og funktion aktivering. Denne slå indeholder en naturlig programming model til relationelle prognoser, hierarkisk navigation på tværs af JSON dokumenter, selv joinforbindelser, geografiske forespørgsler og aktivering af brugerdefinerede funktioner skrevet helt på JavaScript blandt andre funktioner. 

Vi mener, at disse funktioner er nede for at reducere gnidning mellem programmet og databasen og er afgørende for udviklerproduktivitet.

Vi anbefaler, at komme i gang ved at se den følgende video, hvor viser Aravind Ramachandran Documentdbs forespørgsel funktioner, og ved at besøge vores [Forespørgsel tennisklub](http://www.documentdb.com/sql/demo), hvor du kan prøve DocumentDB og kører SQL-forespørgsler i forhold til vores datasæt.

> [AZURE.VIDEO dataexposedqueryingdocumentdb]

Derefter gå tilbage til denne artikel, hvor vi begynder med en SQL-forespørgsel-selvstudium, der vejleder dig gennem nogle enkle JSON-dokumenter og SQL-kommandoer.

## <a name="getting-started-with-sql-commands-in-documentdb"></a>Introduktion til SQL-kommandoer i DocumentDB
For at få vist DocumentDB SQL på arbejdet, Lad os begynde med et par enkle JSON-dokumenter og gennemgå nogle enkle forespørgsler mod den. Overvej disse to JSON dokumenter om to familier. Bemærk, at med DocumentDB, vi ikke behøver at oprette en hvilken som helst ved hjælp af skemaer eller sekundære indekser eksplicit. Vi skal blot indsætte JSON dokumenter til en DocumentDB af websteder og efterfølgende forespørgsel. Her har vi en simpel JSON dokumentoplysninger for Andersen familien, overordnede, børn (og deres kæledyr), adresse og registrering. Dokumentet har strenge, tal, booleske værdier, matrixer og indlejrede egenskaber. 

**Dokument**  

    {
        "id": "AndersenFamily",
        "lastName": "Andersen",
        "parents": [
           { "firstName": "Thomas" },
           { "firstName": "Mary Kay"}
        ],
        "children": [
           {
               "firstName": "Henriette Thaulow", "gender": "female", "grade": 5,
               "pets": [{ "givenName": "Fluffy" }]
           }
        ],
        "address": { "state": "WA", "county": "King", "city": "seattle" },
        "creationDate": 1431620472,
        "isRegistered": true
    }


Her er et andet dokument med et lille forskel – `givenName` og `familyName` bruges i stedet for `firstName` og `lastName`.

**Dokument**  

    {
        "id": "WakefieldFamily",
        "parents": [
            { "familyName": "Wakefield", "givenName": "Robin" },
            { "familyName": "Miller", "givenName": "Ben" }
        ],
        "children": [
            {
                "familyName": "Merriam", 
                "givenName": "Jesse", 
                "gender": "female", "grade": 1,
                "pets": [
                    { "givenName": "Goofy" },
                    { "givenName": "Shadow" }
                ]
            },
            { 
                "familyName": "Miller", 
                 "givenName": "Lisa", 
                 "gender": "female", 
                 "grade": 8 }
        ],
        "address": { "state": "NY", "county": "Manhattan", "city": "NY" },
        "creationDate": 1431620462,
        "isRegistered": false
    }



Nu skal du prøve et par forespørgsler disse data til forstå nogle af de vigtigste aspekter af DocumentDB SQL. For eksempel følgende forespørgsel returnerer dokumenterne, der hvor id-feltet svarer til `AndersenFamily`. Eftersom den er en `SELECT *`, output fra forespørgslen er fuldført JSON dokumentet:

**Forespørgsel**

    SELECT * 
    FROM Families f 
    WHERE f.id = "AndersenFamily"

**Resultater**

    [{
        "id": "AndersenFamily",
        "lastName": "Andersen",
        "parents": [
           { "firstName": "Thomas" },
           { "firstName": "Mary Kay"}
        ],
        "children": [
           {
               "firstName": "Henriette Thaulow", "gender": "female", "grade": 5,
               "pets": [{ "givenName": "Fluffy" }]
           }
        ],
        "address": { "state": "WA", "county": "King", "city": "seattle" },
        "creationDate": 1431620472,
        "isRegistered": true
    }]


Nu kan du overveje de tilfælde, hvor vi vil omformatere JSON output i en anden figur. Denne forespørgsel projicerer et nyt JSON objekt med to markerede felter, navn og by, når den adresse, by har samme navn som tilstanden. I dette tilfælde "NY, NY" svarer til.

**Forespørgsel**   

    SELECT {"Name":f.id, "City":f.address.city} AS Family 
    FROM Families f 
    WHERE f.address.city = f.address.state

**Resultater**

    [{
        "Family": {
            "Name": "WakefieldFamily", 
            "City": "NY"
        }
    }]


Næste forespørgslen returnerer alle de angivne navne af børn i serien, id svarer til `WakefieldFamily` sorteret efter by bopælsperioder.

**Forespørgsel**

    SELECT c.givenName 
    FROM Families f 
    JOIN c IN f.children 
    WHERE f.id = 'WakefieldFamily'
    ORDER BY f.address.city ASC

**Resultater**

    [
      { "givenName": "Jesse" }, 
      { "givenName": "Lisa"}
    ]


Vi vil gerne henlede opmærksomheden på et par vigtige aspekter af forespørgselssprog DocumentDB gennem eksemplerne vi har set indtil nu:  
 
-   Da DocumentDB SQL fungerer på JSON værdier, omhandler den træet formet enheder i stedet for rækker og kolonner. Derfor sproget, der gør det muligt at referere til noder i træet i en vilkårlig dybde ønsker `Node1.Node2.Node3…..Nodem`, svarende til relationelle SQL referere til to del referencen til `<table>.<column>`.   
-   Den strukturerede forespørgselssprog fungerer sammen med skema mindre data. Derfor skal Typesystemet være bundet dynamisk. Samme udtryk kan medføre forskellige typer på forskellige dokumenter. Resultatet af en forespørgsel er en gyldig JSON-værdi, men nødvendigvis ikke være et fast skema.  
-   DocumentDB understøtter kun strenge JSON-dokumenter. Det betyder, at type system og udtryk er begrænset til kun håndtere JSON typer. Se [JSON specifikation](http://www.json.org/) for flere oplysninger.  
-   En DocumentDB af websteder er en gratis skema beholder af JSON dokumenter. Relationer i data enheder i og på tværs af dokumenter i en samling er implicit hentes efter beholdning og ikke efter primær nøgle og fremmed nøgle-relationer. Dette er et vigtigt aspekt værd fremhæver på baggrund af handel dokument joinforbindelserne beskrives senere i denne artikel.

## <a name="documentdb-indexing"></a>DocumentDB indeksering

Før vi dykke ned DocumentDB SQL-syntaks, er det værd udforske indeksering designet i DocumentDB. 

Formålet med databaseindeks er at kunne levere forespørgsler i deres forskellige former og figurer med mindste ressourceforbrug (som CPU og input/output) samtidig med at god hastighed og kort ventetid. Valg af det rigtige indeks til at forespørge en database kræver ofte meget planlægning og forsøg. Denne metode udgør en udfordring for skema mindre databaser, hvor dataene ikke overens med en faste skemaet og udvikles hurtigt. 

Derfor, når vi designet DocumentDB indeksering undersystem, vi angive følgende mål:

-   Indeksere dokumenter uden at kræve skema: indeksering undersystem ikke kræver en hvilken som helst skemaoplysninger eller foretage en hvilken som helst antagelser om skema for dokumenter. 

-   Support til effektive og omfattende hierarkiske, og relationelle forespørgsler: indekset understøtter forespørgselssprog DocumentDB effektivt, herunder understøttelse af hierarkiske og relationelle prognoser.

-   Understøttelse af ensartet forespørgsler in face of en vedvarende mængde skriver: høj Skriv overførselshastighed arbejdsmængder med ensartet forespørgsler, indekset opdateres trinvist, effektivt og online trods en vedvarende mængde skriver. Opdateringen ensartet indeks er vigtigt at kunne levere forespørgsler på niveauet konsistens, hvori brugeren konfigureret dokumenttjenesten.

-   Understøttelse af flere arkitekturer: givet reservation baseret modellen for ressource styring på tværs af lejere, indeks opdateringer udføres inden for budgettet system ressourcer (CPU, hukommelse og input/output-operationer sekundet) fordelt replika. 

-   Lagerplads effektivitet: omkostningseffektivitet, er på disken lagerplads spild af indekset afgrænset og mere forudsigelige. Det er afgørende, fordi DocumentDB kan udvikler indgå omkostninger baseret kompromiser mellem indeks faste i forhold til forespørgslens ydeevne.  

Se i [DocumentDB eksempler](https://github.com/Azure/azure-documentdb-net) på MSDN for eksempler viser, hvordan du konfigurerer politikken indeksering for en samling. Lad os nu få vist flere detaljer i DocumentDB SQL-syntaks.


## <a name="basics-of-a-documentdb-sql-query"></a>Grundlæggende om en DocumentDB SQL-forespørgsel
Alle forespørgsler, der består af en SELECT-delsætning, og valgfri FROM og WHERE-delsætninger per ANSI SQL-standarder. Typisk for hver forespørgsel er kilden i FROM-delsætningen optalt. Derefter filteret i WHERE-delsætningen anvendes på kilden til at hente et undersæt af JSON dokumenter. Til sidst skal bruges SELECT-delsætningen Project de ønskede JSON-værdier i select-listen.
    
    SELECT [TOP <top_expression>] <select_list> 
    [FROM <from_specification>] 
    [WHERE <filter_condition>]
    [ORDER BY <sort_specification]    


## <a name="from-clause"></a>FROM-delsætning
Den `FROM <from_specification>` delsætning er valgfrit, medmindre kilden er filtreret eller forventet senere i forespørgslen. Formålet med denne delsætning er til at angive den datakilde, som forespørgslen skal fungere. Hele gruppen af websteder er ofte kilden, men kan du angive et undersæt af gruppen af websteder i stedet. 

En forespørgsel som `SELECT * FROM Families` angiver, at hele familiemedlemmer samlingen er kilden over der skal optælles. En speciel id rod kan bruges til at repræsentere samlingen i stedet for med samlingsnavnet. Følgende liste indeholder de regler, håndhæves per forespørgsel:

- Gruppen af websteder kan være alias, såsom `SELECT f.id FROM Families AS f` eller blot `SELECT f.id FROM Families f`. Her `f` svarer til `Families`. `AS`er en valgfri nøgleord til alias-id'et.

-   Bemærk det én gang alias, den oprindelige kilde kan ikke bindes. For eksempel `SELECT Families.id FROM Families f` er syntaktisk forkert, da id "Familier" ikke kan løses længere.

-   Alle egenskaber, der skal bruges til reference skal være fuldstændigt. Dette gennemtvinges i fravær af faste skema overholdelse, for at undgå eventuelle tvetydige bindinger. Derfor `SELECT id FROM Families f` er syntaktisk ugyldige siden egenskaben `id` ikke er bundet.
    
### <a name="sub-documents"></a>Underordnede dokumenter
Kilden kan også være begrænset til et mindre undersæt. For eksempel til optælling af et underordnet træ i hvert dokument, kan underordnede roden derefter bliver kilden, som vist i følgende eksempel.

**Forespørgsel**

    SELECT * 
    FROM Families.children

**Resultater**  

    [
      [
        {
            "firstName": "Henriette Thaulow",
            "gender": "female",
            "grade": 5,
            "pets": [
              {
                  "givenName": "Fluffy"
              }
            ]
        }
      ],
      [
        {
            "familyName": "Merriam",
            "givenName": "Jesse",
            "gender": "female",
            "grade": 1
        },
        {
            "familyName": "Miller",
            "givenName": "Lisa",
            "gender": "female",
            "grade": 8
        }
      ]
    ]

Mens eksemplet ovenfor bruges en matrix som kilde, kan et objekt også bruges som kilde, som er, hvad der skal vises i eksemplet nedenfor. Alle gyldige JSON værdier (ikke Udefineret), der kan findes i kilden anses skal indgå i resultatet af forespørgslen. Hvis nogle skrifttypefamilier ikke har en `address.state` værdi, der skal udelukkes i resultatet af forespørgslen.

**Forespørgsel**

    SELECT * 
    FROM Families.address.state

**Resultater**

    [
      "WA", 
      "NY"
    ]


## <a name="where-clause"></a>WHERE-delsætning
WHERE-delsætningen (**`WHERE <filter_condition>`**) er valgfrit. Det angiver den eller de betingelser, JSON-dokumenter, der leveres af kilden skal opfylde for at blive medtaget som en del af resultatet. En hvilken som helst JSON dokument skal evalueres de angivne betingelser til "sand" anses for resultatet. WHERE-delsætningen bruges af indeks lag for at finde ud af det absolutte mindste undersæt af kildedokumenter, der kan være en del af resultatet. 

Følgende forespørgsel anmoder om dokumenter, der indeholder en hvis værdi er navnet i egenskaben `AndersenFamily`. Ethvert andet dokument, der ikke har en-egenskaben name, eller hvor værdien stemmer ikke overens `AndersenFamily` udelades. 

**Forespørgsel**

    SELECT f.address
    FROM Families f 
    WHERE f.id = "AndersenFamily"

**Resultater**

    [{
      "address": {
        "state": "WA", 
        "county": "King", 
        "city": "seattle"
      }
    }]


Det forrige eksempel viste en enkel lige forespørgsel. DocumentDB SQL understøtter også en lang række skalær udtryk. De mest almindeligt anvendte er binær- og monadiske udtryk. Egenskaben referencer fra JSON kildeobjektet er også gyldigt udtryk. 

Følgende binære operatorer understøttes i øjeblikket og kan bruges i forespørgsler, som vist i følgende eksempler:  
<table>
<tr>
<td>Aritmetiske</td> 
<td>+,-,*,/,%</td>
</tr>
<tr>
<td>Bitvis</td>    
<td>|, &, ^, <<, >>, >>> (nul fyld højre Skift) </td>
</tr>
<tr>
<td>Logisk</td>
<td>OG, ELLER EJ</td>
</tr>
<tr>
<td>Sammenligning</td> 
<td>=, !=, &lt;, &gt;, &lt;=, &gt;=, <></td>
</tr>
<tr>
<td>Streng</td> 
<td>|| (sammenkædning)</td>
</tr>
</table>  

Lad os se nærmere på nogle forespørgsler ved hjælp af binære operatorer.

    SELECT * 
    FROM Families.children[0] c
    WHERE c.grade % 2 = 1     -- matching grades == 5, 1
    
    SELECT * 
    FROM Families.children[0] c
    WHERE c.grade ^ 4 = 1    -- matching grades == 5
    
    SELECT *
    FROM Families.children[0] c
    WHERE c.grade >= 5     -- matching grades == 5


Monadiske +,-, ~ og ikke understøttes også og kan bruges i forespørgsler, som vist i følgende eksempel:

    SELECT *
    FROM Families.children[0] c
    WHERE NOT(c.grade = 5)  -- matching grades == 1
    
    SELECT *
    FROM Families.children[0] c
    WHERE (-c.grade = -5)  -- matching grades == 5



Egenskaben referencer er også tilladt foruden binær- og monadiske operatorer. For eksempel `SELECT * FROM Families f WHERE f.isRegistered` returnerer JSON dokumentet med egenskaben `isRegistered` hvor indstillingen af egenskaben værdi er lig med JSON `true` værdi. Andre værdier (FALSK, null, ikke er defineret, `<number>`, `<string>`, `<object>`, `<array>`, osv.) fører til kildedokumentet, idet der ses bort fra resultatet. 

### <a name="equality-and-comparison-operators"></a>Lighed og sammenligning af operatorer
Følgende tabel viser resultatet af lighed sammenligninger i DocumentDB SQL mellem en hvilken som helst to typer JSON.
<table style = "width:300px">
   <tbody>
      <tr>
         <td valign="top">
            <strong>Op</strong>
         </td>
         <td valign="top">
            <strong>Ikke defineret</strong>
         </td>
         <td valign="top">
            <strong>Null</strong>
         </td>
         <td valign="top">
            <strong>Boolesk værdi</strong>
         </td>
         <td valign="top">
            <strong>Tal</strong>
         </td>
         <td valign="top">
            <strong>Streng</strong>
         </td>
         <td valign="top">
            <strong>Objekt</strong>
         </td>
         <td valign="top">
            <strong>Matrix</strong>
         </td>
      </tr>
      <tr>
         <td valign="top">
            <strong>Ikke defineret<strong>
         </td>
         <td valign="top">
Ikke defineret </td>
         <td valign="top">
Ikke defineret </td>
         <td valign="top">
Ikke defineret </td>
         <td valign="top">
Ikke defineret </td>
         <td valign="top">
Ikke defineret </td>
         <td valign="top">
Ikke defineret </td>
         <td valign="top">
Ikke defineret </td>
      </tr>
      <tr>
         <td valign="top">
            <strong>Null<strong>
         </td>
         <td valign="top">
Ikke defineret </td>
         <td valign="top">
            <strong>Ok</strong>
         </td>
         <td valign="top">
Ikke defineret </td>
         <td valign="top">
Ikke defineret </td>
         <td valign="top">
Ikke defineret </td>
         <td valign="top">
Ikke defineret </td>
         <td valign="top">
Ikke defineret </td>
      </tr>
      <tr>
         <td valign="top">
            <strong>Boolesk værdi<strong>
         </td>
         <td valign="top">
Ikke defineret </td>
         <td valign="top">
Ikke defineret </td>
         <td valign="top">
            <strong>Ok</strong>
         </td>
         <td valign="top">
Ikke defineret </td>
         <td valign="top">
Ikke defineret </td>
         <td valign="top">
Ikke defineret </td>
         <td valign="top">
Ikke defineret </td>
      </tr>
      <tr>
         <td valign="top">
            <strong>Tal<strong>
         </td>
         <td valign="top">
Ikke defineret </td>
         <td valign="top">
Ikke defineret </td>
         <td valign="top">
Ikke defineret </td>
         <td valign="top">
            <strong>Ok</strong>
         </td>
         <td valign="top">
Ikke defineret </td>
         <td valign="top">
Ikke defineret </td>
         <td valign="top">
Ikke defineret </td>
      </tr>
      <tr>
         <td valign="top">
            <strong>Streng<strong>
         </td>
         <td valign="top">
Ikke defineret </td>
         <td valign="top">
Ikke defineret </td>
         <td valign="top">
Ikke defineret </td>
         <td valign="top">
Ikke defineret </td>
         <td valign="top">
            <strong>Ok</strong>
         </td>
         <td valign="top">
Ikke defineret </td>
         <td valign="top">
Ikke defineret </td>
      </tr>
      <tr>
         <td valign="top">
            <strong>Objekt<strong>
         </td>
         <td valign="top">
Ikke defineret </td>
         <td valign="top">
Ikke defineret </td>
         <td valign="top">
Ikke defineret </td>
         <td valign="top">
Ikke defineret </td>
         <td valign="top">
Ikke defineret </td>
         <td valign="top">
            <strong>Ok</strong>
         </td>
         <td valign="top">
Ikke defineret </td>
      </tr>
      <tr>
         <td valign="top">
            <strong>Matrix<strong>
         </td>
         <td valign="top">
Ikke defineret </td>
         <td valign="top">
Ikke defineret </td>
         <td valign="top">
Ikke defineret </td>
         <td valign="top">
Ikke defineret </td>
         <td valign="top">
Ikke defineret </td>
         <td valign="top">
Ikke defineret </td>
         <td valign="top">
            <strong>Ok</strong>
         </td>
      </tr>
   </tbody>
</table>

For andre sammenligningsoperatorer som >, > =,! =, < og < = følgende regler gælder:   

-   Sammenligning af forskellige typer resulterer i udefineret.
-   Sammenligning mellem to objekter eller to matrixer resultater i udefineret.   

Hvis resultatet af skalarudtryk i filteret er ikke defineret, tilsvarende dokumentet vil ikke medtages i resultatet, da Udefineret logisk ikke svarer til "true".

### <a name="between-keyword"></a>MELLEM nøgleord
Du kan også bruge nøgleordet BETWEEN til at udtrykke forespørgsler områder af værdier som i ANSI SQL. MELLEM kan bruges mod strenge eller tal/bogstaver.

For eksempel returnerer denne forespørgsel alle family dokumenter, hvor det første underordnede grade er mellem 1-5 (begge inklusive). 

    SELECT *
    FROM Families.children[0] c
    WHERE c.grade BETWEEN 1 AND 5

I modsætning til i ANSI SQL, kan du også bruge delsætningen BETWEEN i FROM-delsætningen som i følgende eksempel.

    SELECT (c.grade BETWEEN 0 AND 10)
    FROM Families.children[0] c

Husk at oprette en politik for indeksering, der anvender et indeks områdetype mod en hvilken som helst numeriske egenskaber/stier, der er filtreret i delsætningen BETWEEN for hurtigere forespørgsel udførelse af tidspunkter. 

Primære forskellen mellem at bruge BETWEEN i DocumentDB og ANSI SQL er, at du kan express egenskaber for blandede typer område forespørgsler – for eksempel du muligvis har "kategori" være et tal (5) i nogle dokumenter og strenge i andre ("grade4"). I disse tilfælde ignoreres som i JavaScript, en sammenligning mellem to forskellige typer resultater i "Udefineret", og dokumentet.

### <a name="logical-and-or-and-not-operators"></a>Logisk (AND, OR og ikke) operatorer
Logiske operatorer fungerer på booleske værdier. Logiske sandheden tabeller til disse operatorer er vist i følgende tabeller.

ELLER|SAND|FALSK|Ikke defineret
---|---|---|---
SAND|SAND|SAND|SAND
FALSK|SAND|FALSK|Ikke defineret
Ikke defineret|SAND|Ikke defineret|Ikke defineret

OG|SAND|FALSK|Ikke defineret
---|---|---|---
SAND|SAND|FALSK|Ikke defineret
FALSK|FALSK|FALSK|FALSK
Ikke defineret|Ikke defineret|FALSK|Ikke defineret

IKKE|  |
---|---
SAND|FALSK
FALSK|SAND
Ikke defineret|Ikke defineret

### <a name="in-keyword"></a>NØGLEORD
Nøgleordet IN kan bruges til at kontrollere, om en bestemt værdi svarer til en værdi i en liste. For eksempel returnerer denne forespørgsel alle family dokumenter hvor id'et er en af "WakefieldFamily" eller "AndersenFamily". 
 
    SELECT *
    FROM Families 
    WHERE Families.id IN ('AndersenFamily', 'WakefieldFamily')

I dette eksempel returnerer alle dokumenter, hvor tilstanden er en af de angivne værdier.

    SELECT *
    FROM Families 
    WHERE Families.address.state IN ("NY", "WA", "CA", "PA", "OH", "OR", "MI", "WI", "MN", "FL")

### <a name="ternary--and-coalesce--operators"></a>Ternær (?) og samling (?) operatorer
Ternær og samling operatorerne kan bruges til at oprette betingede udtryk, der ligner populære programmeringssprog som C# og JavaScript. 

Operatoren Ternær (?) kan være meget nyttige, når bygning af nye JSON-egenskaber på farten. For eksempel kan du nu skrive forespørgsler for at klassificere klasse niveauer i en human læsbar form som begynder/mellemniveau/Avanceret, som vist nedenfor.
 
     SELECT (c.grade < 5)? "elementary": "other" AS gradeLevel 
     FROM Families.children[0] c

Du kan også indlejre opkald til operatoren like i forespørgslen nedenfor.
 
    SELECT (c.grade < 5)? "elementary": ((c.grade < 9)? "junior": "high")  AS gradeLevel 
    FROM Families.children[0] c

Som med andre forespørgsel operatorer udelades Hvis egenskaberne der refereres til i et betinget udtryk mangler i et dokument, eller hvis de typer, der sammenlignes er forskellige, derefter disse dokumenter i forespørgselsresultaterne.

Operatoren samling (?) kan bruges til at kontrollere effektivt (kaldes også for tilstedeværelse af en egenskab defineres) i et dokument. Dette er nyttigt, når forespørgsel semistrukturerede eller blandede typer data. For eksempel returnerer denne forespørgsel "Efternavn" Hvis den findes, eller "Efternavn" Hvis det ikke er til stede.

    SELECT f.lastName ?? f.surname AS familyName
    FROM Families f

### <a name="quoted-property-accessor"></a>Anførselstegn Egenskabstilgang
Du kan også få adgang til egenskaber for brug af operatoren tilbudte egenskaben `[]`. For eksempel `SELECT c.grade` og `SELECT c["grade"]` er ækvivalente. Denne syntaks er nyttig, når du har brug at undgå en egenskab, der indeholder mellemrum, specialtegn, eller der sker med dele det samme navn som en SQL-nøgleord eller reserveret ord.

    SELECT f["lastName"]
    FROM Families f
    WHERE f["id"] = "AndersenFamily"


## <a name="select-clause"></a>SELECT-delsætning
SELECT-delsætningen (**`SELECT <select_list>`**) er obligatorisk og angiver, hvilke værdier der skal hentes fra forespørgslen, på samme måde som i ANSI-SQL. Det undersæt, der er blevet filtreret oven på dokumentets kilde overføres til den projicering fase, hvor de angivne JSON-værdier er hentet og et nyt JSON-objekt er opbygget, til hvert enkelt input, overføres til den. 

I følgende eksempel viser en typisk udvælgelsesforespørgsel. 

**Forespørgsel**

    SELECT f.address
    FROM Families f 
    WHERE f.id = "AndersenFamily"

**Resultater**

    [{
      "address": {
        "state": "WA", 
        "county": "King", 
        "city": "seattle"
      }
    }]


### <a name="nested-properties"></a>Indlejrede egenskaber
I eksemplet nedenfor vi projicerer to indlejrede egenskaber `f.address.state` og `f.address.city`.

**Forespørgsel**

    SELECT f.address.state, f.address.city
    FROM Families f 
    WHERE f.id = "AndersenFamily"

**Resultater**

    [{
      "state": "WA", 
      "city": "seattle"
    }]


Projicering understøtter også JSON udtryk, som vist i følgende eksempel.

**Forespørgsel**

    SELECT { "state": f.address.state, "city": f.address.city, "name": f.id }
    FROM Families f 
    WHERE f.id = "AndersenFamily"

**Resultater**

    [{
      "$1": {
        "state": "WA", 
        "city": "seattle", 
        "name": "AndersenFamily"
      }
    }]


Lad os se på rolle `$1` her. Den `SELECT` delsætning skal oprette et JSON-objekt og da ikke angives nogen nøgle, er vi bruge implicit argument variabelnavne starter med `$1`. For eksempel denne forespørgsel returnerer to implicit argumentvariabler, mærket `$1` og `$2`.

**Forespørgsel**

    SELECT { "state": f.address.state, "city": f.address.city }, 
           { "name": f.id }
    FROM Families f 
    WHERE f.id = "AndersenFamily"

**Resultater**

    [{
      "$1": {
        "state": "WA", 
        "city": "seattle"
      }, 
      "$2": {
        "name": "AndersenFamily"
      }
    }]


### <a name="aliasing"></a>Udjævning
Nu Lad os udvide eksemplet ovenfor med eksplicit udjævning af værdier. SOM det er nøgleordet bruges til udjævning. Bemærk, at det er valgfrit, som vist under Sådan viser du den anden værdi som `NameInfo`. 

I tilfælde af en forespørgsel indeholder to egenskaber med samme navn, skal alias bruges til at omdøbe en eller begge af egenskaberne, så de er entydige, i det forventede resultat.

**Forespørgsel**

    SELECT 
           { "state": f.address.state, "city": f.address.city } AS AddressInfo, 
           { "name": f.id } NameInfo
    FROM Families f 
    WHERE f.id = "AndersenFamily"

**Resultater**

    [{
      "AddressInfo": {
        "state": "WA", 
        "city": "seattle"
      }, 
      "NameInfo": {
        "name": "AndersenFamily"
      }
    }]


### <a name="scalar-expressions"></a>Skalær udtryk
Ud over egenskaben referencer understøtter SELECT-delsætningen også skalær udtryk som konstanter, matematiske udtryk, logiske udtryk osv. Her er for eksempel en simpel forespørgsel "Hej verden".

**Forespørgsel**

    SELECT "Hello World"

**Resultater**

    [{
      "$1": "Hello World"
    }]


Her er en mere kompleks eksempel, der bruger et skalarudtryk.

**Forespørgsel**

    SELECT ((2 + 11 % 7)-2)/3   

**Resultater**

    [{
      "$1": 1.33333
    }]


I følgende eksempel er resultatet af udtrykket skalær en boolesk værdi.

**Forespørgsel**

    SELECT f.address.city = f.address.state AS AreFromSameCityState
    FROM Families f 

**Resultater**

    [
      {
        "AreFromSameCityState": false
      }, 
      {
        "AreFromSameCityState": true
      }
    ]


### <a name="object-and-array-creation"></a>Oprettelse af objekt og matrix
En anden nøglefunktionerne af DocumentDB SQL er oprettelse af matrix/objekt. Bemærk, at vi oprettede et nyt JSON-objekt i det forrige eksempel. På samme måde, kan en også oprette matrixer, som vist i følgende eksempler.

**Forespørgsel**

    SELECT [f.address.city, f.address.state] AS CityState 
    FROM Families f 

**Resultater**  

    [
      {
        "CityState": [
          "seattle", 
          "WA"
        ]
      }, 
      {
        "CityState": [
          "NY", 
          "NY"
        ]
      }
    ]

### <a name="value-keyword"></a>VÆRDI nøgleord
Nøgleordet **værdi** er en måde at JSON returværdi. For eksempel vist nedenfor forespørgslen returnerer skalarværdi `"Hello World"` i stedet for `{$1: "Hello World"}`.

**Forespørgsel**

    SELECT VALUE "Hello World"

**Resultater**

    [
      "Hello World"
    ]


Følgende forespørgsel returnerer værdien JSON uden den `"address"` etiket i resultaterne.

**Forespørgsel**

    SELECT VALUE f.address
    FROM Families f 

**Resultater**  

    [
      {
        "state": "WA", 
        "county": "King", 
        "city": "seattle"
      }, 
      {
        "state": "NY", 
        "county": "Manhattan", 
        "city": "NY"
      }
    ]

I følgende eksempel udvider her for at vise, hvordan til at returnere JSON enkle værdier (bladniveau af træet JSON). 

**Forespørgsel**

    SELECT VALUE f.address.state
    FROM Families f 

**Resultater**

    [
      "WA",
      "NY"
    ]


###<a name="-operator"></a>* Operator
Operatoren speciel (*) understøttes til project-dokument som-er. Når du har brugt, skal det eneste planlagte felt. Mens du en forespørgsel som `SELECT * FROM Families f` er gyldig, `SELECT VALUE * FROM Families f ` og `SELECT *, f.id FROM Families f ` er ikke gyldige.

**Forespørgsel**

    SELECT * 
    FROM Families f 
    WHERE f.id = "AndersenFamily"

**Resultater**

    [{
        "id": "AndersenFamily",
        "lastName": "Andersen",
        "parents": [
           { "firstName": "Thomas" },
           { "firstName": "Mary Kay"}
        ],
        "children": [
           {
               "firstName": "Henriette Thaulow", "gender": "female", "grade": 5,
               "pets": [{ "givenName": "Fluffy" }]
           }
        ],
        "address": { "state": "WA", "county": "King", "city": "seattle" },
        "creationDate": 1431620472,
        "isRegistered": true
    }]

###<a name="top-operator"></a>ØVERSTE operatoren
Nøgleordet ØVERSTE kan bruges til at begrænse antallet af værdier fra en forespørgsel. Når toppen bruges sammen med delsætningen ORDER BY, er resultatsættet begrænset til det første N antal bestilte værdier. Ellers returnerer den første N antallet af resultater i en udefineret rækkefølge. Som en bedste fremgangsmåde i en SELECT-sætning altid bruge delsætningen ORDER BY med TOP-delsætningen. Dette er den eneste måde at forudsigeligt angive, hvilke rækker der påvirkes af toppen. 


**Forespørgsel**

    SELECT TOP 1 * 
    FROM Families f 

**Resultater**

    [{
        "id": "AndersenFamily",
        "lastName": "Andersen",
        "parents": [
           { "firstName": "Thomas" },
           { "firstName": "Mary Kay"}
        ],
        "children": [
           {
               "firstName": "Henriette Thaulow", "gender": "female", "grade": 5,
               "pets": [{ "givenName": "Fluffy" }]
           }
        ],
        "address": { "state": "WA", "county": "King", "city": "seattle" },
        "creationDate": 1431620472,
        "isRegistered": true
    }]

TOPPEN kan bruges med en konstant værdi (som vist ovenfor) eller med en variabel værdi ved hjælp af forespørgsler med parametre. Få mere at vide, skal du se parameterforespørgsler nedenfor.

## <a name="order-by-clause"></a>Delsætningen ORDER BY
Som du kan medtage en valgfri Order By-delsætning under forespørgsel i ANSI-SQL. Delsætningen kan indeholde et valgfrit ASC/DESC-argument for at angive den rækkefølge, hvori der skal hentes resultater. Se [DocumentDB rækkefølge ved gennemgang](documentdb-orderby.md)for nærmere mere detaljeret på Order By.

Her er for eksempel en forespørgsel, der henter familier i rækkefølgen af de indbyggede bynavnet.

**Forespørgsel**

    SELECT f.id, f.address.city
    FROM Families f 
    ORDER BY f.address.city
    
**Resultater**
    
    [
      {
        "id": "WakefieldFamily",
        "city": "NY"
      },
      {
        "id": "AndersenFamily",
        "city": "Seattle"   
      }
    ]

Og her er en forespørgsel, der henter familier i rækkefølgen af oprettelsesdato, som er gemt som et tal, der repræsenterer epoke tid, Windows XP, forløbet tid siden 1 Jan 1970 i sekunder.

**Forespørgsel**

    SELECT f.id, f.creationDate
    FROM Families f 
    ORDER BY f.creationDate DESC
    
**Resultater**
    
    [
      {
        "id": "WakefieldFamily",
        "creationDate": 1431620462
      },
      {
        "id": "AndersenFamily",
        "creationDate": 1431620472  
      }
    ]
    
## <a name="advanced-database-concepts-and-sql-queries"></a>Avancerede databasen begreber og SQL-forespørgsler
### <a name="iteration"></a>Gentagelse
En ny konstruktion blev tilføjet via nøgleordet **IN** i DocumentDB SQL til understøttelse af gentager JSON matrixer. FRA kilden understøtter gentagelse. Lad os starte med i følgende eksempel:

**Forespørgsel**

    SELECT * 
    FROM Families.children

**Resultater**  

    [
      [
        {
          "firstName": "Henriette Thaulow", 
          "gender": "female", 
          "grade": 5, 
          "pets": [{ "givenName": "Fluffy"}]
        }
      ], 
      [
        {
            "familyName": "Merriam", 
            "givenName": "Jesse", 
            "gender": "female", 
            "grade": 1
        }, 
        {
            "familyName": "Miller", 
            "givenName": "Lisa", 
            "gender": "female", 
            "grade": 8
        }
      ]
    ]

Nu Lad os se på en anden forespørgsel, som udfører gentagelse over underordnede websteder i gruppen af websteder. Bemærk forskellen i matrixen output. I dette eksempel opdeles `children` og samkopieres resultaterne i en enkelt matrix.  

**Forespørgsel**

    SELECT * 
    FROM c IN Families.children

**Resultater**  

    [
      {
          "firstName": "Henriette Thaulow",
          "gender": "female",
          "grade": 5,
          "pets": [{ "givenName": "Fluffy" }]
      },
      {
          "familyName": "Merriam",
          "givenName": "Jesse",
          "gender": "female",
          "grade": 1
      },
      {
          "familyName": "Miller",
          "givenName": "Lisa",
          "gender": "female",
          "grade": 8
      }
    ]

Dette kan yderligere bruges til at filtrere på hver enkelt post i matrixen, som vist i følgende eksempel.

**Forespørgsel**

    SELECT c.givenName
    FROM c IN Families.children
    WHERE c.grade = 8

**Resultater**  

    [{
      "givenName": "Lisa"
    }]

### <a name="joins"></a>Joinforbindelser
Er nødvendigheden at deltage i på tværs af tabeller er meget vigtigt i en relationel database. Det er den logiske følger til design af standardiseret skemaer. I modsætning til dette behandler DocumentDB med ikke-normaliserede datamodellen for skema ledig dokumenter. Dette er den logiske udgave af en "selv-join".

Syntaksen, der understøtter sproget, der er < from_source1 > JOINFORBINDELSE < from_source2 > JOINFORBINDELSE... Deltage i < from_sourceN >. Overordnet set returnerer dette et sæt **N**- tupler (tupel med **N** værdier). Hver tupel har værdier, der er oprettet med gentager alle samling aliasser deres respektive sæt. Dette er med andre ord, en fuld krydsprodukt sæt af deltager i joinforbindelsen.

Følgende eksempler viser, hvordan JOIN-delsætningen fungerer. Resultatet er tom siden krydsprodukt af hvert dokument fra kilde i eksemplet nedenfor og et tomt sæt er tom.

**Forespørgsel**

    SELECT f.id
    FROM Families f
    JOIN f.NonExistent

**Resultater**  

    [{
    }]


I eksemplet nedenfor er joinforbindelsen mellem dokumentroden og `children` underordnede rod. Det er et krydsprodukt mellem to JSON-objekter. Underordnede websteder, er en matrix er ikke effektive i JOINFORBINDELSEN, da vi arbejder med en enkelt rod, der er børn matrixen. Derfor indeholder resultatet kun to resultater, da krydsprodukt af hvert dokument med matrixen giver præcis kun ét dokument.

**Forespørgsel**

    SELECT f.id
    FROM Families f
    JOIN f.children
 
**Resultater**

    [
      {
        "id": "AndersenFamily"
      }, 
      {
        "id": "WakefieldFamily"
      }
    ]


I følgende eksempel viser en mere traditionelt joinforbindelse:

**Forespørgsel**

    SELECT f.id
    FROM Families f
    JOIN c IN f.children 

**Resultater**

    [
      {
        "id": "AndersenFamily"
      }, 
      {
        "id": "WakefieldFamily"
      }, 
      {
        "id": "WakefieldFamily"
      }
    ]



Det første, du skal være opmærksom er, at den `from_source` , **deltage i** delsætningen er en gentagelsesstreng. Så er strømmen i dette tilfælde som følger:  

-   Udvid hver underordnet element **c** i matrixen.
-   Anvende et krydsprodukt med i roden af dokumentet **f** med hver underordnet element **c** , der blev de i første trin.
-   Til sidst skal projekt roden objekt **f** egenskaben name alene. 

Det første dokument (`AndersenFamily`) indeholder kun én underordnet element, så resultatsættet indeholder kun et enkelt objekt, der svarer til dette dokument. Det andet dokument (`WakefieldFamily`) indeholder to underordnede elementer. Så, giver krydsprodukt et separat objekt for hver underordnede, hvilket medfører to objekter, en for hver underordnede, der svarer til dette dokument. Bemærk, at felterne roden i begge disse dokumenter bliver samme, ligesom du ville forvente i et krydsprodukt.

Værktøjet reelle af JOINFORBINDELSEN er at formular tupler fra krydsprodukt i en figur, der ellers er svært at project. Desuden som vi vil se i eksemplet nedenfor, kan du filtrere efter kombinationen af en tupel, kan brugeren vælger en betingelse, der er opfyldt ved tupler overordnede.

**Forespørgsel**

    SELECT 
        f.id AS familyName,
        c.givenName AS childGivenName,
        c.firstName AS childFirstName,
        p.givenName AS petName 
    FROM Families f 
    JOIN c IN f.children 
    JOIN p IN c.pets
 
**Resultater**

    [
      {
        "familyName": "AndersenFamily", 
        "childFirstName": "Henriette Thaulow", 
        "petName": "Fluffy"
      }, 
      {
        "familyName": "WakefieldFamily", 
        "childGivenName": "Jesse", 
        "petName": "Goofy"
      }, 
      {
       "familyName": "WakefieldFamily", 
       "childGivenName": "Jesse", 
       "petName": "Shadow"
      }
    ]



I dette eksempel er en naturlig udvidelse af det foregående eksempel, og udfører en dobbelt joinforbindelse. Så kan krydsprodukt ses som følgende pseudo kode.

    for-each(Family f in Families)
    {   
        for-each(Child c in f.children)
        {
            for-each(Pet p in c.pets)
            {
                return (Tuple(f.id AS familyName, 
                  c.givenName AS childGivenName, 
                  c.firstName AS childFirstName,
                  p.givenName AS petName));
            }
        }
    }

`AndersenFamily`har ét barn, der har en pet. Så krydsprodukt giver én række (1*1*1) fra denne familie. WakefieldFamily har dog to underordnede elementer, men kun én underordnet "Jesse" har kæledyr. Jesse har 2 kæledyr via. Derfor giver krydsprodukt 1*1*, 2 = 2 rækker fra denne familie.

I det næste eksempel, der er et ekstra filter på `pet`. Dette omfatter ikke alle tupler, hvor pet navnet ikke er "Skygger". Bemærk, at vi kan opbygge tupler fra matrixer, filter på ethvert af elementerne i en tupel og en vilkårlig kombination af elementerne i project. 

**Forespørgsel**

    SELECT 
        f.id AS familyName,
        c.givenName AS childGivenName,
        c.firstName AS childFirstName,
        p.givenName AS petName 
    FROM Families f 
    JOIN c IN f.children 
    JOIN p IN c.pets
    WHERE p.givenName = "Shadow"

**Resultater**

    [
      {
       "familyName": "WakefieldFamily", 
       "childGivenName": "Jesse", 
       "petName": "Shadow"
      }
    ]


## <a name="javascript-integration"></a>JavaScript-integration
DocumentDB giver en programming model for at udføre JavaScript baseret programlogik direkte på samlinger med hensyn til lagrede procedurer og udløsere. Dette giver mulighed for begge dele:

-   Mulighed for at gøre høj ydeevne transaktions CRUD handlinger og forespørgsler dokumenter i en samling i henhold til den dybe integration af JavaScript runtime direkte i af-databaseprogram. 
-   En naturlig modellering af kontrolflow, variable angivelse af område, og opgaven og integration af undtagelse, der håndterer primitiver med databasetransaktioner. Flere oplysninger om DocumentDB understøttelse af JavaScript integration, skal du i dokumentationen til den JavaScript server side programmering.

###<a name="user-defined-functions-udfs"></a>Brugerdefinerede funktioner
Sammen med de typer, der allerede er defineret i denne artikel, understøtter DocumentDB SQL for bruger defineret funktioner (UDF). Især understøttes skalær brugerdefinerede funktioner, hvor udviklerne kan overføre i nul eller flere argumenter og returnerer en enkelt argument resultat tilbage. Hver af disse argumenter kontrolleres, om der juridiske JSON-værdier.  

DocumentDB SQL-syntaks udvidet til at understøtte brugerdefineret programlogik ved hjælp af disse brugerdefinerede funktioner. Brugerdefinerede funktioner kan være registreret hos DocumentDB og derefter skal refereres til som en del af en SQL-forespørgsel. De brugerdefinerede funktioner er faktisk exquisitely designet skal aktiveres af forespørgsler. Brugerdefinerede funktioner har ikke adgang til objektet kontekst, som andre JavaScript typerne (lagrede procedurer og udløsere) have følger til denne valgmulighed. Da der udføre forespørgsler i skrivebeskyttet tilstand, kan de køre på primær eller på sekundær replikaer. Derfor er brugerdefinerede funktioner designet til at køre på sekundær replikaer i modsætning til andre typer JavaScript.

Nedenfor er et eksempel på, hvordan en kan være registreret hos DocumentDB databasen, specifikt under en dokumentsamling.

   
       UserDefinedFunction regexMatchUdf = new UserDefinedFunction
       {
           Id = "REGEX_MATCH",
           Body = @"function (input, pattern) { 
                       return input.match(pattern) !== null;
                   };",
       };
       
       UserDefinedFunction createdUdf = client.CreateUserDefinedFunctionAsync(
           UriFactory.CreateDocumentCollectionUri("testdb", "families"), 
           regexMatchUdf).Result;  
                                                                             
Det foregående eksempel opretter en UDF, hvis navn er `REGEX_MATCH`. Den accepterer to JSON strengværdier `input` og `pattern` og kontrol, hvis de første matches mønstret angivet i andet bruger JavaScript string.match() funktionen.


Vi kan nu bruge denne UDF i en forespørgsel i en projicering. Brugerdefinerede funktioner skal være kvalificeret sammen med præfikset store og små bogstaver "udf." Når kaldt inden for forespørgsler. 

>[AZURE.NOTE] Før du 3-17-2015 understøttes DocumentDB UDF opkald uden "udf." Præfiks som Vælg REGEX_MATCH(). Dette opkald mønster frarådes.  

**Forespørgsel**

    SELECT udf.REGEX_MATCH(Families.address.city, ".*eattle")
    FROM Families

**Resultater**

    [
      {
        "$1": true
      }, 
      {
        "$1": false
      }
    ]

Udf-filen kan også bruges i et filter, som vist i eksemplet nedenfor, også kvalificeret med "udf." præfiks:

**Forespørgsel**

    SELECT Families.id, Families.address.city
    FROM Families
    WHERE udf.REGEX_MATCH(Families.address.city, ".*eattle")

**Resultater**

    [{
        "id": "AndersenFamily",
        "city": "Seattle"
    }]


Egentlig brugerdefinerede funktioner er gyldig skalær udtryk og kan bruges i både prognoser og filtre. 

Hvis du vil udvide på fordelene ved brugerdefinerede funktioner, Lad os se på et andet eksempel med betinget logik:

       UserDefinedFunction seaLevelUdf = new UserDefinedFunction()
       {
           Id = "SEALEVEL",
           Body = @"function(city) {
                switch (city) {
                    case 'seattle':
                        return 520;
                    case 'NY':
                        return 410;
                    case 'Chicago':
                        return 673;
                    default:
                        return -1;
                    }"
            };

            UserDefinedFunction createdUdf = await client.CreateUserDefinedFunctionAsync(
                UriFactory.CreateDocumentCollectionUri("testdb", "families"), 
                seaLevelUdf);
    
    
Nedenfor er et eksempel, der benytter udf-filen.

**Forespørgsel**

    SELECT f.address.city, udf.SEALEVEL(f.address.city) AS seaLevel
    FROM Families f 

**Resultater**

     [
      {
        "city": "seattle", 
        "seaLevel": 520
      }, 
      {
        "city": "NY", 
        "seaLevel": 410
      }
    ]


Som eksempler på foregående præsenterer, integrere brugerdefinerede funktioner en potens af JavaScript sprog med DocumentDB SQL til at give en omfattende programmeres grænseflade at udføre kompleks beskrivelse, betinget logik ved hjælp af indbyggede JavaScript runtime-funktioner.

DocumentDB SQL indeholder argumenterne til de brugerdefinerede funktioner til hvert dokument i kilden på den aktuelle fase (WHERE-delsætning eller SELECT-delsætning) for behandling af udf-filen. Resultatet er integreret i den overordnede udførelse af rørledning problemfrit. Hvis egenskaberne, der refereres til ved UDF parametre er ikke tilgængelige i værdien JSON, parameteren betragtes som Udefineret og dermed UDF aktiveringen udelades helt. På samme måde hvis resultatet af udf-filen er defineret, er den ikke inkluderet i resultatet. 

Brugerdefinerede funktioner er i oversigt, smarte værktøjer til at udføre kompleks forretningslogik som en del af forespørgslen.

### <a name="operator-evaluation"></a>Operatoren beregning
DocumentDB, tegner kan sammenlignes med JavaScript-operatorer og dens evaluering semantik af denne for at være en JSON-database. Mens DocumentDB forsøger at bevare JavaScript semantik med hensyn til JSON support, afviger handlingen evalueringen i visse tilfælde.

I DocumentDB SQL, er i modsætning til i traditionelle SQL typerne værdier ofte ikke kendt indtil værdierne rent faktisk er hentet fra database. For at udføre forespørgsler effektivt, har de fleste af operatorerne faste type krav. 

DocumentDB SQL udføre ikke implicitte konverteringer, i modsætning til JavaScript. For eksempel en forespørgsel som `SELECT * FROM Person p WHERE p.Age = 21` svarer til dokumenter, der indeholder en alder egenskab, hvis værdien er 21. Ethvert andet dokument, hvor alder egenskaben stemmer overens med strengen "21" eller andre muligvis uendelig variationer som "021", "21.0", "0021", "00021", der ikke kan matches osv. Dette er i modsætning til det JavaScript, hvor strengværdierne er implicit casted til tal (baseret på operatoren, ex: ==). Denne indstilling er afgørende for effektiv indeks, der stemmer overens i DocumentDB SQL. 

## <a name="parameterized-sql-queries"></a>Parameteriseret SQL-forespørgsler
DocumentDB understøtter forespørgsler med parametre udtrykt med velkendte @ notation. Parameteriseret SQL indeholder robust håndtering af og slippe af brugerinput for, at forhindre utilsigtede visning af data via SQL-indsættelse. 

Du kan for eksempel skrive en forespørgsel, der tager efternavn og adresse, område som parametre og derefter udføre for forskellige værdier efternavn og adresse stat, der er baseret på brugerinput.

    SELECT * 
    FROM Families f
    WHERE f.lastName = @lastName AND f.address.state = @addressState

Denne anmodning kan derefter sendes til DocumentDB som en parameterforespørgsel JSON som vist nedenfor.

    {      
        "query": "SELECT * FROM Families f WHERE f.lastName = @lastName AND f.address.state = @addressState",     
        "parameters": [          
            {"name": "@lastName", "value": "Wakefield"},         
            {"name": "@addressState", "value": "NY"},           
        ] 
    }

Argumentet til toppen kan angives ved hjælp af parameterforespørgsler som vist nedenfor.

    {      
        "query": "SELECT TOP @n * FROM Families",     
        "parameters": [          
            {"name": "@n", "value": 10},         
        ] 
    }

Parameterværdier kan være en hvilken som helst gyldigt JSON (strenge, tal, booleske værdier, null-værdi, lige matrixer eller indlejret JSON). Også da DocumentDB er skema mindre, valideres parametre ikke mod en hvilken som helst type.

##<a name="built-in-functions"></a>Indbyggede funktioner
DocumentDB understøtter også en række indbyggede funktioner til almindelige handlinger, der kan bruges i forespørgsler som brugerdefinerede funktioner.

<table>
<tr>
<td>Matematiske funktioner</td> 
<td>ABS loft EKSP, FLOOR, LOG, LOG10, POWER, RUNDE, log, KVROD, FIRKANT, Afkort, ARCCOS, ARCSIN, ARCTAN, ATN2, og COS, COT, grader, PI, radianer, SIN og TAN</td>
</tr>
<tr>
<td>Skriv fejlkontrol funktioner</td>    
<td>IS_ARRAY, IS_BOOL, IS_NULL, IS_NUMBER, IS_OBJECT, IS_STRING, IS_DEFINED og IS_PRIMITIVE</td>
</tr>
<tr>
<td>Strengfunktioner</td>   
<td>Sammenkæd, indeholder, ENDSWITH, INDEX_OF, venstre, længde, NEDERSTE, LTRIM, Erstat, REPLIKERES, omvendt, højre, RTRIM, STARTSWITH, UNDERSTRENG og ØVERSTE</td>
</tr>
<tr>
<td>Matrixfunktioner</td>    
<td>ARRAY_CONCAT, ARRAY_CONTAINS, ARRAY_LENGTH og ARRAY_SLICE</td>
</tr>
<tr>
<td>Geografiske funktioner</td>  
<td>ST_DISTANCE, ST_WITHIN, ST_ISVALID og ST_ISVALIDDETAILED</td>
</tr>
</table>  

Hvis du aktuelt bruger en brugerdefineret funktion (UDF), som en indbygget funktion er nu tilgængelig, skal du bruge den tilsvarende indbyggede funktion, som det skal være hurtigere at køre og mere effektivt. 

### <a name="mathematical-functions"></a>Matematiske funktioner
De matematiske funktioner hver udføre en beregning, som regel baseret på inputværdier, der er angivet som argumenter, og returnerer en numerisk værdi. Her er en liste over understøttede indbyggede matematiske funktioner.

<table>
<tr>
<td><strong>Brugen</strong></td>
<td><strong>Beskrivelse</strong></td>
</tr>
<tr>
<td><a href="https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_abs">ABS (num_expr)</a></td> 
<td>Returnerer den absolutte (positive) værdi af det angivne numeriske udtryk.</td>
</tr>
<tr>
<td><a href="https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_ceiling">LOFT (num_expr)</a></td> 
<td>Returnerer den mindste heltalsværdi større end eller lig med det angivne numeriske udtryk.</td>
</tr>
<tr>
<td><a href="https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_floor">FLOOR (num_expr)</a></td> 
<td>Returnerer det største heltal mindre end eller lig med det angivne numeriske udtryk.</td>
</tr>
<tr>
<td><a href="https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_exp">EXP (num_expr)</a></td> 
<td>Returnerer eksponenten af det angivne numeriske udtryk.</td>
</tr>
<tr>
<td><a href="https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_log">LOG (num_expr [, base])</a></td> 
<td>Returnerer den naturlige logaritme af det angivne numeriske udtryk eller logaritmen ved hjælp af den angivne base</td>
</tr>
<tr>
<td><a href="https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_log10">LOG10 (num_expr)</a></td> 
<td>Returnerer base-10 logaritmisk værdien af det angivne numeriske udtryk.</td>
</tr>
<tr>
<td><a href="https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_round">ROUND (num_expr)</a></td> 
<td>Returnerer en numerisk værdi, afrundet til det nærmeste heltal.</td>
</tr>
<tr>
<td><a href="https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_trunc">Afkort (num_expr)</a></td> 
<td>Returnerer en numerisk værdi, afkortet til det nærmeste heltal.</td>
</tr>
<tr>
<td><a href="https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_sqrt">KVROD (num_expr)</a></td>   
<td>Returnerer kvadratroden af det angivne numeriske udtryk.</td>
</tr>
<tr>
<td><a href="https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_square">FIRKANT (num_expr)</a></td>   
<td>Returnerer det angivne numeriske udtryk.</td>
</tr>
<tr>
<td><a href="https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_power">POWER (num_expr, num_expr)</a></td>   
<td>Returnerer en potens af det angivne numeriske udtryk til en værdi er angivet.</td>
</tr>
<tr>
<td><a href="https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_sign">Log (num_expr)</a></td>   
<td>Returnerer værdien log (-1, 0, 1) af den angivne numerisk udtryk.</td>
</tr>
<tr>
<tr>
<td><a href="https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_acos">ARCCOS (num_expr)</a></td>   
<td>Returnerer vinklen i radianer, hvis cosinus er den angivne numerisk udtryk kaldes også cosinus.</td>
</tr>
<tr>
<td><a href="https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_asin">ARCSIN (num_expr)</a></td>   
<td>Returnerer vinklen i radianer, hvis sinus er den angivne numerisk udtryk. Dette kaldes også arcus sinus.</td>
</tr>
<tr>
<td><a href="https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_atan">ARCTAN (num_expr)</a></td>   
<td>Returnerer vinklen i radianer, hvis tangens er den angivne numerisk udtryk. Dette kaldes også arcus tangens.</td>
</tr>
<tr>
<td><a href="https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_atn2">ATN2 (num_expr)</a></td>   
<td>Returnerer vinklen i radianer mellem positive x-aksen og stråler fra startpunkt til punkt (y; x), hvor x og y er værdierne i de to angivne slæk udtryk.</td>
</tr>
<tr>
<td><a href="https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_cos">COS (num_expr)</a></td> 
<td>Returnerer den trigonometriske cosinus af den angivne vinkel i radianer, i det angivne udtryk.</td>
</tr>
<tr>
<td><a href="https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_cot">COT (num_expr)</a></td> 
<td>Returnerer trigonometrisk cotangens til den angivne vinkel i radianer, i det angivne numeriske udtryk.</td>
</tr>
<tr>
<td><a href="https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_degrees">GRADER (num_expr)</a></td> 
<td>Returnerer den tilsvarende vinkel i grader til en vinkel angivet i radianer.</td>
</tr>
<tr>
<td><a href="https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_pi">PI)</a></td>   
<td>Returnerer konstant værdien af PI.</td>
</tr>
<tr>
<td><a href="https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_radians">RADIANER (num_expr)</a></td> 
<td>Returnerer radianer, når der angives et numerisk udtryk i grader.</td>
</tr>
<tr>
<td><a href="https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_sin">SIN (num_expr)</a></td> 
<td>Returnerer den trigonometriske sinus af den angivne vinkel i radianer, i det angivne udtryk.</td>
</tr>
<tr>
<td><a href="https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_tan">TAN (num_expr)</a></td> 
<td>Returnerer tangens input udtrykket i det angivne udtryk.</td>
</tr>

</table> 

Du kan for eksempel nu køre forespørgsler ud som følger:

**Forespørgsel**

    SELECT VALUE ABS(-4)

**Resultater**

    [4]

Primære forskellen mellem Documentdbs funktioner sammenlignet med ANSI SQL er, at de er udviklet til at fungere fint med skema mindre og blandede skemadata. Eksempelvis hvis du har et dokument, hvor egenskaben Size mangler eller har en ikke-numerisk værdi som "Ukendt" og derefter dokumentet er ignoreret over, i stedet for at returnere en fejl.

### <a name="type-checking-functions"></a>Skriv fejlkontrol funktioner
Funktionerne type fejlkontrol gør det muligt at kontrollere typen af et udtryk i SQL-forespørgsler. Type fejlkontrol funktioner kan bruges til at bestemme typen af egenskaber i dokumenter i en fart, når det er variabelt eller ukendt. Her er en liste over understøttede indbygget type kontrollere funktioner.

<table>
<tr>
  <td><strong>Brugen</strong></td>
  <td><strong>Beskrivelse</strong></td>
</tr>
<tr>
  <td><a href="https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_is_array">IS_ARRAY (udtryk)</a></td>
  <td>Returnerer en boolesk værdi, der angiver, hvis typen værdien er en matrix.</td>
</tr>
<tr>
  <td><a href="https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_is_bool">IS_BOOL (udtryk)</a></td>
  <td>Returnerer en boolesk værdi, der angiver, hvis typen værdien er en boolesk værdi.</td>
</tr>
<tr>
  <td><a href="https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_is_null">IS_NULL (udtryk)</a></td>
  <td>Returnerer en boolesk værdi, der angiver, hvis typen værdien er null.</td>
</tr>
<tr>
  <td><a href="https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_is_number">IS_NUMBER (udtryk)</a></td>
  <td>Returnerer en boolesk værdi, der angiver, hvis typen værdien er et tal.</td>
</tr>
<tr>
  <td><a href="https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_is_object">IS_OBJECT (udtryk)</a></td>
  <td>Returnerer en boolesk værdi, der angiver, hvis typen værdien er et JSON-objekt.</td>
</tr>
<tr>
  <td><a href="https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_is_string">IS_STRING (udtryk)</a></td>
  <td>Returnerer en boolesk værdi, der angiver, hvis typen værdien er en streng.</td>
</tr>
<tr>
  <td><a href="https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_is_defined">IS_DEFINED (udtryk)</a></td>
  <td>Returnerer en boolesk værdi, der angiver, hvis egenskaben har fået tildelt en værdi.</td>
</tr>
<tr>
  <td><a href="https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_is_primitive">IS_PRIMITIVE (udtryk)</a></td>
  <td>Returnerer en boolesk værdi, der angiver, hvis typen værdien er en streng, tal, boolesk eller null.</td>
</tr>

</table>

Du kan nu bruger disse funktioner, til at køre forespørgsler ud som følger:

**Forespørgsel**

    SELECT VALUE IS_NUMBER(-4)

**Resultater**

    [true]

### <a name="string-functions"></a>Strengfunktioner
De følgende skalarfunktioner udføre en handling på en streng inputværdi og returnerer en streng, numerisk eller boolesk værdi. Her er en liste over indbyggede strengfunktioner:

Brugen|Beskrivelse
---|---
[LÆNGDEN (str_expr)](https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_length)|Returnerer antallet af tegn i den angivne strengudtryk
[Sammenkæd (str_expr, str_expr [, str_expr])](https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_concat)|Returnerer en streng, der er resultatet af at sammenkæde to eller flere strengværdier.
[UNDERSTRENG (str_expr, num_expr, num_expr)](https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_substring)|Returnerer en del af et strengudtryk.
[STARTSWITH (str_expr, str_expr)](https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_startswith)|Returnerer en boolesk, der angiver om først streng udtryk, der slutter med andet
[ENDSWITH (str_expr, str_expr)](https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_endswith)|Returnerer en boolesk, der angiver om først streng udtryk, der slutter med andet
[INDEHOLDER (str_expr, str_expr)](https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_contains)|Returnerer en boolesk, der angiver om først streng udtryk, der indeholder andet.
[INDEX_OF (str_expr, str_expr)](https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_index_of)|Returnerer startpositionen for den første forekomst af andet streng udtrykket i det første angivne strengudtryk eller -1, hvis strengen ikke findes.
[LEFT (str_expr, num_expr)](https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_left)|Returnerer den venstre del af en streng med det angivne antal tegn.
[RIGHT (str_expr, num_expr)](https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_right)|Returnerer den højre del af en streng med det angivne antal tegn.
[LTRIM (str_expr)](https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_ltrim)|Returnerer et strengudtryk, når fjernes foranstillet tomme celler.
[RTRIM (str_expr)](https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_rtrim)|Returnerer et strengudtryk efter afkortes alle efterfølgende tomme celler.
[NEDERSTE (str_expr)](https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_lower)|Returnerer et strengudtryk efter konvertering af store tegndata til små bogstaver.
[ØVERSTE (str_expr)](https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_upper)|Returnerer et strengudtryk efter konvertering af små tegndata til store bogstaver.
[Erstat (str_expr, str_expr, str_expr)](https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_replace)|Erstatter alle forekomster af en bestemt strengværdi med en anden strengværdi.
[REPLIKERES (str_expr, num_expr)](https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_replicate)|Gentager en strengværdi et angivet antal gange.
[OMVENDT (str_expr)](https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_reverse)|Returnerer en strengværdi omvendt rækkefølge.

Du kan nu bruge disse funktioner, til at køre forespørgsler ud som følger. For eksempel kan du finde familie navn med store bogstaver på følgende måde:

**Forespørgsel**

    SELECT VALUE UPPER(Families.id)
    FROM Families

**Resultater**

    [
        "WAKEFIELDFAMILY", 
        "ANDERSENFAMILY"
    ]

Eller sammenføje strenge som i dette eksempel:

**Forespørgsel**

    SELECT Families.id, CONCAT(Families.address.city, ",", Families.address.state) AS location
    FROM Families

**Resultater**

    [{
      "id": "WakefieldFamily",
      "location": "NY,NY"
    },
    {
      "id": "AndersenFamily",
      "location": "seattle,WA"
    }]


Strengfunktioner kan også bruges i WHERE-delsætningen til at filtrere resultaterne, som i følgende eksempel:

**Forespørgsel**

    SELECT Families.id, Families.address.city
    FROM Families
    WHERE STARTSWITH(Families.id, "Wakefield")

**Resultater**

    [{
      "id": "WakefieldFamily",
      "city": "NY"
    }]

### <a name="array-functions"></a>Matrixfunktioner
De følgende skalarfunktioner udfører en handling på en matrix inputværdi og retur numeriske, boolesk eller en matrix værdi. Her er en liste over indbyggede matrixfunktioner:

Brugen|Beskrivelse
---|---
[ARRAY_LENGTH (arr_expr)](https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_array_length)|Returnerer antallet af elementer i det angivne matrix udtryk.
[ARRAY_CONCAT (arr_expr, arr_expr [, arr_expr])](https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_array_concat)|Returnerer en matrix, der er resultatet af at sammenkæde to eller flere matrix værdier.
[ARRAY_CONTAINS (arr_expr, udtryk)](https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_array_contains)|Returnerer en boolesk værdi, der angiver, om matrixen indeholder den angivne værdi.
[ARRAY_SLICE (arr_expr, num_expr [, num_expr])](https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_array_slice)|Returnerer en del af et udtryk, matrix.

Matrixfunktioner kan bruges til at manipulere matrixer i JSON. Her er for eksempel en forespørgsel, der returnerer alle dokumenter, hvor en af overordnede er "Robin Wakefield". 

**Forespørgsel**

    SELECT Families.id 
    FROM Families 
    WHERE ARRAY_CONTAINS(Families.parents, { givenName: "Robin", familyName: "Wakefield" })

**Resultater**

    [{
      "id": "WakefieldFamily"
    }]

Her er et andet eksempel, der bruger ARRAY_LENGTH til at få antallet af børn per familie.

**Forespørgsel**

    SELECT Families.id, ARRAY_LENGTH(Families.children) AS numberOfChildren
    FROM Families 

**Resultater**

    [{
      "id": "WakefieldFamily",
      "numberOfChildren": 2
    },
    {
      "id": "AndersenFamily",
      "numberOfChildren": 1
    }]

### <a name="spatial-functions"></a>Geografiske funktioner

DocumentDB understøtter følgende Åbn geospatiale Consortium (OGC) indbyggede funktioner til geospatiale forespørgsler. Få mere at vide på geospatiale support i DocumentDB, skal du se [arbejde med geospatiale data i Azure DocumentDB](documentdb-geospatial.md). 

<table>
<tr>
  <td><strong>Brugen</strong></td>
  <td><strong>Beskrivelse</strong></td>
</tr>
<tr>
  <td>ST_DISTANCE (point_expr, point_expr)</td>
  <td>Returnerer afstanden mellem de to GeoJSON punkt udtryk.</td>
</tr>
<tr>
  <td>ST_WITHIN (point_expr, polygon_expr)</td>
  <td>Returnerer en boolesk udtryk, der angiver, om det GeoJSON punkt, der er angivet i det første argument er i GeoJSON polygon i det andet argument.</td>
</tr>
<tr>
  <td>ST_ISVALID</td>
  <td>Returnerer en boolesk værdi, der angiver, om det angivne GeoJSON punkt eller polygon udtryk er gyldig.</td>
</tr>
<tr>
  <td>ST_ISVALIDDETAILED</td>
  <td>Returnerer en JSON-værdi, der indeholder en boolesk værdi, hvis det angivne GeoJSON punkt eller polygon udtryk er gyldig, og hvis det er ugyldige, desuden årsagen til som en strengværdi.</td>
</tr>
</table>

Geografiske funktioner kan bruges til at udføre afstand querries mod geografiske data. Her er for eksempel en forespørgsel, der returnerer alle family dokumenter, der er inden for 30 km af den angivne placering ved hjælp af funktionen ST_DISTANCE indbyggede. 

**Forespørgsel**

    SELECT f.id 
    FROM Families f 
    WHERE ST_DISTANCE(f.location, {'type': 'Point', 'coordinates':[31.9, -4.8]}) < 30000

**Resultater**

    [{
      "id": "WakefieldFamily"
    }]

Hvis du medtager geografiske indeksering i din indeksering politik, vil derefter "afstanden forespørgsler" være served effektivt gennem indekset. Du kan finde flere oplysninger om geografiske indeksering, skal du se afsnittet nedenfor. Hvis du ikke har et geografiske indeks til de angivne stier, kan du stadig udføre geografiske forespørgsler ved at angive `x-ms-documentdb-query-enable-scan` anmodning sidehoved med værdi er angivet til "true". I .NET, kan dette gøres ved at overføre det valgfrie argument **FeedOptions** til forespørgsler med [EnableScanInQuery](https://msdn.microsoft.com/library/microsoft.azure.documents.client.feedoptions.enablescaninquery.aspx#P:Microsoft.Azure.Documents.Client.FeedOptions.EnableScanInQuery) er indstillet til sand. 

ST_WITHIN kan bruges til at kontrollere, hvis et punkt er placeret i en polygon. Ofte bruges polygoner til at repræsentere grænser som postnumre, tilstand grænser eller neutralt lag. Igen Hvis du medtager geografiske indeksering i din indeksering politik, derefter "i" forespørgsler vil være served effektivt gennem indekset. 

Polygon argumenter i ST_WITHIN kan indeholde kun en enkelt ringetone, det vil sige polygoner, må ikke indeholde huller i dem. Se [DocumentDB begrænsninger](documentdb-limits.md) for det maksimale antal point, der er tilladt i en polygon for en ST_WITHIN forespørgsel.

**Forespørgsel**

    SELECT * 
    FROM Families f 
    WHERE ST_WITHIN(f.location, {
        'type':'Polygon', 
        'coordinates': [[[31.8, -5], [32, -5], [32, -4.7], [31.8, -4.7], [31.8, -5]]]
    })

**Resultater**

    [{
      "id": "WakefieldFamily",
    }]
    
>[AZURE.NOTE] Svarer til hvordan uoverensstemmelse mellem datatyper fungerer i DocumentDB forespørgsel, hvis værdien placering er angivet i enten argument er forkert udformet eller ugyldige, og den vil evaluere **Udefineret** og evalueret dokumentet skal udelades fra forespørgselsresultaterne. Hvis forespørgslen ikke returnerer nogen resultater, du Kør ST_ISVALIDDETAILED til fejlfinding hvorfor typen spatail er ugyldige.     

ST_ISVALID og ST_ISVALIDDETAILED kan bruges til at kontrollere, om et geografiske objekt er gyldig. For eksempel kontrollerer følgende forespørgsel gyldighed et punkt med et fraværende-område længde værdi (-132.8). ST_ISVALID returnerer en boolesk værdi, og ST_ISVALIDDETAILED returnerer booleske og en streng, der indeholder årsagen hvorfor det betragtes som ugyldige.

**Forespørgsel**

    SELECT ST_ISVALID({ "type": "Point", "coordinates": [31.9, -132.8] })

**Resultater**

    [{
      "$1": false
    }]

Disse funktioner kan også bruges til at validere polygoner. For eksempel bruge her vi ST_ISVALIDDETAILED til at validere en polygon, der ikke er lukket. 

**Forespørgsel**

    SELECT ST_ISVALIDDETAILED({ "type": "Polygon", "coordinates": [[ 
        [ 31.8, -5 ], [ 31.8, -4.7 ], [ 32, -4.7 ], [ 32, -5 ] 
        ]]})

**Resultater**

    [{
       "$1": { 
          "valid": false, 
          "reason": "The Polygon input is not valid because the start and end points of the ring number 1 are not the same. Each ring of a polygon must have the same start and end points." 
        }
    }]
    
Der ombrydes geografiske funktioner og SQL-syntaksen for DocumentDB. Nu Lad os se nærmere på hvordan LINQ forespørgsler fungerer, og hvordan det fungerer sammen med syntaksen vi har set indtil nu.

## <a name="linq-to-documentdb-sql"></a>LINQ til DocumentDB SQL
LINQ er en .NET programming model, der udtrykker beregning som forespørgsler på streams objekter. DocumentDB indeholder client side bibliotek til brugergrænsefladen med LINQ ved at fremme en konvertering mellem JSON- og .NET-objekter og en tilknytning fra et undersæt af LINQ forespørgsler til DocumentDB forespørgsler. 

Billedet herunder viser arkitekturen i understøttende LINQ forespørgsler med DocumentDB.  Ved hjælp af DocumentDB-klienten, kan udviklere oprette et **IQueryable** objekt, der direkte forespørger DocumentDB forespørgsel provider, som derefter oversætter LINQ forespørgslen til en DocumentDB forespørgsel. Forespørgslen er derefter overføres til DocumentDB serveren for at hente et sæt af resultater i JSON-format. De returnerede resultater deserialiseres i en strøm af .NET objekter på klientsiden.

![Arkitekturen i understøttende LINQ forespørgsler-med DocumentDB - SQL-syntaks, JSON forespørgselssprog, database begreber og SQL-forespørgsler][1]
 


### <a name="net-and-json-mapping"></a>.NET og JSON tilknytning
Tilknytning mellem .NET objekter og JSON dokumenter er neutralt - hvert medlem datafelt er knyttet til et objekt, JSON, hvor feltnavnet er knyttet til den "nøgle" del af objektet, og "værdi" del er gælder, der er knyttet til værdi del af objektet. Overvej følgende eksempel. Objektet familie oprettet er knyttet til JSON-dokument, som vist nedenfor. Og omvendt, JSON dokumentet er tilknyttet tilbage til et .NET-objekt.

**C#-klasse**

    public class Family
    {
        [JsonProperty(PropertyName="id")]
        public string Id;
        public Parent[] parents;
        public Child[] children;
        public bool isRegistered;
    };
    
    public struct Parent
    {
        public string familyName;
        public string givenName;
    };
    
    public class Child
    {
        public string familyName;
        public string givenName;
        public string gender;
        public int grade;
        public List<Pet> pets;
    };
    
    public class Pet
    {
        public string givenName;
    };
    
    public class Address
    {
        public string state;
        public string county;
        public string city;
    };
    
    // Create a Family object.
    Parent mother = new Parent { familyName= "Wakefield", givenName="Robin" };
    Parent father = new Parent { familyName = "Miller", givenName = "Ben" };
    Child child = new Child { familyName="Merriam", givenName="Jesse", gender="female", grade=1 };
    Pet pet = new Pet { givenName = "Fluffy" };
    Address address = new Address { state = "NY", county = "Manhattan", city = "NY" };
    Family family = new Family { Id = "WakefieldFamily", parents = new Parent [] { mother, father}, children = new Child[] { child }, isRegistered = false };


**JSON**  

    {
        "id": "WakefieldFamily",
        "parents": [
            { "familyName": "Wakefield", "givenName": "Robin" },
            { "familyName": "Miller", "givenName": "Ben" }
        ],
        "children": [
            {
                "familyName": "Merriam", 
                "givenName": "Jesse", 
                "gender": "female", 
                "grade": 1,
                "pets": [
                    { "givenName": "Goofy" },
                    { "givenName": "Shadow" }
                ]
            },
            { 
              "familyName": "Miller", 
              "givenName": "Lisa", 
              "gender": "female", 
              "grade": 8 
            }
        ],
        "address": { "state": "NY", "county": "Manhattan", "city": "NY" },
        "isRegistered": false
    };



### <a name="linq-to-sql-translation"></a>LINQ til SQL-oversættelse
Provideren DocumentDB forespørgsel udfører en bedste indsats tilknytning fra en LINQ forespørgsel til en DocumentDB SQL-forespørgsel. I den følgende beskrivelse antager vi læseren har grundlæggende kendskab af LINQ.

Først skal for Typesystemet understøtter vi alle JSON enkle typer – numeriske datatyper, boolesk, streng og null. Kun disse JSON-datatyper understøttes. De følgende skalær udtryk understøttes.

-   Konstante værdier – omfatter disse konstante værdier af datatyperne enkle på det tidspunkt, forespørgslen evalueres.

-   Egenskaben/matrix indeksudtryk – disse udtryk refererer til egenskaben for et objekt eller et matrixelement.

        family.Id;
        family.children[0].familyName;
        family.children[0].grade;
        family.children[n].grade; //n is an int variable

-   Matematiske udtryk - omfatter disse almindelige matematiske udtryk på numeriske og booleske værdier. Referere til SQL-specifikationen til den komplette liste.

        2 * family.children[0].grade;
        x + y;

-   Sammenligning af strengudtryk - disse omfatter sammenligne en strengværdi til nogle konstant strengværdi.  
 
        mother.familyName == "Smith";
        child.givenName == s; //s is a string variable

-   Objekt/matrix oprettelse af udtryk - disse udtryk, der er returtypen et objekt af værditypen sammensat eller anonyme eller en matrix med sådanne objekter. Disse værdier kan indlejres.

        new Parent { familyName = "Smith", givenName = "Joe" };
        new { first = 1, second = 2 }; //an anonymous type with 2 fields              
        new int[] { 3, child.grade, 5 };

### <a name="list-of-supported-linq-operators"></a>Liste over understøttede LINQ operatorer
Her er en liste over understøttede LINQ operatorer i provideren LINQ inkluderet med DocumentDB .NET SDK.

-   **Vælg**: prognoser oversættelse til SQL SELECT herunder objekt byggeri
-   **Hvor**: filtre oversættelse til SQL WHERE og understøtter oversættelse mellem & &, fuldført. og! til SQL-operatorer
-   **SelectMany**: gør det muligt for hensyn matrixer til SQL JOIN-delsætningen. Kan bruges til at kæde/indlejre udtryk til at filtrere på array-elementer
-   **SorterEfter og OrderByDescending**: omsættes til ORDER BY stigende/faldende:
-   **CompareTo**: omsættes til området sammenligninger. Ofte anvendte i strenge, da de ikke er tilsvarende i .NET
-   **Tage**: omsættes til SQL ØVERST for at begrænse resultaterne fra en forespørgsel
-   **Matematiske funktioner**: understøtter oversættelse fra. Nettooversigts Abs, ARCCOS, ARCSIN ARCTAN, loft Cos Exp Floor, Log, Log10, Pow, runde, log, Sin, KVROD, Tan, og Afkort til de tilsvarende SQL indbyggede funktioner.
-   **Strengfunktioner**: understøtter oversættelse fra. Nettooversigts kontaktformular, indeholder, EndsWith IndexOf Tæl, ToLower, TrimStart, Erstat, vend, TrimEnd, StartsWith, understreng, og ToUpper til de tilsvarende SQL indbyggede funktioner.
-   **Matrixfunktioner**: understøtter oversættelse fra. Nettooversigts kontaktformular, indeholder og antal til de tilsvarende SQL indbyggede funktioner.
-   **Geospatiale lokalnummer funktioner**: understøtter oversættelse fra følgebrev metoder afstand i IsValid og IsValidDetailed til de tilsvarende SQL indbyggede funktioner.
-   **Bruger brugerdefinerede funktionen lokalnummer funktion**: understøtter oversættelse fra metoden følgebrev UserDefinedFunctionProvider.Invoke til den tilsvarende bruger defineret funktionen.
-   **Diverse**: understøtter oversættelse af samling og betingede operatorer. Kan oversætte indeholder strengen indeholder, ARRAY_CONTAINS eller SQL i afhængigt af konteksten.

### <a name="sql-query-operators"></a>SQL-forespørgsel operatorer
Her er nogle eksempler, der viser, hvordan nogle af forespørgsel standardoperatorer LINQ oversættes ned til DocumentDB forespørgsler.

#### <a name="select-operator"></a>Vælg Operator
Syntaksen er `input.Select(x => f(x))`, hvor `f` er et skalarudtryk.

**LINQ lambda udtryk**

    input.Select(family => family.parents[0].familyName);

**SQL** 

    SELECT VALUE f.parents[0].familyName
    FROM Families f



**LINQ lambda udtryk**

    input.Select(family => family.children[0].grade + c); // c is an int variable


**SQL** 

    SELECT VALUE f.children[0].grade + c
    FROM Families f 



**LINQ lambda udtryk**

    input.Select(family => new
    {
        name = family.children[0].familyName,
        grade = family.children[0].grade + 3
    });


**SQL** 

    SELECT VALUE {"name":f.children[0].familyName, 
                  "grade": f.children[0].grade + 3 }
    FROM Families f



#### <a name="selectmany-operator"></a>SelectMany operator
Syntaksen er `input.SelectMany(x => f(x))`, hvor `f` er et skalarudtryk, der returnerer en type af websteder.

**LINQ lambda udtryk**

    input.SelectMany(family => family.children);

**SQL** 

    SELECT VALUE child
    FROM child IN Families.children



#### <a name="where-operator"></a>Hvor operator
Syntaksen er `input.Where(x => f(x))`, hvor `f` er et skalarudtryk, der returnerer en boolesk værdi.

**LINQ lambda udtryk**

    input.Where(family=> family.parents[0].familyName == "Smith");

**SQL** 

    SELECT *
    FROM Families f
    WHERE f.parents[0].familyName = "Smith" 



**LINQ lambda udtryk**

    input.Where(
        family => family.parents[0].familyName == "Smith" && 
        family.children[0].grade < 3);

**SQL** 

    SELECT *
    FROM Families f
    WHERE f.parents[0].familyName = "Smith"
    AND f.children[0].grade < 3


### <a name="composite-sql-queries"></a>Sammensatte SQL-forespørgsler
Ovenstående operatorerne kan bestå at danne en mere effektiv metode forespørgsler. Da DocumentDB understøtter indlejrede samlinger, kan sammensætning enten være sammenføjet eller indlejret.

#### <a name="concatenation"></a>Sammenkædning 

Syntaksen er `input(.|.SelectMany())(.Select()|.Where())*`. En sammenkædede forespørgsel kan starte med en valgfri `SelectMany` forespørgsel efterfulgt af flere `Select` eller `Where` operatorer.


**LINQ lambda udtryk**

    input.Select(family=>family.parents[0])
        .Where(familyName == "Smith");

**SQL**

    SELECT *
    FROM Families f
    WHERE f.parents[0].familyName = "Smith"



**LINQ lambda udtryk**

    input.Where(family => family.children[0].grade > 3)
        .Select(family => family.parents[0].familyName);

**SQL** 

    SELECT VALUE f.parents[0].familyName
    FROM Families f
    WHERE f.children[0].grade > 3



**LINQ lambda udtryk**

    input.Select(family => new { grade=family.children[0].grade}).
        Where(anon=> anon.grade < 3);
            
**SQL** 

    SELECT *
    FROM Families f
    WHERE ({grade: f.children[0].grade}.grade > 3)



**LINQ lambda udtryk**

    input.SelectMany(family => family.parents)
        .Where(parent => parents.familyName == "Smith");

**SQL** 

    SELECT *
    FROM p IN Families.parents
    WHERE p.familyName = "Smith"



#### <a name="nesting"></a>Indlejre

Syntaksen er `input.SelectMany(x=>x.Q())` hvor Q er en `Select`, `SelectMany`, eller `Where` operator.

I en indlejret forespørgsel anvendes den inderste forespørgsel til hvert element i ydre af websteder. En vigtig funktion er, at forespørgslen indre kan referere til felterne for elementerne i samlingen ydre som Self-joins.

**LINQ lambda udtryk**

    input.SelectMany(family=> 
        family.parents.Select(p => p.familyName));

**SQL** 

    SELECT VALUE p.familyName
    FROM Families f
    JOIN p IN f.parents


**LINQ lambda udtryk**

    input.SelectMany(family => 
        family.children.Where(child => child.familyName == "Jeff"));
            
**SQL** 

    SELECT *
    FROM Families f
    JOIN c IN f.children
    WHERE c.familyName = "Jeff"



**LINQ lambda udtryk**
            
    input.SelectMany(family => family.children.Where(
        child => child.familyName == family.parents[0].familyName));

**SQL** 

    SELECT *
    FROM Families f
    JOIN c IN f.children
    WHERE c.familyName = f.parents[0].familyName


## <a name="executing-sql-queries"></a>Igangværende SQL-forespørgsler
DocumentDB Fremviser ressourcer via en REST-API, der kan kaldes af en hvilken som helst kan foretage HTTP/HTTPS-anmodninger sprog. Desuden tilbyder DocumentDB programming biblioteker for flere populære sprog som .NET, Node.js, JavaScript og Python. REST-API og de forskellige biblioteker alle understøtter forespørgsler via SQL. .NET SDK understøtter LINQ forespørgsler ud over SQL.

I følgende eksempler viser, hvordan du opretter en forespørgsel og sender den på en DocumentDB database konto.

### <a name="rest-api"></a>REST-API
DocumentDB indeholder en åben RESTful programming model via HTTP. Database konti kan klargøres ved hjælp af et Azure-abonnement. DocumentDB ressource modellen består af et sæt af ressourcer under en database-konto, hver især er tilgængelige ved hjælp af en logisk og stabil URI. Et sæt ressourcer kaldes en feed i dette dokument. En database konto består af et sæt af databaser, hver med flere samlinger, hver af hvilke slå indeholder dokumenter, brugerdefinerede funktioner og andre ressourcetyper.

Grundlæggende interaktion modellen med disse ressourcer er via HTTP-verber få, læg, INDLÆG og Slet med deres standard fortolkning. INDLÆG verber bruges til at oprette en ny ressource, for at udføre en lagret procedure eller til at udstede en DocumentDB forespørgsel. Forespørgsler læses altid kun handlinger med ingen side-effekter.

Følgende eksempler viser et INDLÆG til en DocumentDB forespørgsel foretaget mod en samling, der indeholder de to eksempeldokumenter vi har gennemset hidtil. Forespørgslen indeholder en enkelt filter på JSON-egenskaben name. Bemærk brugen af den `x-ms-documentdb-isquery` og indholdstype: `application/query+json` sidehoveder til angivelse af, at handlingen er en forespørgsel.


**Anmode om**

    POST https://<REST URI>/docs HTTP/1.1
    ...
    x-ms-documentdb-isquery: True
    Content-Type: application/query+json

    {      
        "query": "SELECT * FROM Families f WHERE f.id = @familyId",     
        "parameters": [          
            {"name": "@familyId", "value": "AndersenFamily"}         
        ] 
    }
    

**Resultater**

    HTTP/1.1 200 Ok
    x-ms-activity-id: 8b4678fa-a947-47d3-8dd3-549a40da6eed
    x-ms-item-count: 1
    x-ms-request-charge: 0.32
    
    <indented for readability, results highlighted>
    
    {  
       "_rid":"u1NXANcKogE=",
       "Documents":[  
          {  
             "id":"AndersenFamily",
             "lastName":"Andersen",
             "parents":[  
                {  
                   "firstName":"Thomas"
                },
                {  
                   "firstName":"Mary Kay"
                }
             ],
             "children":[  
                {  
                   "firstName":"Henriette Thaulow",
                   "gender":"female",
                   "grade":5,
                   "pets":[  
                      {  
                         "givenName":"Fluffy"
                      }
                   ]
                }
             ],
             "address":{  
                "state":"WA",
                "county":"King",
                "city":"seattle"
             },
             "_rid":"u1NXANcKogEcAAAAAAAAAA==",
             "_ts":1407691744,
             "_self":"dbs\/u1NXAA==\/colls\/u1NXANcKogE=\/docs\/u1NXANcKogEcAAAAAAAAAA==\/",
             "_etag":"00002b00-0000-0000-0000-53e7abe00000",
             "_attachments":"_attachments\/"
          }
       ],
       "count":1
    }


Det andet eksempel viser en mere kompleks forespørgsel, der returnerer flere resultater fra joinforbindelsen.

**Anmode om**

    POST https://<REST URI>/docs HTTP/1.1
    ...
    x-ms-documentdb-isquery: True
    Content-Type: application/query+json
    
    {      
        "query": "SELECT 
                     f.id AS familyName, 
                     c.givenName AS childGivenName, 
                     c.firstName AS childFirstName, 
                     p.givenName AS petName 
                  FROM Families f 
                  JOIN c IN f.children 
                  JOIN p in c.pets",     
        "parameters": [] 
    }


**Resultater**

    HTTP/1.1 200 Ok
    x-ms-activity-id: 568f34e3-5695-44d3-9b7d-62f8b83e509d
    x-ms-item-count: 1
    x-ms-request-charge: 7.84
    
    <indented for readability, results highlighted>
    
    {  
       "_rid":"u1NXANcKogE=",
       "Documents":[  
          {  
             "familyName":"AndersenFamily",
             "childFirstName":"Henriette Thaulow",
             "petName":"Fluffy"
          },
          {  
             "familyName":"WakefieldFamily",
             "childGivenName":"Jesse",
             "petName":"Goofy"
          },
          {  
             "familyName":"WakefieldFamily",
             "childGivenName":"Jesse",
             "petName":"Shadow"
          }
       ],
       "count":3
    }


Hvis resultatet af en forespørgsel kan ikke passer ind i en enkelt side med resultater REST-API og returnerer derefter et fortsættelse token gennem den `x-ms-continuation-token` svarheaderen. Klienter kan indstillinger resultater ved at medtage sidehovedet i efterfølgende resultater. Antallet resultater for hver side kan også styres gennem den `x-ms-max-item-count` tal sidehoved.

Hvis du vil administrere data konsistens politik for forespørgsler, skal du bruge den `x-ms-consistency-level` sidehoved som alle REST-API-anmodninger. Det er påkrævet til også ekko sidste til session konsistens, `x-ms-session-token` Cookie sidehoved i forespørgsel. Bemærk, at samlingen forespurgte indeksering politik også kan påvirke konsistens af forespørgselsresultater. Med standard indeksering politikindstillinger, til samlinger indekset er altid opdateret med dokumentets indhold og forespørgselsresultater matcher konsistens valgt til data. Hvis politikken indeksering er afslappet langs til Lazy, kan forespørgsler returnerer forældede resultater. Du kan finde flere oplysninger, der refererer til [DocumentDB konsistens niveauer] [consistency-levels].

Hvis den konfigurerede indeksering politik på gruppen af websteder ikke kan understøtter den angivne forespørgsel, returnerer DocumentDB serveren 400 "forkert anmodning". Dette returneres for forespørgsler i området mod stier, der er konfigureret for hash (lige) opslag og for stier udtrykkeligt udelukket fra indeksering. Den `x-ms-documentdb-query-enable-scan` sidehoved kan angives Query skal udføre en scanning, når et indeks ikke er tilgængelig.

### <a name="c-net-sdk"></a>C# (.NET) SDK
.NET SDK understøtter både LINQ og SQL forespørgsler. I følgende eksempel viser, hvordan du udfører forespørgslen enkelt filter introduceret tidligere i dette dokument.


    foreach (var family in client.CreateDocumentQuery(collectionLink, 
        "SELECT * FROM Families f WHERE f.id = \"AndersenFamily\""))
    {
        Console.WriteLine("\tRead {0} from SQL", family);
    }
    
    SqlQuerySpec query = new SqlQuerySpec("SELECT * FROM Families f WHERE f.id = @familyId");
    query.Parameters = new SqlParameterCollection();
    query.Parameters.Add(new SqlParameter("@familyId", "AndersenFamily"));

    foreach (var family in client.CreateDocumentQuery(collectionLink, query))
    {
        Console.WriteLine("\tRead {0} from parameterized SQL", family);
    }

    foreach (var family in (
        from f in client.CreateDocumentQuery(collectionLink)
        where f.Id == "AndersenFamily"
        select f))
    {
        Console.WriteLine("\tRead {0} from LINQ query", family);
    }
    
    foreach (var family in client.CreateDocumentQuery(collectionLink)
        .Where(f => f.Id == "AndersenFamily")
        .Select(f => f))
    {
        Console.WriteLine("\tRead {0} from LINQ lambda", family);
    }


I dette eksempel sammenligner to egenskaber for lighed i hvert dokument og bruger anonym prognoser. 


    foreach (var family in client.CreateDocumentQuery(collectionLink,
        @"SELECT {""Name"": f.id, ""City"":f.address.city} AS Family 
        FROM Families f 
        WHERE f.address.city = f.address.state"))
    {
        Console.WriteLine("\tRead {0} from SQL", family);
    }
    
    foreach (var family in (
        from f in client.CreateDocumentQuery<Family>(collectionLink)
        where f.address.city == f.address.state
        select new { Name = f.Id, City = f.address.city }))
    {
        Console.WriteLine("\tRead {0} from LINQ query", family);
    }
    
    foreach (var family in
        client.CreateDocumentQuery<Family>(collectionLink)
        .Where(f => f.address.city == f.address.state)
        .Select(f => new { Name = f.Id, City = f.address.city }))
    {
        Console.WriteLine("\tRead {0} from LINQ lambda", family);
    }


Det næste eksempel viser joinforbindelser, udtrykt gennem LINQ SelectMany.


    foreach (var pet in client.CreateDocumentQuery(collectionLink,
          @"SELECT p
            FROM Families f 
                 JOIN c IN f.children 
                 JOIN p in c.pets 
            WHERE p.givenName = ""Shadow"""))
    {
        Console.WriteLine("\tRead {0} from SQL", pet);
    }
    
    // Equivalent in Lambda expressions
    foreach (var pet in
        client.CreateDocumentQuery<Family>(collectionLink)
        .SelectMany(f => f.children)
        .SelectMany(c => c.pets)
        .Where(p => p.givenName == "Shadow"))
    {
        Console.WriteLine("\tRead {0} from LINQ lambda", pet);
    }



.NET klienten gentager automatisk listen over alle sider af forespørgselsresultater i foreach-blokke som vist ovenfor. Indstillingerne forespørgsel introduceret i sektionen REST-API findes også i .NET SDK ved hjælp af den `FeedOptions` og `FeedResponse` klasser i metoden CreateDocumentQuery. Du kan styre antallet af sider ved hjælp af den `MaxItemCount` indstilling. 

Du kan også direkte styre sideinddeling ved at oprette `IDocumentQueryable` ved hjælp af den `IQueryable` objektet og derefter ved at læse den` ResponseContinuationToken` værdier og sende dem igen som `RequestContinuationToken` i `FeedOptions`. `EnableScanInQuery`kan angive for at aktivere søgninger, når forespørgslen ikke understøttes af den konfigurerede indeksering politik. Du kan bruge for partitioneret samlinger, `PartitionKey` at køre forespørgslen mod en enkelt partition (selvom DocumentDB kan automatisk trække det fra forespørgselsteksten), og `EnableCrossPartitionQuery` til at køre forespørgsler, der kan være nødvendigt at køres mod flere partitioner. 

Se i [DocumentDB .NET eksempler](https://github.com/Azure/azure-documentdb-net) for flere eksempler, der indeholder forespørgsler. 

### <a name="javascript-server-side-api"></a>JavaScript serversiden API 
DocumentDB giver en programming model for at udføre JavaScript baseret programlogik direkte på samlingerne ved hjælp af lagrede procedurer og udløsere. JavaScript-logik, der er registreret hos en af websteder kan derefter udstede databasehandlinger på handlinger på siden dokumenter af den angivne af websteder. Disse handlinger er ombrudt i omgivende SURT transaktioner.

I følgende eksempel viser, hvordan du bruger den queryDocuments i serveren JavaScript API til at gøre forespørgsler fra indenfor gemt procedurer og udløsere.


    function businessLogic(name, author) {
        var context = getContext();
        var collectionManager = context.getCollection();
        var collectionLink = collectionManager.getSelfLink()
    
        // create a new document.
        collectionManager.createDocument(collectionLink,
            { name: name, author: author },
            function (err, documentCreated) {
                if (err) throw new Error(err.message);
    
                // filter documents by author
                var filterQuery = "SELECT * from root r WHERE r.author = 'George R.'";
                collectionManager.queryDocuments(collectionLink,
                    filterQuery,
                    function (err, matchingDocuments) {
                        if (err) throw new Error(err.message);
    context.getResponse().setBody(matchingDocuments.length);
    
                        // Replace the author name for all documents that satisfied the query.
                        for (var i = 0; i < matchingDocuments.length; i++) {
                            matchingDocuments[i].author = "George R. R. Martin";
                            // we don't need to execute a callback because they are in parallel
                            collectionManager.replaceDocument(matchingDocuments[i]._self,
                                matchingDocuments[i]);
                        }
                    })
            });
    }

## <a name="aggregate-functions"></a>Aggregatfunktioner

Indbygget understøttelse af aggregatfunktioner er i works, men hvis du har brug for antal eller sum funktionalitet i mellemtiden, kan du opnå det samme resultat på forskellige måder.  

På Læs vej:

- Du kan udføre aggregatfunktioner ved hentning af dataene og at gøre en optælling lokalt. Det anbefales for at bruge en billige forespørgsel projicering som `SELECT VALUE 1` i stedet for hele dokumentet som `SELECT * FROM c`. Dette hjælper med at maksimere antallet af dokumenter, der er behandlet i hver enkelt side med resultater, og undgå ekstra forbindelse til tjenesten, hvis det er nødvendigt.
- Du kan også bruge en lagret procedure for at minimere netværksventetid på gentagne Runder. Du kan finde et, der er gemt eksempelprocedure, der beregner antallet for en given filterforespørgsel, og [Count.js](https://github.com/Azure/azure-documentdb-js-server/blob/master/samples/stored-procedures/Count.js). Den lagrede procedure giver brugerne mulighed at kombinere omfattende forretningslogik sammen med at gøre sammenlægninger på en effektiv måde.

På Skriv vej:

- En anden almindelige mønster er allerede samle resultaterne i stien "skrive". Dette er især flot, når mængde "læse" anmodninger er højere end "skrive" anmodninger. Én gang før aggregerede resultaterne er tilgængelige med et enkelt punkt læse anmodningen.  Den bedste måde at sammenlægge udfyldt i DocumentDB er at oprette en udløser, der er aktiveret med hver "skrive", og opdater en metadata-dokument, der har de seneste resultater for den forespørgsel, der er der indtruffet. For eksempel skal du se på [UpdateaMetadata.js](https://github.com/Azure/azure-documentdb-js-server/blob/master/samples/triggers/UpdateMetadata.js) stikprøven, der opdaterer minSize, maxSize og totalSize af Metadatadokumentet til samlingen. Eksemplet kan udvides for at opdatere en tæller, sum, osv.

##<a name="references"></a>Referencer
1.  [Introduktion til Azure DocumentDB][introduction]
2.  [Specifikation af DocumentDB SQL](http://go.microsoft.com/fwlink/p/?LinkID=510612)
3.  [DocumentDB .NET eksempler](https://github.com/Azure/azure-documentdb-net)
4.  [DocumentDB konsistens niveauer][consistency-levels]
5.  ANSI SQL 2011 [http://www.iso.org/iso/iso_catalogue/catalogue_tc/catalogue_detail.htm?csnumber=53681](http://www.iso.org/iso/iso_catalogue/catalogue_tc/catalogue_detail.htm?csnumber=53681)
6.  JSON [http://json.org/](http://json.org/)
7.  JavaScript specifikation [http://www.ecma-international.org/publications/standards/Ecma-262.htm](http://www.ecma-international.org/publications/standards/Ecma-262.htm) 
8.  LINQ [http://msdn.microsoft.com/library/bb308959.aspx](http://msdn.microsoft.com/library/bb308959.aspx) 
9.  Forespørgsel evalueringsteknikker til store databaser [http://dl.acm.org/citation.cfm?id=152611](http://dl.acm.org/citation.cfm?id=152611)
10. Query Processing i parallelle relationsdatabaser, tryk på IEEE Computer Society, 1994
11. Lu, Ooi, Tan, Query Processing i parallelle relationsdatabaser, tryk på IEEE Computer Society, 1994.
12. Christopher Olston Thomas Reed Utkarsh Srivastava, og Ravi Kumar, Andrew Tomkins: gris latinsk: et ikke så fremmed sprog til databehandling SIGMOD 2008.
13.     G. Graefe. Overlapper ramme for forespørgselsoptimering. IEEE Data Eng. Bull., 18 3: 1995.


[1]: ./media/documentdb-sql-query/sql-query1.png
[introduction]: documentdb-introduction.md
[consistency-levels]: documentdb-consistency-levels.md
 
