<properties
    pageTitle="Web App klone ved hjælp af PowerShell"
    description="Lær, hvordan du klone din Onlines til nye online ved hjælp af PowerShell."
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
    ms.date="01/13/2016"
    ms.author="ahmedelnably"/>

# <a name="azure-app-service-app-cloning-using-powershell"></a>Azure App Service App klone ved hjælp af PowerShell#

En ny indstilling er blevet føjet til ny-AzureRMWebApp, der ville giver brugeren mulighed for at klone en eksisterende Web App til en nyligt oprettet app i et andet område eller i det samme område med version af Microsoft Azure PowerShell version 1.1.0. Dette vil aktivere kunder at implementere et antal apps på tværs af forskellige regioner hurtigt og nemt.

App klone er i øjeblikket kun understøttet for premium niveau app service-planer. Den nye funktion anvender de samme begrænsninger som sikkerhedskopiering Web Apps, skal du se [sikkerhedskopiere en WebApp i Azure App Service](web-sites-backup.md).

[AZURE.INCLUDE [app-service-web-to-api-and-mobile](../../includes/app-service-web-to-api-and-mobile.md)] 

For at få mere for at vide om brug af Azure ressourcestyring baseret Azure PowerShell kontrollere-cmdlet'er til at administrere dine Web Apps [Azure ressourcestyring baseret PowerShell-kommandoer til Azure Web App](app-service-web-app-azure-resource-manager-powershell.md)

## <a name="cloning-an-existing-app"></a>Klone en eksisterende App ##

Scenarie: En eksisterende WebApp i område syd Central os, brugeren vil klone indholdet til en ny online i Nord Central os område. Dette kan gøres ved hjælp af Azure ressourcestyring versionen af PowerShell-cmdlet til at oprette en ny online med indstillingen - SourceWebApp.

Viden om ressource gruppenavn, der indeholder den kilde WebApp, kan vi bruge følgende PowerShell-kommando til at få den kilde online oplysninger (i dette tilfælde kaldet kilde-webapp):

    $srcapp = Get-AzureRmWebApp -ResourceGroupName SourceAzureResourceGroup -Name source-webapp

Hvis du vil oprette en ny App Service Plan, kan vi bruge ny AzureRmAppServicePlan kommandoen som i følgende eksempel

    New-AzureRmAppServicePlan -Location "South Central US" -ResourceGroupName DestinationAzureResourceGroup -Name NewAppServicePlan -Tier Premium

Ved hjælp af kommandoen ny AzureRmWebApp, vi kan oprette den nye WebApp i område nord Central os og knytte den til en eksisterende premium niveau App Service planlægge, desuden vi kan bruge den samme ressourcegruppe som kilde WebApp eller oprette en ny ressourcegruppe, følgende bevise, at:

    $destapp = New-AzureRmWebApp -ResourceGroupName DestinationAzureResourceGroup -Name dest-webapp -Location "North Central US" -AppServicePlan DestinationAppServicePlan -SourceWebApp $srcapp

At klone en eksisterende WebApp, herunder alle tilknyttede installation pladser, brugeren skal bruge parameteren IncludeSourceWebAppSlots, følgende PowerShell-kommando demonstrerer anvendelse af denne parameter med kommandoen ny AzureRmWebApp:

    $destapp = New-AzureRmWebApp -ResourceGroupName DestinationAzureResourceGroup -Name dest-webapp -Location "North Central US" -AppServicePlan DestinationAppServicePlan -SourceWebApp $srcapp -IncludeSourceWebAppSlots $true

Hvis du vil klone en eksisterende WebApp i det samme område, skal brugeren til at oprette en ny ressourcegruppe og en ny app-tjeneste planlægge i samme område og derefter bruge følgende PowerShell-kommando til at klone WebApp

    $destapp = New-AzureRmWebApp -ResourceGroupName NewAzureResourceGroup -Name dest-webapp -Location "South Central US" -AppServicePlan NewAppServicePlan -SourceWebApp $srcap

## <a name="cloning-an-existing-app-to-an-app-service-environment"></a>Klone en eksisterende App til et App-tjenesten miljø ##

Scenarie: En eksisterende WebApp i område syd Central os, brugeren vil klone indholdet til en ny online til en eksisterende App Service miljø (ASE).

Viden om ressource gruppenavn, der indeholder den kilde WebApp, kan vi bruge følgende PowerShell-kommando til at få den kilde online oplysninger (i dette tilfælde kaldet kilde-webapp):

    $srcapp = Get-AzureRmWebApp -ResourceGroupName SourceAzureResourceGroup -Name source-webapp

Viden om de ASE navn og ressource gruppenavn, som ASE tilhører, brugeren kan bruge kommandoen ny AzureRmWebApp til at oprette nye online i den eksisterende ASE, følgende bevise, at:

    $destapp = New-AzureRmWebApp -ResourceGroupName DestinationAzureResourceGroup -Name dest-webapp -Location "North Central US" -AppServicePlan DestinationAppServicePlan -ASEName DestinationASE -ASEResourceGroupName DestinationASEResourceGroupName -SourceWebApp $srcapp

