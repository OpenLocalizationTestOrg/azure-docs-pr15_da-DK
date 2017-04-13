<properties
    pageTitle="Simulere en enhed med IoT Gateway SDK | Microsoft Azure"
    description="Azure IoT Gateway SDK gennemgang ved hjælp af Linux til at illustrere afsendelse telemetri fra en simuleret enhed ved hjælp af Azure IoT Gateway SDK."
    services="iot-hub"
    documentationCenter=""
    authors="chipalost"
    manager="timlt"
    editor=""/>

<tags
     ms.service="iot-hub"
     ms.devlang="cpp"
     ms.topic="article"
     ms.tgt_pltfrm="na"
     ms.workload="na"
     ms.date="08/29/2016"
     ms.author="andbuc"/>


# <a name="azure-iot-gateway-sdk-beta--send-device-to-cloud-messages-with-a-simulated-device-using-linux"></a>Azure IoT Gateway SDK (beta) – sende enhed i skyen meddelelser med en simuleret enhed med Linux

[AZURE.INCLUDE [iot-hub-gateway-sdk-simulated-selector](../../includes/iot-hub-gateway-sdk-simulated-selector.md)]

## <a name="build-and-run-the-sample"></a>Oprette og køre eksemplet

Inden du går i gang, skal du:

- [Konfigurere din udviklingsmiljø] [ lnk-setupdevbox] til at arbejde med SDK på Linux.
- [Oprette en IoT hub] [ lnk-create-hub] i dit Azure-abonnement, skal du navnet på din hub til at udføre denne gennemgang. Hvis du ikke har en konto, kan du oprette en [gratis konto] [ lnk-free-trial] på blot et par minutter.
- Tilføj to enheder til IoT-hub, og notér deres id'er og enhed nøgler. Du kan bruge [enhed Stifinder eller iothub explorer] [ lnk-explorer-tools] værktøj til at føje dine enheder til IoT hubben du oprettede i forrige trin og hente deres taster.

Sådan oprettes stikprøvernes:

1. Åbn en shell.
2. Gå til rodmappen i din lokale kopi af **azure-iot-gateway-sdk** -lager.
3. Kør scriptet **tools/build.sh** . Dette script bruger værktøjet **cmake** til at oprette en mappe med navnet **opbygge** i rodmappen i din lokale kopi af **azure-iot-gateway-sdk** -lager og generere en makefile. Scriptet derefter opbygger løsningen og kører testene.

> [AZURE.NOTE]  Hver gang du kører scriptet **build.sh** , slettes, og derefter genopretter mappen **opbygge** i rodmappen i din lokale kopi af **azure-iot-gateway-sdk** -lager.

Sådan køres stikprøvernes:

Åbn filen **samples/simulated_device_cloud_upload/src/simulated_device_cloud_upload_lin.json** i din lokale kopi af **azure-iot-gateway-sdk** lager i et tekstredigeringsprogram. Denne fil konfigurerer modulerne i gatewayen eksempel:

- Modulet **IoTHub** opretter forbindelse til din IoT hub. Du skal konfigurere den for at sende data til din IoT hub. Nærmere betegnet kan værdien **IoTHubName** til navnet på din IoT-hub, og værdien **IoTHubSuffix** til **azure-devices.net**. Indstille værdien **Transport** til en af: "HTTP", "AMQP" eller "MQTT". Vær opmærksom på, på nuværende tidspunkt kun "HTTP" deler én TCP-forbindelse for alle enhed meddelelser. Hvis du angiver værdien til "AMQP" eller "MQTT", bevares gatewayen en separat TCP-forbindelse til IoT Hub for hver enhed.
- Modulet **tilknytning** knytter MAC-adresserne til dine simuleret enheder til dine IoT Hub enhed id'er. Sørg for, **deviceId** værdierne svarer til id'erne for de to enheder, du har føjet til din IoT-hub, og at **deviceKey** værdierne indeholder de pågældende taster af to enheder.
- **BLE1** og **BLE2** moduler er simuleret enheder. Bemærk, hvordan deres MAC-adresser svarer til dem i modulet **tilknytning** .
- Modulet **logføring** registrerer din gateway aktivitet til en fil.
- **Modul sti** værdierne vises under forudsætter, at du udfører stikprøvernes i roden af din lokale kopi af **azure-iot-gateway-sdk** -lager.
- **BLE1** og **BLE2** moduler matrixen **links** i bunden af filen JSON opretter forbindelse til modulet **tilknytning** og modulet **tilknytning** til modulet **IoTHub** . Det sikrer også, at alle meddelelser, der er logget af modulet **logføring** .

