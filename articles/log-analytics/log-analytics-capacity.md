<properties
    pageTitle="Kapacitet Management-løsning i Log Analytics | Microsoft Azure"
    description="Du kan bruge løsningen kapacitetsplanlægning i Log Analytics kan hjælpe dig med at forstå kapaciteten af dine Hyper-V-servere, der administreres af System Center Virtual Machine Manager"
    services="log-analytics"
    documentationCenter=""
    authors="bandersmsft"
    manager="jwhit"
    editor=""/>

<tags
    ms.service="log-analytics"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/10/2016"
    ms.author="banders"/>

# <a name="capacity-management-solution-in-log-analytics"></a>Kapacitet Management-løsning i Log Analytics


Du kan bruge løsningen kapacitetsplanlægning i Log Analytics kan hjælpe dig med at forstå kapaciteten af dine Hyper-V-servere, der administreres af System Center Virtual Machine Manager. Denne løsning kræver både System Center Operations Manager og System Center Virtual Machine Manager. Kapacitet planlægning er ikke tilgængelig, hvis du kun bruger direkte tilsluttet supportmedarbejdere. Løsningen for at opdatere Operations Manager-agent er installeret. Løsningen læser tællere i ydeevne på overvåget serveren og sender anvendelsesdata til tjenesten OMS i skyen for behandling. Logik anvendes på brugerdata, og skytjenesten poster dataene. Brugsmønstre identificeres over tid, og kapacitet projiceres, baseret på aktuelle forbrug.

For eksempel kan en projicering identificere når flere processorer eller mere hukommelse, der er behov for en individuel server. I dette eksempel kan prognosen angiver, at inden for 30 dage serveren skal ekstra hukommelse. Dette kan hjælpe dig med at planlægge til en opgradering af hukommelsen under serverens næste vedligeholdelsesvindue, der kan forekomme én gang hver to uger.

>[AZURE.NOTE] Kapacitet Management-løsning er ikke tilgængelig til at blive føjet til arbejdsområder. Kunder, der har den installeret kapacitet management-løsning kan fortsætte med at bruge løsningen.  

Kapaciteten planlægning løsning er ved at blive opdateret for at løse følgende kunden rapporteret udfordringer:

- Krav til brug af Virtual Machine Manager og Operations Manager
- Manglende evne til at tilpasse/filter baseret på grupper
- Hver time datasammenlægning hyppigere ikke nok
- Ingen VM niveau indsigt
- Pålidelige data

Fordelene ved den nye kapacitet løsning:

- Understøtter findelt dataindsamling med øget pålidelighed og nøjagtighed
- Understøttelse af Hyper-V uden VMM
- Visualisering af målepunkter i PowerBI
- Viden om VM niveau anvendelsen


## <a name="installing-and-configuring-the-solution"></a>Installation og konfiguration af løsningen
Brug følgende oplysninger til at installere og konfigurere løsningen.

