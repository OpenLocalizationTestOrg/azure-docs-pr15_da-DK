<properties
 pageTitle="Få Azure værktøjer (macOS 10,10) | Microsoft Azure"
 description="Installere Python og Azure kommandolinjen (Azure CLI) på macOS."
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

# <a name="21-get-azure-tools-macos-1010"></a>2.1 få Azure værktøjer (macOS 10,10)

> [AZURE.SELECTOR]
- [Windows 7 +](iot-hub-raspberry-pi-kit-node-lesson2-get-azure-tools-win32.md)
- [Ubuntu 16.04](iot-hub-raspberry-pi-kit-node-lesson2-get-azure-tools-ubuntu.md)
- [macOS 10,10](iot-hub-raspberry-pi-kit-node-lesson2-get-azure-tools-mac.md)

## <a name="211-what-you-will-do"></a>2.1.1 hvad du vil gøre

Installere på Azure kommandolinjen (Azure CLI). Hvis du opfylder problemer, kan du søge efter løsninger i [foretage fejlfinding af siden](iot-hub-raspberry-pi-kit-node-troubleshooting.md).

## <a name="212-what-you-will-learn"></a>2.1.2 hvad du lærer

- Sådan installeres Azure CLI.
- Sådan tilføjes IoT undergruppe af Azure CLI.

## <a name="213-what-you-need"></a>2.1.3 hvad du bør

- En Mac med internetforbindelse
- Et aktivt Azure abonnement. Hvis du ikke har en konto, kan du oprette en [gratis konto](https://azure.microsoft.com/free/) på blot et par minutter.

## <a name="214-install-python"></a>2.1.4 installere Python

Selvom macOS leveres med Python 2.7 af feltet, anbefales det at installere Python gennem Homebrew. Se [Installere Python på macOS](http://docs.python-guide.org/en/latest/starting/install/osx/).

Installere Python og pip ved at køre følgende kommando:

```bash
brew install python
```

## <a name="215-install-the-azure-cli"></a>2.1.5 installere Azure CLI

Azure CLI indeholder en flere platforme kommandolinjen oplevelse til Azure, så du kan arbejde direkte fra kommandolinjen at blive klargjort og administrere ressourcer. 

For at installere den nyeste Azure CLI skal du følge disse trin:

1. Kør følgende kommandoer i et Terminal vindue. Det kan tage fem minutter at installere Azure CLI.

    ```bash
    pip install azure-cli-core==0.1.0b7 azure-cli-vm==0.1.0b7 azure-cli-storage==0.1.0b7 azure-cli-role==0.1.0b7 azure-cli-resource==0.1.0b7 azure-cli-profile==0.1.0b7 azure-cli-network==0.1.0b7 azure-cli-iot==0.1.0b7 azure-cli-feedback==0.1.0b7 azure-cli-configure==0.1.0b7 azure-cli-component==0.1.0b7 azure-cli==0.1.0b7
    ```

2. Kontrollere installation ved at køre følgende kommando:

    ```bash
    az iot -h
    ```
  
Du bør se følgende output, hvis installationen er fuldført.

![s iot -h](media/iot-hub-raspberry-pi-lessons/lesson2/az_iot_help_osx.png)

## <a name="215-summary"></a>2.1.5 oversigt over

Du har installeret Azure CLI. Fortsætte til næste afsnit for at oprette din Azure IoT Hub og enhed identitet ved hjælp af Azure CLI.

## <a name="next-steps"></a>Næste trin

[2.2 oprette IoT centrum og registrere din hindbær Pi 3](iot-hub-raspberry-pi-kit-node-lesson2-prepare-azure-iot-hub.md)
