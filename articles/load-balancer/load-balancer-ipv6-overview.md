<properties
    pageTitle="Oversigt over IPv6 til Azure justering af belastning | Microsoft Azure"
    description="Forstå IPv6-understøttelse af Azure belastning og Indlæs afstemmes FOS."
    services="load-balancer"
    documentationCenter="na"
    authors="sdwheeler"
    manager="carmonm"
    editor=""
    keywords="IPv6-azure justering af belastning dobbelt stak, offentlige IP-adresse, oprindelige ipv6, mobile, og iot"
/>
<tags
    ms.service="load-balancer"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="infrastructure-services"
    ms.date="09/14/2016"
    ms.author="sewhee"
/>

# <a name="overview-of-ipv6-for-azure-load-balancer"></a>Oversigt over IPv6 til Azure justering af belastning

Internettet Indlæs balancere kan også anvendes sammen med en IPv6-adresse. Ud over IPv4 connectivity, det giver mulighed for følgende funktioner:

* Oprindelig til slut IPv6-forbindelse mellem offentlige Internet-klienter og virtuelle Azure-computere (FOS) gennem justering af belastning.
* Oprindelig til slut IPv6 udgående forbindelsen mellem FOS og offentlige Internet IPv6-aktiverede klienter.

Følgende billede viser IPv6-funktionaliteten for Azure justering af belastning.

![Azure justering af belastning, IPv6](./media/load-balancer-ipv6-overview/load-balancer-ipv6.png)

Når installeret, kan en IPv4 eller IPv6-aktiverede Internet-klient kommunikere med offentlig IPv4 eller IPv6-adresser (eller værtsnavne) af den Azure internettet justering af belastning. Justering af belastning dirigerer IPv6-pakker til de private IPv6-adresser for FOS ved hjælp af NAT (NAT). IPv6-Internet-klient kan ikke kommunikere direkte med IPv6-adressen på FOS.

## <a name="features"></a>Funktioner

IPv6-understøttelse af FOS installeres via Azure ressourcestyring indeholder:

1. Netværksbelastningen IPv6-tjenester til IPv6-klienter på internettet
2. Oprindelig IPv6 og IPv4 slutpunkterne på FOS ("to stablet")
3. Indgående og udgående startet oprindelige IPv6-forbindelser
4. Understøttede protokoller som TCP, UDP og HTTP (S) aktivere et bredt udvalg af tjenesten arkitekturer

## <a name="benefits"></a>Fordele

Denne funktion gør det muligt for følgende vigtige fordele:

* Overholde government krav, der kræver, at nye programmer er tilgængelige for at kun IPv6-klienter
* Aktivér mobile og Internet ting (IOT) udviklere at bruge to stablet (IPv4 + IPv6) virtuelle Azure-computere for at udbedre voksende mobile & IOT markeder

## <a name="details-and-limitations"></a>Detaljer og begrænsninger

Detaljer

* Azure DNS-tjenesten indeholder både IPv4 A og IPv6 AAAA navneposter og svarer med både poster for justering af belastning. Klienten vælger hvilke adresse (IPv4 eller IPv6) til at kommunikere med.
* Når en VM starter en forbindelse til en offentlig Internet IPv6-forbindelse enhed, er hvor VM kilde IPv6-adresse netværksadresse oversættes (NAT) til den offentlige IPv6-adresse på justering af belastning.
* VM'er, der kører Linux-operativsystemet skal være konfigureret til at modtage en IPv6-IP-adresse via DHCP. Mange af Linux billederne i galleriet Azure er allerede konfigureret til at understøtte IPv6 uden ændringer. Se [Konfigurere DHCPv6 for Linux FOS](load-balancer-ipv6-for-linux.md) kan finde flere oplysninger
* Hvis du vælger at bruge en tilstand efterprøvning af af med dine justering af belastning, oprette en IPv4 efterprøvning af af og bruge det sammen med både IPv4 og IPv6 slutpunkter. Hvis tjenesten på din VM går ned, hentes både IPv4 og IPv6 slutpunkter af rotation.

Begrænsninger

* Du kan ikke tilføje IPv6-regler for indlæsning af justering af belastning på portalen Azure. Reglerne, der kan kun oprettes gennem skabelonen, CLI, PowerShell.
* Du kan ikke opgradere eksisterende FOS for at bruge IPv6-adresser. Du skal installere nye FOS.
* En enkelt IPv6-adresse kan tildeles til en enkelt netværksgrænseflade i hver VM.
* De offentlige IPv6-adresser kan ikke tildeles til en VM. De kan kun tildeles til en justering af belastning.
* Du kan ikke konfigurere DNS-opslag til din offentlige IPv6-adresser.
* FOS med disse IPv6-adresser må ikke være medlemmer af en Azure skybaseret tjeneste. De kan være forbundet til et virtuelt Azure-netværk (VNet) og kommunikere med hinanden over deres IPv4-adresser.
* Privat IPv6-adresser kan være installeret på individuelle FOS i en ressourcegruppe, men kan ikke installeres i en ressourcegruppe via skala sæt.
* Azure FOS kan ikke oprette forbindelse via IPv6 til andre FOS, andre Azure tjenester eller lokale enheder. De kan kun kommunikere med Azure justering af belastning over IPv6. Men de kan kommunikere med disse andre ressourcer, ved hjælp af IPv4.
* IPv4-sikkerhedsgruppe netværk (NSG) beskyttelse understøttes i dobbelt stak (IPv4 + IPv6) installationer. NSGs gælder ikke for IPv6 slutpunkterne.
* IPv6-slutpunktet på VM vises ikke direkte til internettet. Det er bag en justering af belastning. Kun de porte, der er angivet i reglerne for indlæsning af belastningsjusteringstjenesten er tilgængelige via IPv6.
* Ændre parameteren IdleTimeout til IPv6 er **understøttes ikke i øjeblikket**. Standard er fire minutter.

## <a name="next-steps"></a>Næste trin

Lær at implementere en justering af belastning, IPv6.

* [Tilgængeligheden af IPv6 efter område](https://go.microsoft.com/fwlink/?linkid=828357)
* [Installere en belastningsjustering med IPv6 ved hjælp af en skabelon](load-balancer-ipv6-internet-template.md)
* [Installere en belastningsjustering med IPv6 ved hjælp af Azure PowerShell](load-balancer-ipv6-internet-ps.md)
* [Installere en belastningsjustering med IPv6 ved hjælp af Azure CLI](load-balancer-ipv6-internet-cli.md)
