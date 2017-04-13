<properties
   pageTitle="ExpressRoute placeringer | Microsoft Azure"
   description="I denne artikel indeholder en detaljeret oversigt over placeringer, hvor tjenester tilbydes og hvordan du opretter forbindelse til Azure områder."
   services="expressroute"
   documentationCenter="na"
   authors="cherylmc"
   manager="carmonm"
   editor="" />
<tags
   ms.service="expressroute"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="10/21/2016"
   ms.author="cherylmc" />

# <a name="expressroute-partners-and-peering-locations"></a>ExpressRoute partnere og peering placeringer

Tabellerne i denne artikel indeholder oplysninger på ExpressRoute connectivity udbydere af ExpressRoute geografiske om, skal Microsofts skytjenester understøttes ExpressRoute og ExpressRoute systemintegratorer (SIs).

## <a name="partners"></a>ExpressRoute connectivity udbydere

ExpressRoute understøttes på tværs af alle Azure områder og placeringer. Følgende kortet indeholder en liste over Azure områder og ExpressRoute placeringer. ExpressRoute placeringer referere til dem, hvor Microsoft peers med flere tjenesteudbydere.

![Placering kort][0]

Du vil have adgang til Azure tjenester på tværs af alle områder i en geopolitiske region, hvis du har forbindelse til mindst én ExpressRoute placering i det geopolitiske område. Den følgende tabel indeholder en oversigt over Azure områder til ExpressRoute placeringer i et geopolitiske område.

|**Geopolitiske område**|**Azure områder**|**ExpressRoute placeringer**|
|---|---|---|
|**Nordamerika**|Indtastning af østasiatiske USA, vest USA, af US 2, centrale USA, syd centrale USA, nord centrale USA, Canada Central, Canada øst|Atlanta Chicago Dallas, Las Vegas, Los Angeles, New York, Seattle, Silicon Valley, Washington DC, Montreal +, Quebec by +, og Toronto|
|**Sydamerika**|Brasilien syd|Sao Paulo|
|**Europe**|Nord Europe, vest Europe Storbritannien vest, Storbritannien syd|Amsterdam Dublin London, Newport(Wales) +, og Paris|
|**Asien**|Sydøstasien, Sydøstasien|Hong Kong, Singapore|
|**Japan**|Japan vest, Japan øst|Osaka, Tokyo|
|**Australien**|Australien Sydøst, Australien øst|Melbourne, Sydney|
|**Indien**|Indien vest, Indien Central, Indien syd|Chennai, Mumbai|



Tabellen nedenfor indeholder oplysninger om områder og geopolitiske grænser for nationale skyer.

|**Geopolitiske område**|**Azure områder**|**ExpressRoute placeringer**|
|---|---|---|---|
|**Amerikanske regering skyen**|USA Gov krigsskibe i Iowa, USA Gov Virginia|Chicago, Dallas, New York, Washington DC|
|**Kina**|Kina Nord, Kina øst|Beijing, Shanghai|
|**Tyskland**|Tyskland Central, Tyskland øst|Berlin, Frankfurt|


Forbindelse på tværs af geopolitiske områder understøttes ikke på den standard ExpressRoute SKU. Du skal aktivere tilføjelsesprogrammet premium ExpressRoute til at understøtte globale connectivity. Forbindelse til nationale skyen miljøer understøttes ikke. Hvis der opstår nødvendigt, kan du arbejde med udbyderen connectivity.


## <a name="connectivity-provider-locations"></a>Connectivity udbyder placeringer

