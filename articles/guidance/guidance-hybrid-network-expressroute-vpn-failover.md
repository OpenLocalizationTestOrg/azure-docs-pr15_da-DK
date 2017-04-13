<properties
   pageTitle="Implementere en meget tilgængelige hybrid netværksarkitektur | Microsoft Azure"
   description="Hvordan du kan implementere et sikkert websted til websted netværksarkitektur, der strækker sig over en Azure virtuelt netværk og et lokalt netværk, der er forbundet ved hjælp af ExpressRoute med VPN-gateway failover."
   services="guidance,virtual-network,vpn-gateway,expressroute"
   documentationCenter="na"
   authors="telmosampaio"
   manager="christb"
   editor=""
   tags="azure-resource-manager"/>

<tags
   ms.service="guidance"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="10/24/2016"
   ms.author="telmos"/>

# <a name="implementing-a-highly-available-hybrid-network-architecture"></a>Implementere en meget tilgængelige hybrid netværksarkitektur

[AZURE.INCLUDE [pnp-header](../../includes/guidance-pnp-header-include.md)]

I denne artikel beskriver bedste fremgangsmåder til at oprette forbindelse et lokalt netværk med virtuelle netværk på Azure ved hjælp af ExpressRoute, med et websted til websted virtuelt privat netværk (VPN) som en failover forbindelse. Trafikken flyder mellem det lokale netværk og en Azure virtuelt netværk (VNet) via en ExpressRoute forbindelse.  Hvis der er et tab af tilslutningen i ExpressRoute kredsløb, dirigeres trafik gennem en IPSec VPN-tunnel.

> [AZURE.NOTE] Azure har to forskellige installation modeller: [Ressourcestyring] [ resource-manager-overview] og klassisk. Denne grundrids bruger Ressourcestyring, som Microsoft anbefaler til nye installationer.

Typisk anvendelse sager for denne arkitektur omfatter:

- Hybrid programmer hvor arbejdsbelastninger, som er distribueret mellem et lokalt netværk og Azure.

- Programmer, der kører store, vigtige arbejdsbelastninger, der kræver en høj grad af skalerbarhed.

- Store sikkerhedskopierings- og udstyr til data, der skal gemmes et andet sted.

- Håndtering af arbejdsbelastninger, som Big Data.

- Brug af Azure som et nedbrud websted.

Bemærk, at hvis ExpressRoute kredsløb er tilgængelig, håndtere VPN ruten kun private peering forbindelser. Offentlige peering og Microsoft peering forbindelser overfører via internettet.

## <a name="architecture-diagram"></a>Arkitektur diagram

>[AZURE.NOTE] [Azure VPN Gateway service] [ azure-vpn-gateway] implementerer to typer af virtuelt netværk gateways; VPN-virtuelt netværk gateways og ExpressRoute virtuelt netværk gateways. I resten af dokumentet anvendes ordet *VPN-Gateway* henviser til Azure service, mens sætninger *VPN-gateway, virtuelt netværk* og *ExpressRoute virtuelt netværksgateway* til at referere til VPN- og ExpressRoute installation af gatewayen henholdsvis.

I det følgende diagram fremhæver de vigtige komponenter i denne arkitektur:

> Et Visio-dokument, der indeholder dette arkitektur diagram kan hentes på [Microsoft download center][visio-download]. I dette diagram er på "Hybrid netværket - ER VPN" side.

![[0]][0]

- **Azure virtuelle netværk (VNets).** Hver VNet er placeret i en enkelt Azure region og kan være vært for flere niveauer af programmet. Programmet niveauer kan være opdelt ved hjælp af undernet i hver VNet og/eller netværk sikkerhedsgrupper (NSGs).

- **Lokalt virksomhedens netværk.** Dette er et netværk af computere og enheder, der er forbundet via en privat LAN-netværk i en organisation, der kører.

- **VPN maskinen.** Dette er en enhed eller tjeneste, som indeholder eksterne forbindelse til den lokale netværk. VPN maskinen kan være en hardwareenhed, eller det kan være en softwareløsning som Routing og Remote Access Service (RRAS) i Windows Server 2012.

> [AZURE.NOTE] Få en liste over understøttede VPN-anvendelser og oplysninger om konfiguration af valgte VPN udstyr til at oprette forbindelse til en VPN-Gateway, Azure, i vejledning til den relevante enhed på [listen over VPN-enheder, der understøttes af Azure][vpn-appliance].

- **VPN-gateway, virtuelt netværk.** Gatewayen VPN-virtuelt netværk gør det muligt for VNet at oprette forbindelse til VPN maskinen i det lokale netværk. Gatewayen VPN-virtuelt netværk er konfigureret til at acceptere anmodninger fra det lokale netværk kun via VPN maskinen. Få mere at vide under [oprette forbindelse et lokalt netværk til et Microsoft Azure virtuelle netværk][connect-to-an-Azure-vnet].

