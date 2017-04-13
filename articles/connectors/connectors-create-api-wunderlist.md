<properties
pageTitle="Wunderlist | Microsoft Azure"
description="Opret logik apps med Azure App service. Wunderlist giver en opgaveliste liste og opgave manager så andre kan få deres ting udført.  Om du deler en indkøbsliste med en holder, gør arbejder på et projekt eller planlægge en ferie Wunderlist det nemt at registrere, dele og fuldføre din to¬dos. Wunderlist synkroniserer med det samme mellem din telefon, tablet eller computer, så du kan få adgang til alle dine opgaver fra et vilkårligt sted."
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

# <a name="get-started-with-the-wunderlist-connector"></a>Komme i gang med Wunderlist forbindelsen

Wunderlist giver en opgaveliste liste og opgave manager så andre kan få deres ting udført.  Om du deler en indkøbsliste med en holder, gør arbejder på et projekt eller planlægge en ferie Wunderlist det nemt at registrere, dele og fuldføre din to¬dos. Wunderlist synkroniserer med det samme mellem din telefon, tablet eller computer, så du kan få adgang til alle dine opgaver fra et vilkårligt sted.

>[AZURE.NOTE] Denne version i denne artikel gælder for logik apps 2015-08-01-skema prøveversionen. 

Du kan komme i gang ved at oprette en logik app nu, i [oprette en logik app](../app-service-logic/app-service-logic-create-a-logic-app.md).

## <a name="triggers-and-actions"></a>Udløsere og handlinger

Wunderlist forbindelsen kan bruges som en handling den har trigger(s). Alle forbindelser understøtter data i JSON og XML-formater. 

 Wunderlist forbindelsen har følgende handling(er) og/eller trigger(s) tilgængelige:

### <a name="wunderlist-actions"></a>Wunderlist handlinger
Du kan udføre disse handling(er):

