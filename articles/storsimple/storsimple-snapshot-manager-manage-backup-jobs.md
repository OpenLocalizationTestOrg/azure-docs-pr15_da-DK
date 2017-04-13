<properties 
   pageTitle="StorSimple øjebliksbillede Manager sikkerhedskopieringsjob | Microsoft Azure"
   description="Beskriver, hvordan du bruger StorSimple øjebliksbillede Manager MMC-snap-in til at få vist og administrere planlagt, kører i øjeblikket og færdige sikkerhedskopieringsjob."
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


# <a name="use-storsimple-snapshot-manager-to-view-and-manage-backup-jobs"></a>Brug StorSimple Snapshot Manager til at få vist og administrere sikkerhedskopieringsjob

## <a name="overview"></a>Oversigt

Noden **job** i ruden **område** viser **planlagt**, **seneste 24 timer**og **kører** sikkerhedskopiering opgaver, som du har startet interaktivt eller af en konfigurerede politik. 

Dette selvstudium beskrives det, hvordan du kan bruge noden **job** til at få vist oplysninger om planlagt, seneste og i øjeblikket kører sikkerhedskopieringsjob. (Listen over job og tilsvarende oplysninger vises i ruden **resultater** ). Desuden kan du højreklikke på en viste job og se en genvejsmenu, der viser en liste over tilgængelige handlinger.

## <a name="view-scheduled-jobs"></a>Få vist planlagte opgaver

Benyt følgende fremgangsmåde til at få vist planlagte sikkerhedskopieringsjob.

#### <a name="to-view-scheduled-jobs"></a>Sådan får du vist planlagte opgaver

1. Klik på ikonet Skrivebord for at starte StorSimple øjebliksbillede Manager. 

2. Udvid noden **job** ruden **område** , og klik på **planlagt**. Følgende oplysninger vises i ruden **resultater** :

    - **Navn** – navnet på det planlagte øjebliksbillede

    - **Næste kørsel** – datoen og klokkeslættet for det næste planlagte øjebliksbillede

    - **Sidste køre** – datoen og klokkeslættet for den seneste planlagt øjebliksbillede

    >[AZURE.NOTE] For enkeltstående kun snapshots, vil de **Næste kørsel** og **Sidste køre** være den samme. 
 
    ![Planlagte sikkerhedskopieringsjob](./media/storsimple-snapshot-manager-manage-backup-jobs/HCS_SSM_Jobs_scheduled.png) 
 
3. Højreklik på navnet på jobbet i ruden **resultater** for at udføre yderligere handlinger på en bestemt opgave, og vælg menuindstillingerne.

## <a name="view-recent-jobs"></a>Vis seneste job

Benyt følgende fremgangsmåde for at vise sikkerhedskopiering og gendannelse sager, der blev afsluttet i de seneste 24 timer.

#### <a name="to-view-recent-jobs"></a>Sådan får du vist seneste job

1. Klik på ikonet Skrivebord for at starte StorSimple Snapshot Manager.

2. Udvid noden **job** ruden **område** , og klik på **seneste 24 timer**. Ruden **resultater** viser sikkerhedskopieringsjob for de seneste 24 timer (til op til 64 job). Følgende oplysninger vises i ruden **resultater** , afhængigt af indstillingerne for **visning** startposition:

    - **Navn** – navnet på det planlagte øjebliksbillede.
 
    - **Introduktion** – dato og klokkeslæt, når snapshot startede.

    - **Stoppet** – dato og klokkeslæt, når snapshot færdig eller blev afbrudt.

    - **Forløbet** – mængden af tid mellem **Introduktion** og **stoppet** gange.

    - **Status** – tilstanden for den senest afsluttede sag. **Success** angiver, at sikkerhedskopieringen blev oprettet. **Mislykket** angiver, at jobbet ikke kørte korrekt.

    - **Du kan finde oplysninger** – årsagen til fejlen.

    - **Byte behandles (MB)** – mængden data fra gruppen lydstyrken, der blev behandlet (i MB). 

    ![Job, der har kørt i de seneste 24 timer](./media/storsimple-snapshot-manager-manage-backup-jobs/HCS_SSM_Jobs_Last_24_hours.png) 

3. Højreklik på jobbet navnet i ruden **resultater** for at udføre yderligere handlinger på et bestemt job, og vælg menuindstillingerne.

    ![Slette et job](./media/storsimple-snapshot-manager-manage-backup-catalog/HCS_SSM_Delete_backup.png) 
     
## <a name="view-currently-running-jobs"></a>Få vist i øjeblikket kører job

Brug følgende fremgangsmåde for at få vist job, der er i gang.

#### <a name="to-view-currently-running-jobs"></a>Sådan får du vist i øjeblikket kører job

1. Klik på ikonet Skrivebord for at starte StorSimple øjebliksbillede Manager.

2. Udvid noden **job** ruden **område** , og klik på **kører**. Afhængigt af indstillingerne for **visning** startposition, vises følgende oplysninger i ruden **resultater** : 

    - **Navn** – navnet på den planlagt øjebliksbillede.

    - **Introduktion** – dato og klokkeslæt, når snapshot startede.

    - **Kontrolpunkt** – den aktuelle handling af sikkerhedskopien.

    - **Status** – den færdiggjorte procentdel.
    
    - **Forløbet** – mængden tid, der er gået, efter at sikkerhedskopieringen begyndte. 

    - **Gennemsnitlig overførselshastighed (MB)** – forholdet mellem samlet antal bytes behandlet som samlede tid til at behandle (MB).

    - **Byte behandles (MB)** – Samlet antal bytes behandlet (i MB).

    - **Byte skrevet (MB)** – samlede byte data, der er skrevet (i MB). Den indeholder dataene, samt metadata og derfor er typisk større end byte behandles.

    ![I øjeblikket er aktive job](./media/storsimple-snapshot-manager-manage-backup-jobs/HCS_SSM_Jobs_running.png)

3. Højreklik på jobbet navnet i ruden **resultater** for at udføre yderligere handlinger på et bestemt job, og vælg menuindstillingerne.

## <a name="next-steps"></a>Næste trin

- Lær, hvordan du [bruger StorSimple øjebliksbillede Manager til at administrere din StorSimple løsning](storsimple-snapshot-manager-admin.md).
- Lær, hvordan du [bruger StorSimple øjebliksbillede Manager til at administrere kataloget til sikkerhedskopiering](storsimple-snapshot-manager-manage-backup-catalog.md).















            


 

