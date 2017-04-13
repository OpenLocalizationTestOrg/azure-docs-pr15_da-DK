<properties
pageTitle="Porte, der bruges ved HDInsight | Azure"
description="En liste over porte, der bruges i Hadoop-tjenester, der kører på HDInsight."
services="hdinsight"
documentationCenter=""
authors="Blackmist"
manager="jhubbard"
editor="cgronlun"/>

<tags
ms.service="hdinsight"
ms.devlang="na"
ms.topic="article"
ms.tgt_pltfrm="na"
ms.workload="big-data"
ms.date="10/03/2016"
ms.author="larryfr"/>

# <a name="ports-and-uris-used-by-hdinsight"></a>Porte og URI'er, der bruges af HDInsight

Dette dokument indeholder en liste over de porte, der bruges i Hadoop-tjenester, der kører på Linux-baserede HDInsight klynger. Den indeholder også oplysninger om porte, der bruges til at oprette forbindelse til den klynge ved hjælp af SSH.

## <a name="public-ports-vs-non-public-ports"></a>Offentlige porte kontra ikke-offentlige porte

Linux-baserede HDInsight klynger viser kun tre porte offentligt på internettet. 22, 23 og 443. Disse bruges til at opnå sikker adgang til den klynge ved hjælp af SSH og -tjenester, der vises over sikre HTTPS-protokollen.

Internt HDInsight er implementeret ved flere virtuelle Azure-computere (knuderne i klynge,) kører i et virtuelt Azure-netværk. Du kan åbne porte ikke være tilgængelig via internettet fra inden for det virtuelle netværk. Eksempelvis hvis du opretter forbindelse til en af noderne hoved ved hjælp af SSH, fra noden hoved du kan derefter direkte adgang til tjenester, der kører på klyngenoderne.

> [AZURE.IMPORTANT] Når du opretter en HDInsight klynge, hvis du ikke angiver et virtuelt Azure-netværk som en konfigurationsindstilling for, oprettes der; Du kan dog deltage i andre computere (såsom andre virtuelle Azure-computere eller din klientmaskinen udvikling) til denne virtuelle netværk vil automatisk blive oprettet. 

For at deltage i flere computere til det virtuelle netværk, skal du oprette det virtuelle netværk først og derefter angive den, når du opretter din klynge HDInsight. Få mere at vide under [udvide HDInsight-funktioner ved hjælp af et virtuelt Azure-netværk](hdinsight-extend-hadoop-virtual-network.md)

## <a name="public-ports"></a>Offentlige porte

Alle noderne i en HDInsight klynge er placeret i et virtuelt Azure-netværk og kan ikke åbnes direkte fra internettet. En offentlig gateway giver internetadgang til følgende porte, der er fælles på tværs af alle HDInsight klynge typer.

| Tjenesten | Port | Protokol | Beskrivelse |
| ---- | ---------- | -------- | ----------- | ----------- |
| sshd | 22 | SSH | Opretter forbindelse klienter til sshd på den primære headnode. Se [Brug af SSH med Linux-baserede HDInsight](hdinsight-hadoop-linux-use-ssh-windows.md) |
| sshd | 22 | SSH | Opretter forbindelse klienter til sshd på noden kant (kun HDInsight Premium). Se [Introduktion til brug af R Server på HDInsight](hdinsight-hadoop-r-server-get-started.md) |
| sshd | 23 | SSH | Opretter forbindelse klienter til sshd på den sekundære headnode. Se [Brug af SSH med Linux-baserede HDInsight](hdinsight-hadoop-linux-use-ssh-windows.md) |
| Ambari | 443 | HTTPS | Ambari web brugergrænseflade. Se [administrere HDInsight ved hjælp af brugergrænsefladen i Ambari Web.](hdinsight-hadoop-manage-ambari.md) |
| Ambari | 443 | HTTPS | Ambari REST-API. Se [administrere HDInsight ved hjælp af Ambari REST-API](hdinsight-hadoop-manage-ambari-rest-api.md) |
| WebHCat | 443 | HTTPS | HCatalog REST-API. Se [bruge Hive med krøllet](hdinsight-hadoop-use-pig-curl.md), [bruge gris med krøllet](hdinsight-hadoop-use-pig-curl.md), [bruge MapReduce med krøllet](hdinsight-hadoop-use-mapreduce-curl.md) |
| HiveServer2 | 443 | ODBC | Opretter forbindelse til Hive ved hjælp af ODBC. Se [Forbinder du Excel til HDInsight med Microsoft ODBC-driver](hdinsight-connect-excel-hive-odbc-driver.md). |
| HiveServer2 | 443 | JDBC | Opretter forbindelse til Hive ved hjælp af JDBC. Se [oprette forbindelse til Hive på HDInsight, der bruger Hive JDBC driveren](hdinsight-connect-hive-jdbc-driver.md) |

