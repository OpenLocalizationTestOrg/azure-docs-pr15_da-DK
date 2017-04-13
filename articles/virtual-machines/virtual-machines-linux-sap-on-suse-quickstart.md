<properties
   pageTitle="Test af SAP NetWeaver på Microsoft Azure SUSE Linux FOS | Microsoft Azure"
   description="Test af SAP NetWeaver på Microsoft Azure SUSE Linux FOS"
   services="virtual-machines-linux"
   documentationCenter=""
   authors="hermanndms"
   manager="timlt"
   editor=""
   tags="azure-resource-manager"
   keywords=""/>
<tags
   ms.service="virtual-machines-linux"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="vm-linux"
   ms.workload="infrastructure-services"
   ms.date="09/15/2016"
   ms.author="hermannd"/>

# <a name="running-sap-netweaver-on-microsoft-azure-suse-linux-vms"></a>Igangværende SAP NetWeaver på Microsoft Azure SUSE Linux FOS


I denne artikel beskrives forskellige ting, du skal overveje, når du kører SAP NetWeaver på virtuelle Microsoft Azure SUSE Linux-computere (FOS). SAP NetWeaver understøttes og maj 19 2016 officielt på SUSE Linux FOS på Azure. Alle oplysninger om Linux versioner, SAP kerneversioner og så videre kan findes i SAP Note 1928533 "SAP-programmer på Azure: understøttes produkter og Azure VM typer".
Yderligere dokumentation om SAP på Linux FOS kan findes her: [Brug af SAP på Linux virtuelle maskiner (VM'er)](virtual-machines-linux-sap-get-started.md).


Følgende oplysninger kan hjælpe dig med at undgå nogle mulige faldgruber.

## <a name="suse-images-on-azure-for-running-sap"></a>SUSE billeder på Azure til kørsel af SAP

Brug kun SUSE Linux Enterprise Server SLES 12 (SPx) til kørsel af SAP NetWeaver på Azure, – se også SAP note 1928533. En speciel SUSE billede er i Azure Marketplace ("SLES 11 SP3 til SAP CAL"), men det er ikke beregnet til generel brug. Brug ikke dette billede, fordi det er reserveret til løsningen [SAP skyen maskinen bibliotek](https://cal.sap.com/) .  

Du skal bruge Azure Ressourcestyring for alle nye test og installationer på Azure. Du kan lede efter SUSE SLES billeder og versioner ved hjælp af Azure PowerShell eller Azure kommandolinjen (CLI), kan du bruge følgende kommandoer. Du kan derefter bruge output, for eksempel til at definere OS billedet i en JSON-skabelon til at installere en ny SUSE Linux VM.
Disse PowerShell-kommandoer er gyldige for Azure PowerShell version 1.0.1 eller nyere.

* Se efter eksisterende udgivere, herunder SUSE:

   ```
   PS  : Get-AzureRmVMImagePublisher -Location "West Europe"  | where-object { $_.publishername -like "*US*"  }
   CLI : azure vm image list-publishers westeurope | grep "US"
   ```

* Se efter eksisterende tilbud fra SUSE:

   ```
   PS  : Get-AzureRmVMImageOffer -Location "West Europe" -Publisher "SUSE"
   CLI : azure vm image list-offers westeurope SUSE
   ```

* Se efter SUSE SLES tilbud:

   ```
   PS  : Get-AzureRmVMImageSku -Location "West Europe" -Publisher "SUSE" -Offer "SLES"
   CLI : azure vm image list-skus westeurope SUSE SLES
   ```

* Se efter en bestemt version af en SLES SKU:

   ```
   PS  : Get-AzureRmVMImage -Location "West Europe" -Publisher "SUSE" -Offer "SLES" -skus "12"
   CLI : azure vm image list westeurope SUSE SLES 12
   ```

## <a name="installing-walinuxagent-in-a-suse-vm"></a>Installere WALinuxAgent i en SUSE VM

Agent kaldet WALinuxAgent er en del af SLES billederne i Azure Marketplace. Finde oplysninger om at installere den manuelt (for eksempel, når du overfører en SLES OS virtuel harddisk (Virtuelle) fra det lokale), i:

- [OpenSUSE] (http://software.opensuse.org/package/WALinuxAgent)

- [Azure] (virtuelle-maskiner-linux-godkendt-distros.md)

- [SUSE] (https://www.suse.com/communities/blog/suse-linux-enterprise-server-configuration-for-windows-azure/)

## <a name="sap-enhanced-monitoring"></a>SAP "udvidet overvågning"

SAP "udvidet overvågning" er et obligatorisk betingelse til at køre SAP på Azure. Kontroller, at oplysningerne i SAP Bemærk 2191498 "SAP på Linux med Azure: udvidet overvågning".

## <a name="attaching-azure-data-disks-to-an-azure-linux-vm"></a>Vedhæfte Azure datadisce til en Azure Linux VM

Du bør aldrig tilslutningen Azure datadisce til en Azure Linux VM ved hjælp af enheds-ID. I stedet bruge globalt entydigt id (UUID). Vær forsigtig, når du bruger grafiske værktøjer til tilslutningen Azure data disk f.eks. Kontrollér posterne i /etc/fstab.

Problem med enheds-ID er, at det kan ændres, og derefter Azure VM kan hænge i startprocessen. Hvis du vil reducere problemet, kan du tilføje parameteren nofail i /etc/fstab. Men vær forsigtig med nofail fordi programmer kan bruge tilslutningspunktet som før, og kan skrive i filsystemet roden i tilfælde af en ekstern Azure datadisk ikke blev tilsluttet under start.

Den eneste undtagelse tilslutning via UUID er vedhæfte en OS-cd til fejlfinding, som beskrevet i følgende afsnit.

## <a name="troubleshooting-a-suse-vm-that-isnt-accessible-anymore"></a>Fejlfinding i forbindelse med en SUSE VM, der ikke længere er tilgængelig

Der kan være situationer, hvor en SUSE VM på Azure hænger i startprocessen (for eksempel med en fejl, der er relateret til tilslutte diske). Du kan kontrollere dette problem ved hjælp af funktionen Start diagnosticering til virtuelle Azure-computere v2 i portalen Azure. Få mere at vide under [Start diagnostics] (https://azure.microsoft.com/blog/boot-diagnostics-for-virtual-machines-v2/).

En metode til at løse problemet er at vedhæfte OS disken fra den beskadigede VM til en anden SUSE VM på Azure. Derefter foretage de nødvendige ændringer som redigering /etc/fstab eller fjerne netværk udev regler, som beskrevet i næste afsnit.

Der er et vigtigt at overveje. Implementering af flere SUSE FOS fra det samme Azure Marketplace-billede (for eksempel SLES 11 SP4) får OS disken, der skal altid være fastgøres med samme UUID. Derfor medfører bruger UUID vedhæfte en OS disk fra en anden VM, der blev installeret ved hjælp af det samme billede med Azure Marketplace to identiske UUID'er. Dette forårsager problemer og kan betyde, at VM beregnet til fejlfinding i forbindelse med faktisk starter fra OS vedhæftede og beskadigede disken i stedet for det oprindelige niveau.

Der er to måder at undgå dette:

* Bruge et andet Azure Marketplace-billede til fejlfinding i forbindelse med VM (for eksempel SLES 11 SPx i stedet for SLES 12).
* Ikke vedhæfte den beskadigede OS disk fra en anden VM ved hjælp af UUID – Brug noget andet.

## <a name="uploading-a-suse-vm-from-on-premises-to-azure"></a>Overførsel af en SUSE VM fra det lokale til Azure

Se en beskrivelse af disse trin for at overføre en SUSE VM fra det lokale til Azure [forberede en SLES eller openSUSE virtuel maskine til Azure] (virtual-machines-linux-suse-create-upload-vhd.md).

Hvis du vil overføre en VM uden trinnet deprovision i slutningen (for eksempel til Behold en eksisterende SAP-installation, samt værtsnavnet), skal du kontrollere følgende elementer:

* Sørg for, at OS disken er tilsluttet ved hjælp af UUID og ikke enheds-ID. Ændre til UUID kun på /etc/fstab er ikke nok til OS disken. Også huske at tilpasse Start loader via YaST eller ved at redigere /boot/grub/menu.lst.
* Hvis du bruger formatet VHDX for SUSE OS disken og konvertere den til Virtuelle til overførsel til Azure, er det sandsynligvis, IP-adresse, ændres fra eth0 til eth1. For at undgå problemer, når du Start på Azure senere, skal du ændre tilbage til eth0, som beskrevet i [rette eth0 i klonede SLES 11 VMware] (https://dartron.wordpress.com/2013/09/27/fixing-eth1-in-cloned-sles-11-vmware/).

Ud over hvad er beskrevet i artiklen, anbefaler vi, at du fjerner dette:

   /lib/udev/rules.d/75-persistent-NET-generator.Rules

Du kan også installere Azure Linux Agent (waagent) for at hjælpe dig med at undgå problemer, som der ikke er flere netværkskort.

## <a name="deploying-a-suse-vm-on-azure"></a>Implementere en SUSE VM på Azure

Du skal oprette nye SUSE FOS ved hjælp af JSON skabelonfiler i den nye Azure ressourcestyring model. Når JSON skabelonfilen er oprettet, kan du installere VM ved hjælp af kommandoen følgende CLI som et alternativ til PowerShell:

   ```
   azure group deployment create "<deployment name>" -g "<resource group name>" --template-file "<../../filename.json>"

   ```
Du kan finde flere oplysninger om JSON skabelonfiler, se [redigering Azure ressourcestyring skabeloner] (ressource-gruppe-redigering-templates.md) og [Azure Hurtig start skabeloner] (https://azure.microsoft.com/documentation/templates/).

Du kan finde flere oplysninger om CLI og Azure ressourcestyring se [Brug Azure CLI til Mac, Linux og Windows med Azure ressourcestyring] (xplat-cli-azure-ressource-manager.md).

## <a name="sap-license-and-hardware-key"></a>SAP licens og hardware-tasten

En ny funktion blev introduceret for at beregne tasten SAP hardware, der bruges til SAP-licens til officiel certificering af SAP-Azure. SAP kernen skulle tilpasses for at gøre brug af dette. Tidligere SAP kerneversioner til Linux omfatter ikke denne ændring af koden. Derfor i visse situationer (for eksempel Azure VM ændre størrelsen på), tasten SAP hardware ændret og SAP licensen blev ikke længere gyldige. Dette er løst i de seneste SAP Linux kerner. Få mere at vide skal du se SAP note 1928533.

## <a name="suse-sapconf-package--tuned-adm"></a>SUSE sapconf pakke / indstillet adm

SUSE indeholder en pakke, kaldet "sapconf", der administrerer et sæt af SAP-specifikke indstillinger. Yderligere oplysninger om denne pakke gør, og hvordan du kan installere og bruge det, du [ved hjælp af sapconf til at forberede en SUSE Linux Enterprise Server til at køre SAP-systemer] (https://www.suse.com/communities/blog/using-sapconf-to-prepare-suse-linux-enterprise-server-to-run-sap-systems/) og [Hvad er sapconf, eller hvordan du forbereder et SUSE Linux Enterprise Server til kørsel af SAP-systemer?] (http://scn.sap.com/community/linux/blog/2014/03/31/what-is-sapconf-or-how-to-prepare-a-suse-linux-enterprise-server-for-running-sap-systems).

I mellemtiden er der et nyt værktøj, der erstatter sapconf - indstillet adm. En kan finde flere oplysninger om dette værktøj, følge to linkene nedenfor.

SLES dokumentation om indstillet adm profil sap-hana kan findes [her](https://www.suse.com/documentation/sles-for-sap-12/book_s4s/data/sec_s4s_configure_sapconf.html) 

Justering systemer til SAP arbejdsmængder med indstillet adm - kan findes [her](https://www.suse.com/documentation/sles-for-sap-12/pdfdoc/book_s4s/book_s4s.pdf) i kapitel 6.2


## <a name="nfs-share-in-distributed-sap-installations"></a>NFS dele i fordelt SAP-installationer

Hvis du har en fordelt installation – for eksempel, hvor du vil installere databasen og SAP application servere i separate VM'er – kan du dele mappen /sapmnt via NFS Network File System (). Hvis du har problemer med med trinnene til installation, når du har oprettet NFS del for /sapmnt, kontrollere, om "no_root_squash" er indstillet til share.

## <a name="logical-volumes"></a>Logiske drev

Tidligere Hvis flere nødvendige logiske store mængder på tværs af forskellige drev og Azure data (for eksempel sikrer SAP-databasen), blev det anbefales at bruge mdadm som lvm ikke blev fuldt godkendt endnu på Azure. Hvis du vil se, hvordan du konfigurerer Linux RAID på Azure ved hjælp af mdadm, se [konfigurere software-RAID på Linux](virtual-machines-linux-configure-raid.md). I mellemtiden og begyndelsen af maj 2016 også lvm understøttes fuldt ud på Azure og kan bruges som et alternativ til mdadm. Du kan finde flere oplysninger om lvm på Azure [Konfigurere LVM på en Linux VM i Azure](virtual-machines-linux-configure-lvm.md).


## <a name="azure-suse-repository"></a>Azure SUSE lager

Hvis du har et problem med adgang til den almindelige Azure SUSE lager, kan du bruge en enkelt kommando til at nulstille den. Dette kan ske, hvis du opretter en privat OS billede i et Azure område og derefter kopiere billedet til et andet område, hvor du vil installere nye VM'er, der er baseret på dette privat OS billede. Kun køre følgende kommando i VM:

   ```
   service guestregister restart
   ```

## <a name="gnome-desktop"></a>Gnome skrivebord

Hvis du vil bruge Gnome computeren til at installere et komplet SAP demo system i en enkelt VM – herunder en SAP-grafiske Brug browser og SAP management console – dette tip til at installere den på Azure SLES billeder:

   For SLES 11:

   ```
   zypper in -t pattern gnome
   ```

   For SLES 12:

   ```
   zypper in -t pattern gnome-basic
   ```

## <a name="sap-support-for-oracle-on-linux-in-the-cloud"></a>SAP understøttelse af Oracle på Linux i skyen

Der er en support begrænsning fra Oracle på Linux i virtuelt miljøer. Selvom det ikke er et emne i Azure-specifikke, er det vigtigt at forstå. SAP understøtter ikke Oracle på SUSE eller Red Hat i en offentlig sky som Azure. Tage direkte kontakt Oracle for at diskutere dette emne.
