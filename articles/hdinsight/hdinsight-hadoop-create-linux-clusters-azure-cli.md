<properties
    pageTitle="Oprette Hadoop, HBase eller Storm klynger på Linux i HDInsight med på tværs af platforme Azure CLI | Microsoft Azure"
    description="Få mere at vide, hvordan du opretter Linux-baserede HDInsight klynger med på tværs af platforme Azure CLI, Azure ressourcestyring skabeloner og Azure REST-API. Du kan angive, hvilke klynge (Hadoop, HBase eller Storm) eller bruge scripts til at installere brugerdefinerede komponenter.."
    services="hdinsight"
    documentationCenter=""
    authors="Blackmist"
    manager="jhubbard"
    editor="cgronlun"
    tags="azure-portal"/>

<tags
    ms.service="hdinsight"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="big-data"
    ms.date="09/20/2016"
    ms.author="larryfr"/>

#<a name="create-linux-based-clusters-in-hdinsight-using-the-azure-cli"></a>Oprette Linux-baserede klynger i HDInsight ved hjælp af Azure CLI

[AZURE.INCLUDE [selector](../../includes/hdinsight-selector-create-clusters.md)]

Azure CLI er et på tværs af platforme kommandolinjen værktøj, der tillader dig at administrere Azure Services. Det kan bruges, sammen med Azure ressource datastyring skabeloner til at oprette en HDInsight klynge, sammen med tilknyttede lagerplads konti og andre tjenester.

Azure ressourcestyring skabeloner er JSON-dokumenter, der beskriver en __ressourcegruppe__ og alle de ressourcer i den (såsom HDInsight.) Denne skabelon baseret fremgangsmåde gør det muligt at definere alle de ressourcer, som du har brug for HDInsight i en skabelon. Det kan du også administrere ændringer til gruppen som helhed gennem __installationer__, som vedrører hele gruppen ændringer.

Trinnene i dette dokument gennemgå processen med at oprette en ny HDInsight klynge ved hjælp af Azure CLI og en skabelon.

