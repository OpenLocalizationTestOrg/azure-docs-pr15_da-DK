<properties
    pageTitle="Log Analytics søge reference | Microsoft Azure"
    description="Log analyser Søg reference beskriver sproget, søgning og giver forespørgselssyntaksen generelle indstillinger, der kan bruges til at søge efter data og filtrering udtryk for at indsnævre søgningen."
    services="log-analytics"
    documentationCenter=""
    authors="bandersmsft"
    manager="jwhit"
    editor=""/>

<tags
    ms.service="log-analytics"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/25/2016"
    ms.author="banders"/>


# <a name="log-analytics-search-reference"></a>Log Analytics søge reference

I følgende afsnit reference om søgning sprog beskrives indstillingerne generelle forespørgsel syntaks, kan du bruge hvornår søge efter data og filtrering udtryk for at indsnævre søgningen. Den beskriver også kommandoer, som du kan bruge til at handle ud fra de data, der er hentet.

Du kan læse mere om de felter, der returneres i søgninger og facetter, der hjælper dig med at dill-i samme kategorier af data i [søgefeltet og grene henviser til sektion](#search-field-and-facet-reference).

## <a name="general-query-syntax"></a>Generelle forespørgselssyntaksen

Syntaks:

```
filterExpression | command1 | command2 …
```

Filterudtrykket (`filterExpression`) definerer "where"-betingelse for forespørgslen. Kommandoerne, der gælder for de resultater, der returneres af forespørgslen. Flere kommandoer skal være adskilt af tegnet (|).

### <a name="general-syntax-examples"></a>Generel syntakseksempler

Eksempler:

```
system
```

Denne forespørgsel returnerer resultater, der indeholder ordet "system" i en hvilken som helst felt, der er blevet indekseret for hele teksten eller vilkår søgning.

>[AZURE.NOTE] Ikke alle felter er indekseret denne måde, men de mest almindelige tekstdata felter (såsom beskrivelser og navne) typisk ville være.

```
system error
```

Denne forespørgsel returnerer resultater, der indeholder de ord *system* og *fejl*.

```
system error | sort ManagementGroupName, TimeGenerated desc | top 10
```

Denne forespørgsel returnerer resultater, der indeholder de ord *system* og *fejl*. Det sorteres resultaterne efter feltet *ManagementGroupName* (i stigende rækkefølge) og derefter *TimeGenerated* (i faldende rækkefølge). Det kræver kun de første 10 resultater.

>[AZURE.IMPORTANT] Der skelnes mellem alle feltnavne og værdier for felterne streng og tekst.

## <a name="filter-expression"></a>Filterudtrykket

Følgende underafsnit Forklar filterudtryk.

### <a name="string-literals"></a>Strengkonstanter

En strengkonstant er en streng, der ikke genkendes af parseren som et nøgleord eller en foruddefineret datatype (for eksempel et tal eller dato).

Eksempler:

```
These all are string literals
```

Denne forespørgsel søger efter resultater, der indeholder forekomster af alle fem ord. For at udføre en kompleks streng søgning skal du omslutte strengkonstant i anførselstegn, f.eks.:

```
" Windows Server"
```

Dette returnerer kun resultater med nøjagtigt match til "Windows Server".

### <a name="numbers"></a>Tal

Parseren understøtter decimalt heltal og flydende tal syntaks for numeriske felter.

Eksempler:

```
Type:Perf 0.5
```

```
HTTP 500
```

### <a name="datetime"></a>Dato og klokkeslæt

Hver enkelt dataelement i systemet har en egenskab, *TimeGenerated* , der repræsenterer den oprindelige datoen og klokkeslættet for posten. Visse typer data kan desuden har flere dato/klokkeslæt-felter (for eksempel *LastModified*).

Vælgeren tidslinje diagram og klokkeslæt i Log Analytics viser en fordeling af resultater over tid (i henhold til den aktuelle forespørgsel køres), baseret på feltet *TimeGenerated* . Dato og klokkeslæt felter har en bestemt tekststreng format, der kan bruges i forespørgsler til at begrænse forespørgslen til en bestemt tidsramme. Du kan også bruge syntaksen til at referere til relative tidsintervaller (eksempelvis "mellem 3 dage siden og 2 timer siden").

Syntaks:

```
yyyy-mm-ddThh:mm:ss.dddZ
```

```
yyyy-mm-ddThh:mm:ss.ddd
```

```
yyyy-mm-ddThh:mm:ss
```

```
yyyy-mm-ddThh:mm:ss
```

```
yyyy-mm-ddThh:mm
```

```
yyyy-mm-dd
```


Eksempel:

```
TimeGenerated:2013-10-01T12:20
```

Den forrige kommando returnerer kun poster med en *TimeGenerated* værdi af præcis 12:20 på 1 oktober 2013. Det er sandsynligvis ikke, at den giver et resultat, men du forstår formålet.

Parseren understøtter også den instruktion dato/klokkeslæt-værdi på nu.


Igen, er det sandsynligvis ikke, at dette giver et resultat da data ikke gøre det hele systemet så hurtigt.

Disse eksempler er dokumentkomponenter skal bruges til relative og absolutte datoer. I de næste tre underafsnit, får vi beskriver, hvordan du bruger dem i mere avancerede filtre med eksempler, der bruger relative datointervaller.

### <a name="datetime-math"></a>Dato og klokkeslæt matematisk

Brug dato/klokkeslæt matematiske operatorer til forskydning eller afrunde dato/klokkeslæt-værdi ved hjælp af simple beregninger af dato/klokkeslæt.

Syntaks:

```
datetime/unit
```

```
datetime[+|-]count unit
```

|Operatoren|Beskrivelse|
|---|---|
|/|Afrunder dato og klokkeslæt til den angivne enhed. Eksempel: Nu / dag Runder den aktuelle dato og klokkeslæt til midnat på den aktuelle dag.|
|+ eller -|Forskyder dato/klokkeslæt med det angivne antal enheder. Eksempler: nu + 1 time forskyder den aktuelle dato og klokkeslæt ved en time ahead.2013-10-01T12:00-10 dage forskyder datoværdien tilbage efter 10 dage.|



Du kan sammenkæde dato/klokkeslæt matematiske operatorer, f.eks.:

```
NOW+1HOUR-10MONTHS/MINUTE
```

I følgende tabel vises de understøttede dato/klokkeslæt-enheder.

Dato/klokkeslæt-enhed|Beskrivelse
---|---
ÅR, ÅR|Runder til aktuelle år, eller forskyder med det angivne antal år.
MÅNEDEN, MÅNEDER|Runder til aktuelle måned eller forskyder med det angivne antal måneder.
DAG, DAGE, DATO|Runder til dags dato i måneden eller forskyder med det angivne antal dage.
TIME, TIMER|Runder til aktuelle time, eller forskyder med det angivne antal timer.
MINUT, MINUTTER|Runder til aktuelle minut, eller forskyder med det angivne antal minutter.
SEKUNDER,|Runder til den aktuelle andet, eller forskyder med det angivne antal sekunder.
MILLISEKUNDER, MILLISEKUNDER, MILLI, MILLIS|Runder til aktuelle millisekunder, eller forskyder med det angivne antal millisekunder.


### <a name="field-facets"></a>Feltet facetter

Ved hjælp af feltet facetter, kan du angive betingelsen søgningen til bestemte felter og deres nøjagtige værdier, i modsætning til at skrive "gratis tekst" forespørgsler til forskellige ord i hele indekset. Vi har allerede bruges følgende syntaks i flere eksempler i forrige afsnit. Her, giver vi mere komplekse eksempler.

**Syntaksen for**

```
field:value
```

```
field=value
```

**Beskrivelse**

Søger i feltet for den specifikke værdi. Værdien, der kan være en strengkonstant, tal eller dato og klokkeslæt.

Eksempel:

```
TimeGenerated:NOW
```

```
ObjectDisplayName:"server01.contoso.com"
```

```
SampleValue:0.3
```

**Syntaksen for**

*feltet > værdi*

*feltet < værdi*

*feltet > = værdi*

*feltet < = værdi*

*feltet! = værdi*

**Beskrivelse**

Indeholder sammenligninger.

Eksempel:

```
TimeGenerated>NOW+2HOURS
```

**Syntaksen for**

```
field:[from..to]
```

**Beskrivelse**

Indeholder område faceting.

Eksempel:

```
TimeGenerated:[NOW..NOW+1DAY]
```

```
SampleValue:[0..2]
```

### <a name="logical-operators"></a>Logiske operatorer

Forespørgselssprog understøtter logiske operatorer (*og*, *eller*og *ikke*) og deres C-typografi aliases (*&&*, *||*, og *!*) henholdsvis. Du kan bruge parenteser til at gruppere disse operatorer.

Eksempler:

```
system OR error

```

```
Type:Alert AND NOT(Severity:1 OR ObjectId:"8066bbc0-9ec8-ca83-1edc-6f30d4779bcb8066bbc0-9ec8-ca83-1edc-6f30d4779bcb")
```

Du kan udelade den logiske operator for argumenterne på øverste niveau filter. I dette tilfælde antages operatoren AND.


Filterudtrykket|Svarer til
---|---
systemfejl|system og fejl
system "Windows Server" eller alvorlighed: 1|system og ("Windows Server" eller alvorlighed: 1)

### <a name="wildcarding"></a>Brug af jokertegn

Forespørgselssproget understøtter ved hjælp af den (*\*) tegn til at repræsentere en eller flere tegn for en værdi i en forespørgsel.

Eksempler:

 Finde alle computere med "SQL" i navnet som "Redmond-SQL".

```
Type=Event Computer=*SQL*
```

>[AZURE.NOTE] Jokertegn kan ikke bruges i forbindelse med anførselstegn i dag. Meddelelsen =`"*This text*"` vil overveje det (\*) bruges som en konstant (\*) tegn.
## <a name="commands"></a>Kommandoer

Kommandoerne, der gælder for de resultater, der returneres af forespørgslen. Brug tegnet (|) til at anvende en kommando på hentede resultaterne. Flere kommandoer skal være adskilt af tegnet.

>[AZURE.NOTE] Kommandonavne kan skrives i store bogstaver eller små bogstaver, i modsætning til feltnavnene og data.

### <a name="sort"></a>Sortér

Syntaks:

    sort field1 asc|desc, field2 asc|desc, …

Sorterer resultaterne efter bestemte felter. Asc/desc præfikset er valgfrit. Hvis de udelades, antages *asc* sorteringsrækkefølgen. Hvis en forespørgsel ikke bruger kommandoen *Sortér* eksplicit, Sortér **TimeGenerated** desc er standardfunktionsmåden, og den returnerer altid de seneste resultater først.

### <a name="toplimit"></a>Top/grænse

Syntaks:

    top number


    limit number
Begrænser svar på de øverste N resultater.

Eksempel:

    Type:Alert errors detected | top 10

Returnerer de øverste 10 tilsvarende resultater.

### <a name="skip"></a>Spring over

Syntaks:

    skip number

Springer antallet af resultater, der vises.

Eksempel:

    Type:Alert errors detected | top 10 | skip 200

Returnerer øverste 10 tilsvarende resultater fra og med resultatet 200.

### <a name="select"></a>Vælg

Syntaks:

    select field1, field2, ...

Begrænser resultater til de felter, du vælger.

Eksempel:

    Type:Alert errors detected | select Name, Severity

Begrænser de returnerede resultater felter til *navn* og *alvor*.

### <a name="measure"></a>Mål

Kommandoen *mål* bruges til at anvende statistiske funktioner til rå søgeresultaterne. Dette er meget nyttigt at få *Gruppér efter* visninger over dataene. Når du bruger kommandoen *måling* , viser Log Analytics Søg en tabel med aggregerede resultater.

Syntaks:

    measure aggregateFunction1([aggregatedField]) [as fieldAlias1] [, aggregateFunction2([aggregatedField2]) [as fieldAlias2] [, ...]] by groupField1 [, groupField2 [, groupField3]]  [interval interval]


    measure aggregateFunction1([aggregatedField]) [as fieldAlias1] [, aggregateFunction2([aggregatedField2]) [as fieldAlias2] [, ...]]  interval interval



Samler resultaterne af *groupField* og beregner aggregeret måle værdier ved hjælp af *aggregatedField*.


|Mål statistisk funktion|Beskrivelse|
|---|---|
|*aggregateFunction*|Navnet på den (og små bogstaver) aggregatfunktion. De følgende aggregatfunktioner understøttes: Tæl Maks MIN SUM AVG STAFV COUNTDISTINCT fraktil ## eller PCT ## (## er et vilkårligt tal mellem 1 til 99)|
|*aggregatedField*|Det felt, er der aggregeres. Dette felt er valgfrit for funktionen Tæl samling, men skal være et eksisterende numerisk felt for SUM, MAX, MIN, AVG STDDEV eller fraktil ## eller PCT ## (## er et vilkårligt tal mellem 1 til 99). AggregatedField kan også være en af funktionerne Forlæng understøttes.|
|*fieldAlias*|(Valgfrit) alias for den beregnede aggregerede værdi. Hvis ikke er angivet, bliver feltnavnet AggregatedValue.|
|*groupField*|Navnet på det felt, resultatsættet er grupperet efter.|
|*Interval*|Et tidsinterval i formatet:**nnnNAME** hvor: nnn er positivt heltal. **Navn** er interval navnet. Understøttede interval navne medtage (store og små bogstaver): MILLISEKUNDER [S] anden [S] minut [S] time [S] dag [S] måned [S] år [S]|


Indstillingen opdateringsinterval kan kun bruges i dato/klokkeslæt gruppefelter (såsom *TimeGenerated* og *TimeCreated*). I øjeblikket dette gennemtvinges ikke af tjenesten, men et felt uden dato/klokkeslæt, der sendes til backend-version, der forårsager en kørselsfejl. Når skemavalidering er implementeret, afviser service API forespørgsler, der bruger felter uden dato og klokkeslæt til interval sammenlægning. Den aktuelle *måling* implementering understøtter interval gruppering for en hvilken som helst aggregatfunktion.

Hvis BY-delsætningen udelades, men er angivet et interval (som en anden syntaks), forudsættes feltet *TimeGenerated* er som standard.

Eksempler:

**Eksempel 1**

    Type:Alert | measure count() as Count by ObjectId

*FORKLARING*

Grupperer alarmer ved *ObjectID* og beregner antallet af beskeder for hver gruppe. Aggregeret værdi returneres som feltet *Tæl* (alias).

**Eksempel 2**

    Type:Alert | measure count() interval 1HOUR

*FORKLARING*

De vigtige beskeder, grupperes efter 1-timers mellemrum ved hjælp af feltet *TimeGenerated* , og returnerer antallet af beskeder i hvert interval.

**Eksempel 3**

    Type:Alert | measure count() as AlertsPerHour interval 1HOUR

*FORKLARING*

Samme som i forrige eksempel, men med et aggregeret feltalias (*AlertsPerHour*).

**Eksempel 4**

    * | måle count() ved TimeCreated interval 5DAYS

*FORKLARING*

Grupperer resultaterne efter 5 dagsintervaller ved hjælp af feltet *TimeCreated* , og returnerer antallet resultater i hvert interval.

**Eksempel 5**

    Type:Alert | measure max(Severity) by WorkflowName

*FORKLARING*

Grupperer påmindelserne efter arbejdsbelastningen navn, og returnerer den maksimale alvorlighed værdi for hver af arbejdsprocessen.

**Eksempel 6**

    Type:Alert | measure min(Severity) by WorkflowName

*FORKLARING*

Samme som i forrige eksempel, men med *Min* aggregeret funktion.

**Eksempel 7**

    Type:Perf | measure avg(CounterValue) by Computer

*FORKLARING*

Grupperer performance ved Computer og beregner gennemsnittet (gennemsnit).

**Eksempel 8**

    Type:Perf | measure sum(CounterValue) by Computer

*FORKLARING*

Samme som det forrige eksempel, anvender men *Sum*.

**Eksempel 9**

    Type:Perf | measure stddev(CounterValue) by Computer

*FORKLARING*

Samme som det forrige eksempel, bruger men *STAFV*.

**Eksempel på 10**

    Type:Perf | measure percentile70(CounterValue) by Computer

*FORKLARING*

Samme som det forrige eksempel, bruger men *PERCENTILE70*.

**Eksempel 11**

    Type:Perf | measure pct70(CounterValue) by Computer

*FORKLARING*

Samme som det forrige eksempel, bruger men *PCT70*. Bemærk, at *PCT ##* kun et alias for funktionen *fraktil ##* .

**Eksempel 12**

    Type:Perf | measure avg(CounterValue) by Computer, CounterName

*FORKLARING*

Grupperer performance først efter Computer og derefter CounterName og beregner gennemsnittet (gennemsnit).

**Eksempel 13**

    Type:Alert | measure count() as Count by WorkflowName | sort Count desc | top 5

*FORKLARING*

Får de bedste fem arbejdsprocesser med det maksimale antal beskeder.

**Eksempel 14**

    * | måle countdistinct(Computer) efter Type

*FORKLARING*

Tæller antallet af entydige computere rapportering for hver Type.

**Eksempel på 15**

    * | måle countdistinct(Computer) Interval 1 time

*FORKLARING*

Tæller antallet af entydige computere rapportering for hver time.

**Eksempel 16**

```
Type:Perf CounterName=”% Processor Time” InstanceName=”_Total” | measure avg(CounterValue) by Computer Interval 1HOUR
```

*FORKLARING*

Grupperer % processortid ved Computer, og returnerer gennemsnittet for hver 1 time.

**Eksempel 17**

    Type:W3CIISLog | measure max(TimeTaken) by csMethod Interval 5MINUTES

*FORKLARING*

Grupperer W3CIISLog efter metode, og returnerer maksimalt for hver 5 minutter.

**Eksempel på 18**

```
Type:Perf CounterName=”% Processor Time” InstanceName=”_Total”  | measure min(CounterValue) as MIN, avg(CounterValue) as AVG, percentile75(CounterValue) as PCT75, max(CounterValue) as MAX by Computer Interval 1HOUR
```

*FORKLARING*

Grupperer % processortid ved computer, og returnerer den værdi for minimum, gennemsnit, 75 fraktil og maksimum for hver 1 time.

**Eksempel 19**

```
Type:Perf CounterName=”% Processor Time”  | measure min(CounterValue) as MIN, avg(CounterValue) as AVG, percentile75(CounterValue) as PCT75, max(CounterValue) as MAX by Computer, InstanceName Interval 1HOUR
```

*FORKLARING*

Grupperer % processortid først ved computer og derefter navnet på forekomsten, og returnerer den værdi for minimum, gennemsnit, 75 fraktil og maksimum for hver 1 time

**Eksempel 20**

```
Type= Perf CounterName="Disk Writes/sec" Computer="BaconDC01.BaconLand.com" | measure max(product(CounterValue,60)) as MaxDWPerMin by InstanceName Interval 1HOUR
```

*FORKLARING*

Beregner maksimalt antal Disk skriver i minuttet for hver disk på computeren

### <a name="where"></a>Hvor

Syntaks:

```
**where** AggregatedValue>20
```

Kan kun bruges efter en *måling* kommando til at filtrere de samlede resultater, der har produceret funktionen *måling* sammenlægning.

Eksempler:

    Type:Perf CounterName:"% Total Run Time" | Measure max(CounterValue) as MAXCPU by Computer

    Type:Perf CounterName:"% Total Run Time" | Measure max(CounterValue) by Computer | where (AggregatedValue>50 and AggregatedValue<90)

### <a name="in"></a>I

Syntaks:

```
(Outer Query) (Field to use with inner query results) IN {Inner query | measure count() by (Field to send to outer query)} (rest  of outer query)  
```

Beskrivelse: Denne syntaks kan du oprette en sammenlægning og kilde på listen over værdier fra sammenlægningen til en anden ydre (primær) søgning, der ser ud til begivenheder med disse værdi. Du kan gøre dette ved at omgive indre søgningen i klammeparenteser og fødning af dens resultater som mulige værdier for et felt i den ydre søgning ved hjælp af operatoren IN.

Indre forespørgsel eksempel: *computere i øjeblikket manglende sikkerhedsopdateringer* med følgende sammenlægning forespørgsel:

```
Type:Update Classification="Security Updates"  UpdateState=needed TimeGenerated>NOW-25HOURS | measure count() by Computer
```    

Den endelige forespørgsel, der finder *alle Windows hændelser for computere, der aktuelt manglende sikkerhedsopdateringer* ligner:

```
Type=Event Computer IN {Type:Update Classification="Security Updates"  UpdateState=needed TimeGenerated>NOW-25HOURS | measure count() by Computer}
```

### <a name="dedup"></a>Dedup

**Syntaksen for**

    Dedup FieldName

**Beskrivelse** Returnerer det første dokument, der er fundet for hver entydig værdi for det angivne felt.

**Eksempel**

    Type=Event | sort TimeGenerated DESC | Dedup EventID

Ovenstående eksempel returnerer én begivenhed (seneste da vi bruge DESC på TimeGenerated) per Begivenhedid


### <a name="extend"></a>Udvide

**Beskrivelse** Gør det muligt at oprette Runtime-felter i forespørgsler. Du kan også bruge måling kommandoen efter Forlæng, hvis du vil udføre sammenlægning.

**Eksempel 1**

    Type=SQLAssessmentRecommendation | Extend product(RecommendationScore, RecommendationWeight) AS RecommendationWeightedScore
Vise vægtet anbefaling point for SQL vurdering anbefalinger

**Eksempel 2**

    Type=Perf CounterName="Private Bytes" | EXTEND div(CounterValue,1024) AS KBs | Select CounterValue,Computer,KBs
Vis tællerværdi i KB'er i stedet for byte

**Eksempel 3**

    Type=WireData | EXTEND scale(TotalBytes,0,100) AS ScaledTotalBytes | Select ScaledTotalBytes,TotalBytes | SORT TotalBytes DESC
Skalere værdien af WireData TotalBytes, således at alle resultater ligger mellem 0 og 100.

**Eksempel 4**

```
Type=Perf CounterName="% Processor Time" | EXTEND if(map(CounterValue,0,50,0,1),"HIGH","LOW") as UTILIZATION
```
Mærke performance tællerværdier er mindre end 50% las lav og andre som høj

**Eksempel 5**

```
Type= Perf CounterName="Disk Writes/sec" Computer="BaconDC01.BaconLand.com" | Extend product(CounterValue,60) as DWPerMin| measure max(DWPerMin) by InstanceName Interval 1HOUR
```
Beregner maksimalt antal Disk skriver i minuttet for hver disk på computeren

**Understøttede funktioner**


| Funktionen | Beskrivelse | Syntakseksempler |
|---------|---------|---------|
| ABS | Returnerer den absolutte værdi af den angivne værdi eller funktion. | `abs(x)` <br> `abs(-5)` |
| ARCCOS | Returnerer bue cosinus af en værdi eller en funktion | `acos(x)` |
| og | Returnerer en værdi på true, hvis og kun, hvis alle dens operander evalueres til sand. | `and(not(exists(popularity)),exists(price))` |
| ARCSIN | Returnerer bue sinus af en værdi eller en funktion | `asin(x)` |
| ARCTAN | Returnerer arcus tangens til en værdi eller en funktion | `atan(x)` |
| ARCTAN2 |  Returnerer resultatet af konvertering af rektangulært koordinaterne vinklen x, y til polære koordinater | `atan2(x,y)` |
| cbrt | Kube rod |  `cbrt(x)` |
| ceil | Runder op til et heltal | `ceil(x)`  <br> `ceil(5.6)`-Returnerer 6 |
| COS | Returnerer cosinus til en vinkel | `cos(x)` |
| COSH | Returnerer den hyperbolske cosinus af en vinkel | `cosh(x)` |
| definition | definition er kort til standard. Returnerer værdien for "felt", eller hvis feltet ikke findes, returnerer den angivne standardværdi og giver den første værdi hvor: `exists()==true`. | `def(rating,5)`– Dette def()-funktionen returnerer bedømmelse, eller hvis nogen klassifikation, der er angivet i dokumentet, returnerer 5 <br> `def(myfield, 1.0)`-svarer til`if(exists(myfield),myfield,1.0)` |
| grader | Konverter radianer til grader |  `deg(x)` |
| division | `div(x,y)`Dividerer x af y. | `div(1,y)` <br> `div(sum(x,100),max(y,1))` |
| fordeling | Returnerer afstanden mellem to vektorer, (punkt) i en n-flerdimensionale område. I en potens plus to eller flere forekomster af ValueSource og beregner afstanden mellem to mulighederne. Hver ValueSource skal være et tal. Der skal være et lige antal ValueSource forekomster overførte og metoden antages det, at den første halvdel repræsenterer den første vektor og anden halvdel repræsenterer den anden vektor.  | `dist(2, x, y, 0, 0)`– Beregner Euclidean afstand between,(0,0) og (x, y) for hvert dokument. <br> `dist(1, x, y, 0, 0)`– Beregner Manhattans (taxicab), afstanden mellem (0,0) og (x, y) for hvert dokument. <br> `dist(2,,x,y,z,0,0,0)`-Euclidean afstanden mellem (0,0,0) og (x, y, z) for hvert dokument.<br>`dist(1,x,y,z,e,f,g)`-Manhattans afstanden mellem (x, y, z) og (e, f, g), hvor hvert bogstav er et feltnavn. |
| der findes | Returnerer SAND, hvis et medlem af feltet findes. | `exists(author)`-Returnerer SAND for en hvilken som helst dokument har en værdi i feltet "Forfatter".<br>`exists(query(price:5.00))`-Returnerer SAND, hvis det svarer til "pris", "5,00". |
| EXP | Returnerer Eulers tal opløftet i potens x | `exp(x)` |
| Floor | Runder ned til et heltal | `floor(x)`  <br> `floor(5.6)`-Returnerer 5 |
| hypo | Returnerer sqrt(sum(pow(x,2),pow(y,2))) uden mellemliggende overløb eller underløb | `hypo(x,y)`  <br> ` |
| Hvis | Aktiverer betinget funktionen forespørgsler. I `if(test,value1,value2)` -Test er eller refererer til en logisk værdi eller et udtryk, der returnerer en logisk værdi (SAND eller FALSK).  `value1`er den værdi, der returneres af funktionen, hvis test er sand. `value2`er den værdi, der returneres af funktionen, hvis test giver falsk. Et udtryk, der kan være en funktion som producerer booleske værdier, eller endda funktioner, der returnerer numeriske værdier, hvor case-værdien 0 blive fortolket som FALSK eller strenge, hvorefter tom streng fortolkes som FALSK. | `if(termfreq(cat,'electronics'),popularity,42)`– Denne funktion kontrollerer hvert dokument til den at se, om den indeholder ordet "electronics" i feltet kat. Hvis det er tilfældet, returneres derefter returneres værdien i feltet popularitets, ellers værdien af 42. |
| Lineær | Implementerer `m*x+c` hvor m og c er konstanter og x er en vilkårlig funktion. Dette er svarer til `sum(product(m,x),c)`, men er lidt mere effektiv, som det er implementeret som en enkelt funktion. | `linear(x,m,c) linear(x,2,4)`Returnerer`2*x+4` |
| ln| Returnerer den naturlige logfil over den angivne funktion |  `ln(x)` |
| Log | Returnerer loggen grundtal 10 af den angivne funktion. | `log(x)   log(sum(x,100))` |
| kort | Knytter en hvilken som helst værdier en input x-funktion, der falder inden for min og max inklusive den angivne destination. Argumenter min og max skal være konstanter. Argumenter mål og standard kan være konstanter eller funktioner. Hvis værdien af x ikke falder mellem min og max, derefter enten returneres værdien af x eller en standardværdi returneres, hvis angivet som et 5th argument. |  `map(x,min,max,target) map(x,0,0,1)`-Ændres alle værdierne 0 til 1. Det kan være nyttige i håndtering af standardværdier 0.<br> `map(x,min,max,target,default)    map(x,0,100,1,-1)`-Ændres værdier mellem 0 og 100 til 1, og alle andre værdier til -1.<br>  `map(x,0,100,sum(x,599),docfreq(text,solr))`-Ændres værdier mellem 0 og 100 x + 599 og alle andre værdier til hyppigheden for ordet 'solr' i Felttekst. |
| Maks. | Returnerer den største numeriske værdi af flere indlejrede funktioner eller konstanter, der er angivet som argumenter: `max(x,y,...)`. Funktionen Maks kan også være nyttige til "bottoming" en anden funktion eller angivet felt på nogle konstant.  Brug den `field(myfield,max)` syntaks for at vælge den maksimale værdi af et enkelt felt med flere værdier.  | `max(myfield,myotherfield,0)` |
| min | Returnerer den mindste numeriske værdi af flere indlejrede funktioner af konstanter, der er angivet som argumenter: `min(x,y,...)`. Funktionen min kan også være nyttige til at give en "øvre grænse" på en funktion, ved hjælp af en konstant. Brug den `field(myfield,min)` syntaks for at vælge den mindste værdi af et enkelt felt med flere værdier. | `min(myfield,myotherfield,0)` |
| Rest | Beregner modulus af funktionen x med funktionen y. |`mod(1,x)` <br> `mod(sum(x,100), max(y,1))`   |
| MS | Returnerer millisekunder af forskellen mellem argumenterne. Datoer er i forhold til Unix eller POSIX tid epoke, midnat, januar 1 1970 UTC. Argumenter kan være navnet på et indekserede TrieDateField eller en dato matematisk baseret på en konstant dato eller nu. `ms()`svarer til `ms(NOW)`, antal millisekunder siden epoke. `ms(a)`Returnerer antallet millisekunder siden epoke, der repræsenterer argumentet. `ms(a,b)`Returnerer antallet millisekunder, b ligger før en, som er `a - b`.| `ms(NOW/DAY)`<br>`ms(2000-01-01T00:00:00Z)`<br>`ms(mydatefield)`<br>`ms(NOW,mydatefield)`<br>`ms(mydatefield,2000-01-01T00:00:00Z)`<br>`ms(datefield1,datefield2)` |
| ikke | Logisk negeret værdien af den ombrudte funktion. | `not(exists(author))`-GÆLDER kun, når `exists(author)` er falsk. |
| eller | En logisk adskillelse. | `or(value1,value2)`-SAND, hvis værdi1 eller værdi2 er sand. |
| Pow | Hæver den angivne base til den angivne potens. `pow(x,y)`hæver x til en potens af y. |  `pow(x,y)`<br>`pow(x,log(y))`<br>`pow(x,0.5)`-Den samme som KVROD. |
| produkt | Returnerer produktet af flere værdier eller funktioner, som er angivet i en kommasepareret liste. `mul(...)`kan også bruges som et alias for denne funktion. | `product(x,y,...)`<br>`product(x,2)`<br>`product(x,y)`<br>`mul(x,y)` |
| recip | Udfører en reciprokke funktion med `recip(x,m,a,b)` implementere `a/(m*x+b)` hvor m, a, b er konstanter, og x er en hvilken som helst vilkårligt kompleks funktion. Når en og b er lig med, og x > = 0, denne funktion ikke har en maksimumværdi af 1, der udelader som x øges. Øger værdien af en og b sammen resultater i en bevægelse af funktionen hel til en fladere del af kurven. Disse egenskaber kan gøre den til en ideel funktionen for øger mere seneste dokumenter, når x er `rord(datefield)`. | `recip(myfield,m,a,b)`<br>`recip(rord(creationDate),1,1000,1000)` |
| RAD | Konvertere grader til radianer | `rad(x)` |
| rint| Runder op til nærmeste heltal | `rint(x)`  <br> `rint(5.6)`-Returnerer 6 |
| sin | Returnerer sinus af en vinkel | `sin(x)` |
| SINH | Returnerer den hyperbolske sinus af en vinkel | `sinh(x)` |
| skala | Ubalance værdier af funktionen x, så de falder mellem de angivne minTarget og maxTarget begge inklusive. Den aktuelle implementering kører på tværs alle værdierne funktionen til at hente min og max, så den kan vælge den korrekte skala. Den aktuelle implementering kan skelne, når dokumenter er blevet slettet eller dokumenter, der ingen værdi. Det bruger 0.0 værdierne i disse tilfælde. Det betyder, at hvis værdier er normalt alle større end 0,0, en kan stadig ender med 0,0 som Minimumværdien skal tilknyttes fra. I disse tilfælde et passende `map()` funktion kan bruges som en løsning til at ændre 0,0 til en værdi i området reelt tal, som vist her:`scale(map(x,0,0,5),1,2)` | `scale(x,minTarget,maxTarget)`<br>`scale(x,1,2)`-Skaleres værdierne for x, så alle værdier skal være mellem 1 og 2 begge inklusive. |
| KVROD | Returnerer kvadratroden af den angivne værdi eller funktion. | `sqrt(x)`<br>`sqrt(100)`<br>`sqrt(sum(x,100))` |
| strdist | Beregne afstanden mellem to strenge. Anvender Lucene stavekontrol Designkontrol StringDistance brugergrænsefladen og understøtter alle de tilgængelige i pågældende pakke implementering af samt tillader Tilslut deres egne via Solrs ressource indlæsning funktioner-programmer. strdist tager `(string1, string2, distance measure)`. Mulige værdier for afstand mål er: <br>jw: Jaro Winkler<br>redigere: Levenstein eller rediger afstand<br>ngram: feltet NGramDistance, hvis angivet, kan du kan også sende i størrelse, ngram for. Standard er 2.<br>FQN: Fuldt kvalificeret klasse navn til en implementering af grænsefladen StringDistance. Skal have en ingen arg parametre.|`strdist("SOLR",id,edit)` |
| Sub | Returnerer x-y fra `sub(x,y)`. | `sub(myfield,myfield2)`<br>`sub(100,sqrt(myfield))` |
| Sum | Returnerer summen af flere værdier eller funktioner, som er angivet i en kommasepareret liste. `add(...)`kan bruges som et alias for denne funktion. | `sum(x,y,...)`<br>`sum(x,1)`<br>`sum(x,y)`<br>`sum(sqrt(x),log(y),z,0.5)`<br>`add(x,y)`|
|termfreq | Returnerer antallet gange ordet vises i feltet for dokumentet. | termfreq(Text,'memory')|
| TAN | Returnerer tangens til en vinkel | `tan(x)` |
| TANH | Returnerer hyperbolske tangens til en vinkel | `tanh(x)` |



## <a name="search-field-and-facet-reference"></a>Reference til søgning felt og facet

Når du bruger Log Søg til at finde data, vises resultater forskellige felt og facetter. Dog nogle af de oplysninger, du ser muligvis ikke særligt beskrivende. Du kan bruge følgende oplysninger kan hjælpe dig med at forstå resultaterne.



|Felt|Søgeområdet|Beskrivelse|
|---|---|---|
|TenantId|Alle|Bruges til at partition data|
|TimeGenerated|Alle|Bruges til at drive tidslinjen, timeselectors (i Søg og i andre skærme). Den repræsenterer, når slags data blev oprettet (typisk på agent). Tid, der er angivet i ISO-formatet og er altid UTC. Hvis det er 'typer', der er baseret på eksisterende instrumentation (det vil sige begivenheder i en logfil) er dette typisk er i realtid, log posten/linje/post er logget på; for nogle af de andre typer, der er oprettet via management packs eller i skyen – er det vil sige anbefalinger/beskeder/updateagent/osv, dette den tid, når denne nye slags data med et øjebliksbillede af en konfiguration af nogle Sortér er indsamlet eller en anbefaling/påmindelse blev oprettet baseret på den.|
|Begivenhedid|Begivenhed|Begivenhedid i Windows-hændelseslog|
|Hændelseslog|Begivenhed|Hændelseslog, hvor hændelsen blev logført af Windows|
|EventLevelName|Begivenhed|Kritiske / advarsel / oplysninger / succes|
|EventLevel|Begivenhed|Numeriske værdi for kritiske / advarsel / oplysninger / succes (Brug EventLevelName i stedet for nemmere/lettere at læse forespørgsler)|
|SourceSystem|Alle|Hvor data kommer fra (med hensyn til 'vedhæfte' tilstand til tjenesten – det vil sige Operations Manager, OMS (= data, der er oprettet i skyen), Azure-lager (data kommer fra WAD) og osv.|
|Objektnavn|PerfHourly|Windows ydeevne objektnavn|
|Navn på forekomst|PerfHourly|Windows ydeevne tæller forekomstnavn|
|CounteName|PerfHourly|Windows ydeevne tællernavn|
|ObjectDisplayName|PerfHourly, ConfigurationAlert, ConfigurationObject, ConfigurationObjectProperty|Få vist navnet på det objekt, der er rettet ved en regel for ydeevnen af websteder i Operations Manager, eller som for objektet har opdaget ved funktionsdygtige indsigt eller mod meddelelsen blev genereret|
|RootObjectName|PerfHourly, ConfigurationAlert, ConfigurationObject, ConfigurationObjectProperty|Vist navn for overordnet for overordnet (i en dobbelt hosting relation: det vil sige SqlDatabase, der er hostet af SqlInstance, der er hostet af Windows-Computer) for objektet rettet ved en regel for ydeevnen af websteder i Operations Manager, eller som for objektet har opdaget ved funktionsdygtige indsigt eller mod meddelelsen blev genereret|
|Computer|De fleste kontotyper|Computernavn, som dataene tilhører|
|DeviceName|ProtectionStatus|Computernavn dataene tilhører (samme som 'Computer')|
|DetectionId|ProtectionStatus| |
|ThreatStatusRank|ProtectionStatus|Truslen status rang er en numerisk repræsentation af truslen status og svarer til http-Svarkoder, har vi venstre mellemrum mellem tal (hvilket er årsagen nogen trusler er 150 og ikke 100 eller 0), så vi har plads til at tilføje nye stater. Når vi gør en aggregeret værdi for truslen status og beskyttelsesstatus for, ønsker vi at vise den værste tilstand, computeren har været i den valgte periode. Vi bruger tallene skal rangordnes de forskellige tilstande, så vi kan finde posten med det højeste tal.|
|ThreatStatus|ProtectionStatus|Beskrivelse af ThreatStatus, kort 1:1 med ThreatStatusRank|
|TypeofProtection|ProtectionStatus|Antimalware-produkt, der er registreret i computeren: ingen, værktøjet Microsoft Malware, Forefront og osv.|
|ScanDate|ProtectionStatus| |
|SourceHealthServiceId|ProtectionStatus, RequiredUpdate|HealthService ID for denne computer agent|
|HealthServiceId|De fleste kontotyper|HealthService ID for denne computer agent|
|ManagementGroupName|De fleste kontotyper|Administration af gruppens navn til Operations Manager-vedhæftet supportmedarbejdere Ellers vil det være null/tom|
|Objekttype|ConfigurationObject|Type (som Operations Manager management pack 'type' / klasse) for dette objekt, der er registreret af Log Analytics konfiguration vurdering|
|UpdateTitle|RequiredUpdate|Navnet på den opdatering, der blev fundet ikke er installeret|
|PublishDate|RequiredUpdate|Hvornår blev opdateringen udgives på Microsoft update?|
|Server|RequiredUpdate|Computernavn dataene tilhører (samme som 'Computer')|
|Produkt|RequiredUpdate|Produkt, som opdateringen gælder for|
|UpdateClassification|RequiredUpdate|Opdateringstype (opdateringspakke, servicepakke osv.)|
|KBID|RequiredUpdate|KB artikel-ID, der beskriver denne bedste praksis eller opdatering|
|WorkflowName|ConfigurationAlert|Navnet på den regel eller skærm, som produceret påmindelsen|
|Alvor|ConfigurationAlert|Alvorlighed af påmindelsen|
|Prioritet|ConfigurationAlert|Prioriteten af påmindelsen|
|IsMonitorAlert|ConfigurationAlert|Genereres denne besked med en skærm (SAND) eller en regel (FALSK)?|
|AlertParameters|ConfigurationAlert|XML med parametrene for Log Analytics-besked|
|Kontekst|ConfigurationAlert|XML med 'kontekst' på Log Analytics-beskeden|
|Arbejdsbelastning|ConfigurationAlert|Teknologi eller 'arbejdsbelastningen', som den vigtige besked refererer til|
|AdvisorWorkload|Anbefaling|Teknologi eller 'arbejdsbelastningen', som anbefalingen refererer til|
|Beskrivelse|ConfigurationAlert|Beskrivelse af besked (kort)|
|DaysSinceLastUpdate|UpdateAgent|Hvor mange dage siden (i forhold til 'TimeGenerated' af denne post) denne agent installere en opdatering fra WSUS/Microsoft Update?|
|DaysSinceLastUpdateBucket|UpdateAgent|Baseret på DaysSinceLastUpdate, installeret en kategorisering i 'tid filsæt' af er en computer, hvor længe siden sidste alle opdateringer fra WSUS/Microsoft Update|
|AutomaticUpdateEnabled|UpdateAgent|Automatisk opdatering kontrollere aktiveres eller deaktiveres på denne agent?
|AutomaticUpdateValue|UpdateAgent|Kontrollerer automatiske opdateringer er angivet til automatisk at hente og installere, kun hente eller kun tjekke?|
|WindowsUpdateAgentVersion|UpdateAgent|Versionsnummeret for Microsoft Update-agent|
|WSUSServer|UpdateAgent|Hvilke WSUS-serveren er denne update-agent målretning?|
|OSVersion|UpdateAgent|Version af operativsystemet denne update-agent der kører på|
|Navn|Anbefaling, ConfigurationObjectProperty|/ Titel på anbefaling, eller navnet på egenskaben fra Log Analytics konfiguration vurdering|
|Værdi|ConfigurationObjectProperty|Værdien af en egenskab fra Log Analytics konfiguration vurdering|
|KBLink|Anbefaling|URL-adressen til KB-artiklen, der beskriver denne bedste praksis eller opdatering|
|RecommendationStatus|Anbefaling|Anbefalinger er blandt de nogle typer, hvis poster 'opdateret', ikke kun føjes til søgeindekset. Denne status ændres om anbefalingen er aktiv eller Åbn, eller hvis Log Analytics registrerer, at den er blevet løst.|
|RenderedDescription|Begivenhed|Gengivne beskrivelse (genbrugte tekst med på forhånd parametre) af en Windows-begivenhed|
|ParameterXml|Begivenhed|XML sammen med parametrene i afsnittet 'data' i en Windows-begivenhed (som det fremgår Logbog)|
|EventData|Begivenhed|XML med afsnittet hele 'data' i en Windows-begivenhed (som det fremgår Logbog)|
|Kilde|Begivenhed|Hændelseslog kilde, der oprettede hændelsen|
|EventCategory|Begivenhed|Kategori af begivenheden, direkte fra Windows-hændelseslog|
|Brugernavn|Begivenhed|Brugernavnet for hændelsen Windows (typisk NT AUTHORITY\LOCALSYSTEM)|
|SampleValue|PerfHourly|MIDDEL gennemsnitsværdien for hver time sammenlægning af en ydeevne tæller|
|Min|PerfHourly|Mindste værdi i en ydeevne tæller hver time aggregat hver time interval|
|Maks|PerfHourly|Største værdi i en ydeevne tæller hver time aggregat hver time interval|
|Percentile95|PerfHourly|95th fraktil værdien for det interval af timer for en ydeevne tæller hver time aggregat|
|SampleCount|PerfHourly|Hvor mange 'raw' ydeevne tæller eksempler blev brugt til at producere denne hver time Sammenlæg post|
|Truslen|ProtectionStatus|Navnet på malware, der blev fundet|
|StorageAccount|W3CIISLog|Azure-lager konto loggen blev læst fra|
|AzureDeploymentID|W3CIISLog|Azure-installation-ID'ET for skytjenesten loggen tilhører|
|Rolle|W3CIISLog|Rolle Skytjenesten Azure loggen tilhører|
|RoleInstance|W3CIISLog|RoleInstance af Azure rollen som loggen tilhører|
|sSiteName|W3CIISLog|IIS-websted, som loggen tilhører (metabasen notation) s-sitename felt i den oprindelige log|
|sComputerName|W3CIISLog|Feltet s-computernavn i den oprindelige log|
|sIP|W3CIISLog|Server IP-adresse på HTTP-anmodningen blev adresseret til. S-IP-felt i den oprindelige log|
|csMethod|W3CIISLog|HTTP-metode (Hent/INDLÆG/osv) bruges af klienten i HTTP-anmodningen. Cs-metoden i den oprindelige log|
|cIP|W3CIISLog|Klient IP-adresse på HTTP anmodning stammer fra. C-IP-felt i den oprindelige log|
|csUserAgent|W3CIISLog|HTTP-brugeragent defineret klienten (browser eller på anden måde). Cs-brugeragenten i den oprindelige log|
|scStatus|W3CIISLog|HTTP (200/403/500/osv) returnerede statuskode af serveren til klienten. Cs-status i den oprindelige log|
|TimeTaken|W3CIISLog|Hvor lang tid (i millisekunder), som anmodningen oprindeligt brugte for at fuldføre. Feltet timetaken i den oprindelige log|
|csUriStem|W3CIISLog|Relativ Uri (uden at hoste adresse, det vil sige ' / søge '), der er blevet anmodet om. Feltet cs uristem i den oprindelige log|
|csUriQuery|W3CIISLog|URI-forespørgsel. URI forespørgsler er kun nødvendige for dynamisk sider, som ASP-sider, så feltet indeholder som regel en bindestreg for statiske sider.|
|sportsgren|W3CIISLog|Serverport, HTTP-anmodningen blev sendt til (og IIS lytter til, da det valgte)|
|csUserName|W3CIISLog|Godkendte brugernavn, hvis anmodningen er godkendt og ikke anonymt|
|csVersion|W3CIISLog|HTTP-protokollen version bruges i anmodningen (det vil sige ' HTTP / 1.1')|
|csCookie|W3CIISLog|Cookieoplysninger|
|csReferer|W3CIISLog|Websted, som brugeren senest besøgte. Dette websted angivet et link til det aktuelle websted.|
|csHost|W3CIISLog|Host sidehoved (det vil sige ' www.mysite.com'), der blev anmodet|
|scSubStatus|W3CIISLog|Understatus fejlkode|
|scWin32Status|W3CIISLog|Windows statuskode|
|csBytes|W3CIISLog|Byte sendt i anmodningen fra klienten til serveren|
|scBytes|W3CIISLog|Byte, der returneres tilbage i svar fra serveren til klienten|
|ConfigChangeType|ConfigurationChange|Type ændring (WindowsServices / Software / osv)|
|ChangeCategory|ConfigurationChange|Kategori af ændringen (ændret / tilføjet / fjernet)|
|SoftwareType|ConfigurationChange|Type software (Opdater / program)|
|SoftwareName|ConfigurationChange|Navnet på softwaren (gælder kun for softwareændringer)|
|Publisher|ConfigurationChange|Leverandør, der publiceres softwaren (gælder kun for softwareændringer)|
|SvcChangeType|ConfigurationChange|Type ændring, der blev anvendt på en Windows-tjeneste (stat / StartupType / sti / kontonavnet) – gælder kun for Windows-tjenesteændringer|
|SvcDisplayName|ConfigurationChange|Få vist navnet på den tjeneste, der er blevet ændret|
|SvcName|ConfigurationChange|Navnet på den tjeneste, der er blevet ændret|
|SvcState|ConfigurationChange|Ny (aktuelle) status for tjenesten|
|SvcPreviousState|ConfigurationChange|Forrige kendte (kun relevant, hvis tjenestetilstand ændret) tjenestens tilstand|
|SvcStartupType|ConfigurationChange|Tjenesten Starttype|
|SvcPreviousStartupType|ConfigurationChange|Forrige service Starttype (kun relevant, hvis tjenesten Starttype ændret)|
|SvcAccount|ConfigurationChange|Tjenestekonto|
|SvcPreviousAccount|ConfigurationChange|Forrige tjenestekonto (kun relevant, hvis servicekonto ændret)|
|SvcPath|ConfigurationChange|Stien til den eksekverbare fil til Windows-tjenesten|
|SvcPreviousPath|ConfigurationChange|Forrige stien til den eksekverbare fil til Windows-tjenesten (kun relevant, hvis den ændret)|
|SvcDescription|ConfigurationChange|Beskrivelse af tjenesten|
|Forrige|ConfigurationChange|Forrige tilstanden for denne software (installerede / ikke installeret / forrige version)|
|Aktuelle|ConfigurationChange|Seneste tilstanden for denne software (installerede / ikke installeret / aktuelle version)|

## <a name="next-steps"></a>Næste trin
Yderligere oplysninger om log søgninger:

- Bliv fortrolig med [log søgninger](log-analytics-log-searches.md) til at få vist detaljerede oplysninger, der indsamles af løsninger.
- Bruge [brugerdefinerede felter i Log Analytics](log-analytics-custom-fields.md) til at udvide log søgninger.
