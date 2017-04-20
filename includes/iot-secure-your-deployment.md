# <a name="securing-your-iot-deployment"></a>Sikring af din installation af IoT

Denne artikel indeholder det næste detaljeniveau til sikring af infrastrukturen Azure IoT-baserede Internet af ting (IoT). Den har hyperlinks til niveau installationsdetaljer til konfiguration og implementering af hver komponent. Det indeholder også sammenligninger og valg mellem forskellige konkurrerende metoder.

Sikring af Azure IoT installationen kan inddeles i følgende tre sikkerhedsområder:

- **Sikkerhed**: sikring af IoT enheden, mens den er implementeret i naturen.
- **Forbindelsessikkerhed**: at sikre, at alle data, der overføres mellem IoT enhed og IoT Hub er fortrolige og sikret mod manipulation.
- **Cloud-sikkerhed**: at give et middel til at sikre data, mens den bevæger sig gennem, og er gemt i skyen.

![Tre sikkerhedsområder][img-overview]

## <a name="secure-device-provisioning-and-authentication"></a>Klargøring af enheden og godkendelse

Azure IoT Suite sikrer IoT enheder ved hjælp af følgende to metoder:

- Ved at angive en entydig identitet nøgle (sikkerheds-id) for hver enhed, der kan bruges af enheden for at kommunikere med IoT-Hub.

- Ved hjælp af en i-enhed [x.509-certifikat] [ lnk-x509] og den private nøgle som et middel til at godkende enheden til IoT-Hub. Denne godkendelsesmetode, der sikrer, at den private nøgle på enheden ikke er kendt uden for enheden til enhver tid, hvilket giver et højere sikkerhedsniveau.

Token sikkerhedsmetode giver godkendelse for hvert opkald, der foretages af enheden IoT Hub ved at knytte den symmetriske nøgle til nyt opkald. X.509-baseret godkendelse giver mulighed for godkendelse af en IoT-enhed på det fysiske lag som en del af virksomheden TLS-forbindelse. Metoden security-token-baserede kan bruges uden x.509-godkendelsen, der er et mønster, der er mindre sikker. Valget mellem de to metoder er først og fremmest afgøres af hvor sikker enhed godkendelse skal være, og tilgængeligheden af sikker lagring på enheden (for at gemme den private nøgle sikkert).

## <a name="iot-hub-security-tokens"></a>IoT Hub sikkerhedstokens

IoT Hub bruger sikkerhedstokens til at godkende enheder og tjenester for at undgå at sende nøgler på netværket. Desuden begrænses sikkerhedstokens i gyldighed og omfang. Automatisk oprette Azure IoT Hub SDK'er tokens uden at kræve en særlig konfiguration. Nogle situationer kræver dog bruger til at oprette og bruge sikkerhedstokens direkte. Disse omfatter direkte brug af MQTT, AMQP eller HTTP-overflader, eller ved gennemførelse af mønsteret sikkerhedstokentjeneste.

Yderligere oplysninger om strukturen af sikkerheds-id og dens anvendelse kan findes i følgende artikler:

-   [Strukturen af en sporingstoken sikkerhed][lnk-security-tokens]
-   [Med SAS-tokens som en enhed][lnk-sas-tokens]

Hver IoT Hub har en [Enhed identitet registreringsdatabasen] [ lnk-identity-registry] , der kan bruges til at oprette ressourcer pr. enhed i tjenesten som en kø, der indeholder de meddelelser, under flyvning sky-til-enhed, og at give adgang til enheden mod slutpunkterne. IoT Hub identitet registreringsdatabasen giver sikker lagring af enheden identiteter og sikkerhedsnøgler for en løsning. Enkeltpersoner eller grupper af enheden identiteter kan føjes til en liste over tilladte og blokerede websteder, aktivering fuldstændig kontrol over adgangen til enheden. Følgende artikler giver flere detaljer om strukturen i registreringsdatabasen enhed identitet og understøttede handlinger.

