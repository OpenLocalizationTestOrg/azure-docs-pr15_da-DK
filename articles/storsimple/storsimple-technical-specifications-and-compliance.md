<properties 
   pageTitle="StorSimple tekniske specifikationer | Microsoft Azure"
   description="I denne artikel beskrives de tekniske specifikationer og Standardkrav overholdelse oplysninger til StorSimple hardware-komponenter."
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

# <a name="technical-specifications-and-compliance-for-the-storsimple-device"></a>Tekniske specifikationer og overholdelse af regler for StorSimple-enhed

## <a name="overview"></a>Oversigt

Hardwarekomponenterne i enheden Microsoft Azure StorSimple overholde de tekniske specifikationer og Standardkrav, der er beskrevet i denne artikel. De tekniske specifikationer Beskriv Power og køling moduler (PCMs), diskdrev, lagerkapacitet og bilag. Oplysninger om overholdelse dækker ting som internationale standarder, sikkerhed og emission og kabler.


## <a name="power-and-cooling-module-specifications"></a>Specifikationer for Power og køling modul  

StorSimple enhed har to 100-240 v dobbelt Vifte, SBB-kompatibel Power køling moduler (PCMs). Dette giver en overflødige power konfiguration. Hvis en PCM mislykkes, enheden fortsætter med at fungere normalt på de andre PCM, indtil det fejlbehæftede modul erstattes.  

EBOD omslutning bruger en 580 W PCM og primære omslutning bruger en 764 W PCM. I følgende tabel vises de tekniske specifikationer, der er knyttet til PCMs.

| Specifikation af           | 580 W PCM (EBOD)                                    | 764 W PCM (primær)                                |
|------------------------ | --------------------------------------------------- | -------------------------------------------------- |
| Maksimale output power    | 580 W                                               | 764                                                |
| Frekvens               | 50/60 Hz                                            | 50/60 Hz                                           |
| Valg af spænding område | Automatisk lige: 90-264 V AC, 47/63 Hz               | Automatisk lige: 90-264 V AC, 47/63 Hz               |
| Maksimale inrush aktuelle  | 20 A                                                | 20 A                                               |
| Power faktor rettelse | > 95% nominel indgangsspænding                          | > 95% nominel indgangsspænding                         |
| Harmoniske               | Opfylder EN61000-3-2                                   | Opfylder EN61000-3-2                                  |
| Output                  | 5 v Standby spænding @ 2.0 A                          | 5 v Standby spænding @ 2.7 A                         |
|                         | + 5 v @ 42 A                                          | + 5 v @ 40 A                                         |
|                         | + 12 v @ 38 A                                         | + 12 v @ 38 A                                        |
| Udskiftes under drift           |  Ja                                                | Ja                                                |
| Parametre og indikatorer       | AC til/fra-parameter og fire statusindikator indikatorer     | AC til/fra-parameter og seks statusindikator indikatorer     |
| Omslutning køling       | Aksial afkølingsblæsere med variable vifte hastighed kontrolelement  | Aksial afkølingsblæsere med variable vifte hastighed kontrolelement |

 
## <a name="power-consumption-statistics"></a>Power forbrugsstatistik  

I følgende tabel vises typisk power forbrugsdata (faktiske værdier kan variere fra den publicerede) til de forskellige modeller af StorSimple enhed. 
 
 Betingelser | 240 V AC | 240 V AC | 240 V AC | 110 V AC | 110 V AC | 110 V AC 
 ---------- | -------- | -------- | -------- | -------- | -------- | -------- 
 Ventilatorer langsom, drev inaktiv | 1,45 A  |0.31 kW | 1057.76 BTU/t | 3.19 A | 0.34 kW | 1160.13 BTU/t 
 Ventilatorer langsom, drev få adgang til | 1,54 A | 0,33 kW | 1126.01 BTU/t | 3.27 A | 0.36 kW | 1228.37 BTU/t 
 Ventilatorer hurtig drev inaktiv, to PSUs drevet | 2.14 A | 0.49 kW  | 1671.95 BTU/t | 4,99 A | 0.54 kW | 1842.56 BTU/t 
 Ventilatorer hurtig drev inaktiv, én strømforsyning drevet en inaktiv | 2.05 A | 0.48 kW | 1637.83 BTU/t | 4.58 A | 0,50 kW | 1706.07 BTU/t 
 Ventilatorer hurtig drev adgang til to PSUs drevet | 2,26 A | 0.51 kW | 1740.19 BTU/t | 4,95 A | 0.54 kW | 1842.56 BTU/t 
 Ventilatorer hurtigt, drev får adgang til, én strømforsyning drevet en inaktiv | 2.14 A |0.49 kW | 1671.95 BTU/t | 4.81 A  | 0.53 kW | 1808.44 BTU/t 

