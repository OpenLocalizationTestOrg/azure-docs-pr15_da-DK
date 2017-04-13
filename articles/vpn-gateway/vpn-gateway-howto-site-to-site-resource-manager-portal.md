<properties
   pageTitle="Oprette et virtuelt netværk med en-til-websted VPN-forbindelse ved hjælp af Azure ressourcestyring og portalen Azure | Microsoft Azure"
   description="Sådan oprettes VNet ved hjælp af implementeringsmodel ressourcestyring og forbinde den til din lokale lokale netværk ved hjælp af en VPN-S2S gateway-forbindelse."
   services="vpn-gateway"
   documentationCenter="na"
   authors="cherylmc"
   manager="carmonm"
   editor=""
   tags="azure-resource-manager"/>

<tags
   ms.service="vpn-gateway"
   ms.devlang="na"
   ms.topic="hero-article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="10/14/2016"
   ms.author="cherylmc"/>

# <a name="create-a-vnet-with-a-site-to-site-connection-using-the-azure-portal"></a>Oprette en VNet med forbindelse til websted ved hjælp af portalen Azure

> [AZURE.SELECTOR]
- [Ressourcestyring - Azure-portalen](vpn-gateway-howto-site-to-site-resource-manager-portal.md)
- [Ressourcestyring - PowerShell](vpn-gateway-create-site-to-site-rm-powershell.md)
- [Klassisk - klassisk Portal](vpn-gateway-site-to-site-create.md)


I denne artikel vejledes du gennem oprettelse af et virtuelt netværk og en VPN-websted til gateway-forbindelse til dit lokale netværk ved hjælp af implementeringsmodel Azure ressourcestyring og Azure portalen. Websted til websted forbindelser kan bruges til tværs lokal og hybrid konfigurationer.

![Diagram](./media/vpn-gateway-howto-site-to-site-resource-manager-portal/s2srmportal.png)


### <a name="deployment-models-and-methods-for-site-to-site-connections"></a>Installation-modeller og metoder til at websted forbindelser

[AZURE.INCLUDE [deployment models](../../includes/vpn-gateway-deployment-models-include.md)] 

Følgende tabel viser de tilgængelige installation modeller og metoder til at websted konfigurationer. Når en artikel med konfigurationstrinnene er tilgængelig, link vi direkte til den fra denne tabel.

[AZURE.INCLUDE [site-to-site table](../../includes/vpn-gateway-table-site-to-site-include.md)]

#### <a name="additional-configurations"></a>Yderligere konfigurationer 

Hvis du vil oprette forbindelse VNets sammen, men ikke opretter forbindelse til en lokal placering, kan du se [konfigurere en VNet-VNet forbindelse](vpn-gateway-vnet-vnet-rm-ps.md). Hvis du vil føje en forbindelse til websted til et VNet, der allerede har en forbindelse, kan du se [tilføje en S2S forbindelse til en VNet med en eksisterende forbindelse for VPN-gateway](vpn-gateway-howto-multi-site-to-site-resource-manager-portal.md).

## <a name="before-you-begin"></a>Inden du går i gang

Kontrollér, at du har følgende elementer, før du starter konfigurationen af:

- En VPN-kompatibel enhed og en person, der er i stand til at konfigurere den. Se [om VPN-enheder](vpn-gateway-about-vpn-devices.md). Hvis du ikke kender konfiguration af enheden VPN eller ikke har kendskab til den IP-adresse, områder, der er placeret i dine lokale netværkskonfiguration, skal du være koordineret med en person, der kan give disse oplysninger for dig.

- En eksternt modstående offentlige IP-adresse til din VPN-enhed. Denne IP-adresse må ikke være placeret bag en NAT.
    
