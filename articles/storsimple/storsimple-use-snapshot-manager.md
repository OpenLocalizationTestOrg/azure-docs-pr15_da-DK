<properties 
   pageTitle="Brugergrænsefladen StorSimple øjebliksbillede Manager | Microsoft Azure"
   description="Beskriver brugergrænsefladen StorSimple af snapshots og forklarer, hvordan du bruger den til at administrere sikkerhedskopieringsjob og kataloget til sikkerhedskopiering."
   services="storsimple"
   documentationCenter="NA"
   authors="SharS"
   manager="carmonm"
   editor="" />
<tags 
   ms.service="storsimple"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="TBD"
   ms.date="04/25/2016"
   ms.author="v-sharos" />

# <a name="storsimple-snapshot-manager-user-interface"></a>StorSimple øjebliksbillede Manager-brugergrænseflade

## <a name="overview"></a>Oversigt

StorSimple øjebliksbillede Manager har en intuitiv brugergrænseflade, som du kan bruge til at tage og administrere sikkerhedskopier. Dette selvstudium giver en introduktion til brugergrænsefladen og derefter beskrives det, hvordan du skal bruge de komponenter. Have en detaljeret beskrivelse af StorSimple øjebliksbillede Manager skal du se [Hvad er StorSimple øjebliksbillede Manager?](storsimple-what-is-snapshot-manager.md)

### <a name="console-description"></a>Console beskrivelse

Klik på ikonet StorSimple øjebliksbillede Manager på din computer for at få vist brugergrænsefladen. Vinduet console vises, som vist i nedenstående illustration.

![StorSimple øjebliksbillede Manager ruder](./media/storsimple-use-snapshot-manager/HCS_SSM_gui_panes.png)

Vinduet console indeholder fem vigtigste elementer. Klik på det relevante hyperlink til en fuldstændig beskrivelse af hvert element.

