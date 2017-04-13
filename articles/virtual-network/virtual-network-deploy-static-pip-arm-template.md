<properties
   pageTitle="Installere en VM med en statiske offentlige IP-Adresser ved hjælp af en skabelon i ressourcestyring | Microsoft Azure"
   description="Lær, hvordan du installerer FOS med en statiske offentlige IP-Adresser ved hjælp af en skabelon i ressourcestyring."
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
   ms.date="04/27/2016"
   ms.author="jdial" />

# <a name="deploy-a-vm-with-a-static-public-ip-using-a-template"></a>Installere en VM med en statiske offentlige IP-Adresser ved hjælp af en skabelon

[AZURE.INCLUDE [virtual-network-deploy-static-pip-arm-selectors-include.md](../../includes/virtual-network-deploy-static-pip-arm-selectors-include.md)]

[AZURE.INCLUDE [virtual-network-deploy-static-pip-intro-include.md](../../includes/virtual-network-deploy-static-pip-intro-include.md)]

[AZURE.INCLUDE [azure-arm-classic-important-include](../../includes/learn-about-deployment-models-rm-include.md)]Klassisk implementeringsmodel.

[AZURE.INCLUDE [virtual-network-deploy-static-pip-scenario-include.md](../../includes/virtual-network-deploy-static-pip-scenario-include.md)]

## <a name="public-ip-resources-in-a-template-file"></a>Offentlige IP-ressourcer i en skabelonfil

Du kan se og hente [Eksempel på en skabelon](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/IaaS-Story/03-Static-public-IP/azuredeploy.json).

Afsnittet nedenfor viser definitionen af den offentlige IP-ressource, baseret på ovenstående scenarie.

      {
        "apiVersion": "2015-06-15",
        "type": "Microsoft.Network/publicIPAddresses",
        "name": "[variables('webVMSetting').pipName]",
        "location": "[variables('location')]",
        "properties": {
          "publicIPAllocationMethod": "Static"
        },
        "tags": {
          "displayName": "PublicIPAddress - Web"
        }
      },

Bemærk egenskaben **publicIPAllocationMethod** , som er angivet til *statisk*. Denne egenskab kan være enten *dynamisk* (standardværdien) eller *statiske*. Konfigurer den til statisk garantier, som den offentlige IP-adresse, der er tildelt aldrig vil ændre.

Afsnittet nedenfor viser tilknytningen af den offentlige IP-adresse til en netværksgrænseflade.

      {
        "apiVersion": "2015-06-15",
        "type": "Microsoft.Network/networkInterfaces",
        "name": "[variables('webVMSetting').nicName]",
        "location": "[variables('location')]",
        "tags": {
          "displayName": "NetworkInterface - Web"
        },
        "dependsOn": [
          "[concat('Microsoft.Network/publicIPAddresses/', variables('webVMSetting').pipName)]",
          "[concat('Microsoft.Network/virtualNetworks/', parameters('vnetName'))]"
        ],
        "properties": {
          "ipConfigurations": [
            {
              "name": "ipconfig1",
              "properties": {
                "privateIPAllocationMethod": "Static",
                "privateIPAddress": "[variables('webVMSetting').ipAddress]",
                "publicIPAddress": {
                  "id": "[resourceId('Microsoft.Network/publicIPAddresses',variables('webVMSetting').pipName)]"
                },
                "subnet": {
                  "id": "[variables('frontEndSubnetRef')]"
                }
              }
            }
          ]
        }
      },

Bemærk egenskaben **publicIPAddress** , der peger på en ressource med navnet **variables('webVMSetting').pipName** **Id** . Det er navnet på den offentlige IP-ressource, der vises ovenfor.

Til sidst skal er netværksgrænsefladen angivet i egenskaben **networkProfile** VM der oprettes.

      "networkProfile": {
        "networkInterfaces": [
          {
            "id": "[resourceId('Microsoft.Network/networkInterfaces', variables('webVMSetting').nicName)]"
          }
        ]
      }

## <a name="deploy-the-template-by-using-click-to-deploy"></a>Installere skabelonen ved hjælp af klik til at udrulle

