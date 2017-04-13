<properties 
   pageTitle="Ændre tilstanden StorSimple enhed | Microsoft Azure"
   description="I denne artikel beskrives StorSimple enhed tilstandene og forklarer, hvordan du bruger Windows PowerShell til StorSimple til at ændre enhedstilstanden."
   services="storsimple"
   documentationCenter=""
   authors="alkohli"
   manager="carmonm"
   editor="" />
<tags 
   ms.service="storsimple"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="06/17/2016"
   ms.author="alkohli" />

# <a name="change-the-device-mode-on-your-storsimple-device"></a>Ændre enhedstilstanden på enheden StorSimple

Denne artikel indeholder en kort beskrivelse af de forskellige tilstande, hvori enheden StorSimple kan fungere. Enheden StorSimple kan fungere i tre tilstande: normal, vedligeholdelse og gendannelse. 

Når du har læst i denne artikel, vil du vide:

- Hvilke StorSimple enhed tilstandene for er
- Hvordan du kan finde ud af, hvilken tilstand StorSimple enheden er i
- Sådan ændres fra normal vedligeholdelsestilstand og *omvendt*


Ovenstående administrationsopgaver kan kun udføres via grænsefladen Windows PowerShell for enheden StorSimple.

## <a name="about-storsimple-device-modes"></a>Om StorSimple enhed tilstande

Enheden StorSimple kan fungere i normal, vedligeholdelse eller gendannelse tilstand. Hver af følgende tilstande er som beskrevet nedenfor.

### <a name="normal-mode"></a>Normal tilstand

Dette er defineret som normal funktionel tilstand til en fuldt konfigureret StorSimple enhed. Som standard skal din enhed være i normal tilstand.

### <a name="maintenance-mode"></a>Vedligeholdelsestilstand

Nogle gange kan StorSimple enheden skal placeres i vedligeholdelsestilstand. Denne tilstand kan du udføre vedligeholdelse på enheden og installere forstyrrende opdateringer, som dem, der er relateret til disk firmware.

Du kan placere systemet til vedligeholdelsestilstand kun via Windows PowerShell til StorSimple. Alle i/o-anmodninger er standset i denne tilstand. Tjenester som permanente-RAM (NVRAM) eller den klyngetjeneste er stoppet. Begge enheder er genstartet, når du indtaster eller afslutte denne tilstand. Når du afslutter tilstanden vedligeholdelse, fortsætter alle tjenesterne, og du skal være sund. Dette kan tage et par minutter.

>[AZURE.NOTE]**Vedligeholdelsestilstand understøttes kun på en enhed med fungerer korrekt. Det understøttes ikke på en enhed, hvor den ene eller begge over enheder, der ikke virker.**
</br>

### <a name="recovery-mode"></a>Tilstanden for nedbrud

Tilstanden for nedbrud kan beskrives som "Windows fejlsikret tilstand med netværkssupport". Tilstanden for nedbrud deltager Microsoft Support-teamet, og gør det muligt at udføre diagnosticering på systemet. Det primære mål med tilstanden nedbrud er at hente systemets logfiler.

Hvis dit system er i tilstanden nedbrud, skal du kontakte Microsoft Support til næste trin. Gå til [Kontakt Microsoft Support](storsimple-contact-microsoft-support.md)kan finde flere oplysninger.

>[AZURE.NOTE] **Du kan ikke markere enheden i tilstanden nedbrud. Hvis enheden er i en ugyldig tilstand, forsøger tilstanden nedbrud at få enheden i en Microsoft Support personale, kan du undersøge det.**

## <a name="determine-storsimple-device-mode"></a>Bestemme StorSimple enhed tilstand

#### <a name="to-determine-the-current-device-mode"></a>Til at bestemme den aktuelle enhed tilstand

1. Log på konsollen enhed seriel ved at følge trinnene i [Brug trykfarver til at oprette forbindelse til enhed seriel konsollen](storsimple-deployment-walkthrough.md#use-putty-to-connect-to-the-device-serial-console).

2. Se den banner meddelelse i menuen Seriel konsol på enheden. Denne meddelelse angiver eksplicit, om enheden er i tilstanden vedligeholdelse eller gendannelse. Hvis meddelelsen ikke indeholder nogen bestemte oplysninger, der vedrører tilstanden system, er enheden i normal tilstand.

## <a name="change-the-storsimple-device-mode"></a>Ændre tilstanden StorSimple enhed 

Du kan placere StorSimple enheden til Vedligeholdelsestilstand (fra normal tilstand) for at udføre vedligeholdelse eller installere vedligeholdelse tilstand opdateringer. Udfør følgende procedurer for at angive eller afslutte vedligeholdelsestilstand.

> [AZURE.IMPORTANT] Kontroller, at begge enhed enheder er sund ved at åbne **Hardware Status** på siden **vedligeholdelse** i portalen Azure klassisk før redigeringstilstand vedligeholdelse. Hvis en eller begge enheder ikke sund, skal du kontakte Microsoft Support til de næste trin. Gå til [Kontakt Microsoft Support](storsimple-contact-microsoft-support.md)kan finde flere oplysninger.

#### <a name="to-enter-maintenance-mode"></a>Angive Vedligeholdelsestilstand

1. Log på enhed seriel konsollen ved at følge trinnene i [Brug trykfarver til at oprette forbindelse til enhed seriel konsollen](storsimple-deployment-walkthrough.md#use-putty-to-connect-to-the-device-serial-console).

2. Vælg indstilling 1, **Log på med fuld adgang**i menuen Seriel konsol. Når du bliver bedt om det, du give **enhed administratoradgangskode**. Standardadgangskoden, er: `Password1`.

3. Ved kommandoprompten skal du indtaste 

    `Enter-HcsMaintenanceMode`

4. Du får vist en advarselsmeddelelse, der fortæller, at Vedligeholdelsestilstand kan forstyrre alle i/o-anmodninger og bryde forbindelsen til Azure klassisk portalen, og du bliver bedt om at bekræfte. Skriv **Y** for at skifte til vedligeholdelsestilstand.

5. Begge enheder genstartes. Når genstart er fuldført, vises en anden meddelelse, der angiver, at enheden er i vedligeholdelsestilstand.


#### <a name="to-exit-maintenance-mode"></a>Tilstanden med vedligeholdelse

1. Log på enheden seriel konsollen. Kontrollér fra banner meddelelsen, som din enhed i vedligeholdelsestilstand.

2. Ved kommandoprompten skal du indtaste:

    `Exit-HcsMaintenanceMode`

3. En advarselsmeddelelse og en bekræftelsesmeddelelse vises. Skriv **Y** vedligeholdelse fra tilstanden.

4. Begge enheder genstartes. Når genstart er fuldført, vises en anden meddelelse, der angiver, at enheden er i normal tilstand.


## <a name="next-steps"></a>Næste trin

Lær, hvordan du kan [anvende normal og vedligeholdelse tilstand opdateringer](storsimple-update-device.md) på enheden StorSimple.

