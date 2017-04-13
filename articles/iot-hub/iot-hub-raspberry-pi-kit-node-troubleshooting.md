<properties
 pageTitle="Fejlfinding i forbindelse med | Microsoft Azure"
 description="Fejlfinding i forbindelse med siden for hindbær Pi Node.js oplevelse"
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

# <a name="troubleshooting"></a>Fejlfinding i forbindelse med

## <a name="hardware-issues"></a>Problemer med hardware

### <a name="the-application-runs-well-but-the-led-is-not-blinking"></a>Programmet kører godt, men LED ikke blinker

Dette problem er altid relateret til hardware kredsløb forbindelsen. Brug følgende trin til at identificere problemer med.

1. Markér, hvis du vælger den rigtige **GPIO** på din tavle. De to porte i denne lektion skal være **GPIO GND (pinkode 6)** og **GPIO 04 (pinkode 7)**.
2. Kontrollér, om polaritet af din LED er korrekt. Længere ben skal angiver **positive**, anode pinkode.
3. Brug **3.3 v fastgøre** og **GND pinkode** på din hindbærfyld Pi 3. Behandle dine Pi som DC Power. Markér, hvis LED fungerer fint.

![LED specifikation](media/iot-hub-raspberry-pi-lessons/troubleshooting/led_spec.png)

### <a name="other-hardware-issues"></a>Andre problemer med hardware

