<properties 
   pageTitle="Almindelige PowerShell-kommandoer til FOS | Microsoft Azure"
   description="Almindelige PowerShell-kommandoer til at komme i gang oprettelse og administration af din FOS i Azure i Windows"
   services="virtual-machines-windows"
   documentationCenter=""
   authors="davidmu1" 
   manager="timlt" 
   editor="tysonn" 
   tags="azure-resource-manager"/>
   
<tags
   ms.service="virtual-machines-windows"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="vm-windows"
   ms.workload="infrastructure-services"
   ms.date="09/27/2016"
   ms.author="davidmu" />

# <a name="common-powershell-commands-for-creating-and-managing-vms"></a>Almindelige PowerShell-kommandoer til oprettelse og administration af FOS

I denne artikel beskrives nogle af de Azure PowerShell-kommandoer, som du kan bruge til at oprette og administrere virtuelle maskiner i abonnementet Azure.  Du kan bruge **Get-Help** *kommandoen*mere detaljeret hjælp til bestemte kommandolinjeparametre og indstillinger.

Se, [hvordan du installerer og konfigurerer Azure PowerShell](../powershell-install-configure.md) for at få oplysninger om at installere den nyeste version af Azure PowerShell, vælge dit abonnement og logge på din konto.

## <a name="create-a-vm"></a>Oprette en VM

