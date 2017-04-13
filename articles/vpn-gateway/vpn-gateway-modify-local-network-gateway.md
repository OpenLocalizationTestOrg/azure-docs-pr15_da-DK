<properties
   pageTitle="Ændre lokale netværk gateway IP-adressepræfikser og IP-gateway | Microsoft Azure"
   description="I denne artikel vejledes du gennem ændre IP-adressepræfikser for dit lokale netværksgateway"
   services="vpn-gateway"
   documentationCenter="na"
   authors="cherylmc"
   manager="carmonm"
   editor=""
   tags="azure-resource-manager"/>

<tags
   ms.service="vpn-gateway"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="08/08/2016"
   ms.author="cherylmc"/>

# <a name="modify-local-network-gateway-settings-using-powershell"></a>Ændre indstillinger for lokale netværk gateway ved hjælp af PowerShell

Nogle gange kan ændre indstillingerne for dit lokale netværksgateway AddressPrefix eller GatewayIPAddress. Vejledningen nedenfor hjælper dig med at ændre indstillinger for gateway dit lokale netværk. Du kan også ændre disse indstillinger i portalen Azure.

## <a name="before-you-begin"></a>Inden du går i gang
    
Du skal installere den nyeste version af Azure ressourcestyring PowerShell-cmdlet'er. Se, [hvordan du installerer og konfigurerer Azure PowerShell](../powershell-install-configure.md) kan finde flere oplysninger om installation af PowerShell-cmdlet'er.

## <a name="to-modify-ip-address-prefixes"></a>Ændre IP-adressepræfikser

[AZURE.INCLUDE [vpn-gateway-modify-ip-prefix-rm](../../includes/vpn-gateway-modify-ip-prefix-rm-include.md)]

## <a name="to-modify-the-gateway-ip-address"></a>Ændre gateway IP-adressen

[AZURE.INCLUDE [vpn-gateway-modify-lng-gateway-ip-rm](../../includes/vpn-gateway-modify-lng-gateway-ip-rm-include.md)]

## <a name="next-steps"></a>Næste trin

Du kan kontrollere din gateway-forbindelse. Se [bekræfte en gateway-forbindelse](vpn-gateway-verify-connection-resource-manager.md).

