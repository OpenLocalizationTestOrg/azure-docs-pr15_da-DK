<properties 
   pageTitle="Active Directory-godkendelse og ressourcestyring | Microsoft Azure"
   description="En udvikler vejledning til godkendelse med Azure ressourcestyring API og Active Directory for at integrere en app med andre Azure-abonnementer."
   services="azure-resource-manager,active-directory"
   documentationCenter="na"
   authors="dushyantgill"
   manager="timlt"
   editor="tysonn" />
<tags 
   ms.service="azure-resource-manager"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="identity"
   ms.date="08/31/2016"
   ms.author="dugill;tomfitz" />

# <a name="how-to-use-azure-active-directory-and-resource-manager-to-manage-a-customers-resources"></a>Sådan bruges Azure Active Directory og ressourcestyring til at administrere en kundes ressourcer

## <a name="introduction"></a>Introduktion

Hvis du er softwareudvikler, der har brug for at oprette en app, der administrerer kundens Azure ressourcer, viser i dette emne, hvordan du godkende med Azure ressourcestyring API'er og få adgang til ressourcer i andre abonnementer. 

Din app kan få adgang til de ressourcestyring API'er i flere måder:

1. **Bruger + app Åbn**: for apps, få adgang til ressourcer på vegne af en bruger, der er logget på. Denne metode fungerer til apps, som webapps og kommandolinjen værktøjer, som kan håndtere kun "interaktive management" Azure ressourcer.
1. **Kun App - adgang**: til apps, der kører daemon tjenester og planlagte opgaver. Den app identitet er tildelt direkte adgang til ressourcer. Denne metode fungerer til apps, der skal have langsigtede "offline adgang" til Azure.

Dette emne indeholder en trinvis vejledning til at oprette en app, der anvender begge disse metoder til godkendelse. Det viser, hvordan du udfører hver enkelt trin med REST-API eller C#. Fuldført ASP.NET MVC programmet findes på [https://github.com/dushyantgill/VipSwapper/tree/master/CloudSense](https://github.com/dushyantgill/VipSwapper/tree/master/CloudSense).

