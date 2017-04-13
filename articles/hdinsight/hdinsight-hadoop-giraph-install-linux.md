<properties
    pageTitle="Installere og bruge Giraph på Linux-baserede HDInsight (Hadoop) | Microsoft Azure"
    description="Lær, hvordan du installerer Giraph på Linux-baserede HDInsight klynger ved hjælp af scripthandlinger. Scripthandlinger kan bruges til at tilpasse klyngen under oprettelsen ved at ændre klynge konfiguration eller installation af tjenester og funktioner."
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
    ms.date="10/17/2016"
    ms.author="larryfr"/>

# <a name="install-giraph-on-hdinsight-hadoop-clusters-and-use-giraph-to-process-large-scale-graphs"></a>Installer Giraph på HDInsight Hadoop klynger, og bruge Giraph til at behandle store grafer

Du kan installere Giraph på en hvilken som helst type klynge i Hadoop på Azure HDInsight ved hjælp af **Scripthandling** til at tilpasse en klynge.

I dette emne lærer du, hvordan du installerer Giraph ved hjælp af scripthandling. Når du har installeret Giraph, lærer hvordan du bruger Giraph til mest almindelige programmer, som er at behandle store grafer også.

> [AZURE.NOTE] Oplysninger i denne artikel gælder kun for Linux-baserede HDInsight klynger. Oplysninger om at arbejde med Windows-baserede klynger finder du [Installere Giraph på HDInsight Hadoop-klynger (Windows)](hdinsight-hadoop-giraph-install.md)

## <a name="whatis"></a>Hvad er Giraph?

