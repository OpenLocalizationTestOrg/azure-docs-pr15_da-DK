<properties
    pageTitle="Tilpasse forudkonfigureret løsninger | Microsoft Azure"
    description="Giver vejledning i, hvordan du tilpasser Azure IoT pakke forudkonfigureret løsninger."
    services=""
    suite="iot-suite"
    documentationCenter=".net"
    authors="aguilaaj"
    manager="timlt"
    editor=""/>

<tags
     ms.service="iot-suite"
     ms.devlang="dotnet"
     ms.topic="article"
     ms.tgt_pltfrm="na"
     ms.workload="na"
     ms.date="10/11/2016"
     ms.author="aguilaaj"/>

# <a name="customize-a-preconfigured-solution"></a>Tilpasse en forudkonfigureret løsning

De forudkonfigurerede løsninger, der følger med Azure IoT Suite viser tjenesterne inden for den pakke, der arbejder sammen for at levere en til en komplet løsning. Fra dette udgangspunkt er der flere forskellige steder, hvor du kan udvide og tilpasse løsningen til bestemte scenarier. I følgende afsnit beskrives disse almindelige tilpasning af punkter.

## <a name="finding-the-source-code"></a>Finde kildekoden

Koden til de forudkonfigurerede løsninger findes på GitHub i følgende typer lagre:

- Remote overvågning: [https://www.github.com/Azure/azure-iot-remote-monitoring](https://github.com/Azure/azure-iot-remote-monitoring)
- Skønnet vedligeholdelse: [https://github.com/Azure/azure-iot-predictive-maintenance](https://github.com/Azure/azure-iot-predictive-maintenance)

Koden til de forudkonfigurerede løsninger er angivet til at vise de mønstre og fremgangsmåder, der bruges til at implementere til slut funktionaliteten i en IoT løsning ved hjælp af Azure IoT pakke. Du kan finde flere oplysninger om, hvordan du opretter og implementere løsninger i GitHub typer lagre.

## <a name="changing-the-preconfigured-rules"></a>Ændre de forudkonfigurerede regler

Remote overvågning løsningen indeholder tre [Azure Stream Analytics](https://azure.microsoft.com/services/stream-analytics/) -job for at implementere enhed-oplysninger, telemetri og regler logik vises for løsningen.

De tre stream analytics-job og deres syntaks er beskrevet i dybden i [Remote overvågning forudkonfigurerede løsning gennemgang](iot-suite-remote-monitoring-sample-walkthrough.md). 

Du kan redigere disse job direkte, hvis du vil ændre logikken, eller du kan føje logik bestemte til scenariet. Du kan finde Stream Analytics-job på følgende måde:
 
1. Gå til [Azure-portalen](https://portal.azure.com).
2. Gå til ressourcegruppen med samme navn som din IoT løsning. 
3. Vælg det Azure Stream Analytics-job, du vil ændre. 
4. Stoppe jobbet ved at vælge **Stop**i række kommandoer. 
5. Rediger input, forespørgsel og output.

    En simpel ændring er ved at ændre forespørgslen til kørslen **regler** til at bruge en **"<"** i stedet for en **">"**. Portalen løsning vises stadig **">"** når du redigerer en regel, men vil du bemærke funktionsmåden er spejlvendt på grund af ændringen i den underliggende sag.

6. Starte jobbet

> [AZURE.NOTE] Dashboardet remote overvågning afhænger af bestemte data, så ændringer af job kan forårsage dashboardet mislykkes.

## <a name="adding-your-own-rules"></a>Tilføje dine egne regler

Ud over at ændre de forudkonfigurerede Azure Stream Analytics-job, kan du bruge Azure portal at tilføje nye opgaver og tilføje nye forespørgsler til eksisterende sager.

## <a name="customizing-devices"></a>Tilpasse enheder

En af de mest almindelige lokalnummer aktiviteter arbejder med enheder, der er specifikke for det pågældende scenarie. Der er flere forskellige metoder til at arbejde med enheder. Disse metoder omfatter at ændre en simuleret enhed, så det svarer til det pågældende scenarie, eller ved hjælp af [IoT enhed SDK][] enheden fysisk tilsluttes løsningen.

Se [Iot pakke forbindelse enheder](iot-suite-connecting-devices.md) og [Remote overvågning C SDK eksempel](https://github.com/Azure/azure-iot-sdks/tree/master/c/serializer/samples/remote_monitoring) , der er udviklet til at fungere med remote overvågning forudkonfigurerede løsningen til en trinvis vejledning til at føje enheder til remote overvågning forudkonfigurerede løsningen.

### <a name="creating-your-own-simulated-device"></a>Oprette din egen simuleret enhed

Inkluderet i remote overvågning løsning kildekode (der er nævnt ovenfor), er en .NET simulator. Denne simulator er et klargjort som en del af løsningen og kan ændres for at sende forskellige metadata, telemetri eller svare på forskellige kommandoer.

Den forudkonfigurerede simulator i remote overvågning forudkonfigurerede løsningen er en afkøler enhed, der udsender temperatur og luftfugtighed telemetri, du kan ændre simulator i [Simulator.WebJob](https://github.com/Azure/azure-iot-remote-monitoring/tree/master/Simulator/Simulator.WebJob) projektet, når du har forked GitHub-lager.

### <a name="available-locations-for-simulated-devices"></a>Tilgængelige placeringer til simuleret enheder

Standardsættet af placeringer er i Seattle/Redmond, Washington, USA. Du kan ændre disse placeringer i [SampleDeviceFactory.cs][lnk-sample-device-factory].


### <a name="building-and-using-your-own-physical-device"></a>Opbygge og bruge din egen (fysisk) enhed

[Azure IoT SDK'er](https://github.com/Azure/azure-iot-sdks) giver biblioteker til at oprette forbindelse adskillige enhedstyper (sprog og operativsystemer) til IoT løsninger.

## <a name="modifying-dashboard-limits"></a>Begrænsninger for ændring dashboard

### <a name="number-of-devices-displayed-in-dashboard-dropdown"></a>Antal enheder, der vises på rullelisten dashboard

Standardindstillingen er 200. Du kan ændre dette nummer i [DashboardController.cs][lnk-dashboard-controller].

### <a name="number-of-pins-to-display-in-bing-map-control"></a>Antallet af ben skal vises i kontrolelementet Bing-kort

Standardindstillingen er 200. Du kan ændre dette nummer i [TelemetryApiController.cs][lnk-telemetry-api-controller-01].

### <a name="time-period-of-telemetry-graph"></a>Tidsperiode af telemetri graph

Standardværdien er 10 minutter. Du kan ændre dette i [TelmetryApiController.cs][lnk-telemetry-api-controller-02].

## <a name="manually-setting-up-application-roles"></a>Manuelt konfigurationen af programmet roller

Følgende procedure beskriver, hvordan at tilføje **Administratorer** og **skrivebeskyttet** programmet roller til en forudkonfigureret løsning. Bemærk, at forudkonfigurerede løsninger, der er klargjort fra webstedet azureiotsuite.com allerede indeholder rollerne **Admin** og **skrivebeskyttet** .

Medlemmer af rollen **skrivebeskyttet** kan se dashboardet og listen over enheder, men har ikke tilladelse til at tilføje enheder, ændre enhed attributter eller sende kommandoer.  Medlemmer af **administratorrolle** har fuld adgang til alle funktioner i en løsning.

1. Gå til [Azure klassisk portal][lnk-classic-portal].

2. Vælg **Active Directory**.

3. Klik på navnet på den AAD lejer, du brugte, da du klargjort din løsning.

4. Klik på **programmer**.

5. Klik på navnet på det program, der svarer til løsningsnavnet på din forudkonfigurerede. Hvis du ikke kan se dit program på listen, Vælg **programmer firmaet ejer** i **Vis** Rul ned og klikke på markeringen.

6.  Klik på **Administrer manifestet** og derefter **Hente manifestet**nederst på siden.

7. Dette henter en .json-fil til din lokale computer.  Åbne denne fil til redigering i et tekstredigeringsprogram efter eget valg.

8. På den tredje linje i filen .json, kan du finde:

  ```
  "appRoles" : [],
  ```
  Erstat med følgende:

  ```
  "appRoles": [
  {
  "allowedMemberTypes": [
  "User"
  ],
  "description": "Administrator access to the application",
  "displayName": "Admin",
  "id": "a400a00b-f67c-42b7-ba9a-f73d8c67e433",
  "isEnabled": true,
  "value": "Admin"
  },
  {
  "allowedMemberTypes": [
  "User"
  ],
  "description": "Read only access to device information",
  "displayName": "Read Only",
  "id": "e5bbd0f5-128e-4362-9dd1-8f253c6082d7",
  "isEnabled": true,
  "value": "ReadOnly"
  } ],
  ```

9. Gem filen opdaterede .json (du kan overskrive den eksisterende fil).

10.  Vælg **Administrer manifestet** derefter **Overføre manifestet** til at overføre filen .json, du har gemt i det forrige trin i portalen Azure Management nederst på siden.

11. Du er nu tilføjet rollerne **Admin** og **skrivebeskyttet** i dit program.

12. Hvis du vil tildele en af disse roller for en bruger i mappen, se [tilladelser på webstedet azureiotsuite.com][lnk-permissions].

## <a name="feedback"></a>Feedback

Har du en tilpasning du gerne vil se er omfattet af dette dokument? Tilføj funktion forslag til [Bruger tale](https://feedback.azure.com/forums/321918-azure-iot)eller kommentar til artiklen nedenfor. 

## <a name="next-steps"></a>Næste trin

Hvis du vil vide mere om indstillinger til tilpasning af de forudkonfigurerede løsninger, i:

- [Oprette forbindelse logik App til din Azure IoT pakke Remote overvågning forudkonfigureret løsning][lnk-logicapp]
- [Brug dynamiske telemetri med remote overvågning forudkonfigureret løsning][lnk-dynamic]
- [Enhed oplysninger metadata i remote overvågning forudkonfigureret løsning][lnk-devinfo]

[lnk-logicapp]: iot-suite-logic-apps-tutorial.md
[lnk-dynamic]: iot-suite-dynamic-telemetry.md
[lnk-devinfo]: iot-suite-remote-monitoring-device-info.md

[IoT enhed SDK]: https://azure.microsoft.com/documentation/articles/iot-hub-sdks-summary/
[lnk-permissions]: iot-suite-permissions.md
[lnk-dashboard-controller]: https://github.com/Azure/azure-iot-remote-monitoring/blob/3fd43b8a9f7e0f2774d73f3569439063705cebe4/DeviceAdministration/Web/Controllers/DashboardController.cs#L27
[lnk-telemetry-api-controller-01]: https://github.com/Azure/azure-iot-remote-monitoring/blob/3fd43b8a9f7e0f2774d73f3569439063705cebe4/DeviceAdministration/Web/WebApiControllers/TelemetryApiController.cs#L27
[lnk-telemetry-api-controller-02]: https://github.com/Azure/azure-iot-remote-monitoring/blob/e7003339f73e21d3930f71ceba1e74fb5c0d9ea0/DeviceAdministration/Web/WebApiControllers/TelemetryApiController.cs#L25 
[lnk-sample-device-factory]: https://github.com/Azure/azure-iot-remote-monitoring/blob/master/Common/Factory/SampleDeviceFactory.cs#L40
[lnk-classic-portal]: https://manage.windowsazure.com
