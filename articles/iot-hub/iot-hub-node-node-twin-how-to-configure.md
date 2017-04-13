<properties
    pageTitle="Bruge dobbelt egenskaber | Microsoft Azure"
    description="Dette selvstudium viser, hvordan du kan bruge to egenskaber"
    services="iot-hub"
    documentationCenter=".net"
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

# <a name="tutorial-use-desired-properties-to-configure-devices-preview"></a>Selvstudium: Brug ønskede egenskaber til at konfigurere enheder (preview)

[AZURE.INCLUDE [iot-hub-selector-twin-how-to-configure](../../includes/iot-hub-selector-twin-how-to-configure.md)]

I slutningen af dette selvstudium har du to Node.js console-programmer:

* **SimulateDeviceConfiguration.js**, en simuleret enhed app, der venter på en ønsket konfigurationsopdatering og rapporterer status for en simuleret konfiguration opdateringsprocessen.
* **SetDesiredConfigurationAndQuery.js**, en Node.js-app, der er beregnet til at blive kørt fra back-end, der angiver den ønskede konfiguration på en enhed og forespørger opdateringsprocessen konfiguration.

> [AZURE.NOTE] I artiklen [IoT Hub SDK'er] [ lnk-hub-sdks] indeholder oplysninger om de forskellige SDK'er, som du kan bruge til at opbygge både enhed og back end-programmer.

For at fuldføre dette selvstudium skal du følgende:

+ Node.js version 0.10.x eller nyere.

+ En active Azure-konto. (Hvis du ikke har en konto, kan du oprette en [gratis konto] [ lnk-free-trial] på blot et par minutter.)

Hvis du har fulgt [komme i gang med enhed twins] [ lnk-twin-tutorial] selvstudium, du allerede har en enhed administration af hub og en enhed identitet, kaldet **myDeviceId**; og du kan gå til [oprette simuleret enhed app] [ lnk-how-to-configure-createapp] sektion.

[AZURE.INCLUDE [iot-hub-get-started-create-hub-pp](../../includes/iot-hub-get-started-create-hub-pp.md)]

## <a name="create-the-simulated-device-app"></a>Oprette simuleret enhed-app

I dette afsnit, skal oprette du en Node.js console-app, der opretter forbindelse til din hub som **myDeviceId**, venter på en ønsket konfigurationsopdatering og derefter rapporterer opdateringer om opdateringsprocessen simuleret konfiguration.

1. Oprette en ny tom mappe med navnet **simulatedeviceconfiguration**. Oprette en ny package.json-fil ved hjælp af følgende kommando i din kommandoprompt i mappen **simulatedeviceconfiguration** . Accepter alle standardværdier:

    ```
    npm init
    ```

2. På din kommandoprompt i mappen **simulatedeviceconfiguration** skal køre følgende kommando for at installere **azure-iot-enhed**og **azure-iot-enhed – mqtt** pakke:

    ```
    npm install azure-iot-device@dtpreview azure-iot-device-mqtt@dtpreview --save
    ```

3. Ved hjælp af en teksteditor, oprette en ny **SimulateDeviceConfiguration.js** -fil i mappen **simulatedeviceconfiguration** .

4. Tilføje følgende kode i filen **SimulateDeviceConfiguration.js** og erstatte pladsholderen **{enhed forbindelsesstrengen}** med den forbindelsesstreng, du kopierede, da du oprettede **myDeviceId** enhed identitet:

        'use strict';
        var Client = require('azure-iot-device').Client;
        var Protocol = require('azure-iot-device-mqtt').Mqtt;

        var connectionString = '{device connection string}';
        var client = Client.fromConnectionString(connectionString, Protocol);

        client.open(function(err) {
            if (err) {
                console.error('could not open IotHub client');
            } else {
                client.getTwin(function(err, twin) {
                    if (err) {
                        console.error('could not get twin');
                    } else {
                        console.log('retrieved device twin');
                        twin.properties.reported.telemetryConfig = {
                            configId: "0",
                            sendFrequency: "24h"
                        }
                        twin.on('properties.desired', function(desiredChange) {
                            console.log("received change: "+JSON.stringify(desiredChange));
                            var currentTelemetryConfig = twin.properties.reported.telemetryConfig;
                            if (desiredChange.telemetryConfig &&desiredChange.telemetryConfig.configId !== currentTelemetryConfig.configId) {
                                initConfigChange(twin);
                            }
                        });
                    }
                });
            }
        });

    **Klient** -objektet viser alle de metoder, der kræves for at interagere med enhed twins fra enheden. Forrige kode, når det starter objektet **klienten** henter dobbelt for **myDeviceId**og vedhæfter en handler om opdatering ønskede egenskaber. Handleren, der bekræfter, at der er en anmodning om ændring af faktiske konfiguration ved at sammenligne configIds og derefter kalder en metode, der starter konfigurationen ændringen.

    Bemærk, for at sikre en enkel, den forrige kode bruger et hårdt kodet standardstørrelse til den oprindelige konfiguration. Et reelt tal app indlæser sandsynligvis konfigurationen fra et lokalt lager.
    
    > [AZURE.IMPORTANT] Ønskede egenskaben Skift hændelser der altid udledes én gang på enhedsforbindelse, skal du kontrollere, at der er en faktiske ændring i de ønskede egenskaber, før du udfører en handling.

5. Tilføj følgende metoder før den `client.open()` aktivering:

        var initConfigChange = function(twin) {
            var currentTelemetryConfig = twin.properties.reported.telemetryConfig;
            currentTelemetryConfig.pendingConfig = twin.properties.desired.telemetryConfig;
            currentTelemetryConfig.status = "Pending";

            var patch = {
            telemetryConfig: currentTelemetryConfig
            };
            twin.properties.reported.update(patch, function(err) {
                if (err) {
                    console.log('Could not report properties');
                } else {
                    console.log('Reported pending config change: ' + JSON.stringify(patch));
                    setTimeout(function() {completeConfigChange(twin);}, 60000);
                }
            });
        }

        var completeConfigChange =  function(twin) {
            var currentTelemetryConfig = twin.properties.reported.telemetryConfig;
            currentTelemetryConfig.configId = currentTelemetryConfig.pendingConfig.configId;
            currentTelemetryConfig.sendFrequency = currentTelemetryConfig.pendingConfig.sendFrequency;
            currentTelemetryConfig.status = "Success";
            delete currentTelemetryConfig.pendingConfig;
            
            var patch = {
                telemetryConfig: currentTelemetryConfig
            };
            patch.telemetryConfig.pendingConfig = null;

            twin.properties.reported.update(patch, function(err) {
                if (err) {
                    console.error('Error reporting properties: ' + err);
                } else {
                    console.log('Reported completed config change: ' + JSON.stringify(patch));
                }
            });
        };

    Metoden **initConfigChange** opdaterer anmeldt egenskaber for objektet lokale dobbelt med opdateringsforespørgsel config og angiver status til **ventende**og derefter opdaterer enhed med dobbelt om tjenesten. Når du har opdateret korrekt på dobbelt, simulerer den en længerevarende proces, der ophører i udførelse af **completeConfigChange**. Denne metode opdaterer lokale med dobbelt anmeldt egenskaber angive status for **succes** og fjerne objektet **pendingConfig** . Det opdaterer derefter dobbelt om tjenesten.

    Bemærk, at gemme båndbredde, rapporteret egenskaber opdateres ved at angive egenskaberne for at være ændret (navngivet **programrettelse** i ovenstående kode), i stedet for at erstatte hele dokumentet.

    > [AZURE.NOTE] Dette selvstudium simulere ikke en hvilken som helst funktionsmåde for samtidige konfiguration opdateringer. Nogle konfiguration opdateringsprocesser muligvis ikke kunne tages højde for ændringer af target konfigurationen af mens kører opdateringen, andre muligvis nødt til at sætte dem i kø og andre kan afvise dem med en fejltilstand. Sørg for, at skal du tænke på den ønskede funktionsmåde til din specifikke konfigurationsprocessen, og Tilføj den relevante logik inden du starter konfigurationen ændringen.

6. Køre appen enhed:

        node SimulateDeviceConfiguration.js

    Skal du se afsnittet meddelelsen `retrieved device twin`. Bevar appen kører.

## <a name="create-the-service-app"></a>Oprette service-app

I dette afsnit, skal oprette du en Node.js console-app, der opdaterer de *ønskede egenskaber* under den dobbelt, der er knyttet til **myDeviceId** med et nyt telemetri konfigurationsobjekt. Det derefter forespørgsler på enheden twins, der er gemt i hubben og viser forskellen mellem de ønskede og anmeldt konfigurationer på enheden.

1. Oprette en ny tom mappe med navnet **setdesiredandqueryapp**. Oprette en ny package.json-fil ved hjælp af følgende kommando i din kommandoprompt i mappen **setdesiredandqueryapp** . Accepter alle standardværdier:

    ```
    npm init
    ```

2. På din kommandoprompt i mappen **setdesiredandqueryapp** skal køre følgende kommando for at installere pakken **azure-iothub** :

    ```
    npm install azure-iothub@dtpreview node-uuid --save
    ```

3. Ved hjælp af en teksteditor, oprette en ny **SetDesiredAndQuery.js** -fil i mappen **addtagsandqueryapp** .

4. Tilføje følgende kode i filen **SetDesiredAndQuery.js** og erstatte pladsholderen **{service forbindelsesstrengen}** med den forbindelsesstreng, du kopierede, da du oprettede dit hub:

        'use strict';
        var iothub = require('azure-iothub');
        var uuid = require('node-uuid');
        var connectionString = '{service connection string}';
        var registry = iothub.Registry.fromConnectionString(connectionString);
         
        registry.getTwin('myDeviceId', function(err, twin){
            if (err) {
                console.error(err.constructor.name + ': ' + err.message);
            } else {
                var newConfigId = uuid.v4();
                var newFrequency = process.argv[2] || "5m";
                var patch = {
                    properties: {
                        desired: {
                            telemetryConfig: {
                                configId: newConfigId,
                                sendFrequency: newFrequency
                            }
                        }
                    }
                }
                twin.update(patch, function(err) {
                    if (err) {
                        console.error('Could not update twin: ' + err.constructor.name + ': ' + err.message);
                    } else {
                        console.log(twin.deviceId + ' twin updated successfully');
                    }
                });
                setInterval(queryTwins, 10000);
            }
        });
            

    Objektet **registreringsdatabasen** viser alle de metoder, der kræves for at interagere med enhed twins fra tjenesten. Forrige kode, når det starter objektet **registreringsdatabasen** henter dobbelt for **myDeviceId**og opdaterer egenskaberne ønskede med et nyt telemetri konfigurationsobjekt. Herefter kalder hændelsen **queryTwins** funktionen 10 sekunder.

    > [AZURE.IMPORTANT] Dette program forespørger IoT Hub hvert 10 til vejledende formål. Bruge forespørgsler til at generere bruger mod rapporter på tværs af mange enheder og ikke til at registrere ændringer. Hvis din løsning kræver meddelelser i realtid af enhedshændelser bruger [enhed i skyen meddelelser][lnk-d2c].

5. Tilføj følgende kode højre før den `registry.getDeviceTwin()` aktivering for at implementere funktionen **queryTwins** :

        var queryTwins = function() {
            var query = registry.createQuery("SELECT * FROM devices WHERE deviceId = 'myDeviceId'", 100);
            query.nextAsTwin(function(err, results) {
                if (err) {
                    console.error('Failed to fetch the results: ' + err.message);
                } else {
                    console.log();
                    results.forEach(function(twin) {
                        var desiredConfig = twin.properties.desired.telemetryConfig;
                        var reportedConfig = twin.properties.reported.telemetryConfig;
                        console.log("Config report for: " + twin.deviceId);
                        console.log("Desired: ");
                        console.log(JSON.stringify(desiredConfig, null, 2));
                        console.log("Reported: ");
                        console.log(JSON.stringify(reportedConfig, null, 2));
                    });
                }
            });
        };

    De forrige kode forespørgsler på twins gemt i hubben og udskriver de ønskede og anmeldt telemetri konfigurationer. Refererer til det [IoT Hub forespørgselssprog] [ lnk-query] lære at oprette avancerede rapporter på tværs af alle dine enheder.


8. Mens **SimulateDeviceConfiguration.js** kører, skal du køre programmet med:

        node SetDesiredAndQuery.js 5m

    Du skal se det, der rapporteres konfiguration ændres fra **Success** til **ventende** til **succes** igen med det nye aktive sende hyppigheden for fem minutter i stedet for 24 timer.

    > [AZURE.IMPORTANT] Der findes en forsinkelse på op til et minut mellem handlingen enhed rapport og resultatet af forespørgslen. Dette er at aktivere forespørgsel infrastrukturen til at arbejde på meget høj skala. Hvis du vil hente ensartet visninger af en enkelt dobbelt, skal du bruge metoden **getDeviceTwin** i klassen **registreringsdatabasen** .

## <a name="next-steps"></a>Næste trin

Angive en ønsket konfiguration som *ønskede egenskaber* fra en back end-program i dette selvstudium, og skrevet en simuleret enhed app at registrere, ændre og simulere en flere trin opdateringsprocessen rapportere status som *rapporteret egenskaber* til to.

Brug følgende ressourcer for at se, hvordan du:

- sende telemetri fra enheder med at [komme i gang med IoT Hub] [ lnk-iothub-getstarted] selvstudium
- planlægge eller udføre handlinger på store datasæt enheder se [Brug job til at planlægge og transmittere enhed handlinger] [ lnk-schedule-jobs] selvstudium.
- styre enheder interaktivt (såsom slår en vifte fra en bruger kontrolleres app), med [Brug direkte metoder] [ lnk-methods-tutorial] selvstudium.


<!-- links -->
[lnk-hub-sdks]: iot-hub-devguide-sdks.md
[lnk-free-trial]: http://azure.microsoft.com/pricing/free-trial/

[lnk-devguide-jobs]: iot-hub-devguide-jobs.md
[lnk-query]: iot-hub-devguide-query-language.md
[lnk-d2c]: iot-hub-devguide-messaging.md#device-to-cloud-messages
[lnk-methods]: iot-hub-devguide-direct-methods.md
[lnk-dm-overview]: iot-hub-device-management-overview.md
[lnk-twin-tutorial]: iot-hub-node-node-twin-getstarted.md
[lnk-schedule-jobs]: iot-hub-schedule-jobs.md
[lnk-dev-setup]: https://github.com/Azure/azure-iot-sdks/blob/master/doc/get_started/node-devbox-setup.md
[lnk-connect-device]: https://azure.microsoft.com/develop/iot/
[lnk-device-management]: iot-hub-device-management-get-started.md
[lnk-gateway-SDK]: iot-hub-linux-gateway-sdk-get-started.md
[lnk-iothub-getstarted]: iot-hub-node-node-getstarted.md
[lnk-methods-tutorial]: iot-hub-c2d-methods.md

[lnk-guid]: https://en.wikipedia.org/wiki/Globally_unique_identifier

[lnk-how-to-configure-createapp]: iot-hub-node-node-twin-how-to-configure.md#create-the-simulated-device-app
