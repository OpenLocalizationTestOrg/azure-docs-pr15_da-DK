## <a name="typical-output"></a>Typisk output

Nedenfor er et eksempel på output skrives til logfilen ved prøven Hello World. Fanen og ny linje-tegn er føjet til læsbarheden:

```
[{
    "time": "Mon Apr 11 13:48:07 2016",
    "content": "Log started"
}, {
    "time": "Mon Apr 11 13:48:48 2016",
    "properties": {
        "helloWorld": "from Azure IoT Gateway SDK simple sample!"
    },
    "content": "aGVsbG8gd29ybGQ="
}, {
    "time": "Mon Apr 11 13:48:55 2016",
    "properties": {
        "helloWorld": "from Azure IoT Gateway SDK simple sample!"
    },
    "content": "aGVsbG8gd29ybGQ="
}, {
    "time": "Mon Apr 11 13:49:01 2016",
    "properties": {
        "helloWorld": "from Azure IoT Gateway SDK simple sample!"
    },
    "content": "aGVsbG8gd29ybGQ="
}, {
    "time": "Mon Apr 11 13:49:04 2016",
    "content": "Log stopped"
}]
```

## <a name="code-snippets"></a>Kodestykker

I dette afsnit beskrives nogle vigtige dele af koden i eksemplet Hello World.

### <a name="gateway-creation"></a>Gateway-oprettelse

Udvikleren skal skrive *gateway proces*. Dette program opretter den interne infrastruktur (broker), indlæser modulerne og indstiller alt til at fungere korrekt. SDK indeholder funktionen **Gateway_Create_From_JSON** , så du kan initialiseres en gateway fra en JSON-fil. Funktionen **Gateway_Create_From_JSON** skal du angive det stien til en fil i JSON, der angiver modulerne, der skal indlæses. 

Du kan finde koden til gateway-proces i eksemplet Hej verden i [main.c] [ lnk-main-c] fil. For læsbarheden viser af en snippet nedenfor en forkortet version af koden gateway proces. Dette program opretter en gateway og venter for brugeren at trykke på **ENTER** før den nedbryde gatewayen. 

```
int main(int argc, char** argv)
{
    GATEWAY_HANDLE gateway;
    if ((gateway = Gateway_Create_From_JSON(argv[1])) == NULL)
    {
        printf("failed to create the gateway from JSON\n");
    }
    else
    {
        printf("gateway successfully created from JSON\n");
        printf("gateway shall run until ENTER is pressed\n");
        (void)getchar();
        Gateway_LL_Destroy(gateway);
    }
    return 0;
}
```

JSON-indstillingsfil indeholder en liste over moduler, der skal indlæses. Hvert modul skal angive a:

- **module_name**: et entydigt navn til modulet.
- **module_path**: stien til biblioteket, som indeholder modulet. Til Linux er en .so-fil, i Windows er en .dll-fil.
- **argumenter**: alle konfigurationsoplysninger, der er brug for modulet.

JSON-filen indeholder også links mellem de moduler, der vil blive overført til broker. Et link har to egenskaber:
- **kilde**: et modulnavn fra den `modules` afsnit, eller "\*".
- **vask**: et modulnavn fra den `modules` afsnit

Hvert hyperlink definerer en meddelelsesruten og retning. Meddelelser fra modulet `source` der skal leveres i modulet `sink`. Den `source` kan sættes til "\*", der angiver, at der kan modtages meddelelser fra et modul af `sink`.

Følgende eksempel viser den JSON-indstillingsfil, der bruges til at konfigurere Hello World prøven på Linux. Alle meddelelser, der er produceret af modulet `hello_world` vil blive brugt i modulet `logger`. Om et modul kræver et argument, afhænger af designet af modulet. Modulet logføring tager et argument, som er stien til filen, og modulet Hej verden tager ikke ingen argumenter i dette eksempel:

```
{
    "modules" :
    [ 
        {
            "module name" : "logger",
            "module path" : "./modules/logger/liblogger_hl.so",
            "args" : {"filename":"log.txt"}
        },
        {
            "module name" : "hello_world",
            "module path" : "./modules/hello_world/libhello_world_hl.so",
            "args" : null
        }
    ],
    "links" :
    [
        {
            "source" : "hello_world",
            "sink" : "logger"
        }
    ]
}
```

### <a name="hello-world-module-message-publishing"></a>Hej verden modul meddelelse udgivelse

Du kan finde den kode, der bruges af modulet "Velkommen" til at offentliggøre meddelelser i ['hello_world.c'] [ lnk-helloworld-c] fil. Af en snippet nedenfor viser en ændret version med yderligere kommentarer og nogle fejlbehandlingskode fjernet for læsbarheden:

```
int helloWorldThread(void *param)
{
    // create data structures used in function.
    HELLOWORLD_HANDLE_DATA* handleData = param;
    MESSAGE_CONFIG msgConfig;
    MAP_HANDLE propertiesMap = Map_Create(NULL);
    
    // add a property named "helloWorld" with a value of "from Azure IoT
    // Gateway SDK simple sample!" to a set of message properties that
    // will be appended to the message before publishing it. 
    Map_AddOrUpdate(propertiesMap, "helloWorld", "from Azure IoT Gateway SDK simple sample!")

    // set the content for the message
    msgConfig.size = strlen(HELLOWORLD_MESSAGE);
    msgConfig.source = HELLOWORLD_MESSAGE;

    // set the properties for the message
    msgConfig.sourceProperties = propertiesMap;
    
    // create a message based on the msgConfig structure
    MESSAGE_HANDLE helloWorldMessage = Message_Create(&msgConfig);

    while (1)
    {
        if (handleData->stopThread)
        {
            (void)Unlock(handleData->lockHandle);
            break; /*gets out of the thread*/
        }
        else
        {
            // publish the message to the broker
            (void)Broker_Publish(handleData->brokerHandle, helloWorldMessage);
            (void)Unlock(handleData->lockHandle);
        }

        (void)ThreadAPI_Sleep(5000); /*every 5 seconds*/
    }

    Message_Destroy(helloWorldMessage);

    return 0;
}
```

### <a name="hello-world-module-message-processing"></a>Hej verden modul meddelelsesbehandling

Modulet Hej verden skal aldrig behandle meddelelser, der udgiver broker andre moduler. Det gør gennemførelsen af meddelelse-tilbagekald i modulet Hej verden en Nej-op-funktion.

```
static void HelloWorld_Receive(MODULE_HANDLE moduleHandle, MESSAGE_HANDLE messageHandle)
{
    /* No action, HelloWorld is not interested in any messages. */
}
```

### <a name="logger-module-message-publishing-and-processing"></a>Logføring modul meddelelse, udgivelse og behandling

Modulet logføring modtager meddelelser fra broker og skriver dem til en fil. Virksomheden udgiver aldrig nogen meddelelser. Koden i modulet logføring kalder derfor aldrig funktionen **Broker_Publish** .

Funktionen **Logger_Recieve** i [logger.c] [ lnk-logger-c] fil er broker kalder for at levere meddelelser til modulet logføring tilbagekaldet. Af en snippet nedenfor viser en ændret version med yderligere kommentarer og nogle fejlbehandlingskode fjernet for læsbarheden:

```
static void Logger_Receive(MODULE_HANDLE moduleHandle, MESSAGE_HANDLE messageHandle)
{

    time_t temp = time(NULL);
    struct tm* t = localtime(&temp);
    char timetemp[80] = { 0 };

    // Get the message properties from the message
    CONSTMAP_HANDLE originalProperties = Message_GetProperties(messageHandle); 
    MAP_HANDLE propertiesAsMap = ConstMap_CloneWriteable(originalProperties);

    // Convert the collection of properties into a JSON string
    STRING_HANDLE jsonProperties = Map_ToJSON(propertiesAsMap);

    //  base64 encode the message content
    const CONSTBUFFER * content = Message_GetContent(messageHandle);
    STRING_HANDLE contentAsJSON = Base64_Encode_Bytes(content->buffer, content->size);

    // Start the construction of the final string to be logged by adding
    // the timestamp
    STRING_HANDLE jsonToBeAppended = STRING_construct(",{\"time\":\"");
    STRING_concat(jsonToBeAppended, timetemp);

    // Add the message properties
    STRING_concat(jsonToBeAppended, "\",\"properties\":"); 
    STRING_concat_with_STRING(jsonToBeAppended, jsonProperties);

    // Add the content
    STRING_concat(jsonToBeAppended, ",\"content\":\"");
    STRING_concat_with_STRING(jsonToBeAppended, contentAsJSON);
    STRING_concat(jsonToBeAppended, "\"}]");

    // Write the formatted string
    LOGGER_HANDLE_DATA *handleData = (LOGGER_HANDLE_DATA *)moduleHandle;
    addJSONString(handleData->fout, STRING_c_str(jsonToBeAppended);
}
```

## <a name="next-steps"></a>Næste trin

Se følgende emner for at få mere for at vide om, hvordan du bruger IoT Gateway SDK:

- [IoT Gateway SDK – sender enheden til sky meddelelser med en simuleret enhed ved hjælp af Linux][lnk-gateway-simulated].
- [Azure IoT Gateway SDK] [ lnk-gateway-sdk] på GitHub.

<!-- Links -->
[lnk-main-c]: https://github.com/Azure/azure-iot-gateway-sdk/blob/master/samples/hello_world/src/main.c
[lnk-helloworld-c]: https://github.com/Azure/azure-iot-gateway-sdk/blob/master/modules/hello_world/src/hello_world.c
[lnk-logger-c]: https://github.com/Azure/azure-iot-gateway-sdk/blob/master/modules/logger/src/logger.c
[lnk-gateway-sdk]: https://github.com/Azure/azure-iot-gateway-sdk/
[lnk-gateway-simulated]: ../articles/iot-hub/iot-hub-linux-gateway-sdk-simulated-device.md