<properties
    pageTitle="Azure AD v2.0 slutpunktet | Microsoft Azure"
    description="En sammenligning mellem den oprindelige Azure AD og v2.0 slutpunkterne."
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

# <a name="whats-different-about-the-v20-endpoint"></a>Hvad er anderledes om v2.0 slutpunktet?

Hvis du kender til Azure Active Directory eller har integreret apps med Azure AD tidligere, kan der være nogle forskelle i v2.0 slutpunktet, som du ikke ville forvente.  Dette dokument opkald ud af disse forskelle til forstået.

> [AZURE.NOTE]
    Ikke alle Azure Active Directory scenarier og funktioner, der understøttes af v2.0 slutpunkt.  For at bestemme, hvis du skal bruge v2.0 slutpunkt skal du læse om [v2.0 begrænsninger](active-directory-v2-limitations.md).


## <a name="microsoft-accounts-and-azure-ad-accounts"></a>Microsoft-konti og Azure AD-konti
v2.0 slutpunktet Tillad udviklere til at skrive apps, der accepterer logon fra både Microsoft Accounts og Azure AD-konti, ved hjælp af et enkelt auth slutpunkt.  Det giver dig mulighed for at skrive din app helt konto-agnostic; Det kan være ignorant af typen konto, som brugeren logger på med.  Du *kan* gøre din app opmærksom på typen konto, der bruges i en bestemt session naturligvis, men du behøver ikke at.