- Et Azure-abonnement. Hvis du ikke allerede har et Azure-abonnement, kan du aktivere din [MSDN abonnement fordele](http://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) eller Log op for at få [gratis konto](http://azure.microsoft.com/pricing/free-trial/).

### <a name="values"></a>Konfiguration eksempelværdier i denne opgave


Når du bruger disse trin som en opgave, kan du bruge konfiguration eksempelværdier:

- **VNet navn:** TestVNet1
- **Adresse mellemrum:** 10.11.0.0/16 og 10.12.0.0/16
- **Undernet:**
    - Front end: 10.11.0.0/24
    - Backend-version: 10.12.0.0/24
    - GatewaySubnet: 10.12.255.0/27
- **Ressourcegruppe:** TestRG1
- **Placering:** Indtastning af østasiatiske USA
- **DNS-Server:** 8.8.8.8
- **Gatewaynavn:** VNet1GW
- **Offentlige IP-adresse:** VNet1GWIP
- **VPN-Type:** Distribuere-baseret
- **Forbindelsestype:** Websted til websted (IPsec)
- **Gateway Type:** VPN
- **Lokale netværk gatewaynavn:** Site2
- **Forbindelsesnavn:** VNet1toSite2


## <a name="CreatVNet"></a>1. Opret et virtuelt netværk 

Hvis du allerede har en VNet, kan du kontrollere, at indstillingerne er kompatibel med din VPN-gateway-design. Særligt opmærksomme på en hvilken som helst undernet, som kan overlappe andre netværk. Hvis du har overlappende undernet, fungerer forbindelsen ikke korrekt. Hvis din VNet er konfigureret med de korrekte indstillinger, kan du begynde at trinnene i afsnittet [angive en DNS-server](#dns) .

### <a name="to-create-a-virtual-network"></a>Oprette et virtuelt netværk

[AZURE.INCLUDE [vpn-gateway-basic-vnet-rm-portal](../../includes/vpn-gateway-basic-vnet-rm-portal-include.md)]  

## <a name="subnets"></a>2. tilføje yderligere adresseområde og undernet

Du kan tilføje yderligere adresseområde og undernet til din VNet, når den er blevet oprettet.

[AZURE.INCLUDE [vpn-gateway-additional-address-space](../../includes/vpn-gateway-additional-address-space-include.md)] 

## <a name="dns"></a>3. Angiv en DNS-server

### <a name="to-specify-a-dns-server"></a>Angive en DNS-server

[AZURE.INCLUDE [vpn-gateway-add-dns-rm-portal](../../includes/vpn-gateway-add-dns-rm-portal-include.md)]

## <a name="gatewaysubnet"></a>4. oprette en gateway-undernet

Før der oprettes forbindelse til netværket virtuel til en gateway, skal du først oprette gatewayen undernet for det virtuelle netværk, du vil oprette forbindelse. Hvis det er muligt, er det bedst at oprette en gateway-undernet, ved hjælp af en CIDR blok med /28 eller /27 for at give tilstrækkelig IP-adresser for at medtage yderligere fremtidige konfigurationskrav.

Hvis du opretter denne konfiguration som en opgave, du referere til disse [værdier](#values) , når du opretter dit undernet, gateway.

### <a name="to-create-a-gateway-subnet"></a>Oprette en gateway-undernet


[AZURE.INCLUDE [vpn-gateway-add-gwsubnet-rm-portal](../../includes/vpn-gateway-add-gwsubnet-rm-portal-include.md)]

## <a name="VNetGateway"></a>5. Opret et virtuelt netværksgateway

Hvis du opretter denne konfiguration som en opgave, kan du referere til [konfiguration af eksempelværdier](#values).

### <a name="to-create-a-virtual-network-gateway"></a>Oprette et virtuelt netværksgateway

[AZURE.INCLUDE [vpn-gateway-add-gw-rm-portal](../../includes/vpn-gateway-add-gw-rm-portal-include.md)]

## <a name="LocalNetworkGateway"></a>6. Opret en lokale netværksgateway

'Lokale netværk gatewayen' refererer til din lokale placering. Navngive gatewayen lokale netværk, som Azure kan referere til den. 

Hvis du opretter denne konfiguration som en opgave, kan du referere til [konfiguration af eksempelværdier](#values).

### <a name="to-create-a-local-network-gateway"></a>Oprette en lokal netværksgateway

[AZURE.INCLUDE [vpn-gateway-add-lng-rm-portal](../../includes/vpn-gateway-add-lng-rm-portal-include.md)]

## <a name="VPNDevice"></a>7. Konfigurer din VPN-enhed

[AZURE.INCLUDE [vpn-gateway-configure-vpn-device-rm](../../includes/vpn-gateway-configure-vpn-device-rm-include.md)]

## <a name="CreateConnection"></a>8. Opret en-til-websted VPN-forbindelse

Opret websted til websted VPN-forbindelse mellem din gateway, virtuelt netværk og enheden VPN. Sørg for at erstatte værdierne med dine egne. Den delte nøgle skal svare til den værdi, du har brugt til konfigurationen af VPN-enhed. 

Før du begynder i dette afsnit, du bekræfte, at din virtuelt netværksgateway og lokale netværk gateways er færdig med at oprette. Hvis du opretter denne konfiguration som en opgave, du referere til disse [værdier](#values) , når du opretter din forbindelse.

### <a name="to-create-the-vpn-connection"></a>Sådan opretter du VPN-forbindelse


[AZURE.INCLUDE [vpn-gateway-add-site-to-site-connection-rm-portal](../../includes/vpn-gateway-add-site-to-site-connection-rm-portal-include.md)]

## <a name="VerifyConnection"></a>9. bekræfte VPN-forbindelse

Du kan kontrollere din VPN-forbindelse i portalen eller ved hjælp af PowerShell.

[AZURE.INCLUDE [vpn-gateway-verify-connection-rm](../../includes/vpn-gateway-verify-connection-rm-include.md)]

## <a name="next-steps"></a>Næste trin

- Når forbindelsen er fuldført, kan du føje virtuelle maskiner til dine virtuelle netværk. Se den virtuelle maskiner [læringssti](https://azure.microsoft.com/documentation/learning-paths/virtual-machines) kan finde flere oplysninger.

- Se oplysninger om BGP [BGP oversigt](vpn-gateway-bgp-overview.md) og [hvordan du konfigurerer BGP](vpn-gateway-bgp-resource-manager-ps.md).
