<properties
   pageTitle="Klone StorSimple lydstyrken | Microsoft Azure"
   description="I denne artikel beskrives de forskellige Klon typer, og hvornår du skal bruge dem, og beskriver, hvordan du kan bruge en sikkerhedskopi, der er angivet til klone en individuelle lydstyrken."
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
   ms.date="07/27/2016"
   ms.author="alkohli" />

# <a name="use-the-storsimple-manager-service-to-clone-a-volume-update-2"></a>Bruge tjenesten StorSimple Manager til at klone lydstyrken (opdatering 2)

[AZURE.INCLUDE [storsimple-version-selector-clone-volume](../../includes/storsimple-version-selector-clone-volume.md)]

## <a name="overview"></a>Oversigt

Siden StorSimple Manager **Kataloget sikkerhedskopiering** viser de ekstra sæt, der er oprettet, når manuel eller automatisk sikkerhedskopiering er taget. Du kan bruge denne side til at vise alle sikkerhedskopier til en politik for sikkerhedskopiering eller et drev, Vælg eller slette sikkerhedskopier eller bruge en sikkerhedskopi til at gendanne eller klone en volumenlicens.

![Sikkerhedskopiering katalogside](./media/storsimple-clone-volume-u2/backupCatalog.png)  

I dette selvstudium beskrives, hvordan du kan bruge en sikkerhedskopi, der er angivet til klone en individuelle lydstyrken. Det også beskriver forskellen mellem *midlertidige* og *permanente* kopier.

