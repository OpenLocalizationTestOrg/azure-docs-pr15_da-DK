<properties
    pageTitle="Bruge PowerShell til at sikkerhedskopiere og gendanne App Service apps"
    description="Lær at bruge PowerShell til at sikkerhedskopiere og gendanne en app i Azure App Service"
    services="app-service"
    documentationCenter=""
    authors="NKing92"
    manager="wpickett"
    editor="" />

<tags
    ms.service="app-service"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/10/2016"
    ms.author="nicking"/>
# <a name="use-powershell-to-back-up-and-restore-app-service-apps"></a>Bruge PowerShell til at sikkerhedskopiere og gendanne App Service apps

> [AZURE.SELECTOR]
- [PowerShell](app-service-powershell-backup.md)
- [REST-API](../app-service-web/websites-csm-backup.md)

Lær at bruge Azure PowerShell til at sikkerhedskopiere og gendanne [App Service apps](https://azure.microsoft.com/services/app-service/web/). Finde flere oplysninger om web app sikkerhedskopier, herunder krav og begrænsninger, [sikkerhedskopiere en WebApp i Azure App Service](../app-service-web/web-sites-backup.md).

## <a name="prerequisites"></a>Forudsætninger
Hvis du vil bruge PowerShell til at administrere din app sikkerhedskopier, skal bruge du følgende:

- **En SAS URL-adresse** , der tillader læse- og skriveadgang til en objektbeholder med Azure-lager. Se [om SAS datamodellen](../storage/storage-dotnet-shared-access-signature-part-1.md) for en forklaring på SAS URL-adresser. Du kan finde eksempler på administrere Azure-lager ved hjælp af PowerShell i [Bruge Azure PowerShell med Azure-lager](../storage/storage-powershell-guide-full.md) .
- Hvis du vil sikkerhedskopiere en database sammen med din online **A databaseforbindelsesstreng** .

### <a name="how-to-generate-a-sas-url-to-use-with-the-web-app-backup-cmdlets"></a>Hvordan du opretter en SAS URL-adresse til brug med sikkerhedskopiering cmdletter web app
En URL-adresse til SAS kan oprettes med PowerShell. Her er et eksempel på, hvordan du opretter en, der kan bruges sammen med cmdlet'erne beskrives i denne artikel.

        $storageAccountName = "<your storage account's name>"
        $storageAccountRg = "<your storage account's resource group>"

        # This returns an array of keys for your storage account. Be sure to select the appropriate key. Here we select the first key as a default.
        $storageAccountKey = Get-AzureRmStorageAccountKey -ResourceGroupName $storageAccountRg -Name $storageAccountName
        $context = New-AzureStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storageAccountKey[0].Value

        $blobContainerName = "<name of blob container for app backups>"
        $sasUrl = New-AzureStorageContainerSASToken -Name $blobContainerName -Permission rwdl -Context $context -ExpiryTime (Get-Date).AddMonths(1) -FullUri

## <a name="install-azure-powershell-132-or-greater"></a>Installere Azure PowerShell 1.3.2 eller nyere

Se [Bruge Azure PowerShell med Azure ressourcestyring](../powershell-install-configure.md) vejledning i installation og brug af Azure PowerShell.

## <a name="create-a-backup"></a>Oprette en sikkerhedskopi

Brug cmdlet'en ny AzureRmWebAppBackup til at oprette en sikkerhedskopi af en WebApp.

        $sasUrl = "<your SAS URL>"
        $resourceGroupName = "Default-Web-WestUS"
        $appName = "ContosoApp"

        $backup = New-AzureRmWebAppBackup -ResourceGroupName $resourceGroupName -Name $appName -StorageAccountUrl $sasUrl

Dette opretter en sikkerhedskopi med en automatisk genereret navn. Hvis du vil angive et navn til sikkerhedskopien, kan du bruge parameteren BackupName valgfrit.

        $backup = New-AzureRmWebAppBackup -ResourceGroupName $resourceGroupName -Name $appName -StorageAccountUrl $sasUrl -BackupName MyBackup

Oprette en sikkerhedskopi Databaseindstilling, ved hjælp af ny AzureRmWebAppDatabaseBackupSetting cmdlet først for at medtage en database i sikkerhedskopien, og derefter angive indstillingen i parameteren databaser i ny AzureRmWebAppBackup cmdlet. Parameteren databaser accepterer en matrix med databaseindstillinger, så du kan sikkerhedskopiere mere end én database.

        $dbSetting1 = New-AzureRmWebAppDatabaseBackupSetting -Name DB1 -DatabaseType SqlAzure -ConnectionString "<connection_string>"
        $dbSetting2 = New-AzureRmWebAppDatabaseBackupSetting -Name DB2 -DatabaseType SqlAzure -ConnectionString "<connection_string>"
        $dbBackup = New-AzureRmWebAppBackup -ResourceGroupName $resourceGroupName -Name $appName -BackupName MyBackup -StorageAccountUrl $sasUrl -Databases $dbSetting1,$dbSetting2

## <a name="get-backups"></a>Få sikkerhedskopier

Get-AzureRmWebAppBackupList cmdlet returnerer en matrix med alle sikkerhedskopier til en WebApp. Du skal angive navnet på WebApp og dens ressourcegruppe.

        $resourceGroupName = "Default-Web-WestUS"
        $appName = "ContosoApp"
        $backups = Get-AzureRmWebAppBackupList -Name $appName -ResourceGroupName $resourceGroupName

Brug cmdlet'en Get-AzureRmWebAppBackup for at få en bestemt sikkerhedskopi.

        $backup = Get-AzureRmWebAppBackup -Name $appName -ResourceGroupName $resourceGroupName -BackupId 10102

Du kan også pipe et Web-app objekt inden for nogen af sikkerhedskopiering management-cmdlet'er til nemmere.

        $app = Get-AzureRmWebApp -Name ContosoApp -ResourceGroupName Default-Web-WestUS
        $backupList = $app | Get-AzureRmWebAppBackupList
        $backup = $app | Get-AzureRmWebAppBackup -BackupId 10102

## <a name="schedule-automatic-backups"></a>Planlægge automatisk sikkerhedskopiering

Du kan planlægge sikkerhedskopier til at opstå automatisk til et bestemt interval. Brug Rediger AzureRmWebAppBackupConfiguration cmdlet til at konfigurere en plan for sikkerhedskopiering. Denne cmdlet indeholder mange parametre:

- **Navn** - navnet på WebApp.
- **ResourceGroupName** - navnet på den ressourcegruppe, der indeholder WebApp.
- **Slot** - valgfrit. Navnet på web app-slotten.
- **StorageAccountUrl** - SAS URL-adressen for objektbeholderen Azure-lager, der bruges til at gemme sikkerhedskopierne.
- **FrequencyInterval** - numerisk værdi for hvor ofte sikkerhedskopier af bør foretages. Skal være et positivt heltal.
- **FrequencyUnit** - tidsenhed for hvor ofte sikkerhedskopier af bør foretages. Indstillinger er time og dag.
- **RetentionPeriodInDays** - hvor mange dage sikkerhedskopier af automatisk skal gemmes, før der slettes automatisk.
- **Starttidspunkt** - valgfrit. Den tid, når de automatiske sikkerhedskopier skal begynde. Sikkerhedskopier begynder med det samme, hvis dette er null. Skal være en dato/klokkeslæt.
- **Databaser** - valgfrit. En matrix med DatabaseBackupSettings for databaser til sikkerhedskopi.
- **KeepAtLeastOneBackup** - valgfrit skiftet parameter. Angive denne, hvis en sikkerhedskopi skal altid være informeret kontoen lagerplads, uanset hvor gammel, det er.

Følgende er et eksempel på, hvordan du bruger denne cmdlet.

        $resourceGroupName = "Default-Web-WestUS"
        $appName = "ContosoApp"
        $slotName = "StagingSlot"
        $dbSetting1 = New-AzureRmWebAppDatabaseBackupSetting -Name DB1 -DatabaseType SqlAzure -ConnectionString "<connection_string>"
        $dbSetting2 = New-AzureRmWebAppDatabaseBackupSetting -Name DB2 -DatabaseType SqlAzure -ConnectionString "<connection_string>"
        Edit-AzureRmWebAppBackupConfiguration -Name $appName -ResourceGroupName $resourceGroupName -Slot $slotName `
          -StorageAccountUrl "<your SAS URL>" -FrequencyInterval 6 -FrequencyUnit Hour -Databases $dbSetting1,$dbSetting2 `
          -KeepAtLeastOneBackup -StartTime (Get-Date).AddHours(1)

Brug cmdlet'en Get-AzureRmWebAppBackupConfiguration for at få den aktuelle tidsplan for sikkerhedskopiering. Det kan være nyttige til at redigere en plan, som allerede er blevet konfigureret.

        $configuration = Get-AzureRmWebAppBackupConfiguration -Name $appName -ResourceGroupName $resourceGroupName

        # Modify the configuration slightly
        $configuration.FrequencyInterval = 2
        $configuration.FrequencyUnit = "Day"

        # Apply the new configuration by piping it into the Edit-AzureRmWebAppBackupConfiguration cmdlet
        $configuration | Edit-AzureRmWebAppBackupConfiguration

## <a name="restore-a-web-app-from-a-backup"></a>Gendanne en web-app fra en sikkerhedskopi

Brug cmdlet'en Gendan AzureRmWebAppBackup, hvis du vil gendanne en web-app fra en sikkerhedskopi. Den nemmeste måde at bruge denne cmdlet er at pipe i et ekstra objekt hentet fra Get-AzureRmWebAppBackup cmdlet eller Get-AzureRmWebAppBackupList cmdlet.

Når du har et ekstra objekt, kan du pipe til Cmdletten Gendan AzureRmWebAppBackup. Angiv parameteren Overskriv Skift for at angive, at du har til hensigt at overskrive indholdet af din online med indholdet af sikkerhedskopien. Hvis sikkerhedskopien indeholder databaser, gendannes disse databaser samt.

        $backup | Restore-AzureRmWebAppBackup -Overwrite

Følgende er et eksempel på, hvordan du kan bruge Gendan-AzureRmWebAppBackup ved at angive alle parametre.

        $resourceGroupName = "Default-Web-WestUS"
        $appName = "ContosoApp"
        $slotName = "StagingSlot"
        $blobName = "ContosoBackup.zip"
        $dbSetting1 = New-AzureRmWebAppDatabaseBackupSetting -Name DB1 -DatabaseType SqlAzure -ConnectionString "<connection_string>"
        $dbSetting2 = New-AzureRmWebAppDatabaseBackupSetting -Name DB2 -DatabaseType SqlAzure -ConnectionString "<connection_string>"
        Restore-AzureRmWebAppBackup -ResourceGroupName $resourceGroupName -Name $appName -Slot $slotName -StorageAccountUrl "<your SAS URL>" -BlobName $blobName -Databases $dbSetting1,$dbSetting2 -Overwrite

## <a name="delete-a-backup"></a>Slette en sikkerhedskopi

Brug cmdlet'en Fjern AzureRmWebAppBackup, hvis du vil slette en sikkerhedskopi. Dette fjerner sikkerhedskopien fra kontoen lagerplads. Angiv appnavnet på din, dens ressourcegruppe og ID'ET for sikkerhedskopien, du vil slette.

        $resourceGroupName = "Default-Web-WestUS"
        $appName = "ContosoApp"
        Remove-AzureRmWebAppBackup -ResourceGroupName $resourceGroupName -Name $appName -BackupId 10102

Du kan også pipe et ekstra objekt i cmdlet'en Fjern AzureRmWebAppBackup til at slette den.

        $backup = Get-AzureRmWebAppBackup -Name $appName -ResourceGroupName $resourceGroupName -BackupId 10102
        $backup | Remove-AzureRmWebAppBackup -Overwrite
