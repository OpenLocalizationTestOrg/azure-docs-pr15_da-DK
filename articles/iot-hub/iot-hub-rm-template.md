<properties
    pageTitle="Oprette en IoT Hub ved hjælp af en ARM skabelon og C# | Microsoft Azure"
    description="Følge dette selvstudium for at komme i gang ved hjælp af Azure ressourcestyring skabeloner til at oprette en IoT Hub med et C#-program."
    services="iot-hub"
    documentationCenter=".net"
    authors="dominicbetts"
    manager="timlt"
    editor=""/>

<tags
     ms.service="iot-hub"
     ms.devlang="dotnet"
     ms.topic="article"
     ms.tgt_pltfrm="na"
     ms.workload="na"
     ms.date="08/16/2016"
     ms.author="dobett"/>

# <a name="create-an-iot-hub-using-a-c-program-with-an-azure-resource-manager-template"></a>Oprette en IoT hub ved hjælp af et C#-program med en skabelon til Azure ressourcestyring

[AZURE.INCLUDE [iot-hub-resource-manager-selector](../../includes/iot-hub-resource-manager-selector.md)]

## <a name="introduction"></a>Introduktion

Du kan bruge Azure ressourcestyring til at oprette og administrere Azure IoT hubs fra et program. Dette selvstudium viser, hvordan du bruge en skabelon til Azure ressourcestyring til at oprette en IoT hub fra et C#-program.

> [AZURE.NOTE] Azure har to forskellige installation modeller til oprettelse og arbejde med ressourcer: [Azure ressourcestyring og klassisk](../resource-manager-deployment-model.md).  Denne artikel omhandler ved hjælp af implementeringsmodel Azure ressourcestyring.

For at fuldføre dette selvstudium skal bruge du følgende:

- Microsoft Visual Studio 2015.
- En active Azure-konto. <br/>Hvis du ikke har en konto, kan du oprette en [gratis konto] [ lnk-free-trial] på blot et par minutter.
- En [Azure-lager konto] [ lnk-storage-account] hvor du kan gemme dine Azure ressourcestyring skabelonfiler.
- [Microsoft Azure PowerShell 1.0] [ lnk-powershell-install] eller nyere.

[AZURE.INCLUDE [iot-hub-prepare-resource-manager](../../includes/iot-hub-prepare-resource-manager.md)]

## <a name="prepare-your-visual-studio-project"></a>Forberede din Visual Studio-projekt

1. Oprette en visuel C# Windows-projekt, ved hjælp af projektskabelon **Console-program** i Visual Studio. Navngive projektet **CreateIoTHub**.

2. I Solution Explorer, skal du højreklikke på dit projekt, og klik derefter på **Administrer NuGet pakker**.

3. I Nuget pakke Manager Kontroller **medtage foreløbig version**, og Søg efter **Microsoft.Azure.Management.ResourceManager**. Klik på **Installer**, klik på **OK**i **Gennemse ændringer** og derefter klikke på **jeg accepterer** for at acceptere licenserne.

4. I Nuget pakke Manager skal du søge efter **Microsoft.IdentityModel.Clients.ActiveDirectory**.  Klik på **Installer**, klik på **OK**i **Gennemse ændringer** og derefter klikke på **jeg accepterer** for at acceptere licensen.

5. Erstat de eksisterende **ved hjælp af** sætninger i Program.cs, med følgende:

    ```
    using System;
    using Microsoft.Azure.Management.ResourceManager;
    using Microsoft.Azure.Management.ResourceManager.Models;
    using Microsoft.IdentityModel.Clients.ActiveDirectory;
    using Microsoft.Rest;
    ```
    
6. Tilføj følgende statisk variabler erstatter pladsholder værdierne i Program.cs. Du har lavet en note af **ApplicationId**, **SubscriptionId**, **TenantId**og **adgangskode** tidligere i dette selvstudium. **Din Azure-lager kontonavn** er navnet på kontoen, Azure-lager, hvor du gemmer dine Azure ressourcestyring skabelonfiler. **Ressource gruppenavn** er navnet på den ressourcegruppe, du bruger, når du opretter IoT-Hub, kan det være en eksisterende ressourcegruppe eller et nyt. **Installation navn** er et navn til installationen, som **Deployment_01**.

    ```
    static string applicationId = "{Your ApplicationId}";
    static string subscriptionId = "{Your SubscriptionId}";
    static string tenantId = "{Your TenantId}";
    static string password = "{Your application Password}";
    static string storageAddress = "https://{Your storage account name}.blob.core.windows.net";
    static string rgName = "{Resource group name}";
    static string deploymentName = "{Deployment name}";
    ```

[AZURE.INCLUDE [iot-hub-get-access-token](../../includes/iot-hub-get-access-token.md)]

## <a name="submit-an-azure-resource-manager-template-to-create-an-iot-hub"></a>Sende en Azure ressourcestyring skabelon for at oprette en IoT-hub

