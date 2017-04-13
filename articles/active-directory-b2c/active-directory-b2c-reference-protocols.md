<properties
    pageTitle="Azure Active Directory B2C | Microsoft Azure"
    description="Sådan oprettes apps direkte ved hjælp af de protokoller, der understøttes af Azure Active Directory B2C."
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

# <a name="azure-ad-b2c-authentication-protocols"></a>Azure AD-B2C: Godkendelsesprotokoller

Azure Active Directory (Azure AD) B2C leverer identitet som en tjeneste til dine apps ved at understøtte to branche standard protokoller: OpenID forbinde og OAuth 2.0. Tjenesten er standarder-kompatibel, men en hvilken som helst to implementering af disse protokoller kan have mindre forskelle.  Oplysningerne i denne vejledning vil være nyttige for dig, hvis du skriver din kode, ved at sende direkte og håndtering af HTTP-anmodninger i stedet for ved hjælp af en Åbn datakilde. Vi anbefaler, at du læser denne side, før du kaste dig ud i oplysningerne om hver bestemt protokol. Men hvis du er bekendt med Azure AD B2C, kan du gå direkte til [protokollen reference hjælpelinjer](#protocols).

<!-- TODO: Need link to libraries above -->

## <a name="the-basics"></a>De grundlæggende funktioner
Hver app, der bruger Azure AD B2C skal være registreret i adresselisten B2C [Azure-portalen](https://portal.azure.com). Registreringsprocessen app indsamler og tildeler nogle få værdier til din app:

- Et **Program-ID** , der entydigt identificerer din app.
- En **Omdirigere URI** eller **pakke-id** , der kan bruges til at dirigere svar tilbage til din app.
- Et par andre scenarie-specifikke værdier. Yderligere oplysninger finder Lær [at registrere dit program](active-directory-b2c-app-registration.md).

Når du har registreret din app, kommunikerer den med Azure AD ved at sende anmodninger til v2.0 slutpunktet:

```
https://login.microsoftonline.com/common/oauth2/v2.0/authorize
https://login.microsoftonline.com/common/oauth2/v2.0/token
```

Fire parter er involveret i exchange i næsten alle OAuth og OpenID forbinde flyder:

![OAuth 2.0 roller](./media/active-directory-b2c-reference-protocols/protocols_roles.png)

- **Godkendelse server** er Azure AD v2.0 slutpunkt. Sikker heltal alt i forbindelse med brugeroplysninger og adgang. Den håndterer også tillidsforhold mellem parter i et flow. Det er ansvarlig for at bekræfte brugerens identitet, give tilbagekalde adgang til ressourcer og udsteder tokens. Det er også kendt som identitetsudbyder.
- **Ressource ejer** er typisk slutbrugerens. Det er den part, der ejer dataene, og det har mulighed for at tillade tredjepart mulighed for at få adgang til data- eller ressource.
- **OAuth-klienten** er din app. Det er identificeret med dens program-ID. Det er som regel den part, der slutbrugere interagere med. Det også anmodninger tokens fra serveren godkendelse. Ejeren af ressource skal give tilladelse til at få adgang til ressourcen klient.
- **Ressource server** er, hvor ressourcen eller data er placeret. Det har tillid til godkendelse serveren for at godkende sikker og godkende OAuth-klienten. Det bruges også bæreren access tokens til at sikre, at adgang til en ressource kan tildeles.

## <a name="policies"></a>Politikker
Nærmere, Azure AD B2C politikker er de vigtigste funktioner i tjenesten. Azure AD-B2C udvider de standard OAuth 2.0 og OpenID forbinde protokoller ved at introducere politikker. Disse indstillinger kan Azure AD B2C til at udføre meget mere end simple godkendelse og autorisation. Politikker beskriver fuldt consumer identitet oplevelser, herunder tilmeldingsprocessen logon og redigering af profil. Politikker kan defineres i en administrativ brugergrænseflade. De kan udføres ved hjælp af en speciel forespørgselsparameter i HTTP-godkendelse af anmodninger. Politikker er ikke standardfunktionerne i OAuth 2.0 og OpenID opretter forbindelse, så du skal bruge tid på at forstå disse. Du kan finde yderligere oplysninger finder [Azure AD B2C politik oversigtsvejledning](active-directory-b2c-reference-policies.md).

## <a name="tokens"></a>Tokens
Azure AD B2C implementeringen af OAuth 2.0 og OpenID forbinde gør omfattende brug af bæreren tokens, herunder bæreren tokens, der repræsenteres som JSON web tokens (JWTs). En bærertoken er et lette sikkerhedstoken, der giver adgang til en beskyttet ressource "bæreren". Bæreren er en hvilken som helst part, der kan præsentere tokenet. Azure AD skal først godkende fest, før den kan modtage en bærertokenet. Men hvis de nødvendige trin ikke er i secure token i overførslen og lager, det kan hentes og bruges af en utilsigtede part.

Nogle sikkerhedstokens har en indbygget funktion, der forhindrer uautoriserede personer i at bruge dem, men bæreren tokens har ikke denne funktion. De skal være transport på en sikker kanal, såsom transport lag sikkerhed (HTTPS). Hvis en bærertoken er sendt uden for en sikker kanal, kan den skadevoldende part bruge et mand i midten angreb til at købe tokenet og bruge det til at få uautoriseret adgang til en beskyttet ressource. De samme sikkerhedsprincipper anvende når bæreren tokens gemt eller cachelagret til senere brug. Altid sikre, at din app transmitterer og gemmer bæreren tokens på en sikker måde.

Du kan finde yderligere bæreren token sikkerhedsovervejelser [RFC 6750 trin 5](http://tools.ietf.org/html/rfc6750).

Yderligere oplysninger om de forskellige typer tokens, der bruges i Azure AD B2C er tilgængelige i [Azure AD token referencen](active-directory-b2c-reference-tokens.md).

## <a name="protocols"></a>Protokoller

Når du er klar til at gennemse nogle eksempel anmodninger, kan du starte med en af følgende selvstudier. Hver af dem svarer til et bestemt godkendelse scenarie. Hvis du har brug for hjælp til at bestemme, hvilke flow der passer til dine, se [typerne apps, du kan oprette ved hjælp af Azure AD B2C](active-directory-b2c-apps.md).

- [Opbygge mobil- og oprindelige programmer ved hjælp af OAuth 2.0](active-directory-b2c-reference-oauth-code.md)
- [Opbygge online ved hjælp af OpenID forbindelse](active-directory-b2c-reference-oidc.md)
