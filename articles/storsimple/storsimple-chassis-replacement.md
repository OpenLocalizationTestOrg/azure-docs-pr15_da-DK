<properties 
   pageTitle="Erstatte kabinettet på en enhed med StorSimple | Microsoft Azure"
   description="Beskriver, hvordan du fjerner og erstatter kabinettet til din StorSimple primære omslutning eller EBOD omslutning."
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

# <a name="replace-the-chassis-on-your-storsimple-device"></a>Erstatte kabinettet på enheden StorSimple

## <a name="overview"></a>Oversigt

Dette selvstudium forklarer, hvordan du fjerner og erstatter et kabinet i en StorSimple 8000 serie enhed. StorSimple 8100 modellen er en enkelt omslutning-enhed (én kabinet), mens 8600 er en enhed med to omslutning (to kabinetter). For en 8600 model, er der potentielt to kabinet, som kan mislykkes i enheden: kabinettet for den primære omslutning eller kabinettet for EBOD omslutning.

I begge tilfælde er erstatning kabinettet, som leveres af Microsoft tom. Ingen Power og køling moduler (PCMs), controller moduler dækkende tilstand disk SSD'er (), harddiske (harddiske) eller EBOD moduler, medtages.

>[AZURE.IMPORTANT] Gennemgå oplysningerne om sikkerhed i [StorSimple hardware-komponent erstatning](storsimple-hardware-component-replacement.md)før fjerne og erstatte kabinettet.

## <a name="remove-the-chassis"></a>Fjerne kabinettet

Udfør følgende trin for at fjerne kabinettet på enheden StorSimple.

#### <a name="to-remove-a-chassis"></a>Fjerne et kabinet

1. Sørg for, at StorSimple enheden er lukket ned og afbrudt fra alle kilderne, power.

2. Fjerne alle netværks- og SAS kabler, hvis det er relevant.

3. Fjern enheden fra racket.

4. Fjerne hver af drevene, og notér de pladser, hvorfra de fjernes. Yderligere oplysninger finder du se [fjerne diskdrevet](storsimple-disk-drive-replacement.md#remove-the-disk-drive).

5. På EBOD omslutning (Hvis dette er det chassis, der mislykkedes), fjerne EBOD controller moduler. Yderligere oplysninger finder du se [fjerne en EBOD controller](storsimple-ebod-controller-replacement.md#remove-an-ebod-controller). 

    På den primære omslutning (Hvis dette er det chassis, der mislykkedes), fjerne enheder, og notér de pladser, hvorfra de fjernes. Yderligere oplysninger finder du se [fjerne en controller](storsimple-controller-replacement.md#remove-a-controller).

## <a name="install-the-chassis"></a>Installere kabinettet

Udfør følgende trin for at installere kabinettet på enheden StorSimple.

#### <a name="to-install-a-chassis"></a>Installere et kabinet

1. Tilslutte kabinettet i racket. Se [Rack-tilslutning enheden StorSimple 8100](storsimple-8100-hardware-installation.md#rack-mount-your-storsimple-8100-device) eller [Rack-tilslutning enheden StorSimple 8600](storsimple-8600-hardware-installation.md#rack-mount-your-storsimple-8600-device)kan finde flere oplysninger.

2. Når kabinettet er forbundet med racket, kan du installere controller modulerne placeret på samme måde, som de tidligere var installeret i.

3. Installér drevene i de samme stillinger og pladser, som de tidligere var installeret i.

    >[AZURE.NOTE] Vi anbefaler, at du installerer på SSDs i stik først og derefter installere harddiske.

2. Tilslut din enhed til de relevante power kilder med den enhed, der er forbundet med racket samt components være installeret, og tænd for enheden. Yderligere oplysninger finder du [kabel enheden StorSimple 8100](storsimple-8100-hardware-installation.md#cable-your-storsimple-8100-device) eller [kabel enheden StorSimple 8600](storsimple-8600-hardware-installation.md#cable-your-storsimple-8600-device).

## <a name="next-steps"></a>Næste trin

Lær mere om [StorSimple hardware-komponent erstatning](storsimple-hardware-component-replacement.md).

