<properties
    pageTitle="Hvordan du planlægger dit virtuelle netværk for Azure RemoteApp af websteder | Microsoft Azure"
    description="Få mere at vide om at planlægge din virtuelt netværk for Azure RemoteApp af websteder."
    services="remoteapp"
    documentationCenter="" 
    authors="mghosh1616"
    manager="mbaldwin" />

<tags
    ms.service="remoteapp"
    ms.workload="compute"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/15/2016"
    ms.author="elizapo" />

# <a name="how-to-plan-your-virtual-network-for-azure-remoteapp"></a>Hvordan du planlægger dit virtuelle netværk til Azure RemoteApp

> [AZURE.IMPORTANT]
> Azure RemoteApp er ved at blive nedlagt. Læs [meddelelsen](https://go.microsoft.com/fwlink/?linkid=821148) for at få mere at vide.

Dette dokument beskriver, hvordan du konfigurerer din Azure virtuelt netværk (VNET) og undernettet til Azure RemoteApp. Hvis du har kendskab til Azure virtuelle netværk, er dette en funktion, der hjælper dig med at virtualisere din netværksinfrastruktur i skyen og til at oprette hybrid løsninger med Azure og dine lokale ressourcer. Du kan læse mere om det [her](../virtual-network/virtual-networks-overview.md).

Hvis du vil definere sikkerhedspolitikker for trafik (både indgående og udgående) i dit virtuelle netværk, hvor du installerer RemoteApp Azure, anbefales det opretter et separat undernet til Azure RemoteApp fra resten af dine installationer i det Azure virtuelle netværk. Du kan finde flere oplysninger om, hvordan du definerer sikkerhedspolitikker på dit undernet, Azure virtuelt netværk, skal du læse [Hvad er et netværk sikkerhed gruppe (NSG)?](../virtual-network/virtual-networks-nsg.md).

## <a name="types-of-azure-remoteapp-collections-with-azure-virtual-networks"></a>Typer af websteder Azure RemoteApp med Azure virtuelle netværk

På følgende grafik viser indstillingerne for to forskellige af websteder, når du vil bruge et virtuelt netværk.

### <a name="azure-remoteapp-cloud-collection-with-vnet"></a>Azure RemoteApp skyen af websteder med VNET

 ![Azure RemoteApp - skyen af websteder med en VNET](./media/remoteapp-planvpn/ra-cloudvpn.png)

Dette repræsenterer en Azure RemoteApp samling, hvor alle de ressourcer, der RemoteApp sessionen hosts skal have adgang til er implementeret i Azure. De kan være i den samme VNET som RemoteApp VNET eller en anden VNET i Azure.

### <a name="azure-remoteapp-hybrid-collection-with-vnet"></a>Azure RemoteApp hybrid af websteder med VNET

![Azure RemoteApp - Hybrid af websteder med en VNET](./media/remoteapp-planvpn/ra-hybridvpn.png)

Dette repræsenterer en Azure RemoteApp samling, hvor nogle af de ressourcer, der har brug for at få adgang til RemoteApp session hosts er udløst lokalt. RemoteApp VNET er knyttet til den lokale netværk ved hjælp af Azure hybride teknologier som websted til websted VPN- eller Express distribution.


## <a name="how-the-system-works"></a>Sådan fungerer systemet

Under overfladen installerer Azure RemoteApp Azure virtuelle maskiner (med dine overførte billede) på det virtuelle netværksundernet, som du har valgt under klargøringen. Hvis du har valgt for en hybrid af websteder, forsøger vi at løse fulde domænenavn fra domænecontrolleren du har angivet i arbejdsprocessen for klargøring med den DNS-server, der er angivet i det virtuelle netværk.  
Hvis du opretter forbindelse til et eksisterende virtuelt netværk, skal du sørge for at få vist de nødvendige porte i dit netværk sikkerhedsgrupper i dit undernet, Azure RemoteApp. 

Vi anbefaler, at du bruger et [stort nok undernet til Azure RemoteApp](remoteapp-vnetsizing.md). Den største understøttes af Azure virtuelt netværk er /8 (ved hjælp af CIDR undernetdefinitioner). Dit undernet skal være stort nok til at medtage alle Azure RemoteApp FOS under skalere op, når flere brugere får adgang til apps. 

Følgende er de ting, du skal aktivere på dit undernet, virtuelt netværk: 

2.  Udgående trafik fra undernettet skal have lov af portområde 10101 10175 til at kommunikere med en af de interne Azure RemoteApp-tjenester.
3.  Udgående trafik skal have lov fra dit undernet til at oprette forbindelse til Azure-lager på port 443
4.  Hvis du har Active Directory, der er hostet i Azure, Sørg for en hvilken som helst VM inden for det virtuelle netværksundernet til Azure RemoteApp er kunne oprette forbindelse til pågældende domænenavn fra domænecontrolleren. DNS-Posterne i det virtuelle netværk bør kunne løse det fuldstændige Domænenavn for denne domænenavn fra domænecontrolleren.


## <a name="virtual-network-with-forced-tunneling"></a>Virtuelt netværk med tvunget tunnelføring

[Tvungen tunnelføring](../vpn-gateway/vpn-gateway-about-forced-tunneling.md) understøttes nu for alle nye Azure RemoteApp af websteder. Vi understøtter i øjeblikket ikke overførslen af en eksisterende af websteder til at understøtte tvunget tunnelføring.  Er du nødt til at slette alle eksisterende websteder ved hjælp af den VNET, som du opretter et link til Azure RemoteApp og oprette en ny for at få tvungen tunnelføring aktiveret på din af websteder. 
