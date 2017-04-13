<properties
    pageTitle="Bruge tidsbaserede Hadoop Oozie koordinater i HDInsight | Microsoft Azure"
    description="Bruge tidsbaserede Hadoop Oozie koordinater i HDInsight, en stor datatjeneste. Lær at definere Oozie arbejdsprocesser og koordinatorer og sende job."
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
    ms.date="07/25/2016"
    ms.author="jgao"/>


# <a name="use-time-based-oozie-coordinator-with-hadoop-in-hdinsight-to-define-workflows-and-coordinate-jobs"></a>Brug tidsbaserede Oozie koordinater med Hadoop i HDInsight til at definere arbejdsprocesser og koordinere job

I denne artikel lærer du at definere arbejdsprocesser og koordinatorer, og hvordan du kan udløse koordinater job, baseret på tid. Det er praktisk at gå gennem [Brug Oozie med HDInsight] [ hdinsight-use-oozie] før du læse denne artikel. Ud over Oozie, kan du også planlægge job med Azure Data Factory. Få Azure Data Factory, under [Brug gris og Hive med Data Factory](../data-factory/data-factory-data-transformation-activities.md).

> [AZURE.NOTE] I denne artikel kræver en Windows-baseret HDInsight klynge. Se oplysninger om brug af Oozie, herunder tidsbaserede job på en Linux-baserede klynge [Brug Oozie med Hadoop til at definere og køre en arbejdsproces for Linux-baserede HDInsight](hdinsight-use-oozie-linux-mac.md)

##<a name="what-is-oozie"></a>Hvad er Oozie

Apache Oozie er en arbejdsproces/kører koordineret system, der administrerer Hadoop-job. Det er integreret med Hadoop stablen, og det understøtter Hadoop-job til Apache MapReduce, Apache gris, Apache Hive og Apache Sqoop. Det kan også bruges til at planlægge job, der er specifikke for et system, som Java-programmer eller shell-scripts.

Følgende billede viser den arbejdsproces, du vil implementere:

![Arbejdsprocesdiagram][img-workflow-diagram]

Arbejdsprocessen indeholder to handlinger:

1. En Hive handling kører et HiveQL script for at tælle forekomster af hver logføringsniveau type i en log4j logfil. Hver log4j logfil består af en linje med felter, der indeholder et [LOGFØRINGSNIVEAU] felt for at få vist typen og alvorlighed, f.eks.:

        2012-02-03 18:35:34 SampleClass6 [INFO] everything normal for id 577725851
        2012-02-03 18:35:34 SampleClass4 [FATAL] system problem at id 1991281254
        2012-02-03 18:35:34 SampleClass3 [DEBUG] detail for id 1304807656
        ...

    Hive script output minder om:

        [DEBUG] 434
        [ERROR] 3
        [FATAL] 1
        [INFO]  96
        [TRACE] 816
        [WARN]  4

    Du kan finde flere oplysninger om Hive, se [Brug Hive med HDInsight][hdinsight-use-hive].

2.  Handlingen Sqoop eksporterer HiveQL handling output til en tabel i en Azure SQL-database. Du kan finde flere oplysninger om Sqoop, se [Brug Sqoop med HDInsight][hdinsight-use-sqoop].

> [AZURE.NOTE] Understøttede Oozie versioner på HDInsight klynger, se [Nyheder i de klynge versioner, der leveres af HDInsight?] [hdinsight-versions].


##<a name="prerequisites"></a>Forudsætninger

Inden du starter selvstudiet, skal du have følgende:

- **En arbejdsstation med Azure PowerShell**.

    [AZURE.INCLUDE [upgrade-powershell](../../includes/hdinsight-use-latest-powershell.md)]

- **En HDInsight klynge**. Se oplysninger om oprettelse af en HDInsight klynge, [oprette HDInsight klynger][hdinsight-provision], eller [Kom i gang med HDInsight][hdinsight-get-started]. Du skal gennemgå selvstudiet følgende data:

    <table border = "1">
    <tr><th>Klynge egenskab</th><th>Windows PowerShell variabelnavn</th><th>Værdi</th><th>Beskrivelse</th></tr>
    <tr><td>HDInsight klyngenavn</td><td>$clusterName</td><td></td><td>Den HDInsight klynge, hvor du skal køre dette selvstudium.</td></tr>
    <tr><td>HDInsight klynge brugernavn</td><td>$clusterUsername</td><td></td><td>HDInsight klynge brugernavnet. </td></tr>
    <tr><td>HDInsight klynge brugeradgangskode </td><td>$clusterPassword</td><td></td><td>HDInsight klynge brugeradgangskode.</td></tr>
    <tr><td>Kontonavn Azure lager</td><td>$storageAccountName</td><td></td><td>En Azure-lager konto tilgængelig til HDInsight klynge. Brug lagerplads standardkontoen, du har angivet under processen klynge klargøring til dette selvstudium.</td></tr>
    <tr><td>Azure Blob objektbeholder navn</td><td>$containerName</td><td></td><td>I dette eksempel skal du bruge objektbeholderen Azure Blob-lager, der bruges til standard HDInsight radialklyngebaseret filsystemet. Som standard har det samme navn som HDInsight klyngen.</td></tr>
    </table>

