<properties
   pageTitle="Hvordan du føjer flere VPN-gateway til-websted forbindelser til et virtuelt netværk for implementeringsmodel Ressourcestyring, ved hjælp af portalen Azure | Microsoft Azure"
   description="Føje flere websted S2S forbindelser til en VPN-gateway, der har en eksisterende forbindelse"
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
   ms.date="10/10/2016"
   ms.author="cherylmc"/>



# <a name="add-a-site-to-site-connection-to-a-vnet-with-an-existing-vpn-gateway-connection"></a>Føje en forbindelse til websted til et VNet med en eksisterende forbindelse for VPN-gateway

> [AZURE.SELECTOR]
- [Ressourcestyring - Portal](vpn-gateway-howto-multi-site-to-site-resource-manager-portal.md)
- [Klassisk - PowerShell](vpn-gateway-multi-site.md)

I denne artikel vejledes du gennem ved hjælp af portalen Azure føje-til-websted (S2S) forbindelser til en VPN-gateway, der har en eksisterende forbindelse. Denne type forbindelse der ofte kaldes en "med flere websted" konfiguration. 

Du kan bruge i denne artikel til at føje en S2S forbindelse til en VNet, der allerede har en S2S forbindelse, punkt-til-Site forbindelse eller VNet-VNet forbindelse. Der er visse begrænsninger, når du tilføjer forbindelser. Se afsnittet [inden du går i gang](#before) i denne artikel for at kontrollere, inden du starter konfigurationen. 

I denne artikel gælder for VNets, der er oprettet ved hjælp af Ressourcestyring implementeringsmodel, der har en RouteBased VPN-gateway. Disse trin gælder ikke for ExpressRoute /-til-websted fungerer sammen forbindelse konfigurationer. Du kan finde oplysninger om fungerer sammen forbindelser i [ExpressRoute/S2S fungerer sammen forbindelser](../expressroute/expressroute-howto-coexist-resource-manager.md) .

### <a name="deployment-models-and-methods"></a>Installation-modeller og metoder

[AZURE.INCLUDE [vpn-gateway-clasic-rm](../../includes/vpn-gateway-classic-rm-include.md)] 

Vi opdaterer denne tabel som nye artikler og yderligere værktøjer bliver tilgængelige for denne konfiguration. Når en artikel, der er tilgængelige, link vi direkte til den fra denne tabel.

[AZURE.INCLUDE [vpn-gateway-table-multi-site](../../includes/vpn-gateway-table-multisite-include.md)] 


## <a name="before"></a>Inden du går i gang

Kontrollere følgende elementer:

- Du opretter ikke forbindelse til ExpressRoute/S2S fungerer sammen.
- Du har et virtuelt netværk, der blev oprettet ved hjælp af Ressourcestyring installation modellen med en eksisterende forbindelse.
- Gatewayen virtuelt netværk for din VNet er RouteBased. Hvis du har en PolicyBased VPN-gateway, skal du slette gatewayen virtuelt netværk og opretter en ny VPN-gateway RoutBased.
- Ingen af adresseområder overlapper på grund af VNets, som denne VNet opretter forbindelse til.
- Du har VPN-kompatible enhed og en person, der er i stand til at konfigurere den. Se [om VPN-enheder](vpn-gateway-about-vpn-devices.md). Hvis du ikke kender konfiguration af enheden VPN eller ikke har kendskab til den IP-adresse, områder, der er placeret i dine lokale netværkskonfiguration, skal du være koordineret med en person, der kan give disse oplysninger for dig.
- Du har en eksternt modstående offentlige IP-adresse til din VPN-enhed. Denne IP-adresse må ikke være placeret bag en NAT.


## <a name="part1"></a>Del 1 – konfigurere en forbindelse

1. I en browser, gå til [Azure-portalen](http://portal.azure.com) og, hvis det er nødvendigt, logge på med din Azure-konto.
2. Klik på **alle ressourcer** og find din **virtuelt netværksgateway** på listen over ressourcer og klikke på den.
3. Klik på **forbindelser**under bladet **virtuelle netværksgateway** .

    ![Forbindelser blade](./media/vpn-gateway-howto-multi-site-to-site-resource-manager-portal/connectionsblade.png "Connections blade")<br>

4. Klik på **+ Tilføj**bladet **forbindelser** .

    ![Forbindelsesknappen Tilføj](./media/vpn-gateway-howto-multi-site-to-site-resource-manager-portal/addbutton.png "Add connection button")<br>

5. Udfyld følgende felter på bladet **Tilføj forbindelse** :
    - **Navn:** Det navn, du vil give til det websted, du opretter forbindelse til.
    - **Forbindelsestype:** Vælge **websted til websted (IPsec)**.

    ![Tilføje forbindelse blade](./media/vpn-gateway-howto-multi-site-to-site-resource-manager-portal/addconnectionblade.png "Add connection blade")<br>

## <a name="part2"></a>Del 2 - Tilføj en lokale netværksgateway

1. Klik på **lokale netværksgateway** , ***Vælg en gateway lokale netværk***. Dette åbner bladet **Vælg lokale netværksgateway** .

    ![Vælge lokale netværksgateway](./media/vpn-gateway-howto-multi-site-to-site-resource-manager-portal/chooselng.png "Choose local network gateway")<br>
2. Klik på **Opret ny** for at åbne bladet **Opret lokale netværksgateway** .

    ![Oprette lokale netværk gateway blade](./media/vpn-gateway-howto-multi-site-to-site-resource-manager-portal/createlngblade.png "Create local network gateway")<br>

3. Udfyld følgende felter på bladet **Opret lokale netværksgateway** :
    - **Navn:** Det navn, du vil give til den lokale netværk gateway ressource.
    - **IP-adresse:** Den offentlige IP-adressen på den VPN-enhed på det websted, du vil oprette forbindelse til.
    - **Adresse mellemrum:** Den adresse, der skal være skal dirigeres til det nye websted lokale netværk.
4. Klik på **OK** på bladet **Opret lokale netværksgateway** til at gemme ændringerne.

## <a name="part3"></a>Del 3 – tilføje den delte nøgle og oprette forbindelsen

1. Tilføj den delte nøgle, du vil bruge til at oprette forbindelsen bladet **Tilføj forbindelse** . Du kan enten få den delte nøgle fra enheden VPN eller foretage en dig her og derefter konfigurere din VPN-enhed for at bruge den samme delte nøgle. Det er vigtigt er, at de pågældende taster præcis det samme.

    ![Delte nøgle](./media/vpn-gateway-howto-multi-site-to-site-resource-manager-portal/sharedkey.png "Shared key")<br>
2. I bunden af bladet, skal du klikke på **OK** for at oprette forbindelsen.

## <a name="part4"></a>Del 4 – bekræfte VPN-forbindelse

Du kan kontrollere din VPN-forbindelse i portalen eller ved hjælp af PowerShell.

[AZURE.INCLUDE [vpn-gateway-verify-connection-rm](../../includes/vpn-gateway-verify-connection-rm-include.md)]


## <a name="next-steps"></a>Næste trin

- Når forbindelsen er fuldført, kan du føje virtuelle maskiner til dine virtuelle netværk. Se den virtuelle maskiner [læringssti](https://azure.microsoft.com/documentation/learning-paths/virtual-machines) kan finde flere oplysninger.
