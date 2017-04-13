<properties 
   pageTitle="StorSimple øjebliksbillede Manager sikkerhedskopiering katalog | Microsoft Azure"
   description="Beskriver, hvordan du bruger StorSimple øjebliksbillede Manager MMC-snap-in til at få vist og administrere kataloget til sikkerhedskopiering."
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
   ms.date="04/26/2016"
   ms.author="v-sharos" />

# <a name="use-storsimple-snapshot-manager-to-manage-the-backup-catalog"></a>Brug StorSimple øjebliksbillede Manager til at administrere kataloget til sikkerhedskopiering

## <a name="overview"></a>Oversigt

Den primære funktion af StorSimple øjebliksbillede Manager er muligt at oprette programmet ensartet sikkerhedskopier af StorSimple enheder i form af snapshots. Snapshots vises derefter en XML-fil kaldet et *kataloget sikkerhedskopiering*. Kataloget til sikkerhedskopiering arrangerer snapshots efter lydstyrken gruppe og derefter efter lokal snapshot eller skyen øjebliksbillede. 

I dette selvstudium beskrives, hvordan du kan bruge noden **Sikkerhedskopi katalog** til at udføre følgende opgaver:

- Gendanne en volumenlicens 
- Klone en lydstyrken eller lydstyrken gruppe 
- Slette en sikkerhedskopi 
- Gendanne en fil
- Gendanne den Storsimple øjebliksbillede Manager-database

Du kan få vist kataloget til sikkerhedskopiering ved at udvide noden **Kataloget sikkerhedskopiering** i ruden **område** , og derefter udvide gruppen lydstyrken.

- Hvis du klikker på lydstyrke gruppenavn, viser ruden **resultater** antallet af lokale snapshots og skyen snapshots tilgængelig for gruppen lydstyrken. 

- Hvis du klikker på **Lokal Snapshot** eller **Skyen øjebliksbillede**, viser ruden **resultater** følgende oplysninger om hver ekstra øjebliksbillede (afhængigt af indstillingerne for **visning** ): 

    - **Navn** – den tid, det blev tager snapshot. 

    - **Type** – om dette er en lokal snapshot eller et snapshot af en sky. 

    - **Ejer** – ejeren af indholdet. 

    - **Tilgængelige** – om snapshot er tilgængelig i øjeblikket. **Sand** indikerer, snapshot er tilgængelige og kan gendannes **Falsk** angiver, at snapshot er ikke længere tilgængelig. 

    - **Importeret** – om sikkerhedskopien blev importeret. **Sand** angiver, at sikkerhedskopieringen er importeret fra tjenesten StorSimple Manager samtidig enheden blev konfigureret i StorSimple øjebliksbillede Manager; **Falsk** betyder, den ikke blev importeret, men blev oprettet af StorSimple øjebliksbillede Manager. (Du kan nemt identificere en importerede lydstyrken gruppe, fordi et suffiks er tilføjet, der identificerer den enhed, hvorfra gruppen lydstyrken blev importeret).

    ![Kataloget sikkerhedskopiering](./media/storsimple-snapshot-manager-manage-backup-catalog/HCS_SSM_Backup_catalog.png)

- Hvis du udvide **Lokal Snapshot** eller **Skyen øjebliksbillede**, og klik derefter på navnet på en enkelt øjebliksbillede, vises følgende oplysninger om snapshot, du har valgt i ruden **resultater** :

    - **Navn** – lydstyrken identificeret med drevbogstavet. 

    - **Lokalt navn** – lokale navnet på drevet (hvis relevant). 

    - **Enhed** – navnet på enheden på som lydstyrken er placeret. 

    - **Tilgængelige** – om snapshot er tilgængelig i øjeblikket. **Sand** indikerer, snapshot er tilgængelige og kan gendannes **Falsk** angiver, at snapshot er ikke længere tilgængelig. 


## <a name="restore-a-volume"></a>Gendanne en volumenlicens

Brug følgende fremgangsmåde til at gendanne en enhed fra sikkerhedskopi.

#### <a name="prerequisites"></a>Forudsætninger

Hvis du ikke allerede har gjort det, oprette en lydstyrken og lydstyrken gruppe, og slet derefter lydstyrken. Som standard sikkerhedskopierer StorSimple øjebliksbillede Manager en lydstyrken før til at blive slettet. Denne forholdsregler kan forhindre tab af data, hvis lydstyrken slettes ved et uheld, eller hvis data, der skal gendannes for en eller anden grund. 

StorSimple øjebliksbillede Manager vises følgende meddelelse, mens den opretter advarslen sikkerhedskopien.

![Automatisk snapshot meddelelse](./media/storsimple-snapshot-manager-manage-backup-catalog/HCS_SSM_Automatic_snap.png) 

>[AZURE.IMPORTANT]Du kan ikke slette en enhed, der er en del af en volumenlicens-gruppe. Indstillingen Slet er ikke tilgængelig. <br>

#### <a name="to-restore-a-volume"></a>Gendanne en volumenlicens

