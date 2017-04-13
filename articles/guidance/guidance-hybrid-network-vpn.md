<properties
   pageTitle="Implementere en Hybrid netværksarkitektur med Azure og lokale VPN | Microsoft Azure"
   description="Hvordan du kan implementere et sikkert websted til websted netværksarkitektur, der strækker sig over en Azure virtuelt netværk og et lokalt netværk, der er forbundet ved hjælp af en VPN-forbindelse."
   services=""
   documentationCenter="na"
   authors="RohitSharma-pnp"
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
   ms.author="roshar"/>

# <a name="implementing-a-hybrid-network-architecture-with-azure-and-on-premises-vpn"></a>Implementere en Hybrid netværksarkitektur med Azure og lokale VPN

[AZURE.INCLUDE [pnp-header](../../includes/guidance-pnp-header-include.md)]

I denne artikel beskrives et sæt af fremgangsmåder til at udvide et lokalt netværk til Azure ved hjælp af et websted til websted virtuelt privat netværk (VPN). Trafikken flyder mellem det lokale netværk og en Azure virtuelt netværk (VNet) via en IPSec VPN-tunnel. Denne arkitektur egner sig til hybrid programmer med følgende egenskaber:

- Dele af programmet køres i det lokale miljø, mens andre køres i Azure.

- Trafik mellem lokale hardware og skyen der sandsynligvis er lys, eller det er en fordel at handel lidt udvidede ventetid for fleksibilitet og databehandling på skyen.

- Udvidede netværket udgør et lukket system. Der er ingen direkte sti fra internettet til Azure VNet.

- Brugere opretter forbindelse til den lokale netværk, der skal bruge tjenesterne Azure som vært. Bro mellem det lokale netværk og de tjenester, der kører i Azure er transparent for brugerne.

Eksempler på scenarier, der passer til denne profil omfatter:

- Line of business-programmer, der bruges i en organisation, hvor en del af funktionaliteten er overført til skyen.

- Udvikling/testmiljø arbejdsmængder.

> [AZURE.NOTE] Azure har to forskellige installation modeller: [Azure ressourcestyring] [ resource-manager-overview] og klassisk. Denne grundrids bruger Ressourcestyring, som Microsoft anbefaler til nye installationer.

## <a name="architecture-diagram"></a>Arkitektur diagram

I det følgende diagram fremhæver komponenterne i denne arkitektur:

> Et Visio-dokument, der indeholder dette arkitektur diagram kan hentes på [Microsoft download center][visio-download]. I dette diagram er på "Hybrid netværket - VPN" side.

![[0]][0]

- **Lokale netværk.** Dette er et netværk af computere og enheder, der er forbundet via en privat LAN-netværk i en organisation, der kører.

- **VPN maskinen.** Dette er en enhed eller tjeneste, som indeholder eksterne forbindelse til den lokale netværk. VPN maskinen kan være en hardwareenhed, eller det kan være en softwareløsning som Routing og Remote Access Service (RRAS) i Windows Server 2012.

> [AZURE.NOTE] Få en liste over understøttede VPN-anvendelser og oplysninger om konfiguration af valgte VPN udstyr til at oprette forbindelse til en VPN-Gateway, Azure, i vejledning til den relevante enhed på [listen over VPN-enheder, der understøttes af Azure][vpn-appliance].

- **N-delt cloud-program.** Dette er det program, der er hostet i Azure. Det kan indeholde flere lag, med flere undernet forbindelse via Azure Indlæs balancere. Trafik i hvert undernet er underlagt regler, der er defineret ved hjælp af [Azure netværk sikkerhedsgrupper (NSGs)][azure-network-security-group]. Du kan finde flere oplysninger i [Introduktion til Microsoft Azure sikkerhed][getting-started-with-azure-security].

> [AZURE.NOTE] I denne artikel beskrives programmet skyen som et enkelt objekt. Se [kører en N-delt arkitektur på Azure] [ implementing-a-multi-tier-architecture-on-Azure] kan finde detaljerede oplysninger.

- **[Virtuelt netværk (VNet)][azure-virtual-network].** Programmet skyen og -komponenter til Azure VPN-Gateway er placeret i den samme VNet.

- **[Azure VPN-Gateway][azure-vpn-gateway].** Tjenesten VPN gateway gør det muligt at oprette forbindelse på VNet til det lokale netværk via en VPN-maskinen. Få mere at vide under [oprette forbindelse et lokalt netværk til et Microsoft Azure virtuelle netværk][connect-to-an-Azure-vnet]. Gatewayen VPN omfatter følgende elementer:

  - **Virtuelt netværksgateway.** Dette er en ressource, der indeholder et virtuelt VPN maskinen til VNet. Det er ansvarlig for at dirigere trafik fra det lokale netværk til VNet.

  - **Lokale netværksgateway.** Dette er et uddrag af lokale VPN maskinen. Netværkstrafik fra skyen programmet til det lokale netværk er distribueret via denne gateway.

  - **Forbindelse.** Forbindelsen har egenskaber, der angiver forbindelsestypen (IPSec) og nøglen delt med lokale VPN maskinen for at kryptere trafik.

  - **Gateway-undernet.** Gatewayen virtuelt netværk holdes i sin egen undernet, som er underlagt krav til forskellige, som beskrevet i afsnittet anbefalinger nedenfor.

