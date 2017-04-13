<properties
   pageTitle="Nedbrud gendannelse og enhed sekundær server til din StorSimple virtuelle matrix"
   description="Få mere at vide om, hvordan Sådan flytter du din StorSimple virtuelle matrix."
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

# <a name="disaster-recovery-and-device-failover-for-your-storsimple-virtual-array"></a>Nedbrud gendannelse og enhed sekundær server til din StorSimple virtuelle matrix


## <a name="overview"></a>Oversigt

I denne artikel beskrives nedbrud for din Microsoft Azure StorSimple virtuelle matrix (også kendt som den StorSimple lokale virtuelle enhed), herunder den detaljerede vejledning, der kræves for at skifte til en anden virtuel enhed i tilfælde af nedbrud. En failover gør det muligt at overføre dine data fra en *kilde* enhed i datacenteret til en anden *target* enhed, der findes i den samme eller en anden geografiske placering. Enhed sekundære gælder for hele enheden. Under failover ændres skyen dataene til kilde-enhed ejerskab til, på målenheden.

Enhed failover er orchestrated via funktionen nedbrud gendannelse (DR) og startes fra siden **enheder** . Denne side organiserer alle de StorSimple-enheder, der er oprettet forbindelse til din StorSimple Manager-tjenesten. For hver enhed vises den fulde navn, status, klargjort og maksimale kapacitet, type og model.

![](./media/storsimple-ova-failover-dr/image15.png)

I denne artikel kan kun anvendes på StorSimple virtuelle matrixer. Hvis du vil skifte over en 8000 serie-enhed, gå til [Failover og nedbrud af enheden StorSimple](storsimple-device-failover-disaster-recovery.md).


## <a name="what-is-disaster-recovery"></a>Hvad er nedbrud?

I et (DR) nedbrud stopper primær enhed fungerer. Du kan flytte de sky data, der er knyttet til den mislykkede enhed til en anden enhed ved hjælp af den primære enhed som *kilde* og angive en anden enhed som *destination*i så fald. Denne proces kaldes *failover*. Under failover, alle enheder eller aktier fra kildeenheden ændre ejerskab og overføres til den destinationsadresse-enhed. Ingen filtrering af data er tilladt.

DR er formateret som en fuld enhed gendannelse ved hjælp af den varme kortet – baseret overgang switchmiljø og registrering. Et varmekort defineres ved at tildele værdien varme til de data, der er baseret på at læse og skrive mønstre. Denne varme Knyt derefter niveauer laveste varme data dele til skyen først samtidig med at dele de høj varme (bruges mest) data i det lokale niveau. Under en DR bruges varmekortet til at gendanne og rehydreres dataene fra skyen. Enheden henter alle enheder/aktier i den sidste seneste sikkerhedskopiering (som fastsat internt) og udfører en gendannelse fra, at sikkerhedskopiering af. Hele DR processen orchestrated af enheden.


## <a name="prerequisites-for-device-failover"></a>Forudsætninger for enhed failover


### <a name="prerequisites"></a>Forudsætninger

De følgende forudsætninger skal være opfyldt for en hvilken som helst enhed failover:

- Kilde-enhed skal være i en **deaktiveret** tilstand.

- Target enheden skal vises som **aktiv** i portalen Azure klassisk. Du skal til at klargøre en målliste virtuel enhed af den samme eller højere kapacitet. Derefter skal du bruge den lokale web brugergrænseflade til at konfigurere og registrere den virtuelle enhed korrekt.

    > [AZURE.IMPORTANT] Undlad at konfigurere registrerede virtuelle enheden via tjenesten ved at klikke på **Indstillinger for fuldført enhed**. Ingen enhedskonfiguration skal udføres i tjenesten.

- Kilde- og destinationswebsteder enheden skal være af samme type. Du kan kun skifte over en virtuel enhed, der er konfigureret som en filserver til en anden filserver. På samme måde gælder for en iSCSI-server.

