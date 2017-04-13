<properties
   pageTitle="Installere enheden StorSimple 8100 | Microsoft Azure"
   description="Beskriver, hvordan du Pak, rack Indlæs og kabel enheden StorSimple 8100, før du installerer og konfigurerer softwaren."
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

# <a name="unpack-rack-mount-and-cable-your-storsimple-8100-device"></a>Pak, rack-tilslutning, og enheden StorSimple 8100-kabel

## <a name="overview"></a>Oversigt

Dine Microsoft Azure StorSimple 8100 er en enkelt omslutning, stablet-enhed. Dette selvstudium forklarer, hvordan du Pak, rack-tilslutning og kabel StorSimple 8100 enhedens hardware, før du konfigurerer og installere StorSimple enheden.

## <a name="unpack-your-storsimple-8100-device"></a>Pak enheden StorSimple 8100

Følgende trin viser tydeligt og detaljeret instruktioner om, hvordan du Pak enheden StorSimple 8100 lagerplads. Denne enhed er afsendt i en kasse.

### <a name="prepare-to-unpack-your-device"></a>Forberede dig på at Pak din enhed

Før du Pak din enhed, kan du se følgende oplysninger.

![Advarselsikon](./media/storsimple-safety/IC740879.png)![tunge ikonet](./media/storsimple-8100-hardware-installation/HCS_HeavyWeight_Icon.png) **Advarsel!**

1. Sørg for, at du har to personer, der er tilgængelige til at administrere tykkelse på omslutning, hvis du håndtering af den manuelt. En fuldt konfigureret omslutning kan afveje op til 32 kg (70 pund).
1. Placer feltet på en flad, niveau overflade.

Derefter skal du benytte følgende fremgangsmåde for at Pak din enhed.

#### <a name="to-unpack-your-device"></a>Til Pak din enhed

1. Undersøg feltet og emballagen skummet for crushes, stykker, vand skader eller andre indlysende skader. Hvis feltet eller emballagen er alvorligt beskadiget, kan ikke åbnes feltet. Kan du [kontakte Microsoft Support](storsimple-contact-microsoft-support.md) for at hjælpe dig med at vurdere, om enheden er funktionsdygtige.

2. Pak feltet. Følgende billede viser den udpakkede visning af enheden StorSimple.

     ![Pak enheden lagerplads](./media/storsimple-8100-hardware-installation/HCSUnpackyour2Udevice.png)

    **Udpakkede visning af enheden lagerplads**

     Etiket | Beskrivelse
     ----- | -------------
     1     | Emballagen feltet
     2     | Nederste skum
     3     | Enhed
     4     | Øverste skum
     5     | Tilbehør feltet


3. Sørg for, at du har efter pakke feltet:

   - 1 enkelt omslutning enhed
   - 2 el-kabler
   - 1 crossover Ethernet-kablet
   - 2 Seriel konsol kabler
   - 1 serielt USB-konverteringsprogram for seriel adgang
   - 1 sikret bevis T10 skruetrækker
   - 4 QSFP-til-SFP + kort til brug med 10 GbE netværksgrænseflader
   - 1 rack-tilslutning kit (2 sideskinner med tilslutte hardware)
   - Få Introduktion dokumentation

    Hvis du ikke har modtaget nogen af de elementer, der er anført ovenfor, [Kontakt Microsoft Support](storsimple-contact-microsoft-support.md).

Næste trin er at rack-tilslutning din enhed.

## <a name="rack-mount-your-storsimple-8100-device"></a>Rack-tilslutning enheden StorSimple 8100

Følg de næste trin for at installere enheden StorSimple 8100 lagerplads i et almindeligt 19 tommer rack med forsiden og bagsiden indlæg. StorSimple 8100 enhed har en enkelt primære omslutning.

Installationen består af flere trin, hver især er beskrevet i følgende procedurer.

> [AZURE.IMPORTANT]
StorSimple enheder skal være stablet fungerer korrekt.

### <a name="prepare-the-site"></a>Forberede dig på webstedet

Enheden, skal være installeret i et almindeligt 19 tommer rack, der indeholder både forsiden og bagsiden indlæg. Benyt følgende fremgangsmåde for at forberede rack-installation.

