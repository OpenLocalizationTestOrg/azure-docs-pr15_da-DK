<properties
   pageTitle="Tilføje en VNet gateway til et virtuelt netværk for ExpressRoute ved hjælp af Ressourcestyring og PowerShell | Microsoft Azure"
   description="I denne artikel vejleder dig gennem tilføjelse af en Vnet gateway til en allerede oprettet ressourcestyring VNet for ExpressRoute"
   documentationCenter="na"
   services="expressroute"
   authors="charwen"
   manager="carmonm"
   editor=""
   tags="azure-resource-manager"/>

<tags 
   ms.service="expressroute"
   ms.devlang="na"
   ms.topic="article" 
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services" 
   ms.date="10/10/2016"
   ms.author="charwen"/>

# <a name="configure-a-virtual-network-gateway-for-expressroute-using-resource-manager-and-powershell"></a>Konfigurere et virtuelt netværksgateway til ExpressRoute ved hjælp af Ressourcestyring og PowerShell


> [AZURE.SELECTOR]
- [PowerShell - ressourcestyring](expressroute-howto-add-gateway-resource-manager.md)
- [PowerShell - klassisk](expressroute-howto-add-gateway-classic.md)


I denne artikel fører dig gennem trinnene for at tilføje, ændre størrelsen på og fjerne en virtuelt netværk (VNet) gateway til en eksisterende VNet. Trinnene bruges til denne konfiguration er specifikt for VNets, der er oprettet ved hjælp af **ressourcestyring implementeringsmodel** og, der kan i en ExpressRoute konfiguration. 

**Om Azure-installation modeller**

[AZURE.INCLUDE [vpn-gateway-clasic-rm](../../includes/vpn-gateway-classic-rm-include.md)] 

## <a name="before-beginning"></a>Før du begynder

Bekræft, at du har installeret de Azure PowerShell-cmdletter, der er behov for denne konfiguration (1.0.2 eller nyere). Hvis du ikke har installeret cmdletter, skal du gøre det før du begynder konfigurationstrinnene. Du kan finde flere oplysninger om installation af Azure PowerShell, se, [hvordan du installerer og konfigurerer Azure PowerShell](../powershell-install-configure.md).


[AZURE.INCLUDE [expressroute-gateway-rm-ps](../../includes/expressroute-gateway-rm-ps-include.md)]

    
## <a name="next-steps"></a>Næste trin

Når du har oprettet gatewayen VNet, kan du sammenkæde dine VNet til en ExpressRoute kredsløb. Se [linket et virtuelt netværk til en ExpressRoute kredsløb](expressroute-howto-linkvnet-arm.md).
