<properties
    pageTitle="Tilføje en disk til Linux VM | Microsoft Azure"
    description="Du kan føje en fast disk til din Linux VM"
    keywords="Linux virtuelt tilføje ressource disk"
    services="virtual-machines-linux"
    documentationCenter=""
    authors="rickstercdn"
    manager="timlt"
    editor="tysonn"
    tags="azure-resource-manager" />

<tags
    ms.service="virtual-machines-linux"
    ms.topic="article"
    ms.workload="infrastructure-services"
    ms.tgt_pltfrm="vm-linux"
    ms.devlang="na"
    ms.date="09/06/2016"
    ms.author="rclaus"/>

# <a name="add-a-disk-to-a-linux-vm"></a>Tilføje en disk til en Linux VM

Denne artikel beskrives, hvordan du vedhæfter en fast disk til din VM, så du kan bevare dine data – også selvom din VM reprovisioned på grund af vedligeholdelse eller ændre størrelsen på. Hvis du vil tilføje en disk, skal du [Azure CLI](../xplat-cli-install.md) konfigureret i ressourcestyring mode (`azure config mode arm`).  

## <a name="quick-commands"></a>Hurtig kommandoer

I eksemplerne nedenfor kommandoen Erstat værdier mellem &lt; og &gt; med værdierne fra dit eget miljø.

```bash
azure vm disk attach-new <myuniquegroupname> <myuniquevmname> <size-in-GB>
```

## <a name="attach-a-disk"></a>Vedhæfte en disk

Vedhæfte en ny disk er hurtig. Skriv `azure vm disk attach-new <myuniquegroupname> <myuniquevmname> <size-in-GB>` til at oprette og vedhæfte en ny GB disk til din VM. Hvis du ikke eksplicit identificere en lagerplads-konto, er en disk, du opretter placeret i den samme konto lagerplads hvor OS disken er placeret.  Det skal se ud som følger:

```bash
azure vm disk attach-new myuniquegroupname myuniquevmname 5
```

Output

```bash
info:    Executing command vm disk attach-new
+ Looking up the VM "myuniquevmname"
info:    New data disk location: https://cliexxx.blob.core.windows.net/vhds/myuniquevmname-20150526-0xxxxxxx43.vhd
+ Updating VM "myuniquevmname"
info:    vm disk attach-new command OK
```

## <a name="connect-to-the-linux-vm-to-mount-the-new-disk"></a>Oprette forbindelse til Linux VM til at oprette forbindelse til den nye disk

> [AZURE.NOTE] Dette emne opretter forbindelse til en VM ved hjælp af brugernavne og adgangskoder. Hvis du vil bruge offentlige og private nøgler par til at kommunikere med din VM, se, [hvordan du bruger SSH med Linux på Azure](virtual-machines-linux-mac-create-ssh-keys.md). Du kan ændre **SSH** forbindelsen af FOS, der er oprettet med den `azure vm quick-create` kommando ved hjælp af den `azure vm reset-access` kommando for at nulstille **SSH** access helt, tilføje eller fjerne brugere eller tilføje offentlig nøgle filer for at sikre adgang.

Du skal bruge SSH til din Azure VM til partition, formatere og tilslutte nye disken, så din Linux VM kan bruge den. Hvis du ikke kender, oprette forbindelse med **ssh**, kommandoen tager formularen `ssh <username>@<FQDNofAzureVM> -p <the ssh port>`, og ser sådan ud:

```bash
ssh ops@myuni-westu-1432328437727-pip.westus.cloudapp.azure.com -p 22
```

Output

```bash
The authenticity of host 'myuni-westu-1432328437727-pip.westus.cloudapp.azure.com (191.239.51.1)' can't be established.
ECDSA key fingerprint is bx:xx:xx:xx:xx:xx:xx:xx:xx:x:x:x:x:x:x:xx.
Are you sure you want to continue connecting (yes/no)? yes
Warning: Permanently added 'myuni-westu-1432328437727-pip.westus.cloudapp.azure.com,191.239.51.1' (ECDSA) to the list of known hosts.
ops@myuni-westu-1432328437727-pip.westus.cloudapp.azure.com's password:
Welcome to Ubuntu 14.04.2 LTS (GNU/Linux 3.16.0-37-generic x86_64)

* Documentation:  https://help.ubuntu.com/

System information as of Fri May 22 21:02:32 UTC 2015

System load: 0.37              Memory usage: 2%   Processes:       207
Usage of /:  41.4% of 1.94GB   Swap usage:   0%   Users logged in: 0

Graph this data and manage this system at:
  https://landscape.canonical.com/

Get cloud support with Ubuntu Advantage Cloud Guest:
  http://www.ubuntu.com/business/services/cloud

0 packages can be updated.
0 updates are security updates.

The programs included with the Ubuntu system are free software;
the exact distribution terms for each program are described in the
individual files in /usr/share/doc/*/copyright.

Ubuntu comes with ABSOLUTELY NO WARRANTY, to the extent permitted by
applicable law.

ops@myuniquevmname:~$
```

