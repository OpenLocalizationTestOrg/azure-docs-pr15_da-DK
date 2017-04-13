<properties
    pageTitle="Fejlfinding i forbindelse med Windows VM allokeringsfejl | Microsoft Azure"
    description="Fejlfinding i forbindelse med allokeringsfejl, når du opretter, genstart eller ændre størrelsen på en Windows-VM i Azure"
    services="virtual-machines-windows, azure-resource-manager"
    documentationCenter=""
    authors="JiangChen79"
    manager="felixwu"
    editor=""
    tags="top-support-issue,azure-resource-manager,azure-service-management"/>

<tags
    ms.service="virtual-machines-windows"
    ms.workload="na"
    ms.tgt_pltfrm="vm-windows"
    ms.devlang="na"
    ms.topic="article"
    ms.date="02/02/2016"
    ms.author="cjiang"/>

# <a name="troubleshoot-allocation-failures-when-you-create-restart-or-resize-windows-vms-in-azure"></a>Fejlfinding i forbindelse med allokeringsfejl, når du opretter, genstart eller ændre størrelsen på Windows FOS i Azure

Når du opretter en VM, genstart holdt op med at (deallokeres) FOS eller ændre størrelsen på en VM, tildeler Microsoft Azure Beregn ressourcer til dit abonnement. Vises der muligvis nogle gange fejl, når du udfører disse handlinger – endda, før du når Azure abonnement grænseværdier. I denne artikel forklares det, årsagerne til nogle af de almindelige allokeringsfejl og foreslår mulige afhjælpning. Oplysningerne kan også være nyttig, når du planlægger installationen for dine tjenester. Du kan også [foretage fejlfinding af allokeringsfejl, når du opretter, genstart, eller ændre størrelsen på Linux FOS i Azure](virtual-machines-linux-allocation-failure.md).

[AZURE.INCLUDE [virtual-machines-common-allocation-failure](../../includes/virtual-machines-common-allocation-failure.md)]
