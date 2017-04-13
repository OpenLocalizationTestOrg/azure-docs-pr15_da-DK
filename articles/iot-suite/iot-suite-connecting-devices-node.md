<properties
   pageTitle="Forbinde en enhed med Node.js | Microsoft Azure"
   description="Beskriver, hvordan du slutter en enhed til Azure IoT pakke forudkonfigureret remote overvågning løsningen ved hjælp af et program, der er skrevet i Node.js."
   services=""
   suite="iot-suite"
   documentationCenter="na"
   authors="dominicbetts"
   manager="timlt"
   editor=""/>

<tags
   ms.service="iot-suite"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="10/05/2016"
   ms.author="dobett"/>


# <a name="connect-your-device-to-the-remote-monitoring-preconfigured-solution-nodejs"></a>Tilslut din enhed til remote overvågning forudkonfigurerede løsningen (Node.js)

[AZURE.INCLUDE [iot-suite-selector-connecting](../../includes/iot-suite-selector-connecting.md)]

## <a name="build-and-run-the-nodejs-sample-solution"></a>Oprette og køre node.js eksempel løsningen

1. Hvis du vil klone *Microsoft Azure IoT SDK'er* GitHub lager, og Installer *Microsoft Azure IoT enhed SDK for Node.js* i dit desktop-miljø, skal du følge [forberede din udviklingsmiljø] [ lnk-github-prepare] instruktioner.

2. Fra din lokale kopi af [azure-iot-SDK'er] [ lnk-github-repo] lager, kopiere de følgende to filer fra mappen node/enhed/eksempler til en mappe på din enhed:

  - Packages.JSON
  - remote_monitoring.js

3. Åbn filen remote_monitoring.js, og se efter følgende variabel:

    ```
    var connectionString = "[IoT Hub device connection string]";
    ```

4. Erstat **[IoT Hub enhed forbindelsesstreng]** med din enhed forbindelsesstreng. Du kan finde værdierne for IoT-Hub hostname, enheds-id og nøglen til enhed i dashboardet remote overvågning løsning. En enhed forbindelsesstreng har følgende format:

    ```
    HostName={your IoT Hub hostname};DeviceId={your device id};SharedAccessKey={your device key}
    ```

    Hvis din IoT Hub hostname er **contoso** , og din enheds-id er **mydevice**, ligner din forbindelsesstreng:

    ```
    var connectionString = "HostName=contoso.azure-devices.net;DeviceId=mydevice;SharedAccessKey=2s ... =="
    ```

5. Gem filen. Kør følgende kommandoer på en kommandoprompt i mappen, der indeholder disse filer for at installere de nødvendige pakker og derefter køre eksempelprogrammet:

    ```
    npm install --save
    node remote_monitoring.js
    ```

[AZURE.INCLUDE [iot-suite-visualize-connecting](../../includes/iot-suite-visualize-connecting.md)]

[lnk-github-repo]: https://github.com/azure/azure-iot-sdks
[lnk-github-prepare]: https://github.com/Azure/azure-iot-sdks/blob/master/doc/get_started/node-devbox-setup.md