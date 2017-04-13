<properties
    pageTitle="Tilføje Office 365-brugere forbindelsen i logik Apps | Microsoft Azure"
    description="Oversigt over Office 365-brugere forbindelse med REST-API parametre"
    services=""    
    documentationCenter=""     
    authors="msftman"    
    manager="erikre"    
    editor="" 
    tags="connectors" />

<tags
ms.service="multiple"
ms.devlang="na"
ms.topic="article"
ms.tgt_pltfrm="na"
ms.workload="integration"
ms.date="08/18/2016"
ms.author="deonhe"/>

# <a name="get-started-with-the-office-365-users-connector"></a>Introduktion til Office 365-brugere forbindelsen

Oprette forbindelse til Office 365-brugere at få profiler, Søg brugere og meget mere. 

>[AZURE.NOTE] Denne version i denne artikel gælder for logik apps 2015-08-01-skema prøveversionen.

Med Office 365-brugere kan du:

- Opbyg din virksomhed flow baseret på de data, du får fra Office 365-brugere. 
- Brug handlinger, får direkte rapporter, få en anden persons brugerprofil og meget mere. Disse handlinger får du respons, og foretag derefter output tilgængelig for andre handlinger. For eksempel få en persons direkte rapporter, og derefter få disse oplysninger, og opdatere en SQL Azure-database. 

For at tilføje en handling i logik apps skal du se [oprette en logik app](../app-service-logic/app-service-logic-create-a-logic-app.md).

## <a name="triggers-and-actions"></a>Udløsere og handlinger

Office 365-brugere forbindelsen har de følgende handlinger, der er tilgængelige. Der findes ingen udløsere.

| Udløsere | Handlinger|
| --- | --- |
|Ingen | <ul><li>Få manager</li><li>Få min profil</li><li>Få direkte rapporter</li><li>Få brugerprofil</li><li>Søg efter brugere</li></ul>|

Alle forbindelser understøtter data i JSON og XML-formater. 


## <a name="create-a-connection-to-office-365-users"></a>Oprette en forbindelse til Office 365-brugere

Når du tilføjer denne forbindelse til din logik apps, skal du logge på din konto på Office 365-brugere og tillade logik apps til at oprette forbindelse til din konto.

>[AZURE.INCLUDE [Steps to create a connection to Office 365 Users](../../includes/connectors-create-api-office365users.md)]

Når du opretter forbindelsen, angiver du egenskaber for Office 365-brugere, som bruger-ID. **REST-API reference** i dette emne beskrives disse egenskaber.

>[AZURE.TIP] Du kan bruge denne samme forbindelse i Office 365-brugere i andre logik apps.


## <a name="office-365-users-rest-api-reference"></a>Reference til Office 365-brugere REST-API
Gælder for version: 1.0.

### <a name="get-my-profile"></a>Få min profil 
Henter profilen, så den aktuelle bruger.  
```GET: /users/me``` 

Der er ingen parametre til dette opkald.

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


### <a name="get-user-profile"></a>Få brugerprofil 
Henter en bestemt brugerprofil.  
```GET: /users/{userId}``` 

| Navn| Datatype|Påkrævet|Placeret i|Standardværdi|Beskrivelse|
| ---|---|---|---|---|---|
|bruger-id|streng|Ja|sti|ingen|Bruger vigtigste navn eller mail-id|

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


### <a name="get-manager"></a>Få manager 
Henter brugerprofil for leder for den angivne bruger.  
```GET: /users/{userId}/manager``` 

| Navn| Datatype|Påkrævet|Placeret i|Standardværdi|Beskrivelse|
| ---|---|---|---|---|---|
|bruger-id|streng|Ja|sti|ingen|Bruger vigtigste navn eller mail-id|

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



### <a name="get-direct-reports"></a>Få direkte rapporter 
Få direkte rapporter.  
```GET: /users/{userId}/directReports``` 

| Navn| Datatype|Påkrævet|Placeret i|Standardværdi|Beskrivelse|
| ---|---|---|---|---|---|
|bruger-id|streng|Ja|sti|ingen|Bruger vigtigste navn eller mail-id|

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



### <a name="search-for-users"></a>Søg efter brugere 
Henter søgeresultater brugerprofiler.  
```GET: /users``` 

| Navn| Datatype|Påkrævet|Placeret i|Standardværdi|Beskrivelse|
| ---|---|---|---|---|---|
|searchTerm|streng|Nej|forespørgsel|ingen|Søge streng (gælder for: få vist navn, Fornavn, efternavn, mail, mail kaldenavne og bruger hovednavn)|

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



## <a name="object-definitions"></a>Objektdefinitioner

#### <a name="user-user-model-class"></a>Bruger: Bruger model klasse

|Egenskabsnavn | Datatype |Påkrævet
|---|---|---|
|Vist navn|streng|Nej|
|GivenName|streng|Nej|
|Efternavn|streng|Nej|
|Mail|streng|Nej|
|MailNickname|streng|Nej|
|TelephoneNumber|streng|Nej|
|AccountEnabled|Boolesk værdi|Nej|
|Id|streng|Ja
|UserPrincipalName|streng|Nej|
|Afdeling|streng|Nej|
|Stilling|streng|Nej|
|mobilePhone|streng|Nej|


## <a name="next-steps"></a>Næste trin

[Opret en logik app](../app-service-logic/app-service-logic-create-a-logic-app.md).

Gå tilbage til [API'er liste](apis-list.md).

<!--References-->
[5]: https://portal.azure.com
[7]: ./media/connectors-create-api-office365-users/aad-tenant-applications.PNG
[8]: ./media/connectors-create-api-office365-users/aad-tenant-applications-add-appinfo.PNG
[9]: ./media/connectors-create-api-office365-users/aad-tenant-applications-add-app-properties.PNG
[10]: ./media/connectors-create-api-office365-users/contoso-aad-app.PNG
[11]: ./media/connectors-create-api-office365-users/contoso-aad-app-configure.PNG
