<properties 
   pageTitle="Introduktion til Azure Data sø analyser ved hjælp af .NET SDK | Azure" 
   description="Lær, hvordan du bruger .NET SDK til at oprette sø datalager konti, skal du oprette Data sø Analytics job, og Send job, der er skrevet i U-SQL. " 
   services="data-lake-analytics" 
   documentationCenter="" 
   authors="edmacauley" 
   manager="jhubbard" 
   editor="cgronlun"/>
 
<tags
   ms.service="data-lake-analytics"
   ms.devlang="na"
   ms.topic="hero-article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data" 
   ms.date="10/26/2016"
   ms.author="edmaca"/>

# <a name="tutorial-get-started-with-azure-data-lake-analytics-using-net-sdk"></a>Selvstudium: Introduktion til Azure Data sø analyser ved hjælp af .NET SDK

[AZURE.INCLUDE [get-started-selector](../../includes/data-lake-analytics-selector-get-started.md)]


Lær, hvordan du bruger Azure .NET SDK til at sende job, der er skrevet i [U-SQL](data-lake-analytics-u-sql-get-started.md) Data sø analyser. Se [Oversigt over Azure Data sø Analytics](data-lake-analytics-overview.md)kan finde flere oplysninger om Data sø analyser.

I dette selvstudium, vil du udvikle et C# console-program til at sende et U-SQL-job, som læser en fane adskilt fil værdier (TSV) og konverterer det til en fil med semikolonseparerede værdier (CSV). For at gå gennem samme selvstudiet ved hjælp af andre understøttede værktøjer skal du klikke på fanerne øverst i denne artikel.

##<a name="prerequisites"></a>Forudsætninger

Inden du starter selvstudiet, skal du have følgende:

