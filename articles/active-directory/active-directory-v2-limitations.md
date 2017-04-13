<properties
    pageTitle="v2.0 slutpunkt begrænsninger og begrænsninger | Microsoft Azure"
    description="En liste over begrænsninger og begrænsninger i forbindelse med Azure AD v2.0 slutpunkt."
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

# <a name="should-i-use-the-v20-endpoint"></a>Skal jeg bruge v2.0 slutpunktet?

Når du opbygger programmer, der integreres med Azure Active Directory, skal du beslutte, om v2.0 slutpunkt og godkendelse protokoller opfylder dine behov.  Det oprindelige Azure AD-slutpunkt understøttes stadig fuldt og i nogle respekterer er flere funktion RTF end v2.0.  Men det v2.0 slutpunkt [introducerer betydelige fordele](active-directory-v2-compare.md) for udviklere, der kan få du kan bruge den nye programmering model.

På dette tidspunkt, som vores anbefaling bruges af v2.0 slutpunktet på følgende måde:

- Hvis du vil support til personlige Microsoft-konti i dit program, skal du bruge v2.0 slutpunkt.  Men Sørg for at forstå de begrænsninger, der er angivet i denne artikel, især for gruppepolitikken specifikt for at arbejde og skolekonti.
- Hvis programmet kræver kun understøttende arbejde og skolekonti, skal du bruge [de oprindelige Azure AD-slutpunkter](active-directory-developers-guide.md).

I tidens løb vokser v2.0 slutpunktet at undgå de begrænsninger, der er nævnt her, så du kun overhovedet skal bruge v2.0 slutpunkt.  I denne artikel henvender sig i mellemtiden kan hjælpe dig med at bestemme, om v2.0 slutpunktet er for dig.  Vi fortsætter med at opdatere i denne artikel over tid for at afspejle den aktuelle status for v2.0 slutpunktet, så kig tilbage for at Evaluer igen forhold v2.0 kapacitet til dine behov.

Hvis du har en eksisterende app med Azure AD, der ikke bruger v2.0 slutpunkt, er der ikke nødvendigt at starte fra bunden.  I fremtiden vil vil vi levere en metode til at aktivere dine eksisterende Azure AD-programmer til brug med v2.0 slutpunkt.

## <a name="restrictions-on-apps"></a>Begrænsninger på apps
Følgende typer apps understøttes ikke i øjeblikket af v2.0 slutpunkt.  Ved en beskrivelse af de understøttede typer apps skal du se [i denne artikel](active-directory-v2-flows.md).

