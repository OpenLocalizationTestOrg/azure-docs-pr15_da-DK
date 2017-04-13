<properties
   pageTitle="Sådan mærker du et virtuelt Linux | Microsoft Azure"
   description="Få mere at vide om mærkning af en Linux virtuel maskine, der er oprettet i Azure ved hjælp af Ressourcestyring implementeringsmodel."
   services="virtual-machines-linux"
   documentationCenter=""
   authors="mmccrory"
   manager="timlt"
   editor="tysonn"
   tags="azure-resource-manager"/>

<tags
   ms.service="virtual-machines-linux"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="vm-linux"
   ms.workload="infrastructure-services"
   ms.date="07/05/2016"
   ms.author="memccror"/>

# <a name="how-to-tag-a-linux-virtual-machine-in-azure"></a>Sådan mærke en Linux virtuel maskine i Azure

I denne artikel beskrives de forskellige måder at mærke en Linux virtuel maskine i Azure gennem implementeringsmodel ressourcestyring. Mærker er brugerdefinerede nøgle/værdi-par, som kan placeres direkte i en ressource eller en ressourcegruppe. Azure understøtter i øjeblikket op til 15 mærker hver ressource og ressourcegruppe. Mærker kan være placeret på en ressource på tidspunktet for oprettelse eller føjet til en eksisterende ressource. Vær opmærksom på, mærker understøttes for de ressourcer, der er oprettet, via ressourcestyring implementeringsmodel kun.

[AZURE.INCLUDE [virtual-machines-common-tag](../../includes/virtual-machines-common-tag.md)]

## <a name="tagging-with-azure-cli"></a>Mærkning med Azure CLI

At starte, [installere og konfigurere Azure CLI](../xplat-cli-azure-resource-manager.md) , og Sørg for, at du er i ressourcestyring tilstand (`azure config mode arm`).

Du kan få vist alle egenskaber for en given virtuel maskine, herunder mærkerne, du bruger denne kommando:

        azure vm show -g MyResourceGroup -n MyTestVM

Hvis du vil tilføje et nyt VM mærke via Azure CLI, kan du bruge den `azure vm set` kommando sammen med mærke parameter **-t**:

        azure vm set -g MyResourceGroup -n MyTestVM –t myNewTagName1=myNewTagValue1;myNewTagName2=myNewTagValue2

Sådan fjerner du alle mærker, kan du bruge parameteren **– T** i den `azure vm set` kommandoen.

        azure vm set – g MyResourceGroup –n MyTestVM -T


Nu hvor vi har anvendt mærker til vores ressourcer Azure CLI og portalen, Lad os se nærmere på brugen detaljerne for at se mærkerne i portalen fakturering.

[AZURE.INCLUDE [virtual-machines-common-tag-usage](../../includes/virtual-machines-common-tag-usage.md)]

## <a name="next-steps"></a>Næste trin

* Hvis du vil vide mere om mærkning af ressourcerne Azure, se [Azure ressourcestyring oversigt][] og [Bruge mærker til at organisere dine Azure ressourcer][].
* For at se, hvordan mærker kan hjælpe dig med at administrere din brug af Azure ressourcer skal du se [forstå din faktura Azure][] og [få indsigt i dine Microsoft Azure Ressourceforbrug][].





[Azure CLI environment]: ./xplat-cli-azure-resource-manager.md
[Oversigt over Azure ressourcestyring]: ../azure-resource-manager/resource-group-overview.md
[Bruge mærker til at organisere dine Azure-ressourcer]: ../resource-group-using-tags.md
[Forstå din Azure faktura]: ../billing/billing-understand-your-bill.md
[Få indsigt i dine Microsoft Azure Ressourceforbrug]: ../billing-usage-rate-card-overview.md
