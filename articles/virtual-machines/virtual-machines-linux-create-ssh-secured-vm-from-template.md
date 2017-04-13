<properties
    pageTitle="Oprette en Linux VM ved hjælp af en skabelon til Azure | Microsoft Azure"
    description="Oprette en Linux VM på Azure ved hjælp af en skabelon til Azure ressourcestyring."
    services="virtual-machines-linux"
    documentationCenter=""
    authors="vlivech"
    manager="timlt"
    editor=""
    tags="azure-service-management,azure-resource-manager" />

<tags
    ms.service="virtual-machines-linux"
    ms.workload="infrastructure-services"
    ms.tgt_pltfrm="vm-linux"
    ms.devlang="na"
    ms.topic="hero-article"
    ms.date="10/24/2016"
    ms.author="v-livech"/>

# <a name="create-a-linux-vm-using-an-azure-template"></a>Oprette en Linux VM ved hjælp af en Azure skabelon

I denne artikel beskrives, hvordan hurtigt skal installere en Linux virtuel maskine på Azure ved hjælp af en Azure-skabelon.  I artiklen kræver:

- en Azure-konto ([hente en gratis prøveversion](https://azure.microsoft.com/pricing/free-trial/)).

- [Azure CLI](../xplat-cli-install.md) logget på med `azure login`.

- Azure CLI _skal være i_ Azure ressourcestyring tilstand `azure config mode arm`.

Du kan også hurtigt installere en Linux VM skabelon ved at bruge [Azure-portalen](virtual-machines-linux-quick-create-portal.md).

## <a name="quick-command-summary"></a>Kommandoen hurtige oversigt

```bash
azure group create \
-n myResourceGroup \
-l westus \
--template-uri https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/101-vm-sshkey/azuredeploy.json
```

## <a name="detailed-walkthrough"></a>Detaljeret gennemgang

Skabeloner gør det muligt at oprette FOS på Azure med indstillinger, du vil tilpasse under lanceringen, indstillinger som brugernavne og værtsnavne. I denne artikel er starter vi en Azure skabelon ved hjælp af en Ubuntu VM sammen med en netværk sikkerhedsgruppe (NSG) med port 22 åben til SSH.

Azure ressourcestyring skabeloner er JSON-filer, der kan bruges til simple individuelle opgaver som at åbne en Ubuntu VM som udført i denne artikel.  Azure skabeloner kan også bruges til at oprette komplekse Azure konfigurationer af hele miljøer som en test, Udviklingscenter eller fremstilling installation stak.

## <a name="create-the-linux-vm"></a>Oprette Linux VM

Følgende kodeeksempel viser, hvordan til at ringe til `azure group create` til at oprette en ressourcegruppe og implementere en SSH-sikret Linux VM ad gangen ved hjælp af [denne skabelon til Azure ressourcestyring](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-vm-sshkey/azuredeploy.json). Husk, at i din eksempel, du skal bruge navne, der er entydige for dit miljø. I dette eksempel bruges `myResourceGroup` som ressource gruppenavn, og `myVM` som VM navnet.

```bash
azure group create \
--name myResourceGroup \
--location westus \
--template-uri https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/101-vm-sshkey/azuredeploy.json
```

Output skal se ud som følgende output blokering:

```bash
info:    Executing command group create
+ Getting resource group myResourceGroup
+ Creating resource group myResourceGroup
info:    Created resource group myResourceGroup
info:    Supply values for the following parameters
sshKeyData: ssh-rsa AAAAB3Nza<..ssh public key text..>VQgwjNjQ== myAdminUser@myVM
+ Initializing template configurations and parameters
+ Creating a deployment
info:    Created template deployment "azuredeploy"
data:    Id:                  /subscriptions/<..subid text..>/resourceGroups/myResourceGroup
data:    Name:                myResourceGroup
data:    Location:            westus
data:    Provisioning State:  Succeeded
data:    Tags: null
data:
info:    group create command OK
```

Dette eksempel installeres en VM ved hjælp af den `--template-uri` parameter.  Du kan også hente eller oprette en skabelon lokalt og overfører den skabelon, der anvender den `--template-file` parameter med en sti til skabelonfilen som et argument. Azure CLI beder dig om de parametre, der kræves af skabelonen.

## <a name="next-steps"></a>Næste trin

Søge i [skabelongalleriet](https://azure.microsoft.com/documentation/templates/) for at se, hvilken app rammer til at udrulle næste.
