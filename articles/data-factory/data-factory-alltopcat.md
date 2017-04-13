<properties
    pageTitle="Alle emner til Data Factory-tjenesten | Microsoft Azure"
    description="Liste over alle emner til Azure-tjenesten med navnet Data Factory, der findes på http://azure.microsoft.com/documentation/articles/, titel og beskrivelse."
    services="data-factory"
    documentationCenter=""
    authors="spelluru"
    manager="jhubbard"
    editor="MightyPen"/>

<tags
    ms.service="data-factory"
    ms.workload="data-factory"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/05/2016"
    ms.author="spelluru"/>


# <a name="all-topics-for-azure-data-factory-service"></a>Alle emner til Azure Data Factory-tjenesten

I dette emne beskrives hver emne, der gælder direkte til **Data Factory** -tjenesten for den Azure. Du kan søge denne webside til nøgleord ved hjælp af **Ctrl + F**, søge efter emner af aktuelle interesse.




## <a name="new"></a>Ny

| &nbsp; | Titel | Beskrivelse |
| --: | :-- | :-- |
| 1 | [Flytte data fra Amazon Redshift ved hjælp af Azure Data Factory](data-factory-amazon-redshift-connector.md) | Få mere at vide om, hvordan til at flytte data fra Amazon Redshift ved hjælp af Azure Data Factory. |
| 2 | [Flytte data fra Amazon enkel lagerplads tjenesten ved hjælp af Azure Data Factory](data-factory-amazon-simple-storage-service-connector.md) | Få mere at vide om, hvordan du kan flytte data fra Amazon Simple lagerplads Service (S3) ved hjælp af Azure Data Factory. |
| 3 | [Azure Data Factory - kopi guiden](data-factory-azure-copy-wizard.md) | Få mere at vide om, hvordan du bruger guiden Data Factory Azure kopi for at kopiere data fra understøttede datakilder til dræn. |
| 4 | [Selvstudium: Oprette din første Azure data factory ved hjælp af Data Factory REST-API](data-factory-build-your-first-pipeline-using-rest-api.md) | I dette selvstudium, kan du oprette en stikprøve Azure Data Factory rørledning, ved hjælp af Data Factory REST-API. |
| 5 | [Selvstudium: Oprette en rørledning med kopi aktivitet ved hjælp af .NET API](data-factory-copy-activity-tutorial-using-dotnet-api.md) | I dette selvstudium oprette du en Azure Data Factory rørledning med en kopi aktivitet ved hjælp af .NET API. |
| 6 | [Selvstudium: Oprette en rørledning med kopi aktivitet ved hjælp af REST-API](data-factory-copy-activity-tutorial-using-rest-api.md) | I dette selvstudium oprette du en Azure Data Factory rørledning med en kopi aktivitet ved hjælp af REST-API. |
| 7 | [Guiden data Factory kopi](data-factory-copy-wizard.md) | Få mere at vide om, hvordan du bruger guiden kopi Factory til at kopiere data fra understøttede datakilder til dræn. |
| 8 | [Datastyringsgateway](data-factory-data-management-gateway.md) | Konfigurere en datastyringsgateway til at flytte data mellem lokale miljø og skyen. Brug af Datastyringsgateway i Azure Data Factory til at flytte dine data. |
| 9 | [Flytte data fra en lokal Cassandra-database ved hjælp af Azure Data Factory](data-factory-onprem-cassandra-connector.md) | Få mere at vide om, hvordan til at flytte data fra en lokal Cassandra-database ved hjælp af Azure Data Factory. |
| 10 | [Flytte data fra MongoDB ved hjælp af Azure Data Factory](data-factory-on-premises-mongodb-connector.md) | Få mere at vide om, hvordan til at flytte data fra MongoDB database ved hjælp af Azure Data Factory. |
| 11 | [Flytte data fra Salesforce ved hjælp af Azure Data Factory](data-factory-salesforce-connector.md) | Få mere at vide om, hvordan til at flytte data fra Salesforce ved hjælp af Azure Data Factory. |


