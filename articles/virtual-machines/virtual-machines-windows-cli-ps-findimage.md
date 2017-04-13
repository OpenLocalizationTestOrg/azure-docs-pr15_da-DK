<properties
   pageTitle="Navigere og vælge Windows VM billeder | Microsoft Azure"
   description="Lær at fastslå publisher, tilbud og SKU til billeder, når du opretter en virtuel maskine til Windows med implementeringsmodel ressourcestyring."
   services="virtual-machines-windows"
   documentationCenter=""
   authors="squillace"
   manager="timlt"
   editor=""
   tags="azure-resource-manager"
   />

<tags
   ms.service="virtual-machines-windows"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="vm-windows"
   ms.workload="infrastructure"
   ms.date="08/23/2016"
   ms.author="rasquill"/>

# <a name="navigate-and-select-windows-virtual-machine-images-in-azure-with-powershell-or-the-cli"></a>Navigere og vælge Windows virtuelt billeder i Azure med PowerShell eller CLI

Dette emne beskrives, hvordan du kan finde VM billede udgivere, tilbud, lagerenheder og versioner for hver placering, hvor du kan installere. For at give et eksempel, er nogle ofte anvendte Windows VM billeder:

## <a name="table-of-commonly-used-windows-images"></a>Liste over ofte anvendte Windows billeder


| PublisherName                        | Tilbud                                 | SKU                         |
|:---------------------------------|:-------------------------------------------|:---------------------------------|:--------------------|
| MicrosoftDynamicsNAV             | DynamicsNAV                                | 2015                             |
| MicrosoftSharePoint              | MicrosoftSharePointServer                  | 2013                             |
| MicrosoftSQLServer               | SQL2014 WS2012R2                           | Virksomhed-optimeret-til-DW      |
| MicrosoftSQLServer               | SQL2014 WS2012R2                           | Virksomhed-optimeret-til-OLTP    |
| MicrosoftWindowsServer           | WindowsServer                              | R2-2012-Datacenter                  |
| MicrosoftWindowsServer           | WindowsServer                              | 2012-Datacenter               |
| MicrosoftWindowsServer           | WindowsServer                              | 2008 R2 SP1 |
| MicrosoftWindowsServer           | WindowsServer                              | Windows-Server-tekniske-Preview |
| MicrosoftWindowsServerEssentials | WindowsServerEssentials                    | WindowsServerEssentials          |
| MicrosoftWindowsServerHPCPack    | WindowsServerHPCPack                       | 2012R2                           |


[AZURE.INCLUDE [virtual-machines-common-cli-ps-findimage](../../includes/virtual-machines-common-cli-ps-findimage.md)]
