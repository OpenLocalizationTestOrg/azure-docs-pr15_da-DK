<properties 
   pageTitle="Hardware til StorSimple 10 GbE grænseflader | Microsoft Azure"
   description="Beskriver understøttede small form-faktor tilslutningsbar (SFP) skal, kabler og parametre for 10 GbE netværksgrænseflader på enheden StorSimple."
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
   ms.date="09/21/2016"
   ms.author="alkohli" />

# <a name="supported-hardware-for-the-10-gbe-network-interfaces-on-your-storsimple-device"></a>Understøttede hardware for 10 GbE netværksgrænseflader på enheden StorSimple

## <a name="overview"></a>Oversigt

I denne artikel indeholder oplysninger om supplerende hardware, der fungerer med din Microsoft Azure StorSimple enhed.

## <a name="list-of-devices-tested-by-microsoft"></a>Listen over enheder, der er testet af Microsoft

Microsoft har testet følgende small form-faktor tilslutningsbar (SFP) du skal, kabler og parametre til at sikre, at de fungere optimalt med enheder. (De følgende tabeller, opdateres, som er testet ny hardware.)

### <a name="sfp-transceivers"></a>Du skal SFP +

|Gøre|Model|
|---|---|
|Cisco|SFP-10G-SR|

### <a name="cables"></a>Kabler

|S. Nej. |Gøre|Model|
|---|---|---|
| 1.|Cisco|SFP-H10GB-CU1M|
| 2.|Cisco|SFP-H10GB-CU2M|
| 3.|Cisco|SFP-H10GB-CU3M|
| 4.|Tripp-Lite|N820 - 05M (OM3 =)|

### <a name="switches"></a>Parametre

|S. Nej.|Gøre|Model|
|---|---|---|
| 1. |Cisco|N3K-C3172PQ-10GE|
| 2. |Cisco|N3K-C3048-ZM-F|
| 3. |Cisco|N5K-C5596UP-FA|

## <a name="list-of-devices-tested-in-the-field"></a>Listen over enheder testet i feltet

Dette afsnit indeholder listen over enheder, der er blevet implementeret i feltet ved StorSimple kunder. Disse har ikke testet af Microsoft, men har sandsynligvis til at arbejde med enheden StorSimple.
 
| Parameter                         | Værdi                                    |
|-----------------------------------|------------------------------------------|
| Skifte mærke                     | Juniper                                  |
| Skift model                    | ex4550 32F                               |
| Skift operativsystemversion | JunOS 12.3R9.4                           |
| Blade model                     | Porte indbyggede PIC (0)                    |
| Sender/modtager foretage                  | Juniper                                  |
| Sender/modtager model               | Varenummer 740 021308 <br></br> Varenummer 740 030658                   |
| Sender/modtager firmware-version    | Revision 01 Version 0,0 (rapporteret)            |
| Kabel model                     | Duplex jumper LC/LC 50/125µ, OM3 =, LSZH |
| StorSimple model                | 8600                                     |
| StorSimple softwareversion     | 6.3.9600.17491                           |


## <a name="list-of-devices-tested-by-oem-provider-mellanox"></a>Listen over enheder, der er testet ved OEM-udbyder (Mellanox)  

Mellanox har testet følgende small form-faktor tilslutningsbar (SFP) du skal, kabler og parametre til at sikre, at de fungere optimalt med Mellanox netværksgrænseflader som 10 GbE netværksgrænseflader på enheden StorSimple.

### <a name="cables-and-modules-supported-by-mellanox"></a>Kabler og moduler, der understøttes af Mellanox 

I følgende tabel vises de kabler og moduler, der understøttes af Mellanox. Disse har ikke testet af Microsoft, men har sandsynligvis til at arbejde med enheden StorSimple.

