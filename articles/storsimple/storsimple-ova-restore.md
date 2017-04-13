<properties
   pageTitle="Gendanne fra en sikkerhedskopi af din StorSimple virtuelle matrix"
   description="Lær mere om, hvordan du kan gendanne en sikkerhedskopi af din StorSimple virtuelle matrix."
   services="storsimple"
   documentationCenter="NA"
   authors="alkohli"
   manager="carmonm"
   editor=""/>

<tags
   ms.service="storsimple"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="06/07/2016"
   ms.author="alkohli"/>

# <a name="restore-from-a-backup-of-your-storsimple-virtual-array"></a>Gendanne fra en sikkerhedskopi af din StorSimple virtuelle matrix

## <a name="overview"></a>Oversigt 

I denne artikel gælder til Microsoft Azure StorSimple virtuelle matrix (også kaldet StorSimple lokale virtuel enhed eller StorSimple virtuel enhed), der kører marts 2016 generelt tilgængelig (GA) version eller nyere. I denne artikel beskrives trinvise Sådan gendannes fra et ekstra sæt af din aktier eller enheder på din StorSimple virtuelle matrix. I artiklen indeholder også oplysninger om hvordan på elementniveau gendannelse fungerer på din StorSimple virtuelle matrix, der er konfigureret som en filserver.


## <a name="restore-shares-from-a-backup-set"></a>Gendanne aktier fra et sæt sikkerhedskopier


