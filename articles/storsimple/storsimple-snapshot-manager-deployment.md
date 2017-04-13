<properties 
   pageTitle="Implementere StorSimple øjebliksbillede Manager | Microsoft Azure"
   description="Lær at downloade og installere StorSimple øjebliksbillede Manager en MMC snap-in til administration af StorSimple data beskyttelse og sikkerhedskopiering af funktioner."
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
   ms.date="05/24/2016"
   ms.author="v-sharos" />

# <a name="deploy-the-storsimple-snapshot-manager-mmc-snap-in"></a>Installere snap-in StorSimple øjebliksbillede Manager MMC

## <a name="overview"></a>Oversigt

StorSimple øjebliksbillede Manager er en snap-in til Microsoft Management Console (MMC), der forenkler databeskyttelse og sikkerhedskopiering administration i et Microsoft Azure StorSimple miljø. Med StorSimple øjebliksbillede Manager, som du kan administrere Microsoft Azure StorSimple i det lokale miljø og skyen lagerplads, som om det var et fuldt integreret storage-system, således at forenkle sikkerhedskopierings- og gendannelsesprocesser og reducere omkostninger. 

I dette selvstudium beskrives konfigurationskrav samt procedurerne for at installere, fjerne og opgradering af StorSimple øjebliksbillede Manager.

>[AZURE.NOTE] 
>
>- Du kan ikke bruge StorSimple øjebliksbillede Manager til at administrere Microsoft Azure StorSimple virtuelle matrixer (også kaldet StorSimple lokale virtuelle enheder).
>
>- Hvis du planlægger at installere StorSimple opdatering 2 på enheden StorSimple, skal du sørge for at hente den nyeste version af StorSimple øjebliksbillede Manager og installere den, **før du installerer StorSimple opdatering 2**. Den seneste version af StorSimple øjebliksbillede Manager er bagudkompatible og arbejder med alle frigivne versioner af Microsoft Azure StorSimple. Hvis du bruger den tidligere version af StorSimple øjebliksbillede Manager, skal du opdatere den (du ikke behøver at fjerne den tidligere version, før du installerer den nye version).

## <a name="storsimple-snapshot-manager-installation"></a>Installationen StorSimple øjebliksbillede Manager

StorSimple øjebliksbillede Manager kan installeres på computere, der kører Windows Server 2008 R2 SP1, Windows Server 2012 eller Windows Server 2012 R2-operativsystemet. På servere, der kører Windows 2008 R2, skal du også installere Windows Server 2008 SP1 og Windows Management Framework 3.0. 

Før du installerer eller opgraderer snap-in StorSimple øjebliksbillede Manager til Microsoft Management Console (MMC), skal du kontrollere, at Microsoft Azure StorSimple enhed og host serveren er konfigureret korrekt. 

## <a name="configure-prerequisites"></a>Konfigurere forudsætninger

Følgende fremgangsmåde giver en overordnet oversigt over konfigurationsopgaver, du skal fuldføre, før du installerer StorSimple øjebliksbillede Manager. Komplette konfiguration af Microsoft Azure StorSimple og konfigurationsoplysninger om, herunder systemkrav og en trinvis vejledning under [Implementer enheden lokale StorSimple](storsimple-deployment-walkthrough.md).

