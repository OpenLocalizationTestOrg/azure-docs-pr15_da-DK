<properties
    pageTitle="Sende MapReduce job ved hjælp af HDInsight .NET SDK | Microsoft Azure"
    description="Få mere at vide, hvordan du sender MapReduce job til Azure HDInsight Hadoop ved hjælp af HDInsight .NET SDK."
    editor="cgronlun"
    manager="jhubbard"
    services="hdinsight"
    documentationCenter=""
    tags="azure-portal"
    authors="mumian"/>

<tags
    ms.service="hdinsight"
    ms.workload="big-data"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
   ms.date="10/28/2016"
    ms.author="jgao"/>

# <a name="run-mapreduce-jobs-using-hdinsight-net-sdk"></a>Køre MapReduce job ved hjælp af HDInsight .NET SDK

[AZURE.INCLUDE [mapreduce-selector](../../includes/hdinsight-selector-use-mapreduce.md)]

Få mere at vide, hvordan du sender MapReduce job ved hjælp af HDInsight .NET SDK. HDInsight klynger følger med en glas fil med nogle eksempler på MapReduce. Filen glas er */example/jars/hadoop-mapreduce-examples.jar*.  En af eksemplerne er *wordcount*. Du udvikle et C# console-program til at sende en wordcount sag.  Jobbet læser */example/data/gutenberg/davinci.txt* filen og viser resultaterne */example/data/davinciwordcount*.  Hvis du vil køre programmet, skal du rydde op i outputmappen.

> [AZURE.NOTE] Trinnene i denne artikel skal udføres fra en Windows-klient. Oplysninger om brug af en Linux, OS X eller Unix-klienten til at arbejde med Hive, bruge tabulatorvælgeren vises øverst i artiklen.

##<a name="prerequisites"></a>Forudsætninger

Før du begynder i denne artikel, skal du have følgende:

- **A Hadoop klynge i HDInsight**. Se [Introduktion til brug af Linux-baserede Hadoop i HDInsight](hdinsight-use-sqoop.md#create-cluster-and-sql-database).
- **Visual Studio 2012-2013-2015**.

##<a name="submit-mapreduce-jobs-using-hdinsight-net-sdk"></a>Sende MapReduce job ved hjælp af HDInsight .NET SDK

HDInsight .NET SDK indeholder .NET klientbiblioteker, som gør det nemmere at arbejde med HDInsight klynger fra .NET. 

**Sende job**

1. Oprette et C# console program i Visual Studio.
2. Kør følgende kommando fra konsollen Nuget Package Manager.

        Install-Package Microsoft.Azure.Management.HDInsight.Job

2. Brug følgende kode:

        using System.Collections.Generic;
        using System.IO;
        using System.Text;
        using System.Threading;
        using Microsoft.Azure.Management.HDInsight.Job;
        using Microsoft.Azure.Management.HDInsight.Job.Models;
        using Hyak.Common;

        namespace SubmitHDInsightJobDotNet
        {
            class Program
            {
                private static HDInsightJobManagementClient _hdiJobManagementClient;

                private const string ExistingClusterName = "<Your HDInsight Cluster Name>";
                private const string ExistingClusterUri = ExistingClusterName + ".azurehdinsight.net";
                private const string ExistingClusterUsername = "<Cluster Username>";
                private const string ExistingClusterPassword = "<Cluster User Password>";

                private const string DefaultStorageAccountName = "<Default Storage Account Name>";
                private const string DefaultStorageAccountKey = "<Default Storage Account Key>";
                private const string DefaultStorageContainerName = "<Default Blob Container Name>";

                static void Main(string[] args)
                {
                    System.Console.WriteLine("The application is running ...");

                    var clusterCredentials = new BasicAuthenticationCloudCredentials { Username = ExistingClusterUsername, Password = ExistingClusterPassword };
                    _hdiJobManagementClient = new HDInsightJobManagementClient(ExistingClusterUri, clusterCredentials);

                    SubmitMRJob();

                    System.Console.WriteLine("Press ENTER to continue ...");
                    System.Console.ReadLine();
                }

                private static void SubmitHiveJob()
                {
                    List<string> args = new List<string> { { "/example/data/gutenberg/davinci.txt" }, { "/example/data/davinciwordcount" } };

                    var paras = new MapReduceJobSubmissionParameters
                    {
                        //Content = "wordcount  ",
                        JarFile = @"/example/jars/hadoop-mapreduce-examples.jar",
                        JarClass = "wordcount",
                        Arguments = args
                    };

                    System.Console.WriteLine("Submitting the MR job to the cluster...");
                    var jobResponse = _hdiJobManagementClient.JobManagement.SubmitHiveJob(parameters);
                    var jobId = jobResponse.JobSubmissionJsonResponse.Id;
                    System.Console.WriteLine("Response status code is " + jobResponse.StatusCode);
                    System.Console.WriteLine("JobId is " + jobId);

                    System.Console.WriteLine("Waiting for the job completion ...");

                    // Wait for job completion
                    var jobDetail = _hdiJobManagementClient.JobManagement.GetJob(jobId).JobDetail;
                    while (!jobDetail.Status.JobComplete)
                    {
                        Thread.Sleep(1000);
                        jobDetail = _hdiJobManagementClient.JobManagement.GetJob(jobId).JobDetail;
                    }

                    // Get job output
                    var storageAccess = new AzureStorageAccess(DefaultStorageAccountName, DefaultStorageAccountKey,
                        DefaultStorageContainerName);
                    var output = (jobDetail.ExitValue == 0)
                        ? _hdiJobManagementClient.JobManagement.GetJobOutput(jobId, storageAccess) // fetch stdout output in case of success
                        : _hdiJobManagementClient.JobManagement.GetJobErrorLogs(jobId, storageAccess); // fetch stderr output in case of failure

                    System.Console.WriteLine("Job output is: ");

                    using (var reader = new StreamReader(output, Encoding.UTF8))
                    {
                        string value = reader.ReadToEnd();
                        System.Console.WriteLine(value);
                    }
                }
            }
        }

5. Tryk på **F5** for at køre programmet.


## <a name="next-steps"></a>Næste trin

Du har lært flere måder at oprette en HDInsight klynge i denne artikel. Hvis du vil vide mere, skal du se følgende artikler:

- For at oprette en klynge og sende et Hive-job, kan du se [Introduktion til Azure HDInsight](hdinsight-hadoop-linux-tutorial-get-started.md).
- Se [oprette Linux-baserede Hadoop klynger i HDInsight](hdinsight-hadoop-provision-linux-clusters.md)til oprettelse af HDInsight klynger.
- Se [administrere Hadoop klynger i HDInsight](hdinsight-administer-use-management-portal.md)til administration af HDInsight klynger.
- Se [reference til HDInsight .NET SDK](https://msdn.microsoft.com/library/mt271028.aspx)til at blive fortrolig HDInsight .NET SDK.
- For ikke-interaktiv godkende til Azure, skal du se [oprette ikke-interaktiv godkendelse .NET HDInsight programmer](hdinsight-create-non-interactive-authentication-dotnet-applications.md).




