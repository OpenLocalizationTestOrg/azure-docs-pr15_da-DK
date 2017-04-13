<properties
   pageTitle="Redigering Azure ressourcestyring skabeloner | Microsoft Azure"
   description="Oprette Azure ressourcestyring skabeloner, der bruger deklarativ JSON-syntaks til at implementere programmer til Azure."
   services="azure-resource-manager"
   documentationCenter="na"
   authors="tfitzmac"
   manager="timlt"
   editor="tysonn"/>

<tags
   ms.service="azure-resource-manager"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="10/24/2016"
   ms.author="tomfitz"/>

# <a name="authoring-azure-resource-manager-templates"></a>Redigering Azure ressourcestyring skabeloner

Dette emne beskrives strukturen i en skabelon til Azure ressourcestyring. Der vises de forskellige sektioner af en skabelon og de egenskaber, der er tilgængelige i sektionerne. Skabelonen består af JSON og udtryk, som du kan bruge til at oprette værdier til din installation. 

For at få vist skabelonen for de ressourcer, du allerede har installeret, kan du se [eksportere en Azure ressourcestyring skabelon ud fra eksisterende ressourcer](resource-manager-export-template.md). For at vide om oprettelse af en skabelon, skal du se [Ressourcestyring skabelon gennemgang](resource-manager-template-walkthrough.md). Se [bedste fremgangsmåder til oprettelse af Azure ressourcestyring skabeloner](resource-manager-template-best-practices.md)til anbefalinger om at oprette skabeloner.

En god JSON-editor til at forenkle oprettelse af skabeloner. Se [oprette og implementere Azure ressourcegrupper via Visual Studio](vs-azure-tools-resource-groups-deployment-projects-create-deploy.md)for at få oplysninger om brugen af Visual Studio med dine skabeloner. Oplysninger om brug af eller-kode, under [arbejde med Azure ressourcestyring skabeloner i Visual Studio-kode](resource-manager-vs-code.md).

Grænse din skabelon til 1 MB, og hver parameterfil til 64 KB. Grænsen på 1 MB gælder for sluttilstand på skabelonen, når den er blevet udvidet med gentagen ressourcedefinitioner og værdier for variabler og parametre. 

## <a name="template-format"></a>Skabelonformatet

I strukturen nemmeste indeholder en skabelon følgende elementer:

    {
       "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
       "contentVersion": "",
       "parameters": {  },
       "variables": {  },
       "resources": [  ],
       "outputs": {  }
    }

| Elementnavn   | Påkrævet | Beskrivelse
| :------------: | :------: | :----------
| $schema        |   Ja    | Placeringen af den JSON-skemafil, der beskriver versionen af skabelonen sproget. Brug den URL-adresse, der vises i det foregående eksempel.
| contentVersion |   Ja    | Version af skabelonen (såsom 1.0.0.0). Du kan angive en værdi for dette element. Når du installerer ressourcer ved hjælp af skabelonen, kan denne værdi bruges til at sikre dig, at den rigtige skabelon er bruges.
| parametre     |   Nej     | Værdier, der kan bruges, når installation udføres til at tilpasse ressource installation.
| variabler      |   Nej     | Værdier, der bruges som JSON fragmenter i skabelonen til at forenkle skabelon sprog udtryk.
| ressourcer      |   Ja    | Ressourcetyper, der er installeret eller opdateret i en ressourcegruppe.
| output        |   Nej     | Værdier, der returneres efter installation.

Vi undersøger afsnittene i skabelonen mere detaljeret senere i dette emne. Nu vil vi gennemgå nogle af syntaksen, der udgør skabelonen.

## <a name="expressions-and-functions"></a>Udtryk og funktioner

