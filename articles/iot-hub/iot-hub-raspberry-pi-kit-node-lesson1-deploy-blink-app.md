<properties
 pageTitle="Oprette og installere programmet blink | Microsoft Azure"
 description="Klone programmet eksempel Node.js fra Github og gulp for at installere dette program til din hindbær Pi 3 tavle. Dette eksempelprogram blinker LED tilsluttet kortet hvert 2."
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

# <a name="13-create-and-deploy-the-blink-application"></a>1.3 oprette og installere programmet blink

## <a name="131-what-you-will-do"></a>1.3.1 hvad du vil gøre

Klone programmet eksempel Node.js fra Github, og bruge værktøjet gulp til at installere eksempelprogrammet til din hindbær Pi 3. Eksempelprogrammet blinker LED tilsluttet kortet hvert 2. Hvis du opfylder problemer, kan du søge efter løsninger i [foretage fejlfinding af siden](iot-hub-raspberry-pi-kit-node-troubleshooting.md).

## <a name="132-what-you-will-learn"></a>1.3.2 hvad du lærer

- Sådan bruges de `device-discover-cli` værktøj til at hente netværk oplysninger om din Pi.
- Hvordan du kan installere og køre eksempelprogrammet på din Pi.
- Hvordan du installerer og foretage fejlfinding af programmer, der kører på din Pi.

## <a name="133-what-you-need"></a>1.3.3 hvad du bør

Du skal have gennemført Følg sektioner i Lektion 1:

- [Konfigurere din enhed](iot-hub-raspberry-pi-kit-node-lesson1-configure-your-device.md)
- [Få værktøjerne](iot-hub-raspberry-pi-kit-node-lesson1-get-the-tools-win32.md)

## <a name="134-obtain-the-ip-address-and-host-name-of-your-pi"></a>1.3.4 hente IP-adresse og host navnet på din Pi

Åbn en kommandoprompt i Windows eller et terminal vindue i macOS eller Ubuntu, og derefter køre følgende kommando:

```bash
devdisco list --eth
```

Du burde se output, der ligner følgende:

![enhedsregistrering](media/iot-hub-raspberry-pi-lessons/lesson1/device_discovery.png)

Tage noter på `IP address` og `hostname` af din Pi. Du skal bruge disse oplysninger senere i dette afsnit.

> [AZURE.NOTE] Sørg for, at din Pi er forbundet med det samme netværk som din computer. Eksempelvis hvis computeren er tilsluttet et trådløst netværk, mens din Pi er tilsluttet et netværk med kabel, du måske ikke se IP-adressen i devdisco output.

## <a name="135-clone-the-sample-application"></a>1.3.5 klone eksempelprogrammet

For at åbne eksempelkoden skal du følge disse trin:

1. Klone eksempel lager fra Github ved at køre følgende kommando:

    ```bash
    git clone https://github.com/Azure-Samples/iot-hub-node-raspberrypi-getting-started.git
    ```

2. Åbn eksempelprogrammet i Visual Studio-kode ved at køre følgende kommandoer:

    ```bash
    cd iot-hub-node-raspberrypi-getting-started
    cd Lesson1
    code .
    ```

![Repo struktur](media/iot-hub-raspberry-pi-lessons/lesson1/vscode-blink-mac.png)

Den `app.js` filer i den `app` undermappe er vigtige kildefilen, der indeholder kode til at styre LED.

### <a name="136-install-application-dependencies"></a>1.3.6 installere programmet afhængigheder

Installere bibliotekerne og andre moduler, du har brug for til eksempelprogrammet ved at køre følgende kommando:

```bash
npm install
```

## <a name="137-configure-the-device-connection"></a>1.3.7 konfigurere forbindelsen til enhed

For at konfigurere forbindelsen enhed skal du følge disse trin:

1. Generere konfigurationsfil enhed ved at køre følgende kommando:

    ```bash
    gulp init
    ```

    Konfigurationsfilen `config-raspberrypi.json` indeholder brugerlegitimationsoplysninger, du kan bruge til at logge på din Pi. For at undgå til fejlen brugerlegitimationsoplysninger, oprettes konfigurationsfilen i undermappen `.iot-hub-getting-started` af privat mappe på din computer.

2. Åbn konfigurationsfil enhed i Visual Studio-kode ved at køre følgende kommando:

    ```bash
    # For Windows command prompt
    code %USERPROFILE%\.iot-hub-getting-started\config-raspberrypi.json

    # For macOS or Ubuntu
    code ~/.iot-hub-getting-started/config-raspberrypi.json
    ```

3. Erstat pladsholderen `[device hostname or IP address]` med IP-adressen eller værtsnavnet, som du angiver i sektionen 1.3.4.

    ![Config.JSON](media/iot-hub-raspberry-pi-lessons/lesson1/vscode-config-mac.png)

Tillykke! Du har oprettet den første Northwind for din Pi.

## <a name="138-deploy-and-run-the-sample-application"></a>1.3.8 installere og køre eksempelprogrammet

### <a name="1381-install-nodejs-and-npm-on-your-pi"></a>1.3.8.1 installere Node.js og NPM på din Pi

Installere Node.js og NPM på din Pi ved at køre følgende kommando:

```bash
gulp install-tools
```

Det kan tage ti minutter at gennemføre første gang du kører denne opgave.

### <a name="1382-deploy-and-run-the-sample-app"></a>1.3.8.2 installere og køre appen eksempel

Installere og køre eksempelprogrammet ved at køre følgende kommando:

```bash
gulp deploy && gulp run
```

### <a name="1383-verify-the-app-works"></a>1.3.8.3 bekræfte app fungerer

Du bør nu se led-Indikatoren på din Pi blinker hvert 2.  Hvis du ikke kan se LED blinker, kan du se [fejlfindingsvejledningen](iot-hub-raspberry-pi-kit-node-troubleshooting.md) for løsninger på almindelige problemer.
![Indikator blinker](media/iot-hub-raspberry-pi-lessons/lesson1/led_blinking.jpg)

> [AZURE.NOTE] Brug `Ctrl + C` at afslutte programmet.

## <a name="139-summary"></a>1.3.9 oversigt over

Du har installeret de nødvendige værktøjer til at arbejde med din Pi og implementeret en Northwind til din Pi at blinke blinke. Du kan nu gå videre til næste lektion for at oprette, installere og køre et andet eksempelprogram, der forbinder din Pi til Azure IoT Hub til at sende og modtage meddelelser.

## <a name="next-steps"></a>Næste trin

Nu er du klar til at starte lektion 2, der begynder med [få de Azure værktøjer](iot-hub-raspberry-pi-kit-node-lesson2-get-azure-tools-win32.md)
