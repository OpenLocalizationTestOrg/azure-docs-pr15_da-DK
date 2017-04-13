## <a name="service-chaining---transit-through-peered-vnet"></a>Tjenesten sammenkædning - forsendelse gennem peered VNet

Selvom brugen af system omdirigerer letter trafik automatisk til din installation, er der tilfælde, hvor du vil styre routing af pakker via et virtuelt maskinen.
I dette scenarie er der to VNets i et abonnement, HubVNet og VNet1 som beskrevet i diagrammet nedenfor. Du kan installere netværk virtuelle Appliance(NVA) i VNet HubVNet. Når du har oprettet VNet peering mellem HubVNet og VNet1, kan du konfigurere brugerdefineret omdirigerer og angive det næste trin at for i HubVNet.

![FOR overførsel](./media/virtual-networks-create-vnetpeering-scenario-transit-include/figure01.PNG)

> [AZURE.NOTE] Brug, forudsætter alle VNets her er i samme-abonnement. Men det fungerer også i tværs abonnement scenario.

Den vigtige egenskab til at aktivere overførsel distributionen er parameteren "Tillad videresendt trafik". Dette kan acceptere og sende trafik fra/til for i peered VNet.  
