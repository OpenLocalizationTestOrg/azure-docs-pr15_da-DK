<properties
    pageTitle="Forberede Debian Linux Virtuelle | Microsoft Azure"
    description="Lær, hvordan du opretter Debian 7 og 8 Virtuelle filer til installation i Azure."
    services="virtual-machines-linux"
    documentationCenter=""
    authors="szarkos"
    manager="timlt"
    editor=""
    tags="azure-resource-manager,azure-service-management"/>

<tags
    ms.service="virtual-machines-linux"
    ms.workload="infrastructure-services"
    ms.tgt_pltfrm="vm-linux"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/24/2016"
    ms.author="szark"/>



# <a name="prepare-a-debian-vhd-for-azure"></a>Forberede en Debian Virtuelle til Azure

## <a name="prerequisites"></a>Forudsætninger
Dette afsnit antages det, at du allerede har installeret et Debian Linux-operativsystem fra en .iso-fil, der er hentet fra [Debian websted](https://www.debian.org/distrib/) til en virtuel harddisk. Der findes flere værktøjer for at oprette .vhd filer. Hyper-V er kun ét eksempel. Flere oplysninger ved hjælp af Hyper-V under [installere Hyper-V-rollen og konfigurere en virtuel maskine](https://technet.microsoft.com/library/hh846766.aspx).


## <a name="installation-notes"></a>Installationsnoter

- Se også [Generelle Linux Installationsnoter](virtual-machines-linux-create-upload-generic.md#general-linux-installation-notes) flere tip til forberedelse Linux til Azure.
- Det nyere VHDX format understøttes ikke i Azure. Du kan konvertere disken Virtuelle formatet ved hjælp af Hyper-V Manager eller **Konverter virtuelle** -cmdlet.
- Det anbefales, at du bruger standard partitioner i stedet for LVM (ofte standard for mange installationer), når du installerer Linux systemet. Derved undgår LVM navnet er i konflikt med klonede FOS, især hvis en OS disk overhovedet skal knyttes til en anden VM til fejlfinding. [LVM](virtual-machines-linux-configure-lvm.md) eller [RAID](virtual-machines-linux-configure-raid.md) kan bruges på datadisce Hvis Foretrukne.
- Konfigurer ikke en partition Byt om på OS disken. Azure Linux agent kan konfigureres til at oprette filen Byt om på disken midlertidige ressource. Kan finde flere oplysninger om dette i nedenstående fremgangsmåde.
- Alle de virtuelle harddiske skal have størrelser, multiplum af 1 MB.


## <a name="use-azure-manage-to-create-debian-vhds"></a>Brug Azure-administrere til at oprette Debian virtuelle harddiske

Der findes værktøjer til at oprette virtuelle Debian harddiske til Azure, som den [azure-administrere](https://github.com/credativ/azure-manage) scripts fra [credativ](http://www.credativ.com/). Dette er den anbefalede tilgang kontra oprettelse af et billede fra bunden. For eksempel til at oprette en Debian 8 Virtuelle køre følgende kommandoer for at hente azure-administrere (og afhængigheder) og køre scriptet azure_build_image:

    # sudo apt-get update
    # sudo apt-get install git qemu-utils mbr kpartx debootstrap

    # sudo apt-get install python3-pip python3-dateutil python3-cryptography
    # sudo pip3 install azure-storage azure-servicemanagement-legacy azure-common pytest pyyaml
    # git clone https://github.com/credativ/azure-manage.git
    # cd azure-manage
    # sudo pip3 install .

    # sudo azure_build_image --option release=jessie --option image_size_gb=30 --option image_prefix=debian-jessie-azure section


## <a name="manually-prepare-a-debian-vhd"></a>Forberede en Debian Virtuelle manuelt

1. Vælg den virtuelle maskine i Hyper-V Manager.

2. Klik på **Opret forbindelse** for at åbne et vindue med console for den virtuelle maskine.

3. Kommentar ud linjen for **deb cdrom** i `/etc/apt/source.list` Hvis du konfigurerer VM med en ISO-fil.

4. Redigere den `/etc/default/grub` fil og ændre parameteren **GRUB_CMDLINE_LINUX** på følgende måde for at medtage yderligere kernen parametre til Azure.

        GRUB_CMDLINE_LINUX="console=tty0 console=ttyS0,115200 earlyprintk=ttyS0,115200 rootdelay=30"

5. Genopbygge rydde, og kør:

        # sudo update-grub

6. Tilføje Debian's Azure lagre i /etc/apt/sources.list til Debian 7 eller 8:

    **Debian 7.x "Wheezy"**

        deb http://debian-archive.trafficmanager.net/debian wheezy-backports main
        deb-src http://debian-archive.trafficmanager.net/debian wheezy-backports main
        deb http://debian-archive.trafficmanager.net/debian-azure wheezy main
        deb-src http://debian-archive.trafficmanager.net/debian-azure wheezy main


    **Debian 8.x "Jessie"**

        deb http://debian-archive.trafficmanager.net/debian jessie-backports main
        deb-src http://debian-archive.trafficmanager.net/debian jessie-backports main
        deb http://debian-archive.trafficmanager.net/debian-azure jessie main
        deb-src http://debian-archive.trafficmanager.net/debian-azure jessie main


7. Installer Azure Linux-Agent:

        # sudo apt-get update
        # sudo apt-get install waagent

8. Det er påkrævet for at køre kernen 3.16-baserede fra wheezy backports lager til Debian 7. Først oprette en fil med navnet /etc/apt/preferences.d/linux.pref med følgende indhold:

        Package: linux-image-amd64 initramfs-tools
        Pin: release n=wheezy-backports
        Pin-Priority: 500

    Kør "sudo-Hovedgaden få installation. linux-billede-amd64" for derefter at installere nye kernen.

8. Deprovision den virtuelle maskine og forberede klargøring af Azure, og kør:

        # sudo waagent –force -deprovision
        # export HISTSIZE=0
        # logout

9. Klik på **handling** -> Luk ned i Hyper-V Manager. Din Linux VHD er nu klar til at blive overført til Azure.


## <a name="next-steps"></a>Næste trin

Du er nu klar til at bruge din Debian virtuelle harddisk til at oprette nye virtuelle maskiner i Azure. Hvis dette er første gang, du overfører .vhd-filen til Azure, kan du se trin 2 og 3 i [oprette og overføre en virtuel harddisk, der indeholder Linux-operativsystemet](virtual-machines-linux-classic-create-upload-vhd.md).
