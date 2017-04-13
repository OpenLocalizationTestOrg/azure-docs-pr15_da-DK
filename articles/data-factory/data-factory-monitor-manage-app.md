<properties 
    pageTitle="Overvåge og administrere Azure Data Factory rørledninger" 
    description="Lær, hvordan du bruger overvågning og administration af App til at overvåge og administrere Azure data fabrikker og rørledninger." 
    services="data-factory" 
    documentationCenter="" 
    authors="spelluru" 
    manager="jhubbard" 
    editor="monicar"/>

<tags 
    ms.service="data-factory" 
    ms.workload="data-services" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="09/06/2016" 
    ms.author="spelluru"/>

# <a name="monitor-and-manage-azure-data-factory-pipelines-using-new-monitoring-and-management-app"></a>Overvåge og administrere Azure Data Factory rørledninger ved hjælp af nye overvågning og administration af App
> [AZURE.SELECTOR]
- [Brug af Azure Portal/Azure PowerShell](data-factory-monitor-manage-pipelines.md)
- [Brug af overvågning og administration af App](data-factory-monitor-manage-app.md)

I denne artikel beskrives, hvordan du kan overvåge, administrere og teste din rørledninger og oprette beskeder til at få besked på fejl ved hjælp af **overvågnings- og Management App**. Du kan også se den følgende video for at få mere at vide om brug af overvågning og administration af App.
   

> [AZURE.VIDEO azure-data-factory-monitoring-and-managing-big-data-piplines]
      
## <a name="launching-the-monitoring-and-management-app-a"></a>Start overvågning og administration af App en
For at starte overvågning og administration af App, skal du klikke på **overvågnings og administrer** felt på bladet **DATA FACTORY** til dine data factory.

![Overvågning felt på startsiden for Data Factory](./media/data-factory-monitor-manage-app/MonitoringAppTile.png) 

Du bør se overvågning og administration af App startet i en separat fanevinduet.  

![Overvågning og administration af App](./media/data-factory-monitor-manage-app/AppLaunched.png)

> [AZURE.NOTE] Hvis du ser, at webbrowseren er fastlåst ved "Godkendelse af...", Deaktiver/Fjern markeringen af indstillingen **Bloker tredjepartscookies og webstedsdata** (eller) beholde den aktiveret og oprette en undtagelse for **login.microsoftonline.com** og prøv derefter at åbne programmet igen.


Hvis du ikke kan se aktivitet windows på listen i bunden, skal du klikke på knappen **Opdater** på værktøjslinjen for at opdatere listen. Desuden angive de rigtige værdier for filtrene **Starttidspunkt** og **Sluttidspunkt** .  


## <a name="understanding-the-monitoring-and-management-app"></a>Om overvågning og administration af App
Der er tre faner (**Ressource Explorer**, **Overvågning visninger**og **påmindelser**) til venstre, og den første fane (ressource Explorer) er markeret som standard. 

### <a name="resource-explorer"></a>Ressource Explorer
Du se følgende: 

