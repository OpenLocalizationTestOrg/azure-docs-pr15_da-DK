<properties
    pageTitle="Ændres til Azure AD v2.0 slutpunktet | Microsoft Azure"
    description="En beskrivelse af ændringer, der er henvisninger til app-model v2.0 prøveversionen protokoller."
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
    ms.date="09/16/2016"
    ms.author="dastrock"/>

# <a name="important-updates-to-the-v20-authentication-protocols"></a>Vigtige opdateringer til v2.0 godkendelsesprotokoller
Opmærksomhed udviklere! Over de næste to uger, kan vi foretage nogle få opdateringer til vores v2.0 godkendelsesprotokoller, der kan medføre Bryd ændringer til nogen apps, du har skrevet i vores eksempel periode.  

## <a name="who-does-this-affect"></a>Der påvirker dette?
Alle apps, der er skrevet til brug på v2.0 konvergeret godkendelse slutpunkt

```
https://login.microsoftonline.com/common/oauth2/v2.0/authorize
```

Du kan finde flere oplysninger om v2.0 slutpunktet [her](active-directory-appmodel-v2-overview.md).

Hvis du har oprettet en app ved hjælp af v2.0 slutpunktet ved at kode direkte til v2.0 protokollen, skal ved hjælp af vores OpenID kontakt eller OAuth web middlewares eller bruge andre 3 part-biblioteker til at udføre godkendelse, du være forberedt til at afprøve dine projekter og foretage ændringer, hvis det er nødvendigt.

## <a name="who-doesnt-this-affect"></a>Hvem dette påvirker ikke?
Nogen apps, der er skrevet mod fremstilling Azure AD-godkendelse slutpunktet,

```
https://login.microsoftonline.com/common/oauth2/authorize
```

Denne protokol er lave og vil ikke være problemer eventuelle ændringer.

Desuden, hvis din app **kun** bruger vores ADAL bibliotek til at udføre godkendelse, behøver du ikke ændre noget.  ADAL har afskærmede din app af ændringer.  

## <a name="what-are-the-changes"></a>Hvad er ændringerne?
### <a name="removing-the-x5t-value-from-jwt-headers"></a>Fjerne værdien x5t fra JWT sidehoveder
V2.0 slutpunktet bruger JWT tokens grundigt, som indeholder en parametre gruppehovedsektion med relevante metadata om tokenet.  Hvis du afkode overskriften for én af vores aktuelle JWTs, skal du finde noget i retning af:

```
{ 
    "type": "JWT",
    "alg": "RS256",
    "x5t": "MnC_VZcATfM5pOYiJHMba9goEKY",
    "kid": "MnC_VZcATfM5pOYiJHMba9goEKY"
}
```

Hvor både egenskaben "x5t" og "børn" identificere den offentlige nøgle, der skal bruges til at validere det token signatur, som hentes fra det OpenID forbinde metadata slutpunkt.

Den ændring, vi foretager her, er at fjerne egenskaben "x5t".  Du kan fortsætte med at bruge samme funktioner til at validere tokens, men du skal kun afhængige egenskaben "børn" til at hente den korrekte offentlige nøgle, som angivet i den OpenID forbinde protokol. 

> [AZURE.IMPORTANT] **Tingene: Sørg for, at din app ikke afhænger eksistensen af værdien x5t.**

### <a name="removing-profileinfo"></a>Fjerne profile_info
Tidligere v2.0 slutpunktet er blevet returnerer en base64-kodet JSON-objekt i token svar kaldet `profile_info`.  Når der anmodes om et adgangstoken fra v2.0 slutpunktet ved at sende en anmodning om at:

```
https://login.microsoftonline.com/common/oauth2/v2.0/token
```

Svaret vil se ud på følgende JSON-objektet:
```
{ 
    "token_type": "Bearer",
    "expires_in": 3599,
    "scope": "https://outlook.office.com/mail.read",
    "access_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsI...",
    "refresh_token": "OAAABAAAAiL9Kn2Z27UubvWFPbm0gL...",
    "profile_info": "eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsI...",
}
```

Den `profile_info` værdi, der findes oplysninger om den bruger, der er logget på app - deres visningsnavn, Fornavn, efternavn, e-mailadresse, id osv.  Primært, den `profile_info` blev brugt til token cachelagring og få vist formål.

Vi er nu ved at fjerne den `profile_info` værdi – men bare rolig, vi stadig giver disse oplysninger til udviklere i en anden placering.  I stedet for `profile_info`, returnerer v2.0 slutpunktet nu en `id_token` i hvert token svar:

