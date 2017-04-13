<properties
    pageTitle="Azure AD v2.0 Implicit Flow | Microsoft Azure"
    description="Bygning webprogrammer ved hjælp af Azure AD v2.0 implementering af implicitte strømmen til enkelt side-apps."
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

# <a name="v20-protocols---spas-using-the-implicit-flow"></a>v2.0 protokoller - kursteder ved hjælp af implicitte strømmen
Med v2.0 slutpunktet, kan du logge brugere på din enkelt side-apps med både personlige og arbejds-eller skolekonto konti fra Microsoft.  Enkelt side og andre JavaScript-apps, der kører primært i browseren ansigt nogle interessante udfordringer, når det drejer sig om godkendelse:

- Sikkerhed egenskaberne for disse apps er signifikant forskellige fra traditionelle serverbaserede webprogrammer.
- Mange godkendelse servere og identitetsudbydere understøtter ikke CORS anmodninger.
- Hele siden browseren omdirigerer væk fra den app, bliver særligt invasive til brugeroplevelsen.

Til disse programmer (tror: AngularJS, Ember.js, React.js, osv) Azure AD understøtter OAuth 2.0 Implicit Giv strømmen.  Implicit strømmen er beskrevet i [OAuth 2.0-specifikationen](http://tools.ietf.org/html/rfc6749#section-4.2).  Den primære fordel er, at den giver mulighed for appen til at hente tokens fra Azure AD uden at udføre en back end-serveren legitimationsoplysninger exchange.  Dette giver mulighed for appen til at logge på brugeren, opretholde session og få tokens til andre web API'er alt sammen i klienten JavaScript-kode.  Der er nogle vigtige sikkerhedsmæssige overvejelser at tage i betragtning, når du bruger implicit strømmen - særligt omkring [klienten](http://tools.ietf.org/html/rfc6749#section-10.3) og [brugerrepræsentation](http://tools.ietf.org/html/rfc6749#section-10.3).

Hvis du vil bruge implicit flow og Azure AD til at føje godkendelse til din JavaScript-app, anbefaler vi, du bruger vores Åbn kilde JavaScript-bibliotek, [adal.js](https://github.com/AzureAD/azure-activedirectory-library-for-js).  Der er nogle AngularJS selvstudier [her](active-directory-appmodel-v2-overview.md#getting-started) kan hjælpe dig med at komme i gang.  

Men hvis du foretrækker ikke at bruge et bibliotek i din app, enkelt side og sende protocol meddelelser til dig selv, skal du følge nedenstående generelle trin.

> [AZURE.NOTE]
    Ikke alle Azure Active Directory scenarier og funktioner, der understøttes af v2.0 slutpunkt.  For at bestemme, hvis du skal bruge v2.0 slutpunkt skal du læse om [v2.0 begrænsninger](active-directory-v2-limitations.md).
    
## <a name="protocol-diagram"></a>Protocol-diagram
Hele implicit logon flow ser nogenlunde således ud – alle trin er beskrevet i detaljeret nedenfor.

![OpenId forbinde svømmebaner](../media/active-directory-v2-flows/convergence_scenarios_implicit.png)

## <a name="send-the-sign-in-request"></a>Send anmodning-logon

For at begynde logger brugeren på din app, kan du sende en anmodning om [OpenID forbinde](active-directory-v2-protocols-oidc.md) godkendelse og få en `id_token` fra v2.0 slutpunktet:

```
// Line breaks for legibility only

https://login.microsoftonline.com/{tenant}/oauth2/v2.0/authorize?
client_id=6731de76-14a6-49ae-97bc-6eba6914391e
&response_type=id_token+token
&redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F
&scope=openid%20https%3A%2F%2Fgraph.microsoft.com%2Fmail.read
&response_mode=fragment
&state=12345
&nonce=678910
```

> [AZURE.TIP] Klik på linket nedenfor for at udføre denne anmodning! Når du logger på, skal din browser skal omdirigeres til `https://localhost/myapp/` med en `id_token` på adresselinjen.
    <a href="https://login.microsoftonline.com/common/oauth2/v2.0/authorize?client_id=6731de76-14a6-49ae-97bc-6eba6914391e&response_type=id_token+token&redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F&scope=openid%20https%3A%2F%2Fgraph.microsoft.com%2Fmail.read&response_mode=fragment&state=12345&nonce=678910" target="_blank">https://login.microsoftonline.com/common/oauth2/v2.0/Authorize...</a>

| Parameter | | Beskrivelse |
| ----------------------- | ------------------------------- | --------------- |
| lejer | påkrævet | Den `{tenant}` værdi i sti for din anmodning kan bruges til at styre, hvem der ikke kan logge på programmet.  De tilladte værdier er `common`, `organizations`, `consumers`, og lejer id'er.  Du kan finde flere detaljer, [protocol grundlæggende](active-directory-v2-protocols.md#endpoints). |
| client_id | påkrævet | Program-Id, portalen registrering ([apps.dev.microsoft.com](https://apps.dev.microsoft.com/?referrer=https://azure.microsoft.com/documentation/articles&deeplink=/appList)) tildelt din app. |
| response_type | påkrævet | Må indeholde `id_token` til OpenID forbinde logon.  Det kan også indeholde response_type `token`. Ved hjælp af `token` her har mulighed for din app til at modtage et adgangstoken umiddelbart fra godkende slutpunktet uden at sende en anden anmodning om at godkende slutpunktet.  Hvis du bruger den `token` response_type, den `scope` parameter skal indeholde et omfang, der angiver, hvilken ressource for at udstede token for. |
| redirect_uri | anbefalede | Redirect_uri af din app, hvor godkendelse svar kan sendes og modtages af din app.  Det skal svare nøjagtigt til en af de redirect_uris, der er registreret i portalen, bortset fra det skal være URL-kodes. |
| omfang | påkrævet | En adskilt af mellemrum liste over områder.  Det skal OpenID oprette forbindelse, skal du medtage omfanget `openid`, som oversættes til tilladelsen "Logge dig på i" i samtykke brugergrænseflade.  Du kan eventuelt også vil medtage den `email` eller `profile` [områder](active-directory-v2-scopes.md) for at få adgang til yderligere brugerdata.  Du kan også indeholde andre områder i denne anmodning om anmode om samtykke til forskellige ressourcer.  |
| response_mode | anbefalede | Angiver den metode, der skal bruges til at sende den resulterende token tilbage til din app.  Skal være `fragment` for implicit strømmen.  |
| stat | anbefalede | En værdi, der er inkluderet i anmodningen, der returneres i svaret token.  Det kan være en streng med det indhold, du ønsker.  En tilfældigt entydig værdi bruges typisk til [at forhindre på tværs af websteder anmodning forfalskning angreb](http://tools.ietf.org/html/rfc6749#section-10.12).  Tilstanden bruges også til at kode oplysninger om brugerens tilstand i appen, før der opstod anmodningen om godkendelse, som siden eller visningen, det var på. |
| Nonce | påkrævet | En værdi, der er inkluderet i anmodningen, genereres af den app, der skal medtages i den resulterende id_token som et krav.  Appen kan derefter kontrollere denne værdi for at reducere token genafspilningsangreb.  Værdien er typisk en randomiseret, entydigt streng, der kan bruges til at identificere origin for din anmodning.  |
| prompten | valgfri | Angiver typen brugerinteraktion, der er påkrævet.  På nuværende tidspunkt kun gyldige værdier er 'login', 'Ingen,' og 'samtykke'.  `prompt=login`Tvinger brugeren til at indtaste legitimationsoplysningerne på anmodningen, hvilket eliminerer enkelt Log på.  `prompt=none`er modsat - det sikrer, at brugeren ikke vises med en hvilken som helst interaktiv prompt ene eller den anden.  Hvis anmodningen ikke kan fuldføres uovervåget via single-sign-on, vil v2.0 slutpunktet returnere en fejl.  `prompt=consent`dialogboksen OAuth samtykke, udløses, når brugeren logger på, beder brugeren om at tildele tilladelser til appen. |
| login_hint | valgfri | Kan bruges til at udfylde udfyldt feltet brugernavn/e-mail-adresse i fremgangsmåden logge på siden for brugeren, hvis du kender deres brugernavn forvejen.  Ofte apps anvender denne parameter under ny godkendelse, har du allerede hentet brugernavnet fra en tidligere Log på ved hjælp af den `preferred_username` gøre krav. |
| domain_hint | valgfri | Kan være en af `consumers` eller `organizations`.  Hvis medtaget, vil det springe e-mail-baseret søgningen, der bruger, der er går igennem på siden v2.0 Log på, hvilket medfører en lidt mere strømlinet brugergrænseflade.  Ofte apps anvender denne parameter under godkendelse igen, ved at udtrække den `tid` gøre krav fra id_token.  Hvis den `tid` gøre krav på værdien er `9188040d-6c67-4c5b-b112-36a304b66dad`, skal du bruge `domain_hint=consumers`.  Ellers skal bruge `domain_hint=organizations`. |

På dette tidspunkt brugeren bliver bedt om angive deres legitimationsoplysninger og fuldføre godkendelsen.  V2.0 slutpunktet sikrer også, at brugeren har accepteret til de tilladelser, der er angivet i den `scope` forespørgsel parameter.  Hvis brugeren ikke har accepteret til en af disse tilladelser, vil det Bed brugeren om at samtykke til de nødvendige tilladelser.  Oplysninger om [tilladelser, accept, og med flere lejer apps er angivet her](active-directory-v2-scopes.md).

Når brugeren godkender og giver samtykke, v2.0 slutpunktet vil returnere et svar til din app på den angivne `redirect_uri`, ved hjælp af metoden angivet i den `response_mode` parameter.

#### <a name="successful-response"></a>Vellykket svar

En vellykket svar ved hjælp af `response_mode=fragment` og `response_type=id_token+token` ser ud som følgende med linjeskift for læsbarheden:

```
GET https://localhost/myapp/#
access_token=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...
&token_type=Bearer
&expires_in=3599
&scope=https%3a%2f%2fgraph.microsoft.com%2fmail.read 
&id_token=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...
&state=12345
```

| Parameter | Beskrivelse |
| ----------------------- | ------------------------------- |
| access_token | Inkluderet Hvis `response_type` omfatter `token`. Adgangstoken, appen har skrevet, i dette tilfælde til Microsoft Graph.  Adgangstoken ikke skal afkodes eller ellers kontrolleres, det kan behandles som en uigennemsigtig streng. |
| token_type | Inkluderet Hvis `response_type` omfatter `token`.  Altid være `Bearer`. |
| expires_in | Inkluderet Hvis `response_type` omfatter `token`.  Angiver antallet sekunder Tokenet er gyldige for cachelagring formål. |
| omfang | Inkluderet Hvis `response_type` omfatter `token`.  Angiver den scope(s), som access_token er gyldig. |
| id_token | Den id_token, der anmodes om en appen. Du kan bruge id_token til at kontrollere brugerens identitet og starte en session med brugeren.  Flere oplysninger om id_tokens og deres indhold er inkluderet i [v2.0 slutpunkt token reference](active-directory-v2-tokens.md).  |
| stat | Hvis en parameter for tilstand er medtaget i anmodningen, skal den samme værdi vises i svaret. APP'en skal kontrollere, at værdierne for tilstanden i anmodningen og svaret er ens. |


#### <a name="error-response"></a>Fejlsvar
Fejlsvar kan også blive sendt til den `redirect_uri` så appen kan håndtere dem korrekt:

```
GET https://localhost/myapp/#
error=access_denied
&error_description=the+user+canceled+the+authentication
```

| Parameter | Beskrivelse |
| ----------------------- | ------------------------------- |
| fejl | En streng fejlen kode, der kan bruges til at klassificere typer fejl, der opstår, og kan bruges til at reagere på fejl. |
| error_description | En bestemt fejlmeddelelse, der kan hjælpe en udvikler identificere den egentlige årsag til en godkendelsesfejl.  |

## <a name="validate-the-idtoken"></a>Validere id_token
Kun modtage en id_token er ikke tilstrækkelige til at godkende brugeren; Du skal kontrollere den id_token signatur og kontrollere krav i tokenet per krav til din app.  V2.0 slutpunktet bruger [JSON Web Tokens (JWTs)](http://self-issued.info/docs/draft-ietf-oauth-json-web-token.html) og kryptering med offentlig nøgle til at logge tokens og bekræfte, at de er gyldige.

Du kan vælge at validere det `id_token` i klienten kode, men almindelig praksis er at sende den `id_token` til en back end-serveren og udføre validering der.  Når du har valideret signaturen for id_token, er der et par krav du bliver bedt om at bekræfte.  Se [v2.0 token reference](active-directory-v2-tokens.md) til flere oplysninger, herunder [Validering af Tokens](active-directory-v2-tokens.md#validating-tokens) og [Vigtige oplysninger om signering nøgle overgang](active-directory-v2-tokens.md#validating-tokens).  Vi anbefaler gør brug af et bibliotek til analyse og validering af tokens – der er mindst én tilgængelig for de fleste sprog og platforme.
<!--TODO: Improve the information on this-->

Du kan også til at validere ekstra krav afhængigt af scenariet.  Nogle almindelige valideringer omfatter:

- Kontrollere, at brugeren/organisation har tilmeldt sig til appen.
- Kontrollere, at brugeren har stort godkendelse/rettigheder
- Kontrollere, at en bestemt styrken af godkendelse er opstået som Multi-Factor authentication.

Du kan finde flere oplysninger om krav i en id_token, [v2.0 slutpunkt token reference](active-directory-v2-tokens.md).

Når du har helt godkendt id_token, kan du starte en session med brugeren og bruge krav i id_token vil have oplysninger om brugeren i din app.  Disse oplysninger kan bruges til visning, poster, tilladelser, osv.

## <a name="get-access-tokens"></a>Få access tokens

Nu hvor du har logget brugeren på din enkelt side-app, kan du få access tokens for opkald web API'er sikret med Azure AD, som [Microsoft Graph](https://graph.microsoft.io).  Selvom du allerede har modtaget en token ved hjælp af den `token` response_type, kan du bruge denne metode til at hente tokens til yderligere ressourcer uden at omdirigere brugeren til at logge på igen.

I normal OpenID forbinde/OAuth strømmen, skal du gøre dette ved at oprette en anmodning til v2.0 `/token` slutpunkt.  V2.0 slutpunktet understøtter dog ikke CORS anmodninger, så opkald AJAX at hente og opdatere tokens er uden for spørgsmålet.  Du kan i stedet bruge implicit strømmen i en skjult iframe for at hente nye tokens til andre web API'er: 

```
// Line breaks for legibility only

https://login.microsoftonline.com/{tenant}/oauth2/v2.0/authorize?
client_id=6731de76-14a6-49ae-97bc-6eba6914391e
&response_type=token
&redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F
&scope=https%3A%2F%2Fgraph.microsoft.com%2Fmail.read&response_mode=fragment
&state=12345&nonce=678910
&prompt=none
&domain_hint=organizations
&login_hint=myuser@mycompany.com
```

> [AZURE.TIP] Prøve at kopiere og indsætte den under anmodning om til en fane i browseren! (Husk at erstatte den `domain_hint` og `login_hint` værdier med de korrekte værdier for din bruger)

```
https://login.microsoftonline.com/common/oauth2/v2.0/authorize?client_id=6731de76-14a6-49ae-97bc-6eba6914391e&response_type=token&redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F&scope=https%3A%2F%2Fgraph.microsoft.com%2Fmail.read&response_mode=fragment&state=12345&nonce=678910&prompt=none&domain_hint={{consumers-or-organizations}}&login_hint={{your-username}}
```

| Parameter | | Beskrivelse |
| ----------------------- | ------------------------------- | --------------- |
| lejer | påkrævet | Den `{tenant}` værdi i sti for din anmodning kan bruges til at styre, hvem der ikke kan logge på programmet.  De tilladte værdier er `common`, `organizations`, `consumers`, og lejer id'er.  Du kan finde flere detaljer, [protocol grundlæggende](active-directory-v2-protocols.md#endpoints). |
| client_id | påkrævet | Program-Id, portalen registrering ([apps.dev.microsoft.com](https://apps.dev.microsoft.com/?referrer=https://azure.microsoft.com/documentation/articles&deeplink=/appList)) tildelt din app. |
| response_type | påkrævet | Må indeholde `id_token` til OpenID forbinde logon.  Den kan også omfatte andre response_types såsom `code`. |
| redirect_uri | anbefalede | Redirect_uri af din app, hvor godkendelse svar kan sendes og modtages af din app.  Det skal svare nøjagtigt til en af de redirect_uris, der er registreret i portalen, bortset fra det skal være URL-kodes. |
| omfang | påkrævet | En adskilt af mellemrum liste over områder.  Medtag alle [områder](active-directory-v2-scopes.md) du skal bruge til ressourcen af interesse for få tokens.  |
| response_mode | anbefalede | Angiver den metode, der skal bruges til at sende den resulterende token tilbage til din app.  Kan være en af `query`, `form_post`, eller `fragment`.  |
| stat | anbefalede | En værdi, der er inkluderet i anmodningen, der returneres i svaret token.  Det kan være en streng med det indhold, du ønsker.  En tilfældigt entydig værdi bruges typisk til at forhindre på tværs af websteder anmodning forfalskning angreb.  Tilstanden bruges også til at kode oplysninger om brugerens tilstand i appen, før der opstod anmodningen om godkendelse, som siden eller visningen, det var på. |
| Nonce | påkrævet | En værdi, der er inkluderet i anmodningen, genereres af den app, der skal medtages i den resulterende id_token som et krav.  Appen kan derefter kontrollere denne værdi for at reducere token genafspilningsangreb.  Værdien er typisk en randomiseret, entydigt streng, der kan bruges til at identificere origin for din anmodning.  |
| prompten | påkrævet | Du skal bruge for at opdatere og henter tokens i en skjult iframe, `prompt=none` at sikre, at iframe ikke hænger på logonsiden v2.0, og returnerer med det samme. |
| login_hint | påkrævet | Til opdatering og få tokens i en skjult iframe, skal du medtage brugernavnet for brugeren i dette tip for at skelne mellem flere forskellige sessioner, har brugeren på et bestemt tidspunkt. Du kan udtrække brugernavnet fra en tidligere Log på ved hjælp af den `preferred_username` gøre krav. |
| domain_hint | påkrævet | Kan være en af `consumers` eller `organizations`.  Til opdatering og få tokens i en skjult iframe, skal du medtage domain_hint i anmodningen.  Du skal udtrække den `tid` gøre krav fra id_token af en tidligere logon til at afgøre, hvilken værdi der skal bruges.  Hvis den `tid` gøre krav på værdien er `9188040d-6c67-4c5b-b112-36a304b66dad`, skal du bruge `domain_hint=consumers`.  Ellers skal bruge `domain_hint=organizations`. |

Takket den `prompt=none` parameter, denne anmodning bliver enten lykkes eller mislykkes med det samme og vende tilbage til dit program.  En vellykket svar sendes til din app på den angivne `redirect_uri`, ved hjælp af metoden angivet i den `response_mode` parameter.

#### <a name="successful-response"></a>Vellykket svar
En vellykket svar ved hjælp af `response_mode=fragment` ser således ud:

```
GET https://localhost/myapp/#
access_token=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...
&state=12345
&token_type=Bearer
&expires_in=3599
&scope=https%3A%2F%2Fgraph.windows.net%2Fdirectory.read
```

| Parameter | Beskrivelse |
| ----------------------- | ------------------------------- |
| access_token | Tokenet, der anmodes om en appen. |
| token_type | Altid være `Bearer`. |
| stat | Hvis en parameter for tilstand er medtaget i anmodningen, skal den samme værdi vises i svaret. APP'en skal kontrollere, at værdierne for tilstanden i anmodningen og svaret er ens. |
| expires_in | Hvor længe adgangstoken er gyldig (i sekunder). |
| omfang | De områder, der er gyldige for adgangstoken. |

#### <a name="error-response"></a>Fejlsvar
Fejlsvar kan også blive sendt til den `redirect_uri` så appen kan håndtere dem korrekt.  I tilfælde af `prompt=none`, vil være en forventede fejl:

```
GET https://localhost/myapp/#
error=user_authentication_required
&error_description=the+request+could+not+be+completed+silently
```

| Parameter | Beskrivelse |
| ----------------------- | ------------------------------- |
| fejl | En streng fejlen kode, der kan bruges til at klassificere typer fejl, der opstår, og kan bruges til at reagere på fejl. |
| error_description | En bestemt fejlmeddelelse, der kan hjælpe en udvikler identificere den egentlige årsag til en godkendelsesfejl.  |

Hvis du får vist denne fejl i iframe anmodningen, skal brugeren interaktivt logge på igen til at hente et nyt id.  Du kan vælge at håndtere dette tilfælde i den måde, som giver mening for dit program.

## <a name="refreshing-tokens"></a>Hvis du vil opdatere tokens

Begge `id_token`s og `access_token`s udløber, når et kort stykke tid, så din app skal være forberedt på opdatere disse tokens med jævne mellemrum.  For at opdatere begge typer token, du kan udføre den samme skjulte iframe-anmodning fra et sted over ved hjælp af den `prompt=none` parameter til at styre Azure AD funktionsmåde.  Hvis du vil modtage en ny `id_token`, skal du bruge `response_type=id_token` og `scope=openid`, samt en `nonce` parameter.


## <a name="send-a-sign-out-request"></a>Sende en log af anmodning

OpenIdConnect `end_session_endpoint` understøttes ikke i øjeblikket af v2.0 slutpunkt. Det betyder, at din app ikke kan sende en indkaldelse til v2.0 slutpunktet at afslutte en brugers session og Slet cookies, der er angivet af v2.0 slutpunkt.
For at logge en bruger ud af, skal kan din app blot afsluttes sin egen session med brugeren, og lad brugerens session med v2.0 slutpunkt i-intakte.  Næste gang brugeren forsøger at logge på, får de vist en "Vælg konto" side med deres aktivt logget på konti, der vises.
Brugeren kan vælge at logge af enhver konto, afslutte en session med v2.0 slutpunktet på den pågældende side.

<!--

When you wish to sign the user out of the  app, it is not sufficient to clear your app's cookies or otherwise end the session with the user.  You must also redirect the user to the v2.0 endpoint for sign out.  If you fail to do so, the user will be able to re-authenticate to your app without entering their credentials again, because they will have a valid single sign-on session with the v2.0 endpoint.

You can simply redirect the user to the `end_session_endpoint` listed in the OpenID Connect metadata document:

```
GET https://login.microsoftonline.com/common/oauth2/v2.0/logout?
post_logout_redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F
```

| Parameter | | Description |
| ----------------------- | ------------------------------- | ------------ |
| post_logout_redirect_uri | recommended | The URL which the user should be redirected to after successful logout.  If not included, the user will be shown a generic message by the v2.0 endpoint.  |

-->