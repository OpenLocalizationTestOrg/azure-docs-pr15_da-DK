<properties
    pageTitle="Azure ressourcestyring-baseret på tværs af platforme kommandolinjeværktøjer til Azure Web App | Microsoft Azure"
    description="Lær at bruge de nye Azure ressourcestyring-baseret på tværs af platforme kommandolinjeværktøjer til at administrere dine Azure Web Apps."
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

# <a name="using-azure-resource-manager-based-xplat-cli-for-azure-web-app"></a>Ved hjælp af Azure ressource Manager-baserede XPlat CLI til Azure WebApp#

> [AZURE.SELECTOR]
- [Azure CLI](app-service-web-app-azure-resource-manager-xplat-cli.md)
- [Azure PowerShell](app-service-web-app-azure-resource-manager-powershell.md)

Med versionen af Microsoft Azure på tværs af platforme kommandolinjeparametre værktøjer version 0.10.5, der er tilføjet nye kommandoer. Disse kommandoer giver brugeren mulighed for at bruge ressourcestyring Azure-baserede PowerShell-kommandoer til at administrere Web Apps.

For at få mere for at vide om administration af grupper, se [bruge Azure CLI for at administrere Azure ressourcer og grupper](../xplat-cli-azure-resource-manager.md). 


## <a name="managing-app-service-plans"></a>Administrere App Service-planer ##

### <a name="create-an-app-service-plan"></a>Oprette en App-serviceaftale ###
Hvis du vil oprette en app-serviceaftale, skal du bruge kommandoen **azure appserviceplan oprette** .

Følgende er en beskrivelse af de forskellige parametre:

-   **– ressourcegruppe**: ressourcegruppe, der indeholder den nyoprettede app serviceaftale.
-   **--navn**: navnet på app serviceaftale.
-   **– placering**: app service plan placering.
-   **– niveau**: den ønskede priser sku (indstillingerne er: F1 (gratis). D1 (delt). B1 (grundlæggende lille), B2 (grundlæggende mellem), og B3 (Basic store). S1 (Standard lille), S2 (Standard mellem), og S3 (Standard store). P1 (Premium lille), P2 (Premium mellem), og P3 (Premium store).)
-   **– forekomster**: antal medarbejdere i appen tjeneste plan (Standardværdien er 1).

Eksempel på Brug denne cmdlet:

    azure appserviceplan create --name ContosoAppServicePlan --location "South Central US" --resource-group ContosoAzureResourceGroup --sku P1 --instances 10

### <a name="list-existing-app-service-plans"></a>Liste over eksisterende App Service-planer ###

Du kan få vist eksisterende app tjenesteplanerne, kommandoen **azure appserviceplan liste** .

Du kan få vist alle app tjenesteplanerne under en bestemt ressourcegruppe, bruge:

    azure appserviceplan list --resource-group ContosoAzureResourceGroup

Brug kommandoen **azure appserviceplan show** for at få en bestemt app-serviceaftale skal:

    azure appserviceplan show --name ContosoAppServicePlan --resource-group southeastasia

### <a name="configure-an-existing-app-service-plan"></a>Konfigurere en eksisterende App Service planlægning ###

Hvis du vil ændre indstillingerne for en eksisterende app-serviceaftale, skal du bruge kommandoen **azure appserviceplan config** . Du kan ændre sku, og antallet medarbejdere 

    azure appserviceplan config --nameContosoAppServicePlan --resource-group ContosoAzureResourceGroup --sku S1 --instances 9

#### <a name="scaling-an-app-service-plan"></a>Skalering en App-serviceaftale ####

For at tilpasse en eksisterende App Service Plan, du brug:

    azure appserviceplan config --nameContosoAppServicePlan --resource-group ContosoAzureResourceGroup --instances 9

#### <a name="changing-the-sku-of-an-app-service-plan"></a>Ændre SKU af en App-serviceaftale ####

For at ændre sku af en eksisterende App Service planlægge skal du bruge:

    azure appserviceplan config --nameContosoAppServicePlan --resource-group ContosoAzureResourceGroup --sku S1


### <a name="delete-an-existing-app-service-plan"></a>Slette en eksisterende App Service planlægge ###

Hvis du vil slette en eksisterende app-serviceaftale, skal alle tildelte Onlines blevet flyttet eller slettet først. Derefter bruge kommandoen **azure webapp delete** kan du slette app serviceaftale.

    azure appserviceplan delete --name ContosoAppServicePlan --resource-group southeastasia

## <a name="managing-app-service-web-apps"></a>Administrere App Service Webapps ##

### <a name="create-a-web-app"></a>Oprette en WebApp ###

Hvis du vil oprette en web-app, skal du bruge kommandoen **azure webapp oprette** .

Følgende er en beskrivelse af de forskellige parametre:

- **--navn**: navn til WebApp.
- **– plan**: navn for den serviceaftale, som bruges til at hoste WebApp.
- **– ressourcegruppe**: ressourcegruppe, der er vært App-serviceaftale.
- **– placering**: app webplacering.

Eksempel på Brug denne cmdlet:

    azure webapp create --name ContosoWebApp --resource-group ContosoAzureResourceGroup --plan ContosoAppServicePlan --location "South Central US"

### <a name="delete-an-existing-web-app"></a>Slette en eksisterende Web App ###

Du kan bruge kommandoen **azure webapp delete** til at slette en eksisterende WebApp, skal du angive navnet på WebApp og ressource gruppenavn.

    azure webapp delete --name ContosoWebApp --resource-group ContosoAzureResourceGroup

### <a name="list-existing-web-apps"></a>Liste over eksisterende Web Apps ###

Brug kommandoen **azure webapp liste** for at få vist eksisterende webapps.

Du kan få vist alle webapps under en bestemt ressourcegruppe, bruge:

    azure webapp list --resource-group ContosoAzureResourceGroup

For at få en bestemt web-app skal du bruge kommandoen **azure webapp show** .

    azure webapp show --name ContosoWebApp --resource-group ContosoAzureResourceGroup

### <a name="configure-an-existing-web-app"></a>Konfigurere en eksisterende Web App ###

Hvis du vil ændre de indstillinger og konfigurationer for en eksisterende WebApp, skal du bruge kommandoen **azure webapp config angive** .

Eksempel (1): ændre php versionen af en WebApp 

    azure webapp config set --name ContosoWebApp --resource-group ContosoAzureResourceGroup --phpversion 5.6

Eksempel (2): tilføje eller ændre indstillinger for appen

    webapp config appsettings set --name ContosoWebApp --resource-group ContosoAzureResourceGroup appsetting1=appsetting1value,appsetting2=appsetting2value

For at vide, hvad andre konfiguration kan blive ændret, bruge kommandoen **azure webapp config set -h** .

### <a name="change-the-state-of-an-existing-web-app"></a>Ændre tilstanden for en eksisterende Web App ###

#### <a name="restart-a-web-app"></a>Genstarte en web-app ####

For at genstarte en web-app, skal du angive navn og ressource gruppen af WebApp.

    azure webapp restart --name ContosoWebApp --resource-group ContosoAzureResourceGroup

#### <a name="stop-a-web-app"></a>Stoppe en web-app ####

Hvis du vil stoppe en web-app, skal du angive navn og ressource gruppen af WebApp.

    azure webapp stop --name ContosoWebApp --resource-group ContosoAzureResourceGroup

#### <a name="start-a-web-app"></a>Starte en web-app ####

Hvis du vil starte en web-app, skal du angive navn og ressource gruppen af WebApp.

    azure webapp start --name ContosoWebApp --resource-group ContosoAzureResourceGroup

### <a name="manage-web-app-publishing-profiles"></a>Administrere Web App-udgivelse profiler ###

Hver online har en publicering profil, der kan bruges til at publicere dine apps.

#### <a name="get-publishing-profile"></a>Få publicering af profil ####

For at få profilen publicering til en WebApp skal du bruge:

    azure webapp publishingprofile --name ContosoWebApp --resource-group ContosoAzureResourceGroup

Denne kommando ekkoer publicering profil brugernavnet og adgangskoden til kommandolinjen.

### <a name="manage-web-app-hostnames"></a>Administrere online værtsnavne ###

Hvis du vil administrere hostname bindinger for din online, skal du bruge kommandoen **azure webapp config værtsnavne**  

#### <a name="list-hostname-bindings"></a>Listen hostname bindinger ####

For at få de aktuelle hostname bindinger til en WebApp skal du bruge:

    azure webapp config hostnames list --name ContosoWebApp --resource-group ContosoAzureResourceGroup

#### <a name="add-hostname-bindings"></a>Tilføje hostname bindinger ####

For at føje hostname bindinger til en WebApp, skal du bruge:

    azure webapp config hostnames add --name ContosoWebApp --resource-group ContosoAzureResourceGroup --hostname www.contoso.com

#### <a name="delete-hostname-bindings"></a>Slette hostname bindinger ####

Hvis du vil slette hostname bindinger, skal du bruge:

    azure webapp config hostnames delete --name ContosoWebApp --resource-group ContosoAzureResourceGroup --hostname www.contoso.com

### <a name="next-steps"></a>Næste trin ###
- For at få mere for at vide om understøttelse af Azure ressourcestyring CLI, se [Brug Azure CLI til at administrere Azure ressourcer og grupper.](../xplat-cli-azure-resource-manager.md)
- For at få mere for at vide om administration af App tjenesten ved hjælp af PowerShell, se [Using Azure Resource Manager-Based PowerShell til at administrere Azure Web Apps.](app-service-web-app-azure-resource-manager-powershell.md)
