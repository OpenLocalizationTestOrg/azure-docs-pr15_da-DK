<properties
   pageTitle="Implementere en Hybrid netværksarkitektur med Azure ExpressRoute | Henviser til arkitektur | Microsoft Azure"
   description="Hvordan du kan implementere et sikkert websted til websted netværksarkitektur, der strækker sig over en Azure virtuelt netværk og et lokalt netværk, der er forbundet ved hjælp af Azure ExpressRoute."
   services=""
   documentationCenter="na"
   authors="telmosampaio"
   manager="christb"
   editor=""
   tags=""/>

<tags
   ms.service="guidance"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="10/24/2016"
   ms.author="telmos"/>

# <a name="implementing-a-hybrid-network-architecture-with-azure-expressroute"></a>Implementere en hybrid netværksarkitektur med Azure ExpressRoute

[AZURE.INCLUDE [pnp-header](../../includes/guidance-pnp-header-include.md)]

I denne artikel beskriver bedste fremgangsmåder til at oprette forbindelse til et lokalt netværk med virtuelle netværk på Azure ved hjælp af ExpressRoute. ExpressRoute forbindelser foretages ved hjælp af en privat dedikeret forbindelse via en udbyder af tredjeparters connectivity. Den private forbindelse udvider dit lokale netværk til Azure at give adgang til dine egne IaaS infrastruktur i Azure, offentlige slutpunkter, der bruges i PaaS services og Office365 SaaS services. Dette dokument fokuserer på brug af ExpressRoute til at oprette forbindelse til et enkelt Azure virtuelt netværk (VNet) ved hjælp af det, der kaldes private peering.

> [AZURE.NOTE] Azure har to forskellige installation modeller: [Ressourcestyring] [ resource-manager-overview] og klassisk. Denne grundrids bruger Ressourcestyring, som Microsoft anbefaler til nye installationer.

Typisk anvendelse sager for denne arkitektur omfatter:

- Hybrid programmer hvor arbejdsbelastninger, som er distribueret mellem et lokalt netværk og Azure.

- Programmer, der kører store, vigtige arbejdsbelastninger, der kræver en høj grad af skalerbarhed.

- Store sikkerhedskopierings- og udstyr til data, der skal gemmes et andet sted.

- Håndtering af arbejdsbelastninger, som Big Data.

- Brug af Azure som et nedbrud websted.

> [AZURE.NOTE] [Teknisk oversigt over ExpressRoute] [ expressroute-technical-overview] giver en introduktion til ExpressRoute.

## <a name="architecture-diagram"></a>Arkitektur diagram

I det følgende diagram fremhæver de vigtige komponenter i denne arkitektur:

> Et Visio-dokument, der indeholder dette arkitektur diagram kan hentes på [Microsoft download center][visio-download]. I dette diagram er på siden "Hybrid netværk - ER".

![[0]][0]

- **Azure virtuelle netværk (VNets).** Hver VNet er placeret i en enkelt Azure region og kan være vært for flere niveauer af programmet. Programmet niveauer kan være opdelt ved hjælp af undernet i hver VNet og/eller netværk sikkerhedsgrupper (NSGs). 

- **Azure offentlige tjenester.** Dette er Azure tjenester, der kan anvendes i en hybrid-program. Disse tjenester er også tilgængelig via offentlige internettet, men få adgang til dem via et ExpressRoute kredsløb giver lav ventetid og mere forudsigelige ydeevne, da trafikken ikke går via internettet. Forbindelser udføres ved hjælp af **offentlige peering**, med adresser, der er enten ejes af din organisation eller fra udbyderen connectivity. 

- **Office 365-tjenester.** Dette er de offentligt tilgængelige Office 365-programmer og tjenester, der leveres af Microsoft. Forbindelser udføres ved hjælp af **Microsoft peering**, med adresser, der er enten ejes af din organisation eller fra udbyderen connectivity.

>[AZURE.NOTE] Du kan også oprette forbindelse direkte til Microsoft CRM Online via en Microsoft peering.

- **Lokalt virksomhedens netværk.** Dette er et netværk af computere og enheder, der er forbundet via en privat LAN-netværk i en organisation, der kører.

