<properties
pageTitle="Bruge Office 365 Video-forbindelsen i dine logik apps | Microsoft Azure"
description="Introduktion til brug af Office 365 Video-forbindelsen i dine Microsoft Azure App service logik apps"
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

# <a name="get-started-with-the-office365-video-connector"></a>Introduktion til Office 365 Video-forbindelse
Oprette forbindelse til Office 365 Video for at få oplysninger om et Office 365 video, få en liste over videoer og meget mere. Office 365 Video-forbindelse kan bruges fra:

- Logik apps 

>[AZURE.NOTE] Denne version i denne artikel gælder for logik apps 2015-08-01-skema prøveversionen. Denne forbindelse understøttes ikke på en hvilken som helst tidligere versioner af databaseskema.

Med Office 365 Video kan du:

- Opbyg din virksomhed flow baseret på de data, du får fra Office 365 Video. 
- Brug handlinger, der kontrollerer video portalen status, få en liste over alle video i en kanal og meget mere. Disse handlinger får du respons, og foretag derefter output tilgængelig for andre handlinger. Du kan for eksempel bruge Bing Søg forbindelsen til at søge efter Office 365 videoer og derefter bruge Office 365 video forbindelsen til at få oplysninger om denne video. Hvis videoen opfylder dine behov, kan du sende denne video på Facebook. 

For at tilføje en handling i logik apps skal du se [oprette en logik app](../app-service-logic/app-service-logic-create-a-logic-app.md).

## <a name="triggers-and-actions"></a>Udløsere og handlinger

Office 365 Video forbindelsen har de følgende handlinger, der er tilgængelige. Der findes ingen udløsere.

| Udløsere | Handlinger|
| --- | --- |
| Ingen | <ul><li>Kontroller video portalen status</li><li>Få alle kunne kanaler</li><li>Få afspilning URL-adressen til Azure Media Services manifestet til en video</li><li>Få bærertoken for at få adgang til dekryptere videoen</li><li>Henter oplysninger om et bestemt Office 365 video</li><li>Viser en liste over alle office365 videoer findes i en kanal</li></ul>

Alle forbindelser understøtter data i JSON og XML-formater. 

## <a name="create-a-connection-to-office365-video-connector"></a>Oprette en forbindelse til Office 365 Video-stikket
Når du tilføjer denne forbindelse til din logik apps, skal du logge på din Office 365 Video-konto og tillade logik apps til at oprette forbindelse til din konto.

>[AZURE.INCLUDE [Steps to create a connection to Office 365 Video](../../includes/connectors-create-api-office365video.md)]

Når du opretter forbindelsen, du angiver i Office 365 video egenskaber, som lejer navnet eller kanal ID. **REST-API reference** i dette emne beskrives disse egenskaber.

>[AZURE.TIP] Du kan bruge denne samme Office 365 Video-forbindelse i andre logik apps.

## <a name="swagger-rest-api-reference"></a>Swagger REST-API reference
Gælder for version: 1.0.

### <a name="checks-video-portal-status"></a>Kontroller video portalen status 
Kontrollerer video portalen status for at se, om video services er aktiveret.  
```GET: /{tenant}/IsEnabled``` 

| Navn| Datatype|Påkrævet|Placeret i|Standardværdi|Beskrivelse|
| ---|---|---|---|---|---|
|lejer|streng|Ja|sti|ingen|Lejer navnet på mappen, brugeren er en del af|


#### <a name="response"></a>Svar

|Navn|Beskrivelse|
|---|---|
|200|Lykkedes|
|400|BadRequest|
|401|Uautoriseret|
|404|Blev ikke fundet|
|500|Intern serverfejl|
|standard|Mislykkedes.|



### <a name="get-all-viewable-channels"></a>Få alle kunne kanaler 
Henter alle de kanaler, som brugeren har adgang til visning.  
```GET: /{tenant}/Channels``` 

| Navn| Datatype|Påkrævet|Placeret i|Standardværdi|Beskrivelse|
| ---|---|---|---|---|---|
|lejer|streng|Ja|sti|ingen|Lejer navnet på mappen, brugeren er en del af|


#### <a name="response"></a>Svar

|Navn|Beskrivelse|
|---|---|
|200|Lykkedes|
|400|BadRequest|
|401|Uautoriseret|
|404|Blev ikke fundet|
|500|Intern serverfejl|
|standard|Mislykkedes.|




### <a name="lists-all-the-office365-videos-present-in-a-channel"></a>Viser en liste over alle office365 videoer findes i en kanal 
Viser alle Office 365 videoer findes i en kanal.  
```GET: /{tenant}/Channels/{channelId}/Videos``` 

