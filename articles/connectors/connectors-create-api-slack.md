<properties
pageTitle=" Bruge slæk forbindelsen i dine logik apps | Microsoft Azure"
description="Introduktion til brug af slæk forbindelsen i dine Microsoft Azure App Service logik apps"
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

# <a name="get-started-with-the-slack-connector"></a>Komme i gang med slæk forbindelsen

Slæk er et team kommunikationsværktøj, som samler alle dit team kommunikation i en placerer, med det samme søgbare og tilgængelige overalt.

>[AZURE.NOTE] Denne version i denne artikel gælder for logik apps 2015-08-01-skema prøveversionen.

Med slæk forbindelsen kan du:

* Bruge det til at opbygge logik apps

For at tilføje en handling i logik apps skal du se [oprette en logik app](../app-service-logic/app-service-logic-create-a-logic-app.md).

## <a name="lets-talk-about-triggers-and-actions"></a>Lad os tale om udløsere og handlinger

Slæk forbindelsen kan bruges som en handling der findes ingen udløsere. Alle forbindelser understøtter data i JSON og XML-formater. 

 Slæk forbindelsen har følgende handling(er) og/eller trigger(s) tilgængelige:

### <a name="slack-actions"></a>Slæk handlinger
Du kan udføre disse handling(er):

|Handling|Beskrivelse|
|--- | ---|
|PostMessage|Sende en meddelelse til en bestemt kanal.|
## <a name="create-a-connection-to-slack"></a>Oprette en forbindelse til slæk
Hvis du vil bruge slæk forbindelsen, skal du først oprette en **forbindelse** og derefter registrere oplysninger om disse egenskaber: 

|Egenskaben| Påkrævet|Beskrivelse|
| ---|---|---|
|Token|Ja|Angive slæk legitimationsoplysninger|

Følg disse trin for at logge på slæk og fuldføre konfigurationen af slæk **forbindelse** i din app, logik:

1. Vælg **Gentagelse**
2. Vælg en **frekvens** , og Angiv et **Interval**
3. Vælg **Tilføj en handling**  
![Konfigurere slæk][1]  
4. Angiv slæk i søgefeltet, og vent til søgning til at returnere alle poster med slæk i navnet
5. Vælg **slæk - meddelelse**
6. Vælg **Log på slæk**:  
![Konfigurere slæk][2]
7. Angive dine slæk legitimationsoplysninger for at logge på Godkend programmet    
![Konfigurere slæk][3]  
8. Du bliver omdirigeret til din organisations Log på siden. **Godkend** Slæk i forhold til at interagere med din logik app:      
![Konfigurere slæk][5] 
9. Når tilladelsen er fuldført bliver du omdirigeret til din logik app til at fuldføre den ved at konfigurere sektionen **slæk - Hent alle meddelelser** . Tilføje andre udløsere og handlinger, du skal bruge.  
![Konfigurere slæk][6]
10. Gemme dit arbejde ved at vælge **Gem** på menulinjen ovenfor.


>[AZURE.TIP] Du kan bruge denne forbindelse i andre logik apps.

## <a name="slack-rest-api-reference"></a>Slæk REST-API-reference
#### <a name="this-documentation-is-for-version-10"></a>Denne dokumentation er for version: 1.0


### <a name="post-a-message-to-a-specified-channel"></a>Sende en meddelelse til en bestemt kanal.
**```POST: /chat.postMessage```** 



