<properties 
    pageTitle="Sådan Godkend udvikler firmaer ved hjælp af Azure Active Directory i Azure API Management" 
    description="Lær at tillade brugere ved hjælp af Azure Active Directory API administration." 
    services="api-management" 
    documentationCenter="API Management" 
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

# <a name="how-to-authorize-developer-accounts-using-azure-active-directory-in-azure-api-management"></a>Sådan Godkend udvikler firmaer ved hjælp af Azure Active Directory i Azure API Management


## <a name="overview"></a>Oversigt
Denne vejledning viser, hvordan du aktiverer adgang til portalen udvikler for alle brugere i en eller flere Active Azure-mapper. Denne vejledning også viser, hvordan du administrere grupper af Azure Active Directory-brugere ved at tilføje eksterne grupper, der indeholder brugerne af Azure Active Directory.

>Du skal have et Azure Active Directory, til at oprette et program, for at fuldføre trinnene i denne vejledning.

## <a name="how-to-authorize-developer-accounts-using-azure-active-directory"></a>Sådan Godkend udvikler firmaer ved hjælp af Azure Active Directory

For at komme i gang skal du klikke på **Administrer** i portalen Azure klassisk for din API Management-tjenesten. Du føres til portalen API Management publisher.

![Publisher-portalen][api-management-management-console]

>Hvis du endnu ikke har oprettet en API Management service-forekomst, kan du se [oprette en API Management service forekomst][] i [Introduktion til Azure API Management][] selvstudiet.

Klik på **sikkerhed** fra menuen **API administration** i venstre side, og klik på **Eksterne identiteter**.

![Eksterne identiteter][api-management-security-external-identities]

Klik på **Azure Active Directory**. Skal du notere **Omdirigere URL-adresse** og skifte til din Azure Active Directory i portalen Azure klassisk.

![Eksterne identiteter][api-management-security-aad-new]

Klik på knappen **Tilføj** for at oprette et nyt Azure Active Directory-program, og vælg **Tilføj et program, udvikling af min organisation**.

![Tilføje nye Azure Active Directory-program][api-management-new-aad-application-menu]

Angiv et navn til programmet, Vælg **webprogrammet og/eller Web API**, og klik på knappen Næste.

![Nyt Azure Active Directory-program][api-management-new-aad-application-1]

Angiv Webadressen Enkeltlogon for din udvikler portal **Sign-on URL-adressen**til. I dette eksempel **Sign-on URL-adresse** er `https://aad03.portal.current.int-azure-api.net/signin`. 

Angiv standarddomænet eller et brugerdefineret domæne til Azure Active Directory **App ID URL-adressen**til, og Føj en entydig streng til den. I dette eksempel bruges standarddomænet af **https://contoso5api.onmicrosoft.com** med suffikset i **/api** angivet.

![Egenskaber for ny Azure Active Directory-program][api-management-new-aad-application-2]

Klik på knappen Kontrollér for at gemme og oprette det nye program og skifte til fanen **Konfigurer** til at konfigurere det nye program.

![Nyt Azure Active Directory-program, der er oprettet][api-management-new-aad-app-created]

Hvis flere Azure Active kataloger skal bruges til dette program, skal du klikke på **Ja** for **programmet er flere lejer**. Standard er **Nej**.

![Programmet er flere lejer][api-management-aad-app-multi-tenant]

Kopier **Omdirigere URL-adressen** fra sektionen **Azure Active Directory** under fanen **Eksterne identiteter** i portalen publisher, og sæt dem ind i tekstfeltet **Svar URL-adresse** . 

![Svar URL-adresse][api-management-aad-reply-url]

Rul ned til bunden af fanen Konfigurer, Vælg **Tilladelser for tjenesteprogram** for rullelisten, og Markér **læse directory data**.

![Tilladelser for tjenesteprogram][api-management-aad-app-permissions]

Vælg rullemenuen **Stedfortrædertilladelser** , og Markér **Aktivér enkeltlogon og læse brugernes profiler**.

![Delegeret tilladelser][api-management-aad-delegated-permissions]

>Se [få adgang til API Graph][]kan finde flere oplysninger om programmet og delegeret tilladelser.

Kopiere **Klient-Id** til Udklipsholder.

![Klient-Id][api-management-aad-app-client-id]

Skift tilbage til portalen publisher, og sæt ind i **Klient-Id** har kopieret fra programkonfiguration Azure Active Directory.

![Klient-Id][api-management-client-id]

Skift tilbage til Azure Active Directory-konfiguration, og klik på **Vælg varighed** rullemenuen i sektionen **nøgler** og angive et interval. I dette eksempel bruges **1 år** .

![Nøgle][api-management-aad-key-before-save]

Klik på **Gem** for at gemme konfigurationen og vise tasten. Kopiere nøglen til Udklipsholder.

