<properties 
    pageTitle="Kerner, der er tilgængelige med Jupyter notesbøger på HDInsight Spark klynger på Linux | Microsoft Azure" 
    description="Få mere at vide om yderligere Jupyter notesbog kerner tilgængelige med knallertmotor klynge på HDInsight Linux." 
    services="hdinsight" 
    documentationCenter="" 
    authors="nitinme" 
    manager="jhubbard" 
    editor="cgronlun"
    tags="azure-portal"/>

<tags 
    ms.service="hdinsight" 
    ms.workload="big-data" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="10/05/2016" 
    ms.author="nitinme"/>


# <a name="kernels-available-for-jupyter-notebooks-with-apache-spark-clusters-on-hdinsight-linux"></a>Kerner, der er tilgængelige for Jupyter notesbøger med Apache knallertmotor klynger på HDInsight Linux

Apache knallertmotor klynge på HDInsight (Linux) indeholder Jupyter notesbøger, som du kan bruge til at teste dine programmer. En kerne er et program, der kører og fortolker din kode. HDInsight Spark klynger indeholder to kerner, der kan bruges sammen med Jupyter notesbogen. Dette er:

1. **PySpark** (til programmer, der er skrevet i Python)
2. **Knallertmotor** (til programmer, der er skrevet i Scala)

I denne artikel lærer du om, hvordan du bruger disse kerner, og hvad er de fordele, du får i at bruge dem.

**Forudsætninger for:**

Du skal have følgende:

- Et Azure-abonnement. Se [få Azure gratis prøveversion](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).
- En Apache knallertmotor klynge på HDInsight Linux. Flere oplysninger under [oprette Apache knallertmotor klynger i Azure HDInsight](hdinsight-apache-spark-jupyter-spark-sql.md).

## <a name="how-do-i-use-the-kernels"></a>Hvordan bruger jeg kerner? 

