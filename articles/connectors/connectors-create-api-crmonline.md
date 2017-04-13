<properties
    pageTitle="Føje Dynamics CRM Online-forbindelse til dine logik Apps | Microsoft Azure"
    description="Opret logik apps med Azure App service. Dynamics CRM Online-forbindelsesprovider indeholder en API til at arbejde med objekter på Dynamics CRM Online."
    services="logic-apps"    
    documentationCenter=""     
    authors="MandiOhlinger"    
    manager="erikre"    
    editor="" 
    tags="connectors" />

<tags
ms.service="logic-apps"
ms.devlang="na"
ms.topic="article"
ms.tgt_pltfrm="na"
ms.workload="integration"
ms.date="08/15/2016"
ms.author="mandia"/>

# <a name="get-started-with-the-dynamics-crm-online-connector"></a>Introduktion til Dynamics CRM Online-forbindelse
Oprette forbindelse til Dynamics CRM Online til at oprette en ny post, opdatere et element, og meget mere. Med CRM Online kan du:

- Opbyg din virksomhed rutediagram, der er baseret på de data, du får fra CRM Online. 
- Brug handlinger, der sletter en post, få enheder og meget mere. Disse handlinger får du respons, og foretag derefter output tilgængelig for andre handlinger. Når et element er opdateret i CRM, kan du sende en mail via Office 365.

Dette emne beskrives, hvordan du bruger Dynamics CRM Online forbindelsen i en logik app og også viser de udløsere og handlinger.

>[AZURE.NOTE] Denne version i denne artikel gælder for logik Apps generelt tilgængelig (GA).

Hvis du vil vide mere om logik Apps, skal du se [Hvad er logik apps](../app-service-logic/app-service-logic-what-are-logic-apps.md) og [oprette en logik app](../app-service-logic/app-service-logic-create-a-logic-app.md).

## <a name="connect-to-dynamics-crm-online"></a>Opret forbindelse til Dynamics CRM Online

Før din logik app kan få adgang til alle tjenester, oprette du først en *forbindelse* til tjenesten. En forbindelse indeholder forbindelsen mellem en logik app og en anden tjeneste. For eksempel for at oprette forbindelse til Dynamics, skal du først en Dynamics CRM Online *forbindelse*. For at oprette en forbindelse, skal du angive de legitimationsoplysninger, som du normalt bruger til at få adgang til tjenesten, du vil oprette forbindelse til. Angiv så legitimationsoplysningerne med Dynamics, til kontoen Dynamics CRM Online til at oprette forbindelsen.


### <a name="create-the-connection"></a>Oprette forbindelse

>[AZURE.INCLUDE [Steps to create a connection to Dynamics CRM Online Connection Provider](../../includes/connectors-create-api-crmonline.md)]

## <a name="use-a-trigger"></a>Bruge en udløser

