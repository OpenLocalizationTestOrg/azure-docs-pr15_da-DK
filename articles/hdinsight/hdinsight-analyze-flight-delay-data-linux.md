<properties 
    pageTitle="Analysere flight forsinkelse data med Hive på Linux-baserede HDInsight | Microsoft Azure" 
    description="Lær, hvordan du bruger Hive til at analysere flight data på Linux-baserede HDInsight, og derefter eksportere dataene til SQL-Database ved hjælp af Sqoop." 
    services="hdinsight" 
    documentationCenter="" 
    authors="Blackmist" 
    manager="jhubbard" 
    editor="cgronlun"
    tags="azure-portal"/>

<tags 
    ms.service="hdinsight" 
    ms.workload="big-data" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="10/11/2016" 
    ms.author="larryfr"/>

#<a name="analyze-flight-delay-data-by-using-hive-in-hdinsight"></a>Analysere flight forsinkelse data ved hjælp af Hive i HDInsight

Lær at analysere flight forsinkelse data ved hjælp af Hive på Linux-baserede HDInsight og derefter eksportere dataene til Azure SQL-Database ved hjælp af ved hjælp af Sqoop.

> [AZURE.NOTE] Mens individuelle elementer i dette dokument kan bruges med Windows-baseret HDInsight klynger (Python og Hive for eksempel), der er specifikke for Linux-baserede klynger mange trin. Få en vejledning, der fungerer sammen med en Windows-baseret klynge i [analysér flight forsinkelse data ved hjælp af Hive i HDInsight](hdinsight-analyze-flight-delay-data.md)

###<a name="prerequisites"></a>Forudsætninger

Inden du starter selvstudiet, skal du have følgende:

- **En Azure-abonnement**. Se [få Azure gratis prøveversion](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).

- __En HDInsight klynge__. Se [Introduktion til brug af Hadoop med Hive i HDInsight på Linux](hdinsight-hadoop-linux-tutorial-get-started.md) trin til oprettelse af en ny Linux-baserede HDInsight klynge.

- __Azure SQL-Database__. Du skal bruge en Azure SQL-database som en destination datalager. Hvis du ikke allerede har en SQL-Database, se [SQL-Database Selvstudium: oprette en SQL-database i minutter](../sql-database/sql-database-get-started.md).

- __Azure CLI__. Hvis du ikke har installeret Azure CLI, kan du se [installere og konfigurere Azure CLI](../xplat-cli-install.md) for flere trin.

    [AZURE.INCLUDE [use-latest-version](../../includes/hdinsight-use-latest-cli.md)]


##<a name="download-the-flight-data"></a>Hente flight data

1. Gå til [forskning og Administration af Innovative teknologi Bureau transport statistik][rita-website].
2. På siden skal du vælge følgende værdier:

  	| Navn | Værdi |
  	| ---- | ---- |
  	| Filtrere år | 2013 |
  	| Filtrere periode | Januar |
  	| Felter | År, FlightDate, UniqueCarrier, Carrier, FlightNum, OriginAirportID, Origin, OriginCityName, OriginState, DestAirportID, Dest, DestCityName, DestState, DepDelayMinutes, ArrDelay, ArrDelayMinutes, CarrierDelay, WeatherDelay, NASDelay, SecurityDelay, LateAircraftDelay. Fjern markeringen i alle andre felter |

3. Klik på **Hent**. 

##<a name="upload-the-data"></a>Overføre dataene

1. Brug følgende kommando for at overføre zip-filen til noden HDInsight klynge hoved:

        scp FILENAME.csv USERNAME@CLUSTERNAME-ssh.azurehdinsight.net:

    Erstat __FILNAVN__ med navnet på zip-fil. Erstat __brugernavn__ med SSH logon for HDInsight-klyngen. Erstat CLUSTERNAME med navnet på HDInsight klyngen.
    
    > [AZURE.NOTE] Hvis du bruger en adgangskode til at godkende dit SSH logon, bliver du bedt om adgangskoden. Hvis du har brugt en offentlig nøgle, du skal bruge den `-i` parameter og angive stien til den tilsvarende privat nøgle. For eksempel `scp -i ~/.ssh/id_rsa FILENAME.csv USERNAME@CLUSTERNAME-ssh.azurehdinsight.net:`.

