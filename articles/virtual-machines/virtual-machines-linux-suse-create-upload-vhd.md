<properties
    pageTitle="Oprette og overføre en virtuel harddisk SUSE Linux i Azure"
    description="Lær at oprette og overføre en Azure virtuel harddisk (Virtuelle), der indeholder en SUSE Linux-operativsystemet."
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

# <a name="prepare-a-sles-or-opensuse-virtual-machine-for-azure"></a>Forberede en SLES eller openSUSE virtuel maskine til Azure

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-both-include.md)]

## <a name="prerequisites"></a>Forudsætninger ##

Denne artikel forudsætter, at du allerede har installeret en SUSE eller openSUSE Linux-operativsystemet til en virtuel harddisk. Der findes flere værktøjer for at oprette .vhd filer, for eksempel en virtualization løsning som Hyper-V. Flere oplysninger under [installere Hyper-V-rollen og konfigurere en virtuel maskine](http://technet.microsoft.com/library/hh846766.aspx).

### <a name="sles--opensuse-installation-notes"></a>SLES / openSUSE installationsnoter

- Se også [Generelle Linux Installationsnoter](virtual-machines-linux-create-upload-generic.md#general-linux-installation-notes) flere tip til forberedelse Linux til Azure.

- Formatet VHDX understøttes ikke i Azure, kun **fast Virtuelle**.  Du kan konvertere disken Virtuelle formatet ved hjælp af Hyper-V Manager eller Konverter virtuelle-cmdlet.

- Det anbefales, at du bruger standard partitioner i stedet for LVM (ofte standard for mange installationer), når du installerer Linux systemet. Derved undgår LVM navnet er i konflikt med klonede FOS, især hvis en OS disk overhovedet skal knyttes til en anden VM til fejlfinding. [LVM](virtual-machines-linux-configure-lvm.md) eller [RAID](virtual-machines-linux-configure-raid.md) kan bruges på datadisce Hvis Foretrukne.

- Konfigurer ikke en partition Byt om på OS disken. Linux agent kan konfigureres til at oprette filen Byt om på disken midlertidige ressource.  Kan finde flere oplysninger om dette i nedenstående fremgangsmåde.

- Alle de virtuelle harddiske skal have størrelser, multiplum af 1 MB.


## <a name="use-suse-studio"></a>Brug SUSE Studio
[SUSE Studio](http://www.susestudio.com) kan nemt oprette og administrere dine SLES og openSUSE billeder til Azure og Hyper-V. Dette er den anbefalede fremgangsmåde for at tilpasse din egen SLES og openSUSE billeder.

Som et alternativ til at lave din egen Virtuelle publicerer SUSE BYOS (tage dine egne abonnement) billeder til SLES på [VMDepot](https://vmdepot.msopentech.com/User/Show?user=1007).


## <a name="prepare-suse-linux-enterprise-server-11-sp4"></a>Forberede SUSE Linux Enterprise Server 11 SP4 ##

1. Vælg den virtuelle maskine i den midterste rude af Hyper-V Manager.

2. Klik på **Opret forbindelse** for at åbne vinduet for den virtuelle maskine.

3. Registrere SUSE Linux Enterprise systemet for at lade det Hent og Installer pakker opdateringer.

4. Opdatere systemet med de seneste rettelser:

        # sudo zypper update

5. Installer Azure Linux Agent fra SLES lager:

        # sudo zypper install WALinuxAgent

6. Kontrollér Hvis waagent er indstillet til "til" i chkconfig, og hvis ikke, skal du aktivere den til starte:
               
        # sudo chkconfig waagent on

7. Markér, hvis waagent tjenesten kører, og hvis ikke, starte den: 

        # sudo service waagent start
                
8. Ændre start kernen linje i konfigurationen rydde medtage yderligere kernen parametre til Azure. Gøre denne Åbn "/ boot/grub/menu.lst" i et tekstredigeringsprogram, og Sørg for, at standard kernen omfatter følgende parametre:

        console=ttyS0 earlyprintk=ttyS0 rootdelay=300

    Dette sikrer alle console meddelelser sendes til den første serielle port, som kan hjælpe Azure support med fejlfinding af problemer.

9. Bekræft, at /boot/grub/menu.lst og /etc/fstab referencer begge disken med dens UUID (ved uuid) i stedet for disk-ID (ved-id). 

    Få disk UUID
    
        # ls /dev/disk/by-uuid/

    Hvis /dev/disk/by-id / er brugt, opdateres både /boot/grub/menu.lst og/osv/fstab med den korrekte ved uuid værdi

    Før ændring
    
        root=/dev/disk/by-id/SCSI-xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxxx-part1

    Efter ændring
    
        root=/dev/disk/by-uuid/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx

10. Ændre udev regler for at undgå at generere statisk regler for Ethernet-grænseflader. Disse regler kan medføre problemer, når klone en virtuel maskine i Microsoft Azure eller Hyper-v

        # sudo ln -s /dev/null /etc/udev/rules.d/75-persistent-net-generator.rules
        # sudo rm -f /etc/udev/rules.d/70-persistent-net.rules

11. Det er anbefalet til at redigere filen "/ osv/sysconfig/netværk/dhcp" og ændre den `DHCLIENT_SET_HOSTNAME` -parameter til følgende:

        DHCLIENT_SET_HOSTNAME="no"

12. Skrive en kommentar ud i "/ osv/sudoers", eller fjern følgende linjer, hvis de findes:

        Defaults targetpw   # ask for the password of the target user i.e. root
        ALL    ALL=(ALL) ALL   # WARNING! Only use this together with 'Defaults targetpw'!

13. Sørg for, at SSH serveren er installeret og konfigureret til at starte på starttidspunktet.  Det er normalt standard.

14. Opret ikke mellemrum Byt om på OS disken.

    Azure Linux Agent kan automatisk konfigurere Udskift mellemrum ved hjælp af den lokale ressourcer disk, der er knyttet til VM efter klargøring af på Azure. Bemærk, at lokal ressource disken er en *midlertidig* disk, og kan være blevet tømt når VM er fjernet. Når du har installeret Azure Linux Agent (se forrige trin) ændre følgende parametre i /etc/waagent.conf korrekt:

        ResourceDisk.Format=y
        ResourceDisk.Filesystem=ext4
        ResourceDisk.MountPoint=/mnt/resource
        ResourceDisk.EnableSwap=y
        ResourceDisk.SwapSizeMB=2048    ## NOTE: set this to whatever you need it to be.

15. Kør følgende kommandoer til deprovision den virtuelle maskine og forberede klargøring af Azure:

        # sudo waagent -force -deprovision
        # export HISTSIZE=0
        # logout

16. Klik på- **handling -> Luk ned** i Hyper-V Manager. Din Linux VHD er nu klar til at blive overført til Azure.


----------

## <a name="prepare-opensuse-131"></a>Forberede openSUSE 13.1 + ##

1. Vælg den virtuelle maskine i den midterste rude af Hyper-V Manager.

2. Klik på **Opret forbindelse** for at åbne vinduet for den virtuelle maskine.

3. På shell'en, skal du køre kommandoen '`zypper lr`'. Hvis denne kommando returnerer output stil med følgende og derefter typer lagre er konfigureret som forventet – ingen ændringer (Bemærk, at version tal kan variere):

        # | Alias                 | Name                  | Enabled | Refresh
        --+-----------------------+-----------------------+---------+--------
        1 | Cloud:Tools_13.1      | Cloud:Tools_13.1      | Yes     | Yes
        2 | openSUSE_13.1_OSS     | openSUSE_13.1_OSS     | Yes     | Yes
        3 | openSUSE_13.1_Updates | openSUSE_13.1_Updates | Yes     | Yes

    Hvis kommandoen returnerer "Ingen typer lagre defineret..." derefter bruge følgende kommandoer til at tilføje disse repos:

        # sudo zypper ar -f http://download.opensuse.org/repositories/Cloud:Tools/openSUSE_13.1 Cloud:Tools_13.1
        # sudo zypper ar -f http://download.opensuse.org/distribution/13.1/repo/oss openSUSE_13.1_OSS
        # sudo zypper ar -f http://download.opensuse.org/update/13.1 openSUSE_13.1_Updates

    Derefter kan du kontrollere typer lagre er blevet føjet ved at køre kommandoen '`zypper lr`' igen. I tilfælde af en af de relevante Opdater typer lagre ikke er aktiveret, kan du aktivere tilstanden med følgende kommando:

        # sudo zypper mr -e [NUMBER OF REPOSITORY]


4. Opdatere kernen til den nyeste version:

        # sudo zypper up kernel-default

    Eller opdatere systemet med de seneste rettelser:

        # sudo zypper update

5.  Installer Azure Linux-Agent.

        # sudo zypper install WALinuxAgent

6.  Ændre start kernen linje i konfigurationen rydde medtage yderligere kernen parametre til Azure. Du kan åbne "/ boot/grub/menu.lst" i et tekstredigeringsprogram, og Sørg for, at standard kernen omfatter følgende parametre:

        console=ttyS0 earlyprintk=ttyS0 rootdelay=300

    Dette sikrer alle console meddelelser sendes til den første serielle port, som kan hjælpe Azure support med fejlfinding af problemer. Derudover kan fjerne følgende parametre fra kernen Start linje, hvis de findes:

        libata.atapi_enabled=0 reserve=0x1f0,0x8

7.  Det er anbefalet til at redigere filen "/ osv/sysconfig/netværk/dhcp" og ændre den `DHCLIENT_SET_HOSTNAME` -parameter til følgende:

        DHCLIENT_SET_HOSTNAME="no"

8.  **Vigtige:** Skrive en kommentar ud i "/ osv/sudoers", eller fjern følgende linjer, hvis de findes:

        Defaults targetpw   # ask for the password of the target user i.e. root
        ALL    ALL=(ALL) ALL   # WARNING! Only use this together with 'Defaults targetpw'!

9.  Sørg for, at SSH serveren er installeret og konfigureret til at starte på starttidspunktet.  Det er normalt standard.

10. Opret ikke mellemrum Byt om på OS disken.

    Azure Linux Agent kan automatisk konfigurere Udskift mellemrum ved hjælp af den lokale ressourcer disk, der er knyttet til VM efter klargøring af på Azure. Bemærk, at lokal ressource disken er en *midlertidig* disk, og kan være blevet tømt når VM er fjernet. Når du har installeret Azure Linux Agent (se forrige trin) ændre følgende parametre i /etc/waagent.conf korrekt:

        ResourceDisk.Format=y
        ResourceDisk.Filesystem=ext4
        ResourceDisk.MountPoint=/mnt/resource
        ResourceDisk.EnableSwap=y
        ResourceDisk.SwapSizeMB=2048    ## NOTE: set this to whatever you need it to be.

11. Kør følgende kommandoer til deprovision den virtuelle maskine og forberede klargøring af Azure:

        # sudo waagent -force -deprovision
        # export HISTSIZE=0
        # logout

12. Sikre Azure Linux Agent kører ved start:

        # sudo systemctl enable waagent.service

13. Klik på- **handling -> Luk ned** i Hyper-V Manager. Din Linux VHD er nu klar til at blive overført til Azure.

## <a name="next-steps"></a>Næste trin
Nu er du klar til brug harddisken SUSE Linux virtuelle til at oprette nye virtuelle maskiner i Azure. Hvis dette er første gang, du overfører .vhd-filen til Azure, kan du se trin 2 og 3 i [oprette og overføre en virtuel harddisk, der indeholder Linux-operativsystemet](virtual-machines-linux-classic-create-upload-vhd.md).
