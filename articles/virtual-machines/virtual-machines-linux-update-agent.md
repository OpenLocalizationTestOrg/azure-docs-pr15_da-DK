<properties
    pageTitle="Opdatere den Azure Linux Agent fra GitHub | Microsoft Azure"
    description="Lær, hvordan du opdateringen Azure Linux Agent til din Linux VM i Azure til lateset versionen fra Github"
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
    ms.date="12/14/2015"
    ms.author="mingzhan"/>


# <a name="how-to-update-the-azure-linux-agent-on-a-vm-to-the-latest-version-from-github"></a>Sådan opdaterer du Azure Linux Agent på en VM til den nyeste version fra GitHub

Hvis du vil opdatere din [Azure Linux Agent](https://github.com/Azure/WALinuxAgent) på en Linux VM i Azure, skal du allerede have:

1. En igangværende Linux VM i Azure.
2. En forbindelse til den Linux VM ved hjælp af SSH.

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-both-include.md)]

<br>

> [AZURE.NOTE] Hvis du vil udføre denne opgave fra en Windows-computer, kan du bruge trykfarver til SSH til computeren Linux. Se, [hvordan du logger på et virtuelt kører Linux](virtual-machines-linux-mac-create-ssh-keys.md)kan finde flere oplysninger.

Azure-godkendt Linux distros er lagt pakken Azure Linux Agent i deres typer lagre, så skal du kontrollere og installere den nyeste version fra Distro lageret først Hvis det er muligt.  

Ubuntu, blot skrive:

    #sudo apt-get install walinuxagent

Og CentOS, Skriv:

    #sudo yum install waagent


For Oracle Linux Sørg for, at den `Addons` lager er aktiveret. Vælg for at redigere filen `/etc/yum.repos.d/public-yum-ol6.repo`(Oracle Linux 6) eller `/etc/yum.repos.d/public-yum-ol7.repo`(Oracle Linux), og ret linjen `enabled=0` til `enabled=1` under **[ol6_addons]** eller **[ol7_addons]** i filen.

Skriv derefter for at installere den nyeste version af Azure Linux Agent skal:


    #sudo yum install WALinuxAgent

Hvis du ikke kan finde tilføjelsesprogram lageret kan du blot tilføje disse linjer i slutningen af filen .repo ifølge din Oracle Linux-version:

For Oracle Linux 6 virtuelle maskiner:

    [ol6_addons]
    name=Add-Ons for Oracle Linux $releasever ($basearch)
    baseurl=http://public-yum.oracle.com/repo/OracleLinux/OL6/addons/x86_64
    gpgkey=http://public-yum.oracle.com/RPM-GPG-KEY-oracle-ol6
    gpgcheck=1
    enabled=1

For Oracle Linux 7 virtuelle maskiner:

    [ol7_addons]
    name=Oracle Linux $releasever Add ons ($basearch)
    baseurl=http://public-yum.oracle.com/repo/OracleLinux/OL7/addons/$basearch/
    gpgkey=file:///etc/pki/rpm-gpg/RPM-GPG-KEY-oracle
    gpgcheck=1
    enabled=0

Skriv derefter:

    #sudo yum update WALinuxAgent

Dette er typisk alt, du har brug for, men hvis eller anden grund, du har brug at installere den fra https://github.com direkte, kan du følge nedenstående trin.


## <a name="install-wget"></a>Installere wget

Log på din VM ved hjælp af SSH.

Installere wget (der er nogle distros, der ikke kan installere den som standard som Redhat, CentOS og Oracle Linux versioner 6.4 og 6.5) ved at skrive `#sudo yum install wget` på kommandolinjen.


## <a name="download-the-latest-version"></a>Hente den nyeste version

Åbn [version af Azure Linux Agent i GitHub](https://github.com/Azure/WALinuxAgent/releases) på en webside, og find ud af det seneste nummer. (Du kan finde din aktuelle version ved at skrive `#waagent --version`.)

### <a name="for-version-20x-type"></a>Efter version 2.0.x skal du skrive:

    #wget https://raw.githubusercontent.com/Azure/WALinuxAgent/WALinuxAgent-[version]/waagent  

   Følgende linje bruger version 2.0.14 som et eksempel:

    #wget https://raw.githubusercontent.com/Azure/WALinuxAgent/WALinuxAgent-2.0.14/waagent  

### <a name="for-version-21x-or-later-type"></a>Efter version 2.1.x eller nyere, Skriv:

    #wget https://github.com/Azure/WALinuxAgent/archive/WALinuxAgent-[version].zip
    #unzip WALinuxAgent-[version].zip
    #cd WALinuxAgent-[version]

   Følgende linje bruger version 2.1.0 som et eksempel:

    #wget https://github.com/Azure/WALinuxAgent/archive/WALinuxAgent-2.1.0.zip
    #unzip WALinuxAgent-2.1.0.zip  
    #cd WALinuxAgent-2.1.0

## <a name="install-the-azure-linux-agent"></a>Installer Azure Linux-Agent

### <a name="for-version-20x-use"></a>Efter version 2.0.x, brug:

 Gør waagent eksekverbar:

    #chmod +x waagent

 Kopiere ny den eksekverbare fil til /usr/sbin /.

  De fleste Linux kan du bruge:

    #sudo cp waagent /usr/sbin

  Bruge CoreOS:

    #sudo cp waagent /usr/share/oem/bin/

  Hvis dette er en ny version af Azure Linux Agent, skal du køre:
 
    #sudo /usr/sbin/waagent -install -verbose

### <a name="for-version-21x-use"></a>Efter version 2.1.x, brug:

Du skal muligvis installere pakken `setuptools` først – se [her](https://pypi.python.org/pypi/setuptools). Kør derefter:

    #sudo python setup.py install

## <a name="restart-the-waagent-service"></a>Genstart tjenesten waagent

For de fleste linux Distros:

    #sudo service waagent restart

Bruge Ubuntu:

    #sudo service walinuxagent restart

Bruge CoreOS:

    #sudo systemctl restart waagent

## <a name="confirm-the-azure-linux-agent-version"></a>Bekræfte versionen af Azure Linux Agent

    #waagent -version

For CoreOS virker ovenstående kommando ikke.

Du vil se, at versionen af Azure Linux Agent er blevet opdateret til den nye version.

Du kan finde flere oplysninger om Azure Linux Agent [Azure Linux Agent vigtige oplysninger om](https://github.com/Azure/WALinuxAgent).
