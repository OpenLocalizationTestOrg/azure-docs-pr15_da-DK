<properties 
   pageTitle="Styre routing og bruge virtuelle anvendelser i ressourcestyring ved hjælp af en skabelon | Microsoft Azure"
   description="Lær at styre routing og bruge virtuelle anvendelser i Azure ressourcestyring ved hjælp af en skabelon"
   services="virtual-network"
   documentationCenter="na"
   authors="jimdial"
   manager="carmonm"
   editor=""
   tags="azure-resource-manager"
/>
<tags  
   ms.service="virtual-network"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="02/23/2016"
   ms.author="jdial" />

#<a name="create-user-defined-routes-udr-in-resource-manager-by-using-a-template"></a>Oprette bruger defineret omdirigerer (UDR) i ressourcestyring ved hjælp af en skabelon

[AZURE.INCLUDE [virtual-network-create-udr-arm-selectors-include.md](../../includes/virtual-network-create-udr-arm-selectors-include.md)]

[AZURE.INCLUDE [virtual-network-create-udr-intro-include.md](../../includes/virtual-network-create-udr-intro-include.md)]

[AZURE.INCLUDE [azure-arm-classic-important-include](../../includes/azure-arm-classic-important-include.md)]Denne artikel omhandler implementeringsmodel ressourcestyring. 

[AZURE.INCLUDE [virtual-network-create-udr-scenario-include.md](../../includes/virtual-network-create-udr-scenario-include.md)]

## <a name="udr-resources-in-a-template-file"></a>UDR ressourcer i en skabelonfil