- **ExpressRoute virtuelt netværksgateway.** Gatewayen ExpressRoute virtuelt netværk gør det muligt for VNet at oprette forbindelse til det ExpressRoute kredsløb, der bruges til forbindelsen til dit lokale netværk.

- **Gateway-undernet.** Virtuelt netværk gateways afholdes på det samme undernet.

- **VPN-forbindelse.** Forbindelsen har egenskaber, der angiver forbindelsestypen (IPSec) og nøglen delt med lokale VPN maskinen for at kryptere trafik.

- **ExpressRoute kredsløb.** Dette er et lag 2 eller layer 3 kredsløb fra connectivity udbyderen pågældende joinforbindelser lokale netværk med Azure via routerne kant. Kredsløbet bruger hardware infrastrukturen administreres af provideren connectivity.

- **N-delt cloud-program.** Dette er det program, der er hostet i Azure. Det kan indeholde flere lag, med flere undernet forbindelse via Azure Indlæs balancere. Trafik i hvert undernet er underlagt regler, der er defineret ved hjælp af [Azure netværk sikkerhedsgrupper][azure-network-security-group](NSGs). Du kan finde flere oplysninger i [Introduktion til Microsoft Azure sikkerhed][getting-started-with-azure-security].

## <a name="recommendations"></a>Anbefalinger

Azure byder på mange forskellige ressourcer og ressourcetyper, så denne referencearkitektur kan være klargjort mange forskellige måder. Vi har angivet en Azure ressourcestyring skabelon for at installere den referencearkitektur, der følger disse anbefalinger. Hvis du vælger at oprette din egen referencearkitektur skal du følge disse anbefalinger, medmindre du har særlige krav, en anbefaling der ikke passer.

### <a name="vnet-and-gatewaysubnet"></a>VNet og GatewaySubnet

Oprette gatewayen ExpressRoute virtuelt netværk og gatewayen VPN-virtuelt netværk i den samme VNet. Det betyder, at de skal dele den samme undernet med navnet **GatewaySubnet**

Hvis VNet allerede indeholder et undernet med navnet **GatewaySubnet**, kan du sikre dig, at det har en /27 eller større adresseområde. Hvis det eksisterende undernet er for lille, fjerne den på følgende måde, og Opret en ny, som vist i det næste punkt:

```powershell
$vnet = Get-AzureRmVirtualNetworkGateway -Name <yourvnetname> -ResourceGroupName <yourresourcegroup>
Remove-AzureRmVirtualNetworkSubnetConfig -Name GatewaySubnet -VirtualNetwork $vnet
```

Hvis VNet ikke indeholder et undernet med navnet **GatewaySubnet**, oprette en ny på følgende måde:

```powershell
$vnet = Get-AzureRmVirtualNetworkGateway -Name <yourvnetname> -ResourceGroupName <yourresourcegroup>
Add-AzureRmVirtualNetworkSubnetConfig -Name "GatewaySubnet" -VirtualNetwork $vnet -AddressPrefix "10.200.255.224/27"
$vnet = Set-AzureRmVirtualNetwork -VirtualNetwork $vnet
```

### <a name="vpn-and-expressroute-gateways"></a>VPN- og ExpressRoute gateways

Kontroller, at organisationen opfylder [ExpressRoute nødvendige krav] [ expressroute-prereq] til at oprette forbindelse til Azure.

Hvis du allerede har en VPN-virtuelt netværksgateway i din Azure VNet, du fjerne det, som vist nedenfor.

```powershell
Remove-AzureRmVirtualNetworkGateway -Name <yourgatewayname> -ResourceGroupName <yourresourcegroup>
```

Følg vejledningen i [implementere en hybrid netværksarkitektur med Azure ExpressRoute] [ implementing-expressroute] til at oprette din ExpressRoute forbindelse.

Følg vejledningen i [implementere en hybrid netværksarkitektur med Azure og lokale VPN] [ implementing-vpn] at oprette din VPN virtuelle gateway netværksforbindelse.

Når du har oprettet de virtuelle gateway netværksforbindelser, testmiljø som følgende:

1. Kontrollér, at du kan oprette forbindelse fra dit lokale netværk til din Azure VNet.

2. Kontakt din udbyder for at stoppe ExpressRoute forbindelse til test.

3. Kontrollér, at du kan stadig oprette forbindelse fra dit lokale netværk til din Azure VNet med VPN-virtuelt netværk gateway-forbindelsen.

4. Kontakt din udbyder til reestabilish ExpressRoute connectivity.

## <a name="considerations"></a>Overvejelser i forbindelse med

Se ExpressRoute forhold til det [implementere en Hybrid netværksarkitektur med Azure ExpressRoute] [ guidance-expressroute] vejledning.

Se det [implementere en Hybrid netværksarkitektur med Azure og lokale VPN] -til-websted VPN forhold til[ guidance-vpn] vejledning.

Generelle Azure sikkerhedsovervejelser finder se [Microsofts skytjenester og netværkssikkerhed][best-practices-security].

## <a name="solution-deployment"></a>Løsningsinstallation

