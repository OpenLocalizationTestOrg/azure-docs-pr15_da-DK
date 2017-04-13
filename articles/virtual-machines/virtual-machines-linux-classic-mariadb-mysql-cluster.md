<properties
    pageTitle="Køre en MariaDB (MySQL) klynge på Azure"
    description="Oprette en MariaDB + Galera MySQL klynge på virtuelle Azure-computere"
    services="virtual-machines-linux"
    documentationCenter=""
    authors="sabbour"
    manager="timlt"
    editor=""
    tags="azure-service-management"/>

<tags
    ms.service="virtual-machines-linux"
    ms.devlang="multiple"
    ms.topic="article"
    ms.tgt_pltfrm="vm-linux"
    ms.workload="infrastructure-services"
    ms.date="04/15/2015"
    ms.author="v-ahsab"/>

# <a name="mariadb-mysql-cluster---azure-tutorial"></a>MariaDB (MySQL) klynge - Azure selvstudium

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]

> [AZURE.NOTE]  MariaDB Enterprise klynge er nu tilgængelig i Azure Marketplace.  Den nye tilbud implementerer automatisk en MariaDB Galera klynge på ARM. Du skal bruge den nye tilbud fra https://azure.microsoft.com/en-us/marketplace/partners/mariadb/cluster-maxscale/ 

