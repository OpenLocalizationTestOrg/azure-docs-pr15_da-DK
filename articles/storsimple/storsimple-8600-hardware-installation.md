<properties
   pageTitle="Installere enheden StorSimple 8600 | Microsoft Azure"
   description="Beskriver, hvordan du Pak, rack Indlæs og kabel enheden StorSimple 8600, før du installerer og konfigurerer softwaren."
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
   ms.date="10/24/2016"
   ms.author="alkohli" />

# <a name="unpack-rack-mount-and-cable-your-storsimple-8600-device"></a>Pak, rack-tilslutning, og enheden StorSimple 8600-kabel

## <a name="overview"></a>Oversigt
Dine Microsoft Azure StorSimple 8600 er en enhed med to omslutning og består af en primær og en EBOD omslutning. Dette selvstudium forklarer, hvordan du Pak, rack-tilslutning og kabel StorSimple 8600 enhedens hardware før du konfigurerer StorSimple softwaren.

## <a name="unpack-your-storsimple-8600-device"></a>Pak enheden StorSimple 8600

Følgende trin viser tydeligt og detaljeret instruktioner til, hvordan du Pak enheden StorSimple 8600 lagerplads. Denne enhed er afsendt i to bokse, én til den primære omslutning og et andet til EBOD omslutning. Disse to bokse placeres derefter i en kasse.

### <a name="prepare-to-unpack-your-device"></a>Forberede dig på at Pak din enhed

Før du Pak din enhed, kan du se følgende oplysninger.


![Advarselsikon](./media/storsimple-safety/IC740879.png)![tunge ikonet](./media/storsimple-8600-hardware-installation/HCS_HeavyWeight_Icon.png) **Advarsel!**

1. Sørg for, at du har to personer, der er tilgængelige til at administrere tykkelsen på enheden, hvis du håndtering af den manuelt. En fuldt konfigureret omslutning kan afveje op til 32 kg (70 pund).
1. Placer feltet på en flad, niveau overflade.

Derefter skal du benytte følgende fremgangsmåde for at Pak din enhed.

#### <a name="to-unpack-your-device"></a>Til Pak din enhed

1. Undersøg feltet og emballagen skummet for crushes, stykker, vand skader eller andre indlysende skader. Hvis feltet eller emballagen er alvorligt beskadiget, kan ikke åbnes feltet. Kan du [kontakte Microsoft Support](storsimple-contact-microsoft-support.md) for at hjælpe dig med at vurdere, om enheden er funktionsdygtige.

2. Åbne dialogboksen ydre og derefter tage ud af de to bokse, der svarer til primær og EBOD bilag. Du kan nu Pak primær og EBOD bilag. I følgende figur vises den udpakkede visning af en af bilag.

    ![Pak enheden lagerplads](./media/storsimple-8600-hardware-installation/HCSUnpackyour4Udevice.png)

    **Udpakkede visning af enheden lagerplads**

     Etiket | Beskrivelse
     ----- | -------------
     1     | Emballagen feltet
     2     | SAS kabler (i tilbehør og kabler bakke)
     3     | Nederste skum
     4     | Enhed
     5     | Øverste skum
     6     | Tilbehør feltet

3. Sørg for, at du har efter ud to bokse:

  - 1 primære omslutning (primære omslutning og EBOD omslutning, i to separate bokse)
  - 1 EBOD omslutning
  - 4 el-kabler, 2 i hvert felt
  - 2 SAS kabler (til at forbinde den primære omslutning til EBOD omslutning)
  - 1 crossover Ethernet-kablet
  - 2 Seriel konsol kabler
  - 1 serielt USB-konverteringsprogram for seriel adgang
  - 4 QSFP-til-SFP + kort til brug med 10 GbE netværksgrænseflader
  - 2 rack tilslutningen pakker (4 sideskinner med tilslutte hardware 2 til den primære omslutning og EBOD omslutning), 1 i hvert felt
  - Få Introduktion dokumentation

    Hvis du ikke har modtaget nogen af de elementer, der er anført ovenfor, [Kontakt Microsoft Support](storsimple-contact-microsoft-support.md).  

Næste trin er at rack-tilslutning din enhed.

## <a name="rack-mount-your-storsimple-8600-device"></a>Rack-tilslutning enheden StorSimple 8600

Følg de næste trin for at installere enheden StorSimple 8600 lagerplads i et almindeligt 19 tommer rack med forsiden og bagsiden indlæg. Denne enhed leveres med to bilag: en primær omslutning og en EBOD omslutning. Begge af følgende skal være stablet.

Installationen består af flere trin, hver især er beskrevet i følgende procedurer.

> [AZURE.IMPORTANT]
StorSimple enheder skal være stablet fungerer korrekt.



