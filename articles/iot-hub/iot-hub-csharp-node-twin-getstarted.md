<properties
    pageTitle="Komme i gang med twins | Microsoft Azure"
    description="Dette selvstudium viser, hvordan du bruger twins"
    services="iot-hub"
    documentationCenter="node"
    authors="fsautomata"
    manager="timlt"
    editor=""/>

<tags
     ms.service="iot-hub"
     ms.devlang="node"
     ms.topic="article"
     ms.tgt_pltfrm="na"
     ms.workload="na"
     ms.date="09/13/2016"
     ms.author="elioda"/>

# <a name="tutorial-get-started-with-device-twins-preview"></a>Selvstudium: Introduktion til enhed twins (preview)

[AZURE.INCLUDE [iot-hub-selector-twin-get-started](../../includes/iot-hub-selector-twin-get-started.md)]

I slutningen af dette selvstudium har du en .NET og et Node.js console program:

* **AddTagsAndQuery.sln**, en .NET-app, der er beregnet til at blive kørt fra back-end, hvilket tilføjer mærker og forespørger enhed twins.
* **TwinSimulatedDevice.js**, en Node.js app som simulerer en enhed, der opretter forbindelse til din IoT hub med enhed identitet oprettede tidligere, og dets connectivity betingelse-rapporter.

