<properties
 pageTitle="Få værktøjerne (macOS 10,10) | Microsoft Azure"
 description="Downloade og installere de nødvendige værktøjer og softwaren til det første eksempelprogram til din Pi på macOS."
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

# <a name="12-get-the-tools-macos-1010"></a>1.2 få værktøjer (macOS 10,10)

> [AZURE.SELECTOR]
- [Windows 7 +](iot-hub-raspberry-pi-kit-node-lesson1-get-the-tools-win32.md)
- [Ubuntu 16.04](iot-hub-raspberry-pi-kit-node-lesson1-get-the-tools-ubuntu.md)
- [macOS 10,10](iot-hub-raspberry-pi-kit-node-lesson1-get-the-tools-mac.md)

## <a name="121-what-you-will-do"></a>1.2.1 hvad du vil gøre

Download udviklingsværktøjerne og softwaren til det første eksempelprogram til din hindbær Pi 3. Hvis du opfylder problemer, kan du søge efter løsninger i [foretage fejlfinding af siden](iot-hub-raspberry-pi-kit-node-troubleshooting.md).

## <a name="122-what-you-will-learn"></a>1.2.2 hvad du lærer
I dette afsnit, skal lære du følgende:

- Sådan installeres ciffer og Node.js
    - [Ciffer](https://git-scm.com) er et Åbn kilde distribueret version control system. Eksempelprogrammet til denne lektion er gemt på ciffer.
    - [Node.js](https://nodejs.org/en/) er en JavaScript kørselstidspunktet med et omfattende pakke økosystem.
- Sådan bruges NPM til at installere yderligere værktøjer til udvikling af Node.js.
  - Den nødvendige minimumversion af Node.js er 4,5 LTS.
  - [NPM](https://www.npmjs.com) er et af pakke cheferne for Node.js.

## <a name="123-what-you-need"></a>1.2.3 hvad du bør

- Forbindelse til internettet til at hente udviklingsværktøjerne og softwaren
- En Mac, der kører macOS Yosemite (10,10) eller nyere

## <a name="124-install-git-and-nodejs"></a>1.2.4 installere ciffer og Node.js

For at installere ciffer og Node.js skal du bruge funktionen [Homebrew](http://brew.sh) pakke management ved at følge disse trin:

1. Installere Homebrew. Hvis du allerede har installeret Homebrew, gå til trin 2.
  1. Tryk på `Cmd + Space` og angive `Terminal` at åbne et terminal vindue.
  2. Kør følgende kommando:

    ```bash
    /usr/bin/ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)"
    ```
2. Installere ciffer og Node.js ved at køre følgende kommando:

    ```bash
    brew install node git
    ```

## <a name="125-install-additional-nodejs-development-tools"></a>1.2.5 installere yderligere værktøjer til udvikling af Node.js

Du kan bruge [gulp.js](http://gulpjs.com) til at automatisere installationen af programmet til din Pi. Du kan også bruge [enhed-registrering-cli](https://github.com/Azure/device-discovery-cli) for at hente netværksoplysninger om dine IoT enheder.

Installere `gulp` og `device-discovery-cli` ved at køre følgende kommando i vinduet Terminal:

```bash
sudo npm install -g device-discovery-cli gulp
```

Hvis du oplever problemer med at installere disse yderligere udviklingsværktøjer og Node.js på macOS, skal du se [fejlfindingsvejledningen](iot-hub-raspberry-pi-kit-node-troubleshooting.md) for løsninger på almindelige problemer.

## <a name="126-install-visual-studio-code"></a>1.2.6 installere Visual Studio kode

[Hent](https://code.visualstudio.com/docs/setup/osx) og Installer Visual Studio-kode. Visual Studio-kode er en lette, men effektiv kode Kildeeditor til Windows, Linux og macOS. Du kan bruge denne editor senere i selvstudiet redigere eksempelkode.

## <a name="127-summary"></a>1.2.7 oversigt

Du har installeret de nødvendige udviklingsværktøjer og softwaren til det første eksempelprogram. I næste afsnit, du opretter, installere og køre eksempelprogrammet på din Pi.

## <a name="next-steps"></a>Næste trin

[1.3 oprette og installere programmet blink eksempel](iot-hub-raspberry-pi-kit-node-lesson1-deploy-blink-app.md)