Bruge en JSON skabelon og parameter fil til at oprette en IoT hub i din ressourcegruppe. Du kan også bruge en skabelon til Azure ressourcestyring til at foretage ændringer til en eksisterende IoT-hub.

1. Højreklik på dit projekt i Solution Explorer, skal du klikke på **Tilføj**, og klik derefter på **Nyt element**. Tilføje en JSON fil med navnet **template.json** til projektet.

2. Erstatte indholdet af **template.json** med følgende ressource definitionen til at føje en standard IoT-hub til **Indtastning af østasiatiske USA** område (se [Azure Status]for den aktuelle liste over områder, der understøtter IoT Hub[lnk-status]):

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

3. Højreklik på dit projekt i Solution Explorer, skal du klikke på **Tilføj**, og klik derefter på **Nyt element**. Tilføje en JSON fil med navnet **parameters.json** til projektet.

4. Erstatte indholdet af **parameters.json** med følgende parameteroplysninger, der angiver et navn til den nye IoT hub såsom **{dine initialer} mynewiothub**. Bemærk, at IoT hub navnet skal være globalt entydige, så den skal indeholde dit navn og initialer):

    ```
    {
      "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
      "contentVersion": "1.0.0.0",
      "parameters": {
        "hubName": { "value": "mynewiothub" }
      }
    }
    ```

5. Oprette forbindelse til abonnementet Azure i **Server Explorer**, og Opret en objektbeholder, kaldet **skabeloner**i kontoen Azure-lager. Angiv de **offentlige læseadgang** tilladelser for objektbeholderen **skabeloner** til **Blob**i panelet **Egenskaber** .

6. Højreklik på objektbeholderen **skabeloner** i **Server Explorer**, og klik derefter på **Vis Blob objektbeholder**. Klik på knappen **Overfør Blob** , vælge de to filer, **parameters.json** og **templates.json**, og klik derefter på **Åbn** for at uploade JSON-filer til objektbeholderen **skabeloner** . URL-adresserne på de blob, der indeholder JSON dataene er:

    ```
    https://{Your storage account name}.blob.core.windows.net/templates/parameters.json
    https://{Your storage account name}.blob.core.windows.net/templates/template.json
    ```

7. Føje følgende metode til Program.cs:
    
    ```
    static void CreateIoTHub(ResourceManagementClient client)
    {
        
    }
    ```

5. Føj følgende kode til **CreateIoTHub** metoden til at sende skabelonen og parameter filerne til Azure ressourcestyring:

    ```
    var createResponse = client.Deployments.CreateOrUpdate(
        rgName,
        deploymentName,
        new Deployment()
        {
          Properties = new DeploymentProperties
          {
            Mode = DeploymentMode.Incremental,
            TemplateLink = new TemplateLink
            {
              Uri = storageAddress + "/templates/template.json"
            },
            ParametersLink = new ParametersLink
            {
              Uri = storageAddress + "/templates/parameters.json"
            }
          }
        });
    ```

6. Føj følgende kode til metoden **CreateIoTHub** , der viser status og taster til den nye IoT hub:

    ```
    string state = createResponse.Properties.ProvisioningState;
    Console.WriteLine("Deployment state: {0}", state);

    if (state != "Succeeded")
    {
      Console.WriteLine("Failed to create iothub");
    }
    Console.WriteLine(createResponse.Properties.Outputs);
    ```

## <a name="complete-and-run-the-application"></a>Udføre og køre programmet

Du kan nu udføre programmet ved at ringe metoden **CreateIoTHub** , før du opretter og kører den.

1. Føj følgende kode til slutningen af metoden **Main** :

    ```
    CreateIoTHub(client);
    Console.ReadLine();
    ```
    
2. Klik på **Opret** og derefter **opbygge løsning**. Ret eventuelle fejl.

3. Klik på **fejlfinding** og derefter på **Start fejlfinding** for at køre programmet. Det kan tage flere minutter, før installationen til at køre.

4. Du kan kontrollere, at dit program tilføjet nye IoT hubben ved at gå til [Azure portal] [ lnk-azure-portal] og få vist listen over ressourcer eller ved hjælp af **Get-AzureRmResource** PowerShell-cmdlet.

> [AZURE.NOTE] Dette eksempel program tilføjer en S1 Standard IoT-Hub, du er faktureret. Du kan slette IoT hubben via [Azure portal] [ lnk-azure-portal] eller ved at bruge **Fjern AzureRmResource** PowerShell-cmdlet, når du er færdig.

## <a name="next-steps"></a>Næste trin

Nu du har installeret en IoT hub ved hjælp af en skabelon til Azure ressourcestyring med et C#-program, kan du læse videre:

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
[lnk-storage-account]: ../storage/storage-create-storage-account.md

[lnk-c-sdk]: iot-hub-device-sdk-c-intro.md
[lnk-sdks]: iot-hub-devguide-sdks.md

[lnk-gateway]: iot-hub-linux-gateway-sdk-simulated-device.md
