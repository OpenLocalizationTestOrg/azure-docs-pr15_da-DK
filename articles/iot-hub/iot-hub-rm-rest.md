<properties
    pageTitle="Oprette en IoT Hub ved hjælp af REST-API | Microsoft Azure"
    description="Følge dette selvstudium for at komme i gang ved hjælp af REST-API til at oprette en IoT Hub."
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

# <a name="tutorial-create-an-iot-hub-using-a-c-program-and-the-rest-api"></a>Selvstudium: Oprette en IoT hub ved hjælp af et C#-program og REST-API

[AZURE.INCLUDE [iot-hub-resource-manager-selector](../../includes/iot-hub-resource-manager-selector.md)]

## <a name="introduction"></a>Introduktion

Du kan bruge [IoT Hub ressource udbyder REST-API] [ lnk-rest-api] til at oprette og administrere Azure IoT hubs fra et program. Dette selvstudium viser, hvordan du bruger IoT Hub ressource udbyder REST-API til at oprette en IoT hub fra et C#-program.

> [AZURE.NOTE] Azure har to forskellige installation modeller til oprettelse og arbejde med ressourcer: [Azure ressourcestyring og klassisk](../resource-manager-deployment-model.md).  Denne artikel omhandler ved hjælp af implementeringsmodel Azure ressourcestyring.

For at fuldføre dette selvstudium skal bruge du følgende:

- Microsoft Visual Studio 2015.
- En active Azure-konto. <br/>Hvis du ikke har en konto, kan du oprette en [gratis konto] [ lnk-free-trial] på blot et par minutter.
- [Microsoft Azure PowerShell 1.0] [ lnk-powershell-install] eller nyere.

[AZURE.INCLUDE [iot-hub-prepare-resource-manager](../../includes/iot-hub-prepare-resource-manager.md)]

## <a name="prepare-your-visual-studio-project"></a>Forberede din Visual Studio-projekt

1. Oprette en visuel C# Windows-projekt, ved hjælp af projektskabelon **Console-program** i Visual Studio. Navngive projektet **CreateIoTHubREST**.

2. I Solution Explorer, skal du højreklikke på dit projekt, og klik derefter på **Administrer NuGet pakker**.

3. I Nuget pakke Manager Kontroller **medtage foreløbig version**, og Søg efter **Microsoft.Azure.Management.ResourceManager**. Klik på **Installer**, klik på **OK**i **Gennemse ændringer** og derefter klikke på **jeg accepterer** for at acceptere licenserne.

4. I Nuget pakke Manager skal du søge efter **Microsoft.IdentityModel.Clients.ActiveDirectory**.  Klik på **Installer**, klik på **OK**i **Gennemse ændringer** og derefter klikke på **jeg accepterer** for at acceptere licensen.

6. Erstat de eksisterende **ved hjælp af** sætninger i Program.cs, med følgende:

    ```
    using System;
    using System.Net.Http;
    using System.Net.Http.Headers;
    using System.Text;
    using Microsoft.Azure.Management.ResourceManager;
    using Microsoft.Azure.Management.ResourceManager.Models;
    using Microsoft.IdentityModel.Clients.ActiveDirectory;
    using Newtonsoft.Json;
    using Microsoft.Rest;
    using System.Linq;
    using System.Threading;
    ```
    
7. Tilføj følgende statisk variabler erstatter pladsholder værdierne i Program.cs. Du har lavet en note af **ApplicationId**, **SubscriptionId**, **TenantId**og **adgangskode** tidligere i dette selvstudium. **Ressource gruppenavn** er navnet på den ressourcegruppe, du bruger, når du opretter IoT-hub, kan det være en eksisterende ressourcegruppe eller et nyt. **IoT Hub navn** er navnet på den IoT-Hub, du opretter, såsom **MyIoTHub** (dette navn skal være globalt entydige, så den skal indeholde dit navn og initialer). **Installation navn** er et navn til installationen, som **Deployment_01**.

    ```
    static string applicationId = "{Your ApplicationId}";
    static string subscriptionId = "{Your SubscriptionId}";
    static string tenantId = "{Your TenantId}";
    static string password = "{Your application Password}";
    
    static string rgName = "{Resource group name}";
    static string iotHubName = "{IoT Hub name including your initials}";
    ```

[AZURE.INCLUDE [iot-hub-get-access-token](../../includes/iot-hub-get-access-token.md)]

## <a name="use-the-rest-api-to-create-an-iot-hub"></a>Brug REST-API til at oprette en IoT-hub

