<properties 
   pageTitle="Overvåge enheden StorSimple | Microsoft Azure"
   description="Beskriver, hvordan du bruger tjenesten StorSimple Manager til at overvåge i/o-ydeevne, kapacitet anvendelsen, netværkets hastighed og enhed ydeevne."
   services="storsimple"
   documentationCenter="NA"
   authors="alkohli"
   manager="carmonm"
   editor="" />
<tags 
   ms.service="storsimple"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="TBD"
   ms.date="08/16/2016"
   ms.author="alkohli" />

# <a name="use-the-storsimple-manager-service-to-monitor-your-storsimple-device"></a>Brug tjenesten StorSimple Manager til at overvåge enheden StorSimple 

## <a name="overview"></a>Oversigt

Du kan bruge tjenesten StorSimple Manager til at overvåge bestemte enheder i din StorSimple løsning. Du kan oprette brugerdefinerede diagrammer, der er baseret på i/o-ydeevne, kapacitet anvendelsen, netværkets hastighed og enhed ydeevne målepunkter. 

Vælg tjenesten, StorSimple Manager for at få vist de overvågning oplysninger til en bestemt enhed i portalen Azure klassisk. Klik på fanen **skærm** , og vælg derefter fra listen over enheder. Siden **skærm** indeholder følgende oplysninger.

## <a name="io-performance"></a>I/o-ydeevne 

**I/o-ydeevne** spor målepunkter relateret til antallet af læse og skrive handlinger mellem enten iSCSI afsenderen grænseflader på host serveren og enheden eller enheden og skyen. Denne ydeevnen kan måles for et bestemt drev, en bestemt lydstyrken objektbeholder eller alle lydstyrken beholdere.

Nedenstående diagram viser I/O til afsenderen til din enhed for alle enheder til en fremstilling enhed. Målene afbildet læses og skrive byte sekundet, læse og skrive IO-handlinger sekundet, og læse og skrive latenstider.

![EY ydeevne fra afsenderen til enhed](./media/storsimple-monitor-device/StorSimple_IO_Performance_For_InitiatorTODevice_For_AllVolumesM.png)

I den samme enhed afbildes i/o-handlinger for dataene fra enheden til skyen for alle lydstyrken beholdere. Dataene, findes kun i det lineære niveau på enheden, og intet har flyttet til skyen. Der er ingen læse / skrive-handlinger, der foregår fra enheden til skyen. Derfor er spidser i diagrammet med et interval af 5 minutter, der svarer til den hyppighed, hvormed er markeret på godkendelse af gyldighed mellem enheden og tjenesten. 

![EY ydeevne fra enheden til skyen](./media/storsimple-monitor-device/StorSimple_IO_Performance_For_DeviceTOCloud_For_AllVolumeContainersM.png)


For den samme enhed blev oprettet et snapshot af en sky til lydstyrke data fra og med 2:00 pm. Dette er et resultat i data, der flyder fra enheden til skyen. Læser skriver blev served i skyen i denne varighed. EY diagram viser en spidsbelastning i de forskellige former for statistik svarer til det tidspunkt, hvor snapshot blev taget. 

![EY ydeevne til enhed til skyen efter skyen øjebliksbillede](./media/storsimple-monitor-device/StorSimple_IO_Performance_For_DeviceTOCloud_For_AllVolumeContainers2M.png)


## <a name="capacity-utilization"></a>Kapacitet anvendelsen 

**Kapacitet anvendelsen** registrerer målepunkter, der er relateret til mængden data lagerplads, der bruges af enheder, lydstyrke objektbeholdere eller enhed. Du kan oprette rapporter, der er baseret på kapacitet anvendelsen af din primære lagerplads, din skylagring eller din enhed lagerplads. Kapacitet udnyttelse kan måles på et bestemt drev, en bestemt lydstyrken objektbeholder eller alle lydstyrken beholdere.


Den primære, skyen, og kan beskrives enhed lagerkapacitet på følgende måde:

###<a name="primary-storage-capacity-utilization"></a>Primære lagerplads kapacitet anvendelsen
 
Disse diagrammer vises mængden data, der er skrevet til StorSimple enheder, før data, der er deduplicated og komprimeres. Du kan få vist primære lagerplads anvendelsen af alle enheder eller for en enkelt enhed.

