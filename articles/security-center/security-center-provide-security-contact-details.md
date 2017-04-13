<properties
   pageTitle="Angive sikkerhed kontaktoplysninger i Azure Sikkerhedscenter | Microsoft Azure"
   description="Dette dokument viser, hvordan du giver sikkerhed kontaktoplysninger i Azure Sikkerhedscenter."
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

# <a name="provide-security-contact-details-in-azure-security-center"></a>Angive sikkerhed kontaktoplysninger i Azure Sikkerhedscenter

Azure Sikkerhedscenter anbefaler, at du angiver sikkerhed kontaktoplysninger for dit Azure abonnement, hvis du ikke allerede har. Disse oplysninger bruges af Microsoft til at kontakte dig, hvis Microsoft sikkerhed svar Center (MSRC) opdager, at dine kundedata har fået adgang til en ulovlig eller uautoriserede part. MSRC udfører Vælg sikkerhed overvågning af Azure Netværks- og infrastruktur og modtager truslen intelligence og misbrug klager fra tredjeparter.

Besked via mail sendes på den første daglige forekomst af en påmindelse og kun for høj alvorlighed beskeder. Indstillinger for mail kan kun konfigureres abonnement politikker for. Ressourcegrupper i et abonnement arver disse indstillinger.

> [AZURE.NOTE] Dette dokument introducerer tjenesten ved hjælp af en eksempel-installation.  Dette er ikke en trinvis vejledning.

## <a name="implement-the-recommendation"></a>Implementere anbefalingen

1. Markér **Giv sikkerhed kontaktpersonoplysninger**i bladet **anbefalinger** .
![Angive sikkerhed Kontakt][1]

2. Dette åbner bladet **Giv sikkerhed kontaktpersonoplysninger**. Vælg det Azure abonnement til at levere kontaktoplysninger på.
![Angive sikkerhed kontaktoplysninger][2]

3. En anden **giver sikkerhed kontaktoplysninger** blade åbnes.

  - Angiv sikkerhed kontaktpersons mailadresse eller adresser adskilt af kommaer. Der er ikke en grænse for antallet af e-mail-adresser, som du kan angive.
  - Angiv en sikkerhed kontakter internationalt telefonnummer.
  - Hvis du vil modtage mails om høj alvorlighed beskeder, aktivere indstillingen **Send mig mails om vigtige beskeder**.
  - I fremtiden, får du mulighed for at sende e-mail-meddelelser til abonnementsejere. Denne indstilling er nedtonet i øjeblikket.
  - Vælg **OK** at anvende sikkerhed kontaktoplysninger til dit abonnement.

## <a name="see-also"></a>Se også

Hvis du vil vide mere om Sikkerhedscenter, skal du se følgende:

- [Angive sikkerhedspolitikker i Azure Sikkerhedscenter](security-center-policies.md) – Lær, hvordan du konfigurere sikkerhedspolitikker for dine Azure abonnementer og grupper.
- [Administrere sikkerhedsanbefalinger i Azure Sikkerhedscenter](security-center-recommendations.md) – Lær, hvordan anbefalinger hjælpe dig med at beskytte dine Azure ressourcer.
- [Sikkerhed sundhed overvågning i Azure Sikkerhedscenter](security-center-monitoring.md) – Lær, hvordan du overvåge tilstanden for dine Azure ressourcer.
- [Administrere og besvare sikkerhedsadvarsler i Azure Sikkerhedscenter](security-center-managing-and-responding-alerts.md) – Lær at administrere og reagere på beskeder om sikkerhed.
- [Overvågnings partnerløsninger med Azure Security Center](security-center-partner-solutions.md) – Lær, hvordan du overvåge systemtilstand status for din partnerløsninger.
- [Azure Security Center ofte stillede spørgsmål](security-center-faq.md) – Find ofte stillede spørgsmål om ved hjælp af tjenesten.
- [Azure sikkerhed blog](http://blogs.msdn.com/b/azuresecurity/) – få de seneste nyheder og Azure sikkerhed og oplysninger.

<!--Image references-->
[1]: ./media/security-center-provide-security-contacts/provide-contacts.png
[2]:./media/security-center-provide-security-contacts/provide-contact-details.png