- **En Azure SQL-database**. Du skal konfigurere en firewallregel for SQL-databaseserveren mulighed for at tillade adgang fra computeren. Oplysninger om oprettelse af en Azure SQL database og konfiguration af firewall, se [Introduktion til brug af Azure SQL-database][sqldatabase-get-started]. I denne artikel indeholder et Windows PowerShell-script til at oprette tabellen Azure SQL-database, som du har brug for dette selvstudium.

    <table border = "1">
    <tr><th>Egenskaben for SQL-database</th><th>Windows PowerShell variabelnavn</th><th>Værdi</th><th>Beskrivelse</th></tr>
    <tr><td>SQL server-databasenavn</td><td>$sqlDatabaseServer</td><td></td><td>SQL-databaseserveren, Sqoop vil eksportere data. </td></tr>
    <tr><td>SQL-database login-navn</td><td>$sqlDatabaseLogin</td><td></td><td>SQL-Database login-navn.</td></tr>
    <tr><td>SQL database-logonadgangskode</td><td>$sqlDatabaseLoginPassword</td><td></td><td>SQL-Database-logonadgangskode.</td></tr>
    <tr><td>SQL-databasenavn</td><td>$sqlDatabaseName</td><td></td><td>Azure SQL-database, Sqoop vil eksportere data. </td></tr>
    </table>

    > [AZURE.NOTE] Som standard kan en Azure SQL-database forbindelser fra Azure-tjenester, som Azure HDInsight. Hvis denne indstilling af firewall er deaktiveret, skal du aktivere den fra Azure-portalen. Anvisninger om oprettelse af en SQL-Database og konfiguration af firewallregler, skal du se [oprette og konfigurere SQL-Database][sqldatabase-get-started].


> [AZURE.NOTE] Fill-in værdier i tabeller. Det kan være nyttige for ved at følge dette selvstudium.


##<a name="define-oozie-workflow-and-the-related-hiveql-script"></a>Definere arbejdsproces Oozie og relaterede HiveQL scriptet

Oozie arbejdsprocesser definitioner skrives i hPDL (en XML-proces definition language). Arbejdsproces standardfilnavnet er *arbejdsproces.XML*.  Du vil gemme filen arbejdsproces lokalt, og derefter installere det på HDInsight klyngen ved hjælp af PowerShell Azure senere i dette selvstudium.

Handlingen Hive i arbejdsprocessen kalder en HiveQL script-fil. Denne scriptfil indeholder tre HiveQL sætninger:

1. **Feltet DROP TABLE-sætning** sletter log4j Hive-tabellen, hvis den findes.
2. **Feltet CREATE TABLE-sætning** opretter en log4j Hive ekstern tabel, som peger på placeringen af logfilen log4j;
3.  **Placeringen af logfilen log4j**. Feltafgrænseren, er ";". Standard linje afgrænser er "\n". En ekstern tabel Hive bruges til at undgå den datafil, der fjernes fra den oprindelige placering, hvis du vil køre arbejdsprocessen Oozie flere gange.
3. **Feltet Indsæt OVERSKRIVE sætningen** tæller forekomster af hver logføringsniveau type fra tabellen log4j Hive, og gemmer den output til en placering til Azure Blob-lager.

**Bemærk**: der er et kendt problem med Hive sti. Dette problem der opstår, når du sender en Oozie sag. Du kan finde instruktioner til at løse problemet på TechNet Wiki: [HDInsight Hive-fejl: kan ikke omdøbe][technetwiki-hive-error].

**Til at definere scriptfil HiveQL skal ringes op af arbejdsprocessen**

1. Oprette en tekstfil med følgende indhold:

        DROP TABLE ${hiveTableName};
        CREATE EXTERNAL TABLE ${hiveTableName}(t1 string, t2 string, t3 string, t4 string, t5 string, t6 string, t7 string) ROW FORMAT DELIMITED FIELDS TERMINATED BY ' ' STORED AS TEXTFILE LOCATION '${hiveDataFolder}';
        INSERT OVERWRITE DIRECTORY '${hiveOutputFolder}' SELECT t4 AS sev, COUNT(*) AS cnt FROM ${hiveTableName} WHERE t4 LIKE '[%' GROUP BY t4;

    Der er tre variabler, som bruges i script:

    - ${hiveTableName}
    - ${hiveDataFolder}
    - ${hiveOutputFolder}

    Arbejdsproces definitionsfil (arbejdsproces.XML i dette selvstudium) overfører disse værdier til dette HiveQL script på kørselstidspunktet.

2. Gem filen som **C:\Tutorials\UseOozie\useooziewf.hql** ved hjælp af ANSI (ASCII) kodning. (Brug Notesblok Hvis tekstredigeringsprogrammet indeholder ikke denne indstilling). Denne script-fil som skal installeres til HDInsight klynge senere i selvstudiet.



**Til at definere en arbejdsproces**

