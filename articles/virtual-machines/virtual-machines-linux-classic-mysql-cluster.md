<properties
    pageTitle="Clusterize MySQL med netværksbelastningen sæt | Microsoft Azure"
    description="Konfigurere en Indlæs afstemmes med høj tilgængelighed Linux MySQL klynge oprettet med den klassiske implementeringsmodel på Azure"
    services="virtual-machines-linux"
    documentationCenter=""
    authors="bureado"
    manager="timlt"
    editor=""
    tags="azure-service-management"/>

<tags
    ms.service="virtual-machines-linux"
    ms.workload="infrastructure-services"
    ms.tgt_pltfrm="vm-linux"
    ms.devlang="na"
    ms.topic="article"
    ms.date="04/14/2015"
    ms.author="jparrel"/>

# <a name="using-load-balanced-sets-to-clusterize-mysql-on-linux"></a>Brug af netværksbelastningen sæt til at clusterize MySQL på Linux

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]


Formålet med denne artikel er til at udforske og viser de tilgængelige til at udrulle meget tilgængelige Linux-baserede tjenester på Microsoft Azure, udforske MySQL Server høj tilgængelighed som en grundlæggende forskellige tilgange. En video, som illustrerer denne metode er tilgængelig på [kanal 9](http://channel9.msdn.com/Blogs/Open/Load-balancing-highly-available-Linux-services-on-Windows-Azure-OpenLDAP-and-MySQL).

Vi oprette en disposition for en delt to noder enkelt master MySQL høj tilgængelighed løsning baseret på DRBD, Corosync og Pacemaker. Kun én node kører MySQL ad gangen. Læse og skrive fra DRBD ressource er også begrænset til kun én node ad gangen.

Det er ikke nødvendigt VIP netværksløsning som LVS, da vi bruger Microsoft Azure Indlæs afstemmes sæt til at give både funktionen round robin- og slutpunkt påvisning, fjernelse og korrekt genoprettelse af VIP. VIP er en globalt distribueret IPv4-adresse, der er tildelt af Microsoft Azure, når vi opretter skytjenesten.

Der findes andre mulige arkitekturer for MySQL, herunder næste klynge, Percona og Galera samt flere løsninger til programmer, herunder mindst én som en VM på [VM lager](http://vmdepot.msopentech.com). Så længe disse løsninger kan gentage på unicast kontra multicast eller udsendelse og ikke kan stole på delt storage eller flere netværkgrænseflader, er scenarier nemt at udrulle på Microsoft Azure.

Disse klyngedannelse arkitekturer kan naturligvis udvides til andre produkter som PostgreSQL og OpenLDAP på en lignende måde. For eksempel denne belastning procedure med delte blev testet med flere master OpenLDAP, og du kan se dem på bloggen kanal 9.

## <a name="getting-ready"></a>Gør klar

Du skal en Microsoft Azure-konto med et gyldigt abonnement kan du oprette mindst to (2) FOS (X'ER blev brugt i dette eksempel), et netværk og et undernet, en forbindelse gruppe og en tilgængelighed angivet, samt muligheden for at oprette nye virtuelle harddiske i det samme område som skytjenesten og knytte dem til Linux FOS.

### <a name="tested-environment"></a>Testet miljø

- Ubuntu 13.10
  - DRBD
  - MySQL-Server
  - Corosync og Pacemaker

### <a name="affinity-group"></a>Forbindelse gruppe

Der oprettes en forbindelse gruppe for løsningen ved at logge ind på Azure klassisk portalen rulle ned til indstillinger og oprette en ny forbindelse gruppe. Tildelte ressourcer oprettes senere skal tildeles til denne forbindelse gruppe.

### <a name="networks"></a>Netværk

Der oprettes et nyt netværk, og der oprettes et undernet i netværket. Vi valgte et 10.10.10.0/24 netværk med kun én /24 undernet i.

### <a name="virtual-machines"></a>Virtuelle maskiner

Første Ubuntu 13.10 VM er oprettet ved hjælp af en Endorsed Ubuntu galleriet Hurtige og kaldes `hadb01`. Der oprettes en ny tjeneste i skyen i processen, kaldet hadb. Vi kalder det denne metode til at illustrere delt, netværksbelastningen art, du vil tildele tjenesten, når vi tilføjer flere ressourcer. Oprettelse af `hadb01` er uneventful og fuldført ved hjælp af portalen. Et slutpunkt for SSH oprettes automatisk, og vores oprettede netværk er markeret. Vi kan også vælge at oprette en ny tilgængelighed, der er angivet for FOS.

Når den første VM er oprettet (teknisk, når der oprettes skytjenesten) fortsættes til at oprette den anden VM `hadb02`. Vi vil også bruge Ubuntu 13.10 VM til den anden VM fra galleriet ved hjælp af portalen, men vi kan vælge at bruge en eksisterende skybaseret tjeneste, `hadb.cloudapp.net`, i stedet for at oprette en ny. Sættet netværk og tilgængelighed skal vælges automatisk til os. SSH ark oprettes, for.

Når begge FOS er blevet oprettet, kan vi skal du bemærke SSH porten til `hadb01` (TCP-22) og `hadb02` (automatisk tildelte ved Azure)

### <a name="attached-storage"></a>Vedhæftede lagerplads

Vi vedhæfte en ny disk til begge FOS og oprette nye 5 GB i processen. Diskene skal placeres i beholderen Virtuelle bruges om en vores primære operativsystem disk. Når diske oprettes og vedhæftet der er ingen grund til os til at genstarte Linux som kernen vil se den nye enhed (som regel `/dev/sdc`, kan du se `dmesg` output til)

På hver VM vi fortsætte med at oprette en ny partition ved hjælp af `cfdisk` (primært, Linux partition) og skrive den nye partition tabel. **Opretter ikke et filsystem på denne partition** .

## <a name="setting-up-the-cluster"></a>Konfiguration af klyngen

I begge Ubuntu FOS skal vi bruge APT til at installere Corosync, Pacemaker og DRBD. Ved hjælp af `apt-get`:

    sudo apt-get install corosync pacemaker drbd8-utils.

**Installer ikke MySQL på nuværende tidspunkt** . Debian og Ubuntu installationsscripts til starte en MySQL-datakataloget på `/var/lib/mysql`, men da mappen vil blive erstattet af et DRBD filsystem, vi skal gøre dette senere.

Vi bør på dette tidspunkt også kontrollere (ved hjælp af `/sbin/ifconfig`), at begge FOS bruger adresser i 10.10.10.0/24 undernettet og, kan de pinge hinanden ved navn. Hvis du ønsker, at du kan også bruge `ssh-keygen` og `ssh-copy-id` at sikre, at begge FOS kan kommunikere via SSH uden at kræve en adgangskode.

### <a name="setting-up-drbd"></a>Konfiguration af DRBD

Vi opretter en DRBD ressource, der bruger den underliggende `/dev/sdc1` partition til at oprette en `/dev/drbd1` ressource kan være formateret ved hjælp af ext3 og benyttes i både primære og sekundære noder. Du kan åbne `/etc/drbd.d/r0.res` og kopiere følgende ressource definitionen. I begge FOS, kan du gøre følgende:

    resource r0 {
      on `hadb01` {
        device  /dev/drbd1;
        disk   /dev/sdc1;
        address  10.10.10.4:7789;
        meta-disk internal;
      }
      on `hadb02` {
        device  /dev/drbd1;
        disk   /dev/sdc1;
        address  10.10.10.5:7789;
        meta-disk internal;
      }
    }

Når du gør dette, initialiseret ressource ved hjælp af `drbdadm` i begge FOS:

    sudo drbdadm -c /etc/drbd.conf role r0
    sudo drbdadm up r0

Og til sidst på primært (`hadb01`) gennemtvinge ejerskab (primær) på ressourcen, DRBD:

    sudo drbdadm primary --force r0

Hvis du undersøge indholdet af/procedure/drbd (`sudo cat /proc/drbd`) på begge FOS, skal du se afsnittet `Primary/Secondary` på `hadb01` og `Secondary/Primary` på `hadb02`, i overensstemmelse med løsning på dette tidspunkt. 5 GB disken synkroniseres via 10.10.10.0/24 netværket gratis til kunder.

Når disken synkroniseres kan du oprette filsystemet på `hadb01`. Til testformål brugte vi ext2, men nedenstående vejledning opretter en ext3 filsystemet:

    mkfs.ext3 /dev/drbd1

### <a name="mounting-the-drbd-resource"></a>Tilslutte DRBD ressourcen

På `hadb01` vi er nu klar til at tilslutte DRBD ressourcer. Debian og afledte produkter Brug `/var/lib/mysql` som Mysql's data mappe. Da vi ikke har installeret MySQL skal vi oprette mappen og tilslutte DRBD ressourcen. On `hadb01`:

    sudo mkdir /var/lib/mysql
    sudo mount /dev/drbd1 /var/lib/mysql

## <a name="setting-up-mysql"></a>Konfiguration af MySQL

Nu er du klar til at installere MySQL på `hadb01`:

    sudo apt-get install mysql-server

For `hadb02`, har du to muligheder. Du kan installere mysql-server nu, som opretter /var/lib/mysql og udfylde den med en ny mappe med data, og derefter fortsætte med at fjerne indholdet. On `hadb02`:

    sudo apt-get install mysql-server
    sudo service mysql stop
    sudo rm –rf /var/lib/mysql/*

Den anden mulighed er Sådan flytter du til `hadb02` og derefter installere mysql-server der (installationsscripts vil bemærke den eksisterende installation og ikke rører den)

On `hadb01`:

    sudo drbdadm secondary –force r0

On `hadb02`:

    sudo drbdadm primary –force r0
    sudo apt-get install mysql-server

Hvis du ikke planlægger Sådan flytter du DRBD nu, er den første indstilling nemmere selvom nærmere mindre elegante. Når du konfigurerer det, kan du begynde at arbejde på dine MySQL-database. På `hadb02` (eller er aktivt, uanset hvilken en af serverne ifølge DRBD):

    mysql –u root –p
    CREATE DATABASE azureha;
    CREATE TABLE things ( id SERIAL, name VARCHAR(255) );
    INSERT INTO things VALUES (1, "Yet another entity");
    GRANT ALL ON things.\* TO root;

**Advarsel**: denne sidste erklæring effektivt deaktiverer godkendelse for brugeren, roden i denne tabel. Dette bør erstattes af din fremstilling kvalitet give sætninger, og som er inkluderet kun til vejledende formål.

Du skal også aktivere netværk for MySQL Hvis du vil gøre forespørgsler fra uden for FOS, som er formålet med denne vejledning. Åbn på begge FOS `/etc/mysql/my.cnf` og gå til `bind-address`, ændrer det fra 127.0.0.1 til 0.0.0.0. Når du har gemt filen, udstede en `sudo service mysql restart` på din aktuelle primære.

### <a name="creating-the-mysql-load-balanced-set"></a>Oprette MySQL-indlæsning afstemmes sæt

Vi vil gå tilbage til portalen og gå til den `hadb01` VM, og klik derefter slutpunkter. Vi vil oprette et nyt slutpunkt skal du vælge MySQL (TCP 3306) fra rullemenuen og aksemærker i feltet *Opret ny Indlæs afstemmes sæt* . Vi kalder vores Indlæs afstemmes slutpunkt `lb-mysql`. Vi vil stadig lade de fleste af indstillingerne alene med undtagelse af tid, som vi vil reducere til 5 (sekunder, minimum)

Når der oprettes på slutpunkt vi gå til `hadb02`, slutpunkter, og oprette et nyt slutpunkt, men vi vælger `lb-mysql`, vælg derefter MySQL i rullemenuen. Du kan også bruge Azure CLI til dette trin.

Vi har alt, hvad vi brug for en manuel proces i klyngen i øjeblikket.

### <a name="testing-the-load-balanced-set"></a>Test af afkrydsningsfeltet Indlæs afstemmes sæt

Test, der kan udføres fra en ekstern maskine, ved hjælp af enhver MySQL-klient, samt programmer (for eksempel phpMyAdmin kører som et websted, der Azure) i dette tilfælde brugte vi Mysqls kommandolinjeværktøjet på en anden Linux boks:

    mysql azureha –u root –h hadb.cloudapp.net –e "select * from things;"

### <a name="manually-failing-over"></a>Manuelt ned over

Du kan simulere failover nu ved at lukke MySQL, skifte DRBDS primær og starte MySQL igen.

På hadb01:

    service mysql stop && umount /var/lib/mysql ; drbdadm secondary r0

Klik derefter på hadb02:

    drbdadm primary r0 ; mount /dev/drbd1 /var/lib/mysql && service mysql start

Når du failover manuelt du kan gentage forespørgslen remote og det skal fungerer perfekt.

## <a name="setting-up-corosync"></a>Konfiguration af Corosync

Corosync er den underliggende klynge struktur, der kræves til Pacemaker til at arbejde. For godkendelse af gyldighed v1 og v2 brugere (og andre metoder som Ultramonkey) Corosync er en opdeling af funktioner CRM, mens Pacemaker forbliver mere ligner Hearbeat i funktionalitet.

Den primære begrænsning for Corosync for Azure er, at Corosync foretrækker multicast over Udsend over unicast kommunikation, men Microsoft Azure netværk understøtter kun unicast.

Heldigvis Corosync har en arbejde unicast-tilstand, og kun reelle begrænsningen er, da alle noder ikke kommunikere indbyrdes *automagically*, du skal definere knuderne i dine konfigurationsfiler er, herunder deres IP-adresser. Vi kan bruge Corosync eksempelfilerne til Unicast og kun ændre binde adresse, node lister og logføring directory (Ubuntu bruger `/var/log/corosync` mens eksemplet filer Brug `/var/log/cluster`) og aktivere quorum værktøjer.

**Note på `transport: udpu` direktiv nedenfor og de manuelt defineret IP-adresser for knuderne**.

På `/etc/corosync/corosync.conf` for begge noder:

    totem {
      version: 2
      crypto_cipher: none
      crypto_hash: none
      interface {
        ringnumber: 0
        bindnetaddr: 10.10.10.0
        mcastport: 5405
        ttl: 1
      }
      transport: udpu
    }

    logging {
      fileline: off
      to_logfile: yes
      to_syslog: yes
      logfile: /var/log/corosync/corosync.log
      debug: off
      timestamp: on
      logger_subsys {
        subsys: QUORUM
        debug: off
        }
      }

    nodelist {
      node {
        ring0_addr: 10.10.10.4
        nodeid: 1
      }

      node {
        ring0_addr: 10.10.10.5
        nodeid: 2
      }
    }

    quorum {
      provider: corosync_votequorum
    }

Vi Kopiér denne konfigurationsfil i begge FOS og starte Corosync i begge noder:

    sudo service start corosync

Umiddelbart efter Start tjenesten klyngen indføres i den aktuelle ring og quorum skal bestå. Vi kan se denne funktionalitet ved at gennemgå logfiler eller:

    sudo corosync-quorumtool –l

Skal du følge en output, der svarer til billedet nedenfor:

![corosync quorumtool - l eksempel på output](media/virtual-machines-linux-classic-mysql-cluster/image001.png)

## <a name="setting-up-pacemaker"></a>Konfiguration af Pacemaker

Pacemaker bruger klyngen til at overvåge for at finde ressourcer, definere når primærfarver gå og skifte disse ressourcer til secondaries. Du kan definere ressourcer fra et sæt af tilgængelige scripts eller fra LSB (initialisering-lignende) scripts, mellem andre valgmuligheder.

Vi vil Pacemaker til "ejer" DRBD ressourcen, tilslutningspunkt og MySQL-tjenesten. Hvis Pacemaker kan slå til eller fra DRBD, tilslutte den / umount den, og start/stop MySQL i den rigtige rækkefølge, når det er noget forkert der sker med primært, vores installationen er fuldført.

Når du først installere Pacemaker, skal din konfiguration være nemt nok, stil:

    node $id="1" hadb01
      attributes standby="off"
    node $id="2" hadb02
      attributes standby="off"

Markér den ved at køre `sudo crm configure show`. Opret nu en fil (siger, `/tmp/cluster.conf`) med følgende ressourcer:

    primitive drbd_mysql ocf:linbit:drbd \
          params drbd_resource="r0" \
          op monitor interval="29s" role="Master" \
          op monitor interval="31s" role="Slave"

    ms ms_drbd_mysql drbd_mysql \
          meta master-max="1" master-node-max="1" \
            clone-max="2" clone-node-max="1" \
            notify="true"

    primitive fs_mysql ocf:heartbeat:Filesystem \
          params device="/dev/drbd/by-res/r0" \
          directory="/var/lib/mysql" fstype="ext3"

    primitive mysqld lsb:mysql

    group mysql fs_mysql mysqld

    colocation mysql_on_drbd \
           inf: mysql ms_drbd_mysql:Master

    order mysql_after_drbd \
           inf: ms_drbd_mysql:promote mysql:start

    property stonith-enabled=false

    property no-quorum-policy=ignore

Og nu indlæse den på konfigurationen (du skal kun gøre dette i en node):

    sudo crm configure
      load update /tmp/cluster.conf
      commit
      exit

Du skal også kontrollere, at Pacemaker starter ved start i begge noder:

    sudo update-rc.d pacemaker defaults

Efter et par sekunder, og brug af `sudo crm_mon –L`, Bekræft, at en af dine noder er blevet master for-klyngen, og kører alle ressourcerne. Du kan bruge Indlæs og ps til at kontrollere, at ressourcerne, der kører.

Følgende skærmbillede viser `crm_mon` med en node er stoppet (Afslut ved hjælp af Control-C)

![crm_mon node ikke længere](media/virtual-machines-linux-classic-mysql-cluster/image002.png)

Og dette skærmbillede viser begge noder med én master- og én slave:

![crm_mon funktionsdygtige master/slave](media/virtual-machines-linux-classic-mysql-cluster/image003.png)

## <a name="testing"></a>Test

Vi er klar til en automatisk failover simulering. Der er to måder til at gøre dette: Blødt og hårdt. Bløde som bruger den klynge lukning funktionen: ``crm_standby -U `uname -n` -v on``. På denne måde på masteren kan tage slaven over. Husk at angive det tilbage til fra (crm_mon fortæller dig, en node er i standby)

Det hårde måde lukker den primære VM (hadb01) via portalen eller ændre kørselsniveau på VM (det vil sige standse, lukning) og derefter hjælper vi Corosync og Pacemaker ved-signal master's gå ned. Vi kan teste dette (det er nyttigt til vedligeholdelse windows), men vi kan også gennemtvinge dette scenario ved at fryse lige VM.

## <a name="stonith"></a>STONITH

Det skal være muligt at udstede en VM lukning via Azure CLI i stedet for et STONITH-script, der styrer en fysisk enhed. Du kan bruge `/usr/lib/stonith/plugins/external/ssh` som en base og Aktivér STONITH i den klynge konfiguration. Azure CLI skal installeres globalt og Udgiv indstillinger/profil skal indlæses for den klynge bruger.

Eksempel på kode for den ressource, der er tilgængelige på [GitHub](https://github.com/bureado/aztonith). Du skal ændre den klynge konfiguration ved at tilføje følgende for at `sudo crm configure`:

    primitive st-azure stonith:external/azure \
      params hostlist="hadb01 hadb02" \
      clone fencing st-azure \
      property stonith-enabled=true \
      commit

**Note:** scriptet ikke udføre op/ned på trykkeri. Den oprindelige SSH ressource havde 15 ping kontrol, men gendannelse tid til en Azure VM kan være flere variabel.

## <a name="limitations"></a>Begrænsninger

Følgende begrænsninger gælder:

- Scriptet linbit DRBD ressource, der administrerer DRBD som en ressource i Pacemaker bruger `drbdadm down` ved lukning en node, selvom noden bare gå standby. Dette er ikke ideel, da slaven ikke vil synkroniserer DRBD ressourcen mens masteren bliver skriver. Hvis masteren ikke ikke elskværdig imødekommenhed, kan slaven overtage en ældre filsystem-tilstand. Der er to mulige måder at løse dette:
  - Gennemtvinge et `drbdadm up r0` i alle klyngenoder via en lokal (ikke clusterized) watchdog, eller
  - Redigere linbit DRBD scripts at sikre, at `down` ikke kaldes, i `/usr/lib/ocf/resource.d/linbit/drbd`.
- Justering af belastning skal mindst 5 sekunder at svare, så programmer skal være opmærksom på klynge og være mere tolerant af timeout; andre arkitekturer kan også hjælp, for eksempel i app'en køer, forespørgsel middlewares osv.
- Justering af MySQL er nødvendigt at sikre, at du skriver foretages i et sane tempo og cachelagre tømmes for at disk så ofte som muligt at minimere hukommelsestab
- Skrive ydeevnen vil være afhængige i VM forbinde i virtuelle Skift, som dette er den metode, der bruges af DRBD til at gentage enheden