```
{ 
    "token_type": "Bearer",
    "expires_in": 3599,
    "scope": "https://outlook.office.com/mail.read",
    "access_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsI...",
    "refresh_token": "OAAABAAAAiL9Kn2Z27UubvWFPbm0gL...",
    "id_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsI...",
}
```

Du kan afkode og fortolke id_token for at hente de samme oplysninger, du har modtaget fra profile_info.  Id_token er en JSON Web Token (JWT), med indhold som angivet af OpenID forbindelse.  Koden for at gøre det skal være minder meget om – skal du blot trække det midterste segment (brødtekst) på id_token og base64 afkode den for at få adgang til JSON objektet i.

Over de næste to uger, skal du kode din app for at hente brugeroplysninger fra enten den `id_token` eller `profile_info`; Afhængigt af hvad der er til stede.  På denne måde når ændringen er foretaget din app kan problemfrit håndtere overgangen fra `profile_info` til `id_token` uden afbrydelser.

> [AZURE.IMPORTANT] **Tingene: Sørg for, at din app ikke afhænger eksistensen af de `profile_info` værdi.**

### <a name="removing-idtokenexpiresin"></a>Fjerne id_token_expires_in
Ligner `profile_info`, vi er også fjerne den `id_token_expires_in` parameter fra svar.  Tidligere v2.0 slutpunktet vil returnere en værdi for `id_token_expires_in` sammen med hver id_token svarmeddelelse ved for eksempel i et godkende svar:

```
https://myapp.com?id_token=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsI...&id_token_expires_in=3599...
```

Eller i et token svar:

```
{ 
    "token_type": "Bearer",
    "id_token_expires_in": 3599,
    "scope": "openid",
    "id_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsI...",
    "refresh_token": "OAAABAAAAiL9Kn2Z27UubvWFPbm0gL...",
    "profile_info": "eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsI...",
}
```

Den `id_token_expires_in` værdi vil angive antallet sekunder på id_token forbliver gyldige for.  Vi er nu at fjerne den `id_token_expires_in` værdi helt.  I stedet kan du bruge den OpenID forbinde standard `nbf` og `exp` udgiver sig for at undersøge en id_token gyldighed.  Se [v2.0 token reference](active-directory-v2-tokens.md) kan finde flere oplysninger om disse krav.

> [AZURE.IMPORTANT] **Tingene: Sørg for, at din app ikke afhænger eksistensen af de `id_token_expires_in` værdi.**


### <a name="changing-the-claims-returned-by-scopeopenid"></a>Ændre de krav, der returneres af omfang = openid
Denne ændring vil være den mest betydningsfulde – faktisk, påvirker det næsten alle app, der bruger v2.0 slutpunkt.  Mange programmer sende anmodninger til den v2.0 slutpunkt ved hjælp af den `openid` omfang vil:

```
https://login.microsoftonline.com/common/oauth2/v2.0/authorize?
client_id=...
&redirect_uri=...
&response_mode=form_post
&response_type=id_token
&scope=openid offline_access https://outlook.office.com/mail.read
```

I dag, når brugeren giver tilladelse til den `openid` omfang, din app modtager et væld af oplysninger om brugeren i den resulterende id_token.  Disse krav kan medtage personens navn, foretrukne brugernavn, din mailadresse, objekt-ID og mere.

I denne opdatering, vi er ved at ændre oplysninger, som den `openid` omfang giver din app adgang til, at bedre comform med specifikationen OpenID oprette forbindelse.  Den `openid` omfang vil kun give din app til at logge brugeren i og modtage et app-specifikke id for brugeren i den `sub` gøre krav på id_token.  Krav i en id_token med kun den `openid` omfang, der er tildelt bliver personlige oplysninger.  Eksempel id_token krav er:

```
{ 
    "aud": "580e250c-8f26-49d0-bee8-1c078add1609",
    "iss": "https://login.microsoftonline.com/b9410318-09af-49c2-b0c3-653adc1f376e/v2.0 ",
    "iat": 1449520283,
    "nbf": 1449520283,
    "exp": 1449524183,
    "nonce": "12345",
    "sub": "MF4f-ggWMEji12KynJUNQZphaUTvLcQug5jdF2nl01Q",
    "tid": "b9410318-09af-49c2-b0c3-653adc1f376e",
    "ver": "2.0",
}
```

Hvis du vil hente personlige oplysninger (PII) om brugeren i din app, skal din app til at anmode om yderligere tilladelser fra brugeren.  Vi Introduktion til understøttelse af to nye områder fra den OpenID forbinde specifikation – den `email` og `profile` områder – som gør det muligt at gøre dette.

