<properties
    pageTitle="Hvad er HBase i HDInsight? | Microsoft Azure"
    description="En introduktion til Apache HBase i HDInsight en NoSQL database udbygges med Hadoop. Få mere at vide om brug sager, og Sammenlign HBase med andre Hadoop klynger."
    keywords="bigtable, nosql, hvad er hbase"
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
    ms.topic="get-started-article"
    ms.date="09/14/2016"
    ms.author="jgao"/>



# <a name="what-is-hbase-in-hdinsight-a-nosql-database-that-provides-bigtable-like-capabilities-for-hadoop"></a>Hvad er HBase i HDInsight: A NoSQL database, der indeholder BigTable-lignende funktioner til Hadoop

Apache HBase er en open source-, NoSQL database, som er bygget på Hadoop og Google BigTable er baseret på. HBase indeholder RAM og stærke konsistens for store mængder ustrukturerede og semistructured data i en schemaless database, der er organiseret efter kolonne familier.

Data er gemt i rækkerne i en tabel, og data i en række er grupperet efter kolonne familie. HBase er en schemaless database i mening, hverken kolonnerne eller typen data, der er gemt i dem skal være defineret før du bruger dem. Åbn-kildekode skaleres lineært til at håndtere petabytes af data på tusindvis af noder. Det kan stole på overflødige data, batchbehandling og andre funktioner, der leveres af distribuerede programmer i Hadoop økosystemet.

## <a name="how-is-hbase-implemented-in-azure-hdinsight"></a>Hvordan implementeres HBase i Azure HDInsight?

HDInsight HBase tilbydes som en administreret klynge, der er integreret i Azure-miljø. Klynger er konfigureret til at gemme data direkte i Azure Blob-lager, som indeholder kort ventetid og øget Elasticitet i ydeevne og omkostninger valgmuligheder. Dette gør det muligt for kunder til at oprette interaktive websteder, der arbejder med store datasæt, til at opbygge tjenester, der lagrer føler og telemetri data fra millioner af slutpunkter og til at analysere disse data med Hadoop-job. HBase og Hadoop er god udgangspunktet for stor data project i Azure; de kan især kan realtid programmer til at arbejde med store datasæt.

HDInsight implementeringen bruger skala fra arkitekturen i HBase til at levere automatisk sharding over tabeller, stærke konsistens til læser og skriver og automatisk failover. Ydeevnen forbedres ved i hukommelsen cachelagring af læser med høj overførselshastighed streaming for skrivning. Klargøring af virtuelt netværk er også tilgængelig for HDInsight HBase. Yderligere oplysninger finder du [klargøring HDInsight klynger på Azure virtuelt netværk] [hbase-provision-vnet].

## <a name="how-is-data-managed-in-hdinsight-hbase"></a>Hvordan administreres data i HDInsight HBase?

Data kan administreres i HBase ved hjælp af den `create`, `get`, `put`, og `scan` kommandoer fra HBase shell. Der skrives data til databasen ved hjælp af `put` og læse ved hjælp af `get`. Den `scan` kommandoen bruges til at hente data fra flere rækker i en tabel. Data kan også administreres ved hjælp af HBase C# API, som indeholder en klientbibliotek oven på HBase REST-API. En HBase database kan også forespørges ved hjælp af Hive. En introduktion til disse programming modeller, se [Introduktion til brug af HBase med Hadoop i HDInsight][hbase-get-started]. Samtidig processorer er også tilgængelige, som tillader databehandling i knudepunkt i pågældende host databasen.


## <a name="scenarios-use-cases-for-hbase"></a>Scenarier: Use cases for HBase
Vedtaget use case, for hvilke BigTable (og dermed HBase) blev oprettet blev websøgning. Søgemaskiner opbygge indeks, der er tilknyttet de websider, der indeholder disse betingelser. Men der er mange andre Brug situationer, som HBase egner sig til – flere der er specificeret i dette afsnit.

- Nøgle-værdi store

    HBase kan bruges som et lager til nøgle-værdi, og den er egnet til administration af meddelelse systemer. Facebook bruger HBase til deres meddelelsessystem og det er velegnet til at lagre og administrere Internetkommunikation. WebTable bruger HBase til at søge efter og administrere tabeller, der er hentet fra websider.

- Føler data

    HBase er nyttig til at hente data, der indsamles trinvist fra forskellige kilder. Dette omfatter sociale analytics, tidsserie, holde interaktive dashboards opdateret med tendenser og tællere og administration af overvågningslog log systemer. Som eksempler kan nævnes Bloomberg forhandler terminal og den åbne tid serie Database (OpenTSDB), som lagrer og giver adgang til målepunkter, der indsamles om tilstanden for server-systemer.

- Realtid forespørgsel

    [København](http://phoenix.apache.org/) er en SQL-forespørgsel-programmet Apache HBase. Det bruges som en JDBC driver, og aktiverer forespørgsler og administration af HBase tabeller ved hjælp af SQL.

- HBase som en platform

    Programmer kan køre oven på HBase ved at bruge det som en datastore. Som eksempler kan nævnes København, OpenTSDB, Kiji og Tital. Programmer kan også integreres med HBase. Som eksempler kan nævnes Hive, gris, Solr, Storm, Flume, Impala, gnister, milt og analysér.


##<a name="next-steps"></a>Næste trin

- [Introduktion til brug af HBase med Hadoop i HDInsight][hbase-get-started]
- [Klargør HDInsight klynger på Azure virtuelt netværk] [hbase-provision-vnet]
- [Konfigurere HBase gentagelse i HDInsight](hdinsight-hbase-geo-replication.md)
- [Analysere Twitter synspunkt med HBase i HDInsight][hbase-twitter-sentiment]
- [Bruge Maven til at oprette Java-programmer, der bruger HBase med HDInsight (Hadoop)][hbase-build-java-maven]

##<a name="see-also"></a>Se også

- [Apache HBase](https://hbase.apache.org/)
- [Bigtable: Et fordelt Storage-System til strukturerede Data](http://research.google.com/archive/bigtable.html)




[hbase-provision-vnet]: hdinsight-hbase-provision-vnet.md

[hbase-twitter-sentiment]: hdinsight-hbase-analyze-twitter-sentiment.md

[hbase-build-java-maven]: hdinsight-hbase-build-java-maven.md

[hdinsight-use-hive]: hdinsight-use-hive.md

[hdinsight-storage]: ../hdinsight-hadoop-use-blob-storage.md

[hbase-get-started]: http://azure.microsoft.com/documentation/articles/hdinsight-hbase-get-started/

[azure-purchase-options]: http://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: http://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[azure-management-portal]: https://portal.azure.com/
[azure-create-storageaccount]: ../storage-create-storage-account.md

[apache-hadoop]: http://hadoop.apache.org/
