<properties
 pageTitle="Komme i gang med administration af enheder | Microsoft Azure"
 description="Dette selvstudium viser, hvordan du kommer i gang med administration af enheder på Azure IoT Hub"
 services="iot-hub"
 documentationCenter=".net"
 authors="juanjperez"
 manager="timlt"
 editor=""/>

<tags
 ms.service="iot-hub"
 ms.devlang="multiple"
 ms.topic="article"
 ms.tgt_pltfrm="na"
 ms.workload="na"
 ms.date="09/30/2016" 
 ms.author="juanpere"/>

# <a name="tutorial-get-started-with-device-management-preview"></a>Selvstudium: Kom i gang med administration af enheder (preview)

## <a name="introduction"></a>Introduktion
IoT skyen programmer kan bruge primitiver i Azure IoT Hub, nemlig enhed dobbelt og direkte metoder til at starte fra en fjernplacering og overvåger enhed management handlinger på enheder.  I denne artikel indeholder en vejledning og kode for hvordan et IoT skyen computerprogram og en enhed fungerer sammen for at starte og overvåge fjernenhed genstart ved hjælp af IoT Hub.

For at starte og overvåger enhed management handlinger på dine enheder fra en skybaseret og back end-app fra en fjernplacering, bruge Azure IoT Hub primitiver som [enhed dobbelt] [ lnk-devtwin] og [skyen-til-enhed (C2D) metoder][lnk-c2dmethod]. Dette selvstudium viser, hvordan en back end-app og en enhed kan fungere sammen til giver dig mulighed for Påbegynd og overvåge remote enheden genstart fra IoT Hub.

Du bruger en C2D metode til at starte enhed management handlinger (som genstart, fabrik Nulstil og firmware-opdatering) fra en back end-app i skyen. Enheden er ansvarlig for:

- Håndtering af den metode anmodning, der sendes fra IoT Hub.
- Start af den tilsvarende enhed specifikke handling på enheden.
- Give statusopdateringer gennem enhed med dobbelt rapporteret egenskaber til IoT-Hub.

Du kan bruge en back end-app i skyen til at køre enhed dobbelt forespørgsler til at rapportere om status for din enhed management handlinger.

Dette selvstudium viser, hvordan til:

- Bruge Azure-portalen til at oprette en IoT Hub og oprette en enhed identitet i IoT-hubben.
- Oprette en simuleret enhed, der har en direkte metode, der gør det muligt for genstart, som kan kaldes af skyen.
- Oprette et console-program, der kalder metoden genstart direkte på simuleret enheden via IoT centrum.

I slutningen af dette selvstudium har du to Node.js console-programmer:

**dmpatterns_getstarted_device.js**, som opretter forbindelse til din IoT hub med enhed identitet oprettede tidligere, modtager en genstart direkte metode, simulerer en fysisk genstart og rapporterer tid for den sidste genstart.

**dmpatterns_getstarted_service.js**, som kalder en direkte metode på simuleret enheden, viser svaret, og viser den opdaterede enhed med dobbelt rapporteret egenskaber.

For at fuldføre dette selvstudium skal bruge du følgende:

Node.js version 0.12.x eller nyere <br/>  [Forberede din udviklingsmiljø] [ lnk-dev-setup] beskriver, hvordan du installerer Node.js til dette selvstudium på Windows eller Linux.

En active Azure-konto. (Hvis du ikke har en konto, kan du oprette en [gratis konto] [ lnk-free-trial] på blot et par minutter.)

[AZURE.INCLUDE [iot-hub-get-started-create-hub-pp](../../includes/iot-hub-get-started-create-hub-pp.md)]

## <a name="create-a-simulated-device-app"></a>Oprette en simuleret enhed app

I dette afsnit, skal du oprette en Node.js console-app, der svarer til en direkte metode, der kaldes af i skyen, der udløser en simuleret enheden genstart og bruger enhed dobbelt rapporteret egenskaber for at aktivere enhed dobbelt forespørgsler til at identificere enheder, og når de sidste genstartet.

1. Oprette en ny tom mappe med navnet **manageddevice**.  Oprette en package.json fil ved hjælp af følgende kommando i din kommandoprompt i mappen **manageddevice** .  Accepter alle standardværdier:

    ```
    npm init
    ```
    
2. På din kommandoprompt i mappen **manageddevice** , skal du køre følgende kommando for at installere den **azure-iot-device@dtpreview** enhed SDK-pakken og **azure-iot-device-mqtt@dtpreview** pakke:

    ```
    npm install azure-iot-device@dtpreview azure-iot-device-mqtt@dtpreview --save
    ```

3. Ved hjælp af en teksteditor, oprette en ny **dmpatterns_getstarted_device.js** -fil i mappen **manageddevice** .

