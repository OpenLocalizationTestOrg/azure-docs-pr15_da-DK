<properties
pageTitle="SendGrid | Microsoft Azure"
description="Opret logik apps med Azure App service. SendGrid forbindelsesprovider kan du sende mail og administrere modtagerlister."
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

# <a name="get-started-with-the-sendgrid-connector"></a>Komme i gang med SendGrid forbindelsen

SendGrid forbindelsesprovider kan du sende mail og administrere modtagerlister.

>[AZURE.NOTE] Denne version i denne artikel gælder for logik apps 2015-08-01-skema prøveversionen. 

Du kan komme i gang ved at oprette en logik app nu, i [oprette en logik app](../app-service-logic/app-service-logic-create-a-logic-app.md).

## <a name="triggers-and-actions"></a>Udløsere og handlinger

SendGrid forbindelsen kan bruges som en handling den har trigger(s). Alle forbindelser understøtter data i JSON og XML-formater. 

 SendGrid forbindelsen har de følgende handlinger, der er tilgængelige. Der findes ingen udløsere.

### <a name="sendgrid-actions"></a>SendGrid handlinger
Du kan udføre disse handling(er):

|Handling|Beskrivelse|
|--- | ---|
|[SendEmail](connectors-create-api-sendgrid.md#sendemail)|Sender en mail via SendGrid API (begrænset til 10.000 modtagere)|
|[AddRecipientToList](connectors-create-api-sendgrid.md#addrecipienttolist)|Føje en individuel modtager til en liste over modtagere|


## <a name="create-a-connection-to-sendgrid"></a>Oprette en forbindelse til SendGrid
Hvis du vil oprette logik apps med SendGrid, skal du først oprette en **forbindelse** og derefter angive detaljerne for følgende egenskaber: 

|Egenskaben| Påkrævet|Beskrivelse|
| ---|---|---|
|ApiKey|Ja|Angiv din SendGrid Api-nøgle|
 

>[AZURE.INCLUDE [Steps to create a connection to SendGrid](../../includes/connectors-create-api-sendgrid.md)]

>[AZURE.TIP] Du kan bruge denne forbindelse i andre logik apps.

Når du opretter forbindelsen, kan du bruge det til at udføre handlingerne, og lyt til de udløsere, der er beskrevet i denne artikel.

## <a name="reference-for-sendgrid"></a>Reference til SendGrid
Gælder for version: 1.0

## <a name="sendemail"></a>SendEmail
Sende mail: sender en mail via SendGrid API (begrænset til 10.000 modtagere) 

```POST: /api/mail.send.json``` 

| Navn| Datatype|Påkrævet|Placeret i|Standardværdi|Beskrivelse|
| ---|---|---|---|---|---|
|anmodning om| |Ja|brødteksten|ingen|Mail til at sende|

#### <a name="response"></a>Svar

|Navn|Beskrivelse|
|---|---|
|200|Ok|
|400|Forkert anmodning|
|401|Uautoriseret|
|403|Forbudt|
|404|Blev ikke fundet|
|429|For mange anmodning|
|500|Intern serverfejl. Der opstod en ukendt fejl|
|standard|Mislykkedes.|


## <a name="addrecipienttolist"></a>AddRecipientToList
Føje modtager til liste: føje en individuel modtager til en liste over modtagere 

```POST: /v3/contactdb/lists/{listId}/recipients/{recipientId}``` 

| Navn| Datatype|Påkrævet|Placeret i|Standardværdi|Beskrivelse|
| ---|---|---|---|---|---|
|listId|streng|Ja|sti|ingen|Entydigt id for modtagerlisten|
|recipientId|streng|Ja|sti|ingen|Entydigt id for modtageren|

#### <a name="response"></a>Svar

|Navn|Beskrivelse|
|---|---|
|200|Ok|
|400|Forkert anmodning|
|401|Uautoriseret|
|403|Forbudt|
|404|Blev ikke fundet|
|500|Intern serverfejl. Der opstod en ukendt fejl|
|standard|Mislykkedes.|


## <a name="object-definitions"></a>Objektdefinitioner 

### <a name="emailrequest"></a>EmailRequest


| Egenskabsnavn | Datatype | Påkrævet |
|---|---|---|
|fra|streng|Ja |
|fromname|streng|Nej |
|til|streng|Ja |
|toname|streng|Nej |
|emne|streng|Ja |
|brødteksten|streng|Ja |
|ishtml|Boolesk værdi|Nej |
|Cc|streng|Nej |
|ccname|streng|Nej |
|Bcc|streng|Nej |
|bccname|streng|Nej |
|ReplyTo|streng|Nej |
|dato|streng|Nej |
|sidehoveder|streng|Nej |
|filer|matrix|Nej |
|filnavne|matrix|Nej |



### <a name="emailresponse"></a>EmailResponse


| Egenskabsnavn | Datatype | Påkrævet |
|---|---|---|
|meddelelse|streng|Nej |



### <a name="recipientlists"></a>RecipientLists


| Egenskabsnavn | Datatype | Påkrævet |
|---|---|---|
|lister|matrix|Nej |



### <a name="recipientlist"></a>RecipientList


| Egenskabsnavn | Datatype | Påkrævet |
|---|---|---|
|id|heltal|Nej |
|Navn|streng|Nej |
|recipient_count|heltal|Nej |



### <a name="recipients"></a>Modtagere


| Egenskabsnavn | Datatype | Påkrævet |
|---|---|---|
|modtagere|matrix|Nej |



### <a name="recipient"></a>Modtager


| Egenskabsnavn | Datatype | Påkrævet |
|---|---|---|
|mail|streng|Nej |
|Efternavn|streng|Nej |
|Fornavn|streng|Nej |
|id|streng|Nej |


## <a name="next-steps"></a>Næste trin
[Oprette en logik app](../app-service-logic/app-service-logic-create-a-logic-app.md)