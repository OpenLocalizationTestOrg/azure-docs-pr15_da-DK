<properties
   pageTitle="Oprette VNet Peering ved hjælp af portalen Azure | Microsoft Azure"
   description="Få mere at vide, hvordan du opretter et virtuelt netværk ved hjælp af portalen Azure i ressourcestyring."
   services="virtual-network"
   documentationCenter=""
   authors="NarayanAnnamalai"
   manager="jefco"
   editor=""
   tags="azure-resource-manager"/>

<tags
   ms.service="virtual-network"
   ms.devlang="na"
   ms.topic="hero-article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="09/14/2016"
   ms.author="narayanannamalai;annahar"/>

# <a name="create-a-virtual-network-peering-using-the-azure-portal"></a>Oprette et virtuelt netværk peering ved hjælp af portalen Azure

[AZURE.INCLUDE [virtual-networks-create-vnet-selectors-arm-include](../../includes/virtual-networks-create-vnetpeering-selectors-arm-include.md)]

[AZURE.INCLUDE [virtual-networks-create-vnet-intro](../../includes/virtual-networks-create-vnetpeering-intro-include.md)]

[AZURE.INCLUDE [virtual-networks-create-vnet-scenario-basic-include](../../includes/virtual-networks-create-vnetpeering-scenario-basic-include.md)]

Hvis du vil oprette en VNet peering baseret på dette scenario over ved hjælp af portalen Azure, skal du følge nedenstående trin.

1. Gå til http://portal.azure.com fra en browser, og hvis det er nødvendigt, kan du logge på med din Azure-konto.
2. For at oprette VNET peering, skal du oprette to links, en for hver retning mellem to VNets. Du kan oprette VNET peering link til VNET1 til VNET2 først. På portalen, skal du klikke på **Gennemse** > **Vælg virtuelle netværk**

    ![Oprette VNet peering Azure-portalen](./media/virtual-networks-create-vnetpeering-arm-portal/figure01.png)

3. Vælg VNET1 i virtuelle netværk blade, skal du klikke på Peerings og derefter klikke på Tilføj

    ![Vælg peering](./media/virtual-networks-create-vnetpeering-arm-portal/figure02.png)

4. I bladet tilføje Peering navngive peering link LinkToVnet2, Vælg abonnementet peer virtuelle netværk VNET2, og klik på OK.

    ![Link til VNet](./media/virtual-networks-create-vnetpeering-arm-portal/figure03.png)

5. Når dette VNET peering link er oprettet. Du kan se linket statussen som følgende:

    ![Hyperlinket tilstand](./media/virtual-networks-create-vnetpeering-arm-portal/figure04.png)

6. Næste oprette linket VNET peering for VNET2 til VNET1. Vælg VNET2 i virtuelle netværk blade, skal du klikke på Peerings og derefter klikke på Tilføj

    ![Peer fra andre VNet](./media/virtual-networks-create-vnetpeering-arm-portal/figure05.png)

7. Navngiv peering link LinkToVnet1 i bladet tilføje Peering, Vælg abonnementet og peer virtuelt netværk, skal du klikke på OK.

    ![Oprette virtuelle netværk felt](./media/virtual-networks-create-vnetpeering-arm-portal/figure06.png)

8. Når dette VNET peering link er oprettet. Du kan se linket statussen som følgende:

    ![Endelige hyperlinket tilstand](./media/virtual-networks-create-vnetpeering-arm-portal/figure07.png)

9. Kontrollere status for LinkToVnet2 og den ændres nu til tilsluttet samt.  

    ![Endelige hyperlinket tilstand 2](./media/virtual-networks-create-vnetpeering-arm-portal/figure08.png)

    > [AZURE.NOTE] VNET peering oprettes kun, hvis begge links har forbindelse.

Der er et par kan konfigureres egenskaberne for hvert link:

|Indstillingen|Beskrivelse|Standard|
|:-----|:----------|:------|
|AllowVirtualNetworkAccess|Om en adresse for Peer VNet der skal medtages som en del af mærket Virtual_network|Ja|
|AllowForwardedTraffic|Om trafik ikke stammer fra en peered VNet accepteret eller afvist|Nej|
|AllowGatewayTransit|Gør det muligt for peer VNet at bruge din VNet gateway|Nej|
|UseRemoteGateways|Brug din peer VNet gateway. Peer VNet skal have en gateway, der er konfigureret og AllowGatewayTransit er markeret. Du kan ikke bruge denne indstilling, hvis du har en gateway, der er konfigureret|Nej|

Hvert link i VNet peering har en række over egenskaber. Fra portalen, kan du klikke på linket VNet Peering og ændre tilgængelige parametre, på Gem for at få effekten til at ændre.

[AZURE.INCLUDE [virtual-networks-create-vnet-scenario-crosssub-include](../../includes/virtual-networks-create-vnetpeering-scenario-crosssub-include.md)]

1. Gå til http://portal.azure.com fra en browser, og hvis det er nødvendigt, kan du logge på med din Azure-konto.
2. I dette eksempel bruger vi to abonnementer A og B og to brugere BrugerA og UserB med rettigheder i abonnementerne henholdsvis
3. På portalen, klik på Gennemse, vælge virtuelle netværk. Klik på VNET, og klik på Tilføj.

    ![Scenarie 2 Gennemse](./media/virtual-networks-create-vnetpeering-arm-portal/figure09.png)

