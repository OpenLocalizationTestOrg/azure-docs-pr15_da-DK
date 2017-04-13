<properties
    pageTitle="Oprette og overføre en Oracle Linux Virtuelle | Microsoft Azure"
    description="Lær at oprette og overføre en Azure virtuel harddisk (Virtuelle), der indeholder en Oracle Linux-operativsystemet."
    services="virtual-machines-linux"
    documentationCenter=""
    authors="szarkos"
    manager="timlt"
    editor="tysonn"
    tags="azure-service-management,azure-resource-manager" />

<tags
    ms.service="virtual-machines-linux"
    ms.workload="infrastructure-services"
    ms.tgt_pltfrm="vm-linux"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/24/2016"
    ms.author="szark"/>

# <a name="prepare-an-oracle-linux-virtual-machine-for-azure"></a>Forberede en Oracle Linux virtuelt til Azure

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-both-include.md)]

## <a name="prerequisites"></a>Forudsætninger ##

Denne artikel forudsætter, at du allerede har installeret en Oracle Linux-operativsystemet til en virtuel harddisk. Der findes flere værktøjer for at oprette .vhd filer, for eksempel en virtualization løsning som Hyper-V. Flere oplysninger under [installere Hyper-V-rollen og konfigurere en virtuel maskine](http://technet.microsoft.com/library/hh846766.aspx).


### <a name="oracle-linux-installation-notes"></a>Oracle Linux installationsnoter

- Se også [Generelle Linux Installationsnoter](virtual-machines-linux-create-upload-generic.md#general-linux-installation-notes) flere tip til forberedelse Linux til Azure.

- Oracle-Red Hat kompatible kerne og deres UEK3 (Unbreakable Enterprise kerne) understøttes både på Hyper-V og Azure. Sørg for at opdatere til den seneste kernen under forberedelse af din Oracle Linux Virtuelle fremgangsmåde opnår de bedste resultater.

- Oracle-UEK2 understøttes ikke på Hyper-V og Azure, som ikke omfatter de nødvendige drivere.

- Formatet VHDX understøttes ikke i Azure, kun **fast Virtuelle**.  Du kan konvertere disken Virtuelle formatet ved hjælp af Hyper-V Manager eller Konverter virtuelle-cmdlet.

- Det anbefales, at du bruger standard partitioner i stedet for LVM (ofte standard for mange installationer), når du installerer Linux systemet. Derved undgår LVM navnet er i konflikt med klonede FOS, især hvis en OS disk overhovedet skal knyttes til en anden VM til fejlfinding. [LVM](virtual-machines-linux-configure-lvm.md) eller [RAID](virtual-machines-linux-configure-raid.md) kan bruges på datadisce Hvis Foretrukne.

- NUMA understøttes ikke af VM større på grund af en fejl i Linux kerneversioner under 2.6.37. Dette problem påvirker primært distribution af den foregående Red Hat 2.6.32 kerne. Manuel installation af Azure Linux agent (waagent) deaktiveres automatisk NUMA i sektionen rydde konfiguration for Linux kernen. Kan finde flere oplysninger om dette i nedenstående fremgangsmåde.

- Konfigurer ikke en partition Byt om på OS disken. Linux agent kan konfigureres til at oprette filen Byt om på disken midlertidige ressource.  Kan finde flere oplysninger om dette i nedenstående fremgangsmåde.

- Alle de virtuelle harddiske skal have størrelser, multiplum af 1 MB.

- Sørg for, at den `Addons` lager er aktiveret. Redigere filen `/etc/yum.repo.d/public-yum-ol6.repo`(Oracle Linux 6) eller `/etc/yum.repo.d/public-yum-ol7.repo`(Oracle Linux), og ret linjen `enabled=0` til `enabled=1` under **[ol6_addons]** eller **[ol7_addons]** i filen.


## <a name="oracle-linux-64"></a>Oracle Linux 6.4 + ##

Du skal fuldføre bestemte konfigurationstrinnene i operativsystemet til den virtuelle maskine til at køre i Azure.

1. Vælg den virtuelle maskine i den midterste rude af Hyper-V Manager.

2. Klik på **Opret forbindelse** for at åbne vinduet for den virtuelle maskine.

3. Fjerne NetworkManager ved at køre følgende kommando:

        # sudo rpm -e --nodeps NetworkManager

    **Note:** Hvis pakken ikke allerede er installeret, mislykkes denne kommando med en fejlmeddelelse. Dette er forventet.

4.  Oprette en fil med navnet **netværk** i den `/etc/sysconfig/` mappe, der indeholder følgende tekst:

        NETWORKING=yes
        HOSTNAME=localhost.localdomain

5.  Oprette en fil med navnet **ifcfg eth0** i den `/etc/sysconfig/network-scripts/` mappe, der indeholder følgende tekst:

        DEVICE=eth0
        ONBOOT=yes
        BOOTPROTO=dhcp
        TYPE=Ethernet
        USERCTL=no
        PEERDNS=yes
        IPV6INIT=no

6.  Ændre udev regler for at undgå at generere statisk regler for Ethernet-grænseflader. Disse regler kan medføre problemer, når klone en virtuel maskine i Microsoft Azure eller Hyper-v

        # sudo ln -s /dev/null /etc/udev/rules.d/75-persistent-net-generator.rules
        # sudo rm -f /etc/udev/rules.d/70-persistent-net.rules

7. Sikre Netværkstjeneste begynder på starttidspunktet ved at køre følgende kommando:

        # chkconfig network on

8. Installere python pyasn1 ved at køre følgende kommando:

        # sudo yum install python-pyasn1

9.  Ændre start kernen linje i konfigurationen rydde medtage yderligere kernen parametre til Azure. Gøre denne Åbn "/ boot/grub/menu.lst" i et tekstredigeringsprogram, og Sørg for, at standard kernen omfatter følgende parametre:

        console=ttyS0 earlyprintk=ttyS0 rootdelay=300 numa=off

    Dette sikrer også alle console meddelelser sendes til den første serielle port, som kan hjælpe Azure support med fejlfinding af problemer. Dette vil deaktivere NUMA på grund af en fejl i Oracle-Red Hat kompatible kerne.

    Ud over ovenstående anbefales det at *fjerne* følgende parametre:

        rhgb quiet crashkernel=auto

    Grafiske og stille Start ikke er nyttig i et skybaseret miljø, hvor vi vil alle logfilerne, der skal sendes til den serielle port.

    Den `crashkernel` indstilling kan være konfigureret eventuelt venstre, men Bemærk, at denne parameter kan reducere mængden af tilgængelig hukommelse i VM 128MB eller derover, som kan være problematisk på mindre VM størrelsen.


10. Sørg for, at SSH serveren er installeret og konfigureret til at starte på starttidspunktet.  Det er normalt standard.

11. Installer Azure Linux Agent ved at køre følgende kommando. Den nyeste version er 2.0.15.

        # sudo yum install WALinuxAgent

    Bemærk, at du installerer pakken WALinuxAgent, fjerner du NetworkManager og NetworkManager gnome pakker Hvis de ikke blev allerede fjernet som beskrevet i trin 2.

12. Opret ikke mellemrum Byt om på OS disken.

    Azure Linux Agent kan automatisk konfigurere Udskift mellemrum ved hjælp af den lokale ressourcer disk, der er knyttet til VM efter klargøring af på Azure. Bemærk, at lokal ressource disken er en *midlertidig* disk, og kan være blevet tømt når VM er fjernet. Når du har installeret Azure Linux Agent (se forrige trin) ændre følgende parametre i /etc/waagent.conf korrekt:

        ResourceDisk.Format=y
        ResourceDisk.Filesystem=ext4
        ResourceDisk.MountPoint=/mnt/resource
        ResourceDisk.EnableSwap=y
        ResourceDisk.SwapSizeMB=2048    ## NOTE: set this to whatever you need it to be.

13. Kør følgende kommandoer til deprovision den virtuelle maskine og forberede klargøring af Azure:

        # sudo waagent -force -deprovision
        # export HISTSIZE=0
        # logout

14. Klik på- **handling -> Luk ned** i Hyper-V Manager. Din Linux VHD er nu klar til at blive overført til Azure.


----------


## <a name="oracle-linux-70"></a>Oracle Linux 7.0 + ##

**Ændringer i Oracle Linux 7**

Forberede en Oracle Linux 7 virtuelt til Azure minder meget Oracle Linux 6, men der er flere vigtige forskelle værd at bemærke:

 - Både Red Hat kompatible kernen og Oracle-UEK3 understøttes i Azure.  UEK3 kernen anbefales.
 - Ikke længere er i konflikt med Azure Linux agent pakken NetworkManager. Denne pakke installeres som standard, og vi anbefaler, at den ikke er fjernet.
 - GRUB2 er nu bruges som standard bootloaderen, så fremgangsmåden for redigering kernen parametre er ændret (se nedenfor).
 - XFS er nu standard-filsystemet. Filsystemet ext4 kan stadig bruges, hvis du ønsker.


**Trin til konfiguration**

1. Vælg den virtuelle maskine i Hyper-V Manager.

2. Klik på **Opret forbindelse** for at åbne et vindue med console for den virtuelle maskine.

3.  Oprette en fil med navnet **netværk** i den `/etc/sysconfig/` mappe, der indeholder følgende tekst:

        NETWORKING=yes
        HOSTNAME=localhost.localdomain

4.  Oprette en fil med navnet **ifcfg eth0** i den `/etc/sysconfig/network-scripts/` mappe, der indeholder følgende tekst:

        DEVICE=eth0
        ONBOOT=yes
        BOOTPROTO=dhcp
        TYPE=Ethernet
        USERCTL=no
        PEERDNS=yes
        IPV6INIT=no

5.  Ændre udev regler for at undgå at generere statisk regler for Ethernet-grænseflader. Disse regler kan medføre problemer, når klone en virtuel maskine i Microsoft Azure eller Hyper-v

        # sudo ln -s /dev/null /etc/udev/rules.d/75-persistent-net-generator.rules

6. Sikre Netværkstjeneste begynder på starttidspunktet ved at køre følgende kommando:

        # sudo chkconfig network on

7. Installere pakken python pyasn1 ved at køre følgende kommando:

        # sudo yum install python-pyasn1

8.  Kør følgende kommando for at fjerne markeringen i de aktuelle yum metadata og installere opdateringer:

        # sudo yum clean all
        # sudo yum -y update

9.  Ændre start kernen linje i konfigurationen rydde medtage yderligere kernen parametre til Azure. Du gør dette, Åbn "/ osv/standard/rydde" i et tekstredigeringsprogram og rediger den `GRUB_CMDLINE_LINUX` parameter, for eksempel:

        GRUB_CMDLINE_LINUX="rootdelay=300 console=ttyS0 earlyprintk=ttyS0 net.ifnames=0"

    Dette sikrer også alle console meddelelser sendes til den første serielle port, som kan hjælpe Azure support med fejlfinding af problemer. Det også markeres, deaktiveres de nye OEL 7 navngivningskonventioner for netværkskort. Ud over ovenstående anbefales det at *fjerne* følgende parametre:

        rhgb quiet crashkernel=auto

    Grafiske og stille Start ikke er nyttig i et skybaseret miljø, hvor vi vil alle logfilerne, der skal sendes til den serielle port.

    Den `crashkernel` indstilling kan være konfigureret eventuelt venstre, men Bemærk, at denne parameter kan reducere mængden af tilgængelig hukommelse i VM 128MB eller derover, som kan være problematisk på mindre VM størrelsen.


10. Når du er færdig med redigering "/ osv/standard/rydde" per ovenstående køre følgende kommando for at genopbygge den rydde konfiguration:

        # sudo grub2-mkconfig -o /boot/grub2/grub.cfg

11. Sørg for, at SSH serveren er installeret og konfigureret til at starte på starttidspunktet.  Det er normalt standard.

12. Installer Azure Linux Agent ved at køre følgende kommando:

        # sudo yum install WALinuxAgent
        # sudo systemctl enable waagent

13. Opret ikke mellemrum Byt om på OS disken.

    Azure Linux Agent kan automatisk konfigurere Udskift mellemrum ved hjælp af den lokale ressourcer disk, der er knyttet til VM efter klargøring af på Azure. Bemærk, at lokal ressource disken er en *midlertidig* disk, og kan være blevet tømt når VM er fjernet. Når du har installeret Azure Linux Agent (se det foregående trin) ændre følgende parametre i /etc/waagent.conf korrekt:

        ResourceDisk.Format=y
        ResourceDisk.Filesystem=ext4
        ResourceDisk.MountPoint=/mnt/resource
        ResourceDisk.EnableSwap=y
        ResourceDisk.SwapSizeMB=2048    ## NOTE: set this to whatever you need it to be.

14. Kør følgende kommandoer til deprovision den virtuelle maskine og forberede klargøring af Azure:

        # sudo waagent -force -deprovision
        # export HISTSIZE=0
        # logout

15. Klik på- **handling -> Luk ned** i Hyper-V Manager. Din Linux VHD er nu klar til at blive overført til Azure.


## <a name="next-steps"></a>Næste trin
Du er nu klar til at bruge din Oracle Linux .vhd til at oprette nye virtuelle maskiner i Azure. Hvis dette er første gang, du overfører .vhd-filen til Azure, kan du se trin 2 og 3 i [oprette og overføre en virtuel harddisk, der indeholder Linux-operativsystemet](virtual-machines-linux-classic-create-upload-vhd.md).
