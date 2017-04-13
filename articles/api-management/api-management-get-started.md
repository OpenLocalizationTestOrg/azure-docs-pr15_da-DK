<properties
    pageTitle="Administrere din første API i Azure API Management | Microsoft Azure"
    description="Lær at oprette API'er, tilføje handlinger og komme i gang med administration af API."
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
    ms.topic="hero-article"
    ms.date="10/25/2016"
    ms.author="sdanie"/>

# <a name="manage-your-first-api-in-azure-api-management"></a>Administrere din første API i Azure API Management

## <a name="overview"> </a>Oversigt

Denne vejledning viser, hvordan du hurtigt komme i gang med Azure API administration af og foretage dine første API-opkald.

## <a name="concepts"> </a>Hvad er Azure API Management?

Du kan bruge Azure API administration til at tage en hvilken som helst backend-version, og Start et færdiglavet API-program, der er baseret på den.

Almindelige scenarier omfatter:

* **Sikring af mobile infrastruktur** ved gating adgang med API-taster, forhindre DOS angreb ved hjælp af (throttling) eller ved hjælp af avancerede sikkerhedspolitikker som JWT token validering.
* **Aktivere ISV partner økosystemer** ved giver hurtig partner onboarding via portalen udvikler og opbygning af en API facaden til decouple fra interne installationer, der ikke er parat til at partner forbrug.
* **Køre en intern API-program** ved hjælp af et centralt sted for organisationen at kommunikere om tilgængelighed og seneste ændringer til API'er, gating access, der er baseret på organisationskonti, alle baseret på en sikker kanal mellem gatewayen API og back end.


Systemet består af følgende komponenter:

* **API gateway** er slutpunktet, som:
  * Accepterer API opkald og sender dem til din en back-end.
  * Kontrollerer API taster, JWT tokens, certifikater og andre legitimationsoplysninger.
  * Gennemtvinger kvoter for ressourceforbrug og bedømme begrænsninger.
  * Transformerer din API løbende uden kode ændringer.
  * Cachelagres back end-svar hvor konfigurere.
  * Logfiler over opkald metadata til analytics formål.

* **Publisher-portalen** er den administrative grænseflade, hvor du har konfigureret din API-program. Bruge det til at:
    * Definere eller importere API skemaet.
    * Pakke API'er til produkter.
    * Konfigurere politikker som kvoter eller transformationer til API'er.
    * Få indsigt fra analyser.
    * Administrere brugere.

* **Udvikler portal** fungerer som den primære web tilstedeværelse for udviklere, hvor de kan:
    * Læs API dokumentation.
    * Prøv en API via interaktive administrationskonsollen.
    * Opret en konto og abonnere for at få API taster.
    * Access analytics på deres egne brugen.


## <a name="create-service-instance"> </a>Oprette en API Management forekomst

>[AZURE.NOTE] For at fuldføre dette selvstudium skal have du en Azure konto. Hvis du ikke har en konto, kan du oprette en gratis konto på blot et par minutter. Yderligere oplysninger finder du [Azure gratis prøveversion][].

Det første trin i at arbejde med administration af API er at oprette en forekomst af tjenesten. Logge på [Klassisk Azure-portalen][] , og klik på **Ny** **App Services** **API administration**, og **Opret**.

![Administration af API ny forekomst][api-management-create-instance-menu]

Angiv et entydigt underdomæne navn for URL-adressen **URL-adressen**til.

Vælg den ønskede **abonnement** og **område** for din tjenesteforekomst. Klik på knappen **Næste** , når du har foretaget dine valg.

![Ny API Management-tjenesten][api-management-create-instance-step1]

Skriv **Contoso Ltd.** for **Organisationens navn**, og skriv din mailadresse i feltet **Administrator E-Mail** .

>[AZURE.NOTE] Denne e-mail-adresse bruges til beskeder fra API-system. Se, [hvordan du konfigurerer beskeder og e-mail-skabeloner i Azure API Management][]kan finde flere oplysninger.

![Ny API Management-tjenesten][api-management-create-instance-step2]

