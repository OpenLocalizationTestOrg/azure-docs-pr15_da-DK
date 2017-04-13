<properties
   pageTitle="Konfigurere et virtuelt netværk og Gateway til ExpressRoute i portalen klassisk | Microsoft Azure"
   description="I denne artikel vejledes du gennem konfiguration af et virtuelt netværk i ExpressRoute ved hjælp af den klassiske implementeringsmodel og klassisk portalen."
   documentationCenter="na"
   services="expressroute"
   authors="cherylmc"
   manager="carmonm"
   editor=""
   tags="azure-service-management"/>

<tags 
   ms.service="expressroute"
   ms.devlang="na"
   ms.topic="article" 
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services" 
   ms.date="09/20/2016"
   ms.author="cherylmc"/>

# <a name="create-a-virtual-network-for-expressroute-in-the-classic-portal"></a>Oprette et virtuelt netværk til ExpressRoute i portalen klassisk

Trinnene i denne artikel fører dig gennem konfigurationen af et virtuelt netværk og et virtuelt netværk gatewayen til brug med ExpressRoute ved hjælp af den klassiske implementeringsmodel og klassisk portalen.

Hvis du leder instruktioner til implementeringsmodel Ressourcestyring, kan du bruge følgende artikler: [oprette et virtuelt netværk ved hjælp af PowerShell](../virtual-network/virtual-networks-create-vnet-arm-ps.md) og [Tilføj en VPN-Gateway til en ressourcestyring VNet for ExpressRoute](expressroute-howto-add-gateway-resource-manager.md).

**Om Azure-installation modeller**

[AZURE.INCLUDE [vpn-gateway-clasic-rm](../../includes/vpn-gateway-classic-rm-include.md)] 

## <a name="create-a-classic-vnet-and-gateway"></a>Oprette en klassisk VNet og gateway

