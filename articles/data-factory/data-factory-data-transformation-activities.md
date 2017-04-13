<properties 
    pageTitle="Datatransformation: Proces og Transformér data | Microsoft Azure" 
    description="Lær at transformere data eller processen data i Azure Data Factory bruger Hadoop, Machine Learning eller Azure Data sø analyser." 
    keywords="transformation af data, proces data transformere data, transformation aktivitet"
    services="data-factory" 
    documentationCenter="" 
    authors="sharonlo101" 
    manager="jhubbard" 
    editor="monicar"/>

<tags 
    ms.service="data-factory" 
    ms.workload="data-services" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="09/23/2016" 
    ms.author="shlo"/>

# <a name="transform-data-in-azure-data-factory"></a>Transformere data i Azure Data Factory
> [AZURE.SELECTOR]
[Hive](data-factory-hive-activity.md)  
[Gris](data-factory-pig-activity.md)  
[MapReduce](data-factory-map-reduce.md)  
[Hadoop Streaming](data-factory-hadoop-streaming-activity.md)
[Machine Learning](data-factory-azure-ml-batch-execution-activity.md) 
[En lagret Procedure](data-factory-stored-proc-activity.md)
[Data sø Analytics U-SQL](data-factory-usql-activity.md)
[.NET brugerdefineret](data-factory-use-custom-activities.md)
   

## <a name="overview"></a>Oversigt 
Denne artikel forklares data transformation aktiviteter i Azure Data Factory, du kan bruge til at transformere og behandler din rækkedata på prognoser og viden. En transformation aktivitet udfører i et miljø som Azure HDInsight klynge eller en Azure batchen. Den indeholder links til artikler med detaljerede oplysninger om hver transformation aktivitet.
 
Data Factory understøtter følgende data transformation aktiviteter, der kan tilføjes til [rørledninger](data-factory-create-pipelines.md) enten individuelt eller sammenkædet med en anden aktivitet.

> [AZURE.NOTE] En gennemgang med en trinvis vejledning, kan du [oprette en rørledning med Hive transformation](data-factory-build-your-first-pipeline.md) til artiklen.  

## <a name="hdinsight-hive-activity"></a>HDInsight Hive aktivitet
HDInsight Hive aktiviteten i en Data Factory pipeline udfører Hive forespørgsler på din egen eller efter behov Windows/Linux-baserede HDInsight klynge. Se [Hive aktivitet](data-factory-hive-activity.md) artikel for at få mere at vide om denne aktivitet. 

## <a name="hdinsight-pig-activity"></a>HDInsight gris aktivitet
HDInsight gris aktivitet i en Data Factory pipeline udfører gris forespørgsler på din egen eller efter behov Windows/Linux-baserede HDInsight klynge. Se [Gris aktivitet](data-factory-pig-activity.md) artikel for at få mere at vide om denne aktivitet. 

## <a name="hdinsight-mapreduce-activity"></a>HDInsight MapReduce aktivitet
HDInsight MapReduce aktiviteten i en Data Factory pipeline udfører MapReduce programmer på din egen eller efter behov Windows/Linux-baserede HDInsight klynge. Se [MapReduce aktivitet](data-factory-map-reduce.md) artikel for at få mere at vide om denne aktivitet.

Du kan bruge MapReduce aktivitet for at køre knallertmotor programmer på din HDInsight Spark klynge. Du kan finde oplysninger i [kalde knallertmotor programmer fra Azure Data Factory](data-factory-spark.md) .

## <a name="hdinsight-streaming-activity"></a>HDInsight Streaming aktivitet
Den HDInsight Streaming aktivitet i en Data Factory pipeline udfører Hadoop Streaming-programmerne på din egen eller efter behov Windows/Linux-baserede HDInsight klynge. Du kan finde oplysninger om denne aktivitet i [HDInsight Streaming aktivitet](data-factory-hadoop-streaming-activity.md) .

