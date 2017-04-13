<properties
    pageTitle="Køre Hadoop MapReduce eksempler på Linux-baserede HDInsight | Microsoft Azure"
    description="Introduktion til brug af MapReduce eksempler med Linux-baserede HDInsight. Bruge SSH til at oprette forbindelse til klyngen og derefter bruge kommandoen Hadoop til at køre eksempel job."
    services="hdinsight"
    documentationCenter=""
    authors="Blackmist"
    manager="jhubbard"
    editor="cgronlun"
    tags="azure-portal"/>

<tags
    ms.service="hdinsight"
    ms.workload="big-data"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/27/2016"
    ms.author="larryfr"/>




#<a name="run-the-hadoop-samples-in-hdinsight"></a>Køre Hadoop eksemplerne i HDInsight

[AZURE.INCLUDE [samples-selector](../../includes/hdinsight-run-samples-selector.md)]

Linux-baserede HDInsight klynger indeholder et sæt af MapReduce eksempler, der kan bruges til at blive fortrolig med kører Hadoop MapReduce job. I dette dokument, kan du få mere at vide om de tilgængelige prøver og gennemgå kører nogle af dem.

##<a name="prerequisites"></a>Forudsætninger

- **En Azure-abonnement**: se [få Azure gratis prøveversion](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/)

- **A Linux-baserede HDInsight klynge**: se [Introduktion til brug af Hadoop med Hive i HDInsight på Linux](hdinsight-hadoop-linux-tutorial-get-started.md)

- **En SSH klient**: oplysninger om brug af SSH med HDInsight, skal du se følgende artikler:

    - [Bruge SSH med Linux-baserede Hadoop på HDInsight fra Linux, Unix eller OS X](hdinsight-hadoop-linux-use-ssh-unix.md)

    - [Bruge SSH med Linux-baserede Hadoop på HDInsight fra Windows](hdinsight-hadoop-linux-use-ssh-windows.md)

## <a name="the-samples"></a>Eksemplerne ##

**Placering**: Eksemplerne er placeret på HDInsight klyngen på **/usr/hdp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar**

**Indhold**: I følgende eksempler er indeholdt i dette arkiv:

- **aggregatewordcount**: en samling baseret kort/reducere program, der tæller ordene i input-filer
- **aggregatewordhist**: en samling baseret kort/reducere program, der beregner histogrammet af ord i input-filer
- **bbp**: et kort/reducere program, der bruger Bailey-Borwein-Plouffe til at beregne nøjagtige cifre af Pi
- **dbcount**: en eksempel-job, der tæller loggene pageview, der er gemt i en en database
- **distbbp**: et kort/reducere program, der anvender en BBP type formel til at beregne nøjagtige bit af Pi
- **GREP**: et kort/reducere program, der tæller forekomster af et regex i input
- **joinforbindelse**: et job, der har indflydelse på en joinforbindelse over sorteret, lige opdelt datasæt
- **multifilewc**: et job, der tæller ord fra flere filer
- **pentomino**: en kort/reducere flisen æglæggende program for at finde løsninger på pentomino problemer
- **pi**: et kort/reducere program, der beregner et skøn over Pi ved hjælp af en kvasi Monte nu metode
- **randomtextwriter**: et kort/reducere program, der skriver 10 GB tilfældige tekstdata per node
- **randomwriter**: et kort/reducere program, der skriver 10 GB af tilfældige data per node
- **secondarysort**: et eksempel, der definerer en sekundær sortering til reducere
- **Sortér**: et kort/reducere program, som sorterer fra de data, der er skrevet af tilfældige writer
- **sudoku**: en sudoku Problemløser
- **teragen**: oprette data til terasort
- **terasort**: køre terasort
- **teravalidate**: kontrollere resultaterne af terasort
- **wordcount**: et kort/reducere program, der tæller ordene i input-filer
- **wordmean**: et kort/reducere program, der tæller den gennemsnitlige længde af ord i input-filer
- **wordmedian**: et kort/reducere program, der tæller median længden af ord på listen input filer
- **wordstandarddeviation**: et kort/reducere program, der tæller standardafvigelsen for længden af ord i input-filer

**Kildekode**: kildekode for disse eksempler er inkluderet på HDInsight klyngen på **/usr/hdp/2.2.4.9-1/hadoop/src/hadoop-mapreduce-project/hadoop-mapreduce-examples**

> [AZURE.NOTE] Den `2.2.4.9-1` i stien er versionen af Hortonworks Data Platform for HDInsight-klyngen og kan ændres, når HDInsight er opdateret.

## <a name="how-to-run-the-samples"></a>Sådan køres eksemplerne ##

1. Oprette forbindelse til HDInsight ved hjælp af SSH, som beskrevet i følgende artikler:

    - [Bruge SSH med Linux-baserede Hadoop på HDInsight fra Linux, Unix eller OS X](hdinsight-hadoop-linux-use-ssh-unix.md)

    - [Bruge SSH med Linux-baserede Hadoop på HDInsight fra Windows](hdinsight-hadoop-linux-use-ssh-windows.md)