**Før du forsøger at gendanne aktier, kan du sikre dig, at du har tilstrækkelig plads på kameraet, for at udføre denne handling.** Udfør følgende trin for at gendanne fra en sikkerhedskopi i [Azure klassisk portal](https://manage.windowsazure.com/).

#### <a name="to-restore-a-share"></a>Gendanne en del

1.  Gå til den **sikkerhedskopi katalogfletning**. Filtrer efter tidsinterval til at søge efter sikkerhedskopier og relevante enhed. Klik på ikonet Kontrollér ![](./media/storsimple-ova-restore/image1.png) til at køre forespørgslen.


1.  Klik på listen over sikkerhedskopiering sæt, der vises, og vælg en bestemt sikkerhedskopi. Udvid sikkerhedskopien for at få vist de forskellige aktier under den. Klik på, og vælg en del, du vil gendanne.

2.  Klik på **Gendan som ny**nederst på siden.

3.  Dette starter guiden **gendannelse som ny del** . På siden **Angiv navn og placering** :


    1.  Bekræft enhed kildenavn. Dette skal være den enhed, der indeholder den del, du vil gendanne. Valg af enhed er nedtonet. For at vælge en anden kilde-enhed, skal du afslutte guiden og vælge igen sikkerhedskopien angive igen.

    2.  Angiv et navn til del. Del navnet skal indeholde 3 til 127 tegn.

    3.  Gennemse den størrelse, type og tilladelser, der er knyttet til den del, du forsøger at gendanne. Du vil kunne redigere egenskaberne del via Windows Stifinder, når gendannelsen er fuldført.

    4.  Klik på ikonet Kontrollér ![](./media/storsimple-ova-restore/image1.png).

        ![](./media/storsimple-ova-restore/image9.png)

1.  Når Gendannelsesjobbet er fuldført, gendannelsen starter, og du får vist en ny meddelelse. Hvis du vil overvåge forløbet af Gendan, skal du klikke på **periode**. Dette kommer du til siden **sager** .

2.  Du kan følge udviklingen i Gendannelsesjobbet. Når indstillingen Gendan er 100% færdig, skal du gå tilbage til siden **aktier** på din enhed.

3.  Du kan nu få vist den nye gendannede del på listen over aktier på din enhed. Bemærk som genopretter sker til den samme type dele. En lagdelte del gendannes som lagdelt og et lokalt fastgjorte dele som et lokalt fastgjorte share.

Du har nu afsluttet konfigurationen af enheden og lært at sikkerhedskopiere eller gendanne en del. 


## <a name="restore-volumes-from-a-backup-set"></a>Gendanne enheder fra et sæt sikkerhedskopier


Udfør følgende trin for at gendanne fra en sikkerhedskopi i Azure klassisk portalen. Gendannelseshandlingen gendanner sikkerhedskopien til en ny lydstyrken på den samme virtuelle enhed Du kan ikke gendanne til en anden enhed.

#### <a name="to-restore-a-volume"></a>Gendanne en volumenlicens

1.  Gå til den **sikkerhedskopi katalogfletning**. Filtrer efter tidsinterval til at søge efter sikkerhedskopier og relevante enhed. Klik på ikonet Kontrollér ![](./media/storsimple-ova-restore/image1.png) til at køre forespørgslen.

2.  Klik på listen over sikkerhedskopiering sæt, der vises, og vælg en bestemt sikkerhedskopi. Udvid sikkerhedskopien for at få vist de forskellige enheder under den. Vælge den enhed, du vil gendanne. 

5.  Klik på **Gendan som ny**nederst på siden. Guiden **Gendan som ny lydstyrken** starter.

1.  På siden **Angiv navn og placering** :


    1.  Bekræft enhed kildenavn. Dette skal være den enhed, der indeholder den enhed, du vil gendanne. Valg af enhed er ikke tilgængelig. For at vælge en anden kilde-enhed, skal du afslutte guiden og vælge igen sikkerhedskopien angive igen.

    2.  Angiv et lydstyrken navn for lydstyrken ved at blive gendannet som ny. Lydstyrken navnet skal indeholde 3 til 127 tegn.

    3.  Tryk på pileikonet.

        ![](./media/storsimple-ova-restore/image12.png)

1.  Vælg de relevante ACRs på rullelisten på siden **Angiv hosts, der kan anvende denne enhed** .

    ![](./media/storsimple-ova-restore/image13.png)

1.  Klik på ikonet Kontrollér ![](./media/storsimple-ova-restore/image1.png). Dette vil starte et job Gendan, og du får vist følgende meddelelse, som det er i gang.

2.  Når Gendannelsesjobbet er fuldført, gendannelsen starter, og du får vist en ny meddelelse. Hvis du vil overvåge forløbet af Gendan, skal du klikke på **periode**. Dette kommer du til siden **sager** .

3.  Du kan følge udviklingen i Gendannelsesjobbet. Gå tilbage til siden **enheder** på din enhed.

4.  Du kan nu få vist nye gendannede lydstyrken på listen over enheder på din enhed. Bemærk som genopretter sker til den samme type lydstyrken. En lagdelte lydstyrken gendannes som lagdelt og et lokalt fastgjorte lydstyrken genoprettes som et lokalt fastgjorte lydstyrken.

5.  Når lydstyrken vises online på listen over enheder skal er lydstyrken tilgængelig til brug.  Opdater listen over destinationer i vinduet iSCSI afsenderen egenskaber på værten iSCSI afsenderen.  En ny destination, som indeholder gendannede lydstyrken navnet skal vises som 'inaktiv' under statuskolonnen.

6.  Vælg destinationen, og klik på **Opret forbindelse**.   Når afsenderen har forbindelse til destinationen, bør status skifte til **tilsluttet**. 

7.  I vinduet **Diskadministration** vises de aktiverede enheder, som vist i nedenstående illustration. Højreklik på det opdagede drev (Klik på disknavnet på), og klik derefter på **Online**.

> [AZURE.IMPORTANT] Når du forsøger at gendanne en masse eller en del fra en sikkerhedskopi angive, hvis Gendannelsesjobbet mislykkes, skal en målliste lydstyrken, eller del kan stadig oprettes i portalen. Det er vigtigt, at du sletter denne target lydstyrken eller dele i portalen for at minimere de fremtidige problemer, der følger af dette element.

## <a name="item-level-recovery-ilr"></a>På elementniveau gendannelse (ILR)

Denne version introducerer på elementniveau gendannelse (ILR) på en StorSimple virtuel enhed konfigureret som en filserver. Funktionen gør det muligt at gøre findelt gendannelse af filer og mapper fra en skyen sikkerhedskopi af alle aktierne på StorSimple enheden. Brugere kan hente slettede filer fra seneste sikkerhedskopier ved hjælp af en selvbetjening model.

Hver del har en *.backups* -mappe, der indeholder de seneste sikkerhedskopier. Brugeren kan gå til den ønskede sikkerhedskopi, kopiere relevante filer og mapper fra sikkerhedskopien og gendanne dem. Dette fjerner opkald til administratorer for at gendanne filer fra sikkerhedskopier.

1.  Når du udfører ILR, kan du få vist sikkerhedskopier via Windows Stifinder. Klik på den specifikke del, du vil se på sikkerhedskopien for. Du får vist en *.backups* -mappe, der er oprettet under den del, der gemmer alle sikkerhedskopier. Udvid mappen *.backups* for at få vist sikkerhedskopierne. Mappen får du vist adskilt visning af hele sikkerhedskopien hierarkiet. Denne visning er blevet oprettet efter behov og tager normalt kun et par sekunder at oprette.

    De sidste 5 sikkerhedskopier vises på denne måde og kan bruges til at udføre en på elementniveau gendannelse. 5 seneste sikkerhedskopier omfatter både standard planlagt og manuel sikkerhedskopier.

    
    -   **Planlagt sikkerhedskopier** navngivet som &lt;enhedens navn&gt;DailySchedule-ÅÅÅÅMMDD-TTMMSS-UTC.

    -   **Manuel sikkerhedskopier** navngivet som Ad hoc-ÅÅÅÅMMDD-TTMMSS-UTC.
    
        ![](./media/storsimple-ova-restore/image14.png)

1.  Identificere den sikkerhedskopi, der indeholder den seneste version af filen slettet. Selvom navnet på mappen indeholder et UTC tidsstempel i hver af ovennævnte tilfælde, er det tidspunkt, hvor mappen blev oprettet den faktiske enhed klokkeslæt for start af sikkerhedskopien. Brug tidsstemplet mappe til at finde og identificere sikkerhedskopierne.

2.  Find mappen eller den fil, du vil gendanne i sikkerhedskopien, som du identificerede i ovenstående trin. Bemærk du kan kun se de filer eller mapper, du har tilladelser til. Hvis du ikke kan få adgang til bestemte filer eller mapper, skal du kontakte en administrator for del hvem kan bruge Windows Stifinder redigeringstilladelser dele og give dig adgang til bestemte filen eller mappen. Er det anbefalede bedste fremgangsmåde, del administratoren være en gruppe i stedet for en enkelt bruger.

3.  Kopiere filen eller mappen på den relevante del på dit StorSimple filserver.

![video_icon](./media/storsimple-ova-restore/video_icon.png) **Video, der er tilgængelige**

Se videoen for at se, hvordan du kan oprette aktier, sikkerhedskopiere aktier og gendanne data i en StorSimple virtuelle matrix.

> [AZURE.VIDEO use-the-storsimple-virtual-array]

## <a name="next-steps"></a>Næste trin

Lær mere om, hvordan du [administrerer din StorSimple virtuelle matrix ved hjælp af webdelen lokale brugergrænseflade](storsimple-ova-web-ui-admin.md).