Alle koden for dette emne, der kører som en WebApp, som du kan prøve på [http://vipswapper.azurewebsites.net/cloudsense](http://vipswapper.azurewebsites.net/cloudsense). 

## <a name="what-the-web-app-does"></a>Hvad betyder WebApp

Online:

1. Tegn på en Azure bruger.
2. Spørger brugeren til at give web app-adgang til Ressourcestyring.
3. Henter bruger + app adgangstoken for at få adgang til Ressourcestyring.
4. Bruger-id (fra trin 3) til at ringe ressourcestyring og tildele den app service hovedstolen til en rolle i det abonnement, som giver app langsigtede adgang til abonnementet.
5. Bliver kun app-adgang token.
6. Bruger-id (fra trin 5) til at administrere ressourcer i abonnement via ressourcestyring.

Her er til Slut strømmen af webprogrammet.

![Ressourcestyring godkendelse flow](./media/resource-manager-api-authentication/Auth-Swim-Lane.png)

Som en bruger, kan du angive abonnement-id for det abonnement, du vil bruge:

![Angiv abonnement-id](./media/resource-manager-api-authentication/sample-ux-1.png)

Vælg kontoen, der skal bruges til at logge på.

![Vælg konto](./media/resource-manager-api-authentication/sample-ux-2.png)

Angive dine legitimationsoplysninger.

![angive legitimationsoplysninger](./media/resource-manager-api-authentication/sample-ux-3.png)

Giv app adgang til dine Azure abonnementer:
 
![Give adgang](./media/resource-manager-api-authentication/sample-ux-4.png)
 
Administrere dine abonnementer på forbindelsen:

![Oprette forbindelse abonnement](./media/resource-manager-api-authentication/sample-ux-7.png)


## <a name="register-application"></a>Registrering af program

Inden du starter kodningssprog, kan du registrere din online med Azure Active Directory (AD). App-registrering opretter en central identitet for din app i Azure AD. Den indeholder grundlæggende oplysninger om dit program som OAuth klient-ID, svar URL-adresser og legitimationsoplysninger, som dit program bruger til at godkende og få adgang til Azure ressourcestyring API'er. App-registrering registrerer også de forskellige delegerede tilladelser, som dit program skal bruge, når du åbner Microsoft APIs på vegne af brugeren. 

Da din app har adgang til andre abonnementer, skal du konfigurere det som et program med flere lejer. Hvis du vil overføre validering, give et domæne, der er knyttet til din Active Directory. Log på [Klassisk portal](https://manage.windowsazure.com)for at få vist de domæner, der er knyttet til din Active Directory. Vælg din Active Directory, og vælg derefter **domæner**.

I følgende eksempel viser, hvordan til at registrere app ved hjælp af Azure PowerShell. Du skal have den nyeste version (August 2016) af Azure PowerShell til denne kommando til at fungere. 

    $app = New-AzureRmADApplication -DisplayName "{app name}" -HomePage "https://{your domain}/{app name}" -IdentifierUris "https://{your domain}/{app name}" -Password "{your password}" -AvailableToOtherTenants $true
    
For at logge på som AD-program, skal du program-id og din adgangskode. For at se det program-id, der returneres fra den forrige kommando skal du bruge:

    $app.ApplicationId

I følgende eksempel viser, hvordan til at registrere app ved hjælp af Azure CLI. 

    azure ad app create --name {app name} --home-page https://{your domain}/{app name} --identifier-uris https://{your domain}/{app name} --password {your password} --available true

Resultaterne omfatter sikkerhedskontrol, som du skal bruge, når godkende som programmet.

### <a name="optional-configuration---certificate-credential"></a>Valgfri konfiguration - certifikat legitimationsoplysninger

Azure AD understøtter også certifikat legitimationsoplysninger for programmer: du oprette et selvsigneret certifikat, holde tasten privat, og Føj offentlig nøgle til din Azure AD-program registrering. Dit program sender en lille data til Azure AD signeret ved hjælp af din private nøgle til godkendelse, og Azure AD validerer den signatur, ved hjælp af den offentlige nøgle, du har registreret.

Oplysninger om oprettelse af en AD app med et certifikat, skal du se [Bruge Azure PowerShell til at oprette en sikkerhedskonto til at få adgang til ressourcer tjeneste](resource-group-authenticate-service-principal.md#create-service-principal-with-certificate) eller [Brug Azure CLI til at oprette en sikkerhedskonto til at få adgang til ressourcer tjeneste](resource-group-authenticate-service-principal-cli.md#create-service-principal-with-certificate).

## <a name="get-tenant-id-from-subscription-id"></a>Få lejer id fra abonnement-id

For at få et token, som kan bruges til at ringe til Ressourcestyring, skal dit program vide Azure AD-lejer, der er vært abonnementet Azure lejer ID. Højst sandsynligt dine brugere vide deres abonnement-id, men de kan ikke kender deres lejer id'er til Active Directory. Bed brugeren om abonnement-id for at få det bruger-id-lejer. Når du sender en anmodning om abonnementet, skal du angive dette abonnement-id:

    https://management.azure.com/subscriptions/{subscription-id}?api-version=2015-01-01

Anmodningen mislykkes, fordi brugeren ikke har logget på endnu, men du kan hente lejer id'et fra svaret. I undtagelsen, at hente lejer-id fra værdien svar sidehoved for **WWW-godkendelse**. Du ser denne implementering i metoden [GetDirectoryForSubscription](https://github.com/dushyantgill/VipSwapper/blob/master/CloudSense/CloudSense/AzureResourceManagerUtil.cs#L20) .

## <a name="get-user--app-access-token"></a>Få bruger + app adgangstoken

Dit program omdirigerer brugeren til Azure AD med en OAuth 2.0 Godkend anmodning – til at godkende brugerens legitimationsoplysninger og komme tilbage en godkendelseskode for. Dit program bruger koden tilladelse til at hente en access token for ressourcestyring. Metoden [ConnectSubscription](https://github.com/dushyantgill/VipSwapper/blob/master/CloudSense/CloudSense/Controllers/HomeController.cs#L42) opretter anmodningen om godkendelse.

Dette emne viser REST-API anmodninger om at godkende brugeren. Du kan også bruge hjælper biblioteker til at udføre godkendelse i din kode. Du kan finde flere oplysninger om disse biblioteker, [Azure Active Directory Authentication biblioteker](./active-directory/active-directory-authentication-libraries.md). Du kan finde en vejledning i at integrere identitet management i et program, [Azure Active Directory udvikler vejledning](./active-directory/active-directory-developers-guide.md).

### <a name="auth-request-oauth-20"></a>Anmodning om auth (OAuth 2.0)

Problem med en åben-ID forbinde/OAuth2.0 Godkend anmodning til Azure AD-godkende slutpunktet:

    https://login.microsoftonline.com/{tenant-id}/OAuth2/Authorize

Streng forespørgselsparametre, der er tilgængelige for denne anmodning er beskrevet i emnet med den [anmodning om en tilladelse kode](./active-directory/active-directory-protocols-oauth-code.md#request-an-authorization-code) .

I følgende eksempel viser, hvordan du anmoder om OAuth2.0 godkendelse:

    https://login.microsoftonline.com/{tenant-id}/OAuth2/Authorize?client_id=a0448380-c346-4f9f-b897-c18733de9394&response_mode=query&response_type=code&redirect_uri=http%3a%2f%2fwww.vipswapper.com%2fcloudsense%2fAccount%2fSignIn&resource=https%3a%2f%2fgraph.windows.net%2f&domain_hint=live.com

Azure AD godkender brugeren, og hvis det er nødvendigt, beder brugeren om at give tilladelse til appen. Den returnerer godkendelseskoden til svar URL-adressen for dit program. Afhængigt af den ønskede response_mode Azure AD enten sender dataene i forespørgselsstreng eller som indlæg data.

    code=AAABAAAAiL****FDMZBUwZ8eCAA&session_state=2d16bbce-d5d1-443f-acdf-75f6b0ce8850

### <a name="auth-request-open-id-connect"></a>Anmodning om auth (Åbn ID forbinde)

Hvis du ikke blot ønsker at få adgang til Azure Ressourcestyring på vegne af brugeren, men også tillade, at brugeren til at logge på dit program ved hjælp af deres Azure AD-konto, problem med en åben-ID forbinde Godkend anmodning. Med Åbn ID forbinde modtager dit program også en id_token fra Azure AD, som din app kan bruge til at logge på brugeren.

Streng forespørgselsparametre, der er tilgængelige for denne anmodning er beskrevet i emnet [Send indkaldelsen logon](./active-directory/active-directory-protocols-openid-connect-code.md#send-the-sign-in-request) .

Eksempel Åbn ID forbinde en anmodning om en er:

     https://login.microsoftonline.com/{tenant-id}/OAuth2/Authorize?client_id=a0448380-c346-4f9f-b897-c18733de9394&response_mode=form_post&response_type=code+id_token&redirect_uri=http%3a%2f%2fwww.vipswapper.com%2fcloudsense%2fAccount%2fSignIn&resource=https%3a%2f%2fgraph.windows.net%2f&scope=openid+profile&nonce=63567Dc4MDAw&domain_hint=live.com&state=M_12tMyKaM8

Azure AD godkender brugeren, og hvis det er nødvendigt, beder brugeren om at give tilladelse til appen. Den returnerer godkendelseskoden til svar URL-adressen for dit program. Afhængigt af den ønskede response_mode Azure AD enten sender dataene i forespørgselsstreng eller som indlæg data.

Et eksempel Åbn ID forbinde svar er:

    code=AAABAAAAiL*****I4rDWd7zXsH6WUjlkIEQxIAA&id_token=eyJ0eXAiOiJKV1Q*****T3GrzzSFxg&state=M_12tMyKaM8&session_state=2d16bbce-d5d1-443f-acdf-75f6b0ce8850

### <a name="token-request-oauth20-code-grant-flow"></a>Token anmodning (OAuth2.0 kode Giv dataflow)

Nu hvor dit program har modtaget koden tilladelse fra Azure AD, er det tid til at få adgang til token til Azure ressourcestyring.  Sende en OAuth2.0 kode Giv Token anmode om til Azure AD Token slutpunktet: 

    https://login.microsoftonline.com/{tenant-id}/OAuth2/Token

Streng forespørgselsparametre, der er tilgængelige for denne anmodning er beskrevet i emnet [Brug godkendelseskoden](./active-directory/active-directory-protocols-oauth-code.md#use-the-authorization-code-to-request-an-access-token) .

I følgende eksempel viser en anmodning om kode Giv token med adgangskode legitimationsoplysninger:

    POST https://login.microsoftonline.com/7fe877e6-a150-4992-bbfe-f517e304dfa0/oauth2/token HTTP/1.1

    Content-Type: application/x-www-form-urlencoded
    Content-Length: 1012

    grant_type=authorization_code&code=AAABAAAAiL9Kn2Z*****L1nVMH3Z5ESiAA&redirect_uri=http%3A%2F%2Flocalhost%3A62080%2FAccount%2FSignIn&client_id=a0448380-c346-4f9f-b897-c18733de9394&client_secret=olna84E8*****goScOg%3D

Når du arbejder med certifikat legitimationsoplysninger, kan du oprette en JSON Web Token (JWT) og log (RSA SHA256) ved hjælp af den private nøgle i dit program certifikat legitimationsoplysninger. Typerne krav til tokenet er vist i [JWT token krav](./active-directory/active-directory-protocols-oauth-code.md#jwt-token-claims). Se [Active Directory Auth bibliotek (.NET) kode](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet/blob/dev/src/ADAL.PCL.Desktop/CryptographyHelper.cs) til at signere klient program JWT tokens til reference.

Se [åbne ID forbinde specifikation](http://openid.net/specs/openid-connect-core-1_0.html#ClientAuthentication) få mere at vide om klientgodkendelse. 

I følgende eksempel viser en anmodning om kode Giv token med certifikat legitimationsoplysninger:

    POST https://login.microsoftonline.com/7fe877e6-a150-4992-bbfe-f517e304dfa0/oauth2/token HTTP/1.1
    
    Content-Type: application/x-www-form-urlencoded
    Content-Length: 1012
    
    grant_type=authorization_code&code=AAABAAAAiL9Kn2Z*****L1nVMH3Z5ESiAA&redirect_uri=http%3A%2F%2Flocalhost%3A62080%2FAccount%2FSignIn&client_id=a0448380-c346-4f9f-b897-c18733de9394&client_assertion_type=urn%3Aietf%3Aparams%3Aoauth%3Aclient-assertion-type%3Ajwt-bearer&client_assertion=eyJhbG*****Y9cYo8nEjMyA

Et eksempel svar til kode Giv token: 

    HTTP/1.1 200 OK

    {"token_type":"Bearer","expires_in":"3599","expires_on":"1432039858","not_before":"1432035958","resource":"https://management.core.windows.net/","access_token":"eyJ0eXAiOiJKV1Q****M7Cw6JWtfY2lGc5A","refresh_token":"AAABAAAAiL9Kn2Z****55j-sjnyYgAA","scope":"user_impersonation","id_token":"eyJ0eXAiOiJKV*****-drP1J3P-HnHi9Rr46kGZnukEBH4dsg"}

#### <a name="handle-code-grant-token-response"></a>Håndtere kode Giv token svar

En vellykket token svaret indeholder (bruger + app) adgangstoken til Azure ressourcestyring. Dit program bruger denne adgangstoken til at få adgang til Ressourcestyring på vegne af brugeren. Levetiden for access tokens udstedt af Azure AD er en time. Det er ikke sandsynligt, at dit webprogram skal forny (bruger + app) adgangstoken. Hvis den skal forny adgangstoken, skal du bruge tokenet opdatering, som dit program modtager i token svaret. Sende en OAuth2.0 Token anmode om til Azure AD Token slutpunktet: 

    https://login.microsoftonline.com/{tenant-id}/OAuth2/Token

Parametre til brug med Opdater anmodningen er beskrevet i [opdatere adgangstoken](./active-directory/active-directory-protocols-oauth-code.md#refreshing-the-access-tokens).

I følgende eksempel viser, hvordan du bruger opdateringen token:

    POST https://login.microsoftonline.com/7fe877e6-a150-4992-bbfe-f517e304dfa0/oauth2/token HTTP/1.1

    Content-Type: application/x-www-form-urlencoded
    Content-Length: 1012

    grant_type=refresh_token&refresh_token=AAABAAAAiL9Kn2Z****55j-sjnyYgAA&client_id=a0448380-c346-4f9f-b897-c18733de9394&client_secret=olna84E8*****goScOg%3D

Selvom Opdater tokens kan bruges til at hente nye access tokens til Azure Ressourcestyring, er de er ikke egnet til offline-adgang ved dit program. Opdater tokens levetid er begrænset, og Opdater tokens er bundet til brugeren. Hvis brugeren forlader organisationen, mister programmet ved hjælp af Opdater tokenet adgang. Denne metode er ikke egnet til programmer, der anvendes af teams for at administrere deres Azure ressourcer.

## <a name="check-if-user-can-assign-access-to-subscription"></a>Kontrollere, hvis brugeren kan tildele adgang til abonnement

Dit program nu har en token at få adgang til Azure Ressourcestyring på vegne af brugeren. Næste trin er at oprette forbindelse til din app til abonnementet. Når du har forbindelse, skal din app kan administrere abonnementer, selvom brugeren ikke er til stede (langsigtede offlineadgang). 

For hvert abonnement til at oprette forbindelse, du ringe til [ressourcestyring listetilladelser](https://msdn.microsoft.com/library/azure/dn906889.aspx) API til at afgøre, om brugeren har adgangsrettigheder til administration for abonnementet.

Metoden [UserCanManagerAccessForSubscription](https://github.com/dushyantgill/VipSwapper/blob/master/CloudSense/CloudSense/AzureResourceManagerUtil.cs#L44) af ASP.NET MVC eksempel app implementerer dette opkald.

I følgende eksempel viser, hvordan du anmoder om en brugers tilladelser til et abonnement. 83cfe939-2402-4581-b761-4f59b0a041e4 er id abonnementet.

    GET https://management.azure.com/subscriptions/83cfe939-2402-4581-b761-4f59b0a041e4/providers/microsoft.authorization/permissions?api-version=2015-07-01 HTTP/1.1

    Authorization: Bearer eyJ0eXAiOiJKV1QiLC***lwO1mM7Cw6JWtfY2lGc5A

Et eksempel på svaret få brugerens tilladelser på abonnement er:

    HTTP/1.1 200 OK

    {"value":[{"actions":["*"],"notActions":["Microsoft.Authorization/*/Write","Microsoft.Authorization/*/Delete"]},{"actions":["*/read"],"notActions":[]}]}

Tilladelser API returnerer flere tilladelser. Hver tilladelse består af tilladte handlinger (handlinger) og er ikke tilladt handlinger (notactions). Hvis en handling findes på handlingslisten tilladte i en hvilken som helst tilladelse og ikke findes på listen notactions af denne tilladelse, brugeren har tilladelse til at udføre denne handling. **Microsoft.Authorization/RoleAssignments/Write** er den handling, der giver adgang rights management. Dit program skal analysere tilladelser resultatet for at søge efter en regex match på denne handling streng i de handlinger og notactions af hver enkelt tilladelse.

## <a name="get-app-only-access-token"></a>Få kun app adgang token

Du kender nu, hvis brugeren kan tildele adgang til Azure abonnementet. De næste trin er:

1. Tildele den relevante RBAC rolle til dit program-id på abonnementet.
2. Validere en access-tildeling, ved at forespørge om programmets tilladelse på abonnementet eller ved at åbne ressourcestyring ved hjælp af kun app token.
1. Registrere forbindelsen i dine programmer "forbundne abonnementer" datastruktur - vedvarer id abonnementet.

Lad os se nærmere på det første trin. Hvis du vil tildele den relevante RBAC rolle programmets identitet, skal du identificere:

- Objekt-id for dit program-id i brugerens Azure Active Directory
- Id for rollen RBAC, som programmet kræver på dit abonnement

Når dit program godkender en bruger fra en Azure AD, oprettes der en tjeneste primært objekt for dit program i pågældende Azure AD. Azure kan RBAC roller, der skal tildeles til tjenesten principper til at give direkte adgang til tilsvarende programmer på Azure ressourcer. Denne handling er præcis, hvad vi vil gøre. Forespørgsel Azure AD Graph API til at bestemme id for tjenesten hovedstolen dit program i den bruger, der er logget på er Azure AD.

Du kun har et adgangstoken til Azure ressourcestyring – du har brug for en ny adgangstoken til at ringe til Azure AD Graph API. Hvert program i Azure AD har tilladelse til at forespørge sin egen primært objekt tjenesten, så et kun app adgangstoken er tilstrækkelige.

<a id="app-azure-ad-graph">
### <a name="get-app-only-access-token-for-azure-ad-graph-api"></a>Få kun app-adgang token til Azure AD Graph API

Udsende en klient legitimationsoplysninger Giv OAuth2.0 flow token anmodning til Azure AD token slutpunkt (**https://login.microsoftonline.com/ {directory_domain_name} / OAuth2/Token**) for at godkende din app og få et token til Azure AD Graph API.

Metoden [GetObjectIdOfServicePrincipalInOrganization](https://github.com/dushyantgill/VipSwapper/blob/master/CloudSense/CloudSense/AzureADGraphAPIUtil.cs) af programmet ASP.net MVC eksempel henter en app kun adgang token til Graph API ved hjælp af Active Directory Authentication Library til .NET.

Streng forespørgselsparametre, der er tilgængelige for denne anmodning er beskrevet i emnet med [anmodning om en Access-Token](./active-directory/active-directory-protocols-oauth-service-to-service.md#request-an-access-token) .

Et eksempel anmodning om klientens legitimationsoplysninger Giv token: 

    POST https://login.microsoftonline.com/62e173e9-301e-423e-bcd4-29121ec1aa24/oauth2/token HTTP/1.1
    Content-Type: application/x-www-form-urlencoded
    Content-Length: 187</pre>
    <pre>grant_type=client_credentials&client_id=a0448380-c346-4f9f-b897-c18733de9394&resource=https%3A%2F%2Fgraph.windows.net%2F &client_secret=olna8C*****Og%3D

Et eksempel svar for klient legitimationsoplysninger Giv token: 

    HTTP/1.1 200 OK

    {"token_type":"Bearer","expires_in":"3599","expires_on":"1432039862","not_before":"1432035962","resource":"https://graph.windows.net/","access_token":"eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik1uQ19WWmNBVGZNNXBPWWlKSE1iYTlnb0VLWSIsImtpZCI6Ik1uQ19WWmNBVGZNNXBPWWlKSE1iYTlnb0VLWSJ9.eyJhdWQiOiJodHRwczovL2dyYXBoLndpbmRv****G5gUTV-kKorR-pg"}

### <a name="get-objectid-of-application-service-principal-in-user-azure-ad"></a>Få ObjectId af programmet service hovedstolen bruger Azure AD

Nu Brug kun app adgangstoken forespørgslen [principper for Azure AD Graph Service](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#serviceprincipal-entity) API til at afgøre, objekt-Id for programmets service vigtigste i kataloget.

Metoden [GetObjectIdOfServicePrincipalInOrganization](https://github.com/dushyantgill/VipSwapper/blob/master/CloudSense/CloudSense/AzureADGraphAPIUtil.cs#) af ASP.net MVC eksempelprogrammet implementerer dette opkald.

I følgende eksempel viser, hvordan du anmoder om et program service hovedstolen. a0448380-c346-4f9f-b897-c18733de9394 er klient-id af programmet.

    GET https://graph.windows.net/62e173e9-301e-423e-bcd4-29121ec1aa24/servicePrincipals?api-version=1.5&$filter=appId%20eq%20'a0448380-c346-4f9f-b897-c18733de9394' HTTP/1.1

    Authorization: Bearer eyJ0eXAiOiJK*****-kKorR-pg

I følgende eksempel viser et svar til anmodningen om en programtjenester vigtigste 

    HTTP/1.1 200 OK

    {"odata.metadata":"https://graph.windows.net/62e173e9-301e-423e-bcd4-29121ec1aa24/$metadata#directoryObjects/Microsoft.DirectoryServices.ServicePrincipal","value":[{"odata.type":"Microsoft.DirectoryServices.ServicePrincipal","objectType":"ServicePrincipal","objectId":"9b5018d4-6951-42ed-8a92-f11ec283ccec","deletionTimestamp":null,"accountEnabled":true,"appDisplayName":"CloudSense","appId":"a0448380-c346-4f9f-b897-c18733de9394","appOwnerTenantId":"62e173e9-301e-423e-bcd4-29121ec1aa24","appRoleAssignmentRequired":false,"appRoles":[],"displayName":"CloudSense","errorUrl":null,"homepage":"http://www.vipswapper.com/cloudsense","keyCredentials":[],"logoutUrl":null,"oauth2Permissions":[{"adminConsentDescription":"Allow the application to access CloudSense on behalf of the signed-in user.","adminConsentDisplayName":"Access CloudSense","id":"b7b7338e-683a-4796-b95e-60c10380de1c","isEnabled":true,"type":"User","userConsentDescription":"Allow the application to access CloudSense on your behalf.","userConsentDisplayName":"Access CloudSense","value":"user_impersonation"}],"passwordCredentials":[],"preferredTokenSigningKeyThumbprint":null,"publisherName":"vipswapper"quot;,"replyUrls":["http://www.vipswapper.com/cloudsense","http://www.vipswapper.com","http://vipswapper.com","http://vipswapper.azurewebsites.net","http://localhost:62080"],"samlMetadataUrl":null,"servicePrincipalNames":["http://www.vipswapper.com/cloudsense","a0448380-c346-4f9f-b897-c18733de9394"],"tags":["WindowsAzureActiveDirectoryIntegratedApp"]}]}

### <a name="get-azure-rbac-role-identifier"></a>Få Azure RBAC rolle-id

Hvis du vil tildele den relevante RBAC rolle til tjenesten ydelsen, skal du bestemme id for rollen Azure RBAC.

Højre RBAC rollen for dit program:

- Hvis dit program overvåger kun abonnementet, uden at foretage eventuelle ændringer, kræver det kun Læsertilladelser på abonnementet. Tildele rollen **læser** .
- Hvis dit program administrerer Azure abonnementet, oprette/ændre/slette objekter, kræver bidragyder tilladelser.
  - Hvis du vil administrere en bestemt type ressource, tildele rollerne ressource-specifikke bidragyder (Virtual Machine bidragyder, virtuelt netværk bidragyder, lagerplads konto bidragyder osv.)
  - Hvis du vil administrere en ressourcetype, skal du tildele rollen **bidragyder** .

Rolletildeling for dit program er synligt for brugere, så Vælg mindste påkrævet rettigheder.

Ring til [ressourcestyring rolledefinition API](https://msdn.microsoft.com/library/azure/dn906879.aspx) for at få vist alle Azure RBAC roller og søg derefter forbedrer over resultatet for at finde den ønskede rolledefinition ved navn.

Metoden [GetRoleId](https://github.com/dushyantgill/VipSwapper/blob/master/CloudSense/CloudSense/AzureResourceManagerUtil.cs#L246) af ASP.net MVC eksempel app implementerer dette opkald.

Anmodning om følgende eksempel viser hvordan du kommer Azure RBAC rolle-id. 09cbd307-aa71-4aca-b346-5f253e6e3ebb er id abonnementet.

    GET https://management.azure.com/subscriptions/09cbd307-aa71-4aca-b346-5f253e6e3ebb/providers/Microsoft.Authorization/roleDefinitions?api-version=2015-07-01 HTTP/1.1

    Authorization: Bearer eyJ0eXAiOiJKV*****fY2lGc5

Svaret er placeret i følgende format: 

    HTTP/1.1 200 OK

    {"value":[{"properties":{"roleName":"API Management Service Contributor","type":"BuiltInRole","description":"Lets you manage API Management services, but not access to them.","scope":"/","permissions":[{"actions":["Microsoft.ApiManagement/Services/*","Microsoft.Authorization/*/read","Microsoft.Resources/subscriptions/resources/read","Microsoft.Resources/subscriptions/resourceGroups/read","Microsoft.Resources/subscriptions/resourceGroups/resources/read","Microsoft.Resources/subscriptions/resourceGroups/deployments/*","Microsoft.Insights/alertRules/*","Microsoft.Support/*"],"notActions":[]}]},"id":"/subscriptions/09cbd307-aa71-4aca-b346-5f253e6e3ebb/providers/Microsoft.Authorization/roleDefinitions/312a565d-c81f-4fd8-895a-4e21e48d571c","type":"Microsoft.Authorization/roleDefinitions","name":"312a565d-c81f-4fd8-895a-4e21e48d571c"},{"properties":{"roleName":"Application Insights Component Contributor","type":"BuiltInRole","description":"Lets you manage Application Insights components, but not access to them.","scope":"/","permissions":[{"actions":["Microsoft.Insights/components/*","Microsoft.Insights/webtests/*","Microsoft.Authorization/*/read","Microsoft.Resources/subscriptions/resources/read","Microsoft.Resources/subscriptions/resourceGroups/read","Microsoft.Resources/subscriptions/resourceGroups/resources/read","Microsoft.Resources/subscriptions/resourceGroups/deployments/*","Microsoft.Insights/alertRules/*","Microsoft.Support/*"],"notActions":[]}]},"id":"/subscriptions/09cbd307-aa71-4aca-b346-5f253e6e3ebb/providers/Microsoft.Authorization/roleDefinitions/ae349356-3a1b-4a5e-921d-050484c6347e","type":"Microsoft.Authorization/roleDefinitions","name":"ae349356-3a1b-4a5e-921d-050484c6347e"}]}

Du behøver ikke at ringe til denne API jævnligt. Når du har valgt den velkendte GUID for rolledefinitionen, kan du oprette rolle definition-id som:

    /subscriptions/{subscription_id}/providers/Microsoft.Authorization/roleDefinitions/{well-known-role-guid}

Her er de velkendte GUID over ofte anvendte indbyggede roller:

| Rolle | GUID |
| ----- | ------ |
| Reader | acdd72a7-3385-48ef-bd42-f606fba81ae7
| Bidragyder | b24988ac-6180-42a0-ab88-20f7382dd24c
| Virtuelt bidragyder | d73bb868-a0df-4d4d-bd69-98a00b01fccb
| Virtuelt netværk bidragyder | b34d265f-36f7-4a0d-a4d4-e158ca92e90f
| Lagerplads konto bidragyder | 86e8f5dc-a6e9-4c67-9d15-de283e8eac25
| Websted bidragyder | de139f84-1756-47ae-9be6-808fbbe84772
| Web Plan bidragyder | 2cc479cb-7b4d-49a8-b449-8c00fd0f0a4b
| SQL Server bidragyder | 6d8ee4ec-f05a-4a1d-8b00-a9b17e38b437
| SQL DB bidragyder | 9b7fa17d-e63e-47b0-bb0a-15c516ac86ec

### <a name="assign-rbac-role-to-application"></a>Tildel RBAC rolle til programmet

Har du alt, du vil tildele den relevante RBAC rolle til din primære tjenesten ved hjælp af [ressourcestyring oprette rolletildeling](https://msdn.microsoft.com/library/azure/dn906887.aspx) API.

Metoden [GrantRoleToServicePrincipalOnSubscription](https://github.com/dushyantgill/VipSwapper/blob/master/CloudSense/CloudSense/AzureResourceManagerUtil.cs#L170) af ASP.net MVC eksempel app implementerer dette opkald.

Et eksempel anmodning om at tildele RBAC rolle til programmet: 

    PUT https://management.azure.com/subscriptions/09cbd307-aa71-4aca-b346-5f253e6e3ebb/providers/microsoft.authorization/roleassignments/4f87261d-2816-465d-8311-70a27558df4c?api-version=2015-07-01 HTTP/1.1

    Authorization: Bearer eyJ0eXAiOiJKV1QiL*****FlwO1mM7Cw6JWtfY2lGc5
    Content-Type: application/json
    Content-Length: 230

    {"properties": {"roleDefinitionId":"/subscriptions/09cbd307-aa71-4aca-b346-5f253e6e3ebb/providers/Microsoft.Authorization/roleDefinitions/acdd72a7-3385-48ef-bd42-f606fba81ae7","principalId":"c3097b31-7309-4c59-b4e3-770f8406bad2"}}

I anmodningen bruges følgende værdier:

| GUID | Beskrivelse |
| ------ | --------- |
| 09cbd307-aa71-4aca-b346-5f253e6e3ebb | Abonnementets id
| c3097b31-7309-4c59-b4e3-770f8406bad2 | objekt-id for tjenesten hovedstolen af programmet
| acdd72a7-3385-48ef-bd42-f606fba81ae7 | id for læserrollen
| 4f87261d-2816-465D-8311-70a27558df4c | et nyt guid, der er oprettet for den nye rolletildeling

Svaret er placeret i følgende format: 

    HTTP/1.1 201 Created

    {"properties":{"roleDefinitionId":"/subscriptions/09cbd307-aa71-4aca-b346-5f253e6e3ebb/providers/Microsoft.Authorization/roleDefinitions/acdd72a7-3385-48ef-bd42-f606fba81ae7","principalId":"c3097b31-7309-4c59-b4e3-770f8406bad2","scope":"/subscriptions/09cbd307-aa71-4aca-b346-5f253e6e3ebb"},"id":"/subscriptions/09cbd307-aa71-4aca-b346-5f253e6e3ebb/providers/Microsoft.Authorization/roleAssignments/4f87261d-2816-465d-8311-70a27558df4c","type":"Microsoft.Authorization/roleAssignments","name":"4f87261d-2816-465d-8311-70a27558df4c"}

### <a name="get-app-only-access-token-for-azure-resource-manager"></a>Få kun app adgang token til Azure ressourcestyring

For at validere app, har den ønskede få adgang til på dit abonnement, skal du udføre en test opgave på dit abonnement ved hjælp af en app kun token.

Følg vejledningen for at få en app kun adgang token, fra sektion [få app kun adgang token til Azure AD graf API](#app-azure-ad-graph), med en anden værdi for parameteren ressource: 

    https://management.core.windows.net/

Metoden [ServicePrincipalHasReadAccessToSubscription](https://github.com/dushyantgill/VipSwapper/blob/master/CloudSense/CloudSense/AzureResourceManagerUtil.cs#L110) af programmet ASP.NET MVC eksempel henter en app kun adgang token til Azure ressourcestyring ved hjælp af Active Directory Authentication Library til .net.

#### <a name="get-applications-permissions-on-subscription"></a>Få programmets tilladelser på abonnement

Hvis du vil kontrollere, at dit program har adgang til det ønskede på et Azure-abonnement, kan du også kontakte [Ressourcestyring tilladelser](https://msdn.microsoft.com/library/azure/dn906889.aspx) API. Denne metode minder om hvordan du fastlægges om brugeren har adgang Management rettigheder til abonnementet. Denne gang ringe dog tilladelser API med kun app-adgangstoken, du har modtaget i det forrige trin.

Metoden [ServicePrincipalHasReadAccessToSubscription](https://github.com/dushyantgill/VipSwapper/blob/master/CloudSense/CloudSense/AzureResourceManagerUtil.cs#L110) af ASP.NET MVC eksempel app implementerer dette opkald.

## <a name="manage-connected-subscriptions"></a>Administrere forbundne abonnementer

Når den relevante RBAC rolle er tildelt til dit program tjenesten ydelsen på abonnementet, dit program kan holde overvågning/administration af den ved hjælp af kun app access tokens til Azure ressourcestyring.

Hvis ejeren af et abonnement fjerner programmets rolletildeling ved hjælp af klassisk portal eller kommandolinjen værktøjer, er dit program ikke længere få adgang til dette abonnement. Det er tilfældet, skal du give besked den bruger, forbindelsen til abonnementet blev vigtige fra uden for programmet og give dem mulighed for at "Reparer" forbindelsen. "Reparer" ville blot genskabe rolletildeling, der er blevet slettet offline.

Samme måde som du har aktiveret brugeren til at forbinde abonnementer i dit program, skal du tillade, at brugeren til at afbryde forbindelsen abonnementer for. Afbryd betyder, at fjerne den rolletildeling, der indeholder programmets service hovedstolen på abonnementet fra en access management synspunkt. Du kan vælge en hvilken som helst tilstand i programmet på computeren for abonnementet fjernes muligvis for. Kun brugere med management adgangstilladelse på abonnementet er i stand til at afbryde forbindelsen abonnementet.

[RevokeRoleFromServicePrincipalOnSubscription metode](https://github.com/dushyantgill/VipSwapper/blob/master/CloudSense/CloudSense/AzureResourceManagerUtil.cs#L200) til ASP.net MVC eksempel app implementerer dette opkald.

Det var det - brugere kan nu nemt oprette forbindelse og administrere deres Azure abonnementer med dit program.

