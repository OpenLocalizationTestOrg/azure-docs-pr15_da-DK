<properties
    pageTitle="Access Hadoop GARN program logger fra et program | Microsoft Azure"
    description="Access-program logger ved hjælp af programmering på en Hadoop klynge i HDInsight."
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
    ms.date="10/19/2016"
    ms.author="jgao"/>

# <a name="access-yarn-application-logs-on-windows-based-hdinsight"></a>Access GARN program logger på Windows-baseret HDInsight

Dette emne forklares det, hvordan du kan få adgang til logfiler for GARN (endnu en anden ressource forhandler)-programmer, der er færdige på en Hadoop klynge i Azure HDInsight

> [AZURE.NOTE] Oplysningerne i dette dokument gælder kun for Windows-baseret HDInsight klynger. Du kan finde oplysninger om adgang til GARN logger på Linux-baserede HDInsight klynger, under [Access GARN programmet logger på Linux-baserede Hadoop på HDInsight](hdinsight-hadoop-access-yarn-app-logs-linux.md)

### <a name="prerequisites"></a>Forudsætninger

- En Windows-baseret HDInsight klynge.  Se [oprette Windows-baseret Hadoop klynger i HDInsight](hdinsight-provision-clusters.md).


## <a name="yarn-timeline-server"></a>GARN tidslinje Server

<a href="http://hadoop.apache.org/docs/r2.4.0/hadoop-yarn/hadoop-yarn-site/TimelineServer.html" target="_blank">GARN tidslinje Server</a> indeholder generelle oplysninger om færdige programmer samt framework-specifikke programmet oplysninger via to forskellige grænseflader. Mere specifikt:

* Lagring og hentning af generisk programoplysninger på HDInsight klynger er blevet aktiveret med version 3.1.1.374 eller nyere.
* Komponenten programmet på computeren framework-specifikke oplysninger på den Server, tidslinje er ikke tilgængelige på HDInsight klynger.


Generelle oplysninger på programmer omfatter følgende typer data:

* Program-ID, et entydigt id for et program
* Den bruger, der startede programmet
* Oplysninger om skal forsøge at afslutte programmet
* Beholdere ved forsøg på et bestemt program

På din HDInsight klynger gemmes disse oplysninger ved Azure ressourcestyring til en oversigt over store i beholderen standard af din standardkonto Azure-lager. Disse generisk data på færdige programmer kan hentes via en REST-API:

    GET on https://<cluster-dns-name>.azurehdinsight.net/ws/v1/applicationhistory/apps


## <a name="yarn-applications-and-logs"></a>Logfiler for programmer GARN og

GARN understøtter flere programming modeller (MapReduce være en af dem) ved afkobling ressourcestyring fra programmet planlægning/overvågning. Dette sker via en global *ressourcestyring* (Ressourcestyring), per-arbejder-node *NodeManagers* (NMs) og -program, *ApplicationMasters* (AMs). Per programmet AM forhandler ressourcer (CPU, hukommelse, disk, netværk) for at køre programmet med ressourcestyring. Ressourcestyring fungerer med NMs give disse ressourcer, som er tildelt som *objektbeholdere*. AM er ansvarlig for sporing af fremskridt af beholdere, der tildeles af Ressourcestyring. Et program kan kræve mange beholdere afhængigt af hvilke af programmet.

Desuden hvert program kan bestå af flere *programmet forsøger* for at afslutte tilstedeværelse af går ned eller på grund af tab af kommunikation mellem AM og en ressourcestyring. Det vil sige, beholdere har fået tildelt en bestemt forsøg af et program. En objektbeholder giver kontekst til grundlæggende enhed for arbejde er udført af en GARN programmet set, og alle arbejde, der er udført i forbindelse med en objektbeholder er udført på noden enkelt arbejder, der er allokeret objektbeholderen. Se [GARN begreber] [ YARN-concepts] til yderligere brug.

Logge af programmet (og loggene tilknyttede objektbeholder) er afgørende i forbindelse med fejlfinding problematisk Hadoop-programmer. GARN indeholder en flot ramme for indsamling, sammenlægning og lagring af programmet logfiler med [Log sammenlægning] [ log-aggregation] funktion. Funktionen Log sammenlægning gør adgang til programmet logfiler mere deterministisk, som samler logge på tværs af alle beholdere på en kollega node og gemmer dem som en samlet logfil per arbejder node i standardfilsystemet, når programmet afsluttes. Programmet kan bruge hundredvis eller tusindvis af objektbeholdere, men logfiler for alle beholdere, der kører på en enkelt arbejder node altid være sammenlagt til en enkelt fil, hvilket resulterer i en logfil per arbejder node, der bruges af dit program. Log sammenlægning er aktiveret som standard HDInsight klynger (version 3.0 og derover), og kan finde aggregeret logfiler i beholderen standard af din klynge på følgende placering:

    wasbs:///app-logs/<user>/logs/<applicationId>

I den valgte placering, *brugeren* er navnet på den bruger, der startede programmet, og *applicationId* er det entydige id for et program, der er tildelt af den GARN ressourcestyring.

Loggene aggregeret er ikke direkte læsbare, som de er skrevet i en [TFile][T-file], [binært format] [ binary-format] indekseret af objektbeholder. GARN indeholder CLI værktøjer lagring af disse logfiler som almindelig tekst for programmer eller beholdere af interesse. Du kan få vist disse logfiler, som almindelig tekst ved at køre en af følgende GARNET kommandoer direkte på klyngenoderne (når du har forbindelse til den via RDP):

    yarn logs -applicationId <applicationId> -appOwner <user-who-started-the-application>
    yarn logs -applicationId <applicationId> -appOwner <user-who-started-the-application> -containerId <containerId> -nodeAddress <worker-node-address>


## <a name="yarn-resourcemanager-ui"></a>GARN ressourcestyring brugergrænseflade

GARN ressourcestyring Brugergrænsefladen kører på klynge headnode og kan åbnes fra dashboardet Azure portalen: 

1. Log på [Azure-portalen](https://portal.azure.com/). 
2. På menuen til venstre, klik på **Gennemse**, klik på **HDInsight klynger**, skal du klikke på en Windows-baseret klynge, som du vil have adgang til loggene GARN programmet på computeren.
3. Klik på **Dashboard**i den øverste menu. Du får vist en side, der er åbnet på en ny browser fanen kaldet **HDInsight forespørgsel Console**.
4. Klik på **Garn Brugergrænsefladen**fra **HDInsight forespørgsel Console**.




[YARN-timeline-server]:http://hadoop.apache.org/docs/r2.4.0/hadoop-yarn/hadoop-yarn-site/TimelineServer.html
[log-aggregation]:http://hortonworks.com/blog/simplifying-user-logs-management-and-access-in-yarn/
[T-file]:https://issues.apache.org/jira/secure/attachment/12396286/TFile%20Specification%2020081217.pdf
[binary-format]:https://issues.apache.org/jira/browse/HADOOP-3315
[YARN-concepts]:http://hortonworks.com/blog/apache-hadoop-yarn-concepts-and-applications/