| Navn| Datatype|Påkrævet|Placeret i|Standardværdi|Beskrivelse|
| ---|---|---|---|---|---|
|lejer|streng|Ja|sti|ingen|Lejer navnet på mappen, brugeren er en del af|
|channelId|streng|Ja|sti|ingen|Den kanal-id, hvorfra videoer skal hentes|


#### <a name="response"></a>Svar

|Navn|Beskrivelse|
|---|---|
|200|Lykkedes|
|400|BadRequest|
|401|Uautoriseret|
|404|Blev ikke fundet|
|500|Intern serverfejl|
|standard|Mislykkedes.|




### <a name="gets-information-about-a-particular-office365-video"></a>Henter oplysninger om et bestemt Office 365 video 
Henter oplysninger om et bestemt Office 365 video.  
```GET: /{tenant}/Channels/{channelId}/Videos/{videoId}``` 

| Navn| Datatype|Påkrævet|Placeret i|Standardværdi|Beskrivelse|
| ---|---|---|---|---|---|
|lejer|streng|Ja|sti|ingen|Lejer navnet på mappen, brugeren er en del af|
|channelId|streng|Ja|sti|ingen|Kanal-id|
|videoId|streng|Ja|sti|ingen|Video-id|


#### <a name="response"></a>Svar

|Navn|Beskrivelse|
|---|---|
|200|Lykkedes|
|400|BadRequest|
|401|Uautoriseret|
|404|Blev ikke fundet|
|500|Intern serverfejl|
|standard|Mislykkedes.|




### <a name="get-playback-url-of-the-azure-media-services-manifest-for-a-video"></a>Få afspilning URL-adressen til Azure Media Services manifestet til en video 
Få afspilning URL-adressen til Azure Media Services manifestet for en video.  
```GET: /{tenant}/Channels/{channelId}/Videos/{videoId}/playbackurl``` 

| Navn| Datatype|Påkrævet|Placeret i|Standardværdi|Beskrivelse|
| ---|---|---|---|---|---|
|lejer|streng|Ja|sti|ingen|Lejer navnet på mappen, brugeren er en del af|
|channelId|streng|Ja|sti|ingen|Kanal-id|
|videoId|streng|Ja|sti|ingen|Video-id|
|streamingFormatType|streng|Ja|forespørgsel|ingen|Streaming formatet. 1 – jævne Streaming eller MPEG-STREG. 0 - HLS Streaming|


#### <a name="response"></a>Svar

|Navn|Beskrivelse|
|---|---|
|200|Lykkedes|
|400|BadRequest|
|401|Uautoriseret|
|404|Blev ikke fundet|
|500|Intern serverfejl|
|standard|Mislykkedes.|




### <a name="get-the-bearer-token-to-get-access-to-decrypt-the-video"></a>Få bærertoken for at få adgang til dekryptere videoen 
Få bærertoken for at få adgang til dekryptere videoen.  
```GET: /{tenant}/Channels/{channelId}/Videos/{videoId}/token```

| Navn| Datatype|Påkrævet|Placeret i|Standardværdi|Beskrivelse|
| ---|---|---|---|---|---|
|lejer|streng|Ja|sti|ingen|Lejer navnet på mappen, brugeren er en del af|
|channelId|streng|Ja|sti|ingen|Kanal-id|
|videoId|streng|Ja|sti|ingen|Video-id|


#### <a name="response"></a>Svar

|Navn|Beskrivelse|
|---|---|
|200|Lykkedes|
|400|BadRequest|
|401|Uautoriseret|
|404|Blev ikke fundet|
|500|Intern serverfejl|
|standard|Mislykkedes.|


## <a name="object-definitions"></a>Objektdefinitioner

#### <a name="channel-channel-class"></a>Kanal: Kanal klasse

| Navn | Datatype | Påkrævet|
|---|---|---|
|Id|streng|Nej|
|Titel|streng|Nej|
|Beskrivelse|streng|Nej|


#### <a name="video"></a>Video 

| Navn | Datatype |Påkrævet|
|---|---|---|
|Id|streng|Nej|
|Titel|streng|Nej|
|Beskrivelse|streng|Nej|
|CreationDate|streng|Nej|
|Ejer|streng|Nej|
|ThumbnailUrl|streng|Nej|
|VideoUrl|streng|Nej|
|VideoDuration|heltal|Nej|
|VideoProcessingStatus|heltal|Nej|
|ViewCount|heltal|Nej|


## <a name="next-steps"></a>Næste trin
[Opret en logik app](../app-service-logic/app-service-logic-create-a-logic-app.md).
