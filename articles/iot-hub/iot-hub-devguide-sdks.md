<properties
 pageTitle="Udvikler vejledning - IoT Hub SDK'er | Microsoft Azure"
 description="Azure IoT Hub udvikler guide – oplysninger om og links til de forskellige Azure IoT Hub enheder og tjenester SDK'er."
 services="iot-hub"
 documentationCenter=""
 authors="dominicbetts"
 manager="timlt"
 editor=""/>

<tags
 ms.service="iot-hub"
 ms.devlang="multiple"
 ms.topic="article"
 ms.tgt_pltfrm="na"
 ms.workload="na"
 ms.date="09/30/2016"
 ms.author="dobett"/>

# <a name="iot-hub-sdks"></a>IoT Hub SDK'er

## <a name="iot-hub-device-sdks"></a>IoT Hub enhed SDK'er

Microsoft Azure IoT enheden SDK'er indeholder kode, der gør det nemmere at dokumentkomponent enheder og programmer, oprette forbindelse til, og der administreres af Azure IoT Hub services.

Følgende IoT enhed SDK'er er kan hentes fra GitHub:

- [Azure IoT enhed SDK til C] [ lnk-c-device-sdk] skrevet i ANSI C (C99) til mobilitet og generelle Platformkompatibilitet.
- [Azure IoT enhed SDK til .NET][lnk-dotnet-device-sdk]
- [Azure IoT enhed SDK til Java][lnk-java-device-sdk]
- [Azure IoT enhed SDK til Node.js][lnk-node-device-sdk]
- [Microsoft Azure IoT enhed SDK til Python 2.7][lnk-python-device-sdk]

> [AZURE.NOTE] Se Vigtigt-filer i GitHub typer lagre oplysninger om brug af sprog og platform-specifikke pakke ledere til at installere binære filer og afhængigheder på din computer og udvikling.

## <a name="os-platforms-and-hardware-compatibility"></a>OS-platforme og hardware kompatibilitet

Du kan finde flere oplysninger om SDK kompatibilitet med bestemt hardwareenheder, se [Azure certificeret for IoT enhed katalog][lnk-certified].

## <a name="iot-hub-service-sdks"></a>IoT Hub service SDK'er

Microsoft Azure IoT service SDK'er indeholder kode, der letter dokumentkomponent programmer, som kommunikerer direkte med IoT Hub til administration af enheder og sikkerhed.

Følgende IoT tjeneste SDK'er er kan hentes fra GitHub:

- [Azure IoT service SDK til .NET][lnk-dotnet-service-sdk]
- [Azure IoT service SDK for Node.js][lnk-node-service-sdk]
- [Azure IoT service SDK til Java][lnk-java-service-sdk]

> [AZURE.NOTE] Se Vigtigt-filer i GitHub typer lagre oplysninger om brug af sprog og platform-specifikke pakke ledere til at installere binære filer og afhængigheder på din computer og udvikling.

## <a name="azure-iot-gateway-sdk"></a>Azure IoT Gateway SDK

Dette Azure IoT Gateway SDK indeholder infrastruktur og moduler til at oprette IoT gateway løsninger. Du kan udvide SDK for at oprette gateways, der er skræddersyet til en hvilken som helst til slut scenarie.

Du kan hente [Azure IoT Gateway SDK] [ lnk-gateway-sdk] fra GitHub.

## <a name="online-api-reference-documentation"></a>Onlinedokumentation API reference

Følgende er en liste over links til online API referencedokumentation til Azure IoT enhed, tjeneste og gateway biblioteker:

- [Internet ting (IoT) .NET][lnk-dotnet-ref]
- [IoT Hub RESTEN][lnk-rest-ref]
- [Azure IoT enhed SDK til C][lnk-c-ref]
- [Azure IoT enhed SDK til Java][lnk-java-ref]
- [Azure IoT service SDK til Java][lnk-java-service-ref]
- [Azure IoT enhed SDK til Node.js][lnk-node-ref]
- [Azure IoT service SDK for Node.js][lnk-node-service-ref]
- [Azure IoT gateway SDK][lnk-gateway-ref]

## <a name="next-steps"></a>Næste trin

Andre referenceemner i i denne IoT Hub udvikler vejledning omfatter:

- [IoT Hub slutpunkter][lnk-devguide-endpoints]
- [Forespørgselssprog til twins, metoder og job][lnk-devguide-query]
- [Kvotaer og (throttling)][lnk-devguide-quotas]
- [IoT Hub MQTT support][lnk-devguide-mqtt]

<!-- Links and images -->

[lnk-c-device-sdk]: https://github.com/Azure/azure-iot-sdks/blob/master/c/readme.md
[lnk-dotnet-device-sdk]: https://github.com/Azure/azure-iot-sdks/blob/master/csharp/device/readme.md
[lnk-java-device-sdk]: https://github.com/Azure/azure-iot-sdks/blob/master/java/device/readme.md
[lnk-dotnet-service-sdk]: https://github.com/Azure/azure-iot-sdks/blob/master/csharp/service/README.md
[lnk-java-service-sdk]: https://github.com/Azure/azure-iot-sdks/blob/master/java/service/readme.md
[lnk-node-device-sdk]: https://github.com/Azure/azure-iot-sdks/blob/master/node/device/readme.md
[lnk-node-service-sdk]: https://github.com/Azure/azure-iot-sdks/blob/master/node/service/README.md
[lnk-python-device-sdk]: https://github.com/Azure/azure-iot-sdks/blob/master/python/device/readme.md
[lnk-certified]: https://catalog.azureiotsuite.com/
[lnk-gateway-sdk]: https://github.com/Azure/azure-iot-gateway-sdk/blob/master/README.md

[lnk-dotnet-ref]: https://msdn.microsoft.com/library/mt488521.aspx
[lnk-c-ref]: http://azure.github.io/azure-iot-sdks/c/api_reference/index.html
[lnk-java-ref]: http://azure.github.io/azure-iot-sdks/java/device/api_reference/index.html
[lnk-node-ref]: http://azure.github.io/azure-iot-sdks/node/api_reference/azure-iot-device/1.0.15/index.html
[lnk-rest-ref]: https://msdn.microsoft.com/library/mt548492.aspx
[lnk-java-service-ref]: http://azure.github.io/azure-iot-sdks/java/service/api_reference/index.html
[lnk-node-service-ref]: http://azure.github.io/azure-iot-sdks/node/api_reference/azure-iothub/1.0.17/index.html
[lnk-gateway-ref]: http://azure.github.io/azure-iot-gateway-sdk/api_reference/c/html/

[lnk-devguide-endpoints]: iot-hub-devguide-endpoints.md
[lnk-devguide-quotas]: iot-hub-devguide-quotas-throttling.md
[lnk-devguide-query]: iot-hub-devguide-query-language.md
[lnk-devguide-mqtt]: iot-hub-mqtt-support.md