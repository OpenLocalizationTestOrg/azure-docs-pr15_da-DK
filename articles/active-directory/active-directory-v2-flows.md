<properties
    pageTitle="Typer af v2.0 slutpunktet | Microsoft Azure"
    description="Typer af apps og scenarier, der understøttes af Azure AD v2.0 slutpunkt."
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
    ms.date="09/30/2016"
    ms.author="dastrock"/>

# <a name="types-of-apps-for-the-v20-endpoint"></a>Typer af apps til v2.0 slutpunktet
V2.0 slutpunktet understøtter godkendelse til en række forskellige moderne app arkitekturer, som alle er baseret på de branche standard protokoller [OAuth 2.0](active-directory-v2-protocols.md#oauth2-authorization-code-flow) og/eller [OpenID forbindelse](active-directory-v2-protocols.md#openid-connect-sign-in-flow).  Dette dokument en kort beskrivelse af typerne apps, du kan oprette, uafhængigt af sprog eller platform du foretrækker.  Det hjælper dig med at forstå de højt niveau scenarier før du [hoppe direkte i koden](active-directory-appmodel-v2-overview.md#getting-started).

> [AZURE.NOTE]
    Ikke alle Azure Active Directory scenarier og funktioner, der understøttes af v2.0 slutpunkt.  For at bestemme, hvis du skal bruge v2.0 slutpunkt skal du læse om [v2.0 begrænsninger](active-directory-v2-limitations.md).

## <a name="the-basics"></a>De grundlæggende funktioner
Hver app, der bruger v2.0 slutpunktet skal være registreret hos [apps.dev.microsoft.com](https://apps.dev.microsoft.com/?referrer=https://azure.microsoft.com/documentation/articles&deeplink=/appList).  Registreringsprocessen app vil indsamling og tildele nogle få værdier til din app:

- Et **Program-Id** , der entydigt identificerer din app
- **Omdirigere URI** , der kan bruges til at dirigere svar tilbage til din app
- Et par andre scenarie-specifikke værdier.  For at få flere detaljer, se, hvordan du kan [registrere en app](active-directory-v2-app-registration.md).

Når du er tilmeldt, kommunikerer appen med Azure AD ved at sende anmodninger til Azure Active Directory v2.0 slutpunkt.  Vi giver dig Åbn kilde strukturer og biblioteker, der tager sig af oplysninger om disse anmodninger, eller du kan også selv implementere godkendelse logik ved at oprette forespørgsler til disse slutpunkter:

```
https://login.microsoftonline.com/common/oauth2/v2.0/authorize
https://login.microsoftonline.com/common/oauth2/v2.0/token
```
<!-- TODO: Need a page for libraries to link to -->

## <a name="web-apps"></a>Webapps
Du kan udføre bruger web Apps (.NET PHP Java, fonetisk, Python, Node, og osv), er åbnet via en browser, skal logge på med [OpenID Opret forbindelse](active-directory-v2-protocols.md#openid-connect-sign-in-flow).  I OpenID forbinde online modtager en `id_token`, et sikkerhedstoken, der bekræfter brugerens identitet og indeholder oplysninger om brugeren i form af krav:

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

Du kan få mere at vide om alle typerne tokens og krav, der er tilgængelige på en app i [v2.0 token reference](active-directory-v2-tokens.md).

I web server apps kræver logon godkendelse strømmen disse højt niveau trin:

![Billede af Web App svømmebaner](../media/active-directory-v2-flows/convergence_scenarios_webapp.png)

Validering af id_token ved hjælp af en offentlig signerende nøgle, der er modtaget fra v2.0 slutpunktet er tilstrækkeligt at sikre, at det bruger-id, og Angiv en sessionscookie, der kan bruges til at identificere brugeren på anmodninger om efterfølgende side.

For at se dette scenario i handling skal du prøve et af web app-logon kodeeksemplerne i vores [Introduktion](active-directory-appmodel-v2-overview.md#getting-started) afsnit.

En server WebApp muligvis ud over enkelt logon, også adgang til nogle andre webtjeneste som en REST-API.  I dette tilfælde kan server online deltage i et kombinerede OpenID Opret forbindelse & OAuth 2.0 flow, ved hjælp af [OAuth 2.0 godkendelseskode forløb](active-directory-v2-protocols.md#oauth2-authorization-code-flow). Dette scenarie dækkes under i vores [WebApp WebAPI Introduktion emne](active-directory-v2-devquickstarts-webapp-webapi-dotnet.md).

## <a name="web-apis"></a>Web API'er
Du kan bruge v2.0 slutpunktet til at sikre webtjenester samt, som din app RESTful Web API.  I stedet for id_tokens og session cookies skal bruge Web API'er OAuth 2.0 access_tokens til at sikre deres data og godkende indgående anmodninger.  Kalderen af en Web API tilføjer en access_token i overskriften godkendelse af en HTTP-anmodning:

```
GET /api/items HTTP/1.1
Host: www.mywebapi.com
Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6...
Accept: application/json
...
```

Web API kan derefter bruge access_token til at kontrollere kalderen API identitet og hente oplysninger om kalderen fra krav, der er kodet i access_token.  Du kan få mere at vide om alle typerne tokens og krav, der er tilgængelige på en app i [v2.0 token reference](active-directory-v2-tokens.md).

En Web API kan give brugerne mulighed for at vælge-i/fravælge bestemte funktioner eller data ved at udsætte tilladelser, også kendt som [områder](active-directory-v2-scopes.md).  Til en opkald app til at hente tilladelse til et område, skal brugeren samtykke i området under et rutediagram.  V2.0 slutpunktet sig tager af bede brugeren om tilladelse og registrere disse tilladelser i alle access_tokens, der Web API modtager.  Alle Web API skal bekymre dig om er validere access_tokens den modtager på hver opkald og udføre de korrekte autorisation Kontroller.

En Web API kan modtage access_tokens fra alle typer apps, herunder server Onlines, skrivebord og mobilapps, enkelt side apps, server side daemons og endda andre Web-API'er.  Højt niveau strømmen til web api godkendelse er som følger:

![Billede af Web API svømmebaner](../media/active-directory-v2-flows/convergence_scenarios_webapi.png)

Hvis du vil vide mere om authorization_codes, refresh_tokens og den detaljerede vejledning for at få access_tokens skal du læse om [OAuth 2.0-protokollen](active-directory-v2-protocols-oauth-code.md).

Lære at sikre en web api med OAuth2 access_tokens, se web api-kodeeksempler i vores [Introduktion sektion](active-directory-appmodel-v2-overview.md#getting-started).


## <a name="mobile-and-native-apps"></a>Mobile og oprindelige apps
Apps, der er installeret på en enhed, som bærbare og stationære apps, er ofte nødt til at åbne back end-tjenester eller Web API'er, gemme data og udføre forskellige funktioner på vegne af en bruger.  Disse apps kan føje logon og godkendelse til back end-tjenester ved hjælp af [OAuth 2.0 godkendelseskode forløb](active-directory-v2-protocols-oauth-code.md).  

I dette flow en appen modtager en authorization_code fra v2.0 slutpunktet på bruger-logon, som repræsenterer den app tilladelse til at ringe til back end-tjenester på vegne af den bruger, der aktuelt er logget på.  Appen kan derefter udveksle authoriztion_code i baggrunden for en OAuth 2.0 access_token og en refresh_token.  Appen kan bruge access_token til at godkende Web API'er i HTTP-anmodninger og kan bruge refresh_token for at få nye access_tokens, når ældre udløber.

![Billede af Oprindelig App svømmebaner](../media/active-directory-v2-flows/convergence_scenarios_native.png)

## <a name="single-page-apps-javascript"></a>Enkelt side apps (javascript)
Mange moderne apps har en enkelt side App (SPA) front end skrevet primært i javascript og ofte bruger strukturer som AngularJS, Ember.js, Durandal osv.  Azure AD v2.0 slutpunktet understøtter disse apps, ved hjælp af den [OAuth 2.0 Implicit dataflow](active-directory-v2-protocols-implicit.md).

I dette flow appen modtager tokens fra v2.0 Godkend slutpunkt direkte, uden at udføre en hvilken som helst back end-server til server udveksling.  Dette giver mulighed for alle godkendelse logik og håndtering af session tage placere helt i javascript-klienten, uden at udføre ekstra side omdirigeringer.

![Implicit Flow svømmebaner billede](../media/active-directory-v2-flows/convergence_scenarios_implicit.png)

For at se dette scenario i handling skal du prøve et enkelt side app kodeeksempler i vores [Introduktion](active-directory-appmodel-v2-overview.md#getting-started) afsnit.

### <a name="daemonsserver-side-apps"></a>Daemons/server side apps
Apps, der indeholder længerevarende processer eller arbejder uden tilstedeværelsen af en bruger skal også en måde at få adgang til sikret ressourcer, som Web API'er.  Disse apps kan godkende og få tokens, der bruger den app identitet (i stedet for en brugers delegeret identitet) ved hjælp af OAuth 2.0-klienten legitimationsoplysninger forløb.

I dette flow appen henter tokens ved direkte interaktion med den `/token` slutpunkt:

![Billede af daemon App svømmebaner](../media/active-directory-v2-flows/convergence_scenarios_daemon.png)

For at opbygge en daemon app skal du se klient legitimationsoplysninger documeenation i vores [Introduktion](active-directory-appmodel-v2-overview.md#getting-started) afsnit eller referere til [denne .NET eksempel app](https://github.com/Azure-Samples/active-directory-dotnet-daemon-v2).

## <a name="current-limitations"></a>Aktuelle begrænsninger
Disse typer apps understøttes ikke i øjeblikket af v2.0 slutpunkt, men er i gang med køreplanen.  Yderligere begrænsninger og begrænsninger for v2.0 slutpunktet er beskrevet i [v2.0 begrænsninger artikel](active-directory-v2-limitations.md).

### <a name="chained-web-apis-on-behalf-of"></a>Sammenkædede web API'er (på vegne af)
Mange arkitekturer omfatter en Web API, der skal ringe til en anden efterfølgende Web API, begge sikret af v2.0 slutpunkt.  Dette scenario er fælles for oprindelige klienter, der har en Web API back-end, der kalder en Microsoft Online-tjeneste, såsom Office 365 eller Graph API.

Dette sammenkædede Web API-scenarie kan understøttes ved hjælp af OAuth 2.0 Jwt bæreren legitimationsoplysninger ydelse, også kendt som [On-Behalf-Of dataflow](active-directory-v2-protocols.md#oauth2-on-behalf-of-flow).  Dog er strømmen på vegne af-ikke implementeret for øjeblikket i v2.0 slutpunkt.  Se, hvordan dette flow fungerer i alment tilgængelig Azure AD tjeneste, skal du se [på vegne af kodeeksempel på GitHub](https://github.com/AzureADSamples/WebAPI-OnBehalfOf-DotNet).
