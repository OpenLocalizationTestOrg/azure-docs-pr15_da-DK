<properties 
    pageTitle="Kalde knallertmotor programmer fra Azure Data Factory" 
    description="Lær at kalde knallertmotor programmer fra en Azure data factory ved hjælp af den MapReduce aktivitet." 
    services="data-factory" 
    documentationCenter="" 
    authors="spelluru" 
    manager="jhubbard" 
    editor="monicar"/>

<tags 
    ms.service="data-factory" 
    ms.workload="data-services" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="08/25/2016" 
    ms.author="spelluru"/>

# <a name="invoke-spark-programs-from-data-factory"></a>Kalde knallertmotor programmer fra Data Factory
## <a name="introduction"></a>Introduktion
Du kan bruge den MapReduce aktivitet i en Data Factory pipeline for at køre knallertmotor programmer på din HDInsight Spark klynge. Se [MapReduce aktivitet](data-factory-map-reduce.md) artikel for at få detaljerede oplysninger om brug af aktiviteten før læse denne artikel. 

## <a name="spark-sample-on-github"></a>Knallertmotor eksempel på GitHub
[Knallertmotor - Data Factory eksempel på GitHub](https://github.com/Azure/Azure-DataFactory/tree/master/Samples/Spark) viser, hvordan du bruge MapReduce aktivitet til at starte et styret program. Knallertmotor kopieres lige data fra én Azure Blob-objektbeholder til en anden. 

## <a name="data-factory-entities"></a>Data Factory enheder
Mappen **Knallertmotor-ADF/src/ADFJsons** indeholder filer til Data Factory-enheder (sammenkædede tjenester, datasæt, pipeline).  

Der er to **sammenkædede tjenester** i dette eksempel: Azure-lager og Azure HDInsight. Angiv din Azure lagerplads navn og nøgleværdier i **StorageLinkedService.json** og clusterUri, brugernavn og adgangskode i **HDInsightLinkedService.json**.

Der er to **datasæt** i dette eksempel: **input.json** og **output.json**. Disse filer kan findes i mappen **datasæt** .  Disse filer repræsenterer input- og outputområder datasæt for MapReduce aktivitet

Du kan finde eksempel rørledninger i mappen **ADFJsons/Pipeline** . Gennemse en rørledning for at forstå, hvordan du aktiverer et styret program ved hjælp af MapReduce aktiviteten. 

MapReduce aktiviteten er konfigureret til at kalde **com.adf.sparklauncher.jar** i objektbeholderen **adflibs** i dit Azure-lager (angivet i StorageLinkedService.json). Koden for dette program er i knallertmotor-ADF/src/hovedmenuen/java/com/adf/mappe og det kalder knallertmotor sende og køre knallertmotor job. 

> [AZURE.IMPORTANT] 
> Læs gennem [vigtige oplysninger om. TXT](https://github.com/Azure/Azure-DataFactory/blob/master/Samples/Spark/README.txt) for de seneste og yderligere oplysninger inden du bruger eksemplet. 
>  
> Bruge dit eget HDInsight Spark klynge med denne metode til at starte knallertmotor programmer, der bruger MapReduce aktiviteten. Brug af en efter behov HDInsight klynge understøttes ikke.   


## <a name="see-also"></a>Se også
- [Hive aktivitet](data-factory-hive-activity.md)
- [Gris aktivitet](data-factory-pig-activity.md)
- [MapReduce aktivitet](data-factory-map-reduce.md)
- [Hadoop Streaming aktivitet](data-factory-hadoop-streaming-activity.md)
- [Kalde R scripts](https://github.com/Azure/Azure-DataFactory/tree/master/Samples/RunRScriptUsingADFSample)