Forekomster af API Management-tjenesten er tilgængelige i tre niveauer: udvikler, som Standard og Premium. Som standard oprettes nye forekomster af API Management-tjenesten i udvikler lag. Vælg Standard eller Premium niveau, Markér afkrydsningsfeltet **Avancerede indstillinger** , og vælg det ønskede niveau på følgende skærmbillede.

>[AZURE.NOTE] Det udvikler niveau er udvikling, test og pilot API-programmer, hvor høj tilgængelighed er ikke er noget problem. I Standard- og Premium lag, kan du skalere dit reserveret enhed TÆL for at håndtere mere trafik. Standard og Premium niveauer giver din API Management-tjenesten til mest behandling power og ydeevne. Du kan selv udføre dette selvstudium ved hjælp af en hvilken som helst niveau. Se [API Management priser][]kan finde flere oplysninger om administration af API niveauer.

Klik på afkrydsningsfeltet for at oprette din forekomst af tjenesten.

![Ny API Management-tjenesten][api-management-instance-created]

Når forekomsten af er oprettet, er næste trin at oprette eller importere et API.

## <a name="create-api"> </a>Importere en API

En API består af et sæt handlinger, der kan startes fra en klientprogrammet. API handlinger er proxy til eksisterende webtjenester.

API'er kan oprettes (og kan tilføjes operationer) manuelt, eller de kan importeres. I dette selvstudium skal du importere API til en stikprøve Lommeregner webtjeneste leveres af Microsoft og hostes på Azure.

