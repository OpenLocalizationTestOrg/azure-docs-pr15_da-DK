<properties
   pageTitle="Oprette en Linux VM med flere netværkskort | Microsoft Azure"
   description="Lær, hvordan du opretter en Linux VM med flere netværkskort, der er knyttet til den ved hjælp af Azure CLI eller Ressourcestyring skabeloner."
   services="virtual-machines-linux"
   documentationCenter=""
   authors="iainfoulds"
   manager="timlt"
   editor=""/>

<tags
   ms.service="virtual-machines-linux"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="vm-linux"
   ms.workload="infrastructure"
   ms.date="10/27/2016"
   ms.author="iainfou"/>

# <a name="creating-a-linux-vm-with-multiple-nics"></a>Oprette en Linux VM med flere netværkskort
Du kan oprette en virtuel maskine (VM) i Azure, der indeholder flere virtuelt netværk-grænseflader (NIC) til. Et almindeligt scenarie kunne være at har forskellige undernet til front end- og back end-forbindelse eller et dedikeret til en overvågning eller sikkerhedskopiering løsning netværk. I denne artikel indeholder hurtig kommandoer til at oprette en VM med flere netværkskort, der er knyttet til den. Du kan finde detaljerede oplysninger, herunder hvordan du opretter flere netværkskort i din egen fest scripts få mere at vide om [installation af multi-NIC FOS](../virtual-network/virtual-network-deploy-multinic-arm-cli.md). Forskellige [VM størrelser](virtual-machines-linux-sizes.md) understøtter et skiftende antal netværkskort, så størrelse din VM i overensstemmelse hermed.

>[AZURE.WARNING] Når du opretter en VM – du kan ikke tilføje netværkskort til en eksisterende VM, skal du knytte flere netværkskort. Du kan [oprette en VM, der er baseret på den oprindelige virtuelle disken(e)](virtual-machines-linux-copy-vm.md) og oprette flere netværkskort, som du installerer VM.

## <a name="quick-commands"></a>Hurtig kommandoer
Sørg for, at du har [Azure CLI](../xplat-cli-install.md) logget ind, og brug af Ressourcestyring tilstand:

```bash
azure config mode arm
```

Erstat eksempel parameternavne med dine egne værdier i eksemplerne nedenfor. Eksempel parameternavne inkluderet `myResourceGroup`, `mystorageaccount`, og `myVM`.

Opret først en ressourcegruppe. I følgende eksempel oprettes en ressourcegruppe med navnet `myResourceGroup` i den `WestUS` placering:

```bash
azure group create myResourceGroup -l WestUS
```

Oprette en lagerplads konto for at holde din FOS. I følgende eksempel oprettes en lagerplads kontoen `mystorageaccount`:

```bash
azure storage account create mystorageaccount -g myResourceGroup \
    -l WestUS --kind Storage --sku-name PLRS
```

Oprette et virtuelt netværk for at forbinde dine FOS til. I følgende eksempel oprettes et virtuelt netværk med navnet `myVnet` med en adressepræfiks af `192.168.0.0/16`:

```bash
azure network vnet create -g myResourceGroup -l WestUS \
    -n myVnet -a 192.168.0.0/16
```

Oprette to virtuelt netværksundernet - én til front end-trafik og én til back end-trafik. I følgende eksempel oprettes to undernet, med navnet `mySubnetFrontEnd` og `mySubnetBackEnd`:

```bash
azure network vnet subnet create -g myResourceGroup -e myVnet \
    -n mySubnetFrontEnd -a 192.168.1.0/24
azure network vnet subnet create -g myResourceGroup -e myVnet \
    -n mySubnetBackEnd -a 192.168.2.0/24
```

Opret to netværkskort, vedhæfte én NIC til front end-undernet og én NIC til back end-undernet. I følgende eksempel oprettes to netværkskort, med navnet `myNic1` og `myNic2`, og føjer dem til dit undernet:

```bash
azure network nic create -g myResourceGroup -l WestUS \
    -n myNic1 -m myVnet -k mySubnetFrontEnd
azure network nic create -g myResourceGroup -l WestUS \
    -n myNic2 -m myVnet -k mySubnetBackEnd
```

Til sidst skal oprette dine VM, vedhæfte de to netværkskort, du tidligere har oprettet. I følgende eksempel oprettes en VM med navnet `myVM`:

```bash
azure vm create \
    --resource-group myResourceGroup \
    --name myVM \
    --location WestUS \
    --os-type linux \
    --nic-names myNic1,myNic2 \
    --vm-size Standard_DS2_v2 \
    --storage-account-name mystorageaccount \
    --image-urn UbuntuLTS \
    --admin-username ops \
    --ssh-publickey-file ~/.ssh/id_rsa.pub
```