> [AZURE.NOTE] I artiklen [IoT Hub SDK'er] [ lnk-hub-sdks] indeholder oplysninger om de forskellige SDK'er, som du kan bruge til at opbygge både enhed og back end-programmer.

For at fuldføre dette selvstudium skal du følgende:

+ Microsoft Visual Studio 2015.

+ Node.js version 0.10.x eller nyere.

+ En active Azure-konto. (Hvis du ikke har en konto, kan du oprette en [gratis konto] [ lnk-free-trial] på blot et par minutter.)

[AZURE.INCLUDE [iot-hub-get-started-create-hub-pp](../../includes/iot-hub-get-started-create-hub-pp.md)]

## <a name="create-the-service-app"></a>Oprette service-app

I dette afsnit, skal oprette du en Node.js console-app, der tilføjer placering metadata til den dobbelt, der er knyttet til **myDeviceId**. Det derefter forespørgsler twins, der er gemt i hubben at vælge enhederne, der er placeret i USA, og klik derefter på dem, der rapportering mobilnettet.

1. I Visual Studio, skal du føje et Visual C# Windows klassisk skrivebord projekt til den aktuelle løsning ved hjælp af skabelonen **Console programmet** project. Navngive projektet **AddTagsAndQuery**.

    ![Projekt med en visuel C# Windows klassisk skrivebord][img-createapp]

2. Højreklik på **AddTagsAndQuery** projektet i Solution Explorer, og klik derefter på **Administrer Nuget pakker**.

3. I vinduet **Nuget Package Manager** skal du kontrollere, at **Medtag foreløbig version** er markeret, søge efter **microsoft.azure.devices**, vælge **installere** at installere den den *foreløbige* version af **Microsoft.Azure.Devices** pakke, og Accepter vilkår for anvendelse. Denne fremgangsmåde henter, installerer og føjer en reference til [Microsoft Azure IoT Service SDK] [ lnk-nuget-service-sdk] Nuget pakke og dets afhængigheder.

    ![Vinduet Nuget pakke Manager][img-servicenuget]

4. Tilføj følgende `using` sætninger øverst i filen **Program.cs** :

        using Microsoft.Azure.Devices;

5. Tilføj følgende felter til klassen **Program** . Erstat værdien pladsholder med forbindelsesstrengen for IoT hubben, du oprettede i forrige afsnit.

        static RegistryManager registryManager;
        static string connectionString = "{iot hub connection string}";

6. Føje følgende metode til klassen **Program** :

        public static async Task AddTagsAndQuery()
        {
            var twin = await registryManager.GetTwinAsync("myDeviceId");
            var patch =
                @"{
                    tags: {
                        location: {
                            region: 'US',
                            plant: 'Redmond43'
                        }
                    }
                }";
            await registryManager.UpdateTwinAsync(twin.DeviceId, patch, twin.ETag);

            var query = registryManager.CreateQuery("SELECT * FROM devices WHERE tags.location.plant = 'Redmond43'", 100);
            var twinsInRedmond43 = await query.GetNextAsTwinAsync();
            Console.WriteLine("Devices in Redmond43: {0}", string.Join(", ", twinsInRedmond43.Select(t => t.DeviceId)));

            query = registryManager.CreateQuery("SELECT * FROM devices WHERE tags.location.plant = 'Redmond43' AND properties.reported.connectivity.type = 'cellular'", 100);
            var twinsInRedmond43UsingCellular = await query.GetNextAsTwinAsync();
            Console.WriteLine("Devices in Redmond43 using cellular network: {0}", string.Join(", ", twinsInRedmond43UsingCellular.Select(t => t.DeviceId)));
        }

    Klassen **RegistryManager** viser alle de metoder, der kræves for at interagere med enhed twins fra tjenesten. Den forrige kode først starter **registryManager** objektet, og derefter henter dobbelt for **myDeviceId**, og til sidst opdaterer dens mærker med oplysninger om den ønskede placering.

    Når du har opdateret, det udfører to forespørgsler: først markerer kun enhed twins enheder, der er placeret i **Redmond43** plante, og andet er blevet forbedret for forespørgslen til at vælge kun de enheder, der er også er forbundet via mobilnetværk.

    Bemærk, at den forrige kode, når programmet opretter objektet **forespørgsel** , angiver et maksimalt antal returnerede dokumenter. Objektet **forespørgsel** indeholder en **HasMoreResults** boolesk egenskab, du kan bruge til at kalde metoderne **GetNextAsTwinAsync** flere gange til at hente alle resultater. En metode med navnet **GetNextAsJson** er tilgængelig for resultater, der ikke er enhed twins eksempelvis resultaterne af sammenlægning forespørgsler.

7. Til sidst skal du føje følgende linjer til metoden **Main** :

        registryManager = RegistryManager.CreateFromConnectionString(connectionString);
        AddTagsAndQuery().Wait();
        Console.WriteLine("Press Enter to exit.");
        Console.ReadLine();

8. Kør dette program, og du bør se én enhed i resultaterne for den forespørgsel, der beder om alle enheder, der er placeret i **Redmond43** og ingen for den forespørgsel, der begrænser resultaterne til enheder, der bruger et mobilnetværk.

    ![Resultater af en forespørgsel i vinduet][img-addtagapp]

I næste afsnit kan du oprette en enhed-app, der rapporter på forbindelsesoplysninger og ændres resultatet af forespørgslen i det forrige afsnit.

## <a name="create-the-device-app"></a>Oprette appen enhed

I dette afsnit, skal oprette du en Node.js console-app, der opretter forbindelse til din hub som **myDeviceId**, og derefter opdaterer dens dobbelt rapporteret egenskaber skal indeholde de oplysninger, der er, at den er tilsluttet ved hjælp af et mobilnetværk.

1. Oprette en ny tom mappe med navnet **reportconnectivity**. Oprette en ny package.json-fil ved hjælp af følgende kommando i din kommandoprompt i mappen **reportconnectivity** . Accepter alle standardværdier:

    ```
    npm init
    ```

2. På din kommandoprompt i mappen **reportconnectivity** skal køre følgende kommando for at installere **azure-iot-enhed**og **azure-iot-enhed – mqtt** pakke:

    ```
    npm install azure-iot-device@dtpreview azure-iot-device-mqtt@dtpreview --save
    ```

3. Ved hjælp af en teksteditor, oprette en ny **ReportConnectivity.js** -fil i mappen **reportconnectivity** .

4. Tilføje følgende kode i filen **ReportConnectivity.js** og erstatte pladsholderen **{enhed forbindelsesstrengen}** med den forbindelsesstreng, du kopierede, da du oprettede **myDeviceId** enhed identitet:

        'use strict';
        var Client = require('azure-iot-device').Client;
        var Protocol = require('azure-iot-device-mqtt').Mqtt;

        var connectionString = '{device connection string}';
        var client = Client.fromConnectionString(connectionString, Protocol);

        client.open(function(err) {
        if (err) {
            console.error('could not open IotHub client');
        }  else {
            console.log('client opened');

            client.getTwin(function(err, twin) {
            if (err) {
                console.error('could not get twin');
            } else {
                var patch = {
                    connectivity: {
                        type: 'cellular'
                    }
                };

                twin.properties.reported.update(patch, function(err) {
                    if (err) {
                        console.error('could not update twin');
                    } else {
                        console.log('twin state reported');
                        process.exit();
                    }
                });
            }
            });
        }
        });

    **Klient** -objektet viser alle de metoder, du kræver for at interagere med enhed twins fra enheden. Den forrige kode, når det starter objektet **klienten** henter dobbelt for **myDeviceId** og opdaterer egenskaben anmeldt med connectivity oplysninger.

5. Køre appen enhed

        node ReportConnectivity.js

    Skal du se afsnittet meddelelsen `twin state reported`.

6. Nu, hvor enheden rapporteret dens forbindelsesoplysninger, skal den vises i begge forespørgsler. Køre appen .NET **AddTagsAndQuery** for at køre forespørgsler igen. Denne gang **myDeviceId** bør fremgå i begge forespørgselsresultaterne.

    ![][img-addtagapp2]

## <a name="next-steps"></a>Næste trin
Konfigureret en ny IoT hub på portalen Azure i dette selvstudium, og derefter oprettet en enhed identitet i den hub identitet registreringsdatabasen. Du tilføjede enhed metadata som mærker fra en back end-program og skrevet en simuleret enhed app til rapport enhed forbindelsesoplysninger i enhed dobbelt. Du har også lært sådan forespørgsel oplysningerne ved hjælp af IoT hubben SQL-lignende forespørgselssprog.

Brug følgende ressourcer for at se, hvordan du:

- sende telemetri fra enheder med at [komme i gang med IoT Hub] [ lnk-iothub-getstarted] selvstudium
- konfigurere enheder, der bruger TOS ønskede egenskaber med de [ønskede egenskaber for at konfigurere enheder kan bruge] [ lnk-twin-how-to-configure] selvstudium
- styre enheder interaktivt (såsom at slå en vifte fra en bruger kontrolleres app) med [Brug direkte metoder] [ lnk-methods-tutorial] selvstudium.

<!-- images -->
[img-servicenuget]: media/iot-hub-csharp-node-twin-getstarted/servicesdknuget.png
[img-createapp]: media/iot-hub-csharp-node-twin-getstarted/createnetapp.png
[img-addtagapp]: media/iot-hub-csharp-node-twin-getstarted/addtagapp.png
[img-addtagapp2]: media/iot-hub-csharp-node-twin-getstarted/addtagapp2.png

<!-- links -->
[lnk-hub-sdks]: iot-hub-devguide-sdks.md
[lnk-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[lnk-nuget-service-sdk]: https://www.nuget.org/packages/Microsoft.Azure.Devices/1.1.0-preview-004

[lnk-d2c]: iot-hub-devguide-messaging.md#device-to-cloud-messages
[lnk-methods]: iot-hub-devguide-direct-methods.md
[lnk-twins]: iot-hub-devguide-device-twins.md
[lnk-query]: iot-hub-devguide-query-language.md
[lnk-identity]: iot-hub-devguide-identity-registry.md

[lnk-iothub-getstarted]: iot-hub-node-node-getstarted.md
[lnk-methods-tutorial]: iot-hub-c2d-methods.md
[lnk-twin-how-to-configure]: iot-hub-csharp-node-twin-how-to-configure.md

[lnk-dev-setup]: https://github.com/Azure/azure-iot-sdks/blob/master/doc/get_started/node-devbox-setup.md

