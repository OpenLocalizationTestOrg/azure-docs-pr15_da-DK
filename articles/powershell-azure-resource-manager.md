<properties 
    pageTitle="Azure PowerShell med ressourcestyring | Microsoft Azure" 
    description="Introduktion til brug af Azure PowerShell til at udrulle flere ressourcer som en ressourcegruppe til Azure." 
    services="azure-resource-manager" 
    documentationCenter="" 
    authors="tfitzmac" 
    manager="timlt" 
    editor="tysonn"/>

<tags 
    ms.service="azure-resource-manager" 
    ms.workload="multiple" 
    ms.tgt_pltfrm="powershell" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="08/18/2016" 
    ms.author="tomfitz"/>

# <a name="using-azure-powershell-with-azure-resource-manager"></a>Brug af Azure PowerShell med Azure ressourcestyring

> [AZURE.SELECTOR]
- [Portal](azure-portal/resource-group-portal.md) 
- [Azure CLI](xplat-cli-azure-resource-manager.md)
- [Azure PowerShell](powershell-azure-resource-manager.md)
- [REST-API](resource-manager-rest-api.md)


Azure ressourcestyring implementerer et moderne metode til Azure ressourcer livscyklus kontrolelement. I stedet for oprettelse og administration af individuelle ressourcer, kan du begynde med forestiller sig en hel løsning, som en blog, en Billedgalleri, en SharePoint-portal eller en wiki. Du kan bruge en skabelon – en deklarativ repræsentation af løsningen – til at definere en ressourcegruppe, der indeholder alle de ressourcer, du har brug for at understøtte løsningen. Derefter skal du installere og administrere ressourcegruppen som en logisk enhed. 

I dette selvstudium lærer du at bruge Azure PowerShell med Azure ressourcestyring. Det vejleder dig gennem processen med at implementere en løsning, og arbejde med denne løsning. Du vil bruge Azure PowerShell og en ressourcestyring skabelon til at udrulle:

- SQL server - vært for databasen
- SQL database - til at gemme dataene
- Firewallregler - tillader web app til at oprette forbindelse til databasen
- App-serviceaftale – til at definere de funktioner og omkostninger af WebApp
- Websted - til kørsel af WebApp
- Web config - til lagring af forbindelsesstrengen til databasen 
- Regler for påmindelser - for overvågning af ydeevnen og fejl
- App indsigt - til indstillinger for automatisk skalering

## <a name="prerequisites"></a>Forudsætninger

For at fuldføre dette selvstudium skal bruge du:

- En Azure-konto
  + Du kan [åbne en Azure-konto gratis](/pricing/free-trial/?WT.mc_id=A261C142F): du får kredit du kan bruge til at prøve betalt Azure tjenester, og selv efter at de er vant op du holder kontoen, og brug ledig Azure tjenester, som websteder. Dit kreditkort vil aldrig blive faktureret, medmindre du eksplicit ændre dine indstillinger og bede om at betale.
  
  + Du kan [aktivere MSDN abonnement fordele](/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A261C142F): din MSDN-abonnement giver dig kredit hver måned, som du kan bruge til betalt Azure services.
- Azure PowerShell 1.0. Finde oplysninger om denne version, og hvordan du installerer programmet, se, [hvordan du installerer og konfigurerer Azure PowerShell](powershell-install-configure.md).

Dette selvstudium henvender sig til PowerShell begyndere, men det antages, at du forstår de grundlæggende begreber, som moduler, cmdletter og sessioner.

## <a name="get-help-for-cmdlets"></a>Få hjælp til cmdletter

Brug cmdlet'en Get-Help for at få detaljeret hjælp til en hvilken som helst cmdlet, der vises i dette selvstudium. 

    Get-Help <cmdlet-name> -Detailed

Skriv for at få hjælp til Cmdletten Get-AzureRmResource, f.eks.:

    Get-Help Get-AzureRmResource -Detailed