## <a name="creating-multiple-nics-using-azure-cli"></a>Oprette flere netværkskort ved hjælp af Azure CLI
Hvis du tidligere har oprettet en VM ved hjælp af Azure CLI, vil kommandoerne hurtig bør have kendskab. Processen er det samme for at oprette NIC for én eller flere NIC. Du kan få flere oplysninger om [installation af flere netværkskort ved hjælp af Azure CLI](../virtual-network/virtual-network-deploy-multinic-arm-cli.md), herunder scripting processen med at oprette alle netværkskortene gentagelse.

I følgende eksempel oprettes to netværkskort, med navnet `myNic1` og `myNic2`, med én NIC for at oprette forbindelse til hvert undernet:

```bash
azure network nic create --resource-group myResourceGroup --location WestUS \
    -n myNic1 --subnet-vnet-name myVnet --subnet-name mySubnetFrontEnd
azure network nic create --resource-group myResourceGroup --location WestUS \
    -n myNic2 --subnet-vnet-name myVnet --subnet-name mySubnetBackEnd
```

Typisk oprette du også en [Netværk sikkerhedsgruppe](../virtual-network/virtual-networks-nsg.md) eller [justering af belastning](../load-balancer/load-balancer-overview.md) for at administrere og distribuere trafik på tværs af din FOS. Kommandoerne er igen, på samme måde, når du arbejder med flere netværkskort. I følgende eksempel oprettes en netværk sikkerhedsgruppe med navnet `myNetworkSecurityGroup`:

```bash
azure network nsg create --resource-group myResourceGroup --location WestUS \
    --name myNetworkSecurityGroup
```

Binde din netværkskort til netværk sikkerhedsgruppe ved hjælp af `azure network nic set`. I følgende eksempel binder `myNic1` og `myNic2` med `myNetworkSecurityGroup`:

```bashazure 
azure network nic set --resource-group myResourceGroup --name myNic1 \
    --network-security-group-name myNetworkSecurityGroup
azure network nic set --resource-group myResourceGroup --name myNic2 \
    --network-security-group-name myNetworkSecurityGroup
```

Når du opretter VM, kan du nu angive flere netværkskort. I stedet bruge `--nic-name` for at give en enkelt NIC, i stedet du bruge `--nic-names` og angive en kommasepareret liste over netværkskort. Du skal også være forsigtig, når du vælger VM størrelse. Der er begrænsninger for det samlede antal netværkskort, som du kan føje til en VM. Læs mere om [Linux VM størrelser](virtual-machines-linux-sizes.md). Følgende eksempel viser, hvordan du angiver flere netværkskort og en VM størrelse, der understøtter ved hjælp af flere netværkskort (`Standard_DS2_v2`):

```bash
azure vm create \
    --resource-group myResourceGroup \
    --name myVM \
    --location WestUS \
    --os-type linux \
    --nic-names myNic1,myNic2 \
    --vm-size Standard_DS2_v2 \
    --storage-account-name mystorageaccount \
    --image-urn UbuntuLTS \
    --admin-username ops \
    --ssh-publickey-file ~/.ssh/id_rsa.pub
```

## <a name="creating-multiple-nics-using-resource-manager-templates"></a>Oprette flere netværkskort ved hjælp af Ressourcestyring skabeloner
Azure ressourcestyring skabeloner Brug deklarativ JSON-filer til at definere dit miljø. Du kan få en [Oversigt over Azure ressource Manager](../azure-resource-manager/resource-group-overview.md). Ressourcestyring skabeloner er en måde at oprette flere forekomster af en ressource under installationen, som opretter flere netværkskort. Du kan bruge *Kopiér* til at angive antallet forekomster til at oprette:

```bash
"copy": {
    "name": "multiplenics"
    "count": "[parameters('count')]"
}
```

Få mere at vide om at [oprette flere forekomster ved hjælp af *Kopier*](../resource-group-create-multiple.md). 

Du kan også bruge en `copyIndex()` derefter føje et tal til et ressourcenavn, hvor du kan oprette `myNic1`, `myNic2`osv. Følgende viser et eksempel på tilføje indeksværdien:

```bash
"name": "[concat('myNic', copyIndex())]", 
```

Du kan få en komplet eksempel på [oprettelse af flere netværkskort ved hjælp af Ressourcestyring skabeloner](../virtual-network/virtual-network-deploy-multinic-arm-template.md).

## <a name="next-steps"></a>Næste trin
Sørg for at gennemse [Linux VM størrelser](virtual-machines-linux-sizes.md) , når du forsøger at oprette en VM med flere netværkskort. Læg mærke til det maksimale antal netværkskort understøtter hver VM størrelse. 

Husk, at du ikke føje flere netværkskort til en eksisterende VM, skal du oprette alle netværkskortene, når du installerer VM. Tager sig, når du planlægger dine installationer at sikre dig, at du har alle de nødvendige netværksforbindelsen fra begyndelsen.