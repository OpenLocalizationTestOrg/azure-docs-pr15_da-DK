<properties
   pageTitle="Overføre store mængder data til datalager sø offline måder | Microsoft Azure"
   description="Brug AdlCopy til at kopiere data fra Azure-lager BLOB til sø datalager"
   services="data-lake-store"
   documentationCenter=""
   authors="nitinme"
   manager="jhubbard"
   editor="cgronlun"/>

<tags
   ms.service="data-lake-store"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data"
   ms.date="10/07/2016"
   ms.author="nitinme"/>

# <a name="use-azure-import-export-service-for-offline-copy-of-data-to-data-lake-store"></a>Brug Azure Import-eksport-tjeneste til offlinekopi af data til sø datalager

I denne artikel, skal du lære om, hvordan du kopierer store datasæt (> 200GB) til et Azure sø datalager offlinekopi måder, som [Azure Importér/Eksportér Service](../storage/storage-import-export-service.md). Den fil, der bruges som et eksempel i denne artikel er specifikt 339,420,860,416 byte det vil sige om 319GB på disk. Lad os ringe 319GB.tsv denne fil.

Azure Importér/Eksportér Service kan du overføre sikkert store mængder data til Azure blob-lager ved forsendelses harddiskdrev til et Azure datacenter.

## <a name="prerequisites"></a>Forudsætninger

Før du begynder i denne artikel, skal du have følgende:

