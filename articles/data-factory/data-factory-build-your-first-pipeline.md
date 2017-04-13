<properties
    pageTitle="Data Factory Selvstudium: første data pipeline | Microsoft Azure"
    description="Selvstudiet Azure Data Factory viser, hvordan du oprette og planlægge en data fabrik, der behandler data ved hjælp af Hive script på en Hadoop-klynge."
    services="data-factory"
    keywords="Azure data factory selvstudium, hadoop-klynge, hadoop hive"
    documentationCenter=""
    authors="spelluru"
    manager="jhubbard"
    editor=""/>

<tags
    ms.service="data-factory"
    ms.workload="data-services"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article" 
    ms.date="09/26/2016"
    ms.author="spelluru"/>

# <a name="tutorial-build-your-first-pipeline-to-process-data-using-hadoop-cluster"></a>Selvstudium: Oprette din første pipeline til proces data ved hjælp af Hadoop klynge 
> [AZURE.SELECTOR]
- [Oversigt og forudsætninger](data-factory-build-your-first-pipeline.md)
- [Azure-portalen](data-factory-build-your-first-pipeline-using-editor.md)
- [Visual Studio](data-factory-build-your-first-pipeline-using-vs.md)
- [PowerShell](data-factory-build-your-first-pipeline-using-powershell.md)
- [Ressourcestyring skabelon](data-factory-build-your-first-pipeline-using-arm.md)
- [REST-API](data-factory-build-your-first-pipeline-using-rest-api.md)

Du kan oprette din første Azure data factory med en data rørledning, der behandler data ved at køre Hive script på en Azure HDInsight (Hadoop) klynge i dette selvstudium. 

