<properties
   pageTitle="Administrere dine StorSimple drev (U2) | Microsoft Azure"
   description="Forklarer, hvordan du kan tilføje, redigere, overvåge og slette StorSimple enheder, og hvordan du kan tage dem offline, hvis det er nødvendigt."
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
   ms.workload="NA"
   ms.date="10/28/2016"
   ms.author="alkohli" />

# <a name="use-the-storsimple-manager-service-to-manage-volumes-update-2"></a>Bruge tjenesten StorSimple Manager til at administrere enheder (opdatering 2)

[AZURE.INCLUDE [storsimple-version-selector-manage-volumes](../../includes/storsimple-version-selector-manage-volumes.md)]

## <a name="overview"></a>Oversigt

Dette selvstudium forklarer, hvordan du bruger tjenesten StorSimple Manager til at oprette og administrere enheder på StorSimple enhed og StorSimple virtuelle enhed med opdatering 2 installeret.

Tjenesten StorSimple Manager er en udvidelse i Azure klassisk portalen, hvor du kan administrere din StorSimple løsning fra en enkelt grænseflade. Ud over administration af enheder, kan du bruge tjenesten StorSimple Manager til at oprette og administrere StorSimple tjenester, få vist og administrere enheder, se beskeder, og få vist og administrere politikker for sikkerhedskopiering og kataloget til sikkerhedskopiering.

## <a name="volume-types"></a>Typer af lydstyrken

StorSimple enheder kan være:

- **Lokalt fastgjort enheder**: Data på disse enheder forbliver på den lokale StorSimple-enhed til enhver tid.
- **Tiered enheder**: Data på disse enheder kan spilder til skyen.

En arkivering lydstyrke er en slags lagdelte lydstyrken. Større deduplication afsnit størrelsen bruges til arkivering enheder kan kameraet, for at overføre større segmenter af data til skyen. 

