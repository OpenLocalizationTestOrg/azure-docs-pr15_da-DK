<properties
    pageTitle="Vedhæfte en disk til en Linux VM | Microsoft Azure"
    description="Lær, hvordan du vedhæfter en datadisk til en Azure virtuelt kører Linux og starte den, så det er klar til brug."
    services="virtual-machines-linux"
    documentationCenter=""
    authors="iainfoulds"
    manager="timlt"
    editor="tysonn"
    tags="azure-service-management"/>

<tags
    ms.service="virtual-machines-linux"
    ms.workload="infrastructure-services"
    ms.tgt_pltfrm="vm-linux"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/23/2016"
    ms.author="iainfou"/>

# <a name="how-to-attach-a-data-disk-to-a-linux-virtual-machine"></a>Hvordan du vedhæfter en datadisk til en Linux virtuel maskine

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]Se, hvordan du [vedhæfter en datadisk til med implementeringsmodel ressourcestyring](virtual-machines-linux-add-disk.md).

Du kan vedhæfte både tomme og diske, der indeholder data til din Azure FOS. Begge typer diske er .vhd filer, der er placeret på en Azure-lager-konto. Som med tilføjelse af en disk til en Linux maskine, skal når du vedhæfte disken du initialiseret og formatere den, så det er klar til brug. I denne artikel detaljer vedhæfte både tomme og diske, der allerede indeholder data til din FOS, samt hvordan du kan derefter initialiseret og formatere en ny disk.

> [AZURE.NOTE]Det er en god fremgangsmåde at bruge en eller flere separate diske til at gemme en virtuel maskine data. Når du opretter en Azure virtuelt, har en disk operativsystem og en midlertidig disk. **Brug ikke midlertidige disken til at gemme fast data.** Som navnet antyder, indeholder den midlertidige lagerplads. Den indeholder ingen redundans eller sikkerhedskopi, fordi det ikke er placeret i Azure-lager.
> Midlertidige disken typisk administreres af Azure Linux Agent og automatisk fastgøres til **/mnt/resource** (eller **/mnt** på Ubuntu billeder). På den anden side en datadisk kan navngives af Linux kernen noget i retning af `/dev/sdc`, og du vil dele, formatere og tilslutte denne ressource. Se [Brugervejledning til Azure Linux Agent] [ Agent] få mere at vide.

[AZURE.INCLUDE [howto-attach-disk-windows-linux](../../includes/howto-attach-disk-linux.md)]

## <a name="initialize-a-new-data-disk-in-linux"></a>Initialisering af en ny datadisk i Linux

1. SSH til din VM. Yderligere oplysninger, se, [hvordan du logger på en virtuel maskine kører Linux][Logon].

2. Derefter skal du finde enheds-id'et for data disken initialiseret. Der er to måder at gøre det:

    a) Grep til SCSI-enheder i loggene, sådan som i følgende kommando:

            $sudo grep SCSI /var/log/messages

    For seneste Ubuntu fordelingerne, kan du skal bruge `sudo grep SCSI /var/log/syslog` fordi logføring til `/var/log/messages` muligvis er deaktiveret som standard.

    Du kan finde id for den sidste datadisk, der blev tilføjet i de meddelelser, der vises.

    ![Få disk meddelelser](./media/virtual-machines-linux-classic-attach-disk/scsidisklog.png)

    ELLER

    b) Brug den `lsscsi` kommandoen til at finde ud af enheds-id. `lsscsi`kan installeres ved hjælp af en `yum install lsscsi` (baseret på Red Hat fordelingerne) eller `apt-get install lsscsi` (baseret på Debian salgsdistributioner). Du kan finde den disk, du leder efter, af dets _lun_ eller **logisk enhed**. For eksempel _lun_ om en på disk, du har vedhæftet, kan nemt ses fra `azure vm disk list <virtual-machine>` som:

            ~$ azure vm disk list TestVM
            info:    Executing command vm disk list
            + Fetching disk images
            + Getting virtual machines
            + Getting VM disks
            data:    Lun  Size(GB)  Blob-Name                         OS
            data:    ---  --------  --------------------------------  -----
            data:         30        TestVM-2645b8030676c8f8.vhd  Linux
            data:    0    100       TestVM-76f7ee1ef0f6dddc.vhd
            info:    vm disk list command OK

    Sammenligne disse data med output fra `lsscsi` til den samme eksempel virtuelt:

            ops@TestVM:~$ lsscsi
            [1:0:0:0]    cd/dvd  Msft     Virtual CD/ROM   1.0   /dev/sr0
            [2:0:0:0]    disk    Msft     Virtual Disk     1.0   /dev/sda
            [3:0:1:0]    disk    Msft     Virtual Disk     1.0   /dev/sdb
            [5:0:0:0]    disk    Msft     Virtual Disk     1.0   /dev/sdc

    Det sidste tal i en tupel i hver række er _lun_. Se `man lsscsi` kan finde flere oplysninger.

