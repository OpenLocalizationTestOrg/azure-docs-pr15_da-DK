<properties
    pageTitle="Log søgninger i Log Analytics | Microsoft Azure"
    description="Log søgninger gør det muligt at kombinere og koordinere maskine data fra flere kilder i dit miljø."
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
    ms.date="10/10/2016"
    ms.author="banders"/>

# <a name="log-searches-in-log-analytics"></a>Log søgninger i Log Analytics

Centrale Log Analytics er søgefunktionen log som gør det muligt at kombinere og koordinere maskine data fra flere kilder i dit miljø. Løsninger, der er også drevet af log Søg til at give dig målepunkter drejes rundt om et bestemt problemområde.

Du kan oprette en forespørgsel på siden Søg, og derefter når du søger, kan du filtrere resultaterne ved hjælp af salgsstrategi kontrolelementer. Du kan også oprette avancerede forespørgsler til Transformer, filtrere og rapporten på dine resultater.

Almindelige log søgeforespørgsler vises på de fleste løsning sider. Du kan klikke felter eller analysere i til andre elementer for at få vist oplysninger om elementet ved hjælp af log søgning i hele konsollen OMS.

I dette selvstudium gennemgår vi for eksempler dækker alle de grundlæggende elementer, når du bruger log søgning.

Vi starter med enkel, praktiske eksempler og derefter bygge på dem, så du kan finde en forståelse praktiske use cases om, hvordan du bruger syntaksen til at udtrække de viden, du vil fra dataene.

Når du har kendskab til søgning teknikker, kan du gennemgå de [Log Analytics logger Søg reference](log-analytics-search-reference.md).

## <a name="use-basic-filters"></a>Bruge grundlæggende filtre

Det første, du vil vide er, at den første del af en søgning forespørgsel før en "|" lodret tegnet, altid er et *filter*. Du kan tænke på det som en WHERE-delsætning i TSQL – det bestemmer, *hvilke* undersæt af data for at adskille af OMS datalager. Søge i lageret med data er stort set om at angive egenskaber for de data, du vil udtrække, så det er neutralt, vil starte en forespørgsel med WHERE-delsætningen.

De mest grundlæggende filtre kan du bruge er *nøgleord*, som 'error' eller 'timeout' eller navnet på en computer. Disse typer enkle forespørgsler returnerer generelt forskelligartet figurer af data i det samme resultatsæt. Dette skyldes, at Log Analytics har forskellige *typer* i systemet.


### <a name="to-conduct-a-simple-search"></a>Sådan udfører du en enkel søgning
1. Klik på **Log Søg**i portalen OMS.  
    ![feltet Søg](./media/log-analytics-log-searches/oms-overview-log-search.png)
2. Skriv i feltet forespørgsel `error` og derefter klikke på **Søg**.  
    ![Search-fejl](./media/log-analytics-log-searches/oms-search-error.png)  
    For eksempel forespørgslen for `error` returneres 100.000 **begivenhed** poster (der indsamles via Log Management), 18 **ConfigurationAlert** poster (genereres af konfiguration vurdering) og 12 **ConfigurationChange** poster (fået, ved den ændringsregistrering) på følgende billede.   
    ![søgeresultater](./media/log-analytics-log-searches/oms-search-results01.png)  

Disse filtre er ikke virkelig objekt typer/klasser. *Type* er kun et mærke eller en egenskab, eller en streng/navn/kategori, der er knyttet til en del af data. Nogle dokumenter i systemet er kodet som **Type: ConfigurationAlert** og nogle er mærket som **Type: performance**eller **Type: begivenhed**, og så videre. Hver søgeresultat, dokument, post eller post viser alle de rå egenskaber og deres værdier for hver af disse filer, og du kan bruge disse feltnavne skal angives i filteret, når du vil hente kun posterne, hvor feltet har, givet værdi.

*Type* er blot et felt, der har alle de poster, er ikke forskellig fra et andet felt. Dette blev oprettet ud fra værdien i feltet Type. Denne post har en anden figur eller formular. Tilfældigvis, **Type = performance**, eller **Type = begivenhed** er også den syntaks, som du har brug for at lære at forespørge efter ydelsesdata eller begivenheder.

Du kan bruge enten et kolon (:) eller et lighedstegn (=) efter feltnavnet og før værdien. **Hændelsestype:** og **Type = begivenhed** er tilsvarende i betydning, kan du vælge den typografi, du foretrækker.

Så, hvis typen = performance poster har et felt med navnet 'CounterName', og derefter kan du skrive en forespørgsel, der ligner `Type=Perf CounterName="% Processor Time"`.

Dette giver dig kun ydelsesdata hvor ydeevne tællernavnet er "% Processortid".

### <a name="to-search-for-processor-time-performance-data"></a>Søge efter processor tidsdata ydeevne
- Skriv i feltet Søg forespørgsel`Type=Perf CounterName="% Processor Time"`

Du kan også være mere specifikke og bruge **navn på forekomst = _ 'Total'** i forespørgslen, som er en Windows ydeevne tæller. Du kan også vælge en facet og et andet **felt: værdi**. Filteret føjes automatisk til filteret på forespørgslen linjen. Du kan se det på følgende billede. Det viser, hvor du skal klikke for at tilføje **navn på forekomst: '_Total'** til forespørgslen uden at skrive noget.

