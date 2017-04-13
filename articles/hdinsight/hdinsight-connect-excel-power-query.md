<properties
    pageTitle="Oprette forbindelse Excel til Hadoop med Power-forespørgsel | Microsoft Azure"
    description="Lær at drage fordel af business intelligence-komponenter og bruge Power-forespørgsel til Excel til access-data, der er gemt i Hadoop på HDInsight."
    services="hdinsight"
    documentationCenter=""
    tags="azure-portal"
    authors="mumian"
    manager="jhubbard"
    editor="cgronlun"/>

<tags
    ms.service="hdinsight"
    ms.workload="big-data"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/19/2016"
    ms.author="jgao"/>


#<a name="connect-excel-to-hadoop-by-using-power-query"></a>Oprette forbindelse til Excel til Hadoop ved hjælp af Power-forespørgsel

Én nøglefunktionerne Microsoft stor data løsningens er integration af Microsoft business intelligence (BI)-komponenter med Hadoop klynger i Azure HDInsight. En primær eksempel på denne integration er muligheden for at oprette forbindelse Excel til kontoen Azure-lager, der indeholder de data, der er knyttet til din Hadoop klynge ved hjælp af Microsoft Power-forespørgsel til Excel-tilføjelsesprogram. I denne artikel vejledes du gennem om at konfigurere og bruge Power-forespørgsel til forespørgselsdata, der er knyttet til et Hadoop-klynge administrerede med HDInsight.

> [AZURE.NOTE] Mens trinnene i denne artikel kan bruges med en Linux eller Windows-baseret HDInsight klynge, er Windows påkrævet for klientarbejdsstationen.

### <a name="prerequisites"></a>Forudsætninger

Før du begynder i denne artikel, skal du have følgende:

- **En HDInsight klynge**. For at konfigurere en skal du se [Introduktion til Azure HDInsight][hdinsight-get-started].
- **A arbejdsstation** , der kører Windows 7, Windows Server 2008 R2 eller et nyere operativsystem.
- **Office 2013 Professional Plus, Office 365 Pro plus, Excel 2013 Standalone eller Office 2010 Professional Plus**.


## <a name="install-power-query"></a>Installere Power-forespørgsel

Power-forespørgsel kan bruges til at importere data fra en lang række kilder i Microsoft Excel, hvor den kan power BI-værktøjer som PowerPivot og Power View. Power-forespørgsel kan bl.a importere data, der er blevet output eller, der er dannet af et Hadoop-job, der kører på en HDInsight klynge.

Hente Microsoft Power-forespørgsel til Excel fra [Microsoft Download Center] [ powerquery-download] og installere den.

## <a name="import-hdinsight-data-into-excel"></a>Importere HDInsight data til Excel

Tilføjelsesprogrammet Power-forespørgsel til Excel at gør det nemt at importere data fra din HDInsight klynge i Excel, hvor BI-værktøjer, som PowerPivot og Power-plotning kan bruges til at undersøge, analysere og præsentere dataene.

**Importere data fra en HDInsight klynge**

1. Åbn Excel.

2. Oprette en ny tom projektmappe.

3. Klik på menuen **Power-forespørgsel** , skal du klikke på **Fra Azure**, og klik derefter på **Fra Microsoft Azure HDInsight**.

    ![HDI. PowerQuery.SelectHdiSource][image-hdi-powerquery-hdi-source]

    **Note:** Hvis du ikke kan se menuen **Power-forespørgsel** skal du gå til **filen** > **Indstillinger** > **Tilføjelsesprogrammer**, og vælg **COM-tilføjelsesprogrammer** fra **rullemenuen Administrer nederst på siden** . Vælg knappen **Gå...** , og Bekræft, at afkrydsningsfeltet ud for Power-forespørgsel til Excel-tilføjelsesprogram er blevet kontrolleret.

    **Note:** Power-forespørgsel kan du også importere data fra HDFS ved at klikke på **Fra andre kilder**.

3. Skriv navnet på kontoen Azure Blob-lager, der er knyttet til din klynge for **Kontonavn**, og klik derefter på **OK**. Det kan være [standardkontoen lagerplads](hdinsight-administer-use-management-portal.md#find-the-default-storage-account) eller en sammenkædet lagerplads konto.  Formatet er *https://<StorageAccountName>.blob.core.windows.net/*.

4. Angiv nøgle for kontoen Blob storage for **Kontonøgle**, og klik derefter på **Gem**. (Du skal gøre dette kun den første gang du åbner denne butik.)

5. I **navigationsruden i venstre side af Query Editor,** skal du dobbeltklikke på Blob storage objektbeholder navn. Som standard er objektbeholdernavnet det samme navn som klyngenavnet.

6. Find **HiveSampleData.txt** i kolonnen **navn** (stien til mappen er **… / hive/lager/hivesampletable/**), og klik derefter på **binær** til venstre på HiveSampleData.txt. HiveSampleData.txt leveres med alle klynge. Du kan vælge at bruge din egen fil.

    ![HDI. PowerQuery.ImportData][image-hdi-powerquery-importdata]

7. Hvis du vil, kan du omdøbe kolonnenavne. Når du er klar, skal du klikke på **Luk og Indlæs**.  Dataene er blevet indlæst til din projektmappe:

    ![HDI. PowerQuery.ImportedTable][image-hdi-powerquery-imported-table]

## <a name="next-steps"></a>Næste trin

I denne artikel, du har lært Sådan bruges Power-forespørgsel til at hente data fra HDInsight i Excel. På samme måde, kan du hente data fra HDInsight til Azure SQL-Database. Det er også muligt at overføre data til HDInsight. Hvis du vil vide mere, skal du se følgende artikler:

* [Oprette forbindelse Excel til HDInsight med Microsoft Hive ODBC-Driver][hdinsight-ODBC]
* [Overføre Data til HDInsight][hdinsight-upload-data]

[hdinsight-ODBC]: hdinsight-connect-excel-hive-odbc-driver.md
[hdinsight-get-started]: hdinsight-hadoop-linux-tutorial-get-started.md
[hdinsight-upload-data]: hdinsight-upload-data.md

[image-hdi-powerquery-hdi-source]: ./media/hdinsight-connect-excel-power-query/HDI.PowerQuery.SelectHdiSource.png
[image-hdi-powerquery-importdata]: ./media/hdinsight-connect-excel-power-query/HDI.PowerQuery.ImportData.png
[image-hdi-powerquery-imported-table]: ./media/hdinsight-connect-excel-power-query/HDI.PowerQuery.ImportedTable.PNG

[powerquery-download]: http://go.microsoft.com/fwlink/?LinkID=286689
