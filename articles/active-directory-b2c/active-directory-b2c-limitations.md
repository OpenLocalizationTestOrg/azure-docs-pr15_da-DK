<properties
    pageTitle="Azure Active Directory-B2C: Begrænsninger og begrænsninger | Microsoft Azure"
    description="En liste over begrænsninger og begrænsninger i forbindelse med Azure Active Directory B2C"
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
    ms.topic="article"
    ms.date="07/24/2016"
    ms.author="swkrish"/>

# <a name="azure-active-directory-b2c-limitations-and-restrictions"></a>Azure Active Directory-B2C: Begrænsninger og begrænsninger

Der findes flere funktioner og Azure Active Directory (Azure AD) B2C-funktioner, der ikke understøttes endnu. Mange af disse kendte problemer og begrænsninger adresseret fremover, men du skal være opmærksom på dem, hvis du opretter adgang til forbrugere programmer, der bruger Azure AD B2C.

## <a name="issues-during-the-creation-of-azure-ad-b2c-tenants"></a>Problemer under oprettelse af Azure AD B2C lejere

Hvis du støder på problemer under [oprettelse af en Azure AD B2C lejer](active-directory-b2c-get-started.md), se [oprette en Azure AD-lejer eller en Azure AD B2C lejer – problemer og løsninger](active-directory-b2c-support-create-directory.md) til vejledning.

Bemærk, at der er kendte problemer, når du sletter en eksisterende B2C lejer og oprette den igen med det samme domænenavn. Du skal oprette en B2C lejer med et andet domænenavn.

## <a name="note-about-b2c-tenant-quotas"></a>Bemærkning om B2C lejer kvoter

Antallet af brugere i en B2C lejer er begrænset til 50.000 brugere som standard. Hvis du har brug at hæve kvoten for din B2C lejer, skal du kontakte Support.

## <a name="branding-issues-on-verification-email"></a>Problemer med på bekræftelsesmailen branding

Bekræftelsesmailen standard indeholder Microsoft branding. Vi vil fjerne den fremover. Nu skal fjerne du den ved hjælp af [virksomhed mærkning funktion](../active-directory/active-directory-add-company-branding.md).

## <a name="restrictions-on-applications"></a>Begrænsninger for programmer

Følgende typer programmer understøttes ikke i øjeblikket i Azure AD B2C. Ved en beskrivelse af de understøttede typer programmer skal du se [Azure AD B2C: typer programmer](active-directory-b2c-apps.md).

### <a name="single-page-applications-javascript"></a>Enkelt side programmer (JavaScript)

Mange moderne programmer har en enkelt side program (SPA) front end, der er skrevet primært i JavaScript og ofte bruger en SPA framework såsom AngularJS, Ember.js, Durandal osv. Dette flow er ikke tilgængelig i Azure AD B2C.

### <a name="daemons--server-side-applications"></a>Daemons / serversiden programmer

Programmer, der indeholder længerevarende processer eller arbejder uden tilstedeværelsen af en bruger skal også en måde at få adgang til sikret ressourcer, som Web API'er. Disse programmer kan godkende og hente tokens ved hjælp af programmets identitet (i stedet for en forbruger delegeret identitet) i [OAuth 2.0-klienten legitimationsoplysninger forløb](active-directory-b2c-reference-protocols.md#oauth2-client-credentials-grant-flow). Dette flow findes ikke endnu i Azure AD B2C, så nu skal programmer kan hente tokens, kun, når der opstod en interaktiv consumer logon forløb.

### <a name="standalone-web-apis"></a>Enkeltstående Web API'er

