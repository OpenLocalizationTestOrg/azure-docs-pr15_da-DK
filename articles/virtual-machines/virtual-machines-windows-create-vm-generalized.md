<properties
    pageTitle="Oprette VM fra en generalized Virtuelle | Microsoft Azure"
    description="Lær at oprette en Windows virtuel maskine fra et generalized Virtuelle billede med Azure PowerShell i implementeringsmodel ressourcestyring."
    services="virtual-machines-windows"
    documentationCenter=""
    authors="cynthn"
    manager="timlt"
    editor=""
    tags="azure-resource-manager"/>

<tags
    ms.service="virtual-machines-windows"
    ms.workload="infrastructure-services"
    ms.tgt_pltfrm="vm-windows"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/10/2016"
    ms.author="cynthn"/>

# <a name="create-a-vm-from-a-generalized-vhd-image"></a>Oprette en VM fra et generalized Virtuelle billede

Billede af en generalized Virtuelle har haft alle dine personlige oplysninger fjernes ved hjælp af [Sysprep](virtual-machines-windows-generalize-vhd.md). Du kan oprette en generalized virtuel harddisk, ved at køre Sysprep på et lokalt VM, derefter [overføre Virtuelle til Azure](virtual-machines-windows-upload-image.md), eller ved at køre Sysprep på en eksisterende Azure VM og derefter [kopiere den virtuelle harddisk](virtual-machines-windows-vhd-copy.md).

Hvis du vil oprette en VM fra en særlig virtuel harddisk, skal du se [oprette en VM fra en særlig virtuel harddisk](virtual-machines-windows-create-vm-specialized.md).

Den hurtigste måde at oprette en VM fra en generalized Virtuelle er at bruge en [Hurtig start skabelon] (https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-from-user-image). 


## <a name="prerequisites"></a>Forudsætninger

Hvis du vil bruge en virtuel harddisk, der er overført fra en lokal VM, som oprettet ved hjælp af Hyper-V, skal du kontrollere du har fulgt vejledningen i [forberede en virtuel harddisk Windows til at overføre til Azure](virtual-machines-windows-prepare-for-upload-vhd-image.md). 

Både overførte virtuelle harddiske og eksisterende VM virtuelle harddiske Azure skal være generalized, før du kan oprette en VM ved hjælp af denne metode. Få mere at vide under [generalisere en Windows virtuel maskine brug af Sysprep](virtual-machines-windows-generalize-vhd.md). 


## <a name="set-the-uri-of-the-vhd"></a>Angive URI på den virtuelle harddisk

URI-adressen for den virtuelle harddisk bruge er i formatet: https://**mystorageaccount**.blob.core.windows.net/**mycontainer**/**MyVhdName**.vhd. I dette eksempel den virtuelle harddisk med navnet **myVHD** er i lagerplads konto **mystorageaccount** i beholderen **mycontainer**.

```powershell
$imageURI = "https://mystorageaccount.blob.core.windows.net/mycontainer/myVhd.vhd"
```


## <a name="create-a-virtual-network"></a>Oprette et virtuelt netværk

Oprette vNet og undernet af [virtuelt netværk](../virtual-network/virtual-networks-overview.md).


1. Opret undernettet. Følgende eksempelformel opretter et undernet med navnet **mySubnet** i ressource gruppe **myResourceGroup** sammen med adressepræfikset af **10.0.0.0/24**.  

    ```powershell
    $rgName = "myResourceGroup"
    $subnetName = "mySubnet"
    $singleSubnet = New-AzureRmVirtualNetworkSubnetConfig -Name $subnetName -AddressPrefix 10.0.0.0/24
    ```
      
2. Oprette det virtuelle netværk. Følgende eksempelformel opretter et virtuelt netværk med navnet **myVnet** i **Vest USA** placeringen sammen med adressepræfikset af **10.0.0.0/16**.  

    ```powershell
    $location = "West US"
    $vnetName = "myVnet"
    $vnet = New-AzureRmVirtualNetwork -Name $vnetName -ResourceGroupName $rgName -Location $location -AddressPrefix 10.0.0.0/16 -Subnet $singleSubnet
    ```    
            
## <a name="create-a-public-ip-address-and-network-interface"></a>Oprette en offentlig IP-adresse og netværk brugergrænseflade

For at aktivere kommunikation med den virtuelle maskine i det virtuelle netværk, skal have du en [offentlig IP-adresse](../virtual-network/virtual-network-ip-addresses-overview-arm.md) og en netværksgrænseflade.

1. Oprette en offentlig IP-adresse. I dette eksempel opretter en offentlig IP-adresse med navnet **myPip**. 

    ```powershell
    $ipName = "myPip"
    $pip = New-AzureRmPublicIpAddress -Name $ipName -ResourceGroupName $rgName -Location $location -AllocationMethod Dynamic
    ```       

2. Oprette på NIC. I dette eksempel oprettes en NIC med navnet **myNic**. 

    ```powershell
    $nicName = "myNic"
    $nic = New-AzureRmNetworkInterface -Name $nicName -ResourceGroupName $rgName -Location $location -SubnetId $vnet.Subnets[0].Id -PublicIpAddressId $pip.Id
    ```

