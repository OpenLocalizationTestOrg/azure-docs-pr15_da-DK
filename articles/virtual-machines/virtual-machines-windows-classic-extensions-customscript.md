<properties
   pageTitle="Brugerdefineret Script lokalnummer på en Windows-VM | Microsoft Azure"
   description="Automatisere Azure VM konfigurationsopgaver ved hjælp af filtypenavnet brugerdefineret Script til at køre PowerShell-scripts på en ekstern Windows VM"
   services="virtual-machines-windows"
   documentationCenter=""
   authors="kundanap"
   manager="timlt"
   editor=""
   tags="azure-service-management"/>

<tags
   ms.service="virtual-machines-windows"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="vm-windows"
   ms.workload="infrastructure-services"
   ms.date="08/06/2015"
   ms.author="kundanap"/>

# <a name="custom-script-extension-for-windows-virtual-machines"></a>Brugerdefineret Script-udvidelse til Windows virtuelle maskiner

I denne artikel giver et overblik over, hvordan du bruger filtypenavnet brugerdefineret Script på Windows FOS ved hjælp af Azure PowerShell-cmdlet'er med Azure Service Management API'er.

Virtual machine (VM) filtypenavne er oprettet af Microsoft, og der er tillid til tredjepart udgivere du udvider funktionaliteten af VM. Se en oversigt over VM filtypenavne, [Azure VM extensions og funktioner](virtual-machines-windows-extensions-features.md).

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]Lær, hvordan du kan [udføre disse trin ved hjælp af Ressourcestyring modellen](virtual-machines-windows-extensions-customscript.md).

## <a name="custom-script-extension-overview"></a>Brugerdefineret Script lokalnummer oversigt

Du kan køre PowerShell-scripts på en ekstern VM med filtypenavnet brugerdefineret Script til Windows, uden at logge på den. Du kan køre scripts efter klargøring af VM, eller når som helst under livscyklus VM uden at åbne en ekstra porte. De mest almindelige use cases til at køre brugerdefineret Script lokalnummer omfatter kører, installation og konfiguration af yderligere software på VM, når den er klargjort.

### <a name="prerequisites-for-running-the-custom-script-extension"></a>Forudsætninger for at køre filtypenavnet brugerdefineret Script

1. Installer <a href="http://azure.microsoft.com/downloads" target="_blank">Azure PowerShell-cmdlet'er</a> version 0.8.0 eller nyere.
2. Hvis du vil scripts til at køre på en eksisterende VM skal kontrollere VM Agent er aktiveret på VM. Hvis det ikke er installeret, skal du følge disse [trin](virtual-machines-windows-classic-agents-and-extensions.md). Hvis VM er oprettet fra Azure-portalen, installeres VM Agent som standard.
3. Overføre de scripts, du vil køre på VM til Azure-lager. Scriptene kan komme fra en enkelt objektbeholder eller flere objektbeholdere.
4. Scriptet skal være skrevet, så posten script, som er startet af filtypenavnet, starter andre scripts.

## <a name="custom-script-extension-scenarios"></a>Brugerdefineret Script lokalnummer scenarier

### <a name="upload-files-to-the-default-container"></a>Overføre filer til objektbeholderen standard

I følgende eksempel viser, hvordan du kan køre scripts på VM, hvis de er i objektbeholderen til lagring af standardkontoen for dit abonnement. Du kan overføre dine scripts til ContainerName. Du kan kontrollere lagerplads standardkontoen ved hjælp af den **Get-AzureSubscription – standard** kommandoen.

I følgende eksempel oprettes en VM, men du kan også køre det samme scenario på en eksisterende VM.

    # Create a new VM in Azure.
    $vm = New-AzureVMConfig -Name $name -InstanceSize Small -ImageName $imagename
    $vm = Add-AzureProvisioningConfig -VM $vm -Windows -AdminUsername $username -Password $password
    // Add Custom Script extension to the VM. The container name refers to the storage container that contains the file.
    $vm = Set-AzureVMCustomScriptExtension -VM $vm -ContainerName $container -FileName 'start.ps1'
    New-AzureVM -ServiceName $servicename -Location $location -VMs $vm
    #  After the VM is created, the extension downloads the script from the storage location and executes it on the VM.

    # Viewing the  script execution output.
    $vm = Get-AzureVM -ServiceName $servicename -Name $name
    # Use the position of the extension in the output as index.
    $vm.ResourceExtensionStatusList[i].ExtensionSettingStatus.SubStatusList

### <a name="upload-files-to-a-non-default-storage-container"></a>Overføre filer til en objektbeholder til lagring af ikke-standard

Dette scenarie viser, hvordan du bruger en objektbeholder til lagring af ikke-standard i samme abonnement eller i et andet abonnement for overførsel af scripts og filer. I dette eksempel vises en eksisterende VM, men de samme handlinger kan udføres, mens du opretter en VM.

        Get-AzureVM -Name $name -ServiceName $servicename | Set-AzureVMCustomScriptExtension -StorageAccountName $storageaccount -StorageAccountKey $storagekey -ContainerName $container -FileName 'file1.ps1','file2.ps1' -Run 'file.ps1' | Update-AzureVM

### <a name="upload-scripts-to-multiple-containers-across-different-storage-accounts"></a>Overføre scripts til flere beholdere på tværs af forskellige lagerplads konti

  Hvis script-filerne gemmes på tværs af flere beholdere, har du giver fuld adgang til delte signaturer (SAS) URL-adressen for filerne, hvis du vil køre scripts.

      Get-AzureVM -Name $name -ServiceName $servicename | Set-AzureVMCustomScriptExtension -StorageAccountName $storageaccount -StorageAccountKey $storagekey -ContainerName $container -FileUri $fileUrl1, $fileUrl2 -Run 'file.ps1' | Update-AzureVM


### <a name="add-the-custom-script-extension-from-the-azure-portal"></a>Føje brugerdefineret Script-udvidelsen fra Azure-portalen

Gå til VM <a href="https://portal.azure.com/ " target="_blank">Azure-portalen</a> , og Tilføj filtypenavnet ved at angive scriptfilen for at køre.

  ![Angive scriptfil][5]


### <a name="uninstall-the-custom-script-extension"></a>Fjerne filtypenavnet brugerdefineret Script

Du kan fjerne filtypenavnet brugerdefineret Script fra VM ved hjælp af følgende kommando.

      get-azureVM -ServiceName KPTRDemo -Name KPTRDemo | Set-AzureVMCustomScriptExtension -Uninstall | Update-AzureVM

### <a name="use-the-custom-script-extension-with-templates"></a>Bruge filtypenavnet brugerdefineret Script med skabeloner

For at få mere for at vide om, hvordan du bruger filtypenavnet brugerdefineret Script med Azure ressourcestyring skabeloner, kan du se [Brug af brugerdefineret Script-udvidelse til Windows FOS med Azure ressourcestyring skabeloner](virtual-machines-windows-extensions-customscript.md).

<!--Image references-->
[5]: ./media/virtual-machines-windows-classic-extensions-customscript/addcse.png
