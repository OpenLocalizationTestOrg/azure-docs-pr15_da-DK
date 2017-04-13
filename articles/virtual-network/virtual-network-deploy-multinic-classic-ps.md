<properties
   pageTitle="Installere flere NIC FOS ved hjælp af PowerShell i den klassiske implementeringsmodel | Microsoft Azure"
   description="Lær, hvordan du installerer flere NIC FOS ved hjælp af PowerShell i modellen Klassisk installation"
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

#<a name="deploy-multi-nic-vms-classic-using-powershell"></a>Installere flere NIC FOS (klassisk) ved hjælp af PowerShell

[AZURE.INCLUDE [virtual-network-deploy-multinic-classic-selectors-include.md](../../includes/virtual-network-deploy-multinic-classic-selectors-include.md)]

Du kan oprette virtuelle maskiner (VM'er) i Azure og vedhæfte flere netværkgrænseflader (NIC) til hver af dine FOS. Flere netværkskort aktivere adskillelse af trafiktyper på tværs af netværkskort. Én NIC kan for eksempel kommunikere med internettet, skal du, mens en anden kommunikerer kun med interne ressourcer, der ikke har forbindelse til internettet. Muligheden for at adskille netværkstrafik på tværs af flere netværkskort er påkrævet for mange netværk virtuelle udstyr, som programmet levering og WAN optimeringsløsninger.

[AZURE.INCLUDE [azure-arm-classic-important-include](../../includes/learn-about-deployment-models-classic-include.md)]Lær, hvordan du kan [udføre disse trin, ved hjælp af Ressourcestyring modellen](virtual-network-deploy-multinic-arm-ps.md).

[AZURE.INCLUDE [virtual-network-deploy-multinic-scenario-include.md](../../includes/virtual-network-deploy-multinic-scenario-include.md)]

Du kan ikke i øjeblikket har FOS med et enkelt NIC og FOS med flere netværkskort i den samme skybaseret tjeneste. Derfor skal du implementere back-end-servere i en anden skybaseret tjeneste end og alle andre komponenter i dette scenario. Nedenstående fremgangsmåde bruger en skybaseret tjeneste med navnet *IaaSStory* til de primære ressourcer og *Back-end IaaSStory* back-end-servere.

## <a name="prerequisites"></a>Forudsætninger

Før du kan installere back-end-servere, skal du installere den primære skytjeneste med alle de ressourcer, der er nødvendige for dette scenario. Som minimum skal du oprette et virtuelt netværk med et undernet til backend-version. Gå til [Opret et virtuelt netværk ved hjælp af PowerShell](virtual-networks-create-vnet-classic-netcfg-ps.md) for at se, hvordan du installerer et virtuelt netværk.

[AZURE.INCLUDE [azure-ps-prerequisites-include.md](../../includes/azure-ps-prerequisites-include.md)]

## <a name="deploy-the-back-end-vms"></a>Installere back-end FOS

Back end-FOS afhænger af oprettelse af de ressourcer, der vises nedenfor.

- **Back end-undernet**. Databaseservere bliver en del af et separat undernet, at udskille trafik. Nedenstående script forventer, at dette undernet til findes i en vnet med navnet *WTestVnet*.
- **Lagerplads konto om en disk data**. For at forbedre ydeevnen, datadisce på databaseservere, der anvender dækkende tilstand drev (SSD) teknologi, der kræver en premium lagerplads konto. Sørg for, at du installerer for at understøtte premium lagerplads Azure placeringen.
- **Angive tilgængelighed**. Alle databaseservere, føjes til en enkelt tilgængelighed indstiller til at sikre, at mindst én af FOS er op og kører under vedligeholdelse af.

### <a name="step-1---start-your-script"></a>Trin 1 – Start dit script

Du kan hente den fulde PowerShell-script, der bruges [her](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/IaaS-Story/11-MultiNIC/classic/virtual-network-deploy-multinic-classic-ps.ps1). Følg nedenstående trin for at ændre scriptet til at arbejde i dit miljø.

1. Ændre værdierne i de variabler nedenfor baseret på dit eksisterende ressourcegruppe implementeret over i [forudsætninger](#Prerequisites).

        $location              = "West US"
        $vnetName              = "WTestVNet"
        $backendSubnetName     = "BackEnd"

2. Ændre værdierne i de variabler nedenfor baseret på de værdier, du vil bruge til back end-installationen.

        $backendCSName         = "IaaSStory-Backend"
        $prmStorageAccountName = "iaasstoryprmstorage"
        $avSetName             = "ASDB"
        $vmSize                = "Standard_DS3"
        $diskSize              = 127
        $vmNamePrefix          = "DB"
        $dataDiskSuffix        = "datadisk"
        $ipAddressPrefix       = "192.168.2."
        $numberOfVMs           = 2

### <a name="step-2---create-necessary-resources-for-your-vms"></a>Trin 2 – oprette nødvendige ressourcer til din FOS

Du har brug at oprette en ny tjeneste i skyen, og en lagerplads konto om en data disk for alle FOS. Du skal også angive et billede, og en lokal administratorkonto for FOS. For at oprette disse ressourcer skal du udføre følgende trin.

1. Oprette en ny tjeneste i skyen.

        New-AzureService -ServiceName $backendCSName -Location $location

2. Oprette en ny premium lagerplads konto.

        New-AzureStorageAccount -StorageAccountName $prmStorageAccountName `
            -Location $location `
            -Type Premium_LRS

3. Angiv kontoen lagerplads oprettet ovenfor som kontoen for den aktuelle lagerplads for dit abonnement.

        $subscription = Get-AzureSubscription `
            | where {$_.IsCurrent -eq $true}  
        Set-AzureSubscription -SubscriptionName $subscription.SubscriptionName `
            -CurrentStorageAccountName $prmStorageAccountName

4. Vælg et billede af VM.

        $image = Get-AzureVMImage `
            | where{$_.ImageFamily -eq "SQL Server 2014 RTM Web on Windows Server 2012 R2"} `
            | sort PublishedDate -Descending `
            | select -ExpandProperty ImageName -First 1

5. Angive den lokale administrator kontolegitimationsoplysninger.

        $cred = Get-Credential -Message "Enter username and password for local admin account"

### <a name="step-3---create-vms"></a>Trin 3 – oprette FOS

Du skal bruge en løkke til at oprette så mange FOS, som du ønsker, og oprette de nødvendige netværkskort og FOS inden for løkken. For at oprette netværkskort og FOS skal du udføre følgende trin.

1. Starte en `for` gentage for at gentage kommandoerne til at oprette en VM og to netværkskort så mange gange, som det er nødvendigt, baseret på værdien af den `$numberOfVMs` variabel.

        for ($suffixNumber = 1; $suffixNumber -le $numberOfVMs; $suffixNumber++){

2. Oprette en `VMConfig` objekt, der angiver det billede, størrelse og tilgængelighed, der er angivet for VM.

            $vmName = $vmNamePrefix + $suffixNumber
            $vmConfig = New-AzureVMConfig -Name $vmName `
                            -ImageName $image `
                            -InstanceSize $vmSize `
                            -AvailabilitySetName $avSetName  

3. Klargøre VM som Windows VM.

            Add-AzureProvisioningConfig -VM $vmConfig -Windows `
                -AdminUsername $cred.UserName `
                -Password $cred.Password

4. Angive standard NIC og tildele en statisk IP-adresse.

            Set-AzureSubnet -SubnetNames $backendSubnetName -VM $vmConfig
            Set-AzureStaticVNetIP -IPAddress ($ipAddressPrefix+$suffixNumber+3) -VM $vmConfig

5. Tilføje en anden NIC for hver VM.

            Add-AzureNetworkInterfaceConfig -Name ("RemoteAccessNIC"+$suffixNumber) `
                -SubnetName $backendSubnetName `
                -StaticVNetIPAddress ($ipAddressPrefix+(53+$suffixNumber)) `
                -VM $vmConfig

6. Oprette til datadisce for hver VM.

            $dataDisk1Name = $vmName + "-" + $dataDiskSuffix + "-1"    
            Add-AzureDataDisk -CreateNew -VM $vmConfig `
                -DiskSizeInGB $diskSize `
                -DiskLabel $dataDisk1Name `
                -LUN 0       

            $dataDisk2Name = $vmName + "-" + $dataDiskSuffix + "-2"   
            Add-AzureDataDisk -CreateNew -VM $vmConfig `
                -DiskSizeInGB $diskSize `
                -DiskLabel $dataDisk2Name `
                -LUN 1

7. Oprette hver VM, og Afslut løkken.

            New-AzureVM -VM $vmConfig `
                -ServiceName $backendCSName `
                -Location $location `
                -VNetName $vnetName
        }

### <a name="step-4---run-the-script"></a>Trin 4 – Kør scriptet

Nu, hvor du har downloadet og ændret scriptet baseret på dine behov, runt han scripts for at oprette back-end database FOS med flere netværkskort.

1. Gem dit script og kør den fra **PowerShell** kommandoprompt eller **PowerShell ISE**. Du får vist den indledende output, som vist nedenfor.

        OperationDescription    OperationId                          OperationStatus
        --------------------    -----------                          ---------------
        New-AzureService        xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx Succeeded      
        New-AzureStorageAccount xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx Succeeded      

        WARNING: No deployment found in service: 'IaaSStory-Backend'.

2. Udfyld oplysningerne om det er nødvendigt i legitimationsoplysninger prompt, og klik på **OK**. Output nedenfor vises.

        New-AzureVM             xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx Succeeded
        New-AzureVM             xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx Succeeded