3. Når du bliver spurgt, skal du skrive følgende kommando for at oprette din enhed:

        $sudo fdisk /dev/sdc


4. Når du bliver bedt om det, kan du skrive **n** for at oprette en ny partition.


    ![Oprette enhed](./media/virtual-machines-linux-classic-attach-disk/fdisknewpartition.png)

5. Når du bliver bedt om det, kan du skrive **p** for at gøre partitionen den primære partition. Skriv **1** for at gøre den første partition, og skriv Angiv derefter for at acceptere standardværdien for cylinderen. Det kan vise standardværdierne for først og sidste sektorer, i stedet for cylinderen på nogle systemer. Du kan vælge at acceptere disse standardindstillinger.


    ![Oprette partition](./media/virtual-machines-linux-classic-attach-disk/fdisknewpartdetails.png)



6. Skriv **p** for at se oplysninger om den disk, der opdeler.


    ![Listeoplysninger disk](./media/virtual-machines-linux-classic-attach-disk/fdiskpartitiondetails.png)



7. Skriv **w** for at skrive indstillingerne for disken.


    ![Skrive diskplads ændringer](./media/virtual-machines-linux-classic-attach-disk/fdiskwritedisk.png)

8. Du kan nu oprette filsystemet på den nye partition. Føje partition tallet til enheds-ID (i eksemplet nedenfor `/dev/sdc1`). I følgende eksempel oprettes en ext4 partition på /dev/sdc1:

        # sudo mkfs -t ext4 /dev/sdc1

    ![Oprette filsystemet](./media/virtual-machines-linux-classic-attach-disk/mkfsext4.png)

    >[AZURE.NOTE] SuSE Linux Enterprise 11 systemer understøtter kun skrivebeskyttet adgang til ext4 filsystemer. Det anbefales for disse systemer til at formatere det nye filsystem som ext3 i stedet for ext4.


9. Gøre en mappe til at tilslutte det nye filsystem, på følgende måde:

        # sudo mkdir /datadrive


10. Endelig kan du tilslutte drevet, på følgende måde:

        # sudo mount /dev/sdc1 /datadrive

    Data disken er nu klar til brug som **/datadrive**.
    
    ![Oprette mappen og tilslutte disken](./media/virtual-machines-linux-classic-attach-disk/mkdirandmount.png)


