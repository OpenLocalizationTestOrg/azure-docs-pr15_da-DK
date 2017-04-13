<properties
    pageTitle="Konfiguration af Oracle GoldenGate i FOS | Microsoft Azure"
    description="Gennemgå en selvstudium til at konfigurere og implementere Oracle GoldenGate på Azure Windows Server FOS for høj tilgængelighed og genoprettelse efter nedbrud."
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


#<a name="configuring-oracle-goldengate-for-azure"></a>Konfiguration af Oracle GoldenGate til Azure


Dette selvstudium viser, hvordan man installerer Oracle GoldenGate til virtuelle Azure-computere miljø til høj tilgængelighed og nedbrud. Selvstudiet fokuserer på [tovejs - gentagelse](http://docs.oracle.com/goldengate/1212/gg-winux/GWUAD/wu_about_gg.htm) for RAC Oracle-databaser og kræver, at begge websteder er aktiv.

Oracle GoldenGate understøtter datafordeling og dataintegration af. Det gør det muligt at konfigurere en datafordeling og data synkronisering løsning ved hjælp af Oracle-Oracle gentagelse konfigurationen og indeholder en løsning med fleksible høj tilgængelighed. Oracle GoldenGate supplerer Oracle Data vagt med gentagelse funktioner kan aktivere hele virksomheden oplysninger fordeling og nul nedetid opgraderinger og overførsler. Du kan finde detaljerede oplysninger [Ved hjælp af Oracle GoldenGate med Oracle Data vagt](http://docs.oracle.com/cd/E11882_01/server.112/e17157/unplanned.htm).

Oracle GoldenGate indeholder følgende primære komponenter: udtrække Data pumpe Replicat, spor eller udtrække filer, kontrolpunkter, Manager og indsamler. Hvis du vil have tovejs-replikering mellem to steder, skal du oprette og starte alle komponenter på begge websteder. Du kan finde detaljerede oplysninger om Oracle GoldenGate arkitektur [Oracle GoldenGate vejledning](http://docs.oracle.com/goldengate/1212/gg-winux/index.html).

Dette selvstudium antages det, at du allerede har teoretisk og praktisk viden om Oracle-Database høj tilgængelighed og nedbrud begreber samt [Oracle GoldenGate](http://docs.oracle.com/goldengate/1212/gg-winux/index.html). Du kan finde yderligere oplysninger finder [Oracle-websted](http://www.oracle.com/technetwork/database/features/availability/index.html).

Derudover antages selvstudiet, at du allerede har implementeret følgende forudsætninger:

- Du har allerede gennemset sektionen høj tilgængelighed og overvejelser i forbindelse med genoprettelse efter genoprettelse i emnet [Oracle virtuelt billeder - diverse overvejelser i forbindelse med](virtual-machines-windows-classic-oracle-considerations.md) . Bemærk, at Azure understøtter enkeltstående Oracle-Database forekomster, men ikke Oracle Real Application Clusters (Oracle RAC) i øjeblikket.

- Du har downloadet Oracle GoldenGate softwaren fra webstedet [Oracle-overførsler](http://www.oracle.com/us/downloads/index.html) . Du har markeret produkt Pack Oracle Fusion program – Data Integration. Du har derefter valgt Oracle GoldenGate på Oracle v11.2.1 Media Pack til Microsoft Windows x64 (64-bit) til en Oracle 11 g-database. Derefter skal du hente Oracle GoldenGate V11.2.1.0.3 til Oracle 11g 64-bit på Windows 2008 (64-bit).

- Du har oprettet to virtuelle maskiner (VM'er) i Azure ved hjælp af Oracle Enterprise Edition på Windows Server. Sørg for, at de virtuelle maskiner er i [samme skytjeneste](virtual-machines-linux-load-balance.md) og i det samme [Virtuelt netværk](https://azure.microsoft.com/documentation/services/virtual-network/) for at sikre, at de kan få adgang til hinanden via fast privat IP-adresse.

- Du har angivet virtuelt navnene som "MachineGG1" for websted A og "MachineGG2" til websted B på portalen Azure klassisk.

- Du har oprettet test databaser "TestGG1" på webstedet A og "TestGG2" på webstedet B.

- Du logger på din Windows-server som et medlem af administratorgruppen eller medlem af gruppen **ORA_DBA** .

I dette selvstudium skal du:

1. Konfigurer database på et og websted B  

    1. Udføre indledende indlæsning af data

2. Forberede et og websted B databasereplikering

3. Oprettelse af alle objekter, der er nødvendige for at understøtte DDL gentagelse

4. Konfigurere GoldenGate Manager på webstedet A og B-websted

5. Opret udtrække gruppe og Data Skru processer på webstedet A og B websted

    1. Oprette udtrække og Data Skru processer på et

    2. Oprette en GoldenGate kontrolpunkt tabel på websted B

    3. Tilføje REPLICAT på websted B

    4. Oprette udtrække og Data Skru processer på webstedet B

    5. Oprette en GoldenGate kontrolpunkt tabel i et

    6. Tilføje REPLICAT på et

    7. Tilføje trandata på webstedet A og B websted

    8. Start udtrække og Data Skru processer på et

    9. Start udtrække og Data Skru processer på webstedet B

    10. Start REPLICAT processen på et

    11. Start REPLICAT proces på webstedet B

6. Bekræfte processen tovejs-gentagelse

>[AZURE.IMPORTANT] Dette selvstudium har konfigureret og testet mod følgende softwarekonfiguration:
>
>|                        | **En Database for websted**              | **Webstedets B Database**              |
>|------------------------|----------------------------------|----------------------------------|
>| **Oracle version**     | Oracle11g Release 2 – (11.2.0.1) | Oracle11g Release 2 – (11.2.0.1) |
>| **Computernavn**       | MachineGG1                       | MachineGG2                       |
>| **Operativsystem**   | Windows 2008 R2                  | Windows 2008 R2                  |
>| **Oracle SID**         | TESTGG1                          | TESTGG2                          |
>| **Replikering skema** | SØREN                            | SØREN                            |

For efterfølgende versioner af Oracle-Database og Oracle GoldenGate, kan der være nogle yderligere ændringer, du skal bruge til at implementere. Du kan finde de mest opdaterede version specifikke oplysninger, [Oracle GoldenGate](http://docs.oracle.com/goldengate/1212/gg-winux/index.html) og [Oracle-Database](http://www.oracle.com/us/corporate/features/database-12c/index.html) dokumentation på Oracle-websted. For eksempel for en overgang 11.2.0.4 kildedatabasen og nyere hentning af DDL er udført af logmining serveren asynkront og kræver ingen speciel udløsere, tabeller eller andre databaseobjekter installeres. Oracle GoldenGate opgraderinger kan udføres uden at stoppe brugerprogrammer. Brug af en DDL udløser og understøttende objekter er påkrævet, når Udpak er i integreret tilstand med en Oracle-database for 11g kilde, der er ældre end version 11.2.0.4. Detaljeret vejledning i [installation og konfiguration af Oracle GoldenGate til Oracle-Database](http://docs.oracle.com/goldengate/1212/gg-winux/GIORA.pdf).

##<a name="1-setup-database-on-site-a-and-site-b"></a>1. Konfigurer database på et og websted B
Dette afsnit beskriver, hvordan du udfører database forudsætningerne på både websted A og B. websted Du skal udføre alle trinnene i dette afsnit på begge websteder: et og websted B.

Første, remote skrivebord til websted A og B websted via Azure klassisk-portalen. Åbne en Windows-kommandoprompt, og Opret en privat mappe til Oracle GoldenGate installationsfiler:

    mkdir C:\OracleGG

Derefter udpakke og installere softwaren Oracle GoldenGate i denne mappe. Når dette trin kan du starte GoldenGate Software kommandoen fortolker (GGSCI) ved at udføre følgende kommando:

    C:\OracleGG\.\ggsci

Du kan bruge [GGSCI](http://docs.oracle.com/goldengate/1212/gg-winux/GWUAD/wu_gettingstarted.htm) til at køre flere kommandoer, som konfigurerer, kontrollere og overvåge Oracle GoldenGate.

Derefter skal du køre følgende kommando for at oprette alle underordnede mapper fra installationspakken:

    GGSCI (Hostname) 1> CREATE SUBDIRS

Kør følgende kommando for at afslutte GGSCI kommandoprompten:

    GGSCI (Hostname) 1> EXIT

Derefter skal du oprette en databasebruger, der skal bruges ved processer Oracle GoldenGate overordnet, udtrække og gentagelse. Bemærk, at du kan oprette individuelle brugere for hver proces eller konfigurere kun én almindelige bruger. I dette selvstudium oprette vi en bruger, der kaldes som ggate. Derefter give vi denne bruger de nødvendige tilladelser. Bemærk, at du skal udføre følgende handlinger på webstedet A og B. websted

Åbn SQL\*Plus kommandovindue på webstedet A og B websted med database administratorrettigheder med **SYSDBA**, f.eks.:

    Enter username: / as sysdba

Og kør:

    SQL> create tablespace ggs_data   datafile 'c:\OracleDatabase\oradata\<DBNAME>\<DBNAME>ggs_data01.dbf' size 200m;
    SQL> create user ggate identified by ggate default tablespace ggs_data  temporary tablespace temp;
          grant connect, resource to ggate;
          grant select any dictionary, select any table to ggate;
          grant create table to ggate;
          grant flashback any table to ggate;
          grant execute on dbms_flashback to ggate;
          grant execute on utl_file to ggate;
          grant create any table to ggate;
          grant insert any table to ggate;
          grant update any table to ggate;
          grant delete any table to ggate;
          grant drop any table to ggate;

Derefter skal du finde Initialisering\<DatabaseSID\>. ORA filen i feltet % ORACLE\_HOME %\\Webdatabase mappe på webstedet A og B websted og føje følgende database parametre til INITTEST.ora:

    UNDO\_MANAGEMENT=AUTO
    UNDO\_RETENTION=86400

En komplet liste over alle Oracle GoldenGate GGSCI kommandoer, skal du se [Reference til Oracle GoldenGate til Windows](http://docs.oracle.com/goldengate/1212/gg-winux/GWURF/ggsci_commands.htm).

### <a name="perform-initial-data-load"></a>Udføre indledende indlæsning af data

Du kan udføre den indledende indlæsning af data i databasen ved at følge flere forskellige metoder. For eksempel kan du bruge [Oracle GoldenGate direkte indlæsning](http://docs.oracle.com/goldengate/1212/gg-winux/GWUAD/wu_initsync.htm) eller normalt eksport og Import værktøjer til eksporterer tabeldata fra A til websted B.

Hvis du vil vise Oracle GoldenGate gentagelse processen, demonstrerer dette selvstudium oprettes en tabel i både websted A og B-websted ved hjælp af følgende kommandoer.

Først skal du åbne op SQL\*Plus kommando vindue og køre følgende kommando for at oprette en tabel med lager på webstedet A og B websted databaser:

    create table scott.inventory
    (prod_id number,
    prod_category varchar2(20),
    qty_in_stock number,
    last_dml timestamp default systimestamp);

Dernæst skal du tilføje en betingelse til tabellen nyoprettede på webstedet A og B websted databaser:

    alter table scott.inventory add constraint pk_inventory primary key (prod_id) ;

Derefter give alle rettigheder til den nye tabel lager til bruger ggate på et og websted B:

    grant all on scott.inventory to ggate;

Dernæst skal oprette og aktivere en database udløser, INVENTORY_CDR_TRG, i nyoprettede tabellen for at sikre dig, at alle transaktioner til den nye tabel er registreret, hvis brugeren ikke er ggate. Udføre denne handling på et og websted B.

    CREATE OR REPLACE TRIGGER INVENTORY_CDR_TRG
    BEFORE UPDATE
    ON SCOTT.INVENTORY
    REFERENCING NEW AS New OLD AS Old
    FOR EACH ROW
    BEGIN
    IF SYS_CONTEXT ('USERENV', 'SESSION_USER') != 'GGATE'
    THEN
    :NEW.LAST_DML := SYSTIMESTAMP;
    END IF;
    END;
    /


##<a name="2-prepare-site-a-and-site-b-for-database-replication"></a>2. forberede et og websted B databasereplikering
Dette afsnit forklarer, hvordan du kan forberede et og websted B til databasereplikering. Du skal udføre alle trinnene i dette afsnit på begge websteder: et og websted B.

Første, remote skrivebord til websted A og B websted via Azure klassisk-portalen. Skifte databasen til archivelog tilstand ved hjælp af SQL * Plus kommandovindue:

    sql>shutdown immediate
    sql>startup mount
    sql>alter database archivelog;
    sql>alter database open;


Aktivere derefter minimale [supplerende logføring](http://docs.oracle.com/cd/E11882_01/server.112/e22490/logminer.htm) på følgende måde:

    SQL> ALTER DATABASE ADD SUPPLEMENTAL LOG DATA (ALL) COLUMNS;

Dernæst skal forberede databasen til at understøtte DDL (data definition language) gentagelse:

    SQL> alter system set recyclebin=off scope=spfile;

Derefter, Luk og genstart computeren databasen:

    sql>shutdown immediate
    sql>startup


##<a name="3-create-all-necessary-objects-to-support-ddl-replication"></a>3. Opret alle objekter, der er nødvendige for at understøtte DDL gentagelse
Dette afsnit beskrives de scripts, du skal bruge til at oprette alle objekter, der er nødvendige for at understøtte DDL gentagelse. Du skal køre scripts, der er angivet i dette afsnit på både websted A og B. websted

Åbn en kommandoprompt i Windows, og gå til mappen Oracle GoldenGate som C:\\OracleGG. Start SQL\*Plus kommandoprompt med administratorrettigheder for databasen, som bruger **SYSDBA** på webstedet A og B. websted

Derefter skal du køre følgende scripts:

    SQL> @marker_setup.sql  
    Enter GoldenGate schema name: ggate
    SQL> @ddl_setup.sql  
    Enter GoldenGate schema name: ggate
    SQL> @role_setup.sql
    Enter GoldenGate schema name: ggate
    SQL> grant ggs_ggsuser_role to ggate;
     Grant succeeded.
     SQL> @ddl_enable
     Trigger altered.
     SQL> @ddl_pin ggate


Oracle GoldenGate værktøj kræver et niveau tabel-logon til understøttelse af DDL (data definition language). Det er grunden, Aktivér supplerende logføring på niveauet for tabel ved hjælp af kommandoen Tilføj TRANDATA. Åbne Oracle GoldenGate fortolker kommandovindue skal logge på databasen, og derefter køre kommandoen Tilføj TRANDATA:

    GGSCI 5> DBLOGIN USERID ggate, PASSWORD ggate

    GGSCI(Hostname) 6> add trandata scott.inventory

##<a name="4-configure-goldengate-manager-on-site-a-and-site-b"></a>4. Konfigurer GoldenGate Manager på webstedet A og B-websted
Oracle GoldenGate Manager udfører en række funktioner som Start på andre GoldenGate processer for log filstyring og -rapportering.

Du skal konfigurere Oracle GoldenGate Manager processen på både websted A og B. websted For at gøre dette skal du udføre følgende trin på webstedet A og B. websted

Åbne vinduer kommando vindue og starte Oracle GoldenGate kommandofortolker:

    cd C:\OracleGG\
    c:\OracleGG>ggsci
    Oracle GoldenGate Command Interpreter for Oracle
    Version 11.2.1.0.3 14400833 OGGCORE_11.2.1.0.3_PLATFORMS_120823.1258
    Windows x64 (optimized), Oracle 11g on Aug 23 2012 16:50:36
    Copyright (C) 1995, 2012, Oracle and/or its affiliates. All rights reserved.


Logfører GGSCI sessionen til en database, så du kan udføre kommandoer, som påvirker databasen:

    GGSCI (HostName) 1> DBLOGIN USERID ggate, PASSWORD ggate
    Successfully logged into database.

Få vist status og en mellemliggende (hvis relevant) for alle overordnet, udtrække og Replicat processer på et system:

    GGSCI (HostName) 2> info all
    Program     Status      Group       Lag           Time Since Chkpt
    MANAGER     STOPPED

Åbn parameterfilen ved hjælp af kommandoen Rediger parametre, og derefter tilføje følgende oplysninger:

    GGSCI (HostName) 3> edit params mgr
    PORT 7809
    USERID ggate, PASSWORD ggate
    PURGEOLDEXTRACTS  C:\OracleGG\dirdat\ex, USECHECKPOINTS

Få vist status og en mellemliggende (hvis relevant) for alle overordnet, udtrække og Replicat processer på et system:

    GGSCI (HostName) 46> info all
    Program     Status      Group       Lag           Time Since Chkpt
    MANAGER     STOPPED

Logfører GGSCI sessionen til en database, så du kan udføre kommandoer, som påvirker databasen:

    GGSCI (HostName) 47> dblogin USERID ggate, PASSWORD ggate

    Successfully logged into database.

Starte processen manager:

    GGSCI (HostName) 48> start manager
    Manager started.

##<a name="5-create-extract-group-and-data-pump-processes-on-site-a-and-site-b"></a>5. Opret Udpak gruppe og Data Skru processer på webstedet A og B websted

###<a name="create-extract-and-data-pump-processes-on-site-a"></a>Oprette udtrække og Data Skru processer på et

Du skal oprette udtrække og Data Skru processer på et og websted B. Remote desktop til websted A og B websted via Azure klassisk-portalen. Åbn GGSCI kommandoen fortolker vindue op. Køre følgende kommandoer på webstedet A:

    GGSCI (MachineGG1) 14> add extract ext1 tranlog begin now
    EXTRACT added.
    GGSCI (MachineGG1) 4> add exttrail C:\OracleGG\dirdat\lt, extract ext1
    EXTTRAIL added.
    GGSCI (MachineGG1) 16> add extract dpump1 exttrailsource C:\OracleGG\dirdat\aa
    EXTRACT added.
    GGSCI (MachineGG1) 17> add rmttrail C:\OracleGG\dirdat\ab extract dpump1
    RMTTRAIL added.

Åbn parameterfilen ved hjælp af kommandoen Rediger parametre og derefter tilføje følgende oplysninger: GGSCI (MachineGG1) 18 > Rediger parametre ext1 UDPAK ext1 bruger-id ggate, adgangskode ggate EXTTRAIL C:\OracleGG\dirdat\aa TRANLOGOPTIONS EXCLUDEUSER ggate tabel scott.inventory, GETBEFORECOLS (til opdatering KEYINCLUDING (prod_category, qty_in_stock, last_dml), på slette KEYINCLUDING (prod_category, qty_in_stock, last_dml))

Åbn parameterfilen ved hjælp af kommandoen Rediger parametre, og derefter tilføje følgende oplysninger:

    GGSCI (MachineGG1) 15> edit params dpump1
    EXTRACT dpump1
     USERID ggate, PASSWORD ggate
     RMTHOST ActiveGG2orcldb, MGRPORT 7809, TCPBUFSIZE 100000
     RMTTRAIL C:\OracleGG\dirdat\ab
     PASSTHRU
     TABLE scott.inventory;

###<a name="create-a-goldengate-checkpoint-table-on-site-b"></a>Oprette en GoldenGate kontrolpunkt tabel på websted B

Derefter skal du tilføje en kontrolpunkt tabel på webstedet B. For at gøre dette, skal du åbne et GoldenGate kommandovindue fortolker og køre:

    C:\OracleGG\ggsci
    GGSCI (MachineGG2) 1> DBLOGIN USERID ggate, PASSWORD ggate
    Successfully logged into database.

Og derefter tilføje kontrolpunktstabellen til den database, hvor ggate er ejeren af:

    GGSCI (MachineGG2) 2> ADD CHECKPOINTTABLE ggate.checkpointtable
    Successfully created checkpoint table ggate.checkpointtable.

Tilføj navnet på tabellen Kontrollér punkt til GLOBALS filen på serveren, som er websted B i dette trin. Rediger filen GLOBALS på webstedet B:

    GGSCI (MachineGG2) 1> EDIT PARAMS ./GLOBALS

Føj derefter parameteren CHECKPOINTTABLE til den eksisterende GLOBALS fil:

    GGSCHEMA ggate
    CHECKPOINTTABLE ggate.checkpointtable

Som det sidste trin, Gem og Luk filen GLOBALS parameter.


###<a name="add-replicat-on-site-b"></a>Tilføje REPLICAT på websted B
I dette afsnit beskrives, hvordan du tilføjer en REPLICAT proces "REP2" på webstedet B.

Kommandoen Tilføj REPLICAT bruges til at oprette en Replicat gruppe på webstedet B:

    GGSCI (MachineGG2) 37> add replicat rep2 exttrail C:\OracleGG\dirdatab, checkpointtable ggate.checkpointtable

Åbn parameterfilen ved hjælp af kommandoen Rediger parametre, og derefter tilføje følgende oplysninger:

    GGSCI (MachineGG2) 10> edit params rep2
    REPLICAT rep2
    ASSUMETARGETDEFS
    USERID ggate, PASSWORD ggate
    DISCARDFILE C:\OracleGG\dirdat\discard.txt, append,megabytes 10
    MAP scott.inventory, TARGET scott.inventory;

###<a name="create-extract-and-data-pump-processes-on-site-b"></a>Oprette udtrække og Data Skru processer på webstedet B

I dette afsnit beskrives, hvordan du opretter en ny Udpak proces "EXT2" og en ny data pumpe proces "DPUMP2" på webstedet B:

    GGSCI (MachineGG2) 3> add extract ext2 tranlog begin now
     EXTRACT added.
    GGSCI (MachineGG2) 4> add exttrail C:\OracleGG\dirdat\ac extract ext2
     EXTTRAIL added.
    GGSCI (MachineGG2) 5> add extract dpump2 exttrailsource C:\OracleGG\dirdat\ac
     EXTRACT added.
    GGSCI (MachineGG2) 6> add rmttrail C:\OracleGG\dirdat\ad extract dpump2
     RMTTRAIL added.

Åbn parameterfilen ved hjælp af kommandoen Rediger parametre, og derefter tilføje følgende oplysninger:

    GGSCI (MachineGG2) 31> edit params ext2
    EXTRACT ext2
    USERID ggate, PASSWORD ggate
    EXTTRAIL C:\OracleGG\dirdat\ac
    TRANLOGOPTIONS EXCLUDEUSER ggate
    TABLE scott.inventory,
    GETBEFORECOLS (
    ON UPDATE KEYINCLUDING (prod_category,qty_in_stock, last_dml),
    ON DELETE KEYINCLUDING (prod_category,qty_in_stock, last_dml));

Åbn parameterfilen ved hjælp af kommandoen Rediger parametre, og derefter tilføje følgende oplysninger:

    GGSCI (MachineGG2) 32> edit params dpump2
    EXTRACT dpump2
    USERID ggate, PASSWORD ggate
    RMTHOST MachineGG1, MGRPORT 7809, TCPBUFSIZE 100000
    RMTTRAIL C:\OracleGG\dirdat\ad
    PASSTHRU
    TABLE scott.inventory;

###<a name="create-a-goldengate-checkpoint-table-on-site-a"></a>Oprette en GoldenGate kontrolpunkt tabel i et

Åbn Oracle GoldenGate kommandoen fortolker vindue op, og Opret en kontrolpunkt tabel:

    GGSCI (MachineGG1) 1> DBLOGIN USERID ggate, PASSWORD ggate
    Successfully logged into database.

    GGSCI (MachineGG1) 2> ADD CHECKPOINTTABLE ggate.checkpointtable

    Successfully created checkpoint table ggate.checkpointtable.

Skal du også tilføje navnet på tabellen Kontrollér punkt til filen GLOBALS på webstedet A.

Åbne Oracle GoldenGate kommandoen fortolker vindue op og redigere filen GLOBALS på webstedet A:

    GGSCI (MachineGG1) 1> EDIT PARAMS ./GLOBALS
    Add the CHECKPOINTTABLE parameter to the existing GLOBALS file as follows:
    GGSCHEMA ggate
    CHECKPOINTTABLE ggate.checkpointtable

Gem og Luk filen GLOBALS parameter.

###<a name="add-replicat-on-site-a"></a>Tilføje REPLICAT på et

I dette afsnit beskrives, hvordan du tilføjer en REPLICAT proces "REP1" på webstedet A.

Følgende kommando opretter en Replicat gruppe rep1 med navnet på et spor, og den tilknyttede checkpointtable.

    GGSCI (MachineGG1) 21> add replicat rep1 exttrail C:\OracleGG\dirdat\ad,checkpointtable ggate.checkpointtable
     REPLICAT added.

Åbn parameterfilen ved hjælp af kommandoen Rediger parametre, og derefter tilføje følgende oplysninger:

    GGSCI (MachineGG1) 10> edit params rep1
    REPLICAT rep1
    ASSUMETARGETDEFS
    USERID ggate, PASSWORD ggate
    DISCARDFILE C:\OracleGG\dirdat\discard.txt, append, megabytes 10
    MAP scott.inventory, TARGET scott.inventory;

### <a name="add-trandata-on-site-a-and-site-b"></a>Tilføje trandata på webstedet A og B websted

Aktivere supplerende logføring på niveauet for tabel ved hjælp af kommandoen Tilføj TRANDATA. Åbn Oracle GoldenGate fortolker kommandovindue skal logge på databasen, og derefter køre kommandoen Tilføj TRANDATA.

Fjernskrivebord til MachineGG1, åbne Oracle GoldenGate kommandofortolker, og kør:

    GGSCI (MachineGG1) 11> dblogin userid ggate password ggate
     Successfully logged into database.
    GGSCI (MachineGG1) 12> add trandata scott.inventory cols (prod_category,qty_in_stock, last_dml)
    GGSCI (MachineGG1) 13> info trandata scott.inventory
    Logging of supplemental redo log data is enabled for table SCOTT.INVENTORY.
    Columns supplementally logged for table SCOTT.INVENTORY: PROD_ID, PROD_CATEGORY, QTY_IN_STOCK, LAST_DML.

Fjernskrivebord til MachineGG2, åbne Oracle GoldenGate kommandofortolker, og kør:

    GGSCI (MachineGG2) 18> dblogin userid ggate password ggate
     Successfully logged into database.
    GGSCI (MachineGG2) 14> add trandata scott.inventory cols (prod_category,qty_in_stock, last_dml)
    Logging of supplemental redo data enabled for table SCOTT.INVENTORY.

Viser oplysninger om tilstanden for tabel-niveau supplerende logføring:

    GGSCI (MachineGG2) 15> info trandata scott.inventory
    Logging of supplemental redo log data is enabled for table SCOTT.INVENTORY.
    Columns supplementally logged for table SCOTT.INVENTORY: PROD_ID, PROD_CATEGORY, QTY_IN_STOCK, LAST_DML.

###<a name="add-trandata-on-site-a-and-site-b"></a>Tilføje trandata på webstedet A og B websted

Aktivere supplerende logføring på niveauet for tabel ved hjælp af kommandoen Tilføj TRANDATA. Åbn Oracle GoldenGate fortolker kommandovindue skal logge på databasen, og derefter køre kommandoen Tilføj TRANDATA.

Fjernskrivebord til MachineGG1, åbne Oracle GoldenGate kommandofortolker, og kør:

    GGSCI (MachineGG1) 11> dblogin userid ggate password ggate
     Successfully logged into database.
    GGSCI (MachineGG1) 12> add trandata scott.inventory cols (prod_category,qty_in_stock, last_dml)
    GGSCI (MachineGG1) 13> info trandata scott.inventory
    Logging of supplemental redo log data is enabled for table SCOTT.INVENTORY.
    Columns supplementally logged for table SCOTT.INVENTORY: PROD_ID, PROD_CATEGORY, QTY_IN_STOCK, LAST_DML.

Fjernskrivebord til MachineGG2, åbne Oracle GoldenGate kommandofortolker, og kør:

    GGSCI (MachineGG2) 18> dblogin userid ggate password ggate
     Successfully logged into database.
    GGSCI (MachineGG2) 14> add trandata scott.inventory cols (prod_category,qty_in_stock, last_dml)
    Logging of supplemental redo data enabled for table SCOTT.INVENTORY.
    Display information about the state of table-level supplemental logging:
    GGSCI (MachineGG2) 15> info trandata scott.inventory
    Logging of supplemental redo log data is enabled for table SCOTT.INVENTORY.
    Columns supplementally logged for table SCOTT.INVENTORY: PROD_ID, PROD_CATEGORY, QTY_IN_STOCK, LAST_DML.

###<a name="start-extract-and-data-pump-processes-on-site-a"></a>Start udtrække og Data Skru processer på et

Start Udpak proces ext1 på webstedet A:

    GGSCI (MachineGG1) 31> start extract ext1
    Sending START request to MANAGER …
    EXTRACT EXT1 starting

Start data pumpe proces dpump1 på webstedet A:

    GGSCI (MachineGG1) 23> start extract dpump1
    Sending START request to MANAGER …
    EXTRACT DPUMP1 starting
Få vist oplysninger om Udpak gruppe ext1: GGSCI (MachineGG1) 32 > oplysninger udtrække ext1 UDTRÆKKE EXT1 sidste gang 2013-11-25 08:03 Status kører kontrolpunkt mellemliggende 00:00:00 (opdaterede 00:00:02 siden) Log læst kontrolpunkt Oracle annullere Fortryd logfiler 2013-11-25 08:03:18 Seqno 6, RBA 3230720 SCN 0.1074371 (1074371)

Få vist status og en mellemliggende (hvis relevant) for alle overordnet, udtrække og Replicat processer på et system:

    GGSCI (MachineGG1) 16> info all
    Program     Status      Group       Lag at Chkpt  Time Since Chkpt

    MANAGER     RUNNING
    EXTRACT     RUNNING     DPUMP1      00:00:00      00:46:33
    EXTRACT     RUNNING     EXT1        00:00:00      00:00:04

###<a name="start-extract-and-data-pump-processes-on-site-b"></a>Start udtrække og Data Skru processer på webstedet B

Start Udpak proces ext2 på webstedet B:

    GGSCI (MachineGG2) 22> start extract ext2
    Sending START request to MANAGER …
    EXTRACT EXT2 starting

Start data pumpe proces dpump2 på webstedet B:

    GGSCI (MachineGG2) 23> start extract dpump2
    Sending START request to MANAGER …
    EXTRACT DPUMP2 starting

Få vist status og en mellemliggende (hvis relevant) for alle overordnet, udtrække og Replicat processer på et system:

    GGSCI (ActiveGG2orcldb) 6> info all
    Program     Status      Group       Lag at Chkpt  Time Since Chkpt

    MANAGER     RUNNING
    EXTRACT     RUNNING     DPUMP2      00:00:00      136:13:33
    EXTRACT     RUNNING     EXT2        00:00:00      00:00:04

### <a name="start-replicat-process-on-site-a"></a>Start REPLICAT processen på et

Dette afsnit beskrives, hvordan du starte processen REPLICAT "REP1" på webstedet A.

Starte processen Replicat på webstedet A:

    GGSCI (MachineGG1) 38> start replicat rep1
    Sending START request to MANAGER …
    REPLICAT REP1 starting

Få vist status for en Replicat gruppe:

    GGSCI (MachineGG1) 39> status replicat rep1
     REPLICAT REP1: RUNNING

###<a name="start-replicat-process-on-site-b"></a>Start REPLICAT proces på webstedet B

Dette afsnit beskrives, hvordan du vil starte processen REPLICAT "REP2" på webstedet B.

Starte processen Replicat på webstedet B:

    GGSCI (MachineGG2) 26> start replicat rep2
    Sending START request to MANAGER …
    REPLICAT REP2 starting

Få vist status for en Replicat gruppe:

    GGSCI (MachineGG2) 27> status replicat rep2
    REPLICAT REP2: RUNNING

##<a name="6-verify-the-bi-directional-replication-process"></a>6. bekræfte processen tovejs-gentagelse

For at bekræfte Oracle GoldenGate konfigurationen, skal du indsætte en række i databasen på webstedet A. Remote Desktop til websted A. Åbn SQL * Plus kommandovindue, og kør: SQL > Vælg navn fra v$ databasen.

    NAME
    ———
    TESTGG

    SQL> insert into inventory values  (100,’TV’,100,sysdate);

    1 row created.

    SQL> commit;

    Commit complete.

Marker derefter Hvis rækken replikeres på webstedet B. Fjernskrivebord til websted B. åben op SQL Plus vinduet for at gøre dette, og kør:

    SQL> select name from v$database;

    NAME
    ———
    TESTGG

    SQL> select * from inventory;

    PROD_ID PROD_CATEGORY QTY_IN_STOCK LAST_DML
    ———- ——————– ———— ———
    100 TV 100 22-MAR-13

Indsætte en ny post på webstedet B:

    SQL> insert into inventory  values  (101,’DVD’,10,sysdate);
    1 row created.

    SQL> commit;

    Commit complete.

Fjernskrivebord et, og Kontrollér, om replikering er sket:

    SQL> select * from inventory;

    PROD_ID PROD_CATEGORY QTY_IN_STOCK LAST_DML
    ———- ——————– ———— ———
    100 TV 100 22-MAR-13
    101 DVD 10 22-MAR-13

##<a name="additional-resources"></a>Yderligere ressourcer
[Oracle virtuelt billeder til Azure](virtual-machines-linux-classic-oracle-images.md)
