<properties
    pageTitle="Azure AD-B2C | Microsoft Azure"
    description="Typer af programmer, du kan oprette i Azure Active Directory B2C."
    services="active-directory-b2c"
    documentationCenter=""
    authors="dstrockis"
    manager="mbaldwin"
    editor=""/>

<tags
    ms.service="active-directory-b2c"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="hero-article"
    ms.date="07/22/2016"
    ms.author="dastrock"/>

# <a name="azure-active-directory-b2c-types-of-applications"></a>Azure Active Directory-B2C: Typer programmer

Azure Active Directory (Azure AD) B2C understøtter godkendelse til en række forskellige moderne app arkitekturer. Dem alle er baseret på de branche standard protokoller [OAuth 2.0](active-directory-b2c-reference-protocols.md) eller [OpenID forbindelse](active-directory-b2c-reference-protocols.md). Dette dokument en kort beskrivelse af typerne apps, som du kan oprette, uafhængigt af sprog eller platform du foretrækker. Det også hjælper dig med at forstå de overordnede scenarier før du [begynder at bygge programmer](active-directory-b2c-overview.md#getting-started).

## <a name="the-basics"></a>De grundlæggende funktioner
Hver app, der bruger Azure AD B2C skal være registreret i din [B2C directory](active-directory-b2c-get-started.md) via [Azure-portalen](https://portal.azure.com/). Registreringsprocessen app indsamler og tildeler nogle få værdier til din app:

- Et **Program-ID** , der entydigt identificerer din app.
- En **Omdirigere URI** , der kan bruges til at dirigere svar tilbage til din app.
- Alle andre scenarie-specifikke værdier. Få mere at vide Lær, hvordan du kan [registrere en app](active-directory-b2c-app-registration.md).

Når appen er registreret, skal kommunikerer den med Azure AD ved at sende anmodninger til Azure AD v2.0 slutpunktet:

```
https://login.microsoftonline.com/common/oauth2/v2.0/authorize
https://login.microsoftonline.com/common/oauth2/v2.0/token
```

Hver enkelt anmodning, der sendes til Azure AD B2C angiver en **politik**. En politik styrer funktionaliteten af Azure AD. Du kan også bruge disse slutpunkter til at oprette en høj grad brugerdefinerbar sæt brugeroplevelser. Fælles politikker omfatter tilmelding, logon og profil-Rediger politikker. Hvis du ikke kender med politikker, skal du læse om Azure AD B2C [extensible policy framework](active-directory-b2c-reference-policies.md) , før du fortsætter.

Interaktionen mellem hver app med et v2.0 slutpunkt følger et lignende overordnet mønster:

1. Appen omdirigerer v2.0 slutpunktet brugeren til at udføre en [politik](active-directory-b2c-reference-policies.md).
2. Brugeren er fuldført politikken ifølge politikdefinitionen.
4. Appen modtager en slags sikkerhedstoken fra v2.0 slutpunkt.
5. Appen bruger sikkerhedstokenet til at få adgang til beskyttede data eller en beskyttet ressource.
6. Ressource-server har valideret sikkerhedstoken for at bekræfte, at access kan tildeles.
7. Appen opdaterer med jævne mellemrum sikkerheds-id.

<!-- TODO: Need a page for libraries to link to -->
Disse trin kan variere en smule baseret på typen app, du opretter. Åbn kilde biblioteker kan tale om detaljerne for dig.

## <a name="web-apps"></a>Webapps
Web apps (herunder .NET, PHP, Java, fonetisk, Python og Node.js), der er hostet på en server og adgang til dem via en browser, understøtter Azure AD B2C [OpenID oprette forbindelse](active-directory-b2c-reference-protocols.md) til alle brugeroplevelser. Dette omfatter logon, tilmeldingsprocessen og administration af profil. I Azure AD B2C implementering af OpenID Connect starter din online disse brugeroplevelser ved at udstede anmodninger om godkendelse til Azure AD. Resultatet af anmodningen er en `id_token`. Dette sikkerhedstoken repræsenterer brugerens identitet. Den indeholder også oplysninger om brugeren i form af krav:

```
// Partial raw id_token
eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6ImtyaU1QZG1Cd...

// Partial content of a decoded id_token
{
    "name": "John Smith",
    "email": "john.smith@gmail.com",
    "oid": "d9674823-dffc-4e3f-a6eb-62fe4bd48a58"
    ...
}
```

Lær mere om typerne tokens og krav, der er tilgængelige på en app i [B2C token reference](active-directory-b2c-reference-tokens.md).

I en online tager hver udførelse af en [politik for](active-directory-b2c-reference-policies.md) disse overordnede trin:

![Billede af Web App svømmebaner](./media/active-directory-b2c-apps/webapp.png)

Validering af den `id_token` ved hjælp af en offentlig signerende nøgle, der er modtaget fra Azure AD er tilstrækkelige til at kontrollere identitet på brugeren. Dette angiver også en sessionscookie, der kan bruges til at identificere brugeren på anmodninger om efterfølgende side.

For at se dette scenario i handling skal du prøve et af web app-logon kodeeksemplerne i vores [Introduktion sektion](active-directory-b2c-overview.md#getting-started).

En server WebApp muligvis ud over at lette enkelt logon, også adgang til en back end-webtjeneste. I dette tilfælde kan online udføre en smule [OpenID forbinde flow](active-directory-b2c-reference-oidc.md) og erhverv tokens ved hjælp af godkendelseskoder og opdatere tokens. Dette scenario er afbildet i den følgende [Web API'er sektion](#web-apis).

<!--, and in our [WebApp-WebAPI Getting started topic](active-directory-b2c-devquickstarts-web-api-dotnet.md).-->

## <a name="web-apis"></a>Web API'er
Du kan bruge Azure AD B2C til at sikre webtjenester som din app RESTful web API. Web API'er kan bruge OAuth 2.0 til at sikre deres data ved at godkende indgående HTTP-anmodninger ved hjælp af tokens. Kalderen af en web API tilføjer et token i overskriften godkendelse af en HTTP-anmodning:

```
GET /api/items HTTP/1.1
Host: www.mywebapi.com
Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6...
Accept: application/json
...
```

World Wide web API kan derefter bruge tokenet til at kontrollere kalderen API identitet og til at udtrække oplysninger om kalderen fra krav, der er kodet i tokenet. Lær mere om typerne tokens og krav, der er tilgængelige på en app i [Azure AD B2C token reference](active-directory-b2c-reference-tokens.md).

> [AZURE.NOTE]
    Azure AD-B2C understøtter i øjeblikket kun web API'er, som deres egne kendte klienter adgang til. For eksempel kan din fuldført app omfatte en iOS-app, en Android-appen og en back end-web API. Denne arkitektur understøttes fuldt ud. Tillade en partner klient, som en anden iOS-app til at få adgang til den samme web API ikke understøttes i øjeblikket. Alle komponenterne i din fuldført app skal dele et enkelt program-ID.

En web API kan modtage tokens fra mange forskellige typer af klienter, herunder Onlines, skrivebord og -mobilapps, enkelt side apps, serversiden daemons og andre web API'er. Her er et eksempel med fuldført forløb til en WebApp, der kalder en web API:

![Billede af Web App Web API svømmebaner](./media/active-directory-b2c-apps/webapi.png)

Læs mere om [OAuth 2.0-protokollen](active-directory-b2c-reference-oauth-code.md)for at få mere for at vide om godkendelseskoder, Opdater tokens og trinnene til at hente tokens.

For at lære at sikre en web API med Azure AD B2C, se World Wide web API selvstudier i vores [Introduktion sektion](active-directory-b2c-overview.md#getting-started).

## <a name="mobile-and-native-apps"></a>Mobile og oprindelige apps
Apps, der er installeret på enheder, som bærbare og stationære apps, er ofte nødt til at åbne back end-tjenester eller web API'er på vegne af brugere. Du kan føje tilpassede identitet management oplevelser til dine oprindelige apps og sikkert opkald back end-tjenester ved hjælp af Azure AD B2C og [OAuth 2.0 godkendelse kode forløb](active-directory-b2c-reference-oauth-code.md).  

I dette flow appen udfører [politikker](active-directory-b2c-reference-policies.md) og modtager en `authorization_code` fra Azure AD, når brugeren er fuldført politikken. Den `authorization_code` repræsenterer den app tilladelse til at ringe til back end-tjenester på vegne af den bruger, der er logget på. Appen kan derefter exchange på `authorization_code` i baggrunden for en `id_token` og en `refresh_token`.  Appen kan bruge den `id_token` til at godkende en back end-web API i HTTP-anmodninger. Det kan også bruge den `refresh_token` at få en ny `id_token` hvor en ældre udløber.

> [AZURE.NOTE]
    Azure AD-B2C understøtter i øjeblikket kun tokens, som bruges til at få adgang til en app's egen back end-webtjeneste. For eksempel kan din fuldført app omfatte en iOS-app, en Android-appen og en back end-web API. Denne arkitektur understøttes fuldt ud. Tillade din iOS-app til at få adgang til en partner web API ved hjælp af OAuth 2.0 access tokens understøttes ikke i øjeblikket. Alle komponenterne i din fuldført app skal dele et enkelt program-ID.

![Billede af Oprindelig App svømmebaner](./media/active-directory-b2c-apps/native.png)

## <a name="current-limitations"></a>Aktuelle begrænsninger
Azure AD-B2C understøtter i øjeblikket ikke følgende typer apps, men de er på den roadmapy. Yderligere begrænsninger og begrænsninger, der er relateret til Azure AD B2C er beskrevet i [begrænsninger og begrænsninger](active-directory-b2c-limitations.md).

### <a name="single-page-apps-javascript"></a>Enkelt side apps (JavaScript)
Mange moderne apps har en enkelt side app front-end skrevet primært på JavaScript. De bruger ofte en ramme som AngularJS, Ember.js eller Durandal. Alment tilgængelig Azure AD-tjenesten understøtter disse apps ved hjælp af OAuth 2.0 implicit strømmen. Dette flow er dog ikke endnu er tilgængelig i Azure AD B2C.

### <a name="daemonsserver-side-apps"></a>Daemons-serversiden apps
Apps, der indeholder længerevarende processer eller arbejder uden tilstedeværelsen af en bruger skal også en måde at få adgang til sikret ressourcer som web API'er. Disse apps kan godkende og hente tokens, ved hjælp af den app identitet (i stedet for en brugers delegeret identitet) og ved hjælp af OAuth 2.0-klienten legitimationsoplysninger flow.

Dette flow understøttes ikke i øjeblikket af Azure AD B2C. Disse apps kan få tokens, når der opstod en interaktiv bruger forløb.

### <a name="web-api-chains-on-behalf-of-flow"></a>Web API kæder (på vegne af flow)
Mange arkitekturer omfatter en web API, der skal ringe til en anden efterfølgende web API, hvor begge er sikret med Azure AD B2C. Dette scenario er fælles for oprindelige klienter, der har en Web API back end. Dette kalder derefter en Microsoft-onlinetjeneste som Azure AD Graph API.

Dette scenarie sammenkædede web API kan understøttes ved hjælp af OAuth 2.0 JWT bæreren legitimationsoplysninger Giv, også kaldet på vegne af strømmen.  Dog er på vegne af strømmen ikke implementeret for øjeblikket i Azure AD B2C.
