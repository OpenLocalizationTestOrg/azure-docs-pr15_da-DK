<properties 
   pageTitle="Fejlfinding i forbindelse med omdirigerer - Portal | Microsoft Azure"
   description="Lær, hvordan du udfører fejlfinding af omdirigerer i Azure ressourcestyring installation modellen ved hjælp af portalen Azure."
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

# <a name="troubleshoot-routes-using-the-azure-portal"></a>Fejlfinding i forbindelse med omdirigerer ved hjælp af portalen Azure

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

1. Log på portalen Azure på https://portal.azure.com.
2. Klik på **flere tjenester**, og klik derefter på **virtuelle maskiner** i den viste liste.
3. Vælg en VM at foretage fejlfinding af på listen, der vises, og en VM blade med indstillinger vises.
4. Klik på **Diagnosticer & løse problemer med** og derefter vælge et almindeligt problem. I dette eksempel skal er **jeg kan ikke oprette forbindelse til min Windows VM** markeret. 

    ![](./media/virtual-network-routes-troubleshoot-portal/image1.png)

5. Trin vises under problemet, som vist i billedet nedenfor: 

    ![](./media/virtual-network-routes-troubleshoot-portal/image2.png)

    Klik på *effektive omdirigerer* på listen over anbefalede trin.

6. Bladet **effektive omdirigerer** vises, som vist i billedet nedenfor:

    ![](./media/virtual-network-routes-troubleshoot-portal/image3.png)

    Hvis din VM indeholder kun én NIC, markeres den som standard. Hvis du har mere end én NIC, Vælg den NIC, som du vil have vist de effektive omdirigerer.

    >[AZURE.NOTE] Hvis der er knyttet til NIC VM ikke er kører, bliver ikke vist effektive omdirigerer. Kun de første 200 effektive omdirigerer er vist i portalen. Klik på **Hent**for den komplette liste. Du kan filtrere yderligere på siden resultater fra hentede .csv-filen.

    Bemærk følgende i output:
    - **Kilde**: Angiver typen distribution. System omdirigerer vises som *standard*, UDRs vises som *bruger* og gatewayen omdirigerer (statisk eller BGP) vises som *VPNGateway*.
    - **Tilstand**: Angiver tilstanden for effektive ruten. Mulige værdier er *aktiv* eller *ugyldige*.
    - **AddressPrefixes**: Angiver adressepræfikset af effektive ruten i CIDR notation. 
    - **nextHopType**: Angiver det næste hop for den angivne rute. Mulige værdier er *VirtualAppliance*, *Internet*, *VNetLocal*, *VNetPeering*eller *Null*. En værdi af *Null-værdier* for **nextHopType** i en UDR kan skyldes en ugyldig rute. Eksempelvis hvis **nextHopType** er *VirtualAppliance* og netværket virtuel er maskinen VM ikke i tilstanden klargjort/kører. Hvis **nextHopType** er *VPNGateway* , og der er ingen gateway klargjort/kører i den angivne VNet, kan ruten blive ugyldige.
    
7. Der er ingen rute, der er angivet til *WestUS VNET3* VNet (præfiks 10.10.0.0/16) fra den *WestUS VNet1* (præfiks 10.9.0.0/16) på billedet i ovenstående trin. I det følgende billede er linket peering i tilstanden *afbrudt* :
    
    ![](./media/virtual-network-routes-troubleshoot-portal/image4.png)

    Tovejs-linket til den peering er brudt, som forklarer, hvorfor VM1 kunne ikke oprettes forbindelse til VM3 i *WestUS VNet3* VNet.

8. Følgende billede viser omdirigerer, når du har oprettet linket tovejs-peering:

    ![](./media/virtual-network-routes-troubleshoot-portal/image5.png)

Læs afsnittet [overvejelser i forbindelse med](virtual-network-routes-troubleshoot-portal.md#Considerations) i denne artikel for yderligere fejlfinding scenarier for tvunget-tunnelføring og distribuere evaluering.

### <a name="view-effective-routes-for-a-network-interface"></a>Vis effektive omdirigerer til et netværksgrænseflade

Hvis der påvirkes netværk trafikken til et bestemt netværkskort (NIC), kan du se en komplet liste over effektive omdirigerer på et NIC direkte. For at se de samlede omdirigerer, der gælder for en NIC skal du benytte følgende fremgangsmåde:

1. Log på portalen Azure på https://portal.azure.com.
2. Klik på **flere tjenester**, og klik derefter på **netværksgrænseflader**
3. Søge på listen over for navnet på et NIC, eller Vælg den på listen, der vises. I dette eksempel er **VM1 NIC1** markeret.
4. Vælg **effektive omdirigerer** i bladet **grænsefladen netværk** , som vist i billedet nedenfor:
   
    ![](./media/virtual-network-routes-troubleshoot-portal/image6.png)

    Som standard **omfang** netværksgrænsefladen markeret.

    ![](./media/virtual-network-routes-troubleshoot-portal/image7.png)


### <a name="view-effective-routes-for-a-route-table"></a>Vis effektive omdirigerer for en distribution tabel

Når du ændrer brugerdefinerede omdirigerer (UDRs) i en distribution tabel, kan du gennemse gennemslagskraft omdirigerer føjes på en bestemt VM. En distribution tabel kan være knyttet til en hvilken som helst antal undernet. Du kan nu få vist alle de effektive omdirigerer for alle de netværkskort, en given rutetabel der anvendes på, uden at skulle skifte kontekst fra bladet given rute tabel.

I dette eksempel skal angives et UDR (*UDRoute*) i en distribution tabel (*UDRouteTable*). Dette sender alle internettrafik fra *Subnet1* i VNet *WestUS VNet1* via et netværk virtuelle maskinen (for) i *Subnet2* af den samme VNet. Ruten er vist i billedet nedenfor:

![](./media/virtual-network-routes-troubleshoot-portal/image8.png)

For at se de forskellige Sammenlæg muligheder for en distribution tabel skal du benytte følgende fremgangsmåde:

1. Log på portalen Azure på https://portal.azure.com.
2. Klik på **flere tjenester**, og klik derefter på **rute tabeller**
3. Søge på listen til rutetabellen, du vil se Sammenlæg omdirigerer til og vælge den. I dette eksempel er **UDRouteTable** markeret. En blade for den valgte rutetabel vises, som vist i billedet nedenfor:

    ![](./media/virtual-network-routes-troubleshoot-portal/image9.png)

4. Vælg **Effektive omdirigerer** i bladet **distribuere tabel** . **Omfang** er indstillet til rutetabellen, du har valgt.
5. En distribution tabel kan anvendes til flere undernet. Vælg det **undernet** , du vil have vist på listen. I dette eksempel er **Subnet1** markeret.
6. Vælg en **netværksgrænsefladen**. Alle netværkskort, der er forbundet til det markerede undernet er angivet. I dette eksempel er **VM1 NIC1** markeret.

    ![](./media/virtual-network-routes-troubleshoot-portal/image10.png)

    >[AZURE.NOTE] Hvis NIC ikke er knyttet til en igangværende VM, vises ingen effektive omdirigerer.

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
    - Sikkerhedsgruppe netværk (NSG) regler har der indflydelse på trafik-flow. Få mere at vide artiklen [Foretage fejlfinding af netværk sikkerhedsgrupper](virtual-network-nsg-troubleshoot-portal.md) .
