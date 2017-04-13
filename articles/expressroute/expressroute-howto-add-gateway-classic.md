<properties
   pageTitle="Konfigurere en VNet gateway til ExpressRoute ved hjælp af PowerShell | Microsoft Azure"
   description="Konfigurere en VNet gateway til en klassisk installation model VNet ved hjælp af PowerShell til en ExpressRoute konfiguration."
   documentationCenter="na"
   services="expressroute"
   authors="charwen"
   manager="carmonm"
   editor=""
   tags="azure-service-management"/>
<tags
   ms.service="expressroute"
   ms.devlang="na"
   ms.topic="article" 
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="10/03/2016"
   ms.author="charwen"/>

# <a name="configure-a-virtual-network-gateway-for-expressroute-using-the-classic-deployment-model-and-powershell"></a>Konfigurere et virtuelt netværksgateway til ExpressRoute ved hjælp af klassisk implementeringsmodel og PowerShell

> [AZURE.SELECTOR]
- [PowerShell - ressourcestyring](expressroute-howto-add-gateway-resource-manager.md)
- [PowerShell - klassisk](expressroute-howto-add-gateway-classic.md)

I denne artikel fører dig gennem trinnene for at tilføje, ændre størrelsen på og fjerne en virtuelt netværk (VNet) gateway til en eksisterende VNet. Trinnene bruges til denne konfiguration er specifikt for VNets, der er oprettet ved hjælp af **Klassisk implementeringsmodel** og, der kan i en ExpressRoute konfiguration. 

**Om Azure-installation modeller**

[AZURE.INCLUDE [vpn-gateway-clasic-rm](../../includes/vpn-gateway-classic-rm-include.md)] 

## <a name="before-beginning"></a>Før du begynder

Bekræft, at du har installeret de Azure PowerShell-cmdletter, der er behov for denne konfiguration (1.0.2 eller nyere). Hvis du ikke har installeret cmdletter, skal du gøre det før du begynder konfigurationstrinnene. Du kan finde flere oplysninger om installation af Azure PowerShell, se, [hvordan du installerer og konfigurerer Azure PowerShell](../powershell-install-configure.md).


[AZURE.INCLUDE [expressroute-gateway-classic-ps](../../includes/expressroute-gateway-classic-ps-include.md)]

    
## <a name="next-steps"></a>Næste trin

Når du har oprettet gatewayen VNet, kan du sammenkæde dine VNet til en ExpressRoute kredsløb. Se [linket et virtuelt netværk til en ExpressRoute kredsløb](expressroute-howto-linkvnet-classic.md).
