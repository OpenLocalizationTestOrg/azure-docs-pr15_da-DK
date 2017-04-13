<properties
    pageTitle="Automatisk skala beregne knuder på en Azure batchen puljen | Microsoft Azure"
    description="Aktivere automatisk skalering på en skyen pulje dynamisk justere antallet af knuder Beregn i puljen."
    services="batch"
    documentationCenter=""
    authors="mmacy"
    manager="timlt"
    editor="tysonn"/>

<tags
    ms.service="batch"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="vm-windows"
    ms.workload="multiple"
    ms.date="10/14/2016"
    ms.author="marsma"/>

# <a name="automatically-scale-compute-nodes-in-an-azure-batch-pool"></a>Automatisk skala beregne knuder på en Azure batchen puljen

Med automatisk skalering, kan tjenesten Azure batchen dynamisk tilføje eller fjerne Beregn noder i en gruppe, der er baseret på parametre, du definerer. Du kan potentielt spare både tid og penge ved automatisk at justere mængden af Beregn power bruges af dit program – Tilføj noder som dit arbejde opgave krav stigning, og fjerne dem, når de Formindsk.

Du aktiverer automatisk skalering på en samling af Beregn noder ved at knytte til den en *Autoskalering formel* , som du definerer, såsom med [PoolOperations.EnableAutoScale] [ net_enableautoscale] metode i [Batchen .NET](batch-dotnet-get-started.md) -biblioteket. Tjenesten batchen anvender derefter denne formel til at bestemme antallet af Beregn noder, der er nødvendige for at udføre arbejdsbyrden. Batchen besvarer service målepunkter dataeksempler, der er indsamlet med jævne mellemrum, og justerer antallet af knuder Beregn i puljen med en konfigurerbar interval, der er baseret på din formel.

Du kan aktivere automatisk skalering, når der oprettes en gruppe, eller på en eksisterende gruppe. Du kan også ændre en eksisterende formel i en gruppe, der er "Autoskalering" aktiveret. Batchen giver mulighed for at evaluere dine formler før tildele dem til grupper, samt overvåge status for automatisk skalering kører.

## <a name="automatic-scaling-formulas"></a>Automatisk skalering formler

En automatisk skalering formlen er en strengværdi, som du definerer, der indeholder en eller flere sætninger, og er tildelt til en samling [autoScaleFormula] [ rest_autoscaleformula] element (batchen RESTEN) eller [CloudPool.AutoScaleFormula] [ net_cloudpool_autoscaleformula] egenskaben (batchen .NET). Når tildelt til en gruppe, bruger tjenesten batchen formlen til at bestemme target antallet af knuder, Beregn i puljen til det næste interval af behandling (mere om intervaller senere). Formel strengen må ikke overstige 8 KB i størrelse, skal du kan medtage op til 100 sætninger, der er adskilt af semikolon og kan indeholde linjeskift og kommentarer.

Du kan betragte automatisk skalering formler som at bruge en batchen Autoskalering "sprog". Formel sætninger er ledig udformet udtryk, som kan indeholde både service-defineret variabler (variabler defineret af tjenesten batchen) og brugerdefinerede variabler (variabler, som du definerer). De kan udføre forskellige handlinger på disse værdier ved hjælp af indbyggede typer, operatorer og funktioner. For eksempel kan en sætning tage følgende format:

```
$myNewVariable = function($ServiceDefinedVariable, $myCustomVariable);
```

Formler indeholder normalt flere sætninger, der udfører handlinger på værdier, der hentes i forrige sætninger. For eksempel først vi have en værdi for `variable1`, derefter overføre den til en funktion til at udfylde `variable2`:

```
$variable1 = function1($ServiceDefinedVariable);
$variable2 = function2($OtherServiceDefinedVariable, $variable1);
```