![Søg facet](./media/log-analytics-log-searches/oms-search-facet.png)

Din forespørgsel bliver nu`Type=Perf CounterName="% Processor Time" InstanceName="_Total"`

I dette eksempel skal du ikke behøver at angive **Type = performance** at få adgang til dette resultat. Fordi felterne CounterName og navn på forekomst findes kun for poster af typen = performance, forespørgslen, der er specifikke nok til at returnere det samme resultat som den længere, forrige, som:
```
CounterName="% Processor Time" InstanceName="_Total"
```

Dette skyldes, at alle filtre i forespørgslen evalueres som værende på *og* med hinanden. Effektivt, jo flere felter, du har føjet til kriterierne, du får mindre, mere specifikke og elegant resultater.

For eksempel forespørgslen `Type=Event EventLog="Windows PowerShell"` er identisk med `Type=Event AND EventLog="Windows PowerShell"`. Returnerer den alle hændelser, der er logget ind, og der indsamles fra hændelseslog Windows PowerShell. Hvis du tilføjer et filter flere gange ved gentagne gange at vælge den samme grene, så er problemet rent kosmetiske – det kan rod søgepanelet, men det stadig returnerer det samme resultat, fordi implicit og-operatoren er der altid.

Du kan nemt annullere operatoren implicit AND ved hjælp af en operator ikke eksplicit. Eksempel:

`Type:Event NOT(EventLog:"Windows PowerShell")`eller tilsvarende `Type=Event EventLog!="Windows PowerShell"` returnere alle begivenheder fra alle andre logfiler, der ikke er Windows PowerShell-loggen.

Eller du kan bruge andre boolesk operator såsom 'Eller'. Følgende forespørgsel returnerer de poster, hvor hændelsesloggen er et program eller System.

```
EventLog=Application OR EventLog=System
```

Ved hjælp af den ovenstående forespørgsel, får du vist poster for begge loggene i samme resultatsættet.

Men hvis du fjerner feltet eller ved at lade den implicit og på plads, derefter følgende forespørgsel ikke medfører nogen resultater fordi der ikke er en hændelseslogpost, der hører til begge logge. Hvert hændelseslog element blev skrevet til kun én af de to logfiler.

```
EventLog=Application EventLog=System
```


## <a name="use-additional-filters"></a>Brug flere filtre

Følgende forespørgsel returnerer poster for 2 hændelseslogfiler for alle de computere, der har sendt data.

```
EventLog=Application OR EventLog=System
```

![søgeresultater](./media/log-analytics-log-searches/oms-search-results03.png)

Vælge en af de felter eller filtre, indsnævres forespørgslen til en bestemt computer, med undtagelse af alle andre dem. Forespørgslen vil minde om følgende.

```
EventLog=Application OR EventLog=System Computer=SERVER1.contoso.com
```

Hvor er svarer til følgende på grund af implicitte AND.

```
EventLog=Application OR EventLog=System AND Computer=SERVER1.contoso.com
```

Hver forespørgsel evalueres i følgende eksplicitte rækkefølge. Bemærk i parentesen.

```
(EventLog=Application OR EventLog=System) AND Computer=SERVER1.contoso.com
```

Du kan hente data kun for et bestemt computere ligesom feltet hændelseslog ved at tilføje eller. Eksempel:

```
(EventLog=Application OR EventLog=System) AND (Computer=SERVER1.contoso.com OR Computer=SERVER2.contoso.com OR Computer=SERVER3.contoso.com)
```

På samme måde, dette følgende forespørgsel returnerer **% CPU-tid** for de valgte to computere kun.

```
CounterName="% Processor Time"  AND InstanceName="_Total" AND (Computer=SERVER1.contoso.com OR Computer=SERVER2.contoso.com)
```


### <a name="boolean-operators"></a>Booleske operatorer
Med datetime og numeriske felter, kan du søge efter værdier ved hjælp af *større end*, *mindre end*, og *mindre end eller lig med*. Du kan bruge simple operatorer såsom >, <>, =, < =,! = i søgepanelet forespørgsel.


Du kan forespørge en bestemt hændelseslog efter et bestemt tidsrum. Eksempelvis udtrykkes de seneste 24 timer med følgende instruktion udtryk.

```
EventLog=System TimeGenerated>NOW-24HOURS
```


#### <a name="to-search-using-a-boolean-operator"></a>Søge ved hjælp af en boolesk operator
- Skriv i feltet Søg forespørgsel`EventLog=System TimeGenerated>NOW-24HOURS"`  
    ![søge med boolesk](./media/log-analytics-log-searches/oms-search-boolean.png)

