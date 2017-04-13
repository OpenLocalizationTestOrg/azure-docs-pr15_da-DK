<properties
    pageTitle="Azure ressourcestyring-baserede PowerShell kommandoer til Azure online | Microsoft Azure"
    description="Lær at bruge kommandoerne ny ressourcestyring Azure-baserede PowerShell til at administrere dine Azure Web Apps."
    services="app-service\web"
    documentationCenter=""
    authors="ahmedelnably"
    manager="stefsch"
    editor=""/>

<tags
    ms.service="app-service-web"
    ms.workload="web"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/29/2016"
    ms.author="aelnably"/>

# <a name="using-azure-resource-manager-based-powershell-to-manage-azure-web-apps"></a>Bruge Azure ressource Manager-baserede PowerShell til at administrere Azure Webapps#

> [AZURE.SELECTOR]
- [Azure CLI](app-service-web-app-azure-resource-manager-xplat-cli.md)
- [Azure PowerShell](app-service-web-app-azure-resource-manager-powershell.md)

Med Microsoft Azure PowerShell version 1.0.0 er blevet tilføjet nye kommandoer, der giver brugeren mulighed for at bruge ressourcestyring Azure-baserede PowerShell-kommandoer til at administrere Web Apps.

Se [Bruge Azure PowerShell med Azure ressourcestyring](../powershell-azure-resource-manager.md)for at få mere for at vide om administration af grupper. 

