<properties
   pageTitle="Oversigt over StorSimple virtuelle matrix | Microsoft Azure"
   description="I denne artikel beskrives den StorSimple virtuelle matrix, en integreret storageløsning, der administrerer lager opgaver mellem en lokal virtuel enhed og Microsoft Azure skylagring."
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
   ms.date="10/06/2016"
   ms.author="alkohli" />

# <a name="introduction-to-the-storsimple-virtual-array"></a>Introduktion til den virtuelle StorSimple-matrix

## <a name="overview"></a>Oversigt

Velkommen til Microsoft Azure StorSimple virtuelle matrixen, en integreret storageløsning, der administrerer lager opgaver mellem en lokal virtuel enhed kører i et hypervisor og Microsoft Azure skylagring. Virtuel matrixen (også kaldet StorSimple lokale virtuelle enheden) er en effektiv, økonomiske og nem at håndtere filserver eller iSCSI-serverløsning, der løser mange af de almindelige problemer og udgifter, der er knyttet til enterprise-lager og databeskyttelse. Den virtuelle matrix er særligt velegnet til remote office/gren scenarier i office (ROBO).

Denne oversigt fokuserer på den virtuelle matrix. 

- Gå til for en oversigt over StorSimple 8000 serien, [StorSimple 8000 serie: en hybrid skyen løsning](storsimple-overview.md). 

