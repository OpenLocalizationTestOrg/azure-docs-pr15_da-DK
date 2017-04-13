<properties
pageTitle="RSS | Microsoft Azure"
description="Opret logik apps med Azure App service. RSS-forbindelse gør det muligt for brugerne at publicere og hente feed elementer. Gør det også brugere til at udløse handlinger, når et nyt element er blevet publiceret til feedet."
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
ms.date="08/18/2016"
ms.author="deonhe"/>

# <a name="get-started-with-the-rss-connector"></a>Introduktion til RSS-forbindelse
RSS er et populære web RSS-format, der bruges til at publicere ofte opdateret indhold – som blogindlæg og nyheder.  Mange indhold udgivere giver et RSS-feed for at tillade brugere at abonnere på den.  Bruge RSS-forbindelsen til at hente feed oplysninger og udløser flyder, når nye elementer er udgivet i et RSS-feed.

>[AZURE.NOTE] Denne version i denne artikel gælder for logik apps 2015-08-01-skema prøveversionen. 

Du kan komme i gang ved at oprette en logik app nu, i [oprette en logik app](../app-service-logic/app-service-logic-create-a-logic-app.md).

## <a name="triggers-and-actions"></a>Udløsere og handlinger

RSS-forbindelsen kan bruges som en handling den har trigger(s). Alle forbindelser understøtter data i JSON og XML-formater. 

 RSS-forbindelsen har følgende handling(er) og/eller trigger(s) tilgængelige:

### <a name="rss-actions"></a>RSS-handlinger
Du kan udføre disse handling(er):

|Handling|Beskrivelse|
|--- | ---|
|[ListFeedItems](connectors-create-api-rss.md#listfeeditems)|Få alle RSS-feed elementer.|
### <a name="rss-triggers"></a>RSS-udløsere
Du kan lytte til disse hændelser:

|Udløser | Beskrivelse|
|--- | ---|
|Når en ny feed element publiceret|Udløser en arbejdsproces, når en ny feed udgives|


## <a name="create-a-connection-to-rss"></a>Oprette en forbindelse til RSS

>[AZURE.INCLUDE [Steps to create a connection to an RSS feed](../../includes/connectors-create-api-rss.md)]

>[AZURE.TIP] Du kan bruge denne forbindelse i andre logik apps.

## <a name="reference-for-rss"></a>Reference til RSS
Gælder for version: 1.0

## <a name="onnewfeed"></a>OnNewFeed
Når en ny feed element publiceret: udløser en arbejdsproces, når en ny feed udgives 

```GET: /OnNewFeed``` 

| Navn| Datatype|Påkrævet|Placeret i|Standardværdi|Beskrivelse|
| ---|---|---|---|---|---|
|feedUrl|streng|Ja|forespørgsel|ingen|Feed URL-adresse|

#### <a name="response"></a>Svar

|Navn|Beskrivelse|
|---|---|
|200|Ok|
|202|Accepteret|
|400|Forkert anmodning|
|401|Uautoriseret|
|403|Forbudt|
|404|Blev ikke fundet|
|500|Intern serverfejl. Der opstod en ukendt fejl|
|standard|Mislykkedes.|


## <a name="listfeeditems"></a>ListFeedItems
Liste over alle RSS-feed elementer.: få alle RSS-feed elementer. 

```GET: /ListFeedItems``` 

| Navn| Datatype|Påkrævet|Placeret i|Standardværdi|Beskrivelse|
| ---|---|---|---|---|---|
|feedUrl|streng|Ja|forespørgsel|ingen|Feed URL-adresse|

#### <a name="response"></a>Svar

|Navn|Beskrivelse|
|---|---|
|200|Ok|
|202|Accepteret|
|400|Forkert anmodning|
|401|Uautoriseret|
|403|Forbudt|
|404|Blev ikke fundet|
|500|Intern serverfejl. Der opstod en ukendt fejl|
|standard|Mislykkedes.|


## <a name="object-definitions"></a>Objektdefinitioner 

### <a name="triggerbatchresponsefeeditem"></a>TriggerBatchResponse [FeedItem]


| Egenskabsnavn | Datatype | Påkrævet |
|---|---|---|
|værdi|matrix|Nej |



### <a name="feeditem"></a>FeedItem


| Egenskabsnavn | Datatype | Påkrævet |
|---|---|---|
|id|streng|Ja |
|titel|streng|Ja |
|indhold|streng|Ja |
|links|matrix|Nej |
|updatedOn|streng|Nej |


## <a name="next-steps"></a>Næste trin
[Oprette en logik app](../app-service-logic/app-service-logic-create-a-logic-app.md)