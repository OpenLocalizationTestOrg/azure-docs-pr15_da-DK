<properties
    pageTitle="Brug af API Management-tjenesten til at generere HTTP-anmodninger"
    description="Lær at bruge politikker for anmodning og svar i administration af API til at ringe eksterne tjenester fra din API"
    services="api-management"
    documentationCenter=""
    authors="darrelmiller"
    manager="erikre"
    editor=""/>

<tags
    ms.service="api-management"
    ms.devlang="dotnet"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="na"
    ms.date="10/25/2016"
    ms.author="darrmi"/>


# <a name="using-external-services-from-the-azure-api-management-service"></a>Brug af eksterne tjenester fra Azure API Management-tjenesten

De politikker, der er tilgængelige i Azure API Management-tjenesten kan gøre en lang række nyttige arbejde, der er baseret på den indgående anmodning, udgående svar og grundlæggende konfigurationsoplysninger. Dog kan interagere med eksterne tjenester fra administration af API politikker åbnes mange flere muligheder.

Vi har tidligere har set, hvordan vi kan interagere med [Azure begivenhed Hub-tjeneste til logføring, overvågning og analyse](api-management-log-to-eventhub-sample.md). I denne artikel gennemgås vi politikker, der gør det muligt at interagere med en hvilken som helst eksterne HTTP baseret tjeneste. Disse politikker kan bruges til udløser remote begivenheder eller til at hente oplysninger, der skal bruges til at ændre den oprindelige og svar på nogle måde.

