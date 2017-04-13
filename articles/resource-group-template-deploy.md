<properties
   pageTitle="Implementere ressourcer med PowerShell og skabelon | Microsoft Azure"
   description="Brug Azure ressourcestyring og Azure PowerShell til at implementere en ressourcer til Azure. Ressourcerne, der er defineret i en ressourcestyring skabelon."
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

# <a name="deploy-resources-with-resource-manager-templates-and-azure-powershell"></a>Implementere ressourcer til Ressourcestyring skabeloner og Azure PowerShell

> [AZURE.SELECTOR]
- [PowerShell](resource-group-template-deploy.md)
- [Azure CLI](resource-group-template-deploy-cli.md)
- [Portal](resource-group-template-deploy-portal.md)
- [REST-API](resource-group-template-deploy-rest.md)

Dette emne forklares det, hvordan du kan bruge Azure PowerShell med ressourcestyring skabeloner til at anvende dine ressourcer til Azure.  

> [AZURE.TIP] Hjælp til fejlfinding en fejl under installation, skal du se:
>
> - [Få vist installationshandlinger med Azure PowerShell](resource-manager-troubleshoot-deployments-powershell.md) til at få mere at vide om at få oplysninger, der hjælper dig med at foretage fejlfinding af fejlen
> - [Fejlfinding i forbindelse med almindelige fejl, når du installerer ressourcer til Azure med Azure ressourcestyring](resource-manager-common-deployment-errors.md) for at se, hvordan du kan løse almindelige installationsfejl

Skabelonen kan være enten en lokal fil eller en ekstern fil, der er tilgængelige via en URI. Når din skabelon er placeret i en lagerplads-konto, kan du begrænse adgangen til skabelonen og angiver en delt signatur (SAS) adgangstoken under installationen.

## <a name="quick-steps-to-deployment"></a>Hurtige trin til installation

I denne artikel beskrives alle de forskellige indstillinger kan du under installationen. Dog skal ofte du kun to enkle kommandoer. Du kan hurtigt komme i gang med installation skal du bruge følgende kommandoer:

    New-AzureRmResourceGroup -Name ExampleResourceGroup -Location "West US"
    New-AzureRmResourceGroupDeployment -Name ExampleDeployment -ResourceGroupName ExampleResourceGroup -TemplateFile <PathToTemplate> -TemplateParameterFile <PathToParameterFile>

Hvis du vil vide mere om indstillinger for installation, der kan være bedre velegnet til scenariet, fortsætte med at læse denne artikel.

[AZURE.INCLUDE [resource-manager-deployments](../includes/resource-manager-deployments.md)]

## <a name="deploy-with-powershell"></a>Installere med PowerShell

1. Log på din Azure-konto.

        Add-AzureRmAccount

     Der returneres en oversigt over din konto.

        Environment : AzureCloud
        Account     : someone@example.com
        ...

2. Hvis du har flere abonnementer, give det abonnement-ID, du vil bruge til installation med kommandoen **Sæt AzureRmContext** . 

        Set-AzureRmContext -SubscriptionID <YourSubscriptionId>

3. Typisk, når du installerer en ny skabelon, du vil oprette en ressourcegruppe indeholder ressourcerne. Hvis du har en eksisterende ressourcegruppe, du vil anvende til, kan du springe dette trin og bruge ressourcegruppen. 

     For at oprette en ressourcegruppe, skal du angive et navn og placering for din ressourcegruppe. Du har brug at angive en placering for ressourcegruppen, fordi ressourcegruppen indeholder metadata om ressourcerne. Overholdelse årsager, kan du vil angive, hvor pågældende metadata er gemt. Vi anbefaler Generelt, at du angiver en placering, hvor de fleste af dine ressourcer er placeret. Med den samme placering kan forenkle din skabelon.

        New-AzureRmResourceGroup -Name ExampleResourceGroup -Location "West US"
   
     Der returneres en oversigt over den nye ressourcegruppe.
   
        ResourceGroupName : ExampleResourceGroup
        Location          : westus
        ProvisioningState : Succeeded
        Tags              :
        Permissions       :
             Actions  NotActions
             =======  ==========
             *
        ResourceId        : /subscriptions/######/resourceGroups/ExampleResourceGroup

4. Før du udfører installationen, kan du kontrollere din installationsindstillinger. Cmdletten **Test AzureRmResourceGroupDeployment** gør det muligt at finde problemer, før du opretter faktiske ressourcer. I følgende eksempel viser, hvordan til at validere en installation.

        Test-AzureRmResourceGroupDeployment -ResourceGroupName ExampleResourceGroup -TemplateFile <PathToTemplate>

