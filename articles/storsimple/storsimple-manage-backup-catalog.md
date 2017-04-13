<properties 
   pageTitle="Administrere StorSimple sikkerhedskopiering kataloget | Microsoft Azure"
   description="Forklarer, hvordan du bruger siden StorSimple Manager kataloget sikkerhedskopiering til listen, markere og slette sikkerhedskopier være tilgængelige for et drev."
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
   ms.date="04/28/2016"
   ms.author="v-sharos" />

# <a name="use-the-storsimple-manager-service-to-manage-your-backup-catalog"></a>Bruge tjenesten StorSimple Manager til at administrere din sikkerhedskopiering katalog

## <a name="overview"></a>Oversigt

Siden StorSimple Manager **Kataloget sikkerhedskopiering** viser de ekstra sæt, der er oprettet, når manuel eller planlagte sikkerhedskopier er taget. Du kan bruge denne side til at vise alle sikkerhedskopier til en politik for sikkerhedskopiering eller et drev, Vælg eller slette sikkerhedskopier eller bruge en sikkerhedskopi til at gendanne eller klone en volumenlicens.

Dette selvstudium forklarer, hvordan du skal du vælge, og slette et sæt sikkerhedskopier. Hvis du vil lære at gendanne enheden fra sikkerhedskopi, skal du gå til [gendanne enheden fra et sæt sikkerhedskopier](storsimple-restore-from-backup-set.md). For at lære at klone en enhed, skal du gå til [Klon en StorSimple lydstyrken](storsimple-clone-volume.md).

![Kataloget sikkerhedskopiering](./media/storsimple-manage-backup-catalog/backupcatalog.png) 

Siden **Kataloget sikkerhedskopiering** giver en forespørgsel for at indsnævre sikkerhedskopien Angiv markering. Du kan filtrere de ekstra sæt, der er hentet, baseret på følgende parametre:

- **Enhed** – den enhed, hvor sikkerhedskopien blev oprettet.

- **Politik for sikkerhedskopiering eller lydstyrken** – sikkerhedskopiering politik eller lydstyrken, der er knyttet til dette sikkerhedskopiering sæt.

- **Fra og til** -dato- og klokkeslætsintervallet da sikkerhedskopien blev oprettet.

De filtrerede sikkerhedskopiering sæt er derefter i tabelform baseret på følgende attributter:

- **Navn** – navnet på sikkerhedskopien politik eller lydstyrken, der er knyttet til sikkerhedskopien.

- **Størrelse** – den faktiske størrelse på sikkerhedskopien.

- **Oprettet på** – dato og klokkeslæt, hvor sikkerhedskopierne blev oprettet. 

- **Type** – sikkerhedskopi sæt kan være lokale snapshots eller snapshots i skyen. En lokal snapshot er en sikkerhedskopi af alle dine beholdning-data, der er gemt lokalt på enheden, mens et snapshot af en sky refererer til sikkerhedskopien af lydstyrken data, der findes i skyen. Lokale øjebliksbilleder giver hurtigere adgang, mens skyen snapshots er valgt data af tolerance.

- **Startet af** – sikkerhedskopier af kan startes automatisk, efter en tidsplan eller manuelt af en bruger. Du kan bruge en politik for sikkerhedskopiering til at planlægge sikkerhedskopier. Du kan også bruge indstillingen **tage ekstra** tage en sikkerhedskopi manuelt.

## <a name="list-backup-sets-for-a-volume"></a>Listen sikkerhedskopi indstiller for en volumenlicens
 
Udfør følgende trin for at få vist alle sikkerhedskopier af en enhed.

#### <a name="to-list-backup-sets"></a>På listen sikkerhedskopiering sæt

1. Klik på fanen **sikkerhedskopi katalog** på siden StorSimple Manager service.

2. Filtrere valgene på følgende måde:

    1. Vælg den relevante enhed.

    2. Vælg en enhed for at få vist den tilhørende sikkerhedskopier af i på rullelisten.

    3. Angiv tidsintervallet.

    4. Klik på ikonet Kontrollér ![Ikonet](./media/storsimple-manage-backup-catalog/HCS_CheckIcon.png) at udføre denne forespørgsel.
 
    De sikkerhedskopier, der er knyttet til den valgte enhed skal vises på listen over sikkerhedskopier være tilgængelige.

## <a name="select-a-backup-set"></a>Vælg et sæt sikkerhedskopier

Udfør følgende trin for at vælge en sikkerhedskopi, der er angivet for en lydstyrken eller sikkerhedskopiering politik.

#### <a name="to-select-a-backup-set"></a>Markere et sæt sikkerhedskopier

1. Klik på fanen **sikkerhedskopi katalog** på siden StorSimple Manager service.

2. Filtrere valgene på følgende måde:

    1. Vælg den relevante enhed.

    2. På rullelisten Vælg lydstyrken eller sikkerhedskopi politikken for den sikkerhedskopi, du vil markere.

    3. Angiv tidsintervallet.

    4. Klik på ikonet Kontrollér ![Ikonet](./media/storsimple-manage-backup-catalog/HCS_CheckIcon.png) at udføre denne forespørgsel.

    Sikkerhedskopier af, der er knyttet til den valgte enhed eller sikkerhedskopiering politik skal vises på listen over sikkerhedskopier være tilgængelige.

3. Vælg, og udvid et sæt sikkerhedskopier. Indstillingerne til at **gendanne** og **slette** vises nederst på siden. Du kan udføre nogle af disse handlinger på det ekstra sæt, du har valgt.

## <a name="delete-a-backup-set"></a>Slette et sæt sikkerhedskopier

Slette en sikkerhedskopi, når du ikke længere vil bevare de data, der er knyttet til den. Udfør følgende trin for at slette et sæt sikkerhedskopier.

#### <a name="to-delete-a-backup-set"></a>Slette et sæt sikkerhedskopier

1. Klik på **Sikkerhedskopiér katalog fanen**på siden StorSimple Manager service.

2. Filtrere valgene på følgende måde:

    1. Vælg den relevante enhed.

    2. På rullelisten Vælg lydstyrken eller sikkerhedskopi politikken for den sikkerhedskopi, du vil markere.

    3. Angiv tidsintervallet.

    4. Klik på ikonet Kontrollér ![Ikonet](./media/storsimple-manage-backup-catalog/HCS_CheckIcon.png) at udføre denne forespørgsel.

    Sikkerhedskopier af, der er knyttet til den valgte enhed eller sikkerhedskopiering politik skal vises på listen over sikkerhedskopier være tilgængelige.

3. Vælg, og udvid et sæt sikkerhedskopier. Indstillingerne til at **gendanne** og **slette** vises nederst på siden. Klik på **Slet**.

4. Du får besked, når sletningen er i gang, og når den er færdig. Når sletningen er færdigt, skal du opdatere forespørgslen på denne side. Slettede sæt vises ikke længere på listen over sikkerhedskopier være tilgængelige.

## <a name="next-steps"></a>Næste trin

- Lær, hvordan du [bruger kataloget til sikkerhedskopiering for at gendanne enheden fra et sæt sikkerhedskopier](storsimple-restore-from-backup-set.md).

- Lær, hvordan du [bruger tjenesten StorSimple Manager for at administrere din StorSimple enhed](storsimple-manager-service-administration.md).
