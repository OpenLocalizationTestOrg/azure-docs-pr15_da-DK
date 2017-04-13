<properties
   pageTitle="Oprette forbindelse ved hjælp af implementeringsmodel ressourcestyring og portalen Azure VNets | Microsoft Azure"
   description="Oprette en VPN-gateway forbindelse mellem VNets ved hjælp af Ressourcestyring og Azure portalen."
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
   ms.date="10/25/2016"
   ms.author="cherylmc" />

# <a name="configure-a-vnet-to-vnet-connection-using-the-azure-portal"></a>Konfigurere en VNet-VNet forbindelse ved hjælp af portalen Azure

> [AZURE.SELECTOR]
- [Ressourcestyring - Azure-portalen](vpn-gateway-howto-vnet-vnet-resource-manager-portal.md)
- [Ressourcestyring - PowerShell](vpn-gateway-vnet-vnet-rm-ps.md)
- [Klassisk - klassisk Portal](virtual-networks-configure-vnet-to-vnet-connection.md)


I denne artikel fører dig gennem trinnene til at oprette en forbindelse mellem VNets i ressourcestyring implementeringsmodel ved hjælp af VPN-Gateway og Azure portalen.

Når du forbinder virtuelle netværk ved hjælp af portalen Azure skal VNets være i samme-abonnement. Hvis din virtuelle netværk findes i forskellige abonnementer, kan du stadig forbinde dem ved hjælp af [PowerShell](vpn-gateway-vnet-vnet-rm-ps.md) trin.

![v2v diagram](./media/vpn-gateway-howto-vnet-vnet-resource-manager-portal/v2vrmps.png)

### <a name="deployment-models-and-methods-for-vnet-to-vnet-connections"></a>Installation-modeller og metoder til VNet-VNet forbindelser

[AZURE.INCLUDE [deployment models](../../includes/vpn-gateway-deployment-models-include.md)]

Følgende tabel viser de tilgængelige installation modeller og metoder til VNet-VNet konfigurationer. Når en artikel med konfigurationstrinnene er tilgængelig, link vi direkte til den fra denne tabel.

[AZURE.INCLUDE [vpn-gateway-table-vnet-vnet](../../includes/vpn-gateway-table-vnet-to-vnet-include.md)]

#### <a name="vnet-peering"></a>VNet peering

[AZURE.INCLUDE [vpn-gateway-vnetpeeringlink](../../includes/vpn-gateway-vnetpeeringlink-include.md)]

## <a name="about-vnet-to-vnet-connections"></a>Om VNet-VNet forbindelser

Oprette forbindelse et virtuelt netværk til en anden virtuelt netværk minder (VNet til VNet) om forbindelse en VNet til en lokal placering. Begge typer connectivity bruges en VPN-Azure gateway for at give en sikker tunnel ved hjælp af IPsec/IKE. Den VNets, du opretter forbindelse kan være i forskellige områder eller i forskellige abonnementer.

Du kan også kombinere VNet-VNet kommunikation med flere websted konfigurationer. På denne måde kan du oprette netværkstopologier, der kombinerer tværs lokale-forbindelse med mellem virtuelle netværksforbindelse, som vist i følgende diagram:

![Om forbindelser] (./media/vpn-gateway-howto-vnet-vnet-resource-manager-portal/aboutconnections.png "Om forbindelser")

### <a name="why-connect-virtual-networks"></a>Hvorfor oprette forbindelse virtuelle netværk?

Vil du måske at oprette forbindelse virtuelle netværk af følgende årsager:

- **Cross geografisk område-redundans og geografisk tilstedeværelse**
    - Du kan konfigurere din egen geografisk gentagelse eller synkronisering med sikre forbindelser uden at gå over internettet slutpunkter.
    - Med Azure trafik overordnet og justering af belastning, kan du konfigurere meget tilgængelige arbejdsprocess med geografisk redundans på tværs af flere Azure områder. Et vigtigt eksempel er til konfiguration af SQL altid på med tilgængelighed grupper, der strækker sig over flere Azure områder.