I Azure AD B2C har du mulighed for at [opbygge en Web API, der er sikret ved hjælp af OAuth 2.0 tokens](active-directory-b2c-apps.md#web-apis). Dog vil pågældende Web API kun kunne modtage tokens fra en klient, der deler de samme program-ID. Opbygning af en Web API, der åbnes fra flere forskellige klienter understøttes ikke.

### <a name="web-api-chains-on-behalf-of"></a>Web API kæder (på vegne af)

Mange arkitekturer omfatter en Web API, der skal ringe til en anden efterfølgende Web API, begge sikret med Azure AD B2C. Dette scenario er fælles for oprindelige klienter, der har Web API back-end, der kalder en Microsoft-onlinetjeneste som Azure AD Graph API.

Dette sammenkædede Web API-scenarie kan understøttes ved hjælp af OAuth 2.0 Jwt bæreren legitimationsoplysninger ydelse, også kendt som på vegne af-strømmen. Dog er på vegne af-strømmen ikke implementeret for øjeblikket i Azure AD B2C.

## <a name="restriction-on-libraries-and-sdks"></a>Begrænsning på biblioteker og SDK'er

Sæt af understøttede Microsoft biblioteker, der fungerer Azure AD B2C er meget begrænset på nuværende tidspunkt. Vi har understøttelse af .NET baseret webapps og tjenester samt NodeJS webapps og tjenester.  Vi har også et eksempel .NET klientbibliotek kaldet MSAL, der kan bruges med Azure AD B2C i Windows og andre .NET-apps.

Vi har i øjeblikket ikke bibliotek, der understøtter andre sprog eller platforme, herunder iOS og Android.  Hvis du vil oprette på en anden platform end dem, der er nævnt ovenfor, anbefaler vi ved hjælp af en open source-SDK, der refererer til vores [OAuth 2.0 og OpenID forbinde Protocol Reference](active-directory-b2c-reference-protocols.md) som det er nødvendigt.  Azure AD-B2C implementerer OAuth & OpenID Connect, som gør det muligt at bruge en generisk OAuth eller OpenID forbinde dokumentbibliotek til integration.

Brug open source-biblioteker, der har testet for kompatibilitet med Azure AD B2C af vores iOS og Android Hurtig start selvstudier.  Alle vores Hurtig start-selvstudier er tilgængelige i vores [Introduktion](active-directory-b2c-overview.md#getting-started) afsnit.

## <a name="restriction-on-protocols"></a>Begrænsning på protokoller

Azure AD-B2C understøtter OpenID forbinde og OAuth 2.0. Men ikke alle funktioner og egenskaber for hver protokol er gennemført. For bedre at forstå omfanget af understøttet protokol funktionalitet i Azure AD B2C, læse vores [OpenID forbinde og OAuth 2.0 protocol reference](active-directory-b2c-reference-protocols.md). SAML og WS Fed protocol-understøttelse er ikke tilgængelig.

## <a name="restriction-on-tokens"></a>Begrænsning på tokens

Mange af de tokens, der er udstedt af Azure AD B2C implementeres som JSON Web Tokens eller JWTs. Ikke alle oplysningerne i JWTs (kaldet "krav") er dog helt, som den skal være eller mangler. Eksempler kan nævnes "sub" og "preferred_username" krav.  Tokens for eksisterende politikker forbliver påvirket som værdier, formaterer eller betydningen af krav ændringer over tid, – du kan stole på deres værdier i fremstilling apps.  Når værdier ændres, giver vi dig mulighed for at konfigurere disse ændringer for hver af dine politikker.  For bedre at forstå tokens fra tjenesten Azure AD B2C i øjeblikket, ved at læse vores [token reference](active-directory-b2c-reference-tokens.md).

## <a name="restriction-on-nested-groups"></a>Begrænsning på indlejrede grupper

Indlejrede gruppemedlemskaber understøttes ikke i Azure AD B2C lejere. Vi planer ikke om at tilføje denne egenskab.

## <a name="restriction-on-differential-query-feature-on-azure-ad-graph-api"></a>Begrænsning på forskelle forespørgsel funktion på Azure AD Graph API

[Funktionen forskelle forespørgsel på Azure AD Graph API](https://msdn.microsoft.com/library/azure/ad/graph/howto/azure-ad-graph-api-differential-query) understøttes ikke i Azure AD B2C lejere. Dette er i gang med vores langsigtede køreplanen.

## <a name="issues-with-user-management-on-the-azure-classic-portal"></a>Problemer med brugeradministration på portalen Azure klassisk

B2C funktioner er tilgængelige på Azure-portalen. Du kan dog bruge portalen Azure klassisk at få adgang til andre lejer funktioner, herunder brugeradministration. Der er i øjeblikket er et par kendte problemer med brugeradministration (fanen **brugere** ) på Azure klassisk portalen:

- For en lokal konto bruger (det vil sige en kunde, der tilmelder sig med en e-mailadresse og adgangskode, eller et brugernavn og adgangskode), svarer feltet **Brugernavn** ikke til det logon id (mailadresse eller brugernavn), der blev brugt under tilmeldingen. Dette skyldes, at feltet vises på portalen Azure klassisk er faktisk bruger vigtigste navn (UPN), som ikke bruges i B2C scenarier. Find brugerobjektet i [Graph Explorer](https://graphexplorer.cloudapp.net/)for at få vist det logon-id for den lokale konto. Du kan finde det samme problem med en sociale brugerkontoen (det vil sige en kunde, der tilmelder sig med Facebook, Google +, osv.), men det er tilfældet, der er ingen logon-id til at tale af.

    ![Lokale konti - UPN](./media/active-directory-b2c-limitations/limitations-user-mgmt.png)

- For en lokal konto bruger vil du ikke kunne redigere nogen af felterne og gemme ændringer under fanen **profil** .

## <a name="issues-with-admin-initiated-password-reset-on-the-azure-classic-portal"></a>Problemer med administrator-definerede på Azure klassisk portalen til nulstilling af adgangskode

Hvis du nulstiller adgangskoden til en lokal konto-baserede forbrugere på Azure klassisk portalen (kommandoen **Nulstil adgangskode** under fanen **brugere** ), kan forbrugeren kan ikke ændre sin adgangskode på den næste Log på, hvis du bruger en logge eller logge på politikken, og vil være låst af dine programmer. Som en løsning kan du bruge [Azure AD Graph API](active-directory-b2c-devquickstarts-graph-dotnet.md) Nulstil brugerens adgangskode (uden udløb af adgangskoder) eller bruge en logge på politik i stedet for et tegn eller logge på politik.

## <a name="issues-with-creating-a-custom-attribute"></a>Problemer med at oprette en brugerdefineret attribut

En [brugerdefineret attribut tilføjet på portalen Azure](active-directory-b2c-reference-custom-attr.md) oprettes der ikke umiddelbart i din B2C lejer. Har du kan bruge den brugerdefinerede attribut i mindst én af dine politikker for det, at få oprettet i din B2C lejer og bliver tilgængelig via Graph API.

## <a name="issues-with-verifying-a-domain-on-the-azure-classic-portal"></a>Problemer med bekræftelse af et domæne på portalen Azure klassisk

Du kan ikke i øjeblikket bekræfte et domæne korrekt på [Azure klassisk portal](https://manage.windowsazure.com/).

## <a name="issues-with-sign-in-with-mfa-policy-on-safari-browsers"></a>Problemer med logon med MFA politik i Safari browsere

Anmodninger om at logge på politikker (med MFA slået til) mislykkes periodisk på Safari browsere med HTTP 400 (forkert anmodning) fejl. Dette er forfaldsdato Safaris lav cookie begrænsninger. Der er et par løsninger på dette problem:

- Bruge "tilmelding eller Log på politikken" i stedet for "politikken logon".
- Reducer antallet af **programmet krav** der anmodes om i din politik.
