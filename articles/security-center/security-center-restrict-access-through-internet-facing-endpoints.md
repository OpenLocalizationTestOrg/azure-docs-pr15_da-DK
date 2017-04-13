<properties
   pageTitle="Begrænse adgang via internettet slutpunkter i Azure Sikkerhedscenter | Microsoft Azure"
   description="Dette dokument viser, hvordan du implementere Azure Sikkerhedscenter anbefaling **Begræns adgang via internettet modstående slutpunkt**."
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

# <a name="restrict-access-through-internet-facing-endpoints-in-azure-security-center"></a>Begrænse adgang via internettet slutpunkter i Azure Sikkerhedscenter

Azure Sikkerhedscenter anbefaler, at du begrænser adgang via internettet slutpunkter, hvis en eller flere af dine netværk sikkerhedsgrupper (NSGs) har en eller flere indgående regler, der giver adgang fra "alle" kilde-IP-adresser. Åbning af adgang til "en" kan aktivere hackere at få adgang til dine ressourcer. Sikkerhedscenter anbefaler, at du redigere disse indgående regler for at begrænse adgangen til kilde-IP-adresser, der faktisk skal have adgang.

Denne anbefaling oprettes for alle ikke-webbaserede port, der indeholder "en" som kilde.

> [AZURE.NOTE] Dette dokument introducerer tjenesten ved hjælp af en eksempel-installation. Dette er ikke en trinvis vejledning.

## <a name="implement-the-recommendation"></a>Implementere anbefalingen

1. Vælg **begrænset adgang via internettet modstående slutpunkt**i **anbefalinger blade**.
![Begrænse adgang via internettet modstående slutpunkt][1]

2. Dette åbner bladet **Begræns adgang via internettet modstående slutpunkt**. Denne blade vises virtuel maskiner (VM'er) med indgående regler, som opretter et potentielt sikkerhedsproblem. Vælg en VM.
![Vælg en VM][2]

3. Bladet **NSG** viser netværk sikkerhedsgruppe oplysninger, relaterede indgående regler og den tilknyttede VM. Klik på **Rediger indgående regler** at fortsætte med at redigere en indgående regel.
![Netværk sikkerhedsgruppe blade][3]

4. Vælg den indgående regel til at redigere på bladet **indgående sikkerhedsregler** . Lad os Vælg **AllowWeb**i dette eksempel.
![Indgående sikkerhedsregler][4]

  Bemærk, at du kan også vælge **standardregler for** at se sæt af standardregler, der er indeholdt i alle NSGs. Standardreglerne kan ikke slettes, men fordi de er tildelt en lavere prioritet, de kan tilsidesættes af de regler, du opretter. Lær mere om [standardregler](../virtual-network/virtual-networks-nsg.md#default-rules).
![Standardregler][5]

5. Rediger egenskaberne for den indgående regel på bladet **AllowWeb** så **kilde** er en IP-adresse eller blok IP-adresser. Hvis du vil vide mere om egenskaberne for den indgående regel skal du se [NSG regler](../virtual-network/virtual-networks-nsg.md#nsg-rules).

  ![Redigere indgående regel][6]

## <a name="see-also"></a>Se også

I denne artikel viste dig, hvordan at implementere Sikkerhedscenter anbefaling "Begræns adgang via internettet modstående slutpunkt." Hvis du vil vide mere om aktivering af NSGs og regler, skal du se følgende:

- [Hvad er et netværk sikkerhed gruppe (NSG)?](../virtual-network/virtual-networks-nsg.md)
- [Sådan administreres NSGs ved hjælp af portalen Azure](../virtual-network/virtual-networks-create-nsg-arm-pportal.md)

Hvis du vil vide mere om Sikkerhedscenter, skal du se følgende:

- [Angive sikkerhedspolitikker i Azure Sikkerhedscenter](security-center-policies.md)– Lær, hvordan du konfigurere sikkerhedspolitikker for dine Azure abonnementer og grupper.
- [Administrere sikkerhedsanbefalinger i Azure Sikkerhedscenter](security-center-recommendations.md)– Lær, hvordan anbefalinger hjælpe dig med at beskytte dine Azure ressourcer.
- [Sikkerhed sundhed overvågning i Azure Sikkerhedscenter](security-center-monitoring.md)– Lær, hvordan du overvåge tilstanden for dine Azure ressourcer.
- [Administrere og besvare sikkerhedsadvarsler i Azure Sikkerhedscenter](security-center-managing-and-responding-alerts.md)– Lær at administrere og reagere på beskeder om sikkerhed.
- [Overvågnings partnerløsninger med Azure Security Center](security-center-partner-solutions.md) – Lær, hvordan du overvåge systemtilstand status for din partnerløsninger.
- [Azure Security Center ofte stillede spørgsmål](security-center-faq.md)– Find ofte stillede spørgsmål om ved hjælp af tjenesten.
- [Azure sikkerhed blog](http://blogs.msdn.com/b/azuresecurity/)– få de seneste nyheder og Azure sikkerhed og oplysninger.

<!--Image references-->
[1]: ./media/security-center-restrict-access-thru-internet-facing-endpoint/restrict-access-thru-internet-facing-endpoint.png
[2]: ./media/security-center-restrict-access-thru-internet-facing-endpoint/select-a-vm.png
[3]: ./media/security-center-restrict-access-thru-internet-facing-endpoint/network-security-group-blade.png
[4]: ./media/security-center-restrict-access-thru-internet-facing-endpoint/inbound-security-rules.png
[5]: ./media/security-center-restrict-access-thru-internet-facing-endpoint/default-rules.png
[6]: ./media/security-center-restrict-access-thru-internet-facing-endpoint/edit-inbound-rule.png
