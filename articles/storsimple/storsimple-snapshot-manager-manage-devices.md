<properties 
   pageTitle="Administrere enheder med StorSimple øjebliksbillede Manager | Microsoft Azure"
   description="Beskriver, hvordan du bruger StorSimple øjebliksbillede Manager MMC snap-in'en til at oprette forbindelse og administrere StorSimple enheder."
   services="storsimple"
   documentationCenter=""
   authors="SharS"
   manager="carmonm"
   editor="" />
<tags 
   ms.service="storsimple"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="04/18/2016"
   ms.author="v-sharos" />

# <a name="use-storsimple-snapshot-manager-to-connect-and-manage-storsimple-devices"></a>Brug StorSimple øjebliksbillede Manager til at oprette forbindelse og administrere StorSimple enheder

## <a name="overview"></a>Oversigt

Du kan bruge noder i ruden StorSimple øjebliksbillede Manager **omfang** at kontrollere importerede StorSimple enhedsdata og opdatere forbundne lagerenheder. Når du klikker på noden **enheder** , kan du også se en liste over tilsluttede enheder og tilsvarende statusoplysninger i ruden **resultater** .

![Forbundne enheder](./media/storsimple-snapshot-manager-manage-devices/HCS_SSM_connect_devices.png)

**Figur 1: StorSimple øjebliksbillede Manager forbundne enhed** 

