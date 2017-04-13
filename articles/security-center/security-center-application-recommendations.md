<properties
   pageTitle="Beskytte dine programmer i Azure Sikkerhedscenter | Microsoft Azure"
   description="Dette dokument adresser anbefalinger i Azure Security Center, der hjælper dig med at beskytte dine Azure-programmer og holde i overensstemmelse med sikkerhedspolitikker."
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

# <a name="protecting-your-applications-in-azure-security-center"></a>Beskytte dine programmer i Azure Sikkerhedscenter

Azure Sikkerhedscenter analyserer tilstanden sikkerhed for ressourcerne Azure. Når Sikkerhedscenter identificerer potentielle sikkerhedsrisici, opretter anbefalinger, der fører dig gennem processen med at konfigurere nødvendige kontrolelementer.  Anvende anbefalinger til Azure ressourcetyper: virtuelle maskiner (VM'er), netværk, SQL og programmer.

I denne artikel omhandler anbefalinger, der gælder for programmer.  Programmet anbefalinger center rundt om installation af en firewall til webprogrammer.  Brug tabellen nedenfor som en reference til at hjælpe dig med at forstå de tilgængelige programmet anbefalinger, og hvad hver enkelt skal gøre, hvis du har anvendt den.

## <a name="available-application-recommendations"></a>Tilgængelige programmet anbefalinger

|Anbefaling|Beskrivelse|
|-----|-----|
|[Tilføje en firewall til webprogrammer](security-center-add-web-application-firewall.md)|Anbefaler, at du installerer en firewall til webprogrammer (WAF) for web slutpunkter. Du kan beskytte flere webprogrammer i Sikkerhedscenter ved at tilføje disse programmer til din eksisterende WAF installationer. WAF anvendelser (oprettet ved hjælp af Ressourcestyring implementeringsmodel) skal være installeret på et separat virtuelt netværk. WAF anvendelser (oprettet ved hjælp af den klassiske implementeringsmodel) er begrænset til at bruge en netværk sikkerhedsgruppe. Denne understøttelse udvides til en fuldt tilpassede installation af en WAF maskinen (klassisk) i fremtiden.|
|[Færdiggøre programmet beskyttelse](security-center-add-web-application-firewall.md#finalize-application-protection)|For at fuldføre konfigurationen af en WAF skal blive trafik omdirigeret til WAF maskinen. Følge denne anbefaling fuldfører konfiguration af nødvendige ændringer.|

## <a name="see-also"></a>Se også

Hvis du vil vide mere om anbefalinger, der gælder for andre Azure ressourcetyper, skal du se følgende:

- [Beskytte din virtuelle maskiner i Azure Sikkerhedscenter](security-center-virtual-machine-recommendations.md)
- [Beskytte dit netværk i Azure Security Center](security-center-network-recommendations.md)
- [Beskytte din Azure SQL-tjeneste i Azure Sikkerhedscenter](security-center-sql-service-recommendations.md)

Hvis du vil vide mere om Sikkerhedscenter, skal du se følgende:

- [Angive sikkerhedspolitikker i Azure Sikkerhedscenter](security-center-policies.md) – Lær, hvordan du konfigurere sikkerhedspolitikker for dine Azure abonnementer og grupper.
- [Administrere og besvare sikkerhedsadvarsler i Azure Sikkerhedscenter](security-center-managing-and-responding-alerts.md) – Lær at administrere og reagere på beskeder om sikkerhed.
- [Azure Security Center ofte stillede spørgsmål](security-center-faq.md) – Find ofte stillede spørgsmål om ved hjælp af tjenesten.
