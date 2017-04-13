<properties
    pageTitle="Tilføje cachelagring for at forbedre ydeevnen i Azure API Management | Microsoft Azure"
    description="Lær at forbedre ventetid, forbrug af båndbredde og web service indlæsning af API Management serviceopkald."
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
    ms.topic="get-started-article"
    ms.date="10/25/2016"
    ms.author="sdanie"/>

# <a name="add-caching-to-improve-performance-in-azure-api-management"></a>Tilføje cachelagring for at forbedre ydeevnen i Azure API Management

Handlinger i administration af API kan konfigureres for cachelagring af svar. Svar cachelagring kan markant reducere API ventetid, forbrug af båndbredde, og web service indlæsning af data, der ikke ændres ofte.

Denne vejledning viser, hvordan du tilføje svar cachelagring af din API og konfigurere politikker for eksempel ekkoet API handlinger. Derefter kan du ringe handlingen fra portalen udvikler for at bekræfte cachelagring i aktion.

>[AZURE.NOTE] Finde oplysninger om cachelagring elementer efter nøgle ved hjælp af politik udtryk, under [brugerdefineret cachelagring i Azure API Management](api-management-sample-cache-by-key.md).

## <a name="prerequisites"></a>Forudsætninger

Før du følger trinnene i denne vejledning, skal du have en API Management service forekomst med en API og konfigureret til et produkt. Hvis du endnu ikke har oprettet en API Management service-forekomst, kan du se [oprette en API Management service forekomst][] i [Introduktion til Azure API Management][] selvstudiet.

## <a name="configure-caching"> </a>Konfigurere en handling til cachelagring

I dette trin, vil du gennemgå indstillingerne for cachelagring af handlingen **Få ressource (cachelagret)** stikprøvens ekkoet API.

>[AZURE.NOTE] Hver forekomst af API Management service er forudkonfigureret med en ekkoet API, der kan bruges til at eksperimentere med og få mere at vide om administration af API. Se [Introduktion til Azure API Management][]kan finde flere oplysninger.

For at komme i gang skal du klikke på **Administrer** i portalen Azure klassisk for din API Management-tjenesten. Du føres til portalen API Management publisher.

![Publisher-portalen][api-management-management-console]

Klik på **API'er** i menuen **Administration af API** til venstre, og klik derefter på **Ekkoet API**.

![Ekkoet API][api-management-echo-api]

Klik på fanen **Handlinger** , og klik derefter på handlingen **Få ressource (cachelagret)** på listen **Handlinger** .

![Ekkoet API handlinger][api-management-echo-api-operations]

Klik på fanen **cachelagring** for at få vist indstillingerne for cachelagring for denne handling.

![Cachelagring fanen][api-management-caching-tab]

For at aktivere cachelagring af en handling skal du markere afkrydsningsfeltet **Aktivér** . I dette eksempel er cachelagring aktiveret.

Hver handling svar indtastes baseret på værdierne i felterne **varierer efter parametre for forespørgselsstreng** og **varierer efter overskrifter** . Hvis du vil gemme i cachen flere svar, der er baseret på forespørgselsparametre streng eller sidehoveder, kan du konfigurere dem i disse to felter.

**Varighed** angiver intervallet for udløb af cachelagrede svar. I dette eksempel er intervallet **3600** sekunder, hvilket er svarer til en time.

Bruger cachelagring konfigurationen i dette eksempel, returnerer den første anmodning til handlingen **Få ressource (cachelagret)** et svar fra back end-tjenesten. Dette svar cachelagres, indtastes en af de angivne sidehoveder og forespørgselsparametre streng. Efterfølgende opkald til handlingen med tilsvarende parametre, har det cachelagrede svar, der returneres, indtil cachen varighed interval er udløbet.

## <a name="caching-policies"> </a>Gennemse de cachelagring politikker

I dette trin skal gennemgå du cachelagring indstillingerne for handlingen **Få ressource (cachelagret)** stikprøvens ekkoet API.

Når cachelagring indstillinger er konfigureret for en handling under fanen **cachelagring** , føjes cachelagring politikker for handlingen. Disse politikker kan vises og redigeres i politikeditoren.

Klik på **politikker** fra menuen **Administration af API** til venstre, og vælg derefter **ekkoet API / få ressource (cachelagret)** fra rullelisten **handling** .

![Politikken scope handling][api-management-operation-dropdown]

Dette viser politikker for denne handling i politikeditoren.

![Administration af API politik editor][api-management-policy-editor]

