<properties
    pageTitle="Forskellige måder at oprette en Windows-VM | Microsoft Azure"
    description="Oversigt over de forskellige måder at oprette en Windows virtuel maskine med ressourcestyring."
    services="virtual-machines-windows"
    documentationCenter=""
    authors="cynthn"
    manager="timlt"
    editor=""
    tags="azure-resource-manager"/>

<tags
    ms.service="virtual-machines-windows"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="vm-windows"
    ms.workload="infrastructure-services"
    ms.date="09/27/2016"
    ms.author="cynthn"/>

# <a name="different-ways-to-create-a-windows-virtual-machine-with-resource-manager"></a>Forskellige måder at oprette en Windows virtuel maskine med ressourcestyring

Azure indeholder forskellige måder at oprette en virtuel maskine, fordi virtuelle maskiner egner sig til forskellige brugere og formål. Det betyder, at du skal foretage nogle valg for den virtuelle maskine, og hvordan du opretter den. I denne artikel får du en oversigt over disse valgmuligheder og links til instruktioner.

## <a name="azure-portal"></a>Azure-portalen

Ved hjælp af portalen Azure er en nem måde at afprøve en virtuel maskine, især hvis du lige er begyndt med Azure. 

[Oprette en virtuel maskine, der kører Windows ved hjælp af portalen](virtual-machines-windows-hero-tutorial.md)

## <a name="template"></a>Skabelon

Virtuelle maskiner kræver en kombination af ressourcer (som en tilgængelighed sæt og lagerplads konti). I stedet for implementering og administration af hver ressource separat, kan du oprette en skabelon til Azure Ressourcestyring, installeres og bestemmelser alle ressourcerne i en enkelt, koordineret handling.

- [Oprette en Windows virtuel maskine med en skabelon for ressourcestyring](virtual-machines-windows-ps-template.md)


## <a name="azure-powershell"></a>Azure PowerShell

Hvis du foretrækker at arbejde i en kommando-shell, kan du bruge Azure PowerShell.

- [Oprette en Windows-VM ved hjælp af PowerShell](virtual-machines-windows-ps-create.md)


## <a name="visual-studio"></a>Visual Studio

Brug Visual Studio til at oprette, administrere og installere FOS med værktøjerne Azure til Visual Studio og Azure SDK.

[Azure-værktøjer til Visual Studio](https://www.visualstudio.com/features/azure-tools-vs)