4. Tilføj følgende 'kræver' sætninger i starten af filen **dmpatterns_getstarted_device.js** :

    ```
    'use strict';

    var Client = require('azure-iot-device').Client;
    var Protocol = require('azure-iot-device-mqtt').Mqtt;
    ```

5. Tilføj en **connectionString** variabel og bruge det til at oprette en enhed klient.  Erstat forbindelsesstrengen med din enhed forbindelsesstreng.  

    ```
    var connectionString = 'HostName={youriothostname};DeviceId=myDeviceId;SharedAccessKey={yourdevicekey}';
    var client = Client.fromConnectionString(connectionString, Protocol);
    ```

6. Tilføj følgende funktion for at implementere metoden direkte på enheden

    ```
    var onReboot = function(request, response) {
        
        // Respond the cloud app for the direct method
        response.send(200, 'Reboot started', function(err) {
            if (!err) {
                console.error('An error occured when sending a method response:\n' + err.toString());
            } else {
                console.log('Response to method \'' + request.methodName + '\' sent successfully.');
            }
        });
        
        // Report the reboot before the physical restart
        var date = new Date();
        var patch = {
            iothubDM : {
                reboot : {
                    lastReboot : date.toISOString(),
                }
            }
        };

        // Get device Twin
        client.getTwin(function(err, twin) {
            if (err) {
                console.error('could not get twin');
            } else {
                console.log('twin acquired');
                twin.properties.reported.update(patch, function(err) {
                    if (err) throw err;
                    console.log('Device reboot twin state reported')
                });  
            }
        });
        
        // Add your device's reboot API for physical restart.
        console.log('Rebooting!');
    };
    ```

7. Åbn forbindelsen til dit IoT-hub, og start direkte metode lytteren:

    ```
    client.open(function(err) {
        if (err) {
            console.error('Could not open IotHub client');
        }  else {
            console.log('Client opened.  Waiting for reboot method.');
            client.onDeviceMethod('reboot', onReboot);
        }
    });
    ```
    
8. Gem og Luk filen **dmpatterns_getstarted_device.js** .

 [AZURE.NOTE] Dette selvstudium implementerer for at holde ting enkel, ikke en politik for prøv igen. Fremstilling kode, skal du implementere forsøg politikker (som en eksponentiel backoff), som foreslåede i MSDN-artikel [Midlertidige håndtering af fejl][lnk-transient-faults].

## <a name="trigger-a-remote-reboot-on-the-device-using-a-direct-method"></a>Udløse en remote genstart på enheden ved hjælp af en direkte metode 

I dette afsnit, skal oprette du en Node.js console-app, der starter en remote genstart på en enhed med en direkte metode og bruger enhed dobbelt forespørgsler til at søge efter det sidste genstart tidspunkt for den pågældende enhed.

1. Oprette en ny tom mappe med navnet **triggerrebootondevice**.  Oprette en package.json fil ved hjælp af følgende kommando i din kommandoprompt i mappen **triggerrebootondevice** .  Accepter alle standardværdier:

    ```
    npm init
    ```
    
2. På din kommandoprompt i mappen **triggerrebootondevice** , skal du køre følgende kommando for at installere den **azure-iothub@dtpreview** enhed SDK-pakken og **azure-iot-device-mqtt@dtpreview** pakke:

    ```
    npm install azure-iothub@dtpreview --save
    ```
    
3. Ved hjælp af en teksteditor, oprette en ny **dmpatterns_getstarted_service.js** -fil i mappen **triggerrebootondevice** .

4. Tilføj følgende 'kræver' sætninger i starten af filen **dmpatterns_getstarted_service.js** :

    ```
    'use strict';

    var Registry = require('azure-iothub').Registry;
    var Client = require('azure-iothub').Client;
    ```

5. Tilføj følgende variabelerklæringer og erstatte pladsholder værdier:

    ```
    var connectionString = '{iothubconnectionstring}';
    var registry = Registry.fromConnectionString(connectionString);
    var client = Client.fromConnectionString(connectionString);
    var deviceToReboot = 'myDeviceId';
    ```
    
6. Tilføj følgende funktion for at kalde metoden enhed for at genstarte den destinationsadresse-enhed:

    ```
    var startRebootDevice = function(twin) {

        var methodName = "reboot";
        
        var methodParams = {
            methodName: methodName,
            payload: null,
            timeoutInSeconds: 30
        };
        
        client.invokeDeviceMethod(deviceToReboot, methodParams, function(err, result) {
            if (err) { 
                console.error("Direct method error: "+err.message);
            } else {
                console.log("Successfully invoked the device to reboot.");  
            }
        });
    };
    ```

