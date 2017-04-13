<properties
    pageTitle="Tilslut din app til det virtuelle netværk ved hjælp af PowerShell"
    description="Oplysninger om, hvordan du kan oprette forbindelse til og arbejde med virtuelle netværk ved hjælp af PowerShell"
    services="app-service"
    documentationCenter=""
    authors="ccompy"
    manager="wpickett"
    editor="cephalin"/>

<tags
    ms.service="app-service"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/29/2016"
    ms.author="ccompy"/>

# <a name="connect-your-app-to-your-virtual-network-by-using-powershell"></a>Tilslut din app til det virtuelle netværk ved hjælp af PowerShell #

## <a name="overview"></a>Oversigt ##

I Azure App-tjeneste, kan du forbinde din app (web, mobile eller API) til et Azure virtuelle netværk (VNet) i dit abonnement. Denne funktion kaldes VNet Integration. Funktionen VNet Integration må ikke forveksles med funktionen App Service-miljø, som gør det muligt at køre en forekomst af Azure App tjeneste i netværket virtuel.

Funktionen VNet Integration har en brugergrænseflade (UI) i den nye portal, som du kan bruge til at integrere med virtuelle netværk, der er installeret ved hjælp af den klassiske implementeringsmodel eller implementeringsmodel Azure ressourcestyring. Hvis du vil vide mere om funktionen, skal du se [integrere din app med en Azure virtuelt netværk](web-sites-integrate-with-vnet.md).

I denne artikel er ikke om at bruge Brugergrænsefladen, men hellere om, hvordan du aktiverer integration ved hjælp af PowerShell. Denne artikel indeholder en sektion til hver implementeringsmodel, fordi kommandoerne for hver implementeringsmodel er forskellige.  

Før du fortsætter med denne artikel, skal du sikre, at du har:

- Senest Azure PowerShell SDK installeret. Du kan installere med Web Platform Installer.
- En app i Azure App Service kører i et Standard eller Premium SKU.

## <a name="classic-virtual-networks"></a>Klassisk virtuelle netværk ##

I dette afsnit forklares tre opgaver til virtuelle netværk, der bruger den klassiske implementeringsmodel:

1. Forbind din app til et eksisterende virtuelt netværk, der har en gateway og er konfigureret til punkt-til-site connectivity.
1. Opdater dine virtuelt netværk integration oplysninger om din app.
1. Afbryd din app fra netværket virtuel.

### <a name="connect-an-app-to-a-classic-vnet"></a>Forbinde en app til en klassisk VNet ###

For at forbinde en app til et virtuelt netværk, skal du følge disse tre trin:

1. Erklære web App, det vil kunne deltage i et bestemt virtuelt netværk. Appen genererer et certifikat, der gives til det virtuelle netværk til punkt-til-site connectivity.
1. Upload web app-certifikat til det virtuelle netværk, og derefter hente pakken punkt-til-site VPN-URI.
1. Opdatere den online virtuel netværksforbindelse med pakken punkt-til-site URI.

De første og tredje trin er fuldt tilgængelig for scripts, men det andet trin kræver en engangsadgangskode, manuel handling via portalen, eller adgang til at udføre **sætte** eller **programrettelse** handlinger på virtuelt netværk Azure ressourcestyring slutpunktet. Kontakte Azure Support for at få dette aktiveret. Før du begynder, skal du kontrollere, at du har et klassisk virtuelt netværk, der har forbindelse til punkt-til-site allerede aktiveret, og en udløst gateway. For at oprette gatewayen og aktivere punkt-til-site forbindelse, du vil bruge på portalen, som det er beskrevet i [oprette en VPN-gateway][createvpngateway].

Klassisk virtuelt netværk skal være i samme abonnement som din App Service-plan, der indeholder app, som du integrerer med.

##### <a name="set-up-azure-powershell-sdk"></a>Konfigurere Azure PowerShell SDK #####

Åbne en PowerShell-vinduet og konfigurere dine Azure-konto og abonnement ved hjælp af:

    Login-AzureRmAccount

Denne kommando åbner en meddelelse om at få dine Azure legitimationsoplysninger. Når du logger på, kan du bruge en af følgende kommandoer til at markere det abonnement, du vil bruge. Sørg for, at du bruger det abonnement, dine virtuelt netværk og App-serviceaftale er i.

    Select-AzureRmSubscription –SubscriptionName [WebAppSubscriptionName]

eller

    Select-AzureRmSubscription –SubscriptionId [WebAppSubscriptionId]

##### <a name="variables-used-in-this-article"></a>Variabler, der bruges i denne artikel #####

For at forenkle kommandoer, skal du angive en **$Configuration** PowerShell variabel med den specifikke variant.

Angive en variabel på følgende måde i PowerShell med følgende parametre:

    $Configuration = @{}
    $Configuration.WebAppResourceGroup = "[Your web app resource group]"
    $Configuration.WebAppName = "[Your web app name]"
    $Configuration.VnetSubscriptionId = "[Your vnet subscription id]"
    $Configuration.VnetResourceGroup = "[Your vnet resource group]"
    $Configuration.VnetName = "[Your vnet name]"

App placeringen skal være placering uden mellemrum. Vest USA er for eksempel westus.

    $Configuration.WebAppLocation = "[Your web app Location]"

