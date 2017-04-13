<properties 
    pageTitle="Sådan uddelegere bruger registrering og produkt-abonnement" 
    description="Lær at uddelegere bruger registrering og produkt abonnement til en tredjepart i Azure API Management." 
    services="api-management" 
    documentationCenter="" 
    authors="antonba" 
    manager="erikre" 
    editor=""/>

<tags 
    ms.service="api-management" 
    ms.workload="mobile" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="10/25/2016" 
    ms.author="antonba"/>

# <a name="how-to-delegate-user-registration-and-product-subscription"></a>Sådan uddelegere bruger registrering og produkt-abonnement

Delegering gør det muligt at bruge dit eksisterende websted til håndtering af udvikler log-i/sign-opskrivning og produkter i modsætning til at bruge den indbyggede funktion i portalen udvikler-abonnement. Dette gør det muligt for webstedet at eje brugerdataene og udføre validering af disse trin i en brugerdefineret måde.

## <a name="delegate-signin-up"> </a>Delegating udvikler logon og tilmelding

Hvis du vil uddelegere udvikler logon og tilmelding til dit eksisterende websted skal du at oprette en speciel delegering slutpunkt på dit websted, der fungerer som indgangspunkt for en sådan anmodning startet fra portalen API Management udvikler.

Den endelige arbejdsproces bliver på følgende måde:

1. Udvikler klikker på linket Log på eller tilmelding på portalen API Management udvikler
2. Browseren omdirigeres til slutpunktet delegering
3. Delegering slutpunkt som svar omdirigerer til eller præsenterer Brugergrænsefladen spørger brugeren til at logge på eller tilmelding
4. På succes omdirigeres brugeren tilbage til API udvikler portalen siden til administration af de startes fra


For at begynde, Lad os første konfigureres API Management til at dirigere-anmodninger via din delegering slutpunkt. Klik på **sikkerhed** i portalen API Management publisher, og klik derefter på fanen **delegering** . Klik på afkrydsningsfeltet for at aktivere 'Delegate logon og tilmelding'.

![Delegering side][api-management-delegation-signin-up]

* Beslutte, hvad URL-adressen på dit speciel delegering slutpunkt, som og angive det i feltet **delegering slutpunkt URL-adresse** . 

* Angiv et hemmeligt, der skal bruges til at beregne en signatur, der er angivet for dig til godkendelse til at sikre, at din anmodning faktisk kommer fra Azure API Management i feltet **delegering godkendelsesnøgle** . Du kan klikke på knappen **Generer** for at få API Managemnet tilfældigt genererer en nøgle for dig.

Nu skal du oprette **delegering slutpunkt**. Den skal udføre en række forskellige handlinger:

1. Modtage en anmodning i følgende format:

    > *http://www.yourwebsite.com/apimdelegation?operation=SignIn&returnUrl= {URL-adressen til kildesiden} & salt = {streng} & sig = {streng}*

    Forespørgselsparametre til logon / tilmelding til en sag:
    - **handlingen**: identificerer hvilken type delegering anmoder om det er – det kan kun anvendes **logon** i dette tilfælde
    - **returnUrl**: URL-adressen til den side, hvor brugeren klikkede på et link til logon eller tilmelding
    - **salt**: en speciel Saltstreng bruges til computing hash sikkerhed
    - **sig**: en beregnede sikkerhed hash skal bruges til sammenligning af med dine egne beregnes hash

2. Kontrollér, at anmodningen kommer fra Azure API Management (valgfrit, men stærkt anbefales for sikkerhed)

    * Beregne en HMAC SHA512 hashværdi for en streng, der er baseret på forespørgselsparametre **returnUrl** og **salt** ([eksempelkode, der er angivet nedenfor]):
        > HMAC (**salt** + "\n" + **returnUrl**)
         
    * Sammenligne giver over beregnes resultat til værdien af parameteren **sig** forespørgsel. Hvis de to hashes stemmer overens, kan du gå videre til næste trin, ellers afvise anmodningen.

2. Kontrollér, at du modtager en anmodning om Log på/Log af: forespørgselsparameter **handlingen** skal være indstillet til "**logon**".

3. Præsentere brugeren for Brugergrænsefladen til at logge på eller tilmelding

4. Hvis brugeren er logge op, du har oprettet en tilsvarende konto efter dem i API administration. [Opret en bruger] med API Management REST-API. Når du gør det, kan du sikre dig, at du angiver bruger-ID til den samme som det er i din bruger store eller til et ID, som du kan holde styr på.

5. Når brugeren er blevet godkendt:

    * [anmode om et - enkeltlogon (SSO) token] via API Management REST-API

    * føje en returnUrl forespørgselsparameter til den SSO URL-adresse, du har modtaget fra API-opkald over:
        > FX https://customer.portal.azure-api.net/signin-sso?token&returnUrl=/return/url 

    * omdirigere brugeren til den ovenstående producerede URL-adresse

Ud over handlingen **-logon,** kan du også udføre kontoadministration ved at følge de forrige trin og bruge en af følgende handlinger.

-   **Ændring af adgangskode**
-   **ChangeProfile**
-   **CloseAccount**

Du skal overføre følgende forespørgselsparametre for konto management handlinger.

-   **handlingen**: identificerer hvilken type delegering anmodning om det er (ændring af adgangskode, ChangeProfile eller CloseAccount)
-   **bruger-id**: konto til at administrere bruger-id
-   **salt**: en speciel Saltstreng bruges til computing hash sikkerhed
-   **sig**: en beregnede sikkerhed hash skal bruges til sammenligning af med dine egne beregnes hash

