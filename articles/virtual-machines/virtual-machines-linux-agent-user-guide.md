<properties 
    pageTitle="Brugervejledning til Linux Agent | Microsoft Azure" 
    description="Lær, hvordan du installerer og konfigurerer Linux Agent (waagent) for at administrere din virtuelle maskine interaktion med Azure-strukturen Controller." 
    services="virtual-machines-linux" 
    documentationCenter="" 
    authors="szarkos" 
    manager="timlt" 
    editor=""
    tags="azure-service-management,azure-resource-manager" />

<tags 
    ms.service="virtual-machines-linux" 
    ms.workload="infrastructure-services" 
    ms.tgt_pltfrm="vm-linux" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="10/17/2016" 
    ms.author="szark"/>



# <a name="azure-linux-agent-user-guide"></a>Brugervejledning til Azure Linux Agent

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-both-include.md)]

## <a name="introduction"></a>Introduktion

Microsoft Azure Linux Agent (waagent) administrerer Linux og FreeBSD klargøring og VM interaktion med Azure-strukturen Controller. Det indeholder følgende funktioner til Linux og FreeBSD IaaS installationer:

> [AZURE.NOTE] Se Azure Linux agent [vigtige oplysninger om](https://github.com/Azure/WALinuxAgent/blob/master/README.md) yderligere oplysninger.

* **Klargøring af billede**
  - Oprettelse af en brugerkonto
  - Konfigurere SSH godkendelsestyper
  - Installation af SSH offentlige nøgler og vigtige par
  - Angive værtsnavnet
  - Publicere værtsnavnet på platform DNS
  - Rapportering SSH host vigtige fingeraftryk til platformen
  - Ressourcestyring Disk
  - Formatering og tilslutte ressource disk
  - Konfigurere Udskift mellemrum

* **Netværk**
  - Administrerer omdirigerer for at forbedre kompatibilitet med platform DHCP-servere
  - Sikrer, at interface netværksnavn

* **Kernen**
  - Konfigurerer virtuelle NUMA (deaktivere for kernen < 2.6.37)
  - Forbruger Hyper-V entropi for /dev/random
  - Konfigurerer SCSI timeout for rod enheden (hvilket kunne remote)

* **Diagnosticering**
  - Console omdirigering til den serielle port

* **SCVMM installationer**
    - Registrerer og starter VMM agent for Linux, når du kører i et System Center Virtual Machine Manager 2012 R2-miljø

* **VM lokalnummer**
    - Indsæt komponent, der er oprettet af Microsoft og partnere til Linux VM (IaaS) til at aktivere software og konfiguration automatisering
    - VM lokalnummer referenceimplementering på [https://github.com/Azure/azure-linux-extensions](https://github.com/Azure/azure-linux-extensions)


## <a name="communication"></a>Kommunikation
Strømmen oplysninger fra platformen til agenten sker via to kanaler:

* En starttidspunktet vedhæftet DVD til IaaS installationer. Denne DVD indeholder en OVF-kompatibel konfigurationsfil, der indeholder alle klargøring oplysninger end den faktiske SSH keypairs.
* Et TCP-slutpunkt udsætte en REST-API bruges til at få installation og konfiguration af topologi.


## <a name="requirements"></a>Krav
Følgende systemer er testet og er blevet konstateret, at arbejde med Azure Linux Agent:

> [AZURE.NOTE] Denne liste kan variere fra den officielle liste over understøttede systemer på Microsoft Azure-platformen, som beskrevet her: [http://support.microsoft.com/kb/2805216](http://support.microsoft.com/kb/2805216)

* CoreOS
* CentOS 6.3 +
* Red Hat Enterprise Linux 6,7 +
* Debian 7.0 +
* Ubuntu 12.04 +
* openSUSE 12.3 +
* SLES 11 SP3 +
* Oracle Linux 6.4 +

Andre understøttede systemer:

* FreeBSD 10 + (Azure Linux Agent v2.0.10 +)

Nogle systempakker afhænger af Linux agent for at kunne fungere korrekt:

* Python 2.6 +
* OpenSSL 1.0 +
* OpenSSH 5.3 +
* Filsystemet værktøjer: sfdisk, fdisk, mkfs, delte
* Adgangskode værktøjer: chpasswd, sudo
* Tekst, der behandler værktøjer: sed, grep
* Værktøjer: IP-rute
* Kernen support til tilslutte UDF filesystems.

## <a name="installation"></a>Installation
Installation ved hjælp af en RPM eller en DEB pakke fra din fordeling pakke lager er den foretrukne metode til at installere og opgradere Azure Linux Agent. Alle de [godkendt fordeling udbydere](virtual-machines-linux-endorsed-distros.md) integrere pakken Azure Linux agent i deres billeder og typer lagre.

Se i dokumentationen i [Azure Linux Agent repo på Github](https://github.com/Azure/WALinuxAgent) for indstillinger for avanceret installation, såsom installation fra kilde eller til brugerdefineret placeringer eller præfikser.


## <a name="command-line-options"></a>Kommandolinjeparametre

### <a name="flags"></a>Flag

- detaljeret: Forøg oplysningerne i angivne kommando
- Gennemtving: springe interaktive bekræftelse af visse kommandoer

### <a name="commands"></a>Kommandoer

- Hjælp: Viser de understøttede kommandoer og flag.

- deprovision: forsøger at rydde systemet, så det passer til klargøring af igen. Denne handling slettes følgende:
 * Alle SSH host taster (hvis Provisioning.RegenerateSshHostKeyPair er 'y' i konfigurationsfilen)
 * Konfiguration af NameServer i /etc/resolv.conf
 * Rod adgangskode fra /etc/shadow (hvis Provisioning.DeleteRootPassword er 'y' i konfigurationsfilen)
 * Cachelagrede DHCP-klient rettigheder
 * Nulstiller værtsnavn til localhost.localdomain


> [AZURE.WARNING] Ophævelse af klargøring garanterer ikke, at billedet er ikke markeret med alle følsomme oplysninger og egnet til videredistribution.


- deprovision + user: udfører alt under - deprovision (ovenfor) og også sletter den sidste klargjorte brugerkonto (der fås fra /var/lib/waagent) og tilhørende data. Denne parameter er når deaktivere et billede, der tidligere klargøring af Azure, så den kan hentes og bruges igen.

- version: Viser versionen af waagent

- serialconsole: konfigurerer rydde for at markere ttyS0 (den første serielle port) som konsollen Start. Dette sikrer, at kernen Start logfiler er sendt til den serielle port og gjort tilgængelige til fejlfinding.

- daemon: køre waagent som en daemon til at administrere interaktion med platformen. Dette argument er angivet til waagent i waagent initialisering scriptet.

- Start: køre waagent som en i baggrunden


## <a name="configuration"></a>Konfiguration

En konfigurationsfil (/ etc/waagent.conf) styrer waagent handlinger. Et eksempel på konfigurationsfil er vist nedenfor:

    Provisioning.Enabled=y
    Provisioning.DeleteRootPassword=n
    Provisioning.RegenerateSshHostKeyPair=y
    Provisioning.SshHostKeyPairType=rsa
    Provisioning.MonitorHostName=y
    Provisioning.DecodeCustomData=n
    Provisioning.ExecuteCustomData=n
    Provisioning.PasswordCryptId=6
    Provisioning.PasswordCryptSaltLength=10
    ResourceDisk.Format=y
    ResourceDisk.Filesystem=ext4
    ResourceDisk.MountPoint=/mnt/resource
    ResourceDisk.MountOptions=None
    ResourceDisk.EnableSwap=n
    ResourceDisk.SwapSizeMB=0
    LBProbeResponder=y
    Logs.Verbose=n
    OS.RootDeviceScsiTimeout=300
    OS.OpensslPath=None
    HttpProxy.Host=None
    HttpProxy.Port=None

De indstillinger, der er beskrevet i detaljeret nedenfor. Konfigurationsindstillinger, der er tre typer Boolesk værdi, en streng eller heltal. Boolesk konfigurationsindstillinger kan angives som "j" eller "n". Speciel nøgleordet "Ingen" kan bruges til visse streng type konfigurationsposter som beskrevet nedenfor.

**Provisioning.Enabled:**  
Type: boolesk  
Som standard: y

Dette gør det muligt for brugeren at aktivere eller deaktivere funktionen klargøring i agenten. Gyldige værdier er "j" eller "n". Hvis klargøring er deaktiveret, bevares SSH host og bruger nøgler i billedet, og en hvilken som helst konfiguration, der er angivet i Azure klargøring API ignoreres.

> [AZURE.NOTE] Den `Provisioning.Enabled` parameter som standard til "n" på Ubuntu skyen billeder, der bruger skyen initialisering til klargøring af.

  
**Provisioning.DeleteRootPassword:**  
Type: boolesk  
Som standard: n

Hvis sæt roden adgangskoden i feltet/osv/skygge fil slettes under processen klargøring.


**Provisioning.RegenerateSshHostKeyPair:**  
Type: boolesk  
Som standard: y

Hvis sæt, alle SSH host vigtige par (ecdsa, dsa og rsa) slettes under processen klargøring fra /etc/ssh /. Og et enkelt nyt vigtige par genereres.

Krypteringstypen til nyt nøglerne kan konfigureres med posten, Provisioning.SshHostKeyPairType. Vær opmærksom på, at opretter nogle fordelingerne igen SSH vigtige par for eventuelle manglende krypteringstyper, når SSH daemonen genstartes (for eksempel på en genstart).


**Provisioning.SshHostKeyPairType:**  
Type: streng  
Som standard: rsa

Dette kan angives typen kryptering algoritme, der understøttes af SSH daemonen på den virtuelle maskine. Typisk understøttede værdier er "rsa", "dsa" og "ecdsa". Bemærk, at "putty.exe" på Windows ikke understøtter "ecdsa". Men hvis du vil bruge putty.exe på Windows til at oprette forbindelse til en Linux-installation, skal du bruge "rsa" eller "dsa".


**Provisioning.MonitorHostName:**  
Type: boolesk  
Som standard: y

Hvis angivet, waagent vil overvåge Linux virtuel maskine til hostname ændringer (som returneres af kommandoen "hostname") og automatisk opdatere netværk konfigurationen i billedet for at afspejle ændringen. Hvis du vil overføre ændringen af navnet til DNS-serverne, genstartes netværk i virtual machine. Dette vil kort medføre tab af forbindelse til internettet.


**Provisioning.DecodeCustomData**  
Type: boolesk  
Som standard: n

Hvis angivet, waagent afkoder CustomData fra Base64.


**Provisioning.ExecuteCustomData**  
Type: boolesk  
Som standard: n

Hvis angivet, waagent udføres CustomData efter klargøring af.


**Provisioning.PasswordCryptId**  
Strengtypen:  
Standard: 6

Blev brugt af crypt, når der genereres adgangskode hash.  
 1 – MD5  
 2a - Blowfish  
 5 – SHA-256  
 6 – SHA-512  


**Provisioning.PasswordCryptSaltLength**  
Strengtypen:  
Standard: 10

Længden af tilfældige salt bruges ved oprettelse adgangskode hash.


**ResourceDisk.Format:**  
Type: boolesk  
Som standard: y

Hvis angivet, ressource disken, som platformen formateres og fastgøres med waagent, hvis filsystemtypen anmodet af brugeren i "ResourceDisk.Filesystem" er noget andet end "ntfs". En enkelt partition af typen Linux (83) gøres tilgængelige på disken. Bemærk, at denne partition ikke der formateres, hvis det kan være tilsluttet korrekt.


**ResourceDisk.Filesystem:**  
Type: streng  
Som standard: ext4

Dette angiver, hvilken filsystemet for ressource disken. Understøttede værdier varierer afhængigt af Linux fordeling. Hvis strengen er X, derefter mkfs. X skal være til stede på Linux billedet. SLES 11 billeder skal typisk bruge 'ext3'. FreeBSD billeder skal bruge 'ufs2' her.


**ResourceDisk.MountPoint:**  
Type: streng  
Standard: / mnt/ressource 

Dette angiver den sti, hvormed ressource disken er tilsluttet. Bemærk, at ressource disken er en *midlertidig* disk, og kan være blevet tømt når VM er fjernet.


**ResourceDisk.MountOptions**  
Type: streng  
Som standard: ingen

Angiver disk Indlæs indstillinger, der skal overføres til kommandoen Indlæs -o. Dette er en kommasepareret liste over værdier, ex. 'nodev, nosuid'. Se mount(8) få mere at vide.


**ResourceDisk.EnableSwap:**  
Type: boolesk  
Som standard: n

Hvis angive en Byt om fil (/ swapfile) er oprettet på disken ressource og føjes til systemet Udskift mellemrum.


**ResourceDisk.SwapSizeMB:**  
Type: heltal  
Som standard: 0

Størrelsen af filen i megabyte.


**Logs.Verbose:**  
Type: boolesk  
Som standard: n

Hvis sæt, log oplysningerne er øget. Waagent logfører /var/log/waagent.log og bruger funktionen system logrotate for at rotere logfiler.


**OS. EnableRDMA**  
Type: boolesk  
Som standard: n

Hvis angivet, agenten forsøger at installere og derefter indlæse en RDMA kernen driver, der stemmer overens med versionen af firmware på den underliggende hardware.


**OS. RootDeviceScsiTimeout:**  
Type: heltal  
Som standard: 300

Dette konfigurerer SCSI timeout i sekunder på OS disk og data drev. Hvis du ikke angive standardindstillinger for anvendes systemet.


**OS. OpensslPath:**  
Type: streng  
Som standard: ingen

Dette kan bruges til at angive en alternativ sti til den binære skal bruges til cryptographic handlinger openssl.


**HttpProxy.Host, HttpProxy.Port**  
Type: streng  
Som standard: ingen

Hvis angivet, agenten anvender denne proxy-server til at få adgang til internettet. 


## <a name="ubuntu-cloud-images"></a>Ubuntu skyen billeder

Bemærk, at Ubuntu skyen billeder udnytte [skyen initialisering](https://launchpad.net/ubuntu/+source/cloud-init) for at udføre mange konfigurationsopgaver, der ellers ville administreres af Azure Linux Agent.  Vær opmærksom på følgende forskelle:


- Som standard **Provisioning.Enabled** "n" på Ubuntu skyen billeder, der bruger skyen initialisering til at udføre klargøring opgaver.

- Følgende konfigurationsparametre har ingen indvirkning på Ubuntu skyen billeder, der bruger skyen initialisering til at administrere ressource disken og Ombyt mellemrum:

 - **ResourceDisk.Format**
 - **ResourceDisk.Filesystem**
 - **ResourceDisk.MountPoint**
 - **ResourceDisk.EnableSwap**
 - **ResourceDisk.SwapSizeMB**

- Se følgende ressourcer til at konfigurere ressource disk tilslutningspunktet og Ombyt plads på Ubuntu skyen billeder under klargøringen:

 - [Ubuntu Wiki: Konfigurer Byt om partitioner](http://go.microsoft.com/fwlink/?LinkID=532955&clcid=0x409)
 - [Indsprøjtning brugerdefinerede Data i en Azure virtuelt](virtual-machines-windows-classic-inject-custom-data.md)

