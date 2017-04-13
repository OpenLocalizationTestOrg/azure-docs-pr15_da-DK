<properties
   pageTitle="ExpressRoute fejlfinding vejledning: få ARP tabeller | Microsoft Azure"
   description="Denne side indeholder en vejledning til at hente ARP tabeller for et ExpressRoute kredsløb."
   documentationCenter="na"
   services="expressroute"
   authors="ganesr"
   manager="carolz"
   editor="tysonn"/>
<tags
   ms.service="expressroute"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="10/10/2016"
   ms.author="ganesr"/>

# <a name="expressroute-troubleshooting-guide-getting-arp-tables-in-the-classic-deployment-model"></a>ExpressRoute fejlfinding vejledning: få ARP tabeller i den klassiske implementeringsmodel

> [AZURE.SELECTOR]
[PowerShell - ressourcestyring](expressroute-troubleshooting-arp-resource-manager.md)
[PowerShell - klassisk](expressroute-troubleshooting-arp-classic.md)

I denne artikel fører dig gennem trinnene til at hente tabellerne opløsning ARP (Address Protocol) til din Azure ExpressRoute kredsløb.

>[AZURE.IMPORTANT] Dette dokument er beregnet på at hjælpe dig med at diagnosticere og løse problemer med enkelt. Det er ikke beregnet til at være en erstatning for Microsoft support. Hvis du ikke kan løse problemet ved hjælp af de følgende retningslinjer, kan du åbne en supportanmodning med [Hjælp til Microsoft Azure + support](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).

