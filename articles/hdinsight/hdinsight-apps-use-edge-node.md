<properties
    pageTitle="Brug af tomme kant noder i HDInsight | Microsoft Azure"
    description="Hvordan du kan tilføje en ampty kantnode til HDInsight klynge, der kan bruges som en klient, og test/host programmerne HDInsight."
    services="hdinsight"
    editor="cgronlun"
    manager="jhubbard"
    authors="mumian"
    tags="azure-portal"
    documentationCenter=""/>

<tags
    ms.service="hdinsight"
    ms.workload="big-data"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/14/2016"
    ms.author="jgao"/>

# <a name="use-empty-edge-nodes-in-hdinsight"></a>Brug af tomme kant noder i HDInsight

Lær at tilføje en tom kantnode til en Linux-baserede HDInsight klynge. En tom kantnode er en Linux virtuel maskine med de samme klientværktøjer installeret og konfigureret som headnodes, men har ingen hadoop-tjenester, der kører. Du kan bruge noden kant til adgang til klyngen, test din klientprogrammer og vært for din klientprogrammer. 

Du kan føje en tom kantnode til en eksisterende HDInsight klynge til en ny klynge, når du opretter klyngen. Tilføje en tom kantnode er færdig med Azure ressourcestyring skabelon.  I følgende eksempel viser, hvordan du gør ved hjælp af en skabelon:

    "resources": [
        {
            "name": "[concat(parameters('clusterName'),'/', variables('applicationName'))]",
            "type": "Microsoft.HDInsight/clusters/applications",
            "apiVersion": "[variables('clusterApiVersion')]",
            "properties": {
                "marketPlaceIdentifier": "EmptyNode",
                "computeProfile": {
                    "roles": [{
                        "name": "edgenode",
                        "targetInstanceCount": 1,
                        "hardwareProfile": {
                            "vmSize": "[parameters('edgeNodeSize')]"
                        }
                    }]
                },
                "installScriptActions": [{
                    "name": "[concat('emptynode','-' ,uniquestring(variables('applicationName')))]",
                    "uri": "https://raw.githubusercontent.com/hdinsight/Iaas-Applications/master/EmptyNode/scripts/EmptyNodeSetup.sh",
                    "roles": ["edgenode"]
                }],
                "uninstallScriptActions": [],
                "httpsEndpoints": [],
                "applicationType": "CustomApplication"
            }
        }
    ],

Som vist i stikprøven, kan du eventuelt ringe en [scripthandling](hdinsight-hadoop-customize-cluster-linux.md) for at udføre yderligere konfiguration, som installation [Apache farvetone](hdinsight-hadoop-hue-linux.md) i noden kant.

Når du har oprettet en kantnode, kan du oprette forbindelse til noden kant ved hjælp af SSH og køre klientværktøjer for at få adgang til Hadoop-klynge i HDInsight.

## <a name="add-an-edge-node-to-an-existing-cluster"></a>Føje en kantnode til en eksisterende klynge

