<properties 
   pageTitle="Administrere din StorSimple sikkerhedskopiering politikker | Microsoft Azure"
   description="Beskriver, hvordan du kan bruge tjenesten StorSimple Manager til at oprette og administrere manuel sikkerhedskopier, sikkerhedskopiering tidsplaner og sikkerhedskopiering opbevaring."
   services="storsimple"
   documentationCenter="NA"
   authors="SharS"
   manager="carmonm"
   editor=""/>
<tags 
   ms.service="storsimple"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="TBD"
   ms.date="05/10/2016"
   ms.author="v-sharos"/>

# <a name="use-the-storsimple-manager-service-to-manage-backup-policies-update-2"></a>Bruge tjenesten StorSimple Manager til at administrere sikkerhedskopiering politikker (opdatering 2)

[AZURE.INCLUDE [storsimple-version-selector-manage-backup-policies](../../includes/storsimple-version-selector-manage-backup-policies.md)]

## <a name="overview"></a>Oversigt

Dette selvstudium forklarer, hvordan du bruge siden StorSimple Manager **Sikkerhedskopiering politikker** til at styre sikkerhedskopiering processer og sikkerhedskopiering opbevaring for dine StorSimple drev. Den beskriver også hvordan du fuldfører en sikkerhedskopi manuelt.

Når du sikkerhedskopierer en disk, kan du vælge at oprette en lokal snapshot eller et snapshot af en sky. Hvis du sikkerhedskopierer et lokalt fastgjorte lydstyrken, anbefaler vi, at du angiver et snapshot af en sky. Tage et stort antal lokale øjebliksbillede af et lokalt fastgjorte lydstyrken, der er forbundet til et datasæt, der har en masse transportspand medfører en situation, kan du hurtigt løbe tør for lokale mellemrum. Hvis du vælger at tage lokale øjebliksbilleder, anbefaler vi, at du tager færre daglige snapshots bevare dem sikkerhedskopiere den seneste tilstand for en dag, og derefter slette dem.

Hvis du følger et skyen øjebliksbillede af et lokalt fastgjorte lydstyrken, kan du kopiere de ændrede data i skyen, hvor det er deduplicated og komprimeres. 

## <a name="the-backup-policies-page"></a>Siden sikkerhedskopi politikker

Siden **Sikkerhedskopiering politikker** kan du administrere politikker for sikkerhedskopiering og planlægge lokale og snapshots i skyen. (Sikkerhedskopiering politikker bruges til at konfigurere sikkerhedskopiering tidsplaner og sikkerhedskopiering opbevaring for en samling af enheder). Sikkerhedskopiering politikker gør det muligt at tage et øjebliksbillede af flere enheder på samme tid. Dette betyder, at de sikkerhedskopier, der er oprettet af en politik for sikkerhedskopiering bliver nedbrud ensartet kopier. Siden **Sikkerhedskopiering politikker** viser de ekstra politikker, deres type, de tilknyttede enheder, antallet af sikkerhedskopier bevares og mulighed for at aktivere disse politikker.

Siden **Sikkerhedskopiering politikker** kan du også filtrere de eksisterende sikkerhedskopiering politikker ved en eller flere af følgende felter:

- **Navn på politik** – det navn, der er tilknyttet politikken. De forskellige typer politikker omfatter:

   - Planlagte politikker, der oprettes eksplicit af brugeren.
   - Automatisk politikker, der oprettes, når standard sikkerhedskopien for denne indstilling for lydstyrke er aktiveret på tidspunktet for oprettelse af lydstyrken. Disse politikker er navngivet som *VolumeName*_Default hvor *VolumeName* refererer til navnet på StorSimple lydstyrken af brugeren i portalen Azure klassisk. De politikker, der automatisk resultere i daglige skyen snapshot startende fra 22:30 enhed tid.
   - Importerede politikker, som oprindeligt blev oprettet i StorSimple Snapshot Manager. Disse indeholder et mærke, der beskriver værten StorSimple Snapshot Manager, som politikkerne, der er importeret fra.

