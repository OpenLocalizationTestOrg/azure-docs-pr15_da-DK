<properties
    pageTitle="Optimere MySQL ydeevne på Linux FOS | Microsoft Azure"
    description="Lær, hvordan du kan optimere MySQL, der kører på en Azure virtuelt (VM), der kører Linux."
    services="virtual-machines-linux"
    documentationCenter=""
    authors="NingKuang"
    manager="timlt"
    editor=""
    tags="azure-service-management"/>

<tags
    ms.service="virtual-machines-linux"
    ms.workload="infrastructure-services"
    ms.tgt_pltfrm="vm-linux"
    ms.devlang="na"
    ms.topic="article"
    ms.date="12/15/2015"
    ms.author="ningk"/>

#<a name="optimizing-mysql-performance-on-azure-linux-vms"></a>Optimering af ydeevne MySQL på Azure Linux FOS

Der er mange faktorer, der påvirker MySQL ydeevne på Azure, både i virtuelle markering og software hardwarekonfiguration. I denne artikel fokuserer på optimere ydeevne ved hjælp af lagerplads, system og databasekonfigurationer.

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]


##<a name="utilizing-raid-on-an-azure-virtual-machine"></a>Ved at benytte RAID på en Azure virtuel maskine
Lagerplads er den vigtigste faktor, der påvirker databasens ydeevne i skyen miljøer.  Sammenlignet med en enkelt disk, giver RAID hurtigere adgang via på dokumentsammenfald.  Referere til [Standard RAID niveauer](http://en.wikipedia.org/wiki/Standard_RAID_levels) for at få flere detaljer.   

Disk I/O overførselshastighed og i/o-svartid i Azure kan betydeligt forbedret via RAID. Vores øvelse tests viser disk I/O overførselshastighed kan være dobbelt og i/o-svartid kan blive reduceret med halvdelen i gennemsnit når antallet af RAID diske dobbelt (fra 2 til 4, 4 til 8 osv.). Se [tillæg A](#AppendixA) for at få oplysninger.  

Ud over disk I/O forbedrer MySQL ydeevnen, når du øger RAID-niveauet.  Du kan finde oplysninger i [Tillæg B](#AppendixB) .  

Du kan også overveje afsnit størrelse. Generelt når du har en større afsnit, får du lavere spild, især for store skriver. Når afsnit størrelse er for stor, det kan føje ekstra spild dog og ikke kan du drage fordel af RAID. Den aktuelle standardstørrelsen er 512KB, som skal optimal for mest generelle fremstilling miljøer. Du kan finde oplysninger i [Tillæg C](#AppendixC) .   

Vær opmærksom på, at der er begrænsninger på antallet af diske, kan du tilføje for forskellige virtuelt typer. Disse begrænsninger gennemgås i [virtuelt og skyen Service størrelser til Azure](http://msdn.microsoft.com/library/azure/dn197896.aspx). Du skal 4 vedhæftede datadisce følge RAID eksemplet i denne artikel, selvom du kan vælge at konfigurere RAID med færre diske.  

I denne artikel antages det, du allerede har oprettet en Linux virtuel maskine og har MYSQL installeret og konfigureret. Se hvordan du installerer MySQL på Azure yderligere oplysninger om at komme i gang.  

###<a name="setting-up-raid-on-azure"></a>Konfiguration af RAID på Azure
Følgende trin viser, hvordan du opretter RAID på Azure ved hjælp af portalen Azure klassisk. Du kan også konfigurere RAID ved hjælp af Windows PowerShell-scripts.
I dette eksempel skal vi konfigurere RAID 0 med 4 diske.  

####<a name="step-1-add-a-data-disk-to-your-virtual-machine"></a>Trin 1: Føje disken Data til din virtuelle maskine  

Klik på den virtuelle maskine, som du vil føje en datadisk på siden virtuelle maskiner i portalen Azure klassisk. I dette eksempel er den virtuelle maskine mysqlnode1.  

![][1]

Klik på **Dashboard**på siden for den virtuelle maskine.  

![][2]


Klik på **Vedhæft**på proceslinjen.

![][3]

Og derefter klikke på **Vedhæft tom disk**.  

![][4]

Om data disk, skal du vælge **Host Cache indstilling** til **ingen**.  

Dette tilføjer en tom disk i din virtuelle maskine. Gentag dette trin tre gange, så du har 4 datadisce til RAID.  

Du kan se de tilføjede drev i virtual machine ved at kigge på kernen meddelelse logfilen. For eksempel for at se ud på Ubuntu skal bruge følgende kommando:  

    sudo grep SCSI /var/log/dmesg

####<a name="step-2-create-raid-with-the-additional-disks"></a>Trin 2: Opret RAID med de øvrige disketter
Følg denne artikel for at få detaljeret vejledning til konfiguration af RAID:  

[Konfigurere software RAID på Linux](virtual-machines-linux-configure-raid.md)

>[AZURE.NOTE] Hvis du bruger filsystemet XFS, skal du følge nedenstående trin, når du har oprettet RAID.

For at installere XFS på Debian, Ubuntu eller Linux mynte skal du bruge følgende kommando:  

    apt-get -y install xfsprogs  

For at installere XFS på Fedora, CentOS eller RHEL skal du bruge følgende kommando:  

    yum -y install xfsprogs  xfsdump


####<a name="step-3-set-up-a-new-storage-path"></a>Trin 3: Konfigurere en ny lagerplads sti
Brug følgende kommando:  

    root@mysqlnode1:~# mkdir -p /RAID0/mysql

####<a name="step-4-copy-the-original-data-to-the-new-storage-path"></a>Trin 4: Kopiere de oprindelige data til den nye lagerplads sti
Brug følgende kommando:  

    root@mysqlnode1:~# cp -rp /var/lib/mysql/* /RAID0/mysql/

####<a name="step-5-modify-permissions-so-mysql-can-access-read-and-write-the-data-disk"></a>Trin 5: Ændre tilladelser så MySQL kan få adgang til (læse og skrive) data disken
Brug følgende kommando:  

    root@mysqlnode1:~# chown -R mysql.mysql /RAID0/mysql && chmod -R 755 /RAID0/mysql


##<a name="adjust-the-disk-io-scheduling-algorithm"></a>Justere algoritmen disk i/o-planlægning
Linux implementerer fire typer I/O planlægning algoritmer:  

-   Videresendelseskonsument algoritme (ingen handling)
-   Deadline algoritme (Deadline)
-   Helt sædvanlig kø algoritme (CFQ)
-   Budget periode algoritme (Anticipatory)  

Du kan vælge forskellige i/o-schedulers under forskellige scenarier optimering af ydeevne. I et tilfældigt access-miljø er der ikke en stor forskellen mellem CFQ og Deadline algoritmerne for ydeevnen. Det anbefales generelt at angive MySQL-database-miljøet til deadlinedato for stabilitet. Hvis der er mange sekventielle I/O, kan CFQ reducere disk i/o-ydeevne.   

For SSD og andet udstyr, kan ved hjælp af Videresendelseskonsument eller Deadline opnå en bedre ydeevne end standard planlæggeren.   

Fra kernen 2,5 er den standard i/o-planlægning algoritme Deadline. Startende fra kernen 2.6.18, blev CFQ algoritmen standard i/o-planlægning.  Du kan angive denne indstilling på kernen starttidspunktet eller dynamisk at ændre denne indstilling, når systemet kører.  

I følgende eksempel viser, hvordan du kan tjekke og angive standard planlæggeren Videresendelseskonsument algoritmen med.  

For familien Debian fordeling:

###<a name="step-1view-the-current-io-scheduler"></a>Trin 1. Vis aktuelle I/O scheduler
Brug følgende kommando:  

    root@mysqlnode1:~# cat /sys/block/sda/queue/scheduler

Du får vist følgende output, som angiver den aktuelle scheduler.  

    noop [deadline] cfq


###<a name="step-2-change-the-current-device-devsda-of-io-scheduling-algorithm"></a>Trin 2. Ændre den aktuelle enhed (/ Udviklingscenter/sda) på I/O planlægning algoritme
Brug følgende kommandoer:  

    azureuser@mysqlnode1:~$ sudo su -
    root@mysqlnode1:~# echo "noop" >/sys/block/sda/queue/scheduler
    root@mysqlnode1:~# sed -i 's/GRUB_CMDLINE_LINUX=""/GRUB_CMDLINE_LINUX_DEFAULT="quiet splash elevator=noop"/g' /etc/default/grub
    root@mysqlnode1:~# update-grub

>[AZURE.NOTE] Alene værdien indstilles til /dev/sda er ikke nyttig. Det skal angives på alle datadisce, hvor databasen er placeret.  

Du bør se følgende output, der angiver grub.cfg har gendannet og, standard planlæggeren er blevet opdateret til Videresendelseskonsument.  

    Generating grub configuration file ...
    Found linux image: /boot/vmlinuz-3.13.0-34-generic
    Found initrd image: /boot/initrd.img-3.13.0-34-generic
    Found linux image: /boot/vmlinuz-3.13.0-32-generic
    Found initrd image: /boot/initrd.img-3.13.0-32-generic
    Found memtest86+ image: /memtest86+.elf
    Found memtest86+ image: /memtest86+.bin
    done

For Redhat fordeling familien, du skal kun bruge følgende kommando:   

    echo 'echo noop >/sys/block/sda/queue/scheduler' >> /etc/rc.local

##<a name="configure-system-file-operations-settings"></a>Konfigurere handlinger systemindstillinger fil
En bedste fremgangsmåde er at deaktivere funktionen atime logføring i filsystemet. Atime er access sidst fil. Hver gang en fil åbnes, registrerer filsystemet tidsstemplet log. Men disse oplysninger bruges sjældent. Du kan deaktivere den, hvis du ikke behøver det, der begrænser overordnede disk access tid.  

Hvis du vil deaktivere atime logføring, skal du redigere filen system konfiguration fil /etc/ fstab og tilføje indstillingen **noatime** .  

For eksempel redigere vim /etc/fstab filen og tilføje noatime som vist nedenfor.  

    # CLOUD_IMG: This file was created/modified by the Cloud Image build process
    UUID=3cc98c06-d649-432d-81df-6dcd2a584d41       /        ext4   defaults,discard        0 0
    #Add the “noatime” option below to disable atime logging
    UUID="431b1e78-8226-43ec-9460-514a9adf060e"     /RAID0   xfs   defaults,nobootwait, noatime 0 0
    /dev/sdb1       /mnt    auto    defaults,nobootwait,comment=cloudconfig 0       2

Derefter skal du først åbne filsystem med følgende kommando:  

    mount -o remount /RAID0

Test ændret resultatet. Vær opmærksom på, når du ændrer testfilen, access tid ikke er opdateret.  

Før eksempel:     

![][5]

Efter eksempel:

![][6]

##<a name="increase-the-maximum-number-of-system-handles-for-high-concurrency"></a>Øge det maksimale antal system håndtag til høj på dokumentsammenfald
MySQL er høj på dokumentsammenfald database. Standardantallet af samtidige håndtag er 1024 for Linux, som er ikke altid tilstrækkeligt. **Benyt følgende fremgangsmåde for at øge de maksimale samtidige håndtag til at understøtte høj på dokumentsammenfald af MySQL-systemet**.

###<a name="step-1-modify-the-limitsconf-file"></a>Trin 1: Redigere filen limits.conf
Tilføj følgende fire linjer i filen /etc/security/limits.conf at øge de maksimalt tilladte samtidige håndtag. Bemærk, at 65536 er det maksimale antal, der kan understøtte systemet.   

    * bløde nofile 65536
    * hårde nofile 65536
    * bløde nproc 65536
    * hårde nproc 65536

###<a name="step-2-update-the-system-for-the-new-limits"></a>Trin 2: Opdatere systemet for de nye grænser
Kør følgende kommandoer:  

    ulimit -SHn 65536
    ulimit -SHu 65536

###<a name="step-3-ensure-that-the-limits-are-updated-at-boot-time"></a>Trin 3: Sørg for, at begrænsningerne er opdateret på starttidspunktet
Placere følgende Startkommandoer i filen /etc/rc.local, så den kan træde i kraft under hver starttidspunktet.  

    echo “ulimit -SHn 65536” >>/etc/rc.local
    echo “ulimit -SHu 65536” >>/etc/rc.local

##<a name="mysql-database-optimization"></a>Optimering af MySQL-database
Du kan bruge den samme strategi for justering af ydeevnen til at konfigurere MySQL på Azure som på en lokal computer.  

Primære i/o-optimering regler er:   

-   Øge størrelsen på.
-   Reducere i/o-svartid.  

Hvis du vil optimere MySQL serverindstillinger, kan du opdatere filen my.cnf, som er standard konfigurationsfil til både server og klientcomputere.  

Følgende elementer til konfiguration er de vigtigste faktorer, der påvirker MySQL ydeevne:  

-   **innodb_buffer_pool_size**: bufferen puljen indeholder bufferlagrede data og indekset. Dette er som regel angivet til 70% af fysisk hukommelse.
-   **innodb_log_file_size**: Dette er Annuller Fortryd logstørrelsen. Du kan bruge Annuller Fortryd logfiler til at sikre, at skrivehandlinger er hurtig, pålidelig og gendanne efter nedbrud. Dette er angivet til 512MB, som kan give dig masser af plads til at logge skrivning.
-   **max_connections**: nogle gange programmer ikke lukker forbindelser ordentligt. En større værdi giver serveren længere tid at Papirkurv inaktiv forbindelser. Det maksimale antal forbindelser 10000, men er det anbefalede maksimum 5000.
-   **Innodb_file_per_table**: denne indstilling aktiverer eller deaktiverer muligheden af InnoDB for at gemme tabeller i separate filer. Slå indstillingen sikrer, at flere avancerede administration operationer kan anvendes effektivt. Fra ydeevne synspunkt, kan det hurtigere tabel mellemrum transmissions og optimere snavs management ydeevne. Den anbefalede indstilling for dette er tilfældet.</br>
    Standardindstillingen er slået fra MySQL 5,6. Derfor er ingen handling påkrævet. Standardindstillingerne for andre versioner, som er ældre end 5,6, er slået fra. At gøre dette på er påkrævet. Og skal anvende det før data er indlæst, fordi kun nyoprettet tabeller er påvirket.
-   **innodb_flush_log_at_trx_commit**: standardværdien er 1, med det område, der er angivet til 0 ~ 2. Standardværdien er den mest passende mulighed for enkeltstående MySQL DB. Indstilling af 2 gør det muligt for de fleste dataintegriteten og er velegnet til Master i MySQL klynge. Indstillingen på 0 giver mulighed for tab af data, som kan påvirke den pålidelighed, i nogle tilfælde med bedre ydeevne og er velegnet til Slave i MySQL klynge.
-   **Innodb_log_buffer_size**: log bufferen tillader transaktioner til at køre uden at skulle Rens loggen på disken, før transaktionerne Udfør. Men hvis der er stort binære objekt eller tekstfelt, cachen skal blive brugt meget hurtigt og hyppige disk I/O udløses. Det er bedre at øge størrelsen på bufferen, hvis Innodb_log_waits tilstand variabel ikke er 0.
-   **query_cache_size**: er den bedste løsning at deaktivere den fra begyndelsen. Angiv query_cache_size til 0 (dette er nu standardindstillingen i MySQL 5,6) og bruge andre metoder til at øge hastigheden af forespørgsler.  

Se [Tillæg D](#AppendixD) sammenlignes ydeevnen efter optimering.


##<a name="turn-on-the-mysql-slow-query-log-for-analyzing-the-performance-bottleneck"></a>Slå MySQL langsom forespørgsel loggen til at analysere ydeevne flaskehals
MySQL langsom forespørgsel loggen kan hjælpe dig med at identificere langsom forespørgsler til MySQL. Når du har aktiveret MySQL langsom forespørgsel loggen, kan du bruge MySQL værktøjer som **mysqldumpslow** til at identificere flaskehals ydeevne.  

Vær opmærksom på, at som standard dette ikke er aktiveret. Slå langsomme forespørgsel loggen kan bruge nogle CPU-ressourcer. Det anbefales derfor, at du aktiverer dette midlertidigt til fejlfinding flaskehalse for performance.

###<a name="step-1-modify-mycnf-file-by-adding-the-following-lines-to-the-end"></a>Trin 1: Redigere my.cnf fil ved at tilføje følgende linjer til slutningen   

    long_query_time = 2
    slow_query_log = 1
    slow_query_log_file = /RAID0/mysql/mysql-slow.log

###<a name="step-2-restart-mysql-server"></a>Trin 2: Genstart mysql-server
    service  mysql  restart

###<a name="step-3-check-whether-the-setting-is-taking-effect-using-the-show-command"></a>Trin 3: Kontrollér, om indstillingen tager effekt ved hjælp af kommandoen "Vis"

![][7]   

![][8]

I dette eksempel kan du se, at funktionen langsom forespørgsel er blevet aktiveret. Du kan derefter bruge værktøjet **mysqldumpslow** til at bestemme flaskehalse for performance og optimere ydeevnen, som tilføjelse af indeks.





##<a name="appendix"></a>Tillæg

Følgende eksempel ydeevne testdata produceret på målrettede testmiljø, de giver generelle baggrundsoplysninger om ydeevne data tendensen med forskellige ydeevne justering af fremgangsmåder, men resultaterne kan variere under forskellige versioner af miljø eller produkt.

<a name="AppendixA"></a>Tillæg a  
**Disken ydeevne (IOP'ER) med forskellige RAID-niveauer**


![][9]

**Test kommandoer:**  

    fio -filename=/path/test -iodepth=64 -ioengine=libaio -direct=1 -rw=randwrite -bs=4k -size=5G -numjobs=64 -runtime=30 -group_reporting -name=test-randwrite

>AZURE. Bemærk: Arbejdsbelastningen for denne test bruger 64 tråde, forsøger at få den øvre grænse for RAID.

<a name="AppendixB"></a>Tillæg B:  
**MySQL ydeevne (overførsel) sammenligning med forskellige RAID-niveauer**   
(XFS-filsystemet)


![][10]  
![][11]

**Test kommandoer:**

    mysqlslap -p0ps.123 --concurrency=2 --iterations=1 --number-int-cols=10 --number-char-cols=10 -a --auto-generate-sql-guid-primary --number-of-queries=10000 --auto-generate-sql-load-type=write –engine=innodb

**MySQL ydeevne (OLTP) sammenligning med forskellige RAID-niveauer**  
![][12]

**Test kommandoer:**

    time sysbench --test=oltp --db-driver=mysql --mysql-user=root --mysql-password=0ps.123  --mysql-table-engine=innodb --mysql-host=127.0.0.1 --mysql-port=3306 --mysql-socket=/var/run/mysqld/mysqld.sock --mysql-db=test --oltp-table-size=1000000 prepare

<a name="AppendixC"></a>Tillæg c   
**Disken ydeevne (IOP'ER) sammenligning for forskellige afsnit størrelser**  
(XFS-filsystemet)


![][13]

**Test kommandoer:**  

    fio -filename=/path/test -iodepth=64 -ioengine=libaio -direct=1 -rw=randwrite -bs=4k -size=30G -numjobs=64 -runtime=30 -group_reporting -name=test-randwrite
    fio -filename=/path/test -iodepth=64 -ioengine=libaio -direct=1 -rw=randwrite -bs=4k -size=1G -numjobs=64 -runtime=30 -group_reporting -name=test-randwrite  

Bemærk filstørrelsen bruges til denne test er 30GB og 1GB henholdsvis, med RAID 0(4 disks) XFS fie system.


<a name="AppendixD"></a>Tillæg D:  
**MySQL ydeevne (overførsel) sammenligning før og efter optimering**  
(XFS File System)


![][14]

**Test kommandoer:**

    mysqlslap -p0ps.123 --concurrency=2 --iterations=1 --number-int-cols=10 --number-char-cols=10 -a --auto-generate-sql-guid-primary --number-of-queries=10000 --auto-generate-sql-load-type=write –engine=innodb,misam

**Konfigurationsindstillingen til standard og optmization er som følger:**

|Parametre |Standard    |optmization
|-----------|-----------|-----------
|**innodb_buffer_pool_size**    |Ingen   |7G
|**innodb_log_file_size**   |5M |512M
|**max_connections**    |100    |5000
|**innodb_file_per_table**  |0  |1
|**innodb_flush_log_at_trx_commit** |1  |2
|**innodb_log_buffer_size** |8M |128M
|**query_cache_size**   |16 MILLIONER    |0


Mere og mere detaljeret optimering konfiguration af parametre, se kan du de officielle mysql-instruktioner.  

[http://dev.MySQL.com/doc/refman/5.6/en/innodb-Configuration.HTML](http://dev.mysql.com/doc/refman/5.6/en/innodb-configuration.html)  

[http://dev.MySQL.com/doc/refman/5.6/en/innodb-Parameters.HTML#sysvar_innodb_flush_method](http://dev.mysql.com/doc/refman/5.6/en/innodb-parameters.html#sysvar_innodb_flush_method)

**Testmiljø**  

|Hardware   |Detaljer
|-----------|-------
|CPU    |AMD Opteron(tm) Processor 4171 HAN / 4 kerner
|Hukommelse |14G
|disk   |10G/disk
|OS |Ubuntu 14.04.1 LTS



[1]: ./media/virtual-machines-linux-classic-optimize-mysql/virtual-machines-linux-optimize-mysql-perf-01.png
[2]: ./media/virtual-machines-linux-classic-optimize-mysql/virtual-machines-linux-optimize-mysql-perf-02.png
[3]: ./media/virtual-machines-linux-classic-optimize-mysql/virtual-machines-linux-optimize-mysql-perf-03.png
[4]: ./media/virtual-machines-linux-classic-optimize-mysql/virtual-machines-linux-optimize-mysql-perf-04.png
[5]: ./media/virtual-machines-linux-classic-optimize-mysql/virtual-machines-linux-optimize-mysql-perf-05.png
[6]: ./media/virtual-machines-linux-classic-optimize-mysql/virtual-machines-linux-optimize-mysql-perf-06.png
[7]: ./media/virtual-machines-linux-classic-optimize-mysql/virtual-machines-linux-optimize-mysql-perf-07.png
[8]: ./media/virtual-machines-linux-classic-optimize-mysql/virtual-machines-linux-optimize-mysql-perf-08.png
[9]: ./media/virtual-machines-linux-classic-optimize-mysql/virtual-machines-linux-optimize-mysql-perf-09.png
[10]: ./media/virtual-machines-linux-classic-optimize-mysql/virtual-machines-linux-optimize-mysql-perf-10.png
[11]: ./media/virtual-machines-linux-classic-optimize-mysql/virtual-machines-linux-optimize-mysql-perf-11.png
[12]: ./media/virtual-machines-linux-classic-optimize-mysql/virtual-machines-linux-optimize-mysql-perf-12.png
[13]: ./media/virtual-machines-linux-classic-optimize-mysql/virtual-machines-linux-optimize-mysql-perf-13.png
[14]: ./media/virtual-machines-linux-classic-optimize-mysql/virtual-machines-linux-optimize-mysql-perf-14.png
