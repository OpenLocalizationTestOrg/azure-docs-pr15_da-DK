<properties
 pageTitle="Få værktøjerne (Windows 7 +) | Microsoft Azure"
 description="Downloade og installere de nødvendige værktøjer og softwaren til det første eksempelprogram til din Pi på Windows 7 og nyere versioner."
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

# <a name="12-get-the-tools-windows-7-"></a>1.2 få værktøjerne (Windows 7 +) 

> [AZURE.SELECTOR]
- [Windows 7 +](iot-hub-raspberry-pi-kit-node-lesson1-get-the-tools-win32.md)
- [Ubuntu 16.04](iot-hub-raspberry-pi-kit-node-lesson1-get-the-tools-ubuntu.md)
- [macOS 10,10](iot-hub-raspberry-pi-kit-node-lesson1-get-the-tools-mac.md)

## <a name="121-what-you-will-do"></a>1.2.1 hvad du vil gøre

Download udviklingsværktøjerne og softwaren til det første eksempelprogram til din hindbær Pi 3. Hvis du opfylder problemer, kan du søge efter løsninger i [foretage fejlfinding af siden](iot-hub-raspberry-pi-kit-node-troubleshooting.md).

## <a name="122-what-you-will-learn"></a>1.2.2 hvad du lærer
- Sådan installeres ciffer og Node.js
  - [Ciffer](https://git-scm.com) er et Åbn kilde distribueret version control system. Eksempelprogrammet til denne lektion er gemt på ciffer.
  - [Node.js](https://nodejs.org/en/) er en JavaScript kørselstidspunktet med et omfattende pakke økosystem.
- Sådan bruges NPM til at installere yderligere værktøjer til udvikling af Node.js.
  - Versionskravet af Node.js er 4,5 LTS.
  - [NPM](https://www.npmjs.com) er et af pakke cheferne for Node.js.

## <a name="123-what-you-need"></a>1.2.3 hvad du bør

- Forbindelse til internettet til at hente udviklingsværktøjerne og softwaren
- En computer, der kører Windows

## <a name="124-install-git-and-nodejs"></a>1.2.4 installere ciffer og Node.js

Klik på linkene nedenfor for at hente og installere ciffer og Node.js LTS til Windows.

- [Få ciffer til Windows](https://git-scm.com/download/win/)
- [Få Node.js LTS til Windows](https://nodejs.org/en/)

## <a name="125-install-additional-nodejs-development-tools"></a>1.2.5 installere yderligere værktøjer til udvikling af Node.js

Du kan bruge [gulp.js](http://gulpjs.com) til at automatisere installationen af programmet til din Pi. Du kan også bruge [enhed-registrering-cli](https://github.com/Azure/device-discovery-cli) for at hente netværksoplysninger om dine IoT enheder.

Starte en kommandoprompt som administrator. Installere `gulp` og `device-discovery-cli` ved at køre følgende kommando:

```bash
npm install -g device-discovery-cli gulp
```
    
Hvis du oplever problemer med at installere Node.js og værktøjerne til udvikling af yderligere Node.js på computeren, skal du se [fejlfindingsvejledningen](iot-hub-raspberry-pi-kit-node-troubleshooting.md) for løsninger på almindelige problemer.

## <a name="126-install-visual-studio-code"></a>1.2.6 installere Visual Studio kode

[Hent](https://code.visualstudio.com/docs/setup/windows) og Installer Visual Studio-kode. Visual Studio-kode er en lette, men effektiv kode Kildeeditor til Windows, Linux og macOS. Du kan bruge denne editor senere i selvstudiet redigere eksempelkode.

## <a name="127-summary"></a>1.2.7 oversigt

Du har installeret de nødvendige udviklingsværktøjer og softwaren til det første eksempelprogram. I næste afsnit, du opretter, installere og køre eksempelprogrammet på din Pi.

## <a name="next-steps"></a>Næste trin

[1.3 oprette og installere programmet blink eksempel](iot-hub-raspberry-pi-kit-node-lesson1-deploy-blink-app.md)