Nu hvor du har forbindelse til din VM, er du klar til at vedhæfte en disk.  Først finde disken, ved hjælp af `dmesg | grep SCSI` (den metode, du kan bruge til at finde din nye disk kan variere). I dette tilfælde ser nogenlunde således:

```bash
dmesg | grep SCSI
```

Output

```bash
[    0.294784] SCSI subsystem initialized
[    0.573458] Block layer SCSI generic (bsg) driver version 0.4 loaded (major 252)
[    7.110271] sd 2:0:0:0: [sda] Attached SCSI disk
[    8.079653] sd 3:0:1:0: [sdb] Attached SCSI disk
[ 1828.162306] sd 5:0:0:0: [sdc] Attached SCSI disk
```

og hvis det er dette emne, den `sdc` disken er den, som vi vil. Nu partition disken med `sudo fdisk /dev/sdc` --forudsætter, at der i din sag disken var `sdc`, og gøre det primære disk på partition 1, og Accepter andre standardindstillingerne.

```bash
sudo fdisk /dev/sdc
```

Output

```bash
Device contains neither a valid DOS partition table, nor Sun, SGI or OSF disklabel
Building a new DOS disklabel with disk identifier 0x2a59b123.
Changes will remain in memory only, until you decide to write them.
After that, of course, the previous content won't be recoverable.

Warning: invalid flag 0x0000 of partition table 4 will be corrected by w(rite)

Command (m for help): n
Partition type:
   p   primary (0 primary, 0 extended, 4 free)
   e   extended
Select (default p): p
Partition number (1-4, default 1): 1
First sector (2048-10485759, default 2048):
Using default value 2048
Last sector, +sectors or +size{K,M,G} (2048-10485759, default 10485759):
Using default value 10485759
```

Oprette partitionen ved at skrive `p` kommandoprompten:

```bash
Command (m for help): p

Disk /dev/sdc: 5368 MB, 5368709120 bytes
255 heads, 63 sectors/track, 652 cylinders, total 10485760 sectors
Units = sectors of 1 * 512 = 512 bytes
Sector size (logical/physical): 512 bytes / 512 bytes
I/O size (minimum/optimal): 512 bytes / 512 bytes
Disk identifier: 0x2a59b123

   Device Boot      Start         End      Blocks   Id  System
/dev/sdc1            2048    10485759     5241856   83  Linux

Command (m for help): w
The partition table has been altered!

Calling ioctl() to re-read partition table.
Syncing disks.
```

Og skrive et filsystem til partitionen ved hjælp af kommandoen **mkfs** , der angiver filsystemtypen og enhedens navn. I dette emne, vi bruger `ext4` og `/dev/sdc1` fra ovenfor:

```bash
sudo mkfs -t ext4 /dev/sdc1
```

Output

```bash
mke2fs 1.42.9 (4-Feb-2014)
Discarding device blocks: done
Filesystem label=
OS type: Linux
Block size=4096 (log=2)
Fragment size=4096 (log=2)
Stride=0 blocks, Stripe width=0 blocks
327680 inodes, 1310464 blocks
65523 blocks (5.00%) reserved for the super user
First data block=0
Maximum filesystem blocks=1342177280
40 block groups
32768 blocks per group, 32768 fragments per group
8192 inodes per group
Superblock backups stored on blocks:
    32768, 98304, 163840, 229376, 294912, 819200, 884736
Allocating group tables: done
Writing inode tables: done
Creating journal (32768 blocks): done
Writing superblocks and filesystem accounting information: done
```

Nu vi oprette en mappe for at tilslutte den filen system ved hjælp af `mkdir`:

