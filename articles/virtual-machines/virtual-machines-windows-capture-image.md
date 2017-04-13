<properties
    pageTitle="Tag et VM billede fra generalized Azure VM | Microsoft Azure"
    description="Lær at optage et VM billede fra en generalized Azure VM, der er oprettet i implementeringsmodel ressourcestyring"
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
    ms.date="10/20/2016"
    ms.author="cynthn"/>

# <a name="how-to-capture-a-vm-image-from-a-generalized-azure-vm"></a>Sådan Tag et VM billede fra en generalized Azure VM


I denne artikel viser, hvordan du bruger Azure PowerShell til at oprette et billede af en generalized Azure VM. Du kan derefter bruge billedet til at oprette en anden VM. Billedet indeholder OS disken og diskene data, der er knyttet til den virtuelle maskine. Billedet omfatter ikke ressourcerne, der virtuelt netværk, så du behøver at konfigurere disse ressourcer, når du opretter den nye VM. 


## <a name="prerequisites"></a>Forudsætninger

- Du skal have allerede [generalized VM](virtual-machines-windows-generalize-vhd.md). Generalisering en VM fjerner alle dine personlige oplysninger, blandt andet og laver computer der skal bruges som et billede.

- Du skal have Azure PowerShell version 1.0.x eller nyere installeret. Hvis du ikke har allerede installeret PowerShell, Læs, [hvordan du installerer og konfigurerer Azure PowerShell](../powershell-install-configure.md) til installationstrin.


## <a name="log-in-to-azure-powershell"></a>Log på Azure PowerShell

1. Åbne Azure PowerShell og logge på din Azure-konto.

    ```powershell
    Login-AzureRmAccount
    ```

    Der åbnes et pop op-vindue for dig at angive dine legitimationsoplysninger Azure-konto.

2. Få abonnementet id'er for dine tilgængelige abonnementer.

    ```powershell
    Get-AzureRmSubscription
    ```

3. Angive den korrekte abonnement via abonnement-ID.

    ```powershell
    Select-AzureRmSubscription -SubscriptionId "<subscriptionID>"
    ```

## <a name="deallocate-the-vm-and-set-the-state-to-generalized"></a>Dealloker VM og angive tilstanden til generalized       

1. Dealloker VM ressourcer.

    ```powershell
    Stop-AzureRmVM -ResourceGroupName <resourceGroup> -Name <vmName>
    ```

    *Status* for VM i portalen Azure ændres fra **showet afbrydes** til **showet afbrydes (deallokeres)**.

2. Angiv status for den virtuelle maskine til **Generalized**. 

    ```powershell
    Set-AzureRmVm -ResourceGroupName <resourceGroup> -Name <vmName> -Generalized
    ```

3. Kontrollere status for VM. Sektionen **OSState/generalized** for VM bør have **DisplayStatus** indstillet til **VM generalized**.  

    ```powershell
    $vm = Get-AzureRmVM -ResourceGroupName <resourceGroup> -Name <vmName> -Status
    $vm.Statuses
    ```

## <a name="create-the-image"></a>Oprette-billedet 

1. Kopiere billedet virtuelt til destination objektbeholderen til lagring af ved hjælp af denne kommando. Billedet er oprettet i den samme lagerplads konto som den oprindelige virtuelle maskine. Den `-Path` parameter gemmer en kopi af skabelonen JSON lokalt. Den `-DestinationContainerName` parameter er navnet på den beholder, der skal indeholde dine billeder. Hvis objektbeholderen ikke findes, oprettes den for dig.

    ```powershell
    Save-AzureRmVMImage -ResourceGroupName <resourceGroupName> -Name <vmName> `
        -DestinationContainerName <destinationContainerName> -VHDNamePrefix <templateNamePrefix> `
        -Path <C:\local\Filepath\Filename.json>
    ```

    Du kan finde URL-adressen på dit billede fra filskabelonen til JSON. Gå til **ressourcer** > **storageProfile** > **osDisk** > **billede** > **uri** sektion til den fulde sti i billedet. URL-adressen på billedet, der ser sådan ud: `https://<storageAccountName>.blob.core.windows.net/system/Microsoft.Compute/Images/<imagesContainer>/<templatePrefix-osDisk>.xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx.vhd`.
    
    Du kan også kontrollere URI på portalen. Billedet er kopieret til en objektbeholder med navnet **system** i kontoen lagerplads. 


## <a name="next-steps"></a>Næste trin

- Nu kan du [oprette en VM fra billedet](virtual-machines-windows-create-vm-generalized.md).