### <a name="site-preparation"></a>Forberedelse af websted

Bilag skal være installeret i et almindeligt 19 tommer rack, der indeholder både forsiden og bagsiden indlæg. Benyt følgende fremgangsmåde for at forberede rack-installation.

#### <a name="to-prepare-the-site-for-rack-installation"></a>Sådan forberedes rack-installation af webstedet

1. Sørg for, at primær og EBOD bilag er hvile på en sikker måde i en flad, stabil og niveau arbejde grundflade (eller lignende).

2. Kontrollér, at webstedet, hvor du vil konfigurere har standard netledningen fra en uafhængig kilde eller et rack Power fordeling måleenhed (PDU'ER) med en UPS (sig).

3. Kontrollér, at en 4U (2 X 2U) stikket er tilgængelig på rack, hvor du vil tilslutte bilag.

![Advarselsikon](./media/storsimple-safety/IC740879.png)![tunge ikonet](./media/storsimple-8600-hardware-installation/HCS_HeavyWeight_Icon.png) **Advarsel!**

 Sørg for, at du har to personer, der er tilgængelige til at administrere vægten, hvis du håndtering af enhed konfigurationen manuelt. En fuldt konfigureret omslutning kan afveje op til 32 kg (70 pund).

### <a name="rack-prerequisites"></a>Rack forudsætninger

Bilag er beregnet til installation i et almindeligt 19 tommer rack med:

- Mindste dybde af 27.84 tommer fra rack indlæg til indlæg
- Maksimal vægt på 32 kg for enheden
- Maksimale tilbage Tryk på 5 Pascal (0,5 mm vand måler)

### <a name="rack-mounting-rail-kit"></a>Rail i rackmontering

Et sæt monteringsskinner vil blive leveret til brug sammen med 19 tommer rackkabinettet. Gør er testet for at håndtere den maksimale omslutning vægt. Disse gør kan det også muligt at installation af flere bilag uden tab af plads i racket. Installere EBOD omslutning først.

#### <a name="to-install-the-ebod-enclosure-on-the-rails"></a>Installere EBOD omslutning på skinnerne

2. Udføre dette trin kun, hvis indre gør ikke er installeret på din enhed. Typisk er de indre gør installeret på fabrik. Hvis gør ikke er installeret, skal du installere de venstre rail og højre rail slides til sider af omslutning kabinettet. De vedhæfte med seks metriske skruer på hver side. For at hjælpe med retning, rail slides er markeret **LH – forside** og **relativ Fugtighed – forrest**, og, der er fastgjort bagud fra omslutning har en tilspidset ende.

    ![Vedhæfte rail slides til omslutning kabinet](./media/storsimple-8600-hardware-installation/HCSAttachingRailSlidestoEnclosureChassis.png)

    **Vedhæfte rail slides til sider af omslutning**

    Etiket | Beskrivelse
    ----- | -----------
    1     | M 3 x 4 knappen hoved skruer
    2     | Kabinet slides

3. Vedhæft rail venstre og højre rail samlinger til rack Skab lodret medlemmer. De kantede parenteser er markeret, **LH**, **relativ Fugtighed**og **denne side op** for at vejlede dig gennem rigtige retning.

4. Find rail ben på forsiden og bagsiden af samlingen rail. Udvide rail for at passe ind mellem rack-indlæggene og indsætte benene i forside og bagside rack indlæg lodret medlem huller. Sørg for, at samlingen rail er niveau.

5. Sikker samlingen tog til racket lodret medlemmer ved hjælp af to af de metriske skruer, der er angivet. Du kan bruge én skrue på forsiden og ét på bag.

6. Gentag disse trin for andre tog samlingen.

     ![Vedhæfte rail slides til rack Skab](./media/storsimple-8600-hardware-installation/HCSAttachingRailSlidestoRackCabinet.png)

    **Vedhæfte rail assemblies til racket**

     Etiket | Beskrivelse
     ----- | -----------
     1     | Proces skrue
     2     | Firkant, der er huller forside rack indlæg skrue
     3     | Venstre front rail placering ben
     4     | Proces skrue
     5     | Venstre bag rail placering ben

### <a name="mounting-the-ebod-enclosure-in-the-rack"></a>Tilslutte EBOD omslutning i racket

Bruger rackskinner, der lige blev installeret, skal du gøre følgende for at tilslutte EBOD omslutning i racket.

#### <a name="to-mount-the-ebod-enclosure"></a>At tilslutte EBOD omslutning

1. Løft omslutning med en assistent, og juster den med rackskinner.

2. Indsætte omhyggeligt omslutning i skinnerne, og derefter skubber det helt på racket skab.

    ![Indsætte enhed i racket](./media/storsimple-8600-hardware-installation/HCSInsertingDeviceintheRack.png)

    **Tilslutte omslutning i racket**

3. Fjerne venstre og højre forside flangen bogstaver ved at trække gratis bogstaver. Flangen caps Fastgør blot på flanger.

4. Sikker omslutning i racket ved at installere én medfølgende stjerneskruetrækker hoved skrue gennem hver flangen, venstre og højre.

4. Installere flangen bogstaver ved at trykke på dem på den ønskede placering og fastgøre dem til det ønskede sted.

     ![Installere flangen bogstaver](./media/storsimple-8600-hardware-installation/HCSInstallingFlangeCaps.png)

    **Installere flangen bogstaver**

     Etiket | Beskrivelse
     ----- | -----------
     1     | Omslutning fastholdelsesanordning skrue


### <a name="mounting-the-primary-enclosure-in-the-rack"></a>Tilslutte den primære omslutning i racket

Når du er færdig med at tilslutte EBOD omslutning, skal du tilslutte den primære omslutning, følge de samme trin.

> [AZURE.NOTE]
>
> - Det er muligt at have et par tomme pladser i rack mellem den primære omslutning og EBOD omslutning.
> - Bruge det medfølgende 2m SAS kabel primære omslutning tilsluttes EBOD omslutning.
> - Der er ingen begrænsninger på relative placeringen af hoved måleenheden til EBOD-enhed. Derfor den primære omslutning kan placeres i den øverste slot og den nedenfor EBOD omslutning – eller omvendt.

Næste trin er at kabel enheden til power-, Netværks- og seriel adgang.

## <a name="cable-your-storsimple-8600-device"></a>Enheden StorSimple 8600-kabel

Følgende procedurer viser, hvordan du kabel enheden StorSimple 8600 til power-, Netværks- og serielle forbindelser.

### <a name="prerequisites"></a>Forudsætninger

Før du begynder at kabel din enhed, skal du:

- Din primære omslutning og omslutning EBOD helt pakkes ud
- 4 power kabler (2 hver til primært og EBOD omslutning), der fulgte med enheden
- 2 SAS kabler, der følger med kameraet, for at oprette forbindelse EBOD omslutning til den primære omslutning
- Adgang til 2 Power fordeling enheder (PDU'er) (anbefales)
- Netværkskablerne
- Angivet serielle kabler
- Serielt-USB-konverteringsprogram med den korrekte driver installeret på din PC (hvis det er nødvendigt)
- Angivet 4 QSFP-til-SFP + kort til brug med 10 GbE netværksgrænseflader
- [Understøttede hardware for 10 GbE netværksgrænseflader på enheden StorSimple](storsimple-supported-hardware-for-10-gbe-network-interfaces.md)

### <a name="sas-and-power-cabling"></a>SAS og Power-kabler

Enheden er både en primær omslutning og en EBOD omslutning. Dette kræver, at enhederne, der skal være kabel sammen for seriel vedhæftet SCSI (SAS) forbindelsen og power.

Når du konfigurerer denne enhed for første gang, skal udføre trinnene for SAS kabler først og derefter udføre trinnene til power-kabler.

[AZURE.INCLUDE [storsimple-cable-8600-for-SAS](../../includes/storsimple-sas-cable-8600.md)]

[AZURE.INCLUDE [storsimple-cable-8600-for-power](../../includes/storsimple-cable-8600-for-power.md)]

### <a name="network-cabling"></a>Netværkskabler

Enheden er i en aktiv standby konfiguration: til enhver tid, en controller modul er aktiv og behandling af alle disk og netværk handlinger, mens andre controller modulet er i standby. Hvis der opstår fejl controller, aktiverer den standby controller med det samme, og fortsætter alle disk og netværk handlinger.

Hvis du vil understøtter denne overflødige controller failover, skal du kabel netværket enhed, som vist i følgende trin.

#### <a name="to-cable-for-network-connection"></a>Til kablet til netværksforbindelse

1. Enheden er seks netværksgrænseflader på hver controller: fire 1 Gbps og to 10 Gbps Ethernet-porte. Se nedenstående illustration til at identificere data portene på backplane på din enhed.

     ![Backplane 8600 enhed](./media/storsimple-8600-hardware-installation/HCSBackplaneof2UDevicewithPortsLabeled.jpg)

    **Bagsiden af din enhed, der viser dataporte**

     Etiket   | Beskrivelse
     ------- | -----------
     0,1,4,5 |  1 GbE netværksgrænseflader
     2,3     | 10 GbE netværksgrænseflader
     6       | Serielle porte



1. Se i det følgende diagram til netværkskabler. (Den mindste netværkskonfiguration vises med blå streger. For høj tilgængelighed og ydeevne vises yderligere konfiguration, der er påkrævet med stiplede linjer.)

![Kabel enheden 4U til netværket](./media/storsimple-8600-hardware-installation/HCSCableYour4UDeviceforNetwork.png)

**Netværk-kablet til din enhed**

Etiket | Beskrivelse
----- | -----------
A    | LAN med forbindelse til internettet
B    | Controller 0
C    | PCM 0
D    | Controller 1
E    | PCM 1
F    | EBOD controller 0
G    | EBOD controller 1
H, JEG  | Hosts (for eksempel filservere)
0-5  | Netværksgrænseflader
6    | Primære omslutning
7    | EBOD omslutning

Når kabler enheden, kræver det mindste konfiguration:


- Mindst to netværksgrænseflader forbindelse på hver controller med én til skyadgang og én til iSCSI. DATA 0 port er automatisk aktiveret og konfigureret via seriel konsollen på enheden. Ud over DATA 0 skal en anden dataport også konfigureres via Azure klassisk portalen. I dette tilfælde forbinde DATA 0 port til den primære LAN (netværk med adgang til internettet). De andre dataporte kan være forbundet til SAN/iSCSI LAN, der er adskilt segment af netværket, afhængigt af den tiltænkte rolle.

- Identiske grænseflader på hver controller forbindelse til den samme netværk for at sikre, at tilgængelige Hvis controller serverskift. Hvis du vælger at forbinde DATA 0 og DATA 3 for et af enheder, skal du eksempelvis opretter forbindelse de tilsvarende DATA 0 og DATA 3 på anden controlleren.

Husk på for høj tilgængelighed og ydeevne:


- Når det er muligt, kan du konfigurere et par af netværk brugergrænseflade til skyadgang (1 GbE) og en anden par til iSCSI (10 GbE anbefales) på hver controller.

- Forbind netværksgrænseflader fra hver controller til to forskellige parametre til at sikre, at tilgængelige mod en Skift fejl, når det er muligt. I figur illustreres to 10 GbE netværk grænseflader, DATA 2 og 3, skal du DATA fra hver controller, der er forbundet med to forskellige parametre. Yderligere oplysninger finder du referere til **netværksgrænseflader** under [Høj tilgængelighedskrav til enheden StorSimple](storsimple-system-requirements.md#high-availability-requirements-for-storsimple).

>[AZURE.NOTE] Brug de medfølgende QSFP, hvis bruger SFP + skal med din 10 GbE netværksgrænseflader-SFP + kort. Gå til [understøttede hardware for 10 GbE netværksgrænseflader på enheden StorSimple](storsimple-supported-hardware-for-10-gbe-network-interfaces.md)kan finde flere oplysninger.

### <a name="serial-port-cabling"></a>Seriel port kabler

Udfør følgende trin for at kabel den serielle port.

#### <a name="to-cable-for-serial-connection"></a>Til kablet til seriel forbindelse

1. Din enhed har en seriel port på hver controller, der er identificeret med et skruenøgleikon for. For at finde de serielle porte, referere til den illustration, der viser data porte på bagsiden af din enhed.

2. Identificere den aktive controller på din enhed backplane. En blinkende blå LED angiver, at controlleren er aktiv.

3. Brug det medfølgende serielle kabel (hvis det er nødvendigt, USB-seriel konverteringsprogram til din bærbare computer), og oprette forbindelse til den serielle port af den aktive controller din konsol eller computer (med terminalemulering på enheden).

4. Installere serielt-USB-driverne (følger med enheden) på din computer.

5. Konfigurere den serielle forbindelse på følgende måde:
   - 115.200 baud
   - 8 databit
   - 1 stopbit
   - Ingen fungerer ensartet
   - Flow-styring indstillet til **ingen**

6. Kontrollér, at forbindelsen fungerer ved at trykke på Enter på konsollen. En seriel konsol menu skal vises.

> [AZURE.NOTE] **Backupløsning administration:** Når enheden er installeret i en ekstern datacenter eller på en computer rum med begrænset adgang, kan du sikre dig, at de serielle forbindelser til begge enheder altid er forbundet med en seriel konsol Skift eller tilsvarende udstyr. Dette giver mulighed for out-of-band-fjernbetjening og understøttelse af handlinger i tilfælde af netværk afbrydelser eller uventet fejl.

Du har fuldført kabler enheden til power, netværksadgang og seriel forbindelse. Næste trin er at konfigurere softwaren på din enhed.

## <a name="next-steps"></a>Næste trin

Nu er du klar til at [installere og konfigurere din lokale StorSimple enhed](storsimple-deployment-walkthrough-u2.md).
