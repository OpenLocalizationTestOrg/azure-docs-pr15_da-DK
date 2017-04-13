<properties
   pageTitle="Indlæse data med Azure Data Factory | Microsoft Azure"
   description="Lær, hvordan du indlæser data med Azure Data Factory"
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="twounder"
   manager="barbkess"
   editor=""
   tags="azure-sql-data-warehouse"/>
<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="10/13/2016"
   ms.author="mausher;barbkess"/>

# <a name="load-data-with-azure-data-factory"></a>Indlæse Data med Azure Data Factory 

> [AZURE.SELECTOR]
- [Redgate](sql-data-warehouse-load-with-redgate.md)  
- [Data Factory](sql-data-warehouse-get-started-load-with-azure-data-factory.md)  
- [PolyBase](sql-data-warehouse-get-started-load-with-polybase.md)  
- [BCP](sql-data-warehouse-load-with-bcp.md)  

Dette selvstudium viser, hvordan du opretter en rørledning i Azure Data Factory til at flytte data fra Azure-lager Blob til Azure SQL Data Warehouse. Du skal gøre følgende med følgende trin:

+ Konfigurere eksempeldata i en Azure-lager Blob.
+ Oprette forbindelse ressourcer til Azure Data Factory.
+ Oprette en pipeline for at flytte data fra BLOB-lager til SQL Data Warehouse.

>[AZURE.VIDEO loading-azure-sql-data-warehouse-with-azure-data-factory]


## <a name="before-you-begin"></a>Inden du går i gang

Hvis du vil lære at kende Azure Data Factory, kan du se [Introduktion til Azure Data Factory][].

### <a name="create-or-identify-resources"></a>Opret eller Identificer ressourcer

Før du starter selvstudiet, skal du have følgende ressourcer:

   + **Azure lagerplads Blob**: dette selvstudium bruger Azure lagerplads Blob som datakilde til Azure Data Factory pipeline, og så skal du have en tilgængelig til at gemme eksempeldataene. Hvis du ikke allerede har et, kan du se Sådan [Opret en lagerplads konto][].

   + **SQL Data Warehouse**: dette selvstudium flytter data fra Azure-lager Blob til SQL Data Warehouse og så skal have et datawarehouse online, der er indlæst med eksempeldata AdventureWorksDW. Hvis du ikke allerede har et datalager, kan du se hvordan du [Klargør et][Create a SQL Data Warehouse]. Hvis du har et datawarehouse, men ikke klargøre det med eksempeldataene, kan du [indlæse den manuelt][Load sample data into SQL Data Warehouse].

   + **Azure Data Factory**: Azure Data Factory afsluttes den aktuelle belastning, og så skal du have en, du kan bruge til at oprette data bevægelse pipeline. Hvis du ikke allerede har et, se, hvordan du opretter et i trin 1 i [Introduktion til Azure Data Factory (Data Factory Editor)][].

   + **AZCopy**: du har brug for AZCopy at kopiere eksempeldataene fra din lokale klient til din Azure lagerplads Blob. Du kan finde installationsanvisningerne, [AZCopy dokumentation][].

## <a name="step-1-copy-sample-data-to-azure-storage-blob"></a>Trin 1: Kopiér eksempeldata til Azure-lager Blob

Når du har alle de enheder, der er klar, er du klar til at kopiere eksempeldata til din Azure lagerplads Blob.

1. [Hente eksempeldata][]. Disse data føjer en anden tre år salgsdata til dine AdventureWorksDW eksempeldata.

2. Brug denne AZCopy-kommando til at kopiere de tre år af data til din Azure lagerplads Blob.

    ````
    AzCopy /Source:<Sample Data Location>  /Dest:https://<storage account>.blob.core.windows.net/<container name> /DestKey:<storage key> /Pattern:FactInternetSales.csv
    ````


