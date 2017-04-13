<properties
    pageTitle="Bruge farvetone med Hadoop på HDInsight Linux klynger | Microsoft Azure"
    description="Lær, hvordan du installerer og bruger farvetone med Hadoop klynger på HDInsight Linux."
    services="hdinsight"
    documentationCenter=""
    authors="nitinme"
    manager="jhubbard"
    editor="cgronlun"/>

<tags 
    ms.service="hdinsight" 
    ms.workload="big-data" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="09/13/2016" 
    ms.author="nitinme"/>

# <a name="install-and-use-hue-on-hdinsight-hadoop-clusters"></a>Installere og bruge farvetone på HDInsight Hadoop klynger

Lær, hvordan du installerer farvetone på HDInsight Linux klynger og bruge tunnelføring til at dirigere anmodninger til farvetone.

## <a name="what-is-hue"></a>Hvad er farvetone?

Nuance er et sæt af webprogrammer, der bruges til at interagere med en Hadoop-klynge. Du kan bruge Farvetone til at gennemse den lagerplads, der er knyttet til et Hadoop-klynge (WASB, hvis det er HDInsight klynger), kan køre Hive job og gris scripts osv. Følgende komponenter er tilgængelige med farvetone installationer på en HDInsight Hadoop-klynge.

* Bivoks Hive Editor
* Gris
* Metastore manager
* Oozie
* FileBrowser (som taler med WASB standard objektbeholder)
* Jobbet Browser

