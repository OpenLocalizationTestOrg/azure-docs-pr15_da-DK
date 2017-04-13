<properties
pageTitle="Tilføje Yammer forbindelsen i dine logik Apps | Microsoft Azure"
description="Oversigt over forbindelsen Yammer med REST-API parametre"
services=""    
documentationCenter=""     
authors="msftman"    
manager="erikre"    
editor=""
tags="connectors"/>

<tags
ms.service="multiple"
ms.devlang="na"
ms.topic="article"
ms.tgt_pltfrm="na"
ms.workload="na"
ms.date="05/18/2016"
ms.author="deonhe"/>

# <a name="get-started-with-the-yammer-connector"></a>Komme i gang med Yammer-forbindelse

Oprette forbindelse til Yammer til access samtaler i virksomhedens netværk.

>[AZURE.NOTE] Denne version i denne artikel gælder for logik apps 2015-08-01-skema prøveversionen.

Med Yammer kan du:

- Opbyg din virksomhed flow baseret på de data, du får fra Yammer. 
- Brug udløser til, når der er en ny meddelelse i en gruppe eller et feed følger.
- Bruge handlinger til at sende en meddelelse, få alle meddelelser og meget mere. Disse handlinger får du respons, og foretag derefter output tilgængelig for andre handlinger. Når en ny meddelelse vises, kan du sende en mail via Office 365.

For at tilføje en handling i logik apps skal du se [oprette en logik app](../app-service-logic/app-service-logic-create-a-logic-app.md).

## <a name="triggers-and-actions"></a>Udløsere og handlinger
Yammer indeholder følgende udløsere og handlinger. 

Udløser | Handlinger
--- | ---
<ul><li>Når der er en ny meddelelse i en gruppe</li><li>Når der er feed en ny meddelelse i min følgende</li></ul>| <ul><li>Få alle meddelelser</li><li>Får meddelelser i en gruppe</li><li>Henter meddelelser fra min følgende feed</li><li>Send meddelelse</li><li>Når der er en ny meddelelse i en gruppe</li><li>Når der er feed en ny meddelelse i min følgende</li></ul>

Alle forbindelser understøtter data i JSON og XML-formater. 

## <a name="create-a-connection-to-yammer"></a>Oprette en forbindelse til Yammer
Hvis du vil bruge Yammer-forbindelsen, skal du først oprette en **forbindelse** og derefter Angiv oplysninger om disse egenskaber: 

|Egenskaben| Påkrævet|Beskrivelse|
| ---|---|---|
|Token|Ja|Angive Yammer-legitimationsoplysninger|

>[AZURE.INCLUDE [Steps to create a connection to Yammer](../../includes/connectors-create-api-yammer.md)]


>[AZURE.TIP] Du kan bruge denne forbindelse i andre logik apps.

## <a name="yammer-rest-api-reference"></a>Yammer REST-API reference
Denne dokumentation er for version: 1.0


### <a name="get-all-public-messages-in-the-logged-in-users-yammer-network"></a>Få alle offentlige meddelelser i bruger logget på Yammer-netværk
Svarer til "Alle" samtaler i brugergrænsefladen på Yammer-internettet.  
```GET: /messages.json```

| Navn| Datatype|Påkrævet|Placeret i|Standardværdi|Beskrivelse|
| ---|---|---|---|---|---|
|older_then|heltal|Nej|forespørgsel|ingen|Returnerer meddelelser, der er ældre end meddelelses-ID, der er angivet som en numerisk streng. Dette er nyttigt til med at sideinddele meddelelser. Hvis du får vist 20 meddelelser og den ældste er tal eksempelvis 2912, kan du føje "? older_than = 2912″ til din anmodning om at få de 20 meddelelser før dem, du får vist.|
|newer_then|heltal|Nej|forespørgsel|ingen|Returnerer meddelelser, der er nyere end meddelelses-ID, der er angivet som en numerisk streng. Det skal bruges, når der forespørges efter nye meddelelser. Hvis du kigger på meddelelser, og den seneste meddelelse returneres, er 3516, kan du gøre en anmodning med parameteren "? newer_than = 3516″ for at sikre, at du ikke får flere kopier af meddelelser allerede på siden.|
|grænse|heltal|Nej|forespørgsel|ingen|Returnere det angivne antal meddelelser.|
|siden|heltal|Nej|forespørgsel|ingen|Få den side, der er angivet. Hvis returneres data er større end grænsen, kan du bruge dette felt til at få adgang til efterfølgende sider|


### <a name="response"></a>Svar

