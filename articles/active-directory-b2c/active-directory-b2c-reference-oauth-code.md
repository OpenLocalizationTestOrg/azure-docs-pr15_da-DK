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

# <a name="azure-active-directory-b2c-oauth-20-authorization-code-flow"></a>Azure Active Directory-B2C: OAuth 2.0 godkendelse kode flow

OAuth 2.0 godkendelse kode Giv kan bruges i apps, der er installeret på en enhed for at få adgang til beskyttede ressourcer, som web API'er. Ved hjælp af Azure Active Directory (Azure AD) B2C implementeringen af OAuth 2.0, kan du føje tilmelding, logon og andre identitet administrationsopgaver til din bærbare og stationære apps. Denne vejledning er uafhængig sprog. Det beskrives, hvordan til at sende og modtage HTTP-meddelelser uden at bruge en af vores open source-biblioteker.

<!-- TODO: Need link to libraries -->

OAuth 2.0 godkendelse kode strømmen er beskrevet i [punkt 4.1 i OAuth 2.0-specifikationen](http://tools.ietf.org/html/rfc6749). Du kan bruge det til at udføre godkendelse og autorisation i fleste typer app, herunder [webapps](active-directory-b2c-apps.md#web-apps) og [indbygget installeret apps](active-directory-b2c-apps.md#mobile-and-native-apps). Det gør det muligt for apps til at hente sikkert **access_tokens**, som kan bruges til at få adgang til ressourcer, der er sikret med en [tilladelse server](active-directory-b2c-reference-protocols.md#the-basics).

Denne vejledning fokuserer på en bestemt smag af OAuth 2.0 godkendelse kode flow –**offentlige klienter**. En offentlig klient er en hvilken som helst klientprogrammet, der ikke er tillid til sikker opretholde integriteten af en hemmeligt adgangskode. Dette omfatter mobilapps, -programmer på computeren og ret meget et program, der kører på en enhed og for at opnå access_tokens. Hvis du vil føje identitet management til en WebApp ved hjælp af Azure AD B2C, skal du bruge [OpenID forbindelse](active-directory-b2c-reference-oidc.md) i stedet for OAuth 2.0.

Azure AD-B2C udvider standard OAuth 2.0 flyder for at gøre mere end simple godkendelse og autorisation. Det introducerer [**politikparameter**](active-directory-b2c-reference-policies.md), som gør det muligt at bruge OAuth 2.0 til at tilføje brugeroplevelser for dine Apps, som tilmeldingsprocessen logon og administration af profil. Her viser vi, hvordan du bruger OAuth 2.0 og politikker til at implementere hver af disse oplevelser i din oprindelige programmer. Vi også viser, hvordan du få access_tokens til at få adgang til internettet API'er.

Eksempel på HTTP-anmodninger nedenfor bruger vores eksempel B2C directory, **fabrikamb2c.onmicrosoft.com**, samt vores Northwind og politikker. Du er til at prøve anmodninger ud af dig selv ved at bruge disse værdier, eller du kan erstatte dem med dine egne.
Lær, hvordan du [kommer i din egen B2C directory, programmet, og politikker](#use-your-own-b2c-directory).

## <a name="1-get-an-authorization-code"></a>1. få en godkendelseskode for
Godkendelse kode strømmen begynder med klienten dirigerer brugeren til den `/authorize` slutpunkt. Dette er den interaktive del af strømmen, hvor brugeren kan overtage handling. I denne anmodning klienten angiver de tilladelser, som den skal bruge til at hente fra brugeren i den `scope` parameter og politikken til at udføre i den `p` parameter. Tre eksempler er angivet under (med linjeskift for læsbarhed), hver med en anden politik.

#### <a name="use-a-sign-in-policy"></a>Bruge en politik for logon

```
GET https://login.microsoftonline.com/fabrikamb2c.onmicrosoft.com/oauth2/v2.0/authorize?
client_id=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6
&response_type=code
&redirect_uri=urn%3Aietf%3Awg%3Aoauth%3A2.0%3Aoob
&response_mode=query
&scope=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6%20offline_access
&state=arbitrary_data_you_can_receive_in_the_response
&p=b2c_1_sign_in
```

#### <a name="use-a-sign-up-policy"></a>Bruge en politik for tilmelding

```
GET https://login.microsoftonline.com/fabrikamb2c.onmicrosoft.com/oauth2/v2.0/authorize?
client_id=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6
&response_type=code
&redirect_uri=urn%3Aietf%3Awg%3Aoauth%3A2.0%3Aoob
&response_mode=query
&scope=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6%20offline_access
&state=arbitrary_data_you_can_receive_in_the_response
&p=b2c_1_sign_up
```

#### <a name="use-an-edit-profile-policy"></a>Bruge en politik for Rediger profil

```
GET https://login.microsoftonline.com/fabrikamb2c.onmicrosoft.com/oauth2/v2.0/authorize?
client_id=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6
&response_type=code
&redirect_uri=urn%3Aietf%3Awg%3Aoauth%3A2.0%3Aoob
&response_mode=query
&scope=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6%20offline_access
&state=arbitrary_data_you_can_receive_in_the_response
&p=b2c_1_edit_profile
```

| Parameter | Påkrævet? | Beskrivelse |
| ----------------------- | ------------------------------- | ----------------------- |
| client_id | Påkrævet | Det program-ID, der [Azure-portalen](https://portal.azure.com) , der er tildelt til din app. |
| response_type | Påkrævet | Svartypen, der skal indeholde `code` for godkendelse kode strømmen. |
| redirect_uri | Påkrævet | Redirect_uri af din app, hvor godkendelse svar kan sendes og modtages af din app. Det skal svare nøjagtigt til en af de redirect_uris, der er registreret i portalen, bortset fra at den skal være URL-kodes. |
| omfang | Påkrævet | En adskilt af mellemrum liste over områder. En enkelt omfang værdi angiver til Azure AD begge af de tilladelser, der er der anmodes om. Brug af klient-ID, som omfanget angiver, at din app kræver en **få adgang til token** , der kan bruges med dine egne tjeneste eller web API, repræsenteret af det samme klient-ID.  Den `offline_access` omfang angiver, at din app skal **refresh_token** for langlivet adgang til ressourcer.  Du kan også bruge den `openid` omfang for at anmode om en **id_token** fra Azure AD B2C. |
| response_mode | Anbefalede | Den metode, der skal bruges til at sende den resulterende authorization_code tilbage til din app. Det kan være en af 'forespørgsel', 'form_post' eller 'fragment'.
| stat | Anbefalede | En værdi, der er inkluderet i anmodningen, der returneres i svaret token. Det kan være en streng med indhold, du ønsker. En tilfældigt entydig værdi bruges typisk til at forhindre på tværs af websteder anmodning forfalskning angreb. Tilstanden bruges også til at kode oplysninger om brugerens tilstand i appen, før der opstod anmodningen om godkendelse, som de er på siden eller politikken udføres. |
| p | Påkrævet | Politikken, der skal udføres. Det er navnet på en politik, der er oprettet i adresselisten B2C. Værdien politik navn skal begynde med "b2c\_1\_". Lær mere om politikker i [Extensible policy framework](active-directory-b2c-reference-policies.md). |
| prompten | Valgfri | Typen af brugerinteraktion, der er påkrævet. Den eneste gyldige værdi på nuværende tidspunkt er 'login', som brugeren skal angive deres legitimationsoplysninger på forespørgslen. Enkeltlogon træder ikke i kraft. |

På dette tidspunkt brugeren bliver bedt om Fuldfør politikkens arbejdsprocessen. Dette kan betyde, at brugeren at indtaste deres brugernavn og adgangskode, logge på med en sociale identitet ved tilmelding til mappen eller et andet nummer trin, afhængigt af hvordan politikken er defineret.

Når brugeren er fuldført politikken, Azure AD vil returnere et svar til din app på den angivne `redirect_uri`, ved hjælp af den metode, der er angivet i den `response_mode` parameter. Svaret bliver præcis det samme for hver af ovennævnte tilfælde, uafhængigt af politikken, der blev udført.

En vellykket svar, der bruger `response_mode=query` ser således ud:

```
GET urn:ietf:wg:oauth:2.0:oob?
code=AwABAAAAvPM1KaPlrEqdFSBzjqfTGBCmLdgfSTLEMPGYuNHSUYBrq...        // the authorization_code, truncated
&state=arbitrary_data_you_can_receive_in_the_response                // the value provided in the request
```

| Parameter | Beskrivelse |
| ----------------------- | ------------------------------- |
| kode | Den authorization_code, der anmodes om en appen. Appen kan bruge koden tilladelse til at anmode om en access_token for et destinationsprogram ressource. Authorization_codes er meget korttidsoplysninger. Typisk udløber de efter omkring 10 minutter. |
| stat | Se den fuldstændige beskrivelse i tabellen ovenfor. Hvis en parameter for tilstand er medtaget i anmodningen, skal den samme værdi vises i svaret. APP'en skal kontrollere, at værdierne for tilstanden i anmodningen og svaret er ens. |

Fejlsvar kan også blive sendt til den `redirect_uri` så appen kan håndtere dem korrekt:

```
GET urn:ietf:wg:oauth:2.0:oob?
error=access_denied
&error_description=The+user+has+cancelled+entering+self-asserted+information
&state=arbitrary_data_you_can_receive_in_the_response
```

| Parameter | Beskrivelse |
| ----------------------- | ------------------------------- |
| fejl | En streng fejlen kode, der kan bruges til at klassificere typerne fejl, der opstår, og kan bruges til at reagere på fejl. |
| error_description | En bestemt fejlmeddelelse, der kan hjælpe en udvikler til at identificere den egentlige årsag til en godkendelsesfejl. |
| stat | Se den fuldstændige beskrivelse i den første tabel i dette afsnit. Hvis en parameter for tilstand er medtaget i anmodningen, skal den samme værdi vises i svaret. APP'en skal kontrollere, at værdierne for tilstanden i anmodningen og svaret er ens. |


## <a name="2-get-a-token"></a>2. få et token
Nu hvor du har hentet en authorization_code, kan du indløse den `code` for et token til den ønskede ressource ved at sende en `POST` anmode om at den `/token` slutpunkt. I Azure AD B2C er den eneste ressource, kan du anmode om et token for webstedet Apps egen back end-API. Konferencen, der bruges til at anmode om et token til dig selv, er at bruge din app klient-ID som omfanget:

```
POST fabrikamb2c.onmicrosoft.com/oauth2/v2.0/token?p=b2c_1_sign_in HTTP/1.1
Host: https://login.microsoftonline.com
Content-Type: application/x-www-form-urlencoded

grant_type=authorization_code&client_id=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6&scope=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6 offline_access&code=AwABAAAAvPM1KaPlrEqdFSBzjqfTGBCmLdgfSTLEMPGYuNHSUYBrq...&redirect_uri=urn:ietf:wg:oauth:2.0:oob

```

| Parameter | Påkrævet? | Beskrivelse |
| ----------------------- | ------------------------------- | --------------------- |
| p | Påkrævet | Den politik, der blev brugt til at få fat på godkendelseskoden. Du kan ikke bruge en anden politik i mødeindkaldelsen. Bemærk, at du føjer denne parameter til *forespørgselsstreng*, ikke i brødteksten INDLÆG. |
| client_id | Påkrævet | Det program-ID, der [Azure-portalen](https://portal.azure.com) , der er tildelt til din app. |
| grant_type | Påkrævet | Typen Giv, som skal være `authorization_code` for godkendelse kode strømmen. |
| omfang | Reccommended | En adskilt af mellemrum liste over områder. En enkelt omfang værdi angiver til Azure AD begge af de tilladelser, der er der anmodes om. Brug af klient-ID, som omfanget angiver, at din app kræver en **få adgang til token** , der kan bruges med dine egne tjeneste eller web API, repræsenteret af det samme klient-ID.  Den `offline_access` omfang angiver, at din app skal **refresh_token** for langlivet adgang til ressourcer.  Du kan også bruge den `openid` omfang for at anmode om en **id_token** fra Azure AD B2C. |
| kode | Påkrævet | Den authorization_code, som du har anskaffet i den første del af strømmen. |
| redirect_uri | Påkrævet | Redirect_uri af programmet, hvor du har modtaget på authorization_code. |

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
| access_token | Den signerede JSON Web Token (JWT)-token, du har skrevet. |
| omfang | De områder, der Tokenet er gyldige for, som kan bruges til cachelagring tokens til senere brug. |
| expires_in | Tidsrum, der Tokenet er gyldig (i sekunder). |
| refresh_token | En OAuth 2.0 refresh_token. Appen kan bruge dette token for at anskaffe flere tokens, når det aktuelle token udløber. Refresh_tokens er længe leve og kan bruges til at bevare adgang til ressourcer for længere perioder. Se [B2C token reference](active-directory-b2c-reference-tokens.md)for at få flere detaljer. |

Fejlsvar ser sådan ud:

```
{
    "error": "access_denied",
    "error_description": "The user revoked access to the app.",
}
```

| Parameter | Beskrivelse |
| ----------------------- | ------------------------------- |
| fejl | En streng fejlen kode, der kan bruges til at klassificere typerne fejl, der opstår, og kan bruges til at reagere på fejl. |
| error_description | En bestemt fejlmeddelelse, der kan hjælpe en udvikler til at identificere den egentlige årsag til en godkendelsesfejl. |

## <a name="3-use-the-token"></a>3. Brug tokenet
Nu hvor du har hentet korrekt en `access_token`, du kan bruge tokenet i anmodninger til din back-end web API'er ved at medtage det i den `Authorization` sidehoved:

```
GET /tasks
Host: https://mytaskwebapi.com
Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...
```

## <a name="4-refresh-the-token"></a>4. opdatere tokenet
Access & Id tokens er korttidsoplysninger. Du skal opdatere dem, når de udløber for at fortsætte med at få adgang til ressourcer. Du kan gøre det ved at sende en anden `POST` anmode om at den `/token` slutpunkt. Denne gang giver den `refresh_token` i stedet for den `code`:

```
POST fabrikamb2c.onmicrosoft.com/oauth2/v2.0/token?p=b2c_1_sign_in HTTP/1.1
Host: https://login.microsoftonline.com
Content-Type: application/x-www-form-urlencoded

grant_type=refresh_token&client_id=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6&scope=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6 offline_access&refresh_token=AwABAAAAvPM1KaPlrEqdFSBzjqfTGBCmLdgfSTLEMPGYuNHSUYBrq...&redirect_uri=urn:ietf:wg:oauth:2.0:oob
```

| Parameter | Påkrævet? | Beskrivelse |
| ----------------------- | ------------------------------- | -------- |
| p | Påkrævet | Den politik, der blev brugt til at få fat på den oprindelige refresh_token. Du kan ikke bruge en anden politik i mødeindkaldelsen. Bemærk, at du føjer denne parameter til *forespørgselsstreng*, ikke i brødteksten INDLÆG. |
| client_id | Anbefalede | Det program-ID, der [Azure-portalen](https://portal.azure.com) , der er tildelt til din app. |
| grant_type | Påkrævet | Typen Giv, som skal være `refresh_token` for denne ben med godkendelse kode forløb. |
| omfang | Anbefalede | En adskilt af mellemrum liste over områder. En enkelt omfang værdi angiver til Azure AD begge af de tilladelser, der er der anmodes om. Brug af klient-ID, som omfanget angiver, at din app kræver en **få adgang til token** , der kan bruges med dine egne tjeneste eller web API, repræsenteret af det samme klient-ID.  Den `offline_access` omfang angiver, at din app skal **refresh_token** for langlivet adgang til ressourcer.  Du kan også bruge den `openid` omfang for at anmode om en **id_token** fra Azure AD B2C. |
| redirect_uri | Valgfri | Redirect_uri af programmet, hvor du har modtaget på authorization_code. |
| refresh_token | Påkrævet | Den oprindelige refresh_token, som du købte sammen med den anden del af strømmen. |

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
| access_token | Den signerede JSON Web Token (JWT) token, du har skrevet. |
| omfang | De områder, der Tokenet er gyldige for, som kan bruges til cachelagring tokens til senere brug. |
| expires_in | Tidsrum, der Tokenet er gyldig (i sekunder). |
| refresh_token | En OAuth 2.0 refresh_token. Appen kan bruge dette token for at anskaffe flere tokens, når det aktuelle token udløber. Refresh_tokens er længe leve og kan bruges til at bevare adgang til ressourcer for længere perioder. Se [B2C token reference](active-directory-b2c-reference-tokens.md)for at få flere detaljer. |

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

## <a name="use-your-own-b2c-directory"></a>Bruge din egen B2C mappe

Hvis du vil prøve disse anmodninger til dig selv, skal du først udføre disse tre trin og derefter erstatte eksempelværdier over med dine egne:

- [Opret en B2C mappe](active-directory-b2c-get-started.md) og brug navnet på mappen i anmodningerne.
- [Opret et program](active-directory-b2c-app-registration.md) for at få et program-ID og en redirect_uri. Du vil medtage en **Oprindelig klient** i din app.
- [Opret din politikker for](active-directory-b2c-reference-policies.md) at få din politik navne.
