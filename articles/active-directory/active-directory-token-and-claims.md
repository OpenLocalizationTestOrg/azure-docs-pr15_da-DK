 <properties
   pageTitle="Azure AD-Token Reference | Microsoft Azure"
   description="En vejledning til at forstå og evaluering af krav i SAML 2.0 og JSON Web Tokens (JWT) tokens udstedt af Azure Active Directory (AAD)"
   documentationCenter="na"
   authors="bryanla"
   services="active-directory"
   manager="mbaldwin"
   editor=""/>

<tags
   ms.service="active-directory"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="identity"
   ms.date="10/06/2016"
   ms.author="mbaldwin"/>

# <a name="azure-ad-token-reference"></a>Azure AD token reference

Azure Active Directory (Azure AD) udsender flere typer sikkerhedstokens i behandling af hver godkendelse forløb. I dette dokument beskrives det filformat, sikkerhed egenskaber og indholdet af hver type token.

## <a name="types-of-tokens"></a>Typer af tokens

Azure AD understøtter [OAuth 2.0 authorization protocol](active-directory-protocols-oauth-code.md), hvilket gør brug af både access_tokens og refresh_tokens.  Det understøtter også godkendelse og logge på via [OpenID oprette forbindelse](active-directory-protocols-openid-connect-code.md), som introducerer en tredje type token, id_token.  Hver af disse tokens repræsenteres som en "bærertoken".

