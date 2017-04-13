<properties
   pageTitle="Aktivere VM Agent i Azure Sikkerhedscenter | Microsoft Azure"
   description="Dette dokument viser, hvordan du implementere Azure Sikkerhedscenter anbefaling **Aktivere VM Agent**."
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
   ms.date="10/17/2016"
   ms.author="terrylan"/>

# <a name="enable-vm-agent-in-azure-security-center"></a>Aktivere VM Agent i Azure Sikkerhedscenter

VM Agent skal være installeret på virtuelle maskiner (VM'er) i ordre for at [aktivere dataindsamling](security-center-enable-data-collection.md).  Azure Sikkerhedscenter gør det muligt at se, hvilke FOS kræver VM Agent og anbefaler, at du aktiverer VM Agent på disse FOS.

VM Agent er installeret som standard for VM'er, der er implementeret fra Azure Marketplace. I artiklen [VM Agent og udvidelser – del 2](https://azure.microsoft.com/blog/vm-agent-and-extensions-part-2/) indeholder oplysninger om hvordan du installerer VM Agent.


> [AZURE.NOTE] Dette dokument introducerer tjenesten ved hjælp af en eksempel-installation. Dette er ikke en trinvis vejledning.

## <a name="implement-the-recommendation"></a>Implementere anbefalingen

1. Vælg **Aktivér VM Agent** **anbefalinger blade**.
![Aktivere VM Agent][1]

2. Dette åbner bladet **VM Agent mangler eller ikke svarer**. Denne blade viser VM'er, der kræver VM Agent. Følg vejledningen på bladet installere VM agent.
![VM Agent mangler][2]

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
[1]: ./media/security-center-enable-vm-agent/enable-vm-agent.png
[2]: ./media/security-center-enable-vm-agent/vm-agent-is-missing.png
