<properties 
   pageTitle="Gendanne en StorSimple lydstyrken fra sikkerhedskopi | Microsoft Azure"
   description="Forklarer, hvordan du bruger siden StorSimple Manager kataloget sikkerhedskopiering til at gendanne en StorSimple lydstyrken fra et sæt sikkerhedskopier."
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

# <a name="restore-a-storsimple-volume-from-a-backup-set-update-2"></a>Gendanne en StorSimple lydstyrken fra et ekstra sæt (opdatering 2)

[AZURE.INCLUDE [storsimple-version-selector-restore-from-backup](../../includes/storsimple-version-selector-restore-from-backup.md)]

## <a name="overview"></a>Oversigt

Siden **Kataloget sikkerhedskopiering** viser de ekstra sæt, der er oprettet, når manuel eller automatisk sikkerhedskopiering er taget. Du kan bruge denne side til at vise alle sikkerhedskopier til en politik for sikkerhedskopiering eller et drev, Vælg eller slette sikkerhedskopier eller bruge en sikkerhedskopi til at gendanne eller klone en volumenlicens.

 ![Lav sikkerhedskopier af katalogside](./media/storsimple-restore-from-backup-set-u2/restore.png)

Dette selvstudium forklarer, hvordan du bruger siden **Kataloget sikkerhedskopiering** til at gendanne enheden fra et sæt sikkerhedskopier.

Du kan gendanne en lydstyrken fra et lokalt eller skybaseret sikkerhedskopi. I begge tilfælde viser gendannelsen lydstyrken online straks, men data er hentet i baggrunden. 

Før du starter en gendannelse, skal du være opmærksom på følgende:

