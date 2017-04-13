<properties
pageTitle="ProjectOnline | Microsoft Azure"
description="Opret logik apps med Azure App service. Project Online er en fleksibel online løsning til styring af projektoversigten (PPM) og daglige arbejde fra Microsoft. Leveret via Office 365, Project Online giver organisationer mulighed at komme hurtigt i gang med muligheder for effektiv projektstyring til at planlægge, prioritere og administrere projekter og porteføljeinvesteringer for projekter – næsten overalt på næsten enhver enhed."
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

# <a name="get-started-with-the-projectonline-connector"></a>Komme i gang med ProjectOnline-forbindelse

Project Online er en fleksibel online løsning til styring af projektoversigten (PPM) og daglige arbejde fra Microsoft. Leveret via Office 365, Project Online giver organisationer mulighed at komme hurtigt i gang med muligheder for effektiv projektstyring til at planlægge, prioritere og administrere projekter og porteføljeinvesteringer for projekter – næsten overalt på næsten enhver enhed.

>[AZURE.NOTE] Denne version i denne artikel gælder for logik apps 2015-08-01-skema prøveversionen. 

Du kan komme i gang ved at oprette en logik app nu, i [oprette en logik app](../app-service-logic/app-service-logic-create-a-logic-app.md).

## <a name="triggers-and-actions"></a>Udløsere og handlinger

ProjectOnline forbindelsen kan bruges som en handling den har trigger(s). Alle forbindelser understøtter data i JSON og XML-formater. 

 ProjectOnline forbindelsen har følgende handling(er) og/eller trigger(s) tilgængelige:

### <a name="projectonline-actions"></a>ProjectOnline handlinger
Du kan udføre disse handling(er):

