<properties
    pageTitle="Tilføje Facebook-forbindelse i dine logik Apps | Microsoft Azure"
    description="Oversigt over Facebook-forbindelse med REST-API parametre"
    services=""
    documentationCenter="" 
    authors="MandiOhlinger"
    manager="erikre"
    editor=""
    tags="connectors"/>

<tags
   ms.service="multiple"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na" 
   ms.date="08/18/2016"
   ms.author="mandia"/>

# <a name="get-started-with-the-facebook-connector"></a>Introduktion til Facebook-forbindelse
Oprette forbindelse til Facebook og sende indlæg til en tidslinje, få en feed side og meget mere. 

>[AZURE.NOTE] Denne version i denne artikel gælder for logik apps 2015-08-01-skema prøveversionen.


Med Facebook kan du:

- Opbyg din virksomhed rutediagram, der er baseret på de data, du får fra Facebook. 
- Brug en udløser, når der modtages en ny meddelelse.
- Brug handlinger, sende indlæg til tidslinjen, få en feed side og meget mere. Disse handlinger får du respons, og foretag derefter output tilgængelig for andre handlinger. For eksempel når der er et nyt indlæg på tidslinjen, kan du tage dette indlæg og push på Twitter-feed. 



For at tilføje en handling i logik apps skal du se [oprette en logik app](../app-service-logic/app-service-logic-create-a-logic-app.md).

## <a name="triggers-and-actions"></a>Udløsere og handlinger
Facebook-forbindelse omfatter følgende udløser og handlinger. 

| Udløsere | Handlinger|
| --- | --- |
| <ul><li>Når der er et nyt indlæg på min tidslinje</li></ul> |<ul><li>Få feed fra min tidslinje</li><li>Sende indlæg til mine tidslinje</li><li>Når der er et nyt indlæg på min tidslinje</li><li>Få siden feed</li><li>Få bruger tidslinje</li><li>Sende indlæg til side</li></ul>

Alle forbindelser understøtter data i JSON og XML-formater.

## <a name="create-a-connection-to-facebook"></a>Oprette en forbindelse til Facebook
Når du tilføjer denne forbindelse til din logik apps, skal du tillade logik apps til at oprette forbindelse til din Facebook.

1. Log på din Facebook-konto
2. Vælg **godkende**, og Tillad dine logik apps til at oprette forbindelse og bruge din Facebook. 

>[AZURE.INCLUDE [Steps to create a connection to Facebook](../../includes/connectors-create-api-facebook.md)]

>[AZURE.TIP] Du kan bruge denne samme Facebook-forbindelse i andre logik apps.

## <a name="swagger-rest-api-reference"></a>Swagger REST-API reference
Gælder for version: 1.0.

### <a name="get-feed-from-my-timeline"></a>Få feed fra min tidslinje
Henter feeds fra bruger logget på tidslinjen.  
```GET: /me/feed```

| Navn|Datatype|Påkrævet|Placeret i|Standardværdi|Beskrivelse|
| ---|---|---|---|---|---|
|felter|streng|Nej|forespørgsel|ingen |Angiv de felter, der skal returneres. Eksempel (-id, navn og billede).|
|grænse|heltal|Nej|forespørgsel| ingen|Maksimale antal indlæg, der skal hentes|
|med|streng|Nej|forespørgsel| ingen|Begrænse listen over indlæg for kun personer med placering, der er vedhæftet.|
|filter|streng|Nej|forespørgsel| ingen|Hent kun indlæg, der svarer til et bestemt stream filter.|

#### <a name="response"></a>Svar
|Navn|Beskrivelse|
|---|---|
|200|Ok|
|400|Forkert anmodning|
|500|Intern serverfejl|
|standard|Mislykkedes.|


### <a name="post-to-my-timeline"></a>Sende indlæg til mine tidslinje
Sende en statusmeddelelse til bruger logget på tidslinjen.  
```POST: /me/feed```

| Navn|Datatype|Påkrævet|Placeret i|Standardværdi|Beskrivelse|
| ---|---|---|---|---|---|
|indlæg|streng |Ja|brødteksten|ingen |Ny meddelelse sendes|

#### <a name="response"></a>Svar
|Navn|Beskrivelse|
|---|---|
|200|Ok|
|400|Forkert anmodning|
|500|Intern serverfejl|
|standard|Mislykkedes.|


