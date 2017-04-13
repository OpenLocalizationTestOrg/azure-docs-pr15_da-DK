<properties
   pageTitle="Bruge Hadoop gris med .NET i HDInsight | Microsoft Azure"
   description="Lær, hvordan du bruger .NET SDK for Hadoop til at sende gris job til Hadoop på HDInsight."
   services="hdinsight"
   documentationCenter=".net"
   authors="Blackmist"
   manager="jhubbard"
   editor="cgronlun"
   tags="azure-portal"/>

<tags
   ms.service="hdinsight"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data"
   ms.date="10/17/2016"
   ms.author="larryfr"/>

#<a name="run-pig-jobs-using-the-net-sdk-for-hadoop-in-hdinsight"></a>Køre gris job ved hjælp af .NET SDK for Hadoop i HDInsight

[AZURE.INCLUDE [pig-selector](../../includes/hdinsight-selector-use-pig.md)]

Dette dokument indeholder et eksempel på ved hjælp af .NET SDK for Hadoop til at sende gris job til et Hadoop på HDInsight klynge.

HDInsight .NET SDK indeholder .NET klientbiblioteker, der gør det nemmere at arbejde med HDInsight klynger fra .NET. Gris kan du oprette MapReduce handlinger ved at udforme en række datatransformationer. Du lære, hvordan du bruger et grundlæggende C#-program til at sende et gris job til en HDInsight klynge.

## <a name="prerequisites"></a>Forudsætninger

For at fuldføre trinnene i denne artikel, skal du følgende.

* En Azure HDInsight (Hadoop på HDInsight) klynge (enten Windows eller Linux-baseret).
* Visual Studio 2012 eller 2013 eller 2015.

## <a name="create-the-application"></a>Oprette programmet

HDInsight .NET SDK indeholder .NET klientbiblioteker, som gør det nemmere at arbejde med HDInsight klynger fra .NET. 


1. Åbne Visual Studio 2012 eller 2013
2. Vælg **Ny** i menuen **filer** , og vælg derefter **projekt**.
3. Skriv eller Vælg følgende værdier for det nye projekt.

    <table>
    <tr>
    <th>Egenskaben</th>
    <th>Værdi</th>
    </tr>
    <tr>
    <th>Kategori</th>
    <th>Skabeloner/Visual C# / Windows</th>
    </tr>
    <tr>
    <th>Skabelon</th>
    <th>Console-program</th>
    </tr>
    <tr>
    <th>Navn</th>
    <th>SubmitPigJob</th>
    </tr>
    </table>
4. Klik på **OK** for at oprette projektet.
5. Vælg **Bibliotek Package Manager** eller **Nuget Package Manager**i menuen **Funktioner** , og vælg derefter **Pakke Manager-konsollen**.
6. Køre følgende kommando i console for at installere .NET SDK-pakkerne.

        Install-Package Microsoft.Azure.Management.HDInsight.Job

7. Solution Explorer skal du dobbeltklikke på **Program.cs** for at åbne den. Erstatte den eksisterende kode med følgende.

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

                    SubmitPigJob();

                    System.Console.WriteLine("Press ENTER to continue ...");
                    System.Console.ReadLine();
                }

                private static void SubmitPigJob()
                {
                    var parameters = new PigJobSubmissionParameters
                    {
                        Query = @"LOGS = LOAD 'wasbs:///example/data/sample.log';
                                    LEVELS = foreach LOGS generate REGEX_EXTRACT($0, '(TRACE|DEBUG|INFO|WARN|ERROR|FATAL)', 1)  as LOGLEVEL;
                                    FILTEREDLEVELS = FILTER LEVELS by LOGLEVEL is not null;
                                    GROUPEDLEVELS = GROUP FILTEREDLEVELS by LOGLEVEL;
                                    FREQUENCIES = foreach GROUPEDLEVELS generate group as LOGLEVEL, COUNT(FILTEREDLEVELS.LOGLEVEL) as COUNT;
                                    RESULT = order FREQUENCIES by COUNT desc;
                                    DUMP RESULT;"
                    };

                    System.Console.WriteLine("Submitting the Pig job to the cluster...");
                    var response = _hdiJobManagementClient.JobManagement.SubmitPigJob(parameters);
                    System.Console.WriteLine("Validating that the response is as expected...");
                    System.Console.WriteLine("Response status code is " + response.StatusCode);
                    System.Console.WriteLine("Validating the response object...");
                    System.Console.WriteLine("JobId is " + response.JobSubmissionJsonResponse.Id);
                }
            }
        }


7. Tryk på **F5** for at starte programmet.
8. Tryk på **ENTER** for at afslutte programmet.

## <a name="summary"></a>Oversigt

Som du kan se, kan du oprette .NET-programmer, sende gris job til en HDInsight klynge og overvåge jobstatus .NET SDK for Hadoop.

## <a name="next-steps"></a>Næste trin

Generelle oplysninger om gris i HDInsight.

* [Brug gris med Hadoop på HDInsight](hdinsight-use-pig.md)

Du kan arbejde med Hadoop på HDInsight oplysninger om andre måder.

* [Bruge Hive med Hadoop på HDInsight](hdinsight-use-hive.md)

* [Brug MapReduce med Hadoop på HDInsight](hdinsight-use-mapreduce.md) [preview-portal]: https://portal.azure.com/