2. Fra den `username@#######:~$` Spørg, skal du bruge følgende kommando til at få vist eksemplerne:

        yarn jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar

    Dette opretter listen over eksempel fra den forrige sektion i dokumentet.

3. Brug følgende kommando for at få hjælp af en bestemt prøve. I dette tilfælde **wordcount** eksempel:

        yarn jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar wordcount

    Du modtager følgende meddelelse:

        Usage: wordcount <in> [<in>...] <out>

    Dette angiver, at du kan angive flere input stier til kildedokumenterne. Den sidste kurve er hvor output (antal ord i kildedokumenter) er gemt.

4. Brug følgende fremgangsmåde til at tælle alle ord i den notesbøger af Leonardo Da Vinci, som leveres som eksempeldata med din klynge:

        yarn jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar wordcount /example/data/gutenberg/davinci.txt /example/data/davinciwordcount

    Input til dette job læses fra **wasbs:///example/data/gutenberg/davinci.txt**.

    Output til dette eksempel er gemt i **wasbs: / / / eksempel/data/davinciwordcount**.

    > [AZURE.NOTE] Som nævnt i Hjælp til wordcount eksempel, kan du også angive flere input filer. For eksempel `hadoop jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar wordcount /example/data/gutenberg/davinci.txt /example/data/gutenberg/ulysses.txt /example/data/twowordcount` vil tælle ord i både davinci.txt og ulysses.txt.

5. Når jobbet er fuldført, kan du bruge følgende kommando til at få vist output:

        hdfs dfs -cat /example/data/davinciwordcount/*

    Dette sammenkæder alle de outputfiler, der er oprettet med jobbet, og få dem vist. Dette eksempel på grundlæggende der er kun én fil, men hvis der ikke er mere denne kommando skal gentages over dem alle.

    Output ligner følgende:

        zum     1
        zur     1
        zwanzig 1
        zweite  1

    Hver linje repræsenterer et ord, og hvor mange gange det er opstået i inputdataene.

## <a name="sudoku"></a>Sudoku

Eksemplet Sudoku har noget ikke brugbart brugen vejledninger. "Omfatte en puzzle på kommandolinjen."

[Sudoku](https://en.wikipedia.org/wiki/Sudoku) er en logik puzzle består af ni 3 x 3 gitre. Nogle af cellerne i gitteret har de tal, mens andre er tom, og målet er at løse for de tomme celler. Hyperlinket ovenfor indeholder flere oplysninger om opgaven, men formålet med dette eksempel er til at løse for de tomme celler. Så skal vores input være en fil, der er i følgende format:

- Ni rækker af ni kolonner

- Hver kolonne, som kan indeholde enten et tal eller `?` (som angiver en tom celle)

- Celler, der er adskilt af et mellemrum

Der er en bestemt måde at opbygge Sudoku tænkning; Du kan gentage et tal i en kolonne eller række. Der er et eksempel på den HDInsight-klynge, er korrekt opbygget. Det er placeret i **/usr/hdp/2.2.4.9-1/hadoop/src/hadoop-mapreduce-project/hadoop-mapreduce-examples/src/main/java/org/apache/hadoop/examples/dancing/puzzle1.dta** og indeholder følgende:

    8 5 ? 3 9 ? ? ? ?
    ? ? 2 ? ? ? ? ? ?
    ? ? 6 ? 1 ? ? ? 2
    ? ? 4 ? ? 3 ? 5 9
    ? ? 8 9 ? 1 4 ? ?
    3 2 ? 4 ? ? 8 ? ?
    9 ? ? ? 8 ? 5 ? ?
    ? ? ? ? ? ? 2 ? ?
    ? ? ? ? 4 5 ? 7 8

> [AZURE.NOTE] Den `2.2.4.9-1` del af stien kan blive ændret, som er foretaget opdateringer til HDInsight klynge.

For at køre dette gennem Sudoku eksempel skal du bruge følgende kommando:

    yarn jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar sudoku /usr/hdp/2.2.9.1-1/hadoop/src/hadoop-mapreduce-project/hadoop-mapreduce-examples/src/main/java/org/apache/hadoop/examples/dancing/puzzle1.dta

Resultaterne skal se ud som følger:

    8 5 1 3 9 2 6 4 7
    4 3 2 6 7 8 1 9 5
    7 9 6 5 1 4 3 8 2
    6 1 4 8 2 3 7 5 9
    5 7 8 9 6 1 4 2 3
    3 2 9 4 5 7 8 1 6
    9 4 7 2 8 6 5 3 1
    1 8 5 7 3 9 2 6 4
    2 6 3 1 4 5 9 7 8

## <a name="pi-"></a>PI (π)

Pi udsnittet bruger en statistisk (kvasi Monte nu) metode til at anslå værdien af pi. Punkter, der er placeret inde i en enhed tilfældigt firkantet også falder inden for en cirkel, der er anført i denne firkant med en sandsynlighed, der er lig med området af cirklen, pi/4. Værdien af pi kan beregnede fra værdien af 4R, hvor R er forholdet mellem antallet point, der er inde i en cirkel til det samlede antal point, der er i kvadratet. Jo større eksempel på punkter, desto bedre estimatet er.