## <a name="disk-drive-specifications"></a>Diskdrev specifikationer  

Enheden StorSimple understøtter op til 12 3,5 formular faktor Serial vedhæftet SCSI (SAS), diskdrev. De faktiske drev kan være en blanding af Solid State SSD'er () eller harddiske (harddiske), afhængigt af produktets konfiguration. De 12 diskdrev pladser er placeret i en konfiguration med 3 x 4 foran omslutning. EBOD omslutning giver mulighed for yderligere lagerplads til en anden 12 diskdrev. Dette er altid harddiske.  

## <a name="storage-specifications"></a>Specifikation af lager
StorSimple enheder har en blanding af harddiske og Solid State drev til både 8100 og 8600. Den samlede brugbare kapacitet til 8100 og 8600 er omkring 15 TB og 38 TB. Tabellen nedenfor dokumenter, oplysningerne om SSD, harddisk og skyen kapacitet i forbindelse med StorSimple løsning kapaciteten.

| Enhedsmodel / kapacitet                         | 8100                                                    | 8600                                                    |
|------------------------------------------------|---------------------------------------------------------|---------------------------------------------------------|
| Antallet af harddiske (harddiske)              |   8                                                     |  19                                                     |
| Antallet af Solid State SSD'er)            |   4                                                     |  5                                                      |
| Enkelt harddiskkapacitet                            |   4 TB                                                  |  4 TB                                                   |
| Enkelt SSD kapacitet                            |   400 GB                                                |  800 GB                                                 |
| Ekstra kapacitet                                 |   4 TB                                                  | 4 TB                                                    |
| Brugbar harddiskkapacitet                            | 14 TB                                                   | 36 TB                                                   |
| Brugbar SSD kapacitet                            | 800 GB                                                  | 2 TB                                                    |
| Samlede brugbare kapacitet *                         | ~ 15 TB                                                 | ~ 38 TB                                                 |
| Maksimale løsning kapacitet (herunder skyen)    | 200 TB                                                  | 500 TB                                                  |


<sup> * </sup> -  *Den samlede brugbare kapacitet omfatter kapaciteten for data, metadata og buffers.*

## <a name="enclosure-dimensions-and-weight-specifications"></a>Omslutning dimensioner og vægt specifikationer  

I følgende tabel vises de forskellige omslutning specifikationer for dimensioner og tykkelse.  

### <a name="enclosure-dimensions"></a>Omslutning dimensioner
I følgende tabel vises dimensionerne af omslutning i millimeter og tommer.

|Omslutning |Millimeter |Tommer |
|----------|------------|-------| 
| Højde |87,9 | 3.46 |
| Bredde på tværs af tilslutning flangen | 483 | 19.02 |
| Bredde på tværs af brødteksten i omslutning | 443 | 17.44 |
| Dybde fra front tilslutning flangen til ende af omslutning brødtekst | 577 | 22.72 |
| Dybde fra handlinger panel til længst ende af omslutning | 630.5 | 24.82 |
| Dybde fra tilslutning flangen til længst ende af omslutning |   603 | 23.74 |

### <a name="enclosure-weight"></a>Omslutning tykkelse  

Afhængigt af konfigurationen, en fuldt indlæst primære omslutning kan afveje fra 21 til 33 kg og kræver to personer til at håndtere den. 
 
| Omslutning | Vægt |
|-----------|--------| 
| Maksimal vægt (afhænger af konfigurationen) |30 kg – 33 kg |
| Tom (ingen drev, tilpasses) |21 – 23 kg |

## <a name="enclosure-environment-specifications"></a>Specifikation af omslutning miljø  

Dette afsnit beskrives de specifikationer, der er relateret til omslutning-miljø. Temperatur, fugtighed, højde, stød, vibrationer, papirretning, sikkerhed og elektromagnetisk kompatibilitet (EMC) er inkluderet i denne kategori.  

### <a name="temperature-and-humidity"></a>Temperatur og luftfugtighed

| Omslutning        | Omgivende temperatur område  | Omgivende relativ luftfugtighed | Maksimale våd pære   |
|------------------|----------------------------|---------------------------|--------------------|
| Drift      | 5° C - 35° C (41° F - 95° F)    | 20% og 80% ikke-kondenserende- | 28° C (82° F)        |
| Ikke-drift  | -40° C - 70° C (40° F - 158° F) | 5-100% ikke-kondensering  | 29° C (84° F)        |

### <a name="airflow-altitude-shock-vibration-orientation-safety-and-emc"></a>Ventilation, højde, stød, vibrationer, sideretning, sikkerhed og EMC
 
