
<properties
   pageTitle="Azure virtuelt netværk peering | Microsoft Azure"
   description="Få mere at vide om VNet peering i Azure."
   services="virtual-network"
   documentationCenter="na"
   authors="NarayanAnnamalai"
   manager="jefco"
   editor="tysonn" />
<tags
   ms.service="virtual-network"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="10/17/2016"
   ms.author="narayan" />

# <a name="vnet-peering"></a>VNet peering

VNet peering er en funktion, der forbinder to virtuelle netværk (VNets) i det samme område via netværket Azure grundlæggende element. Når peered, vises de to virtuelle netværk som en for alle connectivity formål. De administreres stadig som separate ressourcer, men virtuelle maskiner i disse virtuelle netværk kan kommunikere med hinanden direkte ved hjælp af private IP-adresser.

Trafik mellem virtuelle maskiner i peered virtuelle netværk er distribueret gennem Azure infrastrukturen samme måde, som er distribueret trafik mellem FOS i det samme virtuelle netværk. Nogle af fordelene ved at bruge VNet peering omfatter:

- En lille ventetid, høj båndbredde forbindelse mellem ressourcer i forskellige virtuelle netværk.
- Muligheden for at bruge ressourcer som netværk anvendelser og VPN gateways som overførsel punkter i en peered VNet.
- Muligheden for at oprette forbindelse et virtuelt netværk, der bruger Azure ressourcestyring modellen til et virtuelt netværk, der bruger den klassiske implementeringsmodel og aktivere fuld forbindelsen mellem ressourcer i disse virtuelle netværk.

Krav og vigtige aspekter af VNet peering:

- De to virtuelle netværk, der er peered skal være i samme Azure område.
- De virtuelle netværk, der er peered har ikke overlapper IP-adresse mellemrum.
- VNet peering er mellem to virtuelle netværk, og der er ingen afledte transitive relation. Eksempelvis hvis virtuelt netværk A peered med virtuelt netværk B, og hvis virtuelt netværk B peered med virtuelt netværk C, det ikke kan oversættes til virtuelle netværk et der peered med virtuelt netværk C.
- Peering kan oprettes mellem virtuelle netværk i to forskellige abonnementer som lang tid en privilegerede brugere af begge abonnementer godkender den peering, og hvilke abonnementer der er knyttet til den samme Active Directory-lejer. 
- Peering mellem virtuelt netværk i ressource manager model og klassisk implementeringsmodel kræver, at VNets skal være i samme-abonnement.
- Et virtuelt netværk, der bruger implementeringsmodel ressourceleder, der kan være peered med en anden virtuelt netværk, der bruger denne model, eller med et virtuelt netværk, der bruger den klassiske implementeringsmodel. Virtuelt netværk, der bruger den klassiske implementeringsmodel kan dog være peered til hinanden.
- Selvom kommunikationen mellem virtuelle maskiner i peered virtuelle netværk har ingen ekstra båndbredde begrænsninger, gælder stadig båndbredde knop baseret på VM størrelse.


![Grundlæggende VNet peering](./media/virtual-networks-peering-overview/figure01.png)

## <a name="connectivity"></a>Forbindelse
Når to virtuelle netværk er peered, kan en virtuel maskine (web/arbejder rolle) i det virtuelle netværk direkte oprette forbindelse med andre virtuelle maskiner i det peered virtuelle netværk. Disse to netværk har fuld IP-niveau connectivity.

Netværksventetid for en returkørsel mellem to virtuelle maskiner i peered virtuelle netværk er den samme som for en returkørsel i et lokalt virtuelle netværk. Netværkets hastighed er baseret på den båndbredde, der er tilladt for den virtuelle maskine stå i forhold til dets størrelse. Der ikke er en hvilken som helst yderligere begrænsninger på båndbredde.

Trafik mellem virtuelle maskiner i peered virtuelle netværk er distribueret direkte via Azure back end-infrastrukturen og ikke gennem en gateway.

Virtuelle maskiner i et virtuelt netværk kan få adgang til interne netværksbelastningen (ILB) slutpunkterne i det peered virtuelle netværk. Netværk sikkerhedsgrupper (NSGs) kan anvendes i et virtuelt netværk til at blokere adgang til andre virtuelle netværk eller undernet, hvis du ønsker.

Når brugere konfigurerer peering, kan de åbne eller lukke NSG reglerne mellem de virtuelle netværk. Hvis brugeren vælger at åbne hele forbindelsen mellem peered virtuelle netværk (som er standardindstillingen), kan de derefter bruge NSGs på bestemte undernet eller virtuelle maskiner til at blokere eller nægte bestemte adgang.

Azure-producenter interne DNS-navneoversættelse for virtuelle maskiner virker ikke på tværs af peered virtuelle netværk. Virtuelle maskiner har interne DNS-navne, der fortolkes kun i det lokale virtuelle netværk. Brugere kan dog konfigurere virtuelle maskiner, der kører i peered virtuelle netværk som DNS-servere for et virtuelt netværk.

