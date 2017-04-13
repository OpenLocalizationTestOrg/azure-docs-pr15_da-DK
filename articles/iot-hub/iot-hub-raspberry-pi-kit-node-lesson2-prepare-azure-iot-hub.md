<properties
 pageTitle="Oprette din IoT hub og registrere din hindbær Pi 3 | Microsoft Azure"
 description="Oprette en ressourcegruppe, oprette en Azure IoT hub og registrere din Pi i Azure IoT hubben ved hjælp af Azure CLI."
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

# <a name="22-create-your-iot-hub-and-register-your-raspberry-pi-3"></a>2.2 oprette IoT centrum og registrere din hindbær Pi 3

## <a name="221-what-you-will-do"></a>2.2.1 hvad du vil gøre

- Oprette en ressourcegruppe.
- Oprette din Azure IoT hub i ressourcegruppen.
- Tilføj din hindbær Pi 3 til Azure IoT hubben ved hjælp af Azure CLI.

Når du bruger Azure CLI føje din Pi til din IoT hub, genererer tjenesten en nøgle til din Pi til at godkende med tjenesten. Hvis du opfylder problemer, kan du søge efter løsninger i [foretage fejlfinding af siden](iot-hub-raspberry-pi-kit-node-troubleshooting.md).

## <a name="222-what-you-will-learn"></a>2.2.2 hvad du lærer

- Sådan bruges Azure CLI til at oprette en Azure IoT Hub.
- Sådan oprettes en enhed identitet for din Pi i din Azure IoT Hub.

## <a name="223-what-you-need"></a>2.2.3 hvad du bør

- En Azure-konto
- En Mac eller en Windows-computer med den Azure CLI, der er installeret

## <a name="224-create-your-azure-iot-hub"></a>2.2.4 oprette Azure IoT-hub

Azure IoT Hub hjælper dig med at oprette forbindelse, overvåge og administrere millioner af IoT aktiver. Hvis du vil oprette Azure IoT-hub, skal du følge disse trin:

1. Log på din Azure-konto ved at køre følgende kommando:

    ```bash
    az login
    ```

    Alle dine tilgængelige Azure abonnementer er angivet efter en vellykket logon.

2. Angiv standardsproget til Azure-abonnement, som du vil bruge ved at køre følgende kommando:

    ```bash
    az account set -n {subscription id or name}
    ```

    Abonnement-ID eller navn kan findes i outputtet for `az login`.

3. Registrere provideren ved at køre følgende kommando:

    ```bash
    az resource provider register -n "Microsoft.Devices"
    ```

    Før du kan installere den Azure ressource, der indeholder udbyderen, skal du registrere udbyderen.

    > [AZURE.NOTE] De fleste udbydere er registreret automatisk af portalen Azure eller den Azure CLI, du bruger, men ikke alle. Du kan finde flere oplysninger om udbyderen, se [fejlfinding af almindelige Azure-installationsfejl med Azure ressourcestyring](../resource-manager-common-deployment-errors.md)

4. Oprette en ressourcegruppe med navnet iot stikprøver i Vest USA område ved at køre følgende kommando:

    ```bash
    az resource group create --name iot-sample --location westus
    ```

5. Oprette en IoT hub i gruppen iot stikprøver ressource ved at køre følgende kommando:

    ```bash
    az iot hub create --name {my hub name} --resource-group iot-sample
    ```

    Standard udgaven af IoT hubben du opretter er **F1**, hvilket er **gratis**. Se [Azure IoT Hub priser](https://azure.microsoft.com/pricing/details/iot-hub/)kan finde flere oplysninger.

> [AZURE.NOTE] Navnet på din IoT-hub skal være globalt entydige.
>
> Du kan oprette kun én **F1** -udgave af Azure IoT Hub under abonnementet Azure.

## <a name="225-register-your-pi-in-your-iot-hub"></a>2.2.5 registrere din Pi i IoT-hub

Hver enhed, der sender/modtager meddelelser til/fra Azure IoT-hub skal være registreret med et entydigt ID.

Registrere din Pi i Azure IoT centrum ved at køre følgende kommando:

```bash
az iot device create --device-id myraspberrypi --hub {my hub name} --resource-group iot-sample
```

## <a name="226-summary"></a>2.2.6 oversigt

Du har oprettet en Azure IoT hub og registreret din Pi med en enhed identitet i Azure IoT-hubben. Du er klar til at gå videre til næste lektion lære at sende meddelelser fra din Pi til IoT centrum.

## <a name="next-steps"></a>Næste trin

Nu er du klar til at starte lektion 3, der begynder med at [oprette en Azure funktionen app og en Azure-lager-konto til at behandle og gemme IoT hub meddelelser](iot-hub-raspberry-pi-kit-node-lesson3-deploy-resource-manager-template.md).