- **Du skal træffe lydstyrken offline** – Hent lydstyrken offline på både værten og enheden, før du at starte gendannelsen. Selvom gendannelsen bringer automatisk lydstyrken online på enheden, skal du manuelt tage enheden online på værten. Du kan tage lydstyrken online på værten, så snart lydstyrken er online på enheden. (Du behøver ikke vente, indtil gendannelsen er afsluttet). For procedurer, skal du gå til [tage en lydstyrken offline](storsimple-manage-volumes-u2.md#take-a-volume-offline).

- **Lydstyrken type efter Gendan** – slettede enheder gendannes baseret på typen i snapshot; Det vil sige, enheder, der blev lokalt fastgjort gendannes som lokalt fastgjorte enheder og enheder, der blev lagdelt gendannes som lagdelte enheder.

    For eksisterende enheder tilsidesætter den aktuelle Brug type lydstyrken den type, der er gemt i snapshot. Eksempelvis hvis du gendanner en enhed fra et øjebliksbillede, der blev foretaget, da typen beholdning blev niveauer, og at lydstyrken type fastgøres nu lokalt (på grund af en konvertering handling, der blev udført), gendannes derefter lydstyrken som et lokalt fastgjorte lydstyrken. Hvis et eksisterende lokalt fastgjorte lydstyrken blev udvidet og efterfølgende gendannet fra en ældre øjebliksbillede, der udføres, når lydstyrken er blevet mindre, bevarer gendannede lydstyrken på samme måde, den aktuelle udvidede størrelse.

    Du kan ikke konvertere en enhed fra en lagdelte lydstyrken til et lokalt fastgjorte lydstyrken eller fra et lokalt fastgjorte lydstyrken til en lagdelte enhed, mens lydstyrken genoprettes. Vent, indtil gendannelsen er færdig, og derefter kan du konvertere lydstyrken til en anden type. Gå til [ændre lydstyrken](storsimple-manage-volumes-u2.md#change-the-volume-type)for oplysninger om konvertering af en volumenlicens. 

- **Enhedens størrelse, afspejles i gendannede lydstyrken** – dette er en vigtig overvejelse, hvis du gendanner en lokalt fastgjorte enhed, der er blevet slettet (fordi lokalt fastgjorte enheder er fuldt klargjort). Sørg for, at du har tilstrækkelig plads, før du forsøger at gendanne en lokalt fastgjorte enhed, der blev slettet. 

- **Du kan udvide et drev, mens den genoprettes** – Vent, indtil gendannelsen er fuldført, før du forsøger at udvide lydstyrken. Finde oplysninger om at udvide en lydstyrken, gå til [Rediger en volumenlicens](storsimple-manage-volumes-u2.md#modify-a-volume).

- **Du kan udføre en sikkerhedskopi, mens du gendanner en lokal enhed** – procedurer gå til [Brug tjenesten StorSimple Manager til at administrere politikker for sikkerhedskopiering](storsimple-manage-backup-policies.md).

- **Du kan annullere en gendannelse** – Hvis du annullerer Gendannelsesjobbet, og klik derefter lydstyrken samles tilbage til den tilstand, den var i, før du selv startede gendannelsen. For procedurer, skal du gå til [annullere et job](storsimple-manage-jobs-u2.md#cancel-a-job).

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
- **Startet af** – sikkerhedskopier af kan startes automatisk, efter en tidsplan eller manuelt af en bruger. (Du kan bruge en politik for sikkerhedskopiering til at planlægge sikkerhedskopier. Du kan også bruge indstillingen **tage ekstra** tage en sikkerhedskopi af interaktive.)

## <a name="how-to-restore-your-storsimple-volume-from-a-backup"></a>Sådan gendannes StorSimple lydstyrken fra en sikkerhedskopi

Du kan bruge siden **Kataloget sikkerhedskopiering** til at gendanne enheden StorSimple fra en bestemt sikkerhedskopi. Husk på, men, gendanne en lydstyrken nulstilles lydstyrken til den tilstand, der var gældende, da som sikkerhedskopien er taget. De data, der blev tilføjet efter sikkerhedskopieringen, går tabt.

> [AZURE.WARNING] Gendanne fra en sikkerhedskopi erstatter de eksisterende enheder fra sikkerhedskopien. Dette kan medføre tab af data, der er skrevet efter som sikkerhedskopien er taget.

### <a name="to-restore-your-volume"></a>Du kan gendanne lydstyrken

1. Klik på fanen **sikkerhedskopi katalog** på siden StorSimple Manager service.

    ![Kataloget sikkerhedskopiering](./media/storsimple-restore-from-backup-set-u2/restore.png)

2. Vælg en sikkerhedskopi, angive på følgende måde:
  1. Vælg den relevante enhed.
  2. På rullelisten Vælg lydstyrken eller sikkerhedskopi politikken for den sikkerhedskopi, du vil markere.
  3. Angiv tidsintervallet.
  4. Klik på ikonet Kontrollér ![Markér ikonet](./media/storsimple-restore-from-backup-set-u2/HCS_CheckIcon.png) at udføre denne forespørgsel.
 
    Sikkerhedskopierne, der er knyttet til den valgte enhed eller sikkerhedskopiering politik skal vises på listen over sikkerhedskopier være tilgængelige.

3. Udvid sikkerhedskopien angive at få vist de tilknyttede enheder. Disse enheder skal tages offline på værten og enheden, før du kan gendanne dem. Få adgang til enhederne på siden **Lydstyrken beholdere** , og følg derefter trinnene i [tage en lydstyrken offline](storsimple-manage-volumes-u2.md#take-a-volume-offline) tage dem offline.

    > [AZURE.IMPORTANT] Sørg for, at du har taget enhederne offline på værten først, før du udfører enhederne offline på enheden. Hvis du ikke foretager enhederne offline på værten, kan det potentielt føre til beskadigelse af data.

4. Gå tilbage til fanen **Sikkerhedskopiering katalog** , og vælg et sæt sikkerhedskopier.

5. Klik på **Gendan** i bunden af siden.

6. Du bliver bedt om at bekræfte. Gennemse oplysningerne om Gendan, og derefter markere afkrydsningsfeltet bekræftelse.

    ![Bekræftelsessiden](./media/storsimple-restore-from-backup-set-u2/ConfirmRestore.png)

7. Klik på ikonet Kontrollér ![markere ikonet](./media/storsimple-restore-from-backup-set-u2/HCS_CheckIcon.png). Dette starter en Gendan sag, du kan få vist ved at åbne siden **job** . 

8. Når gendannelsen er fuldført, kan du kontrollere, at indholdet af dine drev erstattes af enheder fra sikkerhedskopien.

![Video er til rådighed](./media/storsimple-restore-from-backup-set-u2/Video_icon.png) **Video, der er tilgængelige**

Hvis du vil se en video, der viser, hvordan du kan bruge klonen og gendanne funktioner i StorSimple til at gendanne slettede filer, skal du klikke på [her](https://azure.microsoft.com/documentation/videos/storsimple-recover-deleted-files-with-storsimple/).

## <a name="if-the-restore-fails"></a>Hvis indstillingen Gendan mislykkes

Du modtager en besked, hvis gendannelsen mislykkes for en eller anden grund. Hvis dette sker, Opdater listen sikkerhedskopiering for at bekræfte, at sikkerhedskopieringen er stadig er gyldig. Hvis sikkerhedskopien er gyldig, og du gendanner fra skyen, kan derefter forbindelsesproblemer forårsage problemet. 

Tage lydstyrken offline på værten for at fuldføre gendannelsen, og prøv igen gendannelsen. Bemærk, at eventuelle ændringer til lydstyrke data, der blev udført under gendannelsen, går tabt.

## <a name="next-steps"></a>Næste trin

- Lær, hvordan du kan [administrere StorSimple enheder](storsimple-manage-volumes-u2.md).

- Lær, hvordan du [bruger tjenesten StorSimple Manager for at administrere din StorSimple enhed](storsimple-manager-service-administration.md).