- Ressource Explorer **trævisningen** i venstre rude.
- **Diagramvisning** øverst.
- **Aktivitet Windows** liste nederst i den midterste rude.
- **Egenskaber for**/**Aktivitet vinduet Explorer** fanerne i højre rude. 

I ressource Explorer skal se du alle ressourcer (rørledninger, datasæt, sammenkædede services) i data factory i en træstruktur. Når du vælger et objekt i ressource Explorer, vil du bemærke følgende: 

- tilknyttet Data Factory-enhed er fremhævet i diagramvisning.
- knyttet aktivitet windows (Klik [her](data-factory-scheduling-and-execution.md) for at lære om aktivitet windows) er fremhævet på listen aktivitet Windows nederst.  
- Egenskaber for det markerede objekt i vinduet Egenskaber i højre rude. 
- JSON definition af det markerede objekt, hvis det er relevant. For eksempel: en sammenkædet tjeneste eller et datasæt eller en rørledning. 

![Ressource Explorer](./media/data-factory-monitor-manage-app/ResourceExplorer.png)

Se [Planlægning af og udførelse af](data-factory-scheduling-and-execution.md) artiklen grundlæggende oplysninger om i aktivitetsvinduet. 

### <a name="diagram-view"></a>Diagramvisning
Diagramvisning af en fabrik data indeholder en enkelt rude for at overvåge og administrere data fabrik og dens aktiver. Når du vælger en Data Factory-enhed (datasæt/pipeline) i diagramvisning, vil du bemærke følgende:
 
- data factory objekt er markeret i trævisningen
- tilknyttede aktivitet windows er fremhævet på listen aktivitet i Windows.
- Egenskaber for det markerede objekt i vinduet Egenskaber

Når rørledningen er aktiveret (ikke i pausetilstand), vises den med en grøn linje. 

![Pipeline kører](./media/data-factory-monitor-manage-app/PipelineRunning.png)

Du oplever, at der er tre kommandoknapper til pipeline i diagramvisning. Du kan bruge den anden knap til at afbryde midlertidigt pipeline. Afbrydelsen ikke afslutte de aktiviteter, der kører i øjeblikket, og lade dem fortsætte til afslutning. Tredje knap afbrydes pipeline og ophører den eksisterende afholde aktiviteter. Knappen første genoptages pipeline. Når din pipeline midlertidigt, kan du se farveændringer for pipelinen flisen på følgende måde.

![Afbryd midlertidigt/Fortsæt i felt](./media/data-factory-monitor-manage-app/SuspendResumeOnTile.png)

Du kan markere flere elementer to eller flere rørledninger (med CTRL) og bruge kommandoknapper på værktøjslinjen til Afbryd/Fortsæt flere rørledninger ad gangen.

![Afbryd midlertidigt/Fortsæt på kommandolinjen](./media/data-factory-monitor-manage-app/SuspendResumeOnCommandBar.png)

Du kan se alle aktiviteterne i pipeline, ved at højreklikke på feltet pipeline, og klikke på **Åbn pipeline**.

![Menuen Åbn Pipeline](./media/data-factory-monitor-manage-app/OpenPipelineMenu.png)

I visningen åbnet pipeline skal se du alle aktiviteter i pipeline. I dette eksempel, der er kun én aktivitet: Kopiér aktivitet. For at gå tilbage til den forrige visning, skal du klikke på data factory navn i brødkrummemenuen øverst.

![Åbnet Pipeline](./media/data-factory-monitor-manage-app/OpenedPipeline.png)

Når du klikker på et output datasæt, eller når du flytter musen hen over datasæt output i visningen pipeline se aktivitet Windows pop op-menuen for dataset.

![Aktivitet Windows pop op-vindue](./media/data-factory-monitor-manage-app/ActivityWindowsPopup.png)

Du kan klikke på en i aktivitetsvinduet for at se oplysninger for den i vinduet **Egenskaber** i højre rude. 

![Aktivitet i vinduet Egenskaber](./media/data-factory-monitor-manage-app/ActivityWindowProperties.png)

I højre rude skal du skifte til **Aktivitet vinduet Explorer** tab for at få vist flere detaljer.

![Aktivitet i vinduet Stifinder](./media/data-factory-monitor-manage-app/ActivityWindowExplorer.png) 

Du kan også se **løst variabler** for hver aktivitet Kør forsøger i sektionen **forsøg** . 

![Løst variabler](./media/data-factory-monitor-manage-app/ResolvedVariables.PNG)

Skifte til fanen **Script** for at se JSON script definitionen for det markerede objekt.   

![Under fanen script](./media/data-factory-monitor-manage-app/ScriptTab.png)

Du kan se aktivitet windows tre steder:

- Aktivitet Windows pop op-vindue i diagramvisning (midterste rude).
- Aktivitet i vinduet Stifinder i højre rude.
- Aktivitet Windows liste i den nederste rude.

I aktivitet Windows pop op- og aktivitet vinduet Explorer, kan du rulle til forrige uge og næste uge ved hjælp af venstre og højre pil.

![Aktivitet vinduet Explorer venstre/højre pil](./media/data-factory-monitor-manage-app/ActivityWindowExplorerLeftRightArrows.png)

Du kan se knapper til Zoom Zoom ind, zoome ud for at tilpasse, Zoom 100% og låse layout nederst i diagramvisning. Knappen Lås layout forhindrer, at du kommer til at flytte tabeller og rørledninger i diagramvisningen og er som standard. Du kan deaktivere den og flytte objekter rundt i diagrammet. Når du slå dem fra, kan du bruge knappen sidste til automatisk for at anbringe tabeller og rørledninger. Du kan også zoome ind / Zoom ud ved hjælp af musehjul.

![Diagram Vis Zoom kommandoer](./media/data-factory-monitor-manage-app/DiagramViewZoomCommands.png)


### <a name="activity-windows-list"></a>Aktivitet Windows liste
Listen aktivitet windows nederst i den midterste rude viser alle aktivitet vinduer for datasættet, du har valgt i den ressource explorer eller diagramvisning. På listen er som standard i faldende rækkefølge, hvilket betyder, at du ser det seneste aktivitet vindue øverst. 

![Aktivitet Windows liste](./media/data-factory-monitor-manage-app/ActivityWindowsList.png)

Denne liste ikke opdatere automatisk, så brug opdateringsknappen på værktøjslinjen til at opdatere den manuelt.  


Vinduerne aktivitet kan være på en af følgende statusser:

<table>
<tr>
    <th align="left">Status</th><th align="left">Understatus</th><th align="left">Beskrivelse</th>
</tr>
<tr>
    <td rowspan="8">Venter</td><td>ScheduleTime</td><td>Tid, der kommer ikke til aktivitetsvinduet til at køre.</td>
</tr>
<tr>
<td>DatasetDependencies</td><td>Før afhængigheder er ikke klar.</td>
</tr>
<tr>
<td>ComputeResources</td><td>Beregn ressourcer er ikke tilgængelige.</td>
</tr>
<tr>
<td>ConcurrencyLimit</td> <td>Alle aktivitet forekomster er optaget kører windows andre aktivitet.</td>
</tr>
<tr>
<td>ActivityResume</td><td>Aktivitet er afbrudt midlertidigt og kan ikke køre vinduerne aktivitet, indtil den genoptages.</td>
</tr>
<tr>
<td>Prøv igen</td><td>Udførelse af aktivitet skal gentages.</td>
</tr>
<tr>
<td>Datavalidering</td><td>Datavalidering er ikke startet endnu.</td>
</tr>
<tr>
<td>ValidationRetry</td><td>Venter på validering forsøges igen.</td>
</tr>
<tr>
<tr
<td rowspan="2">InProgress</td><td>Validere</td><td>Validering i gang.</td>
</tr>
<td></td>
<td>Aktivitetsvinduet behandles.</td>
</tr>
<tr>
<td rowspan="4">Mislykkedes</td><td>Der opstod timeout for</td><td>Udførelse af tog længere tid end, der er tilladt i aktiviteten.</td>
</tr>
<tr>
<td>Annulleret</td><td>Annulleret af brugerhandling.</td>
</tr>
<tr>
<td>Datavalidering</td><td>Validering mislykkedes.</td>
</tr>
<tr>
<td></td><td>Kunne ikke generere og/eller validere aktivitetsvinduet.</td>
</tr>
<td>Klar</td><td></td><td>Aktivitetsvinduet er klar til forbrug.</td>
</tr>
<tr>
<td>Ignoreret</td><td></td><td>Aktivitetsvinduet behandles ikke.</td>
</tr>
<tr>
<td>Ingen</td><td></td><td>En aktivitet vindue, der bruges til at findes med en anden status, men er blevet nulstillet.</td>
</tr>
</table>


Når du klikker på et aktivitet vindue på listen, kan du se detaljer om det i **Aktivitet i Windows Stifinder** eller **Egenskaber** til højre.

![Aktivitet i vinduet Stifinder](./media/data-factory-monitor-manage-app/ActivityWindowExplorer-2.png)

### <a name="refresh-activity-windows"></a>Opdatere aktivitet i windows  
Oplysningerne er ikke automatisk opdateres, så du kan bruge **opdatere** knappen (anden knap) på kommandolinjen manuelt opdatere aktivitetslisten i windows.  
 

### <a name="properties-window"></a>Egenskabsvinduet
Vinduet Egenskaber er i ruden længst til højre i appen overvågning og administration. 

![Egenskabsvinduet](./media/data-factory-monitor-manage-app/PropertiesWindow.png)

Det viser egenskaberne for det element, du har valgt i ressource explorer (trævisningen) (eller) diagram visningen (eller) aktivitet windows liste. 

### <a name="activity-window-explorer"></a>Aktivitet i vinduet Stifinder

**Aktivitet i vinduet Stifinder** -vinduet er i ruden længst til højre i overvågning og administration af App. Oplysninger om aktivitetsvinduet, du har valgt på listen aktivitet Windows pop op- eller aktivitet Windows vises. 

![Aktivitet i vinduet Stifinder](./media/data-factory-monitor-manage-app/ActivityWindowExplorer-3.png)

Du kan skifte til en anden aktivitet vindue ved at klikke på den i kalendervisningen øverst. Du kan også bruge den **venstre pil**/**højrepil** knapperne øverst for at se aktivitet windows fra den forrige/næste uge.

