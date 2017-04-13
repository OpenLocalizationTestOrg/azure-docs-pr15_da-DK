<properties 
   pageTitle="Kopiere data mellem sø datalager og Azure SQL-database ved hjælp af Sqoop | Microsoft Azure"
   description="Brug Sqoop til at kopiere data mellem Azure SQL-Database og sø datalager" 
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

# <a name="copy-data-between-data-lake-store-and-azure-sql-database-using-sqoop"></a>Kopiere data mellem sø datalager og Azure SQL-database ved hjælp af Sqoop

Lær, hvordan du bruger Apache Sqoop til at importere og eksportere data mellem Azure SQL-Database og sø datalager.
 

## <a name="what-is-sqoop"></a>Hvad er Sqoop?

Stor dataprogrammer er et neutralt valg til at behandle ustrukturerede og semistrukturerede data, som logfiler og filer. Der kan dog også være behov for at behandle strukturerede data, der er gemt i relationsdatabaser.

[Apache Sqoop](https://sqoop.apache.org/docs/1.4.4/SqoopUserGuide.html) er et værktøj, der er udviklet til at overføre data mellem relationsdatabaser og en stor datalager, som sø datalager. Du kan bruge det til at importere data fra en relationsdatabase-system (RDBMS) som Azure SQL-Database til sø datalager. Du kan derefter transformere og analysere data med stor data arbejdsmængder og derefter eksportere dataene i en RDBMS igen. I dette selvstudium bruger du en Azure SQL-Database som din relationsdatabase til importere/eksportere fra.
 

## <a name="prerequisites"></a>Forudsætninger

Før du begynder i denne artikel, skal du have følgende:

- **En Azure-abonnement**. Se [få Azure gratis prøveversion](https://azure.microsoft.com/pricing/free-trial/).
- **Aktivere abonnementet Azure** til sø datalager offentlige preview. Se [vejledningen](data-lake-store-get-started-portal.md#signup). 
- **Azure HDInsight klynge** med adgang til en sø datalager-konto. Se [oprette en HDInsight klynge med sø datalager](data-lake-store-hdinsight-hadoop-use-portal.md). I denne artikel antages det, du har en HDInsight Linux klynge med sø datalager adgang.
- **Azure SQL-Database**. Oplysninger om, hvordan du opretter en, under [oprette en Azure SQL-database](../sql-database/sql-database-get-started.md)

## <a name="do-you-learn-fast-with-videos"></a>Du lære hurtigt med videoer?

[Se denne video](https://mix.office.com/watch/1butcdjxmu114) om, hvordan du kopierer data mellem Azure lagerplads BLOB og sø datalager ved hjælp af DistCp.

## <a name="create-sample-tables-in-the-azure-sql-database"></a>Oprette eksempeltabeller på i Azure SQL-Database

1. Oprette skal starte med to eksempeltabeller i Azure SQL-Database. Brug [SQL Server Management Studio](../sql-database/sql-database-connect-query-ssms.md) eller Visual Studio til at oprette forbindelse til Azure SQL-Database og derefter køre følgende forespørgsler.

    **Oprette Tabel1**

        CREATE TABLE [dbo].[Table1]( 
        [ID] [int] NOT NULL, 
        [FName] [nvarchar](50) NOT NULL, 
        [LName] [nvarchar](50) NOT NULL, 
        CONSTRAINT [PK_Table_4] PRIMARY KEY CLUSTERED 
            ( 
                [ID] ASC 
            ) 
        ) ON [PRIMARY] 
        GO

    **Oprette Tabel2**

        CREATE TABLE [dbo].[Table2]( 
        [ID] [int] NOT NULL, 
        [FName] [nvarchar](50) NOT NULL, 
        [LName] [nvarchar](50) NOT NULL, 
        CONSTRAINT [PK_Table_4] PRIMARY KEY CLUSTERED 
            ( 
                [ID] ASC 
            ) 
        ) ON [PRIMARY] 
        GO

2. Tilføj nogle eksempeldata i **Tabel1**. Lad **Tabel2** stå tomt. Vi vil importere data fra **Tabel1** til sø datalager. Vi vil derefter eksportere data fra sø datalager til **Tabel2**. Kør følgende kodestykke.

         
        INSERT INTO [dbo].[Table1] VALUES (1,'Neal','Kell'), (2,'Lila','Fulton'), (3, 'Erna','Myers'), (4,'Annette','Simpson'); 
  

## <a name="use-sqoop-from-an-hdinsight-cluster-with-access-to-data-lake-store"></a>Bruge Sqoop fra en HDInsight klynge med adgang til Data sø Store

En HDInsight klynge allerede Sqoop-pakker, som er tilgængelige. Hvis du har konfigureret HDInsight klynge for at bruge sø datalager som en ekstra lagerplads, kan du bruge Sqoop (uden nogen ændringer i konfigurationen) til at importere/eksportere data mellem en relationel database (i dette eksempel Azure SQL-Database) og en sø datalager-konto. 

1. Dette selvstudium skal vi antager, at du har oprettet en Linux klynge, så du skal bruge SSH til at oprette forbindelse til klyngen. Se [oprette forbindelse til en Linux-baserede HDInsight klynge](hdinsight-hadoop-linux-use-ssh-unix.md#connect-to-a-linux-based-hdinsight-cluster).

2. Kontrollér, om du kan få adgang til kontoen sø datalager fra klyngen. Køre følgende kommando fra SSH prompt:

        
        hdfs dfs -ls adl://<data_lake_store_account>.azuredatalakestore.net/

    Dette kan give en liste over filer/mapper i kontoen sø datalager.

### <a name="import-data-from-azure-sql-database-into-data-lake-store"></a>Importere data fra Azure SQL-Database til sø datalager

3. Gå til den mappe, hvor Sqoop pakker er tilgængelige. Typisk dette bliver på `/usr/hdp/<version>/sqoop/bin`. 

4. Importere data fra **Tabel1** på sø datalager-kontoen. Brug følgende syntaks:

        
        sqoop-import --connect "jdbc:sqlserver://<sql-database-server-name>.database.windows.net:1433;username=<username>@<sql-database-server-name>;password=<password>;database=<sql-database-name>" --table Table1 --target-dir adl://<data-lake-store-name>.azuredatalakestore.net/Sqoop/SqoopImportTable1

    Bemærk denne **sql-database-server-navn** pladsholder repræsenterer navnet på den server, hvor der kører SQL Azure-databasen. **SQL-database-navn** pladsholder repræsenterer den faktiske databasenavn.

    For eksempel

        
        sqoop-import --connect "jdbc:sqlserver://mysqoopserver.database.windows.net:1433;username=nitinme@mysqoopserver;password=<password>;database=mysqoopdatabase" --table Table1 --target-dir adl://myadlstore.azuredatalakestore.net/Sqoop/SqoopImportTable1

5. Kontrollér, at dataene er blevet overført til kontoen sø datalager. Kør følgende kommando:

        
        hdfs dfs -ls adl://hdiadlstore.azuredatalakestore.net/Sqoop/SqoopImportTable1/

    Du bør se følgende output.

        
        -rwxrwxrwx   0 sshuser hdfs          0 2016-02-26 21:09 adl://hdiadlstore.azuredatalakestore.net/Sqoop/SqoopImportTable1/_SUCCESS
        -rwxrwxrwx   0 sshuser hdfs         12 2016-02-26 21:09 adl://hdiadlstore.azuredatalakestore.net/Sqoop/SqoopImportTable1/part-m-00000
        -rwxrwxrwx   0 sshuser hdfs         14 2016-02-26 21:09 adl://hdiadlstore.azuredatalakestore.net/Sqoop/SqoopImportTable1/part-m-00001
        -rwxrwxrwx   0 sshuser hdfs         13 2016-02-26 21:09 adl://hdiadlstore.azuredatalakestore.net/Sqoop/SqoopImportTable1/part-m-00002
        -rwxrwxrwx   0 sshuser hdfs         18 2016-02-26 21:09 adl://hdiadlstore.azuredatalakestore.net/Sqoop/SqoopImportTable1/part-m-00003

    Hver **del-m -** * fil svarer til en række i kildetabellen * *Tabel1**. Du kan få vist indholdet af del-m -* filer for at kontrollere.


### <a name="export-data-from-data-lake-store-into-azure-sql-database"></a>Eksportere data fra sø datalager i Azure SQL-Database

6. Eksportere dataene fra sø datalager konto til tabellen tomme, **Tabel2**i Azure SQL-Database. Brug følgende syntaks.

        
        sqoop-export --connect "jdbc:sqlserver://<sql-database-server-name>.database.windows.net:1433;username=<username>@<sql-database-server-name>;password=<password>;database=<sql-database-name>" --table Table2 --export-dir adl://<data-lake-store-name>.azuredatalakestore.net/Sqoop/SqoopImportTable1 --input-fields-terminated-by ","

    For eksempel

        
        sqoop-export --connect "jdbc:sqlserver://mysqoopserver.database.windows.net:1433;username=nitinme@mysqoopserver;password=<password>;database=mysqoopdatabase" --table Table2 --export-dir adl://myadlstore.azuredatalakestore.net/Sqoop/SqoopImportTable1 --input-fields-terminated-by ","

6. Kontrollér, at dataene blev overført til tabellen SQL-Database. Brug [SQL Server Management Studio](../sql-database/sql-database-connect-query-ssms.md) eller Visual Studio til at oprette forbindelse til Azure SQL-Database og derefter køre følgende forespørgsel.

        
        SELECT * FROM TABLE2

    Dette bør have følgende output.

        ID  FName   LName
        ------------------
        1   Neal    Kell
        2   Lila    Fulton
        3   Erna    Myers
        4   Annette Simpson

## <a name="see-also"></a>Se også

- [Kopiere data fra Azure-lager BLOB til sø datalager](data-lake-store-copy-data-azure-storage-blob.md)
- [Sikre data i sø datalager](data-lake-store-secure-data.md)
- [Bruge Azure Data sø Analytics med sø datalager](../data-lake-analytics/data-lake-analytics-get-started-portal.md)
- [Bruge Azure HDInsight med sø datalager](data-lake-store-hdinsight-hadoop-use-portal.md)