|Handling|Beskrivelse|
|--- | ---|
|[ListProjects](connectors-create-api-projectonline.md#listprojects)|Viser en liste over projekter i dit project online-websted|
|[CreateProject](connectors-create-api-projectonline.md#createproject)|Opretter et nyt projekt i dit project online-websted|
|[CreateTask](connectors-create-api-projectonline.md#createtask)|Opretter en ny opgave i du projekt|
|[CreateResource](connectors-create-api-projectonline.md#createresource)|Opretter en virksomhedsressourcer i dit project online-websted|
|[ListTasks](connectors-create-api-projectonline.md#listtasks)|Viser de publicerede opgaver i et projekt|
|[CheckoutProject](connectors-create-api-projectonline.md#checkoutproject)|Tjekker et projekt på dit websted|
|[PublishProject](connectors-create-api-projectonline.md#publishproject)|Søge og publicere og eksisterende projekt på dit websted|
### <a name="projectonline-triggers"></a>ProjectOnline udløsere
Du kan lytte til disse hændelser:

|Udløser | Beskrivelse|
|--- | ---|
|Når der oprettes et nyt projekt|Udløser et flow, når der oprettes et nyt projekt|
|Når der oprettes en ny ressource|Udløser en ny flow, når der oprettes en ny ressource|
|Når der oprettes en ny opgave|Udløser et flow, når der oprettes en ny opgave|


## <a name="create-a-connection-to-projectonline"></a>Oprette en forbindelse til ProjectOnline
Hvis du vil oprette logik apps med ProjectOnline, skal du først oprette en **forbindelse** og derefter angive detaljerne for følgende egenskaber: 

|Egenskaben| Påkrævet|Beskrivelse|
| ---|---|---|
|Token|Ja|Angive ProjectOnline legitimationsoplysninger|

>[AZURE.INCLUDE [Steps to create a connection to ProjectOnline](../../includes/connectors-create-api-projectonline.md)]

>[AZURE.TIP] Du kan bruge denne forbindelse i andre logik apps.

## <a name="reference-for-projectonline"></a>Reference til ProjectOnline
Gælder for version: 1.0

## <a name="onnewproject"></a>OnNewProject
Når der oprettes et nyt projekt: udløser et flow, når der oprettes et nyt projekt 

```GET: /trigger/_api/ProjectData/Projects``` 

| Navn| Datatype|Påkrævet|Placeret i|Standardværdi|Beskrivelse|
| ---|---|---|---|---|---|
|siteUrl|streng|Ja|forespørgsel|ingen|Rod URL-adresse til websted dit projektwebsted (eksempel: https://sampletenant.sharepoint.com/teams/sampleteam)|

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


## <a name="onnewresource"></a>OnNewResource
Når der oprettes en ny ressource: udløser en ny flow, når der oprettes en ny ressource 

```GET: /trigger/_api/ProjectData/Resources``` 

| Navn| Datatype|Påkrævet|Placeret i|Standardværdi|Beskrivelse|
| ---|---|---|---|---|---|
|siteUrl|streng|Ja|forespørgsel|ingen|Rod URL-adresse til websted dit projektwebsted (eksempel: https://sampletenant.sharepoint.com/teams/sampleteam)|

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


## <a name="onnewtask"></a>OnNewTask
Når der oprettes en ny opgave: udløser et flow, når der oprettes en ny opgave 

```GET: /trigger/_api/ProjectData/Tasks``` 

| Navn| Datatype|Påkrævet|Placeret i|Standardværdi|Beskrivelse|
| ---|---|---|---|---|---|
|siteUrl|streng|Ja|forespørgsel|ingen|Rod URL-adresse til websted dit projektwebsted (eksempel: https://sampletenant.sharepoint.com/teams/sampleteam)|

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


## <a name="listprojects"></a>ListProjects
Liste over projekter: Viser en liste over projekter i dit project online-websted 

```GET: /_api/ProjectServer/Projects``` 

| Navn| Datatype|Påkrævet|Placeret i|Standardværdi|Beskrivelse|
| ---|---|---|---|---|---|
|siteUrl|streng|Ja|forespørgsel|ingen|Rod URL-adresse til websted dit projektwebsted (eksempel: https://sampletenant.sharepoint.com/teams/sampleteam)|

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


## <a name="createproject"></a>CreateProject
Opretter nyt projekt: opretter et nyt projekt i dit project online-websted 

```POST: /_api/ProjectServer/Projects``` 

| Navn| Datatype|Påkrævet|Placeret i|Standardværdi|Beskrivelse|
| ---|---|---|---|---|---|
|siteUrl|streng|Ja|forespørgsel|ingen|Rod URL-adresse til websted dit projektwebsted (eksempel: https://sampletenant.sharepoint.com/teams/sampleteam)|
|Proj| |Ja|brødteksten|ingen|Nyt projekt for at oprette|

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


## <a name="createtask"></a>CreateTask
Opretter ny opgave: opretter en ny opgave i du projekt 

```POST: /_api/ProjectServer/Projects('{project_id}')/Draft/Tasks/Add``` 

| Navn| Datatype|Påkrævet|Placeret i|Standardværdi|Beskrivelse|
| ---|---|---|---|---|---|
|siteUrl|streng|Ja|forespørgsel|ingen|Rod URL-adresse til websted dit projektwebsted (eksempel: https://sampletenant.sharepoint.com/teams/sampleteam)|
|PROJECT_ID|streng|Ja|sti|ingen|Entydigt ID for projektet tilføje opgaven til|
|opgave| |Ja|brødteksten|ingen|Ny opgave for at føje til projektet|

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


## <a name="createresource"></a>CreateResource
Oprette ny ressource: opretter en virksomhedsressourcer i dit project online-websted 

```POST: /_api/ProjectServer/EnterpriseResources``` 

| Navn| Datatype|Påkrævet|Placeret i|Standardværdi|Beskrivelse|
| ---|---|---|---|---|---|
|siteUrl|streng|Ja|forespørgsel|ingen|Rod URL-adresse til websted dit projektwebsted (eksempel: https://sampletenant.sharepoint.com/teams/sampleteam)|
|ressource| |Ja|brødteksten|ingen|Ny virksomhedsressource til at føje til projektet|

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


## <a name="listtasks"></a>ListTasks
Viser en liste over opgaver: Viser de publicerede opgaver i et projekt 

```GET: /_api/ProjectServer/Projects('{project_id}')/Tasks``` 

| Navn| Datatype|Påkrævet|Placeret i|Standardværdi|Beskrivelse|
| ---|---|---|---|---|---|
|siteUrl|streng|Ja|forespørgsel|ingen|Rod URL-adresse til websted dit projektwebsted (eksempel: https://sampletenant.sharepoint.com/teams/sampleteam)|
|PROJECT_ID|streng|Ja|sti|ingen|Entydigt ID for projektet til at hente opgaver|

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


## <a name="checkoutproject"></a>CheckoutProject
Udtjekning af et projekt: tjekker et projekt på dit websted 

```POST: /_api/ProjectServer/Projects('{project_id}')/checkOut``` 

| Navn| Datatype|Påkrævet|Placeret i|Standardværdi|Beskrivelse|
| ---|---|---|---|---|---|
|siteUrl|streng|Ja|forespørgsel|ingen|Rod URL-adresse til websted dit projektwebsted (eksempel: https://sampletenant.sharepoint.com/teams/sampleteam)|
|PROJECT_ID|streng|Ja|sti|ingen|Entydigt ID for projektet tilføje opgaven til|

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


## <a name="publishproject"></a>PublishProject
Tjek ind og publicere projekt: Tjek ind og udgive og eksisterende projekt på dit websted 

```POST: /_api/ProjectServer/Projects('{project_id}')/Draft/Publish(true)``` 

| Navn| Datatype|Påkrævet|Placeret i|Standardværdi|Beskrivelse|
| ---|---|---|---|---|---|
|siteUrl|streng|Ja|forespørgsel|ingen|Rod URL-adresse til websted dit projektwebsted (eksempel: https://sampletenant.sharepoint.com/teams/sampleteam)|
|PROJECT_ID|streng|Ja|sti|ingen|Entydigt ID for projektet, så Tjek ind|

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

### <a name="triggerprojectswrapper"></a>TriggerProjectsWrapper


| Egenskabsnavn | Datatype | Påkrævet |
|---|---|---|
|værdi|matrix|Nej |



### <a name="triggerproject"></a>TriggerProject


| Egenskabsnavn | Datatype | Påkrævet |
|---|---|---|
|Projektets startdato|streng|Nej |
|Slutdato for projekt|streng|Nej |
|Oprettelsesdato for projektet|streng|Nej |
|ProjectId|streng|Nej |
|Ændringsdato for projekt|streng|Nej |
|ProjectType|heltal|Nej |
|Projektnavn|streng|Nej |



### <a name="triggerresourceswrapper"></a>TriggerResourcesWrapper


| Egenskabsnavn | Datatype | Påkrævet |
|---|---|---|
|værdi|matrix|Nej |



### <a name="triggerresource"></a>TriggerResource


| Egenskabsnavn | Datatype | Påkrævet |
|---|---|---|
|ResourceId|streng|Nej |
|ResourceBaseCalendar|streng|Nej |
|ResourceBookingType|heltal|Nej |
|Ressource kan udjævne|Boolesk værdi|Nej |
|ResourceCostPerUse|tal|Nej |
|ResourceCreatedDate|streng|Nej |
|ResourceEarliestAvailableFrom|streng|Nej |
|ResourceEmail|streng|Nej |
|ResourceInitials|streng|Nej |
|ResourceIsActive|Boolesk værdi|Nej |
|ResourceIsGeneric|Boolesk værdi|Nej |
|ResourceLatestAvailableTo|streng|Nej |
|ResourceModifiedDate|streng|Nej |
|ResourceName|streng|Nej |
|ResourceStatsuName|streng|Nej |
|Ressourcetypen|heltal|Nej |
|TypeDescription|streng|Nej |
|TypeName|streng|Nej |



### <a name="triggertaskswrapper"></a>TriggerTasksWrapper


| Egenskabsnavn | Datatype | Påkrævet |
|---|---|---|
|værdi|matrix|Nej |



### <a name="triggertask"></a>TriggerTask


| Egenskabsnavn | Datatype | Påkrævet |
|---|---|---|
|ProjectId|streng|Nej |
|TaskId|streng|Nej |
|Projektnavn|streng|Nej |
|Opgavenavn|streng|Nej |
|Oprettelsesdato for opgave|streng|Nej |
|Ændringsdato for opgave|streng|Nej |
|Startdato for opgave|streng|Nej |
|Slutdato for opgaven|streng|Nej |
|TaskPriority|heltal|Nej |
|Opgaven er aktiv|Boolesk værdi|Nej |



### <a name="newproject"></a>NewProject


| Egenskabsnavn | Datatype | Påkrævet |
|---|---|---|
|Navn|streng|Ja |
|Beskrivelse|streng|Nej |
|Start|streng|Nej |



### <a name="newreource"></a>NewReource


| Egenskabsnavn | Datatype | Påkrævet |
|---|---|---|
|Navn|streng|Ja |
|IsBudget|Boolesk værdi|Nej |
|IsGeneric|Boolesk værdi|Nej |
|IsInactive|Boolesk værdi|Nej |



### <a name="project"></a>Project


| Egenskabsnavn | Datatype | Påkrævet |
|---|---|---|
|ApprovedStart|streng|Nej |
|ApprovedEnd|streng|Nej |
|CheckedOutDate|streng|Nej |
|CheckOutDescription|streng|Nej |
|CheckOutId|streng|Nej |
|CreatedDate|streng|Nej |
|Id|streng|Nej |
|IsCheckedOut|Boolesk værdi|Nej |
|LastPublishedDate|streng|Nej |
|LastSavedDate|streng|Nej |
|OptimizerDecision|heltal|Nej |
|PlannerDecision|heltal|Nej |
|ProjectType|heltal|Nej |
|Navn|streng|Nej |
|WinprojVersion|streng|Nej |



### <a name="projectswrapper"></a>ProjectsWrapper


| Egenskabsnavn | Datatype | Påkrævet |
|---|---|---|
|værdi|matrix|Nej |



### <a name="newtask"></a>Ny opgave


| Egenskabsnavn | Datatype | Påkrævet |
|---|---|---|
|parametre|ikke defineret|Ja |



### <a name="taskparameters"></a>TaskParameters


| Egenskabsnavn | Datatype | Påkrævet |
|---|---|---|
|Navn|streng|Ja |
|Noter|streng|Nej |
|Start|streng|Nej |
|Varighed|streng|Nej |



### <a name="enterpriseresource"></a>EnterpriseResource


| Egenskabsnavn | Datatype | Påkrævet |
|---|---|---|
|CanLevel|Boolesk værdi|Nej |
|Kode|streng|Nej |
|CostAccrual|heltal|Nej |
|CostCenter|streng|Nej |
|Oprettet|streng|Nej |
|DefaultBookingType|heltal|Nej |
|Mail|streng|Nej |
|ExternalId|streng|Nej |
|Gruppe|streng|Nej |
|HireDate|streng|Nej |
|Id|streng|Nej |
|Initialer|streng|Nej |
|ErAktiv|Boolesk værdi|Nej |
|IsBudget|Boolesk værdi|Nej |
|IsCheckedOut|Boolesk værdi|Nej |
|IsGeneric|Boolesk værdi|Nej |
|IsTeam|Boolesk værdi|Nej |
|MaterialLabel|streng|Nej |
|Ændret|streng|Nej |
|Navn|streng|Nej |
|Fonetik|streng|Nej |
|Ressourcetypen|heltal|Nej |
|TerminationDate|streng|Nej |



### <a name="taskswrapper"></a>TasksWrapper


| Egenskabsnavn | Datatype | Påkrævet |
|---|---|---|
|værdi|matrix|Nej |



### <a name="task"></a>Opgave


| Egenskabsnavn | Datatype | Påkrævet |
|---|---|---|
|Oprettet|streng|Nej |
|Ændret|streng|Nej |
|Start|streng|Nej |
|Udfør|streng|Nej |
|Navn|streng|Nej |
|Id|streng|Nej |
|Prioritet|heltal|Nej |
|ProcentFuldført|heltal|Nej |
|Noter|streng|Nej |
|Kontakt|streng|Nej |


## <a name="next-steps"></a>Næste trin
[Oprette en logik app](../app-service-logic/app-service-logic-create-a-logic-app.md)