## <a name="step-2-connect-resources-to-azure-data-factory"></a>Trin 2: Oprette forbindelse ressourcer til Azure Data Factory

Nu, hvor dataene er på plads kan vi oprette Azure Data Factory pipeline for at flytte data fra Azure blob-lager i SQL Data Warehouse.

Åbn [Azure-portalen][] , og vælg dine data factory fra menuen til venstre for at komme i gang skal.

### <a name="step-21-create-linked-service"></a>Trin 2.1: Oprette sammenkædede Service

Link din Azure lagerplads konto og SQL Data Warehouse til dine data factory.  

1. Først skal starte registreringsprocessen ved at klikke på sektionen 'Sammenkædede Services' af dine data factory, og klik derefter på "nye data gemmes." Vælg et navn for at registrere dit azure-lager under Vælg Azure-lager, mens du skriver, og angiv derefter dit kontonavn og Kontonøgle.

2. Hvis du vil registrere gå SQL Data Warehouse til sektionen 'Forfatter og implementer', vælg 'Ny datalager' og 'Azure SQL Data Warehouse'. Kopiere og sætte ind i denne skabelon, og udfyld derefter din specifikke oplysninger.

    ```JSON
    {
        "name": "<Linked Service Name>",
        "properties": {
            "description": "",
            "type": "AzureSqlDW",
            "typeProperties": {
                 "connectionString": "Data Source=tcp:<server name>.database.windows.net,1433;Initial Catalog=<server name>;Integrated Security=False;User ID=<user>@<servername>;Password=<password>;Connect Timeout=30;Encrypt=True"
             }
        }
    }
    ```

### <a name="step-22-define-the-dataset"></a>Trin 2.2: Definere datasættet

Når du har oprettet de sammenkædede tjenester, har vi definere datasættene.  Det betyder her definerer strukturen af de data, der skal flyttes fra din lagerplads til dit datawarehouse.  Du kan læse mere om oprettelse af

1. Starte denne proces ved at gå til afsnittet 'Forfatter og implementer' i dine data factory.

2. Klik på 'Nye datasæt' og 'Azure Blob-lager' linke din lagerplads til dine data factory.  Du kan bruge den under script til at definere dine data i Azure Blob-lager:

    ```JSON
    {
        "name": "<Dataset Name>",
        "properties": {
            "type": "AzureBlob",
            "linkedServiceName": "<linked storage name>",
            "typeProperties": {
                "folderPath": "<containter name>",
                "fileName": "FactInternetSales.csv",
                "format": {
                "type": "TextFormat",
                "columnDelimiter": ",",
                "rowDelimiter": "\n"
                }
            },
            "external": true,
            "availability": {
                "frequency": "Hour",
                "interval": 1
            },
            "policy": {
                "externalData": {
                    "retryInterval": "00:01:00",
                    "retryTimeout": "00:10:00",
                    "maximumRetry": 3
                }
            }
        }
    }
    ```

3. Nu definere vi vores datasæt for SQL Data Warehouse. Vi starter på samme måde, ved at klikke på 'Nye datasæt' og 'Azure SQL Data Warehouse'.

    ```JSON
    {
        "name": "DWDataset",
        "properties": {
            "type": "AzureSqlDWTable",
            "linkedServiceName": "AzureSqlDWLinkedService",
            "typeProperties": {
                "tableName": "FactInternetSales"
            },
            "availability": {
                "frequency": "Hour",
                "interval": 1
            }
        }
    }
    ```

## <a name="step-3-create-and-run-your-pipeline"></a>Trin 3: Oprette og køre din pipeline

Til sidst skal vi konfigurere og køre rørledningen i Azure Data Factory.  Dette er den handling, som er fuldført faktiske data bevægelse.  Du kan finde en fuld visning af de handlinger, som du kan udføre med SQL Data Warehouse og Azure Data Factory [her][Move data to and from Azure SQL Data Warehouse using Azure Data Factory].

