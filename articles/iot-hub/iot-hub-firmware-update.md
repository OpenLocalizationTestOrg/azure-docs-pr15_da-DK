<properties
 pageTitle="Sådan foretager du en firmware-opdatering | Microsoft Azure"
 description="Dette selvstudium viser, hvordan du gør en firmware-opdatering"
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

# <a name="tutorial-how-to-do-a-firmware-update-preview"></a>Selvstudium: Sådan foretager du en firmware opdatering (preview)

## <a name="introduction"></a>Introduktion
[Komme i gang med administration af enheder] [ lnk-dm-getstarted] selvstudium, så du, hvordan du bruger en [enhed med dobbelt] [ lnk-devtwin] og [skyen-til-enhed (C2D) metoder] [ lnk-c2dmethod] primitiver fra en fjernplacering genstarte en enhed. Dette selvstudium bruger de samme IoT Hub primitiver og giver vejledning og viser, hvordan du gør en firmwareopdatering til slut simuleret.  Dette mønster bruges i firmware opdatering implementering til eksemplet Intel Edison enhed.

Dette selvstudium viser, hvordan til:

- Oprette et console-program, der kalder metoden firmwareUpdate direkte på simuleret enheden via din IoT hub.
- Oprette en simuleret enhed, der implementerer et direkte firmwareUpdate metode, der fører dig gennem en med flere faser proces, der venter på at hente firmware billedet, skal du henter firmware billedet, og til sidst gælder de firmware billede.  Status ændres i hele afholde hvert trin for enhed bruger to rapporteret egenskaber for at opdatere.

I slutningen af dette selvstudium har du to Node.js console-programmer:

**dmpatterns_fwupdate_service.js**, som kalder en direkte metode på simuleret enheden, viser svaret, og med jævne mellemrum (hver 500ms) viser de opdaterede enhed med dobbelt rapporteret egenskaber.

**dmpatterns_fwupdate_device.js**, som opretter forbindelse til din IoT hub med enhed identitet oprettede tidligere, modtager en firmwareUpdate direkte metode, kører via en med flere tilstande til at simulere, herunder en firmware opdatering: venter til hentning af billedet, hente det nye billede og til sidst anvende billedet.


For at fuldføre dette selvstudium skal bruge du følgende:

Node.js version 0.12.x eller nyere <br/>  [Forberede din udviklingsmiljø] [ lnk-dev-setup] beskriver, hvordan du installerer Node.js til dette selvstudium på Windows eller Linux.

En active Azure-konto. (Hvis du ikke har en konto, kan du oprette en [gratis konto] [ lnk-free-trial] på blot et par minutter.)

Følg i artiklen [Introduktion til administration af enheder](iot-hub-device-management-get-started.md) for at oprette din IoT hub og få vist din forbindelsesstreng.

[AZURE.INCLUDE [iot-hub-get-started-create-hub-pp](../../includes/iot-hub-get-started-create-hub-pp.md)]

## <a name="create-a-simulated-device-app"></a>Oprette en simuleret enhed app

I dette afsnit, skal du oprette en Node.js console-app, der svarer til en direkte metode, der kaldes af i skyen, der udløser en simuleret enhed firmware-opdatering og bruger enhed dobbelt rapporteret egenskaber for at aktivere enhed dobbelt forespørgsler til at identificere enheder, og når de sidste genstartet.

1. Oprette en ny tom mappe med navnet **manageddevice**.  Oprette en package.json fil ved hjælp af følgende kommando i din kommandoprompt i mappen **manageddevice** .  Accepter alle standardværdier:

    ```
    npm init
    ```
    
2. På din kommandoprompt i mappen **manageddevice** , skal du køre følgende kommando for at installere den **azure-iot-device@dtpreview** enhed SDK-pakken og **azure-iot-device-mqtt@dtpreview** pakke:

    ```
    npm install azure-iot-device@dtpreview azure-iot-device-mqtt@dtpreview --save
    ```

3. Ved hjælp af en teksteditor, oprette en ny **dmpatterns_fwupdate_device.js** -fil i mappen **manageddevice** .

4. Tilføj følgende 'kræver' sætninger i starten af filen **dmpatterns_fwupdate_device.js** :

    ```
    'use strict';

    var Client = require('azure-iot-device').Client;
    var Protocol = require('azure-iot-device-mqtt').Mqtt;
    ```

5. Tilføj en **connectionString** variabel og bruge det til at oprette en enhed klient.  

    ```
    var connectionString = 'HostName={youriothostname};DeviceId=myDeviceId;SharedAccessKey={yourdevicekey}';
    var client = Client.fromConnectionString(connectionString, Protocol);
    ```

6. Tilføj følgende funktion, der skal bruges til at opdatere dobbelt rapporteret egenskaber

    ```
    var reportFWUpdateThroughTwin = function(twin, firmwareUpdateValue) {
      var patch = {
          iothubDM : {
            firmwareUpdate : firmwareUpdateValue
          }
      };

      twin.properties.reported.update(patch, function(err) {
        if (err) throw err;
        console.log('twin state reported')
      });
    };
    ```
    
