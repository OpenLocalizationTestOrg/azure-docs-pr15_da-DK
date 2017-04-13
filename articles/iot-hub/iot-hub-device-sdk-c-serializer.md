<properties
    pageTitle="Azure IoT enhed SDK til C - serialiseringsfunktionen | Microsoft Azure"
    description="Få mere at vide mere om brugen af biblioteket serialiseringsfunktion i Azure IoT enheden SDK for C"
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

# <a name="microsoft-azure-iot-device-sdk-for-c--more-about-serializer"></a>Microsoft Azure IoT enhed SDK til C – Lær mere om serialiseringsfunktion

[Første artikel](iot-hub-device-sdk-c-intro.md) i denne serie introduceret **Azure IoT enhed SDK for C**. Næste artikel angivet en mere detaljeret beskrivelse af [**IoTHubClient**](iot-hub-device-sdk-c-iothubclient.md). I denne artikel er fuldført beskrivelser af SDK ved at give en mere detaljeret beskrivelse af komponenten resterende: biblioteket **serialiseringsfunktion** .

Indledende artiklen beskrevet Sådan bruges biblioteket **serialiseringsfunktionen** til at sende hændelser, der skal og modtage meddelelser fra IoT Hub. I denne artikel udvider vi pågældende diskussion ved at give en mere fyldestgørende beskrivelse af, hvordan du dine data med makrosprog **serialiseringsfunktion** -modeller. I artiklen også indeholder flere oplysninger om hvordan biblioteket serializes meddelelser (og i nogle tilfælde, hvordan du kan styre serialisering funktionsmåden). Vi vil også beskriver nogle parametre, du kan ændre, der bestemmer størrelsen på de modeller, du opretter.

Til sidst revisits i artiklen nogle emner, der er beskrevet i forrige artikler som meddelelse og håndtering af egenskaben. Disse funktioner fungerer på samme måde, ved hjælp af biblioteket **serialiseringsfunktion** , som med biblioteket **IoTHubClient** , som vi vil finde ud af.

Alt er beskrevet i denne artikel er baseret på **serialiseringsfunktion** SDK eksempler. Hvis du vil følge med, skal du se de **simplesample\_amqp** og **simplesample\_http** programmer, der er inkluderet i Azure IoT enheden SDK til C.

