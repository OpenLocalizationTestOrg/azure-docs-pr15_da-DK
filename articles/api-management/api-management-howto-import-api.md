<properties 
    pageTitle="Administration af API vigtige begreber" 
    description="Få mere at vide om API'er, -produkter, roller, grupper og andre vigtige koncepter API administration." 
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

# <a name="how-to-import-the-definition-of-an-api-with-operations-in-azure-api-management"></a>Sådan importeres definitionen af et API med handlinger i Azure API Management

Nye API'er kan oprettes i API Management, og de handlinger, der er tilføjet manuelt eller API kan importeres sammen med handlinger i ét trin.

API'er og deres aktiviteter kan importeres i følgende formater.

-   WADL
-   Swagger

Denne vejledning viser hvordan opretter en ny API og dens importhandlinger i ét trin. Se oplysninger om manuelt oprette en API og tilføje handlinger, [hvordan du opretter API'er][] og [hvordan du føjer handlinger til en API][].

## <a name="import-api"> </a>Importere en API

API'er er oprettet og konfigureret i publisher-portalen. Klik på **Administrer** i portalen Azure klassisk for din API Management-tjenesten for at få adgang til portalen publisher. Hvis du endnu ikke har oprettet en API Management service-forekomst, kan du se [oprette en API Management service forekomst][] i [Introduktion til Azure API Management][] selvstudiet.

![Publisher-portalen][api-management-management-console]

Klik på **API'er** i menuen **Administration af API** til venstre, og klik derefter på **Importer API**.

![Importér API][api-management-import-apis]

Vinduet **Importér API** indeholder tre faner, der svarer til de tre måder til at levere specifikationen API.

-   Kan du indsætte specifikationen API i tekstfeltet udpegede **fra Udklipsholder** .
-   Kan du gå til, og vælg den fil, der indeholder specifikationen API **fra fil** .
-   **Fra URL-adressen** kan du angive URL-adressen til specifikation for API.

![API importformat][api-management-import-api-clipboard]

Brug alternativknapper i højre side til at angive formatet specifikation når du har indtastet specifikationen API. Følgende filformater understøttes.

-   WADL
-   Swagger

Derefter skal du angive en **URL-adresse API suffiks**. Dette er føjet til grundlæggende URL-adressen for din API management-tjenesten. Grundtallet URL-adressen er fælles for alle API'er hostes på hver forekomst af en API Management-tjenesten. Administration af API adskiller API'er ved deres suffiks, og derfor suffikset skal være entydige for hver API i en bestemt API management service forekomst.

Når alle værdier er angivet, skal du klikke på **Gem** for at oprette API og de tilknyttede handlinger. 

>[AZURE.NOTE] Se [administrere dine første API i Azure API Management](api-management-get-started.md)et selvstudium ved at importere en grundlæggende Lommeregner API i Swagger format.

## <a name="export-api"></a> Eksportere en API

Ud over at importere nye API'er, kan du eksportere definitioner af din API'er fra portalen publisher. For at gøre det, skal du klikke på **Eksporter API** fra på **fanen Oversigt** for din **API**.

![Eksportere API][api-management-export-api]

API'er kan eksporteres ved hjælp af WADL eller Swagger. Vælg det ønskede format, skal du klikke på **Gem**, og vælg den placering, hvor filen skal gemmes.

![API eksportformat][api-management-export-api-format]

## <a name="next-steps"> </a>Næste trin

Når der oprettes en API og de handlinger, der er importeret, kan du gennemse og Konfigurer eventuelle yderligere indstillinger for Tilføj API til et produkt, og publicere den, så den er tilgængelig for udviklere. Du kan finde flere oplysninger i følgende vejledninger.

-   [Sådan konfigureres API indstillinger][]
-   [Hvordan du kan oprette og publicere et produkt][]




[api-management-management-console]: ./media/api-management-howto-import-api/api-management-management-console.png
[api-management-import-apis]: ./media/api-management-howto-import-api/api-management-api-import-apis.png
[api-management-import-api-clipboard]: ./media/api-management-howto-import-api/api-management-import-api-wizard.png
[api-management-export-api]: ./media/api-management-howto-import-api/api-management-export-api.png
[api-management-export-api-format]: ./media/api-management-howto-import-api/api-management-export-api-format.png

[Import an API]: #import-api
[Export an API]: #export-api
[Configure API settings]: #configure-api-settings
[Next steps]: #next-steps

[Introduktion til Azure API Management]: api-management-get-started.md
[Oprette en API Management service-forekomst]: api-management-get-started.md#create-service-instance

[Hvordan du føjer handlinger til en API]: api-management-howto-add-operations.md
[Hvordan du kan oprette og publicere et produkt]: api-management-howto-add-products.md
[Sådan opretter du API'er]: api-management-howto-create-apis.md
[Sådan konfigureres API indstillinger]: api-management-howto-create-apis.md#configure-api-settings