Parameteren placering er påkrævet på grund af ældre eller anden grund, men i forbindelse med oprettelse af en app i en ASE vil den blive ignoreret. 

## <a name="cloning-an-existing-app-slot"></a>Klone en eksisterende App Slot ##

Scenarie: Brugeren vil klone en eksisterende Web App Slot til enten en ny Web App eller en ny Web App-slot. Den nye Web App kan være i samme område som den oprindelige Web App-slot eller i et andet område.

Viden om ressource gruppenavn, der indeholder den kilde WebApp, kan vi bruge følgende PowerShell-kommando til at få den kilde web app slot's oplysninger (i dette tilfælde kaldet kilde-webappslot) bundet til online kilde-webapp:

    $srcappslot = Get-AzureRmWebAppSlot -ResourceGroupName SourceAzureResourceGroup -Name source-webapp -Slot source-webappslot

Følgende viser oprettelse af en kopi af kilde web app til en ny online:

    $destapp = New-AzureRmWebApp -ResourceGroupName DestinationAzureResourceGroup -Name dest-webapp -Location "North Central US" -AppServicePlan DestinationAppServicePlan -SourceWebApp $srcappslot

## <a name="configuring-traffic-manager-while-cloning-a-app"></a>Konfiguration af trafik Manager mens klone en App ##

Oprette flere områder webapps og konfigurere Azure trafik Manager for at omdirigere trafikken til alle disse webapps, du er en n vigtigt scenarie til at sikre, at kundernes apps er meget tilgængelige, når klone en eksisterende online du har mulighed for at oprette forbindelse begge Onlines til en ny trafik manager profil eller en eksisterende - Bemærk, at kun Azure Resource Manager version af trafik Manager understøttes.

### <a name="creating-a-new-traffic-manager-profile-while-cloning-a-app"></a>Oprette en ny trafik Manager profil under klone en App ###

Scenarie: Brugeren vil klone en WebApp til et andet område, mens du konfigurerer en Azure ressourcestyring trafik manager profil, der indeholder begge webapps. Følgende viser oprettelse af en kopi af kilde web app til en ny online under konfiguration af en ny trafik Manager profil:

    $destapp = New-AzureRmWebApp -ResourceGroupName DestinationAzureResourceGroup -Name dest-webapp -Location "South Central US" -AppServicePlan DestinationAppServicePlan -SourceWebApp $srcapp -TrafficManagerProfileName newTrafficManagerProfile

### <a name="adding-new-cloned-web-app-to-an-existing-traffic-manager-profile"></a>Tilføje nye sammenkædes Web App med en eksisterende trafik Manager-profil ###

Scenarie: Brugeren allerede har en Azure ressourcestyring trafik manager profil, som han gerne vil tilføje begge Onlines som slutpunkter. Hvis du vil gøre dette, vi først samle eksisterende trafik manager profil-id'et og skal vi abonnement-id, ressource gruppenavn og den eksisterende trafik manager profilnavn.

    $TMProfileID = "/subscriptions/<Your subscription ID goes here>/resourceGroups/<Your resource group name goes here>/providers/Microsoft.TrafficManagerProfiles/ExistingTrafficManagerProfileName"

Efter at have trafik Manager-id, viser følgende opretter en kopi af kilde web app til en ny online, mens du føjer dem til en eksisterende trafik Manager profil:

    $destapp = New-AzureRmWebApp -ResourceGroupName <Resource group name> -Name dest-webapp -Location "South Central US" -AppServicePlan DestinationAppServicePlan -SourceWebApp $srcapp -TrafficManagerProfileId $TMProfileID

## <a name="current-restrictions"></a>Aktuelle begrænsninger ##

Denne funktion er i øjeblikket i Vis udskrift, vi arbejder på at tilføje nye funktionaliteter over tid, på følgende liste er de kendte begrænsninger for den aktuelle version af app klone:

- Indstillinger for automatisk for skalering sammenkædes ikke
- Indstillinger for tidsplanen for sikkerhedskopiering sammenkædes ikke
- Indstillinger for VNET sammenkædes ikke
- App indsigt er ikke automatisk konfigureret på destinationen WebApp
- Nemt Auth indstillinger sammenkædes ikke
- Kudu filtypenavn ikke sammenkædes
- TiP regler sammenkædes ikke
- Indhold i databasen ikke sammenkædes


### <a name="references"></a>Referencer ###
- [Azure ressourcestyring baseret PowerShell-kommandoer til Azure Web App](app-service-web-app-azure-resource-manager-powershell.md)
- [Web App klone portalen til Azure](app-service-web-app-cloning-portal.md)
- [Sikkerhedskopiere en WebApp i Azure App Service](web-sites-backup.md)
- [Azure ressourcestyring support til Azure trafik Manager Preview](../../articles/traffic-manager/traffic-manager-powershell-arm.md)
- [Introduktion til App Service-miljø](app-service-app-service-environment-intro.md)
- [Brug af Azure PowerShell med Azure ressourcestyring](../powershell-azure-resource-manager.md)
