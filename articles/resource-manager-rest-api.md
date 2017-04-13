<properties
   pageTitle="Ressourcestyring REST API'er | Microsoft Azure"
   description="En oversigt over ressourcestyring REST API'er godkendelse for og brugen eksempler"
   services="azure-resource-manager"
   documentationCenter="na"
   authors="navalev"
   manager="timlt"
   editor=""/>

<tags
   ms.service="azure-resource-manager"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="06/23/2016"
   ms.author="navale;tomfitz;"/>
   
# <a name="resource-manager-rest-apis"></a>Ressourcestyring REST API'er

> [AZURE.SELECTOR]
- [Azure PowerShell](powershell-azure-resource-manager.md)
- [Azure CLI](xplat-cli-azure-resource-manager.md)
- [Portal](./azure-portal/resource-group-portal.md) 
- [REST-API](resource-manager-rest-api.md)

Bag hvert opkald til Azure Resource Manager bag hver udløst skabelon bag hver konto konfigureret lagerplads er der en eller flere opkald til den Azure ressourcestyring RESTful API. Dette emne giver disse API'er, og hvordan du kan ringe til dem uden at bruge en hvilken som helst SDK overhovedet. Det kan være meget nyttigt, hvis du vil have fuld kontrol over alle anmodninger til Azure, eller hvis SDK til dit foretrukne sprog er ikke tilgængelig eller understøtter ikke handlingerne, du vil udføre.