- Gå til [StorSimple onlinehjælp](http://onlinehelp.storsimple.com/)for at få oplysninger om StorSimple 5000/7000 serie enhed.

Virtuel matrixen understøtter iSCSI eller Server meddelelse blok (små og mellemstore virksomheder) protocol. Den kører på den eksisterende hypervisor infrastruktur og giver overgang switchmiljø til skyen, skyen sikkerhedskopi, hurtigt gendanne, på elementniveau gendannelse og genoprettelse efter genoprettelse funktioner.

Den følgende tabel opsummerer de vigtige funktioner i den virtuelle matrix.

| Funktion | Virtuel matrix |
| ------- | ------------- |
|Installationskrav | Bruger virtualization infrastruktur (Hyper-V eller VMware)|
| Tilgængelighed | Enkelt node |
| Samlede kapacitet (herunder skyen) |Op til 64 TB brugbare kapacitet per virtuel enhed |
| Lokale kapacitet | 390 GB til 6,4 TB brugbare kapacitet per virtuel enhed (grund til at klargøre 500 GB til 8 TB plads på harddisken)|
| Oprindelig protokoller | iSCSI eller små og mellemstore virksomheder |
| Gendannelse tid mål (RTO) | iSCSI: mindre end to minutter uanset størrelse |
| Gendannelse punkt mål (frigivne Produktionsordre) | Daglig sikkerhedskopier og efter behov sikkerhedskopier |
| Lagerplads overgang switchmiljø | Varme tilknytning for at afgøre, hvilke data skal være lagdelt ind eller ud |
| Support | Virtualization infrastruktur understøttes af leverandøren |
| Ydeevne | Varierer afhængigt af underliggende infrastruktur |
| Datamobilitet | Kan gendanne til den samme enhed eller på elementniveau gendannelse (filserver) |
| Lagerplads niveauer | Lokale hypervisor lager og skyen |
| Dele størrelse |Lagdelt: op til 20 TB; lokalt fastgjort: op til 2 TB |
| Enhedens størrelse |Lagdelt: op til 5 TB; lokalt fastgjort: op til 500 GB |
| Snapshots | Nedbrud ensartet |
| På elementniveau gendannelse | Ja. brugere kan gendanne fra aktier |

## <a name="why-use-storsimple"></a>Hvorfor bruge StorSimple?

StorSimple forbinder brugere og -servere til Azure-lager i minutter, med ingen ændring af programmet.

I følgende tabel beskrives nogle af de vigtigste fordele, som indeholder et virtuelt matrix løsningen.

| Funktion | Fordel |
|---------|---------|
| Gennemsigtige integration | Virtuel matrixen understøtter iSCSI- eller protokollen små og mellemstore virksomheder. Flytning af data mellem det lokale niveau og skyen niveau er problemfri og bemærke, at brugeren.|
| Begrænset lageromkostninger | Med StorSimple klargøre du tilstrækkelige lokale lager at opfylde aktuelle behov for de mest anvendte varmt data. Som lagerplads skal vokse, skyen StorSimple niveauer kolde data til økonomisk lagerplads. Dataene er deduplicated og komprimeret inden du sender til skyen til at reducere krav til lagerplads og udgifter.|
| Forenklet Lagerstyring | StorSimple giver Central administration i skyen ved hjælp af StorSimple Manager til at administrere flere enheder.| 
| Forbedret nedbrud og overholdelse af regler | StorSimple muliggør hurtigere nedbrud ved at gendanne metadata med det samme og gendanne dataene, efter behov. Det betyder normal handlinger kan fortsætte med minimale afbrydelser.|
| Datamobilitet | Data lagdelt i skyen kan åbnes fra andre websteder til gendannelse og overførsel formål. Bemærk, at du kan gendanne data kun til den oprindelige virtuelle matrix. Du kan dog bruge nedbrud gendannelsesfunktioner til at gendanne hele virtuelle matrixen til en anden virtuelle matrix.|

## <a name="storsimple-workload-summary"></a>StorSimple arbejdsbelastningen oversigt

En oversigt over de understøttede StorSimple arbejdsmængder tabelform nedenfor.

| Scenarie                | Arbejdsbelastning              | Understøttes |  Begrænsninger                                  | Version              |
|-------------------------|-----------------------|-----------|------------------------------------------------|----------------------|
| ROBO samarbejde      | Deling af filer          | Ja       | Se [maksimale begrænsninger for filserver](storsimple-ova-limits.md). <br>Se [systemkrav for understøttede små og mellemstore virksomheder versioner](storsimple-ova-system-requirements.md).   | Alle versioner      |


## <a name="workflows"></a>Arbejdsprocesser

StorSimple virtuelle matrixen er særligt velegnet til de arbejdsprocesser, der er følgende:

- [Skybaseret Lagerstyring](#cloud-based-storage-management)
- [Placering af uafhængige sikkerhedskopi](#location-independent-backup)
- [Data beskyttelse og genoprettelse efter nedbrud](#data-protection-and-disaster-recovery)

### <a name="cloud-based-storage-management"></a>Skybaseret Lagerstyring

Du kan bruge tjenesten StorSimple Manager kører i portalen Azure klassisk til at administrere data, der er gemt på flere enheder og flere steder. Dette er især nyttig i fordelt gren scenarier. Bemærk, at du skal oprette separate forekomster af tjenesten StorSimple Manager til at administrere virtuelle matrixer og fysiske StorSimple enheder. 

![skybaseret Lagerstyring](./media/storsimple-ova-overview/cloud-based-storage-management.png)

### <a name="location-independent-backup"></a>Placering af uafhængige sikkerhedskopi

Skyen øjebliksbilleder giver med den virtuelle matrix, en placering uafhængige, punkt-in-time kopi af en masse eller del. Skyen snapshots er aktiveret som standard og kan ikke deaktiveres. Alle enheder og aktier er sikkerhedskopiering op ad gangen via en enkelt daglig sikkerhedskopiering politik, og du kan tage flere adhoc sikkerhedskopier, når det er nødvendigt.

### <a name="data-protection-and-disaster-recovery"></a>Data beskyttelse og genoprettelse efter nedbrud

Virtuel matrixen understøtter følgende data beskyttelse og genoprettelse efter genoprettelsesscenarier:

- **Lydstyrken eller del Gendan** – Brug indstillingen Gendan som ny arbejdsproces for at gendanne en masse eller del. Brug denne metode til at gendanne hele lydstyrken eller del.
- **På elementniveau gendannelse** – aktier give nem adgang til seneste sikkerhedskopier. Du kan nemt gendanne individuelle filer fra en speciel .backup mappe tilgængelig i skyen. Denne Gendan egenskab er bruger-driven og ingen administrative brugerinput er påkrævet.
- **Nedbrud** – Brug failover muligheden for at gendanne alle enheder eller share for at få en ny virtuelle matrix. Du oprette den nye virtuelle matrix og registrere den med tjenesten StorSimple Manager og derefter mislykkes over den oprindelige virtuelle matrix. Den nye virtuelle matrix derefter antager de ressourcer, der er klargjort. 

## <a name="virtual-array-components"></a>Virtuel matrixkomponenter

Virtuel matrixen indeholder følgende komponenter:

- [Virtuel matrix](#virtual-array) – en hybrid cloud storageenhed, der er baseret på en virtuel maskine, der er klargjort i din virtuelt miljø eller hypervisor.  
- [Tjenesten StorSimple Manager](#storsimple-manager-service) – en udvidelse af Azure klassisk portalen, hvor du kan administrere en eller flere StorSimple enheder fra en enkelt grænseflade, som du kan få adgang til fra forskellige geografiske placeringer. Du kan bruge tjenesten StorSimple Manager til at oprette og administrere tjenester, få vist og administrere enheder og beskeder og administrere enheder, aktier og eksisterende snapshots.
- [Lokale web-brugergrænseflade](#local-web-user-interface) – en webbaseret brugergrænseflade, der bruges til at konfigurere enheden, så den kan oprette forbindelse til det lokale netværk og derefter registrere enheden med tjenesten StorSimple Manager. 
- [Kommandolinjen](#command-line-interface) – en Windows PowerShell-grænseflade, som du kan bruge til at starte en session med support på den virtuelle matrix.
I følgende afsnit beskrives hver af disse komponenter mere detaljeret og beskriver, hvordan løsningen arrangerer data, tildeler lagerplads og letter Lagerstyring og beskyttelse af data.

### <a name="virtual-array"></a>Virtuel matrix

Den virtuelle matrix er en enkelt node lagerplads løsning, der indeholder primære lagerplads, administrerer kommunikation med skylagring og hjælper med at sikre sikkerheden og fortrolighed af alle data, der er gemt på enheden.

Den virtuelle matrix er tilgængelig i en model, der kan downloades. Matrixen lagerplads har en maksimale kapacitet 6,4 TB på enheden (med et underliggende lagerplads krav på 8 TB) og 64 TB herunder lagerplads i skyen. 

Den virtuelle matrix har følgende funktioner:

- Det er rentable. Det gør brug af din eksisterende virtualization infrastruktur og kan anvendes på dit eksisterende Hyper-V eller VMware hypervisor.
- Det er placeret i datacenteret og kan konfigureres som en iSCSI-server eller en filserver. 
- Det er integreret med skyen.
- Sikkerhedskopier er gemt i skyen, som kan lette nedbrud og forenkle på elementniveau gendannelse (ILR). 
- Du kan anvende opdateringer til den virtuelle matrix, ligesom du ville gøre det til en fysisk enhed.

>[AZURE.NOTE] Et virtuelt matrix kan ikke udvides. Det er vigtigt at klargøre tilstrækkelig lagerplads, når du opretter den virtuelle enhed. 

### <a name="storsimple-manager-service"></a>Tjenesten StorSimple Manager

Microsoft Azure StorSimple giver en webbaseret brugergrænseflade (tjenesten StorSimple Manager), der gør det muligt at administrere datacenter og cloud storage centralt. Du kan bruge tjenesten StorSimple Manager til at udføre følgende opgaver:

- Administrere flere StorSimple virtuelle matrixer fra en enkelt tjeneste. 
- Konfigurere og administrere sikkerhedsindstillinger for StorSimple enheder. (Kryptering i skyen er afhængig af Microsoft Azure API'er).
- Konfigurere lagerplads kontolegitimationsoplysninger og egenskaber.
- Konfigurere og administrere enheder eller aktier.
- Sikkerhedskopiere og gendanne data på enheder eller aktier.
- Overvågning af ydeevnen.
- Gennemse systemindstillinger og identificere eventuelle problemer.

Du kan bruge tjenesten StorSimple Manager til at udføre daglige administration af din virtuelle matrix.

Gå til [Brug tjenesten StorSimple Manager til at administrere enheden StorSimple](storsimple-manager-service-administration.md)kan finde flere oplysninger.

### <a name="local-web-user-interface"></a>Lokale web-brugergrænseflade

Virtuel matrixen indeholder en webbaseret brugergrænseflade, der bruges til enkeltstående konfiguration og registrering af enheden med tjenesten StorSimple Manager. Du kan bruge det til at lukke og genstarte den virtuelle matrix, fungerer diagnostiske test, opdatere software, ændre administratoradgangskode enhed, få vist system logfiler og kontakte Microsoft Support for at arkivere en serviceanmodning. 

Oplysninger om brug af Brugergrænsefladen webbaseret, gå til [Brug webbaseret Brugergrænsefladen til at administrere din StorSimple virtuelle matrix](storsimple-ova-web-ui-admin.md).

### <a name="command-line-interface"></a>Kommandolinjen

Grænsefladen inkluderet Windows PowerShell gør det muligt at starte en session med support med Microsoft Support, så de kan hjælpe dig med at fejlfinde og løse problemer, der kan opstå på enheden virtuelle.

## <a name="storage-management-technologies"></a>Teknologier til styring af lager

Ud over den virtuelle matrix og andre komponenter bruger StorSimple løsningen følgende softwareteknologier giver hurtig adgang til vigtige data, reducere lagerplads forbrug og beskytte data, der er gemt på din virtuelle matrix:

- [Automatisk lagring overgang switchmiljø](#automatic-storage-tiering) 
- [Lokalt fastgjorte aktier og enheder](#locally-pinned-shares-and-volumes)
- [Deduplication og komprimering af data lagdelt eller sikkerhedskopieret til skyen](#deduplication-and-compression-for-data-tiered/backed-up-to-the-cloud) 
- [Planlagte og on demand sikkerhedskopier](#scheduled-and-on-demand-backups)

### <a name="automatic-storage-tiering"></a>Automatisk lagring overgang switchmiljø

Den virtuelle matrix bruger en ny tiering metode til at administrere lagrede data på tværs af matrixen virtuelt miljø og skyen. Der er kun to niveauer: lokale virtuelle matrix og Azure i skyen lagerplads. Matrixen StorSimple virtuelle arrangeres automatisk data i de lag, der er baseret på et varmekort, som holder styr på aktuelle brugen, alder og relationer til andre data. Data, der er mest aktive (nyeste) er gemt lokalt, mens mindre aktive og inaktive data automatisk overført til skyen. (Alle sikkerhedskopier er gemt i skyen). StorSimple justerer og omarrangerer data og ændre lagerplads tildelinger som brugsmønstre. For eksempel blive nogle oplysninger om mindre aktiv over en periode. Når det bliver gradvist mindre aktive, er det lagdelt ud i skyen. Hvis de samme data aktiveres igen, er det lagdelt i matrixen lagerplads.

Data til en bestemt lagdelte del eller lydstyrken er garanti for sin egen lokale niveau mellemrum. (cirka 10% af den samlede mængde klargjorte plads for den pågældende del eller lydstyrke). Mens dette reducerer den tilgængelige lagerplads på den virtuelle enhed for den pågældende del eller lydstyrken, sikres det, at overgang switchmiljø for én del eller lydstyrken, ikke påvirkes af andre aktier eller enheder tiering behov. Dermed kan ikke en meget optaget arbejdsbyrde på én del eller lydstyrken tvinge alle andre arbejdsbelastninger til skyen. 

![automatisk lagring overgang switchmiljø](./media/storsimple-ova-overview/automatic-storage-tiering.png)

>[AZURE.NOTE] Du kan angive et lokalt fastgjort lydstyrken, i hvilket tilfælde dataene forbliver på den virtuelle matrix og den er aldrig lagdelt til skyen. Gå til [lokalt fastgjort aktier og enheder](#locally-pinned-shares-and-volumes)kan finde flere oplysninger.

### <a name="locally-pinned-shares-and-volumes"></a>Lokalt fastgjorte aktier og enheder

Du kan oprette relevante aktier og enheder, som lokalt fastgjort. Denne egenskab sikrer, at data, der kræves af kritiske programmer forbliver i den virtuelle matrix og er aldrig lagdelt til skyen. Lokalt fastgjorte aktier og enheder har følgende funktioner: 

- De er ikke underlagt skyen latenstider eller problemer med serverforbindelsen.
- De stadig få glæde af StorSimple skyen sikkerhedskopiering og genoprettelse efter genoprettelse funktioner.

Du kan gendanne et lokalt fastgjorte share eller lydstyrken som lagdelt eller en lagdelte del eller lydstyrken lokalt fastgjort. 

Gå til [Brug tjenesten StorSimple Manager til at administrere enheder](storsimple-manage-volumes-u2.md)kan finde flere oplysninger om lokalt fastgjorte enheder.

### <a name="deduplication-and-compression-for-data-tiered-or-backed-up-to-the-cloud"></a>Deduplication og komprimering af data lagdelt eller sikkerhedskopieret til skyen

StorSimple bruger deduplication og data komprimering til at reducere yderligere lagerplads krav i skyen. Deduplication reducerer den samlede mængde data, der er gemt ved at fjerne redundans i det gemte datasæt. Som oplysningerne ændres, StorSimple ignorerer uændret dataene og registrerer kun ændringer. Desuden reducerer StorSimple mængden af lagrede data ved at identificere og fjerne dubletdata. 

>[AZURE.NOTE] Data, der er gemt på den virtuelle matrix er ikke deduplicated eller komprimeres. Alle deduplication og komprimering sker lige før dataene sendes til skyen.

### <a name="scheduled-and-on-demand-backups"></a>Planlagte og on demand sikkerhedskopier

StorSimple data adgangskodebeskyttelse funktioner gør det muligt at oprette sikkerhedskopier efter behov. Desuden sikrer en plan for sikkerhedskopiering standard, at dataene er sikkerhedskopieret dagligt. Sikkerhedskopier er taget i form af trinvise snapshots, som er gemt i skyen. Snapshots, som registrerer kun ændringerne, siden sidste sikkerhedskopiering, kan oprettes og hurtigt gendannes. Disse snapshots kan være alvorligt vigtige i genoprettelse efter nedbrud scenarier, fordi de erstatte sekundær lagerplads systemer (såsom sikkerhedskopi på bånd), og gør det muligt at gendanne data til din datacenter eller til alternative websteder, hvis det er nødvendigt.

## <a name="next-steps"></a>Næste trin

Lær, hvordan du [forbereder portalen virtuelle matrix](storsimple-ova-deploy1-portal-prep.md).