Politikdefinitionen af for denne handling indeholder de politikker, der definerer den cachelagring konfiguration, der blev gennemset ved hjælp af fanen **cachelagring** i ovenstående trin.

    <policies>
        <inbound>
            <base />
            <cache-lookup vary-by-developer="false" vary-by-developer-groups="false">
                <vary-by-header>Accept</vary-by-header>
                <vary-by-header>Accept-Charset</vary-by-header>
            </cache-lookup>
            <rewrite-uri template="/resource" />
        </inbound>
        <outbound>
            <base />
            <cache-store caching-mode="cache-on" duration="3600" />
        </outbound>
    </policies>

>[AZURE.NOTE] Ændringer til politikker for cachelagring af i politikeditoren afspejles under fanen **cachelagring** af en handling, og omvendt.

## <a name="test-operation"> </a>Ringe til en handling og teste den cachelagring

For at få vist cachelagring i aktion, kan vi kalder handlingen fra portalen udvikler. Klik på **udvikler portal** i øverste højre-menuen.

![Udvikler portal][api-management-developer-portal-menu]

Klik på **API'er** i den øverste menu, og vælg derefter **Ekkoet API**.

![Ekkoet API][api-management-apis-echo-api]

>Hvis du har kun én API konfigurerede eller synlige for din konto, fører derefter klikke på API'er dig direkte til handlinger for denne API.

Vælg handlingen, **Få ressource (cachelagret)** , og klik derefter på **Åbn Console**.

![Åben konsol][api-management-open-console]

Konsollen gør det muligt at kalde handlinger direkte fra portalen udvikler.

![Console][api-management-console]

Bevar standardværdierne for **param1** og **param2**.

Vælg den ønskede nøgle fra rullelisten **abonnement-nøgle** . Hvis dit firma har kun ét abonnement, vil det allerede være markeret.

Angiv **sampleheader:value1** i tekstfeltet **anmode om sidehoveder** .

Klik på **HTTP Get** og notere svar overskrifterne.

Angiv **sampleheader:value2** i tekstfeltet **anmode om sidehoveder** , og klik derefter på **HTTP Get**.

Bemærk, at værdien af **sampleheader** er stadig **værdi1** i svaret. Prøv nogle forskellige værdier, og Bemærk, at det cachelagrede svar fra det første opkald returneres.

Skrive **25** i feltet **param2** , og klik derefter på **HTTP Get**.

Bemærk, at værdien af **sampleheader** i svaret nu er **værdi2**. Fordi handlingen resultaterne indtastes ved forespørgselsstreng, returneres det forrige cachelagrede svar blev ikke.

## <a name="next-steps"> </a>Næste trin

-   Du kan finde flere oplysninger om cachelagring politikker, se [cachelagring politikker][] i [API Management politikreference][].
-   Finde oplysninger om cachelagring elementer efter nøgle ved hjælp af politik udtryk, under [brugerdefineret cachelagring i Azure API Management](api-management-sample-cache-by-key.md).

[api-management-management-console]: ./media/api-management-howto-cache/api-management-management-console.png
[api-management-echo-api]: ./media/api-management-howto-cache/api-management-echo-api.png
[api-management-echo-api-operations]: ./media/api-management-howto-cache/api-management-echo-api-operations.png
[api-management-caching-tab]: ./media/api-management-howto-cache/api-management-caching-tab.png
[api-management-operation-dropdown]: ./media/api-management-howto-cache/api-management-operation-dropdown.png
[api-management-policy-editor]: ./media/api-management-howto-cache/api-management-policy-editor.png
[api-management-developer-portal-menu]: ./media/api-management-howto-cache/api-management-developer-portal-menu.png
[api-management-apis-echo-api]: ./media/api-management-howto-cache/api-management-apis-echo-api.png
[api-management-open-console]: ./media/api-management-howto-cache/api-management-open-console.png
[api-management-console]: ./media/api-management-howto-cache/api-management-console.png


[How to add operations to an API]: api-management-howto-add-operations.md
[How to add and publish a product]: api-management-howto-add-products.md
[Monitoring and analytics]: api-management-monitoring.md
[Add APIs to a product]: api-management-howto-add-products.md#add-apis
[Publish a product]: api-management-howto-add-products.md#publish-product
[Introduktion til Azure API Management]: api-management-get-started.md

[Administration af API politikreference]: https://msdn.microsoft.com/library/azure/dn894081.aspx
[Cachelagring af politikker]: https://msdn.microsoft.com/library/azure/dn894086.aspx

[Oprette en API Management service-forekomst]: api-management-get-started.md#create-service-instance

[Configure an operation for caching]: #configure-caching
[Review the caching policies]: #caching-policies
[Call an operation and test the caching]: #test-operation
[Next steps]: #next-steps