- For en filserver DR anbefales det, du deltager target enheden til det samme domæne som kilden, så de pågældende tilladelser er automatisk løst. Kun sekundære til en destination enhed i det samme domæne understøttes i denne udgave.

### <a name="other-considerations"></a>Andre overvejelser

- Vi anbefaler, at du alle enheder eller aktier på kildeenheden offline.

- Hvis det er en planlagt failover, anbefaler vi, at du tage en sikkerhedskopi af enheden, og derefter fortsætte med sekundære at minimere tab af data. Hvis det er en ikke-planlagt failover, bruges den seneste sikkerhedskopiering til at gendanne enheden.

- De tilgængelige mål enheder til DR er enheder, der har den samme eller større kapacitet, der er sammenlignet med den kilde-enhed. De enheder, der har forbindelse til din tjeneste, men ikke opfylder kriterierne i tilstrækkelig plads vil ikke være tilgængelige som destinationsenheder.

### <a name="dr-prechecks"></a>DR prechecks

Før DR begynder, udføres prechecks på enheden. Kontrollen til at sikre, at ingen fejl opstår ved DR påbegyndes. Prechecks omfatter:

- Validere kontoen lagerplads

- Kontrol af cloud-forbindelse til Azure

- Kontrollere ledig plads på den destinationsadresse-enhed

- Kontrol af, om en iSCSI-server kilde-enhed har gyldige ACR navne, IQN (højst 220 tegn) og CHAP adgangskode (12 og 16 tegn) der er knyttet til enhederne

Hvis nogen af ovennævnte prechecks mislykkes, kan du fortsætte med DR. Du har brug for til at løse disse problemer, og prøv DR.

Når DR er gennemført, overføres ejerskabet af skyen dataene på den kilde-enhed til den destinationsadresse-enhed. Kildeenheden er ikke længere tilgængelig i portalen derefter. Adgang til alle enheder/aktierne på kildeenheden er blokeret og målenheden aktiveres.

> [AZURE.IMPORTANT]
> 
> Selvom enheden er ikke længere tilgængelig, er den virtuelle maskine, som du har klargjort på host systemet stadig forbrug ressourcer. Når DR er blevet fuldført, kan du slette denne virtuelt fra systemet host.

## <a name="fail-over-to-a-virtual-array"></a>Skifte til en virtuel matrix

Vi anbefaler, at du har en anden StorSimple virtuelle matrix klargjort, konfigureret ved hjælp af webdelen lokale Brugergrænsefladen og registreret hos StorSimple Manager tjenesten før du kører denne procedure.


> [AZURE.IMPORTANT]
> 
> - Du har ikke tilladelse til at skifte fra en StorSimple 8000 serie enhed til en 1200 virtuel enhed.
> - Du kan ikke fra en national oplysninger behandling FIPS (Standard) aktiveret virtuelle enhed implementeret i Government portal, til en virtuel enhed i Azure klassisk portal. Omvendt gælder også.

Udfør følgende trin for at gendanne enheden til en virtuel target StorSimple-enhed.

1. Tage enheder/aktier offline på værten. Referere til operativsystem-specifikke vejledningen på værten for at tage enheder/aktierne offline. Hvis det er ikke allerede offline, skal du tage alle enheder/aktier offline på enheden ved at gå til **enheder > aktier** (eller **enhed > enheder**). Vælge en del/enhed og klikke på **Tag offline** nederst på siden. Klik på **Ja**, når du bliver bedt om en bekræftelse. Gentag denne proces for alle aktier/enhederne på enheden.

2. Vælg kildeenheden, for failover på siden **enheder** , og klik derefter på **Deaktiver**. 
    ![](./media/storsimple-ova-failover-dr/image16.png)

3. Du bliver bedt om at bekræfte. Deaktivering af enhed er en permanent proces, der kan ikke fortrydes. Du kan også mindet om at benytte dine aktier/drev offline på værten.

    ![](./media/storsimple-ova-failover-dr/image18.png)