##### <a name="standalone-web-apis"></a>Enkeltstående Web API'er
Med v2.0 slutpunktet har du mulighed for at [opbygge en Web API, der er beskyttet med OAuth 2.0](active-directory-v2-flows.md#web-apis).  Dog vil pågældende Web API kun kunne modtage tokens fra et program, der deler de samme program-id.  Opbygning af en web API, der åbnes fra en klient med et andet program-Id understøttes ikke.  Denne klient vil ikke kunne anmode om eller få tilladelser til webstedet API.

For at se, hvordan du opretter et Web API, der accepterer tokens ud fra en klient med det samme App-Id, skal du se v2.0 slutpunkt Web API eksempler [Komme](active-directory-appmodel-v2-overview.md#getting-started)i gang.

##### <a name="web-api-on-behalf-of-flow"></a>Web API på vegne af Flow
Mange arkitekturer omfatter en Web API, der skal ringe til en anden efterfølgende Web API, begge sikret af v2.0 slutpunkt.  Dette scenario er fælles for oprindelige klienter, der har en Web API back-end, der kalder en Microsoft-onlinetjeneste eller en anden brugerdefineret indbygget web API, der understøtter Azure AD.

Dette scenarie kan understøttes ved hjælp af OAuth 2.0 Jwt bæreren legitimationsoplysninger ydelse, også kendt som On-Behalf-Of dataflow.  Men strømmen på vegne af-understøttes ikke i øjeblikket for v2.0 slutpunkt.  Se, hvordan dette flow fungerer i alment tilgængelig Azure AD tjeneste, skal du se [på vegne af kodeeksempel på GitHub](https://github.com/AzureADSamples/WebAPI-OnBehalfOf-DotNet).

## <a name="restrictions-on-app-registrations"></a>Begrænsninger på app registreringer
På dette tidspunkt, skal alle apps, vil du integrere systemet med v2.0 slutpunktet oprette en ny app registrering på [apps.dev.microsoft.com](https://apps.dev.microsoft.com/?referrer=https://azure.microsoft.com/documentation/articles&deeplink=/appList).  En hvilken som helst eksisterende Azure AD eller Microsoft-Account apps er ikke kompatibelt med v2.0 slutpunktet, og ikke nødvendigvis vil apps, der er registreret i en hvilken som helst portal ud over den nye App registrering Portal.  Vi vil ikke en metode til at aktivere eksisterende programmer til brug som en v2.0 app. På nuværende tidspunkt via, er der ingen migreringsstien til en app til v2.0 slutpunkt.

Apps, der er registreret i den nye App registrering Portal fungerer på samme måde, ikke mod det oprindelige Azure AD-godkendelse slutpunkt.  Du kan dog bruge apps, der er oprettet i portalen App registrering skal integreres korrekt med Microsoft-konto godkendelse slutpunktet, `https://login.live.com`.

Desuden har app registreringer, der er oprettet på [apps.dev.microsoft.com](https://apps.dev.microsoft.com/?referrer=https://azure.microsoft.com/documentation/articles&deeplink=/appList) på følgende advarsler:

- Egenskaben **hjemmesiden** , også kaldet **sign-on – URL-adressen** er ikke understøttet.  Uden en hjemmeside vises disse programmer ikke i panelet mine Office-Apps.
- Kun to app hemmeligheder er tilladt per program-Id på nuværende tidspunkt.
- En app registrering kan kun vises og administreres af en enkelt udvikler-konto.  Det kan ikke deles mellem flere udviklere.
- Der findes flere begrænsninger i formatet af redirect_uri, der er tilladt.  Se afsnittet nedenfor for at få flere oplysninger.

## <a name="restrictions-on-redirect-uris"></a>Begrænsninger på Omdiriger URI'er
Apps, der er registreret i portalen til registrering af nye programmet er i øjeblikket er begrænset til et begrænset antal redirect_uri værdier.  Redirect_uri for webapps og tjenester skal starte med ordning `https`, og alle redirect_uri værdier skal dele et enkelt DNS-domæne.  For eksempel er det ikke muligt at registrere en WebApp, der indeholder redirect_uris:

`https://login-east.contoso.com`  
`https://login-west.contoso.com`

Registreringssystemet sammenligner på hele DNS-navnet på den eksisterende redirect_uri med DNS-navnet på den redirect_uri, du vil tilføje. Anmodning om at tilføje DNS-navnet mislykkes, hvis et af følgende betingelser er opfyldt:  

- Hvis hele DNS-navnet på den nye redirect_uri ikke svarer til DNS-navnet på den eksisterende redirect_uri
- Hvis hele DNS-navnet på den nye redirect_uri ikke er et underdomæne til den eksisterende redirect_uri

Hvis appen har redirect_uri f.eks.

`https://login.contoso.com`

Er det muligt at tilføje:

`https://login.contoso.com/new`

som nøjagtigt stemmer overens med navnet på DNS- eller:

`https://new.login.contoso.com`

som er en DNS-underdomæne til login.contoso.com.  Hvis du vil have en app, der har logon-east.contoso.com og logon-west.contoso.com som redirect_uris, og du skal tilføje følgende redirect_uris i rækkefølge:

`https://contoso.com`  
`https://login-east.contoso.com`  
`https://login-west.contoso.com`  

De sidste to kan tilføjes, fordi de er underdomæner for det første redirect_uri contoso.com. Denne begrænsning fjernes i en kommende version.

For at lære at registrere en app i den nye programmet registrering Portal, skal du henvise til [i denne artikel](active-directory-v2-app-registration.md).

## <a name="restrictions-on-services--apis"></a>Begrænsninger for tjenester og API'er
V2.0 slutpunktet aktuelt understøtter-logon til en app, der er registreret i den nye programmet registrering Portal, forudsat at de falder i listen over [understøttede godkendelsestyper flyder](active-directory-v2-flows.md).  Men disse apps kan kun få fat på OAuth 2.0 access tokens for et meget begrænset antal ressourcer.  V2.0 slutpunktet udsender kun access_tokens for:

- Den app, der anmodes om tokenet.  En app kan hente en access_token for sig selv, hvis de logiske app består af flere forskellige komponenter eller lag.  For at se dette scenario i handling skal du se vores [Introduktion](active-directory-appmodel-v2-overview.md#getting-started) selvstudier.
- Outlook Mail, kalender og kontakter REST API'er, som alle er placeret på https://outlook.office.com.  For at lære at skrive en app, der har adgang til disse API'er, skal du henvise til selvstudierne [Office Kom godt i gang](https://www.msdn.com/office/office365/howto/authenticate-Office-365-APIs-using-v2) .
- Microsoft Graph-API'er.  For at få mere for at vide om Microsoft Graph og alle de data, der er tilgængelig, skal du besøge [https://graph.microsoft.io](https://graph.microsoft.io).

Ingen andre tjenester, der understøttes på nuværende tidspunkt.  Flere Microsoft Online services tilføjes i fremtiden, samt understøttelse af din egen brugerdefinerede indbyggede Web API'er og -tjenester.

## <a name="restrictions-on-libraries--sdks"></a>Begrænsninger for biblioteker og SDK'er
Bibliotek understøttelse af v2.0 slutpunktet er relativt begrænset på nuværende tidspunkt.  Hvis du vil bruge v2.0 slutpunktet i et fremstilling til computeren, har du følgende indstillinger:

- Hvis du bygger et webprogram, kan du bruge vores alment tilgængelig serverbaseret programmer til at udføre logon og token validering på en sikker måde.  Dette omfatter de OWIN Åbn ID forbinde programmer til ASP.NET og vores NodeJS Passport-plug-in.  Kodeeksempler ved hjælp af vores program er tilgængelige i vores [Introduktion](active-directory-appmodel-v2-overview.md#getting-started) afsnit samt.
- Andre platforme og oprindelige og mobile-programmer, kan du også integrere med v2.0 slutpunktet ved at sende og modtage protocol meddelelser i din programkode direkte.  V2.0 OpenID forbinde og OAuth protokoller [har udtrykkeligt er dokumenteret](active-directory-v2-protocols.md) kan hjælpe dig med at udføre disse integration.
- Til sidst, kan du bruge Åbn kilde åbne forbinde-ID og OAuth biblioteker du integrere systemet med v2.0 slutpunkt.  V2.0 protokollen skal være kompatibelt med mange Åbn kilde protocol biblioteker uden større ændringer.  Tilgængeligheden af disse biblioteker varierer per sprog og platform, og [Åbn ID forbinde](http://openid.net/connect/) og [OAuth 2.0](http://oauth.net/2/) websteder vedligeholde en liste over populære installationer. Se [Azure Active Directory (AD) v2.0 og godkendelse biblioteker](active-directory-v2-libraries.md) til flere oplysninger og listen Åbn kilde klientbiblioteker og eksempler, der har reageret v2.0 slutpunkt.

Vi har også udgivet et indledende glimt af [Microsoft godkendelse bibliotek (MSAL)](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet) til .NET kun.  Du er Velkommen til at prøve dette bibliotek i .NET klient- og programmer, men som et preview-bibliotek, der er det ikke være sammen ved GA kvalitet understøtter.

## <a name="restrictions-on-protocols"></a>Begrænsninger for protokoller
V2.0 slutpunktet understøtter kun åbne ID forbinde & OAuth 2.0.  Men ikke alle funktioner og egenskaber for hver protokol er indsat i v2.0 slutpunktet, herunder:

- OpenID forbinde `end_session_endpoint`, som gør det muligt for en app til at afslutte brugerens session med v2.0 slutpunkt.
- id_tokens udstedt af v2.0 slutpunktet kun indeholde en parvis id for brugeren.  Det betyder, at to forskellige programmer modtager forskellige id'er for samme bruger.  Bemærk, at ved at forespørge Microsoft Graph `/me` slutpunkt, du vil kunne hente en correlatable ID for den bruger, der kan bruges på tværs af programmer.
- id_tokens udstedt af v2.0 slutpunktet ikke indeholder en `email` gøre krav til brugeren på nuværende tidspunkt, selvom du får fat på tilladelse fra brugeren til at se deres mail.
- Det OpenID forbinde brugeroplysninger slutpunkt. Brugeroplysninger slutpunktet er ikke implementeret på v2.0 slutpunktet på nuværende tidspunkt.  Profil af alle brugerdata modtog du potentielt på dette slutpunkt er dog tilgængelig fra Microsoft Graph `/me` slutpunkt.
- Rolle og gruppe krav.  V2.0 slutpunktet understøtter på nuværende tidspunkt ikke udstedende rolle eller gruppe krav i id_tokens.

Læs for bedre at forstå omfanget af protocol funktioner, der understøttes i v2.0 slutpunkt, ved hjælp af vores [OpenID Opret forbindelse & OAuth 2.0 Protocol Reference](active-directory-v2-protocols.md).

## <a name="restrictions-for-work--school-accounts"></a>Begrænsninger for Arbejd & skoleopgaver konti
Der er nogle funktioner, der er specifikke for Microsoft organisation/business-brugere, der endnu ikke understøttes af v2.0 slutpunkt.

##### <a name="device-based-conditional-access-native-and-mobile-apps-and-the-microsoft-graph"></a>Enhed-baserede betinget adgang, lokale og mobile-apps og Microsoft Graph
V2.0 slutpunktet understøtter ikke endnu enhed godkendelse til mobil- og oprindelige programmer, som kører på iOS eller Android-oprindelig apps.  Dette kan blokere din oprindelige program fra ringer til Microsoft Graph for visse organisationer.  Enhed godkendelse er påkrævet, når en administrator angiver en enhed-baserede betinget access-politik på et program.  For v2.0 slutpunktet er den mest sandsynlige scenarie for enhed-baserede betinget adgang til en administrator, angive en politik for en ressource i Microsoft Graph, som Outlook API.  Hvis en administrator indstiller denne politik, og din oprindelige program anmoder om et token til Microsoft Graph, mislykkes anmodningen i sidste ende, fordi enhed godkendelse ikke understøttes endnu.  Webprogrammer, der anmoder om tokens til Microsoft Graph, men understøttes, når enheden-baserede politikker er konfigureret.  I webdelen udføres app scenarie enhed godkendelse via brugerens webbrowser.

Som en udvikler har du højst sandsynligt ingen kontrol når politikker er angivet til Microsoft Graph ressourcer eller endda opmærksom på, når det sker.  Hvis du bygger et program for arbejds- og skoleopgaver-brugere, skal du bruge [det oprindelige Azure AD-slutpunkt](active-directory-developers-guide.md) , indtil v2.0 slutpunktet understøtter enhed godkendelse.  Se [denne artikel](active-directory-conditional-access.md#device-based-conditional-access)kan finde flere oplysninger om enhed-baserede betinget adgang.

##### <a name="windows-integrated-authentication-for-federated-tenants"></a>Windows-integreret godkendelse for organisationsnetværket lejere
Hvis du har brugt ADAL (og dermed det oprindelige Azure AD-slutpunkt) i Windows-programmer, at du har skiftet udnytte det, der kendes som SAML program Giv.  Denne bonus gør det muligt for brugere af organisationsnetværket Azure AD lejere til at godkende uovervåget med deres lokalt Active Directory-forekomst uden at skulle angive deres legitimationsoplysninger.  SAML program Giv understøttes ikke i øjeblikket på v2.0 slutpunktet.
