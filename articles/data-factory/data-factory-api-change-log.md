<properties 
    pageTitle="Data Factory - .NET API ændringslog | Microsoft Azure" 
    description="I denne artikel beskrives seneste ændringer, funktionstilføjelser, fejlrettelser osv... i en bestemt version af .NET API til Azure Data Factory." 
    services="data-factory" 
    documentationCenter="" 
    authors="spelluru" 
    manager="jhubbard" 
    editor="monicar"/>

<tags 
    ms.service="data-factory" 
    ms.workload="data-services" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="09/21/2016" 
    ms.author="spelluru"/>

# <a name="azure-data-factory---net-api-change-log"></a>Azure Data Factory - ændringslog for .NET API 
I denne artikel indeholder oplysninger om ændringer til Azure Data Factory SDK i en bestemt version. Du kan finde den seneste NuGet pakke til Azure Data Factory [her](https://www.nuget.org/packages/Microsoft.Azure.Management.DataFactories) 

## <a name="version-4110"></a>Version 4.11.0
Funktionstilføjelser:

- Følgende sammenkædede typer har tilføjet:
    - [OnPremisesMongoDbLinkedService](https://msdn.microsoft.com/library/mt765129.aspx)
    - [AmazonRedshiftLinkedService](https://msdn.microsoft.com/library/mt765121.aspx)
    - [AwsAccessKeyLinkedService](https://msdn.microsoft.com/library/mt765144.aspx)
- Følgende typer datasæt har tilføjet: 
    - [MongoDbCollectionDataset](https://msdn.microsoft.com/library/mt765145.aspx)
    - [AmazonS3Dataset](https://msdn.microsoft.com/library/mt765112.aspx)
- Følgende Kopiér source typer har tilføjet:
    - [MongoDbSource](https://msdn.microsoft.com/en-US/library/mt765123.aspx)

## <a name="version-4100"></a>Version 4.10.0
- Følgende valgfrie egenskaber er blevet føjet til tekstformat:
    - [SkipLineCount](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.models.textformat.skiplinecount.aspx)
    - [Første række som overskrift](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.models.textformat.firstrowasheader.aspx)
    - [TreatEmptyAsNull](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.models.textformat.treatemptyasnull.aspx)
- Følgende sammenkædede typer har tilføjet:
    - [OnPremisesCassandraLinkedService](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.models.onpremisescassandralinkedservice.aspx)
    - [SalesforceLinkedService](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.models.salesforcelinkedservice.aspx)
- Følgende typer datasæt har tilføjet:
    - [OnPremisesCassandraTableDataset](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.models.onpremisescassandratabledataset.aspx)
- Følgende Kopiér source typer har tilføjet:
    - [CassandraSource](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.models.cassandrasource.aspx)
- Føje [WebServiceInputs](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.models.azuremlbatchexecutionactivity.webserviceinputs.aspx) egenskab til AzureMLBatchExecutionActivity 
    - Aktivere, der passerer flere web service input til Azure Machine Learning forsøg


## <a name="version-491"></a>Version 4.9.1

### <a name="bug-fix"></a>Rettelse

- Fraråde WebApi-baseret godkendelse for [WebLinkedService](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.models.weblinkedservice.authenticationtype.aspx).

## <a name="version-490"></a>Version 4.9.0

### <a name="feature-additions"></a>Funktionstilføjelser

- Føje [EnableStaging](https://msdn.microsoft.com/library/mt767916.aspx) og [StagingSettings](https://msdn.microsoft.com/library/mt767918.aspx) egenskaber til CopyActivity. Du kan finde oplysninger om funktionen i [faseinddelt kopi](data-factory-copy-activity-performance.md#staged-copy) .


### <a name="bug-fix"></a>Rettelse

- Introducere overbelastning af [ActivityWindowOperationExtensions.List](https://msdn.microsoft.com/library/mt767915.aspx) metoden, som tager en [ActivityWindowsByActivityListParameters](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.activitywindowsbyactivitylistparameters.aspx) forekomst.
- Markér [WriteBatchSize](https://msdn.microsoft.com/library/dn884293.aspx) og [WriteBatchTimeout](https://msdn.microsoft.com/library/dn884245.aspx) som valgfri i CopySink.

## <a name="version-480"></a>Version 4.8.0

### <a name="feature-additions"></a>Funktionstilføjelser
- Følgende valgfrie egenskaber er blevet føjet til kopi aktivitet Skriv for at aktivere Justering af ydeevnen i kopi:
    - [ParallelCopies](https://msdn.microsoft.com/library/mt767910.aspx)
    - [CloudDataMovementUnits](https://msdn.microsoft.com/library/mt767912.aspx)

## <a name="version-470"></a>Version 4.7.0

### <a name="feature-additions"></a>Funktionstilføjelser
* Tilføjet nye StorageFormat typen [OrcFormat](https://msdn.microsoft.com/library/mt723391.aspx) til at kopiere filer i optimerede række kolonne (ORC)-format.
* Føje [AllowPolyBase](https://msdn.microsoft.com/library/mt723396.aspx) og PolyBaseSettings egenskaber til SqlDWSink.
    * Aktiverer brug af PolyBase til at kopiere data til SQL Data Warehouse.

## <a name="version-461"></a>Version 4.6.1

### <a name="bug-fixes"></a>Fejlrettelser
* Løser HTTP-anmodning om listen over aktivitet i windows.
    * Fjerner ressource gruppenavn og data factory navnet fra anmodning om data.

## <a name="version-460"></a>Version 4.6.0

### <a name="feature-additions"></a>Funktionstilføjelser

- Følgende egenskaber er blevet føjet til [PipelineProperties](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.pipelineproperties_properties.aspx):
    - [PipelineMode](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.pipelineproperties.pipelinemode.aspx)
    - [ExpirationTime](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.pipelineproperties.expirationtime.aspx)
    - [Datasæt](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.pipelineproperties.datasets.aspx)
- Følgende egenskaber er blevet føjet til [PipelineRuntimeInfo](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.common.models.pipelineruntimeinfo.aspx):
    - [PipelineState](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.common.models.pipelineruntimeinfo.pipelinestate.aspx)
- Tilføjede nye [StorageFormat](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.models.storageformat.aspx) Skriv [JsonFormat](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.models.jsonformat.aspx) type til at definere datasæt, hvis dataene findes i JSON-formatet. 

## <a name="version-450"></a>Version 4.5.0

### <a name="feature-additions"></a>Funktionstilføjelser
* Tilføjede [listen handlinger for aktivitet vindue](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.activitywindowoperationsextensions.aspx).
    * Tilføjede metoder til at hente aktivitet windows med filtre, der er baseret på objekttyper (det vil sige, data fabrikker, datasæt, rørledninger og aktiviteter).
* Følgende sammenkædede typer har tilføjet: 
    * [ODataLinkedService](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.odatalinkedservice.aspx), [WebLinkedService](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.weblinkedservice.aspx)
* Følgende typer datasæt har tilføjet: 
    * [ODataResourceDataset](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.odataresourcedataset.aspx), [WebTableDataset](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.webtabledataset.aspx)
* Følgende Kopiér source typer har tilføjet:  
    * [WebSource](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.websource.aspx)

## <a name="version-440"></a>Version 4.4.0

### <a name="feature-additions"></a>Funktionstilføjelser

- Den følgende sammenkædede tjenestetype er blevet tilføjet som datakilder og sinks for kopi aktiviteter:
    - [AzureStorageSasLinkedService](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.models.azurestoragesaslinkedservice.aspx). Du kan se [Azure lagerplads SAS sammenkædede Service](data-factory-azure-blob-connector.md#azure-storage-sas-linked-service) til grundlæggende oplysninger og eksempler. 

## <a name="version-430"></a>Version 4.3.0

### <a name="feature-additions"></a>Funktionstilføjelser

- Følgende sammenkædede service typer haven blevet tilføjet som datakilder for kopi aktiviteter:
    - [HdfsLinkedService](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.models.hdfslinkedservice.aspx). Se [flytte data fra HDFS ved hjælp af Data Factory](data-factory-hdfs-connector.md) til grundlæggende oplysninger og eksempler. 
    - [OnPremisesOdbcLinkedService](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.models.onpremisesodbclinkedservice.aspx). Se [flytte data fra ODBC-data gemmes ved hjælp af Azure Data Factory](data-factory-odbc-connector.md) til grundlæggende oplysninger og eksempler. 

## <a name="version-420"></a>Version 4.2.0

### <a name="feature-additions"></a>Funktionstilføjelser

- Følgende nye aktivitetstype er blevet tilføjet: [AzureMLUpdateResourceActivity](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.azuremlupdateresourceactivity.aspx). Du kan finde oplysninger om aktiviteten, [ved at opdatere Azure ML modellerne ved hjælp af den opdatering ressource aktivitet](data-factory-azure-ml-batch-execution-activity.md#updating-azure-ml-models-using-the-update-resource-activity).
- En ny valgfri egenskab [updateResourceEndpoint](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.azuremllinkedservice.updateresourceendpoint.aspx) er blevet føjet til [AzureMLLinkedService klasse](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.azuremllinkedservice.aspx). 
- Egenskaber for [LongRunningOperationInitialTimeout](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.datafactorymanagementclient.longrunningoperationinitialtimeout.aspx) og [LongRunningOperationRetryTimeout](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.datafactorymanagementclient.longrunningoperationretrytimeout.aspx) er blevet føjet til klassen [DataFactoryManagementClient](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.datafactorymanagementclient.aspx) . 
- Tillad, at konfigurationen af timeout for klient opkald til Data Factory-tjenesten. 


## <a name="version-410"></a>Version 4.1.0

### <a name="feature-additions"></a>Funktionstilføjelser
* Følgende sammenkædede typer har tilføjet: 
    * [AzureDataLakeStoreLinkedService](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.azuredatalakestorelinkedservice.aspx)
    * [AzureDataLakeAnalyticsLinkedService](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.azuredatalakeanalyticslinkedservice.aspx)
* Følgende aktivitetstyper har tilføjet: 
    * [DataLakeAnalyticsUSQLActivity](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.datalakeanalyticsusqlactivity.aspx)
* Følgende typer datasæt har tilføjet: 
    * [AzureDataLakeStoreDataset](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.azuredatalakestoredataset.aspx)
* De følgende kilde- og sink typer for kopi aktivitet har tilføjet:
    * [AzureDataLakeStoreSource](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.azuredatalakestoresource.aspx)
    * [AzureDataLakeStoreSink](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.azuredatalakestoresink.aspx)


## <a name="version-401"></a>Version 4.0.1

### <a name="breaking-changes"></a>Bryd ændringer
Følgende klasser er blevet omdøbt. De nye navne var de oprindelige navne på klasser, før 4.0.0 slipper. 
 
Navngive i 4.0.0 | Navngive i 4.0.1
:------------ | :------------ 
AzureSqlDataWarehouseDataset | [AzureSqlDataWarehouseTableDataset](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.azuresqldatawarehousetabledataset.aspx)
AzureSqlDataset | [AzureSqlTableDataset](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.azuresqltabledataset.aspx)
AzureDataset | [AzureTableDataset](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.azuretabledataset.aspx)
OracleDataset | [OracleTableDataset](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.oracletabledataset.aspx)
RelationalDataset | [RelationalTableDataset](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.relationaltabledataset.aspx)
SqlServerDataset | [SqlServerTableDataset](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.sqlservertabledataset.aspx)


## <a name="version-400"></a>Version 4.0.0

### <a name="breaking-changes"></a>Bryd ændringer



- De følgende klasser/grænseflader er blevet omdøbt.

| Gamle navn | Nyt navn |
| :-------- | :-------- |
| ITableOperations | [IDatasetOperations](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.idatasetoperations.aspx) |  
| Tabel | [Datasæt](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.dataset.aspx) | 
| TableProperties | [DatasetProperties](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.datasetproperties.aspx) | 
| TableTypeProprerties | [DatasetTypeProperties](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.datasettypeproperties.aspx) |
| TableCreateOrUpdateParameters | [DatasetCreateOrUpdateParameters](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.datasetcreateorupdateparameters.aspx) | 
| TableCreateOrUpdateResponse | [DatasetCreateOrUpdateResponse](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.datasetcreateorupdateresponse.aspx) | 
| TableGetResponse | [DatasetGetResponse](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.datasetgetresponse.aspx) | 
| TableListResponse | [DatasetListResponse](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.datasetlistresponse.aspx) |
| CreateOrUpdateWithRawJsonContentParameters | [DatasetCreateOrUpdateWithRawJsonContentParameters](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.datasetcreateorupdatewithrawjsoncontentparameters.aspx) | 
    

- Metoderne **liste** resultater sideinddelt nu. Hvis svaret indeholder en ikke-tomme **NextLink** egenskab, skal klientprogrammet fortsat henter den næste side, indtil alle sider der skal returneres.  Her er et eksempel: 

        PipelineListResponse response = client.Pipelines.List("ResourceGroupName", "DataFactoryName");
        var pipelines = new List<Pipeline>(response.Pipelines);
    
        string nextLink = response.NextLink;
        while (string.IsNullOrEmpty(response.NextLink))
        {
            PipelineListResponse nextResponse = client.Pipelines.ListNext(nextLink);
            pipelines.AddRange(nextResponse.Pipelines);
    
            nextLink = nextResponse.NextLink;
        }
    
- **Listen** pipeline API returnerer kun oversigt over en rørledning i stedet for at få flere detaljer. For eksempel indeholder aktiviteter i en oversigt over pipeline kun navn og type.

### <a name="feature-additions"></a>Funktionstilføjelser
- Klassen [SqlDWSink](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.models.sqldwsink.aspx) understøtter to nye egenskaber, **SliceIdentifierColumnName** og **SqlWriterCleanupScript**, til at understøtte idempotent Kopiér til Azure SQL Data Warehouse. Artiklen [Azure SQL Data Warehouse](data-factory-azure-sql-data-warehouse-connector.md) , specifikt [ordning 1](data-factory-azure-sql-data-warehouse-connector.md#mechanism-1) og [ordning 2](data-factory-azure-sql-data-warehouse-connector.md#mechanism-2) sektioner, få mere at vide om disse egenskaber.

- Vi understøtter nu kører lagret procedure mod Azure SQL-Database og Azure SQL Data Warehouse kilder som en del af den kopi aktivitet. [SqlSource](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.models.sqlsource.aspx) og [SqlDWSource](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.models.sqldwsource.aspx) klasser har følgende egenskaber: **SqlReaderStoredProcedureName** og **StoredProcedureParameters**. Se artiklerne [Azure SQL-Database](data-factory-azure-sql-connector.md#sqlsource) og [Azure SQL Data Warehouse](data-factory-azure-sql-data-warehouse-connector.md#sqldwsource) på Azure.com få mere at vide om disse egenskaber.  