- **Internationale med flere lag programmer med isolationsniveauet eller administrative rammen**
    - I det samme region, kan du konfigurere med flere lag programmer med flere virtuelle netværk, der er forbundet med hinanden på grund af isolationsniveauet eller administrative krav.

Du kan finde flere oplysninger om VNet-VNet forbindelser, [VNet-VNet ofte stillede spørgsmål](#faq) i slutningen af denne artikel.

### <a name="values"></a>Eksempelindstillinger for

Når du bruger disse trin som en opgave, kan du bruge konfiguration eksempelværdier. For eksempel formål, kan vi bruge flere adresse mellemrum for hver VNet. Dog kræver ikke VNet-VNet konfigurationer flere adresse mellemrum.

**Værdier for TestVNet1:**

- VNet navn: TestVNet1
- Adresse mellemrum: 10.11.0.0/16
    - Undernet navn: front end
    - Undernet adresseområde: 10.11.0.0/24
- Ressourcegruppe: TestRG1
- Placering: Af USA
- Adresse mellemrum: 10.12.0.0/16
    - Undernet navn: backend-version
    - Undernet adresseområde: 10.12.0.0/24
- Undernet gatewaynavn: GatewaySubnet (det vil automatisk-fyld i portalen)
    - Gatewayen undernet adresseområde: 10.11.255.0/27
- DNS-Server: Brug af IP-adressen for din DNS-Server
- Virtuelt netværk gatewaynavn: TestVNet1GW
- Gatewayen Type: VPN
- VPN-type: distribuere-baseret
- SKU: Vælg den Gateway SKU, du vil bruge
- Offentlige IP-adressenavn: TestVNet1GWIP
- Forbindelse værdier:
    - Navn: TestVNet1toTestVNet4
    - Delte nøgle: Du kan selv oprette den delte nøgle. I dette eksempel skal bruger vi abc123. Det vigtigste er, når du opretter forbindelse mellem VNets, værdien skal stemme overens.

**Værdier for TestVNet4:**

- VNet navn: TestVNet4
- Adresse mellemrum: 10.41.0.0/16
    - Undernet navn: front end
    - Undernet adresseområde: 10.41.0.0/24
- Ressourcegruppe: TestRG1
- Placering: Vest USA
- Adresse mellemrum: 10.42.0.0/16
    - Undernet navn: backend-version
    - Undernet adresseområde: 10.42.0.0/24
- GatewaySubnet navn: GatewaySubnet (det vil automatisk-fyld i portalen)
    - GatewaySubnet adresseområde: 10.41.255.0/27
- DNS-Server: Brug af IP-adressen for din DNS-Server
- Virtuelt netværk gatewaynavn: TestVNet4GW
- Gatewayen Type: VPN
- VPN-type: distribuere-baseret
- SKU: Vælg den Gateway SKU, du vil bruge
- Offentlige IP-adressenavn: TestVNet4GWIP
- Forbindelse værdier:
    - Navn: TestVNet4toTestVNet1
    - Delte nøgle: Du kan selv oprette den delte nøgle. I dette eksempel skal bruger vi abc123. Det vigtigste er, når du opretter forbindelse mellem VNets, værdien skal stemme overens.


## <a name="CreatVNet"></a>1. oprette og konfigurere TestVNet1

Hvis du allerede har en VNet, kan du kontrollere, at indstillingerne er kompatibel med din VPN-gateway-design. Særligt opmærksomme på en hvilken som helst undernet, som kan overlappe andre netværk. Hvis du har overlappende undernet, fungerer forbindelsen ikke korrekt. Hvis din VNet er konfigureret med de korrekte indstillinger, kan du begynde at trinnene i afsnittet [angive en DNS-server](#dns) .

### <a name="to-create-a-virtual-network"></a>Oprette et virtuelt netværk

[AZURE.INCLUDE [vpn-gateway-basic-vnet-rm-portal](../../includes/vpn-gateway-basic-vnet-rm-portal-include.md)]  

## <a name="subnets"></a>2. tilføje yderligere adresseområde og oprette undernet

Du kan tilføje yderligere adresseområde og oprette undernet, når din VNet er blevet oprettet.
[AZURE.INCLUDE [vpn-gateway-additional-address-space](../../includes/vpn-gateway-additional-address-space-include.md)]

## <a name="gatewaysubnet"></a>3. Opret en gateway-undernet

Før der oprettes forbindelse til netværket virtuel til en gateway, skal du først oprette gatewayen undernet for det virtuelle netværk, du vil oprette forbindelse. Hvis det er muligt, er det bedst at oprette en gateway-undernet, ved hjælp af en CIDR blok med /28 eller /27 for at give tilstrækkelig IP-adresser for at medtage yderligere fremtidige konfigurationskrav.

Hvis du opretter denne konfiguration som en opgave, du referere til disse [eksempler på indstillinger](#values) , når du opretter dit undernet, gateway.

[AZURE.INCLUDE [vpn-gateway-no-nsg](../../includes/vpn-gateway-no-nsg-include.md)]

### <a name="to-create-a-gateway-subnet"></a>Oprette en gateway-undernet

[AZURE.INCLUDE [vpn-gateway-add-gwsubnet-rm-portal](../../includes/vpn-gateway-add-gwsubnet-rm-portal-include.md)]

## <a name="DNSServer"></a>4. Angiv en DNS-server (valgfrit)

Hvis du vil have navneoversættelse for virtuelle maskiner, der er installeret på din VNets, skal du angive en DNS-server.

[AZURE.INCLUDE [vpn-gateway-add-dns-rm-portal](../../includes/vpn-gateway-add-dns-rm-portal-include.md)]


## <a name="VNetGateway"></a>5. Opret et virtuelt netværksgateway

I dette trin skal oprette du gatewayen virtuelt netværk for din VNet. Dette trin kan tage op til 45 minutter at gennemføre. Hvis du opretter denne konfiguration som en opgave, kan du referere til [eksempler på indstillinger](#values).

### <a name="to-create-a-virtual-network-gateway"></a>Oprette et virtuelt netværksgateway

[AZURE.INCLUDE [vpn-gateway-add-gw-rm-portal](../../includes/vpn-gateway-add-gw-rm-portal-include.md)]


## <a name="CreateTestVNet4"></a>6. oprette og konfigurere TestVNet4

Når du har konfigureret TestVNet1, kan du oprette TestVNet4 ved at gentage de forrige trin, erstatter værdierne med de TestVNet4. Du behøver ikke at vente, indtil gatewayen virtuelt netværk for TestVNet1 er færdig med at oprette inden du konfigurerer TestVNet4. Hvis du bruger din egen værdier, skal du kontrollere, at felterne adresse ikke overlapper med en af de VNets, du vil oprette forbindelse til.


## <a name="TestVNet1Connection"></a>7. konfigurere TestVNet1 forbindelsen

Når virtuelt netværk gateways til både TestVNet1 og TestVNet4 har fuldført, kan du oprette netværket virtuel gateway forbindelser. I dette afsnit, skal oprette du en forbindelse fra VNet1 til VNet4.

1. Gå til gatewayen virtuelt netværk for din VNet i **alle de ressourcer**. For eksempel, **TestVNet1GW**. Klik på **TestVNet1GW** for at åbne bladet virtuelt netværk gateway.

    ![Forbindelser blade] (./media/vpn-gateway-howto-vnet-vnet-resource-manager-portal/settings_connection.png "Forbindelser blade")

2. Klik på **+ Tilføj** for at åbne bladet **Tilføj forbindelse** .

3. Skriv et navn til din forbindelse i navnefeltet på bladet **Tilføj forbindelse** . For eksempel, **TestVNet1toTestVNet4**.

    ![Forbindelsesnavn] (./media/vpn-gateway-howto-vnet-vnet-resource-manager-portal/v1tov4.png "Forbindelsesnavn")

4. **Forbindelsestype**. Vælg **VNet-VNet** på rullelisten.

5. Værdien i feltet **første virtuelt netværksgateway** udfyldes automatisk, fordi du opretter denne forbindelse fra den angivne virtuelle netværksgateway.

6. Feltet **anden virtuelt netværksgateway** er gatewayen virtuelt netværk af VNet, du vil oprette en forbindelse til. Klik på **Vælg en anden virtuelt netværksgateway** for at åbne bladet **vælge virtuelt netværksgateway** .

    ![Tilføj forbindelse] (./media/vpn-gateway-howto-vnet-vnet-resource-manager-portal/add_connection.png "Tilføj en forbindelse")

7. Få vist virtuelt netværk gateways, der er angivet på denne blade. Bemærk, at kun virtuelt netværk gateways, som er i dit abonnement er angivet. Hvis du vil oprette forbindelse til et virtuelt netværksgateway, der ikke er i dit abonnement, skal du bruge [PowerShell artikel](vpn-gateway-vnet-vnet-rm-ps.md). 

8. Klik på gatewayen virtuelt netværk, du vil oprette forbindelse til.
 
9. Skriv en delt nøgle for forbindelsen i feltet **delt nøgle** . Du kan generere eller selv oprette denne tast. I en-til-websted forbindelse ville den nøgle, du bruger være præcis det samme for enheden i det lokale miljø og virtuelle gateway netværksforbindelsen. Som minder om her, undtagen, i stedet for at oprette forbindelse til en VPN-enhed, du opretter forbindelse til en anden virtuelt netværksgateway.

    ![Delt nøgle] (./media/vpn-gateway-howto-vnet-vnet-resource-manager-portal/sharedkey.png "Delt nøgle")

10. Klik på **OK** i nederst del af bladet for at gemme ændringerne.

## <a name="TestVNet4Connection"></a>8. konfigurere TestVNet4 forbindelsen

Derefter skal du oprette en forbindelse fra TestVNet4 til TestVNet1. Brug den samme metode, du har brugt til at oprette forbindelsen fra TestVNet1 til TestVNet4. Sørg for, at du bruger den samme delte nøgle.

## <a name="VerifyConnection"></a>9. Kontrollér din forbindelse

Kontrollér forbindelsen. Benyt følgende fremgangsmåde for hver virtuelt netværksgateway:

1. Find bladet for gatewayen virtuelt netværk. For eksempel, **TestVNet4GW**. 
2. Klik på **forbindelser** for at få vist bladet forbindelser til gatewayen virtuelt netværk bladet virtuelt netværk gateway.

Få vist forbindelserne, og Kontrollér status. Når forbindelsen er oprettet, vil du se **lykkedes** og **tilsluttet** som statusværdier.

![Lykkedes] (./media/vpn-gateway-howto-vnet-vnet-resource-manager-portal/connected.png "Lykkedes")

Du kan dobbeltklikke på hver forbindelse separat for at få vist flere oplysninger om forbindelsen.

![Essentials] (./media/vpn-gateway-howto-vnet-vnet-resource-manager-portal/essentials.png "Essentials")

## <a name="faq"></a>VNet-VNet ofte stillede spørgsmål

Se ofte stillede spørgsmål om detaljerne for yderligere oplysninger om VNet-VNet forbindelser.

[AZURE.INCLUDE [vpn-gateway-vnet-vnet-faq](../../includes/vpn-gateway-vnet-vnet-faq-include.md)]

## <a name="next-steps"></a>Næste trin

Når forbindelsen er fuldført, kan du føje virtuelle maskiner til dine virtuelle netværk. Se [oprette en virtuel maskine](../virtual-machines/virtual-machines-windows-hero-tutorial.md) for trin.
