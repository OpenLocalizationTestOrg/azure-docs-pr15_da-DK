<properties
   pageTitle="Azure Sikkerhedscenter truslen Intelligence rapport | Microsoft Azure"
   description="Dette dokument hjælper dig med at bruge Azure Security Center truslen Intelligent rapporter under en undersøgelse til at finde flere oplysninger om en sikkerhedsadvarsel."
   services="security-center"
   documentationCenter="na"
   authors="YuriDio"
   manager="swadhwa"
   editor=""/>

<tags
   ms.service="security-center"
   ms.devlang="na"
   ms.topic="hero-article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="10/17/2016"
   ms.author="yurid"/>

# <a name="azure-security-center-threat-intelligence-report"></a>Azure Sikkerhedscenter truslen Intelligence rapport
Dette dokument forklares, hvordan Azure Security Center truslen Intelligent rapporter kan hjælpe dig med at lære mere om en truslen, der genereres en sikkerhedsadvarsel.

## <a name="what-is-a-threat-intelligence-report"></a>Hvad er en truslen intelligence rapport?
Sikkerhedscenter truslen registrering fungerer ved at overvåge sikkerhedsoplysninger fra ressourcerne Azure, netværk og forbundne partnerløsninger. Det analyserer disse oplysninger, der ofte korrelere oplysninger fra flere kilder til at identificere trusler. Denne proces er en del af Sikkerhedscenter [registrering af funktioner](security-center-detection-capabilities.md). 

Når Sikkerhedscenter identificerer et truslen, udløser en [Sikkerhedsadvarsel](security-center-managing-and-responding-alerts.md), som indeholder detaljerede oplysninger om en bestemt hændelse, herunder forslag til afhjælpning. For at hjælpe hændelsen svar teams undersøge og afhjælpning trusler, Security Center indeholder en truslen intelligence rapport, der indeholder oplysninger om truslen, der blev registreret, herunder oplysninger som den: 

- Hacker identitet eller tilknytninger (hvis disse oplysninger er tilgængelige)
- Hackere mål
- Aktuelle og historiske angreb kampagner (hvis disse oplysninger er tilgængelige)
- Hackere taktikker, værktøjer og procedurer
- Tilknyttede indikatorer af skade (IoC) som URL-adresser og hashværdierne
- Victimology, som er branche og geografiske forekomsten hjælper dig med at afgøre, hvis ressourcerne Azure risikerer
- Oplysninger om afhjælpning og afhjælpning

>[AZURE.NOTE] Mængden oplysninger i en bestemt rapport varierer; detaljeniveauet er baseret på malwaren aktivitet og forekomsten.

Sikkerhedscenter har tre typer truslen rapporter, som kan variere, alt efter angrebet. De tilgængelige rapporter er:

- **Aktivitet grupperapport**: giver dybden i hackere, deres målsætninger og taktikker.
- **Kampagnens rapport**: fokuserer på oplysninger om bestemte angreb kampagner. 
- **Truslen oversigtsrapport**: dækker alle elementer i de forrige to rapporter.

Denne type oplysninger være meget nyttigt under [hændelsen svar](security-center-incident-response.md) processen, hvor der ikke er en igangværende undersøgelse for at forstå kilden til angrebet, den hacker motivationer og hvad du skal gøre for at reducere problemet fremover. 

## <a name="how-to-access-the-threat-intelligence-report"></a>Om at åbne rapporten truslen intelligence?

Du kan gennemse dine aktuelle beskeder ved at se på feltet **sikkerhedsadvarsler** . Åbn Azure-portalen, og følg trinnene nedenfor for at se flere oplysninger om hver enkelt besked:

1. Du får vist feltet **sikkerhedsadvarsler** på dashboardet til Sikkerhedscenter.

2. Klik på feltet for at åbne bladet **sikkerhedsadvarsler** , der indeholder flere oplysninger om de vigtige beskeder, og klik på i sikkerhedsmeddelelsen for, som du ønsker at få mere at vide om.

    ![Sikkerhedsadvarsler](./media/security-center-threat-report/security-center-threat-report-fig1.png)

3. I dette tilfælde vises bladet **mistænkelige proces, der udføres** oplysninger om den vigtige besked, som vist i nedenstående illustration:

    ![Sikkerhed beskeder om detais](./media/security-center-threat-report/security-center-threat-report-fig2.png)

4.  Mængden oplysninger, der er tilgængelige for hver sikkerhedsmeddelelsen varierer afhængigt af besked om den. I feltet **rapporter** har du et link til truslen intelligence rapporten. Klik på den, og der vises en anden browservindue med PDF-fil.

    ![Lagerplads markering](./media/security-center-threat-report/security-center-threat-report-fig3.png)

Du kan hente PDF-filen til denne rapport og læse mere om det sikkerhedsproblem, der blev registreret og udføre handlinger, der er baseret på de oplysninger, der er angivet her.

## <a name="see-also"></a>Se også

I dette dokument, du har lært hvordan Azure Security Center truslen Intelligent rapporter kan hjælpe under en undersøgelse om sikkerhedsadvarsler. Hvis du vil vide mere om Azure Sikkerhedscenter, skal du se følgende:

- [Ofte stillede spørgsmål om azure Sikkerhedscenter](security-center-faq.md). Find ofte stillede spørgsmål om ved hjælp af tjenesten.
- [Udnytte Azure Sikkerhedscenter for hændelsen svar](security-center-incident-response.md)
- [Azure Sikkerhedscenter registrering af funktioner](security-center-detection-capabilities.md)
- [Vejledning i planlægning af azure Sikkerhedscenter og handlinger](security-center-planning-and-operations-guide.md). Lær, hvordan du planlægger og forstå Designovervejelser at indføre Azure Sikkerhedscenter.
- [Administrere og besvare sikkerhedsadvarsler i Azure Sikkerhedscenter](security-center-managing-and-responding-alerts.md). Lær at administrere og reagere på beskeder om sikkerhed.
- [Håndtering af sikkerhedshændelse i Azure Sikkerhedscenter](security-center-incident.md)
- [Azure sikkerhed Blog](http://blogs.msdn.com/b/azuresecurity/). Du kan finde blogindlæg om Azure sikkerhed og overholdelse af regler.