Selvom du kan styre tidsintervallet grafisk, og i de fleste tilfælde du måske gøre det, er der fordele ved at herunder filteret tid direkte ind i forespørgslen. For eksempel det en god med dashboards, hvor du kan tilsidesætte tid for hvert felt, uanset valgknappen *globale* tid på dashboardsiden. Du kan finde yderligere oplysninger finder [Tid anliggende i Dashboard](http://cloudadministrator.wordpress.com/2014/10/19/system-center-advisor-restarted-time-matters-in-dashboard-part-6/).

Når filtrering af tid, huske på, at du får resultater i *skæringspunktet* for to klokkeslætsperioder: den, der er angivet i portalen OMS (S1) og den, der er angivet i forespørgslen (S2).

![skæringspunkt](./media/log-analytics-log-searches/oms-search-intersection.png)

Det betyder, hvis tidsperioderne ikke krydser hinanden, for eksempel i portalen OMS, hvor du vælger **denne uge** og i den forespørgsel, hvor du definerer **sidste uge**, der er ingen skæringspunkt og du modtager ikke nogen resultater.

Sammenligningsoperatorer, der bruges til feltet TimeGenerated er også nyttig i andre situationer. For eksempel med numeriske felter.

Givet f.eks, at konfigurationen vurdering beskeder har følgende alvorlighed værdier:

- 0 = oplysninger
- 1 = advarsel
- 2 = kritisk

Du kan forespørge efter både advarsel og vigtige beskeder og også udelade informativ dem med følgende forespørgsel:

```
Type=ConfigurationAlert  Severity>=1
```


Du kan også bruge område forespørgsler. Det betyder, at du kan angive området starten og slutningen af værdier i en sekvens. Eksempelvis hvis du vil begivenheder fra hændelseslog Operations Manager, hvor Begivenhedid er større end eller lig med 2100, men ikke større end 2199, returnerer derefter følgende forespørgsel dem.

```
Type=Event EventLog="Operations Manager" EventID:[2100..2199]
```


>[AZURE.NOTE] Syntaksen område, skal du bruge er kolon (:) feltværdien: separatoren og *ikke* lighedstegnet (=). Omslutte nedre og øvre slutningen af området i kantede parenteser, og Adskil dem med to punktum (.).

## <a name="manipulate-search-results"></a>Manipulere søgeresultater

Når du søger efter data, skal du vil tilpasse din søgeforespørgsel og har en god kontrolniveau over resultaterne. Når resultater er hentet, kan du anvende kommandoer til at transformere dem.

Kommandoer i Log Analytics søgninger *skal* følge efter lodret tegnet (|). Et filter skal altid være den første del af en forespørgselsstreng. Der definerer det datasæt, du arbejder med, og derefter "pipes" resultaterne i en kommando. Derefter kan du bruge pipen til at føje flere kommandoer. Dette er løst svarer til Windows PowerShell-pipeline.

Generelt forsøger Log Analytics Søg sprog at følge PowerShell typografi og retningslinjer for at gøre det svarer til IT-medarbejdere og for at lette learning kurven.

Kommandoer har navne med verber, så du nemt kan se, hvad de udfører.  

### <a name="sort"></a>Sortér

Kommandoen Sortér kan du angive sorteringsrækkefølgen efter en eller flere felter. Selvom du ikke bruger den, som standard, gennemtvinges gangen faldende rækkefølge. De seneste resultater er altid øverst i søgeresultaterne. Det betyder, at når du kører en søgning med `Type=Event EventID=1234` er det virkelig er udført for dig:

```
Type=Event EventID=1234 **| Sort TimeGenerated desc**
```

Det skyldes, at det er typen oplevelse, du allerede kender, i logge. For eksempel i Windows Logbog.

Ændre den måde, der returneres resultater, kan du bruge sortering. Følgende eksempler viser, hvordan det fungerer.

```
Type=Event EventID=1234 | Sort TimeGenerated asc
```

```
Type=Event EventID=1234 | Sort Computer asc
```

```
Type=Event EventID=1234 | Sort Computer asc,TimeGenerated desc
```


Enkel ovenstående eksempler viser dig, hvordan fungerer kommandoerne – de ændre udformningen af de resultater, returneres filteret.

### <a name="limit-and-top"></a>Grænse og toppen
En anden mindre kendte kommando er GRÆNSE. Grænsen er en PowerShell-lignende verber. Grænsen er funktionelt identisk med kommandoen ØVERSTE. Følgende forespørgsler returnerer de samme resultater.

```
Type=Event EventID=600 | Limit 1
```

```
Type=Event EventID=600 | Top 1
```


#### <a name="to-search-using-top"></a>Søge ved hjælp af toppen
- Skriv i feltet Søg forespørgsel`Type=Event EventID=600 | Top 1`   
    ![Søg øverst](./media/log-analytics-log-searches/oms-search-top.png)

I billedet ovenfor, der er 358 tusinde poster med Begivenhedid = 600. De felter, facetter og filtre i venstre side viser altid oplysninger om resultaterne returneret *af filter del* af den forespørgsel, som er en del før en hvilken som helst tegnet. Ruden **resultater** returnerer kun den seneste 1 resultatet, fordi kommandoen eksempel formet og transformeret resultaterne.

### <a name="select"></a>Vælg

Kommandoen SELECT opfører sig som Vælg-objekt i PowerShell. Returnerer den filtrerede resultater, der ikke har alle deres oprindelige egenskaber. I stedet markerer kun de egenskaber, du angiver.

#### <a name="to-run-a-search-using-the-select-command"></a>Køre en søgning ved hjælp af kommandoen select

1. Skriv i Søg `Type=Event` og derefter klikke på **Søg**.
2. Klik på **+ få vist flere** på en af resultaterne for at se alle de egenskaber, der har resultatet.
3. Vælg nogle af dem, der eksplicit, og forespørgslen ændres til `Type=Event | Select Computer,EventID,RenderedDescription`.  
    ![Vælg Søg](./media/log-analytics-log-searches/oms-search-select.png)

Dette er kommandoen særligt nyttigt, når du vil styre Søg output og vælge kun dele af data, der er afgørende for din udforskning, hvilket ofte ikke den fulde post. Dette er også nyttigt, når poster forskellige typer har *nogle* fælles egenskaber, men ikke *alle* deres egenskaber er almindelige. Det, du kan generere output, der ser mere naturligt som en tabel eller et godt, når der er eksporteret til en CSV-fil og derefter massaged i Excel.



## <a name="use-the-measure-command"></a>Bruge kommandoen mål

MÅL er et af de mest alsidige kommandoer i Log Analytics søgninger. Det kan du anvende statistiske *Funktioner* til dine data og aggregere resultater, der er grupperet efter et bestemt felt. Der findes flere statistiske funktioner, der understøtter mål.

### <a name="measure-count"></a>Måle count()

Den første statistiske funktion til at arbejde med, og én af det nemmeste at forstå er funktionen *count()* .

Resultatet af en søgeforespørgsel såsom `Type=Event`, Vis filtre, også kaldet facetter i venstre side af søgeresultaterne. Filtrene, der viser en fordelingen af værdier efter et bestemt felt for resultaterne i feltet Søg, der er udført.

![Søg måling Tæl](./media/log-analytics-log-searches/oms-search-measure-count01.png)

For eksempel i billedet ovenfor får du vist feltet **Computer** , og det vises, i næsten 739 tusinde begivenheder i resultaterne, der er 68 entydigt og entydige værdier for feltet **Computer** i disse poster. I feltet kun vises de øverste 5, som er de mest almindelige 5 værdier, der er skrevet i felterne **Computer** ), sorteret efter antallet af dokumenter, der indeholder den bestemte værdi i det pågældende felt. I billedet kan du se, at – blandt de hændelser, der næsten 369 tusinde – 90 tusinde kommer fra OpsInsights04.contoso.com computeren, 83 tusinde fra computeren DB03.contoso.com og så videre.