| S. Nej. | Hastighed | Model                 | Beskrivelse                                            | Gøre                |
|--------|-------|-----------------------|--------------------------------------------------------|-----------------------|
| 1.     | 10 GbE| CAB-SFP-SFP - 1M        | passive kobberkabel SFP + 10 Gb/s 1m                   | Arista                |
| 2.     | 10 GbE| CAB-SFP-SFP - 2M        | passive kobberkabel SFP + 10 Gb/s 2m                   | Arista                |
| 3.     | 10 GbE| CAB-SFP-SFP - 3M        | passive kobberkabel SFP + 10 Gb/s 3m                   | Arista                |
| 4.     | 10 GbE| CAB-SFP-SFP - 5M        | passive kobberkabel SFP + 10 Gb/s 5m                   | Arista                |
| 5.     | 10 GbE| Cisco SFP-H10GBCU1M   | Cisco SFP + kabel                                       | Cisco                 |
| 6.     | 10 GbE| Cisco SFP-H10GBCU3M   | Cisco SFP + kabel                                       | Cisco                 |
| 7.     |10 GbE | Cisco SFP-H10GBCU5M   | Cisco SFP + kabel                                       | Cisco                 |
| 8.     | 10 GbE| J9281B HP X242 10 G    | SFP + til SFP + 1m Direct Attach kobberkabel             | HP                    |
| 9.     | 10 GbE| 455883 B21 HP BLc     | 10Gb SR SFP + vælge                                       | HP                    |
| 10.    | 10 GbE| 455886 B21 HP BLc     | 10Gb LR SFP + vælge                                       | HP                    |
| 11.    |10 GbE | 487649 B21 HP BLc     | SFP + 0,5 m brug for 10 GbE kobber kabel                           | HP                    |
| 12.    |10 GbE | 487652 B21 HP BLc     | SFP + 1m brug for 10 GbE kobber kabel                             | HP                    |
| 13.    |10 GbE | 487655 B21 HP BLc     | SFP + 3m brug for 10 GbE kobber kabel                             | HP                    |
| 14.    |10 GbE | 487658 B21 HP BLc     | SFP + 7m brug for 10 GbE kobber kabel                             | HP                    |
| 15.    |10 GbE | 537963 B21 HP BLc     | SFP + 5m brug for 10 GbE kobber kabel                             | HP                    |
| 16.    |10 GbE | AP784A HP             | 3m C-udgaven Passive kobber SFP + kabel                  | HP                    |
| 17.    |10 GbE | AP785A HP             | 5m C-udgaven Passive kobber SFP + kabel                  | HP                    |
| 18.    |10 GbE | AP818A HP             | 1m B serie aktive kobber SFP + kabel                   | HP                    |
| 19.    |10 GbE | AP819A HP             | 3m B serie aktive kobber SFP + kabel                   | HP                    |
| 20.    |10 GbE | J9150A HP             | X132 10 G SFP + LC SR sender/modtager                        | HP                    |
| 21.    |10 GbE | J9151A HP             | X132 10 G SFP + LC LR sender/modtager                        | HP                    |
| 22.    |10 GbE | J9283B HP             | X242 10 G SFP + SFP + 3 m DAC-kabel                        | HP                    |
| 23.    |10 GbE | J9285B HP             | X242 10 G SFP + SFP + 7 m DAC-kabel                        | HP                    |
| 24.    | 10 GbE| JD095B HP             | X240 10 G SFP + SFP + 0.65 m DAC-kabel                     | HP                    |
| 25.    | 10 GbE| JD096B HP             | X240 10 G SFP + SFP + 1,2 m DAC-kabel                      | HP                    |
| 26.    | 10 GbE| JD097B HP             | X240 10 G SFP + SFP + 3 m FAR-kabel                        | HP                    |
| 27.    | 10 GbE| MAM1Q00A QSA Mellanox | QSFP til SFP + kortet                                   | Mellanox teknologier |
| 28.    | 10 GbE| MC2309124-006 Mt      | Passive kobberkabel 1 x SFP+ til QSFP 10 Gb/s 24awg 7 m   | Mellanox teknologier |
| 29.    | 10 GbE| MC2309124-007 Mt      | Passive kobberkabel 1 x SFP+ til QSFP 10 Gb/s 24awg 7 m   | Mellanox teknologier |
| 30.    | 10 GbE| MC2309130-003 Mt      | Passive kobberkabel 1 x SFP+ til QSFP 10 Gb/s 30awg 3 m   | Mellanox teknologier |
| 31.    | 10 GbE| MC2309130 00A Mt      | Passive kobberkabel 1 x SFP+ til QSFP 10 Gb/s 30awg 0,5 m | Mellanox teknologier |
| 32.    | 10 GbE| MC3309124-005 Mt      | Passive kobber kabel 1 x SFP+ 10 Gb/s 24awg 5 m           | Mellanox teknologier |
| 33.    | 10 GbE| MC3309124-007 Mt      | Passive kobber kabel 1 x SFP+ 10 Gb/s 24awg 7 m           | Mellanox teknologier |
| 34.    | 10 GbE| MC3309130-003 Mt      | Passive kobber kabel 1 x SFP+ 10 Gb/s 30awg 3 m           | Mellanox teknologier |
| 35.    | 10 GbE| MC3309130 00A Mt      | Passive kobber kabel 1 x SFP+ 10 Gb/s 30awg 0,5 m         | Mellanox teknologier |

### <a name="switches-supported-by-mellanox"></a>Parametre, der understøttes af Mellanox 

I følgende tabel vises de parametre, der understøttes af Mellanox. Disse har ikke testet af Microsoft, men har sandsynligvis til at arbejde med enheden StorSimple.

| S. Nej. | Hastighed | Model | Beskrivelse                                                         | Gøre              |
|--------|-------|-------------|---------------------------------------------------------------------|-------------|
| 1.     | Brug for 10 GbE | 516733 B21  | HP ProCurve 6120XG brug for 10 GbE Ethernet bladeswitch                      | HP    |
| 2.     | Brug for 10 GbE | 538113 B21  | HP brug for 10 GbE Pass-Through-modul (PTM)                                  | HP    |
| 3.     | Brug for 10 GbE | EN4093      | IBM PureFlex System-strukturen EN4093 10 Gigabit SVG Skift modul | IBM   |
| 4.     | 1GbE  | 3020        | Cisco Catalyst 3020 1GbE Skift blade                               | Cisco |
| 5.     | 1GbE  | 3020 X       | Cisco Catalyst 3020 X 1GbE Skift blade                              | Cisco |
| 6.     | 1GbE  | 438030 B21  | HP 1GbE Skift modul - GbE2c lag 2/3 Ethernet Blade Switch       | HP    |
| 7.     | 1GbE  | 6120G       | HP ProCurve 6120G/XG 1GbE Skift blade                              | HP    |

## <a name="next-steps"></a>Næste trin

[Få mere at vide om StorSimple hardwarekomponenter og status](storsimple-monitor-hardware-status.md).
