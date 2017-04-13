<properties
 pageTitle="Behandle biltype føler data med Apache Storm på HDInsight | Microsoft Azure"
 description="Lær at behandle biltype føler data fra begivenhed Hubs ved hjælp af Apache Storm på HDInsight. Tilføje modeldata fra DocumentDB, og Gem output til lager."
 services="hdinsight,documentdb,notification-hubs"
 documentationCenter=""
 authors="Blackmist"
 manager="jhubbard"
 editor="cgronlun"/>

<tags
ms.service="hdinsight"
ms.devlang="java"
ms.topic="article"
ms.tgt_pltfrm="na"
ms.workload="big-data"
ms.date="08/23/2016"
ms.author="larryfr"/>

#<a name="process-vehicle-sensor-data-from-azure-event-hubs-using-apache-storm-on-hdinsight"></a>Behandle biltype føler data fra Azure begivenhed Hubs ved hjælp af Apache Storm på HDInsight

Lær at behandle biltype føler data fra Azure begivenhed Hubs ved hjælp af Apache Storm på HDInsight. I dette eksempel læser føler data fra Azure begivenhed Hubs, beriger dataene ved at referere til data, der er gemt i Azure DocumentDB og til sidst lagre dataene til Azure-lager, med Hadoop-fil System (HDFS).

![HDInsight og Internet af ting (IoT) arkitektur diagrammet](./media/hdinsight-storm-iot-eventhub-documentdb/iot.png)

##<a name="overview"></a>Oversigt

Føje sensorer til køretøjer, kan du forudsige udstyr problemer, der er baseret på historiske datatendenser samt at forbedre fremtidige versioner, der er baseret på mønster besøgsanalyse. Mens traditionelt MapReduce batchbehandling kan bruges til denne analyse, skal du kunne hurtigt og effektivt indlæser data fra alle køretøjer til Hadoop før MapReduce behandling kan forekomme. Desuden kan du gøre analyse for kritisk fejl stier (program temperaturen, bremser osv.) i realtid.

Azure begivenhed Hubs er bygget til at håndtere store mængde data, der genereres af sensorer og Apache Storm på HDInsight kan bruges til at indlæse og behandle data før den gemmes i HDFS (sikkerhedskopien af Azure-lager) til yderligere MapReduce behandling.

##<a name="solution"></a>Løsning

Telemetridata til program temperaturen, omgivende temperatur og biltype hastighed registreres ved sensorer, derefter sendes til begivenhed hubber sammen med den bil biltype identifikation tal (VIN) og et tidsstempel. Derfra en Storm topologi, der kører på en Apache Storm på HDInsight klynge læser data, behandler den og gemmer den i HDFS.

Under behandling bruges VIN til at hente oplysninger om modellen fra Azure DocumentDB. Dette er føjet til datastrømmen, før den er gemt.

De komponenter, der bruges i Storm topologien er:

* **EventHubSpout** - læser data fra Azure begivenhed Hubs

* **TypeConversionBolt** - Konverterer strengen JSON fra begivenhed Hubs til en tupel, der indeholder de individuelle værdier for program temperaturen, omgivende temperatur, hastighed, VIN og tidsstempel

* **DataReferencBolt** - søger efter biltype modellen fra DocumentDB ved hjælp af VIN

* **WasbStoreBolt** - gemmer dataene til HDFS (Azure-lager)

Følgende er et diagram over denne løsning:

![storm topologi](./media/hdinsight-storm-iot-eventhub-documentdb/iottopology.png)

> [AZURE.NOTE] Dette er et forenklet diagram, og hver komponent i løsningen kan have flere forekomster. For eksempel er på flere forekomster af hver komponent i topologien fordelt over knuderne i Storm på HDInsight klynge.

##<a name="implementation"></a>Implementering

En komplet, automatisk løsning til dette scenario er tilgængelig som en del af [HDInsight-Storm-eksempler](https://github.com/hdinsight/hdinsight-storm-examples) lageret på GitHub. For at bruge dette eksempel skal du følge trinnene i [IoTExample README. MD](https://github.com/hdinsight/hdinsight-storm-examples/blob/master/IotExample/README.md).

## <a name="next-steps"></a>Næste trin

Du kan finde flere eksempel Storm topologier [eksempel topologier for Storm på HDInsight](hdinsight-storm-example-topology.md).
