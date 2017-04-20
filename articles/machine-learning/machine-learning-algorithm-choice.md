<properties
    pageTitle="Sådan vælger du machine learning algoritmer | Microsoft Azure"
    description="Sådan vælger du Azure Machine Learning algoritmer for tilsyn og fungerer undervisning i klynger, klassificering eller regression forsøg."
    services="machine-learning"
    documentationCenter=""
    authors="brohrer"
    manager="jhubbard"
    editor="cgronlun"
    tags=""/>
    
<tags
    ms.service="machine-learning"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="data-services"
    ms.date="08/09/2016"
    ms.author="brohrer;garye" />

# <a name="how-to-choose-algorithms-for-microsoft-azure-machine-learning"></a>Sådan vælger du algoritmer for Microsoft Azure Machine Learning

Svaret på spørgsmålet "Hvilken machine learning-algoritmen skal jeg bruge?" er altid "Det afhænger af." Det afhænger af størrelsen, kvaliteten og arten af dataene. Det afhænger af hvad du vil gøre med svaret. Det afhænger af, hvordan matematik af algoritmen blev oversat til vejledning for den computer, du bruger. Og det afhænger af, hvor meget tid du har. Selv de mest erfarne forskere med data kan ikke se, hvilken algoritme udføres bedst før du prøver dem.

## <a name="the-machine-learning-algorithm-cheat-sheet"></a>Machine Learning-algoritmen Cheat ark

**Microsoft Azure Machine Learning algoritme Cheat ark** hjælper dig med at vælge den rigtige machine learning-algoritme til forudseende analytics-løsninger fra Microsoft Azure Machine Learning biblioteket af algoritmer.
Denne artikel gennemgår hvordan du bruger den.

> [AZURE.NOTE] For at hente det snyde ark og følge med i denne artikel, skal du gå til [Machine learning algoritme snyde ark til Microsoft Azure Machine Learning Studio](machine-learning-algorithm-cheat-sheet.md).

Denne snyde ark har en meget specifik målgruppe for øje: en begyndelsen data scientist med undergraduate niveau machine learning forsøg på at vælge en algoritme til at starte med i Azure Machine Learning Studio. Det betyder, at det giver nogle generaliseringer og oversimplifications, men den vil pege i en retning, der er sikker. Det betyder også, at der er masser af algoritmer, der ikke er angivet her. Efterhånden som Azure Machine Learning vokser, der omfatter en mere komplet sæt af tilgængelige metoder, skal du føje dem.

Disse anbefalinger er kompileret feedback og tip fra en masse data forskere og machine learning eksperter. Vi er ikke enige om alt, men jeg har forsøgt at harmonisere vores udtalelser til uslebne enighed. De fleste af erklæringer, der er uenighed, der begynder med "Det afhænger af..."

### <a name="how-to-use-the-cheat-sheet"></a>Sådan bruges det snyde ark

Læs etiketterne sti og den algoritme, som på "For * &lt;stien etiket&gt; * Brug * &lt;algoritme&gt;*." For eksempel *"hastighed* Brug *to klasse logistisk regression"*. Nogle gange mere end én gren gælder.
Ingen af dem vil nogle gange være den perfekte tilpasning. De er ment som regel af tommelfingeren anbefalinger, så du ikke bekymre dig om det er nøjagtige.
Flere data-forskere, jeg talte med nævnte, er den eneste måde at finde den bedste algoritme til at prøve dem alle.

