<properties 
   pageTitle="Administrere Azure Data sø analyser ved hjælp af PowerShell Azure | Azure" 
   description="Lær at administrere Data sø Analytics job, datakilder, brugere. " 
   services="data-lake-analytics" 
   documentationCenter="" 
   authors="edmacauley" 
   manager="jhubbard" 
   editor="cgronlun"/>
 
<tags
   ms.service="data-lake-analytics"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data" 
   ms.date="05/16/2016"
   ms.author="edmaca"/>

# <a name="manage-azure-data-lake-analytics-using-azure-powershell"></a>Administrere Azure Data sø analyser ved hjælp af Azure PowerShell

[AZURE.INCLUDE [manage-selector](../../includes/data-lake-analytics-selector-manage.md)]

Få mere at vide, hvordan du administrerer Azure Data sø Analytics-konti, datakilder, brugere og job med Azure PowerShell. Hvis du vil se emner ved hjælp af andre værktøjer, skal du klikke på fanen Vælg ovenfor.

**Forudsætninger**

Inden du starter selvstudiet, skal du have følgende:

- **En Azure-abonnement**. Se [få Azure gratis prøveversion](https://azure.microsoft.com/pricing/free-trial/).


<!-- ################################ -->
<!-- ################################ -->


##<a name="install-azure-powershell-10-or-greater"></a>Installere Azure PowerShell 1.0 eller nyere

Se afsnittet nødvendige i [Bruge PowerShell for Azure med Azure ressourcestyring](powershell-azure-resource-manager.md#prerequisites).
    
## <a name="manage-accounts"></a>Administrere konti

Før du kører en hvilken som helst Data sø Analytics-job, skal du have en Data sø Analytics-konto. I modsætning til Azure HDInsight betaler ikke du for en Analytics-konto, når den ikke kører et job.  Du betaler kun for den tid, når den kører et job.  Se [Oversigt over Azure Data sø Analytics](data-lake-analytics-overview.md)kan finde flere oplysninger.  

###<a name="create-accounts"></a>Oprette konti

    $resourceGroupName = "<ResourceGroupName>"
    $dataLakeStoreName = "<DataLakeAccountName>"
    $dataLakeAnalyticsAccountName = "<DataLakeAnalyticsAccountName>"
    $location = "<Microsoft Data Center>"
    
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
        -Name $dataLakeAnalyticsAccountName `
        -ResourceGroupName $resourceGroupName `
        -Location $location `
        -DefaultDataLake $dataLakeStoreName
    
    Write-Host "The newly created Data Lake Analytics account ..."  -ForegroundColor Green
    Get-AzureRmDataLakeAnalyticsAccount `
        -ResourceGroupName $resourceGroupName `
        -Name $dataLakeAnalyticsAccountName  

Du kan også bruge en skabelon til Azure ressourcegruppe. En skabelon til oprettelse af en Data sø Analytics-kontoen og afhængige sø datalager kontoen er i [tillæg A](#appendix-a). Gemme skabelonen til en fil med .json skabelon, og brug derefter følgende PowerShell-script til at ringe til det:


    $AzureSubscriptionID = "<Your Azure Subscription ID>"
    
    $ResourceGroupName = "<New Azure Resource Group Name>"
    $Location = "EAST US 2"
    $DefaultDataLakeStoreAccountName = "<New Data Lake Store Account Name>"
    $DataLakeAnalyticsAccountName = "<New Data Lake Analytics Account Name>"
    
    $DeploymentName = "MyDataLakeAnalyticsDeployment"
    $ARMTemplateFile = "E:\Tutorials\ADL\ARMTemplate\azuredeploy.json"  # update the Json template path 
    
    Login-AzureRmAccount
    
    Select-AzureRmSubscription -SubscriptionId $AzureSubscriptionID
    
    # Create the resource group
    New-AzureRmResourceGroup -Name $ResourceGroupName -Location $Location
    
    # Create the Data Lake Analytics account with the default Data Lake Store account.
    $parameters = @{"adlAnalyticsName"=$DataLakeAnalyticsAccountName; "adlStoreName"=$DefaultDataLakeStoreAccountName}
    New-AzureRmResourceGroupDeployment -Name $DeploymentName -ResourceGroupName $ResourceGroupName -TemplateFile $ARMTemplateFile -TemplateParameterObject $parameters 

 
###<a name="list-account"></a>Liste over konto

Listen Data sø Analytics konti inden for det aktuelle abonnement

    Get-AzureRmDataLakeAnalyticsAccount
    
Output:

    Id         : /subscriptions/xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx/resourceGroups/learn1021rg/providers/Microsoft.DataLakeAnalytics/accounts/learn1021adla
    Location   : eastus2
    Name       : learn1021adla
    Properties : Microsoft.Azure.Management.DataLake.Analytics.Models.DataLakeAnalyticsAccountProperties
    Tags       : {}
    Type       : Microsoft.DataLakeAnalytics/accounts

Listen Data sø Analytics konti inden for en bestemt ressourcegruppe

    Get-AzureRmDataLakeAnalyticsAccount -ResourceGroupName $resourceGroupName

Få oplysninger om en bestemt Data sø Analytics-konto

    Get-AzureRmDataLakeAnalyticsAccount -Name $adlAnalyticsAccountName

Test eksistensen af en bestemt Data sø Analytics-konto

    Test-AzureRmDataLakeAnalyticsAccount -Name $adlAnalyticsAccountName

Cmdlet returnerer **True** eller **False**.

###<a name="delete-data-lake-analytics-accounts"></a>Slette Data sø Analytics-konti

    $resourceGroupName = "<ResourceGroupName>"
    $dataLakeAnalyticsAccountName = "<DataLakeAnalyticsAccountName>"
    
    Remove-AzureRmDataLakeAnalyticsAccount -Name $dataLakeAnalyticsAccountName 

Slet Data sø Analytics konto bliver ikke slettet kontoen afhængige sø datalagring. I følgende eksempel sletter Data sø Analytics-kontoen og sø datalager standardkontoen

    $resourceGroupName = "<ResourceGroupName>"
    $dataLakeAnalyticsAccountName = "<DataLakeAnalyticsAccountName>"
    $dataLakeStoreName = (Get-AzureRmDataLakeAnalyticsAccount -ResourceGroupName $resourceGroupName -Name $dataLakeAnalyticAccountName).Properties.DefaultDataLakeAccount

    Remove-AzureRmDataLakeAnalyticsAccount -ResourceGroupName $resourceGroupName -Name $dataLakeAnalyticAccountName 
    Remove-AzureRmDataLakeStoreAccount -ResourceGroupName $resourceGroupName -Name $dataLakeStoreName

<!-- ################################ -->
<!-- ################################ -->
## <a name="manage-account-data-sources"></a>Administrer konto datakilder

Data sø Analytics understøtter aktuelt følgende datakilder:

- [Azure sø datalager](../data-lake-store/data-lake-store-overview.md)
- [Azure-lager](storage-introduction.md)

Når du opretter en Analytics-konto, skal du angive en Azure datalagring sø konto skal være standardkontoen lagerplads. Sø datalager standardkontoen bruges til at gemme jobbet metadata og job overvågningslogge. Når du har oprettet en Analytics-konto, kan du tilføje flere sø datalagring konti og/eller Azure-lager-konto. 

### <a name="find-the-default-data-lake-store-account"></a>Finde sø datalager standardkontoen

    $resourceGroupName = "<ResourceGroupName>"
    $dataLakeAnalyticsAccountName = "<DataLakeAnalyticsAccountName>"
    $dataLakeStoreName = (Get-AzureRmDataLakeAnalyticsAccount -ResourceGroupName $resourceGroupName -Name $dataLakeAnalyticAccountName).Properties.DefaultDataLakeAccount


### <a name="add-additional-azure-blob-storage-accounts"></a>Tilføje flere Azure Blob storage konti

    $resourceGroupName = "<ResourceGroupName>"
    $dataLakeAnalyticsAccountName = "<DataLakeAnalyticsAccountName>"
    $AzureStorageAccountName = "<AzureStorageAccountName>"
    $AzureStorageAccountKey = "<AzureStorageAccountKey>"
    
    Add-AzureRmDataLakeAnalyticsDataSource -ResourceGroupName $resourceGroupName -Account $dataLakeAnalyticAccountName -AzureBlob $AzureStorageAccountName -AccessKey $AzureStorageAccountKey

### <a name="add-additional-data-lake-store-accounts"></a>Tilføje flere sø datalager konti

    $resourceGroupName = "<ResourceGroupName>"
    $dataLakeAnalyticsAccountName = "<DataLakeAnalyticsAccountName>"
    $AzureDataLakeName = "<DataLakeStoreName>"
    
    Add-AzureRmDataLakeAnalyticsDataSource -ResourceGroupName $resourceGroupName -Account $dataLakeAnalyticAccountName -DataLake $AzureDataLakeName 

### <a name="list-data-sources"></a>Listedatakilder:

    $resourceGroupName = "<ResourceGroupName>"
    $dataLakeAnalyticsAccountName = "<DataLakeAnalyticsAccountName>"

    (Get-AzureRmDataLakeAnalyticsAccount -ResourceGroupName $resourceGroupName -Name $dataLakeAnalyticAccountName).Properties.DataLakeStoreAccounts
    (Get-AzureRmDataLakeAnalyticsAccount -ResourceGroupName $resourceGroupName -Name $dataLakeAnalyticAccountName).Properties.StorageAccounts
    


<!-- ################################ -->
<!-- ################################ -->
## <a name="manage-jobs"></a>Administrere job

Du skal have en Data sø Analytics-konto, før du kan oprette en sag.  Yderligere oplysninger finder du se [administrere Data sø Analytics konti](#manage-data-lake-analytics-accounts).

### <a name="list-jobs"></a>Vis job

    $dataLakeAnalyticsAccountName = "<DataLakeAnalyticsAccountName>"
    
    Get-AzureRmDataLakeAnalyticsJob -Account $dataLakeAnalyticAccountName
    
    Get-AzureRmDataLakeAnalyticsJob -Account $dataLakeAnalyticAccountName -State Running, Queued
    #States: Accepted, Compiling, Ended, New, Paused, Queued, Running, Scheduling, Starting
    
    Get-AzureRmDataLakeAnalyticsJob -Account $dataLakeAnalyticAccountName -Result Cancelled
    #Results: Cancelled, Failed, None, Successed 
    
    Get-AzureRmDataLakeAnalyticsJob -Account $dataLakeAnalyticAccountName -Name <Job Name>
    Get-AzureRmDataLakeAnalyticsJob -Account $dataLakeAnalyticAccountName -Submitter <Job submitter>

    # List all jobs submitted on January 1 (local time)
    Get-AzureRmDataLakeAnalyticsJob -Account $dataLakeAnalyticAccountName `
        -SubmittedAfter "2015/01/01"
        -SubmittedBefore "2015/01/02"   

    # List all jobs that succeeded on January 1 after 2 pm (UTC time)
    Get-AzureRmDataLakeAnalyticsJob -Account $dataLakeAnalyticAccountName `
        -State Ended
        -Result Succeeded
        -SubmittedAfter "2015/01/01 2:00 PM -0"
        -SubmittedBefore "2015/01/02 -0"

    # List all jobs submitted in the past hour
    Get-AzureRmDataLakeAnalyticsJob -Account $dataLakeAnalyticAccountName `
        -SubmittedAfter (Get-Date).AddHours(-1)

### <a name="get-job-details"></a>Få oplysninger om job

    $dataLakeAnalyticsAccountName = "<DataLakeAnalyticsAccountName>"
    Get-AzureRmDataLakeAnalyticsJob -Account $dataLakeAnalyticAccountName -JobID <Job ID>
    
### <a name="submit-jobs"></a>Sende job

    $dataLakeAnalyticsAccountName = "<DataLakeAnalyticsAccountName>"

    #Pass script via path
    Submit-AzureRmDataLakeAnalyticsJob -Account $dataLakeAnalyticAccountName `
        -Name $jobName `
        -ScriptPath $scriptPath

    #Pass script contents
    Submit-AzureRmDataLakeAnalyticsJob -Account $dataLakeAnalyticAccountName `
        -Name $jobName `
        -Script $scriptContents

> [AZURE.NOTE] Standardprioriteten af en sag er 1000, og standard graden af parallelitet for en sag er 1.


### <a name="cancel-jobs"></a>Annullere job

    Stop-AzureRmDataLakeAnalyticsJob -Account $dataLakeAnalyticAccountName `
        -JobID $jobID


## <a name="manage-catalog-items"></a>Administrere katalogelementer

U-SQL-kataloget bruges til at strukturere data og kode, så de kan deles af U-SQL-scripts. Kataloget gør det muligt med data i Azure Data sø højeste ydeevne. Du kan finde yderligere oplysninger finder [Brug U-SQL-katalog](data-lake-analytics-use-u-sql-catalog.md).

###<a name="list-catalog-items"></a>Katalog listeelementer

    #List databases
    Get-AzureRmDataLakeAnalyticsCatalogItem `
        -Account $adlAnalyticsAccountName `
        -ItemType Database
    
    
    
    #List tables
    Get-AzureRmDataLakeAnalyticsCatalogItem `
        -Account $adlAnalyticsAccountName `
        -ItemType Table `
        -Path "master.dbo"

###<a name="get-catalog-item-details"></a>Få oplysninger om katalog 

    #Get a database
    Get-AzureRmDataLakeAnalyticsCatalogItem `
        -Account $adlAnalyticsAccountName `
        -ItemType Database `
        -Path "master"
    
    #Get a table
    Get-AzureRmDataLakeAnalyticsCatalogItem `
        -Account $adlAnalyticsAccountName `
        -ItemType Table `
        -Path "master.dbo.mytable"

###<a name="test-existence-of--catalog-item"></a>Test eksistensen af kataloget element

    Test-AzureRmDataLakeAnalyticsCatalogItem  `
        -Account $adlAnalyticsAccountName `
        -ItemType Database `
        -Path "master"

###<a name="create-catalog-secret"></a>Oprette katalog hemmeligt
    New-AzureRmDataLakeAnalyticsCatalogSecret  `
            -Account $adlAnalyticsAccountName `
            -DatabaseName "master" `
            -Secret (Get-Credential -UserName "username" -Message "Enter the password")

### <a name="modify-catalog-secret"></a>Ændre katalog hemmeligt
    Set-AzureRmDataLakeAnalyticsCatalogSecret  `
            -Account $adlAnalyticsAccountName `
            -DatabaseName "master" `
            -Secret (Get-Credential -UserName "username" -Message "Enter the password")



###<a name="delete-catalog-secret"></a>Slette katalog hemmeligt
    Remove-AzureRmDataLakeAnalyticsCatalogSecret  `
            -Account $adlAnalyticsAccountName `
            -DatabaseName "master"


## <a name="use-azure-resource-manager-groups"></a>Bruge Azure ressourcestyring grupper

Programmer består normalt af mange komponenter, for eksempel en WebApp, database, databaseserver, lager og 3 part services. Azure ressource Manager (ARM) gør det muligt at arbejde med ressourcer i dit program som en gruppe, kaldes en Azure ressourcegruppe. Du kan installere, opdatere, overvåge eller slette alle ressourcerne for dit program i en enkelt, koordineret handling. Du bruger en skabelon til installation og denne skabelon kan arbejde for forskellige miljøer som test, test- og. Du kan få overblik over fakturering for din organisation ved at få vist de akkumulerede omkostninger for hele gruppen. Du kan finde yderligere oplysninger finder [Azure ressourcestyring oversigt](../azure-resource-manager/resource-group-overview.md). 

En Data sø Analytics-tjenesten kan omfatte følgende komponenter:

- Azure Data sø Analytics-konto
- Påkrævet standardkonto for Azure sø datalagring
- Ekstra Azure Data sø lagerplads konti
- Flere Azure-lager-konti

Du kan oprette alle disse komponenter under én ARM gruppe for at gøre dem nemmere at administrere.

![Azure Data sø Analytics-konto og lager](./media/data-lake-analytics-manage-use-portal/data-lake-analytics-arm-structure.png)

En Data sø Analytics-konto og afhængige lagerplads konti skal være placeret i den samme Azure datacenter.
Gruppen ARM kan dog være placeret i et andet datacenter.  

##<a name="see-also"></a>Se også 

- [Oversigt over Microsoft Azure Data sø Analytics](data-lake-analytics-overview.md)
- [Komme i gang med Data sø analyser ved hjælp af Azure-portalen](data-lake-analytics-get-started-portal.md)
- [Administrere Azure Data sø analyser ved hjælp af Azure-portalen](data-lake-analytics-manage-use-portal.md)
- [Overvåge og foretage fejlfinding af Azure Data sø Analytics job ved hjælp af Azure-portalen](data-lake-analytics-monitor-and-troubleshoot-jobs-tutorial.md)

##<a name="appendix-a---data-lake-analytics-arm-template"></a>Tillæg A - Data sø Analytics ARM skabelon

Følgende ARM skabelon kan bruges til at installere en Data sø Analytics-konto og dens afhængige sø datalager-konto.  Gemme den som en json-fil, og derefter bruge PowerShell-script til at ringe til skabelonen. Få mere at vide under [Implementer et program med Azure ressourcestyring skabelon](../resource-group-template-deploy.md#deploy-with-powershell) og [redigering Azure ressourcestyring skabeloner](../resource-group-authoring-templates.md).

    {
      "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
      "contentVersion": "1.0.0.0",
      "parameters": {
        "adlAnalyticsName": {
          "type": "string",
          "metadata": {
            "description": "The name of the Data Lake Analytics account to create."
          }
        },
        "adlStoreName": {
          "type": "string",
          "metadata": {
            "description": "The name of the Data Lake Store account to create."
          }
        }
      },
      "resources": [
        {
          "name": "[parameters('adlStoreName')]",
          "type": "Microsoft.DataLakeStore/accounts",
          "location": "East US 2",
          "apiVersion": "2015-10-01-preview",
          "dependsOn": [ ],
          "tags": { }
        },
        {
          "name": "[parameters('adlAnalyticsName')]",
          "type": "Microsoft.DataLakeAnalytics/accounts",
          "location": "East US 2",
          "apiVersion": "2015-10-01-preview",
          "dependsOn": [ "[concat('Microsoft.DataLakeStore/accounts/',parameters('adlStoreName'))]" ],
          "tags": { },
          "properties": {
            "defaultDataLakeStoreAccount": "[parameters('adlStoreName')]",
            "dataLakeStoreAccounts": [
              { "name": "[parameters('adlStoreName')]" }
            ]
          }
        }
      ],
      "outputs": {
        "adlAnalyticsAccount": {
          "type": "object",
          "value": "[reference(resourceId('Microsoft.DataLakeAnalytics/accounts',parameters('adlAnalyticsName')))]"
        },
        "adlStoreAccount": {
          "type": "object",
          "value": "[reference(resourceId('Microsoft.DataLakeStore/accounts',parameters('adlStoreName')))]"
        }
      }
    }