- **Enheder** – de enheder, der er tilknyttet politikken. Alle de enheder, der er knyttet til en politik for sikkerhedskopiering er grupperet sammen, når der oprettes sikkerhedskopier.

- **Sidst udførte sikkerhedskopiering** – datoen og klokkeslættet for den seneste vellykkede sikkerhedskopiering, der blev foretaget med denne politik.

- **Næste sikkerhedskopiering** – datoen og klokkeslættet for den næste planlagte sikkerhedskopi, der startes af denne politik.

- **Tidsplaner** – antallet af planer, der er knyttet til politikken sikkerhedskopiering.

De ofte anvendte handlinger, du kan udføre fra denne side er:

- Tilføje en ekstra politik 
- Tilføje eller ændre en tidsplan 
- Slette en politik for sikkerhedskopiering 
- Tage en sikkerhedskopi manuelt 
- Oprette en brugerdefineret sikkerhedskopiering politik med flere enheder og tidsplaner 

## <a name="add-a-backup-policy"></a>Tilføje en ekstra politik

Tilføje en ekstra politik for at planlægge automatisk dine sikkerhedskopier. Udfør følgende trin i Azure klassisk portalen for at tilføje en ekstra politik for enheden StorSimple. Når du har tilføjet politikken, kan du angive en tidsplan (se [tilføje eller ændre en tidsplan](#add-or-modify-a-schedule)).

[AZURE.INCLUDE [storsimple-add-backup-policy-u2](../../includes/storsimple-add-backup-policy-u2.md)]

![Video er til rådighed](./media/storsimple-manage-backup-policies-u2/Video_icon.png) **Video, der er tilgængelige**

Hvis du vil se en video, der viser, hvordan du kan oprette et lokalt eller skybaseret sikkerhedskopiering politik, skal du klikke på [her](https://azure.microsoft.com/documentation/videos/create-storsimple-backup-policies/).


## <a name="add-or-modify-a-schedule"></a>Tilføje eller ændre en tidsplan

Du kan tilføje eller ændre en tidsplan, der er knyttet til en eksisterende sikkerhedskopiering politik på enheden StorSimple. Udfør følgende trin i Azure klassisk portalen for at tilføje eller ændre en tidsplan.

[AZURE.INCLUDE [storsimple-add-modify-backup-schedule](../../includes/storsimple-add-modify-backup-schedule-u2.md)]

## <a name="delete-a-backup-policy"></a>Slette en politik for sikkerhedskopiering

Udfør følgende trin i Azure klassisk portalen for at slette en sikkerhedskopi politik på enheden StorSimple.

[AZURE.INCLUDE [storsimple-delete-backup-policy](../../includes/storsimple-delete-backup-policy.md)]


## <a name="take-a-manual-backup"></a>Tage en sikkerhedskopi manuelt

Udfør følgende trin i Azure klassisk portalen for at oprette en (manuel) sikkerhedskopi efter behov for en enkelt enhed.

[AZURE.INCLUDE [storsimple-create-manual-backup](../../includes/storsimple-create-manual-backup.md)]

## <a name="create-a-custom-backup-policy-with-multiple-volumes-and-schedules"></a>Oprette en brugerdefineret sikkerhedskopiering politik med flere enheder og tidsplaner

Udfør følgende trin i Azure klassisk portalen for at oprette en brugerdefineret sikkerhedskopiering politik, der indeholder flere enheder og tidsplaner.

[AZURE.INCLUDE [storsimple-create-custom-backup-policy](../../includes/storsimple-create-custom-backup-policy-u2.md)]


## <a name="next-steps"></a>Næste trin

Lær mere om [ved hjælp af tjenesten StorSimple Manager for at administrere din StorSimple enhed](storsimple-manager-service-administration.md).
