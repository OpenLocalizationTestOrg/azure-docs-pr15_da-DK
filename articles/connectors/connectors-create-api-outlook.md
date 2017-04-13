<properties
pageTitle="Outlook.com | Microsoft Azure"
description="Opret logik apps med Azure App service. Outlook.com-forbindelse tillader dig at administrere din mail, kalendere og kontakter. Du kan udføre forskellige handlinger som send e-mail, planlægge møder, kontaktpersoner osv."
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

# <a name="get-started-with-the-outlookcom-connector"></a>Komme i gang med Outlook.com-forbindelsen

Outlook.com-forbindelse tillader dig at administrere din mail, kalendere og kontakter. Du kan udføre forskellige handlinger som send e-mail, planlægge møder, kontaktpersoner osv.

>[AZURE.NOTE] Denne version i denne artikel gælder for logik apps 2015-08-01-skema prøveversionen. 

Du kan komme i gang ved at oprette en logik app nu, i [oprette en logik app](../app-service-logic/app-service-logic-create-a-logic-app.md).

## <a name="triggers-and-actions"></a>Udløsere og handlinger

Outlook.com-forbindelse kan bruges som en handling den har trigger(s). Alle forbindelser understøtter data i JSON og XML-formater. 

 Outlook.com-forbindelse har følgende handling(er) og/eller trigger(s) tilgængelige:

### <a name="outlookcom-actions"></a>Outlook.com-handlinger
Du kan udføre disse handling(er):