1. Oprette en tekstfil med følgende indhold:

        <workflow-app name="useooziewf" xmlns="uri:oozie:workflow:0.2">
            <start to = "RunHiveScript"/>

            <action name="RunHiveScript">
                <hive xmlns="uri:oozie:hive-action:0.2">
                    <job-tracker>${jobTracker}</job-tracker>
                    <name-node>${nameNode}</name-node>
                    <configuration>
                        <property>
                            <name>mapred.job.queue.name</name>
                            <value>${queueName}</value>
                        </property>
                    </configuration>
                    <script>${hiveScript}</script>
                    <param>hiveTableName=${hiveTableName}</param>
                    <param>hiveDataFolder=${hiveDataFolder}</param>
                    <param>hiveOutputFolder=${hiveOutputFolder}</param>
                </hive>
                <ok to="RunSqoopExport"/>
                <error to="fail"/>
            </action>

            <action name="RunSqoopExport">
                <sqoop xmlns="uri:oozie:sqoop-action:0.2">
                    <job-tracker>${jobTracker}</job-tracker>
                    <name-node>${nameNode}</name-node>
                    <configuration>
                        <property>
                            <name>mapred.compress.map.output</name>
                            <value>true</value>
                        </property>
                    </configuration>
                <arg>export</arg>
                <arg>--connect</arg>
                <arg>${sqlDatabaseConnectionString}</arg>
                <arg>--table</arg>
                <arg>${sqlDatabaseTableName}</arg>
                <arg>--export-dir</arg>
                <arg>${hiveOutputFolder}</arg>
                <arg>-m</arg>
                <arg>1</arg>
                <arg>--input-fields-terminated-by</arg>
                <arg>"\001"</arg>
                </sqoop>
                <ok to="end"/>
                <error to="fail"/>
            </action>

            <kill name="fail">
                <message>Job failed, error message[${wf:errorMessage(wf:lastErrorNode())}] </message>
            </kill>

           <end name="end"/>
        </workflow-app>

    Der findes to handlinger, der er defineret i arbejdsprocessen. Start-til-handling er *RunHiveScript*. Hvis handlingen, der kører *OK*, er den næste handling *RunSqoopExport*.

    RunHiveScript har flere variabler. Du kan overføre værdierne, når du sender Oozie jobbet fra computeren ved hjælp af Azure PowerShell.

    <table border = "1">
    <tr><th>Variabler for arbejdsproces</th><th>Beskrivelse</th></tr>
    <tr><td>${jobTracker}</td><td>Angiv URL-adressen til Hadoop-job sporingen. Du kan bruge <strong>jobtrackerhost:9010</strong> på HDInsight klynge version 3.0 og 2.0.</td></tr>
    <tr><td>${nameNode}</td><td>Angiv URL-adressen for noden Hadoop navn. Brug standard fil system wasbs: / / adresse, for eksempel <i>wasbs: / /&lt;containerName&gt;@&lt;storageAccountName&gt;. blob.core.windows.net</i>.</td></tr>
    <tr><td>${queueName}</td><td>Angiver navnet på kø, jobbet sendes til. Brug <strong>standard</strong>.</td></tr>
    </table>


    <table border = "1">
    <tr><th>Hive handling variabel</th><th>Beskrivelse</th></tr>
    <tr><td>${hiveDataFolder}</td><td>Kildemappe for kommandoen Hive Create Table.</td></tr>
    <tr><td>${hiveOutputFolder}</td><td>Outputmappen for sætningen indsætte OVERSKRIVE.</td></tr>
    <tr><td>${hiveTableName}</td><td>Navnet på den Hive-tabel, der refererer til log4j-datafiler.</td></tr>
    </table>


    <table border = "1">
    <tr><th>Sqoop handling variabel</th><th>Beskrivelse</th></tr>
    <tr><td>${sqlDatabaseConnectionString}</td><td>SQL-Database forbindelsesstreng.</td></tr>
    <tr><td>${sqlDatabaseTableName}</td><td>Tabellen Azure SQL-database til hvor dataene eksporteres.</td></tr>
    <tr><td>${hiveOutputFolder}</td><td>Outputmappen for sætningen Hive indsætte OVERSKRIVE. Dette er den samme mappe til Sqoop eksport (eksport-dir).</td></tr>
    </table>

    Du kan finde flere oplysninger om Oozie arbejdsproces og bruge arbejdsproceshandlinger i [Apache Oozie 4.0] dokumentationen[ apache-oozie-400] (for HDInsight klynge version 3.0) eller [Apache Oozie 3.3.2 dokumentation] [ apache-oozie-332] (for HDInsight klynge version 2.1).

2. Gem filen som **C:\Tutorials\UseOozie\workflow.xml** ved hjælp af ANSI (ASCII) kodning. (Brug Notesblok Hvis tekstredigeringsprogrammet indeholder ikke denne indstilling).

**Til at definere koordinater**

1. Oprette en tekstfil med følgende indhold:

        <coordinator-app name="my_coord_app" frequency="${coordFrequency}" start="${coordStart}" end="${coordEnd}" timezone="${coordTimezone}" xmlns="uri:oozie:coordinator:0.4">
           <action>
              <workflow>
                 <app-path>${wfPath}</app-path>
              </workflow>
           </action>
        </coordinator-app>

    Der er fem variabler, som bruges i definitionsfilen:

  	| Variabel          | Beskrivelse |
  	| ------------------|------------ |
  	| ${coordFrequency} | Jobbet pause tidspunkt. Frekvens er altid udtrykt i minutter. |
  	| ${coordStart}     | Jobbet starttidspunkt. |
  	| ${coordEnd}       | Jobbet sluttidspunkt. |
  	| ${coordTimezone}  | Oozie behandler koordinater job i en fast tidszone med ingen sommertid (typisk repræsenteret ved hjælp af UTC). Denne tidszone kaldes "Oozie behandling-tidszone". |
  	| ${wfPath}         | Stien til arbejdsproces.XML.  Hvis navnet på arbejdsproces fil ikke er standardfilnavnet (arbejdsproces.xml), skal du angive den. |

