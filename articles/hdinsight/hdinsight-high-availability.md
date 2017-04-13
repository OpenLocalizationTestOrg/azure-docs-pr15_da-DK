<properties
    pageTitle="Tilgængeligheden af Hadoop klynger i HDInsight | Microsoft Azure"
    description="HDInsight installerer yderst tilgængelig og pålidelig klynger med en ekstra hoved node."
    services="hdinsight"
    tags="azure-portal"
    editor="cgronlun"
    manager="jhubbard"
    authors="mumian"
    documentationCenter=""/>

<tags
    ms.service="hdinsight"
    ms.workload="big-data"
    ms.tgt_pltfrm="na"
    ms.devlang="multiple"
    ms.topic="article"
    ms.date="10/21/2016"
    ms.author="jgao"/>


#<a name="availability-and-reliability-of-windows-based-hadoop-clusters-in-hdinsight"></a>Tilgængelighed og pålidelighed af Windows-baseret Hadoop klynger i HDInsight


>[AZURE.NOTE] De trin, der er brugt i dette dokument er specifikke for Windows-baseret HDInsight klynger. Hvis du bruger en Linux-baserede klynge, kan du se [tilgængeligheden og pålideligheden af Linux-baserede Hadoop klynger i HDInsight](hdinsight-high-availability-linux.md) for Linux-specifikke oplysninger.

HDInsight gør det muligt for kunder at implementere en række forskellige typer af klynge for forskellige data analytics arbejdsmængder. Klynge typer tilbydes i dag er Hadoop klynger til forespørgslen og analyse arbejdsbelastninger, HBase klynger for NoSQL arbejdsmængder og Storm klynger for behandling af realtid hændelse arbejdsmængder. Inden for en given klynge type er der forskellige roller for de forskellige noder. Eksempel:



- Hadoop klynger for HDInsight er installeret med to roller:
    - I afsnit node (2 noder)
    - Datanode (mindst 1 node)

- HBase klynger for HDInsight er installeret med tre roller:
    - Hoved-servere (2 noder)
    - Område-servere (mindst 1 node)
    - Master/Zookeeper noder (3 noder)

- Storm klynger for HDInsight er installeret med tre roller:
    - Nimbus noder (2 noder)
    - Overordnede servere (mindst 1 node)
    - Zookeeper noder (3 noder)

Standard-installationer Hadoop klynger har typisk en enkelt hoved node. HDInsight fjerner denne ét afsnit med tilføjelsen af en sekundær hoved node/HEAD server/Nimbus node at øge tilgængelighed og pålidelighed af tjenesten bruges til at administrere arbejdsmængder. Disse hoved noder/dyr servere/Nimbus noder er udviklet til at administrere fejl i arbejder noder hurtigere, men afbrydelser af master services kører på noden hoved medfører klynge at ophøre med at arbejde.


