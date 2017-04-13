<properties 
   pageTitle="ExpressRoute fejlfinding Guide – få ARP tabeller | Microsoft Azure"
   description="Denne side indeholder en vejledning til at få ARP tabeller for en ExpressRoute kredsløb"
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

#<a name="expressroute-troubleshooting-guide---getting-arp-tables-in-the-resource-manager-deployment-model"></a>Foretage fejlfinding af ExpressRoute guide – få ARP tabeller i implementeringsmodel ressourcestyring

> [AZURE.SELECTOR]
[PowerShell - ressourcestyring](expressroute-troubleshooting-arp-resource-manager.md)
[PowerShell - klassisk](expressroute-troubleshooting-arp-classic.md)

I denne artikel fører dig gennem trinnene for at få mere at vide tabellerne ARP for din ExpressRoute kredsløb. 

>[AZURE.IMPORTANT] Dette dokument er beregnet på at hjælpe dig med at diagnosticere og løse problemer med enkelt. Det er ikke beregnet til at være en erstatning for Microsoft support. Du skal åbne en supportbilletter med [Microsoft support](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) , hvis du ikke kan løse problemet ved hjælp af den vejledning, der er beskrevet nedenfor.

## <a name="address-resolution-protocol-arp-and-arp-tables"></a>Adresse (ARP opløsning Protocol) og ARP tabeller
Opløsning ARP (Address Protocol) er en lag 2-protokol, der er defineret i [RFC 826](https://tools.ietf.org/html/rfc826). ARP bruges til at tilknytte Ethernet-adresse (MAC-adressen) med en IP-adresse.

ARP tabellen indeholder en tilknytning af IPv4-adresse og MAC-adresse til en bestemt peering. Tabellen ARP for en ExpressRoute kredsløb peering indeholder følgende oplysninger for hver grænseflade (primære og sekundære)

1. Tilknytning af lokale router interface IP-adresse til MAC-adressen
2. Tilknytning af ExpressRoute router interface IP-adresse til MAC-adressen
3. Alder for tilknytningen

ARP tabeller kan hjælpe med at validere konfigurationen af lag 2 og fejlfinding i forbindelse med grundlæggende lag 2 forbindelsesproblemer. 

Eksempel ARP tabel: 

        Age InterfaceProperty IpAddress  MacAddress    
        --- ----------------- ---------  ----------    
         10 On-Prem           10.0.0.1 ffff.eeee.dddd
          0 Microsoft         10.0.0.2 aaaa.bbbb.cccc


Følgende afsnit indeholder oplysninger om hvordan du kan se tabellerne ARP ses af ExpressRoute kant routerne. 

## <a name="prerequisites-for-learning-arp-tables"></a>Forudsætninger for learning ARP tabeller

Sikre, at du har følgende, før du gennemgå yderligere

 - Et gyldigt ExpressRoute kredsløb, der er konfigureret med mindst én peering. Kredsløbet skal konfigureres fuldt af provideren connectivity. Du (eller udbyderen connectivity) skal have konfigureret mindst én af peerings (Azure privat, Azure offentligt og Microsoft) på dette kredsløb.
 - IP-adresseområder bruges til at konfigurere peerings (Azure privat, Azure offentligt og Microsoft). Gennemse IP-adresse tildeling eksemplerne i [ExpressRoute routing krav side](expressroute-routing.md) for at få en forståelse af, hvordan IP-adresser er tilknyttet grænseflader i din side og i ExpressRoute side. Du kan få oplysninger om peering konfigurationen ved at gennemgå [ExpressRoute peering konfigurationssiden](expressroute-howto-routing-arm.md).
 - Oplysninger fra dit netværk team / connectivity udbyder på MAC-adresserne til grænseflader bruges sammen med disse IP-adresser.
 - Du skal have det nyeste PowerShell-modulet til Azure (version 1,50 eller nyere).

## <a name="getting-the-arp-tables-for-your-expressroute-circuit"></a>Få ARP tabeller for din ExpressRoute kredsløb
Dette afsnit indeholder vejledning i, hvordan du kan se tabellerne ARP per peering ved hjælp af PowerShell. Du eller udbyderen connectivity skal have konfigureret peering før forløbende yderligere. Hvert kredsløb har to kurver (primære og sekundære). Du kan se tabellen ARP for hver sti uafhængigt af hinanden.

### <a name="arp-tables-for-azure-private-peering"></a>ARP tabeller til Azure privat peering
Følgende cmdlet indeholder ARP tabeller til Azure privat peering

        # Required Variables
        $RG = "<Your Resource Group Name Here>"
        $Name = "<Your ExpressRoute Circuit Name Here>"
        
        # ARP table for Azure private peering - Primary path
        Get-AzureRmExpressRouteCircuitARPTable -ResourceGroupName $RG -ExpressRouteCircuitName $Name -PeeringType AzurePrivatePeering -DevicePath Primary
        
        # ARP table for Azure private peering - Secodary path
        Get-AzureRmExpressRouteCircuitARPTable -ResourceGroupName $RG -ExpressRouteCircuitName $Name -PeeringType AzurePrivatePeering -DevicePath Secondary 

Eksempel på output er vist nedenfor for et af stierne

        Age InterfaceProperty IpAddress  MacAddress    
        --- ----------------- ---------  ----------    
         10 On-Prem           10.0.0.1 ffff.eeee.dddd
          0 Microsoft         10.0.0.2 aaaa.bbbb.cccc


### <a name="arp-tables-for-azure-public-peering"></a>ARP tabeller til Azure offentlige peering
Følgende cmdlet indeholder ARP tabeller til Azure offentlige peering

        # Required Variables
        $RG = "<Your Resource Group Name Here>"
        $Name = "<Your ExpressRoute Circuit Name Here>"
        
        # ARP table for Azure public peering - Primary path
        Get-AzureRmExpressRouteCircuitARPTable -ResourceGroupName $RG -ExpressRouteCircuitName $Name -PeeringType AzurePublicPeering -DevicePath Primary
        
        # ARP table for Azure public peering - Secodary path
        Get-AzureRmExpressRouteCircuitARPTable -ResourceGroupName $RG -ExpressRouteCircuitName $Name -PeeringType AzurePublicPeering -DevicePath Secondary 


Eksempel på output er vist nedenfor for et af stierne

        Age InterfaceProperty IpAddress  MacAddress    
        --- ----------------- ---------  ----------    
         10 On-Prem           64.0.0.1 ffff.eeee.dddd
          0 Microsoft         64.0.0.2 aaaa.bbbb.cccc


### <a name="arp-tables-for-microsoft-peering"></a>ARP tabeller til Microsoft peering
Følgende cmdlet indeholder ARP tabeller til Microsoft peering

        # Required Variables
        $RG = "<Your Resource Group Name Here>"
        $Name = "<Your ExpressRoute Circuit Name Here>"
        
        # ARP table for Microsoft peering - Primary path
        Get-AzureRmExpressRouteCircuitARPTable -ResourceGroupName $RG -ExpressRouteCircuitName $Name -PeeringType MicrosoftPeering -DevicePath Primary
        
        # ARP table for Microsoft peering - Secodary path
        Get-AzureRmExpressRouteCircuitARPTable -ResourceGroupName $RG -ExpressRouteCircuitName $Name -PeeringType MicrosoftPeering -DevicePath Secondary 


Eksempel på output er vist nedenfor for et af stierne

        Age InterfaceProperty IpAddress  MacAddress    
        --- ----------------- ---------  ----------    
         10 On-Prem           65.0.0.1 ffff.eeee.dddd
          0 Microsoft         65.0.0.2 aaaa.bbbb.cccc


## <a name="how-to-use-this-information"></a>Hvordan du bruger disse oplysninger
Tabellen ARP af en peering kan bruges til at bestemme validere 2 lagkonfiguration og forbindelsen. Dette afsnit indeholder en oversigt over, hvordan ARP tabeller ser ud under forskellige scenarier.

### <a name="arp-table-when-a-circuit-is-in-operational-state-expected-state"></a>ARP tabel, når et kredsløb er i drift tilstand (forventede tilstand)

 - Tabellen ARP har en post til den lokale side med en gyldig IP-adresse og MAC-adresse og en tilsvarende post for Microsoft-side. 
 - Den sidste oktet af lokal IP-adressen være altid et ulige tal.
 - Den sidste oktet Microsoft IP-adresse være altid et lige tal.
 - Den samme MAC-adresse vises på i Microsoft side for alle 3 peerings (primære / sekundære). 


        Age InterfaceProperty IpAddress  MacAddress    
        --- ----------------- ---------  ----------    
         10 On-Prem           65.0.0.1 ffff.eeee.dddd
          0 Microsoft         65.0.0.2 aaaa.bbbb.cccc

### <a name="arp-table-when-on-premises--connectivity-provider-side-has-problems"></a>ARP tabellen, når lokale / connectivity udbyder side har problemer med

 - Kun én post vises i tabellen ARP. Dette viser tilknytningen mellem MAC-adresse og IP-adresse til Microsoft-side. 

        Age InterfaceProperty IpAddress  MacAddress    
        --- ----------------- ---------  ----------    
          0 Microsoft         65.0.0.2 aaaa.bbbb.cccc

>[AZURE.NOTE] Åbn en supportanmodning med udbyderen connectivity foretage fejlfinding af problemer. 


### <a name="arp-table-when-microsoft-side-has-problems"></a>ARP tabel, når Microsoft side har problemer med

 - Du kan ikke se en ARP-tabel, der vises for en peering, hvis der opstår problemer i Microsoft-side. 
 -  Åbn en supportbilletter med [Microsoft support](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade). Angive, at du har et problem med lag 2 connectivity. 

## <a name="next-steps"></a>Næste trin

 - Validere Layer 3 konfigurationer for din ExpressRoute kredsløb
     - Få distribuere oversigt til at bestemme tilstand af BGP sessioner 
     - Hent distribuere tabel til at bestemme, hvilke præfikser meddeles på tværs af ExpressRoute
 - Validere dataoverførsel ved at gennemgå byte ind / ud
 - Åbn en supportbilletter med [Microsoft support](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) , hvis du fortsat oplever problemer.