Du kan bruge knapperne på værktøjslinjen i den nederste rude for at **køre** aktivitetsvinduet eller **opdatere** oplysninger i ruden. 

### <a name="script"></a>Script 
Du kan bruge fanen **Script** til at få vist JSON definitionen af den valgte Data Factory-enhed (sammenkædede service, datasæt og pipeline). 

![Under fanen script](./media/data-factory-monitor-manage-app/ScriptTab.png)

## <a name="using-system-views"></a>Brug af systemvisninger
Overvågning og administration af App omfatter færdigbyggede systemvisninger (**seneste aktivitet i windows**, **mislykket aktivitet windows**, **Igangværende aktivitet windows**), hvor du kan få vist seneste/mislykkedes/igangværende aktivitet windows til dine data factory. 

Skifte til fanen **Overvågning visninger** i venstre side ved at klikke på den. 

![Overvågning visninger under fanen](./media/data-factory-monitor-manage-app/MonitoringViewsTab.png)

Der er tre systemvisninger, der understøttes i øjeblikket. Vælg en indstilling for at se seneste aktivitet windows (eller) mislykkedes aktivitet windows (eller) igangværende aktivitet windows på listen aktivitet Windows (nederst i den midterste rude). 

Når du vælger **seneste aktivitet windows** indstilling, kan du se alle seneste aktivitet vinduer i faldende rækkefølge af **sidste forsøg tid**. 

