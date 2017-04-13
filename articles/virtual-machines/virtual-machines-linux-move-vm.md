<properties
    pageTitle="Flytte en Linux VM | Microsoft Azure"
    description="Flytte en Linux VM til en anden Azure-abonnement eller ressource i implementeringsmodel ressourcestyring."
    services="virtual-machines-linux"
    documentationCenter=""
    authors="cynthn"
    manager="timlt"
    editor=""
    tags="azure-resource-manager"/>

<tags
    ms.service="virtual-machines-linux"
    ms.workload="infrastructure-services"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/08/2016"
    ms.author="cynthn"/>

    


# <a name="move-a-linux-vm-to-another-subscription-or-resource-group"></a>Flytte en Linux VM til et andet abonnement eller ressource gruppe

I denne artikel vejledes du gennem Sådan flyttes en Linux VM mellem grupper eller abonnementer. Flytte en VM mellem abonnementer på kan være praktisk, hvis du har oprettet en VM i en personal-abonnement og nu vil du flytte den til din organisations abonnement.

> [AZURE.NOTE] Ny ressource id'er oprettes som en del af Flyt. Når VM er blevet flyttet, skal du opdatere din værktøjer og scripts kan bruge den nye ressource id'er. 


## <a name="use-the-azure-cli-to-move-a-vm"></a>Brug Azure CLI til at flytte en VM 

Hvis du vil flytte en VM korrekt, skal du flytte VM og alle dens understøttende ressourcer. Bruge kommandoen **Vis azure gruppe** til at få vist alle ressourcerne i en ressourcegruppe og deres id'er. Det hjælper med at pipe output for denne kommando til en fil, så du kan kopiere og indsætte id'erne i nyere kommandoer.

    azure group show <resourceGroupName>

For at flytte en VM og dens ressourcer til en anden ressourcegruppe, skal du bruge kommandoen **azure ressource flytte** CLI. I følgende eksempel viser, hvordan du flytter en VM og de mest almindelige ressourcer, der kræver. Vi bruge parameteren **-i** og overføre i en kommasepareret liste (uden mellemrum) af id'er for ressourcerne til at flytte.

    
    vm=/subscriptions/<sourceSubscriptionID>/resourceGroups/<sourceResourceGroup>/providers/Microsoft.Compute/virtualMachines/<vmName>
    nic=/subscriptions/<sourceSubscriptionID>/resourceGroups/<sourceResourceGroup>/providers/Microsoft.Network/networkInterfaces/<nicName>
    nsg=/subscriptions/<sourceSubscriptionID>/resourceGroups/<sourceResourceGroup>/providers/Microsoft.Network/networkSecurityGroups/<nsgName>
    pip=/subscriptions/<sourceSubscriptionID>/resourceGroups/<sourceResourceGroup>/providers/Microsoft.Network/publicIPAddresses/<publicIPName>
    vnet=/subscriptions/<sourceSubscriptionID>/resourceGroups/<sourceResourceGroup>/providers/Microsoft.Network/virtualNetworks/<vnetName>
    diag=/subscriptions/<sourceSubscriptionID>/resourceGroups/<sourceResourceGroup>/providers/Microsoft.Storage/storageAccounts/<diagnosticStorageAccountName>
    storage=/subscriptions/<sourceSubscriptionID>/resourceGroups/<sourceResourceGroup>/providers/Microsoft.Storage/storageAccounts/<storageAcountName>      
    
    azure resource move --ids $vm,$nic,$nsg,$pip,$vnet,$storage,$diag -d "<destinationResourceGroup>"
    
Hvis du vil flytte VM og dens ressourcer til et andet abonnement, kan du tilføje den **– destination subscriptionId & #60; destinationSubscriptionID & #62;** parameter for at angive abonnementets destination.

Hvis du arbejder fra kommandoprompten på en Windows-computer, du vil tilføje en **$** foran variabelnavne, når du erklærer dem. Dette er ikke nødvendigt i Linux.

Du bliver bedt om at bekræfte, at du vil flytte den angivne ressource. Skriv **Y** for at bekræfte, at du vil flytte ressourcerne.
    

[AZURE.INCLUDE [virtual-machines-common-move-vm](../../includes/virtual-machines-common-move-vm.md)]

## <a name="next-steps"></a>Næste trin

Du kan flytte mange forskellige typer ressourcer mellem grupper og abonnementer. Du kan finde flere oplysninger, kan du se [flytte ressourcer til ny ressourcegruppe eller et andet abonnement](../resource-group-move-resources.md).    