En bærertoken er et lette sikkerhedstoken, der giver adgang til en beskyttet ressource "bæreren". I denne mening er "bæreren" Enhver part, der kan præsentere tokenet. Selvom godkendelse med Azure AD er påkrævet for at modtage en bærertoken, skal der tages trin til at sikre tokenet, hvis du vil forhindre aflytning af en utilsigtede part. Fordi bæreren tokens ikke har en indbygget metode til at forhindre uautoriserede personer i at bruge dem, skal de transport på en sikker kanal som transport lag sikkerhed (HTTPS). Hvis en bærertoken er sendt i Ryd, kan en mand i den midterste angreb bruges til at købe tokenet og få uautoriseret adgang til en beskyttet ressource. De samme sikkerhedsprincipper anvende når lagring eller cachelagring bæreren tokens til senere brug. Altid sikre, at din app transmitterer og gemmer bæreren tokens på en sikker måde. Du kan finde flere overvejelser om sikkerheden på bæreren tokens, [RFC 6750 trin 5](http://tools.ietf.org/html/rfc6750).

Mange af de tokens, der er udstedt af Azure AD implementeres som JSON Web Tokens eller JWTs.  En JWT er en kompakt, URL-adressen sikker måde at overføre oplysninger mellem to parter.  Oplysningerne i JWTs kaldes også "krav" eller påstande af oplysninger om bæreren og emnet for tokenet.  Krav i JWTs er JSON objekter kodet og serialiseres til overførsel.  Da JWTs udstedt af Azure AD er signeret, men ikke er krypteret, kan du nemt Undersøg indholdet af en JWT forbindelse med fejlfinding.  Der findes flere værktøjer til at gøre dette, som [jwt.calebb.net](http://jwt.calebb.net). Yderligere oplysninger om JWTs, kan du referere til [JWT specifikation](http://self-issued.info/docs/draft-ietf-oauth-json-web-token.html).

## <a name="idtokens"></a>Id_tokens

Id_tokens er en slags logon sikkerhedstoken, som din app modtager, når du udfører godkendelse med [OpenID Opret forbindelse](active-directory-protocols-openid-connect-code.md).  De repræsenteres som [JWTs](#types-of-tokens)og indeholder krav, du kan bruge til at logge brugeren på din app.  Du kan bruge krav i en id_token, som behov – ofte de anvendes til visning af kontooplysninger eller foretage access kontrolelement beslutninger i en app.

Id_tokens er signeret, men ikke krypteret på nuværende tidspunkt.  Når din app modtager en id_token, skal den [godkendelse af signaturen](#validating-tokens) for at bevise det token ægthedsbevis og validere nogle krav i tokenet til at bevise dens gyldighed.  Krav godkendt af en app varierer afhængigt af scenariet krav, men der er nogle [almindelige krav valideringer](#validating-tokens) , der skal udføre din app i hver scenarie.

Se afsnittet nedenfor for at få oplysninger på id_tokens krav, samt et eksempel id_token.  Bemærk, at der ikke skal returneres krav i id_tokens i en bestemt rækkefølge.  Desuden kan introduceres nye krav til id_tokens når som helst tidspunkt – din app ikke skal bryde som nye påstande introduceres.  Følgende liste indeholder de krav, som din app kan pålideligt fortolke på tidspunktet for dette blev skrevet.  Hvis det er nødvendigt, kan endnu mere detaljeret findes i [OpenID forbinde specifikation](http://openid.net/specs/openid-connect-core-1_0.html).

#### <a name="sample-idtoken"></a>Eksempel id_token

```
eyJ0eXAiOiJKV1QiLCJhbGciOiJub25lIn0.eyJhdWQiOiIyZDRkMTFhMi1mODE0LTQ2YTctODkwYS0yNzRhNzJhNzMwOWUiLCJpc3MiOiJodHRwczovL3N0cy53aW5kb3dzLm5ldC83ZmU4MTQ0Ny1kYTU3LTQzODUtYmVjYi02ZGU1N2YyMTQ3N2UvIiwiaWF0IjoxMzg4NDQwODYzLCJuYmYiOjEzODg0NDA4NjMsImV4cCI6MTM4ODQ0NDc2MywidmVyIjoiMS4wIiwidGlkIjoiN2ZlODE0NDctZGE1Ny00Mzg1LWJlY2ItNmRlNTdmMjE0NzdlIiwib2lkIjoiNjgzODlhZTItNjJmYS00YjE4LTkxZmUtNTNkZDEwOWQ3NGY1IiwidXBuIjoiZnJhbmttQGNvbnRvc28uY29tIiwidW5pcXVlX25hbWUiOiJmcmFua21AY29udG9zby5jb20iLCJzdWIiOiJKV3ZZZENXUGhobHBTMVpzZjd5WVV4U2hVd3RVbTV5elBtd18talgzZkhZIiwiZmFtaWx5X25hbWUiOiJNaWxsZXIiLCJnaXZlbl9uYW1lIjoiRnJhbmsifQ.
```

> [AZURE.TIP] Øvelse, kan du prøve at undersøge krav i eksempel id_token ved at indsætte det i [calebb.net](http://jwt.calebb.net).

#### <a name="claims-in-idtokens"></a>Krav i id_tokens

| JWT krav | Navn | Beskrivelse |
|-----------|------|-------------|
| `appid`| Program-ID | Identificerer det program, der bruger tokenet til at få adgang til en ressource. Programmet kan fungere som selve eller på vegne af en bruger. Program-ID repræsenterer typisk et objekt i programmet på computeren, men det kan også repræsentere en tjeneste primært objekt i Azure AD. <br><br> **Eksempel på JWT værdi**: <br> `"appid":"15CB020F-3984-482A-864D-1D92265E8268"` |
| `aud`| Målgruppe | Tokenet tilsigtede modtagere. Det program, der modtager tokenet skal kontrollere, at værdien målgruppe er korrekt og afvise alle tokens, der er beregnet til en anden målgruppe. <br><br> **Eksempel SAML værdi**: <br> `<AudienceRestriction>`<br>`<Audience>`<br>`https://contoso.com`<br>`</Audience>`<br>`</AudienceRestriction>` <br><br> **Eksempel på JWT værdi**: <br> `"aud":"https://contoso.com"` |
| `appidacr`| Programmet godkendelse kontekst klassereference | Angiver, hvordan klienten er blevet godkendt. For en offentlig klient er værdien 0. Hvis der anvendes klient-ID og klienten hemmeligt, er værdien 1. <br><br> **Eksempel på JWT værdi**: <br> `"appidacr": "0"`|
| `acr`| Godkendelse kontekst klassereference | Angiver, hvor emnet er blevet godkendt, i modsætning til klienten i programmet godkendelse kontekst klassereference kravet. Værdien "0" Angiver slutbrugerlicensaftale godkendelse ikke opfylder kravene i ISO/IEC 29115. <br><br> **Eksempel på JWT værdi**: <br> `"acr": "0"`|
| | Godkendelse Chat | Registrerer dato og klokkeslæt, når der opstod godkendelse. <br><br> **Eksempel SAML værdi**: <br> `<AuthnStatement AuthnInstant="2011-12-29T05:35:22.000Z">` |
| `amr`| Godkendelsesmetode | Identificerer, hvordan emnet for tokenet blev godkendt. <br><br> **Eksempel SAML værdi**: <br> `<AuthnContextClassRef>`<br>`http://schemas.microsoft.com/ws/2008/06/identity/claims/authenticationmethod/password`<br>`</AuthnContextClassRef>` <br><br> **Eksempel på JWT værdi**:`“amr”: ["pwd"]` |
| `given_name`| Fornavn | Giver først eller "angivet" navnet på brugeren, som er angivet under brugerobjektet Azure AD. <br><br> **Eksempel SAML værdi**: <br> `<Attribute Name=”http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname”>`<br>`<AttributeValue>Frank<AttributeValue>` <br><br> **Eksempel på JWT værdi**: <br> `"given_name": "Frank"` |
| `groups`| Grupper | Indeholder objekt-id'er, der repræsenterer den emne gruppemedlemskaber. Disse værdier er entydige (se objekt-ID) og sikkert kan bruges til at administrere adgang, som sikres tilladelse til at få adgang til en ressource. De grupper, der er inkluderet i grupper kravet er konfigureret på grundlag-program gennem egenskaben "groupMembershipClaims" for programmanifestet. En værdi på nul udelader alle grupper, der er en værdi på "SecurityGroup" kun Active Directory-sikkerhedsgruppe medlemskaber og en værdi på "Alle" er omfatter både sikkerhedsgrupper og distributionslister af Office 365. <br><br> **Eksempel SAML værdi**: <br> `<Attribute Name="http://schemas.microsoft.com/ws/2008/06/identity/claims/groups">`<br>`<AttributeValue>07dd8a60-bf6d-4e17-8844-230b77145381</AttributeValue>` <br><br> **Eksempel på JWT værdi**: <br> `“groups”: ["0e129f5b-6b0a-4944-982d-f776045632af", … ]` |
| `idp` | Identitetsudbyder | Poster identitetsudbyder, som er godkendt emnet for tokenet. Denne værdi er identisk med værdien af udsteder kravet, medmindre brugerkontoen er i en anden lejer end udstederen. <br><br> **Eksempel SAML værdi**: <br> `<Attribute Name=” http://schemas.microsoft.com/identity/claims/identityprovider”>`<br>`<AttributeValue>https://sts.windows.net/cbb1a5ac-f33b-45fa-9bf5-f37db0fed422/<AttributeValue>` <br><br> **Eksempel på JWT værdi**: <br> `"idp":”https://sts.windows.net/cbb1a5ac-f33b-45fa-9bf5-f37db0fed422/”` |
| `iat` | IssuedAt | Gemmer den tid, hvormed Tokenet er udstedt. Det bruges ofte til at måle token friskhed. <br><br> **Eksempel SAML værdi**: <br> `<Assertion ID="_d5ec7a9b-8d8f-4b44-8c94-9812612142be" IssueInstant="2014-01-06T20:20:23.085Z" Version="2.0" xmlns="urn:oasis:names:tc:SAML:2.0:assertion">` <br><br> **Eksempel på JWT værdi**: <br> `"iat": 1390234181` |
| `iss` | Udsteder | Identificerer tjenesten for sikkerhedstoken (STS), der opretter og returnerer tokenet. I tokens, der returnerer Azure AD, er udstederen sts.windows.net. GUID i værdien udsteder krav er mappen Azure AD lejer ID. Lejer ID'ET er en fast og stabil id for kataloget. <br><br> **Eksempel SAML værdi**: <br> `<Issuer>https://sts.windows.net/cbb1a5ac-f33b-45fa-9bf5-f37db0fed422/</Issuer>` <br><br> **Eksempel på JWT værdi**: <br>  `"iss":”https://sts.windows.net/cbb1a5ac-f33b-45fa-9bf5-f37db0fed422/”` |
| `family_name` | Efternavn | Indeholder den sidste navn, efternavn eller efternavn på brugeren, som defineret i Azure AD-brugerobjektet. <br><br> **Eksempel SAML værdi**: <br> `<Attribute Name=” http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname”>`<br>`<AttributeValue>Miller<AttributeValue>` <br><br> **Eksempel på JWT værdi**: <br> `"family_name": "Miller"` |
| `unique_name`| Navn | Indeholder en human læsbar værdi, der identificerer emnet for tokenet. Denne værdi er ingen garanti for skal være entydigt i en lejer og er udviklet til at bruges kun til visning. <br><br> **Eksempel SAML værdi**: <br> `<Attribute Name=”http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name”>`<br>`<AttributeValue>frankm@contoso.com<AttributeValue>` <br><br> **Eksempel på JWT værdi**: <br> `"unique_name": "frankm@contoso.com"` |
| `oid` | Objekt-ID | Indeholder et entydigt id for et objekt i Azure AD. Denne værdi er fast og kan ikke tildelt igen eller genbruges. Bruge objekt-ID'ET til at identificere et objekt i forespørgsler til Azure AD. <br><br> **Eksempel SAML værdi**: <br> `<Attribute Name="http://schemas.microsoft.com/identity/claims/objectidentifier">`<br>`<AttributeValue>528b2ac2-aa9c-45e1-88d4-959b53bc7dd0<AttributeValue>` <br><br> **Eksempel på JWT værdi**: <br> `"oid":"528b2ac2-aa9c-45e1-88d4-959b53bc7dd0"` |
| `roles` | Roller | Repræsenterer alle roller i programmet, som emnet har fået både direkte og indirekte via et gruppemedlemskab og kan bruges til at gennemtvinge rollebaseret adgangskontrol. Programmet roller defineres på grundlag-program gennem den `appRoles` egenskab for programmanifestet. Den `value` egenskab for hver programmet rolle er den værdi, der vises i roller kravet. <br><br> **Eksempel SAML værdi**: <br> `<Attribute Name="http://schemas.microsoft.com/ws/2008/06/identity/claims/role">`<br>`<AttributeValue>Admin</AttributeValue>` <br><br> **Eksempel på JWT værdi**: <br> `“roles”: ["Admin", … ]` |
| `scp` | Omfang | Angiver de repræsentation tilladelser til klientprogrammet. Standardtilladelsen til er `user_impersonation`. Ejeren af den beskyttede ressource kan registrere flere værdier i Azure AD. <br><br> **Eksempel på JWT værdi**: <br> `"scp": "user_impersonation"`|
| `sub` |Emne| Identificerer hovedstolen om, hvilke tokenet imperative forudsætninger oplysninger, som brugeren af et program. Denne værdi fast og kan ikke tildelt igen eller igen, så den kan bruges til at udføre godkendelse kontrol på en sikker måde. Da emnet altid er til stede i tokens Azure AD-problemer, anbefaler vi bruge denne værdi i et system til godkendelse af generelle formål. <br> `SubjectConfirmation`er ikke et krav. Det beskrives, hvordan emnet for Tokenet er bekræftet. `Bearer`Angiver, at emnet er blevet bekræftet af besiddelse af tokenet. <br><br> **Eksempel SAML værdi**: <br> `<Subject>`<br>`<NameID>S40rgb3XjhFTv6EQTETkEzcgVmToHKRkZUIsJlmLdVc</NameID>`<br>`<SubjectConfirmation Method="urn:oasis:names:tc:SAML:2.0:cm:bearer" />`<br>`</Subject>` <br><br> **Eksempel på JWT værdi**: <br> `"sub":"92d0312b-26b9-4887-a338-7b00fb3c5eab"`|
| `tid` | Lejer-ID | En fast, ikke-genanvendelig id, der identificerer directory lejeren, udstedt tokenet. Du kan bruge denne værdi til at få adgang til lejer-specifikke directory ressourcer i et med flere arkitekturer til computeren. For eksempel kan du bruge denne værdi til at identificere leje i et opkald til Graph API. <br><br> **Eksempel SAML værdi**: <br> `<Attribute Name=”http://schemas.microsoft.com/identity/claims/tenantid”>`<br>`<AttributeValue>cbb1a5ac-f33b-45fa-9bf5-f37db0fed422<AttributeValue>` <br><br> **Eksempel på JWT værdi**: <br> `"tid":"cbb1a5ac-f33b-45fa-9bf5-f37db0fed422"`|
| `nbf`, `exp`|Token levetid | Definerer et tidsinterval, hvor et token er gyldig. Den tjeneste, der har valideret tokenet skal kontrollere, at den aktuelle dato er i token levetiden, ellers det skal afvise tokenet. Tjenesten kan tillade i op til fem minutter ud over området token levetid hensyn til en hvilken som helst forskellene i klokkeslættet ("tid skævhed") mellem Azure AD og -tjenesten. <br><br> **Eksempel SAML værdi**: <br> `<Conditions`<br>`NotBefore="2013-03-18T21:32:51.261Z"`<br>`NotOnOrAfter="2013-03-18T22:32:51.261Z"`<br>`>` <br><br> **Eksempel på JWT værdi**: <br> `"nbf":1363289634, "exp":1363293234` |
| `upn`| Brugerens hovednavn | Gemmer brugernavnet for den bruger hovedstolen.<br><br> **Eksempel på JWT værdi**: <br> `"upn": frankm@contoso.com`|
| `ver`| Version | Gemmer versionsnummeret for tokenet. <br><br> **Eksempel på JWT værdi**: <br> `"ver": "1.0"`|

## <a name="access-tokens"></a>Access tokens

Access tokens kan kun konsumeres af Microsoft Services på dette tidspunkt.  Dine apps skal ikke nødvendigt at udføre nogen validering eller kontrol af adgangstokens på grund af de aktuelt understøttede scenarier.  Du kan behandle access tokens som helt uigennemsigtig – de er kun strenge, som kan overføre din app til Microsoft i HTTP-anmodninger.

Når du anmoder om et adgangstoken, returnerer Azure AD også nogle metadata om adgangstoken til din app forbrug.  Disse oplysninger omfatter udløbet tidspunktet for adgangstoken og de områder, som det er relevant.  Dette giver mulighed for din app til at udføre intelligent cachelagring af access tokens uden at analysere Åbn adgangstoken selve.

## <a name="refresh-tokens"></a>Opdatere tokens

Opdatere tokens er sikkerhedstokens, som din app kan bruge til at hente nye access tokens i en OAuth 2.0-flow.  Det gør det muligt for din app til at opnå langsigtede adgang til ressourcer på vegne af en bruger uden interaktion af brugeren.

Opdater tokens er flere ressource, hvilket betyder, at de kan være modtaget i en token anmodning om en ressource, men har indløst for access tokens til en helt anden ressource. Hvis du vil angive flere ressource, skal du angive den `resource` parameter i anmodningen målrettede ressourcen.

Opdater tokens er helt uigennemsigtig til din app. De er langlivet, men din app ikke skal skrives du kan forvente, at en opdatering token varer i en periode.  Opdater tokens kan erklæres på et tidspunkt tidspunkt for en række forskellige årsager.  Den eneste måde at vide om en opdatering token er gyldig din App er forsøger at indløse ved at oprette en token anmodning til Azure AD token slutpunkt.

Når du indløse en opdatering token til en ny adgangstoken, får du et nyt id opdatering i token svaret.  Du skal gemme token nyligt udstedt Opdater erstatte den, du brugte i anmodningen.  Dette sikrer, at din opdatering tokens forbliver gyldige så længe som muligt.

## <a name="validating-tokens"></a>Validere tokens

På dette tidspunkt, er det kun token validering din klient-app skal skal udføre validering af id_tokens.  For at validere en id_token, skal din app validere både den id_token signatur og krav i id_token.

Vi giver dig biblioteker og kodeeksempler, der viser, hvordan du håndterer nemt token datavalidering, hvis du ønsker at forstå underliggende processen.  Der er også flere fra tredjepart Åbn kilde biblioteker for JWT validering, mindst én indstilling til næsten alle platform og sprog. Du kan finde flere oplysninger om Azure AD-godkendelse biblioteker og kodeeksempler [Azure AD-godkendelse biblioteker](active-directory-authentication-libraries.md).

#### <a name="validating-the-signature"></a>Validering af signaturen

En JWT indeholder tre målgrupper, der er adskilt af den `.` tegn.  Det første segment kendes som **sidehoved**, andet som **brødteksten**, og tredjepart som **signatur**.  Segmentet signatur kan bruges til at validere autenticiteten af id_token, så det kan være tillid til på din app.

Id_Tokens er signeret ved hjælp af branche standard asymmetrisk krypteringsalgoritmer, som RSA 256. Overskriften for id_token indeholder oplysninger om metoden nøgle og kryptering bruges til at signere tokenet:

```
{
  "typ": "JWT",
  "alg": "RS256",
  "x5t": "kriMPdmBvx68skT8-mPAB3BseeA"
}
```

Den `alg` krav angiver den algoritme, der blev brugt til at signere adgangstoken, mens den `x5t` krav angiver den bestemt offentlig nøgle, der blev brugt til at signere tokenet.

Azure AD kan Log en id_token ved hjælp af en af et bestemt sæt af offentlige og private nøgler par på et bestemt tidspunkt i gang. Azure AD roterer den mulige sæt nøgler med jævne mellemrum, så din app der skal skrives til at håndtere de vigtigste ændringer automatisk.  En begrundet frekvens for at søge efter opdateringer til de offentlige nøgler, der bruges af Azure AD er døgnet.

Du kan få fat på den signerende vigtige data til at validere signaturen ved hjælp af metadata OpenID forbinde dokumentet findes på:

```
https://login.microsoftonline.com/common/.well-known/openid-configuration
```

> [AZURE.TIP] Prøv denne URL-adresse i en browser!

Metadatadokumentet er et JSON-objekt, der indeholder flere stykker nyttige oplysninger som placeringen af de forskellige slutpunkter, der kræves for at udføre OpenID forbinde godkendelse.  

Den indeholder også en `jwks_uri`, der giver placeringen af sæt af offentlige nøgler, der bruges til at signere tokens.  JSON dokumentet findes i den `jwks_uri` indeholder alle de offentlige vigtige oplysninger i brug på den bestemt tidspunkt, hvor tidspunkt.  Din app kan bruge den `kid` gøre krav på hovedet JWT for at vælge, hvilke offentlig nøgle i dette dokument er blevet brugt til at signere et bestemt token.  Det kan udføre signaturen ved hjælp af den korrekte offentlige nøgle og den angivne algoritme.

Udføre signaturen er omfattet af dette dokument – der er mange Åbn kilde-biblioteker til, hvordan du gør dette, hvis det er nødvendigt.

#### <a name="validating-the-claims"></a>Validering af krav

Når din app modtager en id_token på bruger-logon, skal det også udføre nogle få kontrol over krav i id_token.  Dette omfatter, men er ikke begrænset til:

  - **Målgruppe** kravet - at bekræfte, at id_token blev beregnet tildeles til din app.
  - De **Ikke før** og **Udløbsdatoen** krav - at bekræfte, at id_token ikke er udløbet.
  - **Udsteder** kravet - at bekræfte, at Tokenet er udstedt faktisk til din app, af Azure AD.
  - **Nonce** - at reducere et token genafspilning angreb.
  - og meget mere...

Referere til [specifikation af OpenID oprette forbindelse](http://openid.net/specs/openid-connect-core-1_0.html#IDTokenValidation)til en komplet liste over krav valideringer din app skal udføre.

Oplysninger om de forventede værdier for disse krav er inkluderet i den foregående sektion [id_token sektion](#id-tokens) .

## <a name="sample-tokens"></a>Eksempel Tokens

I dette afsnit vises eksempler på SAML og JWT tokens, der returnerer Azure AD. Disse eksempler kan du se krav i kontekst.
SAML Token

Dette er et eksempel på et typisk SAML token.

    <?xml version="1.0" encoding="UTF-8"?>
    <t:RequestSecurityTokenResponse xmlns:t="http://schemas.xmlsoap.org/ws/2005/02/trust">
      <t:Lifetime>
        <wsu:Created xmlns:wsu="http://docs.oasis-open.org/wss/2004/01/oasis-200401-wss-wssecurity-utility-1.0.xsd">2014-12-24T05:15:47.060Z</wsu:Created>
        <wsu:Expires xmlns:wsu="http://docs.oasis-open.org/wss/2004/01/oasis-200401-wss-wssecurity-utility-1.0.xsd">2014-12-24T06:15:47.060Z</wsu:Expires>
      </t:Lifetime>
      <wsp:AppliesTo xmlns:wsp="http://schemas.xmlsoap.org/ws/2004/09/policy">
        <EndpointReference xmlns="http://www.w3.org/2005/08/addressing">
          <Address>https://contoso.onmicrosoft.com/MyWebApp</Address>
        </EndpointReference>
      </wsp:AppliesTo>
      <t:RequestedSecurityToken>
        <Assertion xmlns="urn:oasis:names:tc:SAML:2.0:assertion" ID="_3ef08993-846b-41de-99df-b7f3ff77671b" IssueInstant="2014-12-24T05:20:47.060Z" Version="2.0">
          <Issuer>https://sts.windows.net/b9411234-09af-49c2-b0c3-653adc1f376e/</Issuer>
          <ds:Signature xmlns:ds="http://www.w3.org/2000/09/xmldsig#">
            <ds:SignedInfo>
              <ds:CanonicalizationMethod Algorithm="http://www.w3.org/2001/10/xml-exc-c14n#" />
              <ds:SignatureMethod Algorithm="http://www.w3.org/2001/04/xmldsig-more#rsa-sha256" />
              <ds:Reference URI="#_3ef08993-846b-41de-99df-b7f3ff77671b">
                <ds:Transforms>
                  <ds:Transform Algorithm="http://www.w3.org/2000/09/xmldsig#enveloped-signature" />
                  <ds:Transform Algorithm="http://www.w3.org/2001/10/xml-exc-c14n#" />
                </ds:Transforms>
                <ds:DigestMethod Algorithm="http://www.w3.org/2001/04/xmlenc#sha256" />
                <ds:DigestValue>cV1J580U1pD24hEyGuAxrbtgROVyghCqI32UkER/nDY=</ds:DigestValue>
              </ds:Reference>
            </ds:SignedInfo>
            <ds:SignatureValue>j+zPf6mti8Rq4Kyw2NU2nnu0pbJU1z5bR/zDaKaO7FCTdmjUzAvIVfF8pspVR6CbzcYM3HOAmLhuWmBkAAk6qQUBmKsw+XlmF/pB/ivJFdgZSLrtlBs1P/WBV3t04x6fRW4FcIDzh8KhctzJZfS5wGCfYw95er7WJxJi0nU41d7j5HRDidBoXgP755jQu2ZER7wOYZr6ff+ha+/Aj3UMw+8ZtC+WCJC3yyENHDAnp2RfgdElJal68enn668fk8pBDjKDGzbNBO6qBgFPaBT65YvE/tkEmrUxdWkmUKv3y7JWzUYNMD9oUlut93UTyTAIGOs5fvP9ZfK2vNeMVJW7Xg==</ds:SignatureValue>
            <KeyInfo xmlns="http://www.w3.org/2000/09/xmldsig#">
              <X509Data>
                <X509Certificate>MIIDPjCCAabcAwIBAgIQsRiM0jheFZhKk49YD0SK1TAJBgUrDgMCHQUAMC0xKzApBgNVBAMTImFjY291bnRzLmFjY2Vzc2NvbnRyb2wud2luZG93cy5uZXQwHhcNMTQwMTAxMDcwMDAwWhcNMTYwMTAxMDcwMDAwWjAtMSswKQYDVQQDEyJhY2NvdW50cy5hY2Nlc3Njb250cm9sLndpbmRvd3MubmV0MIIBIjANBgkqhkiG9w0BAQEFAAOCAQ8AMIIBCgKCAQEAkSCWg6q9iYxvJE2NIhSyOiKvqoWCO2GFipgH0sTSAs5FalHQosk9ZNTztX0ywS/AHsBeQPqYygfYVJL6/EgzVuwRk5txr9e3n1uml94fLyq/AXbwo9yAduf4dCHTP8CWR1dnDR+Qnz/4PYlWVEuuHHONOw/blbfdMjhY+C/BYM2E3pRxbohBb3x//CfueV7ddz2LYiH3wjz0QS/7kjPiNCsXcNyKQEOTkbHFi3mu0u13SQwNddhcynd/GTgWN8A+6SN1r4hzpjFKFLbZnBt77ACSiYx+IHK4Mp+NaVEi5wQtSsjQtI++XsokxRDqYLwus1I1SihgbV/STTg5enufuwIDAQABo2IwYDBeBgNVHQEEVzBVgBDLebM6bK3BjWGqIBrBNFeNoS8wLTErMCkGA1UEAxMiYWNjb3VudHMuYWNjZXNzY29udHJvbC53aW5kb3dzLm5ldIIQsRiM0jheFZhKk49YD0SK1TAJBgUrDgMCHQUAA4IBAQCJ4JApryF77EKC4zF5bUaBLQHQ1PNtA1uMDbdNVGKCmSp8M65b8h0NwlIjGGGy/unK8P6jWFdm5IlZ0YPTOgzcRZguXDPj7ajyvlVEQ2K2ICvTYiRQqrOhEhZMSSZsTKXFVwNfW6ADDkN3bvVOVbtpty+nBY5UqnI7xbcoHLZ4wYD251uj5+lo13YLnsVrmQ16NCBYq2nQFNPuNJw6t3XUbwBHXpF46aLT1/eGf/7Xx6iy8yPJX4DyrpFTutDz882RWofGEO5t4Cw+zZg70dJ/hH/ODYRMorfXEW+8uKmXMKmX2wyxMKvfiPbTy5LmAU8Jvjs2tLg4rOBcXWLAIarZ</X509Certificate>
              </X509Data>
            </KeyInfo>
          </ds:Signature>
          <Subject>
            <NameID Format="urn:oasis:names:tc:SAML:2.0:nameid-format:persistent">m_H3naDei2LNxUmEcWd0BZlNi_jVET1pMLR6iQSuYmo</NameID>
            <SubjectConfirmation Method="urn:oasis:names:tc:SAML:2.0:cm:bearer" />
          </Subject>
          <Conditions NotBefore="2014-12-24T05:15:47.060Z" NotOnOrAfter="2014-12-24T06:15:47.060Z">
            <AudienceRestriction>
              <Audience>https://contoso.onmicrosoft.com/MyWebApp</Audience>
            </AudienceRestriction>
          </Conditions>
          <AttributeStatement>
            <Attribute Name="http://schemas.microsoft.com/identity/claims/objectidentifier">
              <AttributeValue>a1addde8-e4f9-4571-ad93-3059e3750d23</AttributeValue>
            </Attribute>
            <Attribute Name="http://schemas.microsoft.com/identity/claims/tenantid">
              <AttributeValue>b9411234-09af-49c2-b0c3-653adc1f376e</AttributeValue>
            </Attribute>
            <Attribute Name="http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name">
              <AttributeValue>sample.admin@contoso.onmicrosoft.com</AttributeValue>
            </Attribute>
            <Attribute Name="http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname">
              <AttributeValue>Admin</AttributeValue>
            </Attribute>
            <Attribute Name="http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname">
              <AttributeValue>Sample</AttributeValue>
            </Attribute>
            <Attribute Name="http://schemas.microsoft.com/ws/2008/06/identity/claims/groups">
              <AttributeValue>5581e43f-6096-41d4-8ffa-04e560bab39d</AttributeValue>
              <AttributeValue>07dd8a89-bf6d-4e81-8844-230b77145381</AttributeValue>
              <AttributeValue>0e129f4g-6b0a-4944-982d-f776000632af</AttributeValue>
              <AttributeValue>3ee07328-52ef-4739-a89b-109708c22fb5</AttributeValue>
              <AttributeValue>329k14b3-1851-4b94-947f-9a4dacb595f4</AttributeValue>
              <AttributeValue>6e32c650-9b0a-4491-b429-6c60d2ca9a42</AttributeValue>
              <AttributeValue>f3a169a7-9a58-4e8f-9d47-b70029v07424</AttributeValue>
              <AttributeValue>8e2c86b2-b1ad-476d-9574-544d155aa6ff</AttributeValue>
              <AttributeValue>1bf80264-ff24-4866-b22c-6212e5b9a847</AttributeValue>
              <AttributeValue>4075f9c3-072d-4c32-b542-03e6bc678f3e</AttributeValue>
              <AttributeValue>76f80527-f2cd-46f4-8c52-8jvd8bc749b1</AttributeValue>
              <AttributeValue>0ba31460-44d0-42b5-b90c-47b3fcc48e35</AttributeValue>
              <AttributeValue>edd41703-8652-4948-94a7-2d917bba7667</AttributeValue>
            </Attribute>
            <Attribute Name="http://schemas.microsoft.com/identity/claims/identityprovider">
              <AttributeValue>https://sts.windows.net/b9411234-09af-49c2-b0c3-653adc1f376e/</AttributeValue>
            </Attribute>
          </AttributeStatement>
          <AuthnStatement AuthnInstant="2014-12-23T18:51:11.000Z">
            <AuthnContext>
              <AuthnContextClassRef>urn:oasis:names:tc:SAML:2.0:ac:classes:Password</AuthnContextClassRef>
            </AuthnContext>
          </AuthnStatement>
        </Assertion>
      </t:RequestedSecurityToken>
      <t:RequestedAttachedReference>
        <SecurityTokenReference xmlns="http://docs.oasis-open.org/wss/2004/01/oasis-200401-wss-wssecurity-secext-1.0.xsd" xmlns:d3p1="http://docs.oasis-open.org/wss/oasis-wss-wssecurity-secext-1.1.xsd" d3p1:TokenType="http://docs.oasis-open.org/wss/oasis-wss-saml-token-profile-1.1#SAMLV2.0">
          <KeyIdentifier ValueType="http://docs.oasis-open.org/wss/oasis-wss-saml-token-profile-1.1#SAMLID">_3ef08993-846b-41de-99df-b7f3ff77671b</KeyIdentifier>
        </SecurityTokenReference>
      </t:RequestedAttachedReference>
      <t:RequestedUnattachedReference>
        <SecurityTokenReference xmlns="http://docs.oasis-open.org/wss/2004/01/oasis-200401-wss-wssecurity-secext-1.0.xsd" xmlns:d3p1="http://docs.oasis-open.org/wss/oasis-wss-wssecurity-secext-1.1.xsd" d3p1:TokenType="http://docs.oasis-open.org/wss/oasis-wss-saml-token-profile-1.1#SAMLV2.0">
          <KeyIdentifier ValueType="http://docs.oasis-open.org/wss/oasis-wss-saml-token-profile-1.1#SAMLID">_3ef08993-846b-41de-99df-b7f3ff77671b</KeyIdentifier>
        </SecurityTokenReference>
      </t:RequestedUnattachedReference>
      <t:TokenType>http://docs.oasis-open.org/wss/oasis-wss-saml-token-profile-1.1#SAMLV2.0</t:TokenType>
      <t:RequestType>http://schemas.xmlsoap.org/ws/2005/02/trust/Issue</t:RequestType>
      <t:KeyType>http://schemas.xmlsoap.org/ws/2005/05/identity/NoProofKey</t:KeyType>
    </t:RequestSecurityTokenResponse>

### <a name="jwt-token---user-impersonation"></a>JWT Token - brugerrepræsentation

Dette er et eksempel på et typisk JSON web token (JWT) bruges i en tilladelse kode Giv flow.
Ud over krav omfatter tokenet et versionsnummer i **ver** og **appidacr**, godkendelse kontekst klasse reference, som angiver, hvordan klienten er blevet godkendt. For en offentlig klient er værdien 0. Hvis en klient-ID og klienten hemmeligt blev brugt, er værdien 1.

    {
     typ: "JWT",
     alg: "RS256",
     x5t: "kriMPdmBvx68skT8-mPAB3BseeA"
    }.
    {
     aud: "https://contoso.onmicrosoft.com/scratchservice",
     iss: "https://sts.windows.net/b9411234-09af-49c2-b0c3-653adc1f376e/",
     iat: 1416968588,
     nbf: 1416968588,
     exp: 1416972488,
     ver: "1.0",
     tid: "b9411234-09af-49c2-b0c3-653adc1f376e",
     amr: [
      "pwd"
     ],
     roles: [
      "Admin"
     ],
     oid: "6526e123-0ff9-4fec-ae64-a8d5a77cf287",
     upn: "sample.user@contoso.onmicrosoft.com",
     unique_name: "sample.user@contoso.onmicrosoft.com",
     sub: "yf8C5e_VRkR1egGxJSDt5_olDFay6L5ilBA81hZhQEI",
     family_name: "User",
     given_name: "Sample",
     groups: [
      "0e129f6b-6b0a-4944-982d-f776000632af",
      "323b13b3-1851-4b94-947f-9a4dacb595f4",
      "6e32c250-9b0a-4491-b429-6c60d2ca9a42",
      "f3a161a7-9a58-4e8f-9d47-b70022a07424",
      "8d4c81b2-b1ad-476d-9574-544d155aa6ff",
      "1bf80164-ff24-4866-b19c-6212e5b9a847",
      "76f80127-f2cd-46f4-8c52-8edd8bc749b1",
      "0ba27160-44d0-42b5-b90c-47b3fcc48e35"
     ],
     appid: "b075ddef-0efa-123b-997b-de1337c29185",
     appidacr: "1",
     scp: "user_impersonation",
     acr: "1"
    }.

## <a name="related-content"></a>Relateret indhold
- Se Azure AD Graph [politik handlinger](https://msdn.microsoft.com/library/azure/ad/graph/api/policy-operations) og [politik enhed](https://msdn.microsoft.com/library/azure/ad/graph/api/entity-and-complex-type-reference#policy-entity)til at få mere for at vide om administration af sikkerhedstoken levetid politik via Azure AD Graph API.
- Flere oplysninger og eksempler på Administration af politikkerne for via PowerShell-cmdletter, herunder eksempler, under [Konfigurerbar token levetid i Azure AD](active-directory-configurable-token-lifetimes.md). 
