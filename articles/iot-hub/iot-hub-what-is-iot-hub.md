<properties
 pageTitle="Oversigt over Azure IoT Hub | Microsoft Azure"
 description="Oversigt over Azure IoT Hub service: Hvad er iot-hub, enhed forbindelse, internet ting kommunikationsmønstre og service-assisteret kommunikation mønster"
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
 ms.date="08/25/2016"
 ms.author="dobett"/>

# <a name="what-is-azure-iot-hub"></a>Hvad er Azure IoT Hub?

Velkommen til Azure IoT Hub. I denne artikel indeholder en oversigt over Azure IoT Hub og beskriver, hvorfor du skal bruge denne tjeneste til at implementere en Internet af ting (IoT)-løsning. Azure IoT Hub er en komplet administreret tjeneste, der gør det muligt for pålidelig og sikker tovejs kommunikationen mellem millioner af IoT enheder og løsning back-end. Azure IoT Hub:

- Indeholder pålidelig enhed i skyen og skyen-til-enhed-messaging skaleres til brug.
- Aktiverer sikker kommunikation ved hjælp af hver enhed sikkerhedslegitimationsoplysninger og adgangskontrol.
- Indeholder omfattende overvågning for enhed forbindelsen og enhed identitet management begivenheder.
- Omfatter enhed billedbiblioteker til de mest populære sprog og platforme.

Denne artikel for [sammenligning af IoT Hub og begivenhed Hubs] [ lnk-compare] beskrives de tre vigtigste forskelle mellem disse to tjenester og fremhæver fordelene ved at bruge IoT Hub i dine IoT løsninger.

![Azure IoT Hub som skyen gateway i internet af ting løsning][img-architecture]

> [AZURE.NOTE] Finde [Microsoft Azure IoT referencearkitektur]i en detaljeret beskrivelse af IoT arkitektur[lnk-refarch].

## <a name="iot-device-connectivity-challenges"></a>IoT enhed connectivity udfordringer

IoT Hub og enhed biblioteker hjælpe dig til en udfordring af, hvordan du pålideligt og sikkert tilslutte enheder til løsning back-end. IoT enheder:

- Er ofte integrerede systemer uden human operator.
- Kan være i eksterne placeringer, hvor fysisk adgang koster.
- Må kun være tilgængelige via løsning back-end.
- Måske har en begrænset power og behandling af ressourcer.
- Muligvis forbigående, langsom eller dyr netværksforbindelse.
- Muligvis bruge beskyttede, brugerdefineret eller branchespecifikke programmet protokoller.
- Du kan oprette ved hjælp af en stor mængde populære hardware og software platforme.

Ud over de krav til ovenfor, skal alle IoT løsninger også leverer skala, sikkerhed og pålidelighed. Det resulterende sæt af krav til netværksforbindelser er svær og tidskrævende at implementere, når du bruger traditionelle teknologier, som web beholdere og SMS mæglere.

## <a name="why-use-azure-iot-hub"></a>Hvorfor bruge Azure IoT Hub

Azure IoT Hub adresser enhed connectivity udfordringer på følgende måder:

-   **Hver enhed godkendelse og sikker forbindelse**. Du kan klargøre hver enhed med sin egen [security key til] [ lnk-devguide-security] til gør det muligt at oprette forbindelse til IoT Hub. [IoT Hub identitet registreringsdatabasen] [ lnk-devguide-identityregistry] gemmer enhed identiteter og nøgler i en løsning. Løsning back-end kan tilføje individuelle enheder for at tillade eller nægte listerne for at aktivere fuld kontrol over enhed adgang.

-   **Overvågning af enhed connectivity handlinger**. Du kan få detaljerede handlingen logge om enhed identitet management handlinger og enhed connectivity begivenheder. Denne overvågningsfunktioner gør det muligt for din IoT løsning til at identificere problemer med serverforbindelsen, såsom enheder, der forsøger at oprette forbindelse med forkerte legitimationsoplysninger, sende meddelelser for ofte, eller afvise alle meddelelser i skyen-til-enhed.