### <a name="when-there-is-a-new-post-on-my-timeline"></a>Når der er et nyt indlæg på min tidslinje
Aktiverer et nyt flow, når der er et nyt indlæg på bruger logget på tidslinjen.  
```GET: /trigger/me/feed```

Der er ingen parametre. 

#### <a name="response"></a>Svar
|Navn|Beskrivelse|
|---|---|
|200|Ok|
|400|Forkert anmodning|
|500|Intern serverfejl|
|standard|Mislykkedes.|


### <a name="get-page-feed"></a>Få siden feed
Få indlæg fra feed af en bestemt side.  
```GET: /{pageId}/feed```

| Navn|Datatype|Påkrævet|Placeret i|Standardværdi|Beskrivelse|
| ---|---|---|---|---|---|
|pageId|streng|Ja|sti| ingen|Id for den side, hvor indlæg har skal hentes.|
|grænse|heltal|Nej|forespørgsel| ingen|Maksimale antal indlæg, der skal hentes|
|include_hidden|Boolesk værdi|Nej|forespørgsel|ingen |Om der skal indeholde en hvilken som helst indlæg, der er skjult af siden|
|felter|streng|Nej|forespørgsel|ingen |Angiv de felter, der skal returneres. Eksempel (-id, navn og billede).|

#### <a name="response"></a>Svar
|Navn|Beskrivelse|
|---|---|
|200|Ok|
|400|Forkert anmodning|
|500|Intern serverfejl|
|standard|Mislykkedes.|


### <a name="get-user-timeline"></a>Få bruger tidslinje
Få indlæg fra en bruger tidslinje.  
```GET: /{userId}/feed```

| Navn|Datatype|Påkrævet|Placeret i|Standardværdi|Beskrivelse|
| ---|---|---|---|---|---|
|bruger-id|streng|Ja|sti|ingen |Id'et for den bruger, hvis tidslinje har skal hentes.|
|grænse|heltal|Nej|forespørgsel|ingen |Maksimale antal indlæg, der skal hentes|
|med|streng|Nej|forespørgsel|ingen |Begrænse listen over indlæg for kun personer med placering, der er vedhæftet.|
|filter|streng|Nej|forespørgsel| ingen|Hent kun indlæg, der svarer til et bestemt stream filter.|
|felter|streng|Nej|forespørgsel| ingen|Angiv de felter, der skal returneres. Eksempel (-id, navn og billede).|

#### <a name="response"></a>Svar
|Navn|Beskrivelse|
|---|---|
|200|Ok|
|400|Forkert anmodning|
|500|Intern serverfejl|
|standard|Mislykkedes.|


### <a name="post-to-page"></a>Sende indlæg til side
Sende en meddelelse til en Facebook Page som den bruger logget på.  
```POST: /{pageId}/feed```

| Navn|Datatype|Påkrævet|Placeret i|Standardværdi|Beskrivelse|
| ---|---|---|---|---|---|
|pageId|streng|Ja|sti|ingen |Id på siden for at skrive.|
|indlæg|mange |Ja|brødteksten|ingen |Ny meddelelse sendes.|

#### <a name="response"></a>Svar
|Navn|Beskrivelse|
|---|---|
|200|Ok|
|400|Forkert anmodning|
|500|Intern serverfejl|
|standard|Mislykkedes.|


## <a name="object-definitions"></a>Objektdefinitioner

#### <a name="getfeedresponse"></a>GetFeedResponse

|Egenskabsnavn | Datatype | Påkrævet|
|---|---|---|
|data|matrix|Nej|

#### <a name="triggerfeedresponse"></a>TriggerFeedResponse

|Egenskabsnavn | Datatype |Påkrævet|
|---|---|---|
|data|matrix|Nej|

#### <a name="postitem-a-single-entry-in-a-profiles-feed"></a>Subject: En enkelt post i en profil er feed
Profilen, der kan være en bruger, siden, app eller gruppe. 

