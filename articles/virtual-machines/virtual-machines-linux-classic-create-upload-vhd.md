<properties
    pageTitle="Oprette og overføre en Linux VHD | Microsoft Azure"
    description="Oprette og overføre en Azure virtuel harddisk (Virtuelle) med den klassiske implementeringsmodel, der indeholder Linux-operativsystemet."
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
    ms.date="09/01/2016"
    ms.author="iainfou"/>

# <a name="creating-and-uploading-a-virtual-hard-disk-that-contains-the-linux-operating-system"></a>Oprette og overføre en virtuel harddisk, der indeholder Linux-operativsystemet

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]Du kan også [overføre et brugerdefineret disk billede med Azure ressourcestyring](virtual-machines-linux-upload-vhd.md).

I denne artikel beskrives, hvordan du opretter og overføre en virtuel harddisk (Virtuelle), så du kan bruge det som dit eget billede til at oprette virtuelle maskiner i Azure. Lær at forberede operativsystemet, så du kan bruge den til at oprette flere virtuelle maskiner, der er baseret på billedet. 

>  [AZURE.NOTE] Hvis du har et øjeblik, Hjælp os med at forbedre Azure Linux VM dokumentationen ved at tage dette [Hurtig undersøgelse](https://aka.ms/linuxdocsurvey) oplevelser. Alle svar hjælper os Hjælp du udføre dit arbejde.

## <a name="prerequisites"></a>Forudsætninger
I denne artikel antager, at du har følgende elementer:

- **Linux-operativsystem, der er installeret i en .vhd-fil** – du har installeret en [Azure-godkendt Linux fordeling](virtual-machines-linux-endorsed-distros.md) (eller se [oplysninger for ikke-godkendt salgsdistributioner](virtual-machines-linux-create-upload-generic.md)) til en virtuel disk i formatet Virtuelle. Der findes flere værktøjer for at oprette en VM og Virtuelle:
    - Installere og konfigurere [QEMU](https://en.wikibooks.org/wiki/QEMU/Installing_QEMU) eller [KVM](http://www.linux-kvm.org/page/RunningKVM), tager sig bruge Virtuelle som et billedformat. Hvis det er nødvendigt, kan du [konvertere et billede](https://en.wikibooks.org/wiki/QEMU/Images#Converting_image_formats) ved hjælp af `qemu-img convert`.
    - Du kan også bruge Hyper-V [på Windows 10](https://msdn.microsoft.com/virtualization/hyperv_on_windows/quick_start/walkthrough_install) eller [Windows Server 2012-2012 R2](https://technet.microsoft.com/library/hh846766.aspx).

> [AZURE.NOTE] Det nyere VHDX format understøttes ikke i Azure. Når du opretter en VM, kan du angive Virtuelle som format. Hvis det er nødvendigt, kan du konvertere VHDX diske til Virtuelle ved hjælp af [`qemu-img convert`](https://en.wikibooks.org/wiki/QEMU/Images#Converting_image_formats) eller [`Convert-VHD`](https://technet.microsoft.com/library/hh848454.aspx) PowerShell-cmdlet. Desuden understøtter Azure ikke overførsel dynamiske virtuelle harddiske, så du behøver at konvertere sådanne diske til statisk virtuelle harddiske før du overfører. Du kan bruge funktioner såsom [Azure Virtuelle funktioner til gå](https://github.com/Microsoft/azure-vhd-utils-for-go) til at konvertere dynamiske diske under processen med at overføre til Azure.

- **Azure kommandolinjen** - Installer den seneste [Azure kommandolinjen](../virtual-machines-command-line-tools.md) til at overføre den virtuelle harddisk.

<a id="prepimage"> </a>
## <a name="step-1-prepare-the-image-to-be-uploaded"></a>Trin 1: Forbered billedet skal overføres

Azure understøtter forskellige Linux salgsdistributioner (se [Godkendt Salgsdistributioner](virtual-machines-linux-endorsed-distros.md)). I følgende artikler fører dig gennem Sådan forberedes forskellige Linux fordelingerne, der understøttes på Azure. Når du har fuldført trinnene i følgende vejledninger, komme tilbage her, når du har en Virtuelle-fil, der er klar til at overføre til Azure:

- **[CentOS-baserede Salgsdistributioner](virtual-machines-linux-create-upload-centos.md)**
- **[Debian Linux](virtual-machines-linux-debian-create-upload-vhd.md)**
- **[Oracle Linux](virtual-machines-linux-oracle-create-upload-vhd.md)**
- **[Red Hat Enterprise Linux](virtual-machines-linux-redhat-create-upload-vhd.md)**
- **[SLES & openSUSE](virtual-machines-linux-suse-create-upload-vhd.md)**
- **[Ubuntu](virtual-machines-linux-create-upload-ubuntu.md)**
- **[Andre - ikke godkendt Salgsdistributioner](virtual-machines-linux-create-upload-generic.md)**

> [AZURE.NOTE] Azure-platformen SLA gælder for virtuelle maskiner, der kører Linux OS kun, når en af påtegnede fordelingerne bruges sammen med oplysninger om konfigurationen er angivet under 'Understøttes versioner' i [Linux på Azure-Endorsed Salgsdistributioner](virtual-machines-linux-endorsed-distros.md). Alle Linux salgsdistributioner i billedgalleriet Azure er påtegnede salgsdistributioner med de påkrævede konfiguration.

Se også **[Linux Installationsnoter](virtual-machines-linux-create-upload-generic.md#general-linux-installation-notes)** mere generelle tip til forberedelse Linux billeder til Azure.


<a id="connect"> </a>
## <a name="step-2-prepare-the-connection-to-azure"></a>Trin 2: Forberede forbindelsen til Azure

Sørg for, at du bruger Azure CLI i den klassiske implementeringsmodel (`azure config mode asm`), derefter logge på din konto:

```
azure login
```


<a id="upload"> </a>
## <a name="step-3-upload-the-image-to-azure"></a>Trin 3: Overføre billedet til Azure

Du har brug for en lagerplads konto for at overføre filen Virtuelle til. Du kan enten vælge et eksisterende lagerplads firma eller [oprette en ny](../storage/storage-create-storage-account.md).

Brug Azure CLI til at overføre billedet ved hjælp af følgende kommando:

```bash
azure vm image create <ImageName> `
    --blob-url <BlobStorageURL>/<YourImagesFolder>/<VHDName> `
    --os Linux <PathToVHDFile>
```

I det forrige eksempel:

- **BlobStorageURL** er URL-adressen for kontoen lagerplads du planlægger at bruge
- **YourImagesFolder** er objektbeholderen i blob-lager, hvor du vil gemme dine billeder
- **VHDName** er det navn, der vises i portalen for at identificere den virtuelle harddisk.
- **PathToVHDFile** er den fulde sti og navnet på .vhd-filen på din computer.

Følgende viser en komplet eksempel:

```bash
azure vm image create UbuntuLTS `
    --blob-url https://teststorage.blob.core.windows.net/vhds/UbuntuLTS.vhd `
    --os Linux /home/ahmet/UbuntuLTS.vhd
```

## <a name="step-4-create-a-vm-from-the-image"></a>Trin 4: Oprette en VM fra billedet
Du opretter en VM ved hjælp af `azure vm create` på samme måde som en almindelig VM. Angiv det navn, du har givet dit billede i det forrige trin. I følgende eksempel bruge vi **UbuntuLTS** billede navnet fremgår af ovenstående trin:

```bash
azure vm create --userName ops --password P@ssw0rd! --vm-size Small --ssh `
    --location "West US" "DeployedUbuntu" UbuntuLTS
```

For at oprette din egen FOS skal du give dine egne brugernavn + adgangskode, placering, DNS-navn og billede navn.

## <a name="next-steps"></a>Næste trin

Du kan finde yderligere oplysninger finder [Azure CLI reference til Azure klassisk implementeringsmodel](../virtual-machines-command-line-tools.md).

[Step 1: Prepare the image to be uploaded]: #prepimage
[Step 2: Prepare the connection to Azure]: #connect
[Step 3: Upload the image to Azure]: #upload