3. Når bekræftelsen starter deaktiveringen. Når deaktiveringen er gennemført, får du besked.

    ![](./media/storsimple-ova-failover-dr/image19.png)

4. På siden **enheder** ændres nu enhedens tilstand til **deaktiveret**.

    ![](./media/storsimple-ova-failover-dr/image20.png)

5. Vælg den enhed, der er deaktiveret, og klik på **Failover**nederst på siden.

6. I Bekræft failover guiden, der åbnes skal du gøre følgende:

    1. På rullelisten over tilgængelige enheder, Vælg en **Target enhed.** Kun de enheder, der har tilstrækkelig kapacitet vises på rullelisten.

    2. Gennemgå oplysningerne er knyttet til kilde-enhed som enhedens navn, samlede kapacitet og navnene på de aktier, der ikke bliver gennemført over.

        ![](./media/storsimple-ova-failover-dr/image21.png)

7. Se **jeg accepterer, failover er en permanent handling, og når sekundære er gennemført, kildeenheden slettes**.

8. Klik på ikonet Kontrollér ![](./media/storsimple-ova-failover-dr/image1.png).


9. Et failover job kan startes, og du får besked. Klik på **periode** for at overvåge sekundære.

    ![](./media/storsimple-ova-failover-dr/image22.png)

10. På siden **job** skal vises et failover job, der er oprettet for den kilde-enhed. Dette job udfører DR prechecks.

    ![](./media/storsimple-ova-failover-dr/image23.png)

    Når DR prechecks er gået igennem, opsplitte failover jobbet gendannelsesjob for hver del/enhed, som findes på kildeenheden.

    ![](./media/storsimple-ova-failover-dr/image24.png)

11. Når sekundære er fuldført, skal du gå til siden **enheder** .

    en. Vælg den virtuelle StorSimple-enhed, der blev brugt som målenhed til failover-processen.

    b. Gå til **aktier** side (eller **enheder** Hvis iSCSI-server). Alle aktier (enheder) fra den gamle enhed skulle nu være angivet.
    
    ![](./media/storsimple-ova-failover-dr/image25.png)

![](./media/storsimple-ova-failover-dr/video_icon.png)**Video, der er tilgængelige**

Denne video viser, hvordan du kan ikke over en StorSimple lokale virtuel enhed til en anden virtuel enhed.

> [AZURE.VIDEO storsimple-virtual-array-disaster-recovery]

## <a name="business-continuity-disaster-recovery-bcdr"></a>Business løbende nedbrud (BCDR)

Et business løbende (BCDR) nedbrud opstår, når det hele Azure datacenter ikke fungerer. Det kan påvirke din StorSimple Manager tjeneste og de tilknyttede StorSimple enheder.

Hvis der er StorSimple enheder, der blev registreret lige før nedbrud opstod, og derefter disse StorSimple enheder kan være nødvendigt at blive slettet. Efter genoprettelse efter, kan du genskabe og konfigurere disse enheder.

## <a name="errors-during-dr"></a>Fejl under DR

**Skyen connectivity afbrydelse under DR**

Hvis skyen forbindelsen afbrydes efter DR er startet, og før enhed gendannelsen er fuldført, DR mislykkes, og du får besked. Den destinationsadresse-enhed, der blev brugt til DR derefter er markeret som *ubrugelig.* Den samme målenhed kan derefter bruges til fremtidige DRs.

**Ingen kompatible destinationsadresse-enheder**

Hvis de tilgængelige mål enheder ikke har tilstrækkelig plads, vises fejlmeddelelsen på den effekt, der er ingen kompatible target enheder.

**Precheck mislykkede forsøg**

Hvis en af prechecks ikke opfyldes, får du vist precheck mislykkede forsøg.

## <a name="next-steps"></a>Næste trin

Lær mere om, hvordan du [administrerer din StorSimple virtuelle matrix ved hjælp af webdelen lokale brugergrænseflade](storsimple-ova-web-ui-admin.md).