```bash
sudo mkdir /datadrive
```

Og du tilslutte directory ved hjælp af `mount`:

```bash
sudo mount /dev/sdc1 /datadrive
```

Data disken er nu klar til brug som `/datadrive`.

```bash
ls
```

Output

```bash
bin   datadrive  etc   initrd.img  lib64       media  opt   root  sbin  sys  usr  vmlinuz
boot  dev        home  lib         lost+found  mnt    proc  run   srv   tmp  var
```

For at sikre drevet igen automatisk efter en genstart skal det føjes til filen/osv/fstab. Desuden det anbefales, at UUID (globalt entydigt id) bruges i/osv/fstab til at referere til drevet i stedet for blot på enhedens navn (f.eks., `/dev/sdc1`). Hvis Operativsystemet registrerer en fejl under start, undgår ved hjælp af UUID forkerte disken blive tilsluttet til en bestemt placering. Resterende datadisce ville derefter tildeles disse samme enhed id'er. Brug værktøjet **blkid** til at finde består af det nye drev:

```bash
sudo -i blkid
```

Output ligner følgende:

```bash
/dev/sda1: UUID="11111111-1b1b-1c1c-1d1d-1e1e1e1e1e1e" TYPE="ext4"
/dev/sdb1: UUID="22222222-2b2b-2c2c-2d2d-2e2e2e2e2e2e" TYPE="ext4"
/dev/sdc1: UUID="33333333-3b3b-3c3c-3d3d-3e3e3e3e3e3e" TYPE="ext4"
```

>[AZURE.NOTE] Forkert redigere filen **/etc/fstab** kan medføre et ikke kan startes system. Hvis det er sikker på, finder den fordeling dokumentationen til oplysninger om, hvordan korrekt redigere denne fil. Det anbefales også, at der oprettes en sikkerhedskopi af filen /etc/fstab før du kan redigere.

Derefter skal du åbne filen **/etc/fstab** i et tekstredigeringsprogram:

```bash
sudo vi /etc/fstab
```

I dette eksempel skal bruge vi UUID værdien for den nye **/dev/sdc1** enhed, der er oprettet i de forrige trin og tilslutningspunkt **/datadrive**. Tilføj følgende linje til slutningen af filen **/etc/fstab** :

```bash
UUID=33333333-3b3b-3c3c-3d3d-3e3e3e3e3e3e   /datadrive   ext4   defaults,nofail   1   2
```

>[AZURE.NOTE] Fjerne en datadisk uden at redigere fstab senere kan forårsage VM ikke kan starte. De fleste salgsdistributioner angive enten den `nofail` og/eller `nobootwait` fstab indstillinger. Disse indstillinger giver mulighed for et system til at starte, selvom der opstår fejl på disken til at tilslutte på starttidspunktet. Se din fordeling dokumentationen til flere oplysninger om disse parametre.

>[AZURE.NOTE] Indstillingen **nofail** sikrer, at VM starter selvom filsystemet er beskadiget eller disken ikke findes på starttidspunktet. Uden denne indstilling, kan der opstå funktionsmåde, som beskrevet i [Kan SSH til Linux VM på grund af FSTAB fejl](https://blogs.msdn.microsoft.com/linuxonazure/2016/07/21/cannot-ssh-to-linux-vm-after-adding-data-disk-to-etcfstab-and-rebooting/)


### <a name="trimunmap-support-for-linux-in-azure"></a>TRIM/UNMAP understøttelse af Linux i Azure
Nogle Linux kerner understøtter TRIM/UNMAP handlinger for at slette ubrugte blokke på disken. Dette er især praktisk i standard lagerplads til at informere Azure, slettes sider er ikke længere gyldige, og du kan kassere. Dette kan gemme omkostninger, hvis du opretter store filer og derefter slette dem.

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

- Husk, at nye disken ikke er tilgængelig til VM Hvis det genstarter, medmindre du skrive disse oplysninger til filen [fstab](http://en.wikipedia.org/wiki/Fstab) .
- For at sikre din Linux VM er konfigureret korrekt, gennemse [Optimer Linux maskine ydeevnen](virtual-machines-linux-optimization.md) anbefalinger.
- Udvid din lagerkapacitet ved at tilføje flere diske og [konfigurere RAID](virtual-machines-linux-configure-raid.md) til yderligere ydeevne.