Opgave | Kommandoen
-------------- | -------------------------
Oprette en VM-konfiguration | $vm = vm_size" [Ny AzureRmVMConfig](https://msdn.microsoft.com/library/mt603727.aspx) - VMName"vm_name"- VMSize"<BR></BR><BR></BR>VM konfigurationen bruges til at definere eller opdatere indstillinger for VM. Konfigurationen er initialiseret med navnet på VM og dets [størrelse](virtual-machines-windows-sizes.md).
Tilføj indstillinger for søgekonfiguration | $vm = [Sæt AzureRmVMOperatingSystem](https://msdn.microsoft.com/library/mt603843.aspx) - VM $vm-Windows - computernavn "computernavn"-legitimationsoplysninger $cred - ProvisionVMAgent - EnableAutoUpdate<BR></BR><BR></BR>Indstillinger i operativsystemet herunder [legitimationsoplysninger](https://technet.microsoft.com/library/hh849815.aspx) føjes til konfigurationsobjektet, som du tidligere har oprettet ved hjælp af ny AzureRmVMConfig.
Tilføj en netværksgrænseflade | $vm = [Tilføj AzureRmVMNetworkInterface](https://msdn.microsoft.com/library/mt619351.aspx) - VM $vm-Id $nic. Id<BR></BR><BR></BR>En VM skal have en [netværksgrænsefladen](virtual-machines-windows-ps-create.md) til at kommunikere i et virtuelt netværk. Du kan også bruge [Get-AzureRmNetworkInterface](https://msdn.microsoft.com/library/mt619434.aspx) til at hente et eksisterende netværk interface objekt.
Angive et platform billede | $vm = [Sæt AzureRmVMSourceImage](https://msdn.microsoft.com/library/mt619344.aspx) - VM $vm - PublisherName "publisher_name"-tilbyder "publisher_offer" - lagerenheder "product_sku"-"seneste" Version<BR></BR><BR></BR>[Billede af oplysninger](virtual-machines-windows-cli-ps-findimage.md) føjes til konfigurationsobjektet, som du tidligere har oprettet ved hjælp af ny AzureRmVMConfig. Det objekt, der returneres fra denne kommando bruges kun, når du indstiller OS disken til at bruge et billede af platform.
Angive OS disken for at bruge et billede af platform | $vm = [Sæt AzureRmVMOSDisk](https://msdn.microsoft.com/library/mt603746.aspx) - VM $vm-http://mystore1.blob.core.windows.net/vhds/disk_name.vhd"navn"disk_name"- VhdUri" - CreateOption FromImage<BR></BR><BR></BR>Navnet på disken operativsystem og dens placering i [lagerplads](../storage/storage-powershell-guide-full.md) føjes til konfigurationsobjektet, som du tidligere har oprettet.
Angive OS disken for at bruge et generalized billede | $vm = sæt AzureRmVMOSDisk - VM $vm-https://mystore1.blob.core.windows.net/system/Microsoft.Compute/Images/myimages/myprefix-osDisk"navn"disk_name"- SourceImageUri. {guid} .vhd"- VhdUri"https://mystore1.blob.core.windows.net/vhds/disk_name.vhd"- CreateOption FromImage-Windows<BR></BR><BR></BR>Navnet på disken operativsystem, placeringen af kildebilledet og den disk placering på [lager,](../storage/storage-powershell-guide-full.md) føjes til objektet configuration.
Angive OS disken for at bruge en særlig billede | $vm = sæt AzureRmVMOSDisk - VM $vm-http://mystore1.blob.core.windows.net/vhds/"navn"name_of_disk"- VhdUri" - CreateOption vedhæfte - Windows
Oprette en VM | [Ny AzureRmVM]() - ResourceGroupName "resource_group_name"-placering "location_name" - VM $vm<BR></BR><BR></BR>Alle ressourcer, der er oprettet i en [ressourcegruppe](../powershell-azure-resource-manager.md). VM skal oprettes på samme [placering](https://msdn.microsoft.com/library/azure/dn495177.aspx) som ressourcegruppen. Før du kører denne kommando, køre ny AzureRmVMConfig, sæt AzureRmVMOperatingSystem, angive AzureRmVMSourceImage, Tilføj AzureRmVMNetworkInterface og angive AzureRmVMOSDisk.

## <a name="get-information-about-vms"></a>Få oplysninger om FOS

Opgave | Kommandoen
-------------- | -------------------------
Listen FOS i et abonnement| [Get-AzureRmVM](https://msdn.microsoft.com/library/mt603718.aspx)
Listen FOS i en ressourcegruppe | Get-AzureRmVM - ResourceGroupName "resource_group_name"<BR></BR><BR></BR>For at få en liste over grupper i dit abonnement skal du bruge [Get-AzureRmResourceGroup](https://msdn.microsoft.com/library/mt679016.aspx).
Få oplysninger om en VM | Get-AzureRmVM - ResourceGroupName "resource_group_name"-navn "vm_name"

## <a name="manage-vms"></a>Administrere FOS

Opgave | Kommandoen
-------------- | -------------------------
Starte en VM | [Start-AzureRmVM](https://msdn.microsoft.com/library/mt603453.aspx) - ResourceGroupName "resource_group_name"-navn "vm_name"
Stoppe en VM | [Stop AzureRmVM](https://msdn.microsoft.com/library/mt603483.aspx) - ResourceGroupName "resource_group_name"-navn "vm_name"
Genstarte en igangværende VM | [Genstart AzureRmVM](https://msdn.microsoft.com/library/mt603775.aspx) - ResourceGroupName "resource_group_name"-navn "vm_name"
Slette en VM | [Fjern AzureRmVM](https://msdn.microsoft.com/library/mt603641.aspx) - ResourceGroupName "resource_group_name"-navn "vm_name"
Generalize en VM | [Sæt AzureRmVm](https://msdn.microsoft.com/library/mt603688.aspx) - ResourceGroupName YourResourceGroup-navn "vm_name"-Generalized<BR></BR><BR></BR>Kør denne kommando, før du kører Gem AzureRmVMImage.
Registrere en VM | [Gem AzureRmVMImage](https://msdn.microsoft.com/library/mt619423.aspx) - ResourceGroupName "resource_group_name" - VMName "vm_name" - DestinationContainerName "image_container" - VHDNamePrefix "image_name_prefix"-sti "C:\filepath\filename.json"<BR></BR><BR></BR>En virtuel maskine skal [lukke og generalized](virtual-machines-windows-generalize-vhd.md) skal bruges til at oprette et billede. Før du kører denne kommando, du Kør sæt AzureRmVm.
Opdatere en Virtual Machine | [Opdater AzureRmVM](https://msdn.microsoft.com/library/mt603662.aspx) - ResourceGroupName "resource_group_name" - VM $vm<BR></BR><BR></BR>Få den aktuelle VM konfiguration ved hjælp af Get-AzureRmVM, ændre konfigurationsindstillinger på VM objektet og derefter kører denne kommando.
Føje disken data til en VM | [Tilføj AzureRmVMDataDisk](https://msdn.microsoft.com/library/mt603673.aspx) - VM $vm-https://mystore1.blob.core.windows.net/vhds/disk_name.vhd"navn"disk_name"- VhdUri" - LUN #-cachelagring ReadWrite - DiskSizeinGB # - CreateOption Tøm<BR></BR><BR></BR>Brug Get-AzureRmVM til at få objektet VM. Angiv LUN antallet og størrelsen på disken. Køre Update-AzureRmVM for at anvende ændringerne i konfigurationen til VM. Disken, du tilføjer er ikke initialiseret. Finde oplysninger om initialisering af diske, som de er blevet tilføjet, [administrere virtuelle Azure-computere ved hjælp af Ressourcestyring og PowerShell](virtual-machines-windows-ps-manage.md).
Fjerne en datadisk fra en VM | [Fjern AzureRmVMDataDisk](https://msdn.microsoft.com/library/mt603614.aspx) - VM $vm-navn "disk_name"<BR></BR><BR></BR>Brug Get-AzureRmVM til at få objektet VM. Køre Update-AzureRmVM for at anvende ændringerne i konfigurationen til VM.
Tilføje et filtypenavn til en VM | [Sæt AzureRmVMExtension](https://msdn.microsoft.com/library/mt603745.aspx) - ResourceGroupName "resource_group_name"-vm_name"placering"azure_location"- VMName"-navn "extension_name"-Publisher "publisher_name"-Type "extension_type" - TypeHandlerVersion "#. #"-indstillinger for $Settings - ProtectedSettings $ProtectedSettings<BR></BR><BR></BR>Kør denne kommando med de relevante [oplysninger om konfiguration](virtual-machines-windows-extensions-configuration-samples.md) for det filtypenavn, du vil installere.
Fjerne en VM filtypenavn | [Fjern AzureRmVMExtension](https://msdn.microsoft.com/library/mt603782.aspx) - ResourceGroupName "resource_group_name"-vm_name"navn"extension_name"- VMName"

## <a name="next-steps"></a>Næste trin

- Se de grundlæggende trin til oprettelse af en virtuel maskine i [oprette en Windows-VM ved hjælp af Ressourcestyring og PowerShell](virtual-machines-windows-ps-create.md).

