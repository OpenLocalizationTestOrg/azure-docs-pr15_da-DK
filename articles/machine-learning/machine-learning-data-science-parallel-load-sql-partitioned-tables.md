<properties 
    pageTitle="Parallelle masseimport Data ved hjælp af SQL-partitionstabeller | Microsoft Azure" 
    description="Parallel Data masseimport ved hjælp af SQL-partitionstabeller" 
    services="machine-learning" 
    documentationCenter="" 
    authors="bradsev"
    manager="jhubbard" 
    editor="cgronlun" />

<tags 
    ms.service="machine-learning" 
    ms.workload="data-services" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="09/19/2016" 
    ms.author="bradsev" /> 

# <a name="parallel-bulk-data-import-using-sql-partition-tables"></a>Parallel Data masseimport ved hjælp af SQL-partitionstabeller

Dette dokument beskriver, hvordan du opbygger partitioneret tabel(ler) til hurtig parallelle bulk import af data til en SQL Server-database. Ved stor belastning/dataoverførsel til en SQL-database, kan importerer data til SQL-DB og efterfølgende forespørgsler forbedres ved hjælp af _partitioneret tabeller og visninger_. 


## <a name="create-a-new-database-and-a-set-of-filegroups"></a>Oprette en ny database og et sæt af filgrupper

