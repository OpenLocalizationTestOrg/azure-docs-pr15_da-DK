<properties
    pageTitle="Netværk infrastruktur retningslinjer | Microsoft Azure"
    description="Få mere at vide om de vigtigste design og implementering retningslinjer for installation af virtuelle netværk i Azure infrastrukturtjenester."
    documentationCenter=""
    services="virtual-machines-linux"
    authors="iainfoulds"
    manager="timlt"
    editor=""
    tags="azure-resource-manager"/>

<tags
    ms.service="virtual-machines-linux"
    ms.workload="infrastructure-services"
    ms.tgt_pltfrm="vm-linux"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/08/2016"
    ms.author="iainfou"/>

# <a name="networking-infrastructure-guidelines"></a>Netværk infrastruktur retningslinjer

[AZURE.INCLUDE [virtual-machines-linux-infrastructure-guidelines-intro](../../includes/virtual-machines-linux-infrastructure-guidelines-intro.md)] 

I denne artikel fokuserer på forstå de nødvendige planlægning trin til virtuelt netværk i Azure og forbindelse mellem eksisterende lokalt miljøer.


## <a name="implementation-guidelines-for-virtual-networks"></a>Implementering retningslinjer for virtuelle netværk

Beslutninger:

- Hvilken type virtuelt netværk skal du være vært for din IT arbejdsbelastningen eller infrastruktur (kun skyen eller kryds-premises)?
- For virtuelle netværk i tværs det lokale miljø, hvor meget adresseområde skal du være vært for undernet og FOS nu og til begrundet udvidelse i fremtiden?
- Vil du oprette centralt virtuelle netværk eller oprette individuelle virtuelle netværk for hver ressourcegruppe?

Opgaver:

- Definere adresseområde til de virtuelle netværk, der skal oprettes.
- Definere sættet af undernet og adresseområder for hver.
- Definere sættet af lokale netværk adresse mellemrum til de lokale placeringer, som FOS i det virtuelle netværk skal have fat i tværs lokale virtuelle netværk.
- Arbejde med lokale netværk team til at sikre den korrekte routing er konfigureret, når oprette tværs lokale virtuelle netværk.
- Oprette det virtuelle netværk ved hjælp af din navngivningskonvention.


## <a name="virtual-networks"></a>Virtuelt netværk

