

<properties
    pageTitle="Azure AD v2.0 OAuth godkendelse kode Flow | Microsoft Azure"
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
    ms.date="08/08/2016"
    ms.author="dastrock"/>

# <a name="v20-protocols---oauth-20-authorization-code-flow"></a>v2.0 protokoller - OAuth 2.0 godkendelse kode Flow

OAuth 2.0 godkendelse kode Giv kan bruges i apps, der er installeret på en enhed for at få adgang til beskyttede ressourcer, som web API'er.  Bruger app model v2.0 implementering af OAuth 2.0, kan du tilføje logge på og API-adgang til dine bærbare og stationære apps.  Denne vejledning er uafhængig af sprog, og beskriver, hvordan du kan sende og modtage HTTP-meddelelser uden at bruge en af vores open source-biblioteker.

<!-- TODO: Need link to libraries -->

> [AZURE.NOTE]
    Ikke alle Azure Active Directory scenarier og funktioner, der understøttes af v2.0 slutpunkt.  For at bestemme, hvis du skal bruge v2.0 slutpunkt skal du læse om [v2.0 begrænsninger](active-directory-v2-limitations.md).

OAuth 2.0 godkendelse kode strømmen er beskrevet i i [punkt 4.1 i OAuth 2.0-specifikationen](http://tools.ietf.org/html/rfc6749).  Det bruges til at udføre godkendelse og autorisation i fleste typer app, herunder [webapps](active-directory-v2-flows.md#web-apps) og [indbygget installeret apps](active-directory-v2-flows.md#mobile-and-native-apps).  Det gør det muligt for apps til at hente sikkert access_tokens, som kan bruges til at få adgang til ressourcer, der er sikret ved hjælp af v2.0 slutpunkt.  

## <a name="protocol-diagram"></a>Protocol-diagram
På et højt niveau ser hele godkendelse forløbet for en oprindelig/mobile-programmet lidt sådan ud:

![OAuth Auth kode Flow](../media/active-directory-v2-flows/convergence_scenarios_native.png)

## <a name="request-an-authorization-code"></a>Anmode om en tilladelse kode
Godkendelse kode strømmen begynder med klienten dirigerer brugeren til den `/authorize` slutpunkt.  I denne anmodning angiver klienten de tilladelser, der skal til at hente fra brugeren:

```
// Line breaks for legibility only

https://login.microsoftonline.com/{tenant}/oauth2/v2.0/authorize?
client_id=6731de76-14a6-49ae-97bc-6eba6914391e
&response_type=code
&redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F
&response_mode=query
&scope=openid%20offline_access%20https%3A%2F%2Fgraph.microsoft.com%2Fmail.read
&state=12345
```

> [AZURE.TIP] Klik på linket nedenfor for at udføre denne anmodning! Når du logger på, skal din browser skal omdirigeres til `https://localhost/myapp/` med en `code` på adresselinjen.
    <a href="https://login.microsoftonline.com/common/oauth2/v2.0/authorize?client_id=6731de76-14a6-49ae-97bc-6eba6914391e&response_type=code&redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F&response_mode=query&scope=openid%20offline_access%20https%3A%2F%2Fgraph.microsoft.com%2Fmail.read&state=12345" target="_blank">https://login.microsoftonline.com/common/oauth2/v2.0/Authorize...</a>

| Parameter | | Beskrivelse |
| ----------------------- | ------------------------------- | --------------- |
| lejer | påkrævet | Den `{tenant}` værdi i sti for din anmodning kan bruges til at styre, hvem der ikke kan logge på programmet.  De tilladte værdier er `common`, `organizations`, `consumers`, og lejer id'er.  Du kan finde flere detaljer, [protocol grundlæggende](active-directory-v2-protocols.md#endpoints). |
| client_id | påkrævet | Program-Id, portalen registrering ([apps.dev.microsoft.com](https://apps.dev.microsoft.com/?referrer=https://azure.microsoft.com/documentation/articles&deeplink=/appList)) tildelt din app. |
| response_type | påkrævet | Må indeholde `code` for godkendelse kode strømmen. |
| redirect_uri | anbefalede | Redirect_uri af din app, hvor godkendelse svar kan sendes og modtages af din app.  Det skal svare nøjagtigt til en af de redirect_uris, der er registreret i portalen, bortset fra det skal være URL-kodes.  Til oprindelig og mobile-apps, skal du bruge standardværdien for `https://login.microsoftonline.com/common/oauth2/nativeclient`. |
| omfang | påkrævet | En adskilt af mellemrum liste over [områder](active-directory-v2-scopes.md) , du vil brugeren tilladelse til.  |
| response_mode | anbefalede | Angiver den metode, der skal bruges til at sende den resulterende token tilbage til din app.  Kan være `query` eller `form_post`.  |
| stat | anbefalede | En værdi, der er inkluderet i anmodningen, der returneres i svaret token.  Det kan være en streng med det indhold, du ønsker.  En tilfældigt entydig værdi bruges typisk til [at forhindre på tværs af websteder anmodning forfalskning angreb](http://tools.ietf.org/html/rfc6749#section-10.12).  Tilstanden bruges også til at kode oplysninger om brugerens tilstand i appen, før der opstod anmodningen om godkendelse, som siden eller visningen, det var på. |
| prompten | valgfri | Angiver typen brugerinteraktion, der er påkrævet.  På nuværende tidspunkt kun gyldige værdier er 'login', 'Ingen,' og 'samtykke'.  `prompt=login`Tvinger brugeren til at indtaste legitimationsoplysningerne på anmodningen, hvilket eliminerer enkelt Log på.  `prompt=none`er modsat - det sikrer, at brugeren ikke vises med en hvilken som helst interaktiv prompt ene eller den anden.  Hvis anmodningen ikke kan fuldføres uovervåget via single-sign-on, vil v2.0 slutpunktet returnere en fejl.  `prompt=consent`dialogboksen OAuth samtykke, udløses, når brugeren logger på, beder brugeren om at tildele tilladelser til appen. |
| login_hint | valgfri | Kan bruges til at udfylde udfyldt feltet brugernavn/e-mail-adresse i fremgangsmåden logge på siden for brugeren, hvis du kender deres brugernavn forvejen.  Ofte apps anvender denne parameter under ny godkendelse, har du allerede hentet brugernavnet fra en tidligere Log på ved hjælp af den `preferred_username` gøre krav. |
| domain_hint | valgfri | Kan være en af `consumers` eller `organizations`.  Hvis medtaget, vil det springe e-mail-baseret søgningen, der bruger, der er går igennem på siden v2.0 Log på, hvilket medfører en lidt mere strømlinet brugergrænseflade.  Ofte apps anvender denne parameter under godkendelse igen, ved at udtrække den `tid` fra en tidligere logon.  Hvis den `tid` gøre krav på værdien er `9188040d-6c67-4c5b-b112-36a304b66dad`, skal du bruge `domain_hint=consumers`.  Ellers skal bruge `domain_hint=organizations`. |

På dette tidspunkt brugeren bliver bedt om angive deres legitimationsoplysninger og fuldføre godkendelsen.  V2.0 slutpunktet sikrer også, at brugeren har accepteret til de tilladelser, der er angivet i den `scope` forespørgsel parameter.  Hvis brugeren ikke har accepteret til en af disse tilladelser, vil det Bed brugeren om at samtykke til de nødvendige tilladelser.  Oplysninger om [tilladelser, accept, og med flere lejer apps er angivet her](active-directory-v2-scopes.md).

Når brugeren godkender og giver samtykke, v2.0 slutpunktet vil returnere et svar til din app på den angivne `redirect_uri`, ved hjælp af metoden angivet i den `response_mode` parameter.

#### <a name="successful-response"></a>Vellykket svar
En vellykket svar ved hjælp af `response_mode=query` ser således ud:

```
GET https://login.microsoftonline.com/common/oauth2/nativeclient?
code=AwABAAAAvPM1KaPlrEqdFSBzjqfTGBCmLdgfSTLEMPGYuNHSUYBrq...
&state=12345
```

| Parameter | Beskrivelse |
| ----------------------- | ------------------------------- |
| kode | Den authorization_code, der anmodes om en appen. Appen kan bruge koden tilladelse til at anmode om en adgangstoken til ressourcen mål.  Authorization_codes er meget korttidsoplysninger, typisk de udløber efter omkring 10 minutter. |
| stat | Hvis en parameter for tilstand er medtaget i anmodningen, skal den samme værdi vises i svaret. APP'en skal kontrollere, at værdierne for tilstanden i anmodningen og svaret er ens. |

#### <a name="error-response"></a>Fejlsvar
Fejlsvar kan også blive sendt til den `redirect_uri` så appen kan håndtere dem korrekt:

```
GET https://login.microsoftonline.com/common/oauth2/nativeclient?
error=access_denied
&error_description=the+user+canceled+the+authentication
```

| Parameter | Beskrivelse |
| ----------------------- | ------------------------------- |
| fejl | En streng fejlen kode, der kan bruges til at klassificere typer fejl, der opstår, og kan bruges til at reagere på fejl. |
| error_description | En bestemt fejlmeddelelse, der kan hjælpe en udvikler identificere den egentlige årsag til en godkendelsesfejl.  |

#### <a name="error-codes-for-authorization-endpoint-errors"></a>Fejlkoder for godkendelse slutpunkt fejl

I følgende tabel beskrives de forskellige fejlkoder, der kan returneres i den `error` -parameter for fejlsvar.

| Fejlkode | Beskrivelse | Klienthandling |
|------------|-------------|---------------|
| invalid_request | Protocol fejl, som en manglende påkrævet parameter. | Løse og sende anmodningen igen. Dette er en udvikling fejl typisk er taget under indledende testen.|
| unauthorized_client | Klientprogrammet har ikke tilladelse til at anmode om en tilladelse kode. | Dette sker normalt, når klientprogrammet ikke er registreret i Azure AD eller er ikke føjet til brugerens Azure AD-lejer. Programmet kan brugeren med for at installere programmet og føje det til Azure AD-instruktionssæt. |
| adgang nægtet | Ressource ejer nægtet samtykke | Klientprogrammet kan give besked om den bruger, den ikke kan fortsætte, medmindre brugeren giver sit samtykke. |
| unsupported_response_type | Godkendelse serveren understøtter ikke svartypen i anmodningen. | Løse og sende anmodningen igen. Dette er en udvikling fejl typisk er taget under indledende testen.|
|server_error | Der opstod en uventet fejl på serveren. | Forsøg anmodningen igen. Disse fejl kan opstå på midlertidige betingelser. Klientprogrammet kan forklare til brugeren, dens svar er forsinket forfaldsdato en midlertidig fejl. |
| temporarily_unavailable | Serveren er midlertidigt optaget og kan håndtere anmodningen. | Forsøg anmodningen igen. Klientprogrammet kan forklare til brugeren, dens svar er forsinket forfaldsdato en midlertidig tilstand. |
| invalid_resource |Target ressourcen er ugyldig, fordi den ikke findes, Azure AD kan ikke finde den eller den ikke er konfigureret korrekt.| Her angives ressourcen, hvis den findes, ikke som er blevet konfigureret i lejeren. Programmet kan brugeren med for at installere programmet og føje det til Azure AD-instruktionssæt. |

## <a name="request-an-access-token"></a>Anmode om et adgangstoken
Nu, hvor du har hentet en authorization_code og har fået tilladelse af brugeren, kan du indløse den `code` for en `access_token` til den ønskede ressource, ved at sende en `POST` anmode om at den `/token` slutpunkt:

```
// Line breaks for legibility only

POST /{tenant}/oauth2/v2.0/token HTTP/1.1
Host: https://login.microsoftonline.com
Content-Type: application/x-www-form-urlencoded

client_id=6731de76-14a6-49ae-97bc-6eba6914391e
&scope=https%3A%2F%2Fgraph.microsoft.com%2Fmail.read
&code=OAAABAAAAiL9Kn2Z27UubvWFPbm0gLWQJVzCTE9UkP3pSx1aXxUjq3n8b2JRLk4OxVXr...
&redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F
&grant_type=authorization_code
&client_secret=JqQX2PNo9bpM0uEihUPzyrh    // NOTE: Only required for web apps
```

> [AZURE.TIP] Prøv udførelse af denne anmodning i Postman! (Husk at erstatte den `code`)     [ ![Køre i Postman](./media/active-directory-v2-protocols-oauth-code/runInPostman.png)](https://app.getpostman.com/run-collection/8f5715ec514865a07e6a)

| Parameter | | Beskrivelse |
| ----------------------- | ------------------------------- | --------------------- |
| lejer | påkrævet | Den `{tenant}` værdi i sti for din anmodning kan bruges til at styre, hvem der ikke kan logge på programmet.  De tilladte værdier er `common`, `organizations`, `consumers`, og lejer id'er.  Du kan finde flere detaljer, [protocol grundlæggende](active-directory-v2-protocols.md#endpoints). |
| client_id | påkrævet | Program-Id, portalen registrering ([apps.dev.microsoft.com](https://apps.dev.microsoft.com/?referrer=https://azure.microsoft.com/documentation/articles&deeplink=/appList)) tildelt din app. |
| grant_type | påkrævet | Skal være `authorization_code` for godkendelse kode strømmen. |
| omfang | påkrævet | En adskilt af mellemrum liste over områder.  De områder, der anmodes om i denne ben skal være svarer til eller et undersæt af de områder, der anmodes om i den første del.  Hvis de områder, der er angivet i denne anmodning strækker sig over flere ressource servere, returneres v2.0 slutpunktet et token for den ressource, der er angivet i det første område.  Se [tilladelser, accept, og områder](active-directory-v2-scopes.md)for en mere detaljeret forklaring af områder.  |
| kode | påkrævet | Den authorization_code, som du har anskaffet i den første del af strømmen.   |
| redirect_uri | påkrævet | Den samme redirect_uri-værdi, der blev brugt til at få fat på authorization_code. |
| client_secret | kræves til online | Hemmeligheden program, du oprettede i portalen app registrering for din app.  Det skal ikke bruges i en oprindelig app, fordi client_secrets pålideligt ikke kan gemmes på enheder.  Det er påkrævet til online- og web API'er, som har mulighed for at gemme client_secret sikkert på serversiden. |

#### <a name="successful-response"></a>Vellykket svar
En vellykket token svar ser sådan ud:

```
{
    "access_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...",
    "token_type": "Bearer",
    "expires_in": 3599,
    "scope": "https%3A%2F%2Fgraph.microsoft.com%2Fmail.read",
    "refresh_token": "AwABAAAAvPM1KaPlrEqdFSBzjqfTGAMxZGUTdM0t4B4...",
    "id_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJub25lIn0.eyJhdWQiOiIyZDRkMTFhMi1mODE0LTQ2YTctOD...",
}
```
| Parameter | Beskrivelse |
| ----------------------- | ------------------------------- |
| access_token | Nødvendige adgangstoken. Appen kan bruge dette token til at godkende sikret ressourcen, som en web API. |
| token_type | Angiver værdien token type. Den eneste type, der understøtter Azure AD er bæreren  |
| expires_in | Hvor længe adgangstoken er gyldig (i sekunder). |
| omfang | De områder, der er gyldige for access_token. |
| refresh_token |  En OAuth 2.0 Opdater token. Appen kan bruge dette token anskaffe flere access tokens, efter den aktuelle adgangstoken udløber.  Refresh_tokens er langlivet, og kan bruges til at bevare adgang til ressourcer for længere perioder.  Se [v2.0 token reference](active-directory-v2-tokens.md)for at få flere detaljer.  |
| id_token | En usigneret JSON Web Token (JWT). App kan base64Url afkode segmenter af dette token anmode om oplysninger om den bruger, der er logget på. Appen kan cache værdierne og få dem vist, men den må ikke stole på dem til godkendelse og sikkerhedsgrænser.  Du kan finde flere oplysninger om id_tokens [v2.0 slutpunkt token reference](active-directory-v2-tokens.md). |

#### <a name="error-response"></a>Fejlsvar
Fejlsvar ser sådan ud:

```
{
  "error": "invalid_scope",
  "error_description": "AADSTS70011: The provided value for the input parameter 'scope' is not valid. The scope https://foo.microsoft.com/mail.read is not valid.\r\nTrace ID: 255d1aef-8c98-452f-ac51-23d051240864\r\nCorrelation ID: fb3d2015-bc17-4bb9-bb85-30c5cf1aaaa7\r\nTimestamp: 2016-01-09 02:02:12Z",
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

#### <a name="error-codes-for-token-endpoint-errors"></a>Fejlkoder for sikkerhedstoken slutpunkt fejl

| Fejlkode | Beskrivelse | Klienthandling |
|------------|-------------|---------------|
| invalid_request | Protocol fejl, som en manglende påkrævet parameter. | Løse og sende anmodningen igen |
| invalid_grant | Godkendelseskoden er ugyldig eller er udløbet. | Prøv en ny anmodning på `/authorize` slutpunkt |
| unauthorized_client | Godkendte klienten har ikke tilladelse til at bruge denne tilladelse Giv type. | Dette sker normalt, når klientprogrammet ikke er registreret i Azure AD eller er ikke føjet til brugerens Azure AD-lejer. Programmet kan brugeren med for at installere programmet og føje det til Azure AD-instruktionssæt. |
| invalid_client | Klientgodkendelse mislykkedes. | Klient-legitimationsoplysninger er ikke gyldige. For at løse, opdaterer programmet administratoren legitimationsoplysningerne. |
| unsupported_grant_type | Godkendelse serveren understøtter ikke tilladelse Giv type. | Ændre typen Giv i anmodningen. Denne type fejl skal udføres, kun under udvikling og findes under indledende testen. |
| invalid_resource | Target ressourcen er ugyldig, fordi den ikke findes, Azure AD kan ikke finde den eller den ikke er konfigureret korrekt. | Her angives ressourcen, hvis den findes, ikke som er blevet konfigureret i lejeren. Programmet kan brugeren med for at installere programmet og føje det til Azure AD-instruktionssæt. |
| interaction_required | Anmodningen kræver brugerinput. For eksempel er en yderligere godkendelse trin påkrævet. | Prøv igen anmodningen med den samme ressource. |
| temporarily_unavailable | Serveren er midlertidigt optaget og kan håndtere anmodningen. | Forsøg anmodningen igen. Klientprogrammet kan forklare til brugeren, dens svar er forsinket forfaldsdato en midlertidig tilstand.|

## <a name="use-the-access-token"></a>Brug adgangstoken
Nu hvor du har hentet korrekt en `access_token`, du kan bruge tokenet i anmodninger om at Web API'er ved at medtage det i den `Authorization` sidehoved:

> [AZURE.TIP] Udføre denne anmodning i Postman! (Erstatte den `Authorization` sidehoved første)     [ ![Køre i Postman](./media/active-directory-v2-protocols-oauth-code/runInPostman.png)](https://app.getpostman.com/run-collection/8f5715ec514865a07e6a)

```
GET /v1.0/me/messages
Host: https://graph.microsoft.com
Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...
```

## <a name="refresh-the-access-token"></a>Opdatere adgangstoken
Access_tokens er korte leve, og du skal opdatere dem, når de udløber for at fortsætte med at få adgang til ressourcer.  Kan du gøre det ved at sende en anden `POST` anmode om at den `/token` slutpunkt, dette tid, forudsat at den `refresh_token` i stedet for den `code`:

```
// Line breaks for legibility only

POST /{tenant}/oauth2/v2.0/token HTTP/1.1
Host: https://login.microsoftonline.com
Content-Type: application/x-www-form-urlencoded

client_id=6731de76-14a6-49ae-97bc-6eba6914391e
&scope=https%3A%2F%2Fgraph.microsoft.com%2Fmail.read
&refresh_token=OAAABAAAAiL9Kn2Z27UubvWFPbm0gLWQJVzCTE9UkP3pSx1aXxUjq...
&redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F
&grant_type=refresh_token
&client_secret=JqQX2PNo9bpM0uEihUPzyrh    // NOTE: Only required for web apps
```

> [AZURE.TIP] Prøv udførelse af denne anmodning i Postman! (Husk at erstatte den `refresh_token`)     [ ![Køre i Postman](./media/active-directory-v2-protocols-oauth-code/runInPostman.png)](https://app.getpostman.com/run-collection/8f5715ec514865a07e6a)

| Parameter | | Beskrivelse |
| ----------------------- | ------------------------------- | -------- |
| lejer | påkrævet | Den `{tenant}` værdi i sti for din anmodning kan bruges til at styre, hvem der ikke kan logge på programmet.  De tilladte værdier er `common`, `organizations`, `consumers`, og lejer id'er.  Du kan finde flere detaljer, [protocol grundlæggende](active-directory-v2-protocols.md#endpoints). |
| client_id | påkrævet | Program-Id, portalen registrering ([apps.dev.microsoft.com](https://apps.dev.microsoft.com/?referrer=https://azure.microsoft.com/documentation/articles&deeplink=/appList)) tildelt din app. |
| grant_type | påkrævet | Skal være `refresh_token` for denne ben med godkendelse kode forløb. |
| omfang | påkrævet | En adskilt af mellemrum liste over områder.  De områder, der anmodes om i denne ben skal være svarer til eller et undersæt af de områder, der anmodes om i den oprindelige authorization_code anmodning ben.  Hvis de områder, der er angivet i denne anmodning strækker sig over flere ressource servere, returneres v2.0 slutpunktet et token for den ressource, der er angivet i det første område.  Se [tilladelser, accept, og områder](active-directory-v2-scopes.md)for en mere detaljeret forklaring af områder.  |
| refresh_token | påkrævet | Den refresh_token, som du købte sammen med den anden del af strømmen.   |
| redirect_uri | påkrævet | Den samme redirect_uri-værdi, der blev brugt til at få fat på authorization_code. |
| client_secret | kræves til online | Hemmeligheden program, du oprettede i portalen app registrering for din app.  Det skal ikke bruges i en oprindelig app, fordi client_secrets pålideligt ikke kan gemmes på enheder.  Det er påkrævet til online- og web API'er, som har mulighed for at gemme client_secret sikkert på serversiden. |

#### <a name="successful-response"></a>Vellykket svar
En vellykket token svar ser sådan ud:

```
{
    "access_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...",
    "token_type": "Bearer",
    "expires_in": 3599,
    "scope": "https%3A%2F%2Fgraph.microsoft.com%2Fmail.read",
    "refresh_token": "AwABAAAAvPM1KaPlrEqdFSBzjqfTGAMxZGUTdM0t4B4...",
    "id_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJub25lIn0.eyJhdWQiOiIyZDRkMTFhMi1mODE0LTQ2YTctOD...",
}
```
| Parameter | Beskrivelse |
| ----------------------- | ------------------------------- |
| access_token | Nødvendige adgangstoken. Appen kan bruge dette token til at godkende sikret ressourcen, som en web API. |
| token_type | Angiver værdien token type. Den eneste type, der understøtter Azure AD er bæreren  |
| expires_in | Hvor længe adgangstoken er gyldig (i sekunder). |
| omfang | De områder, der er gyldige for access_token. |
| refresh_token |  Et nyt OAuth 2.0 Opdater id. Du skal erstatte det gamle Opdater token med dette netop hentede opdatering token at sikre, at din opdatering tokens forbliver gyldige så længe som muligt.  |
| id_token | En usigneret JSON Web Token (JWT). App kan base64Url afkode segmenter af dette token anmode om oplysninger om den bruger, der er logget på. Appen kan cache værdierne og få dem vist, men den må ikke stole på dem til godkendelse og sikkerhedsgrænser.  Du kan finde flere oplysninger om id_tokens [v2.0 slutpunkt token reference](active-directory-v2-tokens.md). |

#### <a name="error-response"></a>Fejlsvar
```
{
  "error": "invalid_scope",
  "error_description": "AADSTS70011: The provided value for the input parameter 'scope' is not valid. The scope https://foo.microsoft.com/mail.read is not valid.\r\nTrace ID: 255d1aef-8c98-452f-ac51-23d051240864\r\nCorrelation ID: fb3d2015-bc17-4bb9-bb85-30c5cf1aaaa7\r\nTimestamp: 2016-01-09 02:02:12Z",
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

Du kan finde en beskrivelse af fejlkoderne og handlingen anbefalede klient [fejlkoder for sikkerhedstoken slutpunkt fejl](#error-codes-for-token-endpoint-errors).