Det næste element er, hvor certifikatet, der skal skrives. Det skal være et skrivbart sti på din lokale computer. Sørg for at medtage .cer i slutningen.

    $Configuration.GeneratedCertificatePath = "[C:\Path\To\Certificate.cer]"

For at se, hvad du angive skal du skrive **$Configuration**.

    > $Configuration

    Name                           Value
    ----                           -----
    GeneratedCertificatePath       C:\vnetcert.cer
    VnetSubscriptionId             efc239a4-88f9-2c5e-a9a1-3034c21ad496
    WebAppResourceGroup            vnetdemo-rg
    VnetResourceGroup              testase1-rg
    VnetName                       TestNetwork
    WebAppName                     vnetintdemoapp
    WebAppLocation                 centralus

Resten af dette afsnit antages det, at du har en variabel, der er oprettet som lige beskrevet.

##### <a name="declare-the-virtual-network-to-the-app"></a>Erklære det virtuelle netværk til appen #####

Brug følgende kommando til at se app, den anvender denne bestemt virtuelt netværk. Dette medfører app til at oprette det er nødvendigt certifikater:

    $vnet = New-AzureRmResource -Name "$($Configuration.WebAppName)/$($Configuration.VnetName)" -ResourceGroupName $Configuration.WebAppResourceGroup -ResourceType "Microsoft.Web/sites/virtualNetworkConnections" -PropertyObject @{"VnetResourceId" = "/subscriptions/$($Configuration.VnetSubscriptionId)/resourceGroups/$($Configuration.VnetResourceGroup)/providers/Microsoft.ClassicNetwork/virtualNetworks/$($Configuration.VnetName)"} -Location $Configuration.WebAppLocation -ApiVersion 2015-07-01

Hvis denne kommando lykkes, har **$vnet** en **Egenskaber** variabel i den. Variablen **Egenskaber** skal indeholde både et certifikat miniature og certifikatets data.

##### <a name="upload-the-web-app-certificate-to-the-virtual-network"></a>Overføre web app-certifikat til det virtuelle netværk #####

En manuel enkeltstående trin er påkrævet for hver virtuelt netværk kombination og abonnement. Det vil sige, hvis du opretter forbindelse apps i abonnement A virtuelt netværk a, skal du udføre dette trin kun, når du konfigurerer uanset hvor mange apps. Hvis du føjer en ny app til en anden virtuelt netværk, skal du gøre dette igen. Årsagen til dette er, at et sæt af certifikater oprettes på et abonnementsniveau i Azure App Service, og sæt genereres en gang for hvert virtuelle netværk, som apps opretter forbindelse til.

Certifikaterne er allerede blevet indstiller Hvis du har fulgt disse trin, eller hvis du integreret med det samme virtuelle netværk ved hjælp af portalen.

Det første trin er at generere .cer-filen. Det andet trin er at overføre .cer-filen til din virtuelt netværk. Du kan generere .cer-filen fra kald af API i det tidligere trin ved at køre følgende kommandoer.

    $certBytes = [System.Convert]::FromBase64String($vnet.Properties.certBlob)
    [System.IO.File]::WriteAllBytes("$($Configuration.GeneratedCertificatePath)", $certBytes)

Certifikatet, der skal findes på placeringen, **$Configuration.GeneratedCertificatePath** angiver.

Hvis du vil overføre certifikatet manuelt, skal du bruge [Azure portal] [ azureportal] og **Gennemse virtuelt netværk (klassisk)** > **VPN-forbindelser** > **punkt-til-site** > **Administrer certifikater**. Herfra kan du overføre dit certifikat.

##### <a name="get-the-point-to-site-package"></a>Få pakken punkt-til-websted #####

Næste trin i at konfigurere en VPN-forbindelse på en online er at hente pakken punkt-til-websted og giver dem til din online.

Gemme følgende skabelon til en fil kaldet GetNetworkPackageUri.json et sted på computeren, for eksempel C:\Azure\Templates\GetNetworkPackageUri.json.

    {
        "$schema": "http://schema.management.azure.com/schemas/2014-04-01-preview/deploymentTemplate.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {
            "certData": {
                "type": "string"
            },
            "certThumbprint": {
                "type": "string"
            },
            "networkName": {
                "type": "string"
            }
        },
        "variables": {
            "legacyVnetName": "[concat('Group ', resourceGroup().name, ' ', parameters('networkName'))]"
            },
            "resources": [
            ],
        "outputs" : {
            "PackageUri" :
            {
            "value" : "[listPackage(resourceId('Microsoft.ClassicNetwork/virtualNetworks/gateways/clientRootCertificates', parameters('networkName'), 'primary', parameters('certThumbprint')), '2014-06-01').packageUri]", "type" : "string"
            }
        }
    }


Angive inputparametre:

    $parameters = @{"certData" = $vnet.Properties.certBlob ;
    certThumbprint = $vnet.Properties.certThumbprint ;
    "networkName" = $Configuration.VnetName }

Ringe til scriptet:

    $output = New-AzureRmResourceGroupDeployment -Name unused -ResourceGroupName $Configuration.VnetResourceGroup -TemplateParameterObject $parameters -TemplateFile C:\PATH\TO\GetNetworkPackageUri.json


