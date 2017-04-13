<properties
    pageTitle="Installer R på Linux-baserede HDInsight | Microsoft Azure"
    description="Lær at installere og bruge R til at tilpasse Linux-baserede Hadoop klynger."
    services="hdinsight"
    documentationCenter=""
    authors="Blackmist"
    manager="jhubbard"
    editor="cgronlun"/>

<tags
    ms.service="hdinsight"
    ms.workload="big-data"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/20/2016"
    ms.author="larryfr"/>

# <a name="install-and-use-r-on-hdinsight-hadoop-clusters"></a>Installere og bruge R på HDInsight Hadoop klynger

Du kan installere R på en hvilken som helst type klynge i Hadoop på HDInsight ved hjælp af **Scripthandling** klynge tilpasning. Dette gør det muligt for data forskere og analytikere for at bruge R til at implementere en effektiv MapReduce/GARN programming ramme til at behandle store datamængder på Hadoop klynger, der er implementeret i HDInsight.

> [AZURE.IMPORTANT] Det [premium niveau](https://azure.microsoft.com/pricing/details/hdinsight/) tilbyder for HDInsight omfatter R Server som en del af din HDInsight klynge. Dette giver mulighed for at bruge MapReduce og knallertmotor til at køre fordelt beregninger R scripts. Yderligere oplysninger finder du se [Introduktion til brug af R Server på HDInsight](hdinsight-hadoop-r-server-get-started.md). 


## <a name="what-is-r"></a>Hvad er R?

<a href="http://www.r-project.org/" target="_blank">R projekt til statistiske Computing</a> er en åben kildesprog og miljø til statistiske computing. R indeholder hundredvis af indbygget statistiske funktioner og sin egen programmeringssprog, der kombinerer aspekter af funktionelle og objektorienteret programmering. Den indeholder også omfattende grafiske funktioner. R er det foretrukne programming miljø til mest professionel statistikere og forskere i en række forskellige felter.

R-scripts kan køres på Hadoop klynger i HDInsight, der er defineret ved hjælp af Script handlingen oprettelsen af installere R-miljø. R er kompatibelt med Azure Blob Storage (WASB), så data, der er gemt der kan udføres ved hjælp af R på HDInsight.

## <a name="what-the-script-does"></a>Hvad betyder det scriptet

Handlingen script, der bruges til at installere R på din HDInsight klynge installationer følgende Ubuntu-pakker, som giver en grundlæggende R-installation:

* [r-base](http://packages.ubuntu.com/precise/r-base): grundlæggende GNU-R-pakke
* [r-base-Udviklingscenter](http://packages.ubuntu.com/precise/r-base-dev): Auxilliary GNU R-pakker

Følgende RHadoop-pakker er også installeret, som giver integration med MapReduce og HDFS:

* [rmr2](https://github.com/RevolutionAnalytics/rmr2): kan R udviklere bruge Hadoop MapReduce
* [rhdfs](https://github.com/RevolutionAnalytics/rhdfs): kan R udviklere bruge Hadoop HDFS (WASB for HDInsight)

Desuden er følgende R-pakker installeret:

| R-pakke | Hvad kan |
| --------- | ---------------- |
| [rJava](https://cran.r-project.org/web/packages/rJava/index.html) | Et lavt niveau R til Java brugergrænsefladen. |
| [Rcpp](https://cran.r-project.org/web/packages/Rcpp/index.html) | R og C++ integration. |
| [RJSONIO](https://cran.r-project.org/web/packages/RJSONIO/index.html) | Sekventielt/deserialisere R objekter til JSON |
| [bitops](https://cran.r-project.org/web/packages/bitops/index.html) | Funktioner til bitvise handlinger på heltal vektorer. |
| [Samlet](https://cran.r-project.org/web/packages/digest/index.html) | Oprette Cryptographic Hash prøverne R objekter. |
| [funktionelle](https://cran.r-project.org/web/packages/functional/index.html) | Curran, Opret og andre højere ordre-funktioner |
| [reshape2](https://cran.r-project.org/web/packages/reshape2/index.html) | Fleksibelt omstrukturering og aggregere data. |
| [stringr](https://cran.r-project.org/web/packages/stringr/index.html) | Konsistent wrappere til almindelige strengfunktioner. |
| [plyr](https://cran.r-project.org/web/packages/plyr/index.html) | Værktøjer til at opdele, anvende og Data kombineres. |
| [caTools](https://cran.r-project.org/web/packages/caTools/index.html) | Værktøjer til at flytte vinduet Statistik, GIF, Base64, ROC AUC osv. |
| [stringdist](https://cran.r-project.org/web/packages/stringdist/index.html) | Omtrentlig streng tilsvarende og streng afstanden funktioner. |

## <a name="install-r-using-script-actions"></a>Installere R ved hjælp af scripthandlinger

Handlingen følgende script bruges til at installere R på en HDInsight klynge. https://hdiconfigactions.BLOB.Core.Windows.NET/linuxrconfigactionv01/r-Installer-v01.sh
    
Dette afsnit indeholder oplysninger om, hvordan du bruger scriptet, når du opretter en ny klynge ved hjælp af portalen Azure. 

> [AZURE.NOTE] Azure PowerShell, Azure CLI, HDInsight .NET SDK eller Azure ressourcestyring skabeloner kan også bruges til at anvende scripthandlinger. Du kan også anvende scripthandlinger på allerede kører klynger. Se [tilpasse HDInsight klynger med scripthandlinger](hdinsight-hadoop-customize-cluster-linux.md)kan finde flere oplysninger.

1. Start klargøring en klynge ved hjælp af trinnene i [klargøring Linux-baserede HDInsight klynger](hdinsight-hadoop-provision-linux-clusters.md#portal), men ikke fuldføre klargøring.

2. Vælg **Scripthandlinger**på bladet **Valgfri konfiguration** , og angiv oplysningerne nedenfor:

    * __Navn__: Angiv et fuldt navn for handlingen script.
    * __SCRIPT URI__: https://hdiconfigactions.blob.core.windows.net/linuxrconfigactionv01/r-installer-v01.sh
    * __Afsnit__: kontrollere denne indstilling
    * __Arbejder__: kontrollere denne indstilling
    * __ZOOKEEPER__: Markér denne indstilling for at installere på noden Zookeeper.
    * __Parametre__: Lad dette felt stå tomt

3. I bunden af **Scripthandlinger**, kan du bruge knappen **Vælg** til at gemme konfigurationen. Til sidst skal bruge knappen **Vælg** i bunden af bladet **Valgfri konfiguration** til at gemme oplysningerne om valgfri konfiguration.

4. Fortsæt med klargøring klyngen, som beskrevet i [klargøring Linux-baserede HDInsight klynger](hdinsight-hadoop-provision-linux-clusters.md#portal).

## <a name="run-r-scripts"></a>Køre R scripts

Efter klyngen er blevet klargøring, skal du følge nedenstående trin for at bruge R til at udføre en handling af MapReduce på klyngen.

1. Oprette forbindelse til den HDInsight klynge, ved hjælp af SSH:

        ssh USERNAME@CLUSTERNAME-ssh.azurehdinsight.net

    Du kan finde flere oplysninger om brug af SSH med HDInsight, se følgende:

    * [Bruge SSH med Linux-baserede Hadoop på HDInsight fra Linux, Unix eller OS X](hdinsight-hadoop-linux-use-ssh-unix.md)

    * [Bruge SSH med Linux-baserede Hadoop på HDInsight fra Windows](hdinsight-hadoop-linux-use-ssh-windows.md)

2. Fra den `username@hn0-CLUSTERNAME:~$` Spørg, skal du angive følgende kommando for at starte en interaktiv R-session:

        R

3. Angiv følgende R program. Dette genererer tal 1 til 100 og multiplicerer dem med 2.

        library(rmr2)
        ints = to.dfs(1:100)
        calc = mapreduce(input = ints, map = function(k, v) cbind(v, 2*v))

    Den første linje kalder RHadoop bibliotek rmr2, som bruges til MapReduce handlinger.

    Den anden linje genererer værdier 1-100, og derefter gemmer dem til Hadoop-fil system via `to.dfs`.

    Den tredje linje opretter en MapReduce proces, ved hjælp af funktioner, der leveres af rmr2 og begynder behandling. Du bør se flere linjer rulle tidligere, som behandlingen begynder.

4. Nu skal bruge følgende for at se den midlertidige sti, som blev gemt MapReduce output til:

        print(calc())

    Dette bør være noget lignende `/tmp/file5f615d870ad2`. For at få vist den aktuelle afgang, bruge følgende:

        print(from.dfs(calc))

    Output skal se sådan ud:

        [1,]  1 2
        [2,]  2 4
        .
        .
        .
        [98,]  98 196
        [99,]  99 198
        [100,] 100 200

5. At afslutte R, skal du angive følgende:

        q()


## <a name="next-steps"></a>Næste trin

- [Installation og brug farvetone på HDInsight klynger](hdinsight-hadoop-hue-linux.md). Nuance er en web brugergrænseflade, der gør det nemt at oprette, køre, og Gem gris og Hive job samt Gennemse standard lagerplads til dit HDInsight klynge.

- [Installere Giraph på HDInsight klynger](hdinsight-hadoop-giraph-install.md). Bruge klynge tilpasning til at installere Giraph på HDInsight Hadoop klynger. Giraph gør det muligt at udføre graph behandling ved hjælp af Hadoop, og den kan bruges med Azure HDInsight.

- [Installere Solr på HDInsight klynger](hdinsight-hadoop-solr-install.md). Bruge klynge tilpasning til at installere Solr på HDInsight Hadoop klynger. Solr giver dig mulighed at udføre effektiv søgning handlinger på lagrede data.

- [Installere farvetone på HDInsight klynger](hdinsight-hadoop-hue-linux.md). Bruge klynge tilpasning til at installere farvetone på HDInsight Hadoop klynger. Nuance er et sæt af webprogrammer, der bruges til at interagere med en Hadoop-klynge.

[hdinsight-cluster-customize]: hdinsight-hadoop-customize-cluster-linux.md
