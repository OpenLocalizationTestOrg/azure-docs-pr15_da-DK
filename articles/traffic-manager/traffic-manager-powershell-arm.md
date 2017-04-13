<properties
    pageTitle="Azure ressourcestyring support til trafik Manager | Microsoft Azure "
    description="Ved hjælp af PowerShell til trafik Manager med Azure ressourcestyring"
    services="traffic-manager"
    documentationCenter="na"
    authors="sdwheeler"
    manager="carmonm"
    editor=""
/>
<tags
    ms.service="traffic-manager"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="infrastructure-services"
    ms.date="10/11/2016"
    ms.author="sewhee"
/>

# <a name="azure-resource-manager-support-for-azure-traffic-manager"></a>Azure ressourcestyring support til Azure trafik Manager

Azure ressourcestyring er foretrukne management-grænseflade til tjenester i Azure. Azure trafik Manager profiler kan administreres ved hjælp af Ressourcestyring Azure-baserede API'er og værktøjer.

## <a name="resource-model"></a>Ressource-model

Azure trafik Manager er konfigureret til brug af en samling af indstillinger, der kaldes en trafik Manager profil. Denne profil indeholder DNS-indstillinger, indstillinger for trafik routing, slutpunkt overvågning indstillinger og en liste over service slutpunkter som trafik er distribueret.

Hver trafik Manager profil er repræsenteret af en ressource af typen 'TrafficManagerProfiles'. På niveauet REST-API er URI for hver profil på følgende måde:

    https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.Network/trafficManagerProfiles/{profile-name}?api-version={api-version}

## <a name="comparison-with-the-azure-traffic-manager-classic-api"></a>Sammenligning med Azure trafik Manager klassisk API

Fra Azure ressourcestyring support til trafik Manager bruger en anden terminologi end den klassiske implementeringsmodel. Følgende tabel viser forskellene mellem ressourcestyring og klassisk ord:

| Ressourcestyring ord | Klassisk ord |
|-----------------------|--------------|
| Trafik-routing metode | Justering af belastning metode |
| Prioritet metode | Failover metode |
| Vægtet metode | Round robin-metode |
| Ydeevnen metode | Ydeevnen metode |

Baseret på kundefeedback, ændret vi terminologi for at forbedre klarhed og reducere almindelige misforståelser. Der er ingen forskel i funktioner.

## <a name="limitations"></a>Begrænsninger

Når der henvises til et slutpunkt af typen 'AzureEndpoints' for en Web App, kan trafik Manager slutpunkter kun henvise til standard (fremstilling) [Online slot](../app-service-web/web-sites-staged-publishing.md). Brugerdefineret pladser understøttes ikke. Som en løsning, kan brugerdefinerede pladser konfigureres ved hjælp af typen 'ExternalEndpoints'.

## <a name="setting-up-azure-powershell"></a>Konfiguration af Azure PowerShell

Disse instruktioner ved hjælp af Microsoft Azure PowerShell. I følgende artikel forklarer, hvordan du installerer og konfigurerer Azure PowerShell.

- [Hvordan du installerer og konfigurerer Azure PowerShell](../powershell-install-configure.md)

Eksemplerne i denne artikel forudsætter, at du har en eksisterende ressourcegruppe. Du kan oprette en ressourcegruppe ved hjælp af følgende kommando:

```powershell
    New-AzureRmResourceGroup -Name MyRG -Location "West US"
```

>[AZURE.NOTE] Azure ressourcestyring kræver, at alle ressourcegrupper har en placering. Denne placering bruges som standard til ressourcer, der er oprettet i denne ressourcegruppe. Men fordi trafik Manager profil ressourcer er global, ikke internationale, valget af ressource gruppe placering har ingen indvirkning på Azure trafik Manager.

## <a name="create-a-traffic-manager-profile"></a>Oprette en trafik Manager-profil

Brug ny AzureRmTrafficManagerProfile cmdlet til at oprette en trafik Manager profil:

```powershell
    $profile = New-AzureRmTrafficManagerProfile -Name MyProfile -ResourceGroupName MyRG -TrafficRoutingMethod Performance -RelativeDnsName contoso -Ttl 30 -MonitorProtocol HTTP -MonitorPort 80 -MonitorPath "/"
```

I følgende tabel beskrives parametrene:

| Parameter | Beskrivelse |
|-----------|-------------|
| Navn | Ressourcenavnet for trafik Manager profil ressourcen. Profiler i samme ressourcegruppe skal have entydige navne. Dette navn er adskilt fra DNS-navnet for DNS-forespørgsler.|
| ResourceGroupName | Navnet på den ressourcegruppe, som indeholder profil ressourcen.|
| TrafficRoutingMethod | Angiver den trafik-routing metode, der bruges til at bestemme, hvilke slutpunkt returneres i svaret en DNS-forespørgsel. Mulige værdier er 'Ydeevne', 'Vægtet' eller 'Priority'.|
| RelativeDnsName | Angiver hostname del af DNS-navnet fra denne trafik Manager profil. Denne værdi kombineres med DNS-domænenavnet, der bruges af Azure trafik Manager til at danne det fulde domænenavn (fulde Domænenavn) på profilen. For eksempel bliver angiver værdien for 'contoso' 'contoso.trafficmanager.net'.|
| TTL | Angiver DNS-Time-to-Live (TTL), i sekunder. Denne TTL informerer den lokale DNS oversættelsesfunktionerne og DNS-klienter hvor lang tid at cachen DNS besvarelse af denne trafik Manager profil.|
| MonitorProtocol | Angiver protokollen, der skal bruge til at overvåge slutpunkt tilstand. Mulige værdier er 'HTTP' og 'HTTPS'.|
| MonitorPort | Angiver den TCP-port, der bruges til at overvåge slutpunkt tilstand.|
| MonitorPath | Angiver stien i forhold til det slutpunkt domænenavn, der bruges til at sende forespørgsler om slutpunkt tilstand.|