Hvad nu, hvis du vil se alle værdier, da feltet kun viser kun top 5?

Det er kommandoen mål kan foretage med funktionen count(). Denne funktion bruge ikke en hvilken som helst parametre. Du angive kun det felt, som du vil gruppere efter – feltet **Computer** i dette tilfælde:

`Type=Event | Measure count() by Computer`

![Søg måling Tæl](./media/log-analytics-log-searches/oms-search-measure-count-computer.png)

**Computer** er dog kun et felt, der bruges *i* hver enkelt data – der er ingen relationsdatabaser involveret og der er ikke separat **Computer** objekt overalt. Kun værdier *i* dataene kan beskrive, hvilken enhed oprettet dem, og et antal andre egenskaber og aspekter af data – derfor ord *grene*. Du kan dog lige så godt gruppere efter andre felter. Da de oprindelige resultaterne af næsten 739 tusinde hændelser, der er pipes til kommandoen mål har også et felt med navnet **Begivenhedid**, kan du anvende den samme teknik for at gruppere efter feltet, og få en optælling af begivenheder efter Begivenhedid:

```
Type=Event | Measure count() by EventID
```

Hvis du ikke er interesseret i det antal faktiske poster, der indeholder en bestemt værdi, men i stedet Hvis du kun vil en liste over værdierne, selv, kan du tilføje en *Select* -kommando i slutningen af filen og kun markere den første kolonne:

```
Type=Event | Measure count() by EventID | Select EventID
```

Du kan få mere besværlige og lave foreløbige sorteringer resultaterne i forespørgslen, eller du kan bare klikke på kolonnerne i gitteret for.

```
Type=Event | Measure count() by EventID | Select EventID | Sort EventID asc
```

#### <a name="to-search-using-measure-count"></a>Søge ved hjælp af måling tæller

- Skriv i feltet Søg forespørgsel`Type=Event | Measure count() by EventID`
- Tilføje `| Select EventID` til slutningen af forespørgslen.
- Til sidst skal tilføje `| Sort EventID asc` til slutningen af forespørgslen.


Der er nogle vigtige punkter du Bemærk og fremhæve:

Først skal er du se resultaterne ikke oprindelige rå resultaterne længere. I stedet, de er aggregeret resultater – grundlæggende grupper af resultater. Dette er ikke et problem, men du skal vide, at du interaktion med en meget forskelligt figur med data, som er forskellig fra den oprindelige rå figur, der oprettes på farten som et resultat af funktionen sammenlægning/statistiske.

Andet, returnerer **måling Tæl** i øjeblikket kun de øverste 100 forskellige resultater. Denne begrænsning gælder ikke for de andre statistiske funktioner. Tilfældet, skal du som regel bruge filteret mere præcis først at søge efter bestemte elementer, før du anvender måling count().