#### <a name="to-prepare-the-site-for-rack-installation"></a>Sådan forberedes rack-installation af webstedet

1. Sørg for, at enheden sikkert hviler på en flad, stabil og niveau arbejde grundflade (eller lignende).

2. Kontrollér, at webstedet, hvor du vil konfigurere har standard netledningen fra en uafhængig kilde eller en rack power fordeling enhed (PDU'ER) med en UPS (sig).

3. Kontrollér, at en 2U stikket er tilgængelig på rack, hvor du vil tilslutte enheden.

![Advarselsikon](./media/storsimple-safety/IC740879.png)![tunge ikonet](./media/storsimple-8100-hardware-installation/HCS_HeavyWeight_Icon.png) **Advarsel!**

Sørg for, at du har to personer, der er tilgængelige til at administrere vægten, hvis du håndtering af enhed konfigurationen manuelt. En fuldt konfigureret omslutning kan afveje op til 32 kg (70 pund).

### <a name="rack-prerequisites"></a>Rack forudsætninger

8100 omslutning er beregnet til installation i et almindeligt 19 tommer rack med:

- Mindste dybde af 27.84 tommer fra rack indlæg til indlæg.
- Maksimal vægt på 32 kg for enheden
- Maksimale tilbage Tryk på 5 Pascal (0,5 mm vand måler).

### <a name="rack-mounting-rail-kit"></a>Rail i rackmontering

Et sæt monteringsskinner er beregnet til brug sammen med 19 tommer rackkabinettet. Gør er testet for at håndtere den maksimale omslutning vægt. Disse gør kan det også muligt at installation af flere bilag uden at miste plads i racket.


#### <a name="to-install-the-device-on-the-rails"></a>Installere enheden på skinnerne

2. Udføre dette trin kun, hvis indre gør ikke er installeret på din enhed. Typisk er de indre gør installeret på fabrik. Hvis gør ikke er installeret, skal du installere de venstre rail og højre rail slides til sider af omslutning kabinettet. De vedhæfte med seks metriske skruer på hver side. For at hjælpe med retning, rail slides er markeret **LH – forside** og **relativ Fugtighed – forrest**, og, der er fastgjort bagud fra omslutning har en tilspidset ende.<br/>

    ![Vedhæfte rail slides til omslutning kabinet](./media/storsimple-8100-hardware-installation/HCSAttachingRailSlidestoEnclosureChassis.png)
    **Attaching indre rail slides til sider af omslutning**

       Etiket | Beskrivelse
       ----- | -----------
       1     | M 3 x 4 knappen hoved skruer
       2     | Kabinet slides

3. Vedhæft ydre venstre rail og ydre højre rail assemblies til rack Skab lodret medlemmer. De kantede parenteser er markeret, **LH**, **relativ Fugtighed**og **denne side op** for at vejlede dig gennem den rigtige retning.

4. Find rail ben på forsiden og bagsiden af samlingen rail. Udvide rail for at passe ind mellem rack-indlæggene og indsætte benene i forsiden og bagsiden rack indlæg lodret medlem huller. Sørg for, at samlingen rail er niveau.

5. Du kan bruge to af de medfølgende metriske skruer til at sikre samlingen tog til racket lodret medlemmer. Du kan bruge én skrue på forsiden og ét på bag.

6. Gentag disse trin for andre tog samlingen.<br/>

     ![Vedhæfte rail slides til rack Skab](./media/storsimple-8100-hardware-installation/HCSAttachingRailSlidestoRackCabinet.png)

    **Vedhæfte ydre rail assemblies til racket**

     Etiket | Beskrivelse
     ----- | -----------
     1     | Proces skrue
     2     | Firkant, der er huller forside rack indlæg skrue
     3     | Venstre rail front placering ben
     4     | Proces skrue
     5     | Venstre rail bag placering ben


### <a name="mounting-the-device-in-the-rack"></a>Tilslutte enheden i racket

Bruger rackskinner, der lige blev installeret, skal du gøre følgende for at tilslutte enheden i racket.

#### <a name="to-mount-the-device"></a>At tilslutte enheden

1. Løft omslutning med en assistent, og juster den med rackskinner.

2. Indsætte omhyggeligt enheden i skinnerne, og derefter push enheden helt i racket skab.<br/>

    ![Indsætte enhed i racket](./media/storsimple-8100-hardware-installation/HCSInsertingDeviceintheRack.png)

    **Tilslutte enheden i racket**


3. Fjerne venstre og højre forside flangen bogstaver ved at trække gratis bogstaver. Flangen caps Fastgør blot på flanger.

5. Sikker omslutning i racket ved at installere én medfølgende stjerneskruetrækker hoved skrue gennem hver flangen, venstre og højre.

4. Installere flangen bogstaver ved at trykke på dem på den ønskede placering og fastgøre dem på plads.<br/>

     ![Installere flangen bogstaver](./media/storsimple-8100-hardware-installation/HCSInstallingFlangeCaps.png)

    **Installere flangen bogstaver**

     Etiket | Beskrivelse
     ----- | -----------
     1     | Omslutning fastholdelsesanordning skrue

Næste trin er at kabel enheden til power-, Netværks- og seriel adgang.

## <a name="cable-your-storsimple-8100-device"></a>Enheden StorSimple 8100-kabel

Følgende procedurer viser, hvordan du kabel enheden StorSimple 8100 til power-, Netværks- og serielle forbindelser.

### <a name="prerequisites"></a>Forudsætninger

Før du begynder kabler på din enhed, skal du:

- Forbindelse til din enhed til lagring, pakkes helt ud og rack.

- 2 power-kabler, der fulgte med enheden

- Adgang til 2 Power fordeling enheder (anbefales).

- Netværkskablerne

- Angivet serielle kabler

- Seriel USB-konverteringsprogram med den korrekte driver installeret på din PC (hvis det er nødvendigt)

- Angivet 4 QSFP-til-SFP + kort til brug med 10 GbE netværksgrænseflader

- [Understøttede hardware for 10 GbE netværksgrænseflader på enheden StorSimple](storsimple-supported-hardware-for-10-gbe-network-interfaces.md)


### <a name="power-cabling"></a>Power-kabler

Din enhed omfatter overflødige Power og køling moduler (PCMs). Både PCMs skal være installeret og forbindelse til forskellige power kilder for at sikre høj tilgængelighed.

Udfør følgende trin for at enheden til power-kabel.

[AZURE.INCLUDE [storsimple-cable-8100-for-power](../../includes/storsimple-cable-8100-for-power.md)]

### <a name="network-cabling"></a>Netværkskabler

Enheden er en aktiv standby konfiguration: til enhver tid, en controller modul er aktiv og behandling af alle disk og netværk handlinger, mens andre controller modulet er i standby. Hvis en controller mislykkes, skal den standby controller er aktiveret med det samme og fortsætter alle disk og netværk handlinger.

Hvis du vil understøtter denne overflødige controller failover, skal du kabel netværket enhed, som beskrevet i følgende trin.

#### <a name="to-cable-for-network-connection"></a>Til kablet til netværksforbindelse

1. Enheden er seks netværksgrænseflader på hver controller: fire 1 Gbps og to 10 Gbps Ethernet-porte. Identificere de forskellige dataporte på backplane på din enhed.

    ![Backplane 8100 enhed](./media/storsimple-8100-hardware-installation/HCSBackplaneof2UDevicewithPortsLabeled.jpg)

    **Bagsiden af den enhed, der viser dataporte**

     Etiket   | Beskrivelse
     ------- | -----------
     0,1,4,5 |  1 GbE netværksgrænseflader
     2,3     | 10 GbE netværksgrænseflader
     6       | Serielle porte

2. Se i det følgende diagram til netværkskabler. (Den mindste netværkskonfiguration vises med blå streger. Yderligere konfiguration, der kræves til høj tilgængelighed og ydeevne vises med stiplede linjer.)


    ![Kabel enheden 2U til netværket](./media/storsimple-8100-hardware-installation/HCSCableYour2UDeviceforNetwork.png)

    **Netværk-kablet til din enhed**


  	|Etiket | Beskrivelse |
  	|----- | ----------- |
  	| A    | LAN med forbindelse til internettet |
  	| B    | Controller 0 |
  	| C    | PCM 0 |
  	| D    | Controller 1 |
  	| E    | PCM 1 |
  	| F, G | Værter |
  	| 0-5  | Netværksgrænseflader |



Når kabler enheden, kræver det mindste konfiguration:


- Mindst to netværksgrænseflader forbindelse på hver controller med én til skyadgang og én til iSCSI. DATA 0 port er automatisk aktiveret og konfigureret via seriel konsollen på enheden. Ud over DATA 0 skal en anden dataport også konfigureres via Azure klassisk portalen. I dette tilfælde forbinde DATA 0 port til den primære LAN (netværk med adgang til internettet). De andre dataporte kan være forbundet til SAN/iSCSI LAN, der er adskilt segment af netværket, afhængigt af den tiltænkte rolle.

- Identiske grænseflader på hver controller forbindelse til den samme netværk for at sikre, at tilgængelige Hvis controller serverskift. Hvis du vælger at forbinde DATA 0 og DATA 3 for et af enheder, skal du eksempelvis opretter forbindelse de tilsvarende DATA 0 og DATA 3 på anden controlleren.

Husk på for høj tilgængelighed og ydeevne:


- Når det er muligt, kan du konfigurere et par af netværk brugergrænseflade til skyadgang (1 GbE) og en anden par til iSCSI (10 GbE anbefales) på hver controller.

- Forbind netværksgrænseflader fra hver controller til to forskellige parametre til at sikre, at tilgængelige mod en Skift fejl, når det er muligt. I figur illustreres to 10 GbE netværk grænseflader, DATA 2 og 3, skal du DATA fra hver controller, der er forbundet med to forskellige parametre.

Yderligere oplysninger finder du referere til **netværksgrænseflader** under [Høj tilgængelighedskrav til enheden StorSimple](storsimple-system-requirements.md#high-availability-requirements-for-storsimple).

>[AZURE.NOTE] Hvis du bruger SFP + skal med din 10 GbE netværksgrænseflader, skal du bruge de medfølgende QSFP-SFP + kort. Gå til [understøttede hardware for 10 GbE netværksgrænseflader på enheden StorSimple](storsimple-supported-hardware-for-10-gbe-network-interfaces.md)kan finde flere oplysninger.



### <a name="serial-port-cabling"></a>Seriel port kabler

Udfør følgende trin for at kabel den serielle port.

#### <a name="to-cable-for-serial-connection"></a>Til kablet til seriel forbindelse

1. Din enhed har en seriel port på hver controller, der er identificeret med et skruenøgleikon for. Se illustrationen i afsnittet [netværk kabler](#network-cabling) til at finde de serielle porte på backplane på din enhed.

2. Identificere den aktive controller på din enhed backplane. En blinkende blå LED angiver, at controlleren er aktiv.

3. Brug de medfølgende serielle kabler (hvis det er nødvendigt, USB-seriel konverteringsprogram til din bærbare computer), og oprette forbindelse til den serielle port af den aktive controller din konsol eller computer (med terminalemulering på enheden).

4. Installere serielt-USB-driverne (følger med enheden) på din computer.

5. Konfigurere den serielle forbindelse på følgende måde: 115.200 baud, 8 databit, 1 stopbit, ikke fungerer ensartet og flow-styring indstillet til ingen.

6. Kontrollér, at forbindelsen fungerer ved at trykke på Enter på konsollen. En seriel konsol menu skal vises.

>[AZURE.NOTE] **Backupløsning administration**: Når enheden er installeret i en ekstern datacenter eller på en computer rum med begrænset adgang, Kontrollér, at de serielle forbindelser til begge enheder altid har forbindelse til en seriel konsol skifte eller tilsvarende udstyr. Dermed kan out-of-band-fjernbetjening og understøttelse af handlinger, hvis der er netværk afbrydelser eller uventet fejl.

Enheden er nu kabel til power, netværksadgang og seriel forbindelse. Næste trin er at konfigurere softwaren og installere din enhed.

## <a name="next-steps"></a>Næste trin

Lær, hvordan du [installerer og konfigurerer din lokale StorSimple enhed](storsimple-deployment-walkthrough.md).
