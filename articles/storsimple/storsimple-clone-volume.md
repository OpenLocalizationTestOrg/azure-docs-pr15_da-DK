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
   ms.date="08/17/2016"
   ms.author="alkohli" />

# <a name="use-the-storsimple-manager-service-to-clone-a-volume"></a>Bruge tjenesten StorSimple Manager til at klone en volumenlicens

[AZURE.INCLUDE [storsimple-version-selector-clone-volume](../../includes/storsimple-version-selector-clone-volume.md)]

## <a name="overview"></a>Oversigt

Siden StorSimple Manager **Kataloget sikkerhedskopiering** viser de ekstra sæt, der er oprettet, når manuel eller automatisk sikkerhedskopiering er taget. Du kan bruge denne side til at vise alle sikkerhedskopier til en politik for sikkerhedskopiering eller et drev, Vælg eller slette sikkerhedskopier eller bruge en sikkerhedskopi til at gendanne eller klone en volumenlicens.

![Sikkerhedskopiering katalogside](./media/storsimple-clone-volume/HCS_BackupCatalog.png)  

I dette selvstudium beskrives, hvordan du kan bruge en sikkerhedskopi, der er angivet til klone en individuelle lydstyrken. Det også beskriver forskellen mellem *midlertidige* og *permanente* kopier. 

## <a name="create-a-clone-of-a-volume"></a>Oprette en kopi af en volumenlicens

Du kan oprette en Klon på den samme enhed, en anden enhed eller endda et virtuelt ved hjælp af en lokal eller et snapshot af en sky.

#### <a name="to-clone-a-volume"></a>At klone en volumenlicens

1. Klik på fanen **sikkerhedskopi katalog** på siden StorSimple Manager, og vælg et sæt sikkerhedskopier.

2. Udvid sikkerhedskopien angive at få vist de tilknyttede enheder. Klik på og vælge en enhed fra sikkerhedskopien.

     ![Klone en volumenlicens](./media/storsimple-clone-volume/HCS_Clone.png) 

3. Klik på **Klon** for at begynde at klone markerede lydstyrken.

4. I guiden Klon lydstyrken under **Angiv navn og placering**:

  1. Identificere en målenhed med. Dette er den placering, hvor kopien skal oprettes. Du kan vælge den samme enhed eller angive en anden enhed. Hvis du vælger en enhed, der er knyttet til andre skyen tjenesteudbydere (ikke Azure), på rullelisten for den destinationsadresse-enhed viser kun fysiske enheder. Du kan ikke klone en enhed, der er knyttet til andre skyen tjenesteudbydere på en virtuel enhed.

        >  [AZURE.NOTE] Sørg for, den kapacitet, der kræves til kopien er lavere end den kapacitet, der er tilgængelige på den destinationsadresse-enhed.
  2. Angiv et entydigt lydstyrken navn for din Klon. Navnet skal indeholde mellem 3 og 127 tegn.
  3. Tryk på pileikonet ![pil-ikon](./media/storsimple-clone-volume/HCS_ArrowIcon.png) for at fortsætte til næste side.

5. Under **Angiv hosts, der kan anvende denne enhed**:

  1. Angiv en access kontrolelement post (ACR) for kopien. Du kan tilføje en ny ACR eller vælge den eksisterende liste.
  2. Klik på ikonet Kontrollér ![Kontrollér-ikon](./media/storsimple-clone-volume/HCS_CheckIcon.png)at fuldføre handlingen.

6. Et Klon job kan startes, og du får besked, når klonen er blevet oprettet. Klik på **Periode** for at overvåge Klon jobbet på siden **job** .

7. Når Klon job er fuldført:

  1. Gå til siden **enheder** , og vælg fanen **Lydstyrken beholdere** . 
  2. Vælg objektbeholderen lydstyrken, der er knyttet til lydstyrken kilde, du klonet. På listen over enheder, skal du se kopien, som netop er blevet oprettet.

>[AZURE.NOTE] Overvågning og standard sikkerhedskopi deaktiveres automatisk på en klonede lydstyrken.

En Klon, der er oprettet på denne måde er en midlertidig Klon. Du kan finde flere oplysninger om Klon typer se [midlertidige kontra permanent Kopier](#transient-vs.-permanent-clones).

Denne Klon er nu et almindelige lydstyrken, og alle handlinger, der er muligt på en enhed stadig er tilgængelig til kopien. Du skal konfigurere denne lydstyrken for en hvilken som helst sikkerhedskopier.

## <a name="transient-vs-permanent-clones"></a>Forbigående kontra permanent kopier

Midlertidige og permanente Kopier oprettes kun, når du klone til en anden enhed. Du kan klone et bestemt drev fra en sikkerhedskopi, der er angivet til en anden enhed. En Klon er blevet oprettet på denne måde er en *midlertidig* Klon. Midlertidige Klon, har referencer til den oprindelige lydstyrke og anvender lydstyrken til at læse under skrivning lokalt. 

Når du tager et skyen øjebliksbillede af en midlertidig Klon, bliver den resulterende Klon en *permanent* Klon. På permanent Klon er uafhængig og har ikke eventuelle referencer til den oprindelige lydstyrke, det blev klones fra.  

## <a name="scenarios-for-transient-and-permanent-clones"></a>Scenarier, hvor midlertidige og permanente kopier

I følgende afsnit beskrives eksempel situationer, hvor midlertidige og permanente kopier kan bruges.

### <a name="item-level-recovery-with-a-transient-clone"></a>På elementniveau genoprettelse med en midlertidig Klon

Du har brug at gendanne en en-årig Microsoft PowerPoint-præsentationsfil. IT-administratoren identificerer bestemte sikkerhedskopien fra tidsrammen, og derefter filtrerer lydstyrken. Administratoren derefter kloner lydstyrken, finder den fil, du leder efter, og leverer det til dig. I dette scenarie skal bruges en midlertidig Klon. 
 
![Video er til rådighed](./media/storsimple-clone-volume/Video_icon.png) **Video, der er tilgængelige**

Hvis du vil se en video, der viser, hvordan du kan bruge klonen og gendanne funktioner i StorSimple til at gendanne slettede filer, skal du klikke på [her](https://azure.microsoft.com/documentation/videos/storsimple-recover-deleted-files-with-storsimple/).

### <a name="testing-in-the-production-environment-with-a-permanent-clone"></a>Test i produktionsmiljø med en permanent Klon

Du har brug at bekræfte en test fejl i produktionsmiljøet. Du kan oprette en kopi af lydstyrken i produktionsmiljøet ved at tage et skyen øjebliksbillede af denne Klon. Klonede lydstyrken er nu uafhængig. I dette scenarie skal bruges en permanent Klon.

## <a name="next-steps"></a>Næste trin
- Lær, hvordan du kan [gendanne en StorSimple lydstyrken fra et sæt sikkerhedskopier](storsimple-restore-from-backup-set.md).

- Lær, hvordan du [bruger tjenesten StorSimple Manager for at administrere din StorSimple enhed](storsimple-manager-service-administration.md).

 
