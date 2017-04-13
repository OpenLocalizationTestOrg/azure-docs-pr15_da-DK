<properties
pageTitle="Lær at bruge Azure Service Bus forbindelsen i dine logik apps | Microsoft Azure"
description="Opret logik apps med Azure App service. Oprette forbindelse til Azure Service Bus til at sende og modtage meddelelser. Du kan udføre handlinger som send til kø, sende til emne, modtager fra køen og modtage fra abonnement."
services="logic-apps"
documentationCenter=".net,nodejs,java"  
authors="msftman"
manager="erikre"
editor=""
tags="connectors" />

<tags
ms.service="logic-apps"
ms.devlang="multiple"
ms.topic="article"
ms.tgt_pltfrm="na"
ms.workload="integration"
ms.date="08/02/2016"
ms.author="deonhe"/>

# <a name="get-started-with-the-azure-service-bus-connector"></a>Introduktion til Azure Service Bus forbindelsen

Oprette forbindelse til Azure Service Bus til at sende og modtage meddelelser. Du kan udføre handlinger som send til kø, sende til emne, modtager fra køen og modtage fra abonnement.

Hvis du vil bruge [en forbindelse](./apis-list.md), skal du først oprette en logik app. Du kan komme i gang ved at [oprette en logik app nu](../app-service-logic/app-service-logic-create-a-logic-app.md).

## <a name="connect-to-service-bus"></a>Oprette forbindelse til tjenesten Bus

Før din logik app kan få adgang til alle tjenester, skal du først oprette en forbindelse til tjenesten. En [forbindelse](./connectors-overview.md) indeholder forbindelsen mellem en logik app og en anden tjeneste.  

>[AZURE.INCLUDE [Steps to create a connection to Azure Service Bus](../../includes/connectors-create-api-servicebus.md)]

## <a name="use-a-service-bus-trigger"></a>Bruge en Service Bus udløser

