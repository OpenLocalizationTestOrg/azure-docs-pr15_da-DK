<properties
    pageTitle="Hvordan du kan installere en forekomst af Azure API Management service til flere Azure områder"
    description="Lær at implementere en Azure API Management service forekomst i flere Azure områder." 
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

# <a name="how-to-deploy-an-azure-api-management-service-instance-to-multiple-azure-regions"></a>Hvordan du kan installere en forekomst af Azure API Management service til flere Azure områder

Administration af API understøtter flere områder installation som gør det muligt API udgivere at distribuere en enkelt API management-tjenesten på tværs af en hvilken som helst antal ønskede Azure områder. Dette hjælper med at reducere anmodning forsinkelse af geografisk er distribueret API forbrugere og forbedrer også tjenesten kører, hvis ét område går offline. 

Når en API Management-tjenesten oprettes først, den indeholder kun én [enhed][] og er placeret i en enkelt Azure region, som er angivet som det primære område. Flere områder kan nemt tilføjes via Azure klassisk Portal. API Management gatewayserver er installeret til hvert område, og kald trafik bliver dirigeret til den nærmeste gateway. Hvis et område går offline, er trafikken automatisk igen direkte til den næste nærmeste gatewayen. 

> [AZURE.IMPORTANT] Flere områder installation er kun tilgængelig i **[Premium][]** lag.

## <a name="add-region"> </a>Implementere en API Management service forekomst i et nyt område

For at komme i gang skal du klikke på **Administrer** i portalen Azure klassisk for din API Management-tjenesten. Du føres til portalen API Management publisher.

![Publisher-portalen][api-management-management-console]

>Hvis du endnu ikke har oprettet en API Management service-forekomst, kan du se [oprette en API Management service forekomst][] i [Introduktion til Azure API Management][] selvstudiet.

Gå til fanen **skala** i Azure klassisk Portal for din API Management service forekomst. 

![Fanen skala][api-management-scale-service]

Klik på rullelisten under det primære område for at installere på et nyt område, og vælg et område på listen.

![Tilføje område][api-management-add-region]

Når området er markeret, Vælg antallet enheder til det nye område fra rullelisten enhed.

![Angive enheder][api-management-select-units]

Klik på **Gem**, når de ønskede områder og enheder er konfigureret.

## <a name="remove-region"> </a>Slette en API Management service forekomst fra et område

Hvis du vil fjerne en API Management service forekomst fra et område, skal du gå til fanen **skala** i Azure klassisk Portal for din API Management service forekomst. 

![Fanen skala][api-management-scale-service]

Klik på **X** til højre for det ønskede område for at fjerne.  

![Fjerne område][api-management-remove-region]

Klik på **Gem**, når de ønskede områder er fjernet.


[api-management-management-console]: ./media/api-management-howto-deploy-multi-region/api-management-management-console.png

[api-management-scale-service]: ./media/api-management-howto-deploy-multi-region/api-management-scale-service.png
[api-management-add-region]: ./media/api-management-howto-deploy-multi-region/api-management-add-region.png
[api-management-select-units]: ./media/api-management-howto-deploy-multi-region/api-management-select-units.png
[api-management-remove-region]: ./media/api-management-howto-deploy-multi-region/api-management-remove-region.png

[Oprette en API Management service-forekomst]: api-management-get-started.md#create-service-instance
[Introduktion til Azure API Management]: api-management-get-started.md

[Deploy an API Management service instance to a new region]: #add-region
[Delete an API Management service instance from a region]: #remove-region

[enhed]: http://azure.microsoft.com/pricing/details/api-management/
[Premium]: http://azure.microsoft.com/pricing/details/api-management/

