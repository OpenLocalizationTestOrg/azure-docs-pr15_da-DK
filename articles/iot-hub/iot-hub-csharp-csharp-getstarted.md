<properties
    pageTitle="Azure IoT-Hub til C# Introduktion | Microsoft Azure"
    description="Azure IoT hubben med C# komme i gang selvstudium. Bruge Azure IoT Hub og C# med Microsoft Azure IoT SDK'er for at implementere en Internet ting løsning."
    services="iot-hub"
    documentationCenter=".net"
    authors="dominicbetts"
    manager="timlt"
    editor=""/>

<tags
     ms.service="iot-hub"
     ms.devlang="dotnet"
     ms.topic="hero-article"
     ms.tgt_pltfrm="na"
     ms.workload="na"
     ms.date="09/12/2016"
     ms.author="dobett"/>

# <a name="get-started-with-azure-iot-hub-for-net"></a>Introduktion til Azure IoT Hub til .NET

[AZURE.INCLUDE [iot-hub-selector-get-started](../../includes/iot-hub-selector-get-started.md)]

I slutningen af dette selvstudium har du tre Windows console-programmer:

* **CreateDeviceIdentity**, som opretter en enhed identitet og tilknyttede sikkerhed for at oprette forbindelse mellem enheden simuleret.
* **ReadDeviceToCloudMessages**, som viser telemetri sendt af enheden simuleret.
* **SimulatedDevice**, som opretter forbindelse til din IoT hub med enhed identitet oprettede tidligere, og sender en meddelelse til telemetri i sekundet ved hjælp af AMQP-protokollen.

> [AZURE.NOTE] Finde oplysninger om de forskellige SDK'er, som du kan bruge til at opbygge begge programmer til at køre på enheder, og din løsning back-end, se [IoT Hub SDK'er][lnk-hub-sdks].

For at fuldføre dette selvstudium skal bruge du følgende:

+ Microsoft Visual Studio 2015.

+ En active Azure-konto. (Hvis du ikke har en konto, kan du oprette en [gratis konto] [ lnk-free-trial] på blot et par minutter.)

[AZURE.INCLUDE [iot-hub-get-started-create-hub](../../includes/iot-hub-get-started-create-hub.md)]

Du har nu oprettet IoT-hub, og du har strengen hostname og forbindelse, du har brug for at fuldføre resten af dette selvstudium.

## <a name="create-a-device-identity"></a>Oprette en enhed identitet

I dette afsnit, skal oprette du en Windows-konsollen app, der opretter en enhed identitet i identitet registreringsdatabasen i IoT-hub. En enhed kan ikke oprette forbindelse til IoT-hub, medmindre den har en post i registreringsdatabasen enhed identitet. Du kan finde flere oplysninger i afsnittet "Enhed identitet registreringsdatabasen" i [IoT Hub Developer Guide][lnk-devguide-identity]. Når du kører denne console-app, oprettes der et entydigt enheds-ID og -tasten, enheden kan bruge til at identificere sig, når der udsendes enhed i skyen meddelelser til IoT-Hub.