## <a name="create-the-network-security-group-and-an-rdp-rule"></a>Oprette sikkerhedsgruppen netværk og en RDP-regel

Hvis du vil kunne logge på din VM ved hjælp af RDP, skal du har en Sikkerhedsregel for, der giver RDP adgang på port 3389. 

I dette eksempel opretter en NSG med navnet **myNsg** , der indeholder en regel, der kaldes **myRdpRule** , der tillader RDP-trafik via port 3389. Du kan finde flere oplysninger om NSGs ved [åbning af porte til en VM i Azure ved hjælp af PowerShell](virtual-machines-windows-nsg-quickstart-powershell.md).

```powershell
$nsgName = "myNsg"

$rdpRule = New-AzureRmNetworkSecurityRuleConfig -Name myRdpRule -Description "Allow RDP" `
    -Access Allow -Protocol Tcp -Direction Inbound -Priority 110 `
    -SourceAddressPrefix Internet -SourcePortRange * `
    -DestinationAddressPrefix * -DestinationPortRange 3389

$nsg = New-AzureRmNetworkSecurityGroup -ResourceGroupName $rgName -Location $location `
    -Name $nsgName -SecurityRules $rdpRule
```


## <a name="create-a-variable-for-the-virtual-network"></a>Oprette en variabel for det virtuelle netværk

Oprette en variabel for det færdige virtuelle netværk. 

```powershell
$vnet = Get-AzureRmVirtualNetwork -ResourceGroupName $rgName -Name $vnetName
```

## <a name="create-the-vm"></a>Oprette VM

Følgende PowerShell-script viser, hvordan du konfigurerer virtuelt konfigurationer og bruge det overførte billede VM som kilde for den nye installation.

</br>


```powershell
    # Enter a new user name and password to use as the local administrator account 
    # for remotely accessing the VM.
    $cred = Get-Credential
    
    # Name of the storage account where the VHD is located. This example sets the 
    # storage account name as "myStorageAccount"
    $storageAccName = "myStorageAccount"
    
    # Name of the virtual machine. This example sets the VM name as "myVM".
    $vmName = "myVM"
    
    # Size of the virtual machine. This example creates "Standard_D2_v2" sized VM. 
    # See the VM sizes documentation for more information: 
    # https://azure.microsoft.com/documentation/articles/virtual-machines-windows-sizes/
    $vmSize = "Standard_D2_v2"
    
    # Computer name for the VM. This examples sets the computer name as "myComputer".
    $computerName = "myComputer"
    
    # Name of the disk that holds the OS. This example sets the 
    # OS disk name as "myOsDisk"
    $osDiskName = "myOsDisk"
    
    # Assign a SKU name. This example sets the SKU name as "Standard_LRS"
    # Valid values for -SkuName are: Standard_LRS - locally redundant storage, Standard_ZRS - zone redundant storage, Standard_GRS - geo redundant storage, Standard_RAGRS - read access geo redundant storage, Premium_LRS - premium locally redundant storage. 
    $skuName = "Standard_LRS"
    
    # Get the storage account where the uploaded image is stored
    $storageAcc = Get-AzureRmStorageAccount -ResourceGroupName $rgName -AccountName $storageAccName
    
    # Set the VM name and size
    $vmConfig = New-AzureRmVMConfig -VMName $vmName -VMSize $vmSize
    
    #Set the Windows operating system configuration and add the NIC
    $vm = Set-AzureRmVMOperatingSystem -VM $vmConfig -Windows -ComputerName $computerName `
        -Credential $cred -ProvisionVMAgent -EnableAutoUpdate
    $vm = Add-AzureRmVMNetworkInterface -VM $vm -Id $nic.Id
    
    # Create the OS disk URI
    $osDiskUri = '{0}vhds/{1}-{2}.vhd' `
        -f $storageAcc.PrimaryEndpoints.Blob.ToString(), $vmName.ToLower(), $osDiskName
    
    # Configure the OS disk to be created from the existing VHD image (-CreateOption fromImage).
    $vm = Set-AzureRmVMOSDisk -VM $vm -Name $osDiskName -VhdUri $osDiskUri `
        -CreateOption fromImage -SourceImageUri $imageURI -Windows
    
    # Create the new VM
    New-AzureRmVM -ResourceGroupName $rgName -Location $location -VM $vm
```

## <a name="verify-that-the-vm-was-created"></a>Kontroller, at VM blev oprettet 

Når du er færdig, skal du se det nyoprettede VM [Azure portal](https://portal.azure.com) under **Søg** > **virtuelle maskiner**, eller ved hjælp af kommandoerne følgende PowerShell:

```powershell
    $vmList = Get-AzureRmVM -ResourceGroupName $rgName
    $vmList.Name
```

## <a name="next-steps"></a>Næste trin

For at administrere din nye virtuelle maskine med Azure PowerShell, skal du se [administrere virtuelle maskiner ved hjælp af Azure ressourcestyring og PowerShell](virtual-machines-windows-ps-manage.md).


