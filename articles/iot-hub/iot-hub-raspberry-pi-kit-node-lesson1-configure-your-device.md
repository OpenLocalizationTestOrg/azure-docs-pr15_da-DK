<properties
 pageTitle="Konfigurere din enhed | Microsoft Azure"
 description="Konfigurere din hindbær Pi 3 til brug første gang, og Installer Raspbian OS, et gratis operativsystem, der er optimeret til hindbær Pi hardware."
 services="iot-hub"
 documentationCenter=""
 authors="shizn"
 manager="timlt"
 tags=""
 keywords=""/>

<tags
 ms.service="iot-hub"
 ms.devlang="multiple"
 ms.topic="article"
 ms.tgt_pltfrm="na"
 ms.workload="na"
 ms.date="10/21/2016"
 ms.author="xshi"/>

# <a name="11-configure-your-device"></a>1.1 konfigurere din enhed

## <a name="111-what-you-will-do"></a>1.1.1 hvad du vil gøre

Konfigurere din Pi til brug for første gang, og Installer Raspbian operativsystemet, et gratis operativsystem, der er optimeret til hindbær Pi hardware. Hvis du opfylder problemer, kan du søge efter løsninger i [foretage fejlfinding af siden](iot-hub-raspberry-pi-kit-node-troubleshooting.md).

## <a name="112-what-you-will-learn"></a>1.1.2 hvad du lærer

I dette afsnit, skal lære du følgende:

- Hvordan du installerer Raspbian på din Pi
- Sådan Tilslut din Pi, ved hjælp af et USB-kabel
- Hvordan du knytter din Pi til netværket ved hjælp af en Ethernet-kablet eller Wi-Fi
- Sådan tilføjes en Indikator til breadboard og forbinde den til din Pi

## <a name="113-what-you-need"></a>1.1.3 hvad du bør

For at fuldføre dette afsnit skal bruge du følgende dele fra din hindbær Pi 3 Starter Kit:

- Tavlen hindbær Pi 3
- 16GB MicroSD kortet
- 5 v 2A power leverer med seks foot micro USB-kabel
- Breadboard
- Forbindelse kabler
- En 560 Ohm modstand
- En udbredte 10mm LED
- Ethernet-kablet

![Ting i din Starter Kit](media/iot-hub-raspberry-pi-lessons/lesson1/starter_kit.jpg)

Du skal også:

- En traditionel eller trådløs forbindelse til din Pi til at oprette forbindelse til
- Et USB-SD adapter eller mini SD kort brænde OS billedet til kortet MicroSD.
- En computer, der kører Windows, Mac eller Linux. Computeren, der bruges til at installere Raspbian på kortet MicroSD.
- Forbindelse til internettet til at hente de nødvendige værktøjer og software

## <a name="114-install-raspbian-on-the-microsd-card"></a>1.1.4 installere Raspbian på kortet MicroSD

Forberede MicroSD kortet for at skrive Raspbian billedet til.

1. Hent Raspbian.
  1. [Hente](https://www.raspberrypi.org/downloads/raspbian/) zip-filen til Raspbian Jessie med Pixel.
  2. Udtrække Raspbian billedet til en anden mappe på computeren.
2. Installere Raspbian til kortet MicroSD.
  1. [Hent](https://www.etcher.io) og Installer værktøjet Etcher SD kort-brænder.
  2. Kør Etcher, og vælg det Raspbian billede, du har gemt i trin 1.
  3. Vælg MicroSD kort drevet.
    Bemærk: Etcher måske allerede har markeret det korrekte drev.
  4. Klik på Flash for at installere Raspbian til kortet MicroSD.
  5. Fjerne kortet MicroSD fra computeren, når det er fuldført.
    Bemærk: Det er sikkert at fjerne kortet MicroSD direkte, fordi Etcher automatisk skubber eller afbryder forbindelsen til kortet MicroSD når er afsluttet.
  6. Indsætte kortet MicroSD i din Pi.

![Indsætte SD-kortet](media/iot-hub-raspberry-pi-lessons/lesson1/insert_sdcard.jpg)

## <a name="115-power-on-your-pi"></a>1.1.5 power på din Pi

Tænd din Pi ved hjælp af micro USB-kablet og power levering.

![Tænd](media/iot-hub-raspberry-pi-lessons/lesson1/micro_usb_power_on.jpg)

> [AZURE.NOTE] Det er vigtigt at bruge power levering i pakken, der er mindst 2A at sikre dig, at din hindbær indføres med tilstrækkelig fungerer korrekt.

## <a name="116-connect-your-raspberry-pi-3-to-the-network"></a>1.1.6 tilslutte din hindbær Pi 3 til netværket

Du kan forbinde dine Pi, til et netværk med kabel eller til et trådløst netværk. Sørg for, at din Pi er forbundet med det samme netværk som din computer. For eksempel kan du forbinde dine Pi til den samme parameter, der er tilsluttet din computer.

### <a name="1161-connect-to-a-wired-network"></a>1.1.6.1 oprette forbindelse til et netværk med kabel

Brug af Ethernet-kablet til at forbinde dine Pi til dit netværk med kabel. To led-indikatorerne på din Pi slå, hvis der er oprettet forbindelse.

![Oprette forbindelse ved hjælp af Ethernet-kablet](media/iot-hub-raspberry-pi-lessons/lesson1/connect_ethernet.jpg)

### <a name="1162-connect-to-a-wireless-network"></a>1.1.6.2 oprette forbindelse til et trådløst netværk

Følg [vejledningen](https://www.raspberrypi.org/learning/software-guide/wifi/) fra hindbær Pi Foundation tilsluttes din Pi til dit netværk. Disse instruktioner skal du først oprette forbindelse en skærm og et tastatur til din Pi.

## <a name="117-connect-the-led-to-your-pi"></a>1.1.7 forbinde LED til din Pi

Brug [breadboard](https://learn.sparkfun.com/tutorials/how-to-use-a-breadboard), connector kabler, LED og modstand for at udføre denne opgave. Du kan forbinde dem til de [generelle input/output](https://www.raspberrypi.org/documentation/usage/gpio/) (GPIO) porte af din Pi. 

![Breadboard, LED og modstand](media/iot-hub-raspberry-pi-lessons/lesson1/breadboard_led_resistor.jpg)

1. Oprette forbindelse kortere ben på led-Indikatoren til **GPIO GND (pinkode 6)**.
2. Oprette forbindelse længere ben på led-Indikatoren til en del af modstand.
3. Anden del af modstand tilsluttes **GPIO 4 (pinkode 7)**.

Bemærk, at LED polaritet er vigtige. Indstillingen polaritet kaldes ofte aktive lav.

![Benet](media/iot-hub-raspberry-pi-lessons/lesson1/pinout_breadboard.png)

Congratulation! Du har konfigureret din Pi.

## <a name="118-summary"></a>1.1.8 oversigt over

I dette afsnit, har du lært at konfigurere din Pi ved at installere Raspbian, forbinder din Pi til et netværk og forbindelse en Indikator til din Pi. Bemærk, at LED endnu ikke lys op. Du har installeret de nødvendige værktøjer og software med henblik på kører en eksempelprogrammet på din Pi i næste afsnit.

![Hardware er klar](media/iot-hub-raspberry-pi-lessons/lesson1/hardware_ready.jpg)

## <a name="next-steps"></a>Næste trin

[1.2 få værktøjerne](iot-hub-raspberry-pi-kit-node-lesson1-get-the-tools-win32.md)
