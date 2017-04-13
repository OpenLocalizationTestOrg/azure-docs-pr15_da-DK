<properties
    pageTitle="Oprette og overføre en Ubuntu Linux Virtuelle i Azure"
    description="Lær at oprette og overføre en Azure virtuel harddisk (Virtuelle), der indeholder en Ubuntu Linux-operativsystemet."
    services="virtual-machines-linux"
    documentationCenter=""
    authors="szarkos"
    manager="timlt"
    editor="tysonn"
    tags="azure-resource-manager,azure-service-management"/>

<tags
    ms.service="virtual-machines-linux"
    ms.workload="infrastructure-services"
    ms.tgt_pltfrm="vm-linux"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/24/2016"
    ms.author="szark"/>

# <a name="prepare-an-ubuntu-virtual-machine-for-azure"></a>Forberede en Ubuntu virtuelt til Azure

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-both-include.md)]

## <a name="official-ubuntu-cloud-images"></a>Officiel Ubuntu skyen billeder
Ubuntu publicerer nu officielle Azure virtuelle harddiske hentes på [http://cloud-images.ubuntu.com/](http://cloud-images.ubuntu.com/). Hvis du har brug for til at oprette dit eget specialiserede Ubuntu billede til Azure, i stedet anbefales for at bruge manuel fremgangsmåden nedenfor det at starte med disse kendte arbejde virtuelle harddiske og tilpasse efter behov. De seneste billede udgivelser kan altid findes på følgende placeringer:

 - Ubuntu 12.04/præcise: [ubuntu-12.04-server-cloudimg-amd64-disk1.vhd.zip](http://cloud-images.ubuntu.com/releases/precise/release/ubuntu-12.04-server-cloudimg-amd64-disk1.vhd.zip)
 - Ubuntu 14.04/Trusty: [ubuntu-14.04-server-cloudimg-amd64-disk1.vhd.zip](http://cloud-images.ubuntu.com/releases/trusty/release/ubuntu-14.04-server-cloudimg-amd64-disk1.vhd.zip)
 - Ubuntu 16.04/Xenial: [ubuntu-16.04-server-cloudimg-amd64-disk1.vhd.zip](http://cloud-images.ubuntu.com/releases/xenial/release/ubuntu-16.04-server-cloudimg-amd64-disk1.vhd.zip)


## <a name="prerequisites"></a>Forudsætninger

Denne artikel forudsætter, at du allerede har installeret en Ubuntu Linux-operativsystemet til en virtuel harddisk. Der findes flere værktøjer for at oprette .vhd filer, for eksempel en virtualization løsning som Hyper-V. Flere oplysninger under [installere Hyper-V-rollen og konfigurere en virtuel maskine](http://technet.microsoft.com/library/hh846766.aspx).

**Ubuntu installationsnoter**

- Se også [Generelle Linux Installationsnoter](virtual-machines-linux-create-upload-generic.md#general-linux-installation-notes) flere tip til forberedelse Linux til Azure.
- Formatet VHDX understøttes ikke i Azure, kun **fast Virtuelle**.  Du kan konvertere disken Virtuelle formatet ved hjælp af Hyper-V Manager eller Konverter virtuelle-cmdlet.
- Det anbefales, at du bruger standard partitioner i stedet for LVM (ofte standard for mange installationer), når du installerer Linux systemet. Derved undgår LVM navnet er i konflikt med klonede FOS, især hvis en OS disk overhovedet skal knyttes til en anden VM til fejlfinding. [LVM](virtual-machines-linux-configure-lvm.md) eller [RAID](virtual-machines-linux-configure-raid.md) kan bruges på datadisce Hvis Foretrukne.
- Konfigurer ikke en partition Byt om på OS disken. Linux agent kan konfigureres til at oprette filen Byt om på disken midlertidige ressource.  Kan finde flere oplysninger om dette i nedenstående fremgangsmåde.
- Alle de virtuelle harddiske skal have størrelser, multiplum af 1 MB.


## <a name="manual-steps"></a>Manuelle trin

> [AZURE.NOTE] Før du opretter dit eget brugerdefinerede Ubuntu billede til Azure, skal du overveje at bruge billeder fra [http://cloud-images.ubuntu.com/](http://cloud-images.ubuntu.com/) i stedet.


1. Vælg den virtuelle maskine i den midterste rude af Hyper-V Manager.

2. Klik på **Opret forbindelse** for at åbne vinduet for den virtuelle maskine.

3.  Erstat de aktuelle typer lagre i billedet for at bruge Ubuntu's Azure repos. Trinnene variere en smule, afhængigt af Ubuntu versionen.

    Før du redigerer /etc/apt/sources.list, anbefales det at oprette en sikkerhedskopi:

        # sudo cp /etc/apt/sources.list /etc/apt/sources.list.bak

    Ubuntu 12.04:

        # sudo sed -i "s/[a-z][a-z].archive.ubuntu.com/azure.archive.ubuntu.com/g" /etc/apt/sources.list
        # sudo apt-get update

    Ubuntu 14.04:

        # sudo sed -i "s/[a-z][a-z].archive.ubuntu.com/azure.archive.ubuntu.com/g" /etc/apt/sources.list
        # sudo apt-get update

4. Ubuntu Azure billederne følger nu kernen *aktivering af hardware* (HWE). Opdatere operativsystemet til seneste kernen ved at køre følgende kommandoer:

    Ubuntu 12.04:

        # sudo apt-get update
        # sudo apt-get install linux-image-generic-lts-trusty linux-cloud-tools-generic-lts-trusty
        # sudo apt-get install hv-kvp-daemon-init
        (recommended) sudo apt-get dist-upgrade

        # sudo reboot

    Ubuntu 14.04:

        # sudo apt-get update
        # sudo apt-get install linux-image-virtual-lts-vivid linux-lts-vivid-tools-common
        # sudo apt-get install hv-kvp-daemon-init
        (recommended) sudo apt-get dist-upgrade

        # sudo reboot


5. Ændre start kernen linjen for at rydde medtage yderligere kernen parametre til Azure. Du gør dette, Åbn "/ osv/standard/rydde" i et tekstredigeringsprogram finde variablen kaldet `GRUB_CMDLINE_LINUX_DEFAULT` (eller tilføje den, hvis det er nødvendigt) og rediger den for at medtage følgende parametre:

        GRUB_CMDLINE_LINUX_DEFAULT="console=tty1 console=ttyS0,115200n8 earlyprintk=ttyS0,115200 rootdelay=300"

    Gemme og lukke filen og derefter køre '`sudo update-grub`'. Dette sikrer alle console meddelelser sendes til den første serielle port, som kan hjælpe Azure teknisk support med fejlfinding af problemer.

6.  Sørg for, at SSH serveren er installeret og konfigureret til at starte på starttidspunktet.  Det er normalt standard.

7.  Installer Azure Linux-Agent:

        # sudo apt-get update
        # sudo apt-get install walinuxagent

    Bemærk denne installation på `walinuxagent` pakke, fjernes de `NetworkManager` og `NetworkManager-gnome` pakker, hvis de er installeret.

8.  Kør følgende kommandoer til deprovision den virtuelle maskine og forberede klargøring af Azure:

        # sudo waagent -force -deprovision
        # export HISTSIZE=0
        # logout

9. Klik på- **handling -> Luk ned** i Hyper-V Manager. Din Linux VHD er nu klar til at blive overført til Azure.


## <a name="next-steps"></a>Næste trin
Nu er du klar til brug harddisken Ubuntu Linux virtuelle til at oprette nye virtuelle maskiner i Azure. Hvis dette er første gang, du overfører .vhd-filen til Azure, kan du se trin 2 og 3 i [oprette og overføre en virtuel harddisk, der indeholder Linux-operativsystemet](virtual-machines-linux-classic-create-upload-vhd.md).

## <a name="references"></a>Referencer ##

Ubuntu hardware aktivering af (HWE) kerne:

- [http://blog.utlemming.org/2015/01/Ubuntu-1404-Azure-images-Now-Tracking.HTML](http://blog.utlemming.org/2015/01/ubuntu-1404-azure-images-now-tracking.html)
- [http://blog.utlemming.org/2015/02/1204-Azure-cloud-images-Now-Using-hwe.HTML](http://blog.utlemming.org/2015/02/1204-azure-cloud-images-now-using-hwe.html)
