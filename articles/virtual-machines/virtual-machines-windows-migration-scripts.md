<properties
    pageTitle="Communityværktøjer til Azure Service Management til Azure ressourcestyring overførsel"
    description="I denne artikel katalogiserer de værktøjer, der har fået af community'et hjælper med overførsel af IaaS ressourcer fra Azure Service Management til Azure ressourcestyring stablen."
    services="virtual-machines-windows"
    documentationCenter=""
    authors="singhkays"
    manager="timlt"
    editor=""
    tags="azure-resource-manager"/>

<tags
    ms.service="virtual-machines-windows"
    ms.workload="infrastructure-services"
    ms.tgt_pltfrm="vm-windows"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/29/2016"
    ms.author="singhkay"/>

# <a name="community-tools-for-azure-service-management-to-azure-resource-manager-migration"></a>Communityværktøjer til Azure Service Management til Azure ressourcestyring overførsel

I denne artikel katalogiserer de værktøjer, der har fået af community'et hjælper med overførsel af IaaS ressourcer fra Azure Service Management til Azure ressourcestyring stablen.

>[AZURE.NOTE]Disse funktioner understøttes ikke af Microsoft Support officielt. Derfor de er åbne leveres på Github, og vi glæder os til at acceptere PRs løsninger eller ekstra scenarier. Hvis du vil rapportere et problem, ved at bruge funktionen Github problemer.
>
> Overføre med disse værktøjer medføre nedetid for din klassisk virtuelle maskine. Hvis du vil have platform understøttes migrering, kan du besøge 
>
>- [Platform understøttes overførsel af IaaS ressourcer fra klassisk til Azure ressourcestyring stak](./virtual-machines-windows-migration-classic-resource-manager.md)
>- [Tekniske undersøgelse på Platform understøttes overførsel fra klassisk til Azure ressourcestyring](./virtual-machines-windows-migration-classic-resource-manager-deep-dive.md)
>- [Overføre IaaS ressourcer fra klassisk til Azure ressourcestyring ved hjælp af Azure PowerShell](./virtual-machines-windows-ps-migration-classic-resource-manager.md)

## <a name="asm2arm"></a>ASM2ARM

Dette er et PowerShell-script modul for at overføre din **enkelt** Virtual Machine (VM) fra Azure Service Management stak til Azure ressourcestyring stak. 

[Oprette et link til værktøjet dokumentation](https://github.com/Azure/classic-iaas-resourcemanager-migration/tree/master/asm2arm)

## <a name="migaz"></a>migAz

migAz er en ekstra indstilling til at overføre et komplet sæt Azure Service Management IaaS ressourcer til Azure ressourcestyring IaaS ressourcer. Overflytningen kan opstå i det samme abonnement eller mellem forskellige abonnementer og abonnementstyper (ex: CSP abonnementer).

[Oprette et link til værktøjet dokumentation](https://github.com/Azure/classic-iaas-resourcemanager-migration/tree/master/migaz)