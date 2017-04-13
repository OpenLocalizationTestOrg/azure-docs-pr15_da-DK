<properties 
    pageTitle="StorSimple overvågning indikatorer | Microsoft Azure" 
    description="Beskriver de light – emitting dioder (indikatorer) og lyden alarmer, der bruges til at overvåge statussen for StorSimple enheden."
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
    ms.date="08/18/2016"
    ms.author="alkohli" />

# <a name="use-storsimple-monitoring-indicators-to-manage-your-device"></a>Brug StorSimple overvågning KPI'er til at administrere din enhed   

## <a name="overview"></a>Oversigt

Enheden StorSimple indeholder light – emitting dioder (indikatorer) og alarmer, som du kan bruge til at overvåge og overordnede status for enheden StorSimple i moduler. Overvågning symbolerne kan findes på hardwarekomponenterne i enhedens primære omslutning og EBOD omslutning. Overvågning symbolerne kan være enten indikatorer eller lyden alarmer.

Der er tre LED tilstande bruges til at angive status for et modul: grøn, blinkende grøn til rød gule eller rød gul.  

- Grøn indikatorer repræsenterer en sund fungerer status.  
- Blinker grøn til rød gul indikatorer repræsentere tilstedeværelsen af ikke-kritisk betingelser, kan det være nødvendigt at foretage brugerinput.  
- Rød gul indikatorer angiver, at der er en kritiske fejl, der findes i modulet.  

I resten af denne artikel beskrives de forskellige overvågning indikator-indikatorer, deres placering på StorSimple enheden, at Enhedsstatus er baseret på LED-tilstand, og alle tilknyttede lyden alarmer.

## <a name="front-panel-indicator-leds"></a>Indikator for Front panel indikatorer

Panelet forside, også kaldet *Handlinger panelet* eller *ops panel*, viser Sammenlæg status for alle moduler i systemet. Panelet forside er identiske på den primære StorSimple og EBOD omslutning og illustreres herunder.  

   ![Enhed front panel][1]
 
Panelet forside indeholder følgende indikatorer:  