> [AZURE.IMPORTANT] Trinnene i dette dokument bruge standardantallet af arbejder noder (4) til en HDInsight klynge. Hvis du planlægger på mere end 32 arbejder noder (under oprettelse af klynge eller ved at skalere klyngen), skal du vælge en hoved node størrelse med mindst 8 kerner og 14 GB ram.
>
> Se [HDInsight priser](https://azure.microsoft.com/pricing/details/hdinsight/)kan finde flere oplysninger om node størrelser og det tilknyttede omkostninger.

##<a name="prerequisites"></a>Forudsætninger

[AZURE.INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

- **En Azure-abonnement**. Se [få Azure gratis prøveversion](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).
- __Azure CLI__. Trinnene i dette dokument er blevet sidst testet med Azure CLI version 0.10.1.

    [AZURE.INCLUDE [use-latest-version](../../includes/hdinsight-use-latest-cli.md)] 


### <a name="access-control-requirements"></a>Krav til Access

[AZURE.INCLUDE [access-control](../../includes/hdinsight-access-control-requirements.md)]

##<a name="log-in-to-your-azure-subscription"></a>Log på abonnementet Azure

Følg trinnene beskrevet i [oprette forbindelse til et Azure-abonnement fra Azure kommandolinjen (Azure CLI)](../xplat-cli-connect.md) og oprette forbindelse til dit abonnement ved hjælp af metoden __login__ .

##<a name="create-a-cluster"></a>Oprette en klynge

Følgende trin udføres fra en kommandoprompt, shell eller terminal session efter installation og konfiguration af Azure CLI.

1. Brug følgende kommando til at godkende abonnementet Azure:

        azure login

    Du bliver bedt om at angive dit navn og en adgangskode. Hvis du har flere Azure abonnementer, kan du bruge `azure account set <subscriptionname>` til at angive det abonnement, der bruger Azure CLI-kommandoer.

3. Skifte til Azure ressourcestyring tilstand ved hjælp af følgende kommando:

        azure config mode arm

4. Oprette en ressourcegruppe. Denne ressourcegruppe indeholder HDInsight klynge og tilhørende lagerplads konto.

        azure group create groupname location
        
    * Erstat __gruppenavn__ med et entydigt navn til gruppen. 
    * Erstat __placering__ med det geografiske område, du vil oprette en gruppe. 
    
        Brug af en liste over gyldige placeringer på `azure location list` kommando, og brug derefter en af placeringerne fra kolonnen __navn__ .

5. Oprette en lagerplads konto. Denne lagerplads konto bruges som standard opbevaring for HDInsight-klyngen.

        azure storage account create -g groupname --sku-name RAGRS -l location --kind Storage storagename
        
     * Erstat __gruppenavn__ med navnet på den gruppe, der er oprettet i ovenstående trin.
     * Erstat __placering__ med den samme placering, der bruges i det forrige trin. 
     * Erstat __storagename__ med et entydigt navn til kontoen lagerplads.
     
     > [AZURE.NOTE] Flere oplysninger om de parametre, bruges i denne kommando, bruge `azure storage account create -h` til at få vist hjælp til denne kommando.

5. Hent tasten bruges til at få adgang til kontoen lagerplads.

        azure storage account keys list -g groupname storagename
        
    * Erstat __gruppenavn__ med ressource gruppenavn.
    * Erstat __storagename__ med navnet på kontoen, lagerplads.
    
    I de data, der returneres, Gem __nøgle__ værdien for __nøgle1__.

6. Oprette en HDInsight klynge.

        azure hdinsight cluster create -g groupname -l location -y Linux --clusterType Hadoop --defaultStorageAccountName storagename.blob.core.windows.net --defaultStorageAccountKey storagekey --defaultStorageContainer clustername --workerNodeCount 2 --userName admin --password httppassword --sshUserName sshuser --sshPassword sshuserpassword clustername

    * Erstat __gruppenavn__ med ressource gruppenavn.

    * Erstat __Hadoop__ med typen klynge, du vil oprette. For eksempel `Hadoop`, `HBase`, `Storm` eller `Spark`.

        > [AZURE.IMPORTANT] HDInsight klynger kommer i en række forskellige typer, der svarer til det arbejdsbelastningen eller den teknologi, der er tilpasset klyngen til. Der er ingen understøttet metode til at oprette en klynge, der kombinerer flere typer, som Storm og HBase på en klynge. 

    * Erstat __placering__ med den samme placering, der bruges i forrige trin.

    * Erstat __storagename__ med kontonavn lager.

    * Erstat __storagekey__ med den nøgle, der fås i ovenstående trin. 

    * For den `--defaultStorageContainer` parameter, Brug samme navn som du bruger til klyngen.

    * Erstat __admin__ og __httppassword__ med brugernavn og adgangskode, du vil bruge, når du åbner klyngen via HTTPS.

    * Erstatte __sshuser__ og __sshuserpassword__ med det brugernavn og adgangskode, du vil bruge, når du åbner den klynge ved hjælp af SSH

    Det kan tage flere minutter, før oprettelsen af klynge at afslutte. Normalt omkring 15.

##<a name="next-steps"></a>Næste trin

Nu hvor du har oprettet en HDInsight klynge, ved hjælp af Azure CLI, kan du bruge følgende til at få mere for at vide om at arbejde med din klynge:

###<a name="hadoop-clusters"></a>Hadoop klynger

* [Bruge Hive med HDInsight](hdinsight-use-hive.md)
* [Brug gris med HDInsight](hdinsight-use-pig.md)
* [Bruge MapReduce med HDInsight](hdinsight-use-mapreduce.md)

###<a name="hbase-clusters"></a>HBase klynger

* [Komme i gang med HBase på HDInsight](hdinsight-hbase-tutorial-get-started-linux.md)
* [Udvikle Java-programmer til HBase på HDInsight](hdinsight-hbase-build-java-maven-linux.md)

###<a name="storm-clusters"></a>Storm klynger

* [Udvikle Java topologier for Storm på HDInsight](hdinsight-storm-develop-java-topology.md)
* [Bruge Python komponenter i Storm på HDInsight](hdinsight-storm-develop-python-topology.md)
* [Installere og overvåge topologier med Storm på HDInsight](hdinsight-storm-deploy-monitor-topology-linux.md)