## <a name="use-the-max-and-min-functions-with-the-measure-command"></a>Anvend funktionerne Maks eller min med kommandoen mål

Der findes forskellige scenarier, hvor **Måling Max()** og **Mål Min()** er nyttig. Men, da hver funktion er modsatte af hinanden, illustrerer vi Max(), og du kan eksperimentere med Min() på din egen.

Hvis du forespørge efter sikkerhed begivenheder, har de en egenskab **på** ressourceniveau kan variere. Eksempel:

```
Type=SecurityEvent
```

![Søg måling Tæl start](./media/log-analytics-log-searches/oms-search-measure-max01.png)

Hvis du vil have vist den højeste værdi for alle sikkerheden kan hændelser, der er angivet et almindelige Computer, gruppen efter felt, du bruge

```
Type=ConfigurationAlert | Measure Max(Level) by Computer
```

![Søg efter måling Maks computer](./media/log-analytics-log-searches/oms-search-measure-max02.png)

Det bliver vist, at de computere, der havde **niveau** poster, de fleste af dem har mindst niveau 8, mange havde et niveau af 16.

```
Type=ConfigurationAlert | Measure Max(Severity) by Computer
```

![Søg måling maksimale tid genereres computer](./media/log-analytics-log-searches/oms-search-measure-max03.png)

Denne funktion fungerer fint med tal, men det fungerer også med DateTime-felter. Det er praktisk at kontrollere, om den seneste eller sidste tidsstempel for et stykke data indekseret for hver computer. For eksempel: hvornår blev den seneste sikkerhed begivenheden rapporteret til hver computer?

```
Type=ConfigurationChange | Measure Max(TimeGenerated) by Computer
```

## <a name="use-the-avg-function-with-the-measure-command"></a>Brug funktionen avg med kommandoen mål

Funktionen Avg() statistiske bruges med mål kan du beregne regnearksfunktionen MIDDEL gennemsnitsværdien for nogle felt og Grupperesultater efter det samme eller andre felt. Dette er nyttigt i en række i visse tilfælde kan som ydelsesdata.

Vi begynder med ydelsesdata. Bemærk, at OMS aktuelt indsamler tællere i ydeevne til både Windows og Linux-computere.

For at søge efter *alle* ydelsesdata, er den mest grundlæggende forespørgsel:

```
Type=Perf
```

![søge avg start](./media/log-analytics-log-searches/oms-search-avg01.png)

Det første, vil du bemærke er, at Log Analytics viser dig tre perspektiver: liste, som viser dig, som viser de faktiske poster bag diagrammer. Tabel, som viser en tabelvisning over tæller ydelsesdata; og statistik, som viser diagrammer til tællerne i ydeevne.

I billedet ovenfor er der to sæt felter, der er markeret, som angiver følgende:

- Det første sæt identificerer Windows ydeevne tællernavn, objektnavn og navnet på forekomsten i filteret forespørgsel. Dette er de felter, du sandsynligvis vil oftest bruger som facetter/filtre
- **CounterValue** er den faktiske værdi for tælleren. I dette eksempel er værdien *75*.
- **TimeGenerated** er 12:51 i 24-timersformat.

Her er en visning af målepunkter i et diagram.

![søge avg start](./media/log-analytics-log-searches/oms-search-avg02.png)

Når du læse om performance post figuren, og have læst om andre teknikker til søgning, kan du bruge måling Avg() skal aggregeres denne type af numeriske data.

Her er et simpelt eksempel:

```
Type=Perf  ObjectName:Processor  InstanceName:_Total  CounterName:"% Processor Time" | Measure Avg(CounterValue) by Computer
```

![søge avg samplevalue](./media/log-analytics-log-searches/oms-search-avg03.png)

I dette eksempel skal vælge du CPU samlede tid-ydeevne tæller og gennemsnit af Computer. Hvis du vil indskrænke resultaterne til kun de seneste 6 timer, kan du enten bruge kontrolelementet tid filter eller angive i din forespørgsel på følgende måde:

```
Type=Perf  ObjectName:Processor  InstanceName:_Total  CounterName:"% Processor Time" TimeGenerated>NOW-6HOURS | Measure Avg(CounterValue) by Computer
```

### <a name="to-search-using-the-avg-function-with-the-measure-command"></a>Søge ved hjælp af funktionen avg med kommandoen mål
- Skriv i feltet Søg forespørgsel `Type=Perf  ObjectName:Processor  InstanceName:_Total  CounterName:"% Processor Time" TimeGenerated>NOW-6HOURS | Measure Avg(CounterValue) by Computer`.


Du kan samle og koordinere data *på tværs af* computere. Forestil dig f.eks., at du har et sæt værter i nogle sortering af farm, hvor hver node er lig med andre en og de alle de samme skrive af arbejdet, og Indlæs skal afstemmes omkring. Du kan få deres tællere på én gå med følgende forespørgsel og få gennemsnit for hele farmen. Du kan starte ved at vælge computere med eksemplet nedenfor:

```
Type=Perf AND (Computer="AzureMktg01" OR Computer="AzureMktg02" OR Computer="AzureMktg03")
```

