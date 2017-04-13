<properties
    pageTitle="Oprette og overføre et brugerdefineret billede Linux | Microsoft Azure"
    description="Oprette og overføre en virtuel harddisk (Virtuelle) til Azure med et brugerdefineret Linux billede ved hjælp af Ressourcestyring implementeringsmodel."
    services="virtual-machines-linux"
    documentationCenter=""
    authors="iainfoulds"
    manager="timlt"
    editor="tysonn"
    tags="azure-resource-manager"/>

<tags
    ms.service="virtual-machines-linux"
    ms.workload="infrastructure-services"
    ms.tgt_pltfrm="vm-linux"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/10/2016"
    ms.author="iainfou"/>

# <a name="upload-and-create-a-linux-vm-from-custom-disk-image"></a>Overføre og oprette en Linux VM fra brugerdefinerede disk billede

I denne artikel beskrives, hvordan du kan overføre en virtuel harddisk (Virtuelle) til Azure ved hjælp af Ressourcestyring installation modellen og oprette Linux FOS fra dette brugerdefinerede billede. Denne funktion kan du installere og konfigurere en Linux distro til dine behov og derefter bruge Virtuelle til hurtigt for at oprette Azure virtuelle maskiner (VM'er).

## <a name="quick-commands"></a>Hurtig kommandoer
Hvis du har brug for til hurtigt at udføre opgaven, følgende oplysninger i afsnittet grundtallet kommandoer til at overføre en VM til Azure. Mere detaljerede oplysninger og konteksten for hvert trin kan finde resten af dokumentet skal [starte her](#requirements).

Sørg for, at du har [Azure CLI](../xplat-cli-install.md) logget ind, og brug af Ressourcestyring tilstand:

```bash
azure config mode arm
```

Erstat eksempel parameternavne med dine egne værdier i eksemplerne nedenfor. Eksempel parameternavne inkluderet `myResourceGroup`, `mystorageaccount`, og `myimages`.

Opret først en ressourcegruppe. I følgende eksempel oprettes en ressourcegruppe med navnet `myResourceGroup` i den `WestUs` placering:

```bash
azure group create myResourceGroup --location "WestUS"
```

Oprette en lagerplads konto for at holde din virtuelle disk. I følgende eksempel oprettes en lagerplads kontoen `mystorageaccount`:

```bash
azure storage account create mystorageaccount --resource-group myResourceGroup \
    --location "WestUS" --kind Storage --sku-name PLRS
```

Liste over hurtigtasterne til kontoen lagerplads. Skal du notere `key1`:

```bash
azure storage account keys list mystorageaccount --resource-group myResourceGroup
```

Oprette en objektbeholder i kontoen lagerplads ved hjælp af tasten lagerplads du har hentet. I følgende eksempel oprettes en objektbeholder med navnet `myimages` ved hjælp af den lagerplads nøgleværdi fra `key1`:

```bash
azure storage container create --account-name mystorageaccount \
    --account-key key1 --container myimages
```

Til sidst skal overføre din Virtuelle til objektbeholderen, du har oprettet. Angive den lokale sti til din Virtuelle under `/path/to/disk/mydisk.vhd`:

```bash
azure storage blob upload --blobtype page --account-name mystorageaccount \
    --account-key key1 --container myimages /path/to/disk/mydisk.vhd
```

Nu kan du oprette en VM fra dine overførte Virtuel disk [ved hjælp af en ressourcestyring skabelon](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vm-from-specialized-vhd). Du kan også bruge CLI ved at angive URI på harddisken (`--image-urn`). I følgende eksempel oprettes en VM med navnet `myVM` ved hjælp af den virtuelle disk tidligere blev overført:

```bash
azure vm create myVM -l "WestUS" --resource-group myResourceGroup \
    --image-urn https://mystorageaccount.blob.core.windows.net/myimages/mydisk.vhd
```

Kontoen destination lagerplads har skal være den samme som sted, hvor du har overført harddisken virtuelle til. Du skal også angive eller answer beder om alle de yderligere parametre, der kræves, før den `azure vm create` kommando som virtuelt netværk, offentlige IP-adresse, brugernavn og SSH taster. Du kan læse mere om de [tilgængelige CLI ressourcestyring parametre](azure-cli-arm-commands.md#azure-vm-commands-to-manage-your-azure-virtual-machines).

## <a name="requirements"></a>Krav
For at benytte følgende fremgangsmåde, skal du:

- **Linux-operativsystemet installeret i en .vhd-fil** - installere en [Azure-godkendt Linux fordeling](virtual-machines-linux-endorsed-distros.md) (eller se [oplysninger for ikke-godkendt salgsdistributioner](virtual-machines-linux-create-upload-generic.md)) til en virtuel disk i formatet Virtuelle. Der findes flere værktøjer for at oprette en VM og Virtuelle:
    - Installere og konfigurere [QEMU](https://en.wikibooks.org/wiki/QEMU/Installing_QEMU) eller [KVM](http://www.linux-kvm.org/page/RunningKVM), tager sig bruge Virtuelle som et billedformat. Hvis det er nødvendigt, kan du [konvertere et billede](https://en.wikibooks.org/wiki/QEMU/Images#Converting_image_formats) ved hjælp af `qemu-img convert`.
    - Du kan også bruge Hyper-V [på Windows 10](https://msdn.microsoft.com/virtualization/hyperv_on_windows/quick_start/walkthrough_install) eller [Windows Server 2012-2012 R2](https://technet.microsoft.com/library/hh846766.aspx).

> [AZURE.NOTE] Det nyere VHDX format understøttes ikke i Azure. Når du opretter en VM, kan du angive Virtuelle som format. Hvis det er nødvendigt, kan du konvertere VHDX diske til Virtuelle ved hjælp af [`qemu-img convert`](https://en.wikibooks.org/wiki/QEMU/Images#Converting_image_formats) eller [`Convert-VHD`](https://technet.microsoft.com/library/hh848454.aspx) PowerShell-cmdlet. Desuden understøtter Azure ikke overførsel dynamiske virtuelle harddiske, så du behøver at konvertere sådanne diske til statisk virtuelle harddiske før du overfører. Du kan bruge funktioner såsom [Azure Virtuelle funktioner til gå](https://github.com/Microsoft/azure-vhd-utils-for-go) til at konvertere dynamiske diske under processen med at overføre til Azure.

- VM'er, der er oprettet ud fra den brugerdefinerede afbildning skal være placeret i den samme lagerplads konto som selve billedet
    - Oprette en lagerplads konto og objektbeholder til at holde både dit eget billede og oprettede FOS
    - Når du har oprettet alle FOS, kan du sikkert slette dit billede

Sørg for, at du har [Azure CLI](../xplat-cli-install.md) logget ind, og brug af Ressourcestyring tilstand:

```bash
azure config mode arm
```

Erstat eksempel parameternavne med dine egne værdier i eksemplerne nedenfor. Eksempel parameternavne inkluderet `myResourceGroup`, `mystorageaccount`, og `myimages`.


<a id="prepimage"> </a>
## <a name="prepare-the-image-to-be-uploaded"></a>Forberede billedet skal overføres

Azure understøtter forskellige Linux salgsdistributioner (se [Godkendt Salgsdistributioner](virtual-machines-linux-endorsed-distros.md)). I følgende artikler fører dig gennem Sådan forberedes forskellige Linux fordelingerne, der understøttes på Azure:

- **[CentOS-baserede Salgsdistributioner](virtual-machines-linux-create-upload-centos.md)**
- **[Debian Linux](virtual-machines-linux-debian-create-upload-vhd.md)**
- **[Oracle Linux](virtual-machines-linux-oracle-create-upload-vhd.md)**
- **[Red Hat Enterprise Linux](virtual-machines-linux-redhat-create-upload-vhd.md)**
- **[SLES & openSUSE](virtual-machines-linux-suse-create-upload-vhd.md)**
- **[Ubuntu](virtual-machines-linux-create-upload-ubuntu.md)**
- **[Andre - ikke godkendt Salgsdistributioner](virtual-machines-linux-create-upload-generic.md)**

Se også **[Linux Installationsnoter](virtual-machines-linux-create-upload-generic.md#general-linux-installation-notes)** mere generelle tip til forberedelse Linux billeder til Azure.

> [AZURE.NOTE] [Azure-platformen SLA](https://azure.microsoft.com/support/legal/sla/virtual-machines/) gælder for VM'er, der kører Linux kun, når en af påtegnede fordelingerne bruges sammen med oplysninger om konfigurationen er angivet under 'Understøttes versioner' i [Linux på Azure-Endorsed Salgsdistributioner](virtual-machines-linux-endorsed-distros.md).


## <a name="create-a-resource-group"></a>Oprette en ressourcegruppe
Ressourcegrupper samler logisk alle de Azure ressourcer til at understøtte din virtuelle maskiner, som den virtuelle netværk og lagerplads. Få mere at vide om [Azure ressourcegrupper her](../azure-resource-manager/resource-group-overview.md). Før du overføre brugerdefinerede disk billedet, og oprette FOS, skal du først oprette en ressourcegruppe. 

I følgende eksempel oprettes en ressourcegruppe med navnet `myResourceGroup` i den `WestUS` placering:

```bash
azure group create myResourceGroup --location "WestUS"
```

## <a name="create-a-storage-account"></a>Oprette en lagerplads-konto
FOS er gemt som siden BLOB inden for en lagerplads konto. Læs mere om [Azure blob-lager her](../storage/storage-introduction.md#blob-storage). Du opretter en lagerplads kontoen for din brugerdefinerede disk billede og FOS. En hvilken som helst FOS, som du opretter fra din brugerdefinerede disk billede skal være i den samme lagerplads konto som billedet.

I følgende eksempel oprettes en lagerplads kontoen `mystorageaccount` i ressourcegruppen, der har oprettet tidligere:

```bash
azure storage account create mystorageaccount --resource-group myResourceGroup \
    --location "WestUS" --kind Storage --sku-name PLRS
```

## <a name="list-storage-account-keys"></a>Listen lagerplads konto taster
Azure genererer to 512-bit hurtigtaster for hver konto lagerplads. Disse adgangstaster bruges, når godkendelse til kontoen lagerplads, f.eks, du kan udføre skrivning. Læs mere om [Administration af adgang til lager her](../storage/storage-create-storage-account.md#manage-your-storage-account). Du kan få vist hurtigtasterne med den `azure storage account keys list` kommandoen.

Få vist hurtigtasterne for kontoen lagerplads du har oprettet:

```bash
azure storage account keys list mystorageaccount --resource-group myResourceGroup
```

Output ligner:

```
info:    Executing command storage account keys list
+ Getting storage account keys
data:    Name  Key                                                                                       Permissions
data:    ----  ----------------------------------------------------------------------------------------  -----------
data:    key1  d4XAvZzlGAgWdvhlWfkZ9q4k9bYZkXkuPCJ15NTsQOeDeowCDAdB80r9zA/tUINApdSGQ94H9zkszYyxpe8erw==  Full
data:    key2  Ww0T7g4UyYLaBnLYcxIOTVziGAAHvU+wpwuPvK4ZG0CDFwu/mAxS/YYvAQGHocq1w7/3HcalbnfxtFdqoXOw8g==  Full
info:    storage account keys list command OK

```
Skal du notere `key1` da du skal bruge det til at interagere med kontoen lagerplads i de næste trin.

## <a name="create-a-storage-container"></a>Oprette en objektbeholder til lagring
På samme måde, som du opretter forskellige mapper for at organisere dit lokale filsystem logisk, kan du oprette objektbeholdere i en lagerplads konto til at organisere dine virtuelle disk og billeder. En lagerplads konto kan indeholde et vilkårligt antal beholdere. 

I følgende eksempel oprettes en objektbeholder med navnet `myimages`, der angiver hurtigtast der fås i det foregående trin (`key1`):

```bash
azure storage container create --account-name mystorageaccount \
    --account-key key1 --container myimages
```

## <a name="upload-vhd"></a>Overføre Virtuelle
Du kan nu faktisk overføre brugerdefinerede disk billedet. Som med alle virtuelle disk, der bruges af FOS, du overfører og gemme dine brugerdefinerede disk billede som en side blob.

Angiv din access-tast, skal den beholder, du har oprettet i ovenstående trin, og klik derefter stien til den brugerdefinerede disk billede på din lokale computer:

```bash
azure storage blob upload --blobtype page --account-name mystorageaccount \
    --account-key key1 --container myimages /path/to/disk/mydisk.vhd
```

## <a name="create-vm-from-custom-image"></a>Oprette VM fra brugerdefineret billede
Når du opretter FOS fra din brugerdefinerede disk billede, angive URI til disk billede. Sørg for, at destination lagerplads konto matches hvor brugerdefinerede disk billedet er gemt. Du kan oprette din VM ved hjælp af skabelonen Azure CLI eller Ressourcestyring JSON.


### <a name="create-a-vm-using-the-azure-cli"></a>Oprette en VM ved hjælp af Azure CLI
Du angiver den `--image-urn` parameteren med den `azure vm create` kommandoen til at pege på brugerdefinerede disk billedet. Sørg for, at `--storage-account-name` svarer til kontoen lagerplads, hvor dit brugerdefinerede disk billede er gemt. Du behøver ikke at bruge samme beholder som brugerdefineret disk billede til at gemme dine FOS. Sørg for at oprette eventuelle yderligere beholdere på samme måde som de tidligere trin, før du overfører billederne brugerdefinerede disk.

I følgende eksempel oprettes en VM med navnet `myVM` fra din brugerdefinerede disk billede:

```bash
azure vm create myVM -l "WestUS" --resource-group myResourceGroup \
    --image-urn https://mystorageaccount.blob.core.windows.net/myimages/mydisk.vhd
    --storage-account-name mystorageaccount
```

Du stadig har brug at angive eller answer beder om alle de yderligere parametre, der kræves, før den `azure vm create` kommando som virtuelt netværk, offentlige IP-adresse, brugernavn og SSH taster. Læs mere om de [tilgængelige CLI ressourcestyring parametre](azure-cli-arm-commands.md#azure-vm-commands-to-manage-your-azure-virtual-machines).

### <a name="create-a-vm-using-a-json-template"></a>Oprette en VM ved hjælp af en JSON-skabelon
Azure ressourcestyring skabeloner er JavaScript Object Notation (JSON) filer, der definerer det miljø, du vil oprette. Skabelonerne opdeles til anden ressource udbydere som Beregn eller netværket. Du kan bruge eksisterende skabeloner eller skrive din egen. Få mere at vide om [Brug af Ressourcestyring og skabeloner](../azure-resource-manager/resource-group-overview.md).

I den `Microsoft.Compute/virtualMachines` udbyder af din skabelon, du har en `storageProfile` node, der indeholder konfigurationsoplysningerne for din VM. Der er to primære parametre til at redigere den `image` og `vhd` URI'er, der peger på dit brugerdefinerede disk billede og din nye VM virtuelle disk. Følgende viser et eksempel på JSON til brug af et brugerdefineret disk billede:

```bash
"storageProfile": {
          "osDisk": {
            "name": "myVM",
            "osType": "Linux",
            "caching": "ReadWrite",
            "createOption": "FromImage",
            "image": {
              "uri": "https://mystorageaccount.blob.core.windows.net/myimages/mydisk.vhd"
            },
            "vhd": {
              "uri": "https://mystorageaccount.blob.core.windows.net/vhds/newvmname.vhd"
            }
          }
```

Du kan bruge [eksisterende skabelonen for at oprette en VM fra et brugerdefineret billede](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-from-user-image) eller Læs om [oprette dine egne Azure ressourcestyring skabeloner](../resource-group-authoring-templates.md). 

Når du har en skabelon, der er konfigureret, skal du oprette din FOS ved hjælp af den `azure group deployment create` kommandoen. Angive URI af din JSON-skabelon med den `--template-uri` parameter:

```bash
azure group deployment create --resource-group myResourceGroup
    --template-uri https://uri.to.template/mytemplate.json
```

Hvis du har en JSON-fil, der er gemt lokalt på din computer, kan du bruge den `--template-file` parameter i stedet:

```bash
azure group deployment create --resource-group myResourceGroup
    --template-file /path/to/mytemplate.json
```


## <a name="next-steps"></a>Næste trin
Når du har forberedt og har overført din brugerdefinerede virtuelle disk, kan du læse mere om [brugen af Ressourcestyring og skabeloner](../azure-resource-manager/resource-group-overview.md). Du kan også vil [tilføje en datadisk](virtual-machines-linux-add-disk.md) til din nye FOS. Hvis du har programmer, der kører på din FOS, du har brug for at få adgang til, skal du sørge for at [åbne porte og slutpunkter](virtual-machines-linux-nsg-quickstart.md).