2. Gem filen som **C:\Tutorials\UseOozie\coordinator.xml** ved hjælp af ANSI (ASCII) kodning. (Brug Notesblok Hvis tekstredigeringsprogrammet indeholder ikke denne indstilling).

##<a name="deploy-the-oozie-project-and-prepare-the-tutorial"></a>Anvende Oozie projektet og forberede selvstudiet

Du kører en Azure PowerShell-script for at udføre følgende:

- Kopiere scriptet HiveQL (useoozie.hql) til Azure Blob-lager, wasbs:///tutorials/useoozie/useoozie.hql.
- Kopiere arbejdsproces.XML til wasbs:///tutorials/useoozie/workflow.xml.
- Kopiere coordinator.xml til wasbs:///tutorials/useoozie/coordinator.xml.
- Kopiere-datafilen (/ example/data/sample.log) til wasbs:///tutorials/useoozie/data/sample.log.
- Oprette en Azure SQL database-tabel til lagring af Sqoop Eksportér data. Tabelnavnet er *log4jLogCount*.

**Forstå HDInsight lagerplads**

HDInsight bruger Azure Blob-lager til lagring af data. wasbs: / / er Microsofts implementering af Hadoop-distribueret filsystem (HDFS) i Azure Blob-lager. Kan finde flere oplysninger i [Brug Azure Blob-lager med HDInsight][hdinsight-storage].

Når du klargør en HDInsight klynge, er en Azure Blob storage konto og en bestemt objektbeholder fra denne konto angivet som standard-filsystemet, som i HDFS. Ud over denne lagerplads konto, kan du tilføje yderligere lagerplads konti fra samme Azure-abonnement eller fra forskellige Azure abonnementer under processen klargøring. Oplysninger om at tilføje yderligere lagerplads konti, skal du se [klargøring HDInsight klynger][hdinsight-provision]. For at forenkle den Azure PowerShell-script, der bruges i dette selvstudium, er alle filerne gemt i beholderen standard fil system findes i */tutorials/useoozie*. Som standard har denne beholder det samme navn som HDInsight klyngenavnet.
Syntaksen er:

    wasb[s]://<ContainerName>@<StorageAccountName>.blob.core.windows.net/<path>/<filename>

