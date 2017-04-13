<properties
    pageTitle="Oprette og overføre en Linux VHD i Azure"
    description="Lær at oprette og overføre en Azure virtuel harddisk (Virtuelle), der indeholder en Linux-operativsystemet."
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
    ms.date="09/23/2016"
    ms.author="szark"/>

# <a name="information-for-non-endorsed-distributions"></a>Oplysninger om ikke-godkendt fordeling #

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-both-include.md)]


**Vigtigt**: feltet Azure platform SLA gælder for virtuelle maskiner, der kører Linux OS, når en af de [godkendt salgsdistributioner](virtual-machines-linux-endorsed-distros.md) bruges. Alle distribuerede Linux-versioner, der findes i billedgalleriet Azure er påtegnede salgsdistributioner med de påkrævede konfiguration.

- [Linux på Azure - godkendt fordeling](virtual-machines-linux-endorsed-distros.md)
- [Understøttelse af Linux billeder i Microsoft Azure](https://support.microsoft.com/kb/2941892)

Alle salgsdistributioner, der kører på Azure skal opfylde en række forudsætninger, der skal have mulighed for at køre korrekt på platformen.  I denne artikel er ikke på nogen måde omfattende, som hver fordelingen er forskellige; og det er helt muligt, selvom du opfylder alle kriterier nedenfor du stadig skal betydeligt tilpasse systemet Linux for at sikre, at den korrekt kører på en platform.

Det er derfor, anbefaler vi, at du starter med en af vores [Linux på Azure godkendt Salgsdistributioner](virtual-machines-linux-endorsed-distros.md) når det er muligt. I følgende artikler vejleder dig gennem Sådan forberedes forskellige påtegnede Linux fordelingerne, der understøttes på Azure:

- **[CentOS-baserede Salgsdistributioner](virtual-machines-linux-create-upload-centos.md)**
- **[Debian Linux](virtual-machines-linux-debian-create-upload-vhd.md)**
- **[Oracle Linux](virtual-machines-linux-oracle-create-upload-vhd.md)**
- **[Red Hat Enterprise Linux](virtual-machines-linux-redhat-create-upload-vhd.md)**
- **[SLES & openSUSE](virtual-machines-linux-suse-create-upload-vhd.md)**
- **[Ubuntu](virtual-machines-linux-create-upload-ubuntu.md)**

Resten af denne artikel fokuserer på generelle retningslinjer for at køre Linux fordelingen på Azure.


## <a name="general-linux-installation-notes"></a>Generelle Linux Installationsnoter ##

- Formatet VHDX understøttes ikke i Azure, kun **fast Virtuelle**.  Du kan konvertere disken Virtuelle formatet ved hjælp af Hyper-V Manager eller Konverter virtuelle-cmdlet. Hvis du bruger VirtualBox betyder det, at vælge **fast størrelse** i modsætning til standard dynamisk tildeles ved oprettelse af disken.

- Når du installerer Linux systemet er det *anbefales* , at du bruger standard partitioner i stedet for LVM (ofte standard for mange installationer). Derved undgår LVM navnet er i konflikt med klonede FOS, især hvis en OS disken overhovedet skal knyttes til en anden identiske VM til fejlfinding. [LVM](virtual-machines-linux-configure-lvm.md) eller [RAID](virtual-machines-linux-configure-raid.md) kan anvendes på datadisce.

- Kernen understøttelse af tilslutte UDF filsystemer er påkrævet. Ved første start på Azure overskredet klargøring konfigurationen til Linux VM via UDF-formateret medier, der er knyttet til gæst. Azure Linux agent skal kunne tilslutte UDF-filsystemet for at læse konfigurationen og klargøre VM.

- Linux kerneversioner under 2.6.37 understøtter ikke NUMA på Hyper-V med VM større. Dette problem primært virkninger ældre salgsdistributioner ved hjælp af den foregående Red Hat 2.6.32 kerne og blev rettet i RHEL 6.6 (kerne-2.6.32-504). Systemer, der kører brugerdefinerede kerner ældre end 2.6.37 eller RHEL-baserede kerner ældre end 2.6.32-504 skal angive parameteren Start `numa=off` på kommandolinjen i grub.conf kernen. Du kan finde flere oplysninger Red Hat [KB 436883](https://access.redhat.com/solutions/436883).

- Konfigurer ikke en partition Byt om på OS disken. Linux agent kan konfigureres til at oprette filen Byt om på disken midlertidige ressource.  Kan finde flere oplysninger om dette i nedenstående fremgangsmåde.

- Alle de virtuelle harddiske skal have størrelser, multiplum af 1 MB.


### <a name="installing-linux-without-hyper-v"></a>Installere Linux uden Hyper-V ###

I nogle tilfælde Linux Installer kan ikke indeholder driverne til Hyper-V i det oprindelige ramdisk (initrd eller initramfs) medmindre programmet registrerer, at den kører en et Hyper-V-miljø.  Når du bruger et andet virtualization system (det vil sige Virtualbox, KVM osv.) til at forberede dit Linux billede, skal du muligvis at genopbygge initrd for at sikre, at mindst `hv_vmbus` og `hv_storvsc` kernen moduler er tilgængelige under den indledende ramdisk.  Dette er et kendt problem mindst på systemer, der er baseret på den foregående Red Hat fordeling.

Mulighed for at genopbygge initrd eller initramfs billedet kan variere efter fordelingen. I dokumentationen til din fordeling, eller support til den korrekte fremgangsmåde.  Her er et eksempel til at genopbygge den initrd ved hjælp af den `mkinitrd` utility:

Først skal du sikkerhedskopiere det nuværende initrd billede:

    # cd /boot
    # sudo cp initrd-`uname -r`.img  initrd-`uname -r`.img.bak

Dernæst skal genopbygge initrd med den `hv_vmbus` og `hv_storvsc` kernen moduler:

    # sudo mkinitrd --preload=hv_storvsc --preload=hv_vmbus -v -f initrd-`uname -r`.img `uname -r`


### <a name="resizing-vhds"></a>Ændre størrelsen på virtuelle harddiske ###

Virtuelle billeder på Azure skal have en virtuel størrelse, der er justeret med 1MB.  Typisk virtuelle harddiske oprettet ved hjælp af Hyper-V skal allerede være er justeret korrekt.  Hvis den virtuelle harddisk ikke er justeret korrekt kan du modtager en fejlmeddelelse i stil med følgende, når du forsøger at oprette et *billede* fra din Virtuelle:

    "The VHD http://<mystorageaccount>.blob.core.windows.net/vhds/MyLinuxVM.vhd has an unsupported virtual size of 21475270656 bytes. The size must be a whole number (in MBs).”

Du kan afhjælpe dette kan du ændre størrelsen på VM ved hjælp af Hyper-V Manager-konsollen eller [Ændre størrelsen på Virtuelle](http://technet.microsoft.com/library/hh848535.aspx) Powershell-cmdlet.  Hvis du ikke kører i et Windows-miljø er det anbefales at bruge qemu img til at konvertere (hvis det er nødvendigt) og tilpasse størrelsen på den virtuelle harddisk.

> [AZURE.NOTE] Der er et kendt fejl i qemu img versioner > = 2.2.1, der resulterer i en forkert formateret Virtuelle. Problemet er løst i QEMU 2.6. Det anbefales at bruge qemu-img 2.2.0 eller lavere eller opdatere til 2.6 eller højere. Reference: https://bugs.launchpad.net/qemu/+bug/1490611.


 1. Ændre størrelsen på den virtuelle harddisk direkte ved hjælp af funktioner såsom `qemu-img` eller `vbox-manage` kan medføre en virtuel harddisk ikke kan startes.  Så anbefales det at første Konvertér den virtuelle harddisk til et billede, rå disk.  Hvis billedet VM allerede er blevet oprettet som rå disk billede (standard for nogle Hypervisors som KVM) kan du springe dette trin:

        # qemu-img convert -f vpc -O raw MyLinuxVM.vhd MyLinuxVM.raw

 2. Beregne den nødvendige størrelse af disk billedet for at sikre, at den virtuelle størrelse er justeret til 1MB.  Følgende fest shell script kan hjælpe med dette.  Scriptet bruger "`qemu-img info`" til at bestemme virtuelle størrelsen på disk billedet og derefter størrelsen til den næste 1MB:

        rawdisk="MyLinuxVM.raw"
        vhddisk="MyLinuxVM.vhd"

        MB=$((1024*1024))
        size=$(qemu-img info -f raw --output json "$rawdisk" | \
               gawk 'match($0, /"virtual-size": ([0-9]+),/, val) {print val[1]}')

        rounded_size=$((($size/$MB + 1)*$MB))
        echo "Rounded Size = $rounded_size"

 3. Ændre størrelsen på rå disken ved hjælp af $rounded_size som angivet i ovenstående scriptet:

        # qemu-img resize MyLinuxVM.raw $rounded_size

 4. Konverter rå disken tilbage til en fast størrelse virtuel harddisk:

        # qemu-img convert -f raw -o subformat=fixed -O vpc MyLinuxVM.raw MyLinuxVM.vhd



## <a name="linux-kernel-requirements"></a>Linux kernen krav ##

Linux Integration Services (LIS) driverne til Hyper-V og Azure er bidraget direkte til før Linux kernen. Mange salgsdistributioner, der indeholder en seneste Linux kerneversion (det vil sige 3.x) allerede har disse drivere, der er tilgængelige, eller på anden vis leverer backported versioner af disse drivere med deres kerner.  Disse drivere opdateres konstant i mod strømmen kernen med nye rettelser og funktioner, så når det er muligt det anbefales at køre en [godkendt fordeling](virtual-machines-linux-endorsed-distros.md) , der indeholder disse rettelser og opdateringer.

Hvis du kører en variant af Red Hat Enterprise Linux versioner **6.0 6.3**, skal du installere de nyeste drivere LIS til Hyper-V. Driverne kan findes [på denne placering](http://go.microsoft.com/fwlink/p/?LinkID=254263&clcid=0x409). Og RHEL **6.4 +** (og afledte) LIS-drivere er allerede er inkluderet i kernen og så ingen yderligere-pakker er nødvendige for at køre disse systemer på Azure.

Hvis en brugerdefineret kerne er påkrævet, anbefales det at bruge en nyere kerneversion (det vil sige **3,8 +**). For disse salgsdistributioner eller leverandører som vedligeholde deres egne kernen nogle indsats er påkrævet til regelmæssig backport LIS drivere fra før kernen til din brugerdefinerede kerne.  Selvom du allerede kører en relativt seneste kerneversion, anbefales at holde styr på eventuelle tidligere rettelser i LIS drivere og backport dem efter behov. Placeringen af LIS driver kildefiler findes i filen [SERVICEFIRMA](https://git.kernel.org/cgit/linux/kernel/git/torvalds/linux.git/tree/MAINTAINERS) i træet Linux kernen kilde:

    F:  arch/x86/include/asm/mshyperv.h
    F:  arch/x86/include/uapi/asm/hyperv.h
    F:  arch/x86/kernel/cpu/mshyperv.c
    F:  drivers/hid/hid-hyperv.c
    F:  drivers/hv/
    F:  drivers/input/serio/hyperv-keyboard.c
    F:  drivers/net/hyperv/
    F:  drivers/scsi/storvsc_drv.c
    F:  drivers/video/fbdev/hyperv_fb.c
    F:  include/linux/hyperv.h
    F:  tools/hv/

Fravær af følgende rettelser er blevet kendte problemer på Azure på en meget minimum, og så disse skal medtages i kernen. Denne liste er ikke på nogen måde massive eller fuldført for alle salgsdistributioner:

- [ata_piix: udskyde disk, der Hyper-V-drivere som standard](https://git.kernel.org/cgit/linux/kernel/git/torvalds/linux.git/commit/drivers/ata/ata_piix.c?id=cd006086fa5d91414d8ff9ff2b78fbb593878e3c)
- [storvsc: højde for overførsel pakker i NULSTIL stien](https://git.kernel.org/cgit/linux/kernel/git/torvalds/linux.git/commit/drivers/scsi/storvsc_drv.c?id=5c1b10ab7f93d24f29b5630286e323d1c5802d5c)
- [storvsc: undgå brugen af WRITE_SAME](https://git.kernel.org/cgit/linux/kernel/git/next/linux-next.git/commit/drivers/scsi/storvsc_drv.c?id=3e8f4f4065901c8dfc51407e1984495e1748c090)
- [storvsc: deaktivere skrive samme for RAID og virtuelle værter adapter drivere](https://git.kernel.org/cgit/linux/kernel/git/next/linux-next.git/commit/drivers/scsi/storvsc_drv.c?id=54b2b50c20a61b51199bedb6e5d2f8ec2568fb43)
- [storvsc: NULL markøren afvise henvisningen fix](https://git.kernel.org/cgit/linux/kernel/git/next/linux-next.git/commit/drivers/scsi/storvsc_drv.c?id=b12bb60d6c350b348a4e1460cd68f97ccae9822e)
- [storvsc: ring bufferen fejl kan medføre i/o-Frys](https://git.kernel.org/cgit/linux/kernel/git/next/linux-next.git/commit/drivers/scsi/storvsc_drv.c?id=e86fb5e8ab95f10ec5f2e9430119d5d35020c951)
- [scsi_sysfs: beskytte mod dobbelte udførelse af __scsi_remove_device](https://git.kernel.org/cgit/linux/kernel/git/next/linux-next.git/commit/drivers/scsi/scsi_sysfs.c?id=be821fd8e62765de43cc4f0e2db363d0e30a7e9b)


## <a name="the-azure-linux-agent"></a>Azure Linux-Agent ##

[Azure Linux Agent](virtual-machines-linux-agent-user-guide.md) (waagent) er påkrævet til at klargøre en Linux virtuel maskine i Azure korrekt. Du kan få den nyeste version og fil problemer eller sende anmodninger om hente på [Linux Agent GitHub repo](https://github.com/Azure/WALinuxAgent).

- Linux-agent er udgivet under Apache 2.0-licens. Mange salgsdistributioner giver allerede RPM eller deb pakker til agenten, og så i nogle tilfælde kan være installeret og opdateret med store anstrengelser.

- Azure Linux Agent kræver Python v2.6 +.

- Agenten kræver også modulet python pyasn1. De fleste salgsdistributioner giver dette som en separat pakke, som kan være installeret.

- I nogle tilfælde Azure Linux Agent muligvis ikke kompatible med NetworkManager. Mange af de RPM/Deb-pakker, der leveres af fordelingerne konfigurere NetworkManager som en konflikt waagent-pakken, og dermed fjernes NetworkManager, når du installerer pakken Linux agent.


## <a name="general-linux-system-requirements"></a>Generelt Linux-systemkrav ##

- Ændre linjen kernen Start i rydde eller GRUB2 for at medtage følgende parametre. Dette sikrer også alle console meddelelser sendes til den første serielle port, som kan hjælpe Azure support med fejlfinding af problemer:

        console=ttyS0,115200n8 earlyprintk=ttyS0,115200 rootdelay=300

    Dette sikrer også alle console meddelelser sendes til den første serielle port, som kan hjælpe Azure support med fejlfinding af problemer.

    Ud over ovenstående anbefales det at *fjerne* følgende parametre Hvis de findes:

        rhgb quiet crashkernel=auto

    Grafiske og stille Start ikke er nyttig i et skybaseret miljø, hvor vi vil alle logfilerne, der skal sendes til den serielle port.

    Den `crashkernel` indstilling kan være konfigureret eventuelt venstre, men Bemærk, at denne parameter kan reducere mængden af tilgængelig hukommelse i VM 128MB eller derover, som kan være problematisk på mindre VM størrelsen.

- Installation af Azure Linux-Agent

    Azure Linux-Agent er påkrævet til klargøring af et Linux billede på Azure.  Mange salgsdistributioner angiver agenten som en RPM eller Deb-pakke (pakken hedder typisk 'WALinuxAgent' eller 'walinuxagent').  Agenten kan også installeres manuelt ved at følge trinnene i [Linux Agent vejledning](virtual-machines-linux-agent-user-guide.md).

- Sørg for, at SSH serveren er installeret og konfigureret til at starte på starttidspunktet.  Det er normalt standard.

- Opret ikke Byt om plads på disken OS

    Azure Linux Agent kan automatisk konfigurere Udskift mellemrum ved hjælp af den lokale ressourcer disk, der er knyttet til VM efter klargøring af på Azure. Bemærk, at lokal ressource disken er en *midlertidig* disk, og kan være blevet tømt når VM er fjernet. Når du har installeret Azure Linux Agent (se forrige trin) ændre følgende parametre i /etc/waagent.conf korrekt:

        ResourceDisk.Format=y
        ResourceDisk.Filesystem=ext4
        ResourceDisk.MountPoint=/mnt/resource
        ResourceDisk.EnableSwap=y
        ResourceDisk.SwapSizeMB=2048    ## NOTE: set this to whatever you need it to be.

- Kør følgende kommandoer til deprovision den virtuelle maskine, som det sidste trin:

        # sudo waagent -force -deprovision
        # export HISTSIZE=0
        # logout

    >[AZURE.NOTE] På Virtualbox kan du får vist følgende fejlmeddelelse efter kørsel ' waagent-gennemtvinge - deprovision': `[Errno 5] Input/output error`. Denne fejlmeddelelse er ikke kritiske og kan ignoreres.

- Du skal derefter lukke den virtuelle maskine, og Overfør den virtuelle harddisk til Azure.
