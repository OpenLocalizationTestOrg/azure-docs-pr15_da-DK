<properties
   pageTitle="Håndtering af sikkerhedshændelse i Azure Sikkerhedscenter | Microsoft Azure"
   description="Dette dokument hjælper dig med at bruge Azure Security Center-funktioner til at håndtere sikkerhedshændelser."
   services="security-center"
   documentationCenter="na"
   authors="YuriDio"
   manager="swadhwa"
   editor=""/>

<tags
   ms.service="security-center"
   ms.topic="hero-article"
   ms.devlang="na"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="10/18/2016"
   ms.author="yurid"/>

# <a name="handling-security-incident-in-azure-security-center"></a>Håndtering af sikkerhedshændelse i Azure Sikkerhedscenter 
Triaging og undersøge sikkerhedsadvarsler kan være tidskrævende for endnu mest kvalificerede sikkerhed analytikere og for mange det er svært at engang, hvor man skal begynde. Ved hjælp af [analyse](security-center-detection-capabilities.md) til at oprette forbindelse oplysninger mellem forskellige [sikkerhedsadvarsler](security-center-managing-and-responding-alerts.md), Sikkerhedscenter kan give dig en enkelt visning af en angreb kampagnens og alle de relaterede påmindelser – du kan hurtigt at forstå, hvad hackeren tog, og hvilke ressourcer er blevet påvirket.

Dette dokument beskrives, hvordan du bruger beskeder om sikkerhedsfunktioner i Sikkerhedscenter til at hjælpe dig håndtering af sikkerhedshændelser.


## <a name="what-is-a-security-incident"></a>Hvad er en sikkerhedshændelse?

I Sikkerhedscenter er en sikkerhed en sammenlægning af alle beskeder for en ressource, som Juster med [afbryde kæde](https://blogs.technet.microsoft.com/office365security/addressing-your-cxos-top-five-cloud-security-concerns/) mønstre. Hændelser vises i feltet [Sikkerhedsadvarsler](security-center-managing-and-responding-alerts.md) og blade. En hændelse kan afsløre den liste over relaterede påmindelser, som gør det muligt at få flere oplysninger om hver forekomst.

## <a name="managing-security-incidents"></a>Administrere sikkerhedshændelser

Du kan gennemse dine aktuelle sikkerhed henvendelser ved at se på feltet sikkerhed beskeder. Få adgang til portalen Azure, og følg trinnene nedenfor for at se flere oplysninger om hver enkelt sikkerhedshændelse:

1. Du får vist feltet **sikkerhedsadvarsler** på dashboardet til Sikkerhedscenter.

    ![Sikkerhedsadvarsler flisen i Sikkerhedscenter](./media/security-center-incident/security-center-incident-fig1.png)

2.  Klik på dette felt til at udvide den, og hvis der registreres en sikkerhedshændelse, vises den under sikkerhed beskeder diagrammet som vist nedenfor:

    ![Sikkerhedshændelse](./media/security-center-incident/security-center-incident-fig2.png)

3.  Bemærk, at beskrivelsen sikkerhed hændelsen har et andet ikon sammenlignet med andre beskeder. Klik på den for at få vist flere oplysninger om denne hændelse.

    ![Sikkerhedshændelse](./media/security-center-incident/security-center-incident-fig3.png)

4.  På den **hændelse** blade, du vil se flere detaljer om denne sikkerhedshændelse,, som omfatter fuld beskrivelsen, dens alvor (som i dette tilfælde er høj), skal den aktuelle tilstand (i dette tilfælde den er stadig *aktive*, der angiver, at brugeren ikke er taget en handling til at *afvise* det - dette kan gøres ved at højreklikke på hændelsen i bladet **sikkerhedsadvarsler** ) , angrebet ressourcen (i dette tilfælde *VM1*), hvordan trin for hændelsen, og i den nederste rude du har de vigtige beskeder, der blev inkluderet i denne hændelse. Hvis du vil have flere oplysninger om hver enkelt besked, åbnes bare Klik på det og en anden blade, som vist nedenfor:

    ![Sikkerhedshændelse](./media/security-center-incident/security-center-incident-fig4.png)

Oplysningerne på denne blade varierer efter beskeden. Læs [administrere og besvare sikkerhedsadvarsler i Azure Sikkerhedscenter](security-center-managing-and-responding-alerts.md) kan finde flere oplysninger om, hvordan du administrere disse beskeder. Nogle vigtige overvejelser angående denne egenskab:

- Et nyt filter giver dig mulighed at tilpasse visningen kun hændelse, kun beskeder eller begge dele. 
- Samme påmindelsen kan findes som en del af en hændelse (hvis relevant) og kan ses som en enkeltstående besked. 
- Afviser en hændelse, vil de relaterede påmindelser ikke Afvis.

## <a name="see-also"></a>Se også

I dette dokument, du har lært Sådan bruges hændelsen muligheden for sikkerhed i Sikkerhedscenter. Hvis du vil vide mere om Sikkerhedscenter, skal du se følgende:

- [Administrere og besvare sikkerhedsadvarsler i Azure Sikkerhedscenter](security-center-managing-and-responding-alerts.md)
- [Azure Sikkerhedscenter registrering af funktioner](security-center-detection-capabilities.md)
- [Azure Sikkerhedscenter planlægning og Operations Guide](security-center-planning-and-operations-guide.md)
- [Administrere og besvare sikkerhedsadvarsler i Azure Sikkerhedscenter](security-center-managing-and-responding-alerts.md)
- [Azure Security Center ofte stillede spørgsmål](security-center-faq.md)– Find ofte stillede spørgsmål om ved hjælp af tjenesten.
- [Azure sikkerhed blog](http://blogs.msdn.com/b/azuresecurity/)– Find blog indlæg om Azure sikkerhed og overholdelse af regler.
