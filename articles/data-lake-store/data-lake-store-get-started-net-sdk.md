<properties
   pageTitle="Bruge Data sø Store .NET SDK til at udvikle programmer | Microsoft Azure"
   description="Bruge Azure Data sø Store .NET SDK til at udvikle programmer"
   services="data-lake-store"
   documentationCenter=""
   authors="nitinme"
   manager="jhubbard"
   editor="cgronlun"/>

<tags
   ms.service="data-lake-store"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data"
   ms.date="09/27/2016"
   ms.author="nitinme"/>

# <a name="get-started-with-azure-data-lake-store-using-net-sdk"></a>Introduktion til Azure Data sø Store ved hjælp af .NET SDK

> [AZURE.SELECTOR]
- [Portal](data-lake-store-get-started-portal.md)
- [PowerShell](data-lake-store-get-started-powershell.md)
- [.NET SDK](data-lake-store-get-started-net-sdk.md)
- [Java SDK](data-lake-store-get-started-java-sdk.md)
- [REST-API](data-lake-store-get-started-rest-api.md)
- [Azure CLI](data-lake-store-get-started-cli.md)
- [Node.js](data-lake-store-manage-use-nodejs.md)

Lær at bruge [Azure Data sø Store .NET SDK](https://msdn.microsoft.com/library/mt581387.aspx) til at udføre grundlæggende handlinger, sådan som oprette mapper, upload og download datafiler osv. Du kan finde flere oplysninger om Data sø, [Azure sø datalager](data-lake-store-overview.md).

## <a name="prerequisites"></a>Forudsætninger

* **Visual Studio 2013 eller 2015**. Vejledningen nedenfor for brug af Visual Studio-2015.

* **En Azure-abonnement**. Se [få Azure gratis prøveversion](https://azure.microsoft.com/pricing/free-trial/).

* **Azure sø datalager konto**. Finde oplysninger om, hvordan du opretter en konto, under [Introduktion til Azure sø datalager](data-lake-store-get-started-portal.md)

* **Oprette et Azure Active Directory-program**. Du kan bruge Azure AD-programmet til at godkende programmet sø datalager med Azure AD. Der er forskellige måder til at godkende med Azure AD, som er **Slutbrugerlicensaftale godkendelse** eller **tjeneste til godkendelse**. Instruktioner og flere oplysninger om, hvordan du godkender, under [Godkend med sø datalager brug af Azure Active Directory](data-lake-store-authenticate-using-active-directory.md).

## <a name="create-a-net-application"></a>Oprette et .NET-tjenesteprogram

1. Åbne Visual Studio og oprette et console-program.

2. Klik på **Ny**i menuen **filer** , og klik derefter på **projekt**.

3. Skriv fra **Nyt projekt**, eller Vælg følgende værdier:

  	| Egenskaben | Værdi                       |
  	|----------|-----------------------------|
  	| Kategori | Skabeloner/Visual C# / Windows |
  	| Skabelon | Console-program         |
  	| Navn     | CreateADLApplication        |

4. Klik på **OK** for at oprette projektet.

5. Tilføje Nuget-pakker til projektet.

    1. Højreklik på projektnavnet i Solution Explorer, og klik på **Administrer NuGet pakker**.
    2. Sørg for, **pakke kilden** er indstillet til **nuget.org** og afkrydsningsfeltet **Medtag foreløbig version** er markeret under fanen **Nuget Package Manager** .
    3. Søge efter og installere de følgende NuGet-pakker:

        * `Microsoft.Azure.Management.DataLake.Store`– I dette selvstudium bruger v0.12.5 preview.
        * `Microsoft.Azure.Management.DataLake.StoreUploader`– I dette selvstudium bruger v0.10.6 preview.
        * `Microsoft.Rest.ClientRuntime.Azure.Authentication`– I dette selvstudium bruger v2.2.8 preview.

        ![Tilføj en Nuget kilde] (./media/data-lake-store-get-started-net-sdk/ADL.Install.Nuget.Package.png "Opret en ny Azure Data sø-konto")

    4. Luk **Nuget Package Manager**.

6. Åbn **Program.cs**, slette den eksisterende kode, og derefter omfatter følgende udtalelser for at tilføje referencer til navneområder.

        using System;
        using System.Threading;
        
        using Microsoft.Rest.Azure.Authentication;
        using Microsoft.Azure.Management.DataLake.Store;
        using Microsoft.Azure.Management.DataLake.StoreUploader;

7. Erklære variablerne, som vist nedenfor, og leverer værdierne til sø datalager navn og ressource gruppenavn, der allerede findes. Du skal også kontrollere den lokale sti og filnavn, du angiver her, der skal findes på computeren. Du kan tilføje følgende kodestykke efter erklæringerne af navneområder.

        namespace SdkSample
        {
            class Program
            {
                private static DataLakeStoreAccountManagementClient _adlsClient;
                private static DataLakeStoreFileSystemManagementClient _adlsFileSystemClient;
                
                private static string _adlsAccountName;
                private static string _resourceGroupName;
                private static string _location;
                private static string _subId;

                
                private static void Main(string[] args)
                {
                    _adlsAccountName = "<DATA-LAKE-STORE-NAME>"; // TODO: Replace this value with the name of your existing Data Lake Store account.
                    _resourceGroupName = "<RESOURCE-GROUP-NAME>"; // TODO: Replace this value with the name of the resource group containing your Data Lake Store account.
                    _location = "East US 2";
                    _subId = "<SUBSCRIPTION-ID>";
                    
                    string localFolderPath = @"C:\local_path\"; // TODO: Make sure this exists and can be overwritten.
                    string localFilePath = localFolderPath + "file.txt"; // TODO: Make sure this exists and can be overwritten.
                    string remoteFolderPath = "/data_lake_path/";
                    string remoteFilePath = remoteFolderPath + "file.txt";
                }
            }
        }

I de resterende afsnit i denne artikel kan du se, hvordan du bruger de tilgængelige .NET metoder til at udføre handlinger som godkendelse, filoverførsel osv.

## <a name="authentication"></a>Godkendelse

### <a name="if-you-are-using-end-user-authentication-recommended-for-this-tutorial"></a>Hvis du bruger slutbrugerlicensaftale godkendelse (anbefales til dette selvstudium)

Brug denne med et eksisterende Azure AD "Native Client" program; en er angivet for dig nedenfor. For at hjælpe dig med at udføre dette selvstudium hurtigere, anbefaler vi, du bruger denne metode.

    // User login via interactive popup
    // Use the client ID of an existing AAD "Native Client" application.
    SynchronizationContext.SetSynchronizationContext(new SynchronizationContext());
    var domain = "common"; // Replace this string with the user's Azure Active Directory tenant ID or domain name, if needed.
    var nativeClientApp_clientId = "1950a258-227b-4e31-a9cf-717495945fc2";
    var activeDirectoryClientSettings = ActiveDirectoryClientSettings.UsePromptOnly(nativeClientApp_clientId, new Uri("urn:ietf:wg:oauth:2.0:oob"));
    var creds = UserTokenProvider.LoginWithPromptAsync(domain, activeDirectoryClientSettings).Result;

Værd at vide om denne ovenfor kodestykke par.

* For at hjælpe dig med at udføre selvstudiet hurtigere, denne kodestykke bruger en en Azure AD domæne og klient-ID, der er tilgængelige som standard for alle Azure abonnementer. Så du kan **Brug denne kodestykke som-er i dit program**.
* Men hvis du vil bruge din egen Azure AD-domæne og programmet klient-ID, skal du oprette en oprindelige Azure AD-program og derefter bruge Azure AD-domæne, klient-ID, og omdirigere URI for det program, du har oprettet. Du kan finde instruktioner i [Opret et Active Directory-program](../resource-group-create-service-principal-portal.md#create-an-active-directory-application) .

>[AZURE.NOTE] Vejledningen i linkene ovenfor er til en Azure AD-webprogrammet. Trinnene er dog præcis det samme selvom du har valgt at oprette et native client-program i stedet. 

### <a name="if-you-are-using-service-to-service-authentication-with-client-secret"></a>Hvis du bruger godkendelse tjenester med klient hemmeligt 

Følgende kodestykke kan bruges til at godkende dit program uden interaktion, ved hjælp af klienten hemmeligt / nøgle til en programmet / service hovedstolen. Brug denne med et eksisterende [Azure AD "Online"-program](../resource-group-create-service-principal-portal.md).

    // Service principal / appplication authentication with client secret / key
    // Use the client ID and certificate of an existing AAD "Web App" application.
    SynchronizationContext.SetSynchronizationContext(new SynchronizationContext());
    var domain = "<AAD-directory-domain>";
    var webApp_clientId = "<AAD-application-clientid>";
    var clientSecret = "<AAD-application-client-secret>";
    var clientCredential = new ClientCredential(webApp_clientId, clientSecret);
    var creds = ApplicationTokenProvider.LoginSilentAsync(domain, clientCredential).Result;

### <a name="if-you-are-using-service-to-service-authentication-with-certificate"></a>Hvis du bruger godkendelse tjenester med certifikat

Som en tredje valgmulighed, kan følgende kodestykke bruges til at godkende dit program uden interaktion, ved hjælp af certifikatet til en programmet / service hovedstolen. Brug denne med et eksisterende [Azure AD "Online"-program](../resource-group-create-service-principal-portal.md).

    // Service principal / application authentication with certificate
    // Use the client ID and certificate of an existing AAD "Web App" application.
    SynchronizationContext.SetSynchronizationContext(new SynchronizationContext());
    var domain = "<AAD-directory-domain>";
    var webApp_clientId = "<AAD-application-clientid>";
    System.Security.Cryptography.X509Certificates.X509Certificate2 clientCert = <AAD-application-client-certificate>
    var clientAssertionCertificate = new ClientAssertionCertificate(webApp_clientId, clientCert);
    var creds = ApplicationTokenProvider.LoginSilentWithCertificateAsync(domain, clientAssertionCertificate).Result;

## <a name="create-client-objects"></a>Oprette klient objekter

Følgende kodestykke opretter sø datalager konto og filsystemet klienten objekter, der skal bruges til at udstede anmodninger til tjenesten.

    // Create client objects and set the subscription ID
    _adlsClient = new DataLakeStoreAccountManagementClient(creds);
    _adlsFileSystemClient = new DataLakeStoreFileSystemManagementClient(creds);

    _adlsClient.SubscriptionId = _subId;

## <a name="list-all-data-lake-store-accounts-within-a-subscription"></a>Liste over alle sø datalager konti inden for et abonnement

Følgende kodestykke viser alle sø datalager konti inden for et givet Azure abonnement.

    // List all ADLS accounts within the subscription
    public static List<DataLakeStoreAccount> ListAdlStoreAccounts()
    {
        var response = _adlsClient.Account.List();
        var accounts = new List<DataLakeStoreAccount>(response);
        
        while (response.NextPageLink != null)
        {
            response = _adlsClient.Account.ListNext(response.NextPageLink);
            accounts.AddRange(response);
        }
        
        return accounts;
    }

## <a name="create-a-directory"></a>Oprette en mappe

Følgende kodestykke viser en `CreateDirectory` afskrivningsmetode, som du kan bruge til at oprette en mappe i en sø datalager-konto.

    // Create a directory
    public static void CreateDirectory(string path)
    {
        _adlsFileSystemClient.FileSystem.Mkdirs(_adlsAccountName, path);
    }

## <a name="upload-a-file"></a>Overføre en fil

Følgende kodestykke viser en `UploadFile` afskrivningsmetode, som du kan bruge til at overføre filer til en sø datalager-konto.

    // Upload a file
    public static void UploadFile(string srcFilePath, string destFilePath, bool force = true)
    {
        var parameters = new UploadParameters(srcFilePath, destFilePath, _adlsAccountName, isOverwrite: force);
        var frontend = new DataLakeStoreFrontEndAdapter(_adlsAccountName, _adlsFileSystemClient);
        var uploader = new DataLakeStoreUploader(parameters, frontend);
        uploader.Execute();
    }

`DataLakeStoreUploader`understøtter rekursiv upload og download mellem en lokal filsti og filsti sø datalager.    

## <a name="get-file-or-directory-info"></a>Få oplysninger om fil eller mappe

Følgende kodestykke viser en `GetItemInfo` afskrivningsmetode, som du kan bruge til at hente oplysninger om en fil eller mappe, der er tilgængelige i sø datalager. 

    // Get file or directory info
    public static FileStatusProperties GetItemInfo(string path)
    {
        return _adlsFileSystemClient.FileSystem.GetFileStatus(_adlsAccountName, path).FileStatus;
    }

## <a name="list-file-or-directories"></a>Liste over filer eller mapper

Følgende kodestykke viser en `ListItem` afskrivningsmetode, som kan bruge til at få vist filer og mapper på en sø datalager-konto.

    // List files and directories
    public static List<FileStatusProperties> ListItems(string directoryPath)
    {
        return _adlsFileSystemClient.FileSystem.ListFileStatus(_adlsAccountName, directoryPath).FileStatuses.FileStatus.ToList();
    }

## <a name="concatenate-files"></a>Sammenkædning filer

Følgende kodestykke viser en `ConcatenateFiles` afskrivningsmetode, som du kan bruge til at sammenkæde filer. 

    // Concatenate files
    public static void ConcatenateFiles(string[] srcFilePaths, string destFilePath)
    {
        _adlsFileSystemClient.FileSystem.Concat(_adlsAccountName, destFilePath, srcFilePaths);
    }

## <a name="append-to-a-file"></a>Føje til en fil

Følgende kodestykke viser en `AppendToFile` metode, du bruger føje data til en fil, der allerede er gemt i en sø datalager-konto.

    // Append to file
    public static void AppendToFile(string path, string content)
    {
        var stream = new MemoryStream(Encoding.UTF8.GetBytes(content));
        
        _adlsFileSystemClient.FileSystem.Append(_adlsAccountName, path, stream);
    }

## <a name="download-a-file"></a>Hente en fil

Følgende kodestykke viser en `DownloadFile` afskrivningsmetode, som du kan bruge til at hente en fil fra en sø datalager-konto.

    // Download file
    public static void DownloadFile(string srcPath, string destPath)
    {
        var stream = _adlsFileSystemClient.FileSystem.Open(_adlsAccountName, srcPath);
        var fileStream = new FileStream(destPath, FileMode.Create);
        
        stream.CopyTo(fileStream);
        fileStream.Close();
        stream.Close();
    }

## <a name="next-steps"></a>Næste trin

- [Sikre data i sø datalager](data-lake-store-secure-data.md)
- [Bruge Azure Data sø Analytics med sø datalager](../data-lake-analytics/data-lake-analytics-get-started-portal.md)
- [Bruge Azure HDInsight med sø datalager](data-lake-store-hdinsight-hadoop-use-portal.md)
- [Sø datalager .NET SDK Reference](https://msdn.microsoft.com/library/mt581387.aspx)
- [Sø datalager RESTEN Reference](https://msdn.microsoft.com/library/mt693424.aspx)
