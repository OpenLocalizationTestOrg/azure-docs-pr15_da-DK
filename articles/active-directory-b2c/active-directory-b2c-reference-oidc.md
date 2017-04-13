<properties
    pageTitle="Azure Active Directory B2C | Microsoft Azure"
    description="Opbygning af webprogrammer ved hjælp af Azure Active Directory-implementering af OpenID forbinde godkendelse-protokollen."
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
    ms.topic="article"
    ms.date="07/22/2016"
    ms.author="dastrock"/>

# <a name="azure-active-directory-b2c-web-sign-in-with-openid-connect"></a>Azure Active Directory-B2C: Web logge på med OpenID forbindelse

OpenID Connect er en godkendelsesprotokol, indbygget oven på OAuth 2.0, der kan bruges til sikker signering brugere i web-programmer.  Du kan udlicitere tilmelding, log på ved hjælp af Azure Active Directory (Azure AD) B2C implementeringen af OpenID Connect, og andre identitet management oplever i dine webprogrammer til Azure AD. Denne vejledning viser dig, hvordan gøre det på en måde, uafhængig af sprog. Det beskrives det, hvordan til at sende og modtage HTTP-meddelelser uden at bruge en af vores open source-biblioteker.

[OpenID forbinde](http://openid.net/specs/openid-connect-core-1_0.html) udvider protokollen *godkendelse* OAuth 2.0 til brug som en *godkendelse* protokol. Dette kan du udføre enkeltlogon ved hjælp af OAuth. Det introducerer begrebet en `id_token`, som er et sikkerhedstoken, der gør det muligt for klienten til at bekræfte identiteten for brugeren, og få grundlæggende profiloplysninger om brugeren.

Fordi den udvider OAuth 2.0, kan det også apps til at hente sikkert **access_tokens**. Du kan bruge access_tokens til at få adgang til ressourcer, der er sikret med en [tilladelse server](active-directory-b2c-reference-protocols.md#the-basics). Vi anbefaler OpenID opretter forbindelse, hvis du opretter et webprogram, der er hostet på en server og adgang til via en browser. Hvis du vil tilføje identitet administration til din mobiltelefon eller en pc-programmer ved hjælp af Azure AD B2C, skal du bruge [OAuth 2.0](active-directory-b2c-reference-oauth-code.md) i stedet for at oprette forbindelse OpenID.

Azure AD-B2C udvider standard OpenID forbinde protokollen for at gøre mere end simple godkendelse og autorisation. Det introducerer [**politikparameter**](active-directory-b2c-reference-policies.md), som gør det muligt at bruge OpenID Connect til at føje brugeroplevelser til din app – f.eks tilmeldingsprocessen logon og administration af profil. Her viser vi dig, hvordan du bruger OpenID forbinde og politikker til at implementere hver af disse oplevelser i dine webprogrammer. Også lærer du at hente access_tokens til at få adgang til internettet API'er.

Eksempel på HTTP-anmodninger nedenfor bruger vores eksempel B2C directory, **fabrikamb2c.onmicrosoft.com**, samt vores eksempel programmet **https://aadb2cplayground.azurewebsites.net** og politikker. Du er meget Velkommen til at prøve anmodninger om dig selv ved hjælp af disse værdier, eller du kan erstatte dem med dine egne.
Lær, hvordan du [kommer i din egen B2C lejer, programmet, og politikker](#use-your-own-b2c-directory).

## <a name="send-authentication-requests"></a>Sende anmodninger om godkendelse
Når din online skal godkende brugeren, og Udfør en politik, kan den dirigerer brugeren til den `/authorize` slutpunkt. Dette er den interaktive del af strømmen, hvor brugeren kan overtage handling, afhængigt af politikken.

I denne anmodning klienten angiver de tilladelser, som den skal bruge til at hente fra brugeren i den `scope` parameter og politikken til at udføre i den `p` parameter. Tre eksempler er angivet under (med linjeskift for læsbarhed), hver med en anden politik. Til at få føling hvordan virker hver anmodning, prøv indsætte anmodningen i en browser og køre programmet.

#### <a name="use-a-sign-in-policy"></a>Bruge en politik for logon

```
GET https://login.microsoftonline.com/fabrikamb2c.onmicrosoft.com/oauth2/v2.0/authorize?
client_id=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6
&response_type=code+id_token
&redirect_uri=https%3A%2F%2Faadb2cplayground.azurewebsites.net%2F
&response_mode=form_post
&scope=openid%20offline_access
&state=arbitrary_data_you_can_receive_in_the_response
&nonce=12345
&p=b2c_1_sign_in
```

#### <a name="use-a-sign-up-policy"></a>Bruge en politik for tilmelding

```
GET https://login.microsoftonline.com/fabrikamb2c.onmicrosoft.com/oauth2/v2.0/authorize?
client_id=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6
&response_type=code+id_token
&redirect_uri=https%3A%2F%2Faadb2cplayground.azurewebsites.net%2F
&response_mode=form_post
&scope=openid%20offline_access
&state=arbitrary_data_you_can_receive_in_the_response
&nonce=12345
&p=b2c_1_sign_up
```

#### <a name="use-an-edit-profile-policy"></a>Bruge en politik for Rediger profil

```
GET https://login.microsoftonline.com/fabrikamb2c.onmicrosoft.com/oauth2/v2.0/authorize?
client_id=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6
&response_type=code+id_token
&redirect_uri=https%3A%2F%2Faadb2cplayground.azurewebsites.net%2F
&response_mode=form_post
&scope=openid%20offline_access
&state=arbitrary_data_you_can_receive_in_the_response
&nonce=12345
&p=b2c_1_edit_profile
```

| Parameter | Påkrævet? | Beskrivelse |
| ----------------------- | ------------------------------- | ----------------------- |
| client_id | Påkrævet | Det program-ID, der [Azure-portalen](https://portal.azure.com/) , der er tildelt til din app. |
| response_type | Påkrævet | Svartypen, der skal indeholde `id_token` for OpenID Connect. Hvis din online skal også tokens for opkald til en web API, kan du bruge `code+id_token`, som vi har gjort det her. |
| redirect_uri | Anbefalede | Redirect_uri af din app, hvor godkendelse svar kan sendes og modtages af din app. Det skal svare nøjagtigt til en af de redirect_uris, der er registreret i portalen, bortset fra at den skal være URL-kodes. |
| omfang | Påkrævet | En adskilt af mellemrum liste over områder. En enkelt omfang værdi angiver til Azure AD begge af de tilladelser, der er der anmodes om. Den `openid` omfang angiver en tilladelse til at logge på brugeren og få data om brugeren i form af **id_tokens** (mere på vej på dette senere i denne artikel). Den `offline_access` omfang er valgfrit for webapps. Det angiver, at din app skal **refresh_token** for langlivet adgang til ressourcer. |
| response_mode | Anbefalede | Den metode, der skal bruges til at sende den resulterende authorization_code tilbage til din app. Det kan være en af 'forespørgsel', 'form_post' eller 'fragment'.  'form_post' anbefales til bedste sikkerhed. |
| stat | Anbefalede | En værdi, der er inkluderet i anmodningen, der returneres i svaret token. Det kan være en streng med indhold, du ønsker. En tilfældigt entydig værdi bruges typisk til at forhindre på tværs af websteder anmodning forfalskning angreb. Tilstanden bruges også til at kode oplysninger om brugerens tilstand i appen, før der opstod anmodningen om godkendelse, som de er på siden. |
| Nonce | Påkrævet | En værdi, der er inkluderet i anmodningen (genereres af appen), der skal medtages i den resulterende id_token som et krav. Appen kan derefter kontrollere denne værdi for at reducere token genafspilningsangreb. Værdien er typisk en randomiseret, entydigt streng, der kan bruges til at identificere origin for din anmodning. |
| p | Påkrævet | Politikken, der skal udføres. Det er navnet på en politik, der er oprettet i din B2C lejer. Værdien politik navn skal begynde med "b2c\_1\_". Lær mere om politikker i [Extensible policy framework](active-directory-b2c-reference-policies.md). |
| prompten | Valgfri | Typen af brugerinput, der er påkrævet. Den eneste gyldige værdi på nuværende tidspunkt er 'login', som brugeren skal angive deres legitimationsoplysninger på forespørgslen. Enkeltlogon træder ikke i kraft. |

På dette tidspunkt brugeren bliver bedt om Fuldfør politikkens arbejdsprocessen. Dette kan betyde, at brugeren at indtaste deres brugernavn og adgangskode, logge på med en sociale identitet ved tilmelding til mappen eller et andet nummer trin, afhængigt af hvordan politikken er defineret.

Når brugeren er fuldført politikken, Azure AD vil returnere et svar til din app på den angivne `redirect_uri`, ved hjælp af metoden, der er angivet i den `response_mode` parameter. Svaret bliver præcis det samme for hver af ovennævnte tilfælde, uafhængigt af politikken, der blev udført.

En vellykket svar ved hjælp af `response_mode=fragment` ville se således:

```
GET https://aadb2cplayground.azurewebsites.net/#
id_token=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...
&code=AwABAAAAvPM1KaPlrEqdFSBzjqfTGBCmLdgfSTLEMPGYuNHSUYBrq...
&state=arbitrary_data_you_can_receive_in_the_response
```

| Parameter | Beskrivelse |
| ----------------------- | ------------------------------- |
| id_token | Den id_token, der anmodes om en appen. Du kan bruge id_token til at kontrollere brugerens identitet og starte en session med brugeren. Flere oplysninger om id_tokens og deres indhold er inkluderet i [Azure AD B2C token reference](active-directory-b2c-reference-tokens.md). |
| kode | Den authorization_code, appen har skrevet, hvis du har brugt `response_type=code+id_token`. Appen kan bruge koden tilladelse til at anmode om en access_token for et destinationsprogram ressource. Authorization_codes er meget korttidsoplysninger. Typisk udløber de efter omkring 10 minutter. |
| stat | Hvis en parameter for tilstand er medtaget i anmodningen, skal den samme værdi vises i svaret. APP'en skal kontrollere, at værdierne for tilstanden i anmodningen og svaret er ens. |

Fejlsvar kan også sendes til den `redirect_uri` så appen kan håndtere dem korrekt:

```
GET https://aadb2cplayground.azurewebsites.net/#
error=access_denied
&error_description=the+user+canceled+the+authentication
&state=arbitrary_data_you_can_receive_in_the_response
```

| Parameter | Beskrivelse |
| ----------------------- | ------------------------------- |
| fejl | En streng fejlen kode, der kan bruges til at klassificere typer fejl, der opstår, og kan bruges til at reagere på fejl. |
| error_description | En bestemt fejlmeddelelse, der kan hjælpe en udvikler identificere den egentlige årsag til en godkendelsesfejl. |
| stat | Se den fuldstændige beskrivelse i tabellen ovenfor. Hvis en parameter for tilstand er medtaget i anmodningen, skal den samme værdi vises i svaret. APP'en skal kontrollere, at værdierne for tilstanden i anmodningen og svaret er ens. |


## <a name="validate-the-idtoken"></a>Validere id_token
Kun modtage en id_token er ikke nok til at godkende brugeren – skal du kontrollere de id_token signatur og bekræfte krav i tokenet ud fra din app krav. Azure AD-B2C bruger [JSON Web Tokens (JWTs)](http://self-issued.info/docs/draft-ietf-oauth-json-web-token.html) og kryptering med offentlig nøgle til at logge tokens og bekræfte, at de er gyldige.

Der findes mange open source-biblioteker, der er tilgængelige til validering af JWTs, afhængigt af dit sprog rækkefølge. Vi anbefaler, at udforske de relevante indstillinger i stedet for at implementere dine egne validering logik. Oplysningerne her, vil være nyttig forståelse af Sådan korrekt brug af disse biblioteker.

Azure AD-B2C har et OpenID forbinde metadata slutpunkt, som gør det muligt for en app til at hente oplysninger om Azure AD B2C på kørselstidspunktet. Disse oplysninger omfatter slutpunkter, token indhold og token logge taster. Der er et dokument, JSON metadata for hver politik skal skrives i din B2C lejer. For eksempel dokumentets metadata for den `b2c_1_sign_in` politik i den `fabrikamb2c.onmicrosoft.com` er placeret på:

`https://login.microsoftonline.com/fabrikamb2c.onmicrosoft.com/v2.0/.well-known/openid-configuration?p=b2c_1_sign_in`

En af egenskaberne for dokumentet konfiguration er den `jwks_uri`, hvis værdien for denne politik skal være:

`https://login.microsoftonline.com/fabrikamb2c.onmicrosoft.com/discovery/v2.0/keys?p=b2c_1_sign_in`.

For at finde ud af, hvilken politik blev brugt i logge en id_token (og hvor du kan hente metadata fra) i, har du to muligheder. Første gang, politikkens navn er inkluderet i den `acr` gøre krav på id_token. Du kan finde oplysninger om, hvordan du Fortolk krav fra en id_token [Azure AD B2C token reference](active-directory-b2c-reference-tokens.md). Din anden mulighed er at kode politikken i værdien af den `state` parameter, når du udstede anmodningen, og derefter afkode den for at finde ud af, hvilken politik blev brugt. Begge metoder er helt korrekt.

Når du har hentet Metadatadokumentet fra det OpenID forbinde metadata slutpunkt, kan du bruge de RSA 256 offentlige nøgler (som er placeret på dette slutpunkt) til at validere signaturen for id_token. Der kan være flere taster, der er angivet på dette slutpunkt på et bestemt tidspunkt i time, hver identificeret med en `kid`. Overskriften for id_token indeholder også en `kid` gøre krav, hvilket angiver, hvilken af disse taster blev brugt til at logge på id_token. Se [Azure AD B2C token reference](active-directory-b2c-reference-tokens.md) til flere oplysninger, herunder sektioner på [validering af tokens](active-directory-b2c-reference-tokens.md#validating-tokens) og [vigtige oplysninger om at logge vigtige overgang](active-directory-b2c-reference-tokens.md#validating-tokens).
<!--TODO: Improve the information on this-->

Når du har valideret signaturen for id_token, er der flere krav, skal du kontrollere, for eksempel:

- Du skal validere det `nonce` gøre krav på for at forhindre token genafspilningsangreb. Værdien skal være hvad du har angivet i anmodningen logon.
- Du skal validere det `aud` gøre krav på at sikre, at id_token, er udstedt til din app. Værdien skal være program-ID af din app.
- Du skal validere det `iat` og `exp` udgiver sig for at sikre, at id_token ikke er udløbet.

Der findes også flere flere valideringer, du skal udføre, er beskrevet mere detaljeret i [OpenID forbinde Core specifikation](http://openid.net/specs/openid-connect-core-1_0.html).  Du kan også vil validere ekstra krav, afhængigt af scenariet. Nogle almindelige valideringer omfatter:

- At sikre, at brugeren/organisation har tilmeldt for appen.
- At sikre, at brugeren har stort godkendelse/rettigheder.
- At sikre, at der opstod en bestemt styrken af godkendelse som Azure Multi-Factor Authentication.

Du kan finde flere oplysninger om krav i en id_token, [Azure AD B2C token reference](active-directory-b2c-reference-tokens.md).

Når du har helt godkendt id_token, kan du starte en session med brugeren og bruge krav i id_token vil have oplysninger om brugeren i din app. Disse oplysninger kan bruges til visning, poster, godkendelse osv.

## <a name="get-a-token"></a>Få et token
Hvis alt, er din web app skal du gøre udføre politikker, kan du springe de næste par afsnit. Disse afsnit gælder kun for web apps, der skal træffe godkendte opkald til en web API og er beskyttet af Azure AD B2C.

Du kan indløse authorization_code, som du har købt (ved hjælp af `response_type=code+id_token`) for et token til den ønskede ressource ved at sende en `POST` anmode om at den `/token` slutpunkt. I øjeblikket, er den eneste ressource, kan du anmode om et token for webstedet Apps egen back end-API. Konferencen til at anmode om et token til dig selv, er at bruge din app klient-ID som omfanget:

```
POST fabrikamb2c.onmicrosoft.com/oauth2/v2.0/token?p=b2c_1_sign_in HTTP/1.1
Host: https://login.microsoftonline.com
Content-Type: application/x-www-form-urlencoded

grant_type=authorization_code&client_id=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6&scope=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6 offline_access&code=AwABAAAAvPM1KaPlrEqdFSBzjqfTGBCmLdgfSTLEMPGYuNHSUYBrq...&redirect_uri=urn:ietf:wg:oauth:2.0:oob&client_secret=<your-application-secret>

```

| Parameter | Påkrævet? | Beskrivelse |
| ----------------------- | ------------------------------- | --------------------- |
| p | Påkrævet | Den politik, der blev brugt til at få fat på godkendelseskoden. Du kan ikke bruge en anden politik i mødeindkaldelsen. Bemærk, at du føjer denne parameter til **forespørgselsstreng**, ikke i brødteksten INDLÆG. |
| client_id | Påkrævet | Det program-ID, der [Azure-portalen](https://portal.azure.com/) , der er tildelt til din app. |
| grant_type | Påkrævet | Typen Giv, som skal være `authorization_code` for godkendelse kode strømmen. |
| omfang | Anbefalede | En adskilt af mellemrum liste over områder. En enkelt omfang værdi angiver til Azure AD begge af de tilladelser, der er der anmodes om. Den `openid` omfang angiver en tilladelse til at logge på brugeren og få data om brugeren i form af **id_tokens**. Det kan bruges til at hente tokens til webstedet Apps egen back end-API, der er repræsenteret af det samme program-ID som klienten. Den `offline_access` omfang angiver, at din app skal **refresh_token** for langlivet adgang til ressourcer. |
| kode | Påkrævet | Den authorization_code, som du har anskaffet i den første del af strømmen. |
| redirect_uri | Påkrævet | Redirect_uri af programmet, hvor du har modtaget på authorization_code. |
| client_secret | Påkrævet | Hemmeligheden program, du har oprettet i [Azure-portalen](https://portal.azure.com/). Dette program hemmeligheden er et vigtigt sikkerhed genstand. Du skal gemme den sikkert på din server. Du bør også sørge for at rotere denne klient hemmeligt med jævne mellemrum. |

En vellykket token svar ser sådan ud:

```
{
    "not_before": "1442340812",
    "token_type": "Bearer",
    "access_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...",
    "scope": "90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6 offline_access",
    "expires_in": "3600",
    "refresh_token": "AAQfQmvuDy8WtUv-sd0TBwWVQs1rC-Lfxa_NDkLqpg50Cxp5Dxj0VPF1mx2Z...",
}
```
| Parameter | Beskrivelse |
| ----------------------- | ------------------------------- |
| not_before | Det tidspunkt, hvor tokenet betragtes som gyldige epoke tidspunkt. |
| token_type | Værdien token type. Den eneste type, der understøtter Azure AD er bæreren. |
| access_token | Den signerede JWT-token, du har skrevet. |
| omfang | De områder, der Tokenet er gyldige for, som kan bruges til cachelagring tokens til senere brug. |
| expires_in | Hvor lang tid, som access_token er gyldig (i sekunder). |
| refresh_token | En OAuth 2.0 refresh_token. Appen kan bruge dette token for at anskaffe flere tokens, når det aktuelle token udløber.  Refresh_tokens er længe leve og kan bruges til at bevare adgang til ressourcer for længere perioder. Se [B2C token reference](active-directory-b2c-reference-tokens.md)for at få flere detaljer. Bemærk, at du skal have brugt omfanget `offline_access` i både godkendelse og token anmodninger for at modtage en refresh_token. |

Fejlsvar ser sådan ud:

```
{
    "error": "access_denied",
    "error_description": "The user revoked access to the app.",
}
```

| Parameter | Beskrivelse |
| ----------------------- | ------------------------------- |
| fejl | En streng fejlen kode, der kan bruges til at klassificere typer fejl, der opstår, og kan bruges til at reagere på fejl. |
| error_description | En bestemt fejlmeddelelse, der kan hjælpe en udvikler identificere den egentlige årsag til en godkendelsesfejl. |

## <a name="use-the-token"></a>Brug tokenet
Nu hvor du har hentet korrekt en `access_token`, du kan bruge tokenet i anmodninger til din back-end web API'er ved at medtage det i den `Authorization` sidehoved:

```
GET /tasks
Host: https://mytaskwebapi.com
Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...
```

## <a name="refresh-the-token"></a>Opdatere tokenet
Id_tokens er korttidsoplysninger. Du skal opdatere dem, når de udløber for at fortsætte med at få adgang til ressourcer. Du kan gøre det ved at sende en anden `POST` anmode om at den `/token` slutpunkt. Nuværende tidspunkt, Angiv den `refresh_token` i stedet for den `code`:

```
POST fabrikamb2c.onmicrosoft.com/oauth2/v2.0/token?p=b2c_1_sign_in HTTP/1.1
Host: https://login.microsoftonline.com
Content-Type: application/x-www-form-urlencoded

grant_type=refresh_token&client_id=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6&scope=openid offline_access&refresh_token=AwABAAAAvPM1KaPlrEqdFSBzjqfTGBCmLdgfSTLEMPGYuNHSUYBrq...&redirect_uri=urn:ietf:wg:oauth:2.0:oob&client_secret=<your-application-secret>
```

| Parameter | Påkrævet | Beskrivelse |
| ----------------------- | ------------------------------- | -------- |
| p | Påkrævet | Den politik, der blev brugt til at få fat på den oprindelige refresh_token. Du kan ikke bruge en anden politik i mødeindkaldelsen. Bemærk, at du føjer denne parameter til **forespørgselsstreng**, ikke i brødteksten INDLÆG. |
| client_id | Påkrævet | Det program-ID, der [Azure-portalen](https://portal.azure.com/) , der er tildelt til din app. |
| grant_type | Påkrævet | Typen Giv, som skal være `refresh_token` for denne ben med godkendelse kode forløb. |
| omfang | Anbefalede | En adskilt af mellemrum liste over områder. En enkelt omfang værdi angiver til Azure AD begge af de tilladelser, der er der anmodes om. Den `openid` omfang angiver en tilladelse til at logge på brugeren og få data om brugeren i form af **id_tokens**. Det kan bruges til at hente tokens til webstedet Apps egen back end-API, der er repræsenteret af det samme program-ID som klienten. Den `offline_access` omfang angiver, at din app skal **refresh_token** for langlivet adgang til ressourcer. |
| redirect_uri | Anbefalede | Redirect_uri af programmet, hvor du har modtaget på authorization_code. |
| refresh_token | Påkrævet | Den oprindelige refresh_token, som du købte sammen med den anden del af strømmen. Bemærk, at du skal have brugt omfanget `offline_access` i både godkendelse og token anmodninger for at modtage en refresh_token. |
| client_secret | Påkrævet | Hemmeligheden program, du har oprettet i [Azure-portalen](https://portal.azure.com/). Dette program hemmeligheden er et vigtigt sikkerhed genstand. Du skal gemme den sikkert på din server. Du bør også sørge for at rotere denne klient hemmeligt med jævne mellemrum. |

En vellykket token svar ser sådan ud:

```
{
    "not_before": "1442340812",
    "token_type": "Bearer",
    "access_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...",
    "scope": "90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6 offline_access",
    "expires_in": "3600",
    "refresh_token": "AAQfQmvuDy8WtUv-sd0TBwWVQs1rC-Lfxa_NDkLqpg50Cxp5Dxj0VPF1mx2Z...",
}
```
| Parameter | Beskrivelse |
| ----------------------- | ------------------------------- |
| not_before | Det tidspunkt, hvormed tokenet betragtes som gyldige epoke tidspunkt. |
| token_type | Værdien token type. Den eneste type, der understøtter Azure AD er bæreren. |
| access_token | Den signerede JWT-token, du har skrevet. |
| omfang | De områder, der Tokenet er gyldige for, som kan bruges til cachelagring tokens til senere brug. |
| expires_in | Hvor lang tid, som access_token er gyldig (i sekunder). |
| refresh_token | En OAuth 2.0 refresh_token. Appen kan bruge dette token for at anskaffe flere tokens, når det aktuelle token udløber.  Refresh_tokens er længe leve og kan bruges til at bevare adgang til ressourcer for længere perioder. Se [B2C token reference](active-directory-b2c-reference-tokens.md)for at få flere detaljer. |

Fejlsvar ser sådan ud:

```
{
    "error": "access_denied",
    "error_description": "The user revoked access to the app.",
}
```

| Parameter | Beskrivelse |
| ----------------------- | ------------------------------- |
| fejl | En streng fejlen kode, der kan bruges til at klassificere typer fejl, der opstår, og kan bruges til at reagere på fejl. |
| error_description | En bestemt fejlmeddelelse, der kan hjælpe en udvikler identificere den egentlige årsag til en godkendelsesfejl. |


## <a name="send-a-sign-out-request"></a>Sende en indkaldelse til logge

Når du vil logge af app'en brugeren, er det ikke nok til at rydde din app cookies eller på anden måde end sessionen med brugeren. Du skal også omdirigere brugeren til Azure AD logge af. Hvis du undlader at gøre det, kan brugeren ikke kunne godkende igen til din app uden at angive deres legitimationsoplysninger igen. Dette skyldes, at de har en gyldig enkelt sign-on – session med Azure AD.

Du kan blot omdirigere brugeren på `end_session_endpoint` , der er angivet i det samme OpenID forbinde metadata dokument er beskrevet i det tidligere afsnit "Valider id_token":

```
GET https://login.microsoftonline.com/fabrikamb2c.onmicrosoft.com/oauth2/v2.0/logout?
p=b2c_1_sign_in
&post_logout_redirect_uri=https%3A%2F%2Faadb2cplayground.azurewebsites.net%2F
```

| Parameter | Påkrævet? | Beskrivelse |
| ----------------------- | ------------------------------- | ------------ |
| p | Påkrævet | Politikken, du vil bruge til at logge brugeren af dit program. |
| post_logout_redirect_uri | Anbefalede | URL-adressen, som brugeren skal omdirigeres til efter vellykket logge. Hvis den ikke er inkluderet, vises brugeren en generel meddelelse med Azure AD B2C.  |

> [AZURE.NOTE]
    Mens du dirigerer brugeren til den `end_session_endpoint` vil fjerne nogle af brugerens enkelt sign-on – tilstand med Azure AD B2C, den kan ikke logge brugeren af brugerens sociale identitet udbyder (IDP) session. Hvis brugeren vælger den samme IDP under en efterfølgende logon, de skal være skulle godkendes igen, uden at angive deres legitimationsoplysninger. Hvis en bruger ønsker at logge af dit B2C-program, betyder det ikke nødvendigvis de vil logge af deres Facebook-konto helt. Men i forbindelse med lokale konti brugerens session afsluttes korrekt.

## <a name="use-your-own-b2c-tenant"></a>Bruge dit eget B2C lejer

Hvis du vil prøve disse anmodninger til dig selv, skal du først udføre disse tre trin og derefter erstatte eksempelværdier over med dine egne:

- [Opret en B2C lejer](active-directory-b2c-get-started.md), og tryk på navnet på din lejer i anmodningerne.
- [Opret et program](active-directory-b2c-app-registration.md) for at få et program-ID og en redirect_uri. Du vil medtage en **web app/web api** i din app, og du kan også oprette et **program hemmeligt**.
- [Opret din politikker for](active-directory-b2c-reference-policies.md) at få din politik navne.