Variablen **$output. Outputs.packageUri** nu indeholder pakken URI tildeles til din online.

##### <a name="upload-the-point-to-site-package-to-your-app"></a>Overføre pakken punkt-til-websted til din app #####

Det sidste trin er at tilvejebringe appen med denne pakke. Skal du bare køre den næste kommando:

    $vnet = New-AzureRmResource -Name "$($Configuration.WebAppName)/$($Configuration.VnetName)/primary" -ResourceGroupName $Configuration.WebAppResourceGroup -ResourceType "Microsoft.Web/sites/virtualNetworkConnections/gateways" -ApiVersion 2015-07-01 -PropertyObject @{"VnetName" = $Configuration.VnetName ; "VpnPackageUri" = $($output.Outputs.packageUri).Value } -Location $Configuration.WebAppLocation

Hvis en meddelelse beder dig om at bekræfte, at du overskriver en eksisterende ressource, skal du sørge for at tillade den.

Når denne kommando er oprettet, skal din app nu have forbindelse til det virtuelle netværk. Gå til app-konsollen, for at bekræfte succes, og skrive følgende:

    SET WEBSITE_

Hvis der er en miljøvariablen kaldet WEBSITE_VNETNAME, der indeholder en værdi, der svarer til navnet på det virtuelle destinationsadresse-netværk, har alle konfigurationer lykkedes.

### <a name="update-classic-vnet-integration-information"></a>Opdatere klassisk VNet integration oplysninger ###

Opdatere eller synkroniseres dine oplysninger igen, skal du gentage de trin, som du har fulgt, da du oprettede integrationen i første omgang. Disse trin er:

1. Definere dine konfigurationsoplysninger.
1. Erklære det virtuelle netværk til appen.
1. Få pakken punkt-til-websted.
1. Overføre pakken punkt-til-websted til din app.

### <a name="disconnect-your-app-from-a-classic-vnet"></a>Afbryd din app fra en klassisk VNet ###

Hvis du vil afbryde app, skal du de konfigurationsoplysninger, der blev angivet under virtuelt netværksintegration. Brug af disse oplysninger, er der derefter én kommando afbryde forbindelsen til din app fra netværket virtuel.

    $vnet = Remove-AzureRmResource -Name "$($Configuration.WebAppName)/$($Configuration.VnetName)" -ResourceGroupName $Configuration.WebAppResourceGroup -ResourceType "Microsoft.Web/sites/virtualNetworkConnections" -ApiVersion 2015-07-01

## <a name="resource-manager-virtual-networks"></a>Ressourcestyring virtuelle netværk ##

Ressourcestyring virtuelle netværk har Azure ressourcestyring API'er, som forenkle nogle processer, når der sammenlignes med klassisk virtuelle netværk. Vi har et script, som hjælper dig med at udføre følgende opgaver:

- Oprette et virtuelt netværk ressourcestyring og integrere din app.
- Oprette en gateway, konfigurere netværksmuligheder for punkt-til-websted i et eksisterende ressourcestyring virtuelt netværk og derefter integrere din app.
- Integrere din app med et eksisterende ressourcestyring virtuelt netværk, der indeholder en gateway og punkt-til-site-connectivity aktiveret.
- Afbryd din app fra netværket virtuel.

### <a name="resource-manager-vnet-app-service-integration-script"></a>Ressourcestyring VNet App Service integration script ###