1. Klik på feltet for din knallertmotor klynge fra [Azure-portalen](https://portal.azure.com/), fra startboard (hvis det fastgjort til startboard). Du kan også gå til din klynge under **Gennemse alle** > **HDInsight klynger**.   

2. Klik på **Klynge Dashboard**fra bladet knallertmotor klynge, og klik derefter på **Jupyter notesbog**. Hvis du bliver bedt om det, kan du angive Administratoroplysninger for-klyngen.

    > [AZURE.NOTE] Du kan også når Jupyter notesbogen til din klynge ved at åbne følgende URL-adressen i din browser. Erstat __CLUSTERNAME__ med navnet på din klynge:
    >
    > `https://CLUSTERNAME.azurehdinsight.net/jupyter`

2. Oprette en ny notesbog med de nye kerner. Klik på **Ny**, og klik derefter på **Pyspark** eller **gnister**. Du skal bruge knallertmotor kernen i Scala programmer og PySpark kernen for Python programmer.

    ![Opret en ny Jupyter notesbog] (./media/hdinsight-apache-spark-jupyter-notebook-kernels/jupyter-kernels.png "Opret en ny Jupyter notesbog") 

3. Dette bør åbne en ny notesbog med kernen du har valgt.

## <a name="why-should-i-use-the-pyspark-or-spark-kernels"></a>Hvorfor skal jeg bruge PySpark eller knallertmotor kerner?

Her er nogle fordele ved at bruge de nye kerner.

1. **Forudindstillede kontekster**. Med **PySpark** eller **knallertmotor** kerner, der følger med Jupyter notesbøger, behøver du ikke angive knallertmotor eller Hive kontekster eksplicit, før du kan begynde at arbejde med det program, du udvikler; disse er tilgængelige for dig som standard. Disse kontekster er:

    * **sc** - for gnister kontekst
    * **sqlContext** - for Hive kontekst


    Så behøver du ikke at køre sætninger som følgende for at indstille kontekster:

        ###################################################
        # YOU DO NOT NEED TO RUN THIS WITH THE NEW KERNELS
        ###################################################
        sc = SparkContext('yarn-client')
        sqlContext = HiveContext(sc)

    Du kan i stedet bruge forudindstillede kontekster direkte i dit program.
    
2. **Celle magics**. PySpark kernen indeholder nogle foruddefinerede "magics", som er specielle kommandoer, som du kan ringe til med `%%` (fx `%%MAGIC` <args>). Kommandoen magiske skal være det første ord i en celle med kode og Tillad flere tekstlinjer indhold. Magiske ordet skal være det første ord i cellen. Tilføje noget før Magien, lige kommentarer, medfører en fejl.   Finde flere oplysninger om magics, [her](http://ipython.readthedocs.org/en/stable/interactive/magics.html).

    I nedenstående tabel vises de forskellige magics, der er tilgængelige via kerner.

  	| Magisk     | Eksempel                         | Beskrivelse  |
  	|-----------|---------------------------------|--------------|
  	| Hjælp      | `%%help`                            | Genererer en liste over alle tilgængelige magics med eksempel og beskrivelse |
  	| info      | `%%info`                          | Output sessionsoplysninger for den aktuelle Livius slutpunkt |
  	| konfigurere | `%%configure -f`<br>`{"executorMemory": "1000M"`,<br>`"executorCores": 4`} | Konfigurerer parametre for oprettelse af en session. Flaget kraft (-f) er obligatorisk, hvis en session er blevet oprettet og sessionen bliver sænket og genoprettet. Se på [Liviuss INDLÆG /sessions anmode om brødteksten](https://github.com/cloudera/livy#request-body) for en liste over gyldige parametre. Parametre skal være bestået som en JSON-streng og skal være på den næste linje efter Magien, som vist i kolonnen eksempel. |
  	| SQL       |  `%%sql -o <variable name>`<br> `SHOW TABLES`    | Udfører en Hive-forespørgsel til sqlContext. Hvis den `-o` parameter er overført, er bevaret resultatet af forespørgslen i de %% lokale Python kontekst som en [Pandas](http://pandas.pydata.org/) dataframe.   |
  	| lokale     |     `%%local`<br>`a=1`              | Al kode i efterfølgende linjer vil blive udført lokalt. Koden skal være gyldig Python kode. |
  	| logfiler      | `%%logs`                        | Udskriver loggene for den aktuelle Livius session.  |
  	| Slet    | `%%delete -f -s <session number>` | Sletter en bestemt session på det aktuelle Livius slutpunkt. Bemærk, at du ikke kan slette den session, som er startet for kernen sig selv. |
  	| Oprydning   | `%%cleanup -f`                    | Sletter alle sessioner for det aktuelle Livius slutpunktet, herunder denne notesbog session. Tving flaget -f er obligatorisk.  |

    >[AZURE.NOTE] Ud over de magics, der er tilføjet af PySpark kernen, du kan også bruge den [indbyggede IPython magics](https://ipython.org/ipython-doc/3/interactive/magics.html#cell-magics), herunder `%%sh`. Du kan bruge den `%%sh` magisk til at køre scripts og blok med kode på klynge headnode. 

3. **Automatisk visualisering**. **Pyspark** kernen visualiseres automatisk output fra Hive og SQL-forespørgsler. Du har mulighed for at vælge mellem flere forskellige typer visualiseringer, herunder tabel, cirkeldiagram, linje, område-, liggende søjle.

## <a name="parameters-supported-with-the-sql-magic"></a>Parametre understøttes med den %% sql magisk

Den %% sql magisk understøtter forskellige parametre, du kan bruge til at styre slags output, som du modtager, når du kører forespørgsler. I følgende tabel vises output.

| Parameter     | Eksempel                         | Beskrivelse  |
|-----------|---------------------------------|--------------|
| -o      | `-o <VARIABLE NAME>`                          | Brug denne parameter til at bevare resultatet af forespørgslen, i den %% lokale Python kontekst, som en [Pandas](http://pandas.pydata.org/) dataframe. Navnet på variablen dataframe er det variable navn, du angiver. |
| -q      | `-q`                          | Brug dette til at deaktivere visualiseringer for cellen. Hvis du ikke vil visualisere indholdet af en celle og blot ønsker at registrere den som en dataframe, og brug `-q -o <VARIABLE>`. Hvis du vil deaktivere visualiseringer uden at indsamle resultaterne (fx for at køre en SQL-forespørgsel med side effekter, vil en `CREATE TABLE` sætning), kan bruge `-q` uden at angive en `-o` argument. |
| -m       |  `-m <METHOD>`    | Hvor **metode** er **tage** eller **eksempel** (standard er **tage**). Hvis metoden er **tage**, vælger kernen elementer fra øverst del af det result datasæt, der er angivet af MAXROWS (beskrives senere i denne tabel). Hvis **stikprøve**metoden kernen tilfældigt eksempel elementer af datasættet i henhold til `-r` parameter, er næste beskrevet i denne tabel.   |
| -r     |     `-r <FRACTION>`            | **BRØKDEL** er her en flydende tal mellem 0,0 og 1,0. Hvis metoden eksempel for SQL-forespørgslen er `sample`, og derefter kernen tilfældigt eksempler fra den angivne brøk af elementerne i resultatsættet for. f.eks. Hvis du kører en SQL-forespørgsel med argumenterne `-m sample -r 0.01`, og derefter 1% af resultatrækker der tages prøver tilfældigt fra. |
| -n      | `-n <MAXROWS>`                        | **MAXROWS** er et heltal. Kernen vil begrænse antallet af rækker, der output skal **MAXROWS**. Hvis **MAXROWS** er et negativt tal som **-1**, vil derefter antallet af rækker i resultatsættet ikke være begrænset. |

**Eksempel:**

    %%sql -q -m sample -r 0.1 -n 500 -o query2 
    SELECT * FROM hivesampletable

Ovenstående erklæring gør følgende:

* Markerer alle poster fra **hivesampletable**.
* Da vi bruge - q, deaktiveres automatisk visualisering.
* Da vi bruge `-m sample -r 0.1 -n 500` tilfældigt eksempler 10% af rækker i hivesampletable og begrænser størrelsen af resultatsættet til 500 rækker.
* Til sidst, fordi vi har brugt `-o query2` også gemmer den output til en dataframe kaldet **query2**.
    

## <a name="considerations-while-using-the-new-kernels"></a>Overvejelser i forbindelse med når du bruger de nye kerner

Uanset hvilken kernen, du bruger (PySpark eller knallertmotor) efterlade de notesbøger, der kører, forbruger klyngeressourcerne.  Med disse kerner, fordi kontekster er forudindstillede, blot afslutte notesbøgerne ikke afbryde konteksten og dermed på klyngeressourcerne, der fortsætter med at være i brug. En god ide med PySpark og knallertmotor kerner ville være at bruge indstillingen **Luk og standse** den notesbog **-** menuen Filer. Dette afbryder konteksten og derefter afslutter notesbogen.    


## <a name="show-me-some-examples"></a>Vis mig nogle eksempler

Når du åbner en Jupyter notesbog, får du vist to mapper, der er tilgængelige på rodniveau.

* Mappen **PySpark** har eksempel notesbøger, der bruger nye **Python** kernen.
* Mappen **Scala** har eksempel notesbøger, der bruger nye **knallertmotor** kernen.

Du kan åbne notesbogen **00 - [Læs mig første] knallertmotor magisk kernen funktioner** fra mappen **PySpark** eller **knallertmotor** for at få mere at vide om de forskellige magics, der er tilgængelige. Du kan også bruge de andre eksempel notesbøger, der er tilgængelige under de to mapper for at lære at opnå forskellige scenarier ved hjælp af Jupyter notesbøger med HDInsight Spark klynger.

## <a name="where-are-the-notebooks-stored"></a>Hvor gemmes notesbøgerne?

Jupyter notesbøger er gemt på kontoen lagerplads, der er knyttet til klynge under mappen **/HdiNotebooks** .  Notesbøger, filer og mapper, som du opretter ud fra i Jupyter vil være tilgængelige i WASB.  Eksempelvis hvis du bruger Jupyter til at oprette en mappe **Min_mappe** og **myfolder/mynotebook.ipynb**en notesbog, du kan få adgang til notesbogen på `wasbs:///HdiNotebooks/myfolder/mynotebook.ipynb`.  Omvendt gør sig også gældende, det vil sige, hvis du overfører en notesbog direkte til din lagerplads konto på `/HdiNotebooks/mynotebook1.ipynb`, notesbogen kan ses fra Jupyter samt.  Notesbøger, forbliver i kontoen lagerplads, selv efter at klyngen slettes.

Den måde, notesbøger er gemt på kontoen lagerplads er kompatibelt med HDFS. Så, hvis du SSH i den klynge, du kan bruge indsende kommandoer til styring af ud som følger:

    hdfs dfs -ls /HdiNotebooks                            # List everything at the root directory – everything in this directory is visible to Jupyter from the home page
    hdfs dfs –copyToLocal /HdiNotebooks                 # Download the contents of the HdiNotebooks folder
    hdfs dfs –copyFromLocal example.ipynb /HdiNotebooks   # Upload a notebook example.ipynb to the root folder so it’s visible from Jupyter


I tilfælde af, at der er problemer med at få adgang til kontoen lagerplads for-klyngen, notesbøgerne gemmes også på headnode `/var/lib/jupyter`.

## <a name="supported-browser"></a>Understøttet browser
Jupyter notesbøger kører mod HDInsight Spark klynger understøttes kun på Google Chrome.

## <a name="feedback"></a>Feedback

De nye kerner er i udvikling af fase og kan forfaldne over tid. Det kan også betyde, at API'er kan ændres, når disse kerner slettes. Vi vil sætter stor pris på din feedback, du har, når du bruger disse nye kerner. Dette kan være meget nyttigt i udformningen af den endelige version af disse kerner. Du kan lade kommentarer/feedback under sektionen **kommentarer** i bunden af denne artikel.


## <a name="seealso"></a>Se også


* [Oversigt: Apache knallertmotor på Azure HDInsight](hdinsight-apache-spark-overview.md)

### <a name="scenarios"></a>Scenarier

* [Knallertmotor med BI: analyse af interaktive data ved hjælp af knallertmotor i HDInsight med BI-værktøjer](hdinsight-apache-spark-use-bi-tools.md)

* [Knallertmotor med Machine Learning: Brug knallertmotor i HDInsight til analyse af bygning temperaturen ved hjælp af VVS-data](hdinsight-apache-spark-ipython-notebook-machine-learning.md)

* [Knallertmotor med Machine Learning: Brug knallertmotor i HDInsight til at forudsige mad undersøgelsesresultaterne](hdinsight-apache-spark-machine-learning-mllib-ipython.md)

* [Knallertmotor Streaming: Brug knallertmotor i HDInsight til udvikling af realtid streaming programmer](hdinsight-apache-spark-eventhub-streaming.md)

* [Websted log analyse ved hjælp af knallertmotor i HDInsight](hdinsight-apache-spark-custom-library-website-log-analysis.md)

### <a name="create-and-run-applications"></a>Oprette og køre programmer

* [Oprette en enkeltstående program, ved hjælp af Scala](hdinsight-apache-spark-create-standalone-application.md)

* [Køre job fra en fjernplacering på en knallertmotor klynge, ved hjælp af Livius](hdinsight-apache-spark-livy-rest-interface.md)

### <a name="tools-and-extensions"></a>Værktøjer og filtypenavne

* [Brug HDInsight værktøjer plug-in til IntelliJ ide at oprette og sende knallertmotor Scala programmer](hdinsight-apache-spark-intellij-tool-plugin.md)

* [Brug HDInsight værktøjer plug-in til IntelliJ ide for at fejlfinde knallertmotor programmer fra en fjernplacering](hdinsight-apache-spark-intellij-tool-plugin-debug-jobs-remotely.md)

* [Bruge Zeppelin notesbøger med en knallertmotor klynge på HDInsight](hdinsight-apache-spark-use-zeppelin-notebook.md)

* [Bruge eksterne pakker med Jupyter notesbøger](hdinsight-apache-spark-jupyter-notebook-use-external-packages.md)

* [Installere Jupyter på din computer og oprette forbindelse til en HDInsight Spark klynge](hdinsight-apache-spark-jupyter-notebook-install-locally.md)

### <a name="manage-resources"></a>Administrere ressourcer

* [Administrere ressourcer for Apache knallertmotor klynge i Azure HDInsight](hdinsight-apache-spark-resource-manager.md)

* [Holde styr på og fejlfinding job, der kører på en Apache knallertmotor klynge i HDInsight](hdinsight-apache-spark-job-debugging.md)
