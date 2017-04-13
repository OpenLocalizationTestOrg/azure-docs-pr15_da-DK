<properties 
    pageTitle="Hvordan du føjer handlinger til en API i Azure API Management | Microsoft Azure" 
    description="Lær at føje handlinger til en API i Azure API Management." 
    services="api-management" 
    documentationCenter="" 
    authors="steved0x" 
    manager="erikre" 
    editor=""/>

<tags 
    ms.service="api-management" 
    ms.workload="mobile" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="10/25/2016" 
    ms.author="sdanie"/>

# <a name="how-to-add-operations-to-an-api-in-azure-api-management"></a>Hvordan du føjer handlinger til en API i Azure API Management

Før en API i API Management kan bruges, tilføjes operationer. Denne vejledning viser, hvordan du tilføjer og konfigurerer forskellige typer handlinger til en API API administration.

## <a name="add-operation"> </a>Tilføje en handling

Handlinger tilføjes og konfigureres til en API i publisher-portalen. Klik på **Administrer** i portalen Azure klassisk for din API Management-tjenesten for at få adgang til portalen publisher.

![Publisher-portalen][api-management-management-console]

>Hvis du endnu ikke har oprettet en API Management service-forekomst, kan du se [oprette en API Management service forekomst][] i [Introduktion til Azure API Management][] selvstudiet.

Vælg den ønskede API i publisher-portalen, og vælg derefter fanen **Handlinger** . 

![Handlinger][api-management-operations]

Klik på **Tilføj handling** for at tilføje en ny handling. **Handlingen ny** vises, og fanen **signatur** vælges som standard.

![Tilføje handling][api-management-add-operation]

Angiv **HTTP verber** ved at vælge på rullelisten.

![HTTP-metode][api-management-http-method]

<a name="url-template"></a>

Definere skabelonen URL-adresse ved at skrive i en URL-adresse-fragment bestående af en eller flere URL-stisegmenter og nul eller flere parametre for forespørgselsstreng. Skabelonen URL-adresse, vedhæftes API, grundlæggende URL-adressen identificerer en enkelt HTTP-handling. Det kan indeholde et eller flere navngivne variable dele, der er identificeret med klammeparenteser. Følgende variable dele kaldes Skabelonparametre og tildeles dynamisk værdier, der er hentet fra URL-adressen til den anmodning, når anmodningen behandles af API Management platform.

![URL-adresse til skabelon][api-management-url-template]

<a name="rewrite-url-template"></a>

Hvis du vil, kan du angive **omskrive URL-adressen skabelon**. Dette kan du bruge skabelonen standard URL-adressen til at behandle indgående anmodninger på den front end under kald back-end via en konverteret URL-adresse efter skabelonen omskrivning. Skabelonparametre fra URL-adresse til skabelon skal bruges i skabelonen omskrivning. I følgende eksempel viser, hvordan indholdstype kodet som sti segmentet i webtjenesten fra det forrige eksempel kan leveres som en forespørgselsparameter i API publiceret via API Management platformen ved hjælp af URL-adresse-skabeloner.

![Omskrivning af URL-adresse til skabelon][api-management-url-template-rewrite]

Ringer til handlingen anvender formatet `/customers?customerid=ALFKI` og dette knyttes til `/Customers('ALFKI')` når back end-tjenesten startes.


Indtast en beskrivelse af handlingen, og bruges til at levere dokumentation for udviklere ved hjælp af denne API i portalen udvikler, **vist** navn og **Beskrivelse** .

![Beskrivelse][api-management-description]

Beskrivelsen af handlingen kan angives som almindelig tekst eller HTML i tekstboksen **Beskrivelse** .

## <a name="operation-caching"> </a>Cachelagring af handling

Svar cachelagring reducerer forsinkelse af API forbrugere, sænker båndbredde forbrug og afgang belastning på HTTP-web service implementere API. 

Vælg fanen **cachelagring** så nemt og hurtigt cachelagring for handlingen, og Markér afkrydsningsfeltet **Aktivér** .

![Cachelagring][api-management-caching-tab]

**Varighed** angiver det tidsrum, hvor svaret handlingen forbliver i cachen. Standardværdien er 3600 sekunder eller 1 time.

Cache nøgler bruges til at skelne mellem svar, så det svar, der svarer til hver anden cache-nøgle får sin egen separate cachelagrede værdi. Du kan også angive bestemte parametre for forespørgselsstreng og/eller HTTP-headere skal bruges i computing cache nøgleværdier i tekstfelterne **varierer efter parametre for forespørgselsstreng** og **varierer efter sidehoveder** henholdsvis. Når ingen er angivet, fuld anmodning URL-adresse og følgende HTTP sidehoved værdier bruges i cachen til oprettelse af nøgler: **Acceptér** og **Acceptér tegnsæt**.

>Yderligere oplysninger om cachelagre og cachelagring politikker, se, [hvordan du cache handlingen resultater i Azure API Management][].


## <a name="request-parameters"> </a>Anmode om parametre

Handlingen parametre administreres under fanen parametre. Parametre angivet i **URL-adresse til skabelon** under fanen **signatur** føjes der automatisk og kan ændres kun ved at redigere skabelonen URL-adresse. Yderligere parametre kan angives manuelt.

