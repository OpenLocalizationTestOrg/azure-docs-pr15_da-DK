<properties 
    pageTitle="Konfigurere LVM på en virtuel maskine kører Linux | Microsoft Azure" 
    description="Få mere at vide, hvordan du konfigurerer LVM på Linux i Azure." 
    services="virtual-machines-linux" 
    documentationCenter="na" 
    authors="szarkos"  
    manager="timlt" 
    editor="tysonn"
    tag="azure-service-management,azure-resource-manager" />

<tags 
    ms.service="virtual-machines-linux" 
    ms.workload="infrastructure-services" 
    ms.tgt_pltfrm="vm-linux" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="08/24/2016" 
    ms.author="szark"/>


# <a name="configure-lvm-on-a-linux-vm-in-azure"></a>Konfigurere LVM på en Linux VM i Azure

Dette dokument vil diskutere Sådan konfigureres logiske lydstyrken Manager (LVM) i din Azure virtuelle maskine. Selvom det er muligt at konfigurere LVM på en disk, der er knyttet til den virtuelle maskine, som standard vil de fleste skyen billeder ikke have LVM konfigureret på OS disken. Dette er at undgå problemer med dublerede lydstyrken grupper, hvis OS disken er nogensinde knyttet til en anden VM samme fordeling og skrive, det vil sige under et nedbrud. Det anbefales derfor kun at bruge LVM på data disk.


## <a name="linear-vs-striped-logical-volumes"></a>Lineært forhold Stribet logiske drev

LVM kan bruges til at kombinere et antal fysiske diske i en enkelt lagerplads lydstyrken. Som standard oprettes LVM normalt lineær logiske drev, hvilket betyder, at den fysiske lagerplads sammenføjet sammen. I dette tilfælde sendes læse-og skriveadgang handlinger typisk kun til en enkelt disk. Derimod kan du også oprette spredt logiske drev, hvor læsning og skrivning fordeles på flere diske, der er indeholdt i gruppen lydstyrken (det vil sige svarende til RAID 0). Ydeevnen årsager sandsynligvis vil du fordeler dine logiske drev, så læsning og skrivning udnytte alle diskoverfladerne vedhæftede data.

Dette dokument beskrives det, hvordan til at kombinere flere datadisce til en enkelt lydstyrken gruppe og derefter opretter en stribet logiske lydstyrken. Lidt er generalized nedenstående trin for at arbejde med de fleste salgsdistributioner. I de fleste tilfælde er værktøjer og arbejdsprocesser til administration af LVM på Azure ikke grundlæggende forskellige end andre miljøer. Som normalt skal du også se din leverandør af Linux til dokumentation og bedste praksis for LVM med din bestemt fordeling.


## <a name="attaching-data-disks"></a>Vedhæfte datadisce
Et skal normalt starte med to eller flere tom datadisce, når du bruger LVM. Baseret på dine EY behov, kan du vælge at vedhæfte diske, der er gemt i vores Standard Storage med op til 500 EY/ps per disk eller lagerplads vores Premium med op til 5000 EY/ps per disk. I denne artikel vil ikke går detaljer om, hvordan du klargør og vedhæfte datadisce til en Linux virtuel maskine. Se på Microsoft Azure artikel [vedhæfte en disk](virtual-machines-linux-add-disk.md) kan finde detaljerede oplysninger om, hvordan du vedhæfter en tom datadisk til en Linux virtuel maskine på Azure.

## <a name="install-the-lvm-utilities"></a>Installer programmerne LVM

- **Ubuntu**

        # sudo apt-get update
        # sudo apt-get install lvm2

- **RHEL, CentOS og Oracle Linux**

        # sudo yum install lvm2

- **SLES 12 og openSUSE**

        # sudo zypper install lvm2

- **SLES 11**

        # sudo zypper install lvm2

    På SLES11 skal du også redigere /etc/sysconfig/lvm og angive `LVM_ACTIVATED_ON_DISCOVERED` "aktivere":

        LVM_ACTIVATED_ON_DISCOVERED="enable" 


## <a name="configure-lvm"></a>Konfigurere LVM
I denne vejledning vi antages, at du har knyttet tre data disk som vi vil refererer til som `/dev/sdc`, `/dev/sdd` og `/dev/sde`. Bemærk, at disse ikke måske altid er de samme stinavne i din VM. Du kan køre '`sudo fdisk -l`' eller lignende kommando med listen over tilgængelige disken.

1. Forberede de fysiske enheder:

        # sudo pvcreate /dev/sd[cde]
          Physical volume "/dev/sdc" successfully created
          Physical volume "/dev/sdd" successfully created
          Physical volume "/dev/sde" successfully created


2.  Opret en volumenlicens-gruppe. I dette eksempel ringer vi på lydstyrke gruppe "data-vg01":

        # sudo vgcreate data-vg01 /dev/sd[cde]
          Volume group "data-vg01" successfully created


3. Oprette logiske diskenhed(er). Kommandoen under vi opretter en enkelt logisk disk med navnet "data-lv01" for at dække, gruppen hele den enhed, men vær opmærksom på, at det er også muligt at oprette flere logiske enheder i gruppen lydstyrken.

        # sudo lvcreate --extents 100%FREE --stripes 3 --name data-lv01 data-vg01
          Logical volume "data-lv01" created.


4. Formatere logiske lydstyrken

        # sudo mkfs -t ext4 /dev/data-vg01/data-lv01

  >[AZURE.NOTE] Med SLES11 brug "-t ext3" i stedet for ext4. SLES11 understøtter kun skrivebeskyttet adgang til ext4 filesystems.


## <a name="add-the-new-file-system-to-etcfstab"></a>Føje det nye filsystem til /etc/fstab

**Advarsel:** Forkert redigere filen /etc/fstab kan medføre et ikke kan startes system. Hvis det er sikker på, skal du se den fordeling dokumentation for at få oplysninger om, hvordan du korrekt redigere denne fil. Det anbefales også, at der oprettes en sikkerhedskopi af filen /etc/fstab før du kan redigere.

1. Opret den ønskede tilslutningspunkt for din nye filsystem, f.eks.:

        # sudo mkdir /data


2. Find den logiske lydstyrke sti

        # lvdisplay
        --- Logical volume ---
        LV Path                /dev/data-vg01/data-lv01
        ....


3. Åbn /etc/fstab i et tekstredigeringsprogram og Tilføj en post for det nye filsystem, f.eks.:

        /dev/data-vg01/data-lv01  /data  ext4  defaults  0  2

    Gem, og Luk /etc/fstab.


4. Test, posten/osv/fstab er korrekt:

        # sudo mount -a

    Hvis denne kommando resulterer i en fejlmeddelelse, skal du se syntaksen i filen/osv/fstab.

    Næste køre den `mount` kommando for at sikre, at filsystemet er indlæst:

        # mount
        ......
        /dev/mapper/data--vg01-data--lv01 on /data type ext4 (rw)


5. (Valgfrit) Fejlsikret start parametre i /etc/fstab

    Mange salgsdistributioner omfatter enten den `nobootwait` eller `nofail` tilslutte parametre, der kan føjes til filen/osv/fstab. Disse parametre Tillad for at finde fejl, når tilslutte et bestemt filsystem og tillade Linux systemet kan fortsætte med at starte, selvom det er ikke korrekt tilslutte RAID filsystemet. Se din fordeling dokumentation for flere oplysninger om disse parametre.

    Eksempel (Ubuntu):

        /dev/data-vg01/data-lv01  /data  ext4  defaults,nobootwait  0  2
