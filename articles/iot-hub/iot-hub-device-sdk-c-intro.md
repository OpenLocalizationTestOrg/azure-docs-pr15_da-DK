<properties
    pageTitle="Med Azure IoT enhed SDK for C | Microsoft Azure"
    description="Få mere at vide om og komme i gang arbejder sammen med eksempelkoden i Azure IoT enheden SDK for C."
    services="iot-hub"
    documentationCenter=""
    authors="olivierbloch"
    manager="timlt"
    editor=""/>

<tags
     ms.service="iot-hub"
     ms.devlang="cpp"
     ms.topic="article"
     ms.tgt_pltfrm="na"
     ms.workload="na"
     ms.date="09/06/2016"
     ms.author="obloch"/>

# <a name="introducing-the-azure-iot-device-sdk-for-c"></a>Introduktion til Azure IoT enheden SDK til C

**Azure IoT enhed SDK** er et sæt af biblioteker, der er udviklet til at forenkle processen med at sende begivenheder og modtage meddelelser fra tjenesten **Azure IoT Hub** . Der findes forskellige variationer af SDK, hver målretning af en bestemt platformen, men i denne artikel beskrives **Azure IoT enhed SDK for C**.

Azure IoT enheden SDK for C skrives i ANSI C (C99) til at maksimere mobilitet. Det gør det velegnet til at betjene på et antal platforme og enheder, især hvis minimering disken og hukommelsen er en prioritet.  