Oplysninger om at løse almindelige problemer på hindbær Pi 3 finder du referere til [officielle fejlfinding side](http://elinux.org/R-Pi_Troubleshooting).

## <a name="nodejs-package-issues"></a>Node.js pakke problemer

### <a name="no-response-during-gulp-tasks"></a>Intet svar under gulp opgaver

Hvis du støder på problemer med at køre gulp opgaver, kan du tilføje den `--verbose` indstilling til fejlfinding. Prøv at afbryde aktuelle gulp opgaver med `Ctrl + C` og derefter køre følgende kommando i console vindue til at se fejlfinding af meddelelser. Du kan se detaljerede fejlmeddelelser, der udskrives i din console output. 

```bash
gulp --verbose
```

### <a name="device-discovery-issues"></a>Problemer med enheder registrering

For hjælp til fejlfinding af almindelige problemer med den `devdisco` kommandoen, skal du kontrollere [vigtige oplysninger om](https://github.com/Azure/device-discovery-cli/blob/develop/readme.md).

### <a name="other-npm-issues"></a>Andre NPM problemer

Prøv at opdatere din NPM pakke med følgende kommando:

```bash
npm install -g npm
```

Hvis problemet fortsætter, Skriv dine kommentarer i slutningen af denne artikel eller oprette et Github problem i vores [Eksempel lager](https://github.com/Azure-Samples/iot-hub-node-raspberrypi-getting-started)

## <a name="remote-debugging"></a>Ekstern fejlfinding

### <a name="run-the-sample-application-in-debug-mode"></a>Køre eksempelprogrammet i fejlsikret tilstand

```bash
gulp run --debug
```

Når fejlfinding program er klar, skal du se afsnittet ```Debugger listening on port 5858``` i output fra konsollen.

### <a name="configure-vs-code-to-connect-to-the-remote-device"></a>Konfigurere eller-kode til at oprette forbindelse til eksterne enheden

Åbn panelet **fejlfinding** i den venstre side.

Klik på knappen grønne **Starte fejlfinding** (F5). Eller-kode åbnes en **launch.json** -fil, som du vil opdatere.

Opdatere filen **launch.json** med følgende indhold. Erstatte `[device hostname or IP address]` med faktiske enhed IP-adresse eller værtsnavn.   

```json
{
    "version": "0.2.0",
    "configurations": [
        {
            "name": "Attach",
            "type": "node",
            "request": "attach",
            "port": 5858,
            "address": "[device hostname or IP address]",
            "restart": false,
            "sourceMaps": false,
            "outDir": null,
            "localRoot": "${workspaceRoot}",
            "remoteRoot": null
        }
    ]
}
```

![Remote fejlfinding konfiguration](media/iot-hub-raspberry-pi-lessons/troubleshooting/remote_debugging_configuration.png)

### <a name="attach-to-the-remote-application"></a>Vedhæfte til det eksterne program

Klik på den grønne **Starte fejlfinding** (F5) for at starte fejlfinding. 

Læs [JavaScript eller-kode](https://code.visualstudio.com/docs/languages/javascript#_debugging) for at få flere oplysninger om fejlfindingsværktøjet.

![Remote fejlfinding interaktive](media/iot-hub-raspberry-pi-lessons/troubleshooting/remote_debugging_interactive.png)

## <a name="azure-cli-issues"></a>Azure CLI problemer

Azure CLI er et eksempel build. Du kan referere til [Preview installere vejledning](https://github.com/Azure/azure-cli/blob/master/doc/preview_install_guide.md) til at afhjælpe.

Hvis du støder på en hvilken som helst fejl med værktøjet, filen er et [problem](https://github.com/Azure/azure-cli/issues) i afsnittet **problemer** i GitHub repo.

Du kan finde hjælp til fejlfinding af almindelige problemer, [vigtige oplysninger om](https://github.com/Azure/azure-cli/blob/master/README.rst).

## <a name="python-installation-issues"></a>Problemer med installation af Python

### <a name="legacy-installation-issues-macos"></a>Problemer med ældre installation (macOS)

Når du installerer **pip**, udløst fejlen tilladelse, når der er ældre-pakker, der installeres med **sø** tilladelser. Denne situation opstår, fordi tidligere installation af Python ved hjælp af brew (macOS) ikke er fjernet helt. Nogle **pip** -pakker fra en tidligere installation blev oprettet af rod, som medfører, at fejlen tilladelse. Løsningen er at fjerne de pakker, der er installeret af rod. Brug følgende trin til at udføre denne opgave:

1. Gå til /usr/local/lib/python2.7/site-packages
2. Angive pakker oprette ved rod:`ls -l | grep root`
3. Fjerne pakker fra trin 2:`sudo rm -rf {package name}`
4. Geninstallere Python.

## <a name="azure-iot-hub-issues"></a>Azure IoT hub problemer

Hvis du har blev klargjort Azure IoT hubben med `azure-cli`, og du har brug for et værktøj til at administrere de enheder, oprette forbindelse til din IoT-hub, kan du prøve følgende værktøjer:

### <a name="device-explorer"></a>Enhed Explorer

[Enhed Explorer](https://github.com/Azure/azure-iot-sdks/blob/master/tools/DeviceExplorer/doc/how_to_use_device_explorer.md) kører på din lokale Windows-computer og opretter forbindelse til din IoT hub i Azure. Den kommunikerer med følgende [IoT Hub slutpunkter](iot-hub-devguide.md):

- *Administration af enheder identitet* for at klargøre og administrere enheder registreret hos IoT centrum.
- *Modtag enhed i skyen* til gør det muligt at overvåge meddelelser, der sendes fra enheden til din IoT hub.
- *Sende cloud-til-enhed* til giver dig mulighed at sende meddelelser til dine enheder på IoT centrum.

Konfigurere din `IoT hub connection string` i dette værktøj kan bruge alle funktioner.

### <a name="iot-hub-explorer"></a>IoT hub Explorer

[IoT hub Explorer](https://github.com/Azure/azure-iot-sdks/blob/master/tools/iothub-explorer/readme.md) er et eksempel på flere platforme CLI værktøj til at administrere enhed klienter. Værktøjet gør det muligt at administrere enheder i registreringsdatabasen identitet, overvåge enhed i skyen meddelelser og sende cloud-til-enhed kommandoer.

For at installere den nyeste (foreløbig version) version af værktøjet iothub explorer skal du køre følgende kommando i dit miljø, kommandolinjen:

```
npm install -g iothub-explorer@latest
```

Du kan bruge følgende kommando til at få ekstra hjælp til alle iothub explorer kommandoer og deres værdier:

```bash
iothub-explorer help
```

### <a name="use-azure-portal-to-manage-your-resources"></a>Bruge Azure portal til at administrere dine ressourcer

En fuld CLI oplevelse vises i alle disse lektioner lettere at oprette og administrere alle dine Azure ressourcer. Du kan også bruge [Azure portal](../azure-portal-overview.md) til at klargøring, administrere og foretage fejlfinding af dine Azure ressourcer.

## <a name="azure-storage-issues"></a>Azure lagerplads-problemer

[Microsoft Azure lagerplads Explorer (Preview)](http://storageexplorer.com) er en enkeltstående app fra Microsoft, der gør det muligt at arbejde med Azure-lager data i Windows, macOS og Linux. Dette værktøj kan du oprette forbindelse til tabellen og få vist alle dataene i den. Du kan bruge dette værktøj til fejlfinding i din Azure-lager.
