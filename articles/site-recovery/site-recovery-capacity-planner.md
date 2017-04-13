<properties
    pageTitle="Planlæg kapacitet til at beskytte virtuelle maskiner og fysiske servere i Azure gendannelse af websteder til | Microsoft Azure"
    description="Azure gendannelse af websteder koordinater gentagelse, failover og gendannelse af virtuelle maskiner og fysiske servere, der er placeret på i det lokale miljø til Azure eller til en sekundær lokalt websted." 
    services="site-recovery" 
    documentationCenter="" 
    authors="rayne-wiselman" 
    manager="jwhit" 
    editor=""/>

<tags 
    ms.service="site-recovery" 
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="storage-backup-recovery" 
    ms.date="07/12/2016" 
    ms.author="raynew"/>

# <a name="plan-capacity-for-protecting-virtual-machines-and-physical-servers-in-azure-site-recovery"></a>Planlæg kapacitet til beskyttelse af virtuelle maskiner og fysiske servere i Azure gendannelse af websteder

Værktøjet Azure websted gendannelse kapacitet teamplanlægning hjælper dig med at finde ud af dine krav til kapacitet til at beskytte Hyper-V FOS, VMware FOS og Windows/Linux fysiske servere med Azure gendannelse af websteder til.


## <a name="overview"></a>Oversigt

Brug websted gendannelse kapacitet teamplanlægning til at analysere din kilde miljø og arbejdsmængder og finde ud af båndbredde behov, serverressourcer, skal du i din kildeplacering og ressourcer (virtuelle maskiner og lager osv), skal du i din destinationsplacering. 

Du kan køre værktøjet på flere måder:

- **Hurtig planlægning**: Kør værktøjet i denne tilstand for at få Netværks- og prognoser baseret på en gennemsnitlige antal FOS, diske, lager og ændre rente.
- **Detaljeret planlægning**: Kør værktøjet i denne tilstand, og angiv oplysninger om hver arbejdsbelastningen på VM niveau. Analysere VM kompatibilitet, og få Netværks- og prognoser.

## <a name="before-you-start"></a>Før du starter

Før du køre værktøjet:

1. Indsamle oplysninger om dit miljø, herunder FOS, diske per VM, lagerplads per disk.
2. Identificere dine daglige ændring (transportspand) DISKONTO replikerede data. Sådan gør du:

    - Hvis du replikering Hyper-V FOS derefter hente [Hyper-V kapacitet planlægning værktøjet](https://www.microsoft.com/download/details.aspx?id=39057) for at få Skift rente. Du kan [Få mere at vide](site-recovery-capacity-planning-for-hyper-v-replication.md) om dette værktøj. Vi anbefaler, at du kører dette værktøj en uge til at registrere gennemsnit.
    - Hvis du replikering VMware virtuelle maskiner, kan du bruge [vSphere kapacitet, planlægning maskinen](https://labs.vmware.com/flings/vsphere-replication-capacity-planning-appliance) til at finde ud af transportspand rente.
    - Hvis du replikering fysiske servere skal du til at anslå manuelt.

## <a name="run-the-quick-planner"></a>Køre hurtig planlæggeren
1.  Hente og åbne værktøjet til [Azure websted gendannelse kapacitet teamplanlægning](http://aka.ms/asr-capacity-planner-excel) . Du skal køre makroer derfor vælge at aktivere redigering og aktivere indhold, når du bliver bedt om. 
2.  Vælg **Hurtig Planner** på listen i **Vælg typen af teamplanlægning** .

    ![Kom godt i gang](./media/site-recovery-capacity-planner/getting-started.png)

3.  Angiv de nødvendige oplysninger i regnearket **Kapacitet teamplanlægning** . Du skal udfylde alle felterne med cirkel med rødt i skærmbilledet nedenfor.

    - Vælg **Hyper-V til Azure** eller **VMware/fysisk til Azure**i **Vælg scenariet** .
    - I **gennemsnitlige daglige data ændre rente (%)** placere oplysninger, du har indsamlet ved hjælp af [Hyper-V kapacitet planlægning værktøj](site-recovery-capacity-planning-for-hyper-v-replication.md) eller [vSphere kapacitet, planlægning maskinen](https://labs.vmware.com/flings/vsphere-replication-capacity-planning-appliance).  
    - **Komprimering** gælder kun for komprimering tilbydes ved replikering VMware FOS eller fysiske servere til Azure. Vi anslå 30% eller mere, men du kan ændre indstillingen efter behov. Du kan bruge en tredjeparts maskinen som Riverbed til replikering Hyper-V FOS til Azure komprimering. 
    -  Angiv, hvor længe replikaer skal bevares i **Opbevaring input** . Hvis du replikering VMware eller fysiske servere inputværdi i dage. Hvis du replikering Hyper-V angive tidspunktet i timer.
    -  **Antallet af timer i hvilke første replikering batchen af virtuelle maskiner skal udføre** og **antallet af virtuelle maskiner per første replikering batchen** du har indtastet indstillinger, der bruges til at beregne første replikering krav.  Når gendannelse af websteder er installeret skal hele indledende datasættet overføres. 

    ![Input](./media/site-recovery-capacity-planner/inputs.png)

2.  Når du har lagt i værdier for kilde-miljø, indeholder viste output:

    - **Båndbredde, der kræves til delta gentagelse** (MB/sec). Netværksbåndbredde til delta replikering beregnes på den gennemsnitlige daglige data Skift rente.
    - **Båndbredde, der kræves til indledende gentagelse** (MB/sec). Netværksbåndbredde til første replikering beregnes på de første replikering værdier, du placerer i. 
    - **Lagerplads påkrævet (i GB)** er den samlede Azure lagerplads, der er påkrævet.
    - **Samlet IOP'ER på standard lagerplads konti** er beregnes ud fra 8 K IOP'ER størrelsen på de samlede standard lager konti.  Ændre rente for hurtig planlæggeren antallet beregnes ud fra alle kilde FOS diske og daglige data. Ændre rente på disse FOS til detaljerede planlæggeren antallet beregnes ud fra det samlede antal VM'er, der er knyttet til standard Azure FOS og data. 
    - **Antallet af standard lagerplads konti** indeholder det samlede antal standard lagerplads konti, der er behov for at beskytte FOS. Bemærk, at en standard-lager-konto kan indeholde op til 20.000 IOP'ER på tværs af alle FOS i en standard-lager og maksimale 500 IOP'ER understøttes per disk. 
    - **Antallet af blob diske påkrævet** giver antallet af diske, der oprettes på Azure-lager.
    - **Antallet af premium lagerplads konti påkrævet** indeholder det samlede antal premium lagerplads konti, der er behov for at beskytte FOS. Bemærk, at en kilde VM med høj IOP'ER (større end 20000) skal have en premium lagerplads konto. En premium lagerplads konto kan indeholde op til 80000 IOP'ER.
    - **Samlet IOP'ER på premium lagerplads** beregnes på basis 256 K IOP'ER størrelsen på de samlede premium lagerplads konti.  Ændre hastigheden for hurtig planlæggeren antallet beregnes ud fra alle kilde FOS diske og daglig data. Ændre rente på disse FOS til detaljerede planlæggeren antallet beregnes ud fra det samlede antal VM'er, der er knyttet til premium Azure VM (DS og GS serie) og dataene. 
    - **Antallet af konfiguration servere påkrævet** viser, hvor mange konfiguration servere, der kræves til installationen (1)
    - **Antallet af yderligere processer servere påkrævet** viser om yderligere processer servere er påkrævede ud over proces-server, der er konfigureret på server configuration som standard.
    - **100% ekstra lagerplads på kilden** viser om ekstra lagerplads er påkrævet i billedets kildeplacering.
            
    ![Output](./media/site-recovery-capacity-planner/output.png)
 
## <a name="run-the-detailed-planner"></a>Køre detaljerede planlæggeren


1.  Hente og åbne værktøjet til [Azure websted gendannelse kapacitet teamplanlægning](http://aka.ms/asr-capacity-planner-excel) . Du skal køre makroer derfor vælge at aktivere redigering og aktivere indhold, når du bliver bedt om. 
2.  Vælg **Detaljerede teamplanlægning** fra listen i **Vælg typen af teamplanlægning** .

    ![Kom godt i gang](./media/site-recovery-capacity-planner/getting-started-2.png)

3.  Angiv de nødvendige oplysninger i **Arbejdsbelastningen kvalifikation til** regnearket. Du skal udfylde de markerede felter.

    - Angiv det samlede antal kerner på en kildeserveren i **processorer** .
    - Angiv i **hukommelsesallokering i MB** RAM størrelsen på en kildeserveren. 
    - **Antallet af netværkskort** angive antallet netværkskort på en kildeserver. 
    -  Angiv den samlede størrelse af VM opbevaring i **samlede lager (i GB)** . For eksempel hvis kildeserveren har 3 diske med 500 GB, og klik derefter samlede lagerstørrelse er 1500 GB.
    -  Angiv det samlede antal diske af en kildeserveren i **antallet af diske vedhæftet** .
    -  Angiv den gennemsnitlige anvendelsen i **Disk kapacitet anvendelsen** .
    -  Angiv de daglige data ændres afkast for en kildeserveren i **dagligt ændre rente (%)** .
    -  Angiv Azure VM størrelse, som du vil tilknytte i **tilknytning Azure størrelse** . Klik på**Beregne IaaS FOS**, hvis du ikke vil gøre det manuelt. Bemærk, at hvis du indtaster en manuel indstilling og derefter klikke på beregne IaaS FOS din manuel indstilling kan overskrives, da processen Beregn automatisk identificerer den bedste match Azure VM størrelse.

    ![Arbejdsbelastningen kvalifikation](./media/site-recovery-capacity-planner/workload-qualification.png)

4.  Hvis du klikker på er **Beregne IaaS FOS** her, hvad gør den:

    - Validerer de obligatoriske input.
    - Beregner IOP'ER og foreslår den bedste Azure VM aize match for hver VM'er, der er berettiget til Azure-replikering. Hvis en passende størrelse af Azure VM ikke kan registreres fejlen er udstedt. For eksempel hvis antallet af diske vedhæftede i 65 en fejlmeddelelse, er problemer med siden den højeste størrelse er Azure VM 64.
    - Foreslår en lagerplads-konto, som kan bruges til en Azure VM.
    - Beregner det samlede antal standard lagerplads konti og premium lagerplads konti, der kræves til arbejdsbelastningen. Rul ned til højre for at få vist Azure lagerplads type og kontoen lagerplads, der kan bruges til en kildeserveren
    - Er fuldført, og sorterer resten af den tabel, der er baseret på nødvendige lagerplads type (standard eller førsteklasses) tildelt til en VM og antallet af diske vedhæftet. Viser Ja til alle VM'er, der opfylder kravene til sikkerhedskopiering op til Azure, kolonne A (er VM kvalificeret?). Hvis en VM ikke kan være sikkerhedskopier op til vises Azure en fejl.

Kolonner å til AE er output og angive oplysninger om hver VM.

![Arbejdsbelastningen kvalifikation](./media/site-recovery-capacity-planner/workload-qualification-2.png)


### <a name="example"></a>Eksempel
Værktøjet beregnes som et eksempel på seks FOS med de værdier, der vises i tabellen, og tildeler bedste Azure VM matcher og Azure lagerplads kravene.

![Arbejdsbelastningen kvalifikation](./media/site-recovery-capacity-planner/workload-qualification-3.png)

- I output til eksemplet være opmærksom på følgende:
    
    - Den første kolonne er en validering for FOS, diske og transportspand.
    - To standard lagerplads konti og én premium lagerplads konto der skal bruges til fem FOS. 
    -  VM3 ikke opfylder betingelserne for beskyttelse, fordi en eller flere er mere end 1 TB.
    -  VM1 og VM2 kan bruge den første standard lagerplads-konto
    -  VM4 kan bruge kontoen anden standard lagerplads.
    -  VM5 og VM6 har du brug for en premium lagerplads konto og kan både bruge en enkelt konto.

    >[AZURE.NOTE]  IOP'ER på standard og premium lagerplads beregnes på niveauet for VM og ikke på disk niveau. En standard virtuel maskine kan håndtere op til 500 IOP'ER per disk. Hvis IOP'ER for en disk er større end 500 skal du premium lagerplads. Men hvis IOP'ER for en disk er mere end 500 men IOP'ER om en de samlede VM disk er inden for support standard Azure VM (VM størrelse, antal disk antallet af kort, CPU, hukommelse) derefter planlæggeren vælger en standard VM og ikke DS eller GS serie. Du skal opdatere manuelt cellen tilknytning Azure størrelse med relevante DS eller GS serie VM.

5. Når alle oplysninger er på plads, skal du klikke på **Send data til værktøjet planner** for at åbne **Kapacitet Planner** arbejdsbelastninger, som er fremhævet for at vise, om de er berettiget til beskyttelse eller ej.


### <a name="submit-data-in-the-capacity-planner"></a>Sende data i kapacitet planlæggeren

1.  Når du åbner regnearket **Kapacitet teamplanlægning** udfyldes baseret på de indstillinger, du har angivet. Ordet 'Arbejdsbelastningen' vises i cellen **Infra input kilde** til at vise, input **Arbejdsbelastningen kvalifikation til** regnearket. 
2.  Hvis du vil foretage ændringer, skal du redigere **Arbejdsbelastningen kvalifikation til** regnearket, og klik på Send data til værktøjet teamplanlægning igen.  

    ![Kapacitet teamplanlægning](./media/site-recovery-capacity-planner/capacity-planner.png)


