<properties
    pageTitle="Bruge dynamisk telemetri | Microsoft Azure"
    description="Følge dette selvstudium for at lære at bruge dynamiske telemetri med remote overvågning forudkonfigurerede løsningen."
    services=""
    suite="iot-suite"
    documentationCenter=""
    authors="dominicbetts"
    manager="timlt"
    editor=""/>

<tags
     ms.service="iot-suite"
     ms.devlang="na"
     ms.topic="article"
     ms.tgt_pltfrm="na"
     ms.workload="na"
     ms.date="08/25/2016"
     ms.author="dobett"/>

# <a name="use-dynamic-telemetry-with-the-remote-monitoring-preconfigured-solution"></a>Brug dynamiske telemetri med remote overvågning forudkonfigureret løsning

## <a name="introduction"></a>Introduktion

Dynamisk telemetri gør det muligt at visualisere en hvilken som helst telemetri, der er sendt til remote overvågning forudkonfigurerede løsningen. De simulerede enheder, der installerer med løsningen forudkonfigurerede send temperatur og luftfugtighed telemetri, som du kan visualisere på dashboardet. Hvis du tilpasse de eksisterende simulerede enheder, oprette nye simuleret enheder eller forbinde fysiske enheder til den forudkonfigurerede løsning kan du sende andre telemetri værdier som den eksterne temperaturen, RPM eller vindhastighed. Du kan derefter visualisere denne yderligere telemetri på dashboardet.

Dette selvstudium bruger en simpel Node.js simuleret enhed, kan du nemt ændre for at eksperimentere med dynamiske telemetri.

For at fuldføre dette selvstudium, skal du:

- Et aktivt Azure abonnement. Hvis du ikke har en konto, kan du oprette en gratis prøveversion konto på blot et par minutter. Yderligere oplysninger finder du [Azure gratis prøveversion][lnk_free_trial].
- [Node.js] [ lnk-node] version 0.12.x eller nyere.

Du kan selv udføre dette selvstudium på operativsystemer, som Windows eller Linux, hvor du kan installere Node.js.

[AZURE.INCLUDE [iot-suite-provision-remote-monitoring](../../includes/iot-suite-provision-remote-monitoring.md)]

## <a name="configure-the-nodejs-simulated-device"></a>Konfigurere Node.js simuleret enheden

1. Klik på **+ Tilføj en enhed** på dashboardet til til remote overvågning og derefter tilføje en brugerdefineret enhed. Skal du notere af IoT Hub hostname, enheds-id og nøglen enhed. Du bruge dem senere i dette selvstudium, når du klargør klientprogrammet remote_monitoring.js enhed.

2. Sørg for, at Node.js version 0.12.x eller nyere er installeret på computeren udvikling. Køre `node --version` fra en kommandoprompt eller i en shell til at kontrollere, hvilken version. Finde oplysninger om brug af en pakke manager til at installere Node.js på Linux, under [Installation af Node.js via pakke manager][node-linux].

