<properties 
    pageTitle="Sådan Godkend udvikler konti med OAuth 2.0 i Azure API Management" 
    description="Lær at tillade brugere ved hjælp af OAuth 2.0 API administration." 
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

# <a name="how-to-authorize-developer-accounts-using-oauth-20-in-azure-api-management"></a>Sådan Godkend udvikler konti med OAuth 2.0 i Azure API Management

Mange API'er understøtter [OAuth 2.0](http://oauth.net/2/) for at sikre API og sikre, at kun gyldige brugere får adgang, og de kan kun få adgang til ressourcer, som de er berettiget. For at kunne bruge Azure API Management interaktive udvikler konsol med disse API'er tjenesten giver dig mulighed at konfigurere din tjenesteforekomst for at arbejde med din OAuth 2.0 aktiveret API.

## <a name="prerequisites"> </a>Forudsætninger

Denne vejledning viser, hvordan du konfigurerer din API Management service forekomst for at bruge OAuth 2.0 godkendelse til udvikler konti, men viser ikke, hvordan du konfigurerer en OAuth 2.0-udbyder. Konfiguration for hver OAuth 2.0-udbyder er forskellige, selvom trinene er de samme, og de nødvendige dele af oplysninger, der bruges i konfigurere OAuth 2.0 i din API Management service forekomst er de samme. Dette emne viser eksempler på brug af Azure Active Directory som en OAuth 2.0-udbyder.

>[AZURE.NOTE] Du kan finde flere oplysninger om konfiguration af OAuth 2.0 ved hjælp af Azure Active Directory, [WebApp-GraphAPI-DotNet][] eksempel.

## <a name="step1"> </a>Konfigurere en server til OAuth 2.0 godkendelse i API administration

For at komme i gang skal du klikke på **Administrer** i portalen Azure klassisk for din API Management-tjenesten. Du føres til portalen API Management publisher.

![Publisher-portalen][api-management-management-console]

>[AZURE.NOTE] Hvis du endnu ikke har oprettet en API Management service-forekomst, kan du se [oprette en API Management service forekomst][] i [Introduktion til Azure API Management][] selvstudiet.

Klik på **sikkerhed** i menuen **API administration** i venstre side, skal du klikke på **OAuth 2.0**og klik derefter på **Tilføj godkendelse server**.

![OAuth 2.0][api-management-oauth2]

Når du klikker på **Tilføj godkendelse server**, vises den nye serverformular til godkendelse.

![Ny server][api-management-oauth2-server-1]

Angiv et navn og en valgfri beskrivelse i felterne **navn** og **Beskrivelse** . 

>[AZURE.NOTE] Disse felter bruges til at identificere den OAuth 2.0 godkendelse server i den aktuelle forekomst af API Management service og deres værdier kommer ikke fra OAuth 2.0-serveren.

Angive **klienten registrering URL-adressen**. Denne side er, hvor brugere kan oprette og administrere deres konti og varierer afhængigt af den OAuth 2.0-udbyder, der bruges. **Klient registrering sidens URL-adresse** peger på den side, som brugerne kan bruge til at oprette og konfigurere deres egne konti til OAuth 2.0 udbydere, der understøtter brugeradministration af konti. Visse typer ikke konfigurere eller bruge denne funktion, selvom provideren OAuth 2.0 understøtter det. Angiv en pladsholder URL-adresse her som URL-adressen på dit firma eller en URL-adresse f.eks, hvis udbyderen OAuth 2.0 ikke har brugeradministration af konti, der er konfigureret, `https://placeholder.contoso.com`.

Den næste sektion i formularen indeholder indstillinger for **godkendelseskode give typer**, **godkendelse slutpunkt URL-adresse**og **godkendelse anmodningsmetode** .

![Ny server][api-management-oauth2-server-2]

Angiv den **tilladelse kode give typer** ved at markere den ønskede type. **Godkendelseskode** er angivet som standard.

Indtaste **godkendelse slutpunkt URL-adresse**. Til Azure Active Directory, bliver denne URL-adressen ligner følgende URL-adressen, hvor `<client_id>` erstattes med den klient-id, der identificerer dit program til OAuth 2.0-serveren.

    https://login.windows.net/<client_id>/oauth2/authorize

**Anmodning om godkendelsesmetode** angiver, hvordan anmodningen om godkendelse sendes til OAuth 2.0-serveren. **Få** vælges som standard.

Næste afsnit er, hvor den **Token slutpunkt URL-adresse**, **klienten godkendelsesmetoder**, **adgangstoken sende metode**og **standardområde** er angivet.

![Ny server][api-management-oauth2-server-3]

For en server til Azure Active Directory OAuth 2.0, **Token slutpunkt URL-adresse** får følgende format, hvor `<APPID>` har formatet af `yourapp.onmicrosoft.com`.

    https://login.windows.net/<APPID>/oauth2/token

Standardindstillingen for **klient godkendelsesmetoder** er **grundlæggende**, og **adgangstoken sende metode** er **Authorization-header**. Disse værdier er konfigureret på denne sektion i formularen, sammen med den **standardområde**.

