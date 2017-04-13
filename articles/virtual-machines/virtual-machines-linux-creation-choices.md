<properties
    pageTitle="Forskellige måder at oprette en Linux VM | Microsoft Azure"
    description="Se de forskellige måder at oprette en Linux virtuel maskine på Azure, herunder links til værktøjer og selvstudier til hver enkelt metode."
    services="virtual-machines-linux"
    documentationCenter=""
    authors="iainfoulds"
    manager="timlt"
    editor=""
    tags="azure-resource-manager"/>

<tags
    ms.service="virtual-machines-linux"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.tgt_pltfrm="vm-linux"
    ms.workload="infrastructure-services"
    ms.date="09/27/2016"
    ms.author="iainfou"/>

# <a name="different-ways-to-create-a-linux-virtual-machine-in-azure"></a>Forskellige måder at oprette en Linux virtuel maskine i Azure

Du har fleksibilitet i Azure til at oprette en virtuel maskine til Linux (VM), ved hjælp af værktøjer og arbejdsprocesser, der er vant til dig. I denne artikel indeholder en oversigt over disse forskelle og eksempler til oprettelse af din Linux FOS.


## <a name="azure-cli"></a>Azure CLI 

Azure CLI er tilgængelig på tværs af platforme via en npm-pakke, forudsat distro pakker eller Docker objektbeholder. Du kan læse mere om, [hvordan du installerer og konfigurerer Azure CLI](../xplat-cli-install.md). Følgende selvstudier indeholder eksempler på brug af Azure CLI. Læs hver artikel for at få mere at vide om de CLI Hurtig start-kommandoer, der vises:

- [Oprette en Linux VM fra Azure CLI for Udviklingscenter og test](virtual-machines-linux-quick-create-cli.md)
    - I følgende eksempel oprettes en CoreOS VM ved hjælp af en offentlig nøgle med navnet `azure_id_rsa.pub`:

    ```bash
    azure vm quick-create -ssh-publickey-file ~/.ssh/azure_id_rsa.pub \
        --image-urn CoreOS
    ```

- [Oprette en sikker Linux VM ved hjælp af en Azure skabelon](virtual-machines-linux-create-ssh-secured-vm-from-template.md)
    - I følgende eksempel oprettes en VM ved hjælp af en skabelon, der er gemt på GitHub:

    ```bash
    azure group create --name TestRG --location WestUS 
        --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-vm-sshkey/azuredeploy.json
    ```

- [Oprette et komplet Linux-miljø ved hjælp af Azure CLI](virtual-machines-linux-create-cli-complete.md)
    - Omfatter oprettelse af en justering af belastning og flere FOS i et sæt tilgængelighed.

- [Tilføje en disk til en Linux VM](virtual-machines-linux-add-disk.md)
    - I følgende eksempel adderes en 5Gb disk til en eksisterende VM med navnet `TestVM`:

    ```bash
    azure vm disk attach-new --resource-group TestRG --vm-name TestVM \
        --size-in-GB 5
    ```

## <a name="azure-portal"></a>Azure-portalen

[Azure portal](https://portal.azure.com) kan du hurtigt oprette en VM, da der ikke er noget at installere på din computer. Bruge Azure-portalen til at oprette VM:

- [Oprette en Linux VM ved hjælp af portalen Azure](virtual-machines-linux-quick-create-portal.md) 
- [Vedhæfte en disk, ved hjælp af portalen Azure](virtual-machines-linux-attach-disk-portal.md)


## <a name="operating-system-and-image-choices"></a>Operativsystem og billede valg
Når du opretter en VM, kan du vælge et billede, der er baseret på det operativsystem, du vil køre. Azure og Microsofts partnere indeholder mange billeder, som blandt programmer og funktioner, der allerede er installeret. Eller du kan overføre en af dine egne billeder (se [afsnittet nedenfor](#use-your-own-image)).

### <a name="azure-images"></a>Azure billeder
Brug den `azure vm image` CLI kommandoer til at se, hvad der er tilgængeligt ved publisher distro version og builds.

Liste over tilgængelige udgivere på følgende måde:

```bash
azure vm image list-publishers --location WestUS
```

Liste over tilgængelige produkter (tilbud) for en given udgiver på følgende måde:

```bash
azure vm image list-offers --location WestUS --publisher Canonical
```

Liste over tilgængelige lagerenheder (distro versioner) af et givet tilbud på følgende måde:

```bash
azure vm image list-skus --location WestUS --publisher Canonical --offer UbuntuServer
```

Oversigt over alle tilgængelige billeder til en given release følger:

```bash
azure vm image list --location WestUS --publisher Canonical --offer UbuntuServer --sku 16.04.0-LTS
```

Se flere eksempler på søgning og ved hjælp af tilgængelige billeder, [Naviger og vælge Azure virtuelt billeder med Azure CLI](virtual-machines-linux-cli-ps-findimage.md).

Den `azure vm quick-create` og `azure vm create` kommandoer har alias, du kan bruge til hurtigt at få adgang til de mest almindelige distros og deres seneste versioner. Brug af aliasser er ofte hurtigere end angive publisher, tilbud, SKU og version, hver gang du opretter en VM:

| Alias     | Publisher | Tilbud        | SKU         | Version |
|:----------|:----------|:-------------|:------------|:--------|
| CentOS    | OpenLogic | Centos       | 7.2         | seneste  |
| CoreOS    | CoreOS    | CoreOS       | Stabil      | seneste  |
| Debian    | credativ  | Debian       | 8           | seneste  |
| openSUSE  | SUSE      | openSUSE     | 13,2        | seneste  |
| RHEL      | RedHat    | RHEL         | 7.2         | seneste  |
| SLES      | SLES      | SLES         | 12-SP1      | seneste  |
| UbuntuLTS | Vedtaget | UbuntuServer | 14.04.4-LTS | seneste  |

### <a name="use-your-own-image"></a>Bruge dit eget billede

Hvis du kræver specifikke tilpasninger, kan du bruge et billede er baseret på en eksisterende Azure VM ved *hentning af* pågældende VM. Du kan også overføre et billede, der er oprettet i det lokale miljø. Du kan finde flere oplysninger om understøttede distros og hvordan du bruger dine egne billeder, i følgende artikler:

- [Azure godkendt salgsdistributioner](virtual-machines-linux-endorsed-distros.md)

- [Oplysninger om ikke-godkendt fordeling](virtual-machines-linux-create-upload-generic.md)

- Se, [hvordan du kan registrere en Linux virtuel maskine som en ressourcestyring skabelon](virtual-machines-linux-capture-image.md).
    - Hurtig start eksempel kommandoer til at registrere en eksisterende VM:

    ```bash
    azure vm deallocate --resource-group TestRG --vm-name TestVM
    azure vm generalize --resource-group TestRG --vm-name TestVM
    azure vm capture --resource-group TestRG --vm-name TestVM --vhd-name-prefix CapturedVM
    ```

## <a name="next-steps"></a>Næste trin

- Oprette en Linux VM [portal](virtual-machines-linux-quick-create-portal.md)med [CLI](virtual-machines-linux-quick-create-cli.md)eller ved hjælp af en [Azure ressourcestyring skabelon](virtual-machines-linux-cli-deploy-templates.md).

- Når du har oprettet en Linux VM, [føje data disken](virtual-machines-linux-add-disk.md).

- Hurtige trin til at [nulstille en adgangskode eller SSH nøgler og administrere brugere](virtual-machines-linux-using-vmaccess-extension.md)
