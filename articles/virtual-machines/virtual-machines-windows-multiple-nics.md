<properties
   pageTitle="Oprette en Windows-VM med flere netværkskort | Microsoft Azure"
   description="Lær, hvordan du opretter en Windows-VM med flere netværkskort, der er knyttet til den ved hjælp af Azure PowerShell eller Ressourcestyring skabeloner."
   services="virtual-machines-windows"
   documentationCenter=""
   authors="iainfoulds"
   manager="timlt"
   editor=""/>

<tags
   ms.service="virtual-machines-windows"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="vm-windows"
   ms.workload="infrastructure"
   ms.date="10/27/2016"
   ms.author="iainfou"/>

# <a name="creating-a-windows-vm-with-multiple-nics"></a>Oprette en Windows-VM med flere netværkskort
Du kan oprette en virtuel maskine (VM) i Azure, der indeholder flere virtuelt netværk-grænseflader (NIC) til. Et almindeligt scenarie kunne være at har forskellige undernet til front end- og back end-forbindelse eller et dedikeret til en overvågning eller sikkerhedskopiering løsning netværk. I denne artikel indeholder hurtig kommandoer til at oprette en VM med flere netværkskort, der er knyttet til den. Du kan finde detaljerede oplysninger, herunder hvordan du opretter flere netværkskort i din egen PowerShell-scripts, få mere at vide om [installation af multi-NIC FOS](../virtual-network/virtual-network-deploy-multinic-arm-ps.md). Forskellige [VM størrelser](virtual-machines-windows-sizes.md) understøtter et skiftende antal netværkskort, så størrelse din VM i overensstemmelse hermed.

>[AZURE.WARNING] Når du opretter en VM – du kan ikke tilføje netværkskort til en eksisterende VM, skal du vedhæfte flere netværkskort. Du kan [oprette en VM, der er baseret på den oprindelige virtuelle disken(e)](virtual-machines-windows-vhd-copy.md) og oprette flere netværkskort, som du installerer VM.

## <a name="create-core-resources"></a>Oprette vigtige ressourcer
Sørg for, at du har de [seneste Azure PowerShell installeret og konfigureret](../powershell-install-configure.md). Log på din Azure-konto:

```powershell
Login-AzureRmAccount
```

Erstat eksempel parameternavne med dine egne værdier i eksemplerne nedenfor. Eksempel parameternavne inkluderet `myResourceGroup`, `mystorageaccount`, og `myVM`.

Opret først en ressourcegruppe. I følgende eksempel oprettes en ressourcegruppe med navnet `myResourceGroup` i den `WestUs` placering:

```powershell
New-AzureRmResourceGroup -Name "myResourceGroup" -Location "WestUS"
```

Oprette en lagerplads konto for at holde din FOS. I følgende eksempel oprettes en lagerplads kontoen `mystorageaccount`:

```powershell
$storageAcc = New-AzureRmStorageAccount -ResourceGroupName "myResourceGroup" `
    -Location "WestUS" -Name "mystorageaccount" `
    -Kind "Storage" -SkuName "Premium_LRS" 
```

## <a name="create-virtual-network-and-subnets"></a>Oprette virtuelle Netværks- og undernet
Definere to virtuelt netværksundernet - én til front end trafik og én til back end-trafik. I følgende eksempel definerer to undernet, med navnet `mySubnetFrontEnd` og `mySubnetBackEnd`:

```powershell
$mySubnetFrontEnd = New-AzureRmVirtualNetworkSubnetConfig -Name "mySubnetFrontEnd" `
    -AddressPrefix "192.168.1.0/24"
$mySubnetBackEnd = New-AzureRmVirtualNetworkSubnetConfig -Name "mySubnetBackEnd" `
    -AddressPrefix "192.168.2.0/24"
```

Oprette virtuelle netværket og undernet. I følgende eksempel oprettes et virtuelt netværk med navnet `myVnet`:

```powershell
$myVnet = New-AzureRmVirtualNetwork -ResourceGroupName "myResourceGroup" `
    -Location "WestUS" -Name "myVnet" -AddressPrefix "192.168.0.0/16" `
    -Subnet $mySubnetFrontEnd,$mySubnetBackEnd
```


## <a name="create-multiple-nics"></a>Oprette flere netværkskort
Opret to netværkskort, vedhæfte én NIC til front end-undernet og én NIC til back end-undernet. I følgende eksempel oprettes to netværkskort, med navnet `myNic1` og `myNic2`:

```powershell
$frontEnd = $myVnet.Subnets|?{$_.Name -eq 'mySubnetFrontEnd'}
$myNic1 = New-AzureRmNetworkInterface -ResourceGroupName "myResourceGroup" `
    -Location "WestUS" -Name "myNic1" -SubnetId $frontEnd.Id

$backEnd = $myVnet.Subnets|?{$_.Name -eq 'mySubnetBackEnd'}
$myNic2 = New-AzureRmNetworkInterface -ResourceGroupName "myResourceGroup" `
    -Location "WestUS" -Name "myNic2" -SubnetId $backEnd.Id
```

Du opretter typisk også en [netværk sikkerhedsgruppe](../virtual-network/virtual-networks-nsg.md) eller [justering af belastning](../load-balancer/load-balancer-overview.md) til at administrere og distribuere trafik på tværs af din FOS. [Mere detaljeret multi-NIC VM](../virtual-network/virtual-network-deploy-multinic-arm-ps.md) artikel hjælper dig med at oprette en sikkerhedsgruppe netværk og tildele netværkskort.


## <a name="create-the-virtual-machine"></a>Oprette den virtuelle maskine
Nu kan du begynde at opbygge din VM konfiguration. Hver VM størrelse har en grænse for det samlede antal netværkskort, som du kan føje til en VM. Få mere at vide om [Windows VM størrelser](virtual-machines-windows-sizes.md). 

Først skal du indstille dine VM legitimationsoplysninger den `$cred` variabel på følgende måde:

```powershell
$cred = Get-Credential
```

I følgende eksempel definerer en VM med navnet `myVM` og bruger en VM størrelse, der understøtter op til to netværkskort (`Standard_DS2_v2`):

```powershell
$vmConfig = New-AzureRmVMConfig -VMName "myVM" -VMSize "Standard_DS2_v2"
```

Oprette resten af VM config. I følgende eksempel oprettes en Windows Server 2012 R2 VM:

```powershell
$vmConfig = Set-AzureRmVMOperatingSystem -VM $vmConfig -Windows -ComputerName Te"MyVM" `
    -Credential $cred -ProvisionVMAgent -EnableAutoUpdate
$vmConfig = Set-AzureRmVMSourceImage -VM $vmConfig -PublisherName "MicrosoftWindowsServer" `
    -Offer "WindowsServer" -Skus "2012-R2-Datacenter" -Version "latest"
```

Vedhæfte de to netværkskort, du har oprettet tidligere:

```powershell
$vmConfig = Add-AzureRmVMNetworkInterface -VM $vmConfig -Id $myNic1.Id -Primary
$vmConfig = Add-AzureRmVMNetworkInterface -VM $vmConfig -Id $myNic2.Id
```

Konfigurere opbevaring og virtuelle disk til din nye VM:

```powershell
$blobPath = "vhds/WindowsVMosDisk.vhd"
$osDiskUri = $storageAcc.PrimaryEndpoints.Blob.ToString() + $blobPath
$diskName = "windowsvmosdisk"
$vmConfig = Set-AzureRmVMOSDisk -VM $vmConfig -Name $diskName -VhdUri $osDiskUri `
    -CreateOption "fromImage"
```

Til sidst skal du oprette en VM:

```powershell
New-AzureRmVM -VM $vmConfig -ResourceGroupName "myResourceGroup" -Location "WestUS"
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

Du kan også bruge en `copyIndex()` derefter føje et tal til et ressourcenavn, hvor du kan oprette `myNic1`, `MyNic2`osv. Følgende viser et eksempel på tilføje indeksværdien:

```bash
"name": "[concat('myNic', copyIndex())]", 
```

Du kan få en komplet eksempel på [oprettelse af flere netværkskort ved hjælp af Ressourcestyring skabeloner](../virtual-network/virtual-network-deploy-multinic-arm-template.md).

## <a name="next-steps"></a>Næste trin
Sørg for at gennemse [Windows VM størrelser](virtual-machines-windows-sizes.md) , når du forsøger at oprette en VM med flere netværkskort. Læg mærke til det maksimale antal netværkskort understøtter hver VM størrelse. 

Husk, at du ikke føje flere netværkskort til en eksisterende VM, skal du oprette alle netværkskortene, når du installerer VM. Tager sig, når du planlægger dine installationer at sikre dig, at du har alle de nødvendige netværksforbindelsen fra begyndelsen.