7. Tilføj følgende funktioner som vil simulere overførslen og anvende billedets firmware.

    ```
    var simulateDownloadImage = function(imageUrl, callback) {
      var error = null;
      var image = "[fake image data]";
      
      console.log("Downloading image from " + imageUrl);
      
      callback(error, image);
    }

    var simulateApplyImage = function(imageData, callback) {
      var error = null;
      
      if (!imageData) {
        error = {message: 'Apply image failed because of missing image data.'};
      }
      
      callback(error);
    }
    ```

8. Tilføj følgende funktion som opdaterer firmware opdateringsstatus gennem to rapporteret egenskaber til venter på at hente.  Typisk enheder er informeres om en tilgængelig opdatering og en administrator defineret politik årsager kameraet, for at begynde at downloade og anvende opdateringen.  Dette er hvor logik til at aktivere denne politik skal køre.  For enkel, vi forsinke til 4 sekunder og derefter begynde at downloade firmware billedet. 

    ```
    var waitToDownload = function(twin, fwPackageUriVal, callback) {
      var now = new Date();
      
      reportFWUpdateThroughTwin(twin, {
        fwPackageUri: fwPackageUriVal,
        status: 'waiting',
        error : null,
        startedWaitingTime : now.toISOString()
      });
      setTimeout(callback, 4000);
    };
    ```
    
9. Tilføj følgende funktion som opdaterer firmware opdateringsstatus gennem to rapporteres egenskaber skal hente firmware billedet.  Det følger ved at simulere hentning af en firmware og til sidst opdateres opdateringsstatus firmware til at informere af enten en Hent lykkes eller mislykkes.

    ```
    var downloadImage = function(twin, fwPackageUriVal, callback) {
      var now = new Date();   
      
      reportFWUpdateThroughTwin(twin, {
        status: 'downloading',
      });
      
      setTimeout(function() {
        // Simulate download
        simulateDownloadImage(fwPackageUriVal, function(err, image) {
          
          if (err)
          {
            reportFWUpdateThroughTwin(twin, {
              status: 'downloadfailed',
              error: {
                code: error_code,
                message: error_message,
              }
            });
          }
          else {        
            reportFWUpdateThroughTwin(twin, {
              status: 'downloadComplete',
              downloadCompleteTime: now.toISOString(),
            });
            
            setTimeout(function() { callback(image); }, 4000);   
          }
        });
        
      }, 4000);
    }
    ```
    
10. Tilføj følgende funktion som opdaterer firmware opdateringsstatus gennem to rapporteres egenskaber skal anvende firmware billedet.  Det følger ved at simulere en anvendelse af firmware billedet og til sidst opdateres opdateringsstatus firmware til at informere af enten en Anvend lykkes eller mislykkes.

    ```
    var applyImage = function(twin, imageData, callback) {
      var now = new Date();   
      
      reportFWUpdateThroughTwin(twin, {
        status: 'applying',
        startedApplyingImage : now.toISOString()
      });
      
      setTimeout(function() {
        
        // Simulate apply firmware image
        simulateApplyImage(imageData, function(err) {
          if (err) {
            reportFWUpdateThroughTwin(twin, {
              status: 'applyFailed',
              error: {
                code: err.error_code,
                message: err.error_message,
              }
            });
          } else { 
            reportFWUpdateThroughTwin(twin, {
              status: 'applyComplete',
              lastFirmwareUpdate: now.toISOString()
            });    
            
          }
        });
        
        setTimeout(callback, 4000);
        
      }, 4000);
    }
    ```

11. Tilføj følgende functoin som håndtere metoden firmwareUpdate og starte opdateringsprocessen med flere faser firmware.

    ```
    var onFirmwareUpdate = function(request, response) {
            
      // Respond the cloud app for the direct method
      response.send(200, 'FirmwareUpdate started', function(err) {
        if (!err) {
          console.error('An error occured when sending a method response:\n' + err.toString());
        } else {
          console.log('Response to method \'' + request.methodName + '\' sent successfully.');
        }
      });

      // Get the parameter from the body of the method request
      var fwPackageUri = JSON.parse(request.payload).fwPackageUri;

      // Obtain the device twin
      client.getTwin(function(err, twin) {
        if (err) {
          console.error('Could not get device twin.');
        } else {
          console.log('Device twin acquired.');
          
          // Start the multi-stage firmware update
          waitToDownload(twin, fwPackageUri, function() {
            downloadImage(twin, fwPackageUri, function(imageData) {
              applyImage(twin, imageData, function() {});    
            });  
          });

        }
      });
    }
    ```
    
12. Til sidst skal du tilføje følgende kode som opretter forbindelse til IoT hub som en enhed 

    ```
    client.open(function(err) {
      if (err) {
        console.error('Could not connect to IotHub client');
      }  else {
        console.log('Client connected to IoT Hub.  Waiting for firmwareUpdate direct method.');
      }
      
      client.onDeviceMethod('firmwareUpdate', onFirmwareUpdate(request, response));
    });
    ```