> [AZURE.WARNING] Komponenter i HDInsight klynge understøttes fuldt ud, og Microsoft Support hjælper med at isolere og løse problemer i forbindelse med disse komponenter.
>
> Brugerdefinerede komponenter modtager kommercielt begrundet support for at hjælpe dig med at foretage yderligere fejlfinding af problemet. Dette kan medføre løse problemet eller beder dig om at deltage tilgængelige kanaler for de Åbn kilde-teknologier, hvor deep ekspertise i forbindelse med teknologien, der er fundet. For eksempel, der er mange communitywebsteder, der kan bruges, ønsker: [MSDN-forum for HDInsight](https://social.msdn.microsoft.com/Forums/azure/en-US/home?forum=hdinsight), [http://stackoverflow.com](http://stackoverflow.com). Også Apache projekter har projektwebsteder på [http://apache.org](http://apache.org), for eksempel: [Hadoop](http://hadoop.apache.org/).

## <a name="install-hue-using-script-actions"></a>Installere farvetone ved hjælp af scripthandlinger

Handlingen følgende script kan bruges til at installere farvetone på en Linux-baserede HDInsight klynge.
https://hdiconfigactions.BLOB.Core.Windows.NET/linuxhueconfigactionv02/Install-Hue-uber-v02.sh
    
Dette afsnit indeholder oplysninger om, hvordan du bruger scriptet, når den klynge ved hjælp af portalen Azure. 

> [AZURE.NOTE] Azure PowerShell, Azure CLI, HDInsight .NET SDK eller Azure ressourcestyring skabeloner kan også bruges til at anvende scripthandlinger. Du kan også anvende scripthandlinger på allerede kører klynger. Se [tilpasse HDInsight klynger med scripthandlinger](hdinsight-hadoop-customize-cluster-linux.md)kan finde flere oplysninger.

1. Start klargøring en klynge ved hjælp af trinnene i [klargøring HDInsight klynger på Linux](hdinsight-hadoop-provision-linux-clusters.md#portal), men ikke fuldføre klargøring.

    > [AZURE.NOTE] For at installere farvetone på HDInsight klynger, er det anbefalede headnode størrelse mindst A4 (8 kerner, 14 GB hukommelse).

2. Vælg **Scripthandlinger**på bladet **Valgfri konfiguration** , og angiv oplysninger, som vist nedenfor:

    ![Angiv script handlingsparametre for farvetone] (./media/hdinsight-hadoop-hue-linux/hue_script_action.png "Angiv script handlingsparametre for farvetone")

    * __Navn__: Angiv et fuldt navn for handlingen script.
    * __SCRIPT URI__: https://hdiconfigactions.blob.core.windows.net/linuxhueconfigactionv02/install-hue-uber-v02.sh
    * __Afsnit__: kontrollere denne indstilling
    * __Arbejder__: Lad dette stå tomt.
    * __ZOOKEEPER__: Lad dette stå tomt.
    * __Parametre__: Lad dette stå tomt.

3. I bunden af **Scripthandlinger**, kan du bruge knappen **Vælg** til at gemme konfigurationen. Til sidst skal bruge knappen **Vælg** i bunden af bladet **Valgfri konfiguration** til at gemme oplysningerne om valgfri konfiguration.

4. Fortsæt med klargøring klyngen, som beskrevet i [klargøring HDInsight klynger på Linux](hdinsight-hadoop-provision-linux-clusters.md#portal).

## <a name="use-hue-with-hdinsight-clusters"></a>Brug farvetone med HDInsight klynger

SSH Tunneling er den eneste måde at få adgang til farvetone på klyngen, når den kører. Tunnelføring via SSH tillader trafikken at gå direkte til headnode af klyngen hvor farvetone kører. Efter klyngen er blevet klargøring, skal du følge nedenstående trin for at bruge farvetone på en HDInsight Linux-klynge.

1. Brug oplysningerne i [Brug SSH tunnel til adgang til Ambari web brugergrænseflade, Ressourcestyring, JobHistory, NameNode, Oozie, og andre web Brugergrænsefladens](hdinsight-linux-ambari-ssh-tunnel.md) til at oprette en SSH tunnel fra dit klientsystem til HDInsight klynge, og derefter konfigurere din webbrowser for at bruge SSH tunnel som en proxy.

2. Når du har oprettet en SSH tunnel og konfigureret din browser til proxy trafik via det, skal du finde værtsnavnet på den primære hovedsæde node. Du kan gøre dette ved at oprette forbindelse til den klynge ved hjælp af SSH på port 22. For eksempel `ssh USERNAME@CLUSTERNAME-ssh.azurehdinsight.net` hvor __brugernavn__ er dit SSH brugernavn og __CLUSTERNAME__ er navnet på din klynge.

    Du kan finde flere oplysninger om brug af SSH at se de dokumenter, der er følgende:

    * [Bruge SSH med Linux-baserede HDInsight fra en Linux, Unix eller Mac OS X-klient](hdinsight-hadoop-linux-use-ssh-unix.md)
    * [Bruge SSH med Linux-baserede HDInsight fra en Windows-klient](hdinsight-hadoop-linux-use-ssh-windows.md)

3. Når forbindelse, kan du bruge følgende kommando til at få det fulde domænenavn for den primære headnode:

        hostname -f

    Dette vil returnere et navn, der er som følger:

        hn0-myhdi-nfebtpfdv1nubcidphpap2eq2b.ex.internal.cloudapp.net
    
    Dette er værtsnavnet for den primære headnode, hvor webstedet farvetone er placeret.

2. Brug browseren til at åbne farvetone-portalen på http://HOSTNAME:8888. Erstatte HOSTNAME med det navn, du har hentet i det forrige trin.

    > [AZURE.NOTE] Når du logger på for første gang, skal du bedt om at oprette en konto til at logge på portalen farvetone. De legitimationsoplysninger, du angiver her, vil være begrænset til portalen og ikke er relateret til administrator eller SSH brugerlegitimationsoplysninger, du har angivet under klargøring af klynge.

    ![Log på portalen farvetone] (./media/hdinsight-hadoop-hue-linux/HDI.Hue.Portal.Login.png "Angiv legitimationsoplysninger for farvetone portal")

### <a name="run-a-hive-query"></a>Køre en Hive-forespørgsel

1. Klik på **Forespørgsel redaktører**fra portalen Farvetone, og klik derefter på **Hive** for at åbne redigeringsprogrammet Hive.

    ![Bruge Hive] (./media/hdinsight-hadoop-hue-linux/HDI.Hue.Portal.Hive.png "Bruge Hive")

2. På fanen **hjælpe** under **Database**, skal du se **hivesampletable**. Dette er et eksempel på tabellen, som leveres med alle Hadoop klynger på HDInsight. Indtast en eksempelforespørgsel i højre rude og se output på fanen **resultater** i ruden nedenfor, som vist i skærmbilledet.

    ![Køre Hive forespørgsel] (./media/hdinsight-hadoop-hue-linux/HDI.Hue.Portal.Hive.Query.png "Køre Hive forespørgsel")

    Du kan også bruge fanen **diagram** til at se en visuel repræsentation af resultatet.

### <a name="browse-the-cluster-storage"></a>Gennemse den klynge lagerplads

1. Klik på **Fil browseren** i øverste højre hjørne af menulinjen fra portalen farvetone.

2. Som standard åbnes filer browseren på mappen **/user/myuser** . Klik på skråstregen lige før mappen bruger på stien til gå til rodwebstedet for objektbeholderen Azure-lager, der er knyttet til klyngen.

    ![Brug fil browser] (./media/hdinsight-hadoop-hue-linux/HDI.Hue.Portal.File.Browser.png "Brug fil browser")

3. Højreklik på en fil eller mappe for at se de tilgængelige handlinger. Brug knappen **Overfør** til højre til at overføre filer til den aktuelle mappe. Brug knappen **Ny** til at oprette nye filer eller mapper.

> [AZURE.NOTE] Farvetone fil browseren kan kun vise indholdet af objektbeholderen standard, der er knyttet til HDInsight klyngen. Eventuelle ekstra lagerplads konti/objektbeholdere, som du muligvis har knyttet til klyngen vil ikke være tilgængelig via browseren fil. Dog være de ekstra beholdere, der er knyttet til klyngen altid tilgængelige for Hive-job. Hvis du angiver kommandoen eksempelvis `dfs -ls wasbs://newcontainer@mystore.blob.core.windows.net` i redigeringsprogrammet Hive, kan du se indholdet af flere beholdere samt. I denne kommando er **newcontainer** ikke objektbeholderen standard, der er knyttet til en klynge.

## <a name="important-considerations"></a>Vigtige overvejelser

1. Bruges til at installere farvetone scriptet installerer den kun på den primære headnode af klyngen.

2. Under installationen, er flere Hadoop-tjenester (HDFS, GARN, MR2, Oozie) genstartet for opdatering af konfiguration. Når scriptet er færdig med at installere Farvetone, kan det tage lidt tid til andre Hadoop-tjenesterne til at starte. Dette kan påvirke ydeevnen Farvetones først. Når alle tjenester der starter, vil der være farvetone fuldt funktionelle.

3.  Farvetone forstår ikke Tez job, som er den aktuelle standard for Hive. Hvis du vil bruge MapReduce som Hive udførelse af programmet, kan du opdatere scriptet for at bruge følgende kommando i dit script:

        set hive.execution.engine=mr;

4.  Du kan have et scenarie, hvor dine tjenester der kører på den primære headnode, mens ressourcestyring kunne kører på sekundært med Linux klynger. Sådan et scenario kan medføre fejl (vist nedenfor), når du bruger Farvetone til at få vist oplysninger om kører job klyngen. Du kan se oplysninger om job, når jobbet er fuldført.

    ![Farvetone portalen fejl] (./media/hdinsight-hadoop-hue-linux/HDI.Hue.Portal.Error.png "Farvetone portalen fejl")

    Dette er på grund af et kendt problem. Som en løsning kan du ændre Ambari, så aktive ressourcestyring også kører på den primære headnode.

5.  Farvetone forstår WebHDFS, mens HDInsight klynger bruger Azure-lager ved hjælp af `wasbs://`. Så installeres på brugerdefineret script, der bruges med scripthandling WebWasb, som er en WebHDFS-kompatibel tjeneste tale med WASB. Så, selvom portalen farvetone står HDFS på forskellige steder (som når du flytter musen hen over **Fil Browser**), den skal fortolkes som WASB.


## <a name="next-steps"></a>Næste trin

- [Installere Giraph på HDInsight klynger](hdinsight-hadoop-giraph-install-linux.md). Bruge klynge tilpasning til at installere Giraph på HDInsight Hadoop klynger. Giraph gør det muligt at udføre graph behandling ved hjælp af Hadoop, og den kan bruges med Azure HDInsight.

- [Installere Solr på HDInsight klynger](hdinsight-hadoop-solr-install-linux.md). Bruge klynge tilpasning til at installere Solr på HDInsight Hadoop klynger. Solr giver dig mulighed at udføre effektiv søgning handlinger på lagrede data.

- [Installere R på HDInsight klynger](hdinsight-hadoop-r-scripts-linux.md). Bruge klynge tilpasning til at installere R på HDInsight Hadoop klynger. R er et open source-sprog og miljø til statistiske computing. Den indeholder hundredvis af indbyggede statistiske funktioner og sin egen programmeringssprog, der kombinerer aspekter af funktionelle og objektorienteret programmering. Den indeholder også omfattende grafiske funktioner.

[powershell-install-configure]: install-configure-powershell-linux.md
[hdinsight-provision]: hdinsight-provision-clusters-linux.md
[hdinsight-cluster-customize]: hdinsight-hadoop-customize-cluster-linux.md