Hvis du har et eksisterende lokalt infrastruktur allerede konfigureret med en passende netværk maskinen, kan du installere reference arkitekturen ved at følge disse trin:

1. Højreklik på knappen nedenfor, og vælg enten "Åbn link på en ny fane" eller "Åbn hyperlink i nyt vindue":  
[![Installere på Azure](./media/blueprints/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fmspnp%2Freference-architectures%2Fmaster%2Fguidance-hybrid-network-vpn-er%2Fazuredeploy.json)

2. Vent på linket for at åbne i portalen Azure, og derefter følge disse trin: 
  - **Ressourcegruppe** navnet allerede er defineret i parameterfilen, så vælge **Opret ny** og angive `ra-hybrid-vpn-er-rg` i tekstfeltet.
  - Vælg området på rullelisten **placering** .
  - Ikke redigere **Skabelon rod Uri** eller tekstfelterne **Parameter rod Uri** .
  - Gennemse vilkår og betingelser og derefter klikke på afkrydsningsfeltet **jeg accepterer du vilkår og betingelser, der er anført ovenfor** .
  - Klik på knappen **Køb** .

3. Vente på at fuldføre installationen.

4. Højreklik på knappen nedenfor, og vælg enten "Åbn link på en ny fane" eller "Åbn hyperlink i nyt vindue":  
[![Installere på Azure](./media/blueprints/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fmspnp%2Freference-architectures%2Fmaster%2Fguidance-hybrid-network-vpn-er%2Fazuredeploy-expressRouteCircuit.json)

5. Vent på linket for at åbne i portalen Azure, og derefter angive derefter følge disse trin: 
  - Vælg **Brug eksisterende** i sektionen **ressourcegruppe** og angive `ra-hybrid-vpn-er-rg` i tekstfeltet.
  - Vælg området på rullelisten **placering** .
  - Ikke redigere **Skabelon roden Uri** eller tekstfelter **Parameter roden Uri** .
  - Gennemse vilkår og betingelser og derefter klikke på afkrydsningsfeltet **jeg accepterer du vilkår og betingelser, der er anført ovenfor** .
  - Klik på knappen **Køb** .

<!-- links -->

[resource-manager-overview]: ../azure-resource-manager/resource-group-overview.md
[vpn-appliance]: ../vpn-gateway/vpn-gateway-about-vpn-devices.md
[azure-vpn-gateway]: ../vpn-gateway/vpn-gateway-about-vpngateways.md
[connect-to-an-Azure-vnet]: https://technet.microsoft.com/library/dn786406.aspx
[azure-network-security-group]: ../virtual-network/virtual-networks-nsg.md
[getting-started-with-azure-security]: ./../security/azure-security-getting-started.md
[expressroute-prereq]: ../expressroute/expressroute-prerequisites.md
[implementing-expressroute]: ./guidance-hybrid-network-expressroute.md#implementing-this-architecture
[implementing-vpn]: ./guidance-hybrid-network-vpn.md#implementing-this-architecture
[guidance-expressroute]: ./guidance-hybrid-network-expressroute.md
[guidance-vpn]: ./guidance-hybrid-network-vpn.md
[best-practices-security]: ../best-practices-network-security.md
[solution-script]: https://github.com/mspnp/reference-architectures/tree/master/guidance-hybrid-network-vpn-er/Deploy-ReferenceArchitecture.ps1
[solution-script-bash]: https://github.com/mspnp/reference-architectures/tree/master/guidance-hybrid-network-vpn-er/deploy-reference-architecture.sh
[vnet-parameters]: https://github.com/mspnp/reference-architectures/tree/master/guidance-hybrid-network-vpn-er/parameters/virtualNetwork.parameters.json
[virtualnetworkgateway-vpn-parameters]: https://github.com/mspnp/reference-architectures/tree/master/guidance-hybrid-network-vpn-er/parameters/virtualNetworkGateway-vpn.parameters.json
[virtualnetworkgateway-expressroute-parameters]: https://github.com/mspnp/reference-architectures/tree/master/guidance-hybrid-network-vpn-er/parameters/virtualNetworkGateway-expressRoute.parameters.json
[er-circuit-parameters]: https://github.com/mspnp/reference-architectures/tree/master/guidance-hybrid-network-vpn-er/parameters/expressRouteCircuit.parameters.json
[azure-powershell-download]: https://azure.microsoft.com/documentation/articles/powershell-install-configure/
[naming conventions]: ./guidance-naming-conventions.md
[azure-cli]: https://azure.microsoft.com/documentation/articles/xplat-cli-install/
[visio-download]: http://download.microsoft.com/download/1/5/6/1569703C-0A82-4A9C-8334-F13D0DF2F472/RAs.vsdx
[0]: ./media/blueprints/hybrid-network-expressroute-vpn-failover.png "Arkitekturen i en meget tilgængelige hybrid netværksarkitektur for brug af ExpressRoute og VPN-gateway"
[ARM-Templates]: https://azure.microsoft.com/documentation/articles/resource-group-authoring-templates/
