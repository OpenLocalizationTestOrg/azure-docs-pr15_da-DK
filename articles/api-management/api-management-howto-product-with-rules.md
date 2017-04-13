<properties
    pageTitle="Beskytte din API med Azure API Management | Microsoft Azure"
    description="Lær, hvordan du beskytter din API med kvoter og (throttling) (rente begrænse) politikker."
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

# <a name="protect-your-api-with-rate-limits-using-azure-api-management"></a>Beskytte din API med rente begrænsninger ved hjælp af Azure API Management

Denne vejledning viser, hvor nemt det er at føje beskyttelse til din back-end API ved at konfigurere rente grænse og kvote politikker med Azure API Management.

Du skal oprette et "Gratis prøveversion" API-produkt, der gør det muligt for udviklere til at ringe op til 10 i minuttet og op til maksimalt 200 opkald om ugen til din API ved hjælp af [grænse opkald i hver abonnement](https://msdn.microsoft.com/library/azure/dn894078.aspx#LimitCallRate) og [angive kvote per abonnement](https://msdn.microsoft.com/library/azure/dn894078.aspx#SetUsageQuota) politikker i dette selvstudium. Du kan derefter publicere API og teste den rente politik.

Du kan finde mere avancerede variere den benyttede scenarier ved hjælp af politikkerne, der [rente grænse ved nøgle](https://msdn.microsoft.com/library/azure/dn894078.aspx#LimitCallRateByKey) og [kvote-ved-nøgle](https://msdn.microsoft.com/library/azure/dn894078.aspx#SetUsageQuotaByKey) , [Avancerede anmodning (throttling) med Azure API Management](api-management-sample-flexible-throttling.md).

## <a name="create-product"> </a>Til at oprette et produkt

I dette trin skal oprette du en gratis prøveversion, der ikke kræver godkendelse af abonnement.

>[AZURE.NOTE] Hvis du allerede har et produkt, der er konfigureret, og du vil bruge den til dette selvstudium, kan du springe frem til at [konfigurere ringe rente grænse og kvote politikker][] og følge selvstudiet derfra ved hjælp af dit produkt i stedet for gratis prøveversion.

For at komme i gang skal du klikke på **Administrer** i Azure klassisk for din API Management-tjenesten. Du føres til portalen API Management publisher.

![Publisher-portalen][api-management-management-console]

>Hvis du endnu ikke har oprettet en API Management service-forekomst, kan du se [oprette en API Management service forekomst][] i [Administrer din første API i Azure API Management][] selvstudium.

Klik på **produkter** i menuen **Administration af API** til venstre for at få vist siden **produkter** .

![Tilføje produkt][api-management-add-product]

Klik på **Tilføj produkt** for at få vist dialogboksen **Tilføj nyt produkt** .

![Tilføje nyt produkt][api-management-new-product-window]

Skriv **Gratis prøveversion**, i feltet **Titel** .

Skriv følgende tekst i feltet **Beskrivelse** :  **abonnenter vil kunne køre 10 opkald/minut op til maksimalt 200 opkald/uge efter hvilket adgang nægtet.**

Administration af API-produkter kan være beskyttet eller åbne. Beskyttet produkter skal abonnere på, før de kan bruges. Åbn produkter kan bruges uden et abonnement. Sørg for, at **kræver abonnement** er markeret til at oprette et beskyttet produkt, der kræver et abonnement. Dette er standardindstillingen for.

Hvis du vil en administrator til at gennemgå og acceptere eller afvise abonnement forsøg på at dette produkt, kan du vælge at **kræve godkendelse af abonnement**. Hvis afkrydsningsfeltet ikke er markeret, bliver abonnementet forsøg automatisk godkendt. I dette eksempel godkendes abonnementer automatisk, så du skal ikke markere feltet.

For at give udvikler konti, for at abonnere flere gange på det nye produkt, skal du markere afkrydsningsfeltet **Tillad flere samtidige abonnementer** . Dette selvstudium ikke udnytte flere samtidige abonnementer, så lad den være umarkerede.

Når alle værdier er angivet, skal du klikke på **Gem** for at oprette produktet.

![Produkt, der er tilføjet][api-management-product-added]

Nye produkter kan ses af brugere i gruppen **Administratorer** som standard. Vi vil du tilføje gruppen **udviklere** . Klik på **Gratis prøveversion**, og klik derefter på fanen **synlighed** .

>Grupper bruges i administration af API til at administrere synligheden af produkter til udviklere. Produkter Giv synlighed til grupper, og udviklere kan få vist og abonnere på de produkter, der er synlige for de grupper, de tilhører. Se, [hvordan du oprette og bruge grupper i Azure API Management][]kan finde flere oplysninger.

![Tilføje udviklergruppe][api-management-add-developers-group]

Markér afkrydsningsfeltet **udviklere** , og klik derefter på **Gem**.

## <a name="add-api"> </a>Til at føje en API til produktet

I dette trin i selvstudiet føjer vi ekkoet API til det nye gratis prøveversion produkt.

>Hver forekomst af API Management service leveres forudkonfigurerede med en ekkoet API, der kan bruges til at eksperimentere med og få mere at vide om administration af API. Se [administrere dine første API i Azure API Management][]kan finde flere oplysninger.

Klik på **produkter** i menuen **Administration af API** til venstre, og klik derefter på **Gratis prøveversion** for at konfigurere produktet.

![Konfigurere produkt][api-management-configure-product]

Klik på **Tilføj API til produkt**.

![Føje API til produkt][api-management-add-api]

Vælg **Ekkoet API**, og klik derefter på **Gem**.

![Tilføje ekko API][api-management-add-echo-api]

## <a name="policies"> </a>Til at konfigurere opkald rente grænse og kvote politikker

Begrænsninger for rente og kvoter er konfigureret i politikeditoren. Klik på **politikker** under menuen **API administration** i venstre side. Klik på **Gratis prøveversion** **på produktlisten** .

![Produktpolitik][api-management-product-policy]

Klik på **Tilføj politik** for at importere skabelonen politik og begynder at oprette rente grænse og kvote politikker.

![Tilføje politik][api-management-add-policy]

For at indsætte politikker skal du placere markøren i enten på **indgående** eller **udgående** sektion på skabelonen, politik. Rente grænse og kvote politikker, der er indgående politikker, så du placere markøren i det indgående element.

![Politik editor][api-management-policy-editor-inbound]

De to politikker vi tilføjer i dette selvstudium er politikkerne, der [grænse opkald i hver abonnement](https://msdn.microsoft.com/library/azure/dn894078.aspx#LimitCallRate) , og [Angiv kvote for hver abonnement](https://msdn.microsoft.com/library/azure/dn894078.aspx#SetUsageQuota) .

![Politik sætninger][api-management-limit-policies]

Når markøren er placeret i elementet **indgående** politik, klik på pilen ud for **grænse opkald i hver abonnement** til at indsætte dens politikskabelon.

    <rate-limit calls="number" renewal-period="seconds">
    <api name="name" calls="number">
    <operation name="name" calls="number" />
    </api>
    </rate-limit>

**Grænse opkald i hver abonnement** kan bruges på niveauet for produkt og kan også bruges i API og individuelle handlingen navn niveauer. Kun produkt-niveau politikker bruges i dette selvstudium, så slette **api** og **drift** elementerne fra elementet **rente grænse** , så kun de yderste **grænsen** elementet forbliver, som vist i følgende eksempel.

    <rate-limit calls="number" renewal-period="seconds">
    </rate-limit>

I gratis prøveversion den maksimale tilladte opkald rente er 10 opkald i minuttet, og Skriv så **10** som værdien for attributten **opkald** og **60** for attributten **fornyelse periode** .

    <rate-limit calls="10" renewal-period="60">
    </rate-limit>

Placer markøren direkte under det tilføjede **grænsen** element i elementet **indgående** for at konfigurere politikken **angive kvote for hver abonnement** skal, og klik derefter på pilen til venstre for at **angive kvote for hver abonnement**.

    <quota calls="number" bandwidth="kilobytes" renewal-period="seconds">
    <api name="name" calls="number" bandwidth="kilobytes">
    <operation name="name" calls="number" bandwidth="kilobytes" />
    </api>
    </quota>

Da denne politik er også skal være på niveauet for produktet, slette **api** og **drift** navn elementerne, som vist i følgende eksempel.

    <quota calls="number" bandwidth="kilobytes" renewal-period="seconds">
    </quota>

Kvoter kan baseres på antallet af kald per interval, båndbredde eller begge dele. Vi (throttling) ikke på basis af båndbredde i dette selvstudium, så Slet attributten **båndbredde** .

    <quota calls="number" renewal-period="seconds">
    </quota>

I gratis prøveversion er kvoten 200 opkald om ugen. Angiv **200** som værdi for attributten **opkald** , og derefter angive **604800** som værdien for attributten **fornyelse periode** .

    <quota calls="200" renewal-period="604800">
    </quota>

>Politik intervaller er angivet i sekunder. Hvis du vil beregne intervallet for en uge, kan du multiplicere antallet af dage (7) med antallet timer på en dag (24) med antallet af minutter i en time (60) med antallet sekunder i minutter (60): 7 *24* 60 * 60 = 604800.

Når du er færdig med at konfigurere politikken, skal den svarer til eksemplet nedenfor.

    <policies>
        <inbound>
            <rate-limit calls="10" renewal-period="60">
            </rate-limit>
            <quota calls="200" renewal-period="604800">
            </quota>
            <base />

    </inbound>
    <outbound>

        <base />

        </outbound>
    </policies>

Klik på **Gem**, når de ønskede politikker er konfigureret.

![Gemme politik][api-management-policy-save]

## <a name="publish-product"></a> Til at publicere produktet

Nu, hvor den API'erne er tilføjet, og politikkerne, der er konfigureret, produktet skal publiceres, så den kan bruges af udviklere. Klik på **produkter** i menuen **Administration af API** til venstre, og klik derefter på **Gratis prøveversion** for at konfigurere produktet.

![Konfigurere produkt][api-management-configure-product]

Klik på **Publicer**, og klik derefter på **Ja, publicere den** for at bekræfte.

![Publicere produkt][api-management-publish-product]

## <a name="subscribe-account"> </a>Abonnere en udvikler konto til produktet

Nu, hvor produktet er blevet publiceret, er tilgængelige for et abonnement på og bruges af udviklere.

>Administratorer af en API Management forekomst abonnerer automatisk på hvert produkt. I dette selvstudium trin, vil vi abonnerer på en af de ikke-administrator udvikler konti til gratis prøveversion af produktet. Hvis kontoen udvikler er en del af rollen administratorer, kan du følge sammen med dette trin, selvom du allerede er tilmeldt.

Klik på **brugere** på **API administration** i menuen til venstre, og klik derefter på navnet på din konto, udvikler. I dette eksempel bruger vi **Filtrerer en opgavevisning Gragg** udvikler konto.

![Konfigurere udvikler][api-management-configure-developer]

Klik på **Tilføj abonnement**.

![Tilføj abonnement][api-management-add-subscription-menu]

Vælg **Gratis prøveversion**, og klik derefter på **Abonner**.

![Tilføj abonnement][api-management-add-subscription]

>[AZURE.NOTE] I dette selvstudium er flere samtidige abonnementer ikke aktiveret for gratis prøveversion. Hvis det var det, du ville være bedt om at navngive abonnementet, som vist i følgende eksempel.

![Tilføj abonnement][api-management-add-subscription-multiple]

Når du klikker på **Abonner**, vises produktet på listen **abonnement** til brugeren.

![Abonnement, der er tilføjet][api-management-subscription-added]

## <a name="test-rate-limit"> </a>Til at ringe til en handling og teste grænsen

Nu, hvor gratis prøveversion er konfigureret og udgivet, kan vi ringe nogle handlinger og teste den rente politik.
Skifte til portalen udvikler ved at klikke på **udvikler portal** i menuen øverst til højre.

![Udvikler portal][api-management-developer-portal-menu]

Klik på **API'er** i den øverste menu, og klik derefter på **Ekkoet API**.

![Udvikler portal][api-management-developer-portal-api-menu]

Klik på **Få ressource**, og klik derefter på **Prøv det**.

![Åben konsol][api-management-open-console]

Behold standardnavnet parameterværdier, og vælg derefter dit abonnement nøgle til gratis prøveversion af produktet.

![Abonnement-tasten][api-management-select-key]

>[AZURE.NOTE] Hvis du har flere abonnementer, skal du sørge for at markere nøglen til **Gratis prøveversion**, ellers kan de politikker, der er konfigureret i de forrige trin ikke i kraft.

Klik på **Send**, og derefter få vist svaret. Bemærk **Response-status** for **200 OK**.

![Handlingen resultater][api-management-http-get-results]

Klik på **Send** på en sats, der er større end politikken for rente af 10 opkald i minuttet. Når politikken rente grænse overskrides, returneres statussen svar **429 for mange anmodninger** .

![Handlingen resultater][api-management-http-get-429]

**Svar indhold** angiver det resterende interval, før nye forsøg udføres.

Når politikken rente grænsen på 10 opkald i minuttet er aktiveret, vil mislykkes efterfølgende opkald, indtil 60 sekunder er gået fra først af de 10 vellykkede opkald til produktet før rente blev overskredet. I dette eksempel er det resterende interval 54 sekunder.

## <a name="next-steps"> </a>Næste trin

-   Se en demonstration af at angive begrænsninger for rente og kvoter i den følgende video.

> [AZURE.VIDEO rate-limits-and-quotas]


[api-management-management-console]: ./media/api-management-howto-product-with-rules/api-management-management-console.png
[api-management-add-product]: ./media/api-management-howto-product-with-rules/api-management-add-product.png
[api-management-new-product-window]: ./media/api-management-howto-product-with-rules/api-management-new-product-window.png
[api-management-product-added]: ./media/api-management-howto-product-with-rules/api-management-product-added.png
[api-management-add-policy]: ./media/api-management-howto-product-with-rules/api-management-add-policy.png
[api-management-policy-editor-inbound]: ./media/api-management-howto-product-with-rules/api-management-policy-editor-inbound.png
[api-management-limit-policies]: ./media/api-management-howto-product-with-rules/api-management-limit-policies.png
[api-management-policy-save]: ./media/api-management-howto-product-with-rules/api-management-policy-save.png
[api-management-configure-product]: ./media/api-management-howto-product-with-rules/api-management-configure-product.png
[api-management-add-api]: ./media/api-management-howto-product-with-rules/api-management-add-api.png
[api-management-add-echo-api]: ./media/api-management-howto-product-with-rules/api-management-add-echo-api.png
[api-management-developer-portal-menu]: ./media/api-management-howto-product-with-rules/api-management-developer-portal-menu.png
[api-management-publish-product]: ./media/api-management-howto-product-with-rules/api-management-publish-product.png
[api-management-configure-developer]: ./media/api-management-howto-product-with-rules/api-management-configure-developer.png
[api-management-add-subscription-menu]: ./media/api-management-howto-product-with-rules/api-management-add-subscription-menu.png
[api-management-add-subscription]: ./media/api-management-howto-product-with-rules/api-management-add-subscription.png
[api-management-developer-portal-api-menu]: ./media/api-management-howto-product-with-rules/api-management-developer-portal-api-menu.png
[api-management-open-console]: ./media/api-management-howto-product-with-rules/api-management-open-console.png
[api-management-http-get]: ./media/api-management-howto-product-with-rules/api-management-http-get.png
[api-management-http-get-results]: ./media/api-management-howto-product-with-rules/api-management-http-get-results.png
[api-management-http-get-429]: ./media/api-management-howto-product-with-rules/api-management-http-get-429.png
[api-management-product-policy]: ./media/api-management-howto-product-with-rules/api-management-product-policy.png
[api-management-add-developers-group]: ./media/api-management-howto-product-with-rules/api-management-add-developers-group.png
[api-management-select-key]: ./media/api-management-howto-product-with-rules/api-management-select-key.png
[api-management-subscription-added]: ./media/api-management-howto-product-with-rules/api-management-subscription-added.png
[api-management-add-subscription-multiple]: ./media/api-management-howto-product-with-rules/api-management-add-subscription-multiple.png

[How to add operations to an API]: api-management-howto-add-operations.md
[How to add and publish a product]: api-management-howto-add-products.md
[Monitoring and analytics]: ../api-management-monitoring.md
[Add APIs to a product]: api-management-howto-add-products.md#add-apis
[Publish a product]: api-management-howto-add-products.md#publish-product
[Administrere din første API i Azure API Management]: api-management-get-started.md
[Sådan oprettes og bruge grupper i Azure API Management]: api-management-howto-create-groups.md
[View subscribers to a product]: api-management-howto-add-products.md#view-subscribers
[Get started with Azure API Management]: api-management-get-started.md
[Oprette en API Management service-forekomst]: api-management-get-started.md#create-service-instance
[Next steps]: #next-steps

[Create a product]: #create-product
[Konfigurere opkald rente grænse og kvote politikker]: #policies
[Add an API to the product]: #add-api
[Publish the product]: #publish-product
[Subscribe a developer account to the product]: #subscribe-account
[Call an operation and test the rate limit]: #test-rate-limit

[Limit call rate]: https://msdn.microsoft.com/library/azure/dn894078.aspx#LimitCallRate
[Set usage quota]: https://msdn.microsoft.com/library/azure/dn894078.aspx#SetUsageQuota
