<properties
    pageTitle="Brug Hadoop Sqoop i HDInsight | Microsoft Azure"
    description="Lær, hvordan du bruger HDInsight .NET SDK til at køre Sqoop importere og eksportere mellem en Hadoop-klynge og Azure SQL-database."
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

#<a name="run-sqoop-jobs-using-net-sdk-for-hadoop-in-hdinsight"></a>Køre Sqoop job ved hjælp af .NET SDK til Hadoop i HDInsight

[AZURE.INCLUDE [sqoop-selector](../../includes/hdinsight-selector-use-sqoop.md)]

Lær, hvordan du bruger HDInsight .NET SDK til at køre Sqoop job i HDInsight til at importere og eksportere mellem HDInsight klynge og Azure SQL-database eller SQL Server-database.

> [AZURE.NOTE] Trinnene i denne artikel kan bruges med enten en Windows-baseret eller Linux-baserede HDInsight klynge; disse trin fungerer kun fra en Windows-klient. Brug tabulatorvælgeren øverst i denne artikel til at vælge andre metoder.

###<a name="prerequisites"></a>Forudsætninger

Inden du starter selvstudiet, skal du have følgende:

- **A Hadoop klynge i HDInsight**. Se [oprette klynge og SQL-database](hdinsight-use-sqoop.md#create-cluster-and-sql-database).

## <a name="run-sqoop-using-net-sdk"></a>Køre Sqoop ved hjælp af .NET SDK

HDInsight .NET SDK indeholder .NET klientbiblioteker, som gør det nemmere at arbejde med HDInsight klynger fra .NET. I dette afsnit, skal oprette du en C# console program tilladelse til at eksportere hivesampletable til tabellen SQL-Database, du oprettede tidligere i denne selvstudier.

**Sende et Sqoop-job**

1. Oprette et C# console program i Visual Studio.
2. Kør følgende kommando for Nuget at importere pakken fra konsollen Visual Studio Package Manager.

        Install-Package Microsoft.Azure.Management.HDInsight.Job
        
3. Du kan bruge følgende kode i filen Program.cs:

        using System.Collections.Generic;
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
        
                static void Main(string[] args)
                {
                    System.Console.WriteLine("The application is running ...");
        
                    var clusterCredentials = new BasicAuthenticationCloudCredentials { Username = ExistingClusterUsername, Password = ExistingClusterPassword };
                    _hdiJobManagementClient = new HDInsightJobManagementClient(ExistingClusterUri, clusterCredentials);
        
                    SubmitSqoopJob();
        
                    System.Console.WriteLine("Press ENTER to continue ...");
                    System.Console.ReadLine();
                }
        
                private static void SubmitSqoopJob()
                {
                    var sqlDatabaseServerName = "<SQLDatabaseServerName>";
                    var sqlDatabaseLogin = "<SQLDatabaseLogin>";
                    var sqlDatabaseLoginPassword = "<SQLDatabaseLoginPassword>";
                    var sqlDatabaseDatabaseName = "<DatabaseName>";
        
                    var tableName = "<TableName>";
                    var exportDir = "/tutorials/usesqoop/data";
        
                    // Connection string for using Azure SQL Database.
                    // Comment if using SQL Server
                    var connectionString = "jdbc:sqlserver://" + sqlDatabaseServerName + ".database.windows.net;user=" + sqlDatabaseLogin + "@" + sqlDatabaseServerName + ";password=" + sqlDatabaseLoginPassword + ";database=" + sqlDatabaseDatabaseName;
                    // Connection string for using SQL Server.
                    // Uncomment if using SQL Server
                    //var connectionString = "jdbc:sqlserver://" + sqlDatabaseServerName + ";user=" + sqlDatabaseLogin + ";password=" + sqlDatabaseLoginPassword + ";database=" + sqlDatabaseDatabaseName;
        
                    var parameters = new SqoopJobSubmissionParameters
                    {
                        Files = new List<string> { "/user/oozie/share/lib/sqoop/sqljdbc41.jar" }, // This line is required for Linux-based cluster.
                        Command = "export --connect " + connectionString + " --table " + tableName + "_mobile --export-dir " + exportDir + "_mobile --fields-terminated-by \\t -m 1"
                    };
        
                    System.Console.WriteLine("Submitting the Sqoop job to the cluster...");
                    var response = _hdiJobManagementClient.JobManagement.SubmitSqoopJob(parameters);
                    System.Console.WriteLine("Validating that the response is as expected...");
                    System.Console.WriteLine("Response status code is " + response.StatusCode);
                    System.Console.WriteLine("Validating the response object...");
                    System.Console.WriteLine("JobId is " + response.JobSubmissionJsonResponse.Id);
                }
            }
        }
        
4. Tryk på **F5** for at køre programmet. 

##<a name="limitations"></a>Begrænsninger

* Flere eksport - med Linux-baserede HDInsight, Sqoop forbindelsen bruges til at eksportere data til Microsoft SQL Server eller Azure SQL-Database understøtter i øjeblikket ikke flere indsættes.

* "Samling"-med Linux-baserede HDInsight, når du bruger den `-batch` skifte, når du udfører indsættes, Sqoop udfører flere indsættes i stedet for "samling" Indsæt handlinger.

##<a name="next-steps"></a>Næste trin

Nu har du lært, hvordan du bruger Sqoop. Hvis du vil vide mere, skal du se:

- [Brug Oozie med HDInsight](hdinsight-use-oozie.md): Brug Sqoop handling i en arbejdsproces for Oozie.
- [Analysér flight forsinkelse data ved hjælp af HDInsight](hdinsight-analyze-flight-delay-data.md): Brug Hive til at analysere flight forsinke data, og brug derefter Sqoop til at eksportere data til en Azure SQL-database.
- [Overføre data til HDInsight](hdinsight-upload-data.md): finde andre metoder til at overføre data til HDInsight/Azure Blob-lager.


