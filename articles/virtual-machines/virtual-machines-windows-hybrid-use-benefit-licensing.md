<properties
   pageTitle="Azure hybride Brug fordel for vinduet Server | Microsoft Azure"
   description="Lær at maksimere din Windows Server Software Assurance-fordelene for at få lokal licenser til Azure"
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
   ms.workload="infrastructure-services"
   ms.date="07/13/2016"
   ms.author="georgem"/>

# <a name="azure-hybrid-use-benefit-for-windows-server"></a>Azure Hybrid Brug fordel for Windows Server

Kunder, der bruger Windows Server med Software Assurance, kan du flytte dine lokale Windows Server-licenser til Azure og køre Windows Server FOS i Azure til en reduceret pris. Azure Hybrid Brug Benefit kan du kører Windows Server FOS i Azure og kun få faktureret for den grundlæggende Beregn rente. Du kan finde flere oplysninger, skal du se [Azure Hybrid Brug Benefit licensering side](https://azure.microsoft.com/pricing/hybrid-use-benefit/). I denne artikel beskrives det, hvordan du kan installere Windows Server FOS i Azure at bruge denne licensering fordel.

> [AZURE.NOTE] Du kan ikke bruge Azure Marketplace billeder til at installere Windows Server FOS udnytter Azure Hybrid Brug Benefit. Du skal installere din FOS ved hjælp af PowerShell eller Ressourcestyring skabeloner til at registrere din FOS korrekt som berettiget til grundlæggende Beregn rente rabat.

## <a name="pre-requisites"></a>Forudsætninger
Der er et par forudsætninger for at kunne udnytte Azure Hybrid Brug Benefit til Windows Server FOS i Azure:

- Har af Azure PowerShell-modulet, der er installeret
- Har din Windows Server Virtuelle, der er overført til Azure-lager

### <a name="install-azure-powershell"></a>Installere Azure PowerShell
Sørg for, at du har [installeret og konfigureret den seneste Azure PowerShell](../powershell-install-configure.md). Selvom du vil installere din FOS ved hjælp af Ressourcestyring skabeloner, skal du stadig Azure PowerShell installeret for at overføre dine Windows Server Virtuelle (se følgende trin).

### <a name="upload-a-windows-server-vhd"></a>Overføre en Windows Server Virtuelle

Du skal installere en Windows Server VM i Azure, skal du først oprette en virtuel harddisk, der indeholder dine grundlæggende Windows Server build. Denne Virtuelle skal være korrekt forberedt via Sysprep, før du overføre den til Azure. Du kan [læse mere om Virtuelle krav og Sysprep proces](./virtual-machines-windows-upload-image.md) og [Sysprep-understøttelse af serverroller](https://msdn.microsoft.com/windows/hardware/commercialize/manufacture/desktop/sysprep-support-for-server-roles). Sikkerhedskopiere VM, før du kører Sysprep. Når du har forberedt din Virtuelle, overføre den virtuelle harddisk til din Azure-lager konto med den `Add-AzureRmVhd` cmdlet på følgende måde:

```
Add-AzureRmVhd -ResourceGroupName MyResourceGroup -Destination "https://mystorageaccount.blob.core.windows.net/vhds/myvhd.vhd" -LocalFilePath 'C:\Path\To\myvhd.vhd'
```

> [AZURE.NOTE] Microsoft SQL Server, SharePoint Server og Dynamics kan også bruge din Software Assurance licenser. Du stadig har brug at forberede Windows Server billedet ved installation af din programkomponenter og give licens taster i overensstemmelse hermed og derefter overføre disk billedet til Azure. Gennemse den relevante dokumentation for at køre Sysprep med dit program, som [overvejelser i forbindelse med installation af SQL Server ved hjælp af Sysprep](https://msdn.microsoft.com/library/ee210754.aspx) eller [opbygge et SharePoint Server 2016 Reference billede (Sysprep)](http://social.technet.microsoft.com/wiki/contents/articles/33789.build-a-sharepoint-server-2016-reference-image-sysprep.aspx).

Du kan også læse mere om [overførsel af den virtuelle harddisk til Azure proces](./virtual-machines-windows-upload-image.md#upload-the-vm-image-to-your-storage-account).

> [AZURE.TIP] I denne artikel fokuserer på installation af Windows Server FOS. Du kan også installere Windows-klienten FOS på samme måde. I følgende eksempler du erstatte `Server` med `Client` korrekt.

## <a name="deploy-a-vm-via-powershell-quick-start"></a>Installere en VM via PowerShell Hurtig Start
Når du installerer din Windows Server VM via PowerShell, du har en ekstra parameter for `-LicenseType`. Når du har din Virtuelle, der er overført til Azure, skal du oprette en ny VM ved hjælp af `New-AzureRmVM` og angive, hvilke licenser på følgende måde:

```
New-AzureRmVM -ResourceGroupName MyResourceGroup -Location "West US" -VM $vm -LicenseType Windows_Server
```

Du kan [læse en mere detaljeret gennemgang om installation af en VM i Azure via PowerShell](./virtual-machines-windows-hybrid-use-benefit-licensing.md#deploy-windows-server-vm-via-powershell-detailed-walkthrough) nedenfor, eller Læs en mere beskrivende vejledning i de forskellige trin til at [oprette en Windows-VM ved hjælp af Ressourcestyring og PowerShell](./virtual-machines-windows-ps-create.md).

## <a name="deploy-a-vm-via-resource-manager"></a>Installere en VM via ressourcestyring
I ressourcestyring skabelonerne, en ekstra parameter for `licenseType` kan angives. Du kan læse mere om [redigering Azure ressourcestyring skabeloner](../resource-group-authoring-templates.md). Når du har din Virtuelle, der er overført til Azure, redigerer du ressourcestyring skabelon for at medtage licenstype som en del af provideren Beregn og anvende din skabelon som normalt:

```
"properties": {  
   "licenseType": "Windows_Server",
   "hardwareProfile": {
        "vmSize": "[variables('vmSize')]"
   },
```
 
## <a name="verify-your-vm-is-utilizing-the-licensing-benefit"></a>Bekræfte din VM udnytter licensering fordelen
Når du har installeret din VM gennem enten installationsmetode PowerShell eller Ressourcestyring, bekræfte licenstype med `Get-AzureRmVM` på følgende måde:
 
```
Get-AzureRmVM -ResourceGroup MyResourceGroup -Name MyVM
```

Output ligner følgende:

```
Type                     : Microsoft.Compute/virtualMachines
Location                 : westus
LicenseType              : Windows_Server
```

Dette står i kontrast med følgende VM installeres uden Azure hybride Brug Benefit licenser, som en VM installeres direkte fra galleriet Azure:

```
Type                     : Microsoft.Compute/virtualMachines
Location                 : westus
LicenseType              : 
```
 
## <a name="detailed-powershell-walkthrough"></a>Detaljeret gennemgang af PowerShell

De følgende detaljerede PowerShell trin viser en komplet installation af en VM. Du kan læse mere kontekst som de faktiske cmdletter og forskellige komponenter, der oprettes i [oprette en Windows-VM ved hjælp af Ressourcestyring og PowerShell](./virtual-machines-windows-ps-create.md). Du gennemgår oprettelse af din ressourcegruppe, lagerplads konto og virtuelle netværk, og derefter definere din VM og til sidst oprette din VM.
 
Først skal sikkert få legitimationsoplysninger, skal du angive en placering og ressource gruppenavn:

```
$cred = Get-Credential
$location = "West US"
$resourceGroupName = "TestLicensing"
```

Oprette en offentlige IP-adresse:

```
$publicIPName = "testlicensingpublicip"
$publicIP = New-AzureRmPublicIpAddress -Name $publicIPName -ResourceGroupName $resourceGroupName -Location $location -AllocationMethod Dynamic
```

Definere dit undernet, NIC og VNET:

```
$subnetName = "testlicensingsubnet"
$nicName = "testlicensingnic"
$vnetName = "testlicensingvnet"
$subnetconfig = New-AzureRmVirtualNetworkSubnetConfig -Name $subnetName -AddressPrefix 10.0.0.0/8
$vnet = New-AzureRmVirtualNetwork -Name $vnetName -ResourceGroupName $resourceGroupName -Location $location -AddressPrefix 10.0.0.0/8 -Subnet $subnetconfig
$nic = New-AzureRmNetworkInterface -Name $nicName -ResourceGroupName $resourceGroupName -Location $location -SubnetId $vnet.Subnets[0].Id -PublicIpAddressId $publicIP.Id
```

Navngiv din VM og oprette en VM config:

```
$vmName = "testlicensing"
$vmConfig = New-AzureRmVMConfig -VMName $vmName -VMSize "Standard_A1"
```

Definere dine OS:

```
$computerName = "testlicensing"
$vm = Set-AzureRmVMOperatingSystem -VM $vmConfig -Windows -ComputerName $computerName -Credential $cred -ProvisionVMAgent -EnableAutoUpdate
```

Tilføje dit Netværkskort til VM:

```
$vm = Add-AzureRmVMNetworkInterface -VM $vm -Id $nic.Id
```

Definere lagerplads konto til at bruge:

```
$storageAcc = Get-AzureRmStorageAccount -ResourceGroupName $resourceGroupName -AccountName testlicensing
```

Overføre din Virtuelle, passende forberedt og vedhæfte til dine VM til brug:

```
$osDiskName = "licensing.vhd"
$osDiskUri = '{0}vhds/{1}{2}.vhd' -f $storageAcc.PrimaryEndpoints.Blob.ToString(), $vmName.ToLower(), $osDiskName
$urlOfUploadedImageVhd = "https://testlicensing.blob.core.windows.net/vhd/licensing.vhd"
$vm = Set-AzureRmVMOSDisk -VM $vm -Name $osDiskName -VhdUri $osDiskUri -CreateOption FromImage -SourceImageUri $urlOfUploadedImageVhd -Windows
```

Til sidst skal du oprette din VM og definerer typen licenser for at udnytte Azure Hybrid Brug Benefit:

```
New-AzureRmVM -ResourceGroupName $resourceGroupName -Location $location -VM $vm -LicenseType Windows_Server
```

## <a name="next-steps"></a>Næste trin

Få mere at vide om [Azure Hybrid Brug Benefit licensen](https://azure.microsoft.com/pricing/hybrid-use-benefit/).

Lær mere om [Brug af Ressourcestyring skabeloner](../azure-resource-manager/resource-group-overview.md).