Der findes en lang række platforme som SDK er testet (se den [Azure certificeret for IoT enhed katalog](https://catalog.azureiotsuite.com/) få mere at vide). Selvom i denne artikel indeholder gennemgange af eksempelkode, der kører på Windows-platformen, skal du huske på, at den kode, der er beskrevet i denne artikel er den samme på tværs af området af understøttede platforme.

I denne artikel kan du blive introduceret til arkitekturen i Azure IoT enheden SDK til C. Vi viser Sådan initialiseret biblioteket enhed, sende begivenheder til IoT Hub samt modtage meddelelser fra den. Oplysninger i denne artikel skal være nok til at komme i gang ved hjælp af SDK, men også indeholder markører til yderligere oplysninger om bibliotekerne.

## <a name="sdk-architecture"></a>SDK arkitektur

Du kan finde **Azure IoT enhed SDK for C** i [Microsoft Azure IoT SDK'er](https://github.com/Azure/azure-iot-sdks) GitHub lager og få vist oplysninger om API i [C API reference](http://azure.github.io/azure-iot-sdks/c/api_reference/index.html).

Den seneste version af bibliotekerne kan findes i den **overordnede** gren af denne lager:

  ![](media/iot-hub-device-sdk-c-intro/01-MasterBranch.PNG)

Denne lager indeholder hele familien Azure IoT enhed SDK'er. I denne artikel er dog om Azure IoT enheden SDK *for C* som kan findes i mappen **c** .

  ![](media/iot-hub-device-sdk-c-intro/02-CFolder.PNG)

* Core-implementering af SDK kan findes i den **iothub\_klient** mappe, som indeholder implementeringen af det laveste API-lag i SDK: biblioteket **IoTHubClient** . Biblioteket **IoTHubClient** indeholder API'er implementere rå beskeder til at sende meddelelser til IoT Hub samt modtager meddelelser fra den. Når du bruger dette bibliotek, du er ansvarlig for at implementere meddelelse serialisering (til sidst med serialiseringsfunktion stikprøvernes beskrevet nedenfor), men andre oplysninger om at kommunikere med IoT-Hub skal håndteres for dig.
* Mappen **serialiseringsfunktion** indeholder hjælpefunktioner og eksempler viser, hvordan du sekventielt data inden du sender til Azure IoT Hub ved hjælp af biblioteket klient. Bemærk, at brug af serialiseringsfunktionen ikke obligatorisk, og kun som et. Hvis du bruger biblioteket **serialiseringsfunktion** , starter du ved at definere en model, der angiver de hændelser, du vil sende til IoT Hub samt de meddelelser, du forventer at modtage fra den. Når modellen er defineret, giver dig SDK en API grundflade, der gør det muligt at arbejde ubesværet med begivenheder og meddelelser uden at skulle bekymre dig om serialisering detaljer.
Biblioteket, afhænger af andre Åbn kilde-biblioteker, der implementerer transport ved hjælp af flere protokoller (MQTT, AMQP).
* Biblioteket **IoTHubClient** afhænger af andre Åbn kilde biblioteker:
   * Biblioteket [Azure C delt utility](https://github.com/Azure/azure-c-shared-utility) som indeholder almindelige funktioner til grundlæggende opgaver (som streng, listen strengmanipulation, EY, osv....) det er nødvendigt på tværs af flere Azure-relaterede C SDK'er
   * Biblioteket [Azure uAMQP](https://github.com/Azure/azure-uamqp-c) , er klient side implementering af AMQP, der er optimeret til ressource begrænsning enheder.
   * Biblioteket [Azure uMQTT](https://github.com/Azure/azure-umqtt-c) som er et generelt bibliotek implementere MQTT protokollen og optimeret til ressource begrænsning enheder.

Alle dette er nemmere at forstå ved at se eksempler på kode. De følgende afsnit fører dig gennem nogle af eksempelprogrammerne, der er inkluderet i SDK. Dette bør give dig en god funktionalitet for de forskellige funktioner i de arkitektoniske lag af SDK samt en introduktion til, hvordan API'erne fungerer.

## <a name="before-running-the-samples"></a>Før du kører eksemplerne

Før du kan køre eksemplerne i Azure IoT enheden SDK for C skal du oprette en forekomst af tjenesten på abonnementet Azure, hvis du ikke allerede har en og udføre 2 opgaver:
* forberede din udviklingsmiljø
* få enhed legitimationsoplysninger.

Hvis du vil oprette en forekomst af Azure IoT Hub på abonnementet Azure skal du følge vejledningen [her](https://github.com/Azure/azure-iot-sdks/blob/master/doc/setup_iothub.md).

[Filen med vigtige](https://github.com/Azure/azure-iot-sdks/tree/master/c) inkluderet med SDK indeholder en vejledning til at forberede dit udviklingsmiljø og få enhed legitimationsoplysninger.
De følgende afsnit indeholder nogle yderligere kommentarer på disse instruktioner.

### <a name="preparing-your-development-environment"></a>Forberede din udviklingsmiljø

Mens pakker kan bruges til visse platforme (såsom NuGet til Windows eller apt_get for Debian og Ubuntu) og eksemplerne med disse pakker, når de er tilgængelige på fremgangsmåde Detaljeformater hvordan du opretter biblioteket og eksemplerne direkte udgør koden.

Du skal først hente en kopi af SDK fra GitHub og derefter bygge kilden. Du skal modtage en kopi af kilden fra den **overordnede** gren af [GitHub lager](https://github.com/Azure/azure-iot-sdks).

Når du har hentet en kopi af kilden, skal du benytte de fremgangsmåde, der er beskrevet i SDK artikel ["Forberede din udviklingsmiljø"](https://github.com/Azure/azure-iot-sdks/blob/master/c/doc/devbox_setup.md).


Følgende er nogle tip, der kan hjælpe dig med at udføre fremgangsmåden i guiden Forberedelse:

-   Når du installerer værktøjet **CMake** , skal du vælge muligheden for at tilføje **CMake** til systemet sti for **alle brugere** (føje til **den aktuelle bruger** fungerer også):

  ![](media/iot-hub-device-sdk-c-intro/08-CMake.PNG)


-   Før du åbner **udvikler kommandoprompten for VS2015**, Installer værktøjerne ciffer kommandolinjen. For at installere disse værktøjer skal du benytte følgende fremgangsmåde:

    1. Start installationsprogrammet **Visual Studio 2015** (eller vælger **Microsoft Visual Studio 2015** fra **programmer og funktioner** i Kontrolpanel, og vælg **Skift**).
    
    2. Sørg for, at funktionen **Ciffer til Windows** er valgt i installationsprogrammet, men du kan også vælge at kontrollere indstillingen **GitHub udvidelse til Visual Studio** til IDE-integration:

        ![](media/iot-hub-device-sdk-c-intro/10-GitTools.PNG)

    3. Fuldfør konfigurationsguiden for at installere værktøjerne.

    4. Føje ciffer værktøjer **bin** -mappen til miljøvariablen system **PATH** . I Windows ser ud som følger:

        ![](media/iot-hub-device-sdk-c-intro/11-GitToolsPath.PNG)


Når du har fuldført alle de trin, der er beskrevet på siden ["Forberede din udviklingsmiljø"](https://github.com/Azure/azure-iot-sdks/blob/master/c/doc/devbox_setup.md) , er du klar til at samle eksempelprogrammerne.

### <a name="obtaining-device-credentials"></a>Hente enhed legitimationsoplysninger

Nu hvor dit udviklingsmiljø er konfigureret, er det næste, du skal gøre at få et sæt enhed legitimationsoplysninger.  For en enhed skal kunne få adgang til en IoT hub, skal du først føje enheden til IoT Hub enhed registreringsdatabasen. Når du tilføjer din enhed får du vist et sæt enhed legitimationsoplysninger, som du skal bruge for at kameraet, for at kunne oprette forbindelse til en IoT hub. Eksempelprogrammer, vil vi se på i næste afsnit forventer disse legitimationsoplysninger i form af en **enhed forbindelsesstreng**.

Der er et par værktøjerne i SDK Åbn kilde lager til at administrere IoT hubben. En er et program, der hedder enhed Explorer, den anden opgave er en node.js baseret flere platforme CLI værktøj, der hedder iothub explorer Windows. Du kan læse mere om disse værktøjer [her](https://github.com/Azure/azure-iot-sdks/blob/master/doc/manage_iot_hub.md).

Som vi gennemgår kører eksemplerne i Windows i denne artikel, vi ved hjælp af værktøjet enhed Explorer. Men du kan også bruge iothub explorer, hvis du foretrækker CLI værktøjer.

Værktøjet [Enhed Explorer](https://github.com/Azure/azure-iot-sdks/tree/master/tools/DeviceExplorer) bruger Azure IoT service biblioteker til at udføre forskellige funktioner på IoT-Hub, herunder tilføje enheder. Hvis du bruger enhed Explorer til at tilføje en enhed, får du en tilsvarende forbindelsesstreng. Du skal bruge denne forbindelsesstreng at gøre eksemplet programmer kører.

I tilfælde af, at du ikke er allerede kender, processen, beskriver, hvordan du bruger enhed Explorer til at tilføje en enhed, og få en enhed forbindelsesstreng følgende procedure.

Du kan finde en Windows installer for værktøjet enhed Explorer på [SDK slip side](https://github.com/Azure/azure-iot-sdks/releases). Men du kan også køre værktøjet direkte fra dens kode åbne **[DeviceExplorer.sln](https://github.com/Azure/azure-iot-sdks/blob/master/tools/DeviceExplorer/DeviceExplorer.sln)** i **Visual Studio-2015** og opbygning af løsningen.

Når du kører programmet får du vist denne grænseflade:

  ![](media/iot-hub-device-sdk-c-intro/03-DeviceExplorer.PNG)

Angiv din **IoT Hub forbindelsesstreng** i det første felt, og klik på **Opdater**. Dette konfigurerer værktøjet, så den kan kommunikere med IoT Hub.

Klik på **fanen** , når forbindelsesstrengen IoT Hub er konfigureret:

  ![](media/iot-hub-device-sdk-c-intro/04-ManagementTab.PNG)

Dette er, hvor du vil administrere de enheder, der er registreret i IoT-hubben.

Du kan oprette en enhed ved at klikke på knappen **Opret** . Der vises en dialogboks med en række udfyldt på forhånd nøgler (primære og sekundære). Alt, du skal gøre, er Angiv et **Enheds-ID** , og klik derefter på **Opret**.

  ![](media/iot-hub-device-sdk-c-intro/05-CreateDevice.PNG)

Når enheden er oprettet, opdateres listen enheder med alle registrerede enheder, herunder den, du lige har oprettet. Hvis du højreklikker på din nye enhed, får du vist denne menu:

  ![](media/iot-hub-device-sdk-c-intro/06-RightClickDevice.PNG)

Hvis du vælger indstillingen **Kopiér forbindelsesstrengen for valgte enhed** , er forbindelsesstrengen til din enhed kopieret til Udklipsholder. Gemme en kopi af forbindelsesstrengen. Du skal bruge den, når du kører de eksempelprogrammer, der er beskrevet i næste afsnit.

Når du har udført ovenstående trin, er du klar til at begynde at køre kode. Begge eksempler har en konstant øverst i den primære kildefil, der gør det muligt at angive en forbindelsesstreng. For eksempel den tilsvarende linje fra den **iothub\_klient\_eksempel\_amqp** program vises på følgende måde.

```
static const char* connectionString = "[device connection string]";
```

Hvis du vil følge med, indtast din enhed forbindelsesstreng her, genkompiler løsningen, og du vil kunne køre eksemplet.

## <a name="iothubclient"></a>IoTHubClient

I den **iothub\_klient** mappe i azure-iot-SDK'er lager, der er en **eksempler** mappe, der indeholder et program kaldet **iothub\_klient\_eksempel\_amqp**.

Windows-versionen af det **iothub\_klient\_eksempel\_ampq** program omfatter følgende løsningen i Visual Studio:

  ![](media/iot-hub-device-sdk-c-intro/12-iothub-client-sample-amqp.PNG)

Denne løsning indeholder et enkelt projekt. Det er værd at bemærke, at der er fire NuGet-pakker, der er installeret i denne løsning:

- Microsoft.Azure.C.SharedUtility
- Microsoft.Azure.IoTHub.AmqpTransport
- Microsoft.Azure.IoTHub.IoTHubClient
- Microsoft.Azure.uamqp

Du skal altid pakken **Microsoft.Azure.C.SharedUtility** , når du arbejder med SDK. Da dette eksempel er afhængig af AMQP, skal du også medtage **Microsoft.Azure.uamqp** og **Microsoft.Azure.IoTHub.AmqpTransport** pakkerne (der er tilsvarende pakker til MQTT og HTTP). Fordi udsnittet bruger biblioteket **IoTHubClient** , skal du også medtage pakken **Microsoft.Azure.IoTHub.IoTHubClient** i din løsning.

Du kan finde implementering for eksempelprogrammet i den **iothub\_klient\_eksempel\_amqp.c** kildefilen.

Vi bruger denne Northwind for at gennemgå Hvad er påkrævet for at bruge biblioteket **IoTHubClient** .

### <a name="initializing-the-library"></a>Initialisering af biblioteket

> [AZURE.NOTE] Før du begynder at arbejde med bibliotekerne, kan du skal udføre nogle bestemte initialisering platform. Hvis du planlægger at bruge AMQP på Linux skal du eksempelvis initialiseret biblioteket OpenSSL. Eksemplerne i [GitHub lager](https://github.com/Azure/azure-iot-sdks) kalder utility funktionen **platform_init** , når klienten starter, og kald funktionen **platform_deinit** før du afslutter. Disse funktioner er angivet i filen "platform.h" sidehoved. Du skal undersøge definitioner af disse funktioner for din target platform i [lager](https://github.com/Azure/azure-iot-sdks) til at afgøre, om du vil medtage en hvilken som helst platform initialisering kode i din kunde.

Hvis du vil begynde at arbejde med bibliotekerne, du skal først tildeler en IoT Hub klient handle:

```
IOTHUB_CLIENT_HANDLE iotHubClientHandle;
iotHubClientHandle = IoTHubClient_CreateFromConnectionString(connectionString, AMQP_Protocol);
```

Bemærk, at vi der passerer en kopi af vores enhed forbindelsesstreng til denne funktion (den, vi der fås fra enheden Explorer). Vi kan også angive den protokol, som vi vil bruge. I dette eksempel bruges AMQP, men MQTT og HTTP er også en indstilling.

Når du har et gyldigt **IOTHUB\_klient\_HÅNDTERE**, kan du begynde at ringe til API'er for at sende begivenheder og modtage meddelelser fra IoT Hub. Vi vil se på Næste.

### <a name="sending-events"></a>Afsendelse af hændelser

Afsendelse af hændelser til IoT Hub kræver, at du benytte følgende fremgangsmåde:

Først skal oprette en meddelelse:

```
EVENT_INSTANCE message;
sprintf_s(msgText, sizeof(msgText), "Message_%d_From_IoTHubClient_Over_AMQP", i);
message.messageHandle = IoTHubMessage_CreateFromByteArray((const unsigned char*)msgText, strlen(msgText);
```

Send derefter meddelelsen:

```
IoTHubClient_SendEventAsync(iotHubClientHandle, message.messageHandle, SendConfirmationCallback, &message);
```

Hver gang du sender en meddelelse, angiver du en reference til en tilbagekaldsfunktion, der startes, når dataene sendes:

```
static void SendConfirmationCallback(IOTHUB_CLIENT_CONFIRMATION_RESULT result, void* userContextCallback)
{
    EVENT_INSTANCE* eventInstance = (EVENT_INSTANCE*)userContextCallback;
    (void)printf("Confirmation[%d] received for message tracking id = %d with result = %s\r\n", callbackCounter, eventInstance->messageTrackingId, ENUM_TO_STRING(IOTHUB_CLIENT_CONFIRMATION_RESULT, result));
    /* Some device specific action code goes here... */
    callbackCounter++;
    IoTHubMessage_Destroy(eventInstance->messageHandle);
}
```

Bemærk, at opkaldet til den **IoTHubMessage\_Destroy** fungerer, når du er færdig med meddelelsen. Du skal foretage dette opkald frigive de ressourcer, der er allokeret, da du oprettede meddelelsen.

### <a name="receiving-messages"></a>Modtage meddelelser

Fejlmeddelelsen vises, er en asynkron handling. Først skal registrere du et tilbagekald skal aktiveres, når enheden modtager en meddelelse:

```
int receiveContext = 0;
IoTHubClient_SetMessageCallback(iotHubClientHandle, ReceiveMessageCallback, &receiveContext);
```

Den sidste parameter er en ugyldig markøren til det ønskede sted. Det er en markør til et heltal i stikprøven, men det kan være en markør til en mere kompleks datastruktur. Dette gør det muligt for Tilbagekaldsfunktionen til at betjene på delt tilstand med Kalderen af denne funktion.

Når enheden modtager en meddelelse, aktiveres Tilbagekaldsfunktionen registrerede:

```
static IOTHUBMESSAGE_DISPOSITION_RESULT ReceiveMessageCallback(IOTHUB_MESSAGE_HANDLE message, void* userContextCallback)
{
    int* counter = (int*)userContextCallback;
    const char* buffer;
    size_t size;
    if (IoTHubMessage_GetByteArray(message, (const unsigned char**)&buffer, &size) == IOTHUB_MESSAGE_OK)
    {
        (void)printf("Received Message [%d] with Data: <<<%.*s>>> & Size=%d\r\n", *counter, (int)size, buffer, (int)size);
    }

    /* Some device specific action code goes here... */
    (*counter)++;
    return IOTHUBMESSAGE_ACCEPTED;
}
```

Bemærk, at du bruger den **IoTHubMessage\_GetByteArray** funktionen til at hente den meddelelse, som i dette eksempel er en streng.

### <a name="uninitializing-the-library"></a>Ophæver initialisering biblioteket

Når du er færdig begivenheder afsendelse og modtagelse meddelelser, kan du annullere initialisering biblioteket IoT. Gør du ved at udstede følgende funktionskald:

```
IoTHubClient_Destroy(iotHubClientHandle);
```

Dette frigør de ressourcer, der er tildelt tidligere af den **IoTHubClient\_CreateFromConnectionString** funktionen.

Som du kan se, er det nemt at sende begivenheder og modtage meddelelser, der indeholder biblioteket **IoTHubClient** . Biblioteket håndterer detaljerne for at kommunikere med IoT hubben, herunder hvilken protokol du skal bruge (fra perspektivet udvikleren, er det en simpel konfigurationsindstilling).

Biblioteket **IoTHubClient** giver også præcis kontrol over Sådan sekventielt de hændelser, der sender din enhed til IoT Hub. I nogle tilfælde er dette en fordel, men i andre tilfælde er dette en implementering detaljer, som du ikke vil bekymre dig. Hvis det er tilfældet, kan du overveje at bruge **serialiseringsfunktion** biblioteket, der skal gennemgås i næste afsnit.

## <a name="serializer"></a>Serialiseringsfunktionen

Biblioteket **serialiseringsfunktion** er objekter placeret oven på biblioteket **IoTHubClient** i SDK. Det bruger biblioteket **IoTHubClient** til den underliggende kommunikation med IoT-Hub, men den lægger modelfunktioner, der fjerner arbejdsbyrden, der får en meddelelse serialisering fra udvikleren. Hvordan fremgår fungerer dette bibliotek bedste af et eksempel.

I **serialiseringsfunktion** mappe i azure-iot-SDK'er lager er en **eksempler** mappe, der indeholder et program kaldet **simplesample\_amqp**. Windows-versionen af dette eksempel indeholder følgende løsningen i Visual Studio:

  ![](media/iot-hub-device-sdk-c-intro/14-simplesample_amqp.PNG)

Som i forrige eksempel, indeholder denne flere NuGet pakker:

- Microsoft.Azure.C.SharedUtility
- Microsoft.Azure.IoTHub.AmqpTransport
- Microsoft.Azure.IoTHub.IoTHubClient
- Microsoft.Azure.IoTHub.Serializer
- Microsoft.Azure.uamqp

Vi har set de fleste af disse i det forrige eksempel, men **Microsoft.Azure.IoTHub.Serializer** er nyt. Dette er påkrævet, når vi bruger biblioteket **serialiseringsfunktion** .

Du kan finde implementeringen af eksempelprogrammet i den **simplesample\_amqp.c** fil.

De følgende afsnit hjælper dig med de vigtigste dele af dette eksempel.

### <a name="initializing-the-library"></a>Initialisering af biblioteket

Hvis du vil begynde at arbejde med biblioteket **serialiseringsfunktion** , skal du ringe til initialisering API'er:

```
serializer_init(NULL);

IOTHUB_CLIENT_HANDLE iotHubClientHandle = IoTHubClient_CreateFromConnectionString(connectionString, AMQP_Protocol);

ContosoAnemometer* myWeather = CREATE_MODEL_INSTANCE(WeatherStation, ContosoAnemometer);
```

Opkaldet til den **serialiseringsfunktion\_initialisering** funktion er et enkeltstående opkald og bruges til at initialiseret underliggende biblioteket. Derefter skal du ringe til den **IoTHubClient\_CreateFromConnectionString** funktion, som er den samme API som i eksemplet **IoTHubClient** . Dette opkald angiver din enhed forbindelsesstreng (dette er også, hvor du Vælg protokollen, du vil bruge). Bemærk, at dette eksempel bruger AMQP som transport, men kunne har brugt HTTP.

Til sidst skal ringe på **Opret\_MODEL\_forekomst** funktionen. Bemærk, at **WeatherStation** er navneområdet for modellen og **ContosoAnemometer** er navnet på modellen. Når model forekomsten er oprettet, kan du bruge den til at begynde at sende begivenheder og modtage meddelelser. Men det er vigtigt at forstå, hvad en model er.

### <a name="defining-the-model"></a>Definere modellen

En model i biblioteket **serialiseringsfunktion** definerer de hændelser, der kan sende din enhed til IoT Hub og de meddelelser, kaldet *Handlinger* på det sprog, modellering, som det kan modtage. Du definerer en model, ved hjælp af en række C makroer som i den **simplesample\_amqp** eksempel program:

```
BEGIN_NAMESPACE(WeatherStation);

DECLARE_MODEL(ContosoAnemometer,
WITH_DATA(ascii_char_ptr, DeviceId),
WITH_DATA(double, WindSpeed),
WITH_ACTION(TurnFanOn),
WITH_ACTION(TurnFanOff),
WITH_ACTION(SetAirResistance, int, Position)
);

END_NAMESPACE(WeatherStation);
```

Den **BEGYNDER\_NAVNEOMRÅDE** og **END\_NAVNEOMRÅDE** makroer begge tage navneområdet for modellen som argument. Det forventes, at alt mellem disse makroer er definitionen af din /-modellerne og datastrukturer, som modellerne bruger.

I dette eksempel er der en enkelt model kaldet **ContosoAnemometer**. Denne model definerer to hændelser, der kan sende din enhed til IoT Hub: **DeviceId** og **vindhastighed**. Den definerer også tre handlinger (meddelelser), der kan modtage din enhed: **TurnFanOn**, **TurnFanOff**og **SetAirResistance**. Hver enkelt hændelse har en type, og hver handling har et navn (og eventuelt et sæt af parametre).

Definere en API overflader, du kan bruge til at sende hændelser til IoT Hub samt svare på meddelelser, der sendes til enheden, hændelser og handlinger, der er defineret i modellen. Det er bedst forstået gennem et eksempel.

### <a name="sending-events"></a>Afsendelse af hændelser

Modellen definerer de hændelser, du kan sende til IoT Hub. I dette eksempel, der betyder, at en af de to hændelser, der er defineret ved hjælp af **WITH_DATA** makroen. Eksempelvis hvis du vil sende en **vindhastighed** begivenhed til en IoT-hub, er der et par trin involveret i at dette sker. Først er ved at angive de data, vi vil sende:

```
myWeather->WindSpeed = 15;
```

Den model, som vi definerede tidligere giver os mulighed at gøre dette ved at angive et medlem af en **struktur**. Dernæst skal sekventielt vi hændelsen vi vil sende:

```
unsigned char* destination;
size_t destinationSize;

SERIALIZE(&destination, &destinationSize, myWeather->WindSpeed);
```

Denne kode serializes begivenhed til en bufferen (refereres til af **destination**). Til sidst skal sender vi hændelsen til IoT hub med denne kode:

```
sendMessage(iotHubClientHandle, destination, destinationSize);
```

Dette er en hjælper funktion i eksempelprogrammet, der sender vores fortløbende begivenhed til IoT Hub:

```
static void sendMessage(IOTHUB_CLIENT_HANDLE iotHubClientHandle, const unsigned char* buffer, size_t size)
{
    static unsigned int messageTrackingId;
    IOTHUB_MESSAGE_HANDLE messageHandle = IoTHubMessage_CreateFromByteArray(buffer, size);
    if (messageHandle != NULL)
    {
        if (IoTHubClient_SendEventAsync(iotHubClientHandle, messageHandle, sendCallback, (void*)(uintptr_t)messageTrackingId) != IOTHUB_CLIENT_OK)
        {
            printf("failed to hand over the message to IoTHubClient");
        }
        else
        {
            printf("IoTHubClient accepted the message for delivery\r\n");
        }

        IoTHubMessage_Destroy(messageHandle);
    }
    free((void*)buffer);
    messageTrackingId++;
}
```

Denne kode minder meget om hvad vi så i den **iothub\_klient\_eksempel\_amqp** -program, hvor vi har oprettet en meddelelse fra en bytematrix og derefter bruges **IoTHubClient\_SendEventAsync** til at sende det til IoT Hub. Når, vi lige har frigive håndtaget meddelelse og serialiseret databufferen vi allokeres tidligere.

Andet til sidste parameter for **IoTHubClient\_SendEventAsync** er en reference til en tilbagekaldsfunktion, der kaldes, når dataene er blevet sendt. Her er et eksempel på en tilbagekaldsfunktion:

```
void sendCallback(IOTHUB_CLIENT_CONFIRMATION_RESULT result, void* userContextCallback)
{
    int messageTrackingId = (intptr_t)userContextCallback;

    (void)printf("Message Id: %d Received.\r\n", messageTrackingId);

    (void)printf("Result Call Back Called! Result is: %s \r\n", ENUM_TO_STRING(IOTHUB_CLIENT_CONFIRMATION_RESULT, result));
}
```

Den anden parameter er en markør på brugerkontekst; samme markøren vi overføres til **IoTHubClient\_SendEventAsync**. I dette tilfælde konteksten er en enkel tæller, men det kan være noget, du vil.

Det er alt, der er at sende begivenheder. Den eneste, der vises til venstre for at dække er Sådan modtage meddelelser.

### <a name="receiving-messages"></a>Modtage meddelelser

Fejlmeddelelsen vises fungerer på samme måde som meddelelser arbejde i biblioteket **IoTHubClient** . Først skal registrere du en meddelelse tilbagekaldsfunktion:

```
IoTHubClient_SetMessageCallback(iotHubClientHandle, IoTHubMessage, myWeather)
```

Derefter skal skrive du Tilbagekaldsfunktionen, der startes, når der modtages en meddelelse:

```
static IOTHUBMESSAGE_DISPOSITION_RESULT IoTHubMessage(IOTHUB_MESSAGE_HANDLE message, void* userContextCallback)
{
    IOTHUBMESSAGE_DISPOSITION_RESULT result;
    const unsigned char* buffer;
    size_t size;
    if (IoTHubMessage_GetByteArray(message, &buffer, &size) != IOTHUB_MESSAGE_OK)
    {
        printf("unable to IoTHubMessage_GetByteArray\r\n");
        result = EXECUTE_COMMAND_ERROR;
    }
    else
    {
        /*buffer is not zero terminated*/
        char* temp = malloc(size + 1);
        if (temp == NULL)
        {
            printf("failed to malloc\r\n");
            result = EXECUTE_COMMAND_ERROR;
        }
        else
        {
            memcpy(temp, buffer, size);
            temp[size] = '\0';
            EXECUTE_COMMAND_RESULT executeCommandResult = EXECUTE_COMMAND(userContextCallback, temp);
            result =
                (executeCommandResult == EXECUTE_COMMAND_ERROR) ? IOTHUBMESSAGE_ABANDONED :
                (executeCommandResult == EXECUTE_COMMAND_SUCCESS) ? IOTHUBMESSAGE_ACCEPTED :
                IOTHUBMESSAGE_REJECTED;
            free(temp);
        }
    }
    return result;
}
```

Denne kode er dele af et – det er den samme for en løsning. Denne funktion modtager meddelelsen og tager sig af routing til den relevante funktion gennem opkaldet til **EXECUTE\_KOMMANDOEN**. Funktionen kaldet afhænger på dette tidspunkt definitionen af handlingerne i vores model.

Når du definerer en handling i din model, er det nødvendigt at implementere en funktion, der kaldes, når enheden modtager den tilsvarende meddelelse. For eksempel, hvis din model definerer denne handling:

```
WITH_ACTION(SetAirResistance, int, Position)
```

Du skal definere en funktion med denne signatur:

```
EXECUTE_COMMAND_RESULT SetAirResistance(ContosoAnemometer* device, int Position)
{
    (void)device;
    (void)printf("Setting Air Resistance Position to %d.\r\n", Position);
    return EXECUTE_COMMAND_SUCCESS;
}
```

Bemærk, at navnet på funktionen svarer til navnet på handlingen i modellen og, at parametrene for funktionen svarer til de specificerede til handlingen. Den første parameter er altid påkrævet og indeholder en markør til forekomsten af vores model.

Når enheden modtager en meddelelse, der svarer til denne signatur, kaldes den tilsvarende funktion. Derfor, bortset fra at de skal medtage dele af et kode fra **IoTHubMessage**, modtage meddelelser er bare et spørgsmål om definerer en simpel funktion for hver handling, der er defineret i modellen.

### <a name="uninitializing-the-library"></a>Ophæver initialisering biblioteket

Når du er færdig, sender data og modtage meddelelser, kan du annullere initialisering biblioteket IoT:

```
        DESTROY_MODEL_INSTANCE(myWeather);
    }
    IoTHubClient_Destroy(iotHubClientHandle);
}
serializer_deinit();
```

Hver af disse tre funktioner justerer med de tre initialiseringsfunktioner, der er beskrevet tidligere. Ringe til disse API'er sikrer, at du frigøre tidligere tildelte ressourcer.

## <a name="next-steps"></a>Næste trin

I denne artikel dækket grundlæggende brug af biblioteker på **Azure IoT enhed SDK for C**. Det får du nok oplysninger til at forstå, hvad der skal medtages i SDK, dens arkitektur, og hvordan du kommer i gang arbejde med Windows-eksempler. Næste artikel fortsætter beskrivelsen af SDK ved at forklare [mere at vide om biblioteket IoTHubClient](iot-hub-device-sdk-c-iothubclient.md).

Hvis du vil vide mere om udvikling til IoT-Hub, skal du se [IoT Hub SDK'er][lnk-sdks].

Hvis du vil udforske yderligere funktionerne i IoT-Hub, i:

- [Simulere en enhed med IoT Gateway SDK][lnk-gateway]


[lnk-file upload]: iot-hub-csharp-csharp-file-upload.md
[lnk-create-hub]: iot-hub-rm-template-powershell.md
[lnk-c-sdk]: iot-hub-device-sdk-c-intro.md
[lnk-sdks]: iot-hub-devguide-sdks.md

[lnk-gateway]: iot-hub-linux-gateway-sdk-simulated-device.md
