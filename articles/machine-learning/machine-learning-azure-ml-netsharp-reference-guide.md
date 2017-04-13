<properties 
    pageTitle="Vejledning til redskaber # Neural netværk specifikation sproget | Microsoft Azure" 
    description="Syntaksen for den Nettooversigt # neural netværk specifikationssprog, sammen med eksempler på, hvordan du opretter en brugerdefineret neural netværksmodel i Microsoft Azure ML med redskaber#" 
    services="machine-learning" 
    documentationCenter="" 
    authors="jeannt" 
    manager="jhubbard" 
    editor="cgronlun"/>

<tags 
    ms.service="machine-learning" 
    ms.workload="data-services" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="09/12/2016" 
    ms.author="jeannt"/>



# <a name="guide-to-net-neural-network-specification-language-for-azure-machine-learning"></a>Vejledning til Nettooversigt # neural netværk specifikationssprog til Azure Machine Learning

## <a name="overview"></a>Oversigt
Nettooversigt # er et sprog, der er udviklet af Microsoft, der bruges til at definere neural netværk arkitekturer for neural netværk moduler i Microsoft Azure Machine Learning. I denne artikel, skal du se:  

-   Grundlæggende begreber, der er relateret til neural netværk
-   Netværkskrav til neural, og hvordan du definerer de primære komponenter
-   Syntaksen og nøgleord for Nettooversigt # specifikationssprog
-   Eksempler på brugerdefinerede neural netværk, der er oprettet ved hjælp af redskaber# 
    
[AZURE.INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)]  

## <a name="neural-network-basics"></a>Grundlæggende om neural netværk
En neural netværksstruktur består af ***noder*** , der er organiseret i ***lag***, og vægtet ***forbindelser*** (eller ***kanter***) mellem noderne. Forbindelserne er retningsbestemte, og hver forbindelse har en ***kilde*** node og en ***destination*** node.  

Hver ***trainable lag*** (en skjult eller en output layer) indeholder en eller flere ***forbindelse pakker***. En forbindelse samlet består af et kildelag og en specifikation af forbindelser fra denne kilde lag. Alle forbindelser i en given samlet dele den samme ***kilde lag*** og det samme ***destination lag***. I Nettooversigt # betragtes som en forbindelse samlet som tilhører de samlet destination lag.  
 
Nettooversigt # understøtter forskellige typer af forbindelse pakker, hvor du kan tilpasse de måde, som input er knyttet til skjulte lag og knyttet til output.   

Standard- eller standard samlet er en **fuld samlet**, hvor hver node i kildelaget er forbundet til hver node i destination lag.  

Desuden understøtter redskaber # følgende fire typer af avanceret forbindelse pakker:  

-   **Filtreret pakker**. Brugeren kan angive et prædikat ved hjælp af placeringen af noden kilde lag og noden destination lag. Noder er forbundet, når prædikatet er sand.
-   **Convolutional pakker**. Brugeren kan angive small omgivelser af knuder i kildelaget. Hver node i destinationslaget er forbundet til en computere af knuder på kilde lag.
-   **Gruppering af pakker** og **svar normalisering pakker**. Disse ligner convolutional pakker i, at brugeren, der definerer small omgivelser af knuder i kildelaget. Forskellen er, tykkelser af kanterne i disse pakker ikke er trainable. En foruddefineret funktion anvendes i stedet node kildeværdierne til at afgøre værdien destination node.  

Bruge redskaber # til at definere strukturen i et neural netværk gør det muligt at definere komplekse strukturer som dybe neural netværk eller convolutions af vilkårlig dimensioner, som er blevet konstateret, at forbedre learning for de data, som billede, lyd eller video.  

## <a name="supported-customizations"></a>Understøttede tilpasninger
Arkitekturen i neural netværk modeller, du opretter i Azure Machine Learning kan tilpasses stort omfang ved hjælp af redskaber #. Du kan:  

-   Oprette skjulte lag og styre antallet af knuder på hvert lag.
-   Angiv, hvordan lag skal være forbundet med hinanden.
-   Definere speciel connectivity strukturer, som convolutions og tykkelse deling pakker.
-   Angiv forskellige aktivering funktioner.  