1. Slå knappen
2. Indikatoren Power LED (grøn/rød-gul)
3. Indikator for et modul FØRT (på Rød-gul/fra)
4. Indikator for logiske fejl FØRT (på Rød-gul/fra
5. Enhed id-skærm  

Den største forskel mellem panelet forside indikatorer for enheden og rettighederne for EBOD omslutning er **System enhed id-nummer** vises på LED-displayet. Standard enhed ID vises på enheden er **00**, mens vises på EBOD omslutning standard-enhed-ID er **01**. Dette kan du hurtigt skelne mellem enheden og EBOD omslutning, når enheden er slået til. Hvis enheden er slået fra, kan du bruge oplysningerne i [aktivere en ny enhed](storsimple-turn-device-on-or-off.md#turn-on-a-new-device) til at skelne enheden fra EBOD omslutning.  

## <a name="front-panel-led-status"></a>Front panel LED status  

Brug tabellen nedenfor til at identificere status angivet med LED-indikatorerne på panelet forside for enheden eller EBOD omslutning.  

|Tænd | Et modul | Logiske fejl | Alarm | Status|
|-------------|---------------|-----------------|-------|-------|
|Rød gul | FRA     | FRA | I/T. | AC power tabt, operativsystem på sikkerhedskopien power eller AC power på og domænecontrolleren moduler er blevet fjernet.|
|Grøn | PÅ | PÅ | I/T. | OPS panel tænd (5s) teste tilstand|
|Grøn | FRA | FRA | I/T. | Power på alle de funktioner, der er god|
|Grøn | PÅ |I/T. | PCM fejl indikatorer, vifte fejl indikatorer | PCM fejl, vifte fejl, over eller under temperaturen|
| Grøn | PÅ | I/T. | I/o-modul indikatorer  | Controller modul fejl|
| Grøn | PÅ | I/T. | I/T. | Omslutning logik fejl|
| Grøn | Flash | I/T. | Modul statusindikator på controller modul. PCM fejl indikatorer, vifte fejl indikatorer | Ukendt controller Modultype installeret, I2C bus mislykket controller modul vigtig produkt data (VPD) konfigurationsfejl |

## <a name="power-cooling-module-pcm-indicator-leds"></a>Power køling modul (PCM) indikator indikatorer   

Power køling modul (PCM) indikator indikatorer kan findes på bagsiden af den primære omslutning eller EBOD omslutning på hvert PCM modul. Dette emne beskrives, hvordan du kan bruge følgende indikatorer til at overvåge statussen for enheden StorSimple.  

- Den primære omslutning PCM indikatorer
- EBOD omslutning PCM indikatorer

## <a name="pcm-leds-for-the-primary-enclosure"></a>Den primære omslutning PCM indikatorer  

StorSimple enhed har et 764W PCM modul med et ekstra batteri. Følgende illustration viser panelet LED til enheden.  

   ![PCM indikatorer på den primære omslutning][2]

LED forklaring:

1. AC strømsvigt
2. Ventilatorfejl
3. Batteri fejl
4. PCM OK
5. DC fejl
6. Batteri god  

Status for PCM er angivet under panelet LED. Panelet enhed PCM LED har seks indikatorer. Fire ud af disse indikatorer viser status for power levering og vifte. De resterende to indikatorer angive status for ekstra batterimodul i PCM. Du kan bruge de følgende tabeller til at bestemme status for PCM.  

### <a name="pcm-indicator-leds-for-power-supply-and-fan"></a>PCM indikator indikatorer for power levering og vifte
| Status | PCM OK (grøn) | AC fejl (gule) | Vifte fejl (gule) | DC fejl (gule) |
|--------|----------------|-----------------------|------------------|----------------------|
| Ingen AC strøm (til omslutning) | FRA | FRA | FRA | FRA|
| Ingen AC strøm (kun denne PCM) | FRA | PÅ | FRA | PÅ |
| AC præsentere PCM til - OK     | PÅ | FRA | FRA | FRA |
| PCM fejl (vifte fejl) | FRA | FRA | PÅ | I/T. |
| PCM fejl (oven over amp over spænding over aktuelle) | FRA | PÅ | PÅ | PÅ |
| PCM (vifte af tolerance) | PÅ | FRA | FRA | PÅ |
| Gemmer en film | Blinker | FRA | FRA | FRA |
| PCM firmware download | FRA | Blinker | Blinker | Blinker |

### <a name="pcm-indicator-leds-for-the-backup-battery"></a>PCM indikator indikatorer for sikkerhedskopiering batteriet  

| Status | Batteri god (grøn) | Batteri fejl (gul) |
|--------|----------------------|-----------------------|
| Batteri ikke til stede | FRA | FRA |
| Batteri Præsenter og opladning | PÅ | FRA |
| Batteri opladning eller vedligeholdelse afslutning | Blinker | FRA |
| Batteri "bløde" fejl (genoprettelig) | FRA | Blinker |
| Batteri "hårde" fejl (ikke-genoprettelige) | FRA | PÅ |
| Batteri disarmed | Blinker | FRA |

## <a name="pcm-leds-for-the-ebod-enclosure"></a>EBOD omslutning PCM indikatorer  

EBOD omslutning har en 580W ved PCM og ingen ekstra batteri. Panelet PCM for EBOD omslutning har indikator indikatorer kun for power-forsyninger og vifte. Følgende illustration viser disse indikatorer.

   ![PCM indikatorer på EBOD omslutning][3] 
 
Du kan bruge tabellen nedenfor til at bestemme status for PCM.  

| Status | PCM OK (grøn) | AC fejl (gule) | Vifte fejl (gule) | DC fejl (gule) |
|--------|---------------|------------------------|------------------|----------------------|
| Ingen AC strøm (til omslutning) | FRA | FRA | FRA | FRA |
| Ingen AC power (kun denne PCM) | FRA | PÅ | FRA | PÅ |
| AC præsentere PCM på – OK | PÅ | FRA | FRA | FRA |
| PCM fejl (vifte fejl) | FRA | FRA | PÅ | X |
| PCM fejl (oven over amp over spænding over aktuelle | FRA | PÅ | PÅ | PÅ |
| PCM (vifte af tolerance) | PÅ | FRA | FRA | PÅ |
| Standby model | Blinker | FRA | FRA | FRA |
| PCM firmware download | FRA | Blinker | Blinker | Blinker |

## <a name="controller-module-indicator-leds"></a>Indikator for controller modul indikatorer  

StorSimple enheden indeholder indikatorer for primary controller og EBOD controller moduler.   

### <a name="monitoring-leds-for-the-primary-controller"></a>Overvågning indikatorer for primary controller
Følgende illustration hjælper dig med at identificere LED-indikatorerne på den primære controller. (Alle komponenter er angivet for støtte inden for retning).  

   ![Overvågning indikatorer - primære controller][4]
 
Brug tabellen nedenfor til at finde ud af, om modulet controller fungerer korrekt.  

### <a name="controller-indicator-leds"></a>Indikator for controller indikatorer  

| INDIKATOR | Beskrivelse                                                                            
|---- | ----------- |
| Id-skærm (blå) | Angiver, at der modulet identificeres. Hvis den blå Indikator blinker på en igangværende controller, controlleren er den aktive controller, og det andet er den standby controller. Yderligere oplysninger finder du se [identificere den aktive controller på din enhed](storsimple-controller-replacement.md#identify-the-active-controller-on-your-device). |
| Fejl LED (gul) | Angiver en fejl i controlleren.        
| OK-Indikator (grøn) | Stabil grøn angiver, at controlleren OK. Blinker grøn angiver en controller VPD konfigurationsfejl. |
| SAS aktivitet indikatorer (grøn) | Stabil grøn angiver en forbindelse med nogen aktuelle aktivitet. Blinker grøn angiver forbindelsen har igangværende aktivitet. |
| Ethernet statusindikatorer | Højre angiver aktivitet på link/netværket: (stabil grøn) link aktive (blinker grøn) netværk aktivitet. Venstre side angiver netværkshastighed: (gul) 1000 Mb/s (grøn) til 100 Mb s og (fra) 10 Mb/s. Afhængigt af komponent modellen, kan denne light blinke selvom netværksgrænsefladen ikke er aktiveret. |
| INDLÆG indikatorer | Angiver start status, når controlleren er slået til. Hvis StorSimple enheden ikke kan starte, hjælper denne LED Microsoft Support identificere punktet, hvor fejlen opstod Start processens. |

>[AZURE.IMPORTANT] 
Hvis fejlen LED lyser, er der et problem med modulet controller, som kan løses ved at genstarte controlleren. Kontakt Microsoft Support, hvis at genstarte controlleren ikke løser problemet.  


### <a name="monitoring-leds-for-the-ebod-ebod-enclosure"></a>Overvågning indikatorer for EBOD (EBOD omslutning)  

Hver af de 6 Gb/s SAS EBOD enheder har indikatorer, som angiver dens status, som vist i nedenstående illustration.  

  ![Overvågning indikatorer - EBOD omslutning][5]

Brug tabellen nedenfor til at finde ud af, om modulet EBOD controller fungerer normalt.  

### <a name="ebod-controller-module-indicator-leds"></a>Indikator for EBOD controller modul indikatorer  

|Status | I/o-modul OK (grøn) | I/o-modul fejl (gul) | Host port aktivitet (grøn) |
|-------|----------------------|-------------------------------|----------------------------|
| Controller modul OK | PÅ | FRA | - |
| Controller modul fejl | FRA | PÅ | - |
| Ingen eksterne host port forbindelse | - | - | FRA |
| Eksterne host port forbindelse – uden aktivitet | - | - | PÅ |
| Eksterne host port tilslutning – aktivitet | - | - | Blinker |
| Controller modul metadata fejl | Blinker | - | - |

## <a name="disk-drive-indicator-leds-for-the-primary-enclosure-and-ebod-enclosure"></a>Diskdrev indikator indikatorer for de primære omslutning og EBOD omslutning

StorSimple enhed har diskdrev, der er placeret i både den primære omslutning og EBOD omslutning. Hvert diskdrev indeholder overvågning indikator indikatorer, som beskrevet i dette afsnit. 

For diskdrev, drev status er angivet med en grøn Indikator og en rød gul LED tilsluttet på forsiden af hvert drev carrier modul. Følgende illustration viser disse indikatorer.

  ![Diskdrev indikatorer][6]
 
Bruge tabellen nedenfor til at bestemme tilstanden for hver harddisk og dermed også påvirker panelet overordnede forside LED status.  

### <a name="disk-drive-indicator-leds-for-the-ebod-enclosure"></a>Diskdrev indikator indikatorer for EBOD omslutning  

| Status | Aktivitet OK LED (grøn) | Fejl LED (rød-gul) | Der er knyttet ops panel LED |
|-------|--------------------------|----------------------|-------------------------|
| Ingen drev, der er installeret | FRA | FRA | Ingen |
| Drev installeret og køre | Blinker/deaktivering med aktivitet | X | Ingen |
| SCSI omslutning (SES) enhed identitet sæt | PÅ | Blinker 1 sekund på/1 andet fra | Ingen |
| SES enhed fejl bit sæt | PÅ | PÅ | Logiske fejl (rød) |
| Kredsløb strømafbrydelse kontrolelement | FRA | PÅ | Modul fejl (rød) |

## <a name="audible-alarms"></a>Lyden alarmer  

En enhed med StorSimple indeholder lyden alarmer, der er knyttet til både den primære omslutning og EBOD omslutning. En lyden alarm er placeret i panelet forside (også kendt som panelet ops) i begge bilag. Lyden alarmen angiver, når en fejl betingelse findes. Følgende betingelser skal aktivere alarmen:  

- Vifte fejl eller fejl
- Spænding uden for området
- Over eller under temperaturen betingelse
- Termisk overløb
- Systemfejl
- Logiske fejl
- Power levering fejl
- Fjernelse af en potens, køling modul (PCM)  

Den følgende tabel beskriver de forskellige tilstande alarm.  

### <a name="alarm-states"></a>Alarm tilstande  

| Alarm tilstand | Handling | Handling med slå knappen trykkes |
|------------|---------|---------------------------------|
| S0 | Normal tilstand: uovervåget | Bip to gange |
| S1 | Fejl tilstand: 1 sekund på/1 andet fra | Overgang til S2 eller S3 (se noter) |
| S2 | Minde tilstand: forbigående bip | Ingen |
| S3 | Slået fra tilstand: uovervåget | Ingen |
| S4 | Kritiske fejl tilstand: kontinuert alarm | Ikke tilgængelig: slå lyd fra ikke aktiv |

> [AZURE.NOTE] 

>  - I alarm tilstand S1, hvis du ikke trykker på slå lyd fra i 2 minutter overgange tilstanden automatisk S2 eller S3.  
>  - Alarm stater S1 til S4 vende tilbage til S0, når betingelsen fejl ikke er markeret.  
>  - Kritiske fejl tilstand S4 kan angives fra et andet land.  

Du kan slå lyden alarmen ved at trykke på knappen Slå fra i panelet ops. Automatisk nummertasterne sker efter to minutter, hvis indstillingen Slå ikke styres manuelt. Når alarmen er slået fra, vil den stadig lyd med korte forbigående bip til at angive, at der stadig findes et problem. Alarmen bliver uovervåget, når alle problemerne, der ikke er markeret.  

Den følgende tabel beskrives de forskellige alarm betingelser.  

### <a name="alarm-conditions"></a>Alarm betingelser  

| Status | Alvor | Alarm | OPS panelet LED |
|--------|---------|--------|----------------|
| PCM besked – DC strømsvigt fra en enkelt PCM | Fejl – uden tab af redundans | S1 | Et modul|
|PCM besked – DC strømsvigt fra en enkelt PCM | Fejl – tab af redundans | S1 | Et modul |
| PCM vifte fejl | Fejl – tab af redundans | S1 | Et modul |
| SBB modul, der er fundet PCM fejl | Fejl | S1 | Et modul |
| PCM fjernet | Konfigurationsfejl | Ingen | Et modul |
| Omslutning konfigurationsfejl | Fejl – kritiske | S1 | Et modul |
| Lav advarsel temperaturen besked | Advarsel | S1 | Et modul |
| Høj advarsel temperaturen besked | Advarsel | S1 | Et modul |
| Over temperaturen alarm | Fejl – kritiske | S1 | Et modul |
| I2C bus fejl | Fejl – tab af redundans | S1 | Et modul |
| OPS panelet kommunikationsfejl (I2C) | Fejl – kritiske     | S1 | Et modul |
| Controller fejl | Fejl – kritiske | S1 | Et modul |
| SBB interface modul fejl | Fejl – kritiske | S1 | Et modul |
| SBB interface modul fejl – ingen fungerende moduler resterende | Fejl – kritiske | S4 | Et modul |
| Modul med brugergrænseflade til SBB fjernet | Advarsel | Ingen | Et modul |
| Drev power kontrolelement fejl | Advarsel – uden tab af drev power | S1 | Et modul |
| Drev power kontrolelement fejl | Fejl – kritiske; tab af drev power | S1 | Et modul |
| Drev, der er fjernet | Advarsel | Ingen | Et modul |
| Utilstrækkelig power tilgængelige | Advarsel | ingen | Et modul |

## <a name="next-steps"></a>Næste trin

Lær mere om [StorSimple hardwarekomponenter og status](storsimple-monitor-hardware-status.md).

[1]: ./media/storsimple-monitoring-indicators/storsimple-monitoring-indicators-IMAGE01.png
[2]: ./media/storsimple-monitoring-indicators/storsimple-monitoring-indicators-IMAGE02.png
[3]: ./media/storsimple-monitoring-indicators/storsimple-monitoring-indicators-IMAGE03.png
[4]: ./media/storsimple-monitoring-indicators/storsimple-monitoring-indicators-IMAGE04.png
[5]: ./media/storsimple-monitoring-indicators/storsimple-monitoring-indicators-IMAGE05.png
[6]: ./media/storsimple-monitoring-indicators/storsimple-monitoring-indicators-IMAGE06.png

 