Vi opretter en flere Master [Galera](http://galeracluster.com/products/) klynge af [MariaDBs](https://mariadb.org/en/about/), en robust, SVG og pålideligt drop-in erstatning for MySQL til at arbejde i et miljø med høj tilgængelighed på virtuelle Azure-computere.

## <a name="architecture-overview"></a>Oversigt over arkitektur

Dette emne udfører følgende trin:

1. Oprette en 3-node klynge
2. Adskille diskene Data fra cd'en OS
3. Oprette den Data i RAID-0/spredt indstilling for at øge IOP'ER
4. Bruge Azure belastning til saldo indlæsning af noderne 3
5. Oprette et VM billede, der indeholder MariaDB + Galera for at minimere gentagne arbejde, og bruge det til at oprette andre klynge FOS.

![Arkitektur](./media/virtual-machines-linux-classic-mariadb-mysql-cluster/Setup.png)

> [AZURE.NOTE]  I dette emne bruges [Azure CLI](../xplat-cli-install.md) værktøjer, så sørg for at hente dem og forbinde dem til dit Azure abonnement ifølge instruktionerne. Hvis du har brug for en reference til de tilgængelige kommandoer i Azure CLI, se dette link til [Azure CLI kommandoreference](../virtual-machines-command-line-tools.md). Du kan også har brug for at [oprette en SSH nøgle til godkendelse] og noter **.pem filplacering**.


## <a name="creating-the-template"></a>Oprette skabelonen

### <a name="infrastructure"></a>Infrastruktur

1. Oprette en forbindelse gruppe for at holde ressourcerne, der sammen

        azure account affinity-group create mariadbcluster --location "North Europe" --label "MariaDB Cluster"

2. Oprette et virtuelt netværk

        azure network vnet create --address-space 10.0.0.0 --cidr 8 --subnet-name mariadb --subnet-start-ip 10.0.0.0 --subnet-cidr 24 --affinity-group mariadbcluster mariadbvnet

3. Oprette en lagerplads til at hoste alle vores diske. Bemærk, at du ikke bør placere mere end 40 stærkt bruges diske på den samme konto lagerplads til at undgå at nå 20.000 IOP'ER konto lagergrænsen. I dette tilfælde er vi væk fra fra dette nummer, så vi kan Gem alt på den samme konto for enkel

        azure storage account create mariadbstorage --label mariadbstorage --affinity-group mariadbcluster

3. Finde navnet på CentOS 7 virtuelt billedet

        azure vm image list | findstr CentOS
Dette vil output noget i retning af `5112500ae3b842c8b9c604889f8753c3__OpenLogic-CentOS-70-20140926`. Brug navnet på i det følgende trin.

4. Oprette skabelonen VM erstatte **/path/to/key.pem** med den sti, hvor du har gemt den oprettede .pem SSH nøgle

        azure vm create --virtual-network-name mariadbvnet --subnet-names mariadb --blob-url "http://mariadbstorage.blob.core.windows.net/vhds/mariadbhatemplate-os.vhd"  --vm-size Medium --ssh 22 --ssh-cert "/path/to/key.pem" --no-ssh-password mariadbtemplate 5112500ae3b842c8b9c604889f8753c3__OpenLogic-CentOS-70-20140926 azureuser

5. Vedhæfte 4 x 500GB datadisce til VM til brug i RAID-konfiguration

        FOR /L %d IN (1,1,4) DO azure vm disk attach-new mariadbhatemplate 512 http://mariadbstorage.blob.core.windows.net/vhds/mariadbhatemplate-data-%d.vhd

6. SSH til skabelonen VM, som du har oprettet på **mariadbhatemplate.cloudapp.net:22** og oprette forbindelse ved hjælp af din privat nøgle.

### <a name="software"></a>Software

1. Få rod

        sudo su

2. Installere RAID support:

     - Installere mdadm

                yum install mdadm

     - Oprette RAID 0/stribe konfigurationen med et EXT4 filsystem

                mdadm --create --verbose /dev/md0 --level=stripe --raid-devices=4 /dev/sdc /dev/sdd /dev/sde /dev/sdf
                mdadm --detail --scan >> /etc/mdadm.conf
                mkfs -t ext4 /dev/md0

     - Oprette mappen tilslutningen punkt

                mkdir /mnt/data

     - Hente består af den nyoprettede RAID-enhed

                blkid | grep /dev/md0

     - Redigere /etc/fstab

                vi /etc/fstab

     - Tilføje enheden i for at aktivere automatisk mouting på Genstart erstatte UUID med den værdi, som fås fra kommandoen **blkid** før

                UUID=<UUID FROM PREVIOUS>   /mnt/data ext4   defaults,noatime   1 2

     - Oprette forbindelse til den nye partition

                mount /mnt/data

3. Installere MariaDB:

     - Oprette MariaDB.repo-filen:

                vi /etc/yum.repos.d/MariaDB.repo

     - Udfylde den med den under indhold

                [mariadb]
                name = MariaDB
                baseurl = http://yum.mariadb.org/10.0/centos7-amd64
                gpgkey=https://yum.mariadb.org/RPM-GPG-KEY-MariaDB
                gpgcheck=1

     - Fjerne eksisterende omvendt polsk og mariadb-libs for at undgå konflikter

            yum remove postfix mariadb-libs-*

     - Installere MariaDB med Galera

            yum install MariaDB-Galera-server MariaDB-client galera

4. Flytte mappen MySQL data til RAID Bloker enhed

     - Kopiér den aktuelle MySQL-mappe i den nye placering, og fjern den gamle mappe

            cp -avr /var/lib/mysql /mnt/data  
            rm -rf /var/lib/mysql

     - Angive tilladelser på ny mappe i overensstemmelse hermed

            chown -R mysql:mysql /mnt/data && chmod -R 755 /mnt/data/  

     - Oprette en symlink, der peger den gamle mappe til den nye placering på RAID partitionen

            ln -s /mnt/data/mysql /var/lib/mysql

5. Fordi [SELinux forstyrrer handlingerne klynge](http://galeracluster.com/documentation-webpages/configuration.html#selinux), det er nødvendigt at deaktivere den til den aktuelle session (indtil der vises en kompatibel version). Redigere `/etc/selinux/config` at deaktivere for efterfølgende genstarter:

            setenforce 0

       then editing `/etc/selinux/config` to set `SELINUX=permissive`

6. Validere MySQL kører

    - Start MySQL

            service mysql start

    - Secure MySQL-installation, angive rod adgangskoden, fjerne anonyme brugere, deaktivering remote rod logon og fjerne testdatabase

            mysql_secure_installation

    - Oprette en bruger på databasen til klyngehandlinger og eventuelt dine programmer

            mysql -u root -p
            GRANT ALL PRIVILEGES ON *.* TO 'cluster'@'%' IDENTIFIED BY 'p@ssw0rd' WITH GRANT OPTION; FLUSH PRIVILEGES;
            exit

   - Stoppe MySQL

            service mysql stop

7. Oprette konfiguration pladsholder

    - Rediger MySQL-konfigurationen for at oprette en pladsholder for klynge indstillingerne. Udskift ikke den **`<Vairables>`** eller fjern kommentar fra nu. Der sker, når vi oprette en VM fra denne skabelon.

            vi /etc/my.cnf.d/server.cnf

    - Redigere sektionen **[galera]** og fjerne markeringen

    - Redigere sektionen **[mariadb]**

            wsrep_provider=/usr/lib64/galera/libgalera_smm.so
            binlog_format=ROW
            wsrep_sst_method=rsync
            bind-address=0.0.0.0 # When set to 0.0.0.0, the server listens to remote connections
            default_storage_engine=InnoDB
            innodb_autoinc_lock_mode=2

            wsrep_sst_auth=cluster:p@ssw0rd # CHANGE: Username and password you created for the SST cluster MySQL user
            #wsrep_cluster_name='mariadbcluster' # CHANGE: Uncomment and set your desired cluster name
            #wsrep_cluster_address="gcomm://mariadb1,mariadb2,mariadb3" # CHANGE: Uncomment and Add all your servers
            #wsrep_node_address='<ServerIP>' # CHANGE: Uncomment and set IP address of this server
            #wsrep_node_name='<NodeName>' # CHANGE: Uncomment and set the node name of this server

8. Åbn nødvendige porte i firewallen for (ved hjælp af FirewallD på CentOS 7)

    - MySQL:`firewall-cmd --zone=public --add-port=3306/tcp --permanent`
    - GALERA:`firewall-cmd --zone=public --add-port=4567/tcp --permanent`
    - GALERA IST:`firewall-cmd --zone=public --add-port=4568/tcp --permanent`
    - RSYNC:`firewall-cmd --zone=public --add-port=4444/tcp --permanent`
    - Genindlæs firewallen:`firewall-cmd --reload`

9.  Hvis du Optimer systemet for ydeevnen. Referere til denne artikel om [strategi for justering af ydeevnen](virtual-machines-linux-classic-optimize-mysql.md) for flere oplysninger

    - Redigere konfigurationsfil MySQL igen

            vi /etc/my.cnf.d/server.cnf

    - Redigere sektionen **[mariadb]** og tilføje den nedenfor

    > [AZURE.NOTE] Det anbefales, at **innodb\_bufferen\_pool_size** være 70% af din VM hukommelse. Det er angivet på 2,45 GB her til mediet Azure VM med 3,5 GB RAM.

            innodb_buffer_pool_size = 2508M # The buffer pool contains buffered data and the index. This is usually set to 70% of physical memory.
            innodb_log_file_size = 512M #  Redo logs ensure that write operations are fast, reliable, and recoverable after a crash
            max_connections = 5000 # A larger value will give the server more time to recycle idled connections
            innodb_file_per_table = 1 # Speed up the table space transmission and optimize the debris management performance
            innodb_log_buffer_size = 128M # The log buffer allows transactions to run without having to flush the log to disk before the transactions commit
            innodb_flush_log_at_trx_commit = 2 # The setting of 2 enables the most data integrity and is suitable for Master in MySQL cluster
            query_cache_size = 0

10. Stoppe MySQL, deaktivere MySQL-tjenesten i at køre på Start for at undgå at ændre op på klynge, når du tilføjer en ny node og deprovision maskinen.

        service mysql stop
        chkconfig mysql off
        waagent -deprovision

11. Registrere VM via portalen. (I øjeblikket [problem #1268 i Azure CLI] værktøjer beskrives fakultet, billeder, der hentes ved værktøjerne Azure CLI ikke registrere de vedhæftede datadisce.)

    - Lukning maskinen via portalen
    - Klik på registrering og angive navnet billede som **mariadb-galera-billede** og angive en beskrivelse og kontrollere "Jeg har kørt waagent".
    ![Registrere den virtuelle maskine](./media/virtual-machines-linux-classic-mariadb-mysql-cluster/Capture.png)
    ![registrere den virtuelle maskine](./media/virtual-machines-linux-classic-mariadb-mysql-cluster/Capture2.PNG)

## <a name="creating-the-cluster"></a>Oprette klyngen

Oprette 3 FOS af den skabelon, du lige har oprettet og derefter konfigurere og starte klyngen.

1. Oprette den første CentOS 7 VM fra det **mariadb-galera-image** billede, du har oprettet, forudsat at virtuelt netværk navn **mariadbvnet** og undernet **mariadb**machine størrelse **Medium**, der passerer i Skytjenesten navn skal være **mariadbha** (eller andet navn, du vil få adgang til via mariadbha.cloudapp.net), at navnet på denne computer for at være **mariadb1** og brugernavn skal være **azureuser**og aktivere SSH adgang og sende SSH certifikat .pem fil og erstatte **/path/to/key.pem** med stien sted, hvor du gemt tasten genererede .pem SSH.

    > [AZURE.NOTE] Kommandoerne nedenfor er opdelt over flere linjer i klarhed, men du skal angive hver som én linje.

        azure vm create
        --virtual-network-name mariadbvnet
        --subnet-names mariadb
        --availability-set clusteravset
        --vm-size Medium
        --ssh-cert "/path/to/key.pem"
        --no-ssh-password
        --ssh 22
        --vm-name mariadb1
        mariadbha mariadb-galera-image azureuser

2. Oprette 2 flere virtuelle maskiner ved _at forbinde_ dem til den aktuelt oprettede **mariadbha** skybaseret tjeneste, ændre **VM navn** samt **SSH port** til en entydig port ikke konflikt med andre FOS i den samme skybaseret tjeneste.

        azure vm create
        --virtual-network-name mariadbvnet
        --subnet-names mariadb
        --availability-set clusteravset
        --vm-size Medium
        --ssh-cert "/path/to/key.pem"
        --no-ssh-password
        --ssh 23
        --vm-name mariadb2
        --connect mariadbha mariadb-galera-image azureuser
og for MariaDB3

        azure vm create
        --virtual-network-name mariadbvnet
        --subnet-names mariadb
        --availability-set clusteravset
        --vm-size Medium
        --ssh-cert "/path/to/key.pem"
        --no-ssh-password
        --ssh 24
        --vm-name mariadb3
        --connect mariadbha mariadb-galera-image azureuser

3. Skal du hente den interne IP-adresse for hver af de 3 FOS til næste trin:

    ![Få IP-adresse](./media/virtual-machines-linux-classic-mariadb-mysql-cluster/IP.png)

4. SSH til 3 FOS og og redigere konfigurationsfil på hver

        sudo vi /etc/my.cnf.d/server.cnf

    uncommenting **`wsrep_cluster_name`** og **`wsrep_cluster_address`** ved at fjerne den **#** i begyndelsen og validering de er faktisk som ønsket.
    Desuden kan erstatte **`<ServerIP>`** i **`wsrep_node_address`** og **`<NodeName>`** i **`wsrep_node_name`** med den VM IP-adresse og navngiv henholdsvis og fjern kommentar fra disse linjer samt.

5. Start klyngen på MariaDB1 og lad den køre ved start

        sudo service mysql bootstrap
        chkconfig mysql on

6. Start MySQL på MariaDB2 og MariaDB3 og lad den køre ved start

        sudo service mysql start
        chkconfig mysql on

## <a name="load-balancing-the-cluster"></a>Klyngen justering af belastning
Da du oprettede grupperet FOS, kan du føjet dem til en tilgængelighed angive kaldet **clusteravset** for at sikre, at de er placeret på forskellige fejl og opdatere domæner og, Azure aldrig betyder vedligeholdelse på alle computere på én gang. Denne konfiguration opfylder kravene til understøttes af den Azure serviceaftale (SLA).

Nu kan du bruge den Azure justering af belastning til saldo anmodninger mellem vores 3 noder.

Køre den under kommandoer på computeren ved hjælp af Azure CLI.
Kommandoen parameterstrukturen er:`azure vm endpoint create-multiple <MachineName> <PublicPort>:<VMPort>:<Protocol>:<EnableDirectServerReturn>:<Load Balanced Set Name>:<ProbeProtocol>:<ProbePort>`

    azure vm endpoint create-multiple mariadb1 3306:3306:tcp:false:MySQL:tcp:3306
    azure vm endpoint create-multiple mariadb2 3306:3306:tcp:false:MySQL:tcp:3306
    azure vm endpoint create-multiple mariadb3 3306:3306:tcp:false:MySQL:tcp:3306

Til sidst, da CLI indstiller belastningsjustering efterprøvning af af intervallet til 15 sekunder (der kan være lidt for lang tid), ændre det i portalen under **slutpunkter** på grund af FOS

![Redigere slutpunkt](./media/virtual-machines-linux-classic-mariadb-mysql-cluster/Endpoint.PNG)

Klik derefter på på Reconfigure The Load-Balanced angive og gå til næste

![Omkonfigurere indlæse ikke-opgjorte sæt](./media/virtual-machines-linux-classic-mariadb-mysql-cluster/Endpoint2.PNG)

ændre intervallet sende forespørgsler til 5 sekunder og Gem derefter

![Skift efterprøvning af af Interval](./media/virtual-machines-linux-classic-mariadb-mysql-cluster/Endpoint3.PNG)

## <a name="validating-the-cluster"></a>Validering af klyngen

Det hårde arbejde er udført. Klyngen skal være nu tilgængeligt på `mariadbha.cloudapp.net:3306` , vil Tryk på justering af belastning og distribuere anmodninger mellem 3 FOS korrekt og effektivt.

Brug din foretrukne MySQL-klient til at oprette forbindelse eller lige forbindelse fra et af FOS at bekræfte denne klynge virker.

     mysql -u cluster -h mariadbha.cloudapp.net -p

Opret en ny database og udfylde den med nogle data derefter

    CREATE DATABASE TestDB;
    USE TestDB;
    CREATE TABLE TestTable (id INT NOT NULL AUTO_INCREMENT PRIMARY KEY, value VARCHAR(255));
    INSERT INTO TestTable (value)  VALUES ('Value1');
    INSERT INTO TestTable (value)  VALUES ('Value2');
    SELECT * FROM TestTable;

Resulterer i tabellen nedenfor

    +----+--------+
  	| id | value  |
    +----+--------+
  	|  1 | Value1 |
  	|  4 | Value2 |
    +----+--------+
    2 rows in set (0.00 sec)

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## <a name="next-steps"></a>Næste trin

I denne artikel, du har oprettet en 3 node MariaDB + Galera meget tilgængelige klynge på Azure virtuelle maskiner, der kører CentOS 7. FOS er Indlæs afstemmes med Azure belastning.

Du vil se nærmere på [en anden måde at klynge MySQL på Linux](virtual-machines-linux-classic-mysql-cluster.md) og måder at [optimere og teste MySQL ydeevne på Azure Linux FOS](virtual-machines-linux-classic-optimize-mysql.md).

<!--Anchors-->
[Architecture overview]: #architecture-overview
[Creating the template]: #creating-the-template
[Creating the cluster]: #creating-the-cluster
[Load balancing the cluster]: #load-balancing-the-cluster
[Validating the cluster]: #validating-the-cluster
[Next steps]: #next-steps

<!--Image references-->

<!--Link references-->
[Galera]: http://galeracluster.com/products/
[MariaDBs]: https://mariadb.org/en/about/
[oprette en SSH nøgle til godkendelse]:http://www.jeff.wilcox.name/2013/06/secure-linux-vms-with-ssh-certificates/
[problem #1268 i Azure CLI]:https://github.com/Azure/azure-xplat-cli/issues/1268