- Den `email` omfang er meget enkle – gør det din app adgang til brugerens primære mailadresse via det `email` gøre krav på id_token.  Bemærk, at den `email` Kræv altid kan ikke findes i id_tokens – det kun medtages Hvis de findes i brugerens profil.
- Den `profile` omfang giver din app adgang til alle andre grundlæggende oplysninger om brugeren – deres navn, foretrukne brugernavn, objekt-ID, og så videre.

Dette gør det muligt at kode din app på en minimale afsløre måde – du kan bede brugeren om lige sæt af oplysninger, der kræver din app til med databehandlingen.  Hvis du vil fortsætte med at få det samlede sæt af brugeroplysninger, modtager din app i øjeblikket, skal du medtage alle tre områder i dine anmodninger om tilladelse:

```
https://login.microsoftonline.com/common/oauth2/v2.0/authorize?
client_id=...
&redirect_uri=...
&response_mode=form_post
&response_type=id_token
&scope=openid profile email offline_access https://outlook.office.com/mail.read
```

Din app kan begynde at sende den `email` og `profile` områder med det samme og v2.0 slutpunkt skal acceptere disse to områder og starte anmoder om tilladelser fra brugere efter behov.  Men ændringen i fortolkning af den `openid` omfang kan træde i kraft i et par uger.

> [AZURE.IMPORTANT] **Tingene: tilføje den `profile` og `email` områder, hvis din app kræver oplysninger om brugeren.**  Bemærk, at ADAL skal medtage begge af disse tilladelser i anmodninger om som standard. 

### <a name="removing-the-issuer-trailing-slash"></a>Fjerne udsteder efterstillet skråstreg.
Tidligere tog værdien udsteder, der vises i tokens fra v2.0 slutpunkt formularen

```
https://login.microsoftonline.com/{some-guid}/v2.0/
```

Hvor guid blev tenantId af den Azure AD-lejer som udstedt tokenet.  Med disse ændringer bliver værdien udsteder

```
https://login.microsoftonline.com/{some-guid}/v2.0 
```

i begge tokens og i OpenID forbinde discovery-dokumentet.

> [AZURE.IMPORTANT] **Tingene: Sørg for, at din app accepterer værdien udsteder både med og uden en efterstillet skråstreg under udsteder validering.**

## <a name="why-change"></a>Hvorfor ændre?
Den primære motivation til Introduktion til disse ændringer er at er kompatibel med OpenID forbinde standard specifikationen.  Vi håber minimere forskelle mellem integration med Microsoft identitet services og med andre identitet tjenester i branchen, som OpenID forbinde kompatibel.  Vi vil aktivere udviklere til at bruge deres foretrukne Åbn kilde godkendelse biblioteker uden at ændre bibliotekerne til at imødekomme forskelle i Microsoft.

## <a name="what-can-you-do"></a>Hvad kan du gøre?
Fra og med i dag, kan du begynde at gøre alle de ændringer, der er beskrevet ovenfor.  Du bør med det samme:

1.  **Fjerne afhængigheder på den `x5t` sidehoved parameter.**
2.  **Problemfrit at håndtere overgangen fra `profile_info` til `id_token` i token svar.**
3.  **Fjerne afhængigheder på den `id_token_expires_in` parameter for svar.**
3.  **Tilføje den `profile` og `email` områder til din app, hvis din app skal grundlæggende brugeroplysninger.**
4.  **Acceptér udsteder værdier i tokens både med og uden en efterstillet skråstreg.**

Vores [v2.0 protocol dokumentation](active-directory-v2-protocols.md) er allerede blevet opdateret for at afspejle disse ændringer, så du kan bruge den som reference i Hjælp til at opdatere din kode.

Hvis du har yderligere spørgsmål på omfanget af ændringerne, du Velkommen til at nå ud til os på Twitter på @AzureAD.

## <a name="how-often-will-protocol-changes-occur"></a>Hvor ofte sker protocol ændringer?
Vi ikke forudse yderligere Bryd ændres til godkendelsesprotokoller.  Vi bevidst samler disse ændringer i en version, så du ikke behøver at gennemgå denne type af opdateringsprocesser igen som helst snart.  Vi fortsætter med at føje funktioner til godkendelsestjenesten konvergeret v2.0, kan du drage fordel af naturligvis, men disse ændringer skal være tilsætningsstoffet og ikke sideskift eksisterende kode.

Endelig vil vi sige tak, fordi du prøver ting i perioden, preview.  Indsigt og oplevelser af vores Tidlige optagere har været uvurderlig dermed langt, og vi håber, du vil fortsætte med at dele din mening og ideer.

Tillykke kodningssprog!

Microsoft identitet Division
