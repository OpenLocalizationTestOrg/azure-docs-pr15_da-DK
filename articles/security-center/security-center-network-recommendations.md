<properties
   pageTitle="Beskytte dit netværk i Azure Sikkerhedscenter | Microsoft Azure"
   description="Dette dokument adresser anbefalinger i Azure Security Center, der hjælper dig med at beskytte netværket Azure og holde i overensstemmelse med sikkerhedspolitikker."
   services="security-center"
   documentationCenter="na"
   authors="TerryLanfear"
   manager="MBaldwin"
   editor=""/>

<tags
   ms.service="security-center"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="08/04/2016"
   ms.author="terrylan"/>

# <a name="protecting-your-network-in-azure-security-center"></a>Beskytte dit netværk i Azure Security Center

Azure Sikkerhedscenter analyserer tilstanden sikkerhed for ressourcerne Azure. Når Sikkerhedscenter identificerer potentielle sikkerhedsrisici, opretter anbefalinger, der fører dig gennem processen med at konfigurere nødvendige kontrolelementer.  Anvende anbefalinger til Azure ressourcetyper: virtuelle maskiner (VM'er), netværk, SQL og programmer.

I denne artikel omhandler anbefalinger, der gælder for dit netværk.  Netværk anbefalinger center omkring næste generering af firewalls, netværk sikkerhedsgrupper, konfigurere indgående trafikregler og mere.  Brug tabellen nedenfor som en reference til at hjælpe dig med at forstå tilgængeligt netværk anbefalinger, og hvad hver enkelt vil gøre, hvis du har anvendt den.

## <a name="available-network-recommendations"></a>Tilgængeligt netværk anbefalinger

|Anbefaling|Beskrivelse|
|-----|-----|
|[Tilføje en næste generering af Firewall](security-center-add-next-generation-firewall.md)|Anbefaler, at du føjer en næste generering af Firewall (NGFW) fra en Microsoft-partner for at øge din sikkerhed beskyttelse.|
|[Distribuere-trafik gennem NGFW kun](security-center-add-next-generation-firewall.md#route-traffic-through-ngfw-only)|Anbefaler, at du konfigurerer netværk sikkerhed gruppe (NSG) regler, der tvinger indgående trafik til dit VM gennem din NGFW.|
|[Aktivere netværk sikkerhedsgrupper på undernet eller virtuelle maskiner](security-center-enable-network-security-groups.md)|Anbefaler, at du aktiverer NSGs på undernet eller FOS.|
|[Begrænse adgang via internettet modstående slutpunkt](security-center-restrict-access-through-internet-facing-endpoints.md)|Anbefaler, at du konfigurerer indgående trafikregler for NSGs.|

## <a name="see-also"></a>Se også

Hvis du vil vide mere om anbefalinger, der gælder for andre Azure ressourcetyper, skal du se følgende:

- [Beskytte din virtuelle maskiner i Azure Sikkerhedscenter](security-center-virtual-machine-recommendations.md)
- [Beskytte dine programmer i Azure Sikkerhedscenter](security-center-application-recommendations.md)
- [Beskytte din Azure SQL-tjeneste i Azure Sikkerhedscenter](security-center-sql-service-recommendations.md)

Hvis du vil vide mere om Sikkerhedscenter, skal du se følgende:

- [Angive sikkerhedspolitikker i Azure Sikkerhedscenter](security-center-policies.md) – Lær, hvordan du konfigurere sikkerhedspolitikker for dine Azure abonnementer og grupper.
- [Administrere og besvare sikkerhedsadvarsler i Azure Sikkerhedscenter](security-center-managing-and-responding-alerts.md) – Lær at administrere og reagere på beskeder om sikkerhed.
- [Azure Security Center ofte stillede spørgsmål](security-center-faq.md) – Find ofte stillede spørgsmål om ved hjælp af tjenesten.
