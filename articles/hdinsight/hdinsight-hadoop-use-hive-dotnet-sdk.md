<properties
    pageTitle="Køre Hive forespørgsler ved hjælp af HDInsight .NET SDK | Microsoft Azure"
    description="Få mere at vide, hvordan du sender Hadoop-job til Azure HDInsight Hadoop ved hjælp af HDInsight .NET SDK."
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
   ms.date="09/14/2016"
    ms.author="jgao"/>

# <a name="run-hive-queries-using-hdinsight-net-sdk"></a>Køre Hive forespørgsler ved hjælp af HDInsight .NET SDK

[AZURE.INCLUDE [hive-selector](../../includes/hdinsight-selector-use-hive.md)]


Få mere at vide, hvordan du sender Hive forespørgsler med HDInsight .NET SDK.

> [AZURE.NOTE] Trinnene i denne artikel skal udføres fra en Windows-klient. Oplysninger om brug af en Linux, OS X eller Unix-klienten til at arbejde med Hive, bruge tabulatorvælgeren vises øverst i artiklen.

##<a name="prerequisites"></a>Forudsætninger

Før du begynder i denne artikel, skal du have følgende:

- **A Hadoop klynge i HDInsight**. Se [Introduktion til brug af Linux-baserede Hadoop i HDInsight](hdinsight-use-sqoop.md#create-cluster-and-sql-database).
- **Visual Studio 2012-2013-2015**.

##<a name="submit-hive-queries-using-hdinsight-net-sdk"></a>Sende Hive forespørgsler med HDInsight .NET SDK

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

                    SubmitHiveJob();

                    System.Console.WriteLine("Press ENTER to continue ...");
                    System.Console.ReadLine();
                }

                private static void SubmitHiveJob()
                {
                    Dictionary<string, string> defines = new Dictionary<string, string> { { "hive.execution.engine", "ravi" }, { "hive.exec.reducers.max", "1" } };
                    List<string> args = new List<string> { { "argA" }, { "argB" } };
                    var parameters = new HiveJobSubmissionParameters
                    {
                        Query = "SHOW TABLES",
                        Defines = defines,
                        Arguments = args
                    };

                    System.Console.WriteLine("Submitting the Hive job to the cluster...");
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

* [Introduktion til Azure HDInsight][hdinsight-get-started]
* [Oprette Hadoop klynger i HDInsight][hdinsight-provision]
* [Administrere Hadoop klynger i HDInsight ved hjælp af portalen Azure](hdinsight-administer-use-management-portal.md)
* [HDInsight .NET SDK reference](https://msdn.microsoft.com/library/mt271028.aspx)
* [Brug gris med HDInsight](hdinsight-use-pig.md)
* [Bruge Sqoop med HDInsight](hdinsight-use-sqoop-mac-linux.md)
* [Oprette ikke-interaktiv godkendelse .NET HDInsight programmer](hdinsight-create-non-interactive-authentication-dotnet-applications.md)


[hdinsight-provision]: hdinsight-provision-clusters.md
[hdinsight-get-started]: hdinsight-hadoop-linux-tutorial-get-started.md


