<properties
    pageTitle="Brug interaktive Hive i HDInsight | Microsoft Azure"
    description="Lær at bruge interaktive Hive (Hive på LLAP) i HDInsight."
    keywords=""
    services="hdinsight"
    documentationCenter=""
    tags="azure-portal"
    authors="mumian" 
    manager="jhubbard"
    editor="cgronlun"/>

<tags
    ms.service="hdinsight"
    ms.workload="big-data"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/27/2016"
    ms.author="jgao"/>


# <a name="use-interactive-hive-in-hdinsight-preview"></a>Brug interaktive Hive i HDInsight (Preview)

Interaktive Hive (kaldes også [Direkte lange og proces]( https://cwiki.apache.org/confluence/display/Hive/LLAP)) er en ny HDInsight [klynge type]( hdinsight-hadoop-provision-linux-clusters.md#cluster-types).  Interaktive Hive tillader i cachelager, der gør Hive forespørgsler meget mere interaktive og hurtigere. Den nye funktion gør HDInsight en af verdens de fleste performant, fleksible, og Åbn Big Data løsningen på skyen med i hukommelsen cachelagres (ved hjælp af Hive og knallertmotor) og avancerede analytics via deep integration med R-tjenester. 

Den interaktive Hive klynge er forskellig fra Hadoop-klynge. Den kun indeholder Hive-tjenesten. 

> [AZURE.NOTE] MapReduce, gris, Sqoop, Oozie og andre tjenester fjernes fra denne type klynge snart.
Tjenesten Hive i den interaktive Hive klynge er kun tilgængelig via Ambari Hive visning, Beeline og Hive ODBC. Det kan ikke åbnes via Hive-konsollen, Templeton, Azure CLI og Azure PowerShell. 


 


## <a name="create-an-interactive-hive-cluster"></a>Oprette en interaktiv Hive klynge

Interaktive Hive klynge understøttes kun på Linux-baserede klynger. Du kan finde oplysninger om oprettelse af HDInsight klynger [oprette Linux-baserede Hadoop klynger i HDInsight](hdinsight-hadoop-provision-linux-clusters.md).


## <a name="execute-hive-from-interactive-hive"></a>Udføre Hive fra interaktive Hive

Der er forskellige indstillinger, hvordan du kan udføre Hive forespørgsler:

- Køre Hive ved hjælp af visningen Ambari Hive

    Du kan finde oplysninger om brug af visningen Hive [Brug af Hive visningen med Hadoop i HDInsight]( hdinsight-hadoop-use-hive-ambari-view.md).

- Køre Hive ved hjælp af Beeline

    Oplysninger om brug af Beeline på HDInsight, kan du se [Brug Hive med Hadoop i HDInsight med Beeline](hdinsight-hadoop-use-hive-beeline.md).

    Du kan bruge Beeline fra headnode eller en tom kantnode.  Det anbefales at bruge Beeline fra en tom kantnode.  Du kan finde oplysninger om oprettelse af en HDInsight klynge med en tom edgenode [Brug tomme kant knuder på HDInsight](hdinsight-apps-use-edge-node.md).

- Køre Hive ved hjælp af Hive ODBC

    Du kan finde oplysninger om brug af Hive ODBC [Forbinder du Excel til Hadoop med Microsoft Hive ODBC-driver](hdinsight-connect-excel-hive-odbc-driver.md).

**Sådan finder du forbindelsesstrengen JDBC:**

1.  Log Ambari ved hjælp af følgende URL-adressen: https://<ClusterName>. AzureHDInsight.net.
2.  Klik på **Hive** fra menuen til venstre.
3.  Klik på ikonet fremhævet for at kopiere URL-adressen:

    ![Hadoop-HDInsight interaktive Hive LLAP JDBC](./media/hdinsight-hadoop-use-interactive-hive/hdinsight-hadoop-use-interactive-hive-jdbc.png)

## <a name="see-also"></a>Se også
-   [Oprette Linux-baserede Hadoop klynger i HDInsight](hdinsight-hadoop-provision-linux-clusters.md): Lær at oprette interaktive Hive klynger i HDInsight.
-   [Bruge Hive med Hadoop i HDInsight med Beeline](hdinsight-hadoop-use-hive-beeline.md): Lær, hvordan du bruger Beeline til at sende Hive forespørgsler.
-   [Forbinder du Excel til Hadoop med Microsoft Hive ODBC-driver](hdinsight-connect-excel-hive-odbc-driver.md): Lær, hvordan Excel tilsluttes Hive.
