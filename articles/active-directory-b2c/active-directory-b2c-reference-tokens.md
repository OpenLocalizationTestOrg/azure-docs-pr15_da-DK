<properties
    pageTitle="Azure Active Directory B2C | Microsoft Azure"
    description="Typerne tokens udstedt i Azure Active Directory B2C."
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


# <a name="azure-ad-b2c-token-reference"></a>Azure AD-B2C: Token reference

Azure Active Directory (Azure AD) B2C udsender flere typer sikkerhedstokens, når det behandler hver [godkendelse forløb](active-directory-b2c-apps.md). I dette dokument beskrives det filformat, sikkerhed egenskaber og indholdet af hver type token.

## <a name="types-of-tokens"></a>Typer af tokens

Azure AD-B2C understøtter [OAuth 2.0 authorization protocol](active-directory-b2c-reference-protocols.md), hvilket gør brug af både access tokens og Opdater tokens. Det understøtter også godkendelse og logge på via [OpenID oprette forbindelse](active-directory-b2c-reference-protocols.md), som introducerer en tredje type token: id-token. Hver af disse tokens repræsenteres som en bærertokenet.

En bærertoken er et lette sikkerhedstoken, der giver adgang til en beskyttet ressource "bæreren". Bæreren er en hvilken som helst part, der kan præsentere tokenet. Azure AD skal først godkende fest, før den kan modtage en bærertokenet. Men hvis de nødvendige trin ikke er i secure token i overførslen og lager, det kan hentes og bruges af en utilsigtede part. Nogle sikkerhedstokens har en indbygget ordning for at forhindre uautoriserede personer i at bruge dem, men bæreren tokens har ikke denne funktion. De skal være transport på en sikker kanal, såsom transport lag sikkerhed (HTTPS).

Hvis en bærertoken er sendt uden for en sikker kanal, kan skadelig fest bruge et mand i midten angreb til at købe tokenet og bruge det til at få uautoriseret adgang til en beskyttet ressource. De samme sikkerhedsprincipper anvende når bæreren tokens gemt eller cachelagret til senere brug. Altid sikre, at din app transmitterer og gemmer bæreren tokens på en sikker måde.