- **En Azure-abonnement**. Se [få Azure gratis prøveversion](https://azure.microsoft.com/pricing/free-trial/).

- **Azure-lager-konto**.

- **Azure Data sø Analytics-konto (valgfrit)** – som se [Introduktion til Azure Data sø Analytics](../data-lake-analytics/data-lake-analytics-get-started-portal.md) for oplysninger om, hvordan du opretter en sø datalager-konto.


## <a name="preparing-the-data"></a>Forberedelse af dataene

Før du bruger tjenesten Importér/Eksportér, skal vi bryde datafilen for at blive overført **til kopier, der er mindre end 200 GB** i størrelse. Dette skyldes, at importere tool ikke fungerer med filer, der er større end 200GB. I overensstemmelse med dette, i dette selvstudium vi opdele filen i dele af 100GB byte. Du kan gøre dette nemt ved hjælp af [Cygwin](https://cygwin.com/install.html). Cygwin understøtter Linux-kommando, som gør det muligt for brugerne at gøre dette nemt. Vi bruger følgende kommando for vores sag.

    split -b 100m 319GB.tsv

Opdelingen opretter filer med navnene nedenfor.

    319GB.tsv-part-aa

    319GB.tsv-part-ab

    319GB.tsv-part-ac

    319GB.tsv-part-ad

## <a name="get-disks-ready-with-data"></a>Er du klar diske med data

Følg vejledningen på [Ved hjælp af Azure Importér/Eksportér Service](../storage/storage-import-export-service.md) (under afsnittet **forberede dine drev** ) for at forberede dit harddiske. Her er den samlede strøm om hvordan du forbereder drevet.

1. Levere en harddisk, der opfylder kravet skal bruges til Auzre Importér/Eksportér tjeneste.

2. Identificere en Azure-lager-konto, hvor dataene skal være kopierede én gang si afsendt til Azure datacentret.

3. Brug [Azure Importér/Eksportér værktøjet](http://go.microsoft.com/fwlink/?LinkID=301900&clcid=0x409)en fra kommandolinjen til. Her er et eksempel kodestykke om, hvordan du bruger værktøjet.

    ````
    WAImportExport PrepImport /sk:<StorageAccountKey> /t: <TargetDriveLetter> /format /encrypt /logdir:e:\myexportimportjob\logdir /j:e:\myexportimportjob\journal1.jrn /id:myexportimportjob /srcdir:F:\demo\ExImContainer /dstdir:importcontainer/vf1/
    ````
    Du kan finde flere eksempel kodestykker om, hvordan du bruger **Azure Importér/Eksportér værktøj**i [Ved hjælp af Azure Importér/Eksportér Service](../storage/storage-import-export-service.md) .

4. Ovenstående kommando opretter en journal-fil på den angivne placering. Du vil bruge denne journal-fil til at oprette et job til import fra [Azure klassisk Portal](https://manage.windowsazure.com).

## <a name="create-an-import-job"></a>Oprette et importjob

Nu kan du oprette et job til import af ved hjælp af vejledningen på [Ved hjælp af Azure Importér/Eksportér Service](../storage/storage-import-export-service.md) (under afsnittet **oprette importjobbet** ). Denne import sagen, med andre detaljer, give også journal-fil, der er oprettet under klargøringen diskdrev.

## <a name="physically-ship-the-disks"></a>Fysisk levere diskene

Nu kan du fysisk levere disk, der et Azure datacenter, hvor dataene er kopieret til Azure-lager BLOB du har angivet, mens du opretter importjobbet. Også, mens du opretter sag, hvis du har valgt for at give sporingsoplysningerne senere, du kan nu gå tilbage til import tingene og opdateret sporingsnummeret.

## <a name="copy-data-from-azure-storage-blobs-to-azure-data-lake-store"></a>Kopiere data fra Azure-lager BLOB til Azure sø datalager

Når status for importjobbet viser færdige, kan du kontrollere, om dataene er tilgængelig i Azure lagerplads BLOB du har angivet. Du kan derefter bruge en række forskellige metoder til at flytte data fra Azure-lager BLOB til Azure Data sø Store. Du kan finde alle de tilgængelige indstillinger for overførsel af data, [Ingesting data til sø datalager](data-lake-store-data-scenarios.md#ingest-data-into-data-lake-store).

I dette afsnit giver vi dig med de JSON definitioner, som du kan bruge til at oprette en Azure Data Factory pipeline til kopiering af data. Du kan bruge disse JSON definitioner fra [Azure-portalen](../data-factory/data-factory-copy-activity-tutorial-using-azure-portal.md) eller [Visual Studio](../data-factory/data-factory-copy-activity-tutorial-using-visual-studio.md) eller [Azure PowerShell](../data-factory/data-factory-copy-activity-tutorial-using-powershell.md).

### <a name="source-linked-service-azure-storage-blob"></a>Kilde sammenkædet Service (Azure-lager Blob)

````
{
    "name": "AzureStorageLinkedService",
    "properties": {
        "type": "AzureStorage",
        "description": "",
        "typeProperties": {
            "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
        }
    }
}
````

### <a name="target-linked-service-azure-data-lake-store"></a>Målret sammenkædede Service (Azure sø datalager)

````
{
    "name": "AzureDataLakeStoreLinkedService",
    "properties": {
        "type": "AzureDataLakeStore",
        "description": "",
        "typeProperties": {
            "authorization": "<Click 'Authorize' to allow this data factory and the activities it runs to access this Data Lake Store with your access rights>",
            "dataLakeStoreUri": "https://<adls_account_name>.azuredatalakestore.net/webhdfs/v1",
            "sessionId": "<OAuth session id from the OAuth authorization session. Each session id is unique and may only be used once>"
        }
    }
}
````
### <a name="input-data-set"></a>Indtast datasæt
````
{
    "name": "InputDataSet",
    "properties": {
        "published": false,
        "type": "AzureBlob",
        "linkedServiceName": "AzureStorageLinkedService",
        "typeProperties": {
            "folderPath": "importcontainer/vf1/"
        },
        "availability": {
            "frequency": "Hour",
            "interval": 1
        },
        "external": true,
        "policy": {}
    }
}
````
### <a name="output-data-set"></a>Output datasæt
````
{
"name": "OutputDataSet",
"properties": {
  "published": false,
  "type": "AzureDataLakeStore",
  "linkedServiceName": "AzureDataLakeStoreLinkedService",
  "typeProperties": {
    "folderPath": "/importeddatafeb8job/"
    },
  "availability": {
    "frequency": "Hour",
    "interval": 1
    }
  }
}
````
### <a name="pipeline-copy-activity"></a>Pipeline (kopi aktivitet)
````
{
    "name": "CopyImportedData",
    "properties": {
        "description": "Pipeline with copy activity",
        "activities": [
            {
                "type": "Copy",
                "typeProperties": {
                    "source": {
                        "type": "BlobSource"
                    },
                    "sink": {
                        "type": "AzureDataLakeStoreSink",
                        "copyBehavior": "PreserveHierarchy",
                        "writeBatchSize": 0,
                        "writeBatchTimeout": "00:00:00"
                    }
                },
                "inputs": [
                    {
                        "name": "InputDataSet"
                    }
                ],
                "outputs": [
                    {
                        "name": "OutputDataSet"
                    }
                ],
                "policy": {
                    "timeout": "01:00:00",
                    "concurrency": 1
                },
                "scheduler": {
                    "frequency": "Hour",
                    "interval": 1
                },
                "name": "AzureBlobtoDataLake",
                "description": "Copy Activity"
            }
        ],
        "start": "2016-02-08T22:00:00Z",
        "end": "2016-02-08T23:00:00Z",
        "isPaused": false,
        "pipelineMode": "Scheduled"
    }
}
````
Du kan finde flere oplysninger om, hvordan du bruger Azure Data Factory til at flytte data fra Azure-lager Blob og Azure sø datalager, kan du se [flytte data fra Azure lagerplads Blob til Azure Data sø Store ved hjælp af Azure Data Factory](../data-factory/data-factory-azure-datalake-connector.md#sample-copy-data-from-azure-blob-to-azure-data-lake-store).

## <a name="reconstruct-the-data-files-in-azure-data-lake-store"></a>Rekonstruere datafiler i Azure sø datalager

Vi i gang med en fil, som blev 319GB i størrelse og brudt den i filer i mindre, så den kan overføres ved hjælp af tjenesten Azure Importér/Eksportér. Nu, hvor dataene er i Azure sø datalager, kan vi reconstrcut filen til dets oprindelige størrelse. Du kan bruge følgende Azure PowerShell-cmldts for at gøre dette.

````
# Login to our account
Login-AzureRmAccount

# List your subscriptions
Get-AzureRmSubscription

# Switch to the subscription you want to work with
Set-AzureRmContext –SubscriptionId
Register-AzureRmResourceProvider -ProviderNamespace "Microsoft.DataLakeStore"

# Join  the files
Join-AzureRmDataLakeStoreItem -AccountName "<adls_account_name" -Paths "/importeddatafeb8job/319GB.tsv-part-aa","/importeddatafeb8job/319GB.tsv-part-ab", "/importeddatafeb8job/319GB.tsv-part-ac", "/importeddatafeb8job/319GB.tsv-part-ad" -Destination "/importeddatafeb8job/MergedFile.csv”
````

## <a name="next-steps"></a>Næste trin

- [Sikre data i sø datalager](data-lake-store-secure-data.md)
- [Bruge Azure Data sø Analytics med sø datalager](../data-lake-analytics/data-lake-analytics-get-started-portal.md)
- [Bruge Azure HDInsight med sø datalager](data-lake-store-hdinsight-hadoop-use-portal.md)
