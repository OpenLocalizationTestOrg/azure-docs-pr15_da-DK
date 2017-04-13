<properties 
    pageTitle="Bruge Hive med Hadoop til websted log analyse | Microsoft Azure" 
    description="Lær at bruge Hive med HDInsight til at analysere websted logfiler. Du skal bruge en logfil som input til en HDInsight tabel og bruger HiveQL forespørge på data." 
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
    ms.date="05/17/2016" 
    ms.author="nitinme"/>

# <a name="use-hive-with-hdinsight-to-analyze-logs-from-websites"></a>Bruge Hive med HDInsight til at analysere logfiler fra websteder

Lær at bruge HiveQL med HDInsight til at analysere logfiler fra et websted. Websted log analyse kan bruges til målgruppe dit publikum, der er baseret på lignende aktiviteter, kategorisere besøgende ved demografiske data, og at finde ud af indholdet de visning, websteder, de kommer fra osv.

Du skal bruge en HDInsight klynge til at analysere websted logfiler for at få indsigt i hyppigheden for besøg på webstedet fra eksterne websteder på en dag i dette eksempel. Du kan også oprette en oversigt over websted fejl, som brugerne oplever. Skal du lære, hvordan du:

- Oprette forbindelse til en Azure Blob-lager, som indeholder websted logfiler.
- Oprette HIVE tabeller, hvis du vil forespørge disse logfiler.
- Oprette HIVE forespørgsler for at analysere dataene.
- Bruge Microsoft Excel til at oprette forbindelse til HDInsight (ved hjælp af open database connectivity (ODBC) til at hente den analyserede data.

![HDI. Samples.Website.Log.Analysis][img-hdi-weblogs-sample]

##<a name="prerequisites"></a>Forudsætninger

- Du skal har klargjort en Hadoop klynge på Azure HDInsight. Flere oplysninger under [Klargøring HDInsight klynger][hdinsight-provision]. 
- Du skal have Microsoft Excel 2013 eller Excel 2010 installeret.
- Du skal have [Microsoft Hive ODBC-Driver](http://www.microsoft.com/download/details.aspx?id=40886) at importere data fra Hive til Excel.


##<a name="to-run-the-sample"></a>Køre stikprøvernes

1. Fra [Azure-portalen](https://portal.azure.com/), fra Startboard (Hvis du fastgjort klynge der), klik på feltet klynge, du vil køre eksemplet.

2. Klik på **Klynge Dashboard**bladet klynge under **Hurtige Links**, og klik derefter på **HDInsight klynge Dashboard**fra bladet **Klynge Dashboard** . Du kan også åbne dashboardet direkte ved hjælp af følgende URL-adressen:

        https://<clustername>.azurehdinsight.net
    
    Når du bliver bedt om det, du Godkend ved hjælp af administratorens brugernavn og adgangskode, du brugte, når klyngen.
  
2. Klik på fanen **Kom Introduktion galleri** fra den webside, der åbnes, og klik derefter på **Websted Log analyse** stikprøvernes under kategorien **løsninger med eksempeldata** .

3. Følg instruktionerne på websiden for at afslutte eksemplet.

##<a name="next-steps"></a>Næste trin
Prøv følgende eksempel: [analysere føler data ved hjælp af Hive med HDInsight](hdinsight-hive-analyze-sensor-data.md).


[hdinsight-provision]: hdinsight-provision-clusters.md
[hdinsight-sensor-data-sample]: ../hdinsight-use-hive-sensor-data-analysis.md

[img-hdi-weblogs-sample]: ./media/hdinsight-hive-analyze-website-log/hdinsight-weblogs-sample.png
 
