<properties
   pageTitle="Installere Endpoint Protection i Azure Sikkerhedscenter | Microsoft Azure"
   description="Dette dokument viser, hvordan du implementere Azure Sikkerhedscenter anbefaling **Installere Endpoint Protection**."
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
   ms.date="08/16/2016"
   ms.author="terrylan"/>

# <a name="install-endpoint-protection-in-azure-security-center"></a>Installere Endpoint Protection i Azure Sikkerhedscenter

Azure Sikkerhedscenter anbefaler, at du klargøre et Antimalwareprogram program til din Azure virtuelle maskiner (VM'er), hvis Antimalwareprogram ikke allerede er aktiveret. Denne anbefaling gælder kun for Windows FOS.

> [AZURE.NOTE] Dette dokument introducerer tjenesten ved hjælp af en eksempel-installation.  Dette er ikke en trinvis vejledning.

## <a name="implement-the-recommendation"></a>Implementere anbefalingen

1. Vælg **Installer Endpoint Protection**i bladet **anbefalinger** .
![Vælg Installer Endpoint Protection][1]

2. **Installere Endpoint Protection** blade åbnes viser en liste over FOS uden Antimalwareprogram aktiveret. Vælg på listen FOS, du vil installere Antimalwareprogram på, og klik på **Installer på FOS**.
![Vælg FOS installere Antimalwareprogram på][2]

3. **Vælg Endpoint Protection** blade åbnes tillader dig at vælge den Antimalwareprogram løsning, du vil bruge. Lad os markere **Microsoft Antimalwareprogram**i dette eksempel.
![Vælg Endpoint Protection][3]

4. Yderligere oplysninger om løsningen Antimalwareprogram vises. Vælg **Opret**.
![Oprette Antimalwareprogram løsning][4]

5. Angiv de nødvendige konfigurationsindstillinger på bladet **Tilføje lokalnummer** , og klik derefter på **OK**. Hvis du vil vide mere om konfigurationsindstillingerne, se [standard og brugerdefineret Antimalwareprogram konfiguration](../security/azure-security-antimalware.md#default-and-custom-antimalware-configuration).

[Microsoft Antimalwareprogram](../azure-security-antimalware.md) er nu aktiv på de valgte FOS.

## <a name="see-also"></a>Se også

I denne artikel viste dig, hvordan at implementere Sikkerhedscenter anbefaling "Installer Endpoint Protection." Hvis du vil vide mere om aktivering af et Antimalwareprogram program i Azure, skal du se følgende:

- [Microsoft Antimalwareprogram til Skytjenester og virtuelle maskiner](../azure-security-antimalware.md) – Lær, hvordan du installerer Microsoft Antimalwareprogram.

Hvis du vil vide mere om Sikkerhedscenter, skal du se følgende:

- [Angive sikkerhedspolitikker i Azure Sikkerhedscenter](security-center-policies.md) – Lær, hvordan du kan konfigurere sikkerhedspolitikker.
- [Administrere sikkerhedsanbefalinger i Azure Sikkerhedscenter](security-center-recommendations.md) – Lær, hvordan anbefalinger hjælpe dig med at beskytte dine Azure ressourcer.
- [Sikkerhed sundhed overvågning i Azure Sikkerhedscenter](security-center-monitoring.md) – Lær, hvordan du overvåge tilstanden for dine Azure ressourcer.
- [Administrere og besvare sikkerhedsadvarsler i Azure Sikkerhedscenter](security-center-managing-and-responding-alerts.md) – Lær at administrere og reagere på beskeder om sikkerhed.
- [Overvågnings partnerløsninger med Azure Security Center](security-center-partner-solutions.md) – Lær, hvordan du overvåge systemtilstand status for din partnerløsninger.
- [Azure Security Center ofte stillede spørgsmål](security-center-faq.md) – Find ofte stillede spørgsmål om ved hjælp af tjenesten.
- [Azure sikkerhed blog](http://blogs.msdn.com/b/azuresecurity/) – Find blog indlæg om Azure sikkerhed og overholdelse af regler.

<!--Image references-->
[1]:./media/security-center-install-endpoint-protection/select-install-endpoint-protection.png
[2]:./media/security-center-install-endpoint-protection/install-endpoint-protection-blade.png
[3]:./media/security-center-install-endpoint-protection/select-endpoint-protection.png
[4]:./media/security-center-install-endpoint-protection/create-antimalware-solution.png