4. Klik på Tilføj access blade, Vælg en rolle, og vælg netværk bidragyder, skal du klikke på Tilføj brugere, Skriv UserB tegn i navnet og klikke på OK.

    ![RBAC](./media/virtual-networks-create-vnetpeering-arm-portal/figure10.png)

    Dette er ikke et krav peering kan oprettes, selvom brugere hæve individuelt peering anmodninger til deres respektive Vnets, som svarer til anmodningerne. Tilføje privilegerede brugere af de andre VNet som brugere i den lokale VNet gør det nemmere at installationen er i portalen.

5. Derefter at logge på Azure-portalen med UserB, som er rettighed bruger til SubscriptionB. Følge ovenstående trin for at tilføje BrugerA som netværk bidragyder.

    ![RBAC2](./media/virtual-networks-create-vnetpeering-arm-portal/figure11.png)

    > [AZURE.NOTE] Du kan logge af og logge på begge brugersessioner i browser for at sikre, at tilladelsen er aktiveret korrekt.

6. Log på portalen som BrugerA, gå til bladet VNET3 skal du klikke på Peering, kontrollere ' jeg, om min ressource-ID "afkrydsningsfelt og type ressourcen ID for VNET5 i under format.

    / subscriptions/{SubscriptionID}/resourceGroups/{ResourceGroupName}/providers/Microsoft.Network/VirtualNetwork/{VNETname}

    ![Ressource-ID](./media/virtual-networks-create-vnetpeering-arm-portal/figure12.png)

7. Log på portalen som UserB og følge ovenstående trin til at oprette peering link fra VNET5 til VNet3.

    ![Ressource-ID 2](./media/virtual-networks-create-vnetpeering-arm-portal/figure13.png)

8. Peering oprettes og en hvilken som helst virtuel maskine i VNet3 bør kunne kommunikere med en hvilken som helst virtuel maskine i VNet5

[AZURE.INCLUDE [virtual-networks-create-vnet-scenario-transit-include](../../includes/virtual-networks-create-vnetpeering-scenario-transit-include.md)]

1. Som et første skridt, VNET peering links fra HubVnet til VNET1. Bemærk, at Tillad videresendt trafik indstilling ikke er markeret til linket.

    ![Grundlæggende Peering](./media/virtual-networks-create-vnetpeering-arm-portal/figure14.png)

2. Peering links fra VNET1 til HubVnet kan oprettes som et næste trin. Bemærk, at Tillad videresendt trafik indstilling er markeret.

    ![Grundlæggende Peering](./media/virtual-networks-create-vnetpeering-arm-portal/figure15a.png)

3. Når peering er oprettet, kan du referere til denne [artikel](virtual-network-create-udr-arm-ps.md) og definere brugerdefineret Route(UDR) for at omdirigere VNet1 trafik via et virtuelt maskinen at bruge dens egenskaber. Når du angiver den næste Hop adresse i omdirigere, kan du angive den til virtuelle maskinen i peer VNet HubVNet IP-adresse


[AZURE.INCLUDE [virtual-networks-create-vnet-scenario-asmtoarm-include](../../includes/virtual-networks-create-vnetpeering-scenario-asmtoarm-include.md)]



1. Gå til http://portal.azure.com fra en browser, og hvis det er nødvendigt, kan du logge på med din Azure-konto.

2. For at oprette VNET peering i dette scenarie, skal du oprette kun et link fra det virtuelle netværk i Azure ressourcestyring til ordet i klassisk. Det vil sige, fra **VNET1** til **VNET2**. På portalen, skal du klikke på **Gennemse** > Vælg **Virtuelle netværk**

3. Vælg **VNET1**i bladet virtuelle netværk. Klik på **Peerings**, og derefter klikke på **Tilføj**.

4. Navngive dit link i bladet tilføje Peering. Her er den hedder **LinkToVNet2**. Marker **Klassisk**under Peer detaljer.

5. Klik på abonnementet og peer virtuelt netværk **VNET2**. Klik derefter på OK.

    ![Sammenkæde Vnet1 med Vnet 2](./media/virtual-networks-create-vnetpeering-arm-portal/figure18.png)

6. Når dette VNet peering link er oprettet, er peered to virtuelle netværk, og du vil kunne se følgende:

    ![Kontrol peering forbindelsen](./media/virtual-networks-create-vnetpeering-arm-portal/figure19.png)


## <a name="remove-vnet-peering"></a>Fjerne VNet Peering

1.  Gå til http://portal.azure.com fra en browser, og hvis det er nødvendigt, kan du logge på med din Azure-konto.
2.  Gå til virtuelt netværk blade, skal du klikke på Peerings, klik på det Link, du vil fjerne, skal du klikke på knappen Slet.

    ![Delete1](./media/virtual-networks-create-vnetpeering-arm-portal/figure15.png)

3. Når du fjerner et link i VNET peering, går tilstanden peer link til afbrudt.

    ![Delete2](./media/virtual-networks-create-vnetpeering-arm-portal/figure16.png)

4. I denne tilstand, ikke kan du genoprette linket indtil tilstanden peer link ændres til startet. Vi anbefaler, at du fjerner begge linkene, før du genskabe VNET peering.