Du kan bruge visningen **mislykket aktivitet windows** til at se alle mislykkedes aktivitet vinduer på listen. Vælg et vindue mislykkedes aktivitet på listen for at se oplysninger om det i vinduet **Egenskaber** (eller) **Aktivitet vinduet Stifinder**. Du kan også hente en hvilken som helst logfiler for et mislykkedes aktivitet vindue. 


## <a name="sorting-and-filtering-activity-windows"></a>Sortering og filtrering af aktivitet i windows
Ændre indstillingerne for **Starttidspunkt** og **Sluttidspunkt** i kommandolinjen til filter aktivitet windows. Når du ændrer starttidspunkt og sluttidspunkt, skal du klikke på knappen ud for sluttidspunkt til at opdatere listen aktivitet i Windows.

![Start- og sluttidspunkt](./media/data-factory-monitor-manage-app/StartAndEndTimes.png)

> [AZURE.NOTE] Hele tiden er i øjeblikket i UTC-format i overvågning og administration af App. 

Klik på navnet på en kolonne **aktivitet Windows liste**(for eksempel: Status). 

![Menuen aktivitet Windows opstilling kolonne](./media/data-factory-monitor-manage-app/ActivityWindowsListColumnMenu.png)

Du kan gøre følgende:

- Sortere i stigende rækkefølge.
- Sortere i faldende rækkefølge.
- Filtrere efter en eller flere værdier (klar, venter osv.)

Når du angiver et filter på en kolonne, kan du se knappen filter aktiveret for den pågældende kolonne til at angive, at værdierne i kolonnen er filtreret værdier. 

![Filtrere i kolonne i aktivitet Windows liste](./media/data-factory-monitor-manage-app/ActivityWindowsListFilterInColumn.png)

Du kan bruge det samme pop op-vindue til at rydde filtre. Hvis du vil fjerne alle filtre til listen aktivitet windows, skal du klikke på knappen Ryd filter på kommandolinjen. 

![Ryd alle filtre i aktivitet Windows liste](./media/data-factory-monitor-manage-app/ClearAllFiltersActivityWindowsList.png)


