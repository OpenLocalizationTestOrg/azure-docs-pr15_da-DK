<properties
    pageTitle="Administrere Azure CDN med PowerShell | Microsoft Azure"
    description="Lær at bruge Azure PowerShell-cmdlet'er til at administrere Azure CDN."
    services="cdn"
    documentationCenter=""
    authors="camsoper"
    manager="erikre"
    editor=""/>

<tags
    ms.service="cdn"
    ms.workload="tbd"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/17/2016"
    ms.author="casoper"/>


# <a name="manage-azure-cdn-with-powershell"></a>Administrere Azure CDN med PowerShell

PowerShell indeholder en af de mest fleksible metoder til at administrere din Azure CDN profiler og slutpunkter.  Du kan bruge PowerShell interaktivt eller ved at skrive scripts til at automatisere administrationsopgaver.  Dette selvstudium viser flere af de mest almindelige opgaver, du kan udføre med PowerShell til at administrere dine Azure CDN profiler og slutpunkter.

## <a name="prerequisites"></a>Forudsætninger

Hvis du vil bruge PowerShell til at administrere Azure CDN profiler og slutpunkter, skal du have af Azure PowerShell-modulet, der er installeret.  At lære, hvordan du installerer Azure PowerShell og oprette forbindelse til Azure ved hjælp af den `Login-AzureRmAccount` cmdlet, se, [hvordan du installerer og konfigurerer Azure PowerShell](../powershell-install-configure.md).

>[AZURE.IMPORTANT] Du skal logge på med `Login-AzureRmAccount` før du kan udføre Azure PowerShell-cmdlet'er.

## <a name="listing-the-azure-cdn-cmdlets"></a>Listen over Azure CDN cmdletter

Du kan få vist alle Azure CDN-cmdletter ved hjælp af den `Get-Command` cmdlet.

```text
PS C:\> Get-Command -Module AzureRM.Cdn

CommandType     Name                                               Version    Source
-----------     ----                                               -------    ------
Cmdlet          Get-AzureRmCdnCustomDomain                         2.0.0      AzureRm.Cdn
Cmdlet          Get-AzureRmCdnEndpoint                             2.0.0      AzureRm.Cdn
Cmdlet          Get-AzureRmCdnEndpointNameAvailability             2.0.0      AzureRm.Cdn
Cmdlet          Get-AzureRmCdnOrigin                               2.0.0      AzureRm.Cdn
Cmdlet          Get-AzureRmCdnProfile                              2.0.0      AzureRm.Cdn
Cmdlet          Get-AzureRmCdnProfileSsoUrl                        2.0.0      AzureRm.Cdn
Cmdlet          New-AzureRmCdnCustomDomain                         2.0.0      AzureRm.Cdn
Cmdlet          New-AzureRmCdnEndpoint                             2.0.0      AzureRm.Cdn
Cmdlet          New-AzureRmCdnProfile                              2.0.0      AzureRm.Cdn
Cmdlet          Publish-AzureRmCdnEndpointContent                  2.0.0      AzureRm.Cdn
Cmdlet          Remove-AzureRmCdnCustomDomain                      2.0.0      AzureRm.Cdn
Cmdlet          Remove-AzureRmCdnEndpoint                          2.0.0      AzureRm.Cdn
Cmdlet          Remove-AzureRmCdnProfile                           2.0.0      AzureRm.Cdn
Cmdlet          Set-AzureRmCdnEndpoint                             2.0.0      AzureRm.Cdn
Cmdlet          Set-AzureRmCdnOrigin                               2.0.0      AzureRm.Cdn
Cmdlet          Set-AzureRmCdnProfile                              2.0.0      AzureRm.Cdn
Cmdlet          Start-AzureRmCdnEndpoint                           2.0.0      AzureRm.Cdn
Cmdlet          Stop-AzureRmCdnEndpoint                            2.0.0      AzureRm.Cdn
Cmdlet          Test-AzureRmCdnCustomDomain                        2.0.0      AzureRm.Cdn
Cmdlet          Unpublish-AzureRmCdnEndpointContent                2.0.0      AzureRm.Cdn
```

## <a name="getting-help"></a>Få hjælp

Du kan få hjælp med nogen af disse cmdlet'er ved hjælp af den `Get-Help` cmdlet.  `Get-Help`Viser forbrug og syntaksen, og viser eventuelt eksempler.