- **Lokale kant routere.** Dette er routere, der forbinder lokale netværk til kredsløb administreres af provideren. Afhængigt af hvordan din forbindelse er klargjort, skal du angive de offentlige IP-adresser, der bruges af routerne. 

- **Microsoft edge routere.** Dette er to routere i en aktiv-aktiv meget tilgængelige konfiguration. Disse routere aktivere en udbyder af forbindelse til at oprette forbindelse deres kredsløb direkte til deres datacenter. Afhængigt af hvordan din forbindelse er klargjort, skal du angive de offentlige IP-adresser, der bruges af routerne.

- **ExpressRoute kredsløb.** Dette er et lag 2 eller layer 3 kredsløb fra connectivity udbyderen pågældende joinforbindelser lokale netværk med Azure via routerne kant. Kredsløbet bruger hardware infrastrukturen administreres af provideren connectivity.

- **Connectivity udbydere.** Dette er firmaer, der leverer en forbindelse, enten ved hjælp af lag 2 eller layer 3 forbindelsen mellem dit datacenter og en Azure datacenter.

## <a name="recommendations"></a>Anbefalinger

Azure byder på mange forskellige ressourcer og ressourcetyper, så denne referencearkitektur kan være klargjort mange forskellige måder. Vi har angivet en Azure ressourcestyring skabelon for at installere den referencearkitektur, der følger disse anbefalinger. Hvis du vælger at oprette din egen referencearkitektur skal du følge disse anbefalinger, medmindre du har særlige krav, en anbefaling der ikke passer.

### <a name="connectivity-providers"></a>Udbydere af forbindelse

Vælg en passende ExpressRoute connectivity udbyder for din placering. Hvis du vil hente en liste over connectivity udbydere tilgængelige på din placering, skal du bruge kommandoen følgende Azure PowerShell:

```powershell
Get-AzureRmExpressRouteServiceProvider
```

ExpressRoute connectivity udbydere forbinde dit datacenter til Microsoft på følgende måder:

- **Der findes på en sky exchange samtidig.** Hvis du er samtidig placeret i en funktion med en sky exchange, kan du bestille virtuelle kryds-forbindelser til Microsoft cloud gennem samtidig placering-provideren Ethernet exchange. Udbydere af samtidig placering kan tilbyde Layer 2 kryds-forbindelser eller administrerede Layer 3 kryds-forbindelser mellem infrastrukturen i funktionen samtidig placering og Microsoft cloud..

- **Punkt til punkt Ethernet-forbindelser.** Du kan oprette forbindelse til din lokale datacentre/kontorer i Microsoft skyen gennem punkt til punkt Ethernet links. Punkt til punkt Ethernet udbydere kan tilbyde Layer 2 forbindelser eller administrerede Layer 3 forbindelser mellem webstedet og Microsoft skyen.

- **Alle-til-alle (IPVPN) netværk.** Du kan integrere din WAN med Microsoft skyen. IPVPN udbydere (typisk MPLS VPN) tilbyder en-til-en forbindelse mellem din gren kontorer og datacentre. Microsoft cloud kan være forbundet til din WAN at give den et ligesom andre afdelingen. WAN udbydere tilbyder typisk administrerede Layer 3-forbindelse.

Du kan finde flere oplysninger om connectivity udbydere, se [ExpressRoute kredsløb og routing domæner][connectivity-providers].

### <a name="expressroute-circuit"></a>ExpressRoute kredsløb

Sørg for, at din organisation har gennemgået [ExpressRoute nødvendige krav] [ expressroute-prereqs] til at oprette forbindelse til Azure.

Hvis du ikke allerede har gjort det, kan du tilføje et undernet med navnet `GatewaySubnet` til din Azure VNet og oprette en ExpressRoute virtuelt netværksgateway, ved hjælp af tjenesten Azure VPN-Gateway. Du kan finde flere oplysninger om denne proces, se [ExpressRoute arbejdsprocesser for klargøring af kredsløb og kredsløb stater][ExpressRoute-provisioning].

Oprette et ExpressRoute kredsløb på følgende måde:

1. Kør følgende PowerShell-kommando:

    ```powershell
    New-AzureRmExpressRouteCircuit -Name <<circuit-name>> -ResourceGroupName <<resource-group>> -Location <<location>> -SkuTier <<sku-tier>> -SkuFamily <<sku-family>> -ServiceProviderName <<service-provider-name>> -PeeringLocation <<peering-location>> -BandwidthInMbps <<bandwidth-in-mbps>>
    ```

2. Sende den `ServiceKey` for den nye kredsløb til udbyderen af.

3. Vent provideren til at klargøre kredsløbet. Du kan kontrollere tilstanden klargøring af et kredsløb ved hjælp af følgende PowerShell-kommando:

    ```powershell
    Get-AzureRmExpressRouteCircuit -Name <<circuit-name>> -ResourceGroupName <<resource-group>>
    ```

Den `Provisioning state` i den `Service Provider` sektion af output ændres fra `NotProvisioned` til `Provisioned` hvornår kredsløbet er klar.

>[AZURE.NOTE]Hvis du bruger en layer 3-forbindelse, skal udbyderen, der konfigurere og administrere distribution til. Du angiver de oplysninger, der er nødvendige for at aktivere hos udbyderen for at implementere de relevante omdirigerer.

Hvis du bruger en layer 2-forbindelse, skal du følge nedenstående trin:

1. Reservere to/30 undernet bestående af gyldige offentlige IP-adresser for hver type af peering du vil implementere. Disse/30 undernet bruges til at give IP-adresser til routerne bruges til kredsløbet. Hvis du implementerer privat, offentlig og Microsoft peering, skal du 6 /30 undernet med gyldige offentlige IP-adresser.     

2. Konfigurere routing til ExpressRoute kredsløb. Du skal køre kommandoen nedenfor for hver type af peering du vil konfigurere (privat, offentlige og Microsoft).

    >[AZURE.NOTE]Se [Opret og rediger rute for en ExpressRoute kredsløb] [ configure-expressroute-routing] få mere at vide. Brug følgende PowerShell-kommandoer til at tilføje et netværk, peering til dirigere trafik:

    ```powershell
    Set-AzureRmExpressRouteCircuitPeeringConfig -Name <<peering-name>> -Circuit <<circuit-name>> -PeeringType <<peering-type>> -PeerASN <<peer-asn>> -PrimaryPeerAddressPrefix <<primary-peer-address-prefix>> -SecondaryPeerAddressPrefix <<secondary-peer-address-prefix>> -VlanId <<vlan-id>>

    Set-AzureRmExpressRouteCircuit -ExpressRouteCircuit <<circuit-name>>
    ```

3. Reservere en anden gruppe af gyldige offentlige IP-adresser til brug for NAT til offentlige, og Microsoft peering. Det anbefales at have en anden gruppe for hver peering. Angiv puljen til udbyderen forbindelse, så de kan konfigurere BGP reklamer for disse områder.

[Sammenkæde dine private VNet(s) i skyen til ExpressRoute kredsløb][link-vnet-to-expressroute]. Brug følgende PowerShell-kommandoer:

```
$circuit = Get-AzureRmExpressRouteCircuit -Name <<circuit-name>> -ResourceGroupName <<resource-group>>
$gw = Get-AzureRmVirtualNetworkGateway -Name <<gateway-name>> -ResourceGroupName <<resource-group>>
New-AzureRmVirtualNetworkGatewayConnection -Name <<connection-name>> -ResourceGroupName <<resource-group>> -Location <<location> -VirtualNetworkGateway1 $gw -PeerId $circuit.Id -ConnectionType ExpressRoute
```

Bemærk følgende punkter:

- ExpressRoute bruger kant Gateway Protocol (BGP) til udveksling af routing-oplysninger mellem dit netværk og Azure.

- Du kan oprette forbindelse flere VNets, der er placeret i forskellige områder for at det samme ExpressRoute kredsløb, som alle VNets og ExpressRoute kredsløb er placeret i det samme geopolitiske område.

## <a name="scalability-considerations"></a>Overvejelser i forbindelse med skalerbarhed

ExpressRoute kredsløb giver en stor båndbredde sti mellem netværk. Generelt, jo højere båndbredde desto større omkostninger. Selvom nogle udbydere gør det muligt at ændre båndbredden, skal du kontrollere, at du vælger en indledende båndbredde, der overgår dine behov, og giver plads til vækst. I tilfælde af, at du har brug at øge båndbredde fremover, kan du er venstre med to indstillinger.

