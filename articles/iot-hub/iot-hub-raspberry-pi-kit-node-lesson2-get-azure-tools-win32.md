<properties
 pageTitle="Få Azure værktøjer (Windows 7 +) | Microsoft Azure"
 description="Installere Python og Azure kommandolinjen (Azure CLI) på Windows 7 og nyere versioner."
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

# <a name="21-get-azure-tools-windows-7-"></a>2.1 få Azure værktøjer (Windows 7 +)

> [AZURE.SELECTOR]
- [Windows 7 +](iot-hub-raspberry-pi-kit-node-lesson2-get-azure-tools-win32.md)
- [Ubuntu 16.04](iot-hub-raspberry-pi-kit-node-lesson2-get-azure-tools-ubuntu.md)
- [macOS 10,10](iot-hub-raspberry-pi-kit-node-lesson2-get-azure-tools-mac.md)

## <a name="211-what-you-will-do"></a>2.1.1 hvad du vil gøre

Installer Python og på kommandolinjen Azure brugergrænseflade (Azure CLI). Hvis du opfylder problemer, kan du søge efter løsninger i [foretage fejlfinding af siden](iot-hub-raspberry-pi-kit-node-troubleshooting.md).

## <a name="212-what-you-will-learn"></a>2.1.2 hvad du lærer

- Sådan installeres Python.
- Sådan installeres Azure CLI.

## <a name="213-what-you-need"></a>2.1.3 hvad du bør

- En Windows-computer med internetforbindelse
- Et aktivt Azure abonnement. Hvis du ikke har en konto, kan du oprette en [gratis konto](https://azure.microsoft.com/free/) på blot et par minutter.

## <a name="214-install-python"></a>2.1.4 installere Python

Installere Python på din Windows-computer. Du kan installere Python 2.7, 3.4 eller 3.5. Dette selvstudium er baseret på Python 2.7. Hvis du har allerede installeret Python, gå til afsnittet 2.1.5.

[Få Python til Windows](https://www.python.org/downloads/)

Skal du også tilføje stien til de mapper, hvor python.exe og pip.exe er installeret i systemet `PATH` miljøvariablen. Som standard installeres python.exe i `C:\Python27` og pip.exe er installeret i `C:\Python27\Scripts`.

## <a name="215-install-the-azure-cli"></a>2.1.5 installere Azure CLI

Azure CLI indeholder en flere platforme kommandolinjen oplevelse til Azure, så du kan arbejde direkte fra kommandolinjen at blive klargjort og administrere ressourcer.

For at installere Azure CLI skal du følge disse trin:

1. Åbn et kommandopromptvindue som administrator.
2. Kør følgende kommandoer:

    ```bash
    pip install azure-cli-core==0.1.0b7 azure-cli-vm==0.1.0b7 azure-cli-storage==0.1.0b7 azure-cli-role==0.1.0b7 azure-cli-resource==0.1.0b7 azure-cli-profile==0.1.0b7 azure-cli-network==0.1.0b7 azure-cli-iot==0.1.0b7 azure-cli-feedback==0.1.0b7 azure-cli-configure==0.1.0b7 azure-cli-component==0.1.0b7 azure-cli==0.1.0b7
    ```
3. Kontrollere installationen ved at køre følgende kommando:

    ```bash
    az iot -h
    ```

I følgende eksempel vises, hvis installationen er fuldført.

![s iot -h](media/iot-hub-raspberry-pi-lessons/lesson2/az_iot_help_win.png)

## <a name="216-summary"></a>2.1.6 oversigt

Du har installeret Azure CLI. Fortsætte til næste afsnit for at oprette din Azure IoT Hub og enhed identitet ved hjælp af Azure CLI.

## <a name="next-steps"></a>Næste trin

[2.2 oprette IoT centrum og registrere din hindbær Pi 3](iot-hub-raspberry-pi-kit-node-lesson2-prepare-azure-iot-hub.md)