5. Kør kommandoen **Ny AzureRmResourceGroupDeployment** , og Angiv de nødvendige parametre for at installere ressourcer til din ressourcegruppe. Parametrene omfatter et navn til din installation, navnet på din ressourcegruppe, sti eller URL-adressen til den skabelon, du har oprettet og andre parametre, der kræves for det pågældende scenarie. Hvis parameteren **tilstand** ikke er angives, bruges af standardværdien for **tilvækst** . For at køre en komplet implementering skal du angive **tilstand** til **afsluttet**. Vær forsigtig, når du bruger tilstanden fuldført, som du kan slette ressourcer, der ikke er i skabelonen ved et uheld.

     For at installere en lokal skabelon, skal du bruge parameteren **TemplateFile** :

        New-AzureRmResourceGroupDeployment -Name ExampleDeployment -ResourceGroupName ExampleResourceGroup -TemplateFile <PathToTemplate>

     Du skal installere en ekstern skabelon, du brug **TemplateUri** parameter:

        New-AzureRmResourceGroupDeployment -Name ExampleDeployment -ResourceGroupName ExampleResourceGroup -TemplateUri <LinkToTemplate>
   
     Du har følgende indstillinger til at give parameterværdier: 
   
     1. Bruge indbyggede parametre.

            New-AzureRmResourceGroupDeployment -Name ExampleDeployment -ResourceGroupName ExampleResourceGroup -TemplateFile <PathToTemplate> -myParameterName "parameterValue"

     2. Brug et parameter-objekt.

            $parameters = @{"<ParameterName>"="<Parameter Value>"}
            New-AzureRmResourceGroupDeployment -Name ExampleDeployment -ResourceGroupName ExampleResourceGroup -TemplateFile <PathToTemplate> -TemplateParameterObject $parameters

     3. Bruge en lokal parameterfil. Du kan finde oplysninger om skabelonfilen [parameterfil](#parameter-file).

            New-AzureRmResourceGroupDeployment -Name ExampleDeployment -ResourceGroupName ExampleResourceGroup -TemplateFile <PathToTemplate> -TemplateParameterFile <PathToParameterFile>

     4. Bruge en ekstern parameterfil. Du kan finde oplysninger om skabelonfilen [parameterfil](#parameter-file). 

            New-AzureRmResourceGroupDeployment -Name ExampleDeployment -ResourceGroupName ExampleResourceGroup -TemplateUri <LinkToTemplate> -TemplateParameterUri <LinkToParameterFile>

        Når du bruger en ekstern parameterfil, du kan ikke overføre andre værdier enten indbygget eller fra en lokal fil. Du kan finde yderligere oplysninger finder [rangorden for Parameter](#parameter-precendence).

     Når ressourcerne, der er implementeret, vises en oversigt over installationen.

        DeploymentName    : ExampleDeployment
        ResourceGroupName : ExampleResourceGroup
        ProvisioningState : Succeeded
        Timestamp         : 4/14/2015 7:00:27 PM
        Mode              : Incremental
        ...

     Hvis skabelonen indeholder en parameter med samme navn som en af parametrene i PowerShell-kommando, bliver du bedt om at angive en værdi for denne parameter. Parameteren fra din skabelon, der indeholder omvendt polsk **FromTemplate**. For eksempel med en parameter navnet **ResourceGroupName** i din skabelon er i konflikt med parameteren **ResourceGroupName** i [Ny AzureRmResourceGroupDeployment](https://msdn.microsoft.com/library/azure/mt679003.aspx) cmdlet. Du bliver bedt om at angive en værdi for **ResourceGroupNameFromTemplate**. Generelt, skal du undgå denne forvirring ved at navngive ikke parametre med samme navn som parametre, der bruges i forbindelse med installation.

6. Hvis du vil logge yderligere oplysninger om den installation, der kan hjælpe dig med at foretage fejlfinding af installation fejl, skal du bruge parameteren **DeploymentDebugLogLevel** . Du kan angive, at anmodning om indhold, svar indhold eller begge logføres med handlingen installation.

        New-AzureRmResourceGroupDeployment -Name ExampleDeployment -DeploymentDebugLogLevel All -ResourceGroupName ExampleResourceGroup -TemplateFile <PathOrLinkToTemplate>
        
     Du kan finde flere oplysninger om brug af fejlfinding indholdet til at foretage fejlfinding af installationer, [fejlfinding ressource gruppe installationer med Azure PowerShell](resource-manager-troubleshoot-deployments-powershell.md).

## <a name="deploy-template-from-storage-with-sas-token"></a>Installere skabelon fra storage med SAS token

Du kan føje dine skabeloner til et lager konto og kæde under installation med et SAS token.

> [AZURE.IMPORTANT] Ved at følge nedenstående trin, er den blob, der indeholder skabelonen tilgængelig for kun ejeren af kontoen. Når du opretter et SAS token for blob, er blob dog tilgængelig for alle med URI. Hvis en anden bruger opfanger URI, er denne bruger kan få adgang til skabelonen. Ved hjælp af et SAS token er en god metode til at begrænse adgangen til dine skabeloner, men du skal ikke medtage følsomme oplysninger som adgangskoder direkte i skabelonen.

### <a name="add-private-template-to-storage-account"></a>Føje privat skabelon til lagerplads konto

Følgende trin konfigurere en lagerplads konto efter skabeloner:

1. Oprette en ressourcegruppe.

        New-AzureRmResourceGroup -Name ManageGroup -Location "West US"

2. Oprette en lagerplads konto. Kontonavn lager skal være entydige i hele Azure, så du skal angive dit eget navn for kontoen.

        New-AzureRmStorageAccount -ResourceGroupName ManageGroup -Name storagecontosotemplates -Type Standard_LRS -Location "West US"

3. Angiv kontoen for den aktuelle lagerplads.

        Set-AzureRmCurrentStorageAccount -ResourceGroupName ManageGroup -Name storagecontosotemplates

4. Oprette en beholder. Tilladelsen er angivet til **fra** hvilket betyder, at beholderen er kun tilgængeligt til ejer.

        New-AzureStorageContainer -Name templates -Permission Off
        
5. Tilføj din skabelon til objektbeholderen.

        Set-AzureStorageBlobContent -Container templates -File c:\Azure\Templates\azuredeploy.json
        
### <a name="provide-sas-token-during-deployment"></a>Angiv SAS token under installationen

Du skal installere en privat skabelon med en konto med lagerplads, hente et SAS token og medtage dem i URI til skabelonen.

1. Hvis du har ændret kontoen for den aktuelle lagerplads, kan du angive kontoen for den aktuelle lagerplads til den, der indeholder dine skabeloner.

        Set-AzureRmCurrentStorageAccount -ResourceGroupName ManageGroup -Name storagecontosotemplates

2. Oprette et SAS token med læsetilladelser og et udløbstid til at begrænse adgangen. Hent den fulde URI på den skabelon, herunder SAS tokenet.

        $templateuri = New-AzureStorageBlobSASToken -Container templates -Blob azuredeploy.json -Permission r -ExpiryTime (Get-Date).AddHours(2.0) -FullUri

3. Installer skabelonen ved at indsende den URI, der indeholder SAS tokenet.

        New-AzureRmResourceGroupDeployment -ResourceGroupName ExampleResourceGroup -TemplateUri $templateuri

Et eksempel på brug af et SAS token med sammenkædede skabeloner, se [Brug af sammenkædede skabeloner med Azure ressourcestyring](resource-group-linked-templates.md).

[AZURE.INCLUDE [resource-manager-parameter-file](../includes/resource-manager-parameter-file.md)]

## <a name="parameter-precedence"></a>Rangorden for parameter

Du kan bruge indbygget parametre og en lokal parameterfil i handlingen samme installation. Du kan for eksempel angive nogle af værdierne i parameterfilen lokale og tilføje andre værdier indbygget under installationen. Hvis du angiver værdier for en parameter i både lokale parameterfilen, og indbygget, har værdien indbygget højere prioritet.

Men du kan ikke bruge indbygget parametre til en ekstern parameterfil. Når du angiver en parameterfil i parameteren **TemplateParameterUri** , ignoreres alle parametre for indbygget. Du skal angive alle parameterværdier i den eksterne fil. Hvis skabelonen indeholder en følsomme værdi, som du kan medtage i parameterfilen, enten tilføje denne værdi til en vigtige samling af legitimationsoplysninger og referere til den vigtige samling af legitimationsoplysninger i eksterne parameterfilen eller dynamisk give alle parameter værdier indbygget.

Få mere at vide om at bruge en KeyVault til at overføre sikker værdier, skal du se [overføre sikker værdier under installationen](resource-manager-keyvault-parameter.md).

## <a name="next-steps"></a>Næste trin
- Se et eksempel med at udrulle ressourcer via biblioteket .NET klient [Implementer ressourcer ved hjælp af .NET biblioteker og en skabelon](virtual-machines/virtual-machines-windows-csharp-template.md).
- Se [redigering skabeloner](resource-group-authoring-templates.md#parameters)for at definere parametre i skabelon.
- Vejledning i installation af din løsning til forskellige miljøer, under [udvikling og testmiljøer i Microsoft Azure](solution-dev-test-environments.md).