For at få en liste over cmdletter i modulet ressourcer med en hjælp-opsummering skal du skrive: 

    Get-Command -Module AzureRM.Resources | Get-Help | Format-Table Name, Synopsis

Output skal ligne følgende uddrag:

    Name                                   Synopsis
    ----                                   --------
    Find-AzureRmResource                   Searches for resources using the specified parameters.
    Find-AzureRmResourceGroup              Searches for resource group using the specified parameters.
    Get-AzureRmADGroup                     Filters active directory groups.
    Get-AzureRmADGroupMember               Get a group members.
    ...

Hvis du vil have fuld hjælp til en-cmdlet, skal du skrive en kommando med formatet:

    Get-Help <cmdlet-name> -Full
  
## <a name="login-to-your-azure-account"></a>Log på din konto for Azure

Før du arbejder på din løsning, skal du logge på din konto.

Brug cmdlet'en **Tilføj AzureRmAccount** for at logge på din Azure-konto.

    Add-AzureRmAccount

Cmdletten beder dig om logonoplysninger til kontoen Azure. Når du logger på, henter den dine kontoindstillinger, så de er tilgængelige til Azure PowerShell. 

Kontoindstillingerne udløbe, så du behøver at opdatere dem nogle gange. Kør **Tilføj AzureRmAccount** igen for at opdatere indstillingerne for kontoen. 

>[AZURE.NOTE] Ressourcestyring moduler kræver Tilføj AzureRmAccount. En publiceringsindstillinger filen er ikke tilstrækkelige.     

Hvis du har mere end ét abonnement, give det abonnement-id, du vil bruge til installation med cmdlet'en **Set-AzureRmContext** til.

    Set-AzureRmContext -SubscriptionID <YourSubscriptionId>

## <a name="create-a-resource-group"></a>Oprette en ressourcegruppe

Før du anvender en hvilken som helst ressourcer til dit abonnement, skal du oprette en ressourcegruppe, der indeholder ressourcerne. 

Brug **Ny AzureRmResourceGroup** cmdlet til at oprette en ressourcegruppe.

Kommandoen bruger parameteren **Name** til at angive et navn til ressourcegruppen og parameteren **placering** til at angive dens placering. Afhængigt af, hvad vi har opdaget i forrige afsnit, skal vi bruge "Vest USA" til placeringen.

    New-AzureRmResourceGroup -Name TestRG1 -Location "West US"
    
Output bliver ligner:

    ResourceGroupName : TestRG1
    Location          : westus
    ProvisioningState : Succeeded
    Tags              :
    ResourceId        : /subscriptions/{guid}/resourceGroups/TestRG1

Din ressourcegruppe er blevet oprettet.

## <a name="deploy-your-solution"></a>Installere løsningen