- **Interne justering af belastning.** Netværkstrafik fra en VPN-Gateway er distribueret til skyen programmet via en intern justering af belastning. Justering af belastning er placeret i det front end-undernet af programmet.

## <a name="recommendations"></a>Anbefalinger

Azure byder på mange forskellige ressourcer og ressourcetyper, så denne referencearkitektur kan være klargjort mange forskellige måder. Vi har angivet en Azure ressourcestyring skabelon for at installere den referencearkitektur, der følger disse anbefalinger. Hvis du vælger at oprette din egen referencearkitektur skal du følge disse anbefalinger, medmindre du har særlige krav, en anbefaling der ikke passer.

### <a name="vnet-and-gateway-subnet"></a>VNet og gatewayen undernet

Oprette en Azure VNet ved tilbageholdelse af komponenterne i skyen. Adresseområde af Azure VNet skal være stort nok til at medtage de adresser, der bruges til FOS og undernet i VNet. Sørg for, at adresseområde VNet har tilstrækkelig plads til vækst, hvis yderligere FOS sandsynligvis vil være behov i fremtiden. Adresseområde af VNet skal ikke overlapper med lokale netværk. For eksempel bruger diagrammet ovenfor adresse mellemrum 10.20.0.0/16 for VNet.

Oprette et navngivet _GatewaySubnet_, med et adresseområde på /27 undernet. Dette undernet kræves af gatewayen virtuelt netværk og fordeling af 32 adresser til dette undernet hjælper med at forhindre, at du kører sig i forhold til mulige gateway størrelsesbegrænsninger i fremtiden. Undgå at placere dette undernet midt-adresseområde. En god ide er at indstille adresseområde for gateway-undernet i øverste slutningen af VNet-adresseområde. Eksemplet i diagrammet bruger 10.20.255.224/27.  En hurtig procedure til at beregne CIDR er som følger:

1. Angive variable bit i adresseområde af VNet til 1, op til de bit, der bruges af gateway-undernet, og angiv derefter de resterende bit til 0.

2. Konvertere de resulterende bit til et decimaltal og express det som en adresseområde med præfiks længde indstillet til størrelsen af gateway-undernettet.

For eksempel bliver en VNet med en IP-adresseområder for 10.20.0.0/16 anvende trin #1 ovenfor 10.20.0b11111111.0b11100000.  Konvertere, til et decimaltal og udtrykke det som en adresseområde giver 10.20.255.224/27

> [AZURE.WARNING] Forsøg at installere andre virtuelle maskiner eller rolle forekomster til gateway-undernet. Desuden ikke Tildel en NSG til dette undernet, som det medfører, at gatewayen ikke korrekt.

### <a name="virtual-network-gateway"></a>Virtuelt netværksgateway

Tildele en offentlig IP-adresse til gatewayen virtuelt netværk.

Oprette gatewayen virtuelt netværk i Gateway-undernet og tildele den nyligt tildelte offentlige IP-adressen. Brug af typen gateway, der passer bedst til dine behov, og som er aktiveret af VPN anvendelsen:

Oprette en [politik-baserede gateway] [ policy-based-routing] Hvis du har brug for til at overvåge styre, hvordan anmodninger distribueres. baseret på politik kriterier såsom adressepræfikser. Bruge statisk distribution baseret på politik gateways, og virker kun med forbindelser til websted.

Oprette en [distribution baseret gateway] [ route-based-routing] Hvis du opretter forbindelse til den lokale netværk ved hjælp af RRAS, understøtter flere websted eller kryds-område forbindelser, eller implementere VNet-VNet forbindelser (herunder omdirigerer, der starter ved flere VNets). Distribuere-baserede destinationsgatewayen anvender dynamiske routing til dirigere trafikken mellem netværk. De kan acceptere fejl i netværksstien bedre end statisk omdirigerer, fordi de kan prøve alternative omdirigerer. Distribuere-baserede gateways kan også reducere omkostninger management, fordi omdirigerer ikke muligvis opdateres manuelt, når netværksadresser ændres.

Se [om VPN enheder for at websted VPN-Gateway forbindelser]til en liste over understøttede VPN-anvendelser,[vpn-appliances].

> [AZURE.NOTE] Når gatewayen er blevet oprettet, kan du skifte mellem gateway typer uden at slette og genoprette gatewayen.

