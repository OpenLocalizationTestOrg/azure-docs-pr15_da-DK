<properties 
   pageTitle="Gendanne en StorSimple lydstyrken fra sikkerhedskopi | Microsoft Azure"
   description="Forklarer, hvordan du bruger siden StorSimple Manager kataloget sikkerhedskopiering til at gendanne en StorSimple lydstyrken fra et sæt sikkerhedskopier."
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
   ms.date="08/17/2016"
   ms.author="alkohli" />

# <a name="restore-a-storsimple-volume-from-a-backup-set"></a>Gendanne en StorSimple lydstyrken fra et sæt sikkerhedskopier

[AZURE.INCLUDE [storsimple-version-selector-restore-from-backup](../../includes/storsimple-version-selector-restore-from-backup.md)]

## <a name="overview"></a>Oversigt

Siden **Kataloget sikkerhedskopiering** viser de ekstra sæt, der er oprettet, når manuel eller automatisk sikkerhedskopiering er taget. Du kan bruge denne side til at vise alle sikkerhedskopier til en politik for sikkerhedskopiering eller et drev, Vælg eller slette sikkerhedskopier eller bruge en sikkerhedskopi til at gendanne eller klone en volumenlicens.

 ![Lav sikkerhedskopier af katalogside](./media/storsimple-restore-from-backup-set/HCS_BackupCatalog.png)

Dette selvstudium forklarer, hvordan du bruger siden **Kataloget sikkerhedskopiering** til at gendanne en lydstyrken på din enhed fra et sæt sikkerhedskopier.

## <a name="how-to-use-the-backup-catalog"></a>Sådan bruger du kataloget til sikkerhedskopiering 

Siden **Kataloget sikkerhedskopiering** indeholder en forespørgsel, der hjælper dig med at indsnævre sikkerhedskopien Angiv markering. Du kan filtrere de ekstra sæt, der skal hentes baseret på følgende parametre:

- **Enhed** – den enhed, hvor sikkerhedskopien blev oprettet.
- **Politik for sikkerhedskopiering** eller **lydstyrken** – sikkerhedskopiering politik eller lydstyrken, der er knyttet til dette sikkerhedskopiering sæt.
- **Fra** og **til** -dato- og klokkeslætsintervallet da sikkerhedskopien blev oprettet.

De filtrerede sikkerhedskopiering sæt er derefter i tabelform baseret på følgende attributter:

- **Navn** – navnet på sikkerhedskopien politik eller lydstyrken, der er knyttet til sikkerhedskopien.
- **Størrelse** – den faktiske størrelse på sikkerhedskopien.
- **Oprettet på** – dato og klokkeslæt, hvor sikkerhedskopierne blev oprettet. 
- **Type** – sikkerhedskopi sæt kan være lokale snapshots eller snapshots i skyen. En lokal snapshot er en sikkerhedskopi af alle dine beholdning-data, der er gemt lokalt på enheden, mens et snapshot af en sky refererer til sikkerhedskopien af lydstyrken data, der findes i skyen. Lokale øjebliksbilleder giver hurtigere adgang, mens skyen snapshots er valgt data af tolerance.
- **Startet af** – sikkerhedskopierne kan startes automatisk, efter en tidsplan eller manuelt ved at en bruger. (Du kan bruge en politik for sikkerhedskopiering til at planlægge sikkerhedskopier. Du kan også bruge indstillingen **tage ekstra** tage en sikkerhedskopi af interaktive.)

## <a name="how-to-restore-your-storsimple-volume-from-a-backup"></a>Sådan gendannes StorSimple lydstyrken fra en sikkerhedskopi

Du kan bruge siden **Kataloget sikkerhedskopiering** til at gendanne enheden StorSimple fra en bestemt sikkerhedskopi. 

> [AZURE.WARNING] Gendanne fra en sikkerhedskopi erstatter de eksisterende enheder fra sikkerhedskopien. Dette kan medføre tab af data, der er skrevet efter som sikkerhedskopien er taget.

Før du starter en gendannelse på en enhed, kan du sikre dig, at lydstyrken er offline. Du skal først at kaste lydstyrken offline på værten og klik derefter på enheden. Følg trinnene i [tage en lydstyrken offline](storsimple-manage-volumes.md#take-a-volume-offline). Udfør følgende trin for at gendanne en enhed fra et sæt sikkerhedskopier.

### <a name="to-restore-from-a-backup-set"></a>Gendanne fra et sæt sikkerhedskopier

1. Klik på fanen **sikkerhedskopi katalog** på siden StorSimple Manager service.

    ![Kataloget sikkerhedskopiering](./media/storsimple-restore-from-backup-set/HCS_Restore.png)

2. Vælg en sikkerhedskopi, angive på følgende måde:
  1. Vælg den relevante enhed.
  2. På rullelisten Vælg lydstyrken eller sikkerhedskopi politikken for den sikkerhedskopi, du vil markere.
  3. Angiv tidsintervallet.
  4. Klik på ikonet Kontrollér ![Markér ikonet](./media/storsimple-restore-from-backup-set/HCS_CheckIcon.png) at udføre denne forespørgsel.
 
    Sikkerhedskopier af, der er knyttet til den valgte enhed eller sikkerhedskopiering politik skal vises på listen over sikkerhedskopier være tilgængelige.

3. Udvid sikkerhedskopien angive at få vist de tilknyttede enheder. Disse enheder skal tages offline på værten og enheden, før du kan gendanne dem. Følg trinnene i [tage en lydstyrken offline](storsimple-manage-volumes.md#take-a-volume-offline).

    >  [AZURE.IMPORTANT] Sørg for, at du har taget enhederne offline på værten først, før du udfører enhederne offline på enheden. Hvis du ikke foretager enhederne offline på værten, kan det potentielt føre til beskadigelse af data.

4. Vælg et sæt sikkerhedskopier. Klik på **Gendan** i bunden af siden.

6. Du bliver bedt om at bekræfte. 

    ![Bekræftelsessiden](./media/storsimple-restore-from-backup-set/HCS_ConfirmRestore.png)

7. Gennemse oplysningerne i Gendan, og klik på ikonet Kontrollér ![markere ikonet](./media/storsimple-restore-from-backup-set/HCS_CheckIcon.png). Dette starter en Gendan sag, du kan få vist ved at åbne siden **job** . 

8. Når gendannelsen er fuldført, kan du kontrollere, at indholdet af dine drev erstattes af enheder fra sikkerhedskopien.

![Video er til rådighed](./media/storsimple-restore-from-backup-set/Video_icon.png) **Video, der er tilgængelige**

Hvis du vil se en video, der viser, hvordan du kan bruge klonen og gendanne funktioner i StorSimple til at gendanne slettede filer, skal du klikke på [her](https://azure.microsoft.com/documentation/videos/storsimple-recover-deleted-files-with-storsimple/).

## <a name="next-steps"></a>Næste trin

- Lær, hvordan du kan [administrere StorSimple enheder](storsimple-manage-volumes.md).

- Lær, hvordan du [bruger tjenesten StorSimple Manager for at administrere din StorSimple enhed](storsimple-manager-service-administration.md).