-   **Et omfattende sæt af enhed biblioteker**. [Azure IoT enhed SDK'er] [ lnk-device-sdks] er tilgængelige og understøttede for forskellige sprog og platforme – C er for mange Linux fordelingerne, Windows og realtid operativsystemer. Azure IoT enhed SDK'er understøtter også administrerede sprog, som C#, Java og JavaScript.

-   **IoT protokoller og udvidelse**. Hvis din løsning ikke kan bruge enhed biblioteker, Fremviser IoT Hub en offentlige protokol, der gør det muligt for enheder for at bruge indbygget MQTT v3.1.1, HTTP 1.1 eller AMQP 1.0 protokoller. Du kan også udvide IoT Hub for at yde support til brugerdefineret protokoller ved at:

    - Oprette et felt gateway med [Azure IoT Gateway SDK] [ lnk-gateway-sdk] , der konverterer din brugerdefinerede protokol til en af de tre protokoller forstået ved IoT Hub. 
    - Tilpasse [Azure IoT protocol gateway][protocol-gateway], en åben kildekomponent, der kører i skyen.

-   **Skala**. Azure IoT Hub skaleres til millioner af samtidigt tilsluttede enheder og millioner af hændelser sekundet.

Disse fordele er generiske for mange kommunikationsmønstre. IoT Hub aktuelt giver dig mulighed at implementere følgende bestemte kommunikationsmønstre:

-   **Indtagelse begivenhed-baserede enhed i skyen.** IoT Hub kan pålideligt modtage millioner af begivenheder sekundet fra dine enheder. Det kan derefter behandle dem på din varmt vej ved hjælp af en begivenhed processor program. Det kan også gemme dem på din kolde sti til analyse. IoT Hub bevarer begivenhed dataene i op til syv dage at garantere stabil behandling og opfanger spidser i afkrydsningsfeltet Indlæs.

-   * *Pålidelig messaging cloud-til-enhed (eller *kommandoer*). ** løsning back-end kan bruge IoT Hub til at sende meddelelser med en med mindste-det samme levering GARANTIBRUD til individuelle enheder. Hver meddelelse med en enkelt time to live-indstilling, og back-end kan anmode om kvitteringer for både levering og udløb. Disse kvitteringer sikre fuld indsigt i livscyklussen for en meddelelse i skyen-til-enhed. Du kan derefter implementere forretningslogik, der indeholder handlinger, der kører på enheder.

-   **Overføre filer og cachelagrede føler data til skyen.** Dine enheder kan overføre filer til Azure-lager ved hjælp af SAS URI'er administreres for dig af IoT Hub. IoT Hub kan generere beskeder, når du modtager filer i skyen for at aktivere back-end at behandle dem.

## <a name="gateways"></a>Gateways

En gateway i en IoT løsning er typisk enten en [protocol gateway] [ lnk-gateway] , der er implementeret i skyen eller et [felt gateway] [ lnk-field-gateway] , der installeres lokalt med dine enheder. En protokol gateway udfører protocol oversættelse, for eksempel MQTT til AMQP. En felt gateway kan køre analytics på kanten, skal træffe beslutninger tid med at reducere ventetid, giver enhed management services, gennemtvinge sikkerhed og beskyttelse af personlige oplysninger begrænsninger og også udføre protocol oversættelse. Begge typer gateway fungerer som mellemled mellem dine enheder og IoT centrum.

En gateway felt er forskellig fra en simpel trafik routing enhed (som et netværk adresse oversættelse enheds- eller firewall), fordi den typisk udfører en aktiv rolle i administration af strømmen af access og oplysninger i din løsning.

En løsning kan omfatte både protokollen og felt gateways.

## <a name="how-does-iot-hub-work"></a>Hvordan fungerer IoT Hub?

Azure IoT Hub implementerer [service-assisteret kommunikation] [ lnk-service-assisted-pattern] mønster til at formidle interaktioner mellem dine enheder og din løsning back-end. Formålet med tjenesten assisteret kommunikation er at oprette troværdig, tovejs Kommunikationsstier mellem et kontrolelement system, som IoT Hub og særlige formål enheder, der er implementeret i har tillid til fysisk plads. Mønstret fastsætter følgende principper:

- Sikkerhed tilsidesætter alle andre funktioner.
- Enheder accepterer ikke uønsket netværksoplysninger. En enhed opretter alle forbindelser og omdirigerer i en udelukkende er udgående måde. Til en enhed til at modtage en kommando fra back-end igangsætte enheden regelmæssigt en forbindelse for at kontrollere, om ventende kommandoer til at behandle.
- Enheder skal kun oprette forbindelse til eller oprette omdirigerer til kendte tjenester, de er peered med, såsom IoT Hub.
- Kommunikationsstien mellem enheder og tjenester eller mellem enhed og gatewayen er sikret på protocol programlag.
- System-niveau godkendelse og godkendelse er baseret på hver enhed identiteter. De gør access legitimationsoplysninger og tilladelser næsten med det samme simpel.
- Tovejs kommunikation til enheder, der har forbindelse sporadisk på grund af power eller connectivity problemstillinger foretages ved at holde kommandoer og enhedsbeskeder, indtil en enhed, der opretter forbindelse for at hente dem. IoT Hub fører enhed-specifikke køer til de kommandoer, sender programmet.
- Programmet dataene er sikret separat for beskyttet forsendelse gennem gateways til en bestemt tjeneste.

Mobile branche har brugt tjenesten assisteret kommunikation mønster skaleres til optager enormt meget plads til at implementere opslagsnål meddelelse tjenester som [Windows opslagsnål meddelelse Services][lnk-wns], [Google Cloud Messaging][lnk-google-messaging], og [Apple-Pushmeddelelsestjeneste][lnk-apple-push].

IoT Hub understøttes over Expressroutes offentlige peering kurve.

## <a name="next-steps"></a>Næste trin

Hvis du vil vide, hvordan Azure IoT Hub giver mulighed for baseret på standarder IoT administration af enheder for dig at fra en fjernplacering administrere, konfigurere og opdatere dine enheder, se [Oversigt over Azure IoT Hub enhedsstyring][lnk-device-management].

Hvis du vil implementere klientprogrammer på en lang række enhed hardwareplatforme og operativsystemer, kan du bruge IoT enheden SDK'er. IoT enheden SDK'er omfatter biblioteker, som det er nemmere at sende telemetri til en IoT hub og modtager cloud-til-enhed kommandoer. Når du bruger SDK'er, kan du vælge mellem forskellige netværksprotokoller til at kommunikere med IoT Hub. Hvis du vil vide mere, se [oplysninger om enhed SDK'er][lnk-device-sdks].