> [AZURE.NOTE] Kun den *wasb: / /* syntaks understøttes i HDInsight klynge version 3.0. Den ældre *asv: / /* syntaks understøttes i HDInsight 2.1 og 1,6 klynger, men det understøttes ikke i HDInsight 3.0 klynger.

> [AZURE.NOTE] Wasb: / / sti er en virtuel sti. Finde flere oplysninger i [Brug Azure Blob-lager med HDInsight][hdinsight-storage].

En fil, der er gemt i beholderen standard fil system kan åbnes fra HDInsight ved hjælp af en af de følgende URI'er (jeg bruger arbejdsproces.XML som eksempel):

    wasbs://mycontainer@mystorageaccount.blob.core.windows.net/tutorials/useoozie/workflow.xml
    wasbs:///tutorials/useoozie/workflow.xml
    /tutorials/useoozie/workflow.xml

Hvis du vil have adgang til filen direkte fra kontoen lagerplads, er blob navnet til filen:

    tutorials/useoozie/workflow.xml

**Forstå Hive interne og eksterne tabeller**

Der er et par ting, du bør vide om Hive interne og eksterne tabeller:

- Kommandoen Opret tabel opretter en intern tabel, også kaldet en administreret tabel. Datafilen skal være placeret i beholderen standard.
- Kommandoen Opret tabel flytter datafilen til /hive/warehouse/<TableName> mappe i beholderen standard.
- Kommandoen Opret ekstern tabel opretter en ekstern tabel. Datafilen kan være placeret uden for objektbeholderen standard.
- Kommandoen Opret ekstern tabel flyttes ikke datafilen.
- Kommandoen Opret ekstern tabel tillader ikke, at alle undermapper i den mappe, der er angivet i delsætningen placering. Det er grunden til hvorfor selvstudiet opretter en kopi af filen sample.log.

Du kan finde flere oplysninger, se [HDInsight: Hive interne og eksterne tabeller Introduktion][cindygross-hive-tables].

**Sådan forbereder du dig selvstudiet**

1. Åbn Windows PowerShell ISE (Skriv **PowerShell_ISE**i startskærmen i Windows 8, og klik derefter på **Windows PowerShell ISE**. Du kan finde flere oplysninger i [Begynde Windows PowerShell i Windows 8 og Windows][powershell-start]).
2. Kør følgende kommando for at oprette forbindelse til abonnementet Azure i den nederste rude:

        Add-AzureAccount

    Du bliver bedt om at angive dine legitimationsoplysninger Azure-konto. Denne metode til at tilføje en forbindelse til abonnement udløber, og efter 12 timer, er du nødt til at køre følgende cmdlet igen.

    > [AZURE.NOTE] Hvis du har flere Azure abonnementer og abonnementets standard ikke er den person, du vil bruge, kan du bruge <strong>Vælg AzureSubscription</strong> cmdlet til at vælge et abonnement.

3. Kopier følgende script til ruden script, og angiv derefter de første seks variabler:

        # WASB variables
        $storageAccountName = "<StorageAccountName>"
        $containerName = "<BlobStorageContainerName>"

        # SQL database variables
        $sqlDatabaseServer = "<SQLDatabaseServerName>"  
        $sqlDatabaseLogin = "<SQLDatabaseLoginName>"
        $sqlDatabaseLoginPassword = "SQLDatabaseLoginPassword>"
        $sqlDatabaseName = "<SQLDatabaseName>"  
        $sqlDatabaseTableName = "log4jLogsCount"

        # Oozie files for the tutorial
        $hiveQLScript = "C:\Tutorials\UseOozie\useooziewf.hql"
        $workflowDefinition = "C:\Tutorials\UseOozie\workflow.xml"
        $coordDefinition =  "C:\Tutorials\UseOozie\coordinator.xml"

        # WASB folder for storing the Oozie tutorial files.
        $destFolder = "tutorials/useoozie"  # Do NOT use the long path here


    I afsnittet [forudsætninger, der er](#prerequisites) flere beskrivelser af variablerne, i dette selvstudium.

3. Føj følgende til scriptet i ruden script:

        # Create a storage context object
        $storageaccountkey = get-azurestoragekey $storageAccountName | %{$_.Primary}
        $destContext = New-AzureStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storageaccountkey

        function uploadOozieFiles()
        {
            Write-Host "Copy HiveQL script, workflow definition and coordinator definition ..." -ForegroundColor Green
            Set-AzureStorageBlobContent -File $hiveQLScript -Container $containerName -Blob "$destFolder/useooziewf.hql" -Context $destContext
            Set-AzureStorageBlobContent -File $workflowDefinition -Container $containerName -Blob "$destFolder/workflow.xml" -Context $destContext
            Set-AzureStorageBlobContent -File $coordDefinition -Container $containerName -Blob "$destFolder/coordinator.xml" -Context $destContext
        }

        function prepareHiveDataFile()
        {
            Write-Host "Make a copy of the sample.log file ... " -ForegroundColor Green
            Start-CopyAzureStorageBlob -SrcContainer $containerName -SrcBlob "example/data/sample.log" -Context $destContext -DestContainer $containerName -destBlob "$destFolder/data/sample.log" -DestContext $destContext
        }

        function prepareSQLDatabase()
        {
            # SQL query string for creating log4jLogsCount table
            $cmdCreateLog4jCountTable = " CREATE TABLE [dbo].[$sqlDatabaseTableName](
                    [Level] [nvarchar](10) NOT NULL,
                    [Total] float,
                CONSTRAINT [PK_$sqlDatabaseTableName] PRIMARY KEY CLUSTERED
                (
                [Level] ASC
                )
                )"

            #Create the log4jLogsCount table
            Write-Host "Create Log4jLogsCount table ..." -ForegroundColor Green
            $conn = New-Object System.Data.SqlClient.SqlConnection
            $conn.ConnectionString = "Data Source=$sqlDatabaseServer.database.windows.net;Initial Catalog=$sqlDatabaseName;User ID=$sqlDatabaseLogin;Password=$sqlDatabaseLoginPassword;Encrypt=true;Trusted_Connection=false;"
            $conn.open()
            $cmd = New-Object System.Data.SqlClient.SqlCommand
            $cmd.connection = $conn
            $cmd.commandtext = $cmdCreateLog4jCountTable
            $cmd.executenonquery()

            $conn.close()
        }

        # upload workflow.xml, coordinator.xml, and ooziewf.hql
        uploadOozieFiles;

        # make a copy of example/data/sample.log to example/data/log4j/sample.log
        prepareHiveDataFile;

        # create log4jlogsCount table on SQL database
        prepareSQLDatabase;

4. Klik på **Kør Script** , eller tryk på **F5** for at køre scriptet. Output bliver ligner:

    ![Selvstudium forberedelse output][img-preparation-output]

##<a name="run-the-oozie-project"></a>Kør Oozie projektet

I øjeblikket giver ikke Azure PowerShell en hvilken som helst cmdletter for definition af Oozie job. Du kan bruge cmdlet'en **Aktiver RestMethod** til at kalde Oozie webtjenester. Oozie web services API er en HTTP RESTEN JSON-API. Du kan finde flere oplysninger om Oozie webtjenesterne API i [Apache Oozie 4.0] dokumentationen[ apache-oozie-400] (for HDInsight klynge version 3.0) eller [Apache Oozie 3.3.2 dokumentation] [ apache-oozie-332] (for HDInsight klynge version 2.1).

**Sende en Oozie job**

1. Åbn Windows PowerShell ISE (Skriv **PowerShell_ISE**i startskærmbilledet Windows 8, og klik derefter på **Windows PowerShell ISE**. Du kan finde flere oplysninger i [Begynde Windows PowerShell i Windows 8 og Windows][powershell-start]).

3. Kopier følgende script til ruden script, og angiv derefter de første 14 variabler (men springe **$storageUri**).

        #HDInsight cluster variables
        $clusterName = "<HDInsightClusterName>"
        $clusterUsername = "<HDInsightClusterUsername>"
        $clusterPassword = "<HDInsightClusterUserPassword>"

        #Azure Blob storage (WASB) variables
        $storageAccountName = "<StorageAccountName>"
        $storageContainerName = "<BlobContainerName>"
        $storageUri="wasbs://$storageContainerName@$storageAccountName.blob.core.windows.net"

        #Azure SQL database variables
        $sqlDatabaseServer = "<SQLDatabaseServerName>"
        $sqlDatabaseLogin = "<SQLDatabaseLoginName>"
        $sqlDatabaseLoginPassword = "<SQLDatabaseloginPassword>"
        $sqlDatabaseName = "<SQLDatabaseName>"  

        #Oozie WF/coordinator variables
        $coordStart = "2014-03-21T13:45Z"
        $coordEnd = "2014-03-21T13:45Z"
        $coordFrequency = "1440"    # in minutes, 24h x 60m = 1440m
        $coordTimezone = "UTC"  #UTC/GMT

        $oozieWFPath="$storageUri/tutorials/useoozie"  # The default name is workflow.xml. And you don't need to specify the file name.
        $waitTimeBetweenOozieJobStatusCheck=10

        #Hive action variables
        $hiveScript = "$storageUri/tutorials/useoozie/useooziewf.hql"
        $hiveTableName = "log4jlogs"
        $hiveDataFolder = "$storageUri/tutorials/useoozie/data"
        $hiveOutputFolder = "$storageUri/tutorials/useoozie/output"

        #Sqoop action variables
        $sqlDatabaseConnectionString = "jdbc:sqlserver://$sqlDatabaseServer.database.windows.net;user=$sqlDatabaseLogin@$sqlDatabaseServer;password=$sqlDatabaseLoginPassword;database=$sqlDatabaseName"
        $sqlDatabaseTableName = "log4jLogsCount"

        $passwd = ConvertTo-SecureString $clusterPassword -AsPlainText -Force
        $creds = New-Object System.Management.Automation.PSCredential ($clusterUsername, $passwd)

    I afsnittet [forudsætninger, der er](#prerequisites) flere beskrivelser af variablerne, i dette selvstudium.

    $coordstart og $coordend er arbejdsgangen start- og sluttidspunkt. Søge efter "utc tid" på bing.com for at finde ud af UTC/GMT klokkeslættet. $CoordFrequency er i minutter, du vil køre arbejdsprocessen, hvor ofte.

3. Føj følgende til scriptet. Denne del definerer Oozie data:

        #OoziePayload used for Oozie web service submission
        $OoziePayload =  @"
        <?xml version="1.0" encoding="UTF-8"?>
        <configuration>

           <property>
               <name>nameNode</name>
               <value>$storageUrI</value>
           </property>

           <property>
               <name>jobTracker</name>
               <value>jobtrackerhost:9010</value>
           </property>

           <property>
               <name>queueName</name>
               <value>default</value>
           </property>

           <property>
               <name>oozie.use.system.libpath</name>
               <value>true</value>
           </property>

           <property>
               <name>oozie.coord.application.path</name>
               <value>$oozieWFPath</value>
           </property>

           <property>
               <name>wfPath</name>
               <value>$oozieWFPath</value>
           </property>

           <property>
               <name>coordStart</name>
               <value>$coordStart</value>
           </property>

           <property>
               <name>coordEnd</name>
               <value>$coordEnd</value>
           </property>

           <property>
               <name>coordFrequency</name>
               <value>$coordFrequency</value>
           </property>

           <property>
               <name>coordTimezone</name>
               <value>$coordTimezone</value>
           </property>

           <property>
               <name>hiveScript</name>
               <value>$hiveScript</value>
           </property>

           <property>
               <name>hiveTableName</name>
               <value>$hiveTableName</value>
           </property>

           <property>
               <name>hiveDataFolder</name>
               <value>$hiveDataFolder</value>
           </property>

           <property>
               <name>hiveOutputFolder</name>
               <value>$hiveOutputFolder</value>
           </property>

           <property>
               <name>sqlDatabaseConnectionString</name>
               <value>&quot;$sqlDatabaseConnectionString&quot;</value>
           </property>

           <property>
               <name>sqlDatabaseTableName</name>
               <value>$SQLDatabaseTableName</value>
           </property>

           <property>
               <name>user.name</name>
               <value>admin</value>
           </property>

        </configuration>
        "@

    >[AZURE.NOTE] Den største forskel sammenlignet med filen arbejdsproces bidrag data er variable **oozie.coord.application.path**. Når du sender en arbejdsproces sag, bruger du **oozie.wf.application.path** i stedet.

4. Føj følgende til scriptet. Denne del kontrollerer Oozie web Tjenestestatus:

        function checkOozieServerStatus()
        {
            Write-Host "Checking Oozie server status..." -ForegroundColor Green
            $clusterUriStatus = "https://$clusterName.azurehdinsight.net:443/oozie/v2/admin/status"
            $response = Invoke-RestMethod -Method Get -Uri $clusterUriStatus -Credential $creds -OutVariable $OozieServerStatus

            $jsonResponse = ConvertFrom-Json (ConvertTo-Json -InputObject $response)
            $oozieServerSatus = $jsonResponse[0].("systemMode")
            Write-Host "Oozie server status is $oozieServerSatus..."

            if($oozieServerSatus -notmatch "NORMAL")
            {
                Write-Host "Oozie server status is $oozieServerSatus...cannot submit Oozie jobs. Check the server status and re-run the job."
                exit 1
            }
        }

5. Føj følgende til scriptet. Denne del opretter en Oozie sag:

        function createOozieJob()
        {
            # create Oozie job
            Write-Host "Sending the following Payload to the cluster:" -ForegroundColor Green
            Write-Host "`n--------`n$OoziePayload`n--------"
            $clusterUriCreateJob = "https://$clusterName.azurehdinsight.net:443/oozie/v2/jobs"
            $response = Invoke-RestMethod -Method Post -Uri $clusterUriCreateJob -Credential $creds -Body $OoziePayload -ContentType "application/xml" -OutVariable $OozieJobName -debug -Verbose

            $jsonResponse = ConvertFrom-Json (ConvertTo-Json -InputObject $response)
            $oozieJobId = $jsonResponse[0].("id")
            Write-Host "Oozie job id is $oozieJobId..."

            return $oozieJobId
        }

    > [AZURE.NOTE] Når du sender et arbejdsprocesjob, skal du foretage en anden webtjeneste, der ringer for at starte kørslen, når jobbet er blevet oprettet. I dette tilfælde udløses koordinater jobbet af klokkeslæt. Jobbet starter automatisk.

6. Føj følgende til scriptet. Denne del kontrollerer jobstatus Oozie:

        function checkOozieJobStatus($oozieJobId)
        {
            # get job status
            Write-Host "Sleeping for $waitTimeBetweenOozieJobStatusCheck seconds until the job metadata is populated in the Oozie metastore..." -ForegroundColor Green
            Start-Sleep -Seconds $waitTimeBetweenOozieJobStatusCheck

            Write-Host "Getting job status and waiting for the job to complete..." -ForegroundColor Green
            $clusterUriGetJobStatus = "https://$clusterName.azurehdinsight.net:443/oozie/v2/job/" + $oozieJobId + "?show=info"
            $response = Invoke-RestMethod -Method Get -Uri $clusterUriGetJobStatus -Credential $creds
            $jsonResponse = ConvertFrom-Json (ConvertTo-Json -InputObject $response)
            $JobStatus = $jsonResponse[0].("status")

            while($JobStatus -notmatch "SUCCEEDED|KILLED")
            {
                Write-Host "$(Get-Date -format 'G'): $oozieJobId is in $JobStatus state...waiting $waitTimeBetweenOozieJobStatusCheck seconds for the job to complete..."
                Start-Sleep -Seconds $waitTimeBetweenOozieJobStatusCheck
                $response = Invoke-RestMethod -Method Get -Uri $clusterUriGetJobStatus -Credential $creds
                $jsonResponse = ConvertFrom-Json (ConvertTo-Json -InputObject $response)
                $JobStatus = $jsonResponse[0].("status")
            }

            Write-Host "$(Get-Date -format 'G'): $oozieJobId is in $JobStatus state!"
            if($JobStatus -notmatch "SUCCEEDED")
            {
                Write-Host "Check logs at http://headnode0:9014/cluster for detais."
                exit -1
            }
        }

7. (Valgfrit) Føj følgende til scriptet.

        function listOozieJobs()
        {
            Write-Host "Listing Oozie jobs..." -ForegroundColor Green
            $clusterUriStatus = "https://$clusterName.azurehdinsight.net:443/oozie/v2/jobs"
            $response = Invoke-RestMethod -Method Get -Uri $clusterUriStatus -Credential $creds

            write-host "Job ID                                   App Name        Status      Started                         Ended"
            write-host "----------------------------------------------------------------------------------------------------------------------------------"
            foreach($job in $response.workflows)
            {
                Write-Host $job.id "`t" $job.appName "`t" $job.status "`t" $job.startTime "`t" $job.endTime
            }
        }

        function ShowOozieJobLog($oozieJobId)
        {
            Write-Host "Showing Oozie job info..." -ForegroundColor Green
            $clusterUriStatus = "https://$clusterName.azurehdinsight.net:443/oozie/v2/job/$oozieJobId" + "?show=log"
            $response = Invoke-RestMethod -Method Get -Uri $clusterUriStatus -Credential $creds
            write-host $response
        }

        function killOozieJob($oozieJobId)
        {
            Write-Host "Killing the Oozie job $oozieJobId..." -ForegroundColor Green
            $clusterUriStartJob = "https://$clusterName.azurehdinsight.net:443/oozie/v2/job/" + $oozieJobId + "?action=kill" #Valid values for the 'action' parameter are 'start', 'suspend', 'resume', 'kill', 'dryrun', 'rerun', and 'change'.
            $response = Invoke-RestMethod -Method Put -Uri $clusterUriStartJob -Credential $creds | Format-Table -HideTableHeaders -debug
        }

7. Føj følgende til scriptet:

        checkOozieServerStatus
        # listOozieJobs
        $oozieJobId = createOozieJob($oozieJobId)
        checkOozieJobStatus($oozieJobId)
        # ShowOozieJobLog($oozieJobId)
        # killOozieJob($oozieJobId)

    Fjerne # fortegn, hvis du vil køre de andre funktioner.

7. Hvis din HDinsight klynge er version 2.1, skal du erstatte "https://$clusterName.azurehdinsight.net:443/oozie/v2/" med "https://$clusterName.azurehdinsight.net:443/oozie/v1/". HDInsight klynge version 2.1 understøtter ikke understøtter version 2 af web-tjenester.

7. Klik på **Kør Script** , eller tryk på **F5** for at køre scriptet. Output bliver ligner:

    ![Selvstudium køre arbejdsprocessen output][img-runworkflow-output]

8. Oprette forbindelse til din SQL-Database til at se de eksporterede data.

**Kontrollere jobstatus fejlloggen**

For at foretage fejlfinding af en arbejdsproces, kan logfilen Oozie findes på C:\apps\dist\oozie-3.3.2.1.3.2.0-05\oozie-win-distro\logs\Oozie.log fra klynge headnode. Finde oplysninger om RDP, under [Administration af HDInsight klynger ved hjælp af portalen Azure][hdinsight-admin-portal].

**Vil køre selvstudiet**

Hvis du vil køre arbejdsprocessen, skal du udføre følgende opgaver:

- Slet filen Hive script output.
- Slette dataene i tabellen log4jLogsCount.

Her er et eksempel på Windows PowerShell-script, som du kan bruge:

    $storageAccountName = "<AzureStorageAccountName>"
    $containerName = "<ContainerName>"

    #SQL database variables
    $sqlDatabaseServer = "<SQLDatabaseServerName>"
    $sqlDatabaseLogin = "<SQLDatabaseLoginName>"
    $sqlDatabaseLoginPassword = "<SQLDatabaseLoginPassword>"
    $sqlDatabaseName = "<SQLDatabaseName>"
    $sqlDatabaseTableName = "log4jLogsCount"

    Write-host "Delete the Hive script output file ..." -ForegroundColor Green
    $storageaccountkey = get-azurestoragekey $storageAccountName | %{$_.Primary}
    $destContext = New-AzureStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storageaccountkey
    Remove-AzureStorageBlob -Context $destContext -Blob "tutorials/useoozie/output/000000_0" -Container $containerName

    Write-host "Delete all the records from the log4jLogsCount table ..." -ForegroundColor Green
    $conn = New-Object System.Data.SqlClient.SqlConnection
    $conn.ConnectionString = "Data Source=$sqlDatabaseServer.database.windows.net;Initial Catalog=$sqlDatabaseName;User ID=$sqlDatabaseLogin;Password=$sqlDatabaseLoginPassword;Encrypt=true;Trusted_Connection=false;"
    $conn.open()
    $cmd = New-Object System.Data.SqlClient.SqlCommand
    $cmd.connection = $conn
    $cmd.commandtext = "delete from $sqlDatabaseTableName"
    $cmd.executenonquery()

    $conn.close()


##<a name="next-steps"></a>Næste trin
Du har lært at definere en arbejdsproces for Oozie og en Oozie koordinater, og hvordan du kører en Oozie koordinater sag ved hjælp af PowerShell Azure i dette selvstudium. Hvis du vil vide mere, skal du se følgende artikler:

- [Komme i gang med HDInsight][hdinsight-get-started]
- [Bruge Azure Blob-lager med HDInsight][hdinsight-storage]
- [Administrere HDInsight ved hjælp af Azure PowerShell][hdinsight-admin-powershell]
- [Overføre data til HDInsight][hdinsight-upload-data]
- [Bruge Sqoop med HDInsight][hdinsight-use-sqoop]
- [Bruge Hive med HDInsight][hdinsight-use-hive]
- [Brug gris med HDInsight][hdinsight-use-pig]
- [Udvikle Java MapReduce programmer til HDInsight][hdinsight-develop-java-mapreduce]



[hdinsight-cmdlets-download]: http://go.microsoft.com/fwlink/?LinkID=325563


[hdinsight-versions]:  hdinsight-component-versioning.md
[hdinsight-storage]: hdinsight-hadoop-use-blob-storage.md
[hdinsight-get-started]: hdinsight-hadoop-linux-tutorial-get-started.md
[hdinsight-admin-portal]: hdinsight-administer-use-management-portal.md


[hdinsight-use-sqoop]: hdinsight-use-sqoop.md
[hdinsight-provision]: hdinsight-provision-clusters.md
[hdinsight-admin-powershell]: hdinsight-administer-use-powershell.md
[hdinsight-upload-data]: hdinsight-upload-data.md
[hdinsight-use-hive]: hdinsight-use-hive.md
[hdinsight-use-pig]: hdinsight-use-pig.md
[hdinsight-storage]: hdinsight-hadoop-use-blob-storage.md
[hdinsight-develop-java-mapreduce]: hdinsight-develop-deploy-java-mapreduce-linux.md
[hdinsight-use-oozie]: hdinsight-use-oozie.md

[sqldatabase-get-started]: ../sql-database/sql-database-get-started.md

[azure-management-portal]: https://portal.azure.com/
[azure-create-storageaccount]: ../storage-create-storage-account.md

[apache-hadoop]: http://hadoop.apache.org/
[apache-oozie-400]: http://oozie.apache.org/docs/4.0.0/
[apache-oozie-332]: http://oozie.apache.org/docs/3.3.2/

[powershell-download]: http://azure.microsoft.com/downloads/
[powershell-about-profiles]: http://go.microsoft.com/fwlink/?LinkID=113729
[powershell-install-configure]: ../powershell-install-configure.md
[powershell-start]: http://technet.microsoft.com/library/hh847889.aspx
[powershell-script]: http://technet.microsoft.com/library/ee176949.aspx

[cindygross-hive-tables]: http://blogs.msdn.com/b/cindygross/archive/2013/02/06/hdinsight-hive-internal-and-external-tables-intro.aspx

[img-workflow-diagram]: ./media/hdinsight-use-oozie-coordinator-time/HDI.UseOozie.Workflow.Diagram.png
[img-preparation-output]: ./media/hdinsight-use-oozie-coordinator-time/HDI.UseOozie.Preparation.Output1.png  
[img-runworkflow-output]: ./media/hdinsight-use-oozie-coordinator-time/HDI.UseOozie.RunCoord.Output.png  

[technetwiki-hive-error]: http://social.technet.microsoft.com/wiki/contents/articles/23047.hdinsight-hive-error-unable-to-rename.aspx
