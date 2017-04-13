<properties
    pageTitle="Oprette en kopi af din Windows-VM | Microsoft Azure"
    description="Lær at oprette en kopi af din specialiserede Azure VM, der kører Windows, i implementeringsmodel ressourcestyring."
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
    ms.date="09/21/2016"
    ms.author="cynthn"/>

# <a name="create-a-vm-from-a-specialized-vhd"></a>Oprette en VM fra en særlig virtuel harddisk

Oprette en ny VM ved at knytte en særlig Virtuelle som OS disken ved hjælp af Powershell. En særlig Virtuelle fører brugerkonti, programmer og andre tilstand data fra din oprindelige VM. 

Hvis du vil oprette en VM fra en generalized virtuel harddisk, skal du se [oprette en VM fra et generalized Virtuelle billede](virtual-machines-windows-create-vm-generalized.md).

## <a name="create-the-subnet-and-vnet"></a>Oprette undernet og vNet

Oprette vNet og undernet af [virtuelt netværk](../virtual-network/virtual-networks-overview.md).

1. Opret undernettet. I dette eksempel opretter et undernet med navnet **mySubNet**i gruppen ressource **myResourceGroup**, og angiver præfikset undernet adresse til **10.0.0.0/24**.

    ```powershell
    $rgName = "myResourceGroup"
    $subnetName = "mySubNet"
    $singleSubnet = New-AzureRmVirtualNetworkSubnetConfig -Name $subnetName -AddressPrefix 10.0.0.0/24
    ```

2. Oprette vNet. I dette eksempel angiver navnet på det virtuelle netværk skal være **myVnetName**, placeringen for at **Vest USA**og adressepræfikset for det virtuelle netværk til **10.0.0.0/16**. 

    ```powershell
    $location = "West US"
    $vnetName = "myVnetName"
    $vnet = New-AzureRmVirtualNetwork -Name $vnetName -ResourceGroupName $rgName -Location $location -AddressPrefix 10.0.0.0/16 -Subnet $singleSubnet
    ```    
            
## <a name="create-a-public-ip-address-and-nic"></a>Oprette en offentlig IP-adresse og NIC

For at aktivere kommunikation med den virtuelle maskine i det virtuelle netværk, skal have du en [offentlig IP-adresse](../virtual-network/virtual-network-ip-addresses-overview-arm.md) og en netværksgrænseflade.

1. Oprette den offentlige IP-adresse. I dette eksempel er offentlige IP-adressen angivet til **myIP**.

    ```powershell
    $ipName = "myIP"
    $pip = New-AzureRmPublicIpAddress -Name $ipName -ResourceGroupName $rgName -Location $location -AllocationMethod Dynamic
    ```       

2. Oprette på NIC. I dette eksempel er NIC navnet angivet til **myNicName**.

    ```powershell
    $nicName = "myNicName"
    $nic = New-AzureRmNetworkInterface -Name $nicName -ResourceGroupName $rgName -Location $location -SubnetId $vnet.Subnets[0].Id -PublicIpAddressId $pip.Id
    ```

## <a name="create-the-network-security-group-and-an-rdp-rule"></a>Oprette sikkerhedsgruppen netværk og en RDP-regel

Hvis du vil kunne logge på din VM ved hjælp af RDP, vil du have en sikkerhedsregel, der tillader RDP adgang via port 3389. Da den virtuelle harddisk til den nye VM blev oprettet ud fra en eksisterende kan specialiserede VM, når VM er blevet oprettet du bruge en eksisterende konto fra den kilde virtuelle maskine, der havde tilladelse til at logge på ved hjælp af RDP.

I dette eksempel angiver NSG navnet til **myNsg** og regelnavn RDP til **myRdpRule**.

```powershell
$nsgName = "myNsg"

$rdpRule = New-AzureRmNetworkSecurityRuleConfig -Name myRdpRule -Description "Allow RDP" `
    -Access Allow -Protocol Tcp -Direction Inbound -Priority 110 `
    -SourceAddressPrefix Internet -SourcePortRange * `
    -DestinationAddressPrefix * -DestinationPortRange 3389

