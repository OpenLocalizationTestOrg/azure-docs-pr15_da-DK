<properties
 pageTitle="Eksempel Apache Storm topologier på HDInsight | Microsoft Azure"
 description="En liste over eksempel Storm topologier oprettes og testes med Apache Storm på HDInsight herunder grundlæggende C# og Java topologier, og arbejde med begivenhed Hubs."
 services="hdinsight"
 documentationCenter=""
 authors="Blackmist"
 manager="jhubbard"
 editor="cgronlun"
    tags="azure-portal"/>

<tags
 ms.service="hdinsight"
 ms.devlang="na"
 ms.topic="article"
 ms.tgt_pltfrm="na"
 ms.workload="big-data"
 ms.date="08/23/2016"
 ms.author="larryfr"/>

# <a name="example-storm-toplogies-and-components-for-apache-storm-on-hdinsight"></a>Eksempel Storm toplogies og -komponenter til Apache Storm på HDInsight

Følgende er en liste over eksempler oprettes og vedligeholdes af Microsoft til brug sammen med Apache Storm på HDInsight. Disse eksempler dækker en lang række emner i at oprette grundlæggende C# og Java topologier til at arbejde med Azure tjenester som begivenhed Hubs DocumentDB Power BI, SQL-Database, og HBase på HDInsight og Azure-lager. Nogle eksempler viser også, hvordan du arbejder med ikke-Azure eller endda ikke-Microsoft teknologier, som SignalR og Socket.IO