Afsnittet **klient legitimationsoplysninger** indeholder **Klient-ID** og **klienten hemmeligt**, som hentes under processen oprettelse og konfiguration af din OAuth 2.0-server. Når **Klient-ID** og **klienten hemmeligt** er angivet, genereres **redirect_uri** for **godkendelseskode** . Denne URI bruges til at konfigurere svar URL-adressen i din OAuth 2.0 serverkonfiguration.

![Ny server][api-management-oauth2-server-4]

Hvis **godkendelseskode give typer** er indstillet til **ressource ejeradgangskode**, bruges sektionen **ressource ejer adgangskode legitimationsoplysninger** til at angive disse legitimationsoplysninger Ellers kan du lade feltet være tomt.

![Ny server][api-management-oauth2-server-5]

Når formularen er fuldført, skal du klikke på **Gem** for at gemme API Management OAuth 2.0 godkendelse serverkonfiguration. Når konfigurationen af server er gemt, kan du konfigurere API'er for at bruge denne konfiguration, som vist i næste afsnit.

## <a name="step2"> </a>Konfigurere en API for at bruge OAuth 2.0 bruger godkendelse

Klik på **API'er** i menuen **API administration** i venstre side, klik på navnet på den ønskede API, skal du klikke på **sikkerhed**og markér derefter feltet til **OAuth 2.0**.

![Bruger godkendelse][api-management-user-authorization]

Vælg den ønskede **godkendelse server** på rullelisten, og klik på **Gem**.

![Bruger godkendelse][api-management-user-authorization-save]

## <a name="step3"> </a>Teste OAuth 2.0 bruger godkendelse i portalen udvikler

Når du har konfigureret din OAuth 2.0 godkendelse server og konfigureret din API for at bruge serveren, kan du tester den ved at gå til portalen udvikler og kalder en API.  Klik på **udvikler portal** i øverste højre-menuen.

![Udvikler portal][api-management-developer-portal-menu]

Klik på **API'er** i den øverste menu, og vælg **Ekkoet API**.

![Ekkoet API][api-management-apis-echo-api]

>[AZURE.NOTE] Hvis du har kun én API konfigurerede eller synlige for din konto, fører derefter klikke på API'er dig direkte til handlinger for denne API.

Vælg handlingen **Få ressource** , klik på **Åbn konsol**, og vælg derefter **godkendelseskode** fra rullelisten.

![Åben konsol][api-management-open-console]

Når **tilladelsen kode** er markeret, vises en pop op-vindue med form logon af OAuth 2.0-udbyder. I dette eksempel leveres formularen logon af Azure Active Directory.

>[AZURE.NOTE] Hvis du har pop op-vinduer deaktiveret, bliver du bedt om at aktivere dem ved at browseren. Når du aktiverer dem, vises Vælg **godkendelseskode** igen og logge på formularen.

![Log på][api-management-oauth2-signin]

Når du har logget på, **anmodning om sidehoveder** er udfyldt med en `Authorization : Bearer` sidehoved, der godkender anmodningen.

![Anmode om sidehoved token][api-management-request-header-token]

På dette tidspunkt kan du konfigurere de ønskede værdier for de resterende parametre og sende anmodningen. 

## <a name="next-steps"></a>Næste trin

Kan finde flere oplysninger om brug af OAuth 2.0 og API administration i den følgende video og tilhørende [artikel](api-management-howto-protect-backend-with-aad.md).

> [AZURE.VIDEO protecting-web-api-backend-with-azure-active-directory-and-api-management]

[api-management-management-console]: ./media/api-management-howto-oauth2/api-management-management-console.png
[api-management-oauth2]: ./media/api-management-howto-oauth2/api-management-oauth2.png
[api-management-user-authorization]: ./media/api-management-howto-oauth2/api-management-user-authorization.png
[api-management-user-authorization-save]: ./media/api-management-howto-oauth2/api-management-user-authorization-save.png
[api-management-oauth2-signin]: ./media/api-management-howto-oauth2/api-management-oauth2-signin.png
[api-management-request-header-token]: ./media/api-management-howto-oauth2/api-management-request-header-token.png
[api-management-developer-portal-menu]: ./media/api-management-howto-oauth2/api-management-developer-portal-menu.png
[api-management-open-console]: ./media/api-management-howto-oauth2/api-management-open-console.png
[api-management-oauth2-server-1]: ./media/api-management-howto-oauth2/api-management-oauth2-server-1.png
[api-management-oauth2-server-2]: ./media/api-management-howto-oauth2/api-management-oauth2-server-2.png
[api-management-oauth2-server-3]: ./media/api-management-howto-oauth2/api-management-oauth2-server-3.png
[api-management-oauth2-server-4]: ./media/api-management-howto-oauth2/api-management-oauth2-server-4.png
[api-management-oauth2-server-5]: ./media/api-management-howto-oauth2/api-management-oauth2-server-5.png
[api-management-apis-echo-api]: ./media/api-management-howto-oauth2/api-management-apis-echo-api.png


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

[Prerequisites]: #prerequisites
[Configure an OAuth 2.0 authorization server in API Management]: #step1
[Configure an API to use OAuth 2.0 user authorization]: #step2
[Test the OAuth 2.0 user authorization in the Developer Portal]: #step3
[Next steps]: #next-steps

