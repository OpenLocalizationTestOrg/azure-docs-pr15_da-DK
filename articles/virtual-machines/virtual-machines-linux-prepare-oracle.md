<properties
pageTitle="Forberede en Oracle Linux Virtual Machine til Azure | Microsoft Azure"
description="Trinvise konfiguration af en Oracle-virtuelt kører Linux i Microsoft Azure."
services="virtual-machines-linux"
authors="bbenz"
manager="timlt"
documentationCenter="virtual-machines"
tags="azure-service-management,azure-resource-manager"
/>

<tags
ms.service="virtual-machines-linux"
ms.devlang="na"
ms.topic="article"
ms.tgt_pltfrm="vm-linux"
ms.workload="infrastructure-services"
ms.date="06/22/2015"
ms.author="bbenz" />

# <a name="prepare-an-oracle-linux-virtual-machine-for-azure"></a>Forberede en Oracle Linux virtuelt til Azure

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-both-include.md)]


-   [Forberede en Oracle Linux 6.4 + virtuelt til Azure](virtual-machines-linux-oracle-create-upload-vhd.md)

-   [Forberede en Oracle Linux 7.0 + virtuelt til Azure](virtual-machines-linux-oracle-create-upload-vhd.md)

## <a name="prerequisites"></a>Forudsætninger
Denne artikel forudsætter, at du allerede har installeret en Oracle Linux-operativsystemet til en virtuel harddisk. Der findes flere værktøjer for at oprette .vhd filer, for eksempel en virtualization løsning som Hyper-V. Flere oplysninger under [installere Hyper-V og oprette et virtuelt](http://technet.microsoft.com/library/hh846766.aspx).

**Oracle Linux installationsnoter**

- Oracle-Red Hat kompatible kerne og dens UEK3 (Unbreakable Enterprise kerne) understøttes både på Hyper-V og Azure. Du opnår de bedste resultater, skal du sørge for at opdatere til den seneste kernen under forberedelse af din Oracle Linux Virtuelle.

- Oracle-UEK2 understøttes ikke på Hyper-V og Azure, som ikke omfatter de nødvendige drivere.

- Det nyere VHDX format understøttes ikke i Azure. Du kan konvertere disken til Virtuelle format ved hjælp af Hyper-V Manager eller Konverter virtuelle cmdlet.

- Når du installerer Linux-system, anbefaler vi, at du bruger standard partitioner i stedet for LVM (ofte standard for mange installationer). Derved undgår LVM navnet er i konflikt med klonede FOS, især hvis en OS disk overhovedet skal knyttes til en anden VM til fejlfinding. LVM eller [RAID](virtual-machines-linux-configure-raid.md) kan bruges på datadisce Hvis Foretrukne.

- NUMA understøttes ikke af VM større på grund af en fejl i Linux kerneversioner under 2.6.37. Dette problem påvirker primært salgsdistributioner, der bruger den foregående Red Hat 2.6.32 kerne. Manuel installation af Azure Linux agent (waagent) deaktiveres automatisk NUMA i sektionen rydde konfiguration for Linux kernen. Kan finde flere oplysninger om dette i nedenstående fremgangsmåde.

- Konfigurer ikke en partition Byt om på OS disken. Linux agent kan konfigureres til at oprette filen Byt om på disken midlertidige ressource. Kan finde flere oplysninger om dette i nedenstående fremgangsmåde.

- Alle de virtuelle harddiske skal have størrelser, multiplum af 1 MB.

- Sørg for, at den `Addons` lager er aktiveret. Vælg for at redigere filen `/etc/yum.repo.d/public-yum-ol6.repo`(Oracle Linux 6) eller `/etc/yum.repo.d/public-yum-ol7.repo`(Oracle Linux), og ret linjen `enabled=0` til `enabled=1` under **[ol6_addons]** eller **[ol7_addons]** i filen.


## <a name="oracle-linux-64"></a>Oracle Linux 6.4 +
Du skal fuldføre bestemte konfigurationstrinnene i operativsystemet til den virtuelle maskine til at køre i Azure.

1. Vælg den virtuelle maskine i den midterste rude af Hyper-V Manager.

2. Klik på **Opret forbindelse** for at åbne vinduet for den virtuelle maskine.

3. Fjerne NetworkManager ved at køre følgende kommando:

        # sudo rpm -e --nodeps NetworkManager

    >[AZURE.NOTE] Hvis pakken ikke allerede er installeret, mislykkes denne kommando med en fejlmeddelelse. Dette er forventet.

4. Oprette en fil med navnet **netværk** i den /etc/sysconfig eller den mappe, der indeholder følgende tekst:

    `NETWORKING=yes`  
    `HOSTNAME=localhost.localdomain`

5.  Oprette en fil med navnet **ifcfg eth0** i /etc/sysconfig/network-scripts / mappe, der indeholder følgende tekst:

        DEVICE=eth0
        ONBOOT=yes
        BOOTPROTO=dhcp
            TYPE=Ethernet
            USERCTL=no
            PEERDNS=yes
        IPV6INIT=no

6.  Flytte (eller fjerne) udev regler til at undgå at generere statisk regler for grænsefladen Ethernet. Disse regler forårsage problemer, når du klone en virtuel maskine i Azure eller Hyper-v

        # sudo mkdir -m 0700 /var/lib/waagent
        # sudo mv /lib/udev/rules.d/75-persistent-net-generator.rules /var/lib/waagent/ 2\>/dev/null
        # sudo mv /etc/udev/rules.d/70-persistent-net.rules /var/lib/waagent/ 2\>/dev/null

7.  Sørg for, at netværkstjenesten begynder på starttidspunktet ved at køre følgende kommando:

        # chkconfig network on

8.  Installere python pyasn1 ved at køre følgende kommando:

        # sudo yum install python-pyasn1

9.  Ændre start kernen linje i konfigurationen rydde medtage yderligere kernen parametre til Azure. Du kan åbne "/ boot/grub/menu.lst" i et tekstredigeringsprogram, og Sørg for, at standard kernen omfatter følgende parametre:

        console=ttyS0 earlyprintk=ttyS0 rootdelay=300 numa=off

    Dette sikrer også, at alle console meddelelser sendes til den første serielle port, som kan hjælpe Azure support med fejlfinding af problemer. Dette vil deaktivere NUMA på grund af en fejl i Oracle-Red Hat kompatible kerne.

    Ud over ovenstående, anbefaler vi, at du *fjerne* følgende parametre:

        rhgb quiet crashkernel=auto

    Grafiske og stille Start ikke er nyttig i et skybaseret miljø, hvor vi vil alle logfilerne, der skal sendes til den serielle port.

    Den `crashkernel` indstilling kan være konfigureret eventuelt venstre, men Bemærk, at denne parameter kan reducere mængden af tilgængelig hukommelse i VM ved 128 MB eller mere. Det kan være problematisk på mindre VM størrelsen.

10.  Sørg for, at SSH serveren er installeret og konfigureret til at starte på starttidspunktet. Det er normalt standard.

11.  Installer Azure Linux Agent ved at køre følgende kommando:

        # <a name="sudo-yum-install-walinuxagent"></a>sudo yum Installer WALinuxAgent

    Bemærk, at du installerer pakken WALinuxAgent, fjerner du NetworkManager og NetworkManager gnome pakker Hvis de ikke blev allerede fjernet som beskrevet i trin 2.

12.  Opret ikke mellemrum Byt om på OS disken.

    Azure Linux Agent kan automatisk konfigurere Udskift mellemrum ved hjælp af den lokale ressourcer disk, der er knyttet til VM efter klargøring af på Azure. Bemærk, at lokale ressourcer disken er en *midlertidig* disk, og det kan være blevet tømt når VM er fjernet. Når du har installeret Azure Linux Agent (se forrige trin) ændre følgende parametre i /etc/waagent.conf korrekt:

        ResourceDisk.Format=y

        ResourceDisk.Filesystem=ext4

        ResourceDisk.MountPoint=/mnt/resource

        ResourceDisk.EnableSwap=y

        ResourceDisk.SwapSizeMB=2048 ## NOTE: Angiv denne indstilling til hvad du vil have det skal være.

13.  Kør følgende kommandoer til deprovision den virtuelle maskine og forberede klargøring af Azure:

        # <a name="sudo-waagent--force--deprovision"></a>sudo waagent-gennemtvinge - deprovision
        # <a name="export-histsize0"></a>eksportere HISTSIZE = 0
        # <a name="logout"></a>Log

14.  Klik på **Action -\> lukke** i Hyper-V Manager. Din Linux VHD er nu klar til at blive overført til Azure.

## <a name="oracle-linux-70"></a>Oracle Linux 7.0 +
**Ændringer i Oracle Linux 7**

Forberede en Oracle Linux 7 virtuelt til Azure minder meget processen for Oracle Linux 6. Der er dog flere vigtige forskelle værd at bemærke:

-   Både Red Hat kompatible kernen og Oracle-UEK3 understøttes i Azure. Vi anbefaler UEK3 kernen.

-   Ikke længere er i konflikt med Azure Linux agent pakken NetworkManager. Denne pakke installeres som standard, og vi anbefaler, at den ikke er fjernet.

-   GRUB2 er nu bruges som standard bootloaderen, så fremgangsmåden for redigering kernen parametre er ændret (se nedenfor).

-   XFS er nu standard-filsystemet. Filsystemet ext4 kan stadig bruges, hvis du ønsker.

**Trin til konfiguration**

1.  Vælg den virtuelle maskine i Hyper-V Manager.

2.  Klik på **Opret forbindelse** for at åbne et vindue med console for den virtuelle maskine.

3.  Oprette en fil med navnet **netværk** i den /etc/sysconfig eller den mappe, der indeholder følgende tekst:

        NETWORKING=yes
        HOSTNAME=localhost.localdomain

4.  Oprette en fil med navnet **ifcfg eth0** i /etc/sysconfig/network-scripts / mappe, der indeholder følgende tekst:

        DEVICE=eth0
        ONBOOT=yes
        BOOTPROTO=dhcp
        TYPE=Ethernet
        USERCTL=no
            PEERDNS=yes
        IPV6INIT=no

5.  Flytte (eller fjerne) udev regler til at undgå at generere statisk regler for grænsefladen Ethernet. Disse regler forårsage problemer, når du klone en virtuel maskine i Microsoft Azure eller Hyper-V.

        # sudo mkdir -m 0700 /var/lib/waagent
        # sudo mv /lib/udev/rules.d/75-persistent-net-generator.rules /var/lib/waagent/ 2>/dev/null
        # sudo mv /etc/udev/rules.d/70-persistent-net.rules /var/lib/waagent/ 2>/dev/null

6.  Sørg for, at netværkstjenesten begynder på starttidspunktet ved at køre følgende kommando:

        # sudo chkconfig network on

7.  Installere pakken python pyasn1 ved at køre følgende kommando:

        # sudo yum install python-pyasn1

8.  Kør følgende kommando for at fjerne markeringen i de aktuelle yum metadata og installere opdateringer:

        # sudo yum clean all
        # sudo yum -y update

9.  Ændre start kernen linje i konfigurationen rydde medtage yderligere kernen parametre til Azure. Sådan gør du åbne "/ osv/standard/rydde" i et tekstredigeringsprogram og rediger rydde\_CMDLINE\_LINUX parameter, for eksempel:

        GRUB\_CMDLINE\_LINUX="rootdelay=300 console=ttyS0 earlyprintk=ttyS0"

    Dette sikrer også alle console meddelelser sendes til den første serielle port, som kan hjælpe Azure support med fejlfinding af problemer. Ud over ovenstående, anbefaler vi, at du *fjerne* følgende parametre:

        rhgb quiet crashkernel=auto

    Grafiske og stille Start ikke er nyttig i et skybaseret miljø, hvor vi vil alle logfilerne, der skal sendes til den serielle port.

    Den `crashkernel` indstilling kan være konfigureret eventuelt venstre, men Bemærk, at denne parameter kan reducere mængden af tilgængelig hukommelse i VM ved 128 MB eller mere. Det kan være problematisk på mindre VM størrelsen.

10.  Når du er færdig med redigering "/ osv/standard/rydde", køre følgende kommando for at genopbygge den rydde konfiguration:

        # <a name="sudo-grub2-mkconfig--o-bootgrub2grubcfg"></a>sudo grub2 mkconfig - o /boot/grub2/grub.cfg

11.  Sørg for, at SSH serveren er installeret og konfigureret til at starte på starttidspunktet. Det er normalt standard.

12.  Installer Azure Linux Agent ved at køre følgende kommando:

        # <a name="sudo-yum-install-walinuxagent"></a>sudo yum Installer WALinuxAgent

13.  Opret ikke mellemrum Byt om på OS disken.

    Azure Linux Agent kan automatisk konfigurere Udskift mellemrum ved hjælp af den lokale ressourcer disk, der er knyttet til VM efter klargøring af på Azure. Bemærk, at lokale ressourcer disken er en *midlertidig* disk, og det kan være blevet tømt når VM er fjernet. Når du har installeret Azure Linux Agent (se forrige trin) ændre følgende parametre i /etc/waagent.conf korrekt:

        ResourceDisk.Format=y ResourceDisk.Filesystem=ext4 ResourceDisk.MountPoint=/mnt/resource ResourceDisk.EnableSwap=y ResourceDisk.SwapSizeMB=2048 ## NOTE: Angiv denne indstilling til hvad du vil have det skal være.

14.  Kør følgende kommandoer til deprovision den virtuelle maskine og forberede klargøring af Azure:

        # <a name="sudo-waagent--force--deprovision"></a>sudo waagent-gennemtvinge - deprovision
        # <a name="export-histsize0"></a>eksportere HISTSIZE = 0
        # <a name="logout"></a>Log

15.  Klik på **Action -\> lukke** i Hyper-V Manager. Din Linux VHD er nu klar til at blive overført til Azure.