Vælg den Azure VPN Gateway SKU, der passer bedst til dine krav til overførselshastighed. Azure VPN-Gateway er tilgængelig i tre lagerenheder, der er vist i følgende tabel. Hver SKU giver forskellige overførselshastighed, [er gebyrer] [ azure-gateway-charges] baseret på mængden tid, der er klargjort gatewayen og tilgængelig.

| SKU | VPN overførselshastighed | Maks IPSec-tunneler|
|-----|----------------|------------------|
| Grundlæggende | 100 Mbps | 10 |
| Standard | 100 Mbps | 10 |
| Høj ydeevne | 200 Mbps | 30 |

> [AZURE.NOTE] Den grundlæggende SKU er ikke kompatibel med Azure ExpressRoute. Du kan [ændre SKU] [ changing-SKUs] efter gatewayen er blevet oprettet.

Opret routing regler for gateway-undernet, der direkte indgående programmet trafik fra en gateway til den interne justering af belastning i stedet for at tillade anmodninger om at overføre direkte til de VM'er, der implementerer programmet.

### <a name="on-premises-network-connection"></a>Lokal netværksforbindelse

Oprette en gateway lokale netværk. Angiv den offentlige IP-adresse i det lokale miljø VPN maskinen og adresseområde i det lokale netværk. Bemærk, at lokale VPN maskinen skal have en offentlig IP-adresse, der kan åbnes af gatewayen lokale netværk i Azure VPN-Gateway. VPN-enheden kan ikke være placeret bag en NAT-enhed.

Oprette en-til-websted forbindelse til gatewayen virtuelt netværk og gatewayen lokale netværk. Vælg forbindelsestype til-websted (IPSec), og Angiv den delte nøgle. Websted til kryptering med Azure VPN-Gateway er baseret på IPSec-protokollen, ved hjælp af allerede delte nøgler til godkendelse. Du kan angive tasten, når du opretter Azure VPN gatewayen. Du skal konfigurere VPN maskinen kører lokalt med den samme nøgle. Andre godkendelsesmetoder understøttes ikke i øjeblikket.

Sørg for, at de lokale dirigere infrastruktur er konfigureret til at videresende anmodninger, der er beregnet til adresserne i Azure VNet til VPN-enhed.

Åbne nogen porte, der kræves af programmet skyen i det lokale netværk.

Test forbindelse for at kontrollere, at:

- Lokalt VPN maskinen korrekt dirigerer trafik til skyen programmet via Azure VPN gatewayen.

- VNet korrekt dirigerer trafik tilbage til det lokale netværk.

- Forbudte trafik i begge retninger blokeres korrekt.

## <a name="scalability-considerations"></a>Overvejelser i forbindelse med skalerbarhed

Du kan opnå begrænset lodret skalerbarhed ved at flytte fra Basic eller Standard VPN Gateway SKU'er til høj ydeevne VPN SKU.

VNets, forventer en stor mængde VPN-trafik, kan du overveje at distribuere de forskellige arbejdsmængder i separate mindre VNets og konfiguration af en VPN-Gateway for hver af dem.

Du kan dele VNet, enten vandret eller lodret. Hvis du vil dele vandret, skal du flytte nogle VM forekomster fra hvert niveau til undernet i den nye VNet. Resultatet er, at hver VNet har samme struktur og funktionalitet. Hvis du vil dele lodret, omdesigne hvert niveau for at inddele funktionaliteten i forskellige logiske områder (såsom håndtering af ordrer, fakturering, konto kundestyring og osv.). De enkelte funktionsområder kan derefter placeres i sin egen VNet.

Replikering et lokalt Active Directory-domænecontrolleren i VNet og implementere DNS i VNet, kan hjælpe med at reducere nogle af de sikkerhedsrelaterede og administrative trafik, der flyder fra det lokale til skyen.

## <a name="availability-considerations"></a>Overvejelser i forbindelse med tilgængelighed

Hvis du har brug at sikre, at det lokale netværk forbliver tilgængelige for gatewayen Azure VPN-, implementere en sekundær klynge af lokale VPN gatewayen.

Hvis din organisation har flere lokale websteder, kan du oprette [flere websted forbindelser] [ vpn-gateway-multi-site] til en eller flere Azure VNets. Denne fremgangsmåde kræver dynamisk (rute-baseret) routing, så sørg for, at gatewayen lokale VPN understøtter denne funktion.

Se [SERVICENIVEAUAFTALE for VPN-Gateway] [ sla-for-vpn-gateway] for oplysninger om VPN-Gateway SERVICENIVEAUAFTALE.

## <a name="manageability-considerations"></a>Overvejelser i forbindelse med administration

Overvåge diagnostiske oplysninger fra lokale VPN udstyr. Denne proces, afhænger af de funktioner, der leveres af VPN maskinen. Eksempelvis hvis du bruger tjenesten Routing og Remote Access på Windows Server 2012, skal du aktivere [logføring af RRAS][rras-logging].

