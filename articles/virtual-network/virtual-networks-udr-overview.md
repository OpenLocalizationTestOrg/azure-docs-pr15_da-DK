<properties 
   pageTitle="Hvad er brugerdefineret omdirigerer og IP-videresendelse?"
   description="Lær at bruge bruger defineret omdirigerer (UDR) og IP-videresendelse til at videresende trafik til netværket virtuel udstyr i Azure."
   services="virtual-network"
   documentationCenter="na"
   authors="jimdial"
   manager="carmonm"
   editor="tysonn" />
<tags 
   ms.service="virtual-network"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="03/15/2016"
   ms.author="jdial" />

# <a name="what-are-user-defined-routes-and-ip-forwarding"></a>Hvad er brugerdefineret omdirigerer og IP-videresendelse?
Når du føjer virtuelle maskiner (VM'er) til et virtuelt netværk (VNet) i Azure, vil du bemærke, FOS er i stand til at kommunikere med hinanden via netværket, automatisk. Du behøver ikke at angive en gateway, selvom FOS findes i forskellige undernet. Gælder også for kommunikation fra FOS til det offentlige Internet, og endda til dit lokale netværk, når en hybrid forbindelse fra Azure til din egen datacenter findes.

Dette flow af kommunikation er muligt, fordi Azure bruger en række system omdirigerer til at definere, hvordan IP-trafik flyder. System omdirigerer Kontrolflow kommunikation i følgende situationer:

- Fra i det samme undernet.
- Fra et undernet til en anden inden for en VNet.
- Fra FOS til internettet.
- Fra en VNet til en anden VNet gennem en VPN-gateway.
- Fra en VNet til dit lokale netværk via en VPN-gateway.

I nedenstående illustration vises en simpel opsætning med en VNet, to undernet og et par FOS sammen med de system omdirigerer, der tillader IP-trafik til dataflow.

![System omdirigerer i Azure](./media/virtual-networks-udr-overview/Figure1.png)

Selvom brugen af system omdirigerer letter trafik automatisk til din installation, er der tilfælde, hvor du vil styre routing af pakker via et virtuelt maskinen. Du kan så ved at oprette brugerdefinerede omdirigerer, der angiver det næste hop for pakker, der flyder til et bestemt undernet at gå til din virtuelle maskinen i stedet og aktivere videresendelse af IP-adresse for VM kører som virtuelle maskinen.

I nedenstående figur viser et eksempel på brugerdefinerede omdirigerer og IP-videresendelse tvinge pakker sendes til ét undernet fra en anden for at gå gennem et virtuelt maskinen på et tredje undernet.

![System omdirigerer i Azure](./media/virtual-networks-udr-overview/Figure2.png)

>[AZURE.IMPORTANT] Brugerdefinerede omdirigerer anvendes kun på trafik fra et undernet. Du kan ikke oprette omdirigerer for at angive, hvordan trafik kommer til et undernet fra internettet, f.eks. Desuden må ikke maskinen du videresender trafik til være i det samme undernet, hvor trafikken kommer. Opret altid et separat undernet for din udstyr. 

## <a name="route-resource"></a>Distribuere ressource
Pakker distribueres via et TCP/IP-netværk, der er baseret på en distribution tabel, der er defineret på hver node på det fysiske netværk. En distribution tabel er en samling af individuelle omdirigerer bruges til at beslutte, hvor du vil videresende pakker, der er baseret på destinationens IP-adresse. En rute består af følgende:

|Egenskaben|Beskrivelse|Begrænsninger|Overvejelser i forbindelse med|
|---|---|---|---|
| Adressepræfiks | Den destination CIDR, som gælder ruten som 10.1.0.0/16.|Skal være et gyldigt CIDR område, der repræsenterer adresser på offentlige Internet, Azure virtuelt netværk eller lokalt datacenter.|Kontrollér, at **adressepræfiks** ikke indeholder adressen til den **næste hop-adresse**, ellers din pakker angives i en løkke, der går fra kilden til det næste hop uden overhovedet at nå destinationen. |
| Næste hop type | Typen Azure hop pakken skal sendes til. | Skal være en af følgende værdier: <br/> **Virtuelt netværk**. Repræsenterer det lokale virtuelle netværk. Eksempelvis hvis du har to undernet, 10.1.0.0/16 og 10.2.0.0/16 i det samme virtuelle netværk, har ruten for hvert undernet i rutetabellen *Virtuelt netværk*næste hop værdien. <br/> **Virtuelt netværksgateway**. Repræsenterer en VPN-Gateway, Azure S2S. <br/> **Internet**. Repræsenterer standard Internet gatewayen fra Azure-infrastruktur. <br/> **Virtuel maskinen**. Repræsenterer et virtuelt maskinen, du har føjet til din Azure virtuelt netværk. <br/> **Ingen**. Repræsenterer et sort hul. Pakker videresendt til et sort hul viderestilles ikke overhovedet.| Overvej at bruge en **ingen** type stoppe pakker fra der flyder til en given destination. | 
| Næste hop-adresse | Den næste hopadresse indeholder pakker skal videresendes til IP-adressen. Næste hop værdier er kun tilladt i omdirigerer, hvor den næste hop type er *Virtuelle maskinen*.| Skal være en IP-adresse, der kan nås inden for det virtuelle netværk, hvor den brugerdefinerede rute er anvendt. | Hvis IP-adressen repræsenterer en VM, skal du aktivere [viderestilling af IP-adresse](#IP-forwarding) i Azure af VM. |

Nogle af værdierne, der "NextHopType" har forskellige navne i Azure PowerShell:
- Virtuelt netværk er VnetLocal
- Virtuelt netværk gatewayen er VirtualNetworkGateway
- Virtuel maskinen er VirtualAppliance
- Internet er Internet
- Ingen er ingen

### <a name="system-routes"></a>System omdirigerer
Hver undernet, der er oprettet i et virtuelt netværk er automatisk knyttet til en distribution tabel, der indeholder følgende system distribuere regler:

- **Lokal Vnet regel**: denne regel oprettes automatisk til hver undernet i et virtuelt netværk. Det angiver, at der er et direkte link mellem FOS i VNet, og der ikke er nogen mellemliggende næste hop.
- **Lokal regel**: denne regel gælder for al trafik bestemt til adresseområde lokalt og bruger VPN-gateway, som den næste hop destination.
- **Internet regel**: denne regel håndtag al trafik bestemt til det offentlige Internet (adresse præfiks 0.0.0.0/0) og bruger gatewayen infrastruktur internet som det næste trin for al trafik bestemt til internettet.

### <a name="user-defined-routes"></a>Brugerdefinerede omdirigerer
For de fleste miljøer skal du kun de system omdirigerer allerede er defineret af Azure. Dog kan du vil oprette en distribution tabel og tilføje en eller flere omdirigerer i visse tilfælde kan f.eks.:

- Tving tunnel til internettet via dit lokale netværk.
- Brug af virtuelle udstyr i dit miljø, Azure.

I de tilfælde ovenfor, er du nødt til at oprette en distribution tabel og tilføje brugerdefinerede omdirigerer til den. Du kan have flere distribuere tabeller, og den samme rutetabel kan knyttes til en eller flere undernet. Og hvert undernet kan kun knyttes til en enkelt rutetabel. Alle FOS og skytjenester i en undernet Brug rutetabellen der er knyttet til undernettet.

Undernet er afhængige af system omdirigerer indtil en distribution tabel er knyttet til undernettet. Når der findes en tilknytning, kan du gøre baseret på længste præfiks Match (LPM) mellem både brugerdefinerede omdirigerer og system omdirigerer routing. Hvis der er mere end én rute med samme LPM matcher er derefter en rute markeret ud fra dets origin i følgende rækkefølge:

1. Bruger definerede rute
1. BGP rute (når ExpressRoute bruges)
1. System rute

Få at vide, hvordan du opretter brugerdefinerede omdirigerer, se, [hvordan du opretter omdirigerer og aktivere IP-videresendelse i Azure](virtual-network-create-udr-arm-template.md).

>[AZURE.IMPORTANT] Brugerdefinerede omdirigerer anvendes kun til Azure FOS og cloud services. Eksempelvis hvis du vil tilføje en firewall virtuelle maskinen mellem dit lokale netværk og Azure, har du oprette en brugerdefineret rute til dine Azure distribuere tabeller, der videresender al trafik, gå til det lokale adresseområde til virtuelle maskinen. Du kan også føje en brugerdefineret rute (UDR) til GatewaySubnet for at videresende alle trafik fra det lokale til Azure gennem virtuelle maskinen. Dette er en seneste tilføjelse.

### <a name="bgp-routes"></a>BGP omdirigerer
Hvis du har en ExpressRoute forbindelse mellem dit lokale netværk og Azure, kan du aktivere BGP at sprede omdirigerer fra dit lokale netværk til Azure. Disse BGP omdirigerer bruges på samme måde som system omdirigerer og brugerdefinerede omdirigerer i hvert Azure undernet. Flere oplysninger under [ExpressRoute Introduktion](../expressroute/expressroute-introduction.md).

>[AZURE.IMPORTANT] Du kan konfigurere din Azure miljø for at bruge kraft tunnelføring via dit lokale netværk ved at oprette en bruger definerede rute for undernet 0.0.0.0/0, der bruger VPN-gatewayen, som det næste trin. Men det fungerer kun, hvis du bruger en VPN-gateway, ikke ExpressRoute. For ExpressRoute konfigureres tvunget tunnelføring gennem BGP.

## <a name="ip-forwarding"></a>IP-videresendelse
Som beskrevet ovenfor, er en af de vigtigste grunde til at oprette en bruger definerede rute at videresende trafik til et virtuelt maskinen. Et virtuelt maskinen er ikke noget mere end en VM, der kører et program, der bruges til at håndtere netværkstrafik på en måde, som en firewall eller en NAT-enhed.

Denne virtuelle maskinen VM skal kunne modtage indgående trafik, der ikke er adresseret til sig selv. For at tillade en VM til at modtage trafik til andre destinationer, skal du aktivere IP-videresendelse af VM. Dette er en Azure konfiguration, ikke en indstilling i gæst-operativsystemet.

## <a name="next-steps"></a>Næste trin

- Lær, hvordan du [opretter omdirigerer i implementeringsmodel ressourcestyring](virtual-network-create-udr-arm-template.md) og knytte dem til undernet. 
- Lær, hvordan du [opretter omdirigerer i modellen Klassisk installation](virtual-network-create-udr-classic-ps.md) og knytte dem til undernet.
