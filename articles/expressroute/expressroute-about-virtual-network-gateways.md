<properties 
   pageTitle="Om ExpressRoute virtuelt netværk gateways | Microsoft Azure"
   description="Få mere at vide om virtuelt netværk gateways for ExpressRoute."
   services="expressroute"
   documentationCenter="na"
   authors="cherylmc"
   manager="carmonm"
   editor=""
   tags="azure-resource-manager, azure-service-management"/>
<tags 
   ms.service="expressroute"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="10/03/2016"
   ms.author="cherylmc" />

# <a name="about-virtual-network-gateways-for-expressroute"></a>Om virtuelt netværk gateways for ExpressRoute


Et virtuelt netværksgateway bruges til at sende netværkstrafik mellem Azure virtuelle netværk og lokale placeringer. Når du konfigurerer en ExpressRoute forbindelse, skal du oprette og konfigurere et virtuelt netværksgateway og en virtuel gateway netværksforbindelse.

Når du opretter en virtuel netværksgateway, kan du angive flere indstillinger. En af de nødvendige indstillinger angiver, om gatewayen skal bruges til ExpressRoute eller websted til websted VPN-trafik. I implementeringsmodel ressourcestyring indstillingen er '-GatewayType'.

Når netværkstrafik sendes på en dedikeret privat forbindelse, kan du bruge typen gateway 'ExpressRoute'. Dette kaldes også som en ExpressRoute gateway. Når netværkstrafik sendes krypterede på offentlige internettet, kan du bruge typen gateway 'Vpn'. Dette kaldes en VPN-gateway. Websted til websted, punkt-til-websted og VNet-VNet forbindelser alle bruger en VPN-gateway. 

Hver virtuelt netværk kan have kun én virtuelt netværksgateway per gateway type. Du kan for eksempel har et virtuelt netværksgateway, der anvender - GatewayType Vpn og en, der bruger - GatewayType ExpressRoute. I denne artikel fokuserer på gatewayen ExpressRoute virtuelt netværk.

## <a name="gwsku"></a>Gatewayen lagerenheder

[AZURE.INCLUDE [expressroute-gwsku-include](../../includes/expressroute-gwsku-include.md)]

Hvis du vil opgradere din gateway til en mere effektiv gateway SKU, kan du bruge Skift størrelse-AzureRmVirtualNetworkGateway PowerShell-cmdlet i de fleste tilfælde. Dette fungerer for opgraderinger til Standard- og ved anvendelse lagerenheder. Men hvis du vil opgradere til den UltraPerformance SKU, skal du at genskabe gatewayen.

###  <a name="aggthroughput"></a>Anslået Sammenlæg overførselshastighed ved gateway SKU


Den følgende tabel viser typerne gateway og anslåede Sammenlæg overførselshastigheden. I denne tabel gælder for både ressourcestyring og klassisk installation modeller.

[AZURE.INCLUDE [expressroute-table-aggthroughput](../../includes/expressroute-table-aggtput-include.md)] 


## <a name="resources"></a>REST API'er og PowerShell-cmdletter

Flere tekniske ressourcer og bestemte syntakskrav, når du bruger REST API'er og PowerShell-cmdlet'er til virtuelt netværk gateway konfigurationer, finde de følgende sider:

|**Klassisk** | **Ressourcestyring**|
|-----|----|
|[PowerShell](https://msdn.microsoft.com/library/mt270335.aspx)|[PowerShell](https://msdn.microsoft.com/library/mt163510.aspx)|
|[REST-API](https://msdn.microsoft.com/library/jj154113.aspx)|[REST-API](https://msdn.microsoft.com/library/mt163859.aspx)|


## <a name="next-steps"></a>Næste trin

Se [Oversigt over ExpressRoute](expressroute-introduction.md) kan finde flere oplysninger om konfiguration af tilgængelige forbindelse. 







 
