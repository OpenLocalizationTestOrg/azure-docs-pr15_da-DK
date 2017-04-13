<properties
   pageTitle="Tilføje en næste generering af firewall i Azure Sikkerhedscenter | Microsoft Azure"
   description="Dette dokument viser, hvordan du implementere Azure Security Center-anbefalinger **Tilføj næste generering af Firewall** og **distribuere traffice gennem NGFW kun**."
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

# <a name="add-a-next-generation-firewall-in-azure-security-center"></a>Tilføje en næste generering af Firewall i Azure Security Center

Azure Sikkerhedscenter kan anbefaler, at du tilføjer en næste generering af firewall (NGFW) fra en Microsoft-partner for at øge din sikkerhed beskyttelse. Dette dokument fører dig gennem et eksempel på, hvordan du gør dette.

> [AZURE.NOTE] Dette dokument introducerer tjenesten ved hjælp af en eksempel-installation.  Dette er ikke en trinvis vejledning.

## <a name="implement-the-recommendation"></a>Implementere anbefalingen

1. Vælg **Tilføj en næste generering af Firewall**i bladet **anbefalinger** .
![Tilføje en næste generering af Firewall][1]

2. Vælg et slutpunkt i bladet **Tilføj næste generering af Firewall** .
![Vælg et slutpunkt][2]

3. En anden **Tilføj næste generering af Firewall** blade åbnes. Du kan vælge at bruge en eksisterende løsning, hvis det er muligt, eller du kan oprette en ny. I dette eksempel er der ingen eksisterende løsninger så vi vil oprette en ny NGFW.
![Oprette nye næste generering af Firewall][3]

4. For at oprette en ny NGFW skal du vælge en løsning på listen over integreret partnere. I dette eksempel vi kan du markere **Afkrydsningsfeltet punkt**.
![Vælg Næste generering af Firewall-løsning][4]

5. **Kontrollér punkt** blade åbnes giver dig oplysninger om partnerløsning. Vælg **Opret** i bladet oplysninger.
![Firewall oplysninger blade][5]

6. **Opret virtuelt** blade åbnes. Her kan du angive oplysninger, der kræves for at dreje op et virtuelt (VM), der skal køre NGFW. Følg trinnene og angive de ønskede NGFW oplysninger. Vælg OK for at anvende.
![Oprette virtuelle maskine for at køre NGFW][6]

## <a name="route-traffic-through-ngfw-only"></a>Distribuere-trafik gennem NGFW kun

Gå tilbage til bladet **anbefalinger** . En ny post blev oprettet, efter du har tilføjet en NGFW via Sikkerhedscenter, kaldet **dirigere trafik via NGFW kun**. Denne anbefaling oprettes kun, hvis du har installeret dit NGFW via Sikkerhedscenter. Hvis du har forbindelse til internettet slutpunkter, bliver Sikkerhedscenter anbefaler, at du konfigurerer netværk sikkerhedsgruppe regler, der tvinger indgående trafik til dit VM gennem din NGFW.

1. Vælg **dirigere trafik via NGFW kun**i **anbefalinger blade**.
![Distribuere-trafik gennem NGFW kun][7]

2. Dette åbner bladet **omdirigere trafik via NGFW kun** som viser FOS, som du kan omdirigere trafikken til. Vælg en VM på listen.
![Vælg en VM][8]

3. En blade for den valgte VM åbnes og viser relaterede indgående regler. En beskrivelse giver dig flere oplysninger om mulige videre trin. Klik på **Rediger indgående regler** at fortsætte med at redigere en indgående regel. Det forventes, **kilde** er angivet til **en hvilken som helst** for internettet slutpunkterne sammenkædet med NGFW. Hvis du vil vide mere om egenskaberne for den indgående regel skal du se [NSG regler](../virtual-network/virtual-networks-nsg.md#nsg-rules).
![Konfigurere regler for at begrænse adgangen][9]
![Rediger indgående regel][10]

## <a name="see-also"></a>Se også

Dette dokument viste dig, hvordan at implementere Sikkerhedscenter anbefaling "Tilføj næste generering af Firewall." Hvis du vil vide mere om NGFWs og Kontrollér punkt partner løsningen skal du se følgende:

- [Næste-generering af Firewall](https://en.wikipedia.org/wiki/Next-Generation_Firewall)
- [Se punkt vSEC](https://azure.microsoft.com/marketplace/partners/checkpoint/check-point-r77-10/)

Hvis du vil vide mere om Sikkerhedscenter, skal du se følgende:

- [Angive sikkerhedspolitikker i Azure Sikkerhedscenter](security-center-policies.md) – Lær, hvordan du kan konfigurere sikkerhedspolitikker.
- [Administrere sikkerhedsanbefalinger i Azure Sikkerhedscenter](security-center-recommendations.md) – Lær, hvordan anbefalinger hjælpe dig med at beskytte dine Azure ressourcer.
- [Sikkerhed sundhed overvågning i Azure Sikkerhedscenter](security-center-monitoring.md) – Lær, hvordan du overvåge tilstanden for dine Azure ressourcer.
- [Administrere og besvare sikkerhedsadvarsler i Azure Sikkerhedscenter](security-center-managing-and-responding-alerts.md) – Lær at administrere og reagere på beskeder om sikkerhed.
- [Overvågnings partnerløsninger med Azure Security Center](security-center-partner-solutions.md) – Lær, hvordan du overvåge systemtilstand status for din partnerløsninger.
- [Azure Security Center ofte stillede spørgsmål](security-center-faq.md) – Find ofte stillede spørgsmål om ved hjælp af tjenesten.
- [Azure sikkerhed blog](http://blogs.msdn.com/b/azuresecurity/) – Find blog indlæg om Azure sikkerhed og overholdelse af regler.

<!--Image references-->
[1]: ./media/security-center-add-next-gen-firewall/add-next-gen-firewall.png
[2]: ./media/security-center-add-next-gen-firewall/select-an-endpoint.png
[3]: ./media/security-center-add-next-gen-firewall/create-new-next-gen-firewall.png
[4]: ./media/security-center-add-next-gen-firewall/select-next-gen-firewall.png
[5]: ./media/security-center-add-next-gen-firewall/firewall-solution-info-blade.png
[6]: ./media/security-center-add-next-gen-firewall/create-virtual-machine.png
[7]: ./media/security-center-add-next-gen-firewall/route-traffic-through-ngfw.png
[8]: ./media/security-center-add-next-gen-firewall/select-vm.png
[9]: ./media/security-center-add-next-gen-firewall/configure-rules-to-limit-access.png
[10]: ./media/security-center-add-next-gen-firewall/edit-inbound-rule.png