Øge båndbredden. Husk, at ikke alle udbydere gør det muligt at gøre det dynamisk. Og du skal undgå at skulle gøre dette som muligt. Men hvis det er nødvendigt, efter at have kontrolleret hos din udbyder, kører kommandoerne nedenfor.

```powershell
$ckt = Get-AzureRmExpressRouteCircuit -Name <<circuit-name>> -ResourceGroupName <<resource-group>>
$ckt.ServiceProviderProperties.BandwidthInMbps = <<bandwidth-in-mbps>>
Set-AzureRmExpressRouteCircuit -ExpressRouteCircuit $ckt
```

Du kan øge båndbredde uden tab af forbindelse. Nedgradere båndbredden medfører forstyrrelse i connectivity. Du skal slette kredsløbet og indsætte det igen med den nye konfiguration.

Hvis du bruger den Standard SKU for ExpressRoute og har du brug for at opgradere til Premium eller ændre du er dine priser planlægge (betalte eller ubegrænset), kører kommandoerne nedenfor. Den `Sku.Tier` egenskaben kan være `Standard` eller `Premium`; den `Sku.Name` egenskaben kan være `MeteredData` eller `UnlimitedData`.

```powershell
$ckt = Get-AzureRmExpressRouteCircuit -Name <<circuit-name>> -ResourceGroupName <<resource-group>>

$ckt.Sku.Tier = "Premium"
$ckt.Sku.Family = "MeteredData"
$ckt.Sku.Name = "Premium_MeteredData"

 Set-AzureRmExpressRouteCircuit -ExpressRouteCircuit $ckt
```

>[AZURE.IMPORTANT] Sørg for, at den `Sku.Name` egenskab matcher den `Sku.Tier` og `Sku.Family`. Hvis du ændrer familien og niveau, men ikke navnet, være din forbindelse deaktiveret.

Du kan opgradere SKU uden afbrydelser, men du kan ikke skifte fra den ubegrænsede priser plan til forbrugsafregnede. Når nedgradere SKU, skal din forbrug af båndbredde forbliver standard inden for den standard SKU.

> [AZURE.NOTE] ExpressRoute indeholder to priser planer til kunder, baseret på vha eller ubegrænset data. Se [ExpressRoute priser] [ expressroute-pricing] få mere at vide. Gebyrer variere, alt efter kredsløb båndbredde. Tilgængelige båndbredde, sandsynligt varierer fra udbyder til udbyder. Brug den `Get-AzureRmExpressRouteServiceProvider` til at se de udbydere, der er tilgængelige i dit område og de båndbredder, de tilbyder.

Et enkelt ExpressRoute kredsløb kan understøtte et antal peerings og VNet links. Se [begrænsninger for ExpressRoute] [ expressroute-limits] kan finde flere oplysninger.

Der er ExpressRoute Premium tilføjelsesprogram for et ekstra gebyr:

- Op til 10.000 omdirigerer per kredsløb. Uden ExpressRoute Premium tilføjelsesprogram er grænsen 4.000 omdirigerer per kredsløb.

- Global connectivity, aktivere et ExpressRoute kredsløb, der er placeret et sted i verden til at få adgang til ressourcer i en hvilken som helst område i stedet for kun områder i den samme kontinent.

- Et større antal VNet links per kredsløb fra 10 til en større grænse, afhængigt af båndbredden for kredsløbet.

ExpressRoute kredsløb er designet til at tillade midlertidige netværk lyseksplosioner op til to gange den båndbredde grænse, som du udtaget til gratis. Dette opnås ved hjælp af overflødige links. Men ikke alle connectivity udbydere understøtter denne funktion Kontrollér, at udbyderen connectivity gør det muligt for denne funktion før afhængigt af den.

## <a name="availability-considerations"></a>Overvejelser i forbindelse med tilgængelighed

Du kan konfigurere høj tilgængelighed til Azure forbindelsen på forskellige måder, afhængigt af typen provider, du bruger, og antallet ExpressRoute kredsløb og virtuelle gateway netværksforbindelser du er villig til at konfigurere. De punkter nedenfor opsummering tilgængelighed muligheder:

