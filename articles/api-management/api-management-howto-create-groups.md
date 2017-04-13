<properties 
    pageTitle="Sådan oprettes og bruge grupper til at administrere udvikler konti i Azure API Management" 
    description="Lær, hvordan du administrere udvikler konti ved hjælp af grupper i Azure API Management" 
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

# <a name="how-to-create-and-use-groups-to-manage-developer-accounts-in-azure-api-management"></a>Sådan oprettes og bruge grupper til at administrere udvikler konti i Azure API Management

Grupper bruges i administration af API til at administrere synligheden af produkter til udviklere. Produkter er først synligt for grupper, og klik derefter udviklere i disse grupper kan få vist og abonnere på de produkter, der er knyttet til grupperne. 

Administration af API har følgende fast systemgrupper.

-   **Administratorer** – Azure abonnement administratorer er medlemmer af denne gruppe. Administratorer administrere API Management service forekomster, oprette API'er, handlinger og produkter, der bruges af udviklere.
-   **Udviklere** - godkendte udvikler portal brugere falder inden for denne gruppe. Udviklere er de kunder, opbygger programmer, der bruger din API'er. Udviklere får adgang til portalen udvikler og opbygge programmer, der kalder en API operationer.
-   **Gæster** - portal ikke-godkendte udvikler-brugere, som kundeemner besøger portalen udvikler af en forekomst, API Management falder inden for denne gruppe. De kan få tildelt bestemte skrivebeskyttet adgang, som muligheden for at få vist API'er, men ikke ringe til dem.

Ud over systemgrupperne, kan administratorer oprette brugerdefinerede grupper eller [udnytte eksterne grupper i tilknyttede Azure Active Directory-lejere][]. Brugerdefineret og eksterne grupper kan bruges sammen med systemgrupper i give udviklere synlighed og adgang til API-produkter. For eksempel kan du oprette en brugerdefineret gruppe for udviklere, der er knyttet til en bestemt partnerorganisation og give dem adgang til API'erne fra et produkt, der indeholder relevante API'er. En bruger kan være medlem af mere end én gruppe.

Denne vejledning viser, hvordan administratorer af en API Management forekomst kan tilføje nye grupper og knytte dem til produkter og udviklere.

>[AZURE.NOTE] Ud over oprettelse og administration af grupper i publisher-portalen, kan du oprette og administrere dine grupper, ved hjælp af objektet API Management REST-API [gruppe](https://msdn.microsoft.com/library/azure/dn776329.aspx) .

## <a name="create-group"> </a>Oprette en gruppe

Hvis du vil oprette en ny gruppe, skal du klikke på **Administrer** i portalen Azure klassisk for din API Management-tjenesten. Du føres til portalen API Management publisher.

![Publisher-portalen][api-management-management-console]

>Hvis du endnu ikke har oprettet en API Management service-forekomst, kan du se [oprette en API Management service forekomst][] i [Introduktion til Azure API Management][] selvstudiet.

Klik på **grupper** i **API Management** menuen til venstre, og klik derefter på **Tilføj gruppe**.

![Tilføj ny gruppe][api-management-add-group]

Angiv et entydigt navn til gruppen og en valgfri beskrivelse, og klik på **Gem**.

![Tilføj ny gruppe][api-management-add-group-window]

Den nye gruppe vises under fanen grupper. Hvis du vil redigere **navnet** eller **beskrivelsen** af gruppen, skal du klikke på navnet på gruppen på listen. Hvis du vil slette gruppen, skal du klikke på **Slet**.

![Gruppe, der er tilføjet][api-management-new-group]

Nu hvor gruppen er oprettet, kan det være knyttet til produkter og udviklere.

## <a name="associate-group-product"> </a>Knytte en gruppe til et produkt

Klik på **produkter** i menuen **Administration af API** til venstre for at knytte en gruppe til et produkt, og klik derefter på navnet på det ønskede produkt.

![Angiv synlighed][api-management-add-group-to-product]

Vælg fanen **indsigt for** at tilføje og fjerne grupper og få vist de aktuelle grupper for produktet. Tilføje eller fjerne grupper, Markér eller fjern markeringen i afkrydsningsfelterne for de ønskede grupper og klik på **Gem**.

![Angiv synlighed][api-management-add-group-to-product-visibility]

>[AZURE.NOTE] Hvis du vil tilføje Azure Active Directory-grupper, se, [hvordan du Godkend udvikler firmaer ved hjælp af Azure Active Directory i Azure API Management](api-management-howto-aad.md).
>
>Hvis du vil konfigurere grupper fra fanen **synlighed** for et produkt, skal du klikke på **Administrer grupper**.

Når et produkt er knyttet til en gruppe, kan udviklere i den pågældende gruppe få vist og abonnere på produktet.

## <a name="associate-group-developer"> </a>Knytte grupper til udviklere

Klik på **brugere** fra menuen **Administration af API** til venstre for at knytte grupper til udviklere, og markér derefter afkrydsningsfeltet ud for udviklere, du vil knytte til en gruppe.

![Føje udvikler til gruppe][api-management-add-group-to-developer]

Når de ønskede udviklere er markeret, skal du klikke på den ønskede gruppe i rullemenuen **Føj til gruppe** . Udviklere kan fjernes fra grupper ved hjælp af den **Fjern fra gruppe** ned. 

![Udviklere][api-management-add-group-to-developer-saved]

Når tilknytningen er tilføjet mellem udvikleren og gruppen, kan du få den vist i fanen **brugere** .


## <a name="next-steps"> </a>Næste trin

-   Når en udvikler er føjet til en gruppe, kan de se og abonnere på de produkter, der er knyttet til denne gruppe. Du kan finde flere oplysninger, se [hvordan oprette og publicere et produkt i Azure API Management][],
-   Ud over oprettelse og administration af grupper i publisher-portalen, kan du oprette og administrere dine grupper, ved hjælp af objektet API Management REST-API [gruppe](https://msdn.microsoft.com/library/azure/dn776329.aspx) .


[api-management-management-console]: ./media/api-management-howto-create-groups/api-management-management-console.png
[api-management-add-group]: ./media/api-management-howto-create-groups/api-management-add-group.png
[api-management-add-group-window]: ./media/api-management-howto-create-groups/api-management-add-group-window.png
[api-management-new-group]: ./media/api-management-howto-create-groups/api-management-new-group.png
[api-management-add-group-to-product]: ./media/api-management-howto-create-groups/api-management-add-group-to-product.png
[api-management-add-group-to-product-visibility]: ./media/api-management-howto-create-groups/api-management-add-group-to-product-visibility.png
[api-management-add-group-to-developer]: ./media/api-management-howto-create-groups/api-management-add-group-to-developer.png
[api-management-add-group-to-developer-saved]: ./media/api-management-howto-create-groups/api-management-add-group-to-developer-saved.png

[api-management-]: ./media/api-management-howto-create-groups/api-management-.png

[Create a group]: #create-group
[Associate a group with a product]: #associate-group-product
[Associate groups with developers]: #associate-group-developer
[Next steps]: #next-steps

[Hvordan oprette og publicere et produkt i Azure API Management]: api-management-howto-add-products.md

[Introduktion til Azure API Management]: api-management-get-started.md
[Oprette en API Management service-forekomst]: api-management-get-started.md#create-service-instance
[udnytte eksterne grupper i tilknyttede Azure Active Directory-lejere]: api-management-howto-aad.md#how-to-add-an-external-azure-active-directory-group