En udløser er en begivenhed, der kan bruges til at starte arbejdsprocessen defineret i en logik app. Udløsere afstemning"" tjenesten i et interval og hyppighed, som du ønsker. [Få mere at vide om udløsere](../app-service-logic/app-service-logic-what-are-logic-apps.md#logic-app-concepts).

1. Skriv "dynamics" i appen logik til at få en liste over udløserne:  

    ![](./media/connectors-create-api-crmonline/dynamics-triggers.png)

2. Vælg **Dynamics CRM Online - når der oprettes en post**. Hvis der findes allerede en forbindelse, skal du vælge en organisation og enhed fra på rullelisten.

    ![](./media/connectors-create-api-crmonline/select-organization.png)

    Hvis du bliver bedt om at logge på, skal du angive tegnet i detaljer for at oprette forbindelsen. [Oprette forbindelsen](connectors-create-api-crmonline.md#create-the-connection) i dette emne beskrives, hvordan. 

    > [AZURE.NOTE] I dette eksempel køres logik app, når der oprettes en post. For at se resultatet af denne udløser skal du tilføje endnu en handling, der sender dig en mail. For eksempel tilføje handlingen Office 365 *sende en mail* , mails du, når den nye post er blevet tilføjet. 

3. Vælg knappen **Rediger** , og angiv **hyppighed** og **Interval** . Eksempelvis hvis du vil udløse til afstemning hver 15 minutter viser derefter Angiv **hyppighed** til **minut**og indstille **Interval** til **15**. 

    ![](./media/connectors-create-api-crmonline/edit-properties.png)

4. **Gemme** dine ændringer (øverste venstre hjørne af værktøjslinjen). Din logik app er gemt og kan aktiveres automatisk.


## <a name="use-an-action"></a>Bruge en handling

En handling er en handling, der er foretaget af den arbejdsproces, der er defineret i en logik app. [Lær mere om handlinger](../app-service-logic/app-service-logic-what-are-logic-apps.md#logic-app-concepts).

1. Vælg plustegnet. Du kan se flere valgmuligheder: **Tilføj en handling**, **Tilføj en betingelse**eller en af **flere** indstillinger.

    ![](./media/connectors-create-api-crmonline/add-action.png)

2. Vælg **Tilføj en handling**.

3. Skriv "dynamics" i tekstfeltet, til at få en liste over alle de tilgængelige handlinger.

    ![](./media/connectors-create-api-crmonline/dynamics-actions.png)

4. Vælg **Dynamics CRM Online - opdaterer en post**i vores eksempel. Hvis der findes allerede en forbindelse, vælg derefter det **Organisationsnavn**, **Objektnavn**og andre egenskaber:  

    ![](./media/connectors-create-api-crmonline/sample-action.png)

    Hvis du bliver bedt om forbindelsesoplysningerne, skal du angive oplysninger til at oprette forbindelsen. [Oprette forbindelsen](connectors-create-api-crmonline.md#create-the-connection) i dette emne beskrives disse egenskaber. 

    > [AZURE.NOTE] I dette eksempel skal opdaterer vi en eksisterende post i CRM Online. Du kan bruge output fra en anden udløser til at opdatere posten. For eksempel tilføje SharePoint, *Når et eksisterende element ændres* udløser. Tilføj derefter handlingen CRM Online *opdaterer en post* , der bruger SharePoint-felter til at opdatere den eksisterende post i CRM Online. 

5. **Gemme** dine ændringer (øverste venstre hjørne af værktøjslinjen). Din logik app er gemt og kan aktiveres automatisk.


## <a name="technical-details"></a>Tekniske detaljer

## <a name="triggers"></a>Udløsere

|Udløser | Beskrivelse|
|--- | ---|
|[Når der oprettes en post](connectors-create-api-crmonline.md#when-a-record-is-created)|Aktiverer et flow, når der oprettes et objekt i CRM.|
|[Når en post er opdateret](connectors-create-api-crmonline.md#when-a-record-is-updated)|Aktiverer et flow, når et objekt er ændret i CRM.|
|[Når en post er blevet slettet](connectors-create-api-crmonline.md#when-a-record-is-deleted)|Aktiverer et flow, når et objekt er slettet i CRM.|


## <a name="actions"></a>Handlinger

|Handling|Beskrivelse|
|--- | ---|
|[Liste over poster](connectors-create-api-crmonline.md#list-records)|Denne handling bliver posterne for et objekt.|
|[Oprette en ny post](connectors-create-api-crmonline.md#create-a-new-record)|Denne handling opretter en ny post af et objekt.|
|[Få post](connectors-create-api-crmonline.md#get-record)|Denne handling får den angivne post for et objekt.|
|[Slette en post](connectors-create-api-crmonline.md#delete-a-record)|Denne handling sletter en post fra en enhed af websteder.|
|[Opdatere en post](connectors-create-api-crmonline.md#update-a-record)|Denne handling opdaterer en eksisterende post for et objekt.|

### <a name="trigger-and-action-details"></a>Oplysninger om udløser og handling

I dette afsnit, skal du se specifikke oplysninger om hver udløser og en handling, herunder eventuelle nødvendig eller valgfri input egenskaber og noget tilsvarende output, der er knyttet til forbindelsen.

#### <a name="when-a-record-is-created"></a>Når der oprettes en post
Aktiverer et flow, når der oprettes et objekt i CRM. 

|Egenskabsnavn| Vist navn|Beskrivelse|
| ---|---|---|
|datasæt *|Organisationsnavn|Navnet på CRM-organisationen som Contoso|
|tabel *|Objektnavn|Navnet på objektet|
|$skip|Spring over Tæl|Antallet af poster at springe (standard = 0)|
|$top|Maksimale få Tæl|Maksimale antal poster, der kan få (standard = 256)|
|$filter|Filtrere forespørgsel|En forespørgsel til ODATA-filter til at begrænse de poster, der returneres|
|$orderby|Sortér efter|En ODATA SorterEfter forespørgsel for at angive rækkefølgen af poster|

En stjerne (*) betyder, at egenskaben er påkrævet.

##### <a name="output-details"></a>Output detaljer
ItemsList

| Egenskabsnavn | Datatype |
|---|---|
|værdi|matrix|


#### <a name="when-a-record-is-updated"></a>Når en post er opdateret
Aktiverer et flow, når et objekt er ændret i CRM. 

|Egenskabsnavn| Vist navn|Beskrivelse|
| ---|---|---|
|datasæt *|Organisationsnavn|Navnet på CRM-organisationen som Contoso|
|tabel *|Objektnavn|Navnet på objektet|
|$skip|Spring over Tæl|Antallet af poster at springe (standard = 0)|
|$top|Maksimale få Tæl|Maksimale antal poster, der kan få (standard = 256)|
|$filter|Filtrere forespørgsel|En forespørgsel til ODATA-filter til at begrænse de poster, der returneres|
|$orderby|Sortér efter|En ODATA SorterEfter forespørgsel for at angive rækkefølgen af poster|

En stjerne (*) betyder, at egenskaben er påkrævet.

##### <a name="output-details"></a>Output detaljer
ItemsList

| Egenskabsnavn | Datatype |
|---|---|
|værdi|matrix|


#### <a name="when-a-record-is-deleted"></a>Når en post er blevet slettet
Aktiverer et flow, når et objekt er slettet i CRM. 

|Egenskabsnavn| Vist navn|Beskrivelse|
| ---|---|---|
|datasæt *|Organisationsnavn|Navnet på CRM-organisationen som Contoso|
|tabel *|Objektnavn|Navnet på objektet|
|$skip|Spring over Tæl|Antallet af poster at springe (standard = 0)|
|$top|Maksimale få Tæl|Maksimale antal poster, der kan få (standard = 256)|
|$filter|Filtrere forespørgsel|En forespørgsel til ODATA-filter til at begrænse de poster, der returneres|
|$orderby|Sortér efter|En ODATA SorterEfter forespørgsel for at angive rækkefølgen af poster|

En stjerne (*) betyder, at egenskaben er påkrævet.

##### <a name="output-details"></a>Output detaljer
ItemsList

| Egenskabsnavn | Datatype |
|---|---|
|værdi|matrix|


#### <a name="list-records"></a>Liste over poster
Denne handling bliver posterne for et objekt. 

|Egenskabsnavn| Vist navn|Beskrivelse|
| ---|---|---|
|datasæt *|Organisationsnavn|Navnet på CRM-organisationen som Contoso|
|tabel *|Objektnavn|Navnet på objektet|
|$skip|Spring over Tæl|Antallet af poster at springe (standard = 0)|
|$top|Maksimale få Tæl|Maksimale antal poster, der kan få (standard = 256)|
|$filter|Filtrere forespørgsel|En forespørgsel til ODATA-filter til at begrænse de poster, der returneres|
|$orderby|Sortér efter|En ODATA SorterEfter forespørgsel for at angive rækkefølgen af poster|

En stjerne (*) betyder, at egenskaben er påkrævet.

##### <a name="output-details"></a>Output detaljer
ItemsList

| Egenskabsnavn | Datatype |
|---|---|
|værdi|matrix|


#### <a name="create-a-new-record"></a>Oprette en ny post
Denne handling opretter en ny post af et objekt. 

|Egenskabsnavn| Vist navn|Beskrivelse|
| ---|---|---|
|datasæt *|Organisationsnavn|Navnet på CRM-organisationen som Contoso|
|tabel *|Objektnavn|Navnet på objektet|

En stjerne (*) betyder, at egenskaben er påkrævet.

##### <a name="output-details"></a>Output detaljer
Ingen.


#### <a name="get-record"></a>Få post
Denne handling får den angivne post for et objekt. 

|Egenskabsnavn| Vist navn|Beskrivelse|
| ---|---|---|
|datasæt *|Organisationsnavn|Navnet på CRM-organisationen som Contoso|
|tabel *|Objektnavn|Navnet på objektet|
|id *|Vare-id|Angiv id'et for posten|

En stjerne (*) betyder, at egenskaben er påkrævet.

##### <a name="output-details"></a>Output detaljer
Ingen.


#### <a name="delete-a-record"></a>Slette en post
Denne handling sletter en post fra en enhed af websteder. 

|Egenskabsnavn| Vist navn|Beskrivelse|
| ---|---|---|
|datasæt *|Organisationsnavn|Navnet på CRM-organisationen som Contoso|
|tabel *|Objektnavn|Navnet på objektet|
|id *|Vare-id|Angiv id'et for posten|

En stjerne (*) betyder, at egenskaben er påkrævet.


#### <a name="update-a-record"></a>Opdatere en post
Denne handling opdaterer en eksisterende post for et objekt. 

|Egenskabsnavn| Vist navn|Beskrivelse|
| ---|---|---|
|datasæt *|Organisationsnavn|Navnet på CRM-organisationen som Contoso|
|tabel *|Objektnavn|Navnet på objektet|
|id *|Optage id|Angiv id'et for posten|

En stjerne (*) betyder, at egenskaben er påkrævet.

##### <a name="output-details"></a>Output detaljer
Ingen.


## <a name="http-responses"></a>HTTP-svar

De handlinger og udløsere kan returnere en eller flere af følgende HTTP statuskoder: 

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

[Opret en logik app](../app-service-logic/app-service-logic-create-a-logic-app.md). Udforske andre tilgængelige forbindelserne i logik Apps i vores [API'er liste](apis-list.md).

