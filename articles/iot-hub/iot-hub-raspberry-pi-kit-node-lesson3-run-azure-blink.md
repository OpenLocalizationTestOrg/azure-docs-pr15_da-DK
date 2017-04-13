<properties
 pageTitle="Køre Northwind til at sende enhed i skyen meddelelser | Microsoft Azure"
 description="Installere og køre en Northwind til din hindbær Pi 3, der sender meddelelser til IoT hub og blinker LED."
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

# <a name="32-run-sample-application-to-send-device-to-cloud-messages"></a>3.2 køre Northwind til at sende meddelelser til enhed i skyen

## <a name="321-what-you-will-do"></a>3.2.1 hvad du vil gøre

Installere og køre en eksempelprogrammet på din hindbær Pi 3, som sender meddelelser til dine IoT-hub. Hvis du opfylder problemer, kan du søge efter løsninger i [foretage fejlfinding af siden](iot-hub-raspberry-pi-kit-node-troubleshooting.md).

## <a name="322-what-you-will-learn"></a>3.2.2 hvad du lærer

- Sådan bruges værktøjet gulp til at installere og køre programmet eksempel Node.js på din Pi.

## <a name="323-what-you-need"></a>3.2.3 hvad du bør

- Du skal have gennemført den forrige sektion i denne lektion: [oprette en Azure funktionen app og en Azure-lager-konto til at behandle og gemme IoT hub meddelelser](iot-hub-raspberry-pi-kit-node-lesson3-deploy-resource-manager-template.md).

## <a name="324-get-your-iot-hub-and-device-connection-strings"></a>3.2.4 få strenge din IoT hub og enhed

Forbindelsesstrengen enhed bruges til at forbinde Pi til din IoT hub. IoT hub forbinde streng, der bruges til at forbinde dine IoT hub til identiteten enhed, der repræsenterer din Pi i IoT hub.

- Få vist IoT hub forbindelsesstrengen ved at køre følgende kommando for Azure CLI:

```bash
az iot hub show-connection-string --name {my hub name} --resource-group iot-sample
```

`{my hub name}`er det navn, du har angivet i lektion 2. Brug `iot-sample` som værdien af `{resource group name}` Hvis du ikke ændrer værdien i lektion 2.

- Få vist forbindelsesstreng enhed ved at køre følgende kommando:

```bash
az iot device show-connection-string --hub {my hub name} --device-id myraspberrypi --resource-group iot-sample
```

`{my hub name}`kræver samme værdi som den, der bruges med kommandoen foregående. Brug `iot-sample` som værdien af `{resource group name}` og bruge `myraspberrypi` som værdien af `{device id}` Hvis du ikke ændrer værdien i lektion 2.

## <a name="325-configure-the-device-connection"></a>3.2.5 konfigurere forbindelsen til enhed

1. Initialiseret konfigurationsfilen ved at køre følgende kommandoer:

    ```bash
    npm install
    gulp init
    ```

2. Åbn filen enhed konfiguration `config-raspberrypi.json` i Visual Studio-kode ved at køre følgende kommando:

    ```bash
    # For Windows command prompt
    code %USERPROFILE%\.iot-hub-getting-started\config-raspberrypi.json
  
    # For macOS or Ubuntu
    code ~/.iot-hub-getting-started/config-raspberrypi.json
    ```

    ![config.JSON](media/iot-hub-raspberry-pi-lessons/lesson3/config.png)

3. Foretage de følgende erstatninger i den `config-raspberrypi.json` fil:

  - Erstat **[enhed værtsnavn eller IP-adresse]** med enhed IP-adresse eller hostname, du har fået fra `device-discovery-cli` eller med værdien nedarvet fra hvad du har konfigureret i Lektion 1.
  - Erstatte **[IoT enhed forbindelsesstreng]** med den `device connection string` du der fås.
  - Erstatte **[IoT hub forbindelsesstreng]** med den `iot hub connection string` du der fås.

Du opdaterer den `config-raspberrypi.json` filen, så du kan installere eksempelprogrammet fra din computer.

## <a name="326-deploy-and-run-the-sample-application"></a>3.2.6 installere og køre eksempelprogrammet

Installere og køre eksempelprogrammet på din Pi ved at køre følgende kommando:

```bash
gulp
```

> [AZURE.NOTE] Standard gulp opgaven kører `install-tools`, `deploy`, og `run` opgaver sekventielt. I [Lektion 1](iot-hub-raspberry-pi-kit-node-lesson1-deploy-blink-app.md)skal kørte du disse opgaver efter hinanden separat.

## <a name="327-verify-the-sample-application-works"></a>3.2.7 bekræfte eksempel programmet works

Du bør se Indikator, der er tilsluttet din Pi blinker hvert 2. Hver gang LED blinker, eksempelprogrammet sender en meddelelse til din IoT hub og bekræfter, meddelelsen er blevet sendt til IoT-hub. Desuden udskrives meddelelsen i vinduet console for hver af meddelelsen modtages af IoT hubben. Eksempelprogrammet ophører automatisk efter afsendelse af 20 meddelelser.

![](media/iot-hub-raspberry-pi-lessons/lesson3/gulp_run.png)

## <a name="328-summary"></a>3.2.8 oversigt

Du har installeret og køre det nye blink eksempelprogram på din Pi til at sende enhed i skyen meddelelser til dine IoT-hub. Du kan flytte til næste afsnit til at overvåge dine meddelelser, som de er skrevet på kontoen Azure-lager.

## <a name="next-steps"></a>Næste trin

[3.3 læste meddelelser bevaret i Azure-lager](iot-hub-raspberry-pi-kit-node-lesson3-read-table-storage.md)