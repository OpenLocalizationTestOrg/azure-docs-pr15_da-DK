<properties
    pageTitle="Oversigt over Azure AD .NET-protokol | Microsoft Azure"
    description="I denne artikel beskrives, hvordan du bruger HTTP-meddelelser til at give adgang til webprogrammer og web API'er i din lejer, ved hjælp af Azure Active Directory og OAuth 2.0."
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


# <a name="authorize-access-to-web-applications-using-oauth-20-and-azure-active-directory"></a>Give adgang til webprogrammer ved hjælp af OAuth 2.0 og Azure Active Directory

Azure Active Directory (Azure AD) bruger OAuth 2.0 til gør det muligt at give adgang til webprogrammer og web API'er i din Azure AD-lejer. Denne vejledning er uafhængig sprog, og beskriver, hvordan du kan sende og modtage HTTP-meddelelser uden at bruge en af vores open source-biblioteker.

OAuth 2.0 godkendelse kode strømmen er beskrevet i [punkt 4.1 i OAuth 2.0-specifikationen](https://tools.ietf.org/html/rfc6749#section-4.1) . Det bruges til at udføre godkendelse og autorisation i fleste af app typer, herunder Onlines og indbygget installeret apps.

[AZURE.INCLUDE [active-directory-protocols-getting-started](../../includes/active-directory-protocols-getting-started.md)]


## <a name="oauth-20-authorization-flow"></a>OAuth 2.0 godkendelse flow

På et højt niveau ser hele godkendelse strømmen for et program lidt sådan ud:

![OAuth Auth kode Flow](media/active-directory-protocols-oauth-code/active-directory-oauth-code-flow-native-app.png)


## <a name="request-an-authorization-code"></a>Anmode om en tilladelse kode

Godkendelse kode strømmen begynder med klienten dirigerer brugeren til den `/authorize` slutpunkt. I denne anmodning angiver klienten de tilladelser, der skal til at hente fra brugeren. Du kan åbne OAuth 2.0 slutpunkterne programmets side klassisk Azure-portalen, i knappen **Vis slutpunkter** i den nederste skuffe.

```
// Line breaks for legibility only

https://login.microsoftonline.com/{tenant}/oauth2/authorize?
client_id=6731de76-14a6-49ae-97bc-6eba6914391e
&response_type=code
&redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F
&response_mode=query
&resource=https%3A%2F%2Fservice.contoso.com%2F
&state=12345
```

| Parameter | | Beskrivelse |
| ----------------------- | ------------------------------- | --------------- |
| lejer | påkrævet | Den `{tenant}` værdi i sti for din anmodning kan bruges til at styre, hvem der ikke kan logge på programmet.  De tilladte værdier er lejer id'er, f.eks. `8eaef023-2b34-4da1-9baa-8bc8c9d6a490` eller `contoso.onmicrosoft.com` eller `common` for lejer uafhængige tokens |
| client_id | påkrævet | Det program-Id, der tildeles til din app, når du har registreret det med Azure AD. Du kan finde det i portalen Azure administration. Klik på **Active Directory**, skal du klikke på mappen, klik på programmet, og klik på **Konfigurer** |
| response_type | påkrævet | Må indeholde `code` for godkendelse kode strømmen. |
| redirect_uri | anbefalede | Redirect_uri af din app, hvor godkendelse svar kan sendes og modtages af din app.  Det skal svare nøjagtigt til en af de redirect_uris, der er registreret i portalen, bortset fra det skal være URL-kodes.  Til oprindelig og mobile-apps, skal du bruge standardværdien for `urn:ietf:wg:oauth:2.0:oob`. |
| response_mode | anbefalede | Angiver den metode, der skal bruges til at sende den resulterende token tilbage til din app.  Kan være `query` eller `form_post`.  |
| stat | anbefalede | En værdi, der er inkluderet i anmodningen, der returneres i svaret token. En tilfældigt entydig værdi bruges typisk til [at forhindre på tværs af websteder anmodning forfalskning angreb](http://tools.ietf.org/html/rfc6749#section-10.12).  Tilstanden bruges også til at kode oplysninger om brugerens tilstand i appen, før der opstod anmodningen om godkendelse, som siden eller visningen, det var på. |
| ressource | valgfri | App-ID URI af web API (sikret ressource). For at finde web API, App-ID URI i portalen Azure Management, skal du klikke på **Active Directory**, skal du klikke på mappen, klik på programmet og derefter klikke på **Konfigurer**. |
| prompten | valgfri |  Angiv typen af brugerinput, der er påkrævet.<p> Gyldige værdier er: <p> *login*: brugeren bliver bedt om at godkende igen. <p> *samtykke*: brugeren samtykke har fået tildelt, men skal opdateres. Brugeren skal bliver bedt om at samtykke. <p> *admin_consent*: en administrator skal blive bedt om at samtykke på vegne af alle brugerne i organisationen |
| login_hint | valgfri | Kan bruges til at udfylde udfyldt feltet brugernavn/e-mail-adresse i fremgangsmåden logge på siden for brugeren, hvis du kender deres brugernavn forvejen.  Ofte apps anvender denne parameter under fornyet godkendelse, har du allerede hentet brugernavnet fra en tidligere Log på ved hjælp af den `preferred_username` gøre krav. |
| domain_hint | valgfri | Indeholder et tip om lejer eller domæne, brugeren skal bruge til at logge på. Værdien af domain_hint er et registreret domæne for lejeren. Hvis lejeren er knyttet til en lokal adresseliste, omdirigerer AAD til den angivne lejer sammenslutningsserver. |

> [AZURE.NOTE] Hvis brugeren er en del af en organisation, kan en administrator for organisationen samtykke eller afvise på brugerens vegne eller give brugeren samtykke. Brugeren er givet mulighed for at samtykke kun, når administratoren tillader, at den.

På dette tidspunkt vil blive bedt om brugeren til at angive deres legitimationsoplysninger og samtykke til de tilladelser, der er angivet i den `scope` forespørgsel parameter. Når brugeren godkender og giver samtykke, Azure AD sender svar til din app på den `redirect_uri` adresse i din anmodning.

### <a name="successful-response"></a>Vellykket svar

En vellykket svar kunne se sådan ud:

```
GET  HTTP/1.1 302 Found
Location: http://localhost/myapp/?code= AwABAAAAvPM1KaPlrEqdFSBzjqfTGBCmLdgfSTLEMPGYuNHSUYBrqqf_ZT_p5uEAEJJ_nZ3UmphWygRNy2C3jJ239gV_DBnZ2syeg95Ki-374WHUP-i3yIhv5i-7KU2CEoPXwURQp6IVYMw-DjAOzn7C3JCu5wpngXmbZKtJdWmiBzHpcO2aICJPu1KvJrDLDP20chJBXzVYJtkfjviLNNW7l7Y3ydcHDsBRKZc3GuMQanmcghXPyoDg41g8XbwPudVh7uCmUponBQpIhbuffFP_tbV8SNzsPoFz9CLpBCZagJVXeqWoYMPe2dSsPiLO9Alf_YIe5zpi-zY4C3aLw5g9at35eZTfNd0gBRpR5ojkMIcZZ6IgAA&session_state=7B29111D-C220-4263-99AB-6F6E135D75EF&state=D79E5777-702E-4260-9A62-37F75FF22CCE
```

| Parameter | Beskrivelse |
| -----------------------| --------------- |
| admin_consent | Værdien er sand, hvis en administrator accepteret at samtykke anmodning om meddelelse om.|
| kode | Koden godkendelse, programmet har anmodet om. Programmet kan bruge koden tilladelse til at anmode om en adgangstoken til ressourcen mål. |
| session_state | En entydig værdi, der identificerer den aktuelle brugersession. Denne værdi er et GUID, men skal behandles som en uigennemsigtig værdi, der overføres uden undersøgelse. |
| stat | Hvis en parameter for tilstand er medtaget i anmodningen, skal den samme værdi vises i svaret. Er det en god ide for programmet for at kontrollere, at værdierne for tilstanden i anmodningen og svaret er ens inden du bruger svaret. Dette hjælper med at registrere [på tværs af websteder anmode om forfalskning (CSRF) angreb](https://tools.ietf.org/html/rfc6749#section-10.12) mod klienten.  

### <a name="error-response"></a>Fejlsvar

Fejlsvar kan også blive sendt til den `redirect_uri` så programmet kan håndtere dem korrekt.

```
GET http://localhost:12345/?
error=access_denied
&error_description=the+user+canceled+the+authentication
```

| Parameter | Beskrivelse |
|-----------|-------------|
| fejl | En kode fejlværdi defineret i sektionen 5.2 af [OAuth 2.0 godkendelse Framework](http://tools.ietf.org/html/rfc6749). Den næste tabel beskrives de fejlkoder, Azure AD returnerer. |
| error_description | En mere detaljeret beskrivelse af fejlen. Denne meddelelse er ikke beregnet til at være slutbrugerlicensaftale fulde. |
| stat | Værdien tilstand er en tilfældigt ikke genbruges værdi, der er sendt i anmodningen og returneres i svaret for at forhindre på tværs af websteder anmodning forfalskning (CSRF) angreb. |

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

## <a name="use-the-authorization-code-to-request-an-access-token"></a>Brug koden tilladelse til at anmode om et adgangstoken

Nu, hvor du har hentet en godkendelseskode for og har fået tilladelse af brugeren, kan du indløse koden for et adgangstoken til den ønskede ressource, ved at sende en anmodning om INDLÆG til den `/token` slutpunkt:

```
// Line breaks for legibility only

POST /{tenant}/oauth2/token HTTP/1.1
Host: https://login.microsoftonline.com
Content-Type: application/x-www-form-urlencoded
grant_type=authorization_code
&client_id=2d4d11a2-f814-46a7-890a-274a72a7309e
&code=AwABAAAAvPM1KaPlrEqdFSBzjqfTGBCmLdgfSTLEMPGYuNHSUYBrqqf_ZT_p5uEAEJJ_nZ3UmphWygRNy2C3jJ239gV_DBnZ2syeg95Ki-374WHUP-i3yIhv5i-7KU2CEoPXwURQp6IVYMw-DjAOzn7C3JCu5wpngXmbZKtJdWmiBzHpcO2aICJPu1KvJrDLDP20chJBXzVYJtkfjviLNNW7l7Y3ydcHDsBRKZc3GuMQanmcghXPyoDg41g8XbwPudVh7uCmUponBQpIhbuffFP_tbV8SNzsPoFz9CLpBCZagJVXeqWoYMPe2dSsPiLO9Alf_YIe5zpi-zY4C3aLw5g9at35eZTfNd0gBRpR5ojkMIcZZ6IgAA
&redirect_uri=https%3A%2F%2Flocalhost%2Fmyapp%2F
&resource=https%3A%2F%2Fservice.contoso.com%2F
&client_secret=p@ssw0rd

//NOTE: client_secret only required for web apps
```

| Parameter | | Beskrivelse |
| ----------------------- | ------------------------------- | --------------------- |
| lejer | påkrævet |  Den `{tenant}` værdi i sti for din anmodning kan bruges til at styre, hvem der ikke kan logge på programmet.  De tilladte værdier er lejer id'er, f.eks. `8eaef023-2b34-4da1-9baa-8bc8c9d6a490` eller `contoso.onmicrosoft.com` eller `common` for lejer uafhængige tokens |
| client_id | påkrævet | Det program-Id, der tildeles til din app, når du har registreret det med Azure AD. Du kan finde det i portalen Azure klassisk. Klik på **Active Directory**, skal du klikke på mappen, klik på programmet, og klik på **Konfigurer** |
| grant_type | påkrævet | Skal være `authorization_code` for godkendelse kode strømmen. |
| kode | påkrævet | Den `authorization_code` , som du har anskaffet i forrige afsnit   |
| redirect_uri | påkrævet | Den samme `redirect_uri` værdi, der blev brugt til at få fat på `authorization_code`. |
| client_secret | kræves til online | Hemmeligheden program, du oprettede i portalen app registrering for din app.  Det skal ikke bruges i en oprindelig app, fordi client_secrets pålideligt ikke kan gemmes på enheder.  Det er påkrævet til online- og web API'er, som har mulighed for at gemme den `client_secret` sikkert på serversiden. |
| ressource | påkrævet Hvis angivet i kode anmodningen om godkendelse, ellers valgfrit | App-ID URI af web API (sikret ressource).
Find den App-ID URI i portalen Azure Management, klikke på **Active Directory**, klikke på mappen, klik på programmet, og klik derefter på **Konfigurer**.

### <a name="successful-response"></a>Vellykket svar

Azure AD returnerer et adgangstoken på en vellykket svar. For at minimere netværk opkald fra klientprogrammet og deres tilknyttede ventetid, cache klientprogrammet access tokens token levetid, der er angivet i OAuth 2.0 svaret. For at bestemme token levetiden skal du bruge enten den `expires_in` eller `expires_on` parameterværdier.

Hvis en web API ressource returnerer en `invalid_token` fejlkode, dette kan skyldes, at ressourcen har registreret, Tokenet er udløbet. Hvis klienten og ressource ur tiderne forskellige (også kaldet et "tidsforskel"), overveje ressourcen tokenet udløbet før tokenet slettes fra klientens cache. Hvis dette forekommer, kan du fjerne markeringen token fra cachen, selvom det er stadig er omfattet af dens beregnede levetid.

En vellykket svar kunne se sådan ud:

```
{
  "access_token": " eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1THdqcHdBSk9NOW4tQSJ9.eyJhdWQiOiJodHRwczovL3NlcnZpY2UuY29udG9zby5jb20vIiwiaXNzIjoiaHR0cHM6Ly9zdHMud2luZG93cy5uZXQvN2ZlODE0NDctZGE1Ny00Mzg1LWJlY2ItNmRlNTdmMjE0NzdlLyIsImlhdCI6MTM4ODQ0MDg2MywibmJmIjoxMzg4NDQwODYzLCJleHAiOjEzODg0NDQ3NjMsInZlciI6IjEuMCIsInRpZCI6IjdmZTgxNDQ3LWRhNTctNDM4NS1iZWNiLTZkZTU3ZjIxNDc3ZSIsIm9pZCI6IjY4Mzg5YWUyLTYyZmEtNGIxOC05MWZlLTUzZGQxMDlkNzRmNSIsInVwbiI6ImZyYW5rbUBjb250b3NvLmNvbSIsInVuaXF1ZV9uYW1lIjoiZnJhbmttQGNvbnRvc28uY29tIiwic3ViIjoiZGVOcUlqOUlPRTlQV0pXYkhzZnRYdDJFYWJQVmwwQ2o4UUFtZWZSTFY5OCIsImZhbWlseV9uYW1lIjoiTWlsbGVyIiwiZ2l2ZW5fbmFtZSI6IkZyYW5rIiwiYXBwaWQiOiIyZDRkMTFhMi1mODE0LTQ2YTctODkwYS0yNzRhNzJhNzMwOWUiLCJhcHBpZGFjciI6IjAiLCJzY3AiOiJ1c2VyX2ltcGVyc29uYXRpb24iLCJhY3IiOiIxIn0.JZw8jC0gptZxVC-7l5sFkdnJgP3_tRjeQEPgUn28XctVe3QqmheLZw7QVZDPCyGycDWBaqy7FLpSekET_BftDkewRhyHk9FW_KeEz0ch2c3i08NGNDbr6XYGVayNuSesYk5Aw_p3ICRlUV1bqEwk-Jkzs9EEkQg4hbefqJS6yS1HoV_2EsEhpd_wCQpxK89WPs3hLYZETRJtG5kvCCEOvSHXmDE6eTHGTnEgsIk--UlPe275Dvou4gEAwLofhLDQbMSjnlV5VLsjimNBVcSRFShoxmQwBJR_b2011Y5IuD6St5zPnzruBbZYkGNurQK63TJPWmRd3mbJsGM0mf3CUQ",
  "token_type": "Bearer",
  "expires_in": "3600",
  "expires_on": "1388444763",
  "resource": "https://service.contoso.com/",
  "refresh_token": "AwABAAAAvPM1KaPlrEqdFSBzjqfTGAMxZGUTdM0t4B4rTfgV29ghDOHRc2B-C_hHeJaJICqjZ3mY2b_YNqmf9SoAylD1PycGCB90xzZeEDg6oBzOIPfYsbDWNf621pKo2Q3GGTHYlmNfwoc-OlrxK69hkha2CF12azM_NYhgO668yfcUl4VBbiSHZyd1NVZG5QTIOcbObu3qnLutbpadZGAxqjIbMkQ2bQS09fTrjMBtDE3D6kSMIodpCecoANon9b0LATkpitimVCrl-NyfN3oyG4ZCWu18M9-vEou4Sq-1oMDzExgAf61noxzkNiaTecM-Ve5cq6wHqYQjfV9DOz4lbceuYCAA",
  "scope": "https%3A%2F%2Fgraph.microsoft.com%2Fmail.read",
"id_token": " eyJ0eXAiOiJKV1QiLCJhbGciOiJub25lIn0.eyJhdWQiOiIyZDRkMTFhMi1mODE0LTQ2YTctODkwYS0yNzRhNzJhNzMwOWUiLCJpc3MiOiJodHRwczovL3N0cy53aW5kb3dzLm5ldC83ZmU4MTQ0Ny1kYTU3LTQzODUtYmVjYi02ZGU1N2YyMTQ3N2UvIiwiaWF0IjoxMzg4NDQwODYzLCJuYmYiOjEzODg0NDA4NjMsImV4cCI6MTM4ODQ0NDc2MywidmVyIjoiMS4wIiwidGlkIjoiN2ZlODE0NDctZGE1Ny00Mzg1LWJlY2ItNmRlNTdmMjE0NzdlIiwib2lkIjoiNjgzODlhZTItNjJmYS00YjE4LTkxZmUtNTNkZDEwOWQ3NGY1IiwidXBuIjoiZnJhbmttQGNvbnRvc28uY29tIiwidW5pcXVlX25hbWUiOiJmcmFua21AY29udG9zby5jb20iLCJzdWIiOiJKV3ZZZENXUGhobHBTMVpzZjd5WVV4U2hVd3RVbTV5elBtd18talgzZkhZIiwiZmFtaWx5X25hbWUiOiJNaWxsZXIiLCJnaXZlbl9uYW1lIjoiRnJhbmsifQ.”
}

```

| Parameter | Beskrivelse |
| ----------------------- | ------------------------------- |
| access_token | Nødvendige adgangstoken. Appen kan bruge dette token til at godkende sikret ressourcen, som en web API. |
| token_type | Angiver værdien token type. Den eneste type, der understøtter Azure AD er bæreren. Finde flere oplysninger om bæreren tokens, [OAuth2.0 godkendelse Framework: bæreren Token brugen (RFC 6750)](http://www.rfc-editor.org/rfc/rfc6750.txt)  |
| expires_in | Hvor længe adgangstoken er gyldig (i sekunder). |
| expires_on | Det tidspunkt, hvor adgangstoken udløber. Datoen, der repræsenteres som antallet sekunder fra 1970-01-01T0:0:0Z UTC indtil udløbsdatoen. Denne værdi bruges til at bestemme levetiden for cachelagrede tokens. |
| ressource | App-ID URI af web API (sikret ressource).|
| omfang | Repræsentation tilladelser til klientprogrammet. Standardtilladelsen til er `user_impersonation`. Ejeren af den beskyttede ressource kan registrere flere værdier i Azure AD.|
| refresh_token |  En OAuth 2.0 Opdater token. Appen kan bruge dette token for at anskaffe flere access tokens, når den aktuelle adgangstoken udløber.  Opdatere tokens er langlivet, og kan bruges til at bevare adgang til ressourcer for længere perioder. |
| id_token | En usigneret JSON Web Token (JWT). App kan base64Url afkode segmenter af dette token anmode om oplysninger om den bruger, der er logget på. Appen kan cache værdierne og få dem vist, men den må ikke stole på dem til godkendelse og sikkerhedsgrænser. |

### <a name="jwt-token-claims"></a>JWT Token krav
JWT token i værdien af den `id_token` parameter kan afkodes i de følgende krav:

```
{
 "typ": "JWT",
 "alg": "none"
}.
{
 "aud": "2d4d11a2-f814-46a7-890a-274a72a7309e",
 "iss": "https://sts.windows.net/7fe81447-da57-4385-becb-6de57f21477e/",
 "iat": 1388440863,
 "nbf": 1388440863,
 "exp": 1388444763,
 "ver": "1.0",
 "tid": "7fe81447-da57-4385-becb-6de57f21477e",
 "oid": "68389ae2-62fa-4b18-91fe-53dd109d74f5",
 "upn": "frank@contoso.com",
 "unique_name": "frank@contoso.com",
 "sub": "JWvYdCWPhhlpS1Zsf7yYUxShUwtUm5yzPmw_-jX3fHY",
 "family_name": "Miller",
 "given_name": "Frank"
}.
```

Den `id_token` parameter indeholder følgende krav. Du kan finde flere oplysninger om JSON web tokens, [JWT IETF kladde specifikation](http://go.microsoft.com/fwlink/?LinkId=392344). Du kan finde flere oplysninger om token typer og krav Læs [understøttes Token og Kræv typer](active-directory-token-and-claims.md)

| Gøre krav på type | Beskrivelse |
|------------|-------------|
| AUD | Publikum på mellem tokenet. Når Tokenet er udstedt til en-klientprogrammet, målgruppen er den `client_id` af klienten.
| EXP | Udløbsdatoen. Det tidspunkt, hvor tokenet udløber. Den aktuelle dato og klokkeslæt for tokenet skal gælde, skal du være mindre end eller lig med den `exp` værdi. Tid, der repræsenteres som antallet sekunder fra 1 januar 1970 (1970-01-01T0:0:0Z) UTC indtil tokenet, er udstedt. |
| family_name | Brugerens efternavn eller efternavn. Programmet kan vise denne værdi. |
| given_name | Brugerens fornavn. Programmet kan vise denne værdi. |
| IAT | Udstedt samtidig. Det tidspunkt, hvor JWT blev udstedt. Tid, der repræsenteres som antallet sekunder fra 1 januar 1970 (1970-01-01T0:0:0Z) UTC indtil tokenet, er udstedt. |
| ISS | Identificerer token udsteder |
| NBF | Ikke før tid. Den tid, når tokenet træder i kraft. Tokenet skal gælde, være den aktuelle dato og klokkeslæt større end eller lig med værdien Nbf. Tid, der repræsenteres som antallet sekunder fra 1 januar 1970 (1970-01-01T0:0:0Z) UTC indtil tokenet, er udstedt. |
| objekt-id | Objekt-id (ID) af brugerobjektet i Azure AD. |
| Sub | Token emne id. Dette er en fast og fast id for den bruger, der beskriver tokenet. Bruge denne værdi i cachelagring logik. |
| tid | Lejer id (ID) for den udstedt tokenet Azure AD-lejer. |
| unique_name | Et entydigt id for, der kan vises for brugeren. Det er normalt brugerens hovednavn (UPN). |
| UPN | Brugerens hovednavn på brugeren. |
| ver | Version. Versionen af JWT tokenet, typisk 1.0. |

### <a name="error-response"></a>Fejlsvar

Token udstedelse slutpunkt fejl er HTTP fejlkoder, fordi klienten kalder token udstedelse slutpunktet direkte. Ud over statuskode HTTP returnerer Azure AD token udstedelse slutpunktet også et JSON-dokument med objekter, der beskriver fejlen.

Et eksempel fejlsvar kunne se sådan ud:

```
{
  "error": "invalid_grant",
  "error_description": "AADSTS70002: Error validating credentials. AADSTS70008: The provided authorization code or refresh token is expired. Send a new interactive authorization request for this user and resource.\r\nTrace ID: 3939d04c-d7ba-42bf-9cb7-1e5854cdce9e\r\nCorrelation ID: a8125194-2dc8-4078-90ba-7b6592a7f231\r\nTimestamp: 2016-04-11 18:00:12Z",
  "error_codes": [
    70002,
    70008
  ],
  "timestamp": "2016-04-11 18:00:12Z",
  "trace_id": "3939d04c-d7ba-42bf-9cb7-1e5854cdce9e",
  "correlation_id": "a8125194-2dc8-4078-90ba-7b6592a7f231"
}
```
| Parameter | Beskrivelse |
| ----------------------- | ------------------------------- |
| fejl | En streng fejlen kode, der kan bruges til at klassificere typer fejl, der opstår, og kan bruges til at reagere på fejl. |
| error_description | En bestemt fejlmeddelelse, der kan hjælpe en udvikler identificere den egentlige årsag til en godkendelsesfejl.  |
| error_codes | En liste over STS bestemte fejlkoder, der kan hjælpe med at diagnosticering. |
| tidsstempel | Det tidspunkt, hvor der opstod en fejl. |
| trace_id | Et entydigt id for den forespørgsel, der kan hjælpe med at diagnosticering.  |
| correlation_id | Et entydigt id for den forespørgsel, der kan hjælpe med at diagnosticering på tværs af komponenter.|

#### <a name="http-status-codes"></a>HTTP status-koder

I følgende tabel vises de HTTP statuskoder, der returnerer token udstedelse slutpunktet. Fejlkoden er tilstrækkelige til at beskrive svaret i nogle tilfælde, men i tilfælde af fejl, du skal analysere ledsagende JSON dokumentet, og Undersøg dens fejlkode.

| HTTP-kode | Beskrivelse |
|-----------|-------------|
| 400       | Standard HTTP-kode. Bruges i de fleste tilfælde og er typisk på grund af en forkert anmodning. Løse og sende anmodningen igen. |
| 401       | Godkendelse mislykkedes. For eksempel mangler anmodningen parameteren client_secret.|
| 403       | Godkendelsen mislykkedes. For eksempel har brugeren ikke tilladelse til at få adgang til ressourcen. |
| 500       | Der opstod en intern fejl i tjenesten. Forsøg anmodningen igen. |

#### <a name="error-codes-for-token-endpoint-errors"></a>Fejlkoder for sikkerhedstoken slutpunkt fejl

| Fejlkode | Beskrivelse | Klienthandling |
|------------|-------------|---------------|
| invalid_request | Protocol fejl, som en manglende påkrævet parameter. | Løse og sende anmodningen igen |
| invalid_grant | Godkendelseskoden er ugyldig eller er udløbet. | Prøv en ny anmodning på `/authorize` slutpunkt |
| unauthorized_client | Godkendte klienten har ikke tilladelse til at bruge denne tilladelse Giv type. | Dette sker normalt, når klientprogrammet ikke er registreret i Azure AD eller er ikke føjet til brugerens Azure AD-lejer. Programmet kan brugeren med for at installere programmet og føje det til Azure AD-instruktionssæt. |
| invalid_client | Klientgodkendelse mislykkedes. | Klient-legitimationsoplysninger er ikke gyldige. For at løse, opdaterer programmet administratoren legitimationsoplysningerne. |
| unsupported_grant_type | Godkendelse serveren understøtter ikke tilladelse Giv type. | Ændre typen Giv i anmodningen. Denne type fejl skal udføres, kun under udvikling og findes under indledende testen. |
| invalid_resource | Target ressourcen er ugyldig, fordi den ikke findes, Azure AD kan ikke finde den eller den ikke er konfigureret korrekt. | Her angives ressourcen, hvis den findes, ikke som er blevet konfigureret i lejeren. Programmet kan brugeren med for at installere programmet og føje det til Azure AD-instruktionssæt. |
| interaction_required | Anmodningen kræver brugerinput. For eksempel er en yderligere godkendelse trin påkrævet. | Prøv igen på anmodningen med den samme ressource. |
| temporarily_unavailable | Serveren er midlertidigt optaget og kan håndtere anmodningen. | Forsøg anmodningen igen. Klientprogrammet kan forklare til brugeren, dens svar er forsinket forfaldsdato en midlertidig tilstand.|

## <a name="use-the-access-token-to-access-the-resource"></a>Brug adgangstoken til at få adgang til ressourcen

Nu hvor du har hentet korrekt en `access_token`, du kan bruge tokenet i anmodninger om at Web API'er, ved at medtage det i den `Authorization` sidehoved. Specifikationen [RFC 6750](http://www.rfc-editor.org/rfc/rfc6750.txt) forklarer, hvordan du bruger bæreren tokens i HTTP-anmodninger til at få adgang til beskyttede ressourcer.

### <a name="sample-request"></a>Eksempel anmodning

```
GET /data HTTP/1.1
Host: service.contoso.com
Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1THdqcHdBSk9NOW4tQSJ9.eyJhdWQiOiJodHRwczovL3NlcnZpY2UuY29udG9zby5jb20vIiwiaXNzIjoiaHR0cHM6Ly9zdHMud2luZG93cy5uZXQvN2ZlODE0NDctZGE1Ny00Mzg1LWJlY2ItNmRlNTdmMjE0NzdlLyIsImlhdCI6MTM4ODQ0MDg2MywibmJmIjoxMzg4NDQwODYzLCJleHAiOjEzODg0NDQ3NjMsInZlciI6IjEuMCIsInRpZCI6IjdmZTgxNDQ3LWRhNTctNDM4NS1iZWNiLTZkZTU3ZjIxNDc3ZSIsIm9pZCI6IjY4Mzg5YWUyLTYyZmEtNGIxOC05MWZlLTUzZGQxMDlkNzRmNSIsInVwbiI6ImZyYW5rbUBjb250b3NvLmNvbSIsInVuaXF1ZV9uYW1lIjoiZnJhbmttQGNvbnRvc28uY29tIiwic3ViIjoiZGVOcUlqOUlPRTlQV0pXYkhzZnRYdDJFYWJQVmwwQ2o4UUFtZWZSTFY5OCIsImZhbWlseV9uYW1lIjoiTWlsbGVyIiwiZ2l2ZW5fbmFtZSI6IkZyYW5rIiwiYXBwaWQiOiIyZDRkMTFhMi1mODE0LTQ2YTctODkwYS0yNzRhNzJhNzMwOWUiLCJhcHBpZGFjciI6IjAiLCJzY3AiOiJ1c2VyX2ltcGVyc29uYXRpb24iLCJhY3IiOiIxIn0.JZw8jC0gptZxVC-7l5sFkdnJgP3_tRjeQEPgUn28XctVe3QqmheLZw7QVZDPCyGycDWBaqy7FLpSekET_BftDkewRhyHk9FW_KeEz0ch2c3i08NGNDbr6XYGVayNuSesYk5Aw_p3ICRlUV1bqEwk-Jkzs9EEkQg4hbefqJS6yS1HoV_2EsEhpd_wCQpxK89WPs3hLYZETRJtG5kvCCEOvSHXmDE6eTHGTnEgsIk--UlPe275Dvou4gEAwLofhLDQbMSjnlV5VLsjimNBVcSRFShoxmQwBJR_b2011Y5IuD6St5zPnzruBbZYkGNurQK63TJPWmRd3mbJsGM0mf3CUQ
```

### <a name="error-response"></a>Fejlsvar

Sikret ressourcer, der implementerer RFC 6750 problem HTTP statuskoder. Hvis anmodningen inkluderer ikke godkendelsesoplysninger eller mangler tokenet, svaret indeholder en `WWW-Authenticate` sidehoved. Når en anmodning om mislykkes, ressourceserveren svarer med den en HTTP-statuskode og en fejlkode.

Følgende er et eksempel på et mislykkes svar, når klientens anmodning ikke omfatter bærertokenet:

```
HTTP/1.1 401 Unauthorized
WWW-Authenticate: Bearer authorization_uri="https://login.window.net/contoso.com/oauth2/authorize",  error="invalid_token",  error_description="The access token is missing.",
```

#### <a name="error-parameters"></a>Fejl parametre

| Parameter | Beskrivelse |
|-----------|-------------|
| authorization_uri | URI (fysisk slutpunkt) på serveren, godkendelse. Denne værdi bruges også som en opslagsnøgle til at få flere oplysninger om serveren fra en registrering slutpunkt. <p><p> Klienten skal kontrollere, at godkendelse serveren er der er tillid til. Det er tilstrækkelige til at kontrollere, at URL-adressen begynder med https://login.windows.net eller et andet hostname, der understøtter Azure AD, når ressourcen, der er beskyttet af Azure AD. En lejer-specifikke ressource skal altid returnere en lejer-specifikke tilladelse URI. |
| fejl | En kode fejlværdi defineret i sektionen 5.2 af [OAuth 2.0 godkendelse Framework](http://tools.ietf.org/html/rfc6749).|
| error_description | En mere detaljeret beskrivelse af fejlen. Denne meddelelse er ikke beregnet til at være slutbrugerlicensaftale fulde.|
| ressource_id | Returnerer det entydige id for ressourcen. Klientprogrammet kan bruge dette id som værdien af den `resource` parameter, når der anmodes om en token for ressourcen. <p><p> Det er meget vigtigt at bekræfte denne værdi klientprogrammet, ellers en skadelig tjeneste muligvis til at frembringe et **udvidelse af rettigheder** angreb <p><p> Den anbefalede strategi for at undgå et angreb er at kontrollere, at den `resource_id` svarer til bunden af World Wide web API URL-adresse, der åbnes. Hvis der er adgang til https://service.contoso.com/data, eksempelvis de `resource_id` kan være htttps://service.contoso.com/. Klientprogrammet skal afvise en `resource_id` , der begynder ikke med grundlæggende URL-adressen, medmindre der er en pålidelig alternative metode til at kontrollere id'et. |

#### <a name="bearer-scheme-error-codes"></a>Bæreren farveskema fejlkoder

Specifikationen RFC 6750 definerer følgende fejl for de ressourcer, der bruger ved hjælp af WWW godkende sidehovedet og bæreren farveskema i svaret.

| HTTP-statuskode | Fejlkode | Beskrivelse | Klienthandling |
|------------------|------------|-------------|---------------|
| 400 | invalid_request | Anmodningen er ikke korrekt udformet. For eksempel kan det mangler en parameter eller ved hjælp af samme parameteren to gange. | Rette fejlen, og forsøg anmodningen igen. Denne type fejl skal udføres, kun under udvikling og findes i første afprøvning. |
| 401 | invalid_token   | Adgangstoken er gået tabt, ugyldige eller er tilbagekaldt. Værdien af parameteren error_description indeholder flere detaljer. |  Anmode om et nyt id fra serveren godkendelse. Hvis det nye token mislykkes, der opstod en uventet fejl. Sende en fejlmeddelelse til den bruger, og prøv igen efter tilfældige forsinkelser. |
| 403 | insufficient_scope | Adgangstoken indeholder ikke repræsentation tilladelse til at få adgang til ressourcen. | Sende en ny anmodning om tilladelse til godkendelse slutpunkt. Hvis svaret indeholder parameteren omfang, kan du bruge værdien omfang i anmodningen til ressourcen. |
| 403 | insufficient_access | Emnet for tokenet har ikke de tilladelser, der er nødvendige for at få adgang til ressourcen. | Spørg brugeren at bruge en anden konto eller anmode om tilladelser til den angivne ressource. |

## <a name="refreshing-the-access-tokens"></a>Opdatere access tokens

Access Tokens er forbigående og skal opdateres, når de udløber for at fortsætte med at få adgang til ressourcer. Du kan opdatere den `access_token` ved at sende en anden `POST` anmode om at den `/token` slutpunkt, men denne gang give den `refresh_token` i stedet for den `code`.

Opdatere tokens ikke har angivet levetid. Levetid for opdatering tokens er typisk relativt lange. Men i nogle tilfælde Opdater tokens udløber, er tilbagekaldt eller mangler nødvendige rettigheder til den ønskede handling. Dit program skal forvente og håndtere fejl, der returneres af sikkerhedstoken udstedelse slutpunktet korrekt.

Når du modtager et svar med fejlen Opdater token, Slet den aktuelle opdatering token og anmode om en ny godkendelseskode eller access-token. Især når ved hjælp af en opdatering token i godkendelse kode Giv flow, hvis du modtager et svar med det `interaction_required` eller `invalid_grant` fejlkoder, Slet Opdater tokenet og anmode om en ny Godkendelseskode.

Anmode om en stikprøve til **lejer-specifikke** slutpunktet (du kan også bruge **almindelige** slutpunktet) til at få en ny access token ved hjælp af en opdatering token ser sådan ud:

```
// Line breaks for legibility only

POST /{tenant}/oauth2/token HTTP/1.1
Host: https://login.microsoftonline.com
Content-Type: application/x-www-form-urlencoded

client_id=6731de76-14a6-49ae-97bc-6eba6914391e
&refresh_token=OAAABAAAAiL9Kn2Z27UubvWFPbm0gLWQJVzCTE9UkP3pSx1aXxUjq...
&grant_type=refresh_token
&resource=https%3A%2F%2Fservice.contoso.com%2F
&client_secret=JqQX2PNo9bpM0uEihUPzyrh    // NOTE: Only required for web apps
```
| Parameter | Beskrivelse |
|-----------|-------------|
| access_token | Den nye adgangstoken, der blev anmodet om.|
| expires_in   | Resterende levetiden for tokenet i sekunder. En typisk værdi er 3600 (1 time). |
| expires_on   | Dato og klokkeslæt, hvor tokenet udløber. Datoen, der repræsenteres som antallet sekunder fra 1970-01-01T0:0:0Z UTC indtil udløbsdatoen. |
| refresh_token | En ny OAuth 2.0-refresh_token, der kan bruges til at anmode om nye access tokens, når i dette svar udløber. |
| ressource     | Identificerer den beskyttede ressource, der kan være adgangstoken anvendte til access. |
| omfang        | Repræsentation tilladelser til den oprindelige klientprogrammet. Standardtilladelsen til er **user_impersonation**. Ejeren af target ressourcen kan registrere alternative værdier i Azure AD. |
| token_type   | Typen token. Den eneste understøttede værdi er **bæreren**. |

### <a name="successful-response"></a>Vellykket svar

En vellykket token svar ser sådan ud:

```
{
  "token_type": "Bearer",
  "expires_in": "3600",
  "expires_on": "1460404526",
  "resource": "https://service.contoso.com/",
  "access_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1THdqcHdBSk9NOW4tQSJ9.eyJhdWQiOiJodHRwczovL3NlcnZpY2UuY29udG9zby5jb20vIiwiaXNzIjoiaHR0cHM6Ly9zdHMud2luZG93cy5uZXQvN2ZlODE0NDctZGE1Ny00Mzg1LWJlY2ItNmRlNTdmMjE0NzdlLyIsImlhdCI6MTM4ODQ0MDg2MywibmJmIjoxMzg4NDQwODYzLCJleHAiOjEzODg0NDQ3NjMsInZlciI6IjEuMCIsInRpZCI6IjdmZTgxNDQ3LWRhNTctNDM4NS1iZWNiLTZkZTU3ZjIxNDc3ZSIsIm9pZCI6IjY4Mzg5YWUyLTYyZmEtNGIxOC05MWZlLTUzZGQxMDlkNzRmNSIsInVwbiI6ImZyYW5rbUBjb250b3NvLmNvbSIsInVuaXF1ZV9uYW1lIjoiZnJhbmttQGNvbnRvc28uY29tIiwic3ViIjoiZGVOcUlqOUlPRTlQV0pXYkhzZnRYdDJFYWJQVmwwQ2o4UUFtZWZSTFY5OCIsImZhbWlseV9uYW1lIjoiTWlsbGVyIiwiZ2l2ZW5fbmFtZSI6IkZyYW5rIiwiYXBwaWQiOiIyZDRkMTFhMi1mODE0LTQ2YTctODkwYS0yNzRhNzJhNzMwOWUiLCJhcHBpZGFjciI6IjAiLCJzY3AiOiJ1c2VyX2ltcGVyc29uYXRpb24iLCJhY3IiOiIxIn0.JZw8jC0gptZxVC-7l5sFkdnJgP3_tRjeQEPgUn28XctVe3QqmheLZw7QVZDPCyGycDWBaqy7FLpSekET_BftDkewRhyHk9FW_KeEz0ch2c3i08NGNDbr6XYGVayNuSesYk5Aw_p3ICRlUV1bqEwk-Jkzs9EEkQg4hbefqJS6yS1HoV_2EsEhpd_wCQpxK89WPs3hLYZETRJtG5kvCCEOvSHXmDE6eTHGTnEgsIk--UlPe275Dvou4gEAwLofhLDQbMSjnlV5VLsjimNBVcSRFShoxmQwBJR_b2011Y5IuD6St5zPnzruBbZYkGNurQK63TJPWmRd3mbJsGM0mf3CUQ",
  "refresh_token": "AwABAAAAv YNqmf9SoAylD1PycGCB90xzZeEDg6oBzOIPfYsbDWNf621pKo2Q3GGTHYlmNfwoc-OlrxK69hkha2CF12azM_NYhgO668yfcUl4VBbiSHZyd1NVZG5QTIOcbObu3qnLutbpadZGAxqjIbMkQ2bQS09fTrjMBtDE3D6kSMIodpCecoANon9b0LATkpitimVCrl PM1KaPlrEqdFSBzjqfTGAMxZGUTdM0t4B4rTfgV29ghDOHRc2B-C_hHeJaJICqjZ3mY2b_YNqmf9SoAylD1PycGCB90xzZeEDg6oBzOIPfYsbDWNf621pKo2Q3GGTHYlmNfwoc-OlrxK69hkha2CF12azM_NYhgO668yfmVCrl-NyfN3oyG4ZCWu18M9-vEou4Sq-1oMDzExgAf61noxzkNiaTecM-Ve5cq6wHqYQjfV9DOz4lbceuYCAA"
}
```

### <a name="error-response"></a>Fejlsvar

Et eksempel fejlsvar kunne se sådan ud:

```
{
  "error": "invalid_resource",
  "error_description": "AADSTS50001: The application named https://foo.microsoft.com/mail.read was not found in the tenant named 295e01fc-0c56-4ac3-ac57-5d0ed568f872.  This can happen if the application has not been installed by the administrator of the tenant or consented to by any user in the tenant.  You might have sent your authentication request to the wrong tenant.\r\nTrace ID: ef1f89f6-a14f-49de-9868-61bd4072f0a9\r\nCorrelation ID: b6908274-2c58-4e91-aea9-1f6b9c99347c\r\nTimestamp: 2016-04-11 18:59:01Z",
  "error_codes": [
    50001
  ],
  "timestamp": "2016-04-11 18:59:01Z",
  "trace_id": "ef1f89f6-a14f-49de-9868-61bd4072f0a9",
  "correlation_id": "b6908274-2c58-4e91-aea9-1f6b9c99347c"
}
```

| Parameter | Beskrivelse |
| ----------------------- | ------------------------------- |
| fejl | En streng fejlen kode, der kan bruges til at klassificere typer fejl, der opstår, og kan bruges til at reagere på fejl. |
| error_description | En bestemt fejlmeddelelse, der kan hjælpe en udvikler identificere den egentlige årsag til en godkendelsesfejl.  |
| error_codes | En liste over STS bestemte fejlkoder, der kan hjælpe med at diagnosticering. |
| tidsstempel | Det tidspunkt, hvor der opstod en fejl. |
| trace_id | Et entydigt id for den forespørgsel, der kan hjælpe med at diagnosticering.  |
| correlation_id | Et entydigt id for den forespørgsel, der kan hjælpe med at diagnosticering på tværs af komponenter.|

Du kan finde en beskrivelse af fejlkoderne og handlingen anbefalede klient [fejlkoder for sikkerhedstoken slutpunkt fejl](#error-codes-for-token-endpoint-errors).