## <a name="send-one-way-request"></a>Send-en-måde-anmodning
Muligvis den nemmeste eksterne interaktion er fire og glemmer typografien for anmodning, der gør det muligt for en ekstern tjeneste have besked om en slags vigtige begivenhed. Vi kan bruge politikken kontrolelement flow `choose` til at registrere alle slags betingelse, vi er interesseret i, og derefter, hvis betingelsen er opfyldt, kan vi foretage en ekstern HTTP-anmodning ved hjælp af politikken [send-en-måde-anmodning](https://msdn.microsoft.com/library/azure/dn894085.aspx#SendOneWayRequest) . Det kan være en anmodning til et meddelelsessystem som Hipchat eller slæk eller en mail API som SendGrid eller MailChimp, eller for kritiske supportopkald noget i stil PagerDuty. Alle disse SMS systemer har enkel HTTP APIs, som vi nemt kan aktivere.

### <a name="alerting-with-slack"></a>Advarer med slæk
I følgende eksempel viser, hvordan du kan sende en meddelelse til en slæk chatrum, hvis HTTP-svar statuskode er større end eller lig med 500. Fejlen 500 område angiver et problem med vores back end-API, klienten af vores API ikke identificere sig selv. Det kræver normalt en slags brugerinput på vores del.  

    <choose>
        <when condition="@(context.Response.StatusCode >= 500)">
          <send-one-way-request mode="new">
            <set-url>https://hooks.slack.com/services/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</set-url>
            <set-method>POST</set-method>
            <set-body>@{
                    return new JObject(
                            new JProperty("username","APIM Alert"),
                            new JProperty("icon_emoji", ":ghost:"),
                            new JProperty("text", String.Format("{0} {1}\nHost: {2}\n{3} {4}\n User: {5}",
                                                    context.Request.Method,
                                                    context.Request.Url.Path + context.Request.Url.QueryString,
                                                    context.Request.Url.Host,
                                                    context.Response.StatusCode,
                                                    context.Response.StatusReason,
                                                    context.User.Email
                                                    ))
                            ).ToString();
                }</set-body>
          </send-one-way-request>
        </when>
    </choose>

Slæk har begrebet indgående web kroge. Når du konfigurerer en indgående web krog, genererer slæk en speciel URL, som gør det muligt at oprette en enkelt POST-anmodning og til at sende en meddelelse til slæk kanalen. JSON brødteksten, som vi opretter er baseret på et format, der er defineret af slæk.

![Slæk Web krog](./media/api-management-sample-send-request/api-management-slack-webhook.png)

### <a name="is-fire-and-forget-good-enough"></a>Er fire og glemmer godt nok?
Der er visse kompromiserne, når du bruger en brand og glemmer typografi for anmodning. Hvis en eller anden grund, mislykkes, anmodningen og derefter fejlen rapporteres ikke. I denne særlige situation er kompleksitet har en sekundær mislykket rapportering system og yderligere ydeevne omkostninger for venter på svar ikke berettiget. For scenarier, hvor det er vigtigt at kontrollere svaret, derefter på [send anmodning](https://msdn.microsoft.com/library/azure/dn894085.aspx#SendRequest) politik er et bedre valg.

## <a name="send-request"></a>Send anmodning
Den `send-request` politik kan bruge en ekstern tjeneste til at udføre kompleks behandlingsfunktioner og returnere data til API management tjeneste, der kan bruges til yderligere behandling af politik.

### <a name="authorizing-reference-tokens"></a>Godkendelse af referencetokens
En overordnet funktion af API Management beskytter back end-ressourcer. Hvis godkendelse bruges af din API opretter serveren [JWT tokens](http://jwt.io/) som en del af OAuth2 flow, ligesom [Azure Active Directory](../active-directory/active-directory-aadconnect.md) , kan du bruge den `validate-jwt` politik for at bekræfte tokenet gyldighed. Dog oprette nogle godkendelse servere hvad kaldes [referencetokens](http://leastprivilege.com/2015/11/25/reference-tokens-and-introspection/) , som ikke kan bekræftes uden at foretage et opkald tilbage til godkendelse serveren.

### <a name="standardized-introspection"></a>Standardiseret introspection
Tidligere er der oprettet ingen standardiseret metode til bekræftelse af et referencetoken med en server til godkendelse. Men et senest foreslåede standard [RFC 7662](https://tools.ietf.org/html/rfc7662) blev udgivet af IETF, der definerer, hvordan en ressource server kan bekræfte en token gyldighed.

### <a name="extracting-the-token"></a>Udtrække tokenet
Det første trin er at udtrække tokenet fra overskriften godkendelse. Sidehoved værdien skal være formateret med den `Bearer` godkendelse farveskema, et enkelt mellemrum og derefter godkendelse tokenet ud fra [RFC 6750](http://tools.ietf.org/html/rfc6750#section-2.1). Der er desværre tilfælde, hvor godkendelse farveskema udelades. For at tage højde for dette, når parsing, vi opdele værdien sidehoved på et mellemrum og vælge den sidste streng fra den returnerede matrix af strenge. Dette giver en løsning for forkert formaterede godkendelse sidehoveder.

    <set-variable name="token" value="@(context.Request.Headers.GetValueOrDefault("Authorization","scheme param").Split(' ').Last())" />

### <a name="making-the-validation-request"></a>Foretage validering anmodningen
Når vi har tilladelsen tokenet, kan vi foretage anmodning om validering tokenet. RFC 7662 kalder denne proces introspection og kræver, at du `POST` en HTML-formular til introspection ressourcen. HTML-formularen skal mindst indeholde et tasten/værdi-par med tasten `token`. Denne anmodning til serveren godkendelse skal også godkendes for at sikre, at skadelig klienter ikke kan gå fiskeri for gyldige tokens.

    <send-request mode="new" response-variable-name="tokenstate" timeout="20" ignore-error="true">
      <set-url>https://microsoft-apiappec990ad4c76641c6aea22f566efc5a4e.azurewebsites.net/introspection</set-url>
      <set-method>POST</set-method>
      <set-header name="Authorization" exists-action="override">
        <value>basic dXNlcm5hbWU6cGFzc3dvcmQ=</value>
      </set-header>
      <set-header name="Content-Type" exists-action="override">
        <value>application/x-www-form-urlencoded</value>
      </set-header>
      <set-body>@($"token={(string)context.Variables["token"]}")</set-body>
    </send-request>

### <a name="checking-the-response"></a>Markere svaret
Den `response-variable-name` attribut bruges til at give adgang returnerede svaret. Det navn, der er defineret i denne egenskab kan bruges som en nøgle til den `context.Variables` ordbog til at få adgang til den `IResponse` objekt.

Vi kan hente brødteksten fra objektet svar og RFC 7622 fortæller os, svaret skal være et JSON-objekt og skal indeholde mindst en egenskab, der kaldes `active` , som er en boolesk værdi. Når `active` er sand, og derefter Tokenet er gyldige.

### <a name="reporting-failure"></a>Rapportering af fejl
Vi bruger en `<choose>` politik til at registrere Hvis Tokenet er ugyldige, og hvis det er tilfældet, skal returnere en 401 svar.

    <choose>
      <when condition="@((bool)((IResponse)context.Variables["tokenstate"]).Body.As<JObject>()["active"] == false)">
        <return-response response-variable-name="existing response variable">
          <set-status code="401" reason="Unauthorized" />
          <set-header name="WWW-Authenticate" exists-action="override">
            <value>Bearer error="invalid_token"</value>
          </set-header>
        </return-response>
      </when>
    </choose>

Ud fra [RFC 6750](https://tools.ietf.org/html/rfc6750#section-3) , som beskriver, hvordan `bearer` tokens skal bruges, vi også returnere en `WWW-Authenticate` sidehoved med 401 svaret. WWW-godkende er beregnet på at Fortæl en klient til, hvordan du oprette en korrekt autoriserede anmodning. På grund af en lang række af fremgangsmåder muligt med OAuth2, som er det svært at videresende alle de nødvendige oplysninger. Der er heldigvis anstrengelser foregår kan hjælpe [kunder Lær, hvordan du korrekt Godkend anmodninger om til en ressource-server](http://tools.ietf.org/html/draft-jones-oauth-discovery-00).

### <a name="final-solution"></a>Endelig løsning
Lægger alle elementerne sammen, skal få vi politikken følgende:

    <inbound>
      <!-- Extract Token from Authorization header parameter -->
      <set-variable name="token" value="@(context.Request.Headers.GetValueOrDefault("Authorization","scheme param").Split(' ').Last())" />

      <!-- Send request to Token Server to validate token (see RFC 7662) -->
      <send-request mode="new" response-variable-name="tokenstate" timeout="20" ignore-error="true">
        <set-url>https://microsoft-apiappec990ad4c76641c6aea22f566efc5a4e.azurewebsites.net/introspection</set-url>
        <set-method>POST</set-method>
        <set-header name="Authorization" exists-action="override">
          <value>basic dXNlcm5hbWU6cGFzc3dvcmQ=</value>
        </set-header>
        <set-header name="Content-Type" exists-action="override">
          <value>application/x-www-form-urlencoded</value>
        </set-header>
        <set-body>@($"token={(string)context.Variables["token"]}")</set-body>
      </send-request>

      <choose>
            <!-- Check active property in response -->
            <when condition="@((bool)((IResponse)context.Variables["tokenstate"]).Body.As<JObject>()["active"] == false)">
                <!-- Return 401 Unauthorized with http-problem payload -->
                <return-response response-variable-name="existing response variable">
                    <set-status code="401" reason="Unauthorized" />
                    <set-header name="WWW-Authenticate" exists-action="override">
                        <value>Bearer error="invalid_token"</value>
                    </set-header>
                </return-response>
            </when>
        </choose>
      <base />
    </inbound>

Dette er kun den ene af mange eksempler på, hvordan den `send-request` politik kan bruges til at integrere nyttige eksterne tjenester i processen med anmodninger om og svar, der flyder gennem API Management-tjenesten.

## <a name="response-composition"></a>Svar sammensætning
Den `send-request` politik kan bruges til at forbedre en primær anmodning til en back end-system, som vi har set i det forrige eksempel, eller den kan bruges som en komplet Erstat til back end-opkald. Ved hjælp af denne metode kan vi nemt oprette sammensatte ressourcer, der samles fra flere forskellige systemer.

### <a name="building-a-dashboard"></a>Opbygge et dashboard   
Undertiden vil du muligvis, at få vist oplysninger, som findes i flere back end-systemer, f.eks., at drive et dashboard. KPI'erne kommer fra alle forskellige back-udløber, men du foretrækker ikke at give direkte adgang til dem, og det ville være rart, hvis kunne hentes alle oplysninger i en enkelt anmodning. Nogle af back end-oplysninger skal måske nogle udsnit og terninger og lidt sanitizing først! Der kunne cache sammensatte ressourcen kan være en nyttige til at reducere back end-belastningen, som du ved, at brugere har en vane at hammering på F5 for at få vist, hvis deres underperforming målepunkter kan ændres.    

### <a name="faking-the-resource"></a>Faking ressourcen
Det første trin til at lave vores dashboard ressource er at konfigurere en ny handling i portalen API Management publisher. Dette er en pladsholder handling, der bruges til at konfigurere vores politik for sammensætning for at oprette vores dynamiske ressource.

![Dashboard-handling](./media/api-management-sample-send-request/api-management-dashboard-operation.png)

### <a name="making-the-requests"></a>Foretage anmodningerne
Én gang på `dashboard` handlingen er blevet oprettet vi kan konfigurere en politik specifikt for denne handling. 

![Dashboard-handling](./media/api-management-sample-send-request/api-management-dashboard-policy.png)

Det første trin er at udtrække en hvilken som helst forespørgselsparametre fra indgående anmodningen, så vi kan sende dem til vores backend-version. I dette eksempel er vores dashboard viser oplysninger, der er baseret på et bestemt tidsrum en derfor har en `fromDate` og `toDate` parameter. Vi kan bruge den `set-variable` politik om at hente oplysningerne fra URL-anmodningen.

    <set-variable name="fromDate" value="@(context.Request.Url.Query["fromDate"].Last())">
    <set-variable name="toDate" value="@(context.Request.Url.Query["toDate"].Last())">

Når vi har disse oplysninger kan vi foretage anmodninger om alle back end-systemerne. Hver enkelt anmodning, oprettes der en ny URL-adresse med parameteroplysninger og ringer dens respektive server og gemmer svaret i en kontekst variabel.

    <send-request mode="new" response-variable-name="revenuedata" timeout="20" ignore-error="true">
      <set-url>@($"https://accounting.acme.com/salesdata?from={(string)context.Variables["fromDate"]}&to={(string)context.Variables["fromDate"]}")"</set-url>
      <set-method>GET</set-method>
    </send-request>

    <send-request mode="new" response-variable-name="materialdata" timeout="20" ignore-error="true">
      <set-url>@($"https://inventory.acme.com/materiallevels?from={(string)context.Variables["fromDate"]}&to={(string)context.Variables["fromDate"]}")"</set-url>
      <set-method>GET</set-method>
    </send-request>

    <send-request mode="new" response-variable-name="throughputdata" timeout="20" ignore-error="true">
    <set-url>@($"https://production.acme.com/throughput?from={(string)context.Variables["fromDate"]}&to={(string)context.Variables["fromDate"]}")"</set-url>
      <set-method>GET</set-method>
    </send-request>

    <send-request mode="new" response-variable-name="accidentdata" timeout="20" ignore-error="true">
    <set-url>@($"https://production.acme.com/throughput?from={(string)context.Variables["fromDate"]}&to={(string)context.Variables["fromDate"]}")"</set-url>
      <set-method>GET</set-method>
    </send-request>

Disse anmodninger udføres i rækkefølge, som ikke er ideel. I en kommende version Vi introducerer en ny politik for kaldet `wait` , som giver alle disse anmodninger om at udføre parallelt.

### <a name="responding"></a>Svarer

For at oprette sammensatte svaret kan vi bruge politikken [retur-svar](https://msdn.microsoft.com/library/azure/dn894085.aspx#ReturnResponse) . Den `set-body` element kan bruge et udtryk til at oprette en ny `JObject` med alle de komponent, repræsentationer integreret som egenskaber.

    <return-response response-variable-name="existing response variable">
      <set-status code="200" reason="OK" />
      <set-header name="Content-Type" exists-action="override">
        <value>application/json</value>
      </set-header>
      <set-body>
        @(new JObject(new JProperty("revenuedata",((IResponse)context.Variables["revenuedata"]).Body.As<JObject>()),
                      new JProperty("materialdata",((IResponse)context.Variables["materialdata"]).Body.As<JObject>()),
                      new JProperty("throughputdata",((IResponse)context.Variables["throughputdata"]).Body.As<JObject>()),
                      new JProperty("accidentdata",((IResponse)context.Variables["accidentdata"]).Body.As<JObject>())
                      ).ToString())
      </set-body>
    </return-response>

Hele politikken ser ud som følger:

    <policies>
        <inbound>

      <set-variable name="fromDate" value="@(context.Request.Url.Query["fromDate"].Last())">
      <set-variable name="toDate" value="@(context.Request.Url.Query["toDate"].Last())">

        <send-request mode="new" response-variable-name="revenuedata" timeout="20" ignore-error="true">
          <set-url>@($"https://accounting.acme.com/salesdata?from={(string)context.Variables["fromDate"]}&to={(string)context.Variables["fromDate"]}")"</set-url>
          <set-method>GET</set-method>
        </send-request>

        <send-request mode="new" response-variable-name="materialdata" timeout="20" ignore-error="true">
          <set-url>@($"https://inventory.acme.com/materiallevels?from={(string)context.Variables["fromDate"]}&to={(string)context.Variables["fromDate"]}")"</set-url>
          <set-method>GET</set-method>
        </send-request>

        <send-request mode="new" response-variable-name="throughputdata" timeout="20" ignore-error="true">
        <set-url>@($"https://production.acme.com/throughput?from={(string)context.Variables["fromDate"]}&to={(string)context.Variables["fromDate"]}")"</set-url>
          <set-method>GET</set-method>
        </send-request>

        <send-request mode="new" response-variable-name="accidentdata" timeout="20" ignore-error="true">
        <set-url>@($"https://production.acme.com/throughput?from={(string)context.Variables["fromDate"]}&to={(string)context.Variables["fromDate"]}")"</set-url>
          <set-method>GET</set-method>
        </send-request>

        <return-response response-variable-name="existing response variable">
          <set-status code="200" reason="OK" />
          <set-header name="Content-Type" exists-action="override">
            <value>application/json</value>
          </set-header>
          <set-body>
            @(new JObject(new JProperty("revenuedata",((IResponse)context.Variables["revenuedata"]).Body.As<JObject>()),
                          new JProperty("materialdata",((IResponse)context.Variables["materialdata"]).Body.As<JObject>()),
                          new JProperty("throughputdata",((IResponse)context.Variables["throughputdata"]).Body.As<JObject>()),
                          new JProperty("accidentdata",((IResponse)context.Variables["accidentdata"]).Body.As<JObject>())
                          ).ToString())
          </set-body>
        </return-response>
        </inbound>
        <backend>
            <base />
        </backend>
        <outbound>
            <base />
        </outbound>
    </policies>

I konfigurationen af pladsholderen betyder handlingen vi kan konfigurere dashboard ressourcen cachelagres for mindst en time, fordi vi forstår, hvilke data, at hvis det er en time, der er forældet, vil den stadig være tilstrækkeligt effektive til at formidle nyttige oplysninger til brugerne.

## <a name="summary"></a>Oversigt
Azure API Management-tjenesten giver fleksible politikker, der kan anvendes selektivt på HTTP-trafik og aktiverer sammensætning af back end-tjenester. Om du vil forbedre din API genvej med advarer funktioner, kontrol, validering funktioner eller oprette nye sammensatte ressourcer, der er baseret på flere back end-tjenester på `send-request` og relaterede politikker åbne en verden af muligheder.

## <a name="watch-a-video-overview-of-these-policies"></a>Se en video oversigt over disse politikker
Flere oplysninger om de [send-en-måde-anmodning](https://msdn.microsoft.com/library/azure/dn894085.aspx#SendOneWayRequest), [send anmodning](https://msdn.microsoft.com/library/azure/dn894085.aspx#SendRequest)og [retur-svar](https://msdn.microsoft.com/library/azure/dn894085.aspx#ReturnResponse) politikker, der er dækket i denne artikel, skal du se følgende video.

> [AZURE.VIDEO send-request-and-return-response-policies]