11. Føje det nye drev til /etc/fstab:

    For at sikre drevet igen automatisk efter en genstart skal det føjes til filen/osv/fstab. Det anbefales desuden, UUID (globalt entydigt id) bruges i /etc/fstab til at referere til drevet i stedet for blot enhedens navn (det vil sige /dev/sdc1). Brug af UUID undgår forkerte disken blive tilsluttet til en bestemt placering, hvis OS registreres en fejl under start og eventuelle resterende datadisce derefter tildeles disse enheds-id'er. Hvis du vil finde består af det nye drev, kan du bruge værktøjet **blkid** :

        # sudo -i blkid

    Output ligner følgende:

        /dev/sda1: UUID="11111111-1b1b-1c1c-1d1d-1e1e1e1e1e1e" TYPE="ext4"
        /dev/sdb1: UUID="22222222-2b2b-2c2c-2d2d-2e2e2e2e2e2e" TYPE="ext4"
        /dev/sdc1: UUID="33333333-3b3b-3c3c-3d3d-3e3e3e3e3e3e" TYPE="ext4"


    >[AZURE.NOTE] Forkert redigere filen **/etc/fstab** kan medføre et ikke kan startes system. Hvis det er sikker på, finder den fordeling dokumentationen til oplysninger om, hvordan korrekt redigere denne fil. Det anbefales også, at der oprettes en sikkerhedskopi af filen /etc/fstab før du kan redigere.

    Derefter skal du åbne filen **/etc/fstab** i et tekstredigeringsprogram:

        # sudo vi /etc/fstab

    I dette eksempel skal bruge vi UUID værdien for den nye **/dev/sdc1** enhed, der er oprettet i de forrige trin og tilslutningspunkt **/datadrive**. Tilføj følgende linje til slutningen af filen **/etc/fstab** :

        UUID=33333333-3b3b-3c3c-3d3d-3e3e3e3e3e3e   /datadrive   ext4   defaults,nofail   1   2

    Eller på systemer, der er baseret på SuSE Linux du muligvis bruge en anden format:

        /dev/disk/by-uuid/33333333-3b3b-3c3c-3d3d-3e3e3e3e3e3e   /datadrive   ext3   defaults,nofail   1   2
    
    >[AZURE.NOTE] Den `nofail` indstilling sikrer, at VM starter selvom filsystemet er beskadiget eller disken ikke findes på starttidspunktet. Uden denne indstilling, du kan støde på funktionsmåde, som beskrevet i [Kan SSH til Linux VM på grund af FSTAB fejl](https://blogs.msdn.microsoft.com/linuxonazure/2016/07/21/cannot-ssh-to-linux-vm-after-adding-data-disk-to-etcfstab-and-rebooting/).

    Du kan nu teste, filsystemet er tilsluttet korrekt ved at ophæve tilknytningen af og remounting filsystemet, dvs. i eksemplet tilslutte punkt `/datadrive` oprettet i de tidligere trin:

        # sudo umount /datadrive
        # sudo mount /datadrive

    Hvis den `mount` kommandoen viser en fejl, skal du kontrollere/osv/fstab filen til korrekte syntaks. Hvis oprettet yderligere datadrev eller partitioner, angive dem i/osv/fstab samt separat.

    Gøre drevet skrivbart ved hjælp af denne kommando:

        # sudo chmod go+w /datadrive

>[AZURE.NOTE] Fjerne en datadisk uden at redigere fstab efterfølgende kan forårsage VM ikke kan starte. Hvis dette er et almindelige forekomst, skal de fleste salgsdistributioner give enten den `nofail` og/eller `nobootwait` fstab indstillinger, der tillader et system til at starte, selvom der opstår fejl på disken til at tilslutte på genstart. Se din fordeling dokumentationen til flere oplysninger om disse parametre.

### <a name="trimunmap-support-for-linux-in-azure"></a>TRIM/UNMAP understøttelse af Linux i Azure
Nogle Linux kerner understøtter TRIM/UNMAP handlinger for at slette ubrugte blokke på disken. Disse handlinger er primært nyttige i standard lagerplads til at informere Azure, slettes sider er ikke længere gyldige, og du kan kassere. Fjerne sider kan gemme omkostninger, hvis du opretter store filer og derefter slette dem.

Der er to måder at aktivere TRIM support i din Linux VM. Som altid se din fordeling til den anbefalede fremgangsmåde:

- Brug den `discard` tilslutte indstillingen i `/etc/fstab`, f.eks.:

        UUID=33333333-3b3b-3c3c-3d3d-3e3e3e3e3e3e   /datadrive   ext4   defaults,discard   1   2

- Du kan også køre den `fstrim` kommandoen manuelt fra kommandolinjen eller føje den til din crontab til at køre regelmæssigt:

    **Ubuntu**

        # sudo apt-get install util-linux
        # sudo fstrim /datadrive

    **RHEL/CentOS**

        # sudo yum install util-linux
        # sudo fstrim /datadrive

## <a name="troubleshooting"></a>Fejlfinding i forbindelse med
[AZURE.INCLUDE [virtual-machines-linux-lunzero](../../includes/virtual-machines-linux-lunzero.md)]


## <a name="next-steps"></a>Næste trin
Du kan læse mere om at bruge din Linux VM i følgende artikler:

- [Sådan logger en virtuel maskine kører Linux][Logon]

- [Hvordan du kan afbryde forbindelsen til en disk fra en Linux virtuel maskine](virtual-machines-linux-classic-detach-disk.md)

- [Brug af Azure CLI med implementeringsmodel klassisk](../virtual-machines-command-line-tools.md)

<!--Link references-->
[Agent]: virtual-machines-linux-agent-user-guide.md
[Logon]: virtual-machines-linux-mac-create-ssh-keys.md