1. I Visual Studio, skal du føje et Visual C# Windows klassisk skrivebord projekt til den aktuelle løsning ved hjælp af skabelonen **Console programmet** project. Sørg for, at versionen af .NET Framework er 4.5.1 eller nyere. Navngive projektet **CreateDeviceIdentity**.

    ![Projekt med en visuel C# Windows klassisk skrivebord][10]

2. Højreklik på **CreateDeviceIdentity** projektet i Solution Explorer, og klik derefter på **Administrer Nuget pakker**.

3. I vinduet **Nuget Package Manager** Vælg **Gennemse**, søge efter **microsoft.azure.devices**, vælge **installere** at installere **Microsoft.Azure.Devices** pakken og Acceptér vilkårene for anvendelse. Denne fremgangsmåde henter, installerer og føjer en reference til [Microsoft Azure IoT Service SDK] [ lnk-nuget-service-sdk] Nuget pakke og dets afhængigheder.

    ![Vinduet Nuget pakke Manager][11]

4. Tilføj følgende `using` sætninger øverst i filen **Program.cs** :

        using Microsoft.Azure.Devices;
        using Microsoft.Azure.Devices.Common.Exceptions;

5. Tilføj følgende felter til klassen **Program** . Erstat værdien pladsholder med forbindelsesstrengen for IoT hubben, du oprettede i forrige afsnit.

        static RegistryManager registryManager;
        static string connectionString = "{iot hub connection string}";

6. Føje følgende metode til klassen **Program** :

        private static async Task AddDeviceAsync()
        {
            string deviceId = "myFirstDevice";
            Device device;
            try
            {
                device = await registryManager.AddDeviceAsync(new Device(deviceId));
            }
            catch (DeviceAlreadyExistsException)
            {
                device = await registryManager.GetDeviceAsync(deviceId);
            }
            Console.WriteLine("Generated device key: {0}", device.Authentication.SymmetricKey.PrimaryKey);
        }

    Denne metode opretter en enhed identitet med id- **myFirstDevice**. (Hvis enheds-ID, der allerede findes i registreringsdatabasen, koden blot henter enhedsoplysninger fra det eksisterende.) Appen viser derefter den primære nøgle for denne identitet. Du kan bruge denne tast i simuleret enheden til at oprette forbindelse til din IoT-hub.

7. Til sidst skal du føje følgende linjer til metoden **Main** :

        registryManager = RegistryManager.CreateFromConnectionString(connectionString);
        AddDeviceAsync().Wait();
        Console.ReadLine();

8. Kør dette program, og notere tasten enhed.

    ![Enhed nøgle genereres af programmet][12]

> [AZURE.NOTE] IoT Hub identitet registreringsdatabasen gemmer kun enhed identiteter for at aktivere sikker adgang til den pågældende hub. Det gemmer enheds-id'er og taster, der bruges som sikkerhedslegitimationsoplysninger og en aktiveret/deaktiveret flag, du kan bruge til at deaktivere adgang til en enkelt enhed. Hvis programmet skal gemme andre enhed-specifikke metadata, skal der bruges et program-specifikke store. Yderligere oplysninger finder du [IoT Hub Developer Guide][lnk-devguide-identity].

## <a name="receive-device-to-cloud-messages"></a>Modtage enhed i skyen meddelelser

I dette afsnit, skal oprette du en Windows-konsollen app, som læser enhed i skyen meddelelser fra IoT Hub. En IoT hub Fremviser en [Azure begivenhed Hubs][lnk-event-hubs-overview]-kompatible slutpunkt til gør det muligt at læse enhed i skyen meddelelser. Hvis du vil bevare ting enkel, opretter dette selvstudium en grundlæggende læser, ikke der passer til en høj overførselshastighed installation. Få vist [proces enhed i skyen meddelelser] for at lære at behandle enhed i skyen meddelelser skaleres,[ lnk-process-d2c-tutorial] selvstudium. Yderligere oplysninger om, hvordan du behandle meddelelser fra begivenhed Hubs, se [Introduktion til begivenhed Hubs] [ lnk-eventhubs-tutorial] selvstudium. (Dette selvstudium er gældende for IoT Hub begivenhed Hub-kompatible slutpunkterne).

> [AZURE.NOTE] Begivenhed Hub-kompatible slutpunkt for at læse enhed i skyen meddelelser altid bruger AMQP-protokollen.

1. I Visual Studio, skal du føje et Visual C# Windows klassisk skrivebord projekt til den aktuelle løsning ved hjælp af skabelonen **Console programmet** project. Sørg for, at versionen af .NET Framework er 4.5.1 eller nyere. Navngive projektet **ReadDeviceToCloudMessages**.

    ![Projekt med en visuel C# Windows klassisk skrivebord][10]

2. Højreklik på **ReadDeviceToCloudMessages** projektet i Solution Explorer, og klik derefter på **Administrer Nuget pakker**.

3. Søge efter **WindowsAzure.ServiceBus**i vinduet **Nuget Package Manager** , vælge **Installer**, og Accepter vilkår for anvendelse. Denne fremgangsmåde henter, installerer og føjer en reference til [Azure Service Bus][lnk-servicebus-nuget], med alle dens afhængigheder. Denne pakke gør det muligt for programmet til at oprette forbindelse til begivenhed Hub-kompatible slutpunktet på IoT centrum.

4. Tilføj følgende `using` sætninger øverst i filen **Program.cs** :

        using Microsoft.ServiceBus.Messaging;
        using System.Threading;

5. Tilføj følgende felter til klassen **Program** . Erstat værdien pladsholder med forbindelsesstrengen for IoT hubben du oprettede i afsnittet "Oprette en IoT hub".

        static string connectionString = "{iothub connection string}";
        static string iotHubD2cEndpoint = "messages/events";
        static EventHubClient eventHubClient;

6. Føje følgende metode til klassen **Program** :

        private static async Task ReceiveMessagesFromDeviceAsync(string partition, CancellationToken ct)
        {
            var eventHubReceiver = eventHubClient.GetDefaultConsumerGroup().CreateReceiver(partition, DateTime.UtcNow);
            while (true)
            {
                if (ct.IsCancellationRequested) break;
                EventData eventData = await eventHubReceiver.ReceiveAsync();
                if (eventData == null) continue;

                string data = Encoding.UTF8.GetString(eventData.GetBytes());
                Console.WriteLine("Message received. Partition: {0} Data: '{1}'", partition, data);
            }
        }

    Denne metode bruger en **EventHubReceiver** forekomst til at modtage meddelelser fra alle IoT hub enhed-til-skyen modtage partitioner. Bemærk, hvordan du overfører en `DateTime.Now` parameter, når du opretter objektet **EventHubReceiver** , så den kun modtager meddelelser, der sendes, når programmet starter. Dette filter er nyttig i et testmiljø, så du kan se det aktuelle sæt af meddelelser. Din kode skal sikre dig, at det behandler alle meddelelserne i et produktionsmiljø. Du kan finde flere oplysninger, se, [hvordan til at behandle IoT Hub enhed i skyen meddelelser] [ lnk-process-d2c-tutorial] selvstudium.

7. Til sidst skal du føje følgende linjer til metoden **Main** :

        Console.WriteLine("Receive messages. Ctrl-C to exit.\n");
        eventHubClient = EventHubClient.CreateFromConnectionString(connectionString, iotHubD2cEndpoint);

        var d2cPartitions = eventHubClient.GetRuntimeInformation().PartitionIds;

        CancellationTokenSource cts = new CancellationTokenSource();

        System.Console.CancelKeyPress += (s, e) =>
        {
          e.Cancel = true;
          cts.Cancel();
          Console.WriteLine("Exiting...");
        };

        var tasks = new List<Task>();
        foreach (string partition in d2cPartitions)
        {
           tasks.Add(ReceiveMessagesFromDeviceAsync(partition, cts.Token));
        }  
        Task.WaitAll(tasks.ToArray());

## <a name="create-a-simulated-device-app"></a>Oprette en simuleret enhed app

I dette afsnit, skal oprette du en Windows-konsollen app, der simulerer en enhed, som sender enhed i skyen meddelelser til en IoT-hub.

1. I Visual Studio, skal du føje et Visual C# Windows klassisk skrivebord projekt til den aktuelle løsning ved hjælp af skabelonen **Console programmet** project. Sørg for, at versionen af .NET Framework er 4.5.1 eller nyere. Navngive projektet **SimulatedDevice**.

    ![Projekt med en visuel C# Windows klassisk skrivebord][10]

2. Højreklik på **SimulatedDevice** projektet i Solution Explorer, og klik derefter på **Administrer Nuget pakker**.

3. I vinduet **Nuget Package Manager** Vælg **Gennemse**, søge efter **Microsoft.Azure.Devices.Client**, vælge **installere** at installere **Microsoft.Azure.Devices.Client** pakken og Acceptér vilkårene for anvendelse. Denne fremgangsmåde henter, installerer og føjer en reference til [Azure IoT - enhed SDK Nuget pakke] [ lnk-device-nuget] og dens afhængigheder.

4. Tilføj følgende `using` sætningen øverst i filen **Program.cs** :

        using Microsoft.Azure.Devices.Client;
        using Newtonsoft.Json;

5. Tilføj følgende felter til klassen **Program** . Erstatte pladsholder værdier med IoT hub hostname du har hentet i afsnittet "Oprette en IoT hub", og nøglen til den enhed, der er hentet i afsnittet "Oprette en enhed identitet".

        static DeviceClient deviceClient;
        static string iotHubUri = "{iot hub hostname}";
        static string deviceKey = "{device key}";

6. Føje følgende metode til klassen **Program** :

        private static async void SendDeviceToCloudMessagesAsync()
        {
            double avgWindSpeed = 10; // m/s
            Random rand = new Random();

            while (true)
            {
                double currentWindSpeed = avgWindSpeed + rand.NextDouble() * 4 - 2;

                var telemetryDataPoint = new
                {
                    deviceId = "myFirstDevice",
                    windSpeed = currentWindSpeed
                };
                var messageString = JsonConvert.SerializeObject(telemetryDataPoint);
                var message = new Message(Encoding.ASCII.GetBytes(messageString));

                await deviceClient.SendEventAsync(message);
                Console.WriteLine("{0} > Sending message: {1}", DateTime.Now, messageString);

                Task.Delay(1000).Wait();
            }
        }

    Denne metode sender en ny enhed i skyen-meddelelse i sekundet. Meddelelsen indeholder et JSON-serialiseret objekt med enheds-ID og et tilfældigt tal for at simulere en vindhastighed bliver hastighed føler.

7. Til sidst skal du føje følgende linjer til metoden **Main** :

        Console.WriteLine("Simulated device\n");
        deviceClient = DeviceClient.Create(iotHubUri, new DeviceAuthenticationWithRegistrySymmetricKey("myFirstDevice", deviceKey));

        SendDeviceToCloudMessagesAsync();
        Console.ReadLine();

  Metoden **Create** opretter en forekomst af **DeviceClient** , der bruger protokollen AMQP til at kommunikere med IoT Hub som standard. Brug tilsidesættelse af metoden **Create** , der gør det muligt at angive protokollen, hvis du vil bruge HTTP-protokollen. Hvis du bruger HTTP-protokollen, skal du også føje pakken **Microsoft.AspNet.WebApi.Client** Nuget til dit projekt for at medtage navneområdet **System.Net.Http.Formatting** .

Dette selvstudium fører dig gennem trinnene til at oprette en IoT Hub enhed klient. Du kan også bruge [Forbindelse-tjeneste til Azure IoT Hub] [ lnk-connected-service] Visual Studio-typenavn til at føje den nødvendige kode til klientprogrammet enhed.


> [AZURE.NOTE] Dette selvstudium implementerer for at holde ting enkel, ikke en politik for prøv igen. Fremstilling kode, skal du implementere forsøg politikker (som en eksponentiel backoff), som foreslåede i MSDN-artikel [Midlertidige håndtering af fejl][lnk-transient-faults].

## <a name="run-the-applications"></a>Køre programmer

Du er nu klar til at køre programmerne.

1.  Højreklik på din løsning i Visual Studio i Solution Explorer, og klik derefter på **Angiv start projekter**. Vælg **flere projekter, Start**, og derefter vælge **Start** som handlingen for både **ReadDeviceToCloudMessages** og **SimulatedDevice** projekter.

    ![Startegenskaberne for projektet][41]

2.  Tryk på **F5** for at starte begge apps, der kører. Output fra konsollen fra **SimulatedDevice** app viser meddelelserne enheden simuleret sender til IoT centrum. Output fra konsollen fra **ReadDeviceToCloudMessages** app viser de meddelelser, der modtager IoT centrum.

    ![Console output fra apps][42]

3. Feltet **brugen** i [Azure portal] [ lnk-portal] viser antallet af meddelelser, der sendes til hubben:

    ![Azure portalen brugen felt][43]


## <a name="next-steps"></a>Næste trin

Konfigureret en IoT hub på portalen Azure i dette selvstudium, og derefter oprettet en enhed identitet i den hub identitet registreringsdatabasen. Du har brugt denne enhed identitet til at aktivere appen simuleret enhed til at sende enhed i skyen meddelelser til den pågældende hub. Du kan også oprettet en app, der viser de meddelelser, der modtages af hubben. 

Vil fortsætte med at komme i gang med IoT Hub og udforske andre IoT scenarier, skal du se:

- [Oprette forbindelse til din enhed][lnk-connect-device]
- [Kom godt i gang med administration af enheder][lnk-device-management]
- [Introduktion til IoT Gateway SDK][lnk-gateway-SDK]

Få vist [proces enhed i skyen meddelelser] for at lære at udvide din IoT løsning og behandle enhed i skyen meddelelser skaleres,[ lnk-process-d2c-tutorial] selvstudium.

<!-- Images. -->
[41]: ./media/iot-hub-csharp-csharp-getstarted/run-apps1.png
[42]: ./media/iot-hub-csharp-csharp-getstarted/run-apps2.png
[43]: ./media/iot-hub-csharp-csharp-getstarted/usage.png
[10]: ./media/iot-hub-csharp-csharp-getstarted/create-identity-csharp1.png
[11]: ./media/iot-hub-csharp-csharp-getstarted/create-identity-csharp2.png
[12]: ./media/iot-hub-csharp-csharp-getstarted/create-identity-csharp3.png

<!-- Links -->
[lnk-process-d2c-tutorial]: iot-hub-csharp-csharp-process-d2c.md

[lnk-hub-sdks]: iot-hub-devguide-sdks.md
[lnk-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[lnk-portal]: https://portal.azure.com/

[lnk-eventhubs-tutorial]: ../event-hubs/event-hubs-csharp-ephcs-getstarted.md
[lnk-devguide-identity]: iot-hub-devguide-identity-registry.md
[lnk-servicebus-nuget]: https://www.nuget.org/packages/WindowsAzure.ServiceBus
[lnk-event-hubs-overview]: ../event-hubs/event-hubs-overview.md

[lnk-nuget-service-sdk]: https://www.nuget.org/packages/Microsoft.Azure.Devices/
[lnk-device-nuget]: https://www.nuget.org/packages/Microsoft.Azure.Devices.Client/
[lnk-transient-faults]: https://msdn.microsoft.com/library/hh680901(v=pandp.50).aspx
[lnk-connected-service]: https://visualstudiogallery.msdn.microsoft.com/e254a3a5-d72e-488e-9bd3-8fee8e0cd1d6
[lnk-device-management]: iot-hub-device-management-get-started.md
[lnk-gateway-SDK]: iot-hub-linux-gateway-sdk-get-started.md
[lnk-connect-device]: https://azure.microsoft.com/develop/iot/
