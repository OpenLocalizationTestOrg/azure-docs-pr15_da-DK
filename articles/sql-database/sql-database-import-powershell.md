<properties
    pageTitle="Importere en BACPAC fil for at oprette en Azure SQL-database ved hjælp af PowerShell | Microsoft Azure"
    description="Importere en BACPAC fil for at oprette en Azure SQL-database ved hjælp af PowerShell"
    services="sql-database"
    documentationCenter=""
    authors="stevestein"
    manager="jhubbard"
    editor=""/>

<tags
    ms.service="sql-database"
    ms.devlang="NA"
    ms.topic="article"
    ms.tgt_pltfrm="powershell"
    ms.workload="data-management"
    ms.date="08/31/2016"
    ms.author="sstein"/>

# <a name="import-a-bacpac-file-to-create-an-azure-sql-database-by-using-powershell"></a>Importere en BACPAC fil for at oprette en Azure SQL-database ved hjælp af PowerShell

**Enkelt database**

> [AZURE.SELECTOR]
- [Azure-portalen](sql-database-import.md)
- [PowerShell](sql-database-import-powershell.md)
- [SSMS](sql-database-cloud-migrate-compatible-import-bacpac-ssms.md)
- [SqlPackage](sql-database-cloud-migrate-compatible-import-bacpac-sqlpackage.md)

Denne artikel indeholder anvisninger til oprettelse af en Azure SQL-database ved at importere en [BACPAC](https://msdn.microsoft.com/library/ee210546.aspx#Anchor_4) fil med PowerShell.

Databasen er oprettet fra en BACPAC fil (.bacpac), importeret fra en Azure-lager blob beholder. Hvis du ikke har en BACPAC fil i Azure-lager, kan du se [arkiv Azure SQL-database til en BACPAC fil ved hjælp af PowerShell](sql-database-export-powershell.md). Hvis du allerede har en BACPAC-fil, der ikke er i Azure-lager, [Brug AzCopy nemt overføre den til kontoen Azure-lager](../storage/storage-use-azcopy.md#blob-upload).

> [AZURE.NOTE] Azure SQL-Database automatisk opretter og vedligeholder sikkerhedskopier for hver brugerdatabase, du kan gendanne. Yderligere oplysninger finder du i [SQL-Database automatisk sikkerhedskopiering](sql-database-automated-backups.md).


For at importere en SQL-database skal bruge du følgende:

- Et Azure-abonnement. Hvis du skal bruge et Azure abonnement bare Klik på **Gratis prøveversion** øverst på denne side, og derefter vende tilbage til denne artikel er færdig.
- En BACPAC fil på den database, du vil importere. BACPAC skal være i en [Azure-lager konto](../storage/storage-create-storage-account.md) blob beholder.



[AZURE.INCLUDE [Start your PowerShell session](../../includes/sql-database-powershell.md)]



## <a name="set-up-the-variables-for-your-environment"></a>Konfigurere variabler for dit miljø

Der er et par variabler, hvor du vil erstatte eksempelværdier med de specifikke værdier for databasen og kontoen lagerplads.

Navnet på den server, skal være en server, der aktuelt findes i det abonnement, der er valgt i det forrige trin. Det skal være den server, du vil den database, der skal oprettes i. Importere en database direkte til en elastiske puljen understøttes ikke. Men du kan importere først til en enkelt database, og derefter flytte databasen til en gruppe.

Databasenavnet er det navn, du vil bruge til den nye database.

    $ResourceGroupName = "resource group name"
    $ServerName = "server name"
    $DatabaseName = "database name"


Følgende variabler er fra kontoen lagerplads, hvor din BACPAC er placeret. Gå til din lagerplads konto for at hente disse værdier i [Azure-portalen](https://portal.azure.com). Du kan finde den primære hurtigtast ved at klikke på **alle indstillinger** og derefter **taster** fra kontoen lagerplads blade.

Blob navnet er navnet på en eksisterende BACPAC-fil, du vil oprette databasen fra. Du vil medtage filtypenavnet .bacpac.

    $StorageName = "storageaccountname"
    $StorageKeyType = "StorageAccessKey"
    $StorageUri = "http://$StorageName.blob.core.windows.net/containerName/filename.bacpac"
    $StorageKey = "primaryaccesskey"


Kører på [Get-legitimationsoplysninger] (https://msdn.microsoft.com/library/azure/hh849815(v=azure.300\).aspx) cmdlet åbnes et vindue, der beder om dit brugernavn og din adgangskode. Angiv administrator logon og adgangskoden til SQL Database-serveren ($ServerName fra ovenfor), og ikke brugernavn og adgangskode for kontoen Azure.

    $credential = Get-Credential


## <a name="import-the-database"></a>Importere databasen

Denne kommando sender anmodning om en import-database til tjenesten. Afhængigt af størrelsen på databasen, kan importen gå et stykke tid at fuldføre.

    $importRequest = New-AzureRmSqlDatabaseImport –ResourceGroupName $ResourceGroupName –ServerName $ServerName –DatabaseName $DatabaseName –StorageKeytype $StorageKeyType –StorageKey $StorageKey -StorageUri $StorageUri –AdministratorLogin $credential.UserName –AdministratorLoginPassword $credential.Password –Edition Standard –ServiceObjectiveName S0 -DatabaseMaxSizeBytes 50000


## <a name="monitor-the-progress-of-the-operation"></a>Overvåge forløbet af handlingen

Når du har kørt ny-AzureRmSqlDatabaseImport (https://msdn.microsoft.com/library/azure/mt707793(v=azure.300\).aspx), du kan kontrollere status for din anmodning ved at køre [Get-AzureRmSqlDatabaseImportExportStatus] (https://msdn.microsoft.com/library/azure/mt707794(v=azure.300\).aspx).

    Get-AzureRmSqlDatabaseImportExportStatus -OperationStatusLink $importRequest.OperationStatusLink



## <a name="sql-database-powershell-import-script"></a>SQL-Database PowerShell Importér script


    $ResourceGroupName = "resourceGroupName"
    $ServerName = "servername"
    $DatabaseName = "databasename"

    $StorageName = "storageaccountname"
    $StorageKeyType = "StorageAccessKey"
    $StorageUri = "http://$StorageName.blob.core.windows.net/containerName/filename.bacpac"
    $StorageKey = "primaryaccesskey"

    $credential = Get-Credential

    $importRequest = New-AzureRmSqlDatabaseImport –ResourceGroupName $ResourceGroupName –ServerName $ServerName –DatabaseName $DatabaseName –StorageKeytype $StorageKeyType –StorageKey $StorageKey -StorageUri $StorageUri –AdministratorLogin $credential.UserName –AdministratorLoginPassword $credential.Password –Edition Standard –ServiceObjectiveName S0 -DatabaseMaxSizeBytes 50000

    Get-AzureRmSqlDatabaseImportExportStatus -OperationStatusLink $importRequest.OperationStatusLink



## <a name="next-steps"></a>Næste trin

- Du kan få til at oprette forbindelse til og forespørge importerede SQL-Database, [Opret forbindelse til SQL-Database med SQL Server Management Studio og udføre en stikprøve T-SQL-forespørgsel](sql-database-connect-query-ssms.md)