Du kan finde yderligere sikkerhedsovervejelser på bæreren tokens, [RFC 6750 trin 5](http://tools.ietf.org/html/rfc6750).

Mange af de tegn, der Azure AD B2C problemer implementeres som JSON web tokens (JWTs). En JWT er en kompakt, URL-adressen sikker måde at overføre oplysninger mellem to parter. JWTs indeholder oplysninger, der er kendt som krav. Dette er påstande oplysninger om bæreren og emnet for tokenet. Krav i JWTs er JSON-objekter, der-kodet og serialiseres til overførsel. Da JWTs udstedt af Azure AD B2C er signeret, men ikke er krypteret, kan du nemt Undersøg indholdet af en JWT til at foretage fejlfinding af den. Der findes flere hjælpeværktøjer, som kan gøre dette, herunder [calebb.net](http://calebb.net). Du kan finde flere oplysninger om JWTs referere til [JWT specifikationer](http://self-issued.info/docs/draft-ietf-oauth-json-web-token.html).

### <a name="id-tokens"></a>Id-tokens

Et ID-token er en slags sikkerhedstoken, som din app modtager fra Azure AD B2C `authorize` og `token` slutpunkter. ID tokens repræsenteres som [JWTs](#types-of-tokens), og de indeholder krav, du kan bruge til at identificere brugere i din app. Når ID tokens er hentet fra den `authorize` slutpunkt, de er ofte bruges til at logge på brugere til webprogrammer. Når ID tokens er hentet fra den `token` slutpunkt, de kan sendes i HTTP-anmodninger under kommunikation mellem to komponenter i samme programmet eller tjenesten. Du kan bruge krav i et ID-token efter behov. De er ofte anvendte for at vise kontooplysninger eller tage beslutninger om kontrol af adgang i en app.  

ID tokens er signeret, men de er krypteret ikke på nuværende tidspunkt. Når din app eller API modtager et ID-token, skal den [godkendelse af signaturen](#token-validation) for at bevise, at Tokenet er autentisk. Din app eller API skal du også validere nogle krav i tokenet til at bevise, at det er gyldig. Krav godkendt af en app kan variere, afhængigt af krav scenarie, men din app skal udføre nogle [almindelige krav valideringer](#token-validation) i hver scenarie.

#### <a name="sample-id-token"></a>Eksempel-ID token
```
// Line breaks for display purposes only

eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsImtpZCI6IklkVG9rZW5TaWduaW5nS2V5Q29udGFpbmVyIn0.
eyJleHAiOjE0NDIzNjAwMzQsIm5iZiI6MTQ0MjM1NjQzNCwidmVyIjoiMS4wIiwiaXNzIjoiaHR0cHM6Ly9s
b2dpbi5taWNyb3NvZnRvbmxpbmUuY29tLzc3NTUyN2ZmLTlhMzctNDMwNy04YjNkLWNjMzExZjU4ZDkyNS92
Mi4wLyIsImFjciI6ImIyY18xX3NpZ25faW5fc3RvY2siLCJzdWIiOiJOb3Qgc3VwcG9ydGVkIGN1cnJlbnRs
eS4gVXNlIG9pZCBjbGFpbS4iLCJhdWQiOiI5MGMwZmU2My1iY2YyLTQ0ZDUtOGZiNy1iOGJiYzBiMjlkYzYi
LCJpYXQiOjE0NDIzNTY0MzQsImF1dGhfdGltZSI6MTQ0MjM1NjQzNCwiaWRwIjoiZmFjZWJvb2suY29tIn0.
h-uiKcrT882pSKUtWCpj-_3b3vPs3bOWsESAhPMrL-iIIacKc6_uZrWxaWvIYkLra5czBcGKWrYwrAC8ZvQe
DJWZ50WXQrZYODEW1OUwzaD_I1f1HE0c2uvaWdGXBpDEVdsD3ExKaFlKGjFR2V7F-fPThkVDdKmkUDQX3bVc
yyj2V2nlCQ9jd7aGnokTPfLfpOjuIrTsAdPcGpe5hfSEuwYDmqOJjGs9Jp1f-eSNEiCDQOaTBSvr479L5ptP
XWeQZyX2SypN05Rjr05bjZh3j70ZUimiocfJzjibeoDCaQTz907yAg91WYuFOrQxb-5BaUoR7K-O7vxr2M-_
CQhoFA

```

### <a name="access-tokens"></a>Access tokens

Et adgangstoken er også en formular af sikkerhedstoken, som din app modtager fra Azure AD B2C `authorize` og `token` slutpunkter. Access tokens repræsenteres også som [JWTs](#types-of-tokens), og de indeholder krav, du kan bruge til at identificere brugere i din webtjenester og API'er.

Access tokens er signeret, men de er ikke krypteret på nuværende tidspunkt- og minder meget om id tokens.  Access tokens skal bruges til at give adgang til webtjenester og API'er og til at identificere og godkende brugeren i disse tjenester.  De indeholder dog ikke en hvilken som helst program tillades på disse tjenester.  Det vil sige, den `scp` krav i access tokens ikke begrænse eller på anden måde repræsenterer den adgang, som emnet for tokenet har fået tildelt.

Når din API modtager et adgangstoken, skal det [godkendelse af signaturen](#token-validation) for at bevise, at Tokenet er autentisk. Din API skal også validere nogle krav i tokenet til at bevise, at det er gyldig. Krav godkendt af en app kan variere, afhængigt af krav scenarie, men din app skal udføre nogle [almindelige krav valideringer](#token-validation) i hver scenarie.

### <a name="claims-in-id--access-tokens"></a>Krav i-ID og Access Tokens

Når du bruger Azure AD B2C, har du detaljerede kontrol over indholdet af din tokens. Du kan konfigurere [politikker](active-directory-b2c-reference-policies.md) for at sende bestemte sæt af brugerdata i krav, der kræver din app til dens handlinger. Disse krav kan omfatte standardegenskaber som brugerens `displayName` og `emailAddress`. De kan også indeholde [brugerdefinerede attributter](active-directory-b2c-reference-custom-attr.md) , du kan definere i B2C-biblioteket. Hver-ID og access token, du modtager skal indeholde et bestemt sæt af sikkerhedsrelaterede krav. Dine programmer kan bruge disse krav til sikker godkendelse af brugere og anmodninger.

Bemærk, at krav i ID tokens ikke returneres i en bestemt rækkefølge. Desuden kan nye påstande indføres i ID tokens når som helst. Din app bør ikke sideskift, som nye påstande introduceres. Her er de krav, du forventer at findes i-ID og access tokens, der er udstedt af Azure AD B2C. Eventuelle yderligere krav bestemmes af politikker. Øvelse, kan du prøve at undersøge krav i eksempel-ID tokenet ved at indsætte det i [calebb.net](http://calebb.net). Yderligere oplysninger finder du i [OpenID forbinde specifikation](http://openid.net/specs/openid-connect-core-1_0.html).

| Navn | Gør krav | Eksempel på værdi | Beskrivelse |
| ----------------------- | ------------------------------- | ------------ | --------------------------------- |
| Målgruppe | `aud` | `90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6` | Et publikum krav identificerer den ønskede modtager af tokenet. Til Azure AD B2C er målgruppen din app program-ID, som tildelt til din app i portalen app registrering. Din app skal validere denne værdi og Afvis tokenet, hvis det ikke stemmer overens. |
| Udsteder | `iss` | `https://login.microsoftonline.com/775527ff-9a37-4307-8b3d-cc311f58d925/v2.0/` | Dette krav identificerer tjenesten for sikkerhedstoken (STS), der opretter og returnerer tokenet. Den identificerer også den Azure AD-mappe, hvor brugeren er blevet godkendt. Din app skal validere udsteder krav til at sikre, at tokenet stammer fra v2.0 slutpunktet. |
| Udstedt på | `iat` | `1438535543` | Dette krav er den tid, hvormed tokenet, er udstedt, repræsenteret epoke tidspunkt. |
| Udløbsdato | `exp` | `1438539443` | Udløbsdatoen krav er den tid, hvormed tokenet bliver ugyldige, repræsenteret i epoke tid. Din app skal bruge denne Kravbrugeren for at bekræfte token levetiden gyldighed.  |
| Tidligst | `nbf` | `1438535543` | Dette krav er den tid, hvormed tokenet bliver gyldig, repræsenteres på epoke tid. Dette er som regel den samme som den tid, der er udstedt tokenet. Din app skal bruge denne krav til at bekræfte token levetiden gyldighed.  |
| Version | `ver` | `1.0` | Dette er versionen af token ID, som defineret af Azure AD. |
| Kode hash | `c_hash` | `SGCPtt01wxwfgnYZy2VJtQ` | En kode hash er inkluderet i et ID token kun, når tokenet udstedes sammen med en OAuth 2.0 Godkendelseskode. En kode hash kan bruges til at validere autenticiteten af en godkendelseskode for. Se [OpenID forbinde specifikation](http://openid.net/specs/openid-connect-core-1_0.html) få mere at vide om, hvordan du udfører denne validering. |
| Access token hash | `at_hash` | `SGCPtt01wxwfgnYZy2VJtQ` | En access-token hashværdi er inkluderet i et ID-token kun, når tokenet udstedes sammen med et OAuth 2.0-adgangstoken. En access-token hashværdi kan bruges til at validere autenticiteten af et adgangstoken. Se [OpenID forbinde specifikation](http://openid.net/specs/openid-connect-core-1_0.html) få mere at vide om, hvordan du udfører denne validering. |
| Nonce | `nonce` | `12345` | En foreløbig hændelse er en strategi, der bruges til at reducere token genafspilningsangreb. Din app kan angive en foreløbig hændelse i en anmodning om en tilladelse ved hjælp af den `nonce` forespørgsel parameter. Den værdi, du giver i anmodningen der udledes uændrede i den `nonce` gøre krav på en id-token. Dette giver mulighed for din app til at kontrollere værdien op mod den værdi, der er det angivet på anmodningen, som knytter den app session med et bestemt ID-token. Din app skal udføre denne validering under processen ID token validering. |
| Emne | `sub` | `Not supported currently. Use oid claim.` | Dette er en sikkerhedskonto om, hvilke tokenet imperative forudsætninger oplysninger, som brugeren af en app. Denne værdi er fast og kan ikke tildelt igen eller igen. Det kan bruges til at udføre godkendelse kontrol på en sikker måde, som når tokenet bruges til at få adgang til en ressource. Dog er emne kravet endnu ikke implementeret i Azure AD B2C. Du skal konfigurere dine politikker for at medtage objekt-ID'ET `oid` gøre krav på og bruge dens værdi til at identificere brugere i stedet for at bruge emne kravet om godkendelse. |
| Godkendelse kontekst klassereference | `acr` | `b2c_1_sign_in` | Dette er navnet på politikken, der blev brugt til at få fat på id-token.  |
| Godkendelse af tid | `auth_time` | `1438535543` | Dette krav er den tid, hvormed et sidste indtastet brugerlegitimationsoplysninger, repræsenteret epoke tidspunkt. |


### <a name="refresh-tokens"></a>Opdatere tokens

Opdatere tokens er sikkerhedstokens, som din app kan bruge til at få fat på nye ID tokens og få adgang til tokens i en OAuth 2.0-flow. De giver din app langsigtede adgang til ressourcer på vegne af brugere uden interaktion med disse brugere.

Modtage en opdatering token i svar token din app skal anmode om den `offline_acesss` omfang. Til at få mere at vide om de `offline_access` omfang, skal du henvise til [Azure AD B2C protocol reference](active-directory-b2c-reference-protocols.md).

Opdatere tokens er, og altid være, helt uigennemsigtig til din app. De kan er udstedt af Azure AD og kontrolleres og fortolket kun Azure AD. De er langlivet, men din app ikke skal skrives med den forventning, at en opdatering token varer for et bestemt tidsrum. Opdater tokens kan erklæres på et tidspunkt for en række forskellige årsager. Den eneste måde at vide om en opdatering token er gyldig din App er forsøger at indløse ved at foretage en token anmodning Azure AD.

Når du indløse en opdatering token for et nyt id (og hvis din app har fået tildelt den `offline_access` omfang), modtager du en ny opdatering token i token svaret. Du skal gemme nyligt udstedt Opdater tokenet. Det skal erstatte det Opdater token, du tidligere har brugt i anmodningen. Dette hjælper med at sikre, at din opdatering tokens forbliver gyldige så længe som muligt.

## <a name="token-validation"></a>Token validering

Hvis du vil validere et token, skal din app kontrollere standardsignatur eller krænkelse af tokenet.

Mange Åbn kilde biblioteker er tilgængelige til validering af JWTs, afhængigt af dit foretrukne sprog. Vi anbefaler, at du udforske de relevante indstillinger i stedet for at implementere dine egne validering logik. Oplysningerne i denne vejledning kan hjælpe dig med at lære, hvordan du korrekt brug af disse biblioteker.

### <a name="validate-the-signature"></a>Godkendelse af signaturen
En JWT indeholder tre målgrupper, adskilt af den `.` tegn. Det første segment er **sidehoved**, andet er **brødteksten**, og tredjepart er **signatur**. Segmentet signatur kan bruges til at validere autenticiteten af tokenet, så det kan være tillid til på din app.

Azure AD B2C tokens er signeret af ved hjælp af branchestandard asymmetrisk krypteringsalgoritmer, som RSA 256. Overskriften for tokenet indeholder oplysninger om metoden nøgle og kryptering bruges til at signere tokenet:

```
{
        "typ": "JWT",
        "alg": "RS256",
        "kid": "GvnPApfWMdLRi8PDmisFn7bprKg"
}
```

Den `alg` krav angiver den algoritme, der blev brugt til at signere tokenet. Den `kid` Kravbrugeren angiver de bestemt offentlig nøgle, der blev brugt til at signere tokenet.

Når som helst angivne log Azure AD et token ved hjælp af en af et bestemt sæt af offentlige og private nøgler par. Azure AD roterer taster mulige sæt med jævne mellemrum, så din app der skal skrives til at håndtere de vigtigste ændringer automatisk. En begrundet frekvens for at søge efter opdateringer til de offentlige nøgler, der bruges af Azure AD er døgnet.

Azure AD-B2C har et OpenID forbinde metadata slutpunkt. Dette giver mulighed for apps til at hente oplysninger om Azure AD B2C på kørselstidspunktet. Disse oplysninger omfatter slutpunkter, token indhold og token logge taster. B2C mappen indeholder et JSON metadata dokument for hver politik. For eksempel dokumentets metadata for den `b2c_1_sign_in` politik i den `fabrikamb2c.onmicrosoft.com` er placeret på:

```
https://login.microsoftonline.com/fabrikamb2c.onmicrosoft.com/v2.0/.well-known/openid-configuration?p=b2c_1_sign_in
```

`fabrikamb2c.onmicrosoft.com`er den B2C mappe, der bruges til at godkende brugeren, og `b2c_1_sign_in` er politik for bruges til at få fat på tokenet. Til at afgøre, hvilken politik blev brugt til at signere et token (og Sådan kommer du til at hente metadata), har du to muligheder. Første gang, politikkens navn er inkluderet i den `acr` gøre krav på tokenet. Du kan oversætte krænkelse af brødteksten i JWT ved base-64 kodning brødteksten og deserialisering af strengen JSON, der er resultatet. Den `acr` krav bliver navnet på politikken, der blev brugt til at udstede tokenet.  Din anden mulighed er at kodes politikken i værdien af den `state` parameter, når du udstede anmodningen, og derefter afkode den for at finde ud af, hvilken politik blev brugt. Begge metoder er gyldig.

Metadatadokumentet er et JSON-objekt, der indeholder flere stykker nyttige oplysninger. Dette omfatter placeringen af slutpunkterne kræves for at udføre OpenID forbinde godkendelse. De indeholder også `jwks_uri`, der giver placeringen af sæt af offentlige nøgler, der er brugt til at signere tokens. Placering er angivet her, men det er bedst at hente placeringen dynamisk ved hjælp af Metadatadokumentet og fortolkning af `jwks_uri`:

```
https://login.microsoftonline.com/fabrikamb2c.onmicrosoft.com/discovery/v2.0/keys?p=b2c_1_sign_in
```

JSON dokumentet findes i denne URL-adresse indeholder alle de offentlige vigtige oplysninger i brug på et bestemt tidspunkt. Din app kan bruge den `kid` gøre krav på hovedet JWT for at vælge den offentlige nøgle i JSON dokumentet, der bruges til at signere et bestemt token. Det kan udføre signaturen ved hjælp af den korrekte offentlige nøgle og den angivne algoritme.

En beskrivelse af, hvordan du udfører signaturen er omfattet af dette dokument. Mange Åbn kilde biblioteker er tilgængelige til at hjælpe dig med dette, hvis du vil have den.

### <a name="validate-the-claims"></a>Validere krav
Når din app eller API modtager et ID-token, bør det også udføre flere Kontroller mod påstande i id-token. Disse omfatter, men er ikke begrænset til:

- **Målgruppe** kravet: Dette bekræfter, at ID tokenet blev beregnet tildeles til din app.
- **Ikke før** og **udløbsdatoen** krav: disse bekræfte, at ID-tokenet ikke er udløbet.
- **Udsteder** kravet: Dette bekræfter, er udstedt tokenet til din app af Azure AD.
- **Nonce**: Dette er en strategi for sikkerhedstoken genafspilning angreb afhjælpning.

Se [specifikation af OpenID oprette forbindelse](https://openid.net)til en komplet liste over valideringer din app skal udføre. Oplysninger om de forventede værdier for disse krav er inkluderet i den foregående [token sektion](#types-of-tokens).  

## <a name="token-lifetimes"></a>Token levetid

Den følgende token levetid er angivet til yderligere din viden. De kan hjælpe dig, når du udvikle og fejlfinde apps. Bemærk, at dine apps ikke skal skrives du kan forvente nogen af disse levetid skal være konstant. De kan og vil ændre.  Du kan læse mere om tilpasning af sikkerhedstoken levetid i Azure AD B2C [her](active-directory-b2c-token-session-sso.md).

| Token | Levetid | Beskrivelse |
| ----------------------- | ------------------------------- | ------------ |
| Id-tokens | En time | ID tokens er typisk gyldige i timen. Din online kan bruge denne levetid til at bevare sin egen sessioner med brugere (anbefales). Du kan også vælge en anden session levetid. Hvis din app behov for at få et nyt ID token, skal du blot nødvendig for at få en ny anmodning-logon til Azure AD. Hvis en bruger har en gyldig browsersession med Azure AD, kan brugeren ikke forpligtet angive legitimationsoplysninger igen. |
| Opdatere tokens | Op til 14 dage | Et enkelt Opdater token er gyldige for maksimalt 14 dage. Men et Opdater token kan blive ugyldige når som helst for en hvilken som helst antal årsager. Din app bør fortsat forsøger at bruge et Opdater token, indtil anmodningen mislykkes, eller indtil din app erstatter Opdater tokenet med en ny.  En opdatering token kan også blive ugyldige, hvis 90 dage er gået, siden brugeren sidst angivne legitimationsoplysninger. |
| Godkendelseskoder | Fem minutter | Godkendelseskoder er bevidst forbigående. De skal være har indløst umiddelbart efter access tokens, ID tokens eller Opdater tokens når de modtages. |
