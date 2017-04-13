<properties
   pageTitle="Implementere ressourcer med Azure CLI og skabelon | Microsoft Azure"
   description="Brug Azure ressourcestyring og Azure CLI skal installeres en ressourcer til Azure. Ressourcerne, der er defineret i en ressourcestyring skabelon."
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
   ms.date="08/15/2016"
   ms.author="tomfitz"/>

# <a name="deploy-resources-with-resource-manager-templates-and-azure-cli"></a>Implementere ressourcer til Ressourcestyring skabeloner og Azure CLI

> [AZURE.SELECTOR]
- [PowerShell](resource-group-template-deploy.md)
- [Azure CLI](resource-group-template-deploy-cli.md)
- [Portal](resource-group-template-deploy-portal.md)
- [REST-API](resource-group-template-deploy-rest.md)

Dette emne forklares, hvordan du bruger Azure CLI med ressourcestyring skabeloner til at anvende dine ressourcer til Azure.  

> [AZURE.TIP] Hjælp til fejlfinding en fejl under installation, skal du se:
>
> - [Vis installation operationer med Azure CLI](resource-manager-troubleshoot-deployments-cli.md) for at få mere at vide om at få oplysninger, der hjælper dig med at foretage fejlfinding af fejlen
> - [Fejlfinding i forbindelse med almindelige fejl, når du installerer ressourcer til Azure med Azure ressourcestyring](resource-manager-common-deployment-errors.md) for at se, hvordan du kan løse almindelige installationsfejl

Skabelonen kan være enten en lokal fil eller en ekstern fil, der er tilgængelige via en URI. Når din skabelon er placeret i en lagerplads-konto, kan du begrænse adgangen til skabelonen og angiver en delt signatur (SAS) adgangstoken under installationen.

## <a name="quick-steps-to-deployment"></a>Hurtige trin til installation

I denne artikel beskrives alle de forskellige indstillinger kan du under installationen. Dog skal ofte du kun to enkle kommandoer. Du kan hurtigt komme i gang med installation skal du bruge følgende kommandoer:

    azure group create -n ExampleResourceGroup -l "West US"
    azure group deployment create -f <PathToTemplate> -e <PathToParameterFile> -g ExampleResourceGroup -n ExampleDeployment

Hvis du vil vide mere om indstillinger for installation, der kan være bedre velegnet til scenariet, fortsætte med at læse denne artikel.

[AZURE.INCLUDE [resource-manager-deployments](../includes/resource-manager-deployments.md)]

## <a name="deploy-with-azure-cli"></a>Installere med Azure CLI

Hvis du ikke har benyttet Azure CLI med Ressourcestyring, kan du se [Brug af Azure CLI til Mac, Linux, og Windows med Azure ressourcestyring](xplat-cli-azure-resource-manager.md).

1. Log på din Azure-konto. Når du har indtastet dine legitimationsoplysninger, returnerer kommandoen resultatet af dit logon.

        azure login
  
        ...
        info:    login command OK

2. Hvis du har flere abonnementer, skal du give det abonnement-id, du vil bruge til installation.

        azure account set <YourSubscriptionNameOrId>

3. Skifte til Azure ressourcestyring modul. Du modtager bekræftelsen af den nye tilstand.

        azure config mode arm
   
        info:     New mode is arm

4. Hvis du ikke har en eksisterende ressourcegruppe, kan du oprette en ressourcegruppe. Angiv navnet på den ressourcegruppe og placering, du skal bruge til din løsning. Du har brug at angive en placering for ressourcegruppen, fordi ressourcegruppen indeholder metadata om ressourcerne. Overholdelse årsager, kan du vil angive, hvor pågældende metadata er gemt. Vi anbefaler Generelt, at du angiver en placering, hvor de fleste af dine ressourcer er placeret. Med den samme placering kan forenkle din skabelon.

        azure group create -n ExampleResourceGroup -l "West US"

     Der returneres en oversigt over den nye ressourcegruppe.
   
        info:    Executing command group create
        + Getting resource group ExampleResourceGroup
        + Creating resource group ExampleResourceGroup
        info:    Created resource group ExampleResourceGroup
        data:    Id:                  /subscriptions/####/resourceGroups/ExampleResourceGroup
        data:    Name:                ExampleResourceGroup
        data:    Location:            westus
        data:    Provisioning State:  Succeeded
        data:    Tags:
        data:
        info:    group create command OK

5. Validere din installation, inden du udfører den ved at køre kommandoen **azure Gruppeskabelon validere** . Når du tester installationen, du give parametre, præcis, som du ville gøre, når du udfører installationen (vist i næste trin).

        azure group template validate -f <PathToTemplate> -p "{\"ParameterName\":{\"value\":\"ParameterValue\"}}" -g ExampleResourceGroup

