<properties 
   pageTitle="Bruge Apache København og SQuirreL i HDInsight | Microsoft Azure" 
   description="Lær, hvordan du bruger Apache København i HDInsight, og hvordan du installerer og konfigurerer SQuirreL på computeren til at oprette forbindelse til en HBase klynge i HDInsight." 
   services="hdinsight" 
   documentationCenter="" 
   authors="mumian" 
   manager="jhubbard" 
   editor="cgronlun"/>

<tags
   ms.service="hdinsight"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data" 
   ms.date="09/02/2016"
   ms.author="jgao"/>

# <a name="use-apache-phoenix-with-linux-based-hbase-clusters-in-hdinsight"></a>Bruge Apache København med Linux-baserede HBase klynger i HDInsight  

Lær, hvordan du bruger [Apache København](http://phoenix.apache.org/) i HDInsight, og hvordan du bruger SQLLine. Du kan finde flere oplysninger om København, [København på 15 minutter eller mindre](http://phoenix.apache.org/Phoenix-in-15-minutes-or-less.html). Du kan finde grammatik København [København grammatik](http://phoenix.apache.org/language/index.html).

>[AZURE.NOTE] Finde oplysninger om København i HDInsight, [Nyheder i de Hadoop klynge versioner, der leveres af HDInsight?] [hdinsight-versions].

##<a name="use-sqlline"></a>Brug SQLLine
[SQLLine](http://sqlline.sourceforge.net/) er en kommandolinjen til at udføre SQL. 

###<a name="prerequisites"></a>Forudsætninger
Før du kan bruge SQLLine, skal du have følgende:

- **A HBase klynge i HDInsight**. Oplysninger om klargøring HBase klynge, skal du se [komme i gang med Apache HBase i HDInsight][hdinsight-hbase-get-started].
- **Opret forbindelse til HBase klynge via den protokol til Fjernskrivebord**. Flere oplysninger under [administrere Hadoop klynger i HDInsight ved hjælp af portalen Azure klassisk][hdinsight-manage-portal].


Når du opretter forbindelse til en HBase klynge, skal du oprette forbindelse til en af Dyrepasserne. Hver HDInsight klynge har 3 Dyrepasserne. 

**At finde ud af Zookeeper værtsnavn**

1. Åbn Ambari ved at gennemse for at **https://<ClusterName>. azurehdinsight.net**.
2. Angiv HTTP (klynge) brugernavnet og adgangskoden til logon.
3. Klik på **ZooKeeper** fra menuen til venstre. Du skal se 3 **ZooKeeper Server** er angivet.
4. Klik på en af **ZooKeeper Server** vises. Find **Hostname**i ruden Oversigt. Det er lig med *zk1 jdolehb.3lnng4rcvp5uzokyktxs4a5dhd.bx.internal.cloudapp.net*.

**Bruge SQLLine**

1. Oprette forbindelse til den klynge ved hjælp af SSH. Flere oplysninger under [Brug SSH med Linux-baserede Hadoop på HDInsight fra Linux, Unix, eller OS X](hdinsight-hadoop-linux-use-ssh-unix.md) eller [Brug SSH med Linux-baserede Hadoop på HDInsight fra Windows](hdinsight-hadoop-linux-use-ssh-windows.md) afhængigt af klientcomputeren OS.

2. Kør følgende kommandoer til at køre SQLLine fra SSH:

        cd /usr/hdp/2.2.9.1-7/phoenix/bin
        ./sqlline.py <ClusterName>:2181:/hbase-unsecure

2. Kør følgende kommandoer for at oprette en HBase tabel, og Indsæt nogle data:

        CREATE TABLE Company (COMPANY_ID INTEGER PRIMARY KEY, NAME VARCHAR(225));
    
        !tables
        
        UPSERT INTO Company VALUES(1, 'Microsoft');
        
        SELECT * FROM Company;
        
        !quit

Se afsnittet [SQLLine manuel](http://sqlline.sourceforge.net/#manual) og [København grammatik](http://phoenix.apache.org/language/index.html)kan finde flere oplysninger.


 
##<a name="next-steps"></a>Næste trin
I denne artikel, har du lært at bruge Apache København i HDInsight.  Du kan få flere

- [Oversigt over HDInsight HBase][hdinsight-hbase-overview]: HBase er en Apache, Åbn-kilde, NoSQL-database, der er bygget på Hadoop, der indeholder RAM og stærke konsistens til store mængder ustrukturerede og semistructured data.
- [Klargør HBase klynger på Azure virtuelt netværk][hdinsight-hbase-provision-vnet]: med virtuelt netværksintegration HBase klynger kan installeres på det samme virtuelle netværk som dine programmer, så programmer kan kommunikere med HBase direkte.
- [Konfigurere HBase gentagelse i HDInsight](hdinsight-hbase-geo-replication.md): Lær, hvordan du konfigurerer HBase gentagelse på tværs af to Azure datacentre. 
- [Analysere Twitter synspunkt med HBase i HDInsight][hbase-twitter-sentiment]: Lær, hvordan du gør realtid [synspunkt analyse](http://en.wikipedia.org/wiki/Sentiment_analysis) af stor data ved hjælp af HBase i en Hadoop klynge i HDInsight.

[azure-portal]: https://portal.azure.com
[vnet-point-to-site-connectivity]: https://msdn.microsoft.com/library/azure/09926218-92ab-4f43-aa99-83ab4d355555#BKMK_VNETPT

[hdinsight-versions]: hdinsight-component-versioning.md
[hdinsight-hbase-get-started]: hdinsight-hbase-tutorial-get-started.md
[hdinsight-manage-portal]: hdinsight-administer-use-management-portal.md#connect-to-hdinsight-clusters-by-using-rdp
[hdinsight-hbase-provision-vnet]: hdinsight-hbase-provision-vnet.md
[hdinsight-hbase-overview]: hdinsight-hbase-overview.md
[hbase-twitter-sentiment]: hdinsight-hbase-analyze-twitter-sentiment.md

[hdinsight-hbase-phoenix-sqlline]: ./media/hdinsight-hbase-phoenix-squirrel/hdinsight-hbase-phoenix-sqlline.png
[img-certificate]: ./media/hdinsight-hbase-phoenix-squirrel/hdinsight-hbase-vpn-certificate.png
[img-vnet-diagram]: ./media/hdinsight-hbase-phoenix-squirrel/hdinsight-hbase-vnet-point-to-site.png
[img-squirrel-driver]: ./media/hdinsight-hbase-phoenix-squirrel/hdinsight-hbase-squirrel-driver.png
[img-squirrel-alias]: ./media/hdinsight-hbase-phoenix-squirrel/hdinsight-hbase-squirrel-alias.png
[img-squirrel]: ./media/hdinsight-hbase-phoenix-squirrel/hdinsight-hbase-squirrel.png
[img-squirrel-sql]: ./media/hdinsight-hbase-phoenix-squirrel/hdinsight-hbase-squirrel-sql.png


 