Klik på **Tilføj forespørgselsparameter** for at tilføje en ny parameter i forespørgslen, og Angiv følgende oplysninger:

-   **Navn** - parameternavn.
-   **Beskrivelse** – en kort beskrivelse af parameteren (valgfrit).
-   **Type** - parametertype markeret i rullemenuen ned.
-   **Værdierne** - værdier, der kan tildeles til denne parameter. En af værdierne, der kan markeres som standard (valgfrit).
-   **Påkrævet** - gøre parameteren obligatorisk ved at markere afkrydsningsfeltet. 

![Anmode om parametre][api-management-request-parameters]

## <a name="request-body"> </a>Anmodningsteksten

Hvis handlingen tillader (fx læg, INDLÆG) og kræver en brødtekst, du kan medtage et eksempel på det i alle de understøttede repræsentation formater (fx json, XML). 

>Anmodningsteksten bruges kun til dokumentation og er ikke godkendt.

Hvis du vil angive en anmodningsteksten, skal du skifte til fanen **brødtekst** .

Klik på **Tilføj repræsentation**, Begynd at skrive ønskede indholdstype navn (fx programmet/json), Vælg den på rullelisten, og sæt det ønskede anmodning brødtekst eksempel i det valgte format i tekstfeltet. 

![Anmodningsteksten][api-management-request-body]

I yderligere til repræsentationer, kan du også angive en valgfri beskrivelse i tekstboksen **Beskrivelse** .

## <a name="responses"> </a>Svar

Det er en god ide at give eksempler på svar til alle statuskoder, medføre, at handlingen. Hver statuskode muligvis mere end én svar brødtekst eksempel, en for hver af de understøttede indholdstyper. 

Klik på **Tilføj** for at tilføje et svar, og begynd at skrive den ønskede statuskode. I dette eksempel Statuskoden er **200 OK**. Når koden, der vises i rullelisten, markere det, og svarkoden er oprettet og føjes til din handling.

![Svarkode][api-management-response-code]

Klik på **Tilføj repræsentation**skal begynde at skrive det ønskede indhold typenavn (fx programmet/json) og derefter vælge den i rullemenuen ned.

![Brødteksten indholdstype][api-management-response-body-content-type]

Indsæt eksemplet svar brødteksten i det valgte format i tekstfeltet. 

![Svar brødtekst][api-management-response-body]

Hvis du vil, kan du tilføje en valgfri beskrivelse i tekstboksen **Beskrivelse** .

Når handlingen er konfigureret, kan du klikke på **Gem**.


## <a name="next-steps"> </a>Næste trin

Når handlingerne er føjet til en API, er næste trin at knytte API til et produkt og publicere den, så udviklere kan ringe til dens handlinger.

-   [Hvordan du kan oprette og publicere et produkt][]

[api-management-management-console]: ./media/api-management-howto-add-operations/api-management-management-console.png
[api-management-operations]: ./media/api-management-howto-add-operations/api-management-operations.png
[api-management-add-operation]: ./media/api-management-howto-add-operations/api-management-add-operation.png
[api-management-http-method]: ./media/api-management-howto-add-operations/api-management-http-method.png
[api-management-url-template]: ./media/api-management-howto-add-operations/api-management-url-template.png
[api-management-url-template-rewrite]: ./media/api-management-howto-add-operations/api-management-url-template-rewrite.png
[api-management-description]: ./media/api-management-howto-add-operations/api-management-description.png
[api-management-caching-tab]: ./media/api-management-howto-add-operations/api-management-caching-tab.png
[api-management-request-parameters]: ./media/api-management-howto-add-operations/api-management-request-parameters.png
[api-management-request-body]: ./media/api-management-howto-add-operations/api-management-request-body.png
[api-management-response-code]: ./media/api-management-howto-add-operations/api-management-response-code.png
[api-management-response-body-content-type]: ./media/api-management-howto-add-operations/api-management-response-body-content-type.png
[api-management-response-body]: ./media/api-management-howto-add-operations/api-management-response-body.png


[api-management-contoso-api]: ./media/api-management-howto-add-operations/api-management-contoso-api.png

[api-management-add-new-api]: ./media/api-management-howto-add-operations/api-management-add-new-api.png
[api-management-api-settings]: ./media/api-management-howto-add-operations/api-management-api-settings.png
[api-management-api-settings-credentials]: ./media/api-management-howto-add-operations/api-management-api-settings-credentials.png
[api-management-api-summary]: ./media/api-management-howto-add-operations/api-management-api-summary.png
[api-management-echo-operations]: ./media/api-management-howto-add-operations/api-management-echo-operations.png

[Add an operation]: #add-operation
[Operation caching]: #operation-caching
[Request parameters]: #request-parameters
[Request body]: #request-body
[Responses]: #responses
[Next steps]: #next-steps

[Introduktion til Azure API Management]: api-management-get-started.md
[Oprette en API Management service-forekomst]: api-management-get-started.md#create-service-instance

[How to add operations to an API]: api-management-howto-add-operations.md
[Hvordan du kan oprette og publicere et produkt]: api-management-howto-add-products.md
[Sådan cache handlingen resultater i Azure API Management]: api-management-howto-cache.md