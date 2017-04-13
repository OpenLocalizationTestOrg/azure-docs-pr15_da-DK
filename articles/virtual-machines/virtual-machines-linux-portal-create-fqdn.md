<properties
   pageTitle="Oprette fulde Domænenavn for en VM Azure-portalen | Microsoft Azure"
   description="Lær at oprette et fuldt domænenavn eller fulde Domænenavn for en ressourceleder baseret virtuel maskine i portalen Azure."
   services="virtual-machines-linux"
   documentationCenter=""
   authors="iainfoulds"
   manager="timlt"
   editor="tysonn"
   tags="azure-resource-manager"/>

<tags
   ms.service="virtual-machines-linux"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="vm-linux"
   ms.workload="infrastructure-services"
   ms.date="08/23/2016"
   ms.author="iainfou"/>

# <a name="create-a-fully-qualified-domain-name-in-the-azure-portal"></a>Oprette et fuldt domænenavn i portalen Azure
Når du opretter en virtuel maskine (VM) i [Azure-portalen](https://portal.azure.com) ved hjælp af Ressourcestyring implementeringsmodel, oprettes der automatisk en offentlige IP-ressource til den virtuelle maskine. Du kan bruge denne IP-adresse at få fjernadgang til VM. Selvom portalen ikke opretter et [fuldt domænenavn](https://en.wikipedia.org/wiki/Fully_qualified_domain_name)eller fulde Domænenavn, som standard, kan du tilføje et, når VM er oprettet. I denne artikel beskrives trinnene, hvis du vil oprette en DNS-navn eller det fulde Domænenavn.

[AZURE.INCLUDE [virtual-machines-common-portal-create-fqdn](../../includes/virtual-machines-common-portal-create-fqdn.md)]

Du kan nu oprette forbindelse fra en fjernplacering til VM ved hjælp af DNS-navnet som med `ssh adminuser@testdnslabel.centralus.cloudapp.azure.com`.

## <a name="next-steps"></a>Næste trin
Nu hvor din VM har et offentligt IP- og DNS-navn, du kan installere almindelige programmet strukturer eller tjenester som nginx, MongoDB, Docker, osv.

Du kan også læse mere om at [bruge ressourcestyring](../azure-resource-manager/resource-group-overview.md) tip til opbygning af din Azure installationer.