<properties
    pageTitle="Introduktion til Linux i Azure | Microsoft Azure"
    description="Få mere at vide om at bruge Linux virtuelle maskiner på Azure."
    services="virtual-machines-linux"
    documentationCenter="python"
    authors="szarkos"
    manager="timlt"
    editor=""
    tags="azure-resource-manager,azure-service-management"/>

<tags
    ms.service="virtual-machines-linux"
    ms.workload="infrastructure-services"
    ms.tgt_pltfrm="vm-linux"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/24/2016"
    ms.author="szark"/>

#<a name="introduction-to-linux-on-azure"></a>Introduktion til Linux på Azure

Dette emne indeholder en oversigt over nogle aspekter af brugen af Linux virtuelle maskiner i Azure skyen. Implementere en Linux virtuel maskine er nemt at bruge et billede fra galleriet.


## <a name="authentication-usernames-passwords-and-ssh-keys"></a>Godkendelse: Brugernavne, adgangskoder og SSH nøgler

Når du opretter en Linux virtuel maskine, ved hjælp af portalen Azure klassisk, bliver du bedt om at angive et brugernavn, adgangskode eller en SSH offentlig nøgle. Valg af et brugernavn til implementering af en Linux virtuel maskine på Azure, er underlagt følgende begrænsning: navnene på Systemkonti (UID < 100) allerede er til stede i virtual machine er ikke tilladt, 'root' f.eks.


 - Se [oprette en virtuel maskine kører Linux](virtual-machines-linux-quick-create-cli.md)
 - Se, [hvordan du bruger SSH med Linux på Azure](virtual-machines-linux-mac-create-ssh-keys.md)


## <a name="obtaining-superuser-privileges-using-sudo"></a>Bestil superbruger rettigheder ved hjælp af`sudo`

Den brugerkonto, der er angivet under virtuelt forekomst installation på Azure er en privilegerede konto. Denne konto er konfigureret af Azure Linux Agent skal kunne ændre rettigheder til rod (superbruger konto) ved hjælp af den `sudo` utility. Når du er logget på ved hjælp af denne brugerkonto, vil du kunne køre kommandoer som rod ved hjælp af kommandosyntaks

    # sudo <COMMAND>

Du kan eventuelt få rod shell ved hjælp af **sudo -s**.

- Få vist [ved hjælp af rod rettigheder på Linux virtuelle maskiner i Azure](virtual-machines-linux-use-root-privileges.md)


## <a name="firewall-configuration"></a>Konfiguration af Firewall

Azure leverer en indgående pakkefilter, der begrænser connectivity til porte, der er angivet i portalen Azure klassisk. Som standard er den eneste tilladte port SSH. Du kan åbne for adgang til flere porte på computeren Linux virtuelle ved at konfigurere slutpunkter i Azure klassisk portalen:

 - Se: [Sådan konfigurerer slutpunkter til en virtuel maskine](virtual-machines-windows-classic-setup-endpoints.md)

Linux billederne i galleriet Azure aktiverer ikke *iptables* firewall som standard. Hvis du vil, kan firewallen konfigureres for at give flere filtrering.


## <a name="hostname-changes"></a>Hostname ændringer

Når du først installere en forekomst af et Linux-billede, skal du angive et værtsnavn til den virtuelle maskine. Når den virtuelle maskine kører, udgives denne hostname til platform DNS-servere, så flere virtuelle maskiner, der er forbundet med hinanden kan udføre IP-adresseopslag ved hjælp af værtsnavne.

Hvis hostname ændringer er beskedteksten, når en virtuel maskine er blevet installeret, skal du bruge kommandoen

    # sudo hostname <newname>

Azure Linux Agent indeholder funktioner, der registrerer automatisk ændringen navn og konfigurerer korrekt virtuelt fastholdes ændringen og offentliggøre ændringen til platform DNS-servere.

 - [Brugervejledning til Azure Linux Agent](virtual-machines-linux-agent-user-guide.md)

### <a name="cloud-init"></a>Skyen initialisering
**Ubuntu** og **CoreOS** billeder anvender skyen initialisering på Azure, som indeholder flere funktioner til at starte sig selv en virtuel maskine.

 - [Hvordan skal tilføjes brugerdefinerede Data](virtual-machines-windows-classic-inject-custom-data.md)
 - [Brugerdefinerede Data og skyen initialisering på Microsoft Azure](https://azure.microsoft.com/blog/2014/04/21/custom-data-and-cloud-init-on-windows-azure/)
 - [Oprette Azure Udskift partitioner, der bruger skyen initialisering](https://wiki.ubuntu.com/AzureSwapPartitions)
 - [Hvordan du bruger CoreOS på Azure](https://coreos.com/os/docs/latest/booting-on-azure.html)


## <a name="virtual-machine-image-capture"></a>Virtuelt billede registrering

Azure giver mulighed for at registrere tilstanden for en eksisterende virtuelt til et billede, der senere kan bruges til at udrulle yderligere virtuelt forekomster. Azure Linux Agent muligvis bruges til at rollback nogle af de tilpasninger, der blev udført under processen klargøring. Du kan følge nedenstående trin for at registrere en virtuel maskine som et billede:

1. Køre **waagent-deprovision** at fortryde klargøring tilpasning. Eller **waagent-deprovision + bruger** du kan også slette den brugerkonto, der er angivet under klargøring og alle de tilknyttede data.

2. Luk ned/power fra den virtuelle maskine.

3. Klik på *Hent* i portalen Azure klassisk eller bruge Powershell eller CLI værktøjerne til at registrere den virtuelle maskine som et billede.

 - Se: [Sådan registrere en Linux virtuel maskine skal bruges som en skabelon](virtual-machines-linux-classic-capture-image.md)


## <a name="attaching-disks"></a>Vedhæfte diske

Hver virtuelt har en midlertidig, lokal *ressource disk* vedhæftet. Da data på en ressource disk ikke måske er robust på tværs af genstarter, er det ofte bruges af programmer og processer, der kører i virtual machine til forbigående og **midlertidig** lagring af data. Det er også bruges til at gemme siden eller udskifte filer til operativsystemet.

På Linux, ressource disken typisk administreres af Azure Linux Agent og automatisk fastgøres til **/mnt/resource** (eller **/mnt** på Ubuntu billeder).


>[AZURE.NOTE] Bemærk, ressource disken er en **midlertidig** disk og kan slettes og omformateret når VM genstartes.

På Linux data disken kan navngives af kernen som `/dev/sdc`, og brugerne skal partition, formatere og tilslutte ressourcen. Dette er dækket trinvise i selvstudiet: [hvordan du vedhæfter en Data Disk til en virtuel maskine](virtual-machines-linux-classic-attach-disk.md).

 - **Se også:** [Konfigurere Software RAID på Linux](virtual-machines-linux-configure-raid.md)  &  [Konfigurere LVM på en Linux VM i Azure](virtual-machines-linux-configure-lvm.md)

