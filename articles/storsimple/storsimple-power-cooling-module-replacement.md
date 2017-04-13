<properties 
   pageTitle="Erstatte en PCM på enheden StorSimple | Microsoft Azure"
   description="Beskriver, hvordan du fjerner og erstatter Power og køling modul (PCM) på enheden StorSimple"
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
   ms.date="08/18/2016"
   ms.author="alkohli" />

# <a name="replace-a-power-and-cooling-module-on-your-storsimple-device"></a>Erstatte en Power og køling modul på enheden StorSimple

## <a name="overview"></a>Oversigt

Power og køling modul PCM () i enheden Microsoft Azure StorSimple består af en power levering og afkølingsblæsere, der styres gennem primær og EBOD bilag. Der er kun én model af PCM, der er certificeret til hver omslutning. Den primære omslutning er certificeret til en 764 W PCM og EBOD omslutning er certificeret til en 580 W PCM. Selvom PCMs til den primære omslutning og EBOD omslutning er forskellige, erstatning proceduren er den samme.

Dette selvstudiet beskrives, hvordan du:

- Fjerne en PCM
- Installere en erstatning PCM

>[AZURE.IMPORTANT] Gennemgå oplysningerne om sikkerhed i [StorSimple hardware-komponent erstatning](storsimple-hardware-component-replacement.md)før fjerne og erstatte en PCM.

## <a name="before-you-replace-a-pcm"></a>Før du erstatter et PCM

Vær opmærksom på følgende vigtige problemer, før du erstatter din PCM:

- Hvis power levering af PCM mislykkes, lade det fejlbehæftede modul, der er installeret, men fjerne netledningen. Vifte fortsat modtage power fra omslutning og fortsætte med at angive stort køling. Hvis vifte mislykkes, skal PCM erstattes med det samme.

- Før du fjerner PCM Afbryd strømmen af PCM ved at primære slukke (hvis det findes) eller ved at fjerne fysisk netledningen. Dette giver en advarsel til dit system, en power lukning er overhængende.

- Sørg for, at de andre PCM fungerer for fortsat drift før du erstatter den fejlbehæftede PCM. En defekt PCM skal erstattes med en fuldt funktionsdygtige PCM så tidligt som muligt.

- PCM modul erstatning tager kun få minutter at gennemføre, men den skal fuldføres inden for 10 minutter for at fjerne den mislykkedes PCM for at forhindre overophedning.

- Bemærk, at de erstatning 764 W PCM moduler leveret fra factory ikke indeholder ekstra batterimodul. Du skal fjerne batteriet fra din fejlbehæftede PCM og derefter indsætte det i modulet erstatning før du udfører erstatning. Finde flere oplysninger om at [fjerne og indsætte en ekstra batterimodul](storsimple-battery-replacement.md).


## <a name="remove-a-pcm"></a>Fjerne en PCM

Følg disse instruktioner, når du er klar til at fjerne en Power og køling modul (PCM) fra enheden Microsoft Azure StorSimple.

>[AZURE.NOTE] Inden du fjerner din PCM, skal du bekræfte, at du har en korrekte erstatning (764 W for den primære omslutning) eller 580 W for EBOD omslutning.

#### <a name="to-remove-a-pcm"></a>Fjerne en PCM

1. Klik på **enheder**på Azure-klassisk portalen > **vedligeholdelse** > **Hardware Status**. Kontrollere status for PCM komponenter under **Delt komponenter** for at identificere, hvilke PCM mislykkes:

     - Hvis en power levering i PCM 0 mislykkes, er status for **Power leverer i PCM 0** bliver røde.

     - Hvis en power levering i PCM 1 mislykkes, er status for **Power leverer i PCM 1** bliver røde.

     - Hvis vifte i PCM 1 mislykkes, er status for **køling 0 for PCM 0** eller **køling 1 for PCM 0** bliver røde.

2. Find den mislykkedes PCM på bagsiden af den primære omslutning. Hvis du kører en 8600 model, skal du identificere den primære omslutning ved at se på det System enhed id-nummer, der vises på panelet forside LED-displayet. Standard enheds-ID vises på den primære omslutning er **00**, mens enheds-ID vises på EBOD omslutning standardværdien er **01**. I følgende diagram og tabel beskriver panelet forside i LED-displayet.

    ![System-ID på forside OPS panel](./media/storsimple-power-cooling-module-replacement/IC740991.png)

     **Figur 1** Front panel på enheden  

  	|Etiket|Beskrivelse|
  	|:---|:-----------|
  	|1|Slå knappen|
  	|2|Tænd|
  	|3|Et modul|
  	|4|Logiske fejl|
  	|5|Enhed id-skærm|

