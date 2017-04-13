<properties
   pageTitle="Kontrollere en gateway forbindelse | Microsoft Azure"
   description="I denne artikel beskrives, hvordan du bekræfte en gateway forbindelse i implementeringsmodel ressourcestyring"
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
   ms.date="10/14/2016"
   ms.author="cherylmc"/>

# <a name="verify-a-gateway-connection"></a>Kontrollere en gateway-forbindelse

Du kan kontrollere din gateway-forbindelse på et par forskellige måder. I denne artikel viser dig, hvordan til at kontrollere status for en ressourcestyring gateway forbindelse ved hjælp af portalen Azure og ved hjælp af PowerShell.


## <a name="verify-using-powershell"></a>Bekræfte ved hjælp af PowerShell

Du skal installere den nyeste version af Azure ressourcestyring PowerShell-cmdlet'er. Oplysninger om installation af PowerShell-cmdletter, se, [hvordan du installerer og konfigurerer Azure PowerShell](../powershell-install-configure.md). Du kan finde flere oplysninger om brug af Ressourcestyring cmdlet'er i [Bruge Windows PowerShell med ressourcestyring](../powershell-azure-resource-manager.md).

### <a name="step-1-log-in-to-your-azure-account"></a>Trin 1: Log på din Azure-konto

1. Åbn din PowerShell console med administratorrettigheder og oprette forbindelse til din konto.

        Login-AzureRmAccount

2. Markér abonnementer til kontoen.

        Get-AzureRmSubscription 

3. Angiv det abonnement, du vil bruge.

        Select-AzureRmSubscription -SubscriptionName "Replace_with_your_subscription_name"

### <a name="step-2-verify-your-connection"></a>Trin 2: Kontrollér din forbindelse


[AZURE.INCLUDE [verify connection powershell](../../includes/vpn-gateway-verify-connection-ps-rm-include.md)] 


## <a name="verify-using-the-azure-portal"></a>Bekræfte ved hjælp af portalen Azure

[AZURE.INCLUDE [verify connection portal](../../includes/vpn-gateway-verify-connection-portal-rm-include.md)] 


## <a name="next-steps"></a>Næste trin

- Du kan føje virtuelle maskiner til dine virtuelle netværk. Se [oprette en virtuel maskine](../virtual-machines/virtual-machines-windows-hero-tutorial.md) for trin.

