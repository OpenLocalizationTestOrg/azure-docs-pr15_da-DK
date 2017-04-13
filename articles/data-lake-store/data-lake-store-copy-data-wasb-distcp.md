<properties
   pageTitle="Kopierer data til og fra WASB til datalager sø ved hjælp af Distcp | Microsoft Azure"
   description="Brug Distcp til at kopiere data til og fra Azure-lager BLOB til sø datalager"
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
   ms.date="10/28/2016"
   ms.author="nitinme"/>

# <a name="use-distcp-to-copy-data-between-azure-storage-blobs-and-data-lake-store"></a>Brug Distcp til at kopiere data mellem Azure lagerplads BLOB og sø datalager

> [AZURE.SELECTOR]
- [Brug af DistCp](data-lake-store-copy-data-wasb-distcp.md)
- [Brug af AdlCopy](data-lake-store-copy-data-azure-storage-blob.md)


Når du har oprettet en HDInsight klynge, der har adgang til en sø datalager-konto, kan du bruge Hadoop økosystemet værktøjer som Distcp til at kopiere data **til og fra** en HDInsight klynge lager (WASB) til en sø datalager-konto. I denne artikel indeholder en vejledning til, hvordan dette.

##<a name="prerequisites"></a>Forudsætninger

Før du begynder i denne artikel, skal du have følgende:

- **En Azure-abonnement**. Se [få Azure gratis prøveversion](https://azure.microsoft.com/pricing/free-trial/).
- **Aktivere abonnementet Azure** til sø datalager offentlige preview. Se [vejledningen](data-lake-store-get-started-portal.md#signup).
- **Azure HDInsight klynge** med adgang til en sø datalager-konto. Se [oprette en HDInsight klynge med sø datalager](data-lake-store-hdinsight-hadoop-use-portal.md). Kontrollér, at du aktiverer Fjernskrivebord for-klyngen.

## <a name="do-you-learn-fast-with-videos"></a>Du lære hurtigt med videoer?

[Se denne video](https://mix.office.com/watch/1liuojvdx6sie) om, hvordan du kopierer data mellem Azure lagerplads BLOB og sø datalager ved hjælp af DistCp.

## <a name="use-distcp-from-remote-desktop-windows-cluster-or-ssh-linux-cluster"></a>Bruge Distcp fra Fjernskrivebord (Windows-klynge) eller SSH (Linux klynge)

En HDInsight klynge leveres med værktøjet Distcp, som kan bruges til at kopiere data fra forskellige kilder til en HDInsight klynge. Hvis du har konfigureret HDInsight klynge for at bruge sø datalager som en ekstra lagerplads, kan værktøjet Distcp være anvendte out-of-the-box til at kopiere data til og fra en sø datalager konto. I dette afsnit kan vi se på hvordan du bruger værktøjet Distcp.

1. Hvis du har en Windows-klynge, remote til en HDInsight klynge, der har adgang til en sø datalager-konto. Yderligere oplysninger finder du [oprette forbindelse til klynger ved hjælp af RDP](../hdinsight/hdinsight-administer-use-management-portal.md#connect-to-clusters-using-rdp). Åbn kommandolinjen Hadoop fra klynge skrivebord.

    Hvis du har en Linux klynge, kan du bruge SSH til at oprette forbindelse til klyngen. Se [oprette forbindelse til en Linux-baserede HDInsight klynge](../hdinsight/hdinsight-hadoop-linux-use-ssh-unix.md#connect-to-a-linux-based-hdinsight-cluster). Køre kommandoer fra SSH prompten.

3. Kontrollér, om du kan få adgang til Azure lagerplads BLOB (WASB). Kør følgende kommando:

        hdfs dfs –ls wasb://<container_name>@<storage_account_name>.blob.core.windows.net/

    Dette kan give en liste over indhold i blob storage.

4. Kontrollér på samme måde, uanset om du kan få adgang til kontoen sø datalager fra klyngen. Kør følgende kommando:

        hdfs dfs -ls adl://<data_lake_store_account>.azuredatalakestore.net:443/

    Dette kan give en liste over filer/mapper i kontoen sø datalager.

5. Brug Distcp til at kopiere data fra WASB til en sø datalager-konto.

        hadoop distcp wasb://<container_name>@<storage_account_name>.blob.core.windows.net/example/data/gutenberg adl://<data_lake_store_account>.azuredatalakestore.net:443/myfolder

    Dette kopierer indholdet af **** mappen/eksempel/data/gutenberg/i WASB til **/myfolder** i kontoen sø datalager.

6. På samme måde, bruge Distcp til at kopiere data fra sø datalager konto til WASB.

        hadoop distcp adl://<data_lake_store_account>.azuredatalakestore.net:443/myfolder wasb://<container_name>@<storage_account_name>.blob.core.windows.net/example/data/gutenberg

    Dette kopierer indholdet af **/myfolder** i kontoen sø datalager **** til/eksempel/data/gutenberg/mappe i WASB.

## <a name="see-also"></a>Se også

- [Kopiere data fra Azure-lager BLOB til sø datalager](data-lake-store-copy-data-azure-storage-blob.md)
- [Sikre data i sø datalager](data-lake-store-secure-data.md)
- [Bruge Azure Data sø Analytics med sø datalager](../data-lake-analytics/data-lake-analytics-get-started-portal.md)
- [Bruge Azure HDInsight med sø datalager](data-lake-store-hdinsight-hadoop-use-portal.md)
