<properties
   pageTitle="Aktivere netværk sikkerhedsgrupper i Azure Sikkerhedscenter | Microsoft Azure"
   description="Dette dokument viser, hvordan du implementere Azure Sikkerhedscenter anbefaling **Aktivere netværk sikkerhedsgrupper**."
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

# <a name="enable-network-security-groups-in-azure-security-center"></a>Aktivere netværk sikkerhedsgrupper i Azure Security Center

Azure Sikkerhedscenter anbefaler, at du aktiverer en netværk sikkerhedsgruppe (NSG), hvis der ikke allerede er aktiveret. NSGs indeholder en liste over listen ACL (Access Control) regler, der tillade eller nægte netværkstrafik til din VM forekomster i et virtuelt netværk. NSGs kan være knyttet til undernet eller enkelte VM forekomster i pågældende undernet. Når en NSG er knyttet til et undernet, regler ACL til alle VM forekomsterne i pågældende undernet. Desuden trafik til en enkelt VM kan være begrænset yderligere ved at knytte en NSG direkte til VM. For at lære mere under [Hvad er et netværk sikkerhed gruppe (NSG)?](../virtual-network/virtual-networks-nsg.md)

Hvis du ikke har aktiveret NSGs, Sikkerhedscenter skal præsentere to anbefalinger for dig: aktivere netværk-sikkerhedsgrupper på undernet og aktivere netværk-sikkerhedsgrupper på virtuelle maskiner. Du vælger hvilke niveau, undernet eller VM, anvende NSGs.


> [AZURE.NOTE] Dette dokument introducerer tjenesten ved hjælp af en eksempel-installation.  Dette er ikke en trinvis vejledning.

## <a name="implement-the-recommendation"></a>Implementere anbefalingen

1. Vælg **Aktivér netværk sikkerhedsgrupper** i bladet **anbefalinger** på undernet eller på virtuelle maskiner.
![Aktivere netværk sikkerhedsgrupper][1]

2. Dette åbner blade **Konfigurere mangler netværk-sikkerhedsgrupper** for undernet eller virtuelle maskiner, afhængigt af den anbefaling, du har valgt. Vælg et undernet eller en virtuel maskine til at konfigurere en NSG på.

  ![Konfigurere NSG for undernet][2]

  ![Konfigurere NSG til VM][3]
3. Vælg en eksisterende NSG, eller Vælg til at oprette en ny NSG på bladet **Vælg netværk sikkerhedsgruppe** .

  ![Vælg netværk sikkerhedsgruppe][4]

Hvis du opretter en ny NSG, skal du følge trinnene i [Sådan administreres NSGs ved hjælp af portalen Azure](../virtual-network/virtual-networks-create-nsg-arm-pportal.md) til at oprette en NSG og angive sikkerhedsregler.

## <a name="see-also"></a>Se også

I denne artikel viste dig, hvordan at implementere Sikkerhedscenter anbefaling "Aktivere netværk-sikkerhedsgrupper" for undernet eller virtuelle computere. Hvis du vil vide mere om aktivering af NSGs, skal du se følgende:

- [Hvad er et netværk sikkerhed gruppe (NSG)?](../virtual-network/virtual-networks-nsg.md)
- [Sådan administreres NSGs ved hjælp af portalen Azure](../virtual-network/virtual-networks-create-nsg-arm-pportal.md)

Hvis du vil vide mere om Sikkerhedscenter, skal du se følgende:

- [Angive sikkerhedspolitikker i Azure Sikkerhedscenter](security-center-policies.md) – Lær, hvordan du konfigurere sikkerhedspolitikker for dine Azure abonnementer og grupper.
- [Administrere sikkerhedsanbefalinger i Azure Sikkerhedscenter](security-center-recommendations.md) – Lær, hvordan anbefalinger hjælpe dig med at beskytte dine Azure ressourcer.
- [Sikkerhed sundhed overvågning i Azure Sikkerhedscenter](security-center-monitoring.md) – Lær, hvordan du overvåge tilstanden for dine Azure ressourcer.
- [Administrere og besvare sikkerhedsadvarsler i Azure Sikkerhedscenter](security-center-managing-and-responding-alerts.md) – Lær at administrere og reagere på beskeder om sikkerhed.
- [Overvågnings partnerløsninger med Azure Security Center](security-center-partner-solutions.md) – Lær, hvordan du overvåge systemtilstand status for din partnerløsninger.
- [Azure Security Center ofte stillede spørgsmål](security-center-faq.md) – Find ofte stillede spørgsmål om ved hjælp af tjenesten.
- [Azure sikkerhed blog](http://blogs.msdn.com/b/azuresecurity/) – få de seneste nyheder og Azure sikkerhed og oplysninger.

<!--Image references-->
[1]: ./media/security-center-enable-nsg/enable-nsg.png
[2]:./media/security-center-enable-nsg/configure-nsg-for-subnet.png
[3]: ./media/security-center-enable-nsg/configure-nsg-for-vm.png
[4]: ./media/security-center-enable-nsg/choose-nsg.png
