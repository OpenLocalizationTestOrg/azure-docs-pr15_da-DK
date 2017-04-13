<properties 
    pageTitle="Programmet indsigt datamodel" 
    description="Beskriver de egenskaber, der er eksporteret fra fortløbende eksport i JSON, og anvendes som filtre." 
    services="application-insights" 
    documentationCenter=""
    authors="alancameronwills" 
    manager="douge"/>

<tags 
    ms.service="application-insights" 
    ms.workload="tbd" 
    ms.tgt_pltfrm="ibiza" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="03/21/2016" 
    ms.author="awills"/>

# <a name="application-insights-export-data-model"></a>Programmet indsigt eksport-datamodel

Denne tabel viser egenskaberne for telemetri, der sendes fra [Programmet indsigt](app-insights-overview.md) SDK'er til portalen. Du får vist disse egenskaber i data output fra [Fortløbende eksportere](app-insights-export-telemetry.md).
De vises også i egenskabsfiltre i [Metrisk Stifinder](app-insights-metrics-explorer.md) og [Diagnosticering søge](app-insights-diagnostic-search.md).

Peger på Bemærk:

* `[0]`i disse tabeller angiver et punkt i den sti, hvor du skal indsætte et indeks men det er ikke altid 0.
* Tid varighed er i en mikrosekunder, så 10000000 tiendedele == 1 sekund.
* Datoer og klokkeslæt er UTC, og er givet i ISO-formatet`yyyy-MM-DDThh:mm:ss.sssZ`