Følgende trin Opret en klassisk VNet og et virtuelt netværk gatewayen. Hvis du allerede har en klassisk VNet, kan du se afsnittet [konfiguration af en eksisterende klassisk VNet](#config) i denne artikel.

1. Log på [Azure klassisk portal](http://manage.windowsazure.com).

2. Klik på **Ny**i nederste venstre hjørne af skærmen. Klik på **Netværkstjenester**i navigationsruden, og klik derefter på **Virtuelt netværk**. Klik på **Brugerdefineret oprette** for at starte konfigurationsguiden.

3. På siden **virtuelle netværk** details, skal du angive følgende:

    - **Navn** – navn netværket virtuel. Du skal bruge denne virtuelle netværksnavn, når du installerer din FOS og PaaS forekomster, så du ikke vil foretage navnet for kompliceret.
    - **Placering** – placeringen er direkte relateret til den fysiske placering (område), hvor du vil dine ressourcer (FOS) skal placeres. Hvis du vil FOS, du anvender denne virtuelle netværk, der skal være fysisk er placeret i af USA, Vælg den nye placering. Du kan ikke ændre det område, der er knyttet til dit virtuelle netværk, du har oprettet.

4. Angiv følgende oplysninger på siden **DNS-servere og VPN-forbindelse** , og klik derefter på næste pilen nederst til højre. 

    - **DNS-servere** – Angiv navnet på DNS-server og IP-adresse, eller Vælg en tidligere registrerede DNS-server i genvejsmenuen. Denne indstilling oprette ikke en DNS-server. Det kan du angive de DNS-servere, du vil bruge til navneoversættelse for denne virtuelle netværk.
    - **Websted til websted Connectivity** - Markér afkrydsningsfeltet for **Konfigurer en VPN til-websted**.
    - **ExpressRoute** – Vælg afkrydsningsfeltet **Brug ExpressRoute**. Denne indstilling vises kun, hvis du har valgt **Konfigurer en VPN til-websted**.
    - **Lokalnetværk** - det er nødvendigt at have et lokalt netværk websted for ExpressRoute. Dog tale om en ExpressRoute forbindelse vil de adressepræfikser, der er angivet for webstedet lokale netværk blive ignoreret. I stedet bruges adressepræfikser reklame for Microsoft via ExpressRoute kredsløb til routing formål.<BR>Hvis du allerede har et lokalt netværk, der er oprettet for dine ExpressRoute forbindelse, kan du vælge den på rullelisten. Hvis ikke, vælge **Angiv et nyt lokalnetværk**.

5. Siden **til websted Connectivity** vises, hvis du har valgt for at angive et nyt lokale netværk i det forrige trin. Angiv følgende oplysninger for at konfigurere dit lokale netværk, og klik derefter på pilen Næste. 

    - **Navn** - det navn, du vil ringe til din lokale (lokalt) netværk websted.
    - **Adresseområde** -, herunder første IP- og CIDR (antal adresse). Du kan angive en hvilken som helst adresseområde, så længe den ikke overlapper med adresseområde til netværket virtuel. Typisk dette ville angive adresseområder for dit lokale netværk, men hvis det er ExpressRoute, disse indstillinger anvendes ikke. Denne indstilling er dog påkrævet for at oprette det lokale netværk, når du ved hjælp af portalen klassisk.
    - **Tilføj adresseområde** – denne indstilling er ikke relevant for ExpressRoute.


6. Angiv følgende oplysninger på siden **Virtuelle netværk adresse mellemrum** , og klik derefter på markeringen nederst til højre til at konfigurere dit netværk. 

    - **Adresseområde** -, herunder Start IP-adresse og adresse antal. Kontrollér, at felterne adresse, du angiver ikke overlapper et af felterne adresse, du har på dit lokale netværk.
    - **Tilføj undernet** - som herunder første IP- og adresse antal. Yderligere undernet er ikke påkrævet.
    - **Tilføj gateway undernet** - Klik for at tilføje gateway-undernettet. Gateway-undernet bruges kun til gatewayen virtuelt netværk og er påkrævet til denne konfiguration.<BR>Gateway-undernet CIDR (adresse Tæl) for ExpressRoute skal være /28 eller større (/ 27/26 osv.). Dette giver mulighed for nok IP-adresser i pågældende undernet til mulighed for konfiguration til at arbejde. Hvis du har markeret afkrydsningsfeltet for at bruge ExpressRoute, angiver portalen i portalen klassisk en gateway-undernet med /28 på.  Du kan justere CIDR adresse Tæl i portalen klassisk. Gateway-undernet vises som **Gateway** i portalen klassisk, selvom det rigtige navn på det gateway-undernet, som er oprettet er faktisk **GatewaySubnet**. Du kan se dette navn ved hjælp af PowerShell eller i portalen Azure.

7. Klik på markeringen nederst på siden, og netværket virtuel begynder at oprette. Når den er fuldført, vises **oprettet** vises under **Status** på siden **netværk** i portalen klassisk.

## <a name="gw"></a>Oprette gatewayen

1. På siden **netværk** , skal du klikke på det virtuelle netværk, som du lige har oprettet, og klik derefter på **Dashboard** øverst på siden.

2. Klik på **Opret Gateway** nederst på siden **Dashboard** , og vælg **Dynamisk Routing**. Klik på **Ja** for at bekræfte, at du vil oprette en gateway.

3. Når gatewayen begynder at oprette, kan du se en tilladelsen til meddelelsen, at du ved, at gatewayen er blevet startet. Det kan tage op til 45 minutter til at oprette gatewayen.

4. Link dit netværk til et kredsløb. Følg vejledningen i artiklen [Sådan oprettes en kæde VNets til ExpressRoute kredsløb](expressroute-howto-linkvnet-classic.md).

## <a name="config"></a>Konfigurere en eksisterende klassisk VNet til ExpressRoute

Hvis du allerede har en klassisk VNet, kan du konfigurere det til at oprette forbindelse til ExpressRoute i portalen klassisk. Indstillingerne er den samme som i ovenstående afsnit, så læse afsnittene at blive fortrolig med de nødvendige indstillinger. Hvis du vil oprette forbindelse til ExpressRoute /-til-websted fungerer sammen, skal du se [i denne artikel](expressroute-howto-coexist-classic.md) for at se trinnene. De er anderledes end trinnene i denne artikel.
 
1. Du skal oprette det lokale netværk, før du opdaterer resten af indstillingerne for VNet. Hvis du vil oprette en ny lokal netværk, som er påkrævet, når du konfigurerer ExpressRoute via portalen klassisk, skal du klikke på **Ny** **>** **Netværk Services** **>** **Virtuelt netværk** **>** **Tilføj lokale netværk**. Følg trinnene i guiden for at oprette det lokale netværk.

2. Brug **Konfigurer** side, du vil opdatere resten af indstillingerne for din VNet og knytte VNet til det lokale netværk.

3. Når du har konfigureret indstillingerne, gå til afsnittet [oprette gatewayen](#gw) i denne artikel for at oprette gatewayen.


## <a name="next-steps"></a>Næste trin

- Hvis du vil tilføje virtuelle maskiner til virtuelle netværk, skal du se [virtuelle maskiner læringsstier](https://azure.microsoft.com/documentation/learning-paths/virtual-machines/).
- Hvis du vil have mere at vide om ExpressRoute, kan du få vist [ExpressRoute oversigt](expressroute-introduction.md).


 
