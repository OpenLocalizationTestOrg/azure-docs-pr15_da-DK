<properties
   pageTitle="Løse slutpunkt beskyttelse sundhed beskeder i Azure Sikkerhedscenter | Microsoft Azure"
   description="Dette dokument viser, hvordan du implementere Azure Sikkerhedscenter anbefaling **løse Endpoint Protection sundhed beskeder**."
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

# <a name="resolve-endpoint-protection-health-alerts-in-azure-security-center"></a>Løse slutpunkt beskyttelse sundhed beskeder i Azure Security Center

Azure Sikkerhedscenter anbefaler, at du kan løse fundet slutpunkt beskyttelse sundhed beskeder.  Sikkerhedscenter gør det muligt at se, hvilke virtuelle maskiner (VM'er) har slutpunkt beskyttelse fejl og antallet af mislykkede forsøg.

> [AZURE.NOTE] Dette dokument introducerer tjenesten ved hjælp af en eksempel-installation. Dette er ikke en trinvis vejledning.

## <a name="implement-the-recommendation"></a>Implementere anbefalingen

1. Vælg **løse Endpoint Protection sundhed beskeder**i **anbefalinger blade**.
![Løse slutpunkt beskyttelse sundhed beskeder][1]

2. Dette åbner bladet **Endpoint Protection fejl** , som viser FOS med fejl og antallet af mislykkede forsøg for hver VM. Vælg en VM på listen.
![Endpoint protection fejl][2]

3. En **Liste over fejl** blade åbnes til den valgte VM, viser en liste over fejl. Vælg en fejl på listen for at få mere at vide. Dette åbner en blade med oplysninger om markerede fejlen.
![Mislykkede forsøg på listen][3]
  ![fejl begivenhed][4]

## <a name="see-also"></a>Se også

Hvis du vil vide mere om Sikkerhedscenter, skal du se følgende:

- [Angive sikkerhedspolitikker i Azure Sikkerhedscenter](security-center-policies.md)– Lær, hvordan du konfigurere sikkerhedspolitikker for dine Azure abonnementer og grupper.
- [Administrere sikkerhedsanbefalinger i Azure Sikkerhedscenter](security-center-recommendations.md)– Lær, hvordan anbefalinger hjælpe dig med at beskytte dine Azure ressourcer.
- [Sikkerhed sundhed overvågning i Azure Sikkerhedscenter](security-center-monitoring.md)– Lær, hvordan du overvåge tilstanden for dine Azure ressourcer.
- [Administrere og besvare sikkerhedsadvarsler i Azure Sikkerhedscenter](security-center-managing-and-responding-alerts.md)– Lær at administrere og reagere på beskeder om sikkerhed.
- [Overvågnings partnerløsninger med Azure Security Center](security-center-partner-solutions.md) – Lær, hvordan du overvåge systemtilstand status for din partnerløsninger.
- [Azure Security Center ofte stillede spørgsmål](security-center-faq.md)– Find ofte stillede spørgsmål om ved hjælp af tjenesten.
- [Azure sikkerhed blog](http://blogs.msdn.com/b/azuresecurity/)– få de seneste nyheder og Azure sikkerhed og oplysninger.

<!--Image references-->
[1]: ./media/security-center-resolve-endpoint-protection/resolve-endpoint-protection.png
[2]: ./media/security-center-resolve-endpoint-protection/endpoint-protection-failure.png
[3]: ./media/security-center-resolve-endpoint-protection/failure-list.png
[4]: ./media/security-center-resolve-endpoint-protection/failure-event.png
