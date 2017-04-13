<properties
    pageTitle="Azure Active Directory B2C: Oversigt over | Microsoft Azure"
    description="Udviklingsprogrammer consumer ned med Azure Active Directory B2C"
    services="active-directory-b2c"
    documentationCenter=""
    authors="swkrish"
    manager="mbaldwin"
    editor="bryanla"/>

<tags
    ms.service="active-directory-b2c"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="hero-article"
    ms.date="07/24/2016"
    ms.author="swkrish"/>

# <a name="azure-active-directory-b2c-sign-up-and-sign-in-consumers-in-your-applications"></a>Azure Active Directory-B2C: Tilmeld dig og logge på forbrugere i dine programmer

Azure Active Directory-B2C er en omfattende skyen identitet management-løsning til forbrugere mod web og mobilprogrammer. Det er en let tilgængelig globale tjeneste, som kan skaleres til hundredvis af millioner af forbrugere identiteter. Azure Active Directory B2C holder bygget på en professionel og sikker platform, dine programmer, din virksomhed og dine brugere, der er beskyttet.

Tidligere ville udviklere der ønskede at tilmelde dig og logge på forbrugere til deres programmer være skrevet deres egen kode. Og de vil har brugt lokale databaser eller systemer til at gemme brugernavne og adgangskoder. Azure Active Directory-B2C giver en bedre måde at integrere consumer identitet management i deres programmer ved hjælp af en sikker, baseret på standarder platform og et bredt udvalg af extensible politikker for udviklere. Når du bruger Azure Active Directory B2C, kan dine brugere tilmelde dig dine programmer ved hjælp af deres eksisterende konti til sociale (Facebook, Google, Amazon, LinkedIn) eller ved at oprette nye legitimationsoplysninger (mailadresse og adgangskode, eller brugernavn og adgangskode) Vi kalder de sidste "lokale konti."

## <a name="get-started"></a>Komme i gang

Du opretter et program, der accepterer consumer tilmelde, og log på, du vil første behov for at registrere programmet med en Azure Active Directory B2C lejer. Få din egen lejer ved hjælp af trinnene beskrevet i [oprette en Azure AD B2C lejer](active-directory-b2c-get-started.md).

Du kan skrive dit program mod Azure Active Directory B2C-tjenesten, ved at vælge at sende protocol meddelelser direkte ved hjælp af [OAuth 2.0](active-directory-b2c-reference-protocols.md#oauth2-authorization-code-flow) , eller [Åbn ID forbindelse](active-directory-b2c-reference-protocols.md#openid-connect-sign-in-flow), eller ved hjælp af vores biblioteker til at gøre arbejdet for dig. Vælg din foretrukne platform i den følgende tabel og komme i gang.

[AZURE.INCLUDE [active-directory-b2c-quickstart-table](../../includes/active-directory-b2c-quickstart-table.md)]

## <a name="whats-new"></a>Hvad er nyt

Markér tilbage her ofte for at få mere for at vide om kommende ændringer til Azure Active Directory B2C. Vi vil også tweet om opdateringer ved hjælp af @AzureAD.

- Få mere at vide om vores [politik for extensible framework](active-directory-b2c-reference-policies.md) og om de forskellige typer af politikker, som du kan oprette og bruge i dine programmer.
- Opret et bogmærke vores [blog for skytjeneste](https://blogs.msdn.microsoft.com/azureadb2c/) til meddelelser om underordnede serviceproblemer, opdateringer, status og afhjælpninger. Fortsætte med at overvåge [Azure status dashboard](https://azure.microsoft.com/status/) samt.
- Aktuelle [tjenestebegrænsninger, begrænsninger, og begrænsninger](active-directory-b2c-limitations.md).
- Til sidst en [eksempel-kode](https://github.com/Azure-Samples/active-directory-dotnet-webapp-openidconnect-aspnetcore-b2c) Hjælp Azure AD B2C og ASP.NET Core.

## <a name="how-to-articles"></a>Artikler

Lær at bruge bestemte Azure Active Directory B2C funktioner:

- Konfigurere [Facebook](active-directory-b2c-setup-fb-app.md), [Google +](active-directory-b2c-setup-goog-app.md), [Microsoft-konto](active-directory-b2c-setup-msa-app.md), [Amazon](active-directory-b2c-setup-amzn-app.md)og [LinkedIn](active-directory-b2c-setup-li-app.md) konti til brug i programmerne consumer ned.
- [Brug brugerdefinerede attributter til at indsamle oplysninger om dine brugere](active-directory-b2c-reference-custom-attr.md).
- [Aktivere Azure Multi-Factor Authentication i programmerne consumer ned](active-directory-b2c-reference-mfa.md).
- [Konfigurere selvbetjening for nulstilling af adgangskode til dine brugere](active-directory-b2c-reference-sspr.md).
- [Tilpasse udseendet af Tilmeld dig Log på, og andre sider med adgang til forbrugere](active-directory-b2c-reference-ui-customization.md) , der leveres af Azure Active Directory B2C.
- [Brug Azure Active Directory Graph-API til fra et program oprette, læse, opdatere, og slette forbrugere](active-directory-b2c-devquickstarts-graph-dotnet.md) i din Azure Active Directory B2C lejer.

## <a name="next-steps"></a>Næste trin

Disse links vil være nyttige til udforske tjenesten i dybden:

- Se [Azure Active Directory B2C prisoplysninger](https://azure.microsoft.com/pricing/details/active-directory-b2c/).
- Få hjælp til stakoverløb ved hjælp af [azure-aktiv-directory](http://stackoverflow.com/questions/tagged/azure-active-directory) eller [adal](http://stackoverflow.com/questions/tagged/adal) mærker.
- Give os dine tanker ved at bruge [Bruger stemme](https://feedback.azure.com/forums/169401-azure-active-directory/)– vi vil gerne høre dem! Bruge sætningen "AzureADB2C:" i titlen på dit indlæg, så vi kan finde den.
- Gennemse [Azure AD-B2C Protocol Reference](active-directory-b2c-reference-protocols.md).
- Gennemse [Azure AD-B2C Token Reference](active-directory-b2c-reference-tokens.md).
- Læs [Azure Active Directory B2C ofte stillede spørgsmål](active-directory-b2c-faqs.md).
- [Fil, der understøtter anmodninger om Azure Active Directory B2C](active-directory-b2c-support.md).

## <a name="get-security-updates-for-our-products"></a>Hente sikkerhedsopdateringer til vores produkter

Vi anbefaler, at finde meddelelser om når sikkerhedshændelser forekommer, ved at besøge [denne side](https://technet.microsoft.com/security/dd252948) og abonnere på vejledende sikkerhedsadvarsler.
