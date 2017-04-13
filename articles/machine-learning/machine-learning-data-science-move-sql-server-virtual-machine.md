<properties 
    pageTitle="Flyt data til SQL Server på en Azure virtuelt | Azure" 
    description="Flytte data fra flade filer eller fra en lokal SQL Server til SQL Server på Azure VM." 
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
    ms.date="09/14/2016" 
    ms.author="bradsev" /> 

# <a name="move-data-to-sql-server-on-an-azure-virtual-machine"></a>Flyt data til SQL Server på en Azure virtuelt

Dette emne beskrives indstillingerne til at flytte data fra flade filer (csv- eller TSV formater) eller fra en lokal SQL Server til SQL Server på en Azure virtuel maskine. Disse opgaver til at flytte data til skyen er en del af processen Team Data videnskabelige.

Efter et emne, der viser indstillingerne for at flytte data til en Azure SQL-Database til at lære mere maskine, kan du se [flytte data til en Azure SQL-Database til Azure Machine Learning](machine-learning-data-science-move-sql-azure.md).

I **menuen** under links til emner, der beskriver, hvordan indtager data i andre destinationsadresse-miljøer, hvor dataene kan være gemt og behandles under Team Data videnskabelige proces (TDSP).

[AZURE.INCLUDE [cap-ingest-data-selector](../../includes/cap-ingest-data-selector.md)]


Den følgende tabel opsummerer indstillingerne til at flytte data til SQL Server på en Azure virtuel maskine.

<b>KILDE</b> |<b>DESTINATION: SQL Server på Azure VM</b> |
------------------ |-------------------- |
<b>Flad fil</b> |1. <a href="#insert-tables-bcp">kommandolinjen flere kopi utility (BCP)</a><br> 2. <a href="#insert-tables-bulkquery">flere Indsæt SQL-forespørgsel</a><br> 3. <a href="#sql-builtin-utilities">grafiske indbyggede funktioner i SQL Server</a>
<b>Lokal SQL Server</b> | 1. <a href="#deploy-a-sql-server-database-to-a-microsoft-azure-vm-wizard">Implementer en SQL Server-Database til en Microsoft Azure VM guide</a><br> 2. <a href="#export-flat-file">eksportere til en flad fil</a><br> 3. <a href="#sql-migration">guiden Overførsel af SQL-Database</a> <br> 4. <a href="#sql-backup">Webdatabase tilbage op og gendanne</a><br>

Bemærk, at dette dokument forudsætter, at SQL-kommandoer, udføres fra SQL Server Management Studio eller Visual Studio Database Stifinder.