Kopier følgende script, og Gem den til en fil. Hvis du ikke vil bruge scriptet, Velkommen til at få mere at vide fra den for at se, hvordan du konfigurerer ting med et virtuelt netværk ressourcestyring.


    function ReadHostWithDefault($message, $default)
    {
        $result = Read-Host "$message [$default]"
        if($result -eq "")
        {
            $result = $default
        }
            return $result
        }

    function PromptCustom($title, $optionValues, $optionDescriptions)
    {
        Write-Host $title
        Write-Host
        $a = @()
        for($i = 0; $i -lt $optionValues.Length; $i++){
            Write-Host "$($i+1))" $optionDescriptions[$i]
        }
        Write-Host

        while($true)
        {
            Write-Host "Choose an option: "
            $option = Read-Host
            $option = $option -as [int]

            if($option -ge 1 -and $option -le $optionValues.Length)
            {
                return $optionValues[$option-1]
            }
        }
    }

    function PromptYesNo($title, $message, $default = 0)
    {
        $yes = New-Object System.Management.Automation.Host.ChoiceDescription "&Yes", ""
        $no = New-Object System.Management.Automation.Host.ChoiceDescription "&No", ""
        $options = [System.Management.Automation.Host.ChoiceDescription[]]($yes, $no)
        $result = $host.ui.PromptForChoice($title, $message, $options, $default)
        return $result
    }

    function CreateVnet($resourceGroupName, $vnetName, $vnetAddressSpace, $vnetGatewayAddressSpace, $location)
    {
        Write-Host "Creating a new VNET"
        $gatewaySubnet = New-AzureRmVirtualNetworkSubnetConfig -Name "GatewaySubnet" -AddressPrefix $vnetGatewayAddressSpace
        New-AzureRmVirtualNetwork -Name $vnetName -ResourceGroupName $resourceGroupName -Location $location -AddressPrefix $vnetAddressSpace -Subnet $gatewaySubnet
    }

    function CreateVnetGateway($resourceGroupName, $vnetName, $vnetIpName, $location, $vnetIpConfigName, $vnetGatewayName, $certificateData, $vnetPointToSiteAddressSpace)
    {
        $vnet = Get-AzureRmVirtualNetwork -Name $vnetName -ResourceGroupName $resourceGroupName
        $subnet=Get-AzureRmVirtualNetworkSubnetConfig -Name "GatewaySubnet" -VirtualNetwork $vnet

        Write-Host "Creating a public IP address for this VNET"
        $pip = New-AzureRmPublicIpAddress -Name $vnetIpName -ResourceGroupName $resourceGroupName -Location $location -AllocationMethod Dynamic
        $ipconf = New-AzureRmVirtualNetworkGatewayIpConfig -Name $vnetIpConfigName -Subnet $subnet -PublicIpAddress $pip

        Write-Host "Adding a root certificate to this VNET"
        $root = New-AzureRmVpnClientRootCertificate -Name "AppServiceCertificate.cer" -PublicCertData $certificateData

        Write-Host "Creating Azure VNET Gateway. This may take up to an hour."
        New-AzureRmVirtualNetworkGateway -Name $vnetGatewayName -ResourceGroupName $resourceGroupName -Location $location -IpConfigurations $ipconf -GatewayType Vpn -VpnType RouteBased -EnableBgp $false -GatewaySku Basic -VpnClientAddressPool $vnetPointToSiteAddressSpace -VpnClientRootCertificates $root
    }

    function AddNewVnet($subscriptionId, $webAppResourceGroup, $webAppName)
    {
        Write-Host "Adding a new Vnet"
        Write-Host
        $vnetName = Read-Host "Specify a name for this Virtual Network"

        $vnetGatewayName="$($vnetName)-gateway"
        $vnetIpName="$($vnetName)-ip"
        $vnetIpConfigName="$($vnetName)-ipconf"

        # Virtual Network settings
        $vnetAddressSpace="10.0.0.0/8"
        $vnetGatewayAddressSpace="10.5.0.0/16"
        $vnetPointToSiteAddressSpace="172.16.0.0/16"

        $changeRequested = 0
        $resourceGroupName = $webAppResourceGroup

        while($changeRequested -eq 0)
        {
            Write-Host
            Write-Host "Currently, I will create a VNET with the following settings:"
            Write-Host
            Write-Host "Virtual Network Name: $vnetName"
            Write-Host "Resource Group Name:  $resourceGroupName"
            Write-Host "Gateway Name: $vnetGatewayName"
            Write-Host "Vnet IP name: $vnetIpName"
            Write-Host "Vnet IP config name:  $vnetIpConfigName"
            Write-Host "Address Space:$vnetAddressSpace"
            Write-Host "Gateway Address Space:$vnetGatewayAddressSpace"
            Write-Host "Point-To-Site Address Space:  $vnetPointToSiteAddressSpace"
            Write-Host
            $changeRequested = PromptYesNo "" "Do you wish to change these settings?" 1

            if($changeRequested -eq 0)
            {
                $vnetName = ReadHostWithDefault "Virtual Network Name" $vnetName
                $resourceGroupName = ReadHostWithDefault "Resource Group Name" $resourceGroupName
                $vnetGatewayName = ReadHostWithDefault "Vnet Gateway Name" $vnetGatewayName
                $vnetIpName = ReadHostWithDefault "Vnet IP name" $vnetIpName
                $vnetIpConfigName = ReadHostWithDefault "Vnet IP configuration name" $vnetIpConfigName
                $vnetAddressSpace = ReadHostWithDefault "Vnet Address Space" $vnetAddressSpace
                $vnetGatewayAddressSpace = ReadHostWithDefault "Vnet Gateway Address Space" $vnetGatewayAddressSpace
                $vnetPointToSiteAddressSpace = ReadHostWithDefault "Vnet Point-to-site Address Space" $vnetPointToSiteAddressSpace
            }
        }

        $ErrorActionPreference = "Stop";

        # We create the virtual network and add it here. The way this works is:
        # 1) Add the VNET association to the App. This allows the App to generate certificates, etc. for the VNET.
        # 2) Create the VNET and VNET gateway, add the certificates, create the public IP, etc., required for the gateway
        # 3) Get the VPN package from the gateway and pass it back to the App.

        $webApp = Get-AzureRmResource -ResourceName $webAppName -ResourceType "Microsoft.Web/sites" -ApiVersion 2015-08-01 -ResourceGroupName $webAppResourceGroup
        $location = $webApp.Location

        Write-Host "Creating App association to VNET"
        $propertiesObject = @{
         "vnetResourceId" = "/subscriptions/$($subscriptionId)/resourceGroups/$($resourceGroupName)/providers/Microsoft.Network/virtualNetworks/$($vnetName)"
        }
        $virtualNetwork = New-AzureRmResource -Location $location -Properties $PropertiesObject -ResourceName "$($webAppName)/$($vnetName)" -ResourceType "Microsoft.Web/sites/virtualNetworkConnections" -ApiVersion 2015-08-01 -ResourceGroupName $webAppResourceGroup -Force

        CreateVnet $resourceGroupName $vnetName $vnetAddressSpace $vnetGatewayAddressSpace $location

        CreateVnetGateway $resourceGroupName $vnetName $vnetIpName $location $vnetIpConfigName $vnetGatewayName $virtualNetwork.Properties.CertBlob $vnetPointToSiteAddressSpace

        Write-Host "Retrieving VPN Package and supplying to App"
        $packageUri = Get-AzureRmVpnClientPackage -ResourceGroupName $resourceGroupName -VirtualNetworkGatewayName $vnetGatewayName -ProcessorArchitecture Amd64

        # Put the VPN client configuration package onto the App
        $PropertiesObject = @{
        "vnetName" = $VirtualNetworkName; "vpnPackageUri" = $packageUri
        }

        New-AzureRmResource -Location $location -Properties $PropertiesObject -ResourceName "$($webAppName)/$($vnetName)/primary" -ResourceType "Microsoft.Web/sites/virtualNetworkConnections/gateways" -ApiVersion 2015-08-01 -ResourceGroupName $webAppResourceGroup -Force

        Write-Host "Finished!"
    }

    function AddExistingVnet($subscriptionId, $resourceGroupName, $webAppName)
    {
        $ErrorActionPreference = "Stop";

        # At this point, the gateway should be able to be joined to an App, but may require some minor tweaking. We will declare to the App now to use this VNET
        Write-Host "Getting App information"
        $webApp = Get-AzureRmResource -ResourceName $webAppName -ResourceType "Microsoft.Web/sites" -ApiVersion 2015-08-01 -ResourceGroupName $resourceGroupName
        $location = $webApp.Location

        $webAppConfig = Get-AzureRmResource -ResourceName "$($webAppName)/web" -ResourceType "Microsoft.Web/sites/config" -ApiVersion 2015-08-01 -ResourceGroupName $resourceGroupName
        $currentVnet = $webAppConfig.Properties.VnetName
        if($currentVnet -ne $null -and $currentVnet -ne "")
        {
            Write-Host "Currently connected to VNET $currentVnet"
        }

        # Display existing vnets
        $vnets = Get-AzureRmVirtualNetwork
        $vnetNames = @()
        foreach($vnet in $vnets)
        {
            $vnetNames += $vnet.Name
        }

        Write-Host
        $vnet = PromptCustom "Select a VNET to integrate with" $vnets $vnetNames

        # We need to check if this VNET is able to be joined to a App, based on following criteria
            # If there is no gateway, we can create one.
            # If there is a gateway:
                # It must be of type Vpn
                # It must be of VpnType RouteBased
                # If it doesn't have the right certificate, we will need to add it.
                # If it doesn't have a point-to-site range, we will need to add it.

        $gatewaySubnet = $vnet.Subnets | Where-Object { $_.Name -eq "GatewaySubnet" }

        if($gatewaySubnet -eq $null -or $gatewaySubnet.IpConfigurations -eq $null -or $gatewaySubnet.IpConfigurations.Count -eq 0)
        {
            $ErrorActionPreference = "Continue";
            # There is no gateway. We need to create one.
            Write-Host "This Virtual Network has no gateway. I will need to create one."

            $vnetName = $vnet.Name
            $vnetGatewayName="$($vnetName)-gateway"
            $vnetIpName="$($vnetName)-ip"
            $vnetIpConfigName="$($vnetName)-ipconf"

            # Virtual Network settings
            $vnetAddressSpace="10.0.0.0/8"
            $vnetGatewayAddressSpace="10.5.0.0/16"
            $vnetPointToSiteAddressSpace="172.16.0.0/16"

            $changeRequested = 0

            Write-Host "Your VNET is in the address space $($vnet.AddressSpace.AddressPrefixes), with the following Subnets:"
            foreach($subnet in $vnet.Subnets)
            {
                Write-Host "$($subnet.Name): $($subnet.AddressPrefix)"
            }

            $vnetGatewayAddressSpace = Read-Host "Please choose a GatewaySubnet address space"

            while($changeRequested -eq 0)
            {
                Write-Host
                Write-Host "Currently, I will create a VNET gateway with the following settings:"
                Write-Host
                Write-Host "Virtual Network Name: $vnetName"
                Write-Host "Resource Group Name:  $($vnet.ResourceGroupName)"
                Write-Host "Gateway Name: $vnetGatewayName"
                Write-Host "Vnet IP name: $vnetIpName"
                Write-Host "Vnet IP config name:  $vnetIpConfigName"
                Write-Host "Address Space:$($vnet.AddressSpace.AddressPrefixes)"
                Write-Host "Gateway Address Space:$vnetGatewayAddressSpace"
                Write-Host "Point-To-Site Address Space:  $vnetPointToSiteAddressSpace"
                Write-Host
                $changeRequested = PromptYesNo "" "Do you wish to change these settings?" 1

                if($changeRequested -eq 0)
                {
                    $vnetGatewayName = ReadHostWithDefault "Vnet Gateway Name" $vnetGatewayName
                    $vnetIpName = ReadHostWithDefault "Vnet IP name" $vnetIpName
                    $vnetIpConfigName = ReadHostWithDefault "Vnet IP configuration name" $vnetIpConfigName
                    $vnetGatewayAddressSpace = ReadHostWithDefault "Vnet Gateway Address Space" $vnetGatewayAddressSpace
                    $vnetPointToSiteAddressSpace = ReadHostWithDefault "Vnet Point-to-site Address Space" $vnetPointToSiteAddressSpace
                }
            }

            $ErrorActionPreference = "Stop";

            Write-Host "Creating App association to VNET"
            $propertiesObject = @{
             "vnetResourceId" = "/subscriptions/$($subscriptionId)/resourceGroups/$($vnet.ResourceGroupName)/providers/Microsoft.Network/virtualNetworks/$($vnetName)"
            }

            $virtualNetwork = New-AzureRmResource -Location $location -Properties $PropertiesObject -ResourceName "$($webAppName)/$($vnet.Name)" -ResourceType "Microsoft.Web/sites/virtualNetworkConnections" -ApiVersion 2015-08-01 -ResourceGroupName $resourceGroupName -Force

            # If there is no gateway subnet, we need to create one.
            if($gatewaySubnet -eq $null)
            {
                $gatewaySubnet = New-AzureRmVirtualNetworkSubnetConfig -Name "GatewaySubnet" -AddressPrefix $vnetGatewayAddressSpace
                $vnet.Subnets.Add($gatewaySubnet);
                Set-AzureRmVirtualNetwork -VirtualNetwork $vnet
            }

            CreateVnetGateway $vnet.ResourceGroupName $vnetName $vnetIpName $location $vnetIpConfigName $vnetGatewayName $virtualNetwork.Properties.CertBlob $vnetPointToSiteAddressSpace

            $gateway = Get-AzureRmVirtualNetworkGateway -ResourceGroupName $vnet.ResourceGroupName -Name $vnetGatewayName
        }
        else
        {
            $uriParts = $gatewaySubnet.IpConfigurations[0].Id.Split('/')
            $gatewayResourceGroup = $uriParts[4]
            $gatewayName = $uriParts[8]

            $gateway = Get-AzureRmVirtualNetworkGateway -ResourceGroupName $vnet.ResourceGroupName -Name $gatewayName

            # validate gateway types, etc.
            if($gateway.GatewayType -ne "Vpn")
            {
                Write-Error "This gateway is not of the Vpn type. It cannot be joined to an App."
                return
            }

            if($gateway.VpnType -ne "RouteBased")
            {
                Write-Error "This gateways Vpn type is not RouteBased. It cannot be joined to an App."
                return
            }

            if($gateway.VpnClientConfiguration -eq $null -or $gateway.VpnClientConfiguration.VpnClientAddressPool -eq $null)
            {
                Write-Host "This gateway does not have a Point-to-site Address Range. Please specify one in CIDR notation, e.g. 10.0.0.0/8"
                $pointToSiteAddress = Read-Host "Point-To-Site Address Space"
                Set-AzureRmVirtualNetworkGatewayVpnClientConfig -VirtualNetworkGateway $gateway.Name -VpnClientAddressPool $pointToSiteAddress
            }

            Write-Host "Creating App association to VNET"
            $propertiesObject = @{
             "vnetResourceId" = "/subscriptions/$($subscriptionId)/resourceGroups/$($vnet.ResourceGroupName)/providers/Microsoft.Network/virtualNetworks/$($vnet.Name)"
            }

            $virtualNetwork = New-AzureRmResource -Location $location -Properties $PropertiesObject -ResourceName "$($webAppName)/$($vnet.Name)" -ResourceType "Microsoft.Web/sites/virtualNetworkConnections" -ApiVersion 2015-08-01 -ResourceGroupName $resourceGroupName -Force

            # We need to check if the certificate here exists in the gateway.
            $certificates = $gateway.VpnClientConfiguration.VpnClientRootCertificates

            $certFound = $false
            foreach($certificate in $certificates)
            {
                if($certificate.PublicCertData -eq $virtualNetwork.Properties.CertBlob)
                {
                    $certFound = $true
                    break
                }
            }

            if(-not $certFound)
            {
                Write-Host "Adding certificate"
                Add-AzureRmVpnClientRootCertificate -VpnClientRootCertificateName "AppServiceCertificate.cer" -PublicCertData $virtualNetwork.Properties.CertBlob -VirtualNetworkGatewayName $gateway.Name
            }
        }

        # Now finish joining by getting the VPN package and giving it to the App
        Write-Host "Retrieving VPN Package and supplying to App"
        $packageUri = Get-AzureRmVpnClientPackage -ResourceGroupName $vnet.ResourceGroupName -VirtualNetworkGatewayName $gateway.Name -ProcessorArchitecture Amd64

        # Put the VPN client configuration package onto the App
        $PropertiesObject = @{
        "vnetName" = $vnet.Name; "vpnPackageUri" = $packageUri
        }

        New-AzureRmResource -Location $location -Properties $PropertiesObject -ResourceName "$($webAppName)/$($vnet.Name)/primary" -ResourceType "Microsoft.Web/sites/virtualNetworkConnections/gateways" -ApiVersion 2015-08-01 -ResourceGroupName $resourceGroupName -Force

        Write-Host "Finished!"
    }

    function RemoveVnet($subscriptionId, $resourceGroupName, $webAppName)
    {
        $webAppConfig = Get-AzureRmResource -ResourceName "$($webAppName)/web" -ResourceType "Microsoft.Web/sites/config" -ApiVersion 2015-08-01 -ResourceGroupName $resourceGroupName
        $currentVnet = $webAppConfig.Properties.VnetName
        if($currentVnet -ne $null -and $currentVnet -ne "")
        {
            Write-Host "Currently connected to VNET $currentVnet"

            Remove-AzureRmResource -ResourceName "$($webAppName)/$($currentVnet)" -ResourceType "Microsoft.Web/sites/virtualNetworkConnections" -ApiVersion 2015-08-01 -ResourceGroupName $resourceGroupName
        }
          else
        {
          Write-Host "Not connected to a VNET."
        }
    }

    Write-Host "Please Login"
    Login-AzureRmAccount

    # Choose subscription. If there's only one we will choose automatically

    $subs = Get-AzureRmSubscription
    $subscriptionId = ""

    if($subs.Length -eq 0)
    {
        Write-Error "No subscriptions bound to this account."
        return
    }

    if($subs.Length -eq 1)
    {
        $subscriptionId = $subs[0].SubscriptionId
    }
    else
    {
        $subscriptionChoices = @()
        $subscriptionValues = @()

        foreach($subscription in $subs){
        $subscriptionChoices += "$($subscription.SubscriptionName) ($($subscription.SubscriptionId))";
        $subscriptionValues += ($subscription.SubscriptionId);
        }

        $subscriptionId = PromptCustom "Choose a subscription" $subscriptionValues $subscriptionChoices
    }

    Select-AzureRmSubscription -SubscriptionId $subscriptionId

    $resourceGroup = Read-Host "Please enter the Resource Group of your App"

    $appName = Read-Host "Please enter the Name of your App"

    $options = @("Add a NEW Virtual Network to an App", "Add an EXISTING Virtual Network to an App", "Remove a Virtual Network from an App");
    $optionValues = @(0, 1, 2)
    $option = PromptCustom "What do you want to do?" $optionValues $options

    if($option -eq 0)
    {
        AddNewVnet $subscriptionId $resourceGroup $appName
    }
    if($option -eq 1)
    {
        AddExistingVnet $subscriptionId $resourceGroup $appName
    }
    if($option -eq 2)
    {
        RemoveVnet $subscriptionId $resourceGroup $appName
    }