Du kan finde oplysninger om syntaksen specifikation sprog skal [Struktur specifikation](#Structure-specifications).  
 
Du kan finde eksempler på definerer neural netværk for nogle almindelige computer læ opgaver fra simpleks til komplekse, [eksempler](#Examples-of-Net#-usage).  

## <a name="general-requirements"></a>Generelle krav
-   Der skal være ét output lag, mindst ét input lag og nul eller flere skjulte lag. 
-   Hvert lag, har et fast antal noder, objekter er arrangeret i en rektangulær matrix af vilkårlig dimensioner. 
-   Indtast lag har nogen tilknyttede erfaren parametre og repræsenterer det punkt, hvor Forekomstdata indtaster netværket. 
-   Trainable lag (skjulte- og outputområder lagene) har tilknyttede erfaren parametre, kaldet tykkelser og systematiske fejl. 
-   Kilde- og destinationstabellerne knuderne skal være i separate lag. 
-   Forbindelser skal være acykliske; Det vil sige, kan der være en kæde af forbindelser, der fører tilbage til den oprindelige kilde node.
-   Output laget må ikke være en kilde portion samlet en forbindelse.  

## <a name="structure-specifications"></a>Specifikation af struktur
En neural netværk struktur specifikation består af tre sektioner: **erklæringen konstant**, **lag definition**, **forbindelse definition**. Der er også en valgfri **dele definition** sektion. Du kan angive sektionerne i en hvilken som helst rækkefølge.  

## <a name="constant-declaration"></a>Erklæringen konstant 
Erklæringen af en konstant er valgfrit. Det giver mulighed for at definere værdier, der bruges et andet sted i definitionen af neural netværk. Sætningen definition består af et id, efterfulgt af et lighedstegn (=) og en værdiudtryk.   

Den følgende sætning definerer for eksempel en konstant **x**:  


    Const X = 28;  

Omslutte id-navnene og værdier med klammeparentes, for at definere to eller flere konstanter samtidigt, og Adskil dem med semikolon. Eksempel:  

    Const { X = 28; Y = 4; }  

I højre side af hver tildelingsudtryk kan være et heltal, et reelt tal, en boolesk værdi (SAND eller FALSK) eller et matematiske udtryk. Eksempel:  

    Const { X = 17 * 2; Y = true; }  

## <a name="layer-declaration"></a>Definition af lag
Der kræves lag erklæring. Den definerer størrelse og kilde for det lag, herunder forbindelse pakker og attributter. Sætningen definition starter med navnet på det lag (input, skjulte eller output), efterfulgt af målangivelserne af lag (en tupel positivt heltal). Eksempel:  

    input Data auto;
    hidden Hidden[5,20] from Data all;
    output Result[2] from Hidden all;  

-   Produktet af dimensionerne er antallet af knuder på lag. I dette eksempel er der to dimensioner [5,20], hvilket betyder, at der er 100 noder i lag.
-   Lag, kan defineres i en hvilken som helst rækkefølge med én undtagelse: Hvis mere end ét input lag er defineret, den rækkefølge, hvori de er defineret skal svare til rækkefølgen af funktioner i inputdataene.  


Brug nøgleordet **automatisk** til at angive, at antallet af knuder på et lag bestemmes automatisk. Nøgleordet **automatisk** har forskellige effekter, afhængigt af lag:  

-   I en input lag opgørelse er antallet af knuder antallet af funktioner i inputdataene.
-   I en skjult lag erklæring er antallet af knuder det tal, der er angivet efter parameterværdien for **antallet af knuder, skjulte**. 
-   I en output lag opgørelse er antallet af knuder 2 til to klasse klassificering, 1 til regression og lig med antallet af knuder output til multiclass klassificering.   

Følgende netværk definitionen kan for eksempel størrelsen på alle lag, du vil bestemmes automatisk:  

    input Data auto;
    hidden Hidden auto from Data all;
    output Result auto from Hidden all;  


Et lag definition for et trainable lag (skjulte eller outputområder-lag) kan du kan også medtage output funktionen (også kaldet en aktivering funktion), som standard er **sigmoid** til klassifikation modeller og **lineær** regression modeller. (Også selvom du bruger standard, kan du eksplicit angive funktionen aktivering, hvis det er nødvendigt for klarhed.)

De følgende output-funktioner understøttes:  

-   sigmoid
-   Lineær
-   softmax
-   rlinear
-   firkant
-   KVROD
-   srlinear
-   ABS
-   TANH 
-   brlinear  

For eksempel bruger følgende erklæring funktionen **softmax** :  

    output Result [100] softmax from Hidden all;  

## <a name="connection-declaration"></a>Forbindelse erklæring
Umiddelbart efter definerer det trainable lag, skal du angive forbindelser mellem de lag, du har defineret. Angivelsen forbindelse samlet starter med nøgleord **fra**, efterfulgt af navnet på den samlet kilde lag og typen forbindelse samlet til at oprette.   

I øjeblikket er understøttes fem typer forbindelse pakker:  

-   **Fuld** pakker, angivet med nøgleord **alle**
-   **Filtreret** pakker, angivet med den nøgleord **hvor**, efterfulgt af et prædikat udtryk
-   **Convolutional** pakker, angivet med den nøgleord **convolve**, efterfulgt af convolution attributterne
-   **Pooling** pakker, angivet med nøgleord **Maks puljen** eller **betyder puljen**
-   **Svar normalisering** pakker, angivet med nøgleord **svar normen**      

## <a name="full-bundles"></a>Fuld pakker  

En fuld forbindelse samlet omfatter en forbindelse fra hver node i kildelaget til hver node i destination lag. Dette er typen af standard netværksforbindelse.  

## <a name="filtered-bundles"></a>Filtrerede pakker
En filtreret forbindelse samlet specifikation omfatter en prædikat, udtrykt syntaktisk meget som et C# lambda udtryk. I følgende eksempel definerer to filtrerede pakker:  

    input Pixels [10, 20];
    hidden ByRow[10, 12] from Pixels where (s,d) => s[0] == d[0];
    hidden ByCol[5, 20] from Pixels where (s,d) => abs(s[1] - d[1]) <= 1;  

-   **S** er en parameter, der repræsenterer et indeks i matrixen rektangulært af noder input laget, _pixel_i prædikatet for _ByRow_og **d** er en parameter, der repræsenterer et indeks i matrixen af knuder på det skjulte lag, _ByRow_. Typen af både **s** og **d** er en tupel med heltal længde to. Objekter **s** områder over alle par af heltal med _0 < = s [0] < 10_ og _0 < = s[1] < 20_, og **d** intervaller over alle par af heltal, med _0 < = d [0] < 10_ og _0 < = d[1] < 12_. 
-   I højre side af prædikat udtrykket er der en betingelse. I dette eksempel for hver værdi af **s** og **d** så betingelsen er sand, er der en kant fra noden kilde lag til noden destination lag. Denne filterudtryk angiver således, at samlet omfatter en forbindelse fra noden defineret af **s** til noden defineret af **d** i alle tilfælde, hvor s [0] er lig med d [0].  

Du kan også angive et sæt af vægte for en filtreret pakke. Værdien for attributten **tykkelser** skal være en tupel flydende værdier med en længde, der svarer til antallet af forbindelser, der er defineret af samlet. Som standard oprettes tilfældigt tykkelser.  

Vægtværdier er grupperet efter destination Nodeindekset. Det vil sige, hvis den første destination node er forbundet til K kilde noder, er de første _K_ elementer i tuplen **tykkelser** tykkelser til den første destination node i kilde indeks rækkefølge. Det samme gælder for de resterende destination noder.  

Det er muligt at angive tykkelser direkte som konstante værdier. Eksempelvis hvis du har lært vægt tidligere, kan du angive dem som konstanter ved hjælp af følgende syntaks:

    const Weights_1 = [0.0188045055, 0.130500451, ...]


## <a name="convolutional-bundles"></a>Convolutional pakker
Når kursus data har en ensartet struktur, anvendes ofte convolutional forbindelser til at få mere at vide på højt niveau funktioner i dataene. For eksempel på billede, lyd eller video data, geografiske eller tidsmæssig dimensionalitet kan være ret ensartet.  

Convolutional pakker anvende rektangulære **kerner** , der er slid gennem dimensionerne. Grundlæggende, hver kernen definerer et sæt af tykkelser, der er anvendt i lokale omgivelser kaldes **kernen programmer**. Hvert kernen program svarer til en node i kilde-lag, som er anført som **central node**. Tykkelser af en kerne deles mange forbindelser. I en convolutional samlet hver kerne er rektangulært og alle kernen programmer har samme størrelse.  

Convolutional pakker understøtter følgende attributter:

**InputShape** definerer dimensionerne for kildelaget med henblik på denne convolutional samlet. Værdien skal være en tupel med positivt heltal. Produktet af heltallene skal være lig med antallet af knuder i kildelaget, men ellers det nødvendigt ikke at matche dimensionalitet erklæret for kilde lag. Længden af denne tupel bliver **aritet** værdien for den convolutional samlet. (Typisk aritet refererer til antallet af argumenter eller operander, der kan tage en funktion.)  

Brug attributterne, **KernelShape**, **Trinvis**, **indre margen**, **LowerPad**og **UpperPad**til at angive figur og placeringen af kerner:   

-   **KernelShape**: (obligatorisk) definerer dimensionerne for hver kernen i convolutional samlet. Værdien skal være en tupel med positivt heltal med en længde, der er lig med aritet for samlet. Hver komponent i denne tupel må ikke være større end den tilsvarende komponent i **InputShape**. 
-   **Trinvis**: (valgfrit) definerer skyderen trin størrelsen på convolution (ét trin størrelse for hver dimension), der er afstanden mellem noderne central. Værdien skal være en tupel med positivt heltal med en længde, der er aritet for samlet. Hver komponent i denne tupel må ikke være større end den tilsvarende komponent i **KernelShape**. Standardværdien er en tupel med alle komponenter, der er lig med en. 
-   **Deling**: (valgfrit) definerer den vægt, deling for hver dimension i convolution. Værdien, der kan være en enkelt boolesk værdi eller en tupel booleske værdier med en længde, der er aritet for samlet. En boolesk enkeltværdi udvidet til at være en tupel korrekt længde med alle komponenter, der er lig med den angivne værdi. Standardværdien er en tupel, der består af alle sande værdier. 
-   **MapCount**: (valgfrit) definerer antallet af funktion maps til convolutional samlet. Værdien, der kan være et enkelt positivt heltal eller en tupel med positivt heltal med en længde, der er aritet for samlet. En enkelt heltalsværdi udvidet til at være en tupel korrekt længde med de første komponenter, der er lig med den angivne værdi og alle de resterende komponenter, der er lig med en. Standardværdien er et. Det samlede antal funktion kort er produktet af komponenterne i tuplen. Factoring af denne samlede antal på tværs af komponenterne bestemmer, hvordan funktionen knytte værdierne grupperes i noderne destination. 
-   **Tykkelser**: (valgfrit) definerer de indledende tykkelser for samlet. Værdien skal være en tupel af flydende punkt værdier med en længde, der er antallet af gange, kerner antallet af tykkelser per kernen, som defineret senere i denne artikel. Standard tykkelser genereres tilfældigt.  

Der er to sæt af egenskaber, der bestemmer indre margen, de egenskaber, der udelukker:

-   **Indre margen**: (valgfrit) bestemmer om input skal være foret ved hjælp af et **standard indre margen farveskema**. Værdien kan være en enkelt boolesk værdi, eller det kan være en tupel booleske værdier med en længde, der er aritet for samlet. En boolesk enkeltværdi udvidet til at være en tupel korrekt længde med alle komponenter, der er lig med den angivne værdi. Hvis værdien for en dimension er sand, foret kilden logisk i denne dimension med nul værdier celler til at understøtte flere kernen programmer, så de centrale af de første og sidste kerner i denne dimension er de første og sidste noder i denne dimension i kildelaget. Dermed, antallet af "dummy" knuder i hver dimension bestemmes automatisk, så den passer nøjagtigt _(InputShape [d] - 1) / trinvis [d] + 1_ kerner til kildelaget polstret. Hvis værdien for en dimension er falsk, defineres kerner, så antallet af knuder på hver side, der er udeladt, er den samme (op til en forskel på 1). Standardværdien for denne attribut er en tupel med alle komponenter, der er lig med falsk.
-   **UpperPad** og **LowerPad**: (valgfrit) Angiv større kontrol over mængden af udfyldning til brug. **Vigtige:** Følgende attributter kan defineres, men kun hvis egenskaben **udfyldning** ovenfor er ***ikke*** defineret. Værdierne, der skal være heltal værdier tupler med længde, der er aritet af samlet. Når følgende attributter er angivet, føjes "dummy" noder til nedre og øvre enderne af hver dimension af input lag. Antallet af knuder, der er føjet til de nedre og øvre ender i hver dimension bestemmes af **LowerPad**[i] og **UpperPad**[i] henholdsvis. For at sikre, at kerner svarer kun til "reelle" noder og ikke til "dummy" noder, skal være opfyldt følgende betingelser:
    -   Hver komponent i **LowerPad** skal være absolut mindre end KernelShape [d] / 2. 
    -   Hver komponent i **UpperPad** må ikke være større end KernelShape [d] / 2. 
    -   Standardværdien for følgende attributter er en tupel med alle komponenter, der er lig med 0. 

Indstillingen **udfyldning** = SAND gør det muligt for den ønskede indre margen, som er nødvendig for at bevare "midte" kernen i "reelle" input. Dette ændrer matematisk lidt til databehandling outputstørrelse. Generelt outputstørrelsen _D_ beregnes som _D = (I - K) / S + 1_, hvor _jeg_ er input størrelsen _K_ er kernen størrelsen, _S_ er trinnet, og _/_ er heltalsdivision (Afrund mod nul). Hvis du angiver UpperPad = [1, 1] input størrelsen _jeg_ er effektivt 29, og dermed _D = (29-5) / 2 + 1 = 13_. Men, når **udfyldning** = true, grundlæggende _jeg_ får frifaldssensor ved _K - 1_; derfor _D = ((28 + 4) - 5) / 2 + 1 = 27 / 2 + 1 = 13 + 1 = 14_. Du kan få bedre kontrol over udfyldningen end = true, hvis du lige har angivet **udfyldning** ved at angive værdier for **UpperPad** og **LowerPad** .

Få flere oplysninger om convolutional netværk og deres programmer i disse artikler:  

-   [http://deeplearning.NET/Tutorial/lenet.HTML](http://deeplearning.net/tutorial/lenet.html )
-   [http://Research.Microsoft.com/pubs/68920/icdar03.PDF](http://research.microsoft.com/pubs/68920/icdar03.pdf) 
-   [http://People.csail.mit.edu/jvb/Papers/cnn_tutorial.PDF](http://people.csail.mit.edu/jvb/papers/cnn_tutorial.pdf)  

## <a name="pooling-bundles"></a>Gruppering af pakker
En **gruppering af samlet** gælder geometri ligner convolutional connectivity, men den bruger foruddefinerede funktioner til node kildeværdierne for at udlede værdien destination node. Det vil sige, gruppering af forbindelser pakker har ingen trainable tilstand (tykkelser eller systematiske fejl). Gruppering af forbindelser pakker understøtter alle de attributter, der er convolutional undtagen **Deling**, **MapCount**og **tykkelser**.  

Typisk overlapper kerner opsummeret ved tilstødende gruppering af forbindelser enheder ikke. Hvis trin [d] er lig med KernelShape [d] i hver dimension, er det lag, der fås det traditionelle lokale gruppering af forbindelser lag, som ofte er ansat i convolutional neural netværk. Hver destination node beregner det maksimale antal eller middelværdien af dens kernen i kildelaget aktiviteter.  

Det følgende eksempel viser en gruppering af forbindelser samlet: 

    hidden P1 [5, 12, 12]
      from C1 max pool {
        InputShape  = [ 5, 24, 24];
        KernelShape = [ 1,  2,  2];
        Stride      = [ 1,  2,  2];
      }  

-   Aritet for samlet er 3 (længden af tupler **InputShape**, **KernelShape**og **Trinvis**). 
-   Antallet af knuder på kildelaget er _5 *24* 24 = 2880_. 
-   Dette er en traditionel lokale gruppering af forbindelser lag, fordi **KernelShape** og **Trinvis** er lig med. 
-   Antallet af knuder på destinationslaget er _5 *12* 12 = 1440_.  
    
Få flere oplysninger om gruppering af forbindelser lag, i disse artikler:  

-   [http://www.CS.Toronto.edu/~hinton/absps/imagenet.PDF](http://www.cs.toronto.edu/~hinton/absps/imagenet.pdf) (Sektion 3.4)
-   [http://CS.nyu.edu/~koray/publis/lecun-iscas-10.PDF](http://cs.nyu.edu/~koray/publis/lecun-iscas-10.pdf) 
-   [http://CS.nyu.edu/~koray/publis/jarrett-iccv-09.PDF](http://cs.nyu.edu/~koray/publis/jarrett-iccv-09.pdf)
    
## <a name="response-normalization-bundles"></a>Svar normalisering pakker
**Svar normalisering** er et lokale normalisering farveskema, der blev introduceret først ved Geoffrey Hinton et al, i papiret [ImageNet Classiﬁcation med dybe Convolutional Neural netværk](http://www.cs.toronto.edu/~hinton/absps/imagenet.pdf). Svar normalisering bruges til at støtte generalisering i neural redskaber. Når en neuron kraft på en meget høj aktivering niveau, undertrykker et lokale svar normalisering lag niveauet for aktivering af de omgivende neurons. Dette gøres ved hjælp af tre parametre (***α***, ***β***og ***k***) og en convolutional struktur (eller computere figur). Hver neuron i destinationslaget ***y*** svarer til en neuron ***x*** i kilde-lag. Niveauet for aktivering af ***y*** er givet ved følgende formel, hvor ***f*** er niveauet for aktivering af en neuron, og ***Nx*** er kernen (eller det sæt, der indeholder neurons i computere af ***x***), som defineret i den følgende convolutional struktur:  

![][1]  

Svar normalisering pakker understøtter alle de attributter, der er convolutional undtagen **Deling**, **MapCount**og **tykkelser**.  
 
-   Hvis kernen indeholder neurons i det samme kort som ***x***, kaldes normalisering farveskema som **samme kort normalisering**. For at definere samme kort normalisering, skal den første koordinere i **InputShape** har værdien 1.
-   Hvis kernen indeholder neurons i den samme geografiske placering som ***x***, men neurons er i andre kort, kaldes normalisering farveskema **på tværs af kort normalisering**. Denne type svar normalisering implementerer en slags sideretningen men inspireret af den type, findes i reelle neurons oprette konkurrence for stor aktivering niveauer blandt neuron output beregnes på forskellige kort. For at definere på tværs af kort normalisering, den første koordinere skal være et heltal, der er større end, og ikke er større end antallet af kort, og resten af koordinaterne skal have værdien 1.  

Eftersom svar normalisering pakker anvende en foruddefineret funktion til node kildeværdierne til at afgøre værdien destination node, har de ingen trainable tilstand (tykkelser eller systematiske fejl).   

**Besked**: knuderne i destinationslaget svarer til neurons, der er kerner central noder. Eksempelvis hvis KernelShape [d] er ulige, derefter _KernelShape [d] / 2_ svarer til den centrale kerne node. Hvis _KernelShape [d]_ er lige, noden central er på _KernelShape [d] / 2-1_. Derfor, hvis **udfyldning**[d] er falsk, først og sidste _KernelShape [d] / 2_ noder ikke har tilsvarende noder i destination lag. For at undgå denne situation, definere **udfyldning** som [true, SAND,..., Sand].  

Ud over de fire attributter, der er beskrevet tidligere, understøtter svar normalisering pakker også følgende attributter:  

-   **Alpha**: (obligatorisk) angiver en flydende værdi, der svarer til ***α*** i den foregående formel. 
-   **Beta**: (obligatorisk) angiver en flydende værdi, der svarer til ***β*** i den foregående formel. 
-   **Forskydning**: (valgfrit) angiver en flydende værdi, der svarer til ***k*** i den foregående formel. Som standard 1.  

I følgende eksempel definerer et svar normalisering samlet, ved hjælp af følgende attributter:  

    hidden RN1 [5, 10, 10]
      from P1 response norm {
        InputShape  = [ 5, 12, 12];
        KernelShape = [ 1,  3,  3];
        Alpha = 0.001;
        Beta = 0.75;
      }  

-   Kilde lag indeholder fem kort, hver med aof dimension af 12 x 12, alt i 1440 noder. 
-   Værdien af **KernelShape** angiver, at dette er et samme kort normalisering lag, hvor Bynavn er et 3 x 3 rektangel. 
-   Standardværdien for **udfyldning** er falsk, og som derfor destination lag indeholder kun 10 noder i hver dimension. For at medtage en node i det destination lag, der svarer til noderne i kilde-lag, skal du tilføje indre margen = [SAND; SAND; SAND]; og ændre størrelsen på RN1 [5, 12, 12].  

## <a name="share-declaration"></a>Dele erklæring 
Nettooversigt # understøtter eventuelt definerer flere pakker med delte tykkelser. Tykkelser af enhver to pakker kan deles, hvis deres strukturer er den samme. Følgende syntaks definerer pakker med delte tykkelser:  

    share-declaration:
        share    {    layer-list    }
        share    {    bundle-list    }
       share    {    bias-list    }
    
    layer-list:
        layer-name    ,    layer-name
        layer-list    ,    layer-name
    
    bundle-list:
       bundle-spec    ,    bundle-spec
        bundle-list    ,    bundle-spec
    
    bundle-spec:
       layer-name    =>     layer-name
    
    bias-list:
        bias-spec    ,    bias-spec
        bias-list    ,    bias-spec
    
    bias-spec:
        1    =>    layer-name
    
    layer-name:
        identifier  

For eksempel angiver følgende del-erklæring lagnavne, der angiver, at der skal deles både tykkelser og systematiske fejl:  

    Const {
      InputSize = 37;
      HiddenSize = 50;
    }
    input {
      Data1 [InputSize];
      Data2 [InputSize];
    }
    hidden {
      H1 [HiddenSize] from Data1 all;
      H2 [HiddenSize] from Data2 all;
    }
    output Result [2] {
      from H1 all;
      from H2 all;
    }
    share { H1, H2 } // share both weights and biases  

-   Funktionerne input er opdelt i to lige store input lag. 
-   De skjulte lag skal du beregne højere niveau funktioner på de to input lag. 
-   Del-erklæring angiver, at _H1_ og _H2_ skal beregnes på samme måde fra deres respektive input.  
 
Du kan også kan dette angives med to separate del-erklæringer på følgende måde:  

    share { Data1 => H1, Data2 => H2 } // share weights  

<!-- -->

    share { 1 => H1, 1 => H2 } // share biases  

Du kan bruge den korte formular kun, når lag indeholder en enkelt samlet. Generelt er muligt at dele kun, når den relevante struktur er identiske, hvilket betyder, at de har samme størrelse, samme convolutional geometri og så videre.  

## <a name="examples-of-net-usage"></a>Eksempler på nettet # brugen
Dette afsnit indeholder nogle eksempler på, hvordan du kan bruge redskaber # tilføje skjulte lag, skal du angive den måde, skjulte lag interagere med andre lag, og opbygge convolutional netværk.   

### <a name="define-a-simple-custom-neural-network-hello-world-example"></a>Angive et enkelt brugerdefineret neural netværk: "Hej" eksempel
Denne enkle eksempel viser, hvordan du opretter en neural netværksmodel, der indeholder et enkelt skjulte lag.  

    input Data auto;
    hidden H [200] from Data all;
    output Out [10] sigmoid from H all;  

Eksemplet illustrerer nogle grundlæggende kommandoer på følgende måde:  

-   Den første linje definerer input laget (kaldet _Data_). Når du bruger nøgleordet **automatisk** , medtager neural netværket automatisk alle funktion kolonner i eksemplerne input. 
-   Den anden linje opretter det skjulte lag. Navnet _H_ er tildelt til det skjulte lag, som har 200 noder. Dette lag er fuldt forbundet til det input lag.
-   Den tredje linje definerer det output lag (kaldet _O_), som indeholder 10 output noder. Hvis neural netværket bruges til klassificering, er der en output node per klasse. Nøgleordet **sigmoid** angiver, at funktionen output anvendes til output lag.   

### <a name="define-multiple-hidden-layers-computer-vision-example"></a>Definere flere skjulte lag: computer syn eksempel
I følgende eksempel viser, hvordan du definerer et lidt mere komplekse neural netværk med flere brugerdefinerede skjulte lag.  

    // Define the input layers 
    input Pixels [10, 20];
    input MetaData [7];
    
    // Define the first two hidden layers, using data only from the Pixels input
    hidden ByRow [10, 12] from Pixels where (s,d) => s[0] == d[0];
    hidden ByCol [5, 20] from Pixels where (s,d) => abs(s[1] - d[1]) <= 1;
    
    // Define the third hidden layer, which uses as source the hidden layers ByRow and ByCol
    hidden Gather [100] 
    {
      from ByRow all;
      from ByCol all;
    }
    
    // Define the output layer and its sources
    output Result [10]  
    {
      from Gather all;
      from MetaData all;
    }  

I dette eksempel vises flere funktioner i neural netværk specifikationssprog:  

-   Strukturen har to input lag, _pixel_ og _MetaData_.
-   _Pixel_ laget er en kildelag til to forbindelse-pakker, med destination lag, _ByRow_ og _ByCol_.
-   Lag, _samle_ og _resultatet_ er destination lag i flere forbindelse pakker.
-   Output laget, _resultatet_er et destination lag i to forbindelse pakker; en med det andet niveau skjulte (samle) som en destination lag, og den anden med input laget (MetaData) som en destination lag.
-   De skjulte lag, _ByRow_ og _ByCol_, Angiv filtrerede forbindelse ved hjælp af prædikat udtryk. Mere præcist node i _ByRow_ på [x, y] er forbundet med knuderne i _pixel_ , der har den første indeks koordinere lig med den node første koordinere, x. På samme måde, noden i _ByCol på [x, y] er forbundet med knuderne i _pixel_ , der har det andet indeks koordinere inden for et af den node anden koordinere, y.  

### <a name="define-a-convolutional-network-for-multiclass-classification-digit-recognition-example"></a>Angive et convolutional netværk for multiclass klassifikation: ciffer talegenkendelse eksempel
Definitionen af følgende netværket er udviklet til at genkende tal, og den illustrerer nogle avancerede teknikker til at tilpasse et neural netværk.  

    input Image [29, 29];
    hidden Conv1 [5, 13, 13] from Image convolve 
    {
       InputShape  = [29, 29];
       KernelShape = [ 5,  5];
       Stride      = [ 2,  2];
       MapCount    = 5;
    }
    hidden Conv2 [50, 5, 5]
    from Conv1 convolve 
    {
       InputShape  = [ 5, 13, 13];
       KernelShape = [ 1,  5,  5];
       Stride      = [ 1,  2,  2];
       Sharing     = [false, true, true];
       MapCount    = 10;
    }
    hidden Hid3 [100] from Conv2 all;
    output Digit [10] from Hid3 all;  


-   Strukturen indeholder et enkelt input lag, _billede_.
-   Nøgleord **convolve** angiver, at de lag, med navnet _Conv1_ og _Conv2_ er convolutional lag. Hver af disse lag erklæringer efterfølges af en liste over convolution attributter.
-   Nettet har en tredje skjult lag, _Hid3_, som er fuldt forbundet med den anden skjult lag, _Conv2_.
-   Output lag, _ciffer_, er forbundet kun for den tredje skjult lag, _Hid3_. Nøgleord indikerer **alle** , at output lag fuldt er forbundet med _Hid3_.
-   Aritet for convolution er tre (længden af tupler **InputShape**, **KernelShape**, **Trinvis**og **Deling**). 
-   Antallet af tykkelser per kernen er _1 + **KernelShape**\[0] * * *KernelShape**\[1]* **KernelShape**\[2] = 1 + 1 *5* 5 = 26. Eller 26 * 50 = 1300_.
-   Du kan beregne knuderne i hvert lag, der er skjulte på følgende måde:
    -   **NodeCount**\[0] = (5 - 1) / 1 + 1 = 5.
    -   **NodeCount**\[1] = (13-5) / 2 + 1 = 5. 
    -   **NodeCount**\[2] = (13-5) / 2 + 1 = 5. 
-   Det samlede antal noder kan beregnes ved hjælp af den angivne dimensionerne for lag, [50, 5, 5], på følgende måde: _ **MapCount** * * *NodeCount**\[0]* **NodeCount**\[1] * * *NodeCount**\[2] = 10* 5 *5* 5_
-   Da **Deling**[d] er falsk kun for _d == 0_, antallet af kerner er _ **MapCount** * * *NodeCount**\[0] = 10* 5 = 50_. 


## <a name="acknowledgements"></a>Godkendelser

Nettooversigt #-sprog til tilpasning af arkitekturen i neural netværk er udviklet hos Microsoft af Shon Katzenberger (Architect, Machine Learning) og Alexey Kamenev (Software engineering, Microsoft Research). Det bruges internt til machine learning-projekter og lige fra billede registrering til tekst analytics-programmer. Du kan finde flere oplysninger, se [Neural redskaber i Azure ML - Introduktion til redskaber #](http://blogs.technet.com/b/machinelearning/archive/2015/02/16/neural-nets-in-azure-ml-introduction-to-net.aspx)


[1]:./media/machine-learning-azure-ml-netsharp-reference-guide/formula_large.gif
 
