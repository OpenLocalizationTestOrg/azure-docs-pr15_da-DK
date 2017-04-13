
<properties
    pageTitle="Azure AD v2.0 OAuth klient legitimationsoplysninger Flow | Microsoft Azure"
    description="Bygning webprogrammer ved hjælp af Azure AD-implementering af OAuth 2.0 godkendelse-protokollen."
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
    ms.date="09/26/2016"
    ms.author="dastrock"/>

# <a name="v20-protocols---oauth-20-client-credentials-flow"></a>v2.0 protokoller - OAuth 2.0-klienten legitimationsoplysninger dataflow

Den [OAuth 2.0-klienten legitimationsoplysninger give](http://tools.ietf.org/html/rfc6749#section-4.4), også kaldet "Toleddet OAuth" kan bruges til at få adgang til internettet, der er hostet ressourcer ved hjælp af identiteten af et program.  Det bruges normalt til server til server interaktioner, der skal køre i baggrunden uden øjeblikkelig precense af en slutbruger.  Disse typer programmer er ofte kaldes **daemons** eller **tjenestekonti**.

> [AZURE.NOTE]
    Ikke alle Azure Active Directory scenarier og funktioner, der understøttes af v2.0 slutpunkt.  For at bestemme, hvis du skal bruge v2.0 slutpunkt skal du læse om [v2.0 begrænsninger](active-directory-v2-limitations.md).

I den mere typisk tre "treleddet OAuth," er en klientprogrammet givet tilladelse til at få adgang til en ressource på vegne af en bestemt bruger.  Tilladelsen er **delegerede** fra brugeren programmet, som regel under processen [samtykke](active-directory-v2-scopes.md) .  Men i klienten legitimationsoplysninger strømmen har tilladelsen **direkte** til selve programmet.  Når appen præsenterer et token til en ressource, håndhæves ressourcen, selve appen har tilladelse til at udføre en handling - ikke, at en bruger har tilladelse.

## <a name="protocol-diagram"></a>Protocol-diagram
Hele klientens legitimationsoplysninger strømmen ser nogenlunde således ud – alle trin er beskrevet i detaljeret nedenfor.

![Klient legitimationsoplysninger Flow](../media/active-directory-v2-flows/convergence_scenarios_client_creds.png)

## <a name="get-direct-authorization"></a>Får du direkte tilladelse 
Der er to måder, modtager en app typisk direkte tilladelse til at få adgang til en ressource: via en adgangslisten kontrolelement på ressourcen, eller program tilladelsestildeling i Azure AD.  Der er flere andre måder, en ressource kan vælge at Godkend dens klienter, og hver ressource server kan vælge den metode, der giver mest mening for dens anvendelse.  Disse to metoder er de mest almindelige i Azure AD og reccommended for klienter og ressourcer, der vil udføre klient legitimationsoplysninger strømmen.

### <a name="access-control-lists"></a>Adgangskontrollister
En udbyder af givne ressource kan gennemtvinge en tilladelse check ud fra en liste over program-id'er, der kender og giver nogle bestemt adgangsniveau.  Når ressourcen modtager et token fra v2.0 slutpunkt, kan det afkode tokenet og udtrække på klientens program-ID fra den `appid` og `iss` krav.  Det kan derefter sammenligne, programmet mod nogle adgangslisten kontrolelement (ACL) det fører.  Granularitet og metode til kontrolelementet adgangslisten kan variere dramatisk fra ressource til ressource.

En fælles use case til sådanne ACLs er teste løbere for et webprogram eller web api.  World Wide web api kan kun give et undersæt af dets fulde tilladelser til dens forskellige klienter.  Men hvis du vil køre start til slut test på api, en test-klient er oprettet, får tokens fra v2.0 slutpunkt og sender dem til api.  Api kan derefter ACL test klientens program-ID for fuld adgang til hele den api-funktioner.  Bemærk, at hvis du har en oversigt på din tjeneste, du skal du sørge for at validere ikke kun hvem der har `appid`, men også validere, som den `iss` af tokenet, der er tillid til samt.

Denne type godkendelse er fælles for daemons og tjenestekonti, der skal have adgang til data, som ejes af forbrugere brugere med personlige Microsoft-konti.  Det anbefales, at du får fat på den nødvendige tilladelse via programmet perimssions data ejes af organisationer skal.

### <a name="application-permissions"></a>Tilladelser for tjenesteprogram
I stedet for ved hjælp af ACLs API'er kan få vist et sæt af **tilladelser** , som kan tildeles til programmet.  Et program tilladelse tildeles til et program som administrator af en organisation, og kan kun bruges til at få adgang til data, der er ejet af organisationen og dens medarbejdere.  For eksempel viser Microsoft Graph flere tilladelser:

- Læse mail i alle postkasser
- Læse og skrive mail i alle postkasser
- Sender post som en anden bruger
- Læse directory-data
- [+ mere](https://graph.microsoft.io)

For at få fat på disse tilladelser i din app, kan du udføre følgende trin.

#### <a name="request-the-permissions-in-the-app-registration-portal"></a>Anmode om tilladelser i portalen app registrering

- Gå til dit program i [apps.dev.microsoft.com](https://apps.dev.microsoft.com/?referrer=https://azure.microsoft.com/documentation/articles&deeplink=/appList)eller [oprette en app](active-directory-v2-app-registration.md) , hvis du ikke allerede har.  Du skal sikre dig, at dit program har oprettet mindst ét program hemmeligt.
- Find sektionen **Direkte tilladelser for tjenesteprogram** , og Tilføj de tilladelser, der kræver, at din app.
- Sørg for at **gemme** app-registrering

#### <a name="recommended-sign-the-user-into-your-app"></a>Anbefalede: brugeren Log på din app

Når oprettelse af et program, der benytter tilladelser for tjenesteprogram, skal app typisk skal have en side/visning, der gør det muligt for administrator for at godkende på app-tilladelser.  Denne side kan være en del af den app tilmelding forløb, en del af indstillinger for den app eller et dedikeret "forbinde" flow.  I mange situationer giver det mening at vise dette App "visning af tilslutning", når en bruger har logget på med en arbejdet eller skolen Microsoft-konto.

Logger brugeren på app, kan du angive den organziation, som brugeren tilhører før bede dem om at godkende tilladelser for tjenesteprogram.  Mens ikke absolut nødvendigt, kan det hjælpe dig med at oprette en mere intuitiv oplevelse for brugerne organisatoriske.  For at logge brugeren i skal du følge vores [v2.0 protocol selvstudier](active-directory-v2-protocols.md).

#### <a name="request-the-permissions-from-a-directory-admin"></a>Anmode om tilladelser fra en directory-administrator

Når du er klar til at anmode om tilladelser fra virksomhedens administrator, kan du omdirigere brugeren v2.0 **administrator samtykke slutpunkt**.

```
// Line breaks for legibility only

GET https://login.microsoftonline.com/{tenant}/adminconsent?
client_id=6731de76-14a6-49ae-97bc-6eba6914391e
&state=12345
&redirect_uri=http://localhost/myapp/permissions
```

```
// Pro Tip: Try pasting the below request in a browser!
```

```
https://login.microsoftonline.com/common/adminconsent?client_id=6731de76-14a6-49ae-97bc-6eba6914391e&state=12345&redirect_uri=http://localhost/myapp/permissions
```

| Parameter | | Beskrivelse |
| ----------------------- | ------------------------------- | --------------- |
| lejer | påkrævet | Directory lejeren, som du vil anmode om tilladelse fra.  Kan angives i formatet fulde navn eller guid.  Hvis du ikke ved, hvilken lejer, brugeren tilhører og vil lade dem logge på med en hvilken som helst lejeradministration, skal du bruge `common`. |
| client_id | påkrævet | Program-Id, portalen registrering ([apps.dev.microsoft.com](https://apps.dev.microsoft.com/?referrer=https://azure.microsoft.com/documentation/articles&deeplink=/appList)) tildelt din app. |
| redirect_uri | påkrævet | Den redirect_uri, hvor svaret skal sendes til din app til at håndtere.  Det skal svare nøjagtigt til en af de redirect_uris, der er registreret i portalen, bortset fra den skal være URL-kodes og kan have flere sti målgrupper. |
| stat | anbefalede | En værdi, der er inkluderet i anmodningen, der returneres i svaret token.  Det kan være en streng med det indhold, du ønsker.  Tilstanden bruges til at kode oplysninger om brugerens tilstand i appen, før der opstod anmodningen om godkendelse, som siden eller visningen, det var på. |

På dette tidspunkt gennemtvinger Azure AD, kun lejeradministrator kan logge på at fuldføre anmodningen.  Administratoren vil blive bedt om at godkende alle de direkte programmet tilladelser, som du har anmodet om for din app i portalen registrering. 

##### <a name="successful-response"></a>Vellykket svar
Hvis administratoren godkender tilladelser for dit program, bliver vellykket svaret:

```
GET http://localhost/myapp/permissions?tenant=a8990e1f-ff32-408a-9f8e-78d3b9139b95&state=state=12345&admin_consent=True
```

| Parameter | Beskrivelse |
| ----------------------- | ------------------------------- | --------------- |
| lejer | Directory lejeren, der har givet dit program de tilladelser, den anmodet om, i guid-formatet. |
| stat | En værdi, der er inkluderet i anmodningen, der returneres i svaret token.  Det kan være en streng med det indhold, du ønsker.  Tilstanden bruges til at kode oplysninger om brugerens tilstand i appen, før der opstod anmodningen om godkendelse, som siden eller visningen, det var på. |
| admin_consent | Indstilles til `True`. |


##### <a name="error-response"></a>Fejlsvar
Hvis administratoren ikke godkender tilladelser for dit program, bliver mislykkedes svaret:

```
GET http://localhost/myapp/permissions?error=permission_denied&error_description=The+admin+canceled+the+request
```

| Parameter | Beskrivelse |
| ----------------------- | ------------------------------- | --------------- |
| fejl | En streng fejlen kode, der kan bruges til at klassificere typer fejl, der opstår, og kan bruges til at reagere på fejl. |
| error_description | En bestemt fejlmeddelelse, der kan hjælpe en udvikler identificere den egentlige årsag til en fejl.  |

Når du har modtaget en vellykket svar fra app klargøring af slutpunkt, har din app fået de direkte adgangstilladelser for den ønskede.  Nu kan du flytte til anmoder om et token for den ønskede ressource.

## <a name="get-a-token"></a>Få et token
Når du har hentet den nødvendige tilladelse for dit program, kan du fortsætte med indhente access tokens til API'er.  For at få et token ved hjælp af klienten legitimationsoplysninger Giv skal du sende en indkaldelse til INDLÆG til den `/token` v2.0 slutpunkt:

```
POST /common/oauth2/v2.0/token HTTP/1.1
Host: login.microsoftonline.com
Content-Type: application/x-www-form-urlencoded

client_id=535fb089-9ff3-47b6-9bfb-4f1264799865&scope=https%3A%2F%2Fgraph.microsoft.com%2F.default&client_secret=qWgdYAmab0YSkuL1qKv5bPX&grant_type=client_credentials
```

```
curl -X POST -H "Content-Type: application/x-www-form-urlencoded" -d 'client_id=535fb089-9ff3-47b6-9bfb-4f1264799865&scope=https%3A%2F%2Fgraph.microsoft.com%2F.default&client_secret=qWgdYAmab0YSkuL1qKv5bPX&grant_type=client_credentials' 'https://login.microsoftonline.com/common/oauth2/v2.0/token'
```

| Parameter | | Beskrivelse |
| ----------------------- | ------------------------------- | --------------- |
| client_id | påkrævet | Program-Id, portalen registrering ([apps.dev.microsoft.com](https://apps.dev.microsoft.com/?referrer=https://azure.microsoft.com/documentation/articles&deeplink=/appList)) tildelt din app. |
| omfang | påkrævet | Den værdi, der overføres den `scope` parameter i mødeindkaldelsen skal være ressource-id (App-ID URI) på den ønskede ressource, udstyret med den `.default` suffiks.  Så til Microsoft Graph eksemplet, værdien skal være `https://graph.microsoft.com/.default`.  Denne værdi oplyser v2.0 slutpunktet, i alle de direkte programmet tilladelser, du har konfigureret for din app, den skal for at udstede et token for dem, der vedrører den ønskede ressource. |
| client_secret | påkrævet | Hemmeligheden program, du har oprettet i portalen registrering for din app. |
| grant_type | påkrævet | Skal være `client_credentials`. | 

#### <a name="successful-response"></a>Vellykket svar
En vellykket svar tager formularen:

```
{
  "token_type": "Bearer",
  "expires_in": 3599,
  "access_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik1uQ19WWmNBVGZNNXBP..."
}
```

| Parameter | Beskrivelse |
| ----------------------- | ------------------------------- |
| access_token | Nødvendige adgangstoken. Appen kan bruge dette token til at godkende sikret ressourcen, som en web API. |
| token_type | Angiver værdien token type. Den eneste type, der er Azure AD understøtter `Bearer`.  |
| expires_in | Hvor længe adgangstoken er gyldig (i sekunder). |

#### <a name="error-response"></a>Fejlsvar
En fejlsvar tager formularen:

```
{
  "error": "invalid_scope",
  "error_description": "AADSTS70011: The provided value for the input parameter 'scope' is not valid. The scope https://foo.microsoft.com/.default is not valid.\r\nTrace ID: 255d1aef-8c98-452f-ac51-23d051240864\r\nCorrelation ID: fb3d2015-bc17-4bb9-bb85-30c5cf1aaaa7\r\nTimestamp: 2016-01-09 02:02:12Z",
  "error_codes": [
    70011
  ],
  "timestamp": "2016-01-09 02:02:12Z",
  "trace_id": "255d1aef-8c98-452f-ac51-23d051240864",
  "correlation_id": "fb3d2015-bc17-4bb9-bb85-30c5cf1aaaa7"
}
```

| Parameter | Beskrivelse |
| ----------------------- | ------------------------------- |
| fejl | En streng fejlen kode, der kan bruges til at klassificere typer fejl, der opstår, og kan bruges til at reagere på fejl. |
| error_description | En bestemt fejlmeddelelse, der kan hjælpe en udvikler identificere den egentlige årsag til en godkendelsesfejl.  |
| error_codes | En liste over STS bestemte fejlkoder, der kan hjælpe med at diagnosticering.  |
| tidsstempel | Det tidspunkt, hvor der opstod en fejl. |
| trace_id | Et entydigt id for den forespørgsel, der kan hjælpe med at diagnosticering.  |
| correlation_id | Et entydigt id for den forespørgsel, der kan hjælpe med at diagnosticering på tværs af komponenter. |

## <a name="use-a-token"></a>Bruge et token
Nu, hvor du har hentet et token, kan du bruge dette token til at anmodninger om ressourcen.  Når tokenet udløber, skal du blot Gentag anmodningen, så den `/token` slutpunkt til at hente et nyt adgangstoken.

```
GET /v1.0/me/messages
Host: https://graph.microsoft.com
Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...
```

```
// Pro Tip: Try the below command out! (but replace the token with your own)
```

```
curl -X GET -H "Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q" 'https://graph.microsoft.com/v1.0/me/messages'
```

## <a name="code-sample"></a>Eksempel på kode
Se vores [v2.0 daemon kodeeksempel](https://github.com/Azure-Samples/active-directory-dotnet-daemon-v2)for at få vist et eksempel på et program, implementerer client_credentials give ved hjælp af administratoren samtykke slutpunkt.