Følgende er tilgængelige for bestemte klynge kontotyper:

| Tjenesten | Port | Protokol |Klynge type | Beskrivelse |
| ------------ | ---- |  ----------- | --- | ----------- |
| Stargate | 443 | HTTPS | HBase | HBase REST-API. Se [Introduktion til brug af HBase](hdinsight-hbase-tutorial-get-started-linux.md) |
| Livius | 443 | HTTPS |  Knallertmotor | Knallertmotor REST-API. Få vist [sende knallertmotor sager ved hjælp af Livius](hdinsight-apache-spark-livy-rest-interface.md) |
| Storm | 443 | HTTPS | Storm | Storm web brugergrænseflade. Se [Implementer og administrere Storm topologier på HDInsight](hdinsight-storm-deploy-monitor-topology-linux.md)

### <a name="authentication"></a>Godkendelse

Alle tjenester offentligt vises på internettet skal godkendes:

| Port | Legitimationsoplysninger |
| ---- | ----------- |
| 22 eller 23 | De SSH brugerlegitimationsoplysninger, der er angivet under oprettelse af klynge |
| 443 | Logonnavn (standard: administrator) og din adgangskode, der er angivet under oprettelse af klynge |

## <a name="non-public-ports"></a>Ikke-offentlige porte

> [AZURE.NOTE] Nogle af tjenesterne er kun tilgængelige på bestemte klynge typer. For eksempel er HBase kun tilgængelig på HBase klynge typer.

### <a name="hdfs-ports"></a>HDFS porte

| Tjenesten | Node(r) | Port | Protokol | Beskrivelse |
| ------- | ------- | ---- | -------- | ----------- | 
| NameNode web brugergrænseflade | I afsnit noder | 30070 | HTTPS | Web Brugergrænsefladen til at få vist aktuel status |
| NameNode metadatatjeneste | hoved noder | 8020 | IPC | Fil-systemet metadata 
| DataNode | Alle arbejder noder | 30075 | HTTPS | Web brugergrænseflade til at få vist status, logfiler, osv. |
| DataNode | Alle arbejder noder | 30010 | &nbsp; | Dataoverførsel |
| DataNode | Alle arbejder noder | 30020 | IPC | Metadata handlinger |
| Sekundær NameNode | I afsnit noder | 50090 | HTTP | Kontrolpunkt til NameNode metadata |

### <a name="yarn-ports"></a>GARN porte

| Tjenesten | Node(r) | Port | Protokol | Beskrivelse |
| ------- | ------- | ---- | -------- | ----------- |
| Ressourcestyring web brugergrænseflade | I afsnit noder | 8088 | HTTP | Web brugergrænseflade for ressourcestyring |
| Ressourcestyring web brugergrænseflade | I afsnit noder | 8090 | HTTPS | Web brugergrænseflade for ressourcestyring |
| Ressourcestyring administrator grænseflade | hoved noder | 8141 | IPC | For programmet indsendelser (Hive, Hive server, gris, osv.) |
| Ressourcestyring scheduler | hoved noder | 8030 | HTTP | Administrativ brugergrænseflade |
| Ressourcestyring programmet grænseflade | hoved noder | 8050 | HTTP |Adressen på programmer manager grænsefladen |
| NodeManager | Alle arbejder noder | 30050 | &nbsp; | Adressen på objektbeholder manager |
| NodeManager web brugergrænseflade | Alle arbejder noder | 30060 | HTTP | Ressource manager grænseflade |
| Tidslinje adresse | I afsnit noder | 10200 | RPC | Tidslinje RPC-tjenesten. |
| Tidslinje web brugergrænseflade | I afsnit noder | 8181 | HTTP | Webdelen tidslinje service brugergrænseflade |