## <a name="service-chaining"></a>Tjenesten sammenkædning
Brugerne kan konfigurere brugerdefinerede distribuere tabeller, der peger på virtuelle maskiner i peered virtuelle netværk som det "næste hop" IP-adresse, som vist i diagrammet senere i denne artikel. Dette gør det muligt for brugere at opnå sammenkædning af tjenesten, som de kan dirigere trafik fra et virtuelt netværk til et virtuelt enhed, der kører i et peered virtuelle netværk via brugerdefinerede distribuere tabeller.

Brugere kan også effektivt oprette hub Ege type miljøer, hvor hubben kan være vært for infrastrukturkomponenter som et netværk virtuelle maskinen. Alle de talte virtuelle netværk kan derefter peer-med den, samt et undersæt af trafik til udstyr, der kører i det virtuelle netværk hub. Kort sagt giver VNet peering gør det næste hop IP-adressen på den 'brugerdefinerede distribuere tabel' skal være en virtuel maskine i det peered virtuelle netværk IP-adresse.

## <a name="gateways-and-on-premises-connectivity"></a>Gateways og lokal forbindelse
Hver virtuelt netværk, uanset om det er peered med en anden virtuelt netværk og kan stadig har sin egen gateway og bruge det til at oprette forbindelse til en lokal installation. Brugere kan også konfigurere [VNet-VNet forbindelser](../vpn-gateway/vpn-gateway-vnet-vnet-rm-ps.md) ved hjælp af gateways, selvom de virtuelle netværk er peered.

Når begge muligheder for virtuelt netværk integration er konfigureret, trafik mellem de virtuelle netværk bevæger sig gennem konfigurationen af peering (det vil sige, via Azure grundlæggende element).

Når virtuelle netværk er peered, kan brugere også konfigurere gatewayen i det peered virtuelle netværk som en overførsel til en lokal installation. I dette tilfælde kan ikke det virtuelle netværk, der bruger en ekstern gateway have sin egen gateway. Et virtuelt netværk kan have kun én gateway. Det kan enten være en lokal gateway eller en ekstern gateway (i peered virtuelle netværket), som vist i følgende illustration.

Overførsel af gatewayen understøttes ikke i peering relationen mellem virtuelle netværk ved hjælp af Ressourcestyring modellen og dem, der bruger den klassiske implementeringsmodel. Begge virtuelle netværk i peering relationen skal bruge implementeringsmodel Ressourcestyring for en gateway-overførsel til at arbejde.

Når de virtuelle netværk, der deler en enkelt Azure ExpressRoute forbindelse er peered, trafik mellem dem gennemgår peering relationen (det vil sige, via Azure grundlæggende element netværket). Brugerne kan stadig bruge lokale gateways i hver virtuelt netværk til at oprette forbindelse til den lokale kredsløb. De kan også bruge en delt gateway og konfigurere overførsel til lokal forbindelse.

![VNet peering overførsel](./media/virtual-networks-peering-overview/figure02.png)

## <a name="provisioning"></a>Klargøring
VNet peering er en privilegerede handling. Det er en særskilt funktion under navneområdet VirtualNetworks. En bruger kan tildeles specifikke rettigheder til at tillade peering. En bruger, der har læse / skrive-adgang til det virtuelle netværk arver automatisk disse rettigheder.

En bruger, der er enten en administrator eller en privilegerede brugere af muligheden for peering kan starte en peering handling på en anden VNet. Hvis der er en matchende anmodning om peering på anden siden, og andre krav er opfyldt, oprettes den peering.

Se artiklerne i afsnittet "Næste trin" for at få flere oplysninger om hvordan du opretter VNet peering mellem to virtuelle netværk.

## <a name="limits"></a>Begrænsninger
Der er begrænsninger på antallet af peerings, der er tilladt for et enkelt virtuelt netværk. Referere til [Azure netværk begrænsninger](../azure-subscription-service-limits.md#networking-limits) kan finde flere oplysninger.

## <a name="pricing"></a>Priser
VNet peering vil være gratis i perioden, gennemse. Efter den er udgivet, vises der et nominel gebyr på indgangs- og udgangspunkt trafik, der anvender den peering. Yderligere oplysninger finder du referere til [priser side](https://azure.microsoft.com/pricing/details/virtual-network).


## <a name="next-steps"></a>Næste trin
- [Konfigurere peering mellem virtuelle netværk](virtual-networks-create-vnetpeering-arm-portal.md).
- Få mere at vide om [NSGs](virtual-networks-nsg.md).
- Få mere at vide om [brugerdefinerede omdirigerer og IP-videresendelse](virtual-networks-udr-overview.md).
