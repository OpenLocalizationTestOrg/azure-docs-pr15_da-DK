<properties
    pageTitle="Brug Hadoop Sqoop i Linux-baserede HDInsight | Microsoft Azure"
    description="Lær at køre Sqoop importere og eksportere mellem et Hadoop-Linux-baseret på HDInsight klynge og Azure SQL-database."
    editor="cgronlun"
    manager="jhubbard"
    services="hdinsight"
    documentationCenter=""
    authors="Blackmist"
    tags="azure-portal"/>

<tags
    ms.service="hdinsight"
    ms.workload="big-data"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/03/2016"
    ms.author="larryfr"/>

#<a name="use-sqoop-with-hadoop-in-hdinsight-ssh"></a>Bruge Sqoop med Hadoop i HDInsight (SSH)

[AZURE.INCLUDE [sqoop-selector](../../includes/hdinsight-selector-use-sqoop.md)]

Lær, hvordan du bruger Sqoop til at importere og eksportere mellem en Linux-baserede HDInsight klynge og Azure SQL-Database eller SQL Server-database.

> [AZURE.NOTE] Trinnene i artiklen Brug SSH til at oprette forbindelse til en Linux-baserede HDInsight klynge. Windows-klienter kan også bruge Azure PowerShell og HDInsight .NET SDK til at arbejde med Sqoop på Linux-baserede klynger. Brug tabulatorvælgeren til at åbne disse artikler.

##<a name="prerequisites"></a>Forudsætninger

Inden du starter selvstudiet, skal du have følgende:

- **A Hadoop klynge i HDInsight** og en __Azure SQL-Database__: trinnene i dette dokument er baseret på den klynge og database, der er oprettet ved hjælp af [Opret klynge og SQL-database](hdinsight-use-sqoop.md#create-cluster-and-sql-database) dokumentet. Hvis du allerede har en HDInsight klynge og SQL-Database, kan du erstatte rettighederne for værdierne, der bruges i dette dokument.
- **Arbejdsstation**: en computer med en SSH-klient.

##<a name="install-freetds"></a>Installere FreeTDS

1. Brug SSH til at oprette forbindelse til Linux-baserede HDInsight klynge. Adressen, der skal bruges, når forbindelsen er `CLUSTERNAME-ssh.azurehdinsight.net` og port er `22`.

    Du kan finde flere oplysninger om brug af SSH til at oprette forbindelse til HDInsight, følgende dokumenter:

    * **Linux, Unix eller OS X-klienter**: se [oprette forbindelse til en Linux-baserede HDInsight klynge fra Linux, OS X eller Unix](hdinsight-hadoop-linux-use-ssh-unix.md#connect-to-a-linux-based-hdinsight-cluster)

    * **Windows-klienter**: se [oprette forbindelse til en Linux-baserede HDInsight klynge fra Windows](hdinsight-hadoop-linux-use-ssh-windows.md#connect-to-a-linux-based-hdinsight-cluster)

3. Brug følgende kommando for at installere FreeTDS:

        sudo apt-get --assume-yes install freetds-dev freetds-bin

    FreeTDS bruges i flere trin til at oprette forbindelse til SQL-Database.

##<a name="create-the-table-in-sql-database"></a>Oprette tabellen i SQL-Database

> [AZURE.IMPORTANT] Hvis du bruger en HDInsight klynge og SQL-Database, der er oprettet ved hjælp af trinnene i [oprette klynge og SQL-database](hdinsight-use-sqoop.md)skal du ignorere trin i dette afsnit, som den database og tabel er oprettet som en del af trinnene i dette dokument.

1. Brug følgende kommando fra SSH forbindelsen til HDInsight, til at oprette forbindelse til SQL-databaseserver og Kreta den tabel, der skal bruges i resten af disse trin:

        TDSVER=8.0 tsql -H <serverName>.database.windows.net -U <adminLogin> -P <adminPassword> -p 1433 -D sqooptest

    Du modtager output, der svarer til følgende:

        locale is "en_US.UTF-8"
        locale charset is "UTF-8"
        using default charset "UTF-8"
        Default database being set to sqooptest
        1>

5. På den `1>` Spørg, skal du angive følgende linjer:

        CREATE TABLE [dbo].[mobiledata](
        [clientid] [nvarchar](50),
        [querytime] [nvarchar](50),
        [market] [nvarchar](50),
        [deviceplatform] [nvarchar](50),
        [devicemake] [nvarchar](50),
        [devicemodel] [nvarchar](50),
        [state] [nvarchar](50),
        [country] [nvarchar](50),
        [querydwelltime] [float],
        [sessionid] [bigint],
        [sessionpagevieworder] [bigint])
        GO
        CREATE CLUSTERED INDEX mobiledata_clustered_index on mobiledata(clientid)
        GO

    Når den `GO` sætning er angivet, sætningerne forrige skal evalueres. Først skal der oprettes **mobiledata** tabel og derefter et grupperet indeks er føjet til den (kræves af SQL-Database).

    Brug følgende til at bekræfte, at tabellen er blevet oprettet:

        SELECT * FROM information_schema.tables
        GO

    Du burde se output, der svarer til følgende:

        TABLE_CATALOG   TABLE_SCHEMA    TABLE_NAME      TABLE_TYPE
        sqooptest       dbo     mobiledata      BASE TABLE

8. Angiv `exit` på den `1>` Spørg at afslutte værktøjet tsql.

##<a name="sqoop-export"></a>Sqoop Eksportér

3. Fra SSH forbindelsen til HDInsight, se følgende kommando for at bekræfte, at Sqoop kan se din SQL-Database:

        sqoop list-databases --connect jdbc:sqlserver://<serverName>.database.windows.net:1433 --username <adminLogin> --password <adminPassword>

    Dette skal returnere en liste over databaser, herunder den **sqooptest** database, du oprettede tidligere.

4. Brug følgende kommando for at eksportere data fra **hivesampletable** til tabellen **mobiledata** :

        sqoop export --connect 'jdbc:sqlserver://<serverName>.database.windows.net:1433;database=sqooptest' --username <adminLogin> --password <adminPassword> --table 'mobiledata' --export-dir 'wasbs:///hive/warehouse/hivesampletable' --fields-terminated-by '\t' -m 1

    Dette giver Sqoop til at oprette forbindelse til SQL-Database til **sqooptest** databasen, og eksportere data fra den **wasbs: / / / hive/warehouse/hivesampletable** (fysisk filer til *hivesampletable*) til tabellen **mobiledata** .

5. Når kommandoen er fuldført, kan du bruge følgende til at oprette forbindelse til databasen ved hjælp af TSQL:

        TDSVER=8.0 tsql -H <serverName>.database.windows.net -U <adminLogin> -P <adminPassword> -p 1433 -D sqooptest

    Når forbindelse, kan du bruge følgende udtalelser til at bekræfte, at dataene er eksporteret til tabellen **mobiledata** :

        SELECT * FROM mobiledata
        GO

    Du bør se en oversigt over dataene i tabellen. Skriv `exit` at afslutte værktøjet tsql.

##<a name="sqoop-import"></a>Sqoop import

1. Bruge følgende for at importere data fra tabellen **mobiledata** i SQL-Database til den **wasbs: / / / selvstudier/usesqoop/importeddata** mappe på HDInsight:

        sqoop import --connect 'jdbc:sqlserver://<serverName>.database.windows.net:1433;database=sqooptest' --username <adminLogin> --password <adminPassword> --table 'mobiledata' --target-dir 'wasbs:///tutorials/usesqoop/importeddata' --fields-terminated-by '\t' --lines-terminated-by '\n' -m 1

    De importerede data, har de felter, der er adskilt af et tabulatortegn, og linjerne afsluttes med en ny linje-tegn.

2. Når importen er færdig, skal du bruge følgende kommando til liste over dataene ud i den nye mappe:

        hadoop fs -text wasbs:///tutorials/usesqoop/importeddata/part-m-00000

##<a name="using-sql-server"></a>Brug af SQL Server

Du kan også bruge Sqoop til at importere og eksportere data fra SQL Server, i dit datacenter eller på en virtuel maskine Azure som vært. Forskelle mellem at bruge SQL-Database og SQL Server er:

* Både HDInsight og SQL Server skal være i samme Azure virtuelt netværk

    > [AZURE.NOTE] HDInsight understøtter placering-baserede kun virtuelle netværk, og den virker ikke i øjeblikket med forbindelse gruppe-baserede virtuelle netværk.

    Når du bruger SQL Server i dit datacenter, skal du konfigurere det virtuelle netværk som *- til-websted* eller *punkt-til-websted*.

    > [AZURE.NOTE] Til **punkt-til-site** virtuelle netværk, skal SQL Server køre VPN-klient konfigurationsprogrammet, som er tilgængelig fra **Dashboard** af din Azure virtuelle netværkskonfiguration.

    Se [Oversigt over virtuelle netværk](../virtual-network/virtual-networks-overview.md)kan finde flere oplysninger Azure virtuelt netværk.

* SQL Server skal være konfigureret til at tillade SQL-godkendelse. Se [vælge en anden godkendelsestilstand](https://msdn.microsoft.com/ms144284.aspx) kan finde flere oplysninger

* Du kan være nødvendigt at konfiguration af SQL Server for at acceptere remote forbindelser. Se, [hvordan du foretage fejlfinding af forbindelse til SQL Server-database engine](http://social.technet.microsoft.com/wiki/contents/articles/2102.how-to-troubleshoot-connecting-to-the-sql-server-database-engine.aspx) kan finde flere oplysninger

* Du skal oprette **sqooptest** databasen i SQL Server ved hjælp af et værktøj som **SQL Server Management Studio** eller **tsql** - trin til brug af Azure CLI bruges kun til Azure SQL-Database

    Sætningerne TSQL til at oprette tabellen **mobiledata** ligner dem, der bruges til SQL-Database med undtagelse af oprettelse af en clusterd indeks - dette er ikke påkrævet til SQL Server:

        CREATE TABLE [dbo].[mobiledata](
        [clientid] [nvarchar](50),
        [querytime] [nvarchar](50),
        [market] [nvarchar](50),
        [deviceplatform] [nvarchar](50),
        [devicemake] [nvarchar](50),
        [devicemodel] [nvarchar](50),
        [state] [nvarchar](50),
        [country] [nvarchar](50),
        [querydwelltime] [float],
        [sessionid] [bigint],
        [sessionpagevieworder] [bigint])

* Når du opretter forbindelse til SQL Server fra HDInsight, kan du skal bruge IP-adressen på den SQL Server, medmindre du har konfigureret et System DNS (Domain Name) for at finde navne på Azure virtuelle netværket. Eksempel:

        sqoop import --connect 'jdbc:sqlserver://10.0.1.1:1433;database=sqooptest' --username <adminLogin> --password <adminPassword> --table 'mobiledata' --target-dir 'wasbs:///tutorials/usesqoop/importeddata' --fields-terminated-by '\t' --lines-terminated-by '\n' -m 1

##<a name="limitations"></a>Begrænsninger

* Flere eksport - med Linux-baserede HDInsight, Sqoop forbindelsen bruges til at eksportere data til Microsoft SQL Server eller Azure SQL-Database understøtter i øjeblikket ikke flere indsættes.

* "Samling"-med Linux-baserede HDInsight, når du bruger den `-batch` skifte, når du udfører indsættes, Sqoop udfører flere indsættes i stedet for "samling" Indsæt handlinger.

##<a name="next-steps"></a>Næste trin

Nu har du lært, hvordan du bruger Sqoop. Hvis du vil vide mere, skal du se:

- [Bruge Oozie med HDInsight][hdinsight-use-oozie]: Brug Sqoop handling i en arbejdsproces for Oozie.
- [Analysere flight forsinkelse data ved hjælp af HDInsight][hdinsight-analyze-flight-data]: Brug Hive til at analysere flight forsinke data, og brug derefter Sqoop til at eksportere data til en Azure SQL-database.
- [Overføre data til HDInsight][hdinsight-upload-data]: finde andre metoder til at overføre data til HDInsight/Azure Blob-lager.



[hdinsight-versions]:  hdinsight-component-versioning.md
[hdinsight-provision]: hdinsight-provision-clusters.md
[hdinsight-get-started]: hdinsight-hadoop-linux-tutorial-get-started.md
[hdinsight-storage]: ../hdinsight-hadoop-use-blob-storage.md
[hdinsight-analyze-flight-data]: hdinsight-analyze-flight-delay-data.md
[hdinsight-use-oozie]: hdinsight-use-oozie.md
[hdinsight-upload-data]: hdinsight-upload-data.md
[hdinsight-submit-jobs]: hdinsight-submit-hadoop-jobs-programmatically.md

[sqldatabase-get-started]: ../sql-database-get-started.md
[sqldatabase-create-configue]: ../sql-database-create-configure.md

[powershell-start]: http://technet.microsoft.com/library/hh847889.aspx
[powershell-install]: powershell-install-configure.md
[powershell-script]: http://technet.microsoft.com/library/ee176949.aspx

[sqoop-user-guide-1.4.4]: https://sqoop.apache.org/docs/1.4.4/SqoopUserGuide.html
