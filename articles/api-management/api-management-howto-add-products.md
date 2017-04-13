<properties 
    pageTitle="Hvordan du kan oprette og publicere et produkt i Azure API Management" 
    description="Lær at oprette og publicere produkter i Azure API Management." 
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

# <a name="how-to-create-and-publish-a-product-in-azure-api-management"></a>Hvordan du kan oprette og publicere et produkt i Azure API Management

Azure API Administration indeholder et produkt en eller flere API'er samt en kvote for anvendelse og vilkårene for anvendelse. Når et produkt er udgivet, kan udviklere abonnere på produktet og begynde at bruge produktets API'er. Emnet indeholder en vejledning til oprettelse af et produkt, tilføje en API og publicere den for udviklere.

## <a name="create-product"> </a>Opretter et produkt

Handlinger er tilføjet og konfigureret til en API i publisher-portalen. Klik på **Administrer** i portalen Azure klassisk for din API Management-tjenesten for at få adgang til portalen publisher.

![Publisher-portalen][api-management-management-console]

>Hvis du endnu ikke har oprettet en API Management service-forekomst, kan du se [oprette en API Management service forekomst][] i [Introduktion til Azure API Management][] selvstudiet.

Klik på **produkter** i menuen til venstre for at få vist siden **produkter** , og klik på **Tilføj produkt**.

![Produkter][api-management-products]

![Nyt produkt][api-management-add-new-product]

Angiv et beskrivende navn for produktet i feltet **navn** og en beskrivelse af produktet i feltet **Beskrivelse** .

Administration af API-produkter kan være **åben** eller **beskyttet**. Beskyttet produkter skal abonnere på, før de kan bruges, mens du Åbn produkter kan bruges uden et abonnement. Markér **kræver abonnement** for at oprette et beskyttet produkt, der kræver et abonnement. Dette er standardindstillingen for.

Markér **godkendes abonnement** , hvis du vil have en administrator til at gennemgå og acceptere eller afvise abonnement forsøg på at dette produkt. Hvis feltet er markeret, bliver abonnementet forsøg automatisk godkendt. Du kan finde flere oplysninger om abonnementer, [Vis abonnenter på et produkt][].

Markér afkrydsningsfeltet **Tillad flere abonnementer** for at tillade udvikler konti, for at abonnere flere gange på produktet. Hvis dette felt ikke er markeret, kan hver udvikler konto abonnere på et enkelt tidspunkt for produktet.

![Ubegrænset flere abonnementer][api-management-unlimited-multiple-subscriptions]

Markér afkrydsningsfeltet **Begræns antallet af samtidige abonnementer til** for at begrænse antallet af flere samtidig abonnementer, og Angiv grænsen på abonnement. I eksemplet nedenfor er samtidig abonnementer begrænset til fire af hvert udvikler konto.

![Fire flere abonnementer][api-management-four-multiple-subscriptions]

Når alle nye produkt indstillinger er konfigureret, skal du klikke på **Gem** for at oprette det nye produkt.

![Produkter][api-management-products-page]

>Som standard nye produkter er ikke-udgivne, og kun er synlige for **administratorgruppen** .

Hvis du vil konfigurere et produkt, klik på produktnavnet under fanen **produkter** .

## <a name="add-apis"> </a>Tilføje API'er til et produkt

Siden **produkter** indeholder fire links til konfiguration: **Oversigt**, **Indstillinger**, **synlighed**og **abonnenter**. Under fanen **Oversigt** er, hvor du kan tilføje API'er og publicere eller annullere publiceringen af et produkt.

![Oversigt][api-management-new-product-summary]

Før du udgiver dit produkt skal du tilføje en eller flere API'er. For at gøre dette, skal du klikke på **Tilføj API til produkt**.

