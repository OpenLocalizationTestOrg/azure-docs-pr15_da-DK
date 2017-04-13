<properties
    pageTitle="Fejlfinding og analysere Hadoop-tjenester med heap gemmer | Microsoft Azure"
    description="Automatisk indsamle heap gemmer for Hadoop-tjenester og placere i kontoen Azure Blob-lager til fejlfinding og analyse."
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


# <a name="collect-heap-dumps-in-blob-storage-to-debug-and-analyze-hadoop-services"></a>Indsamle heap gemmer i Blob-lager til at udføre fejlfinding og analysere Hadoop-tjenester

[AZURE.INCLUDE [heapdump-selector](../../includes/hdinsight-selector-heap-dump.md)]

Heap gemmer indeholder et øjebliksbillede af programmets hukommelse, herunder værdierne af variabler på det tidspunkt dumpet blev oprettet. Så de er meget nyttig til diagnosticering problemer, der opstår under kørsel. Heap gemmer kan automatisk indsamles for Hadoop-tjenester og placeret i Azure Blob storage kontoen for en bruger under HDInsightHeapDumps /. 

Samling af heap gemmer for forskellige tjenester skal være aktiveret for tjenester på individuelle klynger. Standard for denne funktion er at være slået fra for en klynge. Disse heap gemmer kan være store, så det anbefales at overvåge kontoen Blob storage hvor de er gemmes, når samlingen er blevet aktiveret.

> [AZURE.NOTE] Oplysninger i denne artikel gælder kun for Windows-baseret HDInsight. Finde oplysninger om Linux-baserede HDInsight, se [aktivere heap gemmer for Hadoop-tjenester på Linux-baserede HDInsight](hdinsight-hadoop-collect-debug-heap-dump-linux.md)

## <a name="eligible-services-for-heap-dumps"></a>Gyldigt tjenester til heap gemmer

Du kan aktivere heap gemmer for følgende tjenester:

*  **hcatalog** - tempelton
*  **hive** - hiveserver2, metastore, derbyserver
*  **mapreduce** - jobhistoryserver
*  **garn** - Ressourcestyring, nodemanager, timelineserver
*  **hdfs** - datanode, secondarynamenode, namenode

## <a name="configuration-elements-that-enable-heap-dumps"></a>Konfiguration af elementer, der aktiverer heap gemmer

Hvis du vil slå heap gemmer til en tjeneste, skal du angive de korrekte konfiguration elementer i sektionen for den pågældende tjeneste, som er angivet af **navn_på_tjeneste**.

    "javaargs.<service_name>.XX:+HeapDumpOnOutOfMemoryError" = "-XX:+HeapDumpOnOutOfMemoryError",
    "javaargs.<service_name>.XX:HeapDumpPath" = "-XX:HeapDumpPath=c:\Dumps\<service_name>_%date:~4,2%%date:~7,2%%date:~10,2%%time:~0,2%%time:~3,2%%time:~6,2%.hprof"

Værdien af **navn_på_tjeneste** kan være en af de tjenester, der er nævnt ovenfor: tempelton, hiveserver2, metastore, derbyserver, jobhistoryserver, Ressourcestyring, nodemanager, timelineserver, datanode, secondarynamenode, eller namenode.

## <a name="enable-using-azure-powershell"></a>Aktivere ved hjælp af Azure PowerShell

For eksempel for at aktivere heap gemmer ved hjælp af Azure PowerShell til jobhistoryserver skal gøre du følgende:

[AZURE.INCLUDE [upgrade-powershell](../../includes/hdinsight-use-latest-powershell.md)]

    $MapRedConfigValues = new-object 'Microsoft.WindowsAzure.Management.HDInsight.Cmdlet.DataObjects.AzureHDInsightMapReduceConfiguration'

    $MapRedConfigValues.Configuration = @{ "javaargs.jobhistoryserver.XX:+HeapDumpOnOutOfMemoryError"="-XX:+HeapDumpOnOutOfMemoryError" ; "javaargs.jobhistoryserver.XX:HeapDumpPath" = "-XX:HeapDumpPath=c:\\Dumps\\jobhistoryserver_%date:~4,2%_%date:~7,2%_%date:~10,2%_%time:~0,2%_%time:~3,2%_%time:~6,2%.hprof" }

## <a name="enable-using-net-sdk"></a>Aktivere ved hjælp af .NET SDK

For eksempel for at aktivere heap gemmer ved hjælp af Azure HDInsight .NET SDK til jobhistoryserver skal gøre du følgende:

    clusterInfo.MapReduceConfiguration.ConfigurationCollection.Add(new KeyValuePair<string, string>("javaargs.jobhistoryserver.XX:+HeapDumpOnOutOfMemoryError", "-XX:+HeapDumpOnOutOfMemoryError"));

    clusterInfo.MapReduceConfiguration.ConfigurationCollection.Add(new KeyValuePair<string, string>("javaargs.jobhistoryserver.XX:HeapDumpPath", "-XX:HeapDumpPath=c:\\Dumps\\jobhistoryserver_%date:~4,2%_%date:~7,2%_%date:~10,2%_%time:~0,2%_%time:~3,2%_%time:~6,2%.hprof"));
