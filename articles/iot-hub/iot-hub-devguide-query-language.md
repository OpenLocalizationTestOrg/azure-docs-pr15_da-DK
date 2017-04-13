<properties
 pageTitle="Udvikler vejledning - forespørgselssprog | Microsoft Azure"
 description="Azure IoT Hub udvikler vejledning - beskrivelse af forespørgselssprog, der bruges til at hente oplysninger om twins, metoder og job fra IoT-hub"
 services="iot-hub"
 documentationCenter=".net"
 authors="fsautomata"
 manager="timlt"
 editor=""/>

<tags
 ms.service="iot-hub"
 ms.devlang="multiple"
 ms.topic="article"
 ms.tgt_pltfrm="na"
 ms.workload="na"
 ms.date="09/30/2016" 
 ms.author="elioda"/>

# <a name="reference---query-language-for-twins-and-jobs"></a>Reference - forespørgselssprog til twins og -job

## <a name="overview"></a>Oversigt

IoT Hub giver en effektiv synes godt om SQL-sprog for at hente oplysninger om [enhed twins] [ lnk-twins] og - [job][lnk-jobs]. I denne artikel indeholder:

* En introduktion til de vigtigste funktioner i IoT Hub forespørgselssprog og
* En detaljeret beskrivelse af sproget.

## <a name="getting-started-with-twin-queries"></a>Introduktion til dobbelt forespørgsler

[Enhed twins] [ lnk-twins] kan indeholde vilkårlig JSON objekter som både mærker og egenskaber. IoT Hub kan forespørgsel enhed twins som et enkelt JSON-dokument, der indeholder alle dobbelt oplysninger.
Antag f.eks, at din IoT hub twins har følgende struktur:

        {                                                                      
            "deviceId": "myDeviceId",                                            
            "etag": "AAAAAAAAAAc=",                                              
            "tags": {                                                            
                "location": {                                                      
                    "region": "US",                                                  
                    "plant": "Redmond43"                                             
                }                                                                  
            },                                                                   
            "properties": {                                                      
                "desired": {                                                       
                    "telemetryConfig": {                                             
                        "configId": "db00ebf5-eeeb-42be-86a1-458cccb69e57",            
                        "sendFrequencyInSecs": 300                                          
                    },                                                               
                    "$metadata": {                                                   
                    ...                                                     
                    },                                                               
                    "$version": 4                                                    
                },                                                                 
                "reported": {                                                      
                    "connectivity": {                                                
                        "type": "cellular"                            
                    },                                                               
                    "telemetryConfig": {                                             
                        "configId": "db00ebf5-eeeb-42be-86a1-458cccb69e57",            
                        "sendFrequencyInSecs": 300,                                         
                        "status": "Success"                                            
                    },                                                               
                    "$metadata": {                                                   
                    ...                                                
                    },                                                               
                    "$version": 7                                                    
                }                                                                  
            }                                                                    
        }

IoT Hub Fremviser enhed twins som et dokumentsamling kaldet **enheder**.
Følgende forespørgsel henter så hele sættet af enhed twins:

        SELECT * FROM devices

