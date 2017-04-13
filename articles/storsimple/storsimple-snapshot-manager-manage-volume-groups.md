<properties 
   pageTitle="StorSimple øjebliksbillede Manager lydstyrken grupper | Microsoft Azure"
   description="Beskriver, hvordan du bruger StorSimple Snapshot Manager MMC-snap-in til at oprette og administrere lydstyrken grupper."
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
   ms.date="04/18/2016"
   ms.author="v-sharos" />

# <a name="use-storsimple-snapshot-manager-to-create-and-manage-volume-groups"></a>Brug StorSimple øjebliksbillede Manager til at oprette og administrere lydstyrken grupper

## <a name="overview"></a>Oversigt

Du kan bruge noden **Lydstyrken grupper** i ruden **omfang** tildele enheder til lydstyrke grupper, få vist oplysninger om en volumenlicens-gruppe, planlægge sikkerhedskopier og redigere lydstyrken grupper. 

Lydstyrken grupper er grupper af relaterede enheder, der bruges til at sikre, at sikkerhedskopiering er ensartet programmet på computeren. Se [enheder og lydstyrken grupper](storsimple-what-is-snapshot-manager.md#volumes-and-volume-groups) og [Integration med Windows lydstyrken tjenesten Øjebliksbillede](storsimple-what-is-snapshot-manager.md#integration-with-windows-volume-shadow-copy-service)kan finde flere oplysninger.

>[AZURE.IMPORTANT] 
>
> * Alle enheder i en lydstyrken gruppe skal komme fra en enkelt skyen udbyder.
> 
> * Når du konfigurerer lydstyrken grupper, ikke blande klynge-delte enheder (CSVs) og ikke-CSVs i gruppen samme lydstyrken. StorSimple øjebliksbillede Manager understøtter ikke en blanding af CSVs og ikke-CSVs i det samme øjebliksbillede.
 
![Lydstyrken grupper node](./media/storsimple-snapshot-manager-manage-volume-groups/HCS_SSM_Volume_groups.png)

**Figur 1: StorSimple øjebliksbillede Manager lydstyrken grupper node** 

Dette selvstudium beskriver, hvordan du kan bruge StorSimple øjebliksbillede Manager til at:

- Få vist oplysninger om dine lydstyrken grupper 
- Oprette en volumenlicens-gruppe
- Sikkerhedskopiere en volumenlicens-gruppe
- Redigere en volumenlicens-gruppe
- Slette en volumenlicens-gruppe

Alle disse handlinger er også tilgængelige i ruden **Handlinger** .
 
## <a name="view-volume-groups"></a>Se lydstyrken grupper

Hvis du klikker på noden **Lydstyrken grupper** , viser ruden **resultater** følgende oplysninger om hver lydstyrken gruppe, afhængigt af de kolonne valg, du foretager. (Kolonnerne i ruden **resultater** kan konfigureres. Højreklik på **enheder** node, Vælg **visning**, og vælg derefter **Tilføj/fjern kolonner**.)

Resultater kolonne | Beskrivelse 
:--------------|:------------ 
Navn           | Kolonnen **navn** indeholder navnet på gruppen lydstyrken.
Program    | Kolonnen **programmer** viser antallet af VSS forfattere aktuelt er installeret og køre på Windows-vært.
Markeret       | Kolonnen **valgt** viser antallet enheder, der er indeholdt i gruppen lydstyrken. Et nul (0) angiver, at ingen program er knyttet til enhederne i gruppen lydstyrken.
Importeret       | Kolonnen **importeret** viser antallet af importerede enheder. Når den er indstillet til **Sand**, angiver denne kolonne, at en lydstyrken gruppe er importeret fra portalen Azure klassisk og blev ikke oprettet i StorSimple øjebliksbillede Manager.
 
>[AZURE.NOTE] StorSimple Snapshot Manager lydstyrken grupper vises også under fanen **Sikkerhedskopi politikker** i portalen Azure klassisk.
 
## <a name="create-a-volume-group"></a>Oprette en volumenlicens-gruppe

Brug følgende fremgangsmåde til at oprette en volumenlicens-gruppe.

#### <a name="to-create-a-volume-group"></a>Sådan oprettes en volumenlicens-gruppe

1. Klik på ikonet Skrivebord for at starte StorSimple øjebliksbillede Manager. 

2. Højreklik på **Lydstyrke grupper**i ruden **omfang** , og klik derefter på **Opret lydstyrken gruppe**. 

    ![Oprette lydstyrken gruppe](./media/storsimple-snapshot-manager-manage-volume-groups/HCS_SSM_Create_volume_group.png)
 
    Dialogboksen **Opret en lydstyrken gruppe** vises. 

    ![Dialogboksen Opret en lydstyrken gruppe](./media/storsimple-snapshot-manager-manage-volume-groups/HCS_SSM_CreateVolumeGroup_dialog.png) 

3.  Angiv følgende oplysninger: 

    1. Skriv et entydigt navn til den nye gruppe lydstyrken i feltet **navn** . 

    2. Vælg programmer, der er knyttet til de enheder, du vil tilføje til gruppen lydstyrken i feltet **programmer** . 

        Feltet **programmer** viser kun de programmer, der bruger StorSimple enheder og har VSS forfattere aktiveret for dem. En VSS writer er aktiveret, kun, hvis alle de enheder, som writer er opmærksom på, er StorSimple enheder. Hvis feltet programmer er tom, installeres ingen programmer, der bruger Azure StorSimple enheder og har understøttet VSS forfattere. (Understøtter i øjeblikket, Azure StorSimple Microsoft Exchange og SQL Server.) Du kan finde flere oplysninger om VSS forfattere, [Integration med Windows lydstyrken tjenesten Øjebliksbillede](storsimple-what-is-snapshot-manager.md#integration-with-windows-volume-shadow-copy-service).

        Hvis du vælger et program, markeres automatisk alle enheder, der er knyttet til den. Omvendt, hvis du vælger enheder, der er knyttet til et bestemt program, programmet vælges automatisk i feltet **programmer** . 

    3. Vælg StorSimple enheder til at føje til gruppen lydstyrken i feltet **enheder** . 

      - Du kan medtage enheder med et eller flere partitioner. (Flere partition enheder kan være dynamiske diske eller grundlæggende disk med flere partitioner). En enhed, der indeholder flere partitioner behandles som en enkelt enhed. Derfor, hvis du tilføjer kun én af partitionerne til en volumenlicens-gruppe, alle andre partitioner føjes automatisk til den pågældende lydstyrken gruppe på samme tid. Når du føjer en flere partition lydstyrken til en volumenlicens-gruppe, fortsætter flere partition lydstyrken skal behandles som en enkelt enhed.

      - Du kan oprette tomme lydstyrken grupper ved at tildele ikke alle enheder til dem. 

      - Ikke blande klynge-delte enheder (CSVs) og ikke-CSVs i gruppen samme lydstyrken. StorSimple øjebliksbillede Manager understøtter ikke en blanding af csv-enheder og ikke-csv-enheder i det samme øjebliksbillede. 

4. Klik på **OK** for at gemme gruppen lydstyrken.

## <a name="back-up-a-volume-group"></a>Sikkerhedskopiere en volumenlicens-gruppe

Brug følgende fremgangsmåde til at sikkerhedskopiere en volumenlicens-gruppe.

#### <a name="to-back-up-a-volume-group"></a>Sikkerhedskopiere en volumenlicens-gruppe

1. Klik på ikonet Skrivebord for at starte StorSimple øjebliksbillede Manager.

2. I ruden **omfang** Udvid noden **Lydstyrken grupper** , skal du højreklikke på et gruppenavn lydstyrken og derefter klikke på **Tag sikkerhedskopi**. 

    ![Sikkerhedskopiere lydstyrken gruppe med det samme](./media/storsimple-snapshot-manager-manage-volume-groups/HCS_SSM_Take_backup.png)

3. Vælg **Lokal Snapshot** eller et **Skyen Snapshot**i dialogboksen **Tage sikkerhedskopi** , og klik derefter på **Opret**. 

    ![Tage ekstra dialogboksen](./media/storsimple-snapshot-manager-manage-volume-groups/HCS_SSM_TakeBackup_dialog.png) 

4. Udvid noden **Jobs** for at bekræfte, at sikkerhedskopieringen kører, og klik derefter på **kører**. Sikkerhedskopien skal være angivet.

5. Få vist fuldførte snapshot, udvid noden **Sikkerhedskopi katalog** , udvide lydstyrken gruppenavn, og klik derefter på **Lokal Snapshot** eller **Skyen øjebliksbillede**. Sikkerhedskopien vil blive vist, hvis den er fuldført. 

## <a name="edit-a-volume-group"></a>Redigere en volumenlicens-gruppe

Brug følgende fremgangsmåde til at redigere en volumenlicens-gruppe.

#### <a name="to-edit-a-volume-group"></a>Redigere en volumenlicens-gruppe

1. Klik på ikonet Skrivebord for at starte StorSimple øjebliksbillede Manager.

2. Udvid noden **Lydstyrken grupper** i ruden **område** , skal du højreklikke på et gruppenavn lydstyrken, og klik derefter på **Rediger**. 

3. Dialogboksen **Opret en lydstyrken gruppe **vises. Du kan ændre **navnet**, **programmer**og **enheder** posterne. 

4. Klik på **OK** for at gemme ændringerne.

## <a name="delete-a-volume-group"></a>Slette en volumenlicens-gruppe

Brug følgende fremgangsmåde til at slette en volumenlicens-gruppe. 

>[AZURE.WARNING] Dette sletter også alle de sikkerhedskopier, der er knyttet til gruppen lydstyrken.

#### <a name="to-delete-a-volume-group"></a>Sådan slettes en volumenlicens-gruppe

1. Klik på ikonet Skrivebord for at starte StorSimple øjebliksbillede Manager. 

2. Udvid noden **Lydstyrken grupper** i ruden **område** , skal du højreklikke på et gruppenavn lydstyrken, og klik derefter på **Slet**. 

3. Dialogboksen **Slet lydstyrken gruppe** vises. Skriv **Bekræft** i tekstfeltet, og klik derefter på **OK**. 

    Alle sikkerhedskopier, der er tilknyttet gruppen lydstyrken slettes fra kataloget til sikkerhedskopiering og gruppen slettede lydstyrken forsvinder fra listen i ruden **resultater** .

## <a name="next-steps"></a>Næste trin

- Lær, hvordan du [bruger StorSimple øjebliksbillede Manager til at administrere din StorSimple løsning](storsimple-snapshot-manager-admin.md).
- Lær, hvordan du [bruger StorSimple øjebliksbillede Manager til at oprette og administrere politikker for sikkerhedskopiering](storsimple-snapshot-manager-manage-backup-policies.md).