- Operations Manager er påkrævet for kapacitet Management-løsning.
- Virtual Machine Manager er påkrævet for kapacitet Management-løsning.
- Operations Manager forbindelse med Virtual Machine Manager (VMM) er påkrævet. Yderligere oplysninger om at oprette forbindelse se systemer, [hvordan du opretter forbindelse VMM med Operations Manager](http://technet.microsoft.com/library/hh882396.aspx).
- Operations Manager skal være tilsluttet Log analyser.
- Føje kapacitet Management-løsning til arbejdsområdet OMS ved hjælp af processen beskrevet i [tilføje Log Analytics løsninger fra løsningsgalleriet](log-analytics-add-solutions.md).  Der er ingen yderligere konfiguration, der er påkrævet.


## <a name="capacity-management-data-collection-details"></a>Kapacitet samling detaljer om administration af data

Kapacitet Management indsamler ydelsesdata, metadata og tilstand data ved hjælp af supportmedarbejdere, som du har aktiveret.

Følgende tabel viser data samling metoder og andre oplysninger om, hvordan data indsamles for kapacitet administration.

| platform | Direkte Agent | SCOM agent | Azure-lager | SCOM påkrævet? | SCOM agent data, der sendes via administration af gruppe | Frekvens af websteder |
|---|---|---|---|---|---|---|
|Windows|![Nej](./media/log-analytics-capacity/oms-bullet-red.png)|![Ja](./media/log-analytics-capacity/oms-bullet-green.png)|![Nej](./media/log-analytics-capacity/oms-bullet-red.png)|            ![Ja](./media/log-analytics-capacity/oms-bullet-green.png)|![Ja](./media/log-analytics-capacity/oms-bullet-green.png)| hver time|

I følgende tabel vises eksempler på datatyper, der indsamles via kapacitet administration:

|**Datatype**|**Felter**|
|---|---|
|Metadata|BaseManagedEntityId, ObjectStatus, OrganizationalUnit, ActiveDirectoryObjectSid, PhysicalProcessors, NetworkName, IP-adresse, ForestDNSName, NetbiosComputerName, VirtualMachineName, LastInventoryDate, HostServerNameIsVirtualMachine, IP-adresse, NetbiosDomainName, LogicalProcessors, DNSName, vist navn, DomainDnsName, ActiveDirectorySite, PrincipalName, OffsetInMinuteFromGreenwichTime|
|Ydeevne|Objektnavn, CounterName, PerfmonInstanceName, PerformanceDataId, PerformanceSourceInternalID, SampleValue, TimeSampled, TimeAdded|
|Stat|StateChangeEventId, StateId, NewHealthState, OldHealthState, kontekst, TimeGenerated, TimeAdded, StateId2, BaseManagedEntityId, MonitorId, HealthState, LastModified, LastGreenAlertGenerated, DatabaseTimeModified|

## <a name="capacity-management-page"></a>Kapacitet siden til administration


 Når kapacitetsplanlægning løsningen er installeret, kan du se kapaciteten af serverne overvåget ved hjælp af feltet **Kapacitetsplanlægning** på siden **Oversigt** i OMS.

![Billede af kapacitetsplanlægning felt](./media/log-analytics-capacity/oms-capacity01.png)

Feltet åbnes dashboardet **Kapacitet administration** , hvor du kan få vist en oversigt over din serverkapacitet. Siden viser de følgende fliser, kan du klikke på:

- *Virtuelt antal*: Viser antallet af dage tilbage til kapaciteten af virtuelle maskiner
- *Beregne*: Viser processorer og tilgængelig hukommelse
- *Lager*: Viser den anvendte diskplads og beregne gennemsnittet af disken ventetid
- *Søg*: Stifinder data, du kan bruge til at søge efter data i OMS-systemet

![Billede af kapacitet administrationsdashboard](./media/log-analytics-capacity/oms-capacity02.png)


### <a name="to-view-a-capacity-page"></a>Sådan får du vist en kapacitet side

- Klik på **Administration af kapacitet**på siden **Oversigt** , og klik derefter på **beregne** eller **lagerplads**.

## <a name="compute-page"></a>Beregne side

Du kan bruge dashboardet **beregne** i Microsoft Azure OMS til at få vist oplysninger om anvendelsen, forventet dage af kapacitet, og effektivitet, der er relateret til infrastrukturen kapacitet. Du kan bruge området **anvendelsen** til at få vist CPU-forbrug på core og hukommelse i din virtuelt hosts. Du kan bruge værktøjet projicering til at anslå, hvor meget kapacitet forventes skal være tilgængelige for et bestemt datoområde. Du kan bruge området **effektivitet** til at se, hvordan effektiv din virtuelt hosts er. Du kan få vist oplysninger om sammenkædede elementer ved at klikke på dem.

Du kan oprette en Excel-projektmappe for følgende kategorier:

- Øverste hosts med højeste core anvendelsen
- Øverste hosts med højeste hukommelsesbrug
- Øverste hosts med ineffektiv virtuelle maskiner
- Øverste værter ved anvendelsen
- Nederste værter ved anvendelsen

![Billede af kapacitet Management beregne](./media/log-analytics-capacity/oms-capacity03.png)


De følgende områder vises på dashboardet til at **beregne** :

**Anvendelsen**: Vis CPU core og hukommelse anvendelsen i din virtuelt hosts.

- *Bruges kerner*: Sum for alle værter (% af CPU'EN anvendes multipliceret med antallet af fysiske kerner på host).
- *Gratis kerner*: samlet fysiske kerner minus anvendte kerner.
- *Procentdel kerner tilgængelig*: ledig fysiske kerner divideret med antal fysiske kerner.
- *Virtuel kerner per VM*: samlet virtuelle kerner i systemet divideret med det samlede antal virtuelle maskiner i systemet.
- *Virtuel kerner til fysiske kerner-bredde-forhold*: forholdet mellem samlede fysiske kerner til fysiske kerner, der bruges af virtuelle maskiner i systemet.
- *Antallet af virtuelle kerner tilgængelig*: virtuelle core fysiske kerner forholdet mellem ganget med de tilgængelige fysiske kerner.
- *Bruges hukommelse*: summen af hukommelse, der bruges af alle værter.
- *Gratis hukommelse*: Samlet fysisk hukommelse minus bruges hukommelse.
- *Procentdel hukommelse tilgængelig*: ledig fysisk hukommelse divideret med samlede fysiske hukommelse.
- *Virtuel hukommelse per VM*: samlet virtuel hukommelse i systemet divideret med det samlede antal virtuelle maskiner i systemet.
- *Virtuel hukommelse til fysisk hukommelse-bredde-forhold*: samlet virtuel hukommelse i systemet divideret med den samlede fysiske hukommelse i systemet.
- *Virtuel hukommelse tilgængelig*: virtuel hukommelse til fysisk hukommelse forholdet multipliceret med den tilgængelige fysiske hukommelse.

**Projicering værktøj**

Ved hjælp af værktøjet projicering, kan du få vist historiske tendenser for dit ressourceforbrug mere effektivt. Dette omfatter brugen tendenser for virtuelle maskiner, hukommelse, core og lagerplads. Muligheden for projicering bruger en projicering algoritme kan hjælpe dig besked, når du kører af hver af ressourcerne. Dette hjælper dig med at beregne stort kapacitet, planlægning, så du kan, når du vil købe flere kapacitet (som hukommelse, kerner eller lagerplads).

**Effektivitet**

- *Inaktiv VM*: med mindre end 10% af CPU og 10% hukommelse til det angivne tidsrum.
- *Overutilized VM*: Brug af mere end 90% af CPU og 90% hukommelse for den angivne tidsperiode.
- *Inaktiv Host*: Brug af mindre end 10% af CPU og 10% hukommelse for den angivne tidsperiode.
- *Overutilized Host*: Brug af mere end 90% af CPU og 90% hukommelse for den angivne tidsperiode.

### <a name="to-work-with-items-on-the-compute-page"></a>Arbejde med elementer på siden Beregn

1. Få vist yderligere kapacitetsoplysninger om CPU kerner og hukommelse i brug i området **anvendelsen** på dashboardet **beregne** .
2. Klik på et element for at åbne den i **søgesiden** og få vist detaljerede oplysninger om den.
3. Flyt skyderen dato til at vise en projicering kapacitet, der skal bruges på den dato, du vælger i værktøjet **projicering** .
4. Få vist kapacitet effektivitet oplysninger om virtuelle maskiner og virtuelt hosts i området **effektivitet** .

## <a name="direct-attached-storage-page"></a>Direkte vedhæftet lager side

Du kan bruge dashboardet **Direkte vedhæftet lager** i OMS til at få vist kapacitetsoplysninger om lagerplads anvendelsen, disken ydeevne og planlagte dage af diskplads. Du kan bruge området **anvendelsen** til at få vist diskplads i din virtuelt hosts. Du kan bruge området **Diskydeevnen** til at få vist disk overførselshastighed og forsinkelse i din virtuelt hosts. Du kan også bruge værktøjet projicering til at anslå, hvor meget kapacitet forventes skal være tilgængelige for et bestemt datoområde. Du kan få vist oplysninger om sammenkædede elementer ved at klikke på dem.

Du kan oprette en Excel-projektmappe fra oplysningerne kapacitet for følgende kategorier:

- Øverste diskplads af vært
- Øverste gennemsnitlig forsinkelse af vært

De følgende områder vises på siden **lager** :

- *Anvendelsen*: få vist diskplads i din virtuelt hosts.
- *Samlet diskplads*: Sum (logisk diskplads) for alle værter
- *Bruges diskplads*: Sum (brugt logiske diskplads) for alle værter
- *Tilgængelige diskplads*: Samlet diskplads minus anvendte diskplads.
- *Procentdel Disk bruges*: bruges diskplads divideret med samlede diskplads.
- *Procentdel disken tilgængelig*: ledig harddiskplads divideret med samlede diskplads.

![Billede af kapacitet Management direkte vedhæftet lager](./media/log-analytics-capacity/oms-capacity04.png)

**Disken ydeevne**

Ved hjælp af OMS, kan du få vist tendensen historiske brugen af din diskplads. Muligheden for projicering bruger en algoritme til fremtidig brug af project. Til brug af diskplads især muligheden for projicering gør muligt at project, når du muligvis løbe tør for plads på harddisken. Dette hjælper dig med at planlægge stort lagerplads og ved, at når du har brug at købe mere lagerplads.

**Projicering værktøj**

Ved hjælp af værktøjet projicering, kan du få vist historiske tendenser for udnyttelse af pladsen din disk. Muligheden for projicering kan du også project, når du kører ikke mere ledig diskplads. Dette hjælper dig med at planlægge stort kapacitet og ved, at når du har brug at købe mere lagerplads kapacitet.

### <a name="to-work-with-items-on-the-direct-attached-storage-page"></a>Arbejde med elementer på siden direkte vedhæftet lager

1. Du kan se oplysningerne disk anvendelsen på dashboardet til **Direkte vedhæftet lager** i området **anvendelsen** .
2. Klik på et sammenkædet element for at åbne den i **søgeside** og få vist detaljerede oplysninger om den.
3. Du kan få vist disk overførselshastighed og ventetid oplysninger i området **Disk ydeevne** .
4. Flyt skyderen dato til at vise en projicering kapacitet, der skal bruges på den dato, du vælger i **projicering værktøj**.


## <a name="next-steps"></a>Næste trin

- Brug [Log søgninger i Log Analytics](log-analytics-log-searches.md) til at få vist detaljerede kapacitet management data.
