<properties
    pageTitle="Oprette et virtuelt skala sæt ved hjælp af PowerShell | Microsoft Azure"
    description="Oprette et virtuelt skala sæt ved hjælp af PowerShell"
    services="virtual-machine-scale-sets"
    documentationCenter=""
    authors="davidmu1"
    manager="timlt"
    editor=""
    tags="azure-resource-manager"/>

<tags
    ms.service="virtual-machine-scale-sets"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="10/18/2016"
    ms.author="davidmu"/>

# <a name="create-a-windows-virtual-machine-scale-set-using-azure-powershell"></a>Oprette en Windows virtuelt skala, sæt med Azure PowerShell

Disse trin skal du følge en Udfyld-i-feltet-tomme fremgangsmåde til oprettelse af et Azure virtuelt skala sæt. Se [Virtuelt skala angiver oversigt](virtual-machine-scale-sets-overview.md) mere at vide om skala sæt.

Det skal tage om 30 minutter for at udføre trinnene i denne artikel.

## <a name="step-1-install-azure-powershell"></a>Trin 1: Installere Azure PowerShell

Se, [hvordan du installerer og konfigurerer Azure PowerShell](../powershell-install-configure.md) for at få oplysninger om at installere den nyeste version af Azure PowerShell, vælge dit abonnement og logge på din konto.

## <a name="step-2-create-resources"></a>Trin 2: Oprette ressourcer

Oprette de ressourcer, der skal bruges til din nye skala sæt.

### <a name="resource-group"></a>Ressourcegruppe

Et virtuelt skala sæt skal være indeholdt i en ressourcegruppe.

1. Få en liste over tilgængelige placeringer, hvor du kan placere ressourcer:

        Get-AzureLocation | Sort Name | Select Name

2. Vælg en placering, der passer dig bedst, Erstat værdien af **$locName** med navn, placering, og opret derefter variablen:

        $locName = "location name from the list, such as Central US"

3. Erstat værdien af **$rgName** med det navn, du vil bruge til den nye ressourcegruppe og derefter oprette variablen: 

        $rgName = "resource group name"
        
4. Oprette ressourcegruppen:
    
        New-AzureRmResourceGroup -Name $rgName -Location $locName

    Du burde se noget i retning af dette eksempel:

        ResourceGroupName : myrg1
        Location          : centralus
        ProvisioningState : Succeeded
        Tags              :
        ResourceId        : /subscriptions/########-####-####-####-############/resourceGroups/myrg1

### <a name="storage-account"></a>Lagerplads konto

En lagerplads konto bruges af en virtuel maskine til at gemme operativsystem disken og diagnosticering data, der bruges til skalering. Når det er muligt, er det bedste fremgangsmåde at have en lagerplads konto for hvert virtuelt, der er oprettet i et sæt skala. Hvis du ikke kan planlægge i forhold til højst 20 FOS per lagerplads konto. Eksemplet i denne artikel viser tre lagerplads konti, der oprettes til tre virtuelle maskiner.

1. Erstat værdien af **$saName** med et navn til kontoen lagerplads. Teste et navn til at er entydige. 

        $saName = "storage account name"
        Get-AzureRmStorageAccountNameAvailability $saName

    Hvis svaret er **Sand**, er dit foreslåede navn er entydige.

3. Erstat værdien af **$saType** med typen konto, lager og derefter oprette variablen:  

        $saType = "storage account type"
        
    Mulige værdier er: Standard_LRS, Standard_GRS, Standard_RAGRS eller Premium_LRS.
        
4. Oprette kontoen:
    
        New-AzureRmStorageAccount -Name $saName -ResourceGroupName $rgName –Type $saType -Location $locName

    Du burde se noget i retning af dette eksempel:

        ResourceGroupName   : myrg1
        StorageAccountName  : myst1
        Id                  : /subscriptions/########-####-####-####-############/resourceGroups/myrg1/providers/Microsoft
                              .Storage/storageAccounts/myst1
        Location            : centralus
        AccountType         : StandardLRS
        CreationTime        : 3/15/2016 4:51:52 PM
        CustomDomain        :
        LastGeoFailoverTime :
        PrimaryEndpoints    : Microsoft.Azure.Management.Storage.Models.Endpoints
        PrimaryLocation     : centralus
        ProvisioningState   : Succeeded
        SecondaryEndpoints  :
        SecondaryLocation   :
        StatusOfPrimary     : Available
        StatusOfSecondary   :
        Tags                : {}
        Context             : Microsoft.WindowsAzure.Commands.Common.Storage.AzureStorageContext