```
{
    "modules" :
    [ 
        {
            "module name" : "IoTHub",
            "module path" : "./build/modules/iothub/libiothub_hl.so",
            "args" : 
            {
                "IoTHubName" : "{Your IoT hub name}",
                "IoTHubSuffix" : "azure-devices.net",
                "Transport": "HTTP"
            }
        },
        {
            "module name" : "mapping",
            "module path" : "./build/modules/identitymap/libidentity_map_hl.so",
            "args" : 
            [
                {
                    "macAddress" : "01-01-01-01-01-01",
                    "deviceId"   : "{Device ID 1}",
                    "deviceKey"  : "{Device key 1}"
                },
                {
                    "macAddress" : "02-02-02-02-02-02",
                    "deviceId"   : "{Device ID 2}",
                    "deviceKey"  : "{Device key 2}"
                }
            ]
        },
        {
            "module name":"BLE1",
            "module path" : "./build/modules/simulated_device/libsimulated_device_hl.so",
            "args":
            {
                "macAddress" : "01-01-01-01-01-01"
            }
        },
        {
            "module name":"BLE2",
            "module path" : "./build/modules/simulated_device/libsimulated_device_hl.so",
            "args":
            {
                "macAddress" : "02-02-02-02-02-02"
            }
        },
        {
            "module name":"Logger",
            "module path" : "./build/modules/logger/liblogger_hl.so",
            "args":
            {
                "filename":"./deviceCloudUploadGatewaylog.log"
            }
        }
    ],
    "links" : [
        { "source" : "*", "sink" : "Logger" },
        { "source" : "BLE1", "sink" : "mapping" },
        { "source" : "BLE2", "sink" : "mapping" },
        { "source" : "mapping", "sink" : "IoTHub" }
    ]
}

```

Gemme de ændringer, du har foretaget konfigurationsfilen.

Sådan køres stikprøvernes:

1. I din shell, skal du gå til rodmappen i din lokale kopi af **azure-iot-gateway-sdk** -lager.
2. Kør følgende kommando:

    ```
    ./build/samples/simulated_device_cloud_upload/simulated_device_cloud_upload_sample ./samples/simulated_device_cloud_upload/src/simulated_device_cloud_upload_lin.json
    ```

3. Du kan bruge [enhed Stifinder eller iothub explorer] [ lnk-explorer-tools] værktøj til at overvåge de meddelelser, der IoT hub modtager fra en gateway.

## <a name="next-steps"></a>Næste trin

Hvis du vil opnå en mere avanceret forståelse af IoT Gateway SDK og eksperimentere med nogle kodeeksempler, skal du besøge følgende udvikler selvstudier og ressourcer:

- [Sende enhed i skyen meddelelser fra en reelle enhed med IoT Gateway SDK][lnk-physical-device]
- [Azure IoT Gateway SDK][lnk-gateway-sdk]

Hvis du vil udforske yderligere funktionerne i IoT-Hub, i:

- [Udvikler vejledning][lnk-devguide]
- [Secure din IoT løsning fra bunden op][lnk-securing]

<!-- Links -->
[lnk-setupdevbox]: https://github.com/Azure/azure-iot-gateway-sdk/blob/master/doc/devbox_setup.md
[lnk-free-trial]: https://azure.microsoft.com/pricing/free-trial/
[lnk-explorer-tools]: https://github.com/Azure/azure-iot-sdks/blob/master/doc/manage_iot_hub.md
[lnk-gateway-sdk]: https://github.com/Azure/azure-iot-gateway-sdk/

[lnk-physical-device]: iot-hub-gateway-sdk-physical-device.md

[lnk-devguide]: iot-hub-devguide.md
[lnk-securing]: iot-hub-security-ground-up.md
[lnk-create-hub]: iot-hub-create-through-portal.md