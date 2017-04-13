<properties
    pageTitle="Hvad er VM skala angiver? | Microsoft Azure"
    description="Få mere at vide om VM skala sæt."
    keywords="Linux virtuelt virtuelt skala angiver" 
    services="virtual-machines-linux"
    documentationCenter=""
    authors="gatneil"
    manager="madhana"
    editor="tysonn"
    tags="azure-resource-manager" />

<tags
    ms.service="virtual-machine-linux"
    ms.workload="infrastructure-services"
    ms.tgt_pltfrm="vm-linux"
    ms.devlang="na"
    ms.topic="article"
    ms.date="03/24/2016"
    ms.author="gatneil"/>

# <a name="what-are-virtual-machine-scale-sets"></a>Hvad er virtuelt skala angiver?

Virtuelt skala sæt gør det muligt at administrere flere FOS som et sæt. På et højt niveau har skala sæt følgende fordele og ulemper:

Medarbejdere:

1. Høj tilgængelighed. Hvert skala sæt placerer dens FOS i en tilgængelighed, der er konfigureret med 5 fejl domæner (FDs) og 5 opdatering domæner (UDs) for at sikre tilgængelighed (kan finde flere oplysninger om FDs og UDs, se [VM tilgængelighed](./virtual-machines-linux-manage-availability.md)). 
2. Nem integration med Azure justering af belastning og App gatewayen.
3. Nem integration med Azure Autoskalering.
4. Forenklet implementering, administration og rydde op i FOS.
5. Understøtte almindelige Windows og Linux typer samt brugerdefinerede billeder.

Ulemper:

1. Kan ikke knytte datadisce til VM forekomster i et sæt skala. I stedet skal bruge Blob-lager, Azure filer, Azure tabeller eller andre lagerplads løsning.

## <a name="quick-create-using-azure-cli"></a>Hurtig-Opret ved hjælp af Azure CLI

[AZURE.INCLUDE [cli-vmss-quick-create](../../includes/virtual-machines-linux-cli-vmss-quick-create-include.md)]

## <a name="next-steps"></a>Næste trin

Generelle oplysninger, skal du se den [primære landingssiden for skala sæt](https://azure.microsoft.com/services/virtual-machine-scale-sets/).

Se den [primære dokumentation siden for skala angiver](../virtual-machine-scale-sets/virtual-machine-scale-sets-overview.md)flere dokumentation.

For eksempel søge ressourcestyring skabeloner ved hjælp af skala sæt, efter "vmss" i [Azure Hurtig start skabeloner github repo](https://github.com/Azure/azure-quickstart-templates).

