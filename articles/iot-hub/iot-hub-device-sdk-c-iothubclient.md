<properties
    pageTitle="Azure IoT enhed SDK til C - IoTHubClient | Microsoft Azure"
    description="Få mere at vide mere om brugen af biblioteket IoTHubClient i Azure IoT enheden SDK for C"
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

# <a name="microsoft-azure-iot-device-sdk-for-c--more-about-iothubclient"></a>Microsoft Azure IoT enhed SDK til C – Lær mere om IoTHubClient

[Første artikel](iot-hub-device-sdk-c-intro.md) i denne serie introduceret **Microsoft Azure IoT enhed SDK for C**. Denne artikel forklares, der er to arkitektonisk lag i SDK. Er biblioteket **IoTHubClient** som direkte administrerer kommunikation med IoT Hub i bunden. Der er også biblioteket **serialiseringsfunktion** , der opbygger, at hvis du vil give serialisering tjenester. I denne artikel giver vi flere detaljer i biblioteket **IoTHubClient** .

Den forrige artikel beskrivelse af, hvordan du bruger biblioteket **IoTHubClient** til at sende begivenheder til IoT Hub og modtage meddelelser. I denne artikel udvider diskussionen med et beskrive, hvordan du administrerer mere præcist, *Når* du sender og modtager data indeholder en introduktion til **underniveau API'er**. Vi skal også forklarer, hvordan du kan vedhæfte egenskaber på hændelser (og hente dem fra meddelelser) ved hjælp af egenskaben håndtering af funktioner i biblioteket **IoTHubClient** . Til sidst skal giver vi yderligere forklaring af de forskellige måder at håndtere meddelelser, der er modtaget fra IoT Hub.

I artiklen afsluttes ved dækker et par diverse emner, herunder mere om enhed legitimationsoplysninger og hvordan du kan ændre funktionsmåden for **IoTHubClient** gennem konfigurationsindstillinger.

Vi bruger **IoTHubClient** SDK eksemplerne til at forklare disse emner. Hvis du vil følge med, skal du se de **iothub\_klient\_eksempel\_http** og **iothub\_klient\_eksempel\_amqp** programmer, der er inkluderet i Azure IoT enheden SDK til C. alt er beskrevet i følgende afsnit vises i disse eksempler.