> [AZURE.TIP] Du kan bruge [Azure Data Factory](https://azure.microsoft.com/services/data-factory/) som et alternativ til at oprette og planlægge en rørledning, der flytter data til en SQL Server VM på Azure. Yderligere oplysninger finder du se [kopiere data med Azure Data Factory (kopi aktivitet)](../data-factory/data-factory-data-movement-activities.md).


## <a name="prereqs"></a>Forudsætninger
Dette selvstudium forudsætter, at du har:

* En **Azure-abonnement**. Hvis du ikke har et abonnement, kan du tilmelde dig en [gratis prøveversion](https://azure.microsoft.com/pricing/free-trial/).
* En **Azure-lager-konto**. Du vil bruge en konto til Azure-lager til lagring af data i dette selvstudium. Hvis du ikke har en Azure-lager-konto, kan du se artiklen [Opret en lagerplads konto](../storage/storage-create-storage-account.md#create-a-storage-account) . Når du har oprettet kontoen lagerplads, skal du hente den kontonøgle, der bruges til at få adgang til opbevaring. Få [vist, Kopiér og Regenerer lagerplads adgangstaster](../storage/storage-create-storage-account.md#view-copy-and-regenerate-storage-access-keys).
* Klargjort **SQL Server på en Azure VM**. Yderligere oplysninger finder du [konfigurere en virtuel maskine Azure SQL Server, som en server til IPython notesbog til avanceret analyse](machine-learning-data-science-setup-sql-server-virtual-machine.md).
* Installeret og konfigureret **Azure PowerShell** lokalt. Finde en vejledning, se, [hvordan du installerer og konfigurerer Azure PowerShell](../powershell-install-configure.md).


## <a name="filesource_to_sqlonazurevm"></a>Flytte data fra en flad fil kilde til SQL Server på en Azure VM

Hvis dine data er i en flad fil (arrangeret i en række/kolonne format), kan det flyttes til SQL Server VM på Azure via følgende metoder:

1. [Kommandolinjen flere kopi utility (BCP)](#insert-tables-bcp) 
2. [Flere Indsæt SQL-forespørgsel](#insert-tables-bulkquery)
3. [Grafiske indbyggede funktioner i SQL Server (Importér/Eksportér, SSIS)](#sql-builtin-utilities)


### <a name="insert-tables-bcp"></a>Kommandolinjen flere kopi utility (BCP)

BCP er et kommandolinjen værktøj, der er installeret med SQL Server og er et af de hurtigste måder til at flytte data. Det fungerer på tværs af alle tre SQL Server-varianter, (lokal SQL Server, SQL Azure og SQL Server VM på Azure). 

> [AZURE.NOTE]**Hvor dataene skal for BCP?**  
> Selvom det ikke er påkrævet, at de filer, der indeholder kildedataene, der er placeret på samme computer som destination SQL server giver mulighed for hurtigere overførsler (netværk hastighed vs lokal disk EY hastighed). Du kan flytte flad filer, der indeholder data til maskinen hvor SQL Server er installeret ved hjælp af forskellige fil kopiere funktioner som [AZCopy](../storage/storage-use-azcopy.md), [Azure Storage Explorer](http://storageexplorer.com/) eller windows kopiere/indsætte via RDP Remote Desktop Protocol ().

1. Sikre, at databasen og tabellerne oprettes på target SQL Server-databasen. Her er et eksempel på, hvordan du gør dette ved hjælp af den `Create Database` og `Create Table` kommandoer:

        CREATE DATABASE <database_name>
    
        CREATE TABLE <tablename>
        (
            <columnname1> <datatype> <constraint>,
            <columnname2> <datatype> <constraint>,
            <columnname3> <datatype> <constraint>
        ) 
    
2. Opret den filformatet, der beskriver skemaet for tabellen ved at udstede følgende kommando fra kommandolinjen på computeren, hvor bcp er installeret.

    `bcp dbname..tablename format nul -c -x -f exportformatfilename.xml -S servername\sqlinstance -T -t \t -r \n` 

3. Indsætte data i databasen ved hjælp af kommandoen bcp på følgende måde. Dette bør arbejde fra kommandolinjen forudsætter, at der SQL Server er installeret på samme computer:

    `bcp dbname..tablename in datafilename.tsv -f exportformatfilename.xml -S servername\sqlinstancename -U username -P password -b block_size_to_move_in_single_attemp -t \t -r \n`

> **Optimere BCP indsætter** Se følgende artikel ['Retningslinjer for optimering af masseimport'](https://technet.microsoft.com/library/ms177445%28v=sql.105%29.aspx) til at optimere sådanne indsættes.


### <a name="insert-tables-bulkquery-parallel"></a>Parallelizing indsættes for hurtigere flytning af Data

Hvis de data, du vil flytte, er stor, kan du gøre ting ved at udføre flere BCP kommandoer samtidigt parallelt i et PowerShell-Script.

> [AZURE.NOTE]**Big data indtagelse** Du kan optimere indlæsning til små og store datasæt af data ved at dele dine logiske og fysiske databasetabeller ved hjælp af flere filgrupper og partition tabeller. Se [Parallelle Indlæs SQL partitionstabeller](machine-learning-data-science-parallel-load-sql-partitioned-tables.md), du kan finde flere oplysninger om oprettelse og indlæsning af data til partitionstabeller.


Nedenstående eksempel PowerShell script demonstrerer parallelle indsættes ved hjælp af bcp:
    
    $NO_OF_PARALLEL_JOBS=2

     Set-ExecutionPolicy RemoteSigned #set execution policy for the script to execute
     # Define what each job does
       $ScriptBlock = {
           param($partitionnumber)

           #Explictly using SQL username password
           bcp database..tablename in datafile_path.csv -F 2 -f format_file_path.xml -U username@servername -S tcp:servername -P password -b block_size_to_move_in_single_attempt -t "," -r \n -o path_to_outputfile.$partitionnumber.txt

            #Trusted connection w.o username password (if you are using windows auth and are signed in with that credentials)
            #bcp database..tablename in datafile_path.csv -o path_to_outputfile.$partitionnumber.txt -h "TABLOCK" -F 2 -f format_file_path.xml  -T -b block_size_to_move_in_single_attempt -t "," -r \n 
      }
    

    # Background processing of all partitions
    for ($i=1; $i -le $NO_OF_PARALLEL_JOBS; $i++)
    {
      Write-Debug "Submit loading partition # $i"
      Start-Job $ScriptBlock -Arg $i      
    }
    

    # Wait for it all to complete
    While (Get-Job -State "Running")
    {
      Start-Sleep 10
      Get-Job
    }
    
    # Getting the information back from the jobs
    Get-Job | Receive-Job
    Set-ExecutionPolicy Restricted #reset the execution policy


### <a name="insert-tables-bulkquery"></a>Flere Indsæt SQL-forespørgsel

[Flere indsætte SQL-forespørgsel](https://msdn.microsoft.com/library/ms188365) kan bruges til at importere data til databasen fra række/kolonne baseret filer (understøttede datatyper er dækket i[Forberede Data for flere eksportere eller importere (SQL Server)](https://msdn.microsoft.com/library/ms188609)) emne. 

Her er nogle eksempel kommandoer til at indsætte flere er som nedenfor:  

1. Analysere dine data, og Angiv eventuelle brugerdefinerede indstillinger, før du importerer for at sikre at, SQL Server-databasen, får det samme format for en hvilken som helst speciel felter som datoer. Her er et eksempel på, hvordan du kan angive datoformatet som år-måned-dag (Hvis dine data indeholder datoen i år-måned-dag-format):

        SET DATEFORMAT ymd; 
    
2. Importere data ved hjælp af masseimport importere sætninger:

        BULK INSERT <tablename>
        FROM    
        '<datafilename>'
        WITH 
        (
        FirstRow=2,
        FIELDTERMINATOR =',', --this should be column separator in your data
        ROWTERMINATOR ='\n'   --this should be the row separator in your data
        )
      

### <a name="sql-builtin-utilities"></a>Indbyggede funktioner i SQL Server

Du kan bruge SQL Server Integration Services (SSIS) til at importere data til SQL Server VM på Azure fra en flad fil. SSIS er tilgængelig i to studio miljøer. Yderligere oplysninger finder du [Integration Services (SSIS) og Studio miljøer](https://technet.microsoft.com/library/ms140028.aspx):

- SQL Server Data Tools vide, skal du se [Microsoft SQL Server Data Tools](https://msdn.microsoft.com/data/tools.aspx)  
- Guiden Import og eksport vide, skal du se [guiden SQL Server Import og eksport](https://msdn.microsoft.com/library/ms141209.aspx)


## <a name="sqlonprem_to_sqlonazurevm"></a>Flytte Data fra lokale SQL Server til SQL Server på en Azure VM

Du kan også bruge følgende overførsel strategier:

1. [Anvende en SQL Server-Database til en Microsoft Azure VM guide](#deploy-a-sql-server-database-to-a-microsoft-azure-vm-wizard)
2. [Eksportere til en flad fil](#export-flat-file) 
3. [Guiden Overførsel af SQL-Database](#sql-migration)
4. [Webdatabase tilbage op og gendanne](#sql-backup)

Vi beskriver hver af disse nedenfor:

### <a name="deploy-a-sql-server-database-to-a-microsoft-azure-vm-wizard"></a>Anvende en SQL Server-Database til en Microsoft Azure VM guide

**Implementer en SQL Server-Database til en Microsoft Azure VM guide** er en enkel og anbefalede måde at flytte data fra en lokal SQL Server-forekomst til SQL Server på en Azure VM. Du kan finde detaljerede trin samt en diskussion på alternativer, under [overførsel en database til SQL Server på en Azure VM](../virtual-machines/virtual-machines-windows-migrate-sql.md).

### <a name="export-flat-file"></a>Eksportere til en flad fil

Forskellige metoder kan bruges til masseforsendelse Eksporter data fra en On-Premises SQL Server, som beskrevet i emnet [flere Import og eksport af Data (SQL Server)](https://msdn.microsoft.com/library/ms175937.aspx) . Dette dokument dækker flere Copy Program (BCP) som et eksempel. Når dataene er eksporteret til en flad fil, kan kun importeres til en anden SQL server ved hjælp af masseimport. 

1. Eksportere dataene fra lokal SQL Server til en fil ved hjælp af værktøjet bcp således

    `bcp dbname..tablename out datafile.tsv -S  servername\sqlinstancename -T -t \t -t \n -c`

2. Oprette databasen og tabellen på SQL Server VM på Azure ved hjælp af den `create database` og `create table` tabel skemaet eksporteres i trin 1.

3. Oprette en fil i format til at beskrive tabel skemaet for de data, der eksporteres/importeret. Oplysninger om filformatet er beskrevet i [oprette en fil i Format (SQL Server)](https://msdn.microsoft.com/library/ms191516.aspx).

    Formatere generering af filen når kører BCP fra SQL Server-computer 

        bcp dbname..tablename format nul -c -x -f exportformatfilename.xml -S servername\sqlinstance -T -t \t -r \n 

    Formatere generering af filen når kører BCP fra en fjernplacering mod en SQL Server 

        bcp dbname..tablename format nul -c -x -f  exportformatfilename.xml  -U username@servername.database.windows.net -S tcp:servername -P password  --t \t -r \n
        
    
4. Bruge en af de metoder, der er beskrevet i afsnittet [Flytte Data fra kildefilen](#filesource_to_sqlonazurevm) til at flytte data i flade filer til en SQL Server.

### <a name="sql-migration"></a>Guiden Overførsel af SQL-Database

[Guiden til overflytning af SQL Server Database](http://sqlazuremw.codeplex.com/) indeholder en brugervenlig metode til at flytte data mellem to forekomster af SQL server. Det gør det muligt for brugerne for at tilknytte dataskemaet mellem kilder og destinationstabellerne, Vælg kolonnetyper og forskellige andre funktioner. Det bruges flere kopi (BCP) under overfladen. Et skærmbillede af skærmbilledet Velkommen til guiden SQL Database overflytning er vist nedenfor.  

![Guiden til SQL Server-overførsel][2]

### <a name="sql-backup"></a>Webdatabase tilbage op og gendanne

SQL Server understøtter: 

1. [Webdatabase tilbage op og gendanne funktionalitet](https://msdn.microsoft.com/library/ms187048.aspx) (begge dele til en lokal fil eller bacpac eksportere til blob) og [Data niveau programmer](https://msdn.microsoft.com/library/ee210546.aspx) (ved hjælp af bacpac). 
2. Mulighed for at oprette SQL Server FOS direkte på Azure med et kopierede database eller en kopi til en eksisterende SQL Azure-database. Se [Brug af guiden Kopier Database](https://msdn.microsoft.com/library/ms188664.aspx)kan finde flere oplysninger. 

Et skærmbillede af Database bag op/Gendan indstillinger fra SQL Server Management Studio er vist nedenfor.

![Værktøj til Import af SQL Server][1]

## <a name="resources"></a>Ressourcer

[Overflytte en Database til SQL Server på en Azure VM](../virtual-machines/virtual-machines-windows-migrate-sql.md)

[SQL Server på virtuelle Azure-computere oversigt](../virtual-machines/virtual-machines-windows-sql-server-iaas-overview.md)

[1]: ./media/machine-learning-data-science-move-sql-server-virtual-machine/sqlserver_builtin_utilities.png
[2]: ./media/machine-learning-data-science-move-sql-server-virtual-machine/database_migration_wizard.png