Her er et eksempel fra [Cortana Intelligence galleri](http://gallery.cortanaintelligence.com/) af et eksperiment, der forsøger flere algoritmer mod de samme data, og sammenligner resultaterne: [sammenligne flere klasse klassificeringer: brev anerkendelse](http://gallery.cortanaintelligence.com/Details/a635502fc98b402a890efe21cec65b92).

>[AZURE.TIP] Hvis du vil hente og udskrive et diagram, der giver et overblik over mulighederne i Machine Learning Studio, se [Oversigtsdiagram af Azure Machine Learning Studio funktioner](machine-learning-studio-overview-diagram.md).

## <a name="flavors-of-machine-learning"></a>Versionerne af machine learning

### <a name="supervised"></a>Kontrolleret

Kontrollerede learning algoritmer komme med forudsigelser, der er baseret på en række eksempler. Historiske Aktiekurser kan eksempelvis bruges til at fare gæt til fremtidige priser. Hvert eksempel bruges til uddannelse, der er navngivet med værdien af interesse – i dette tilfælde aktiekursen. En algoritme kontrollerede learning søger efter mønstre i disse værdietiketter. Den kan bruge de oplysninger, der kan være relevante, dag i ugen, jul, firmaets økonomiske data, typen industri, tilstedeværelse af geopolicitical forstyrrende hændelser – og hver algoritme, der søger efter forskellige typer af mønstre. Når algoritmen, der har fundet det bedste mønster kan det, bruges dette mønster til at komme med forudsigelser for ikke-navngivet test data – morgendagens priser.

Dette er en populære og nyttige type machine learning. Med en enkelt undtagelse er alle moduler i Azure Machine Learning kontrolleret læring algoritmer. Der er flere bestemte typer af kontrollerede undervisning, der er repræsenteret i Azure Machine Learning: klassificering, regression og anomali registrering.

* **Klassificering**. Når dataene, der bruges til at forudsige en kategori, kaldes kontrollerede learning også klassificering. Dette er tilfældet, når du tildeler et billede som et billede af en kat eller en hund. Hvis der er kun to valgmuligheder, kaldes dette **to klasse** eller **binomiale klassificering**. Når der findes flere kategorier, som når at forudsige vinderen af NCAA marts Madness-turnering, er dette problem kendt som **flere klasse klassifikation**.

* **Regression**. Når en værdi er der forventes som med aktie-kurser, kaldes kontrollerede learning regression.

* **Registrering af anomali**. Nogle gange er målet at identificere datapunkter, der er ganske enkelt usædvanlige. I afsløring af svig, for eksempel er en usædvanlig kreditkort forbrugsgrænse mønstre tvivlsomme. De mulige variationer, der er så mange og så få, at det ikke er muligt at få at vide hvilken svigagtig aktivitet uddannelse eksemplerne ser ud. Den fremgangsmåde, der fører til registrering af anomali er blot vide, hvilken normal aktivitet ser sådan ud (ved hjælp af en ikke-svigagtig transaktioner i opdateringshistorikken) og identificere alt, hvad der er væsentligt anderledes.

### <a name="unsupervised"></a>Opsyn

I fungerer learning har datapunkter ingen etiket, der er tilknyttet. I stedet er formålet med en fungerer learning algoritme til at organisere data på en måde eller til at beskrive dens struktur. Dette kan betyde grupperes i klynger og til at finde forskellige måder at se på komplekse data, så det ser mere enkel eller mere organiseret.

### <a name="reinforcement-learning"></a>Styrke indlæring

I styrkelse læring, får algoritmen til at vælge en handling som svar på hvert datapunkt. Algoritmen learning modtager også en belønning signal kort tid senere, der angiver, hvor god var beslutningen.
På baggrund af dette ændrer algoritmen strategien for at opnå den højeste belønning. Der er i øjeblikket ingen styrke undervisningsmoduler algoritme i Azure maskine undervisning. Styrke undervisning er almindelige i robotics, hvor sensor aflæsninger på et tidspunkt i tid er et datamærke, og algoritmen, der skal vælge den robot næste handling. Det er også en fysisk egnet til Internet ting programmer.

## <a name="considerations-when-choosing-an-algorithm"></a>Overvejelser ved valg af en algoritme

### <a name="accuracy"></a>Nøjagtighed

Få det mest præcise svar muligt ikke altid nødvendigt.
Nogle gange er en tilnærmelse passende, afhængigt af hvad du vil bruge den til. Hvis det er tilfældet, kan du muligvis klipper din behandlingstid drastisk ved stikning med flere omtrentlige metoder. En anden fordel ved flere omtrentlige metoder er, at de naturligt har tendens til at undgå [overfitting](https://youtu.be/DQWI1kvmwRg).

### <a name="training-time"></a>Uddannelsestiden

Antallet af minutter eller timer, der er nødvendige for at træne en model varierer megen mellem algoritmer. Uddannelse tid er ofte tæt forbundet med nøjagtighed — en typisk følger med den anden. Desuden er nogle algoritmer mere følsomme over for antallet af datapunkter, end andre.
Når tiden er begrænset kan den nå valget af algoritme, især når datasættet, der er stor.

### <a name="linearity"></a>Linearitet

Masser af machine learning algoritmer gør brug af linearitet. Lineær klassificering algoritmer forudsætter, at klasser kan være adskilt af en ret linje (eller dens højere-dimensionelle analog). Disse omfatter logistisk regression og understøtter vektor maskiner (som implementeret i Azure Machine Learning).
Lineær regression algoritmer forudsætter, at datatendenser følge en ret linje. Disse forudsætninger er ikke dårligt for nogle problemer, men på andre de sænkes nøjagtighed.

![Ikke-lineær klasse bounday][1]

***Ikke-lineær klasse grænse*** *-stole på en lineær klassificering algoritmen ville resultere i manglende nøjagtighed*

![Data med en ikke-lineære tendens][2]

***Data med en ikke-lineære tendens*** *-ved hjælp af en lineær regression metode ville generere meget større fejl end nødvendigt*

På trods af deres farer er lineær algoritmer meget populært som en første forsvarslinje. De har tendens til at være algorithmically nemt og hurtigt at træne.

### <a name="number-of-parameters"></a>Antallet af parametre

Parametre er knapperne en scientist data kommer til at slå, når du opretter en algoritme. De er tal, der har indflydelse på funktionen af den algoritme, som fejltolerance eller antallet af gentagelser eller indstillinger mellem varianter af hvordan algoritmen, der fungerer. Oplæringstid og nøjagtigheden af algoritmen, der kan undertiden være meget følsomme til at få de rigtige indstillinger. Normalt kræver algoritmer med store tal parametre de fleste forsøg og fejl til at finde en god kombination.

Alternativt, der er en [parameter hele](machine-learning-algorithm-parameters-optimize.md) modulet blok i Azure Machine Learning, der automatisk forsøger alle kombinationer af parametre ved uanset granularitet, du vælger. Det er en god måde at sikre, at du har samlet plads på den parameter, øger den tid, det tager at træne en model eksponentielt med antallet af parametre.

Spejlvendes er, typisk har mange parametre angiver, at en algoritme, som har større fleksibilitet. Det kan ofte opnå en god nøjagtighed. Du kan finde den rette kombination af parameterindstillinger er angivet.

### <a name="number-of-features"></a>Antallet af funktioner

For visse typer data, antallet af funktioner, der kan være meget store sammenlignet med antallet af datapunkter. Dette er ofte tilfældet med genetics eller tekstdata. Det store antal funktioner kan bog ned nogle learning algoritmer, så tid unfeasibly lang uddannelse. Understøttelse af Vector maskiner er yderst velegnede til denne sag (se nedenfor).

### <a name="special-cases"></a>Særlige tilfælde

Nogle learning algoritmer, der gør bestemte antagelser om strukturen af data eller de ønskede resultater. Hvis du kan finde en, der passer tilnærmer dine behov, kan det give dig mere brugbare resultater, mere præcise forudsigelser eller uddannelse hurtigere.

|**Algoritme**|**Nøjagtighed**|**Uddannelsestiden**|**Linearitet**|**Parametre**|**Noter**|
|---|:---:|:---:|:---:|:---:|---|
|**To klasse klassifikation**| | | | | |
|[logistisk regression](https://msdn.microsoft.com/library/azure/dn905994.aspx)                    | |●|●|5| |
|[beslutning skov](https://msdn.microsoft.com/library/azure/dn906008.aspx)|●|○| |6| |
|[beslutning jungle](https://msdn.microsoft.com/library/azure/dn905976.aspx)|●|○| |6|Lav hukommelse fodspor|
|[forstærket beslutningstræ](https://msdn.microsoft.com/library/azure/dn906025.aspx)|●|○| |6|Stor hukommelse|
|[neural netværk](https://msdn.microsoft.com/library/azure/dn905947.aspx)|●| | |9|[Yderligere tilpasning er mulig](http://go.microsoft.com/fwlink/?LinkId=402867)|
|[Gennemsnitlig perceptron](https://msdn.microsoft.com/library/azure/dn906036.aspx)|○|○|●|4| |
|[understøttelse af vector maskine](https://msdn.microsoft.com/library/azure/dn905835.aspx)| |○|●|5|God til store funktionssæt|
|[lokalt omfattende support vektor maskine](https://msdn.microsoft.com/library/azure/dn913070.aspx)|○| | |8|God til store funktionssæt|
|[Bayes' punkt maskine](https://msdn.microsoft.com/library/azure/dn905930.aspx)| |○|●|3| |
|**Flere klasse klassifikation**| | | | | |
|[logistisk regression](https://msdn.microsoft.com/library/azure/dn905853.aspx)| |●|●|5| |
|[beslutning skov](https://msdn.microsoft.com/library/azure/dn906015.aspx)|●|○| |6| |
|[beslutning jungle](https://msdn.microsoft.com/library/azure/dn905963.aspx)|●|○| |6|Lav hukommelse fodspor|
|[neural netværk](https://msdn.microsoft.com/library/azure/dn906030.aspx)|●| | |9|[Yderligere tilpasning er mulig](http://go.microsoft.com/fwlink/?LinkId=402867)|
|[en-v-all](https://msdn.microsoft.com/library/azure/dn905887.aspx)|-|-|-|-|Se egenskaberne for den valgte metode for to-klasse|
|**Regression**| | | | | |
|[Lineær](https://msdn.microsoft.com/library/azure/dn905978.aspx)| |●|●|4| |
|[Bayesian lineær](https://msdn.microsoft.com/library/azure/dn906022.aspx)| |○|●|2| |
|[beslutning skov](https://msdn.microsoft.com/library/azure/dn905862.aspx)|●|○| |6| |
|[forstærket beslutningstræ](https://msdn.microsoft.com/library/azure/dn905801.aspx)|●|○| |5|Stor hukommelse|
|[Hurtig skov fraktil](https://msdn.microsoft.com/library/azure/dn913093.aspx)|●|○| |9|Fordelinger i stedet for punkt forudsigelser|
|[neural netværk](https://msdn.microsoft.com/library/azure/dn905924.aspx)|●| | |9|[Yderligere tilpasning er mulig](http://go.microsoft.com/fwlink/?LinkId=402867)|
|[Poisson](https://msdn.microsoft.com/library/azure/dn905988.aspx)| | |●|5|Teknisk log-lineære. For at forudsige tæller|
|[ordenstal](https://msdn.microsoft.com/library/azure/dn906029.aspx)| | | |0|For at forudsige rang-bestilling|
|**Registrering af anomali**| | | | | |
|[understøttelse af vector maskine](https://msdn.microsoft.com/library/azure/dn913103.aspx)|○|○| |2|Især god til store funktionssæt|
|[PCA-baserede anomali registrering](https://msdn.microsoft.com/library/azure/dn913102.aspx)| |○|●|3| |
|[K-midler](https://msdn.microsoft.com/library/azure/5049a09b-bd90-4c4e-9b46-7c87e3a36810/)| |○|●|4|En algoritme, der er understøttelse af klynger|


**Egenskaber:**

**●** - viser stor nøjagtighed, hurtig uddannelse tider og brugen af linearitet

**○** - viser god nøjagtighed og moderat uddannelse tider

## <a name="algorithm-notes"></a>Algoritmen noter

### <a name="linear-regression"></a>Lineær regression

Som tidligere nævnt, tilpasser [lineær regression](https://msdn.microsoft.com/library/azure/dn905978.aspx) en linje (eller plan eller hyperplane) til datasættet. Det er en arbejdshesten, enkel og hurtig, men det kan være alt for simplistic for nogle problemer.
Se her for en [lineær regression selvstudium](machine-learning-linear-regression-in-azure.md).

![Data med en lineær tendens][3]

***Data med en lineær tendens***

### <a name="logistic-regression"></a>Logistisk regression

Selvom det indeholder forvirrende 'regression' i navnet, er logistisk regression faktisk et effektivt værktøj til [to klasse](https://msdn.microsoft.com/library/azure/dn905994.aspx) -og [multiclass](https://msdn.microsoft.com/library/azure/dn905853.aspx) . Det er hurtig og enkel. Den omstændighed, at det bruger en '-formet kurve i stedet for en lige linje gør det en fysisk egnet til at opdele dataene i grupper. Logistisk regression giver lineær klasse grænser, ske når du bruger det, en lineær tilnærmelse er noget, du kan leve med.

![Logistisk regression to klassedata med én funktion][4]

***En logistisk regression to klassedata med én funktion*** *-grænsen for klasse er det tidspunkt, hvor logistisk kurve er lige så tæt på begge klasser*

### <a name="trees-forests-and-jungles"></a>Træer, skove og jungles

Beslutning skove ([regression](https://msdn.microsoft.com/library/azure/dn905862.aspx), [to klasse](https://msdn.microsoft.com/library/azure/dn906008.aspx)og [multiclass](https://msdn.microsoft.com/library/azure/dn906015.aspx)), afgørelse jungles ([to-klasse](https://msdn.microsoft.com/library/azure/dn905976.aspx) og [multiclass](https://msdn.microsoft.com/library/azure/dn905963.aspx)) og øget beslutningstræer ([regression](https://msdn.microsoft.com/library/azure/dn905801.aspx) og [to klasse](https://msdn.microsoft.com/library/azure/dn906025.aspx)) er alle baseret på beslutningstræer, en tilegne sig en grundlæggende machine learning-konceptet. Der er mange varianter af beslutningstræer, men de alle gøre det samme – underopdele funktion pladsen i områder med overvejende samme etiket. Det kan være områder af samme art eller konstant, afhængigt af om du laver klassificering eller regression.

![Beslutningstræ underinddeler en funktion plads][5]

***Et beslutningstræ underinddeler en funktion plads i forskellige områder af nogenlunde ensartede værdier***

Funktionen kan kan inddeles i vilkårligt små områder, og det er nemt at forestille sig at opdele det fint nok for at have et datapunkt pr. område – et ekstremt eksempel på overfitting. For at undgå dette, en stor mængde træer er konstrueret med matematiske omhyggelig truffet, ikke mellem træerne. Gennemsnittet af "beslutning skoven" er et træ, der undgår overfitting. Beslutning skove kan bruge en masse hukommelse. Beslutning om jungles er en variant, der forbruger mindre hukommelse på bekostning af uddannelse lidt længere tid.

Forstærket beslutningstræer undgå overfitting ved at begrænse, hvor mange gange de kan underopdele og hvordan få datapunkter, der er tilladt i hver region. Algoritmen, oprettes der en række træer, hvoraf hver lærer at kompensere for den fejl, der er tilbage af træet før. Resultatet er en meget præcis deltageren, der har tendens til at bruge en masse hukommelse. Fuld teknisk beskrivelse, se [Friedmans oprindelige papir](http://www-stat.stanford.edu/~jhf/ftp/trebst.pdf).

[Hurtig skov fraktil regression](https://msdn.microsoft.com/library/azure/dn913093.aspx) er en variation af beslutningstræer til det særlige tilfælde, hvor du vil vide, ikke kun typiske () middelværdien af dataene i et område, men også dens udbredelse i form af quantiles.

### <a name="neural-networks-and-perceptrons"></a>Neural netværk og perceptrons

Neural netværk er hjerne-inspireret undervisning algoritmer, som dækker [multiclass](https://msdn.microsoft.com/library/azure/dn906030.aspx), [to klasse](https://msdn.microsoft.com/library/azure/dn905947.aspx)og [regression](https://msdn.microsoft.com/library/azure/dn905924.aspx) problemer. De findes i en uendelig lang, men neural elektricitetsnets Azure Machine Learning er alle i form af styret acykliske grafer. Det betyder, at inputfunktioner sendes frem (aldrig tilbage) gennem en række lag inden der er blevet konverteret til output. I hvert lag er input vægtes i forskellige kombinationer, opsummeres og videreformidles til det næste lag. Denne kombination af simple beregninger giver mulighed for at lære Avanceret klasse ruder og data tendenser tilsyneladende ved magic. Mange lag NET slags udføre det "dybe learning", som afføder så meget teknisk rapportering og science fiction.

Denne højtydende leveres ikke gratis, men. Neural netværk kan tage lang tid at træne, især for store datasæt med masser af funktioner. De har også flere parametre end de fleste algoritmer, hvilket betyder, at parameteren gadefejning udvider oplæringstiden megen.
Og for de overachievers, der ønsker at [angive deres egne netværksstruktur](http://go.microsoft.com/fwlink/?LinkId=402867), mulighederne er utrættelige.

<a name="boundaries-learned-by-neural-networks6"></a>![Grænser, lærte af neural netværk][6]
---------------------------

***Grænserne har lært af neural netværk kan være komplekse og uregelmæssige***

De [to klasse gennemsnit perceptron](https://msdn.microsoft.com/library/azure/dn906036.aspx) er skyrocketing uddannelse tider neural netværk svar. Det bruger et netværksstruktur, der giver lineær klasse grænser. Det er næsten primitive efter moderne standarder, men det har stor erfaring i arbejdet i robustly og er lille nok til hurtigt at lære.

### <a name="svms"></a>SVMs

Understøttelse af vector maskiner (SVMs), finde den grænse, der adskiller klasser af som bred en margen som muligt. Når to klasser ikke kan adskilles klart, finde algoritmerne kan de bedste grænsen. Som er skrevet på Azure Machine Learning, sker [to klasse SVM](https://msdn.microsoft.com/library/azure/dn905835.aspx) med en lige linje. (I SVM taler, det bruger en lineær kerne.) Da det gør denne lineære tilnærmelse, er den i stand til at køre ret hurtigt. Hvor det virkelig kommer til sin ret, er det med funktionen intens data som tekst eller genom. I disse tilfælde er SVMs i stand til at adskille klasser, hurtigere og med færre overfitting end de fleste andre algoritmer, ud over at kræve en mindre mængde hukommelse.

![Understøttelse af vector maskine klasse grænse][7]

***En typisk support vektor maskine klasse grænse maksimerer den margen, der adskiller to klasser***

Et andet produkt af Microsoft Research, [to klasse lokalt dyb SVM](https://msdn.microsoft.com/library/azure/dn913070.aspx) er en ikke-lineær variant af SVM, der bevarer de fleste af effektiviteten af den lineære version hastighed og hukommelse. Det er ideelt til tilfælde, hvor den lineære metode ikke giver tilstrækkelig præcise svar. Udviklerne holdt den fast ved at opdele problemet i en masse mindre lineære SVM problemer. Læse [fuld beskrivelse](http://research.microsoft.com/um/people/manik/pubs/Jose13.pdf) , oplysninger om, hvordan de hentes fra denne fremgangsmåde.

Ved hjælp af en intelligent udvidelse af ikke-lineære SVMs, tegner [en klasse SVM](https://msdn.microsoft.com/library/azure/dn913103.aspx) en ramme, der beskriver tæt hele datasættet. Det er nyttigt i forbindelse med registrering af anomali. Nye datamærker, der ligger langt uden for denne grænse er usædvanligt, at der er bemærkelsesværdige.

### <a name="bayesian-methods"></a>Bayesian-metoder

Bayesian metoder har en særdeles ønskværdige kvalitet: de undgå overfitting. Dette gøres ved visse antagelser på forhånd om den sandsynlige fordeling af svaret. En anden byproduct ved denne fremgangsmåde er, at de har meget få parametre. Azure Machine Learning har begge Bayesian algoritmer for både klassificering ([to klasse Bayes punkt maskine](https://msdn.microsoft.com/library/azure/dn905930.aspx)) og regression ([Bayesian lineær regression](https://msdn.microsoft.com/library/azure/dn906022.aspx)).
Bemærk, at dette forudsætter, at dataene kan opdele eller passer med en lige linje.

Bayes' punkt maskiner blev udviklet ved Microsoft Research på en historisk bemærkning. De har nogle usædvanligt smuk teoretiske arbejde bagved. Interesserede studerende ledes hen til den [oprindelige artikel i JMLR](http://jmlr.org/papers/volume1/herbrich01a/herbrich01a.pdf) og en [nyttige blog ved Christian løber](http://blogs.technet.com/b/machinelearning/archive/2014/10/30/embracing-uncertainty-probabilistic-inference.aspx).

### <a name="specialized-algorithms"></a>Særlige algoritmer

Hvis du har et meget specifikt mål, du kan være heldig. I samlingen Azure Machine Learning algoritmer, der er specialiseret i forudsigelse rang ([ordinal regressionsanalyse](https://msdn.microsoft.com/library/azure/dn906029.aspx)) er der, at tælle forudsigelse ([Poisson-regression](https://msdn.microsoft.com/library/azure/dn905988.aspx)) og anomali registrering (en baseret på [hovedkomponenter analyse](https://msdn.microsoft.com/library/azure/dn913102.aspx) og én baseret på s [understøtter vektor maskine](https://msdn.microsoft.com/library/azure/dn913103.aspx)).
Og der er en overskydende klyngedannelse algoritme samt ([K-midler](https://msdn.microsoft.com/library/azure/5049a09b-bd90-4c4e-9b46-7c87e3a36810/)).

![PCA-baserede anomali registrering][8]

***PCA-baserede anomali registrering*** *-langt størstedelen af dataene, der falder ind stereotypical distribution punkter afviger væsentligt fra, at fordelingen er tvivlsomme*

![Datasæt, der er grupperet ved hjælp af K-midler][9]

***Et datasæt er inddelt i 5 klynger ved hjælp af K-midler***

Der er også en ensemble [one-v-all multiclass klassificering](https://msdn.microsoft.com/library/azure/dn905887.aspx), som opdeler N klasse klassifikation problemet i N-1 to klasse klassifikation problemer. Præcision, oplæringstid og linearitet egenskaber bestemmes af bruges to klasse klassificeringerne.

![To klasse klassificeringer kombineres for at danne en tre-class-klassificering][10]

***Et par bestående af to klasse klassificeringer kombineres til en tre-class-klassificering***

Azure maskine undervisning også omfatter adgang til en effektiv maskine learning ramme under titlen på [Vowpal Wabbit](https://msdn.microsoft.com/library/azure/8383eb49-c0a3-45db-95c8-eb56a1fef5bf).
VW strider imod kategorisering her, da det kan lære både klassificering og regression problemer og kan endda lære af delvist uden etiket data. Du kan konfigurere det til at bruge en af en række læring algoritmer, tab af funktioner og optimering af algoritmer. Det blev udviklet fra bunden op til at være effektiv, parallelle og ekstremt hurtig. Den håndterer ridiculously store funktionssæt med lidt synlige.
Startet og ledet af Microsoft Researchs egen John Langford, er VW en formel én post i et felt af materiel bil algoritmer. Ikke alle problemer passer VW, men hvis det er tilfældet, kan det være værd at din mens til at forcere indlæringstiden på grænsefladen. Det er også tilgængelige som [enkeltstående åben kildekode](https://github.com/JohnLangford/vowpal_wabbit) på flere sprog.


<!-- Media -->

[1]: ./media/machine-learning-algorithm-choice/image1.png
[2]: ./media/machine-learning-algorithm-choice/image2.png
[3]: ./media/machine-learning-algorithm-choice/image3.png
[4]: ./media/machine-learning-algorithm-choice/image4.png
[5]: ./media/machine-learning-algorithm-choice/image5.png
[6]: ./media/machine-learning-algorithm-choice/image6.png
[7]: ./media/machine-learning-algorithm-choice/image7.png
[8]: ./media/machine-learning-algorithm-choice/image8.png
[9]: ./media/machine-learning-algorithm-choice/image9.png
[10]: ./media/machine-learning-algorithm-choice/image10.png