[ZooKeeper](http://zookeeper.apache.org/ ) noder (ZKs) er blevet tilføjet og bruges til fyldtegn valg af hoved noder og for at sikre, der arbejder noder og gateways (GWs) ved hvornår mislykkes den sekundære hovedsæde node (hoved Node1), når den aktive hovedsæde node (hoved Node0) ændres til inaktiv.

![Diagram over en meget pålidelig hoved knuderne i HDInsight Hadoop-implementering.](./media/hdinsight-high-availability/hadoop.high.availability.architecture.diagram.png)




## <a name="check-active-head-node-service-status"></a>Kontrollere Tjenestestatus aktive hoved node
Til at bestemme, hvilke hoved node er aktiv og kontrollere status for de tjenester, der kører på hovedet noden, skal du oprette forbindelse til Hadoop-klynge ved hjælp af RDP Remote Desktop Protocol (). Du kan finde instruktioner RDP [administrere Hadoop klynger i HDInsight ved hjælp af portalen Azure](hdinsight-administer-use-management-portal.md#connect-to-hdinsight-clusters-by-using-rdp). Når du har knyttet til klyngen, skal du dobbeltklikke på ikonet **Hadoop Service tilgængelig** er placeret på skrivebordet for at få en status om hvilke hoved noden Namenode, Jobtracker, Templeton, Oozieservice, Metastore og Hiveserver2 services er løbende eller for HDI 3.0, Namenode, Ressourcestyring, historik Server, Templeton, Oozieservice, Metastore og Hiveserver2 tjenester.

![](./media/hdinsight-high-availability/Hadoop.Service.Availability.Status.png)

På skærmbilledet er den aktive hovedsæde node *headnode0*.

## <a name="access-log-files-on-the-secondary-head-node"></a>Access logfiler på den sekundære hovedsæde node

For at få adgang til jobbet logge på den sekundære hovedsæde node, hvis det er blevet den aktive hovedsæde node JobTracker UI-browsing fungerer stadig på samme måde som for den primære aktive node. For at få adgang til JobTracker, skal du oprette forbindelse til Hadoop-klynge ved hjælp af RDP, som beskrevet i forrige afsnit. Når du har eksternt i klyngen, skal du dobbeltklikke på ikonet **Hadoop navn Node Status** er placeret på skrivebordet, og klik derefter på **NameNode logfiler** til at gå til mappen i logge på den sekundære hovedsæde node.

![](./media/hdinsight-high-availability/Hadoop.Head.Node.Log.Files.png)


## <a name="configure-head-node-size"></a>Konfigurere hoved node størrelse
Noderne hoved fordeles som store virtuelle maskiner (VM'er) som standard. Denne størrelse er nok til administration af de fleste Hadoop-job, der kører på klyngen. Men der er scenarier, som kan kræve meget store FOS for noderne hoved. Et eksempel er, når klyngen har til at administrere et stort antal mindre Oozie opgaver.

Meget store FOS kan konfigureres ved hjælp af Azure PowerShell-cmdlet'er eller HDInsight SDK.

Oprettelse og klargøring af en klynge ved hjælp af Azure PowerShell er beskrevet i [administrere HDInsight ved hjælp af PowerShell](hdinsight-administer-use-powershell.md). Konfiguration af en meget store hoved node kræver tilføjelsen af den `-HeadNodeVMSize ExtraLarge` -parameter til den `New-AzureRmHDInsightcluster` cmdlet, der bruges i denne kode.

    # Create a new HDInsight cluster in Azure PowerShell
    # Configured with an ExtraLarge head-node VM
    New-AzureRmHDInsightCluster `
                -ResourceGroupName $resourceGroupName `
                -ClusterName $clusterName ` 
                -Location $location `
                -HeadNodeVMSize ExtraLarge `
                -DefaultStorageAccountName "$storageAccountName.blob.core.windows.net" `
                -DefaultStorageAccountKey $storageAccountKey `
                -DefaultStorageContainerName $containerName  `
                -ClusterSizeInNodes $clusterNodes

For SDK minder tekstenheden. Oprettelse og klargøring af en klynge ved hjælp af SDK er beskrevet i [Ved hjælp af HDInsight .NET SDK](hdinsight-provision-clusters.md#sdk). Konfiguration af en meget store hoved node kræver tilføjelsen af den `HeadNodeSize = NodeVMSize.ExtraLarge` -parameter til den `ClusterCreateParameters()` metode, der bruges i denne kode.

    # Create a new HDInsight cluster with the HDInsight SDK
    # Configured with an ExtraLarge head-node VM
    ClusterCreateParameters clusterInfo = new ClusterCreateParameters()
    {
        Name = clustername,
        Location = location,
        HeadNodeSize = NodeVMSize.ExtraLarge,
        DefaultStorageAccountName = storageaccountname,
        DefaultStorageAccountKey = storageaccountkey,
        DefaultStorageContainer = containername,
        UserName = username,
        Password = password,
        ClusterSizeInNodes = clustersize
    };


## <a name="next-steps"></a>Næste trin

- [Apache ZooKeeper](http://zookeeper.apache.org/ )
- [Oprette forbindelse til HDInsight klynger ved hjælp af RDP](hdinsight-administer-use-management-portal.md#rdp)
- [Brug af HDInsight .NET SDK](hdinsight-provision-clusters.md#sdk)
