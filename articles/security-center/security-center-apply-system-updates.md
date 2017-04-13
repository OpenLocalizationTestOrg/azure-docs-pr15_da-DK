<properties
   pageTitle="Anvende systemopdateringer i Azure Sikkerhedscenter | Microsoft Azure"
   description="Dette dokument viser, hvordan du implementere Azure Security Center-anbefalinger **anvende systemopdateringer** og **genstarte efter opdateringer**."
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

# <a name="apply-system-updates-in-azure-security-center"></a>Anvende systemopdateringer i Azure Security Center

Azure Sikkerhedscenter overvåger dagligt Windows og Linux virtuelle-maskiner (VM'er) efter manglende opdateringer til operativsystemet. Sikkerhedscenter henter en liste over tilgængelige sikkerhedsopdateringer og vigtige opdateringer fra Windows Update eller Windows Server Update Services (WSUS), afhængigt af hvor tjenesten er konfigureret på en Windows-VM.  Sikkerhedscenter kontrollerer også for de seneste opdateringer i Linux systemer. Hvis din VM mangler en systemopdatering, anbefaler Sikkerhedscenter, at du anvender systemopdateringer

> [AZURE.NOTE] Dette dokument introducerer tjenesten ved hjælp af en eksempel-installation.  Dette er ikke en trinvis vejledning.

## <a name="implement-the-recommendation"></a>Implementere anbefalingen

1. Vælg **Anvend opdateringer**i bladet **anbefalinger** .
![Anvende systemopdateringer][1]

2. Bladet **Anvend opdateringer** åbnes viser en liste over FOS manglende systemopdateringer. Vælg en VM.
![Vælg en VM][2]

3. En blade åbnes viser en liste over manglende opdateringer til pågældende VM. Vælg et systemopdatering. Lad os Vælg KB3156016 i dette eksempel.
![Manglende sikkerhedsopdateringer][3]

4. Følg trinnene i bladet **Security Update** til at anvende den manglende opdatering.
![Sikkerhedsopdatering][4]

## <a name="reboot-after-system-updates"></a>Genstart efter opdateringer

5. Gå tilbage til bladet **anbefalinger** . En ny post blev oprettet, efter du har anvendt systemopdateringer, kaldet **Genstart efter opdateringer**. Dette element kan du, at du skal genstarte VM for at fuldføre processen med at anvende systemopdateringer.
![Genstart efter opdateringer][5]

6. Vælg **Genstart efter opdateringer**. Dette åbner **en genstart afventer at fuldføre systemopdateringer** blade viser en liste over FOS, som du skal genstarte for at fuldføre processen Anvend system opdateringer.
![Genstart ventende][6]

Genstart VM fra Azure at fuldføre processen.

## <a name="see-also"></a>Se også

Hvis du vil vide mere om Sikkerhedscenter, skal du se følgende:

- [Angive sikkerhedspolitikker i Azure Sikkerhedscenter](security-center-policies.md) – Lær, hvordan du konfigurere sikkerhedspolitikker for dine Azure abonnementer og grupper.
- [Administrere sikkerhedsanbefalinger i Azure Sikkerhedscenter](security-center-recommendations.md) – Lær, hvordan anbefalinger hjælpe dig med at beskytte dine Azure ressourcer.
- [Sikkerhed sundhed overvågning i Azure Sikkerhedscenter](security-center-monitoring.md) – Lær, hvordan du overvåge tilstanden for dine Azure ressourcer.
- [Administrere og besvare sikkerhedsadvarsler i Azure Sikkerhedscenter](security-center-managing-and-responding-alerts.md) – Lær at administrere og reagere på beskeder om sikkerhed.
- [Overvågnings partnerløsninger med Azure Security Center](security-center-partner-solutions.md) – Lær, hvordan du overvåge systemtilstand status for din partnerløsninger.
- [Azure Security Center ofte stillede spørgsmål](security-center-faq.md) – Find ofte stillede spørgsmål om ved hjælp af tjenesten.
- [Azure sikkerhed blog](http://blogs.msdn.com/b/azuresecurity/) – Find blog indlæg om Azure sikkerhed og overholdelse af regler.

<!--Image references-->
[1]: ./media/security-center-apply-system-updates/recommendation.png
[2]:./media/security-center-apply-system-updates/select-vm.png
[3]: ./media/security-center-apply-system-updates/missing-security-updates.png
[4]: ./media/security-center-apply-system-updates/security-update.png
[5]: ./media/security-center-apply-system-updates/reboot-after-system-updates.png
[6]: ./media/security-center-apply-system-updates/restart-pending.png