2. Når overførslen er færdig, skal du oprette forbindelse til den klynge ved hjælp af SSH:

        ssh USERNAME@CLUSTERNAME-ssh.azurehdinsight.net
        
    Du kan finde flere oplysninger om brug af SSH med Linux-baserede HDInsight, i følgende artikler:
    
    * [Bruge SSH med Linux-baserede Hadoop på HDInsight fra Linux, Unix eller OS X](hdinsight-hadoop-linux-use-ssh-unix.md)

    * [Bruge SSH med Linux-baserede Hadoop på HDInsight fra Windows](hdinsight-hadoop-linux-use-ssh-windows.md)
    
3. Når forbindelse, kan du bruge følgende til at udpakke .zip-filen:

        unzip FILENAME.zip
    
    Dette vil udtrække en .csv-fil, der er omkring 60MB i størrelse.
    
4. Bruge følgende for at oprette en ny mappe på WASB (den distribuerede data store bruges af HDInsight), og Kopiér filen:

    hdfs dfs - mkdir -p /tutorials/flightdelays/data hdfs dfs-placere FILENAME.csv/selvstudier/flightdelays/data /
    
##<a name="create-and-run-the-hiveql"></a>Oprette og køre HiveQL

Følge nedenstående trin for at importere data fra csv-filen til en Hive-tabel med navnet __forsinkelser__.

1. Bruge følgende for at oprette og redigere en ny fil med navnet __flightdelays.hql__:

        nano flightdelays.hql
        
    Du kan bruge følgende som indholdet af denne fil:
    
        DROP TABLE delays_raw;
        -- Creates an external table over the csv file
        CREATE EXTERNAL TABLE delays_raw (
            YEAR string,
            FL_DATE string,
            UNIQUE_CARRIER string,
            CARRIER string,
            FL_NUM string,
            ORIGIN_AIRPORT_ID string,
            ORIGIN string,
            ORIGIN_CITY_NAME string,
            ORIGIN_CITY_NAME_TEMP string,
            ORIGIN_STATE_ABR string,
            DEST_AIRPORT_ID string,
            DEST string,
            DEST_CITY_NAME string,
            DEST_CITY_NAME_TEMP string,
            DEST_STATE_ABR string,
            DEP_DELAY_NEW float,
            ARR_DELAY_NEW float,
            CARRIER_DELAY float,
            WEATHER_DELAY float,
            NAS_DELAY float,
            SECURITY_DELAY float,
            LATE_AIRCRAFT_DELAY float)
        -- The following lines describe the format and location of the file
        ROW FORMAT DELIMITED FIELDS TERMINATED BY ','
        LINES TERMINATED BY '\n'
        STORED AS TEXTFILE
        LOCATION '/tutorials/flightdelays/data';
        
        -- Drop the delays table if it exists
        DROP TABLE delays;
        -- Create the delays table and populate it with data
        -- pulled in from the CSV file (via the external table defined previously)
        CREATE TABLE delays AS
        SELECT YEAR AS year,
            FL_DATE AS flight_date,
            substring(UNIQUE_CARRIER, 2, length(UNIQUE_CARRIER) -1) AS unique_carrier,
            substring(CARRIER, 2, length(CARRIER) -1) AS carrier,
            substring(FL_NUM, 2, length(FL_NUM) -1) AS flight_num,
            ORIGIN_AIRPORT_ID AS origin_airport_id,
            substring(ORIGIN, 2, length(ORIGIN) -1) AS origin_airport_code,
            substring(ORIGIN_CITY_NAME, 2) AS origin_city_name,
            substring(ORIGIN_STATE_ABR, 2, length(ORIGIN_STATE_ABR) -1)  AS origin_state_abr,
            DEST_AIRPORT_ID AS dest_airport_id,
            substring(DEST, 2, length(DEST) -1) AS dest_airport_code,
            substring(DEST_CITY_NAME,2) AS dest_city_name,
            substring(DEST_STATE_ABR, 2, length(DEST_STATE_ABR) -1) AS dest_state_abr,
            DEP_DELAY_NEW AS dep_delay_new,
            ARR_DELAY_NEW AS arr_delay_new,
            CARRIER_DELAY AS carrier_delay,
            WEATHER_DELAY AS weather_delay,
            NAS_DELAY AS nas_delay,
            SECURITY_DELAY AS security_delay,
            LATE_AIRCRAFT_DELAY AS late_aircraft_delay
        FROM delays_raw;
        