Du kan se og hente [Eksempel på en skabelon](https://github.com/telmosampaio/azure-templates/tree/master/IaaS-NSG-UDR).

Afsnittet nedenfor viser definitionen af front end UDR i filen **azuredeploy-vnet-nsg-udr.json** , baseret på ovenstående scenarie.

    "apiVersion": "2015-06-15",
    "type": "Microsoft.Network/routeTables",
    "name": "[parameters('frontEndRouteTableName')]",
    "location": "[resourceGroup().location]",
    "tags": {
      "displayName": "UDR - FrontEnd"
    },
    "properties": {
      "routes": [
        {
          "name": "RouteToBackEnd",
          "properties": {
            "addressPrefix": "[parameters('backEndSubnetPrefix')]",
            "nextHopType": "VirtualAppliance",
            "nextHopIpAddress": "[parameters('vmaIpAddress')]"
          }
        }
      ]


Hvis du vil knytte UDR til front end-undernet, skal du ændre undernet definitionen i skabelonen, og brug reference-id'et for UDR.

    "subnets": [
        "name": "[parameters('frontEndSubnetName')]",
        "properties": {
          "addressPrefix": "[parameters('frontEndSubnetPrefix')]",
          "networkSecurityGroup": {
            "id": "[resourceId('Microsoft.Network/networkSecurityGroups', parameters('frontEndNSGName'))]"
          },
          "routeTable": {
              "id": "[resourceId('Microsoft.Network/routeTables', parameters('frontEndRouteTableName'))]"
          }
        },
        ...]

Bemærk de samme udføres til back-end NSG og back-end-undernet i skabelonen.

Du skal også sikre, at **FW1** VM har IP videresendelse egenskaben aktiveret på NIC, der skal bruges til at modtage og videresende pakker. Afsnittet nedenfor viser definitionen af NIC for FW1 i filen azuredeploy-nsg-udr.json, baseret på ovenstående scenarie.

    "apiVersion": "2015-06-15",
    "type": "Microsoft.Network/networkInterfaces",
    "location": "[variables('location')]",
    "tags": {
      "displayName": "NetworkInterfaces - DMZ"
    },
    "name": "[concat(variables('fwVMSettings').nicName, copyindex(1))]",
    "dependsOn": [
      "[concat('Microsoft.Network/publicIPAddresses/', variables('fwVMSettings').pipName, copyindex(1))]",
      "[concat('Microsoft.Resources/deployments/', 'vnetTemplate')]"
    ],
    "properties": {
      "ipConfigurations": [
        {
          "name": "ipconfig1",
          "properties": {
            "enableIPForwarding": true,
            "privateIPAllocationMethod": "Static",
            "privateIPAddress": "[concat('192.168.0.',copyindex(4))]",
            "publicIPAddress": {
              "id": "[resourceId('Microsoft.Network/publicIPAddresses',concat(variables('fwVMSettings').pipName, copyindex(1)))]"
            },
            "subnet": {
              "id": "[variables('dmzSubnetRef')]"
            }
          }
        }
      ]
    },
    "copy": {
      "name": "fwniccount",
      "count": "[parameters('fwCount')]"
    }

## <a name="deploy-the-arm-template-by-using-click-to-deploy"></a>Installere skabelonen ARM ved hjælp af klik til at udrulle

Eksempel skabelonen tilgængelig i den offentlige lager bruger en parameterfil, der indeholder standard værdier, der bruges til at generere dette scenario, der er beskrevet ovenfor. Klik på **Implementer til Azure**for at installere denne skabelon ved hjælp af Klik på Følg [dette link](https://github.com/telmosampaio/azure-templates/tree/master/IaaS-NSG-UDR)for at installere, erstatte Standardparameterværdier, hvis det er nødvendigt, og følg vejledningen i portalen.

## <a name="deploy-the-arm-template-by-using-powershell"></a>Installere skabelonen ARM ved hjælp af PowerShell

Følg nedenstående trin for at installere den ARM skabelon, du har hentet ved hjælp af PowerShell.

[AZURE.INCLUDE [powershell-preview-include.md](../../includes/powershell-preview-include.md)]

1. Hvis du aldrig har brugt Azure PowerShell, se, [hvordan du installere og konfigurere Azure PowerShell](../powershell-install-configure.md) , og følg instruktionerne helt til end for at logge på Azure, og vælg dit abonnement.

2. Køre den `New-AzureRmResourceGroup` til at oprette en ressourcegruppe.

        New-AzureRmResourceGroup -Name TestRG -Location westus

3. Køre den `New-AzureRmResourceGroupDeployment` til at installere skabelonen.

        New-AzureRmResourceGroupDeployment -Name DeployUDR -ResourceGroupName TestRG `
            -TemplateUri https://raw.githubusercontent.com/telmosampaio/azure-templates/master/IaaS-NSG-UDR/azuredeploy.json `
            -TemplateParameterUri https://raw.githubusercontent.com/telmosampaio/azure-templates/master/IaaS-NSG-UDR/azuredeploy.parameters.json            

    Forventet afgang:

        ResourceGroupName : TestRG
        Location          : westus
        ProvisioningState : Succeeded
        Tags              : 
        Permissions       : 
                            Actions  NotActions
                            =======  ==========
                            *                  
                            
        Resources         : 
                            Name                Type                                     Location
                            ==================  =======================================  ========
                            ASFW                Microsoft.Compute/availabilitySets       westus  
                            ASSQL               Microsoft.Compute/availabilitySets       westus  
                            ASWEB               Microsoft.Compute/availabilitySets       westus  
                            FW1                 Microsoft.Compute/virtualMachines        westus  
                            SQL1                Microsoft.Compute/virtualMachines        westus  
                            SQL2                Microsoft.Compute/virtualMachines        westus  
                            WEB1                Microsoft.Compute/virtualMachines        westus  
                            WEB2                Microsoft.Compute/virtualMachines        westus  
                            NICFW1              Microsoft.Network/networkInterfaces      westus  
                            NICSQL1             Microsoft.Network/networkInterfaces      westus  
                            NICSQL2             Microsoft.Network/networkInterfaces      westus  
                            NICWEB1             Microsoft.Network/networkInterfaces      westus  
                            NICWEB2             Microsoft.Network/networkInterfaces      westus  
                            NSG-BackEnd         Microsoft.Network/networkSecurityGroups  westus  
                            NSG-FrontEnd        Microsoft.Network/networkSecurityGroups  westus  
                            PIPFW1              Microsoft.Network/publicIPAddresses      westus  
                            PIPSQL1             Microsoft.Network/publicIPAddresses      westus  
                            PIPSQL2             Microsoft.Network/publicIPAddresses      westus  
                            PIPWEB1             Microsoft.Network/publicIPAddresses      westus  
                            PIPWEB2             Microsoft.Network/publicIPAddresses      westus  
                            UDR-BackEnd         Microsoft.Network/routeTables            westus  
                            UDR-FrontEnd        Microsoft.Network/routeTables            westus  
                            TestVNet            Microsoft.Network/virtualNetworks        westus  
                            testvnetstorageprm  Microsoft.Storage/storageAccounts        westus  
                            testvnetstoragestd  Microsoft.Storage/storageAccounts        westus  
                            
        ResourceId        : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/TestRG

## <a name="deploy-the-arm-template-by-using-the-azure-cli"></a>Installere skabelonen ARM ved hjælp af Azure CLI

Følg nedenstående trin for at installere skabelonen ARM ved hjælp af Azure CLI.

1. Hvis du aldrig har brugt Azure CLI, se [installere og konfigurere Azure CLI](../xplat-cli-install.md) , og følg vejledningen op til det sted, hvor du kan vælge din Azure-konto og abonnement.
2. Køre den `azure config mode` kommandoen til at skifte til Ressourcestyring tilstand, som vist nedenfor.

        azure config mode arm

    Her er den forventede afgang for kommandoen ovenfor:

        info:    New mode is arm

3. I din browser skal du gå til **https://raw.githubusercontent.com/telmosampaio/azure-templates/master/IaaS-NSG-UDR/azuredeploy.parameters.json**, kopiere indholdet af json-filen og sætte ind i en ny fil på computeren. I dette scenarie skal du kopiere værdier under til en fil med navnet **c:\udr\azuredeploy.parameters.json**.

        {
          "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
          "contentVersion": "1.0.0.0",
          "parameters": {
            "fwCount": {
              "value": 1
            },
            "webCount": {
              "value": 2
            },
            "sqlCount": {
              "value": 2
            }
          }
        }

4. Kør cmdlet **oprettelse af azure gruppe** for at installere den nye VNet ved hjælp af skabelonen og parameter filerne, du har downloadet og ændret ovenfor. Listen vises, når output forklares de parametre, bruges.

        azure group create -n TestRG -l westus --template-uri 'https://raw.githubusercontent.com/telmosampaio/azure-templates/master/IaaS-NSG-UDR/azuredeploy.json' -e 'c:\udr\azuredeploy.parameters.json'

    Forventet afgang:

        info:    Executing command group create
        info:    Getting resource group TestRG
        info:    Updating resource group TestRG
        info:    Updated resource group TestRG
        info:    Initializing template configurations and parameters
        info:    Creating a deployment
        info:    Created template deployment "azuredeploy"
        data:    Id:                  /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/TestRG
        data:    Name:                TestRG
        data:    Location:            westus
        data:    Provisioning State:  Succeeded
        data:    Tags: null
        data:    
        info:    group create command OK

5. Kør kommandoen **azure gruppen Vis** for at få vist de ressourcer, der er oprettet i den nye ressourcegruppe. 

        azure group show TestRG

    Forventet resultat
        
        info:    Executing command group show
        info:    Listing resource groups
        info:    Listing resources for the group
        data:    Id:                  /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/TestRG
        data:    Name:                TestRG
        data:    Location:            westus
        data:    Provisioning State:  Succeeded
        data:    Tags: null
        data:    Resources:
        data:    
        data:      Id      : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/TestRG/providers/Microsoft.Compute/availabilitySets/ASFW
        data:      Name    : ASFW
        data:      Type    : availabilitySets
        data:      Location: westus
        data:      Tags    : displayName=AvailabilitySet - DMZ
        data:    
        data:      Id      : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/TestRG/providers/Microsoft.Compute/availabilitySets/ASSQL
        data:      Name    : ASSQL
        data:      Type    : availabilitySets
        data:      Location: westus
        data:      Tags    : displayName=AvailabilitySet - SQL
        data:    
        data:      Id      : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/TestRG/providers/Microsoft.Compute/availabilitySets/ASWEB
        data:      Name    : ASWEB
        data:      Type    : availabilitySets
        data:      Location: westus
        data:      Tags    : displayName=AvailabilitySet - Web
        data:    
        data:      Id      : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/TestRG/providers/Microsoft.Compute/virtualMachines/FW1
        data:      Name    : FW1
        data:      Type    : virtualMachines
        data:      Location: westus
        data:      Tags    : displayName=VMs - DMZ
        data:    
        data:      Id      : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/TestRG/providers/Microsoft.Compute/virtualMachines/SQL1
        data:      Name    : SQL1
        data:      Type    : virtualMachines
        data:      Location: westus
        data:      Tags    : displayName=VMs - SQL
        data:    
        data:      Id      : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/TestRG/providers/Microsoft.Compute/virtualMachines/SQL2
        data:      Name    : SQL2
        data:      Type    : virtualMachines
        data:      Location: westus
        data:      Tags    : displayName=VMs - SQL
        data:    
        data:      Id      : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/TestRG/providers/Microsoft.Compute/virtualMachines/WEB1
        data:      Name    : WEB1
        data:      Type    : virtualMachines
        data:      Location: westus
        data:      Tags    : displayName=VMs - Web
        data:    
        data:      Id      : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/TestRG/providers/Microsoft.Compute/virtualMachines/WEB2
        data:      Name    : WEB2
        data:      Type    : virtualMachines
        data:      Location: westus
        data:      Tags    : displayName=VMs - Web
        data:    
        data:      Id      : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/TestRG/providers/Microsoft.Network/networkInterfaces/NICFW1
        data:      Name    : NICFW1
        data:      Type    : networkInterfaces
        data:      Location: westus
        data:      Tags    : displayName=NetworkInterfaces - DMZ
        data:    
        data:      Id      : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/TestRG/providers/Microsoft.Network/networkInterfaces/NICSQL1
        data:      Name    : NICSQL1
        data:      Type    : networkInterfaces
        data:      Location: westus
        data:      Tags    : displayName=NetworkInterfaces - SQL
        data:    
        data:      Id      : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/TestRG/providers/Microsoft.Network/networkInterfaces/NICSQL2
        data:      Name    : NICSQL2
        data:      Type    : networkInterfaces
        data:      Location: westus
        data:      Tags    : displayName=NetworkInterfaces - SQL
        data:    
        data:      Id      : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/TestRG/providers/Microsoft.Network/networkInterfaces/NICWEB1
        data:      Name    : NICWEB1
        data:      Type    : networkInterfaces
        data:      Location: westus
        data:      Tags    : displayName=NetworkInterfaces - Web
        data:    
        data:      Id      : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/TestRG/providers/Microsoft.Network/networkInterfaces/NICWEB2
        data:      Name    : NICWEB2
        data:      Type    : networkInterfaces
        data:      Location: westus
        data:      Tags    : displayName=NetworkInterfaces - Web
        data:    
        data:      Id      : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/TestRG/providers/Microsoft.Network/networkSecurityGroups/NSG-BackEnd
        data:      Name    : NSG-BackEnd
        data:      Type    : networkSecurityGroups
        data:      Location: westus
        data:      Tags    : displayName=NSG - Front End
        data:    
        data:      Id      : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/TestRG/providers/Microsoft.Network/networkSecurityGroups/NSG-FrontEnd
        data:      Name    : NSG-FrontEnd
        data:      Type    : networkSecurityGroups
        data:      Location: westus
        data:      Tags    : displayName=NSG - Remote Access
        data:    
        data:      Id      : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/TestRG/providers/Microsoft.Network/publicIPAddresses/PIPFW1
        data:      Name    : PIPFW1
        data:      Type    : publicIPAddresses
        data:      Location: westus
        data:      Tags    : displayName=PublicIPAddresses - DMZ
        data:    
        data:      Id      : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/TestRG/providers/Microsoft.Network/publicIPAddresses/PIPSQL1
        data:      Name    : PIPSQL1
        data:      Type    : publicIPAddresses
        data:      Location: westus
        data:      Tags    : displayName=PublicIPAddresses - SQL
        data:    
        data:      Id      : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/TestRG/providers/Microsoft.Network/publicIPAddresses/PIPSQL2
        data:      Name    : PIPSQL2
        data:      Type    : publicIPAddresses
        data:      Location: westus
        data:      Tags    : displayName=PublicIPAddresses - SQL
        data:    
        data:      Id      : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/TestRG/providers/Microsoft.Network/publicIPAddresses/PIPWEB1
        data:      Name    : PIPWEB1
        data:      Type    : publicIPAddresses
        data:      Location: westus
        data:      Tags    : displayName=PublicIPAddresses - Web
        data:    
        data:      Id      : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/TestRG/providers/Microsoft.Network/publicIPAddresses/PIPWEB2
        data:      Name    : PIPWEB2
        data:      Type    : publicIPAddresses
        data:      Location: westus
        data:      Tags    : displayName=PublicIPAddresses - Web
        data:    
        data:      Id      : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/TestRG/providers/Microsoft.Network/routeTables/UDR-BackEnd
        data:      Name    : UDR-BackEnd
        data:      Type    : routeTables
        data:      Location: westus
        data:      Tags    : displayName=Route Table - Back End
        data:    
        data:      Id      : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/TestRG/providers/Microsoft.Network/routeTables/UDR-FrontEnd
        data:      Name    : UDR-FrontEnd
        data:      Type    : routeTables
        data:      Location: westus
        data:      Tags    : displayName=UDR - FrontEnd
        data:    
        data:      Id      : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/TestRG/providers/Microsoft.Network/virtualNetworks/TestVNet
        data:      Name    : TestVNet
        data:      Type    : virtualNetworks
        data:      Location: westus
        data:      Tags    : displayName=VNet
        data:    
        data:      Id      : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/TestRG/providers/Microsoft.Storage/storageAccounts/testvnetstorageprm
        data:      Name    : testvnetstorageprm
        data:      Type    : storageAccounts
        data:      Location: westus
        data:      Tags    : displayName=Storage Account - Premium
        data:    
        data:      Id      : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/TestRG/providers/Microsoft.Storage/storageAccounts/testvnetstoragestd
        data:      Name    : testvnetstoragestd
        data:      Type    : storageAccounts
        data:      Location: westus
        data:      Tags    : displayName=Storage Account - Simple
        data:    
        data:    Permissions:
        data:      Actions: *
        data:      NotActions: 
        data:    
        info:    group show command OK

>[AZURE.TIP] Hvis du ikke kan se alle ressourcerne, du kører den `azure group deployment show` kommando for at sikre, at tilstanden klargøring af installationen er *lykkedes*.