Virtuelt netværk er nødvendige for at understøtte kommunikationen mellem virtuelle maskiner (VM'er). Du kan definere undernet, brugerdefineret IP-adresse, DNS-indstillingerne, sikkerhed, filtrering og indlæse justering, som med fysiske netværk. Ved hjælp af en [VPN-Gateway](../vpn-gateway/vpn-gateway-about-vpngateways.md) eller [Express distribuere kredsløb](../expressroute/expressroute-introduction.md), kan du oprette forbindelse Azure virtuelle netværk til dit lokale netværk. Du kan læse mere om [virtuelle netværk og deres komponenter](../virtual-network/virtual-networks-overview.md).

Ved hjælp af grupper, har du opnå større fleksibilitet, hvordan du designer dit virtuelle netværk komponenter. FOS kan oprette forbindelse til virtuelle netværk uden for deres egne ressourcegruppe. Et almindelige design fremgangsmåde er at oprette centraliserede ressourcegrupper, der indeholder dine core netværksinfrastruktur, som kan administreres af en fælles gruppe. FOS og deres programmer, der er installeret på separate grupper. Denne fremgangsmåde giver programmet ejere adgang til ressourcegruppen, der indeholder deres FOS uden at åbne for adgang til konfigurationen af de bredere virtuelle netværk ressourcer.

## <a name="site-connectivity"></a>Websted connectivity

### <a name="cloud-only-virtual-networks"></a>Kun skyen virtuelle netværk
Hvis lokale brugere og computere ikke kræver igangværende forbindelse til FOS i en Azure virtuelt netværk, er din virtuelle netværksdesign direkte frem:

![Kun skyen virtuelle standardnetværksdiagram](./media/virtual-machines-common-infrastructure-service-guidelines/vnet01.png)

Denne metode er typisk til internettet arbejdsbelastninger, som en internetbaserede webserver. Du kan administrere disse FOS ved hjælp af SSH eller punkt-til-site VPN-forbindelser.

Da de ikke oprette forbindelse til dit lokale netværk, kan kun Azure virtuelle netværk bruge en hvilken som helst del af private IP-adresseområder. Adresseområdet kan være det samme private område, der er i brug i det lokale miljø.


### <a name="cross-premises-virtual-networks"></a>Tværs lokale virtuelle netværk
Hvis lokale brugere og computere kræver igangværende forbindelse til FOS i en Azure virtuelt netværk, kan du oprette et virtuelt netværk i tværs det lokale miljø. Oprette forbindelse det virtuelle netværk til dit lokale netværk med en ExpressRoute eller et websted til websted VPN-forbindelse.

![Tværs lokale virtuelle netværksdiagram](./media/virtual-machines-common-infrastructure-service-guidelines/vnet02.png)

I denne konfiguration er Azure virtuelle netværket grundlæggende en skybaseret udvidelse af dit lokale netværk.

Da de opretter forbindelse til dit lokale netværk, tværs lokale virtuelle netværk skal bruge en del af det adresseområde, der anvendes af din organisation, der er entydige. På samme måde, der forskellige virksomhedens steder er tildelt en bestemt IP-undernet, bliver Azure en anden placering, som du udvide ud af dit netværk.

Hvis du vil tillade pakker til at rejse fra netværket tværs lokale virtuelle til dit lokale netværk, skal du konfigurere sæt relevante lokalt adressepræfikser som en del af den lokale netværk definition for det virtuelle netværk. Afhængigt af adresseområdet i det virtuelle netværk og sæt relevante lokalt placeringer, kan der være mange adressepræfikser i det lokale netværk.

Du kan konvertere et kun skyen virtuelt netværk til et virtuelt netværk i tværs det lokale miljø, men den mest sandsynlige kræver, at du til IP-adresse nyt din virtuelt netværk adresseområde og Azure ressourcer. Derfor nøje overveje, om et virtuelt netværk skal have forbindelse til dit lokale netværk, når du tildeler en IP-undernet.

## <a name="subnets"></a>Undernet
Undernet gør det muligt at organisere ressourcer, der er relateret, enten logisk (for eksempel, ét undernet for VM'er, der er knyttet til det samme program), eller fysisk (for eksempel, ét undernet per ressourcegruppe). Du kan også anvende undernet isolationsniveauet teknikker for ekstra sikkerhed.

For virtuelle netværk i tværs det lokale miljø, skal du designe undernet med de samme regler, du bruger til lokale ressourcer. **Azure altid bruger de første tre IP-adresser for adresseområde for hvert undernet**. For at bestemme antallet af adresser, der er behov for undernettet skal du starte med at tælle antallet af FOS, du skal bruge nu. Anslå til fremtidig vækst, og derefter bruge tabellen nedenfor til at bestemme størrelsen af undernettet.

Antallet af FOS behov | Antallet af host bit behov | Størrelsen af undernettet
--- | --- | ---
1-3 | 3 | / 29
4 – 11     | 4 | / 28
12-27 | 5 | / 27
28 – 59 | 6 | / 26
60-123 | 7 | / 25

> [AZURE.NOTE] For normal lokalt undernet er det maksimale antal host adresser til et undernet med n host bit 2<sup>n</sup> -2. For et Azure undernet er det maksimale antal host adresser til et undernet med n host bit 2<sup>n</sup> -5 (2 + 3 for de adresser, der bruger Azure på hvert undernet).

Hvis du vælger en undernet størrelse, der er for lille, du har til nyt-IP-adresse og geninstaller FOS i undernettet.


## <a name="network-security-groups"></a>Netværk sikkerhedsgrupper
Du kan anvende filtrering regler på trafik, der flyder gennem din virtuelle netværk ved hjælp af netværk sikkerhedsgrupper. Du kan oprette findelt spamfiltrering regler for at sikre dine virtuelle netværk miljø og styre indgående og udgående trafik, kilde- og IP-intervaller, tilladt porte osv. Netværk sikkerhedsgrupper kan anvendes til undernet i et virtuelt netværk eller direkte til en given virtuelt netværk-grænseflade. Det anbefales at have nogle niveau af netværk sikkerhedsgruppe filtrering trafik på dine virtuelle netværk. Du kan læse mere om [Netværk sikkerhedsgrupper](../virtual-network/virtual-networks-nsg.md).


## <a name="additional-network-components"></a>Yderligere netværkskomponenter
Som med en lokal fysisk netværksinfrastruktur, kan Azure virtuelt netværk indeholde mere end bare undernet og IP-adresser. Når du designer infrastrukturen programmet på computeren, kan du overveje at inkorporere nogle af disse yderligere komponenter:

- [VPN gateways](../vpn-gateway/vpn-gateway-about-vpngateways.md) - Kontakt Azure virtuelle netværk til andre Azure virtuelle netværk eller oprette forbindelse til lokale netværk via et websted til websted VPN-forbindelse. Implementere Express distribuere forbindelser til dedikeret, sikre forbindelser. Du kan også give brugere direkte adgang til punkt-til-Site VPN-forbindelser.
- [Justering af belastning](../load-balancer/load-balancer-overview.md) - indeholder belastning af trafik til både eksterne og interne trafik efter behov.
- [Application Gateway](../application-gateway/application-gateway-introduction.md) - HTTP belastning på programlag, give nogle yderligere fordele for webprogrammer i stedet for installation af Azure justering af belastning.
- [Trafik Manager](../traffic-manager/traffic-manager-overview.md) - DNS-baseret trafik fordeling til at dirigere slutbrugere til det nærmeste tilgængelige programmet slutpunkt, så du kan hoste dit program af Azure datacentre i forskellige områder.


## <a name="next-steps"></a>Næste trin

[AZURE.INCLUDE [virtual-machines-linux-infrastructure-guidelines-next-steps](../../includes/virtual-machines-linux-infrastructure-guidelines-next-steps.md)] 