Grundlæggende syntaksen i skabelonen er JSON. Dog udvide udtryk og funktioner, der er tilgængelige i skabelonen JSON. Med udtryk, kan du oprette værdier, der ikke er faste ordret værdier. Udtryk er omsluttet med parenteser [og], og evalueres, når skabelonen, der er installeret. Udtryk kan vises et vilkårligt sted i en JSON strengværdi og altid returnere et andet JSON-værdi. Hvis du vil bruge en strengkonstant, der starter med en højreparentes [, skal du bruge to kantede parenteser [[.

Typisk, skal du bruge udtryk med funktioner til at udføre handlinger til at konfigurere installationen. Ligesom i JavaScript, er funktionskald formateret som **functionName(arg1,arg2,arg3)**. Du referere til egenskaber ved hjælp af operatorerne prik og [indeks].

I følgende eksempel viser, hvordan du bruger flere funktioner, når bygning af værdier:
 
    "variables": {
       "location": "[resourceGroup().location]",
       "usernameAndPassword": "[concat('parameters('username'), ':', parameters('password'))]",
       "authorizationHeader": "[concat('Basic ', base64(variables('usernameAndPassword')))]"
    }

Du kan finde den komplette liste over skabelon funktioner [Azure ressourcestyring skabelon funktioner](resource-group-template-functions.md). 


## <a name="parameters"></a>Parametre

I afsnittet parametre i skabelonen, kan du angive, hvilke værdier, du kan angive, når du installerer ressourcerne. Disse parameterværdier gør det muligt at tilpasse installationen ved at angive værdier, der er skræddersyet til et bestemt miljø (som Udviklingscenter, test og fremstilling). Du behøver ikke at angive parametre i skabelonen, men uden parametre skabelonen altid vil installere de samme ressourcer med de samme navne, placeringer og egenskaber.

Du kan bruge disse parameterværdier i hele skabelonen til at angive værdier for de ressourcer, der er installeret. Kun parametre, som er defineret i sektionen parametre kan bruges i andre dele af skabelonen.

Du kan definere parametre med følgende struktur:

    "parameters": {
       "<parameter-name>" : {
         "type" : "<type-of-parameter-value>",
         "defaultValue": "<default-value-of-parameter>",
         "allowedValues": [ "<array-of-allowed-values>" ],
         "minValue": <minimum-value-for-int>,
         "maxValue": <maximum-value-for-int>,
         "minLength": <minimum-length-for-string-or-array>,
         "maxLength": <maximum-length-for-string-or-array-parameters>,
         "metadata": {
             "description": "<description-of-the parameter>" 
         }
       }
    }

| Elementnavn   | Påkrævet | Beskrivelse
| :------------: | :------: | :----------
| parameterName  |   Ja    | Navnet på parameteren. Skal være et gyldigt JavaScript-id.
| type           |   Ja    | Værdien for parameteren type. Se nedenstående liste over tilladte typer.
| Standardværdi   |   Nej     | Standardværdien for parameteren, hvis nogen værdi er angivet for parameteren.
| allowedValues  |   Nej     | Matrix over tilladte værdier for parameteren at sikre dig, at den korrekte værdi leveres.
| minValue       |   Nej     | Denne værdi er den mindste værdi for int typeparametre, begge inklusive.
| maxValue       |   Nej     | Denne værdi er den maksimale værdi for int typeparametre, begge inklusive.
| minLength      |   Nej     | Denne værdi er minimumlængden for streng, secureString og matrix typeparametre, begge inklusive.
| maxLength      |   Nej     | Denne værdi er den maksimale længde for streng, secureString og matrix typeparametre, begge inklusive.
| Beskrivelse    |   Nej     | Beskrivelse af den parameter, der vises til brugerne af skabelonen via brugergrænsefladen på portalen brugerdefineret skabelon.

De tilladte typer og værdier er:

- **streng**
- **secureString**
- **heltal**
- **Boolesk**
- **objekt** 
- **secureObject**
- **matrix**

Hvis du vil angive en parameter som valgfri, give en standardværdi (kan være en tom streng). 

Hvis du angiver et parameternavn, der svarer til en af parametrene i kommandoen til at installere skabelonen, bliver du bedt om at angive en værdi for en parameter med omvendt polsk **FromTemplate**. Eksempelvis hvis du medtager en parameter med navnet **ResourceGroupName** i din skabelon, der er den samme som parameteren **ResourceGroupName** i [Ny AzureRmResourceGroupDeployment] [ deployment2cmdlet] cmdlet, du bliver bedt om at angive en værdi for **ResourceGroupNameFromTemplate**. Generelt, skal du undgå denne forvirring ved at navngive ikke parametre med samme navn som parametre, der bruges i forbindelse med installation.

>[AZURE.NOTE] Alle adgangskoder, nøgler og andre hemmeligheder skal bruge typen **secureString** . Skabelonparametre med typen secureString kan ikke læses efter ressource installation. 

I følgende eksempel viser, hvordan du definerer parametre:

    "parameters": {
      "siteName": {
        "type": "string",
        "defaultValue": "[concat('site', uniqueString(resourceGroup().id))]"
      },
      "hostingPlanName": {
        "type": "string",
        "defaultValue": "[concat(parameters('siteName'),'-plan')]"
      },
      "skuName": {
        "type": "string",
        "defaultValue": "F1",
        "allowedValues": [
          "F1",
          "D1",
          "B1",
          "B2",
          "B3",
          "S1",
          "S2",
          "S3",
          "P1",
          "P2",
          "P3",
          "P4"
        ]
      },
      "skuCapacity": {
        "type": "int",
        "defaultValue": 1,
        "minValue": 1
      }
    }

Til, hvordan du indtaster parameterværdierne under installationen, skal du se [Implementer et program med Azure ressourcestyring skabelon](resource-group-template-deploy.md#parameter-file). 

## <a name="variables"></a>Variabler

I sektionen variabler skal oprette du værdier, der kan bruges i hele din skabelon. Variabler er typisk baseret på værdier leveres fra parametrene. Du behøver ikke at definere variabler, men de forenkle ofte din skabelon ved at reducere komplekse udtryk.

Du kan definere variabler med følgende struktur:

    "variables": {
       "<variable-name>": "<variable-value>",
       "<variable-name>": { 
           <variable-complex-type-value> 
       }
    }

I følgende eksempel viser, hvordan du definerer en variabel, der er dannet ud fra to parameterværdier:

     "variables": {
       "connectionString": "[concat('Name=', parameters('username'), ';Password=', parameters('password'))]"
    }

Det næste eksempel viser en variabel, der er en kompleks JSON-type, og variabler, der er dannet ud fra andre variabler:

    "parameters": {
       "environmentName": {
         "type": "string",
         "allowedValues": [
           "test",
           "prod"
         ]
       }
    },
    "variables": {
       "environmentSettings": {
         "test": {
           "instancesSize": "Small",
           "instancesCount": 1
         },
         "prod": {
           "instancesSize": "Large",
           "instancesCount": 4
         }
       },
       "currentEnvironmentSettings": "[variables('environmentSettings')[parameters('environmentName')]]",
       "instancesSize": "[variables('currentEnvironmentSettings').instancesSize]",
       "instancesCount": "[variables('currentEnvironmentSettings').instancesCount]"
    }

## <a name="resources"></a>Ressourcer

I afsnittet ressourcer definerer du de ressourcer, der er installeret eller opdateret. Dette afsnit kan få kompliceret, fordi du skal kunne forstå de typer, du installerer for at give de rigtige værdier. 

Du kan definere ressourcer med følgende struktur:

    "resources": [
       {
         "apiVersion": "<api-version-of-resource>",
         "type": "<resource-provider-namespace/resource-type-name>",
         "name": "<name-of-the-resource>",
         "location": "<location-of-resource>",
         "tags": "<name-value-pairs-for-resource-tagging>",
         "comments": "<your-reference-notes>",
         "dependsOn": [
           "<array-of-related-resource-names>"
         ],
         "properties": "<settings-for-the-resource>",
         "copy": {
           "name": "<name-of-copy-loop>",
           "count": "<number-of-iterations>"
         }
         "resources": [
           "<array-of-child-resources>"
         ]
       }
    ]

| Elementnavn             | Påkrævet | Beskrivelse
| :----------------------: | :------: | :----------
| apiVersion               |   Ja    | Version af REST-API til brug for at oprette ressourcen.
| type                     |   Ja    | Type af ressourcen. Denne værdi er en kombination af navneområde provideren ressource og ressourcetype (såsom **Microsoft.Storage/storageAccounts**).
| Navn                     |   Ja    | Navnet på ressourcen. Navnet skal følge URI-komponent begrænsninger, der er defineret i RFC3986. Desuden Azure-tjenester, der viser navnet på ressourcen til eksterne parter validere på navnet for at sikre, at den er ikke et forsøg på at efterligne en anden identitet. Se [kontrollere ressourcenavn](https://msdn.microsoft.com/library/azure/mt219035.aspx).
| placering                 |   Varierer  | Understøttede geografisk-placeringer af den angivne ressource. Du kan vælge nogen af de tilgængelige placeringer, men typisk det giver mening at vælge en, der er tæt på dine brugere. Normalt også mening at placere ressourcer, der arbejder sammen med hinanden i samme område. De fleste ressourcetyper kræver en placering, men nogle typer (såsom en rolletildeling) kræver ikke en placering.
| mærker                     |   Nej     | Mærker, der er tilknyttet ressourcen.
| kommentarer                 |   Nej     | Dine noter til at dokumentere ressourcerne i din skabelon
| dependsOn                |   Nej     | Ressourcer, der afhænger af den ressource, der defineres. Afhængigheder mellem ressourcer evalueres og ressourcer, der er installeret på deres afhængige rækkefølge. Når ressourcer, der ikke er afhængige af hinanden, er de implementeret parallelt. Værdien, der kan være en kommasepareret liste over en ressource navne eller ressource entydige identifikatorer.
| Egenskaber               |   Nej     | Ressource-specifikke konfigurationsindstillinger. Værdierne for egenskaberne, er den samme som de værdier, du angiver i anmodningsteksten for handlingen REST-API (læg metode) til at oprette ressourcen. Se links til ressource skema dokumentation eller REST-API, under [ressourcestyring udbydere, områder, API versioner og skemaer](resource-manager-supported-services.md).
| Kopiér                     |   Nej     | Hvis der er brug for mere end én forekomst, hvor mange ressourcer til at oprette. Se [oprette flere forekomster af ressourcer i Azure ressourcestyring](resource-group-create-multiple.md)kan finde flere oplysninger. |
| ressourcer                |   Nej     | Underordnet ressourcer, der afhænger af den ressource, der defineres. Du kan angive ressourcetyper, der er tilladt i skemaet på den overordnede ressource. Det fulde navn på den underordnede ressourcetype indeholder den overordnede ressourcetype, som **Microsoft.Web/sites/extensions**. Afhængighed af overordnede ressourcen forudsættes ikke; Du skal eksplicit definere afhængigheden. 

Viden om hvilke værdier du skal angive for **apiVersion**, er **typen**og **placeringen** ikke umiddelbart indlysende. Heldigvis kan du se disse værdier via Azure PowerShell eller Azure CLI.

For at få alle ressource-udbydere med **PowerShell**skal du bruge:

    Get-AzureRmResourceProvider -ListAvailable

Find udbyderne ressource, du er interesseret i, på listen returnerede. For at få ressourcetyperne for en ressource udbyder (såsom Storage) skal du bruge:

    (Get-AzureRmResourceProvider -ProviderNamespace Microsoft.Storage).ResourceTypes

For at få API versionerne for en ressourcetype (sådanne lagerplads-konti) skal du bruge:

    ((Get-AzureRmResourceProvider -ProviderNamespace Microsoft.Storage).ResourceTypes | Where-Object ResourceTypeName -eq storageAccounts).ApiVersions

For at få understøttede placeringer for en af ressourcetypen skal du bruge:

    ((Get-AzureRmResourceProvider -ProviderNamespace Microsoft.Storage).ResourceTypes | Where-Object ResourceTypeName -eq storageAccounts).Locations

For at få alle ressource-udbydere med **Azure CLI**skal du bruge:

    azure provider list

Find udbyderne ressource, du er interesseret i, på listen returnerede. For at få ressourcetyperne for en ressource udbyder (såsom Storage) skal du bruge:

    azure provider show Microsoft.Storage

For at få understøttede steder og API versioner skal du bruge:

    azure provider show Microsoft.Storage --details --json

Hvis du vil vide mere om ressource udbydere, se [ressourcestyring udbydere, områder, API versioner og skemaer](resource-manager-supported-services.md).

Afsnittet ressourcer, der indeholder en matrix med ressourcerne, der skal installeres. Du kan også angive en matrix med underordnede ressourcer inden for hver ressource. I afsnittet dine ressourcer kan derfor har en struktur som:

    "resources": [
       {
           "name": "resourceA",
       },
       {
           "name": "resourceB",
           "resources": [
               {
                   "name": "firstChildResourceB",
               },
               {   
                   "name": "secondChildResourceB",
               }
           ]
       },
       {
           "name": "resourceC",
       }
    ]


I følgende eksempel viser en **Microsoft.Web/serverfarms** ressource og en **Microsoft.Web/sites** ressource med en underordnet **filtypenavne** ressource. Bemærk, at webstedet er markeret som afhænger af serverfarmen, da serverfarmen skal findes, før webstedet kan installeres. Bemærk også, at **filtypenavne** ressourcen er underordnet i forhold til webstedet.

    "resources": [
      {
        "apiVersion": "2015-08-01",
        "name": "[parameters('hostingPlanName')]",
        "type": "Microsoft.Web/serverfarms",
        "location": "[resourceGroup().location]",
        "tags": {
          "displayName": "HostingPlan"
        },
        "sku": {
          "name": "[parameters('skuName')]",
          "capacity": "[parameters('skuCapacity')]"
        },
        "properties": {
          "name": "[parameters('hostingPlanName')]",
          "numberOfWorkers": 1
        }
      },
      {
        "apiVersion": "2015-08-01",
        "type": "Microsoft.Web/sites",
        "name": "[parameters('siteName')]",
        "location": "[resourceGroup().location]",
        "tags": {
          "environment": "test",
          "team": "Web"
        },
        "dependsOn": [
          "[concat('Microsoft.Web/serverFarms/', parameters('hostingPlanName'))]"
        ],
        "properties": {
          "name": "[parameters('siteName')]",
          "serverFarmId": "[resourceId('Microsoft.Web/serverfarms', parameters('hostingPlanName'))]"
        },
        "resources": [
          {
            "apiVersion": "2015-08-01",
            "type": "extensions",
            "name": "MSDeploy",
            "dependsOn": [
              "[concat('Microsoft.Web/sites/', parameters('siteName'))]"
            ],
            "properties": {
              "packageUri": "https://auxmktplceprod.blob.core.windows.net/packages/StarterSite-modified.zip",
              "dbType": "None",
              "connectionString": "",
              "setParameters": {
                "Application Path": "[parameters('siteName')]"
              }
            }
          }
        ]
      }
    ]


## <a name="outputs"></a>Output

I sektionen output skal angive du værdier, der returneres fra installation. Du kan for eksempel returnere URI for at få adgang til en udløst ressource.

I følgende eksempel viser strukturen i en definition af output:

    "outputs": {
       "<outputName>" : {
         "type" : "<type-of-output-value>",
         "value": "<output-value-expression>"
       }
    }

| Elementnavn   | Påkrævet | Beskrivelse
| :------------: | :------: | :----------
| outputName     |   Ja    | Navnet på værdien output. Skal være et gyldigt JavaScript-id.
| type           |   Ja    | Type af værdien for output. Outputværdier understøtter den samme type som skabelon inputparametre.
| værdi          |   Ja    | Skabelon sprog udtryk, der er evalueret og returneret som outputværdi.


I følgende eksempel viser en værdi, der returneres i sektionen output.

    "outputs": {
       "siteUri" : {
         "type" : "string",
         "value": "[concat('http://',reference(resourceId('Microsoft.Web/sites', parameters('siteName'))).hostNames[0])]"
       }
    }

Du kan finde flere oplysninger om at arbejde med output [Deling tilstand i Azure ressourcestyring skabeloner](best-practices-resource-manager-state.md).

## <a name="next-steps"></a>Næste trin
- Få vist [Azure Hurtig start skabeloner](https://azure.microsoft.com/documentation/templates/)for at få vist fuldført skabeloner til mange forskellige typer løsninger.
- Få mere at vide om de funktioner, du kan bruge fra i en skabelon under [Azure ressourcestyring skabelon funktioner](resource-group-template-functions.md).
- For at kombinere flere skabeloner under installationen, skal du se [Brug af sammenkædede skabeloner med Azure ressourcestyring](resource-group-linked-templates.md).
- Du skal muligvis bruge ressourcer, der findes i en anden ressourcegruppe. Dette scenario er almindelige, når du arbejder med lagerplads firmaer eller virtuelle netværk, der deles på tværs af flere grupper. Du kan finde yderligere oplysninger finder [resourceId funktionen](resource-group-template-functions.md#resourceid).


[deployment2cmdlet]: https://msdn.microsoft.com/library/mt740620(v=azure.200).aspx