Bruge [Azure VPN-Gateway diagnosticering] [ gateway-diagnostic-logs] til at registrere oplysninger om problemer med serverforbindelsen. Disse logfiler kan bruges til at spore oplysninger som kilde- og destinationer for forbindelse anmodninger om, hvilken protokol der blev brugt, og hvordan blev oprettet forbindelse (eller hvorfor mislykkedes).

Overvåge loggene driften af gatewayen Azure VPN ved hjælp af overvågningslogge tilgængelig i portalen Azure. Separate logfiler er tilgængelige for gatewayen lokale netværk, gatewayen Azure netværk og forbindelsen. Disse oplysninger kan bruges til at registrere eventuelle ændringer til gatewayen, og kan være nyttigt, hvis en tidligere fungerende gateway holder op med at arbejde for en eller anden grund.

![[2]][2]

Overvåge connectivity og spore connectivity mislykkedes hændelser. Du kan bruge en overvågning pakke som [Nagios] [ nagios] til at registrere og rapportere disse oplysninger.

## <a name="security-considerations"></a>Overvejelser om sikkerheden

Opret en anden delt nøgle for hver VPN-gateway. Brug en stærk delt nøgle til at modstå skadelige angreb.

> [AZURE.NOTE] I øjeblikket, bruge du ikke Azure-tasten samling til allerede delte nøgler Azure VPN gatewayen.

Sikre, at lokale VPN maskinen bruger en krypteringsmetode, der er [kompatibelt med Azure VPN Gateway][vpn-appliance-ipsec]. Baseret på politik routing understøtter gatewayen Azure VPN AES256, AES128 og 3DES krypteringsalgoritmer. Distribuere-baserede gateways understøtter AES256 og 3DES.

Hvis din lokale VPN maskinen er på et afskærmede netværk, der har en firewall mellem det afskærmede netværk og internettet, kan det være nødvendigt at konfigurere [Yderligere firewallregler] [ additional-firewall-rules] til at tillade til websted VPN-forbindelse.

Hvis programmet i VNet sender data til internettet, kan du overveje at [implementere tvunget tunnelføring] [ forced-tunneling] til at dirigere al internetbaserede trafik via lokale netværk. Denne fremgangsmåde gør det muligt at overvåge udgående anmodninger fra programmet fra den lokale infrastruktur.

> [AZURE.NOTE] Tvunget tunnelføring kan påvirke forbindelse til Azure tjenester (tjenesten Storage, for eksempel) og Windows-licens Manager.

## <a name="troubleshooting-considerations"></a>Overvejelser i forbindelse med fejlfinding

> [AZURE.NOTE] Gå til Routing og Remote Access-Blog til generelle oplysninger om [fejlfinding af almindelige VPN-relaterede fejl][troubleshooting-vpn-errors].

Hvis trafik kan ikke gennemgang VPN-forbindelsen, skal du kontrollere følgende:

### <a name="on-premises-vpn-appliance"></a>Lokalt VPN anvendelse:

**Lokalt VPN maskinen fungerer korrekt?**

Markere en logfiler, der genereres af VPN maskinen for fejl og mislykkede forsøg. Placeringen af disse oplysninger kan variere, alt efter anvendelsen. Hvis du bruger RRAS på Windows Server 2012, kan du bruge følgende PowerShell-kommando til at vise begivenhed fejloplysninger for tjenesten RRAS:

```
Get-EventLog -LogName System -EntryType Error -Source RemoteAccess | Format-List -Property *
```

Egenskaben _meddelelse_ for hvert element indeholder en beskrivelse af fejlen. Nogle almindelige eksempler er:

- Manglende evne til at oprette forbindelse, muligvis på grund af en forkert IP-adresse, der er angivet for Azure VPN-Gateway i RRAS VPN-grænseflade konfiguration:

```
EventID            : 20111
MachineName        : on-prem-vm
Data               : {41, 3, 0, 0}
Index              : 14231
Category           : (0)
CategoryNumber     : 0
EntryType          : Error
Message            : RoutingDomainID- {00000000-0000-0000-0000-000000000000}: A Demand Dial connection to the remote
                     interface AzureGateway on port VPN2-4 was successfully initiated but failed to complete
                     successfully because of the  following error: The network connection between your computer and
                     the VPN server could not be established because the remote server is not responding. This could
                     be because one of the network devices (e.g, firewalls, NAT, routers, etc) between your computer
                     and the remote server is not configured to allow VPN connections. Please contact your
                     Administrator or your service provider to determine which device may be causing the problem.
Source             : RemoteAccess
ReplacementStrings : {{00000000-0000-0000-0000-000000000000}, AzureGateway, VPN2-4, The network connection between
                     your computer and the VPN server could not be established because the remote server is not
                     responding. This could be because one of the network devices (e.g, firewalls, NAT, routers, etc)
                     between your computer and the remote server is not configured to allow VPN connections. Please
                     contact your Administrator or your service provider to determine which device may be causing the
                     problem.}
InstanceId         : 20111
TimeGenerated      : 3/18/2016 1:26:02 PM
TimeWritten        : 3/18/2016 1:26:02 PM
UserName           :
Site               :
Container          :
```