|Egenskabsnavn | Datatype |Påkrævet|
|---|---|---|
|id|streng|Nej|
|admin_creator|matrix|Nej|
|billedtekst|streng|Nej|
|created_time|streng|Nej|
|Beskrivelse|streng|Nej|
|feed_targeting|ikke defineret|Nej|
|fra|ikke defineret|Nej|
|ikonet|streng|Nej|
|is_hidden|Boolesk værdi|Nej|
|is_published|Boolesk værdi|Nej|
|link|streng|Nej|
|meddelelse|streng|Nej|
|Navn|streng|Nej|
|object_id|streng|Nej|
|billede|streng|Nej|
|Placer|ikke defineret|Nej|
|beskyttelse af personlige oplysninger|ikke defineret|Nej|
|Egenskaber|matrix|Nej|
|kilde|streng|Nej|
|status_type|streng|Nej|
|historie|streng|Nej|
|målretning af|ikke defineret|Nej|
|til|matrix|Nej|
|type|streng|Nej|
|updated_time|streng|Nej|
|with_tags|ikke defineret|Nej|

#### <a name="triggeritem-a-single-entry-in-a-profiles-feed"></a>TriggerItem: En enkelt post i en profil er feed
Profilen, der kan være en bruger, siden, app eller gruppe.

|Egenskabsnavn | Datatype |Påkrævet|
|---|---|---|
|id|streng|Nej|
|created_time|streng|Nej|
|fra|ikke defineret|Nej|
|meddelelse|streng|Nej|
|type|streng|Nej|

#### <a name="adminitem"></a>AdminItem

|Egenskabsnavn | Datatype |Påkrævet|
|---|---|---|
|id|streng|Nej|
|link|streng|Nej|

#### <a name="propertyitem"></a>PropertyItem

|Egenskabsnavn | Datatype |Påkrævet|
|---|---|---|
|Navn|streng|Nej|
|tekst|streng|Nej|
|linkreference|streng|Nej|

#### <a name="userpostfeedrequest"></a>UserPostFeedRequest

|Egenskabsnavn | Datatype |Påkrævet|
|---|---|---|
|meddelelse|streng|Ja|
|link|streng|Nej|
|billede|streng|Nej|
|Navn|streng|Nej|
|billedtekst|streng|Nej|
|Beskrivelse|streng|Nej|
|Placer|streng|Nej|
|mærker|streng|Nej|
|beskyttelse af personlige oplysninger|ikke defineret|Nej|
|object_attachment|streng|Nej|

#### <a name="pagepostfeedrequest"></a>PagePostFeedRequest

|Egenskabsnavn | Datatype |Påkrævet|
|---|---|---|
|meddelelse|streng|Ja|
|link|streng|Nej|
|billede|streng|Nej|
|Navn|streng|Nej|
|billedtekst|streng|Nej|
|Beskrivelse|streng|Nej|
|handlinger|matrix|Nej|
|Placer|streng|Nej|
|mærker|streng|Nej|
|object_attachment|streng|Nej|
|målretning af|ikke defineret|Nej|
|feed_targeting|ikke defineret|Nej|
|publiceret|Boolesk værdi|Nej|
|scheduled_publish_time|streng|Nej|
|backdated_time|streng|Nej|
|backdated_time_granularity|streng|Nej|
|child_attachments|matrix|Nej|
|multi_share_end_card|Boolesk værdi|Nej|

#### <a name="postfeedresponse"></a>PostFeedResponse

|Egenskabsnavn | Datatype |Påkrævet|
|---|---|---|
|id|streng|Nej|

#### <a name="profilecollection"></a>ProfileCollection

|Egenskabsnavn | Datatype |Påkrævet|
|---|---|---|
|data|matrix|Nej|

#### <a name="useritem"></a>UserItem

|Egenskabsnavn | Datatype |Påkrævet|
|---|---|---|
|id|streng|Nej|
|Fornavn|streng|Nej|
|Efternavn|streng|Nej|
|Navn|streng|Nej|
|køn|streng|Nej|
|om|streng|Nej|

#### <a name="actionitem"></a>ActionItem

|Egenskabsnavn | Datatype |Påkrævet|
|---|---|---|
|Navn|streng|Nej|
|link|streng|Nej|

#### <a name="targetitem"></a>TargetItem

|Egenskabsnavn | Datatype |Påkrævet|
|---|---|---|
|lande|matrix|Nej|
|landestandarder|matrix|Nej|
|områder|matrix|Nej|
|byer|matrix|Nej|