For at komme i gang skrive nogle kode og køre nogle eksempler skal du se [komme i gang med IoT Hub] [ lnk-get-started] selvstudium.

[img-architecture]: media/iot-hub-what-is-iot-hub/hubarchitecture.png


[lnk-get-started]: iot-hub-csharp-csharp-getstarted.md
[protocol-gateway]: https://github.com/Azure/azure-iot-protocol-gateway/blob/master/README.md
[lnk-service-assisted-pattern]: http://blogs.msdn.com/b/clemensv/archive/2014/02/10/service-assisted-communication-for-connected-devices.aspx "Tjenesten assisteret kommunikation, blogindlæg af Clemens Vasters"
[lnk-compare]: iot-hub-compare-event-hubs.md
[lnk-gateway]: iot-hub-protocol-gateway.md
[lnk-field-gateway]: iot-hub-devguide-endpoints.md#field-gateways
[lnk-devguide-identityregistry]: iot-hub-devguide-identity-registry.md
[lnk-devguide-security]: iot-hub-devguide-security.md
[lnk-wns]: https://msdn.microsoft.com/library/windows/apps/mt187203.aspx
[lnk-google-messaging]: https://developers.google.com/cloud-messaging/
[lnk-apple-push]: https://developer.apple.com/library/ios/documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/Chapters/ApplePushService.html#//apple_ref/doc/uid/TP40008194-CH100-SW9
[lnk-device-sdks]: https://github.com/Azure/azure-iot-sdks
[lnk-refarch]: http://download.microsoft.com/download/A/4/D/A4DAD253-BC21-41D3-B9D9-87D2AE6F0719/Microsoft_Azure_IoT_Reference_Architecture.pdf
[lnk-gateway-sdk]: https://github.com/Azure/azure-iot-gateway-sdk
[lnk-device-management]: iot-hub-device-management-overview.md
