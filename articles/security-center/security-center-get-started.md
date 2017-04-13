<properties
   pageTitle="Startvejledning til Azure Sikkerhedscenter | Microsoft Azure"
   description="I denne artikel hjælper dig med at komme hurtigt i gang med Azure Security Center ved at lede dig gennem sikkerhed overvågning og politik for administration af komponenterne og opretter forbindelse til de næste trin."
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
   ms.date="10/28/2016"
   ms.author="terrylan"/>

# <a name="azure-security-center-quick-start-guide"></a>Startvejledning til Azure Security Center

I denne artikel kan du hurtigt komme i gang med Azure Sikkerhedscenter ved at lede dig gennem sikkerhed overvågning og politik for administration af komponenterne i Sikkerhedscenter.

> [AZURE.NOTE] I denne artikel introducerer tjenesten ved hjælp af en eksempel-installation. I denne artikel er ikke en trinvis vejledning.

## <a name="prerequisites"></a>Forudsætninger

For at komme i gang med Sikkerhedscenter, skal du have et abonnement på Microsoft Azure. Hvis du ikke har et abonnement, kan du tilmelde dig en [gratis konto](https://azure.microsoft.com/pricing/free-trial/).

Det gratis niveau i Sikkerhedscenter er automatisk aktiveret til dit abonnement og giver indsigt i tilstanden sikkerhed for ressourcerne Azure. Den indeholder administration af grundlæggende sikkerhedspolitik, anbefalinger til sikkerhed og integration med sikkerhedsprodukter og tjenester fra Azure partnere.

Du kan få adgang til Sikkerhedscenter fra [Azure-portalen](https://azure.microsoft.com/features/azure-portal/). Hvis du vil vide mere om Azure portalen skal du i [portalen dokumentation](https://azure.microsoft.com/documentation/services/azure-portal/).

## <a name="data-collection"></a>Dataindsamling

Sikkerhedscenter indsamler data fra din virtuelle maskiner (VM'er) til at vurdere deres sikkerhedstilstand, giver anbefalinger til sikkerhed, og giver dig besked om trusler. Når du først åbne Sikkerhedscenter, aktiveres dataindsamling på alle FOS i dit abonnement. Dataindsamling anbefales, men du kan fravælge ved at deaktivere dataindsamling i politikken Sikkerhedscenter.

Følgende trin beskriver, hvordan man åbner og bruger komponenterne i Sikkerhedscenter. I disse trin viser vi dig hvordan du deaktiverer dataindsamling, hvis du vælger at fravælge.

## <a name="access-security-center"></a>Access Sikkerhedscenter

Følg disse trin for at få adgang til Sikkerhedscenter i portalen:

1. Vælg **Sikkerhedscenter**i **Microsoft Azure** -menuen.
![Azure-menuen][1]

2. Hvis du får adgang til Sikkerhedscenter for første gang, åbnes bladet **Velkommen** . Vælg Ja, **! Jeg vil Start Azure Sikkerhedscenter** til at åbne bladet **Sikkerhedscenter** og aktivere dataindsamling.
![Velkomstsiden][10]

3. Når du Start Sikkerhedscenter fra bladet Velkommen eller vælge Sikkerhedscenter i menuen Microsoft Azure, åbnes bladet **Sikkerhedscenter** . Nem adgang til bladet **Sikkerhedscenter** i fremtiden, Vælg indstillingen **Fastgør blade til dashboard** (øverst til højre).
![Fastgør blade til dashboard indstilling][2]

## <a name="use-security-center"></a>Bruge Sikkerhedscenter

Du kan konfigurere sikkerhedspolitikker til Azure-abonnementer og grupper. Lad os konfigurere en sikkerhedspolitik for dit abonnement:

1. Vælg feltet **politik** på bladet **Sikkerhedscenter** .
![Sikkerhedspolitik][3]

2. Vælg et abonnement på bladet **sikkerhedspolitik - definere politik for hver abonnement eller ressourcegruppe** .
3. **Dataindsamling** er aktiveret til at indsamle logge automatisk på bladet **sikkerhedspolitik** . Filtypenavnet overvågning er klargjort på alle aktuelle og nye FOS i abonnement. (Du kan fravælge dataindsamling ved at angive **dataindsamling** til **fra**, men dette forhindrer Sikkerhedscenter, hvorved du får sikkerhedsadvarsler og anbefalinger).
4. Vælg **Vælg en lagerplads konto per område**på bladet **sikkerhedspolitik** . For hvert område, hvor du har VM'er, der kører, kan du vælge kontoen lagerplads, hvor data, der indsamles fra disse FOS er gemt. Hvis du ikke vælger en lagerplads konto for hvert område, oprettes den for dig. De data, der indsamles er logisk adskilt fra andre kunders data af sikkerhedsmæssige årsager.

     > [AZURE.NOTE] Vi anbefaler, at du aktiverer dataindsamling og vælger en lagerplads konto på niveauet for abonnement først. Sikkerhedspolitikker kan angives i Azure abonnementsniveau og ressource grupperingsniveau, men konfigurationen af dataindsamling og -lagerplads konto optræder på niveauet abonnement.

5. Vælg **politik for beskyttelse mod**bladet **sikkerhedspolitik** . Dette åbner bladet **politik for beskyttelse mod** .
![Politik for beskyttelse mod][4]

6. Aktivér de anbefalinger, som du vil have vist som en del af din sikkerhedspolitik under bladet **politik for beskyttelse mod** . Eksempler:

 - Indstille **systemopdateringer** til **på** scanner alle understøttede virtuelle maskiner til manglende OS opdateringer.
 - Indstille **OS svagheder** til **på** scanner alle understøttede virtuelle maskiner for at finde en hvilken som helst OS konfigurationer, som kan gøre den virtuelle maskine mere udsatte for angreb.

### <a name="view-recommendations"></a>Vis anbefalinger

1. Gå tilbage til bladet **Sikkerhedscenter** , og Vælg feltet **anbefalinger** . Sikkerhedscenter analyserer med jævne mellemrum tilstanden sikkerhed for ressourcerne Azure. Når Sikkerhedscenter identificerer potentielle sikkerhedsrisici, viser anbefalinger på bladet **anbefalinger** .
![Anbefalinger i Azure Sikkerhedscenter][5]

2.  Vælg en anbefaling på bladet **anbefalinger** til at få vist yderligere oplysninger og/eller foretage dig noget for at løse problemet.

### <a name="view-the-health-and-security-state-of-your-resources"></a>Få vist tilstand og sikkerhed tilstanden for dine ressourcer

1.  Gå tilbage til bladet **Sikkerhedscenter** . Feltet **ressourcer sikkerhed sundhed** indeholder indikatorer for tilstanden sikkerhed for virtuelle maskiner, netværk, data og -programmer.
2.  Vælg **virtuelle computere** at få flere oplysninger. Bladet **virtuelle maskiner** åbnes og viser en status oversigt over Antimalwareprogram programmer, opdateringer, genstarter og OS svagheder af din FOS.
![Feltet ressourcer tilstand i Azure Sikkerhedscenter][6]

3.  Vælg en anbefaling under **VIRTUELT anbefalinger** til at få vist flere oplysninger og/eller handle til at konfigurere nødvendige kontrolelementer.
4.  Vælg en VM under **virtuelle computere** at få vist yderligere oplysninger.

### <a name="view-security-alerts"></a>Få vist sikkerhedsadvarsler

1.  Gå tilbage til bladet **Sikkerhedscenter** , og Vælg feltet **sikkerhedsadvarsler** . Bladet **sikkerhedsadvarsler** åbnes og viser en liste over beskeder. Analysen Security Center for sikkerhedslogfiler og aktivitet på netværket genererer disse beskeder. Beskeder fra integreret partnerløsninger er inkluderet.
![Sikkerhedsadvarsler i Azure Sikkerhedscenter][7]

    > [AZURE.NOTE] Sikkerhedsadvarsler er kun tilgængelig, hvis det Standard niveau i Sikkerhedscenter. Der findes en 90 dages gratis prøveversion af Standard-niveauet. Du kan finde oplysninger om, hvordan du får det Standard niveau i [Næste trin](#next-steps) .

2.  Vælg en besked til at få vist yderligere oplysninger. I dette eksempel Lad os markere **binær ændret systemfil opdaget**. Dette åbner blade, som giver yderligere oplysninger om påmindelsen.
![Sikkerhed beskeder om detaljer i Azure Sikkerhedscenter][8]

### <a name="view-the-health-of-your-partner-solutions"></a>Få vist tilstanden for din partnerløsninger

1. Gå tilbage til bladet **Sikkerhedscenter** . Feltet **partnerløsninger** kan du overvåge systemtilstand status for din partnerløsninger, der er integreret med abonnementet Azure overblik.
2. Vælg feltet **partnerløsninger** . En blade åbnes og viser en liste over dine partnerløsninger, der er knyttet til Sikkerhedscenter.
![Partnerløsninger][9]

3. Vælg en partnerløsning. Lad os markere **F5 WAF** løsningen i dette eksempel.  En blade åbnes og viser status for partner løsningen og den løsning tilknyttede ressourcer. Vælg **løsning console** for at åbne partner administration oplevelsen for denne løsning.

## <a name="next-steps"></a>Næste trin
I denne artikel introduceret du til sikkerhed overvågning og politik for administration af komponenterne i Sikkerhedscenter. Nu hvor du er vant til i Sikkerhedscenter, kan du prøve følgende trin:

- Konfigurere en sikkerhedspolitik for abonnementet Azure. Se [indstillingen sikkerhedspolitikker i Azure Security Center](security-center-policies.md)for at få mere for at vide.
- Brug anbefalingerne i Sikkerhedscenter til at hjælpe dig med at beskytte dine Azure ressourcer. Se [administrere sikkerhedsanbefalinger i Azure Security Center](security-center-recommendations.md)for at få mere for at vide.
- Gennemse og administrere din aktuelle sikkerhedsadvarsler. For at få mere for at vide, skal du se [administrere og besvare sikkerhedsadvarsler i Azure Sikkerhedscenter](security-center-managing-and-responding-alerts.md).
- Få mere at vide om [Avancerede truslen registrering af funktioner](security-center-detection-capabilities.md) , der følger med det [almindelige niveau](security-center-pricing.md) i Sikkerhedscenter. Der findes en 90 dages gratis prøveversion af Standard-niveauet.
- Hvis du har spørgsmål om brug af Sikkerhedscenter, skal du se [Azure Security Center ofte stillede spørgsmål](security-center-faq.md).

<!--Image references-->
[1]: ./media/security-center-get-started/azure-menu.png
[2]: ./media/security-center-get-started/security-center-pin.png
[3]: ./media/security-center-get-started/security-policy.png
[4]: ./media/security-center-get-started/prevention-policy.png
[5]: ./media/security-center-get-started/recommendations.png
[6]: ./media/security-center-get-started/resources-health.png
[7]: ./media/security-center-get-started/security-alert.png
[8]: ./media/security-center-get-started/security-alert-detail.png
[9]: ./media/security-center-get-started/partner-solutions.png
[10]: ./media/security-center-get-started/welcome.png
