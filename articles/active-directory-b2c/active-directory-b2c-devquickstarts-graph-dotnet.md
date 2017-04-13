<properties
    pageTitle="Azure Active Directory B2C: Bruge grafen API | Microsoft Azure"
    description="Sådan kalde Graph API for B2C lejere ved hjælp af en program-id til at automatisere processen."
    services="active-directory-b2c"
    documentationCenter=".net"
    authors="dstrockis"
    manager="mbaldwin"
    editor="bryanla"/>

<tags
    ms.service="active-directory-b2c"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="dotnet"
    ms.topic="article"
    ms.date="08/30/2016"
    ms.author="dastrock"/>

# <a name="azure-ad-b2c-use-the-graph-api"></a>Azure AD-B2C: Bruge grafen API

Azure Active Directory (Azure AD) B2C lejere har tendens til at være meget store. Det betyder, at mange almindelige lejer administrationsopgaver skal udføres fra et program. En primær eksempel er brugeradministration. Du skal muligvis overføre en eksisterende bruger store til en B2C lejer. Du muligvis vil hoste brugerregistrering af din egen side og oprette brugerkonti i Azure AD i baggrunden. Disse typer opgaver kræver mulighed for at oprette, læse, opdatere og slette brugerkonti. Du kan gøre disse opgaver ved hjælp af Azure AD Graph API.

B2C lejere er der to primære måder at kommunikere med Graph API.