2. Brug __Ctrl + X__og __Y__ til at gemme filen.

3. Bruge følgende for at starte Hive, og kør filen __flightdelays.hql__ :

        beeline -u 'jdbc:hive2://localhost:10001/;transportMode=http' -n admin -f flightdelays.hql
        
    > [AZURE.NOTE] I dette eksempel `localhost` bruges, fordi du har forbindelse til noden hoved af HDInsight klynge, som er, hvor HiveServer2 kører.

4. Brug følgende kommando til at åbne en interaktiv Beeline session:

        beeline -u 'jdbc:hive2://localhost:10001/;transportMode=http' -n admin

5. Når du modtager den `jdbc:hive2://localhost:10001/>` Spørg, skal du bruge følgende for at hente data fra de importerede flight forsinkelse data.

        INSERT OVERWRITE DIRECTORY '/tutorials/flightdelays/output'
        ROW FORMAT DELIMITED FIELDS TERMINATED BY '\t'
        SELECT regexp_replace(origin_city_name, '''', ''),
            avg(weather_delay)
        FROM delays
        WHERE weather_delay IS NOT NULL
        GROUP BY origin_city_name;

    Dette vil hente en liste over byer, der fandt vejrudsigt forsinkelser, sammen med den gennemsnitlige tidsrum og gemme den til `/tutorials/flightdelays/output`. Senere, Sqoop læser dataene fra denne placering og eksportere den til Azure SQL-Database.

6. Hvis du vil afslutte Beeline, skal du angive `!quit` kommandoprompten.

## <a name="create-a-sql-database"></a>Oprette en SQL-Database

Hvis du allerede har en SQL-Database, skal du få navnet på serveren. Du kan finde det i [Azure-portalen](https://portal.azure.com) ved at markere __SQL-databaser__, og klik derefter filtrering på navnet på den database, du vil bruge. Servernavnet er angivet i kolonnen __SERVER__ .

Hvis du ikke allerede har en SQL-Database, kan du bruge oplysningerne i [SQL-Database Selvstudium: oprette en SQL-database i minutter](../sql-database/sql-database-get-started.md) til at oprette en. Du skal gemme navnet på server, bruges til databasen.

##<a name="create-a-sql-database-table"></a>Oprette en tabel i SQL-Database

> [AZURE.NOTE] Der findes mange måder til at oprette forbindelse til SQL-Database til at oprette en tabel. I følgende trin bruges [FreeTDS](http://www.freetds.org/) fra HDInsight klynge.

1. Brug SSH til at oprette forbindelse til Linux-baserede HDInsight klynge, og Kør følgende fra SSH session.

3. Brug følgende kommando for at installere FreeTDS:

        sudo apt-get --assume-yes install freetds-dev freetds-bin

4. Når FreeTDS er blevet installeret, kan du bruge følgende kommando til at oprette forbindelse til SQL-databaseserver. Erstat __servernavn__ med navnet på SQL-databaseserveren. Erstat __adminLogin__ og __adminPassword__ med logon for SQL-Database. Erstat __Databasenavn__ med databasenavnet.

        TDSVER=8.0 tsql -H <serverName>.database.windows.net -U <adminLogin> -P <adminPassword> -p 1433 -D <databaseName>

    Du modtager output, der svarer til følgende:

        locale is "en_US.UTF-8"
        locale charset is "UTF-8"
        using default charset "UTF-8"
        Default database being set to sqooptest
        1>

5. På den `1>` Spørg, skal du angive følgende linjer:

        CREATE TABLE [dbo].[delays](
        [origin_city_name] [nvarchar](50) NOT NULL,
        [weather_delay] float,
        CONSTRAINT [PK_delays] PRIMARY KEY CLUSTERED   
        ([origin_city_name] ASC))
        GO

    Når den `GO` sætning er angivet, sætningerne forrige skal evalueres. Dette vil oprette en ny tabel med navnet __forsinkelser__med et grupperet indeks (kræves af SQL-Database).

    Brug følgende til at bekræfte, at tabellen er blevet oprettet:

        SELECT * FROM information_schema.tables
        GO

    Du burde se output, der svarer til følgende:

        TABLE_CATALOG   TABLE_SCHEMA    TABLE_NAME      TABLE_TYPE
        databaseName       dbo     delays      BASE TABLE

8. Angiv `exit` på den `1>` Spørg at afslutte værktøjet tsql.
    
##<a name="export-data-with-sqoop"></a>Eksportere data med Sqoop

2. Brug følgende kommando til at bekræfte, at Sqoop kan se din SQL-Database:

        sqoop list-databases --connect jdbc:sqlserver://<serverName>.database.windows.net:1433 --username <adminLogin> --password <adminPassword>

    Dette skal returnere en liste over databaser, herunder den database, du har oprettet tabellen forsinkelser i tidligere.

3. Brug følgende kommando for at eksportere data fra hivesampletable til tabellen mobiledata:

        sqoop export --connect 'jdbc:sqlserver://<serverName>.database.windows.net:1433;database=<databaseName>' --username <adminLogin> --password <adminPassword> --table 'delays' --export-dir 'wasbs:///tutorials/flightdelays/output' --fields-terminated-by '\t' -m 1

    Dette giver Sqoop til at oprette forbindelse til SQL-Database til den database, der indeholder tabellen forsinkelser og eksportere data fra wasbs: / / / selvstudier/flightdelays/output (hvor vi gemt output fra tidligere, forespørgslen hive) til tabellen forsinkelser.

4. Når kommandoen er fuldført, kan du bruge følgende til at oprette forbindelse til databasen ved hjælp af TSQL:

        TDSVER=8.0 tsql -H <serverName>.database.windows.net -U <adminLogin> -P <adminPassword> -p 1433 -D sqooptest

    Når forbindelse, kan du bruge følgende udtalelser til at bekræfte, at dataene er eksporteret til tabellen mobiledata:
    
        SELECT * FROM delays
        GO

    Du bør se en oversigt over dataene i tabellen. Skriv `exit` at afslutte værktøjet tsql.

##<a id="nextsteps"></a>Næste trin

Nu kan du forstå, hvordan du kan overføre en fil til Azure Blob-lager, hvordan du kan udfylde en Hive-tabel ved hjælp af data fra Azure Blob-lager, hvordan du kører Hive forespørgsler og Sådan bruges Sqoop til at eksportere data fra HDFS til Azure SQL-database. Hvis du vil vide mere, skal du se følgende artikler:

* [Introduktion til HDInsight][hdinsight-get-started]
* [Bruge Hive med HDInsight][hdinsight-use-hive]
* [Bruge Oozie med HDInsight][hdinsight-use-oozie]
* [Bruge Sqoop med HDInsight][hdinsight-use-sqoop]
* [Brug gris med HDInsight][hdinsight-use-pig]
* [Udvikle Java MapReduce programmer til HDInsight][hdinsight-develop-mapreduce]
* [Udvikle Python Hadoop streaming programmer til HDInsight][hdinsight-develop-streaming]



[azure-purchase-options]: http://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: http://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: http://azure.microsoft.com/pricing/free-trial/


[rita-website]: http://www.transtats.bts.gov/DL_SelectFields.asp?Table_ID=236&DB_Short_Name=On-Time
[cindygross-hive-tables]: http://blogs.msdn.com/b/cindygross/archive/2013/02/06/hdinsight-hive-internal-and-external-tables-intro.aspx

[hdinsight-use-oozie]: hdinsight-use-oozie-linux-mac.md
[hdinsight-use-hive]: hdinsight-use-hive.md
[hdinsight-provision]: hdinsight-provision-clusters.md
[hdinsight-storage]: hdinsight-hadoop-use-blob-storage.md
[hdinsight-upload-data]: hdinsight-upload-data.md
[hdinsight-get-started]: hdinsight-hadoop-linux-tutorial-get-started.md
[hdinsight-use-sqoop]: hdinsight-use-sqoop-mac-linux.md
[hdinsight-use-pig]: hdinsight-use-pig.md
[hdinsight-develop-streaming]: hdinsight-hadoop-streaming-python.md
[hdinsight-develop-mapreduce]: hdinsight-develop-deploy-java-mapreduce-linux.md

[hadoop-hiveql]: https://cwiki.apache.org/confluence/display/Hive/LanguageManual+DDL

[technetwiki-hive-error]: http://social.technet.microsoft.com/wiki/contents/articles/23047.hdinsight-hive-error-unable-to-rename.aspx


 
