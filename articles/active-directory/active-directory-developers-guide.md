<properties
   pageTitle="Azure Active Directory developer's vejledning | Microsoft Azure"
   description="I denne artikel indeholder en omfattende vejledning til udvikler rundt ressourcer til Azure Active Directory."
   services="active-directory"
   documentationCenter="dev-center-name"
   authors="bryanla"
   manager="mbaldwin"
   editor=""/>

<tags
   ms.service="active-directory"
   ms.devlang="na"
   ms.topic="hero-article"
   ms.tgt_pltfrm="na"
   ms.workload="identity"
   ms.date="10/24/2016"
   ms.author="mbaldwin"/>


# <a name="azure-active-directory-developers-guide"></a>Azure Active Directory developer's vejledning

## <a name="overview"></a>Oversigt
Som en identitet administration som en tjeneste (IDMaaS) platform indeholder Azure Active Directory (AD) udviklere en effektiv måde at integrere identitet management i deres programmer. Følgende artikler indeholder oversigter på implementering og vigtigste funktioner i Azure AD. Vi anbefaler, at du læse dem i rækkefølge eller hoppe til [Kom i gang](#getting-started) , hvis du er klar til at grave.


1. [Fordelene ved Azure AD-integration](./develop/active-directory-how-to-integrate.md): se, hvorfor integration med Azure AD tilbyder den bedste løsning til sikker logon og godkendelse.

1. [Azure AD-godkendelse scenarier](active-directory-authentication-scenarios.md): udnytte forenklet godkendelse i Azure AD til at levere sign-on til dit program.

1. [Integration af programmer med Azure AD](active-directory-integrating-applications.md): Lær at tilføje, opdatere og fjerne programmer fra Azure AD, og om mærkning retningslinjerne for integrerede apps.

1. [Azure AD Graph API](active-directory-graph-api.md): bruger API'EN Azure AD Graph automatisk adgang til Azure AD via REST-API slutpunkter. Azure AD Graph API er også tilgængelig via [Microsoft Graph](https://graph.microsoft.io/). Microsoft Graph giver et samlet API, der giver adgang til flere Microsoft-skytjeneste API'er, via et enkelt REST-API slutpunkt og med en enkelt adgangstoken.

1. [Azure AD-godkendelse biblioteker](active-directory-authentication-libraries.md): nemt godkende brugere for at få adgangstokens ved hjælp af Azure AD-godkendelse biblioteker for .NET, JavaScript, mål-C og Android.


## <a name="getting-started"></a>Kom godt i gang

Disse selvstudier er skræddersyet til flere platforme og kan hjælpe dig med hurtigt starte udvikling med Azure Active Directory. Som en betingelse skal du [få en Azure Active Directory-lejer](active-directory-howto-tenant.md).

### <a name="mobile-and-pc-application-quick-start-guides"></a>Mobile og PC-program Hurtig start hjælpelinjer

|[![iOS](./media/active-directory-developers-guide/ios.png)](active-directory-devquickstarts-ios.md)|[![Android](./media/active-directory-developers-guide/android.png)](active-directory-devquickstarts-android.md)|[![.NET](./media/active-directory-developers-guide/net.png)](active-directory-devquickstarts-dotnet.md)|[![Windows Universal](./media/active-directory-developers-guide/windows.png)](active-directory-devquickstarts-windowsstore.md)|[![Xamarin](./media/active-directory-developers-guide/xamarin.png)](active-directory-devquickstarts-xamarin.md)|[![Cordova](./media/active-directory-developers-guide/cordova.png)](active-directory-devquickstarts-cordova.md)|[![OAuth 2.0](./media/active-directory-developers-guide/oauth-2.png)](active-directory-protocols-oauth-code.md)
|:--:|:--:|:--:|:--:|:--:|:--:|:--:|:--:|
|[iOS](active-directory-devquickstarts-ios.md)|[Android](active-directory-devquickstarts-android.md)|[.NET](active-directory-devquickstarts-dotnet.md)|[Windows Universal](active-directory-devquickstarts-windowsstore.md)|[Xamarin](active-directory-devquickstarts-xamarin.md)|[Cordova](active-directory-devquickstarts-cordova.md)|[Integrere direkte med OAuth 2.0](active-directory-protocols-oauth-code.md)|

### <a name="web-application-quick-start-guides"></a>Web application Hurtig start hjælpelinjer

|[![.NET](./media/active-directory-developers-guide/net.png)](active-directory-devquickstarts-webapp-dotnet.md)|[![Java](./media/active-directory-developers-guide/java.png)](active-directory-devquickstarts-webapp-java.md)|[![AngularJS](./media/active-directory-developers-guide/angularjs.png)](active-directory-devquickstarts-angular.md)|[![JavaScript](./media/active-directory-developers-guide/javascript.png)](https://github.com/Azure-Samples/active-directory-javascript-singlepageapp-dotnet-webapi)|[![Node.js](./media/active-directory-developers-guide/nodejs.png)](active-directory-devquickstarts-openidconnect-nodejs.md) | [![Oprette forbindelse OpenID](./media/active-directory-developers-guide/openid-connect.png)](active-directory-protocols-openid-connect-code.md)
|:--:|:--:|:--:|:--:|:--:|:--:|
|[.NET](active-directory-devquickstarts-webapp-dotnet.md)|[Java](active-directory-devquickstarts-webapp-java.md)|[AngularJS](active-directory-devquickstarts-angular.md)|[JavaScript](https://github.com/Azure-Samples/active-directory-javascript-singlepageapp-dotnet-webapi)|[Node.js](active-directory-devquickstarts-openidconnect-nodejs.md)|[Integrere direkte med OpenID forbindelse](active-directory-protocols-openid-connect-code.md)|

### <a name="web-api-quick-start-guides"></a>Web API Hurtig start hjælpelinjer

|[![.NET](./media/active-directory-developers-guide/net.png)](active-directory-devquickstarts-webapi-dotnet.md)|[![Node.js](./media/active-directory-developers-guide/nodejs.png)](active-directory-devquickstarts-webapi-nodejs.md)
|:--:|:--:|
|[.NET](active-directory-devquickstarts-webapi-dotnet.md)|[Node.js](active-directory-devquickstarts-webapi-nodejs.md)

### <a name="querying-the-directory-quickstart-guide"></a>Forespørgsler guiden directory Hurtig start

| [![.NET](./media/active-directory-developers-guide/graph.png)](active-directory-graph-api-quickstart.md)|
|:--:|
|[Graph API](active-directory-graph-api-quickstart.md)|

## <a name="how-tos"></a>Sådan gør du

Disse artikler beskrives det, hvordan du udfører bestemte opgaver ved hjælp af Azure Active Directory:

- [Få en Azure AD-lejer](active-directory-howto-tenant.md)
- [Logge på en hvilken som helst Azure AD-bruger, der bruger flere lejer programmet mønster](active-directory-devhowto-multi-tenant-overview.md)
- Aktivere kryds-app SSO-Teknologier med ADAL, på [Android](active-directory-sso-android.md) og [iOS](active-directory-sso-ios.md) -enheder
- [Gøre programmet AppSource certificeret til Azure AD](active-directory-devhowto-appsource-certified.md)
- [Liste over dit program i galleriet Azure AD-program](active-directory-app-gallery-listing.md)
- [Sende webapps til Office 365 til sælgeren Dashboard](https://msdn.microsoft.com/office/office365/howto/submit-web-apps-seller-dashboard)
- [Forstå programmanifestet Azure Active Directory](active-directory-application-manifest.md)
- [Forstå mærkning retningslinjerne for knapperne logon og app acquisition i klientprogrammet](active-directory-branding-guidelines.md)
- [Eksempel: Hvordan du opretter apps, som logge brugere på både personlige og arbejds- eller skolekonto-konto](active-directory-appmodel-v2-overview.md)
- [Eksempel: Hvordan du opretter apps, der Tilmeld dig og logge på forbrugere](../active-directory-b2c/active-directory-b2c-overview.md)
- [Preview: konfiguration af sikkerhedstoken levetid i Azure AD](active-directory-configurable-token-lifetimes.md) ved hjælp af PowerShell. Se [politik handlinger](https://msdn.microsoft.com/library/azure/ad/graph/api/policy-operations) og [politik enhed](https://msdn.microsoft.com/library/azure/ad/graph/api/entity-and-complex-type-reference#policy-entity) for at få oplysninger om konfiguration af via Azure AD Graph API.

## <a name="reference"></a>Reference

Disse artikler giver en foundation reference til RESTEN og godkendelse bibliotek API'er, protokoller, fejl, kodeeksempler og slutpunkter.  

###  <a name="support"></a>Support
- [Mærket spørgsmål](http://stackoverflow.com/questions/tagged/azure-active-directory): finde Azure Active Directory-løsninger på stakoverløb ved at søge efter mærker [azure-aktiv-directory](http://stackoverflow.com/questions/tagged/azure-active-directory) og [adal](http://stackoverflow.com/questions/tagged/adal).
- Se [Azure AD udvikler ordliste](active-directory-dev-glossary.md) til definitioner af nogle af de ofte anvendte udtryk, der er relateret til udvikling af programmer og integration.

### <a name="code"></a>Kode

- [Azure Active Directory open source - biblioteker](http://github.com/AzureAD): er den nemmeste måde at søge efter et bibliotek kilde ved hjælp af vores [biblioteksliste](active-directory-authentication-libraries.md).

- [Azure Active Directory eksempler](https://github.com/azure-samples?query=active-directory): den nemmeste måde at navigere på listen over eksempler er ved at bruge [indekset for kodeeksempler](active-directory-code-samples.md).

- [Active Directory Authentication Library (ADAL) til .NET](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet) - referencedokumentation er tilgængelige for både [den seneste overordnet version](https://docs.microsoft.com/active-directory/adal/microsoft.identitymodel.clients.activedirectory) og den [forrige overordnet version](https://docs.microsoft.com/active-directory/adal/v2/microsoft.identitymodel.clients.activedirectory).

### <a name="graph-api"></a>Graph API

- [Graph API reference](https://msdn.microsoft.com/library/azure/hh974476.aspx): RESTEN reference til Azure Active Directory Graph API. [Få vist interaktive Graph API reference oplevelse](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/api-catalog).

- [Graph API tilladelse områder](https://msdn.microsoft.com/Library/Azure/Ad/Graph/howto/azure-ad-graph-api-permission-scopes): OAuth 2.0 tilladelse områder, der bruges til at styre den adgang, som har en app til directory data i en lejer.

### <a name="authentication-and-authorization-protocols"></a>Godkendelse og autorisation protokoller

- [Logge nøgle overgang i Azure AD](active-directory-signing-key-rollover.md): Få mere at vide om Azure AD signering vigtige overgang cadence og hvordan du opdaterer nøgle til de mest almindelige programmer scenarier.

- [OAuth 2.0 protokol: ved hjælp af godkendelse kode Giv](active-directory-protocols-oauth-code.md): Du kan bruge den OAuth 2.0-protokol godkendelse kode Giv til at give adgang til webprogrammer og Web API'er i din Azure Active Directory lejer.

- [OAuth 2.0 protokol: om implicit Giv](active-directory-dev-understanding-oauth2-implicit-grant.md): få flere oplysninger om Giv implicit godkendelse og om det er bedst til dit program.

- [OAuth 2.0 protokol: tjenesten til tjenesten opkald ved hjælp af klienten legitimationsoplysninger](active-directory-protocols-oauth-service-to-service.md): OAuth 2.0-klienten legitimationsoplysninger Giv tillader en webtjeneste (en fortrolige klienten) for at bruge sin egen legitimationsoplysninger til at godkende, når du ringer til en anden webtjeneste, i stedet for at efterligne en bruger. I dette scenarie skal er kunden typisk en midterste niveau webtjeneste, et daemon tjenesten eller websted.

- [OpenID forbinde 1.0-protokollen: logon og godkendelse](active-directory-protocols-openid-connect-code.md): feltet OpenID forbinde 1.0-protokollen udvider OAuth 2.0 til brug som en godkendelsesprotokol. En klientprogrammet kan modtage en id_token for at administrere processen logon, eller udvide godkendelse kode strømmen for at modtage både en id_token og godkendelse kode.

- [SAML 2.0-protokollen reference](active-directory-saml-protocol-reference.md): feltet SAML 2.0-protokollen giver programmer for at give en enkelt sign-on – oplevelse for brugerne.

- [WS-Federation 1.2 protokol](http://docs.oasis-open.org/wsfed/federation/v1.2/os/ws-federation-1.2-spec-os.html): Azure Active Directory understøtter WS-Federation 1.2 ud fra Web Services sammenslutning Version 1.2-specifikationen. Du kan finde flere oplysninger om dokumentets sammenslutning metadata, [Sammenslutning Metadata](active-directory-federation-metadata.md).

- [Understøttede token og Kræv datatyper](active-directory-token-and-claims.md): Du kan bruge denne vejledning til at forstå og evaluerer krav i tokens SAML 2.0 og JSON Web Tokens (JWT).

## <a name="videos"></a>Videoer

### <a name="build"></a>Opbygge

Disse oversigt præsentationer på udvikling af apps ved hjælp af Azure Active Directory funktion højttalere, du arbejder direkte i engineering-teamet. Præsentationer dækker grundlæggende emner, herunder IDMaaS, godkendelse, identitetssammenslutninger og enkeltlogon.

- [Microsoft identitet: Tilstanden for den Union og fremtidige retning](https://azure.microsoft.com/documentation/videos/build-2016-microsoft-identity-state-of-the-union-and-future-direction/)
- [Azure Active Directory: Administration identitet som en tjeneste til moderne programmer](https://azure.microsoft.com/documentation/videos/build-2015-azure-active-directory-identity-management-as-a-service-for-modern-applications/)
- [Udvikle moderne webprogrammer med Azure Active Directory](https://azure.microsoft.com/documentation/videos/build-2015-develop-modern-web-applications-with-azure-active-directory/)
- [Udvikle moderne oprindelige programmer med Azure Active Directory](https://azure.microsoft.com/documentation/videos/build-2015-develop-modern-native-applications-with-azure-active-directory/)

### <a name="azure-friday"></a>Azure fredag
[Azure fredag](https://azure.microsoft.com/documentation/videos/azure-friday/) er et tilbagevendende fredag 1:1 video serie, der er dedikeret til hvilket du korte (10-15 minutter) interviews med eksperter på en række forskellige Azure emner.  Bruge funktionen Services Filter på siden til at se alle Azure Active Directory-videoer.

- [Azure identitet 101](https://azure.microsoft.com/documentation/videos/azure-identity-basics/)
- [Azure identitet 102](https://azure.microsoft.com/documentation/videos/azure-identity-creating-active-directory/)
- [Azure identitet 103](https://azure.microsoft.com/documentation/videos/azure-identity-application-to-authenticate/)

## <a name="social"></a>Sociale

- [Active Directory-teamets blog](http://blogs.technet.com/b/ad/): den seneste udvikling i en verden med Azure Active Directory.

- [Azure Active Directory Graph teamets blog](http://blogs.msdn.com/b/aadgraphteam): Azure Active Directory-oplysninger, der er specifikke for Graph API.

- [Skyen identitet](http://www.cloudidentity.net): tanker på identitet administration som en tjeneste, fra en vigtigste Azure Active Directory PM.  

- [Azure Active Directory på Twitter](https://twitter.com/azuread): Azure Active Directory meddelelser i 140 tegn eller derunder.

## <a name="windows-server-on-premises-development"></a>Windows Server lokalt udvikling
For at vide om brug af Windows Server og Active Directory Federation Services (ADFS) udvikling, skal du se:

- [AD FS scenarier for udviklere](https://technet.microsoft.com/windows-server-docs/identity/ad-fs/overview/ad-fs-scenarios-for-developers): giver et overblik over AD FS komponenter, og hvordan det fungerer, med oplysninger om de understøttede godkendelse/godkendelse scenarier.
- [AD FS gennemgange](https://technet.microsoft.com/windows-server-docs/identity/ad-fs/ad-fs-development): en liste over gennemgå artikler, som viser trinvise instruktioner om implementering af relaterede godkendelse/godkendelse flyder.
