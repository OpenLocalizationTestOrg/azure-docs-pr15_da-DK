<properties 
    pageTitle="Geninstaller Windows virtuelle maskiner | Microsoft Azure" 
    description="Beskriver, hvordan du Geninstaller Windows virtuelle maskiner for at reducere RDP forbindelsesproblemer." 
    services="virtual-machines-windows" 
    documentationCenter="virtual-machines" 
    authors="iainfoulds" 
    manager="timlt"
    tags="azure-resource-manager,top-support-issue" 
/>
    

<tags 
    ms.service="virtual-machines-windows" 
    ms.devlang="na" 
    ms.topic="support-article" 
    ms.tgt_pltfrm="vm-windows"
    ms.workload="infrastructure" 
    ms.date="09/19/2016" 
    ms.author="iainfou" 
/>


# <a name="redeploy-virtual-machine-to-new-azure-node"></a>Geninstaller virtuelt til nye Azure node

Hvis du har modstående problemer kan fejlfinding RDP (Remote Desktop) forbindelse eller program adgang til Windows-baseret Azure VM (virtual machine), geninstallation VM hjælpe. Når du Geninstaller en VM, flyttes VM til en ny node i Azure infrastruktur og styrer det tilbage til, bevares alle dine konfigurationsindstillinger og de tilknyttede ressourcer. I denne artikel beskrives, hvordan geninstallere en VM ved hjælp af Azure PowerShell eller Azure portalen.

> [AZURE.NOTE] Når du Geninstaller en VM, midlertidige disken går tabt, og dynamiske IP-adresser, der er knyttet til virtuelt netværk interface opdateres. 

## <a name="using-azure-powershell"></a>Brug af Azure PowerShell

Sørg for, at du har den seneste Azure PowerShell 1.x, der er installeret på computeren. Se, [hvordan du installerer og konfigurerer Azure PowerShell](../powershell-install-configure.md)kan finde flere oplysninger.

Brug denne Azure PowerShell-kommando til at geninstallere din virtuelle maskine:

    Set-AzureRmVM -Redeploy -ResourceGroupName $rgname -Name $vmname 


[AZURE.INCLUDE [virtual-machines-common-redeploy-to-new-node](../../includes/virtual-machines-common-redeploy-to-new-node.md)]


## <a name="next-steps"></a>Næste trin
Hvis du har problemer med at oprette forbindelse til din VM, kan du finde hjælp til [fejlfinding i forbindelse med RDP forbindelser](virtual-machines-windows-troubleshoot-rdp-connection.md) eller [detaljerede RDP fejlfindingstrin](virtual-machines-windows-detailed-troubleshoot-rdp.md). Hvis du ikke kan få adgang til et program, der kører på din VM, kan du også læse [programmet fejlfinding af problemer](virtual-machines-windows-troubleshoot-app-connection.md).