> [AZURE.NOTE] I denne artikel indeholder ikke en oversigt over Azure Data Factory. Se [Introduktion til Azure Data Factory](data-factory-introduction.md)for en oversigt over tjenesten. Se [Data Factory læringssti](https://azure.microsoft.com/documentation/learning-paths/data-factory/) til den bedste måde at navigere gennem vores indhold til at få mere at vide om Data Factory.

## <a name="whats-covered-in-this-tutorial"></a>Hvad er dækket i dette selvstudium? 
**Azure Data Factory** gør det muligt at skrive data **Bevægelse** og opgaver til **behandling** af data som datadrevne arbejdsprocesser (også kaldet data rørledninger). Lærer du at oprette din første data rørledning med en databehandling (eller datatransformation) aktivitet. Denne aktivitet bruger en HDInsight Hadoop-klynge til at transformere og analysere eksempel weblogfiler.  

I dette selvstudium skal du udføre følgende trin:

1.  Oprette en **data factory**. En data fabrik kan indeholde et eller flere data rørledninger Flyt og proces dataene. 
2.  Oprette **sammenkædede tjenester**. Du opretter en sammenkædet tjeneste sammenkæde et datalager eller en Beregn tjeneste til fabriksindstillingerne data. Et datalager som Azure-lager indeholder input/output data over aktiviteter i pipeline. En Beregn tjeneste såsom HDInsight Hadoop klynge processer/transformationer data.    
3.  Oprette input og output **datasæt**. Et input datasæt repræsenterer input til en aktivitet i pipeline og et output datasæt repræsenterer output til aktiviteten.
3.  Oprette **pipeline**. En rørledning kan have en eller flere aktiviteter (eksempler: Kopiér aktivitet, HDInsight Hive aktivitet). Dette eksempel bruger den HDInsight Hive aktivitet, der kører et Hive-script på en HDInsight Hadoop-klynge. Scriptet først opretter en tabel, der henviser til rå web logdataene, der er gemt i Azure blob-lager og derefter partitioner på rækkedata efter år og måned.

    Der findes to typer aktiviteter, der understøttes af Azure Data Factory. De er: [data bevægelse aktiviteter](data-factory-data-movement-activities.md) og [data transformation aktiviteter](data-factory-data-transformation-activities.md). Der er kun én data bevægelse aktivitet, der er den kopi aktivitet. I dette selvstudium skal du ikke bruge den kopi aktivitet. Se et selvstudium om at bruge den kopi aktivitet [Selvstudium: Kopiér data fra en Azure blob til Azure SQL](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md). HDInsight Hive aktivitet, du bruger i dette selvstudium er en af de data transformation aktiviteter, der understøttes af Data Factory.  
 
Her er den **diagramvisning** af eksempel data factory du opretter i dette selvstudium. 

![Diagramvisning i Data Factory selvstudium](./media/data-factory-build-your-first-pipeline/data-factory-tutorial-diagram-view.png)

I dette selvstudium indeholder **inputdata** mappe med objektbeholderen **adfgetstarted** Azure blob en fil med navnet input.log. Denne logfil har opslagsord fra tre måneder: januar, februar og marts 2016. Her er eksempel rækkerne for hver måned i filen input. 

    2016-01-01,02:01:09,SAMPLEWEBSITE,GET,/blogposts/mvc4/step2.png,X-ARR-LOG-ID=2ec4b8ad-3cf0-4442-93ab-837317ece6a1,80,-,1.54.23.196,Mozilla/5.0+(Windows+NT+6.3;+WOW64)+AppleWebKit/537.36+(KHTML,+like+Gecko)+Chrome/31.0.1650.63+Safari/537.36,-,http://weblogs.asp.net/sample/archive/2007/12/09/asp-net-mvc-framework-part-4-handling-form-edit-and-post-scenarios.aspx,\N,200,0,0,53175,871 
    2016-02-01,02:01:10,SAMPLEWEBSITE,GET,/blogposts/mvc4/step7.png,X-ARR-LOG-ID=d7472a26-431a-4a4d-99eb-c7b4fda2cf4c,80,-,1.54.23.196,Mozilla/5.0+(Windows+NT+6.3;+WOW64)+AppleWebKit/537.36+(KHTML,+like+Gecko)+Chrome/31.0.1650.63+Safari/537.36,-,http://weblogs.asp.net/sample/archive/2007/12/09/asp-net-mvc-framework-part-4-handling-form-edit-and-post-scenarios.aspx,\N,200,0,0,30184,871
    2016-03-01,02:01:10,SAMPLEWEBSITE,GET,/blogposts/mvc4/step7.png,X-ARR-LOG-ID=d7472a26-431a-4a4d-99eb-c7b4fda2cf4c,80,-,1.54.23.196,Mozilla/5.0+(Windows+NT+6.3;+WOW64)+AppleWebKit/537.36+(KHTML,+like+Gecko)+Chrome/31.0.1650.63+Safari/537.36,-,http://weblogs.asp.net/sample/archive/2007/12/09/asp-net-mvc-framework-part-4-handling-form-edit-and-post-scenarios.aspx,\N,200,0,0,30184,871

Når filen er behandlet af pipeline med HDInsight Hive aktiviteter, kører aktiviteten et Hive-script på HDInsight klyngen, partitioner Indtast data efter år og måned. Scriptet opretter tre outputmapper, der indeholder en fil med poster fra hver måned.  

    adfgetstarted/partitioneddata/year=2016/month=1/000000_0
    adfgetstarted/partitioneddata/year=2016/month=2/000000_0
    adfgetstarted/partitioneddata/year=2016/month=3/000000_0

Fra ovenstående linjerne eksempel, der er skrevet den første opgave (med 2016-01-01) til filen 000000_0 på måneden = 1 mappe. På samme måde, den anden opgave er skrevet til filen i måned = 2 mappe og tredjepart en skrives til filen i måneden = 3 mappe.  


## <a name="pre-requisites"></a>Forudsætninger
Inden du starter selvstudiet, skal du have følgende forudsætninger:

1.  **Azure abonnement** - Hvis du ikke har et Azure-abonnement, kan du oprette en gratis prøveversion konto på blot et par minutter. Se [Gratis prøveversion](https://azure.microsoft.com/pricing/free-trial/) artikel om hvordan du kan hente en gratis prøveversion konto.

2.  **Azure-lager** – du bruger en konto til Azure-lager til lagring af data i dette selvstudium. Hvis du ikke har en Azure-lager-konto, kan du se artiklen [Opret en lagerplads konto](../storage/storage-create-storage-account.md#create-a-storage-account) . Når du har oprettet kontoen lagerplads, Bemærk **kontonavn** og **hurtigtast**. Få [vist, Kopiér og Regenerer lagerplads hurtigtaster](../storage/storage-create-storage-account.md#view-and-copy-storage-access-keys). 

### <a name="upload-files-to-azure-storage-for-the-tutorial"></a>Overføre filer til Azure-lager på selvstudiet
Før du starter selvstudiet, skal du oprette kontoen Azure-lager med eksempelfiler i dette selvstudium.

1. Overføre Hive forespørgselsfil (HQL) til **script** mappe med objektbeholderen **adfgetstarted** blob.
2. Overføre en fil til **inputdata** mappe med objektbeholderen **adfgetstarted** blob. 

#### <a name="create-hql-script-file"></a>Oprette HQL scriptfil 

1. Start **Notesblok** og indsætte følgende HQL script. Dette Hive script opretter to tabeller: **WebLogsRaw** og **WebLogsPartitioned**. Klik på **filer** i menuen, og vælg **Gem som**. Skifte til mappen **C:\adfgetstarted** på harddisken. Vælg * *alle filer (*.*) **for den** Gem som Skriv** felt. Angiv **partitionweblogs.hql** for den **filnavn**. Bekræft, at den **kode** i feltet nederst i dialogboksen for er indstillet til **ANSI**. Hvis ikke, skal du angive den til **ANSI **.  

        set hive.exec.dynamic.partition.mode=nonstrict;
        
        DROP TABLE IF EXISTS WebLogsRaw; 
        CREATE TABLE WebLogsRaw (
          date  date,
          time  string,
          ssitename string,
          csmethod  string,
          csuristem  string,
          csuriquery string,
          sport int,
          susername string,
          cipcsUserAgent string,
          csCookie string,
          csReferer string,
          cshost  string,
          scstatus  int,
          scsubstatus  int,
          scwin32status  int,
          scbytes int,
          csbytes int,
          timetaken int
        )
        ROW FORMAT DELIMITED FIELDS TERMINATED BY ' '
        LINES TERMINATED BY '\n' 
        tblproperties ("skip.header.line.count"="2");
        
        LOAD DATA INPATH '${hiveconf:inputtable}' OVERWRITE INTO TABLE WebLogsRaw;
        
        DROP TABLE IF EXISTS WebLogsPartitioned ; 
        create external table WebLogsPartitioned (  
          date  date,
          time  string,
          ssitename string,
          csmethod  string,
          csuristem  string,
          csuriquery string,
          sport int,
          susername string,
          cipcsUserAgent string,
          csCookie string,
          csReferer string,
          cshost  string,
          scstatus  int,
          scsubstatus  int,
          scwin32status  int,
          scbytes int,
          csbytes int,
          timetaken int
        )
        partitioned by ( year int, month int)
        ROW FORMAT DELIMITED FIELDS TERMINATED BY ',' 
        STORED AS TEXTFILE 
        LOCATION '${hiveconf:partitionedtable}';
        
        INSERT INTO TABLE WebLogsPartitioned  PARTITION( year , month) 
        SELECT
          date,
          time,
          ssitename,
          csmethod,
          csuristem,
          csuriquery,
          sport,
          susername,
          cipcsUserAgent,
          csCookie,
          csReferer,
          cshost,
          scstatus,
          scsubstatus,
          scwin32status,
          scbytes,
          csbytes,
          timetaken,
          year(date),
          month(date)
        FROM WebLogsRaw

På kørselstidspunktet, den Hive aktivitet i Data Factory pipeline overfører værdier den **inputtable** og **partitionedtable** parametre som vist i følgende kodestykket:  

        "inputtable": "wasb://adfgetstarted@<storageaccountname>.blob.core.windows.net/inputdata",
        "partitionedtable": "wasb://adfgetstarted@<storageaccountname>.blob.core.windows.net/partitioneddata"

**Storageaccountname** er navnet på kontoen Azure-lager.
 
#### <a name="create-a-sample-input-file"></a>Oprette en eksempelfil input
Med Notesblok kan du oprette en fil med navnet **input.log** i **c:\adfgetstarted** med følgende indhold: 

    #Software: Microsoft Internet Information Services 8.0
    #Fields: date time s-sitename cs-method cs-uri-stem cs-uri-query s-port cs-username c-ip cs(User-Agent) cs(Cookie) cs(Referer) cs-host sc-status sc-substatus sc-win32-status sc-bytes cs-bytes time-taken
    2016-01-01 02:01:09 SAMPLEWEBSITE GET /blogposts/mvc4/step2.png X-ARR-LOG-ID=2ec4b8ad-3cf0-4442-93ab-837317ece6a1 80 - 1.54.23.196 Mozilla/5.0+(Windows+NT+6.3;+WOW64)+AppleWebKit/537.36+(KHTML,+like+Gecko)+Chrome/31.0.1650.63+Safari/537.36 - http://weblogs.asp.net/sample/archive/2007/12/09/asp-net-mvc-framework-part-4-handling-form-edit-and-post-scenarios.aspx www.sample.com 200 0 0 53175 871 46
    2016-01-01 02:01:09 SAMPLEWEBSITE GET /blogposts/mvc4/step3.png X-ARR-LOG-ID=9eace870-2f49-4efd-b204-0d170da46b4a 80 - 1.54.23.196 Mozilla/5.0+(Windows+NT+6.3;+WOW64)+AppleWebKit/537.36+(KHTML,+like+Gecko)+Chrome/31.0.1650.63+Safari/537.36 - http://weblogs.asp.net/sample/archive/2007/12/09/asp-net-mvc-framework-part-4-handling-form-edit-and-post-scenarios.aspx www.sample.com 200 0 0 51237 871 32
    2016-01-01 02:01:09 SAMPLEWEBSITE GET /blogposts/mvc4/step4.png X-ARR-LOG-ID=4bea5b3d-8ac9-46c9-9b8c-ec3e9500cbea 80 - 1.54.23.196 Mozilla/5.0+(Windows+NT+6.3;+WOW64)+AppleWebKit/537.36+(KHTML,+like+Gecko)+Chrome/31.0.1650.63+Safari/537.36 - http://weblogs.asp.net/sample/archive/2007/12/09/asp-net-mvc-framework-part-4-handling-form-edit-and-post-scenarios.aspx www.sample.com 200 0 0 72177 871 47
    2016-01-01 02:01:09 SAMPLEWEBSITE GET /blogposts/mvc4/step5.png X-ARR-LOG-ID=9b0c14b1-434d-495a-9b0d-46775194257b 80 - 1.54.23.196 Mozilla/5.0+(Windows+NT+6.3;+WOW64)+AppleWebKit/537.36+(KHTML,+like+Gecko)+Chrome/31.0.1650.63+Safari/537.36 - http://weblogs.asp.net/sample/archive/2007/12/09/asp-net-mvc-framework-part-4-handling-form-edit-and-post-scenarios.aspx www.sample.com 200 0 0 37931 871 32
    2016-01-01 02:01:09 SAMPLEWEBSITE GET /blogposts/mvc4/step6.png X-ARR-LOG-ID=f99cff81-ec38-4a13-b2fe-21b10adca996 80 - 1.54.23.196 Mozilla/5.0+(Windows+NT+6.3;+WOW64)+AppleWebKit/537.36+(KHTML,+like+Gecko)+Chrome/31.0.1650.63+Safari/537.36 - http://weblogs.asp.net/sample/archive/2007/12/09/asp-net-mvc-framework-part-4-handling-form-edit-and-post-scenarios.aspx www.sample.com 200 0 0 27146 871 47
    2016-01-01 02:01:09 SAMPLEWEBSITE GET /blogposts/mvc4/step1.png X-ARR-LOG-ID=af94d3b4-8e05-4871-82c4-638f866d4e83 80 - 1.54.23.196 Mozilla/5.0+(Windows+NT+6.3;+WOW64)+AppleWebKit/537.36+(KHTML,+like+Gecko)+Chrome/31.0.1650.63+Safari/537.36 - http://weblogs.asp.net/sample/archive/2007/12/09/asp-net-mvc-framework-part-4-handling-form-edit-and-post-scenarios.aspx www.sample.com 200 0 0 66259 871 140
    2016-02-01 02:01:10 SAMPLEWEBSITE GET /blogposts/mvc4/step7.png X-ARR-LOG-ID=d7472a26-431a-4a4d-99eb-c7b4fda2cf4c 80 - 1.54.23.196 Mozilla/5.0+(Windows+NT+6.3;+WOW64)+AppleWebKit/537.36+(KHTML,+like+Gecko)+Chrome/31.0.1650.63+Safari/537.36 - http://weblogs.asp.net/sample/archive/2007/12/09/asp-net-mvc-framework-part-4-handling-form-edit-and-post-scenarios.aspx www.sample.com 200 0 0 30184 871 47
    2016-02-01 02:01:10 SAMPLEWEBSITE GET /blogposts/mvc4/step7.png X-ARR-LOG-ID=d7472a26-431a-4a4d-99eb-c7b4fda2cf4c 80 - 1.54.23.196 Mozilla/5.0+(Windows+NT+6.3;+WOW64)+AppleWebKit/537.36+(KHTML,+like+Gecko)+Chrome/31.0.1650.63+Safari/537.36 - http://weblogs.asp.net/sample/archive/2007/12/09/asp-net-mvc-framework-part-4-handling-form-edit-and-post-scenarios.aspx www.sample.com 200 0 0 30184 871 47
    2016-02-01 02:01:10 SAMPLEWEBSITE GET /blogposts/mvc4/step7.png X-ARR-LOG-ID=d7472a26-431a-4a4d-99eb-c7b4fda2cf4c 80 - 1.54.23.196 Mozilla/5.0+(Windows+NT+6.3;+WOW64)+AppleWebKit/537.36+(KHTML,+like+Gecko)+Chrome/31.0.1650.63+Safari/537.36 - http://weblogs.asp.net/sample/archive/2007/12/09/asp-net-mvc-framework-part-4-handling-form-edit-and-post-scenarios.aspx www.sample.com 200 0 0 30184 871 47
    2016-02-01 02:01:10 SAMPLEWEBSITE GET /blogposts/mvc4/step7.png X-ARR-LOG-ID=d7472a26-431a-4a4d-99eb-c7b4fda2cf4c 80 - 1.54.23.196 Mozilla/5.0+(Windows+NT+6.3;+WOW64)+AppleWebKit/537.36+(KHTML,+like+Gecko)+Chrome/31.0.1650.63+Safari/537.36 - http://weblogs.asp.net/sample/archive/2007/12/09/asp-net-mvc-framework-part-4-handling-form-edit-and-post-scenarios.aspx www.sample.com 200 0 0 30184 871 47
    2016-02-01 02:01:10 SAMPLEWEBSITE GET /blogposts/mvc4/step7.png X-ARR-LOG-ID=d7472a26-431a-4a4d-99eb-c7b4fda2cf4c 80 - 1.54.23.196 Mozilla/5.0+(Windows+NT+6.3;+WOW64)+AppleWebKit/537.36+(KHTML,+like+Gecko)+Chrome/31.0.1650.63+Safari/537.36 - http://weblogs.asp.net/sample/archive/2007/12/09/asp-net-mvc-framework-part-4-handling-form-edit-and-post-scenarios.aspx www.sample.com 200 0 0 30184 871 47
    2016-02-01 02:01:10 SAMPLEWEBSITE GET /blogposts/mvc4/step7.png X-ARR-LOG-ID=d7472a26-431a-4a4d-99eb-c7b4fda2cf4c 80 - 1.54.23.196 Mozilla/5.0+(Windows+NT+6.3;+WOW64)+AppleWebKit/537.36+(KHTML,+like+Gecko)+Chrome/31.0.1650.63+Safari/537.36 - http://weblogs.asp.net/sample/archive/2007/12/09/asp-net-mvc-framework-part-4-handling-form-edit-and-post-scenarios.aspx www.sample.com 200 0 0 30184 871 47
    2016-02-01 02:01:10 SAMPLEWEBSITE GET /blogposts/mvc4/step7.png X-ARR-LOG-ID=d7472a26-431a-4a4d-99eb-c7b4fda2cf4c 80 - 1.54.23.196 Mozilla/5.0+(Windows+NT+6.3;+WOW64)+AppleWebKit/537.36+(KHTML,+like+Gecko)+Chrome/31.0.1650.63+Safari/537.36 - http://weblogs.asp.net/sample/archive/2007/12/09/asp-net-mvc-framework-part-4-handling-form-edit-and-post-scenarios.aspx www.sample.com 200 0 0 30184 871 47
    2016-02-01 02:01:10 SAMPLEWEBSITE GET /blogposts/mvc4/step7.png X-ARR-LOG-ID=d7472a26-431a-4a4d-99eb-c7b4fda2cf4c 80 - 1.54.23.196 Mozilla/5.0+(Windows+NT+6.3;+WOW64)+AppleWebKit/537.36+(KHTML,+like+Gecko)+Chrome/31.0.1650.63+Safari/537.36 - http://weblogs.asp.net/sample/archive/2007/12/09/asp-net-mvc-framework-part-4-handling-form-edit-and-post-scenarios.aspx www.sample.com 200 0 0 30184 871 47
    2016-03-01 02:01:10 SAMPLEWEBSITE GET /blogposts/mvc4/step7.png X-ARR-LOG-ID=d7472a26-431a-4a4d-99eb-c7b4fda2cf4c 80 - 1.54.23.196 Mozilla/5.0+(Windows+NT+6.3;+WOW64)+AppleWebKit/537.36+(KHTML,+like+Gecko)+Chrome/31.0.1650.63+Safari/537.36 - http://weblogs.asp.net/sample/archive/2007/12/09/asp-net-mvc-framework-part-4-handling-form-edit-and-post-scenarios.aspx www.sample.com 200 0 0 30184 871 47
    2016-03-01 02:01:10 SAMPLEWEBSITE GET /blogposts/mvc4/step7.png X-ARR-LOG-ID=d7472a26-431a-4a4d-99eb-c7b4fda2cf4c 80 - 1.54.23.196 Mozilla/5.0+(Windows+NT+6.3;+WOW64)+AppleWebKit/537.36+(KHTML,+like+Gecko)+Chrome/31.0.1650.63+Safari/537.36 - http://weblogs.asp.net/sample/archive/2007/12/09/asp-net-mvc-framework-part-4-handling-form-edit-and-post-scenarios.aspx www.sample.com 200 0 0 30184 871 47
    2016-03-01 02:01:10 SAMPLEWEBSITE GET /blogposts/mvc4/step7.png X-ARR-LOG-ID=d7472a26-431a-4a4d-99eb-c7b4fda2cf4c 80 - 1.54.23.196 Mozilla/5.0+(Windows+NT+6.3;+WOW64)+AppleWebKit/537.36+(KHTML,+like+Gecko)+Chrome/31.0.1650.63+Safari/537.36 - http://weblogs.asp.net/sample/archive/2007/12/09/asp-net-mvc-framework-part-4-handling-form-edit-and-post-scenarios.aspx www.sample.com 200 0 0 30184 871 47
    2016-03-01 02:01:10 SAMPLEWEBSITE GET /blogposts/mvc4/step7.png X-ARR-LOG-ID=d7472a26-431a-4a4d-99eb-c7b4fda2cf4c 80 - 1.54.23.196 Mozilla/5.0+(Windows+NT+6.3;+WOW64)+AppleWebKit/537.36+(KHTML,+like+Gecko)+Chrome/31.0.1650.63+Safari/537.36 - http://weblogs.asp.net/sample/archive/2007/12/09/asp-net-mvc-framework-part-4-handling-form-edit-and-post-scenarios.aspx www.sample.com 200 0 0 30184 871 47
    2016-03-01 02:01:10 SAMPLEWEBSITE GET /blogposts/mvc4/step7.png X-ARR-LOG-ID=d7472a26-431a-4a4d-99eb-c7b4fda2cf4c 80 - 1.54.23.196 Mozilla/5.0+(Windows+NT+6.3;+WOW64)+AppleWebKit/537.36+(KHTML,+like+Gecko)+Chrome/31.0.1650.63+Safari/537.36 - http://weblogs.asp.net/sample/archive/2007/12/09/asp-net-mvc-framework-part-4-handling-form-edit-and-post-scenarios.aspx www.sample.com 200 0 0 30184 871 47

#### <a name="upload-input-file-and-hql-file-to-your-azure-blob-storage"></a>Overføre en fil og HQL fil til Azure Blob-lager

Dette afsnit indeholder en vejledning til brug af **AzCopy** værktøj til at kopiere input.log og partitionweblogs.hql filer til Azure Blob-lager. Du kan bruge en hvilken som helst værktøjet efter eget valg (for eksempel: [Microsoft Azure lagerplads Explorer](http://storageexplorer.com/), [CloudXPlorer ClumsyLeaf software](http://clumsyleaf.com/products/cloudxplorer) til at udføre denne opgave.   
     
1. Hent den [nyeste version af **AzCopy**](http://aka.ms/downloadazcopy)eller den [seneste preview-version](http://aka.ms/downloadazcopypr). Se [hvordan du bruger AzCopy](../storage/storage-use-azcopy.md) artikel for at få vejledning til, ved hjælp af værktøjet.
2. Gå til mappen c:\adfgetstarted og køre følgende kommando: 

        "C:\Program Files (x86)\Microsoft SDKs\Azure\AzCopy\AzCopy" /Source:. /Dest:https://<storageaccountname>.blob.core.windows.net/adfgetstarted/inputdata /DestKey:<storageaccesskey>  /Pattern:input.log

    Denne kommando uploader filen **input.log** til kontoen lagerplads (**adfgetstarted** objektbeholder og **inputdata** mappe). Erstat **storageaccountname** med navnet på din lagerplads konto og **storageaccesskey** hurtigtast lagerplads.

    > [AZURE.NOTE] Denne kommando opretter en objektbeholder med navnet **adfgetstarted** i dit Azure Blob-lager og kopierer filen **input.log** fra dit lokale drev til mappen **inputdata** i objektbeholderen. 
    
3. Når filen er blevet overført, kan du få vist den output, der svarer til det følgende fra AzCopy.
    
        Finished 1 of total 1 file(s).
        [2015/12/16 23:07:33] Transfer summary:
        -----------------
        Total files transferred: 1
        Transfer successfully:   1
        Transfer skipped:        0
        Transfer failed:         0
        Elapsed time:            00.00:00:01
5. Kør følgende kommando for at overføre filen **partitionweblogs.hql** til mappen **script** for objektbeholderen **adfgetstarted** . Her er kommandoen: 
    
        AzCopy /Source:. /Dest:https://<storageaccountname>.blob.core.windows.net/adfgetstarted/script /DestKey:<storageaccesskey>  /Pattern:partitionweblogs.hql

Du har fuldført forudsætningerne. Du kan oprette en data fabrik ved hjælp af en af følgende måder. Klik på en af fanerne øverst eller linkene nedenfor til at udføre selvstudiet. 

- [Azure-portalen](data-factory-build-your-first-pipeline-using-editor.md)
- [Visual Studio](data-factory-build-your-first-pipeline-using-vs.md)
- [PowerShell](data-factory-build-your-first-pipeline-using-powershell.md)
- [Ressourcestyring skabelon](data-factory-build-your-first-pipeline-using-arm.md)
- [REST-API](data-factory-build-your-first-pipeline-using-rest-api.md)