Afhængigt af dine valg af **visning** viser ruden **resultater** følgende oplysninger om hver enhed. (Du kan finde flere oplysninger om konfiguration af en visning, gå til [menuen Vis](storsimple-use-snapshot-manager.md#view-menu).


| Resultater kolonne  |Beskrivelse          |
|:----------------|:--------------------| 
| Navn            | Navnet på enheden, der er konfigureret i portalen Azure klassisk|
| Model           | Model antallet af enheden|
| Version         | Versionen af programmet installeret på enheden |
| Status          | Om enheden er tilgængelig |
| Sidst synkroniseret     | Dato og klokkeslæt, når enheden sidst blev synkroniseret |
| Serienr.      | Serienummeret for enheden |
 
Hvis du højreklikker på noden **enheder** i ruden **område** , kan du vælge mellem følgende handlinger:

- Tilføje eller udskifte en enhed 
- Oprette forbindelse til en enhed og kontrollere import 
- Opdatere forbundne enheder 

Hvis du klikker på noden **enheder** og derefter skal du højreklikke på navnet på en enhed i ruden **resultater** , kan du vælge mellem følgende handlinger:

- Godkende en enhed 
- Se detaljer om enhed 
- Opdatere en enhed 
- Slette en enhedskonfiguration 
- Ændre en adgangskode til enhed

>[AZURE.NOTE] Alle disse handlinger er også tilgængelige i ruden **Handlinger** .
 
Dette selvstudium beskrives, hvordan du bruger StorSimple øjebliksbillede Manager til at oprette forbindelse og administrere enheder og udføre følgende opgaver:

- Tilføje eller udskifte en enhed 
- Oprette forbindelse til en enhed og kontrollere import 
- Opdatere forbundne enheder 
- Godkende en enhed 
- Se detaljer om enhed 
- Opdatere en enkelt enhed 
- Slette en enhedskonfiguration 
- Ændre en adgangskode til udløbet enhed
- Erstatte en mislykkedes enhed

>[AZURE.NOTE] Gå til [StorSimple øjebliksbillede Manager brugergrænsefladen](storsimple-use-snapshot-manager.md)for generelle oplysninger om brug af grænsefladen StorSimple øjebliksbillede Manager.


## <a name="add-or-replace-a-device"></a>Tilføje eller udskifte en enhed

Brug følgende fremgangsmåde til at tilføje eller udskifte en StorSimple enhed.

#### <a name="to-add-or-replace-a-device"></a>Tilføje eller erstatte en enhed

1. Klik på ikonet Skrivebord for at starte StorSimple øjebliksbillede Manager.

2. Højreklik på noden **enheder** i ruden **omfang** , og klik derefter på **Konfigurer en enhed**. Dialogboksen **Konfigurer en enhed** vises.

    ![Konfigurere en StorSimple-enhed](./media/storsimple-snapshot-manager-manage-devices/HCS_SSM_config_device.png) 

3. Vælg IP-adressen på den enhed eller virtuelle enhed i rullelisten **enhed** . 

4. I tekstfeltet **adgangskode** skal du skrive adgangskoden StorSimple af snapshots, du har oprettet for enheden i portalen Azure klassisk. Klik på **OK**. StorSimple øjebliksbillede Manager søger efter den enhed, du har identificeret. 

    - Hvis enheden er tilgængelig, tilføjer StorSimple øjebliksbillede Manager en forbindelse. 

    - Hvis enheden er ikke tilgængelig for en eller anden grund, returnerer StorSimple øjebliksbillede Manager en fejlmeddelelse. Klik på **OK** for at lukke fejlmeddelelsen, og klik derefter på **Annuller** for at lukke dialogboksen **Konfigurer en enhed** .

## <a name="connect-a-device-and-verify-imports"></a>Oprette forbindelse til en enhed og kontrollere import

Brug følgende fremgangsmåde til at oprette forbindelse en StorSimple enhed og kontrollere, at alle eksisterende lydstyrken grupper, der har tilknyttede sikkerhedskopier er importeret.

#### <a name="to-connect-a-device-and-verify-imports"></a>Kan du forbinde en enhed og bekræfte importerer

1. Tilslutte en enhed med StorSimple øjebliksbillede Manager, skal du følge vejledningen i tilføje eller erstatte en enhed. Når der oprettes forbindelse til en enhed, svarer StorSimple øjebliksbillede Manager på følgende måde:

    - Hvis enheden er ikke tilgængelig for en eller anden grund, returnerer StorSimple øjebliksbillede Manager en fejlmeddelelse. 

   - Hvis enheden er tilgængelig, tilføjer StorSimple øjebliksbillede Manager en forbindelse. Når du vælger enheden, vises den i ruden **resultater** og statusfeltet angiver, at er enheden **tilgængelig**. StorSimple øjebliksbillede Manager importerer en hvilken som helst lydstyrken grupper, der er konfigureret til enheden, forudsat at grupperne lydstyrken har tilknyttede sikkerhedskopier. Sikkerhedskopiering politikker importeres ikke. Lydstyrke-grupper, der ikke har tilknyttede sikkerhedskopier importeres ikke.

2. Klik på ikonet Skrivebord for at starte StorSimple øjebliksbillede Manager.

3. Højreklik på den øverste knude i ruden **område** , og klik derefter på **Skift mellem importerer visning**.

    ![Vælg Skift mellem importerer visning](./media/storsimple-snapshot-manager-manage-devices/HCS_SSM_Toggle_Imports_Display.png) 

4. Dialogboksen **Skift mellem importerer visning** vises med status for de importerede lydstyrken grupper og sikkerhedskopier. Klik på **OK**. 

Når lydstyrken grupper og sikkerhedskopier er importeret, kan du bruge StorSimple øjebliksbillede Manager til at administrere dem på samme måde som du vil administrere lydstyrken grupper og sikkerhedskopier, som du har oprettet og konfigureret med StorSimple øjebliksbillede Manager. 

## <a name="refresh-connected-devices"></a>Opdatere forbundne enheder

Benyt følgende fremgangsmåde til at synkronisere de tilsluttede StorSimple enheder med StorSimple øjebliksbillede Manager.

####<a name="to-refresh-connected-devices"></a>Opdatere forbundne enheder

1. Klik på ikonet Skrivebord for at starte StorSimple øjebliksbillede Manager.

2. Højreklik på **enheder**i ruden **omfang** , og klik derefter på **Opdater enheder**. Dette synkroniserer de tilsluttede enheder med StorSimple øjebliksbillede Manager, så du kan få vist de lydstyrken grupper og sikkerhedskopier, herunder eventuelle seneste tilføjelser. 

    ![Opdatere StorSimple enhederne](./media/storsimple-snapshot-manager-manage-devices/HCS_SSM_Refresh_devices.png)
 
Handlingen **Opdater enheder** henter alle nye lydstyrken grupper og alle tilknyttede sikkerhedskopier fra forbundne enheder. I modsætning til **Scan enheder** handlingen tilgængelig for noden **enheder** gendanner **Opdatere enheder** ikke sikkerhedskopiering registreringsdatabasen.

## <a name="authenticate-a-device"></a>Godkende en enhed

Brug følgende fremgangsmåde til at godkende en StorSimple enhed med StorSimple øjebliksbillede Manager.

#### <a name="to-authenticate-a-device"></a>Til at godkende en enhed

1. Klik på ikonet Skrivebord for at starte StorSimple øjebliksbillede Manager.

2. Klik på **enheder**i ruden **område** .

3. Højreklik på navnet på enheden, og klik derefter på **Godkend**i ruden **resultater** .

4. Dialogboksen **Godkend** vises. Skriv adgangskoden, enhed, og klik derefter på **OK**.

    ![Godkende i dialogboksen](./media/storsimple-snapshot-manager-manage-devices/HCS_SSM_Authenticate.png) 
 
## <a name="view-device-details"></a>Se detaljer om enhed

Benyt følgende fremgangsmåde til at se detaljerne for en enhed med StorSimple og, hvis det er nødvendigt, kan du synkronisere enheden med StorSimple øjebliksbillede Manager.

#### <a name="to-view-and-resynchronize-device-details"></a>Få vist og synkronisere detaljer for enhed

1. Klik på ikonet Skrivebord for at starte StorSimple øjebliksbillede Manager.

2. Klik på **enheder**i ruden **område** .

3. Højreklik på navnet på enheden, og klik derefter på **Detaljer**i ruden **resultater** . 

4. siden **Detaljer for enhed** dialogboksen vises. Dette felt viser navn, model, version, serienummer, status, target iSCSI kvalificerede navn (IQN), og sidste synkroniseringsdato og klokkeslæt. 

   - Klik på **synkroniseres igen,** Hvis du vil synkronisere enheden.

   - Klik på **OK** eller **Annuller** for at lukke dialogboksen.

    ![Detaljer for enhed](./media/storsimple-snapshot-manager-manage-devices/HCS_SSM_Device_details.png) 
 
## <a name="refresh-an-individual-device"></a>Opdatere en enkelt enhed

Brug følgende fremgangsmåde til at synkronisere en individuel StorSimple enhed med StorSimple øjebliksbillede Manager.

#### <a name="to-refresh-a-device"></a>Opdatere en enhed

1. Klik på ikonet Skrivebord for at starte StorSimple øjebliksbillede Manager. 

2. Klik på **enheder**i ruden **område** . 

3. Højreklik på navnet på enheden, og klik derefter på **Opdater enhed**i ruden **resultater** . Dette synkroniseres enheden med StorSimple øjebliksbillede Manager. 

## <a name="delete-a-device-configuration"></a>Slette en enhedskonfiguration

Brug følgende fremgangsmåde til at slette en enkelt StorSimple enhedskonfiguration fra StorSimple øjebliksbillede Manager.

#### <a name="to-delete-a-device-configuration"></a>Slette en enhedskonfiguration

1. Klik på ikonet Skrivebord for at starte StorSimple øjebliksbillede Manager. 

2. Klik på **enheder**i ruden **område** . 

3. Højreklik på navnet på enheden, og klik derefter på **Slet**i ruden **resultater** . 

4. Følgende meddelelse vises. Klik på **Ja** for at slette konfigurationen, eller klik på **Nej** for at annullere sletningen.

    ![Slet enhedskonfiguration](./media/storsimple-snapshot-manager-manage-devices/HCS_SSM_DeleteDevice.png)

## <a name="change-an-expired-device-password"></a>Ændre en adgangskode til udløbet enhed

Du skal angive en adgangskode for at godkende en StorSimple enhed med StorSimple øjebliksbillede Manager. Du kan konfigurere denne adgangskode, når du bruger Windows PowerShell-grænseflade til konfiguration af enheden. Dog kan udløber adgangskoden. Hvis dette sker, kan du bruge portalen Azure klassisk ændre adgangskoden. Da enheden blev konfigureret i StorSimple øjebliksbillede Manager, før adgangskoden udløbet, skal du derefter igen godkende enheden i StorSimple øjebliksbillede Manager. 

#### <a name="to-change-the-expired-password"></a>Ændre adgangskoden udløbet

1. Starte tjenesten StorSimple Manager i Azure klassisk portalen.

2. Klik på **enheder** > **Konfigurer** til enheden.

3. Rul ned til sektionen StorSimple øjebliksbillede Manager. Angiv en adgangskode, der er 14-15 tegn. Sørg for, at adgangskoden indeholder en blanding af store bogstaver, små bogstaver, numeriske og særlige tegn.

4. Angiv adgangskode for at bekræfte den igen.

5. Klik på **Gem** nederst på siden.

#### <a name="to-re-authenticate-the-device"></a>Til at godkende enheden igen

1. Start StorSimple øjebliksbillede Manager.

2. Klik på **enheder**i ruden **område** . Der vises en liste over konfigurerede enheder i ruden **resultater** . 

3. Vælg den enhed, højreklikke og derefter klikke på **Godkend**.

4. Skriv den nye adgangskode i vinduet **Godkend** . 

5. Vælg den enhed, skal du højreklikke og derefter vælge **Opdater enhed**. Dette synkroniseres enheden med StorSimple øjebliksbillede Manager. 

## <a name="replace-a-failed-device"></a>Erstatte en mislykkedes enhed

Hvis en enhed med StorSimple mislykkes, og er erstattes af en enhed med standby (failover), følge nedenstående trin for at oprette forbindelse til den nye enhed og få vist de tilknyttede sikkerhedskopier.

#### <a name="to-connect-to-a-new-device-after-failover"></a>Oprette forbindelse til en ny enhed efter failover

1. Omkonfigurere iSCSI-forbindelsen til den nye enhed. Finde en vejledning, gå til "trin 7: Indlæs, initialisering og formaterer en enhed" i [Implementer enheden lokale StorSimple](storsimple-deployment-walkthrough-u2.md). 

>[AZURE.NOTE] Hvis den nye StorSimple-enhed har den samme IP-adresse som den gamle liste, kan du kunne oprette forbindelse gamle konfigurationen. 

2. Stop Microsoft StorSimple Management-tjenesten:

    1. Start Server Manager.

    2. Vælg **tjenester**på Server Manager Dashboard i menuen **Funktioner** . 

    3. Vælg den **Microsoft StorSimple Management-tjenesten**i vinduet **tjenester** . 

    4. Klik på **Stop tjenesten**under **Microsoft StorSimple Management-tjenesten**, i højre rude. 

3. Fjerne de konfigurationsoplysninger, der er relateret til den gamle enhed: 

    1. Åbn Stifinder, gå til C:\ProgramData\Microsoft\StorSimple\BACatalog. 

    2. Slette filer i mappen BACatalog. 

4. Genstart Microsoft StorSimple Management-tjenesten: 

    1. Vælg **tjenester**på Server Manager Dashboard i menuen **Funktioner** . 

    2. Vælg den **Microsoft StorSimple Management-tjenesten**i vinduet **tjenester** . 

    3. Klik på **Genstart tjenesten**under **Microsoft StorSimple Management-tjenesten**, i højre rude. 

5. Start StorSimple øjebliksbillede Manager. 

6. For at konfigurere den nye StorSimple enhed skal du udføre trin i trin 2: oprette forbindelse til en StorSimple enhed i [Installere StorSimple øjebliksbillede Manager](storsimple-snapshot-manager-deployment.md). 

7. Højreklik på den øverste knude i ruden **område** (StorSimple øjebliksbillede Manager i eksemplet), og klik derefter på **Skift mellem importerer visning**. 

8. Der vises en meddelelse, hvor de importerede lydstyrken grupper og sikkerhedskopier kan ses i StorSimple øjebliksbillede Manager. Klik på **OK**. 

## <a name="next-steps"></a>Næste trin

- Lær, hvordan du [bruger StorSimple øjebliksbillede Manager til at administrere din StorSimple løsning](storsimple-snapshot-manager-admin.md).
- Lær, hvordan du [bruger StorSimple øjebliksbillede Manager til at få vist og administrere enheder](storsimple-snapshot-manager-manage-volumes.md).

