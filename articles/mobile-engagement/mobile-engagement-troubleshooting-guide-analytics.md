<properties 
   pageTitle="Azure Mobile aftale fejlfindingsvejledningen - analyser" 
   description="Foretage fejlfinding af problemer med analyse, overvågning, segmentering og Dashboard i Azure Mobile aftale" 
   services="mobile-engagement" 
   documentationCenter="" 
   authors="piyushjo" 
   manager="dwrede" 
   editor=""/>

<tags
   ms.service="mobile-engagement"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="mobile-multiple"
   ms.workload="mobile" 
   ms.date="08/19/2016"
   ms.author="piyushjo"/>

# <a name="troubleshooting-guide-for-analytics-monitoring-segmentation-and-dashboard-issues"></a>Fejlfinding i forbindelse med vejledning til analyse, overvågning, segmentering og Dashboard problemer

Følgende er mulige problemer, der kan opstå med hvordan Azure Mobile aftale indsamler oplysninger om dine programmer, enheder og brugere.

## <a name="missingdelayed-information"></a>Manglende/forsinket oplysninger

### <a name="issue"></a>Problem
- Du kan finde oplysninger er forsinket i der vises i Analytics, segmentering eller Dashboard.
- Der mangler oplysninger fra overvågnings.
- Der mangler oplysninger fra Analytics, segmentering eller Dashboard.
- Nå segmentering begrænsninger.

### <a name="causes"></a>Årsager

- Du kan bruge Analytics-API skærm API og segmenter API til at se, om alle data, du er manglende fra Brugergrænsefladen er synlige gennem API'erne.
- Hvis Azure Mobile aftale SDK ikke korrekt er integreret i din app derefter du ikke kunne se oplysningerne i Analytics, segmentering, overvågning eller Dashboards.
- Segmenter kan ikke ændres, når de er oprettet, segmenter kan kun være "klonede" (kopieret) eller "ødelagt" (slettet). Segmenter kan kun indeholde 10 kriterier.
- Den bedste måde at teste manglende oplysninger fra overvågning er at konfigurere en test-enhed, fjerne og/eller geninstallere appen på enheden test.
- Du kan finde oplysninger opdateres døgnet for Analytics, segmentering eller Dashboards.
- Oplysninger i nye målgrupper vises muligvis ikke indtil 24 timer, når de er oprettet, selvom målgruppen er baseret på forrige oplysninger.
- Filtrering af data i Brugergrænsefladen analytics, vises alle eksempler på denne type uanset hvilken version af din app (fx "nedbrud" filtreret efter navn vises fra version 1 og 2 af din app).
- Hvor lang tid for analyser er baseret på datoen fra brugernes Enhedsindstillinger, så en bruger, hvis phone har datoen, der er forkert angivet kunne ses i den forkerte tidsperiode.
- Ingen data logføres, når du bruger knappen "teste" serversiden skubber, data logføres kun for reelle opslagsnål kampagner.

## <a name="cant-locate-items-in-ui"></a>Kan ikke finde elementer i Brugergrænsefladen

### <a name="issue"></a>Problem
- Kan ikke oprette målgrupper, der er baseret på bestemte indbygget eller brugerdefineret app oplysninger mærke kriterier.
- Kan ikke finde bestemte indbygget eller brugerdefineret app oplysninger mærke kriterier i analyse, overvågning eller Dashboards.
- Kan ikke fortolke dataene i analyse, overvågning, segmentering eller Dashboards.

### <a name="causes"></a>Årsager

- Nogle indbygget i elementer og app oplysninger mærker er kun tilgængelige som push kriterier men muligvis ikke føjet til en målgruppe eller synlige fra analyse, overvågning eller Dashboard. 
- For indbygget elementer og app oplysninger mærker, der ikke kan føjes til en målgruppe, skal du konfiguration liste over målretning af kriterier i hver kampagnens til at udføre den samme funktion som målretning af baseret på en målgruppe.
- Se genvejsmenuer i sektionerne analyse, overvågning, segmentering og Dashboards i Azure Mobile aftale Brugergrænsefladen for mere hjælp, og hvordan du kan finde oplysninger.

## <a name="crash-troubleshooting"></a>Ned fejlfinding

### <a name="issue"></a>Problem
- Programmet går ned, der vises i analyse, overvågning eller Dashboard.

### <a name="causes"></a>Årsager

- Hvis du vil foretage fejlfinding af skal programmet går ned, fremgår af analyse, overvågning eller Dashboard du kontrollere produktbemærkninger til kendte problemer med tidligere versioner af SDK.
- Yderligere fejlfinding udføre programmer går ned en begivenhed i en test enhed med dit program, der er installeret og udseendet af din enheds-ID i afsnittet "Overvåge – hændelser" i Azure Mobile aftale Brugergrænsefladen. Udfør derefter den begivenhed, der forårsager programmet går ned, og se yderligere oplysninger i afsnittet "Skærm – nedbrud" i Azure Mobile aftale Brugergrænsefladen. 

 