|Handling|Beskrivelse|
|--- | ---|
|[GetEmails](connectors-create-api-outlook.md#GetEmails)|Henter mails fra en mappe|
|[SendEmail](connectors-create-api-outlook.md#SendEmail)|Sender en mail|
|[DeleteEmail](connectors-create-api-outlook.md#DeleteEmail)|Sletter en mail efter id|
|[MarkAsRead](connectors-create-api-outlook.md#MarkAsRead)|Markerer en mail som læst|
|[ReplyTo](connectors-create-api-outlook.md#ReplyTo)|Svar til en mail|
|[GetAttachment](connectors-create-api-outlook.md#GetAttachment)|Henter mail vedhæftet fil efter id|
|[SendMailWithOptions](connectors-create-api-outlook.md#SendMailWithOptions)|Sende en mail med flere indstillinger, og vent på modtageren om at svare med en af indstillingerne|
|[SendApprovalMail](connectors-create-api-outlook.md#SendApprovalMail)|Sende en mail til godkendelse, og vent på et svar fra modtageren|
|[CalendarGetTables](connectors-create-api-outlook.md#CalendarGetTables)|Henter kalendere|
|[CalendarGetItems](connectors-create-api-outlook.md#CalendarGetItems)|Henter elementer fra en kalender|
|[CalendarPostItem](connectors-create-api-outlook.md#CalendarPostItem)|Opretter en ny begivenhed|
|[CalendarGetItem](connectors-create-api-outlook.md#CalendarGetItem)|Henter et bestemt element fra en kalender|
|[CalendarDeleteItem](connectors-create-api-outlook.md#CalendarDeleteItem)|Sletter et kalenderelement|
|[CalendarPatchItem](connectors-create-api-outlook.md#CalendarPatchItem)|Delvist opdaterer et kalenderelement|
|[ContactGetTables](connectors-create-api-outlook.md#ContactGetTables)|Henter mapper med kontaktpersoner|
|[ContactGetItems](connectors-create-api-outlook.md#ContactGetItems)|Henter kontakter fra en mappe med kontaktpersoner|
|[ContactPostItem](connectors-create-api-outlook.md#ContactPostItem)|Opretter en ny kontakt|
|[ContactGetItem](connectors-create-api-outlook.md#ContactGetItem)|Henter en bestemt kontakt fra en mappe med kontaktpersoner|
|[ContactDeleteItem](connectors-create-api-outlook.md#ContactDeleteItem)|Sletter en kontakt|
|[ContactPatchItem](connectors-create-api-outlook.md#ContactPatchItem)|Delvist opdaterer en kontakt|
### <a name="outlookcom-triggers"></a>Outlook.com udløsere
Du kan lytte til disse hændelser:

|Udløser | Beskrivelse|
|--- | ---|
|Om at starte begivenhed snart|Udløser et flow, når du går i gang med en kommende kalenderbegivenhed|
|På ny mail|Udløser et flow, når der modtages en ny mail|
|På nye elementer|Udløses, når der oprettes et nyt kalenderelement|
|Opdateret elementer|Udløses, når et kalenderelement er ændret|


## <a name="create-a-connection-to-outlookcom"></a>Oprette en forbindelse til Outlook.com
Hvis du vil oprette logik apps med Outlook.com, skal du først oprette en **forbindelse** og derefter registrere oplysninger om følgende egenskaber: 

|Egenskaben| Påkrævet|Beskrivelse|
| ---|---|---|
|Token|Ja|Angive legitimationsoplysninger for Outlook.com|
Når du opretter forbindelsen, kan du bruge det til at udføre handlingerne, og lyt til de udløsere, der er beskrevet i denne artikel.

>[AZURE.INCLUDE [Steps to create a connection to Outlook.com](../../includes/connectors-create-api-outlook.md)] 

>[AZURE.TIP] Du kan bruge denne forbindelse i andre logik apps.  

## <a name="reference-for-outlookcom"></a>Reference til Outlook.com
Gælder for version: 1.0

## <a name="onupcomingevents"></a>OnUpcomingEvents
Om at starte begivenhed snart: udløser et flow, når du går i gang med en kommende kalenderbegivenhed 

```GET: /Events/OnUpcomingEvents``` 

| Navn| Datatype|Påkrævet|Placeret i|Standardværdi|Beskrivelse|
| ---|---|---|---|---|---|
|tabel|streng|Ja|forespørgsel|ingen|Entydigt id for kalenderen|
|lookAheadTimeInMinutes|heltal|Nej|forespørgsel|15|Tid (i minutter) til at se videre efter kommende begivenheder|

#### <a name="response"></a>Svar

|Navn|Beskrivelse|
|---|---|
|200|Lykkedes|
|202|Lykkedes|
|400|BadRequest|
|401|Uautoriseret|
|403|Forbudt|
|500|Intern serverfejl|
|standard|Mislykkedes.|


## <a name="getemails"></a>GetEmails
Få mails: henter mails fra en mappe 

```GET: /Mail``` 

| Navn| Datatype|Påkrævet|Placeret i|Standardværdi|Beskrivelse|
| ---|---|---|---|---|---|
|Mappesti|streng|Nej|forespørgsel|Indbakke|Stien til mappen til at hente mails (standard: "Indbakke")|
|toppen|heltal|Nej|forespørgsel|10|Antallet af mails til at hente (standard: 10)|
|fetchOnlyUnread|Boolesk værdi|Nej|forespørgsel|SAND|Hente kun ulæste mails?|
|includeAttachments|Boolesk værdi|Nej|forespørgsel|FALSK|Hvis du vil også blive hentet indstillet til sand, vedhæftede filer sammen med mail|
|searchQuery|streng|Nej|forespørgsel|ingen|Søgeforespørgsel til at filtrere mails|
|Spring over|heltal|Nej|forespørgsel|0|Antallet af mails for at springe (standard: 0)|
|skipToken|streng|Nej|forespørgsel|ingen|Springe token til Hent ny side|

#### <a name="response"></a>Svar

|Navn|Beskrivelse|
|---|---|
|200|Lykkedes|
|400|BadRequest|
|401|Uautoriseret|
|403|Forbudt|
|500|Intern serverfejl|
|standard|Mislykkedes.|


## <a name="sendemail"></a>SendEmail
Sende mail: sender en mail 

```POST: /Mail``` 

| Navn| Datatype|Påkrævet|Placeret i|Standardværdi|Beskrivelse|
| ---|---|---|---|---|---|
|emailMessage| |Ja|brødteksten|ingen|Mail|

#### <a name="response"></a>Svar

|Navn|Beskrivelse|
|---|---|
|200|Lykkedes|
|400|BadRequest|
|401|Uautoriseret|
|403|Forbudt|
|500|Intern serverfejl|
|standard|Mislykkedes.|


## <a name="deleteemail"></a>DeleteEmail
Slette mail: Sletter en mail efter id 

```DELETE: /Mail/{messageId}``` 

| Navn| Datatype|Påkrævet|Placeret i|Standardværdi|Beskrivelse|
| ---|---|---|---|---|---|
|messageId|streng|Ja|sti|ingen|Id for mailen for at slette|

#### <a name="response"></a>Svar

|Navn|Beskrivelse|
|---|---|
|200|Lykkedes|
|400|BadRequest|
|401|Uautoriseret|
|403|Forbudt|
|500|Intern serverfejl|
|standard|Mislykkedes.|


## <a name="markasread"></a>MarkAsRead
Markér som læst: markerer en mail som læst 

```POST: /Mail/MarkAsRead/{messageId}``` 

| Navn| Datatype|Påkrævet|Placeret i|Standardværdi|Beskrivelse|
| ---|---|---|---|---|---|
|messageId|streng|Ja|sti|ingen|Id for e-mailen skal markeres som læst|

#### <a name="response"></a>Svar

|Navn|Beskrivelse|
|---|---|
|200|Lykkedes|
|400|BadRequest|
|401|Uautoriseret|
|403|Forbudt|
|500|Intern serverfejl|
|standard|Mislykkedes.|


## <a name="replyto"></a>ReplyTo
Svare på mail: svar til en mail 

```POST: /Mail/ReplyTo/{messageId}``` 

| Navn| Datatype|Påkrævet|Placeret i|Standardværdi|Beskrivelse|
| ---|---|---|---|---|---|
|messageId|streng|Ja|sti|ingen|Id for mailen for at besvare|
|kommentar|streng|Ja|forespørgsel|ingen|Svar kommentar|
|replyAll|Boolesk værdi|Nej|forespørgsel|FALSK|Svare alle modtagerne|

#### <a name="response"></a>Svar

|Navn|Beskrivelse|
|---|---|
|200|Lykkedes|
|400|BadRequest|
|401|Uautoriseret|
|403|Forbudt|
|500|Intern serverfejl|
|standard|Mislykkedes.|


## <a name="getattachment"></a>GetAttachment
Få vedhæftet fil: henter mail vedhæftet fil efter id 

```GET: /Mail/{messageId}/Attachments/{attachmentId}``` 

| Navn| Datatype|Påkrævet|Placeret i|Standardværdi|Beskrivelse|
| ---|---|---|---|---|---|
|messageId|streng|Ja|sti|ingen|Id for e-mailen|
|attachmentId|streng|Ja|sti|ingen|Id for den vedhæftede fil for at hente|

#### <a name="response"></a>Svar

|Navn|Beskrivelse|
|---|---|
|200|Lykkedes|
|400|BadRequest|
|401|Uautoriseret|
|403|Forbudt|
|500|Intern serverfejl|
|standard|Mislykkedes.|


## <a name="onnewemail"></a>OnNewEmail
På ny mail: udløser et flow, når der modtages en ny mail 

```GET: /Mail/OnNewEmail``` 

| Navn| Datatype|Påkrævet|Placeret i|Standardværdi|Beskrivelse|
| ---|---|---|---|---|---|
|Mappesti|streng|Nej|forespørgsel|Indbakke|E-mail-mappe for at hente (standard: Indbakke)|
|til|streng|Nej|forespørgsel|ingen|Modtager mailadresser|
|fra|streng|Nej|forespørgsel|ingen|Fra adresse|
|prioritet|streng|Nej|forespørgsel|Normal|Vigtigheden af e-mailen (høj, Normal, lav) (standard: Normal)|
|fetchOnlyWithAttachment|Boolesk værdi|Nej|forespørgsel|FALSK|Hente kun mails med en vedhæftet fil|
|includeAttachments|Boolesk værdi|Nej|forespørgsel|FALSK|Medtage vedhæftede filer|
|subjectFilter|streng|Nej|forespørgsel|ingen|Streng, der skal søges efter i emnet|

#### <a name="response"></a>Svar

|Navn|Beskrivelse|
|---|---|
|200|Lykkedes|
|202|Accepteret|
|400|BadRequest|
|401|Uautoriseret|
|403|Forbudt|
|500|Intern serverfejl|
|standard|Mislykkedes.|


## <a name="sendmailwithoptions"></a>SendMailWithOptions
Sende mail med indstillinger: sende en mail med flere indstillinger, og vent på modtageren om at svare med en af indstillingerne 

```POST: /mailwithoptions/$subscriptions``` 

| Navn| Datatype|Påkrævet|Placeret i|Standardværdi|Beskrivelse|
| ---|---|---|---|---|---|
|optionsEmailSubscription| |Ja|brødteksten|ingen|Anmodning om abonnement til e-mail-indstillinger|

#### <a name="response"></a>Svar

|Navn|Beskrivelse|
|---|---|
|200|Ok|
|201|Abonnement, der er oprettet|
|400|BadRequest|
|401|Uautoriseret|
|403|Forbudt|
|500|Intern serverfejl|
|standard|Mislykkedes.|


## <a name="sendapprovalmail"></a>SendApprovalMail
Send godkendelse mail: sende en mail til godkendelse, og vent på et svar fra modtageren 

```POST: /approvalmail/$subscriptions``` 

| Navn| Datatype|Påkrævet|Placeret i|Standardværdi|Beskrivelse|
| ---|---|---|---|---|---|
|approvalEmailSubscription| |Ja|brødteksten|ingen|Anmodning om abonnement til godkendelse af mail|

#### <a name="response"></a>Svar

|Navn|Beskrivelse|
|---|---|
|200|Ok|
|201|Abonnement, der er oprettet|
|400|BadRequest|
|401|Uautoriseret|
|403|Forbudt|
|500|Intern serverfejl|
|standard|Mislykkedes.|


## <a name="calendargettables"></a>CalendarGetTables
Få kalendere: henter kalendere 

```GET: /datasets/calendars/tables``` 

Der er ingen parametre til dette opkald
#### <a name="response"></a>Svar

|Navn|Beskrivelse|
|---|---|
|200|Ok|
|standard|Mislykkedes.|


## <a name="calendargetitems"></a>CalendarGetItems
Få hændelser: henter elementer fra en kalender 

```GET: /datasets/calendars/tables/{table}/items``` 

| Navn| Datatype|Påkrævet|Placeret i|Standardværdi|Beskrivelse|
| ---|---|---|---|---|---|
|tabel|streng|Ja|sti|ingen|Entydigt id for kalenderen til at hente|
|$filter|streng|Nej|forespørgsel|ingen|En forespørgsel til ODATA-filter til at begrænse antallet af poster|
|$orderby|streng|Nej|forespørgsel|ingen|En ODATA SorterEfter forespørgsel for at angive rækkefølgen af poster|
|$skip|heltal|Nej|forespørgsel|ingen|Antallet af poster at springe (standard = 0)|
|$top|heltal|Nej|forespørgsel|ingen|Maksimale antal poster til at hente (standard = 256)|

#### <a name="response"></a>Svar

|Navn|Beskrivelse|
|---|---|
|200|Ok|
|standard|Mislykkedes.|


## <a name="calendarpostitem"></a>CalendarPostItem
Oprette begivenhed: opretter en ny begivenhed 

```POST: /datasets/calendars/tables/{table}/items``` 

| Navn| Datatype|Påkrævet|Placeret i|Standardværdi|Beskrivelse|
| ---|---|---|---|---|---|
|tabel|streng|Ja|sti|ingen|Entydigt id for en kalender|
|element| |Ja|brødteksten|ingen|Kalender-element til at oprette|

#### <a name="response"></a>Svar

|Navn|Beskrivelse|
|---|---|
|200|Ok|
|standard|Mislykkedes.|


## <a name="calendargetitem"></a>CalendarGetItem
Få begivenhed: henter et bestemt element fra en kalender 

```GET: /datasets/calendars/tables/{table}/items/{id}``` 

| Navn| Datatype|Påkrævet|Placeret i|Standardværdi|Beskrivelse|
| ---|---|---|---|---|---|
|tabel|streng|Ja|sti|ingen|Entydigt id for en kalender|
|id|streng|Ja|sti|ingen|Entydigt id for et kalenderelement til at hente|

#### <a name="response"></a>Svar

|Navn|Beskrivelse|
|---|---|
|200|Ok|
|standard|Mislykkedes.|


## <a name="calendardeleteitem"></a>CalendarDeleteItem
Slette begivenhed: sletter et kalenderelement 

```DELETE: /datasets/calendars/tables/{table}/items/{id}``` 

| Navn| Datatype|Påkrævet|Placeret i|Standardværdi|Beskrivelse|
| ---|---|---|---|---|---|
|tabel|streng|Ja|sti|ingen|Entydigt id for en kalender|
|id|streng|Ja|sti|ingen|Entydigt id for kalenderelement slette|

#### <a name="response"></a>Svar

|Navn|Beskrivelse|
|---|---|
|200|Ok|
|standard|Mislykkedes.|


## <a name="calendarpatchitem"></a>CalendarPatchItem
Opdater begivenhed: delvist opdaterer et kalenderelement 

```PATCH: /datasets/calendars/tables/{table}/items/{id}``` 

| Navn| Datatype|Påkrævet|Placeret i|Standardværdi|Beskrivelse|
| ---|---|---|---|---|---|
|tabel|streng|Ja|sti|ingen|Entydigt id for en kalender|
|id|streng|Ja|sti|ingen|Entydigt id for kalenderelement opdatere|
|element| |Ja|brødteksten|ingen|Kalender-element til at opdatere|

#### <a name="response"></a>Svar

|Navn|Beskrivelse|
|---|---|
|200|Ok|
|standard|Mislykkedes.|


## <a name="calendargetonnewitems"></a>CalendarGetOnNewItems
På nye elementer: udløses, når der oprettes et nyt kalenderelement 

```GET: /datasets/calendars/tables/{table}/onnewitems``` 

| Navn| Datatype|Påkrævet|Placeret i|Standardværdi|Beskrivelse|
| ---|---|---|---|---|---|
|tabel|streng|Ja|sti|ingen|Entydigt id for en kalender|
|$filter|streng|Nej|forespørgsel|ingen|En forespørgsel til ODATA-filter til at begrænse antallet af poster|
|$orderby|streng|Nej|forespørgsel|ingen|En ODATA SorterEfter forespørgsel for at angive rækkefølgen af poster|
|$skip|heltal|Nej|forespørgsel|ingen|Antallet af poster at springe (standard = 0)|
|$top|heltal|Nej|forespørgsel|ingen|Maksimale antal poster til at hente (standard = 256)|

#### <a name="response"></a>Svar

|Navn|Beskrivelse|
|---|---|
|200|Ok|
|standard|Mislykkedes.|


## <a name="calendargetonupdateditems"></a>CalendarGetOnUpdatedItems
Opdateret elementer: udløses, når et kalenderelement er ændret 

```GET: /datasets/calendars/tables/{table}/onupdateditems``` 

| Navn| Datatype|Påkrævet|Placeret i|Standardværdi|Beskrivelse|
| ---|---|---|---|---|---|
|tabel|streng|Ja|sti|ingen|Entydigt id for en kalender|
|$filter|streng|Nej|forespørgsel|ingen|En forespørgsel til ODATA-filter til at begrænse antallet af poster|
|$orderby|streng|Nej|forespørgsel|ingen|En ODATA SorterEfter forespørgsel for at angive rækkefølgen af poster|
|$skip|heltal|Nej|forespørgsel|ingen|Antallet af poster at springe (standard = 0)|
|$top|heltal|Nej|forespørgsel|ingen|Maksimale antal poster til at hente (standard = 256)|

#### <a name="response"></a>Svar

|Navn|Beskrivelse|
|---|---|
|200|Ok|
|standard|Mislykkedes.|


## <a name="contactgettables"></a>ContactGetTables
Få mapper med kontakter: henter kontaktpersoner 

```GET: /datasets/contacts/tables``` 

Der er ingen parametre til dette opkald
#### <a name="response"></a>Svar

|Navn|Beskrivelse|
|---|---|
|200|Ok|
|standard|Mislykkedes.|


## <a name="contactgetitems"></a>ContactGetItems
Hente kontakter: henter kontakter fra en mappe med kontaktpersoner 

```GET: /datasets/contacts/tables/{table}/items``` 

| Navn| Datatype|Påkrævet|Placeret i|Standardværdi|Beskrivelse|
| ---|---|---|---|---|---|
|tabel|streng|Ja|sti|ingen|Entydigt id for mappen med kontakter til at hente|
|$filter|streng|Nej|forespørgsel|ingen|En forespørgsel til ODATA-filter til at begrænse antallet af poster|
|$orderby|streng|Nej|forespørgsel|ingen|En ODATA SorterEfter forespørgsel for at angive rækkefølgen af poster|
|$skip|heltal|Nej|forespørgsel|ingen|Antallet af poster at springe (standard = 0)|
|$top|heltal|Nej|forespørgsel|ingen|Maksimale antal poster til at hente (standard = 256)|

#### <a name="response"></a>Svar

|Navn|Beskrivelse|
|---|---|
|200|Ok|
|standard|Mislykkedes.|


## <a name="contactpostitem"></a>ContactPostItem
Opret kontakt: opretter en ny kontakt 

```POST: /datasets/contacts/tables/{table}/items``` 

| Navn| Datatype|Påkrævet|Placeret i|Standardværdi|Beskrivelse|
| ---|---|---|---|---|---|
|tabel|streng|Ja|sti|ingen|Entydigt id for en mappe med kontaktpersoner|
|element| |Ja|brødteksten|ingen|Kontakt for at oprette|

#### <a name="response"></a>Svar

|Navn|Beskrivelse|
|---|---|
|200|Ok|
|standard|Mislykkedes.|


## <a name="contactgetitem"></a>ContactGetItem
Få kontakt: henter en bestemt kontakt fra en mappe med kontaktpersoner 

```GET: /datasets/contacts/tables/{table}/items/{id}``` 

| Navn| Datatype|Påkrævet|Placeret i|Standardværdi|Beskrivelse|
| ---|---|---|---|---|---|
|tabel|streng|Ja|sti|ingen|Entydigt id for en mappe med kontaktpersoner|
|id|streng|Ja|sti|ingen|Entydigt id for en kontakt til at hente|

#### <a name="response"></a>Svar

|Navn|Beskrivelse|
|---|---|
|200|Ok|
|standard|Mislykkedes.|


## <a name="contactdeleteitem"></a>ContactDeleteItem
Slette kontakt: Sletter en kontakt 

```DELETE: /datasets/contacts/tables/{table}/items/{id}``` 

| Navn| Datatype|Påkrævet|Placeret i|Standardværdi|Beskrivelse|
| ---|---|---|---|---|---|
|tabel|streng|Ja|sti|ingen|Entydigt id for en mappe med kontaktpersoner|
|id|streng|Ja|sti|ingen|Entydigt id for kontakt for at slette|

#### <a name="response"></a>Svar

|Navn|Beskrivelse|
|---|---|
|200|Ok|
|standard|Mislykkedes.|


## <a name="contactpatchitem"></a>ContactPatchItem
Opdatere kontakt: delvist opdaterer en kontakt 

```PATCH: /datasets/contacts/tables/{table}/items/{id}``` 

| Navn| Datatype|Påkrævet|Placeret i|Standardværdi|Beskrivelse|
| ---|---|---|---|---|---|
|tabel|streng|Ja|sti|ingen|Entydigt id for en mappe med kontaktpersoner|
|id|streng|Ja|sti|ingen|Entydigt id for kontakt for at opdatere|
|element| |Ja|brødteksten|ingen|Kontakt element for at opdatere|

#### <a name="response"></a>Svar

|Navn|Beskrivelse|
|---|---|
|200|Ok|
|standard|Mislykkedes.|


## <a name="object-definitions"></a>Objektdefinitioner 

### <a name="triggerbatchresponseidictionarystringobject"></a>TriggerBatchResponse [IDictionary [streng, objekt]]


| Egenskabsnavn | Datatype | Påkrævet |
|---|---|---|
|værdi|matrix|Nej |



### <a name="object"></a>Objekt


| Egenskabsnavn | Datatype | Påkrævet |
|---|---|---|



### <a name="sendmessage"></a>SendMessage


| Egenskabsnavn | Datatype | Påkrævet |
|---|---|---|
|Vedhæftede filer|matrix|Nej |
|Fra|streng|Nej |
|Cc|streng|Nej |
|Bcc|streng|Nej |
|Emne|streng|Ja |
|Brødteksten|streng|Ja |
|Prioritet|streng|Nej |
|IsHtml|Boolesk værdi|Nej |
|Til|streng|Ja |



### <a name="sendattachment"></a>SendAttachment


| Egenskabsnavn | Datatype | Påkrævet |
|---|---|---|
|@odata.type|streng|Nej |
|Navn|streng|Ja |
|ContentBytes|streng|Ja |



### <a name="receivemessage"></a>ReceiveMessage


| Egenskabsnavn | Datatype | Påkrævet |
|---|---|---|
|Id|streng|Nej |
|IsRead|Boolesk værdi|Nej |
|HasAttachment|Boolesk værdi|Nej |
|DateTimeReceived|streng|Nej |
|Vedhæftede filer|matrix|Nej |
|Fra|streng|Nej |
|Cc|streng|Nej |
|Bcc|streng|Nej |
|Emne|streng|Ja |
|Brødteksten|streng|Ja |
|Prioritet|streng|Nej |
|IsHtml|Boolesk værdi|Nej |
|Til|streng|Ja |



### <a name="receiveattachment"></a>ReceiveAttachment


| Egenskabsnavn | Datatype | Påkrævet |
|---|---|---|
|Id|streng|Ja |
|ContentType|streng|Ja |
|@odata.type|streng|Nej |
|Navn|streng|Ja |
|ContentBytes|streng|Ja |



### <a name="digestmessage"></a>DigestMessage


| Egenskabsnavn | Datatype | Påkrævet |
|---|---|---|
|Emne|streng|Ja |
|Brødteksten|streng|Nej |
|Prioritet|streng|Nej |
|Samlet|matrix|Ja |
|Vedhæftede filer|matrix|Nej |
|Til|streng|Ja |



### <a name="triggerbatchresponsereceivemessage"></a>TriggerBatchResponse [ReceiveMessage]


| Egenskabsnavn | Datatype | Påkrævet |
|---|---|---|
|værdi|matrix|Nej |



### <a name="datasetsmetadata"></a>DataSetsMetadata


| Egenskabsnavn | Datatype | Påkrævet |
|---|---|---|
|tabelformat|ikke defineret|Nej |
|BLOB|ikke defineret|Nej |



### <a name="tabulardatasetsmetadata"></a>TabularDataSetsMetadata


| Egenskabsnavn | Datatype | Påkrævet |
|---|---|---|
|kilde|streng|Nej |
|vist navn|streng|Nej |
|urlEncoding|streng|Nej |
|tableDisplayName|streng|Nej |
|tablePluralName|streng|Nej |



### <a name="blobdatasetsmetadata"></a>BlobDataSetsMetadata


| Egenskabsnavn | Datatype | Påkrævet |
|---|---|---|
|kilde|streng|Nej |
|vist navn|streng|Nej |
|urlEncoding|streng|Nej |



### <a name="tablemetadata"></a>TableMetadata


| Egenskabsnavn | Datatype | Påkrævet |
|---|---|---|
|Navn|streng|Nej |
|titel|streng|Nej |
|x-ms-tilladelser|streng|Nej |
|x-ms-funktioner|ikke defineret|Nej |
|skema|ikke defineret|Nej |



### <a name="tablecapabilitiesmetadata"></a>TableCapabilitiesMetadata


| Egenskabsnavn | Datatype | Påkrævet |
|---|---|---|
|sortRestrictions|ikke defineret|Nej |
|filterRestrictions|ikke defineret|Nej |
|filterFunctions|matrix|Nej |



### <a name="tablesortrestrictionsmetadata"></a>TableSortRestrictionsMetadata


| Egenskabsnavn | Datatype | Påkrævet |
|---|---|---|
|kan sorteres|Boolesk værdi|Nej |
|unsortableProperties|matrix|Nej |
|ascendingOnlyProperties|matrix|Nej |



### <a name="tablefilterrestrictionsmetadata"></a>TableFilterRestrictionsMetadata


| Egenskabsnavn | Datatype | Påkrævet |
|---|---|---|
|filtrerbare|Boolesk værdi|Nej |
|nonFilterableProperties|matrix|Nej |
|requiredProperties|matrix|Nej |



### <a name="optionsemailsubscription"></a>OptionsEmailSubscription


| Egenskabsnavn | Datatype | Påkrævet |
|---|---|---|
|NotificationUrl|streng|Nej |
|Meddelelse|ikke defineret|Nej |



### <a name="messagewithoptions"></a>MessageWithOptions


| Egenskabsnavn | Datatype | Påkrævet |
|---|---|---|
|Emne|streng|Ja |
|Indstillinger|streng|Ja |
|Brødteksten|streng|Nej |
|Prioritet|streng|Nej |
|Vedhæftede filer|matrix|Nej |
|Til|streng|Ja |



### <a name="subscriptionresponse"></a>SubscriptionResponse


| Egenskabsnavn | Datatype | Påkrævet |
|---|---|---|
|id|streng|Nej |
|ressource|streng|Nej |
|notificationType|streng|Nej |
|notificationUrl|streng|Nej |



### <a name="approvalemailsubscription"></a>ApprovalEmailSubscription


| Egenskabsnavn | Datatype | Påkrævet |
|---|---|---|
|NotificationUrl|streng|Nej |
|Meddelelse|ikke defineret|Nej |



### <a name="approvalmessage"></a>ApprovalMessage


| Egenskabsnavn | Datatype | Påkrævet |
|---|---|---|
|Emne|streng|Ja |
|Indstillinger|streng|Ja |
|Brødteksten|streng|Nej |
|Prioritet|streng|Nej |
|Vedhæftede filer|matrix|Nej |
|Til|streng|Ja |



### <a name="approvalemailresponse"></a>ApprovalEmailResponse


| Egenskabsnavn | Datatype | Påkrævet |
|---|---|---|
|SelectedOption|streng|Nej |



### <a name="tableslist"></a>TablesList


| Egenskabsnavn | Datatype | Påkrævet |
|---|---|---|
|værdi|matrix|Nej |



### <a name="table"></a>Tabel


| Egenskabsnavn | Datatype | Påkrævet |
|---|---|---|
|Navn|streng|Nej |
|Vist navn|streng|Nej |



### <a name="item"></a>Element


| Egenskabsnavn | Datatype | Påkrævet |
|---|---|---|
|ItemInternalId|streng|Nej |



### <a name="calendaritemslist"></a>CalendarItemsList


| Egenskabsnavn | Datatype | Påkrævet |
|---|---|---|
|værdi|matrix|Nej |



### <a name="calendaritem"></a>CalendarItem


| Egenskabsnavn | Datatype | Påkrævet |
|---|---|---|
|ItemInternalId|streng|Nej |



### <a name="contactitemslist"></a>ContactItemsList


| Egenskabsnavn | Datatype | Påkrævet |
|---|---|---|
|værdi|matrix|Nej |



### <a name="contactitem"></a>ContactItem


| Egenskabsnavn | Datatype | Påkrævet |
|---|---|---|
|ItemInternalId|streng|Nej |



### <a name="datasetslist"></a>DataSetsList


| Egenskabsnavn | Datatype | Påkrævet |
|---|---|---|
|værdi|matrix|Nej |



### <a name="dataset"></a>Datasæt


| Egenskabsnavn | Datatype | Påkrævet |
|---|---|---|
|Navn|streng|Nej |
|Vist navn|streng|Nej |


## <a name="next-steps"></a>Næste trin
[Oprette en logik app](../app-service-logic/app-service-logic-create-a-logic-app.md)