I dette afsnit, skal bruge du en ressourcestyring skabelon til at føje en kantnode til en eksisterende HDInsight klynge.  Skabelonen ressourceleder, der kan findes i [GitHub](https://github.com/hdinsight/Iaas-Applications/tree/master/EmptyNode). Skabelonen ressourceleder, der ringer op til et script handling script, der findes i https://raw.githubusercontent.com/hdinsight/Iaas-Applications/master/EmptyNode/scripts/EmptyNodeSetup.sh. Scriptet udføre ikke handlinger.  Dette er til at vise opkald scripthandling fra en ressourcestyring skabelon.

**Føje en tom kantnode til en eksisterende klynge**

1. Oprette en HDInsight klynge, hvis du endnu ikke har en.  Se [Hadoop Selvstudium: Introduktion til brug af Linux-baserede Hadoop i HDInsight](hdinsight-hadoop-linux-tutorial-get-started.md).
2. Klik på følgende billede for at logge på Azure og åbne skabelonen Azure ressourcestyring i portalen Azure. 

    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fhdinsight%2FIaas-Applications%2Fmaster%2FEmptyNode%2Fazuredeploy.json" target="_blank"><img src="https://acom.azurecomcdn.net/80C57D/cdn/mediahandler/docarticles/dpsmedia-prod/azure.microsoft.com/en-us/documentation/articles/hdinsight-hbase-tutorial-get-started-linux/20160201111850/deploy-to-azure.png" alt="Deploy to Azure"></a>

3. Konfigurere følgende egenskaber:

    - CLUSTERNAME: Angiv navnet på en eksisterende HDInsight klynge.
    - EDGENODESIZE: Vælg en af VM størrelserne.
    - EDGENODEPREFIX: Standardværdien er **nyt**.  Brug af standardværdien, er kant nodenavnet **nye edgenode**.  Du kan tilpasse præfikset fra portalen. Du kan også tilpasse det fulde navn fra skabelonen.


4. Klik på **OK** for at gemme ændringerne.
5. Vælg en ressourcegruppe i **ressourcegruppe**.
6. Klik på **Gennemse juridiske betingelser**, og klik derefter på **Køb**.
7. Vælg **Fastgør til dashboard**, og klik derefter på **Opret**.

## <a name="add-an-edge-node-when-creating-a-cluster"></a>Tilføje en kantnode, når du opretter en klynge

I dette afsnit, skal bruge du en ressourcestyring skabelon til at oprette HDInsight klynge med en kantnode.  Skabelonen ressourceleder, der kan findes i en offentlig [Azure Blob-objektbeholder til lagring](http://hditutorialdata.blob.core.windows.net/armtemplates/create-linux-based-hadoop-cluster-in-hdinsight-with-edge-node.json). Skabelonen ressourceleder, der ringer op til et script handling script, der findes i https://raw.githubusercontent.com/hdinsight/Iaas-Applications/master/EmptyNode/scripts/EmptyNodeSetup.sh. Scriptet udføre ikke handlinger.  Dette er til at vise opkald scripthandling fra en ressourcestyring skabelon.

**Føje en tom kantnode til en eksisterende klynge**

1. Oprette en HDInsight klynge, hvis du endnu ikke har en.  Se [Hadoop Selvstudium: Introduktion til brug af Linux-baserede Hadoop i HDInsight](hdinsight-hadoop-linux-tutorial-get-started.md).
2. Klik på følgende billede for at logge på Azure og åbne skabelonen Azure ressourcestyring i portalen Azure. 

    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fhditutorialdata.blob.core.windows.net%2Farmtemplates%2Fcreate-linux-based-hadoop-cluster-in-hdinsight-with-edge-node.json" target="_blank"><img src="https://acom.azurecomcdn.net/80C57D/cdn/mediahandler/docarticles/dpsmedia-prod/azure.microsoft.com/en-us/documentation/articles/hdinsight-hbase-tutorial-get-started-linux/20160201111850/deploy-to-azure.png" alt="Deploy to Azure"></a>

3. Konfigurere følgende egenskaber:
        
    - CLUSTERNAME: Angiv et navn til den nye klynge for at oprette.
    - CLUSTERLOGINUSERNAME: Angive brugernavn Hadoop HTTP.  Standardnavnet er **administrator**.
    - CLUSTERLOGINPASSWORD: Skriv Hadoop HTTP brugerens adgangskode.
    - SSHUSERNAME: Angive brugernavn SSH. Standardnavnet er **sshuser**.
    - SSHPASSWORD: Skriv SSH brugerens adgangskode.
    - PLACERING: Angive placeringen af ressource gruppens navn, klyngen og lagerplads standardkontoen.
    - CLUSTERTYPE: Standardværdien er **hadoop**.
    - CLUSTERWORKERNODECOUNT: Standardværdien er 2.
    - EDGENODESIZE: Vælg en af VM størrelserne.
    - EDGENODEPREFIX: Standardværdien er **nyt**.  Brug af standardværdien, er kant nodenavnet **nye edgenode**.  Du kan tilpasse præfikset fra portalen. Du kan også tilpasse det fulde navn fra skabelonen.

4. Klik på **OK** for at gemme ændringerne.
5. Angiv en ny ressourcegruppe navn i **ressourcegruppe**.
6. Klik på **Gennemse juridiske betingelser**, og klik derefter på **Køb**.
7. Vælg **Fastgør til dashboard**, og klik derefter på **Opret**. 


## <a name="access-an-edge-node"></a>Få adgang til en kantnode

Noden kant ssh slutpunkt er <EdgeNodeName>. <ClusterName>-ssh.azurehdinsight.net:22.  For eksempel nye-edgenode.myedgenode0914-ssh.azurehdinsight.net:22.

Noden kant vises som et program på Azure-portalen.  På portalen giver dig oplysningerne til at få adgang til noden kant ved hjælp af SSH.

**At bekræfte kant node SSH slutpunktet**

1. Log [Azure-portalen](https://portal.azure.com).
2. Åbn HDInsight klyngen med en kantnode.
3. Klik på **programmer** fra bladet klynge. Du skal se noden kant.  Standardnavnet er **nye edgenode**.
4. Klik på noden kant. Du skal se SSH slutpunktet.

**Du kan bruge Hive på noden kant**

5. Brug SSH til at oprette forbindelse til noden kant.  Se [bruge SSH med Linux-baserede Hadoop på HDInsight fra Linux, Unix, eller OS X](hdinsight-hadoop-linux-use-ssh-unix.md) eller [bruge SSH med Linux-baserede Hadoop på HDInsight fra Windows](hdinsight-hadoop-linux-use-ssh-windows.md).
6. Når du har forbindelse til noden kant ved hjælp af SSH, skal du bruge følgende kommando til at åbne konsollen Hive:

        hive
7. Kør følgende kommando for at få vist Hive tabeller i klyngen:

        show tables;

## <a name="delete-an-edge-node"></a>Slette en kantnode

Du kan slette en kantnode fra Azure-portalen.

**Adgang til en kantnode**

1. Log [Azure-portalen](https://portal.azure.com).
2. Åbn HDInsight klyngen med en kantnode.
3. Klik på **programmer** fra bladet klynge. Du skal se en liste over kant noder.  
4. Højreklik på noden kant, du vil slette, og klik derefter på **Slet**.
5. Klik på **Ja** for at bekræfte.

## <a name="next-steps"></a>Næste trin

I denne artikel, har du lært, hvordan du tilføjer en kantnode og hvordan du kan få adgang til noden kant. Hvis du vil vide mere, skal du se følgende artikler:

- [Installere HDInsight-programmer](hdinsight-apps-install-applications.md): Lær, hvordan du installerer et HDInsight program til din klynger.
- [Installere brugerdefinerede HDInsight-programmer](hdinsight-apps-install-custom-applications.md): Lær, hvordan du installerer et ikke-udgivne HDInsight program til HDInsight.
- [Publicere HDInsight-programmer](hdinsight-apps-publish-applications.md): Lær at publicere dine brugerdefinerede HDInsight programmer til Azure Marketplace.
- [MSDN: installere et HDInsight program](https://msdn.microsoft.com/library/mt706515.aspx): Lær, hvordan du definerer HDInsight programmer.
- [Tilpasse Linux-baserede HDInsight klynger ved hjælp af Script handlingen](hdinsight-hadoop-customize-cluster-linux.md): Lær at bruge scripthandling til at installere flere programmer.
- [Oprette Linux-baserede Hadoop klynger i HDInsight ved hjælp af Ressourcestyring skabeloner](hdinsight-hadoop-create-linux-clusters-arm-templates.md): Lær, hvordan du ringe til Ressourcestyring skabeloner for at oprette HDInsight klynger.