## <a name="address-resolution-protocol-arp-and-arp-tables"></a>Adresse (ARP opløsning Protocol) og ARP tabeller
ARP er en lag 2-protokol, der er defineret i [RFC 826](https://tools.ietf.org/html/rfc826). ARP bruges til at knytte en Ethernet-adresse (MAC-adressen) til en IP-adresse.

En ARP tabel indeholder en tilknytning af IPv4-adresse og MAC-adresse til en bestemt peering. Tabellen ARP for en ExpressRoute kredsløb peering indeholder følgende oplysninger for hver grænseflade (primære og sekundære):

1. Tilknytning af en lokal router interface IP-adresse til en MAC-adresse
2. Tilknytning af en ExpressRoute router interface IP-adresse til en MAC-adresse
3. Alderen på tilknytningen

ARP tabeller kan hjælpe med at godkende lag 2-konfigurationen og med fejlfinding af forbindelsesproblemer grundlæggende Layer 2.

Følgende er et eksempel på en ARP tabel:

        Age InterfaceProperty IpAddress  MacAddress    
        --- ----------------- ---------  ----------    
         10 On-Prem           10.0.0.1 ffff.eeee.dddd
          0 Microsoft         10.0.0.2 aaaa.bbbb.cccc


Følgende afsnit indeholder oplysninger om, hvordan du få vist de ARP tabeller, der kan ses af ExpressRoute kant routere.

## <a name="prerequisites-for-using-arp-tables"></a>Forudsætninger for ved hjælp af ARP tabeller

Sørg for, at du har følgende, før du fortsætter:

 - Et gyldigt ExpressRoute kredsløb, der er konfigureret med mindst én peering. Kredsløbet skal konfigureres fuldt af provideren connectivity. Du (eller udbyderen connectivity) skal konfigurere mindst én af peerings (Azure privat, Azure offentlige eller Microsoft) på dette kredsløb.

 - IP-adresseområder, der bruges til at konfigurere peerings (Azure privat, Azure offentlige og Microsoft). Gennemse IP-adresse tildeling eksemplerne i [ExpressRoute routing krav side](expressroute-routing.md) for at få en forståelse af, hvordan IP-adresser er tilknyttet grænseflader på din aise og i ExpressRoute side. Du kan få oplysninger om peering konfigurationen ved at gennemgå [ExpressRoute peering konfigurationssiden](expressroute-howto-routing-classic.md).

 - Oplysninger fra udbyderen af netværk team eller connectivity om MAC-adresser for de grænseflader, der bruges med disse IP-adresser.

 - Seneste Windows PowerShell-modulet til Azure (version 1,50 eller nyere).

## <a name="arp-tables-for-your-expressroute-circuit"></a>ARP tabeller for din ExpressRoute kredsløb
Dette afsnit indeholder oplysninger om, hvordan du får vist tabellerne ARP for hver type peering ved hjælp af PowerShell. Før du fortsætter, skal du eller udbyderen af forbindelse til at konfigurere den peering. Hvert kredsløb har to kurver (primære og sekundære). Du kan se tabellen ARP for hver sti uafhængigt af hinanden.

### <a name="arp-tables-for-azure-private-peering"></a>ARP tabeller til Azure privat peering
Følgende cmdlet indeholder ARP tabeller til Azure privat peering:

        # Required variables
        $ckt = "<your Service Key here>

        # ARP table for Azure private peering--primary path
        Get-AzureDedicatedCircuitPeeringArpInfo -ServiceKey $ckt -AccessType Private -Path Primary

        # ARP table for Azure private peering--secondary path
        Get-AzureDedicatedCircuitPeeringArpInfo -ServiceKey $ckt -AccessType Private -Path Secondary

Følgende er eksempel på output til en af stierne:

        Age InterfaceProperty IpAddress  MacAddress    
        --- ----------------- ---------  ----------    
         10 On-Prem           10.0.0.1 ffff.eeee.dddd
          0 Microsoft         10.0.0.2 aaaa.bbbb.cccc


### <a name="arp-tables-for-azure-public-peering"></a>ARP tabeller til Azure offentlige peering:
Følgende cmdlet indeholder ARP tabeller til Azure offentlige peering:

        # Required variables
        $ckt = "<your Service Key here>

        # ARP table for Azure public peering--primary path
        Get-AzureDedicatedCircuitPeeringArpInfo -ServiceKey $ckt -AccessType Public -Path Primary

        # ARP table for Azure public peering--secondary path
        Get-AzureDedicatedCircuitPeeringArpInfo -ServiceKey $ckt -AccessType Public -Path Secondary

Følgende er eksempel på output til en af stierne:

        Age InterfaceProperty IpAddress  MacAddress    
        --- ----------------- ---------  ----------    
         10 On-Prem           10.0.0.1 ffff.eeee.dddd
          0 Microsoft         10.0.0.2 aaaa.bbbb.cccc


Følgende er eksempel på output til en af stierne:

        Age InterfaceProperty IpAddress  MacAddress    
        --- ----------------- ---------  ----------    
         10 On-Prem           64.0.0.1 ffff.eeee.dddd
          0 Microsoft         64.0.0.2 aaaa.bbbb.cccc


### <a name="arp-tables-for-microsoft-peering"></a>ARP tabeller til Microsoft peering
Følgende cmdlet indeholder ARP tabeller til Microsoft peering:

    # ARP table for Microsoft peering--primary path
    Get-AzureDedicatedCircuitPeeringArpInfo -ServiceKey $ckt -AccessType Microsoft -Path Primary

    # ARP table for Microsoft peering--secondary path
    Get-AzureDedicatedCircuitPeeringArpInfo -ServiceKey $ckt -AccessType Microsoft -Path Secondary


Eksempel på output er vist nedenfor for et af stierne:

        Age InterfaceProperty IpAddress  MacAddress    
        --- ----------------- ---------  ----------    
         10 On-Prem           65.0.0.1 ffff.eeee.dddd
          0 Microsoft         65.0.0.2 aaaa.bbbb.cccc


## <a name="how-to-use-this-information"></a>Hvordan du bruger disse oplysninger
Tabellen ARP af en peering kan bruges til at validere lag 2-konfigurationen og forbindelse. Dette afsnit indeholder en oversigt over, hvordan ARP tabeller ser ud i forskellige scenarier.

### <a name="arp-table-when-a-circuit-is-in-an-operational-expected-state"></a>ARP tabel, når et kredsløb er i en funktionsdygtige (forventet) tilstand

 - Tabellen ARP har en post til den lokale side med en gyldig IP- og MAC-adresse og en tilsvarende post for Microsoft-side.
 - Den sidste oktet af den lokale IP-adresse er altid et ulige tal.
 - Den sidste oktet Microsoft IP-adresse er altid et lige tal.
 - Den samme MAC-adresse vises i Microsoft side for alle tre peerings (primære/sekundære).


        Age InterfaceProperty IpAddress  MacAddress    
        --- ----------------- ---------  ----------    
         10 On-Prem           65.0.0.1 ffff.eeee.dddd
          0 Microsoft         65.0.0.2 aaaa.bbbb.cccc

### <a name="arp-table-when-its-on-premises-or-when-the-connectivity-provider-side-has-problems"></a>ARP tabel, når det er i det lokale miljø, eller når connectivity udbyder side har problemer med

 Kun én post vises i tabellen ARP. Det viser tilknytningen mellem MAC-adressen og IP-adressen, der bruges i Microsoft-side.

        Age InterfaceProperty IpAddress  MacAddress    
        --- ----------------- ---------  ----------    
          0 Microsoft         65.0.0.2 aaaa.bbbb.cccc

>[AZURE.NOTE] Hvis du oplever et problem således, skal du åbne en supportanmodning med udbyderen forbindelse for at kunne løse problemet.


### <a name="arp-table-when-the-microsoft-side-has-problems"></a>ARP tabel, når Microsoft side har problemer med

 - Du kan ikke se en ARP-tabel, der vises for en peering, hvis der opstår problemer i Microsoft-side.
 -  Åbn en supportanmodning med [Hjælp til Microsoft Azure + support](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade). Angive, at du har et problem med Layer 2 connectivity.

## <a name="next-steps"></a>Næste trin

 - Validere Layer 3 konfigurationer for din ExpressRoute kredsløb:
     - Få en rute oversigt til at bestemme tilstand af BGP sessioner.
     - Få en distribution tabel til at bestemme, hvilke præfikser meddeles på tværs af ExpressRoute.
 - Valider dataoverførsel ved at gennemgå byte ind og ud.
 - Åbn en supportanmodning med [Hjælp til Microsoft Azure + support](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) , hvis du fortsat oplever problemer.