## <a name="updated-articles-data-factory"></a>Opdaterede artikler, Data Factory

I dette afsnit beskrives artikler, som er blevet opdateret for nylig, hvor opdateringen blev stor eller betydeligt. For hver opdaterede artikel vises en grov kodestykke den tilføjede tabsbeløb teksts. Artiklerne er blevet opdateret inden for datointervallet af **2016-08-22** til **2016-10-05**.

| &nbsp; | Artikel | Tekstopdateringer, kodestykke | Opdateret, da |
| --: | :-- | :-- | :-- |
| 12 | [Azure Data Factory - ændringslog for .NET API](data-factory-api-change-log.md) | I denne artikel indeholder oplysninger om ændringer til Azure Data Factory SDK i en bestemt version. Du kan finde den seneste NuGet pakke til Azure Data Factory her (https://www.nuget.org/packages/Microsoft.Azure.Management.DataFactories) **Version 4.11.0** funktionstilføjelser: / følgende sammenkædede typer er blevet tilføjet: - OnPremisesMongoDbLinkedService (https://msdn.microsoft.com/library/mt765129.aspx) - AmazonRedshiftLinkedService (https://msdn.microsoft.com/library/mt765121.aspx) - AwsAccessKeyLinkedService (https://msdn.microsoft.com/library/mt765144.aspx) / følgende typer datasæt er blevet tilføjet: - MongoDbCollectionDataset (https://msdn.microsoft.com/library/mt765145.aspx) - AmazonS3Dataset (https://msdn.microsoft.com/library/mt765112.aspx) / følgende Kopiér source typer er blevet tilføjet :-MongoDbSource (https://msdn.microsoft.com/en-US/library/mt765123.aspx) **Version 4.10.0** / følgende valgfrie egenskaber er blevet føjet til tekstformat:-samt | 2016-09-22 |
| 13 | [Flytte data til og fra Azure Blob ved hjælp af Azure Data Factory](data-factory-azure-blob-connector.md) |  / copyBehavior / definerer adfærden kopi, når kilden er BlobSource eller filsystemet.  / **PreserveHierarchy:** bevarer hierarkiet fil i destinationsmappen. Den relative sti af kildefilen til kildemappe er identisk med den relative sti i destinationsfilen til destinationsmappe.. br /.. br /. **FlattenHierarchy:** alle filer fra kildemappen er i det første niveau af destinationsmappen. Mål-filerne indeholder automatisk genereret navn. .BR /.. br /. **MergeFiles: (standard)** flettes alle filer fra kildemappen til en fil. Hvis navnet på filen/Blob er angivet, ville flettede filnavnet være det angivne navn ellers ville være automatisk oprettede filnavn.  / Ikke / **BlobSource** understøtter også disse to egenskaber for at opnå bagudkompatibilitet. / **treatEmptyAsNull**: Angiver, om at behandle null eller en tom streng som null-værdi. / **skipHeaderLineCount** - angiver, hvor mange linjer skal være ignoreret. Det er relevant, kun, når input datasæt bruger tekstformat. På samme måde, **BlobSink** understøtter de | 2016-09-28 |
| 14 | [Oprette skønnet rørledninger ved hjælp af Azure Machine Learning aktiviteter](data-factory-azure-ml-batch-execution-activity.md) | **Webtjeneste kræver flere input** Hvis webtjenesten tager flere input, kan du bruge egenskaben **webServiceInputs** i stedet for ved hjælp af **webServiceInput**. Datasæt, der refereres til ved **webServiceInputs** skal også medtages i aktivitet **input**. I din Azure ML forsøg har web service input og output porte og globale parametre standardnavne ("input1", "input2"), som du kan tilpasse. De navne, du bruger til webServiceInputs, webServiceOutputs og globalParameters indstillinger skal svare nøjagtigt til navnene i forsøg. Du kan få vist eksempel anmodning om data på siden batchen udførelse af hjælp til din Azure ML slutpunkt at bekræfte den forventede tilknytning.    {"navn": "PredictivePipeline", "Egenskaber": {"beskrivelse": "Brug AzureML model," "aktiviteter": {"navn": "MLActivity", "type": "AzureMLBatchExecution", "beskrivelse": "forudsigelse analyse på batchen input", "input": {"navn": "inputDataset1"}, {"navn": "inputDatase | 2016-09-13 |
| 15 | [Kopiere aktivitet ydeevne og justering vejledning](data-factory-copy-activity-performance.md) | 1. **Opret en oprindelig plan**. Teste din pipeline ved hjælp af kopi aktivitet mod en stikprøve repræsentant data i udviklingsfasen. Du kan bruge den Data Factory udskæring model (data-factory-planlægnings-og-execution.md time-series-datasets-and-data-slices) til at begrænse mængden data, du arbejder med.  Indsamle kørselstid og ydeevne egenskaber ved hjælp af **overvågnings- og Management App**. Vælg **skærm og administrer** på startsiden Data Factory. Vælg **output datasæt**i træstrukturen. Vælg den kopi aktivitet, der kører på listen **Aktivitet Windows** . **Aktivitet Windows** vises kopi Aktivitetens varighed og størrelsen på de data, der er kopieret. Overførselshastigheden er angivet i **Aktivitet vinduet Stifinder**. Hvis du vil vide mere om appen, skal du se skærm og administrere Azure Data Factory rørledninger ved hjælp af overvågning og administration af App (data-factory-skærm-administrere-app.md).  ! Køre detaljer (./media/data-factory-copy-activity-performance/mmapp-activity-run-details.pn aktivitet | 2016-09-27 |
| 16 | [Selvstudium: Oprette en rørledning med kopi aktivitet ved hjælp af Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md) |   Bemærk følgende punkter:-datasæt **type** er indstillet til **AzureBlob**.     - **linkedServiceName** er indstillet til **AzureStorageLinkedService**. Du har oprettet denne sammenkædede tjeneste i trin 2.     - **mappesti** er indstillet til objektbeholderen **adftutorial** . Du kan også angive navnet på en blob i mappen ved hjælp af egenskaben **fileName** . Da du ikke angiver navnet på blob, betragtes som data fra alle BLOB i beholderen som en indtastede data.    - **type** er indstillet til **tekstformat** - der er to felter i den tekst fil ΓÇô **Fornavn** og **Efternavn** ΓÇô adskilt af et kommategn (**columnDelimiter**) - **tilgængelighed** er indstillet til **hver time** (**frekvens** er indstillet til **time** og **interval** er angivet til **1**). Derfor søger Data Factory efter inputdataene hver time i rodmappen på blob objektbeholder (**adftutorial**), du har angivet.  Hvis du ikke angiver et **filnavn** til en **input** dataset, er alle filer/BLOB fra mappen input (**mappesti**) consid | 29-09-2016 |
| 17 | [Oprette, overvåge og administrere Azure data fabrikker ved hjælp af Data Factory .NET SDK](data-factory-create-data-factories-programmatically.md) | Bemærk, at program-ID og adgangskoden (klient hemmeligt) nede og bruge det i denne gennemgang. **Få Azure-abonnement og lejer id'er** Hvis du ikke har seneste version af PowerShell, der er installeret på computeren, skal du følge vejledningen i, hvordan du installerer og konfigurerer Azure PowerShell (… / powershell-Installer-configure.md) artikel for at installere den. 1. Start Azure PowerShell og køre følgende kommando for 2. Kør følgende kommando og angive brugernavn og adgangskode, du bruger til at logge på portalen Azure.         Logon-AzureRmAccount Hvis du har kun én Azure abonnement, der er knyttet til denne konto, behøver du ikke at udføre de næste to trin. 3. Kør følgende kommando for at få vist alle abonnementer til denne konto.       Get-AzureRmSubscription 4. Kør følgende kommando for at markere det abonnement, du vil arbejde med. Erstat **NameOfAzureSubscription** med navnet på dit Azure-abonnement.       Get-AzureRmSubscription - SubscriptionName NameOfAzureSubscription / sæt AzureRmCo | 2016-09-14 |
| 18 | [Rørledninger og aktiviteter i Azure Data Factory](data-factory-create-pipelines.md) |       "start": "2016-07-12T00:00:00Z", "end": "2016-07-13T00:00:00Z"}} Bemærk følgende punkter: / i sektionen aktiviteter, der er kun en aktivitet, hvis **type** er indstillet til **kopi**. / Input for aktiviteten er indstillet til **InputDataset** og afgang for aktiviteten er indstillet til **OutputDataset**. / **BlobSource** er angivet som kildetypen i sektionen **typeProperties** og **SqlSink** er angivet som typen sink. For en komplet gennemgang af oprettelse af denne pipeline, se Selvstudium: kopiere data fra Blob-lager til SQL-Database (data-factory-copy-data-from-azure-blob-storage-to-sql-database.md). **Eksempel transformation pipeline** I følgende eksempel pipeline er der en aktivitet af typen **HDInsightHive** i sektionen **aktiviteter** . I dette eksempel transformerer HDInsight Hive aktivitet (data-factory-hive-activity.md) data fra en Azure Blob-lager ved at køre en Hive script-fil på en Azure HDInsight Hadoop-klynge.  {"navn": "TransformPipeline", "p | 2016-09-27 |
| 19 | [Transformere data i Azure Data Factory](data-factory-data-transformation-activities.md) | Data Factory understøtter følgende data transformation aktiviteter, der kan føjes til rørledninger (data-factory-oprette-pipelines.md) enten individuelt eller sammenkædet med en anden aktivitet. .  AZURE. Se Oprette en rørledning med Hive transformation (data-factory-build-your-first-pipeline.md) artikel NOTE til en gennemgang med trinvise instruktioner. **HDInsight Hive aktivitet** HDInsight Hive aktiviteten i en Data Factory pipeline udfører Hive forespørgsler på din egen eller efter behov Windows/Linux-baserede HDInsight klynge. Se Hive aktivitet (data-factory-hive-activity.md) artikel for oplysninger om denne aktivitet. **HDInsight gris aktivitet** HDInsight gris aktivitet i en Data Factory pipeline udfører gris forespørgsler på din egen eller efter behov Windows/Linux-baserede HDInsight klynge. Se gris aktivitet (data-factory-gris-activity.md) artikel for oplysninger om denne aktivitet. **HDInsight MapReduce aktivitet** HDInsight MapReduce aktiviteten i en Data Factory pipeline udfører MapReduce programmer på y | 2016-09-26 |
| 20 | [Data Factory planlægnings- og udførelse af](data-factory-scheduling-and-execution.md) | CopyActivity2 vil kun køre, hvis CopyActivity1 er udført og Dataset2 er tilgængelig. Her er eksempel rørledningen JSON: {"navn": "ChainActivities", "Egenskaber": {"beskrivelse": "Kør aktiviteter i rækkefølge", "aktiviteter": {"type": "Kopiere", "typeProperties": {"kilde": {"type": "BlobSource"}, "sink": {"type": "BlobSink", "copyBehavior": "PreserveHierarchy", "writeBatchSize": 0; "writeBatchTimeout": "00: 00:00"}}, "input": {"navn": "Dataset1"}, "skriver": {"navn": "Dataset2"}, "politik": {"timeout": "01: 00:00"}, "scheduler": {"frekvens": "Time", "interval": 1} "navn": "CopyFromBlob1ToBlob2", "beskrivelse": "Kopiere data fra en blob til en anden"}, {"type": "Kopiere", "typeProperties": {"kilde": {"type": "BlobSource"}, "sink" : {"type": "BlobSink", "writeBatchSize": 0; "writeBatchTimeout": "00: 00:00"}}, "i | 2016-09-28 |





## <a name="tutorials"></a>Selvstudier

| &nbsp; | Titel | Beskrivelse |
| --: | :-- | :-- |
| 21 | [Selvstudium: Oprette din første pipeline til proces data ved hjælp af Hadoop klynge](data-factory-build-your-first-pipeline.md) | Selvstudiet Azure Data Factory viser, hvordan du oprette og planlægge en data fabrik, der behandler data ved hjælp af Hive script på en Hadoop-klynge. |
| 22 | [Selvstudium: Oprette din første Azure data factory ved hjælp af Azure ressourcestyring skabelon](data-factory-build-your-first-pipeline-using-arm.md) | I dette selvstudium, kan du oprette en stikprøve Azure Data Factory rørledning, ved hjælp af en skabelon til Azure ressourcestyring. |
| 23 | [Selvstudium: Oprette din første Azure data factory Azure portalen](data-factory-build-your-first-pipeline-using-editor.md) | I dette selvstudium, kan du oprette en eksempel Azure Data Factory-rørledning med Data Factory redigeringsprogram i portalen Azure. |
| 24 | [Selvstudium: Oprette din første Azure data factory ved hjælp af Azure PowerShell](data-factory-build-your-first-pipeline-using-powershell.md) | I dette selvstudium, kan du oprette en eksempel Azure Data Factory-rørledning med Azure PowerShell. |
| 25 | [Selvstudium: Opbygge din Azure første data factory ved hjælp af Microsoft Visual Studio](data-factory-build-your-first-pipeline-using-vs.md) | I dette selvstudium, kan du oprette en stikprøve Azure Data Factory rørledning, ved hjælp af Visual Studio. |
| 26 | [Selvstudium: Oprette en rørledning med kopi aktivitet i portalen til Azure](data-factory-copy-activity-tutorial-using-azure-portal.md) | I dette selvstudium oprette du en Azure Data Factory rørledning med en kopi aktivitet ved hjælp af Data Factory-Editor i portalen Azure. |
| 27 | [Selvstudium: Oprette en rørledning med kopi aktiviteter med Azure PowerShell](data-factory-copy-activity-tutorial-using-powershell.md) | I dette selvstudium oprette du en Azure Data Factory rørledning med en kopi aktivitet ved hjælp af Azure PowerShell. |
| 28 | [Selvstudium: Oprette en rørledning med kopi aktivitet ved hjælp af Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md) | I dette selvstudium oprette du en Azure Data Factory rørledning med en kopi aktivitet ved hjælp af Visual Studio. |
| 29 | [Kopiér data fra Blob-lager til SQL-Database ved hjælp af Data Factory](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) | Dette selvstudium viser, hvordan du bruger kopi aktivitet i en Azure Data Factory pipeline til at kopiere data fra Blob-lager til SQL-database. |
| 30 | [Selvstudium: Oprette en rørledning med kopi aktivitet ved hjælp af guiden Data Factory kopi](data-factory-copy-data-wizard-tutorial.md) | I dette selvstudium opretter du en Azure Data Factory rørledning med en kopi aktivitet ved hjælp af guiden Kopier, der understøttes af Data Factory |



## <a name="data-movement"></a>Flytning af data

| &nbsp; | Titel | Beskrivelse |
| --: | :-- | :-- |
| 31 | [Flytte data til og fra Azure Blob ved hjælp af Azure Data Factory](data-factory-azure-blob-connector.md) | Lær at kopiere blob-data i Azure Data Factory. Brug af vores eksempel: hvordan du kopierer data til og fra Azure Blob-lager og Azure SQL-Database. |
| 32 | [Flytte data til og fra Azure Data sø Store ved hjælp af Azure Data Factory](data-factory-azure-datalake-connector.md) | Lær at flytte data til/fra Azure Data sø Store ved hjælp af Azure Data Factory |
| 33 | [Flytte data til og fra DocumentDB ved hjælp af Azure Data Factory](data-factory-azure-documentdb-connector.md) | Få mere at vide hvordan flytte data til/fra Azure DocumentDB af websteder ved hjælp af Azure Data Factory |
| 34 | [Flytte data til og fra Azure SQL-Database ved hjælp af Azure Data Factory](data-factory-azure-sql-connector.md) | Lær at flytte data til/fra Azure SQL-Database ved hjælp af Azure Data Factory. |
| 35 | [Flytte data til og fra Azure SQL Data Warehouse ved hjælp af Azure Data Factory](data-factory-azure-sql-data-warehouse-connector.md) | Lær at flytte data til/fra Azure SQL Data Warehouse ved hjælp af Azure Data Factory |
| 36 | [Flytte data til og fra Azure-tabel ved hjælp af Azure Data Factory](data-factory-azure-table-connector.md) | Lær at flytte data til/fra Azure Table Storage ved hjælp af Azure Data Factory. |
| 37 | [Kopiere aktivitet ydeevne og justering vejledning](data-factory-copy-activity-performance.md) | Få mere at vide om vigtige faktorer, der påvirker ydeevnen for flytning af data i Azure Data Factory, når du bruger kopi aktivitet. |
| 38 | [Flyt data ved hjælp af kopi aktivitet](data-factory-data-movement-activities.md) | Få mere at vide om flytning af data i Data Factory rørledninger: dataoverførsel mellem skyen butikker og mellem et lokalt lager og et lager til skyen. Brug Kopiér aktivitet. |
| 39 | [Produktbemærkninger til Datastyringsgateway](data-factory-gateway-release-notes.md) | Produktbemærkninger til data Management Gateway tory |
| 40 | [Flytte data fra lokale HDFS ved hjælp af Azure Data Factory](data-factory-hdfs-connector.md) | Få mere at vide om, hvordan til at flytte data fra lokale HDFS ved hjælp af Azure Data Factory. |
| 41 | [Overvåge og administrere Azure Data Factory rørledninger ved hjælp af nye overvågning og administration af App](data-factory-monitor-manage-app.md) | Lær, hvordan du bruger overvågning og administration af App til at overvåge og administrere Azure data fabrikker og rørledninger. |
| 42 | [Flytte data mellem lokale datakilder og skyen med Data Management Gateway](data-factory-move-data-between-onprem-and-cloud.md) | Konfigurere en datastyringsgateway til at flytte data mellem lokale miljø og skyen. Brug af Datastyringsgateway i Azure Data Factory til at flytte dine data. |
| 43 | [Flytte data fra et OData datakilde ved hjælp af Azure Data Factory](data-factory-odata-connector.md) | Få mere at vide om, hvordan til at flytte data fra OData kilder ved hjælp af Azure Data Factory. |
| 44 | [Flytte data fra ODBC-data butikker, der bruger Azure Data Factory](data-factory-odbc-connector.md) | Få mere at vide om, hvordan til at flytte data fra ODBC-data butikker, der bruger Azure Data Factory. |
| 45 | [Flytte data fra DB2 ved hjælp af Azure Data Factory](data-factory-onprem-db2-connector.md) | Få mere at vide om, hvordan flytte data fra DB2-Database ved hjælp af Azure Data Factory |
| 46 | [Flytte data til og fra et lokalt filsystem ved hjælp af Azure Data Factory](data-factory-onprem-file-system-connector.md) | Lær at flytte data til og fra et lokalt filsystem ved hjælp af Azure Data Factory. |
| 47 | [Flytte data fra MySQL ved hjælp af Azure Data Factory](data-factory-onprem-mysql-connector.md) | Få mere at vide om, hvordan til at flytte data fra MySQL-database ved hjælp af Azure Data Factory. |
| 48 | [Flytte data til/fra lokale Oracle ved hjælp af Azure Data Factory](data-factory-onprem-oracle-connector.md) | Lær at flytte data til/fra Oracle-database, der er i det lokale miljø ved hjælp af Azure Data Factory. |
| 49 | [Flytte data fra PostgreSQL ved hjælp af Azure Data Factory](data-factory-onprem-postgresql-connector.md) | Få mere at vide om, hvordan til at flytte data fra PostgreSQL-Database ved hjælp af Azure Data Factory. |
| 50 | [Flytte data fra Sybase ved hjælp af Azure Data Factory](data-factory-onprem-sybase-connector.md) | Få mere at vide om, hvordan til at flytte data fra Sybase-Database ved hjælp af Azure Data Factory. |
| 51 | [Flytte data fra Teradata ved hjælp af Azure Data Factory](data-factory-onprem-teradata-connector.md) | Få mere at vide om Teradata forbindelse til Data Factory-tjenesten, hvor du kan flytte data fra Teradata-Database |
| 52 | [Flytte data til og fra SQL Server lokalt eller på IaaS (Azure VM) ved hjælp af Azure Data Factory](data-factory-sqlserver-connector.md) | Få mere at vide om, hvordan til at flytte data til/fra SQL Server-database, der er lokalt eller i en Azure VM ved hjælp af Azure Data Factory. |
| 53 | [Flytte data fra et websted i tabel, der bruger Azure Data Factory](data-factory-web-table-connector.md) | Få mere at vide om, hvordan til at flytte data fra det lokale en tabel i en webside i Azure Data Factory. |



## <a name="data-transformation"></a>Transformation af data

| &nbsp; | Titel | Beskrivelse |
| --: | :-- | :-- |
| 54 | [Oprette skønnet rørledninger ved hjælp af Azure Machine Learning aktiviteter](data-factory-azure-ml-batch-execution-activity.md) | Beskriver, hvordan du opretter oprette skønnet rørledninger ved hjælp af Azure Data Factory og Azure maskine læring |
| 55 | [Beregne sammenkædede tjenester](data-factory-compute-linked-services.md) | Få mere at vide om Beregn enviornments, du kan bruge i Azure Data Factory rørledninger transformation/proces data. |
| 56 | [Procesfarver store datasæt ved hjælp af Data Factory og batchen](data-factory-data-processing-using-batch.md) | Beskriver, hvordan du behandle store mængder data i en Azure Data Factory pipeline ved hjælp af muligheden for parallel behandling af Azure Batch. |
| 57 | [Transformere data i Azure Data Factory](data-factory-data-transformation-activities.md) | Lær at transformere data eller processen data i Azure Data Factory bruger Hadoop, Machine Learning eller Azure Data sø analyser. |
| 58 | [Hadoop Streaming aktivitet](data-factory-hadoop-streaming-activity.md) | Få mere at vide, hvordan du kan bruge den Hadoop Streaming aktivitet på en Azure data fabrik for at køre Hadoop Streaming programmer på en i-demand/dine egne HDInsight klynge. |
| 59 | [Hive aktivitet](data-factory-hive-activity.md) | Få mere at vide, hvordan du kan bruge den Hive aktivitet på en Azure data fabrik til at køre Hive forespørgsler på en i-demand/dine egne HDInsight klynge. |
| 60 | [Kalde MapReduce programmer fra Data Factory](data-factory-map-reduce.md) | Lær at behandle data ved at køre MapReduce programmer på en Azure HDInsight klynge fra en Azure data fabrik. |
| 61 | [Gris aktivitet](data-factory-pig-activity.md) | Få mere at vide, hvordan du kan bruge den gris aktivitet på en Azure data fabrik til at køre gris scripts på en i-demand/dine egne HDInsight klynge. |
| 62 | [Kalde knallertmotor programmer fra Data Factory](data-factory-spark.md) | Lær at kalde knallertmotor programmer fra en Azure data factory ved hjælp af den MapReduce aktivitet. |
| 63 | [SQL Server gemt Procedure aktivitet](data-factory-stored-proc-activity.md) | Få mere at vide, hvordan du kan bruge den SQL Server gemt Procedure aktivitet til at starte en lagret procedure i en Azure SQL-Database eller Azure SQL Data Warehouse fra en Data Factory rørledning. |
| 64 | [Bruge brugerdefinerede aktiviteter i en Azure Data Factory pipeline](data-factory-use-custom-activities.md) | Lær at oprette brugerdefinerede aktiviteter og bruge dem i en Azure Data Factory pipeline. |
| 65 | [Køre U-SQL-script på Azure Data sø Analytics fra Azure Data Factory](data-factory-usql-activity.md) | Lær at behandle data ved at køre U-SQL-scripts på Azure Data sø Analytics Beregn tjeneste. |



## <a name="samples"></a>Eksempler

| &nbsp; | Titel | Beskrivelse |
| --: | :-- | :-- |
| 66 | [Azure Data Factory - eksempler](data-factory-samples.md) | Du kan finde oplysninger om eksempler, der leveres med Azure Data Factory-tjenesten. |



## <a name="use-cases"></a>Use Cases

| &nbsp; | Titel | Beskrivelse |
| --: | :-- | :-- |
| 67 | [Kunde casestudier](data-factory-customer-case-studies.md) | Få mere at vide om, hvordan nogle af vores kunder har brugt Azure Data Factory. |
| 68 | [Use Case - kunde profiler](data-factory-customer-profiling-usecase.md) | Få mere at vide, hvordan Azure Data Factory bruges til at oprette en databaserede-arbejdsproces (pipeline) for at profil gaming kunder. |
| 69 | [Use Case - produkt anbefalinger](data-factory-product-reco-usecase.md) | Få mere at vide om en Brug sag implementeret ved hjælp af Azure Data Factory sammen med andre tjenester. |



## <a name="monitor-and-manage"></a>Overvåge og administrere

| &nbsp; | Titel | Beskrivelse |
| --: | :-- | :-- |
| 70 | [Overvåge og administrere Azure Data Factory rørledninger](data-factory-monitor-manage-pipelines.md) | Lær, hvordan du bruger Azure Portal og Azure PowerShell til at overvåge og administrere Azure data fabrikker og rørledninger, du har oprettet. |



## <a name="sdk"></a>SDK

| &nbsp; | Titel | Beskrivelse |
| --: | :-- | :-- |
| 71 | [Azure Data Factory - ændringslog for .NET API](data-factory-api-change-log.md) | I denne artikel beskrives seneste ændringer, funktionstilføjelser, fejlrettelser osv... i en bestemt version af .NET API til Azure Data Factory. |
| 72 | [Oprette, overvåge og administrere Azure data fabrikker ved hjælp af Data Factory .NET SDK](data-factory-create-data-factories-programmatically.md) | Lær, hvordan du automatisk at oprette, overvåge og administrere Azure data fabrikker ved hjælp af Data Factory SDK. |
| 73 | [Azure Data Factory Udviklerreference](data-factory-sdks.md) | Få mere at vide om forskellige måder at oprette, overvåge og administrere Azure data fabrikker |



## <a name="miscellaneous"></a>Diverse

| &nbsp; | Titel | Beskrivelse |
| --: | :-- | :-- |
| 74 | [Azure Data Factory - ofte stillede spørgsmål](data-factory-faq.md) | Ofte stillede spørgsmål om Azure Data Factory. |
| 75 | [Azure Data Factory - funktioner og systemvariabler](data-factory-functions-variables.md) | Oversigt over Azure Data Factory-funktioner og systemvariabler |
| 76 | [Azure Data Factory - navngivning af regler](data-factory-naming-rules.md) | Beskriver naming regler for Data Factory objekter. |
| 77 | [Foretage fejlfinding af problemer med Data Factory](data-factory-troubleshoot.md) | Lær at foretage fejlfinding af problemer med at bruge Azure Data Factory. |

