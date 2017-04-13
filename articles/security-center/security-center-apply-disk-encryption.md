<properties
   pageTitle="Anvende disk kryptering i Azure Sikkerhedscenter | Microsoft Azure"
   description="Dette dokument viser, hvordan du implementere Azure Sikkerhedscenter anbefaling **Anvend disk kryptering**."
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

# <a name="apply-disk-encryption-in-azure-security-center"></a>Anvende disk kryptering i Azure Security Center

Azure Sikkerhedscenter anbefaler, at du anvender disk kryptering, hvis du har Windows eller Linux VM diske, der ikke er krypteret ved hjælp af Azure Disk kryptering. Disken kryptering gør det muligt at kryptere Windows og Linux IaaS VM disken.  Kryptering anbefales til både OS og enheder på din VM.


Disken kryptering bruger funktionen branche standard [BitLocker](https://technet.microsoft.com/library/cc732774.aspx) i Windows og funktionen [DM Crypt](https://en.wikipedia.org/wiki/Dm-crypt) af Linux at give OS og data kryptering for at beskytte og beskytte dine data og opfylde din organisations sikkerhed og overholdelse forpligtelser. Disken kryptering er integreret med [Azure nøgle samling](https://azure.microsoft.com/documentation/services/key-vault/) kan hjælpe dig med at kontrollere og administrere disk krypteringsnøgler og hemmeligheder i abonnementet nøgle samling og sikre, at alle data i VM diskene er krypteret på resten i din [Azure-lager](https://azure.microsoft.com/documentation/services/storage/).

> [AZURE.NOTE] Azure Disk kryptering understøttes på følgende Windows server-operativsystemer - Windows Server 2008 R2, Windows Server 2012 og Windows Server 2012 R2. Disken kryptering understøttes på følgende Linux serveroperativsystemer - Ubuntu, CentOS, SUSE og SUSE Linux Enterprise Server (SLES).

## <a name="implement-the-recommendation"></a>Implementere anbefalingen

1. Vælg **Anvend disk kryptering**i bladet **anbefalinger** .
2. I bladet **Anvend disk kryptering** vises en liste over FOS, Disk kryptering anbefales.
3. Følg vejledningen for at anvende kryptering på disse FOS.

![][1]

Hvis du vil kryptere virtuelle Azure-computere, der er identificeret af Sikkerhedscenter genberegner kryptering, anbefaler vi følgende trin:

- Installere og konfigurere Azure PowerShell. Dette gør det muligt at køre de PowerShell-kommandoer, der kræves for at konfigurere de nødvendige forudsætninger for at kryptere virtuelle Azure-computere.
- Hent og kør Azure Disk kryptering forudsætninger Azure PowerShell-script.
- Kryptere virtuelle computere.

[Kryptere en Azure virtuelt](security-center-disk-encryption.md) fører dig gennem disse trin.  Dette emne antages det, du bruger Windows 10 som klientmaskinen, hvorfra du vil konfigurere disk kryptering.

Der er mange fremgangsmåder, der kan bruges til at konfigurere forudsætninger og konfigurere kryptering for virtuelle Azure-computere. Hvis du er allerede ordentligt versed i Azure PowerShell eller Azure CLI, kan du foretrækker at bruge alternative fremgangsmåder. For at få mere for at vide om disse andre muligheder, skal du se [Azure disk kryptering](../security/azure-security-disk-encryption.md).



## <a name="see-also"></a>Se også

Dette dokument viste dig, hvordan at implementere Sikkerhedscenter anbefaling "Anvend disk kryptering." Hvis du vil vide mere om disk kryptering skal du se følgende:

- [Kryptering og key management med Azure nøgle samling](https://azure.microsoft.com/documentation/videos/azurecon-2015-encryption-and-key-management-with-azure-key-vault/) (video-og 36 min 39 sec) – Lær at bruge disk kryptering administration for IaaS FOS og Azure-tasten samling af legitimationsoplysninger for at beskytte og beskytte dine data.
- [Kryptere en Azure virtuelt](security-center-disk-encryption.md) (dokument) – Lær, hvordan du krypterer virtuelle Azure-computere.
- [Azure disk kryptering](../security/azure-security-disk-encryption.md) (dokument) – Lær, hvordan du aktiverer disk kryptering til Windows og Linux FOS.

Hvis du vil vide mere om Sikkerhedscenter, skal du se følgende:

- [Angive sikkerhedspolitikker i Azure Sikkerhedscenter](security-center-policies.md) – Lær, hvordan du kan konfigurere sikkerhedspolitikker.
- [Sikkerhed sundhed overvågning i Azure Sikkerhedscenter](security-center-monitoring.md) – Lær, hvordan du overvåge tilstanden for dine Azure ressourcer.
- [Administrere og besvare sikkerhedsadvarsler i Azure Sikkerhedscenter](security-center-managing-and-responding-alerts.md) – Lær at administrere og reagere på beskeder om sikkerhed.
- [Administrere sikkerhedsanbefalinger i Azure Sikkerhedscenter](security-center-recommendations.md) – Lær, hvordan anbefalinger hjælpe dig med at beskytte dine Azure ressourcer.
- [Azure Security Center ofte stillede spørgsmål](security-center-faq.md) – Find ofte stillede spørgsmål om ved hjælp af tjenesten.
- [Azure sikkerhed blog](http://blogs.msdn.com/b/azuresecurity/) – Find blog indlæg om Azure sikkerhed og overholdelse af regler.



<!--Image references-->
[1]: ./media/security-center-apply-disk-encryption/apply-disk-encryption.png
