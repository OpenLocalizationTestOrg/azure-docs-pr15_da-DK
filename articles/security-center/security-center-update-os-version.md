<properties
   pageTitle="Opdater OS version i Azure Sikkerhedscenter | Microsoft Azure"
   description="I denne artikel beskrives, hvordan at implementere Azure Sikkerhedscenter anbefaling **opdatering OS-version**."
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
   ms.date="07/29/2016"
   ms.author="terrylan"/>

# <a name="update-os-version-in-azure-security-center"></a>Opdatere OS-version i Azure Sikkerhedscenter

Til virtuelle maskiner (FOS) i skytjenester anbefaler Azure Sikkerhedscenter, at operativsystemet (OS) skal opdateres, hvis der er en nyere version.  Kun cloud services internettet og arbejder roller, der kører i fremstilling pladser overvåges.

> [AZURE.NOTE] Dette dokument introducerer tjenesten ved hjælp af en eksempel-installation.  Dette er ikke en trinvis vejledning.

## <a name="implement-the-recommendation"></a>Implementere anbefalingen

1. Vælg **Opdater OS-version**i bladet **anbefalinger** .
![Opdatere OS-version][1]

2. Dette åbner bladet **opdatering OS-version**. Følg trinnene i denne blade opdatere OS-version.

## <a name="see-also"></a>Se også

I denne artikel viste dig, hvordan at implementere Sikkerhedscenter anbefaling "Opdater OS version". Hvis du vil vide mere om skytjenester og opdatering af OS-version til en skybaseret tjeneste, i:

- [Oversigt over Services i skyen](../cloud-services/cloud-services-choose-me.md)
- [Hvordan du opdaterer en skybaseret tjeneste](../cloud-services/cloud-services-update-azure-service.md)
- [Sådan konfigureres Cloud Services](../cloud-services/cloud-services-how-to-configure-portal.md)

Hvis du vil vide mere om Sikkerhedscenter, skal du se følgende:

- [Angive sikkerhedspolitikker i Azure Sikkerhedscenter](security-center-policies.md) – Lær, hvordan du konfigurere sikkerhedspolitikker for dine Azure abonnementer og grupper.
- [Administrere sikkerhedsanbefalinger i Azure Sikkerhedscenter](security-center-recommendations.md) – Lær, hvordan anbefalinger hjælpe dig med at beskytte dine Azure ressourcer.
- [Sikkerhed sundhed overvågning i Azure Sikkerhedscenter](security-center-monitoring.md) – Lær, hvordan du overvåge tilstanden for dine Azure ressourcer.
- [Administrere og besvare sikkerhedsadvarsler i Azure Sikkerhedscenter](security-center-managing-and-responding-alerts.md) – Lær at administrere og reagere på beskeder om sikkerhed.
- [Overvågnings partnerløsninger med Azure Security Center](security-center-partner-solutions.md) – Lær, hvordan du overvåge systemtilstand status for din partnerløsninger.
- [Azure Security Center ofte stillede spørgsmål](security-center-faq.md) – Find ofte stillede spørgsmål om ved hjælp af tjenesten.
- [Azure sikkerhed blog](http://blogs.msdn.com/b/azuresecurity/) – få de seneste nyheder og Azure sikkerhed og oplysninger.

<!--Image references-->
[1]: ./media/security-center-update-os-version/update-os-version.png
