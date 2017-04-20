> [AZURE.SELECTOR]
- [Linux](../articles/iot-hub/iot-hub-linux-gateway-sdk-get-started.md)
- [Windows](../articles/iot-hub/iot-hub-windows-gateway-sdk-get-started.md)

Denne artikel indeholder en detaljeret gennemgang af [Hello World eksempelkode] [ lnk-helloworld-sample] til at illustrere de grundlæggende komponenter i [Azure IoT Gateway SDK] [ lnk-gateway-sdk] arkitektur. Eksemplet bruger IoT Hub Gateway SDK til at bygge en enkelt gateway, der registrerer en "hello world"-meddelelse til en fil hvert femte sekund.

Denne gennemgang omfatter:

- **Begreber**: en oversigt over de komponenter, der udgør en gateway, som du opretter med IoT Gateway SDK.  
- **Hej verden eksempel arkitektur**: i denne artikel beskrives, hvordan begreberne, der gælder for prøven Hej verden, og hvordan komponenterne, der passer sammen.
- **Hvordan du opbygger prøven**: processen med at opbygge prøven.
- **Sådan køres prøven**: de trin, der kræves for at køre prøven. 
- **Typisk output**: et eksempel på output kan forvente, når du kører prøven.
- **Kodestykker**: en samling af kodestykker til at vise, hvordan implementerer Hello World prøven nøgler gateway komponenter.

## <a name="azure-iot-gateway-sdk-concepts"></a>Azure IoT Gateway SDK begreber

Før du undersøge eksempelkoden eller oprette dit eget felt gateway ved hjælp af IoT Gateway SDK, skal du forstå de nøglebegreber, der ligger til grund for arkitekturen af SDK.

### <a name="modules"></a>Moduler

Du kan opbygge en gateway med Azure IoT Gateway SDK ved at oprette og montering af *moduler*. Moduler bruger *meddelelser* til udveksling af data med hinanden. Et modul modtager en meddelelse, udfører en handling på den, omdanner eventuelt det til en ny meddelelse og derefter udgiver den til andre moduler til at behandle. Nogle moduler kan producere kun nye meddelelser og aldrig behandle indgående meddelelser. En kæde af moduler oprettes et edb-pipeline til hvert modul, udføre en transformering af data i et punkt i rørledningen.

![En kæde af moduler i gateway, der er bygget med Azure IoT Gateway SDK][1]
 
SDK-PAKKEN indeholder følgende:

- Forhånd skriftlige moduler, som udfører fælles gateway-funktioner.
- Grænsefladerne, som en udvikler kan bruge til at skrive brugerdefinerede moduler.
- Den infrastruktur, der er nødvendige for at installere og køre et sæt af moduler.

SDK indeholder et abstraktionslag, der gør det muligt at opbygge gateways til at køre på en række forskellige operativsystemer og platforme.

![Azure abstraktionslag for IoT Gateway SDK][2]

### <a name="messages"></a>Meddelelser

Men tænke moduler, sende meddelelser til hinanden er en nem måde at nemmere, hvordan en gateway-funktioner, afspejler det præcist ikke hvad der sker. Moduler kan bruge en mægler til at kommunikere med hinanden, udgive meddelelser til broker (bus, pubsub eller messaging mønster) og derefter lade broker, sende meddelelsen til de moduler, der er knyttet til den.

En moduler bruger funktionen **Broker_Publish** til at udgive en meddelelse til broker. Broker, der leverer meddelelser til et modul ved aktivering af en tilbagekaldsfunktion. En meddelelse, der består af et sæt egenskaber for nøgle-eller værdi og indhold, der er givet som en hukommelsesblok.

![Rollen som mægler i Azure IoT Gateway SDK][3]

### <a name="message-routing-and-filtering"></a>Meddelelsesrouting og filtrering

Der er to metoder til at dirigere meddelelser til de korrekte moduler. Hyperlinks kan overføres til broker, så broker ved kilden og vask for hvert modul, eller modulet kan filtrere på egenskaberne for meddelelsen. Et modul skal kun handle ud fra en meddelelse, hvis meddelelsen er beregnet til den. Links og filtrering af meddelelser er hvad effektivt opretter en meddelelse rørledning.

## <a name="hello-world-sample-architecture"></a>Hej verden eksempel arkitektur

Hello World-eksempel viser de begreber, der er beskrevet i forrige afsnit. Hej verden prøven implementerer en gateway, der har en pipeline består af to moduler:

-   *"Hello world"* -modulet opretter hvert femte sekund for en meddelelse og sender dem til modulet logføring.
-   Modulet *logføring* skriver de meddelelser, den modtager, til en fil.

![Arkitektur af Hello World prøve, der er bygget med Azure IoT Gateway SDK][4]

Som beskrevet i forrige afsnit, sender modulet Hej verden ikke meddelelser direkte til modulet logføring hvert femte sekund. I stedet udgiver en meddelelse til broker hvert femte sekund.

Modulet logføring modtager meddelelsen fra broker og reagerer på, skrive indholdet af meddelelsen til en fil.

Modulet logføring forbruger kun meddelelser fra broker, det udgiver aldrig nye meddelelser til broker.

![Hvordan broker, der dirigerer meddelelser mellem moduler i Azure IoT Gateway SDK][5]

Skærmbilledet ovenfor viser arkitekturen i Hello World-prøven og de relative stier til kildefilerne, der implementerer forskellige dele af prøven på [lager][lnk-gateway-sdk]. Gennemse koden på din egen, eller brug kodestykker nedenfor som en vejledning.

<!-- Images -->
[1]: media/iot-hub-gateway-sdk-getstarted-selector/modules.png
[2]: media/iot-hub-gateway-sdk-getstarted-selector/modules_2.png
[3]: media/iot-hub-gateway-sdk-getstarted-selector/messages_1.png
[4]: media/iot-hub-gateway-sdk-getstarted-selector/high_level_architecture.png
[5]: media/iot-hub-gateway-sdk-getstarted-selector/detailed_architecture.png

<!-- Links -->
[lnk-helloworld-sample]: https://github.com/Azure/azure-iot-gateway-sdk/tree/master/samples/hello_world
[lnk-gateway-sdk]: https://github.com/Azure/azure-iot-gateway-sdk