<properties
    pageTitle="Konfiguration af Oracle Data vagt i FOS | Microsoft Azure"
    description="Gennemgå en selvstudium til at konfigurere og implementere Oracle Data vagt på Azure virtuelle maskiner til høj tilgængelighed og genoprettelse efter nedbrud."
    services="virtual-machines-windows"
    authors="rickstercdn"
    manager="timlt"
    documentationCenter=""
    tags="azure-service-management"/>
<tags
    ms.service="virtual-machines-windows"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="vm-windows"
    ms.workload="infrastructure-services"
    ms.date="09/06/2016"
    ms.author="rclaus" />

#<a name="configuring-oracle-data-guard-for-azure"></a>Konfiguration af Oracle Data vagt til Azure


Dette selvstudium viser, hvordan du konfigurerer og implementere Oracle Data vagt i virtuelle Azure-computere miljø til høj tilgængelighed og nedbrud. Selvstudiet fokuserer på envejs gentagelse til RAC Oracle-databaser.

Oracle Data vagt understøtter databeskyttelse og nedbrud til Oracle-Database. Det er en enkel, høj ydeevne, drop-in løsning til nedbrud, databeskyttelse og høj tilgængelighed til hele Oracle-database.

Dette selvstudium antages det, at du allerede har teoretisk og praktisk viden om Oracle-Database høj tilgængelighed og nedbrud begreber. Finde [Oracle websted](http://www.oracle.com/technetwork/database/features/availability/index.html) og også [Oracle vagt begreber og Administration af hjælpelinjer](https://docs.oracle.com/cd/E11882_01/server.112/e41134/toc.htm)på oplysninger.

Derudover antages selvstudiet, at du allerede har implementeret følgende forudsætninger:

- Du har allerede gennemset sektionen høj tilgængelighed og overvejelser i forbindelse med genoprettelse efter genoprettelse i emnet [Oracle virtuelt billeder - diverse overvejelser i forbindelse med](virtual-machines-windows-classic-oracle-considerations.md) . Azure understøtter i øjeblikket enkeltstående Oracle-Database forekomster, men ikke Oracle Real Application Clusters (Oracle RAC).


- Du har oprettet to virtuelle maskiner (VM'er) i Azure ved hjælp af den samme platform leveres Oracle Enterprise Edition billede. Kontrollér, at de virtuelle maskiner er i [samme skytjeneste](virtual-machines-windows-load-balance.md) og i det samme virtuelle netværk til at sikre, at de kan få adgang til hinanden via fast privat IP-adresse. Desuden anbefales det at placere FOS i den samme [tilgængelighed indstillet](virtual-machines-windows-manage-availability.md) til at tillade Azure til at placere dem i separate fejl domæner og opgradere domæner. Oracle Data vagt er kun tilgængelig med Oracle-Database Enterprise Edition. Hver computer skal have mindst 2 GB hukommelse og 5 GB ledig diskplads. Du kan finde de mest opdaterede oplysninger om den angivne VM størrelser platform ved [Virtuelt størrelser til Azure](virtual-machines-windows-sizes.md). Hvis du har brug for ekstra disk lydstyrken for din FOS, kan du vedhæfte flere diske. Du kan finde oplysninger [hvordan du vedhæfter en Data Disk til en virtuel maskine](virtual-machines-windows-classic-attach-disk.md).



- Du har angivet virtuelt navnene som "COMPUTER1" til den primære VM og "Computer2" af standby VM på portalen Azure klassisk.

- Du har angivet miljøvariablen **ORACLE_HOME** til at pege på den samme oracle rod installationssti i primær og standby virtuelle maskiner, såsom `C:\OracleDatabase\product\11.2.0\dbhome_1\database`.

- Du logger på din Windows-server som et medlem af **administratorgruppen** eller medlem af gruppen **ORA_DBA** .

I dette selvstudium skal du:

Implementere fysiske standby database-miljø

1. Oprette en primær database

2. Forberede den primære database til oprettelse af standby database

    1. Aktivere logføring af tvunget

    2. Oprette en fil med adgangskode

    3. Konfigurere en logfil over standby Annuller Fortryd

    4. Aktivere arkivering

    5. Angive primære database Initialiseringsparametre

Oprette en fysisk standby database

1. Forberede en initialisering parameter datafil for standby database

2. Konfigurere lytteren og tnsnames for at understøtte databasen på primære og standby computere

    1. Konfigurere listener.ora på begge-servere at holde posterne til begge databaser

    2. For at holde posterne til både primære og standby databaser, kan du konfigurere tnsnames.ora på de primære og standby virtuelle maskiner. 

    3. Start lytteren og kontrollere tnsping på begge virtuelle maskiner til begge tjenester.

3. Start den standby forekomst i nomount tilstand

4. Brug RMAN at klone databasen og til at oprette en standby database

5. Starte den fysiske standby database i tilstanden administrerede nedbrud

6. Bekræfte den fysiske standby database

> [AZURE.IMPORTANT] Dette selvstudium har oprettet og testet mod følgende konfiguration af hardware og software:
>
>|                      | **Primære Database**                      | **Standby Database**                      |
>|----------------------|-------------------------------------------|-------------------------------------------|
>| **Oracle version**   | Oracle11g Enterprise version (11.2.0.4.0) | Oracle11g Enterprise version (11.2.0.4.0) |
>| **Computernavn**     | (Machine1)                                  | Computer2                                  |
>| **Operativsystem** | Windows 2008 R2                           | Windows 2008 R2                           |
>| **Oracle SID**       | TEST                                      | TEST\_STBY                                |
>| **Hukommelse**           | Min 2 GB                                  | Min 2 GB                                  |
>| **Diskplads.**       | Min 5 GB                                  | Min 5 GB                                  |

For efterfølgende versioner af Oracle-Database og Oracle Data vagt, kan der være nogle yderligere ændringer, du skal bruge til at implementere. Den mest opdaterede version-specifikke oplysninger i dokumentationen [Data vagt](http://www.oracle.com/technetwork/database/features/availability/data-guard-documentation-152848.html) og [Oracle-Database](http://www.oracle.com/us/corporate/features/database-12c/index.html) på Oracle-websted.

##<a name="implement-the-physical-standby-database-environment"></a>Implementere fysiske standby database-miljø
### <a name="1-create-a-primary-database"></a>1. oprette en primær database

- Oprette en primær database "TEST" i den primære virtuelle maskine. For at vide, skal du se oprette og konfigurere en Oracle-Database.
- Hvis du vil se navnet på databasen, oprette forbindelse til databasen som for brugeren med SYSDBA rolle i SQL * Plus kommandoprompten, og Kør følgende sætning:

        SQL> select name from v$database;

        The result will display like the following:

        NAME
        ---------
        TEST
- Derefter skal du søge navnene på databasefilerne fra visningen dba_data_files system:

        SQL> select file_name from dba_data_files;
        FILE_NAME
        -------------------------------------------------------------------------------
        C:\ <YourLocalFolder>\TEST\USERS01.DBF
        C:\ <YourLocalFolder>\TEST\UNDOTBS01.DBF
        C:\ <YourLocalFolder>\TEST\SYSAUX01.DBF
        C:\<YourLocalFolder>\TEST\SYSTEM01.DBF
        C:\<YourLocalFolder>\TEST\EXAMPLE01.DBF

### <a name="2-prepare-the-primary-database-for-standby-database-creation"></a>2. forberede den primære database til oprettelse af standby database

Før du opretter en standby database, anbefales det, at du sikre dig, den primære database er konfigureret korrekt. Følgende er en liste over trin, som du skal udføre:

1. Aktivere logføring af tvunget
2. Oprette en fil med adgangskode
3. Konfigurere en logfil over standby Annuller Fortryd
4. Aktivere arkivering
5. Angive primære database Initialiseringsparametre

#### <a name="enable-forced-logging"></a>Aktivere logføring af tvunget

Hvis du vil implementere en Standby-Database, skal vi aktivere 'Tvunget logføring' i den primære database. Denne indstilling sikrer, at selvom handlingen 'uden ' logføring er færdig, kraft logføring har højere prioritet, og alle handlinger er logget på loggene Annuller Fortryd. Derfor Sørg vi for, at alt i den primære database logføres og gentagelse standby omfatter alle handlinger i den primære database. For at aktivere logføring af gældende skal du køre sætningen alter database:

    SQL> ALTER DATABASE FORCE LOGGING;

    Database altered.

#### <a name="create-a-password-file"></a>Oprette en fil med adgangskode

Hvis du vil kunne sende og anvende arkiverede logge fra den primære server til Standby-serveren, skal for adgangskoden være identisk på både primære og standby servere. Det er grunden du opretter en adgangskode fil på den primære database og kopiere det til Standby-serveren.

>[AZURE.IMPORTANT] Når du bruger Oracle-Database 12c, er der en ny bruger, **SYSDG**, som du kan bruge til at administrere Oracle Data vagt. Se [ændringer i Oracle-Database 12 c version](http://docs.oracle.com/database/121/UNXAR/release_changes.htm#UNXAR404)kan finde flere oplysninger.

Desuden Kontrollér, at ORACLE\_hjem miljø er allerede defineret i (machine1). Hvis ikke, du angive den som en miljøvariablen ved hjælp af dialogboksen miljøvariabler. For at få adgang til denne dialogboks ved at starte værktøjet **System** ved at dobbeltklikke på ikonet System i **Kontrolpanel**; derefter skal du klikke på fanen **Avanceret** , og vælg **Miljøvariabler**. Hvis du vil angive miljøvariabler, skal du klikke på knappen **Ny** under **Systemvariabler**. Luk eksisterende Windows kommandoprompten efter konfigurering af miljøvariablerne, og Åbn konfigurere et nyt.

Kør følgende sætning at skifte til Oracle\_hjem directory, som C:\\OracleDatabase\\produkt\\11.2.0\\dbhome\_1\\database.

    cd %ORACLE_HOME%\database

Derefter oprette en adgangskode fil ved hjælp af værktøjet adgangskode fil oprettelse, [ORAPWD](http://docs.oracle.com/cd/B28359_01/server.111/b28310/dba007.htm). I den samme Windows kommandoprompt i COMPUTER1 skal du køre følgende kommando ved at angive adgangskoden til **for**værdien password:

    ORAPWD FILE=PWDTEST.ora PASSWORD=password FORCE=y

Denne kommando opretter en adgangskode-fil, som PWDTEST.ora i ORACLE\_HOME\\database directory. Du skal kopiere denne fil til % ORACLE\_HOME %\\Webdatabase directory i computer2 manuelt.

#### <a name="configure-a-standby-redo-log"></a>Konfigurere en logfil over standby Annuller Fortryd

Derefter skal du konfigurere en logfil over Standby Annuller Fortryd, så primært korrekt kan modtage Annuller Fortryd, når det bliver til en standby. Oprette dem allerede her, kan du også loggene standby Annuller Fortryd oprettes automatisk på standby. Det er vigtigt at konfigurere den Standby annullere Fortryd logfiler (SRL) med samme størrelse som det online annullere Fortryd logge. Størrelsen af de aktuelle standby Annuller Fortryd logfiler skal svare nøjagtigt til størrelsen på de aktuelle primære database online Annuller Fortryd logfiler.

Kør følgende sætning i SQL\*PLUS kommandoprompten i (machine1). V$ logfilen er en systemvisning, der indeholder oplysninger om Annuller Fortryd logfiler.

    SQL> select * from v$logfile;
    GROUP# STATUS  TYPE    MEMBER                                                       IS_
    ---------- ------- ------- ------------------------------------------------------------ ---
    3         ONLINE  C:\<YourLocalFolder>\TEST\REDO03.LOG               NO
    2         ONLINE  C:\<YourLocalFolder>\TEST\REDO02.LOG               NO
    1         ONLINE  C:\<YourLocalFolder>\TEST\REDO01.LOG               NO
    Next, query the v$log system view, displays log file information from the control file.
    SQL> select bytes from v$log;
    BYTES
    ----------
    52428800
    52428800
    52428800


Bemærk, at 52428800 50 MB.

Derefter skal du i SQL\*Plus-vinduet Kør følgende sætninger for at tilføje en ny standby Annuller Fortryd log file-gruppe til en standby database og angive et tal, der identificerer den gruppe, ved hjælp af delsætningen gruppe. Ved hjælp af gruppering af tal kan gøre administrere standby Annuller Fortryd log filgrupper nemmere:

    SQL> ALTER DATABASE ADD STANDBY LOGFILE GROUP 4 'C:\<YourLocalFolder>\TEST\REDO04.LOG' SIZE 50M;
    Database altered.
    SQL> ALTER DATABASE ADD STANDBY LOGFILE GROUP 5 'C:\<YourLocalFolder>\TEST\REDO05.LOG' SIZE 50M;
    Database altered.
    SQL> ALTER DATABASE ADD STANDBY LOGFILE GROUP 6 'C:\<YourLocalFolder>\TEST\REDO06.LOG' SIZE 50M;
    Database altered.

Dernæst skal køre systemvisningen følger at få vist oplysninger om Annuller Fortryd logfiler. Denne handling kontrollerer også, at grupperne standby Annuller Fortryd log filen blev oprettet:

    SQL> select * from v$logfile;
    GROUP# STATUS  TYPE MEMBER IS_
    ---------- ------- ------- --------------------------------------------- ---
    3         ONLINE C:\<YourLocalFolder>\TEST\REDO03.LOG NO
    2         ONLINE C:\<YourLocalFolder>\TEST\REDO02.LOG NO
    1         ONLINE C:\<YourLocalFolder>\TEST\REDO01.LOG NO
    4         STANDBY C:\<YourLocalFolder>\TEST\REDO04.LOG
    5         STANDBY C:\<YourLocalFolder>\TEST\REDO05.LOG NO
    6         STANDBY C:\<YourLocalFolder>\TEST\REDO06.LOG NO
    6 rows selected.

#### <a name="enable-archiving"></a>Aktivere arkivering

Aktivere derefter arkivering ved at køre følgende udtalelser for at sætte den primære database i ARCHIVELOG tilstand og aktivere automatisk arkivering. Du kan aktivere arkiv log tilstand ved at tilslutte databasen og derefter køre kommandoen archivelog.

Først skal logge på som sysdba. I Windows-kommandoprompt skal du køre:

    sqlplus /nolog

    connect / as sysdba

Derefter lukning database i SQL\*Plus kommandoprompten:

    SQL> shutdown immediate;
    Database closed.
    Database dismounted.
    ORACLE instance shut down.

Udfør derefter Start Indlæs kommando for at oprette forbindelse til databasen. Dette sikrer, at Oracle knytter forekomsten til den angivne database.

    SQL> startup mount;
    ORACLE instance started.
    Total System Global Area 1503199232 bytes
    Fixed Size                  2281416 bytes
    Variable Size             922746936 bytes
    Database Buffers          570425344 bytes
    Redo Buffers                7745536 bytes
    Database mounted.

Derefter skal du køre:

    SQL> alter database archivelog;
    Database altered.

Derefter skal du køre Alter database-sætningen med delsætningen åben til rådighed databasen til normal brug:

    SQL> alter database open;

    Database altered.

#### <a name="set-primary-database-initialization-parameters"></a>Angive primære database Initialiseringsparametre

Hvis du vil konfigurere Data vagt, skal du oprette og konfigurere standby parametrene i en almindelig pfile (initialisering parameter tekstfil) første. Når pfile er klar, skal du konvertere den til en server parameter-fil (SPFILE).

Du kan styre det Data vagt-miljø ved hjælp af parametrene i Initialisering. ORA-fil. Når følge dette selvstudium, skal du opdatere den primære database Initialisering. ORA så, at den kan indeholde begge roller: primær eller Standby.

    SQL> create pfile from spfile;
    File created.

Derefter skal du redigere pfile for at tilføje standby parametre. Åbn INITTEST for at gøre dette. ORA filer i placeringen af % ORACLE\_HOME %\\database. Tilføj derefter følgende udtalelser til filen INITTEST.ora. Navnekonventionen for din Initialisering. ORA filen er Initialisering\<YourDatabaseName\>. ORA.

    db_name='TEST'
    db_unique_name='TEST'
    LOG_ARCHIVE_CONFIG='DG_CONFIG=(TEST,TEST_STBY)'
    LOG_ARCHIVE_DEST_1= 'LOCATION=C:\OracleDatabase\archive   VALID_FOR=(ALL_LOGFILES,ALL_ROLES) DB_UNIQUE_NAME=TEST'
    LOG_ARCHIVE_DEST_2= 'SERVICE=TEST_STBY LGWR ASYNC VALID_FOR=(ONLINE_LOGFILES,PRIMARY_ROLE) DB_UNIQUE_NAME=TEST_STBY'
    LOG_ARCHIVE_DEST_STATE_1=ENABLE
    LOG_ARCHIVE_DEST_STATE_2=ENABLE
    REMOTE_LOGIN_PASSWORDFILE=EXCLUSIVE
    LOG_ARCHIVE_FORMAT=%t_%s_%r.arc
    LOG_ARCHIVE_MAX_PROCESSES=30
    # Standby role parameters --------------------------------------------------------------------
    fal_server=TEST_STBY
    fal_client=TEST
    standby_file_management=auto
    db_file_name_convert='TEST_STBY','TEST'
    log_file_name_convert='TEST_STBY','TEST'
    # ---------------------------------------------------------------------------------------------


Forrige sætning blokering indeholder tre vigtige konfiguration elementer:
-   **LOG_ARCHIVE_CONFIG...:** Du definerer id'erne entydig database ved hjælp af denne erklæring.
-   **LOG_ARCHIVE_DEST_1...:** Du definerer den lokale arkiv mappeplacering ved hjælp af denne erklæring. Vi anbefaler, at du opretter en ny mappe til din database arkivering behov og angiver den lokale arkiv placering ved hjælp af denne erklæring eksplicit i stedet for at bruge Oracle-standard mappen % ORACLE_HOME%\database\archive.
-   **LOG_ARCHIVE_DEST_2... ... LGWR ASYNC:** du definerer en asynkron log writer proces (LGWR) til at indsamle transaktion Annuller Fortryd data og overføre den til standby destinationer. Her angiver DB_UNIQUE_NAME et entydigt navn til databasen på destination standby serveren.

Når den nye parameterfil er klar, skal du oprette spfile fra den.

Først skal lukning databasen:

    SQL> shutdown immediate;

    Database closed.

    Database dismounted.

    ORACLE instance shut down.

Derefter skal du køre Start nomount kommando på følgende måde:

    SQL> startup nomount pfile='c:\OracleDatabase\product\11.2.0\dbhome_1\database\initTEST.ora';
    ORACLE instance started.
    Total System Global Area 1503199232 bytes
    Fixed Size                  2281416 bytes
    Variable Size             922746936 bytes
    Database Buffers          570425344 bytes
    Redo Buffers                7745536 bytes

Opret nu en spfile:

    SQL>create spfile frompfile='c:\OracleDatabase\product\11.2.0\dbhome\_1\database\initTEST.ora';

    File created.

Derefter lukning databasen:

    SQL> shutdown immediate;

    ORA-01507: database not mounted

Derefter skal du bruge startkommandoen til at starte en forekomst:

    SQL> startup;
    ORACLE instance started.
    Total System Global Area 1503199232 bytes
    Fixed Size                  2281416 bytes
    Variable Size             922746936 bytes
    Database Buffers          570425344 bytes
    Redo Buffers                7745536 bytes
    Database mounted.
    Database opened.

##<a name="create-a-physical-standby-database"></a>Oprette en fysisk standby database
Dette afsnit beskrives de trin, som du skal udføre på computer2 til at forberede den fysiske standby database.

Først skal du Fjernskrivebord til computer2 via Azure klassisk-portalen.

Klik derefter på Standby serveren (computer2), oprette alle de mapper, der er nødvendige for standby databasen, eksempelvis C:\\\<YourLocalFolder\>\\TEST. Sørg for, mappestrukturen stemmer overens mappestrukturen på COMPUTER1 skal huske alle de nødvendige filer, som controlfile, datafiles, redologfiles, udump, bdump og cdump filer under følge dette selvstudium. Desuden definere ORACLE\_til hjemmet og ORACLE\_BASE miljøvariabler i computer2. Hvis ikke, du angive dem som en miljøvariablen ved hjælp af dialogboksen miljøvariabler. For at få adgang til denne dialogboks ved at starte værktøjet **System** ved at dobbeltklikke på ikonet System i **Kontrolpanel**; derefter skal du klikke på fanen **Avanceret** , og vælg **Miljøvariabler**. Hvis du vil angive miljøvariabler, skal du klikke på knappen **Ny** under **Systemvariabler**. Efter konfigurering af miljøvariablerne, skal du lukke eksisterende Windows kommandoprompten og åbne en ny for at se ændringerne.

Derefter skal du gøre følgende:

1. Forberede en initialisering parameter datafil for standby database

2. Konfigurere lytteren og tnsnames for at understøtte databasen på primære og standby computere

    1. Konfigurere listener.ora på begge-servere at holde posterne til begge databaser

    2. Konfigurere tnsnames.ora på de primære og standby virtuelle maskiner til at holde posterne til både primære og standby databaser

    3. Start lytteren og kontrollere tnsping på begge virtuelle maskiner til begge tjenester.

3. Start den standby forekomst i nomount tilstand

4. Brug RMAN at klone databasen og til at oprette en standby database

5. Starte den fysiske standby database i tilstanden administrerede nedbrud

6. Bekræfte den fysiske standby database

### <a name="1-prepare-an-initialization-parameter-file-for-standby-database"></a>1. forberede en initialisering parameter datafil for standby database

Dette afsnit viser, hvordan til at forberede en initialisering parameter datafil for standby databasen. Gør du ved først at kopiere INITTEST. ORA fil fra computeren 1 til computer2 manuelt. Du skal kunne se INITTEST. ORA filen i feltet % ORACLE\_HOME %\\databasemappe i begge computere. Rediger derefter filen INITTEST.ora i computer2 til implementerer standby rollen som angivet nedenfor:

    db_name='TEST'
    db_unique_name='TEST_STBY'
    db_create_file_dest='c:\OracleDatabase\oradata\test_stby’
    db_file_name_convert=’TEST’,’TEST_STBY’
    log_file_name_convert='TEST','TEST_STBY'


    job_queue_processes=10
    LOG_ARCHIVE_CONFIG='DG_CONFIG=(TEST,TEST_STBY)'
    LOG_ARCHIVE_DEST_1='LOCATION=c:\OracleDatabase\TEST_STBY\archives VALID_FOR=(ALL_LOGFILES,ALL_ROLES) DB_UNIQUE_NAME=’TEST'
    LOG_ARCHIVE_DEST_2='SERVICE=TEST LGWR ASYNC VALID_FOR=(ONLINE_LOGFILES,PRIMARY_ROLE)
    LOG_ARCHIVE_DEST_STATE_1='ENABLE'
    LOG_ARCHIVE_DEST_STATE_2='ENABLE'
    LOG_ARCHIVE_FORMAT='%t_%s_%r.arc'
    LOG_ARCHIVE_MAX_PROCESSES=30


Forrige sætning blokering indeholder to konfiguration af vigtige elementer:

-   **\*. LOG_ARCHIVE_DEST_1:** skal du oprette mappen c:\OracleDatabase\TEST_STBY\archives i computer2 manuelt.
-   **\*. LOG_ARCHIVE_DEST_2:** dette er et valgfrit trin. Du kan angive dette, som det kan være nødvendigt, når den primære maskine er i vedligeholdelse og standby maskinen bliver en primær database.

Derefter skal du starte den standby forekomst. Skriv følgende kommando fra en Windows-kommandoprompt til at oprette en Oracle-forekomst ved at oprette en Windows-tjeneste på databaseserveren standby:

    oradim -NEW -SID TEST\_STBY -STARTMODE MANUAL

Kommandoen **Oradim** opretter en Oracle-forekomst, men starter ikke den. Du kan finde det i C:\\OracleDatabase\\produkt\\11.2.0\\dbhome\_1\\BIN-mappe.

##<a name="configure-the-listener-and-tnsnames-to-support-the-database-on-primary-and-standby-machines"></a>Konfigurere lytteren og tnsnames for at understøtte databasen på primære og standby computere
Før du opretter en standby database, skal du kontrollere, at de primære og standby databaser i konfigurationen kan tale med hinanden. For at gøre dette, skal du konfigurere både lytteren og TNSNames, enten manuelt eller ved hjælp af funktionen NETCA for konfiguration. Dette er en obligatorisk opgave, når du bruger værktøjet gendannelse Manager (RMAN).

### <a name="configure-listenerora-on-both-servers-to-hold-entries-for-both-databases"></a>Konfigurere listener.ora på begge-servere at holde posterne til begge databaser

Fjernskrivebord (machine1), og rediger listener.ora fil som angivet nedenfor. Når du redigere filen listener.ora, altid skal du kontrollere, at åbning og afsluttende parentes justere i den samme kolonne. Du kan finde filen listener.ora i følgende mappe: c:\\OracleDatabase\\produkt\\11.2.0\\dbhome\_1\\netværk\\administrator\\.

    # listener.ora Network Configuration File: C:\OracleDatabase\product\11.2.0\dbhome_1\network\admin\listener.ora

    # Generated by Oracle configuration tools.

    SID_LIST_LISTENER =
      (SID_LIST =
        (SID_DESC =
          (SID_NAME = test)
          (ORACLE_HOME = C:\OracleDatabase\product\11.2.0\dbhome_1)
          (PROGRAM = extproc)
          (ENVS = "EXTPROC_DLLS=ONLY:C:\OracleDatabase\product\11.2.0\dbhome_1\bin\oraclr11.dll")
        )
      )

    LISTENER =
      (DESCRIPTION_LIST =
        (DESCRIPTION =
          (ADDRESS = (PROTOCOL = TCP)(HOST = MACHINE1)(PORT = 1521))
          (ADDRESS = (PROTOCOL = IPC)(KEY = EXTPROC1521))
        )
      )

Næste, remote desktop computer2, og rediger listener.ora fil på følgende måde: # listener.ora netværk konfigurationsfil: C:\OracleDatabase\product\11.2.0\dbhome_1\network\admin\listener.ora

    # Generated by Oracle configuration tools.

    SID_LIST_LISTENER =
      (SID_LIST =
        (SID_DESC =
          (SID_NAME = test_stby)
          (ORACLE_HOME = C:\OracleDatabase\product\11.2.0\dbhome_1)
          (PROGRAM = extproc)
          (ENVS = "EXTPROC_DLLS=ONLY:C:\OracleDatabase\product\11.2.0\dbhome_1\bin\oraclr11.dll")
        )
      )

    LISTENER =
      (DESCRIPTION_LIST =
        (DESCRIPTION =
          (ADDRESS = (PROTOCOL = TCP)(HOST = MACHINE2)(PORT = 1521))
          (ADDRESS = (PROTOCOL = IPC)(KEY = EXTPROC1521))
        )
      )


### <a name="configure-tnsnamesora-on-the-primary-and-standby-virtual-machines-to-hold-entries-for-both-primary-and-standby-databases"></a>Konfigurere tnsnames.ora på de primære og standby virtuelle maskiner til at holde posterne til både primære og standby databaser

Fjernskrivebord (machine1), og Rediger filen tnsnames.ora som angivet nedenfor. Du kan finde filen tnsnames.ora i følgende mappe: c:\\OracleDatabase\\produkt\\11.2.0\\dbhome\_1\\netværk\\administrator\\.

    TEST =
      (DESCRIPTION =
        (ADDRESS_LIST =
          (ADDRESS = (PROTOCOL = TCP)(HOST = MACHINE1)(PORT = 1521))
        )
        (CONNECT_DATA =
          (SERVICE_NAME = test)
        )
      )

    TEST_STBY =
      (DESCRIPTION =
        (ADDRESS_LIST =
          (ADDRESS = (PROTOCOL = TCP)(HOST = MACHINE2)(PORT = 1521))
        )
        (CONNECT_DATA =
          (SERVICE_NAME = test_stby)
        )
      )

Fjernskrivebord computer2, og rediger tnsnames.ora fil på følgende måde:

    TEST =
      (DESCRIPTION =
        (ADDRESS_LIST =
          (ADDRESS = (PROTOCOL = TCP)(HOST = MACHINE1)(PORT = 1521))
        )
        (CONNECT_DATA =
          (SERVICE_NAME = test)
        )
      )

    TEST_STBY =
      (DESCRIPTION =
        (ADDRESS_LIST =
          (ADDRESS = (PROTOCOL = TCP)(HOST = MACHINE2)(PORT = 1521))
        )
        (CONNECT_DATA =
          (SERVICE_NAME = test_stby)
        )
      )


### <a name="start-the-listener-and-check-tnsping-on-both-virtual-machines-to-both-services"></a>Start lytteren og kontrollere tnsping på begge virtuelle maskiner til begge tjenester.

Åbne en ny Windows-kommandoprompt i både primære og standby virtuelle maskiner, og Kør følgende sætninger:

    C:\Users\DBAdmin>tnsping test

    TNS Ping Utility for 64-bit Windows: Version 11.2.0.1.0 - Production on 14-NOV-2013 06:29:08
    Copyright (c) 1997, 2010, Oracle.  All rights reserved.
    Used parameter files:
    C:\OracleDatabase\product\11.2.0\dbhome_1\network\admin\sqlnet.ora
    Used TNSNAMES adapter to resolve the alias
    Attempting to contact (DESCRIPTION = (ADDRESS_LIST = (ADDRESS = (PROTOCOL = TCP)(HOST = MACHINE1)(PORT = 1521))) (CONNECT_DATA = (SER
    VICE_NAME = test)))
    OK (0 msec)


    C:\Users\DBAdmin>tnsping test_stby

    TNS Ping Utility for 64-bit Windows: Version 11.2.0.1.0 - Production on 14-NOV-2013 06:29:16
    Copyright (c) 1997, 2010, Oracle.  All rights reserved.
    Used parameter files:
    C:\OracleDatabase\product\11.2.0\dbhome_1\network\admin\sqlnet.ora
    Used TNSNAMES adapter to resolve the alias
    Attempting to contact (DESCRIPTION = (ADDRESS_LIST = (ADDRESS = (PROTOCOL = TCP)(HOST = MACHINE2)(PORT = 1521))) (CONNECT_DATA = (SER
    VICE_NAME = test_stby)))
    OK (260 msec)


##<a name="start-up-the-standby-instance-in-nomount-state"></a>Start den standby forekomst i nomount tilstand
Konfigurere miljøet til at understøtte standby databasen på en standby virtuel maskine (computer2).

Først skal Kopier Adgangskodefilen fra den primære computer (COMPUTER1) til standby maskinen (computer2) manuelt. Det er nødvendigt, som **for** adgangskoden skal være identiske på begge computere.

Åbn kommandoprompten Windows i computer2 derefter og Konfigurer miljøvariabler til at pege på Standby databasen på følgende måde:

    SET ORACLE_HOME=C:\OracleDatabase\product\11.2.0\dbhome_1
    SET ORACLE_SID=TEST_STBY

Dernæst skal starte Standby databasen i nomount tilstand, og opret derefter en spfile.

Starte databasen:

    SQL>shutdown immediate;

    SQL>startup nomount
    ORACLE instance started.

    Total System Global Area  747417600 bytes
    Fixed Size                  2179496 bytes
    Variable Size             473960024 bytes
    Database Buffers          264241152 bytes
    Redo Buffers                7036928 bytes


##<a name="use-rman-to-clone-the-database-and-to-create-a-standby-database"></a>Brug RMAN at klone databasen og til at oprette en standby database
Du kan bruge værktøjet gendannelse Manager (RMAN) til at tage en sikkerhedskopi af den primære database til at oprette den fysiske standby database.

En streng Fjernskrivebord til standby virtuelt (computer2), og Kør værktøjet RMAN ved at angive en fuld forbindelse for begge MÅLET (primære database, COMPUTER1) og EKSTRAKOMPONENTEN (standby database, computer2) forekomster.

>[AZURE.IMPORTANT] Brug ikke godkendelsen af operativsystem, som der er ingen database i standby servercomputeren endnu.

    C:\> RMAN TARGET sys/password@test AUXILIARY sys/password@test_STBY

    RMAN>DUPLICATE TARGET DATABASE
      FOR STANDBY
      FROM ACTIVE DATABASE
      DORECOVER
        NOFILENAMECHECK;

##<a name="start-the-physical-standby-database-in-managed-recovery-mode"></a>Starte den fysiske standby database i tilstanden administrerede nedbrud
Dette selvstudium viser, hvordan du opretter en fysisk standby database. Finde oplysninger om oprettelse af en logisk standby database, i dokumentationen til Oracle.

Åbn SQL\*Plus kommandoprompten, og Aktivér Data vagt på standby virtuelt eller serveren (computer2) på følgende måde:

    SHUTDOWN IMMEDIATE;
    STARTUP MOUNT;
    ALTER DATABASE RECOVER MANAGED STANDBY DATABASE DISCONNECT FROM SESSION;

Når du åbner standby databasen i **TILSLUTTE** tilstand, arkivere loggen levering fortsætter og administrerede genoprettelsen fortsætter log anvende på standby databasen. Dette sikrer, at standby databasen forbliver opdateret med den primære database. Bemærk, at standby databasen ikke må være tilgængelige for rapportering i dette tidsrum.

Når du åbner standby databasen i **Læse kun** tilstand, arkivet logge levering fortsætter. Men administrerede genoprettelsen stopper. Derved standby databasen til bliver stadig forældet, indtil administrerede genoprettelsen genoptages. Du kan få adgang til standby databasen til brug ved rapportering i denne periode, men data muligvis ikke afspejler de seneste ændringer.

Vi anbefaler Generelt, at du beholder den standby database i **TILSLUTTE** tilstand for at holde dataene i standby databasen opdateret Hvis der er en fejl i den primære database. Du kan dog holde standby databasen i tilstanden **Læse kun** for rapportering afhængigt af dit program krav. Nedenstående trin viser, hvordan du aktiverer Data vagt i skrivebeskyttet tilstand, ved hjælp af SQL\*Plus:

    SHUTDOWN IMMEDIATE;
    STARTUP MOUNT;
    ALTER DATABASE OPEN READ ONLY;


##<a name="verify-the-physical-standby-database"></a>Bekræfte den fysiske standby database
Dette afsnit viser, hvordan til kontrol af høj tilgængelighed konfigurationen som administrator.

Åbn SQL\*Plus kommandopromptvindue, og Kontrollér arkiverede annullere Fortryd log på Standby Virtual Machine (computer2):

    SQL> show parameters db_unique_name;

    NAME                                TYPE       VALUE
    ------------------------------------ ----------- ------------------------------
    db_unique_name                      string     TEST_STBY

    SQL> SELECT NAME FROM V$DATABASE

    SQL> SELECT SEQUENCE#, FIRST_TIME, NEXT_TIME, APPLIED FROM V$ARCHIVED_LOG ORDER BY SEQUENCE#;

    SEQUENCE# FIRST_TIM NEXT_TIM APPLIED
    ----------------  ---------------  --------------- ------------
    45                    23-FEB-14   23-FEB-14   YES
    45                    23-FEB-14   23-FEB-14   NO
    46                    23-FEB-14   23-FEB-14   NO
    46                    23-FEB-14   23-FEB-14   YES
    47                    23-FEB-14   23-FEB-14   NO
    47                    23-FEB-14   23-FEB-14   NO

Åbn SQL * Plus kommandoprompt-vinduet, og Skift logfiles på den primære maskine (COMPUTER1):

    SQL> alter system switch logfile;
    System altered.

    SQL> archive log list
    Database log mode              Archive Mode
    Automatic archival             Enabled
    Archive destination            C:\OracleDatabase\archive
    Oldest online log sequence     69
    Next log sequence to archive   71
    Current log sequence           71

Kontrollere arkiverede Annuller Fortryd log på Standby Virtual Machine (computer2):

    SQL> SELECT SEQUENCE#, FIRST_TIME, NEXT_TIME, APPLIED FROM V$ARCHIVED_LOG ORDER BY SEQUENCE#;

    SEQUENCE# FIRST_TIM NEXT_TIM APPLIED
    ----------------  ---------------  --------------- ------------
    45                    23-FEB-14   23-FEB-14   YES
    46                    23-FEB-14   23-FEB-14   YES
    47                    23-FEB-14   23-FEB-14   YES
    48                    23-FEB-14   23-FEB-14   YES

    49                    23-FEB-14   23-FEB-14   YES
    50                    23-FEB-14   23-FEB-14   IN-MEMORY

Søg efter en hvilken som helst afstand på Standby Virtual Machine (computer2):

    SQL> SELECT * FROM V$ARCHIVE_GAP;
    no rows selected.

En anden metode til bekræftelse kunne være Sådan flytter du til standby databasen og derefter teste, hvis det er muligt at failback til den primære database. For at aktivere standby databasen som en primær database skal du bruge følgende udtalelser:

    SQL> ALTER DATABASE RECOVER MANAGED STANDBY DATABASE FINISH;
    SQL> ALTER DATABASE ACTIVATE STANDBY DATABASE;

Hvis du ikke har aktiveret flashback på den oprindelige primære database, kan det anbefales, at du slippe den oprindelige primære database og genskabe som en standby database.

Vi anbefaler, at du aktiverer flashback database på primært og standby databaserne. Når der sker en failover, kan den primære database blinkede tilbage til tid, før sekundære og hurtigt konverteres til en standby database.

##<a name="additional-resources"></a>Yderligere ressourcer
[Oracle virtuelt billeder til Azure](virtual-machines-windows-classic-oracle-images.md)
