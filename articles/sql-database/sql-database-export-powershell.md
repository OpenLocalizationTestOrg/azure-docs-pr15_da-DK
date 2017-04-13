<properties
    pageTitle="Arkivere en Azure SQL-database til en BACPAC-fil ved hjælp af PowerShell"
    description="Arkivere en Azure SQL-database til en BACPAC-fil ved hjælp af PowerShell"
    services="sql-database"
    documentationCenter=""
    authors="stevestein"
    manager="jhubbard"
    editor=""/>

<tags
    ms.service="sql-database"
    ms.devlang="NA"
    ms.date="08/15/2016"
    ms.author="sstein"
    ms.workload="data-management"
    ms.topic="article"
    ms.tgt_pltfrm="NA"/>


# <a name="archive-an-azure-sql-database-to-a-bacpac-file-by-using-powershell"></a>Arkivere en Azure SQL-database til en BACPAC-fil ved hjælp af PowerShell

> [AZURE.SELECTOR]
- [Azure-portalen](sql-database-export.md)
- [PowerShell](sql-database-export-powershell.md)


Denne artikel indeholder anvisninger til at arkivere dine Azure SQL-database til en [BACPAC](https://msdn.microsoft.com/library/ee210546.aspx#Anchor_4) -fil (gemt på Azure Blob-lager) ved hjælp af PowerShell.

Når du har brug at oprette et arkiv med en Azure SQL-database, kan du eksportere databaseskema og data til en BACPAC fil. En BACPAC fil er blot en ZIP-fil med filtypenavnet .bacpac. En BACPAC fil kan senere gemmes, i Azure Blob-lager eller lokale lager i en lokal placering. Det kan også importeres tilbage til Azure SQL-Database eller til en SQL Server-installation i det lokale miljø.

**Overvejelser i forbindelse med**

- For et arkiv skal være en transaktion ensartet, skal du sikre dig, der ikke skrive aktivitet bliver til virkelighed under eksporten eller, der skal eksporteres fra en [en transaktion ensartet kopi](sql-database-copy.md) af din Azure SQL-database.
- Den maksimale størrelse på en BACPAC fil arkiveret til Azure Blob-lager er 200 GB. Hvis du vil arkivere en større BACPAC-fil til det lokale lager, du brug værktøjet [SqlPackage](https://msdn.microsoft.com/library/hh550080.aspx) kommandoprompt. Dette værktøj leveres med både Visual Studio og SQL Server. Du kan også [hente](https://msdn.microsoft.com/library/mt204009.aspx) den nyeste version af SQL Server Data Tools til at få dette værktøj.
- Arkivering til Azure premium lager ved hjælp af en BACPAC fil understøttes ikke.
- Hvis eksporten overskrider 20 timers, kan den annulleres. For at øge ydeevnen under eksport, kan du:
 - Midlertidigt øge serviceniveau.
 - Ophøre alle læse og skrive aktivitet under eksporten.
 - Bruge et [grupperet indeks](https://msdn.microsoft.com/library/ms190457.aspx) med ikke-null-værdier på alle store tabeller. En eksport mislykkes uden grupperet indeks, hvis det tager længere tid end 6-12 timer. Dette skyldes, at tjenesten Eksportér skal udføre en tabelscanning for at forsøge at eksportere hele tabellen. Der er en god metode til at finde ud af, hvis dine tabeller er optimeret til eksport til at køre **DBCC SHOW_STATISTICS** , og Sørg for, at *RANGE_HI_KEY* ikke er null og dens værdi har god fordeling. Yderligere oplysninger finder du [DBCC SHOW_STATISTICS](https://msdn.microsoft.com/library/ms174384.aspx).

> [AZURE.NOTE] BACPACs er ikke beregnet til at bruges til sikkerhedskopiering og gendannelse handlinger. Azure SQL-Database opretter automatisk sikkerhedskopiering for hver brugerdatabase. Yderligere oplysninger finder du i [SQL-Database automatisk sikkerhedskopiering](sql-database-automated-backups.md).

For at fuldføre i denne artikel skal bruge du følgende:

- Et Azure-abonnement.
- Azure SQL-database.
- En [konto Standard Azure-lager](../storage/storage-create-storage-account.md), med en blob objektbeholder til at gemme BACPAC i standard-lager.


[AZURE.INCLUDE [Start your PowerShell session](../../includes/sql-database-powershell.md)]




## <a name="export-your-database"></a>Eksportere din database

[AzureRmSqlDatabaseExport ny] (https://msdn.microsoft.com/library/azure/mt707796(v=azure.300\).aspx) cmdlet sender en anmodning om Eksportér database til tjenesten. Afhængigt af størrelsen på databasen, kan eksporten gå et stykke tid at fuldføre.

> [AZURE.IMPORTANT] For at sikre, at en en transaktion ensartet BACPAC-fil, du skal første [oprette en kopi af databasen](sql-database-copy-powershell.md), og Eksportér derefter kopien af databasen.


     $exportRequest = New-AzureRmSqlDatabaseExport –ResourceGroupName $ResourceGroupName –ServerName $ServerName `
       –DatabaseName $DatabaseName –StorageKeytype $StorageKeytype –StorageKey $StorageKey -StorageUri $BacpacUri `
       –AdministratorLogin $creds.UserName –AdministratorLoginPassword $creds.Password


## <a name="monitor-the-progress-of-the-export-operation"></a>Overvåge forløbet af eksport

Når du har kørt ny-AzureRmSqlDatabaseExport (https://msdn.microsoft.com/library/azure/mt603644(v=azure.300\).aspx), du kan kontrollere status for din anmodning ved at køre [Get-AzureRmSqlDatabaseImportExportStatus] (https://msdn.microsoft.com/library/azure/mt707794(v=azure.300\).aspx). Køre dette umiddelbart efter anmodningen normalt returnerer **Status: InProgress**. Når du ser **Status: lykkedes** eksporten er gennemført.


    Get-AzureRmSqlDatabaseImportExportStatus -OperationStatusLink $exportRequest.OperationStatusLink



## <a name="export-sql-database-example"></a>Eksempel på Eksportér SQL-database

I følgende eksempel eksporterer en eksisterende SQL-database til en BACPAC og derefter viser, hvordan du kan kontrollere status for eksport.

Hvis du vil køre eksemplet, er der et par variabler, du vil erstatte med de specifikke værdier for din database og lager konto. Gå til din lagerplads konto for at få kontonavn lager, blob objektbeholder navn og nøgleværdi i [Azure-portalen](https://portal.azure.com). Du kan finde nøglen ved at klikke på **Access-taster** på din lagerplads konto blade.

Erstatte følgende `VARIABLE-VALUES` med værdier for din specifikke Azure ressourcer. Databasenavnet er den eksisterende database, du vil eksportere.



    $subscriptionId = "YOUR AZURE SUBSCRIPTION ID"

    Login-AzureRmAccount
    Set-AzureRmContext -SubscriptionId $subscriptionId

    # Database to export
    $DatabaseName = "DATABASE-NAME"
    $ResourceGroupName = "RESOURCE-GROUP-NAME"
    $ServerName = "SERVER-NAME"
    $serverAdmin = "ADMIN-NAME"
    $serverPassword = "ADMIN-PASSWORD" 
    $securePassword = ConvertTo-SecureString –String $serverPassword –AsPlainText -Force
    $creds = New-Object –TypeName System.Management.Automation.PSCredential –ArgumentList $serverAdmin, $securePassword

    # Generate a unique filename for the BACPAC
    $bacpacFilename = $DatabaseName + (Get-Date).ToString("yyyyMMddHHmm") + ".bacpac"

    # Storage account info for the BACPAC
    $BaseStorageUri = "https://STORAGE-NAME.blob.core.windows.net/BLOB-CONTAINER-NAME/"
    $BacpacUri = $BaseStorageUri + $bacpacFilename
    $StorageKeytype = "StorageAccessKey"
    $StorageKey = "YOUR STORAGE KEY"

    $exportRequest = New-AzureRmSqlDatabaseExport –ResourceGroupName $ResourceGroupName –ServerName $ServerName `
       –DatabaseName $DatabaseName –StorageKeytype $StorageKeytype –StorageKey $StorageKey -StorageUri $BacpacUri `
       –AdministratorLogin $creds.UserName –AdministratorLoginPassword $creds.Password
    $exportRequest

    # Check status of the export
    Get-AzureRmSqlDatabaseImportExportStatus -OperationStatusLink $exportRequest.OperationStatusLink



## <a name="next-steps"></a>Næste trin

- Hvis du vil lære at importere en Azure SQL-database ved hjælp af Powershell, skal du se [importere en BACPAC ved hjælp af PowerShell](sql-database-import-powershell.md).


## <a name="additional-resources"></a>Yderligere ressourcer

- [Ny AzureRmSqlDatabaseExport] (https://msdn.microsoft.com/library/azure/mt707796(v=azure.300\).aspx)
- [Get-AzureRmSqlDatabaseImportExportStatus] (https://msdn.microsoft.com/library/azure/mt707794(v=azure.300\).aspx)