- **Visual Studio 2015, Visual Studio 2013 opdatere 4, eller Visual Studio 2012 med Visual C++ installeret**.
- **Microsoft Azure SDK til .NET version 2.5 eller nyere**.  Installere det ved hjælp af [Web platform installer](http://www.microsoft.com/web/downloads/platform.aspx).
- **En Azure Data sø Analytics-konto**. Se [Administrere Data sø analyser ved hjælp af Azure .NET SDK](data-lake-analytics-manage-use-dotnet-sdk.md).

##<a name="create-console-application"></a>Oprette console-program

I dette selvstudium behandle du nogle Søg logfiler.  Søg loggen kan være gemt i enten Data sø store eller Azure Blob-lager. 

En logfil over eksempel søgning kan findes i en offentlig Azure Blob-beholder. I programmet på computeren, kan du hente filen til computeren og derefter overføre filen til sø datalager standardkontoen for dine Data sø Analytics-konto.

**Oprette et U-SQL-script**

Data sø Analytics job skrives på sproget, U-SQL. Hvis du vil vide mere om U-SQL, skal du se [komme i gang med U-SQL sprog](data-lake-analytics-u-sql-get-started.md) og [U-SQL language reference](http://go.microsoft.com/fwlink/?LinkId=691348).

Oprette en **SampleUSQLScript.txt** -fil med følgende U-SQL-script, og Placer filen i det **C:\temp\* * sti.  Stien er hårdt i .NET programmet på computeren, som du opretter i den næste procedure.  

    @searchlog =
        EXTRACT UserId          int,
                Start           DateTime,
                Region          string,
                Query           string,
                Duration        int?,
                Urls            string,
                ClickedUrls     string
        FROM "/Samples/Data/SearchLog.tsv"
        USING Extractors.Tsv();
    
    OUTPUT @searchlog   
        TO "/Output/SearchLog-from-Data-Lake.csv"
    USING Outputters.Csv();

Dette U-SQL-script læser kildefilen data ved hjælp af **Extractors.Tsv()**og opretter derefter en CSV-fil ved hjælp af **Outputters.Csv()**. 

I C#-programmet skal du forberede **/Samples/Data/SearchLog.tsv** filen og mappen **/Output/** .    

Det er nemmere at bruge relative stier efter filer, der er lagret i data sø konti. Du kan også bruge absolutte stier.  For eksempel 

    adl://<Data LakeStorageAccountName>.azuredatalakestore.net:443/Samples/Data/SearchLog.tsv
    
Du skal bruge absolutte stier til at få adgang til filer i sammenkædede lagerplads konti.  Syntaksen for filer, der er gemt i den sammenkædede Azure-lager-konto er:

    wasb://<BlobContainerName>@<StorageAccountName>.blob.core.windows.net/Samples/Data/SearchLog.tsv

>[AZURE.NOTE] Der findes i øjeblikket et kendt problem med Azure Data sø Service.  Hvis appen eksempel bliver afbrudt eller støder på en fejl, skal du muligvis manuelt slette de sø datalager & Data sø Analytics-konti, som scriptet opretter.  Hvis du ikke kender portalen Azure, kan [administrere Azure Data sø Analytics Azure portalen](data-lake-analytics-manage-use-portal.md) vejledningen komme i gang.       

**Oprette et program**

1. Åbne Visual Studio.
2. Oprette et C# console program.
3. Åbne NuGet pakke Management console, og Kør følgende kommandoer:

        Install-Package Microsoft.Azure.Management.DataLake.Analytics -Pre
        Install-Package Microsoft.Azure.Management.DataLake.Store -Pre
        Install-Package Microsoft.Azure.Management.DataLake.StoreUploader -Pre
        Install-Package Microsoft.Rest.ClientRuntime.Azure.Authentication -Pre
        Install-Package WindowsAzure.Storage


       
5. Indsæt følgende kode i Program.cs:

        using System;
        using System.IO;
        using System.Collections.Generic;
        using System.Threading;
        using Microsoft.Rest;
        using Microsoft.Rest.Azure.Authentication;
        using Microsoft.Azure.Management.DataLake.Store;
        using Microsoft.Azure.Management.DataLake.StoreUploader;
        using Microsoft.Azure.Management.DataLake.Analytics;
        using Microsoft.Azure.Management.DataLake.Analytics.Models;
        using Microsoft.WindowsAzure.Storage.Blob;

        namespace SdkSample
        {
          class Program
          {
            private const string SUBSCRIPTIONID = "<Enter Your Azure Subscription ID>";
            private const string CLIENTID = "1950a258-227b-4e31-a9cf-717495945fc2";
            private const string DOMAINNAME = "common"; // Replace this string with the user's Azure Active Directory tenant ID or domain name, if needed.

            private static string _adlaAccountName = "<Enter an Existing Data Lake Analytics Account Name>";
            private static string _adlsAccountName = "<Enter the default Data Lake Store Account Name>";

            private static DataLakeAnalyticsAccountManagementClient _adlaClient;
            private static DataLakeStoreFileSystemManagementClient _adlsFileSystemClient;
            private static DataLakeAnalyticsJobManagementClient _adlaJobClient;
        
            private static void Main(string[] args)
            {
                string localFolderPath = @"c:\temp\";

                // Connect to Azure
                var creds = AuthenticateAzure(DOMAINNAME, CLIENTID);

                SetupClients(creds, SUBSCRIPTIONID);

                // Transfer the source file from a public Azure Blob container to Data Lake Store.
                CloudBlockBlob blob = new CloudBlockBlob(new Uri("https://adltutorials.blob.core.windows.net/adls-sample-data/SearchLog.tsv"));
                blob.DownloadToFile(localFolderPath + "SearchLog.tsv", FileMode.Create); // from WASB
                UploadFile(localFolderPath + "SearchLog.tsv", "/Samples/Data/SearchLog.tsv"); // to ADLS
                WaitForNewline("Source data file prepared.", "Submitting a job.");


                // Submit the job
                Guid jobId = SubmitJobByPath(localFolderPath + "SampleUSQLScript.txt", "My First ADLA Job");
                WaitForNewline("Job submitted.", "Waiting for job completion.");

                // Wait for job completion
                WaitForJob(jobId);
                WaitForNewline("Job completed.", "Downloading job output.");

                // Download job output
                DownloadFile(@"/Output/SearchLog-from-Data-Lake.csv", localFolderPath + "SearchLog-from-Data-Lake.csv");
        
                WaitForNewline("Job output downloaded. You can now exit.");
            }
        
            public static ServiceClientCredentials AuthenticateAzure(
                string domainName,
                string nativeClientAppCLIENTID)
            {
                // User login via interactive popup
                SynchronizationContext.SetSynchronizationContext(new SynchronizationContext());
                // Use the client ID of an existing AAD "Native Client" application.
                var activeDirectoryClientSettings = ActiveDirectoryClientSettings.UsePromptOnly(nativeClientAppCLIENTID, new Uri("urn:ietf:wg:oauth:2.0:oob"));
                return UserTokenProvider.LoginWithPromptAsync(domainName, activeDirectoryClientSettings).Result;
            }

            public static void SetupClients(ServiceClientCredentials tokenCreds, string subscriptionId)
            {
                _adlaClient = new DataLakeAnalyticsAccountManagementClient(tokenCreds);
                _adlaClient.SubscriptionId = subscriptionId;

                _adlaJobClient = new DataLakeAnalyticsJobManagementClient(tokenCreds);

                _adlsFileSystemClient = new DataLakeStoreFileSystemManagementClient(tokenCreds);
            }

            public static void UploadFile(string srcFilePath, string destFilePath, bool force = true)
            {
                var parameters = new UploadParameters(srcFilePath, destFilePath, _adlsAccountName, isOverwrite: force);
                var frontend = new DataLakeStoreFrontEndAdapter(_adlsAccountName, _adlsFileSystemClient);
                var uploader = new DataLakeStoreUploader(parameters, frontend);
                uploader.Execute();
            }

            public static void DownloadFile(string srcPath, string destPath)
            {
                var stream = _adlsFileSystemClient.FileSystem.Open(_adlsAccountName, srcPath);
                var fileStream = new FileStream(destPath, FileMode.Create);

                stream.CopyTo(fileStream);
                fileStream.Close();
                stream.Close();
            }

            // Helper function to show status and wait for user input
            public static void WaitForNewline(string reason, string nextAction = "")
            {
                Console.WriteLine(reason + "\r\nPress ENTER to continue...");

                Console.ReadLine();

                if (!String.IsNullOrWhiteSpace(nextAction))
                    Console.WriteLine(nextAction);
            }


            // List all Data Lake Analytics accounts within the subscription
            public static List<DataLakeAnalyticsAccount> ListADLAAccounts()
            {
                var response = _adlaClient.Account.List();
                var accounts = new List<DataLakeAnalyticsAccount>(response);

                while (response.NextPageLink != null)
                {
                    response = _adlaClient.Account.ListNext(response.NextPageLink);
                    accounts.AddRange(response);
                }

                Console.WriteLine("You have %i Data Lake Analytics account(s).", accounts.Count);
                for (int i = 0; i < accounts.Count; i++)
                {
                    Console.WriteLine(accounts[i].Name);
                }

                return accounts;
            }
            public static Guid SubmitJobByPath(string scriptPath, string jobName)
            {
                var script = File.ReadAllText(scriptPath);

                var jobId = Guid.NewGuid();
                var properties = new USqlJobProperties(script);
                var parameters = new JobInformation(jobName, JobType.USql, properties, priority: 1, degreeOfParallelism: 1, jobId: jobId);
                var jobInfo = _adlaJobClient.Job.Create(_adlaAccountName, jobId, parameters);

                return jobId;
            }

            public static JobResult WaitForJob(Guid jobId)
            {
                var jobInfo = _adlaJobClient.Job.Get(_adlaAccountName, jobId);
                while (jobInfo.State != JobState.Ended)
                {
                    jobInfo = _adlaJobClient.Job.Get(_adlaAccountName, jobId);
                }
                return jobInfo.Result.Value;
            }
          }
        }

6. Tryk på **F5** for at køre programmet. Output er som at:

    ![Azure Data sø Analytics job U-SQL .NET SDK output](./media/data-lake-analytics-get-started-net-sdk/data-lake-analytics-dotnet-job-output.png)

7. Kontrollere outputfilen.  Stien til og standardnavnet er c:\Temp\SearchLog-from-Data-Lake.csv.

## <a name="see-also"></a>Se også

- For at se det samme selvstudium ved hjælp af andre værktøjer skal du klikke på fanen vælgere øverst på siden.
- For at se en mere kompleks forespørgsel skal du se [analysér websted logfiler, der bruger Azure Data sø analyser](data-lake-analytics-analyze-weblogs.md).
- For at komme i gang U-SQL udviklingsprogrammer skal du se [udvikle U-SQL-scripts ved hjælp af Data sø Tools til Visual Studio](data-lake-analytics-data-lake-tools-get-started.md).
- Få U-SQL under [Introduktion til Azure Data sø Analytics U-SQL sprog](data-lake-analytics-u-sql-get-started.md)og [U-SQL language reference](http://go.microsoft.com/fwlink/?LinkId=691348).
- Se [administrere Azure Data sø Analytics Azure-portalen](data-lake-analytics-manage-use-portal.md)til af administrationsopgaver.
- For at få et overblik over Data sø Analytics skal du se [Oversigt over Azure Data sø analyser](data-lake-analytics-overview.md).
