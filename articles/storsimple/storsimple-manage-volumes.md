<properties
   pageTitle="Administrere dine StorSimple drev | Microsoft Azure"
   description="Forklarer, hvordan du kan tilføje, redigere, overvåge og slette StorSimple enheder, og hvordan du kan tage dem offline, hvis det er nødvendigt."
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
   ms.date="05/11/2016"
   ms.author="v-sharos" />

# <a name="use-the-storsimple-manager-service-to-manage-volumes"></a>Bruge tjenesten StorSimple Manager til at administrere enheder

[AZURE.INCLUDE [storsimple-version-selector-manage-volumes](../../includes/storsimple-version-selector-manage-volumes.md)]

## <a name="overview"></a>Oversigt

Dette selvstudium forklarer, hvordan du bruger tjenesten StorSimple Manager til at oprette og administrere enheder på StorSimple enhed og StorSimple virtuelle enhed.

Tjenesten StorSimple Manager er en udvidelse af Azure klassisk portalen, hvor du kan administrere din StorSimple løsning fra en enkelt grænseflade. Ud over administration af enheder, kan du bruge tjenesten StorSimple Manager til at oprette og administrere StorSimple tjenester, få vist og administrere enheder, se beskeder, og få vist og administrere politikker for sikkerhedskopiering og kataloget til sikkerhedskopiering.

> [AZURE.NOTE] Azure StorSimple kan oprette tyndt klargjort enheder. Du kan ikke oprette klargjort fuldt eller delvist klargjort enheder på en Azure StorSimple system.
>
> Tynd klargøring er en virtualization teknologi, hvor tilgængelige lagerplads vises overskrider fysisk ressourcer. I stedet for at reservere tilstrækkelig lagerplads på forhånd bruger Azure StorSimple tynd klargøring til at tildele lige nok plads til at opfylde aktuelle krav. Hvilke elastiske skylagring letter denne metode, fordi Azure StorSimple kan øge eller mindske skylagring at opfylde skiftende behov.

## <a name="the-volumes-page"></a>Siden enheder

Siden **enheder** tillader dig at administrere lagerenheder, der er klargjort på Microsoft Azure StorSimple enheden til din initiatorer (servere). På listen over enheder vises på enheden StorSimple.

 ![Enheder side](./media/storsimple-manage-volumes/HCS_VolumesPage.png)

En enhed består af en række attributter:

- **Navn** – et beskrivende navn, der skal være entydigt og hjælper med at identificere lydstyrken. Dette navn bruges også i overvågning rapporter, når du filtrerer på en bestemt enhed.

- **Status** – kan være online eller offline. Hvis et drev, hvis det er offline, er det er ikke synlige for initiatorer (servere), der har adgang til at bruge lydstyrken.

- **Kapacitet** – angiver, hvor stor lydstyrken er, i gruppens øjne afsenderen (server). Kapacitet angiver den samlede mængde af data, der kan være gemt ved afsenderen (server). Enheder er tyndt klargjort, og data er deduplicated. Det betyder, at enheden allerede ikke allokere fysisk lagerkapacitet, internt eller på skyen ifølge konfigurerede lydstyrken kapacitet. Kapaciteten lydstyrken er allokeret og consumed efter behov.

- **Type** – typen beholdning kan være lagdelte eller arkivering (en undertype af lagdelt)

- **Access** -angiver initiatorer (servere), der har adgang til denne enhed. Initiatorer, der ikke er medlemmer af access kontrolelement post (ACR), der er knyttet til lydstyrken kan ikke se lydstyrken.

- **Overvågnings** -angiver, hvorvidt der skal overvåges en volumenlicens. En enhed har overvågning aktiveret som standard, når den er oprettet. Overvågning er, men, være deaktiveret for en lydstyrken Klon. Følg vejledningen i skærm en enhed for at aktivere overvågning for et drev.

De mest almindelige opgaver, der er knyttet til en lydstyrken er:

- Tilføje en disk
- Ændre en volumenlicens
- Slette en volumenlicens
- Tage en lydstyrken offline
- Overvåge en volumenlicens

## <a name="add-a-volume"></a>Tilføje en disk

