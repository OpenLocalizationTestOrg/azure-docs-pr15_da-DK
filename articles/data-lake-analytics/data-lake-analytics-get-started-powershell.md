<properties 
   pageTitle="Introduktion til Azure Data sø analyser ved hjælp af PowerShell Azure | Azure" 
   description="Lær at bruge Azure PowerShell til at oprette en sø datalager-konto, skal du oprette et Data sø Analytics-job fra U-SQL, og Send jobbet. " 
   services="data-lake-analytics" 
   documentationCenter="" 
   authors="edmacauley" 
   manager="jhubbard" 
   editor="cgronlun"/>
 
<tags
   ms.service="data-lake-analytics"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data" 
   ms.date="09/21/2016"
   ms.author="edmaca"/>

# <a name="tutorial-get-started-with-azure-data-lake-analytics-using-azure-powershell"></a>Selvstudium: Introduktion til Azure Data sø analyser ved hjælp af Azure PowerShell

[AZURE.INCLUDE [get-started-selector](../../includes/data-lake-analytics-selector-get-started.md)]

Lær at bruge Azure PowerShell til at oprette Azure Data sø Analytics-konti, definere Data sø Analytics job i [U-SQL](data-lake-analytics-u-sql-get-started.md)og sende job til Data sø analytisk konti. Se [Oversigt over Azure Data sø Analytics](data-lake-analytics-overview.md)kan finde flere oplysninger om Data sø analyser.

I dette selvstudium, vil du udvikle et job, som læser en fane adskilt fil værdier (TSV) og konverterer det til en fil med kommaseparerede værdier (CSV). For at gå gennem samme selvstudiet ved hjælp af andre understøttede værktøjer skal du klikke på fanerne øverst på denne sektion.

##<a name="prerequisites"></a>Forudsætninger

Inden du starter selvstudiet, skal du have følgende:

