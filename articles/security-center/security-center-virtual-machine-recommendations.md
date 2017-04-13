<properties
   pageTitle="Beskytte din virtuelle maskiner i Azure Sikkerhedscenter | Microsoft Azure"
   description="Dette dokument adresser anbefalinger i Azure Security Center, der hjælper dig med at beskytte din virtuelle maskiner og holde i overensstemmelse med sikkerhedspolitikker."
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
   ms.date="09/25/2016"
   ms.author="terrylan"/>

# <a name="protecting-your-virtual-machines-in-azure-security-center"></a>Beskytte din virtuelle maskiner i Azure Sikkerhedscenter

Azure Sikkerhedscenter analyserer tilstanden sikkerhed for ressourcerne Azure. Når Sikkerhedscenter identificerer potentielle sikkerhedsrisici, opretter anbefalinger, der fører dig gennem processen med at konfigurere nødvendige kontrolelementer.  Anvende anbefalinger til Azure ressourcetyper: virtuelle maskiner (VM'er), netværk, SQL og programmer.

I denne artikel omhandler anbefalinger, der gælder for FOS.  VM anbefalinger center rundt om dataindsamling, anvende opdateringer, klargøring Antimalwareprogram, kryptere VM disken og meget mere.  Brug tabellen nedenfor som en reference til at hjælpe dig med at forstå de tilgængelige VM anbefalinger, og hvad hver enkelt skal gøre, hvis du har anvendt den.

## <a name="available-vm-recommendations"></a>Tilgængelige VM anbefalinger

|Anbefaling|Beskrivelse|
|-----|-----|
|[Aktivere dataindsamling for abonnementer](security-center-enable-data-collection.md)|Anbefaler, at du slår dataindsamling i sikkerhedspolitikken for hver af dine abonnementer og alle virtuelle maskiner (VM'er) i dine abonnementer.|
|[Afhjælpning OS svagheder](security-center-remediate-os-vulnerabilities.md)|Anbefaler, at du justere din OS konfigurationer med regler anbefalet konfiguration tillader fx ikke adgangskoder skal gemmes.|
|[Anvende systemopdateringer](security-center-apply-system-updates.md)|Anbefaler, at du installerer manglende system sikkerhedsopdateringer og vigtige opdateringer til FOS.|
|[Genstart efter opdateringer](security-center-apply-system-updates.md#reboot-after-system-updates)|Anbefaler, at du genstarter en VM for at fuldføre processen med at anvende systemopdateringer.|
|[Installere Endpoint Protection](security-center-install-endpoint-protection.md)|Anbefaler, at du klargør Antimalwareprogram programmer til at FOS (kun Windows FOS).|
|[Løse Endpoint Protection sundhed beskeder](security-center-resolve-endpoint-protection-health-alerts.md)|Anbefaler, at du kan løse slutpunkt beskyttelse fejl.|
|[Aktivere VM Agent](security-center-enable-vm-agent.md)|Gør det muligt at se, hvilke FOS kræver VM Agent. VM Agent skal være installeret på FOS for at klargøre programrettelse scanning, Oprindelig plan scanning og Antimalwareprogram programmer. VM Agent er installeret som standard for VM'er, der er implementeret fra Azure Marketplace. I artiklen [VM Agent og udvidelser – del 2](http://azure.microsoft.com/blog/2014/04/15/vm-agent-and-extensions-part-2/) indeholder oplysninger om hvordan du installerer VM Agent.|
| [Anvende disk kryptering](security-center-apply-disk-encryption.md) |Anbefaler, at du krypterer VM disken ved hjælp af Azure Disk kryptering (Windows og Linux VM'er). Kryptering anbefales til både OS og enheder på din VM.|
| [Opdatere OS-version](security-center-update-os-version.md) | Anbefaler, at du opdaterer operativsystemversion (OS) til din skybaseret tjeneste til den nyeste version, der er tilgængelige for din OS familie.  Hvis du vil vide mere om Cloud Services, få vist [Cloud Services oversigt](../cloud-services/cloud-services-choose-me.md). |
| [Sikkerhedsrisiko vurdering ikke er installeret](security-center-vulnerability-assessment-recommendations.md) | Anbefaler, at du installerer en sikkerhedsrisiko vurdering løsning på din VM. |
| [Afhjælpning svagheder](security-center-vulnerability-assessment-recommendations.md#review-recommendation) | Gør det muligt at se system- og svagheder, der er fundet af sikkerhedsrisiko vurdering løsningen installeret på din VM. |

## <a name="see-also"></a>Se også

Hvis du vil vide mere om anbefalinger, der gælder for andre Azure ressourcetyper, skal du se følgende:

- [Beskytte dine programmer i Azure Sikkerhedscenter](security-center-application-recommendations.md)
- [Beskytte dit netværk i Azure Security Center](security-center-network-recommendations.md)
- [Beskytte din Azure SQL-tjeneste i Azure Sikkerhedscenter](security-center-sql-service-recommendations.md)

Hvis du vil vide mere om Sikkerhedscenter, skal du se følgende:

- [Angive sikkerhedspolitikker i Azure Sikkerhedscenter](security-center-policies.md) – Lær, hvordan du konfigurere sikkerhedspolitikker for dine Azure abonnementer og grupper.
- [Administrere og besvare sikkerhedsadvarsler i Azure Sikkerhedscenter](security-center-managing-and-responding-alerts.md) – Lær at administrere og reagere på beskeder om sikkerhed.
- [Azure Security Center ofte stillede spørgsmål](security-center-faq.md) – Find ofte stillede spørgsmål om ved hjælp af tjenesten.
