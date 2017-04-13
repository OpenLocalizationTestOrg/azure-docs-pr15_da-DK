<properties
   pageTitle="Installere og administrere Apache Storm topologier på Linux-baserede HDInsight | Microsoft Azure"
   description="Lær, hvordan du installerer, overvåge og administrere Apache Storm topologier ved hjælp af dashboardet Storm på Linux-baserede HDInsight. Brug Hadoop tools til Visual Studio."
   services="hdinsight"
   documentationCenter=""
   authors="Blackmist"
   manager="jhubbard"
   editor="cgronlun"/>

<tags
   ms.service="hdinsight"
   ms.devlang="java"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data"
   ms.date="09/07/2016"
   ms.author="larryfr"/>

# <a name="deploy-and-manage-apache-storm-topologies-on-linux-based-hdinsight"></a>Installere og administrere Apache Storm topologier på Linux-baserede HDInsight

Lær de grundlæggende regler for administration og overvågning af Storm topologier kører på Linux-baserede Storm på HDInsight klynger i dette dokument.

> [AZURE.IMPORTANT] Trinnene i denne artikel kræver en Linux-baserede Storm på HDInsight klynge. Finde oplysninger om installation og overvågning topologier på Windows-baseret HDInsight i [Implementer og administrere Apache Storm topologier på Windows-baseret HDInsight](hdinsight-storm-deploy-monitor-topology.md)

## <a name="prerequisites"></a>Forudsætninger

* **A Linux-baserede Storm på HDInsight klynge**: se [komme i gang med Apache Storm på HDInsight](hdinsight-apache-storm-tutorial-get-started-linux.md) trin til oprettelse af en klynge

* **Kendskab til SSH og SCP**: Du kan finde flere oplysninger om brug af SSH og SCP med HDInsight, skal du se følgende:

    * **Linux, Unix eller OS X-klienter**: se [Brug SSH med Linux-baserede Hadoop på HDInsight fra Linux, OS X eller Unix](hdinsight-hadoop-linux-use-ssh-unix.md)

    * **Windows-klienter**: se [Brug SSH med Linux-baserede Hadoop på HDInsight fra Windows](hdinsight-hadoop-linux-use-ssh-windows.md)

* **En SCP klient**: Dette leveres med alle Linux, Unix og OS X-systemer. Windows-klienter, anbefaler vi at PSCP, som er tilgængelig fra siden [trykfarver overførselssiden](http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html).

## <a name="start-a-storm-topology"></a>Starte en Storm topologi

### <a name="using-ssh-and-the-storm-command"></a>Brug af SSH og kommandoen Storm

1. Brug SSH til at oprette forbindelse til HDInsight klyngen. Erstatte **brugernavn** på navnet på dit SSH logon. Erstat **CLUSTERNAME** med dit HDInsight klyngenavn:

        ssh USERNAME@CLUSTERNAME-ssh.azurehdinsight.net

    Du kan finde flere oplysninger om brug af SSH til at oprette forbindelse til din HDInsight klynge, følgende dokumenter:
    
    * **Linux, Unix eller OS X-klienter**: se [Brug SSH med Linux-baserede Hadoop på HDInsight fra Linux, OS X eller Unix](hdinsight-hadoop-linux-use-ssh-unix.md)
    
    * **Windows-klienter**: se [Brug SSH med Linux-baserede Hadoop på HDInsight fra Windows](hdinsight-hadoop-linux-use-ssh-windows.md)

2. Brug følgende kommando til at starte en topologi for eksempel:

        storm jar /usr/hdp/current/storm-client/contrib/storm-starter/storm-starter-topologies-0.9.3.2.2.4.9-1.jar storm.starter.WordCountTopology WordCount

    Dette starter eksempel WordCount topologien på klyngen. Den tilfældigt generere sætninger og tælle forekomster af hvert ord i sætningerne.

    > [AZURE.NOTE] Når du indsender topologi til klyngen, skal du først kopiere filen glas, der indeholder klyngen inden du bruger den `storm` kommandoen. Dette kan gøres ved hjælp af den `scp` kommando fra den klient, hvor filen findes. For eksempel`scp FILENAME.jar USERNAME@CLUSTERNAME-ssh.azurehdinsight.net:FILENAME.jar`
    >
    > Eksemplet WordCount og andre storm starter eksempler findes allerede på din klynge på `/usr/hdp/current/storm-client/contrib/storm-starter/`.

### <a name="programmatically"></a>Fra et program

