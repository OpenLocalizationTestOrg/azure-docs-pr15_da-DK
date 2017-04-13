<properties 
   pageTitle="StorSimple af snapshots og enheder | Microsoft Azure"
   description="Beskriver, hvordan du bruger den StorSimple øjebliksbillede Manager MMC snap-in til at få vist og administrere enheder og konfigurere sikkerhedskopier."
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

# <a name="use-storsimple-snapshot-manager-to-view-and-manage-volumes"></a>Brug StorSimple øjebliksbillede Manager til at få vist og administrere enheder

## <a name="overview"></a>Oversigt

Du kan bruge noden StorSimple øjebliksbillede Manager **enheder** (i ruden **omfang** ) til at vælge enheder og få vist oplysninger om dem. Enhederne præsenteres som drev, der svarer til de enheder, der er tilsluttet af værten. Noden **enheder** viser lokale drev og lydstyrken typer, der understøttes af StorSimple, herunder enheder, der er fundet ved hjælp af iSCSI og en enhed. 

Gå til [understøttelse af flere lydstyrken typer](storsimple-what-is-snapshot-manager.md#support-for-multiple-volume-types)kan finde flere oplysninger om understøttede enheder.

![Lydstyrken liste i ruden med søgeresultater](./media/storsimple-snapshot-manager-manage-volumes/HCS_SSM_Volume_node.png)

Noden **enheder** kan du også Scan eller slette enheder, når StorSimple øjebliksbillede Manager opdager dem. 

Dette selvstudium forklarer, hvordan du kan tilslutte, initialiseret, og formater drev og derefter bruge StorSimple øjebliksbillede Manager til at:

- Få vist oplysninger om enheder 
- Slette enheder
- Scan enheder 
- Konfigurere en grundlæggende lydstyrken og op igen
- Konfigurere en dynamisk spejlet enhed og op igen

>[AZURE.NOTE] Alle **lydstyrken** node handlingerne er også tilgængelige i ruden **Handlinger** .
 
## <a name="mount-volumes"></a>Tilslutte enheder

Brug følgende fremgangsmåde til at tilslutte, initialiseret og formatere StorSimple enheder. Denne fremgangsmåde bruger Disk Management, et systemværktøj til styring af harddiske og de tilsvarende enheder eller partitioner. Du kan finde flere oplysninger om administration af diskplads ved at gå til [Disk Management](https://technet.microsoft.com/library/cc770943.aspx) på Microsoft TechNet-webstedet.

#### <a name="to-mount-volumes"></a>At tilslutte enheder

1. Start Microsoft iSCSI afsenderen på computeren host.

2. Angive en af de interface IP-adresser som portalen mål eller registrering IP-adresse, og oprette forbindelse til enheden. Når enheden er tilsluttet, bliver enhederne tilgængelig for din Windows-system. Du kan finde flere oplysninger om brug af Microsoft iSCSI afsenderen, gå til afsnittet "Oprette forbindelse til en iSCSI-målenhed" i [installation og konfiguration af Microsoft iSCSI afsenderen][1].

3. Sådan startes Diskadministration, kan du bruge en af følgende indstillinger:

    - Skriv Diskmgmt.msc i feltet **Kør** .

    - Start Server Manager, udvid noden **lagerplads** , og vælg derefter **Disk Management**.

    - Start **Administration**, udvid noden **Computer Management** , og vælg derefter **Disk Management**. 

    >[AZURE.NOTE] Du skal bruge administratorrettigheder til at køre Diskadministration.
 
4. Gør diskenhed(er) online:

   1. Højreklik på en hvilken som helst lydstyrken markeret **Offline**i administration af Disk.

   2. Klik på **Genaktiver Disk**. Disken skal markeres **Online** , når disken genaktiveres.

5. Initialiseret diskenhed(er):

   1. Højreklik på de fundne enheder.

   2. Vælg **Initialiseret Disk**i menuen.

   3. Vælg de diske, du vil initialiseret i dialogboksen **Initialiseret Disk** , og klik derefter på **OK**.

6. Formatere simple enheder:

   1. Højreklik på en enhed, du vil formatere.

   2. Vælg **Ny simpel lydstyrken**i menuen.

   3. Bruge guiden Ny enkel lydstyrken til at formatere lydstyrken:

      - Angiv størrelsen.
      - Angiv et drevbogstav.
      - Vælg skelnes.
      - Angive en 64 KB allokering enhedsstørrelse.
      - Udføre en hurtig formatering.

7. Formater med flere partition drev. Finde en vejledning, gå til sektionen "Partitioner og enheder" i [Implementere Disk Management](https://msdn.microsoft.com/library/dd163556.aspx).

## <a name="view-information-about-your-volumes"></a>Få vist oplysninger om dine drev

Gør følgende for at få vist oplysninger om lokale og Azure StorSimple enheder.

#### <a name="to-view-volume-information"></a>Sådan får du vist lydstyrken oplysninger

1. Klik på ikonet Skrivebord for at starte StorSimple øjebliksbillede Manager. 

2. Klik på noden **enheder** i ruden **område** . Der vises en liste over lokale og tilsluttede enheder, herunder alle Azure StorSimple enheder, i ruden **resultater** . Kolonnerne i ruden **resultater** kan konfigureres. (Højreklik på noden **enheder** , Vælg **visning**, og vælg derefter **Tilføj/fjern kolonner**).

    ![Konfigurere kolonnerne](./media/storsimple-snapshot-manager-manage-volumes/HCS_SSM_View_volumes.png)

    Resultater kolonne | Beskrivelse 
    :--------------|:-------------
    Navn           | Kolonnen **navn** indeholder bogstavet for hver opdaget lydstyrken.
    Enhed         | Kolonnen **enhed** indeholder IP-adressen på den enhed, der er tilsluttet computeren host.
    Enhedens lydstyrken navn | Kolonnen **Enhed lydstyrken navn** indeholder navnet på den som den valgte enhed tilhører lydstyrke for enhed. Dette er det angivne i Azure klassisk portal for det pågældende bestemte drev lydstyrken navn.
    Access stier   | Kolonnen **Access stier** viser access stien til lydstyrken. Dette er drev bogstav eller Indlæs tidspunkt, hvor lydstyrken er tilgængelige på computeren, host.
 
## <a name="delete-a-volume"></a>Slette en volumenlicens

Brug følgende fremgangsmåde til at slette en enhed fra StorSimple øjebliksbillede Manager.

>[AZURE.NOTE] Du kan ikke slette en enhed, hvis det er en del af en hvilken som helst lydstyrken gruppe. (Indstillingen Slet er ikke tilgængelig for enheder, der er medlemmer af en volumenlicens gruppe). Du skal slette gruppen hele lydstyrken for at slette lydstyrken.


#### <a name="to-delete-a-volume"></a>Slette en volumenlicens

1. Klik på ikonet Skrivebord for at starte StorSimple øjebliksbillede Manager.

2. Klik på noden **enheder** i ruden **område** . 

3. Højreklik på det drev, du vil slette, i ruden **resultater** .

4. Klik på **Slet**i menuen. 

    ![Slette en volumenlicens](./media/storsimple-snapshot-manager-manage-volumes/HCS_SSM_Delete_volume.png) 

5. Dialogboksen **Slet lydstyrke** vises. Skriv **Bekræft** i tekstfeltet, og klik derefter på **OK**.

6. Som standard sikkerhedskopierer StorSimple øjebliksbillede Manager en lydstyrken inden det slettes. Denne forholdsregler kan beskytte dig mod datatab, hvis sletningen var utilsigtet. StorSimple øjebliksbillede Manager viser meddelelsen **Automatisk Snapshot** status, mens den sikkerhedskopierer lydstyrken. 

    ![Automatisk snapshot meddelelse](./media/storsimple-snapshot-manager-manage-volumes/HCS_SSM_Automatic_snap.png) 

## <a name="rescan-volumes"></a>Scan enheder

Brug følgende fremgangsmåde til at scanne igen de enheder, der er forbundet til StorSimple øjebliksbillede Manager.

#### <a name="to-rescan-the-volumes"></a>At scanne igen enhederne

1. Klik på ikonet Skrivebord for at starte StorSimple øjebliksbillede Manager.

2. Højreklik på **enheder**i ruden **omfang** , og klik derefter på **Scan enheder**.

    ![Scan enheder](./media/storsimple-snapshot-manager-manage-volumes/HCS_SSM_Rescan_volumes.png)
 
    Denne procedure synkroniserer listen lydstyrken med StorSimple øjebliksbillede Manager. Ændringerne, som nye enheder eller slettede enheder, afspejles i resultaterne.

## <a name="configure-and-back-up-a-basic-volume"></a>Konfigurere og sikkerhedskopiere en grundlæggende lydstyrken

Brug følgende fremgangsmåde til at konfigurere en sikkerhedskopi af en grundlæggende beholdning, og derefter starte en sikkerhedskopi med det samme eller oprette en politik for planlagte sikkerhedskopier.

### <a name="prerequisites"></a>Forudsætninger

Inden du går i gang:

- Sørg for, at StorSimple enhed og host computeren er konfigureret korrekt. Gå til [Implementer enheden lokale StorSimple](storsimple-deployment-walkthrough-u2.md)kan finde flere oplysninger.

- Installere og konfigurere StorSimple øjebliksbillede Manager. Gå til [Installere StorSimple øjebliksbillede Manager](storsimple-snapshot-manager-deployment.md)kan finde flere oplysninger.

#### <a name="to-configure-backup-of-a-basic-volume"></a>Sådan konfigureres sikkerhedskopi af en grundlæggende lydstyrken

1. Oprette en grundlæggende lydstyrken på enheden StorSimple.

2. Tilslutte, initialiseret og formatere lydstyrken, som beskrevet i [tilslutte enheder](#mount-volumes). 

3. Klik på ikonet StorSimple øjebliksbillede Manager på din computer. Vinduet StorSimple øjebliksbillede Manager åbnes. 

4. Højreklik på noden **enheder** i ruden **omfang** , og vælg derefter **Scan enheder**. Når du er færdig med søgningen, skal en liste over enheder vises i ruden **resultater** . 

5. Højreklik på drevet i ruden **resultater** , og vælg derefter **Opret lydstyrken gruppe**. 

    ![Oprette lydstyrken gruppe](./media/storsimple-snapshot-manager-manage-volumes/HCS_SSM_Create_volume_group.png) 

6. Skriv et navn til gruppen lydstyrken i dialogboksen **Opret lydstyrken gruppe** , tildele enheder til den, og klik derefter på **OK**.

7. Udvid noden **Lydstyrken grupper** i ruden **område** . Gruppen ny lydstyrken skal vises under noden **Lydstyrken grupper** . 

8. Højreklik på gruppenavnet lydstyrken.

    - For at starte en interaktiv (efter behov) sikkerhedskopieringsjob skal du klikke på **Tage sikkerhedskopiering**. 

    - Klik på **Opret sikkerhedskopieringspolitik**for at planlægge en automatisk sikkerhedskopiering. Vælg en lydstyrken gruppe på listen på siden **Generelt** . Angiv tidsplan oplysninger på siden **tidsplan** . Når du er færdig, skal du klikke på **OK**. 

9. Udvid noden **job** i ruden **område** for at bekræfte, at sikkerhedskopieringen er startet, og klik derefter på noden **kører** . Liste over kørende job vises i ruden **resultater** . 

## <a name="configure-and-back-up-a-dynamic-mirrored-volume"></a>Konfigurere og sikkerhedskopiere en dynamisk spejlet enhed

Benytte følgende fremgangsmåde for at konfigurere sikkerhedskopi af en dynamisk spejlet enhed:

- Trin 1: Brug Diskadministration til at oprette en dynamisk spejlet lydstyrken. 

- Trin 2: Brug StorSimple øjebliksbillede Manager til at konfigurere sikkerhedskopi.

### <a name="prerequisites"></a>Forudsætninger

Inden du går i gang:

- Sørg for, at StorSimple enhed og host computeren er konfigureret korrekt. Gå til [Implementer enheden lokale StorSimple](storsimple-deployment-walkthrough-u2.md)kan finde flere oplysninger.

- Installere og konfigurere StorSimple øjebliksbillede Manager. Gå til [Installere StorSimple øjebliksbillede Manager](storsimple-snapshot-manager-deployment.md)kan finde flere oplysninger.

- Konfigurere to enheder på StorSimple enheden. (I eksemplerne, de tilgængelige enheder er **Disk 1** og **2 for disken**). 

### <a name="step-1-use-disk-management-to-create-a-dynamic-mirrored-volume"></a>Trin 1: Brug Diskadministration til at oprette en dynamisk spejlet enhed

Disk Management er et systemværktøj til styring af harddiske og enheder eller partitioner, de indeholder. Du kan finde flere oplysninger om administration af diskplads ved at gå til [Disk Management](https://technet.microsoft.com/library/cc770943.aspx) på Microsoft TechNet-webstedet.

#### <a name="to-create-a-dynamic-mirrored-volume"></a>Hvis du vil oprette et dynamisk spejlvendt lydstyrken

1. Sådan startes Diskadministration, kan du bruge en af følgende indstillinger: 

   - Åbne dialogboksen **Kør** , Skriv **Diskmgmt.msc**, og tryk på Enter.

   - Start Server Manager, udvid noden **lagerplads** , og vælg derefter **Disk Management**. 

   - Start **Administration**, udvid noden **Computer Management** , og vælg derefter **Disk Management**. 

2. Sørg for, at du har to enheder, der er tilgængelige på StorSimple enhed. (I eksemplet med de tilgængelige enheder er **Disk 1** og **2 for disken**). 

3. Højreklik på **Disk 1** i vinduet Diskadministration i den højre kolonne i den nederste rude, og vælg **Ny spejlvendt lydstyrken**. 

    ![Ny spejlet lydstyrken](./media/storsimple-snapshot-manager-manage-volumes/HCS_SSM_New_mirrored_volume.png) 

4. Klik på **Næste**side i guiden **Ny spejlet lydstyrken** .

5. Vælg **Disk 2** i ruden **markeret** på siden **Vælg diske** , klik på **Tilføj**, og klik derefter på **Næste**. 

6. Accepter standardindstillingerne, og klik derefter på **Næste**på siden **Tildel Drive Letter eller sti** . 

7. Vælg **64 KB**i feltet **Allokering enhedsstørrelse** på siden **Format lydstyrken** . Markér afkrydsningsfeltet **udføre hurtig formatering** , og klik derefter på **Næste**. 

8. Gennemse dine indstillinger, og klik derefter på **Udfør**på siden **Fuldfører nye spejlvendt lydstyrken** . 

9. Der vises en meddelelse til at angive, at den grundlæggende disk konverteres til en dynamisk disk. Klik på **Ja**.

    ![Dynamisk disk konvertering meddelelse](./media/storsimple-snapshot-manager-manage-volumes/HCS_SSM_Disk_management_msg.png) 

10. Kontrollér, at Disk 1 og 2 for Disk vises som dynamiske spejlede enheder i Diskadministration. (**Dynamisk** skal vises i statuskolonnen, og skal ændre Søjlefarve kapacitet til rød, der angiver et spejlvendt lydstyrke). 

    ![Disk Management spejlvendt dynamiske diske](./media/storsimple-snapshot-manager-manage-volumes/HCS_SSM_Verify_dynamic_disks_2.png) 
 
### <a name="step-2-use-storsimple-snapshot-manager-to-configure-backup"></a>Trin 2: Brug StorSimple øjebliksbillede Manager til at konfigurere sikkerhedskopi

Brug følgende fremgangsmåde til at konfigurere en dynamisk spejlet beholdning, og derefter starte en sikkerhedskopi med det samme eller oprette en politik for planlagte sikkerhedskopier.

#### <a name="to-configure-backup-of-a-dynamic-mirrored-volume"></a>Hvis du vil konfigurere sikkerhedskopi af et dynamisk spejlvendt lydstyrken

1. Klik på ikonet StorSimple øjebliksbillede Manager på din computer. Vinduet StorSimple øjebliksbillede Manager åbnes. 

2. Højreklik på noden **enheder** i ruden **område** og vælge **Scan enheder**. Når du er færdig med søgningen, skal en liste over enheder vises i ruden **resultater** . Dynamisk spejlede lydstyrken er angivet som en enkelt enhed. 

3. Højreklik på det dynamiske spejlede drev i ruden **resultater** , og klik derefter på **Opret lydstyrken gruppe**. 

4. Skriv et navn til gruppen lydstyrken i dialogboksen **Opret lydstyrken gruppe** , tildele dynamiske spejlede lydstyrken til denne gruppe, og klik derefter på **OK**. 

5. Udvid noden **Lydstyrken grupper** i ruden **område** . Gruppen ny lydstyrken skal vises under noden **Lydstyrken grupper** . 

6. Højreklik på gruppenavnet lydstyrken. 

    - For at starte en interaktiv (efter behov) sikkerhedskopieringsjob skal du klikke på **Tage sikkerhedskopiering**. 

    - Klik på **Opret sikkerhedskopieringspolitik**for at planlægge en automatisk sikkerhedskopiering. På siden **Generelt** skal du vælge gruppen lydstyrken på listen. Angiv tidsplan oplysninger på siden **tidsplan** . Når du er færdig, skal du klikke på **OK**. 

7. Du kan overvåge sikkerhedskopieringen, når det køres. Udvid noden **job** i ruden **omfang** og klik derefter på **kører**, oplysninger om job vises i ruden **resultater** . Når sikkerhedskopieringen er færdig, overføres oplysninger til joblisten **seneste 24** timer. 

## <a name="next-steps"></a>Næste trin

- Lær, hvordan du [bruger StorSimple øjebliksbillede Manager til at administrere din StorSimple løsning](storsimple-snapshot-manager-admin.md).
- Lær, hvordan du [bruger StorSimple øjebliksbillede Manager til at oprette og administrere lydstyrken grupper](storsimple-snapshot-manager-manage-volume-groups.md).

<!--Reference links-->
[1]: https://msdn.microsoft.com/library/ee338480(v=ws.10).aspx
