<properties
   pageTitle="Bruge Python komponenter i en Storm topologi på HDinsight | Microsoft Azure"
   description="Få mere at vide, hvordan du kan bruge Python komponenter fra med Apache Storm på Azure HDInsight. Du vil lære at bruge Python komponenter fra en begge Java baseret og Clojure baseret Storm topologi."
   services="hdinsight"
   documentationCenter=""
   authors="Blackmist"
   manager="jhubbard"
   editor="cgronlun"/>

<tags
   ms.service="hdinsight"
   ms.devlang="python"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data"
   ms.date="09/27/2016"
   ms.author="larryfr"/>

#<a name="develop-apache-storm-topologies-using-python-on-hdinsight"></a>Udvikle Apache Storm topologier ved hjælp af Python på HDInsight

Apache Storm understøtter flere sprog, lige så du kan kombinere komponenter fra flere sprog i én topologi. I dette dokument lærer du, hvordan du bruger Python komponenter i din Java og Clojure-baserede Storm topologier på HDInsight.

##<a name="prerequisites"></a>Forudsætninger

* Python 2,7 eller nyere

* Java JDK 1.7 eller nyere

* [Leiningen](http://leiningen.org/)

##<a name="storm-multi-language-support"></a>Storm understøttelse af flere sprog

Storm er udviklet til at arbejde med komponenter, der er skrevet ved hjælp af en hvilken som helst programmeringssprog, men dette kræver, at komponenterne forstå, hvordan du arbejder med [Thrift definition for Storm](https://github.com/apache/storm/blob/master/storm-core/src/storm.thrift). Et modul leveres til Python, som en del af projektet Apache Storm, hvor du kan nemt brugergrænsefladen med Storm. Du kan finde dette modul på [https://github.com/apache/storm/blob/master/storm-multilang/python/src/main/resources/resources/storm.py](https://github.com/apache/storm/blob/master/storm-multilang/python/src/main/resources/resources/storm.py).

Da Apache Storm er en Java-proces, der kører på Java Virtual Machine (JVM), der udføres komponenter, der er skrevet på andre sprog som underprocesser. De Storm bit kører i JVM kommunikerer med disse underprocesser, ved hjælp af JSON meddelelser sendes over stdin/stdout. Flere oplysninger om kommunikation mellem komponenter kan findes i dokumentationen til [flere lang Protocol](https://storm.apache.org/documentation/Multilang-protocol.html) .

###<a name="the-storm-module"></a>Modulet Storm

Modulet storm (https://github.com/apache/storm/blob/master/storm-multilang/python/src/main/resources/resources/storm.py), indeholder de bit, der bruges til at oprette Python komponenter, der fungerer med Storm.

Dette giver ting som `storm.emit` at sende tupler, og `storm.logInfo` skrives til loggene. Jeg vil opfordrer dig til at læse over denne fil og forstå, hvad den indeholder.

##<a name="challenges"></a>Udfordringer

Bruger modulet __storm.py__ , kan du oprette Python spouts, der forbruger data og bolte, der behandler data, men den overordnede Storm topologi definition, kabler af kommunikation mellem komponenter er stadig skrevet ved hjælp af Java eller Clojure. Hvis du bruger Java, skal du desuden kan også oprette Java-komponenter, der fungerer som en grænseflade til Python komponenter.

Da Storm klynger køre i en fordelt måde, skal du også sikre, at eventuelle moduler, der kræves af din Python komponenter er tilgængelige på alle arbejder noder i klyngen. Storm indeholder ikke en nem måde at gøre dette for flere lang ressourcer – du har enten at medtage alle afhængigheder som en del af filen glas for topologien, eller Installer manuelt afhængigheder på hver arbejder node i klyngen.

###<a name="java-vs-clojure-topology-definition"></a>Java kontra Clojure topologi definition

Af de to metoder til at definere en topologi er Clojure er langt den nemmeste/reneste, som du kan direkte reference python komponenter i topologi definitionen. For Java-baserede topologi definitioner, skal du også angive Java komponenter, der håndterer ting som erklæring af felterne i tupler returneres fra Python komponenter.

Begge metoder er beskrevet i dette dokument, sammen med eksempelprojekter.

##<a name="python-components-with-a-java-topology"></a>Python komponenter med en Java topologi

> [AZURE.NOTE] I dette eksempel er tilgængelig på [https://github.com/Azure-Samples/hdinsight-python-storm-wordcount](https://github.com/Azure-Samples/hdinsight-python-storm-wordcount) i mappen __JavaTopology__ . Dette er et Maven baseret projekt. Hvis du ikke kender Maven, kan du se [udvikle Java-baserede topologier med Apache Storm på HDInsight](hdinsight-storm-develop-java-topology.md) kan finde flere oplysninger om oprettelse af en Maven projekt til en Storm topologi.

En Java-baserede topologi, der bruger Python (eller andre JVM sprogkomponenter) vises først at bruge Java komponenter. men hvis du ser i hver af Java spouts/bolte, får du vist kode, der ligner følgende:

    public SplitBolt() {
        super("python", "countbolt.py");
    }

Dette er hvor Java aktiverer Python og kører scriptet, der indeholder den faktiske bolt logik. Java spouts/bolte (for eksempel) erklære blot felterne i en tupel, der udsendes af komponenten underliggende Python.

Faktisk Python filerne er gemt i den `/multilang/resources` mappe i dette eksempel. Den `/multilang` directory der refereres til i __pom.xml__:

<resources>
    <resource>
        <!-- Where the Python bits are kept -->
        <directory>${basedir} / multilang</directory>
    </resource>
</resources>

Dette omfatter alle filerne i den `/multilang` mappe i den glas, der oprettes fra dette projekt.

> [AZURE.IMPORTANT] Bemærk, at dette kun angiver den `/multilang` directory og ikke `/multilang/resources`. Storm forventer ikke JVM ressourcer i en `resources` directory, så det er ledt efter internt allerede. Placere komponenter i denne mappe kan du kun reference ved navn i Java-kode. For eksempel `super("python", "countbolt.py");`. En anden måde at tænke på det er, at Storm ser den `resources` mappe som rod (/), når du åbner flere lang ressourcer.
>
> For eksempel projektet, den `storm.py` modul er inkluderet i den `/multilang/resources` directory.

###<a name="build-and-run-the-project"></a>Oprette og køre projektet

For at køre dette projekt lokalt, skal du blot bruge følgende kommando for Maven til at oprette og køre i lokal tilstand:

    mvn compile exec:java -Dstorm.topology=com.microsoft.example.WordCount

Brug ctrl + c for at afbryde processen.

Hvis du vil installere projektet til en HDInsight klynge kører Apache Storm, skal du følge nedenstående trin:

1. Oprette en uber glas:

        mvn package

    Dette vil oprette en fil med navnet __WordCount – 1.0-SNAPSHOT.jar__ i den `/target` mappe for dette projekt.

2. Overføre glas filen til den Hadoop-klynge, ved hjælp af en af følgende fremgangsmåder:

    * Til __Linux-baserede__ HDInsight klynger: Brug `scp WordCount-1.0-SNAPSHOT.jar USERNAME@CLUSTERNAME-ssh.azurehdinsight.net:WordCount-1.0-SNAPSHOT.jar` til at kopiere filen glas til klynge, erstatte brugernavn med dit brugernavn SSH og CLUSTERNAME med HDInsight klyngenavn.

        Når filen er færdig med at overføre, oprette forbindelse til den klynge ved hjælp af SSH og begynde at bruge den topologi`storm jar WordCount-1.0-SNAPSHOT.jar com.microsoft.example.WordCount wordcount`

    * Til __Windows-baseret__ HDInsight klynger: oprette forbindelse til Storm Dashboard ved at gå til HTTPS://CLUSTERNAME.azurehdinsight.net/ i din browser. Erstatte CLUSTERNAME med dit HDInsight klyngenavn og angive administratornavn og adgangskode, når du bliver bedt om.

        Bruger formularen skal udføre følgende handlinger:

        * __Jar fil__: vælge __Gennemse__og derefter vælge filen __WordCount-1.0-SNAPSHOT.jar__
        * __Klassenavnet__: Angiv`com.microsoft.example.WordCount`
        * __Yderligere Paramters__: Angiv et fuldt navn såsom `wordcount` til at identificere topologien

        Til sidst skal du vælge __Send__ for at starte topologien.

> [AZURE.NOTE] Når den er startet, kører en Storm topologi, til showet afbrydes (aflives.) Du kan stoppe topologien ved at bruge en den `storm kill TOPOLOGYNAME` kommando fra kommandolinjen (SSH sessionen til en Linux klynge eksempelvis) eller ved hjælp af Brugergrænsefladen Storm, Vælg topologien, og vælg derefter knappen __Slet__ .

##<a name="python-components-with-a-clojure-topology"></a>Python komponenter med en Clojure topologi

> [AZURE.NOTE] I dette eksempel er tilgængelig på [https://github.com/Azure-Samples/hdinsight-python-storm-wordcount](https://github.com/Azure-Samples/hdinsight-python-storm-wordcount) i mappen __ClojureTopology__ .

Denne topologi blev oprettet ved hjælp af [Leiningen](http://leiningen.org) til at [oprette et nyt Clojure projekt](https://github.com/technomancy/leiningen/blob/stable/doc/TUTORIAL.md#creating-a-project). Herefter foretaget scaffolded projektet følgende ændringer:

* __Project.CLJ__: tilføjet afhængigheder for Storm og udeladelser for elementer, der kan medføre et problem, når installeret på HDInsight-serveren.
* __ressourcer/ressourcer__: Leiningen opretter en standard `resources` directory, men de filer, der er gemt her ser ud til at få tilføjet i roden af filen glas, der er oprettet ud fra dette projekt, og Storm forventer filer i en underordnet mappe med navnet `resources`. Så er tilføjet en underordnet mappe og Python filerne er gemt i `resources/resources`. På kørselstidspunktet, bliver dette behandlet som rod (/) for at få adgang til Python komponenter.
* __src/wordcount/Core.CLJ__: denne fil indeholder topologi definitionen og refereres til fra filen __project.clj__ . Du kan finde flere oplysninger om brug af Clojure til at definere en Storm topologi, [Clojure DSL](https://storm.apache.org/documentation/Clojure-DSL.html).

###<a name="build-and-run-the-project"></a>Oprette og køre projektet

__Oprette og køre projektet lokalt__, skal du bruge følgende kommando:

    lein clean, run

Brug __Ctrl + C for__at stoppe topologien.

__Til at opbygge en uberjar og implementere til HDInsight__, skal du følge nedenstående trin:

1. Oprette en uberjar, der indeholder topologi og nødvendige afhængigheder:

        lein uberjar

    Dette vil oprette en ny fil med navnet `wordcount-1.0-SNAPSHOT.jar` i den `target\uberjar+uberjar` directory.
    
2. Bruge en af følgende metoder til at installere og køre topologien til en HDInsight klynge:

    * __Linux-baserede HDInsight__
    
        1. Kopiere filen til den HDInsight klynge hoved node ved hjælp af `scp`. Eksempel:
        
                scp wordcount-1.0-SNAPSHOT.jar USERNAME@CLUSTERNAME-ssh.azurehdinsight.net:wordcount-1.0-SNAPSHOT.jar
                
            Erstat brugernavn med en SSH bruger til klynge og CLUSTERNAME med dit HDInsight klyngenavn.
            
        2. Når filen er blevet kopieret til klyngen, skal du bruge SSH til at oprette forbindelse til klyngen og sende jobbet. Oplysninger om brug af SSH med HDInsight, skal du se en af følgende:
        
            * [Bruge SSH med Linux-baserede HDInsight fra Linux, Unix eller OS X](hdinsight-hadoop-linux-use-ssh-unix.md)
            * [Bruge SSH med Linux-baserede HDInsight fra Windows](hdinsight-hadoop-linux-use-ssh-windows.md)
            
        3. Når forbindelse, kan du bruge følgende til at starte topologien:
        
                storm jar wordcount-1.0-SNAPSHOT.jar wordcount.core wordcount
    
    * __Windows-baseret HDInsight__
    
        1. Oprette forbindelse til Storm Dashboard ved at gå til HTTPS://CLUSTERNAME.azurehdinsight.net/ i din browser. Erstatte CLUSTERNAME med dit HDInsight klyngenavn og angive administratornavn og adgangskode, når du bliver bedt om.

        2. Bruger formularen skal udføre følgende handlinger:

            * __Jar fil__: vælge __Gennemse__og derefter vælge filen __wordcount-1.0-SNAPSHOT.jar__
            * __Klassenavnet__: Angiv`wordcount.core`
            * __Yderligere Paramters__: Angiv et fuldt navn såsom `wordcount` til at identificere topologien

            Til sidst skal du vælge __Send__ for at starte topologien.

> [AZURE.NOTE] Når den er startet, kører en Storm topologi, til showet afbrydes (aflives.) Du kan stoppe topologien ved at bruge en den `storm kill TOPOLOGYNAME` kommando fra kommandolinjen (SSH sessionen til en Linux klynge) eller ved hjælp af Brugergrænsefladen Storm, Vælg topologien, og vælg derefter knappen __Slet__ .

##<a name="next-steps"></a>Næste trin

I dette dokument, du har lært Sådan bruger du Python komponenter fra en Storm topologi. Se følgende dokumenter for at få andre måder at bruge Python med HDInsight:

* [Sådan bruges Python til streaming MapReduce job](hdinsight-hadoop-streaming-python.md)
* [Sådan bruger du Python bruger defineret funktioner (UDF) i gris og Hive](hdinsight-python.md)
