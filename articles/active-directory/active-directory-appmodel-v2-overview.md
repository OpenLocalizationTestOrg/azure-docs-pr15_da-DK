<properties
    pageTitle="Oversigt over v2.0 slutpunkt | Microsoft Azure"
    description="En introduktion til at lave apps med både Microsoft-Account og Azure Active Directory-logon."
    services="active-directory"
    documentationCenter=""
    authors="dstrockis"
    manager="mbaldwin"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/27/2016"
    ms.author="dastrock"/>

# <a name="sign-in-microsoft-account--azure-ad-users-in-a-single-app"></a>Log på Microsoft-Account & Azure AD-brugere i en enkelt app

Tidligere var en app-udvikleren der ønskede at understøtte både Microsoft-konti og Azure Active Directory nødvendig for at integrere med to separate systemer.  Vi har nu introduceret en ny API version til godkendelse, der gør det muligt at logge på brugerne på med begge typer konti med Azure AD-system.  Dette konvergeret godkendelsessystem kaldes også **v2.0 slutpunkt**.  Med v2.0 slutpunktet kan en enkel integration du når en målgruppe, der strækker sig over millioner af brugere med både personlige og arbejde/skole-konti.

Apps, der bruger v2.0 slutpunktet kan også bruge REST API'er fra [Microsoft Graph](https://graph.microsoft.io) og [Office 365](https://msdn.microsoft.com/office/office365/howto/authenticate-Office-365-APIs-using-v2) ved hjælp af hver type konto.

<!-- For a quick introduction to the v2.0 endpoint, please view the [Getting Started with Microsoft Identities: Enterprise Grade Sign In For Your Apps](https://azure.microsoft.com/documentation/videos/build-2016-getting-started-with-microsoft-identities-enterprise-grade-sign-in-for-your-apps/) video. -->

## <a name="getting-started"></a>Kom godt i gang
[AZURE.VIDEO build-2016-getting-started-with-microsoft-identities-enterprise-grade-sign-in-for-your-apps]

Vælg din foretrukne platform fra nedenstående liste for at oprette en app ved hjælp af vores Åbn kilde biblioteker og rammer.  Du kan også bruge vores OAuth 2.0 og OpenID forbinde protocol dokumentation til send og modtag meddelelser protocol direkte uden brug af et auth bibliotek.

<!-- TODO: Finalize this table  -->
[AZURE.INCLUDE [active-directory-v2-quickstart-table](../../includes/active-directory-v2-quickstart-table.md)]

## <a name="whats-new"></a>Hvad er nyt
De grundlæggende oplysninger kan være nyttige med at forstå, hvad er, og hvad er ikke muligt med v2.0 slutpunkt.

- Få mere at vide om de [typer af apps, du kan oprette med v2.0 slutpunkt](active-directory-v2-flows.md).
- Forstå [begrænsninger, begrænsninger, og begrænsninger](active-directory-v2-limitations.md) til v2.0 slutpunktet.
- Vi har for nylig tilføjet understøttelse af [admin begrænsede områder](active-directory-v2-scopes.md) og [OAuth2 klient legitimationsoplysninger give](active-directory-v2-protocols-oauth-client-creds.md).  Prøve dem!

## <a name="reference"></a>Reference
Disse links vil være nyttige til udforske platform i dybden:

- Opbyg 2016: [Introduktion til Microsoft identiteter: Enterprise Grade Log på For dine Apps](https://azure.microsoft.com/documentation/videos/build-2016-getting-started-with-microsoft-identities-enterprise-grade-sign-in-for-your-apps/)
- Få hjælp til stakoverløb ved hjælp af [azure-aktiv-directory](http://stackoverflow.com/questions/tagged/azure-active-directory) eller [adal](http://stackoverflow.com/questions/tagged/adal) mærker.
- [v2.0 Protocol Reference](active-directory-v2-protocols.md)
- [v2.0 Token Reference](active-directory-v2-tokens.md)
- [Reference til v2.0-bibliotek](active-directory-v2-libraries.md)
- [Områder og samtykke i slutpunktet v2.0](active-directory-v2-scopes.md)
- [Portalen Microsoft App registrering](https://apps.dev.microsoft.com/?referrer=https://azure.microsoft.com/documentation/articles&deeplink=/appList)
- [Reference til Office 365 REST-API](https://msdn.microsoft.com/office/office365/howto/authenticate-Office-365-APIs-using-v2)
- [Microsoft Graph](https://graph.microsoft.io)