> [AZURE.SELECTOR]
[Placeringer af udbyder](expressroute-locations.md#connectivity-provider-locations)
[udbydere efter placering](expressroute-locations-providers.md#connectivity-provider-locations)

### <a name="production-azure"></a>Fremstilling Azure
| **Placering**  | **Tjenesteudbydere** |
|---------------|-----------------------|
| **Amsterdam** | Aryaka netværk, AT & T NetBond British Telecom Colt, Equinix, og euNetworks, GÉANT, InterCloud, Internet Solutions - sky oprette forbindelse, Interxion, niveau 3 kommunikation, Orange, Tata kommunikation, TeleCity gruppe, Telenor, Verizon |
| **Atlanta** | Equinix |
| **Chennai** | Tata kommunikation |
| **Chicago** | AT & T NetBond, Comcast, Equinix, niveau 3 kommunikation, Zayo gruppe |
| **Dallas** | AT & T NetBond, Cologix, Equinix, niveau 3 kommunikation Megaport |
| **Dublin** | Colt, Telecity gruppe |
| **Hongkong** | British Telecom, Kina Telecom globale Equinix Megaport, Orange, og PCCW Global begrænset, Tata kommunikation Verizon |
| **London** | AT & T NetBond British Telecom Colt, Equinix, InterCloud, og Internet Solutions - sky oprette forbindelse, Interxion, Jisc, niveau 3 kommunikation, MTN, NTT kommunikation, Orange, Tata kommunikation, Telecity gruppe, Telenor, Verizon, Vodafone |
| **Las Vegas** | Niveau 3 kommunikation +, Megaport
| **Los Angeles** | CoreSite, Equinix, Megaport, NTT, Zayo Group |
| **Melbourne** | AARNet, Equinix, Megaport, NEXTDC, Telstra Corporation |
| **New York** | Equinix, Megaport, Zayo Group |
| **Newport(Wales) +** | Næste generering af Data + |
| **Montreal** | Cologix + |
| **Mumbai** | Tata kommunikation |
| **Osaka** | Equinix, Internet Initiative Japan Inc. - IIJ, NTT kommunikation, Softbank |
| **Paris** | Interxion, Equinix + |
| **Sao Paulo** | Equinix, Telefonica |
| **Seattle** | Equinix, niveau 3 kommunikation Megaport |
| **Silicon Valley** | Aryaka netværk, AT & T NetBond British Telecom CenturyLink +, Comcast, Equinix, og niveau 3 kommunikation, Orange, Tata kommunikation, Verizon, Zayo Group |
| **Singapore** | Aryaka netværk, AT & T NetBond British Telecom Equinix, InterCloud, Megaport, Orange, SingTel, Tata kommunikation, og Verizon |
| **Sydney** | AARNet, AT & T NetBond, British Telecom, Equinix, Megaport, NEXTDC, Orange, Telstra Corporation, Verizon |
| **Tokyo** | Aryaka netværk, British Telecom, Colt, Equinix, Internet Initiative Japan Inc. - IIJ, NTT kommunikation, Softbank, Verizon |
| **Århus** | Cologix, Equinix, Zayo Group |
| **Washington DC** | Aryaka netværk, AT & T NetBond, British Telecom, Comcast, Equinix, InterCloud, niveau 3 kommunikation, Megaport, Orange, Tata kommunikation, Verizon, Zayo Group |

 **+**Angiver kommer snart

### <a name="national-cloud-environments"></a>Nationalt skyen miljøer

#### <a name="us-government-cloud"></a>Amerikanske regering skyen

| **Placering**  |**Tjenesteudbydere** |
|---------------|--------------------|
| **Chicago** | AT & T NetBond, Equinix, niveau 3 kommunikation Verizon |
| **Dallas** |  Equinix, Verizon + |
| **New York** | Equinix, niveau 3 kommunikation +, Verizon |
| **Washington DC** | AT & T NetBond, Equinix, niveau 3 kommunikation Verizon |

#### <a name="china"></a>Kina

| **Placering**  | **Tjenesteudbydere** |
|---------------|-----------------------|
| **Beijing** | Kina Telecom |
| **Shanghai** |  Kina Telecom |
Hvis du vil vide mere, skal du se [ExpressRoute i Kina](http://www.windowsazure.cn/home/features/expressroute/)

#### <a name="germany"></a>Tyskland

| **Placering**  | **Tjenesteudbydere** |
|---------------|-----------------------|
| **Berlin** | Colt +, e-overdækket |
| **Frankfurt** | Colt, Equinix, Interxion |

## <a name="nonpartners"></a>Forbindelsen via tjenesteudbydere ikke vises på listen

Hvis din forbindelse udbyder ikke vises i forrige afsnit, kan du stadig oprette en forbindelse.

- Spørg udbyderen forbindelse for at se, om de er forbundet til en af udveksling i ovenstående tabel. Du kan se linkene nedenfor for at indsamle flere oplysninger om services, som exchange udbydere. Flere connectivity udbydere er allerede tilsluttet Ethernet udveksling.

    - [Equinix sky Exchange](http://www.equinix.com/services/interconnection-connectivity/cloud-exchange/)
    - [TeleCity CloudIX](http://www.telecitygroup.com/colocation-services/cloud-ix.htm)
    - [InterXion](http://www.interxion.com/)
    - [NextDC](http://www.nextdc.com/)
    - [CoreSite](http://www.coresite.com/)
    - [Cologix](http://www.cologix.com/)
- Har udbyderen connectivity udvide dit netværk til peering placeringen af valg.
    - Sørg for, at udbyderen connectivity udvider forbindelsen på en meget tilgængelige måde, så der er ingen enkelt punkter af fejl.
- Bestil et ExpressRoute kredsløb med exchange som udbyderen forbindelse for at kunne oprette forbindelse til Microsoft.
    - Følg trinnene i [oprette et ExpressRoute kredsløb](expressroute-howto-circuit-classic.md) til at konfigurere forbindelsen.

|**Placering**|**Exchange**|**Udbydere af forbindelse**|
|-------------|------------|-------------------------|
| **New York** | Equinix | Lightower |
| **Seattle** | Equinix | Alaska kommunikation |
| **Silicon Valley** | Equinix | XO kommunikation |
| **Singapore** | Equinix | 1CLOUDSTAR |
| **Washington DC** | Equinix | Lightower |

## <a name="expressroute-system-integrators"></a>ExpressRoute system ændres

Aktivere privat forbindelse til dine behov kan være en udfordring, baseret på skalaen på dit netværk. Du kan arbejde med en af de systemintegratorer, der er angivet i følgende tabel for at hjælpe dig med at onboarding til ExpressRoute.

|**Kontinent**|**System ændres**|
|-------------|---------------------|
| **Asien** | Avanade Inc., OneAs1a|
| **Europe** | Avanade Inc., Dotnet løsninger|
| **OS** | Avanade Inc. Equinix Professional Services Perficient, og Project førerskab|

## <a name="next-steps"></a>Næste trin

- Du kan finde flere oplysninger om ExpressRoute, [ExpressRoute ofte stillede spørgsmål](expressroute-faqs.md).
- Sørg for, at alle forudsætninger er opfyldt. Se [ExpressRoute forudsætninger](expressroute-prerequisites.md).

<!--Image References-->
[0]: ./media/expressroute-locations/expressroute-locations-map.png "Placering kort"