## <a name="performing-batch-actions"></a>Udføre batchen handlinger

### <a name="rerun-selected-activity-windows"></a>Kør markeret aktivitet i windows
Vælg en aktivitet vindue, skal du klikke på pil ned for den første linje kommandoknappen, og vælg **Kør** / **Kør igen med strømmen i pipeline**. Når du vælger **Kør igen med strømmen i pipeline** indstilling, kører det igen samt alle mod strømmen aktivitet vinduer. 
    ![Kør et aktivitet vindue](./media/data-factory-monitor-manage-app/ReRunSlice.png)

Du kan også vælge flere aktivitet vinduer på listen, og kør igen dem på samme tid. Kan du filtrere aktivitet windows-baseret på status (for eksempel: **mislykket**) og derefter køre igen vinduerne mislykkedes aktivitet efter rette det problem, der får aktivitet windows til at mislykkes. Se afsnittet nedenfor for at få oplysninger om filtrering aktivitet windows på listen.  

### <a name="pauseresume-multiple-pipelines"></a>Afbryd midlertidigt/Fortsæt flere rørledninger
Du kan markere flere elementer to eller flere rørledninger (med CTRL) og bruge kommandoknapper liggende (fremhævet med rødt rektangel på følgende billede) til Afbryd/Fortsæt dem ad gangen.

![Afbryd/Genoptag på kommandolinjen](./media/data-factory-monitor-manage-app/SuspendResumeOnCommandBar.png)

## <a name="creating-alerts"></a>Oprette påmindelser 
Siden beskeder kan du oprette en besked, vise/Rediger/slette eksisterende beskeder. Du kan også deaktivere/aktivere en besked. Hvis du vil se siden beskeder, skal du klikke på fanen beskeder.

![Fanen beskeder](./media/data-factory-monitor-manage-app/AlertsTab.png)

### <a name="to-create-an-alert"></a>Oprette en besked

1. Klik på **Tilføj påmindelse** for at tilføje en besked. Du kan se siden detaljer. 

    ![Opret beskeder - siden detaljer](./media/data-factory-monitor-manage-app/CreateAlertDetailsPage.png)
1. Angiv **navn** og **Beskrivelse** for den vigtige besked, og klik på **Næste**. Du bør se siden **filtre** .

    ![Opret beskeder - filtre side](./media/data-factory-monitor-manage-app/CreateAlertFiltersPage.png)

2. Vælg den **begivenhed**, **status**og **understatus** (valgfrit), du vil tjenesten Data Factory til at give dig besked om, og klik på **Næste**. Du bør se siden **modtagere** .

    ![Opret beskeder - modtagere side](./media/data-factory-monitor-manage-app/CreateAlertRecipientsPage.png) 
3. Vælg **mail abonnement administratorer** indstilling og/eller angive **Yderligere administrator mail**, og klik på **Udfør**. Du bør se beskeden på listen. 
    
    ![Liste over beskeder](./media/data-factory-monitor-manage-app/AlertsList.png)

Brug knapperne der er knyttet til den vigtige besked til Rediger/Slet/Deaktiver/aktiver en besked på listen beskeder. 

### <a name="eventstatussubstatus"></a>Status-begivenhed/understatus
Den følgende tabel indeholder listen over tilgængelige hændelser og statusser (og understatus).

Navn på denne begivenhed | Status | Sub status
-------------- | ------ | ----------
Aktivitet køre Introduktion | I gang | Starte
Aktivitet køre færdig | Lykkedes | Lykkedes 
Aktivitet køre færdig | Mislykkedes| Mislykkedes ressourceallokering<br/><br/>Udførelse af mislykkedes<br/><br/>Timeout<br/><br/>Mislykkedes validering<br/><br/>Efterladt uden vedligeholdelse
Efter behov HDI klynge oprette Introduktion | I gang | &nbsp; |
Efter behov HDI klynge oprettet | Lykkedes | &nbsp; |
Efter behov HDI klynge slettet | Lykkedes | &nbsp; |
### <a name="to-editdeletedisable-an-alert"></a>Til Rediger/Slet/deaktivere en besked


![Beskeder knapper](./media/data-factory-monitor-manage-app/AlertButtons.png)



    
 


