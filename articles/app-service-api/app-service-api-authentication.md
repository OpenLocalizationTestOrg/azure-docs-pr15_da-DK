<properties
    pageTitle="Godkendelse og API Apps i Azure App Service | Microsoft Azure"
    description="Få mere at vide om godkendelse og autorisation tjenesterne, hvor du kan Azure App Service API Apps."
    services="app-service\api"
    documentationCenter=".net"
    authors="tdykstra"
    manager="wpickett"
    editor=""/>

<tags
    ms.service="app-service-api"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="05/23/2016"
    ms.author="rachelap"/>

# <a name="authentication-and-authorization-for-api-apps-in-azure-app-service"></a>Godkendelse og API Apps i Azure App Service

## <a name="overview"></a>Oversigt 

> [AZURE.NOTE] Dette emne bliver overført til en konsolideret [App tjeneste godkendelse / godkendelse](../app-service/app-service-authentication-overview.md) emne, som omhandler Web, Mobile og API Apps.

Azure App Service indeholder indbyggede godkendelses- og tjenester, der implementerer [OAuth 2.0](#oauth) og [OpenID forbindelse](#oauth). I denne artikel beskrives de tjenester og indstillinger, der er tilgængelige for API Apps i Azure App Service.

I følgende diagram vises nogle vigtigste karakteristika for App Service godkendelse:

* Den preprocesses indgående API anmodninger, hvilket betyder, at den fungerer sammen med en hvilken som helst sprog eller framework understøttes af App Service.
* Det giver dig adskillige muligheder for godkendelse af hvor meget arbejde du vil gøre i din egen programkode.
* Det fungerer for både slutbrugerens og godkendelse af service-konto. 
* Understøtter fem identitetsudbydere: Azure Active Directory, Facebook, Google, Twitter og Microsoft-Account.
* Det fungerer på samme for API Apps, Web Apps og Mobile-Apps.

![](./media/app-service-api-authentication/api-apps-overview.png)

## <a name="language-agnostic"></a>Sprog agnostic

App Service godkendelse behandling sker før anmodninger når din API-app, hvilket betyder, at godkendelsesfunktioner fungerer til API-apps, der er skrevet i en hvilken som helst sprog eller framework.  Din API kan være baseret på ASP.NET, Java, Node.js eller en hvilken som helst framework, der understøtter App Service.

App Service overfører på JSON web tokenet (JWT) i overskriften godkendelse af en HTTP-anmodning, og kode, der er skrevet i en hvilken som helst sprog eller framework kan få de nødvendige oplysninger fra tokenet. Desuden giver App Service dig nemmere adgang til de mest almindeligt anvendte krav ved at angive nogle speciel overskrifter, som følgende:

* X-MS-KLIENT-HOVEDSTOLEN-NAVN
* X-MS-KLIENT-HOVEDSTOLEN-ID
* X-MS-TOKEN-FACEBOOK-ACCESS-TOKEN
* X-MS-TOKEN-FACEBOOK-EXPIRES-ON
 
I en .NET-API, kan du bruge den `Authorize` attribut, og om detaljerede tilladelse, du nemt kan skrive kode baseret på krav, fordi krav oplysninger er udfyldt for dig i .NET klasser.

## <a name="multiple-protection-options"></a>Flere indstillinger for beskyttelse

App Service kan forhindre anonyme HTTP-anmodninger fra at kontakte din API-app, men kan videregive alle anmodninger og validere tokens for anmodninger, der indeholder dem, eller det kan lade gennem alle anmodninger uden at gøre noget på dem:

1. Tillad kun godkendte anmodninger om at oprette forbindelse til din API-app.

    Hvis der modtages en anonym anmodning fra en browser, App Service omdirigerer til en logonside for godkendelsesudbyderen af (Azure AD, Google, Twitter, osv.), som du vælger. 

    Med denne indstilling, du behøver ikke at skrive en hvilken som helst godkendelse programkode overhovedet i din app og godkendelseskode er forenklet, fordi de vigtigste krav er angivet på HTTP-overskrifterne.

2. Tillad alle anmodninger om at nå din API-app, men validere godkendte anmodninger og overfører langs oplysninger til godkendelse i HTTP-overskrifterne.

    Denne indstilling giver dig større fleksibilitet i anmodninger om anonym, men du skal skrive programkode, hvis du vil forhindre, at anonyme brugere ved hjælp af din API. Da de mest populære krav er gået i overskrifterne for HTTP-anmodninger, er godkendelseskode relativt nemt.
    
3. Tillad alle anmodninger om at nå dine API, Foretag intet på godkendelsesoplysninger i anmodningerne.

    Denne indstilling lader opgaver af godkendelse og autorisation helt op til din programkode.

[Azure-portalen](https://portal.azure.com/), du vælger indstillingen, du vil have på den **godkendelse / godkendelse** blade.

![](./media/app-service-api-authentication/authblade.png)

Slå **App Service godkendelse**for indstillinger for 1 og 2, og vælg **Log på** eller **Tillad anmodning (ingen handling)**i rullelisten **handling skal udføre, når der ikke er godkendt anmodning om** .  Hvis du vælger **Log på**, er det nødvendigt at vælge en udbyder af godkendelse og konfigurere udbyderen.

![](./media/app-service-api-authentication/actiontotake.png)

Du kan finde detaljerede oplysninger om, hvordan du konfigurerer godkendelse, se, [hvordan du konfigurere din App-tjenesteprogrammet bruge logon til Azure Active Directory](../app-service-mobile/app-service-mobile-how-to-configure-active-directory-authentication.md). I artiklen gælder for API apps samt mobilapps, og den links til andre artikler til de andre godkendelsesprovidere.
 
## <a id="internal"></a>Godkendelse af Service-konto

App Service godkendelse fungerer for interne scenarier f.eks, hvor opkald fra én API-app til en anden API-app. I dette scenarie får du et token ved hjælp af legitimationsoplysninger for en tjenestekonto i stedet for slutbrugerens legitimationsoplysninger. En tjenestekonto er også kendt som en *tjeneste vigtigste* i Azure Active Directory, og godkendelse ved hjælp af denne konto er også kendt som et scenarie med-tjenester. 

Beskytte kaldet API-app ved hjælp af Azure Active Directory-tjenester scenarier, og give en AAD tjeneste vigtigste godkendelse token, når du ringer til API-app. Du får et token ved at give klienten-ID og hemmeligt fra programmet AAD-klienten. Ingen specielle kun Azure-kode er påkrævet, som bruges til at gælde for håndtering af Mobile tjenester Zumo tokenet. Et eksempel på dette scenario, ved hjælp af ASP.NET API apps er dækket af selvstudiet [Service vigtigste godkendelse til API-Apps](app-service-api-dotnet-service-principal-auth.md).

Hvis du vil håndtere et scenarie med-tjenester uden at bruge App Service godkendelse, kan du bruge klientcertifikater eller basisgodkendelse. Du kan finde oplysninger om klientcertifikater i Azure, [Hvordan til at konfigurere TLS Fællesgodkendelse for Web Apps](../app-service-web/app-service-web-configure-tls-mutual-auth.md). Du kan finde oplysninger om basisgodkendelse i ASP.NET [Godkendelse filtre i ASP.NET Web API 2](http://www.asp.net/web-api/overview/security/authentication-filters).

Tjenesten godkendelse-konto fra en App Service logik app til en API-app er et særligt tilfælde, der er beskrevet i [ved hjælp af din brugerdefinerede API hostes på App Service med logik apps](../app-service-logic/app-service-logic-custom-hosted-api.md).

## <a name="mobile-client-authentication"></a>Mobilklient godkendelse

Du kan finde oplysninger om, hvordan du håndterer godkendelse fra mobilklienter [dokumentation om godkendelse for mobilapps](../app-service-mobile/app-service-mobile-ios-get-started-users.md). App Service godkendelse fungerer på samme måde for mobilapps og API apps.
  
## <a name="more-information"></a>Få mere at vide

Du kan finde flere oplysninger om godkendelse og godkendelse i Azure App-tjeneste, se følgende ressourcer:

* [Udvidelse App Service godkendelse / godkendelse](/blog/announcing-app-service-authentication-authorization/)
* [Sådan konfigureres din App-tjenesteprogrammet bruge Azure Active Directory-logon](../app-service-mobile/app-service-mobile-how-to-configure-active-directory-authentication.md) (Indeholder links til andre godkendelsesprovidere øverst på siden). 

Du kan finde flere oplysninger om OAuth 2.0, OpenID forbinde og JSON Web Tokens (JWT), i følgende ressourcer.

* [Introduktion til OAuth 2.0] (http://shop.oreilly.com/product/0636920021810.do "Introduktion til OAuth 2.0") 
* [Introduktion til OAuth2, OpenID forbinde og JSON Web Tokens (JWT) - PluralSight kursus](http://www.pluralsight.com/courses/oauth2-json-web-tokens-openid-connect-introduction) 
* [Opbygge og sikring af en RESTful API til flere forskellige kunder i ASP.NET - PluralSight kursus](http://www.pluralsight.com/courses/building-securing-restful-api-aspdotnet)

Du kan finde flere oplysninger om Azure Active Directory, i følgende ressourcer.

* [Azure AD-scenarier](http://aka.ms/aadscenarios)
* [Azure AD udviklere vejledning](http://aka.ms/aaddev)
* [Azure AD-eksempler](http://aka.ms/aadsamples)

## <a name="next-steps"></a>Næste trin

I denne artikel indeholder forklaring godkendelse og autorisation funktioner til App-tjeneste, du kan bruge til API-apps. Det næste selvstudium i få Introduktion serien viser, hvordan du implementere [brugergodkendelse i App Service API Apps](app-service-api-dotnet-user-principal-auth.md).