- Den forkerte delte nøgle, der er angivet i RRAS VPN interface netværkskonfiguration:

```
EventID            : 20111
MachineName        : on-prem-vm
Data               : {233, 53, 0, 0}
Index              : 14245
Category           : (0)
CategoryNumber     : 0
EntryType          : Error
Message            : RoutingDomainID- {00000000-0000-0000-0000-000000000000}: A Demand Dial connection to the remote
                     interface AzureGateway on port VPN2-4 was successfully initiated but failed to complete
                     successfully because of the  following error: IKE authentication credentials are unacceptable

Source             : RemoteAccess
ReplacementStrings : {{00000000-0000-0000-0000-000000000000}, AzureGateway, VPN2-4, IKE authentication credentials are
                     unacceptable
                     }
InstanceId         : 20111
TimeGenerated      : 3/18/2016 1:34:22 PM
TimeWritten        : 3/18/2016 1:34:22 PM
UserName           :
Site               :
Container          :
```

Du kan også få hændelseslogoplysninger om forsøg på at oprette forbindelse via RRAS tjenesten ved hjælp af følgende PowerShell-kommando:

```
Get-EventLog -LogName Application -Source RasClient | Format-List -Property *
```

I tilfælde af en mislykket til at oprette forbindelse, indeholder denne log fejl, der ligner følgende:

```
EventID            : 20227
MachineName        : on-prem-vm
Data               : {}
Index              : 4203
Category           : (0)
CategoryNumber     : 0
EntryType          : Error
Message            : CoId={B4000371-A67F-452F-AA4C-3125AA9CFC78}: The user SYSTEM dialed a connection named
                     AzureGateway which has failed. The error code returned on failure is 809.
Source             : RasClient
ReplacementStrings : {{B4000371-A67F-452F-AA4C-3125AA9CFC78}, SYSTEM, AzureGateway, 809}
InstanceId         : 20227
TimeGenerated      : 3/18/2016 1:29:21 PM
TimeWritten        : 3/18/2016 1:29:21 PM
UserName           :
Site               :
Container          :
```

**Er VPN maskinen korrekt dirigere trafik via Azure VPN Gateway?**

Bruge et værktøj som [PsPing] [ psping] at bekræfte forbindelsen og routing på tværs af gatewayen VPN. For eksempel køre følgende kommando for at teste forbindelsen fra en lokal computer til en webserver, der er placeret på VNet (erstatte `<<web-server-address>>` med adressen på webserveren):

```
PsPing -t <<web-server-address>>:80
```

Hvis den lokale computer kan omdirigere trafikken til webserveren, skal du se afsnittet output, der svarer til følgende:

```
D:\PSTools>psping -t 10.20.0.5:80

PsPing v2.01 - PsPing - ping, latency, bandwidth measurement utility
Copyright (C) 2012-2014 Mark Russinovich
Sysinternals - www.sysinternals.com

TCP connect to 10.20.0.5:80:
Infinite iterations (warmup 1) connecting test:
Connecting to 10.20.0.5:80 (warmup): 6.21ms
Connecting to 10.20.0.5:80: 3.79ms
Connecting to 10.20.0.5:80: 3.44ms
Connecting to 10.20.0.5:80: 4.81ms

  Sent = 3, Received = 3, Lost = 0 (0% loss),
  Minimum = 3.44ms, Maximum = 4.81ms, Average = 4.01ms
```

Hvis den lokale computer ikke kan kommunikere med den angivne destination, får du vist meddelelser således:

```
D:\PSTools>psping -t 10.20.1.6:80

PsPing v2.01 - PsPing - ping, latency, bandwidth measurement utility
Copyright (C) 2012-2014 Mark Russinovich
Sysinternals - www.sysinternals.com

TCP connect to 10.20.1.6:80:
Infinite iterations (warmup 1) connecting test:
Connecting to 10.20.1.6:80 (warmup): This operation returned because the timeout period expired.
Connecting to 10.20.1.6:80: This operation returned because the timeout period expired.
Connecting to 10.20.1.6:80: This operation returned because the timeout period expired.
Connecting to 10.20.1.6:80: This operation returned because the timeout period expired.
Connecting to 10.20.1.6:80:
  Sent = 3, Received = 0, Lost = 3 (100% loss),
  Minimum = 0.00ms, Maximum = 0.00ms, Average = 0.00ms
```

**Tillader lokal firewall VPN-trafik til igennem? Har været åbnet de korrekte porte**

Kontrollér, at lokale VPN maskinen anvender en krypteringsmetode, der er [kompatibelt med Azure VPN Gateway][vpn-appliance].