[IoT Hub understøtter protokoller som MQTT, AMQP og HTTP-][lnk-protocols]. Hver af disse protokoller bruge sikkerhedstokens fra enheden IoT IoT hub anderledes:

- AMQP: SASL almindelig og AMQP kravsbaseret sikkerhed ({policyName}@sas.root.{iothubName} for tokens Hub-niveau; {deviceId} i forbindelse med virkefelt for enheden tokens).

- MQTT: TILSLUT pakke bruger {deviceId} som {ClientId} {IoThubhostname} / {deviceId} i feltet **brugernavn** og et SAS-id i feltet **adgangskode** .

- HTTP: Ugyldigt token er i anmodningen godkendelsesheader.

IoT Hub enhed identitet registreringsdatabasen kan bruges til at konfigurere legitimationsoplysninger pr. enhed og adgangskontrol. Men hvis en IoT løsning allerede har en betydelig investering i en [Brugerdefineret enhed identitet registreringsdatabasen og/eller godkendelse skema][lnk-custom-auth], den kan integreres i en eksisterende infrastruktur med IoT Hub ved at oprette en sikkerhedstokentjeneste.

### <a name="x509-certificate-based-device-authentication"></a>X.509-certifikat-baseret enhed godkendelse

Brug af en [enhed baseret x.509-certifikat] [ lnk-use-x509] og den tilknyttede private og offentlige nøglepar giver mulighed for yderligere godkendelse ved det fysiske lag. Den private nøgle gemmes sikkert i enheden og kan ikke findes uden for enheden. X.509-certifikatet indeholder oplysninger om enheden, f.eks enheds-ID, og andre organisatoriske oplysninger. En signatur i certifikatet er genereret ved hjælp af den private nøgle.

Overordnet enhed klargøring flow:

- Knytte et id til en fysisk enhed – anordningens identitet og/eller x.509-certifikat, der er knyttet til enheden under enhed produktion eller ibrugtagning.

- Oprette en tilsvarende post i identitet i IoT Hub – anordningens identitet og tilknyttede enhedsoplysninger i registreringsdatabasen IoT Hub-enhed.

- Sikkert gemme x.509-certifikataftryk i registreringsdatabasen af IoT Hub-enhed.

### <a name="root-certificate-on-device"></a>Rodcertifikat på enheden

Under oprettelse af en sikker TLS-forbindelse med IoT Hub, godkender enheden IoT IoT-Hub med et rodcertifikat, som er en del af enheden SDK. For C-klient-SDK, certifikatet er placeret i mappen "\\c\\certifikater" under roden af repo. Selvom disse rodcertifikater er langlivet, de stadig kan udløbe eller tilbagekaldes. Hvis der er ingen mulighed for at opdatere certifikatet på enheden, kan enheden muligvis ikke senere tilsluttes IoT-Hub (eller en anden cloud-tjeneste). Har mulighed for at opdatere rodcertifikatet, når IoT enheden installeres vil effektivt reducere denne risiko.

## <a name="securing-the-connection"></a>Sikring af forbindelsen

Internet-forbindelse mellem IoT enhed og IoT Hub er sikret ved hjælp af Transport Layer Security (TLS)-standarden. Azure IoT understøtter [TLS 1.2][lnk-tls12], TLS 1.1 og TLS 1.0, i denne rækkefølge. Støtte til TLS 1.0 af hensyn til bagudkompatibiliteten kun. Det anbefales at bruge TLS 1.2, da det giver de fleste sikkerhed.

Azure IoT Suite understøtter følgende krypteringsprogrammerne, i denne rækkefølge.