Du kan finde **Azure IoT enhed SDK for C** i [Microsoft Azure IoT SDK'er](https://github.com/Azure/azure-iot-sdks) GitHub lager og få vist oplysninger om API i [C API reference](http://azure.github.io/azure-iot-sdks/c/api_reference/index.html).

## <a name="the-modeling-language"></a>Modellering sprog

[Indledende artikel](iot-hub-device-sdk-c-intro.md) i denne serie introduceret **Azure IoT enhed SDK for C** modeling sprog via det viste eksempel i den **simplesample\_amqp** program:

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

Som du kan se, er modellering sproget baseret på C makroer. Du altid starter definitionen for din med **PÅBEGYNDE\_NAVNEOMRÅDE** og slutter altid med **END\_NAVNEOMRÅDE**. Det er normalt navngive navneområdet for din virksomhed, eller som i dette eksempel det projekt, du arbejder på.

Hvad der skal være i navneområdet er model definitioner. I dette tilfælde er der en enkelt model for en anemometer. Igen skal modellen kan navngives noget, men typisk dette kaldes for enhed eller type data, du vil udveksle med IoT Hub.  

Modeller indeholder en definition af de hændelser, kan du vandindtrængen til IoT Hub ( *data*) samt de meddelelser, du kan modtage fra IoT Hub ( *Handlinger*). Som du kan se fra eksemplet, har hændelser, der en type og et navn. handlinger har et navn og valgfrie parametre (hver med en type).

Hvad er ikke vist i dette eksempel er flere datatyper, der understøttes af SDK. Vi vil gennemgå næste.

> [AZURE.NOTE] IoT Hub refererer til de data, der er en enhed sender til den som *begivenheder*, mens modellering sproget refererer til den som *data* (defineret ved hjælp af **WITH_DATA**). På samme måde, refererer IoT Hub til de data, du sender til enheder, som *meddelelser*, mens modellering sproget refererer til den som *Handlinger* (defineret ved hjælp af **WITH_ACTION**). Vær opmærksom på, at disse vilkår kan anvendes i flæng i denne artikel.

### <a name="supported-data-types"></a>Understøttede datatyper

Følgende datatyper understøttes i modeller, der er oprettet med biblioteket **serialiseringsfunktion** :

| Type                    | Beskrivelse                            |
|-------------------------|----------------------------------------|
| dobbelt                  | dobbelt præcision flydende tal |
| heltal                     | 32-bit heltal                         |
| slæk                   | enkelt præcision flydende tal |
| lang                    | langt heltal                           |
| int8\_t                 | 8-bit heltal                          |
| Int16\_t                | 16-bit heltal                         |
| Int32\_t                | 32-bit heltal                         |
| Int64\_t                | 64-bit heltal                         |
| Boolesk                    | Boolesk værdi                                |
| ASCII\_tegn\_ptr        | ASCII-streng                           |
| EDM\_dato\_tid\_forskydning | dato tidsforskydning                       |
| EDM\_GUID               | GUID                                   |
| EDM\_BINÆRE             | binært tal                                 |
| ERKLÆRE\_struktur         | kompleks datatype                      |

Lad os starte med den sidste datatype. Den **DECLARE\_struktur** gør det muligt at definere komplekse datatyper, som er grupper af de andre enkle typer. Disse grupperinger giver os mulighed at definere en model, der ser sådan ud:

```
DECLARE_STRUCT(TestType,
double, aDouble,
int, aInt,
float, aFloat,
long, aLong,
int8_t, aInt8,
uint8_t, auInt8,
int16_t, aInt16,
int32_t, aInt32,
int64_t, aInt64,
bool, aBool,
ascii_char_ptr, aAsciiCharPtr,
EDM_DATE_TIME_OFFSET, aDateTimeOffset,
EDM_GUID, aGuid,
EDM_BINARY, aBinary
);

DECLARE_MODEL(TestModel,
WITH_DATA(TestType, Test)
);
```

Vores model indeholder en enkelt data hændelse af typen **TestType**. **TestType** er en kompleks type, der indeholder flere medlemmer, som, viser de enkle datatyper understøttes af **serialiseringsfunktion** modeling language.

Med en model således, kan vi skrive programkode for at sende data til IoT-Hub, der vises på følgende måde:

```
TestModel* testModel = CREATE_MODEL_INSTANCE(MyThermostat, TestModel);

testModel->Test.aDouble = 1.1;
testModel->Test.aInt = 2;
testModel->Test.aFloat = 3.0f;
testModel->Test.aLong = 4;
testModel->Test.aInt8 = 5;
testModel->Test.auInt8 = 6;
testModel->Test.aInt16 = 7;
testModel->Test.aInt32 = 8;
testModel->Test.aInt64 = 9;
testModel->Test.aBool = true;
testModel->Test.aAsciiCharPtr = "ascii string 1";

time_t now;
time(&now);
testModel->Test.aDateTimeOffset = GetDateTimeOffset(now);

EDM_GUID guid = { { 0x00, 0x01, 0x02, 0x03, 0x04, 0x05, 0x06, 0x07, 0x08, 0x09, 0x0A, 0x0B, 0x0C, 0x0D, 0x0E, 0x0F } };
testModel->Test.aGuid = guid;

unsigned char binaryArray[3] = { 0x01, 0x02, 0x03 };
EDM_BINARY binaryData = { sizeof(binaryArray), &binaryArray };
testModel->Test.aBinary = binaryData;

SendAsync(iotHubClientHandle, (const void*)&(testModel->Test));
```

Grundlæggende, er vi tildeling af en værdi til alle medlemmer af **Test** strukturen og derefter ringe **SendAsync** for at sende hændelsen **Test** data til skyen. **SendAsync** er en hjælper funktion, der sender en enkelt data begivenhed til IoT Hub:

```
void SendAsync(IOTHUB_CLIENT_LL_HANDLE iotHubClientHandle, const void *dataEvent)
{
    unsigned char* destination;
    size_t destinationSize;
    if (SERIALIZE(&destination, &destinationSize, *(const unsigned char*)dataEvent) ==
    {
        // null terminate the string
        char* destinationAsString = (char*)malloc(destinationSize + 1);
        if (destinationAsString != NULL)
        {
            memcpy(destinationAsString, destination, destinationSize);
            destinationAsString[destinationSize] = '\0';
            IOTHUB_MESSAGE_HANDLE messageHandle = IoTHubMessage_CreateFromString(destinationAsString);
            if (messageHandle != NULL)
            {
                IoTHubClient_SendEventAsync(iotHubClientHandle, messageHandle, sendCallback, (void*)0);

                IoTHubMessage_Destroy(messageHandle);
            }
            free(destinationAsString);
        }
        free(destination);
    }
}
```

Denne funktion serializes hændelsen angivne data og sender den til IoT Hub ved hjælp af **IoTHubClient\_SendEventAsync**. Dette er den samme kode, der er beskrevet i forrige artikler (**SendAsync** indkapsler logik i en praktisk funktion).

Én andre hjælper-funktion, der er brugt i den forrige kode er **GetDateTimeOffset**. Denne funktion omdanner et givet tidspunkt til en værdi af typen **EDM\_dato\_tid\_forskydning**:

```
EDM_DATE_TIME_OFFSET GetDateTimeOffset(time_t time)
{
    struct tm newTime;
    gmtime_s(&newTime, &time);
    EDM_DATE_TIME_OFFSET dateTimeOffset;
    dateTimeOffset.dateTime = newTime;
    dateTimeOffset.fractionalSecond = 0;
    dateTimeOffset.hasFractionalSecond = 0;
    dateTimeOffset.hasTimeZone = 0;
    dateTimeOffset.timeZoneHour = 0;
    dateTimeOffset.timeZoneMinute = 0;
    return dateTimeOffset;
}
```

Hvis du kører denne kode, sendes følgende meddelelse til IoT Hub:

```
{"aDouble":1.100000000000000, "aInt":2, "aFloat":3.000000, "aLong":4, "aInt8":5, "auInt8":6, "aInt16":7, "aInt32":8, "aInt64":9, "aBool":true, "aAsciiCharPtr":"ascii string 1", "aDateTimeOffset":"2015-09-14T21:18:21Z", "aGuid":"00010203-0405-0607-0809-0A0B0C0D0E0F", "aBinary":"AQID"}
```

Bemærk, at serialiseringen JSON, som er genereret biblioteket **serialiseringsfunktion** formatet. Bemærk også, at hvert medlem af objektet fortløbende JSON svarer til medlemmer af **TestType** , som vi definerede i vores model. Værdierne, der også svare nøjagtigt til dem, der anvendes i koden. Bemærk imidlertid, de binære data er base64-kodet: "AQID" er base64 kodning af {0x01, 0x02, 0x03}.

I dette eksempel vises fordelen ved at bruge biblioteket **serialiseringsfunktion** – det giver mulighed for os til at sende JSON i skyen, uden at skulle eksplicit håndtere serialisering i vores program. Alle vi behøver at bekymre dig om at værdierne for hændelserne, der data i vores model og derefter ringe enkel API'er for at sende disse begivenheder til skyen.

Disse oplysninger, kan vi definere modeller, der indeholder området af understøttede datatyper, herunder komplekse typer (vi kan også omfatte komplekse typer i andre komplekse typer). Men han serialiseret JSON genereres af det foregående eksempel viser et vigtigt punkt. *Hvordan* vi sender data med biblioteket **serialiseringsfunktion** afgør præcis, hvordan JSON dannes. Dette bestemt punkt er, hvad vi gennemgår næste.

## <a name="more-about-serialization"></a>Mere om serialisering

Den forrige sektion fremhæver et eksempel på output genereres af biblioteket **serialiseringsfunktion** . I dette afsnit gennemgår vi, hvordan biblioteket serializes data, og hvordan du kan styre denne funktionsmåde ved hjælp af serialisering API'er.

Hvis du vil fremføre diskussion på serialisering, skal arbejder vi du med en ny model, der er baseret på en indstillet. Først skal vi giver nogle baggrundsoplysninger om dette scenario, forsøger vi at adresse.

Vi vil bruge som model en indstillet, der måler temperatur og luftfugtighed. Enkelte data, skal sendes til IoT Hub anderledes. Som standard indstillet ingresses hændelsen temperaturen én gang hver 2 minutter. Hændelsen luftfugtighed er ingressed én gang hver 15 minutter. Når enten begivenhed er ingressed, skal den indeholde et tidsstempel, der viser tid, at den tilsvarende temperaturen eller luftfugtighed blev målt.

Givet dette scenario, vi viser to forskellige måder at tilpasse dataene, og vi gennemgår effekten, modellering har på fortløbende output.

### <a name="model-1"></a>Model 1

Her er den første version af en model, der understøtter det forrige scenario:

```
BEGIN_NAMESPACE(Contoso);

DECLARE_STRUCT(TemperatureEvent,
int, Temperature,
EDM_DATE_TIME_OFFSET, Time);

DECLARE_STRUCT(HumidityEvent,
int, Humidity,
EDM_DATE_TIME_OFFSET, Time);

DECLARE_MODEL(Thermostat,
WITH_DATA(TemperatureEvent, Temperature),
WITH_DATA(HumidityEvent, Humidity)
);

END_NAMESPACE(Contoso);
```

Bemærk, at modellen indeholder to datahændelser: **temperatur** og **luftfugtighed**. I modsætning til tidligere eksempler typen af hver enkelt hændelse er en struktur, der er defineret ved hjælp af **DECLARE\_struktur**. **TemperatureEvent** omfatter et temperaturen mål og et tidsstempel **HumidityEvent** indeholder et luftfugtighed mål og et tidsstempel. Denne model giver os en naturlig måde at tilpasse data til dette scenario, der er beskrevet ovenfor. Når vi sender en begivenhed til skyen, sender vi enten et temperaturen/tidsstempel eller et par luftfugtighed/tidsstempel.

Vi kan sende en temperaturen begivenhed til skyen ved hjælp af kode som følgende:

```
time_t now;
time(&now);
thermostat->Temperature.Temperature = 75;
thermostat->Temperature.Time = GetDateTimeOffset(now);

unsigned char* destination;
size_t destinationSize;
if (SERIALIZE(&destination, &destinationSize, thermostat->Temperature) == IOT_AGENT_OK)
{
    sendMessage(iotHubClientHandle, destination, destinationSize);
}
```

Vi vil bruge faste værdier til temperatur og luftfugtighed i eksempelkoden, men Forestil dig, at vi faktisk henter disse værdier ved at indsamle de tilsvarende sensorer på indstillet.

Ovenstående kode bruger **GetDateTimeOffset** hjælper, der blev introduceret tidligere. Årsager, som bliver Ryd senere, adskiller denne kode eksplicit opgaven serialisering og sende begivenheden. Den forrige kode serializes hændelsen temperaturen i bufferen. Derefter **sendMessage** er en hjælper funktion (inkluderet i **simplesample\_amqp**), der sender en begivenhed til IoT Hub:

```
static void sendMessage(IOTHUB_CLIENT_HANDLE iotHubClientHandle, const unsigned char* buffer, size_t size)
{
    static unsigned int messageTrackingId;
    IOTHUB_MESSAGE_HANDLE messageHandle = IoTHubMessage_CreateFromByteArray(buffer, size);
    if (messageHandle != NULL)
    {
        IoTHubClient_SendEventAsync(iotHubClientHandle, messageHandle, sendCallback, (void*)(uintptr_t)messageTrackingId);

        IoTHubMessage_Destroy(messageHandle);
    }
    free((void*)buffer);
}
```

Denne kode er et undersæt af det **SendAsync** hjælpeprogram, der er beskrevet i forrige afsnit, så vi ikke kan skifte over det igen her.

Når vi kører koden forrige for at sende hændelsen temperaturen, sendes denne fortløbende formular for hændelsen til IoT Hub:

```
{"Temperature":75, "Time":"2015-09-17T18:45:56Z"}
```

Vi sender en temperatur, der er af typen **TemperatureEvent** og strukturen indeholder medlem **temperatur** og **tid** . Dette afspejles direkte i fortløbende dataene.

På samme måde, kan vi sender en luftfugtighed begivenhed med denne kode:

```
thermostat->Humidity.Humidity = 45;
thermostat->Humidity.Time = GetDateTimeOffset(now);
if (SERIALIZE(&destination, &destinationSize, thermostat->Humidity) == IOT_AGENT_OK)
{
    sendMessage(iotHubClientHandle, destination, destinationSize);
}
```

Den fortløbende formular, der sendes til IoT Hub ser således ud:

```
{"Humidity":45, "Time":"2015-09-17T18:45:56Z"}
```

Dette er igen, som forventet.

Med denne model, kan du forestille dig hvordan yderligere hændelser kan nemt tilføjes. Du definerer flere strukturer ved hjælp af **DECLARE\_struktur**, og Medtag tilsvarende begivenheden i den model ved hjælp af **med\_DATA**.

Nu, Lad os ændre modellen, så den indeholder de samme data, men med en anden struktur.

### <a name="model-2"></a>Model 2

Overvej denne alternative model til den ovenfor:

```
DECLARE_MODEL(Thermostat,
WITH_DATA(int, Temperature),
WITH_DATA(int, Humidity),
WITH_DATA(EDM_DATE_TIME_OFFSET, Time)
);
```

Vi har i dette tilfælde er slettet på **DECLARE\_struktur** makroer og blot definerer dataelementer fra vores scenarie med brug af simple typer fra modellering sprog.

Lad os ignorere lige for tidspunkt, hvor hændelsen **tid** . Med side er her i koden for at vandindtrængen **temperaturen**:

```
time_t now;
time(&now);
thermostat->Temperature = 75;

unsigned char* destination;
size_t destinationSize;
if (SERIALIZE(&destination, &destinationSize, thermostat->Temperature) == IOT_AGENT_OK)
{
    sendMessage(iotHubClientHandle, destination, destinationSize);
}
```

Denne kode sender følgende fortløbende hændelse til IoT Hub:

```
{"Temperature":75}
```

Og koden for at sende hændelsen luftfugtighed vises på følgende måde:

```
thermostat->Humidity = 45;
if (SERIALIZE(&destination, &destinationSize, thermostat->Humidity) == IOT_AGENT_OK)
{
    sendMessage(iotHubClientHandle, destination, destinationSize);
}
```

Denne kode sender dette til IoT Hub:

```
{"Humidity":45}
```

Der er så langt stadig uden overraskelser. Nu Lad os ændre Sådan bruger vi SERIALISERET makroen.

Makroen **SERIALISERET** kan tage flere data begivenheder som argumenter. Dette giver os mulighed at sekventielt hændelsen **temperatur** og **luftfugtighed** sammen og sende dem til IoT Hub i et opkald:

```
if (SERIALIZE(&destination, &destinationSize, thermostat->Temperature, thermostat->Humidity) == IOT_AGENT_OK)
{
    sendMessage(iotHubClientHandle, destination, destinationSize);
}
```

Du kan gæt, er resultatet af denne kode, som sendes to begivenheder til IoT Hub:

[

{"Temperatur": 75},

{"Luftfugtighed": 45}

]

Det vil sige, kan du forventer, at denne kode er den samme som at sende **temperatur** og **luftfugtighed** separat. Det er kun en hjælp til at overføre begge begivenheder til **SERIALISERET** i samme opkaldet. Det er dog ikke sag. I stedet sender ovenstående kode begivenheden enkelt data til IoT Hub:

{"Temperatur": 75, "luftfugtighed": 45}

Det kan virke underlig, fordi vores model definerer **temperatur** og **luftfugtighed** som to *separate* hændelser:

```
DECLARE_MODEL(Thermostat,
WITH_DATA(int, Temperature),
WITH_DATA(int, Humidity),
WITH_DATA(EDM_DATE_TIME_OFFSET, Time)
);
```

Til punktet, ikke har vi flere modellere disse begivenheder, hvor **temperatur** og **luftfugtighed** er i samme struktur:

```
DECLARE_STRUCT(TemperatureAndHumidityEvent,
int, Temperature,
int, Humidity,
);

DECLARE_MODEL(Thermostat,
WITH_DATA(TemperatureAndHumidityEvent, TemperatureAndHumidity),
);
```

Hvis vi har brugt denne model, vil det være nemmere at forstå, hvordan **temperatur** og **luftfugtighed** vil blive sendt i den samme fortløbende meddelelse. Men det ikke være Ryd hvorfor det fungerer på denne måde, når du overfører begge data begivenheder til **SERIALISERET** ved hjælp af model 2.

Denne funktionalitet er nemmere at forstå, hvis du kender de forudsætninger, der biblioteket **serialiseringsfunktion** foretager. For at få over dette skal du går tilbage til vores model:

```
DECLARE_MODEL(Thermostat,
WITH_DATA(int, Temperature),
WITH_DATA(int, Humidity),
WITH_DATA(EDM_DATE_TIME_OFFSET, Time)
);
```

Tænk på denne model i objektorienteret vilkår. I dette tilfælde vi modeling en fysisk enhed (en indstillet), og enheden omfatter attributter som **temperatur** og **luftfugtighed**.

Vi kan sende hele tilstanden for vores modellen med kode som følgende:

```
if (SERIALIZE(&destination, &destinationSize, thermostat->Temperature, thermostat->Humidity, thermostat->Time) == IOT_AGENT_OK)
{
    sendMessage(iotHubClientHandle, destination, destinationSize);
}
```

Hvis værdierne i temperatur, fugtighed og tid angives, vi vil se en begivenhed som denne sendes til IoT Hub:

```
{"Temperature":75, "Humidity":45, "Time":"2015-09-17T18:45:56Z"}
```

Nogle gange kan du kun vil sende *nogle* egenskaber for modellen til skyen (dette er især SAND, hvis modellen indeholder et stort antal datahændelser). Det er praktisk til at sende kun et undersæt af data begivenheder, som i eksemplet tidligere:

```
{"Temperature":75, "Time":"2015-09-17T18:45:56Z"}
```

Dette genererer nøjagtigt samme fortløbende hændelse som om vi har defineret en **TemperatureEvent** med medlem **temperatur** og **tid** samme måde, som vi har med model 1. Vi er i dette tilfælde kunne oprette nøjagtigt samme fortløbende begivenheden ved hjælp af en anden model (model 2), fordi vi kaldet **SERIALISERET** på en anden måde.

Det vigtigste er, at hvis du overføre flere data begivenheder til **SERIALISERET,** og klik derefter det antages, at hver enkelt hændelse er en egenskab i et enkelt JSON-objekt.

Den bedste metode afhænger af du, og hvordan du synes om din model. Hvis du sender "begivenheder" i skyen og hver enkelt hændelse indeholder et defineret sæt af egenskaber, gør den første tilgang en masse mening. I dette tilfælde du vil bruge **DECLARE\_struktur** til at definere strukturen i hver enkelt hændelse og medtage dem i modellen med den **med\_DATA** makro. Derefter sende hver enkelt hændelse, som vi har gjort i første eksemplet ovenfor. I denne metode skal du kun overfører en enkelt data begivenhed til **SERIALISERINGSFUNKTION**.

Hvis du synes om din model på en objektorienteret måde, derefter den anden tilgang måske passer til dig. I dette tilfælde elementerne defineret ved hjælp af **med\_DATA** er "egenskaberne" for objektet. Du kan overføre uanset undersæt af hændelser til **SERIALISERET** , som du kan lide, afhængigt af hvor meget af din "objektets" stat, du vil sende til skyen.

Nether tilgang er rigtigt eller forkert. Bare være opmærksom på, hvordan biblioteket **serialiseringsfunktion** fungerer, og vælg den modellering tilgang, der passer bedst tilnærmer dine behov.

## <a name="message-handling"></a>Håndtering af meddelelser

Indtil videre i denne artikel indeholder kun diskuteres afsendelse begivenheder til IoT-Hub, og ikke er adresseret modtagelse af meddelelser. Årsagen til dette er, hvad vi brug for at vide om at modtage meddelelser i høj grad har været dækket i en [tidligere artikel](iot-hub-device-sdk-c-intro.md). Tilbagekalde fra denne artikel, behandler meddelelser ved at registrere en meddelelse tilbagekaldsfunktion:

```
IoTHubClient_SetMessageCallback(iotHubClientHandle, IoTHubMessage, myWeather)
```

Du kan derefter skrive Tilbagekaldsfunktionen, der startes, når der modtages en meddelelse:

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

Denne implementering af **IoTHubMessage** kalder funktionen bestemte for hver handling i din model. For eksempel, hvis din model definerer denne handling:

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

**SetAirResistance** kaldes derefter, når meddelelsen, der sendes til din enhed.

Hvad vi ikke har forklaring endnu er ser den fortløbende version af meddelelse ud. Det vil sige, hvis du vil sende meddelelsen **SetAirResistance** til din enhed, hvordan, der ser ud?

Hvis du sender en meddelelse til en enhed, skal du gøre det via Azure IoT service SDK. Du stadig har brug at vide, hvilke streng til at sende til kalde en bestemt handling. Standardformatet for til at sende en meddelelse vises på følgende måde:

```
{"Name" : "", "Parameters" : "" }
```

Du sender en fortløbende JSON objekt med to egenskaber: **navn** er navnet på handlingen (meddelelse) og **parametre** indeholder parametre for denne handling.

For eksempel for at kalde **SetAirResistance** kan du sende meddelelsen til en enhed:

```
{"Name" : "SetAirResistance", "Parameters" : { "Position" : 5 }}
```

Navnet på handlingen skal svare nøjagtigt til en handling, der er defineret i modellen. Parameternavne skal svare til samt. Bemærk også store og små bogstaver. **Navn** og **parametre** er altid store bogstaver. Kontroller, så det svarer til er tilfældet med din handlingsnavn og parametre i din model. I dette eksempel er navnet på handlingen "SetAirResistance" og ikke "setairresistance".

Dette afsnit beskrives alt, hvad du bør vide, når begivenheder afsendelse og modtagelse meddelelser med biblioteket **serialiseringsfunktion** . Inden du går videre, Lad os dækker nogle parametre, du kan konfigurere, der styrer, hvor stor er din model.

## <a name="macro-configuration"></a>Makro konfiguration

Hvis du bruger biblioteket **serialiseringsfunktion** findes en vigtig del af SDK skal være opmærksom på i biblioteket azure-c-delt-utility.
Hvis du har klonet Azure-iot-SDK'er lager fra GitHub ved hjælp af indstillingen--rekursiv, kan du finde dette delte utility bibliotek her:

```
.\\c\\azure-c-shared-utility
```

Hvis du ikke har klonet biblioteket, kan du finde det [her](https://github.com/Azure/azure-c-shared-utility).

I biblioteket Delte utility, kan du finde følgende mappe:

```
azure-c-shared-utility\\macro\_utils\_h\_generator.
```

Denne mappe indeholder en Visual Studio-løsning, der kaldes **makro\_af websted\_h\_generator.sln**:

  ![](media/iot-hub-device-sdk-c-serializer/01-macro_utils_h_generator.PNG)

I denne løsning genereres den **makro\_utils.h** fil. Der er en standard-makro\_utils.h filen, der fulgte med SDK. Denne løsning kan du redigere nogle parametre og derefter genskabe filen sidehoved på baggrund af disse parametre.

De to vigtigste parametre til at bekymre sig om er **nArithmetic** og **nMacroParameters** der er defineret i disse to linjer, der findes i makro\_utils.tt:

```
<#int nArithmetic=1024;#>
<#int nMacroParameters=124;/*127 parameters in one macro deﬁnition in C99 in chapter 5.2.4.1 Translation limits*/#>

```

Disse værdier er standardparametrene inkluderet med SDK. Hver parameter har følgende betydning:

-   nMacroParameters – styrer, hvor mange parametre, du kan have på én DECLARE\_MODEL makro definition.

-   nArithmetic – styrer det samlede antal medlemmer, der er tilladt i en model.

Grunden disse parametre er vigtige, er de styre, hvor stor din model kan være. Overvej denne modeldefinition f.eks.:

```
DECLARE_MODEL(MyModel,
WITH_DATA(int, MyData)
);
```

Som nævnt tidligere **DECLARE\_MODEL** er bare en C makro. Navnene på modellen og **med\_DATA** -sætning (endnu en anden makro) er parametrene for **DECLARE\_MODEL**. **nMacroParameters** definerer, hvor mange parametre skal indgå i **DECLARE\_MODEL**. Dette definerer effektivt, hvor mange data begivenhed og en handling erklæringer kan du få. Som sådan med standardgrænsen på 124 betyder, at du kan definere en model med en kombination af om 60 handlinger og data begivenheder. Hvis du forsøger at overskrider denne grænse, modtager du kompilerfejl, der ser nogenlunde sådan ud:

  ![](media/iot-hub-device-sdk-c-serializer/02-nMacroParametersCompilerErrors.PNG)

Parameteren **nArithmetic** er flere oplysninger om den interne virkemåde af makrosprog end dit program.  Den styrer det samlede antal medlemmer, du kan have i din model, herunder **DECLARE_STRUCT** makroer. Hvis du begynder at se kompilerfejl som dette, skal derefter du prøve øge **nArithmetic**:

   ![](media/iot-hub-device-sdk-c-serializer/03-nArithmeticCompilerErrors.PNG)

Hvis du vil ændre disse parametre, skal du ændre værdierne i makroen\_utils.tt fil, genkompiler makroen\_af websted\_h\_generator.sln løsning, og kør kompileret program. Når du gør dette, en ny makro\_utils.h fil er oprettet og placeret i den. \\almindelige\\Inc. directory.

Hvis du vil bruge den nye version af makro\_utils.h, fjerne **serialiseringsfunktion** NuGet pakke fra din løsning og medtage **serialiseringsfunktion** Visual Studio-projekt i stedet. Dette gør det muligt for din kode for at samle mod kildekode af biblioteket serialiseringsfunktion. Dette omfatter opdaterede makroen\_utils.h. Hvis du vil gøre dette for **simplesample\_amqp**, starte ved at fjerne NuGet pakke til biblioteket serialiseringsfunktion fra løsningen:

   ![](media/iot-hub-device-sdk-c-serializer/04-serializer-github-package.PNG)

Tilføj derefter dette projekt til Visual Studio-løsning:

> . \\c\\serialiseringsfunktion\\opbygge\\windows\\serializer.vcxproj

Når du er færdig, skal din løsning se sådan ud:

   ![](media/iot-hub-device-sdk-c-serializer/05-serializer-project.PNG)

Nu, når din løsning, opdaterede makroen samle\_utils.h er inkluderet i din binært tal.

Bemærk, stigende disse høj nok kan overskride compileren begrænsninger. **NMacroParameters** er den primære parameter, som skal være pågældende til dette punkt. C99 specifikationen angiver, at et minimum af 127 parametre er tilladt i en makro definition. Microsoft compiler følger specifikationen nøjagtigt (og har en begrænsning på 127), så du ikke kan mulighed for at øge **nMacroParameters** ud over standard. Andre compilere kan gør det muligt at gøre det (for eksempel GNU compileren understøtter en højere grænse).

Vi har hidtil dækket stort set alt, hvad du bør vide om, hvordan du skrive programkode med biblioteket **serialiseringsfunktion** . Før indgå, Lad os besøger nogle emner fra forrige artikler, som du måske spekulerer du over om.

## <a name="the-lower-level-apis"></a>Underniveau API'er

Eksempelprogrammet, hvor i denne artikel fokuserer er **simplesample\_amqp**. Dette eksempel bruger det på et højere niveau (den ikke-"d") API'er til at sende begivenheder og modtage meddelelser. Hvis du bruger disse API'er, kører en baggrundstråd, som tager sig af både sende begivenheder og modtage meddelelser. Du kan dog bruge lavere niveau (d) API'er til at fjerne denne baggrundstråd og tage eksplicitte styring over, når du sender begivenheder eller modtage meddelelser fra skyen.

Som beskrevet i [forrige artikel](iot-hub-device-sdk-c-iothubclient.md), er der en række funktioner, der består af et højere API'er:

-   IoTHubClient\_CreateFromConnectionString

-   IoTHubClient\_SendEventAsync

-   IoTHubClient\_SetMessageCallback

-   IoTHubClient\_Destroy

Disse API'er er vist i **simplesample\_amqp**.

Er der også en tilsvarende række underniveau API'er.

-   IoTHubClient\_d\_CreateFromConnectionString

-   IoTHubClient\_d\_SendEventAsync

-   IoTHubClient\_d\_SetMessageCallback

-   IoTHubClient\_d\_Destroy

Bemærk, at de underniveau API'er fungerer på samme måde som beskrevet i forrige artiklerne. Hvis du vil have en baggrundstråd til at håndtere hændelser, der afsendelse og modtagelse meddelelser, kan du bruge det første sæt af API'er. Du bruger det andet sæt af API'er, hvis du vil eksplicitte styring over, når du sender og modtager data fra IoT Hub. Et sæt af API'er arbejde lige samt med biblioteket **serialiseringsfunktion** .

Et eksempel på hvordan underniveau API'er bruges med biblioteket **serialiseringsfunktion** , se den **simplesample\_http** programmet.

## <a name="additional-topics"></a>Yderligere emner

Et par andre emner værd at nævne er igen ejendom håndtering, ved hjælp af alternative enhed legitimationsoplysninger og -indstillinger. Dette er alle emnerne i en [tidligere artikel](iot-hub-device-sdk-c-iothubclient.md). Den primære er, at alle disse funktioner fungerer på samme måde med biblioteket **serialiseringsfunktion** , som med biblioteket **IoTHubClient** . Eksempelvis hvis du vil vedhæfte egenskaber til en begivenhed fra din model, skal du bruge **IoTHubMessage\_egenskaber** og **kort**\_**AddorUpdate**, på samme måde som beskrevet tidligere:

```
MAP_HANDLE propMap = IoTHubMessage_Properties(message.messageHandle);
sprintf_s(propText, sizeof(propText), "%d", i);
Map_AddOrUpdate(propMap, "SequenceNumber", propText);
```

Om hændelsen blev oprettet ud fra biblioteket **serialiseringsfunktion** eller oprettet manuelt ved hjælp af biblioteket **IoTHubClient** betyder ikke noget.

For hver anden enhed legitimationsoplysninger, ved hjælp af **IoTHubClient\_d\_Opret** fungerer lige så godt som **IoTHubClient\_CreateFromConnectionString** for tildeling af en **IOTHUB\_klient\_HÅNDTERE**.

Endelig, hvis du bruger biblioteket **serialiseringsfunktion** , kan du angive konfigurationsindstillinger med **IoTHubClient\_d\_SetOption** samme måde som du har, når du bruger biblioteket **IoTHubClient** .

En funktion, der er entydige for biblioteket **serialiseringsfunktion** er initialisering API'er. Før du kan begynde at arbejde med biblioteket, skal du ringe **serialiseringsfunktion\_initialisering**:

```
serializer_init(NULL);
```

Dette er gjort lige før du ringer til **IoTHubClient\_CreateFromConnectionString**.

På samme måde, når du er færdig arbejder med biblioteket, du sørger sidste opkaldet er til **serialiseringsfunktion\_deinit**:

```
serializer_deinit();
```

Ellers skal fungerer alle de andre funktioner, der er anført ovenfor på samme måde i biblioteket **serialiseringsfunktion** som i biblioteket **IoTHubClient** . Du kan finde flere oplysninger om nogen af disse emner, [forrige artikel](iot-hub-device-sdk-c-iothubclient.md) i denne serie.

## <a name="next-steps"></a>Næste trin

I denne artikel beskrives detaljerede oplysninger om de forskellige aspekter af biblioteket **serialiseringsfunktion** i **Azure IoT enhed SDK for C**. Hvis du skal har en god forståelse af, hvordan du bruger modeller til at sende begivenheder og modtage meddelelser fra IoT Hub med oplysninger.

Dette fuldfører også tre dele serien om, hvordan du udvikler programmer med **Azure IoT enhed SDK for C**. Dette bør være nok oplysninger til at ikke kun komme i gang, men at give dig en indgående forståelse af, hvordan API'erne fungerer. Yderligere oplysninger er der et par eksempler i SDK ikke er omfattet her. Ellers er [SDK dokumentation](https://github.com/Azure/azure-iot-sdks) en god ressource kan finde flere oplysninger.


Hvis du vil vide mere om udvikling til IoT-Hub, skal du se [IoT Hub SDK'er][lnk-sdks].

Hvis du vil udforske yderligere funktionerne i IoT-Hub, i:

- [Simulere en enhed med IoT Gateway SDK][lnk-gateway]

[lnk-sdks]: iot-hub-devguide-sdks.md

[lnk-gateway]: iot-hub-linux-gateway-sdk-simulated-device.md
