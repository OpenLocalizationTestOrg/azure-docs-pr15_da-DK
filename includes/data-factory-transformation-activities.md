Azure Data Factory understøtter følgende transformation aktiviteter, der kan tilføjes til rørledninger enten individuelt eller sammenkædet med en anden aktivitet.

Data transformation aktivitet |  Beregne miljø 
:----------------------- | :--------------------
[Hive](../articles/data-factory/data-factory-hive-activity.md) | HDInsight [Hadoop] 
[Gris](../articles/data-factory/data-factory-pig-activity.md) | HDInsight [Hadoop]  
[MapReduce](../articles/data-factory/data-factory-map-reduce.md) | HDInsight [Hadoop]  
[Hadoop Streaming](../articles/data-factory/data-factory-hadoop-streaming-activity.md) | HDInsight [Hadoop]
[Maskine læ aktiviteter: udførelse af batchen og Opdater ressource](../articles/data-factory/data-factory-azure-ml-batch-execution-activity.md) | Azure VM 
[Lagret Procedure](../articles/data-factory/data-factory-stored-proc-activity.md) | Azure SQL, SQL Azure datawarehouse eller SQL Server |
[Data sø Analytics U-SQL](../articles/data-factory/data-factory-usql-activity.md) | Azure Data sø Analytics 
[DotNet](../articles/data-factory/data-factory-use-custom-activities.md) | HDInsight [Hadoop] eller Azure batchen
   
> [AZURE.NOTE] 
> Du kan bruge MapReduce aktivitet for at køre knallertmotor programmer på din HDInsight Spark klynge. Du kan finde oplysninger i [kalde knallertmotor programmer fra Azure Data Factory](../articles/data-factory/data-factory-spark.md) .
> Du kan oprette en brugerdefineret aktivitet for at køre R scripts på din HDInsight klynge med R, der er installeret. Se [køre R Script bruger Azure Data Factory](https://github.com/Azure/Azure-DataFactory/tree/master/Samples/RunRScriptUsingADFSample).