- [Opret en ny database](https://technet.microsoft.com/library/ms176061.aspx) (hvis den ikke findes)
- Tilføjes den database, som vil indeholde de fysiske filer, der er partitioneret database filgrupper

  Bemærk: Dette kan gøres med [CREATE DATABASE](https://technet.microsoft.com/library/ms176061.aspx) , hvis du er nybegynder eller [JUSTER DATABASE](https://msdn.microsoft.com/library/bb522682.aspx) Hvis databasen allerede findes

- Tilføj en eller flere filer (efter behov) til hver database filgruppe

 > [AZURE.NOTE] Angiv den destination filgruppe, der indeholder data for denne partition og filnavne fysiske database, hvor filgruppedata skal gemmes.
 
Følgende eksempel opretter en ny database med tre filgrupper end primært og log-grupper, der indeholder én fysisk fil i hver. Databasefiler oprettes i mappen standard SQL Server-Data, som er konfigureret i SQL Server-forekomsten. Du kan finde flere oplysninger om standardfilplaceringen, [Filplaceringer for standardforekomster og navngivne forekomster af SQL Server](https://msdn.microsoft.com/library/ms143547.aspx).

    DECLARE @data_path nvarchar(256);
    SET @data_path = (SELECT SUBSTRING(physical_name, 1, CHARINDEX(N'master.mdf', LOWER(physical_name)) - 1)
      FROM master.sys.master_files
      WHERE database_id = 1 AND file_id = 1);
    
    EXECUTE ('
        CREATE DATABASE <database_name>
        ON  PRIMARY 
        ( NAME = ''Primary'', FILENAME = ''' + @data_path + '<primary_file_name>.mdf'', SIZE = 4096KB , FILEGROWTH = 1024KB ), 
        FILEGROUP [filegroup_1] 
        ( NAME = ''FileGroup1'', FILENAME = ''' + @data_path + '<file_name_1>.ndf'' , SIZE = 4096KB , FILEGROWTH = 1024KB ), 
        FILEGROUP [filegroup_2] 
        ( NAME = ''FileGroup1'', FILENAME = ''' + @data_path + '<file_name_2>.ndf'' , SIZE = 4096KB , FILEGROWTH = 1024KB ), 
        FILEGROUP [filegroup_3] 
        ( NAME = ''FileGroup1'', FILENAME = ''' + @data_path + '<file_name>.ndf'' , SIZE = 102400KB , FILEGROWTH = 10240KB ), 
        LOG ON 
        ( NAME = ''LogFileGroup'', FILENAME = ''' + @data_path + '<log_file_name>.ldf'' , SIZE = 1024KB , FILEGROWTH = 10%)
    ')
    
## <a name="create-a-partitioned-table"></a>Oprette en partitioneret tabel

Opret partitioneret tabel(ler) i henhold til data-skema, der er tilknyttet den database filgrupper sammen, der er oprettet i forrige trin. Når dataene er importeret til partitioneret tabel(ler) bulk, vil poster vil blive fordelt mellem filgrupper i henhold til ordningen en partition, som beskrevet nedenfor.

**Hvis du vil oprette en partitionstabel, skal du:**

- [Opret en partition funktion](https://msdn.microsoft.com/library/ms187802.aspx) , der definerer en række værdier/grænser skal medtages i hver enkelt partitionstabellen, f.eks., at begrænse partitioner pr. måned (nogle\_datetime\_felt) i år 2013:

        CREATE PARTITION FUNCTION <DatetimeFieldPFN>(<datetime_field>)  
        AS RANGE RIGHT FOR VALUES (
            '20130201', '20130301', '20130401',
            '20130501', '20130601', '20130701', '20130801',
            '20130901', '20131001', '20131101', '20131201' )

- [Opret en partition ordning](https://msdn.microsoft.com/library/ms179854.aspx) , som er tilknyttet hver partition interval i funktionen partition en fysisk filgruppe, f.eks.:

        CREATE PARTITION SCHEME <DatetimeFieldPScheme> AS  
        PARTITION <DatetimeFieldPFN> TO (
        <filegroup_1>, <filegroup_2>, <filegroup_3>, <filegroup_4>,
        <filegroup_5>, <filegroup_6>, <filegroup_7>, <filegroup_8>,
        <filegroup_9>, <filegroup_10>, <filegroup_11>, <filegroup_12> )

  Tip: Du kan kontrollere områder i kraft i hver partition efter funktion ordningen, Kør følgende forespørgsel:

        SELECT psch.name as PartitionScheme,
            prng.value AS ParitionValue,
            prng.boundary_id AS BoundaryID
        FROM sys.partition_functions AS pfun
        INNER JOIN sys.partition_schemes psch ON pfun.function_id = psch.function_id
        INNER JOIN sys.partition_range_values prng ON prng.function_id=pfun.function_id
        WHERE pfun.name = <DatetimeFieldPFN>

- [Opret en partitioneret tabel](https://msdn.microsoft.com/library/ms174979.aspx) (s) i henhold til dine data-skema, og Angiv partitionen ordningen og begrænsning feltet bruges til at partitionere tabellen, f.eks.:

        CREATE TABLE <table_name> ( [include schema definition here] )
        ON <TablePScheme>(<partition_field>)

Yderligere oplysninger finder du under [oprette partitioneret tabeller og indeks](https://msdn.microsoft.com/library/ms188730.aspx).


## <a name="bulk-import-the-data-for-each-individual-partition-table"></a>Dataene for hver enkelt partitionstabellen masseimporten

- Du kan bruge BCP, BULK INSERT eller andre metoder, som [Guiden Overflytning af SQL Server](http://sqlazuremw.codeplex.com/). Det viste eksempel bruges metoden BCP.

- [Ændrer databasen](https://msdn.microsoft.com/library/bb522682.aspx) til at ændre transaktionslog skema til BULK_LOGGED for at minimere spild af logføring, f.eks.:

        ALTER DATABASE <database_name> SET RECOVERY BULK_LOGGED

- For at fremskynde indlæsning af data, kan du starte Importer massehandlinger parallelt. Tip til at fremskynde bulk import af store data til SQL Server-databaser, du [indlæse 1 TB på mindre end 1 time](http://blogs.msdn.com/b/sqlcat/archive/2006/05/19/602142.aspx).

Følgende PowerShell script er et eksempel på parallel dataindlæsningen bruger BCP.

    # Set database name, input data directory, and output log directory
    # This example loads comma-separated input data files
    # The example assumes the partitioned data files are named as <base_file_name>_<partition_number>.csv
    # Assumes the input data files include a header line. Loading starts at line number 2.

    $dbname = "<database_name>"
    $indir  = "<path_to_data_files>"
    $logdir = "<path_to_log_directory>"

    # Select authentication mode
    $sqlauth = 0
    
    # For SQL authentication, set the server and user credentials
    $sqlusr = "<user@server>"
    $server = "<tcp:serverdns>"
    $pass   = "<password>"

    # Set number of partitions per table - Should match the number of input data files per table
    $numofparts = <number_of_partitions>
       
    # Set table name to be loaded, basename of input data files, input format file, and number of partitions
    $tbname = "<table_name>"
    $basename = "<base_input_data_filename_no_extension>"
    $fmtfile = "<full_path_to_format_file>"
   
    # Create log directory if it does not exist
    New-Item -ErrorAction Ignore -ItemType directory -Path $logdir
      
    # BCP example using Windows authentication
    $ScriptBlock1 = {
       param($dbname, $tbname, $basename, $fmtfile, $indir, $logdir, $num)
       bcp ($dbname + ".." + $tbname) in ($indir + "\" + $basename + "_" + $num + ".csv") -o ($logdir + "\" + $tbname + "_" + $num + ".txt") -h "TABLOCK" -F 2 -C "RAW" -f ($fmtfile) -T -b 2500 -t "," -r \n
    }
    
    # BCP example using SQL authentication
    $ScriptBlock2 = {
       param($dbname, $tbname, $basename, $fmtfile, $indir, $logdir, $num, $sqlusr, $server, $pass)
       bcp ($dbname + ".." + $tbname) in ($indir + "\" + $basename + "_" + $num + ".csv") -o ($logdir + "\" + $tbname + "_" + $num + ".txt") -h "TABLOCK" -F 2 -C "RAW" -f ($fmtfile) -U $sqlusr -S $server -P $pass -b 2500 -t "," -r \n
    }
    
    # Background processing of all partitions
    for ($i=1; $i -le $numofparts; $i++)
    {
       Write-Output "Submit loading trip and fare partitions # $i"
       if ($sqlauth -eq 0) {
          # Use Windows authentication
          Start-Job -ScriptBlock $ScriptBlock1 -Arg ($dbname, $tbname, $basename, $fmtfile, $indir, $logdir, $i)
       } 
       else {
          # Use SQL authentication
          Start-Job -ScriptBlock $ScriptBlock2 -Arg ($dbname, $tbname, $basename, $fmtfile, $indir, $logdir, $i, $sqlusr, $server, $pass)
       }
    }
    
    Get-Job
    
    # Optional - Wait till all jobs complete and report date and time
    date
    While (Get-Job -State "Running") { Start-Sleep 10 }
    date


## <a name="create-indexes-to-optimize-joins-and-query-performance"></a>Opret indeks for at optimere joinforbindelser og forespørgslens ydeevne

- Hvis du vil udtrække data til modeller fra flere tabeller, kan du oprette indekser på tasterne join join-ydeevne kan forbedres.

- [Opret indeks](https://technet.microsoft.com/library/ms188783.aspx) (grupperede eller ikke-grupperede) rettet mod den samme filgruppe for hver partition for f.eks.:

        CREATE CLUSTERED INDEX <table_idx> ON <table_name>( [include index columns here] )
        ON <TablePScheme>(<partition)field>)
eller,

        CREATE INDEX <table_idx> ON <table_name>( [include index columns here] )
        ON <TablePScheme>(<partition)field>)

 > [AZURE.NOTE] Du kan vælge at oprette indekser før bulk import af data. Oprettelse af indeks, før du importerer bulk bliver langsom indlæsning af data.


## <a name="advanced-analytics-process-and-technology-in-action-example"></a>Avanceret analyse proces og teknologi i aktion eksempel

Til slut gennemgang eksempel på brug af Cortana Analytics processen med en offentlig dataset, se [Cortana Analytics proces i aktion: Brug af SQL Server](machine-learning-data-science-process-sql-walkthrough.md).
 
