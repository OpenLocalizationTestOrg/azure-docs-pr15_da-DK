<properties
    pageTitle="Flytte en Windows VM | Microsoft Azure"
    description="Flytte en Windows-VM til en anden Azure-abonnement eller ressource i implementeringsmodel ressourcestyring."
    services="virtual-machines-windows"
    documentationCenter=""
    authors="cynthn"
    manager="timlt"
    editor=""
    tags="azure-resource-manager"/>

<tags
    ms.service="virtual-machines-windows"
    ms.workload="infrastructure-services"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/08/2016"
    ms.author="cynthn"/>

    


# <a name="move-a-windows-vm-to-another-azure-subscription-or-resource-group"></a>Flytte en Windows-VM til en anden Azure-abonnement eller ressource 

I denne artikel vejledes du gennem Sådan flytter du en Windows-VM mellem grupper eller abonnementer. Flytte mellem abonnementer på kan være praktisk, hvis du oprindeligt har oprettet en VM i en personal-abonnement og nu vil du flytte den til din organisations abonnement for at fortsætte dit arbejde.

> [AZURE.NOTE] Ny ressource id'er oprettes som en del af Flyt. Når VM er blevet flyttet, skal du opdatere din værktøjer og scripts kan bruge den nye ressource id'er. 


[AZURE.INCLUDE [virtual-machines-common-move-vm](../../includes/virtual-machines-common-move-vm.md)]


## <a name="use-powershell-to-move-a-vm"></a>Bruge Powershell til at flytte en VM

Hvis du vil flytte en virtuel maskine til en anden ressourcegruppe, skal du kontrollere, at du også flytte alle de ressourcer, der er afhængige. Hvis du vil bruge Cmdletten Flyt AzureRMResource, skal du navnet på ressourcen og typen ressource. Du kan få fra Cmdletten finde AzureRMResource.

    Find-AzureRMResource -ResourceGroupNameContains "<sourceResourceGroupName>"
    

Hvis du vil flytte en VM skal vi flytte flere ressourcer. Vi kan kun oprette separate variabler for hver ressource og derefter skrive dem. I dette eksempel indeholder de fleste af de grundlæggende ressourcer til en VM, men du kan tilføje mere efter behov.

    $sourceRG = "<sourceResourceGroupName>"
    $destinationRG = "<destinationResourceGroupName>"
    
    $vm = Get-AzureRmResource -ResourceGroupName $sourceRG -ResourceType "Microsoft.Compute/virtualMachines" -ResourceName "<vmName>"
    $storageAccount = Get-AzureRmResource -ResourceGroupName $sourceRG -ResourceType "Microsoft.Storage/storageAccounts" -ResourceName "<storageAccountName>"
    $diagStorageAccount = Get-AzureRmResource -ResourceGroupName $sourceRG -ResourceType "Microsoft.Storage/storageAccounts" -ResourceName "<diagnosticStorageAccountName>"
    $vNet = Get-AzureRmResource -ResourceGroupName $sourceRG -ResourceType "Microsoft.Network/virtualNetworks" -ResourceName "<vNetName>"
    $nic = Get-AzureRmResource -ResourceGroupName $sourceRG -ResourceType "Microsoft.Network/networkInterfaces" -ResourceName "<nicName>"
    $ip = Get-AzureRmResource -ResourceGroupName $sourceRG -ResourceType "Microsoft.Network/publicIPAddresses" -ResourceName "<ipName>"
    $nsg = Get-AzureRmResource -ResourceGroupName $sourceRG -ResourceType "Microsoft.Network/networkSecurityGroups" -ResourceName "<nsgName>"
    
    Move-AzureRmResource -DestinationResourceGroupName $destinationRG -ResourceId $vm.ResourceId, $storageAccount.ResourceId, $diagStorageAccount.ResourceId, $vNet.ResourceId, $nic.ResourceId, $ip.ResourceId, $nsg.ResourceId

For at flytte ressourcerne til andet abonnement, skal du medtage parameteren **- DestinationSubscriptionId** . 

    Move-AzureRmResource -DestinationSubscriptionId "<destinationSubscriptionID>" -DestinationResourceGroupName $destinationRG -ResourceId $vm.ResourceId, $storageAccount.ResourceId, $diagStorageAccount.ResourceId, $vNet.ResourceId, $nic.ResourceId, $ip.ResourceId, $nsg.ResourceId



Du bliver bedt om at bekræfte, at du vil flytte de angivne ressourcer. Skriv **Y** for at bekræfte, at du vil flytte ressourcerne.

  
## <a name="next-steps"></a>Næste trin

Du kan flytte mange forskellige typer ressourcer mellem grupper og abonnementer. Du kan finde flere oplysninger, kan du se [flytte ressourcer til ny ressourcegruppe eller et andet abonnement](../resource-group-move-resources.md).    