Gemme en kopi af scriptet. Den hedder V2VnetAllinOne.ps1 i denne artikel, men du kan bruge et andet navn. Der findes ingen argumenter til dette script. Du skal du bare køre den. Det første, du vil udføre scriptet er bede dig om at logge på. Når du logger på, scriptet henter oplysninger om din konto og returnerer en liste over abonnementer. Ikke optælling anmodning om dine legitimationsoplysninger, ser indledende script udførelsen sådan ud:

    PS C:\Users\ccompy\Documents\VNET> .\V2VnetAllInOne.ps1
    Please Login

    Environment           : AzureCloud
    Account               : ccompy@microsoft.com
    TenantId              : 722278f-fef1-499f-91ab-2323d011db47
    SubscriptionId        : af5358e1-acac-2c90-a9eb-722190abf47a
    CurrentStorageAccount :

    Choose a subscription

    1) Demo abonnement (af5358e1-acac-2c90-a9eb-722190abf47a)
    2) MS Test (a5350f55-dd5a-41ec-2ddb-ff7b911bb2ef)
    3) Lilla Demo abonnement (2d4c99a4-57f9-4d5e-a0a1-0034c52db59d)

    Vælg en indstilling: 3

    Konto: ccompy@microsoft.com miljø: AzureCloud abonnement: 2d4c99a4-57f9-4d5e-a0a1-0034c52db59d lejer: 722278f-fef1-499f-91ab-2323d011db47

    Angiv ressourcegruppe af din App: hcdemo-indbyggede rg skal du skrive navnet på din App: v2vnetpowershell Hvad vil du gøre?

    1) Føje et nyt virtuelt netværk til en App
    2) Føje en eksisterende virtuelt netværk til en App
    3) Fjerne et virtuelt netværk fra en App