|Handling|Beskrivelse|
|--- | ---|
|[RetrieveLists](connectors-create-api-wunderlist.md#retrievelists)|Hent de lister, der er knyttet til din konto.|
|[CreateList](connectors-create-api-wunderlist.md#createlist)|Oprette en liste.|
|[ListTasks](connectors-create-api-wunderlist.md#listtasks)|Hent opgaver fra en bestemt liste.|
|[CreateTask](connectors-create-api-wunderlist.md#createtask)|Oprette en opgave|
|[ListSubTasks](connectors-create-api-wunderlist.md#listsubtasks)|Hent underopgaver, fra en bestemt liste eller fra en bestemt opgave.|
|[CreateSubTask](connectors-create-api-wunderlist.md#createsubtask)|Oprette en underopgaven inden for en bestemt opgave|
|[ListNotes](connectors-create-api-wunderlist.md#listnotes)|Hente noter til en bestemt liste eller en bestemt opgave.|
|[CreateNote](connectors-create-api-wunderlist.md#createnote)|Tilføje en bemærkning til en bestemt opgave|
|[ListComments](connectors-create-api-wunderlist.md#listcomments)|Hent opgave kommentarer til en bestemt liste eller en bestemt opgave.|
|[CreateComment](connectors-create-api-wunderlist.md#createcomment)|Føje en kommentar til en bestemt opgave|
|[RetrieveReminders](connectors-create-api-wunderlist.md#retrievereminders)|Hent påmindelser for en bestemt liste eller en bestemt opgave.|
|[CreateReminder](connectors-create-api-wunderlist.md#createreminder)|Indstille en påmindelse.|
|[RetrieveFiles](connectors-create-api-wunderlist.md#retrievefiles)|Hente filer til en bestemt liste eller en bestemt opgave.|
|[GetList](connectors-create-api-wunderlist.md#getlist)|Henter en bestemt liste|
|[DeleteList](connectors-create-api-wunderlist.md#deletelist)|Sletter en liste|
|[UpdateList](connectors-create-api-wunderlist.md#updatelist)|Opdatere en bestemt liste|
|[GetTask](connectors-create-api-wunderlist.md#gettask)|Henter en bestemt opgave|
|[UpdateTask](connectors-create-api-wunderlist.md#updatetask)|Opdaterer en bestemt opgave|
|[DeleteTask](connectors-create-api-wunderlist.md#deletetask)|Sletter en bestemt opgave|
|[GetSubTask](connectors-create-api-wunderlist.md#getsubtask)|Henter en bestemt underopgaven|
|[UpdateSubTask](connectors-create-api-wunderlist.md#updatesubtask)|Opdaterer en bestemt underopgaven|
|[DeleteSubTask](connectors-create-api-wunderlist.md#deletesubtask)|Sletter en bestemt underopgaven|
|[GetNote](connectors-create-api-wunderlist.md#getnote)|Hente en bestemt note|
|[UpdateNote](connectors-create-api-wunderlist.md#updatenote)|Opdatere en bestemt note|
|[DeleteNote](connectors-create-api-wunderlist.md#deletenote)|Slette en bestemt note|
|[GetComment](connectors-create-api-wunderlist.md#getcomment)|Hente en bestemt opgave kommentar|
|[UpdateReminder](connectors-create-api-wunderlist.md#updatereminder)|Opdatere en bestemt påmindelse|
|[DeleteReminder](connectors-create-api-wunderlist.md#deletereminder)|Slette en bestemt påmindelse|
### <a name="wunderlist-triggers"></a>Wunderlist udløsere
Du kan lytte til disse hændelser:

|Udløser | Beskrivelse|
|--- | ---|
|Når en opgave forfalder|Aktiverer et nyt flow, når en opgave på listen forfalder|
|Når der oprettes en ny opgave|Udløser en ny flow, når der oprettes en ny opgave på listen|
|Når der sker en påmindelse|Udløser en ny flow, når der udsendes en påmindelse|


## <a name="create-a-connection-to-wunderlist"></a>Oprette en forbindelse til Wunderlist
Hvis du vil oprette logik apps med Wunderlist, skal du først oprette en **forbindelse** og derefter angive detaljerne for følgende egenskaber: 

|Egenskaben| Påkrævet|Beskrivelse|
| ---|---|---|
|Token|Ja|Angive Wunderlist legitimationsoplysninger|
Når du opretter forbindelsen, kan du bruge det til at udføre handlingerne, og lyt til de udløsere, der er beskrevet i denne artikel. 


>[AZURE.INCLUDE [Steps to create a connection to Wunderlist](../../includes/connectors-create-api-wunderlist.md)] 


>[AZURE.TIP] Du kan bruge denne forbindelse i andre logik apps.

## <a name="reference-for-wunderlist"></a>Reference til Wunderlist
Gælder for version: 1.0

## <a name="triggertaskdue"></a>TriggerTaskDue
Når en opgave forfalder: udløser en ny flow, når en opgave på listen forfalder 

```GET: /trigger/tasksdue``` 

| Navn| Datatype|Påkrævet|Placeret i|Standardværdi|Beskrivelse|
| ---|---|---|---|---|---|
|list_id|heltal|Ja|forespørgsel|ingen|Liste-ID|

#### <a name="response"></a>Svar

|Navn|Beskrivelse|
|---|---|
|200|Handlingen er udført|


## <a name="triggertasknew"></a>TriggerTaskNew
Når der oprettes en ny opgave: udløser en ny flow, når der oprettes en ny opgave på listen 

```GET: /trigger/tasksnew``` 

| Navn| Datatype|Påkrævet|Placeret i|Standardværdi|Beskrivelse|
| ---|---|---|---|---|---|
|list_id|heltal|Ja|forespørgsel|ingen|Liste-ID|

#### <a name="response"></a>Svar

|Navn|Beskrivelse|
|---|---|
|200|Handlingen er udført|


## <a name="triggerreminder"></a>TriggerReminder
Når en påmindelse indtræffer: udløser en ny flow, når der udsendes en påmindelse 

```GET: /trigger/reminders``` 

| Navn| Datatype|Påkrævet|Placeret i|Standardværdi|Beskrivelse|
| ---|---|---|---|---|---|
|list_id|heltal|Ja|forespørgsel|ingen|Liste-ID|
|TASK_ID|heltal|Nej|forespørgsel|ingen|Opgave-ID|

#### <a name="response"></a>Svar

|Navn|Beskrivelse|
|---|---|
|200|Handlingen er udført|


## <a name="retrievelists"></a>RetrieveLists
Hent lister: hente de lister, der er knyttet til din konto. 

```GET: /lists``` 

| Navn| Datatype|Påkrævet|Placeret i|Standardværdi|Beskrivelse|
| ---|---|---|---|---|---|

#### <a name="response"></a>Svar

|Navn|Beskrivelse|
|---|---|
|200|Handlingen er udført|
|400|Forkert anmodning|
|500|Intern serverfejl. Der opstod en ukendt fejl|
|standard|Mislykkedes.|


## <a name="createlist"></a>CreateList
Oprette en liste: oprette en liste. 

```POST: /lists``` 

| Navn| Datatype|Påkrævet|Placeret i|Standardværdi|Beskrivelse|
| ---|---|---|---|---|---|
|indlæg| |Ja|brødteksten|ingen|Ny liste skal have oprettet|

#### <a name="response"></a>Svar

|Navn|Beskrivelse|
|---|---|
|200|Handlingen er udført|
|standard|Mislykkedes.|


## <a name="listtasks"></a>ListTasks
Få opgaver: hente opgaver fra en bestemt liste. 

```GET: /tasks``` 

| Navn| Datatype|Påkrævet|Placeret i|Standardværdi|Beskrivelse|
| ---|---|---|---|---|---|
|list_id|heltal|Ja|forespørgsel|ingen|Liste-ID|
|fuldført|Boolesk værdi|Nej|forespørgsel|ingen|Fuldført|

#### <a name="response"></a>Svar

|Navn|Beskrivelse|
|---|---|
|200|Handlingen er udført|
|400|Forkert anmodning|
|500|Intern serverfejl. Der opstod en ukendt fejl|
|standard|Mislykkedes.|


## <a name="createtask"></a>CreateTask
Oprette en opgave: oprette en opgave 

```POST: /tasks``` 

| Navn| Datatype|Påkrævet|Placeret i|Standardværdi|Beskrivelse|
| ---|---|---|---|---|---|
|indlæg| |Ja|brødteksten|ingen|Ny opgave skal have oprettet|

#### <a name="response"></a>Svar

|Navn|Beskrivelse|
|---|---|
|201|Oprettet|


## <a name="listsubtasks"></a>ListSubTasks
Få underopgaver: hente underopgaver, fra en bestemt liste eller fra en bestemt opgave. 

```GET: /subtasks``` 

| Navn| Datatype|Påkrævet|Placeret i|Standardværdi|Beskrivelse|
| ---|---|---|---|---|---|
|list_id|heltal|Ja|forespørgsel|ingen|Liste-ID|
|TASK_ID|heltal|Nej|forespørgsel|ingen|Opgave-ID|
|fuldført|Boolesk værdi|Nej|forespørgsel|ingen|Fuldført|

#### <a name="response"></a>Svar

|Navn|Beskrivelse|
|---|---|
|200|Handlingen er udført|
|400|Forkert anmodning|
|500|Intern serverfejl. Der opstod en ukendt fejl|
|standard|Mislykkedes.|


## <a name="createsubtask"></a>CreateSubTask
Oprette en underopgaven: oprette en underopgaven inden for en bestemt opgave 

```POST: /subtasks``` 

| Navn| Datatype|Påkrævet|Placeret i|Standardværdi|Beskrivelse|
| ---|---|---|---|---|---|
|indlæg| |Ja|brødteksten|ingen|Ny underopgaven skal have oprettet|

#### <a name="response"></a>Svar

|Navn|Beskrivelse|
|---|---|
|201|Oprettet|


## <a name="listnotes"></a>ListNotes
Hente noter: hente noter til en bestemt liste eller en bestemt opgave. 

```GET: /notes``` 

| Navn| Datatype|Påkrævet|Placeret i|Standardværdi|Beskrivelse|
| ---|---|---|---|---|---|
|list_id|heltal|Ja|forespørgsel|ingen|Liste-ID|
|TASK_ID|heltal|Nej|forespørgsel|ingen|Opgave-ID|

#### <a name="response"></a>Svar

|Navn|Beskrivelse|
|---|---|
|200|Handlingen er udført|
|400|Forkert anmodning|
|500|Intern serverfejl. Der opstod en ukendt fejl|
|standard|Mislykkedes.|


## <a name="createnote"></a>CreateNote
Oprette en note: tilføje en bemærkning til en bestemt opgave 

```POST: /notes``` 

| Navn| Datatype|Påkrævet|Placeret i|Standardværdi|Beskrivelse|
| ---|---|---|---|---|---|
|indlæg| |Ja|brødteksten|ingen|Ny note skal have oprettet|

#### <a name="response"></a>Svar

|Navn|Beskrivelse|
|---|---|
|201|Oprettet|


## <a name="listcomments"></a>ListComments
Få opgave kommentarer: hente opgave kommentarer til en bestemt liste eller en bestemt opgave. 

```GET: /task_comments``` 

| Navn| Datatype|Påkrævet|Placeret i|Standardværdi|Beskrivelse|
| ---|---|---|---|---|---|
|list_id|heltal|Ja|forespørgsel|ingen|Liste-ID|
|TASK_ID|heltal|Nej|forespørgsel|ingen|Opgave-ID|

#### <a name="response"></a>Svar

|Navn|Beskrivelse|
|---|---|
|200|Handlingen er udført|
|400|Forkert anmodning|
|500|Intern serverfejl. Der opstod en ukendt fejl|
|standard|Mislykkedes.|


## <a name="createcomment"></a>CreateComment
Føje en kommentar til en opgave: føje en kommentar til en bestemt opgave 

```POST: /task_comments``` 

| Navn| Datatype|Påkrævet|Placeret i|Standardværdi|Beskrivelse|
| ---|---|---|---|---|---|
|indlæg| |Ja|brødteksten|ingen|Ny opgave kommentar skal have oprettet|

#### <a name="response"></a>Svar

|Navn|Beskrivelse|
|---|---|
|201|Oprettet|


## <a name="retrievereminders"></a>RetrieveReminders
Få påmindelser: hente påmindelser for en bestemt liste eller en bestemt opgave. 

```GET: /reminders``` 

| Navn| Datatype|Påkrævet|Placeret i|Standardværdi|Beskrivelse|
| ---|---|---|---|---|---|
|list_id|heltal|Ja|forespørgsel|ingen|Liste-ID|
|TASK_ID|heltal|Nej|forespørgsel|ingen|Opgave-ID|

#### <a name="response"></a>Svar

|Navn|Beskrivelse|
|---|---|
|200|Handlingen er udført|
|400|Forkert anmodning|
|500|Intern serverfejl. Der opstod en ukendt fejl|
|standard|Mislykkedes.|


## <a name="createreminder"></a>CreateReminder
Indstille en påmindelse: indstille en påmindelse. 

```POST: /reminders``` 

| Navn| Datatype|Påkrævet|Placeret i|Standardværdi|Beskrivelse|
| ---|---|---|---|---|---|
|indlæg| |Ja|brødteksten|ingen|Rykker skal have oprettet|

#### <a name="response"></a>Svar

|Navn|Beskrivelse|
|---|---|
|200|Handlingen er udført|
|standard|Mislykkedes.|


## <a name="retrievefiles"></a>RetrieveFiles
Få filer: hente filer til en bestemt liste eller en bestemt opgave. 

```GET: /files``` 

| Navn| Datatype|Påkrævet|Placeret i|Standardværdi|Beskrivelse|
| ---|---|---|---|---|---|
|list_id|heltal|Ja|forespørgsel|ingen|Liste-ID|
|TASK_ID|heltal|Nej|forespørgsel|ingen|Opgave-ID|

#### <a name="response"></a>Svar

|Navn|Beskrivelse|
|---|---|
|200|Handlingen er udført|
|400|Forkert anmodning|
|500|Intern serverfejl. Der opstod en ukendt fejl|
|standard|Mislykkedes.|


## <a name="getlist"></a>GetList
Få liste: henter en bestemt liste 

```GET: /lists/{id}``` 

| Navn| Datatype|Påkrævet|Placeret i|Standardværdi|Beskrivelse|
| ---|---|---|---|---|---|
|id|streng|Ja|sti|ingen|Liste-ID|

#### <a name="response"></a>Svar

|Navn|Beskrivelse|
|---|---|
|200|Ok|


## <a name="deletelist"></a>DeleteList
Slette liste: Sletter en liste 

```DELETE: /lists/{id}``` 

| Navn| Datatype|Påkrævet|Placeret i|Standardværdi|Beskrivelse|
| ---|---|---|---|---|---|
|id|heltal|Ja|sti|ingen|Liste-ID|
|revision|heltal|Ja|forespørgsel|ingen|Revision|

#### <a name="response"></a>Svar

|Navn|Beskrivelse|
|---|---|
|204|Intet indhold|


## <a name="updatelist"></a>UpdateList
Opdatere en liste: opdatere en bestemt liste 

```PATCH: /lists/{id}``` 

| Navn| Datatype|Påkrævet|Placeret i|Standardværdi|Beskrivelse|
| ---|---|---|---|---|---|
|id|heltal|Ja|sti|ingen|Liste-ID|
|indlæg| |Ja|brødteksten|ingen|Vis oplysninger|

#### <a name="response"></a>Svar

|Navn|Beskrivelse|
|---|---|
|200|Ok|


## <a name="gettask"></a>GetTask
Få opgave: henter en bestemt opgave 

```GET: /tasks/{id}``` 

| Navn| Datatype|Påkrævet|Placeret i|Standardværdi|Beskrivelse|
| ---|---|---|---|---|---|
|list_id|heltal|Ja|forespørgsel|ingen|Liste-ID|
|id|heltal|Ja|sti|ingen|Opgave-ID|

#### <a name="response"></a>Svar

|Navn|Beskrivelse|
|---|---|
|200|Ok|


## <a name="updatetask"></a>UpdateTask
Opdatere en opgave: opdaterer en bestemt opgave 

```PATCH: /tasks/{id}``` 

| Navn| Datatype|Påkrævet|Placeret i|Standardværdi|Beskrivelse|
| ---|---|---|---|---|---|
|list_id|heltal|Ja|forespørgsel|ingen|Liste-ID|
|id|heltal|Ja|sti|ingen|Opgave-ID|
|indlæg| |Ja|brødteksten|ingen|Opgavedetaljer|

#### <a name="response"></a>Svar

|Navn|Beskrivelse|
|---|---|
|200|Ok|


## <a name="deletetask"></a>DeleteTask
Slette opgave: Sletter en bestemt opgave 

```DELETE: /tasks/{id}``` 

| Navn| Datatype|Påkrævet|Placeret i|Standardværdi|Beskrivelse|
| ---|---|---|---|---|---|
|list_id|heltal|Ja|forespørgsel|ingen|Liste-ID|
|id|heltal|Ja|sti|ingen|Opgave-ID|
|revision|heltal|Ja|forespørgsel|ingen|Revision|

#### <a name="response"></a>Svar

|Navn|Beskrivelse|
|---|---|
|204|Intet indhold|


## <a name="getsubtask"></a>GetSubTask
Få underopgaven: henter en bestemt underopgaven 

```GET: /subtasks/{id}``` 

| Navn| Datatype|Påkrævet|Placeret i|Standardværdi|Beskrivelse|
| ---|---|---|---|---|---|
|id|streng|Ja|sti|ingen|Underopgaven-ID|

#### <a name="response"></a>Svar

|Navn|Beskrivelse|
|---|---|
|200|Ok|


## <a name="updatesubtask"></a>UpdateSubTask
Opdatere en underopgaven: opdaterer en bestemt underopgaven 

```PATCH: /subtasks/{id}``` 

| Navn| Datatype|Påkrævet|Placeret i|Standardværdi|Beskrivelse|
| ---|---|---|---|---|---|
|id|heltal|Ja|sti|ingen|Underopgaven-ID|
|indlæg| |Ja|brødteksten|ingen|Underopgaven detaljer|

#### <a name="response"></a>Svar

|Navn|Beskrivelse|
|---|---|
|200|Ok|


## <a name="deletesubtask"></a>DeleteSubTask
Slette en underopgaven: Sletter en bestemt underopgaven 

```DELETE: /subtasks/{id}``` 

| Navn| Datatype|Påkrævet|Placeret i|Standardværdi|Beskrivelse|
| ---|---|---|---|---|---|
|id|heltal|Ja|sti|ingen|Underopgaven-ID|
|revision|heltal|Ja|forespørgsel|ingen|Revision|

#### <a name="response"></a>Svar

|Navn|Beskrivelse|
|---|---|
|204|Intet indhold|


## <a name="getnote"></a>GetNote
Få en note: hente en bestemt note 

```GET: /notes/{id}``` 

| Navn| Datatype|Påkrævet|Placeret i|Standardværdi|Beskrivelse|
| ---|---|---|---|---|---|
|id|streng|Ja|sti|ingen|Note-ID|

#### <a name="response"></a>Svar

|Navn|Beskrivelse|
|---|---|
|200|Ok|


## <a name="updatenote"></a>UpdateNote
Opdatere en note: opdatere en bestemt note 

```PATCH: /notes/{id}``` 

| Navn| Datatype|Påkrævet|Placeret i|Standardværdi|Beskrivelse|
| ---|---|---|---|---|---|
|id|heltal|Ja|sti|ingen|Note-ID|
|indlæg| |Ja|brødteksten|ingen|Note detaljer|

#### <a name="response"></a>Svar

|Navn|Beskrivelse|
|---|---|
|200|Ok|


## <a name="deletenote"></a>DeleteNote
Slette en note: slette en bestemt note 

```DELETE: /notes/{id}``` 

| Navn| Datatype|Påkrævet|Placeret i|Standardværdi|Beskrivelse|
| ---|---|---|---|---|---|
|id|heltal|Ja|sti|ingen|Note-ID|
|revision|heltal|Ja|forespørgsel|ingen|Revision|

#### <a name="response"></a>Svar

|Navn|Beskrivelse|
|---|---|
|204|Intet indhold|


## <a name="getcomment"></a>GetComment
Få opgave kommentar: hente en bestemt opgave kommentar 

```GET: /task_comments/{id}``` 

| Navn| Datatype|Påkrævet|Placeret i|Standardværdi|Beskrivelse|
| ---|---|---|---|---|---|
|id|streng|Ja|sti|ingen|Kommentar-ID|

#### <a name="response"></a>Svar

|Navn|Beskrivelse|
|---|---|
|200|Ok|


## <a name="updatereminder"></a>UpdateReminder
Opdatere en påmindelse: opdatere en bestemt påmindelse 

```PATCH: /reminders/{id}``` 

| Navn| Datatype|Påkrævet|Placeret i|Standardværdi|Beskrivelse|
| ---|---|---|---|---|---|
|id|heltal|Ja|sti|ingen|Påmindelse-ID|
|indlæg| |Ja|brødteksten|ingen|Påmindelse detaljer|

#### <a name="response"></a>Svar

|Navn|Beskrivelse|
|---|---|
|200|Ok|


## <a name="deletereminder"></a>DeleteReminder
Slette en påmindelse: slette en bestemt påmindelse 

```DELETE: /reminders/{id}``` 

| Navn| Datatype|Påkrævet|Placeret i|Standardværdi|Beskrivelse|
| ---|---|---|---|---|---|
|id|heltal|Ja|sti|ingen|ID for påmindelsen.|
|revision|heltal|Ja|forespørgsel|ingen|Revision|

#### <a name="response"></a>Svar

|Navn|Beskrivelse|
|---|---|
|204|Intet indhold|


## <a name="object-definitions"></a>Objektdefinitioner 

### <a name="list"></a>Liste


| Egenskabsnavn | Datatype | Påkrævet |
|---|---|---|
|id|heltal|Nej |
|created_at|streng|Nej |
|titel|streng|Nej |
|list_type|streng|Nej |
|type|streng|Nej |
|revision|heltal|Nej |



### <a name="createdlist"></a>CreatedList


| Egenskabsnavn | Datatype | Påkrævet |
|---|---|---|
|id|heltal|Nej |
|created_at|streng|Nej |
|titel|streng|Nej |
|revision|heltal|Nej |
|type|streng|Nej |



### <a name="task"></a>Opgave


| Egenskabsnavn | Datatype | Påkrævet |
|---|---|---|
|id|heltal|Nej |
|assignee_id|heltal|Nej |
|assigner_id|heltal|Nej |
|created_at|streng|Nej |
|created_by_id|heltal|Nej |
|due_date|streng|Nej |
|list_id|heltal|Nej |
|revision|heltal|Nej |
|starred|Boolesk værdi|Nej |
|titel|streng|Nej |



### <a name="subtask"></a>Underopgaven


| Egenskabsnavn | Datatype | Påkrævet |
|---|---|---|
|id|heltal|Nej |
|TASK_ID|heltal|Nej |
|created_at|streng|Nej |
|created_by_id|heltal|Nej |
|revision|streng|Nej |
|titel|streng|Nej |



### <a name="note"></a>Bemærk!


| Egenskabsnavn | Datatype | Påkrævet |
|---|---|---|
|id|heltal|Nej |
|TASK_ID|heltal|Nej |
|indhold|streng|Nej |
|created_at|streng|Nej |
|updated_at|streng|Nej |
|revision|heltal|Nej |



### <a name="comment"></a>Kommentar


| Egenskabsnavn | Datatype | Påkrævet |
|---|---|---|
|id|heltal|Nej |
|TASK_ID|heltal|Nej |
|revision|heltal|Nej |
|tekst|streng|Nej |
|type|streng|Nej |
|created_at|streng|Nej |



### <a name="reminder"></a>Påmindelse


| Egenskabsnavn | Datatype | Påkrævet |
|---|---|---|
|id|heltal|Nej |
|dato|streng|Nej |
|TASK_ID|heltal|Nej |
|revision|heltal|Nej |
|type|streng|Nej |
|created_at|streng|Nej |
|updated_at|streng|Nej |



### <a name="createdreminder"></a>CreatedReminder


| Egenskabsnavn | Datatype | Påkrævet |
|---|---|---|
|id|heltal|Nej |
|dato|streng|Nej |
|TASK_ID|heltal|Nej |
|revision|heltal|Nej |
|created_at|streng|Nej |
|updated_at|streng|Nej |



### <a name="file"></a>Fil


| Egenskabsnavn | Datatype | Påkrævet |
|---|---|---|
|id|heltal|Nej |
|URL-adresse|streng|Nej |
|TASK_ID|heltal|Nej |
|list_id|heltal|Nej |
|user_id|heltal|Nej |
|filnavn|streng|Nej |
|content_type|streng|Nej |
|file_size|heltal|Nej |
|local_created_at|streng|Nej |
|created_at|streng|Nej |
|updated_at|streng|Nej |
|type|streng|Nej |
|revision|heltal|Nej |



### <a name="newtask"></a>Ny opgave


| Egenskabsnavn | Datatype | Påkrævet |
|---|---|---|
|list_id|heltal|Ja |
|titel|streng|Ja |
|assignee_id|heltal|Nej |
|fuldført|Boolesk værdi|Nej |
|recurrence_type|streng|Nej |
|recurrence_count|heltal|Nej |
|due_date|streng|Nej |
|starred|Boolesk værdi|Nej |



### <a name="newlist"></a>NewList


| Egenskabsnavn | Datatype | Påkrævet |
|---|---|---|
|titel|streng|Ja |



### <a name="newsubtask"></a>NewSubtask


| Egenskabsnavn | Datatype | Påkrævet |
|---|---|---|
|list_id|heltal|Ja |
|TASK_ID|heltal|Ja |
|titel|streng|Ja |
|fuldført|Boolesk værdi|Nej |



### <a name="newnote"></a>NewNote


| Egenskabsnavn | Datatype | Påkrævet |
|---|---|---|
|list_id|heltal|Ja |
|TASK_ID|heltal|Ja |
|indhold|streng|Ja |



### <a name="newcomment"></a>NyKommentar


| Egenskabsnavn | Datatype | Påkrævet |
|---|---|---|
|list_id|heltal|Ja |
|TASK_ID|heltal|Ja |
|tekst|streng|Ja |



### <a name="newreminder"></a>NewReminder


| Egenskabsnavn | Datatype | Påkrævet |
|---|---|---|
|list_id|heltal|Ja |
|TASK_ID|heltal|Ja |
|dato|streng|Ja |



### <a name="updatetask"></a>UpdateTask


| Egenskabsnavn | Datatype | Påkrævet |
|---|---|---|
|revision|heltal|Nej |
|titel|streng|Nej |
|assignee_id|heltal|Nej |
|fuldført|Boolesk værdi|Nej |
|recurrence_type|streng|Nej |
|recurrence_count|heltal|Nej |
|due_date|streng|Nej |
|starred|Boolesk værdi|Nej |



### <a name="updatelist"></a>UpdateList


| Egenskabsnavn | Datatype | Påkrævet |
|---|---|---|
|revision|heltal|Nej |
|titel|streng|Nej |



### <a name="updatesubtask"></a>UpdateSubtask


| Egenskabsnavn | Datatype | Påkrævet |
|---|---|---|
|revision|heltal|Nej |
|titel|streng|Nej |
|fuldført|Boolesk værdi|Nej |



### <a name="updatenote"></a>UpdateNote


| Egenskabsnavn | Datatype | Påkrævet |
|---|---|---|
|revision|heltal|Nej |
|indhold|streng|Nej |



### <a name="updatereminder"></a>UpdateReminder


| Egenskabsnavn | Datatype | Påkrævet |
|---|---|---|
|dato|streng|Nej |
|revision|heltal|Nej |


## <a name="next-steps"></a>Næste trin
[Oprette en logik app](../app-service-logic/app-service-logic-create-a-logic-app.md)