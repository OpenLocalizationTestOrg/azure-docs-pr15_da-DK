<properties
    pageTitle="Installere og bruge Giraph på Hadoop klynger i HDInsight | Microsoft Azure"
    description="Lær, hvordan du tilpasser HDInsight klynge med Giraph, og hvordan du bruger Giraph."
    services="hdinsight"
    documentationCenter=""
    authors="nitinme"
    manager="jhubbard"
    editor="cgronlun"
    tags="azure-portal"/>

<tags
    ms.service="hdinsight"
    ms.workload="big-data"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="02/05/2016"
    ms.author="nitinme"/>

# <a name="install-and-use-giraph-in-hdinsight"></a>Installere og bruge Giraph i HDInsight


Lær, hvordan du tilpasser Windows-baseret HDInsight klynge med Giraph ved hjælp af Script handlingen, og hvordan du kan bruge Giraph til at behandle store grafer. Du kan finde oplysninger om brug af Giraph med en Linux-baserede klynge [Installere Giraph på HDInsight Hadoop-klynger (Linux)](hdinsight-hadoop-giraph-install-linux.md).
 
Du kan installere Giraph på alle typer klynge (Hadoop Storm HBase, og knallertmotor) på Azure HDInsight ved hjælp af *Scripthandling*. Et eksempel på script til at installere Giraph på en HDInsight klynge er tilgængelig fra en skrivebeskyttet Azure lagerplads blob på [https://hdiconfigactions.blob.core.windows.net/giraphconfigactionv01/giraph-installer-v01.ps1](https://hdiconfigactions.blob.core.windows.net/giraphconfigactionv01/giraph-installer-v01.ps1). Eksempelscriptet fungerer kun med HDInsight klynge version 3.1. Du kan finde flere oplysninger om HDInsight klynge versioner, [HDInsight klynge versioner](hdinsight-component-versioning.md).

**Relaterede artikler**

- [Installere Giraph på HDInsight Hadoop klynger (Linux)](hdinsight-hadoop-giraph-install-linux.md)
- [Oprette Hadoop klynger i HDInsight](hdinsight-provision-clusters.md): generelle oplysninger om oprettelse af HDInsight klynger.
- [Tilpasse HDInsight klynge ved hjælp af scripthandling][hdinsight-cluster-customize]: generelle oplysninger om tilpasning af HDInsight klynger ved hjælp af Script handlingen.
- [Udvikle scripthandling scripts til HDInsight](hdinsight-hadoop-script-actions.md).

## <a name="what-is-giraph"></a>Hvad er Giraph?

<a href="http://giraph.apache.org/" target="_blank">Apache Giraph</a> kan du udføre graph behandling ved hjælp af Hadoop, og de kan bruges med Azure HDInsight. Grafer modeller relationer mellem objekter, som forbindelserne mellem routere på et stort netværk som internettet eller relationer mellem personer på sociale netværk (også kaldet en sociale graf). Graph behandling gør det muligt at årsag om relationerne mellem objekter i et diagram, f.eks.:

- Identificere potentielle venner, der er baseret på din aktuelle relationer.
- Identificere den korteste vej mellem to computere i et netværk.
- Beregning af siden rangen for websider.


## <a name="install-giraph-using-portal"></a>Installere Giraph ved hjælp af portalen

1. Begynde at oprette en klynge ved hjælp af indstillingen **Opret brugerdefineret** , som beskrevet på [oprette Hadoop klynger i HDInsight](hdinsight-provision-clusters.md#portal).
2. Klik på **Tilføj scripthandling** for at vise detaljer om handlingen script på siden **Scripthandlinger** i guiden, som vist nedenfor:

    ![Brug scripthandling til at tilpasse en klynge] (./media/hdinsight-hadoop-giraph-install/hdi-script-action-giraph.png "Brug scripthandling til at tilpasse en klynge")

    <table border='1'>
        <tr><th>Egenskaben</th><th>Værdi</th></tr>
        <tr><td>Navn</td>
            <td>Angiv et navn for handlingen script. For eksempel <b>Installere Giraph</b>.</td></tr>
        <tr><td>Script URI</td>
            <td>Angiv URI Uniform Resource Identifier () til det script, der er aktiveret for at tilpasse klyngen. Eksempel: <i>https://hdiconfigactions.blob.core.windows.net/giraphconfigactionv01/giraph-installer-v01.ps1</i></td></tr>
        <tr><td>Node af Type</td>
            <td>Angiv noderne, hvor tilpasning scriptet skal køres. Du kan vælge <b>alle noder</b>, <b>hoved noder kun</b>eller <b>arbejder noder kun</b>.
        <tr><td>Parametre</td>
            <td>Angiv parametrene, hvis det er nødvendigt ved scriptet. Scriptet til at installere Giraph kræver ikke eventuelle parametre, så du kan lade feltet være tomt.</td></tr>
    </table>

    Du kan tilføje mere end én scripthandling for at installere flere komponenter på klyngen. Når du har tilføjet scriptene, skal du klikke på markeringen for at starte oprettelsen af klynge.

## <a name="use-giraph"></a>Brug Giraph

Vi bruger eksemplet SimpleShortestPathsComputation til at vise de grundlæggende <a href = "http://people.apache.org/~edwardyoon/documents/pregel.pdf">Pregel</a> implementering til at finde den hurtigste vej til objekter i et diagram. Brug følgende trin til at overføre eksempeldataene og eksempel glas, køre et job ved hjælp af eksemplet SimpleShortestPathsComputation, og derefter få vist resultaterne.

1. Overføre et eksempel på datafil til Azure Blob-lager. Oprette en ny fil med navnet **tiny_graph.txt**på computeren lokale. Det skal indeholde følgende linjer:

        [0,0,[[1,1],[3,3]]]
        [1,0,[[0,1],[2,2],[3,1]]]
        [2,0,[[1,2],[4,4]]]
        [3,0,[[0,3],[1,1],[4,4]]]
        [4,0,[[3,4],[2,4]]]

    Overføre filen tiny_graph.txt til den primære lagerplads for dit HDInsight klynge. For vejledning i at overføre data, skal du se [overføre data til Hadoop-job i HDInsight](hdinsight-upload-data.md).

    Disse data i denne artikel beskrives en relation mellem objekter i en direkte graf ved hjælp af formatet [kilde\_-id, kilde\_værdi, [[dest\_id], [kant\_værdi];...]]. Hver linje repræsenterer en relation mellem en **kilde\_id** objekt og en eller flere **dest\_id** objekter. Den **kant\_værdi** (eller vægt) kan betragtes som en styrke eller afstand af forbindelsen mellem **source_id** og **dest\_id**.

    Tegnet, og brug af værdien (eller vægt) som afstanden mellem objekter, ovennævnte data kan se sådan ud:

    ![tiny_graph.txt tegnet som cirkler med skiftende afstanden mellem tekstlinjer](./media/hdinsight-hadoop-giraph-install/giraph-graph.png)



4. Køre eksemplet SimpleShortestPathsComputation. Brug følgende Azure PowerShell-cmdletter til at køre eksemplet ved hjælp af tiny_graph.txt filen som input. 

    [AZURE.INCLUDE [upgrade-powershell](../../includes/hdinsight-use-latest-powershell.md)]

        $clusterName = "clustername"
        # Giraph examples jar
        $jarFile = "wasbs:///example/jars/giraph-examples.jar"
        # Arguments for this job
        $jobArguments = "org.apache.giraph.examples.SimpleShortestPathsComputation",
                        "-ca", "mapred.job.tracker=headnodehost:9010",
                        "-vif", "org.apache.giraph.io.formats.JsonLongDoubleFloatDoubleVertexInputFormat",
                        "-vip", "wasbs:///example/data/tiny_graph.txt",
                        "-vof", "org.apache.giraph.io.formats.IdWithValueTextOutputFormat",
                        "-op",  "wasbs:///example/output/shortestpaths",
                        "-w", "2"
        # Create the definition
        $jobDefinition = New-AzureHDInsightMapReduceJobDefinition
          -JarFile $jarFile
          -ClassName "org.apache.giraph.GiraphRunner"
          -Arguments $jobArguments

        # Run the job, write output to the Azure PowerShell window
        $job = Start-AzureHDInsightJob -Cluster $clusterName -JobDefinition $jobDefinition
        Write-Host "Wait for the job to complete ..." -ForegroundColor Green
        Wait-AzureHDInsightJob -Job $job
        Write-Host "STDERR"
        Get-AzureHDInsightJobOutput -Cluster $clusterName -JobId $job.JobId -StandardError
        Write-Host "Display the standard output ..." -ForegroundColor Green
        Get-AzureHDInsightJobOutput -Cluster $clusterName -JobId $job.JobId -StandardOutput

    I ovennævnte eksempel skal du erstatte **clustername** med navnet på din HDInsight klynge, der indeholder Giraph, der er installeret.

5. Få vist resultaterne. Når jobbet er afsluttet, resultaterne skal lagres på to outputfiler i den __wasbs: / / / eksempel/ud/shotestpaths__ mappe. Filerne, der kaldes __en del-m-00001__ og __del-m-00002__. Udfør følgende trin for at hente og få vist output:

        $subscriptionName = "<SubscriptionName>"       # Azure subscription name
        $storageAccountName = "<StorageAccountName>"   # Azure Storage account name
        $containerName = "<ContainerName>"             # Blob storage container name

        # Select the current subscription
        Select-AzureSubscription $subscriptionName

        # Create the Storage account context object
        $storageAccountKey = Get-AzureStorageKey $storageAccountName | %{ $_.Primary }
        $storageContext = New-AzureStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storageAccountKey

        # Download the job output to the workstation
        Get-AzureStorageBlobContent -Container $containerName -Blob example/output/shortestpaths/part-m-00001 -Context $storageContext -Force
        Get-AzureStorageBlobContent -Container $containerName -Blob example/output/shortestpaths/part-m-00002 -Context $storageContext -Force

    Dette opretter mappestrukturen __output-eksempel/shortestpaths__ i den aktuelle mappe på computeren, og hente to outputfilerne til den nye placering.

    Brug cmdlet'en __kat__ til at vise indholdet af filerne:

        Cat example/output/shortestpaths/part*

    Output skal se ud som følger:


        0   1.0
        4   5.0
        2   2.0
        1   0.0
        3   1.0

    SimpleShortestPathComputation eksempel er svært indkodet skal starte med objekt ID 1, og find den hurtigste vej til andre objekter. Så output skal læses som `destination_id distance`, hvor afstanden er værdien (eller vægt) af kanterne kørte kilometer mellem objekt ID 1 og mål-ID.

    Visualisere, kan du kontrollere resultaterne ved rejse kortest stier mellem ID 1 og alle andre objekter. Bemærk, at den hurtigste vej til ID 1 og 4-ID 5. Dette er den samlede afstand mellem <span style="color:orange">ID 1 og 3</span>og derefter <span style="color:red">ID 3 og 4</span>.

    ![Tegning af objekter som cirkler med kortest stier, der er tegnet mellem](./media/hdinsight-hadoop-giraph-install/giraph-graph-out.png)

## <a name="install-giraph-using-aure-powershell"></a>Installere Giraph ved hjælp af Aure PowerShell

Se [tilpasse HDInsight klynger ved hjælp af Script handlingen](hdinsight-hadoop-customize-cluster.md#call_scripts_using_powershell).  Eksemplet viser, hvordan du installerer knallertmotor med Azure PowerShell. Du har brug at tilpasse scriptet for at bruge [https://hdiconfigactions.blob.core.windows.net/giraphconfigactionv01/giraph-installer-v01.ps1](https://hdiconfigactions.blob.core.windows.net/giraphconfigactionv01/giraph-installer-v01.ps1).

## <a name="install-giraph-using-net-sdk"></a>Installere Giraph ved hjælp af .NET SDK

Se [tilpasse HDInsight klynger ved hjælp af Script handlingen](hdinsight-hadoop-customize-cluster.md#call_scripts_using_azure_powershell). Eksemplet viser, hvordan du installerer knallertmotor ved hjælp af .NET SDK. Du har brug at tilpasse scriptet for at bruge [https://hdiconfigactions.blob.core.windows.net/giraphconfigactionv01/giraph-installer-v01.ps1](https://hdiconfigactions.blob.core.windows.net/giraphconfigactionv01/giraph-installer-v01.ps1).


## <a name="see-also"></a>Se også

- [Installere Giraph på HDInsight Hadoop klynger (Linux)](hdinsight-hadoop-giraph-install-linux.md)
- [Oprette Hadoop klynger i HDInsight](hdinsight-provision-clusters.md): generelle oplysninger om oprettelse af HDInsight klynger.
- [Tilpasse HDInsight klynge ved hjælp af scripthandling][hdinsight-cluster-customize]: generelle oplysninger om tilpasning af HDInsight klynger ved hjælp af Script handlingen.
- [Udvikle scripthandling scripts til HDInsight](hdinsight-hadoop-script-actions.md).
- [Installere og bruge knallertmotor på HDInsight klynger][hdinsight-install-spark]: scripthandling eksempel om installation af knallertmotor.
- [Installere R på HDInsight klynger][hdinsight-install-r]: scripthandling eksempel om installation af R.
- [Installere Solr på HDInsight klynger](hdinsight-hadoop-solr-install.md): scripthandling eksempel om installation af Solr.



[tools]: https://github.com/Blackmist/hdinsight-tools
[aps]: http://azure.microsoft.com/documentation/articles/install-configure-powershell/

[powershell-install]: ../powershell-install-configure.md
[hdinsight-provision]: hdinsight-provision-clusters.md
[hdinsight-install-r]: hdinsight-hadoop-r-scripts.md
[hdinsight-install-spark]: hdinsight-hadoop-spark-install.md
[hdinsight-cluster-customize]: hdinsight-hadoop-customize-cluster.md
