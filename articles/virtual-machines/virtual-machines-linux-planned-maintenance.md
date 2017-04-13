<properties
    pageTitle="Planlagt vedligeholdelse for Linux FOS | Microsoft Azure"
    description="Forstå, hvilke Azure planlagt vedligeholdelse er, og hvordan det påvirker din Linux virtuelle maskiner, der kører i Azure"
    services="virtual-machines-linux"
    documentationCenter=""
    authors="drewm"
    manager="timlt"
    editor=""
    tags="azure-service-management,azure-resource-manager"/>

<tags
    ms.service="virtual-machines-linux"
    ms.workload="infrastructure-services"
    ms.tgt_pltfrm="vm-linux"
    ms.devlang="na"
    ms.topic="article"
    ms.date="04/26/2016"
    ms.author="drewm"/>

# <a name="planned-maintenance-for-linux-virtual-machines-in-azure"></a>Planlagt vedligeholdelse for Linux virtuelle maskiner i Azure

Forstå, hvilke Azure planlagt vedligeholdelse er, og hvordan det kan påvirke tilgængeligheden af din Linux virtuelle computere. I denne artikel findes også for [Windows virtuelle computere](virtual-machines-windows-planned-maintenance.md). 

Denne artikel indeholder en baggrund som Azure planlagt vedligeholdelse processen. Hvis du ønsker at foretage fejlfinding af, hvorfor din VM genstartet, kan du [læse denne blog indlæg, der beskriver visning VM genstart logfiler](https://azure.microsoft.com/blog/viewing-vm-reboot-logs/).

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-both-include.md)]

## <a name="why-azure-performs-planned-maintenance"></a>Hvorfor Azure udfører planlagt vedligeholdelse

Microsoft Azure udfører med jævne mellemrum opdateringer over hele verden til at forbedre den pålidelighed, ydeevne og sikkerheden for den underliggende virtuelle maskiner host infrastruktur. Mange af disse opdateringer er udført uden nogen betydning for din virtuelle maskiner eller Cloud Services, herunder hukommelse bevares opdateringer.

Dog kræver nogle opdateringer en genstart din virtuelle maskiner til at anvende de nødvendige opdateringer til infrastruktur. Virtuelle maskiner er lukket, mens vi programrettelse infrastrukturen, og klik derefter på virtuelle maskiner genstartes.

Bemærk, at der er to typer af vedligeholdelse, der kan påvirke tilgængeligheden af virtuelle maskiner: planlagte og ikke-planlagt. Denne side beskrives, hvordan Microsoft Azure udfører planlagt vedligeholdelse. Se [forstå planlagt eller ikke-planlagt vedligeholdelse](virtual-machines-linux-manage-availability.md)kan finde flere oplysninger om ikke-planlagt vedligeholdelse.

[AZURE.INCLUDE [virtual-machines-common-planned-maintenance](../../includes/virtual-machines-common-planned-maintenance.md)]
