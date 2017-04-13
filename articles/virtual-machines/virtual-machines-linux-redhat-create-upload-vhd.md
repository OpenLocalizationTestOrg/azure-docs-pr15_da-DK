<properties
    pageTitle="Oprette og overføre en rød Hat Enterprise Linux Virtuelle til brug i Azure"
    description="Lær at oprette og overføre en Azure virtuel harddisk (Virtuelle), der indeholder et Red Hat Linux-operativsystem."
    services="virtual-machines-linux"
    documentationCenter=""
    authors="SuperScottz"
    manager="timlt"
    editor="tysonn"
    tags="azure-resource-manager,azure-service-management"/>

<tags
    ms.service="virtual-machines-linux"
    ms.workload="infrastructure-services"
    ms.tgt_pltfrm="vm-linux"
    ms.devlang="na"
    ms.topic="article"
    ms.date="02/17/2016"
    ms.author="mingzhan"/>


# <a name="prepare-a-red-hat-based-virtual-machine-for-azure"></a>Forberede en Red Hat-baseret virtuel maskine til Azure

I denne artikel lærer du, hvordan du forbereder en rød Hat Enterprise Linux (RHEL) virtuel maskine til brug i Azure. Versioner af RHEL, der er dækket i denne artikel er 6,7, 7.1 og 7.2. Hypervisors til forberedelse, der er dækket i denne artikel er Hyper-V, kerne-baserede Virtual Machine (KVM) og VMware. Se [Red Hat Skyadgang websted](http://www.redhat.com/en/technologies/cloud-computing/cloud-access) og [Kører RHEL på Azure](https://access.redhat.com/articles/1989673)kan finde flere oplysninger om krav skal man opfylde for at deltage i program til Red Hat Skyadgang.

[Forberede en RHEL 6,7 virtuel maskine fra Hyper-V Manager](#rhel67hyperv)

[Forberede en RHEL 7.1/7.2 virtuel maskine fra Hyper-V Manager](#rhel7xhyperv)

[Forberede en RHEL 6,7 virtuel maskine fra KVM](#rhel67kvm)

[Forberede en RHEL 7.1/7.2 virtuel maskine fra KVM](#rhel7xkvm)

[Forberede en RHEL 6,7 virtuel maskine fra VMware](#rhel67vmware)

[Forberede en RHEL 7.1/7.2 virtuel maskine fra VMware](#rhel7xvmware)

[Forberede en RHEL 7.1/7.2 virtuel maskine fra en kickstart-fil](#rhel7xkickstart)


## <a name="prepare-a-red-hat-based-virtual-machine-from-hyper-v-manager"></a>Forberede en Red Hat-baseret virtuel maskine fra Hyper-V Manager
### <a name="prerequisites"></a>Forudsætninger
Dette afsnit antages det, at du allerede har installeret et RHEL billede (fra en ISO-fil, du har hentet fra Red Hat websted) til en virtuel harddisk (Virtuelle). Få mere at vide om, hvordan du bruger Hyper-V Manager til at installere en afbildning af operativsystemet, kan du se [installere Hyper-V-rollen og konfigurere en virtuel maskine](http://technet.microsoft.com/library/hh846766.aspx).

**RHEL installationsnoter**

- Se også [Generelle Linux Installationsnoter](virtual-machines-linux-create-upload-generic.md#general-linux-installation-notes) flere tip til forberedelse Linux til Azure.

- Det nyere VHDX format understøttes ikke i Azure. Du kan konvertere disken til Virtuelle format ved hjælp af Hyper-V Manager eller **Konverter virtuelle** PowerShell-cmdlet.

- Virtuelle harddiske skal oprettes som "faste"--dynamiske virtuelle harddiske understøttes ikke.

- Når du installerer Linux-system, anbefaler vi, at du bruger standard partitioner i stedet for LVM (ofte standard for mange installationer). Derved undgår LVM navnet er i konflikt med klonede FOS, især hvis en OS disk overhovedet skal knyttes til en anden VM til fejlfinding. LVM eller [RAID](virtual-machines-linux-configure-raid.md) kan bruges på datadisce Hvis Foretrukne.

- Konfigurer ikke en partition Byt om på OS disken. Du kan konfigurere Linux agent for at oprette filen Byt om på disken midlertidige ressource. Yderligere oplysninger om dette er tilgængelig i nedenstående fremgangsmåde.

- Alle de virtuelle harddiske skal have størrelser, multiplum af 1 MB.

- Når du bruger **qemu img** konvertere diskbilleder til Virtuelle format, Bemærk, at der er et kendt fejl i qemu img versioner 2.2.1 eller nyere. Denne fejl resulterer i en forkert formateret Virtuelle. Problemet er beregnet på at rettes i en kommende version af qemu img. Nu skal vi anbefaler, at du bruger qemu-img version 2.2.0 eller tidligere.

### <a id="rhel67hyperv"> </a>Forberede en RHEL 6,7 virtuel maskine fra Hyper-V Manager###


1.  Vælg den virtuelle maskine i Hyper-V Manager.

2.  Klik på **Opret forbindelse** for at åbne et vindue med console for den virtuelle maskine.

3.  Fjerne NetworkManager ved at køre følgende kommando:

        # sudo rpm -e --nodeps NetworkManager

    Bemærk, at hvis pakken ikke allerede er installeret, mislykkes denne kommando med en fejlmeddelelse. Dette er forventet.

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

6.  Flytte (eller fjerne) udev regler til at undgå at generere statisk regler for grænsefladen Ethernet. Disse regler forårsage problemer, når du klone en virtuel maskine i Microsoft Azure eller Hyper-v

        # sudo mkdir -m 0700 /var/lib/waagent
        # sudo mv /lib/udev/rules.d/75-persistent-net-generator.rules /var/lib/waagent/
        # sudo mv /etc/udev/rules.d/70-persistent-net.rules /var/lib/waagent/

7.  Sørg for, at netværkstjenesten begynder på starttidspunktet ved at køre følgende kommando:

        # sudo chkconfig network on

8.  Registrere din Red Hat abonnement for at aktivere installationen af pakker fra RHEL lager ved at køre følgende kommando:

        # sudo subscription-manager register --auto-attach --username=XXX --password=XXX

9.  Pakken WALinuxAgent `WALinuxAgent-<version>` nogle til Red Hat ekstra lager. Aktivere ekstra lager ved at køre følgende kommando:

        # subscription-manager repos --enable=rhel-6-server-extras-rpms

10. Ændre start kernen linje i konfigurationen rydde medtage yderligere kernen parametre til Azure. Du kan åbne `/boot/grub/menu.lst` i et tekstredigeringsprogram, og Sørg for, at standard kernen omfatter følgende parametre:

        console=ttyS0
        earlyprintk=ttyS0
        rootdelay=300
        numa=off

    Dette sikrer også, at alle console meddelelser sendes til den første serielle port, som kan hjælpe Azure support med fejlfinding af problemer. Dette vil deaktivere NUMA på grund af en fejl i kernen-versionen, der bruges af RHEL 6.

    Ud over ovenstående handlingen anbefaler vi, at du fjerner følgende parametre:

        rhgb quiet crashkernel=auto

    Grafiske og stille Start ikke er nyttig i et skybaseret miljø, hvor vi vil alle logfilerne, der skal sendes til den serielle port.

    Indstillingen crashkernel kan være konfigureret eventuelt venstre, men Bemærk, at denne parameter kan reducere mængden af tilgængelig hukommelse i VM ved 128 MB eller mere. Det kan være problematisk på mindre VM størrelser.

11. Sørg for, at SSH serveren er installeret og konfigureret til at starte på starttidspunktet. Det er normalt standard. Ændre /etc/ssh/sshd_config for at medtage følgende linje:

        ClientAliveInterval 180

12. Installer Azure Linux Agent ved at køre følgende kommando:

        # sudo yum install WALinuxAgent
        # sudo chkconfig waagent on

    Bemærk, at du installerer pakken WALinuxAgent, fjerner du NetworkManager og NetworkManager gnome pakker Hvis de ikke blev allerede fjernet som beskrevet i trin 2.

13. Opret ikke mellemrum Byt om på OS disken.
Azure Linux Agent kan automatisk konfigurere Udskift mellemrum ved hjælp af den lokale ressource disk, der er knyttet til VM, efter VM er klargjort på Azure. Bemærk, at lokal ressource disken er en midlertidig disk, og kan være blevet tømt når VM er fjernet. Når du har installeret Azure Linux Agent (se det foregående trin) ændre følgende parametre i /etc/waagent.conf korrekt:

        ResourceDisk.Format=y
        ResourceDisk.Filesystem=ext4
        ResourceDisk.MountPoint=/mnt/resource
        ResourceDisk.EnableSwap=y
        ResourceDisk.SwapSizeMB=2048    ## NOTE: set this to whatever you need it to be.

14. Unregister abonnementet (hvis det er nødvendigt) ved at køre følgende kommando:

        # sudo subscription-manager unregister

15. Kør følgende kommandoer til deprovision den virtuelle maskine og forberede klargøring af Azure:

        # sudo waagent -force -deprovision
        # export HISTSIZE=0
        # logout

16. Klik på **handling > Luk** i Hyper-V Manager. Din Linux VHD er nu klar til at blive overført til Azure.
 

### <a id="rhel7xhyperv"> </a>Forberede en RHEL 7.1/7.2 virtuel maskine fra Hyper-V Manager###

1.  Vælg den virtuelle maskine i Hyper-V Manager.

2.  Klik på **Opret forbindelse** for at åbne et vindue med console for den virtuelle maskine.

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

5.  Sørg for, at netværkstjenesten begynder på starttidspunktet ved at køre følgende kommando:

        # sudo chkconfig network on

6.  Registrere din Red Hat abonnement for at aktivere installationen af pakker fra RHEL lager ved at køre følgende kommando:

        # sudo subscription-manager register --auto-attach --username=XXX --password=XXX

7.  Ændre start kernen linje i konfigurationen rydde medtage yderligere kernen parametre til Azure. Du kan åbne `/etc/default/grub` i et tekstredigeringsprogram og rediger parameteren **GRUB_CMDLINE_LINUX** . Eksempel:

        GRUB_CMDLINE_LINUX="rootdelay=300
        console=ttyS0
        earlyprintk=ttyS0"

    Dette sikrer også, at alle console meddelelser sendes til den første serielle port, som kan hjælpe Azure support med fejlfinding af problemer. Ud over ovenstående handlingen anbefaler vi, at du fjerner følgende parametre:

        rhgb quiet crashkernel=auto

    Grafiske og stille Start ikke er nyttig i et skybaseret miljø, hvor vi vil alle logfilerne, der skal sendes til den serielle port.
    Indstillingen crashkernel kan være konfigureret eventuelt venstre, men Bemærk, at denne parameter kan reducere mængden af tilgængelig hukommelse i VM ved 128 MB eller mere. Det kan være problematisk på mindre VM størrelser.

8.  Når du er færdig med redigering `/etc/default/grub`, køre følgende kommando for at genopbygge den rydde konfiguration:

        # sudo grub2-mkconfig -o /boot/grub2/grub.cfg

9.  Sørg for, at SSH serveren er installeret og konfigureret til at starte på starttidspunktet. Det er normalt standard. Ændre `/etc/ssh/sshd_config` medtage følgende linje:

        ClientAliveInterval 180

10. Pakken WALinuxAgent `WALinuxAgent-<version>` nogle til Red Hat ekstra lager. Aktivere ekstra lager ved at køre følgende kommando:

        # subscription-manager repos --enable=rhel-7-server-extras-rpms

11. Installer Azure Linux Agent ved at køre følgende kommando:

        # sudo yum install WALinuxAgent
        # sudo systemctl enable waagent.service

12. Opret ikke mellemrum Byt om på OS disken. Azure Linux Agent kan automatisk konfigurere Udskift mellemrum ved hjælp af den lokale ressource disk, der er knyttet til VM, efter VM er klargjort på Azure. Bemærk, at lokal ressource disken er en midlertidig disk, og kan være blevet tømt når VM er fjernet. Når du har installeret Azure Linux Agent (se det foregående trin) ændre følgende parametre i `/etc/waagent.conf` korrekt:

        ResourceDisk.Format=y
        ResourceDisk.Filesystem=ext4
        ResourceDisk.MountPoint=/mnt/resource
        ResourceDisk.EnableSwap=y
        ResourceDisk.SwapSizeMB=2048    ## NOTE: set this to whatever you need it to be.

13. Hvis du vil fjerne registreringen af abonnementet, skal du køre følgende kommando:

        # sudo subscription-manager unregister

14. Kør følgende kommandoer til deprovision den virtuelle maskine og forberede klargøring af Azure:

        # sudo waagent -force -deprovision
        # export HISTSIZE=0
        # logout

15. Klik på **handling > Luk** i Hyper-V Manager. Din Linux VHD er nu klar til at blive overført til Azure.
 


## <a name="prepare-a-red-hat-based-virtual-machine-from-kvm"></a>Forberede en Red Hat-baseret virtuel maskine fra KVM

### <a id="rhel67kvm"> </a>Forberede en RHEL 6,7 virtuel maskine fra KVM###


1.  Hent KVM billedet af RHEL 6,7 fra Red Hat.

2.  Angive en adgangskode, rod.

    Oprette en krypteret adgangskode og kopiere output fra kommandoen:

        # openssl passwd -1 changeme

    Angive en adgangskode, rod med guestfish:

        # guestfish --rw -a <image-name>
        ><fs> run
        ><fs> list-filesystems
        ><fs> mount /dev/sda1 /
        ><fs> vi /etc/shadow
        ><fs> exit

    Ændre det andet felt på brugeren, root fra "!" til den krypterede adgangskode.

3.  Oprette en virtuel maskine i KVM fra qcow2 billedet, angive disktypen til **qcow2**og angive virtuelt netværk interface enhedsmodel til **virtio**. Derefter starter virtual machine og logge på som rod.

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

6.  Flytte (eller fjerne) udev reglerne for at undgå at generere statisk regler for grænsefladen Ethernet. Disse regler forårsage problemer, når du klone en virtuel maskine i Microsoft Azure eller Hyper-v

        # mkdir -m 0700 /var/lib/waagent
        # mv /lib/udev/rules.d/75-persistent-net-generator.rules /var/lib/waagent/
        # mv /etc/udev/rules.d/70-persistent-net.rules /var/lib/waagent/

7.  Sørg for, at netværkstjenesten begynder på starttidspunktet ved at køre følgende kommando:

        # chkconfig network on

8.  Registrere din Red Hat abonnement for at aktivere installationen af pakker fra RHEL lager ved at køre følgende kommando:

        # subscription-manager register --auto-attach --username=XXX --password=XXX

9.  Ændre start kernen linje i konfigurationen rydde medtage yderligere kernen parametre til Azure. Du kan åbne `/boot/grub/menu.lst` i et tekstredigeringsprogram, og Sørg for, at standard kernen omfatter følgende parametre:

        console=ttyS0 earlyprintk=ttyS0 rootdelay=300 numa=off

    Dette sikrer også, at alle console meddelelser sendes til den første serielle port, som kan hjælpe Azure support med fejlfinding af problemer. Dette vil deaktivere NUMA på grund af en fejl i kernen-versionen, der bruges af RHEL 6.

    Ud over ovenstående handlingen anbefaler vi, at du fjerner følgende parametre:

        rhgb quiet crashkernel=auto

    Grafiske og stille Start ikke er nyttig i et skybaseret miljø, hvor vi vil alle logfilerne, der skal sendes til den serielle port.
    Indstillingen crashkernel muligvis ikke konfigureret eventuelt venstre, men Bemærk, at denne parameter kan reducere mængden af tilgængelig hukommelse i VM ved 128 MB eller mere. Det kan være problematisk på mindre VM størrelser.

10. Tilføje Hyper-V moduler til initramfs:  

    Redigere `/etc/dracut.conf` og føje indhold: add_drivers += "hv_vmbus hv_netvsc hv_storvsc"

    Genopbygge initramfs: # dracut – f - v

11. Fjerne skyen initialisering:

        # yum remove cloud-init

12. Sørg for, at SSH serveren er installeret og konfigureret til at starte ved start:

        # chkconfig sshd on

    Ændre /etc/ssh/sshd_config for at medtage følgende linjer:

        PasswordAuthentication yes
        ClientAliveInterval 180

    Genstart sshd:

        # service sshd restart

13. Pakken WALinuxAgent `WALinuxAgent-<version>` nogle til Red Hat ekstra lager. Aktivere ekstra lager ved at køre følgende kommando:

        # subscription-manager repos --enable=rhel-6-server-extras-rpms

14. Installer Azure Linux Agent ved at køre følgende kommando:

        # yum install WALinuxAgent
        # chkconfig waagent on

15. Azure Linux Agent kan automatisk konfigurere Udskift mellemrum ved hjælp af den lokale ressource disk, der er knyttet til VM, efter VM er klargjort på Azure. Bemærk, at lokal ressource disken er en midlertidig disk, og kan være blevet tømt når VM er fjernet. Når du har installeret Azure Linux Agent (se det foregående trin) ændre følgende parametre i **/etc/waagent.conf** korrekt:

        ResourceDisk.Format=y
        ResourceDisk.Filesystem=ext4
        ResourceDisk.MountPoint=/mnt/resource
        ResourceDisk.EnableSwap=y
        ResourceDisk.SwapSizeMB=2048    ## NOTE: set this to whatever you need it to be.

16. Unregister abonnementet (hvis det er nødvendigt) ved at køre følgende kommando:

        # subscription-manager unregister

17. Kør følgende kommandoer til deprovision den virtuelle maskine og forberede klargøring af Azure:

        # waagent -force -deprovision
        # export HISTSIZE=0
        # logout

18. Luk VM i KVM.

19. Konvertere billedet qcow2 til Virtuelle format.
    Først konvertere billedet til raw-format:

         # qemu-img convert -f qcow2 –O raw rhel-6.7.qcow2 rhel-6.7.raw
    Sørg for, at størrelsen på rå billedet er justeret med 1 MB. Ellers runde op størrelse til at justere med 1 MB: # MB = $((1024*1024)) # størrelse = $(qemu img info -f rå – Medtag json "rhel-6.7.raw" | \ gawk ' svarer ($0, / "virtuel størrelse": ([0-9] +), /, val) {udskrive val[1]}') # rounded_size = $((($size/$MB + 1)*$MB))

         # qemu-img resize rhel-6.7.raw $rounded_size

    Konvertere rå disken til en fast størrelse Virtuelle:

         # qemu-img convert -f raw -o subformat=fixed -O vpc rhel-6.7.raw rhel-6.7.vhd


### <a id="rhel7xkvm"> </a>Forberede en RHEL 7.1/7.2 virtuel maskine fra KVM###


1.  Hent KVM billedet af RHEL 7.1 (eller 7.2) fra webstedet Red Hat. Vi bruger RHEL 7.1 som eksempel her.

2.  Angive en adgangskode, rod.

    Oprette en krypteret adgangskode, og kopier output fra kommandoen:

        # openssl passwd -1 changeme

    Angive en adgangskode, rod med guestfish.

        # guestfish --rw -a <image-name>
        ><fs> run
        ><fs> list-filesystems
        ><fs> mount /dev/sda1 /
        ><fs> vi /etc/shadow
        ><fs> exit

    Ændre det andet felt i roden bruger fra "!" til den krypterede adgangskode.

3.  Oprette en virtuel maskine i KVM fra qcow2 billedet, angive disktypen til **qcow2**og angive virtuelt netværk interface enhedsmodel til **virtio**. Derefter starter virtual machine og logge på som rod.

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

6.  Sørg for, at netværkstjenesten begynder på starttidspunktet ved at køre følgende kommando:

        # chkconfig network on

7.  Registrere din Red Hat abonnement for at aktivere installation af pakker fra RHEL lager ved at køre følgende kommando:

        # subscription-manager register --auto-attach --username=XXX --password=XXX

8.  Ændre start kernen linje i konfigurationen rydde medtage yderligere kernen parametre til Azure. Du kan åbne `/etc/default/grub` i et tekstredigeringsprogram og rediger parameteren **GRUB_CMDLINE_LINUX** . Eksempel:

        GRUB_CMDLINE_LINUX="rootdelay=300
        console=ttyS0
        earlyprintk=ttyS0"

    Dette sikrer også, at alle console meddelelser sendes til den første serielle port, som kan hjælpe Azure support med fejlfinding af problemer. Ud over ovenstående handlingen anbefaler vi, at du fjerner følgende parametre:

        rhgb quiet crashkernel=auto

    Grafiske og stille Start ikke er nyttig i et skybaseret miljø, hvor vi vil alle logfilerne, der skal sendes til den serielle port.
    Indstillingen crashkernel kan være konfigureret eventuelt venstre, men Bemærk, at denne parameter kan reducere mængden af tilgængelig hukommelse i VM ved 128 MB eller mere. Det kan være problematisk på mindre VM størrelser.

9.  Når du er færdig med redigering `/etc/default/grub`, køre følgende kommando for at genopbygge den rydde konfiguration:

        # grub2-mkconfig -o /boot/grub2/grub.cfg

10. Tilføje Hyper-V moduler til initramfs:

    Redigere `/etc/dracut.conf` og føje indhold:

        add_drivers+=”hv_vmbus hv_netvsc hv_storvsc”

    Genopbygge initramfs:

        # dracut –f -v

11. Fjerne skyen initialisering:

        # yum remove cloud-init

12. Sørg for, at SSH serveren er installeret og konfigureret til at starte ved start:

        # systemctl enable sshd

    Ændre /etc/ssh/sshd_config for at medtage følgende linjer:

        PasswordAuthentication yes
        ClientAliveInterval 180

    Genstart sshd:

        systemctl restart sshd

13. Pakken WALinuxAgent `WALinuxAgent-<version>` nogle til Red Hat ekstra lager. Aktivere ekstra lager ved at køre følgende kommando:

        # subscription-manager repos --enable=rhel-7-server-extras-rpms

14. Installer Azure Linux Agent ved at køre følgende kommando:

        # yum install WALinuxAgent

    Aktivere tjenesten waagent:

        # systemctl enable waagent.service

15. Opret ikke mellemrum Byt om på OS disken. Azure Linux Agent kan automatisk konfigurere Udskift mellemrum ved hjælp af den lokale ressource disk, der er knyttet til VM, efter VM er klargjort på Azure. Bemærk, at lokal ressource disken er en midlertidig disk, og kan være blevet tømt når VM er fjernet. Når du har installeret Azure Linux Agent (se det foregående trin) ændre følgende parametre i `/etc/waagent.conf` korrekt:

        ResourceDisk.Format=y
        ResourceDisk.Filesystem=ext4
        ResourceDisk.MountPoint=/mnt/resource
        ResourceDisk.EnableSwap=y
        ResourceDisk.SwapSizeMB=2048    ## NOTE: set this to whatever you need it to be.

16. Unregister abonnementet (hvis det er nødvendigt) ved at køre følgende kommando:

        # subscription-manager unregister

17. Kør følgende kommandoer til deprovision den virtuelle maskine og forberede klargøring af Azure:

        # sudo waagent -force -deprovision
        # export HISTSIZE=0
        # logout

18. Luk den virtuelle maskine i KVM.

19. Konvertere billedet qcow2 til Virtuelle format.

    Først konvertere billedet til raw-format:

         # qemu-img convert -f qcow2 –O raw rhel-7.1.qcow2 rhel-7.1.raw

    Sørg for, at størrelsen på rå billedet er justeret med 1 MB. Ellers runde op størrelse til at justere med 1 MB:

         # MB=$((1024*1024))
         # size=$(qemu-img info -f raw --output json "rhel-7.1.raw" | \
                  gawk 'match($0, /"virtual-size": ([0-9]+),/, val) {print val[1]}')
         # rounded_size=$((($size/$MB + 1)*$MB))

         # qemu-img resize rhel-7.1.raw $rounded_size

    Konvertere rå disken til en fast størrelse Virtuelle:

         # qemu-img convert -f raw -o subformat=fixed -O vpc rhel-7.1.raw rhel-7.1.vhd


## <a name="prepare-a-red-hat-based-virtual-machine-from-vmware"></a>Forberede en Red Hat-baseret virtuel maskine fra VMware
### <a name="prerequisites"></a>Forudsætninger
Dette afsnit antages det, at du allerede har installeret en RHEL virtuel maskine i VMware. Få mere at vide om, hvordan du installerer et operativsystem i VMware, skal du se [Installationsvejledningen til VMware gæst operativsystem](http://partnerweb.vmware.com/GOSIG/home.html).

- Når du installerer Linux-operativsystemet, anbefaler vi, at du bruger standard partitioner i stedet for LVM (ofte standard for mange installationer). Derved undgår LVM navnet er i konflikt med klonede FOS, især hvis en OS disk overhovedet skal knyttes til en anden VM til fejlfinding. LVM eller RAID kan bruges på datadisce Hvis Foretrukne.

- Konfigurer ikke en partition Byt om på OS disken. Du kan konfigurere Linux agent for at oprette filen Byt om på disken midlertidige ressource. Du kan finde flere oplysninger om dette i nedenstående fremgangsmåde.

- Når du opretter den virtuelle harddisk, Vælg **Store Virtuel disk som en enkelt fil**.



### <a id="rhel67vmware"> </a>Forberede en RHEL 6,7 virtuel maskine fra VMware###

1.  Fjerne NetworkManager ved at køre følgende kommando:

         # sudo rpm -e --nodeps NetworkManager

    Bemærk, at hvis pakken ikke allerede er installeret, mislykkes denne kommando med en fejlmeddelelse. Dette er forventet.

2.  Oprette en fil med navnet **netværk** i den /etc/sysconfig eller den mappe, der indeholder følgende tekst:

        NETWORKING=yes
        HOSTNAME=localhost.localdomain

3.  Oprette en fil med navnet **ifcfg eth0** i /etc/sysconfig/network-scripts / mappe, der indeholder følgende tekst:

        DEVICE=eth0
        ONBOOT=yes
        BOOTPROTO=dhcp
        TYPE=Ethernet
        USERCTL=no
        PEERDNS=yes
        IPV6INIT=no

4.  Flytte (eller fjerne) udev reglerne for at undgå at generere statisk regler for grænsefladen Ethernet. Disse regler forårsage problemer, når du klone en virtuel maskine i Microsoft Azure eller Hyper-v

        # sudo mkdir -m 0700 /var/lib/waagent
        # sudo mv /lib/udev/rules.d/75-persistent-net-generator.rules /var/lib/waagent/
        # sudo mv /etc/udev/rules.d/70-persistent-net.rules /var/lib/waagent/

5.  Sørg for, at netværkstjenesten begynder på starttidspunktet ved at køre følgende kommando:

        # sudo chkconfig network on

6.  Registrere din Red Hat abonnement for at aktivere installationen af pakker fra RHEL lager ved at køre følgende kommando:

        # sudo subscription-manager register --auto-attach --username=XXX --password=XXX

7.  Pakken WALinuxAgent `WALinuxAgent-<version>` nogle til Red Hat ekstra lager. Aktivere ekstra lager ved at køre følgende kommando:

        # subscription-manager repos --enable=rhel-6-server-extras-rpms

8.  Ændre start kernen linje i konfigurationen rydde medtage yderligere kernen parametre til Azure. Du kan åbne "/ boot/grub/menu.lst" i et tekstredigeringsprogram, og Sørg for, at standard kernen omfatter følgende parametre:

        console=ttyS0
        earlyprintk=ttyS0
        rootdelay=300
        numa=off

    Dette sikrer også, at alle console meddelelser sendes til den første serielle port, som kan hjælpe Azure support med fejlfinding af problemer. Dette vil deaktivere NUMA på grund af en fejl i kernen-versionen, der bruges af RHEL 6.
    Ud over ovenstående handlingen anbefaler vi, at du fjerner følgende parametre:

        rhgb quiet crashkernel=auto

    Grafiske og stille Start ikke er nyttig i et skybaseret miljø, hvor vi vil alle logfilerne, der skal sendes til den serielle port.
    Indstillingen crashkernel kan være konfigureret eventuelt venstre, men Bemærk, at denne parameter kan reducere mængden af tilgængelig hukommelse i VM ved 128 MB eller mere. Det kan være problematisk på mindre VM størrelser.

9.  Tilføje Hyper-V moduler til initramfs:

        Edit `/etc/dracut.conf` and add content:

            add_drivers+=”hv_vmbus hv_netvsc hv_storvsc”

        Rebuild initramfs:

            # dracut –f -v

10. Sørg for, at SSH serveren er installeret og konfigureret til at starte på starttidspunktet. Det er normalt standard. Ændre `/etc/ssh/sshd_config` medtage følgende linje:

        ClientAliveInterval 180

11. Installer Azure Linux Agent ved at køre følgende kommando:

        # sudo yum install WALinuxAgent
        # sudo chkconfig waagent on

12. Opret ikke Byt om plads på disken OS:

    Azure Linux Agent kan automatisk konfigurere Udskift mellemrum ved hjælp af den lokale ressource disk, der er knyttet til VM, efter VM er klargjort på Azure. Bemærk, at lokal ressource disken er en midlertidig disk, og kan være blevet tømt når VM er fjernet. Når du har installeret Azure Linux Agent (se det foregående trin) ændre følgende parametre i `/etc/waagent.conf` korrekt:

        ResourceDisk.Format=y
        ResourceDisk.Filesystem=ext4
        ResourceDisk.MountPoint=/mnt/resource
        ResourceDisk.EnableSwap=y
        ResourceDisk.SwapSizeMB=2048    ## NOTE: set this to whatever you need it to be.

13. Unregister abonnementet (hvis det er nødvendigt) ved at køre følgende kommando:

        # sudo subscription-manager unregister

14. Kør følgende kommandoer til deprovision den virtuelle maskine og forberede klargøring af Azure:

        # sudo waagent -force -deprovision
        # export HISTSIZE=0
        # logout

15. Luk VM, og konvertere VMDK-filen til en .vhd-fil.

    Først konvertere billedet til raw-format:

        # qemu-img convert -f vmdk –O raw rhel-6.7.vmdk rhel-6.7.raw

    Sørg for, at størrelsen på rå billedet er justeret med 1 MB. Ellers runde op størrelse til at justere med 1 MB:

        # MB=$((1024*1024))
        # size=$(qemu-img info -f raw --output json "rhel-6.7.raw" | \
                gawk 'match($0, /"virtual-size": ([0-9]+),/, val) {print val[1]}')
        # rounded_size=$((($size/$MB + 1)*$MB))
        # qemu-img resize rhel-6.7.raw $rounded_size

    Konvertere rå disken til en fast størrelse Virtuelle:

        # qemu-img convert -f raw -o subformat=fixed -O vpc rhel-6.7.raw rhel-6.7.vhd


### <a id="rhel7xvmware"> </a>Forberede en RHEL 7.1/7.2 virtuel maskine fra VMware###

1.  Oprette en fil med navnet **netværk** i den /etc/sysconfig eller den mappe, der indeholder følgende tekst:

        NETWORKING=yes
        HOSTNAME=localhost.localdomain

2.  Oprette en fil med navnet **ifcfg eth0** i /etc/sysconfig/network-scripts / mappe, der indeholder følgende tekst:

        DEVICE=eth0
        ONBOOT=yes
        BOOTPROTO=dhcp
        TYPE=Ethernet
        USERCTL=no
        PEERDNS=yes
        IPV6INIT=no

3.  Sørg for, at netværkstjenesten begynder på starttidspunktet ved at køre følgende kommando:

        # sudo chkconfig network on

4.  Registrere din Red Hat abonnement for at aktivere installationen af pakker fra RHEL lager ved at køre følgende kommando:

        # sudo subscription-manager register --auto-attach --username=XXX --password=XXX

5.  Ændre start kernen linje i konfigurationen rydde medtage yderligere kernen parametre til Azure. Du kan åbne `/etc/default/grub` i et tekstredigeringsprogram og rediger parameteren **GRUB_CMDLINE_LINUX** . Eksempel:

        GRUB_CMDLINE_LINUX="rootdelay=300
        console=ttyS0
        earlyprintk=ttyS0"

    Dette sikrer også, at alle console meddelelser sendes til den første serielle port, som kan hjælpe Azure support med fejlfinding af problemer. Ud over ovenstående handlingen anbefaler vi, at du fjerner følgende parametre:

        rhgb quiet crashkernel=auto

    Grafiske og stille Start ikke er nyttig i et skybaseret miljø, hvor vi vil alle logfilerne, der skal sendes til den serielle port.
    Indstillingen crashkernel kan være konfigureret eventuelt venstre, men Bemærk, at denne parameter kan reducere mængden af tilgængelig hukommelse i VM ved 128 MB eller mere. Det kan være problematisk på mindre VM størrelser.

6.  Når du er færdig med redigering `/etc/default/grub`, køre følgende kommando for at genopbygge den rydde konfiguration:

         # sudo grub2-mkconfig -o /boot/grub2/grub.cfg

7.  Tilføje Hyper-V moduler til initramfs:

    Redigere `/etc/dracut.conf`, tilføje indhold:

        add_drivers+=”hv_vmbus hv_netvsc hv_storvsc”

    Genopbygge initramfs:

        # dracut –f -v

8.  Sørg for, at SSH serveren er installeret og konfigureret til at starte på starttidspunktet. Det er normalt standard. Ændre `/etc/ssh/sshd_config` medtage følgende linje:

        ClientAliveInterval 180

9.  Pakken WALinuxAgent `WALinuxAgent-<version>` nogle til Red Hat ekstra lager. Aktivere ekstra lager ved at køre følgende kommando:

        # subscription-manager repos --enable=rhel-7-server-extras-rpms

10. Installer Azure Linux Agent ved at køre følgende kommando:

        # sudo yum install WALinuxAgent
        # sudo systemctl enable waagent.service

11. Opret ikke mellemrum Byt om på OS disken. Azure Linux Agent kan automatisk konfigurere Udskift mellemrum ved hjælp af den lokale ressource disk, der er knyttet til VM, efter VM er klargjort på Azure. Bemærk, at lokal ressource disken er en midlertidig disk, og kan være blevet tømt når VM er fjernet. Når du har installeret Azure Linux Agent (se det foregående trin) ændre følgende parametre i `/etc/waagent.conf` korrekt:

        ResourceDisk.Format=y
        ResourceDisk.Filesystem=ext4
        ResourceDisk.MountPoint=/mnt/resource
        ResourceDisk.EnableSwap=y
        ResourceDisk.SwapSizeMB=2048    ## NOTE: set this to whatever you need it to be.

12. Hvis du vil fjerne registreringen af abonnementet, skal du køre følgende kommando:

        # sudo subscription-manager unregister

13. Kør følgende kommandoer til deprovision den virtuelle maskine og forberede klargøring af Azure:

        # sudo waagent -force -deprovision
        # export HISTSIZE=0
        # logout

14. Luk VM, og konvertere filen VMDK til Virtuelle format.

    Først konvertere billedet til raw-format:

        # qemu-img convert -f vmdk –O raw rhel-7.1.vmdk rhel-7.1.raw

    Sørg for, at størrelsen på rå billedet er justeret med 1 MB. Ellers runde op størrelse til at justere med 1 MB:

        # MB=$((1024*1024))
        # size=$(qemu-img info -f raw --output json "rhel-7.1.raw" | \
                 gawk 'match($0, /"virtual-size": ([0-9]+),/, val) {print val[1]}')
        # rounded_size=$((($size/$MB + 1)*$MB))
        # qemu-img resize rhel-7.1.raw $rounded_size

    Konvertere rå disken til en fast størrelse Virtuelle:

        # qemu-img convert -f raw -o subformat=fixed -O vpc rhel-7.1.raw rhel-7.1.vhd


## <a name="prepare-a-red-hat-based-virtual-machine-from-an-iso-by-using-a-kickstart-file-automatically"></a>Forberede en Red Hat-baseret virtuel maskine fra en ISO ved hjælp af en kickstart fil automatisk


### <a id="rhel7xkickstart"> </a>Forberede en RHEL 7.1/7.2 virtuel maskine fra en kickstart-fil###


1.  Oprette en kickstart-fil med indholdet nedenfor, og Gem filen. Du kan finde oplysninger om installation af kickstart [Kickstart installationsvejledningen](https://access.redhat.com/documentation/en-US/Red_Hat_Enterprise_Linux/7/html/Installation_Guide/chap-kickstart-installations.html).



        # Kickstart for provisioning a RHEL 7 Azure VM

        # System authorization information
        auth --enableshadow --passalgo=sha512

        # Use graphical install
        text

        # Do not run the Setup Agent on first boot
        firstboot --disable

        # Keyboard layouts
        keyboard --vckeymap=us --xlayouts='us'

        # System language
        lang en_US.UTF-8

        # Network information
        network  --bootproto=dhcp

        # Root password
        rootpw --plaintext "to_be_disabled"

        # System services
        services --enabled="sshd,waagent,NetworkManager"

        # System timezone
        timezone Etc/UTC --isUtc --ntpservers 0.rhel.pool.ntp.org,1.rhel.pool.ntp.org,2.rhel.pool.ntp.org,3.rhel.pool.ntp.org

        # Partition clearing information
        clearpart --all --initlabel

        # Clear the MBR
        zerombr

        # Disk partitioning information
        part /boot --fstype="xfs" --size=500
        part / --fstyp="xfs" --size=1 --grow --asprimary

        # System bootloader configuration
        bootloader --location=mbr

        # Firewall configuration
        firewall --disabled

        # Enable SELinux
        selinux --enforcing

        # Don't configure X
        skipx

        # Power down the machine after install
        poweroff



        %packages
        @base
        @console-internet
        chrony
        sudo
        parted
        -dracut-config-rescue

        %end

        %post --log=/var/log/anaconda/post-install.log

        #!/bin/bash

        # Register Red Hat Subscription
        subscription-manager register --username=XXX --password=XXX --auto-attach --force

        # Install latest repo update
        yum update -y

        # Enable extras repo
        subscription-manager repos --enable=rhel-7-server-extras-rpms

        # Install WALinuxAgent
        yum install -y WALinuxAgent

        # Unregister Red Hat subscription
        subscription-manager unregister

        # Enable waaagent at boot-up
        systemctl enable waagent

        # Disable the root account
        usermod root -p '!!'

        # Configure swap in WALinuxAgent
        sed -i 's/^\(ResourceDisk\.EnableSwap\)=[Nn]$/\1=y/g' /etc/waagent.conf
        sed -i 's/^\(ResourceDisk\.SwapSizeMB\)=[0-9]*$/\1=2048/g' /etc/waagent.conf

        # Set the cmdline
        sed -i 's/^\(GRUB_CMDLINE_LINUX\)=".*"$/\1="console=tty1 console=ttyS0 earlyprintk=ttyS0 rootdelay=300"/g' /etc/default/grub

        # Enable SSH keepalive
        sed -i 's/^#\(ClientAliveInterval\).*$/\1 180/g' /etc/ssh/sshd_config

        # Build the grub cfg
        grub2-mkconfig -o /boot/grub2/grub.cfg

        # Configure network
        cat << EOF > /etc/sysconfig/network-scripts/ifcfg-eth0
        DEVICE=eth0
        ONBOOT=yes
        BOOTPROTO=dhcp
        TYPE=Ethernet
        USERCTL=no
        PEERDNS=yes
        IPV6INIT=no
        NM_CONTROLLED=yes
        EOF

        # Deprovision and prepare for Azure
        waagent -force -deprovision

        %end

2.  Placer filen kickstart på et sted, der er tilgængeligt fra installation systemet.

3.  Oprette en ny VM i Hyper-V Manager. Vælg **Vedhæft en virtuel harddisk senere**på siden **oprette forbindelse virtuel harddisk** , og Fuldfør guiden Ny virtuelt.

4.  Åbn indstillingerne for VM:

    en.  Vedhæfte en ny virtuel harddisk til VM. Sørg for at markere **Virtuelle Format** og **Fast størrelse**.

    b.  Vedhæft installationen ISO til DVD-drevet.

    c.  Angive BIOS til at starte fra CD.

5.  Start VM. Når installationsvejledningen vises, skal du trykke på **Tab** for at konfigurere indstillingerne for start.

6.  ENTER `inst.ks=<the location of the kickstart file>` i slutningen af startindstillinger, og tryk på **Enter**.

7.  Vent til at afslutte installationen. Når overførslen er færdig, lukkes hvor VM automatisk. Din Linux VHD er nu klar til at blive overført til Azure.

## <a name="known-issues"></a>Kendte problemer
Der er kendte problemer, når du bruger RHEL 7.1 i Hyper-V og Azure.

### <a name="disk-io-freeze"></a>Disken i/o-Frys

Dette problem kan opstå under hyppige lagerplads disk i/o-aktiviteter med RHEL 7.1 i Hyper-V og Azure.   

Gendannelsen rente:

Dette problem er forbigående. Det står dog mere end ofte tidligere under hyppigere disk i/o-handlinger i Hyper-V og Azure.   


[AZURE.NOTE] Denne kendte problemer er allerede behandlet af Red Hat. For at installere de tilhørende rettelser skal du køre følgende kommando:

    # sudo yum update

### <a name="the-hyper-v-driver-could-not-be-included-in-the-initial-ram-disk-when-using-a-non-hyper-v-hypervisor"></a>Hyper-V-driver kunne ikke medtages i indledende RAM-disken, når du bruger en ikke-Hyper-V hypervisor

I nogle tilfælde kan Linux Installer ikke indeholde driverne til Hyper-V i den indledende RAM disk (initrd eller initramfs) medmindre programmet registrerer, at den kører i et Hyper-V-miljø.

Når du bruger et andet virtualization system (det vil sige Virtualbox, Xen osv.) til at forberede dit Linux billede, skal det være nødvendigt at genopbygge initrd for at sikre, at mindst hv_vmbus og hv_storvsc kernen moduler er tilgængelige under den indledende RAM disk. Dette er et kendt problem mindst på systemer, der er baseret på den foregående Red Hat fordeling.

Du kan løse problemet, skal du tilføje Hyper-V moduler til initramfs og genopbygge den:

Redigere `/etc/dracut.conf` og føje indhold:

        add_drivers+=”hv_vmbus hv_netvsc hv_storvsc”

Genopbygge initramfs:

        # dracut –f -v

Få mere at vide ved at se oplysninger om [genopbygge initramfs](https://access.redhat.com/solutions/1958).

## <a name="next-steps"></a>Næste trin
Nu er du klar til brug harddisken Red Hat Enterprise Linux virtuelle til at oprette nye virtuelle maskiner i Azure. Hvis dette er første gang, du overfører .vhd-filen til Azure, kan du se trin 2 og 3 i [oprette og overføre en virtuel harddisk, der indeholder Linux-operativsystemet](virtual-machines-linux-classic-create-upload-vhd.md).

Du kan finde flere oplysninger om den hypervisors, der er certificeret til at køre Red Hat Enterprise Linux, [Red Hat](https://access.redhat.com/certified-hypervisors)på webstedet.