Med disse sætninger i formlen, dit mål er at ankomme til et antal Beregn noder, der puljen skal skaleres til - **destination** antallet af **knuder, dedikeret**. Dette nummer kan være højere, nederste eller den samme som den aktuelle antallet af knuder på puljen. Batchen evaluerer en samling Autoskalering formel til et bestemt interval ([automatisk skalering intervaller](#automatic-scaling-interval) gennemgås nedenfor). Det Juster derefter target antallet af knuder i puljen til det tal, der angiver Autoskalering formlen på tidspunktet for evalueringen.

Denne formel på to linjer Autoskalering angiver, at antallet af knuder skal justeres efter antallet af aktive opgaver, op til maksimalt 10 Beregn noder som et hurtigt eksempel:

```
$averageActiveTaskCount = avg($ActiveTasks.GetSample(TimeInterval_Minute * 15));
$TargetDedicated = min(10, $averageActiveTaskCount);
```

De næste par afsnit i denne artikel beskrives de forskellige enheder, der udgør din Autoskalering formler, herunder variabler, operatorer, handlinger og funktioner. Du finder ud af, hvordan du får fat i forskellige Beregn ressource og opgave målepunkter i batchen. Du kan bruge disse målepunkter intelligent justere din puljen node tælle baseret på Ressourcestatus brugen og opgave. Du får derefter Lær, hvordan du kan oprette en formel og aktivere automatisk skalering på en gruppe ved hjælp af både batchen RESTEN og .NET API'er. Vi vil færdigt med et par eksempelformler.

> [AZURE.IMPORTANT] Hver Azure batchen konto er begrænset til et maksimalt antal kerner (og dermed Beregn noder), der kan bruges til behandling. Tjenesten batchen opretter noder kun op til denne core grænse. Det kan derfor ikke når target antallet af knuder, Beregn, der er angivet af en formel. Se [kvotaer og begrænsninger for tjenesten Azure batchen](batch-quota-limit.md) for oplysninger om visning og øget kvoter for din konto.

## <a name="variables"></a>Variabler

Du kan bruge både **service-defineret** og **brugerdefinerede** variabler i formlerne Autoskalering. Tjenesten brugerdefineret variablerne, der er indbygget i tjenesten batchen – nogle er skrivebeskyttet, og nogle er skrivebeskyttet. Brugerdefinerede variabler er variabler, *du* definerer. I to linjer eksempelformlen ovenfor, `$TargetDedicated` er en tjeneste brugerdefineret variable, mens du `$averageActiveTaskCount` er en brugerdefineret variabel.

I tabellen nedenfor vises både læse / skrive- og skrivebeskyttet variabler, der er defineret af tjenesten batchen.

Du kan **hente** og **angive** værdierne i disse service-defineret variabler til at administrere antallet af knuder Beregn i en gruppe:

| Læse / skrive-tjenesten brugerdefineret variabler | Beskrivelse |
| --- | --- |
| $TargetDedicated | **Target** antallet af **dedikeret Beregn noder** til puljen. Dette er antallet af knuder, Beregn, puljen skal skaleres til. Det er en "mål" tal, da det er muligt for en gruppe ikke at nå mål antallet af knuder. Dette kan ske, hvis destinationen antallet af knuder er ændret igen af en beregning i efterfølgende Autoskalering før puljen har nået det indledende mål. Det kan også ske, hvis en batchen konto node eller core kvote er nået før target antallet af knuder er nået. |
| $NodeDeallocationOption | Den handling, der opstår, når Beregn noder fjernes fra en gruppe. Mulige værdier er:<ul><li>**requeue**– ophører opgaver med det samme og indsætter dem tilbage på jobbet køen, så de omlægges.<li>**Afslut**– ophører opgaver med det samme og fjerner dem fra jobbet køen.<li>**taskcompletion**– venter i øjeblikket er aktive opgaver for at afslutte og derefter fjerner noden fra puljen.<li>**retaineddata**– venter på, at alle de lokale opgave bevares data på noden, der skal ryddes op, før du fjerner noden fra puljen.</ul> |

Du kan **få** vist værdien af variablerne service-defineret til at foretage justeringer, der er baseret på målepunkter fra tjenesten batchen:

| Skrivebeskyttet service-defineret variabler | Beskrivelse |
| --- | --- |
| $CPUPercent | Den gennemsnitlige procentdel af CPU-brug. |
| $WallClockSeconds | Antallet af sekunder consumed. |
| $MemoryBytes | Det gennemsnitlige antal megabyte, der bruges. |
| $DiskBytes | Det gennemsnitlige antal gigabyte, der bruges på de lokale drev. |
| $DiskReadBytes | Antallet byte, der læses. |
| $DiskWriteBytes | Antallet byte, der skrives. |
| $DiskReadOps | Antallet af Læs diskhandlinger, der udføres. |
| $DiskWriteOps | Antallet af Skriv diskhandlinger, der udføres. |
| $NetworkInBytes | Antallet af indgående byte. |
| $NetworkOutBytes | Antallet af udgående byte. |
| $SampleNodeCount | Antallet af knuder Beregn. |
| $ActiveTasks | Antallet af opgaver i tilstanden aktiv. |
| $RunningTasks | Antallet af opgaver i en igangværende tilstand. |
| $PendingTasks | Summen af $ActiveTasks og $RunningTasks. |
| $SucceededTasks | Antallet af opgaver, der blev afsluttet. |
| $FailedTasks | Antallet af opgaver, der mislykkedes. |
| $CurrentDedicated | Det aktuelle antal dedikeret beregne noder. |

> [AZURE.TIP] Skrivebeskyttet tilstand, service, der er defineret variablerne, der vises over er *objekter* , som giver forskellige metoder til at få adgang til data, der er knyttet til hver. [Hent eksempeldata](#getsampledata) under få flere oplysninger.

## <a name="types"></a>Datatyper

Disse **filtyper** understøttes i en formel.

- dobbelt
- doubleVec
- doubleVecList
- streng
- tidsstempel – tidsstempel er en sammensat struktur, der indeholder følgende medlemmer:

    - år
    - måned (1-12)
    - dag (1-31)
    - Weekday (i formatet af tal, f.eks. 1 for mandag)
    - Time (i 24-timers talformat, f.eks. 13 betyder 1 PM)
    - minut (00-59)
    - andet (00-59)
- TimeInterval

    - TimeInterval_Zero
    - TimeInterval_100ns
    - TimeInterval_Microsecond
    - TimeInterval_Millisecond
    - TimeInterval_Second
    - TimeInterval_Minute
    - TimeInterval_Hour
    - TimeInterval_Day
    - TimeInterval_Week
    - TimeInterval_Year

## <a name="operations"></a>Handlinger

Disse **Handlinger** er tilladt på de typer, der er anført ovenfor.

| Handling                             | Understøttede operatorer   | Resultattype   |
| ------------------------------------- | --------------------- | ------------- |
| Dobbelt *operator* dobbelte              | +, -, *, /            | dobbelt            |
| Dobbelt *operator* timeinterval        | *                     | TimeInterval      |
| doubleVec *operator* dobbelte           | +, -, *, /            | doubleVec         |
| doubleVec *operator* doubleVec        | +, -, *, /            | doubleVec         |
| TimeInterval *operator* dobbelte        | *, /                  | TimeInterval      |
| TimeInterval *operator* timeinterval  | +, -                  | TimeInterval      |
| TimeInterval *operator* tidsstempel     | +                     | tidsstempel         |
| tidsstempel *operator* timeinterval     | +                     | tidsstempel         |
| tidsstempel *operator* tidsstempel        | -                     | TimeInterval      |
| *operatoren*dobbelte                      | -, !                  | dobbelt            |
| *operatoren*timeinterval                | -                     | TimeInterval      |
| Dobbelt *operator* dobbelte              | <, < =, ==, > =, >,! =  | dobbelt            |
| streng *operator* streng              | <, < =, ==, > =, >,! =  | dobbelt            |
| tidsstempel *operator* tidsstempel        | <, < =, ==, > =, >,! =  | dobbelt            |
| TimeInterval *operator* timeinterval  | <, < =, ==, > =, >,! =  | dobbelt            |
| Dobbelt *operator* dobbelte              | & &, & #124; & #124;      | dobbelt            |

Når du tester en dobbelt med en Ternær operatoren (`double ? statement1 : statement2`), undtagen nul er **Sand**, og nul er **Falsk**.

## <a name="functions"></a>Funktioner

Disse foruddefinerede **Funktioner** er tilgængelige for dig at bruge til at definere en automatisk skalering formel.

| Funktionen                          | Returtype.   | Beskrivelse
| --------------------------------- | ------------- | --------- |
| AVG(doubleVecList)                | dobbelt        | Returnerer den gennemsnitlige værdi for alle værdier i doubleVecList.
| Len(doubleVecList)                | dobbelt        | Returnerer længden af den vektor, der er oprettet ud fra doubleVecList.
| LG(Double)                        | dobbelt        | Returnerer loggen grundtal 2 af dobbelt.
| LG(doubleVecList)                 | doubleVec     | Returnerer componentwise loggen grundtal 2 af doubleVecList. En vec(double) skal eksplicit overføres til parameteren. Ellers antages den dobbelte lg(double) version.
| ln(Double)                        | dobbelt        | Returnerer den naturlige logfil over dobbelt.
| ln(doubleVecList)                 | doubleVec     | Returnerer componentwise loggen grundtal 2 af doubleVecList. En vec(double) skal eksplicit overføres til parameteren. Ellers antages den dobbelte lg(double) version.
| Log(Double)                       | dobbelt        | Returnerer loggen grundtal 10 af dobbelt.
| Log(doubleVecList)                | doubleVec     | Returnerer componentwise loggen grundtal 10 af doubleVecList. En vec(double) skal eksplicit overføres til parameteren enkelt dobbelte. Ellers antages den dobbelte log(double) version.
| Max(doubleVecList)                | dobbelt        | Returnerer den største værdi i doubleVecList.
| Min(doubleVecList)                | dobbelt        | Returnerer den mindste værdi i doubleVecList.
| norm(doubleVecList)               | dobbelt        | Returnerer den vektor, der er oprettet ud fra doubleVecList to-normen.
| fraktil (doubleVec v, dobbelte p) | dobbelt        | Returnerer elementet fraktil i vektor v.
| funktionerne rand()                            | dobbelt        | Returnerer en vilkårlig værdi mellem 0,0 og 1,0.
| Range(doubleVecList)              | dobbelt        | Returnerer forskellen mellem de min og max værdier i doubleVecList.
| Std(doubleVecList)                | dobbelt        | Returnerer standardafvigelsen i stikprøverne af værdierne i doubleVecList.
| Stop()                            |               | Stop evalueringen af udtrykket Autoskalering.
| SUM(doubleVecList)                | dobbelt        | Returnerer summen af alle komponenter i doubleVecList.
| tid (streng dateTime = "")          | tidsstempel     | Returnerer tidsstempel af det aktuelle klokkeslæt, hvis der ikke er overført parametre, eller tidsstempel af dato/klokkeslæt-streng, hvis det er overskredet. Understøttede dateTime formater er W3C-DTF og RFC 1123.
| Val (doubleVec v, dobbelte i)        | dobbelt        | Returnerer værdien af det element, der er på den placering i i vektor v, med en første indeks nul.

Nogle af de funktioner, der er beskrevet i ovenstående tabel kan acceptere en liste som et argument. Kommasepareret liste er en hvilken som helst kombination af *dobbelte* og *doubleVec*. Eksempel:

`doubleVecList := ( (double | doubleVec)+(, (double | doubleVec) )* )?`

Værdien *doubleVecList* konverteres til en enkelt *doubleVec* før beregning. Eksempelvis hvis `v = [1,2,3]`, derefter ringe `avg(v)` svarer til opkald `avg(1,2,3)`. Ringe til `avg(v, 7)` svarer til opkald `avg(1,2,3,7)`.

## <a name="getsampledata"></a>Hente eksempeldata

Autoskalering formler handle på målepunkter data (eksempler), der leveres af tjenesten batchen. En formel forøges eller formindskes gruppestørrelse baseret på de værdier, der henter programmet fra tjenesten. De service-defineret variabler, der er beskrevet ovenfor er objekter, som giver forskellige metoder til at få adgang til data, der er knyttet til objektet. For eksempel viser følgende udtryk en anmodning om at hente de sidste fem minutter af CPU-brug:

```
$CPUPercent.GetSample(TimeInterval_Minute * 5)
```

| Metode | Beskrivelse |
| --- | --- |
| GetSample() | Den `GetSample()` metode returnerer en vektor af dataeksempler.<br/><br/>Et eksempel er 30 sekunder værd målepunkter data. Eksempler er med andre ord, der fås hvert 30. Men som angivet nedenfor, der er en forsinkelse fra, når en stikprøve der indsamles, og når den er tilgængelig for en formel. Ikke alle eksempler for en given periode muligvis som sådan til evaluering af en formel.<ul><li>`doubleVec GetSample(double count)`<br/>Angiver antallet af prøver at få fra de seneste eksempler, der er indsamlet.<br/><br/>`GetSample(1)`Returnerer den sidste tilgængelige eksempel. For målepunkter som `$CPUPercent`, men dette der ikke bør benyttes fordi det er ikke muligt at kende, *Når* prøven er indsamlet. Det kan være seneste, eller på grund af problemer med system, kan det være meget ældre. Det er bedre i så fald at bruge et tidsinterval, som vist nedenfor.<li>`doubleVec GetSample((timestamp or timeinterval) startTime [, double samplePercent])`<br/>Angiver en tidsramme til indsamling af eksempeldata. Du kan også angiver det også procentdelen af eksempler, der skal være tilgængelig i den ønskede tidsramme.<br/><br/>`$CPUPercent.GetSample(TimeInterval_Minute * 10)`vil returnere 20 prøver, hvis alle eksempler for de sidste ti minutter er til stede i CPUPercent oversigten. Hvis det seneste minut i kommunikationsoversigten ikke er tilgængelig, men returneres kun 18 eksempler. I dette tilfælde:<br/><br/>`$CPUPercent.GetSample(TimeInterval_Minute * 10, 95)`vil mislykkes, fordi kun 90 procent af eksemplerne er tilgængelige.<br/><br/>`$CPUPercent.GetSample(TimeInterval_Minute * 10, 80)`vil det lykkes.<li>`doubleVec GetSample((timestamp or timeinterval) startTime, (timestamp or timeinterval) endTime [, double samplePercent])`<br/>Angiver en tidsramme for indsamling af data med både et starttidspunkt og et sluttidspunkt.<br/><br/>Som nævnt ovenfor, er der en forsinkelse fra, når en stikprøve der indsamles, og når den er tilgængelig for en formel. Dette skal betragtes som når du bruger den `GetSample` metode. Se `GetSamplePercent` nedenfor.|
| GetSamplePeriod() | Returnerer tidsrum eksempler, der er taget i datasættet historiske eksempel. |
| Count() | Returnerer det samlede antal eksempler i metriske oversigten. |
| HistoryBeginTime() | Returnerer tidsstempel i eksemplet ældste tilgængelige data for en metrikværdi. |
| GetSamplePercent() |Returnerer procentdelen af eksempler, der er tilgængelige for et givet tidsinterval. Eksempel:<br/><br/>`doubleVec GetSamplePercent( (timestamp or timeinterval) startTime [, (timestamp or timeinterval) endTime] )`<br/><br/>Da den `GetSample` metode mislykkes, hvis procentdelen af eksempler returneres, er mindre end `samplePercent` angivet, kan du bruge den `GetSamplePercent` metode til at kontrollere først. Derefter kan du udføre en alternativ handling, hvis utilstrækkelig eksempler er til stede, uden at stoppe automatisk skalering evalueringen.|

### <a name="samples-sample-percentage-and-the-getsample-method"></a>Eksempler, eksempel procent og metoden *GetSample()*

Core driften af en formel til Autoskalering er at få opgaver og ressourcer metriske data og derefter tilpasse gruppestørrelse baseret på dataene. Som er det vigtigt at har en klar forståelse af hvordan Autoskalering formler skal interagere med målepunkter data eller "eksempler."

**Eksempler**

Tjenesten batchen tager *eksempler* på opgaver og ressourcer målepunkter og gør dem tilgængelige til dine Autoskalering formler med jævne mellemrum. Disse eksempler er registreret hvert 30 af tjenesten batchen. Der er dog typisk nogle ventetid, som medfører en forsinkelse mellem når disse eksempler er registreret, og når de er gjort tilgængelige for (og kan læses af) formlerne Autoskalering. Desuden på grund af forskellige faktorer som netværk eller andre infrastruktur problemer, være prøver ikke registreret for et bestemt interval. Dette resulterer i "manglende" eksempler.

**Eksempel procentdel**

Når `samplePercent` der overføres til den `GetSample()` metode eller `GetSamplePercent()` kaldes, "procent" refererer til en sammenligning mellem det samlede *mulige* antal eksempler, der er registreret af tjenesten batchen og antallet af eksempler, der er *tilgængelige* til Autoskalering formlen.

Lad os se på et 10 minutter timespan som et eksempel. Da eksempler registreres hver 30 sekunder, inden for et 10 minutters timespan, vil det maksimale antal eksempler, der er registreret i Batch være 20 prøver (2 i minuttet). På grund af den naturlige forsinkelse på reporting funktion eller et andet problem inden for Azure infrastrukturen, kan der dog kun 15 eksempler, der er tilgængelige for Autoskalering formlen læsevenligheden. Det betyder, for denne periode 10 minutter, kun **75%** af det samlede antal eksempler, der er registreret der faktisk er tilgængelige i formlen.

**GetSample() og eksempel områder**

Formlerne Autoskalering går til vækst og formindskelse dine grupper – tilføjelse af noder eller fjerne noder. Fordi noder koste penge, vil du sikre dig, at dine formler bruger en intelligente metode til analyse, der er baseret på tilstrækkelige data. Vi anbefaler derfor, at du bruger en cirkulerer type analyse i formlerne. Denne type vil forøge og formindske dine grupper, der er baseret på et *område* med indsamlede eksempler.

Gør du ved at bruge `GetSample(interval look-back start, interval look-back end)` til at returnere en **vektor** af eksempler:

```
$runningTasksSample = $RunningTasks.GetSample(1 * TimeInterval_Minute, 6 * TimeInterval_Minute);
```

Når linjen ovenfor evalueres i Batch, returneres en række eksempler en vektor af værdier. Eksempel:

```
$runningTasksSample=[1,1,1,1,1,1,1,1,1,1];
```

Når du har indsamlet vektoren for eksempler, kan du derefter bruge funktioner som `min()`, `max()`, og `avg()` at udlede sigende værdier fra det indsamlede område.

Du kan gennemtvinge en formel beregning *mislykkes* for ekstra sikkerhed Hvis mindre end en bestemt eksempel procentdel er tilgængelig for en bestemt periode. Når du gennemtvinge en formel beregning mislykkes, skal du bede Batch for at indstille yderligere evaluering af formlen, hvis den angivne procentdel af eksempler ikke er tilgængelig – og ingen ændring af gruppestørrelse foretages. Hvis du vil angive en påkrævet procentdel af prøver til evaluering kan fuldføres, skal du angive den som tredje parameter til `GetSample()`. Her angives et krav på 75% af eksempler:

```
$runningTasksSample = $RunningTasks.GetSample(60 * TimeInterval_Second, 120 * TimeInterval_Second, 75);
```

Det er også vigtigt på grund af tidligere nævnte forsinkelsen i eksempel tilgængelighed, til at angive et tidsinterval altid med et udseende tilbage starttidspunkt, som er ældre end et minut. Dette skyldes, at det tager cirka et minut for prøver at sprede gennem hele systemet, så eksempler i området `(0 * TimeInterval_Second, 60 * TimeInterval_Second)` er ofte ikke tilgængelige. Igen, kan du bruge parameteren procentdel af `GetSample()` tvinge en bestemt eksempel procentsats.

> [AZURE.IMPORTANT] Vi **anbefaler** , du *kun *undgå stole ** på `GetSample(1)` i din Autoskalering formler **. Dette skyldes `GetSample(1)` grundlæggende står til tjenesten batchen "Giv mig det sidste eksempel, du har, uanset hvor længe siden du fik." Da det er kun en enkelt eksempel, og det kan være en ældre eksempel, muligvis ikke repræsentant for et større billede af seneste opgave eller ressource tilstand. Hvis du bruger `GetSample(1)`, Sørg for, at det er en del af en større sætning og ikke kun datapunktet, der afhænger af din formel.

## <a name="metrics"></a>Målepunkter

Du kan bruge både **ressource** og **opgave** målepunkter, når du definerer en formel. Du justere target antallet af dedikeret knuder på den gruppe, der er baseret på de målepunkter data, du hente og evaluerer. Se afsnittet [variabler](#variables) ovenfor for at få mere at vide på hver metrisk.

<table>
  <tr>
    <th>Metrisk</th>
    <th>Beskrivelse</th>
  </tr>
  <tr>
    <td><b>Ressource</b></td>
    <td><p><b>Ressource målepunkter</b> er baseret på CPU, båndbredde og hukommelse brugen af Beregn noder samt antallet af knuder.</p>
        <p> Disse service-defineret variabler er nyttige til foretage justeringer, der er baseret på node Tæl:</p>
    <p><ul>
      <li>$TargetDedicated</li>
            <li>$CurrentDedicated</li>
            <li>$SampleNodeCount</li>
    </ul></p>
    <p>Disse service-defineret variabler er nyttige til at foretage justeringer, der er baseret på node ressourceforbrug:</p>
    <p><ul>
      <li>$CPUPercent</li>
      <li>$WallClockSeconds</li>
      <li>$MemoryBytes</li>
      <li>$DiskBytes</li>
      <li>$DiskReadBytes</li>
      <li>$DiskWriteBytes</li>
      <li>$DiskReadOps</li>
      <li>$DiskWriteOps</li>
      <li>$NetworkInBytes</li>
      <li>$NetworkOutBytes</li></ul></p>
  </tr>
  <tr>
    <td><b>Opgave</b></td>
    <td><p><b>Opgave målepunkter</b> er baseret på status for opgaver, som aktiv, venter på, og fuldført. Følgende service-defineret variabler er nyttige til at foretage justeringer af gruppestørrelse baseret på opgave målepunkter:</p>
    <p><ul>
      <li>$ActiveTasks</li>
      <li>$RunningTasks</li>
      <li>$PendingTasks</li>
      <li>$SucceededTasks</li>
            <li>$FailedTasks</li></ul></p>
        </td>
  </tr>
</table>

## <a name="write-an-autoscale-formula"></a>Skrive en Autoskalering formel

Du opretter en Autoskalering formel ved hjælp af sætninger, der bruger ovenstående komponenterne og derefter kombinere sætningerne i en komplet formel. I dette afsnit skal oprette vi en eksempel Autoskalering formel, der kan udføre nogle reale skalering beslutninger.

Først skal Lad os definerer kravene til vores nye Autoskalering formel. Formlen skal gøre følgende:

1. **Øge** target antallet af Beregn knuder i en gruppe, hvis CPU-brug er høj.
2. **Mindske** target antallet af Beregn knuder i en gruppe, når CPU-brug er lav.
3. Begrænse altid det **maksimale** antal noder til 400.

Du kan *øge* antallet af knuder under højt CPU-brug, skal vi definere den sætning, der udfylder en brugerdefineret variabel (`$totalNodes`) med en værdi, der er 110 procent af aktuelle target antallet af knuder, men kun hvis de mindste gennemsnitlige CPU-brug under de sidste 10 minutter blev over 70%. Ellers skal bruge vi den aktuelle dedikerede værdi.

```
$totalNodes =
    (min($CPUPercent.GetSample(TimeInterval_Minute * 10)) > 0.7) ?
    ($CurrentDedicated * 1.1) : $CurrentDedicated;
```

Den næste sætning i vores formel til at *reducere* antallet af knuder under lav CPU-brug, angiver den samme `$totalNodes` variable til 90 procent af aktuelle target antallet af knuder, hvis den gennemsnitlige CPU-brug i de seneste 60 minutter var under 20%. Ellers skal bruge den aktuelle værdi af `$totalNodes` , som vi udfyldt i sætningen ovenfor.

```
$totalNodes =
    (avg($CPUPercent.GetSample(TimeInterval_Minute * 60)) < 0.2) ?
    ($CurrentDedicated * 0.9) : $totalNodes;
```

Nu begrænse target antallet af dedikeret Beregn noder til en **maksimale** af 400:

```
$TargetDedicated = min(400, $totalNodes)
```

Her er fuldført formlen:

```
$totalNodes =
    (min($CPUPercent.GetSample(TimeInterval_Minute * 10)) > 0.7) ?
    ($CurrentDedicated * 1.1) : $CurrentDedicated;
$totalNodes =
    (avg($CPUPercent.GetSample(TimeInterval_Minute * 60)) < 0.2) ?
    ($CurrentDedicated * 0.9) : $totalNodes;
$TargetDedicated = min(400, $totalNodes)
```

## <a name="create-an-autoscale-enabled-pool"></a>Oprette en Autoskalering aktiverede puljen

For at oprette en ny gruppe med Autoskalering aktiveret, kan du bruge en af en af følgende fremgangsmåder:

**Batchen .NET**

1. Opret puljen med [BatchClient.PoolOperations.CreatePool](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.pooloperations.createpool.aspx).
1. Angive egenskaben [CloudPool.AutoScaleEnabled](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudpool.autoscaleenabled.aspx) til `true`.
1. Indstil egenskaben [CloudPool.AutoScaleFormula](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudpool.autoscaleformula.aspx) med Autoskalering formlen.
1. (Valgfrit) Angive egenskaben [CloudPool.AutoScaleEvaluationInterval](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudpool.autoScaleevaluationinterval.aspx) (standard er 15 minutter).
1. Udfør puljen med [CloudPool.Commit](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudpool.commit.aspx) eller [CommitAsync](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudpool.commitasync.aspx).

**Batchen REST-API**

* [Tilføj en gruppe til en konto](https://msdn.microsoft.com/library/azure/dn820174.aspx): angive den `enableAutoScale` og `autoScaleFormula` elementer i din REST-API anmodning om at konfigurere automatisk skalering for en gruppe, når du opretter den.

Følgende kodestykke opretter en Autoskalering aktiverede puljen ved hjælp af [Batchen .NET] [ net_api] bibliotek. Den gruppe Autoskalering formel angiver target antallet af knuder til 5 på mandage og 1 på hver anden dag i ugen. [Interval for automatisk skalering](#automatic-scaling-interval) er angivet til 30 minutter. I dette og de andre C# kodestykker i denne artikel, "myBatchClient" er en initialiseret korrekt forekomst af [BatchClient][net_batchclient].

```csharp
CloudPool pool = myBatchClient.PoolOperations.CreatePool("mypool", "3", "small");
pool.AutoScaleEnabled = true;
pool.AutoScaleFormula = "$TargetDedicated = (time().weekday == 1 ? 5:1);";
pool.AutoScaleEvaluationInterval = TimeSpan.FromMinutes(30);
pool.Commit();
```

Ud over batchen REST-API og .NET SDK, kan du bruge en af de andre [Batchen SDK'er](batch-technical-overview.md#batch-development-apis), [batchen PowerShell-cmdletter](batch-powershell-cmdlets-get-started.md)og [Batchen CLI](batch-cli-get-started.md) til at arbejde med Autoskalering.

> [AZURE.IMPORTANT] Når du opretter en Autoskalering aktiverede puljen, skal du **ikke** angive den `targetDedicated` parameter. Også, hvis du manuelt vil ændre størrelsen på en Autoskalering aktiverede puljen (for eksempel med [BatchClient.PoolOperations.ResizePool][net_poolops_resizepool]), derefter skal du første **deaktivere** automatisk skalering på puljen, ændre dets størrelse.

### <a name="automatic-scaling-interval"></a>Interval for automatisk skalering

Som standard justerer tjenesten batchen gruppestørrelse ifølge formlen Autoskalering hver **15 minutter**. Dette interval kan konfigureres, men ved hjælp af følgende puljen egenskaber:

* [CloudPool.AutoScaleEvaluationInterval] [ net_cloudpool_autoscaleevalinterval] (Batch .NET)
* [autoScaleEvaluationInterval] [ rest_autoscaleinterval] (REST-API)

Det mindste interval er fem minutter, og det maksimale antal er 168 timer. Hvis der er angivet et interval uden for dette interval, returnerer tjenesten batchen fejlen forkert anmodning (400).

> [AZURE.NOTE] Autoskalering er i øjeblikket ikke er beregnet til at reagere på ændringer i mindre end et minut, men hellere er beregnet til at tilpasse størrelsen af din puljen efterhånden som du kører en arbejdsbyrde.

## <a name="enable-autoscaling-on-an-existing-pool"></a>Aktivere Autoskalering på en eksisterende gruppe

Hvis du allerede har oprettet en gruppe med et angivet antal Beregn noder ved hjælp af parameteren *targetDedicated* , kan du stadig aktivere Autoskalering på puljen. Hvert Batch SDK indeholder en "Aktivér Autoskalering"-operation, f.eks.:

* [BatchClient.PoolOperations.EnableAutoScale] [ net_enableautoscale] (Batch .NET)
*  [Aktivere automatisk skalering på en samling] [ rest_enableautoscale] (REST-API)

Når du aktiverer Autoskalering på en eksisterende gruppe, gælder følgende:

* Hvis automatisk skalering er **deaktiveret** på puljen når du udsteder "Aktivér Autoskalering" anmodningen, angive du *skal* en gyldig Autoskalering formel, når du udsteder anmodningen. Du kan *eventuelt* angive et Autoskalering evaluering interval. Hvis du ikke angiver et interval, bruges af standardværdien for 15 minutter.

* Hvis Autoskalering er **aktiveret** på gruppen, kan du angive en formel til Autoskalering, et evaluering interval eller begge dele. Du kan ikke udelade begge egenskaber.

  * Hvis du angiver en ny Autoskalering evaluering interval, er ikke længere eksisterende beregning i tidsplanen, og startes en ny tidsplan. Den nye tidsplan starttidspunkt er den tid, hvormed anmodningen "aktivere Autoskalering", er udstedt.

  * Hvis du udelader Autoskalering formlen eller evaluering interval, tjenesten batchen du fortsætter med at bruge den aktuelle værdi af indstillingen.

> [AZURE.NOTE] Hvis der blev angivet en værdi for parameteren *targetDedicated* , da puljen blev oprettet, ignoreres, når automatisk skalering formlen evalueres.

Denne C#-kodestykke bruger [Batchen .NET] [ net_api] bibliotek til at aktivere Autoskalering på en eksisterende gruppe:

```csharp
// Define the autoscaling formula. This formula sets the target number of nodes
// to 5 on Mondays, and 1 on every other day of the week
string myAutoScaleFormula = "$TargetDedicated = (time().weekday == 1 ? 5:1);";

// Set the autoscale formula on the existing pool
myBatchClient.PoolOperations.EnableAutoScale(
    "myexistingpool",
    autoscaleFormula: myAutoScaleFormula);
```

### <a name="update-an-autoscale-formula"></a>Opdatere en Autoskalering formel

Du bruger den samme "aktivere Autoskalering" anmodning *opdatere* formlen på en eksisterende Autoskalering aktiverede puljen (for eksempel med [EnableAutoScale] [ net_enableautoscale] i batchen .NET). Der er ingen speciel "opdatere Autoskalering" handling. Eksempelvis hvis Autoskalering er allerede aktiveret på "myexistingpool", når følgende kode afvikles, autoskalering formlen erstattes med indholdet af `myNewFormula`.

```csharp
myBatchClient.PoolOperations.EnableAutoScale(
    "myexistingpool",
    autoscaleFormula: myNewFormula);
```

### <a name="update-the-autoscale-interval"></a>Opdatere Autoskalering interval

Når med opdaterer en Autoskalering formel, du bruger den samme [EnableAutoScale] [ net_enableautoscale] metode til at ændre intervallet Autoskalering evaluering af en eksisterende Autoskalering aktiverede samling. For eksempel, at angive Autoskalering evaluering interval til 60 minutter for en gruppe, der allerede er Autoskalering aktiverede:

```csharp
myBatchClient.PoolOperations.EnableAutoScale(
    "myexistingpool",
    autoscaleEvaluationInterval: TimeSpan.FromMinutes(60));
```

## <a name="evaluate-an-autoscale-formula"></a>Evaluere en formel til Autoskalering

Du kan altid evaluere en formel, før du anvender den til en gruppe. På denne måde, kan du udføre en "test Kør" af formlen til at se, hvordan dens sætninger evaluere før du sætte formlen i fremstilling.

Hvis du vil evaluere en Autoskalering formel, skal du første **aktivere Autoskalering** på puljen med en **gyldig formel**. Hvis du vil teste en formel i en gruppe, som endnu ikke har Autoskalering aktiveret, kan du bruge én linje formlen `$TargetDedicated = 0` når du først aktivere Autoskalering. Brug derefter en af følgende fremgangsmåder til at evaluere den formel, du vil teste:

* [BatchClient.PoolOperations.EvaluateAutoScale](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.pooloperations.evaluateautoscale.aspx) eller [EvaluateAutoScaleAsync](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.pooloperations.evaluateautoscaleasync.aspx)

    Metoderne batchen .NET kræver ID for en eksisterende gruppe og en streng, der indeholder Autoskalering formlen skal evalueres. Evalueringsresultaterne er indeholdt i den returnerede [AutoScaleEvaluation](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.autoscaleevaluation.aspx) forekomst.

* [Evaluere en automatisk skalering formel](https://msdn.microsoft.com/library/azure/dn820183.aspx)

    I denne REST-API-anmodning, Angiv pulje-ID i URI og Autoskalering formlen i *autoScaleFormula* elementet i anmodningsteksten. Svar på handlingen indeholder alle fejloplysninger, der er relateret til formlen.

I denne [Batchen .NET] [ net_api] kodestykke, vi evaluere en formel, før du anvender den på [CloudPool][net_cloudpool]. Hvis gruppen ikke har aktiveret Autoskalering, aktivere vi den først.

```csharp
// First obtain a reference to an existing pool
CloudPool pool = batchClient.PoolOperations.GetPool("myExistingPool");

// If autoscaling isn't already enabled on the pool, enable it.
// You can't evaluate an autoscale formula on non-autoscale-enabled pool.
if (pool.AutoScaleEnabled == false)
{
    // We need a valid autoscale formula to enable autoscaling on the
    // pool. This formula is valid, but won't resize the pool:
    pool.EnableAutoScale(
        autoscaleFormula: $"$TargetDedicated = {pool.CurrentDedicated};",
        autoscaleEvaluationInterval: TimeSpan.FromMinutes(5));

    // Batch limits EnableAutoScale calls to once every 30 seconds.
    // Because we want to apply our new autoscale formula below if it
    // evaluates successfully, and we *just* enabled autoscaling on
    // this pool, we pause here to ensure we pass that threshold.
    Thread.Sleep(TimeSpan.FromSeconds(31));

    // Refresh the properties of the pool so that we've got the
    // latest value for AutoScaleEnabled
    pool.Refresh();
}

// We must ensure that autoscaling is enabled on the pool prior to
// evaluating a formula
if (pool.AutoScaleEnabled == true)
{
    // The formula to evaluate - adjusts target number of nodes based on
    // day of week and time of day
    string myFormula = @"
        $curTime = time();
        $workHours = $curTime.hour >= 8 && $curTime.hour < 18;
        $isWeekday = $curTime.weekday >= 1 && $curTime.weekday <= 5;
        $isWorkingWeekdayHour = $workHours && $isWeekday;
        $TargetDedicated = $isWorkingWeekdayHour ? 20:10;
    ";

    // Perform the autoscale formula evaluation. Note that this does not
    // actually apply the formula to the pool.
    AutoScaleRun eval =
        batchClient.PoolOperations.EvaluateAutoScale(pool.Id, myFormula);

    if (eval.Error == null)
    {
        // Evaluation success - print the results of the AutoScaleRun.
        // This will display the values of each variable as evaluated by the
        // autoscale formula.
        Console.WriteLine("AutoScaleRun.Results: " +
            eval.Results.Replace("$", "\n    $"));

        // Apply the formula to the pool since it evaluated successfully
        batchClient.PoolOperations.EnableAutoScale(pool.Id, myFormula);
    }
    else
    {
        // Evaluation failed, output the message associated with the error
        Console.WriteLine("AutoScaleRun.Error.Message: " +
            eval.Error.Message);
    }
}
```

Vellykket evalueringen af formlen i denne kodestykke medfører output, der svarer til følgende:

```
AutoScaleRun.Results:
    $TargetDedicated=10;
    $NodeDeallocationOption=requeue;
    $curTime=2016-10-13T19:18:47.805Z;
    $isWeekday=1;
    $isWorkingWeekdayHour=0;
    $workHours=0
```

## <a name="get-information-about-autoscale-runs"></a>Få oplysninger om Autoskalering kører

For at sikre, at formlen fungerer som forventet, anbefaler vi du med jævne mellemrum kontrollerer resultaterne af Autoskalering "kører" batchen udfører på din gruppe. Så få (eller opdatere) en reference til puljen, og se filegenskaberne for den sidste Autoskalering køre.

I batchen .NET har egenskaben [CloudPool.AutoScaleRun](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudpool.autoscalerun.aspx) flere egenskaber, der giver oplysninger om den seneste automatisk skalering køre er udført på puljen af tjenesten batchen.

* [AutoScaleRun.Timestamp](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.autoscalerun.timestamp.aspx)
* [AutoScaleRun.Results](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.autoscalerun.results.aspx)
* [AutoScaleRun.Error](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.autoscalerun.error.aspx)

[Få oplysninger om en samling](https://msdn.microsoft.com/library/dn820165.aspx) anmodningen returnerer oplysninger om puljen, som indeholder den seneste automatisk skalering køre oplysninger i [autoScaleRun](https://msdn.microsoft.com/library/dn820165.aspx#bk_autrun)i REST-API.

Følgende C#-kodestykke bruger biblioteket batchen .NET til at udskrive oplysninger om den sidste Autoskalering køre på puljen "myPool":

```csharp
Cloud pool = myBatchClient.PoolOperations.GetPool("myPool");
Console.WriteLine("Last execution: " + pool.AutoScaleRun.Timestamp);
Console.WriteLine("Result:" + pool.AutoScaleRun.Results.Replace("$", "\n  $"));
Console.WriteLine("Error: " + pool.AutoScaleRun.Error);
```

Eksempel på output fra den foregående kodestykke:

```
Last execution: 10/14/2016 18:36:43
Result:
  $TargetDedicated=10;
  $NodeDeallocationOption=requeue;
  $curTime=2016-10-14T18:36:43.282Z;
  $isWeekday=1;
  $isWorkingWeekdayHour=0;
  $workHours=0
Error:
```

## <a name="example-autoscale-formulas"></a>Eksempel Autoskalering formler

Lad os se nærmere på nogle formler, der viser forskellige måder at justere Beregn ressourcer i en gruppe.

### <a name="example-1-time-based-adjustment"></a>Eksempel 1: Tidsbaserede justering

Måske vil du justere størrelsen puljen baseret på dag i ugen og klokkeslættet for dagen for at øge eller mindske antallet af knuder i puljen i overensstemmelse hermed.

Denne formel henter først det aktuelle klokkeslæt. Hvis det er en ugedag (1-5) og i arbejdstiden (8 til 18), er gruppestørrelse mål angivet til 20 noder. Ellers skal indstilles til 10 noder.

```
$curTime = time();
$workHours = $curTime.hour >= 8 && $curTime.hour < 18;
$isWeekday = $curTime.weekday >= 1 && $curTime.weekday <= 5;
$isWorkingWeekdayHour = $workHours && $isWeekday;
$TargetDedicated = $isWorkingWeekdayHour ? 20:10;
```

### <a name="example-2-task-based-adjustment"></a>Eksempel 2: Opgavebaseret justering

I dette eksempel tilpasses puljestørrelse, baseret på antallet af opgaver i køen. Bemærk, at både kommentarer og linjeskift acceptable i formlen strenge.

```csharp
// Get pending tasks for the past 15 minutes.
$samples = $ActiveTasks.GetSamplePercent(TimeInterval_Minute * 15);
// If we have fewer than 70 percent data points, we use the last sample point,
// otherwise we use the maximum of last sample point and the history average.
$tasks = $samples < 70 ? max(0,$ActiveTasks.GetSample(1)) : max( $ActiveTasks.GetSample(1), avg($ActiveTasks.GetSample(TimeInterval_Minute * 15)));
// If number of pending tasks is not 0, set targetVM to pending tasks, otherwise
// half of current dedicated.
$targetVMs = $tasks > 0? $tasks:max(0, $TargetDedicated/2);
// The pool size is capped at 20, if target VM value is more than that, set it
// to 20. This value should be adjusted according to your use case.
$TargetDedicated = max(0, min($targetVMs, 20));
// Set node deallocation mode - keep nodes active only until tasks finish
$NodeDeallocationOption = taskcompletion;
```

### <a name="example-3-accounting-for-parallel-tasks"></a>Eksempel 3: Accounting for parallel opgaver

Dette er et andet eksempel, justerer puljen størrelsen baseret på antallet af opgaver. Denne formel også tager højde [MaxTasksPerComputeNode] [ net_maxtasks] værdi, der er angivet til puljen. Dette er især nyttig i situationer, hvor [udførelse af parallel opgave](batch-parallel-node-tasks.md) er blevet aktiveret på din gruppe.

```csharp
// Determine whether 70 percent of the samples have been recorded in the past
// 15 minutes; if not, use last sample
$samples = $ActiveTasks.GetSamplePercent(TimeInterval_Minute * 15);
$tasks = $samples < 70 ? max(0,$ActiveTasks.GetSample(1)) : max( $ActiveTasks.GetSample(1),avg($ActiveTasks.GetSample(TimeInterval_Minute * 15)));
// Set the number of nodes to add to one-fourth the number of active tasks (the
// MaxTasksPerComputeNode property on this pool is set to 4, adjust this number
// for your use case)
$cores = $TargetDedicated * 4;
$extraVMs = (($tasks - $cores) + 3) / 4;
$targetVMs = ($TargetDedicated + $extraVMs);
// Attempt to grow the number of compute nodes to match the number of active
// tasks, with a maximum of 3
$TargetDedicated = max(0,min($targetVMs,3));
// Keep the nodes active until the tasks finish
$NodeDeallocationOption = taskcompletion;
```

### <a name="example-4-setting-an-initial-pool-size"></a>Eksempel 4: Angive en indledende gruppestørrelse

I dette eksempel vises et C# kodestykke med en Autoskalering formel, der angiver gruppestørrelsen til et bestemt antal noder for en indledende tidsperiode. Derefter tilpasses den gruppestørrelse baseret på antallet kører og aktive opgaver, når det oprindelige tidsrum er gået.

Formlen i den følgende kodestykke:

- Angiver den indledende gruppestørrelse til fire noder.
- Justeres ikke gruppestørrelsen inden for de første 10 minutter for den gruppe livscyklus.
- Når du har 10 minutter, henter den maksimale værdi af antallet, der kører og aktive opgaver inden for de seneste 60 minutter.
  - Hvis begge værdier er 0 (angiver, at ingen opgaver var aktiv eller aktiv i de seneste 60 minutter), vil gruppestørrelsen er angivet til 0.
  - Hvis enten værdi er større end nul, der foretages ingen ændring.

```csharp
string now = DateTime.UtcNow.ToString("r");
string formula = string.Format(@"
    $TargetDedicated = {1};
    lifespan         = time() - time(""{0}"");
    span             = TimeInterval_Minute * 60;
    startup          = TimeInterval_Minute * 10;
    ratio            = 50;

    $TargetDedicated = (lifespan > startup ? (max($RunningTasks.GetSample(span, ratio), $ActiveTasks.GetSample(span, ratio)) == 0 ? 0 : $TargetDedicated) : {1});
    ", now, 4);
```

## <a name="next-steps"></a>Næste trin

* [Maksimere Azure batchen beregne Ressourceforbrug med samtidige node opgaver](batch-parallel-node-tasks.md) indeholder oplysninger om, hvordan du kan udføre flere opgaver på Beregn knuderne i din gruppe samtidigt. Ud over Autoskalering kan denne funktion hjælpe med at sænke job varighed for nogle arbejdsbelastninger, så du sparer penge.

* Sørg for, at din batchen programmet forespørger tjenesten batchen i den mest optimale måde til en anden effektivitet booster. I [forespørgsel tjenesten Azure batchen effektivt](batch-efficient-list-queries.md), skal lærer du, hvordan du kan begrænse mængden data, der krydser en netværksforbindelse, når du opretter en forespørgsel status for potentielt tusindvis af Beregn noder eller opgaver.

[net_api]: https://msdn.microsoft.com/library/azure/mt348682.aspx
[net_batchclient]: http://msdn.microsoft.com/library/azure/microsoft.azure.batch.batchclient.aspx
[net_cloudpool]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudpool.aspx
[net_cloudpool_autoscaleformula]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudpool.autoscaleformula.aspx
[net_cloudpool_autoscaleevalinterval]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudpool.autoscaleevaluationinterval.aspx
[net_enableautoscale]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.pooloperations.enableautoscale.aspx
[net_maxtasks]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudpool.maxtaskspercomputenode.aspx
[net_poolops_resizepool]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.pooloperations.resizepool.aspx

[rest_api]: https://msdn.microsoft.com/library/azure/dn820158.aspx
[rest_autoscaleformula]: https://msdn.microsoft.com/library/azure/dn820173.aspx
[rest_autoscaleinterval]: https://msdn.microsoft.com/library/azure/dn820173.aspx
[rest_enableautoscale]: https://msdn.microsoft.com/library/azure/dn820173.aspx
