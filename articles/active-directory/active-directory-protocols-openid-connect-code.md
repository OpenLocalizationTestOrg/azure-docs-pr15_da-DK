<properties
    pageTitle="Oversigt over Azure AD .NET-protokol | Microsoft Azure"
    description="I denne artikel beskrives, hvordan du bruger HTTP-meddelelser til at give adgang til webprogrammer og web API'er i din lejer, ved hjælp af Azure Active Directory og OpenID forbindelse."
    services="active-directory"
    documentationCenter=".net"
    authors="priyamohanram"
    manager="mbaldwin"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/03/2016"
    ms.author="priyamo"/>


# <a name="authorize-access-to-web-applications-using-openid-connect-and-azure-active-directory"></a>Give adgang til webprogrammer ved hjælp af OpenID forbinde og Azure Active Directory

[OpenID Connect](http://openid.net/specs/openid-connect-core-1_0.html) er et enkelt identitet lag, der er bygget oven på OAuth 2.0-protokollen. OAuth 2.0 definerer funktioner til at hente og bruge **access tokens** til at få adgang til beskyttede ressourcer, men de definere ikke standard metoder til at informere identitet. OpenID forbinde implementerer godkendelse som en udvidelse af godkendelsesprocessen OAuth 2.0 samt oplysninger om slutbrugerens i form af en `id_token` , der bekræfter identitet for brugeren, samt indeholder grundlæggende profiloplysninger om brugeren.

OpenID Connect er vores anbefaling, hvis du bygger et webprogram, der er hostet på en server og adgang til via en browser.

## <a name="authentication-flow-using-openid-connect"></a>Godkendelse flow med OpenID Opret forbindelse

Den mest grundlæggende logon arbejdsgang indeholder følgende trin – hver af dem er beskrevet i detaljeret nedenfor.

![OpenId forbinde godkendelse Flow](media/active-directory-protocols-openid-connect-code/active-directory-oauth-code-flow-web-app.png)


## <a name="send-the-sign-in-request"></a>Send anmodning-logon

Når dit webprogram skal godkende brugeren, skal den dirigerer brugeren til den `/authorize` slutpunkt. Denne anmodning ligner den første del af den [OAuth 2.0 godkendelse kode dataflow](active-directory-protocols-oauth-code.md), med nogle vigtige forskelle:

- Anmodningen skal indeholde omfanget `openid` i den `scope` parameter.
- Den `response_type` parameter skal indeholde `id_token`.
- Anmodningen skal indeholde den `nonce` parameter.

Så en anmodning om eksempel ville se således ud:

```
// Line breaks for legibility only

GET https://login.microsoftonline.com/{tenant}/oauth2/authorize?
client_id=6731de76-14a6-49ae-97bc-6eba6914391e
&response_type=id_token
&redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F
&response_mode=form_post
&scope=openid
&state=12345
&nonce=7362CAEA-9CA5-4B43-9BA3-34D7C303EBA7
```

| Parameter | | Beskrivelse |
| ----------------------- | ------------------------------- | --------------- |
| lejer | påkrævet | Den `{tenant}` værdi i sti for din anmodning kan bruges til at styre, hvem der ikke kan logge på programmet.  De tilladte værdier er lejer id'er, f.eks. `8eaef023-2b34-4da1-9baa-8bc8c9d6a490` eller `contoso.onmicrosoft.com` eller `common` for lejer uafhængige tokens |
| client_id | påkrævet | Det program-Id, der tildeles til din app, når du har registreret det med Azure AD. Du kan finde det i portalen Azure klassisk. Klik på **Active Directory**, skal du klikke på mappen, klik på programmet, og klik på **Konfigurer** |
| response_type | påkrævet | Må indeholde `id_token` til OpenID forbinde logon.  Den kan også omfatte andre response_types såsom `code`. |
| omfang | påkrævet | En adskilt af mellemrum liste over områder.  Det skal OpenID oprette forbindelse, skal du medtage omfanget `openid`, som oversættes til tilladelsen "Logge dig på i" i samtykke brugergrænseflade.  Du kan også indeholde andre områder i denne anmodning til anmodning om tilladelse. |
| Nonce | påkrævet | En værdi, der er inkluderet i anmodningen, genereres af den app, der skal medtages i den resulterende `id_token` som et krav.  Appen kan derefter kontrollere denne værdi for at reducere token genafspilningsangreb.  Værdien er typisk en randomiseret, entydigt streng eller GUID, der kan bruges til at identificere origin for din anmodning.  |
| redirect_uri | anbefalede | Redirect_uri af din app, hvor godkendelse svar kan sendes og modtages af din app.  Det skal svare nøjagtigt til en af de redirect_uris, der er registreret i portalen, bortset fra det skal være URL-kodes. |
| response_mode | anbefalede | Angiver den metode, der skal bruges til at sende den resulterende authorization_code tilbage til din app.  Understøttede værdier er `form_post` for *HTTP formular stil* eller `fragment` for *URL-adressen fragment*.  For webprogrammer det anbefales at bruge `response_mode=form_post` at sikre, at den mest sikre overførsel af tokens til dit program.  
| stat | anbefalede | En værdi, der er inkluderet i anmodningen, der returneres i svaret token.  Det kan være en streng med det indhold, du ønsker.  En tilfældigt entydig værdi bruges typisk til [at forhindre på tværs af websteder anmodning forfalskning angreb](http://tools.ietf.org/html/rfc6749#section-10.12).  Tilstanden bruges også til at kode oplysninger om brugerens tilstand i appen, før der opstod anmodningen om godkendelse, som siden eller visningen, det var på. |
| prompten | valgfri | Angiver typen brugerinteraktion, der er påkrævet.  På nuværende tidspunkt kun gyldige værdier er 'login', 'Ingen,' og 'samtykke'.  `prompt=login`Tvinger brugeren til at indtaste legitimationsoplysningerne på anmodningen, hvilket eliminerer enkelt Log på.  `prompt=none`er modsat - det sikrer, at brugeren ikke vises med en hvilken som helst interaktiv prompt ene eller den anden.  Hvis anmodningen ikke kan fuldføres uovervåget via single-sign-on, vil slutpunktet returnere en fejl.  `prompt=consent`dialogboksen OAuth samtykke, udløses, når brugeren logger på, beder brugeren om at tildele tilladelser til appen. |
| login_hint | valgfri | Kan bruges til at udfylde udfyldt feltet brugernavn/e-mail-adresse i fremgangsmåden logge på siden for brugeren, hvis du kender deres brugernavn forvejen.  Ofte apps anvender denne parameter under ny godkendelse, har du allerede hentet brugernavnet fra en tidligere Log på ved hjælp af den `preferred_username` gøre krav. |


På dette tidspunkt brugeren bliver bedt om angive deres legitimationsoplysninger og fuldføre godkendelsen.

### <a name="sample-response"></a>Eksempel svar

Et eksempel svar, kan når brugeren har godkendt, se ud som dette:

```
POST /myapp/ HTTP/1.1
Host: localhost
Content-Type: application/x-www-form-urlencoded

id_token=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik1uQ19WWmNB...&state=12345
```

| Parameter | Beskrivelse |
| ----------------------- | ------------------------------- |
| id_token | Den `id_token` , der anmodes om en appen. Du kan bruge den `id_token` til at kontrollere brugerens identitet og starte en session med brugeren.  |
| stat | En værdi, der er inkluderet i anmodningen, der returneres i svaret token. En tilfældigt entydig værdi bruges typisk til [at forhindre på tværs af websteder anmodning forfalskning angreb](http://tools.ietf.org/html/rfc6749#section-10.12).  Tilstanden bruges også til at kode oplysninger om brugerens tilstand i appen, før der opstod anmodningen om godkendelse, som siden eller visningen, det var på. |

### <a name="error-response"></a>Fejlsvar
Fejlsvar kan også blive sendt til den `redirect_uri` så appen kan håndtere dem korrekt:

```
POST /myapp/ HTTP/1.1
Host: localhost
Content-Type: application/x-www-form-urlencoded

error=access_denied&error_description=the+user+canceled+the+authentication
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

## <a name="validate-the-idtoken"></a>Validere id_token

Kun modtage en `id_token` ikke er tilstrækkelige til at godkende brugeren; Du skal validere signaturen og kontrollere krav i den `id_token` per krav til din app. Azure AD slutpunktet bruger JSON Web Tokens (JWTs) og kryptering med offentlig nøgle til at logge tokens og bekræfte, at de er gyldige.

Du kan vælge at validere det `id_token` i klienten kode, men almindelig praksis er at sende den `id_token` til en back end-serveren og udføre validering der. Når du har valideret signaturen for den `id_token`, der er et par krav du bliver bedt om at bekræfte.

Du kan også til at validere ekstra krav afhængigt af scenariet. Nogle almindelige valideringer omfatter:

- Kontrollere, at brugeren/organisation har tilmeldt sig til appen.
- Kontrollere, at brugeren har stort godkendelse/rettigheder
- Kontrollere, at en bestemt styrken af godkendelse er opstået som Multi-Factor authentication.

Når du har helt godkendt den `id_token`, du kan starte en session med brugeren og bruge krav i den `id_token` vil have oplysninger om brugeren i din app. Disse oplysninger kan bruges til visning, poster, tilladelser, osv. Læs [understøttes Token og Kravbrugeren typer](active-directory-token-and-claims.md)kan finde flere oplysninger om token filtyper og krav.

## <a name="send-a-sign-out-request"></a>Sende en log af anmodning

Det er ikke tilstrækkelige at fjerne din app cookies eller på anden måde end sessionen med brugeren, når du vil logge af app'en brugeren.  Du skal også omdirigere brugeren på `end_session_endpoint` for Log af.  Hvis du undlader at gøre det, vil brugeren kunne igen godkende din app uden at angive deres legitimationsoplysninger igen, fordi de har en gyldig enkelt sign-on – session med Azure AD-slutpunkt.

Du kan blot omdirigere brugeren på `end_session_endpoint` er angivet i dokumentets OpenID forbinde metadata:

```
GET https://login.microsoftonline.com/common/oauth2/logout?
post_logout_redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F

```

| Parameter | | Beskrivelse |
| ----------------------- | ------------------------------- | ------------ |
| post_logout_redirect_uri | anbefalede | URL-adressen, brugeren skal omdirigeres til efter vellykket log.  Hvis ikke findes, vises brugeren en generel meddelelse.  |

## <a name="token-acquisition"></a>Token Acquisition

Mange Onlines skal ikke kun logge brugeren i, men også få adgang til en webtjeneste på vegne af den bruger, der bruger OAuth. Dette scenarie kombinerer OpenID oprette forbindelse til brugergodkendelse under samtidigt hentning en `authorization_code` , der kan bruges til at få `access_tokens` ved hjælp af OAuth godkendelse kode dataflow.

## <a name="get-access-tokens"></a>Få Access Tokens

Du kan opnå adgangstokens, skal du redigere anmodningen logon fra ovenfor:

```
// Line breaks for legibility only

GET https://login.microsoftonline.com/{tenant}/oauth2/authorize?
client_id=6731de76-14a6-49ae-97bc-6eba6914391e      // Your registered Application Id
&response_type=id_token+code
&redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F       // Your registered Redirect Uri, url encoded
&response_mode=form_post                              // form_post', or 'fragment'
&scope=openid
&resource=https%3A%2F%2Fservice.contoso.com%2F                                   
&state=12345                                         // Any value, provided by your app
&nonce=678910                                        // Any value, provided by your app
```

Ved at medtage tilladelse områder i anmodningen og bruge `response_type=code+id_token`, `authorize` slutpunkt sikrer, at brugeren har accepteret til de tilladelser, der er angivet i den `scope` forespørge parameter og returnere din app kode en tilladelse til at udveksle for et adgangstoken.

### <a name="successful-response"></a>Vellykket svar

En vellykket svar ved hjælp af `response_mode=form_post` ser således ud:

```
POST /myapp/ HTTP/1.1
Host: localhost
Content-Type: application/x-www-form-urlencoded

id_token=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik1uQ19WWmNB...&code=AwABAAAAvPM1KaPlrEqdFSBzjqfTGBCmLdgfSTLEMPGYuNHSUYBrq...&state=12345
```

| Parameter | Beskrivelse |
| ----------------------- | ------------------------------- |
| id_token | Den `id_token` , der anmodes om en appen. Du kan bruge den `id_token` til at kontrollere brugerens identitet og starte en session med brugeren. |
| kode | Den authorization_code, der anmodes om en appen. Appen kan bruge koden tilladelse til at anmode om en adgangstoken til ressourcen mål. Authorization_codes er meget korttidsoplysninger, typisk de udløber efter omkring 10 minutter. |
| stat | Hvis en parameter for tilstand er medtaget i anmodningen, skal den samme værdi vises i svaret. APP'en skal kontrollere, at værdierne for tilstanden i anmodningen og svaret er ens. |

### <a name="error-response"></a>Fejlsvar

Fejlsvar kan også blive sendt til den `redirect_uri` så appen kan håndtere dem korrekt:

```
POST /myapp/ HTTP/1.1
Host: localhost
Content-Type: application/x-www-form-urlencoded

error=access_denied&error_description=the+user+canceled+the+authentication
```

| Parameter | Beskrivelse |
| ----------------------- | ------------------------------- |
| fejl | En streng fejlen kode, der kan bruges til at klassificere typer fejl, der opstår, og kan bruges til at reagere på fejl. |
| error_description | En bestemt fejlmeddelelse, der kan hjælpe en udvikler identificere den egentlige årsag til en godkendelsesfejl.  |

Du kan finde en beskrivelse af de mulige fejlkoder og deres anbefalede klienthandling [fejlkoder for godkendelse slutpunkt fejl](#error-codes-for-authorization-endpoint-errors).

Når du har fået tilladelse `code` og en `id_token`, du kan logge brugeren på og få access tokens på deres vegne.  Hvis du vil logge brugeren i, skal du validerer den `id_token` nøjagtigt, som er beskrevet ovenfor. For at få adgangstokens, kan du følge trinnene beskrevet i vores [OAuth-protokollen dokumentation](active-directory-protocols-oauth-code.md#Use-the-Authorization-Code-to-Request-an-Access-Token).