Dette emne viser du ikke hvordan du opretter din skabelon eller diskutere strukturen i skabelonen. Disse oplysninger under [redigering Azure ressourcestyring skabeloner](resource-group-authoring-templates.md) og [Ressourcestyring skabelon gennemgang](resource-manager-template-walkthrough.md). Du vil installere skabelonen foruddefinerede [klargøring en Web App med en SQL-Database](https://azure.microsoft.com/documentation/templates/201-web-app-sql-database/) fra [Azure Hurtig start skabeloner](https://azure.microsoft.com/documentation/templates/).

Du har din ressourcegruppe, og du har skabelonen, så du er nu klar til at installere den infrastruktur, der er defineret i din skabelon til ressourcegruppen. Du installerer ressourcer med **Ny AzureRmResourceGroupDeployment** cmdlet. Skabelonen angiver mange standardværdier, som vi skal bruge, så du ikke behøver at angive værdier for disse parametre. Grundlæggende syntaksen ser sådan ud:

    New-AzureRmResourceGroupDeployment -ResourceGroupName TestRG1 -administratorLogin exampleadmin -TemplateUri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-web-app-sql-database/azuredeploy.json 

Du angiver ressourcegruppen og placeringen af skabelonen. Hvis skabelonen er en lokal fil, kan du bruge parameteren **- TemplateFile** og angive stien til skabelonen. Du kan angive den **-tilstand** -parameter til **tilvækst** eller **afsluttet**. Som standard udfører ressourcestyring en trinvis opdatering under installationen; det er derfor ikke nødvendigt at angive **-tilstand** når du vil **tilvækst**. For at forstå forskellene mellem disse tilstande installation, skal du se [Implementer et program med Azure ressourcestyring skabelon](resource-group-template-deploy.md). 

###<a name="dynamic-template-parameters"></a>Dynamisk skabelonparametre

Hvis du er bekendt med PowerShell, ved du, at du kan navigere gennem de tilgængelige parametre for en cmdlet ved at skrive et minustegn (-) og derefter trykke på tabulatortasten. Denne samme funktionalitet fungerer også med parametre, du angiver i skabelonen. Så snart du skriver navnet på skabelonen, cmdlet henter skabelonen, fortolker den, og tilføjer skabelonparametre til kommandoen dynamisk. Dette gør det meget nemt at angive parameterværdier skabelon.

Når du angiver kommandoen, bliver du bedt om den manglende obligatoriske parameter, **administratorLoginPassword**. Og når du skriver adgangskoden, secure strengværdien er skjult. Denne strategi fjerner risikoen for at angive en adgangskode i almindelig tekst.

    cmdlet New-AzureRmResourceGroupDeployment at command pipeline position 1
    Supply values for the following parameters:
    (Type !? for Help.)
    administratorLoginPassword: ********

Hvis skabelonen indeholder en parameter med et navn, der svarer til en af parametrene i kommandoen til at installere skabelonen (såsom herunder en parameter med navnet **ResourceGroupName** i din skabelon som er den samme som parameteren **ResourceGroupName** i [Ny AzureRmResourceGroupDeployment](https://msdn.microsoft.com/library/azure/mt679003.aspx) cmdlet), bliver du bedt om at angive en værdi for en parameter med omvendt polsk **FromTemplate** (såsom **ResourceGroupNameFromTemplate**). Generelt, skal du undgå denne forvirring ved at navngive ikke parametre med samme navn som parametre, der bruges i forbindelse med installation.

Kommandoen kører og returnerer meddelelser, som ressourcerne, der er oprettet. I sidste ende, kan du se resultatet af din installation.

    DeploymentName    : azuredeploy
    ResourceGroupName : TestRG1
    ProvisioningState : Succeeded
    Timestamp         : 4/11/2016 7:26:11 PM
    Mode              : Incremental
    TemplateLink      :
                Uri            : https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-web-app-sql-database/azuredeploy.json
                ContentVersion : 1.0.0.0
    Parameters        :
                Name             Type                       Value
                ===============  =========================  ==========
                skuName          String                     F1
                skuCapacity      Int                        1
                administratorLogin  String                  exampleadmin
                administratorLoginPassword  SecureString
                databaseName     String                     sampledb
                collation        String                     SQL_Latin1_General_CP1_CI_AS
                edition          String                     Basic
                maxSizeBytes     String                     1073741824
                requestedServiceObjectiveName  String       Basic

    Outputs           :
                Name             Type                       Value
                ===============  =========================  ==========
                siteUri          String                     websites5wdai7p2k2g4.azurewebsites.net
                sqlSvrFqdn       String                     sqlservers5wdai7p2k2g4.database.windows.net
                    
    DeploymentDebugLogLevel :

I blot nogle få trin, vi oprettede og installeret de ressourcer, der kræves for et komplekst websted. 

### <a name="log-debug-information"></a>Log fejlfindingsoplysninger

Når du installerer en skabelon, kan du logge yderligere oplysninger om anmodning og svar ved at angive parameteren **- DeploymentDebugLogLevel** , når du kører **Ny AzureRmResourceGroupDeployment**. Disse oplysninger kan hjælpe dig med at foretage fejlfinding af installation fejl. Standardværdien er **ingen** som betyder ingen anmodning eller svar indhold logføres. Du kan angive logføring indholdet fra anmodningen, svar eller begge dele.  Du kan finde flere oplysninger om fejlfinding i forbindelse med installationer og logføring fejlfindingsoplysninger [fejlfinding ressource gruppe installationer med Azure PowerShell](resource-manager-troubleshoot-deployments-powershell.md). I følgende eksempel registrerer anmodning om indhold og svar indhold til installationen.

    New-AzureRmResourceGroupDeployment -ResourceGroupName TestRG1 -DeploymentDebugLogLevel All -TemplateUri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-web-app-sql-database/azuredeploy.json 

> [AZURE.NOTE] Når du angiver parameteren DeploymentDebugLogLevel, nøje overveje typer oplysninger, du sender under installationen. Ved logføringsoplysninger om anmodningen eller svar, kan du eventuelt udsætte følsomme data, som er hentet via handlingerne installation. 


## <a name="get-information-about-your-resource-groups"></a>Få oplysninger om dine ressourcegrupper

Når du har oprettet en ressourcegruppe, kan du bruge cmdletter i modulet ressourcestyring til at administrere dine ressourcegrupper.

- Brug cmdlet'en **Get-AzureRmResourceGroup** for at få en ressourcegruppe i dit abonnement:

        Get-AzureRmResourceGroup -ResourceGroupName TestRG1
    
    Som returnerer følgende oplysninger:

        ResourceGroupName : TestRG1
        Location          : westus
        ProvisioningState : Succeeded
        Tags              :
        ResourceId        : /subscriptions/{guid}/resourceGroups/TestRG
        
        ...

    Hvis du ikke angiver en ressource gruppenavn, returnerer cmdlet alle ressourcegrupperne i dit abonnement.

- Brug **Find AzureRmResource** cmdlet og dens **ResourceGroupNameContains** -parameter for at få ressourcerne i ressourcegruppen. Find AzureRmResource får uden parametre, alle de ressourcer, i abonnementet Azure.

        Find-AzureRmResource -ResourceGroupNameContains TestRG1
    
     Som returnerer en liste over ressourcer, der er formateret som:
        
        Name              : sqlservers5wdai7p2k2g4
        ResourceId        : /subscriptions/{guid}/resourceGroups/TestRG1/providers/Microsoft.Sql/servers/sqlservers5wdai7p2k2g4
        ResourceName      : sqlservers5wdai7p2k2g4
        ResourceType      : Microsoft.Sql/servers
        Kind              : v2.0
        ResourceGroupName : TestRG1
        Location          : westus
        SubscriptionId    : {guid}
        Tags              : {System.Collections.Hashtable}
        ...
            
- Du kan bruge mærker til at organisere ressourcerne i dit abonnement logisk og hente ressourcer med **Find AzureRmResource** og **Finde AzureRmResourceGroup** cmdletter.

        Find-AzureRmResource -TagName displayName -TagValue Website

        Name              : webSites5wdai7p2k2g4
        ResourceId        : /subscriptions/{guid}/resourceGroups/TestRG1/providers/Microsoft.Web/sites/webSites5wdai7p2k2g4
        ResourceName      : webSites5wdai7p2k2g4
        ResourceType      : Microsoft.Web/sites
        ResourceGroupName : TestRG1
        Location          : westus
        SubscriptionId    : {guid}
                
      There is much more you can do with tags. For more information, see [Using tags to organize your Azure resources](resource-group-using-tags.md).

## <a name="add-to-a-resource-group"></a>Føje til en ressourcegruppe

Hvis du vil føje en ressource til ressourcegruppen, kan du bruge Cmdletten **Ny AzureRmResource** . Dog kan tilføje en ressource på denne måde medføre fremtidige forvirring fordi den nye ressource ikke findes i din skabelon. Hvis du har installeret den tidligere skabelon igen, skal du installere en ufuldstændig løsning. Hvis du anvender ofte, kan du finde det nemmere og mere pålidelig at føje den nye ressource til skabelonen og installere det igen.

## <a name="move-a-resource"></a>Flytte en ressource

Du kan flytte eksisterende ressourcer til en ny ressourcegruppe. Du kan finde eksempler, [Flytte ressourcer til ny ressourcegruppe eller et andet abonnement](resource-group-move-resources.md).

## <a name="export-template"></a>Eksport skabelon

Til en eksisterende ressourcegruppe (implementeres via PowerShell eller en af de andre metoder som portalen), kan du få vist skabelonen Ressourcestyring for ressourcegruppen. Eksportere skabelonen, der indeholder to fordele:

1. Du kan nemt automatisere fremtidige installationer af løsningen, fordi alle infrastrukturen er defineret i skabelonen.

2. Du kan blive fortrolig med skabelon syntaksen ved at kigge på JavaScript Object Notation (JSON), der repræsenterer din løsning.

Via PowerShell, kan du enten oprette en skabelon, der repræsenterer den aktuelle tilstand for dit ressourcegruppe eller hente den skabelon, der blev brugt til en bestemt installation.

Eksport af skabelonen for en ressourcegruppe er nyttigt, når du har foretaget ændringer i en ressourcegruppe, og brug for at hente JSON repræsentationen af den aktuelle tilstand. Skabelonen genererede indeholder dog kun et minimale antal parametre og ingen variabler. De fleste af værdierne i skabelonen er faste. Før du anvender den oprettede skabelon, kan du konvertere flere af værdierne i parametre, så du kan tilpasse installationen til forskellige miljøer.

Eksport af skabelonen for en bestemt installation er nyttigt, når du har brug at få vist den faktiske skabelon, der blev brugt til at implementere ressourcer. Skabelonen omfatter alle parametre og variabler, der er defineret for den oprindelige installation. Men hvis nogen i organisationen har foretaget ændringer i ressourcegruppen uden for det er defineret i skabelonen, denne skabelon ikke repræsenterer den aktuelle tilstand for ressourcegruppen.

> [AZURE.NOTE] Funktionen Eksportér skabelon i Vis udskrift, og ikke alle ressourcetyper understøtter eksport af en skabelon. Når du forsøger at eksportere en skabelon, vises muligvis en fejlmeddelelse om nogle ressourcer, der ikke blev eksporteret. Hvis det er nødvendigt, kan du manuelt definere disse ressourcer i skabelonen efter hente den.

###<a name="export-template-from-resource-group"></a>Eksportere skabelon fra ressourcegruppe

Køre **Eksportér AzureRmResourceGroup** cmdlet for at få vist skabelonen til en ressourcegruppe.

    Export-AzureRmResourceGroup -ResourceGroupName TestRG1 -Path c:\Azure\Templates\Downloads\TestRG1.json
    
###<a name="download-template-from-deployment"></a>Hent skabelon fra installation

For at hente skabelonen bruges til en bestemt installation, skal du køre følgende **Gem AzureRmResourceGroupDeploymentTemplate** cmdlet.

    Save-AzureRmResourceGroupDeploymentTemplate -DeploymentName azuredeploy -ResourceGroupName TestRG1 -Path c:\Azure\Templates\Downloads\azuredeploy.json

## <a name="delete-resources-or-resource-group"></a>Slette ressourcer eller ressourcegruppe

- Brug cmdlet'en **Fjern AzureRmResource** , hvis du vil slette en ressource fra ressourcegruppen. Denne cmdlet sletter ressourcen, men sletter ikke ressourcegruppen.

    Denne kommando fjerner webstedet TestSite fra gruppen TestRG1 ressource.

        Remove-AzureRmResource -Name TestSite -ResourceGroupName TestRG1 -ResourceType "Microsoft.Web/sites" -ApiVersion 2015-08-01

- Brug cmdlet'en **Fjern AzureRmResourceGroup** , hvis du vil slette en ressourcegruppe. Denne cmdlet sletter ressourcegruppen og dens ressourcer.

        Remove-AzureRmResourceGroup -Name TestRG1
        
    Du bliver bedt om at bekræfte sletningen.
        
        Confirm
        Are you sure you want to remove resource group 'TestRG1'
        [Y] Yes  [N] No  [S] Suspend  [?] Help (default is "Y"): Y

## <a name="deployment-script"></a>Installation script

Tidligere installation eksemplerne i dette emne, der blev vist kun de enkelte cmdletter bruges til at implementere ressourcer til Azure. I følgende eksempel viser et installation script, som opretter ressourcegruppen og installerer ressourcerne.

    <#
      .SYNOPSIS
      Deploys a template to Azure
      
      .DESCRIPTION
      Deploys an Azure Resource Manager template

      .PARAMETER subscriptionId
      The subscription id where the template will be deployed.

      .PARAMETER resourceGroupName
      The resource group where the template will be deployed. Can be the name of an existing or a new resource group.

      .PARAMETER resourceGroupLocation
      Optional, a resource group location. If specified, will try to create a new resource group in this location. If not specified, assumes resource group is existing.

      .PARAMETER deploymentName
      The deployment name.

      .PARAMETER templateFilePath
      Optional, path to the template file. Defaults to template.json.

      .PARAMETER parametersFilePath
      Optional, path to the parameters file. Defaults to parameters.json. If file is not found, will prompt for parameter values based on template.
    #>

    param(
      [Parameter(Mandatory=$True)]
      [string]
      $subscriptionId,

      [Parameter(Mandatory=$True)]
      [string]
      $resourceGroupName,

      [string]
      $resourceGroupLocation,

      [Parameter(Mandatory=$True)]
      [string]
      $deploymentName,

      [string]
      $templateFilePath = "template.json",

      [string]
      $parametersFilePath = "parameters.json"
    )

    #******************************************************************************
    # Script body
    # Execution begins here 
    #******************************************************************************
    $ErrorActionPreference = "Stop"

    # sign in
    Write-Host "Logging in...";
    Add-AzureRmAccount;

    # select subscription
    Write-Host "Selecting subscription '$subscriptionId'";
    Set-AzureRmContext -SubscriptionID $subscriptionId;

    #Create or check for existing resource group
    $resourceGroup = Get-AzureRmResourceGroup -Name $resourceGroupName -ErrorAction SilentlyContinue
    if(!$resourceGroup)
    {
      Write-Host "Resource group '$resourceGroupName' does not exist. To create a new resource group, please enter a location.";
      if(!$resourceGroupLocation) {
        $resourceGroupLocation = Read-Host "resourceGroupLocation";
      }
      Write-Host "Creating resource group '$resourceGroupName' in location '$resourceGroupLocation'";
      New-AzureRmResourceGroup -Name $resourceGroupName -Location $resourceGroupLocation
    }
    else{
      Write-Host "Using existing resource group '$resourceGroupName'";
    }

    # Start the deployment
    Write-Host "Starting deployment...";
    if(Test-Path $parametersFilePath) {
      New-AzureRmResourceGroupDeployment -ResourceGroupName $resourceGroupName -TemplateFile $templateFilePath -TemplateParameterFile $parametersFilePath;
    } else {
      New-AzureRmResourceGroupDeployment -ResourceGroupName $resourceGroupName -TemplateFile $templateFilePath;
    }

## <a name="next-steps"></a>Næste trin

- Se [Redigering Azure ressourcestyring skabeloner](./resource-group-authoring-templates.md)for at få mere for at vide om oprettelse af Ressourcestyring skabeloner.
- Se [Implementer et program med Azure ressourcestyring skabelon](./resource-group-template-deploy.md)for at få mere for at vide om installation af skabeloner.
- Du kan finde et detaljeret eksempel ved implementering af et projekt, [Implementer microservices forudsigeligt i Azure](app-service-web/app-service-deploy-complex-application-predictably.md).
- Hvis du vil vide mere om fejlfinding i forbindelse med en installation, der mislykkedes, se [fejlfinding ressource gruppe installationer i Azure](./resource-manager-troubleshoot-deployments-powershell.md).

