<properties
    pageTitle="Oversigt over Microsoft Azure IoT Suite | Microsoft Azure"
    description="Oversigt over, hvordan Azure IoT pakke leverer internettet over ting forudkonfigurerede løsninger til at indsamle, analysere, og gemme data, giver visualiseringer og integrere med andre systemer."
    services=""
    suite="iot-suite"
    documentationCenter=""
    authors="dominicbetts"
    manager="timlt"
    editor=""/>

<tags
     ms.service="iot-suite"
     ms.devlang="na"
     ms.topic="get-started-article"
     ms.tgt_pltfrm="na"
     ms.workload="na"
     ms.date="08/09/2016"
     ms.author="dobett"/>

# <a name="what-is-azure-iot-suite"></a>Hvad er Azure IoT pakke?

Azure internettet af ting (IoT) services indeholder et bredt udvalg af funktioner. Bruge tjenesterne enterprise gør det muligt at:

- Indsaml data fra enheder
- Analysere data streams i-bevægelse
- Gemme og forespørgsel store datasæt
- Visualisere både realtid og historiske data
- Integrere med back office-systemer

Til at levere disse funktioner, Azure IoT pakke pakker sammen Azure tjenesterne med brugerdefinerede udvidelser som *forudkonfigurerede løsninger*. Disse forudkonfigurerede løsninger er grundlæggende almindelige IoT løsning mønstre, der hjælper med at reducere den tid, du bruger til at fremvise din IoT løsninger-installationer. Brug af [IoT software development kit][lnk-sdks], du kan tilpasse og udvide disse løsninger, så det opfylder dine egne krav. Du kan også bruge disse løsninger som eksempler eller skabeloner, når du udvikler nye IoT løsninger.

Den følgende video giver en introduktion til Azure IoT pakke:

> [AZURE.VIDEO azurecon-2015-introducing-the-microsoft-azure-iot-suite]

## <a name="azure-iot-services-in-azure-iot-suite"></a>Azure IoT tjenester i Azure IoT pakke

De forudkonfigurerede løsninger bruger typisk følgende tjenester:

- Core til Azure IoT pakke er [Azure IoT Hub] [ lnk-iot-hub] tjeneste. Denne tjeneste indeholder enhed i skyen og skyen-til-enhed SMS funktioner og fungerer som gatewayen til skyen og andre vigtige IoT pakke tjenester. Tjenesten gør det muligt at modtage meddelelser fra dine enheder på skala, og send kommandoer til dine enheder.

- [Azure Stream Analytics] [ lnk-asa] indeholder i bevægelse dataanalyse. IoT pakke bruger denne tjeneste for at behandle indgående telemetri, udføre sammenlægning og registrere begivenheder. De forudkonfigurerede løsninger også bruge stream analytics til at behandle informativ meddelelser, der indeholder data som metadata eller kommandoen svar fra enheder. Løsningerne, der bruger Stream Analytics til at behandle meddelelser fra dine enheder og levere disse meddelelser til andre tjenester.

- [Azure-lager] [ lnk-azure-storage] og [Azure DocumentDB] [ lnk-document-db] har funktionerne, data-lager. De forudkonfigurerede løsninger Brug blob-lager til at gemme telemetri og til at gøre det tilgængeligt til analyse. Løsningerne, der bruger DocumentDB til at gemme enhedsmetadata og aktivere enhed projektoversigter i løsningerne.

- [Azure Onlines] [ lnk-web-apps] og [Microsoft Power BI] [ lnk-power-bi] , har funktionerne til visualisering af data. Fleksibilitet af Power BI kan du hurtigt oprette dine egne interaktive dashboards, der bruger IoT pakke data.

En oversigt over arkitekturen i en typisk IoT løsning, under [Microsoft Azure og Internet af ting (IoT)][iot-suite-what-is-azure-iot].

## <a name="preconfigured-solutions"></a>Forudkonfigurerede løsninger

IoT pakke indeholder forudkonfigurerede løsninger, der giver dig mulighed for at komme hurtigt i gang med og for at udforske de almindelige IoT scenarier, som *Remote overvågning* og *skønnet vedligeholdelse*, der gør det muligt Azure IoT pakke. Du kan installere disse løsninger til dit Azure-abonnement og derefter køre et komplet og til slut IoT scenarie.

## <a name="next-steps"></a>Næste trin

Nu hvor du har en oversigt over IoT pakke kan foretage, og hvad er de primære komponenter, du kan få flere oplysninger om de forudkonfigurerede løsninger i IoT pakke, se [Hvad er Azure IoT forudkonfigureret løsninger?][lnk-what-are-preconfig]

[lnk-sdks]: https://azure.microsoft.com/documentation/articles/iot-hub-sdks-summary/
[lnk-iot-hub]: https://azure.microsoft.com/documentation/services/iot-hub/
[lnk-asa]: https://azure.microsoft.com/documentation/services/stream-analytics/
[lnk-azure-storage]: https://azure.microsoft.com/documentation/services/storage/
[lnk-document-db]: https://azure.microsoft.com/documentation/services/documentdb/
[lnk-power-bi]: https://powerbi.microsoft.com/
[lnk-web-apps]: https://azure.microsoft.com/documentation/services/app-service/web/
[iot-suite-what-is-azure-iot]: iot-suite-what-is-azure-iot.md
[lnk-what-are-preconfig]: iot-suite-what-are-preconfigured-solutions.md