>[AZURE.NOTE] 
>
>Et lokalt fastgjorte lydstyrken der klones som en lagdelte lydstyrken. Hvis du har brug for at være fastgjort lokalt klonede lydstyrken, kan du konvertere kopien til et lokalt fastgjorte drev, når handlingen Klon er gennemført. Finde oplysninger om konvertering af en lagdelte lydstyrken til et lokalt fastgjorte lydstyrken, gå til [ændre typen lydstyrken](storsimple-manage-volumes-u2.md#change-the-volume-type).
>
>Hvis du forsøger at konvertere en klonede lydstyrken fra mislykkes lagdelt til lokalt fastgjort umiddelbart efter klone (når den er stadig en midlertidig Klon), konverteringen med følgende fejlmeddelelse:
>
>`Unable to modify the usage type for volume {0}. This can happen if the volume being modified is a transient clone and hasn’t been made permanent. Take a cloud snapshot of this volume and then retry the modify operation.` 
>
>Denne fejl er modtaget kun, hvis du klone til en anden enhed. Du kan konvertere lydstyrken til fastgjort lokalt, hvis du først konvertere den midlertidige Klon til en permanent Klon korrekt. For at konvertere den midlertidige Klon til en permanent Klon, skal du tage et skyen øjebliksbillede af den.

## <a name="create-a-clone-of-a-volume"></a>Oprette en kopi af en volumenlicens

Du kan oprette en Klon på den samme enhed, en anden enhed eller endda et virtuelt ved hjælp af et lokalt eller skybaseret øjebliksbillede.

#### <a name="to-clone-a-volume"></a>At klone en volumenlicens

1. Klik på fanen **sikkerhedskopi katalog** på siden StorSimple Manager, og vælg et sæt sikkerhedskopier.

2. Udvid sikkerhedskopien angive at få vist de tilknyttede enheder. Klik på og vælge en enhed fra sikkerhedskopien.

     ![Klone en volumenlicens](./media/storsimple-clone-volume-u2/CloneVol.png) 

3. Klik på **Klon** for at begynde at klone markerede lydstyrken.

4. I guiden Klon lydstyrken under **Angiv navn og placering**:

  1. Identificere en målenhed med. Dette er den placering, hvor kopien skal oprettes. Du kan vælge den samme enhed eller angive en anden enhed. Hvis du vælger en enhed, der er knyttet til andre skyen tjenesteudbydere (ikke Azure), på rullelisten for den destinationsadresse-enhed viser kun fysiske enheder. Du kan ikke klone en enhed, der er knyttet til andre skyen tjenesteudbydere på en virtuel enhed.

        >[AZURE.NOTE] Sørg for, den kapacitet, der kræves til kopien er lavere end den kapacitet, der er tilgængelige på den destinationsadresse-enhed.

  2. Angiv et entydigt lydstyrken navn for din Klon. Navnet skal indeholde mellem 3 og 127 tegn. 
    
        >[AZURE.NOTE] Feltet **Klon lydstyrken som** bliver **Tiered** , selvom du klone et lokalt fastgjorte lydstyrken. Du kan ikke ændre denne indstilling men hvis du har brug for at være lokalt fastgjort samt klonede lydstyrken, kan du konvertere kopien til et lokalt fastgjorte lydstyrken når du har oprettet klonen korrekt. Finde oplysninger om konvertering af en lagdelte lydstyrken til et lokalt fastgjorte lydstyrken, gå til [ændre typen lydstyrken](storsimple-manage-volumes-u2.md#change-the-volume-type).

        ![Klon guiden 1](./media/storsimple-clone-volume-u2/clone1.png) 

  3. Tryk på pileikonet ![pil-ikon](./media/storsimple-clone-volume-u2/HCS_ArrowIcon.png) for at fortsætte til næste side.

5. Under **Angiv hosts, der kan anvende denne enhed**:

  1. Angiv en access kontrolelement post (ACR) for kopien. Du kan tilføje en ny ACR eller vælge den eksisterende liste.

        ![Klon guiden 2](./media/storsimple-clone-volume-u2/clone2.png) 

  2. Klik på ikonet Kontrollér ![Kontrollér-ikon](./media/storsimple-clone-volume-u2/HCS_CheckIcon.png)at fuldføre handlingen.

6. Et Klon job kan startes, og du får besked, når klonen er blevet oprettet. Klik på **Periode** for at overvåge Klon jobbet på siden **job** . Du får vist følgende meddelelse, når Klon jobbet er afsluttet:

    ![Klon meddelelse](./media/storsimple-clone-volume-u2/CloneMsg.png) 

7. Når Klon job er fuldført:

  1. Gå til siden **enheder** , og vælg fanen **Lydstyrken beholdere** . 
  2. Vælg objektbeholderen lydstyrken, der er knyttet til lydstyrken kilde, du klonet. På listen over enheder, skal du se kopien, som netop er blevet oprettet.

>[AZURE.NOTE] Overvågning og standard sikkerhedskopi deaktiveres automatisk på en klonede lydstyrken.

En Klon, der er oprettet på denne måde er en midlertidig Klon. Du kan finde flere oplysninger om Klon typer se [midlertidige kontra permanent Kopier](#transient-vs.-permanent-clones).

Denne Klon er nu et almindelige lydstyrken, og alle handlinger, der er muligt på en enhed stadig er tilgængelig til kopien. Du skal konfigurere denne lydstyrken for en hvilken som helst sikkerhedskopier.

## <a name="transient-vs-permanent-clones"></a>Forbigående kontra permanent kopier

Midlertidige kopier oprettes kun, når du klone til en anden enhed. Du kan klone et bestemt drev fra en sikkerhedskopi, der er angivet til en anden enhed, der administreres af StorSimple Manager. Midlertidige Klon, har referencer til data i den oprindelige lydstyrke og bruger disse data til at læse og skrive lokalt på målenheden. 

Når du tager et skyen øjebliksbillede af en midlertidig Klon, bliver den resulterende Klon en *permanent* Klon. Under denne proces, der oprettes en kopi af dataene i skyen og tid til at kopiere disse data bestemmes af størrelsen af dataene og de Azure latenstider (dette er en Azure-Azure kopi). Denne proces kan tage dage til uger. Midlertidige Klon bliver en permanent Klon denne måde og har ikke alle henvisninger til de oprindelige lydstyrke data, den blev klones fra. 

## <a name="scenarios-for-transient-and-permanent-clones"></a>Scenarier, hvor midlertidige og permanente kopier

I følgende afsnit beskrives eksempel situationer, hvor midlertidige og permanente kopier kan bruges.

### <a name="item-level-recovery-with-a-transient-clone"></a>På elementniveau genoprettelse med en midlertidig Klon

Du har brug at gendanne en en-årig Microsoft PowerPoint-præsentationsfil. IT-administratoren identificerer bestemte sikkerhedskopien fra tidsrammen, og derefter filtrerer lydstyrken. Administratoren derefter kloner lydstyrken, finder den fil, du leder efter, og leverer det til dig. I dette scenarie skal bruges en midlertidig Klon. 
 
![Video er til rådighed](./media/storsimple-clone-volume-u2/Video_icon.png) **Video, der er tilgængelige**

Hvis du vil se en video, der viser, hvordan du kan bruge klonen og gendanne funktioner i StorSimple til at gendanne slettede filer, skal du klikke på [her](https://azure.microsoft.com/documentation/videos/storsimple-recover-deleted-files-with-storsimple/).

### <a name="testing-in-the-production-environment-with-a-permanent-clone"></a>Test i produktionsmiljø med en permanent Klon

Du har brug at bekræfte en test fejl i produktionsmiljøet. Du opretter en kopi af lydstyrken i produktionsmiljøet og derefter tage et skyen øjebliksbillede af denne Klon til at oprette en uafhængig klonede lydstyrken. I dette scenarie skal bruges en permanent Klon.  

## <a name="next-steps"></a>Næste trin
- Lær, hvordan du kan [gendanne en StorSimple lydstyrken fra et sæt sikkerhedskopier](storsimple-restore-from-backup-set-u2.md).

- Lær, hvordan du [bruger tjenesten StorSimple Manager for at administrere din StorSimple enhed](storsimple-manager-service-administration.md).

 
