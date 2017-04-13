<properties
pageTitle="SMTP | Microsoft Azure"
description="Opret logik apps med Azure App service. Oprette forbindelse til SMTP til at sende mail."
services="logic-apps"   
documentationCenter=".net,nodejs,java"  
authors="msftman"   
manager="erikre"    
editor=""
tags="connectors" />

<tags
ms.service="app-service-logic"
ms.devlang="multiple"
ms.topic="article"
ms.tgt_pltfrm="na"
ms.workload="integration"
ms.date="07/15/2016"
ms.author="deonhe"/>

# <a name="get-started-with-the-smtp-connector"></a>Komme i gang med SMTP-forbindelse

Oprette forbindelse til SMTP til at sende mail.

Hvis du vil bruge [en forbindelse](./apis-list.md), skal du først oprette en logik app. Du kan komme i gang ved at [oprette en logik app nu](../app-service-logic/app-service-logic-create-a-logic-app.md).

## <a name="connect-to-smtp"></a>Oprette forbindelse til SMTP

Før din logik app kan få adgang til alle tjenester, skal du først oprette en *forbindelse* til tjenesten. En [forbindelse](./connectors-overview.md) indeholder forbindelsen mellem en logik app og en anden tjeneste. For eksempel for at oprette forbindelse til SMTP, skal du først en SMTP- *forbindelse*. Hvis du vil oprette en forbindelse, skal du angive de legitimationsoplysninger, som du normalt bruger til at få adgang til tjenesten, du vil oprette forbindelse til. I eksemplet SMTP-ville du tilfældet, skal legitimationsoplysningerne til forbindelsesnavn, SMTP-serveradresse og bruger logonoplysninger for at oprette forbindelse til SMTP. [Få flere oplysninger om forbindelser]()  

### <a name="create-a-connection-to-smtp"></a>Oprette en forbindelse til SMTP

>[AZURE.INCLUDE [Steps to create a connection to SMTP](../../includes/connectors-create-api-smtp.md)]

## <a name="use-an-smtp-trigger"></a>Bruge en SMTP-udløser

