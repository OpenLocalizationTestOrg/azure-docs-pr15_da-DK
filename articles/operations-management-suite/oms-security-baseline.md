<properties
   pageTitle="Handlinger Management pakke sikkerhed og Overvåg løsning oprindelige | Microsoft Azure"
   description="Dette dokument forklares, hvordan du bruger OMS sikkerhed og overvågning løsning til at udføre en oprindelig plan vurdering af alle overvåget computere til overholdelse af regler og sikkerhed formål."
   services="operations-management-suite"
   documentationCenter="na"
   authors="YuriDio"
   manager="swadhwa"
   editor=""/>

<tags
   ms.service="operations-management-suite"
   ms.devlang="na"
   ms.topic="hero-article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="09/08/2016"
   ms.author="yurid"/>

# <a name="baseline-assessment-in-operations-management-suite-security-and-audit-solution"></a>Oprindelig plan vurdering i handlinger Management pakke sikkerhed og Overvåg løsning

Dette dokument hjælper dig med at bruge [Handlinger Management pakke (OMS) sikkerhed og Overvåg løsning](operations-management-suite-overview.md) oprindelige vurdering funktioner til at åbne sikker tilstand for ressourcerne overvåget.

## <a name="what-is-baseline-assessment"></a>Hvad er oprindelige vurdering?

Microsoft, sammen med branche og government organisationer over hele verden, du definerer en Windows-konfiguration, som repræsenterer meget sikre server-installationer. Denne konfiguration er et sæt af registreringsdatabasenøgler, politik overvågningsindstillinger og indstillinger for sikkerhedspolitik sammen med Microsofts anbefalede værdier for disse indstillinger. Denne regelsæt kaldes sikkerhed oprindelig plan. Muligheden for OMS sikkerhed og Overvåg oprindelige vurdering kan problemfrit scanne alle computere for overholdelse af angivne standarder. 

Der findes tre typer regler:

- **Registreringsdatabasen regler**: Kontrollér, at registreringsdatabasenøgler er indstillet korrekt.
- **Overvåge regler**: reglerne for din overvågningslog politik.
- **Sikkerhedsregler**: reglerne for brugerens tilladelser på computeren.

