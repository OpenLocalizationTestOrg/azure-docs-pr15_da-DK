<properties
pageTitle="GitHub | Microsoft Azure"
description="Opret logik apps med Azure App service. GitHub er et webbaseret ciffer lager værtstjeneste. Den indeholder alle de fordelte revision kontrolelement og kilde kode management (SCM) funktioner ciffer samt tilføje sin egen funktioner."
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

# <a name="get-started-with-the-github-connector"></a>Komme i gang med GitHub forbindelsen

GitHub er et webbaseret ciffer lager værtstjeneste. Den indeholder alle de fordelte revision kontrolelement og kilde kode management (SCM) funktioner ciffer samt tilføje sin egen funktioner.

>[AZURE.NOTE] Denne version i denne artikel gælder for logik apps 2015-08-01-skema prøveversionen. 

Du kan komme i gang ved at oprette en logik app nu, i [oprette en logik app](../app-service-logic/app-service-logic-create-a-logic-app.md).

## <a name="triggers-and-actions"></a>Udløsere og handlinger

GitHub forbindelsen kan bruges som en handling den har trigger(s). Alle forbindelser understøtter data i JSON og XML-formater. 

 GitHub forbindelsen har følgende handling(er) og/eller trigger(s) tilgængelige:

### <a name="github-actions"></a>GitHub handlinger
Du kan udføre disse handling(er):

|Handling|Beskrivelse|
|--- | ---|
|[CreateIssue](connectors-create-api-github.md#createissue)|Opretter et problem|
### <a name="github-triggers"></a>GitHub udløsere
Du kan lytte til disse hændelser:

|Udløser | Beskrivelse|
|--- | ---|
|Når et problem, der åbnes|Et problem, der er åbnet|
|Når et problem, der er lukket|Et problem, der er lukket|
|Når et problem, der er tildelt|Et problem, der er tildelt|


## <a name="create-a-connection-to-github"></a>Oprette en forbindelse til GitHub
Hvis du vil oprette logik apps med GitHub, skal du først oprette en **forbindelse** og derefter angive detaljerne for følgende egenskaber: 

|Egenskaben| Påkrævet|Beskrivelse|
| ---|---|---|
|Token|Ja|Angive GitHub legitimationsoplysninger|
Når du opretter forbindelsen, kan du bruge det til at udføre handlingerne, og lyt til de udløsere, der er beskrevet i denne artikel. 

>[AZURE.INCLUDE [Steps to create a connection to GitHub](../../includes/connectors-create-api-github.md)]

>[AZURE.TIP] Du kan bruge denne forbindelse i andre logik apps.

## <a name="reference-for-github"></a>Reference til GitHub
Gælder for version: 1.0

## <a name="createissue"></a>CreateIssue
Oprette et problem: opretter et problem 

```POST: /repos/{repositoryOwner}/{repositoryName}/issues``` 

| Navn| Datatype|Påkrævet|Placeret i|Standardværdi|Beskrivelse|
| ---|---|---|---|---|---|
|repositoryOwner|streng|Ja|sti|ingen|Lager ejer|
|repositoryName|streng|Ja|sti|ingen|Lager navn|
|issueBasicDetails| |Ja|brødteksten|ingen|Oplysninger om problem|

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


## <a name="issueopened"></a>IssueOpened
Når et problem, der åbnes: et problem, der er åbnet 

```GET: /trigger/issueOpened``` 

Der er ingen parametre til dette opkald
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


## <a name="issueclosed"></a>IssueClosed
Når et problem, der er lukket: et problem, der er lukket 

```GET: /trigger/issueClosed``` 

Der er ingen parametre til dette opkald
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


## <a name="issueassigned"></a>IssueAssigned
Når et problem, der er tildelt: et problem, der er tildelt 

```GET: /trigger/issueAssigned``` 

Der er ingen parametre til dette opkald
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

### <a name="issuebasicdetailsmodel"></a>IssueBasicDetailsModel


| Egenskabsnavn | Datatype | Påkrævet |
|---|---|---|
|titel|streng|Ja |
|brødteksten|streng|Ja |
|modtager|streng|Ja |



### <a name="issuedetailsmodel"></a>IssueDetailsModel


| Egenskabsnavn | Datatype | Påkrævet |
|---|---|---|
|titel|streng|Ja |
|brødteksten|streng|Ja |
|modtager|streng|Ja |
|tal|streng|Nej |
|stat|streng|Nej |
|created_at|streng|Nej |
|repository_url|streng|Nej |


## <a name="next-steps"></a>Næste trin
[Oprette en logik app](../app-service-logic/app-service-logic-create-a-logic-app.md)