En udløser er en begivenhed, der kan bruges til at starte arbejdsprocessen defineret i en logik app. [Få mere at vide om udløsere](../app-service-logic/app-service-logic-what-are-logic-apps.md#logic-app-concepts).

Fordi SMTP ikke har en udløser af sig selv, i dette eksempel skal vi bruge **Salesforce - når der oprettes et objekt** udløser. Denne udløser aktiveres, når der oprettes et nyt objekt i Salesforce. I dette eksempel skal vil vi konfigurere det så hver gang der oprettes et nyt kundeemne i Salesforce, forekommer handlingen *sende mail* via SMTP-forbindelse med en meddelelse om det nye kundeemne, der oprettes.

1. Angive *salesforce* i søgefeltet i logik apps designer og vælg derefter **Salesforce - når der oprettes et objekt** udløser.  
 ![](../../includes/media/connectors-create-api-salesforce/trigger-1.png)  

2. **Når der oprettes et objekt** kontrolelementet til lydafspilning vises.
 ![](../../includes/media/connectors-create-api-salesforce/trigger-2.png)  

3. Vælg den **Objekttype** og vælg derefter *kundeemne* på listen over objekter. I dette trin du angiver, at du opretter en udløser, der giver besked din logik app, når der oprettes et nyt kundeemne i Salesforce.  
 ![](../../includes/media/connectors-create-api-salesforce/trigger3.png)  

4. Udløseren er blevet oprettet.  
 ![](../../includes/media/connectors-create-api-salesforce/trigger-4.png)  

## <a name="use-an-smtp-action"></a>Bruge en SMTP-handling

En handling er en handling, der er foretaget af den arbejdsproces, der er defineret i en logik app. [Lær mere om handlinger](../app-service-logic/app-service-logic-what-are-logic-apps.md#logic-app-concepts).

Nu, hvor udløseren er blevet tilføjet, skal du følge disse trin for at føje en SMTP-handling, der sker, når der oprettes et nyt kundeemne i Salesforce.

1. Vælg **+ nyt trin** at føje den handling, du vil have skal udføre, når der oprettes et nyt kundeemne.  
 ![](../../includes/media/connectors-create-api-salesforce/trigger4.png)  

2. Vælg **Tilføj en handling**. Dette åbner søgefeltet, hvor du kan søge efter noget, du vil foretage.  
 ![](../../includes/media/connectors-create-api-smtp/using-smtp-action-2.png)  

3. Angiv *smtp* til at søge efter handlinger, der er relateret til SMTP.  

4. Vælg **SMTP - Send mail** som handlingen skal udføre, når der oprettes nye kundeemne. Handling kontrolelement blokering åbnes. Du er nødt til at oprette din SMTP-forbindelse i Forespørgselsdesigner blokering, hvis du ikke har gjort det tidligere.  
 ![](../../includes/media/connectors-create-api-smtp/smtp-2.png)    

5. Indtast dine ønskede mailoplysninger i blokken **SMTP - Send mail** .  
 ![](../../includes/media/connectors-create-api-smtp/using-smtp-action-4.PNG)  

6. Gemme dit arbejde for at aktivere arbejdsprocessen.  

## <a name="technical-details"></a>Tekniske detaljer

Her er nogle oplysninger om udløsere, handlinger og svar, der understøtter denne forbindelse:

## <a name="smtp-triggers"></a>SMTP-udløsere

SMTP har ingen udløsere. 

## <a name="smtp-actions"></a>SMTP-handlinger

SMTP har følgende handlinger:


|Handling|Beskrivelse|
|--- | ---|
|[Sende mail](connectors-create-api-smtp.md#send-email)|Denne handling sender en mail til en eller flere modtagere.|

### <a name="action-details"></a>Handlingsdetaljer

Her er detaljerne for handlingen for denne forbindelse, sammen med dens svar:


### <a name="send-email"></a>Sende mail
Denne handling sender en mail til en eller flere modtagere. 


|Egenskabsnavn| Vist navn|Beskrivelse|
| ---|---|---|
|Til|Til|Angive e-mail-adresser adskilt af komma somrecipient1@domain.com;recipient2@domain.com|
|CC|Cc|Angive e-mail-adresser adskilt af komma somrecipient1@domain.com;recipient2@domain.com|
|Emne|Emne|E-mail-emne|
|Brødteksten|Brødteksten|Mail brødtekst|
|Fra|Fra|Mailadressen på afsenderen somsender@domain.com|
|IsHtml|Html er|Send e-mailen som HTML (sand/falsk)|
|Bcc|Bcc|Angive e-mail-adresser adskilt af komma somrecipient1@domain.com;recipient2@domain.com|
|Prioritet|Prioritet|Vigtigheden af e-mailen (høj, Normal eller lav)|
|ContentData|Vedhæftede filer indhold Data|Indhold data (base64 kodet til streams og as-er til streng)|
|ContentType|Indholdstype for vedhæftede filer|Indholdstype|
|ContentTransferEncoding|Vedhæftede filer indhold filoverførsel kodning|Indhold filoverførsel kodning (base64 eller ingen)|
|Filnavn|Vedhæftede filer filnavn|Filnavn|
|ContentId|Indholds-ID'ET vedhæftede filer|Indholds-id'et|

En * angiver, at en egenskab er påkrævet


## <a name="http-responses"></a>HTTP-svar

De handlinger og udløsere ovenfor kan returnere en eller flere af følgende HTTP statuskoder: 

|Navn|Beskrivelse|
|---|---|
|200|Ok|
|202|Accepteret|
|400|Forkert anmodning|
|401|Uautoriseret|
|403|Forbudt|
|404|Blev ikke fundet|
|500|Intern serverfejl. Der opstod ukendt fejl.|
|standard|Mislykkedes.|

## <a name="next-steps"></a>Næste trin
[Oprette en logik app](../app-service-logic/app-service-logic-create-a-logic-app.md)