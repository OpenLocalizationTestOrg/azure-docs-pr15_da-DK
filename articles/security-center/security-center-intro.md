<properties
   pageTitle="Introduktion til Azure Sikkerhedscenter | Microsoft Azure"
   description="Få mere at vide om Azure Sikkerhedscenter, dens vigtige funktioner, og hvordan det fungerer."
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
   ms.date="07/21/2016"
   ms.author="terrylan"/>

# <a name="introduction-to-azure-security-center"></a>Introduktion til Azure Security Center

Få mere at vide om Azure Sikkerhedscenter, dens vigtige funktioner, og hvordan det fungerer.

> [AZURE.NOTE] Dette dokument introducerer tjenesten ved hjælp af en eksempel-installation.

## <a name="what-is-azure-security-center"></a>Hvad er Azure Security Center?
 Sikkerhedscenter hjælper dig med at forhindre, registrere og besvare trusler med øget indsigt i og kontrol over sikkerheden for dine Azure ressourcer. Det giver integreret sikkerhed overvågning og politik for administration på tværs af dine abonnementer på Azure, hjælper med at registrere trusler, der ellers går sig og fungerer med et omfattende netværk af sikkerhedsløsninger.

##  <a name="key-capabilities"></a>Vigtige funktioner
 Sikkerhedscenter leverer til brugervenlige og effektive truslen om forebyggelse, registrering og svar-funktioner, der er indbygget i til Azure. Vigtige funktioner er:

| | |
|----- |-----|
| Forhindre | Overvåger tilstanden sikkerhed for ressourcerne Azure |
| | Definerer politikker til Azure-abonnementer og ressourcegrupper, der er baseret på virksomhedens sikkerhedskrav typerne af programmer, du bruger og følsomheden for dine data |
| | Bruger politik-driven sikkerhedsanbefalinger til at hjælpe ejere af tjenesten gennem processen til implementering af behov kontrolelementer |
| | Hurtigt installerer sikkerhedstjenester og anvendelser fra Microsoft og partnere |
| Registrere |Indsamler og analyserer sikkerhedsdata fra Azure ressourcerne, netværk og partnerløsninger som Antimalwareprogram programmer og firewalls automatisk |
| | Udnytter globale trussel intelligence fra Microsoft-produkter og tjenester, Microsoft Digital forbrydelser enhed (DCU), Microsoft sikkerhed svar Center MSRC () og eksterne kilder |
| | Anvender avanceret analyse, herunder machine learning og funktionsmæssige analyse |
| Svar | Indeholder prioriterede hændelser/sikkerhedsadvarsler |
| | Giver indsigt i kilden til angreb og påvirkede ressourcer |
| | Forslag til at stoppe det aktuelle angreb og undgår fremtidige angreb |