- ExpressRoute understøtter ikke router redundans protokoller som HSRP og VRRP at implementere høj tilgængelighed. I stedet bruges et overflødige par BGP sessioner per peering. For at lette meget tilgængelige forbindelser til netværket, Klargør Microsoft Azure du med to overflødige portene på to routere (en del af Microsoft edge) i en aktiv-aktiv konfiguration.

- Hvis du bruger en 2-lagforbindelse, skal du installere overflødige routere i dit lokale netværk i en aktiv-aktiv konfiguration. Forbind det primære kredsløb til én router, og det sekundære kredsløb til en anden. Dette giver dig en meget tilgængelige forbindelse i begge ender af forbindelsen. Det er nødvendigt, hvis du kræver ExpressRoute SERVICENIVEAUAFTALE. Se [SERVICENIVEAUAFTALE til Azure ExpressRoute] [ sla-for-expressroute] få mere at vide.

I følgende diagram vises en konfiguration med overflødige lokalt routere forbindelse til de primære og sekundære kredsløb. Hvert kredsløb håndterer trafik til en offentlig peering og en privat peering (hver peering er angivet et par /30 adresse mellemrum, som beskrevet i forrige afsnit).

![[1]][1]

Hvis du bruger en layer 3-forbindelse, skal du kontrollere, at det giver overflødige BGP sessioner, der håndterer tilgængelighed for dig.

Virtuelt Network kan være forbundet med flere ExpressRoute kredsløb og hver kredsløb kan leveres af forskellige tjenesteudbydere. Denne strategi giver yderligere høj tilgængelighed og genoprettelse efter nedbrud.

Konfigurere en VPN til-websted som en failover sti til ExpressRoute. Dette gælder kun for private peering. Internettet er kun failover stien for Azure og Office 365-tjenester.

Som standard bruger BGP sessioner en inaktiv timeoutværdien af 60 sekunder. Når en session er timeout 3 gange, ruten er markeret som ikke tilgængelig, og al trafik omdirigeres til den resterende router. Denne 180 sekunders timeout kan være for lang til vigtige programmer. Du kan ændre indstillingerne for BGP timeout for den lokale router til en mindre værdi i så fald.

## <a name="manageability-considerations"></a>Overvejelser i forbindelse med administration

Du kan bruge [Azure Connectivity værktøjskassen (AzureCT)] [ azurect] til at overvåge forbindelsen mellem dit lokale datacenter og Azure.

## <a name="security-considerations"></a>Overvejelser om sikkerheden

Du kan konfigurere sikkerhedsindstillinger for Azure forbindelsen på forskellige måder, afhængigt af din sikkerhedsproblemer og overholdelse af angivne standarder behov. De punkter nedenfor opsummerer dine sikkerhedsindstillinger.

ExpressRoute fungerer i layer 3. Trusler i programlag forhindres ved hjælp af et netværk sikkerhed maskinen som begrænser trafik til gyldige ressourcer. Desuden kan ExpressRoute forbindelser ved hjælp af offentlige peering kun startes fra lokale. Dette forhindrer en uautoriseret tjeneste for at få adgang til og risikere at afsløre lokale data fra offentlige internettet.

Tilføj for at maksimere sikkerhed, netværk sikkerhedsfunktioner mellem det lokale netværk og udbyder kant routerne. Det gør det nemmere for at begrænse indgående pengestrømme af uautoriserede trafik fra VNet:

![[2]][2]

Til overvågning eller overholdelse formål, kan det være nødvendigt at forbyde direkte adgang fra komponenter, der kører i VNet til internettet og implementere [tvunget tunnelføring][forced-tuneling]. I dette tilfælde skal internettrafik omdirigeres tilbage gennem en proxy, der kører i det lokale miljø, hvor den kan overvåges. Proxyen kan konfigureres til at blokere uautoriseret trafik der flyder ud og filtrere potentielt skadelig indgående trafik.

![[3]][3]