| Navn| Datatype|Påkrævet|Placeret i|Standardværdi|Beskrivelse|
| ---|---|---|---|---|---|
|kanal|streng|Ja|forespørgsel|ingen|Kanal, privat gruppe eller Chat kanal til at sende meddelelsen til. Kan være et navn (ex: #general) eller en kodet-ID.|
|tekst|streng|Ja|forespørgsel|ingen|Tekst i meddelelsen for at sende. Formateringsindstillinger, se https://api.slack.com/docs/formatting.|
|brugernavn|streng|Nej|forespørgsel|ingen|Navnet på bot|
|as_user|Boolesk værdi|Nej|forespørgsel|ingen|Overføre sand for at sende meddelelsen som den godkendte bruger i stedet for som en bot|
|Analysér|streng|Nej|forespørgsel|ingen|Ændre, hvordan meddelelser behandles. Yderligere oplysninger finder du https://api.slack.com/docs/formatting.|
|link_names|heltal|Nej|forespørgsel|ingen|Finde og tilknytte kanal navne og brugernavne.|
|unfurl_links|Boolesk værdi|Nej|forespørgsel|ingen|Overfører True til Aktivér unfurling af primært tekstbaseret indhold.|
|unfurl_media|Boolesk værdi|Nej|forespørgsel|ingen|Overføre FALSK for at deaktivere unfurling af medieindhold.|
|icon_url|streng|Nej|forespørgsel|ingen|URL-adresse til et billede, der skal bruges som et ikon for denne meddelelse|
|icon_emoji|streng|Nej|forespørgsel|ingen|Emoji skal bruges som et ikon for denne meddelelse|


### <a name="here-are-the-possible-responses"></a>Her er de mulige svar:

|Navn|Beskrivelse|
|---|---|
|200|Ok|
|400|Forkert anmodning|
|408|Anmodning om Timeout|
|429|For mange anmodninger|
|500|Intern serverfejl. Der opstod en ukendt fejl|
|503|Slæk tjenesten er ikke tilgængelig|
|504|Timeout for gateway|
|standard|Mislykkedes.|
------



## <a name="object-definitions"></a>Objekt definition(s): 

 **Meddelelse**: Yammer-meddelelse

Nødvendige egenskaber for meddelelse:


Ingen af egenskaberne, der er påkrævet. 


**Alle egenskaber**: 


| Navn | Datatype |
|---|---|
|id|heltal|
|content_excerpt|streng|
|sender_id|heltal|
|replied_to_id|heltal|
|created_at|streng|
|network_id|heltal|
|message_type|streng|
|sender_type|streng|
|URL-adresse|streng|
|web_url|streng|
|group_id|heltal|
|brødteksten|ikke defineret|
|thread_id|heltal|
|direct_message|Boolesk værdi|
|client_type|streng|
|client_url|streng|
|sprog|streng|
|notified_user_ids|matrix|
|beskyttelse af personlige oplysninger|streng|
|liked_by|ikke defineret|
|system_message|Boolesk værdi|



 **PostOperationRequest**: repræsenterer en post-anmodning for Yammer-forbindelse at sende til yammer

Nødvendige egenskaber for PostOperationRequest:

brødteksten

**Alle egenskaber**: 


| Navn | Datatype |
|---|---|
|brødteksten|streng|
|group_id|heltal|
|replied_to_id|heltal|
|direct_to_id|heltal|
|Udsend|Boolesk værdi|
|emne1|streng|
|emne2|streng|
|topic3|streng|
|topic4|streng|
|topic5|streng|
|topic6|streng|
|topic7|streng|
|topic8|streng|
|topic9|streng|
|topic10|streng|
|topic11|streng|
|topic12|streng|
|topic13|streng|
|topic14|streng|
|topic15|streng|
|topic16|streng|
|topic17|streng|
|topic18|streng|
|topic19|streng|
|topic20|streng|



 **MessageList**: listen over meddelelser

Nødvendige egenskaber for MessageList:


Ingen af egenskaberne, der er påkrævet. 


**Alle egenskaber**: 


| Navn | Datatype |
|---|---|
|meddelelser|matrix|



 **MessageBody**: meddelelsestekst

Nødvendige egenskaber for MessageBody:


Ingen af egenskaberne, der er påkrævet. 


**Alle egenskaber**: 


| Navn | Datatype |
|---|---|
|parses|streng|
|almindelig|streng|
|RTF|streng|



 **LikedBy**: synes godt om ved

Nødvendige egenskaber for LikedBy:


Ingen af egenskaberne, der er påkrævet. 


**Alle egenskaber**: 


| Navn | Datatype |
|---|---|
|Tæl|heltal|
|navne|matrix|



 **YammmerEntity**: synes godt om ved

Nødvendige egenskaber for YammmerEntity:


Ingen af egenskaberne, der er påkrævet. 


**Alle egenskaber**: 


| Navn | Datatype |
|---|---|
|type|streng|
|id|heltal|
|full_name|streng|


## <a name="next-steps"></a>Næste trin
[Oprette en logik app](../app-service-logic/app-service-logic-create-a-logic-app.md)
## <a name="object-definitions"></a>Objekt definition(s): 

 **WebResultModel**: Bing web søgeresultater

Nødvendige egenskaber for WebResultModel:


Ingen af egenskaberne, der er påkrævet. 


**Alle egenskaber**: 


| Navn | Datatype |
|---|---|
|Titel|streng|
|Beskrivelse|streng|
|DisplayUrl|streng|
|Id|streng|
|FullUrl|streng|



 **VideoResultModel**: Bing video søgeresultater

Nødvendige egenskaber for VideoResultModel:


Ingen af egenskaberne, der er påkrævet. 


**Alle egenskaber**: 


| Navn | Datatype |
|---|---|
|Titel|streng|
|DisplayUrl|streng|
|Id|streng|
|MediaUrl|streng|
|Runtime|heltal|
|Miniature|ikke defineret|



 **ThumbnailModel**: miniature egenskaberne for elementet multimedier

Nødvendige egenskaber for ThumbnailModel:


Ingen af egenskaberne, der er påkrævet. 


**Alle egenskaber**: 


| Navn | Datatype |
|---|---|
|MediaUrl|streng|
|ContentType|streng|
|Bredde|heltal|
|Højde|heltal|
|FileSize|heltal|



 **ImageResultModel**: Bing billede af søgeresultater

Nødvendige egenskaber for ImageResultModel:


Ingen af egenskaberne, der er påkrævet. 


**Alle egenskaber**: 


| Navn | Datatype |
|---|---|
|Titel|streng|
|DisplayUrl|streng|
|Id|streng|
|MediaUrl|streng|
|SourceUrl|streng|
|Miniature|ikke defineret|



 **NewsResultModel**: Bing nyheder søgeresultater

Nødvendige egenskaber for NewsResultModel:


Ingen af egenskaberne, der er påkrævet. 


**Alle egenskaber**: 


| Navn | Datatype |
|---|---|
|Titel|streng|
|Beskrivelse|streng|
|DisplayUrl|streng|
|Id|streng|
|Kilde|streng|
|Dato|streng|



 **SpellResultModel**: Bing stavekontrol forslag resultater

Nødvendige egenskaber for SpellResultModel:


Ingen af egenskaberne, der er påkrævet. 


**Alle egenskaber**: 


| Navn | Datatype |
|---|---|
|Id|streng|
|Værdi|streng|



 **RelatedSearchResultModel**: Bing relateret søgeresultater

Nødvendige egenskaber for RelatedSearchResultModel:


Ingen af egenskaberne, der er påkrævet. 


**Alle egenskaber**: 


| Navn | Datatype |
|---|---|
|Titel|streng|
|Id|streng|
|BingUrl|streng|



 **CompositeSearchResultModel**: Bing sammensatte søgeresultater

Nødvendige egenskaber for CompositeSearchResultModel:


Ingen af egenskaberne, der er påkrævet. 


**Alle egenskaber**: 


| Navn | Datatype |
|---|---|
|WebResultsTotal|heltal|
|ImageResultsTotal|heltal|
|VideoResultsTotal|heltal|
|NewsResultsTotal|heltal|
|SpellSuggestionsTotal|heltal|
|WebResults|matrix|
|ImageResults|matrix|
|VideoResults|matrix|
|NewsResults|matrix|
|SpellSuggestionResults|matrix|
|RelatedSearchResults|matrix|


## <a name="object-definitions"></a>Objekt definition(s): 

 **PostOperationResponse**: repræsenterer svar i indlæg funktion af slæk forbindelse til opslag til slæk

Nødvendige egenskaber for PostOperationResponse:


Ingen af egenskaberne, der er påkrævet. 


**Alle egenskaber**: 


| Navn | Datatype |
|---|---|
|Ok|Boolesk værdi|
|kanal|streng|
|TS|streng|
|meddelelse|ikke defineret|
|fejl|streng|



 **MessageItem**: en kanal meddelelse.

Nødvendige egenskaber for MessageItem:


Ingen af egenskaberne, der er påkrævet. 


**Alle egenskaber**: 


| Navn | Datatype |
|---|---|
|tekst|streng|
|id|streng|
|bruger|streng|
|oprettet|heltal|
|is_user slettet|Boolesk værdi|


## <a name="next-steps"></a>Næste trin
[Oprette en logik app](../app-service-logic/app-service-logic-create-a-logic-app.md)

[1]: ./media/connectors-create-api-slack/connectionconfig1.png
[2]: ./media/connectors-create-api-slack/connectionconfig2.png 
[3]: ./media/connectors-create-api-slack/connectionconfig3.png
[4]: ./media/connectors-create-api-slack/connectionconfig4.png
[5]: ./media/connectors-create-api-slack/connectionconfig5.png
[6]: ./media/connectors-create-api-slack/connectionconfig6.png