> [AZURE.NOTE] [IoT Hub SDK'er] [ lnk-hub-sdks] understøtter sideinddeling af store resultater.

IoT Hub giver mulighed for at hente twins filtrering med vilkårlig betingelser. For eksempel

        SELECT * FROM devices
        WHERE tags.location.region = 'US'

Henter twins med mærket **location.region** indstillet til **os**.
Booleske operatorer og matematiske sammenligninger understøttes også, f.eks.

        SELECT * FROM devices
        WHERE tags.location.region = 'US'
            AND properties.reported.telemetryConfig.sendFrequencyInSecs >= 60

Henter alle twins, der er placeret i USA, der er konfigureret til at sende telemetri mindre ofte end hvert minut. Som en hjælp er det også muligt at bruge matrixkonstanter sammen med **i** og **ni** (ikke i) operatorer. For eksempel

        SELECT * FROM devices
        WHERE property.reported.connectivity IN ['wired', 'wifi']

Henter alle twins, der rapporteres wifi eller wired connectivity. Refererer til [WHERE-delsætning] [ lnk-query-where] sektion til den fulde reference af de Filteregenskaber.

Gruppering og sammenlægninger understøttes også. For eksempel

        SELECT properties.reported.telemetryConfig.status AS status,
            COUNT() AS numberOfDevices
        FROM devices
        GROUP BY properties.reported.telemetryConfig.status

Returnerer antallet af enheder i hver telemetri konfiguration status.

        [
            {
                "numberOfDevices": 3,
                "status": "Success"
            },
            {
                "numberOfDevices": 2,
                "status": "Pending"
            },
            {
                "numberOfDevices": 1,
                "status": "Error"
            }
        ]

Ovenstående eksempel illustreres en situation, hvor tre enheder rapporteret vellykket konfiguration, to stadig anvender konfigurationen, og en rapporteret en fejl.

### <a name="c-example"></a>Eksempel på C#

Forespørgselsfunktionen, der vises af [C# service SDK] [ lnk-hub-sdks] i den klassen **RegistryManager** .
Her er et eksempel på en simpel forespørgsel:

        var query = registryManager.CreateQuery("SELECT * FROM devices", 100);
        while (query.HasMoreResults)
        {
            var page = await query.GetNextAsTwinAsync();
            foreach (var twin in page) 
            {
                // do work on twin object
            }
        }

Bemærk, hvordan objektet **forespørgsel** er en forekomst med en sidestørrelse (op til 1000), og klik derefter flere sider kan hentes ved at ringe metoderne **GetNextAsTwinAsync** flere gange.
Det er vigtigt at være opmærksom på, at Forespørgselsobjektet Fremviser flere **Næste\***, afhængigt af indstillingen deserialisering, der kræves, før forespørgslen, det vil sige dobbelt eller job objekter eller almindeligt Json skal bruges, når du bruger prognoser.

### <a name="node-example"></a>Eksempel på node

Forespørgselsfunktionen, der vises af [Node service SDK] [ lnk-hub-sdks] i den objektet **registreringsdatabasen** .
Her er et eksempel på en simpel forespørgsel:

        var query = registry.createQuery('SELECT * FROM devices', 100);
        var onResults = function(err, results) {
            if (err) {
                console.error('Failed to fetch the results: ' + err.message);
            } else {
                // Do something with the results
                results.forEach(function(twin) {
                    console.log(twin.deviceId);
                });

                if (query.hasMoreResults) {
                    query.nextAsTwin(onResults);
                }
            }
        };
        query.nextAsTwin(onResults);

Bemærk, hvordan objektet **forespørgsel** er en forekomst med en sidestørrelse (op til 1000), og klik derefter flere sider kan hentes ved at ringe metoderne **nextAsTwin** flere gange.
Det er vigtigt at være opmærksom på, at Forespørgselsobjektet Fremviser flere **Næste\***, afhængigt af indstillingen deserialisering, der kræves, før forespørgslen, det vil sige dobbelt eller job objekter eller almindeligt Json skal bruges, når du bruger prognoser.

### <a name="limitations"></a>Begrænsninger

I øjeblikket prognoser understøttes kun, når du bruger sammenlægninger, det vil sige kan kun bruge ikke-aggregeret forespørgsler `SELECT *`. Desuden sammenlægning understøttes kun i forbindelse med gruppering.

## <a name="getting-started-with-jobs-queries"></a>Introduktion til job forespørgsler

[Job] [ lnk-jobs] ikke en metode til at udføre handlinger på sæt enheder. Hver enhed dobbelt indeholder oplysninger om de job, som det er en del i en samling, kaldet **job**.
Logisk

        {                                                                      
            "deviceId": "myDeviceId",                                            
            "etag": "AAAAAAAAAAc=",                                              
            "tags": {                                                            
                ...                                                              
            },                                                                   
            "properties": {                                                      
                ...                                                                 
            },
            "jobs": [
                { 
                    "deviceId": "myDeviceId",
                    "jobId": "myJobId",    
                    "jobType": "scheduleTwinUpdate",            
                    "status": "completed",                    
                    "startTimeUtc": "2016-09-29T18:18:52.7418462",
                    "endTimeUtc": "2016-09-29T18:20:52.7418462",
                    "createdDateTimeUtc": "2016-09-29T18:18:56.7787107Z",
                    "lastUpdatedDateTimeUtc": "2016-09-29T18:18:56.8894408Z",
                    "outcome": {
                        "deviceMethodResponse": null   
                    }                                         
                },
                ...
            ]                                                             
        }

Denne samling er i øjeblikket, queriable som **devices.jobs** i forespørgselssprog IoT Hub.

For eksempel for at få alle job (tidligere og planlagte), der gælder for en enkelt enhed, kan du bruge følgende forespørgsel:

        SELECT * FROM devices.jobs
        WHERE devices.jobs.deviceId = 'myDeviceId'

Bemærk, hvordan denne forespørgsel indeholder enhed-specifikke status (og eventuelt direkte metode svaret) for hver sag, returneres.
Du kan også filtrere med vilkårlig boolesk betingelser på alle egenskaber for objekter i samlingen **devices.jobs** .
For eksempel følgende forespørgsel:

        SELECT * FROM devices.jobs
        WHERE devices.jobs.deviceId = 'myDeviceId'
            AND devices.jobs.jobType = 'scheduleTwinUpdate'
            AND devices.jobs.status = 'completed'
            AND devices.jobs.createdTimeUtc > '2016-09-01'

Henter alle færdige dobbelt Opdateringsjob for enhed **myDeviceId** , der er oprettet efter September 2016.

Det er også muligt at hente og enheder udfald af et enkelt job.

        SELECT * FROM devices.jobs
        WHERE devices.jobs.jobId = 'myJobId'

### <a name="limitations"></a>Begrænsninger
Forespørgsler om **devices.jobs** understøtter i øjeblikket ikke:

* Prognoser, det vil sige kun `SELECT *` er muligt.
* Betingelser, der refererer til enhed med dobbelt ud over jobegenskaber som vist ovenfor;
* Udføre sammenlægninger, fx antal, gennemsnit, gruppere efter.

## <a name="basics-of-an-iot-hub-query"></a>Grundlæggende om en IoT Hub forespørgsel

Hver IoT Hub forespørgsel består af en SELECT- og fra delsætninger og ved valgfri WHERE og GRUPPÉR efter delsætninger. Alle forespørgsler, der kører på en samling af JSON dokumenter, f.eks. enhed twins. FROM-delsætningen angiver dokumentsamlingen for at blive nævnt på (**enheder** eller **devices.jobs**). Derefter er anvendt filteret i WHERE-delsætningen. Hvis det er sammenlægninger, resultaterne af dette trin er grupperet som angivet i GROUP BY-delsætning, og for hver gruppe, oprettes der en række som angivet i SELECT-delsætningen.

        SELECT <select_list>
        FROM <from_specification>
        [WHERE <filter_condition>]
        [GROUP BY <group_specification>]

## <a name="from-clause"></a>FROM-delsætning

Delsætningen **fra < from_specification >** kan forudsætter kun to værdier: **fra enheder**til forespørgsel enhed twins eller **fra devices.jobs**til jobbet per enhed Forespørgselsoplysninger.

## <a name="where-clause"></a>WHERE-delsætning

Den **hvor < filter_condition >** delsætning er valgfrit. Det angiver den eller de betingelser, som JSON dokumenterne i fra samlingen skal opfylde for at blive medtaget som en del af resultatet. En hvilken som helst JSON dokument skal evalueres de angivne betingelser til "sand" medtages i resultatet.

De tilladte betingelser er beskrevet i afsnittet [udtryk og betingelser][lnk-query-expressions].

## <a name="select-clause"></a>SELECT-delsætning

SELECT-delsætningen (**Vælg < select_list >**) er obligatorisk og angiver, hvilke værdier der skal hentes fra forespørgslen. Det angiver JSON værdierne, der bruges til at oprette nye JSON objekter For hvert element i det filtrerede (og eventuelt grupperede) undersæt af samlingen fra fasen projicering genererer et nyt JSON-objekt, opbygget med de værdier, der er angivet i SELECT-delsætningen.

Dette er grammatik i SELECT-delsætningen:

        SELECT [TOP <max number>] <projection list>

        <projection_list> ::=
            '*'
            | <projection_element> AS alias [, <projection_element> AS alias]+

        <projection_element> :==
            attribute_name
            | <projection_element> '.' attribute_name
            | <aggregate>

        <aggregate> :==
            count(<projection_element>) | count()
            | avg(<projection_element>) | avg()
            | sum(<projection_element>) | sum()
            | min(<projection_element>) | min()
            | max(<projection_element>) | max()

hvor **attribute_name** refererer til en hvilken som helst egenskab for JSON dokumentet i samlingen fra. Nogle eksempler på SELECT-delsætninger kan findes i [Introduktion til dobbelt forespørgsler] [ lnk-query-getstarted] sektion.

I øjeblikket markering delsætninger anderledes end **Vælg \* ** understøttes kun i Sammenlæg forespørgsler på twins.

## <a name="group-by-clause"></a>GROUP BY-delsætning

Delsætningen **GROUP BY < group_specification >** er et valgfrit trin, der kan udføres efter det angivne i WHERE-delsætningen og før prognosen angivet i dialogboksen Vælg filter. Grupperer dokumenter baseret på værdien af en attribut. Disse grupper bruges til at generere aggregerede værdier som angivet i SELECT-delsætningen.

Et eksempel på en forespørgsel med GROUP BY er:

        SELECT properties.reported.telemetryConfig.status AS status,
            COUNT() AS numberOfDevices
        FROM devices
        GROUP BY properties.reported.telemetryConfig.status

Formel syntaks for GROUP BY er:

        GROUP BY <group_by_element>
        <group_by_element> :==
            attribute_name
            | < group_by_element > '.' attribute_name

hvor **attribute_name** refererer til en hvilken som helst egenskab for JSON dokumentet i samlingen fra. 

Delsætningen GROUP BY understøttes i øjeblikket kun, når forespørgsler twins.

## <a name="expressions-and-conditions"></a>Udtryk og betingelser

På et højt niveau, et *udtryk*:

* Evalueres til en forekomst af typen JSON (det vil sige boolesk, tal, streng, matrix eller -objekt), og
* Er defineret af manipulere data, der kommer fra enheden JSON dokumentet og konstanter ved hjælp af indbyggede operatorer og funktioner.

*Betingelser* er udtryk, der evalueres til en boolesk værdi, det vil sige en konstant, der er anderledes end boolesk **Sand** betragtes som **Falsk** (herunder **null**, **Udefineret**, en hvilken som helst objekt eller en matrix forekomst, en streng og klart på boolesk **Falsk**).

Syntaksen for udtryk er:

        <expression> ::=
            <constant> |
            attribute_name |
            unary_operator <expression> |
            <expression> binary_operator <expression> |
            <create_array_expression> |
            '(' <expression> ')'

        <constant> ::=
            <undefined_constant>
            | <null_constant> 
            | <number_constant> 
            | <string_constant> 
            | <array_constant> 

        <undefined_constant> ::= undefined
        <null_constant> ::= null
        <number_constant> ::= decimal_literal | hexadecimal_literal
        <string_constant> ::= string_literal
        <array_constant> ::= '[' <constant> [, <constant>]+ ']'

hvor:

| Symbol | Definition |
| -------------- | -----------------|
| attribute_name | En egenskab for JSON dokumentet i samlingen fra. |
| unary_operator | En hvilken som helst monadiske operator ud fra operatorer sektion. |
| binary_operator | En binær operator ud fra operatorer sektion. |
| decimal_literal | En slæk udtrykt i decimal notation. |
| hexadecimal_literal | Et tal, der er angivet af strengen '0 x' efterfulgt af en streng med hexadecimale cifre. |
| string_literal | Strengkonstanter er Unicode-strenge, der er repræsenteret af en sekvens af nul eller flere Unicode-tegn eller escape serier. Strengkonstanter omsluttes af enkelte anførselstegn (apostrof: ') eller dobbelte anførselstegn (anførselstegn: "). Tilladt skifter: `\'`, `\"`, `\\`, `\uXXXX` for Unicode-tegn, der er defineret af 4 hexadecimale cifre. |

### <a name="operators"></a>Operatorer

Følgende operatorer understøttes:

| Familie | Operatorer |
| -------------- | -----------------|
| Aritmetiske | +,-,*,/,% |
| Logisk | OG, ELLER EJ |
| Sammenligning |  =,! = <>,, < =, > = <> |

## <a name="next-steps"></a>Næste trin

Lær, hvordan du kan udføre forespørgsler i dine apps, ved hjælp af [IoT Hub SDK'er][lnk-hub-sdks].

[lnk-query-where]: iot-hub-devguide-query-language.md#where-clause
[lnk-query-expressions]: iot-hub-devguide-query-language.md#expressions-and-conditions
[lnk-query-getstarted]: iot-hub-devguide-query-language.md#getting-started-with-twin-queries

[lnk-twins]: iot-hub-devguide-device-twins.md
[lnk-jobs]: iot-hub-devguide-jobs.md
[lnk-devguide-endpoints]: iot-hub-devguide-endpoints.md
[lnk-devguide-quotas]: iot-hub-devguide-quotas-throttling.md
[lnk-devguide-mqtt]: iot-hub-mqtt-support.md

[lnk-hub-sdks]: iot-hub-devguide-sdks.md