Eksempel skabelonen tilgængelig i den offentlige lager bruger en parameterfil, der indeholder standard værdier, der bruges til at generere dette scenario, der er beskrevet ovenfor. Klik på **Implementer til Azure** i filen Readme.md til [VM med statisk PIP](https://github.com/Azure/azure-quickstart-templates/tree/master/IaaS-Story/03-Static-public-IP) skabelonen for at installere denne skabelon, der bruger Klik til at implementere. Erstatte Standardparameterværdier, hvis det er nødvendigt, og angiv værdier for de tomme parametre.  Følg vejledningen i portalen for at oprette en virtuel maskine med en statisk offentlige IP-adresse.

## <a name="deploy-the-template-by-using-powershell"></a>Installere skabelonen ved hjælp af PowerShell

Følg nedenstående trin for at installere den skabelon, du har hentet ved hjælp af PowerShell.

1. Hvis du aldrig har brugt Azure PowerShell, se, [hvordan du installere og konfigurere Azure PowerShell](../powershell-install-configure.md) og følge instruktionerne i trin 1 til 3.

2. Køre **Ny AzureRmResourceGroup** cmdlet for at oprette en ny ressourcegruppe, hvis det er nødvendigt i en PowerShell-konsollen. Hvis du allerede har en ressourcegruppe, der er oprettet, gå til trin 3.

        New-AzureRmResourceGroup -Name PIPTEST -Location westus

    Forventet afgang:

        ResourceGroupName : PIPTEST
        Location          : westus
        ProvisioningState : Succeeded
        Tags              :
        ResourceId        : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/StaticPublicIP

3. Køre **Ny AzureRmResourceGroupDeployment** cmdlet for at installere skabelonen, i en PowerShell-konsollen.

        New-AzureRmResourceGroupDeployment -Name DeployVM -ResourceGroupName PIPTEST `
            -TemplateUri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/IaaS-Story/03-Static-public-IP/azuredeploy.json `
            -TemplateParameterUri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/IaaS-Story/03-Static-public-IP/azuredeploy.parameters.json

    Forventet afgang:

        DeploymentName    : DeployVM
        ResourceGroupName : PIPTEST
        ProvisioningState : Succeeded
        Timestamp         : <Deployment date> <Deployment time>
        Mode              : Incremental
        TemplateLink      :
                            Uri            : https://raw.githubusercontent.com/Azure/azure-quickstart-templates/mas
                            ter/IaaS-Story/03-Static-public-IP/azuredeploy.json
                            ContentVersion : 1.0.0.0

        Parameters        :
                            Name                      Type                       Value     
                            ========================  =========================  ==========
                            vnetName                  String                     WTestVNet
                            vnetPrefix                String                     192.168.0.0/16
                            frontEndSubnetName        String                     FrontEnd  
                            frontEndSubnetPrefix      String                     192.168.1.0/24
                            storageAccountNamePrefix  String                     iaasestd  
                            stdStorageType            String                     Standard_LRS
                            osType                    String                     Windows   
                            adminUsername             String                     adminUser
                            adminPassword             SecureString                         

        Outputs           :

## <a name="deploy-the-template-by-using-the-azure-cli"></a>Installere skabelonen ved hjælp af Azure CLI

Følg nedenstående trin for at installere skabelonen ved hjælp af Azure CLI.

1. Hvis du aldrig har brugt Azure CLI, skal du følge trinnene i artiklen [installere og konfigurere Azure CLI](../xplat-cli-install.md) og derefter disse trin for at oprette forbindelse på CLI til dit abonnement i afsnittet "Brug azure-logon til at godkende interaktivt" i artiklen [Opret forbindelse til et Azure-abonnement fra Azure kommandolinjen (Azure CLI)](../xplat-cli-connect.md) .
2. Kør kommandoen **azure config tilstand** for at skifte til Ressourcestyring tilstand, som vist nedenfor.

        azure config mode arm

    Her er den forventede afgang for kommandoen ovenfor:

        info:    New mode is arm

3. Åbne [parameterfilen](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/IaaS-Story/03-Static-public-IP/azuredeploy.parameters.json), vælg dens indhold og gemme den til en fil på computeren. I dette eksempel skal gemmes parametrene i en fil med navnet *parameters.json*. Ændre parameterværdier i filen, hvis du ønsker, men som minimum, anbefales det, at du ændrer værdien for parameteren adminPassword til en entydig, kompleks adgangskode.

4. Kør cmdlet **azure gruppe installation oprette** for at installere den nye VNet ved hjælp af skabelonen og parameter filerne, du har downloadet og ændret ovenfor. Erstat teksten i kommandoen nedenfor, <path> med stien du gemte filen til. 

        azure group create -n PIPTEST2 -l westus --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/IaaS-Story/03-Static-public-IP/azuredeploy.json -e <path>\parameters.json

    Forventet afgang (liste parameterværdier bruges):

        info:    Executing command group create
        + Getting resource group PIPTEST2
        + Creating resource group PIPTEST2
        info:    Created resource group PIPTEST2
        + Initializing template configurations and parameters
        + Creating a deployment
        info:    Created template deployment "azuredeploy"
        data:    Id:                  /subscriptions/<Subscription ID>/resourceGroups/PIPTEST2
        data:    Name:                PIPTEST2
        data:    Location:            westus
        data:    Provisioning State:  Succeeded
        data:    Tags: null
        data:
        info:    group create command OK
