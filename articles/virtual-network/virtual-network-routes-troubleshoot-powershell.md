<properties 
   pageTitle="Fejlfinding i forbindelse med omdirigerer - PowerShell | Microsoft Azure"
   description="Lær, hvordan du udfører fejlfinding af omdirigerer i Azure ressourcestyring installation modellen med Azure PowerShell."
   services="virtual-network"
   documentationCenter="na"
   authors="AnithaAdusumilli"
   manager="narayan"
   editor=""
   tags="azure-resource-manager"
/>
<tags 
   ms.service="virtual-network"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="09/23/2016"
   ms.author="anithaa" />

# <a name="troubleshoot-routes-using-azure-powershell"></a>Fejlfinding i forbindelse med omdirigerer ved hjælp af Azure PowerShell

> [AZURE.SELECTOR]
- [Azure-portalen](virtual-network-routes-troubleshoot-portal.md)
- [PowerShell](virtual-network-routes-troubleshoot-powershell.md)

Hvis du oplever problemer med netværksforbindelsen til eller fra din Azure VM (Virtual Machine), har omdirigerer der indflydelse på VM trafik-flow. I denne artikel indeholder en oversigt over diagnosticering egenskaber for omdirigerer til hjælp til fejlfinding af yderligere.

Distribuere tabeller er knyttet til undernet og er effektive på alle netværksgrænseflader (NIC) i pågældende undernet. Følgende typer omdirigerer kan anvendes på hver netværksgrænseflade:

- **System omdirigerer:** Som standard har alle undernet, der er oprettet i et virtuelt Azure-netværk (VNet) system distribuere tabeller, der tillader lokale VNet trafik, lokale trafik via VPN gateways og internettrafik. Der findes også system omdirigerer for peered VNets.
- **BGP omdirigerer:** Overføres til netværksgrænseflader gennem ExpressRoute eller websted til websted VPN-forbindelser. Lær mere om BGP routing ved at læse artikler [BGP med VPN gateways](../vpn-gateway/vpn-gateway-bgp-overview.md) og [ExpressRoute oversigt](../expressroute/expressroute-introduction.md) .
- **Brugerdefinerede omdirigerer (UDR):** Hvis du bruger netværk virtuelle anvendelser eller er tvunget-tunnelføring trafik til et lokalt netværk via en VPN til-websted, du skal muligvis brugerdefinerede omdirigerer (UDRs) der er knyttet til dit undernet distribuere tabel. Hvis du ikke kender UDRs, skal du læse artiklen [brugerdefinerede omdirigerer](virtual-networks-udr-overview.md#user-defined-routes) .

Med de forskellige omdirigerer, der kan anvendes på et netværksgrænseflade, kan det være svært at bestemme, hvilke Sammenlæg omdirigerer er effektive. Hjælp til fejlfinding af VM netværksforbindelse, kan du se alle de effektive forskellige muligheder for en netværksgrænseflade i implementeringsmodel Azure ressourcestyring.

## <a name="using-effective-routes-to-troubleshoot-vm-traffic-flow"></a>Brug af effektive omdirigerer til at foretage fejlfinding af VM trafikken

I denne artikel bruges i følgende scenario som et eksempel til at illustrere fejlfinding i forbindelse med de effektive forskellige muligheder for en netværkskort:

En VM (*VM1*), der er knyttet til VNet (*VNet1*, præfiks: 10.9.0.0/16) kan ikke oprette forbindelse til en VM(VM3) i et nyligt peered VNet (*VNet3*, præfiks 10.10.0.0/16). Der er ingen UDRs eller BGP dirigerer anvendt på VM1 NIC1 netværksgrænseflade forbundet til VM, kun system omdirigerer anvendes.

I denne artikel forklarer, hvordan du årsagen til forbindelsesfejl, ved hjælp af effektive omdirigerer egenskab i Azure ressourcestyring implementeringsmodel.
Mens i eksempel bruges kun system omdirigerer, kan de samme trin bruges til at bestemme mislykkede forsøg på indgående og udgående forbindelse via en hvilken som helst distribuere type.

>[AZURE.NOTE] Hvis din VM har mere end én NIC vedhæftet, skal du se effektive omdirigerer for hver af netværkskortene til at diagnosticere problemer med netværksforbindelsen til og fra en VM.

### <a name="view-effective-routes-for-a-virtual-machine"></a>Vis effektive omdirigerer til en virtuel maskine

For at se de samlede omdirigerer, der er anvendt til en VM skal du benytte følgende fremgangsmåde:

### <a name="view-effective-routes-for-a-network-interface"></a>Vis effektive omdirigerer til et netværksgrænseflade

For at se de samlede omdirigerer, der er anvendt til en netværksgrænseflade skal du benytte følgende fremgangsmåde:

1.  Starte en session med Azure PowerShell og logge på Azure. Hvis du ikke kender Azure PowerShell, skal du læse artiklen [Sådan installerer og konfigurerer Azure PowerShell](../powershell-install-configure.md) .

2.  Følgende kommando returnerer alle omdirigerer anvendt på et netværksgrænseflade med navnet *VM1 NIC1* i ressourcegruppen *RG1*.

        Get-AzureRmEffectiveRouteTable -NetworkInterfaceName VM1-NIC1 -ResourceGroupName RG1

    >[AZURE.TIP] Hvis du ikke kender navnet på et netværksgrænseflade, Skriv følgende kommando for at hente navnene på alle netværksgrænseflader i en ressource group.*

        Get-AzureRmNetworkInterface -ResourceGroupName RG1 | Format-Table Name

    I følgende eksempel ligner output for hver rute, der er anvendt til det undernet NIC er forbundet med:

        Name :
        State : Active
        AddressPrefix : {10.9.0.0/16}
        NextHopType : VNetLocal
        NextHopIpAddress : {}

        Name :
        State : Active
        AddressPrefix : {0.0.0.0/16}
        NextHopType : Internet
        NextHopIpAddress : {}

    Bemærk følgende i output:
    - **Navn**: navnet på den effektive rute kan være tom, medmindre eksplicit er angivet, til brugerdefinerede omdirigerer. 
    - **Tilstand**: Angiver tilstanden for effektive ruten. Mulige værdier er "Aktiv" eller "Ugyldig"
    - **AddressPrefixes**: Angiver adressepræfikset af effektive ruten i CIDR notation. 
    - **nextHopType**: Angiver det næste hop for den angivne rute. Mulige værdier er *VirtualAppliance*, *Internet*, *VNetLocal*, *VNetPeering*eller *Null*. En værdi af *Null-værdier* for **nextHopType** i en UDR kan skyldes en ugyldig rute. Eksempelvis hvis **nextHopType** er *VirtualAppliance* og netværket virtuel er maskinen VM ikke i tilstanden klargjort/kører. Hvis **nextHopType** er *VPNGateway* , og der er ingen gateway klargjort/kører i den angivne VNet, kan ruten blive ugyldige.
    - **NextHopIpAddress**: Angiver IP-adressen på det næste hop for effektive ruten.
    
    Følgende kommando returnerer omdirigerer i en nemmere at få vist tabel:

        Get-AzureRmEffectiveRouteTable -NetworkInterfaceName VM1-NIC1 -ResourceGroupName RG1 | Format-Table

    I følgende eksempel er nogle af output til dette scenario, der er beskrevet tidligere har modtaget:

        Name State AddressPrefix NextHopType NextHopIpAddress
        ---- ----- ------------- ----------- ----------------
        Active {10.9.0.0/16} VnetLocal {}
        Active {0.0.0.0/0} Internet {}
    

3. Der er ingen rute, der er angivet til *WestUS VNet3* VNet (præfiks 10.10.0.0/16)* * fra *WestUS VNet1* (præfiks 10.9.0.0/16) i output fra det forrige trin. Som vist i billedet nedenfor, er linket VNet peering med *WestUS VNet3* VNet i tilstanden *forbindelsen er afbrudt* .
    
    ![](./media/virtual-network-routes-troubleshoot-portal/image4.png)

    Tovejs-linket til den peering er brudt, som forklarer, hvorfor VM1 kunne ikke oprettes forbindelse til VM3 i *WestUS VNet3* VNet. Konfigurere et tovejs-VNet peering link igen til *WestUS VNet1* og *WestUS VNet3* VNets. Output returneres, når linket VNet peering er korrekt oprettet følger:

        Name State AddressPrefix NextHopType NextHopIpAddress
        ---- ----- ------------- ----------- ----------------
        Active {10.9.0.0/16} VnetLocal {}
        Active {10.10.0.0/16} VNetPeering {}
        Active {0.0.0.0/0} Internet {}
        
    Når du har afgjort problemet, kan du tilføje, fjerne, eller ændre omdirigerer og distribuere tabeller. Skriv følgende kommando for at se en liste over de kommandoer, der bruges til at gøre det:

        Get-Help *-AzureRmRouteConfig

## <a name="considerations"></a>Overvejelser i forbindelse med

Et par ting, du skal huske på, når du gennemser på listen over omdirigerer returneres:

- Routing er baseret på længste præfiks Match (LPM) mellem UDRs, BGP og system omdirigerer. Hvis der er mere end én rute med samme LPM matcher, er derefter en rute markeret ud fra dets origin i følgende rækkefølge:
    - Brugerdefinerede rute
    - BGP distribution
    - Distribuere system (standard)

    Du kan kun se effektive omdirigerer, der er LPM match baseret på alle tilgængelige omdirigerer med effektive omdirigerer. Ved at vise, hvordan omdirigerer faktisk evalueres for en given NIC, gør det det meget nemmere at foretage fejlfinding af bestemte omdirigerer, der har indflydelse på forbindelse til/fra din VM.

- Hvis du har UDRs og sender trafik til et netværk virtuelle maskinen (for), med *VirtualAppliance* som **nextHopType**, sikre, at IP-videresendelse er aktiveret på for modtager trafikken eller pakker udelades. 
- Hvis tvungen tunnelføring er aktiveret, vil alle udgående Internettrafik sendes til lokale. RDP/SSH fra internettet til dine VM fungerer muligvis ikke med denne indstilling, afhængigt af hvordan lokale håndterer denne trafik. 
  Tvungen-tunnelføring kan aktiveres:
    - Hvis bruger VPN til-websted, ved at angive en brugerdefineret rute (UDR) med nextHopType som VPN-Gateway
    - Hvis en standardrute annonceres over BGP
- For VNet peering trafik til at fungere korrekt, skal der være en system rute med **nextHopType** *VNetPeering* for den peered VNet præfiks område. Hvis en sådan rute ikke findes, og linket VNet peering ser OK:
    - Vent et par sekunder, og prøv igen, hvis det er et nyligt oprettet peering link. Nogle gange tager længere tid at sprede omdirigerer til alle netværksgrænseflader i et undernet.
    - Sikkerhedsgruppe netværk (NSG) regler har der indflydelse på trafik-flow. Få mere at vide artiklen [Foretage fejlfinding af netværk sikkerhedsgrupper](virtual-network-nsg-troubleshoot-powershell.md) .
