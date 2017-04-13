<properties 
   pageTitle="StorSimple øjebliksbillede Manager sikkerhedskopiering politikker | Microsoft Azure"
   description="Beskriver, hvordan du bruger StorSimple Snapshot Manager MMC snap-in til at oprette og administrere de ekstra politikker, der bestemmer planlagte sikkerhedskopier."
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
   ms.date="05/12/2016"
   ms.author="v-sharos" />

# <a name="use-storsimple-snapshot-manager-to-create-and-manage-backup-policies"></a>Brug StorSimple øjebliksbillede Manager til at oprette og administrere politikker for sikkerhedskopiering

## <a name="overview"></a>Oversigt

En politik for sikkerhedskopiering opretter en tidsplan for sikkerhedskopiering af lydstyrken data lokalt eller i skyen. Når du opretter en politik for sikkerhedskopiering, kan du også angive en opbevaringspolitik. (Du kan bevare højst 64 snapshots). Du kan finde flere oplysninger om sikkerhedskopiering politikker, se [sikkerhedskopi typer](storsimple-what-is-snapshot-manager.md#backup-type) i [StorSimple 8000 serie: en hybrid skyen løsning](storsimple-overview.md).

Dette selvstudiet beskrives, hvordan du:

- Opret en politik for sikkerhedskopiering 
- Redigere en politik for sikkerhedskopiering 
- Slette en politik for sikkerhedskopiering 

## <a name="create-a-backup-policy"></a>Opret en politik for sikkerhedskopiering

Brug følgende fremgangsmåde til at oprette en ny politik for sikkerhedskopiering.

#### <a name="to-create-a-backup-policy"></a>Sådan oprettes en sikkerhedskopi politik

1. Klik på ikonet Skrivebord for at starte StorSimple øjebliksbillede Manager.

2. Højreklik på **Politikker for sikkerhedskopiering**i ruden **omfang** , og klik på **Opret sikkerhedskopi politik**.

    ![Opret en politik for sikkerhedskopiering](./media/storsimple-snapshot-manager-manage-backup-policies/HCS_SSM_Create_BU_policy.png)

    Dialogboksen **Opret en politik** vises. 

    ![Oprette en politik - fanen Generelt](./media/storsimple-snapshot-manager-manage-backup-policies/HCS_SSM_Create_policy_general.png)

3. Angiv følgende oplysninger under fanen **Generelt** :

   1. Skriv et navn til politikken i tekstboksen **navn** .

   2. Skriv navnet på gruppen lydstyrken, der er knyttet til politikken i tekstfeltet **lydstyrken gruppe** .

   3. Vælg enten **lokal Snapshot** eller **skyen øjebliksbillede**.

   4. Vælg antallet af snapshots til at bevare. Hvis du vælger **alle**, vil være 64 snapshots bevares (maksimum). 

4. Klik på fanen **tidsplan** .

    ![Oprette en politik - fanen planlægning](./media/storsimple-snapshot-manager-manage-backup-policies/HCS_SSM_Create_policy_schedule.png)

5. Angiv følgende oplysninger under fanen **tidsplan** : 

   1. Klik på afkrydsningsfeltet **Aktiver** for at planlægge det næste sikkerhedskopiering.

   2. Vælg **én gang**, **dagligt**, **Ugentligt**eller **Månedligt**under **Indstillinger**. 

   3. Klik på kalenderikonet, og vælg en startdato i boksen **Start** tekst.

   4. Du kan angive valgfrit Gentag tidsplaner og en slutdato under **Avancerede indstillinger**.

   5. Klik på **OK**.

Når du opretter en sikkerhedskopi politik, vises følgende oplysninger i ruden **resultater** :

- **Navn** – navnet på sikkerhedskopien politik.

- **Type** – lokal snapshot eller skyen øjebliksbillede.

- **Lydstyrken gruppe** – gruppen lydstyrken, der er knyttet til politikken.

- **Opbevaring** – antallet af snapshots bevares; Maksimum er 64.

- **Oprettet** – den dato, hvor denne politik blev oprettet.

- **Aktiveret** – om politikken der aktuelt er aktiveret: **Sand** angiver, at det er i kraft; **Falsk** angiver, at det ikke er i kraft. 

## <a name="edit-a-backup-policy"></a>Redigere en politik for sikkerhedskopiering

Brug følgende fremgangsmåde til at redigere en eksisterende sikkerhedskopiering politik.

#### <a name="to-edit-a-backup-policy"></a>Redigere en politik for sikkerhedskopiering

1. Klik på ikonet Skrivebord for at starte StorSimple øjebliksbillede Manager. 

2. Klik på noden **Sikkerhedskopi politikker** i ruden **område** . Alle de ekstra politikker vises i ruden **resultater** . 

3. Højreklik på den politik, du vil redigere, og klik derefter på **Rediger**. 

    ![Redigere en politik for sikkerhedskopiering](./media/storsimple-snapshot-manager-manage-backup-policies/HCS_SSM_Edit_BU_policy.png) 

4. Når vinduet **Opret en politik** vises, Angiv dine ændringer, og klik derefter på **OK**. 

## <a name="delete-a-backup-policy"></a>Slette en politik for sikkerhedskopiering

Brug følgende fremgangsmåde til at slette en sikkerhedskopi politik.

#### <a name="to-delete-a-backup-policy"></a>Slette en politik for sikkerhedskopiering

1. Klik på ikonet Skrivebord for at starte StorSimple øjebliksbillede Manager. 

2. Klik på noden **Sikkerhedskopi politikker** i ruden **område** . Alle de ekstra politikker vises i ruden **resultater** . 

3. Højreklik på den ekstra politik, du vil slette, og klik derefter på **Slet**.

4. Når bekræftelsesmeddelelsen vises, skal du klikke på **Ja**.

    ![Politik for sikkerhedskopiering bekræftelse af sletning](./media/storsimple-snapshot-manager-manage-backup-policies/HCS_SSM_Delete_BU_policy.png)

## <a name="next-steps"></a>Næste trin

- Lær, hvordan du [bruger StorSimple øjebliksbillede Manager til at administrere din StorSimple løsning](storsimple-snapshot-manager-admin.md).
- Lær, hvordan du [bruger StorSimple øjebliksbillede Manager til at få vist og administrere sikkerhedskopieringsjob](storsimple-snapshot-manager-manage-backup-jobs.md).