|Navn|Beskrivelse|
|---|---|
|200|Ok|
|400|Forkert anmodning|
|408|Anmodning om Timeout|
|429|For mange anmodninger|
|500|Intern serverfejl. Der opstod en ukendt fejl|
|503|Yammer-tjenesten er ikke tilgængelig|
|504|Timeout for gateway|
|standard|Mislykkedes.|


### <a name="post-a-message-to-a-group-or-all-company-feed"></a>Sende en meddelelse til en gruppe eller All Company Feed
Hvis gruppe-ID'ET er angivet, sendes meddelelsen til den angivne gruppe, ellers er det vil være skrevet i alle virksomhedens Feed.    
```POST: /messages.json``` 

| Navn| Datatype|Påkrævet|Placeret i|Standardværdi|Beskrivelse|
| ---|---|---|---|---|---|
|input| |Ja|brødteksten|ingen|Anmodningsmeddelelsen for indlæg|


### <a name="response"></a>Svar

|Navn|Beskrivelse|
|---|---|
|201|Oprettet|



## <a name="object-definitions"></a>Objektdefinitioner

#### <a name="message-yammer-message"></a>Meddelelse: Yammer-meddelelse

| Navn | Datatype | Påkrævet |
|---|---| --- | 
|id|heltal|Nej|
|content_excerpt|streng|Nej|
|sender_id|heltal|Nej|
|replied_to_id|heltal|Nej|
|created_at|streng|Nej|
|network_id|heltal|Nej|
|message_type|streng|Nej|
|sender_type|streng|Nej|
|URL-adresse|streng|Nej|
|web_url|streng|Nej|
|group_id|heltal|Nej|
|brødteksten|ikke defineret|Nej|
|thread_id|heltal|Nej|
|direct_message|Boolesk værdi|Nej|
|client_type|streng|Nej|
|client_url|streng|Nej|
|sprog|streng|Nej|
|notified_user_ids|matrix|Nej|
|beskyttelse af personlige oplysninger|streng|Nej|
|liked_by|ikke defineret|Nej|
|system_message|Boolesk værdi|Nej|

#### <a name="postoperationrequest-represents-a-post-request-for-yammer-connector-to-post-to-yammer"></a>PostOperationRequest: Repræsenterer en post-anmodning for Yammer-forbindelse at sende til yammer

| Navn | Datatype | Påkrævet |
|---|---| --- | 
|brødteksten|streng|Ja|
|group_id|heltal|Nej|
|replied_to_id|heltal|Nej|
|direct_to_id|heltal|Nej|
|Udsend|Boolesk værdi|Nej|
|emne1|streng|Nej|
|emne2|streng|Nej|
|topic3|streng|Nej|
|topic4|streng|Nej|
|topic5|streng|Nej|
|topic6|streng|Nej|
|topic7|streng|Nej|
|topic8|streng|Nej|
|topic9|streng|Nej|
|topic10|streng|Nej|
|topic11|streng|Nej|
|topic12|streng|Nej|
|topic13|streng|Nej|
|topic14|streng|Nej|
|topic15|streng|Nej|
|topic16|streng|Nej|
|topic17|streng|Nej|
|topic18|streng|Nej|
|topic19|streng|Nej|
|topic20|streng|Nej|

#### <a name="messagelist-list-of-messages"></a>MessageList: Listen over meddelelser

| Navn | Datatype | Påkrævet |
|---|---| --- | 
|meddelelser|matrix|Nej|


#### <a name="messagebody-message-body"></a>MessageBody: Meddelelsens brødtekst

| Navn | Datatype | Påkrævet |
|---|---| --- | 
|parses|streng|Nej|
|almindelig|streng|Nej|
|RTF|streng|Nej|

#### <a name="likedby-liked-by"></a>LikedBy: Synes godt om ved

| Navn | Datatype | Påkrævet |
|---|---| --- | 
|Tæl|heltal|Nej|
|navne|matrix|Nej|

#### <a name="yammmerentity-liked-by"></a>YammmerEntity: Synes godt om ved

| Navn | Datatype | Påkrævet |
|---|---| --- | 
|type|streng|Nej|
|id|heltal|Nej|
|full_name|streng|Nej|


## <a name="next-steps"></a>Næste trin
[Opret en logik app](../app-service-logic/app-service-logic-create-a-logic-app.md).

[1]: ./media/connectors-create-api-yammer/connectionconfig1.png
[2]: ./media/connectors-create-api-yammer/connectionconfig2.png 
[3]: ./media/connectors-create-api-yammer/connectionconfig3.png
[4]: ./media/connectors-create-api-yammer/connectionconfig4.png
[5]: ./media/connectors-create-api-yammer/connectionconfig5.png