5. Gentag trin 1 til 4 for at oprette tre lagerplads konti, for eksempel myst1, myst2 og myst3.

### <a name="virtual-network"></a>Virtuelt netværk

Et virtuelt netværk er påkrævet for virtuelle maskiner i sættet skala.

1. Erstat værdien af **$subnetName** med det navn, du vil bruge til undernettet i det virtuelle netværk og derefter oprette variablen: 

        $subnetName = "subnet name"
        
2. Oprette undernet konfiguration:
    
        $subnet = New-AzureRmVirtualNetworkSubnetConfig -Name $subnetName -AddressPrefix 10.0.0.0/24
        
    Adressepræfikset kan være forskellige fra netværket virtuel.

3. Erstat værdien af **$netName** med det navn, du vil bruge til det virtuelle netværk og derefter oprette variablen: 

        $netName = "virtual network name"
        
4. Oprette virtuelle netværket:
    
        $vnet = New-AzureRmVirtualNetwork -Name $netName -ResourceGroupName $rgName -Location $locName -AddressPrefix 10.0.0.0/16 -Subnet $subnet

### <a name="configuration-of-the-scale-set"></a>Konfiguration af sættet skala

Du har alle de ressourcer, du skal bruge skalaen konfigureres, så lad os oprette den.  

1. Erstat værdien af **$ipName** med det navn, du vil bruge til IP-konfigurationen og derefter oprette variablen: 

        $ipName = "IP configuration name"
        
2. Oprette IP-konfigurationen:

        $ipConfig = New-AzureRmVmssIpConfig -Name $ipName -LoadBalancerBackendAddressPoolsId $null -SubnetId $vnet.Subnets[0].Id

2. Erstat værdien af **$vmssConfig** med det navn, du vil bruge til skala sæt konfiguration og derefter oprette variablen:   

        $vmssConfig = "Scale set configuration name"
        
3. Oprette konfigurationen for sættet skala:

        $vmss = New-AzureRmVmssConfig -Location $locName -SkuCapacity 3 -SkuName "Standard_A0" -UpgradePolicyMode "manual"
        
    I dette eksempel viser en skala Angiv der oprettes med de tre virtuelle computere. Se [Virtuelt skala angiver oversigt over](virtual-machine-scale-sets-overview.md) yderligere oplysninger om kapaciteten af skala sæt. Dette trin omfatter også indstille størrelsen (kaldet SkuName) på de virtuelle maskiner i dialogboksen Angiv. For at finde en størrelse, der opfylder dine behov, kan du se på [størrelsen for virtuelle maskiner](../virtual-machines/virtual-machines-windows-sizes.md).
    
4. Føje interface netværkskonfiguration til skala sæt konfigurationen:
        
        Add-AzureRmVmssNetworkInterfaceConfiguration -VirtualMachineScaleSet $vmss -Name $vmssConfig -Primary $true -IPConfiguration $ipConfig
        
    Du burde se noget i retning af dette eksempel:

        Sku                   : Microsoft.Azure.Management.Compute.Models.Sku
        UpgradePolicy         : Microsoft.Azure.Management.Compute.Models.UpgradePolicy
        VirtualMachineProfile : Microsoft.Azure.Management.Compute.Models.VirtualMachineScaleSetVMProfile
        ProvisioningState     :
        OverProvision         :
        Id                    :
        Name                  :
        Type                  :
        Location              : Central US
        Tags                  :

#### <a name="operating-system-profile"></a>Operativsystem profil

1. Erstat værdien af **$computerName** sammen med præfikset computer navn, du vil bruge, og derefter oprette variablen: 

        $computerName = "computer name prefix"
        
2. Erstat værdien af **$adminName** på navnet på administratorkontoen på virtuelle maskiner, og opret derefter variablen:

        $adminName = "administrator account name"
        
3. Erstat værdien af **$adminPassword** med adgangskoden til kontoen, og opret derefter variablen:

        $adminPassword = "password for administrator accounts"
        