Baseret på politik routing understøtter gatewayen Azure VPN AES256, AES128 og 3DES krypteringsalgoritmer. Distribuere-baserede gateways understøtter AES256 og 3DES.

### <a name="azure-vnet-gateway"></a>Azure VNet Gateway:

Undersøge [Azure VPN-Gateway diagnosticeringslogfiler] [ gateway-diagnostic-logs] potentielle problemer.

**Er VPN-Gateway, Azure og lokale VPN maskinen konfigureret med den samme delte godkendelsesnøgle?**

Du kan få vist den delte nøgle, der er gemt af gatewayen Azure VPN ved hjælp af kommandoen følgende Azure CLI:

```
azure network vpn-connection shared-key show <<resource-group>> <<vpn-connection-name>>
```

Brug kommandoen passer til din lokale VPN maskinen for at vise den delte nøgle, der er konfigureret til denne anvendelse.

Kontrollér, at det _GatewaySubnet_ undernet holder Azure VPN-Gateway ikke er knyttet til en NSG.

Du kan få vist undernet oplysninger ved hjælp af kommandoen følgende Azure CLI:

```
azure network vnet subnet show -g <<resource-group>> -e <<vnet-name>> -n GatewaySubnet
```

Sikre, at der er ingen datafelt hedder _netværk sikkerhedsgruppe id_. I følgende eksempel viser resultaterne for forekomsten af _GatewaySubnet_ , der har en tildelt NSG (_VPN Gatewaygruppe_). Dette kan medføre forhindre gatewayen ikke fungerer korrekt, hvis der er alle regler, der er defineret for denne NSG:

```
C:\>azure network vnet subnet show -g profx-prod-rg -e profx-vnet -n GatewaySubnet
    info:    Executing command network vnet subnet show
    + Looking up virtual network "profx-vnet"
    + Looking up the subnet "GatewaySubnet"
    data:    Id                              : /subscriptions/########-####-####-####-############/resourceGroups/profx-prod-rg/providers/Microsoft.Network/virtualNetworks/profx-vnet/subnets/GatewaySubnet
    data:    Name                            : GatewaySubnet
    data:    Provisioning state              : Succeeded
    data:    Address prefix                  : 10.20.3.0/27
    data:    Network Security Group id       : /subscriptions/########-####-####-####-############/resourceGroups/profx-prod-rg/providers/Microsoft.Network/networkSecurityGroups/VPN-Gateway-Group
    info:    network vnet subnet show command OK
```

**Er de virtuelle maskiner i Azure VNet konfigureret til at tillade trafik kommer fra uden for VNet? Markér alle NSG regler, der er knyttet til undernet, der indeholder disse virtuelle computere.**

Du kan få vist alle regler for NSG ved hjælp af kommandoen følgende Azure CLI:

```
azure network nsg show -g <<resource-group>> -n <<nsg-name>>
```

**Er blevet afbrudt gatewayen Azure VPN**

Du kan bruge følgende Azure PowerShell-kommando til at kontrollere status for Azure VPN-forbindelse. Den `<<connection-name>>` parameter er navnet på den Azure VPN-forbindelse, der kæder gatewayen virtuelt netværk og den lokale gateway.

```
Get-AzureRmVirtualNetworkGatewayConnection -Name <<connection-name>> - ResourceGroupName <<resource-group>>
```

De følgende kodestykker fremhæve output genereres Hvis gatewayen er forbundet (det første eksempel), og afbrudt (det andet eksempel):

```
PS C:\> Get-AzureRmVirtualNetworkGatewayConnection -Name profx-gateway-connection -ResourceGroupName profx-prod-rg

AuthorizationKey           :
VirtualNetworkGateway1     : Microsoft.Azure.Commands.Network.Models.PSVirtualNetworkGateway
VirtualNetworkGateway2     :
LocalNetworkGateway2       : Microsoft.Azure.Commands.Network.Models.PSLocalNetworkGateway
Peer                       :
ConnectionType             : IPsec
RoutingWeight              : 0
SharedKey                  : ####################################
ConnectionStatus           : Connected
EgressBytesTransferred     : 55254803
IngressBytesTransferred    : 32227221
ProvisioningState          : Succeeded
...
```

```
PS C:\> Get-AzureRmVirtualNetworkGatewayConnection -Name profx-gateway-connection2 -ResourceGroupName profx-prod-rg

AuthorizationKey           :
VirtualNetworkGateway1     : Microsoft.Azure.Commands.Network.Models.PSVirtualNetworkGateway
VirtualNetworkGateway2     :
LocalNetworkGateway2       : Microsoft.Azure.Commands.Network.Models.PSLocalNetworkGateway
Peer                       :
ConnectionType             : IPsec
RoutingWeight              : 0
SharedKey                  : ####################################
ConnectionStatus           : NotConnected
EgressBytesTransferred     : 0
IngressBytesTransferred    : 0
ProvisioningState          : Succeeded
...
```