#### <a name="feedtargetitem-object-that-controls-news-feed-targeting-for-this-post"></a>FeedTargetItem: Objekt, der styrer nyheder feed målretning til denne meddelelse
Alle i disse grupper er større sandsynlighed for at se dette indlæg, andre er mindre sandsynlige. Gælder kun for sider.

|Egenskabsnavn | Datatype |Påkrævet|
|---|---|---|
|lande|matrix|Nej|
|områder|matrix|Nej|
|byer|matrix|Nej|
|age_min|heltal|Nej|
|age_max|heltal|Nej|
|køn|matrix|Nej|
|relationship_statuses|matrix|Nej|
|interested_in|matrix|Nej|
|college_years|matrix|Nej|
|interesser|matrix|Nej|
|relevant_until|heltal|Nej|
|education_statuses|matrix|Nej|
|landestandarder|matrix|Nej|

#### <a name="placeitem"></a>PlaceItem

|Egenskabsnavn | Datatype |Påkrævet|
|---|---|---|
|id|streng|Nej|
|Navn|streng|Nej|
|overall_rating|tal|Nej|
|placering|ikke defineret|Nej|

#### <a name="locationitem"></a>LocationItem

|Egenskabsnavn | Datatype |Påkrævet|
|---|---|---|
|by|streng|Nej|
|land|streng|Nej|
|længde|tal|Nej|
|located_in|streng|Nej|
|længdegrader|tal|Nej|
|Navn|streng|Nej|
|område|streng|Nej|
|stat|streng|Nej|
|Gade|streng|Nej|
|ZIP|streng|Nej|

#### <a name="privacyitem"></a>PrivacyItem

|Egenskabsnavn | Datatype |Påkrævet|
|---|---|---|
|Beskrivelse|streng|Nej|
|værdi|streng|Ja|
|Tillad|streng|Nej|
|nægte|streng|Nej|
|venner|streng|Nej|

#### <a name="childattachmentsitem"></a>ChildAttachmentsItem

|Egenskabsnavn | Datatype |Påkrævet|
|---|---|---|
|link|streng|Nej|
|billede|streng|Nej|
|image_hash|streng|Nej|
|Navn|streng|Nej|
|Beskrivelse|streng|Nej|

#### <a name="postphotorequest"></a>PostPhotoRequest

|Egenskabsnavn | Datatype |Påkrævet|
|---|---|---|
|URL-adresse|streng|Ja|
|billedtekst|streng|Nej|

#### <a name="postphotoresponse"></a>PostPhotoResponse

|Egenskabsnavn | Datatype |Påkrævet|
|---|---|---|
|id|streng|Ja|
|post_id|streng|Ja|

#### <a name="postvideorequest"></a>PostVideoRequest

|Egenskabsnavn | Datatype |Påkrævet|
|---|---|---|
|videoData|streng|Ja|
|Beskrivelse|streng|Ja|
|titel|streng|Ja|
|uploadedVideoName|streng|Nej|

#### <a name="getphotoresponse"></a>GetPhotoResponse

|Egenskabsnavn | Datatype |Påkrævet|
|---|---|---|
|data|ikke defineret|Ja|

#### <a name="getphotoresponseitem"></a>GetPhotoResponseItem

|Egenskabsnavn | Datatype |Påkrævet|
|---|---|---|
|URL-adresse|streng|Ja|
|is_silhouette|Boolesk værdi|Ja|
|højde|streng|Nej|
|bredde|streng|Nej|

#### <a name="geteventresponse"></a>GetEventResponse

|Egenskabsnavn | Datatype |Påkrævet|
|---|---|---|
|data|matrix|Ja|

#### <a name="geteventresponseitem"></a>GetEventResponseItem

|Egenskabsnavn | Datatype |Påkrævet|
|---|---|---|
|id|streng|Ja|
|Navn|streng|Ja|
|start_time|streng|Nej|
|end_time|streng|Nej|
|tidszone|streng|Nej|
|placering|streng|Nej|
|Beskrivelse|streng|Nej|
|ticket_uri|streng|Nej|
|rsvp_status|streng|Ja|


## <a name="next-steps"></a>Næste trin

[Opret en logik app](../app-service-logic/app-service-logic-create-a-logic-app.md).