Nu hvor du har computerne, vil du også kun markere to KPI'er (nøgletal): % CPU-brug og % ledig diskplads. Så, bliver del af forespørgslen:

```
Type=Perf InstanceName:_Total  ((ObjectName:Processor AND CounterName:"% Processor Time") OR (ObjectName="LogicalDisk" AND CounterName="% Free Space")) AND TimeGenerated>NOW-4HOURS
```

Du kan nu føje computere og tællere med eksemplet nedenfor:

```
Type=Perf InstanceName:_Total  ((ObjectName:Processor AND CounterName:"% Processor Time") OR (ObjectName="LogicalDisk" AND CounterName="% Free Space")) AND TimeGenerated>NOW-4HOURS AND (Computer="AzureMktg01" OR Computer="AzureMktg02" OR Computer="AzureMktg03")
```

Fordi du har en meget specifik markering, kan kommandoen **måling Avg()** returnere gennemsnittet ikke som computer, men på tværs af farmen, ved blot at gruppering efter CounterName. Eksempel:

```
Type=Perf  InstanceName:_Total  ((ObjectName:Processor AND CounterName:"% Processor Time") OR (ObjectName="LogicalDisk" AND CounterName="% Free Space")) AND TimeGenerated>NOW-4HOURS AND (Computer="AzureMktg01" OR Computer="AzureMktg02" OR Computer="AzureMktg03") | Measure Avg(CounterValue) by CounterName
```

Det giver dig en nyttig kompakt visning af et par af dit miljø KPI'er.

![Søg avg gruppering](./media/log-analytics-log-searches/oms-search-avg04.png)


Du kan nemt bruge søgestrengen i et dashboard. For eksempel kan du gemme søgestrengen og oprette et dashboard fra det navngivne *Web Farm KPI'er*. Hvis du vil vide mere om at bruge dashboards, skal du se [oprette et brugerdefineret dashboard i Log Analytics](log-analytics-dashboards.md).

![Søg avg dashboard](./media/log-analytics-log-searches/oms-search-avg05.png)

### <a name="use-the-sum-function-with-the-measure-command"></a>Brug funktionen sum med kommandoen mål