- For opgaver, interaktive, køres én gang, skal du fungere som en administratorkonto i B2C lejeren, når du udfører opgaverne. Denne tilstand kræver en administrator til at logge på med legitimationsoplysningerne, før-administratoren kan udføre en hvilken som helst opkald til Graph API.
- For automatiseret, fortløbende opgaver, skal du bruge en type af servicekonto, at du angiver med de nødvendige rettigheder til at udføre administrationsopgaver. I Azure AD, kan du gøre dette ved at registrere et program og godkendelse til Azure AD. Dette gøres ved hjælp af et **Program-ID** , der bruger den [OAuth 2.0-klienten legitimationsoplysninger give](../active-directory/active-directory-authentication-scenarios.md#daemon-or-server-application-to-web-api). I dette tilfælde fungerer programmet som sig selv ikke som en bruger til at ringe til Graph API.

I denne artikel vil vi diskutere hvordan du udfører automatiserede use case. Hvis du vil vise, skal vi oprette en .NET 4.5 `B2CGraphClient` , som udfører bruger oprette, læse, opdatere og slette (CRUD) handlinger. Kunden har en Windows-kommandolinjeparametre grænseflade (CLI), hvor du kan aktivere forskellige metoder. Dog skrives koden til fungerer på en ikke-interaktive og automatiseret måde.

## <a name="get-an-azure-ad-b2c-tenant"></a>Få en Azure AD B2C lejer

Før du kan oprette programmer eller brugere, eller du kan interagere med Azure AD overhovedet, skal du en Azure AD B2C lejer og en global administrationskonto i lejeren. Hvis du ikke har allerede en lejer [Introduktion til Azure AD B2C](active-directory-b2c-get-started.md).

## <a name="register-a-service-application-in-your-tenant"></a>Registrere et tjenesteprogram i din lejer

Når du har en B2C lejer, skal du oprette din tjenesteprogram ved hjælp af Azure AD PowerShell-cmdlet'er.
Først skal du hente og installere [Microsoft Online Services-logon-assistenten](http://go.microsoft.com/fwlink/?LinkID=286152). Derefter kan du hente og installere [64-bit Azure Active Directory-modul til Windows PowerShell](http://go.microsoft.com/fwlink/p/?linkid=236297).

> [AZURE.IMPORTANT]
Hvis du vil bruge API'EN Graph med din B2C lejer, skal du registrere et dedikeret program ved hjælp af PowerShell. Følg instruktionerne i denne artikel for at gøre det. Du kan genbruge de allerede eksisterende B2C programmer, der er registreret i portalen Azure.

Når du har installeret af PowerShell-modulet, kan du åbne PowerShell og oprette forbindelse til din B2C lejer. Når du har kørt `Get-Credential`, bliver du spurgt, om et brugernavn og adgangskode, kan du angive det brugernavn og adgangskoden til din B2C lejer administratorkonto.

```
> $msolcred = Get-Credential
> Connect-MsolService -credential $msolcred
```

Før du opretter dit program, skal du oprette en ny **klient hemmeligt**.  Dit program anvender Hemmeligheden bag klienten til at godkende Azure AD og for at få fat på access tokens. Du kan oprette en gyldig hemmeligt i PowerShell:

```
> $bytes = New-Object Byte[] 32
> $rand = [System.Security.Cryptography.RandomNumberGenerator]::Create()
> $rand.GetBytes($bytes)
> $rand.Dispose()
> $newClientSecret = [System.Convert]::ToBase64String($bytes)
> $newClientSecret
```

Den sidste kommando skal udskrive din nye klient hemmeligt. Kopiere det et sted, hvor sikkert. Du skal bruge den senere. Du kan nu oprette dit program ved at indsende det nye klient hemmeligt som legitimationsoplysninger for appen:

```
> New-MsolServicePrincipal -DisplayName "My New B2C Graph API App" -Type password -Value $newClientSecret

DisplayName           : My New B2C Graph API App
ServicePrincipalNames : {dd02c40f-1325-46c2-a118-4659db8a55d5}
ObjectId              : e2bde258-6691-410b-879c-b1f88d9ef664
AppPrincipalId        : dd02c40f-1325-46c2-a118-4659db8a55d5
TrustedForDelegation  : False
AccountEnabled        : True
Addresses             : {}
KeyType               : Password
KeyId                 : a261e39d-953e-4d6a-8d70-1f915e054ef9
StartDate             : 9/2/2015 1:33:09 AM
EndDate               : 9/2/2016 1:33:09 AM
Usage                 : Verify
```

Hvis du opretter programmet korrekt, skal den udskrive egenskaber for programmet som dem ovenfor. Du skal bruge begge `ObjectId` og `AppPrincipalId`, så kopiere disse værdier for.

Når du opretter et program i din B2C lejer, skal du tildele den de tilladelser, der skal udføre bruger CRUD handlinger. Tildele rollerne programmet tre: directory læsere (for at læse brugere), directory forfattere (for at oprette og opdatere brugere), og en Brugeradministrator konto (for at slette brugere). Disse roller har velkendte id'er, så du kan erstatte den `-RoleMemberObjectId` parameter med `ObjectId` ovenfra og køre følgende kommandoer. At se en liste over alle directory roller, prøv at køre `Get-MsolRole`.

```
> Add-MsolRoleMember -RoleObjectId 88d8e3e3-8f55-4a1e-953a-9b9898b8876b -RoleMemberObjectId <Your-ObjectId> -RoleMemberType servicePrincipal
> Add-MsolRoleMember -RoleObjectId 9360feb5-f418-4baa-8175-e2a00bac4301 -RoleMemberObjectId <Your-ObjectId> -RoleMemberType servicePrincipal
> Add-MsolRoleMember -RoleObjectId fe930be7-5e62-47db-91af-98c3a49a38b1 -RoleMemberObjectId <Your-ObjectId> -RoleMemberType servicePrincipal
```

Du nu har et program, der har tilladelse til at oprette, læse, opdatere og slette brugere fra din B2C lejer.

## <a name="download-configure-and-build-the-sample-code"></a>Hente, konfigurere og opbygge eksempelkoden

Først skal hente eksempelkoden, og få den kører. Vi tager derefter et nærmere Kig på den.  Du kan [hente eksempelkoden som en .zip-fil](https://github.com/AzureADQuickStarts/B2C-GraphAPI-DotNet/archive/master.zip). Du kan også klone til en mappe efter eget valg:

```
git clone https://github.com/AzureADQuickStarts/B2C-GraphAPI-DotNet.git
```

Åbn den `B2CGraphClient\B2CGraphClient.sln` Visual Studio-løsning i Visual Studio. I den `B2CGraphClient` project, skal du åbne filen `App.config`. Erstatte de tre app-indstillinger med dine egne værdier:

```
<appSettings>
    <add key="b2c:Tenant" value="contosob2c.onmicrosoft.com" />
    <add key="b2c:ClientId" value="{The AppPrincipalId from above}" />
    <add key="b2c:ClientSecret" value="{The client secret you generated above}" />
</appSettings>
```

[AZURE.INCLUDE [active-directory-b2c-devquickstarts-tenant-name](../../includes/active-directory-b2c-devquickstarts-tenant-name.md)]

Derefter skal du højreklikke på den `B2CGraphClient` løsning og Genopbyg stikprøvernes. Hvis du er gået igennem, skal du nu har en `B2C.exe` eksekverbar fil, der er placeret i `B2CGraphClient\bin\Debug`.

## <a name="build-user-crud-operations-by-using-the-graph-api"></a>Opbygge bruger CRUD handlinger ved hjælp af Graph API

Hvis du vil bruge B2CGraphClient, skal du åbne en `cmd` Windows kommandoen Spørg og ændre mappen til den `Debug` directory. Kør derefter den `B2C Help` kommandoen.

```
> cd B2CGraphClient\bin\Debug
> B2C Help
```

For at se en kort beskrivelse af hver kommando. Hver gang du starter en af disse kommandoer `B2CGraphClient` gør en anmodning til Azure AD Graph API.

### <a name="get-an-access-token"></a>Få et adgangstoken

En anmodning til Graph API kræver et adgangstoken til godkendelse. `B2CGraphClient`bruger på open source-Active Directory Authentication bibliotek (ADAL) til at få fat på access tokens. ADAL gør token acquisition nemmere ved at angive en enkel API og tager sig af nogle vigtige detaljer, som cachelagring access tokens. Du behøver ikke at bruge ADAL til at hente tokens, men. Du kan også finde tokens ved at oprette HTTP-anmodninger.

> [AZURE.NOTE]
    Dette kodeeksempel bruger ADAL v2 for at kommunikere med Graph API.  Du skal bruge ADAL v2 eller v3 Hvis du vil have adgangstokens, som kan bruges med Azure AD Graph API.

Når `B2CGraphClient` køres, programmet opretter en forekomst af den `B2CGraphClient` klasse. Parametre for denne klasse konfigurerer en ADAL godkendelse stilladser:

```C#
public B2CGraphClient(string clientId, string clientSecret, string tenant)
{
    // The client_id, client_secret, and tenant are provided in Program.cs, which pulls the values from App.config
    this.clientId = clientId;
    this.clientSecret = clientSecret;
    this.tenant = tenant;

    // The AuthenticationContext is ADAL's primary class, in which you indicate the tenant to use.
    this.authContext = new AuthenticationContext("https://login.microsoftonline.com/" + tenant);

    // The ClientCredential is where you pass in your client_id and client_secret, which are
    // provided to Azure AD in order to receive an access_token by using the app's identity.
    this.credential = new ClientCredential(clientId, clientSecret);
}
```

Vi bruger den `B2C Get-User` kommando som et eksempel. Når `B2C Get-User` startes uden eventuelle yderligere input, CLI opkald på `B2CGraphClient.GetAllUsers(...)` metode. Denne metode kalder `B2CGraphClient.SendGraphGetRequest(...)`, som sender en HTTP GET-anmodning til Graph API. Før `B2CGraphClient.SendGraphGetRequest(...)` sender GET-anmodning, den først får en access token ved hjælp af ADAL:

```C#
public async Task<string> SendGraphGetRequest(string api, string query)
{
    // First, use ADAL to acquire a token by using the app's identity (the credential)
    // The first parameter is the resource we want an access_token for; in this case, the Graph API.
    AuthenticationResult result = authContext.AcquireToken("https://graph.windows.net", credential);

    ...

```

Du kan få en adgang token for API'EN Graph ved at ringe på ADAL `AuthenticationContext.AcquireToken(...)` metode. ADAL, returnerer en `access_token` , der repræsenterer programmets identitet.

### <a name="read-users"></a>Læs brugere

Når du vil hente en liste over brugere eller hente en bestemt bruger på Graph API, kan du sende en HTTP `GET` anmode om at den `/users` slutpunkt. En anmodning for alle brugere i en lejer ser sådan ud:

```
GET https://graph.windows.net/contosob2c.onmicrosoft.com/users?api-version=1.6
Authorization: Bearer eyJhbGciOiJSUzI1NiIsIng1dCI6IjdkRC1nZWNOZ1gxWmY3R0xrT3ZwT0IyZGNWQSIsInR5cCI6IkpXVCJ9.eyJhdWQiOiJod...
```

For at se denne anmodning skal du køre:

 ```
 > B2C Get-User
 ```

Der er to vigtige ting at være opmærksom på:

- Adgangstoken hentet via ADAL føjes til den `Authorization` sidehoved ved hjælp af den `Bearer` farveskema.
- B2C lejere, skal du bruge Forespørgselsparameteren `api-version=1.6`.

Begge af disse oplysninger skal håndteres i den `B2CGraphClient.SendGraphGetRequest(...)` metode:

```C#
public async Task<string> SendGraphGetRequest(string api, string query)
{
    ...

    // For B2C user management, be sure to use the 1.6 Graph API version.
    HttpClient http = new HttpClient();
    string url = "https://graph.windows.net/" + tenant + api + "?" + "api-version=1.6";
    if (!string.IsNullOrEmpty(query))
    {
        url += "&" + query;
    }

    // Append the access token for the Graph API to the Authorization header of the request by using the Bearer scheme.
    HttpRequestMessage request = new HttpRequestMessage(HttpMethod.Get, url);
    request.Headers.Authorization = new AuthenticationHeaderValue("Bearer", result.AccessToken);
    HttpResponseMessage response = await http.SendAsync(request);

    ...
```

### <a name="create-consumer-user-accounts"></a>Oprette consumer brugerkonti

Når du opretter brugerkonti i din B2C lejer, kan du sende en HTTP `POST` anmode om at den `/users` slutpunkt:

```
POST https://graph.windows.net/contosob2c.onmicrosoft.com/users?api-version=1.6
Authorization: Bearer eyJhbGciOiJSUzI1NiIsIng1dCI6IjdkRC1nZWNOZ1gxWmY3R0xrT3ZwT0IyZGNWQSIsInR5cCI6IkpXVCJ9.eyJhdWQiOiJod...
Content-Type: application/json
Content-Length: 338

{
    // All of these properties are required to create consumer users.

    "accountEnabled": true,
    "signInNames": [                            // controls which identifier the user uses to sign in to the account
        {
            "type": "emailAddress",             // can be 'emailAddress' or 'userName'
            "value": "joeconsumer@gmail.com"
        }
    ],
    "creationType": "LocalAccount",            // always set to 'LocalAccount'
    "displayName": "Joe Consumer",              // a value that can be used for displaying to the end user
    "mailNickname": "joec",                     // an email alias for the user
    "passwordProfile": {
        "password": "P@ssword!",
        "forceChangePasswordNextLogin": false   // always set to false
    },
    "passwordPolicies": "DisablePasswordExpiration"
}
```

De fleste af disse egenskaber i mødeindkaldelsen skal oprette consumer brugere. Hvis du vil have mere for at vide, skal du klikke på [her](https://msdn.microsoft.com/library/azure/ad/graph/api/users-operations#CreateLocalAccountUser). Bemærk, at den `//` kommentarer er blevet medtaget illustration. Du skal ikke medtage dem i en faktisk anmodning.

For at se anmodningen skal du køre en af følgende kommandoer:

```
> B2C Create-User ..\..\..\usertemplate-email.json
> B2C Create-User ..\..\..\usertemplate-username.json
```

Den `Create-User` kommandoen tager en .json fil som en inputparameteren. Indeholder en JSON repræsentation af et brugerobjekt. Der er to .json eksempelfiler i eksempelkoden: `usertemplate-email.json` og `usertemplate-username.json`. Du kan ændre disse filer efter dine behov. Ud over de obligatoriske felter ovenfor medtages flere valgfrie felter, du kan bruge i disse filer. Få at vide om de valgfrie felter kan findes i [Azure AD Graph API enhedsreference](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#user-entity).

Du kan se, hvordan POST-anmodningen er opbygget i `B2CGraphClient.SendGraphPostRequest(...)`.

- Føjer den et adgangstoken til den `Authorization` sidehoved for din anmodning.
- Det angiver `api-version=1.6`.
- Den indeholder brugerobjektet JSON i brødteksten i indkaldelsen.

> [AZURE.NOTE]
Hvis de konti, du vil overføre fra en eksisterende bruger store har nederste adgangskoder end [stærke adgangskoder hos Azure AD B2C](https://msdn.microsoft.com/library/azure/jj943764.aspx), kan du deaktivere den stærk adgangskode krav ved hjælp af den `DisableStrongPassword` værdi i den `passwordPolicies` egenskab. For eksempel kan du ændre Opret brugeranmodningen angivet ovenfor, på følgende måde: `"passwordPolicies": "DisablePasswordExpiration, DisableStrongPassword"`.

### <a name="update-consumer-user-accounts"></a>Opdatere consumer brugerkonti

Når du opdaterer user-objekter, er processen, der ligner den, du kan bruge til at oprette user-objekter. Men denne proces bruger HTTP `PATCH` metode:

```
PATCH https://graph.windows.net/contosob2c.onmicrosoft.com/users/<user-object-id>?api-version=1.6
Authorization: Bearer eyJhbGciOiJSUzI1NiIsIng1dCI6IjdkRC1nZWNOZ1gxWmY3R0xrT3ZwT0IyZGNWQSIsInR5cCI6IkpXVCJ9.eyJhdWQiOiJod...
Content-Type: application/json
Content-Length: 37

{
    "displayName": "Joe Consumer",              // this request updates only the user's displayName
}
```

Prøv at opdatere en bruger ved at opdatere dine JSON-filer med nye data. Du kan derefter bruge `B2CGraphClient` til at køre en af følgende kommandoer:

```
> B2C Update-User <user-object-id> ..\..\..\usertemplate-email.json
> B2C Update-User <user-object-id> ..\..\..\usertemplate-username.json
```

Undersøge den `B2CGraphClient.SendGraphPatchRequest(...)` metode have mere at vide om, hvordan du sender denne anmodning.

### <a name="search-users"></a>Brugere af Søg

Du kan søge efter brugere i din B2C lejer på flere måder. En ved hjælp af brugerens objekt-ID eller to, ved hjælp af brugerens logon id (det vil sige det `signInNames` egenskaben).

Køre en af følgende kommandoer til at søge efter en bestemt bruger:

```
> B2C Get-User <user-object-id>
> B2C Get-User <filter-query-expression>
```

Her er et par eksempler:

```
> B2C Get-User 2bcf1067-90b6-4253-9991-7f16449c2d91
> B2C Get-User $filter=signInNames/any(x:x/value%20eq%20%27joeconsumer@gmail.com%27)
```

### <a name="delete-users"></a>Slette brugere

Det er nemt at processen til at slette en bruger. Bruge HTTP `DELETE` metode og konstruktion URL-adresse med de korrekte objekt-ID:

```
DELETE https://graph.windows.net/contosob2c.onmicrosoft.com/users/<user-object-id>?api-version=1.6
Authorization: Bearer eyJhbGciOiJSUzI1NiIsIng1dCI6IjdkRC1nZWNOZ1gxWmY3R0xrT3ZwT0IyZGNWQSIsInR5cCI6IkpXVCJ9.eyJhdWQiOiJod...
```

Skriv følgende kommando for at få vist et eksempel, og få vist Sletteanmodningen, der udskrives til konsollen:

```
> B2C Delete-User <object-id-of-user>
```

Undersøge den `B2CGraphClient.SendGraphDeleteRequest(...)` metode have mere at vide om, hvordan du sender denne anmodning.

Du kan udføre mange andre handlinger med Azure AD Graph API ud over brugeradministration. [Azure AD Graph API reference](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/api-catalog) indeholder oplysninger om hver handling, sammen med eksempel anmodninger.

## <a name="use-custom-attributes"></a>Bruge brugerdefinerede attributter

De fleste consumer programmer skal gemme visse typer brugerdefinerede profiloplysninger. Der er en måde, kan du gøre dette til at definere en brugerdefineret attribut i din B2C lejer. Du kan derefter behandle attributten på samme måde, du behandle alle andre egenskaber på et brugerobjekt. Du kan opdatere attributten slette attributten forespørgsel ved attributten, og send attributten som et krav i tokens logon og meget mere.

Hvis du vil angive en brugerdefineret attribut i din B2C lejer, se [B2C brugerdefineret attributreference](active-directory-b2c-reference-custom-attr.md).

Du kan få vist de brugerdefinerede attributter, der er defineret i din B2C lejer ved hjælp af `B2CGraphClient`:

```
> B2C Get-B2C-Application
> B2C Get-Extension-Attribute <object-id-in-the-output-of-the-above-command>
```

Output fra disse funktioner vises detaljerne for hver brugerdefineret attribut, f.eks.:

```JSON
{
      "odata.type": "Microsoft.DirectoryServices.ExtensionProperty",
      "objectType": "ExtensionProperty",
      "objectId": "cec6391b-204d-42fe-8f7c-89c2b1964fca",
      "deletionTimestamp": null,
      "appDisplayName": "",
      "name": "extension_55dc0861f9a44eb999e0a8a872204adb_Jersey_Number",
      "dataType": "Integer",
      "isSyncedFromOnPremises": false,
      "targetObjects": [
        "User"
      ]
}
```

Du kan bruge det fulde navn, såsom `extension_55dc0861f9a44eb999e0a8a872204adb_Jersey_Number`, som en egenskab på dit brugerobjekter.  Opdatere filen .json med den nye egenskab og en værdi for egenskaben, og derefter køre:

```
> B2C Update-User <object-id-of-user> <path-to-json-file>
```

Ved hjælp af `B2CGraphClient`, du har et tjenesteprogram, der kan administrere dine B2C lejer brugere fra et program. `B2CGraphClient`bruger et separat program-id til at godkende til Azure AD Graph API. Det får også tokens ved hjælp af en klient hemmeligt. Når du indarbejder denne funktionalitet i dit program, du huske et par vigtige punkter til B2C apps:

- Du vil give programmet de rette tilladelser i lejeren.
- Nu skal du bruge ADAL v2 for at få adgangstokens. (Du kan også sende protocol meddelelser direkte, uden at bruge et bibliotek.)
- Når du ringer Graph API, kan du bruge `api-version=1.6`.
- Når du opretter og opdatere consumer brugere, er nogle egenskaber påkrævet, som beskrevet ovenfor.

Hvis du har spørgsmål eller anmodninger om handlinger, du vil udføre ved hjælp af Graph API på din B2C lejer, skrive en kommentar til denne artikel eller filen er et problem i GitHub kode eksempel lager.