### <a name="host-vm-configuration-network-bandwidth-utilization-and-application-performance"></a>Værtskonfigurationen VM, båndbredde udnyttelse af netværk og ydeevne i programmet:

Kontrollér, at firewallen i gæst operativsystem på Azure FOS i undernettet er konfigureret korrekt for at tillade tilladte trafik fra lokal IP-intervaller.

**Findes lydstyrken for trafik tæt grænsen båndbredde til Azure VPN gatewayen?**

Værktøj, afhænger af de tilgængelige for VPN anvendelsen kører lokalt faciliteter. Eksempelvis hvis du bruger RRAS på Windows Server 2012, kan du bruge Performance Monitor til at registrere mængden data, der har modtaget og overføres via VPN-forbindelse ved hjælp af objektet _RAS samlede_ , Vælg de _Modtagne byte/sekund_ og _Byte overføres/sekund_ tællere:

![[3]][3]

Du bør sammenligne resultater med den båndbredde, der er tilgængelige for gatewayen VPN (100 Mbps for Basic og Standard lagerenheder og 200 Mbps for den høj ydeevne SKU):

![[4]][4]

**En af virtuelle maskiner i Azure VNet kører langsomt? Er de overbelastet, er der nok af dem til at håndtere afkrydsningsfeltet Indlæs, er alle Indlæs-balancere konfigureret korrekt?**

Dette kræver, [at indsamle og analysere diagnostiske oplysninger][azure-vm-diagnostics]. Du kan undersøge resultaterne ved hjælp af portalen Azure, men mange tredjepartsværktøjer er også tilgængelig kan, der giver detaljerede indsigt i ydelsesdata.

**Er det program, hvilket gør effektiv brug af ressourcer, der er skyen?**

Instrument programkode, der kører på hver VM til at afgøre, om programmer foretager bedste anvendelse af ressourcer. Du kan også bruge funktioner såsom [Programmet indsigt] [ application-insights] til at udføre disse opgaver.

## <a name="solution-deployment"></a>Løsningsinstallation

Hvis du har et eksisterende lokalt infrastruktur allerede konfigureret med en VPN-maskinen, kan du installere reference arkitekturen ved at følge disse trin:

1. Højreklik på knappen nedenfor, og vælg enten "Åbn link på en ny fane" eller "Åbn hyperlink i nyt vindue":  
[![Installere på Azure](./media/blueprints/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fmspnp%2Freference-architectures%2Fmaster%2Fguidance-hybrid-network-vpn%2Fazuredeploy.json)

2. Vent på linket for at åbne i portalen Azure, og derefter følge disse trin: 
    - **Ressourcegruppe** navnet allerede er defineret i parameterfilen, så vælge **Opret ny** og angive `ra-hybrid-vpn-rg` i tekstfeltet.
    - Vælg området på rullelisten **placering** .
    - Ikke redigere **Skabelon roden Uri** eller tekstfelter **Parameter roden Uri** .
    - Gennemse vilkår og betingelser og derefter klikke på afkrydsningsfeltet **jeg accepterer du vilkår og betingelser, der er anført ovenfor** .
    - Klik på knappen **Køb** .

3. Vente på at fuldføre installationen.

## <a name="next-steps"></a>Næste trin

- [Installere yderligere domænecontrollere til et lokalt Active Directory-domæne ved hjælp af FOS i en Azure VNet][installing-ad].

- [Retningslinjer for installation af Windows Server Active Directory på Azure FOS][deploying-ad].

- [Oprette en DNS-server i en VNet][creating-dns].

- [Konfiguration og administration af DNS i en VNet][configuring-dns].

- [Brug af lokal Stormshield netværk sikkerhedsfunktioner på tværs af en VPN-forbindelse][stormshield].

- [Implementere en Hybrid netværksarkitektur med Azure ExpressRoute][expressroute].

<!-- links -->

[implementing-a-multi-tier-architecture-on-Azure]: ./guidance-compute-n-tier-vm.md
[resource-manager-overview]: ../azure-resource-manager/resource-group-overview.md
[arm-templates]: ../resource-group-authoring-templates.md
[azure-cli]: ../virtual-machines-command-line-tools.md
[azure-portal]: ../azure-portal/resource-group-portal.md
[azure-powershell]: ../powershell-azure-resource-manager.md
[azure-virtual-network]: ../virtual-network/virtual-networks-overview.md
[vpn-appliance]: ../vpn-gateway/vpn-gateway-about-vpn-devices.md
[azure-vpn-gateway]: https://azure.microsoft.com/services/vpn-gateway/
[azure-gateway-charges]: https://azure.microsoft.com/pricing/details/vpn-gateway/
[azure-network-security-group]: https://azure.microsoft.com/documentation/articles/virtual-networks-nsg/
[connect-to-an-Azure-vnet]: https://technet.microsoft.com/library/dn786406.aspx
[vpn-gateway-multi-site]: ../vpn-gateway/vpn-gateway-multi-site.md
[policy-based-routing]: https://en.wikipedia.org/wiki/Policy-based_routing
[route-based-routing]: https://en.wikipedia.org/wiki/Static_routing
[network-security-group]: ../virtual-network/virtual-networks-nsg.md
[sla-for-vpn-gateway]: https://azure.microsoft.com/support/legal/sla/vpn-gateway/v1_0/
[additional-firewall-rules]: https://technet.microsoft.com/library/dn786406.aspx#firewall
[nagios]: https://www.nagios.org/
[azure-vpn-gateway-diagnostics]: http://blogs.technet.com/b/keithmayer/archive/2014/12/18/diagnose-azure-virtual-network-vpn-connectivity-issues-with-powershell.aspx
[ping]: https://technet.microsoft.com/library/ff961503.aspx
[tracert]: https://technet.microsoft.com/library/ff961507.aspx
[psping]: http://technet.microsoft.com/sysinternals/jj729731.aspx
[nmap]: http://nmap.org
[changing-SKUs]: https://azure.microsoft.com/blog/azure-virtual-network-gateway-improvements/
[gateway-diagnostic-logs]: http://blogs.technet.com/b/keithmayer/archive/2015/12/07/step-by-step-capturing-azure-resource-manager-arm-vnet-gateway-diagnostic-logs.aspx
[troubleshooting-vpn-errors]: https://blogs.technet.microsoft.com/rrasblog/2009/08/12/troubleshooting-common-vpn-related-errors/
[rras-logging]: https://www.petri.com/enable-diagnostic-logging-in-windows-server-2012-r2-routing-and-remote-access
[create-on-prem-network]: https://technet.microsoft.com/library/dn786406.aspx#routing
[create-azure-vnet]: ../virtual-network/virtual-networks-create-vnet-classic-cli.md
[azure-vm-diagnostics]: https://azure.microsoft.com/blog/windows-azure-virtual-machine-monitoring-with-wad-extension/
[application-insights]: ../application-insights/app-insights-overview-usage.md
[forced-tunneling]: https://azure.microsoft.com/documentation/articles/vpn-gateway-about-forced-tunneling/
[getting-started-with-azure-security]: ./../security/azure-security-getting-started.md
[vpn-appliances]: ../vpn-gateway/vpn-gateway-about-vpn-devices.md
[installing-ad]: ../active-directory/active-directory-install-replica-active-directory-domain-controller.md
[deploying-ad]: https://msdn.microsoft.com/library/azure/jj156090.aspx
[creating-dns]: https://blogs.msdn.microsoft.com/mcsuksoldev/2014/03/04/creating-a-dns-server-in-azure-iaas/
[configuring-dns]: ../virtual-network/virtual-networks-manage-dns-in-vnet.md
[stormshield]: https://azure.microsoft.com/marketplace/partners/stormshield/stormshield-network-security-for-cloud/
[vpn-appliance-ipsec]: ../vpn-gateway/vpn-gateway-about-vpn-devices.md#ipsec-parameters
[expressroute]: ./guidance-hybrid-network-expressroute.md
[naming conventions]: ./guidance-naming-conventions.md
[solution-script]: https://github.com/mspnp/reference-architectures/tree/master/guidance-hybrid-network-vpn/Deploy-ReferenceArchitecture.ps1
[solution-script-bash]: https://github.com/mspnp/reference-architectures/tree/master/guidance-hybrid-network-vpn/deploy-reference-architecture.sh
[visio-download]: http://download.microsoft.com/download/1/5/6/1569703C-0A82-4A9C-8334-F13D0DF2F472/RAs.vsdx
[vnet-parameters]: https://github.com/mspnp/reference-architectures/tree/master/guidance-hybrid-network-vpn/parameters/virtualNetwork.parameters.json
[virtualNetworkGateway-parameters]: https://github.com/mspnp/reference-architectures/tree/master/guidance-hybrid-network-vpn/parameters/virtualNetworkGateway.parameters.json
[azure-powershell-download]: https://azure.microsoft.com/documentation/articles/powershell-install-configure/
[azure-cli]: https://azure.microsoft.com/documentation/articles/xplat-cli-install/
[0]: ./media/blueprints/hybrid-network-vpn.png "Strukturen i et hybridt netværk, der strækker sig over lokale og infrastruktur i skyen"
[1]: ./media/guidance-hybrid-network-vpn/partitioned-vpn.png "Partitionering en VNet for at forbedre skalerbarhed"
[2]: ./media/guidance-hybrid-network-vpn/audit-logs.png "Overvågningslogge i portalen Azure"
[3]: ./media/guidance-hybrid-network-vpn/RRAS-perf-counters.png "Ydeevnen tællere til overvågning VPN-netværkstrafik"
[4]: ./media/guidance-hybrid-network-vpn/RRAS-perf-graph.png "Eksempel VPN-netværk Ydeevnegraf"