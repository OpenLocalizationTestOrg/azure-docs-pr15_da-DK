<properties
 pageTitle="Oprette en Azure funktionen og Azure-lager konto | Microsoft Azure"
 description="Azure funktionen app lytter til Azure IoT hub begivenheder, behandler indgående meddelelser og skriver dem til Azure-tabellager."
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

# <a name="31-create-an-azure-function-app-and-azure-storage-account"></a>3.1 oprette en Azure funktionen og Azure-lager-konto

[Azure-funktioner](../../articles/azure-functions/functions-overview.md) er en løsning til nemt kører små kode, kaldet "funktioner" i skyen. En Azure funktionen app vært udførelse af funktioner i Azure.

## <a name="311-what-will-you-do"></a>3.1.1 Hvad vil du gøre

Bruge en skabelon til Azure ressourcestyring til at oprette en Azure funktionen app og en Azure-lager-konto. Azure funktionen app lytter til Azure IoT hub begivenheder, behandler indgående meddelelser og skriver dem til Azure-tabellager. Hvis du opfylder problemer, kan du søge efter løsninger i [foretage fejlfinding af siden](iot-hub-raspberry-pi-kit-node-troubleshooting.md).

## <a name="312-what-will-you-learn"></a>3.1.2 Hvad vil du lære

- Sådan bruges [Azure ressourcestyring](../../articles/azure-resource-manager/resource-group-overview.md) til at implementere Azure ressourcer.
- Sådan bruges en Azure funktionen app til at behandle IoT hub meddelelser og skrive dem i en tabel i Azure-tabellager.

## <a name="313-what-do-you-need"></a>3.1.3 Hvad har du brug for

- Du skal have gennemført de forrige lektioner: [Kom i gang med din hindbær Pi 3](iot-hub-raspberry-pi-kit-node-get-started.md) og [oprette Azure IoT centrum](iot-hub-raspberry-pi-kit-node-get-started.md).

## <a name="314-open-the-sample-app"></a>3.1.4 åbne appen med eksempel

Åbne eksempelprojektet i Visual Studio-kode ved at køre følgende kommandoer:

```bash
cd Lesson3
code .
```

![Repo struktur](media/iot-hub-raspberry-pi-lessons/lesson3/repo_structure.png)

- Den `app.js` filer i den `app` undermappe er vigtige kildefilen. Denne kildefilen indeholder koden for at sende en meddelelse 20 gange til din IoT hub og blinke LED for hver meddelelse sendes.
- Den `arm-template.json` filen er skabelonen Azure Ressourcestyring, der indeholder en Azure funktionen app og en Azure-lager-konto.
- Den `arm-template-param.json` fil er den konfigurationsfil, der bruges af skabelonen Azure ressourcestyring.
- Den `ReceiveDeviceMessages` undermappe indeholder Node.js koden for funktionen Azure.

## <a name="315-configure-azure-resource-manager-templates-and-create-resources-in-azure"></a>3.1.5 konfigurere Azure ressourcestyring skabeloner og oprette ressourcer i Azure

Opdater den `arm-template-param.json` fil i Visual Studio-kode.

![Azure ressourcestyring skabelonparametre](media/iot-hub-raspberry-pi-lessons/lesson3/arm_para.png)

- Erstat **[dit IoT Hub navn]** med **{Min hub name}** , du angav i [lektion 2](iot-hub-raspberry-pi-kit-node-lesson2-prepare-azure-iot-hub.md).
- Erstat **[præfiks streng for nye ressourcer]** med et præfiks, du vil. Præfikset sikrer, at ressourcenavnet globalt entydige for at undgå konflikt. Brug ikke tankestreg eller tal indledende i præfikset.

> [AZURE.NOTE] Du behøver ikke `azure_storage_connection_string` i dette afsnit. Organisere dem som den er.

Når du opdaterer den `arm-template-param.json` fil ved at anvende ressourcerne til Azure ved at køre følgende kommando:

```bash
az resource group deployment create --template-file-path arm-template.json --parameters-file-path arm-template-param.json -g iot-sample -n mydeployment
```

Det tager om fem minutter til at oprette disse ressourcer. Mens oprettelsen af ressource er i gang, kan du gå videre til næste afsnit.

## <a name="316-summary"></a>3.1.6 oversigt

Du har oprettet din Azure funktionen app for at behandle IoT hub meddelelser og en Azure-lager-konto for at gemme disse meddelelser. Du kan flytte til næste afsnit for at installere og køre stikprøvernes at sende enhed i skyen meddelelser på din Pi.

## <a name="next-steps"></a>Næste trin

[3.2 køre Northwind at sende enhed i skyen meddelelser på din hindbær Pi 3](iot-hub-raspberry-pi-kit-node-lesson3-run-azure-blink.md)

