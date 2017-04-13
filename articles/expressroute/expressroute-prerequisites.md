<properties
   pageTitle="Forudsætninger for ExpressRoute indføring | Microsoft Azure"
   description="Denne side indeholder en liste over krav til at være opfyldt, før du kan bestille et Azure ExpressRoute kredsløb."
   documentationCenter="na"
   services="expressroute"
   authors="cherylmc"
   manager="carmonm"
   editor=""/>
<tags
   ms.service="expressroute"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="10/10/2016"
   ms.author="cherylmc"/>


# <a name="expressroute-prerequisites--checklist"></a>ExpressRoute forudsætninger og tjekliste  

Hvis du vil oprette forbindelse til Microsofts skytjenester ved hjælp af ExpressRoute, skal du kontrollere, at følgende krav er angivet i afsnittene herunder er opfyldt.

[AZURE.INCLUDE [expressroute-office365-include](../../includes/expressroute-office365-include.md)]

## <a name="azure-account"></a>Azure-konto

- En gyldig og aktive Microsoft Azure-konto. Dette er påkrævet for at konfigurere ExpressRoute kredsløb. ExpressRoute kredsløb er ressourcer i Azure abonnementer. Et Azure-abonnement er et krav, selvom det er begrænset til Azure Microsoft cloud services, som Office 365-tjenester og CRM online.
- Et aktivt Office 365-abonnement (hvis ved hjælp af Office 365-tjenester). Se afsnittet [Office 365 specifikke krav](#office-365-specific-requirements) i denne artikel for at få mere at vide.

## <a name="connectivity-provider"></a>Connectivity udbyder
- Du kan arbejde med en [ExpressRoute connectivity partner](expressroute-locations.md#partners) til at oprette forbindelse til Microsoft skyen. Du kan konfigurere en forbindelse mellem dit lokale netværk og Microsoft på [tre måder](expressroute-introduction.md#howtoconnect). 
- Hvis din udbyder ikke er en ExpressRoute connectivity partner, kan du stadig oprette forbindelse til Microsoft cloud via en [udbyder af sky exchange](expressroute-locations.md#nonpartners).

## <a name="network-requirements"></a>Netværkskrav til
- **Overflødige connectivity**: der er ingen redundans krav på fysisk forbindelse mellem dig og din udbyder. Microsoft kræver overflødige BGP sessioner konfigureres mellem Microsofts router og peering routerne, selv når du har kun [én fysisk forbindelse til en sky exchange](expressroute-faqs.md#onep2plink). 
- **Routing**: afhængigt af hvordan du opretter forbindelse til Microsoft Cloud, du eller din udbyder har brug for at konfigurere og administrere BGP sessioner for [routing domæner](expressroute-circuit-peerings.md). Nogle Ethernet connectivity provider eller sky exchange provider kan tilbyde BGP administration som en værdi tilføjelse af tjenester.
- **NAT**: Microsoft accepterer kun offentlige IP-adresser via Microsoft peering. Hvis du bruger private IP-adresser i dit lokale netværk, adresser du eller din udbyder behov for at oversætte de private IP-adresser til den offentlige IP- [ved hjælp af NAT](expressroute-nat.md).
- **QoS**: Skype for Business har forskellige tjenester (fx tale, video-og tekst), der kræver opdelte QoS behandling. Du og din udbyder bør følge [QoS krav](expressroute-qos.md).
- **Netværkssikkerhed**: Du skal overveje [Netværkssikkerhed](../best-practices-network-security.md) , ved at oprette forbindelse til Microsoft Cloud via ExpressRoute.
 
## <a name="office-365"></a>Office 365

Hvis du planlægger at aktivere Office 365 på ExpressRoute, skal du gennemgå følgende dokumenter kan finde flere oplysninger om kravene til Office 365.


- [Oversigt over ExpressRoute til Office 365](https://support.office.com/en-us/article/Azure-ExpressRoute-for-Office-365-6d2534a2-c19c-4a99-be5e-33a0cee5d3bd)
- [Rute med ExpressRoute til Office 365](https://support.office.com/en-us/article/Routing-with-ExpressRoute-for-Office-365-e1da26c6-2d39-4379-af6f-4da213218408)
- [Office 365 URL-adresser og IP-adresseområder](https://support.office.com/en-us/article/Office-365-URLs-and-IP-address-ranges-8548a211-3fe7-47cb-abb1-355ea5aa88a2)
- [Netværksplanlægning og justering af ydeevnen for Office 365](https://support.office.com/en-us/article/Network-planning-and-performance-tuning-for-Office-365-e5f1228c-da3c-4654-bf16-d163daee8848)
- [Netværksbåndbreddeberegnere og værktøjer](https://support.office.com/en-us/article/Network-and-migration-planning-for-Office-365-f5ee6c33-bcd7-4b0b-b0f8-dc1d9fb8d132)
- [Office 365 integration i lokale miljøer](https://support.office.com/en-us/article/Office-365-integration-with-on-premises-environments-263faf8d-aa21-428b-aed3-2021837a4b65)

## <a name="crm-online"></a>CRM Online 
Hvis du vil aktivere CRM Online på ExpressRoute, skal du gennemgå følgende dokumenter kan finde flere oplysninger om CRM Online

- [CRM Online URL-adresser](https://support.microsoft.com/kb/2655102) og [IP-adresseområder](https://support.microsoft.com/kb/2728473)

## <a name="next-steps"></a>Næste trin

- Du kan finde flere oplysninger om ExpressRoute, [ExpressRoute ofte stillede spørgsmål](expressroute-faqs.md).
- Find en udbyder af ExpressRoute connectivity. Se [ExpressRoute partnere og peering placeringer](expressroute-locations.md).
- Se krav til [distribution](expressroute-routing.md), [NAT](expressroute-nat.md) og [QoS](expressroute-qos.md).
- Konfigurere ExpressRoute forbindelsen.
    - [Oprette et ExpressRoute kredsløb](expressroute-howto-circuit-classic.md)
    - [Konfigurere routing](expressroute-howto-routing-classic.md)
    - [Sammenkæde en VNet med et ExpressRoute kredsløb](expressroute-howto-linkvnet-classic.md)

