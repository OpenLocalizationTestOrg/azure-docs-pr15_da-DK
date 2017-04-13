<properties
    pageTitle="Oprette en IoT Hub ved hjælp af en Azure ressourcestyring skabelon og PowerShell | Microsoft Azure"
    description="Følge dette selvstudium for at komme i gang ved hjælp af Azure ressourcestyring skabeloner til at oprette en IoT Hub med PowerShell."
    services="iot-hub"
    documentationCenter=".net"
    authors="dominicbetts"
    manager="timlt"
    editor=""/>

<tags
     ms.service="iot-hub"
     ms.devlang="multiple"
     ms.topic="article"
     ms.tgt_pltfrm="na"
     ms.workload="na"
     ms.date="09/07/2016"
     ms.author="dobett"/>

# <a name="create-an-iot-hub-using-powershell"></a>Oprette en IoT hub ved hjælp af PowerShell

[AZURE.INCLUDE [iot-hub-resource-manager-selector](../../includes/iot-hub-resource-manager-selector.md)]

## <a name="introduction"></a>Introduktion

Du kan bruge Azure ressourcestyring til at oprette og administrere Azure IoT hubs fra et program. Dette selvstudium viser, hvordan du bruge en skabelon til Azure ressourcestyring til at oprette en IoT hub med PowerShell.

> [AZURE.NOTE] Azure har to forskellige installation modeller til oprettelse og arbejde med ressourcer: [Azure ressourcestyring og klassisk](../resource-manager-deployment-model.md).  Denne artikel omhandler ved hjælp af implementeringsmodel Azure ressourcestyring.

For at fuldføre dette selvstudium skal du følgende:

- En active Azure-konto. <br/>Hvis du ikke har en konto, kan du oprette en [gratis konto] [ lnk-free-trial] på blot et par minutter.
- [Microsoft Azure PowerShell 1.0] [ lnk-powershell-install] eller nyere.

> [AZURE.TIP] I artiklen [Brug af Azure PowerShell med Azure ressourcestyring] [ lnk-powershell-arm] indeholder flere oplysninger om, hvordan du bruger PowerShell-scripts og Azure ressourcestyring skabeloner til at oprette Azure ressourcer. 

## <a name="connect-to-your-azure-subscription"></a>Oprette forbindelse til abonnementet Azure

Skriv følgende kommando for at logge på abonnementet Azure i en PowerShell-kommandoen prompt:

```
Login-AzureRmAccount
```

Du kan bruge følgende kommandoer til at se, hvor du kan installere en IoT hub og de aktuelt understøttede API versioner:

```
((Get-AzureRmResourceProvider -ProviderNamespace Microsoft.Devices).ResourceTypes | Where-Object ResourceTypeName -eq IoTHubs).Locations
((Get-AzureRmResourceProvider -ProviderNamespace Microsoft.Devices).ResourceTypes | Where-Object ResourceTypeName -eq IoTHubs).ApiVersions
```

Oprette en ressourcegruppe indeholde IoT centrum ved hjælp af følgende kommando i en af de understøttede placeringer til IoT Hub. I dette eksempel oprettes en ressourcegruppe med navnet **MyIoTRG1**:

```
New-AzureRmResourceGroup -Name MyIoTRG1 -Location "East US"
```

## <a name="submit-an-azure-resource-manager-template-to-create-an-iot-hub"></a>Sende en Azure ressourcestyring skabelon for at oprette en IoT-hub

Bruge en JSON-skabelon til at oprette en IoT hub i din ressourcegruppe. Du kan også bruge en skabelon til Azure ressourcestyring til at foretage ændringer til en eksisterende IoT-hub.

