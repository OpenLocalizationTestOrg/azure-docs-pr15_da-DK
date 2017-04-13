<properties
   pageTitle="Nulstille en Azure VPN-Gateway | Microsoft Azure"
   description="I denne artikel vejledes du gennem nulstilling af din Azure VPN-Gateway. I artiklen gælder for VPN-gateways i både klassiske og ressourcestyring installation modeller."
   services="vpn-gateway"
   documentationCenter="na"
   authors="cherylmc"
   manager="carmonm"
   editor=""
   tags="azure-resource-manager,azure-service-management"/>

<tags
   ms.service="vpn-gateway"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="09/23/2016"
   ms.author="cherylmc"/>

# <a name="reset-an-azure-vpn-gateway-using-powershell"></a>Nulstille en Azure VPN-Gateway ved hjælp af PowerShell


I denne artikel vejledes du gennem nulstilling af din Azure VPN-Gateway, ved hjælp af PowerShell-cmdlet'er. Disse instruktioner omfatter både den klassiske implementeringsmodel og implementeringsmodel ressourcestyring.

Nulstilling af gatewayen Azure VPN er nyttigt, hvis du mister tværs lokale VPN-forbindelse på en eller flere S2S VPN-tunneler. I så fald dine lokale VPN enheder alle fungerer korrekt, men kan ikke oprette IPSec-tunneler med Azure VPN gateways. 

Hver Azure VPN-gateway, består af to VM forekomster, der kører i en aktiv standby konfiguration. Når du bruger PowerShell-cmdlet til at nulstille gatewayen, genstarter gatewayen, og klik derefter genanvender i tværs lokale konfigurationer til den. Gatewayen bevarer den offentlige IP-adresse, den allerede har. Det betyder, at du ikke behøver at opdatere VPN router konfigurationen med en ny offentlige IP-adresse til Azure VPN-gateway.  

Når kommandoen er udstedt, er den aktuelle aktive forekomst af gatewayen Azure VPN genstartet med det samme. Der vil være et kort mellemrum under sekundære fra den aktive forekomst (der genstartet), på forekomsten af standby. Mellemrummet skal være mindre end et minut.

Hvis forbindelsen ikke er gendannet efter den første genstart, skal du udstede den samme kommando igen for at genstarte den anden VM forekomst (den nye aktive gateway). Hvis to genstart der anmodes om tilbage til igen, vises der en lidt længere periode hvor begge VM forekomster (aktive og standby) er der genstartet. Dette medfører en længere pause på VPN-forbindelsen, op til 2 til 4 minutter for FOS at fuldføre genstart.

Efter to genstarter, hvis du fortsat oplever forbindelsesproblemer i tværs det lokale miljø, skal du åbne en supportanmodning fra Azure-portalen.

## <a name="before-you-begin"></a>Inden du går i gang

Før du nulstiller din gateway, kan du kontrollere de vigtigste elementer for hver IPsec-til-websted (S2S) VPN tunnel nedenfor. En hvilken som helst uoverensstemmelse i elementerne, medfører afbrydelse af S2S VPN tunneler. Kontrollere og rette konfigurationer til dit lokale og Azure VPN gateways spare du fra unødvendige genstarter og afbrydelser for andre arbejde forbindelserne på gateways.

Før nulstilling af din gateway, skal du kontrollere følgende elementer:

- Internet IP-adresserne (VIPs) til både gatewayen VPN-Azure og lokale VPN-gateway er konfigureret korrekt i både Azure og lokale VPN politikker.
- Delt nøgle skal være den samme på både Azure og lokale VPN gateways.
- Hvis du anvender bestemte IPsec/IKE konfiguration, som kryptering, krypteres algoritmer, og for (til PFS), sikre både Azure og lokale VPN gateways har de samme konfigurationer.

## <a name="reset-a-vpn-gateway-using-the-resource-management-deployment-model"></a>Nulstille en VPN-Gateway ved hjælp af implementeringsmodel ressourcestyring

Ressourcestyring PowerShell-cmdlet til nulstilling af gatewayen er `Reset-AzureRmVirtualNetworkGateway`. I følgende eksempel nulstiller Azure VPN gateway, "VNet1GW" i ressourcegruppe "TestRG1".

    $gw = Get-AzureRmVirtualNetworkGateway -Name VNet1GW -ResourceGroup TestRG1
    Reset-AzureRmVirtualNetworkGateway -VirtualNetworkGateway $gw

## <a name="reset-a-vpn-gateway-using-the-classic-deployment-model"></a>Nulstille en VPN-Gateway ved hjælp af den klassiske implementeringsmodel

PowerShell-cmdlet til nulstilling af Azure VPN-gateway er `Reset-AzureVNetGateway`. I følgende eksempel nulstiller gatewayen Azure VPN for det virtuelle netværk kaldet "ContosoVNet".
 
    Reset-AzureVNetGateway –VnetName “ContosoVNet” 

Resultat:

    Error          :
    HttpStatusCode : OK
    Id             : f1600632-c819-4b2f-ac0e-f4126bec1ff8
    Status         : Successful
    RequestId      : 9ca273de2c4d01e986480ce1ffa4d6d9
    StatusCode     : OK


## <a name="next-steps"></a>Næste trin
    
Se [reference til styring af PowerShell-cmdlet](https://msdn.microsoft.com/library/azure/mt617104.aspx) og [ressourcestyring PowerShell cmdlet reference](http://go.microsoft.com/fwlink/?LinkId=828732) kan finde flere oplysninger.