>[AZURE.NOTE] Se [hvordan du opretter API'er](api-management-howto-create-apis.md) og [hvordan du føjer handlinger til en API](api-management-howto-add-operations.md)til retningslinjer for oprettelse af en API og Manuel tilføjelse af handlinger.

API'er er konfigureret til publisher-portalen, som åbnes via portalen Azure klassisk. Når portalen publisher, skal du klikke på **Administrer** i portalen Azure klassisk for din API Management-tjenesten.

![Publisher-portalen][api-management-management-console]

Klik på **API'er** i menuen **Administration af API** til venstre for at importere Lommeregner API skal, og klik derefter på **Importere API**.

![Knappen Importér API][api-management-import-api]

Udfør følgende trin for at konfigurere Lommeregner API:

1. Klik på **Fra URL-adresse**, Angiv **http://calcapi.cloudapp.net/calcapi.json** i tekstfeltet **URL-adresse specifikationen dokument** eller klikke på alternativknappen **Swagger** .
2. Skriv **beregning** i tekstfeltet **URL-adresse API suffiks** .
3. Klik i boksen **produkter (valgfrit)** , og vælg **Starter**.
4. Klik på **Gem** for at importere API.

![Tilføje nye API'ER][api-management-import-new-api]

>[AZURE.NOTE] **Administration af API** understøtter i øjeblikket både 1.2 og 2.0 version af Swagger dokument til import. Sørg for, at, selvom [Swagger 2.0-specifikationen](http://swagger.io/specification) erklærer, at `host`, `basePath`, og `schemes` egenskaber er valgfri, dokumentet Swagger 2.0 **skal** indeholde disse egenskaber Ellers bliver det ikke importeret. 

Når API er importeret, vises siden Oversigt til API i publisher-portalen.

![Oversigt over API][api-management-imported-api-summary]

Sektionen API har flere faner. Under fanen **Oversigt** viser grundlæggende målepunkter samt oplysninger om API. Under fanen [Indstillinger](api-management-howto-create-apis.md#configure-api-settings) , der bruges til at få vist og redigere konfigurationen for en API. Fanen [Handlinger](api-management-howto-add-operations.md) , der bruges til at administrere den API handlinger. Fanen **sikkerhed** kan bruges til at konfigurere gateway godkendelse til back end-serveren ved hjælp af basisgodkendelse eller [fælles certifikatgodkendelse](api-management-howto-mutual-certificates.md)og konfigurere [bruger godkendelse ved hjælp af OAuth 2.0](api-management-howto-oauth2.md).  Fanen **problemer** , der bruges til at få vist problemer, der rapporteres af de udviklere, der bruger din API'er. Fanen **produkter** bruges til at konfigurere de produkter, der indeholder denne API.

Som standard leveres hver API Management forekomst med to eksempel produkter:

-   **Starter**
-   **Ubegrænset**

Dette selvstudium blev grundlæggende Lommeregner API føjet til Starter produktet når API blev importeret.

For at foretage opkald til en API, skal udviklere først abonnere på et produkt, der giver dem adgang til den. Udviklere kan abonnere på produkter i portalen udvikler eller administratorer kan abonnere udviklere til portalen publisher-produkter. Du er administrator, da du oprettede forekomsten API Management i selvstudiet, i de forrige trin, så du allerede er tilmeldt til hvert produkt som standard.

## <a name="call-operation"> </a>Ringe til en handling fra portalen udvikler

Handlinger kan kun kaldes direkte fra portalen udvikler, som indeholder en nem måde at få vist og teste handlinger for en API. I dette selvstudium trin, kan du ringe til grundlæggende Lommeregner APIS **Tilføj to heltal** handling. Klik på **udvikler portal** i menuen øverst til højre på portalen publisher.

![Udvikler portal][api-management-developer-portal-menu]

Klik på **API'er** fra den øverste menu, og klik derefter på **Grundlæggende Lommeregner** for at få vist de tilgængelige handlinger.

![Udvikler portal][api-management-developer-portal-calc-api]

Bemærk eksempel beskrivelser og parametre, der er importeret sammen med det API og handlinger, indsende dokumentation for udviklere, som skal bruge denne handling. Disse beskrivelser kan også tilføjes, når handlinger er tilføjet manuelt.

Klik på **Prøv det**for at ringe handlingen **Tilføj to heltal** .

![Prøv det][api-management-developer-portal-calc-api-console]

Du kan angive nogle værdier for parametrene eller beholde standardindstillingerne og klik derefter på **Send**.

![HTTP Get][api-management-invoke-get]

Når en handlingen er aktiveret, vises portalen udvikler **status for svar**, **svar sidehoveder**og alle **svar indhold**.

![Svar][api-management-invoke-get-response]

## <a name="view-analytics"> </a>Se analytics

For at se analytics for grundlæggende Lommeregner, skal du skifte tilbage til portalen publisher ved at vælge **Administrer** på menuen øverst til højre på portalen udvikler.

![Administrere][api-management-manage-menu]

Standardvisningen for portalen publisher er **Dashboard**, der indeholder en oversigt over din API Management forekomst.

![Dashboard][api-management-dashboard]

Hold musen over diagrammet til **Grundlæggende Lommeregner** til at få vist bestemte omfanget af brugen af API til en given tidsperiode.

>[AZURE.NOTE] Hvis du ikke kan se alle linjer i diagrammet, skifte tilbage til portalen udvikler og foretage nogle opkald til API, vent et øjeblik og vende tilbage til dashboard.

Klik på **Vis detaljer** for at få vist siden Oversigt for API, herunder en større udgave af det viste målepunkter.

![Analytics][api-management-mouse-over]

![Oversigt][api-management-api-summary-metrics]

Klik på **Analytics** fra menuen **Administration af API** til venstre for detaljerede målepunkter og rapporter.

![Oversigt][api-management-analytics-overview]

Sektionen **Analytics** har følgende fire faner:

-   **Et hurtigt overblik** indeholder overordnede brugen og tilstand for statistik, samt den øverste udviklere, bedste produkter, øverste API'er og øverste handlinger.
-   **Brugen** indeholder et detaljeret indblik i API opkald og båndbredde, herunder en geografisk repræsentation.
-   **Sundhed** fokuserer på status-koder, cache succes satser, svar klokkeslæt og API og svar tidspunkter for service.
-   **Aktivitet** indeholder rapporter, der analysere ned i den specifikke aktivitet ved udvikler, produkt, API og handling.

## <a name="next-steps"> </a>Næste trin

- Lær, hvordan du [Beskyt din API med rente begrænsninger](api-management-howto-product-with-rules.md).

[Azure gratis prøveversion]: http://azure.microsoft.com/pricing/free-trial/?WT.mc_id=api_management_hero_a

[Create an API Management instance]: #create-service-instance
[Create an API]: #create-api
[Add an operation]: #add-operation
[Add the new API to a product]: #add-api-to-product
[Subscribe to the product that contains the API]: #subscribe
[Call an operation from the Developer Portal]: #call-operation
[View analytics]: #view-analytics
[Next steps]: #next-steps


[How to manage developer accounts in Azure API Management]: api-management-howto-create-or-invite-developers.md
[Configure API settings]: api-management-howto-create-apis.md#configure-api-settings
[Sådan konfigureres beskeder og e-mail-skabeloner i Azure API Management]: api-management-howto-configure-notifications.md
[Responses]: api-management-howto-add-operations.md#responses
[How create and publish a product]: api-management-howto-add-products.md
[Administration af API priser]: http://azure.microsoft.com/pricing/details/api-management/

[Azure klassisk Portal]: https://manage.windowsazure.com/

[api-management-management-console]: ./media/api-management-get-started/api-management-management-console.png
[api-management-create-instance-menu]: ./media/api-management-get-started/api-management-create-instance-menu.png
[api-management-create-instance-step1]: ./media/api-management-get-started/api-management-create-instance-step1.png
[api-management-create-instance-step2]: ./media/api-management-get-started/api-management-create-instance-step2.png
[api-management-instance-created]: ./media/api-management-get-started/api-management-instance-created.png
[api-management-import-api]: ./media/api-management-get-started/api-management-import-api.png
[api-management-import-new-api]: ./media/api-management-get-started/api-management-import-new-api.png
[api-management-imported-api-summary]: ./media/api-management-get-started/api-management-imported-api-summary.png
[api-management-calc-operations]: ./media/api-management-get-started/api-management-calc-operations.png
[api-management-list-products]: ./media/api-management-get-started/api-management-list-products.png
[api-management-add-api-to-product]: ./media/api-management-get-started/api-management-add-api-to-product.png
[api-management-add-myechoapi-to-product]: ./media/api-management-get-started/api-management-add-myechoapi-to-product.png
[api-management-api-added-to-product]: ./media/api-management-get-started/api-management-api-added-to-product.png
[api-management-developers]: ./media/api-management-get-started/api-management-developers.png
[api-management-add-subscription]: ./media/api-management-get-started/api-management-add-subscription.png
[api-management-add-subscription-window]: ./media/api-management-get-started/api-management-add-subscription-window.png
[api-management-subscription-added]: ./media/api-management-get-started/api-management-subscription-added.png
[api-management-developer-portal-menu]: ./media/api-management-get-started/api-management-developer-portal-menu.png
[api-management-developer-portal-calc-api]: ./media/api-management-get-started/api-management-developer-portal-calc-api.png
[api-management-developer-portal-calc-api-console]: ./media/api-management-get-started/api-management-developer-portal-calc-api-console.png
[api-management-invoke-get]: ./media/api-management-get-started/api-management-invoke-get.png
[api-management-invoke-get-response]: ./media/api-management-get-started/api-management-invoke-get-response.png
[api-management-manage-menu]: ./media/api-management-get-started/api-management-manage-menu.png
[api-management-dashboard]: ./media/api-management-get-started/api-management-dashboard.png

[api-management-add-response]: ./media/api-management-get-started/api-management-add-response.png
[api-management-add-response-window]: ./media/api-management-get-started/api-management-add-response-window.png
[api-management-developer-key]: ./media/api-management-get-started/api-management-developer-key.png
[api-management-mouse-over]: ./media/api-management-get-started/api-management-mouse-over.png
[api-management-api-summary-metrics]: ./media/api-management-get-started/api-management-api-summary-metrics.png
[api-management-analytics-overview]: ./media/api-management-get-started/api-management-analytics-overview.png
[api-management-analytics-usage]: ./media/api-management-get-started/api-management-analytics-usage.png
[api-management-]: ./media/api-management-get-started/api-management-.png
[api-management-]: ./media/api-management-get-started/api-management-.png