Funktionen sum er lig med andre funktioner af kommandoen mål. Du kan se et eksempel om, hvordan du kan bruge funktionen sum [W3C IIS logfiler](http://blogs.msdn.com/b/dmuscett/archive/2014/09/20/w3c-iis-logs-search-in-system-center-advisor-limited-preview.aspx)søge i Microsoft Azure funktionsdygtige indsigt.

Du kan bruge Max() og Min() med tal, dato, klokkeslæt og tekststrenge. Med tekststrenge, de er sorteret alfabetisk, og du modtager første og sidste.

Du kan dog bruge Sum() med noget andet end numeriske felter. Dette gælder også for Avg().

### <a name="use-the-percentile-function-with-the-measure-command"></a>Bruge funktionen fraktil med kommandoen mål

Funktionen fraktil minder om Avg() og Sum() i, at du kan kun bruge den til numeriske felter. Du kan bruge en hvilken som helst fraktil mellem 1 til 99 på et numerisk felt. Du kan også bruge både **fraktil** og **pct** kommandoer. Her er nogle eksempler:  

```
Type:Perf CounterName:"DiskTransers/sec" |measure percentile95(CurrentValue) by Computer
```
```
Type:Perf ObjectName=LogicalDisk CounterName="Current Disk Queue Length" Computer="MyComputerName" | measure pct65(CurrentValue) by InstanceName
```

## <a name="use-the-where-command"></a>Brug where kommando

Den hvor kommandoen fungerer ligesom et filter, men det kan anvendes i pipeline til at filtrere samlede resultater, der er produceret af en måling kommando – i modsætning til rå resultater, der er filtreret i starten af en forespørgsel.

Eksempel:

```
Type=Perf  CounterName="% Processor Time"  InstanceName="_Total" | Measure Avg(CounterValue) as AVGCPU by Computer
```

Du kan tilføje en anden pipe "|" tegn, og hvor kommando til at få kun computere, hvis gennemsnitlige CPU er større end 80%, med i følgende eksempel:

```
Type=Perf  CounterName="% Processor Time"  InstanceName="_Total" | Measure Avg(CounterValue) as AVGCPU by Computer | Where AVGCPU>80
```

Hvis du kender til Microsoft System Center - Operations Manager, kan du betragte where kommando i management pack vilkår. Hvis eksemplet var en regel, den første del af forespørgslen ville være datakilden og where kommandoen ville være betingelse registrering.

Du kan bruge forespørgslen som et felt i **Min Dashboard**, som en skærm sorterer, til at se, hvornår computer CPU'er er udnyttet. Hvis du vil vide mere om dashboards, skal du se [oprette et brugerdefineret dashboard i Log Analytics](log-analytics-dashboards.md). Du kan også oprette og bruge dashboards ved hjælp af mobilappen. Du kan finde flere oplysninger [OMS Mobile-App ](http://www.windowsphone.com/en-us/store/app/operational-insights/4823b935-83ce-466c-82bb-bd0a3f58d865). I de nederste to felter på følgende billede kan du se skærmen, vises en liste, og som et tal. Grundlæggende, du altid vil tal skal være nul og listen skal være tom. Ellers angiver en besked om betingelse. Hvis det er nødvendigt, kan du bruge det til at se nærmere på hvilke computere er under tryk.

![Mobile dashboard](./media/log-analytics-log-searches/oms-search-mobile.png)

## <a name="use-the-in-operator"></a>Bruge operatoren in

Operatoren *i* , sammen med *NOT IN* kan du bruge subsearches, som er søgninger, der inkluderer en ny søgning som argument. De er indeholdt i klammeparenteserne {} i en anden *primær* eller *ydre* søgning. Resultatet af en subsearch, ofte en liste over forskellige resultater, bruges derefter som et argument i den primære Søg.

Du kan bruge subsearches til at matche undersæt af dine data, du ikke kan beskrive direkte i et søgeudtryk, men der kan oprettes ud fra en søgning. Eksempelvis hvis du er interesseret i at bruge en enkelt søgning til at finde alle begivenheder fra *computere manglende sikkerhedsopdateringer*, skal du til at designe en subsearch, der identificerer den *computere manglende sikkerhedsopdateringer* først, før der er angivet hændelser, der hører til disse værter.

Du kan så express *computere i øjeblikket mangler påkrævede sikkerhedsopdateringer* med følgende forespørgsel:

```
Type:Update UpdateState=Needed Optional=false Classification="Security Updates" TimeGenerated>NOW-24HOURS | measure count() by Computer
```    

![I Søg eksempel](./media/log-analytics-log-searches/oms-search-in01-revised.png)

Når du har på listen, kan du bruge søgningen som en indre søgning til at levere listen over computere til en ydre (primær) søgning, der søger efter hændelser for disse computere. Du kan gøre dette ved at omgive indre søgningen i klammeparenteser og fødning af dens resultater som mulige værdier for et filter felt i den ydre søgning ved hjælp af operatoren IN. Forespørgslen ligner:

```
Type=Event Computer IN {Type:Update UpdateState=Needed Optional=false Classification="Security Updates" TimeGenerated>NOW-24HOURS | measure count() by Computer}
```
![I Søg eksempel](./media/log-analytics-log-searches/oms-search-in02-revised.png)


Også meddelelse om filteret tid brugt i indre søgningen, fordi System opdatering vurdering tager et øjebliksbillede af alle computere døgnet. Du kan foretage indre forespørgslen mere lette og præcis ved at kun søge efter en dag. Ydre søgningen bruger i stedet på valg af tidszone i brugergrænsefladen, hentning af begivenheder fra de seneste 7 dage. Du kan finde flere oplysninger om tid operatorer i [booleske operatorer](#boolean-operators) .

Da du virkelig kun bruge resultatet af feltet indre Søg som filter værdi for den ydre liste, kan du stadig anvende kommandoer i ydre søgningen. Du kan for eksempel stadig gruppere de ovenstående hændelser med en anden måling kommando:

```
Type=Event Computer IN {Type:Update UpdateState=Needed Optional=false Classification="Security Updates" TimeGenerated>NOW-24HOURS | measure count() by Computer} | measure count() by Source
```

![I Søg eksempel](./media/log-analytics-log-searches/oms-search-in03-revised.png)


Generelt, du vil indre forespørgslen til at udføre hurtigt, fordi Log Analytics har tjenesten side-timeout for den og til at returnere en lille mængde resultater. Hvis indre forespørgslen returnerer flere resultater, bliver resultatlisten afkortet, som muligvis kan forårsage ydre søgningen til returnerer forkerte resultater.

En anden regel er, at indre søgningen aktuelt skal give *aggregeret* resultater. Det vil sige, skal den indeholde en *måling* kommando; Du kan ikke i øjeblikket feed rå resultater i en ydre søgning.

Desuden kan der kun én IN-operatoren, og det skal være det sidste filter i forespørgslen. Flere på operatorer må ikke være eller ville – det grundlæggende forhindrer kører flere subsearches: det vigtigste er, at kun én sub/indre søgningen er muligt for hver ydre søgning.

Selv med disse begrænsninger i gør det muligt for mange typer af forbundne søgninger, og gør det muligt at definere noget lignende grupper som computere, brugere eller filer – uanset felterne i dine data indeholder. Her er flere eksempler:

**Alle opdateringer mangler på computere, hvor indstilling af automatisk opdatering er deaktiveret**

```
Type=Update UpdateState=Needed Optional=false Computer IN {Type=UpdateSummary WindowsUpdateSetting=Manual | measure count() by Computer} | measure count() by KBID
```

**Alle fejlhændelser fra computere, der kører SQL Server (= hvor SQL vurdering har kørt)**

```
Type=Event EventLevelName=error Computer IN {Type=SQLAssessmentRecommendation | measure count() by Computer}
```

**Alle sikkerhed begivenheder fra computere, der domæne enheder (= hvor AD vurdering har kørt)**

```
Type=SecurityEvent Computer IN { Type=ADAssessmentRecommendation | measure count() by Computer }
```

**Som andre konti har logget på med de samme computere, hvor konto BACONLAND\jochan har logget på?**

```
Type=SecurityEvent EventID=4624   Account!="BACONLAND\\jochan" Computer IN { Type=SecurityEvent EventID=4624   Account="BACONLAND\\jochan" | measure count() by Computer } | measure count() by Account
```

## <a name="use-the-distinct-command"></a>Bruge kommandoen distinct

Som navnet antyder, indeholder denne kommando en liste over entydige værdier for et felt. Det er fantastisk enkle, men meget nyttigt. Den samme kunne opnås med måling count() kommandoen samt, som vist nedenfor.

```
Type=Event | Measure count() by Computer
```

![Eksempel på forskellige kommandoen](./media/log-analytics-log-searches/oms-search-distinct01-revised.png)

Men hvis du er interesseret i er blot en liste med entydige værdier og ikke antallet af dokumenter, der har, kan give dig værdier, og klik derefter DISTINCT renere og nemmere at læse output og kortere syntaks, som vist nedenfor.

```
Type=Event | Distinct Computer
```
![Eksempel på forskellige kommandoen](./media/log-analytics-log-searches/oms-search-distinct02-revised.png)

## <a name="use-the-countdistinct-function-with-the-measure-command"></a>Bruge funktionen countdistinct med kommandoen mål
Funktionen countdistinct tæller antallet af entydige værdier i de enkelte grupper. Det kan for eksempel bruges til at tælle antallet af entydige computere rapportering for hver Type:

```
* | measure countdistinct(Computer) by Type
```

![OMS countdistinct](./media/log-analytics-log-searches/oms-countdistinct.png)

## <a name="use-the-measure-interval-command"></a>Bruge kommandoen måling interval
Med i nærheden af ydeevnen i realtid dataindsamling, kan du indsamle og visualisere en hvilken som helst ydeevne tæller i Log Analytics. Ganske enkelt at indtaste forespørgslen **Type: performance** returnerer tusindvis af metriske grafer baseret på antallet tællere og servere i dit miljø, Log analyser. Med efter behov metriske sammenlægning, kan du se på den overordnede målepunkter i dit miljø på en høj grad og mere detaljerede data, som du vil have til undersøgelse.

Lad os sige, at du vil se, hvad er den gennemsnitlige CPU på tværs af alle computere. Studere gennemsnittet CPU for alle de computere, muligvis ikke være praktisk fordi resultater kan få udjævnet. Hvis du vil undersøge få mere at vide, kan du sammenlægge resultatet i en mindre tid vinduet dele og se i en tidsserie på tværs af forskellige dimensioner. For eksempel kan du udføre hver time gennemsnittet af CPU-brug på tværs af alle computere på følgende måde:

```
Type:Perf CounterName="% Processor Time" InstanceName="_Total" | measure avg(CounterValue) by Computer Interval 1HOUR
```

![mål gennemsnitlige interval](./media/log-analytics-log-searches/oms-measure-avg-interval.png)

Som standard vises disse resultater i et interaktivt kurvediagram med flere serier.  Dette diagram understøtter serie aktivering/deaktivering af (med y-aksen tilpasse igen), Zoom og holder markøren.  Visningsindstillingen er stadig tilgængelige til visning af rækkedata, hvis det er nødvendigt.

Du kan også gruppere efter andre felter. I dette eksempel, jeg leder på alle % tællere for en bestemt computer, og jeg gerne vil vide, hvad er hver time 70 fraktiler af hver tæller:

```
Type:Perf Computer=beefpatty4 CounterName=%* InstanceName=_Total | measure percentile70(CounterValue) by CounterName Interval 1HOUR
```
Én ting at være opmærksom på, er, at disse forespørgsler ikke er begrænset til tællere i ydeevne. Du kan anvende dem på en hvilken som helst metrikværdi. I dette eksempel leder jeg på W3C IIS-logfiler. Jeg gerne vil vide, hvad er den maksimale tid det tager over et 5 minutters interval til at behandle hver enkelt anmodning:

```
Type:W3CIISLog | measure max(TimeTaken) by csMethod Interval 5MINUTES
```

### <a name="use-multiple-aggregates-in-one-query"></a>Bruge flere samlinger i en forespørgsel
Du kan angive flere Sammenlæg delsætninger i en måling kommando.  Hver enkelt kan være alias uafhængigt af hinanden.  Hvis det ikke er angivet et alias bliver resulterende feltnavnet den aggregatfunktion, der blev brugt (det vil sige "avg(CounterValue)" for avg(CounterValue)).

 ```
Type=WireData | measure avg(ReceivedBytes), avg(SentBytes) by Direction interval 1hour
```
![OMS multiaggregates1](./media/log-analytics-log-searches/oms-multiaggregates1.png)

Her er et andet eksempel:
 ```
* | measure countdistinct(Computer) as Computers, count() as TotalRecords by Type
```


## <a name="next-steps"></a>Næste trin

Du kan finde flere oplysninger om log søgninger, i:

- Bruge [brugerdefinerede felter i Log Analytics](log-analytics-custom-fields.md) til at udvide log søgninger.
- Gennemse den [Log Analytics logger Søg reference](log-analytics-search-reference.md) for at få vist alle søgefelter og facetter, der er tilgængelige i Log analyser.
