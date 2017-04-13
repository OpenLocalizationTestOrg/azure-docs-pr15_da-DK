<properties
   pageTitle="Integrering af sø datalager med andre Azure tjenester | Microsoft Azure"
   description="Forstå, hvordan sø datalager integreres med andre Azure tjenester"
   documentationCenter=""
   services="data-lake-store"
   authors="nitinme"
   manager="jhubbard"
   editor="cgronlun"/>

<tags
   ms.service="data-lake-store"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data"
   ms.date="10/28/2016"
   ms.author="nitinme"/>

# <a name="integrating-data-lake-store-with-other-azure-services"></a>Integrering af sø datalager med andre Azure tjenester

Azure datalager sø kan bruges sammen med andre Azure tjenester til at aktivere et større udvalg af scenarier. I følgende artikel viser de tjenester, sø datalager kan integreres med.

## <a name="use-data-lake-store-with-azure-hdinsight"></a>Brug sø datalager med Azure HDInsight

Du kan klargøre en [Azure HDInsight](https://azure.microsoft.com/documentation/learning-paths/hdinsight-self-guided-hadoop-training/) klynge, der bruger sø datalager som HDFS-kompatibel lagerplads. I denne udgave kan til Hadoop og Storm klynger i Windows og Linux, du bruge sø datalager kun som en ekstra lagerplads. Sådan klynger stadig bruge Azure lagerplads (WASB) som standard-lagerplads. For HBase klynger på Windows og Linux, kan du dog bruge sø datalager som standard-lagerplads eller ekstra lagerplads eller begge dele.

Du kan finde oplysninger om, hvordan du klargør en HDInsight klynge med sø datalager, i:

* [Klargøre en HDInsight klynge med datalager sø portalen til Azure](data-lake-store-hdinsight-hadoop-use-portal.md)
* [Klargøre en HDInsight klynge med datalager sø ved hjælp af Azure PowerShell](data-lake-store-hdinsight-hadoop-use-powershell.md)

**Foretrækker videoer?** Følge linkene nedenfor for at se videoer med oplysninger om, hvordan du bruger sø datalager med HDInsight klynger.

* [Oprette en HDInsight klynge med adgang til sø datalager](https://mix.office.com/watch/l93xri2yhtp2)
* Når klyngen er konfigureret, [Access-data i datalager sø ved hjælp af Hive og gris scripts](https://mix.office.com/watch/1n9g5w0fiqv1q)


## <a name="use-data-lake-store-with-azure-data-lake-analytics"></a>Brug sø datalager med Azure Data sø Analytics

[Azure Data sø Analytics](../data-lake-analytics/data-lake-analytics-overview.md) gør det muligt at arbejde med stor Data skaleres til skyen. Dynamisk bestemmelser ressourcer og kan du gøre analytics på TB eller lige Exabyte af data, der kan være gemt i et tal over understøttede datakilder, en af dem der sø datalager. Data sø Analytics er særligt optimeret til at arbejde med Azure sø datalager - give det højeste niveau af ydeevne, overførselshastighed og parallelization for dig stor data arbejdsmængder.

Flere oplysninger om, hvordan du bruger Data sø Analytics med sø datalager, under [Komme i gang med Data sø analyser ved hjælp af sø datalager](../data-lake-analytics/data-lake-analytics-get-started-portal.md).

**Foretrækker videoer?** Følge linkene nedenfor for at se videoer med oplysninger om, hvordan du bruger sø datalager med HDInsight klynger.

* [Oprette forbindelse Azure Data sø Analytics til Azure sø datalager](https://mix.office.com/watch/qwji0dc9rx9k)
* [Access Azure sø datalager via Data sø Analytics](https://mix.office.com/watch/1n0s45up381a8)


## <a name="use-data-lake-store-with-azure-data-factory"></a>Brug sø datalager med Azure Data Factory

Du kan bruge [Azure Data Factory](https://azure.microsoft.com/services/data-factory/) til indtager data fra Azure tabeller, Azure SQL-Database, Azure SQL DataWarehouse, antal BLOB-lager i Azure og lokale databaser. Der førsteklasses bestemmer i Azure økosystemet kan Azure Data Factory bruges til at dirigerer indtagelse af data fra disse kilde til Azure Data sø Store.

Flere oplysninger om, hvordan du bruger Azure Data Factory med sø datalager, under [flytte data til og fra datalager sø ved hjælp af Data Factory](../data-factory/data-factory-azure-datalake-connector.md).

**Videoer igen!** Se [Data organisering ved hjælp af Azure Data Factory til Azure sø datalager](https://mix.office.com/watch/1oa7le7t2u4ka). 

## <a name="copy-data-from-azure-storage-blobs-into-data-lake-store"></a>Kopiere data fra Azure-lager BLOB til sø datalager

Azure datalager sø indeholder et værktøj på kommandolinjen, AdlCopy, der gør det muligt at kopiere data fra Azure Blob-lager til en sø datalager-konto. Yderligere oplysninger finder du se [kopiere data fra Azure lagerplads BLOB til sø datalager](data-lake-store-copy-data-azure-storage-blob.md).

## <a name="copy-data-between-azure-sql-database-and-data-lake-store"></a>Kopiere data mellem Azure SQL-Database og sø datalager

Du kan bruge Apache Sqoop til at importere og eksportere data mellem Azure SQL-Database og sø datalager. Yderligere oplysninger finder du se [kopiere data mellem sø datalager og Azure SQL-database ved hjælp af Sqoop](data-lake-store-data-transfer-sql-sqoop.md).

**Se denne video** på [Ved hjælp af Apache Sqoop til at flytte data mellem relationelle kilder og Azure sø datalager](https://mix.office.com/watch/1butcdjxmu114).

## <a name="use-data-lake-store-with-stream-analytics"></a>Brug sø datalager med Stream Analytics

Du kan bruge sø datalager som et af output til at gemme data streames ved hjælp af Azure Stream analyser. Få mere at vide under [streamdata fra Azure lagerplads Blob til sø datalager brug af Azure Stream analyser](data-lake-store-stream-analytics.md).

## <a name="use-data-lake-store-with-power-bi"></a>Brug sø datalager med Power BI

Du kan bruge Power BI til at importere data fra en sø datalager konto til at analysere og visualisere data. Se [analysér data i sø datalager ved hjælp af Power BI](data-lake-store-power-bi.md)kan finde flere oplysninger.

## <a name="use-data-lake-store-with-data-catalog"></a>Brug sø datalager med datakatalog

Du kan registrere data fra sø datalager til Azure datakatalog så dataene kan findes i hele organisationen. Se [registrere data fra sø datalager i Azure datakatalog](data-lake-store-with-data-catalog.md)kan finde flere oplysninger.


## <a name="see-also"></a>Se også

- [Oversigt over Azure sø datalager](data-lake-store-overview.md)
- [Komme i gang med datalager sø ved hjælp af portalen](data-lake-store-get-started-portal.md)
- [Komme i gang med datalager sø ved hjælp af PowerShell](data-lake-store-get-started-powershell.md)  
