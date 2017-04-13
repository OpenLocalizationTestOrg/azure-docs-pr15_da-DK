<properties
    pageTitle="Brugerdefineret cachelagring i Azure API Management"
    description="Lær, hvordan du cache elementer med nøglen i Azure API Management"
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

# <a name="custom-caching-in-azure-api-management"></a>Brugerdefineret cachelagring i Azure API Management
Azure API Management-tjenesten har indbygget understøttelse af [HTTP-svar cachelagring](api-management-howto-cache.md) ved hjælp af ressource URL-adressen som nøgle. Tasten kan ændres af anmodning om sidehoveder ved hjælp af den `vary-by` egenskaber. Dette er nyttigt for cachelagring af hele HTTP-svar (også kendt repræsentationer), men nogle gange det er nyttigt at kun cachen en del af en repræsentation. De nye [cache opslagsværdi](https://msdn.microsoft.com/library/azure/dn894086.aspx#GetFromCacheByKey) og [cache-store-value](https://msdn.microsoft.com/library/azure/dn894086.aspx#StoreToCacheByKey) politikker giver mulighed for at gemme og hente vilkårlig dele af dataene i Politikdefinitioner. Denne mulighed tilføjer også værdi til politikken tidligere introduceret [send anmodning](https://msdn.microsoft.com/library/azure/dn894085.aspx#SendRequest) , fordi du kan nu cache svar fra eksterne services.

## <a name="architecture"></a>Arkitektur  
API Management-tjenesten bruges en delt per lejer datacache så, som du skalere op til flere enheder, du vil stadig få adgang til den samme cachelagrede data. Når du arbejder med en installation af flere områder er der uafhængige cache inden for hver af områderne. På grund af dette er det vigtigt at behandle cachen ikke som et datalager, hvor det er den eneste kilde til nogle oplysning. Hvis du har og senere besluttede dig for at drage fordel af flere områder installationen, kan derefter kunder med brugere, der rejser miste adgang til disse cachelagrede data.

## <a name="fragment-caching"></a>Fragment cache
Der er visse tilfælde, hvor svar, der returneres, indeholder en del af data, der koster til at bestemme og endnu altid er opdateret for et begrundet tidsrum. Som et eksempel, bør du overveje en tjeneste, der er oprettet af et flyselskab, som indeholder oplysninger om flight reservation, flight status osv. Hvis brugeren er medlem af programmet flyselskaber punkter, skal de også have oplysninger om deres aktuelle status og kørte kilometer akkumuleret. Disse brugerrelaterede oplysninger kan være gemt i et andet system, men det kan være nyttigt at inkludere den i svar returneres om flight status og reservation. Dette kan gøres ved hjælp af en proces, der kaldes fragment cachelagring. Den primære repræsentation kan returneres fra den oprindelige server ved hjælp af en slags tokenet til at angive, hvor de brugerrelaterede oplysninger der skal indsættes. 

Overvej følgende JSON svaret fra en back end-API.


    {
      "airline" : "Air Canada",
      "flightno" : "871",
      "status" : "ontime",
      "gate" : "B40",
      "terminal" : "2A",
      "userprofile" : "$userprofile$"
    }  

Og sekundær ressource på `/userprofile/{userid}` , ser ud,

     { "username" : "Bob Smith", "Status" : "Gold" }

For at finde ud af de relevante brugeroplysninger skal medtages, har vi brug at identificere, hvem der slutbrugerens er. Denne funktion er implementering afhængige. Jeg bruger som et eksempel på `Subject` gøre krav på en `JWT` token. 

    <set-variable
      name="enduserid"
      value="@(context.Request.Headers.GetValueOrDefault("Authorization","").Split(' ')[1].AsJwt()?.Subject)" />

Vi gemme dette `enduserid` værdi i en kontekst variabel til senere brug. Næste trin er at finde ud af, hvis en tidligere anmodning har allerede hentet brugeroplysningerne og gemmes i cachen. I dette vi bruger den `cache-lookup-value` politik.

      <cache-lookup-value
      key="@("userprofile-" + context.Variables["enduserid"])"
      variable-name="userprofile" />

Hvis der ikke er noget i cachen, der svarer til den nøgleværdi, så Nej `userprofile` kontekst variabel oprettes. Vi kontrollerer, om en vellykket opslag ved hjælp af den `choose` styre flow politik.

    <choose>
        <when condition="@(!context.Variables.ContainsKey("userprofile"))">
            <!— If the userprofile context variable doesn’t exist, make an HTTP request to retrieve it.  -->
        </when>
    </choose>


Hvis den `userprofile` kontekst variabel ikke findes, og klik derefter vi at foretage en HTTP-anmodning til at hente den.

    <send-request
      mode="new"
      response-variable-name="userprofileresponse"
      timeout="10"
      ignore-error="true">

      <!-- Build a URL that points to the profile for the current end-user -->
      <set-url>@(new Uri(new Uri("https://apimairlineapi.azurewebsites.net/UserProfile/"),
          (string)context.Variables["enduserid"]).AbsoluteUri)
      </set-url>
      <set-method>GET</set-method>
    </send-request>

Vi bruger den `enduserid` at opbygge URL-adressen til bruger profil ressourcen. Når vi har svaret, kan vi adskille brødteksten af svaret og gemme den i en kontekst variabel igen.

    <set-variable
        name="userprofile"
        value="@(((IResponse)context.Variables["userprofileresponse"]).Body.As<string>())" />

For at undgå os skulle gøre denne HTTP-anmodning igen, når den samme bruger foretager en anden anmodning, kan vi gemme brugerprofilen i cachen.

    <cache-store-value
        key="@("userprofile-" + context.Variables["enduserid"])"
        value="@((string)context.Variables["userprofile"])" duration="100000" />

Vi gemmer værdien i cachen ved hjælp af den nøjagtige samme nøgle, vi oprindeligt har forsøgt at hente den med. Den varighed, vi vælger at gemme værdien, der skal baseres på hvordan oplysninger ændringer og hvordan tolerant brugere ofte til forældede oplysninger. 

Det er vigtigt at huske, henter fra cachen er stadig en uden for proces, anmodning om netværk og kan potentielt stadig tilføje mange millisekunder i indkaldelsen. Fordelene kommer ved bestemmelse af brugerprofiloplysninger tager betydeligt længere tid end på grund af behøver at Webdatabase forespørgsler eller Sammenlæg oplysninger fra flere back-ender.

Det sidste trin i processen er at opdatere den returnerede svar med vores brugerprofiloplysninger.

    <!—Update response body with user profile-->
    <find-and-replace
        from='"$userprofile$"'
        to="@((string)context.Variables["userprofile"])" />

Jeg har valgt at medtage anførselstegnene som en del af tokenet, så selvom feltet Erstat ikke opstår, svaret var stadig er gyldig JSON. Det var primært at gøre fejlfinding nemmere.

Når du kombinere alle disse trin sammen, er en politik, der ligner den følgende resultat.

     <policies>
        <inbound>
            <!-- How you determine user identity is application dependent -->
            <set-variable
              name="enduserid"
              value="@(context.Request.Headers.GetValueOrDefault("Authorization","").Split(' ')[1].AsJwt()?.Subject)" />

            <!--Look for userprofile for this user in the cache -->
            <cache-lookup-value
              key="@("userprofile-" + context.Variables["enduserid"])"
              variable-name="userprofile" />

            <!-- If we don’t find it in the cache, make a request for it and store it -->
            <choose>
                <when condition="@(!context.Variables.ContainsKey("userprofile"))">
                    <!—Make HTTP request to get user profile -->
                    <send-request
                      mode="new"
                      response-variable-name="userprofileresponse"
                      timeout="10"
                      ignore-error="true">

                       <!-- Build a URL that points to the profile for the current end-user -->
                        <set-url>@(new Uri(new Uri("https://apimairlineapi.azurewebsites.net/UserProfile/"),(string)context.Variables["enduserid"]).AbsoluteUri)</set-url>
                        <set-method>GET</set-method>
                    </send-request>

                    <!—Store response body in context variable -->
                    <set-variable
                      name="userprofile"
                      value="@(((IResponse)context.Variables["userprofileresponse"]).Body.As<string>())" />

                    <!—Store result in cache -->
                    <cache-store-value
                      key="@("userprofile-" + context.Variables["enduserid"])"
                      value="@((string)context.Variables["userprofile"])"
                      duration="100000" />
                </when>
            </choose>
            <base />
        </inbound>
        <outbound>
            <!—Update response body with user profile-->
            <find-and-replace
                  from='"$userprofile$"'
                  to="@((string)context.Variables["userprofile"])" />
            <base />
        </outbound>
     </policies>

Denne cachelagring metode bruges primært på websteder, hvor HTML består på serversiden, så den kan gengives som en enkelt side. Det kan dog også være nyttigt i API'er, hvor klienter ikke kan gøre klient side HTTP cachelagring eller bør ikke at sætte ansvaret i klienten.

Denne samme slags fragment cachelagring kan også udføres af back end-webserverne ved hjælp af en Redis cachelagring server, men brug af API Management-tjenesten til at udføre dette arbejde er nyttig, når de cachelagrede fragmenter der kommer fra en anden back-ender end de primære svar.

## <a name="transparent-versioning"></a>Gennemsigtige versionsstyring
Det er almindelig praksis for flere forskellige implementering versioner af en API til understøttes på én gang. Dette er måske til at understøtte forskellige miljøer, som Udviklingscenter, test, fremstilling osv, eller det kan være til at understøtte ældre versioner af API til at give tid til forbrugere API til at overføre til nyere versioner. 

En metode til håndtering af dette i stedet for at kræve klient udviklere til at ændre URL-adresser fra `/v1/customers` til `/v2/customers` er at gemme i brugerens profildata, hvilken version af API de aktuelt ønsker at bruge, og kald den relevante back end-URL-adresse. For at bestemme den korrekte back end-URL-adresse til at ringe til en bestemt klient, er det nødvendigt at forespørge på nogle konfigurationsdata. Ved at cachelagre denne konfigurationsdata, kan vi minimere ydeevnen ved at gøre dette opslag.

Det første trin er at finde ud af det id, der bruges til at konfigurere den ønskede version. I dette eksempel, jeg har valgt at knytte version til abonnement produktnøglen. 

        <set-variable name="clientid" value="@(context.Subscription.Key)" />

Vi gør derefter et opslag i cachen for at se, hvis vi allerede har hentet ønskede klientversionen.

        <cache-lookup-value
        key="@("clientversion-" + context.Variables["clientid"])"
        variable-name="clientversion" />

Vi Markér derefter afkrydsningsfeltet for at se, hvis der ikke blev fundet den i cachen.

    <choose>
        <when condition="@(!context.Variables.ContainsKey("clientversion"))">

Hvis vi ikke derefter vi gå og hente den.

    <send-request
        mode="new"
        response-variable-name="clientconfiguresponse"
        timeout="10"
        ignore-error="true">
                <set-url>@(new Uri(new Uri(context.Api.ServiceUrl.ToString() + "api/ClientConfig/"),(string)context.Variables["clientid"]).AbsoluteUri)</set-url>
                <set-method>GET</set-method>
    </send-request>

Udtrække brødteksten svar fra svaret.

    <set-variable
          name="clientversion"
          value="@(((IResponse)context.Variables["clientconfiguresponse"]).Body.As<string>())" />

Gemme den igen i cachen til fremtidig brug.

    <cache-store-value
          key="@("clientversion-" + context.Variables["clientid"])"
          value="@((string)context.Variables["clientversion"])"
          duration="100000" />

Og til sidst opdatere back end-URL-adressen for at vælge versionen af tjenesten ønskes af klienten.

    <set-backend-service
          base-url="@(context.Api.ServiceUrl.ToString() + "api/" + (string)context.Variables["clientversion"] + "/")" />

Politikken helt er som følger.

     <inbound>
        <base />
        <set-variable name="clientid" value="@(context.Subscription.Key)" />
        <cache-lookup-value key="@("clientversion-" + context.Variables["clientid"])" variable-name="clientversion" />

        <!-- If we don’t find it in the cache, make a request for it and store it -->
        <choose>
            <when condition="@(!context.Variables.ContainsKey("clientversion"))">
                <send-request mode="new" response-variable-name="clientconfiguresponse" timeout="10" ignore-error="true">
                    <set-url>@(new Uri(new Uri(context.Api.ServiceUrl.ToString() + "api/ClientConfig/"),(string)context.Variables["clientid"]).AbsoluteUri)</set-url>
                    <set-method>GET</set-method>
                </send-request>
                <!-- Store response body in context variable -->
                <set-variable name="clientversion" value="@(((IResponse)context.Variables["clientconfiguresponse"]).Body.As<string>())" />
                <!-- Store result in cache -->
                <cache-store-value key="@("clientversion-" + context.Variables["clientid"])" value="@((string)context.Variables["clientversion"])" duration="100000" />
            </when>
        </choose>
        <set-backend-service base-url="@(context.Api.ServiceUrl.ToString() + "api/" + (string)context.Variables["clientversion"] + "/")" />
    </inbound>


Aktivere forbrugere af API til at styre, hvilken back end-version åbnes af klienter uden at skulle Opdater og geninstaller klienter er en elegant løsning, der løser mange API versionsstyring problemstillinger.

## <a name="tenant-isolation"></a>Lejer isolationsniveauet

I større, med flere lejer installationer oprette virksomheden separat grupper af lejere på forskellige installationer af back end-hardware. Derved minimeres antallet af kunder, der påvirkes af et hardwareproblem på back-end. Gør det også softwareversioner rulles i faser. Ideelt bør denne back end-arkitektur være gennemsigtigt til API forbrugere. Dette kan opnås på samme måde at gennemsigtig versionsstyring, fordi det er baseret på den samme teknik af manipulere back end-URL-adressen, der bruger konfigurationstilstand per API-nøgle.  

I stedet for at returnere en Foretrukne version af API til hver abonnement nøgle, skal du gå tilbage et id, som relaterer til en lejer til gruppen tildelte hardware. Pågældende id kan bruges til at oprette den relevante back end-URL-adresse.

## <a name="summary"></a>Oversigt
Bruge Azure API management cachen til lagring af alle slags data giver effektiv adgang til af konfigurationsdata, der kan påvirke den måde en indgående anmodning behandles. Det kan også bruges til at gemme datafragmenter, der kan øge svar, returneres fra en back-end API.

## <a name="next-steps"></a>Næste trin
Giv os din feedback i Disqus tråd i dette emne hvis der er andre scenarier, der har aktiveret politikkerne for dig, eller hvis der er scenarier du vil opnå, men gør føler ikke er i øjeblikket er muligt.
