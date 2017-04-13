<properties 
   pageTitle="Udskift batteriet på en enhed med StorSimple | Microsoft Azure"
   description="I denne artikel beskrives, hvordan du kan fjerne, erstatte og vedligehold ekstra batterimodul på enheden StorSimple."
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

# <a name="replace-the-backup-battery-module-on-your-storsimple-device"></a>Erstat ekstra batterimodul på enheden StorSimple

## <a name="overview"></a>Oversigt

Den primære omslutning Power og køling modul (PCM) på enheden Microsoft Azure StorSimple har en ekstra batteripakke. Denne pack omfatter power, så StorSimple-enheden kan gemme data, hvis der er tab af AC strøm til den primære omslutning. Denne batteripakke kaldes *Sikkerhedskopiering batterimodul*. Ekstra batterimodul findes kun for den primære omslutning i enheden StorSimple (EBOD omslutning ikke indeholder en sikkerhedskopi batterimodul). 

Dette selvstudiet beskrives, hvordan du:

- Fjerne ekstra batterimodul 
- Installere en ny sikkerhedskopiering batterimodul
- Bevare ekstra batterimodul

>[AZURE.IMPORTANT] Gennemgå oplysningerne om sikkerhed i [Introduktion til StorSimple hardware-komponent erstatning](storsimple-hardware-component-replacement.md)før fjerne og erstatte en ekstra batterimodul.

## <a name="remove-the-backup-battery-module"></a>Fjerne ekstra batterimodul

Ekstra batterimodul for enheden StorSimple er en felt pære enhed. Før den er installeret i PCM, skal batterimodul gemmes i dens oprindelige emballagen. Udfør følgende trin for at fjerne ekstra batteriet.

#### <a name="to-remove-the-backup-battery-module"></a>Fjerne ekstra batterimodul

1. Gå til **enheder**på Azure klassisk portalen > **vedligeholdelse** > **Hardware Status**. Se status for batteriet under **Delt komponenter**.

2. Identificere PCM batteriet mislykkes. Figur 1 viser bagsiden af StorSimple enhed.

    ![Backplane af enhed primære omslutning moduler](./media/storsimple-battery-replacement/IC740994.png)

    **Figur 1** Bagsiden af primær enhed, der viser PCM og controller moduler

  	|Etiket|Beskrivelse|
  	|:----|:----------|
  	|1|PCM 0|
  	|2|PCM 1|
  	|3|Controller 0|
  	|4|Controller 1|

    Som vist med nummer 3 i figur 2, skal være oplyst indikatoren overvågning LED på PCM 0, der svarer til **Batteri fejl** .

    ![Backplane af enhed PCM overvågning indikator indikatorer](./media/storsimple-battery-replacement/IC740992.png)

    **Figur 2** Bagsiden af PCM, der viser indikatoren overvågning indikatorer

  	|Etiket|Beskrivelse|
  	|:---|:-----------|
  	|1|AC strømsvigt|
  	|2|Ventilatorfejl|
  	|3|Batteri fejl|
  	|4|PCM OK|
  	|5|DC strømsvigt|
  	|6|Batteri sund|

3. Hvis du vil fjerne PCM med et mislykkedes batteri, skal du følge trinnene i [fjerne en PCM](storsimple-power-cooling-module-replacement.md#remove-a-pcm).

4. Løft med PCM, der er fjernet, rotere batteri modul håndtaget opad, som angivet i følgende figur, og trække den op til Fjern batteriet.

    ![Fjerne batteri fra PCM](./media/storsimple-battery-replacement/IC741019.png)

    **Figur 3** Fjernelse af batteriet fra PCM

5. Anbring modulet i feltet pære måleenheden emballagen.

6. Returnere den defekte enhed til Microsoft for stort vedligeholdelse og håndtering af.

## <a name="install-a-new-backup-battery-module"></a>Installere en ny sikkerhedskopiering batterimodul

Udfør følgende trin for at installere batterimodul erstatning i PCM i den primære omslutning af enheden StorSimple.

#### <a name="to-install-the-battery-module"></a>Installere batteri-modulet

1. Placer ekstra batterimodul i den rigtige retning i PCM.

2. Tryk på ned batteri modul håndtaget helt til sæde forbindelsen.

3. Erstat PCM i den primære omslutning ved at følge vejledningen i [erstatte en Power og køling modul på enheden StorSimple](storsimple-power-cooling-module-replacement.md).

4. Når erstatning er fuldført, skal du gå til **enheder** > **vedligeholdelse** > **Hardware Status** på portalen Azure klassisk. Kontrollér status for batteriet at sikre dig, at installationen er fuldført. En grøn status angiver, at batteriet er i orden.

## <a name="maintain-the-backup-battery-module"></a>Bevare ekstra batterimodul

Enheden StorSimple har ekstra batterimodul power til domænecontrolleren under power tab af hændelser. Det kan StorSimple kameraet, for at gemme vigtige data, før du lukker ned på en kontrolleret måde. Med to fuldt opkrævet batterier i PCMs, kan systemet håndtere to efterfølgende tab begivenheder.

I Azure klassisk portalen angiver **Hardware Status** på siden **vedligeholdelse** om batteriet fungerer forkert eller slutningen af liv-nærmer. Batteristatus er angivet med **batteri i PCM 0** eller **batteri i PCM 1** under **Delt komponenter**. Denne side kan du se tilstanden **Degraded (forringet)** til slutningen af livscyklussen nærmer, og **MISLYKKET** til slutningen af livscyklussen nået. 

>[AZURE.NOTE] Batteriet kan rapportere **MISLYKKET** , når det blot skal betale.
 
Hvis tilstanden **Degraded (forringet)** vises, anbefaler vi den følgende fremgangsmåde:

- Systemet kan have fandt en seneste strømsvigt eller batterierne kan under periodiske reparation. Overholde systemet for 12 timer, inden du fortsætter.

    - Hvis tilstanden er stadig **Degraded (forringet)** efter 12 timers fortløbende forbindelse til AC power med enheder og PCMs, der kører, skal batteriet skal erstattes. Kan du [kontakte Microsoft Support](storsimple-contact-microsoft-support.md) for en ekstra batterimodul erstatning.

    - Hvis der bliver tilstanden OK efter 12 timer, batteriet er brugbar, og det er kun nødvendigt et vedligeholdelse gebyr.

- Hvis der er ikke en tilknyttet tab af strømmen og PCM er tændt og tilsluttet AC power, skal batteriet erstattes. [Kontakt Microsoft Support](storsimple-contact-microsoft-support.md) for at bestille et ekstra batterimodul erstatning.

>[AZURE.IMPORTANT] Kassér mislykkedes batteriet ifølge nationale og regionale regler. 

## <a name="next-steps"></a>Næste trin

Lær mere om [StorSimple hardware-komponent erstatning](storsimple-hardware-component-replacement.md).
