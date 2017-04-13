<properties 
    pageTitle="Geninstaller Linux virtuelle maskiner | Microsoft Azure" 
    description="Beskriver, hvordan du Geninstaller Linux virtuelle maskiner for at reducere SSH forbindelsesproblemer." 
    services="virtual-machines-linux" 
    documentationCenter="virtual-machines" 
    authors="iainfoulds" 
    manager="timlt"
    tags="azure-resource-manager,top-support-issue" 
/>
    

<tags 
    ms.service="virtual-machines-linux" 
    ms.devlang="na" 
    ms.topic="support-article" 
    ms.tgt_pltfrm="vm-linux"
    ms.workload="infrastructure" 
    ms.date="09/19/2016" 
    ms.author="iainfou" 
/>

# <a name="redeploy-virtual-machine-to-new-azure-node"></a>Geninstaller virtuelt til nye Azure node

Hvis du har modstående problemer, der kan hjælpe fejlfinding SSH eller programadgang til en Azure VM (virtual machine), geninstallation VM. Når du Geninstaller en VM, flyttes VM til en ny node i Azure infrastruktur og styrer det tilbage til, bevares alle dine konfigurationsindstillinger og de tilknyttede ressourcer. I denne artikel beskrives, hvordan geninstallere en VM ved hjælp af Azure CLI eller Azure portalen.

> [AZURE.NOTE] Når du Geninstaller en VM, midlertidige disken går tabt, og dynamiske IP-adresser, der er knyttet til virtuelt netværk interface opdateres. 


## <a name="using-azure-cli"></a>Brug af Azure CLI

Sørg for, at du har den [Seneste Azure CLI installeret](../xplat-cli-install.md) på computeren, og du er i ressourcestyring tilstanden (`azure config mode arm`).

Brug følgende kommando for Azure CLI at geninstallere din virtuelle maskine:

```bash
azure vm redeploy --resourcegroup <resourcegroup> --vm-name <vmname> 
```

Når den flyttes gennem genimplementeringen processen, kan du se status for VM ændringen. Den `PowerState` VM går fra 'Kører' til 'Opdatering' derefter 'Start', og til sidst 'kørsel' som den går gennem processen med geninstallation til en ny vært. Kontrollere status for FOS inden for en ressourcegruppe med:

```bash
azure vm list -g <resourcegroup>
```


[AZURE.INCLUDE [virtual-machines-common-redeploy-to-new-node](../../includes/virtual-machines-common-redeploy-to-new-node.md)]


## <a name="next-steps"></a>Næste trin
Hvis du har problemer med at oprette forbindelse til din VM, kan du finde hjælp til [fejlfinding i forbindelse med SSH forbindelser](virtual-machines-linux-troubleshoot-ssh-connection.md) eller [detaljerede SSH fejlfindingstrin](virtual-machines-linux-detailed-troubleshoot-ssh-connection.md). Hvis du ikke kan få adgang til et program, der kører på din VM, kan du også læse [programmet fejlfinding af problemer](virtual-machines-linux-troubleshoot-app-connection.md).