Mapperen til dette eksempel opretter et antal point tilfældigt inde i en firkant enhed og derefter tæller antallet af de punkter, der er i cirklen.

Reduktionsrør derefter indsamles der tælles ved mappers punkter og beregner et skøn over værdien af pi fra formel 4R, hvor R er forholdet mellem antallet point tælles inde i en cirkel til det samlede antal point, der er i kvadratet.

Brug følgende kommando for at køre dette eksempel. Dette bruger 16 kort med 10,000,000 prøver til at beregne værdien af pi:

    yarn jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar pi 16 10000000

Den værdi, der returneres af dette skal ligne **3.14159155000000000000**. For referencer er de første 10 decimaler af pi 3.1415926535.

##<a name="10gb-greysort"></a>10GB Greysort

GraySort er en benchmark sortering, hvis metrikværdi er den Sortér rente (TB/minut), der opnås når du sorterer meget store mængder data, som regel en 100TB mindste.

Dette eksempel bruger en mindre 10GB af data, så den kan køres relativt hurtigt. Den anvender de MapReduce programmer, der er udviklet af Owen O'Malley og Arun Murthy, der har vundet årlige generelle ("daytona") terabyte Sortér benchmark i 2009 med en hastighed på 0.578 TB/min (100 TB i 173 minutter). Du kan finde flere oplysninger om dette og andre sortering benchmarktests webstedet [Sortbenchmark](http://sortbenchmark.org/) .

Dette eksempel bruger tre typer MapReduce programmer:

- **TeraGen**: et MapReduce program, der genererer rækker af data for at sortere

- **TeraSort**: eksempler på indtastede data og bruger MapReduce til at sortere dataene i en samlet rækkefølge

    TeraSort er en standard sortering af MapReduce funktioner, med undtagelse af en brugerdefineret partitioner, der bruger en sorteret liste over N-1 prøver fra taster, der definerer det vigtigste område for hver reducere. Især alle nøgler sådanne, som eksempel [i-1] < = nøgle < eksempel [i] sendes til reducere i. Dette garantier, output af reducere i er alle, der er mindre end output fra reducere i + 1.

- **TeraValidate**: et MapReduce program, der har valideret, output er sorteret globalt

    Det opretter en tilknytning per fil i outputmappen, og hver tilknytning sikrer, at hver nøgle er mindre end eller lig med den forrige opgave. Funktionen kort genererer også poster af de første og sidste taster for hver fil, og funktionen reducere sikrer, at den første tast af fil i er større end den sidste nøgle i filen i-1. Problemer rapporteres som output fra reducere med de pågældende taster, der er defekt.

Brug følgende trin til at oprette data, sortere og derefter validere output:

1. Generere 10GB af data, der skal gemmes til den HDInsight klynge standard-lager på **wasbs: / / / eksempel / / 10GB-Sortér-datainput**:

        yarn jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar teragen -Dmapred.map.tasks=50 100000000 /example/data/10GB-sort-input

    Den `-Dmapred.map.tasks` fortæller Hadoop, hvor mange kort opgaver for at bruge til jobbet. De sidste to parametre Fortæl sagen til at oprette 10GB data og gemme den på **wasbs: / / / eksempel / / 10GB-Sortér-datainput**.

2. Brug følgende kommando til at sortere dataene:

        yarn jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar terasort -Dmapred.map.tasks=50 -Dmapred.reduce.tasks=25 /example/data/10GB-sort-input /example/data/10GB-sort-output

    Den `-Dmapred.reduce.tasks` fortæller Hadoop, hvor mange reducere opgaver skal bruges til jobbet. De sidste to parametre er lige input- og -placeringer til data.

3. Du kan bruge følgende til at validere de data, der genereres af sorteringen:

        yarn jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar teravalidate -Dmapred.map.tasks=50 -Dmapred.reduce.tasks=25 /example/data/10GB-sort-output /example/data/10GB-sort-validate

##<a name="next-steps"></a>Næste trin ##

I denne artikel, du har lært at køre eksemplerne inkluderet med Linux-baserede HDInsight klynger. Du kan finde selvstudier om at bruge gris, Hive og MapReduce med HDInsight, i følgende emner:

* [Brug gris med Hadoop på HDInsight][hdinsight-use-pig]
* [Bruge Hive med Hadoop på HDInsight][hdinsight-use-hive]
* [Bruge MapReduce med Hadoop på HDInsight] [hdinsight-use-mapreduce]



[hdinsight-errors]: hdinsight-debug-jobs.md
[hdinsight-use-mapreduce]: hdinsight-use-mapreduce.md
[hdinsight-sdk-documentation]: https://msdn.microsoft.com/library/azure/dn479185.aspx

[hdinsight-submit-jobs]: hdinsight-submit-hadoop-jobs-programmatically.md
[hdinsight-introduction]: hdinsight-hadoop-introduction.md



[hdinsight-samples]: hdinsight-run-samples.md

[hdinsight-use-hive]: hdinsight-use-hive.md
[hdinsight-use-pig]: hdinsight-use-pig.md
