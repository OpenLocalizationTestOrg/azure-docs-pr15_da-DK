<properties
    pageTitle="Analysere føler data ved hjælp af Hive og Hadoop | Microsoft Azure"
    description="Lær at analysere føler data ved hjælp af konsollen Hive forespørgsel med HDInsight (Hadoop), og derefter få vist dataene i Microsoft Excel med Power View."
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
    ms.date="09/20/2016" 
    ms.author="larryfr"/>

#<a name="analyze-sensor-data-using-the-hive-query-console-on-hadoop-in-hdinsight"></a>Analysere føler data ved hjælp af konsollen Hive forespørgsel på Hadoop i HDInsight

Lær at analysere føler data ved hjælp af konsollen Hive forespørgsel med HDInsight (Hadoop), og derefter få vist dataene i Microsoft Excel ved hjælp af Power View.

> [AZURE.NOTE] Trinnene i dette dokument virker kun med Windows-baseret HDInsight klynger.

I dette eksempel skal du bruge Hive til proces historiske data produceret af varme, ventilation og aircondition (VVS) systemer til at identificere systemer, der ikke er stand til at opretholde et sæt temperaturen pålideligt. Skal du lære, hvordan du:

- Oprette HIVE tabeller til forespørgsel efter data gemt i filer med kommaseparerede værdier (CSV).
- Oprette HIVE forespørgsler for at analysere dataene.
- Bruge Microsoft Excel til at oprette forbindelse til HDInsight (ved hjælp af open database connectivity (ODBC) til at hente den analyserede data.
- Brug Power View til at få vist dataene.

![Et diagram over løsningsarkitekturen](./media/hdinsight-hive-analyze-sensor-data/hvac-architecture.png)

##<a name="prerequisites"></a>Forudsætninger

* En HDInsight (Hadoop) klynge: Du kan finde oplysninger om oprettelse af en klynge [klargøring Hadoop klynger i HDInsight](hdinsight-provision-clusters.md) .

* Microsoft Excel 2013

    > [AZURE.NOTE] Microsoft Excel bruges til datavisualisering med [Power View](https://support.office.com/Article/Power-View-Explore-visualize-and-present-your-data-98268d31-97e2-42aa-a52b-a68cf460472e?ui=en-US&rs=en-US&ad=US).

* [Microsoft Hive ODBC-Driver](http://www.microsoft.com/download/details.aspx?id=40886)

##<a name="to-run-the-sample"></a>Køre stikprøvernes

1. Fra din webbrowser skal du gå til følgende URL-adressen. Erstatte `<clustername>` med navnet på din HDInsight klynge.

        https://<clustername>.azurehdinsight.net

    Når du bliver bedt om det, du Godkend ved hjælp af administratorens brugernavn og adgangskode, du brugte, når denne klynge.

2. Klik på fanen **Kom Introduktion galleri** fra den webside, der åbnes, og klik derefter på eksemplet **Føler dataanalyse** under kategorien **løsninger med eksempeldata** .

    ![Få Introduktion galleriet Hurtige](./media/hdinsight-hive-analyze-sensor-data/getting-started-gallery.png)

3. Følg instruktionerne på websiden for at afslutte eksemplet.