```text
PS C:\> Get-Help Get-AzureRmCdnProfile

NAME
    Get-AzureRmCdnProfile

SYNOPSIS
    Gets an Azure CDN profile.


SYNTAX
    Get-AzureRmCdnProfile [-ProfileName <String>] [-ResourceGroupName <String>] [-InformationAction
    <ActionPreference>] [-InformationVariable <String>] [<CommonParameters>]


DESCRIPTION
    Gets an Azure CDN profile and all related information.


RELATED LINKS

REMARKS
    To see the examples, type: "get-help Get-AzureRmCdnProfile -examples".
    For more information, type: "get-help Get-AzureRmCdnProfile -detailed".
    For technical information, type: "get-help Get-AzureRmCdnProfile -full".

```

## <a name="listing-existing-azure-cdn-profiles"></a>Listen over eksisterende Azure CDN profiler

Den `Get-AzureRmCdnProfile` cmdlet uden parametre henter alle dine eksisterende CDN profiler.

```powershell
Get-AzureRmCdnProfile
```

Dette output kan være pipes til cmdlet'er til optælling.

```powershell
# Output the name of all profiles on this subscription.
Get-AzureRmCdnProfile | ForEach-Object { Write-Host $_.Name }

# Return only **Azure CDN from Verizon** profiles.
Get-AzureRmCdnProfile | Where-Object { $_.Sku.Name -eq "StandardVerizon" }
```

Du kan også returnere en enkelt profil ved at angive profilgruppen navn og en ressource.

```powershell
Get-AzureRmCdnProfile -ProfileName CdnDemo -ResourceGroupName CdnDemoRG
```

>[AZURE.TIP] Det er muligt at have flere CDN profiler med det samme navn, så længe de er i forskellige grupper.  Udelade den `ResourceGroupName` parameter returnerer alle profiler med et tilsvarende navn.

## <a name="listing-existing-cdn-endpoints"></a>Listen over eksisterende CDN slutpunkter

`Get-AzureRmCdnEndpoint`kan hente individuelle ark eller alle slutpunkterne på en profil.  

```powershell
# Get a single endpoint.
Get-AzureRmCdnEndpoint -ProfileName CdnDemo -ResourceGroupName CdnDemoRG -EndpointName cdndocdemo

# Get all of the endpoints on a given profile. 
Get-AzureRmCdnEndpoint -ProfileName CdnDemo -ResourceGroupName CdnDemoRG

# Return all of the endpoints on all of the profiles.
Get-AzureRmCdnProfile | Get-AzureRmCdnEndpoint

# Return all of the endpoints in this subscription that are currently running.
Get-AzureRmCdnProfile | Get-AzureRmCdnEndpoint | Where-Object { $_.ResourceState -eq "Running" }
```

## <a name="creating-cdn-profiles-and-endpoints"></a>Oprettelse af CDN profiler og slutpunkter

`New-AzureRmCdnProfile`og `New-AzureRmCdnEndpoint` bruges til at oprette CDN profiler og slutpunkter.

```powershell
# Create a new profile
New-AzureRmCdnProfile -ProfileName CdnPoshDemo -ResourceGroupName CdnDemoRG -Sku StandardAkamai -Location "Central US"

# Create a new endpoint
New-AzureRmCdnEndpoint -ProfileName CdnPoshDemo -ResourceGroupName CdnDemoRG -Location "Central US" -EndpointName cdnposhdoc -OriginName "Contoso" -OriginHostName "www.contoso.com"

# Create a new profile and endpoint (same as above) in one line
New-AzureRmCdnProfile -ProfileName CdnPoshDemo -ResourceGroupName CdnDemoRG -Sku StandardAkamai -Location "Central US" | New-AzureRmCdnEndpoint -EndpointName cdnposhdoc -OriginName "Contoso" -OriginHostName "www.contoso.com"

```

## <a name="checking-endpoint-name-availability"></a>Kontrollere slutpunkt navn tilgængelighed

`Get-AzureRmCdnEndpointNameAvailability`Returnerer et objekt, der angiver, hvis et navn på slutpunkt er tilgængelig.

```powershell
# Retrieve availability
$availability = Get-AzureRmCdnEndpointNameAvailability -EndpointName "cdnposhdoc"

# If available, write a message to the console.
If($availability.NameAvailable) { Write-Host "Yes, that endpoint name is available." }
Else { Write-Host "No, that endpoint name is not available." }
```

## <a name="adding-a-custom-domain"></a>Føje et brugerdefineret domæne

`New-AzureRmCdnCustomDomain`føjer et brugerdefineret domænenavn til et eksisterende slutpunkt.

