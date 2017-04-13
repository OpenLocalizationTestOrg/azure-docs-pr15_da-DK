<properties
    pageTitle="Oprette en IoT Hub ved hjælp af Azure CLI | Microsoft Azure"
    description="Følg denne artikel for at oprette en IoT Hub bruger Azure kommandolinjen."
    services="iot-hub"
    documentationCenter=".net"
    authors="BeatriceOltean"
    manager="timlt"
    editor=""/>

<tags
     ms.service="iot-hub"
     ms.devlang="multiple"
     ms.topic="article"
     ms.tgt_pltfrm="na"
     ms.workload="na"
     ms.date="09/21/2016"
     ms.author="boltean"/>

# <a name="create-an-iot-hub-using-azure-cli"></a>Oprette en IoT Hub ved hjælp af Azure CLI

[AZURE.INCLUDE [iot-hub-resource-manager-selector](../../includes/iot-hub-resource-manager-selector.md)]

## <a name="introduction"></a>Introduktion

Du kan bruge Azure kommandolinjen til at oprette og administrere Azure IoT hubs fra et program. I denne artikel viser, hvordan du bruger Azure CLI til at oprette en IoT Hub.

For at fuldføre dette selvstudium skal du følgende:

- En active Azure-konto. Hvis du ikke har en konto, kan du oprette en [gratis konto] [ lnk-free-trial] på blot et par minutter.
- [Azure CLI 0.10.4] [ lnk-CLI-install] eller nyere. Hvis du allerede har Azure CLI kan du validerer den aktuelle version kommandoprompten med følgende kommando:
```
    azure --version
```

> [AZURE.NOTE] Azure har to forskellige installation modeller til oprettelse og arbejde med ressourcer: [Azure ressourcestyring og klassisk](../resource-manager-deployment-model.md). Azure CLI skal være i Azure ressourcestyring tilstand:
```
    azure config mode arm
```

## <a name="set-your-azure-account-and-subscription"></a>Angive Azure-konto og abonnement 

1. Ved kommandoprompten logon ved at skrive følgende kommando
```
    azure login
```
Brug af foreslåede webbrowser og kode for at godkende.

2. Hvis du har flere Azure abonnementer, vil oprette forbindelse til Azure give adgang til alle Azure abonnementer, der er knyttet til dine legitimationsoplysninger. Du kan få vist de Azure abonnementer, samt hvilke en er standard, ved hjælp af kommandoen
```
    azure account list 
```

Angive den abonnement kontekst, hvor du vil køre resten af brug kommandoer

```
    azure account set <subscription name>
```

3. Hvis du ikke har en ressourcegruppe kan du oprette en navngivet **exampleResourceGroup** 
```
    azure group create -n exampleResourceGroup -l westus
```

> [AZURE.TIP] I artiklen [Brug Azure CLI for at administrere Azure ressourcer og ressourcegrupper] [ lnk-CLI-arm] indeholder flere oplysninger om, hvordan du bruger Azure CLI til at administrere Azure ressourcer. 


## <a name="create-an-iot-hub"></a>Oprette en IoT-Hub

Obligatoriske parametre:

```
 azure iothub create -g <resource-group> -n <name> -l <location> -s <sku-name> -u <units>  
    - <resourceGroup> The resource group name (case insensitive alphanumeric, underscore and hyphen, 1-64 length)
    - <name> (The name of the IoT hub to be created. The format is case insensitive alphanumeric, underscore and hyphen, 3-50 length )
    - <location> (The location (azure region/datacenter) where the IoT hub will be provisioned.
    - <sku-name> (The name of the sku, one of: [F1, S1, S2, S3] etc. For the latest full list refer to the pricing page for IoT Hub.
    - <units> (The number of provisioned units. Range : F1 [1-1] : S1, S2 [1-200] : S3 [1-10]. IoT Hub units are based on your total message count and the number of devices you want to connect.)
```
Hvis du vil se alle parametre tilgængelig for oprettelse af du kan bruge kommandoen Hjælp i kommandoprompten
```
    azure iothub create -h 
```
Hurtig eksempel:

 Hvis du vil oprette en IoT Hub kaldet **exampleIoTHubName** i ressource gruppe **exampleResourceGroup** skal du bare køre følgende kommando
```
    azure iothub create -g exampleResourceGroup -n exampleIoTHubName -l westus -k s1 -u 1
```

> [AZURE.NOTE] Denne kommando Azure CLI opretter en S1 Standard IoT-Hub, du er faktureret. Du kan slette IoT hub **exampleIoTHubName** hjælp af følgende kommando 
```
    azure iothub delete -g exampleResourceGroup -n exampleIoTHubName
```


## <a name="next-steps"></a>Næste trin
Hvis du vil vide mere om udvikling til IoT-Hub, skal du se følgende:
- [IoT Hub SDK'er][lnk-sdks]

Hvis du vil udforske yderligere funktionerne i IoT-Hub, i:

- [Ved hjælp af portalen Azure til at administrere IoT Hub][lnk-portal]

<!-- Links -->
[lnk-free-trial]: https://azure.microsoft.com/pricing/free-trial/
[lnk-azure-portal]: https://portal.azure.com/
[lnk-status]: https://azure.microsoft.com/status/
[lnk-CLI-install]: ../xplat-cli-install.md
[lnk-rest-api]: https://msdn.microsoft.com/library/mt589014.aspx
[lnk-CLI-arm]: ../xplat-cli-azure-resource-manager.md

[lnk-sdks]: iot-hub-devguide-sdks.md
[lnk-portal]: iot-hub-create-through-portal.md 