$nsg = New-AzureRmNetworkSecurityGroup -ResourceGroupName $rgName -Location $location `
    -Name $nsgName -SecurityRules $rdpRule
```

Du kan finde flere oplysninger om slutpunkter og NSG regler, [åbning af porte til en VM i Azure ved hjælp af PowerShell](virtual-machines-windows-nsg-quickstart-powershell.md).

## <a name="create-the-vm-configuration"></a>Oprette VM konfiguration

Konfigurere VM konfiguration til at vedhæfte den kopierede virtuelle harddisk som den virtuelle harddisk OS.


```powershell
    # Set the URI for the VHD that you want to use. In this example, the VHD file named "myOsDisk.vhd" is kept in a storage account named "myStorageAccount" in a container named "myContainer".
    $osDiskUri = "https://myStorageAccount.blob.core.windows.net/myContainer/myOsDisk.vhd"
    
    #Set the VM name and size. This example sets the VM name to "myVM" and the VM size to "Standard_A2".
    $vmName = "myVM"
    $vmConfig = New-AzureRmVMConfig -VMName $vmName -VMSize "Standard_A2"

    #Add the NIC
    $vm = Add-AzureRmVMNetworkInterface -VM $vmConfig -Id $nic.Id

    #Add the OS disk by using the URL of the copied OS VHD. In this example, when the OS disk is created, the term "osDisk" is appened to the VM name to create the OS disk name. This example also specifies that this Windows-based VHD should be attached to the VM as the OS disk.
    $osDiskName = $vmName + "osDisk"
    $vm = Set-AzureRmVMOSDisk -VM $vm -Name $osDiskName -VhdUri $osDiskUri -CreateOption attach -Windows
```


Hvis du har datadisce, der skal knyttes til VM, bør du også tilføje følgende: 

```powershell
    # Optional: Add data disks by using the URLs of the copied data VHDs at the appropriate Logical Unit Number (Lun).
    $dataDiskName = $vmName + "dataDisk"
    $vm = Add-AzureRmVMDataDisk -VM $vm -Name $dataDiskName -VhdUri $dataDiskUri -Lun 0 -CreateOption attach
```

Data og operativsystem disk URL-adresser ser omtrent sådan ud: `https://StorageAccountName.blob.core.windows.net/BlobContainerName/DiskName.vhd`. Du kan finde dette på portalen ved at navigere til objektbeholderen til lagring af destination, klikke på operativsystemet eller data Virtuelle, som blev kopieret og derefter kopiere indholdet af URL-adressen.


## <a name="create-the-vm"></a>Oprette VM

Oprette VM ved hjælp af de konfigurationer, vi lige har oprettet.

```powershell
#Create the new VM
New-AzureRmVM -ResourceGroupName $rgName -Location $location -VM $vm
```

Hvis denne kommando blev fuldført, skal se du output således:

```
RequestId IsSuccessStatusCode StatusCode ReasonPhrase
--------- ------------------- ---------- ------------
                         True         OK OK   
 
```
 
## <a name="verify-that-the-vm-was-created"></a>Kontroller, at VM blev oprettet 
 
Du skal se det nyoprettede VM enten i [Azure-portalen](https://portal.azure.com), under **Søg** > **virtuelle maskiner**, eller ved hjælp af kommandoerne følgende PowerShell:

```powershell
    $vmList = Get-AzureRmVM -ResourceGroupName $rgName
    $vmList.Name
```

## <a name="next-steps"></a>Næste trin

Gå til VM i [portalen](https://portal.azure.com)for at logge på din nye virtuelle maskine, klik på **Opret forbindelse**, og Åbn filen RDP Remote Desktop. Brug af din oprindelige virtuelle maskine kontolegitimationsoplysninger til at logge på din nye virtuelle maskine. Se, [hvordan du opretter forbindelse og logge på en Azure virtuelt, der kører Windows](virtual-machines-windows-connect-logon.md)kan finde flere oplysninger.







