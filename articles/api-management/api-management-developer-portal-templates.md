<properties 
    pageTitle="Hvordan du tilpasser Azure API Management udvikler portalen ved hjælp af skabeloner | Microsoft Azure" 
    description="Lær, hvordan du tilpasser Azure API Management udvikler portalen ved hjælp af skabeloner." 
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


# <a name="how-to-customize-the-azure-api-management-developer-portal-using-templates"></a>Hvordan du tilpasser Azure API Management udvikler portalen ved hjælp af skabeloner

Azure API Administration indeholder flere tilpasningsfunktioner for at tillade, at administratorer tilpasse [udseende og funktionalitet for portalen udvikler](api-management-customize-portal.md)samt tilpasse indholdet af siden udvikler portalen ved hjælp af et sæt skabeloner, som konfigurere indholdet af selve siderne. Brug af [DotLiquid](http://dotliquidmarkup.org/) syntaksen og et medfølgende sæt af ressourcer, der er lokaliseret streng, ikoner og siden kontrolelementer, har du stor fleksibilitet til at konfigurere indholdet af siderne, efter behov ved hjælp af skabelonerne.

## <a name="developer-portal-templates-overview"></a>Oversigt over udvikler portalen skabeloner

Udvikler portalen skabeloner administreres i portalen udvikler administratorer af forekomsten af API administration. Hvis du vil administrere udvikler skabeloner, skal du gå til din API Management service forekomst i portalen Azure klassisk og klikke på **Gennemse**.

![Udvikler portal][api-management-browse]

Hvis du er allerede i publisher-portalen, kan du få adgang til portalen udvikler ved at klikke på **udvikler portal**.

![Udvikler portalen menu][api-management-developer-portal-menu]

For at få adgang til portalen skabelonerne udvikler, klikke på Tilpas ikonet til venstre for at få vist menuen tilpasning, og klik på **skabeloner**.

![Udvikler portalen skabeloner][api-management-customize-menu]

Listen over skabeloner viser flere kategorier af skabeloner, der dækker de forskellige sider i portalen udvikler. Hver skabelon er anderledes, men trinnene for at redigere dem og publicere ændringerne er den samme. Hvis du vil redigere en skabelon, skal du klikke på navnet på skabelonen.

![Udvikler portalen skabeloner][api-management-templates-menu]

Hvis du klikker på en skabelon, fører dig til udvikler portalen siden, som kan tilpasses af denne skabelon. Skabelonen vises i dette eksempel på **produktlisten** . Skabelonen **produktlisten** styrer område af skærmen, angivet med den røde firkant. 

![Produkter listeskabelon][api-management-developer-portal-templates-overview]

Nogle skabeloner, som skabeloner **Brugerprofil** tilpasse forskellige dele af den samme side. 

![Brugerskabeloner profil][api-management-user-profile-templates]

Editor for hver enkelt udvikler portalen skabelon består af to sektioner vises i bunden af siden. I venstre side viser ruden redigering til skabelonen, og i højre side vises datamodellen til skabelonen. 

Skabelonen redigering ruden indeholder den kode, der styrer udseendet og funktionaliteten af den pågældende side i portalen udvikler. Markering i skabelonen bruger [DotLiquid](http://dotliquidmarkup.org/) syntaks. Én populære editor til DotLiquid er [DotLiquid for designere](https://github.com/dotliquid/dotliquid/wiki/DotLiquid-for-Designers). Der vises eventuelle ændringer til skabelonen under redigering i realtid i browseren, men er ikke synlige for kunder, indtil du [gemme](#to-save-a-template) og [publicere](#to-publish-a-template) skabelonen.

![Skabelon markering][api-management-template]

Ruden **skabelon data** indeholder en vejledning til datamodellen for de enheder, der er tilgængelige til brug i en bestemt skabelon. Denne vejledning kan ved at vise de direkte data, der aktuelt vises i portalen udvikler. Du kan udvide ruderne skabelon ved at klikke på rektangel i øverste højre hjørne af ruden **skabelon data** .

![Skabelon-datamodel][api-management-template-data]

I det forrige eksempel er der to produkter, der vises i portalen udvikler, der er hentet fra de data, der vises i ruden **skabelon data** , som vist i følgende eksempel.

    {
        "Paging": {
            "Page": 1,
            "PageSize": 10,
            "TotalItemCount": 2,
            "ShowAll": false,
            "PageCount": 1
        },
        "Filtering": {
            "Pattern": null,
            "Placeholder": "Search products"
        },
        "Products": [
            {
                "Id": "56ec64c380ed850042060001",
                "Title": "Starter",
                "Description": "Subscribers will be able to run 5 calls/minute up to a maximum of 100 calls/week.",
                "Terms": "",
                "ProductState": 1,
                "AllowMultipleSubscriptions": false,
                "MultipleSubscriptionsCount": 1
            },
            {
                "Id": "56ec64c380ed850042060002",
                "Title": "Unlimited",
                "Description": "Subscribers have completely unlimited access to the API. Administrator approval is required.",
                "Terms": null,
                "ProductState": 1,
                "AllowMultipleSubscriptions": false,
                "MultipleSubscriptionsCount": 1
            }
        ]
    }

Markering i skabelonen **produktlisten** behandler data for at give den ønskede output ved gentagelse gennem indsamling af produkter til at vise oplysninger og et link til hvert enkelt produkt. Bemærk de `<search-control>` og `<page-control>` elementer i markeringen. Disse styrer visningen af den søgning og sideopdeling kontrolelementer på siden. `ProductsStrings|PageTitleProducts`er en reference til lokaliseret streng, der indeholder den `h2` overskrifter for siden. En liste over streng ressourcer, siden kontrolelementer og ikoner, der er tilgængelige til brug i udvikler portalen skabeloner, finder du [API Management Udviklerreference portalen skabeloner](https://msdn.microsoft.com/library/azure/mt697540.aspx).

    <search-control></search-control>
    <div class="row">
        <div class="col-md-9">
            <h2>{% localized "ProductsStrings|PageTitleProducts" %}</h2>
        </div>
    </div>
    <div class="row">
        <div class="col-md-12">
        {% if products.size > 0 %}
        <ul class="list-unstyled">
        {% for product in products %}
            <li>
                <h3><a href="/products/{{product.id}}">{{product.title}}</a></h3>
                {{product.description}}
            </li>   
        {% endfor %}
        </ul>
        <paging-control></paging-control>
        {% else %}
        {% localized "CommonResources|NoItemsToDisplay" %}
        {% endif %}
        </div>
    </div>

## <a name="to-save-a-template"></a>Gemme en skabelon

Klik på Gem for at gemme en skabelon, i editoren skabelon.

![Gem skabelon][api-management-save-template]

Gemte ændringer er ikke direkte i portalen udvikler, indtil de er udgivet.

## <a name="to-publish-a-template"></a>Publicere en skabelon

Gemte skabeloner kan udgives, enten individuelt eller alle sammen. Hvis du vil publicere en skabelon til en enkelt, klik på Publicer i editoren skabelon.

![Publicere skabelon][api-management-publish-template]

Klik på **Ja** for at bekræfte og gøre skabelonen direkte på portalen udvikler.

![Bekræfte publicere][api-management-publish-template-confirm]

Hvis du vil publicere alle i øjeblikket ikke-udgivne skabelon versioner, skal du klikke på **Udgiv** i listen over skabeloner. Ikke-udgivne skabeloner er udpeget af en stjerne efter navnet på skabelonen. I dette eksempel udgives skabelonerne **produktlisten** og **Produktnummer** .

![Publicere skabeloner][api-management-publish-templates]

Klik på **Publicer tilpasninger** for at bekræfte.

![Bekræfte publicere][api-management-publish-customizations]

Nyligt publicerede skabeloner er effektive umiddelbart på portalen udvikler.

## <a name="to-revert-a-template-to-the-previous-version"></a>Gendanne en skabelon til den tidligere version

Klik på tilbage for at gendanne en skabelon til den forrige udgivne version, i editoren skabelon.

![Omdanne skabelon][api-management-revert-template]

Klik på **Ja** for at bekræfte.

![Bekræfte][api-management-revert-template-confirm]

Den tidligere udgivne version af en skabelon er direkte i portalen udvikler, når handlingen Genindlæs er fuldført.

## <a name="to-restore-a-template-to-the-default-version"></a>Gendanne en skabelon til standardversionen

Gendanne skabeloner til deres standardversionen er en proces. Først skabelonerne skal gendannes, og klik derefter de gendannede versioner skal publiceres.

Klik på Gendan i editoren skabelon for at gendanne en enkelt skabelon til standardversionen.

![Omdanne skabelon][api-management-reset-template]

Klik på **Ja** for at bekræfte.

![Bekræfte][api-management-reset-template-confirm]

Klik på **Gendan standardskabelonerne** på skabelonlisten for at gendanne alle skabeloner til deres standardversioner.

![Gendanne skabeloner][api-management-restore-templates]

De gendannede skabeloner skal derefter udgives enkeltvis eller på én gang ved at følge trinnene i [at udgive en skabelon](#to-publish-a-template).

## <a name="developer-portal-templates-reference"></a>Portalen skabeloner Udviklerreference

Du kan finde oplysninger om udvikler portalen skabeloner, strengressourcer, ikoner og siden kontrolelementer, [API Management Udviklerreference portalen skabeloner](https://msdn.microsoft.com/library/azure/mt697540.aspx).

## <a name="watch-a-video-overview"></a>Se en video oversigt

Se følgende video for at se, hvordan du tilføjer et diskussionsforum og bedømmelser på siderne API og handling i portalen udvikler ved hjælp af skabeloner.

> [AZURE.VIDEO adding-developer-portal-functionality-using-templates-in-azure-api-management]


[api-management-customize-menu]: ./media/api-management-developer-portal-templates/api-management-customize-menu.png
[api-management-templates-menu]: ./media/api-management-developer-portal-templates/api-management-templates-menu.png
[api-management-developer-portal-templates-overview]: ./media/api-management-developer-portal-templates/api-management-developer-portal-templates-overview.png
[api-management-template]: ./media/api-management-developer-portal-templates/api-management-template.png
[api-management-template-data]: ./media/api-management-developer-portal-templates/api-management-template-data.png
[api-management-developer-portal-menu]: ./media/api-management-developer-portal-templates/api-management-developer-portal-menu.png
[api-management-browse]: ./media/api-management-developer-portal-templates/api-management-browse.png
[api-management-user-profile-templates]: ./media/api-management-developer-portal-templates/api-management-user-profile-templates.png
[api-management-save-template]: ./media/api-management-developer-portal-templates/api-management-save-template.png
[api-management-publish-template]: ./media/api-management-developer-portal-templates/api-management-publish-template.png
[api-management-publish-template-confirm]: ./media/api-management-developer-portal-templates/api-management-publish-template-confirm.png
[api-management-publish-templates]: ./media/api-management-developer-portal-templates/api-management-publish-templates.png
[api-management-publish-customizations]: ./media/api-management-developer-portal-templates/api-management-publish-customizations.png
[api-management-revert-template]: ./media/api-management-developer-portal-templates/api-management-revert-template.png
[api-management-revert-template-confirm]: ./media/api-management-developer-portal-templates/api-management-revert-template-confirm.png
[api-management-reset-template]: ./media/api-management-developer-portal-templates/api-management-reset-template.png
[api-management-reset-template-confirm]: ./media/api-management-developer-portal-templates/api-management-reset-template-confirm.png
[api-management-restore-templates]: ./media/api-management-developer-portal-templates/api-management-restore-templates.png







