<properties
   pageTitle="Beregn-, Netværks- og lagerplads udbydere | Microsoft Azure"
   description="Oversigt over Beregn-, Netværks- og lagerplads ressource udbydere (Kapacitetsplanlægning, NRP og vejledende pris) til Linux-programmer i Azure ressourcestyring implementeringsmodel"
   services="virtual-machines-linux"
   documentationCenter=""
   authors="tfitzmac"
   manager="timlt"
   editor="tysonn"
   tags="azure-resource-manager,azure-service-management"/>

<tags
   ms.service="virtual-machines-linux"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="vm-linux"
   ms.workload="infrastructure-services"
   ms.date="08/19/2015"
   ms.author="tomfitz"/>

# <a name="azure-compute-network-and-storage-providers-for-linux-applications-under-azure-resource-manager-deployment-model"></a>Azure Beregn-, Netværks- og lagerplads udbydere af Linux-programmer under Azure ressourcestyring implementeringsmodel

Optagelse af Beregn-, Netværks- og lagerplads funktioner med implementeringsmodel Azure ressourcestyring grundlæggende forenkler installation og administration af komplekse programmer, der kører på IaaS. Mange programmer kræver en kombination af ressourcer, herunder et virtuelt netværk, lagerplads konto, virtuelt og en netværk-grænseflade. Azure ressourcestyring implementeringsmodel giver mulighed for at oprette en JSON-skabelon til at installere og administrere alle disse ressourcer sammen som et enkelt program.

[AZURE.INCLUDE [virtual-machines-common-compare-deployment-models](../../includes/virtual-machines-common-compare-deployment-models.md)]