Resten af dette afsnit beskrives hver af disse tre indstillinger.

### <a name="create-a-resource-manager-vnet-and-integrate-with-it"></a>Oprette en ressourcestyring VNet og integrere med den ###

For at oprette et nyt virtuelt netværk, der bruger implementeringsmodel ressourcestyring og integrere med din app, vælge **1) føje et nyt virtuelt netværk til en App**. Dette vil bede dig om navnet på det virtuelle netværk. I min tilfælde, som du kan se i de følgende indstillinger, jeg har brugt navnet, v2pshell.

Scriptet giver oplysninger om det virtuelle netværk, der skal oprettes. Hvis jeg vil, kan jeg ændre nogen af værdierne. I dette eksempel udførelse, jeg har oprettet et virtuelt netværk, der indeholder følgende indstillinger:

    Virtual Network Name:         v2pshell
    Resource Group Name:          hcdemo-rg
    Gateway Name:                 v2pshell-gateway
    Vnet IP name:                 v2pshell-ip
    Vnet IP config name:          v2pshell-ipconf
    Address Space:                10.0.0.0/8
    Gateway Address Space:        10.5.0.0/16
    Point-To-Site Address Space:  172.16.0.0/12

    Do you wish to change these settings?
    [Y] Yes  [N] No  [?] Help (default is "N"):

