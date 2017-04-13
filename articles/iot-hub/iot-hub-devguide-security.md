<properties
 pageTitle="Udvikler vejledning - styre adgangen til IoT Hub | Microsoft Azure"
 description="Azure IoT Hub udvikler vejledning - hvordan du kan styre adgangen til IoT Hub og administrere sikkerhed"
 services="iot-hub"
 documentationCenter=".net"
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

# <a name="control-access-to-iot-hub"></a>Styre adgangen til IoT Hub

## <a name="overview"></a>Oversigt

I denne artikel beskrives indstillingerne for at sikre IoT centrum. IoT Hub bruger *tilladelser* til at give adgang til hver IoT hub slutpunkter. Tilladelser begrænse adgangen til en IoT-hub, der er baseret på funktionalitet.

I denne artikel beskrives:

- De forskellige tilladelser, du kan give en enhed eller back end-App til at få adgang til din IoT hub.
- Godkendelsesprocessen og tokens bruges til at bekræfte tilladelser.
- Sådan omfang legitimationsoplysninger for at begrænse adgangen til bestemte ressourcer.
- IoT Hub understøttelse af x.509-certifikater.
- Brugerdefineret enhed godkendelsesmetoder, der bruger eksisterende enhed identitet registre eller godkendelsesmetode.

### <a name="when-to-use"></a>Hvornår skal jeg bruge

Du skal have relevante tilladelser til at få adgang til alle IoT Hub slutpunkterne. For eksempel skal en enhed indeholde et token, der indeholder sikkerhedslegitimationsoplysninger sammen med alle meddelelser, der sendes til IoT Hub.

## <a name="access-control-and-permissions"></a>Adgangskontrol og tilladelser