### <a name="hive-ports"></a>Hive porte

| Tjenesten | Node(r) | Port | Protokol | Beskrivelse |
| ------- | ------- | ---- | -------- | ----------- |
| HiveServer2 | I afsnit noder | 10001 | Thrift | Tjeneste til automatisk at oprette forbindelse til Hive (Thrift/JDBC) |
| HiveServer | I afsnit noder | 10000 | Thrift | Tjeneste til automatisk at oprette forbindelse til Hive (Thrift/JDBC) |
| Hive Metastore | I afsnit noder | 9083 | Thrift | Tjeneste til automatisk at oprette forbindelse til Hive metadata (Thrift/JDBC) |

### <a name="webhcat-ports"></a>WebHCat porte

| Tjenesten | Node(r) | Port | Protokol | Beskrivelse |
| ------- | ------- | ---- | -------- | ----------- |
| WebHCat server | I afsnit noder | 30111 | HTTP | Web API oven på HCatalog og andre Hadoop-tjenester |

### <a name="mapreduce-ports"></a>MapReduce porte

| Tjenesten | Node(r) | Port | Protokol | Beskrivelse |
| ------- | ------- | ---- | -------- | ----------- |
| JobHistory | I afsnit noder | 19888 | HTTP | MapReduce JobHistory web brugergrænseflade |
| JobHistory | I afsnit noder | 10020 | &nbsp; | MapReduce JobHistory server |
| ShuffleHandler | &nbsp; | 13562 | &nbsp; | Overførsler mellemliggende kort output til anmodning om Reducers |

### <a name="oozie"></a>Oozie

| Tjenesten | Node(r) | Port | Protokol | Beskrivelse |
| ------- | ------- | ---- | -------- | ----------- |
| Oozie server | I afsnit noder | 11000 | HTTP | URL-adressen til Oozie service |
| Oozie server | I afsnit noder | 11001 | HTTP | Port til Oozie administrator |

### <a name="ambari-metrics"></a>Ambari målepunkter

| Tjenesten | Node(r) | Port | Protokol | Beskrivelse |
| ------- | ------- | ---- | -------- | ----------- |
| Tidslinje (programmet oversigt) | I afsnit noder | 6188 | HTTP | Webdelen tidslinje service brugergrænseflade |
| Tidslinje (programmet oversigt) | I afsnit noder | 30200 | RPC | Webdelen tidslinje service brugergrænseflade |

### <a name="hbase-ports"></a>HBase porte

| Tjenesten | Node(r) | Port | Protokol | Beskrivelse |
| ------- | ------- | ---- | -------- | ----------- |
| HMaster | I afsnit noder | 16000 | &nbsp; | &nbsp; |
| HMaster oplysninger Webbrugergrænseflade | I afsnit noder | 16010 | HTTP | Port til webdelen HBase Master brugergrænseflade |
| Område-server | Alle arbejder noder | 16020 | &nbsp; | &nbsp; |
| &nbsp; | &nbsp; | 2181 | &nbsp; | Den port, som klienter bruger til at oprette forbindelse til ZooKeeper |

### <a name="kafka-ports"></a>Kafka porte

| Tjenesten | Node(r) | Port | Protokol | Beskrivelse |
| ------- | ------- | ---- | -------- | ----------- |
| Broker  | Arbejder noder | 9092 | [Kafka protokol](http://kafka.apache.org/protocol.html) | Bruges til klientkommunikation |
| &nbsp; | Zookeeper noder | 2181 | &nbsp; | Den port, som klienter bruger til at oprette forbindelse til Zookeeper |