1. Klik på ikonet Skrivebord for at starte StorSimple øjebliksbillede Manager. 

2. I ruden **omfang** Udvid noden **Sikkerhedskopi katalog** , udvide en lydstyrken gruppe og derefter klikke på **Lokale Snapshots** eller **Skyen Snapshots**. Der vises en liste over sikkerhedskopiering snapshots i ruden **resultater** . 

3. Find den sikkerhedskopi, du vil gendanne, højreklik, og klik derefter på **Gendan**. 

    ![Gendanne kataloget sikkerhedskopiering](./media/storsimple-snapshot-manager-manage-backup-catalog/HCS_SSM_Restore_BU_catalog.png) 

4. Gennemgå oplysningerne på siden confirmation, Skriv **Bekræft**, og klik derefter på **OK**. StorSimple øjebliksbillede Manager bruger sikkerhedskopien til at gendanne lydstyrken. 

    ![Gendanne bekræftelsesmeddelelsen](./media/storsimple-snapshot-manager-manage-backup-catalog/HCS_SSM_Restore_volume_msg.png) 

5. Du kan overvåge gendannelsen, når det køres. Udvid noden **job** i ruden **omfang** , og klik derefter på **kører**. Oplysninger om job vises i ruden **resultater** . Når Gendannelsesjobbet er afsluttet, overføres oplysninger om job til listen over **seneste 24 timer** .

## <a name="clone-a-volume-or-volume-group"></a>Klone en lydstyrken eller lydstyrken gruppe

Brug følgende fremgangsmåde til at oprette en dublet (Klon) på en enhed eller lydstyrken gruppe.

#### <a name="to-clone-a-volume-or-volume-group"></a>At klone en lydstyrken eller lydstyrken gruppe

1. Klik på ikonet Skrivebord for at starte StorSimple øjebliksbillede Manager.

2. Udvid noden **Sikkerhedskopi katalog** i ruden **omfang** , udvide en volumenlicens-gruppe, og klik **Skyen Snapshots**. Der vises en liste over sikkerhedskopier i ruden **resultater** .

3. Find lydstyrken eller lydstyrken gruppe, du vil klone, højreklik på lydstyrken eller lydstyrken gruppenavn, og klik på **Klon**. Dialogboksen **Klon skyen øjebliksbillede** vises.

    ![Klone et snapshot af en sky](./media/storsimple-snapshot-manager-manage-backup-catalog/HCS_SSM_Clone.png) 

4. Udfyld dialogboksen **Klon skyen øjebliksbillede** på følgende måde: 

    1. Skriv et navn til klonede lydstyrken i tekstboksen **navn** . Dette navn vises i noden **enheder** . 

    2. (Valgfrit) Vælg **drev**, og vælg derefter et drevbogstav fra på rullelisten. 

    3. (Valgfrit) Vælg **Mappe (NTFS)**, og skrive en mappesti, eller klik på Gennemse og vælge en placering til mappen. 

    4. Klik på **Opret**.