>[AZURE.IMPORTANT] Du skal konfigurere CNAME hos din DNS-udbyder, som beskrevet i [Sådan tilknyttes brugerdefineret domæne til indhold levering netværk (CDN) slutpunkt](./cdn-map-content-to-custom-domain.md).  Du kan teste tilknytningen før du foretager ændringer slutpunkt ved hjælp af `Test-AzureRmCdnCustomDomain`.

```powershell
# Get an existing endpoint
$endpoint = Get-AzureRmCdnEndpoint -ProfileName CdnPoshDemo -ResourceGroupName CdnDemoRG -EndpointName cdnposhdoc

# Check the mapping
$result = Test-AzureRmCdnCustomDomain -CdnEndpoint $endpoint -CustomDomainHostName "cdn.contoso.com"

# Create the custom domain on the endpoint
If($result.CustomDomainValidated){ New-AzureRmCdnCustomDomain -CustomDomainName Contoso -HostName "cdn.contoso.com" -CdnEndpoint $endpoint }
```

## <a name="modifying-an-endpoint"></a>Ændre et slutpunkt

`Set-AzureRmCdnEndpoint`ændrer et eksisterende slutpunkt.

```powershell
# Get an existing endpoint
$endpoint = Get-AzureRmCdnEndpoint -ProfileName CdnPoshDemo -ResourceGroupName CdnDemoRG -EndpointName cdnposhdoc

# Set up content compression
$endpoint.IsCompressionEnabled = $true
$endpoint.ContentTypesToCompress = "text/javascript","text/css","application/json"

# Save the changed endpoint and apply the changes
Set-AzureRmCdnEndpoint -CdnEndpoint $endpoint
```

## <a name="purgingpre-loading-cdn-assets"></a>Sletning af/Pre-loading CDN Aktiver

`Unpublish-AzureRmCdnEndpointContent`Rydder datalageret cachelagret aktiver, mens `Publish-AzureRmCdnEndpointContent` allerede indlæser Aktiver på understøttede slutpunkter.

```powershell
# Purge some assets.
Unpublish-AzureRmCdnEndpointContent -ProfileName CdnDemo -ResourceGroupName CdnDemoRG -EndpointName cdndocdemo -PurgeContent "/images/kitten.png","/video/rickroll.mp4"

# Pre-load some assets.
Publish-AzureRmCdnEndpointContent -ProfileName CdnDemo -ResourceGroupName CdnDemoRG -EndpointName cdndocdemo -LoadContent "/images/kitten.png","/video/rickroll.mp4"

# Purge everything in /images/ on all endpoints.
Get-AzureRmCdnProfile | Get-AzureRmCdnEndpoint | Unpublish-AzureRmCdnEndpointContent -PurgeContent "/images/*"
```

## <a name="startingstopping-cdn-endpoints"></a>Starte/stoppe CDN slutpunkter
`Start-AzureRmCdnEndpoint`og `Stop-AzureRmCdnEndpoint` kan bruges til at starte og stoppe individuelle slutpunkter eller grupper af slutpunkter.

```powershell
# Stop the cdndocdemo endpoint
Stop-AzureRmCdnEndpoint -ProfileName CdnDemo -ResourceGroupName CdnDemoRG -EndpointName cdndocdemo

# Stop all endpoints
Get-AzureRmCdnProfile | Get-AzureRmCdnEndpoint | Stop-AzureRmCdnEndpoint

# Start all endpoints
Get-AzureRmCdnProfile | Get-AzureRmCdnEndpoint | Start-AzureRmCdnEndpoint
```

## <a name="deleting-cdn-resources"></a>Slette CDN ressourcer

`Remove-AzureRmCdnProfile`og `Remove-AzureRmCdnEndpoint` kan bruges til at fjerne profiler og slutpunkter.

```powershell
# Remove a single endpoint
Remove-AzureRmCdnEndpoint -ProfileName CdnPoshDemo -ResourceGroupName CdnDemoRG -EndpointName cdnposhdoc

# Remove all the endpoints on a profile and skip confirmation (-Force)
Get-AzureRmCdnProfile -ProfileName CdnPoshDemo -ResourceGroupName CdnDemoRG | Get-AzureRmCdnEndpoint | Remove-AzureRmCdnEndpoint -Force

# Remove a single profile
Remove-AzureRmCdnProfile -ProfileName CdnPoshDemo -ResourceGroupName CdnDemoRG
```

## <a name="next-steps"></a>Næste trin

Lær at automatisere Azure CDN med [.NET](./cdn-app-dev-net.md) eller [Node.js](./cdn-app-dev-node.md).

Se [Oversigt over CDN](./cdn-overview.md)for at få mere for at vide om CDN funktioner.