Hvis det er nødvendigt, kan du ændre lydstyrken typen fra lokale for lagdelt eller fra lagdelt til lokale. Gå til [ændre typen beholdning](#change-the-volume-type)kan finde flere oplysninger.

### <a name="locally-pinned-volumes"></a>Lokalt fastgjorte enheder

Lokalt fastgjorte enheder er fuldt klargjort enheder, der kan ikke niveau data til skyen, garanterer hvorved det sikres lokale for primære data, uafhængigt af skyen connectivity. Data på lokalt fastgjorte enheder er ikke deduplicated og komprimerede. snapshot af lokalt fastgjorte enheder er dog deduplicated. 

Lokalt fastgjorte enheder er fuldt klargjort; Du skal derfor har tilstrækkelig plads på din enhed, når du opretter dem. Du kan klargøre lokalt fastgjorte enheder op til en maksimal størrelse på 8 TB på StorSimple 8100 enheden og 20 TB på 8600 enheden. StorSimple forbeholder sig den resterende lokale plads på enheden til snapshots, metadata og databehandling. Du kan øge størrelsen på et lokalt fastgjorte lydstyrken til den maksimale tilgængelige plads, men du kan ikke formindske størrelsen på en enhed, der er oprettet én gang.

Når du opretter et lokalt fastgjorte lydstyrken, reduceres den tilgængelige plads til oprettelse af lagdelte enheder. Omvendt gør sig også gældende: Hvis du har eksisterende lagdelte mængder, pladsen til oprettelse af lokalt fastgjort enheder være mindre end de maksimale begrænsninger, der er anført ovenfor. Se [ofte stillede spørgsmål på lokalt fastgjorte enheder](storsimple-local-volume-faq.md)kan finde flere oplysninger om lokale drev.   

### <a name="tiered-volumes"></a>Lagdelte enheder

Lagdelte enheder er tyndt klargjort enheder, hvori du hyppigt dataene forbliver lokalt på enheden og mindre ofte anvendte data er automatisk lagdelt til skyen. Tynd klargøring er en virtualization teknologi, hvor tilgængelige lagerplads vises overskrider fysisk ressourcer. I stedet for at reservere tilstrækkelig lagerplads på forhånd bruger StorSimple tynd klargøring til at tildele lige nok plads til at opfylde aktuelle krav. Hvilke elastiske skylagring letter denne metode, fordi StorSimple kan øge eller mindske skylagring at opfylde skiftende behov.

Hvis du bruger lagdelte lydstyrken til arkivering data, ændrer Hvis du markerer afkrydsningsfeltet **Brug denne lydstyrken for mindre hyppigt arkivering data** størrelsen på deduplication afsnit til din enhed til 512 KB. Hvis du ikke vælger denne indstilling, bruge tilsvarende lagdelte lydstyrken en del udgave af 64 KB. En del større deduplication kan kameraet, for at fremskynde overførsel af store arkivering data til skyen.

>[AZURE.NOTE] Arkivering enheder, der er oprettet med en før opdatering 2 version af StorSimple skal importeres som lagdelte med afkrydsningsfeltet arkivering markeret.

### <a name="provisioned-capacity"></a>Klargjort kapacitet

Se nedenstående tabel for maksimalt klargjort kapacitet til hver type enhed og lydstyrken. (Bemærk, at lokalt fastgjort enheder er ikke tilgængelig på en virtuel enhed).

|             | Maksimale lagdelte lydstyrken størrelse | Maksimum lokalt fastgjort lydstyrken størrelse |
|-------------|----------------------------|------------------------------------|
| **Fysiske enheder** |       |       |
| 8100                 | 64 TB | 8 TB |
| 8600                 | 64 TB | 20 TB |
| **Virtuelle enheder**  |       |       |
| 8010                | 30 TB | I/T.   |
| 8020               | 64 TB | I/T.   |

## <a name="the-volumes-page"></a>Siden enheder

Siden **enheder** tillader dig at administrere lagerenheder, der er klargjort på Microsoft Azure StorSimple enheden til din initiatorer (servere). På listen over enheder vises på enheden StorSimple.

 ![Enheder side](./media/storsimple-manage-volumes-u2/VolumePage.png)

En enhed består af en række attributter:

- **Lydstyrken navn** – et beskrivende navn, der skal være entydigt og hjælper med at identificere lydstyrken. Dette navn bruges også i overvågning rapporter, når du filtrerer på en bestemt enhed.

- **Status** – kan være online eller offline. Hvis en lydstyrken er offline, er det er ikke synlige for initiatorer (servere), der har adgang til at bruge lydstyrken.

- **Kapacitet** – angiver den samlede mængde af data, der kan være gemt ved afsenderen (server). Lokalt fastgjort enheder er fuldt klargjort og er placeret på StorSimple enhed. Lagdelte enheder er tyndt klargjort og data, der er deduplicated. Med tyndt klargjort enheder allokere enheden ikke allerede fysisk lagerkapacitet internt eller på skyen ifølge konfigurerede lydstyrken kapacitet. Kapaciteten lydstyrken er allokeret og consumed efter behov.

- **Type** – angiver, om lydstyrken er **Tiered** (standard) eller **lokalt fastgjort**.

- **Sikkerhedskopiering** – angiver, om der findes en sikkerhedskopi standardpolitikken for lydstyrken.

- **Access** -angiver initiatorer (servere), der har adgang til denne enhed. Initiatorer, der ikke er medlemmer af access kontrolelement post (ACR), der er knyttet til lydstyrken kan ikke se lydstyrken.

- **Overvågnings** -angiver, hvorvidt der skal overvåges en volumenlicens. En enhed har overvågning aktiveret som standard, når den er oprettet. Overvågning er, men, være deaktiveret for en lydstyrken Klon. Følg vejledningen i [skærm en enhed](#monitor-a-volume)for at aktivere overvågning for et drev. 

Bruge instruktionerne i dette selvstudium til at udføre følgende opgaver:

- Tilføje en disk 
- Ændre en volumenlicens 
- Ændre typen lydstyrken
- Slette en volumenlicens 
- Tage en lydstyrken offline 
- Overvåge en volumenlicens 

## <a name="add-a-volume"></a>Tilføje en disk

Du har [oprettet en volumenlicens](storsimple-deployment-walkthrough-u2.md#step-6-create-a-volume) under installation af din StorSimple løsning. Tilføjer en disk er en lignende procedure.

#### <a name="to-add-a-volume"></a>Vil tilføje en disk

1. Vælg enheden, på siden **enheder** , dobbeltklik på den, og klik derefter på fanen **Lydstyrken beholdere** .

2. Vælg en lydstyrken objektbeholder på listen, og dobbeltklik på den for at få adgang til de enheder, der er knyttet til objektbeholderen.

3. Klik på **Tilføj** i bunden af siden. Tilføj en lydstyrken guide starter.

     ![Tilføje lydstyrke guiden grundlæggende indstillinger](./media/storsimple-manage-volumes-u2/TieredVolEx.png)

4. I guiden Tilføj et lydstyrken, under **Grundlæggende indstillinger**skal du gøre følgende:

  1. Angiv et **navn** til din enhed.
  2. Vælg en **Brugen Type** på rullelisten. For arbejdsbelastninger, der kræver data skal være tilgængelig lokalt på enheden til enhver tid, og vælg **Lokalt fastgjort**. Vælg **Tiered**for alle andre former for data. (**Tiered** er standard).
  3. Hvis du har valgt **Tiered** i trin 2, kan du vælge afkrydsningsfeltet **Brug denne lydstyrken for mindre hyppigt arkivering data** for at konfigurere en arkivering lydstyrken.
  4. Angiv **Klargjort kapacitet** til din enhed i GB eller TB. Du kan se [Provisioned kapacitet](#provisioned-capacity) til maksimale størrelser for hver type enhed og lydstyrken. Se på **Tilgængelige kapacitet** til at finde ud af, hvor meget lagerplads er tilgængelig på din enhed.

5. Tryk på pileikonet![Pileikonet](./media/storsimple-manage-volumes-u2/HCS_ArrowIcon.png). Hvis du konfigurerer et lokalt fastgjorte lydstyrken, får du vist følgende meddelelse.

    ![Ændre lydstyrken type meddelelse](./media/storsimple-manage-volumes-u2/LocalVolEx.png)
   
5. Tryk på pileikonet ![pileikonet](./media/storsimple-manage-volumes-u2/HCS_ArrowIcon.png)igen for at gå til siden **Yderligere indstillinger** .

    ![Tilføje lydstyrke guiden yderligere indstillinger](./media/storsimple-manage-volumes-u2/AddVolume2.png)<br>

6. Tilføje en ny access kontrolelement post (ACR) under **Yderligere indstillinger**:
  
  1. Vælg en access kontrolelement post (ACR) på rullelisten. Du kan også tilføje en ny ACR. ACRs bestemmer, hvilke værter har adgang til dine drev ved at sammenligne host IQN med, vises i posten. Hvis du ikke angiver en ACR, får du vist følgende meddelelse.

        ![Angive ACR](./media/storsimple-manage-volumes-u2/SpecifyACR.png)

  2. Vi anbefaler, at du markerer afkrydsningsfeltet **Aktiver en sikkerhedskopi af standard for denne enhed** .
  3. Klik på ikonet Kontrollér ![Ikonet](./media/storsimple-manage-volumes-u2/HCS_CheckIcon.png) for at oprette lydstyrken med de angivne indstillinger.

Ny lydstyrken er nu klar til brug.

>[AZURE.NOTE] Hvis du opretter et lokalt fastgjorte lydstyrken og derefter oprette en anden lokalt fastgjorte lydstyrken med det samme bagefter, lydstyrke oprettelse job køres sekventielt. Det første lydstyrke oprettelse af job skal afsluttes, før det næste lydstyrke oprettelse af job kan begynde.

## <a name="modify-a-volume"></a>Ændre en volumenlicens

Redigere en enhed, når du har brug at udvide den eller ændre de værter, der få adgang til lydstyrken.

> [AZURE.IMPORTANT] 
>
> - Hvis du ændrer størrelsen på enheden, skal ændres på værten samt enhedens størrelse. 
> - Host side trinnene beskrevet her er til Windows Server 2012 (2012R2). Procedurerne for Linux eller andre host operativsystemer vil være forskellige. Henvise til dine host operativsystem instruktioner, når du ændrer lydstyrken på en vært, der kører et andet operativsystem. 

#### <a name="to-modify-a-volume"></a>Ændre en volumenlicens

1. Vælg enheden, på siden **enheder** , dobbeltklik på den, og klik derefter på fanen **Lydstyrken beholdere** .

2. Vælg en lydstyrken objektbeholder på listen, og dobbeltklik på den for at få vist de enheder, der er knyttet til objektbeholderen.

3. Vælge en enhed, og klik på **Rediger**nederst på siden. Guiden Rediger lydstyrken starter.

4. I guiden Rediger lydstyrken under **Grundlæggende indstillinger**kan du gøre følgende:

  - Rediger **navnet**.
  - Konvertere **Brugen Type** fra lokalt fastgjort til lagdelt eller fra lagdelt til lokalt fastgjort (se [ændre typen beholdning](#change-the-volume-type) mere).
  - Øge **klargjort kapacitet**. **Klargjort kapacitet** kan kun øges. Du kan ikke formindske en volumenlicens, når den er oprettet.

5. Under **Yderligere indstillinger**, kan du ændre ACR, forudsat at lydstyrken er offline. Hvis enheden er online, skal du tage det offline først. Referere til trinnene i [tage en lydstyrken offline](#take-a-volume-offline) før at ændre ACR.

    > [AZURE.NOTE] Du kan ikke ændre indstillingen **Aktiver en standard-sikkerhedskopi** for lydstyrken.

6. Gem dine ændringer ved at klikke på ikonet Kontrollér ![Kontrollér-ikon](./media/storsimple-manage-volumes-u2/HCS_CheckIcon.png). Azure klassisk portalen, vises meddelelsen opdaterer lydstyrken. Det vises en meddelelse, når lydstyrken er blevet opdateret.

7. Hvis du udvide en disk, kan du benytte følgende fremgangsmåde på din Windows host computer:

   1. Gå til **Computer Management** ->**Disk Management**.
   2. Højreklik på **Diskadministration** , og vælg **Scan diske**.
   3. Vælg på listen over diske lydstyrken, du har opdateret, højreklikke og derefter vælge **Udvide lydstyrken**. Guiden udvide lydstyrken starter. Klik på **Næste**.
   4. Fuldføre guiden, acceptere standardværdierne. Når guiden er afsluttet, bør lydstyrken vise øget størrelse.

    >[AZURE.NOTE] Hvis du udvide et lokalt fastgjorte lydstyrken og udvid derefter fastgjort en anden lokalt lydstyrken samme bagefter, lydstyrke udvidelse job køres sekventielt. Det første lydstyrke udvidelse job skal afsluttes, før det næste lydstyrke udvidelse job kan begynde.

![Video er til rådighed](./media/storsimple-manage-volumes-u2/Video_icon.png) **Video, der er tilgængelige**

Hvis du vil se en video, der viser, hvordan du kan udvide en enhed, skal du klikke på [her](https://azure.microsoft.com/documentation/videos/expand-a-storsimple-volume/).

## <a name="change-the-volume-type"></a>Ændre typen lydstyrken

Du kan ændre typen beholdning fra lagdelt til lokalt fastgjort eller fra fastgjort lokalt til lagdelte. Denne konvertering må ikke være en hyppige forekomst. Nogle mulige årsager til konvertering af en enhed fra er lagdelt til lokalt fastgjort:

- Lokale garantier for datatilgængelighed og ydeevne
- Fjernelse af skyen latenstider og skyen forbindelsesproblemer.

Dette er typisk lille eksisterende enheder, du vil have adgang til ofte. Et lokalt fastgjorte lydstyrken er fuldt klargjort, når den er oprettet. Hvis du konverterer en lagdelte lydstyrken til et lokalt fastgjorte drev, kontrollerer StorSimple, at du har tilstrækkelig plads på din enhed, før det starter konverteringen. Hvis du har tilstrækkelig plads, modtager du en fejl, og handlingen annulleres. 

> [AZURE.NOTE] Før du starter en samtale fra lagdelt til lokalt fastgjort, skal du kontrollere, at du overvejer dine andre arbejdsbelastninger mellemrum krav. 

Du vil ændre et lokalt fastgjorte lydstyrken til en lagdelte enhed, hvis du har brug for ekstra plads til at klargøre andre enheder. Når du konverterer lokalt fastgjorte lydstyrken til lagdelt tilgængelig kapacitet i enhed øges med størrelsen på den frigivne kapacitet. Hvis forbindelsesproblemer forhindrer konvertering af en enhed fra den lokale type til typen lagdelte, udvise lokale lydstyrken egenskaberne for en lagdelte lydstyrken, indtil konverteringen er fuldført. Dette skyldes, at nogle data kan være flyttet til skyen. Disse spildt data fortsætter med at fylde lokale plads på den enhed, der ikke kan frigives, indtil handlingen genstartes og fuldført.

>[AZURE.NOTE] Konvertere en volumenlicens kan gå et stykke tid, og du kan ikke annullere en konvertering, når programmet starter. Lydstyrken forbliver online under konverteringen, og du kan udføre sikkerhedskopier, men du ikke kan udvide eller gendanne lydstyrken, mens konverteringen finder sted.  

Konvertering fra en lagdelte til et lokalt fastgjorte lydstyrken kan påvirke enhed ydeevnen. Følgende faktorer kan desuden øge den tid, det tager for at fuldføre konverteringen:

- Der er utilstrækkelig båndbredde.

- Der findes ingen aktuelle sikkerhedskopi.

For at minimere de effekter, som kan have disse faktorer:

- Gennemse dine båndbredde, throttling politikker, og Sørg for, at en dedikeret 40 Mbps båndbredde er tilgængelig.
- Planlægge konverteringen for belastet.
- Tage et øjebliksbillede i skyen, før du starter konverteringen.

Hvis du konverterer flere enheder (understøtte forskellige arbejdsbelastninger), skal du prioritere lydstyrken konverteringen, så højere prioritet enheder konverteres først. For eksempel skal du konvertere enheder, der hoster virtuelle maskiner (VM'er) eller enheder med SQL arbejdsbelastninger før du konverterer enheder med filen del arbejdsbelastninger.

#### <a name="to-change-the-volume-type"></a>Ændre lydstyrken

1. Vælg enheden, på siden **enheder** , dobbeltklik på den, og klik derefter på fanen **Lydstyrken beholdere** .

2. Vælg en lydstyrken objektbeholder på listen, og dobbeltklik på den for at få vist de enheder, der er knyttet til objektbeholderen.

3. Vælge en enhed, og klik på **Rediger**nederst på siden. Guiden Rediger lydstyrken starter.

4. På siden **Grundlæggende indstillinger for** at ændre typen brugen ved at vælge den nye type på rullelisten **Type Brug** .

    - Hvis du vil ændre typen til **lokalt fastgjort**, vil om der er tilstrækkelig kapacitet kontrollere StorSimple.
    - Hvis du vil ændre typen til **Tiered** , og denne enhed, der skal bruges til arkivering data, skal du markere afkrydsningsfeltet **Brug denne lydstyrken for mindre hyppigt arkivering data** .

        ![Arkiv afkrydsningsfelt](./media/storsimple-manage-volumes-u2/ModifyTieredVolEx.png)

5. Tryk på pileikonet ![pileikonet](./media/storsimple-manage-volumes-u2/HCS_ArrowIcon.png) at gå til siden **Yderligere indstillinger** . Hvis du konfigurerer et lokalt fastgjorte lydstyrken, vises følgende meddelelse.

    ![Ændre lydstyrken type meddelelse](./media/storsimple-manage-volumes-u2/ModifyLocalVolEx.png)

6. Tryk på pileikonet ![pileikonet](./media/storsimple-manage-volumes-u2/HCS_ArrowIcon.png) igen for at fortsætte.

7. Klik på ikonet Kontrollér ![Ikonet](./media/storsimple-manage-volumes-u2/HCS_CheckIcon.png) for at starte konverteringsprocessen. Portalen Azure, vises meddelelsen opdaterer lydstyrken. Det vises en meddelelse, når lydstyrken er blevet opdateret.

## <a name="take-a-volume-offline"></a>Tage en lydstyrken offline

Du muligvis tage en lydstyrken offline, når du planlægger at redigere eller slette den. Når en enhed er offline, er det er ikke tilgængelig for læse / skrive-adgang. Du skal tage lydstyrken offline på værten og på enheden. 

#### <a name="to-take-a-volume-offline"></a>Tage en lydstyrken offline

1. Sørg for, at den pågældende enhed ikke er i brug før tage det offline.

2. Tage lydstyrken offline på værten første. Dette fjerner en potentiel risiko for beskadigelse af data på enheden. Se vejledningen til din værtsoperativsystemet for bestemte trin.

3. Når værten er offline, kan du foretage lydstyrken på enheden offline ved at benytte følgende fremgangsmåde:

  1. Vælg enheden, på siden **enheder** , dobbeltklik på den, og klik derefter på fanen **Lydstyrken beholdere** . Fanen **Lydstyrken beholdere** lister i et tabelformat alle lydstyrken beholdere, der er knyttet til enheden.
  2. Vælg en objektbeholder lydstyrken og klikke på den for at få vist listen over alle enheder inde i beholderen.
  3. Vælge en enhed, og klik på **Tag offline**.
  4. Klik på **Ja**, når du bliver bedt om en bekræftelse. Lydstyrken skulle nu være offline.

    Når en enhed er offline, bliver indstillingen **Få Online** tilgængelig.

> [AZURE.NOTE] Kommandoen **Tage Offline** sender en anmodning til kameraet, for at tage lydstyrken offline. Hvis hosts stadig bruger lydstyrken, dette resulterer i brudte forbindelser, men at lydstyrken offline mislykkes ikke. 

## <a name="delete-a-volume"></a>Slette en volumenlicens

> [AZURE.IMPORTANT] Kun, hvis det er offline, kan du slette en enhed.

Udfør følgende trin for at slette en enhed.

#### <a name="to-delete-a-volume"></a>Slette en volumenlicens

1. Vælg enheden, på siden **enheder** , dobbeltklik på den, og klik derefter på fanen **Lydstyrken beholdere** .

2. Vælg objektbeholderen lydstyrken, der indeholder det drev, du vil slette. Klik på objektbeholderen lydstyrken for at få adgang til siden **enheder** .

3. Alle de enheder, der er knyttet til denne objektbeholder vises i et tabelformat. Kontrollere status for det drev, du vil slette. Hvis du vil slette lydstyrken ikke er offline, tage det offline først følge trinnene i [tage en lydstyrken offline](#take-a-volume-offline).

4. Når enheden er offline, kan du klikke på **Slet** nederst på siden.

5. Klik på **Ja**, når du bliver bedt om en bekræftelse. Lydstyrken nu blive slettet og siden **enheder** viser den opdaterede liste over drev inde i beholderen.

    >[AZURE.NOTE]Hvis du sletter et lokalt fastgjorte lydstyrken, kan plads til nye enheder ikke opdateres med det samme. Tjenesten StorSimple Manager opdaterer lokale pladsen med jævne mellemrum. Vi anbefaler, at du venter på et par minutter, før du forsøger at oprette nye lydstyrken.<br> Desuden, hvis du sletter et lokalt fastgjorte lydstyrken og derefter slette en anden lokalt fastgjort lydstyrken med det samme bagefter, lydstyrke sletningen job køres sekventielt. Det første lydstyrke sletningen job skal afsluttes, før det næste lydstyrke sletningen job kan begynde.
 
## <a name="monitor-a-volume"></a>Overvåge en volumenlicens

Lydstyrken overvågning, kan du indsamle jeg/O-relaterede statistikker for et drev. Overvågning er aktiveret som standard for de første 32 enheder, du opretter. Overvågning af flere enheder er deaktiveret som standard. Overvågning af klonede drev er også deaktiveret som standard.

Udfør følgende trin for at aktivere eller deaktivere overvågning for et drev.

#### <a name="to-enable-or-disable-volume-monitoring"></a>Sådan aktiveres eller deaktiveres lydstyrken overvågning

1. Vælg enheden, på siden **enheder** , dobbeltklik på den, og klik derefter på fanen **Lydstyrken beholdere** .

2. Vælg objektbeholderen lydstyrken, hvori lydstyrken er placeret, og klik derefter på objektbeholderen lydstyrken for at få adgang til siden **enheder** .

3. Alle de enheder, der er knyttet til denne objektbeholder vises i visningen tabelformat. Klik på, og vælg lydstyrken eller lydstyrken Klon.

4. Klik på **Rediger**nederst på siden.

5. Vælg **aktivere** eller **deaktivere** fra rullelisten **overvågnings** i guiden Rediger lydstyrken under **Grundlæggende indstillinger**.

## <a name="next-steps"></a>Næste trin

- Lær, hvordan du [Klon en StorSimple lydstyrken](storsimple-clone-volume.md).

- Lær, hvordan du [bruger tjenesten StorSimple Manager for at administrere din StorSimple enhed](storsimple-manager-service-administration.md).

 