[Apache Giraph](http://giraph.apache.org/) kan du udføre graph behandling ved hjælp af Hadoop, og de kan bruges med Azure HDInsight. Grafer modeller relationer mellem objekter, som forbindelserne mellem routere på et stort netværk som internettet eller relationer mellem personer på sociale netværk (også kaldet en sociale graf). Graph behandling gør det muligt at årsag om relationerne mellem objekter i et diagram, f.eks.:

- Identificere potentielle venner, der er baseret på din aktuelle relationer.
- Identificere den korteste vej mellem to computere i et netværk.
- Beregning af siden rangen for websider.

> [AZURE.WARNING] Komponenter i HDInsight klynge understøttes fuldt ud, og Microsoft Support hjælper med at isolere og løse problemer i forbindelse med disse komponenter.
>
> Brugerdefinerede komponenter som Giraph, modtager kommercielt begrundet support for at hjælpe dig med at foretage yderligere fejlfinding af problemet. Dette kan medføre løse problemet eller beder dig om at deltage tilgængelige kanaler for de Åbn kilde-teknologier, hvor deep ekspertise i forbindelse med teknologien, der er fundet. For eksempel, der er mange communitywebsteder, der kan bruges, ønsker: [MSDN-forum for HDInsight](https://social.msdn.microsoft.com/Forums/azure/en-US/home?forum=hdinsight), [http://stackoverflow.com](http://stackoverflow.com). Også Apache projekter har projektwebsteder på [http://apache.org](http://apache.org), for eksempel: [Hadoop](http://hadoop.apache.org/).

##<a name="what-the-script-does"></a>Hvad betyder det scriptet

Dette script udfører følgende handlinger:

* Installerer Giraph til`/usr/hdp/current/giraph`
* Kopier den `giraph-examples.jar` fil til standard-lager (WASB) til din klynge:`/example/jars/giraph-examples.jar`

## <a name="install"></a>Installere Giraph ved hjælp af scripthandlinger

Et eksempel på script til at installere Giraph på en HDInsight klynge er tilgængelig på følgende placering.

    https://hdiconfigactions.blob.core.windows.net/linuxgiraphconfigactionv01/giraph-installer-v01.sh

Dette afsnit indeholder en vejledning i at bruge eksempelscriptet mens du opretter klyngen ved hjælp af portalen Azure. 

> [AZURE.NOTE] Azure PowerShell, Azure CLI, HDInsight .NET SDK eller Azure ressourcestyring skabeloner kan også bruges til at anvende scripthandlinger. Du kan også anvende scripthandlinger på allerede kører klynger. Se [tilpasse HDInsight klynger med scripthandlinger](hdinsight-hadoop-customize-cluster-linux.md)kan finde flere oplysninger.

1. Start med at oprette en klynge ved hjælp af trinnene i [oprette Linux-baserede HDInsight klynger](hdinsight-hadoop-create-linux-clusters-portal.md), men ikke fuldføre oprettelse af.

2. Vælg **Scripthandlinger**på bladet **Valgfri konfiguration** , og angiv oplysningerne nedenfor:

    * __Navn__: Angiv et fuldt navn for handlingen script.
    * __SCRIPT URI__: https://hdiconfigactions.blob.core.windows.net/linuxgiraphconfigactionv01/giraph-installer-v01.sh
    * __Afsnit__: kontrollere denne indstilling
    * __Arbejder__: Lad dette ikke er markeret
    * __ZOOKEEPER__: Lad dette ikke er markeret
    * __Parametre__: Lad dette felt stå tomt

3. I bunden af **Scripthandlinger**, kan du bruge knappen **Vælg** til at gemme konfigurationen. Til sidst skal bruge knappen **Vælg** i bunden af bladet **Valgfri konfiguration** til at gemme oplysningerne om valgfri konfiguration.

4. Fortsætte med at oprette klyngen, som beskrevet i [oprette Linux-baserede HDInsight klynger](hdinsight-hadoop-create-linux-clusters-portal.md).

## <a name="usegiraph"></a>Hvordan bruger jeg Giraph i HDInsight?

Når klyngen er færdig med at oprette, kan du bruge følgende trin til at køre eksemplet SimpleShortestPathsComputation inkluderet med Giraph. Dette implementerer den grundlæggende <a href = "http://people.apache.org/~edwardyoon/documents/pregel.pdf">Pregel</a> implementering til at finde den hurtigste vej til objekter i et diagram.

1. Oprette forbindelse til den HDInsight klynge, ved hjælp af SSH:

        ssh USERNAME@CLUSTERNAME-ssh.azurehdinsight.net

    Du kan finde flere oplysninger om brug af SSH med HDInsight, se følgende:

    * [Bruge SSH med Linux-baserede Hadoop på HDInsight fra Linux, Unix eller OS X](hdinsight-hadoop-linux-use-ssh-unix.md)

    * [Bruge SSH med Linux-baserede Hadoop på HDInsight fra Windows](hdinsight-hadoop-linux-use-ssh-windows.md)

1. Du kan bruge følgende til at oprette en ny fil med navnet __tiny_graph.txt__:

        nano tiny_graph.txt

    Du kan bruge følgende som indholdet af denne fil:

        [0,0,[[1,1],[3,3]]]
        [1,0,[[0,1],[2,2],[3,1]]]
        [2,0,[[1,2],[4,4]]]
        [3,0,[[0,3],[1,1],[4,4]]]
        [4,0,[[3,4],[2,4]]]

    Disse data i denne artikel beskrives en relation mellem objekter i en direkte graf ved hjælp af formatet [kilde\_-id, kilde\_værdi, [[dest\_id], [kant\_værdi];...]]. Hver linje repræsenterer en relation mellem en **kilde\_id** objekt og en eller flere **dest\_id** objekter. Den **kant\_værdi** (eller vægt) kan betragtes som en styrke eller afstand af forbindelsen mellem **source_id** og **dest\_id**.

    Tegnet, og brug af værdien (eller vægt) som afstanden mellem objekter, ovennævnte data kan se sådan ud:

    ![tiny_graph.txt tegnet som cirkler med skiftende afstanden mellem tekstlinjer](./media/hdinsight-hadoop-giraph-install-linux/giraph-graph.png)

2. For at gemme filen, kan du bruge __Ctrl + X__, og derefter __Y__, og til sidst __Enter__ til at acceptere filnavnet.

3. Du kan bruge følgende til at gemme dataene i primære lagerplads på din HDInsight klynge:

        hdfs dfs -put tiny_graph.txt /example/data/tiny_graph.txt

4. Køre eksemplet SimpleShortestPathsComputation ved hjælp af følgende kommando.

         yarn jar /usr/hdp/current/giraph/giraph-examples.jar org.apache.giraph.GiraphRunner org.apache.giraph.examples.SimpleShortestPathsComputation -ca mapred.job.tracker=headnodehost:9010 -vif org.apache.giraph.io.formats.JsonLongDoubleFloatDoubleVertexInputFormat -vip /example/data/tiny_graph.txt -vof org.apache.giraph.io.formats.IdWithValueTextOutputFormat -op /example/output/shortestpaths -w 2

    De parametre, bruges sammen med denne kommando er beskrevet i følgende tabel.

  	| Parameter | Hvad gør den |
  	| --------- | ------------ |
  	| `jar /usr/hdp/current/giraph/giraph-examples.jar` | Glas filen med eksemplerne. |
  	| `org.apache.giraph.GiraphRunner` | Den klasse, der bruges til at starte eksemplerne. |
  	| `org.apache.giraph.examples.SimpleShortestPathsCoputation` | Eksempel, der skal være kørte. I dette tilfælde vil den beregne den hurtigste vej til ID 1 og alle andre id'er i diagrammet. |
  	| `-ca mapred.job.tracker=headnodehost:9010` | Headnode for-klyngen. |
  	| `-vif org.apache.giraph.io.formats.JsonLongDoubleFloatDoubleVertexInputFromat` | Formatet input skal bruges til at inputdataene. |
  	| `-vip /example/data/tiny_graph.txt` | Input-datafil. |
  	| `-vof org.apache.giraph.io.formats.IdWithValueTextOutputFormat` | Outputformat. I dette tilfælde ID og værdi som almindelig tekst. |
  	| `-op /example/output/shortestpaths` | Outputplacering. |
  	| `-w 2` | Antallet af medarbejdere bruge. I dette tilfælde 2. |

    Du kan finde flere oplysninger om disse og andre parametre, bruges sammen med eksempler på Giraph, [Giraph Hurtig start](http://giraph.apache.org/quick_start.html).

5. Når jobbet er afsluttet, gemmes resultaterne i den __wasbs: / / / eksempel/ud/shotestpaths__ directory. De filer, der er oprettet begynder med __del-m -__ og ender med et tal, der angiver først, anden, osv. fil. Bruge følgende for at få vist output:

        hdfs dfs -text /example/output/shortestpaths/*

    Output skal se ud som følger:

        0   1.0
        4   5.0
        2   2.0
        1   0.0
        3   1.0

    SimpleShortestPathComputation eksempel er svært indkodet skal starte med objekt ID 1, og find den hurtigste vej til andre objekter. Så output skal læses som `destination_id distance`, hvor afstanden er værdien (eller vægt) af kanterne kørte kilometer mellem objekt ID 1 og mål-ID.

    Visualisere, kan du kontrollere resultaterne ved rejse kortest stier mellem ID 1 og alle andre objekter. Bemærk, at den hurtigste vej til ID 1 og 4-ID 5. Dette er den samlede afstand mellem <span style="color:orange">ID 1 og 3</span>og derefter <span style="color:red">ID 3 og 4</span>.

    ![Tegning af objekter som cirkler med kortest stier, der er tegnet mellem](./media/hdinsight-hadoop-giraph-install-linux/giraph-graph-out.png)


## <a name="next-steps"></a>Næste trin

- [Installation og brug farvetone på HDInsight klynger](hdinsight-hadoop-hue-linux.md). Nuance er et websted brugergrænseflade, der gør det nemt at oprette, køre, og Gem gris og Hive job samt Gennemse standard lagerplads til dit HDInsight klynge.

- [Installere R på HDInsight klynger](hdinsight-hadoop-r-scripts-linux.md): oplysninger om, hvordan du bruger klynge tilpasning for at installere og bruge R på HDInsight Hadoop klynger. R er et open source-sprog og miljø til statistiske computing. Den indeholder hundredvis af indbyggede statistiske funktioner og sin egen programmeringssprog, der kombinerer aspekter af funktionelle og objektorienteret programmering. Den indeholder også omfattende grafiske funktioner.

- [Installere Solr på HDInsight klynger](hdinsight-hadoop-solr-install-linux.md). Bruge klynge tilpasning til at installere Solr på HDInsight Hadoop klynger. Solr giver dig mulighed at udføre effektiv søgning handlinger på data, der gemmes.