3. Indikatoren overvågning indikatorer bag ved den primære omslutning kan også bruges til at identificere den fejlbehæftede PCM. Se følgende diagram og tabellen for at forstå, hvordan du bruger LED-indikatorerne til at finde den fejlbehæftede PCM. Eksempelvis hvis LED svarer til den **Vifte mislykkes** lyser, mislykkes vifte. Hvis den Indikator, der svarer til **AC fejl** lyser, mislykkes power levering på samme måde. 

    ![Backplane af enhed PCM overvågning indikator indikatorer](./media/storsimple-power-cooling-module-replacement/IC740992.png)

     **Figur 2** Bagsiden af PCM med led

  	|Etiket|Beskrivelse|
  	|:---|:-----------|
  	|1|AC strømsvigt|
  	|2|Ventilatorfejl|
  	|3|Batteri fejl|
  	|4|PCM OK|
  	|5|DC strømsvigt|
  	|6|Batteri sund|

4. Se i det følgende diagram af bagsiden af StorSimple kameraet, for at finde det fejlbehæftede PCM modul. PCM 0 er placeret til venstre og PCM 1 til højre. Den følgende tabel beskrives det, modulerne.

     ![Backplane af enhed primære omslutning moduler](./media/storsimple-power-cooling-module-replacement/IC740994.png)

     **Figur 3** Bagsiden af enheden med plug-in'en moduler 

  	|Etiket|Beskrivelse|
  	|:---|:-----------|
  	|1|PCM 0|
  	|2|PCM 1|
  	|3|Controller 0|
  	|4|Controller 1|

5. Sluk fejlbehæftede PCM, og træk levering netledningen. Nu kan du fjerne PCM.

6. Forstå låsen og side af PCM håndtaget mellem din rulleboks og pege, og disse dem sammen for at åbne i håndtaget.

    ![Åbning af PCM Handle](./media/storsimple-power-cooling-module-replacement/IC740995.png)

    **Figur 4** Åbne PCM håndtag

7. Tager fat om håndtaget, og fjern PCM.

    ![Fjernelse af enhed PCM](./media/storsimple-power-cooling-module-replacement/IC740996.png)

    **Figur 5** Fjerne PCM

## <a name="install-a-replacement-pcm"></a>Installere en erstatning PCM

Følg disse instruktioner for at installere en PCM på enheden StorSimple. Sørg for, at du har indsat ekstra batterimodul før du installerer erstatning PCM (gælder kun 764 W PCMs). Finde flere oplysninger om at [fjerne og indsætte en ekstra batterimodul](storsimple-battery-replacement.md).

#### <a name="to-install-a-pcm"></a>Installere en PCM

1. Kontrollér, at du har den korrekte erstatning PCM for denne omslutning. Den primære omslutning skal have en 764 W PCM og EBOD omslutning skal en 580 W PCM. Du bør ikke forsøge at bruge den 580 W PCM i den primære omslutning eller den 764 W PCM i EBOD omslutning. Følgende billede viser, hvor du vil identificere disse oplysninger på etiketten, der er fastgjort til PCM.

    ![Enhed PCM etiket](./media/storsimple-power-cooling-module-replacement/IC740973.png)

    **Figur 6** PCM etiket

2. Kontrollér, om skade på omslutning betaling især forbindelserne. 
                                        
    >[AZURE.NOTE] **Installer ikke modulet, hvis en hvilken som helst forbindelse ben er bøjet.**

3. Med PCM håndtaget i åben stilling, glide modulet omslutning.

    ![Installation af enhed PCM](./media/storsimple-power-cooling-module-replacement/IC740975.png)

    **Figur 7** Installere PCM

4. Luk håndtaget PCM manuelt. Du skal høre et enkelt klik, som håndtaget låsen deltager. 
                                        
    >[AZURE.NOTE] For at sikre, at forbindelsen benene har ansat, kan du let tug på handle uden at slippe låsen. Hvis PCM slides ud, betyder det, at låsen blev lukket før forbindelserne ansat.

5. Forbind power-kabler til power-kilde og med PCM.

6. Sikker belastningen fritagelse baller. 

7. Slå PCM.

8. Kontrollere, at erstatningen lykkedes: i Azure klassisk portalen StorSimple Manager-tjenesten, gå til **enheder** > **vedligeholdelse** > **Hardware Status**. Status for PCM bør være grønne under **Delt komponenter**. 
                                        
    >[AZURE.NOTE] Det kan tage et par minutter til erstatning PCM fuldstændigt initialiseret.

## <a name="next-steps"></a>Næste trin

Lær mere om [StorSimple hardware-komponent erstatning](storsimple-hardware-component-replacement.md).