Hvis du vil vide mere om den komplette liste over parametre og indstillinger for PowerShell-cmdletter, se [Reference til fuld Cmdlet af Ressourcestyring Web App Azure-baserede PowerShell-cmdlet'er](https://msdn.microsoft.com/library/mt619237.aspx)

## <a name="managing-app-service-plans"></a>Administrere App Service-planer ##

### <a name="create-an-app-service-plan"></a>Oprette en App-serviceaftale ###
Brug **Ny AzureRmAppServicePlan** cmdlet til at oprette en app-serviceaftale.

Følgende er en beskrivelse af de forskellige parametre:

-   **Navn**: navnet på app serviceaftale.
-   **Placering**: service plan placering.
-   **ResourceGroupName**: ressourcegruppe, der indeholder den nyoprettede app serviceaftale.
-   **Niveau**: det ønskede priser niveau (standard er gratis, andre muligheder er delt, Basic, Standard og Premium).
-   **WorkerSize**: størrelsen af medarbejdere (standard er lille, hvis niveau parameteren er angivet som Basic, Standard eller Premium. Andre muligheder er Medium og stor.)
-   **NumberofWorkers**: antal medarbejdere i appen tjeneste plan (Standardværdien er 1). 

Eksempel på Brug denne cmdlet:

    New-AzureRmAppServicePlan -Name ContosoAppServicePlan -Location "South Central US" -ResourceGroupName ContosoAzureResourceGroup -Tier Premium -WorkerSize Large -NumberofWorkers 10

### <a name="create-an-app-service-plan-in-an-app-service-environment"></a>Oprette en App-serviceaftale i en App Service-miljø ###
For at oprette en app-serviceaftale i en app service-miljø, skal du bruge kommandoen samme kommandoen **Ny AzureRmAppServicePlan** med ekstra parametre til at angive den ASE navn og ASES ressource gruppenavn.

Eksempel på Brug denne cmdlet:

    New-AzureRmAppServicePlan -Name ContosoAppServicePlan -Location "South Central US" -ResourceGroupName ContosoAzureResourceGroup -AseName constosoASE -AseResourceGroupName contosoASERG -Tier Premium -WorkerSize Large -NumberofWorkers 10

Hvis du vil vide mere om app service-miljø, se [Introduktion til App Service-miljø](app-service-app-service-environment-intro.md)

### <a name="list-existing-app-service-plans"></a>Liste over eksisterende App Service-planer ###

Brug **Get-AzureRmAppServicePlan** cmdlet for at få vist eksisterende app tjenesteplanerne.

Du kan få vist alle app tjenesteplanerne under dit abonnement, kan du bruge: 

    Get-AzureRmAppServicePlan

Du kan få vist alle app tjenesteplanerne under en bestemt ressourcegruppe, bruge:

    Get-AzureRmAppServicePlan -ResourceGroupname ContosoAzureResourceGroup

For at få en bestemt app-serviceaftale skal du bruge:

    Get-AzureRmAppServicePlan -Name ContosoAppServicePlan


### <a name="configure-an-existing-app-service-plan"></a>Konfigurere en eksisterende App Service planlægning ###

Brug cmdlet'en **Set-AzureRmAppServicePlan** til at ændre indstillingerne for en eksisterende app-serviceaftale. Du kan ændre niveauet, arbejder størrelse og antallet af medarbejdere 

    Set-AzureRmAppServicePlan -Name ContosoAppServicePlan -ResourceGroupName ContosoAzureResourceGroup -Tier Standard -WorkerSize Medium -NumberofWorkers 9

#### <a name="scaling-an-app-service-plan"></a>Skalering en App-serviceaftale ####

For at tilpasse en eksisterende App Service Plan, du brug:

    Set-AzureRmAppServicePlan -Name ContosoAppServicePlan -ResourceGroupName ContosoAzureResourceGroup -NumberofWorkers 9

#### <a name="changing-the-worker-size-of-an-app-service-plan"></a>Ændre størrelsen på en App-tjenesten planlægge arbejder ####

For at ændre størrelsen på medarbejdere i en eksisterende App Service planlægge skal du bruge:

    Set-AzureRmAppServicePlan -Name ContosoAppServicePlan -ResourceGroupName ContosoAzureResourceGroup -WorkerSize Medium

#### <a name="changing-the-tier-of-an-app-service-plan"></a>Ændre niveauet i en App-serviceaftale ####

For at ændre niveauet i en eksisterende App Service planlægge skal du bruge:

    Set-AzureRmAppServicePlan -Name ContosoAppServicePlan -ResourceGroupName ContosoAzureResourceGroup -Tier Standard

### <a name="delete-an-existing-app-service-plan"></a>Slette en eksisterende App Service planlægge ###

Hvis du vil slette en eksisterende app-serviceaftale, skal alle tildelte Onlines blevet flyttet eller slettet først. Derefter bruger Cmdletten **Fjern AzureRmAppServicePlan** , kan du slette app serviceaftale.

    Remove-AzureRmAppServicePlan -Name ContosoAppServicePlan -ResourceGroupName ContosoAzureResourceGroup

## <a name="managing-app-service-web-apps"></a>Administrere App Service Webapps ##

### <a name="create-a-web-app"></a>Oprette en WebApp ###

Brug **Ny AzureRmWebApp** cmdlet til at oprette en web-app.

Følgende er en beskrivelse af de forskellige parametre:

- **Navn**: navn til WebApp.
- **AppServicePlan**: navn for den serviceaftale, som bruges til at hoste WebApp.
- **ResourceGroupName**: ressourcegruppe, der er vært App-serviceaftale.
- **Placering**: app webplacering.

Eksempel på Brug denne cmdlet:

    New-AzureRmWebApp -Name ContosoWebApp -AppServicePlan ContosoAppServicePlan -ResourceGroupName ContosoAzureResourceGroup -Location "South Central US"

### <a name="create-a-web-app-in-an-app-service-environment"></a>Oprette en WebApp i en App Service-miljø ###

For at oprette en web-app i en App Service-miljø (ASE). Bruge kommandoen samme **Ny AzureRmWebApp** med ekstra parametre til at angive ASE navn og en ressource gruppenavn, som ASE tilhører.

    New-AzureRmWebApp -Name ContosoWebApp -AppServicePlan ContosoAppServicePlan -ResourceGroupName ContosoAzureResourceGroup -Location "South Central US"  -ASEName ContosoASEName -ASEResourceGroupName ContosoASEResourceGroupName

Hvis du vil vide mere om app service-miljø, se [Introduktion til App Service-miljø](app-service-app-service-environment-intro.md)

### <a name="delete-an-existing-web-app"></a>Slette en eksisterende Web App ###

Du kan bruge cmdlet'en **Fjern AzureRmWebApp** til at slette en eksisterende WebApp, skal du angive navnet på WebApp og ressource gruppenavn.

    Remove-AzureRmWebApp -Name ContosoWebApp -ResourceGroupName ContosoAzureResourceGroup


### <a name="list-existing-web-apps"></a>Liste over eksisterende Web Apps ###

Brug cmdlet'en **Get-AzureRmWebApp** for at få vist eksisterende webapps.

Du kan få vist alle webapps under dit abonnement, kan du bruge:

    Get-AzureRmWebApp

Du kan få vist alle webapps under en bestemt ressourcegruppe, bruge:

    Get-AzureRmWebApp -ResourceGroupname ContosoAzureResourceGroup

For at få en bestemt web-app skal du bruge:

    Get-AzureRmWebApp -Name ContosoWebApp

### <a name="configure-an-existing-web-app"></a>Konfigurere en eksisterende Web App ###

Brug cmdlet'en **Set-AzureRmWebApp** til at ændre de indstillinger og konfigurationer for en eksisterende WebApp. En komplet liste over parametre, skal du se [Cmdlet referencekæde](https://msdn.microsoft.com/library/mt652487.aspx)

Eksempel (1): Brug denne cmdlet til at ændre strenge

    $connectionstrings = @{ ContosoConn1 = @{ Type = “MySql”; Value = “MySqlConn”}; ContosoConn2 = @{ Type = “SQLAzure”; Value = “SQLAzureConn”} }
    Set-AzureRmWebApp -Name ContosoWebApp -ResourceGroupName ContosoAzureResourceGroup -ConnectionStrings $connectionstrings

Eksempel (2): tilføje eller ændre indstillinger for appen

    $appsettings = @{appsetting1 = "appsetting1value"; appsetting2 = "appsetting2value"}
    Set-AzureRmWebApp -Name ContosoWebApp -ResourceGroupName ContosoAzureResourceGroup -AppSettings $appsettings


Eksempel (3): set web app til at køre i 64-bit tilstand

    Set-AzureRmWebApp -Name ContosoWebApp -ResourceGroupName ContosoAzureResourceGroup -Use32BitWorkerProcess $False

### <a name="change-the-state-of-an-existing-web-app"></a>Ændre tilstanden for en eksisterende Web App ###

#### <a name="restart-a-web-app"></a>Genstarte en web-app ####

For at genstarte en web-app, skal du angive navn og ressource gruppen af WebApp.

    Restart-AzureRmWebapp -Name ContosoWebApp -ResourceGroupName ContosoAzureResourceGroup

#### <a name="stop-a-web-app"></a>Stoppe en web-app ####

Hvis du vil stoppe en web-app, skal du angive navn og ressource gruppen af WebApp.

    Stop-AzureRmWebapp -Name ContosoWebApp -ResourceGroupName ContosoAzureResourceGroup

#### <a name="start-a-web-app"></a>Starte en web-app ####

Hvis du vil starte en web-app, skal du angive navn og ressource gruppen af WebApp.

    Start-AzureRmWebapp -Name ContosoWebApp -ResourceGroupName ContosoAzureResourceGroup

### <a name="manage-web-app-publishing-profiles"></a>Administrere Web App-udgivelse profiler ###

Hver online har en publicering profil, der kan bruges til at publicere dine apps, flere handlinger kan udføres udgivelse profiler.

#### <a name="get-publishing-profile"></a>Få publicering af profil ####

For at få profilen publicering til en WebApp skal du bruge:

    Get-AzureRmWebAppPublishingProfile -Name ContosoWebApp -ResourceGroupName ContosoAzureResourceGroup -OutputFile .\publishingprofile.txt

Denne kommando ekkoer profilen publicering til kommandolinjen som godt outputområder profilen publicering til en tekstfil.

#### <a name="reset-publishing-profile"></a>Nulstille publicering af profil ####

Hvis du vil nulstille begge installere publicering adgangskoden til FTP- og en web App, brug:

    Reset-AzureRmWebAppPublishingProfile -Name ContosoWebApp -ResourceGroupName ContosoAzureResourceGroup

### <a name="manage-web-app-certificates"></a>Administrere Web App certifikater ###

For at få mere for at vide om, hvordan du administrere web app certifikater, se [SSL-certifikater binding ved hjælp af PowerShell](app-service-web-app-powershell-ssl-binding.md)


### <a name="next-steps"></a>Næste trin ###
- For at få mere for at vide om understøttelse af Azure ressourcestyring PowerShell, se [ved hjælp af Azure PowerShell med Azure ressourcestyring.](../powershell-azure-resource-manager.md)
- Du kan få om App Service-miljøer, [Introduktion til App Service-miljø.](app-service-app-service-environment-intro.md)
- For at få mere for at vide om administration af App Service SSL-certifikater ved hjælp af PowerShell, se [SSL-certifikater binding ved hjælp af PowerShell.](app-service-web-app-powershell-ssl-binding.md)
- Hvis du vil vide mere om den komplette liste over ressourcestyring Azure-baserede PowerShell-cmdlet'er til Azure Web Apps, se [Azure-Cmdlet referencen til Web Apps Azure ressourcestyring PowerShell Cmdlets.](https://msdn.microsoft.com/library/mt619237.aspx)
- - For at få mere for at vide om administration af App tjenesten ved hjælp af CLI, se [Using Azure Resource Manager-Based XPlat CLI til Azure Web App.](app-service-web-app-azure-resource-manager-xplat-cli.md)
