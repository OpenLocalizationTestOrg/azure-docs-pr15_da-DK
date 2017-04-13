<properties
    pageTitle="Optimere din Linux VM på Azure | Microsoft Azure"
    description="Få mere at vide nogle tip til optimering at sikre, at du har konfigureret din Linux VM for optimal ydeevne på Azure"
    keywords="Linux virtuel maskine virtuelt linux ubuntu virtuelt" 
    services="virtual-machines-linux"
    documentationCenter=""
    authors="rickstercdn"
    manager="timlt"
    editor="tysonn"
    tags="azure-resource-manager" />

<tags
    ms.service="virtual-machines-linux"
    ms.workload="infrastructure-services"
    ms.tgt_pltfrm="vm-linux"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/06/2016"
    ms.author="rclaus"/>

# <a name="optimize-your-linux-vm-on-azure"></a>Optimere din Linux VM på Azure

Oprette en Linux virtuel maskine (VM) det er nemt at fra kommandolinjen eller fra portalen. Dette selvstudium viser, hvordan du sikre, at du har konfigureret den til at optimere præstationen på Microsoft Azure-platformen. I dette emne bruges en Ubuntu Server VM, men du kan også oprette Linux virtuelt ved hjælp af [dine egne billeder som skabeloner](virtual-machines-linux-create-upload-generic.md).  

## <a name="prerequisites"></a>Forudsætninger