Når du få vist de primære lagerplads lydstyrke kapacitet anvendelsen diagrammer for alle enheder kontra hver af de enkelte enheder og opsummere de primære data i begge disse tilfælde, kan der være en uoverensstemmelse mellem de to tal. De samlede primære data på alle enheder kan ikke tilføje til det samlede antal af de primære data for de enkelte enheder. Det kan skyldes et af følgende:

- **Øjebliksbilleddata, som findes for alle enheder**: Dette problem kan ses kun, hvis du kører version tidligere end Update 3. De primære data, der vises for alle enheder er summen af de primære data for hver lydstyrken og øjebliksbilleddata. De primære data, der vises for en given lydstyrken svarer til kun mængden data, der er tildelt på lydstyrken (og omfatter ikke de tilsvarende lydstyrke øjebliksbilleddata).

    Dette kan også forklares ved følgende ligning:

    *Primære data (alle enheder) = Sum (primære data (lydstyrken i) + datastørrelse snapshot (lydstyrken i))*
    
    *hvor, primære data (lydstyrken i) = størrelsen på primære data, der er allokeret til lydstyrke i*
 
    Hvis snapshots slettes via tjenesten, udføres sletningen asynkront i baggrunden. Det kan tage lidt tid, før lydstyrken datastørrelsen skal opdateres efter sletningen øjebliksbillede. 

    Hvis kører opdatering, 3 eller nyere, derefter medtages øjebliksbilleddata ikke i lydstyrken dataene. Og den primære anvendelsen beregnes på følgende måde:

    * Primære data (alle enheder) = Sum (primære data med (lydstyrken i)
    
    *hvor, primære data (lydstyrken i) = størrelsen på primære data, der er allokeret til lydstyrke i*
 
- **Enheder med overvågning deaktiveret inkluderet i alle enheder**: Hvis du har mængder på enheden, overvågning er slået fra, overvågningsdata for disse enkelte enheder er ikke tilgængelige i diagrammerne. Men data for alle enheder i diagrammet omfatter de enheder, overvågning er slået fra. 
 
- **Slettet enheder med live tilknyttede sikkerhedskopier inkluderet til alle enheder**: Hvis enheder, der indeholder data, snapshot slettes men tilknyttede snapshots findes stadig, så du kan få vist en uoverensstemmelse.

- **Slettede enheder, der er inkluderet til alle enheder**: I nogle tilfælde gamle enheder kan findes, selvom disse er blevet slettet. Effekten af at sletningen kan ikke ses, og enheden viser muligvis nederste tilgængelige kapacitet. Skal du kontakte Microsoft Support for at fjerne disse enheder.

Følgende diagrammer viser primær lagerplads kapacitet anvendelsen af en enhed med StorSimple før og efter et snapshot af en sky blev udført. Som det er lige lydstyrken data, bør et snapshot af en sky ikke ændre den primære lagerplads. Diagrammet viser ingen forskel i primære kapacitet anvendelsen som et resultat af tage et snapshot af en sky, som du kan se. Skyen snapshot i gang med omkring 2:00 pm på enheden.

![Primære kapacitet anvendelsen før skyen snapshot](./media/storsimple-monitor-device/StorSimple_PrimaryCapacityUtil_For_AllVolumes2M.png)

![Primære kapacitet anvendelsen efter skyen snapshot](./media/storsimple-monitor-device/StorSimple_PrimaryCapacityUtil_For_AllVolumes1M.png)

Hvis du kører Opdater 2 eller højere, du kan opdele primære lagerplads kapacitet anvendelsen efter en enkelt lydstyrken, alle enheder, alle lagdelte enheder og alle lokalt fastgjorte enheder som vist nedenfor. Opdele efter alle lokalt fastgjorte enheder, så du hurtigt undersøges, hvor meget af det lokale niveau bruges.

![Primære kapacitet anvendelsen for alle lokalt fastgjorte enheder](./media/storsimple-monitor-device/localvolumes.png)


###<a name="cloud-storage-capacity-utilization"></a>Cloud storage kapacitet anvendelsen

Disse diagrammer vises mængden skylagring bruges. Disse data er deduplicated og komprimeres. Dette beløb omfatter skyen snapshots, som kan indeholde data, der ikke afspejles i en hvilken som helst primære lydstyrken og gemmes i ældre eller påkrævet opbevaring formål. Du kan sammenligne primært og cloud storage forbrug figurer for at få en ide om data reduktion rente, selvom antallet ikke vil være nøjagtige. Følgende diagrammer viser cloud storage kapacitet anvendelsen af en StorSimple enhed før og efter et snapshot af en sky blev taget. Skyen snapshot i gang med omkring 2:00 pm på enheden, og du kan se skyen kapacitet anvendelsen som ad gangen, øget fra 5.73 MB til 4.04 GB.

![Skyen kapacitet anvendelsen før skyen øjebliksbillede](./media/storsimple-monitor-device/StorSimple_CloudCapacityUtil_For_AllVolumeContainers2M.png)

![Kapacitet anvendelsen efter skyen snapshot i skyen](./media/storsimple-monitor-device/StorSimple_CloudCapacityUtil_For_AllVolumeContainers1M.png)


###<a name="device-storage-capacity-utilization"></a>Enhed lagerplads kapacitet anvendelsen

Disse søjlediagrammer viser de samlede udnyttelse for den enhed, der vil være mere end primær lagerplads anvendelsen, fordi den indeholder det SSD lineære niveau. Dette niveau indeholder en mængde data, der også findes på enheden er øvrige lag. Kapaciteten i det SSD lineære niveau skiftes, så når der kommer nye data, gamle data flyttes til harddisk niveauet (for, hvornår den er deduplicated og komprimeret) og derefter til skyen.

I tidens løb primære kapacitet anvendelsen og enhed kapacitetsudnyttelse, højst sandsynligt øger sammen indtil dataene begynder at blive lagdelt til skyen. På dette tidspunkt enhed kapacitet anvendelsen begynder sandsynligvis at flade, men primær kapacitet anvendelsen øges, fordi der skrives flere data.

Følgende diagrammer viser primære lagerplads kapacitet anvendelsen af en StorSimple enhed, før og efter et snapshot af en sky blev udført. Skyen snapshot i gang med 2:00 pm og enhed kapacitet anvendelsen gang reducere på det pågældende tidspunkt. Enhed lagerplads kapacitet anvendelsen gik fra 11.58 GB til 7.48 GB. Dette angiver, højst sandsynligt dekomprimerede dataene i det lineære SSD niveau blev deduplicated, komprimeres og flyttes til harddisk niveauet. Bemærk, at hvis enheden allerede har en stor mængde data i både SSD og harddisk niveauer, du ikke måske se denne Formindsk. I dette eksempel har enheden en lille mængde data.

![Enhed kapacitet anvendelsen før skyen øjebliksbillede](./media/storsimple-monitor-device/StorSimple_DeviceCapacityUtil2M.png)

![Enhed kapacitet anvendelsen efter skyen øjebliksbillede](./media/storsimple-monitor-device/StorSimple_DeviceCapacityUtil1M.png)


## <a name="network-throughput"></a>Netværkskapacitet

**Netværkskapacitet** registrerer målepunkter, der er relateret til mængden data, der overføres fra iSCSI afsenderen netværksgrænseflader på host serveren og på enheden og mellem enheden og skyen. Du kan overvåge denne metrikværdi for hver af iSCSI netværk grænsefladerne på din enhed.

Følgende diagrammer viser netværkets hastighed for Data 0 og Data 4 begge 1 GbE netværksgrænseflader på din enhed. I dette tilfælde blev Data 0 skyen aktiverede Data 4 var iSCSI aktiverede. Du kan se både det indgående og udgående trafik for enheden StorSimple. Linjen flad i diagrammet begyndende fra 3:24 pm er på grund fakultet, at vi indsamler data hver 5 minutter, og skal ignoreres. 

![Netværkskapacitet for Data4](./media/storsimple-monitor-device/StorSimple_NetworkThroughput_Data0M.png)

![Netværkskapacitet for Data4](./media/storsimple-monitor-device/StorSimple_NetworkThroughput_Data4M.png)


## <a name="device-performance"></a>Enhed ydeevne 

**Enhed ydeevne** registrerer målepunkter, der er relateret til ydeevnen for din enhed. Det følgende diagram viser CPU anvendelsen statistik for en enhed i fremstilling.

![CPU-forbrug for enhed](./media/storsimple-monitor-device/StorSimple_DeviceMonitor_DevicePerformance1M.png)

## <a name="next-steps"></a>Næste trin

- Lær, hvordan du kan [bruge dashboardet StorSimple Manager service-enhed](storsimple-device-dashboard.md).

- Lær, hvordan du [bruger tjenesten StorSimple Manager for at administrere din StorSimple enhed](storsimple-manager-service-administration.md).
