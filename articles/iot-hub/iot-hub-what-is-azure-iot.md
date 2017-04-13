<properties
 pageTitle="Azure-løsninger til Internet ting | Microsoft Azure"
 description="En oversigt over IoT på Azure, herunder en løsningsarkitektur, som eksempel, og hvordan den relaterer til Azure IoT-Hub, enhed SDK'er og forudkonfigurerede løsninger"
 services="iot-hub"
 documentationCenter=""
 authors="dominicbetts"
 manager="timlt"
 editor=""/>

<tags
 ms.service="iot-hub"
 ms.devlang="na"
 ms.topic="get-started-article"
 ms.tgt_pltfrm="na"
 ms.workload="na"
 ms.date="10/05/2016"
 ms.author="dobett"/>

[AZURE.INCLUDE [iot-azure-and-iot](../../includes/iot-azure-and-iot.md)]

## <a name="next-steps"></a>Næste trin

Azure IoT Hub er en Azure tjeneste, som giver mulighed for sikker og pålidelig tovejs-kommunikation mellem dit program igen slutningen og millioner af enheder. Det gør det muligt for programmet back-end at:

- Modtage telemetri skaleres fra dine enheder.
- Distribuere data fra dine enheder til en stream begivenhed processor.
- Modtage filoverførsler fra enheder.
- Sende cloud-til-enhed kommandoer til bestemte enheder.

Du kan bruge IoT Hub for at implementere din egen løsning back-end. Desuden omfatter IoT Hub en enhed identitet registreringsdatabasen, der bruges til at klargøre enheder, deres legitimationsoplysninger og deres rettigheder til at oprette forbindelse til hubben. Du kan få mere at vide om IoT-Hub, [Hvad er IoT Hub?] [lnk-iot-hub].

Hvis du vil vide, hvordan Azure IoT Hub giver mulighed for baseret på standarder IoT administration af enheder for dig at fra en fjernplacering administrere, konfigurere og opdatere dine enheder, se [Oversigt over Azure IoT Hub enhedsstyring][lnk-device-management].

Hvis du vil implementere klientprogrammer på en lang række enhed hardwareplatforme og operativsystemer, kan du bruge IoT enheden SDK'er. IoT enheden SDK'er omfatter biblioteker, som det er nemmere at sende telemetri til en IoT hub og modtager cloud-til-enhed kommandoer. Når du bruger SDK'er, kan du vælge mellem flere netværksprotokoller til at kommunikere med IoT Hub. Hvis du vil vide mere, se [oplysninger om enhed SDK'er][lnk-device-sdks].

For at komme i gang skrive nogle kode og køre nogle eksempler skal du se [komme i gang med IoT Hub] [ lnk-getstarted] selvstudium.

Du er måske også interesseret i [Azure IoT pakke][lnk-iot-suite], som er en samling af forudkonfigurerede løsninger. IoT pakke gør det muligt at komme hurtigt i gang og skalere IoT projekter for at løse almindelige IoT scenarier – som remote overvågning, administration af aktiver og skønnet vedligeholdelse.

[lnk-getstarted]: iot-hub-csharp-csharp-getstarted.md
[lnk-device-sdks]: https://github.com/Azure/azure-iot-sdks/blob/master/readme.md
[lnk-iot-hub]: iot-hub-what-is-iot-hub.md
[lnk-iot-suite]: https://azure.microsoft.com/documentation/suites/iot-suite/
[lnk-iotdev]: https://azure.microsoft.com/develop/iot/
[lnk-device-management]: iot-hub-device-management-overview.md