<properties
    pageTitle="Azure AD v2.0 token reference | Microsoft Azure"
    description="Typerne tokens og krav, der udsendes af slutpunktet v2.0"
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

# <a name="v20-token-reference"></a>v2.0 token reference

V2.0 slutpunktet udsender flere typer sikkerhedstokens i behandling af hver [godkendelse forløb](active-directory-v2-flows.md). I dette dokument beskrives det filformat, sikkerhed egenskaber og indholdet af hver type token.

> [AZURE.NOTE]
    Ikke alle Azure Active Directory scenarier og funktioner, der understøttes af v2.0 slutpunkt.  For at bestemme, hvis du skal bruge v2.0 slutpunkt skal du læse om [v2.0 begrænsninger](active-directory-v2-limitations.md).

## <a name="types-of-tokens"></a>Typer af tokens

V2.0 slutpunktet understøtter [OAuth 2.0 authorization protocol](active-directory-v2-protocols.md), hvilket gør brug af både access_tokens og refresh_tokens.  Det understøtter også godkendelse og logge på via [OpenID oprette forbindelse](active-directory-v2-protocols.md#openid-connect-sign-in-flow), som introducerer en tredje type token, id_token.  Hver af disse tokens repræsenteres som en "bærertoken".

En bærertoken er et lette sikkerhedstoken, der giver adgang til en beskyttet ressource "bæreren". I denne mening er "bæreren" Enhver part, der kan præsentere tokenet. Selvom fest skal først godkende med Azure AD modtage bærertoken, hvis de nødvendige trin ikke er i secure token i overførslen og lager, kan den opfange og bruges af en utilsigtede part. Mens nogle sikkerhedstokens har en indbygget ordning for at forhindre uautoriserede personer i at bruge dem, bæreren tokens har ikke denne funktion og skal være forsendelse i en sikker kanal som transport lag sikkerhed (HTTPS). Hvis en bærertoken er sendt i Ryd, kan en mand i den midterste angreb bruges af en skadelig part til at købe tokenet og bruge den til en uautoriseret adgang til en beskyttet ressource. De samme sikkerhedsprincipper anvende når lagring eller cachelagring bæreren tokens til senere brug. Altid sikre, at din app transmitterer og gemmer bæreren tokens på en sikker måde. Du kan finde flere overvejelser om sikkerheden på bæreren tokens, [RFC 6750 trin 5](http://tools.ietf.org/html/rfc6750).

Mange af de tokens, der er udstedt af v2.0 slutpunktet implementeres som Json Web Tokens eller JWTs.  En JWT er en kompakt, URL-adressen sikker måde at overføre oplysninger mellem to parter.  Oplysningerne i JWTs kaldes også "krav" eller påstande af oplysninger om bæreren og emnet for tokenet.  Krav i JWTs er JSON objekter kodet og serialiseres til overførsel.  Da JWTs udstedt af v2.0 slutpunktet er signeret, men ikke er krypteret, kan du nemt Undersøg indholdet af en JWT forbindelse med fejlfinding. Yderligere oplysninger om JWTs, kan du referere til [JWT specifikation](http://self-issued.info/docs/draft-ietf-oauth-json-web-token.html).

## <a name="idtokens"></a>Id_tokens

Id_tokens er en slags logon sikkerhedstoken, som din app modtager, når du udfører godkendelse med [OpenID Opret forbindelse](active-directory-v2-protocols.md#openid-connect-sign-in-flow).  De repræsenteres som [JWTs](#types-of-tokens)og indeholder krav, du kan bruge til at logge brugeren på din app.  Du kan bruge krav i en id_token, som behov – ofte de anvendes til visning af kontooplysninger eller foretage access kontrolelement beslutninger i en app.  V2.0 slutpunktet udsteder kun én type id_token, som har et ensartet sæt af krav uanset bruger, der er logget på.  Det er at sige, at formatet og indholdet af id_tokens vil være den samme for begge personlige Microsoft-Account brugere og arbejds- eller skolekonto konti.

Id_tokens er signeret, men ikke krypteret på nuværende tidspunkt.  Når din app modtager en id_token, skal den [godkendelse af signaturen](#validating-tokens) for at bevise det token ægthedsbevis og validere nogle krav i tokenet til at bevise dens gyldighed.  Krav godkendt af en app varierer afhængigt af scenariet krav, men der er nogle [almindelige krav valideringer](#validating-tokens) , der skal udføre din app i hver scenarie.

Detaljerede oplysninger om krav i id_tokens er angivet nedenfor, samt et eksempel id_token.  Bemærk, at der ikke skal returneres krav i id_tokens i en bestemt rækkefølge.  Desuden kan introduceres nye krav til id_tokens når som helst tidspunkt – din app ikke skal bryde som nye påstande introduceres.  På listen herunder indeholder de krav, som din app kan pålideligt fortolke på tidspunktet for dette blev skrevet.  Hvis det er nødvendigt, kan endnu mere detaljeret findes i [OpenID forbinde specifikation](http://openid.net/specs/openid-connect-core-1_0.html).

#### <a name="sample-idtoken"></a>Eksempel id_token

```
eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsImtpZCI6Ik1uQ19WWmNBVGZNNXBPWWlKSE1iYTlnb0VLWSJ9.eyJhdWQiOiI2NzMxZGU3Ni0xNGE2LTQ5YWUtOTdiYy02ZWJhNjkxNDM5MWUiLCJpc3MiOiJodHRwczovL2xvZ2luLm1pY3Jvc29mdG9ubGluZS5jb20vYjk0MTk4MTgtMDlhZi00OWMyLWIwYzMtNjUzYWRjMWYzNzZlL3YyLjAiLCJpYXQiOjE0NTIyODUzMzEsIm5iZiI6MTQ1MjI4NTMzMSwiZXhwIjoxNDUyMjg5MjMxLCJuYW1lIjoiQmFiZSBSdXRoIiwibm9uY2UiOiIxMjM0NSIsIm9pZCI6ImExZGJkZGU4LWU0ZjktNDU3MS1hZDkzLTMwNTllMzc1MGQyMyIsInByZWZlcnJlZF91c2VybmFtZSI6InRoZWdyZWF0YmFtYmlub0BueXkub25taWNyb3NvZnQuY29tIiwic3ViIjoiTUY0Zi1nZ1dNRWppMTJLeW5KVU5RWnBoYVVUdkxjUXVnNWpkRjJubDAxUSIsInRpZCI6ImI5NDE5ODE4LTA5YWYtNDljMi1iMGMzLTY1M2FkYzFmMzc2ZSIsInZlciI6IjIuMCJ9.p_rYdrtJ1oCmgDBggNHB9O38KTnLCMGbMDODdirdmZbmJcTHiZDdtTc-hguu3krhbtOsoYM2HJeZM3Wsbp_YcfSKDY--X_NobMNsxbT7bqZHxDnA2jTMyrmt5v2EKUnEeVtSiJXyO3JWUq9R0dO-m4o9_8jGP6zHtR62zLaotTBYHmgeKpZgTFB9WtUq8DVdyMn_HSvQEfz-LWqckbcTwM_9RNKoGRVk38KChVJo4z5LkksYRarDo8QgQ7xEKmYmPvRr_I7gvM2bmlZQds2OeqWLB1NSNbFZqyFOCgYn3bAQ-nEQSKwBaA36jYGPOVG2r2Qv1uKcpSOxzxaQybzYpQ
```

> [AZURE.TIP] Øvelse, kan du prøve at undersøge krav i eksempel id_token ved at indsætte det i [calebb.net](https://calebb.net).

#### <a name="claims-in-idtokens"></a>Krav i id_tokens
| Navn | Gør krav | Eksempel på værdi | Beskrivelse |
| ----------------------- | ------------------------------- | ------------ | --------------------------------- |
| Målgruppe | `aud` | `6731de76-14a6-49ae-97bc-6eba6914391e` | Identificerer den ønskede modtager af tokenet.  I id_tokens er målgruppen din app program-Id, som tildelt til din app i portalen app registrering.  Din app skal validere denne værdi og Afvis tokenet, hvis det ikke stemmer overens. |
| Udsteder | `iss` | `https://login.microsoftonline.com/b9419818-09af-49c2-b0c3-653adc1f376e/v2.0 ` | Identificerer tjenesten for sikkerhedstoken (STS), der opretter og returnerer tokenet, samt den Azure AD-lejer, hvori brugeren er blevet godkendt.  Din app skal validere udsteder krav til at sikre, at tokenet stammer fra v2.0 slutpunktet.  Det skal også bruge guid del af kravet til at begrænse sæt af lejere, der har tilladelse til at logge på appen.  Guid, der angiver brugeren er en forbruger bruger fra Microsoft-konto er `9188040d-6c67-4c5b-b112-36a304b66dad`. |
| Udstedt på | `iat` | `1452285331` | Den tid, som er udstedt tokenet, repræsenteret i epoke tid. |
| Udløbsdato | `exp` | `1452289231` | Den tid, hvormed tokenet bliver ugyldige, repræsenteret i epoke tid.  Din app skal bruge denne krav til at bekræfte token levetiden gyldighed.  |
| Tidligst | `nbf` | `1452285331` |  Den tid, hvormed tokenet bliver gyldig, repræsenteret i epoke tid. Det er som regel den samme som handlingen udstedelse klokkeslæt.  Din app skal bruge denne krav til at bekræfte token levetiden gyldighed.  |
| Version | `ver` | `2.0` | Versionen af id_token, som defineret af Azure AD.  For slutpunkt v2.0 værdien bliver `2.0`. |
| Lejer-Id | `tid` | `b9419818-09af-49c2-b0c3-653adc1f376e` | Et guid, der repræsenterer den Azure AD lejer som brugeren er fra.  Guid for arbejds- og skoleopgaver konti, vil være fast lejer ID'ET for den organisation, som brugeren tilhører.  For personlige konti, vil værdien være `9188040d-6c67-4c5b-b112-36a304b66dad`.  Den `profile` omfang er påkrævet for at modtage denne krav. |
| Kode Hash | `c_hash` | `SGCPtt01wxwfgnYZy2VJtQ` | Giver kode resultat er inkluderet i id_tokens kun, når id_token udstedes sammen med en OAuth 2.0 Godkendelseskode.  Det kan bruges til at validere autenticiteten af en godkendelseskode for.  Se [OpenID forbinde specifikation](http://openid.net/specs/openid-connect-core-1_0.html) få mere at vide om at udføre denne validering. |
| Access Token Hash | `at_hash` | `SGCPtt01wxwfgnYZy2VJtQ` | Giver adgang token resultat er inkluderet i id_tokens kun, når id_token udstedes sammen med et OAuth 2.0-adgangstoken.  Det kan bruges til at validere autenticiteten af et adgangstoken.  Se [OpenID forbinde specifikation](http://openid.net/specs/openid-connect-core-1_0.html) få mere at vide om at udføre denne validering. |
| Nonce | `nonce` | `12345` | Noncen er en strategi for mindske token genafspilningsangreb.  Din app kan angive en foreløbig hændelse i en anmodning om en tilladelse ved hjælp af den `nonce` forespørgsel parameter.  Den værdi, du giver i anmodningen der udledes i id_token `nonce` krav, uændrede.  Dette giver mulighed for din app til at kontrollere værdien op mod den værdi, der er det angivet på anmodningen, som knytter den app session med en given id_token.  Din app skal udføre denne validering under processen id_token validering. |
| Navn | `name` | `Babe Ruth` | Kravet navn indeholder en human læsbar værdi, der identificerer emnet for tokenet. Denne værdi er ingen garanti for skal være entydig, er af og er udviklet til at bruges kun til visning.  Den `profile` omfang er påkrævet for at modtage denne krav. |
| Mail | `email` | `thegreatbambino@nyy.onmicrosoft.com` | Den primære mailadresse, der er knyttet til brugerkontoen, hvis der findes en.  Dens værdi er af og kan ændres til en given bruger over tid.  Den `email` omfang er påkrævet for at modtage denne krav. |
| Foretrukne brugernavn | `preferred_username` | `thegreatbambino@nyy.onmicrosoft.com` | Det primære brugernavn, der bruges til at repræsentere brugeren i v2.0 slutpunkt.  Det kan være en e-mail-adresse, telefonnummer eller en generisk brugernavn uden et bestemt format.  Dens værdi er af og kan ændres til en given bruger over tid.  Den `profile` omfang er påkrævet for at modtage denne krav. |
| Emne | `sub` | `MF4f-ggWMEji12KynJUNQZphaUTvLcQug5jdF2nl01Q` | Hovedstolen om, hvilke tokenet imperative forudsætninger oplysninger, som brugeren af en app. Denne værdi fast og kan ikke tildelt igen eller igen, så den kan bruges til at udføre godkendelse kontrol på en sikker måde, som når tokenet bruges til at få adgang til en ressource. Da emnet altid er til stede i tokens Azure AD-problemer, anbefaler vi bruge denne værdi i et almindelige formål authorization system. |
| ObjectId | `oid` | `a1dbdde8-e4f9-4571-ad93-3059e3750d23` | Objekt-id'et for kontoen arbejds- eller skolekonto i Azure AD-systemet.  Dette krav udstedes ikke til personlige Microsoft-konti.  Den `profile` omfang er påkrævet for at modtage denne krav. |


## <a name="access-tokens"></a>Access tokens

Access tokens udstedt af v2.0 slutpunktet kan kun konsumeres af Microsoft Services på dette tidspunkt.  Dine apps skal ikke nødvendigt at udføre nogen validering eller kontrol af adgangstokens på grund af de aktuelt understøttede scenarier.  Du kan behandle access tokens som helt uigennemsigtig – de er kun strenge, som kan overføre din app til Microsoft i HTTP-anmodninger.

I den nærmeste fremtid introducerer v2.0 slutpunktet muligheden for, at din app til at modtage access tokens fra andre klienter.  På tidspunkt opdateres oplysningerne med din app behøver for at udføre access token validering og andre lignende opgaver.

Når du anmoder om et adgangstoken fra v2.0 slutpunkt, returnerer v2.0 slutpunktet også nogle metadata om adgangstoken til din app forbrug.  Disse oplysninger omfatter udløbet tidspunktet for adgangstoken og de områder, som det er relevant.  Dette giver mulighed for din app til at udføre intelligent cachelagring af access tokens uden at analysere Åbn adgangstoken selve.

## <a name="refresh-tokens"></a>Opdatere tokens

Opdatere tokens er sikkerhedstokens som din app kan bruge til at få fat på ny åbner tokens i en OAuth 2.0-flow.  Det gør det muligt for din app til at opnå langsigtede adgang til ressourcer på vegne af en bruger uden interaktion af brugeren.

Opdater tokens er flere ressource.  Det vil sige være, at en Opdater-token, der er modtaget i en token anmodning om en ressource kan indløst for access tokens til en helt anden ressource.

For at modtage en opdatering i et token svar, din app skal anmode om og have tildelt den `offline_acesss` omfang.   Til at få mere at vide om de `offline_access` omfang, skal du se [samtykke & områder artikel her](active-directory-v2-scopes.md).

Opdatere tokens er, og altid være, helt uigennemsigtig til din app.  De kan er udstedt af Azure AD v2.0 slutpunkt og kun være kontrolleres og fortolket af v2.0 slutpunkt.  De er langlivet, men din app ikke skal skrives du kan forvente, at en opdatering token varer i en periode.  Opdater tokens kan erklæres på et tidspunkt tidspunkt for en række forskellige årsager.  Den eneste måde at vide om en opdatering token er gyldig din App er forsøger at indløse ved at oprette en token anmodning til v2.0 slutpunkt.

Når du indløse en opdatering token til en ny adgangstoken (og hvis din app har fået de `offline_access` omfang), modtager du en ny opdatering token i token svaret.  Du skal gemme token nyligt udstedt Opdater erstatte den, du brugte i anmodningen.  Dette sikrer, at din opdatering tokens forbliver gyldige så længe som muligt.

## <a name="validating-tokens"></a>Validere tokens

På dette tidspunkt, er det kun token validering dine apps skal skal udføre validering af id_tokens.  For at validere en id_token, skal din app validere både den id_token signatur og krav i id_token.

<!-- TODO: Link -->
Vi giver dig biblioteker og kodeeksempler, der viser, hvordan du håndterer nemt token validering – den under oplysninger blot er beregnet til dem, som ønsker at forstå underliggende processen.  Der findes også flere 3 part Åbn kilde-biblioteker til JWT validering – der er mindst én indstilling til næsten alle platform og sprog, der findes.

#### <a name="validating-the-signature"></a>Validering af signaturen
En JWT indeholder tre målgrupper, der er adskilt af den `.` tegn.  Det første segment kendes som **sidehoved**, andet som **brødteksten**, og tredjepart som **signatur**.  Segmentet signatur kan bruges til at validere autenticiteten af id_token, så det kan være tillid til på din app.

Id_Tokens er signeret ved hjælp af branche standard asymmetrisk krypteringsalgoritmer, som RSA 256. Overskriften for id_token indeholder oplysninger om metoden nøgle og kryptering bruges til at signere tokenet:

```
{
  "typ": "JWT",
  "alg": "RS256",
  "kid": "MnC_VZcATfM5pOYiJHMba9goEKY"
}
```

Den `alg` krav angiver den algoritme, der blev brugt til at signere adgangstoken, mens den `kid` krav angiver den bestemt offentlig nøgle, der blev brugt til at signere tokenet.

Når som helst givet tidspunkt kan v2.0 slutpunktet Log en id_token ved hjælp af en af et bestemt sæt af offentlige og private nøgler par.  V2.0 slutpunktet roterer den mulige sæt nøgler med jævne mellemrum, så din app der skal skrives til at håndtere de vigtigste ændringer automatisk.  En begrundet frekvens for at søge efter opdateringer til de offentlige nøgler, der bruges af v2.0 slutpunkt er om 24 timer.

Du kan få fat på den signerende vigtige data til at validere signaturen ved hjælp af metadata OpenID forbinde dokumentet findes på:

```
https://login.microsoftonline.com/common/v2.0/.well-known/openid-configuration
```

> [AZURE.TIP] Prøv denne URL-adresse i en browser!

Metadatadokumentet er et JSON-objekt, der indeholder flere stykker nyttige oplysninger som placeringen af de forskellige slutpunkter, der kræves for at udføre OpenID forbinde godkendelse.  

Den indeholder også en `jwks_uri`, der giver placeringen af sæt af offentlige nøgler, der bruges til at signere tokens.  JSON dokumentet findes i den `jwks_uri` indeholder alle de offentlige vigtige oplysninger i brug på den bestemt tidspunkt, hvor tidspunkt.  Din app kan bruge den `kid` gøre krav på hovedet JWT for at vælge, hvilke offentlig nøgle i dette dokument er blevet brugt til at signere et bestemt token.  Det kan udføre signaturen ved hjælp af den korrekte offentlige nøgle og den angivne algoritme.

Udføre signaturen er omfattet af dette dokument – der er mange Åbn kilde-biblioteker til, hvordan du gør dette, hvis det er nødvendigt.

#### <a name="validating-the-claims"></a>Validering af krav
Når din app modtager en id_token på bruger-logon, skal det også udføre nogle få kontrol over krav i id_token.  Dette omfatter, men er ikke begrænset til:

- **Målgruppe** kravet - at bekræfte, at id_token blev beregnet tildeles til din app.
- De **Ikke før** og **Udløbsdatoen** krav - at bekræfte, at id_token ikke er udløbet.
- **Udsteder** kravet - at bekræfte, at Tokenet er udstedt faktisk til din app, af v2.0 slutpunkt.
- **Nonce** - som en token genafspilning angreb afhjælpning.
- og meget mere...

Referere til [specifikation af OpenID oprette forbindelse](http://openid.net/specs/openid-connect-core-1_0.html#IDTokenValidation)til en komplet liste over krav valideringer din app skal udføre.

Oplysninger om de forventede værdier for disse krav medtages over i den [id_token sektion](#id_tokens).


## <a name="token-lifetimes"></a>Token levetid

Den følgende token levetid findes kun til forstået, som de kan hjælpe med at udvikle og fejlfinding apps.  Dine apps ikke skal skrives du kan forvente nogen af disse levetid til forbliver konstante – de kan og vil ændre på et bestemt tidspunkt i gang.

| Token | Levetid | Beskrivelse |
| ----------------------- | ------------------------------- | ------------ |
| Id_Tokens (arbejds- eller skolekonto-konti) | 1 time | Id_Tokens er typisk gyldige i timen.  Din online kan bruge denne samme levetid i vedligeholde sin egen session med brugeren (anbefales), eller Vælg en helt anden session levetid.  Hvis din app behov for at få en ny id_token, skal du blot nødvendig for at få en ny logon anmodning på v2.0 Godkend slutpunkt.  Hvis brugeren har en gyldig browsersession med v2.0 slutpunktet, kan de ikke blive nødt til at indtaste legitimationsoplysningerne igen. |
| Id_Tokens (personlige konti) | 24 timer | Id_Tokens for personlige konti er typisk gyldige til 24 timer.  Din online kan bruge denne samme levetid i vedligeholde sin egen session med brugeren (anbefales), eller Vælg en helt anden session levetid.  Hvis din app behov for at få en ny id_token, skal du blot nødvendig for at få en ny logon anmodning på v2.0 Godkend slutpunkt.  Hvis brugeren har en gyldig browsersession med v2.0 slutpunktet, kan de ikke blive nødt til at indtaste legitimationsoplysningerne igen. |
| Access Tokens (arbejds- eller skolekonto-konti) | 1 time | Angivet i token svar som en del af de token metadata. |
| Access Tokens (personlige konti) | 1 time | Angivet i token svar som en del af de token metadata.  Access_tokens udstedt på vegne af personlige konti kan konfigureres til en anden levetid, men en time er typisk tilfældet. |
| Opdatere Tokens (arbejds- eller skolekonto konto) | Op til 14 dage | Et enkelt Opdater token er gyldige for maksimalt 14 dage.  Opdater tokenet kan blive ugyldige når som helst for en hvilken som helst antal årsager, så din app skal fortsætte med at forsøge at bruge en Opdater-token, indtil det afbrydes, eller indtil din app erstatter det med et nyt Opdater-id.  En opdatering token, også blive ugyldige, hvis det er 90 dage siden som brugeren har skrevet deres legitimationsoplysninger. |
| Opdatere Tokens (personlige konti) | Op til 1 år | Et enkelt Opdater token er gyldige for maksimalt 1 år.  Opdater tokenet kan blive ugyldige når som helst for en hvilken som helst antal årsager, så din app skal fortsætte med at forsøge at bruge en Opdater-token, indtil det afbrydes. |
| Authorization koder (arbejds- eller skolekonto-konti) | 10 minutter | Godkendelseskoder er vilje gjort forbigående, og skal være lige har indløst for access_tokens og refresh_tokens når de modtages. |
| Godkendelseskoder (personlige konti) | 5 minutter | Authorization koder er vilje gjort forbigående, og skal være lige har indløst for access_tokens og refresh_tokens når de modtages.  Godkendelseskoder udstedt på vegne af personlige konti er også bruges én gang. |
