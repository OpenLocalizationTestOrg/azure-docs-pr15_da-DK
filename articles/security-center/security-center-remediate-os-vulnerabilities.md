<properties
   pageTitle="Afhjælpning OS svagheder i Azure Sikkerhedscenter | Microsoft Azure"
   description="Dette dokument viser, hvordan du implementere Azure Sikkerhedscenter anbefaling **afhjælpning OS svagheder**."
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

# <a name="remediate-os-vulnerabilities-in-azure-security-center"></a>Afhjælpning OS svagheder i Azure Sikkerhedscenter

Azure Sikkerhedscenter analyserer dagligt operativsystemet VM (virtual machine) (OS) konfigurationer, der kan gøre VM mere udsatte for angreb og anbefaler ændringer i konfigurationen til at løse disse svagheder. Du kan finde flere oplysninger om de specifikke konfigurationer, der skal overvåges, på [listen over anbefalet konfigurationsregler](https://gallery.technet.microsoft.com/Azure-Security-Center-a789e335). Sikkerhedscenter anbefaler, at du løser sikkerhedsrisici, når din VM OS konfiguration ikke svarer til de anbefalede konfigurationsregler.

> [AZURE.NOTE] Dette dokument introducerer tjenesten ved hjælp af en eksempel-installation.  Dette er ikke en trinvis vejledning.

## <a name="implement-the-recommendation"></a>Implementere anbefalingen

1. Vælg **afhjælpning OS svagheder**i bladet **anbefalinger** .
![Afhjælpning OS svagheder][1]

    Bladet **afhjælpning OS svagheder** åbnes og viser en liste over dine FOS med OS konfigurationer, der ikke stemmer overens med de anbefalede konfigurationsregler.  For hver VM identificerer bladet:

   - **Mislykkedes regler** – antallet af regler, der hvor VM OS konfigurationen mislykkedes.
   - **SCANNE sidste gang** --dato og klokkeslæt for, at Sikkerhedscenter sidste scannet den VM OS konfiguration.
   - **Tilstand** – den aktuelle tilstand for sikkerhedsrisikoen:

        - Åbn: Sikkerhedsrisikoen er ikke blevet løst endnu
        - Igangværende: Sikkerhedsrisikoen anvendes aktuelt, ingen handling er påkrævet af dig
        - Løst: Sikkerhedsrisikoen blev allerede behandlet (når problemet er løst, posten er nedtonet)
  - **ALVORLIGHED** – alle svagheder er angivet til en alvorlighed af lav, hvilket betyder, at en sikkerhedsrisiko skal løses, men ikke kræver øjeblikkelig handling.

Vælg en VM. En blade for pågældende VM åbnes og viser de regler, der har mislykkedes.
   ![Af konfigurationsregler, der har mislykkedes][2]

Vælg en regel. I dette eksempel kan vælge **adgangskode skal opfylder kravene til kompleksitet**. En blade åbnes, der beskriver mislykkedes reglen og påvirkningen. Gennemgå oplysningerne og overveje, hvordan operativsystemkonfigurationer anvendes.
  ![Beskrivelse af regel mislykkedes][3]

  Sikkerhedscenter bruger almindelige konfiguration optælling (CCE) til at tildele entydige identifikationskoder for af konfigurationsregler. Følgende oplysninger er angivet i denne blade:

  - NAME - Navnet på reglen
  - ALVORLIGHED – CCE alvorlighed værdi af kritiske, vigtige eller advarsel
  - CCIED – CCE entydigt id for reglen
  - Beskrivelse – Beskrivelse af regel
  - SIKKERHEDSRISIKO – Forklaring af sikkerhedsrisiko eller risiko hvis regel ikke anvendes
  - VIRKNING – Business virkning når regel anvendes
  - FORVENTEDE værdi – Værdien forventet, når Sikkerhedscenter analyserer konfigurationen VM OS mod reglen
  - REGEL OPERATION – Regel operation bruges af Sikkerhedscenter under analyse af konfigurationen VM OS mod reglen
  - FAKTISKE værdi – Værdi returneres efter analyse af konfigurationen VM OS mod reglen
  - RESULTAT af evaluering –-resultatet af analyse: overfører, fejl


## <a name="see-also"></a>Se også

I denne artikel viste dig, hvordan at implementere Sikkerhedscenter anbefaling "Remediate OS svagheder." Du kan gennemse konfiguration regelsæt [her](https://gallery.technet.microsoft.com/Azure-Security-Center-a789e335). Sikkerhedscenter bruger CCE (almindelige konfiguration optælling) til at tildele entydige identifikationskoder for af konfigurationsregler. Besøg webstedet [CCE](http://cce.mitre.org) kan finde flere oplysninger.

Hvis du vil vide mere om Sikkerhedscenter, skal du se følgende:

- [Angive sikkerhedspolitikker i Azure Sikkerhedscenter](security-center-policies.md) – Lær, hvordan du konfigurere sikkerhedspolitikker for dine Azure abonnementer og grupper.
- [Administrere sikkerhedsanbefalinger i Azure Sikkerhedscenter](security-center-recommendations.md) – Lær, hvordan anbefalinger hjælpe dig med at beskytte dine Azure ressourcer.
- [Sikkerhed sundhed overvågning i Azure Sikkerhedscenter](security-center-monitoring.md) – Lær, hvordan du overvåge tilstanden for dine Azure ressourcer.
- [Administrere og besvare sikkerhedsadvarsler i Azure Sikkerhedscenter](security-center-managing-and-responding-alerts.md) – Lær at administrere og reagere på beskeder om sikkerhed.
- [Overvågnings partnerløsninger med Azure Security Center](security-center-partner-solutions.md) – Lær, hvordan du overvåge systemtilstand status for din partnerløsninger.
- [Azure Security Center ofte stillede spørgsmål](security-center-faq.md) – Find ofte stillede spørgsmål om ved hjælp af tjenesten.
- [Azure sikkerhed blog](http://blogs.msdn.com/b/azuresecurity/) – Find blog indlæg om Azure sikkerhed og overholdelse af regler.

<!--Image references-->
[1]: ./media/security-center-remediate-os-vulnerabilities/recommendation.png
[2]:./media/security-center-remediate-os-vulnerabilities/vm-remediate-os-vulnerabilities.png
[3]: ./media/security-center-remediate-os-vulnerabilities/vulnerability-details.png