Cmdletten opretter en trafik Manager profil i Azure og returnerer en tilsvarende profil objekt til PowerShell. På dette tidspunkt indeholder profilen ikke nogen slutpunkter. Du kan finde flere oplysninger om tilføjelse af slutpunkter til en trafik Manager profil, [Tilføje trafik Manager slutpunkter](#adding-traffic-manager-endpoints).

## <a name="get-a-traffic-manager-profile"></a>Få en trafik Manager-profil

For at hente en eksisterende profil objektet til trafik Manager skal du bruge Get-AzureRmTrafficManagerProfle cmdlet:

```powershell
    $profile = Get-AzureRmTrafficManagerProfile -Name MyProfile -ResourceGroupName MyRG
```

Denne cmdlet returnerer et trafik Manager profil objekt.

## <a name="update-a-traffic-manager-profile"></a>Opdatere en trafik Manager-profil

Ændre trafik Manager profiler følger en proces i trin 3:

1. Hent den profil, ved hjælp af Get-AzureRmTrafficManagerProfile eller bruge den profil, der returneres af ny AzureRmTrafficManagerProfile.
2. Ændre profilen. Du kan tilføje og fjerne slutpunkter eller ændre slutpunkt eller profil parametre. Disse ændringer er offline handlinger. Du ændrer kun det lokale objekt i hukommelsen, der repræsenterer profilen.
3. Foretag dine ændringer ved hjælp af cmdlet'en Set-AzureRmTrafficManagerProfile til.

Alle Profilegenskaber kan ændres undtagen profilens RelativeDnsName. Hvis du vil ændre RelativeDnsName, skal du slette profil og en ny profil med et nyt navn.

Følgende eksempel viser, hvordan du ændrer profilens TTL:

```powershell
    $profile = Get-AzureRmTrafficManagerProfile -Name MyProfile -ResourceGroupName MyRG
    $profile.Ttl = 300
    Set-AzureRmTrafficManagerProfile -TrafficManagerProfile $profile
```

Der findes tre typer trafik Manager slutpunkter:

1. **Azure slutpunkter** er tjenester, der findes i Azure
2. **Eksterne slutpunkter** er hostet uden for Azure-tjenester
3. **Indlejrede slutpunkter** bruges til at opbygge indlejrede hierarkier af trafik Manager profiler. Indlejrede slutpunkter aktivere avancerede trafik-routing konfigurationer til komplekse programmer.

I alle tre tilfælde, kan der tilføjes slutpunkter på to måder:

1. Brug af en proces i trin 3 beskrevet tidligere. Fordelen ved denne metode er, at flere slutpunkt kan ændres i en enkelt opdatering.
2. Brug af ny AzureRmTrafficManagerEndpoint cmdlet. Denne cmdlet føjer et slutpunkt til en eksisterende trafik Manager profil i en enkelt handling.

## <a name="adding-azure-endpoints"></a>Tilføje Azure slutpunkter

Azure slutpunkter referere til tjenester, der findes i Azure. Tre typer Azure slutpunkter understøttes:

1. Azure Webapps
2. 'Klassisk' cloud services (som kan indeholde en PaaS tjeneste eller IaaS virtuelle maskiner)
3. Azure PublicIpAddress ressourcer (som kan knyttes til en belastningsjustering eller et virtuelt NIC). PublicIpAddress skal have et DNS-navn, der er tildelt til bruges i trafik Manager.

I hver sag:

- Tjenesten er angivet ved hjælp af parameteren 'targetResourceId' Tilføj AzureRmTrafficManagerEndpointConfig eller ny AzureRmTrafficManagerEndpoint.
- 'Mål' og 'EndpointLocation' er angivet af brugergrænsefladen på TargetResourceId.
- Angive 'tykkelsen' er valgfrit. Tykkelser bruges kun, hvis profilen, der er konfigureret til at bruge metoden trafik-routing 'Vægtet'. Ellers skal ignoreres de. Hvis angivet, skal værdien være et tal mellem 1 og 1000. Standardværdien er '1'.
- Angive 'prioriteten' er valgfrit. Prioriteter bruges kun, hvis profilen, der er konfigureret til at bruge metoden trafik-routing 'Priority'. Ellers skal ignoreres de. Gyldige værdier er fra 1 til 1000 med lavere værdier, der angiver en højere prioritet. Hvis angivet for et slutpunkt, skal de angives for alle slutpunkter. Hvis udelades, er standardværdier, startende med '1' anvendt i den rækkefølge, slutpunkterne er angivet.

### <a name="example-1-adding-web-app-endpoints-using-add-azurermtrafficmanagerendpointconfig"></a>Eksempel 1: Tilføje Web App slutpunkter ved hjælp af Tilføj AzureRmTrafficManagerEndpointConfig

I dette eksempel skal vi oprette en trafik Manager profil og tilføje to Web App-slutpunkter ved hjælp af Tilføj AzureRmTrafficManagerEndpointConfig cmdlet.

```powershell
    $profile = New-AzureRmTrafficManagerProfile -Name myprofile -ResourceGroupName MyRG -TrafficRoutingMethod Performance -RelativeDnsName myapp -Ttl 30 -MonitorProtocol HTTP -MonitorPort 80 -MonitorPath "/"
    $webapp1 = Get-AzureRMWebApp -Name webapp1
    Add-AzureRmTrafficManagerEndpointConfig -EndpointName webapp1ep -TrafficManagerProfile $profile -Type AzureEndpoints -TargetResourceId $webapp1.Id -EndpointStatus Enabled
    $webapp2 = Get-AzureRMWebApp -Name webapp2
    Add-AzureRmTrafficManagerEndpointConfig -EndpointName webapp2ep -TrafficManagerProfile $profile -Type AzureEndpoints -TargetResourceId $webapp2.Id -EndpointStatus Enabled
    Set-AzureRmTrafficManagerProfile -TrafficManagerProfile $profile
```

### <a name="example-2-adding-a-classic-cloud-service-endpoint-using-new-azurermtrafficmanagerendpoint"></a>Eksempel 2: Tilføje en 'klassisk' skyen tjenesteslutpunkt, ved hjælp af ny AzureRmTrafficManagerEndpoint

I dette eksempel føjes et 'klassisk' skybaseret tjeneste slutpunkt til en trafik Manager profil. I dette eksempel skal angivet vi profil ved hjælp af gruppenavne profil og ressource i stedet for at overføre et profil objekt. Begge tilgange understøttes.

    $cloudService = Get-AzureRmResource -ResourceName MyCloudService -ResourceType "Microsoft.ClassicCompute/domainNames" -ResourceGroupName MyCloudService
    New-AzureRmTrafficManagerEndpoint -Name MyCloudServiceEndpoint -ProfileName MyProfile -ResourceGroupName MyRG -Type AzureEndpoints -TargetResourceId $cloudService.Id -EndpointStatus Enabled

### <a name="example-3-adding-a-publicipaddress-endpoint-using-new-azurermtrafficmanagerendpoint"></a>Eksempel 3: Tilføje et publicIpAddress slutpunkt ved hjælp af ny AzureRmTrafficManagerEndpoint

I dette eksempel føjes en offentlige IP-adresseressource til profilen trafik Manager. Den offentlige IP-adresse skal have en DNS-navn, der er konfigureret, og det kan være bundet til NIC af en VM eller til en belastningsjustering.

    $ip = Get-AzureRmPublicIpAddress -Name MyPublicIP -ResourceGroupName MyRG
    New-AzureRmTrafficManagerEndpoint -Name MyIpEndpoint -ProfileName MyProfile -ResourceGroupName MyRG -Type AzureEndpoints -TargetResourceId $ip.Id -EndpointStatus Enabled

## <a name="adding-external-endpoints"></a>Tilføje eksterne slutpunkter

Trafik Manager bruger eksterne slutpunkter til at dirigere trafik til tjenester, der er hostet uden for Azure. Som med Azure slutpunkter kan eksterne slutpunkter tilføjes enten ved hjælp af Tilføj-AzureRmTrafficManagerEndpointConfig efterfulgt af sæt AzureRmTrafficManagerProfile eller ny AzureRMTrafficManagerEndpoint.

Når du angiver eksterne slutpunkter:

- Domænenavnet slutpunkt skal angives ved hjælp af parameteren 'Mål'
- Hvis metoden trafik-routing 'Ydeevne' bruges, er EndpointLocation påkrævet. Ellers er valgfrit. Værdien skal være et [gyldigt Azure områdenavn](https://azure.microsoft.com/regions/).
- 'Vægt' og 'Priority' er valgfrit.

### <a name="example-1-adding-external-endpoints-using-add-azurermtrafficmanagerendpointconfig-and-set-azurermtrafficmanagerprofile"></a>Eksempel 1: Tilføje eksterne slutpunkter ved hjælp af Tilføj AzureRmTrafficManagerEndpointConfig og angive AzureRmTrafficManagerProfile

I dette eksempel skal vi oprette en trafik Manager profil, tilføje to eksterne slutpunkter og foretag ændringerne.

    $profile = New-AzureRmTrafficManagerProfile -Name myprofile -ResourceGroupName MyRG -TrafficRoutingMethod Performance -RelativeDnsName myapp -Ttl 30 -MonitorProtocol HTTP -MonitorPort 80 -MonitorPath "/"
    Add-AzureRmTrafficManagerEndpointConfig -EndpointName eu-endpoint -TrafficManagerProfile $profile -Type ExternalEndpoints -Target app-eu.contoso.com -EndpointStatus Enabled
    Add-AzureRmTrafficManagerEndpointConfig -EndpointName us-endpoint -TrafficManagerProfile $profile -Type ExternalEndpoints -Target app-us.contoso.com -EndpointStatus Enabled
    Set-AzureRmTrafficManagerProfile -TrafficManagerProfile $profile

### <a name="example-2-adding-external-endpoints-using-new-azurermtrafficmanagerendpoint"></a>Eksempel 2: Tilføje eksterne slutpunkter ved hjælp af ny AzureRmTrafficManagerEndpoint

I dette eksempel skal tilføje vi eksterne ark til en eksisterende profil. Profilen, der er angivet ved hjælp af gruppenavne profil og ressource.

    New-AzureRmTrafficManagerEndpoint -Name eu-endpoint -ProfileName MyProfile -ResourceGroupName MyRG -Type ExternalEndpoints -Target app-eu.contoso.com -EndpointStatus Enabled

## <a name="adding-nested-endpoints"></a>Tilføje 'Indlejrede' slutpunkter

Hver trafik Manager profil angiver en enkelt metode trafik-routing. Der er dog scenarier, der kræver mere avancerede trafik routing end ruten leveres af en enkelt trafik Manager profil. Du kan indlejre trafik Manager profiler for at kombinere fordelene ved mere end én trafik-routing metode. Indlejrede profiler giver dig mulighed at tilsidesætte trafik Manager standardfunktionsmåden til understøttelse af større og mere komplekse installationer som programmet. Du kan finde mere detaljerede eksempler, [indlejrede trafik Manager profiler](traffic-manager-nested-profiles.md).

Indlejrede slutpunkter er konfigureret på den overordnede profil, og ved hjælp af en bestemt slutpunkt type, 'NestedEndpoints'. Når du angiver indlejrede slutpunkter:

- Slutpunktet skal angives ved hjælp af parameteren 'targetResourceId'
- Hvis metoden trafik-routing 'Ydeevne' bruges, er EndpointLocation påkrævet. Ellers er valgfrit. Værdien skal være et [gyldigt Azure områdenavn](http://azure.microsoft.com/regions/).
- 'Vægt' og 'Priority' er valgfri, som for Azure slutpunkter.
- Parameteren 'MinChildEndpoints' er valgfrit. Standardværdien er '1'. Hvis antallet af tilgængelige slutpunkter falder under denne grænse, finder den overordnede profil profilen underordnet 'nedsat' og omdirigerer trafik til de andre slutpunkter i den overordnede profil.

### <a name="example-1-adding-nested-endpoints-using-add-azurermtrafficmanagerendpointconfig-and-set-azurermtrafficmanagerprofile"></a>Eksempel 1: Tilføje indlejrede slutpunkter ved hjælp af Tilføj AzureRmTrafficManagerEndpointConfig og angive AzureRmTrafficManagerProfile

I dette eksempel skal vi oprette nye trafik Manager underordnede og overordnede profiler, tilføje underordnede som et indlejret slutpunkt til overordnet og foretag ændringerne.

    $child = New-AzureRmTrafficManagerProfile -Name child -ResourceGroupName MyRG -TrafficRoutingMethod Priority -RelativeDnsName child -Ttl 30 -MonitorProtocol HTTP -MonitorPort 80 -MonitorPath "/"
    $parent = New-AzureRmTrafficManagerProfile -Name parent -ResourceGroupName MyRG -TrafficRoutingMethod Performance -RelativeDnsName parent -Ttl 30 -MonitorProtocol HTTP -MonitorPort 80 -MonitorPath "/"
    Add-AzureRmTrafficManagerEndpointConfig -EndpointName child-endpoint -TrafficManagerProfile $parent -Type NestedEndpoints -TargetResourceId $child.Id -EndpointStatus Enabled -EndpointLocation "North Europe" -MinChildEndpoints 2
    Set-AzureRmTrafficManagerProfile -TrafficManagerProfile $profile

Pladshensyn i dette eksempel vi ikke tilføje andre slutpunkter til de underordnede eller overordnede profiler.

### <a name="example-2-adding-nested-endpoints-using-new-azurermtrafficmanagerendpoint"></a>Eksempel 2: Tilføje indlejrede slutpunkter ved hjælp af ny AzureRmTrafficManagerEndpoint

I dette eksempel tilføje vi en eksisterende underordnede profil som en indlejret slutpunkt til en eksisterende overordnede profil. Profilen, der er angivet ved hjælp af gruppenavne profil og ressource.

    $child = Get-AzureRmTrafficManagerEndpoint -Name child -ResourceGroupName MyRG
    New-AzureRmTrafficManagerEndpoint -Name child-endpoint -ProfileName parent -ResourceGroupName MyRG -Type NestedEndpoints -TargetResourceId $child.Id -EndpointStatus Enabled -EndpointLocation "North Europe" -MinChildEndpoints 2

## <a name="update-a-traffic-manager-endpoint"></a>Opdatere et trafik Manager slutpunkt

Der er to måder at opdatere et eksisterende trafik Manager slutpunkt:

1. Få profilen trafik Manager ved hjælp af Get-AzureRmTrafficManagerProfile, opdatere slutpunkt egenskaber i profilen, og foretag ændringerne ved hjælp af sæt AzureRmTrafficManagerProfile. Denne metode har fordelen ved at opdatere mere end et slutpunkt i en enkelt handling.
2. Få trafik Manager slutpunktet ved hjælp af Get-AzureRmTrafficManagerEndpoint, opdatere slutpunkt egenskaberne, og foretag ændringerne ved hjælp af sæt AzureRmTrafficManagerEndpoint. Denne metode er nemmere, da det ikke kræver indeksering i matrixen slutpunkter i profilen.

### <a name="example-1-updating-endpoints-using-get-azurermtrafficmanagerprofile-and-set-azurermtrafficmanagerprofile"></a>Eksempel 1: Opdatering af slutpunkter ved hjælp af Get-AzureRmTrafficManagerProfile og angive AzureRmTrafficManagerProfile

I dette eksempel skal ændre vi prioriteten på to slutpunkter inden for en eksisterende profil.

    $profile = Get-AzureRmTrafficManagerProfile -Name myprofile -ResourceGroupName MyRG
    $profile.Endpoints[0].Priority = 2
    $profile.Endpoints[1].Priority = 1
    Set-AzureRmTrafficManagerProfile -TrafficManagerProfile $profile

### <a name="example-2-updating-an-endpoint-using-get-azurermtrafficmanagerendpoint-and-set-azurermtrafficmanagerendpoint"></a>Eksempel 2: Opdatere ark ved hjælp af Get-AzureRmTrafficManagerEndpoint og angive AzureRmTrafficManagerEndpoint

I dette eksempel skal ændre vi tykkelsen på et enkelt slutpunkt i en eksisterende profil.

    $endpoint = Get-AzureRmTrafficManagerEndpoint -Name myendpoint -ProfileName myprofile -ResourceGroupName MyRG -Type ExternalEndpoints
    $endpoint.Weight = 20
    Set-AzureRmTrafficManagerEndpoint -TrafficManagerEndpoint $endpoint

## <a name="enabling-and-disabling-endpoints-and-profiles"></a>Aktivering og deaktivering af slutpunkter og profiler

Trafik Manager gør det muligt for individuelle slutpunkter til aktiveres og deaktiveres, samt tillade aktivering og deaktivering af hele profiler.
Disse kan ændres af få/opdatere/indstilling i slutpunktet eller profil ressourcer. Hvis du vil strømline disse almindelige handlinger, understøttes de også via dedikeret cmdletter.

### <a name="example-1-enabling-and-disabling-a-traffic-manager-profile"></a>Eksempel 1: Aktivere og deaktivere en trafik Manager-profil

Brug Aktivér AzureRmTrafficManagerProfile til at aktivere en trafik Manager profil. Profilen, der kan angives ved hjælp af en profil objekt. Objektet profil kan overføres via pipeline eller ved at bruge den '-TrafficManagerProfile' parameter. I dette eksempel skal angive vi profilen ved profil og ressource gruppenavn.

```powershell
    Enable-AzureRmTrafficManagerProfile -Name MyProfile -ResourceGroupName MyResourceGroup
```

Sådan deaktiveres en trafik Manager profil:

```powershell
    Disable-AzureRmTrafficManagerProfile -Name MyProfile -ResourceGroupName MyResourceGroup
```

Deaktiver AzureRmTrafficManagerProfile cmdlet beder om en bekræftelse. Denne prompt kan udelades ved hjælp af den '-kraft ' parameter.

### <a name="example-2-enabling-and-disabling-a-traffic-manager-endpoint"></a>Eksempel 2: Aktivering og deaktivering af et trafik Manager slutpunkt

Brug Aktivér AzureRmTrafficManagerEndpoint til at aktivere et trafik Manager slutpunkt. Der er to måder til at angive den første eller sidste ark

1. Brug en TrafficManagerEndpoint objekt, der sendes via pipeline eller brug af den '-TrafficManagerEndpoint' parameter
2. Bruge navn på slutpunkt, slutpunkt type, profilnavn og ressource gruppenavn:

```powershell
    Enable-AzureRmTrafficManagerEndpoint -Name MyEndpoint -Type AzureEndpoints -ProfileName MyProfile -ResourceGroupName MyRG
```

På samme måde til at deaktivere en trafik Manager slutpunkt:

```powershell
     Disable-AzureRmTrafficManagerEndpoint -Name MyEndpoint -Type AzureEndpoints -ProfileName MyProfile -ResourceGroupName MyRG -Force
```

Som med Deaktiver-AzureRmTrafficManagerProfile, beder Cmdletten Deaktiver AzureRmTrafficManagerEndpoint om en bekræftelse. Denne prompt kan udelades ved hjælp af den '-kraft ' parameter.

## <a name="delete-a-traffic-manager-endpoint"></a>Slette et trafik Manager slutpunkt

For at fjerne individuelle slutpunkter, skal du bruge Fjern AzureRmTrafficManagerEndpoint cmdlet:

```powershell
    Remove-AzureRmTrafficManagerEndpoint -Name MyEndpoint -Type AzureEndpoints -ProfileName MyProfile -ResourceGroupName MyRG
```

Denne cmdlet beder om en bekræftelse. Denne prompt kan udelades ved hjælp af den '-kraft ' parameter.

## <a name="delete-a-traffic-manager-profile"></a>Slette en trafik Manager-profil

For at slette en trafik Manager profil, skal du bruge Fjern AzureRmTrafficManagerProfile cmdlet, der angiver gruppenavne profil og ressource:

```powershell
    Remove-AzureRmTrafficManagerProfile -Name MyProfile -ResourceGroupName MyRG [-Force]
```

Denne cmdlet beder om en bekræftelse. Denne prompt kan udelades ved hjælp af den '-kraft ' parameter.

Profilen, der skal slettes kan også angives ved hjælp af et objekt profil:

```powershell
    $profile = Get-AzureRmTrafficManagerProfile -Name MyProfile -ResourceGroupName MyRG
    Remove-AzureRmTrafficManagerProfile -TrafficManagerProfile $profile [-Force]
```

Denne fremgangsmåde kan også pipes:

```powershell
    Get-AzureRmTrafficManagerProfile -Name MyProfile -ResourceGroupName MyRG | Remove-AzureRmTrafficManagerProfile [-Force]
```

## <a name="next-steps"></a>Næste trin

[Trafik Manager overvågning](traffic-manager-monitoring.md)

[Overvejelser i forbindelse med trafik Manager ydeevne](traffic-manager-performance-considerations.md)
