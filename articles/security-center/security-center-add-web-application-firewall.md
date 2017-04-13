<properties
   pageTitle="Tilføje en firewall til webprogrammer i Azure Sikkerhedscenter | Microsoft Azure"
   description="Dette dokument viser, hvordan du implementere Azure Security Center-anbefalinger, **Tilføj en firewall til webprogrammer** og **Færdiggør programmet beskyttelse**."
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

# <a name="add-a-web-application-firewall-in-azure-security-center"></a>Tilføje en firewall til webprogrammer i Azure Security Center

Azure Sikkerhedscenter kan anbefaler, at du tilføjer en firewall til webprogrammer (WAF) fra en Microsoft-partner for at sikre dine webprogrammer. Dette dokument fører dig gennem et eksempel på, hvordan du gør dette.

> [AZURE.NOTE] Dette dokument introducerer tjenesten ved hjælp af en eksempel-installation.  Dette er ikke en trinvis vejledning.

## <a name="implement-the-recommendation"></a>Implementere anbefalingen

1. Vælg **Secure webprogrammet ved hjælp af firewall til webprogrammer**i bladet **anbefalinger** .
![Secure web Application][1]

2. Vælg et webprogram i bladet **Secure dine webprogrammer ved hjælp af firewall til webprogrammer** . **Tilføj en Firewall til webprogrammer** blade åbnes.
![Tilføje en firewall til webprogrammer][2]
3. Du kan vælge at bruge en eksisterende firewall til webprogrammer, hvis det er muligt, eller du kan oprette en ny. I dette eksempel er der ingen eksisterende WAFs så vi vil oprette en ny WAF.

4. For at oprette en ny WAF skal du vælge en løsning på listen over integreret partnere. I dette eksempel vælger vi **Barracuda Firewall til webprogrammer**.
5. Bladet **Barracuda Firewall til webprogrammer** åbnes giver dig oplysninger om partnerløsning. Vælg **Opret** i bladet oplysninger.
![Firewall oplysninger blade][3]

6. Bladet **Nye Firewall til webprogrammer** åbnes, hvor du kan udføre **VM** konfigurationstrinnene og angive **WAF oplysninger**. Vælg **VM konfiguration**.

7. I bladet **VM konfiguration** kan du angive oplysninger, der kræves for at dreje op den virtuelle maskine, som kører på WAF.
![VM konfiguration][4]
8. Gå tilbage til den **Nye Firewall til webprogrammer** blade, og vælg **WAF oplysninger**. Du kan konfigurere WAF selve i bladet **WAF oplysninger** . Trin 7 kan du konfigurere den virtuelle maskine, som kører på WAF og trin 8 gør det muligt at klargøre WAF sig selv.

## <a name="finalize-application-protection"></a>Færdiggøre programmet beskyttelse

1. Gå tilbage til bladet **anbefalinger** . En ny post blev oprettet, efter du har oprettet WAF, kaldet **Færdiggør programmet beskyttelse**. Dette element gør det muligt at vide, at du har brug at fuldføre processen med at faktisk kabelføring op WAF inden for det virtuelle Azure-netværk, så den kan beskytte programmet.
![Færdiggøre programmet beskyttelse][5]

2. Vælg **Færdiggør programmet beskyttelse**. En ny blade åbnes. Du kan se, at der er et webprogram, der skal have trafikken omdirigeret.
3. Vælg webprogrammet. En blade åbnes, som giver dig trinnene til fuldfører web application firewall konfigurationen. Fuldfør trinnene, og vælg derefter **begrænset trafik**. Sikkerhedscenter gør derefter kabler-op for dig.
![Begrænse trafik][6]

> [AZURE.NOTE] Du kan beskytte flere webprogrammer i Sikkerhedscenter ved at tilføje disse programmer til din eksisterende WAF installationer. WAF anvendelser (oprettet ved hjælp af Ressourcestyring implementeringsmodel) skal være installeret på et separat virtuelt netværk. WAF anvendelser (oprettet ved hjælp af den klassiske implementeringsmodel) er begrænset til at bruge en netværk sikkerhedsgruppe. Denne understøttelse udvides til en fuldt tilpassede installation af en WAF maskinen (klassisk) i fremtiden. Lær mere om [Klassisk og ressourcestyring installation modeller](../azure-classic-rm.md) for Azure ressourcer.

Loggene fra WAF er nu fuldt integreret. Sikkerhedscenter kan begynde at indsamle og analysere logfiler, så den kan overflade vigtige sikkerhedsadvarsler for dig automatisk.

## <a name="see-also"></a>Se også

Dette dokument viste dig, hvordan at implementere Sikkerhedscenter anbefaling "Tilføj et webprogram." Hvis du vil vide mere om at konfigurere en firewall til webprogrammer, skal du se følgende:

- [Konfiguration af en Firewall til webprogrammer (WAF) til App Service-miljø](../app-service-web/app-service-app-service-environment-web-application-firewall.md)

Hvis du vil vide mere om Sikkerhedscenter, skal du se følgende:

- [Angive sikkerhedspolitikker i Azure Sikkerhedscenter](security-center-policies.md) – Lær, hvordan du konfigurere sikkerhedspolitikker for dine Azure abonnementer og grupper.
- [Sikkerhed sundhed overvågning i Azure Sikkerhedscenter](security-center-monitoring.md) – Lær, hvordan du overvåge tilstanden for dine Azure ressourcer.
- [Administrere og besvare sikkerhedsadvarsler i Azure Sikkerhedscenter](security-center-managing-and-responding-alerts.md) – Lær at administrere og reagere på beskeder om sikkerhed.
- [Administrere sikkerhedsanbefalinger i Azure Sikkerhedscenter](security-center-recommendations.md) – Lær, hvordan anbefalinger hjælpe dig med at beskytte dine Azure ressourcer.
- [Azure Security Center ofte stillede spørgsmål](security-center-faq.md) – Find ofte stillede spørgsmål om ved hjælp af tjenesten.
- [Azure sikkerhed blog](http://blogs.msdn.com/b/azuresecurity/) – Find blog indlæg om Azure sikkerhed og overholdelse af regler.

<!--Image references-->
[1]: ./media/security-center-add-web-application-firewall/secure-web-application.png
[2]:./media/security-center-add-web-application-firewall/add-a-waf.png
[3]: ./media/security-center-add-web-application-firewall/info-blade.png
[4]: ./media/security-center-add-web-application-firewall/select-vm-config.png
[5]: ./media/security-center-add-web-application-firewall/finalize-waf.png
[6]: ./media/security-center-add-web-application-firewall/restrict-traffic.png