Hvis du vil ændre nogen af værdierne, Skriv **Y** , og foretag ændringerne. Når du er tilfreds med de virtuelle netværksindstillinger, skrive **N** eller blot trykke på Enter, når du bliver bedt om at ændre indstillingerne. Derfra på indtil fuldførelse scriptet fortæller dig, at nogle af hvad det ' anden at gøre, indtil den begynder at oprette gatewayen virtuelt netværk. Dette trin kan tage op til en time. Der er ingen statusindikator i denne fase, men scriptet fortæller dig, når gatewayen er blevet oprettet.

Når scriptet afsluttes, der står **færdig**. På dette tidspunkt har du et virtuelt ressourcestyring-netværk, der indeholder navn og indstillinger, du har valgt. Denne nye virtuelle netværk vil også blive integreret med din app.

### <a name="integrate-your-app-with-a-preexisting-resource-manager-vnet"></a>Integrere din app med en eksisterende ressourcestyring VNet ###

Når du integreres med et eksisterende virtuelt netværk og, hvis du angiver et virtuelt ressourcestyring-netværk, der ikke har en gateway eller forbindelse til punkt-til-websted, angive scriptet,. Hvis VNET allerede har disse ting, du konfigurere, går scriptet direkte til app-integration. Hvis du vil starte denne proces, skal du blot markere **2) føje en eksisterende virtuelt netværk til en App**.

