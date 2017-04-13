<properties
    pageTitle="Vedhæfte en datadisk til en Linux VM | Microsoft Azure"
    description="Sådan vedhæftes en Linux VM i portalen Azure ved hjælp af Ressourcestyring implementeringsmodel nye eller eksisterende datadisk."
    services="virtual-machines-linux"
    documentationCenter=""
    authors="cynthn"
    manager="timlt"
    editor=""
    tags="azure-resource-manager"/>

<tags
    ms.service="virtual-machines-linux"
    ms.workload="infrastructure-services"
    ms.tgt_pltfrm="vm-linux"
    ms.devlang="na"
    ms.topic="article"
    ms.date="07/06/2016"
    ms.author="cynthn"/>

# <a name="how-to-attach-a-data-disk-to-a-linux-vm-in-the-azure-portal"></a>Hvordan du vedhæfter en datadisk til en Linux VM i portalen Azure

I denne artikel beskrives, hvordan du vedhæfter både nye og eksisterende til en Linux virtuel maskine via Azure-portalen. Du kan også [vedhæfte en datadisk til en Windows-VM i portalen Azure](virtual-machines-windows-attach-disk-portal.md). Før du gør dette, kan du gennemse disse tip:

- Størrelsen af den virtuelle maskine styrer, hvor mange datadisce du kan vedhæfte. Yderligere oplysninger finder du [størrelser til virtuelle computere](virtual-machines-linux-sizes.md).
- For at kunne bruge Premium lagerplads, skal du bruge en DS-serier eller GS serie virtuel maskine. Du kan bruge diske fra både Premium og Standard lagerplads konti med disse virtuelle maskiner. Premium storage findes i visse områder. Yderligere oplysninger finder du [Premium lagerplads: High-Performance lagerplads til Azure virtuelt arbejdsbelastninger](../storage/storage-premium-storage.md).
- Diske, der er knyttet til virtuelle maskiner er faktisk .vhd filer på en Azure-lager-konto. Se Få mere at vide [om og virtuelle harddiske til virtuelle computere](virtual-machines-linux-about-disks-vhds.md).
- Til en ny disk, kan du ikke behøver at oprette den første, fordi Azure opretter den, når du installerer den.
- For en eksisterende disk være .vhd-filen tilgængelig i en Azure-lager-konto. Du kan bruge en .vhd, der allerede er der, hvis det ikke er knyttet til en anden virtuel computer, eller Overfør dine egne .vhd fil til kontoen lagerplads.


[AZURE.INCLUDE [virtual-machines-common-attach-disk-portal](../../includes/virtual-machines-common-attach-disk-portal.md)]

## <a name="next-steps"></a>Næste trin

Når disken er tilføjet, skal du gøre den klar til brug. Se i den virtuelle maskine operativsystem: "Sådan: Initialisering af en ny datadisk i Linux" i denne [artikel](virtual-machines-linux-classic-attach-disk.md#how-to-initialize-a-new-data-disk-in-linux).
