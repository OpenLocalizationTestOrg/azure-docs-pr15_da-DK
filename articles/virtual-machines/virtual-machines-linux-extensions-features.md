<properties
 pageTitle="Virtuelt extensions og funktioner | Microsoft Azure"
 description="Få mere at vide, hvilke udvidelser der er tilgængelige til Azure virtuelle maskiner, grupperet efter hvad de giver eller forbedre."
 services="virtual-machines-linux"
 documentationCenter=""
 authors="neilpeterson"
 manager="timlt"
 editor=""
 tags="azure-service-management,azure-resource-manager"/>

<tags
 ms.service="virtual-machines-linux"
 ms.devlang="na"
 ms.topic="article"
 ms.tgt_pltfrm="vm-linux"
 ms.workload="infrastructure-services"
 ms.date="09/22/2016"
 ms.author="nepeters"/>

# <a name="about-virtual-machine-extensions-and-features"></a>Om virtuelt filtypenavne og funktioner

## <a name="azure-vm-extensions"></a>Azure VM filtypenavne

Azure virtuelt udvidelser er små programmer, som giver indlæg installation konfiguration og automatisering opgave på virtuelle Azure-computere. Hvis en virtuel maskine kræver software er installeret, anti-virus beskyttelse eller Docker konfiguration, kan der for eksempel bruges filtypenavnet VM til at udføre disse opgaver. Azure VM filtypenavne kan køres ved hjælp af Azure CLI, PowerShell, ressource Administrer skabeloner og Azure portalen. Filtypenavne kan sammen med en ny virtuelt installation eller kører i forhold til et eksisterende system.

Dette dokument indeholder forudsætninger for Azure virtuelt lokalnummer og vejledning til, hvordan du registrerer tilgængelige VM udvidelser. 

## <a name="azure-vm-agent"></a>Azure VM Agent

Azure VM Agent administrerer interaktion mellem en Azure Virtual Machine og Azure-strukturen Controller. VM agent er ansvarlig for mange funktionelle aspekter ved implementering og administration af Azure virtuelle maskiner, herunder kører VM Extensions. Azure VM Agent er forudinstalleret på Azure galleriet billeder og kan installeres på understøttede operativsystemer. 

Du kan finde oplysninger om understøttede operativsystemer og installationsvejledning [Brugervejledning til Azure Linux Agent](./virtual-machines-linux-agent-user-guide.md).

## <a name="discover-vm-extensions"></a>Opdag VM filtypenavne

Mange forskellige VM udvidelser er tilgængelige til brug med virtuelle Azure-computere. For at se en komplet liste skal du køre følgende kommando med Azure CLI, erstatter placeringen med placeringen af valg.

```none
azure vm extension-image list westus
```

<br />

## <a name="common-vm-extensions"></a>Almindelige VM filtypenavne

|Filtypenavn   |Beskrivelse   |Få mere at vide   |
|---|---|---|
|Brugerdefineret Script-udvidelse til Linux  | Køre scripts mod en Azure Virtual Machine  |[Brugerdefineret Script-udvidelse til Linux](./virtual-machines-linux-extensions-customscript.md)   |
|Docker lokalnummer |Installerer Docker daemonen at understøtte remote Docker kommandoer.  | [Docker VM lokalnummer](./virtual-machines-linux-dockerextension.md)  |
|VM Access lokalnummer | Få adgang til Azure Virtual Machine  |[VM Access lokalnummer](https://github.com/Azure/azure-linux-extensions/tree/master/VMAccess) |
|Azure diagnosticering lokalnummer |Administrere Azure diagnosticering |[Azure diagnosticering lokalnummer](https://azure.microsoft.com/blog/windows-azure-virtual-machine-monitoring-with-wad-extension/) |