Du kan finde **Azure IoT enhed SDK for C** i [Microsoft Azure IoT SDK'er](https://github.com/Azure/azure-iot-sdks) GitHub lager og få vist oplysninger om API i [C API reference](http://azure.github.io/azure-iot-sdks/c/api_reference/index.html).

## <a name="the-lower-level-apis"></a>Underniveau API'er

Den forrige artikel beskrevet grundlæggende driften af **IotHubClient** i forbindelse med den **iothub\_klient\_eksempel\_amqp** program. For eksempel forklaret det initialisering biblioteket ved hjælp af denne kode.

```
IOTHUB_CLIENT_HANDLE iotHubClientHandle;
iotHubClientHandle = IoTHubClient_CreateFromConnectionString(connectionString, AMQP_Protocol);
```

Det er også beskrevet hvordan du kan sende hændelser ved hjælp af dette funktionskald.

```
IoTHubClient_SendEventAsync(iotHubClientHandle, message.messageHandle, SendConfirmationCallback, &message);
```

I artiklen beskrives også hvordan du kan modtage meddelelser ved at registrere en tilbagekaldsfunktion.

```
int receiveContext = 0;
IoTHubClient_SetMessageCallback(iotHubClientHandle, ReceiveMessageCallback, &receiveContext);
```

I artiklen viste også, hvordan frigive ressourcer ved hjælp af kode f.eks.

```
IoTHubClient_Destroy(iotHubClientHandle);
```

Der er dog companion funktioner til hver af disse API'er:

-   IoTHubClient\_d\_CreateFromConnectionString

-   IoTHubClient\_d\_SendEventAsync

-   IoTHubClient\_d\_SetMessageCallback

-   IoTHubClient\_d\_Destroy


Disse funktioner, der er alle medtage "D" i API-navnet. End, er parametrene for hver af disse funktioner identiske med deres ikke d-versionerne. Funktionsmåden for disse funktioner er dog forskelligt, når en vigtig måde.

Når du kalder **IoTHubClient\_CreateFromConnectionString**, de underliggende biblioteker oprette en ny tråd, der kører i baggrunden. Denne tråd sender hændelser, der skal og modtager meddelelser fra IoT Hub. Ingen sådanne tråd oprettes, når du arbejder med "D" API'er. Oprettelse af baggrundstråd er en nemmere for udvikleren. Du behøver at bekymre dig om eksplicit sende begivenheder og modtage meddelelser fra IoT Hub – det sker automatisk i baggrunden. I modsætning giver "D" API'er dig eksplicitte kontrol over kommunikation med IoT-Hub, hvis du vil have den.

Dette for bedre for at forstå, Lad os se på et eksempel:

Når du kalder **IoTHubClient\_SendEventAsync**, hvad du rent faktisk foretager lægger begivenheden i en bufferen. Den baggrundstråd, der er oprettet, når du ringer til **IoTHubClient\_CreateFromConnectionString** kontinuerligt overvåger bufferen og sender data, den indeholder til IoT-Hub. Dette sker i baggrunden på samme tid, er den primære tråd udfører andet arbejde.

På samme måde, når du registrerer en tilbagekaldsfunktion for meddelelser, der bruger **IoTHubClient\_SetMessageCallback**, du instruktioner om SDK have baggrund tråden kalde Tilbagekaldsfunktionen, når meddelelsen er modtaget, uafhængigt af den primære tråd.

"D" API'er oprette ikke en baggrundstråd. En ny API skal kaldes i stedet for at sende og modtage data fra IoT Hub eksplicit. Dette er vist i følgende eksempel.

Den **iothub\_klient\_eksempel\_http** program, der er inkluderet i SDK demonstrerer underniveau API'er. I dette eksempel sender vi begivenheder til IoT Hub med kode som følgende:

```
EVENT_INSTANCE message;
sprintf_s(msgText, sizeof(msgText), "Message_%d_From_IoTHubClient_LL_Over_HTTP", i);
message.messageHandle = IoTHubMessage_CreateFromByteArray((const unsigned char*)msgText, strlen(msgText));

IoTHubClient_LL_SendEventAsync(iotHubClientHandle, message.messageHandle, SendConfirmationCallback, &message)
```

De første tre linjer oprette meddelelsen, og den sidste linje sender begivenheden. Dog som tidligere nævnt sender"" begivenheden betyder, at dataene blot er placeret i en bufferen. Intet er overført på netværket, når vi kalder **IoTHubClient\_d\_SendEventAsync**. I rækkefølge til faktisk vandindtrængen dataene til IoT-Hub, skal du ringe **IoTHubClient\_d\_DoWork**, som i dette eksempel:

```
while (1)
{
    IoTHubClient_LL_DoWork(iotHubClientHandle);
    ThreadAPI_Sleep(1000);
}
```

Denne kode (fra den **iothub\_klient\_eksempel\_http** program) gentagne gange kalder **IoTHubClient\_d\_DoWork**. Hver gang **IoTHubClient\_d\_DoWork** er kaldet det sender nogle begivenheder fra bufferen til IoT Hub og den henter en meddelelse i køen, der sendes til enheden. Tilfører betyder, hvis vi har registreret en tilbagekaldsfunktion for meddelelser, klik derefter på tilbagekald er aktiveret (hvis det er i kø fejlmeddelelser). Vi vil have registreret Sådan tilbagekaldsfunktion med kode som følgende:

```
IoTHubClient_LL_SetMessageCallback(iotHubClientHandle, ReceiveMessageCallback, &receiveContext)
```

Årsagen til, **IoTHubClient\_d\_DoWork** kaldes ofte i en løkke er, hver gang den hedder, sender programmet *nogle* bufferen begivenheder til IoT Hub og henter *Næste* meddelelse i kø til enheden. Hver opkald er ikke garanti for, at sende alle bufferlagrede begivenheder eller i kø meddelelser for at hente alle. Hvis du vil sende alle begivenheder i bufferen og derefter fortsætte med på med andre behandling kan du erstatte denne løkke med kode som følgende:

```
IOTHUB_CLIENT_STATUS status;

while ((IoTHubClient_LL_GetSendStatus(iotHubClientHandle, &status) == IOTHUB_CLIENT_OK) && (status == IOTHUB_CLIENT_SEND_STATUS_BUSY))
{
    IoTHubClient_LL_DoWork(iotHubClientHandle);
    ThreadAPI_Sleep(1000);
}
```

Denne kode kalder **IoTHubClient\_d\_DoWork** indtil alle begivenheder i bufferen er blevet sendt til IoT Hub. Bemærk dette også ikke indebærer, at alle i kø meddelelser er modtaget. En del af årsagen til dette er, at søgningen efter "alle" meddelelser ikke som deterministisk en handling. Hvad sker der, hvis du hente "alle" på meddelelserne, men derefter en anden sendes til enheden umiddelbart efter? En bedre måde at håndtere, der er en programmerede timeout. Funktionen meddelelse tilbagekald kan nulstille en timer, hver gang kaldes. Du kan derefter skrive logik for at fortsætte behandling, hvis for eksempel ingen meddelelser er modtaget i de seneste *X* sekunder.

Når du er færdig ingressing begivenheder og modtage meddelelser, skal du sørge for at ringe til den tilsvarende funktion til at rydde op ressourcer.

```
IoTHubClient_LL_Destroy(iotHubClientHandle);
```

Der er grundlæggende kun én række API'er til at sende og modtage data med en baggrundstråd og en anden række API'er, der indeholder det samme uden baggrundstråd. En masse udviklere kan foretrækker de ikke - d API'er, men de underniveau API'er er praktiske, når udvikleren ønsker eksplicitte kontrol over netværksoverførsler. For eksempel indsamler visse enheder data over tid og kun vandindtrængen begivenheder med bestemte intervaller (for eksempel én gang en time eller en gang om dagen). Underniveau API'er giver dig mulighed for at styre, eksplicit når du sender og modtager data fra IoT Hub. Andre kan blot foretrækker enkel, som de underniveau API'er giver. Alt, hvad sker der i den primære tråd i stedet for nogle arbejde sker i baggrunden.

Uanset hvilken model, du vælger, skal du sørge for at være konsekvente i hvilke API'er, du bruger. Hvis du starter ved at ringe til **IoTHubClient\_d\_CreateFromConnectionString**, være sikker på, at du kun bruge de tilsvarende underniveau API'er for eventuelle opfølgende arbejde:

-   IoTHubClient\_d\_SendEventAsync

-   IoTHubClient\_d\_SetMessageCallback

-   IoTHubClient\_d\_Destroy

-   IoTHubClient\_d\_DoWork

Modsat gælder også. Hvis du starter med **IoTHubClient\_CreateFromConnectionString**, derefter bruge de ikke - d API'er for eventuelle yderligere behandling.

I Azure IoT enheden SDK for C, se den **iothub\_klient\_eksempel\_http** programmet for en komplet eksempel på lavere niveauer API'er. Den **iothub\_klient\_eksempel\_amqp** programmet kan referere til en fuld eksempel af de ikke - d API'er.

## <a name="property-handling"></a>Håndtering af egenskab

Hidtil når vi har beskrevet sender data, har vi referere til brødteksten i meddelelsen. For eksempel kan du overveje denne kode:

```
EVENT_INSTANCE message;
sprintf_s(msgText, sizeof(msgText), "Hello World");
message.messageHandle = IoTHubMessage_CreateFromByteArray((const unsigned char*)msgText, strlen(msgText));
IoTHubClient_LL_SendEventAsync(iotHubClientHandle, message.messageHandle, SendConfirmationCallback, &message)
```

I dette eksempel sender en meddelelse til IoT Hub med teksten "Hej verden". IoT Hub kan imidlertid egenskaber, der skal knyttes til hver meddelelse. Egenskaber er navn/værdi-par, som kan knyttes til meddelelsen. For eksempel kan vi ændre den forrige kode for at vedhæfte en egenskab i en meddelelse:

```
MAP_HANDLE propMap = IoTHubMessage_Properties(message.messageHandle);
sprintf_s(propText, sizeof(propText), "%d", i);
Map_AddOrUpdate(propMap, "SequenceNumber", propText);
```

Vi start ved at ringe **IoTHubMessage\_egenskaber** og sende den i håndtaget i vores meddelelse. Hvad vi kommer tilbage er en **kort\_HÅNDTERE** reference, så vi kan begynde at tilføje egenskaber. Disse opnås ved at ringe til **kort\_AddOrUpdate**, som tager en reference til et kort\_HÅNDTAG, navnet og egenskabsværdien. Med denne API kan vi tilføje lige så mange egenskaber, som vi ønsker.

Når begivenheden læses fra **Begivenhed Hubs**, kan modtageren Optæl egenskaberne og hente deres tilsvarende værdier. For eksempel i .NET skal dette gøres ved at få adgang til [samlingen Properties for objektet EventData](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.eventdata.properties.aspx).

I det forrige eksempel, vi vedhæfte egenskaber til en begivenhed, som vi sender til IoT Hub. Egenskaber kan også knyttes til meddelelser, der er modtaget fra IoT Hub. Hvis vi vil hente egenskaber fra en meddelelse, kan vi bruge kode som følgende i vores meddelelse tilbagekaldsfunktion:

```
static IOTHUBMESSAGE_DISPOSITION_RESULT ReceiveMessageCallback(IOTHUB_MESSAGE_HANDLE message, void* userContextCallback)
{
    . . .

    // Retrieve properties from the message
    MAP_HANDLE mapProperties = IoTHubMessage_Properties(message);
    if (mapProperties != NULL)
    {
        const char*const* keys;
        const char*const* values;
        size_t propertyCount = 0;
        if (Map_GetInternals(mapProperties, &keys, &values, &propertyCount) == MAP_OK)
        {
            if (propertyCount > 0)
            {
                printf("Message Properties:\r\n");
                for (size_t index = 0; index < propertyCount; index++)
                {
                    printf("\tKey: %s Value: %s\r\n", keys[index], values[index]);
                }
                printf("\r\n");
            }
        }
    }

    . . .
}
```

Opkaldet til **IoTHubMessage\_egenskaber** returnerer den **kort\_HÅNDTERE** reference. Vi derefter overfører, der henviser til **kort\_GetInternals** at opnå en reference til en matrix med navne/værdipar (samt en optælling af egenskaberne). På dette tidspunkt er det et spørgsmål om optælling af egenskaberne for at få adgang til de værdier, vi vil.

Du behøver ikke at bruge egenskaber i dit program. Men hvis du har brug at konfigurere dem på begivenheder eller hente dem fra meddelelser, biblioteket **IoTHubClient** gør det nemt.

## <a name="message-handling"></a>Håndtering af meddelelser

Som nævnt tidligere, når du modtager mails fra IoT Hub reagerer biblioteket **IoTHubClient** ved aktivering af en registreret tilbagekaldsfunktion. Der er en returnerede parameter for denne funktion, der fortjener nogle yderligere forklaring. Her er et uddrag af Tilbagekaldsfunktionen i den **iothub\_klient\_eksempel\_http** eksempel program:

```
static IOTHUBMESSAGE_DISPOSITION_RESULT ReceiveMessageCallback(IOTHUB_MESSAGE_HANDLE message, void* userContextCallback)
{
    . . .
    return IOTHUBMESSAGE_ACCEPTED;
}
```

Bemærk, at returtypen er **IOTHUBMESSAGE\_fordeling\_RESULTATET** og i dette tilfælde, der returneres **IOTHUBMESSAGE\_ACCEPTEREDE**. Der findes andre værdier, vi kan returnere fra denne funktion, ændre, hvordan biblioteket **IoTHubClient** reagerer på meddelelsen tilbagekald. Her er indstillingerne.

-   **IOTHUBMESSAGE\_ACCEPTEREDE** – meddelelsen er blevet behandlet. Biblioteket **IoTHubClient** skal ikke aktivere Tilbagekaldsfunktionen igen med den samme meddelelse.

-   **IOTHUBMESSAGE\_afvist** – meddelelsen blev ikke behandlet, og der ikke er nogen ønske om at gøre dette i fremtiden. Biblioteket **IoTHubClient** skal ikke aktivere Tilbagekaldsfunktionen igen med den samme meddelelse.

-   **IOTHUBMESSAGE\_ABANDONED** – meddelelsen blev ikke behandlet, men biblioteket **IoTHubClient** skal aktivere Tilbagekaldsfunktionen igen med den samme meddelelse.

For de første to returnerede koder sender biblioteket **IoTHubClient** en meddelelse til IoT-Hub, der angiver, at meddelelsen skal slettes fra enheden køen og ikke leveret igen. Resultatet er den samme (meddelelsen er slettet fra enheden køen), men om meddelelsen er blevet accepteret eller afvist registreres stadig.  Registrering af denne skelnen er nyttigt at afsendere af meddelelsen, der kan lytte til feedback og find ud af, hvis en enhed har accepteret eller afvist en bestemt meddelelse.

I det sidste tilfælde meddelelsen også sendes til IoT-Hub, men det angiver, at meddelelsen skal være udleveres igen. Du kan typisk afbryde en meddelelse, hvis du støder på en fejl, men vil forsøge at behandle meddelelsen igen. I modsætning er afviser en meddelelse relevant når du støder på en uoprettelig fejl (eller hvis du blot beslutter, at du ikke vil behandle meddelelsen).

Under alle omstændigheder være opmærksom på de forskellige returnerede koder så du kan få den ønskede fra biblioteket **IoTHubClient** funktionsmåde.

## <a name="alternate-device-credentials"></a>Alternative enhed legitimationsoplysninger

Som beskrevet tidligere, er det første, du skal gøre, når du arbejder med biblioteket **IoTHubClient** at få en **IOTHUB\_klient\_HÅNDTERE** med et opkald som følgende:

```
IOTHUB_CLIENT_HANDLE iotHubClientHandle;
iotHubClientHandle = IoTHubClient_CreateFromConnectionString(connectionString, AMQP_Protocol);
```

Argumenterne, der **IoTHubClient\_CreateFromConnectionString** er forbindelsesstrengen af vores enhed og en parameter, der angiver den protokol, vi bruger til at kommunikere med IoT Hub. Forbindelsesstrengen har et format, der ser således ud:

```
HostName=IOTHUBNAME.IOTHUBSUFFIX;DeviceId=DEVICEID;SharedAccessKey=SHAREDACCESSKEY
```

Der er fire stk. af oplysninger i denne streng: IoT Hub navn, IoT Hub suffiks, enheds-ID og delte hurtigtast. Få det fulde domænenavn (fulde Domænenavn) af en IoT-hub, når du opretter din IoT hub forekomst i portalen Azure – dette giver dig IoT hub navn (den første del af det fulde Domænenavn) og IoT hub suffiks (resten af det fulde Domænenavn). Du får det enheds-ID og hurtigtast delt, når du registrerer din enhed med IoT Hub (som beskrevet i [forrige artikel](iot-hub-device-sdk-c-intro.md)).

**IoTHubClient\_CreateFromConnectionString** giver dig en metode til at initialiseret biblioteket. Hvis du foretrækker, kan du oprette en ny **IOTHUB\_klient\_HÅNDTERE** ved hjælp af disse individuelle parametre i stedet for forbindelsesstrengen. Dette opnås med følgende kode:

```
IOTHUB_CLIENT_CONFIG iotHubClientConfig;
iotHubClientConfig.iotHubName = "";
iotHubClientConfig.deviceId = "";
iotHubClientConfig.deviceKey = "";
iotHubClientConfig.iotHubSuffix = "";
iotHubClientConfig.protocol = HTTP_Protocol;
IOTHUB_CLIENT_HANDLE iotHubClientHandle = IoTHubClient_LL_Create(&iotHubClientConfig);
```

Dette giver samme resultat som **IoTHubClient\_CreateFromConnectionString**.

Det kan være tydelige, vil du ville bruge **IoTHubClient\_CreateFromConnectionString** i stedet for denne mere detaljeret metode til initialisering. Husk på, men, når du registrerer en enhed i IoT Hub du får er et enheds-ID og en enhed nøgle (ikke en forbindelsesstreng). Værktøjet **Enhedshåndtering** SDK introduceret i [forrige artikel](iot-hub-device-sdk-c-intro.md) bruger biblioteker i **Azure IoT service SDK** til at oprette forbindelsesstrengen fra enheds-ID, nøglen enhed og IoT Hub værtsnavn. Så ringe **IoTHubClient\_d\_Opret** kan være bedre, fordi det sparer dig trin til oprettelse af en forbindelsesstreng. Brug, uanset hvilken metode er praktisk.

## <a name="configuration-options"></a>Konfigurationsindstillinger

Hidtil afspejler alt beskrevet om, hvordan biblioteket **IoTHubClient** fungerer dens standardfunktionsmåde. Der er dog nogle indstillinger, du kan angive for at ændre, hvordan biblioteket fungerer. Dette opnås ved at udnytte det **IoTHubClient\_d\_SetOption** API. Overvej dette eksempel:

```
unsigned int timeout = 30000;
IoTHubClient_LL_SetOption(iotHubClientHandle, "timeout", &timeout);
```

Der er nogle indstillinger, der ofte bruges:

-   **SetBatching** (boolesk) – Hvis **Sand**, og klik derefter data, der sendes til IoT Hub sendes i batches. Hvis **Falsk**, og klik derefter meddelelser sendes enkeltvis. Standard er **Falsk**. Bemærk, at indstillingen **SetBatching** gælder kun for HTTP-protokollen og ikke til MQTT eller AMQP protokollerne.

-   **Timeout** (usigneret int) – denne værdi er repræsenteret i millisekunder. Hvis sender en HTTP-anmodning eller modtager et svar tager længere tid end nuværende tidspunkt, og klik derefter på forbindelsen får timeout.

Indstillingen batching er vigtigt. Som standard bibliotek ingresses begivenheder individuelt (en enkelt hændelse er de ting, du overfører til **IoTHubClient\_d\_SendEventAsync**). Hvis indstillingen batching er **Sand**, indsamler biblioteket så mange begivenheder som muligt fra bufferen (op til den maksimale størrelse, som IoT Hub accepterer).  Begivenhed batchen sendes til IoT Hub i et enkelt HTTP-opkald (de enkelte hændelser er samlet i en JSON matrix). Aktivere samling typisk resulterer i stor ydeevne, da du er at reducere netværk kørsler. Det reducerer også væsentligt båndbredde, da du sender et sæt af HTTP-headere med en begivenhed batchen i stedet for en række sidehoveder for hver enkelt hændelse. Medmindre du har en bestemt grund til at gøre ellers, vil du som regel til at aktivere samling.

## <a name="next-steps"></a>Næste trin

I denne artikel beskrives detaljeret funktionsmåden for biblioteket **IoTHubClient** findes i **Azure IoT enhed SDK for C**. Disse oplysninger, bør du have en god forståelse af funktionerne i biblioteket **IoTHubClient** . [Næste artikel](iot-hub-device-sdk-c-serializer.md) indeholder lignende oplysninger i biblioteket **serialiseringsfunktion** .

Hvis du vil vide mere om udvikling til IoT-Hub, skal du se [IoT Hub SDK'er][lnk-sdks].

Hvis du vil udforske yderligere funktionerne i IoT-Hub, i:

- [Simulere en enhed med IoT Gateway SDK][lnk-gateway]

[lnk-sdks]: iot-hub-devguide-sdks.md

[lnk-gateway]: iot-hub-linux-gateway-sdk-simulated-device.md