## <a name="machine-learning-activities"></a>Machine Learning aktiviteter
Azure Data Factory kan du nemt oprette rørledninger, der bruger en publiceret Azure Machine Learning-webtjeneste for skønnet analyser. Bruger [Batchen udførelse af aktivitet](data-factory-azure-ml-batch-execution-activity.md#invoking-a-web-service-using-batch-execution-activity) i en Azure Data Factory pipeline, kan du aktivere en Machine Learning webtjeneste for at gøre prognoser på dataene i batchen.

I tidens løb skal skønnet-modeller i maskine læring vundne forsøg være retrained ved hjælp af nye input datasæt. Når du er færdig med omskoling, vil du opdatere vurdering webtjenesten med retrained Machine Learning modellen. Du kan bruge [Opdatere ressource aktivitet](data-factory-azure-ml-batch-execution-activity.md#updating-models-using-update-resource-activity) opdatere webtjenesten med den nyligt erfaren model.  

Se [Brug Machine Learning aktiviteter](data-factory-azure-ml-batch-execution-activity.md) få mere at vide om disse Machine Learning aktiviteter. 

## <a name="stored-procedure-activity"></a>Lagret procedure aktivitet
Du kan bruge SQL Server en lagret Procedure aktiviteten i en Data Factory pipeline til at kalde en lagret procedure i en af de følgende data butikker: Azure SQL-Database Azure SQL Data Warehouse, SQL Server-Database i virksomheden eller en Azure VM. Se [Gemt Procedure aktivitet](data-factory-stored-proc-activity.md) artikel for at få mere at vide.  

## <a name="data-lake-analytics-u-sql-activity"></a>Data sø Analytics U-SQL aktivitet
Data sø Analytics U-SQL aktivitet kører et U-SQL-script på en Azure Data sø Analytics-klynge. Se [Data Analytics U-SQL aktivitet](data-factory-usql-activity.md) artikel for at få mere at vide. 

## <a name="net-custom-activity"></a>.NET tilpassede aktiviteter
Hvis du har brug for til at transformere data på en måde, der ikke understøttes af Data Factory, kan du oprette en brugerdefineret aktivitet med dine egne databehandling logik og bruge aktiviteten i pipeline. Du kan konfigurere brugerdefineret .NET aktiviteten til at køre ved hjælp af en Azure batchen tjeneste eller en Azure HDInsight klynge. Se [Brug brugerdefinerede aktiviteter](data-factory-use-custom-activities.md) artikel for at få mere at vide. 

Du kan oprette en brugerdefineret aktivitet for at køre R scripts på din HDInsight klynge med R, der er installeret. Se [køre R Script bruger Azure Data Factory](https://github.com/Azure/Azure-DataFactory/tree/master/Samples/RunRScriptUsingADFSample). 

## <a name="compute-environments"></a>Beregne miljøer
Du opretter en sammenkædet tjeneste for Beregn miljø og derefter bruge tjenesten sammenkædede, når du definerer en transformation aktivitet. Der findes to typer Beregn miljøer, der understøttes af Data Factory. 

1. **Efter behov**: I dette tilfælde databehandling miljøet fuldt administreres af Data Factory. Det oprettes automatisk af tjenesten Data Factory, før et job er sendt til proces data og fjernes, når jobbet er afsluttet. Du kan konfigurere og styre findelt indstillingerne for efter behov Beregn miljø til udførelse af jobbet, klyngeadministration af og starte sig selv handlinger. 
2. **Få dine egne**: I dette tilfælde kan du registrere din egen miljø (for eksempel HDInsight klynge) som en sammenkædet tjeneste i Data Factory. Den miljø administreres af dig og tjenesten Data Factory bruger den til at udføre aktiviteterne. 

Se [Beregne sammenkædede Services](data-factory-compute-linked-services.md) artikel for at få mere at vide om beregne tjenester, der understøttes af Data Factory. 


## <a name="summary"></a>Oversigt
Azure Data Factory understøtter følgende data transformation aktiviteter og Beregn miljøer for aktiviteterne. Transformation aktiviteter kan tilføjes til rørledninger enten individuelt eller sammenkædet med en anden aktivitet.

Data transformation aktivitet |  Beregne miljø 
:----------------------- | :--------------------
[Hive](data-factory-hive-activity.md) | HDInsight [Hadoop] 
[Gris](data-factory-pig-activity.md) | HDInsight [Hadoop]  
[MapReduce](data-factory-map-reduce.md) | HDInsight [Hadoop]  
[Hadoop Streaming](data-factory-hadoop-streaming-activity.md) | HDInsight [Hadoop]
[Maskine læ aktiviteter: udførelse af batchen og Opdater ressource](data-factory-azure-ml-batch-execution-activity.md) | Azure VM 
[Lagret Procedure](data-factory-stored-proc-activity.md) | Azure SQL, Azure SQL datawarehouse eller SQL Server |
[Data sø Analytics U-SQL](data-factory-usql-activity.md) | Azure Data sø Analytics 
[DotNet](data-factory-use-custom-activities.md) | HDInsight [Hadoop] eller Azure batchen
   

