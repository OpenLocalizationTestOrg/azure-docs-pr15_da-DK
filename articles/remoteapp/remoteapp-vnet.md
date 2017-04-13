
<properties
    pageTitle="Validere Azure VNET til brug med Azure RemoteApp | Microsoft Azure"
    description="Lær at sikre, at din Azure VNET er klar til brug med Azure RemoteApp"
    services="remoteapp"
    documentationCenter=""
    authors="lizap"
    manager="mbaldwin" />

<tags
    ms.service="remoteapp"
    ms.workload="compute"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/15/2016"
    ms.author="elizapo" />



# <a name="validate-the-azure-vnet-to-use-with-azure-remoteapp"></a>Validere Azure VNET til brug med Azure RemoteApp

> [AZURE.IMPORTANT]
> Azure RemoteApp er ved at blive nedlagt. Læs [meddelelsen](https://go.microsoft.com/fwlink/?linkid=821148) for at få mere at vide.

Før du bruger en Azure VNET med Azure RemoteApp, kan du vil validere VNET. Det hjælper med at undgå problemer med forbindelse.

Hvis du vil validere dine Azure VNET, skal du gøre følgende:

1. Oprette en Azure virtuelt i undernet af den Azure VNET, du vil bruge med Azure RemoteApp.

2. Opret forbindelse til VM ved hjælp af indstillingen **Opret forbindelse** i administrationsportalen.
3. Deltage i den virtuelle maskine til det samme domæne, du vil bruge med Azure RemoteApp. Hvis du opretter en hybrid af websteder, der opretter forbindelse til dit lokale netværk, kan du deltage i den virtuelle maskine til dit lokale domæne.

Hvis dette lykkes, er Azure VNET klar til brug med RemoteApp.

Se følgende artikler kan finde flere oplysninger om arbejdsprocessen til slut hybrid af websteder:

- [Hvordan du planlægger dit virtuelle netværk til Azure RemoteApp](remoteapp-planvnet.md)
- [Oprette en hybrid af websteder](remoteapp-create-hybrid-deployment.md)
- [Installer Azure RemoteApp af websteder på netværket Azure virtuelle (med understøttelse af ExpressRoute)](http://blogs.msdn.com/b/rds/archive/2015/04/23/deploy-azure-remoteapp-collection-to-your-azure-virtual-network-with-support-for-expressroute.aspx)
