<properties 
   pageTitle="StorSimple virtuelle matrix sikkerhedskopiering selvstudium | Microsoft Azure"
   description="Beskriver, hvordan du sikkerhedskopiere StorSimple virtuelle matrix aktier og enheder."
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
   ms.date="06/07/2016"
   ms.author="alkohli" />

# <a name="back-up-your-storsimple-virtual-array"></a>Sikkerhedskopiere din StorSimple virtuelle matrix

## <a name="overview"></a>Oversigt 

Dette selvstudium gælder for Microsoft Azure StorSimple virtuelle matrix (også kaldet StorSimple lokale virtuelle enheden eller StorSimple virtuelle enheder), der kører marts 2016 generelt tilgængelig (GA) version eller nyere versioner.

StorSimple virtuelle matrixen er en hybrid cloud storage lokale virtuel enhed, der kan konfigureres som en filserver eller en iSCSI-server. Det kan oprette sikkerhedskopier, gendanne fra sikkerhedskopier og udføre enhed failover, hvis det er nødvendigt nedbrud. Når konfigureret som en filserver, kan det også på elementniveau gendannelse. Dette selvstudium beskriver, hvordan bruge Azure klassisk portalen eller webdelen StorSimple brugergrænseflade til at oprette planlagte og manuel sikkerhedskopier af dine StorSimple virtuelle matrix.


## <a name="back-up-shares-and-volumes"></a>Sikkerhedskopiere aktier og enheder

Sikkerhedskopier beskytter punkt-in-time, forbedre mulighed for gendannelse og minimere Gendan sluttidspunkter for aktier og enheder. Du kan sikkerhedskopiere en del eller lydstyrken på enheden StorSimple på to måder: **planlagt** eller **manuelt**. Hver af metoderne, der er beskrevet i følgende afsnit.

> [AZURE.NOTE] I denne udgave oprettes planlagte sikkerhedskopier af en standardpolitik, der kører dagligt på et bestemt tidspunkt og sikkerhedskopierer alle aktier eller enheder på enheden. Det er ikke muligt at oprette brugerdefinerede politikker for planlagte sikkerhedskopier på nuværende tidspunkt.

## <a name="change-the-backup-schedule"></a>Ændre tidsplanen for sikkerhedskopiering

Enheden StorSimple virtuelle har en sikkerhedskopi standardpolitik, der starter på et bestemt tidspunkt på dagen (22:30) og sikkerhedskopierer alle aktier eller enheder på enheden en gang om dagen. Du kan ændre klokkeslættet for, hvor sikkerhedskopiering startes, men hyppigheden og opbevaring (som angiver antallet sikkerhedskopier til at bevare) ikke kan ændres. Under disse sikkerhedskopier er hele virtuelle enheden sikkerhedskopieret; Vi anbefaler derfor, at du planlægger disse sikkerhedskopier for belastet.

Udfør følgende trin i [Azure klassisk portal](https://manage.windowsazure.com/) for at ændre standard sikkerhedskopiering starttidspunktet.

#### <a name="to-change-the-start-time-for-the-default-backup-policy"></a>Ændre starttidspunktet for den standardpolitik for sikkerhedskopiering

1. Gå til fanen enhed **konfiguration** .

2. Angive starttidspunktet for den daglige sikkerhedskopiering i afsnittet **Sikkerhedskopiering** .

3. Klik på **Gem**.

### <a name="take-a-manual-backup"></a>Tage en sikkerhedskopi manuelt

Ud over planlagte sikkerhedskopier, kan du tage et manuelt (efter behov) sikkerhedskopiering når som helst.

#### <a name="to-create-a-manual-on-demand-backup"></a>Oprette en sikkerhedskopi af manuelt (efter behov)

1. Gå til fanen **aktier** eller fanen **enheder** .

2. Klik på **Sikkerhedskopiér alle**nederst på siden. Du bliver bedt om at bekræfte, at du vil tage sikkerhedskopien nu. Klik på ikonet Kontrollér ![markere ikonet](./media/storsimple-ova-backup/image3.png) at fortsætte med at sikkerhedskopieringen.

    ![Sikkerhedskopiering bekræftelse](./media/storsimple-ova-backup/image4.png)

    Du får besked, startes en sikkerhedskopi.

    ![sikkerhedskopi starter](./media/storsimple-ova-backup/image5.png)

    Du får besked, jobbet blev oprettet.

    ![sikkerhedskopieringsjob oprettet](./media/storsimple-ova-backup/image7.png)

3. For at få vist status på sag, skal du klikke på **Periode**.

4. Når sikkerhedskopieringen er færdig, skal du gå til fanen **sikkerhedskopi-katalog** . Du bør se sikkerhedskopien færdige.

    ![Færdige sikkerhedskopi](./media/storsimple-ova-backup/image8.png)

5. Angiv indstillingerne for filter til den relevante enhed, sikkerhedskopiering politik og tidsinterval og derefter klikke på ikonet Kontrollér ![Markér ikonet](./media/storsimple-ova-backup/image3.png).

    Sikkerhedskopien skal vises på listen over sikkerhedskopier være tilgængelige, der vises i kataloget.

## <a name="view-existing-backups"></a>Få vist eksisterende sikkerhedskopier

Udfør følgende trin i Azure klassisk portalen for at få vist eksisterende sikkerhedskopier.

#### <a name="to-view-existing-backups"></a>Til at få vist eksisterende sikkerhedskopier

1. Klik på fanen **sikkerhedskopi katalog** på siden StorSimple Manager service.

2. Vælg en sikkerhedskopi, angive på følgende måde:

    1. Vælg enheden.

    2. I rullelisten, skal du vælge del eller lydstyrken for den sikkerhedskopi, du vil markere.

    3. Angiv tidsintervallet.

    4. Klik på ikonet Kontrollér ![](./media/storsimple-ova-backup/image3.png) at udføre denne forespørgsel.

    Sikkerhedskopierne, der er knyttet til den markerede del eller lydstyrken skal vises på listen over sikkerhedskopier være tilgængelige.

![video_icon](./media/storsimple-ova-backup/video_icon.png) **Video, der er tilgængelige**

Se videoen for at se, hvordan du kan oprette aktier, sikkerhedskopiere aktier og gendanne data i en StorSimple virtuelle matrix.

> [AZURE.VIDEO use-the-storsimple-virtual-array]

## <a name="next-steps"></a>Næste trin

Lær mere om at [administrere din StorSimple virtuelle matrix](storsimple-ova-web-ui-admin.md).