Du kan tildele [tilladelser](#iot-hub-permissions) på følgende måder:

* **Hub niveau delt access politikker**. Delt adgang politikker kan give en vilkårlig kombination af [tilladelser](#iot-hub-permissions). Du kan definere politikker [Azure portal][lnk-management-portal], eller fra et program ved hjælp af [IoT Hub ressource udbyder REST API'er][lnk-resource-provider-apis]. En nyoprettet IoT hub har følgende standardpolitikker:

    - **iothubowner**: politik med alle tilladelser.
    - **tjeneste**: politik med ServiceConnect tilladelse.
    - **enhed**: politik med DeviceConnect tilladelse.
    - **registryRead**: politik med RegistryRead tilladelse.
    - **registryReadWrite**: politik med RegistryRead og RegistryWrite tilladelser.


* **Hver enhed sikkerhedslegitimationsoplysninger**. Hver IoT Hub indeholder en [enhed identitet registreringsdatabasen][lnk-identity-registry]. Du kan konfigurere sikkerhedslegitimationsoplysninger, som giver **DeviceConnect** tilladelser, der er fastsat til de tilsvarende enhed slutpunkter for hver enhed i denne registreringsdatabasenøgle.

For eksempel i en typisk IoT løsning:

- Komponenten enhed management bruger politikken *registryReadWrite* .
- Komponenten begivenhed processor bruger politikken *tjeneste* .
- Komponenten runtime enhed business logik bruger politikken *tjeneste* .
- Individuelle enheder oprette forbindelse ved hjælp af legitimationsoplysninger, der er gemt i IoT-hub identitet registreringsdatabasen.

## <a name="authentication"></a>Godkendelse

Azure IoT Hub giver adgang til slutpunkter ved at kontrollere et token mod delt adgang politikker og enhed identitet registreringsdatabasen sikkerhedslegitimationsoplysninger.

Sikkerhedslegitimationsoplysninger som symmetriske nøgler sendes aldrig over en netværksforbindelse.

> [AZURE.NOTE] Provideren Azure IoT Hub ressource er sikret gennem abonnementet Azure, som er alle udbydere i [Azure ressourcestyring][lnk-azure-resource-manager].

Du kan finde flere oplysninger om at oprette og bruge sikkerhedstokens, se [IoT Hub sikkerhedstokens][lnk-sas-tokens].

### <a name="protocol-specifics"></a>Protocol specifikke oplysninger

Hver understøttet protokol, som MQTT, AMQP og HTTP, transporter tokens på forskellige måder.

Når du bruger MQTT, Opret forbindelse pakken har deviceId som ClientId, {iothubhostname} / {deviceId} i feltet brugernavn og en SAS token i feltet adgangskode. {iothubhostname} skal være det fulde CName af IoT hub (for eksempel contoso.azure-devices.net).

Når du bruger [AMQP][lnk-amqp], IoT Hub understøtter [SASL almindelig] [ lnk-sasl-plain] og [AMQP krav-baseret-sikkerhed][lnk-cbs].

Hvis du bruger AMQP krav-baseret-sikkerhed, angiver standarden, hvordan til at overføre disse tokens.

For SASL almindelig, kan det være **brugernavn** :

* `{policyName}@sas.root.{iothubName}`Hvis bruger hub niveau tokens.
* `{deviceId}@sas.{iothubname}`Hvis bruger enhed-fastsat tokens.

I begge tilfælde, indeholder adgangskodefeltet tokenet, som beskrevet i [IoT Hub sikkerhedstokens][lnk-sas-tokens].

HTTP implementerer godkendelse ved at medtage et gyldigt token i overskriften **godkendelse** anmodning.

#### <a name="example"></a>Eksempel

Brugernavn (DeviceId er store og små bogstaver):`iothubname.azure-devices.net/DeviceId`

Adgangskode (Opret SAS med enhed Stifinder):`SharedAccessSignature sr=iothubname.azure-devices.net%2fdevices%2fDeviceId&sig=kPszxZZZZZZZZZZZZZZZZZAhLT%2bV7o%3d&se=1487709501`

> [AZURE.NOTE] [Azure IoT Hub SDK'er] [ lnk-sdks] automatisk generere tokens, når du opretter forbindelse til tjenesten. I nogle tilfælde understøtter SDK'er ikke alle protokollerne eller alle godkendelsesmetoderne.

### <a name="special-considerations-for-sasl-plain"></a>Særlige overvejelser i forbindelse med SASL almindelig

Når du bruger SASL almindelig med AMQP, kan en klient, oprette forbindelse til en IoT hub kan bruge et enkelt token for hver TCP-forbindelse. Når tokenet udløber, TCP-forbindelsen afbryder forbindelsen til tjenesten og udløser forbindelsen. Denne funktionalitet, er mens ikke problematisk for en programmet back end-komponent, meget skadevoldende til et program enhed side af følgende årsager:

*  Gateways Forbind normalt på vegne af mange enheder. Når du bruger SASL almindelig, har de til at oprette en forskellige TCP-forbindelse til hver enhed, oprette forbindelse til en IoT hub. Dette scenarie betydeligt øges forbrug af power og netværk ressourcer og øger forsinkelse på hver enhedsforbindelse.
* Begrænsede ressourcer enheder påvirkes negativt ved øget brug af ressourcer til at oprette forbindelse igen efter hver token udløb.

## <a name="scope-hub-level-credentials"></a>Omfang hub niveau legitimationsoplysninger

Du kan begrænse sikkerhed på brugerniveau hub politikker ved at oprette tokens med en begrænset ressource-URI. For eksempel er slutpunkt til at sende enhed i skyen meddelelser fra en enhed **/devices/ {deviceId} / meddelelser/begivenheder**. Du kan også bruge en politik for hub niveau delt adgang med **DeviceConnect** tilladelser til at signere et token, hvis resourceURI er **/devices/ {deviceId}**. Denne metode opretter et token, som kan kun bruges til at sende meddelelser på vegne af enhed **deviceId**.

Denne funktion ligner [begivenhed Hubs publisher politik][lnk-event-hubs-publisher-policy], og gør det muligt at implementere brugerdefinerede godkendelsesmetoder.

## <a name="security-tokens"></a>Sikkerheds-id

IoT Hub bruger sikkerhedstokens til at godkende enheder og tjenester til at undgå at sende taster på nettet. Desuden er sikkerhedstokens begrænset i gyldighed og omfang. [Azure IoT Hub SDK'er] [ lnk-sdks] automatisk generere tokens uden en særlig konfiguration. Nogle scenarier, dog kræver, at brugeren til at oprette og bruge sikkerhedstokens direkte. Dette omfatter på direkte brug af MQTT, AMQP eller HTTP overflader eller implementeringen af mønster token tjeneste, som beskrevet i [Brugerdefineret enhed godkendelse][lnk-custom-auth].

IoT Hub kan også godkende med IoT Hub-enheder ved hjælp af [x.509-certifikater][lnk-x509]. 

### <a name="security-token-structure"></a>Sikkerhed token struktur
Du kan bruge sikkerhedstokens til at give tid afgrænset adgang til enheder og tjenester til bestemte funktioner i IoT Hub. For at sikre, at kun autoriserede enheder og tjenester kan forbindelse, skal være logget sikkerhedstokens med en delt adgang politiknøgle eller en symmetriske nøgle, gemmes sammen med en enhed identitet i registreringsdatabasen identitet.

Et token signeret med en delt adgang politik vigtige giver adgang til alle de funktioner, der er knyttet til de delte adgangstilladelser til politik. På den anden side giver et token, der er signeret med en enhed identitet symmetriske nøgle kun **DeviceConnect** tilladelse til den tilknyttede enhed identitet.

Det pågældende sikkerhedstoken har følgende format:

    SharedAccessSignature sig={signature-string}&se={expiry}&skn={policyName}&sr={URL-encoded-resourceURI}

Dette er de forventede værdier:

| Værdi | Beskrivelse |
| ----- | ----------- |
| {signatur} | En HMAC SHA256 signatur streng i formatet: `{URL-encoded-resourceURI} + "\n" + expiry`. **Vigtigt**: nøglen afkodes fra base64 og bruges som nøgle til at udføre HMAC SHA256 beregning. |
| {resourceURI} | URI præfiks (efter segment) af slutpunkterne, der kan åbnes med dette token, startende med hostname af IoT hub (ingen protocol). For eksempel`myHub.azure-devices.net/devices/device1` |
| {udløbet} | UTF8 strenge for antal sekunder siden 00:00:00 epoke UTC på 1 januar 1970. |
| {URL-kodet-resourceURI} | Lavere tilfælde kodning URL-adressen på ressourcen, små bogstaver URI |
| {Politiknavn} | Navnet på den delte access-politik, som henviser til dette symbol. Fraværende tale om tokens, der refererer til enhed registreringsdatabasen legitimationsoplysninger. |

**Note på præfiks**: feltet URI præfiks beregnes efter segment og ikke efter tegn. For eksempel `/a/b` er et præfiks for `/a/b/c` , men ikke for `/a/bc`.

Den følgende Node.js kodestykke viser en funktion, kaldet **generateSasToken** , der beregner token fra input `resourceUri, signingKey, policyName, expiresInMins`. I næste afsnit Detaljeformater initialisering de forskellige input til forskellige token use cases.

    var generateSasToken = function(resourceUri, signingKey, policyName, expiresInMins) {
        resourceUri = encodeURIComponent(resourceUri.toLowerCase()).toLowerCase();

        // Set expiration in seconds
        var expires = (Date.now() / 1000) + expiresInMins * 60;
        expires = Math.ceil(expires);
        var toSign = resourceUri + '\n' + expires;

        // Use crypto
        var hmac = crypto.createHmac('sha256', new Buffer(signingKey, 'base64'));
        hmac.update(toSign);
        var base64UriEncoded = encodeURIComponent(hmac.digest('base64'));

        // Construct autorization string
        var token = "SharedAccessSignature sr=" + resourceUri + "&sig="
        + base64UriEncoded + "&se=" + expires;
        if (policyName) token += "&skn="+policyName;
        return token;
    };

Som en sammenligning er det tilsvarende Python kode til at oprette et sikkerhedstoken:

    from base64 import b64encode, b64decode
    from hashlib import sha256
    from time import time
    from urllib import quote_plus, urlencode
    from hmac import HMAC

    def generate_sas_token(uri, key, policy_name, expiry=3600):
        ttl = time() + expiry
        sign_key = "%s\n%d" % ((quote_plus(uri)), int(ttl))
        print sign_key
        signature = b64encode(HMAC(b64decode(key), sign_key, sha256).digest())

        rawtoken = {
            'sr' :  uri,
            'sig': signature,
            'se' : str(int(ttl))
        }

        if policy_name is not None:
            rawtoken['skn'] = policy_name

        return 'SharedAccessSignature ' + urlencode(rawtoken)

> [AZURE.NOTE] Da tid gyldighed Tokenet er blevet godkendt på IoT Hub maskiner, er det vigtigt, at drift på uret på den computer, som genererer tokenet minimale.

### <a name="use-sas-tokens-in-a-device-client"></a>Brug SAS tokens i en enhed-klient

Der er to måder at få **DeviceConnect** tilladelser med IoT Hub med sikkerheds-id: Brug en [symmetriske enhed nøgle fra enheden identitet registreringsdatabasen](#use-a-symmetric-key-in-the-identity-registry), eller brug en [delt hurtigtast for politik](#use-a-shared-access-policy).

Husk, at alle funktioner, der er tilgængelige fra enheder vises af design på slutpunkter med præfiks `/devices/{deviceId}`.

> [AZURE.IMPORTANT] Den eneste måde, IoT Hub godkender en bestemt enhed bruger den symmetriske nøgle enhed identitet. I tilfælde når en politik for delt adgang bruges til at få adgang til funktioner til enheden, skal løsningen overveje komponenten udstede sikkerhedstoken som underkomponent der er tillid til.

Enhed mod slutpunkterne er (uanset protocol):

| Første eller sidste ark | Funktionalitet |
| ----- | ----------- |
| `{iot hub host name}/devices/{deviceId}/messages/events` | Sende enhed i skyen meddelelser. |
| `{iot hub host name}/devices/{deviceId}/devicebound` | Modtage meddelelser i skyen-til-enhed. |

### <a name="use-a-symmetric-key-in-the-identity-registry"></a>Bruge en symmetriske nøgle i registreringsdatabasen identitet

Når du bruger en enhed identitet symmetriske nøgle til at oprette et token på politiknavn (`skn`) element i tokenet udelades.

For eksempel skal en token, der er oprettet, for at få adgang til alle enhed funktionalitet have følgende parametre:

* ressource-URI: `{IoT hub name}.azure-devices.net/devices/{device id}`,
* logge nøgle: en hvilken som helst symmetriske nøgle til den `{device id}` identitet,
* ingen politikkens navn
* helst udløb.

Eksempel på brug af funktionen Node ville være:

    var endpoint ="myhub.azure-devices.net/devices/device1";
    var deviceKey ="...";

    var token = generateSasToken(endpoint, deviceKey, null, 60);

Resultatet, der giver adgang til alle funktioner for device1, der ville være:

    SharedAccessSignature sr=myhub.azure-devices.net%2fdevices%2fdevice1&sig=13y8ejUk2z7PLmvtwR5RqlGBOVwiq7rQR3WZ5xZX3N4%3D&se=1456971697

> [AZURE.NOTE] Det er muligt at oprette et secure token ved hjælp af værktøjet .NET [Enhed Explorer][lnk-device-explorer].

### <a name="use-a-shared-access-policy"></a>Bruge en delt access-politik

Når du opretter et token fra en politik for delt adgang, politikken navngiver felt `skn` skal være angivet til navnet på politikken anvendes. Det er også påkrævet, at politikken giver **DeviceConnect** tilladelse.

De to primære scenarier for brug af politikker for delt adgang til at få adgang til enhed funktionalitet er:

* [protocol gateways i skyen][lnk-endpoints],
* [token services] [ lnk-custom-auth] bruges til at implementere brugerdefineret godkendelsesmetode.

Da den delte access-politik kan potentielt give adgang til at oprette forbindelse som enhver enhed, er det vigtigt at bruge den rigtige ressource URI, når du opretter sikkerhedstokens. Dette er især vigtigt for sikkerhedstoken tjenester, der har til at begrænse tokenet til en bestemt enhed ved hjælp af ressource-URI. Dette punkt er mindre relevant for protocol gateways, som de allerede formidler trafikken for alle enheder.

En token tjenesten ved hjælp af allerede er oprettet delt adgang politikken **enhed** vil oprette et token med følgende parametre som et eksempel:

* ressource-URI: `{IoT hub name}.azure-devices.net/devices/{device id}`,
* logge nøgle: en af de pågældende taster af den `device` politikken,
* Politikkens navn: `device`,
* helst udløb.

Eksempel på brug af funktionen Node ville være:

    var endpoint ="myhub.azure-devices.net/devices/device1";
    var policyName = 'device';
    var policyKey = '...';

    var token = generateSasToken(endpoint, policyKey, policyName, 60);

Resultatet, der giver adgang til alle funktioner for device1, der ville være:

    SharedAccessSignature sr=myhub.azure-devices.net%2fdevices%2fdevice1&sig=13y8ejUk2z7PLmvtwR5RqlGBOVwiq7rQR3WZ5xZX3N4%3D&se=1456971697&skn=device

En gateway protocol kan bruge det samme token til alle enheder, du skal blot angive ressourcen URI til `myhub.azure-devices.net/devices`.

### <a name="use-security-tokens-from-service-components"></a>Brug sikkerhedstokens fra service-komponenter

Service-komponenter kan kun oprette sikkerhedstokens ved hjælp af delt adgang politikker give de relevante tilladelser, som beskrevet tidligere.

Dette er funktionerne tjeneste vises på slutpunkterne:

| Første eller sidste ark | Funktionalitet |
| ----- | ----------- |
| `{iot hub host name}/devices` | Oprette, opdatere, hente og slette enhed identiteter. |
| `{iot hub host name}/messages/events` | Modtage meddelelser enhed i skyen. |
| `{iot hub host name}/servicebound/feedback` | Modtage feedback til skyen-til-enhed meddelelser. |
| `{iot hub host name}/devicebound` | Sende meddelelser i skyen-til-enhed. |

Som et eksempel, vil en tjeneste genererende ved hjælp af allerede er oprettet delt adgang politikken **registryRead** oprette et token med følgende parametre:

* ressource-URI: `{IoT hub name}.azure-devices.net/devices`,
* logge nøgle: en af de pågældende taster af den `registryRead` politikken,
* Politikkens navn: `registryRead`,
* helst udløb.

    varians slutpunkt = "myhub.azure-devices.net/devices"   varians politiknavn = 'enhed'.   varians policyKey = "...";

    varians token = generateSasToken (slutpunkt, policyKey, politiknavn, 60)

Resultatet er, som vil give adgang for at læse alle enhed identiteter, er:

    SharedAccessSignature sr=myhub.azure-devices.net%2fdevices&sig=JdyscqTpXdEJs49elIUCcohw2DlFDR3zfH5KqGJo4r4%3D&se=1456973447&skn=registryRead

## <a name="supported-x509-certificates"></a>Understøttede x.509-certifikater

Du kan bruge en hvilken som helst x.509-certifikat til at godkende en enhed med IoT Hub. Dette omfatter:

-   **Et eksisterende x.509-certifikat**. En enhed er muligvis allerede et x.509-certifikat, der er knyttet til den. Enheden kan bruge dette certifikat til at godkende med IoT Hub.

-   **Et selv oprettede og selvsigneret X-509 certifikat**. En producenten af enheden eller interne deployer kan oprette disse certifikater og gemme den tilhørende privat nøgle (og certifikat) på enheden. Du kan også bruge funktioner såsom [OpenSSL] [ lnk-openssl] og [Windows SelfSignedCertificate] [ lnk-selfsigned] utility hertil.

-   **CA-signerede x.509-certifikat**. Du kan også bruge et x.509-certifikat, der er oprettet og logget af en certificering nøglecenter (CA) til at identificere en enhed og godkende en enhed med IoT Hub.

En enhed kan enten bruge et x.509-certifikat eller et sikkerhedstoken for godkendelse, men ikke begge dele.

### <a name="register-an-x509-client-certificate-for-a-device"></a>Registrere en x.509-klientcertifikat til en enhed

[Azure IoT Service SDK for C#] [ lnk-service-sdk] (version 1.0.8+) understøtter registrere en enhed, der bruger en klient-X.509-certifikat til godkendelse. Andre API'er som Importér/Eksportér enheder understøtter også x.509-klientcertifikater.

### <a name="c-support"></a>C\# Support

Klassen **RegistryManager** indeholder en automatisk metode til at registrere en enhed. Især aktivere metoderne **AddDeviceAsync** og **UpdateDeviceAsync** en bruger til at registrere og opdatere en enhed i Iot Hub enhed identitet registreringsdatabasen. Disse to metoder tage en forekomst af **enhed** som input. Klassen **enhed** omfatter en **godkendelse** egenskab, der gør det muligt for brugeren at angive primære og sekundære x.509-certifikat thumbprints. Miniature repræsenterer en SHA-1-hash af det x.509-certifikat (gemt ved hjælp af binær DER kodning). Brugere har mulighed for at angive en primær miniature eller en sekundær miniature eller begge dele. Primære og sekundære thumbprints understøttes for at kunne håndtere certifikat overgang scenarier.

> [AZURE.NOTE] IoT Hub kræver ikke eller gemme det hele x.509-klientcertifikat, kun miniature.

Her er et eksempel C\# kodestykke til at registrere en enhed med en x.509-klientcertifikat:

```
var device = new Device(deviceId)
{
  Authentication = new AuthenticationMechanism()
  {
    X509Thumbprint = new X509Thumbprint()
    {
      PrimaryThumbprint = "921BC9694ADEB8929D4F7FE4B9A3A6DE58B0790B"
    }
  }
};
RegistryManager registryManager = RegistryManager.CreateFromConnectionString(deviceGatewayConnectionString);
await registryManager.AddDeviceAsync(device);
```

### <a name="use-an-x509-client-certificate-during-runtime-operations"></a>Bruge en klient-X.509-certifikat under runtime handlinger

[Azure IoT enhed SDK til .NET] [ lnk-client-sdk] (version 1.0.11+) understøtter brugen af x.509-klientcertifikater.

### <a name="c-support"></a>C\# Support

Klassen **DeviceAuthenticationWithX509Certificate** understøtter oprettelse af  **DeviceClient** forekomster ved hjælp af en klient-X.509-certifikat.

Her er et eksempel kodestykke:

```
var authMethod = new DeviceAuthenticationWithX509Certificate("<device id>", x509Certificate);

var deviceClient = DeviceClient.Create("<IotHub DNS HostName>", authMethod);
```

## <a name="custom-device-authentication"></a>Brugerdefineret enhed godkendelse

Du kan bruge IoT Hub [enhed identitet registreringsdatabasen] [ lnk-identity-registry] til at konfigurere og enheder sikkerhedslegitimationsoplysninger og adgangskontrol ved hjælp af [tokens][lnk-sas-tokens]. Men hvis en IoT løsning allerede har en betydeligt investering i en brugerdefineret enhed identitet registreringsdatabasen og/eller godkendelse farveskema, du kan integrere denne eksisterende infrastruktur med IoT Hub ved at oprette en *token service*. Du kan bruge andre IoT funktioner i din løsning på denne måde.

En token tjeneste er en brugerdefineret skybaseret tjeneste. Det bruger en IoT Hub *delt access-politik* med **DeviceConnect** tilladelser til at oprette *enhed-fastsat* tokens. Disse tokens aktivere en enhed til at oprette forbindelse til din IoT-hub.

  ![Trin mønsterets token service][img-tokenservice]

Dette er de vigtigste trin mønsterets token service:

1. Oprette en politik for access IoT Hub, der deles med **DeviceConnect** tilladelser for IoT centrum. Du kan oprette denne politik skal skrives i [Azure portal] [ lnk-management-portal] eller fra et program. Tjenesten token bruger denne politik til at logge på tokens, programmet opretter.
2. Når en enhed har brug at få adgang til din IoT-hub, anmoder om en signeret token fra din token tjeneste. Enheden kan godkende med dit brugerdefinerede enhed identitet registreringsdatabasen/godkendelse skema til at bestemme den enhed identitet, tjenesten token bruger til at oprette tokenet.
3. Tjenesten token returnerer et token. Tokenet er oprettet ved hjælp af `/devices/{deviceId}` som `resourceURI`, med `deviceId` som den enhed, der er godkendt. Tjenesten token bruger den delte access-politik til at oprette tokenet.
4. Enheden bruger tokenet direkte med IoT hubben.

> [AZURE.NOTE] Du kan bruge klassen .NET [SharedAccessSignatureBuilder] [ lnk-dotnet-sas] eller klassen Java [IotHubServiceSasToken] [ lnk-java-sas] til at oprette et token i din token tjeneste.

Tjenesten token kan angive token udløbet efter behov. Når tokenet udløber, afbryder IoT hubben enhed forbindelsen. Derefter skal enheden anmode om et nyt id fra tjenesten token. Hvis du bruger et kort udløbet tidspunkt, øges belastning på både enheden og token-tjenesten.

For en enhed til at oprette forbindelse til din-hub, skal du stadig føje den til IoT Hub enhed identitet registreringsdatabasen – også selvom enheden bruger en token og ikke en enhed nøgle til at oprette forbindelse. Derfor, du kan fortsætte med at bruge per enhed adgangskontrol ved at aktivere eller deaktivere enhed identiteter i [IoT Hub identitet registreringsdatabasen] [ lnk-identity-registry] når enheden godkender med et token. Dette afhjælper risici ved at bruge tokens med lang udløbet gange.

### <a name="comparison-with-a-custom-gateway"></a>Sammenligning med en brugerdefineret gateway

Token service mønster er den anbefalede måde at implementere et brugerdefineret identitet registreringsdatabasen/godkendelse farveskema med IoT Hub. Det anbefales, fordi IoT Hub fortsætter med at håndtere de fleste af løsning trafikken. Der er dog tilfælde, hvor den brugerdefinerede godkendelse ordning så intertwined med den protokol, der kræves en tjeneste behandling af al trafik (*brugerdefinerede gateway*). Et eksempel på dette er [sikkerhed TLS (Transport Layer) og før delte nøgler (PSKs)][lnk-tls-psk]. Du kan finde flere oplysninger, se [protocol gateway] [ lnk-protocols] emne.

## <a name="reference-topics"></a>Referenceemner i:

De følgende referenceemner giver dig flere oplysninger om kontrol af adgang til din IoT hub.

## <a name="iot-hub-permissions"></a>IoT Hub tilladelser

I følgende tabel vises de tilladelser, du kan bruge til at styre adgangen til dine IoT-hub.

| Tilladelse            | Noter |
| --------------------- | ----- |
| **RegistryRead**      | Giver læseadgang til enhed identitet registreringsdatabasen. Du kan finde flere oplysninger, se [enhed identitet registreringsdatabasen][lnk-identity-registry]. |
| **RegistryReadWrite** | Giver læse / skrive-adgang til enhed identitet registreringsdatabasen. Du kan finde flere oplysninger, se [enhed identitet registreringsdatabasen][lnk-identity-registry]. |
| **ServiceConnect**    | Giver adgang til tjenesten mod kommunikations- og overvågning slutpunkter i skyen. For eksempel giver det tilladelse til back end-skytjenester at modtage meddelelser enhed i skyen, sende meddelelser i skyen-til-enhed og hente de tilsvarende levering bekræftelse. |
| **DeviceConnect**     | Giver adgang til enhed mod kommunikation slutpunkter. For eksempel giver det tilladelse til at sende enhed i skyen meddelelser og modtage meddelelser i skyen-til-enhed. Denne tilladelse bruges af enheder. |

## <a name="additional-reference-material"></a>Yderligere referencemateriale

Andre referenceemner i i udvikler vejledning omfatter:

- [IoT Hub slutpunkter] [ lnk-endpoints] beskrives de forskellige slutpunkter, hver IoT hub fremviser for runtime og styring handlinger.
- [(Throttling) og kvoter for] [ lnk-quotas] beskrives de kvoter, der gælder for tjenesten IoT Hub og variere den benyttede funktionsmåden kan forvente, når du bruger tjenesten.
- [IoT Hub enheder og tjenester SDK'er] [ lnk-sdks] viser en liste over de forskellige sprog SDK'er du en bruges, når du udvikler både enhed og service-programmer, der arbejder sammen med IoT Hub.
- [Forespørge sprog efter twins, metoder og job] [ lnk-query] beskrives forespørgselssprog, du kan bruge til at hente oplysninger fra IoT Hub om din enhed twins, metoder og -job.
- [Understøttelse af IoT Hub MQTT] [ lnk-devguide-mqtt] indeholder flere oplysninger om understøttelse af IoT Hub for MQTT-protokollen.

## <a name="next-steps"></a>Næste trin

Nu du har lært at styre adgangen IoT-Hub, kan du måske interesseret i Developer Guide følgende emner:

- [Bruge enhed twins til at synkronisere stat og konfigurationer][lnk-devguide-device-twins]
- [Aktivere en direkte metode på en enhed][lnk-devguide-directmethods]
- [Planlægge opgaver på flere enheder][lnk-devguide-jobs]

Hvis du gerne vil prøve nogle af de begreber, der er beskrevet i denne artikel, kan du måske interesseret i følgende IoT Hub selvstudier:

- [Introduktion til Azure IoT Hub][lnk-getstarted-tutorial]
- [Hvordan du kan sende meddelelser i skyen-til-enhed med IoT Hub][lnk-c2d-tutorial]
- [Sådan behandles IoT Hub enhed i skyen meddelelser][lnk-d2c-tutorial]

<!-- links and images -->

[img-tokenservice]: ./media/iot-hub-devguide-security/tokenservice.png
[lnk-endpoints]: iot-hub-devguide-endpoints.md
[lnk-quotas]: iot-hub-devguide-quotas-throttling.md
[lnk-sdks]: iot-hub-devguide-sdks.md
[lnk-query]: iot-hub-devguide-query-language.md
[lnk-devguide-mqtt]: iot-hub-mqtt-support.md
[lnk-openssl]: https://www.openssl.org/
[lnk-selfsigned]: https://technet.microsoft.com/library/hh848633

[lnk-resource-provider-apis]: https://msdn.microsoft.com/library/mt548492.aspx
[lnk-sas-tokens]: iot-hub-devguide-security.md#security-tokens
[lnk-amqp]: https://www.amqp.org/
[lnk-azure-resource-manager]: ../azure-resource-manager/resource-group-overview.md
[lnk-cbs]: https://www.oasis-open.org/committees/download.php/50506/amqp-cbs-v1%200-wd02%202013-08-12.doc
[lnk-event-hubs-publisher-policy]: https://code.msdn.microsoft.com/Service-Bus-Event-Hub-99ce67ab
[lnk-management-portal]: https://portal.azure.com
[lnk-sasl-plain]: http://tools.ietf.org/html/rfc4616
[lnk-identity-registry]: iot-hub-devguide-identity-registry.md
[lnk-dotnet-sas]: https://msdn.microsoft.com/library/microsoft.azure.devices.common.security.sharedaccesssignaturebuilder.aspx
[lnk-java-sas]: http://azure.github.io/azure-iot-sdks/java/service/api_reference/com/microsoft/azure/iot/service/auth/IotHubServiceSasToken.html
[lnk-tls-psk]: https://tools.ietf.org/html/rfc4279
[lnk-protocols]: iot-hub-protocol-gateway.md
[lnk-custom-auth]: iot-hub-devguide-security.md#custom-device-authentication
[lnk-x509]: iot-hub-devguide-security.md#supported-x509-certificates
[lnk-devguide-device-twins]: iot-hub-devguide-device-twins.md
[lnk-devguide-directmethods]: iot-hub-devguide-direct-methods.md
[lnk-devguide-jobs]: iot-hub-devguide-jobs.md
[lnk-service-sdk]: https://github.com/Azure/azure-iot-sdks/tree/master/csharp/service
[lnk-client-sdk]: https://github.com/Azure/azure-iot-sdks/tree/master/csharp/device
[lnk-device-explorer]: https://github.com/Azure/azure-iot-sdks/blob/master/tools/DeviceExplorer/doc/how_to_use_device_explorer.md

[lnk-getstarted-tutorial]: iot-hub-csharp-csharp-getstarted.md
[lnk-c2d-tutorial]: iot-hub-csharp-csharp-c2d.md
[lnk-d2c-tutorial]: iot-hub-csharp-csharp-process-d2c.md