> [AZURE.NOTE] Læs [Brug OMS sikkerhed for at vurdere den oprindelige plan konfiguration af sikkerhed](https://blogs.technet.microsoft.com/msoms/2016/08/12/use-oms-security-to-assess-the-security-configuration-baseline/) for en kort oversigt over denne funktion.

## <a name="security-baseline-assessment"></a>Sikkerhed oprindelige vurdering

Du kan gennemse dine aktuelle sikkerhed oprindelige vurdering for alle de computere, der overvåges af OMS sikkerhed og Overvåg ved hjælp af dashboardet.  Udfør følgende trin for at få adgang til dashboardet sikkerhed oprindelige vurdering:

1. Klik på **sikkerhed og Overvåg** felt i **Microsoft Operations Management Suite** primære dashboard.
2. Klik på **Oprindeligt vurdering** under **Sikkerhed domæner**i dashboardet **sikkerhed og overvågning** . Dashboardet **Sikkerhed oprindelige vurdering** vises som vist på følgende billede:
    
    ![OMS sikkerhed og Overvåg oprindelige vurdering](./media/oms-security-baseline/oms-security-baseline-fig1.png)

Dette dashboard er opdelt i tre væsentlige områder:

- **Computere sammenlignet med oprindelige**: Dette afsnit giver en oversigt over antallet af computere, der ikke blev åbnet og procentdelen af computere, som overføres vurdering. Det giver også de øverste 10 computere og procentdel resultatet for vurdering.
- **Påkrævet regler Status**: denne sektion med formålet at få kendskab mislykkedes reglerne efter klassifikation og mislykkedes regler efter type. Ved at se til det første diagram kan du hurtigt identificere Hvis de fleste mislykkedes reglerne er vigtige, eller ej. Det giver også en liste over de øverste 10 mislykkede regler og deres alvor. Anden grafen viser typen regel, der opstod en fejl under vurdering. 
- **Computere, der mangler oprindelige vurdering**: Dette afsnit liste over de computere, der ikke blev fundet på grund af inkompatibilitet mellem operativsystem eller mislykkede. 

### <a name="accessing-computers-compared-to-baseline"></a>Få adgang til computere, der er sammenlignet med Oprindelig plan

Ideelt set er alle computere, der kompatibel med sikkerhed oprindelige vurdering. Men det er meningen, at i nogle omstændigheder dette ikke sker. Det er vigtigt at medtage gennemgå de computere, der ikke kunne overføre alle sikkerhed vurdering test som en del af processen sikkerhed administration. En hurtig måde at visualisere, der er ved at vælge indstillingen **computere adgang til** findes i afsnittet **computere sammenlignet med Oprindelig plan** . Du burde se log søgeresultatet vises en liste over computere, som viser i følgende skærmbillede:

![Computer, der er åbnet resultater](./media/oms-security-baseline/oms-security-baseline-fig2.png)

Søgeresultatet vises i et tabelformat, hvor den første kolonne har navnet på den computer, og den anden farve har antallet af regler, der mislykkedes. Klik på antallet af mislykkede regler ud over navnet på den computer for at hente oplysninger om typen regel, der mislykkedes. Du burde se et resultat, der ligner den, der vises på følgende billede:

![Computer, der er åbnet resultater detaljer](./media/oms-security-baseline/oms-security-baseline-fig3.png)

I denne søgeresultatet har du summen af adgang til regler, antallet kritisk regler, der mislykkedes, advarsel-regler og regler for oplysninger, der er mislykkedes.

### <a name="accessing-required-rules-status"></a>Få adgang til nødvendige regler status

Efter at få oplysninger om det procentvise antal computere, som overføres vurdering, kan du kan få flere oplysninger om, hvilke regler mislykkes ifølge oplysningerne. Denne visualisering hjælper dig med at prioritere, hvilke computere skal løses først for at sikre, at de er kompatibel i den næste vurdering. Hold markøren over den vigtige del af diagrammet, der er placeret i feltet **mislykkedes regler efter klassifikation** under **påkrævet regler status** og klikke på den. Du burde se et resultat, der ligner følgende skærmbillede:

![Kunne ikke udføre regler alvorlighed detaljer](./media/oms-security-baseline/oms-security-baseline-fig4.png) 

I dette log resultat se du typer oprindelige regel, der mislykkedes, beskrivelsen af reglen, og det almindelige konfiguration optælling (CCE)-ID for denne sikkerhedsregel for. Følgende attributter skal være nok til at udføre en korrigerende handling, som du kan løse problemet i target computeren.

> [AZURE.NOTE] Få adgang til [Nationale sikkerhedsrisiko Database](https://nvd.nist.gov/cce/index.cfm)kan finde flere oplysninger om CCE.

### <a name="accessing-computers-missing-baseline-assessment"></a>Få adgang til computere, der mangler oprindelige vurdering

OMS understøtter domæne medlem oprindelige profil på Windows Server 2008 R2 op til Windows Server 2012 R2. Windows Server 2016 oprindelig plan er endnu ikke færdig og tilføjes, så snart den er blevet publiceret. Alle andre operativsystemer scannet via OMS sikkerhed og Overvåg oprindelige vurdering vises under sektionen **computere, der mangler oprindelige vurdering** .

## <a name="see-also"></a>Se også

I dette dokument, du har lært om OMS sikkerhed og Overvåg oprindelige vurdering. Hvis du vil vide mere om OMS sikkerhed skal du se følgende artikler:

- [Oversigt over operationer Management pakke (OMS)](operations-management-suite-overview.md)
- [Overvåge og reagere på sikkerhedsadvarsler i handlinger Management pakke sikkerhed og Overvåg løsning](oms-security-responding-alerts.md)
- [Overvåge ressourcer i handlinger Management pakke sikkerhed og Overvåg løsning](oms-security-monitoring-resources.md)