| Omslutning          | Funktionsdygtige specifikationer                                                |
|--------------------|---------------------------------------------------------------------------| 
| Ventilation            | System ventilation er foran til bag. System skal være drevet med en low-pressure, bag udstødningsgas installation. Tilbage tryk, der er oprettet af rack døre og hindringer må ikke overstige 5 Pa (0,5 mm vand måler). |
| Højden og drift  | -30 meter til 3045 meter (-100 fod til 10.000 fod) med maksimale fungerer temperaturen deaktivere klassificeret af 5 ° C over 7000 fod. |
| Højde, ikke-drift  | -305 målere til 12.192 meter (-1,000 fod til 40.000 fod) |
| Stød og drift  | 5g 10 ms ½ sinus |
| Stød og ikke-drift  | 30g 10 ms ½ sinus |
| Vibrationer, drift  | 0.21g RMS 5-500 Hz tilfældigt |
| Vibrationer, ikke-drift  | 1.04g RMS 2-200 Hz tilfældigt |
| Vibrationer, flytning  | 3g 2-200 Hz sinus |
| Retning og tilslutning  | 19" rack tilslutning (2 EIA enheder) |
| Rackskinner  | Så det passer til mindst 700 mm (31.50 tommer) dybde rack overholder IEC 297 |
| Sikkerhed og godkendelser  |   CE og UL da 61000-3, IEC 61000-3, UL 61000-3 |
| EMC  | EN55022 (CISPR - A), FCC A |

## <a name="international-standards-compliance"></a>Overholdelse af internationale standarder
Enheden Microsoft Azure StorSimple i overensstemmelse med de følgende internationale standarder:  

- CE - DA 60950-1  
- CB-rapport til IEC 60950-1  
- UL og cUL til UL 60950-1  

## <a name="safety-compliance"></a>Overholdelse af sikkerhed  

Din Microsoft Azure StorSimple enhed opfylder følgende safety-bedømmelser:  

- System produkt typegodkendelse: UL, cUL, CE  
- Overholdelse af sikkerhed: UL 60950, IEC 60950, da 60950  

## <a name="emc-compliance"></a>EMC overholdelse af angivne standarder 

Din Microsoft Azure StorSimple enhed opfylder følgende EMC-bedømmelser.  

### <a name="emissions"></a>Emission

Enheden er EMC-kompatibel for gennemført og elektromagnetisk emission niveauer.  

- Gennemført emission begrænse niveauer: CFR 47 afsnit 15 b klasse A EN55022 klasse A CISPR klasse A  
- Begrænse emission niveauer: CFR 47 afsnit 15 b klasse A EN55022 klasse A CISPR klasse A   

### <a name="harmonics-and-flicker"></a>Harmoniske og flickr  

Enheden er i overensstemmelse med EN61000-3-2/3.  

### <a name="immunity-limit-levels"></a>Kontrol grænse niveauer  
Enheden er i overensstemmelse med EN55024.  

## <a name="ac-power-cord-compliance"></a>AC power-kabel, overholdelse af angivne standarder
  
Plug- og samlingen fuldført power-kabel, der skal opfylde de standarder, der er relevante for det land, hvor enheden, der anvendes, og de skal have safety godkendelser, der kan accepteres i det pågældende land. De følgende tabeller viser standarder for USA og Europa.  

### <a name="ac-power-cords---usa-must-be-nrtl-listed"></a>AC el-kabler - USA (skal være angivet NRTL)

| Komponent       | Specifikation af                                                     |
| --------------- | ----------------------------------------------------------------- | 
| Netledning type       | SV eller SVT, 18 AWG minimum, 3 ledere maksimale 2.0 målere-længde |
| Plug            | NEMA 5-15P kendskab af typen vedhæftet fil plug bedømt 120 V, 10 A; eller IEC 320 C14, 250 V, 10 A |
| Socket          | IEC 320 C-13, 250 V, 10 A                                         |

### <a name="ac-power-cords---europe"></a>AC el-kabler - Europe

| Komponent       | Specifikation af                                                     |
| --------------- | ----------------------------------------------------------------- | 
| Netledning type       | Harmoniseret, H05-VVF-3G1.0                                         |
| Socket          | IEC 320 C-13, 250 V, 10 A                                         |

## <a name="supported-network-cables"></a>Understøttede netværkskablerne  

Se på [listen over understøttede netværkskablerne og moduler](storsimple-supported-hardware-for-10-gbe-network-interfaces.md)de 10 GbE netværksgrænseflader DATA 2 og 3, skal du DATA.

## <a name="next-steps"></a>Næste trin

Du er nu klar til at installere en StorSimple enhed i dit datacenter. Se [implementerer enheden lokalt](storsimple-deployment-walkthrough-u2.md)kan finde flere oplysninger.  
