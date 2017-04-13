<properties
 pageTitle="Læste meddelelser bevaret i Azure-lager | Microsoft Azure"
 description="Overvåge enhed i skyen-meddelelser, som de er skrevet til Azure table storage."
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

# <a name="33-read-messages-persisted-in-azure-storage"></a>3.3 læste meddelelser bevaret i Azure-lager

## <a name="331-what-will-you-do"></a>3.3.1 Hvad vil du gøre

Overvåge enhed i skyen meddelelser, der sendes fra dit hindbær Pi 3 til IoT-hub, som meddelelserne, der er skrevet til Azure table storage. Hvis du opfylder problemer, kan du søge efter løsninger i [foretage fejlfinding af siden](iot-hub-raspberry-pi-kit-node-troubleshooting.md).

## <a name="332-what-will-you-learn"></a>3.3.2 Hvad vil du lære

- Sådan bruges gulp Læs meddelelse opgaven til at læse meddelelser permanent i din Azure-tabellager.

## <a name="333-what-do-you-need"></a>3.3.3 Hvad har du brug for

- Du skal nu har fuldført den forrige sektion, der [kører Azure blink eksempel-programmet på din hindbær Pi 3](iot-hub-raspberry-pi-kit-node-lesson3-run-azure-blink.md).

## <a name="334-read-new-messages-from-your-storage-account"></a>3.3.4 læse nye meddelelser fra din lagerplads konto

I det forrige afsnit kørte du en eksempelprogrammet på din Pi. Eksempel-program, der sendes meddelelser til Azure IoT-hub. De meddelelser, som sendes til din IoT hub er gemt i din Azure-tabellager via appen Azure funktionen. Du skal bruge forbindelsesstrengen Azure-lager til at læse meddelelser fra din Azure-tabellager.

Hvis du vil læse meddelelser, der er gemt i din Azure-tabellager, skal du følge disse trin:

1. Få vist forbindelsesstrengen ved at køre følgende kommandoer:

    ```bash
    az storage account list -g iot-sample --query [].name
    az storage account show-connection-string -g iot-sample -n {storage name}
    ```

    Kommandoen første henter den `storage name` , der bruges i den anden kommando til at få vist forbindelsesstrengen. `iot-sample`er standardværdien for `{resource group name}` Hvis du ikke ændrer værdien i lektion 2.

2. Åbn konfigurationsfilen `config-raspberrypi.json` fil i Visual Studio-kode ved at køre følgende kommando:

    ```bash
    # For Windows command prompt
    code %USERPROFILE%\.iot-hub-getting-started\config-raspberrypi.json

    # For macOS or Ubuntu
    code ~/.iot-hub-getting-started/config-raspberrypi.json
    ```

3. Erstatte `[Azure Storage connection string]` med den forbindelsesstreng, du har fået i trin 1.
4. Gemme den `config-raspberrypi.json` fil.
5. Sende meddelelser igen og læse dem fra din Azure-tabellager ved at køre følgende kommando:

    ```bash
    gulp run --read-storage
    ```

    Læse fra Azure-tabellager logik er i den `azure-table.js` fil.

    ![gulp køre – Læs-lagerplads](media/iot-hub-raspberry-pi-lessons/lesson3/gulp_read_message.png)

## <a name="335-summary"></a>3.3.5 oversigt over

Du har korrekt forbundet din Pi til din IoT hub i skyen og brugte programmet blink eksempel til at sende meddelelser enhed i skyen. Appen Azure funktionen bruges også til at gemme indgående IoT hub meddelelser til Azure table storage. Du kan gå videre til næste lektion om, hvordan du sende cloud-til-enhed meddelelser fra din IoT hub til din Pi.

## <a name="next-steps"></a>Næste trin

[Øvelse 4: Send cloud-til-enhed meddelelser](iot-hub-raspberry-pi-kit-node-lesson4-send-cloud-to-device-messages.md)
