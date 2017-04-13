<properties
    pageTitle="Bruge skyen initialisering til at tilpasse en Linux VM under oprettelse af | Microsoft Azure"
    description="Bruge skyen initialisering til at tilpasse en Linux VM under oprettelse af."
    services="virtual-machines-linux"
    documentationCenter=""
    authors="vlivech"
    manager="timlt"
    editor=""
    tags="azure-resource-manager"
/>

<tags
    ms.service="virtual-machines-linux"
    ms.workload="infrastructure-services"
    ms.tgt_pltfrm="vm-linux"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/26/2016"
    ms.author="v-livech"
/>

# <a name="using-cloud-init-to-customize-a-linux-vm-during-creation"></a>Bruge skyen initialisering til at tilpasse en Linux VM under oprettelse af

Denne artikel beskrives, hvordan du gør en skyen initialisering script til at angive hostname, opdateringspakker til, der er installeret, og administrere brugerkonti.  Skyen initialisering scriptene kaldes under VM oprettelse fra Azure CLI.  I artiklen kræver:

- en Azure-konto ([hente en gratis prøveversion](https://azure.microsoft.com/pricing/free-trial/)).

- [Azure CLI](../xplat-cli-install.md) logget på med `azure login`.

- Azure CLI _skal være i_ Azure ressourcestyring tilstand `azure config mode arm`.

## <a name="quick-commands"></a>Hurtig kommandoer

Oprette et script i skyen-init.txt, der angiver hostname, opdaterer alle pakker og tilføjer en sudo bruger til Linux.

```bash
#cloud-config
hostname: myVMhostname
apt_upgrade: true
users:
  - name: myNewAdminUser
    groups: sudo
    shell: /bin/bash
    sudo: ['ALL=(ALL) NOPASSWD:ALL']
    ssh-authorized-keys:
      - ssh-rsa AAAAB3<snip>==myAdminUser@myVM
```
Oprette en ressourcegruppe for at starte FOS til.

```bash
azure group create myResourceGroup westus
```

Oprette en Linux VM ved hjælp af skyen initialisering konfigurere det under start.

```bash
azure vm create \
-g myResourceGroup \
-n myVM \
-l westus \
-y Linux \
-f myVMnic \
-F myVNet \
-P 10.0.0.0/22 \
-j mySubnet \
-k 10.0.0.0/24 \
-Q canonical:ubuntuserver:14.04.2-LTS:latest \
-M ~/.ssh/id_rsa.pub \
-u myAdminUser \
-C cloud-init.txt
```

## <a name="detailed-walkthrough"></a>Detaljeret gennemgang

### <a name="introduction"></a>Introduktion

Når du starter en ny Linux VM, får du en standard Linux VM uden noget tilpassede eller er du klar til dine behov. [Skyen initialisering](https://cloudinit.readthedocs.org) er en almindelige metode til at indsætte et script eller konfiguration af indstillinger i pågældende Linux VM, som den starter for første gang.

Under Azure, er der en tre forskellige måder til at foretage ændringer på en Linux VM, som det vil blive installeret eller startes.

- Indsæt scripts med skyen initialisering.
- Indsæt scripts med Azure [VMAccess filtypenavn](virtual-machines-linux-using-vmaccess-extension.md).
- En Azure skabelon, der bruger skyen initialisering.
- En Azure skabelonen ved hjælp af [CustomScriptExtention](virtual-machines-linux-extensions-customscript.md).

Skal tilføjes scripts når som helst efter start:

- SSH til at køre kommandoer direkte
- Indsæt scripts med Azure [VMAccess lokalnummer](virtual-machines-linux-using-vmaccess-extension.md), enten imperatively eller i en Azure skabelon
- Værktøjer til administration af konfiguration som Ansible, Salt, kok og Puppet.

>[AZURE.NOTE]: VMAccess Extension executes a script as root in the same way using SSH can.  However, using the VM extension enables several features that Azure offers that can be useful depending upon your scenario.

## <a name="cloud-init-availability-on-azure-vm-quick-create-image-aliases"></a>Skyen initialisering tilgængelighed på Azure VM hurtige – Opret billede aliasser:

| Alias     | Publisher | Tilbud        | SKU         | Version | skyen initialisering |
|:----------|:----------|:-------------|:------------|:--------|:-----------|
| CentOS    | OpenLogic | Centos       | 7.2         | seneste  | Nej         |
| CoreOS    | CoreOS    | CoreOS       | Stabil      | seneste  | Ja        |
| Debian    | credativ  | Debian       | 8           | seneste  | Nej         |
| openSUSE  | SUSE      | openSUSE     | 13,2        | seneste  | Nej         |
| RHEL      | RedHat    | RHEL         | 7.2         | seneste  | Nej         |
| UbuntuLTS | Vedtaget | UbuntuServer | 14.04.4-LTS | seneste  | Ja        |

Microsoft arbejder med vores partnere til at få skyen initialisering inkluderet og arbejde på de billeder, som de giver til Azure.

## <a name="adding-a-cloud-init-script-to-the-vm-creation-with-the-azure-cli"></a>Tilføje et skyen initialisering script til VM oprettelse med Azure CLI

For at starte et skyen initialisering script, når du opretter en VM i Azure, angive skyen initialisering filen med Azure CLI `--custom-data` skifte.

Oprette en ressourcegruppe for at starte FOS til.

```bash
azure group create myResourceGroup westus
```

Oprette en Linux VM ved hjælp af skyen initialisering konfigurere det under start.

```bash
azure vm create \
--resource-group myResourceGroup \
--name myVM \
--location westus \
--os-type Linux \
--nic-name myVMnic \
--vnet-name myVNet \
--vnet-address-prefix 10.0.0.0/22 \
--vnet-subnet-name mySubnet \
--vnet-subnet-address-prefix 10.0.0.0/24 \
--image-urn canonical:ubuntuserver:14.04.2-LTS:latest \
--ssh-publickey-file ~/.ssh/id_rsa.pub \
--admin-username myAdminUser \
--custom-data cloud-init.txt
```

## <a name="creating-a-cloud-init-script-to-set-the-hostname-of-a-linux-vm"></a>Oprette et skyen initialisering script for at angive hostname af en Linux VM

En af de mest enkle og de vigtigste indstillinger for en hvilken som helst Linux VM ville være værtsnavnet. Vi kan nemt angive dette bruger skyen initialisering med dette script.  

### <a name="example-cloud-init-script-named-cloudconfighostnametxt"></a>Eksempel på skyen initialisering et script med navnet `cloud_config_hostname.txt`.

``` bash
#cloud-config
hostname: myservername
```

Under den indledende start af VM dette skyen initialisering script Angiver værtsnavnet til `myservername`.

```bash
azure vm create \
--resource-group myResourceGroup \
--name myVM \
--location westus \
--os-type Linux \
--nic-name myVMnic \
--vnet-name myVNet \
--vnet-address-prefix 10.0.0.0/22 \
--vnet-subnet-name mySubNet \
--vnet-subnet-address-prefix 10.0.0.0/24 \
--image-urn canonical:ubuntuserver:14.04.2-LTS:latest \
--ssh-publickey-file ~/.ssh/id_rsa.pub \
--admin-username myAdminUser \
--custom-data cloud_config_hostname.txt
```

Logon og bekræfte værtsnavnet for den nye VM.

```bash
ssh myVM
hostname
myservername
```

## <a name="creating-a-cloud-init-script-to-update-linux"></a>Oprette et skyen initialisering script for at opdatere Linux

Sikkerhed vil du din Ubuntu VM opdatere, i den første start.  Brug af skyen initialisering vi kan gøre det med opfølgning-script, afhængigt af den Linux fordeling, du bruger.

### <a name="example-cloud-init-script-cloudconfigaptupgradetxt-for-the-debian-family"></a>Eksempel skyen initialisering script `cloud_config_apt_upgrade.txt` for Debian familien

```bash
#cloud-config
apt_upgrade: true
```

Når Linux er startet, opdateres alle de installerede pakker `apt-get`.

```bash
azure vm create \
--resource-group myResourceGroup \
--name myVM \
--location westus \
--os-type Linux \
--nic-name myVMnic \
--vnet-name myVNet \
--vnet-address-prefix 10.0.0.0/22 \
--vnet-subnet-name mySubNet \
--vnet-subnet-address-prefix 10.0.0.0/24 \
--image-urn canonical:ubuntuserver:14.04.2-LTS:latest \
--ssh-publickey-file ~/.ssh/id_rsa.pub \
--admin-username myAdminUser \
--custom-data cloud_config_apt_upgrade.txt
```

Logon og bekræfte alle pakker er opdateret.

```bash
ssh myUbuntuVM
sudo apt-get upgrade
Reading package lists... Done
Building dependency tree
Reading state information... Done
Calculating upgrade... Done
The following packages have been kept back:
  linux-generic linux-headers-generic linux-image-generic
0 upgraded, 0 newly installed, 0 to remove and 0 not upgraded.
```

## <a name="creating-a-cloud-init-script-to-add-a-user-to-linux"></a>Oprette et skyen initialisering script for at tilføje en bruger til Linux

En af de første opgaver på en hvilken som helst nye Linux VM er at tilføje en bruger til dig selv eller for at undgå at bruge `root`. SSH taster er bedste praksis for sikkerhed og brugervenligheden og de er føjet til den `~/.ssh/authorized_keys` fil med dette skyen initialisering script.

### <a name="example-cloud-init-script-cloudconfigadduserstxt-for-debian-family"></a>Eksempel skyen initialisering script `cloud_config_add_users.txt` for Debian familie

```bash
#cloud-config
users:
  - name: myCloudInitAddedAdminUser
    groups: sudo
    shell: /bin/bash
    sudo: ['ALL=(ALL) NOPASSWD:ALL']
    ssh-authorized-keys:
      - ssh-rsa AAAAB3<snip>==myAdminUser@myUbuntuVM
```

Når Linux er startet, oprettes de viste brugere og føjet til gruppen sudo.

```bash
azure vm create \
--resource-group myResourceGroup \
--name myVM \
--location westus \
--os-type Linux \
--nic-name myVMnic \
--vnet-name myVNet \
--vnet-address-prefix 10.0.0.0/22 \
--vnet-subnet-name mySubNet \
--vnet-subnet-address-prefix 10.0.0.0/24 \
--image-urn canonical:ubuntuserver:14.04.2-LTS:latest \
--ssh-publickey-file ~/.ssh/id_rsa.pub \
--admin-username myAdminUser \
--custom-data cloud_config_add_users.txt
```

Logon og bekræft den nyoprettede bruger.

```bash
ssh myVM
cat /etc/group
```

Output

```bash
root:x:0:
<snip />
sudo:x:27:myCloudInitAddedAdminUser
<snip />
myCloudInitAddedAdminUser:x:1000:
```

## <a name="next-steps"></a>Næste trin

Skyen initialisering bliver til en almindelig metode til at ændre din Linux VM ved start. Azure har også VM filtypenavne, som gør det muligt at ændre din LinuxVM ved start, eller mens den kører. Du kan for eksempel bruge Azure VMAccessExtension nulstille SSH eller bruger oplysninger, mens VM kører. Med skyen initialisering, du har brug for en genstart for at nulstille adgangskoden.

[Om virtuelt filtypenavne og funktioner](virtual-machines-linux-extensions-features.md)

[Administrere brugere, SSH, og markér eller reparere diske på Azure Linux FOS bruger filtypenavnet VMAccess](virtual-machines-linux-using-vmaccess-extension.md)
