<properties 
   pageTitle="Slå enheden StorSimple til eller fra | Microsoft Azure"
   description="Beskriver, hvordan du slår en ny StorSimple enhed, aktivere en enhed, der blev lukket ned eller mistet power og deaktivere en, der kører enhed."
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
   ms.date="08/23/2016"
   ms.author="alkohli" />

# <a name="turn-your-storsimple-device-on-or-off"></a>Slå enheden StorSimple til eller fra 

## <a name="overview"></a>Oversigt

Lukke en Microsoft Azure StorSimple enhed er ikke påkrævet som en del af normal drift. Skal du kan aktivere en ny enhed eller en enhed, der havde kan lukkes. Generelt er en lukning påkrævet i tilfælde, hvor du skal erstatte mislykkedes hardware, fysisk flytte en enhed eller overtage en enhed af tjenesten. I dette selvstudium beskrives den nødvendige fremgangsmåde for at aktivere og lukke enheden StorSimple i forskellige scenarier.

I følgende tabel vises forskellige scenarier for at aktivere og lukke enheden StorSimple og indeholder links til de relevante procedurer.

|Scenarie|Referenceemner|
|:-------|:---------------|
|Aktivere en ny enhed|[Aktivere en ny enhed](#turn-on-a-new-device)<ul><li>[Nye enhed med primære omslutning](#new-device-with-primary-enclosure-only)</li><li>[Nye enhed med EBOD omslutning](#new-device-with-ebod-enclosure)</li></ul>|
|Aktivere en enhed efter lukning|[Aktivere en enhed efter lukning](#turn-on-a-device-after-shutdown)<ul><li>[Enhed med primære omslutning](#device-with-primary-enclosure-only)</li><li>[Enhed med EBOD omslutning](#device-with-ebod-enclosure)</li></ul>|
|Aktivere en enhed efter en strømafbrydelse|[Aktivere en enhed efter en strømafbrydelse](#turn-on-a-device-after-a-power-loss)<ul><li>[Enhed med primære omslutning](#8100)</li><li>[Enhed med EBOD omslutning](#8600)</li></ul>|
|Aktivere en enhed efter den primære omslutning og EBOD forbindelsen afbrydes|[Aktivere en enhed efter primært og EBOD omslutning forbindelsen afbrydes](#turn-on-a-device-after-the-primary-and-ebod-enclosure-connection-is-lost)|
|Lukke en igangværende enhed|[Deaktivere en, der kører enhed](#turn-off-a-running-device)<ul><li>[Enhed med primære omslutning](#8100a)</li><li>[Enhed med EBOD omslutning](#8600a)</li></ul>|

## <a name="turn-on-a-new-device"></a>Aktivere en ny enhed

Trinnene til aktivering af en StorSimple enhed for første gang er forskellige, afhængigt af om enheden er en 8100 eller en 8600 model. 8100 har en enkelt primær omslutning, mens 8600 er en enhed med to omslutning med en primær omslutning og en EBOD omslutning. Detaljeret vejledning til begge modeller er omfattet af de følgende afsnit.

- [Nye enhed med primære omslutning](#new-device-with-primary-enclosure-only)

- [Nye enhed med EBOD omslutning](#new-device-with-ebod-enclosure)

### <a name="new-device-with-primary-enclosure-only"></a>Nye enhed med primære omslutning

StorSimple 8100 modellen er en enkelt omslutning enhed. Enheden indeholder overflødige Power og køling moduler (PCMs). Både PCMs skal være installeret og forbindelse til forskellige power kilder for at sikre høj tilgængelighed.

Udfør følgende trin for at enheden til power-kabel.

[AZURE.INCLUDE [storsimple-cable-8100-for-power](../../includes/storsimple-cable-8100-for-power.md)]

>[AZURE.NOTE]Gå til [installere enheden StorSimple 8100](storsimple-8100-hardware-installation.md)for indstillinger for fuldført enhed og kabler instruktioner. Sørg for, at du har fulgt instruktionerne nøjagtigt.

### <a name="new-device-with-ebod-enclosure"></a>Nye enhed med EBOD omslutning

StorSimple 8600 modellen har både en primær omslutning og en EBOD omslutning. Dette kræver, at enhederne, der skal være kabel sammen for seriel vedhæftet SCSI (SAS) forbindelsen og power.

Når du konfigurerer denne enhed for første gang, skal udføre trinnene for SAS kabler først og derefter udføre trinnene til power-kabler.

[AZURE.INCLUDE [storsimple-sas-cable-8600](../../includes/storsimple-sas-cable-8600.md)]

[AZURE.INCLUDE [storsimple-cable-8600-for-power](../../includes/storsimple-cable-8600-for-power.md)]

>[AZURE.NOTE]Gå til [installere enheden StorSimple 8600](storsimple-8600-hardware-installation.md)for indstillinger for fuldført enhed og kabler instruktioner. Sørg for, at du har fulgt instruktionerne nøjagtigt.

## <a name="turn-on-a-device-after-shutdown"></a>Aktivere en enhed efter lukning

Trinnene til aktivering af en StorSimple enhed, når den er blevet lukket er forskellige afhængigt af om enheden er en 8100 eller en 8600 model. 8100 har en enkelt primære omslutning, mens 8600 er en to-omslutning enhed med en primær omslutning og en EBOD omslutning.

- [Enhed med primære omslutning](#device-with-primary-enclosure-only)

- [Enhed med EBOD omslutning](#device-with-ebod-enclosure)

### <a name="device-with-primary-enclosure-only"></a>Enhed med primære omslutning

Brug følgende fremgangsmåde til at slå en StorSimple-enhed med en primær omslutning og ingen EBOD omslutning efter en lukning.

#### <a name="to-turn-on-a-device-with-a-primary-enclosure-only"></a>Aktivere en enhed med en primær omslutning

1. Sørg for, at en potens skifter på begge Power og køling moduler (PCMs) er i positionen fra. Hvis parametrene ikke er i positionen fra, spejlvende dem til positionen fra, og vent lys til at blive sendt.

2. Tænd for enheden ved spejlvending power parametre på begge PCMs til indstillet. Enheden bør aktivere.

3. Kontrollér følgende for at bekræfte, at enheden er fuldt ud på:

    1. OK indikatorer på begge PCM moduler er grønne.

    2. Statusindikatorer på begge enheder er grønt.

    3. Den blå Indikator på en over enheder, der blinker, hvilket angiver, at controlleren er aktiv.

    Hvis nogen af disse betingelser ikke opfyldes, er enheden ikke sund. Kan du [kontakte Microsoft Support](storsimple-contact-microsoft-support.md).

### <a name="device-with-ebod-enclosure"></a>Enhed med EBOD omslutning

Brug følgende fremgangsmåde til at slå en StorSimple-enhed med en primær omslutning og en EBOD omslutning efter en lukning. Udføre nøjagtigt, som er beskrevet hvert trin i rækkefølge. Fejl ved at gøre dette kan medføre tab af data.

#### <a name="to-turn-on-a-device-with-a-primary-and-an-ebod-enclosure"></a>Aktivere en enhed med en primær og en EBOD omslutning

1. Sørg for, at EBOD omslutning er forbundet med den primære omslutning. Se [installere enheden StorSimple 8600](storsimple-8600-hardware-installation.md)kan finde flere oplysninger.

2. Sørg for, at Power og køling moduler (PCMs) på både EBOD og primære bilag er i positionen fra. Hvis parametrene ikke er i positionen fra, spejlvende dem til positionen fra, og vent lys til at blive sendt.

3. Aktivér EBOD omslutning første ved spejlvending power parametre på begge PCMs til indstillet. PCM indikatorer bør være grønne. En grøn EBOD-controller LED på denne enhed angiver, at EBOD omslutning på.

4. Aktivér de primære omslutning ved spejlvending power parametre på begge PCMs til indstillet. Hele systemet skulle nu være på.

5. Kontrollér, at SAS indikatorer er grønt, hvilket sikrer, at forbindelsen mellem EBOD omslutning og det primære omslutning er god.

## <a name="turn-on-a-device-after-a-power-loss"></a>Aktivere en enhed efter en strømafbrydelse

Et strømsvigt eller afbrudt kan lukke en StorSimple enhed. Strømsvigt kan udføres på en af power forsyninger eller begge power forsyninger. Gendannelse trinnene er forskellige, afhængigt af om enheden er en 8100 eller et 8600 model. 8100 har en enkelt primære omslutning, mens 8600 er en to-omslutning enhed med en primær omslutning og en EBOD omslutning. I dette afsnit beskrives gendannelse proceduren for hvert scenario.

- [Enhed med primære omslutning](#8100)

- [Enhed med EBOD omslutning](#8600)

### <a name="device-with-primary-enclosure-only-a-name8100"></a>Enhed med primære omslutning<a name="8100">

Systemet kan fortsætte med driften normal, hvis der er strømsvigt til en af dens power forsyninger. Men, for at sikre høj tilgængelighed på enheden, gendanne power power levering så tidligt som muligt.

Hvis der er et strømsvigt eller en strømafbrydelse på begge power forsyninger, lukkes systemet på en velordnet og kontrolleret måde. Når power genoprettes, bliver automatisk systemet.

### <a name="device-with-ebod-enclosure-a-name8600"></a>Enhed med EBOD omslutning<a name="8600">

#### <a name="power-loss-on-one-power-supply"></a>Strømsvigt på én power leverer

Systemet kan fortsætte med driften normal, hvis der er strømsvigt til en af dens power forsyninger på den primære omslutning eller EBOD omslutning. Men, for at sikre høj tilgængelighed af enheden, kan du gendanne power power levering så tidligt som muligt.

#### <a name="power-loss-on-both-power-supplies-on-primary-and-ebod-enclosures"></a>Strømsvigt på begge power forsyninger på primær og EBOD bilag

Hvis der er et strømsvigt eller en strømafbrydelse på begge power forsyninger, lukkes EBOD omslutning med det samme, og det primære omslutning lukkes på en velordnet og kontrolleret måde. Når power er gendannet, skal starte maskinen automatisk.

Hvis power er slået fra manuelt, derefter gør følgende for at gendanne power til systemet.

1. Slå EBOD omslutning.

2. Når EBOD omslutning er slået til, kan du slå den primære omslutning.

### <a name="power-loss-on-both-power-supplies-on-ebod-enclosure"></a>Strømsvigt på begge power forsyninger på EBOD omslutning

Når du konfigurerer dine kabler, skal du sikre dig, at EBOD aldrig er forbundet alene til en separat PDU. Hvis EBOD og primære omslutning ikke på samme tid, vil systemet gendanne.

Hvis der kun EBOD omslutning mislykkes i begge power forsyninger, vil systemet ikke automatisk genoprette. Benyt følgende fremgangsmåde for at aktivere systemet og gendanne den til en sund tilstand:

1. Hvis den primære omslutning er slået til, kan du skifte fra både Power og køling moduler (PCMs).

2. Vent et par minutter til system til at gå ned.

3. Slå EBOD omslutning.

4. Når EBOD omslutning er slået til, kan du slå den primære omslutning.

## <a name="turn-on-a-device-after-the-primary-and-ebod-enclosure-connection-is-lost"></a>Aktivere en enhed efter primært og EBOD omslutning forbindelsen afbrydes

Hvis forbindelsen afbrydes mellem standby controller og den tilsvarende EBOD controller, fortsat fungerer enheden. Hvis forbindelsen mellem den aktive systemcontroller og den tilsvarende EBOD controller går tabt, failover skal udføres, og enheden skal fortsætte med at arbejde som normalt.

Når begge Serial vedhæftet SCSI (SAS) kabler fjernes, eller forbindelsen mellem EBOD omslutning og det primære omslutning er vigtige, går enheden ned. På dette tidspunkt udføre følgende trin.

### <a name="to-turn-on-the-device-after-connection-is-lost"></a>Slå enheden, når forbindelsen bliver afbrudt

1. Få adgang til baggrunden på enheden.

2. Hvis SAS kablet forbindelse mellem EBOD omslutning og det primære omslutning er brudt, bliver alle SAS bane indikatorer på EBOD omslutning fra.

3. Luk både Power og køling moduler (PCMs) på EBOD omslutning og det primære omslutning.

4. Vent, indtil alle indikatorer på bagsiden af begge bilag slå fra.

5. Indsætte SAS kabler, og Sørg for, at der er en god forbindelse mellem EBOD omslutning og det primære omslutning.

6. Aktivér EBOD omslutning første ved spejlvending begge PCM parametre til indstillet.

7. Sørg for, at EBOD omslutning er aktiveret ved at kontrollere, at den grønne LED er.

8. Slå den primære omslutning.

9. Sørg for, at den primære omslutning er aktiveret ved at kontrollere, at controller grøn Indikator er slået.

10. Kontrollér, at EBOD omslutning forbindelsen med den primære omslutning er god ved at kontrollere, at SAS bane indikatorer (fire af hvert EBOD controller) er alle på.

>[AZURE.IMPORTANT] Hvis SAS kabler er defekt eller forbindelsen mellem EBOD omslutning og det primære omslutning er ikke godt, når du slår systemet, bliver det går tilstanden nedbrud. Kan du [kontakte Microsoft Support](storsimple-contact-microsoft-support.md) , hvis dette sker.

## <a name="turn-off-a-running-device"></a>Deaktivere en, der kører enhed

En igangværende StorSimple-enhed kan være nødvendigt at blive lukket ned, hvis det er skal flyttes, udføres af tjenesten, eller har en defekt komponent, der skal erstattes. Trinnene er forskellige, afhængigt af om StorSimple enheden er en 8100 eller en 8600 model. 8100 har en enkelt primær omslutning, mens 8600 er en enhed med to omslutning med en primær omslutning og en EBOD omslutning. Dette afsnit indeholder oplysninger om disse trin for at lukke en igangværende enhed.

- [Enhed med primære omslutning](#8100a)

- [Enhed med EBOD omslutning](#8600a)

### <a name="device-with-primary-enclosure-a-name8100a"></a>Enhed med primære omslutning<a name="8100a"> 

For at lukke enheden på en velordnet og kontrolleret måde, kan du gøre det via Azure klassisk portalen eller via Windows PowerShell til StorSimple. 

>[AZURE.IMPORTANT] Der ikke Luk en igangværende enhed ved hjælp af knappen power på bagsiden af enheden.
>
>Før du lukker enheden, Kontrollér, at alle enhedskomponenter er sund. Gå til **enheder**på Azure-klassisk portalen > **vedligeholdelse** > **Hardware Status**, og Bekræft, at status for alle komponenter er grøn. Dette gælder kun for en sund system. Hvis systemet lukkes ned for at erstatte en defekt komponent, du får vist en mislykkedes (rød) eller nedsat (gul) statussen for den pågældende komponent i **Hardware Status**.

Når du får adgang til Windows PowerShell til StorSimple eller Azure klassisk portalen, kan du følge trinnene i [lukke en StorSimple-enhed](storsimple-manage-device-controller.md#shut-down-a-storsimple-device). 

### <a name="device-with-ebod-enclosure-a-name8600a"></a>Enhed med EBOD omslutning<a name="8600a">

>[AZURE.IMPORTANT] Før du lukker den primære omslutning og EBOD omslutning, sikre, at alle enhedskomponenter sund. Gå til **enheder**på Azure-klassisk portalen > **vedligeholdelse** > **Hardware Status**, og Kontrollér, at alle komponenter er sund.

#### <a name="to-shut-down-a-running-device-with-ebod-enclosure"></a>Lukke en igangværende enhed med EBOD omslutning

1. Følg de trin, der er angivet i [lukke en StorSimple enhed](storsimple-manage-device-controller.md#shut-down-a-storsimple-device) til den primære omslutning.

2. Når den primære omslutning lukkes ned, du lukke EBOD ved spejlvending fra både Power og køling modul (PCM) parametre.

3. Kontrollér, at alle indikatorer på bagsiden af EBOD omslutning er fra for at bekræfte, at EBOD er lukket ned.

>[AZURE.NOTE] De SAS kabler, der bruges til at oprette forbindelse EBOD omslutning til den primære omslutning skal ikke fjernes indtil efter systemet er lukket.

## <a name="next-steps"></a>Næste trin

[Kontakt Microsoft Support](storsimple-contact-microsoft-support.md) , hvis du støder på problemer ved at aktivere eller lukke en StorSimple enhed.

