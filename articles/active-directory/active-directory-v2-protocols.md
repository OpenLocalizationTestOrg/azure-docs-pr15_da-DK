<properties
    pageTitle="Azure AD v2.0 protokoller | Microsoft Azure"
    description="En vejledning til protokoller, der understøttes af Azure AD v2.0 slutpunkt."
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

# <a name="v20-protocols---oauth-20--openid-connect"></a>v2.0 protokoller - OAuth 2.0 og OpenID forbindelse

V2.0 slutpunktet kan bruge Azure AD til identitet som-en-tjenesten med branche standard protokoller, OpenID forbinde og OAuth 2.0.  Mens tjenesten er standarder-kompatibel, kan der være mindre forskelle mellem en hvilken som helst to disse protokoller-installationer.  Oplysningerne her, vil være nyttigt, hvis du vælger at skrive din kode ved at sende direkte og håndtering af HTTP-anmodninger eller brug en 3 part Åbn datakilde i stedet for at bruge en af vores Åbn kilde-biblioteker.
<!-- TODO: Need link to libraries above -->

> [AZURE.NOTE]
    Ikke alle Azure Active Directory scenarier og funktioner, der understøttes af v2.0 slutpunkt.  For at bestemme, hvis du skal bruge v2.0 slutpunkt skal du læse om [v2.0 begrænsninger](active-directory-v2-limitations.md).

## <a name="the-basics"></a>De grundlæggende funktioner
I næsten alle OAuth & OpenID forbindelse flyder er der fire parter involveret i exchange:

![OAuth 2.0 roller](../media/active-directory-v2-flows/protocols_roles.png)

- **Godkendelse Server** er v2.0 slutpunkt.  Det er ansvarlig for at sikre det bruger-id, give tilbagekalde adgang til ressourcer og udsteder tokens.  Det er også kendt som identitetsudbyder - sikkert heltal det nogen indflydelse på oplysningerne i brugerens, deres access og tillidsforhold mellem parter i en flow.
- **Ressource ejer** er typisk slutbrugeren.  Det er den part, der ejer dataene, og har mulighed for at tillade tredjepart mulighed for at få adgang til denne data eller ressource.
- **OAuth klient** er din app, der er identificeret med dens program-id.  Det er som regel den part, der slutbrugeren interagerer med, og der anmodes om tokens fra serveren godkendelse.  Klienten skal have tildelt adgang til ressourcen ressource ejeren af.
- **Ressource Server** er, hvor ressourcen eller data er placeret.  Det har tillid til godkendelse serveren for at godkende sikker og godkende OAuth-klienten og bruger bæreren access_tokens til at sikre, at adgang til en ressource kan tildeles.


## <a name="app-registration"></a>App registrering
Hver app, der bruger v2.0 slutpunktet skal være registreret hos [apps.dev.microsoft.com](https://apps.dev.microsoft.com/?referrer=https://azure.microsoft.com/documentation/articles&deeplink=/appList) , før den kan interagere med OAuth eller OpenID forbindelse.  Registreringsprocessen app vil indsamling og tildele nogle få værdier til din app:

- Et **Program-Id** , der entydigt identificerer din app
- En **Omdirigere URI** eller **Pakke-id** , der kan bruges til at dirigere svar tilbage til din app
- Et par andre scenarie-specifikke værdier.

Få mere at vide Lær, hvordan du kan [registrere en app](active-directory-v2-app-registration.md).

## <a name="endpoints"></a>Slutpunkter
Når du er tilmeldt, kommunikerer appen med Azure AD ved at sende anmodninger til v2.0 slutpunktet:

```
https://login.microsoftonline.com/{tenant}/oauth2/v2.0/authorize
https://login.microsoftonline.com/{tenant}/oauth2/v2.0/token
```

Hvor den `{tenant}` kan tage en af fire forskellige værdier:

| Værdi | Beskrivelse |
| ----------------------- | ------------------------------- |
| `common` | Gør det muligt for brugere med både personlige Microsoft-konti og arbejds-eller skolekonto konti fra Azure Active Directory at logge på programmet. |
| `organizations` | Kan kun brugere med arbejds-eller skolekonto konti fra Azure Active Directory at logge på programmet. |
| `consumers` | Kan kun brugere med personlige Microsoft-konti (MSA) for at logge på programmet. |
| `8eaef023-2b34-4da1-9baa-8bc8c9d6a490`eller`contoso.onmicrosoft.com` | Kan kun brugere med arbejds-eller skolekonto konti fra en bestemt Azure Active Directory-lejer at logge på programmet.  Enten det fulde domænenavn for Azure AD-lejer eller lejerens guid-id kan bruges.  |

Flere oplysninger om hvordan du arbejder med disse slutpunkter, vælge en bestemt app typen nedenfor.

## <a name="tokens"></a>Tokens
V2.0 implementeringen af OAuth 2.0 og OpenID forbinde gør omfattende brug af bæreren tokens, herunder bæreren tokens repræsenteres som JWTs. En bærertoken er et lette sikkerhedstoken, der giver adgang til en beskyttet ressource "bæreren". I denne mening er "bæreren" Enhver part, der kan præsentere tokenet. Selvom fest skal først godkende med Azure AD modtage bærertoken, hvis de nødvendige trin ikke er i secure token i overførslen og lager, kan den opfange og bruges af en utilsigtede part. Mens nogle sikkerhedstokens har en indbygget ordning for at forhindre uautoriserede personer i at bruge dem, bæreren tokens har ikke denne funktion og skal være forsendelse i en sikker kanal som transport lag sikkerhed (HTTPS). Hvis en bærertoken er sendt i Ryd, kan en mand i den midterste angreb bruges af en skadelig part til at købe tokenet og bruge den til en uautoriseret adgang til en beskyttet ressource. De samme sikkerhedsprincipper anvende når lagring eller cachelagring bæreren tokens til senere brug. Altid sikre, at din app transmitterer og gemmer bæreren tokens på en sikker måde. Du kan finde flere overvejelser om sikkerheden på bæreren tokens, [RFC 6750 trin 5](http://tools.ietf.org/html/rfc6750).

Yderligere oplysninger om forskellige typer tokens, der bruges i v2.0 slutpunkt er tilgængelig i [v2.0 endpoint token reference](active-directory-v2-tokens.md).

## <a name="protocols"></a>Protokoller

Hvis du er klar til at se nogle eksempel anmodninger, komme i gang med en af de under selvstudier.  Hver af dem svarer til et bestemt godkendelse scenarie.  Hvis du har brug for hjælp til at bestemme, hvilket er det rigtige flow for dig, kan du se [typerne apps, du kan oprette med v2.0](active-directory-v2-flows.md).

- [Opbygge Mobile og oprindelige program med OAuth 2.0](active-directory-v2-protocols-oauth-code.md)
- [Opbygge Web Apps med Åbn ID forbindelse](active-directory-v2-protocols-oidc.md)
- [Opbygge enkelt side-Apps med OAuth 2.0 Implicit strømmen](active-directory-v2-protocols-implicit.md)
- [Build Daemons eller Server Side processer med OAuth 2.0-klienten legitimationsoplysninger dataflow](active-directory-v2-protocols-oauth-client-creds.md)
- Hente tokens i en Web API med den OAuth 2.0 på vegne af Flow (kommer snart)

<!-- - Get tokens using a username & password with the OAuth 2.0 Resource Owner Password Credentials Flow (coming soon) --> 