- **En Azure-abonnement**. Se [få Azure gratis prøveversion](https://azure.microsoft.com/pricing/free-trial/).
- **En arbejdsstation med Azure PowerShell**. Se, [hvordan du installerer og konfigurerer Azure PowerShell](../powershell-install-configure.md).
    
##<a name="create-data-lake-analytics-account"></a>Oprette Data sø Analytics-konto

Du skal have en Data sø Analytics-konto, før du kan køre et job. Hvis du vil oprette en Data sø Analytics-konto, skal du angive følgende:

- **Azure ressourcegruppe**: A Data sø Analytics konto skal oprettes i en Azure ressourcegruppe. [Azure ressourcestyring](../azure-resource-manager/resource-group-overview.md) gør det muligt at arbejde med ressourcer i dit program som en gruppe. Du kan installere, opdatere eller slette alle ressourcerne for dit program i en enkelt, koordineret handling.  

    Optælles ressourcegrupperne i dit abonnement:
    
        Get-AzureRmResourceGroup
    
    Sådan oprettes en ny ressourcegruppe:

        New-AzureRmResourceGroup `
            -Name "<Your resource group name>" `
            -Location "<Azure Data Center>" # For example, "East US 2"

- **Data sø Analytics kontonavn**
- **Placering**: en af de Azure datacentre, der understøtter Data sø analyser.
- **Standard Data sø konto**: hver Data sø Analytics-konto har en standardkonto Data sø.

    Sådan oprettes en ny Data sø-konto:

        New-AzureRmDataLakeStoreAccount `
            -ResourceGroupName "<Your Azure resource group name>" `
            -Name "<Your Data Lake account name>" `
            -Location "<Azure Data Center>"  # For example, "East US 2"

    > [AZURE.NOTE] Kontonavnet Data sø skal kun indeholde små bogstaver og tal.



**Oprette en Data sø Analytics-konto**

1. Åbn PowerShell ISE fra din Windows-arbejdsstation.
2. Kør følgende script:

        $resourceGroupName = "<ResourceGroupName>"
        $dataLakeStoreName = "<DataLakeAccountName>"
        $dataLakeAnalyticsName = "<DataLakeAnalyticsAccountName>"
        $location = "East US 2"
        
        Write-Host "Create a resource group ..." -ForegroundColor Green
        New-AzureRmResourceGroup `
            -Name  $resourceGroupName `
            -Location $location
        
        Write-Host "Create a Data Lake account ..."  -ForegroundColor Green
        New-AzureRmDataLakeStoreAccount `
            -ResourceGroupName $resourceGroupName `
            -Name $dataLakeStoreName `
            -Location $location 
        
        Write-Host "Create a Data Lake Analytics account ..."  -ForegroundColor Green
        New-AzureRmDataLakeAnalyticsAccount `
            -Name $dataLakeAnalyticsName `
            -ResourceGroupName $resourceGroupName `
            -Location $location `
            -DefaultDataLake $dataLakeStoreName
        
        Write-Host "The newly created Data Lake Analytics account ..."  -ForegroundColor Green
        Get-AzureRmDataLakeAnalyticsAccount `
            -ResourceGroupName $resourceGroupName `
            -Name $dataLakeAnalyticsName  

##<a name="upload-data-to-data-lake"></a>Overføre data til Data sø

I dette selvstudium skal du behandle nogle Søg logfiler.  Søg loggen kan være gemt i enten Data sø store eller Azure Blob-lager. 

Et eksempel på søgning logfil er blevet kopieret til en offentlig Azure Blob-objektbeholder. Brug følgende PowerShell-script til at hente filen til computeren, og derefter overføre filen til sø datalager standardkontoen for dine Data sø Analytics-konto.

    $dataLakeStoreName = "<The default Data Lake Store account name>"
    
    $localFolder = "C:\Tutorials\Downloads\" # A temp location for the file. 
    $storageAccount = "adltutorials"  # Don't modify this value.
    $container = "adls-sample-data"  #Don't modify this value.

    # Create the temp location  
    New-Item -Path $localFolder -ItemType Directory -Force 

    # Download the sample file from Azure Blob storage
    $context = New-AzureStorageContext -StorageAccountName $storageAccount -Anonymous
    $blobs = Azure\Get-AzureStorageBlob -Container $container -Context $context
    $blobs | Get-AzureStorageBlobContent -Context $context -Destination $localFolder

    # Upload the file to the default Data Lake Store account    
    Import-AzureRmDataLakeStoreItem -AccountName $dataLakeStoreName -Path $localFolder"SearchLog.tsv" -Destination "/Samples/Data/SearchLog.tsv"

Følgende PowerShell-script viser, hvordan du får sø datalager standardnavnet for en Data sø Analytics-konto:


    $resourceGroupName = "<ResourceGroupName>"
    $dataLakeAnalyticsName = "<DataLakeAnalyticsAccountName>"
    $dataLakeStoreName = (Get-AzureRmDataLakeAnalyticsAccount -ResourceGroupName $resourceGroupName -Name $dataLakeAnalyticsName).Properties.DefaultDataLakeAccount
    echo $dataLakeStoreName

>[AZURE.NOTE] Azure-portalen en brugergrænseflade for at kopiere eksempeldatafilerne til sø datalager standardkontoen. Flere oplysninger under [Introduktion til Azure Data sø analyser ved hjælp af Azure-portalen](data-lake-analytics-get-started-portal.md#upload-data-to-the-default-data-lake-store-account).

Data sø Analytics kan også få adgang til Azure Blob-lager.  Overføre data til Azure Blob-lager, i [Brug af Azure PowerShell med Azure-lager](../storage/storage-powershell-guide-full.md).

##<a name="submit-data-lake-analytics-jobs"></a>Sende Data sø Analytics-job

Data sø Analytics-job skrives på sproget, U-SQL. Hvis du vil vide mere om U-SQL, skal du se [komme i gang med U-SQL sprog](data-lake-analytics-u-sql-get-started.md) og [U-SQL language reference](http://go.microsoft.com/fwlink/?LinkId=691348).

**Oprette et Data sø Analytics job script**

- Oprette en tekstfil med følgende U-SQL-script, og Gem tekstfilen til computeren:

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
    
    Undlad at ændre de to stier, medmindre du kopiere kildefilen til en anden placering.  Data sø Analytics vil oprette outputmappen, hvis den ikke findes.
    
    Det er nemmere at bruge relative stier efter filer, der er lagret i data sø konti. Du kan også bruge absolutte stier.  For eksempel 
    
        adl://<Data LakeStorageAccountName>.azuredatalakestore.net:443/Samples/Data/SearchLog.tsv
        
    Du skal bruge absolutte stier til at få adgang til filer i sammenkædede lagerplads konti.  Syntaksen for filer, der er gemt i sammenkædede Azure-lager-konto er:
    
        wasb://<BlobContainerName>@<StorageAccountName>.blob.core.windows.net/Samples/Data/SearchLog.tsv

    >[AZURE.NOTE] Azure Blob-objektbeholder med offentlige BLOB eller offentlige beholdere adgangstilladelser understøttes ikke i øjeblikket.    
    
    
**Til at sende jobbet**

1. Åbn PowerShell ISE fra din Windows-arbejdsstation.
2. Kør følgende script:

        $dataLakeAnalyticsName = "<DataLakeAnalyticsAccountName>"
        $usqlScript = "c:\tutorials\data-lake-analytics\copyFile.usql"
        
        $job = Submit-AzureRmDataLakeAnalyticsJob -Name "convertTSVtoCSV" -AccountName $dataLakeAnalyticsName –ScriptPath $usqlScript 

        Wait-AdlJob -Account $dataLakeAnalyticsName -JobId $job.JobId

        Get-AzureRmDataLakeAnalyticsJob -AccountName $dataLakeAnalyticsName -JobId $job.JobId

    I scriptet gemmes U-SQL-scriptfil på c:\tutorials\data-lake-analytics\copyFile.usql. Opdatere stien til filen i overensstemmelse hermed.
 
Når jobbet er fuldført, kan du bruge følgende cmdletter for at vise fil, og du kan hente filen:
    
    $resourceGroupName = "<Resource Group Name>"
    $dataLakeAnalyticName = "<Data Lake Analytic Account Name>"
    $destFile = "C:\tutorials\data-lake-analytics\SearchLog-from-Data-Lake.csv"
    
    $dataLakeStoreName = (Get-AzureRmDataLakeAnalyticsAccount -ResourceGroupName $resourceGroupName -Name $dataLakeAnalyticName).Properties.DefaultDataLakeAccount
    
    Get-AzureRmDataLakeStoreChildItem -AccountName $dataLakeStoreName -path "/Output"
    
    Export-AzureRmDataLakeStoreItem -AccountName $dataLakeStoreName -Path "/Output/SearchLog-from-Data-Lake.csv" -Destination $destFile

## <a name="see-also"></a>Se også

- For at se det samme selvstudium ved hjælp af andre værktøjer skal du klikke på fanen vælgere øverst på siden.
- For at se en mere kompleks forespørgsel skal du se [analysér websted logfiler, der bruger Azure Data sø analyser](data-lake-analytics-analyze-weblogs.md).
- For at komme i gang U-SQL udviklingsprogrammer skal du se [udvikle U-SQL-scripts ved hjælp af Data sø Tools til Visual Studio](data-lake-analytics-data-lake-tools-get-started.md).
- Få U-SQL under [Introduktion til Azure Data sø Analytics U-SQL-sprog](data-lake-analytics-u-sql-get-started.md).
- Du kan finde administrationsopgaver, [Administrere Azure Data sø analyser ved hjælp af Azure-portalen](data-lake-analytics-manage-use-portal.md).
- For at få et overblik over Data sø Analytics skal du se [Oversigt over Azure Data sø analyser](data-lake-analytics-overview.md).