4. Oprette profilen operativsystem:

        Set-AzureRmVmssOsProfile -VirtualMachineScaleSet $vmss -ComputerNamePrefix $computerName -AdminUsername $adminName -AdminPassword $adminPassword

#### <a name="storage-profile"></a>Lagerplads profil

1. Erstat værdien af **$storageProfile** med det navn, du vil bruge til profilen lager og derefter oprette variablen:  

        $storageProfile = "storage profile name"
        
2. Oprette variablerne, der definerer billedet for at bruge:  
      
        $imagePublisher = "MicrosoftWindowsServer"
        $imageOffer = "WindowsServer"
        $imageSku = "2012-R2-Datacenter"
        
    For at finde oplysninger om andre billeder der skal bruges ved at se på [Naviger og vælge Azure virtuelt billeder med Windows PowerShell og Azure CLI](../virtual-machines/virtual-machines-windows-cli-ps-findimage.md).
        
3. Erstatte værdien af **$vhdContainers** med en liste, der indeholder de stier, hvor de virtuelle harddiske er gemt som "https://mystorage.blob.core.windows.net/vhds", og opret derefter variablen:
       
        $vhdContainers = @("https://myst1.blob.core.windows.net/vhds","https://myst2.blob.core.windows.net/vhds","https://myst3.blob.core.windows.net/vhds")
        
4. Oprette lagerplads profil:

        Set-AzureRmVmssStorageProfile -VirtualMachineScaleSet $vmss -ImageReferencePublisher $imagePublisher -ImageReferenceOffer $imageOffer -ImageReferenceSku $imageSku -ImageReferenceVersion "latest" -Name $storageProfile -VhdContainer $vhdContainers -OsDiskCreateOption "FromImage" -OsDiskCaching "None"  

### <a name="virtual-machine-scale-set"></a>Virtuelt skala sæt

Til sidst, kan du oprette sættet skala.

1. Erstat værdien af **$vmssName** med navnet på sættet virtuelt skala, og opret derefter variablen:

        $vmssName = "scale set name"
        
2. Oprette sættet skala:

        New-AzureRmVmss -ResourceGroupName $rgName -Name $vmssName -VirtualMachineScaleSet $vmss

    Du burde se noget i retning af dette eksempel, der viser en vellykket installation:

        Sku                   : Microsoft.Azure.Management.Compute.Models.Sku
        UpgradePolicy         : Microsoft.Azure.Management.Compute.Models.UpgradePolicy
        VirtualMachineProfile : Microsoft.Azure.Management.Compute.Models.VirtualMachineScaleSetVMProfile
        ProvisioningState     : Updating
        OverProvision         :
        Id                    : /subscriptions/########-####-####-####-############/resourceGroups/myrg1/providers/Microso
                                ft.Compute/virtualMachineScaleSets/myvmss1
        Name                  : myvmss1
        Type                  : Microsoft.Compute/virtualMachineScaleSets
        Location              : centralus
        Tags                  :

## <a name="step-3-explore-resources"></a>Trin 3: Oplev ressourcer

Brug disse ressourcer til at undersøge sættet virtuelt skala, du har oprettet:

- Azure portal - en begrænset mængde oplysninger er tilgængelige ved hjælp af portalen.
- [Azure ressource Explorer](https://resources.azure.com/) - dette værktøj er bedst for udforske den aktuelle tilstand for dit skala sæt.
- Azure PowerShell - Brug denne kommando for at få oplysninger:

        Get-AzureRmVmss -ResourceGroupName "resource group name" -VMScaleSetName "scale set name"
        
        Or 
        
        Get-AzureRmVmssVM -ResourceGroupName "resource group name" -VMScaleSetName "scale set name"
        

## <a name="next-steps"></a>Næste trin

- Administrere gruppen Skaler, du lige har oprettet ved hjælp af oplysningerne i [administrere virtuelle maskiner i et virtuelt skala sæt](virtual-machine-scale-sets-windows-manage.md)
- Overveje at oprette automatisk skalering af din målestoksforhold, der er angivet ved hjælp af oplysninger i [automatisk skalering og virtuelt skala angiver](virtual-machine-scale-sets-autoscale-overview.md)
- Få flere oplysninger om lodret skalering ved at gennemgå [lodret Autoskalering med virtuelt skala datasæt](virtual-machine-scale-sets-vertical-scale-reprovision.md)
