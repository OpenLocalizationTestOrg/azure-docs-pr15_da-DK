<properties 
   pageTitle="Erstatte en StorSimple EBOD controller | Microsoft Azure"
   description="Beskriver, hvordan du fjerner og erstatter en eller begge EBOD enheder på en enhed med StorSimple 8600."
   services="storsimple"
   documentationCenter=""
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

# <a name="replace-an-ebod-controller-on-your-storsimple-device"></a>Erstatte en EBOD controller på enheden StorSimple

## <a name="overview"></a>Oversigt

Dette selvstudium forklares det, hvordan du erstatter et fejlbehæftede EBOD controller modul på enheden Microsoft Azure StorSimple. Hvis du vil erstatte et EBOD controller modul, skal du:

- Fjerne den fejlbehæftede EBOD controller
- Installere en ny EBOD controller

Overvej følgende oplysninger, inden du går i gang:

- Tom EBOD moduler skal indsættes i alle ubrugte pladser. Omslutning Smart bliver ikke korrekt, hvis en slot stadig er åben.

- EBOD controller er hot-swap og kan fjernes eller erstattet. Fjern ikke et mislykkedes modul, indtil du har en erstatning. Når du starter erstatning processen, skal du afslutte det i 10 minutter.

>[AZURE.IMPORTANT] Før du forsøger at fjerne eller erstatte en hvilken som helst StorSimple komponent, Sørg for, at du gennemser [safety ikonet konventioner](storsimple-safety.md#safety-icon-conventions) og andre [sikkerhedsforanstaltninger](storsimple-safety.md).

## <a name="remove-an-ebod-controller"></a>Fjerne en EBOD controller

Inden erstatter det fejlbehæftede EBOD controller modul i enheden StorSimple, skal du kontrollere, at andre EBOD controller modulet er aktive og kører. Følgende procedure og tabel beskriver, hvordan du fjerner modulet EBOD controller.

#### <a name="to-remove-an-ebod-module"></a>Fjerne et EBOD modul

1. Åbn Azure klassisk-portalen.

2. Gå til **enheder** > **vedligeholdelse** > **Hardware Status**, og Bekræft, at status for LED for det aktive EBOD controller modul er grøn, og LED for modulet mislykkedes EBOD controller er røde.

3. Find det fejlbehæftede EBOD controller modul bag enheden.

4. Fjern de kabler, der forbinder modulet EBOD controller til domænecontrolleren, før du udfører modulet EBOD af systemet.

5. Skal du notere den nøjagtige SAS port i modulet EBOD controller, der er forbundet til controlleren. Du bliver bedt om at gendanne systemet til denne konfiguration, når du har erstattet modulet EBOD. 

    >[AZURE.NOTE] Dette er typisk Port A, som er angivet som **vært på** i følgende diagram.

    ![Backplane EBOD controller](./media/storsimple-ebod-controller-replacement/IC741049.png)

     **Figur 1** Bagsiden af EBOD modul

  	|Etiket|Beskrivelse|
  	|:----|:----------|
  	|1|Fejl LED|
  	|2|Strøm-Indikatoren|
  	|3|SAS forbindelser|
  	|4|SAS indikatorer|
  	|5|Serielle porte factory kun til brug|
  	|6|Port en (vært i)|
  	|7|Port B (vært ud)|
  	|8|Port C (kun Factory-brug)|

## <a name="install-a-new-ebod-controller"></a>Installere en ny EBOD controller

Følgende procedure og tabel beskriver, hvordan du installerer et EBOD controller modul i enheden StorSimple.

#### <a name="to-install-an-ebod-controller"></a>Sådan installeres et EBOD controller

1. Kontrollere enheden, EBOD for skader, især til interface connector. Undlad at installere den nye EBOD controller, hvis en hvilken som helst ben er bøjet.

2. Angiv modulet til omslutning med låse i åben stilling, indtil låse deltage.

    ![Installere EBOD controller](./media/storsimple-ebod-controller-replacement/IC741050.png)

    **Figur 2**  Installere modulet EBOD controller

3. Luk låsen. Du skal høre et enkelt klik, som låsen deltager.

    ![Frigive EBOD låsen](./media/storsimple-ebod-controller-replacement/IC741047.png)

    **Figur 3**  Lukke EBOD modul låsen

4. Genopret kablerne. Brug den nøjagtige konfiguration, der blev fundet før erstatning. Se følgende diagram og tabel for at få oplysninger om, hvordan du opretter forbindelse kabler.

    ![Enheden 4U til power-kabel](./media/storsimple-ebod-controller-replacement/IC770723.png)

    **Figur 4**. Genoprette forbindelse til kabler

  	|Etiket|Beskrivelse|
  	|:----|:----------|
  	|1|Primære omslutning|
  	|2|PCM 0|
  	|3|PCM 1|
  	|4|Controller 0|
  	|5|Controller 1|
  	|6|EBOD controller 0|
  	|7|EBOD controller 1|
  	|8|EBOD omslutning|
  	|9|Power-fordelingen enheder|

## <a name="next-steps"></a>Næste trin

Lær mere om [StorSimple hardware-komponent erstatning](storsimple-hardware-component-replacement.md).