1. Brug en teksteditor til at oprette en skabelon til Azure ressourcestyring kaldet **template.json** med følgende ressource definitionen til at oprette en ny standard IoT-hub. I dette eksempel tilføjer IoT hubben i området til **Indtastning af østasiatiske USA** , opretter to consumer grupper (**cg1** og **cg2**) på begivenheden Hub-kompatible slutpunktet og bruger **2016-02-03** API-version. Denne skabelon have dig til at overføre i IoT hub navn som en parameter ved navn **hubName**. Se [Azure Status]for den aktuelle liste over placeringer, der understøtter IoT Hub[lnk-status].

    ```
    {
      "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
      "contentVersion": "1.0.0.0",
      "parameters": {
        "hubName": {
          "type": "string"
        }
      },
      "resources": [
      {
        "apiVersion": "2016-02-03",
        "type": "Microsoft.Devices/IotHubs",
        "name": "[parameters('hubName')]",
        "location": "East US",
        "sku": {
          "name": "S1",
          "tier": "Standard",
          "capacity": 1
        },
        "properties": {
          "location": "East US"
        }
      },
      {
        "apiVersion": "2016-02-03",
        "type": "Microsoft.Devices/IotHubs/eventhubEndpoints/ConsumerGroups",
        "name": "[concat(parameters('hubName'), '/events/cg1')]",
        "dependsOn": [
          "[concat('Microsoft.Devices/Iothubs/', parameters('hubName'))]"
        ]
      },
      {
        "apiVersion": "2016-02-03",
        "type": "Microsoft.Devices/IotHubs/eventhubEndpoints/ConsumerGroups",
        "name": "[concat(parameters('hubName'), '/events/cg2')]",
        "dependsOn": [
          "[concat('Microsoft.Devices/Iothubs/', parameters('hubName'))]"
        ]
      }
      ],
      "outputs": {
        "hubKeys": {
          "value": "[listKeys(resourceId('Microsoft.Devices/IotHubs', parameters('hubName')), '2016-02-03')]",
          "type": "object"
        }
      }
    }
    ```

2. Gem skabelonfilen Azure Ressourcestyring på din lokale computer. I dette eksempel antages det, du gemmer den i en mappe med navnet **c:\templates**.

3. Kør følgende kommando for at installere dit nye IoT-hub, der passerer navnet på din IoT hub som en parameter. I dette eksempel er navnet på IoT hub **abcmyiothub** (Bemærk, at dette navn skal være globalt entydige, så den skal indeholde dit navn og initialer):

    ```
    New-AzureRmResourceGroupDeployment -ResourceGroupName MyIoTRG1 -TemplateFile C:\templates\template.json -hubName abcmyiothub
    ```

4. Output viser taster til IoT hubben du har oprettet.

5. Du kan kontrollere, at dit program tilføjet nye IoT hubben ved at gå til [Azure portal] [ lnk-azure-portal] og få vist listen over ressourcer eller ved hjælp af **Get-AzureRmResource** PowerShell-cmdlet.

> [AZURE.NOTE] Dette eksempel program tilføjer en S1 Standard IoT-Hub, du er faktureret. Du kan slette IoT hubben via [Azure portal] [ lnk-azure-portal] eller ved at bruge **Fjern AzureRmResource** PowerShell-cmdlet, når du er færdig.

## <a name="next-steps"></a>Næste trin

Nu du har installeret en IoT hub ved hjælp af en skabelon til Azure ressourcestyring med PowerShell, kan du læse videre:

- Læs om funktionerne i [IoT Hub ressource udbyder REST-API][lnk-rest-api].
- Læs [Azure ressourcestyring oversigt] [ lnk-azure-rm-overview] mere at vide om funktionerne i Azure ressource Manager.

Hvis du vil vide mere om udvikling til IoT Hub skal du se følgende:

- [Introduktion til C SDK][lnk-c-sdk]
- [IoT Hub SDK'er][lnk-sdks]

Hvis du vil udforske yderligere funktionerne i IoT-Hub, i:

- [Simulere en enhed med IoT Gateway SDK][lnk-gateway]

<!-- Links -->
[lnk-free-trial]: https://azure.microsoft.com/pricing/free-trial/
[lnk-azure-portal]: https://portal.azure.com/
[lnk-status]: https://azure.microsoft.com/status/
[lnk-powershell-install]: ../powershell-install-configure.md
[lnk-rest-api]: https://msdn.microsoft.com/library/mt589014.aspx
[lnk-azure-rm-overview]: ../azure-resource-manager/resource-group-overview.md
[lnk-powershell-arm]: ../powershell-azure-resource-manager.md

[lnk-c-sdk]: iot-hub-device-sdk-c-intro.md
[lnk-sdks]: iot-hub-devguide-sdks.md

[lnk-gateway]: iot-hub-linux-gateway-sdk-simulated-device.md