5. Når processen kloningssoftware er færdig, skal du har initialiseret klonede lydstyrken. Start Server Manager, og derefter starte Diskadministration. Finde detaljerede oplysninger, se [tilslutte enheder](storsimple-snapshot-manager-manage-volumes.md#mount-volumes). Når det startes, vises lydstyrken under noden **enheder** i ruden **område** . Hvis du ikke se lydstyrken er angivet, skal du opdatere listen over enheder (højreklik på noden **enheder** , og klik derefter på **Opdater**).

## <a name="delete-a-backup"></a>Slette en sikkerhedskopi

Brug følgende fremgangsmåde til at slette et snapshot fra kataloget til sikkerhedskopiering. 

>[AZURE.NOTE]Hvis du sletter et øjebliksbillede, slettes de sikkerhedskopierede data, der er knyttet til snapshot. Processen med fjernelse af data fra skyen kan dog gå et stykke tid.<br>
 
#### <a name="to-delete-a-backup"></a>Slette en sikkerhedskopi

1. Klik på ikonet Skrivebord for at starte StorSimple øjebliksbillede Manager.

2. I ruden **omfang** Udvid noden **Sikkerhedskopi katalog** , udvide en lydstyrken gruppe og derefter klikke på **Lokale Snapshots** eller **Skyen Snapshots**. Der vises en liste over øjebliksbilleder i ruden **resultater** . 

3. Højreklik på den snapshot, du vil slette, og klik derefter på **Slet**.

4. Når bekræftelsesmeddelelsen vises, skal du klikke på **OK**. 

## <a name="recover-a-file"></a>Gendanne en fil

Hvis en fil slettes ved et uheld kommer fra en disk, kan du gendanne filen ved at hente et øjebliksbillede, som allerede datoer sletningen, bruge snapshot til at oprette en kopi af lydstyrken og derefter kopiere filen fra klonede lydstyrken til den oprindelige lydstyrke.

#### <a name="prerequisites"></a>Forudsætninger

Før du begynder, skal du kontrollere, at du har en aktuel sikkerhedskopi af gruppen lydstyrken. Slet derefter en fil, der er gemt på en af enhederne i gruppen lydstyrken. Til sidst skal du følge nedenstående trin til at gendanne de slettede filer fra sikkerhedskopien. 

#### <a name="to-recover-a-deleted-file"></a>Gendanne en slettet fil

1. Klik på ikonet StorSimple øjebliksbillede Manager på din computer. Vinduet StorSimple øjebliksbillede Manager console vises. 

2. Udvid noden **Sikkerhedskopi katalog** i ruden **omfang** , og gå til et øjebliksbillede, der indeholder den slettede fil. Typisk, skal du vælge et øjebliksbillede, der blev oprettet lige før sletningen. 

3. Find den enhed, du vil klone, højreklik, og klik på **Klon**. Dialogboksen **Klon skyen øjebliksbillede** vises.

    ![Klone et snapshot af en sky](./media/storsimple-snapshot-manager-manage-backup-catalog/HCS_SSM_Clone.png) 

4. Udfyld dialogboksen **Klon skyen øjebliksbillede** på følgende måde: 

   1. Skriv et navn til klonede lydstyrken i tekstboksen **navn** . Dette navn vises i noden **enheder** . 

   2. (Valgfrit) Vælg **drev**, og vælg derefter et drevbogstav fra på rullelisten. 

   3. (Valgfrit) Vælge **Mappe (NTFS)**, og skrive en mappesti, eller klik på **Gennemse** og vælge en placering til mappen. 

   4. Klik på **Opret**. 

5. Når processen kloningssoftware er færdig, skal du har initialiseret klonede lydstyrken. Start Server Manager, og derefter starte Diskadministration. Finde detaljerede oplysninger, se [tilslutte enheder](storsimple-snapshot-manager-manage-volumes.md#mount-volumes). Når det startes, vises lydstyrken under noden **enheder** i ruden **område** . 

    Hvis du ikke se lydstyrken er angivet, skal du opdatere listen over enheder (højreklik på noden **enheder** , og klik derefter på **Opdater**).

6. Åbn mappen NTFS, som indeholder klonede lydstyrken, udvid noden **enheder** , og Åbn derefter klonede lydstyrken. Find den fil, du vil gendanne, og kopiere den til den primære lydstyrken.

7. Når du gendanner fil, kan du slette mappen NTFS, som indeholder klonede lydstyrken.

## <a name="restore-the-storsimple-snapshot-manager-database"></a>Gendanne den StorSimple øjebliksbillede Manager-database

Du bør regelmæssigt sikkerhedskopiere den StorSimple øjebliksbillede Manager-database på computeren, host. Hvis nedbrud opstår eller host computeren ikke for en eller anden grund, kan du gendanne den fra sikkerhedskopien. Det er en manuel proces at oprette sikkerhedskopien af databasen.

#### <a name="to-back-up-and-restore-the-database"></a>Sikkerhedskopiere og gendanne databasen

1. Stop Microsoft StorSimple Management-tjenesten:

    1. Start Server Manager.

    2. Vælg **tjenester**på Server Manager dashboard i menuen **Funktioner** .

    3. Vælg den **Microsoft StorSimple Management-tjenesten**i vinduet **tjenester** .

    4. Klik på **Stop tjenesten**under **Microsoft StorSimple Management-tjenesten**, i højre rude.

2. Gå til C:\ProgramData\Microsoft\StorSimple\BACatalog på computeren host. 

    >[AZURE.NOTE] ProgramData er en skjult mappe.
 
3. Find fil, XML-katalog, kopiere filen og gemme kopien i et sikkert sted eller i skyen. Hvis værten mislykkes, kan du bruge denne sikkerhedskopifil for at gendanne de ekstra politikker, du har oprettet i StorSimple øjebliksbillede Manager.

    ![Azure StorSimple kataloget sikkerhedskopiering fil](./media/storsimple-snapshot-manager-manage-backup-catalog/HCS_SSM_bacatalog.png)

4. Genstart Microsoft StorSimple Management-tjenesten: 

    1. Vælg **tjenester**på Server Manager dashboard i menuen **Funktioner** .
    
    2. Vælg den **Microsoft StorSimple Management-tjenesten**i vinduet **tjenester** .

    3. Klik på **Genstart tjenesten**under **Microsoft StorSimple Management-tjenesten**, i højre rude.

5. Gå til C:\ProgramData\Microsoft\StorSimple\BACatalog på computeren host. 

6. Slet katalog XML-filen, og erstatte den med den sikkerhedskopierede version, du har oprettet. 

7. Klik på ikonet skrivebord StorSimple øjebliksbillede Manager for at starte StorSimple øjebliksbillede Manager. 

## <a name="next-steps"></a>Næste trin

- Lær mere om at [bruge StorSimple Snapshot Manager til at administrere din StorSimple løsning](storsimple-snapshot-manager-admin.md).
- Lær mere om [StorSimple øjebliksbillede Manager opgaver og arbejdsprocesser](storsimple-snapshot-manager-admin.md#storsimple-snapshot-manager-tasks-and-workflows).
