<properties
   pageTitle="Oprette en Linux VM på Azure ved hjælp af CLI | Microsoft Azure"
   description="Oprette en Linux VM på Azure ved hjælp af CLI."
   services="virtual-machines-linux"
   documentationCenter=""
   authors="vlivech"
   manager="timlt"
   editor=""/>

<tags
   ms.service="virtual-machines-linux"
   ms.devlang="NA"
   ms.topic="hero-article"
   ms.tgt_pltfrm="vm-linux"
   ms.workload="infrastructure"
   ms.date="10/27/2016"
   ms.author="v-livech"/>


# <a name="create-a-linux-vm-on-azure-by-using-the-cli"></a>Oprette en Linux VM på Azure ved hjælp af CLI

Denne artikel beskrives, hvordan du kan hurtigt installere en Linux virtuel maskine (VM) på Azure ved hjælp af den `azure vm quick-create` kommando i Azure kommandolinjen (CLI). Den `quick-create` kommandoen installerer en VM i en grundlæggende, sikker infrastruktur, du kan bruge til at udvikle prototyper eller teste en konceptet hurtigt. I artiklen kræver:

- en Azure-konto ([hente en gratis prøveversion](https://azure.microsoft.com/pricing/free-trial/)).

- [Azure CLI](../xplat-cli-install.md) logget på med `azure login`.

- Azure CLI _skal være i_ Azure ressourcestyring tilstand `azure config mode arm`.

Du kan også hurtigt installere en Linux VM ved hjælp af [Azure-portalen](virtual-machines-linux-quick-create-portal.md).

## <a name="quick-commands"></a>Hurtig kommandoer

I følgende eksempel viser, hvordan du installerer en CoreOS VM og vedhæfte din Secure Shell (SSH) nøgle (din argumenter kan være forskellige):

```bash
azure vm quick-create -M ~/.ssh/id_rsa.pub -Q CoreOS
```

## <a name="detailed-walkthrough"></a>Detaljeret gennemgang

Følgende gennemgang har en UbuntuLTS VM blive udløst, trin for trin med forklaringer, hvad hvert trin er at gøre.

## <a name="vm-quick-create-aliases"></a>VM hurtige – Opret aliasser

En hurtig måde at vælge en fordeling er at bruge de Azure CLI aliasser, der er knyttet til de mest almindelige OS fordelingerne. I følgende tabel vises aliases (og Azure CLI version 0,10). Alle installationer, der bruger `quick-create` VM'er, der understøttes af Solid State drev (SSD) lagerplads, som har adgang til hurtigere klargøring og høj ydeevne disken som standard. (Disse aliasser udgør en meget lille andel af de tilgængelige salgsdistributioner på Azure. Find flere billeder i Azure Marketplace ved at [søge efter et billede i PowerShell](virtual-machines-linux-cli-ps-findimage.md), [på internettet](https://azure.microsoft.com/marketplace/virtual-machines/)eller [overføre dine egne brugerdefinerede billede](virtual-machines-linux-create-upload-generic.md).)

| Alias     | Publisher | Tilbud        | SKU         | Version |
|:----------|:----------|:-------------|:------------|:--------|
| CentOS    | OpenLogic | CentOS       | 7.2         | seneste  |
| CoreOS    | CoreOS    | CoreOS       | Stabil      | seneste  |
| Debian    | credativ  | Debian       | 8           | seneste  |
| openSUSE  | SUSE      | openSUSE     | 13,2        | seneste  |
| RHEL      | Red Hat    | RHEL         | 7.2         | seneste  |
| UbuntuLTS | Vedtaget | Ubuntu Server | 14.04.4-LTS | seneste  |

Følgende sektioner Brug den `UbuntuLTS` alias for indstillingen **ImageURN** (`-Q`) til at installere en Ubuntu 14.04.4 LTS Server.

Den forrige `quick-create` eksempel kun vist de `-M` flag for at identificere SSH offentlig nøgle til at overføre under deaktivering SSH adgangskoder, så du bliver bedt om følgende argumenter:

- ressourcenavn gruppe (en streng er typisk fint til dit første Azure ressourcegruppe)
- VM navn
- placering (`westus` eller `westeurope` er god standarder)
- Linux (for at lade Azure ved, hvilken du vil OS)
- brugernavn

I følgende eksempel angiver alle værdierne, så ingen yderligere spørge er påkrævet. Så længe du har en `~/.ssh/id_rsa.pub` som en ssh rsa offentlig nøgle filformatet, det fungerer som den er:

```bash
azure vm quick-create \
--resource-group myResourceGroup \
--name myVM \
--location westus \
--os-type Linux \
--admin-username myAdminUser \
--ssh-public-file ~/.ssh/id_rsa.pub \
--image-urn UbuntuLTS
```

Output skal se ud som følgende output blokering:

```bash
info:    Executing command vm quick-create
+ Listing virtual machine sizes available in the location "westus"
+ Looking up the VM "myVM"
info:    Verifying the public key SSH file: /Users/ahmet/.ssh/id_rsa.pub
info:    Using the VM Size "Standard_DS1"
info:    The [OS, Data] Disk or image configuration requires storage account
+ Looking up the storage account cli16330708391032639673
+ Looking up the NIC "examp-westu-1633070839-nic"
info:    An nic with given name "examp-westu-1633070839-nic" not found, creating a new one
+ Looking up the virtual network "examp-westu-1633070839-vnet"
info:    Preparing to create new virtual network and subnet
/ Creating a new virtual network "examp-westu-1633070839-vnet" [address prefix: "10.0.0.0/16"] with subnet "examp-westu-1633070839-snet" [address prefix: "10.+.1.0/24"]
+ Looking up the virtual network "examp-westu-1633070839-vnet"
+ Looking up the subnet "examp-westu-1633070839-snet" under the virtual network "examp-westu-1633070839-vnet"
info:    Found public ip parameters, trying to setup PublicIP profile
+ Looking up the public ip "examp-westu-1633070839-pip"
info:    PublicIP with given name "examp-westu-1633070839-pip" not found, creating a new one
+ Creating public ip "examp-westu-1633070839-pip"
+ Looking up the public ip "examp-westu-1633070839-pip"
+ Creating NIC "examp-westu-1633070839-nic"
+ Looking up the NIC "examp-westu-1633070839-nic"
+ Looking up the storage account clisto1710997031examplev
+ Creating VM "myVM"
+ Looking up the VM "myVM"
+ Looking up the NIC "examp-westu-1633070839-nic"
+ Looking up the public ip "examp-westu-1633070839-pip"
data:    Id                              :/subscriptions/2<--snip-->d/resourceGroups/exampleResourceGroup/providers/Microsoft.Compute/virtualMachines/exampleVMName
data:    ProvisioningState               :Succeeded
data:    Name                            :exampleVMName
data:    Location                        :westus
data:    Type                            :Microsoft.Compute/virtualMachines
data:
data:    Hardware Profile:
data:      Size                          :Standard_DS1
data:
data:    Storage Profile:
data:      Image reference:
data:        Publisher                   :Canonical
data:        Offer                       :UbuntuServer
data:        Sku                         :14.04.4-LTS
data:        Version                     :latest
data:
data:      OS Disk:
data:        OSType                      :Linux
data:        Name                        :clic7fadb847357e9cf-os-1473374894359
data:        Caching                     :ReadWrite
data:        CreateOption                :FromImage
data:        Vhd:
data:          Uri                       :https://cli16330708391032639673.blob.core.windows.net/vhds/clic7fadb847357e9cf-os-1473374894359.vhd
data:
data:    OS Profile:
data:      Computer Name                 :myVM
data:      User Name                     :myAdminUser
data:      Linux Configuration:
data:        Disable Password Auth       :true
data:
data:    Network Profile:
data:      Network Interfaces:
data:        Network Interface #1:
data:          Primary                   :true
data:          MAC Address               :00-0D-3A-33-42-FB
data:          Provisioning State        :Succeeded
data:          Name                      :examp-westu-1633070839-nic
data:          Location                  :westus
data:            Public IP address       :138.91.247.29
data:            FQDN                    :examp-westu-1633070839-pip.westus.cloudapp.azure.com
data:
data:    Diagnostics Profile:
data:      BootDiagnostics Enabled       :true
data:      BootDiagnostics StorageUri    :https://clisto1710997031examplev.blob.core.windows.net/
data:
data:      Diagnostics Instance View:
info:    vm quick-create command OK
```

## <a name="log-in-to-the-new-vm"></a>Log på den nye VM

Log på din VM ved hjælp af den offentlige IP-adresse, der er angivet i output. Du kan også bruge det fuldstændige domænenavn (fulde Domænenavn), der vises:

```bash
ssh -i ~/.ssh/id_rsa.pub ahmet@138.91.247.29
```

Logon skal ligne følgende output blokering:

```bash
Warning: Permanently added '138.91.247.29' (ECDSA) to the list of known hosts.
Welcome to Ubuntu 14.04.4 LTS (GNU/Linux 3.19.0-65-generic x86_64)

 * Documentation:  https://help.ubuntu.com/

  System information as of Thu Sep  8 22:50:57 UTC 2016

  System load: 0.63              Memory usage: 2%   Processes:       81
  Usage of /:  39.6% of 1.94GB   Swap usage:   0%   Users logged in: 0

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

myAdminUser@myVM:~$
```

## <a name="next-steps"></a>Næste trin

Den `azure vm quick-create` kommandoen er den måde til hurtigt at implementere en VM, så du kan logge på en fest shell og kan fungere. Men ved hjælp af `vm quick-create` giver dig ikke omfattende kontrolelement heller ikke gør det muligt at oprette et mere komplekse miljø.  Du skal installere en Linux VM, som er tilpasset din infrastruktur, kan du følge en af følgende artikler:

- [Bruge en skabelon til Azure ressourcestyring til at oprette en bestemt installation](virtual-machines-linux-cli-deploy-templates.md)
- [Oprette dit eget brugerdefinerede miljø til en Linux VM ved hjælp af Azure CLI kommandoer direkte](virtual-machines-linux-create-cli-complete.md)
- [Oprette en SSH sikret Linux VM på Azure ved hjælp af skabeloner](virtual-machines-linux-create-ssh-secured-vm-from-template.md)

Du kan også [Brug den `docker-machine` Azure-driver med forskellige kommandoer til hurtigt at oprette en Linux VM som vært docker](virtual-machines-linux-docker-machine.md).