![Tilføje API'er][api-management-add-apis-to-product]

Vælg de ønskede API'er, og klik på **Gem**.

## <a name="add-description"> </a>Tilføj beskrivende oplysninger til et produkt

Under fanen **Indstillinger** kan du indeholder detaljerede oplysninger om produktet som sit formål, de API'er, den giver adgang til og andre nyttige oplysninger. Indholdet henvender sig til udviklere, der vil kalder API og kan skrives i almindelig tekst eller HTML-kode.

![Indstillinger for produkt][api-management-product-settings]

Markér **kræver abonnement** for at oprette et beskyttet produkt, der kræver et abonnement, der skal bruges, eller fjern markeringen i afkrydsningsfeltet for at oprette et åbent produkt, der kan kaldes uden et abonnement.

Vælg **godkendes abonnement** , hvis du vil godkende manuelt alle anmodninger om produkt-abonnement. Som standard tildelt automatisk alle produkt-abonnementer.

Markér afkrydsningsfeltet **Tillad flere abonnementer** for at tillade udvikler konti, for at abonnere flere gange på produktet, og du kan også angive en grænse. Hvis dette felt ikke er markeret, kan hver udvikler konto abonnere på et enkelt tidspunkt for produktet.

Udfyld eventuelt feltet **vilkår for anvendelse** , der beskriver vilkårene for anvendelse for det produkt, hvilke abonnenter skal acceptere for at bruge produktet.

## <a name="publish-product"> </a>Publicere et produkt

Før kan kaldes API'er i et produkt, skal produktet publiceres. Klik på **Publicer**under fanen **Oversigt** for produktet, og klik derefter på **Ja, publicere den** for at bekræfte. Hvis du vil gøre en tidligere publiceret produkt privat, skal du klikke på **Annuller publicering**.

![Publicere produkt][api-management-publish-product]

## <a name="make-visible"> </a>Gøre et produkt synlig for udviklere

Fanen **synlighed** gør det muligt at vælge, hvilke roller, der kan se produktet på portalen udvikler og abonnere på produktet.

![Produkt synlighed][api-management-product-visiblity]

Kontrollér for at aktivere eller deaktivere synligheden af et produkt for udviklere i en gruppe, eller fjern markeringen i afkrydsningsfeltet ud for gruppen, og klik derefter på **Gem**.

>Se, [hvordan du opretter og bruger grupper for at administrere udvikler konti i Azure API Management][]kan finde flere oplysninger.

## <a name="view-subscribers"> </a>Se abonnenter på et produkt

Fanen **abonnenter** viser de udviklere, som har abonneret på produktet. Detaljer og indstillinger for hver udvikler kan ses ved at klikke på den udvikler navn. I dette eksempel har ingen udviklere endnu abonnerer på produktet.

![Udviklere][api-management-developer-list]

## <a name="next-steps"> </a>Næste trin

Når der tilføjes de ønskede API'er og produktet udgivet, kan udviklere abonnere på produktet og begynde at ringe til API'erne. Se [oprette og konfigurere indstillinger for avanceret produkt i Azure API Management hvordan][]et selvstudium, der viser disse elementer samt avancerede produktkonfiguration.

Du kan finde flere oplysninger om at arbejde med produkter, i den følgende video.

> [AZURE.VIDEO using-products]

[Create a product]: #create-product
[Add APIs to a product]: #add-apis
[Add descriptive information to a product]: #add-description
[Publish a product]: #publish-product
[Make a product visible to developers]: #make-visible
[Vis abonnenter på et produkt]: #view-subscribers
[Next steps]: #next-steps

[api-management-management-console]: ./media/api-management-howto-add-products/api-management-management-console.png
[api-management-add-product]: ./media/api-management-howto-add-products/api-management-add-product.png
[api-management-add-new-product]: ./media/api-management-howto-add-products/api-management-add-new-product.png
[api-management-unlimited-multiple-subscriptions]: ./media/api-management-howto-add-products/api-management-unlimited-multiple-subscriptions.png
[api-management-four-multiple-subscriptions]: ./media/api-management-howto-add-products/api-management-four-multiple-subscriptions.png
[api-management-products-page]: ./media/api-management-howto-add-products/api-management-products-page.png
[api-management-new-product-summary]: ./media/api-management-howto-add-products/api-management-new-product-summary.png
[api-management-add-apis-to-product]: ./media/api-management-howto-add-products/api-management-add-apis-to-product.png
[api-management-product-settings]: ./media/api-management-howto-add-products/api-management-product-settings.png
[api-management-publish-product]: ./media/api-management-howto-add-products/api-management-publish-product.png
[api-management-product-visiblity]: ./media/api-management-howto-add-products/api-management-product-visibility.png
[api-management-developer-list]: ./media/api-management-howto-add-products/api-management-developer-list.png



[api-management-products]: ./media/api-management-howto-add-products/api-management-products.png
[api-management-]: ./media/api-management-howto-add-products/
[api-management-]: ./media/api-management-howto-add-products/


[How to add operations to an API]: api-management-howto-add-operations.md
[How to create and publish a product]: api-management-howto-add-products.md
[Introduktion til Azure API Management]: api-management-get-started.md
[Oprette en API Management service-forekomst]: api-management-get-started.md#create-service-instance
[Next steps]: #next-steps
[Sådan oprettes og bruge grupper til at administrere udvikler konti i Azure API Management]: api-management-howto-create-groups.md
[Hvordan oprette og konfigurere indstillinger for avanceret produkt i Azure API Management]: api-management-howto-product-with-rules.md 