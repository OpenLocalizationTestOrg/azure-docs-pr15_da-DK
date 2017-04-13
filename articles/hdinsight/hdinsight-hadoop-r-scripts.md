<properties
    pageTitle="Brug R i HDInsight til at tilpasse klynger | Microsoft Azure"
    description="Lær, hvordan du installerer R ved hjælp af Script handlingen, og brug R på HDInsight klynger."
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
    ms.date="09/14/2016"
    ms.author="jgao"/>

# <a name="install-and-use-r-on-hdinsight-hadoop-clusters"></a>Installere og bruge R på HDInsight Hadoop klynger

Lær, hvordan du tilpasser Windows baseret HDInsight klynge med R ved hjælp af Script handlingen, og hvordan du bruger R på HDInsight klynger. Det [premium niveau](https://azure.microsoft.com/pricing/details/hdinsight/) tilbyder for HDInsight omfatter R Server som en del af din HDInsight klynge. Dette giver mulighed for at bruge MapReduce og knallertmotor til at køre fordelt beregninger R scripts. Yderligere oplysninger finder du se [Introduktion til brug af R Server på HDInsight](hdinsight-hadoop-r-server-get-started.md). Se oplysninger om brug af R med en Linux-baserede klynge, [installation og brug R på HDinsight Hadoop klynger (Linux)](hdinsight-hadoop-r-scripts-linux.md).
 
Du kan installere R på alle typer klynge (Hadoop Storm HBase, og knallertmotor) på Azure HDInsight ved hjælp af *Scripthandling*. Et eksempel på script til at installere R på en HDInsight klynge er tilgængelig fra en skrivebeskyttet Azure lagerplads blob på [https://hdiconfigactions.blob.core.windows.net/rconfigactionv02/r-installer-v02.ps1](https://hdiconfigactions.blob.core.windows.net/rconfigactionv02/r-installer-v02.ps1). 

**Relaterede artikler**

- [Installere og bruge R på HDinsight Hadoop klynger (Linux)](hdinsight-hadoop-r-scripts-linux.md)
- [Oprette Hadoop klynger i HDInsight](hdinsight-provision-clusters.md): generelle oplysninger om oprettelse af HDInsight klynger
- [Tilpasse HDInsight klynge ved hjælp af scripthandling][hdinsight-cluster-customize]: generelle oplysninger om tilpasning af HDInsight klynger ved hjælp af scripthandling
- [Udvikle scripthandling scripts til HDInsight](hdinsight-hadoop-script-actions.md)

## <a name="what-is-r"></a>Hvad er R?

<a href="http://www.r-project.org/" target="_blank">R projekt til statistiske Computing</a> er en åben kildesprog og miljø til statistiske computing. R indeholder hundredvis af indbygget statistiske funktioner og sin egen programmeringssprog, der kombinerer aspekter af funktionelle og objektorienteret programmering. Den indeholder også omfattende grafiske funktioner. R er det foretrukne programming miljø til mest professionel statistikere og forskere i en række forskellige felter.

R er kompatibelt med Azure Blob Storage (WASB), så data, der er gemt der kan udføres ved hjælp af R på HDInsight.  

## <a name="install-r"></a>Installere R

Et [Eksempel på et script](https://hdiconfigactions.blob.core.windows.net/rconfigactionv02/r-installer-v02.ps1) til at installere R på en HDInsight klynge er tilgængelig fra en skrivebeskyttet blob i Azure-lager. Dette afsnit indeholder oplysninger om, hvordan du bruger eksempelscriptet mens du opretter den klynge ved hjælp af portalen Azure.

> [AZURE.NOTE] Eksempelscriptet blev introduceret med HDInsight klynge version 3.1. Du kan finde flere oplysninger om HDInsight klynge versioner [HDInsight klynge versioner](hdinsight-component-versioning.md).

1. Når du opretter en HDInsight klynge fra portalen, skal du klikke på **Valgfri konfiguration**, og klik derefter på **Scripthandlinger**.
2. På siden **Scripthandlinger** skal du angive følgende værdier:

    ![Brug scripthandling til at tilpasse en klynge] (./media/hdinsight-hadoop-r-scripts/hdi-r-script-action.png "Brug scripthandling til at tilpasse en klynge")

    <table border='1'>
        <tr><th>Egenskaben</th><th>Værdi</th></tr>
        <tr><td>Navn</td>
            <td>Angiv et navn for handlingen script, for eksempel <b>Installere R</b>.</td></tr>
        <tr><td>Script URI</td>
            <td>Angive URI til det script, der er aktiveret for at tilpasse klynge, for eksempel <i>https://hdiconfigactions.blob.core.windows.net/rconfigactionv02/r-installer-v02.ps1</i></td></tr>
        <tr><td>Node af Type</td>
            <td>Angiv noderne, hvor tilpasning scriptet skal køres. Du kan vælge <b>Alle noder</b>, <b>hoved noder kun</b>eller <b>arbejder noder</b> kun.
        <tr><td>Parametre</td>
            <td>Angiv parametrene, hvis det er nødvendigt ved scriptet. Scriptet til at installere R kræver dog ikke alle parametre, så du kan lade feltet være tomt.</td></tr>
    </table>

    Du kan tilføje mere end én scripthandling for at installere flere komponenter på klyngen. Når du har tilføjet scriptene, skal du markere afkrydsningsfeltet for at starte crating klyngen.

Du kan også bruge scriptet til at installere R på HDInsight ved hjælp af Azure PowerShell eller HDInsight .NET SDK. Vejledning til disse procedurer får senere i denne artikel.

## <a name="run-r-scripts"></a>Køre R scripts
I dette afsnit beskrives, hvordan du kører en R-script på Hadoop-klynge med HDInsight.

1. **Opret forbindelse til klyngen Fjernskrivebord**: fra feltet Portal, aktivere Fjernskrivebord for den klynge, du har oprettet med R, der er installeret, og derefter oprette forbindelse til klyngen. Du kan finde instruktioner, [Opret forbindelse til HDInsight klynger ved hjælp af RDP](hdinsight-administer-use-management-portal.md#rdp).

2. **Åbne konsollen R**: feltet R installation placerer et link til R-konsollen på skrivebordet for noden hoved. Klik på den for at åbne konsollen R.

3. **Kør scriptet R**: feltet R script kan køres direkte fra konsollen R ved at indsætte det, at markere den og trykke på ENTER. Her er et simpelt eksempel-script, som genererer tal 1 til 100 og multiplicerer dem med 2.

        library(rmr2)
        library(rhdfs)
        ints = to.dfs(1:100)
        calc = mapreduce(input = ints, map = function(k, v) cbind(v, 2*v))
        from.dfs(calc)

De første to linjer ringe RHadoop-biblioteker, der installeres med R. Den sidste linje udskriver resultaterne til konsollen. Output skal se sådan ud:

    [1,]  1 2
    [2,]  2 4
    .
    .
    .
    [98,]  98 196
    [99,]  99 198
    [100,] 100 200


## <a name="install-r-using-aure-powershell"></a>Installere R ved hjælp af Aure PowerShell

Se [tilpasse HDInsight klynger ved hjælp af Script handlingen](hdinsight-hadoop-customize-cluster.md#call_scripts_using_powershell).  Eksemplet viser, hvordan du installerer knallertmotor med Azure PowerShell. Du har brug at tilpasse scriptet for at bruge [https://hdiconfigactions.blob.core.windows.net/rconfigactionv02/r-installer-v02.ps1](https://hdiconfigactions.blob.core.windows.net/rconfigactionv02/r-installer-v02.ps1).

## <a name="install-r-using-net-sdk"></a>Installere R ved hjælp af .NET SDK

Se [tilpasse HDInsight klynger ved hjælp af Script handlingen](hdinsight-hadoop-customize-cluster.md#call_scripts_using_azure_powershell). Eksemplet viser, hvordan du installerer knallertmotor ved hjælp af .NET SDK. Du har brug at tilpasse scriptet for at bruge [https://hdiconfigactions.blob.core.windows.net/rconfigactionv02/r-installer-v02.ps1](https://hdiconfigactions.blob.core.windows.net/rconfigactionv02/r-installer-v02.ps11).


## <a name="see-also"></a>Se også

- [Installere og bruge R på HDinsight Hadoop klynger (Linux)](hdinsight-hadoop-r-scripts-linux.md)
- [Oprette Hadoop klynger i HDInsight](hdinsight-provision-clusters.md): generelle oplysninger om oprettelse af HDInsight klynger
- [Tilpasse HDInsight klynge ved hjælp af scripthandling][hdinsight-cluster-customize]: generelle oplysninger om tilpasning af HDInsight klynger ved hjælp af scripthandling
- [Udvikle scripthandling scripts til HDInsight](hdinsight-hadoop-script-actions.md)
- [Installere og bruge knallertmotor på HDInsight klynger][hdinsight-install-spark]: scripthandling eksempel om installation af knallertmotor
- [Installere Giraph på HDInsight klynger](hdinsight-hadoop-giraph-install.md): scripthandling eksempel om installation af Giraph
- [Installere Solr på HDInsight klynger](hdinsight-hadoop-solr-install-linux.md): scripthandling eksempel om installation af Solr.

[powershell-install-configure]: powershell-install-configure.md
[hdinsight-provision]: ../hdinsight-provision-clusters/
[hdinsight-cluster-customize]: hdinsight-hadoop-customize-cluster-linux.md
[hdinsight-install-spark]: hdinsight-apache-spark-jupyter-spark-sql.md