>Skal du notere denne tast. Når du lukker vinduet Azure Active Directory konfiguration, kan du vises tasten igen.

![Nøgle][api-management-aad-key-after-save]

Skift tilbage til portalen publisher, og Indsæt tasten i tekstfeltet **Klient hemmeligt** .

![Klient hemmeligt][api-management-client-secret]

**Tilladt lejere** angiver, hvilke mapper har adgang til API'erne forekomsten af API administration. Angiv domænerne for Azure Active Directory forekomster, du vil give adgang. Du kan adskille flere domæner med linjeskift, mellemrum eller kommaer.

![Tilladte lejere][api-management-client-allowed-tenants]

Du kan angive flere domæner i afsnittet **Tilladte lejere** . Før alle brugere kan logge på fra et andet domæne end det oprindelige domæne, hvor programmet blev registreret, skal en global administrator for det andet domæne give tilladelse til programmet til access directory data. Hvis du vil give tilladelse, skal en global administrator log på programmet og klikke på **Acceptér**. I følgende eksempel `miaoaad.onmicrosoft.com` er blevet føjet til **Tilladt lejere** og en global administrator fra dette domæne logge på for første gang.

![Tilladelser][api-management-permissions-form]

>Hvis en ikke-global administrator forsøger at logge på, før tilladelser tildeles en global administrator, logonforsøg mislykkes, og en fejl logonskærmbilledet vises.

Klik på **Gem**, når den ønskede konfiguration er angivet.

![Gem][api-management-client-allowed-tenants-save]

Når ændringerne er gemt, kan brugerne i den angivne Azure Active Directory log på portalen udvikler ved at følge trinnene i [Log på portalen udvikler ved hjælp af en Azure Active Directory-konto][].

## <a name="how-to-add-an-external-azure-active-directory-group"></a>Sådan føjer du en ekstern Azure Active Directory-gruppe

Når du har aktiveret adgangen for brugere i en Azure Active Directory, kan du tilføje Azure Active Directory-grupper i administration af API til nemmere at administrere tilknytningen af udviklere i gruppen med de ønskede produkter.

> For at konfigurere en ekstern Azure Active Directory-gruppe, skal Azure Active Directory først være konfigureret på fanen identiteter ved at følge fremgangsmåden i det forrige afsnit. 

Eksterne Azure Active Directory-grupper føjes fra fanen **synlighed** af det produkt, du vil give adgang til gruppen. Klik på **produkter**, og klik derefter på navnet på det ønskede produkt.

![Konfigurere produkt][api-management-configure-product]

Skifte til fanen **synlighed** , og klik på **Tilføj grupper fra Azure Active Directory**.

![Tilføj grupper][api-management-add-groups]

Vælg **Azure Active Directory-lejer** på rullelisten, og skriv derefter navnet på den ønskede gruppe på listen **grupper** til at blive føjet tekstfelt.

![Vælg gruppe][api-management-select-group]

Denne gruppenavnet, kan findes på listen **grupper** for din Azure Active Directory, som vist i følgende eksempel.

![Listen af Azure Active Directory-grupper][api-management-aad-groups-list]

Klik på **Tilføj** for at validere gruppenavn og tilføje gruppen. I dette eksempel **Contoso 5 udviklere** tilføjes eksterne gruppe. 

![Gruppe, der er tilføjet][api-management-aad-group-added]

Klik på **Gem** for at gemme den nye Gruppemarkering.

Når en Azure Azure Active Directory-gruppe, som er blevet konfigureret fra ét produkt, er den tilgængelig kontrolleres under fanen **synlighed** for de andre produkter i forekomsten af API administration.

Hvis du vil gennemse, og konfigurere egenskaberne for eksterne grupper, når de er tilføjet, klik på navnet på gruppen under fanen **grupper** .

![Administrere grupper][api-management-groups]

Her kan du redigere **navn** og en **Beskrivelse** af gruppen.

![Rediger gruppe][api-management-edit-group]

Brugere fra konfigurerede Azure Active Directory kan logge på udvikler portalen og få vist og abonnere på en hvilken som helst grupper, de har synlighed ved at følge vejledningen i følgende afsnit.

## <a name="how-to-log-in-to-the-developer-portal-using-an-azure-active-directory-account"></a>Sådan log på portalen udvikler ved hjælp af en Azure Active Directory-konto

Åbn et nyt browservindue, brug af **Sign-on URL-adressen** fra programkonfiguration Active Directory for at logge på portalen udvikler ved hjælp af en Azure Active Directory-konto, der er konfigureret i ovenstående afsnit, og klik **Azure Active Directory**.

![Udvikler Portal][api-management-dev-portal-signin]

Angive legitimationsoplysninger for et af brugerne i din Azure Active Directory, og klik på **Log på**.

![Log på][api-management-aad-signin]