5. Køre følgende kommando for at installere ressourcer på din ressourcegruppe, og Angiv de nødvendige parametre. Parametrene omfatter et navn til din installation, navnet på din ressourcegruppe, sti eller URL-adressen til skabelonen, og andre parametre, der kræves for det pågældende scenarie. 
   
     Du har følgende tre indstillinger til at give parameterværdier: 

     1. Bruge indbyggede parametre og en lokal skabelon. Hver parameter er i formatet: `"ParameterName": { "value": "ParameterValue" }`. I følgende eksempel vises parametrene med escape-tegn.

            azure group deployment create -f <PathToTemplate> -p "{\"ParameterName\":{\"value\":\"ParameterValue\"}}" -g ExampleResourceGroup -n ExampleDeployment

     2. Bruge indbyggede parametre og et link til en skabelon.

            azure group deployment create --template-uri <LinkToTemplate> -p "{\"ParameterName\":{\"value\":\"ParameterValue\"}}" -g ExampleResourceGroup -n ExampleDeployment

     3. Bruge en parameterfil. Du kan finde oplysninger om skabelonfilen [parameterfil](#parameter-file).
    
            azure group deployment create -f <PathToTemplate> -e <PathToParameterFile> -g ExampleResourceGroup -n ExampleDeployment

     Efter ressourcerne, der er implementeret via et af disse tre metoder, får du vist en oversigt over installationen.
  
        info:    Executing command group deployment create
        + Initializing template configurations and parameters
        + Creating a deployment
        ...
        info:    group deployment create command OK

     For at køre en komplet implementering skal du angive **tilstand** til **afsluttet**. Vær forsigtig, når du bruger denne tilstand, som du kan slette ressourcer, der ikke er i skabelonen ved et uheld.

        azure group deployment create --mode Complete -f <PathToTemplate> -e <PathToParameterFile> -g ExampleResourceGroup -n ExampleDeployment

6. Hvis du vil logge yderligere oplysninger om den installation, der kan hjælpe dig med at foretage fejlfinding af installation fejl, skal du bruge parameteren **fejlfinding-indstilling** . Du kan angive, at anmodning om indhold, svar indhold eller begge logføres med handlingen installation.

        azure group deployment create --debug-setting All -f <PathToTemplate> -e <PathToParameterFile> -g ExampleResourceGroup -n ExampleDeployment

## <a name="deploy-template-from-storage-with-sas-token"></a>Installere skabelon fra storage med SAS token

Du kan føje dine skabeloner til et lager konto og kæde under installation med et SAS token.

> [AZURE.IMPORTANT] Ved at følge nedenstående trin, er den blob, der indeholder skabelonen tilgængelig for kun ejeren af kontoen. Når du opretter et SAS token for blob, er blob dog tilgængelig for alle med URI. Hvis en anden bruger opfanger URI, er denne bruger kan få adgang til skabelonen. Ved hjælp af et SAS token er en god metode til at begrænse adgangen til dine skabeloner, men du skal ikke medtage følsomme oplysninger som adgangskoder direkte i skabelonen.

### <a name="add-private-template-to-storage-account"></a>Føje privat skabelon til lagerplads konto

Følgende trin konfigurere en lagerplads konto efter skabeloner:

1. Oprette en ressourcegruppe.

        azure group create -n "ManageGroup" -l "westus"

2. Oprette en lagerplads konto. Kontonavn lager skal være entydige i hele Azure, så du skal angive dit eget navn for kontoen.

        azure storage account create -g ManageGroup -l "westus" --sku-name LRS --kind Storage storagecontosotemplates

3. Angive variabler for lagerplads konto og nøgle.

        export AZURE_STORAGE_ACCOUNT=storagecontosotemplates
        export AZURE_STORAGE_ACCESS_KEY={storage_account_key}

4. Oprette en beholder. Tilladelsen er angivet til **fra** hvilket betyder, at beholderen er kun tilgængeligt til ejer.

        azure storage container create --container templates -p Off 
        
4. Tilføj din skabelon til objektbeholderen.

        azure storage blob upload --container templates -f c:\Azure\Templates\azuredeploy.json
        
### <a name="provide-sas-token-during-deployment"></a>Angiv SAS token under installationen

Du skal installere en privat skabelon med en konto med lagerplads, hente et SAS token og medtage dem i URI til skabelonen.

1. Oprette et SAS token med læsetilladelser og et udløbstid til at begrænse adgangen. Angive udløbet tid til at tillade tid nok til at fuldføre installationen. Hent den fulde URI på den skabelon, herunder SAS tokenet.

        expiretime=$(date -I'minutes' --date "+30 minutes")
        fullurl=$(azure storage blob sas create --container templates --blob azuredeploy.json --permissions r --expiry $expiretimetime --json  | jq ".url")

2. Installer skabelonen ved at indsende den URI, der indeholder SAS tokenet.

        azure group deployment create --template-uri $fullurl -g ExampleResourceGroup

Et eksempel på brug af et SAS token med sammenkædede skabeloner, se [Brug af sammenkædede skabeloner med Azure ressourcestyring](resource-group-linked-templates.md).

[AZURE.INCLUDE [resource-manager-parameter-file](../includes/resource-manager-parameter-file.md)]

## <a name="next-steps"></a>Næste trin
- Se et eksempel med at udrulle ressourcer via biblioteket .NET klient [Implementer ressourcer ved hjælp af .NET biblioteker og en skabelon](virtual-machines/virtual-machines-windows-csharp-template.md).
- Se [redigering skabeloner](resource-group-authoring-templates.md#parameters)for at definere parametre i skabelon.
- Vejledning i installation af din løsning til forskellige miljøer, under [udvikling og testmiljøer i Microsoft Azure](solution-dev-test-environments.md).
- Få mere at vide om at bruge en KeyVault til at overføre sikker værdier, skal du se [overføre sikker værdier under installationen](resource-manager-keyvault-parameter.md).