Dette emne antages det, du allerede har en arbejde Azure-abonnement ([gratis prøveversion tilmelding](https://azure.microsoft.com/pricing/free-trial/)), der er [installeret Azure CLI](../xplat-cli-install.md) og har allerede klargjort en VM til din Azure-abonnement. Inden du gør alt med Azure - skal du godkende til dit abonnement. Hvis du vil gøre dette med Azure CLI, nøjes med at skrive `azure login` til at starte processen interaktive. 

## <a name="azure-os-disk"></a>Azure OS Disk

Når du opretter en Linux Vm i Azure, er der to diske, der er knyttet til den. /dev/sda OS disken, og /dev/sdb er midlertidige disken.  Brug ikke den primære OS disk (/ Udviklingscenter/sda) til alt undtagen operativsystemet, som den er optimeret til hurtig VM starttidspunktet og giver ikke god ydeevne for din arbejdsmængder. Du vil vedhæfte en eller flere diske til din VM at få vedvarende og optimeret storage til dine data. 

## <a name="adding-disks-for-size-and-performance-targets"></a>Tilføjelse af diske for størrelse og ydeevne destinationer 

Baseret på VM størrelse, kan du vedhæfte op til 16 yderligere diske på en A-serien, 32 diske på en D-række og 64 diske på en G-serie machine - hver op til 1 TB i størrelse. Du kan tilføje ekstra diske efter behov i dit space og IOP'er krav. Hver disk med et ydeevne mål af 500 IOP'er til Standard-lager og op til 5000 IOP'er per disk til Premium-lagerplads.  Du kan finde flere oplysninger om Premium lagerplads diske referere til [Premium lagerplads: High-Performance lagerplads til Azure FOS](../storage/storage-premium-storage.md)

For at opnå den højeste IOP'er på Premium lagerplads diske hvor deres cacheindstillinger er indstillet til enten "Skrivebeskyttet" eller "Ingen", skal du deaktivere "hindringer" mens tilslutte filsystemet i Linux. Du behøver ikke hindringer, fordi skriver til Premium lagerplads sikkerhedskopier diske er robust for disse cacheindstillinger.

- Hvis du bruger **reiserFS**, deaktivere hindringer ved hjælp af indstillingen Indlæs "barriere = ingen" (For at aktivere hindringer, bruge "barriere = træk")
- Hvis du bruger **ext3/ext4**, deaktivere hindringer ved hjælp af indstillingen Indlæs "barriere = 0" (For at aktivere hindringer, bruge "barriere = 1")
- Hvis du bruger **XFS**, deaktivere hindringer ved hjælp af indstillingen Indlæs i "nobarrier" (For at aktivere hindringer, bruge indstillingen "barriere")

## <a name="storage-account-considerations"></a>Overvejelser i forbindelse med lagerplads konto

Når du opretter din Linux VM i Azure, skal du sikre dig du vedhæfte diske fra lagerplads konti bosiddende i det samme område som din VM at sikre tæt og minimere netværksventetid.  Hver Standard lagerplads konto har maksimalt 20 k IOP'er og en 500 TB størrelse kapacitet.  Det fungerer til cirka 40 meget benyttet diske, herunder både OS disken og en hvilken som helst datadisce, du opretter. Der er ingen maksimale IOP'er grænse, men der er en 32 TB størrelsesbegrænsning for Premium lagerplads-konti. 

Når Håndter med høj IOP'er arbejdsmængder, og du har valgt Standard lagerplads til disken, skal du muligvis opdelte pakken på tværs af flere lagerplads konti for at sikre, at du ikke har klikker på 20.000 IOP'er grænsen for Standard lagerplads konti. Din VM kan indeholde en blanding af diske fra på tværs af forskellige lagerplads konti og opbevaring af typen firma at opnå optimal konfigurationen. 

## <a name="your-vm-temporary-drive"></a>Din VM midlertidigt drev

Som standard når du opretter en VM giver Azure dig en OS disk (/ Udviklingscenter/sda) og en midlertidig disk (/ Udviklingscenter/sdb).  Alle andre diske, du har tilføjet vist som /dev/sdc, /dev/sdd, /dev/sde og så videre. Alle data på harddisken midlertidige (/ Udviklingscenter/sdb) er ikke robust og kan gå tabt, hvis bestemte hændelser som VM ændring af størrelse, geninstallation, eller vedligeholdelse Fremtvinger genstart af din VM.  Størrelsen og typen af midlertidige disken er relateret til den VM størrelse, du vælger fra det øjeblik. Hvis det er en af premium størrelsen FOS (DS, G og DS_V2 serie) sikkerhedskopieres midlertidigt drev til en lokal SSD yderligere ydeevne på op til 48 k IOP'er. 

## <a name="linux-swap-file"></a>Byt om Linux-fil

Hvis din Azure VM er fra et Ubuntu eller CoreOS billede, kan du bruge CustomData til at sende en cloud-config til skyen initialisering. Hvis du [har overført et brugerdefineret Linux-billede](virtual-machines-linux-upload-vhd.md) , der bruger skyen initialisering, skal du også konfigurere Byt om partitioner, der bruger skyen initialisering.

På Ubuntu skyen billeder, skal du bruge skyen initialisering til at konfigurere Byt om partitionen. Se den efterfølgende side på Ubuntu wiki få mere at vide: [AzureSwapPartitions](https://wiki.ubuntu.com/AzureSwapPartitions).

Billeder uden understøttelse af skyen initialisering har VM billeder, der er implementeret fra Azure Marketplace en VM Linux Agent, der er integreret med Operativsystemet. Denne agent kan VM til at interagere med forskellige Azure tjenester. Hvis du har installeret et almindeligt billede fra Azure Marketplace, skal du benytte følgende fremgangsmåde for at konfigurere indstillingerne for Linux Udskift filen korrekt:

Finde og redigere to poster i filen **/etc/waagent.conf** . De kontrollerer eksistensen af en dedikeret Byt om fil og størrelsen af filen. De parametre, du er interesseret i at ændre er `ResourceDisk.EnableSwap=N` og`ResourceDisk.SwapSizeMB=0` 

Du vil ændre dem til følgende:

* ResourceDisk.EnableSwap=Y
* ResourceDisk.SwapSizeMB={size i MB til dine behov} 

Når du har foretaget ændringen, skal du genstarte waagent eller genstart din Linux VM afspejler disse ændringer.  Du kender ændringerne, der er implementeret og en Byt om filen er blevet oprettet, når du bruger den `free` kommandoen til at få vist ledig plads. I eksemplet nedenfor har en 512MB Byt om fil, der er oprettet som et resultat af at ændre filen waagent.conf.

    admin@mylinuxvm:~$ free
                total       used       free     shared    buffers     cached
    Mem:       3525156     804168    2720988        408       8428     633192
    -/+ buffers/cache:     162548    3362608
    Swap:       524284          0     524284
    admin@mylinuxvm:~$
 
## <a name="io-scheduling-algorithm-for-premium-storage"></a>I/o-planlægning algoritme til Premium-lagerplads

Med 2.6.18 Linux kernen, standard I/O planlægning algoritme er ændret fra Deadline til CFQ (helt sædvanlig kø algoritmen). RAM i/o-mønstre er der ringe forskel i ydeevne forskelle mellem CFQ og Deadline.  Om SSD-baserede disk, hvor disk i/o-mønster er afhænger først og fremmest sekventielle, kan skifte tilbage til Videresendelseskonsument eller Deadline algoritmen opnå en bedre i/o-ydeevne.

### <a name="view-the-current-io-scheduler"></a>Få vist den aktuelle i/o-Opgavestyring

Brug følgende kommando:  

    admin@mylinuxvm:~# cat /sys/block/sda/queue/scheduler

Du får vist følgende output, som angiver den aktuelle scheduler.  

    noop [deadline] cfq

###<a name="change-the-current-device-devsda-of-io-scheduling-algorithm"></a>Ændre den aktuelle enhed (/ Udviklingscenter/sda) på I/O planlægning algoritme

Brug følgende kommandoer:  

    azureuser@mylinuxvm:~$ sudo su -
    root@mylinuxvm:~# echo "noop" >/sys/block/sda/queue/scheduler
    root@mylinuxvm:~# sed -i 's/GRUB_CMDLINE_LINUX=""/GRUB_CMDLINE_LINUX_DEFAULT="quiet splash elevator=noop"/g' /etc/default/grub
    root@mylinuxvm:~# update-grub

>[AZURE.NOTE] Alene værdien indstilles til /dev/sda er ikke nyttig. Det skal angives på alle datadisce, hvor sekventielle I/O dominates i/o-mønster.  

Du bør se følgende output, der angiver grub.cfg har gendannet og, standard planlæggeren er blevet opdateret til Videresendelseskonsument.  

    Generating grub configuration file ...
    Found linux image: /boot/vmlinuz-3.13.0-34-generic
    Found initrd image: /boot/initrd.img-3.13.0-34-generic
    Found linux image: /boot/vmlinuz-3.13.0-32-generic
    Found initrd image: /boot/initrd.img-3.13.0-32-generic
    Found memtest86+ image: /memtest86+.elf
    Found memtest86+ image: /memtest86+.bin
    done

For Redhat fordeling familien, du skal kun bruge følgende kommando:   

    echo 'echo noop >/sys/block/sda/queue/scheduler' >> /etc/rc.local

## <a name="using-software-raid-to-achieve-higher-iops"></a>Brug af Software-RAID til at opnå højere jeg / Ops

Hvis din arbejdsbelastninger, som kræver flere IOP'er end kan give en enkelt disk, skal du bruge en software RAID-konfiguration af flere diske. Fordi Azure udfører allerede disk fleksibilitet på det lokale struktur lag, kan du opnå det højeste niveau af ydeevne fra en RAID-0 striping konfiguration.  Du har brug for til at klargøre og oprette nye i Azure-miljøet og knytte dem til din Linux VM før partitionering, formatere og tilslutte drevene.  Få mere at vide om konfiguration af en RAID softwareinstallation på din Linux VM i azure kan findes i dokumentet **[Konfiguration af Software RAID på Linux](virtual-machines-linux-configure-raid.md)** .


## <a name="next-steps"></a>Næste trin

Husk, som med alle optimering diskussioner, du vil have til at udføre tests, før og efter hver enkelt ændring måle påvirkningen har ændringen.  Optimering er en proces i trin for trin, du vil tildele forskellige resultater på tværs af forskellige computere i dit miljø.  Hvad fungerer for én konfiguration fungerer muligvis ikke for andre.

Nogle nyttige links til yderligere ressourcer: 

- [Premium opbevaring: High-Performance lagerplads til Azure virtuelt arbejdsmængder](../storage/storage-premium-storage.md)
- [Brugervejledning til Azure Linux Agent](virtual-machines-linux-agent-user-guide.md)
- [Optimering af ydeevne MySQL på Azure Linux FOS](virtual-machines-linux-classic-optimize-mysql.md)
- [Konfigurere Software RAID på Linux](virtual-machines-linux-configure-raid.md)
