<properties
   pageTitle="Installere flere NIC FOS ved hjælp af Azure CLI i den klassiske implementeringsmodel | Microsoft Azure"
   description="Lær, hvordan du installerer flere NIC FOS ved hjælp af Azure CLI i modellen Klassisk installation"
   services="virtual-network"
   documentationCenter="na"
   authors="jimdial"
   manager="carmonm"
   editor=""
   tags="azure-service-management"
/>
<tags  
   ms.service="virtual-network"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="02/02/2016"
   ms.author="jdial" />

#<a name="deploy-multi-nic-vms-classic-using-the-azure-cli"></a>Installere flere NIC FOS (klassisk) ved hjælp af Azure CLI

[AZURE.INCLUDE [virtual-network-deploy-multinic-classic-selectors-include.md](../../includes/virtual-network-deploy-multinic-classic-selectors-include.md)]

Du kan oprette virtuelle maskiner (VM'er) i Azure og vedhæfte flere netværkgrænseflader (NIC) til hver af dine FOS. Flere netværkskort aktivere adskillelse af trafiktyper på tværs af netværkskort. Én NIC kan for eksempel kommunikere med internettet, skal du, mens en anden kommunikerer kun med interne ressourcer, der ikke har forbindelse til internettet. Muligheden for at adskille netværkstrafik på tværs af flere netværkskort er påkrævet for mange netværk virtuelle udstyr, som programmet levering og WAN optimeringsløsninger.

[AZURE.INCLUDE [azure-arm-classic-important-include](../../includes/learn-about-deployment-models-classic-include.md)]Lær, hvordan du kan [udføre disse trin, ved hjælp af Ressourcestyring modellen](virtual-network-deploy-multinic-arm-cli.md).

[AZURE.INCLUDE [virtual-network-deploy-multinic-scenario-include.md](../../includes/virtual-network-deploy-multinic-scenario-include.md)]

Du kan ikke i øjeblikket har FOS med et enkelt NIC og FOS med flere netværkskort i den samme skybaseret tjeneste. Derfor skal du implementere back-end-servere i en anden skybaseret tjeneste end og alle andre komponenter i dette scenario. Nedenstående fremgangsmåde bruger en skybaseret tjeneste med navnet *IaaSStory* til de primære ressourcer og *Back-end IaaSStory* back-end-servere.

## <a name="prerequisites"></a>Forudsætninger

Før du kan installere back-end-servere, skal du installere den primære skytjeneste med alle de ressourcer, der er nødvendige for dette scenario. Som minimum skal du oprette et virtuelt netværk med et undernet til backend-version. Gå til [Opret et virtuelt netværk ved hjælp af Azure CLI](virtual-networks-create-vnet-classic-cli.md) for at se, hvordan du installerer et virtuelt netværk.

[AZURE.INCLUDE [azure-cli-prerequisites-include.md](../../includes/azure-cli-prerequisites-include.md)]

## <a name="deploy-the-back-end-vms"></a>Installere back-end FOS

Back end-FOS afhænger af oprettelse af de ressourcer, der vises nedenfor.

- **Lagerplads konto om en disk data**. For at forbedre ydeevnen, datadisce på databaseservere, der anvender dækkende tilstand drev (SSD) teknologi, der kræver en premium lagerplads konto. Sørg for, at du installerer for at understøtte premium lagerplads Azure placeringen.
- **Netværkskort**. Hver VM har to netværkskort, en database adgang, og et til administration.
- **Angive tilgængelighed**. Alle databaseservere, føjes til en enkelt tilgængelighed indstiller til at sikre, at mindst én af FOS er op og kører under vedligeholdelse af.

### <a name="step-1---start-your-script"></a>Trin 1 – Start dit script

Du kan hente den fulde fest script, der bruges [her](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/IaaS-Story/11-MultiNIC/classic/virtual-network-deploy-multinic-classic-cli.sh). Følg nedenstående trin for at ændre scriptet til at arbejde i dit miljø.

1. Ændre værdierne i de variabler nedenfor baseret på dit eksisterende ressourcegruppe implementeret over i [forudsætninger](#Prerequisites).

        location="useast2"
        vnetName="WTestVNet"
        backendSubnetName="BackEnd"

2. Ændre værdierne i de variabler nedenfor baseret på de værdier, du vil bruge til back end-installationen.

        backendCSName="IaaSStory-Backend"
        prmStorageAccountName="iaasstoryprmstorage"
        image="0b11de9248dd4d87b18621318e037d37__RightImage-Ubuntu-14.04-x64-v14.2.1"
        avSetName="ASDB"
        vmSize="Standard_DS3"
        diskSize=127
        vmNamePrefix="DB"
        osDiskName="osdiskdb"
        dataDiskPrefix="db"
        dataDiskName="datadisk"
        ipAddressPrefix="192.168.2."
        username='adminuser'
        password='adminP@ssw0rd'
        numberOfVMs=2

### <a name="step-2---create-necessary-resources-for-your-vms"></a>Trin 2 – oprette nødvendige ressourcer til din FOS

1. Oprette en ny skybaseret tjeneste for alle back end-FOS. Bemærk brugen af den `$backendCSName` variable for gruppenavn ressource og `$location` til et Azure område.

        azure service create --serviceName $backendCSName \
            --location $location

2. Oprette en premium lagerplads konto til disk OS og data, der anvendes af din egen FOS.

        azure storage account create $prmStorageAccountName \
            --location $location \
            --type PLRS

### <a name="step-3---create-vms-with-multiple-nics"></a>Trin 3 – oprette FOS med flere netværkskort

1. Starte en løkke for at oprette flere FOS, baseret på den `numberOfVMs` variabler.

        for ((suffixNumber=1;suffixNumber<=numberOfVMs;suffixNumber++));
        do

2. Angiv navn og IP-adressen på hver af de to netværkskort for hver VM.

            nic1Name=$vmNamePrefix$suffixNumber-DA
            x=$((suffixNumber+3))
            ipAddress1=$ipAddressPrefix$x

            nic2Name=$vmNamePrefix$suffixNumber-RA
            x=$((suffixNumber+53))
            ipAddress2=$ipAddressPrefix$x

4. Oprette VM. Bemærk brugen af den `--nic-config` parameter, der indeholder en liste over alle netværkskort med navn, undernet og IP-adresse.

            azure vm create $backendCSName $image $username $password \
                --connect $backendCSName \
                --vm-name $vmNamePrefix$suffixNumber \
                --vm-size $vmSize \
                --availability-set $avSetName \
                --blob-url $prmStorageAccountName.blob.core.windows.net/vhds/$osDiskName$suffixNumber.vhd \
                --virtual-network-name $vnetName \
                --subnet-names $backendSubnetName \
                --nic-config $nic1Name:$backendSubnetName:$ipAddress1::,$nic2Name:$backendSubnetName:$ipAddress2::

5. For hver VM, oprette to data.

            azure vm disk attach-new $vmNamePrefix$suffixNumber \
                $diskSize \
                vhds/$dataDiskPrefix$suffixNumber$dataDiskName-1.vhd

            azure vm disk attach-new $vmNamePrefix$suffixNumber \
                $diskSize \
                vhds/$dataDiskPrefix$suffixNumber$dataDiskName-2.vhd
        done

### <a name="step-4---run-the-script"></a>Trin 4 – Kør scriptet

Nu, hvor du har downloadet og ændret scriptet baseret på dine behov, kan du køre scriptet for at oprette back-end database FOS med flere netværkskort.

1. Gem dit script og køre den fra din **Bash** terminal. Du får vist den indledende output, som vist nedenfor.

        info:    Executing command service create
        info:    Creating cloud service
        data:    Cloud service name IaaSStory-Backend
        info:    service create command OK
        info:    Executing command storage account create
        info:    Creating storage account
        info:    storage account create command OK
        info:    Executing command vm create
        info:    Looking up image 0b11de9248dd4d87b18621318e037d37__RightImage-Ubuntu-14.04-x64-v14.2.1
        info:    Looking up virtual network
        info:    Looking up cloud service
        info:    Getting cloud service properties
        info:    Looking up deployment
        info:    Creating VM

2. Udførelsen afsluttes, efter et par minutter, og du får vist resten af output, som vist nedenfor.

        info:    OK
        info:    vm create command OK
        info:    Executing command vm disk attach-new
        info:    Getting virtual machines
        info:    Adding Data-Disk
        info:    vm disk attach-new command OK
        info:    Executing command vm disk attach-new
        info:    Getting virtual machines
        info:    Adding Data-Disk
        info:    vm disk attach-new command OK
        info:    Executing command vm create
        info:    Looking up image 0b11de9248dd4d87b18621318e037d37__RightImage-Ubuntu-14.04-x64-v14.2.1
        info:    Looking up virtual network
        info:    Looking up cloud service
        info:    Getting cloud service properties
        info:    Looking up deployment
        info:    Creating VM
        info:    OK
        info:    vm create command OK
        info:    Executing command vm disk attach-new
        info:    Getting virtual machines
        info:    Adding Data-Disk
        info:    vm disk attach-new command OK
        info:    Executing command vm disk attach-new
        info:    Getting virtual machines
        info:    Adding Data-Disk
        info:    vm disk attach-new command OK