## <a name="introductory-walkthrough"></a>Indledende gennemgang
 Du kan få adgang til Sikkerhedscenter fra [Azure-portalen](https://azure.microsoft.com/features/azure-portal/). [Log på portalen](https://portal.azure.com), Vælg **Gennemse**, og rul derefter ned til indstillingen **Sikkerhedscenter** eller vælge feltet **Sikkerhedscenter** , som du tidligere var fastgjort til portalen dashboard.

![Sikkerhed flise i Azure-portalen][1]

Fra Sikkerhedscenter, kan du angive sikkerhedspolitikker, overvåge sikkerhedskonfigurationer og få vist sikkerhedsadvarsler.

### <a name="security-policies"></a>Sikkerhedspolitikker

Du kan definere politikker til Azure-abonnementer og ressourcegrupper efter virksomhedens sikkerhedskrav. Du kan også tilpasse dem til typerne af programmer, du bruger eller til følsomheden for dataene i hvert abonnement. Ressourcer, der bruges til udvikling eller afprøvning kan for eksempel har forskellige sikkerhedskrav end dem, der anvendes til fremstilling programmer. På samme måde kan programmer med organiseret data som PII kræver et højere niveau af sikkerhed.

> [AZURE.NOTE] Hvis du vil ændre en sikkerhedspolitik på niveauet for abonnement eller ressourcegruppeniveau, skal du være ejeren af abonnementet eller en bidragyder til den.

Vælg feltet **politik** for en liste over dine abonnementer og ressourcegrupper på bladet **Sikkerhedscenter** .   

![Sikkerhedscenter blade][2]

Vælg et abonnement til at få vist oplysninger om politikken på bladet **sikkerhedspolitik** .

![Sikkerhed politik blade abonnement][3]

**Dataindsamling** (se ovenfor) gør det muligt for indsamling af data til en sikkerhedspolitik. Aktivering af giver:

- Daglig virusscanning af alle understøttes virtuelle maskiner til sikkerhed overvågning og anbefalinger.
- Samling af sikkerhed begivenheder til analyse og truslen registrering.

**Vælg en lagerplads konto per område** (se ovenfor) kan du vælge for hvert område, hvor du har virtuelle maskiner, der kører, kontoen lagerplads, hvor data, der indsamles fra disse virtuelle maskiner er gemt. Hvis du ikke vælger en lagerplads konto for hvert område, oprettes den for dig. De data, der indsamles er logisk adskilt fra andre kunders data af sikkerhedsmæssige årsager.

> [AZURE.NOTE] Indsamling af data og vælge en lagerplads konto per område er konfigureret på niveauet for abonnement.

Vælg **politik for beskyttelse mod** (se ovenfor) for at åbne bladet **politik for beskyttelse mod** . **Vise anbefalinger til** , kan du vælge den sikkerhedskontrol, du vil overvåge og anbefaler baseret på behovene sikkerhed af ressourcer i abonnementet.

Vælg derefter en ressourcegruppe til at få vist oplysninger om politikken.

![Sikkerhed politik blade ressourcegruppe][4]

**Nedarvning af** (se ovenfor) kan du angive ressourcegruppe som:

- Nedarvet (standard) hvilket betyder, at alle sikkerhedspolitikker for denne ressourcegruppe nedarves fra niveauet abonnement.
- Entydige hvilket betyder, at ressourcegruppen har en brugerdefineret sikkerhedspolitik. Du skal foretage ændringer under **Vis anbefalinger til**.

> [AZURE.NOTE] Hvis der er konflikt mellem abonnement niveau politik og ressource-niveau Gruppepolitik, har politikken ressource gruppe niveau højere prioritet.

### <a name="security-recommendations"></a>Anbefalinger til sikkerhed

 Sikkerhedscenter analyserer sikkerhed tilstanden for din Azure ressourcer for at identificere potentielle sikkerhedsrisici. En liste over anbefalinger fører dig gennem processen med at konfigurere nødvendige kontrolelementer. Som eksempler kan nævnes:

- Klargøring af Antimalwareprogram til at identificere og fjerne skadelig software
- Konfiguration af netværk sikkerhedsgrupper og regler til kontrolelementet trafik til den virtuelle maskiner
- Klargøring af web application firewalls kan beskytte mod angreb pågældende mål dine webprogrammer
- Implementering manglende systemopdateringer
- Vælge en adresse til OS konfigurationer, der ikke stemmer overens med de anbefalede grundlinjer

Klik på feltet **anbefalinger** til en liste over anbefalinger. Klik på hver anbefaling for at få vist yderligere oplysninger eller for at tage skridt for at løse problemet.

![Anbefalinger til sikkerhed i Sikkerhedscenter Azure][5]

### <a name="resource-health"></a>Ressource-tilstand

Feltet **ressource sikkerhed sundhed** viser den overordnede sikkerhed stilling af miljøet via ressourcetype, herunder virtuelle maskiner, webprogrammer og andre ressourcer.   

Vælg en ressourcetype i feltet **ressource sikkerhed tilstand** for at se flere oplysninger, herunder en liste over potentielle sikkerhedsrisici, der er identificeret. (**Virtuelle maskiner** er valgt i eksemplet nedenfor).

![Ressourcer sundhed felt][6]

### <a name="security-alerts"></a>Sikkerhedsadvarsler

 Sikkerhedscenter automatisk indsamler, analyserer og integrerer logdata fra Azure ressourcerne, netværk og partnerløsninger som Antimalwareprogram programmer og firewalls. Når trusler registreres, oprettes der en sikkerhedsadvarsel. Som eksempler kan nævnes påvisning af:

- Kompromitteret virtuelle maskiner kommunikere med kendte skadelig IP-adresser
- Avancerede malware registreres ved hjælp af Windows-fejlrapportering
- Råstyrke angreb på virtuelle maskiner
- Sikkerhedsadvarsler fra integreret Antimalwareprogram programmer og firewalls

Klik på feltet **sikkerhedsadvarsler** viser en liste over prioriterede beskeder.

![Sikkerhedsadvarsler][7]

Markere en besked viser flere oplysninger om angreb og forslag til, hvordan du afhjælpning af den.

![Oplysninger om sikkerhed påmindelser][8]

### <a name="partner-solutions"></a>Partnerløsninger

Feltet **partnerløsninger** kan du overvåge hurtigt sundhedsstatus for din partnerløsninger integreret med abonnementet Azure. Sikkerhedscenter viser beskeder, der kommer fra løsningerne.

Vælg feltet **partnerløsninger** . En blade åbnes viser en liste over alle forbundne partnerløsninger.

![Partnerløsninger][9]

## <a name="get-started"></a>Komme i gang
For at komme i gang med Sikkerhedscenter, skal du et abonnement på Microsoft Azure. Sikkerhedscenter er aktiveret til dit Azure-abonnement. Hvis du ikke har et abonnement, kan du tilmelde dig en [gratis prøveversion](https://azure.microsoft.com/pricing/free-trial/).

 Du kan få adgang til Sikkerhedscenter fra [Azure-portalen](https://azure.microsoft.com/features/azure-portal/). Se [portalen dokumentation](https://azure.microsoft.com/documentation/services/azure-portal/) for at få mere at vide.

[Introduktion til Azure Sikkerhedscenter](security-center-get-started.md) fører hurtigt dig gennem sikkerhed-overvågning og administration af gruppepolitik komponenter i Sikkerhedscenter.

## <a name="see-also"></a>Se også
I dette dokument, blev du introduceret til Sikkerhedscenter, dens vigtige funktioner, og hvordan du kommer i gang. Hvis du vil vide mere, skal du se følgende:

- [Angive sikkerhedspolitikker i Azure Sikkerhedscenter](security-center-policies.md) – Lær, hvordan du konfigurere sikkerhedspolitikker for dine Azure abonnementer og grupper.
- [Administrere sikkerhedsanbefalinger i Azure Sikkerhedscenter](security-center-recommendations.md) – Lær, hvordan anbefalinger hjælpe dig med at beskytte dine Azure ressourcer.
- [Sikkerhed sundhed overvågning i Azure Sikkerhedscenter](security-center-monitoring.md) – Lær, hvordan du overvåge tilstanden for dine Azure ressourcer.
- [Administrere og besvare sikkerhedsadvarsler i Azure Sikkerhedscenter](security-center-managing-and-responding-alerts.md) – Lær at administrere og reagere på beskeder om sikkerhed.
- [Overvågning partnerløsninger med Azure Security Center](security-center-partner-solutions.md) – Lær, hvordan du overvåge systemtilstand status for din partnerløsninger.
- [Azure Security Center ofte stillede spørgsmål](security-center-faq.md) – Find ofte stillede spørgsmål om ved hjælp af tjenesten.
- [Azure sikkerhed blog](http://blogs.msdn.com/b/azuresecurity/) – få de seneste nyheder og Azure sikkerhed og oplysninger.

<!--Image references-->
[1]: ./media/security-center-intro/security-tile.png
[2]: ./media/security-center-intro/security-center.png
[3]: ./media/security-center-intro/security-policy.png
[4]: ./media/security-center-intro/security-policy-blade.png
[5]: ./media/security-center-intro/recommendations.png
[6]: ./media/security-center-intro/resources-health.png
[7]: ./media/security-center-intro/security-alert.png
[8]: ./media/security-center-intro/security-alert-detail.png
[9]: ./media/security-center-intro/partner-solutions.png
