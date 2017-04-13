<properties 
    pageTitle="Sådan oprettes API'er i Azure API Management" 
    description="Lær at oprette og konfigurere API'er i Azure API Management." 
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

# <a name="how-to-create-apis-in-azure-api-management"></a>Sådan oprettes API'er i Azure API Management

En API i API Management repræsenterer et sæt handlinger, der kan aktiveres af klientprogrammer. Nye API'er, som er oprettet i publisher-portalen, og derefter de ønskede handlinger tilføjes. Når handlingerne er tilføjet, API føjes til et produkt og kan udgives. Når en API er udgivet, kan det abonneret på og bruges af udviklere.

Denne vejledning viser det første trin i processen: hvordan du kan oprette og konfigurere en ny API i API administration. Se yderligere oplysninger om at tilføje handlinger og publicere et produkt, [hvordan du føjer handlinger til en API][] og [hvordan du kan oprette og publicere et produkt][].

## <a name="create-new-api"> </a>Oprette en ny API

API'er er oprettet og konfigureret i publisher-portalen. Klik på **Administrer** i portalen Azure klassisk for din API Management-tjenesten for at få adgang til portalen publisher.

![Publisher-portalen][api-management-management-console]

>Hvis du endnu ikke har oprettet en API Management service-forekomst, kan du se [oprette en API Management service forekomst][] i [Introduktion til Azure API Management][] selvstudiet.

Klik på **API'er** i menuen **Administration af API** til venstre, og klik derefter på **Tilføj API**.

![Oprette API][api-management-create-api]

Brug vinduet **Tilføj nye API** til at konfigurere det nye API.

![Tilføje nye API'ER][api-management-add-new-api]

Følgende felter bruges til at konfigurere det nye API.

-   **Web API-navn** indeholder et entydigt og beskrivende navn til API. Den vises i udvikler og publisher portaler.
-   **URL-adresse tjeneste** referencer implementere API HTTP-tjenesten. Administration af API videresender anmodninger til denne adresse.
-   **URL-adresse API suffiks** føjes til grundlæggende URL-adressen til API management-tjenesten. Grundtallet URL-adressen er fælles for alle API'er, der er hostet af en API Management service forekomst. Administration af API adskiller API'er ved deres suffiks, og derfor suffikset skal være entydige for hver API til en given udgiver.
-   **URL-adresse API farveskema** bestemmer, hvilke protokoller kan bruges til at få adgang til API. HTTPs er angivet som standard.
-   Klik på **produkter (valgfrit)** ned for at mulighed for at tilføje denne nye API til et produkt, og vælg et produkt. Dette trin kan gentages flere gange føje API til flere produkter.

Når de ønskede værdier er konfigureret, skal du klikke på **Gem**. Når det nye API er oprettet, vises siden Oversigt til API i publisher-portalen.

![Oversigt over API][api-management-api-summary]

## <a name="configure-api-settings"> </a>Konfigurere API indstillinger

Du kan bruge under fanen **Indstillinger** til at bekræfte og redigere konfigurationen for en API. **Web API-navn**, **URL-adresse-tjenesten**og **URL-adresse API suffiks** angives først når API er blevet oprettet og kan ændres her. **Beskrivelse** giver en valgfri beskrivelse og **URL-adresse API farveskema** bestemmer, hvilke protokoller kan bruges til at få adgang til API.

![Indstillinger for API][api-management-api-settings]

Hvis du vil konfigurere gateway godkendelse for tjenesten back end-, implementere API, Vælg fanen **sikkerhed** . **Med legitimationsoplysninger** rullelisten kan bruges til at konfigurere **HTTP grundlæggende** eller **klientcertifikater** godkendelse. Hvis du vil bruge HTTP basisgodkendelse, skal du angive de ønskede legitimationsoplysninger. Oplysninger om brug af klientcertifikat, se, [hvordan du sikrer back end - tjenester ved hjælp af klientcertifikat i Azure API Management][].

Fanen **sikkerhed** kan også bruges til at konfigurere **bruger godkendelse** ved hjælp af OAuth 2.0. Se, [hvordan du Godkend udvikler firmaer ved hjælp af OAuth 2.0 i Azure API Management][]kan finde flere oplysninger.

![Grundlæggende godkendelsesindstillinger][api-management-api-settings-credentials]

Klik på **Gem** for at gemme de ændringer, du foretager af API-indstillingerne.

## <a name="next-steps"> </a>Næste trin

Når en API er oprettet, og de indstillinger, der er konfigureret, de næste trin er at føje handlingerne til API, føje API til et produkt, og publicere den, så den er tilgængelig for udviklere. Du kan finde flere oplysninger i følgende artikler.

-   [Hvordan du føjer handlinger til en API][]
-   [Hvordan du kan oprette og publicere et produkt][]





[api-management-create-api]: ./media/api-management-howto-create-apis/api-management-create-api.png
[api-management-management-console]: ./media/api-management-howto-create-apis/api-management-management-console.png
[api-management-add-new-api]: ./media/api-management-howto-create-apis/api-management-add-new-api.png
[api-management-api-settings]: ./media/api-management-howto-create-apis/api-management-api-settings.png
[api-management-api-settings-credentials]: ./media/api-management-howto-create-apis/api-management-api-settings-credentials.png
[api-management-api-summary]: ./media/api-management-howto-create-apis/api-management-api-summary.png
[api-management-echo-operations]: ./media/api-management-howto-create-apis/api-management-echo-operations.png

[What is an API?]: #what-is-api
[Create a new API]: #create-new-api
[Configure API settings]: #configure-api-settings
[Configure API operations]: #configure-api-operations
[Next steps]: #next-steps

[Hvordan du føjer handlinger til en API]: api-management-howto-add-operations.md
[Hvordan du kan oprette og publicere et produkt]: api-management-howto-add-products.md

[Introduktion til Azure API Management]: api-management-get-started.md
[Oprette en API Management service-forekomst]: api-management-get-started.md#create-service-instance
[Hvordan du sikrer back end-tjenester ved hjælp af klienten certifikatgodkendelse i Azure API Management]: api-management-howto-mutual-certificates.md
[Sådan Godkend udvikler konti med OAuth 2.0 i Azure API Management]: api-management-howto-oauth2.md