<properties
   pageTitle="Administrere partnerløsninger i Azure Sikkerhedscenter | Microsoft Azure"
   description="Dette dokument vejleder dig gennem hvordan Azure Sikkerhedscenter kan du overvåge hurtigt sundhedsstatus for din partnerløsninger integreret med abonnementet Azure."
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
   ms.date="10/26/2016"
   ms.author="terrylan"/>

# <a name="monitoring-partner-solutions-with-azure-security-center"></a>Overvågning partnerløsninger med Azure Security Center

Dette dokument vejleder dig gennem Sådan overvåge systemtilstand status for din partnerløsninger i Azure Sikkerhedscenter.

> [AZURE.NOTE] Dette dokument introducerer tjenesten ved hjælp af en eksempel-installation. Dette er ikke en trinvis vejledning.

## <a name="monitoring-partner-solutions"></a>Overvågning partnerløsninger

Feltet **partnerløsninger** på bladet **Sikkerhedscenter** kan du overvåge hurtigt sundhedsstatus for din partnerløsninger integreret med abonnementet Azure.
![Partnerløsninger side om side][1]

Feltet **partnerløsninger** viser antallet af partnerløsninger og statussen oversigt for disse løsninger.

**STATUS** for en partnerløsning kan være:

- Beskyttet (grøn) – der er ingen sundhed problem.
- Beskadiget (rød) - der er et problem i tilstand, der kræver øjeblikkelig handling.
- Længere ikke rapportering (orange) - løsningen fungerer ikke længere rapportering sin tilstand.
- Ukendt beskyttelsesstatus (orange) - tilstanden for løsningen er ukendt på nuværende tidspunkt på grund af en mislykket processen med at tilføje en ny ressource til den eksisterende løsning.
- Ikke rapporteret (grå) - løsningen ikke har rapporteret noget endnu en løsning status kan være urapporterede, hvis den er bare blevet tilsluttet, og der stadig implementerer.

Hvis der ikke er nogen løsninger, der er integreret med dit abonnement bliver feltet land, der findes ingen løsninger. Valg af feltet **partnerløsninger** gør det muligt at åbne bladet **anbefalinger** for at installere partnerløsninger sikkerhed.
![Ingen partnerløsninger][2]

Sådan får du vist tilstanden for din partnerløsninger:

1. Vælg feltet **partnerløsninger** . En blade åbnes viser en liste over dine partnerløsninger, der er knyttet til Sikkerhedscenter.
![Partnerløsninger][3]

2. Vælg en partnerløsning. I dette eksempel kan du vælge **F5 WAF2** løsningen.  Der åbnes en blade, der viser status for partner løsningen og løsningen tilknyttede ressourcer. Vælg **løsning console** for at åbne partner administration oplevelsen for denne løsning.
![Partner løsning detaljer][4]

3. Gå tilbage til bladet **F5 WAF2** , og vælg **linket app**. Bladet **Link programmer** åbnes. Her kan du forbinde ressourcer til partner løsningen.
![Linket ressourcer til partnerløsning][5]

## <a name="see-also"></a>Se også
I dette dokument, du blev introduceret til **Partnerløsninger** flise i Sikkerhedscenter. Hvis du vil vide mere om Sikkerhedscenter, skal du se følgende:

- [Angive sikkerhedspolitikker i Azure Sikkerhedscenter](security-center-policies.md) – Lær, hvordan du konfigurere sikkerhedspolitikker for dine Azure abonnementer og grupper.
- [Administrere sikkerhedsanbefalinger i Azure Sikkerhedscenter](security-center-recommendations.md) – Lær, hvordan anbefalinger hjælpe dig med at beskytte dine Azure ressourcer.
- [Sikkerhed sundhed overvågning i Azure Sikkerhedscenter](security-center-monitoring.md) – Lær, hvordan du overvåge tilstanden for dine Azure ressourcer.
- [Administrere og besvare sikkerhedsadvarsler i Azure Sikkerhedscenter](security-center-managing-and-responding-alerts.md) – Lær at administrere og reagere på beskeder om sikkerhed.
- [Azure Security Center ofte stillede spørgsmål](security-center-faq.md) – Find ofte stillede spørgsmål om ved hjælp af tjenesten.
- [Azure sikkerhed blog](http://blogs.msdn.com/b/azuresecurity/) – få de seneste nyheder og Azure sikkerhed og oplysninger.

<!--Image references-->
[1]: ./media/security-center-partner-solutions/partner-solutions-tile.png
[2]: ./media/security-center-partner-solutions/no-partner-solutions-to-display.png
[3]: ./media/security-center-partner-solutions/partner-solutions.png
[4]: ./media/security-center-partner-solutions/partner-solutions-detail.png
[5]: ./media/security-center-partner-solutions/link-applications.png
