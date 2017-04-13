<properties
    pageTitle="Azure AD v2.0 områder, tilladelser og samtykke | Microsoft Azure"
    description="En beskrivelse af godkendelse i Azure AD v2.0 slutpunktet, herunder områder, tilladelser og samtykke."
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

# <a name="scopes-permissions--consent-in-the-v20-endpoint"></a>Områder, tilladelser og samtykke i slutpunktet v2.0

Apps, der integreres med Azure AD skal du følge en bestemt autorisation model, der gør det muligt for brugerne at styre, hvordan en app kan få adgang til deres data.  V2.0 implementeringen af denne tilladelse model er blevet opdateret, ændre, hvordan en app skal interagere med Azure AD.  I dette emne beskrives de grundlæggende begreber for denne tilladelse model, herunder områder, tilladelser og samtykke.

> [AZURE.NOTE]
    Ikke alle Azure Active Directory scenarier og funktioner, der understøttes af v2.0 slutpunkt.  For at bestemme, hvis du skal bruge v2.0 slutpunkt skal du læse om [v2.0 begrænsninger](active-directory-v2-limitations.md).

## <a name="scopes--permissions"></a>Områder og tilladelser

Azure AD implementerer [OAuth 2.0](active-directory-v2-protocols.md) godkendelse protokol, der er en metode til at tillade 3 en app fra en få adgang til web-hostet ressourcer på vegne af en bruger.  Alle web-hostet ressourcer, der integreres med Azure AD har en ressource-id eller **App ID URI**.  Nogle af Microsofts web-hostet ressourcer omfatter f.eks.:

- Office 365 Unified Mail API:`https://outlook.office.com`
- Azure AD-graf API:`https://graph.windows.net`
- Microsoft Graph:`https://graph.microsoft.com`

På samme måde gælder for en hvilken som helst 3 part ressourcer, der er integreret med Azure AD.  Nogen af disse ressourcer kan også angive et sæt tilladelser, der kan bruges til at opdele funktionaliteten i ressourcen i mindre stykker.  Microsoft Graph har defineret et par tilladelser som et eksempel:

- Læse en brugers kalender
- Skrive til en brugers kalender
- Send mail som en bruger
- [+ mere](https://graph.microsoft.io)

Ved at definere disse tilladelser, kan ressourcen har detaljerede kontrol over dataene, og hvordan det der vises til verden udenfor.  3 en app fra en kan derefter anmode om disse tilladelser fra en slutbruger - og slutbrugeren skal godkende tilladelserne, før app kan handle på deres vegne.  Ved at samle ressourcens funktionalitet i mindre tilladelsessæt, kan 3 part apps konfigureres for at anmode om kun de bestemte tilladelsesniveauer, som de skal bruge til at udføre deres opgave.  Det kan også slutbrugere mulighed for at vide præcis, hvordan en app skal bruge deres data, så de er mere sikker på, at app ikke fungerer med skadelig formål.

I Azure AD og OAuth, disse tilladelser er kendt som **områder**.  Du kan også få vist dem kaldes **oAuth2Permissions**.  Et omfang er repræsenteret i Azure AD som en strengværdi.  Vi fortsætter med Microsoft Graph-eksempel, er omfang værdien for hver tilladelse:

- Læse en brugers kalender:`Calendar.Read`
- Skrive til en brugers kalender:`Mail.ReadWrite`
- Send mail som en bruger:`Mail.Send`

En app kan anmode om disse tilladelser ved at angive områderne i anmodninger om til slutpunkt v2.0, som beskrevet nedenfor.

## <a name="openid-connect-scopes"></a>Oprette forbindelse OpenId områder

V2.0 implementeringen af OpenID Connect er et par klart defineret områder, der ikke gælder for en bestemt ressource - `openid`, `email`, `profile`, og `offline_access`.

#### <a name="openid"></a>OpenId

Hvis en app udfører Log på med [OpenID Opret forbindelse](active-directory-v2-protocols.md#openid-connect-sign-in-flow), skal den anmode den `openid` omfang.  Den `openid` omfang bliver vist i skærmbilledet arbejde konto samtykke som tilladelsen "Logge dig på i" og i personlige Microsoft-konto samtykke skærmen som tilladelsen "Få vist din profil og oprette forbindelse til apps og -tjenester med din Microsoft-konto".  Denne tilladelse giver en app til at modtage et entydigt id for brugeren i form af den `sub` gøre krav.  Det giver også app adgang til bruger oplysninger slutpunktet.  Den `openid` omfang kan også bruges på v2.0 token slutpunktet til at få fat på id_tokens, som kan bruges til at sikre HTTP opkald mellem forskellige komponenter i en app.

#### <a name="email"></a>Mail

Den `email` omfang kan medtages sammen med den `openid` omfang og eventuelle andre.  Det giver adgang til brugerens primære mailadresse i form af app på `email` gøre krav.  Den `email` krav vil kun blive medtaget i tokens, hvis en e-mailadresse er knyttet til den brugerkonto, som ikke er altid er tilfældet.  Hvis bruger den `email` omfang, din app skal være forberedt til at håndtere store og små bogstaver, hvor den `email` krav findes ikke i tokenet.

#### <a name="profile"></a>Profil

Den `profile` omfang kan medtages sammen med den `openid` omfang og eventuelle andre.  Det giver adgang til et væld af oplysninger om brugeren app.  Dette omfatter, men er ikke begrænset til brugerens fornavn, efternavn, foretrukne brugernavn, objekt-ID og osv.  Se [v2.0 token reference](active-directory-v2-tokens.md)til en komplet liste over de profil krav tilgængelige i id_tokens for en given bruger.

#### <a name="offlineaccess"></a>Offline_access

Den [ `offline_access` omfang](http://openid.net/specs/openid-connect-core-1_0.html#OfflineAccess) giver din app til at få adgang til ressourcer på vegne af brugeren for en længere periode.  På skærmbilledet arbejde konto samtykke vises søgeområdet som tilladelsen "Adgang til dine data når som helst".  I den personlige Microsoft-konto samtykke skærm vises den som tilladelsen "Få adgang til dine oplysninger når som helst".  Når en bruger godkender den `offline_access` omfang, aktiveres din app til at modtage Opdater tokens fra v2.0 token slutpunktet.  Opdatere tokens er langlivet og tillade din app til at hente nye access tokens, som ældre udløbe.

Hvis din app ikke anmoder om den `offline_access` omfang, det vil ikke modtage refresh_tokens.  Det betyder, at når du indløse en authorization_code i [OAuth 2.0 godkendelse kode flow](active-directory-v2-protocols.md#oauth2-authorization-code-flow), skal du kun får tilbage en access_token fra den `/token` slutpunkt.  Access_token gyldighed på et kort tidsrum (normalt en time), men udløber til sidst.  At, som skal tidspunkt, din app til at omdirigere brugeren tilbage til den `/authorize` slutpunkt til at hente en ny authorization_code.  Under denne omdirigering brugeren kan eller ikke skal muligvis angive deres legitimationsoplysninger igen eller igen samtykke til tilladelser, afhængigt af typen app.

Du kan finde flere oplysninger om, hvordan du kan hente og bruge opdatere tokens, skal du se [v2.0 protocol reference](active-directory-v2-protocols.md).


## <a name="requesting-individual-user-consent"></a>Anmode om individuel bruger samtykke

I en [OpenID kontakt eller OAuth 2.0](active-directory-v2-protocols.md) godkendelse anmodning en app kan anmode om de tilladelser, der skal bruges ved hjælp af den `scope` forespørgsel parameter.  Når en bruger logger ind i en app, vil appen for eksempel sende en indkaldelse til ud som følger (med linjeskift for læsbarhed):

```
GET https://login.microsoftonline.com/common/oauth2/v2.0/authorize?
client_id=6731de76-14a6-49ae-97bc-6eba6914391e
&response_type=code
&redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F
&response_mode=query
&scope=
https%3A%2F%2Fgraph.microsoft.com%2Fcalendar.read%20
https%3A%2F%2Fgraph.microsoft.com%2Fmail.send
&state=12345
```

Den `scope` parameter er en adskilt af mellemrum liste over områder, der anmoder om appen.  Hver enkelt omfang angives ved at føje værdien omfang til den ressource-id (App-ID URI).  Ovennævnte anmodning angiver, at APP'en skal have tilladelse til at læse brugerens kalender og sende mail som brugeren.

Når brugeren angiver deres legitimationsoplysninger, søger v2.0 slutpunktet efter en tilsvarende post af **brugeren samtykke**.  Hvis brugeren ikke har accepteret til en af de nødvendige tilladelser tidligere, vil v2.0 slutpunktet Bed brugeren om at give de nødvendige tilladelser.  

![Arbejde konto samtykke skærmbillede](../media/active-directory-v2-flows/work_account_consent.png)

Når brugeren godkender tilladelsen, registreres samtykke, så brugeren ikke har tilladelse igen på efterfølgende logon.

## <a name="requesting-consent-for-an-entire-tenant"></a>Anmode om samtykke for en hel lejer

Ofte, når en organisation køber en licens eller et andet abonnement til et program, vil de klargøre det fuldt ud for deres medarbejdere.  Som en del af denne proces, kan en virksomhedens administrator give samtykke for det pågældende program til at handle på vegne af en medarbejder.  Ved at give tilladelse til en hel lejer, oplever medarbejdere af den pågældende organisation ikke skærmbilledet samtykke for det pågældende program.

For at anmode om samtykke for alle brugere i en lejer, kan din app bruge den **administrator samtykke slutpunkt**, beskrevet nedenfor.

## <a name="admin-restricted-scopes"></a>Administrator-begrænsede områder

Visse høj rettighed tilladelser i Microsoft økosystemet kan markeres som **administrator-begrænsede**.  Eksempler på disse områder omfatter:

- Læse en organizaion directory data:`Directory.Read`
- Skrive data til en organisations bibliotek:`Directory.ReadWrite`
- Læse sikkerhedsgrupper i en organisations bibliotek:`Groups.Read.All`

Mens en forbruger bruger kan give et programadgang til sådanne data, er begrænset organisatoriske brugere fra at give adgang til det samme sæt følsomme firmadata.  Hvis dit program anmoder om adgang til en af disse tilladelser fra et organisatorisk bruger, modtager brugeren en fejlmeddelelse om, at de er ikke autoriseret til at samtykke til din app-tilladelser.

Hvis din app kræver adgang til disse admin-begrænsede områder for organisationer, skal du anmode om dem direkte fra en virksomhedsadministrator også bruger den **administrator samtykke slutpunkt**, beskrevet nedenfor.

Når administrator giver disse tilladelser via administrator samtykke slutpunkt, tildelt samtykke for alle brugere i lejeren, som beskrevet ovenfor.

## <a name="using-the-admin-consent-endpoint"></a>Ved hjælp af administrator samtykke slutpunktet

Ved at følge disse trin, vil din app kunne indsamle tilladelser for alle brugere i en given lejer, herunder administrator-begrænsede områder.  For at se et eksempel på-kode, der implementerer den nedenstående trin foranstående skal referere til [administrator begrænsede områder eksempel](https://github.com/Azure-Samples/active-directory-dotnet-admin-restricted-scopes-v2).

#### <a name="request-the-permissions-in-the-app-registration-portal"></a>Anmode om tilladelser i portalen app registrering

- Gå til dit program i [apps.dev.microsoft.com](https://apps.dev.microsoft.com/?referrer=https://azure.microsoft.com/documentation/articles&deeplink=/appList)eller [oprette en app](active-directory-v2-app-registration.md) , hvis du ikke allerede har.
- Find sektionen **Microsoft Graph tilladelser** , og Tilføj de tilladelser, der kræver, at din app.
- Sørg for at **gemme** app-registrering

#### <a name="recommended-sign-the-user-into-your-app"></a>Anbefalede: brugeren Log på din app

Når opbygge et program, der bruger administratoren samtykke slutpunkt, skal app typisk skal have en side/visning, der gør det muligt for administrator for at godkende på app-tilladelser.  Denne side kan være en del af den app tilmelding forløb, en del af indstillinger for den app eller et dedikeret "forbinde" flow.  I mange situationer giver det mening at vise dette App "visning af tilslutning", når en bruger har logget på med en arbejdet eller skolen Microsoft-konto.

Logger brugeren på app, kan du angive den organziation, som administratoren tilhører før bede dem om at godkende de nødvendige tilladelser.  Mens ikke absolut nødvendigt, kan det hjælpe dig med at oprette en mere intuitiv oplevelse for brugerne organisatoriske.  For at logge brugeren i skal du følge vores [v2.0 protocol selvstudier](active-directory-v2-protocols.md).

#### <a name="request-the-permissions-from-a-directory-admin"></a>Anmode om tilladelser fra en directory-administrator

Når du er klar til at anmode om tilladelser fra virksomhedens administrator, kan du omdirigere brugeren v2.0 **administrator samtykke slutpunkt**.

```
// Line breaks for legibility only

GET https://login.microsoftonline.com/{tenant}/adminconsent?
client_id=6731de76-14a6-49ae-97bc-6eba6914391e
&state=12345
&redirect_uri=http://localhost/myapp/permissions
```

```
// Pro Tip: Try pasting the below request in a browser!
```

```
https://login.microsoftonline.com/common/adminconsent?client_id=6731de76-14a6-49ae-97bc-6eba6914391e&state=12345&redirect_uri=http://localhost/myapp/permissions
```

| Parameter | | Beskrivelse |
| ----------------------- | ------------------------------- | --------------- |
| lejer | påkrævet | Directory lejeren, som du vil anmode om tilladelse fra.  Kan angives i formatet fulde navn eller guid. |
| client_id | påkrævet | Program-Id, portalen registrering ([apps.dev.microsoft.com](https://apps.dev.microsoft.com/?referrer=https://azure.microsoft.com/documentation/articles&deeplink=/appList)) tildelt din app. |
| redirect_uri | påkrævet | Den redirect_uri, hvor svaret skal sendes til din app til at håndtere.  Det skal svare nøjagtigt til en af de redirect_uris, der er registreret i portalen. |
| stat | anbefalede | En værdi, der er inkluderet i anmodningen, der returneres i svaret token.  Det kan være en streng med det indhold, du ønsker.  Tilstanden bruges til at kode oplysninger om brugerens tilstand i appen, før der opstod anmodningen om godkendelse, som siden eller visningen, det var på. |

På dette tidspunkt gennemtvinger Azure AD, kun lejeradministrator kan logge på at fuldføre anmodningen.  Administratoren vil blive bedt om at godkende alle de tilladelser, som du har anmodet om for din app i portalen registrering. 

##### <a name="successful-response"></a>Vellykket svar
Hvis administratoren godkender tilladelser for dit program, bliver vellykket svaret:

```
GET http://localhost/myapp/permissions?tenant=a8990e1f-ff32-408a-9f8e-78d3b9139b95&state=state=12345&admin_consent=True
```

| Parameter | Beskrivelse |
| ----------------------- | ------------------------------- | --------------- |
| lejer | Directory lejeren, der har givet dit program de tilladelser, den anmodet om, i guid-formatet. |
| stat | En værdi, der er inkluderet i anmodningen, der returneres i svaret token.  Det kan være en streng med det indhold, du ønsker.  Tilstanden bruges til at kode oplysninger om brugerens tilstand i appen, før der opstod anmodningen om godkendelse, som siden eller visningen, det var på. |
| admin_consent | Indstilles til `True`. |



##### <a name="error-response"></a>Fejlsvar
Hvis administratoren ikke godkender tilladelser for dit program, bliver mislykkedes svaret:

```
GET http://localhost/myapp/permissions?error=permission_denied&error_description=The+admin+canceled+the+request
```

| Parameter | Beskrivelse |
| ----------------------- | ------------------------------- | --------------- |
| fejl | En streng fejlen kode, der kan bruges til at klassificere typer fejl, der opstår, og kan bruges til at reagere på fejl. |
| error_description | En bestemt fejlmeddelelse, der kan hjælpe en udvikler identificere den egentlige årsag til en fejl.  |

Når du har modtaget en vellykket svar fra administrator samtykke slutpunkt, har din app fået de tilladelser, der anmodet om.  Nu kan du flytte til anmoder om et token for den ønskede ressource, som beskrevet nedenfor.

## <a name="using-permissions"></a>Ved hjælp af tilladelser

Når brugeren giver sit samtykke til tilladelser for din app, kan din app anskaffe access tokens, der repræsenterer din app tilladelse til at få adgang til en ressource i nogle kapacitet.  En given adgangstoken kan kun bruges til en enkelt resorce, men kodet indeni bliver alle tilladelser, som din app har fået tildelt til ressourcen.  For at få et adgangstoken, kan din app foretage en anmodning til v2.0 token slutpunktet:

```
POST common/oauth2/v2.0/token HTTP/1.1
Host: https://login.microsoftonline.com
Content-Type: application/json

{
    "grant_type": "authorization_code",
    "client_id": "6731de76-14a6-49ae-97bc-6eba6914391e",
    "scope": "https://outlook.office.com/mail.read https://outlook.office.com/mail.send",
    "code": "AwABAAAAvPM1KaPlrEqdFSBzjqfTGBCmLdgfSTLEMPGYuNHSUYBrq..."
    "redirect_uri": "https://localhost/myapp",
    "client_secret": "zc53fwe80980293klaj9823"  // NOTE: Only required for web apps
}
```

Den resulterende adgangstoken kan derefter bruges i HTTP-anmodninger ressourcen – den angiver pålideligt ressourcen, din app har den korrekte tilladelse til at udføre en bestemt opgave.  

Du kan finde flere detaljer om OAuth 2.0-protokollen og hvordan du får fat på access tokens, [v2.0 slutpunkt protocol reference](active-directory-v2-protocols.md).
