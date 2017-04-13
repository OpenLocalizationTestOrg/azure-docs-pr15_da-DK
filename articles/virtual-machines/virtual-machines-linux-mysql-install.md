<properties
    pageTitle="Konfigurere MySQL på en Linux VM | Microsoft Azure "
    description="Lær, hvordan du installerer MySQL stablen på en Linux virtuel maskine (Ubuntu eller RedHat familie OS) i Azure"
    services="virtual-machines-linux"
    documentationCenter=""
    authors="SuperScottz"
    manager="timlt"
    editor=""
    tags="azure-resource-manager,azure-service-management"/>

<tags
    ms.service="virtual-machines-linux"
    ms.workload="infrastructure-services"
    ms.tgt_pltfrm="vm-linux"
    ms.devlang="na"
    ms.topic="article"
    ms.date="02/01/2016"
    ms.author="mingzhan"/>


#<a name="how-to-install-mysql-on-azure"></a>Hvordan du installerer MySQL på Azure


I denne artikel kan lære du, hvordan du installerer og konfigurerer MySQL på en Azure virtuelt kører Linux.

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-both-include.md)]


##<a name="install-mysql-on-your-virtual-machine"></a>Installere MySQL på din virtuelle maskine

> [AZURE.NOTE] Du skal allerede har en Microsoft Azure virtuel maskine, der kører Linux for at udføre dette selvstudium. Se [Azure Linux VM selvstudium](virtual-machines-linux-quick-create-cli.md) for at oprette og konfigurere en Linux VM med `mysqlnode` som VM navnet og `azureuser` som bruger, før du fortsætter.

I dette tilfælde Brug 3306 port som MySQL-porten.  

Oprette forbindelse til Linux VM, du har oprettet via trykfarver. Hvis dette er første gang du bruger Azure Linux VM, se, hvordan du bruger trykfarver oprette forbindelse til en Linux VM [her](virtual-machines-linux-mac-create-ssh-keys.md).

Vi bruger lager pakke til at installere MySQL5.6 som et eksempel i denne artikel. MySQL5.6 har faktisk, flere forbedring af ydeevne end MySQL5.5.  Få mere at vide [her](http://www.mysqlperformanceblog.com/2013/02/18/is-mysql-5-6-slower-than-mysql-5-5/).


###<a name="how-to-install-mysql56-on-ubuntu"></a>Hvordan du installerer MySQL5.6 på Ubuntu
Vi bruger Linux VM med Ubuntu fra Azure her.

- Trin 1: Installer MySQL Server 5,6 Skift til `root` bruger:

            #[azureuser@mysqlnode:~]sudo su -

    Installere mysql-server 5,6:

            #[root@mysqlnode ~]# apt-get update
            #[root@mysqlnode ~]# apt-get -y install mysql-server-5.6

    Under installationen vises der en dialogboks vinduet poping op til stil, du kan angive MySQL rod adgangskode nedenfor, og du skal angive adgangskoden her.

    ![Billede af](./media/virtual-machines-linux-mysql-install/virtual-machines-linux-install-mysql-p1.png)


    Skriv adgangskoden igen for at bekræfte.

    ![Billede af](./media/virtual-machines-linux-mysql-install/virtual-machines-linux-install-mysql-p2.png)

- Trin 2: Login MySQL Server

    Når du er færdig MySQL serverinstallation MySQL tjenesten startes automatisk. Du kan logge MySQL-Server med `root` bruger.
    Brug den under kommandoen til logon og input adgangskode.

             #[root@mysqlnode ~]# mysql -uroot -p

- Trin 3: Administrere tjenesten kører MySQL

    (a) få status for MySQL-tjenesten

             #[root@mysqlnode ~]# service mysql status

    (b) start MySQL-tjenesten

             #[root@mysqlnode ~]# service mysql start

    (c) stoppe MySQL-tjenesten

             #[root@mysqlnode ~]# service mysql stop

    (d) genstarte MySQL-tjenesten

             #[root@mysqlnode ~]# service mysql restart


###<a name="how-to-install-mysql-on-red-hat-os-family-like-centos-oracle-linux"></a>Hvordan du installerer MySQL på rød Hat OS familie som CentOS, Oracle Linux
Vi bruger Linux VM med CentOS eller Oracle Linux her.

- Trin 1: Tilføje MySQL Yum lager Skift til `root` bruger:

            #[azureuser@mysqlnode:~]sudo su -

    Hent og Installer pakken MySQL version:

            #[root@mysqlnode ~]# wget http://repo.mysql.com/mysql-community-release-el6-5.noarch.rpm
            #[root@mysqlnode ~]# yum localinstall -y mysql-community-release-el6-5.noarch.rpm

- Trin 2: Rediger under filen for at aktivere MySQL-lager til at hente pakken MySQL5.6.

            #[root@mysqlnode ~]# vim /etc/yum.repos.d/mysql-community.repo

    Opdatere hver værdi af denne fil til nedenfor:

        \# *Enable to use MySQL 5.6*

        [mysql56-community]
        name=MySQL 5.6 Community Server

        baseurl=http://repo.mysql.com/yum/mysql-5.6-community/el/6/$basearch/

        enabled=1

        gpgcheck=1

        gpgkey=file:/etc/pki/rpm-gpg/RPM-GPG-KEY-mysql

- Trin 3: Installer MySQL fra MySQL lager installere MySQL:

           #[root@mysqlnode ~]#yum install mysql-community-server

    MySQL RPM pakke og alle relaterede pakker installeres.

- Trin 4: Administrere tjenesten kører MySQL

    (a) Kontrollér Tjenestestatus på MySQL-serveren:

           #[root@mysqlnode ~]#service mysqld status

    (b) Kontrollér, om port MySQL standardserveren kører:

           #[root@mysqlnode ~]#netstat  –tunlp|grep 3306


    (c) start MySQL-server:

           #[root@mysqlnode ~]#service mysqld start

    (d) stop MySQL-server:

           #[root@mysqlnode ~]#service mysqld stop

    (e) angive MySQL til at starte hvornår system opstart:

           #[root@mysqlnode ~]#chkconfig mysqld on


###<a name="how-to-install-mysql-on-suse-linux"></a>Hvordan du installerer MySQL på SUSE Linux
Vi bruger Linux VM med OpenSUSE her.

- Trin 1: Hente og installere MySQL-Server

    Skifte til `root` bruger igennem under kommandoen:  

           #sudo su -

    Hent og Installer MySQL-pakke:

           #[root@mysqlnode ~]# zypper update

           #[root@mysqlnode ~]# zypper install mysql-server mysql-devel mysql

- Trin 2: Administrere tjenesten kører MySQL

    (a) Kontrollér status for MySQL-server:

           #[root@mysqlnode ~]# rcmysql status

    (b) Kontrollér om standardporten for MySQL-server:

           #[root@mysqlnode ~]# netstat  –tunlp|grep 3306


    (c) start MySQL-server:

           #[root@mysqlnode ~]# rcmysql start

    (d) stop MySQL-server:

           #[root@mysqlnode ~]# rcmysql stop

    (e) angive MySQL til at starte hvornår system opstart:

           #[root@mysqlnode ~]# insserv mysql

###<a name="next-step"></a>Næste trin
Find flere brugen og oplysninger om MySQL [her](https://www.mysql.com/).