Der findes flere [eksempler](app-insights-export-telemetry.md#code-samples) , der viser, hvordan du bruger dem.



## <a name="example"></a>Eksempel

    // A server report about an HTTP request
    {
    "request": [ 
      {
        "urlData": { // derived from 'url'
          "host": "contoso.org",
          "base": "/",
          "hashTag": "" 
        },
        "responseCode": 200, // Sent to client
        "success": true, // Default == responseCode<400
        // Request id becomes the operation id of child events 
        "id": "fCOhCdCnZ9I=",  
        "name": "GET Home/Index",
        "count": 1, // 100% / sampling rate
        "durationMetric": {
          "value": 1046804.0, // 10000000 == 1 second
          // Currently the following fields are redundant:
          "count": 1.0,
          "min": 1046804.0,
          "max": 1046804.0,
          "stdDev": 0.0,
          "sampledValue": 1046804.0
        },
        "url": "/"
      }
    ],
    "internal": {
      "data": {
        "id": "7f156650-ef4c-11e5-8453-3f984b167d05",
        "documentVersion": "1.61"
      }
    },
    "context": {
      "device": { // client browser
        "type": "PC",
        "screenResolution": { },
        "roleInstance": "WFWEB14B.fabrikam.net"
      },
      "application": { },
      "location": { // derived from client ip
        "continent": "North America",
        "country": "United States",
        // last octagon is anonymized to 0 at portal:
        "clientip": "168.62.177.0", 
        "province": "",
        "city": ""
      },
      "data": {
        "isSynthetic": true, // we identified source as a bot
        // percentage of generated data sent to portal:
        "samplingRate": 100.0, 
        "eventTime": "2016-03-21T10:05:45.7334717Z" // UTC
      },
      "user": {
        "isAuthenticated": false,
        "anonId": "us-tx-sn1-azr", // bot agent id
        "anonAcquisitionDate": "0001-01-01T00:00:00Z",
        "authAcquisitionDate": "0001-01-01T00:00:00Z",
        "accountAcquisitionDate": "0001-01-01T00:00:00Z"
      },
      "operation": {
        "id": "fCOhCdCnZ9I=",
        "parentId": "fCOhCdCnZ9I=",
        "name": "GET Home/Index"
      },
      "cloud": { },
      "serverDevice": { },
      "custom": { // set by custom fields of track calls
        "dimensions": [ ],
        "metrics": [ ]
      },
      "session": {
        "id": "65504c10-44a6-489e-b9dc-94184eb00d86",
        "isFirst": true
      }
    }
  }




## <a name="context"></a>Kontekst

Alle typer telemetri sammen med en kontekst sektion. Ikke alle disse felter overføres med hvert datapunkt.



|Sti|Type|Noter|
|---|---|---|
| context.Custom.dimensions [0]  | objekt]  | Nøgle-værdi streng par Angiv ved brugerdefinerede egenskaber parameter. Vigtige Maks 100, værdier Maks 1024. Mere end 100 entydige værdier, egenskaben kan søges, men kan ikke bruges til segmentering. Maks 200 taster per ikey.  |
| context.Custom.Metrics [0]  | objekt]  | Nøgle-værdi-par angive brugerdefinerede mål parameter og TrackMetrics. Vigtige Maks 100, værdierne være numeriske. |
| context.data.eventTime | streng | UTC |
| context.data.isSynthetic | Boolesk værdi | Anmodning om ser ud til at komme fra en bot eller web test. |
| context.data.samplingRate | tal | Procentdel af telemetri genereres af SDK, som sendes til portalen. Område 0,0 100.0.|
| context.Device | objekt | Klientenhed |
| context.Device.browser | streng | Internet Explorer, Chrome... |
| context.device.browserVersion | streng | Chrome 48.0... |
| context.device.deviceModel | streng | |
| context.device.deviceName | streng | |
| context.Device.id | streng | |
| context.Device.locale | streng | Da GB, de-DE... |
| context.Device.Network | streng | |
| context.device.oemName | streng | |
| context.device.osVersion | streng | Host OS |
| context.device.roleInstance | streng | ID for server host |
| context.device.roleName | streng | |
| context.Device.type | streng | PC, Browser... |
| context.Location | objekt | Udledt fra clientip. |
| context.Location.City | streng | Afledt clientip, hvis den kendes  |
| context.Location.clientip | streng | Sidste oktagon anonyme til 0. |
| context.Location.continent | streng | |
| context.Location.Country | streng | |
| context.Location.Province | streng | Stat eller provins |
| context.operation.id | streng | Elementer, der har samme handlings-id'et vises som relaterede elementer på portalen. Normalt anmodnings-id. |
| context.operation.Name | streng | URL-adresse eller anmodning |
| context.operation.parentId | streng | Gør det muligt for indlejrede relaterede elementer. |
| context.session.id | streng | Id for en gruppe af handlinger fra samme kilde. En periode på 30 minutter uden en handling signaler slutningen af en session. |
| context.session.isFirst | Boolesk værdi | |
| context.user.accountAcquisitionDate | streng | |
| context.user.anonAcquisitionDate | streng | |
| context.user.anonId | streng | |
| context.user.authAcquisitionDate | streng | [Godkendte brugere](app-insights-api-custom-events-metrics.md#authenticated-users) |
| context.user.isAuthenticated | Boolesk værdi | |
| internal.data.documentVersion | streng | |
| Internal.data.id | streng | |



## <a name="events"></a>Begivenheder

Brugerdefinerede hændelser, der genereres af [TrackEvent()](app-insights-api-custom-events-metrics.md#track-event). 


|Sti|Type|Noter|
|---|---|---|
| Tæl begivenhed [0] | heltal | 100 / ([udvalg](app-insights-sampling.md) rente). Eksempel 4 =&gt; 25%. |
| navn på denne begivenhed [0] | streng | Navn på denne begivenhed.  Maksimal længde 250. |
| begivenhed [0] URL-adresse | streng | |
| begivenhed [0] urlData.base | streng | |
| begivenhed [0] urlData.host | streng | |

## <a name="exceptions"></a>Undtagelser

Rapporter [undtagelser](app-insights-asp-net-exceptions.md) i serveren og i browseren. 


|Sti|Type|Noter|
|---|---|---|
| basicException [0] samling | streng | |
| basicException [0] Tæl | heltal | 100 / ([udvalg](app-insights-sampling.md) rente). Eksempel 4 =&gt; 25%. |
| basicException [0] exceptionGroup | streng | |
| basicException [0] exceptionType | streng | |streng | |
| basicException [0] failedUserCodeMethod | streng | |
| basicException [0] failedUserCodeAssembly | streng | |
| basicException [0] handledAt | streng | |
| basicException [0] hasFullStack | Boolesk værdi | |
| basicException [0]-id | streng | |
| basicException [0] metode | streng | |
| basicException [0] meddelelse | streng | Undtagelsesmeddelelsen. Maksimal længde 10k.|
| basicException [0] outerExceptionMessage | streng | |
| basicException [0] outerExceptionThrownAtAssembly | streng | |
| basicException [0] outerExceptionThrownAtMethod | streng | |
| basicException [0] outerExceptionType | streng | |
| basicException [0] outerId | streng | |
| basicException [0] parsedStack [0] samling | streng | |
| basicException [0] parsedStack [0] filnavn | streng | |
| basicException [0] parsedStack [0] niveau | heltal | |
| basicException [0] parsedStack [0] linje | heltal | |
| basicException [0] parsedStack [0] metode | streng | |
| basicException [0] stak | streng | Maksimal længde 10k|
| basicException [0] typeName | streng | |



## <a name="trace-messages"></a>Spore meddelelser

Sendt [TrackTrace](app-insights-api-custom-events-metrics.md#track-trace)og [logføring kort](app-insights-asp-net-trace-logs.md).


|Sti|Type|Noter|
|---|---|---|
| meddelelse [0] loggerName | streng ||
| [0] meddelelsesparametre | streng ||
| meddelelse [0] rå | streng | Logmeddelelsen, Maks 10k. |
| meddelelse [0] severityLevel | streng | |



## <a name="remote-dependency"></a>Ekstern afhængighed

Sendt af TrackDependency. Bruges til at rapport ydeevne og brugen af [opkald til afhængigheder](app-insights-asp-net-dependencies.md) på serveren, og AJAX opkald i browseren.

|Sti|Type|Noter|
|---|---|---|
| remoteDependency [0] asynkron | Boolesk værdi | |
| remoteDependency [0] baseName | streng |  |
| remoteDependency [0] commandName | streng | For eksempel "hjem/indeks" |
| remoteDependency [0] Tæl | heltal | 100 / ([udvalg](app-insights-sampling.md) rente). Eksempel 4 =&gt; 25%. |
| remoteDependency [0] dependencyTypeName | streng | HTTP, SQL... |
| remoteDependency [0] durationMetric.value | tal | Tid fra opkaldet til fuldførelse af svar ved afhængighed |
| remoteDependency [0]-id | streng | |
| remoteDependency [0] navn | streng | URL-adressen. Maksimal længde 250.|
| remoteDependency [0] resultCode | streng | fra http-afhængighed |
| remoteDependency [0] succes | Boolesk værdi | |
| remoteDependency [0] type | streng | HTTP, Sql... |
| remoteDependency [0] URL-adresse | streng |  Maksimal længde 2000 |
| remoteDependency [0] urlData.base | streng | Maksimal længde 2000 |
| remoteDependency [0] urlData.hashTag | streng | |
| remoteDependency [0] urlData.host | streng | Maksimal længde 200 |


## <a name="requests"></a>Anmodninger om

Sendt af [TrackRequest](app-insights-api-custom-events-metrics.md#track-request). Standard moduler Brug dette til rapporter serverens svartid, målt på serveren. 


|Sti|Type|Noter|
|---|---|---|
| anmodning om [0] Tæl | heltal | 100 / ([udvalg](app-insights-sampling.md) rente). For eksempel: 4 =&gt; 25%. |
| anmodning om [0] durationMetric.value | tal | Tid fra anmodning om frem til svaret. 1e7 == 1s |
| anmodnings [0]-id | streng | Handlings-id |
| anmodning om [0] navn | streng | Hent/INDLÆG + base URL-adresse.  Maksimal længde 250 |
| anmodning om [0] responseCode | heltal | HTTP-svar sendt til klient |
| anmodning om [0] succes | Boolesk værdi | Standard == (responseCode &lt; 400) |
| URL-anmodningen [0] | streng | Eksklusive host |
| anmodning om [0] urlData.base | streng | |
| anmodning om [0] urlData.hashTag | streng |  |
| anmodning om [0] urlData.host | streng | |


## <a name="page-view-performance"></a>Sidevisning ydeevne

Sendt af browseren. Måler tid at behandle en side fra bruger start af anmodning om at få vist fuldført (eksklusive asynkron AJAX opkald).

Kontekst værdierne viser klient OS og browserversion. 


|Sti|Type|Noter|
|---|---|---|
| clientPerformance [0] clientProcess.value | heltal | Tid fra slutningen af modtager HTML til at vise siden. |
| clientPerformance [0] navn | streng | |
| clientPerformance [0] networkConnection.value | heltal | Tid, det tager at oprette en netværksforbindelse. |
| clientPerformance [0] receiveRequest.value | heltal | Tid fra slutningen af sende anmodningen til at modtage HTML-koden i et svar. |
| clientPerformance [0] sendRequest.value | heltal | Tid fra ført til at sende HTTP-anmodningen. |
| clientPerformance [0] total.value | heltal | Tid fra start til at sende anmodningen til visning af siden. |
| clientPerformance [0] URL-adresse | streng | URL-adressen for denne anmodning |
| clientPerformance [0] urlData.base | streng | |
| clientPerformance [0] urlData.hashTag | streng | |
| clientPerformance [0] urlData.host | streng | |
| clientPerformance [0] urlData.protocol | streng | |

## <a name="page-views"></a>Sidevisninger

Sendt af trackPageView() eller [stopTrackPage](app-insights-api-custom-events-metrics.md#page-view)

|Sti|Type|Noter|
|---|---|---|
| få vist [0] Tæl | heltal | 100 / ([udvalg](app-insights-sampling.md) rente). Eksempel 4 =&gt; 25%. |
| få vist [0] durationMetric.value | heltal | Værdi, du kan også angive i trackPageView() eller ved startTrackPage() - stopTrackPage(). Ikke den samme som clientPerformance værdier. |
| visningsnavn [0] | streng | Sidetitel.  Maksimal længde 250 |
| få vist [0] URL-adresse | streng | |
| få vist [0] urlData.base | streng | |
| få vist [0] urlData.hashTag | streng | |
| få vist [0] urlData.host | streng | |



## <a name="availability"></a>Tilgængelighed

Rapporter [tilgængelighed web test](app-insights-monitor-web-app-availability.md).

|Sti|Type|Noter|
|---|---|---|
| tilgængelighed [0] availabilityMetric.name | streng | tilgængelighed |
| tilgængelighed [0] availabilityMetric.value | tal |1.0 eller 0,0 |
| tilgængelighed [0] Tæl | heltal | 100 / ([udvalg](app-insights-sampling.md) rente). Eksempel 4 =&gt; 25%. |
| tilgængelighed [0] dataSizeMetric.name | streng | |
| tilgængelighed [0] dataSizeMetric.value | heltal | |
| tilgængelighed [0] durationMetric.name | streng | |
| tilgængelighed [0] durationMetric.value | tal | Varigheden af test. 1e7 == 1s |
| tilgængelighed [0] meddelelse | streng | Fejl ved diagnosticering |
| tilgængelighed [0] resultat | streng | Gang/fejl |
| tilgængelighed [0] runLocation | streng | Http nødvendig geografisk kilde |
| tilgængelighed [0] testName | streng | |
| tilgængelighed [0] testRunId | streng | |
| tilgængelighed [0] testTimestamp | streng | |




## <a name="metrics"></a>Målepunkter

Oprettet af TrackMetric().

Metriske værdien findes i context.custom.metrics[0]

Eksempel:

    {
     "metric": [ ],
     "context": {
     ...
     "custom": {
        "dimensions": [
          { "ProcessId": "4068" }
        ],
        "metrics": [
          {
            "dispatchRate": {
              "value": 0.001295,
              "count": 1.0,
              "min": 0.001295,
              "max": 0.001295,
              "stdDev": 0.0,
              "sampledValue": 0.001295,
              "sum": 0.001295
            }
          }
         } ] }
    }

## <a name="about-metric-values"></a>Om metriske værdier

Metriske værdier, både i metriske rapporter og et andet sted, der rapporteres med en standard-objektstruktur. Eksempel:

      "durationMetric": {
        "name": "contoso.org",
        "type": "Aggregation",
        "value": 468.71603053650279,
        "count": 1.0,
        "min": 468.71603053650279,
        "max": 468.71603053650279,
        "stdDev": 0.0,
        "sampledValue": 468.71603053650279
      }

Aktuelt - via det muligvis ændre fremover - i alle værdier, der er rapporteret fra de almindelige SDK moduler, `count==1` og kun den `name` og `value` felter er nyttige. Det eneste tilfælde, hvor de skal være forskellige ville være, hvis du skrive din egen TrackMetric opkald hvor du angive de andre parametre. 

Formålet med de andre felter er at tillade målepunkter være sammenlagt i SDK, at reducere trafik til portalen. Du kan for eksempel gennemsnitlig flere på hinanden følgende målinger før afsendelse af hver metriske rapport. Du vil derefter beregne min, Maks, standardafvigelse og samlede værdi (summen eller gennemsnittet) og angive antal til antallet målinger, der er repræsenteret af rapporten. 

I tabellerne ovenfor, har vi udeladt bruges sjældent felter Tæl, min, Maks, StAfv og sampledValue.

Du kan bruge [udvalg](app-insights-sampling.md) i stedet for udfyldt samle statistik, hvis du har brug at reducere mængde telemetri.


### <a name="durations"></a>Varighed

Medmindre andet er angivet, er varigheder repræsenteret i tiendedele mikrosekunder, så 10000000.0 betyder 1 sekund.



## <a name="see-also"></a>Se også

* [Programmet indsigt](app-insights-overview.md) 
* [Kontinuert Eksportér](app-insights-export-telemetry.md)
* [Kodeeksempler](app-insights-export-telemetry.md#code-samples)