## <a name="delegate-product-subscription"> </a>Delegering af produkt-abonnement

Delegering produkt abonnement fungerer på samme måde at uddelegere bruger-logon/op. Den endelige arbejdsproces er som følger:

1. Udvikler vælger et produkt i portalen API Management udvikler og klikker på knappen Abonner
2. Browseren omdirigeres til slutpunktet delegering
3. Delegering slutpunkt udfører nødvendige produkt abonnement trin – dette er op til dig og den kan medføre omdirigere til en anden side til at anmode om faktureringsoplysninger, stille yderligere spørgsmål, eller blot gemme oplysningerne og ikke kræver nogen brugerhandling


Klik på **stedfortræder produkt abonnement**for at aktivere funktionen, på siden **Delegation** .

Sikre derefter delegering slutpunktet udfører følgende handlinger:


1. Modtage en anmodning i følgende format:

    > *http://www.yourwebsite.com/apimdelegation?operation= {handlingen} & productId = {produkt abonnere på} & bruger-id = {brugeren foretager anmodning} & salt = {streng} & sig = {streng}*

    Forespørgselsparametre for produkt abonnement store og små bogstaver:
    - **handlingen**: identificerer, hvilken type af anmodning om delegering er. For produktet abonnement er anmodninger gyldige indstillinger:
        - "Abonnere": en anmodning om at abonnere på et bestemt produkt med brugeren angivet ID (se nedenfor)
        - "Opsige abonnementet": en anmodning om at opsige abonnementet en bruger fra et produkt
        - "Fornyelse": en requst til at forny et abonnement (f.eks., der kan udløber)
    - **produkt-id**: ID'ET for det produkt, som brugeren har anmodet om at abonnere
    - **bruger-id**: ID'ET for den bruger, for hvem anmodes
    - **salt**: en speciel Saltstreng bruges til computing hash sikkerhed
    - **sig**: en beregnede sikkerhed hash skal bruges til sammenligning af med dine egne beregnes hash


2. Kontrollér, at anmodningen kommer fra Azure API Management (valgfrit, men stærkt anbefales for sikkerhed)

    * Beregne en HMAC SHA512 af en streng, der er baseret på **productId**, **bruger-id** og **salt** forespørgselsparametre:
        > HMAC (**salt** + "\n" + **productId** + "\n" + **bruger-id**)
         
    * Sammenligne giver over beregnes resultat til værdien af parameteren **sig** forespørgsel. Hvis de to hashes stemmer overens, kan du gå videre til næste trin, ellers afvise anmodningen.
    
3. Udføre en hvilken som helst produkt abonnement behandling, der er baseret på typen handling anmoder om i **handlingen** - fx fakturerings-yderligere spørgsmål osv.

4. På korrekt abonnerer brugeren til produktet på din side, du abonnerer på brugeren til administration af API produktet ved at [ringe til REST-API til produkt abonnement].

## <a name="delegate-example-code"></a> Eksempelkode ##

Disse kodeeksempler, der viser, hvordan du tager *delegering valideringsnøgle*, der er angivet i skærmbilledet delegering af portalen publisher til at oprette en HMAC som skal bruges til at validere signaturen, beviser overførte returnUrl gyldighed. Den samme kode fungerer efter productId og bruger-id med mindre ændring.

**C#-kode til at generere hash af returnUrl**

    using System.Security.Cryptography;

    string key = "delegation validation key";
    string returnUrl = "returnUrl query parameter";
    string salt = "salt query parameter";
    string signature;
    using (var encoder = new HMACSHA512(Convert.FromBase64String(key)))
    {
        signature = Convert.ToBase64String(encoder.ComputeHash(Encoding.UTF8.GetBytes(salt + "\n" + returnUrl)));
        // change to (salt + "\n" + productId + "\n" + userId) when delegating product subscription
        // compare signature to sig query parameter
    }


**NodeJS kode til at generere hash af returnUrl**

    var crypto = require('crypto');
    
    var key = 'delegation validation key'; 
    var returnUrl = 'returnUrl query parameter';
    var salt = 'salt query parameter';
    
    var hmac = crypto.createHmac('sha512', new Buffer(key, 'base64'));
    var digest = hmac.update(salt + '\n' + returnUrl).digest();
    // change to (salt + "\n" + productId + "\n" + userId) when delegating product subscription
    // compare signature to sig query parameter
    
    var signature = digest.toString('base64');

## <a name="next-steps"></a>Næste trin

Du kan finde flere oplysninger om delegering, i den følgende video.

> [AZURE.VIDEO delegating-user-authentication-and-product-subscription-to-a-3rd-party-site]

[Delegating developer sign-in and sign-up]: #delegate-signin-up
[Delegating product subscription]: #delegate-product-subscription
[anmode om et enkelt-sign-on (SSO) token]: http://go.microsoft.com/fwlink/?LinkId=507409
[oprette en bruger]: http://go.microsoft.com/fwlink/?LinkId=507655#CreateUser
[ringe til REST-API til produkt-abonnement]: http://go.microsoft.com/fwlink/?LinkId=507655#SSO
[Next steps]: #next-steps
[Eksempler på kode angivet herunder]: #delegate-example-code

[api-management-delegation-signin-up]: ./media/api-management-howto-setup-delegation/api-management-delegation-signin-up.png 