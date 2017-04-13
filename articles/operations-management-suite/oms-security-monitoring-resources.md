<properties
   pageTitle="Overvåge ressourcer i handlinger Management pakke sikkerhed og Overvåg løsning | Microsoft Azure"
   description="Dette dokument hjælper dig med at bruge OMS sikkerhed og overvågning funktioner til at overvåge dine ressourcer og identificere problemer med sikkerhed."
   services="operations-management-suite"
   documentationCenter="na"
   authors="YuriDio"
   manager="swadhwa"
   editor=""/>

<tags
   ms.service="operations-management-suite"
   ms.topic="article" 
   ms.devlang="na"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="10/18/2016"
   ms.author="yurid"/>

# <a name="monitoring-resources-in-operations-management-suite-security-and-audit-solution"></a>Overvågning af ressourcer i handlinger Management pakke sikkerhed og Overvåg løsning

Dette dokument hjælper dig med at bruge OMS sikkerhed og Overvåg funktioner til at overvåge dine ressourcer og identificere problemer med sikkerhed.

## <a name="what-is-oms"></a>Hvad er OMS?

Microsoft Operations Management pakke (OMS) er Microsofts skyen baseret IT management-løsning, der hjælper dig med at administrere og beskytte dine lokale og infrastruktur i skyen. Du kan finde flere oplysninger om OMS du læse artiklen [Handlinger Management-pakken](https://technet.microsoft.com/library/mt484091.aspx).

## <a name="monitoring-resources"></a>Overvåge ressourcer

Når det er muligt, du vil forhindre, at sikkerhedshændelser sker der i første omgang. Det er dog ikke muligt at forhindre, at alle sikkerhedshændelser. Når en sikkerhedshændelse sker, skal du sikre dig, at dens virkning er minimeret.  Der er tre vigtige anbefalinger, der kan bruges til at minimere antallet og påvirkningen af sikkerhedshændelser:

- Vurder regelmæssigt svagheder i dit miljø.
- Kontrollér regelmæssigt alle computersystemer og netværksenheder at sikre, at de har alle de seneste rettelser, der er installeret.
- Kontrollér regelmæssigt alle logfiler og logføring godkendelsesmetoder, herunder operativsystem-hændelseslogge, programmet bestemte logfiler og uautoriseret adgang registrering systemets logfiler.

OMS sikkerhed og Overvåg løsning gør det muligt for IT til at overvåge aktivt alle de ressourcer, som kan hjælpe med at minimere påvirkningen af sikkerhedshændelser. OMS sikkerhed og Overvåg har sikkerhed domæner, der kan bruges til at overvåge ressourcer. Sikkerhed domænerne giver hurtig adgang til en indstillinger for sikkerhed overvågning følgende domæner, der skal gennemgås i få mere at vide:

- Malware vurdering
- Opdatere vurdering
- Identitets- og

> [AZURE.NOTE] Læs [Introduktion til handlinger Management pakke sikkerhed og Overvåg løsning](oms-security-getting-started.md)for en oversigt over alle disse indstillinger.

### <a name="monitoring-system-protection"></a>Overvågning Systembeskyttelse

Hver lag af beskyttelse er vigtige for den overordnede sikkerhedstilstand for dit aktiv i en forsvarslinjer i dybde tilgang. Med fundet trusler og computere med utilstrækkelig beskyttelse vises i feltet Malware vurdering under sikkerhed domæner. Du kan identificere en plan til at anvende beskyttelse på servere, der har brug for den ved hjælp af oplysningerne på den Malware vurdering. For at få adgang til denne indstilling, skal du følge nedenstående trin:

1. Klik på **sikkerhed og Overvåg** felt i **Microsoft Operations Management Suite** primære dashboard.

    ![Sikkerhed og overvågning](./media/oms-security-responding-alerts/oms-security-responding-alerts-fig1.png)

2. Klik på **Antimalwareprogram vurdering** under **Sikkerhed domæner**i dashboardet **sikkerhed og overvågning** . Dashboardet **Antimalwareprogram vurdering** ser ud, som vist nedenfor:

![Malware vurdering](./media/oms-security-monitoring-resources/oms-security-monitoring-resources-fig2-ga.png)

Du kan bruge dashboardet **Malware vurdering** til at identificere de følgende sikkerhedsproblemer:

- **Aktive trusler**: computere, der er blevet afsløret og har aktive trusler i systemet.
- **Remediated trusler**: computere, som er blevet afsløret, men truslerne blev remediated.
- **Signatur forældet**: computere, som har beskyttelse mod skadelig software aktiveret, men signaturen er forældet.
- **Ingen beskyttelse i realtid**: computere, der ikke har installeret Antimalwareprogram.

### <a name="monitoring-updates"></a>Overvågning af opdateringer 

Anvende de nyeste sikkerhedsopdateringer er sikkerhedsmæssige årsager, og det bør fremgå af din strategi for administration af opdatering. Tjenesten Microsoft overvågning Agent (HealthService.exe) læser opdatering fra overvåget computere og sender derefter denne opdaterede oplysninger til tjenesten OMS i skyen for behandling. Tjenesten Microsoft overvågning Agent er konfigureret som en tjeneste, der automatisk, og den altid skal køres i den destinationsadresse-computer.

![overvågning af opdateringer](./media/oms-security-monitoring-resources/oms-security-monitoring-resources-fig3.png)

Logik anvendes til at opdateringsdata og skytjenesten registrerer dataene. Hvis der findes manglende opdateringer, vises de på dashboardet til **opdateringer** . Du kan bruge dashboardet for **opdateringer** til at arbejde med manglende opdateringer og udvikle en plan for at anvende dem på servere, der har brug for dem. Følg nedenstående trin for at få adgang til dashboardet **opdateringer** :

1. Klik på **sikkerhed og Overvåg** felt i **Microsoft Operations Management Suite** primære dashboard.
2. Klik på **Opdater vurdering** under **Sikkerhed domæner**i dashboardet **sikkerhed og overvågning** . Opdater dashboard ser ud, som vist nedenfor:

![opdatere vurdering](./media/oms-security-monitoring-resources/oms-security-monitoring-resources-fig4.png)

Du kan udføre en opdatering vurdering for at forstå den aktuelle status for dine computere og en adresse til de vigtigste trusler i dette dashboard. Ved hjælp af feltet **kritisk eller sikkerhedsopdateringer** , kan IT-administratorer få adgang til detaljerede oplysninger om de opdateringer, der mangler, som vist nedenfor:

![et søgeresultat](./media/oms-security-monitoring-resources/oms-security-monitoring-resources-fig5.png)

Denne rapport indeholder vigtige oplysninger, der kan bruges til at identificere typen truslen dette system er udsatte til, hvilket omfatter i Microsoft KB artikler, der er knyttet til sikkerhedsopdateringen og MS bulletinen, der indeholder flere oplysninger om sikkerhedsrisikoen.

### <a name="monitoring-identity-and-access"></a>Overvågning identitets- og

Det er vigtigt, at deres legitimationsoplysninger er beskyttet med brugere, der arbejder overalt, ved hjælp af forskellige enheder og få adgang til en stor mængde skyen og on-premises apps. Legitimationsoplysninger identitetstyveri angreb er dem som en hacker først får adgang til en almindelig bruger logonoplysninger for at få adgang til et system i netværket. Mange gange dette indledende angreb er kun en metode til at få adgang til netværket, det endelige mål er at finde rettighed konti. 

Hackere forbliver i netværket, ved hjælp af frit tilgængelige værktøjer for at udtrække legitimationsoplysninger fra sessioner af andre konti, der er logget på. Afhængigt af systemkonfigurationen, kan disse legitimationsoplysninger udtrækkes i form af hashes, billetter eller adgangskoder med lige almindelig tekst.  

> [AZURE.NOTE] maskiner, der vises direkte på internettet vises mange mislykkede forsøg, der forsøger at logge på ved hjælp af alle slags kendte brugernavne (fx Administrator). I de fleste tilfælde er det OK, hvis de velkendte brugernavne ikke anvendes, og hvis adgangskoden er stærke nok.

Det er muligt at identificere disse hackere, før de forringer en rettighed konto. Du kan udnytte **OMS sikkerhed og Overvåg løsning** for at overvåge identitet og adgang. Følg nedenstående trin for at få adgang til dashboardet for **identitets- og** :

1. Klik på sikkerhed og Overvåg i dashboardet **Microsoft handlinger Management Suite** primære side om side.
2. Klik på **identitets- og** under **Sikkerhed domæner**i dashboardet **sikkerhed og overvågning** . Dashboardet for **identitets- og** vises som vist nedenfor:

![identitets- og](./media/oms-security-monitoring-resources/oms-security-monitoring-resources-fig6-ga.png)

Som en del af din almindelige strategi for overvågning, skal du medtage identitet overvågning. IT-administratoren skal se ud, hvis der er et bestemt gyldigt brugernavn, der indeholder mange forsøg. Dette kan angive enten hacker, hentet det rigtige brugernavn og forsøger at råstyrke eller et automatisk værktøj, der bruger faste adgangskode, der er udløbet.

Denne dashboard aktiverer IT til hurtigt at identificere potentielle trusler, der er relateret til identitet og adgang til virksomhedens ressourcer. Det er bestemt vigtigt til at identificere også potentielle tendenser, for eksempel i feltet logon Over tid, kan du se over tidsperiode hvor mange gange en mislykket logonforsøg blev udført. I dette tilfælde modtaget computeren **filserversystem** 35 logonforsøg. Du kan udforske flere oplysninger om denne computer ved at klikke på den. 

![Få mere at vide](./media/oms-security-monitoring-resources/oms-security-monitoring-resources-fig7-new.png)

Den rapport, der er oprettet for denne computer viser værdifulde oplysninger om dette mønster. Bemærket, at kolonnen **konto** , får du den brugerkonto, der blev brugt til at forsøge at få adgang til systemet, kolonnen **TIMEGENERATED** giver dig et tidsinterval, hvor afkrydsningsfeltet forsøg blev foretaget og kolonnen **LOGONTYPENAME** giver dig det sted, hvor denne forsøg blev foretaget. Hvis disse forsøg er blevet udført lokalt i systemet af et program, vises **statuskolonne** ville processens navn. Du har allerede procesnavn, der er tilgængelige i scenarier, hvor logonforsøget kommer fra et program, og du kan nu udføre yderligere undersøgelse i target systemet.

## <a name="see-also"></a>Se også

I dette dokument, du har lært, hvordan du bruger OMS sikkerhed og overvågning løsning til at overvåge dine ressourcer. Hvis du vil vide mere om OMS sikkerhed skal du se følgende artikler:

- [Oversigt over operationer Management pakke (OMS)](operations-management-suite-overview.md)
- [Introduktion til handlinger Management pakke sikkerhed og Overvåg løsning](oms-security-getting-started.md)
- [Overvåge og reagere på sikkerhedsadvarsler i handlinger Management pakke sikkerhed og Overvåg løsning](oms-security-responding-alerts.md)