Du kan blive bedt om med en registreringsformular Hvis eventuelle yderligere oplysninger er påkrævet. Udfyld registreringsformularen, og klik på **Tilmeld dig**.

![Registrering][api-management-complete-registration]

Brugeren er nu logget på portalen udvikler til din API Management service forekomst.

![Registreringen er fuldført][api-management-registration-complete]



[api-management-management-console]: ./media/api-management-howto-aad/api-management-management-console.png
[api-management-security-external-identities]: ./media/api-management-howto-aad/api-management-security-external-identities.png
[api-management-security-aad-new]: ./media/api-management-howto-aad/api-management-security-aad-new.png
[api-management-new-aad-application-menu]: ./media/api-management-howto-aad/api-management-new-aad-application-menu.png
[api-management-new-aad-application-1]: ./media/api-management-howto-aad/api-management-new-aad-application-1.png
[api-management-new-aad-application-2]: ./media/api-management-howto-aad/api-management-new-aad-application-2.png
[api-management-new-aad-app-created]: ./media/api-management-howto-aad/api-management-new-aad-app-created.png
[api-management-aad-app-permissions]: ./media/api-management-howto-aad/api-management-aad-app-permissions.png
[api-management-aad-app-client-id]: ./media/api-management-howto-aad/api-management-aad-app-client-id.png
[api-management-client-id]: ./media/api-management-howto-aad/api-management-client-id.png
[api-management-aad-key-before-save]: ./media/api-management-howto-aad/api-management-aad-key-before-save.png
[api-management-aad-key-after-save]: ./media/api-management-howto-aad/api-management-aad-key-after-save.png
[api-management-client-secret]: ./media/api-management-howto-aad/api-management-client-secret.png
[api-management-client-allowed-tenants]: ./media/api-management-howto-aad/api-management-client-allowed-tenants.png
[api-management-client-allowed-tenants-save]: ./media/api-management-howto-aad/api-management-client-allowed-tenants-save.png
[api-management-aad-delegated-permissions]: ./media/api-management-howto-aad/api-management-aad-delegated-permissions.png
[api-management-dev-portal-signin]: ./media/api-management-howto-aad/api-management-dev-portal-signin.png
[api-management-aad-signin]: ./media/api-management-howto-aad/api-management-aad-signin.png
[api-management-complete-registration]: ./media/api-management-howto-aad/api-management-complete-registration.png
[api-management-registration-complete]: ./media/api-management-howto-aad/api-management-registration-complete.png
[api-management-aad-app-multi-tenant]: ./media/api-management-howto-aad/api-management-aad-app-multi-tenant.png
[api-management-aad-reply-url]: ./media/api-management-howto-aad/api-management-aad-reply-url.png
[api-management-permissions-form]: ./media/api-management-howto-aad/api-management-permissions-form.png
[api-management-configure-product]: ./media/api-management-howto-aad/api-management-configure-product.png
[api-management-add-groups]: ./media/api-management-howto-aad/api-management-add-groups.png
[api-management-select-group]: ./media/api-management-howto-aad/api-management-select-group.png
[api-management-aad-groups-list]: ./media/api-management-howto-aad/api-management-aad-groups-list.png
[api-management-aad-group-added]: ./media/api-management-howto-aad/api-management-aad-group-added.png
[api-management-groups]: ./media/api-management-howto-aad/api-management-groups.png
[api-management-edit-group]: ./media/api-management-howto-aad/api-management-edit-group.png

[How to add operations to an API]: api-management-howto-add-operations.md
[How to add and publish a product]: api-management-howto-add-products.md
[Monitoring and analytics]: api-management-monitoring.md
[Add APIs to a product]: api-management-howto-add-products.md#add-apis
[Publish a product]: api-management-howto-add-products.md#publish-product
[Introduktion til Azure API Management]: api-management-get-started.md
[API Management policy reference]: api-management-policy-reference.md
[Caching policies]: api-management-policy-reference.md#caching-policies
[Oprette en API Management service-forekomst]: api-management-get-started.md#create-service-instance

[http://oauth.net/2/]: http://oauth.net/2/
[WebApp-GraphAPI-DotNet]: https://github.com/AzureADSamples/WebApp-GraphAPI-DotNet
[Få adgang til grafen API]: http://msdn.microsoft.com/library/azure/dn132599.aspx#BKMK_Graph

[Prerequisites]: #prerequisites
[Configure an OAuth 2.0 authorization server in API Management]: #step1
[Configure an API to use OAuth 2.0 user authorization]: #step2
[Test the OAuth 2.0 user authorization in the Developer Portal]: #step3
[Next steps]: #next-steps

[Log på portalen udvikler ved hjælp af en Azure Active Directory-konto]: #Log-in-to-the-Developer-portal-using-an-Azure-Active-Directory-account