3. Når du har installeret Node.js, klone den seneste version af [azure-iot-SDK'er] [ lnk-github-repo] lager til din computer og udvikling. Brug altid den **overordnede** gren til den nyeste version af biblioteker og eksempler.

4. Fra din lokale kopi af [azure-iot-SDK'er] [ lnk-github-repo] lager, kopiere de følgende to filer fra mappen node/enhed/eksempler til en tom mappe på computeren udvikling:

  - Packages.JSON
  - remote_monitoring.js

5. Åbn filen remote_monitoring.js, og se efter følgende variable definitionen:

    ```
    var connectionString = "[IoT Hub device connection string]";
    ```

6. Erstat **[IoT Hub enhed forbindelsesstreng]** med din enhed forbindelsesstreng. Brug værdierne for din IoT Hub hostname, enheds-id og nøglen til enhed, du har foretaget noter i trin 1. En enhed forbindelsesstreng har følgende format:

    ```
    HostName={your IoT Hub hostname};DeviceId={your device id};SharedAccessKey={your device key}
    ```

    Hvis din IoT Hub hostname er **contoso** , og din enheds-id er **mydevice**, ser din forbindelsesstreng ud som følger:

    ```
    var connectionString = "HostName=contoso.azure-devices.net;DeviceId=mydevice;SharedAccessKey=2s ... =="
    ```

7. Gem filen. Kør følgende kommandoer i en shell eller kommandoprompten i den mappe, der indeholder disse filer for at installere de nødvendige pakker og derefter køre eksempelprogrammet:

    ```
    npm install
    node remote_monitoring.js
    ```

## <a name="observe-dynamic-telemetry-in-action"></a>Overhold dynamisk telemetri i praksis

Dashboard viser temperatur og luftfugtighed telemetri fra de eksisterende simulerede enheder:

![Standarddashboard][image1]

Hvis du vælger Node.js simuleret enheden du kørte i det forrige afsnit, kan du se temperatur, fugtighed og eksterne temperaturen telemetri:

![Føje eksterne temperaturen til dashboardet][image2]

Remote overvågning løsningen registrerer typen yderligere eksterne temperaturen telemetri og føjer den til diagrammet på dashboardet til automatisk.

## <a name="add-a-telemetry-type"></a>Tilføje en telemetri type

Det næste trin er at erstatte telemetri genereres af Node.js simuleret enhed med et nyt sæt af værdier:

1. Stoppe Node.js simuleret enheden ved at skrive **Ctrl + C** i kommandoprompten eller shell.

2. I filen remote_monitoring.js, kan du se de grundlæggende værdier for de eksisterende temperatur, fugtighed og eksterne temperaturen telemetri. Tilføj en grundlæggende dataværdi for **rpm** på følgende måde:

    ```
    // Sensors data
    var temperature = 50;
    var humidity = 50;
    var externalTemperature = 55;
    var rpm = 200;
    ```

3. Node.js simuleret enheden bruger funktionen **generateRandomIncrement** i filen remote_monitoring.js til at føje et tilfældigt interval til de grundlæggende dataværdier. Tilfældig rækkefølge værdien **rpm** ved at tilføje en linje af kode efter de eksisterende randomizations på følgende måde:

    ```
    temperature += generateRandomIncrement();
    externalTemperature += generateRandomIncrement();
    humidity += generateRandomIncrement();
    rpm += generateRandomIncrement();
    ```

4. Tilføje den nye rpm værdi til JSON overførslen enheden sender til IoT Hub:

    ```
    var data = JSON.stringify({
      'DeviceID': deviceId,
      'Temperature': temperature,
      'Humidity': humidity,
      'ExternalTemperature': externalTemperature,
      'RPM': rpm
    });
    ```

5. Kør Node.js simuleret enheden ved hjælp af følgende kommando:

    ```
    node remote_monitoring.js
    ```

6. Læg mærke til den nye RPM telemetri type, der vises i diagrammet i dashboardet:

![Tilføje RPM til dashboard][image3]

> [AZURE.NOTE] Du skal muligvis deaktivere og derefter aktivere Node.js enheden på siden **enheder** i dashboardet bruges til at se ændringerne med det samme.

## <a name="customize-the-dashboard-display"></a>Tilpasse visningen dashboard

**Enhed-Info** meddelelsen kan medtage metadata om telemetri enheden kan sende til IoT Hub. Disse metadata kan angive typerne telemetri sender enheden. Ændre værdien **deviceMetaData** i filen remote_monitoring.js medtage en **Telemetri** definition efter fastsættelsen **kommandoer** . Følgende kodestykke viser **kommandoer** definitionen (Husk at tilføje en `,` efter **kommandoer** definitionen):

```
'Commands': [{
  'Name': 'SetTemperature',
  'Parameters': [{
    'Name': 'Temperature',
    'Type': 'double'
  }]
},
{
  'Name': 'SetHumidity',
  'Parameters': [{
    'Name': 'Humidity',
    'Type': 'double'
  }]
}],
'Telemetry': [{
  'Name': 'Temperature',
  'Type': 'double'
},
{
  'Name': 'Humidity',
  'Type': 'double'
},
{
  'Name': 'ExternalTemperature',
  'Type': 'double'
}]
```

> [AZURE.NOTE] Remote overvågning løsningen bruger en case-insensitive match til at sammenligne metadata definitionen med data i telemetri strømmen.

Tilføje en **Telemetri** definition, som vist i det foregående kodestykke ændrer ikke funktionsmåden for dashboard. Metadata, der kan også indeholde attributten **vist navn** for at tilpasse visningen i dashboardet. Opdatere **Telemetri** metadata definitionen, som vist i følgende kodestykket:

```
'Telemetry': [
{
  'Name': 'Temperature',
  'Type': 'double',
  'DisplayName': 'Temperature (C*)'
},
{
  'Name': 'Humidity',
  'Type': 'double',
  'DisplayName': 'Humidity (relative)'
},
{
  'Name': 'ExternalTemperature',
  'Type': 'double',
  'DisplayName': 'Outdoor Temperature (C*)'
}
]
```

Følgende skærmbillede viser, hvordan denne ændring ændrer diagramforklaringen på dashboardet til:

![Tilpasse diagramforklaringen][image4]

> [AZURE.NOTE] Du skal muligvis deaktivere og derefter aktivere Node.js enheden på siden **enheder** i dashboardet bruges til at se ændringerne med det samme.

## <a name="filter-the-telemetry-types"></a>Filtrer typerne telemetri

Som standard viser diagrammet på dashboardet til hver dataserie i telemetri strømmen. Du kan bruge **Enhed-Info** -metadata til at skjule visningen af bestemte telemetri typer i diagrammet. 

Hvis du vil gøre diagrammet viser kun temperatur og luftfugtighed telemetri, du Udelad **ExternalTemperature** fra **Enhed-Info** **Telemetri** metadata på følgende måde:

```
'Telemetry': [
{
  'Name': 'Temperature',
  'Type': 'double',
  'DisplayName': 'Temperature (C*)'
},
{
  'Name': 'Humidity',
  'Type': 'double',
  'DisplayName': 'Humidity (relative)'
},
//{
//  'Name': 'ExternalTemperature',
//  'Type': 'double',
//  'DisplayName': 'Outdoor Temperature (C*)'
//}
]
```

**Udendørs temperaturen** vises ikke længere i diagrammet:

![Filtrere telemetri på dashboardet][image5]

Denne ændring påvirker kun visningen af diagrammet. **ExternalTemperature** dataværdierne er stadig gemt og gjort tilgængelige for en hvilken som helst back end-behandling.

> [AZURE.NOTE] Du skal muligvis deaktivere og derefter aktivere Node.js enheden på siden **enheder** i dashboardet bruges til at se ændringerne med det samme.

## <a name="handle-errors"></a>Håndtere fejl

Til en datastream skal vises i diagrammet, skal den **Type** i **Enhed-Info** metadataene svarer til datatypen af telemetri værdierne. Eksempelvis hvis metadata, der angiver, at **typen** data, luftfugtighed er **int** og **dobbelte** findes i telemetri strømmen vises derefter luftfugtighed telemetri ikke i diagrammet. **Luftfugtighed** værdierne gemmes dog stadig og gjort tilgængelige for en hvilken som helst back end-behandling.

## <a name="next-steps"></a>Næste trin

Nu, hvor du har set hvordan du bruger dynamiske telemetri, du kan få mere at vide om, hvordan de forudkonfigurerede løsninger bruge enhedsoplysninger: [enhed oplysninger metadata i eksterne overvågning forudkonfigurerede løsning][lnk-devinfo].

[lnk-devinfo]: iot-suite-remote-monitoring-device-info.md

[image1]: media/iot-suite-dynamic-telemetry/image1.png
[image2]: media/iot-suite-dynamic-telemetry/image2.png
[image3]: media/iot-suite-dynamic-telemetry/image3.png
[image4]: media/iot-suite-dynamic-telemetry/image4.png
[image5]: media/iot-suite-dynamic-telemetry/image5.png

[lnk_free_trial]: http://azure.microsoft.com/pricing/free-trial/
[lnk-node]: http://nodejs.org
[node-linux]: https://github.com/nodejs/node-v0.x-archive/wiki/Installing-Node.js-via-package-manager
[lnk-github-repo]: https://github.com/Azure/azure-iot-sdks