I denne artikel gennemgår ikke alle API, der vises i Azure, men hellere vil bruge nogle som et eksempel, hvordan du kan gå videre og oprette forbindelse til dem. Hvis du forstår de grundlæggende funktioner kan du gå videre og læse [Azure ressourcestyring RESTEN API Reference](https://msdn.microsoft.com/library/azure/dn790568.aspx) for at finde detaljerede oplysninger om, hvordan du bruger resten af API'erne.

## <a name="authentication"></a>Godkendelse
Godkendelse for ARM håndteres af Azure Active Directory (AD). For at oprette forbindelse til en hvilken som helst API, skal du først godkende med Azure AD til at modtage en godkendelse token, du kan overføre hver anmodning. Som vi der beskriver en ren opkald direkte til de REST API'er, antager vi også, at du ikke vil godkende med en normal brugernavn_adgangskode, hvor en pop-opskrivning skærm kan beder dig om brugernavn og din adgangskode og måske lige andre godkendelsesmetoder, der bruges i to faktor-godkendelse scenarier. Derfor vi opretter Hvad hedder et Azure AD-computerprogram og en tjeneste sikkerhedskonto, der skal bruges til logon med. Men husk, at Azure AD understøtter flere godkendelsesprocedurer og dem alle kan bruges til at hente den godkendelse token, som vi brug for efterfølgende anmodninger om API.
Følg [oprette Azure AD-program og Service princippet](./resource-group-create-service-principal-portal.md) for trinvise instruktioner.

### <a name="generating-an-access-token"></a>Generering af et adgangstoken 
Godkendelse mod Azure AD er udført af opkald til Azure AD, findes i login.microsoftonline.com. Skal have følgende oplysninger for at godkende dig:

* Azure AD-lejer ID (navnet på den Azure AD, du bruger til at logge på, ofte den samme som din virksomhed, men ikke er nødvendigt)
* Program-ID (taget under trinnet Azure AD-program til oprettelse af)
* Adgangskode (som du har valgt under oprettelse af programmet Azure AD)

I den under HTTP-anmodning sikre, at erstatte "Azure AD-lejer-ID", "Program-ID" og "Adgangskode" med de korrekte værdier.

**Generisk HTTP-anmodning:**

```HTTP
POST /<Azure AD Tenant ID>/oauth2/token?api-version=1.0 HTTP/1.1 HTTP/1.1
Host: login.microsoftonline.com
Cache-Control: no-cache
Content-Type: application/x-www-form-urlencoded

grant_type=client_credentials&resource=https%3A%2F%2Fmanagement.core.windows.net%2F&client_id=<Application ID>&client_secret=<Password>
```

... er (hvis det lykkes at godkendelse) medfører en lignende svar til dette:

```json
{
  "token_type": "Bearer",
  "expires_in": "3600",
  "expires_on": "1448199959",
  "not_before": "1448196059",
  "resource": "https://management.core.windows.net/",
  "access_token": "eyJ0eXAiOiJKV1QiLCJhb...86U3JI_0InPUk_lZqWvKiEWsayA"
}
```
(Access_token i ovenfor svaret er blevet afkortet for at øge læsbarheden)

**Opretter adgangstoken ved hjælp af fest:**

```console
curl -X POST -H "Content-Type: application/x-www-form-urlencoded" -d "grant_type=client_credentials&resource=https://management.core.windows.net&client_id=<application id>&client_secret=<password you selected for authentication>" https://login.microsoftonline.com/<Azure AD Tenant ID>/oauth2/token?api-version=1.0
```

**Opretter adgangstoken ved hjælp af PowerShell:**

```powershell
Invoke-RestMethod -Uri https://login.microsoftonline.com/<Azure AD Tenant ID>/oauth2/token?api-version=1.0 -Method Post
 -Body @{"grant_type" = "client_credentials"; "resource" = "https://management.core.windows.net/"; "client_id" = "<application id>"; "client_secret" = "<password you selected for authentication>" }
```

Svaret indeholder en Access-Token, oplysninger om, hvor lang tid, der token er gyldige og oplysninger om hvilke ressource kan du bruge dette token til.
Du har modtaget i forrige HTTP opkaldet adgangstoken skal overføres i for alle anmodning til ARM API som et sidehoved med navnet "Godkendelse" med værdien "Bæreren YOUR_ACCESS_TOKEN". Bemærk mellemrummet mellem "Bæreren" og din Access-Token.

Som det fremgår af ovenstående HTTP resultatet, gælder tokenet for et bestemt tidsrum, hvor du skal gemme i cachen og genbruge den samme token. Selvom det er muligt at godkende mod Azure AD for hver API-opkald, vil det være meget ineffektiv.

## <a name="calling-arm-rest-apis"></a>Opkald ARM REST API'er

[Azure ressourcestyring REST API'er er beskrevet her](https://msdn.microsoft.com/library/azure/dn790568.aspx) , og det er uden for området for dette selvstudium til at dokumentere brugen af hver og alle. Brug denne dokumentation kun nogle få API'er til at forklare grundlæggende brug af API'erne og efter, som vi se du officielle dokumentationen til.

### <a name="list-all-subscriptions"></a>Liste over alle abonnementer

Én af de mest enkle handlinger, du kan gøre er at vise de tilgængelige abonnementer, du kan få adgang til. I den under anmodningen, kan du se, hvordan Access-Token er givet som et sidehoved.

(Erstat YOUR_ACCESS_TOKEN med dine faktiske Access Token).

```HTTP
GET /subscriptions?api-version=2015-01-01 HTTP/1.1
Host: management.azure.com
Authorization: Bearer YOUR_ACCESS_TOKEN
Content-Type: application/json
```

… og som et resultat, får du vist en liste over de abonnementer, denne tjeneste hovedstolen har tilladelse til at få adgang til

(Abonnement id'er nedenfor er blevet afkortet for læsbarhed)

```json
{
  "value": [
    {
      "id": "/subscriptions/3a8555...555995",
      "subscriptionId": "3a8555...555995",
      "displayName": "Azure Subscription",
      "state": "Enabled",
      "subscriptionPolicies": {
        "locationPlacementId": "Internal_2014-09-01",
        "quotaId": "Internal_2014-09-01"
      }
    }
  ]
}
```

### <a name="list-all-resource-groups-in-a-specific-subscription"></a>Liste over alle grupper i et bestemt abonnement

Alle de ressourcer, der er tilgængelige med ARM API'er er indlejret i en ressourcegruppe. Vi vil forespørgsel ARM for eksisterende ressourcegrupper på vores abonnement ved hjælp af den under HTTP GET-anmodning. Bemærk, hvordan abonnement-ID angives i som en del af URL-adressen nuværende tidspunkt.

(Erstatte YOUR_ACCESS_TOKEN og SUBSCRIPTION_ID med din faktiske Access Token og abonnement-ID)

```HTTP
GET /subscriptions/SUBSCRIPTION_ID/resourcegroups?api-version=2015-01-01 HTTP/1.1
Host: management.azure.com
Authorization: Bearer YOUR_ACCESS_TOKEN
Content-Type: application/json
```

Du får svaret, afhænger af om du har en hvilken som helst ressourcegrupper, der er angivet, og hvis det er tilfældet, hvor mange.

(Abonnement id'er nedenfor er blevet afkortet for læsbarhed)

```json
{
    "value": [
        {
            "id": "/subscriptions/3a8555...555995/resourceGroups/myfirstresourcegroup",
            "name": "myfirstresourcegroup",
            "location": "eastus",
            "properties": {
                "provisioningState": "Succeeded"
            }
        },
        {
            "id": "/subscriptions/3a8555...555995/resourceGroups/mysecondresourcegroup",
            "name": "mysecondresourcegroup",
            "location": "northeurope",
            "tags": {
                "tagname1": "My first tag"
            },
            "properties": {
                "provisioningState": "Succeeded"
            }
        }
    ]
}
```

### <a name="create-a-resource-group"></a>Oprette en ressourcegruppe

Hidtil vi har kun forespørgsler ARM API'er oplysninger, er det tid vi oprette nogle ressourcer i stedet, og Lad os starte med den enkleste af dem alle, en ressourcegruppe. Følgende HTTP-anmodningen opretter en ny ressourcegruppe i et område placering efter eget valg og tilføjer en eller flere mærker til den (eksemplet under faktisk kun tilføjer en kode).

(Erstatte YOUR_ACCESS_TOKEN, SUBSCRIPTION_ID, RESOURCE_GROUP_NAME med din faktiske Access Token, abonnement-ID og navnet på den ressourcegruppe, du vil oprette)

```HTTP
PUT /subscriptions/SUBSCRIPTION_ID/resourcegroups/RESOURCE_GROUP_NAME?api-version=2015-01-01 HTTP/1.1
Host: management.azure.com
Authorization: Bearer YOUR_ACCESS_TOKEN
Content-Type: application/json

{
  "location": "northeurope",
  "tags": {
    "tagname1": "test-tag"
  }
}
```

Hvis det lykkes, får du et lignende svar til denne

```json
{
  "id": "/subscriptions/3a8555...555995/resourceGroups/RESOURCE_GROUP_NAME",
  "name": "RESOURCE_GROUP_NAME",
  "location": "northeurope",
  "tags": {
    "tagname1": "test-tag"
  },
  "properties": {
    "provisioningState": "Succeeded"
  }
}
```

Du har oprettet en ressourcegruppe i Azure. Tillykke!

### <a name="deploy-resources-to-a-resource-group-using-an-arm-template"></a>Implementere ressourcer til en ressourcegruppe ved hjælp af en ARM skabelon

Med ARM, kan du installere dine ressourcer ved hjælp af ARM skabeloner. Skabelon til en ARM definerer flere ressourcer og deres afhængigheder. I dette afsnit antager vi blot du allerede kender, ARM skabeloner og vi vil kun viser, hvordan du foretager opkaldet API til at starte installationen af en. En detaljeret dokumentation ARM skabeloner kan findes her.

Installation af en skabelon til ARM ikke er forskellige meget hvordan du ringe til andre API'er. Et vigtigt aspekt er, at installation af en skabelon kan tage meget lang tid, afhængigt af hvad er inde i skabelonen, og API-kaldet lige returnerer og det er til dig som udvikler til forespørgsel til status for installationen for at finde ud af, når installationen er færdig.

I dette eksempel skal bruger vi en offentligt tilgængelig ARM skabelon tilgængelig på [GitHub](https://github.com/Azure/azure-quickstart-templates). Skabelonen skal vi bruge installere en Linux VM i området Vest USA. Selvom denne skabelon har den skabelon, der er tilgængelige i et offentligt lager som GitHub, kan du også vælge for at overføre den fulde skabelon som en del af anmodningen. Bemærk, at vi giver dig parameterværdier som en del af anmodningen, der skal bruges i den anvendte skabelon.

(Erstatte SUBSCRIPTION_ID, RESOURCE_GROUP_NAME, DEPLOYMENT_NAME, YOUR_ACCESS_TOKEN, GLOBALY_UNIQUE_STORAGE_ACCOUNT_NAME, ADMIN_USER_NAME, ADMIN_PASSWORD og DNS_NAME_FOR_PUBLIC_IP til værdier, der er relevante for din anmodning)

```HTTP
PUT /subscriptions/SUBSCRIPTION_ID/resourcegroups/RESOURCE_GROUP_NAME/providers/microsoft.resources/deployments/DEPLOYMENT_NAME?api-version=2015-01-01 HTTP/1.1
Host: management.azure.com
Authorization: Bearer YOUR_ACCESS_TOKEN
Content-Type: application/json

{
  "properties": {
    "templateLink": {
      "uri": "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-simple-linux-vm/azuredeploy.json",
      "contentVersion": "1.0.0.0",
    },
    "mode": "Incremental",
    "parameters": {
        "newStorageAccountName": {
          "value": "GLOBALY_UNIQUE_STORAGE_ACCOUNT_NAME"
        },
        "adminUsername": {
          "value": "ADMIN_USER_NAME"
        },
        "adminPassword": {
          "value": "ADMIN_PASSWORD"
        },
        "dnsNameForPublicIP": {
          "value": "DNS_NAME_FOR_PUBLIC_IP"
        },
        "ubuntuOSVersion": {
          "value": "15.04"
        }
    }
  }
}
```

Helt lang JSON svaret for denne anmodning er udeladt for at forbedre læsbarheden af denne dokumentation. Svaret skal indeholde oplysninger om den skabelonbaserede installation, du lige har oprettet.