Som standard fremvise Azure FOS slutpunkter, der bruges til at give login adgang til administration af formål - RDP og Remote Powershell for Windows FOS og SSH for Linux-baserede FOS når installeres via Azure-portalen. For at maksimere sikkerhed, ikke aktivere en offentlig IP-adresse til dit FOS, og brug NSGs til at sikre, at disse FOS ikke er offentligt tilgængelig. FOS skal kun være tilgængelige ved hjælp af den interne IP-adresse. Disse adresser kan gøres tilgængelige via ExpressRoute netværk, så lokale DevOps personale til at udføre eventuelle nødvendige konfiguration eller vedligeholdelse.

Hvis du skal afsløre management slutpunkter for FOS for et eksternt netværk, bruge NSGs og/eller adgangskontrollister at begrænse synligheden af portene til en whitelist af IP-adresser eller netværk.

## <a name="troubleshooting-considerations"></a>Overvejelser i forbindelse med fejlfinding

Hvis en tidligere fungerende ExpressRoute kredsløb mislykkes nu til at oprette forbindelse, ikke findes på en hvilken som helst konfiguration ændringer i det lokale miljø eller i din private VNet, kan du muligvis kontakte connectivity udbyder og arbejde med dem at løse problemet. Du kan også bruge følgende Azure PowerShell-kommandoer til at udføre nogle begrænset kontrol og at finde ud af, hvor problemer kan være:

- Kontrollere, at kredsløbet er klargjort:

```powershell
Get-AzureRmExpressRouteCircuit -Name <<circuit-name>> -ResourceGroupName <<resource-group>>
```

Output fra denne kommando viser flere egenskaber for din kredsløb, herunder `ProvisioningState`, `CircuitProvisioningState`, og `ServiceProviderProvisioningState` som vist nedenfor.

```
ProvisioningState                : Succeeded
Sku                              : {
                                     "Name": "Standard_MeteredData",
                                     "Tier": "Standard",
                                     "Family": "MeteredData"
                                   }
CircuitProvisioningState         : Enabled
ServiceProviderProvisioningState : NotProvisioned
```

Hvis den `ProvisioningState` ikke er angivet til `Succeeded` efter at du forsøgte at oprette et nyt kredsløb, fjernes kredsløbet ved hjælp af kommandoen nedenfor, og prøv at oprette den igen.

```powershell
Remove-AzureRmExpressRouteCircuit -Name <<circuit-name>> -ResourceGroupName <<resource-group>>
```

Hvis din udbyder havde allerede klargjort kredsløb, og den `ProvisioningState` er indstillet til `Failed`, eller den `CircuitProvisioningState` er ikke `Enabled`, kontakte din udbyder for at få hjælp.

## <a name="solution-deployment"></a>Løsningsinstallation

Hvis du har et eksisterende lokalt infrastruktur allerede konfigureret med en passende netværk maskinen, kan du installere reference arkitekturen ved at følge disse trin:

Hvis du har et eksisterende lokalt infrastruktur allerede konfigureret med en VPN-maskinen, kan du installere reference arkitekturen ved at følge disse trin:

1. Højreklik på knappen nedenfor, og vælg enten "Åbn link på en ny fane" eller "Åbn hyperlink i nyt vindue":  
[![Installere på Azure](./media/blueprints/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fmspnp%2Freference-architectures%2Fmaster%2Fguidance-hybrid-network-er%2Fazuredeploy.json)

2. Vent på linket for at åbne i portalen Azure, og derefter følge disse trin: 
  - **Ressourcegruppe** navnet allerede er defineret i parameterfilen, så vælge **Opret ny** og angive `ra-hybrid-er-rg` i tekstfeltet.
  - Vælg området på rullelisten **placering** .
  - Ikke redigere **Skabelon rod Uri** eller tekstfelterne **Parameter rod Uri** .
  - Gennemse vilkår og betingelser og derefter klikke på afkrydsningsfeltet **jeg accepterer du vilkår og betingelser, der er anført ovenfor** .
  - Klik på knappen **Køb** .

3. Vente på at fuldføre installationen.

4. Højreklik på knappen nedenfor, og vælg enten "Åbn link på en ny fane" eller "Åbn hyperlink i nyt vindue":  
[![Installere på Azure](./media/blueprints/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fmspnp%2Freference-architectures%2Fmaster%2Fguidance-hybrid-network-er%2Fazuredeploy-expressRouteCircuit.json)

5. Vent på linket for at åbne i portalen Azure, og derefter følge disse trin: 
  - Vælg **Brug eksisterende** i sektionen **ressourcegruppe** og angive `ra-hybrid-er-rg` i tekstfeltet.
  - Vælg området på rullelisten **placering** .
  - Ikke redigere **Skabelon rod Uri** eller tekstfelterne **Parameter rod Uri** .
  - Gennemse vilkår og betingelser og derefter klikke på afkrydsningsfeltet **jeg accepterer du vilkår og betingelser, der er anført ovenfor** .
  - Klik på knappen **Køb** .

6. Vente på at fuldføre installationen.

## <a name="next-steps"></a>Næste trin

- Se [implementere en meget tilgængelige hybrid netværksarkitektur] [ highly-available-network-architecture] oplysninger om Øget tilgængelighed af et netværk, hybrid baseret på ExpressRoute ved fejlbehæftede over til en VPN-forbindelse.

<!-- links -->
[expressroute-technical-overview]: ../expressroute/expressroute-introduction.md
[resource-manager-overview]: ../azure-resource-manager/resource-group-overview.md
[azure-powershell]: ../powershell-azure-resource-manager.md
[expressroute-prereqs]: ../expressroute/expressroute-prerequisites.md
[configure-expressroute-routing]: ../expressroute/expressroute-howto-routing-arm.md
[sla-for-expressroute]: https://azure.microsoft.com/support/legal/sla/expressroute/v1_0/
[link-vnet-to-expressroute]: ../expressroute/expressroute-howto-linkvnet-arm.md
[ExpressRoute-provisioning]: ../expressroute/expressroute-workflows.md
[expressroute-pricing]: https://azure.microsoft.com/pricing/details/expressroute/
[expressroute-limits]: ../azure-subscription-service-limits.md#networking-limits
[sample-script]: #sample-solution-script
[connectivity-providers]: ../expressroute/expressroute-introduction.md#how-can-i-connect-my-network-to-microsoft-using-expressroute
[azurect]: https://github.com/Azure/NetworkMonitoring/tree/master/AzureCT
[forced-tuneling]: ./guidance-iaas-ra-secure-vnet-hybrid.md
[highly-available-network-architecture]: ./guidance-hybrid-network-expressroute-vpn-failover.md
[arm-templates]: ../resource-group-authoring-templates.md
[naming-conventions]: ./guidance-naming-conventions.md
[solution-script]: https://github.com/mspnp/reference-architectures/tree/master/guidance-hybrid-network-er/Deploy-ReferenceArchitecture.ps1
[solution-script-bash]: https://github.com/mspnp/reference-architectures/tree/master/guidance-hybrid-network-er/deploy-reference-architecture.sh
[vnet-parameters]: https://github.com/mspnp/reference-architectures/tree/master/guidance-hybrid-network-er/parameters/virtualNetwork.parameters.json
[virtualnetworkgateway-parameters]: https://github.com/mspnp/reference-architectures/tree/master/guidance-hybrid-network-er/parameters/virtualNetworkGateway.parameters.json
[visio-download]: http://download.microsoft.com/download/1/5/6/1569703C-0A82-4A9C-8334-F13D0DF2F472/RAs.vsdx
[er-circuit-parameters]: https://github.com/mspnp/reference-architectures/tree/master/guidance-hybrid-network-er/parameters/expressRouteCircuit.parameters.json
[azure-powershell-download]: https://azure.microsoft.com/documentation/articles/powershell-install-configure/
[azure-cli]: https://azure.microsoft.com/documentation/articles/xplat-cli-install/
[0]: ./media/guidance-hybrid-network-expressroute/figure1.png "Hybrid netværksarkitektur ved hjælp af Azure ExpressRoute"
[1]: ./media/guidance-hybrid-network-expressroute/figure2.png "Brug af overflødige routere med ExpressRoute primære og sekundære kredsløb"
[2]: ./media/guidance-hybrid-network-expressroute/figure3.png "Tilføje sikkerhedsenheder til det lokale netværk"
[3]: ./media/guidance-hybrid-network-expressroute/figure4.png "Ved hjælp af tvunget tunnelføring for at overvåge internetbaserede trafik"
[4]: ./media/guidance-hybrid-network-expressroute/figure5.png "Finde ServiceKey af et ExpressRoute kredsløb"