Klik på 'Flere kommandoer' og 'Nye Pipeline' i sektionen 'Forfatter og implementer'.  Når du opretter rørledningen, kan du bruge den under koden for at overføre data til dit datawarehouse:

```JSON
{
    "name": "<Pipeline Name>",
    "properties": {
        "description": "<Description>",
        "activities": [
          {
            "type": "Copy",
            "typeProperties": {
                "source": {
                    "type": "BlobSource",
                    "skipHeaderLineCount": 1
                },
                "sink": {
                    "type": "SqlDWSink",
                    "writeBatchSize": 0,
                    "writeBatchTimeout": "00:00:10"
                }
            },
            "inputs": [
              {
                "name": "<Storage Dataset>"
              }
            ],
            "outputs": [
              {
                "name": "<Data Warehouse Dataset>"
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
            "name": "Sample Copy",
            "description": "Copy Activity"
          }
        ],
        "start": "<Date YYYY-MM-DD>",
        "end": "<Date YYYY-MM-DD>",
        "isPaused": false
    }
}
```

## <a name="next-steps"></a>Næste trin

Start for at få mere for at vide ved at få vist:

- [Azure Data Factory læringssti][].
- [Azure SQL-Data lager forbindelse][]. Dette er emnet core reference for Azure Data Factory med Azure SQL Data Warehouse.


Disse emner indeholder detaljerede oplysninger om Azure Data Factory. De diskutere Azure SQL-Database eller HDInsight, men oplysningerne gælder også for Azure SQL Data Warehouse.

- [Selvstudium: Introduktion til Azure Data Factory][] Dette er det grundlæggende selvstudium til at behandle data med Azure Data Factory. I dette selvstudium skal du oprette din første pipeline, der bruger HDInsight til at transformere og analysere web logge på månedsbasis. Bemærk, at der ikke er nogen kopi aktivitet i dette selvstudium.
- [Selvstudium: kopiere data fra Azure-lager Blob til Azure SQL-Database][]. I dette selvstudium, kan du oprette en rørledning i Azure Data Factory at kopiere data fra Azure-lager Blob til Azure SQL-Database.

<!--Image references-->

<!--Article references-->
[AZCopy dokumentation]: ../storage/storage-use-azcopy.md
[Azure SQL Data Warehouse Connector]: ../data-factory/data-factory-azure-sql-data-warehouse-connector.md
[BCP]: sql-data-warehouse-load-with-bcp.md
[Create a SQL Data Warehouse]: sql-data-warehouse-get-started-provision.md
[Oprette en lagerplads-konto]: ../storage/storage-create-storage-account.md#create-a-storage-account
[Data Factory]: sql-data-warehouse-get-started-load-with-azure-data-factory.md
[Introduktion til Azure Data Factory (Data Factory Editor)]: ../data-factory/data-factory-build-your-first-pipeline-using-editor.md
[Introduktion til Azure Data Factory]: ../data-factory/data-factory-introduction.md
[Load sample data into SQL Data Warehouse]: sql-data-warehouse-load-sample-databases.md
[Move data to and from Azure SQL Data Warehouse using Azure Data Factory]: ../data-factory/data-factory-azure-sql-data-warehouse-connector.md
[PolyBase]: sql-data-warehouse-get-started-load-with-polybase.md
[Selvstudium: Kopiere data fra Azure-lager Blob til Azure SQL-Database]: ../data-factory/data-factory-copy-data-from-azure-blob-storage-to-sql-database.md
[Selvstudium: Introduktion til Azure Data Factory]: ../data-factory/data-factory-build-your-first-pipeline.md

<!--MSDN references-->

<!--Other Web references-->
[Azure Data Factory læringssti]: https://azure.microsoft.com/documentation/learning-paths/data-factory
[Azure-portalen]: https://portal.azure.com
[Hente eksempeldata]: https://migrhoststorage.blob.core.windows.net/adfsample/FactInternetSales.csv