| Krypteringsprogrammet | Længde |
|--------------|--------|
| TLS\_ECDHE\_RSA\_med\_AES\_256\_CBC\_SHA384 (0xc028) ECDH secp384r1 (eq. 7680-bit RSA) FS | 256    |
| TLS\_ECDHE\_RSA\_med\_AES\_128\_CBC\_SHA256 (0xc027) ECDH secp256r1 (eq. 3072-bit RSA) FS | 128    |
| TLS\_ECDHE\_RSA\_med\_AES\_256\_CBC\_SHA (0xc014) ECDH secp384r1 (eq. 7680-bit RSA) FS           | 256    |
| TLS\_ECDHE\_RSA\_med\_AES\_128\_CBC\_SHA (0xc013) ECDH secp256r1 (eq. 3072-bit RSA) FS           | 128    |
| TLS\_RSA\_med\_AES\_256\_GCM\_SHA384 (0x9d) | 256    |
| TLS\_RSA\_med\_AES\_128\_GCM\_SHA256 (0x9c) | 128    |
| TLS\_RSA\_med\_AES\_256\_CBC\_SHA256 (0 x 3D) | 256    |
| TLS\_RSA\_med\_AES\_128\_CBC\_SHA256 (0x3c) | 128    |
| TLS\_RSA\_med\_AES\_256\_CBC\_SHA (0x35)    | 256    |
| TLS\_RSA\_med\_AES\_128\_CBC\_SHA (0x2f)    | 128    |
| TLS\_RSA\_med\_3DES\_Ede, Nederlandene\_CBC\_SHA (0xa)    | 112    |

## <a name="securing-the-cloud"></a>Sikring af skyen

Azure IoT Hub tillader definition af [politik for adgangskontrol] [ lnk-protocols] for de enkelte sikkerhedsnøgler. Det bruger følgende sæt tilladelser til at give adgang til hver af IoT Hub slutpunkter. Tilladelser begrænse adgangen til en IoT-Hub, der er baseret på funktionalitet.

- **RegistryRead**. Giver skrivebeskyttet adgang til registreringsdatabasen identitet for enheden. Yderligere oplysninger finder du under [Enhed identitet registreringsdatabasen][lnk-identity-registry].

- **RegistryReadWrite**. Giver læse- og skriveadgang til enheden identitet registreringsdatabasen. Yderligere oplysninger finder du under [Enhed identitet registreringsdatabasen][lnk-identity-registry].

- **ServiceConnect**. Giver adgang til sky service mod kommunikation og overvågning af slutpunkter. For eksempel giver den tilladelse til at sky-back-end-tjenester til modtager enheden til sky meddelelser, sende meddelelser til sky-til-enhed og hente de tilsvarende levering bekræftelser.

- **DeviceConnect**. Giver adgang til enheden mod kommunikation slutpunkter. For eksempel, giver den tilladelse til at sende meddelelser på enheden til sky og modtage meddelelser på sky-til-enhed. Denne tilladelse bruges af enheder.

Der er to måder at få tilladelse af **DeviceConnect** med IoT Hub med [sikkerhedstokens][lnk-sas-tokens]: ved hjælp af en nøgle til identiteten af enheden, eller en politiknøgle for delt adgang. Desuden er det vigtigt at bemærke, at alle funktioner fra enheder er udsat ved design på slutpunkter med præfikset `/devices/{deviceId}`.

[Service-komponenter kan kun generere sikkerhedstokens] [ lnk-service-tokens] ved hjælp af fælles politikker giver de nødvendige tilladelser.

Tillad styring af brugere, der anvender Active Directory Azure, Azure IoT Hub og andre tjenester, som kan være en del af løsningen.

Data, der indtages af Azure IoT Hub kan forbruges af en række tjenester som Azure Stream Analytics og Azure blob storage. Disse tjenester giver adgang til administration. Læs mere om disse tjenester og tilgængelige indstillinger nedenfor:

- [Azure DocumentDB][lnk-docdb]: en skalerbar, fuldt indekseret databasetjenesten for delvist strukturerede data, der styrer metadata for enhederne du klargøre, attributter, konfiguration og sikkerhedsegenskaber. DocumentDB giver høj ydeevne og høj overførselshastighed forarbejdning, skema agnostic indeksering af data samt en avanceret SQL query grænseflade.

- [Azure Stream Analytics][lnk-asa]: realtid stream behandling i skyen, der gør det muligt hurtigt at udvikle og implementere en analytics prisbillige løsning for at afdække realtid indblik fra enheder, sensorer, infrastruktur og programmer. Data fra denne fuldt administreret tjeneste kan skaleres til en hvilken som helst enhed under stadig at opnå højere overførselshastighed, kort ventetid og fleksibilitet.

- [Azure App Services][lnk-appservices]: en cloud-platform til at opbygge effektive web- og mobile apps, der opretter forbindelse til data overalt; i sky eller på stedet. Byg spændende mobile apps til iOS, Android og Windows. Integrere med din Software som en Service (SaaS) og virksomhedsprogrammer med out of box-tilslutning til snesevis af cloud-baserede tjenester og virksomhedsprogrammer. Kode på dit foretrukne sprog og IDE (.NET, NodeJS, PHP, Python eller Java) til at oprette web-apps og API'er hurtigere end nogensinde før.

- [Logik Apps][lnk-logicapps]: den logik Apps funktion i tjenesten af Azure-App hjælper med at integrere din IoT løsning til din eksisterende line of business-systemer og automatisere arbejdsprocesser. Logik Apps gør det muligt for udviklere at designe arbejdsgange, der starter fra en udløser, og udfør derefter en række trin, regler og handlinger, der bruger stærke forbindelser til at integrere med eksisterende forretningsprocesser. Logik Apps tilbyder out of box-tilslutning til et meget stort økosystem af SaaS, skybaserede og lokale programmer.

- [Azure blob storage][lnk-blob]: sky pålidelig og økonomisk lager for de data, der sender dine enheder til skyen.

## <a name="conclusion"></a>Konklusion

Denne artikel indeholder oversigt over gennemførelsen niveau detaljer for design og implementering af en IoT-infrastruktur ved hjælp af Azure IoT. Konfiguration af hver komponent for at være sikker, er nøglen i sikring af den overordnede IoT infrastruktur. Design valgmuligheder i Azure IoT giver en vis grad af fleksibilitet og valgfrihed; hver valgmulighed kan dog have betydning for sikkerheden. Det anbefales, at hver af disse valgmuligheder evalueres gennem en vurdering af risikoen/omkostning.

[img-overview]: media/iot-secure-your-deployment/overview.png

[lnk-security-tokens]: ../articles/iot-hub/iot-hub-devguide-security.md#security-token-structure
[lnk-sas-tokens]: ../articles/iot-hub/iot-hub-devguide-security.md#use-sas-tokens-as-a-device
[lnk-identity-registry]: ../articles/iot-hub/iot-hub-devguide-identity-registry.md
[lnk-protocols]: ../articles/iot-hub/iot-hub-devguide-security.md
[lnk-custom-auth]: ../articles/iot-hub/iot-hub-devguide-security.md#custom-device-authentication
[lnk-x509]: http://www.itu.int/rec/T-REC-X.509-201210-I/en
[lnk-use-x509]: ../articles/iot-hub/iot-hub-devguide-security.md
[lnk-tls12]: https://tools.ietf.org/html/rfc5246
[lnk-service-tokens]: ../articles/iot-hub/iot-hub-devguide-security.md#using-security-tokens-from-service-components
[lnk-docdb]: https://azure.microsoft.com/services/documentdb/
[lnk-asa]: https://azure.microsoft.com/services/stream-analytics/
[lnk-appservices]: https://azure.microsoft.com/services/app-service/
[lnk-logicapps]: https://azure.microsoft.com/services/app-service/logic/
[lnk-blob]: https://azure.microsoft.com/services/storage/