7. Tilføj følgende funktion for at forespørge efter enheden, og få sidst genstart:

    ```
    var queryTwinLastReboot = function() {

        registry.getTwin(deviceToReboot, function(err, twin){

            if (twin.properties.reported.iothubDM != null)
            {
                if (err) {
                    console.error('Could not query twins: ' + err.constructor.name + ': ' + err.message);
                } else {
                    var lastRebootTime = twin.properties.reported.iothubDM.reboot.lastReboot;
                    console.log('Last reboot time: ' + JSON.stringify(lastRebootTime, null, 2));
                }
            } else 
                console.log('Waiting for device to report last reboot time.');
        });
    };
    ```
    
8. Føj følgende kode for at ringe til de funktioner, der skal udløse genstart direkte metode og forespørgsel til sidst genstart:

    ```
    startRebootDevice();
    setInterval(queryTwinLastReboot, 2000);
    ```
    
9. Gem og Luk filen **dmpatterns_getstarted_service.js** .

## <a name="run-the-applications"></a>Køre programmer

Du er nu klar til at køre programmerne.

1. Kommandoprompten i mappen **manageddevice** skal køre følgende kommando for at begynde at lytte til metoden genstart direkte.

    ```
    node dmpatterns_getstarted_device.js
    ```

2. Kommandoprompten i mappen **triggerrebootondevice** , skal du køre følgende kommando for at udløse remote genstarte og -forespørgsel til enhed med dobbelt til at finde sidst genstart tid.

    ```
    node dmpatterns_getstarted_service.js
    ```

3. Du får vist reagere på den direkte metode ved at udskrive meddelelsen

## <a name="customize-and-extend-the-device-management-actions"></a>Tilpasse og udvide enheden management handlinger

Dine IoT løsninger kan udvide det definerede sæt enhed management mønstre eller aktivere brugerdefinerede mønstre ved hjælp af enhed dobbelt og C2D metode primitiver. Andre eksempler enhed management handlinger er fabriksindstillingerne, firmware-opdatering, softwareopdatering, power management, administration af netværk og forbindelse og kryptering af data.

## <a name="device-maintenance-windows"></a>Enhed vedligeholdelse windows

Typisk, kan du konfigurere enheder for at udføre handlinger på et tidspunkt, der minimerer afbrydelser og nedetid.  Enhed vedligeholdelse windows er et almindeligt anvendte mønster til at definere den tid, når en enhed skal opdatere konfigurationen. Dine back end-løsninger kan bruge de ønskede egenskaber for enheden dobbelt til at definere og aktivere en politik på din enhed, der gør det muligt for et vedligeholdelsesvindue. Når en enhed modtager politikken vedligeholdelse-vinduet, kan det bruge egenskaben anmeldt for enhed dobbelt til at rapportere status for politikken. Back end-app kan derefter bruge enhed dobbelt forespørgsler til at bekræfte til overholdelse af enheder og hver politik.

## <a name="next-steps"></a>Næste trin

I dette selvstudium, du brugte en direkte metode til at udløse en remote genstart på en enhed, bruges enhed dobbelt rapporteret egenskaber for at rapportere sidst genstart fra enheden, og forespørges for enhed med dobbelt at finde sidst genstart af enhed fra skyen.

Hvis du vil fortsætte med at komme i gang med IoT Hub og enhed management mønstre som remote over air firmware-opdatering, i:

[Selvstudium: Sådan foretager du en firmware-opdatering][lnk-fwupdate]

Hvis du vil se, hvordan du udvider din IoT løsning og tidsplan metode opkald på flere enheder, se [tidsplan og broadcast job] [ lnk-tutorial-jobs] selvstudium.

Hvis du vil fortsætte med at komme i gang med IoT-Hub, se [Kom i gang med IoT Gateway SDK][lnk-gateway-SDK].


<!-- images and links -->
[img-output]: media/iot-hub-get-started-with-dm/image6.png
[img-dm-ui]: media/iot-hub-get-started-with-dm/dmui.png

[lnk-dev-setup]: https://github.com/Azure/azure-iot-sdks/blob/master/doc/get_started/node-devbox-setup.md

[lnk-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[lnk-fwupdate]: iot-hub-firmware-update.md
[Azure portal]: https://portal.azure.com/
[Using resource groups to manage your Azure resources]: ../azure-portal/resource-group-portal.md
[lnk-dm-github]: https://github.com/Azure/azure-iot-device-management
[lnk-tutorial-jobs]: iot-hub-schedule-jobs.md
[lnk-gateway-SDK]: iot-hub-linux-gateway-sdk-get-started.md

[lnk-devtwin]: iot-hub-devguide-device-twins.md
[lnk-c2dmethod]: iot-hub-devguide-direct-methods.md
[lnk-transient-faults]: https://msdn.microsoft.com/library/hh680901(v=pandp.50).aspx