Eksempelvis hvis din app ringer til [Microsoft Graph](https://graph.microsoft.io), nogle ekstra funktioner og data vil være tilgængelige for enterprise-brugere, som deres SharePoint-websteder eller Directory-data.  Men for mange handlinger, som [læser en brugers mail](https://graph.microsoft.io/docs/api-reference/v1.0/resources/message), koden kan skrives præcis det samme for både Microsoft Accounts og Azure AD-konti.  

Integrering af din app med Microsoft Accounts og Azure AD er nu en enkel proces.  Du kan bruge en enkelt række slutpunkter, et enkelt bibliotek og en enkelt app registrering kan få adgang til både forbruger- og enterprise verdens.  Hvis du vil vide mere om v2.0 slutpunkt skal du se [oversigten](active-directory-appmodel-v2-overview.md).


## <a name="new-app-registration-portal"></a>Ny app registrering portal
v2.0 slutpunktet kan kun registreres i en ny placering: [apps.dev.microsoft.com](https://apps.dev.microsoft.com/?referrer=https://azure.microsoft.com/documentation/articles&deeplink=/appList).  Dette er på portalen, hvor du kan få et program-Id, tilpasse udseendet af din app logonside og meget mere.  Alt, du skal have adgang til portalen er en Microsoft drevet konto - personlige eller arbejde/skole-konto.  

Vi fortsætter med at føje flere og flere funktioner til denne App registrering Portal over tid.  Formålet er, at denne portal bliver den nye placering, hvor du kan gå til at administrere noget og alt skulle gøre med din Microsoft-apps.


## <a name="one-app-id-for-all-platforms"></a>Én app Id for alle platforme
I den oprindelige Azure Active Directory-tjeneste, kan du har registreret flere forskellige apps for et enkelt projekt.  Du er nødt til at bruge separat app registreringer til din oprindelige klienter og webapps:

![Gamle programmet registrering brugergrænseflade](../media/active-directory-v2-flows/old_app_registration.PNG)

Eksempelvis hvis du har oprettet et websteds- og en iOS-app, havde du til at registrere dem separat, ved hjælp af to forskellige program-id'er.  Hvis du har et websted og en back-end web api skal du muligvis har registreret hver som en separat app i Azure AD.  Hvis du har en iOS-app og en Android-app skal kan du også har registreret to forskellige apps.  

<!-- You may have even registered different apps for each of your build environments - one for dev, one for test, and one for production. -->

Nu skal du blot en enkelt app registrering og et enkelt program-Id for hver af dine projekter.  Du kan føje flere "platforme" til et hvert projekt, og Angiv de korrekte data for hver platform, du har tilføjet.  Du kan naturligvis oprette så mange apps, som du kan lide afhængigt af dine behov, men i fleste tilfælde kun ét program-Id skal være det er nødvendigt.

<!-- You can also label a particular platform as "production-ready" when it is ready to be published to the outside world, and use that same Application Id safely in your development environments. -->

Vores mål er, at dette skal føre til en mere forenklet app administration og udvikling oplevelse, og Opret en mere konsoliderede visning af et enkelt projekt, som du muligvis arbejder på.


## <a name="scopes-not-resources"></a>Områder, ikke ressourcer
I den oprindelige Azure AD-tjeneste, kan en app fungerer som en **ressource**eller en modtager af tokens.  En ressource kan definere et antal **områder** eller **oAuth2Permissions** , som den forstår, så klienten apps til at anmode om tokens til ressourcen for et bestemt sæt områder.  Overvej Azure AD Graph API som et eksempel på en ressource:

- Ressource-id eller `AppID URI`:`https://graph.windows.net/`
- Områder, eller `OAuth2Permissions`: `Directory.Read`, `Directory.Write`osv.  

Alt dette gør sig gældende for den v2.0 slutpunkt.  En app stadig kan fungere som ressource, definere områder og identificeres ved hjælp af en URI.  Klient-apps kan stadig anmode om adgang til disse områder.  Dog er den måde, som en klient, som disse tilladelser ændret.  Tidligere, en OAuth 2.0 Godkend anmodning om at Azure AD muligvis har så ud:

```
GET https://login.microsoftonline.com/common/oauth2/authorize?
client_id=2d4d11a2-f814-46a7-890a-274a72a7309e
&resource=https%3A%2F%2Fgraph.windows.net%2F
...
```

hvor angivet parameteren **ressource** , hvilken ressource appen klienten anmoder om tilladelse til.  Azure AD beregnes de tilladelser, der kræves, før den app, baseret på statisk konfiguration på portalen Azure og udstedt tokens i overensstemmelse hermed.  Nu Godkend den samme OAuth 2.0 anmodning om ser sådan ud:

```
GET https://login.microsoftonline.com/common/oauth2/v2.0/authorize?
client_id=2d4d11a2-f814-46a7-890a-274a72a7309e
&scope=https%3A%2F%2Fgraph.windows.net%2Fdirectory.read%20https%3A%2F%2Fgraph.windows.net%2Fdirectory.write
...
```

hvor parameteren **omfang** angiver, hvilken ressource og tilladelser på app anmoder om tilladelse til. Den ønskede ressource er stadig meget stede i indkaldelsen – det er blot encompassed i hver af værdierne for parameteren omfang.  Bruge parameteren omfang på denne måde kan v2.0 slutpunktet skal være mere kompatibel med OAuth 2.0-specifikationen, og justerer nærmere med almindelige branche fremgangsmåder.  Gør det også apps til at udføre [trinvise samtykke](#incremental-and-dynamic-consent), som er beskrevet i næste afsnit.

## <a name="incremental-and-dynamic-consent"></a>Stigende og dynamiske samtykke
Apps, der er registreret i alment tilgængelig Azure AD tjeneste, der er behov for at angive de nødvendige tilladelser til OAuth 2.0 i Azure-portalen på klokkeslættet for oprettelsen af app:

![Tilladelser registrering brugergrænseflade](../media/active-directory-v2-flows/app_reg_permissions.PNG)

De tilladelser, der er en påkrævet app er konfigureret **statisk**.  Mens dette tilladt konfigurationen af appen til findes i portalen Azure og bevares koden, flot og enkel, vises der et par problemer for udviklere:

- En app skulle du kender det hele af de tilladelser, den skulle få brug for på klokkeslættet for oprettelsen af app.  Tilføje rettigheder over tid blev vanskeligt.
- En app skulle du kende alle de ressourcer, det vil nogensinde få adgang til forvejen.  Det er svært at oprette apps, der kan få adgang til et vilkårligt antal ressourcer.
- En app havde til at anmode om alle de tilladelser, den skulle få brug for på brugerens første logon.  I nogle tilfælde ført dette til en meget lang liste over tilladelser, der ikke bør slutbrugere fra godkendelse af de app Åbn ved første logon.

Med v2.0 slutpunktet, kan du angive de tilladelser, din app skal **dynamisk**, på kørselstidspunktet, under almindelig brugen af din app.  Hvis du vil gøre det, kan du angive de områder, din app skal når som helst givet tidspunkt ved at medtage dem i den `scope` -parameter for anmodning om en tilladelse:

```
GET https://login.microsoftonline.com/common/oauth2/v2.0/authorize?
client_id=2d4d11a2-f814-46a7-890a-274a72a7309e
&scope=https%3A%2F%2Fgraph.windows.net%2Fdirectory.read%20https%3A%2F%2Fgraph.windows.net%2Fdirectory.write
...
```

Den ovenstående anmodninger om tilladelse til app til at læse en Azure AD-bruger directory data samt at skrive data til deres mappe.  Hvis brugeren har accepteret til disse tilladelser tidligere for denne bestemt app, skal du blot vil angive deres legitimationsoplysninger og være logget på appen.  Hvis brugeren ikke har accepteret til en af disse tilladelser, vil v2.0 slutpunktet Bed brugeren om samtykke til disse tilladelser.  Hvis du vil vide mere, kan du læse på [tilladelser, accept, og områder](active-directory-v2-scopes.md).

Tillade en app til at anmode om tilladelser dynamisk via den `scope` parameter giver dig fuld kontrol over dine brugeroplevelsen.  Hvis du vil, kan du vælge at frontload dit samtykke oplevelse og bede om alle tilladelser i en anmodning om indledende godkendelse.  Eller hvis din app kræver et stort antal tilladelser, du kan vælge at indsamle disse tilladelser fra brugeren trinvist, når de forsøger at bruge visse funktioner af din app over tid.

## <a name="well-known-scopes"></a>Kendte områder

#### <a name="offline-access"></a>Offline-adgang
v2.0 slutpunktet kan kræver brug af en ny kendte tilladelse til apps - den `offline_access` omfang.  Alle apps skal du anmoder om denne tilladelse, hvis de har brug at få adgang til ressourcer på vegne af en bruger til et længere stykke tid, selv når brugeren ikke kan være aktivt ved hjælp af appen.  Den `offline_access` omfang vises for brugeren i samtykke dialogbokse som "Få adgang til dine data offline", som brugeren skal acceptere.  Anmoder om den `offline_access` tilladelse gør det muligt for din online til at modtage OAuth 2.0 refresh_tokens fra v2.0 slutpunkt.  Refresh_tokens er langlivet, og kan blive udvekslet for nye OAuth 2.0 access_tokens for udvidede perioder for adgang.  

Hvis din app ikke anmoder om den `offline_access` omfang, det vil ikke modtage refresh_tokens.  Det betyder, at når du indløse en authorization_code i [OAuth 2.0 godkendelse kode flow](active-directory-v2-protocols.md#oauth2-authorization-code-flow), skal du kun får tilbage en access_token fra den `/token` slutpunkt.  Access_token gyldighed på et kort tidsrum (normalt en time), men udløber til sidst.  At, som skal tidspunkt, din app til at omdirigere brugeren tilbage til den `/authorize` slutpunkt til at hente en ny authorization_code.  Under denne omdirigering brugeren kan eller ikke skal muligvis angive deres legitimationsoplysninger igen eller igen samtykke til tilladelser, afhængigt af typen app.

Hvis du vil vide mere om OAuth 2.0, refresh_tokens og access_tokens, se [v2.0 protocol reference](active-directory-v2-protocols.md).

#### <a name="openid-profile--email"></a>OpenID, profil og mail

I den oprindelige Azure Active Directory-tjeneste giver den mest grundlæggende OpenID forbinde logon arbejdsgang et væld af oplysninger om brugeren i den resulterende id_token.  Krav i en id_token kan omfatte brugerens navn, foretrukne brugernavn, e-mailadresse, objekt-ID og mere.

Vi nu begrænse oplysninger, som den `openid` omfang giver din app adgang til.  'Openid' omfanget vil kun give din app til at logge brugeren i, og modtage en app-specifikke id for brugeren.  Hvis du vil hente personlige oplysninger (PII) om brugeren i din app, skal din app til at anmode om yderligere tilladelser fra brugeren.  Vi Introduktion til to nye områder – den `email` og `profile` områder – som gør det muligt at gøre dette.

Den `email` omfang er meget enkle – gør det din app adgang til brugerens primære mailadresse via den `email` gøre krav på id_token.  Den `profile` omfang giver din app adgang til alle andre grundlæggende oplysninger om brugeren – deres navn, foretrukne brugernavn, objekt-ID, og så videre.

Dette gør det muligt at kode din app på en minimale afsløre måde – du kan kun Bed brugeren om mængde oplysninger, der kræver din app til med databehandlingen.  Se [v2.0 omfang referencen](active-directory-v2-scopes.md)kan finde flere oplysninger om disse områder. 

## <a name="token-claims"></a>Token krav

Krav i tokens, der er udstedt af v2.0 slutpunktet vil ikke være identisk med tokens, der er udstedt af alment tilgængelig slutpunkter for Azure AD - apps overføre til den nye tjeneste skal ikke forudsætter et bestemt krav findes i id_tokens eller access_tokens.   Tokens, der er udstedt af v2.0 slutpunktet er kompatible med OAuth 2.0 og OpenID forbinde specifikationer, men kan følge forskellige semantik end alment tilgængelig Azure AD-tjenesten.

Se [v2.0 token reference](active-directory-v2-tokens.md)for at få mere for at vide om de specifikke krav udledes i v2.0 tokens.

## <a name="limitations"></a>Begrænsninger
Der findes nogle begrænsninger, der skal være opmærksom på, når du bruger v2.0 punktet.  Se [v2.0 begrænsninger dokument](active-directory-v2-limitations.md) til at se, hvis nogen af disse begrænsninger gælder for scenariet bestemt.