- [Menulinje](#menu-bar) 
- [Værktøjslinjen](#tool-bar) 
- [Omfang rude](#scope-pane) 
- [Ruden med søgeresultater](#results-pane) 
- [Ruden Dokumenthandlinger](#actions-pane) 

Desuden understøtter StorSimple øjebliksbillede Manager [tastaturnavigation og en række genveje](#keyboard-navigation-and-shortcuts).

### <a name="console-accessibility"></a>Console hjælp til handicappede

Brugergrænsefladen StorSimple øjebliksbillede Manager understøtter hjælp til handicappede-funktioner i Windows-operativsystemet og Microsoft Management Console (MMC) samt nogle StorSimple øjebliksbillede Manager-specifikke tastaturgenveje. 

- For en beskrivelse af funktionerne i Windows Hjælp til handicappede, skal du gå til [tastaturgenveje til Windows](https://support.microsoft.com/kb/126449). 

- For en beskrivelse af MMC hjælpefunktioner til handicappede, skal du gå til [Hjælp til handicappede i MMC 3.0](https://technet.microsoft.com/library/cc766075.aspx)

- Gå til [tastaturnavigation og genveje](#keyboard-navigation-and-shortcuts)for en beskrivelse af StorSimple øjebliksbillede Manager hjælpefunktioner til handicappede.

## <a name="menu-bar"></a>Menulinje

Menulinjen øverst i vinduet console indeholder [filen](#file-menu), [handling](#action-menu), [Vis](#view-menu), [Favoritter](#favorites-menu), [vinduet](#window-menu)og [hjælpe](#help-menu) menuer.

Klik på et element på menulinjen for at få vist en liste over tilgængelige kommandoer i menuen, der. I følgende eksempel viser menuen **Vis** , der er markeret på menulinjen.

![Vismenuen markeret](./media/storsimple-use-snapshot-manager/HCS_SSM_View_menu.png)

### <a name="file-menu"></a>Menuen Filer

Menuen **filer** indeholder standardkommandoerne MMC Microsoft Management Console ().

#### <a name="menu-access"></a>Menuen access

Klik på **filer** på menulinjen for at få vist menuen **filer** . Følgende menu vises.

![Menuen StorSimple øjebliksbillede Manager filer](./media/storsimple-use-snapshot-manager/HCS_SSM_FileMenu.png) 

#### <a name="menu-description"></a>Menuen beskrivelse

I følgende tabel beskrives elementer, der vises i menuen **filer** .

| Menupunktet | Beskrivelse |
|:----------|:-------------|
| Ny       | Klik på **Ny** for at oprette en ny konsol på baggrund af StorSimple øjebliksbillede Manager. |
| Åbn      | Klik på **Åbn** for at åbne en eksisterende konsol. |
| Gem      | Klik på **Gem** for at gemme den aktuelle konsol. |
| Gem som   | Klik på **Gem som** for at oprette en ny, omdøbte forekomst af den aktuelle konsol. Brug indstillingen **Gem som** til at tilpasse en visning og gemme den til senere hentning. For eksempel kan du oprette StorSimple øjebliksbillede Manager snap-ins, der peger på bestemte servere. |
| Tilføj/fjern Snap-in | Klik på **Tilføj/fjern Snap-in** til at tilføje eller fjerne snap-ins og til at organisere noder i ruden **område** . Gå til [Tilføj, fjern, og Organiser Snap-ins og udvidelser i MMC 3.0](https://technet.microsoft.com/library/cc722035.aspx)kan finde flere oplysninger. |
| Indstillinger   | Klik på **Indstillinger** for at ændre ikonet console, angive bruger access tilstande og tilladelser eller slette console filer for at øge ledig diskplads. |
| Liste over filer stier | Klik på en sti i opstillingen genåbne en fil, du senest har åbnet. |
| Afslut      | Klik på **Afslut** for at lukke menuen **filer** . |
 
### <a name="action-menu"></a>Handlingsmenuen

Brug menuen **handling** til at vælge fra tilgængelige handlinger. De elementer, der er tilgængelige for dig, afhænger af det valg, du foretager i ruden **omfang** eller ruden med **Søgeresultater** .

#### <a name="menu-access"></a>Menuen access

For at få vist menuen **handling** , kan du gøre et af følgende:

- Højreklik på et element i ruden **omfang** eller ruden med **Søgeresultater** .

- Markere et element i ruden **omfang** eller ruden med **Søgeresultater** , og klik derefter på **handling** i menulinjen. 

Eksempelvis hvis du vælger den øverste knude i ruden **omfang** og derefter skal du højreklikke på eller klik på **handling** i menulinjen, vises følgende menu.
 
![StorSimple øjebliksbillede Manager handlingsmenuen](./media/storsimple-use-snapshot-manager/HCS_SSM_Action_menu.png)

Ruden **Handlinger** (til højre for konsollen) indeholder den samme liste over handlinger som **handlingsmenuen** . Ruden **Handlinger** indeholder også **få vist** menuindstillinger, som gør det muligt at oprette en brugerdefineret visning af ruden **resultater** .

![Åbne handlingsruden med menuen Vis](./media/storsimple-use-snapshot-manager/HCS_SSM_ActionsPane_Results.png)

#### <a name="menu-description"></a>Menuen beskrivelse

Den følgende tabel indeholder en alfabetisk liste over StorSimple øjebliksbillede Manager handlinger. 

- Kolonnen **handling** indeholder handlinger, du kan udføre på noder og resultater. 

- Kolonnen **Navigation** forklarer, hvordan du få vist det relevante menupunkt under **handling** , så du kan vælge handlingen. Nogle handlinger vises i flere **handling** menuer. For disse handlinger skal du vælge én **Navigation** indstilling fra listen med punktopstilling. 

- Kolonnen **Beskrivelse** beskriver, hvordan du bruger hver handling på **handlingsmenuen** eller ruden Dokumenthandlinger og forklarer, hvad gør den.

>[AZURE.NOTE] Ruden **Dokumenthandlinger** og **handling** menuer indeholde flere indstillinger, som **visningen**, **Nyt vindue herfra**, **opdatere**, **Eksporter listen**og **Hjælp**. Disse indstillinger er tilgængelige som en del af MMC, og der ikke er specifikke til StorSimple øjebliksbillede Manager. Tabellen indeholder beskrivelser af disse indstillinger.
 
| Handling  | Navigation  | Beskrivelse  |
|:--------|:------------|:-------------|
| Godkende | Klik på noden **enheder** , og højreklik på en enhed i ruden **resultater** . | Klik på **Godkend** for at indtaste den adgangskode, du har konfigureret til enheden. |
| Klon  | Udvid **Kataloget sikkerhedskopiering**, udvide **Skyen Snapshots**, skal du klikke på en daterede sikkerhedskopi, og derefter vælge en enhed i ruden **resultater** . | Klik på **Klon** for at oprette en kopi af et snapshot af en sky og gemme den på en placering, som du angiver. |
| Konfigurere en enhed | Højreklik på noden **enheder** . | Klik på **konfigurere en enhed** for at konfigurere en enkelt eller flere enheder til at oprette forbindelse til værten for Windows. |
| Opret politik for sikkerhedskopiering | Gør et af følgende:<ul><li>Højreklik på **sikkerhedskopien politikker**.</li><li>Klik på eller udvid **Lydstyrken grupper**, og højreklik derefter på en volumenlicens-gruppe.</li><li>Klik på eller udvid **Sikkerhedskopi katalog**, og højreklik derefter på en volumenlicens-gruppe.</li></ul> | Klik på **Opret sikkerhedskopieringspolitik** for at konfigurere en planlagt sikkerhedskopiering for en gruppe af lydstyrken. |
| Oprette lydstyrken gruppe | Gør et af følgende:<ul><li>Klik på noden **enheder** , og derefter skal du højreklikke på en enhed i ruden **resultater** .</li><li>Højreklik på noden **Lydstyrken grupper** .</li></ul> | Klik på **Opret lydstyrken gruppe** for at tildele enheder til en volumenlicens-gruppe. |
| Slet | Klik på en node eller resultat (dette element vises på mange **handling** menuer og **Handlinger** ruder). | Klik på **Slet** for at slette node eller resultat, du har valgt. Når bekræftelsesdialogboksen vises, skal du bekræfte eller annullere sletningen. |
| Detaljer | Klik på noden **enheder** , og derefter skal du højreklikke på en enhed i ruden **resultater** . | Klik på **Detaljer** for at få vist oplysninger om konfigurationen til en enhed. |
| Rediger | Klik på **Sikkerhedskopiér politikker**, og derefter skal du højreklikke på en politik i ruden **resultater** . | Klik på **Rediger** for at ændre tidsplanen for sikkerhedskopiering for en gruppe af lydstyrken. |
| Eksportere liste | Klik på en hvilken som helst node eller resultat (dette element vises på alle **handling** menuer og **Handlinger** ruder). | Klik på **Eksporter listen** for at gemme en liste i en fil med kommaseparerede værdier (CSV). Du kan derefter importere denne fil til et regnearksprogram til analyse. |
| Hjælp | Klik på en hvilken som helst node eller resultat. (Dette element vises på alle **handling** menuer og **Handlinger** ruder). | Klik på **Hjælp** for at åbne online Hjælp i et separat browservindue. |
| Nyt vindue herfra | Klik på en hvilken som helst node eller resultat (dette element vises på alle **handling** menuer og **Handlinger** ruder). | Klik på **Nyt vindue her** for at åbne et nyt StorSimple øjebliksbillede Manager-vindue.|
| Opdater | Klik på en hvilken som helst node eller resultat (dette element vises på alle **handling** menuer og **Handlinger** ruder). | Klik på **Opdater** for at opdatere vinduet aktuelt viste StorSimple øjebliksbillede Manager. |
| Opdatere enhed | Klik på noden **enheder** , og højreklik på en enhed i ruden **resultater** . | Klik på **Opdater enhed** for at synkronisere en bestemt tilsluttet enhed med StorSimple øjebliksbillede Manager. |
| Opdatere enheder | Højreklik på noden **enheder** . | Klik på **Opdater enheder** for at synkronisere din liste over tilsluttede enheder med StorSimple øjebliksbillede Manager. |
| Scan enheder | Højreklik på noden **enheder** . | Klik på **Scan enheder** for at opdatere listen over enheder, der vises i ruden **resultater** . |
| Gendanne | Udvid **Kataloget sikkerhedskopiering**, udvide en volumenlicens-gruppe, udvid **Lokale Snapshots** eller **Skyen Snapshots**, og derefter skal du højreklikke på en sikkerhedskopi. | Klik på **Gendan** for at erstatte de aktuelle lydstyrke gruppe data med data fra den valgte sikkerhedskopi. |
| Tage sikkerhedskopi | Gør et af følgende:<ul><li>Udvid **Lydstyrken grupper**, og derefter skal du højreklikke på en volumenlicens-gruppe.</li><li>Udvid **Sikkerhedskopi katalog**, og derefter skal du højreklikke på en volumenlicens-gruppe.</li></ul> | Klik på **Tage sikkerhedskopiering** for at starte en sikkerhedskopi med det samme. |
| Skift mellem importerer visning | Højreklik på den øverste knude i ruden **område** (noden **StorSimple øjebliksbillede Manager** i eksemplerne). | Klik på **Skift mellem importerer visning** for at vise eller skjule grupperne lydstyrken og tilknyttede sikkerhedskopier, der er importeret fra dashboardet StorSimple Manager service. |

### <a name="view-menu"></a>Vismenuen

Brug **menuen** til at oprette en brugerdefineret visning af **resultater** ruden indhold. Menuen **Vis** indeholder **Tilføj/fjern kolonner** og **Tilpas** indstillinger.

#### <a name="menu-access"></a>Menuen access

Du kan åbne menuen **Vis** på menulinjen eller i ruden **Handlinger** .

![Menuen Vis StorSimple øjebliksbillede Manager](./media/storsimple-use-snapshot-manager/HCS_SSM_View_menu.png) 

#### <a name="menu-description"></a>Menuen beskrivelse

I følgende tabel beskrives elementer, der vises i menuen **Vis** .

| Menupunktet  | Beskrivelse |
|:-----------|:-------------|
| Tilføj/fjern kolonner | Klik på **Tilføj/fjern kolonner** for at tilføje eller fjerne kolonner i ruden **resultater** . |
| Tilpasse | Klik på **Tilpas** for at vise eller skjule elementer i vinduet StorSimple øjebliksbillede Manager console. |

### <a name="favorites-menu"></a>Foretruknemenuen

Brug menuen **Favoritter** til at tilføje, fjerne og organisere sidevisninger og opgaver, du bruger ofte. 

#### <a name="menu-access"></a>Menuen access

Du kan få adgang til menuen **Favoritter** på menulinjen.

![Menuen StorSimple øjebliksbillede Manager foretrukne](./media/storsimple-use-snapshot-manager/HCS_SSM_FavoritesMenu.png)

#### <a name="menu-description"></a>Menuen beskrivelse

I følgende tabel beskrives elementer, der vises i menuen **Foretrukne** .

| Menupunktet |  Beskrivelse |
|:----------|:-------------|
| Føj til Favoritter | Klik på **Føj til Favoritter** for at tilføje den aktuelle visning til listen over favoritter. |
| Organisere Favoritter | Klik på **Arranger Favoritter** for at organisere indholdet af mappen Favoritter. |

### <a name="window-menu"></a>Vinduesmenuen

Brug menuen **vindue** til at tilføje og omarrangere StorSimple øjebliksbillede Manager console-vinduer.

#### <a name="menu-access"></a>Menuen access

Du kan få adgang til menuen **vindue** på menulinjen.

![Menuen StorSimple øjebliksbillede Manager vindue](./media/storsimple-use-snapshot-manager/HCS_SSM_WindowMenu.png)

Opstilling i bunden af menuen viser vinduer, der aktuelt er åbne. Klik på en hvilken som helst vindue i denne liste til sætter vinduet i forgrunden. 

#### <a name="menu-description"></a>Menuen beskrivelse

I følgende tabel beskrives de elementer, der vises i menuen vindue.

| Menupunktet  | Beskrivelse |
|:-----------|:-------------|
| Nyt vindue | Klik på **Nyt vindue** for at åbne et nyt vindue console (ud over det eksisterende vindue). |
| Kaskadevis   | Klik på **Kaskadevis** for at få vist vinduerne åben konsol i en overlappende typografi. |
| Delt vandret | Klik på **Side om side vandret** for at få vist vinduerne åben konsol i et felt (eller gitter) format. |
| Arrangere ikoner | Hvis du har flere console windows åbne og spredt over dit skrivebord, minimere dem og derefter klikke på **Arranger ikoner** for at arrangere dem i en vandret række i bunden af skærmen. |

### <a name="help-menu"></a>Menuen Hjælp

Brug menuen **Hjælp** til at få vist tilgængelig online Hjælp til StorSimple af snapshots og MMC. Du kan også få vist oplysninger om de MMC og StorSimple øjebliksbillede Manager softwareversioner, der er installeret på computeren. 

Du kan få adgang til menuen **Hjælp** på menulinjen. Du kan også åbne StorSimple øjebliksbillede Manager Hjælp-emner fra ruden **Handlinger** .

![Menuen StorSimple øjebliksbillede Manager Hjælp](./media/storsimple-use-snapshot-manager/HCS_SSM_HelpMenu.png)

#### <a name="menu-description"></a>Menuen beskrivelse

I følgende tabel beskrives elementer, der vises i menuen Hjælp.

| Menupunktet  | Beskrivelse  |
|:-----------|:-------------|
| Hjælp til StorSimple øjebliksbillede Manager | Klik på **Hjælp på StorSimple øjebliksbillede Manager** for at åbne StorSimple øjebliksbillede Manager Hjælp i et separat vindue. |
| Emner i Hjælp |Klik på **emner i Hjælp** for at åbne MMC onlinehjælp i et separat vindue. |
| TechCenter-websted | Klik på **TechCenter websted** for at åbne Microsoft TechNet Tech Center hjemmeside i et separat vindue. |
| Om Microsoft Management Console | Klik på **Om Microsoft Management Console** for at se, hvilken version af Microsoft Management Console er installeret på computeren. |
| Om StorSimple øjebliksbillede Manager | Klik på **Om StorSimple øjebliksbillede Manager** for at se, hvilken version af tilføjelsesprogrammet er installeret på computeren. |

## <a name="tool-bar"></a>Værktøjslinjen

Værktøjslinjen er placeret under menulinjen indeholder ikoner for navigation og opgave. Hvert ikon, der er en genvej til en bestemt opgave.

### <a name="icon-descriptions"></a>Beskrivelser af statusikoner

I følgende tabel beskrives de ikoner, der vises på værktøjslinjen. 

| Ikonet  | Beskrivelse  |
|:------|:-------------| 
| ![Venstre pil](./media/storsimple-use-snapshot-manager/HCS_SSM_LeftArrow.png) | Klik på ikonet venstre pil for at vende tilbage til den forrige side. |
| ![Højre pil](./media/storsimple-use-snapshot-manager/HCS_SSM_RightArrow.png) | Klik på den højre pil for at gå til den næste side (Hvis pilen er gråt, handlingen, der er ikke tilgængelig). |
| ![Ikonet op](./media/storsimple-use-snapshot-manager/HCS_SSM_Up.png) | Klik på ikonet op for at gå et niveau op i konsoltræet (ruden **område** ). |
| ![Vis/Skjul konsoltræet](./media/storsimple-use-snapshot-manager/HCS_SSM_ShowConsoleTree.png) | Klik på ikonet Vis/Skjul console træet for at få vist eller skjule ruden **område** . |
| ![Eksportere liste](./media/storsimple-use-snapshot-manager/HCS_SSM_ExportListIcon.png) | Klik på ikonet Eksporter liste for at eksportere en liste til en CSV-fil, du angiver. |
| ![Hjælpikonet](./media/storsimple-use-snapshot-manager/HCS_SSM_HelpIcon.png)  |Klik på hjælpikonet for at åbne en online MMC Hjælp-emne. |
| ![Vis/Skjul handlinger rude](./media/storsimple-use-snapshot-manager/HCS_SSM_ShowAction.png) | Klik på Vis/Skjul ikonet i ruden **Handlinger** til at vise eller skjule ruden **Handlinger** . 
 
## <a name="scope-pane"></a>Omfang rude

Ruden **omfang** er den længst til venstre rude i StorSimple øjebliksbillede Manager UI. Det indeholder træet console (eller node) og er den primære navigation metode til StorSimple øjebliksbillede Manager. 
 
### <a name="scope-pane-structure"></a>Omfang ruden struktur

Ruden **område** indeholder en række klikbart objekter (noder) organiseret i en træstruktur. 

![Omfang rude](./media/storsimple-use-snapshot-manager/HCS_SSM_Scope_pane.png) 

- Hvis du vil udvide eller skjule en node, skal du klikke på pileikonet ud for nodenavnet.

- Klik på nodenavnet for at få vist status eller indholdet af en node. Oplysninger, der vises i ruden **resultater** . 

Ruden **område** indeholder følgende noder: 

- [Noden enheder](#devices-node) 
- [Enheder node](#volumes-node) 
- [Lydstyrken grupper node](#volume-groups-node) 
- [Sikkerhedskopiér politikker node](#backup-policies-node) 
- [Sikkerhedskopiér katalog node](#backup-catalog-node) 
- [Job node](#jobs-node) 

### <a name="scope-pane-tasks"></a>Omfang ruden opgaver

Du kan bruge ruden **område** til at udføre en handling på en bestemt node. Hvis du vil markere en opgave, skal du gøre et af følgende:

- Højreklik på noden, og vælg derefter opgaven fra den viste menu.

- Klik på noden, og klik derefter på **handling** i menulinjen. Vælg opgaven, fra den viste menu.

- Klik på noden, og derefter vælge handlingen i ruden **Handlinger** .

Når du markerer en node og bruge en af følgende metoder til at se en oversigt over opgaver, vises kun de handlinger, der kan udføres på noden.

### <a name="devices-node"></a>Noden enheder

Noden **enheder** repræsenterer StorSimple enheder og StorSimple virtuelle enheder, der er forbundet til StorSimple øjebliksbillede Manager. Vælg denne node for at oprette forbindelse og konfigurere en enhed, og importere den tilknyttede enheder, enheder grupper og eksisterende sikkerhedskopier. Flere enheder kan være forbundet til en enkelt vært.

- For at udvide noden, skal du klikke på pileikonet ud for **enheder**.

- Højreklik på noden **enheder** for at få vist en menu med tilgængelige handlinger, eller Højreklik på en af de noder, der vises i den udvidede visning.

- Hvis du vil se en liste over konfigurerede enheder, skal du klikke på **enheder** i ruden **omfang** . Listen over enheder, sammen med oplysninger om hver enhed, vises i ruden **resultater** .

### <a name="volumes-node"></a>Enheder node

Noden **enheder** repræsenterer de drev, der svarer til de enheder, der er tilsluttet af værten, herunder dem opdaget ved iSCSI, og hvilke opdaget ved en enhed. Brug denne node til at få vist listen over tilgængelige enheder og tildele enkelte enheder til lydstyrke grupper.

- For at udvide noden, skal du klikke på pileikonet ud for **enheder**.

- Højreklik på noden **enheder** for at få vist en menu med tilgængelige handlinger, eller Højreklik på en af de noder, der vises i den udvidede visning.

- Hvis du vil se en liste over drev, skal du klikke på **enheder** i ruden **omfang** . På listen over enheder, sammen med oplysninger om hver beholdning, vises i ruden **resultater** .

### <a name="volume-groups-node"></a>Lydstyrken grupper node

Lydstyrken grupper er også kendt som konsistens grupper. Hver lydstyrken gruppe er en samling af programmet beslægtede enheder, der hjælper med at sikre, at programmet konsistens under sikkerhedskopiering. Brug noden **Lydstyrken grupper** til at konfigurere disse grupper og tage interaktive sikkerhedskopier eller oprette sikkerhedskopiering tidsplaner. 

- Tryk på pileikonet ud for **Lydstyrken grupper**for at udvide noden.

- Højreklik på noden **Lydstyrken grupper** for at få vist en menu med tilgængelige handlinger, eller Højreklik på en af de noder, der vises i den udvidede visning.

- Klik på **Lydstyrke grupper** i ruden **område** for at få vist en liste over lydstyrken grupper. En liste over lydstyrken grupper, sammen med oplysninger om hver lydstyrken gruppe skal vises i ruden **resultater** .

### <a name="backup-policies-node"></a>Sikkerhedskopiér politikker node

Sikkerhedskopiering politikker er job tidsplaner til lokal og snapshots i skyen. Brug noden **Sikkerhedskopiering politikker** til at angive, hvor ofte der oprettes en sikkerhedskopi, og hvor lang tid en sikkerhedskopi bør bevares. 

- Tryk på pileikonet ud for **Sikkerhedskopiering af politikker**for at udvide noden.

- Højreklik på noden **Sikkerhedskopi politikker** for at få vist en menu med tilgængelige handlinger, eller Højreklik på en af de noder, der vises i den udvidede visning.

- Klik på **Sikkerhedskopiering politikker** i ruden **område** for at få vist en liste over sikkerhedskopiering politikker. På listen over sikkerhedskopiering politikker, sammen med oplysninger om hver enkelt politik vises i ruden **resultater** .

>[AZURE.NOTE] Du kan bevare højst 64 sikkerhedskopier.


### <a name="backup-catalog-node"></a>Sikkerhedskopiér katalog node

Noden **Sikkerhedskopi katalog** indeholder lister over på stedet og sikkerhedskopier af Azure StorSimple enheder. Denne node er organiseret efter lydstyrken gruppe, og hver lydstyrken Gruppebeholder indeholder separat strukturer for lokale snapshots ( **Lokal Snapshot**s node) og skyen snapshots (noden **Skyen Snapshots** ). Når det er udvidet, vises hver lydstyrken Gruppebeholder de vellykket sikkerhedskopier, der er taget interaktivt eller af en konfigurerede politik.

- For at udvide noden, skal du klikke på pileikonet ud for **Sikkerhedskopiering af kataloget**.

- Højreklik på noden **Sikkerhedskopi katalog** for at få vist en menu med tilgængelige handlinger, eller Højreklik på en af de noder, der vises i den udvidede visning.

- Hvis du vil se en liste over sikkerhedskopiering snapshots, skal du klikke på **Kataloget sikkerhedskopiering** i ruden **omfang** . På listen med snapshots, sammen med oplysninger om hver snapshot, vises i ruden **resultater** .

### <a name="local-snapshots-node"></a>Lokale Snapshots node

Noden **Lokale Snapshots** viser lokale snapshots for en bestemt lydstyrken gruppe. Noden er placeret under noden **Sikkerhedskopi katalog** i ruden **område** . Lokale snapshots er punkt-in-time kopier af lydstyrken data, der er gemt på Azure StorSimple enhed. Denne type sikkerhedskopiering kan typisk oprettes og hurtigt gendannes. Du kan bruge en lokal snapshot, som du ville gøre en lokal sikkerhedskopi.

- Tryk på pileikonet ud for **Lokale Snapshots**for at udvide noden.

- Højreklik på den **Lokale Snapshots** node for at få vist en menu med tilgængelige handlinger, eller Højreklik på en af de noder, der vises i den udvidede visning.

- Hvis du vil se en liste over lokale snapshots, skal du klikke på **Lokale Snapshots** i ruden **omfang** . På listen med snapshots, sammen med oplysninger om hver snapshot, vises i ruden **resultater** .

### <a name="cloud-snapshots-node"></a>Skyen Snapshots node

Noden **Skyen Snapshots** viser skyen snapshots for en bestemt lydstyrken gruppe. Noden er placeret under noden **Sikkerhedskopi katalog** i ruden **område** . Skyen snapshots er punkt-in-time kopier af lydstyrken data, der er gemt i skyen. Et snapshot af en sky er svarer til et øjebliksbillede, replikeres til et andet, storage-system. Skyen snapshots er særligt nyttige i genoprettelse efter nedbrud scenarier.

- Tryk på pileikonet ud for **Skyen Snapshots**for at udvide noden.

- Højreklik på noden **Skyen Snapshots** for at få vist en menu med tilgængelige handlinger, eller Højreklik på en af de noder, der vises i den udvidede visning.

- Hvis du vil se en liste over skyen snapshots, skal du klikke på **Skyen Snapshots** i ruden **omfang** . På listen med snapshots, sammen med oplysninger om hver snapshot, vises i ruden **resultater** .

### <a name="jobs-node"></a>Job node

Noden **job** indeholder oplysninger om planlagt, kører og seneste fuldførte sikkerhedskopieringsjob. 

- Tryk på pileikonet ud for **Jobs**for at udvide noden.

- Højreklik på noden **Jobs** for at få vist en menu med tilgængelige handlinger, eller Højreklik på en af de noder, der vises i den udvidede visning.

- Udvid noden **Jobs** for at få vist en liste over planlagte opgaver, og klik derefter på **planlagt**. På listen over konfigureret tidligere jobs samt oplysninger om hvert job vises i ruden **resultater** . 

- Udvid noden **Jobs** for at få vist en liste over senest fuldførte job, og klik derefter på **seneste 24 timer**. Der vises en liste over sager, der blev afsluttet i de seneste 24 timer i ruden **resultater** . Ruden **resultater** indeholder også oplysninger om hver fuldførte opgave.

- Udvid noden **Jobs** for at få vist en liste over job, der kører, og klik derefter på **kører**. Liste over kørende job samt oplysninger om hvert job vises i ruden **resultater** .

## <a name="results-pane"></a>Ruden med søgeresultater

Ruden **resultater** er den midterste rude i StorSimple øjebliksbillede Manager UI. Den indeholder lister og detaljerede statusoplysninger for noden, du har valgt i ruden **område** .

### <a name="example"></a>Eksempel

Klik på noden **Lydstyrken grupper** i ruden **område** for at få vist i følgende eksempel. Ruden **resultater** viser en liste over lydstyrken grupper med oplysninger om hver gruppe.

![Ruden med søgeresultater](./media/storsimple-use-snapshot-manager/HCS_SSM_Results_pane.png) 

Du kan konfigurere oplysninger vises i ruden **resultater** : Højreklik på en node i ruden **omfang** , klik på **Vis**, og klik derefter på **Tilføj/fjern kolonner**.

## <a name="actions-pane"></a>Ruden Dokumenthandlinger

Ruden **Handlinger** er den højre rude i StorSimple øjebliksbillede Manager UI. Den indeholder en menu med handlinger, som du kan udføre på node, visning eller data, du vælger i **omfang** rude eller ruden med **Søgeresultater** . Ruden **Handlinger** indeholder de samme kommandoer som menuerne **handling** , der er tilgængelig for elementer i **omfang** rude og ruden med **Søgeresultater** . Finde en beskrivelse af hver handling i tabellen i sektionen **handling** i menuen.

### <a name="examples"></a>Eksempler

Udvid noden **Jobs** for at få vist i følgende eksempel, i ruden **omfang** og klikke på **planlagt**. Ruden **Handlinger** viser de tilgængelige handlinger for noden **planlagt** .

![Eksempel på Handlinger ruden planlagte opgaver](./media/storsimple-use-snapshot-manager/HCS_SSM_ActionsPane.png) 

Se flere indstillinger i ruden **omfang** , udvid noden **job** , skal du klikke på **planlagt**, og klik derefter på en planlagt opgave i ruden **resultater** . Ruden **Handlinger** viser de tilgængelige handlinger for planlagte jobbet, som vist i følgende eksempel.

![Eksempel på Handlinger ruden job handlinger](./media/storsimple-use-snapshot-manager/HCS_SSM_ActionsPane_Results.png)

## <a name="keyboard-navigation-and-shortcuts"></a>Tastaturnavigation og genveje

StorSimple øjebliksbillede Manager gør det muligt for handicappede i Windows-operativsystemet og Microsoft Management Console (MMC). Den indeholder også nogle tastaturfunktioner navigation og genveje, der er specifikke til StorSimple øjebliksbillede Manager, som det er beskrevet i følgende afsnit.
 
- [Taster til navigation](#keyboard-navigation-keys) 
- [Menulinje genvejstaster](#menu-bar-shortcut-keys) 
- [Genvejstaster i omfang](#scope-pane-shortcut-keys) 

### <a name="keyboard-navigation-keys"></a>Taster til navigation

I følgende tabel beskrives de taster, du kan bruge til at navigere i StorSimple øjebliksbillede Manager-brugergrænsefladen. 

| Navigation nøgle  | Handling  |
|:----------------|:--------| 
| Tasten pil ned | Brug pil ned for at flytte til det næste element i en menu eller ruden lodret. |
| Angiv | Tryk på Enter for at udføre en handling og derefter gå videre til næste trin. For eksempel kan du trykke på Enter for at vælge **Næste**, **OK**eller **Opret**, og derefter gå til næste trin i guiden.|
| ESC | Tryk på Esc for at lukke en menu eller for at annullere og lukke en side.|
| F1 | Tryk på F1 for at få vist et emne i Hjælp til det aktive vindue.|
| F5 | Tryk på F5 for at opdatere en node. |
| F6 | Tryk på F6 for at flytte fra ruden **område** til ruden **resultater** .|
| F10 | Tryk på F10 for at gå til menulinjen. |
| Venstre pil | Brug den venstre piletast til at flytte vandret fra en søjle menuindstillingen til den forrige indstilling. Når du flytter til det forrige element på menulinjen, vises menuen handling (eller kontekst) til det forrige element. |
| Højre pil | Brug tasten højre pil til at flytte vandret fra én i menuen til næste. Når du flytter til det næste element på menulinjen, vises menuen handling (eller kontekst) for det nye element.
| TAB-tasten | Brug tasten Tab til at flytte til næste rude i konsollen eller til det næste felt markeringen eller tekst i en side. |
| Tasten pil op | Brug pil op lodret flytte til det forrige element på en menu eller rude. |

### <a name="menu-bar-shortcut-keys"></a>Menulinje genvejstaster

I følgende tabel beskrives de kombinationer af genvejstaster til menulinjen. Når du trykker på genvejstasterne, og menuen åbnes, kan du bruge menuen genvejstaster (tasterne understreget i menuen). Gå til [menulinjen](#menu-bar)kan finde flere oplysninger om menulinjen.

| Genvej | Resultat                    | Menuen genvejstast | Resultat          |
|:---------|:--------------------------|:------------------|:----------------|
| ALT + F    | Åbner menuen **filer** .  | N | Åbner en ny forekomst af konsol.   |
|          |                           | O | Åbner siden **Administration** . |
|          |                           | S | Gemmer StorSimple øjebliksbillede Manager-konsollen.|
|          |                           | A | Åbner siden **Gem som** . |
|          |                           | M | Åbner siden **Tilføj/fjern Snap-in** .|
|          |                           | P | Åbner siden med **Indstillinger** . |
|          |                           | H | Åbner online Hjælp.|
| ALT + A    | Åbner menuen **handling** .| Jeg | Slår indstillingen Importér skærm til og fra.|
|          |                           | W | Åbner en ny StorSimple øjebliksbillede Manager konsol.|
|          |                           | F | Opdaterer StorSimple øjebliksbillede Manager-konsollen.|
|          |                           | L | Åbner siden **Eksporter listen** . 
|          |                           | H | Åbner online Hjælp.|
| ALT + V    | Åbner menuen **Vis** .  | A | Åbner siden **Tilføj/fjern kolonner** . |
|          |                           | U | Åbner siden **Tilpas visning** . |
| ALT + O    | Åbner menuen **Foretrukne** . | A | Åbner siden **Føj til Favoritter** . |
|          |                           | O | Åbner siden **Arranger Favoritter** .|
| ALT + W    | Åbner menuen **vindue** .| N | Åbner et nyt StorSimple øjebliksbillede Manager-vindue.|
|          |                           | C | Viser alle åbne konsolvinduer i en overlappende typografi.|
|          |                           | T | Viser alle åbne konsolvinduer i et gittermønster. |
|          |                           | Jeg | Arrangerer ikonerne i en vandret række i bunden af skærmen.|
| ALT + H    | Åbner menuen **Hjælp** .  | H | Åbner online Hjælp.|
|          |                           | T | Åbner websiden Microsoft TechNet Tech Center.|
|          |                           | A | Åbner siden **Om Microsoft Management Console** . |
 
### <a name="scope-pane-shortcut-keys"></a>Genvejstaster i omfang

De følgende tabeller viser genvejen tastekombinationer for hver enkelt node i ruden **omfang** . 

- [Enheder node genvejstaster](#devices-node-shortcut-keys)
- [Enheder node genvejstaster](#volumes-node-shortcut-keys)
- [Genvejstaster til lydstyrke grupper node](#volume-groups-node-shortcut-keys)
- [Sikkerhedskopiér politikker node genvejstaster](#backup-policies-node-shortcut-keys)
- [Sikkerhedskopiér katalog node genvejstaster](#backup-catalog-node-shortcut-keys)
- [Job node genvejstaster](#jobs-node-shortcut-keys)

#### <a name="devices-node-shortcut-keys"></a>Enheder node genvejstaster

| Menugenvej | Resultat                               |
|:--------------|:-------------------------------------|
| C             | Åbner siden **Konfigurer en enhed** . |
| D             | Opdaterer listen over enheder og detaljer for enhed.|
| V             | Åbner menuen **Vis** . |
| W             | Åbner en ny StorSimple øjebliksbillede Manager konsol fokuseret på noden **Detaljer** . |
| F             | Opdaterer StorSimple øjebliksbillede Manager-konsollen. |
| L             | Åbner siden **Eksporter listen** . 
| H             | Åbner online Hjælp.|
 

#### <a name="volumes-node-shortcut-keys"></a>Enheder node genvejstaster

| Menugenvej   | Resultat                              |
|:----------------|:------------------------------------|
| V               | Opdaterer listen over enheder.        |
| V (Tryk to gange) | Åbner menuen **Vis** .            |
| W               | Åbner en ny StorSimple øjebliksbillede Manager konsol fokuseret på noden **enheder** .|
| F               | Opdaterer StorSimple øjebliksbillede Manager-konsollen.|
| L               | Åbner siden **Eksporter listen** . 
| H               | Åbner online Hjælp.|
 
#### <a name="volume-groups-node-shortcut-keys"></a>Genvejstaster til lydstyrke grupper node

| Menugenvej   | Resultat                              |
|:----------------|:------------------------------------|
| G               | Åbner siden **Opret en volumenlicens-gruppe** . |
| V               | Åbner menuen **Vis** . |
| W               | Åbner en ny StorSimple øjebliksbillede Manager konsol fokuseret på noden **Lydstyrken grupper** .|
| F               | Opdaterer StorSimple øjebliksbillede Manager-konsollen. |
| L               | Åbner siden **Eksporter listen** . |
| H               | Åbner online Hjælp.|

#### <a name="backup-policies-node-shortcut-keys"></a>Sikkerhedskopiér politikker node genvejstaster

| Menugenvej   | Resultat                              |
|:----------------|:------------------------------------|
| B               | Åbner siden **Opret en politik** . |
| V               | Åbner menuen **Vis** .            |
| W               | Åbner en ny StorSimple øjebliksbillede Manager konsol fokuseret på noden **Lydstyrken grupper** .|
| F               | Opdaterer StorSimple øjebliksbillede Manager-konsollen.|
| L               | Åbner siden **Eksporter listen **. 
| H               | Åbner online Hjælp.|
 
#### <a name="backup-catalog-node-shortcut-keys"></a>Sikkerhedskopiér katalog node genvejstaster

| Menugenvej   | Resultat                              |
|:----------------|:------------------------------------|
| W               | Åbner en ny StorSimple øjebliksbillede Manager konsol fokuseret på noden **Lydstyrken grupper** . |
| F               | Opdaterer StorSimple øjebliksbillede Manager-konsollen. |
| H               | Åbner online Hjælp.|
 
#### <a name="jobs-node-shortcut-keys"></a>Job node genvejstaster

| Menugenvej   | Resultat                              |
|:----------------|:------------------------------------|
| V               | Åbner menuen **Vis** .            |
| W               | Åbner en ny StorSimple øjebliksbillede Manager konsol fokuseret på noden **job** .|
| F               | Opdaterer StorSimple øjebliksbillede Manager-konsollen.|
| L               | Åbner siden **Eksporter listen** .     |
| H               | Åbner onlinehjælp                   |
 
## <a name="next-steps"></a>Næste trin

- Lær, hvordan du [bruger StorSimple øjebliksbillede Manager til at administrere din StorSimple løsning](storsimple-snapshot-manager-admin.md).
- Lær, hvordan du [bruger StorSimple øjebliksbillede Manager til at oprette forbindelse og administrere enheder](storsimple-snapshot-manager-manage-devices.md).