Brug [IoT Hub REST-API] [ lnk-rest-api] til at oprette en IoT hub i din ressourcegruppe. Du kan også bruge REST-API til at foretage ændringer til en eksisterende IoT-hub.

1. Føje følgende metode til Program.cs:
    
    ```
    static void CreateIoTHub(string token)
    {
        
    }
    ```

2. Føj følgende kode til **CreateIoTHub** metoden til at oprette et **HttpClient** objekt med godkendelse tokenet i overskriften:

    ```
    HttpClient client = new HttpClient();
    client.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Bearer", token);
    ```

3. Føj følgende kode til **CreateIoTHub** metoden til at beskrive IoT hubben for at oprette og generere en JSON repræsentation (se [Azure Status]for den aktuelle liste over placeringer, der understøtter IoT Hub[lnk-status]):

    ```
    var description = new
    {
      name = iotHubName,
      location = "East US",
      sku = new
      {
        name = "S1",
        tier = "Standard",
        capacity = 1
      }
    };
    
    var json = JsonConvert.SerializeObject(description, Formatting.Indented);
    ```

4. Føj følgende kode til **CreateIoTHub** metoden til at sende RESTEN anmodningen til Azure, Markér svaret og hente den URL-adresse, du kan bruge til at overvåge tilstanden for opgaven installation:

    ```
    var content = new StringContent(JsonConvert.SerializeObject(description), Encoding.UTF8, "application/json");
    var requestUri = string.Format("https://management.azure.com/subscriptions/{0}/resourcegroups/{1}/providers/Microsoft.devices/IotHubs/{2}?api-version=2016-02-03", subscriptionId, rgName, iotHubName);
    var result = client.PutAsync(requestUri, content).Result;
      
    if (!result.IsSuccessStatusCode)
    {
      Console.WriteLine("Failed {0}", result.Content.ReadAsStringAsync().Result);
      return;
    }
    
    var asyncStatusUri = result.Headers.GetValues("Azure-AsyncOperation").First();
    ```

5. Føj følgende kode til slutningen af **CreateIoTHub** metoden til at bruge **asyncStatusUri** adressen hentet i det foregående trin til at vente på at fuldføre installationen:

    ```
    string body;
    do
    {
      Thread.Sleep(10000);
      HttpResponseMessage deploymentstatus = client.GetAsync(asyncStatusUri).Result;
      body = deploymentstatus.Content.ReadAsStringAsync().Result;
    } while (body == "{\"status\":\"Running\"}");
    ```

6. Føj følgende kode til slutningen af **CreateIoTHub** metoden til at hente de pågældende taster af IoT-hub, du har oprettet og udskriver dem til konsollen:

    ```
    var listKeysUri = string.Format("https://management.azure.com/subscriptions/{0}/resourceGroups/{1}/providers/Microsoft.Devices/IotHubs/{2}/IoTHubKeys/listkeys?api-version=2015-08-15-preview", subscriptionId, rgName, iotHubName);
    var keysresults = client.PostAsync(listKeysUri, null).Result;
    
    Console.WriteLine("Keys: {0}", keysresults.Content.ReadAsStringAsync().Result);
    ```
    
## <a name="complete-and-run-the-application"></a>Udføre og køre programmet

Du kan nu udføre programmet ved at ringe metoden **CreateIoTHub** , før du opretter og kører den.

1. Føj følgende kode til slutningen af metoden **Main** :

    ```
    CreateIoTHub(token.AccessToken);
    Console.ReadLine();
    ```
    
2. Klik på **Opret** og derefter **opbygge løsning**. Ret eventuelle fejl.

3. Klik på **fejlfinding** og derefter på **Start fejlfinding** for at køre programmet. Det kan tage flere minutter, før installationen til at køre.

4. Du kan kontrollere, at dit program tilføjet nye IoT hubben ved at gå til [Azure portal] [ lnk-azure-portal] og få vist listen over ressourcer eller ved hjælp af **Get-AzureRmResource** PowerShell-cmdlet.

> [AZURE.NOTE] Dette eksempel program tilføjer en S1 Standard IoT-Hub, du er faktureret. Når du er færdig, kan du slette IoT hubben via [Azure portal] [ lnk-azure-portal] eller ved at bruge **Fjern AzureRmResource** PowerShell-cmdlet, når du er færdig.

## <a name="next-steps"></a>Næste trin

Nu du har installeret en IoT hub ved hjælp af REST-API, kan du læse videre:

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

[lnk-c-sdk]: iot-hub-device-sdk-c-intro.md
[lnk-sdks]: iot-hub-devguide-sdks.md

[lnk-gateway]: iot-hub-linux-gateway-sdk-simulated-device.md