En udløser er en begivenhed, der kan bruges til at starte arbejdsprocessen defineret i en logik app. [Få mere at vide om udløsere](../app-service-logic/app-service-logic-what-are-logic-apps.md#logic-app-concepts).  

>[AZURE.INCLUDE [Steps to create a Service Bus trigger](../../includes/connectors-create-api-servicebus-trigger.md)]  

## <a name="use-a-service-bus-action"></a>Bruge en Service Bus handling

En handling er en handling, der er foretaget af den arbejdsproces, der er defineret i en logik app. [Lær mere om handlinger](../app-service-logic/app-service-logic-what-are-logic-apps.md#logic-app-concepts).

[AZURE.INCLUDE [Steps to create a Service Bus action](../../includes/connectors-create-api-servicebus-action.md)]  

## <a name="technical-details"></a>Tekniske detaljer

Her er nogle oplysninger om udløsere, handlinger og svar, der understøtter denne forbindelse.

### <a name="service-bus-triggers"></a>Service Bus udløsere

Service Bus har følgende udløsere:  

|Udløser | Beskrivelse|
|--- | ---|
|[Når der modtages en meddelelse i en kø](connectors-create-api-servicebus.md#when-a-message-is-received-in-a-queue)|Denne handling aktiverer et flow, når der modtages en meddelelse i en kø.|
|[Når der modtages en meddelelse i et emne-abonnement](connectors-create-api-servicebus.md#when-a-message-is-received-in-a-topic-subscription)|Denne handling aktiverer et flow, når der modtages en meddelelse i et emne abonnement.|


### <a name="service-bus-actions"></a>Service Bus handlinger

Service Bus har følgende handlinger:


|Handling|Beskrivelse|
|--- | ---|
|[Sende meddelelse](connectors-create-api-servicebus.md#send-message)|Denne handling sender en meddelelse til en kø eller emne.|
### <a name="action-and-trigger-details"></a>Oplysninger om handling og udløser

Her er detaljerne for handlingerne og udløsere for denne forbindelse, sammen med svarene.



#### <a name="send-message"></a>Sende meddelelse

|Egenskabsnavn| Vist navn|Beskrivelse|
| ---|---|---|
|ContentData *|Indhold|Indholdet af meddelelsen.|
|ContentType|Indholdstype|Indholdstype for meddelelsesindhold.|
|Egenskaber|Egenskaber|Nøgle-værdi-par for hver formidlet egenskab.|
|entityName *|Kø/emnenavn|Navnet på den kø eller emne.|

Disse avancerede parametre er også tilgængelige:

|Egenskabsnavn| Vist navn|Beskrivelse|
| ---|---|---|
|MessageId|Meddelelses-Id|En brugerdefineret værdi, der Service Bus kan bruge til at identificere dublerede meddelelser, hvis det er aktiveret.|
|Til|Til|Adresse for at sende til.|
|ReplyTo|Svare på|Adressen på køen svare på.|
|ReplyToSessionId|Svar til Session-Id|Id for sessioner lov til at svare på.|
|Etiket|Etiket|Program-specifikke etiket.|
|ScheduledEnqueueTimeUtc|ScheduledEnqueueTimeUtc|Dato og klokkeslæt i UTC, når meddelelsen, føjes til køen.|
|Session-id|Session-Id|Id for sessionen.|
|CorrelationId|Korrelations-Id|Id for korrelationen.|
|TimeToLive|Tid til Live|Varighed i aksemærker, at en meddelelse er gyldig. Varigheden starter fra når meddelelsen er sendt til Service Bus.|



En * angiver, at en egenskab er påkrævet.


#### <a name="when-a-message-is-received-in-a-queue"></a>Når der modtages en meddelelse i en kø

|Egenskabsnavn| Vist navn|Beskrivelse|
| ---|---|---|
|queueName *|Kø navn|Navnet på kø.|


En * angiver, at en egenskab er påkrævet.


##### <a name="output-details"></a>Output detaljer

ServiceBusMessage: Dette objekt har indhold og egenskaber for en Service Bus meddelelse.


| Egenskabsnavn | Datatype | Beskrivelse |
|---|---|---|
|ContentData|streng|Indholdet af meddelelsen.|
|ContentType|streng|Indholdstype for meddelelsesindhold.|
|Egenskaber|objekt|Nøgle-værdi-par for hver formidlet egenskab.|
|MessageId|streng|En brugerdefineret værdi, der Service Bus kan bruge til at identificere dublerede meddelelser, hvis det er aktiveret.|
|Til|streng|Sende til adresse.|
|ReplyTo|streng|Adressen på køen svare på.|
|ReplyToSessionId|streng|Id for sessioner lov til at svare på.|
|Etiket|streng|Program-specifikke etiket.|
|ScheduledEnqueueTimeUtc|streng|Dato og klokkeslæt i UTC, når meddelelsen, føjes til køen.|
|Session-id|streng|Id for sessionen.|
|CorrelationId|streng|Id for korrelationen.|
|TimeToLive|streng|Varighed i aksemærker, at en meddelelse er gyldig. Varigheden starter fra når meddelelsen er sendt til Service Bus.|




#### <a name="when-a-message-is-received-in-a-topic-subscription"></a>Når der modtages en meddelelse i et emne-abonnement

|Egenskabsnavn| Vist navn|Beskrivelse|
| ---|---|---|
|topicName *|Emnenavn|Navnet på emnet.|
|subscriptionName *|Emne Abonnementsnavnet|Navnet på det emne abonnement.|


En * angiver, at en egenskab er påkrævet.


##### <a name="output-details"></a>Output detaljer

ServiceBusMessage: Dette objekt har indhold og egenskaber for en Service Bus meddelelse.


| Egenskabsnavn | Datatype | Beskrivelse |
|---|---|---|
|ContentData|streng|Indholdet af meddelelsen.|
|ContentType|streng|Indholdstype for meddelelsesindhold.|
|Egenskaber|objekt|Nøgle-værdi-par for hver formidlet egenskab.|
|MessageId|streng|En brugerdefineret værdi, der Service Bus kan bruge til at identificere dublerede meddelelser, hvis det er aktiveret.|
|Til|streng|Sende til adresse.|
|ReplyTo|streng|Adressen på køen svare på.|
|ReplyToSessionId|streng|Id for sessioner lov til at svare på.|
|Etiket|streng|Program-specifikke etiket.|
|ScheduledEnqueueTimeUtc|streng|Dato og klokkeslæt i UTC, når meddelelsen, føjes til køen.|
|Session-id|streng|Id for sessionen.|
|CorrelationId|streng|Id for korrelationen.|
|TimeToLive|streng|Varighed i aksemærker, at en meddelelse er gyldig. Varigheden starter fra når meddelelsen er sendt til Service Bus.|



### <a name="http-responses"></a>HTTP-svar

De foregående handlinger og udløsere kan returnere en eller flere af følgende HTTP statuskoder:

|Navn|Beskrivelse|
|---|---|
|200|Ok|
|202|Accepteret|
|400|Forkert anmodning|
|401|Uautoriseret|
|403|Forbudt|
|404|Blev ikke fundet|
|500|Intern serverfejl. Der opstod ukendt fejl.|
|standard|Mislykkedes.|

## <a name="next-steps"></a>Næste trin
[Opret en logik app](../app-service-logic/app-service-logic-create-a-logic-app.md).