Du har [oprettet en volumenlicens](storsimple-deployment-walkthrough-u1.md#step-6-create-a-volume) under installation af din StorSimple løsning. Tilføjer en disk er en lignende procedure.

### <a name="to-add-a-volume"></a>Vil tilføje en disk

1. Vælg enheden, på siden **enheder** , dobbeltklik på den, og klik derefter på fanen **Lydstyrken beholdere** .

2. Vælg en lydstyrken objektbeholder, og klik på pilen i den tilsvarende række til at få adgang til de enheder, der er knyttet til objektbeholderen.

3. Klik på **Tilføj** i bunden af siden. Tilføj en lydstyrken guide starter.

     ![Tilføje lydstyrke guiden grundlæggende indstillinger](./media/storsimple-manage-volumes/AddVolume1.png)

4. I guiden Tilføj et lydstyrken, under **Grundlæggende indstillinger**skal du gøre følgende:

  1. Angiv et **navn** til din enhed.
  2. Angive **Klargjort kapacitet** til din enhed i GB eller TB. Kapaciteten skal være mellem 1 GB og 64 TB til en fysisk enhed. Den maksimale kapacitet, der kan være klargjort til en enhed på en StorSimple virtuel enhed er 30 TB.
  3. Vælg den **Type Brug** til din enhed. Hvis du bruger lagdelte lydstyrken til arkivering data, ændrer Hvis du markerer afkrydsningsfeltet **Brug denne lydstyrken for mindre hyppigt arkivering data** størrelsen på deduplication afsnit til din enhed til 512 KB. Hvis du ikke vælger denne indstilling, bruge tilsvarende lagdelte lydstyrken en del udgave af 64 KB. En del større deduplication kan kameraet, for at fremskynde overførsel af store arkivering data til skyen. (Lagdelte enheder var tidligere kaldet primære enheder.)
  5. Tryk på pileikonet ![pileikonet](./media/storsimple-manage-volumes/HCS_ArrowIcon.png)at gå til siden **Yderligere indstillinger** .

        ![Tilføje lydstyrke guiden yderligere indstillinger](./media/storsimple-manage-volumes/AddVolume2.png)

5. Tilføje en ny access kontrolelement post (ACR) under **Yderligere indstillinger**:

  1. Vælg en access kontrolelement post (ACR) på rullelisten. Du kan også tilføje en ny ACR. ACRs bestemmer, hvilke værter har adgang til dine drev ved at sammenligne host IQN med, vises i posten.
  2. Vi anbefaler, at du aktiverer en standard-sikkerhedskopi ved at markere afkrydsningsfeltet **Aktiver en sikkerhedskopi af standard for denne enhed** .
   3. Klik på ikonet Kontrollér ![Ikonet](./media/storsimple-manage-volumes/HCS_CheckIcon.png) for at oprette lydstyrken med de angivne indstillinger.

Ny lydstyrken er nu klar til brug.

## <a name="modify-a-volume"></a>Ændre en volumenlicens

Redigere en enhed, når du har brug at udvide den eller ændre de værter, der få adgang til lydstyrken.

> [AZURE.IMPORTANT]
>
> - Hvis du ændrer størrelsen på enheden, skal ændres på værten samt enhedens størrelse.
> - Host side trinnene beskrevet her er til Windows Server 2012 (2012R2). Procedurerne for Linux eller andre host operativsystemer vil være forskellige. Henvise til dine host operativsystem instruktioner, når du ændrer lydstyrken på en vært, der kører et andet operativsystem.

### <a name="to-modify-a-volume"></a>Ændre en volumenlicens

1. Vælg enheden, på siden **enheder** , dobbeltklik på den, og klik derefter på fanen **Lydstyrken objektbeholder** . Denne side vises i et tabelformat alle lydstyrken beholdere, der er knyttet til enheden.

2. Vælg en objektbeholder lydstyrken og klikke på den for at få vist listen over alle enheder inde i beholderen.

3. På siden **enheder** skal du vælge en enhed og klikke på **Rediger**.

4. I guiden Rediger lydstyrken under **Grundlæggende indstillinger**kan du gøre følgende:

  - Redigere **navn** og **Type** , hvis du vil ændre en lagdelte lydstyrken til en arkivering enhed ved at markere afkrydsningsfeltet **Brug denne lydstyrken for mindre hyppigt arkivering data** for at ændre størrelsen på deduplication afsnit til lydstyrken til 512 KB.
  - Øge **klargjort kapacitet**. **Klargjort kapacitet** kan kun øges. Du kan ikke formindske en volumenlicens, når den er oprettet.

    > [AZURE.NOTE] Du kan ikke ændre objektbeholderen lydstyrken, når den er tildelt til en disk.

5. Under **Yderligere indstillinger**, kan du gøre følgende:

  - Ændre ACRs, forudsat at lydstyrken er offline. Hvis enheden er online, skal du tage det offline først. Referere til trinnene i [tage en lydstyrken offline](#take-a-volume-offline) før at ændre ACR.
  - Redigere listen over ACRs, efter at lydstyrken er offline.

    > [AZURE.NOTE] Du kan ikke ændre indstillingen **Aktiver en sikkerhedskopi af standard for denne enhed** for lydstyrken.

6. Gem dine ændringer ved at klikke på ikonet Kontrollér ![Kontrollér-ikon](./media/storsimple-manage-volumes/HCS_CheckIcon.png). Azure klassisk portalen, vises meddelelsen opdaterer lydstyrken. Det vises en meddelelse, når lydstyrken er blevet opdateret.

7. Hvis du udvide en disk, kan du benytte følgende fremgangsmåde på din Windows host computer:

   1. Gå til **Computer Management** ->**Disk Management**.
   2. Højreklik på **Diskadministration** , og vælg **Scan diske**.
   3. Vælg på listen over diske lydstyrken, du har opdateret, højreklikke og derefter vælge **Udvide lydstyrken**. Guiden udvide lydstyrken starter. Klik på **Næste**.
   4. Fuldføre guiden, acceptere standardværdierne. Når guiden er afsluttet, bør lydstyrken vise øget størrelse.

![Video er til rådighed](./media/storsimple-manage-volumes/Video_icon.png) **Video, der er tilgængelige**

Hvis du vil se en video, der viser, hvordan du kan udvide en enhed, skal du klikke på [her](https://azure.microsoft.com/documentation/videos/expand-a-storsimple-volume/).

## <a name="take-a-volume-offline"></a>Tage en lydstyrken offline

Du muligvis tage en lydstyrken offline, når du planlægger at redigere eller slette den. Når en enhed er offline, er det er ikke tilgængelig for læse / skrive-adgang. Du skal tage lydstyrken offline på værten og på enheden. Udfør følgende trin for at gøre en lydstyrken offline.

### <a name="to-take-a-volume-offline"></a>Tage en lydstyrken offline

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

### <a name="to-delete-a-volume"></a>Slette en volumenlicens

1. Vælg enheden, på siden **enheder** , dobbeltklik på den, og klik derefter på fanen **Lydstyrken beholdere** .

2. Vælg objektbeholderen lydstyrken, der indeholder det drev, du vil slette. Klik på objektbeholderen lydstyrken for at få adgang til siden **enheder** .

3. Alle de enheder, der er knyttet til denne objektbeholder vises i et tabelformat. Kontrollere status for det drev, du vil slette. Hvis du vil slette lydstyrken ikke er offline, tage det offline først følge trinnene i [tage en lydstyrken offline](#take-a-volume-offline).

4. Når enheden er offline, kan du klikke på **Slet** nederst på siden.

5. Klik på **Ja**, når du bliver bedt om en bekræftelse. Lydstyrken nu blive slettet og siden **enheder** viser den opdaterede liste over drev inde i beholderen.

## <a name="monitor-a-volume"></a>Overvåge en volumenlicens

Lydstyrken overvågning, kan du indsamle jeg/O-relaterede statistikker for et drev. Overvågning er aktiveret som standard for de første 32 enheder, du opretter. Overvågning af flere enheder er deaktiveret som standard. Overvågning af klonede drev er også deaktiveret som standard.

Udfør følgende trin for at aktivere eller deaktivere overvågning for et drev.

### <a name="to-enable-or-disable-volume-monitoring"></a>Sådan aktiveres eller deaktiveres lydstyrken overvågning

1. Vælg enheden, på siden **enheder** , dobbeltklik på den, og klik derefter på fanen **Lydstyrken beholdere** .

2. Vælg objektbeholderen lydstyrken, hvori lydstyrken er placeret, og klik derefter på objektbeholderen lydstyrken for at få adgang til siden **enheder** .

3. Alle de enheder, der er knyttet til denne objektbeholder vises i visningen tabelformat. Klik på, og vælg lydstyrken eller lydstyrken Klon.

4. Klik på **Rediger**nederst på siden.

5. Vælg **aktivere** eller **deaktivere** fra rullelisten **overvågnings** i guiden Rediger lydstyrken under **Grundlæggende indstillinger**.

    ![Ændre lydstyrken grundlæggende indstillinger](./media/storsimple-manage-volumes/HCS_MonitorVolumeM.png)


## <a name="next-steps"></a>Næste trin

- Lær, hvordan du [Klon en StorSimple lydstyrken](storsimple-clone-volume.md).

- Lær, hvordan du [bruger tjenesten StorSimple Manager for at administrere din StorSimple enhed](storsimple-manager-service-administration.md).
