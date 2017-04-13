<properties
    pageTitle="Oprette og overføre en CentOS-baserede Linux VHD i Azure"
    description="Lær at oprette og overføre en Azure virtuel harddisk (Virtuelle), der indeholder en CentOS-baserede Linux-operativsystemet."
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
    ms.date="05/09/2016"
    ms.author="szarkos"/>

# <a name="prepare-a-centos-based-virtual-machine-for-azure"></a>Forberede en CentOS-baseret virtuel maskine til Azure

- [Forberede en CentOS 6 virtuel maskine til Azure](#centos-6x)
- [Forberede en CentOS 7.0 + virtuel maskine til Azure](#centos-70)

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-both-include.md)]

## <a name="prerequisites"></a>Forudsætninger ##

Denne artikel antages det, at du allerede har installeret en CentOS (eller lignende afledning) Linux-operativsystemet til en virtuel harddisk. Der findes flere værktøjer for at oprette .vhd filer, for eksempel en virtualization løsning som Hyper-V. Flere oplysninger under [installere Hyper-V-rollen og konfigurere en virtuel maskine](http://technet.microsoft.com/library/hh846766.aspx).


**CentOS installationsnoter**

- Se også [Generelle Linux Installationsnoter](virtual-machines-linux-create-upload-generic.md#general-linux-installation-notes) flere tip til forberedelse Linux til Azure.

- Formatet VHDX understøttes ikke i Azure, kun **fast Virtuelle**.  Du kan konvertere disken Virtuelle formatet ved hjælp af Hyper-V Manager eller Konverter virtuelle-cmdlet.

- Det anbefales, at du bruger standard partitioner i stedet for LVM (ofte standard for mange installationer), når du installerer Linux systemet. Derved undgår LVM navnet er i konflikt med klonede FOS, især hvis en OS disk overhovedet skal knyttes til en anden VM til fejlfinding.  [LVM](virtual-machines-linux-configure-lvm.md) eller [RAID](virtual-machines-linux-configure-raid.md) kan bruges på datadisce Hvis Foretrukne.

- NUMA understøttes ikke af VM større på grund af en fejl i Linux kerneversioner under 2.6.37. Dette problem påvirker primært distribution af den foregående Red Hat 2.6.32 kerne. Manuel installation af Azure Linux agent (waagent) deaktiveres automatisk NUMA i sektionen rydde konfiguration for Linux kernen. Kan finde flere oplysninger om dette i nedenstående fremgangsmåde.

- Konfigurer ikke en partition Byt om på OS disken. Linux agent kan konfigureres til at oprette filen Byt om på disken midlertidige ressource.  Kan finde flere oplysninger om dette i nedenstående fremgangsmåde.

- Alle de virtuelle harddiske skal have størrelser, multiplum af 1 MB.


## <a name="centos-6x"></a>CentOS 6 ##

1. Vælg den virtuelle maskine i Hyper-V Manager.

2. Klik på **Opret forbindelse** for at åbne et vindue med console for den virtuelle maskine.

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

        # sudo chkconfig network on


8. **Kun centOS 6.3**: installere drivere til Linux Integration Services (LIS).

    **Vigtigt: Trinnet er kun gyldige for CentOS 6.3 og tidligere versioner.**  I CentOS 6.4 + findes Linux Integration Services *allerede i standard kernen*.

    - Følg installationsanvisningerne på siden [LIS overførselssiden](https://www.microsoft.com/en-us/download/details.aspx?id=46842) og installere RPM på dit billede.  


9. Installere pakken python pyasn1 ved at køre følgende kommando:

        # sudo yum install python-pyasn1

10. Hvis du vil bruge de OpenLogic spejle, der er placeret i Azure datacentre, Erstat derefter filen /etc/yum.repos.d/CentOS-Base.repo med følgende typer lagre.  Dette kan også tilføje **[openlogic]** -lager, der indeholder pakker til Azure Linux agent:

        [openlogic]
        name=CentOS-$releasever - openlogic packages for $basearch
        baseurl=http://olcentgbl.trafficmanager.net/openlogic/$releasever/openlogic/$basearch/
        enabled=1
        gpgcheck=0

        [base]
        name=CentOS-$releasever - Base
        baseurl=http://olcentgbl.trafficmanager.net/centos/$releasever/os/$basearch/
        gpgcheck=1
        gpgkey=file:///etc/pki/rpm-gpg/RPM-GPG-KEY-CentOS-6

        #released updates
        [updates]
        name=CentOS-$releasever - Updates
        baseurl=http://olcentgbl.trafficmanager.net/centos/$releasever/updates/$basearch/
        gpgcheck=1
        gpgkey=file:///etc/pki/rpm-gpg/RPM-GPG-KEY-CentOS-6

        #additional packages that may be useful
        [extras]
        name=CentOS-$releasever - Extras
        baseurl=http://olcentgbl.trafficmanager.net/centos/$releasever/extras/$basearch/
        gpgcheck=1
        gpgkey=file:///etc/pki/rpm-gpg/RPM-GPG-KEY-CentOS-6

        #additional packages that extend functionality of existing packages
        [centosplus]
        name=CentOS-$releasever - Plus
        baseurl=http://olcentgbl.trafficmanager.net/centos/$releasever/centosplus/$basearch/
        gpgcheck=1
        enabled=0
        gpgkey=file:///etc/pki/rpm-gpg/RPM-GPG-KEY-CentOS-6

        #contrib - packages by Centos Users
        [contrib]
        name=CentOS-$releasever - Contrib
        baseurl=http://olcentgbl.trafficmanager.net/centos/$releasever/contrib/$basearch/
        gpgcheck=1
        enabled=0
        gpgkey=file:///etc/pki/rpm-gpg/RPM-GPG-KEY-CentOS-6

    **Note:** Resten af denne vejledning vil forudsætter, at du bruger mindst repo [openlogic], som bruges til at installere den Azure Linux agent nedenfor.


11. Du kan tilføje følgende linje i /etc/yum.conf:

        http_caching=packages

    Og **i CentOS 6.3 kun** tilføje følgende linje:

        exclude=kernel*

12. Deaktivere modulet yum "fastestmirror" ved at redigere filen "/ etc/yum/pluginconf.d/fastestmirror.conf", og under `[main]`, skrive følgende:

        set enabled=0

13. Kør følgende kommando for at fjerne markeringen i de aktuelle yum metadata:

        # yum clean all

14. **På CentOS 6.3 kun**opdatere kernen ved hjælp af følgende kommando:

        # sudo yum --disableexcludes=all install kernel

15. Ændre start kernen linje i konfigurationen rydde medtage yderligere kernen parametre til Azure. Du kan åbne "/ boot/grub/menu.lst" i et tekstredigeringsprogram, og Sørg for, at standard kernen omfatter følgende parametre:

        console=ttyS0 earlyprintk=ttyS0 rootdelay=300 numa=off

    Dette sikrer også alle console meddelelser sendes til den første serielle port, som kan hjælpe Azure support med fejlfinding af problemer. Dette vil deaktivere NUMA på grund af en fejl i den kerneversion, der bruges af CentOS 6.

    Ud over ovenstående anbefales det at *fjerne* følgende parametre:

        rhgb quiet crashkernel=auto

    Grafiske og stille Start ikke er nyttig i et skybaseret miljø, hvor vi vil alle logfilerne, der skal sendes til den serielle port.

    Den `crashkernel` indstilling kan være konfigureret eventuelt venstre, men Bemærk, at denne parameter kan reducere mængden af tilgængelig hukommelse i VM 128MB eller derover, som kan være problematisk på mindre VM størrelsen.


16. Sørg for, at SSH serveren er installeret og konfigureret til at starte på starttidspunktet.  Det er normalt standard.

17. Installer Azure Linux Agent ved at køre følgende kommando:

        # sudo yum install WALinuxAgent

    Bemærk, at du installerer pakken WALinuxAgent, fjerner du NetworkManager og NetworkManager gnome pakker Hvis de ikke blev allerede fjernet som beskrevet i trin 2.

18. Opret ikke mellemrum Byt om på OS disken.

    Azure Linux Agent kan automatisk konfigurere Udskift mellemrum ved hjælp af den lokale ressourcer disk, der er knyttet til VM efter klargøring af på Azure. Bemærk, at lokal ressource disken er en *midlertidig* disk, og kan være blevet tømt når VM er fjernet. Når du har installeret Azure Linux Agent (se forrige trin) ændre følgende parametre i /etc/waagent.conf korrekt:

        ResourceDisk.Format=y
        ResourceDisk.Filesystem=ext4
        ResourceDisk.MountPoint=/mnt/resource
        ResourceDisk.EnableSwap=y
        ResourceDisk.SwapSizeMB=2048    ## NOTE: set this to whatever you need it to be.

19. Kør følgende kommandoer til deprovision den virtuelle maskine og forberede klargøring af Azure:

        # sudo waagent -force -deprovision
        # export HISTSIZE=0
        # logout

20. Klik på- **handling -> Luk ned** i Hyper-V Manager. Din Linux VHD er nu klar til at blive overført til Azure.


----------


## <a name="centos-70"></a>CentOS 7.0 + ##

**Ændringer i CentOS 7 (og afledte lignende produkter)**

Forberede en CentOS 7 virtuel maskine til Azure ligner meget CentOS 6, men der er flere vigtige forskelle værd at bemærke:

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

6.  Ændre udev regler for at undgå at generere statisk regler for Ethernet-grænseflader. Disse regler kan medføre problemer, når klone en virtuel maskine i Microsoft Azure eller Hyper-v

        # sudo ln -s /dev/null /etc/udev/rules.d/75-persistent-net-generator.rules

6. Sikre Netværkstjeneste begynder på starttidspunktet ved at køre følgende kommando:

        # sudo chkconfig network on

7. Installere pakken python pyasn1 ved at køre følgende kommando:

        # sudo yum install python-pyasn1

8. Hvis du vil bruge de OpenLogic spejle, der er placeret i Azure datacentre, Erstat derefter filen /etc/yum.repos.d/CentOS-Base.repo med følgende typer lagre.  Dette kan også tilføje **[openlogic]** -lager, der indeholder pakker til Azure Linux agent:

        [openlogic]
        name=CentOS-$releasever - openlogic packages for $basearch
        baseurl=http://olcentgbl.trafficmanager.net/openlogic/$releasever/openlogic/$basearch/
        enabled=1
        gpgcheck=0

        [base]
        name=CentOS-$releasever - Base
        baseurl=http://olcentgbl.trafficmanager.net/centos/$releasever/os/$basearch/
        gpgcheck=1
        gpgkey=file:///etc/pki/rpm-gpg/RPM-GPG-KEY-CentOS-7

        #released updates
        [updates]
        name=CentOS-$releasever - Updates
        baseurl=http://olcentgbl.trafficmanager.net/centos/$releasever/updates/$basearch/
        gpgcheck=1
        gpgkey=file:///etc/pki/rpm-gpg/RPM-GPG-KEY-CentOS-7

        #additional packages that may be useful
        [extras]
        name=CentOS-$releasever - Extras
        baseurl=http://olcentgbl.trafficmanager.net/centos/$releasever/extras/$basearch/
        gpgcheck=1
        gpgkey=file:///etc/pki/rpm-gpg/RPM-GPG-KEY-CentOS-7

        #additional packages that extend functionality of existing packages
        [centosplus]
        name=CentOS-$releasever - Plus
        baseurl=http://olcentgbl.trafficmanager.net/centos/$releasever/centosplus/$basearch/
        gpgcheck=1
        enabled=0
        gpgkey=file:///etc/pki/rpm-gpg/RPM-GPG-KEY-CentOS-7

        #contrib - packages by Centos Users
        [contrib]
        name=CentOS-$releasever - Contrib
        baseurl=http://olcentgbl.trafficmanager.net/centos/$releasever/contrib/$basearch/
        gpgcheck=1
        enabled=0
        gpgkey=file:///etc/pki/rpm-gpg/RPM-GPG-KEY-CentOS-7



    **Note:** Resten af denne vejledning vil forudsætter, at du bruger mindst repo [openlogic], som bruges til at installere den Azure Linux agent nedenfor.

9.  Kør følgende kommando for at fjerne markeringen i de aktuelle yum metadata og installere opdateringer:

        # sudo yum clean all
        # sudo yum -y update

10. Ændre start kernen linje i konfigurationen rydde medtage yderligere kernen parametre til Azure. Sådan gør du åbne "/ osv/standard/rydde" i et tekstredigeringsprogram og rediger den `GRUB_CMDLINE_LINUX` parameter, for eksempel:

        GRUB_CMDLINE_LINUX="rootdelay=300 console=ttyS0 earlyprintk=ttyS0 net.ifnames=0"

    Dette sikrer også alle console meddelelser sendes til den første serielle port, som kan hjælpe Azure support med fejlfinding af problemer. Det også markeres, deaktiveres de nye CentOS 7 navngivningskonventioner for netværkskort. Ud over ovenstående anbefales det at *fjerne* følgende parametre:

        rhgb quiet crashkernel=auto

    Grafiske og stille Start ikke er nyttig i et skybaseret miljø, hvor vi vil alle logfilerne, der skal sendes til den serielle port.

    Den `crashkernel` indstilling kan være konfigureret eventuelt venstre, men Bemærk, at denne parameter kan reducere mængden af tilgængelig hukommelse i VM 128MB eller derover, som kan være problematisk på mindre VM størrelsen.

11. Når du er færdig med redigering "/ osv/standard/rydde" per ovenstående køre følgende kommando for at genopbygge den rydde konfiguration:

        # sudo grub2-mkconfig -o /boot/grub2/grub.cfg

12. Sørg for, at SSH serveren er installeret og konfigureret til at starte på starttidspunktet.  Det er normalt standard.

13. **Kun, hvis opbygning af billedet fra VMWare, VirtualBox eller KVM:** Tilføje Hyper-V moduler til initramfs:

    Redigere `/etc/dracut.conf`, tilføje indhold:

        add_drivers+=”hv_vmbus hv_netvsc hv_storvsc”

    Genopbygge initramfs:

        # dracut –f -v

14. Installer Azure Linux Agent ved at køre følgende kommando:

        # sudo yum install WALinuxAgent
        # sudo systemctl enable waagent

15. Opret ikke mellemrum Byt om på OS disken.

    Azure Linux Agent kan automatisk konfigurere Udskift mellemrum ved hjælp af den lokale ressourcer disk, der er knyttet til VM efter klargøring af på Azure. Bemærk, at lokal ressource disken er en *midlertidig* disk, og kan være blevet tømt når VM er fjernet. Når du har installeret Azure Linux Agent (se forrige trin) ændre følgende parametre i /etc/waagent.conf korrekt:

        ResourceDisk.Format=y
        ResourceDisk.Filesystem=ext4
        ResourceDisk.MountPoint=/mnt/resource
        ResourceDisk.EnableSwap=y
        ResourceDisk.SwapSizeMB=2048    ## NOTE: set this to whatever you need it to be.

16. Kør følgende kommandoer til deprovision den virtuelle maskine og forberede klargøring af Azure:

        # sudo waagent -force -deprovision
        # export HISTSIZE=0
        # logout

17. Klik på- **handling -> Luk ned** i Hyper-V Manager. Din Linux VHD er nu klar til at blive overført til Azure.

## <a name="next-steps"></a>Næste trin
Nu er du klar til brug harddisken CentOS Linux virtuelle til at oprette nye virtuelle maskiner i Azure. Hvis dette er første gang, du overfører .vhd-filen til Azure, kan du se trin 2 og 3 i [oprette og overføre en virtuel harddisk, der indeholder Linux-operativsystemet](virtual-machines-linux-classic-create-upload-vhd.md).
