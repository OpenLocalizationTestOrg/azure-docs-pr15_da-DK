<properties 
    pageTitle="Reference i Analytics i programmet indsigt | Microsoft Azure" 
    description="Reference til sætninger i Analytics, effektive søgeværktøjet af programmet indsigt. " 
    services="application-insights" 
    documentationCenter=""
    authors="alancameronwills" 
    manager="douge"/>

<tags 
    ms.service="application-insights" 
    ms.workload="tbd" 
    ms.tgt_pltfrm="ibiza" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="10/27/2016" 
    ms.author="awills"/>

# <a name="reference-for-analytics"></a>Reference til analyser

[Analytics](app-insights-analytics.md) er effektive søgefunktionen af [Programmet indsigt](app-insights-overview.md). Disse sider beskriver forespørgselssprog analyser.

> [AZURE.NOTE] [Test drev analyser af vores simuleret data](https://analytics.applicationinsights.io/demo) , hvis din app ikke sende data til programmet indsigt endnu.

## <a name="index"></a>Indeks


**Lade** [lade](#let-clause)


**Forespørgsler og operatorer** [Tæl](#count-operator)  |  [evaluere](#evaluate-operator) | [udvide](#extend-operator) | [joinforbindelse](#join-operator) | [grænse](#limit-operator) | [mvexpand](#mvexpand-operator) | [Fortolk](#parse-operator) | [project](#project-operator) | [project-væk](#project-away-operator) | [område](#range-operator) | [reducere](#reduce-operator) | [gengive direktiv](#render-directive) | [Begræns delsætningen](#restrict-clause) | [Sortér](#sort-operator) | [opsummere](#summarize-operator) | [tage](#take-operator) | [toppen](#top-operator) | [top-indlejret](#top-nested-operator) | [union](#union-operator) | [hvor](#where-operator) | [hvor i](#where-in-operator)

**Sammenlægninger** [en hvilken som helst](#any)  |  [argmax](#argmax) | [argmin](#argmin) | [avg](#avg) | [buildschema](#buildschema) | [Tæl](#count) | [Tæl.Hvis](#countif) | [DTÆL](#dcount) | [dcountif](#dcountif) | [makelist](#makelist) | [makeset](#makeset) | [Maks](#max) | [min](#min) | [fraktil](#percentile) | [fraktiler](#percentiles) | [percentilesw](#percentilesw) | [percentilew](#percentilew) | [STDAFV](#stdev) | [sum](#sum) | [Varians](#variance)

**Skalarer** [Booleske konstanter](#boolean-literals)  |  [Booleske operatorer](#boolean-operators) | [internetudsendelser](#casts) | [skalær sammenligninger](#scalar-comparisons) | [gettype](#gettype) | [hash](#hash) | [iff](#iff) | [isnotnull](#isnotnull) | [isnull](#isnull) | [notnull](#notnull) | [toscalar](#toscalar)

**Tal** [Aritmetiske operatorer](#arithmetic-operators)  |  [Numeriske konstanter](#numeric-literals) | [abs](#abs) | [placering](#bin) | [exp](#exp) | [floor](#floor) | [gamma](#gamma) | [log](#log) | [SLUMP](#rand) | [KVROD](#sqrt) | [todouble](#todouble) | [toint](#toint) | [tolong](#tolong)

**Dato og klokkeslæt** [Dato og klokkeslæt udtryk](#date-and-time-expressions)  |  [Dato og klokkeslæt konstanter](#date-and-time-literals) | [siden](#ago) | [datepart](#datepart) | [dayofmonth](#dayofmonth) | [dayofweek](#dayofweek) | [dayofyear](#dayofyear) | [endofday](#endofday) | [endofmonth](#endofmonth) | [endofweek](#endofweek) | [endofyear](#endofyear) | [getmonth](#getmonth) | [getyear](#getyear) | [nu](#now) | [startofday](#startofday) | [startofmonth](#startofmonth) | [startofweek](#startofweek) | [startofyear](#startofyear) | [todatetime](#todatetime) | [totimespan](#totimespan) | [weekofyear](#weekofyear)

**Streng** [GUID](#guids)  |  [Korrumperet Strengkonstanter](#obfuscated-string-literals) | [Strengkonstanter](#string-literals) | [strengsammenligninger](#string-comparisons) | [countof](#countof) | [udtrække](#extract) | [isempty](#isempty) | [isnotempty](#isnotempty) | [notempty](#notempty)| [parseurl](#parseurl) | [erstatte](#replace) | [opdele](#split) | [strcat](#strcat) | [strlen](#strlen) | [understreng](#substring) | [tolower](#tolower) | [toupper](#toupper)

**Matrixer, objekter og dynamisk** [Matrix og objekt konstanter](#array-and-object-literals)  |  [Dynamisk objekt funktioner](#dynamic-object-functions) | [dynamiske objekter i Lad delsætninger](#dynamic-objects-in-let-clauses) | [JSON sti udtryk](#json-path-expressions) | [navne](#names) | [arraylength](#arraylength) | [extractjson](#extractjson) | [parsejson](#parsejson) | [område](#range) | [todynamic](#todynamic) | [treepath](#treepath)



## <a name="let"></a>Lade

### <a name="let-clause"></a>lade delsætning

**Tabelformat lade – navngive en tabel**

    let recentReqs = requests | where timestamp > ago(3d); 
    recentReqs | count

**Skalarværdi lade – navngive en værdi**

    let interval = 3d; 
    requests | where timestamp > ago(interval)

**Lambda lade – navngive en funktion**

    let Recent = 
       (interval:timespan) { requests | where timestamp > ago(interval) };
    Recent(3h) | count

    let us_date = (t:datetime) { strcat(getmonth(t),'/',dayofmonth(t),'/',getyear(t)) }; 
    requests | summarize count() by bin(timestamp, 1d) | project count_, day=us_date(timestamp)

Delsætningen Lad binder et [navn](#names) til en tabelformat resultat, skalarværdi eller funktion. Delsætningen er et præfiks til en forespørgsel, og omfanget af bindingen er denne forespørgsel. (Lad indeholder ikke en metode til navnet ting, du bruger senere i sessionen.)

**Syntaksen for**

    let name = scalar_constant_expression ; query

    let name = query ; query

    let name = (parameterName : type [, ...]) { plain_query }; query

    let name = (parameterName : type [, ...]) { scalar_expression }; query

* *type:* `bool`, `int`, `long`, `double`, `string`, `timespan`, `datetime`, `guid`,[`dynamic`](#dynamic-type)
* *plain_query:* En forespørgsel, der ikke foranstillet Lad-delsætningen.

**Eksempler**

    let rows = (n:long) { range steps from 1 to n step 1 };
    rows(10) | ...


Selv-joinforbindelse:

    let Recent = events | where timestamp > ago(7d);
    Recent | where name contains "session_started" 
  	| project start = timestamp, session_id
  	| join (Recent 
        | where name contains "session_ended" 
        | project stop = timestamp, session_id)
      on session_id
  	| extend duration = stop - start 


## <a name="queries-and-operators"></a>Forespørgsler og operatorer

En forespørgsel over din telemetri består af en reference til en kilde stream, efterfulgt af en rørledning af filtre. Eksempel:


```AIQL
requests // The request table starts this pipeline.
| where client_City == "London" // filter the records
   and timestamp > ago(3d)
| count 
```
    
Hvert filter foranstillet tegnet `|` er en forekomst af en *operator*, med nogle parametre. Input til operatoren er den tabel, der er resultatet af den foregående rørledning. I de fleste tilfælde er en hvilken som helst parametre [skalær udtryk](#scalars) over kolonnerne af input. I nogle tilfælde parametrene er navnene på input kolonner, og i nogle tilfælde parameteren er en anden tabel. Resultatet af en forespørgsel er altid en tabel, selvom det kun indeholder én kolonne og én række.

Forespørgsler kan indeholde enkelt linjeskift, men er afsluttet af en tom linje. De kan indeholde kommentarer mellem `//` og slutningen af linjen.

En forespørgsel kan indledes med en eller flere [lade delsætninger](#let-clause), som definerer skalarer, tabeller eller funktioner, der kan bruges i forespørgslen.

```AIQL

    let interval = 3d ;
    let city = "London" ;
    let req = (city:string) {
      requests
      | where client_City == city and timestamp > ago(interval) };
    req(city) | count
```

> `T`der bruges i forespørgslen eksemplerne nedenfor til angivelse af den foregående pipeline eller kilde tabel.
> 

### <a name="count-operator"></a>Tæl operator

Den `count` operator returnerer antallet af poster (rækker) i den input postsæt.

**Syntaksen for**

    T | count

**Argumenter**

* *T*: tabeldata Hvis poster er der skal tælles.

**Returnerer**

Denne funktion returnerer en tabel med en enkelt post og kolonne af typen `long`. Værdien af den celle, der kun er antallet af poster i *T*. 

**Eksempel**

```AIQL
requests | count
```

### <a name="evaluate-operator"></a>evaluere operator

`evaluate`er en udvidelse måde, som muliggør specialiserede algoritmer, der skal føjes til forespørgsler.

`evaluate`skal være den sidste operator i en forespørgsel pipeline (med undtagelse af en muligt `render`). Det skal vises ikke i en funktion brødtekst.

[evaluere autocluster](#evaluate-autocluster) | [evaluere kurv](#evaluate-basket) | [evaluere diffpatterns](#evaluate-diffpatterns) | [evaluere extractcolumns](#evaluate-extractcolumns)

#### <a name="evaluate-autocluster"></a>evaluere autocluster

     T | evaluate autocluster()

AutoCluster finder almindelige mønstre dedikeret attributter (dimensioner) i dataene og reducerer resultaterne af den oprindelige forespørgsel (om det er 100 eller 100 k rækker) til et lille antal mønstre. AutoCluster blev udviklet for at analysere fejl (fx undtagelser, går ned), men kan potentielt arbejde på en hvilken som helst filtrerede datasæt. 

**Syntaksen for**

    T | evaluate autocluster( arguments )

**Returnerer**

AutoCluster returnerer et (som regel lille) sæt mønstre, der registrerer dele data med delte almindelige værdier på tværs af flere separate attributter. Hvert mønster er repræsenteret af en række i resultaterne. 

De første to kolonner er Tæl og procentdel af rækker af den oprindelige forespørgsel, der er hentet af mønstret. De resterende kolonner er fra den oprindelige forespørgsel og deres værdi er enten en bestemt værdi fra kolonnen eller ' *' dvs variable værdier. 

Bemærk, at mønstrene ikke adskilte: de kan være overlappende, og som regel ikke omfatter alle de oprindelige rækker. Nogle rækker må ikke falde under et mønster.

**Tip**

* Brug `where` og `project` i den input pipe til at reducere dataene til lige hvad du er interesseret i.
* Når du finder en interessant række, kan du vil analysere i den yderligere ved at tilføje dens bestemte værdier til din `where` filter.

**Argumenter (alle valgfrit)**

* `output=all | values | minimal` 

    Formatet af resultaterne. Tæl og procent kolonner vises altid i resultaterne. 

 * `all`-alle kolonner fra input skal være output
 * `values`– filtrerer kolonner med kun ' *' i resultaterne
 * `minimal`– filtrerer også kolonner, der er ens for alle rækker i den oprindelige forespørgsel. 


* `min_percent=`*dobbelt* (standard: 1)

    Hvad mindste procentdel der dækkes af de oprettede rækker.

    Eksempel:`T | evaluate autocluster("min_percent=5.5")`


* `num_seeds=`*heltal* (standard: 25) 

    Antallet af frø bestemmer antallet indledende lokal søgning punkter af algoritmen. I nogle tilfælde, afhængigt af strukturen af dataene, øges øge antallet af frø tal (eller kvaliteten) af resultater gennem langsommere forespørgsel fordeling øget Søg mellemrum. Argumentet num_seeds har mindre med tiden resultater i begge retninger så reducere under 5 får ringe ydeevne og øget over 50 sjældent oprette yderligere mønstre.

    Eksempel:`T | evaluate autocluster("num_seeds=50")`


* `size_weight=`*0 < dobbelte < 1*+ (standard: 0,5)

    Giver dig nogle kontrol over balance mellem standard (høj dækning) og informative (mange delte værdier). Stigende size_weight reducerer normalt antallet af mønstre, og hvert mønster har tendens til at dække en større procentdel. Reducere size_weight normalt giver mere specifikke mønstre med flere delte værdier og mindre procentdel omfang. Den struktur formlen er en vægtet geometriske gennemsnit mellem standardiseret generisk point og informative resultat med size_weight og 1-size_weight som vægt. 

    Eksempel:`T | evaluate autocluster("size_weight=0.8")`


* `weight_column=`*column_name*

    Anser for hver række i input efter den angivne vægt (som standard er hver række har en tykkelse '1'), almindelige brugen af en tykkelse kolonne er at tage konto udvalg eller bucketing/sammenlægning af de data, der allerede er integreret i hver række.

    Eksempel:`T | evaluate autocluster("weight_column=sample_Count")` 



#### <a name="evaluate-basket"></a>evaluere kurv

     T | evaluate basket()

Kurv finder alle hyppige mønstre dedikeret attributter (dimensioner) i dataene og returnerer alle hyppige mønstre, som overføres i grænseværdi for frekvens i den oprindelige forespørgsel. Kurv er garanti for, at finde alle hyppige mønstre i dataene, men nødvendigvis ikke har polynomisk kørselstidspunktet. Kørsel af forespørgslen er lineær i antallet rækker, men i nogle tilfælde kan være eksponentiel i antallet af kolonner (dimensioner). Kurv er baseret på algoritmen Apriori oprindeligt blev udviklet til kurv analyse datamining. 

**Returnerer**

Alle mønstre, der vises i mere end en bestemt brøk (standard 0,05) af hændelser.

**Argumenter (alle valgfrit)**


* `threshold=`*0.015 < dobbelte < 1* (standard: 0,05) 

    Angiver det minimale forholdet mellem rækkerne, der skal betragtes som hyppige (mønstre med mindre forholdet bliver ikke returneret).

    Eksempel:`T | evaluate basket("threshold=0.02")`


* `weight_column=`*column_name*

    Anser for hver række i input efter den angivne vægt (som standard er hver række har en tykkelse '1'), almindelige brugen af en tykkelse kolonne er at tage konto udvalg eller bucketing/sammenlægning af de data, der allerede er integreret i hver række.

    Eksempel: T | evaluere basket("weight_column=sample_Count")


* `max_dims=`*1 < int* (standard: 5)

    Angiver det maksimale antal den ikke-relaterede dimensioner per kurv, begrænset som standard at mindske forespørgsel runtime.


* `output=minimize` | `all` 

    Formatet af resultaterne. Tæl og procent kolonner vises altid i resultaterne.

 * `minimize`– filtrerer kolonner med kun ' *' i resultaterne.
 * `all`-alle kolonner fra input skal være output.




#### <a name="evaluate-diffpatterns"></a>evaluere diffpatterns

     requests | evaluate diffpatterns("split=success")

Diffpatterns sammenligner to datasæt af den samme struktur og finder mønstre over dedikeret attributter (dimensioner), beskrive forskelle mellem de to datasæt. Diffpatterns blev udviklet for at analysere fejl (f.eks. ved sammenligner fejl til ikke-fejl i en bestemt tidsramme), men kan muligvis finde forskelle mellem en hvilken som helst to datasæt af den samme struktur. 

**Syntaksen for**

`T | evaluate diffpatterns("split=`*BinaryColumn*`" [, arguments] )`

**Returnerer**

Diffpatterns returnerer et (som regel lille) sæt mønstre, der registrerer forskellige dele af dataene i de to sæt (det vil sige på et mønster, som henter en stor procentdel af rækker i det første datasæt, og lav procentdel af rækker i det andet sæt). Hvert mønster er repræsenteret af en række i resultaterne.

De første fire kolonner er Tæl og procentdel af rækker af den oprindelige forespørgsel, der er hentet af mønster i hvert sæt, femte kolonne er forskellen (i absolutte procentpoint) mellem de to datasæt. De resterende kolonner er fra den oprindelige forespørgsel og deres værdi er enten en bestemt værdi fra kolonnen eller * dvs variable værdier. 

Bemærk, at mønstrene ikke entydige: de kan være overlappende, og som regel ikke omfatter alle de oprindelige rækker. Nogle rækker må ikke falde under et mønster.

**Tip**

* Brug where og project input pipe til at reducere dataene til lige hvad du er interesseret i.

* Når du finder en interessant række, kan du vil analysere i den yderligere ved at tilføje dens bestemte værdier til at filtrere dine hvor.

**Argumenter**

* `split=`*kolonnenavn* (påkrævet)

    Kolonnen skal indeholde nøjagtigt to værdier. Hvis det er nødvendigt, kan du oprette sådan kolonne:

    `requests | extend fault = toint(resultCode) >= 500` <br/>
    `| evaluate diffpatterns("split=fault")`

* `target=`*streng*

    Fortæller algoritmen kun søge efter mønstre, som har større procentdel i datasættet destination, destinationen skal være en af de to værdier i kolonnen delt.

    `requests | evaluate diffpatterns("split=success", "target=false")`

* `threshold=`*0.015 < dobbelte < 1* (standard: 0,05) 

    Angiver den minimale mønster (forholdet) forskellen mellem de to datasæt.

    `requests | evaluate diffpatterns("split=success", "threshold=0.04")`

* `output=minimize | all`

    Formatet af resultaterne. Tæl og procent kolonner vises altid i resultaterne. 

 * `minimize`– filtrerer kolonner med kun ' *' i resultaterne
 * `all`-alle kolonner fra input skal være output

* `weight_column=`*column_name*

    Anser for hver række i input efter den angivne vægt (som standard hver række har en tykkelse '1'). Et almindelige brug af en tykkelse kolonne er at tage konto udvalg eller bucketing/sammenlægning af de data, der allerede er integreret i hver række.

    `requests | evaluate autocluster("weight_column=itemCount")`






#### <a name="evaluate-extractcolumns"></a>evaluere extractcolumns

     exceptions | take 1000 | evaluate extractcolumns("details=json") 

Extractcolumns bruges til at forbedre en tabel med flere simple kolonner, der er dynamisk udpakkede af (semi) strukturerede kolonner, der er baseret på deres type. Understøtter i øjeblikket json kolonner kun begge dynamisk og streng serialisering af jsons.


* `max_columns=`*heltal* (standard: 10) 

    Antallet af nye tilføjede kolonner er dynamiske og det kan være meget stor (faktisk det er antallet af entydige nøgler i alle json-poster) så vi skal begrænse den. De nye kolonner er sorteret i faldende rækkefølge, der er baseret på deres hyppighed og op til max_columns føjes til tabellen.

    `T | evaluate extractcolumns("json_column_name=json", "max_columns=30")`


* `min_percent=`*dobbelt* (standard: 10.0) 

    En anden måde at begrænse nye kolonner ved at ignorere kolonner, hvis hyppighed er lavere end min_percent.

    `T | evaluate extractcolumns("json_column_name=json", "min_percent=60")`


* `add_prefix=`*boolesk* (standard: SAND) 

    Hvis sand føjes navnet på kolonnen komplekse som et præfiks til de udtrukne kolonner navne.


* `prefix_delimiter=`*streng* (standard: "_") 

    Hvis add_prefix = true denne parameter definerer den afgrænser, der skal bruges til at sammenkæde navnene på de nye kolonner.

    `T | evaluate extractcolumns("json_column_name=json",` <br/>
    `"add_prefix=true", "prefix_delimiter=@")`


* `keep_original=`*boolesk* (standard: falsk) 

    Hvis sand, kolonnerne oprindelige (json) gemmes i outputtabellen.


* `output=query | table` 

    Formatet af resultaterne. 

 * `table`-Output er den samme tabel, som modtages minus den angivne input kolonner plus nye kolonner, der er hentet fra kolonnerne input.
 * `query`-Output er en streng, der repræsenterer den forespørgsel, du ville få at få resultatet som tabel. 




### <a name="extend-operator"></a>udvide operator

     T | extend duration = stopTime - startTime

Føje en eller flere beregnede kolonner til en tabel. 


**Syntaksen for**

    T | extend ColumnName = Expression [, ...]

**Argumenter**

* *T:* Inputtabellen.
* *ColumnName:* Navnet på en kolonner for at tilføje. [Navne](#names) skelnes mellem store og kan indeholde alfabetisk, numerisk eller '_' tegn. Brug `['...']` eller `["..."]` citere nøgleord eller navne med andre tegn.
* *Udtryk:* En beregning over de eksisterende kolonner.

**Returnerer**

En kopi af tabellen input med de angivne flere kolonner.

**Tip**

* Brug [`project`](#project-operator) i stedet, hvis du også vil slippe eller omdøbe nogle kolonner.
* Brug ikke `extend` blot at få et kortere navn til brug i en lang udtryk. `...| extend x = anonymous_user_id_from_client | ... func(x) ...` 

    De oprindelige kolonner i tabellen er blevet indekseret; det nye navn til definerer en ekstra kolonne, der ikke er indekseret, så forespørgslen er sandsynligvis kører langsommere.

**Eksempel**

```AIQL
traces
| extend
    Age = now() - timestamp
```


### <a name="join-operator"></a>JOIN-operator

    Table1 | join (Table2) on CommonColumn

Flettes rækkerne i to tabeller ved at sammenligne værdier af den angivne kolonne.


**Syntaksen for**

    Table1 | join [kind=Kind] (Table2) on CommonColumn [, ...]

**Argumenter**

* *Tabel1* - som 'venstre side' af joinforbindelsen.
* *Tabel2* - 'højre' af joinforbindelsen. Det kan være en indlejret forespørgselsudtryk, der resulterer i en tabel.
* *CommonColumn* - en kolonne, der har samme navn i de to tabeller.
* *Type* - angiver, hvordan rækker fra de to tabeller skal matches.

**Returnerer**

En tabel med:

* En kolonne for hver kolonne i hver af de to tabeller, herunder de tilsvarende nøgler. Kolonnerne i højre side omdøbes automatisk, hvis der er konflikt navn.
* En række for hver match mellem tabellerne input. Et match udgør en række, der er valgt fra én tabel, der indeholder den samme værdi for alle de `on` felter findes som en række i anden tabellen. 

* `Kind`ikke er angivet

    Kun én række fra venstre side er matchet for hver værdi af den `on` nøgle. Output indeholder en række for hver overens med denne række med rækker fra højre.

* `Kind=inner`
 
     Der findes en række i output for hver kombination af tilsvarende rækker fra venstre og højre.

* `kind=leftouter`(or `kind=rightouter` or `kind=fullouter`)

     Ud over de indre matches er der en række for hver række i den venstre (og/eller højre), selvom den ikke har nogen tilsvarende. I så fald indeholder ikke-relaterede output cellerne null-værdier.

* `kind=leftanti`

     Returnerer alle poster fra venstre side, der ikke har tilsvarende fra højre. Tabellen resultat har lige kolonner fra venstre side. 
 
Hvis der er flere rækker med de samme værdier for disse felter, får du vist rækker for alle kombinationer.

**Tip**

Den bedste ydeevne:

* Brug `where` og `project` at reducere antallet af rækker og kolonner i tabellerne input, før den `join`. 
* Hvis en tabel er altid mindre end den anden, kan du bruge det som (via) venstre side af joinforbindelsen.
* Kolonnerne til joinforbindelse matcher skal have samme navn. Bruge operatoren projekt, hvis det er nødvendigt at omdøbe en kolonne på en af tabellerne.

**Eksempel**

Få udvidede aktiviteter fra en log i hvilke nogle poster markere starten og slutningen af en aktivitet. 

```AIQL
    let Events = MyLogTable | where type=="Event" ;
    Events
  	| where Name == "Start"
  	| project Name, City, ActivityId, StartTime=timestamp
  	| join (Events
           | where Name == "Stop"
           | project StopTime=timestamp, ActivityId)
        on ActivityId
  	| project City, ActivityId, StartTime, StopTime, Duration, StopTime, StartTime

```


### <a name="limit-operator"></a>grænse operator

     T | limit 5

Returnerer op til det angivne antal rækker fra tabellen input. Der er ingen garanti, hvilke poster der skal returneres. (For at returnere bestemte poster, skal du bruge [`top`](#top-operator).)

**Alias**`take`

**Syntaksen for**

    T | limit NumberOfRows


**Tip**

`Take`er en enkel og effektiv metode til at se et eksempel på dine resultater, når du arbejder interaktivt. Vær opmærksom på, at det ingen garanti for til at producere en bestemt række eller for at frembringe dem i en bestemt rækkefølge.

Der er en implicit grænse på antallet rækker, der returneres af klienten, selvom du ikke bruger `take`. Hvis du vil ophæve denne grænse, bruge den `notruncation` klient anmodning.



### <a name="mvexpand-operator"></a>mvexpand operator

    T | mvexpand listColumn 

Udvider en liste fra en celle har skrevet dynamisk (JSON), så hvert element har en separat række. Alle de andre celler i en udvidet række er dubleret. 

(Se også [`summarize makelist`](#summarize-operator) som udfører funktionen modsatte.)

**Eksempel**

Lad os antage inputtabellen er:

|A:int|B:String|D:Dynamic|
|---|---|---|
|1|"Hej"|{"nøgle": "værdi"}|
|2|"verden"|[0,1, "k", "v"]|

    mvexpand D

Resultatet er:

|A:int|B:String|D:Dynamic|
|---|---|---|
|1|"Hej"|{"nøgle": "værdi"}|
|2|"verden"|0|
|2|"verden"|1|
|2|"verden"|"k"|
|2|"verden"|"v"|


**Syntaksen for**

    T | mvexpand  [bagexpansion=(bag | array)] ColumnName [limit Rowlimit]

    T | mvexpand  [bagexpansion=(bag | array)] [Name =] ArrayExpression [to typeof(Typename)] [limit Rowlimit]

**Argumenter**

* *ColumnName:* I resultatet er matrixer i kolonnen navngivet udvidet til flere rækker. 
* *ArrayExpression:* Et udtryk, hvilket giver en matrix. Hvis denne formular bruges, der tilføjes en ny kolonne og bevares den eksisterende kalender.
* *Navn:* Et navn til den nye kolonne.
* *Typename:* Konverterer den udvidede udtryk til en bestemt type
* *RowLimit:* Det maksimale antal rækker, der er dannet ud fra hver oprindelige række. Standard er 128.

**Returnerer**

Flere rækker for hver af værdierne i en matrix eller i matrix udtrykket i kolonnen navngivet.

Kolonnen udvidet har altid dynamiske type. Brug en cast såsom `todatetime()` eller `toint()` Hvis du vil beregne eller sammenlægge værdier.

To måder at egenskaben pose udvidelser understøttes:

* `bagexpansion=bag`: Egenskaben forsendelser er udvidet til Egenskabssamlingerne for enkelt post. Dette er standard udvidelse.
* `bagexpansion=array`: Egenskaben forsendelser er udvidet til to elementer `[` *nøgle*`,`*værdi* `]` array strukturer, tillade ensartet adgang til nøgler og værdier (samt, for eksempel kører et antal forskellige sammenlægning over egenskaben navn). 

**Eksempler**


    exceptions | take 1 
  	| mvexpand details[0]

Opdeler en undtagelsespost i rækker for hvert element i feltet detaljer.



### <a name="parse-operator"></a>Analysér operator

    T | parse "I got 2 socks for my birthday when I was 63 years old" 
    with * "got" counter:long " " present "for" * "was" year:long *


    T | parse kind=relaxed
          "I got no socks for my birthday when I was 63 years old" 
    with * "got" counter:long " " present "for" * "was" year:long * 

    T |  parse kind=regex "I got socks for my 63rd birthday" 
    with "(I|She) got" present "for .*?" year:long * 

Henter værdier fra en streng. Kan bruge simpelt eller almindelige udtryk tilsvarende.

**Syntaksen for**

    T | parse [kind=regex|relaxed] SourceText 
        with [Match | Column [: Type [*]] ]  ...

**Argumenter**

* `T`: Inputtabellen.
* `kind`: 
 * `simple`(standard): de `Match` strenge er almindelig strenge.
 * `relaxed`: Hvis teksten ikke parses som typen af en kolonne, kolonnen er angivet til null og analysér fortsætter 
 * `regex`: den `Match` strenge er regulære udtryk.
* `Text`: En kolonne eller andre udtryk, der evalueres til eller kan konverteres til en streng.
* *Match:* Svarer til den næste del af strengen, og Slet den.
* *Kolonne:* Tildele den næste del af strengen til denne kolonne. Hvis den ikke findes, oprettes der kolonnen.
* *Type:* Fortolk den næste del af strengen, som den angivne type, som heltal, dato, dobbelt. 


**Returnerer**

Inputtabellen, udvidet ifølge listen over kolonner.

Elementer i det `with` delsætningen sammenlignes med kildeteksten igen. Hvert element chews fra en del af kildeteksten: 

* En strengkonstant eller regulære udtryk du flytter markøren passer med længden af matcher.
* I en regex Analysér en regulære udtryk kan bruge operatoren begrænsning '?' til at flytte til følgende matcher så tidligt som muligt.
* Navnet på en kolonne med en type parser teksten som den angivne type. Medmindre type = lavere, en mislykkes analysér, der ugyldiggør svarer til hele mønsteret.
* Et kolonnenavn uden en type eller med typen 'streng', kopierer det mindste antal tegn for at få adgang til følgende matcher.
* ' *' Springer det mindste antal tegn for at få adgang til følgende matcher. Du kan bruge '*' i starten og slutningen af mønsteret, eller efter en type end streng eller mellem streng matches.

Alle elementer i et analysér mønster skal svare til korrekt. Ellers vil blive produceret nogen resultater. Undtagelser til denne regel er, når det er nemt = lavere, hvis parsing af en indtastede variabel mislykkes, skal resten af analysér fortsætter.

**Eksempler**

*Enkel:*

```AIQL

// Test without reading a table:
 range x from 1 to 1 step 1 
 | parse "I got 2 socks for my birthday when I was 63 years old" 
    with 
     *   // skip until next match
     "got" 
     counter: long // read a number
     " " // separate fields
     present // copy string up to next match
     "for" 
     *  // skip until next match
     "was" 
     year:long // parse number
     *  // skip rest of string
```

x | Tæller | Præsenter | År
---|---|---|---
1 | 2 | SOCKS | 63

*Afslappet langs:*

Når input indeholder et korrekte match for hver indtastede kolonner, giver en lavere analysér samme resultater, som en simpel analysér. Men hvis en af de angivne kolonner ikke parses korrekt, en lavere analysér fortsætter med at behandle resten af mønsteret, mens en simpel analysér stopper og ikke kan oprette et resultat.


```AIQL

// Test without reading a table:
 range x from 1 to 1 step 1 
 | parse kind="relaxed"
        "I got several socks for my birthday when I was 63 years old" 
    with 
     *   // skip until next match
     "got" 
     counter: long // read a number
     " " // separate fields
     present // copy string up to next match
     "for" 
     *  // skip until next match
     "was" 
     year:long // parse number
     *  // skip rest of string
```


x  | Præsenter | År
---|---|---
1 |  SOCKS | 63


*Regex:*

```AIQL

// Run a test without reading a table:
range x from 1 to 1 step 1 
// Test string:
| extend s = "Event: NotifySliceRelease (resourceName=Scheduler, totalSlices=27, sliceNumber=16, lockTime=02/17/2016 08:41, releaseTime=02/17/2016 08:41:00, previousLockTime=02/17/2016 08:40:00)" 
// Parse it:
| parse kind=regex s 
  with ".*?[a-zA-Z]*=" resource 
       ", total.*?sliceNumber=" slice:long *
       "lockTime=" lock
       ",.*?releaseTime=" release 
       ",.*?previousLockTime=" previous:date 
       ".*\\)"
| project-away x, s
```

ressource | udsnit | Lås | Slip | forrige
---|---|---|---|---
Opgavestyring | 16 | 02-17/2016 08:41:00 | 02-17/2016 08:41 | 2016-02-17T08:40:00Z

### <a name="project-operator"></a>Project operator

    T | project cost=price*quantity, price

Vælg kolonnerne, der omfatter, omdøbe eller slip, og Indsæt nye beregnede kolonner. Rækkefølgen af kolonnerne i resultatet er angivet af sidernes rækkefølge argumenterne. Kun de kolonner, der er angivet i argumenterne er inkluderet i resultatet: alle andre i input udelades.  (Se også `extend`.)


**Syntaksen for**

    T | project ColumnName [= Expression] [, ...]

**Argumenter**

* *T:* Inputtabellen.
* *ColumnName:* Navnet på en kolonne, der skal vises i outputtet. Hvis der ikke er nogen *udtryk*, skal en kolonne med dette navn vises i input. [Navne](#names) skelnes mellem store og kan indeholde alfabetisk, numerisk eller '_' tegn. Brug `['...']` eller `["..."]` citere nøgleord eller navne med andre tegn.
* *Udtryk:* Valgfri skalarudtryk, der refererer til kolonnerne input. 

    Det er lovligt til at returnere en ny beregnet kolonne med samme navn som en eksisterende kolonne i input.

**Returnerer**

En tabel, der indeholder de kolonner, der er navngivet som argumenter, og så mange rækker som inputtabellen.

**Eksempel**

I følgende eksempel vises flere typer handlinger, der kan udføres ved hjælp af den `project` operator. Inputtabellen `T` har tre kolonner af typen `int`: `A`, `B`, og `C`. 

```AIQL
T
| project
    X=C,               // Rename column C to X
    A=2*B,             // Calculate a new column A from the old B
    C=strcat("-",tostring(C)), // Calculate a new column C from the old C
    B=2*B,              // Calculate a new column B from the old B
    ['where'] = client_City // rename, using a keyword as a column name
```

### <a name="project-away-operator"></a>Project-væk operator

    T | project-away column1, column2, ...

Udelade bestemte kolonner. Resultatet indeholder alle de input kolonner med undtagelse af dem du navngive.

### <a name="range-operator"></a>Områdeoperator

    range LastWeek from ago(7d) to now() step 1d

Genererer en enkelt kolonne tabel med værdier. Bemærk, at det ikke har en rørledning, der er indtastet. 

|LastWeek|
|---|
|2015-12-05 09:10:04.627|
|2015-12-06 09:10:04.627|
|...|
|2015-12-12 09:10:04.627|



**Syntaksen for**

    range ColumnName from Start to Stop step Step

**Argumenter**

* *ColumnName:* Navnet på den enkelte kolonne i outputtabellen.
* *Start:* Den mindste værdi i output.
* *Stop:* Den højeste værdi, der genereres i output (eller en grænse for den højeste værdi, hvis *trin* trin over denne værdi).
* *Trin:* Forskellen mellem to på hinanden følgende værdier. 

Argumenterne skal være numeriske, dato eller timespan værdier. De kan ikke referere til kolonner i en tabel. (Hvis du vil beregne det område, der er baseret på en inputtabellen, Brug [område *funktionen*](#range), måske med [mvexpand operator](#mvexpand-operator).) 

**Returnerer**

En tabel med en enkelt kolonne med navnet *ColumnName*, hvis værdier er *Start*skal *starte* + *trin*,... op til og med *Stop*.

**Eksempel**  

```AIQL
range Steps from 1 to 8 step 3
```

En tabel med en enkelt kolonne kaldet `Steps` Hvis type er `long` , og hvis værdier er `1`, `4`, og `7`.

**Eksempel**

    range LastWeek from bin(ago(7d),1d) to now() step 1d

En tabel med midnat på de seneste syv dage. Funktionen placering (floor) reducerer hver gang til starten af dag.

**Eksempel**  

```AIQL
range timestamp from ago(4h) to now() step 1m
| join kind=fullouter
  (traces
      | where timestamp > ago(4h)
      | summarize Count=count() by bin(timestamp, 1m)
  ) on timestamp
| project Count=iff(isnull(Count), 0, Count), timestamp
| render timechart  
```

Viser, hvordan den `range` operator kan bruges til at oprette en lille, ad hoc-, dimensionstabel som derefter bruges til at præsentere nuller hvor kildedataene har ingen værdier.

### <a name="reduce-operator"></a>reducere operator

    exceptions | reduce by outerMessage

Forsøger at gruppere lignende poster. For hver gruppe operatoren skriver den `Pattern` det mener bedst beskriver gruppen, og den `Count` af poster i den pågældende gruppe.


![](./media/app-insights-analytics-reference/reduce.png)

**Syntaksen for**

    T | reduce by  ColumnName [ with threshold=Threshold ]

**Argumenter**

* *ColumnName:* Kolonnen for at undersøge. Det skal være af strengtypen.
* *Grænseværdi:* En værdi i intervallet {fra 0 til 1}. Standard er 0,001. Grænseværdi for skal være lille for store input. 

**Returnerer**

To kolonner, `Pattern` og `Count`. I mange tilfælde vil mønster være en komplet værdi fra kolonnen. I nogle tilfælde kan det identificere almindelige ord og erstatte de variable dele med ' *'.

For eksempel resultatet af `reduce by city` kan indeholde: 

|Mønster | Tæl |
|---|---|
| SAN * | 5182 |
| Saint * | 2846 |
| Moskva | 3726 |
| \*-on-\* | 2730 |
| Paris | 27163 |


### <a name="render-directive"></a>gengive direktiv

    T | render [ table | timechart  | barchart | piechart ]

Gengiv omdirigerer præsentation lag, hvordan du får vist tabellen. Det skal være det sidste element i røret. Det er en praktisk alternativ til at bruge kontrolelementerne på den skærm, så du kan gemme en forespørgsel med en bestemt præsentation metode.

### <a name="restrict-clause"></a>begrænse delsætning 

Angiver sæt af tabelnavne, der er tilgængelige for operatorer, som følger. Eksempel:

    let e1 = requests | project name, client_City;
    let e2 =  requests | project name, success;
    // Exclude predefined tables from the union:
    restrict access to (e1, e2);
    union * |  take 10 

### <a name="sort-operator"></a>Sortér operator 

    T | sort by country asc, price desc

Sortere rækker i tabellen input i rækkefølge efter en eller flere kolonner.

**Alias**`order`

**Syntaksen for**

    T  | sort by Column [ asc | desc ] [ `,` ... ]

**Argumenter**

* *T:* Den tabel, der er indtastet til at sortere.
* *Kolonne:* Kolonne med *T* , der skal sorteres. Typen af værdierne skal være numeriske, dato, klokkeslæt eller streng.
* `asc`Sortér i stigende rækkefølge, lav til høj. Standard er `desc`, faldende høj til Lav.

**Eksempel**

```AIQL
Traces
| where ActivityId == "479671d99b7b"
| sort by Timestamp asc
```
Alle rækker i tabellen sporinger, der har en bestemt `ActivityId`, sorteret efter deres tidsstempel.

### <a name="summarize-operator"></a>opsummere operator

Giver en tabel, der samler indholdet af inputtabellen.
 
    requests
  	| summarize count(), avg(duration), makeset(client_City) 
      by client_CountryOrRegion

En tabel, der viser det antal, varighed gennemsnitlige anmodning og sæt byer i hvert land. Der findes en række i output til hvert af de forskellige lande. Kolonnerne output viser Tæl, gennemsnitlig varighed, byer og land. Alle andre input kolonner, ignoreres.


    T | summarize count() by price_range=bin(price, 10.0)

En tabel, der viser, hvor mange elementer har priser i hvert interval [0,10.0], [10.0,20.0], og så videre. I dette eksempel har en kolonne for antallet og én i området pris. Alle andre input kolonner, ignoreres.


**Syntaksen for**

    T | summarize
         [  [ Column = ] Aggregation [ `,` ... ] ]
         [ by
            [ Column = ] GroupExpression [ `,` ... ] ]

**Argumenter**

* *Kolonne:* Valgfrit navn til en kolonne til resultatet. Som standard til et navn, der er afledt af udtrykket. [Navne](#names) skelnes mellem store og kan indeholde alfabetisk, numerisk eller '_' tegn. Brug `['...']` eller `["..."]` citere nøgleord eller navne med andre tegn.
* *Sammenlægning:* Et opkald til en sammenlægning funktion som `count()` eller `avg()`, med kolonnenavne som argumenter. Se [sammenlægninger](#aggregations).
* *GroupExpression:* Et udtryk over kolonnerne, der indeholder et sæt af entydige værdier. Det er som regel enten et kolonnenavn, som allerede indeholder et begrænset antal værdier, eller `bin()` med en numerisk eller tid kolonne som argument. 

Hvis du angiver et numerisk eller tid udtryk uden brug af `bin()`, Analytics automatisk gælder det med et interval af `1h` for tidspunkter, eller `1.0` for tal.

Hvis du ikke angiver en *GroupExpression,* er hele tabellen opsummeret i en enkelt output række.



**Returnerer**

Indtast rækker er arrangeret i grupper, der har de samme værdier i den `by` udtryk. Derefter beregnes de angivne sammenlægningsfunktioner over hver gruppe, der producerer en række for hver gruppe. Resultatet indeholder den `by` kolonner og også mindst én kolonne for hvert beregnes aggregat. (Nogle sammenlægningsfunktioner returnere flere kolonner).

Resultatet er det antal rækker, der er forskellige kombinationer af `by` værdier. Hvis du vil opsummere over områder af numeriske værdier, skal du bruge `bin()` at reducere områder til dedikeret værdier.

**Bemærk!**

Selvom du kan angive vilkårlig udtryk til sammenlægning og grupperingsudtryk, det er mere effektivt at Brug enkel kolonnenavne, eller Anvend `bin()` til en numerisk kolonne.



### <a name="take-operator"></a>tage operator

Alias for [grænse](#limit-operator)


### <a name="top-operator"></a>øverste operator

    T | top 5 by Name desc nulls first

Returnerer de første *N* -poster, der er sorteret efter de angivne kolonner.


**Syntaksen for**

    T | top NumberOfRows by Sort_expression [ `asc` | `desc` ] [`nulls first`|`nulls last`] [, ... ]

**Argumenter**

* *NumberOfRows:* Antallet af rækker af *T* for at vende tilbage.
* *Sort_expression:* Et udtryk, der skal sorteres rækkerne. Det er normalt bare et kolonnenavn. Du kan angive mere end én sort_expression.
* `asc`eller `desc` (standard) kan blive vist til kontrolelementet om markeringen rent faktisk kommer fra "bunden" eller "toppen" af området.
* `nulls first`eller `nulls last` kontrolelementer, hvor null-værdier vises. `First`er standard for `asc`, `last` er standard for `desc`.


**Tip**

`top 5 by name`på overfladen svarer til `sort by name | take 5`. Men der køres hurtigere og altid returnerer sorteret resultater, mens `take` garanterer ikke sådanne.

### <a name="top-nested-operator"></a>top-indlejret operator

    requests 
  	| top-nested 5 of name by count()  
    , top-nested 3 of performanceBucket by count() 
    , top-nested 3 of client_CountryOrRegion by count()
  	| render barchart 

Giver hierarkiske resultater, hvor hvert niveau er analyse ned fra det forrige niveau. Det er praktisk til at besvare spørgsmål, der lyder som "Hvad er de vigtigste 5 anmodninger og for hver af dem, hvad er øverste 3 ydeevne grupperne, og for hver af dem, som er top 3 lande anmodningerne kommer fra"?

**Syntaksen for**

   T | top-indlejret N af kolonne efter sammenlægning [,...]

**Argumenter**

* N:int - antallet af rækker, der skal gå tilbage eller overføre til et højere niveau. I en forespørgsel med tre niveauer, hvor N er 5, 3 og 3, bliver det samlede antal rækker 45.
* KOLONNE - en kolonne til at gruppere efter til sammenlægning. 
* SAMMENLÆGNING - en [sammenlægning funktionen](#aggregations) skal gælde for hver gruppe af rækker. Resultaterne af disse sammenlægninger bestemmer de øverste grupper skal vises.


### <a name="union-operator"></a>Foreningsmængdeoperator

     Table1 | union Table2, Table3

Tager to eller flere tabeller og returnerer rækkerne af dem alle. 

**Syntaksen for**

    T | union [ kind= inner | outer ] [ withsource = ColumnName ] Table2 [ , ...]  

    union [ kind= inner | outer ] [ withsource = ColumnName ] Table1, Table2 [ , ...]  

**Argumenter**

* *Tabel1*, *Tabel2* ...
 *  Navnet på en tabel, såsom `requests`, eller en tabel, der er defineret i en [lade delsætningen](#let-clause); eller
 *  En forespørgsel udtryk, f.eks.`(requests | where success=="True")`
 *  Et sæt af tabeller, der er angivet med et jokertegn. For eksempel `e*` ville formular union over alle de tabeller, der er defineret i forrige Lad delsætninger, hvis navn startede med 'e', sammen med undtagelsestabellen ''.
* `kind`: 
 * `inner`-Resultatet er et undersæt af kolonner, der er fælles for alle input tabeller.
 * `outer`-Resultatet har alle de kolonner, der forekommer i en af materialer. Celler, der ikke blev defineret ved en input række er angivet til `null`.
* `withsource=`*ColumnName:* Hvis angivet, medtages output en kolonne med navnet *ColumnName* , hvor værdien angiver, hvilke kildetabellen har bidraget til hver række.

**Returnerer**

En tabel med så mange rækker, der er i alle tabellerne input, og så mange kolonner, der er forskellige kolonnenavne i input.

**Eksempel**

```AIQL

let ttrr = requests | where timestamp > ago(1h);
let ttee = exceptions | where timestamp > ago(1h);
union tt* | count
```
Samling af alle tabeller, hvis navne begynder "tt".


**Eksempel**

```AIQL

union withsource=SourceTable kind=outer Query, Command
| where Timestamp > ago(1d)
| summarize dcount(UserId)
```
Antallet af forskellige brugere, der har produceret enten en `exceptions` begivenhed eller en `traces` begivenhed over den sidste dag. I resultatet angiver kolonnen 'SourceTable' "Forespørgsel" eller "Kommandoen".

```AIQL
exceptions
| where Timestamp > ago(1d)
| union withsource=SourceTable kind=outer 
   (Command | where Timestamp > ago(1d))
| summarize dcount(UserId)
```

Denne mere effektiv version giver det samme resultat. Det filtrerer alle tabeller, før du opretter Unionen.

### <a name="where-operator"></a>hvor operator

     requests | where resultCode==200

Filtrerer en tabel til undersættet af rækker, der opfylder et prædikat.

**Alias**`filter`

**Syntaksen for**

    T | where Predicate

**Argumenter**

* *T:* Tabelformat input, hvis poster der skal filtreres.
* *Prædikat:* A `boolean` [udtryk](#boolean) over kolonnerne i *T*. Det evalueres for hver række i *T*.

**Returnerer**

Rækker i *T* , *prædikat* er `true`.

**Tip**

Sådan får du den hurtigste ydeevne:

* **Brug simple sammenligninger** mellem kolonnenavne og konstanter. ('Konstant' betyder konstant over tabel - så `now()` og `ago()` er OK, og så parameterværdi tildeles ved hjælp af en [ `let` delsætningen](#let-clause).)

    For eksempel foretrækker `where Timestamp >= ago(1d)` til `where floor(Timestamp, 1d) == ago(1d)`.

* **Simplest betingelser første**: Hvis du har flere delsætninger conjoined med `and`, placere først disse betingelser, der involverer kun én kolonne. Så `Timestamp > ago(1d) and OpId == EventId` er bedre end anden vej rundt.


**Eksempel**

```AIQL
traces
| where Timestamp > ago(1h)
    and Source == "Kuskus"
    and ActivityId == SubActivityIt 
```

Poster, der ikke er ældre end 1 time og kommer fra kilden kaldet "Kuskus" og har to kolonner med samme værdi. 

Bemærk, at vi kan angive sammenligning mellem to kolonner sidst, som den kan ikke bruge indekset og fremtvinger en scanning.


### <a name="where-in-operator"></a>hvor i operator

    requests | where resultCode !in (200, 201)

    requests | where resultCode in (403, 404)

**Syntaksen for**

    T | where col in (expr1, expr2, ...)
    T | where col !in (expr1, expr2, ...)

**Argumenter**

* `col`: En kolonne i tabellen.
* `expr1`...: En liste over skalær udtryk.

Brug `in` bruges til at medtage kun rækker, hvor `col` er lig med et af udtrykkene `expr1...`.

Brug `!in` medtage kun rækker, hvor `col` ikke er lig med nogle af udtrykkene `expr1...`.  


## <a name="aggregations"></a>Sammenlægninger

Sammenlægninger er funktioner, der bruges til at kombinere værdier i grupper, der er oprettet i den [opsummere handling](#summarize-operator). I denne forespørgsel er dcount() for eksempel en sammenlægning funktion:

    requests | summarize dcount(name) by success

### <a name="any"></a>en hvilken som helst 

    any(Expression)

Tilfældigt markerer én række af gruppen, og returnerer værdien af det angivne udtryk.

Dette er nyttigt, når en kolonne indeholder et stort antal ens værdier (f.eks., en kolonne "fejltekst"), og du vil indsamle kolonnen én gang for hver en entydig værdi for tasten sammensat gruppe, f.eks. 

**Eksempel**  

```

traces 
| where timestamp > now(-15min)  
| summarize count(), any(message) by operation_Name 
| top 10 by count_level desc 
```

<a name="argmin"></a>
<a name="argmax"></a>
### <a name="argmin-argmax"></a>argmin, argmax

    argmin(ExprToMinimize, * | ExprToReturn  [ , ... ] )
    argmax(ExprToMaximize, * | ExprToReturn  [ , ... ] ) 

Søger efter en række i den gruppe, der minimerer/maksimerer *ExprToMaximize*, og returnerer værdien af *ExprToReturn* (eller `*` til at returnere hele rækken).

**Tip**: kolonnerne passerede gennem automatisk omdøbes. For at sikre, at du bruger de rigtige navne, undersøge resultaterne ved hjælp af `take 5` før du pipe resultaterne i en anden operator.

**Eksempler**

Vis når længste anmodningen opstod for hvert anmodning navn:

    requests | summarize argmax(duration, timestamp) by name

Vis alle oplysninger om længste anmodningen, ikke kun tidsstemplet:

    requests | summarize argmax(duration, *) by name


Finde den laveste værdi af hver metrikværdi, sammen med dens tidsstempel og andre data:

    metrics 
  	| summarize minValue=argmin(value, *) 
      by name


![](./media/app-insights-analytics-reference/argmin.png)
 


### <a name="avg"></a>AVG

    avg(Expression)

Beregner gennemsnittet af *udtryk* på tværs af gruppen.

### <a name="buildschema"></a>buildschema

    buildschema(DynamicExpression)

Returnerer den minimale skema, der giver adgang til alle værdier for *DynamicExpression*. 

Kolonnetype parameter skal være `dynamic` -en matrix eller egenskab pose. 

**Eksempel**

    exceptions | summarize buildschema(details)

Resultat:

    { "`indexer`":
     {"id":"string",
       "parsedStack":
       { "`indexer`": 
         {  "level":"int",
            "assembly":"string",
            "fileName":"string",
            "method":"string",
            "line":"int"
         }},
      "outerId":"string",
      "message":"string",
      "type":"string",
      "rawStack":"string"
    }}

Bemærk, at `indexer` bruges til at markere, hvor du skal bruge et numerisk indeks. For denne skema nogle gyldige stier, der ville (hvis disse eksempel indeks er i område):

    details[0].parsedStack[2].level
    details[0].message
    arraylength(details)
    arraylength(details[0].parsedStack)

**Eksempel**

Lad os antage kolonnen input har tre dynamiske værdier:

| |
|---|
|`{"x":1, "y":3.5}`
|`{"x":"somevalue", "z":[1, 2, 3]}`
|`{"y":{"w":"zzz"}, "t":["aa", "bb"], "z":["foo"]}`


Den resulterende skema ville være:

    { 
      "x":["int", "string"], 
      "y":["double", {"w": "string"}], 
      "z":{"`indexer`": ["int", "string"]}, 
      "t":{"`indexer`": "string"} 
    }

Skemaet fortæller os, som:

* Rodobjekt er en beholder med fire egenskaber med navnet x, y, z og t.
* Egenskaben kaldet "x", der kan være af typen "int" eller af typen "streng".
* Egenskaben kaldet "j", der kan enten af typen "dobbelt", eller en anden beholder med en egenskab kaldet "w" af typen "streng".
* Den ``indexer`` nøgleord angiver, at "z" og "t" er matrixer.
* Hvert element i matrixen "z" er et heltal eller en streng.
* "t" er en matrix af strenge.
* Hver egenskab er implicit valgfrit, og en hvilken som helst matrix kan være tomme.

##### <a name="schema-model"></a>Skema model

Syntaksen for det returnerede skema er:

    Container ::= '{' Named-type* '}';
    Named-type ::= (name | '"`indexer`"') ':' Type;
    Type ::= Primitive-type | Union-type | Container;
    Union-type ::= '[' Type* ']';
    Primitive-type ::= "int" | "string" | ...;

De er svarer til et undersæt af maskine type anmærkninger, kodet som en dynamisk værdi. Eksempel skemaet vil maskine være:

    var someobject: 
    { 
      x?: (number | string), 
      y?: (number | { w?: string}), 
      z?: { [n:number] : (int | string)},
      t?: { [n:number]: string } 
    }


### <a name="count"></a>Tæl

    count([ Predicate ])

Returnerer en optælling af rækker, *prædikat* evalueres til `true`. Hvis ingen *prædikat* ikke er angivet, returnerer det samlede antal poster i gruppen. 

**Performance tip**: Brug `summarize count(filter)` i stedet for`where filter | summarize count()`

> [AZURE.NOTE] Undgå at bruge count() til at finde antallet af anmodninger, undtagelser eller andre begivenheder, der er opstået. Når [stikprøver, hvor der](app-insights-sampling.md) er i handlingen, vil antallet af datapunkter bevares i programmet indsigt være mindre end antallet oprindelige begivenheder. I stedet bruge `summarize sum(itemCount)...`. Egenskaben itemCount afspejler antallet oprindelige hændelser, der er repræsenteret af hvert bevares datapunkt.

### <a name="countif"></a>Tæl.Hvis

    countif(Predicate)

Returnerer en optælling af rækker, *prædikat* evalueres til `true`.

**Performance tip**: Brug `summarize countif(filter)` i stedet for`where filter | summarize count()`

> [AZURE.NOTE] Undgå at bruge countif() til at finde antallet af anmodninger, undtagelser eller andre begivenheder, der er opstået. Når [stikprøver, hvor der](app-insights-sampling.md) er i handlingen, vil antallet af datapunkter være mindre end antallet faktiske begivenheder. I stedet bruge `summarize sum(itemCount)...`. Egenskaben itemCount afspejler antallet oprindelige hændelser, der er repræsenteret af hvert bevares datapunkt.

### <a name="dcount"></a>DCount

    dcount( Expression [ ,  Accuracy ])

Returnerer en estimering af antallet af entydige værdier af *udtryk* i gruppen. (Du kan få vist de entydige værdier, bruge [`makeset`](#makeset).)

*Nøjagtigheden*, hvis angivet, styrer saldoen mellem hastighed og nøjagtighed.

 * `0`= mindst nøjagtige og hurtigste beregningen.
 * `1`den standard, der afbalancerer nøjagtigheden og beregning tid; om 0,8% fejl.
 * `2`= mest nøjagtige og lavest beregning; om 0,4% fejl.

**Eksempel**

    pageViews 
  	| summarize cities=dcount(client_City) 
      by client_CountryOrRegion

![](./media/app-insights-analytics-reference/dcount.png)


### <a name="dcountif"></a>dcountif

    dcountif( Expression, Predicate [ ,  Accuracy ])

Returnerer en estimering af antallet af entydige værdier af *udtryk* af rækker i gruppen, som *prædikat* er sand. (Du kan få vist de entydige værdier, bruge [`makeset`](#makeset).)

*Nøjagtigheden*, hvis angivet, styrer saldoen mellem hastighed og nøjagtighed.

 * `0`= mindst nøjagtige og hurtigste beregningen.
 * `1`den standard, der afbalancerer nøjagtigheden og beregning tid; om 0,8% fejl.
 * `2`= mest nøjagtige og lavest beregning; om 0,4% fejl.

**Eksempel**

    pageViews 
  	| summarize cities=dcountif(client_City, client_City startswith "St") 
      by client_CountryOrRegion


### <a name="makelist"></a>makelist

    makelist(Expr [ ,  MaxListSize ] )

Returnerer en `dynamic` (JSON) matrix af alle værdier for *udtryk* i gruppen. 

* *MaxListSize* er en valgfri heltal grænse på det maksimale antal elementer, der returneres (standard er *128*).

### <a name="makeset"></a>makeset

    makeset(Expression [ , MaxSetSize ] )

Returnerer en `dynamic` (JSON) matrix af sæt af entydige værdier, der tager *udtryk* i gruppen. (Tip: Hvis du vil bare tælle entydige værdier, bruge [`dcount`](#dcount).)
  
*  *MaxSetSize* er en valgfri heltal grænse på det maksimale antal elementer, der returneres (standard er *128*).

**Eksempel**

    pageViews 
  	| summarize cities=makeset(client_City) 
      by client_CountryOrRegion

![](./media/app-insights-analytics-reference/makeset.png)

Se også den [ `mvexpand` operator](#mvexpand-operator) for funktionen modsatte.


### <a name="max-min"></a>Max, min

    max(Expr)

Beregner maksimalt antal *udtryk*.
    
    min(Expr)

Beregner minimum på *udtryk*.

**Tip**: Dette giver dig min eller Maks på sin egen – for eksempel, det højeste eller laveste pris. Men hvis du vil have andre kolonner i rækken - for eksempel navnet på leverandøren med den laveste pris - bruge [argmin eller argmax](#argmin-argmax).


<a name="percentile"></a>
<a name="percentiles"></a>
<a name="percentilew"></a>
<a name="percentilesw"></a>
### <a name="percentile-percentiles-percentilew-percentilesw"></a>fraktil, fraktiler, percentilew, percentilesw

    percentile(Expression, Percentile)

Returnerer en estimering for *udtryk* i den angivne fraktil i gruppen. Nøjagtigheden afhænger af tætheden af populationen i området af fraktilen.
    
    percentiles(Expression, Percentile1 [ , Percentile2 ...] )

Sådan `percentile()`, men beregner et antal fraktilværdier (som er hurtigere end beregning af hver fraktil individuelt).

    percentilew(Expression, WeightExpression, Percentile)

Vægtet fraktil. Brug denne til allerede aggregerede data.  `WeightExpression`er et heltal, der angiver, hvor mange oprindelige rækker er repræsenteret af hver aggregeret række.

    percentilesw(Expression, WeightExpression, Percentile1, [, Percentile2 ...])

Sådan `percentilew()`, men beregner et antal fraktilværdier.

**Eksempler**


Værdien af `duration` , der er større end 95% af stikprøvernes Indstil og mindre end 5% af det Eksempelsæt, beregnes for hvert navn, anmodning om:

    request 
  	| summarize percentile(duration, 95)
      by name

Udelader "ved..." til at beregne for hele tabellen.

Samtidigt beregne flere fraktiler for forskellige anmodning navne:

    
    requests 
  	| summarize 
        percentiles(duration, 5, 20, 50, 80, 95) 
      by name

![](./media/app-insights-analytics-reference/percentiles.png)

Resultaterne viser, til anmodning om /Events/Index, 5% af anmodninger om er reageret på i mindre end 2.44s, halvdelen af dem i 3.52s, og 5% er langsommere end 6.85s.

Beregne flere statistik:

    requests 
  	| summarize 
        count(), 
        avg(Duration),
        percentiles(Duration, 5, 50, 95)
      by name

#### <a name="weighted-percentiles"></a>Vægtet fraktiler

Brug funktionerne vægtet fraktil i tilfælde, hvor dataene er blevet udfyldt sammenlagt. 

Antag f.eks, at din app udfører mange tusindvis af handlinger sekundet, og du gerne vil vide deres ventetid. Enkel løsningen ville være til at oprette en anmodning om programmet indsigt eller brugerdefineret hændelse for hver handling. Dette vil oprette en masse trafik, selvom tilpasset udvalg skal træde i kraft at reducere den. Men du beslutter at implementere en endnu bedre løsning: Du kan skrive kode i din app til at samle dataene før du sender den til programmet indsigt. Sammenlagt oversigten sendes med jævne mellemrum, reduceres data rente måske til et par punkter i minuttet.

Din kode tager en stream måleenheder ventetid i millisekunder. Eksempel:
    
     { 15, 12, 2, 21, 2, 5, 35, 7, 12, 22, 1, 15, 18, 12, 26, 7 }

Den tæller målene på følgende placeringer:`{ 10, 20, 30, 40, 50, 100 }`

Med jævne mellemrum, foretages der en række TrackEvent opkald, en for hver Malerbøtte med brugerdefinerede mål i hver opkald: 

    foreach (var latency in bins.Keys)
    { telemetry.TrackEvent("latency", null, 
         new Dictionary<string, double>
         ({"latency", latency}, {"opCount", bins[latency]}}); }

I Analytics, kan du se en sådan gruppe af hændelser således:

`opCount` | `latency`| betydning
---|---|---
8 | 10 | = 8 handlinger på 10ms placeringen
6 | 20 | = 6 handlinger på 20 MS placeringen
3 | 30 | = 3 handlinger på 30ms placeringen
1 | 40 | = 1 handlinger på 40ms placeringen

For at få et nøjagtigt billede af den oprindelige fordeling af begivenhed latenstider skal vi bruge `percentilesw`:

    customEvents | summarize percentilesw(latency, opCount, 20, 50, 80)

Resultaterne er på samme måde som hvis vi havde brugt almindelig `percentiles` på det oprindelige sæt mål.

> [AZURE.NOTE] Vægtet fraktiler gælder ikke for [prøver fra data](app-insights-sampling.md), hvor hver række med indsamlede repræsenterer en stikprøve af oprindelige rækker i stedet for en placering. Funktionerne almindelig fraktil er relevante for indsamlede data.

#### <a name="estimation-error-in-percentiles"></a>Skøn fejl i fraktiler

Fraktiler aggregat indeholder en tilnærmet værdi ved hjælp af [T-samlet](https://github.com/tdunning/t-digest/blob/master/docs/t-digest-paper/histo.pdf). 

Nogle vigtige punkter: 

* Grænser for fejlen skøn afhænger af værdien af den ønskede fraktil. Bedste nøjagtigheden er for enden af [0..100] skalere fraktiler 0 og 100 er den nøjagtige minimum- og maksimumværdier for den stokastiske variabel. Nøjagtigheden reduceres gradvist mod midten af skalaen. Det er værste på medianen og er maksimalt 1%. 
* Fejl grænser skal være opfyldt på deres rangering, ikke på værdien. Antag fraktil (X 50) returnerede værdi af Xm. Skønnet garanterer, at mindst 49% og højst 51% af værdierne i X er mindre end Xm. Der er ingen teoretiske grænse på forskellen mellem Xm og faktisk gennemsnitsværdien af X.

### <a name="stdev"></a>STDAFV

     stdev(Expr)

Returnerer standardafvigelsen for *udtryk* over gruppen.

### <a name="variance"></a>varians

    variance(Expr)

Returnerer variansen for *udtryk* over gruppen.

### <a name="sum"></a>Sum

    sum(Expr)

Returnerer summen af *udtryk* over gruppen.                      


## <a name="scalars"></a>Skalarer

[internetudsendelser](#casts) | [sammenligninger](#scalar-comparisons)
<br/>
[GetType](#gettype) | [hash](#hash) | [iff](#iff) |  [isnull](#isnull) | [isnotnull](#isnotnull) | [notnull](#notnull) | [toscalar](#toscalar)

Understøttede datatyper er:

| Type      | Flere navne   | Tilsvarende .NET-type |
| --------- | -------------------- | -------------------- |
| `bool`    | `boolean`            | `System.Boolean`     |
| `datetime`| `date`               | `System.DateTime`    |
| `dynamic` |                      | `System.Object`      |
| `guid`    | `uuid`, `uniqueid`   | `System.Guid`        |
| `int`     |                      | `System.Int32`       |
| `long`    |                      | `System.Int64`       |
| `double`  | `real`               | `System.Double`      |
| `string`  |                      | `System.String`      |
| `timespan`| `time`               | `System.TimeSpan`    |

### <a name="casts"></a>Internetudsendelser

Du kan konverteret fra én type til en anden. Generelt, hvis konverteringen giver mening, fungerer det:

    todouble(10), todouble("10.6")
    toint(10.6) == 11
    floor(10.6) == 10
    toint("200")
    todatetime("2016-04-28 13:02")
    totimespan("1.5d"), totimespan("1.12:00:00")
    toguid("00000000-0000-0000-0000-000000000000")
    tostring(42.5)
    todynamic("{a:10, b:20}")

Kontrollere, om en streng, der kan konverteres til en bestemt type:

    iff(notnull(todouble(customDimensions.myValue)),
       ..., ...)

### <a name="scalar-comparisons"></a>Skalær sammenligninger

||
---|---
`<` |Mindre
`<=`|Mindre eller er lig med
`>` |Større
`>=`|Større eller er lig med
`<>`|Ikke er lig med
`!=`|Ikke er lig med 
`in`| Højre operand er en (dynamisk) matrix og venstre operand er lig med en af dens elementer.
`!in`| Højre operand er en (dynamisk) matrix og venstre operand er ikke lig med nogen af dets elementer.




### <a name="gettype"></a>GetType

**Returnerer**

En streng, der repræsenterer den underliggende lagerplads type argumentets enkelt. Dette er især nyttig, når du har værdier for type `dynamic`: i dette tilfælde `gettype()` kan afsløre hvordan værdien kodes.

**Eksempler**

|||
---|---
`gettype("a")` |`"string" `
`gettype(111)` |`"long" `
`gettype(1==1)` |`"int8"`
`gettype(now())` |`"datetime" `
`gettype(1s)` |`"timespan" `
`gettype(parsejson('1'))` |`"int" `
`gettype(parsejson(' "abc" '))` |`"string" `
`gettype(parsejson(' {"abc":1} '))` |`"dictionary"` 
`gettype(parsejson(' [1, 2, 3] '))` |`"array"` 
`gettype(123.45)` |`"real" `
`gettype(guid(12e8b78d-55b4-46ae-b068-26d7a0080254))` |`"guid"` 
`gettype(parsejson(''))` |`"null"`
`gettype(1.2)==real` | `true`

### <a name="hash"></a>hash

**Syntaksen for**

    hash(source [, mod])

**Argumenter**

* *kilde*: kilden skalær giver resultat beregnes på.
* *rest*: den modulus værdi, der skal anvendes på hashværdien.

**Returnerer**

Xxhash (long) værdien af den angivne skalarværdi modulus den angivne rest værdi (Hvis angivet).

**Eksempler**

```
hash("World")                   // 1846988464401551951
hash("World", 100)              // 51 (1846988464401551951 % 100)
hash(datetime("2015-01-01"))    // 1380966698541616202
```
### <a name="iff"></a>IFF

Den `iff()` funktionen evalueres det første argument (prædikatet) og returnerer værdien af det andet eller tredje argumenter afhængigt af om prædikatet er `true` eller `false`. Andet og tredje argumenterne skal være af samme type.

**Syntaksen for**

    iff(predicate, ifTrue, ifFalse)


**Argumenter**

* *prædikat:* Et udtryk, der evalueres til en `boolean` værdi.
* *ifTrue:* Et udtryk, der bliver evalueret og dens værdi fra funktionen returneres, hvis *prædikat* evalueres til `true`.
* *ifFalse:* Et udtryk, der bliver evalueret og dens værdi fra funktionen returneres, hvis *prædikat* evalueres til `false`.

**Returnerer**

Denne funktion returnerer værdien af *ifTrue* , hvis *prædikat* evalueres til `true`, eller værdien af *ifFalse* på anden måde.

**Eksempel**

```
iff(floor(timestamp, 1d)==floor(now(), 1d), "today", "anotherday")
```

<a name="isnull"/></a>
<a name="isnotnull"/></a>
<a name="notnull"/></a>
### <a name="isnull-isnotnull-notnull"></a>IsNull, isnotnull, notnull

    isnull(parsejson("")) == true

Tager et enkelt argument og fortæller, om det er null.

**Syntaksen for**


    isnull([value])


    isnotnull([value])


    notnull([value])  // alias for isnotnull

**Returnerer**

SAND eller FALSK afhængigt af om værdien er null eller ikke er null.


|x|IsNull(x)
|---|---
| "" | FALSK
|"x" | FALSK
|parsejson("")|SAND
|parsejson("[]")|FALSK
|parsejson("{}")|FALSK

**Eksempel**

    T | where isnotnull(PossiblyNull) | count

Bemærk, at der er andre måder at opnå denne effekt:

    T | summarize count(PossiblyNull)

### <a name="toscalar"></a>toscalar

Evaluerer en forespørgsel eller et udtryk og returnerer resultatet som en enkelt værdi. Denne funktion er nyttig til faseinddelt beregninger; for eksempel beregner en samlet antal begivenheder og derefter bruge, når en oprindelig plan.

**Syntaksen for**

    toscalar(query)
    toscalar(scalar)

**Returnerer**

Argumentet evalueres. Hvis argumentet er en tabel, returnerer den første kolonne i den første række. (Der er god praksis at arrangere, argumentet har kun én kolonne og række.)

**Eksempel**

```AIQL

    // Get the count of requests 5 days ago:
    let baseline = toscalar(requests  
        | where floor(timestamp, 1d) == floor(ago(5d),1d) | count);
    // List the counts relative to that baseline:
    requests | summarize daycount = count() by floor(timestamp, 1d)  
  	| extend relative = daycount - baseline
```




### <a name="boolean-literals"></a>Booleske konstanter

    true == 1
    false == 0
    gettype(true) == "int8"
    typeof(bool) == typeof(int8)

### <a name="boolean-operators"></a>Booleske operatorer

    and 
    or 

    

## <a name="numbers"></a>Tal

[abs](#abs) | [bin](#bin) | [exp](#exp) | [floor](#floor) | [gamma](#gamma) |[log](#log) | [rand](#rand) | [range](#range) | [sqrt](#sqrt) 
| [todouble](#todouble) | [toint](#toint) | [tolong](#tolong)

### <a name="numeric-literals"></a>Numeriske konstanter

|||
|---|---
|`42`|`long`
|`42.0`|`real`

### <a name="arithmetic-operators"></a>Aritmetiske operatorer

|| |
|---|-------------|
| + | Tilføje         |
| - | Subtrahere    |
| * | Multiplicere    |
| / | Dividere      |
| % | Modulus      |
||
|`<` |Mindre
|`<=`|Mindre eller er lig med
|`>` |Større
|`>=`|Større eller er lig med
|`<>`|Ikke er lig med
|`!=`|Ikke er lig med 


### <a name="abs"></a>ABS

**Syntaksen for**

    abs(x)

**Argumenter**

* x - et heltal, reelt eller timespan

**Returnerer**

    iff(x>0, x, -x)

<a name="bin"></a><a name="floor"></a>
### <a name="bin-floor"></a>placering, floor

Runder værdier ned til et heltal multiplum af en bestemt placering størrelse. Bruges meget i den [`summarize by`](#summarize-operator) forespørgsel. Hvis du har et spredte sæt af værdier, grupperes de i et mindre antal specifikke værdier.

Alias `floor`.

**Syntaksen for**

     bin(value, roundTo)
     floor(value, roundTo)

**Argumenter**

* *værdi:* Et tal, dato eller timespan. 
* *roundTo:* "Placering størrelsen". Et tal, dato eller timespan, der deler *værdi*. 

**Returnerer**

Det nærmeste multiplum af *roundTo* under *værdi*.  
 
    (toint((value/roundTo)-0.5)) * roundTo

**Eksempler**

Udtryk | Resultat
---|---
`bin(4.5, 1)` | `4.0`
`bin(time(16d), 7d)` | `14d`
`bin(datetime(1953-04-15 22:25:07), 1d)`|  `datetime(1953-04-15)`


Følgende udtryk beregner et histogram over varighed, med en Malerbøtte størrelse på 1 sekund:

```AIQL

    T | summarize Hits=count() by bin(Duration, 1s)
```

### <a name="exp"></a>EXP

    exp(v)   // e raised to the power v
    exp2(v)  // 2 raised to the power v
    exp10(v) // 10 raised to the power v


### <a name="floor"></a>Floor

Et alias for [`bin()`](#bin).

### <a name="gamma"></a>gamma

[Funktionen gamma](https://en.wikipedia.org/wiki/Gamma_function)

**Syntaksen for**

    gamma(x)

**Argumenter**

* *x:* Et reelt tal

For positivt heltal, `gamma(x) == (x-1)!` For eksempel `gamma(5) == 4 * 3 * 2 * 1`.

Se også [loggamma](#loggamma).


### <a name="log"></a>Log

    log(v)    // Natural logarithm of v
    log2(v)   // Logarithm base 2 of v
    log10(v)  // Logarithm base 10 of v


`v`skal være et reelt tal > 0. Ellers returneres null.

### <a name="loggamma"></a>loggamma


Den naturlige logaritme til [gammafordelingen](#gamma)absolutte værdi.

**Syntaksen for**

    loggamma(x)

**Argumenter**

* *x:* Et reelt tal


### <a name="rand"></a>SLUMP

En generering af tilfældige tal.

* `rand()`-et reelt tal mellem 0,0 og 1,0
* `rand(n)`-et heltal mellem 0 og n-1




### <a name="sqrt"></a>KVROD

Funktionen kvadratroden.  

**Syntaksen for**

    sqrt(x)

**Argumenter**

* *x:* Et reelt tal > = 0.

**Returnerer**

* Et positivt tal så`sqrt(x) * sqrt(x) == x`
* `null`Hvis argumentet er negativt eller ikke kan konverteres til en `real` værdi. 




### <a name="toint"></a>toint

    toint(100)        // cast from long
    toint(20.7) == 21 // nearest int from double
    toint(20.4) == 20 // nearest int from double
    toint("  123  ")  // parse string
    toint(a[0])       // cast from dynamic
    toint(b.c)        // cast from dynamic

### <a name="tolong"></a>tolong

    tolong(20.7) == 21 // conversion from double
    tolong(20.4) == 20 // conversion from double
    tolong("  123  ")  // parse string
    tolong(a[0])       // cast from dynamic
    tolong(b.c)        // cast from dynamic


### <a name="todouble"></a>todouble

    todouble(20) == 20.0 // conversion from long or int
    todouble(" 12.34 ")  // parse string
    todouble(a[0])       // cast from dynamic
    todouble(b.c)        // cast from dynamic



## <a name="date-and-time"></a>Dato og klokkeslæt


[siden](#ago) | [dayofmonth](#dayofmonth) | [dayofweek](#dayofweek) |  [dayofyear](#dayofyear) |[datepart](#datepart) | [endofday](#endofday) | [endofmonth](#endofmonth) | [endofweek](#endofweek) | [endofyear](#endofyear) | [getmonth](#getmonth)|  [getyear](#getyear) | [nu](#now) | [startofday](#startofday) | [startofmonth](#startofmonth) | [startofweek](#startofweek) | [startofyear](#startofyear) | [todatetime](#todatetime) | [totimespan](#totimespan) | [weekofyear](#weekofyear)

### <a name="date-and-time-literals"></a>Dato og klokkeslæt konstanter

|||
---|---
**dato og klokkeslæt**|
`datetime("2015-12-31 23:59:59.9")`<br/>`datetime("2015-12-31")`|Gange er altid i UTC. Udelade datoen, der indeholder et klokkeslæt i dag.
`now()`|Det aktuelle klokkeslæt.
`now(`-*TimeSpan*`)`|`now()-`*TimeSpan*
`ago(`*TimeSpan*`)`|`now()-`*TimeSpan*
**TimeSpan**|
`2d`|2 dage
`1.5h`|1,5 time 
`30m`|30 minutter
`10s`|10 sekunder
`0.1s`|0,1 sekund
`100ms`| 100 millisekunder
`10microsecond`|
`1tick`|á 100 NS
`time("15 seconds")`|
`time("2")`| 2 dage
`time("0.12:34:56.7")`|`0d+12h+34m+56.7s`

### <a name="date-and-time-expressions"></a>Dato og klokkeslæt for udtryk

Udtryk |Resultat
---|---
`datetime("2015-01-02") - datetime("2015-01-01")`| `1d`
`datetime("2015-01-01") + 1d`| `datetime("2015-01-02")`
`datetime("2015-01-01") - 1d`| `datetime("2014-12-31")`
`2h * 24` | `2d`
`2d` / `2h` | `24`
`datetime("2015-04-15T22:33") % 1d` | `timespan("22:33")`
`bin(datetime("2015-04-15T22:33"), 1d)` | `datetime("2015-04-15T00:00")`
||
`<` |Mindre
`<=`|Mindre eller er lig med
`>` |Større
`>=`|Større eller er lig med
`<>`|Ikke er lig med
`!=`|Ikke er lig med 




### <a name="ago"></a>siden

Trækker det angivne tidsinterval fra den aktuelle UTC ur tid. Sådan `now()`, denne funktion kan bruges flere gange i en sætning og bliver klokkeslættet UTC, der refereres til det samme for alle forekomster.

**Syntaksen for**

    ago(a_timespan)

**Argumenter**

* *a_timespan*: Interval til at trække fra den aktuelle UTC ur tid (`now()`).

**Returnerer**

    now() - a_timespan

**Eksempel**

Alle rækker med et tidsstempel i den seneste time:

```AIQL

    T | where timestamp > ago(1h)
```

### <a name="datepart"></a>DatePart

    datepart("Day", datetime(2015-12-14)) == 14

Henter en bestemt del af en dato som et heltal.

**Syntaksen for**

    datepart(part, datetime)

**Argumenter**

* `part:String`-{"Year", "Måned", "dag", "Time", "Minut", "Andet", "Millisekunder", "Mikrosekunder", "Nanosecond"}
* `datetime`

**Returnerer**

Langt heltal, der repræsenterer den angivne del.


### <a name="dayofmonth"></a>dayofmonth

    dayofmonth(datetime("2016-05-15")) == 15 

Ordenstal for dag i måneden.

**Syntaksen for**

    dayofmonth(a_date)

**Argumenter**

* `a_date`: A `datetime`.


### <a name="dayofweek"></a>DayOfWeek

    dayofweek(datetime("2015-12-14")) == 1d  // Monday

Heltal antallet af dage siden den foregående søndag, som en `timespan`.

**Syntaksen for**

    dayofweek(a_date)

**Argumenter**

* `a_date`: A `datetime`.

**Returnerer**

Den `timespan` siden midnat i starten af den foregående søndag, rundes ned til et heltal antal dage.

**Eksempler**

```AIQL
dayofweek(1947-11-29 10:00:05)  // time(6.00:00:00), indicating Saturday
dayofweek(1970-05-11)           // time(1.00:00:00), indicating Monday
```

### <a name="dayofyear"></a>DAYOFYEAR

    dayofyear(datetime("2016-05-31")) == 152 
    dayofyear(datetime("2016-01-01")) == 1 

Ordenstal for dag i året.

**Syntaksen for**

    dayofyear(a_date)

**Argumenter**

* `a_date`: A `datetime`.

<a name="endofday"></a><a name="endofweek"></a><a name="endofmonth"></a><a name="endofyear"></a>
### <a name="endofday-endofweek-endofmonth-endofyear"></a>endofday, endofweek, endofmonth, endofyear

    dt = datetime("2016-05-23 12:34")

    endofday(dt) == 2016-05-23T23:59:59.999
    endofweek(dt) == 2016-05-28T23:59:59.999 // Saturday
    endofmonth(dt) == 2016-05-31T23:59:59.999 
    endofyear(dt) == 2016-12-31T23:59:59.999 


### <a name="getmonth"></a>getmonth

Få månedsnummer (1-12) fra en dato/klokkeslæt.

**Eksempel**

    ... | extend month = getmonth(datetime(2015-10-12))

    --> month == 10

### <a name="getyear"></a>getyear

Få årstallet fra en dato/klokkeslæt.

**Eksempel**

    ... | extend year = getyear(datetime(2015-10-12))

    --> year == 2015

### <a name="now"></a>nu

    now()
    now(-2d)

Den aktuelle UTC klokkeslæt, du kan også tidsforskydning ved en given timespan. Denne funktion kan bruges flere gange i en sætning og klokkeslættet, der refereres til vil være den samme efter alle forekomster.

**Syntaksen for**

    now([offset])

**Argumenter**

* *forskydning:* A `timespan`, føjet til det aktuelle klokkeslæt med UTC ur. Som standard: 0.

**Returnerer**

Det aktuelle klokkeslæt for UTC ur som en `datetime`.

    now() + offset

**Eksempel**

Bestemmer intervallet siden den begivenhed, der er identificeret med prædikatet:

```AIQL
T | where ... | extend Elapsed=now() - timestamp
```

<a name="startofday"></a><a name="startofweek"></a><a name="startofmonth"></a><a name="startofyear"></a>
### <a name="startofday-startofweek-startofmonth-startofyear"></a>startofday, startofweek, startofmonth, startofyear

    date=datetime("2016-05-23 12:34:56")

    startofday(date) == datetime("2016-05-23")
    startofweek(date) == datetime("2016-05-22") // Sunday
    startofmonth(date) == datetime("2016-05-01")
    startofyear(date) == datetime("2016-01-01")



### <a name="todatetime"></a>ToDateTime

Alias `datetime()`.

     todatetime("2016-03-28")
     todatetime("03/28/2016")
     todatetime("2016-03-28 14:34:00")
     todatetime("03/28/2016 2:34pm")
     todatetime("2016-03-28T14:34.5Z")
     todatetime(a[0]) 
     todatetime(b.c) 

Kontrollér, om en streng, der er en gyldig dato:

     iff(notnull(todatetime(customDimensions.myDate)),
         ..., ...)


### <a name="totimespan"></a>totimespan

Alias `timespan()`.

    totimespan("21d")
    totimespan("21h")
    totimespan(request.duration)

### <a name="weekofyear"></a>weekofyear

    weekofyear(datetime("2016-05-14")) == 21
    weekofyear(datetime("2016-01-03")) == 1
    weekofyear(datetime("2016-12-31")) == 53

Heltal resultatet repræsenterer ugenummeret ved den standard ISO 8601. Den første dag i ugen er søndag, og den første uge i året er den uge, der indeholder den årets første torsdag. (De sidste dage i året derfor kan indeholde nogle af dagene i ugen 1 i det næste år, eller de første dage kan indeholde nogle af arbejdsuge 52 eller 53 i det forrige år).


## <a name="string"></a>Streng

[countof](#countof) | [udtrække](#extract) | [extractjson](#extractjson)  | [isempty](#isempty) | [isnotempty](#isnotempty) | [notempty](#notempty) | [parseurl](#parseurl) | [erstatte](#replace) | [opdele](#split) | [strcat](#strcat) | [strlen](#strlen) | [understreng](#substring) | [tolower](#tolower) | [tostring](#tostring) | [toupper](#toupper)


### <a name="string-literals"></a>Strengkonstanter

Reglerne, der er de samme som i JavaScript.

Strenge kan skrives i enkelte eller dobbelte anførselstegn. 

Omvendt skråstreg (`\`) bruges til at escape-tegn som f.eks `\t` (fanen) `\n` (ny linje) og forekomster af det omsluttende anførselstegn.

* `'this is a "string" literal in single \' quotes'`
* `"this is a 'string' literal in double \" quotes"`
* `@"C:\backslash\not\escaped\with @ prefix"`

### <a name="obfuscated-string-literals"></a>Sløret Strengkonstanter

Sløret Strengkonstanter er strenge, som Analytics skal skjule ved visning af strengen (for eksempel, når sporing). Processen obfuscation erstatter alle sløret tegn ved en start (`*`) tegn.

For at danne en sløret strengkonstant, føjes `h` eller 'H'. Eksempel:

```
h'hello'
h@'world' 
h"hello"
```

### <a name="string-comparisons"></a>Strengsammenligninger

Operatoren|Beskrivelse|Store og små bogstaver|SAND eksempel
---|---|---|---
`==`|Er lig med |Ja| `"aBc" == "aBc"`
`<>` `!=`|Ikke er lig med|Ja| `"abc" <> "ABC"`
`=~`|Er lig med |Nej| `"abc" =~ "ABC"`
`!~`|Ikke er lig med |Nej| `"aBc" !~ "xyz"`
`has`|Højre-hand side (højre side) er et helt udtryk på højre-hand side (LHS)|Nej| `"North America" has "america"`
`!has`|Højre side er ikke en fuld ord i LHS|Nej|`"North America" !has "amer"` 
`hasprefix`|Højre side er et præfiks af et udtryk på LHS|Nej|`"North America" hasprefix "ame"`
`!hasprefix`|Højre side er ikke et præfiks for en hvilken som helst ord i LHS|Nej|`"North America" !hasprefix "mer"`
`hassuffix`|Højre side er et suffiks af et udtryk på LHS|Nej|`"North America" hassuffix "rth"`
`!hassuffix`|Højre side er ikke et suffiks for en hvilken som helst ord i LHS|Nej|`"North America" !hassuffix "mer"`
`contains` | Højre side forekommer som understreng af LHS|Nej| `"FabriKam" contains "BRik"`
`!contains`| Højre side, der ikke forekommer i LHS|Nej| `"Fabrikam" !contains "xyz"`
`containscs` | Højre side forekommer som understreng af LHS|Ja| `"FabriKam" contains "Kam"`
`!containscs`| Højre side, der ikke forekommer i LHS|Ja| `"Fabrikam" !contains "Kam"`
`startswith`|Højre side er en indledende understreng af LHS.|Nej|`"Fabrikam" startswith "fab"`
`!startswith`|Højre side er ikke en indledende understreng af LHS.|Nej|`"Fabrikam" !startswith "abr"`
`endswith`|Højre side er en terminal understreng af LHS.|Nej|`"Fabrikam" endswith "kam"`
`!endswith`|Højre side er ikke en terminal understreng af LHS.|Nej|`"Fabrikam" !endswith "ka"`
`matches regex`|LHS indeholder en match for højre side|Ja| `"Fabrikam" matches regex "b.*k"`
`in`|Lig med nogen af disse elementer|Ja|`"abc" in ("123", "345", "abc")`
`!in`|Ikke lig med nogen af disse elementer|Ja|`"bc" !in ("123", "345", "abc")`

Brug `has` eller `in` Hvis du er ved at teste for tilstedeværelse af et helt Leksikalsk ord – det vil sige, et symbol eller et alfanumerisk ord afgrænset af ikke-alfanumeriske tegn eller starten eller slutningen af feltet. `has`udfører hurtigere end `contains`, `startswith` eller `endswith`. Først af disse forespørgsler kører hurtigere:

    EventLog | where continent has "North" | count;
    EventLog | where continent contains "nor" | count





### <a name="countof"></a>countof

    countof("The cat sat on the mat", "at") == 3
    countof("The cat sat on the mat", @"\b.at\b", "regex") == 3

Tæller forekomster af en understreng i en streng. Almindelig streng matches overlappe; regex matcher ikke.

**Syntaksen for**

    countof(text, search [, kind])

**Argumenter**

* *tekst:* En streng.
* *Søg:* Almindelig streng eller regulære udtryk til at matche inde i *teksten*.
* *type:* `"normal"|"regex"` Standard `normal`. 

**Returnerer**

Antallet gange, der kan sammenlignes søgestrengen i beholderen. Almindelig streng matches overlappe; regex matcher ikke.

**Eksempler**

|||
|---|---
|`countof("aaa", "a")`| 3 
|`countof("aaaa", "aa")`| 3 (ikke 2!)
|`countof("ababa", "ab", "normal")`| 2
|`countof("ababa", "aba")`| 2
|`countof("ababa", "aba", "regex")`| 1
|`countof("abcabc", "a.c", "regex")`| 2
    



### <a name="extract"></a>udtrække

    extract("x=([0-9.]+)", 1, "hello x=45.6|wo") == "45.6"

Få en match for et [regulære udtryk](#regular-expressions) fra en tekststreng. Du kan også konverterer den derefter den udtrukne understreng til den angivne type.

**Syntaksen for**

    extract(regex, captureGroup, text [, typeLiteral])

**Argumenter**

* *regex:* En [regulære udtryk](#regular-expressions).
* *captureGroup:* Et positivt `int` konstant, der angiver gruppen capture til at uddrage. 0 står for hele matcher, 1 for den værdi, der svarer til først '(' parentes')' i det regulære udtryk, 2 eller mere for efterfølgende parenteser.
* *tekst:* A `string` til at søge.
* *typeLiteral:* Et valgfrit type konstant (f.eks., `typeof(long)`). Hvis, konverteres den udtrukne understreng til denne type. 

**Returnerer**

Hvis *regex* finder et match i *tekst*: understreng sammenlignes med det angivne capture gruppe *captureGroup*, du kan også konverteres til *typeLiteral*.

Hvis der ikke findes et tilsvarende, eller typekonvertering mislykkes: `null`. 

**Eksempler**

Eksempel strengen `Trace` er søger efter en definition af `Duration`. Matcher konverteres til `real`, og derefter multipliceret med en tid konstant (`1s`) så `Duration` er af typen `timespan`. I dette eksempel er lig med 123.45 sekunder:

```AIQL
...
| extend Trace="A=1, B=2, Duration=123.45, ..."
| extend Duration = extract("Duration=([0-9.]+)", 1, Trace, typeof(real)) * time(1s) 
```

I dette eksempel er svarer til `substring(Text, 2, 4)`:

```AIQL
extract("^.{2,2}(.{4,4})", 1, Text)
```

<a name="notempty"></a>
<a name="isnotempty"></a>
<a name="isempty"></a>
### <a name="isempty-isnotempty-notempty"></a>IsEmpty, isnotempty, notempty

    isempty("") == true

True, hvis argumentet er en tom streng eller er null.
Se også [isnull](#isnull).


**Syntaksen for**

    isempty([value])


    isnotempty([value])


    notempty([value]) // alias of isnotempty

**Returnerer**

Angiver, om argumentet er en tom streng eller isnull.

|x|IsEmpty(x)
|---|---
| "" | SAND
|"x" | FALSK
|parsejson("")|SAND
|parsejson("[]")|FALSK
|parsejson("{}")|FALSK


**Eksempel**


    T | where isempty(fieldName) | count


### <a name="parseurl"></a>parseurl

Opdele en URL-adresse til dens dele.

**Syntaksen for**

    parseurl(urlstring)

**Argumenter**

* *urlstring:* EN URL-ADRESSE.

**Returnerer**

Et objekt, der indeholder delene som strenge.

**Eksempel**

    parseurl("http://user:pass@contoso.com/icecream/buy.aspx?a=1&b=2#tag")

    {
    "Scheme" : "http",
    "Host" : "contoso.com",
    "Port" : "80",
    "Path" : "/icecream/buy.aspx",
    "Username" : "user",
    "Password" : "pass",
    "Query Parameters" : {"a":"1","b":"2"},
    "Fragment" : "tag"
    }

### <a name="replace"></a>Erstat

Erstatte alle forekomster af regex med en anden streng.

**Syntaksen for**

    replace(regex, rewrite, text)

**Argumenter**

* *regex:* [Regulære udtryk](https://github.com/google/re2/wiki/Syntax) til at søge efter *tekst*. Det kan indeholde capture grupper i '('parentes')'. 
* *omskrivning:* Erstatning regex for en hvilken som helst værdi, der er foretaget af *matchingRegex*. Brug `\0` til at referere til hele matcher `\1` for gruppen første capture `\2` og så videre til efterfølgende capture grupper.
* *tekst:* En streng.

**Returnerer**

*tekst* efter erstatte alle forekomster af *regex* med evaluering af *omskrive*. Matcher overlapper ikke.

**Eksempel**

Denne sætning:

```AIQL
range x from 1 to 5 step 1
| extend str=strcat('Number is ', tostring(x))
| extend replaced=replace(@'is (\d+)', @'was: \1', str)
```

Har følgende resultater:

| x    | str | erstattet|
|---|---|---|
| 1    | Tal er 1.000000  | Tal er: 1.000000|
| 2    | Tal er 2.000000  | Tal er: 2.000000|
| 3    | Tal er 3.000000  | Tal er: 3.000000|
| 4    | Tal er 4.000000  | Tal er: 4.000000|
| 5    | Tal er 5.000000  | Tal er: 5.000000|
 



### <a name="split"></a>opdele

    split("aaa_bbb_ccc", "_") == ["aaa","bbb","ccc"]

Opdeler en given streng efter en bestemt afgrænser, og returnerer en strengmatrix med conatined understrengene. Du kan vælge en bestemt understreng kan være returnerede Hvis findes.

**Syntaksen for**

    split(source, delimiter [, requestedIndex])

**Argumenter**

* *kilde*: kildestrengen, der er opdelt efter det angivne afgrænser.
* *afgrænser*: den afgrænser, der skal bruges for at opdele strengen source.
* *requestedIndex*: et valgfrit baseret på nul indeks `int`. Hvis, den returnerede strengmatrix skal indeholde den nødvendige understreng, hvis der findes. 

**Returnerer**

En strengmatrix, der indeholder understrengene af den angivne datakilde streng, der er adskilt af den angivne afgrænser.

**Eksempler**

```
split("aa_bb", "_")           // ["aa","bb"]
split("aaa_bbb_ccc", "_", 1)  // ["bbb"]
split("", "_")                // [""]
split("a__b")                 // ["a","","b"]
split("aabbcc", "bb")         // ["aa","cc"]
```




### <a name="strcat"></a>strcat

    strcat("hello", " ", "world")

Sammenkæder mellem 1 og 16 argumenter, der skal være strenge.

### <a name="strlen"></a>strlen

    strlen("hello") == 5

Længde af en streng.

### <a name="substring"></a>understreng

    substring("abcdefg", 1, 2) == "bc"

Udtrække en understreng fra en bestemt kildestreng begyndende fra et bestemt indeks. Du kan også kan længden på den ønskede understreng angives.

**Syntaksen for**

    substring(source, startingIndex [, length])

**Argumenter**

* *kilde:* Strengen source, som understrengen hentes fra.
* *startingIndex:* Baseret på nul tegn startpositionen for den ønskede understreng.
* *længde:* En valgfri parameter, der kan bruges til at angive det ønskede antal tegn i understrengen. 

**Returnerer**

En understreng fra den angivne streng. Understrengen starter ved startingIndex (baseret på nul) Tegnplacering og fortsætter til slutningen af tegnet streng eller længde, hvis angivet.

**Eksempler**

```
substring("123456", 1)        // 23456
substring("123456", 2, 2)     // 34
substring("ABCD", 0, 2)       // AB
```

### <a name="tolower"></a>tolower

    tolower("HELLO") == "hello"

Konverterer en tekststreng til små bogstaver.

### <a name="toupper"></a>toupper

    toupper("hello") == "HELLO"

Konverterer en tekststreng til store bogstaver.



### <a name="guids"></a>GUID

    guid(00000000-1111-2222-3333-055567f333de)


## <a name="arrays-objects-and-dynamic"></a>Matrixer, objekter og dynamisk

[konstanter](#dynamic-literals) | [konvertering](#casting-dynamic-objects) | [operatorer](#operators) | [lade delsætninger](#dynamic-objects-in-let-clauses)
<br/>
[arraylength](#arraylength) | [extractjson](#extractjson) | [parsejson](#parsejson) | [område](#range) | [treepath](#treepath) | [todynamic](#todynamic) | [zip](#zip)


Her er resultatet af en forespørgsel på en undtagelse for programmet indsigt. Værdien i `details` er en matrix.

![](./media/app-insights-analytics-reference/310.png)

**Indeksering:** Indeks matrixer og objekter lige som JavaScript:

    exceptions | take 1
  	| extend 
        line = details[0].parsedStack[0].line,
        stackdepth = arraylength(details[0].parsedStack)

* Men bruge `arraylength` og andre Analytics-funktioner (ikke ".length"!)

**Brugte** I nogle tilfælde er det nødvendigt at konverteres et element, du udtrækker fra et objekt, fordi dens type kan variere. For eksempel `summarize...to` skal have en bestemt type:

    exceptions 
  	| summarize count() 
      by toint(details[0].parsedStack[0].line)

    exceptions 
  	| summarize count() 
      by tostring(details[0].parsedStack[0].assembly)

**Konstanter** For at oprette en eksplicit matrix eller egenskab pose objekt skal du skrive det som en JSON streng og cast:

    todynamic('[{"x":"1", "y":"32"}, {"x":"6", "y":"44"}]')


**mvexpand:** Hvis du vil trække ud fra hinanden egenskaberne for et objekt i separate rækker, skal du bruge mvexpand:

    exceptions | take 1 
  	| mvexpand details[0].parsedStack[0]


![](./media/app-insights-analytics-reference/410.png)


**treepath:** Sådan finder du alle stier i en kompleks objekt:

    exceptions | take 1 | project timestamp, details 
  	| extend path = treepath(details) 
  	| mvexpand path


![](./media/app-insights-analytics-reference/420.png)

**buildschema:** Sådan finder du det mindste skema, der giver adgang til alle værdierne af udtrykket i tabellen:

    exceptions | summarize buildschema(details)

Resultat:

    { "`indexer`":
     {"id":"string",
       "parsedStack":
       { "`indexer`": 
         {  "level":"int",
            "assembly":"string",
            "fileName":"string",
            "method":"string",
            "line":"int"
         }},
      "outerId":"string",
      "message":"string",
      "type":"string",
      "rawStack":"string"
    }}

Bemærk, at `indexer` bruges til at markere, hvor du skal bruge et numerisk indeks. For denne skema nogle gyldige stier, der ville (hvis disse eksempel indeks er i område):

    details[0].parsedStack[2].level
    details[0].message
    arraylength(details)
    arraylength(details[0].parsedStack)



### <a name="array-and-object-literals"></a>Matrix og objekt konstanter

Hvis du vil oprette en dynamisk konstant, skal du bruge `parsejson` (alias `todynamic`) med en JSON streng argument:

* `parsejson('[43, 21, 65]')`-en matrix med tal
* `parsejson('{"name":"Alan", "age":21, "address":{"street":432,"postcode":"JLK32P"}}')` 
* `parsejson('21')`-en enkelt værdi af dynamiske type, der indeholder et tal
* `parsejson('"21"')`-en enkelt værdi af dynamiske type, der indeholder en streng

Bemærk, at til forskel fra JavaScript, JSON sikrer brugen af dobbelte anførselstegn (`"`) omkring strenge. Det er derfor generelt nemmere at tilbud en JSON-kodet streng konstanter ved hjælp af enkelt anførselstegn (`'`).

I dette eksempel opretter en dynamisk værdi og derefter bruger felterne:

```

T
| extend person = parsejson('{"name":"Alan", "age":21, "address":{"street":432,"postcode":"JLK32P"}}')
| extend n = person.name, add = person.address.street
```


### <a name="dynamic-object-functions"></a>Dynamisk objekt funktioner

|||
|---|---|
| *værdi* `in` *matrix*| True, hvis der er et element i *matrix* , der == *værdi*<br/>`where City in ('London', 'Paris', 'Rome')`
| *værdi* `!in` *matrix*| True, hvis der er ingen element i *matrix* , der == *værdi*
|[`arraylength(`matrix`)`](#arraylength)| Hvis det ikke er en matrix er null
|[`extractjson(`stien, objekt`)`](#extractjson)|Bruger stien til at navigere til objektet.
|[`parsejson(`kilde`)`](#parsejson)| Omdannes en JSON-streng til et dynamisk objekt.
|[`range(`fra, på trin`)`](#range)| En matrix med værdier
|[`mvexpand`listColumn](#mvexpand-operator) | Kopieres en række for hver værdi på en liste i en bestemt celle.
|[`summarize buildschema(`kolonne`)`](#buildschema) |Indstiller type skemaet fra kolonnen indhold
|[`summarize makelist(`kolonnen`)`](#makelist)| Samkopieres grupper af rækker og placerer værdierne for kolonnen i en matrix.
|[`summarize makeset(`kolonne`)`](#makeset) | Samkopieres grupper af rækker og placerer værdierne for kolonnen i en matrix uden gentagelse.

### <a name="dynamic-objects-in-let-clauses"></a>Dynamiske objekter i Lad delsætninger


[Lade delsætninger](#let-clause) store dynamiske værdier som strenge, så disse to delsætninger er ækvivalente, og begge skal den `parsejson` (eller `todynamic`) før nummeret anvendes:

    let list1 = '{"a" : "somevalue"}';
    let list2 = parsejson('{"a" : "somevalue"}');

    T | project parsejson(list1).a, parsejson(list2).a




### <a name="arraylength"></a>arraylength

Antallet af elementer i en dynamisk matrix.

**Syntaksen for**

    arraylength(array)

**Argumenter**

* *matrix:* A `dynamic` værdi.

**Returnerer**

Antallet af elementer i *matrix*, eller `null` Hvis *matrix* ikke er en matrix.

**Eksempler**

```
arraylength(parsejson('[1, 2, 3, "four"]')) == 4
arraylength(parsejson('[8]')) == 1
arraylength(parsejson('[{}]')) == 1
arraylength(parsejson('[]')) == 0
arraylength(parsejson('{}')) == null
arraylength(parsejson('21')) == null
```



### <a name="extractjson"></a>extractjson

    extractjson("$.hosts[1].AvailableMB", EventText, typeof(int))

Få et bestemt element af en JSON tekst ved hjælp af en path-udtryk. Du kan også konvertere den udtrukne streng til en bestemt type.


**Syntaksen for**

```

    string extractjson(jsonPath, dataSource) 
    resulttype extractjson(jsonPath, dataSource, typeof(resulttype))
```


**Returnerer**

Denne funktion udfører en JsonPath forespørgsel i datakilden, der indeholder en gyldig streng, JSON, du kan også konvertere denne værdi til en anden type afhængigt af det tredje argument.



**Eksempel**

[Kantet parentes] notation og prik notation er ens:

    ... | extend AvailableMB = extractjson("$.hosts[1].AvailableMB", EventText, typeof(int)) | ...

    ... | extend AvailableMD = extractjson("$['hosts'][1]['AvailableMB']", EventText, typeof(int)) | ...



**Tip om ydeevne**

* Anvende where-delsætning før du bruger`extractjson()`
* Overvej at bruge en regulære udtryk match med [udtrække](#extract) i stedet. Dette kan køre meget hurtigere og træder i kraft, hvis JSON er oprettet fra en skabelon.
* Brug `parsejson()` Hvis du vil udtrække mere end én værdi fra JSON.
* Overveje at have JSON parses på indtagelse ved erklæring af typen kolonnen skal være dynamisk.

### <a name="json-path-expressions"></a>JSON sti udtryk

|||
|---|---|
|`$`|Rodobjekt|
|`@`|Aktuelle objekt|
|`[0]`|Matrix sænket skrift|
|`.`eller`[0]` | Underordnet|

*(Vi ikke i øjeblikket implementere jokertegn, rekursion, union eller udsnit.)*




### <a name="parsejson"></a>parsejson

Fortolker en `string` som en [JSON værdi](http://json.org/)) og returnerer værdien som `dynamic`. Det er bedre end ved hjælp af `extractjson()` når du har brug for til at uddrage mere end ét element i et sammensat JSON-objekt.

**Syntaksen for**

    parsejson(json)

**Argumenter**

* *json:* Et JSON-dokument.

**Returnerer**

Et objekt af typen `dynamic` angivet af *json*.

**Eksempel**

I følgende eksempel, når `context_custom_metrics` er en `string` , ser sådan ud: 

```
{"duration":{"value":118.0,"count":5.0,"min":100.0,"max":150.0,"stdDev":0.0,"sampledValue":118.0,"sum":118.0}}
```

derefter følgende fragment henter værdien af den `duration` slot i objektet, og fra, at den henter to pladser `duration.value` og  `duration.min` (`118.0` og `110.0`henholdsvis).

```AIQL
T
| ...
| extend d=parsejson(context_custom_metrics) 
| extend duration_value=d.duration.value, duration_min=d["duration"]["min"]
```



### <a name="range"></a>område

Den `range()` funktionen (ikke skal forveksles med den `range` operator) genererer en dynamisk matrix, der holder en række lige store værdier.

**Syntaksen for**

    range(start, stop, step)

**Argumenter**

* *start:* Værdien af det første element i den viste matrix. 
* *stop:* Værdien af det sidste element i den viste matrix eller den mindste værdi, der er større end det sidste element i den viste matrix og inden for et heltal multiplum af *trin* under *start*.
* *trin:* Forskellen mellem to efterfølgende elementer i matrixen.

**Eksempler**

I følgende eksempel returneres `[1, 4, 7]`:

```AIQL
range(1, 8, 3)
```

I følgende eksempel returneres en matrix, der holder alle dage i året 2015:

```AIQL

    range(datetime(2015-01-01), datetime(2015-12-31), 1d)
```

### <a name="todynamic"></a>todynamic

    todynamic('{"a":"a1", "b":["b1", "b2"]}')

Konverterer en streng til en dynamisk værdi.

### <a name="treepath"></a>treepath

    treepath(dynamic_object)

Optælles alle sti-udtryk, der identificerer blade i et dynamisk objekt. 

**Returnerer**

En matrix med stien udtryk.

**Eksempler**

    treepath(parsejson('{"a":"b", "c":123}')) 
    =>       ["['a']","['c']"]
    treepath(parsejson('{"prop1":[1,2,3,4], "prop2":"value2"}'))
    =>       ["['prop1']","['prop1'][0]","['prop2']"]
    treepath(parsejson('{"listProperty":[100,200,300,"abcde",{"x":"y"}]}'))
    =>       ["['listProperty']","['listProperty'][0]","['listProperty'][0]['x']"]

Note, "[0]" Angiver tilstedeværelse af en matrix, men angiver ikke det indeks, der bruges af en bestemt sti.

### <a name="zip"></a>ZIP

    zip(list1, list2, ...)

Kombinerer et sæt af lister på en liste over tupler.

* `list1...`: En liste over værdier

**Eksempler**

    zip(parsejson('[1,3,5]'), parsejson('[2,4,6]'))
    => [ [1,2], [3,4], [5,6] ]

    
    zip(parsejson('[1,3,5]'), parsejson('[2,4]'))
    => [ [1,2], [3,4], [5,null] ]


### <a name="names"></a>Navne

Navne kan være op til 1024 tegn. De store og små bogstaver og kan indeholde bogstaver, tal og understregninger (`_`). 

Tilbud en navn ved hjælp af ['... '] or [" ... "] til at medtage andre tegn, eller brug en nøgleord som et navn. Eksempel:

```AIQL

    requests | 
    summarize  ["distinct urls"] = dcount(name) // non-alphanumerics
    by  ['where'] = client_City, // using a keyword as a name
        ['outcome!'] = success // non-alphanumerics
```


|||
|---|---|
|['path\\file\n\'x\''] | Brug \ escape-tegn|
|["d-e.=/f#\n"] | |
|[@'path\file'] | Ingen skifter - \ er ordret|
|[@"\now& derefter\"] | |
|[hvor] | Brug af et sprog nøgleord som et navn|

[AZURE.INCLUDE [app-insights-analytics-footer](../../includes/app-insights-analytics-footer.md)]


