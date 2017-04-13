<properties
    pageTitle="Ringe til en brugerdefineret API i logik apps"
    description="Brug af din brugerdefinerede API hostes på App Service med logik apps"
    authors="stepsic-microsoft-com"
    manager="dwrede"
    editor=""
    services="logic-apps"
    documentationCenter=""/>

<tags
    ms.service="logic-apps"
    ms.workload="integration"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="05/31/2016"
    ms.author="stepsic"/>

# <a name="using-your-custom-api-hosted-on-app-service-with-logic-apps"></a>Brug af din brugerdefinerede API hostes på App Service med logik apps

Selvom logik apps indeholder et bredt udvalg af 40 + forbindelser til forskellige tjenester, kan du vil ringe op til dine egne brugerdefinerede API, som kan køre din egen programkode. En af de nemmeste og mest SVG måder til at hoste din egen brugerdefinerede web APIS er at bruge App Service. I denne artikel beskrives, hvordan du ringe til en hvilken som helst web API hostet i en App-API'EN app, online eller -mobilappen.

Se [denne artikel](app-service-logic-create-api-app.md)for oplysninger om at bygge API'er som udløser eller handling i logik apps.

## <a name="deploy-your-web-app"></a>Installer din WebApp

Først skal du installere din API som en Web App i App-tjeneste. Vejledningen her dækker grundlæggende installation: [oprette en ASP.NET WebApp](../app-service-web/web-sites-dotnet-get-started.md).  Mens du kan ringe til en hvilken som helst API fra en logik app skal du tilføje Swagger metadata til at integrere nemt med logik apps handlinger for den bedste oplevelse, vi anbefaler, at du.  Du kan finde oplysninger om [tilføjelse af swagger](../app-service-api/app-service-api-dotnet-get-started.md#use-swagger-api-metadata-and-ui).

### <a name="api-settings"></a>Indstillinger for API

Hvis logik apps designer til at fortolke din Swagger, skal er det vigtigt, at du aktiverer CORS og angive APIDefinition egenskaber for din online.  Dette er meget nemt at er angivet i portalen Azure.  Blot åbne bladet indstillinger for din online, og angiv 'API definitionen' til URL-adressen på swagger.json filen (dette er som regel https://{name}.azurewebsites.net/swagger/docs/v1) under sektionen API, og Tilføj en CORS politik for ' *' til at tillade anmodninger fra logik apps Designer.

## <a name="calling-into-the-api"></a>Ringe til API

Når du er i portalen logik apps, hvis du har angivet CORS og API Definition egenskaber du skulle kunne nemt tilføje brugerdefineret API handlinger i dit rutediagram.  Du kan vælge for at søge på dit abonnement websteder for at få vist webstederne med en swagger URL-adresse, defineret i designer.  Du kan også bruge HTTP + Swagger handling, der skal pege på en swagger og få vist tilgængelige handlinger og input.  Til sidst, kan du altid oprette en anmodning om HTTP handlingen bruges til at ringe til en hvilken som helst API, selvom de ikke har eller få vist et swagger dokument.

Hvis du vil sikre din API, er der et par forskellige måder at gøre det:

1. Ingen kodeændring er nødvendig - Azure Active Directory kan bruges til at beskytte din API uden kodeændringer eller geninstallation.
1. Gennemtvinge grundlæggende Auth, AAD Auth eller certifikat Auth i koden for din API.

## <a name="securing-calls-to-your-api-without-a-code-change"></a>Sikring af opkald til din API uden en ændring af koden

I dette afsnit, skal oprette du to Azure Active Directory-programmer – én for din logik app og én til din online.  Du skal godkende opkald til din online ved hjælp af tjenesten hovedstolen (klient-id og hemmeligt) der er knyttet til programmet AAD for din logik app. Til sidst skal du både medtager programmet id'er i din logik app definition.

### <a name="part-1-setting-up-an-application-identity-for-your-logic-app"></a>Del 1: Konfigurere et program-id for din logik app

Dette er, hvad appen logik bruger til at godkende til active directory. Du kun *har brug for* at gøre det én gang for adresselisten. Du kan for eksempel vælge at bruge den samme identitet for alle dine logik apps, selvom du kan også oprette entydige identiteter per logik app, hvis du vil. Du kan enten gøre dette i Brugergrænsefladen eller ved hjælp af PowerShell.

#### <a name="create-the-application-identity-using-the-azure-classic-portal"></a>Oprette program-id ved hjælp af portalen Azure klassisk

1. Gå til [Active directory i portalen Azure klassisk](https://manage.windowsazure.com/#Workspaces/ActiveDirectoryExtension/directory) , og vælg den mappe, du bruger til din online
2. Klik på fanen **programmer**
3. Klik på **Tilføj** i kommandolinjen i bunden af siden
4. Giv din identitet et navn til skal du klikke på pilen Næste
5. Placere i en entydig streng, der er formateret som et domæne i to tekstboksene, og klik på markeringen
6. Klik på fanen **Konfigurer** for dette program
7. Kopiere **Klient-ID**, der skal bruges i din app, logik
8. Klik på **Vælg varighed** i sektionen **nøgler** og vælge enten 1 år eller 2 år
9. Klik på knappen **Gem** i bunden af skærmen (du kan være nødvendigt at vente et par sekunder)
10. Nu skal du kopiere nøglen i feltet. Dette vil også blive brugt i din app, logik

#### <a name="create-the-application-identity-using-powershell"></a>Oprette program-id ved hjælp af PowerShell
1. `Switch-AzureMode AzureResourceManager`
2. `Add-AzureAccount`
3. `New-AzureADApplication -DisplayName "MyLogicAppID" -HomePage "http://someranddomain.tld" -IdentifierUris "http://someranddomain.tld" -Password "Pass@word1!"`
4. Sørg for at kopiere **Lejer-ID**, **Program-ID** og den adgangskode, du brugte

### <a name="part-2-protect-your-web-app-with-an-aad-app-identity"></a>Del 2: Beskytte din online med en AAD app identitet

Hvis der allerede er implementeret din online kan du aktivere den kun på portalen. Ellers kan du aktivere godkendelse en del af installationen af Azure ressource manager.

#### <a name="enable-authorization-in-the-azure-portal"></a>Aktivér godkendelse i portalen Azure

1. Gå til WebApp, og klik på **Indstillinger** i kommandolinjen.
2. Klik på **Godkendelse/godkendelse**.
3. **Aktivere det.**

På dette tidspunkt oprettes et program automatisk for dig. Du skal bruge dette program klient-ID for del 3, så du skal:

1. Gå til [Active directory i portalen Azure klassisk](https://manage.windowsazure.com/#Workspaces/ActiveDirectoryExtension/directory) og vælge adresselisten.
2. Søge efter app i søgefeltet
3. Klik på den på listen
4. Klik på fanen **Konfigurer**
5. Skal du se afsnittet **Klient-ID**

#### <a name="deploying-your-web-app-using-an-arm-template"></a>Implementering af din online ved hjælp af en ARM skabelon

Først skal du oprette et program for din online. Dette bør være forskellige fra det program, der bruges til din logik app. Starte med at følge ovenstående trin i del 1, men nu til **hjemmesiden** og **IdentifierUris** Brug faktisk https://**URL-adressen** for din online.

>[AZURE.NOTE]Når du opretter programmet til din online, skal du bruge en [Azure klassisk portalen tilgang](https://manage.windowsazure.com/#Workspaces/ActiveDirectoryExtension/directory), som PowerShell cmdlet accepterer ikke konfigureret de nødvendige tilladelser til at logge brugere på et websted.

Når du har klient-ID og lejer ID, omfatter følgende som en sub ressource af WebApp i skabelonen installation:

```
"resources" : [
    {
        "apiVersion" : "2015-08-01",
        "name" : "web",
        "type" : "config",
        "dependsOn" : [
            "[concat('Microsoft.Web/sites/','parameters('webAppName'))]"
        ],
        "properties" : {
            "siteAuthEnabled": true,
            "siteAuthSettings": {
              "clientId": "<<clientID>>",
              "issuer": "https://sts.windows.net/<<tenantID>>/",
            }
        }
    }
]
```

Hvis du vil køre en installation automatisk, installerer en tom WebApp og logik app sammen, der bruger AAD, skal du klikke på knappen følgende:

[![Installere på Azure](./media/app-service-logic-custom-hosted-api/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F201-logic-app-custom-api%2Fazuredeploy.json)

Du kan finde skabelonen fuldført [logik app opkald til en brugerdefineret API hostes på App Service og beskyttet af AAD](https://github.com/Azure/azure-quickstart-templates/blob/master/201-logic-app-custom-api/azuredeploy.json).


### <a name="part-3-populate-the-authorization-section-in-the-logic-app"></a>Del 3: Udfylde afsnittet godkendelse i appen logik

I afsnittet **godkendelse** i **HTTP** -handling:`{"tenant":"<<tenantId>>", "audience":"<<clientID from Part 2>>", "clientId":"<<clientID from Part 1>>","secret": "<<Password or Key from Part 1>>","type":"ActiveDirectoryOAuth" }`

| Element | Beskrivelse |
|---------|-------------|
| type | Type godkendelse. Værdien er ActiveDirectoryOAuth til ActiveDirectoryOAuth godkendelse. |
| lejer | Lejer id'et bruges til at identificere AD-lejeren. |
| målgruppe | Der er påkrævet. Den ressource, du opretter forbindelse til. |
| clientID | Klient-id for Azure AD-programmet. |
| hemmeligt | Der er påkrævet. Hemmeligt af den klient, der anmoder om tokenet. |

Skabelonen ovenfor har allerede dette konfigurere, men hvis du opretter logik app direkte, skal du medtage sektionen fuld godkendelse.

## <a name="securing-your-api-in-code"></a>Sikring af dine API i kode

### <a name="certificate-auth"></a>Certifikat auth

Du kan bruge klientcertifikater til at validere indgående anmodninger til din online. Se [Hvordan til at konfigurere TLS Fællesgodkendelse for Web App](../app-service-web/app-service-web-configure-tls-mutual-auth.md) til, hvordan du konfigurerer din kode.

I sektionen *godkendelse* kan du give: `{"type": "clientcertificate","password": "test","pfx": "long-pfx-key"}`.

| Element | Beskrivelse |
|---------|-------------|
| type | Der er påkrævet. Type godkendelse. Værdien skal være ClientCertificate for SSL-klientcertifikater. |
| PFX | Der er påkrævet. Base64-kodet indhold af filen PFX. |
| adgangskode | Der er påkrævet. Adgangskode for at få adgang til PFX-filen. |

### <a name="basic-auth"></a>Grundlæggende auth

Du kan bruge basisgodkendelse (fx brugernavn og din adgangskode) til at validere indgående anmodninger. Grundlæggende auth er et almindelige mønster, og du kan gøre det på en hvilken som helst sprog, du opretter din app i.

I sektionen *godkendelse* , kan du give: `{"type": "basic","username": "test","password": "test"}`.

| Element | Beskrivelse |
|---------|-------------|
| type | Der er påkrævet. Type godkendelse. Værdien skal være Basic til grundlæggende godkendelse. |
| brugernavn | Der er påkrævet. Brugernavn til at godkende. |
| adgangskode | Der er påkrævet. Adgangskode til at godkende. |

### <a name="handle-aad-auth-in-code"></a>Håndtere AAD auth i kode

Som standard ikke Azure Active Directory-godkendelse, der aktiveres i portalen detaljerede godkendelse. Den låser eksempelvis ikke dine API til en bestemt bruger eller app, men kun til en bestemt lejer.

Hvis du vil begrænse API til lige logik appen, for eksempel i kode, kan du udtrække overskriften, som indeholder JWT og Kontrollér, hvem der kalderen, afviser anmodninger, der ikke stemmer overens.

Gå videre, hvis du vil implementere helt i din egen programkode, og Udnyt ikke funktionen Portal, kan du læse denne artikel: [Brug Active Directory til godkendelse i Azure App Service](../app-service-web/web-sites-authentication-authorization.md).

Du stadig har brug at følge ovenstående trin for at oprette en program-id for din logik app og bruge den til at kalde API.