>[AZURE.IMPORTANT]Før du begynder, skal du gennemse [Tjekliste for konfiguration af installation](storsimple-deployment-walkthrough.md#deployment-configuration-checklist) og og [forudsætninger for installation](storsimple-deployment-walkthrough.md#deployment-prerequisites) i [Implementer enheden lokale StorSimple](storsimple-deployment-walkthrough.md).
> <br>
 
### <a name="before-you-install-storsimple-snapshot-manager"></a>Før du installerer StorSimple øjebliksbillede Manager

1. Pak, tilslutte og oprette forbindelse til Microsoft Azure StorSimple enheden, som beskrevet i [installere enheden StorSimple 8100](storsimple-8100-hardware-installation.md) eller [installere enheden StorSimple 8600](storsimple-8600-hardware-installation.md).

2. Sørge for, at computeren host kører en af følgende operativsystemer:

    - Windows Server 2008 R2 (til servere, der kører Windows 2008 R2, skal du også installere Windows Server 2008 SP1 og Windows Management Framework 3.0)
    - Windows Server 2012
    - Windows Server 2012 R2
 
    En StorSimple virtuel enhed være værten for en Microsoft Azure virtuel maskine. 

3. Sørg for, at du opfylder alle Microsoft Azure StorSimple konfigurationskrav. Få mere at vide ved at gå til [forudsætninger for installation](storsimple-deployment-walkthrough.md#deployment-prerequisites).

4. Tilslut enheden til værten og udføre den indledende konfiguration. Få mere at vide ved at gå til [installation trin](storsimple-deployment-walkthrough.md#deployment-steps).

5. Oprette enheder på enheden, tildele dem til værten, og Bekræft, at værten kan tilslutte og bruge dem. StorSimple øjebliksbillede Manager understøtter følgende typer enheder: 

    - Grundlæggende enheder
    - Simple enheder
    - Dynamiske enheder
    - Spejlede dynamiske enheder (RAID 1)
    - Klynge-delte enheder
 
    Oplysninger om oprettelse af enheder på den StorSimple enhed eller StorSimple virtuelle enhed, gå til [trin 6: oprette en lydstyrken](storsimple-deployment-walkthrough.md#step-6-create-a-volume), i [Implementer enheden lokale StorSimple](storsimple-deployment-walkthrough.md).

## <a name="install-a-new-storsimple-snapshot-manager"></a>Installere en ny StorSimple øjebliksbillede Manager

Før du installerer StorSimple af snapshots, Sørg for, at enhederne, du oprettede på StorSimple enheden eller StorSimple virtuel enhed er aktiveret, initialiseret, og der er formateret som beskrevet i [konfigurere forudsætninger](#configure-prerequisites).

>[AZURE.IMPORTANT]
>
>- En StorSimple virtuel enhed være værten for en Microsoft Azure virtuel maskine. 
>
>- Værten skal køre Windows 2008 R2, Windows Server 2012 eller Windows Server 2012 R2. Hvis din server, der kører Windows Server 2008 R2, skal du også installere Windows Server 2008 SP1 og Windows Management Framework 3.0.
>
>- Du skal konfigurere en iSCSI-forbindelse fra værten til StorSimple enheden, før du kan oprette forbindelse til enheden til StorSimple øjebliksbillede Manager.

Følg disse trin for at fuldføre en ny installation af StorSimple øjebliksbillede Manager. Hvis du installerer en opgradering, skal du gå til [opgradere eller geninstallere StorSimple øjebliksbillede Manager](#upgrade-or-reinstall-storsimple-snapshot-manager).

- Trin 1: Installere StorSimple øjebliksbillede Manager 
- Trin 2: Forbind StorSimple øjebliksbillede Manager med en enhed 
- Trin 3: Kontrol forbindelsen til enheden 

###<a name="step-1-install-storsimple-snapshot-manager"></a>Trin 1: Installere StorSimple øjebliksbillede Manager

Brug følgende trin til at installere StorSimple øjebliksbillede Manager.

#### <a name="to-install-storsimple-snapshot-manager"></a>Installere StorSimple øjebliksbillede Manager

1. Hente StorSimple øjebliksbillede Manager-softwaren (gå til [StorSimple øjebliksbillede Manager](https://www.microsoft.com/download/details.aspx?id=44220) i Microsoft Download Center) og gemme den lokalt på værten.

2. Åbn Stifinder, højreklik på den komprimerede mappe, og klik **udtrække alle**.

3. Skriv eller gå til den sti, hvor du vil have til en fil skal udtrækkes i vinduet **udtrække komprimerede (Zipped) mapper** i dialogboksen **Vælg en destination og udtrække filer** . 

       >[AZURE.IMPORTANT] Du skal installere StorSimple øjebliksbillede Manager på C:-drevet.
 
4. Markér afkrydsningsfeltet **Vis filerne, når du er færdig** , og klik derefter på **Udpak**.

    ![Udtrække dialogboksen filer](./media/storsimple-snapshot-manager-deployment/HCS_SSM_extract_files.png) 

4. Når filerne er fuldført, åbnes destinationsmappen. Dobbeltklik på ikonet programmet konfiguration, der vises i destinationsmappen.

5. Når **Setup Successful** meddelelsen vises, skal du klikke på **Luk**. Du bør se StorSimple øjebliksbillede Manager-ikonet på skrivebordet.

    ![ikonet på skrivebordet](./media/storsimple-snapshot-manager-deployment/HCS_SSM_desktop_icon.png) 

### <a name="step-2-connect-storsimple-snapshot-manager-to-a-device"></a>Trin 2: Forbind StorSimple øjebliksbillede Manager med en enhed

Følge nedenstående trin for at oprette forbindelse StorSimple øjebliksbillede Manager til en StorSimple enhed.

#### <a name="to-connect-storsimple-snapshot-manager-to-a-device"></a>Oprette forbindelse StorSimple øjebliksbillede Manager til en enhed

1. Klik på ikonet StorSimple øjebliksbillede Manager på din computer. Vinduet StorSimple øjebliksbillede Manager åbnes. Vinduet indeholder en rude med **omfang** , en ruden med **Søgeresultater** og en ruden **Handlinger** . 

    ![StorSimple øjebliksbillede Manager-brugergrænseflade](./media/storsimple-snapshot-manager-deployment/HCS_SSM_gui_panes.png) 

    - Ruden **område** (i venstre rude) indeholder en liste over noder organiseret i en træstruktur. Du kan udvide nogle noder for at vælge en visning eller bestemte data, der er relateret til denne node. Klik på pilen for at udvide eller skjule en node. Højreklik på et element i ruden **omfang** vil se en liste over tilgængelige handlinger for det pågældende element. 

    - Ruden **resultater** (den midterste rude) indeholder detaljerede oplysninger om node, visning eller data, du har valgt i ruden **omfang** .

    - Ruden **Handlinger** viser en liste over de handlinger, du kan udføre på node, visning eller data, du har valgt i ruden **omfang** .

    Du kan finde en fuldstændig beskrivelse af brugergrænsefladen StorSimple øjebliksbillede Manager [StorSimple øjebliksbillede Manager-brugergrænseflade](storsimple-use-snapshot-manager.md).

2. Højreklik på noden **enheder** i ruden **omfang** , og klik derefter på **Konfigurer en enhed**. Dialogboksen **Konfigurer en enhed** vises.

    ![Konfigurere en enhed](./media/storsimple-snapshot-manager-deployment/HCS_SSM_config_device.png) 

3. Vælg IP-adressen på Microsoft Azure StorSimple enheden eller virtuel enhed på listen **enhed** . I tekstfeltet **adgangskode** skal du skrive adgangskoden StorSimple af snapshots, du har oprettet for enheden i portalen Azure klassisk. Klik på **OK**.

4. StorSimple øjebliksbillede Manager søger efter den enhed, du har identificeret. Hvis enheden er tilgængelig, tilføjer StorSimple øjebliksbillede Manager en forbindelse. Du kan [bekræfte forbindelsen til enheden](#to-verify-the-connection) for at bekræfte, at forbindelsen blev tilføjet.

    Hvis enheden er ikke tilgængelig for en eller anden grund, returnerer StorSimple øjebliksbillede Manager en fejlmeddelelse. Klik på **OK** for at lukke fejlmeddelelsen, og klik derefter på **Annuller** for at lukke dialogboksen **Konfigurer en enhed** .

5. Når der oprettes forbindelse til en enhed, importerer StorSimple øjebliksbillede Manager hver lydstyrken gruppe, der er konfigureret til denne enhed, forudsat at gruppen lydstyrken er knyttet sikkerhedskopier. Lydstyrke-grupper, der ikke har tilknyttede sikkerhedskopier importeres ikke. Desuden importeres sikkerhedskopiering politikker, der er oprettet for en gruppe af lydstyrken ikke. For at se de importerede grupper, skal du højreklikke på den øverste **Lydstyrken grupper** node i ruden **område** , og klik på **Slå importeret grupper**.

### <a name="step-3-verify-the-connection-to-the-device"></a>Trin 3: Kontrol forbindelsen til enheden

Brug følgende trin til at bekræfte, at StorSimple øjebliksbillede Manager er forbundet med StorSimple enheden.

#### <a name="to-verify-the-connection"></a>At bekræfte forbindelsen

1. Klik på noden **enheder** i ruden **område** .

    ![StorSimple øjebliksbillede Manager Enhedsstatus](./media/storsimple-snapshot-manager-deployment/HCS_SSM_Device_status.png) 

2. Kontrollere ruden **resultater** : 

   - Hvis der vises en grøn indikator på enhedsikonet og **tilgængelige** vises i kolonnen **Status** , er derefter enheden forbundet. 

   - Hvis en rød indikator vises på enhedsikonet og ikke tilgængelig i kolonnen **Status** , er enheden ikke forbundet. 

   - Hvis **opdatering af** vises i kolonnen **Status** , StorSimple øjebliksbillede Manager henter lydstyrken grupper og tilknyttede sikkerhedskopier til en forbundet enhed.

## <a name="upgrade-or-reinstall-storsimple-snapshot-manager"></a>Opgradere eller geninstallere StorSimple øjebliksbillede Manager

Du skal fjerne StorSimple øjebliksbillede Manager helt, før du opgraderer eller geninstallere softwaren. 

Før du geninstallerer StorSimple øjebliksbillede Manager, skal du sikkerhedskopiere den eksisterende StorSimple øjebliksbillede Manager-database på computeren, host. Dette gemmer oplysningerne om sikkerhedskopiering politikker og konfiguration, så du let kan gendanne disse data fra sikkerhedskopi.

Følg disse trin, hvis du opgraderer eller geninstallere StorSimple øjebliksbillede Manager:

- Trin 1: Fjern StorSimple øjebliksbillede Manager 
- Trin 2: Sikkerhedskopiere den StorSimple øjebliksbillede Manager-database 
- Trin 3: Geninstallere StorSimple øjebliksbillede Manager og gendanne databasen 

### <a name="step-1-uninstall-storsimple-snapshot-manager"></a>Trin 1: Fjern StorSimple øjebliksbillede Manager

Brug følgende trin til at fjerne StorSimple øjebliksbillede Manager.

#### <a name="to-uninstall-storsimple-snapshot-manager"></a>Fjerne StorSimple øjebliksbillede Manager

1. På computeren, Åbn **Kontrolpanel**, skal du klikke på **programmer**og derefter klikke på **programmer og funktioner**.

2. Klik på **Fjern eller rediger et program**i venstre rude.

3. Højreklik på **StorSimple øjebliksbillede Manager**, og klik derefter på **Fjern**.

4. Dette starter StorSimple øjebliksbillede Manager installationsprogrammet. Klik på **Rediger indstillinger**, og klik derefter på **Fjern**.

    >[AZURE.NOTE] Hvis der er en hvilken som helst MMC processer, der kører i baggrunden, som StorSimple øjebliksbillede Manager eller Disk Management afinstallationen mislykkes, og du modtager en meddelelse til Luk alle forekomster af MMC, før du forsøger at fjerne programmet. Vælg **automatisk lukke programmer, og forsøg at genstarte dem når installationen er fuldført**, og klik derefter på **OK**.
 
5. Når fjernelsen er fuldført, vises meddelelsen **Setup Successful** . Klik på **Luk**.

### <a name="step-2-back-up-the-storsimple-snapshot-manager-database"></a>Trin 2: Sikkerhedskopiere den StorSimple øjebliksbillede Manager-database

Brug følgende trin til at oprette og gemme en kopi af den StorSimple øjebliksbillede Manager-database.

#### <a name="to-back-up-the-database"></a>Sikkerhedskopiere databasen

1. Stop Microsoft StorSimple Management-tjenesten:

   1. Start Server Manager.

   2. Vælg **tjenester**på Server Manager Dashboard i menuen **Funktioner** .

   3. På siden **Services** skal du vælge **Microsoft StorSimple Management-tjenesten**.

   4. Klik på **Stop tjenesten**under **Microsoft StorSimple Management-tjenesten**, i højre rude.

        ![Stoppe tjenesten StorSimple Manager](./media/storsimple-snapshot-manager-deployment/HCS_SSM_stop_service.png)

2. Gå til C:\ProgramData\Microsoft\StorSimple\BACatalog. 

    >[AZURE.NOTE] ProgramData er en skjult mappe.

3. Find fil, XML-katalog, kopiere filen og gemme kopien i et sikkert sted eller i skyen.

    ![StorSimple kataloget sikkerhedskopiering fil](./media/storsimple-snapshot-manager-deployment/HCS_SSM_bacatalog.png)

4. Genstart Microsoft StorSimple Management-tjenesten: 

    1. Vælg **tjenester**på Server Manager Dashboard i menuen **Funktioner** .

    2. På siden **Services** skal du vælge **Microsoft StorSimple Management Servic**e.

    3. Klik på **Genstart tjenesten**under **Microsoft StorSimple Management-tjenesten**, i højre rude. 

### <a name="step-3-reinstall-storsimple-snapshot-manager-and-restore-the-database"></a>Trin 3: Geninstallere StorSimple øjebliksbillede Manager og gendanne databasen

For at geninstallere StorSimple øjebliksbillede Manager skal du følge trinnene i [installere en ny StorSimple øjebliksbillede Manager](#install-a-new-storsimple-snapshot-manager). Derefter skal du bruge følgende procedure til at gendanne StorSimple øjebliksbillede Manager-database.

#### <a name="to-restore-the-database"></a>Gendanne databasen

1. Stop Microsoft StorSimple Management-tjenesten:

    1. Start Server Manager.

    2. Vælg **tjenester**på Server Manager Dashboard i menuen **Funktioner** .

    3. På siden **Services** skal du vælge **Microsoft StorSimple Management-tjenesten**.

    4. Klik på **Stop tjenesten**under **Microsoft StorSimple Management-tjenesten**, i højre rude.

2. Gå til C:\ProgramData\Microsoft\StorSimple\BACatalog. 

     >[AZURE.NOTE] ProgramData er en skjult mappe.

3. Slet katalog XML-filen, og erstatte den med den version, du gemte tidligere.

4. Genstart Microsoft StorSimple Management-tjenesten: 

    1. Vælg **tjenester**på Server Manager Dashboard i menuen **Funktioner** .

    2. På siden **Services** skal du vælge **Microsoft StorSimple Management-tjenesten**.

    3. Klik på **Genstart tjenesten**under **Microsoft StorSimple Management-tjenesten**, i højre rude.

## <a name="next-steps"></a>Næste trin

- Hvis du vil vide mere om StorSimple øjebliksbillede Manager, skal du gå til [Hvad er StorSimple øjebliksbillede Manager?](storsimple-what-is-snapshot-manager.md).

- Hvis du vil vide mere om brugergrænsefladen StorSimple øjebliksbillede Manager skal du gå til [StorSimple øjebliksbillede Manager-brugergrænseflade](storsimple-use-snapshot-manager.md).

- Hvis du vil vide mere om at bruge StorSimple øjebliksbillede Manager, skal du gå til [Brug StorSimple øjebliksbillede Manager til at administrere din StorSimple løsning](storsimple-snapshot-manager-admin.md).