> [AZURE.NOTE] Dette selvstudium implementerer for at holde ting enkel, ikke en politik for prøv igen. Fremstilling kode, skal du implementere retry-politikker (som en eksponentiel backoff), som foreslåede i MSDN-artikel [Midlertidige håndtering af fejl][lnk-transient-faults].

## <a name="trigger-a-remote-firmware-update-on-the-device-using-a-direct-method"></a>Udløse en remote firmware-opdatering på enheden ved hjælp af en direkte metode 

I dette afsnit, skal oprette du en Node.js console-app, der starter en remote firmware-opdatering på en enhed med en direkte metode og bruger enhed dobbelt forespørgsler til at hente med jævne mellemrum status for det aktive firmware-opdatering på enheden.


1. Oprette en ny tom mappe med navnet **triggerfwupdateondevice**.  Oprette en package.json fil ved hjælp af følgende kommando i din kommandoprompt i mappen **triggerfwupdateondevice** .  Accepter alle standardværdier:

    ```
    npm init
    ```
    
2. På din kommandoprompt i mappen **triggerfwupdateondevice** skal køre følgende kommando for at installere den **azure-iothub** enhed SDK pakke og **azure-iot-enhed – mqtt** pakke:

    ```
    npm install azure-iot-hub@dtpreview --save
    ```
    
3. Ved hjælp af en teksteditor, oprette en ny **dmpatterns_getstarted_service.js** -fil i mappen **triggerfwupdateondevice** .

4. Tilføj følgende 'kræver' sætninger i starten af filen **dmpatterns_getstarted_service.js** :

    ```
    'use strict';

    var Registry = require('azure-iothub').Registry;
    var Client = require('azure-iothub').Client;
    ```

5. Tilføj følgende variabelerklæringer og erstatte pladsholder værdier:

    ```
    var connectionString = '{device_connectionstring}';
    var registry = Registry.fromConnectionString(connectionString);
    var client = Client.fromConnectionString(connectionString);
    var deviceToUpdate = 'myDeviceId';
    ```
    
6. Tilføj følgende funktion til at finde og Vis værdien af firmwareUpdate rapporteret egenskab.

    ```
    var queryTwinFWUpdateReported = function() {
        registry.getTwin(deviceToUpdate, function(err, twin){
            if (err) {
              console.error('Could not query twins: ' + err.constructor.name + ': ' + err.message);
            } else {
              console.log((JSON.stringify(twin.properties.reported.iothubDM.firmwareUpdate)) + "\n");
            }
        });
    };
    ```

7. Tilføj følgende funktion for at kalde metoden firmwareUpdate for at genstarte den destinationsadresse-enhed:

    ```
    var startFirmwareUpdateDevice = function() {
      var params = {
          fwPackageUri: 'https://secureurl'
      };
      
      var methodName = "firmwareUpdate";
      var payloadData =  JSON.stringify(params);
      
      var methodParams = {
        methodName: methodName,
        payload: payloadData,
        timeoutInSeconds: 30
      };
      
      client.invokeDeviceMethod(deviceToUpdate, methodParams, function(err, result) {
        if (err) {
          console.error('Could not start the firmware update on the device: ' + err.message)
        } 
      });
    };
    ```

8. Tilføj følgende funktion til kode til at starte firmwaren opdatere sekvens og begynder at dukke med jævne mellemrum dobbelt rapporteret til sidst skal egenskaber:

    ```
    startFirmwareUpdateDevice();
    setInterval(queryTwinFWUpdateReported, 500);
    ```
    
9. Gem og Luk filen **dmpatterns_fwupdate_service.js** .

## <a name="run-the-applications"></a>Køre programmer

Du er nu klar til at køre programmerne.

1. Kommandoprompten i mappen **manageddevice** skal køre følgende kommando for at begynde at lytte til metoden genstart direkte.

    ```
    node dmpatterns_fwupdate_device.js
    ```

2. Kommandoprompten i mappen **triggerfwupdateondevice** , skal du køre følgende kommando for at udløse remote genstarte og -forespørgsel til enhed med dobbelt til at finde sidst genstart tid.

    ```
    node dmpatterns_fwupdate_service.js
    ```

3. Du får vist reagere på den direkte metode ved at udskrive meddelelsen

## <a name="next-steps"></a>Næste trin

I dette selvstudium, du brugte en direkte metode til at udløse en remote firmware-opdatering på en enhed og med jævne mellemrum bruges to rapporteret egenskaber for at forstå statussen for firmwaren opdatere proces.  

Hvis du vil se, hvordan du udvider din IoT løsning og tidsplan metode opkald på flere enheder, se [tidsplan og broadcast job] [ lnk-tutorial-jobs] selvstudium.

[lnk-devtwin]: iot-hub-devguide-device-twins.md
[lnk-c2dmethod]: iot-hub-devguide-direct-methods.md
[lnk-dm-getstarted]: iot-hub-device-management-get-started.md
[lnk-tutorial-jobs]: iot-hub-schedule-jobs.md

[lnk-dev-setup]: https://github.com/Azure/azure-iot-sdks/blob/master/doc/get_started/node-devbox-setup.md
[lnk-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[lnk-transient-faults]: https://msdn.microsoft.com/library/hh680901(v=pandp.50).aspx