| Beskrivelse                                                                                             | Viser                                         | Sprog/Framework         |
|:--------------------------------------------------------------------------------------------------------|:-----------------------------------------------------|:---------------------------|
| [Skrive i Azure datalager sø fra Apache Storm](hdinsight-storm-write-data-lake-store.md) | Skrivning til Azure sø datalager | Java |
| [Begivenhed Hub Spout og Bolt kilde](https://github.com/apache/storm/tree/master/external/storm-eventhubs) | Kilde til begivenhed Hub tud og Bolt | Java |
| [Udvikle Java-baserede topologier for Apache Storm på HDInsight][5797064f]                                 | Maven                                                | Java                       |
| [Udvikle C# topologier for Apache Storm på HDInsight ved hjælp af Visual Studio][16fce2d1]                     | HDInsight værktøjer til Visual Studio                    | C#, Java                   |
| [Oprette flere datastreams i en C# Storm topologi][ec5a4064]                                         | Flere streams                                     | C#                         |
| [Bestemme Twitter cirkulerer emner med Storm på HDInsight][3c86c7c8]                                   | Trident                                              | Java, Trident              |
| [Håndtere hændelser fra Azure begivenhed hubber med Storm på HDInsight (C#)][844d1d81]                                | Begivenhed Hubs                                           | C# og Java                |
| [Håndtere hændelser fra Azure begivenhed hubber med Storm på HDInsight (Java)](hdinsight-storm-develop-java-event-hub-topology.md) | Begivenhed Hubs | Java |
| [Bruge Power Bi til at visualisere data fra en Storm topologi][94d15238]                              | Power BI                                             | C#                         |
| [Analysere føler data med Storm og HBase i HDInsight][ab894747]                                     | Begivenhed Hubs, HBase, Socket.IO, Web dashboard          | C#, Java, JavaScript, HTML |
| [Behandle biltype føler data fra begivenhed Hubs ved hjælp af Storm på HDInsight][246ee964]                        | Begivenhed Hubs, DocumentDb, Azure lagerplads Blob (WASB)    | C#, Java                   |
| [Udtrække, transformation og indlæsning (ETL) fra Azure begivenhed Hubs til HBase, ved hjælp af Storm på HDInsight][b4b68194] | Begivenhed Hubs, HBase                                    | C#                         |
| [Skabelon C# Storm topologi project til at arbejde med Azure tjenester fra Storm på HDInsight][ce0c02a2]  | Begivenhed Hubs, DocumentDb, SQL-Database, HBase, SignalR | C#, Java                   |
| [Skalerbarhed benchmarks læsevenligheden fra Azure begivenhed Hubs ved hjælp af Storm på HDInsight][d6c540e3]           | Meddelelse overførselshastighed, begivenhed Hubs, SQL-Database         | C#, Java                   |
| [Koordinere begivenheder ved hjælp af Storm og HBase på HDInsight](hdinsight-storm-correlation-topology.md) | HBase | C# |
| [Bruge Python med Storm på HDInsight](hdinsight-storm-develop-python-topology.md) | Python komponenter med Java og Clojure baseret Storm topologier | Python |

## <a name="next-steps"></a>Næste trin

* [Komme i gang med Apache Storm på HDInsight][2b8c3488]

* [Lær, hvordan du installere og administrere Storm topologier med Storm på HDInsight][6eb0d3b8]

  [2b8c3488]: hdinsight-apache-storm-tutorial-get-started-linux.md "Lær, hvordan du opretter en Storm på HDInsight klynge og bruger Storm Dashboard til at udrulle eksempel topologier."
  [6eb0d3b8]: hdinsight-storm-deploy-monitor-topology.md "Lær at installere og administrere topologier ved hjælp af dashboardet webbaseret Storm og Storm brugergrænseflade eller HDInsight værktøjerne til Visual Studio."
  [16fce2d1]: hdinsight-storm-develop-csharp-visual-studio-topology.md "Få mere at vide, hvordan du opretter C# Storm topologier ved hjælp af værktøjerne HDInsight til Visual Studio."
  [5797064f]: hdinsight-storm-develop-java-topology.md "Lær, hvordan du opretter Storm topologier i Java, ved hjælp af Maven, ved at oprette en grundlæggende wordcount topologi."
  [94d15238]: hdinsight-storm-power-bi-topology.md "Viser, hvordan du skriver data til Power BI fra en C#-topologi, og derefter oprette et diagram og dashboard ud fra data."
  [ec5a4064]: https://github.com/Blackmist/csharp-storm-example "Viser en grundlæggende Storm topologi, som udfører en wordcount, der er implementeret i C#. Dette viser også, hvordan du opretter flere datastreams inden for en C#-topologi."
  [844d1d81]: hdinsight-storm-develop-csharp-event-hub-topology.md "Lær at læse og skrive data fra Azure begivenhed hubber med Storm på HDInsight."
  [ab894747]: hdinsight-storm-sensor-data-analysis.md "Lær, hvordan du bruger Apache Storm på HDInsight til at behandle føler data fra Azure begivenhed Hubs, visualisere den ved hjælp af D3.js, og Gem den (valgfrit) til HBase."
  [3c86c7c8]: hdinsight-storm-twitter-trending.md "Lær at bruge Trident til at oprette en Storm topologi, der bestemmer cirkulerer emner (baseret på hashtags,) på Twitter."
  [246ee964]: hdinsight-storm-iot-eventhub-documentdb.md "Lær, hvordan du bruger en Storm topologi til at læse meddelelser fra Azure begivenhed Hubs, læse dokumenter fra Azure DocumentDB som reference til data og gemme data til Azure-lager."
  [d6c540e3]: https://github.com/hdinsight/hdinsight-storm-examples/blob/master/EventCountExample "Flere topologier til at vise overførselshastighed, når læsning fra Azure begivenhed Hubs og lagring af til SQL-Database ved hjælp af Apache Storm på HDInsight."
  [b4b68194]: https://github.com/hdinsight/hdinsight-storm-examples/blob/master/RealTimeETLExample "Lær at læse data fra Azure begivenhed Hubs, samling og transformere dataene, og derefter gemme den til HBase på HDInsight."
  [ce0c02a2]: https://github.com/hdinsight/hdinsight-storm-examples/tree/master/templates/HDInsightStormExamples "Dette projekt indeholder skabeloner til spouts, bolte og topologier til at interagere med forskellige Azure tjenester som begivenhed Hubs, DocumentDB og SQL-Database."
 