Denne indstilling virker kun, hvis du har et eksisterende ressourcestyring virtuelt netværk, der er i samme abonnement som din app. Når du vælger indstillingen, vises der en liste over dine ressourcestyring virtuelle netværk.   

    Select a VNET to integrate with

    1) v2demonetwork
    2) v2pshell
    3) v2vnetintdemo
    4) v2asenetwork
    5) v2pshell2

    Vælg en indstilling: 5

Det virtuelle netværk, du vil integrere med du blot markere. Hvis du allerede har en gateway, der har forbindelse, punkt-til-websted, integrere scriptet blot din app til dit virtuelle netværk. Hvis du ikke har en gateway, skal du angive gateway-undernettet. Dit gateway-undernet skal være i din virtuelt netværk adresseområde, og det kan være i andre undernet. Hvis du har et virtuelt netværk uden en gateway, og Kør dette trin, ting, du ser sådan ud:

    This Virtual Network has no gateway. I will need to create one.
    Your VNET is in the address space 172.16.0.0/16, with the following Subnets:
    default: 172.16.0.0/24
    Please choose a GatewaySubnet address space: 172.16.1.0/26

I dette eksempel, jeg har oprettet et virtuelt netværksgateway, der har følgende indstillinger:

    Virtual Network Name:         v2pshell2
    Resource Group Name:          vnetdemo-rg
    Gateway Name:                 v2pshell2-gateway
    Vnet IP name:                 v2pshell2-ip
    Vnet IP config name:          v2pshell2-ipconf
    Address Space:                172.16.0.0/16
    Gateway Address Space:        172.16.1.0/26
    Point-To-Site Address Space:  172.16.0.0/12

    Do you wish to change these settings?
    [Y] Yes  [N] No  [?] Help (default is "N"):
    Creating App association to VNET

Hvis du vil ændre nogen af disse indstillinger, kan du gøre det. Ellers skal du trykke på Enter og scriptet oprettes din gateway, og vedhæft din app til virtuelle netværk. Klokkeslættet for oprettelsen af gatewayen er dog stadig en time, så sørg for, at huske. Når alt er afsluttet, står scriptet **færdig**.

### <a name="disconnect-your-app-from-a-resource-manager-vnet"></a>Afbryd din app fra en ressourcestyring VNet ###

Afbryde forbindelsen netværket virtuel til din app ikke notere gatewayen eller deaktivere punkt-til-site connectivity. Du kan, når alle bruge den til noget andet. Det også afbryder ikke den fra andre apps bortset fra den del, du har angivet. For at udføre denne handling skal du vælge **3) fjerne et virtuelt netværk fra en App**. Når du gør dette, vil du se nogenlunde sådan ud:

    Currently connected to VNET v2pshell

    Confirm
    Are you sure you want to delete the following resource:
    /subscriptions/edcc99a4-b7f9-4b5e-a9a1-3034c51db496/resourceGroups/hcdemo-rg/providers/Microsoft.Web/sites/v2vnetpowers
    hell/virtualNetworkConnections/v2pshell
    [Y] Yes  [N] No  [S] Suspend  [?] Help (default is "Y"):

Selvom scriptet står delete, slettes det virtuelle netværk ikke. Det kun fjerne integrationen. Når du har bekræftet, at dette er, hvad du vil gøre, kommandoen behandles helt hurtigt og fortæller dig er **Sand** , når guiden er fuldført.

<!--Links-->
[createvpngateway]: http://azure.microsoft.com/documentation/articles/vpn-gateway-point-to-site-create/
[azureportal]: http://portal.azure.com