Du kan automatisk distribuere en topologi til Storm på HDInsight ved at kommunikere med tjenesten Nimbus din klynge som vært. [https://github.com/Azure-Samples/hdinsight-Java-Deploy-storm-topology](https://github.com/Azure-Samples/hdinsight-java-deploy-storm-topology) indeholder et eksempel Java-program, der viser, hvordan du installerer og starte en topologi gennem tjenesten Nimbus.

## <a name="monitor-and-manage-using-the-storm-command"></a>Overvåge og administrere ved hjælp af kommandoen storm

Den `storm` utility gør det muligt at arbejde med kører topologier fra kommandolinjen. Følgende er en liste over ofte anvendte kommandoer. Brug `storm -h` for en komplet liste over kommandoer.

### <a name="list-topologies"></a>Listen topologier

Brug følgende kommando for at vise alle kører topologier:

    storm list
    
Dette vil returnere oplysninger som følger:

    Topology_name        Status     Num_tasks  Num_workers  Uptime_secs
    -------------------------------------------------------------------
    WordCount            ACTIVE     29         2            263

### <a name="deactivate-and-reactivate"></a>Deaktivere og genaktivere

Deaktivere en topologi afbrydes det indtil den er blevet slået ned eller igen. Bruge følgende for at deaktivere og genaktivere:

    storm Deactivate TOPOLOGYNAME
    
    storm Activate TOPOLOGYNAME

### <a name="kill-a-running-topology"></a>Slet en igangværende topologi

Storm topologier, når den er startet, fortsat kører, til showet afbrydes. Brug følgende kommando til at stoppe en topologi:

    storm stop TOPOLOGYNAME

### <a name="rebalance"></a>Genoprette

Skulle genoprette balancen en topologi, kan der revidere parallelitet for topologien. Eksempelvis hvis du har tilpasset klynge for at tilføje flere noter, skulle genoprette balancen giver adgang til en igangværende topologi at gøre brug af de nye noder.

> [AZURE.WARNING] Skulle genoprette balancen en topologi først deaktiverer topologien, omfordeler medarbejdere jævnt på tværs af klyngen, derefter til sidst returnerer topologien til den tilstand, den var i, før skulle genoprette balancen opstod. Så hvis topologien var aktiv, bliver aktive igen. Hvis den er blevet deaktiveret, bliver den deaktiveret.

    storm rebalance TOPOLOGYNAME

## <a name="monitor-and-manage-using-the-storm-ui"></a>Overvåge og administrere ved hjælp af brugergrænsefladen i Storm.

Storm Brugergrænsefladen har en grænseflade til at arbejde med kører topologier og findes på din HDInsight klynge. For at få vist Storm Brugergrænsefladen, at bruge en webbrowser til at åbne __https://CLUSTERNAME.azurehdinsight.net/stormui__, hvor __CLUSTERNAME__ er navnet på din klynge.

> [AZURE.NOTE] Hvis bedt om at angive et brugernavn og adgangskode, kan du angive klyngeadministratoren (admin) og adgangskode, du brugte hvornår oprettelse af klyngen.


### <a name="main-page"></a>Hovedsiden

Hovedsiden for Storm Brugergrænsefladen indeholder følgende oplysninger:

* **Klynge oversigt**: grundlæggende oplysninger om Storm klynge.

* **Topologi oversigt**: en liste over kører topologier. Bruge linkene i dette afsnit for at få flere oplysninger om bestemte topologier.

* **Overordnede oversigt**: oplysninger om Storm overordnede.

* **Konfiguration af nimbus**: Nimbus konfiguration for-klyngen.

### <a name="topology-summary"></a>Topologi for oversigt

Vælge et link fra sektionen **topologi oversigt** viser følgende oplysninger om topologien:

* **Topologi oversigt**: grundlæggende oplysninger om topologien.

* **Topologi handlinger**: Administration af handlinger, der kan udføres for topologien.

  * **Aktivér**: CV'er behandling af en deaktiverede topologi.

  * **Deaktiver**: afbrydes en igangværende topologi.

  * **Genoprette**: justerer parallelitet for topologien. Du skal genoprette kører topologier, når du har ændret antallet af knuder i klyngen. Dette giver mulighed for topologien til at justere parallelitet som kompensation for forøget eller reduceret antallet af knuder på klyngen.

    Du kan finde flere oplysninger, kan du se <a href="http://storm.apache.org/documentation/Understanding-the-parallelism-of-a-Storm-topology.html" target="_blank">om parallelitet af en Storm topologi</a>.

  * **Slet**: ophører en Storm topologi efter den angivne timeout.

* **Topologi statistik**: statistik om topologien. Bruge linkene i kolonnen **vinduet** til at angive tidsrummet for de resterende poster på siden.

* **Spouts**: spouts bruges af topologien. Bruge linkene i dette afsnit for at få flere oplysninger om bestemte spouts.

* **Bolte**: bolte bruges af topologien. Bruge linkene i dette afsnit for at få flere oplysninger om bestemte bolte.

* **Topologi konfiguration**: konfigurationen af den valgte topologi.

### <a name="spout-and-bolt-summary"></a>Tud og Bolt oversigt

Hvis du vælger en tud fra afsnittene **Spouts** eller **bolte,** vises følgende oplysninger om det valgte element:

* **Komponent oversigt**: grundlæggende oplysninger om tud eller bolt.

* **Tud/Bolt statistik**: statistik om tud eller bolt. Bruge linkene i kolonnen **vinduet** til at angive tidsrummet for de resterende poster på siden.

* **Input statistik** (kun bolt): oplysninger om input streams consumed ved at bolten.

* **Output statistik**: oplysninger om streams udsendes af dette spout eller bolt.

* **Bobestyreren**: oplysninger om forekomster af tud eller bolt. Vælg et **Port** element til en bestemt executor til at få vist en logfil over diagnostiske oplysninger, der er oprettet for denne forekomst.

* **Fejl**: en hvilken som helst fejloplysninger for dette spout eller bolt.

## <a name="rest-api"></a>REST-API

Storm Brugergrænsefladen er bygget oven på REST-API, så du kan udføre lignende administration og overvågning funktionalitet ved hjælp af REST-API. Du kan bruge REST-API til at oprette brugerdefinerede værktøjer til at administrere og overvågning Storm topologier.

Du kan finde yderligere oplysninger finder [Storm Brugergrænsefladen REST-API](http://storm.apache.org/releases/0.9.6/STORM-UI-REST-API.html). Følgende oplysninger er specifikke for brug af REST-API med Apache Storm på HDInsight.

> [AZURE.IMPORTANT] Storm REST-API er ikke offentligt tilgængeligt via internettet, og skal åbnes ved hjælp af en SSH tunnel til noden HDInsight klynge hoved. Finde oplysninger om oprettelse og brug af en SSH tunnel, kan du se [Brug SSH tunnel til adgang til Ambari web brugergrænseflade, Ressourcestyring, JobHistory, NameNode, Oozie, og andre web Brugergrænsefladens](hdinsight-linux-ambari-ssh-tunnel.md).

### <a name="base-uri"></a>Base URI

Den grundlæggende URI for REST-API på Linux-baserede HDInsight klynger er tilgængelig på noden hoved på **api-https://HEADNODEFQDN:8744/v1/**; dog domænenavn for noden hoved genereres under oprettelse af klynge og er ikke statisk.

Du kan finde det fulde domænenavn (fulde Domænenavn) til Klyngenoden hoved på flere forskellige måder:

* __Fra en SSH session__: bruge kommandoen `headnode -f` fra en SSH session til klyngen.

* __Fra Ambari Web__: vælge __tjenester__ fra toppen af siden og derefter vælge __Storm__. Vælg __Storm Brugergrænsefladen Server__under fanen __Oversigt__ . Det fulde Domænenavn for noden, Storm Brugergrænsefladen og REST-API, der kører på bliver øverst på siden.

* __Fra Ambari REST-API__: bruge kommandoen `curl -u admin:PASSWORD -G "https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/services/STORM/components/STORM_UI_SERVER"` til at hente oplysninger om noden, Storm Brugergrænsefladen og REST-API, der kører på. Erstat __adgangskode__ med administratoradgangskode for-klyngen. Erstat __CLUSTERNAME__ med klyngenavnet. I svaret indeholder posten "værtsnavn" det fuldstændige Domænenavn for noden.

### <a name="authentication"></a>Godkendelse

Anmodninger om at REST-API skal bruge **basisgodkendelse**, så du kan bruge den HDInsight klynge administratornavn og adgangskode.

> [AZURE.NOTE] Fordi basisgodkendelse sendes ved hjælp af klar tekst, bør du **altid** Brug HTTPS til sikker kommunikation med klyngen.

### <a name="return-values"></a>Returnere værdier

Oplysninger, der returneres fra REST-API kan kun bruges fra på klynge eller virtuelle maskiner på det samme Azure virtuelle netværk som klyngen. For eksempel vil det fuldstændige domænenavn (fulde Domænenavn) returneres for Zookeeper servere ikke være tilgængeligt via internettet.

## <a name="next-steps"></a>Næste trin

Nu hvor du har lært at installere og overvåge topologier ved hjælp af Storm Dashboard, lære, hvordan du [udvikle Java-baserede topologier ved hjælp af Maven](hdinsight-storm-develop-java-topology.md).

Du kan finde en liste over flere eksempel topologier, [eksempel topologier for Storm på HDInsight](hdinsight-storm-example-topology.md).
