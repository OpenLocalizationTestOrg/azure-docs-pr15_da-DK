<properties
    pageTitle="Logik App tidsgrænser og konfiguration af | Microsoft Azure"
    description="Oversigt over de grænser og konfigurationsværdier, der er tilgængelige for logik Apps."
    services="logic-apps"
    documentationCenter=".net,nodejs,java"
    authors="jeffhollan"
    manager="dwrede"
    editor=""/>

<tags
    ms.service="logic-apps"
    ms.workload="integration"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/18/2016"
    ms.author="jehollan"/>

# <a name="logic-app-limits-and-configuration"></a>Logik App tidsgrænser og konfiguration af

Nedenfor finder du oplysninger om den aktuelle begrænsninger og konfigurationsoplysninger til Azure logik Apps.

## <a name="limits"></a>Begrænsninger

### <a name="http-request-limits"></a>HTTP-anmodning om begrænsninger

Dette er begrænsninger for et enkelt HTTP-anmodningen og/eller forbindelse opkald

#### <a name="timeout"></a>Timeout

|Navn|Grænse|Noter|
|----|----|----|
|Anmodning om Timeout|1 minut|En [asynkron mønster](app-service-logic-create-api-app.md) eller et [indtil løkke](app-service-logic-loops-and-scopes.md) kan kompensation efter behov|

#### <a name="message-size"></a>Størrelsen på e-mails

|Navn|Grænse|Noter|
|----|----|----|
|Størrelsen på e-mails|50 MB|Nogle forbindelser og API'er understøtter muligvis ikke 50MB.  Anmodning om udløser understøtter op til 25MB|
|Udtryk evaluering grænse|131,072 tegn|`@concat()`, `@base64()`, `string` må ikke være længere end dette|

#### <a name="retry-policy"></a>Prøv igen politik

|Navn|Grænse|Noter|
|----|----|----|
|Gentagne forsøg|4|Kan konfigureres med den [Prøv igen politikparameter](https://msdn.microsoft.com/en-us/library/azure/mt643939.aspx)|
|Prøv igen Maksimal forsinkelse|1 time|Kan konfigureres med den [Prøv igen politikparameter](https://msdn.microsoft.com/en-us/library/azure/mt643939.aspx)|
|Prøv igen min forsinkelse|20 min.|Kan konfigureres med den [Prøv igen politikparameter](https://msdn.microsoft.com/en-us/library/azure/mt643939.aspx)|

### <a name="run-duration-and-retention"></a>Kør varighed og opbevaringspolitikker

Dette er begrænsningerne for en enkelt logik app køre.

|Navn|Grænse|Noter|
|----|----|----|
|Køre varighed|90 dage||
|Lagerplads opbevaring|90 dage|Dette er fra køre starttidspunktet|
|Min gentagelsesinterval|15 sekund||
|Maks gentagelsesinterval|500 dage||


### <a name="looping-and-debatching-limits"></a>Løkker og debatching begrænsninger

Dette er begrænsninger for en enkelt logik app køre.

|Navn|Grænse|Noter|
|----|----|----|
|ForEach elementer|5.000|Du kan bruge [forespørgsel handling](../connectors/connectors-native-query.md) til at filtrere større matrixer efter behov|
|Indtil gentagelser|10.000||
|SplitOn elementer|5.000||
|ForEach parallelitet|20|Du kan angive til en fortløbende foreach ved at tilføje `"operationOptions": "Sequential"` til den `foreach` handling|


### <a name="throughput-limits"></a>Begrænsninger for overførselshastighed

Dette er begrænsninger for en enkelt logik app forekomst. 

|Navn|Grænse|Noter|
|----|----|----|
|Udløsere sekundet|100|Arbejdsprocesser kan distribuere på tværs af flere apps, efter behov|

### <a name="definition-limits"></a>Definition begrænsninger

Dette er begrænsninger for en enkelt logik app definition.

|Navn|Grænse|Noter|
|----|----|----|
|Handlinger i ForEach|1|Du kan føje indlejrede arbejdsprocesser for at udvide dette efter behov|
|Handlinger i arbejdsprocessen|60|Du kan føje indlejrede arbejdsprocesser for at udvide dette efter behov|
|Tilladt handling indlejre dybde|5|Du kan føje indlejrede arbejdsprocesser for at udvide dette efter behov|
|Flyder per område per abonnement|1000||
|Udløsere i samme arbejdsproces|10||
|Maksimalt antal tegn i udtryk|8.192||
|Maks `trackedProperties` størrelse i tegn|16.000|
|`action`/`trigger`navn grænse|80||
|`description`længdebegrænsningen|256||
|`parameters`grænse|50||
|`outputs`grænse|10||

## <a name="configuration"></a>Konfiguration

### <a name="ip-address"></a>IP-adresse

Opkald foretages fra en [forbindelse](../connectors/apis-list.md) kommer fra den IP-adresse, der er angivet nedenfor.

Opkald foretages fra en logik app direkte (det vil sige via [HTTP](../connectors/connectors-native-http.md) eller [HTTP + Swagger](../connectors/connectors-native-http-swagger.md)) kan komme fra en af de [Azure Datacenter IP-intervaller](https://www.microsoft.com/en-us/download/details.aspx?id=41653).

|Logik App område|Udgående IP-adresse|
|-----|----|
|Australien øst|40.126.251.213|
|Australien Sydøst|40.127.80.34|
|Brasilien syd|191.232.38.129|
|Central Indien|104.211.98.164|
|Centrale USA|40.122.49.51|
|Sydøstasien|23.99.116.181|
|Indtastning af østasiatiske USA|191.237.41.52|
|Indtastning af østasiatiske USA 2|104.208.233.100|
|Japan øst|40.115.186.96|
|Japan vest|40.74.130.77|
|Nord centrale USA|65.52.218.230|
|Nord Europe|104.45.93.9|
|Syd centrale USA|104.214.70.191|
|Sydøstasien|13.76.231.68|
|Syd Indien|104.211.227.225|
|Vest Europe|40.115.50.13|
|Vest Indien|104.211.161.203|
|Vest USA|104.40.51.248|


## <a name="next-steps"></a>Næste trin  

- For at komme i gang med logik Apps skal du følge selvstudiet [oprette en logik App](app-service-logic-create-a-logic-app.md) .  
- [Eksempler på almindelige og -scenarier](app-service-logic-examples-and-scenarios.md)
- [Du kan automatisere forretningsprocesser med logik Apps](http://channel9.msdn.com/Events/Build/2016/T694) 
- [Lær at integrere dine systemer med logik Apps](http://channel9.msdn.com/Events/Build/2016/P462)