<properties
   pageTitle="Installere flere NIC FOS ved hjælp af en skabelon i ressourcestyring | Microsoft Azure"
   description="Lær, hvordan du kan installere flere NIC FOS ved hjælp af en skabelon i ressourcestyring."
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
   ms.date="02/02/2016"
   ms.author="jdial" />

# <a name="deploy-multi-nic-vms-using-a-template"></a>Installere flere NIC FOS ved hjælp af en skabelon

[AZURE.INCLUDE [virtual-network-deploy-multinic-arm-selectors-include.md](../../includes/virtual-network-deploy-multinic-arm-selectors-include.md)]

[AZURE.INCLUDE [virtual-network-deploy-multinic-intro-include.md](../../includes/virtual-network-deploy-multinic-intro-include.md)]

[AZURE.INCLUDE [azure-arm-classic-important-include](../../includes/learn-about-deployment-models-rm-include.md)][Klassisk implementeringsmodel](virtual-network-deploy-multinic-classic-ps.md).

[AZURE.INCLUDE [virtual-network-deploy-multinic-scenario-include.md](../../includes/virtual-network-deploy-multinic-scenario-include.md)]

Da du ikke har FOS med et enkelt NIC og FOS med flere netværkskort i samme ressourcegruppe på dette tidspunkt, implementerer du back-end-servere i en ressourcegruppe, og alle andre komponenter i en anden sikkerhedsgruppe. Nedenstående fremgangsmåde bruger en ressourcegruppe navngivne *IaaSStory* for den primære ressourcegruppe og *Back-end IaaSStory* back-end-servere.

## <a name="prerequisites"></a>Forudsætninger

Før du kan installere back-end-servere, skal du installere den primære ressourcegruppe med alle de nødvendige ressourcer til dette scenario. Følg nedenstående trin for at installere disse ressourcer.

1. Gå til [skabelonsiden](https://github.com/Azure/azure-quickstart-templates/tree/master/IaaS-Story/11-MultiNIC).
2. Klik på **Implementer til Azure**på siden skabelon til højre for **overordnede ressourcegruppe**.
3. Hvis det er nødvendigt, ændre parameterværdierne og derefter følge trinnene i portalen Azure preview til at udrulle ressourcegruppen.

> [AZURE.IMPORTANT] Sørg for, at din lagerplads kontonavne er entydige. Du kan ikke have dublerede lagerplads kontonavne i Azure.

## <a name="understand-the-deployment-template"></a>Forstå skabelonen til distribution

Før du installerer den skabelon, der leveres med denne dokumentation, skal du vide, hvad gør den. Følgende fremgangsmåde giver en god oversigt over den pågældende skabelon.

1. Gå til [skabelonsiden](https://github.com/Azure/azure-quickstart-templates/tree/master/IaaS-Story/11-MultiNIC).
2. Klik på **azuredeploy.json** for at åbne skabelonfilen.
3. Bemærk parameteren *osType* nedenfor. Denne parameter bruges til at vælge hvilke VM billede skal bruges til databaseserveren, sammen med flere operativsystem relaterede indstillinger.

        "osType": {
          "type": "string",
          "defaultValue": "Windows",
          "allowedValues": [
            "Windows",
            "Ubuntu"
          ],
          "metadata": {
            "description": "Type of OS to use for VMs: Windows or Ubuntu."
          }
        },

4. Rul ned til listen over variabler, og Markér definitionen for **dbVMSetting** variablerne, vises nedenfor. Den modtager en af de matrix elementer, der er indeholdt i variablen **dbVMSettings** . Hvis du er bekendt med software development terminologi, kan du få vist variablen **dbVMSettings** som en hashtabel eller en dictionay.

        "dbVMSetting": "[variables('dbVMSettings')[parameters('osType')]]"

5. Antag, at du beslutter dig for at installere Windows VM'er, der kører SQL i back-end. Derefter værdien for **osType** ville være *Windows*, og variablen **dbVMSetting** skal indeholde det element, der vises nedenfor, der repræsenterer den første værdi i variablen **dbVMSettings** .

          "Windows": {
            "vmSize": "Standard_DS3",
            "publisher": "MicrosoftSQLServer",
            "offer": "SQL2014SP1-WS2012R2",
            "sku": "Standard",
            "version": "latest",
            "vmName": "DB",
            "osdisk": "osdiskdb",
            "datadisk": "datadiskdb",
            "nicName": "NICDB",
            "ipAddress": "192.168.2.",
            "extensionDeployment": "",
            "avsetName": "ASDB",
            "remotePort": 3389,
            "dbPort": 1433
          },

6. Bemærk **vmSize** indeholder værdien *Standard_DS3*. Visse VM størrelser Tillad brug af flere netværkskort. Du kan kontrollere, hvilke VM størrelser er flere aktiveret ved at besøge [flere NIC oversigt](virtual-networks-multiple-nics.md).
7. Rul ned til **ressourcer** , og Bemærk det første element. Den beskriver en lagerplads konto. Denne lagerplads konto bruges til at vedligeholde data diskene bruges af hver database VM. I dette scenarie skal har hver database VM en OS disk, der er gemt på almindelig lager og to datadisce, der er gemt i SSD (premium) lagerplads.

        {
          "apiVersion": "2015-05-01-preview",
          "type": "Microsoft.Storage/storageAccounts",
          "name": "[parameters('prmStorageName')]",
          "location": "[variables('location')]",
          "tags": {
            "displayName": "Storage Account - Premium"
          },
          "properties": {
            "accountType": "[parameters('prmStorageType')]"
          }
        },

8. Rul ned til den næste ressource, som angivet nedenfor. Denne ressource repræsenterer NIC bruges til access database i hver database VM. Bemærk brugen af funktionen **kopi** i denne ressource. Skabelonen kan du installere så mange FOS, som du vil have, ud fra parameteren **dbCount** . Derfor skal du oprette den samme mængde netværkskort database adgang, en for hver VM.

        {
          "apiVersion": "2015-06-15",
          "type": "Microsoft.Network/networkInterfaces",
          "name": "[concat(variables('dbVMSetting').nicName,'-DA-', copyindex(1))]",
          "location": "[variables('location')]",
          "tags": {
            "displayName": "NetworkInterfaces - DB DA"
          },
          "copy": {
            "name": "dbniccount",
            "count": "[parameters('dbCount')]"
          },
          "properties": {
            "ipConfigurations": [
              {
                "name": "ipconfig1",
                "properties": {
                  "privateIPAllocationMethod": "Static",
                  "privateIPAddress": "[concat(variables('dbVMSetting').ipAddress,copyindex(4))]",
                  "subnet": {
                    "id": "[variables('backEndSubnetRef')]"
                  }
                }
              }
            ]
          }
        },

9. Rul ned til den næste ressource, som angivet nedenfor. Denne ressource repræsenterer NIC bruges til administration af hver database VM. Igen skal bruge du en af disse netværkskort for hver database VM. Bemærk elementet **networkSecurityGroup** , sammenkæde en NSG, der giver adgang til RDP/SSH til denne NIC kun.

        {
          "apiVersion": "2015-06-15",
          "type": "Microsoft.Network/networkInterfaces",
          "name": "[concat(variables('dbVMSetting').nicName, '-RA-',copyindex(1))]",
          "location": "[variables('location')]",
          "tags": {
            "displayName": "NetworkInterfaces - DB RA"
          },
          "copy": {
            "name": "dbniccount",
            "count": "[parameters('dbCount')]"
          },
          "properties": {
            "ipConfigurations": [
              {
                "name": "ipconfig1",
                "properties": {
                  "networkSecurityGroup": {
                    "id": "[resourceId('Microsoft.Network/networkSecurityGroups', parameters('remoteAccessNSGName'))]"
                  },
                  "privateIPAllocationMethod": "Static",
                  "privateIPAddress": "[concat(variables('dbVMSetting').ipAddress,copyindex(54))]",
                  "subnet": {
                    "id": "[variables('backEndSubnetRef')]"
                  }
                }
              }
            ]
          }
        },

10. Rul ned til den næste ressource, som angivet nedenfor. Denne ressource repræsenterer en angive skal deles med alle database FOS tilgængelighed. På den måde, du sikker på, at der altid bliver én VM i dialogboksen Angiv kører under vedligeholdelse af.

        {
          "apiVersion": "2015-06-15",
          "type": "Microsoft.Compute/availabilitySets",
          "name": "[variables('dbVMSetting').avsetName]",
          "location": "[variables('location')]",
          "tags": {
            "displayName": "AvailabilitySet - DB"
          }
        },

11. Rul ned til den næste ressource. Denne ressource repræsenterer database FOS, som det ses i første par linjer, der vises nedenfor. Bemærk brugen af funktionen **Kopiér** igen, at sikre, at flere FOS er oprettet ud fra parameteren **dbCount** . Bemærk også samlingen **dependsOn** . Det viser to netværkskort, der skal oprettes, før VM installeres sammen med sættet tilgængelighed og kontoen lagerplads.

          "apiVersion": "2015-06-15",
          "type": "Microsoft.Compute/virtualMachines",
          "name": "[concat(variables('dbVMSetting').vmName,copyindex(1))]",
          "location": "[variables('location')]",
          "dependsOn": [
            "[concat('Microsoft.Network/networkInterfaces/', variables('dbVMSetting').nicName,'-DA-', copyindex(1))]",
            "[concat('Microsoft.Network/networkInterfaces/', variables('dbVMSetting').nicName,'-RA-', copyindex(1))]",
            "[concat('Microsoft.Compute/availabilitySets/', variables('dbVMSetting').avsetName)]",
            "[concat('Microsoft.Storage/storageAccounts/', parameters('prmStorageName'))]"
          ],
          "tags": {
            "displayName": "VMs - DB"
          },
          "copy": {
            "name": "dbvmcount",
            "count": "[parameters('dbCount')]"
          },

12. Rul ned i elementet **networkProfile** VM ressourcen, som angivet nedenfor. Bemærk, at der er to netværkskort der reference til hver VM. Når du opretter flere netværkskort til en VM, skal du angive den **primære** egenskab for en af netværkskortene til *Sand*, og resten til *Falsk*.

        "networkProfile": {
          "networkInterfaces": [
            {
              "id": "[resourceId('Microsoft.Network/networkInterfaces', concat(variables('dbVMSetting').nicName,'-DA-',copyindex(1)))]",
              "properties": { "primary": true }
            },
            {
              "id": "[resourceId('Microsoft.Network/networkInterfaces', concat(variables('dbVMSetting').nicName,'-RA-',copyindex(1)))]",
              "properties": { "primary": false }
            }
          ]
        }
      }

## <a name="deploy-the-arm-template-by-using-click-to-deploy"></a>Installere skabelonen ARM ved hjælp af klik til at udrulle

> [AZURE.IMPORTANT] Sørg for, at du skal du benytte følgende [forudsætninger](#Pre-requisites) før du følger vejledningen nedenfor.

Eksempel skabelonen tilgængelig i den offentlige lager bruger en parameterfil, der indeholder standard værdier, der bruges til at generere dette scenario, der er beskrevet ovenfor. Du skal installere denne skabelon ved hjælp af Klik på Følg [dette link](https://github.com/Azure/azure-quickstart-templates/tree/master/IaaS-Story/11-MultiNIC)for at installere, til højre for **back end-ressourcegruppe (se dokumentationen)** Klik **installeres til Azure**, erstatte Standardparameterværdier, hvis det er nødvendigt, og følg vejledningen i portalen.

I nedenstående figur vises indholdet af den nye ressourcegruppe efter installation.

![Back-end ressourcegruppe](./media/virtual-network-deploy-multinic-arm-template/Figure2.png)

## <a name="deploy-the-template-by-using-powershell"></a>Installere skabelonen ved hjælp af PowerShell

Følg nedenstående trin for at installere den skabelon, du har hentet ved hjælp af PowerShell.

[AZURE.INCLUDE [powershell-preview-include.md](../../includes/powershell-preview-include.md)]

3. Køre den **`New-AzureRmResourceGroup`** til at oprette en ressourcegruppe ved hjælp af skabelonen.

        New-AzureRmResourceGroup -Name IaaSStory-Backend -Location uswest `
            -TemplateFile 'https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/IaaS-Story/11-MultiNIC/azuredeploy.json' `
            -TemplateParameterFile 'https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/IaaS-Story/11-MultiNIC/azuredeploy.parameters.json'

    Forventet afgang:

        ResourceGroupName : IaaSStory-Backend
        Location          : westus
        ProvisioningState : Succeeded
        Tags              :
        Permissions       :
                            Actions  NotActions
                            =======  ==========
                            *                  

        Resources         :
                            Name                 Type                                 Location
                            ===================  ===================================  ========
                            ASDB                 Microsoft.Compute/availabilitySets   westus  
                            DB1                  Microsoft.Compute/virtualMachines    westus  
                            DB2                  Microsoft.Compute/virtualMachines    westus  
                            NICDB-DA-1           Microsoft.Network/networkInterfaces  westus  
                            NICDB-DA-2           Microsoft.Network/networkInterfaces  westus  
                            NICDB-RA-1           Microsoft.Network/networkInterfaces  westus  
                            NICDB-RA-2           Microsoft.Network/networkInterfaces  westus  
                            wtestvnetstorageprm  Microsoft.Storage/storageAccounts    westus  

        ResourceId        : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/IaaSStory-Backend

## <a name="deploy-the-template-by-using-the-azure-cli"></a>Installere skabelonen ved hjælp af Azure CLI

Følg nedenstående trin for at installere skabelonen ved hjælp af Azure CLI.

1. Hvis du aldrig har brugt Azure CLI, se [installere og konfigurere Azure CLI](../xplat-cli-install.md) , og følg vejledningen op til det sted, hvor du kan vælge din Azure-konto og abonnement.
2. Køre den **`azure config mode`** kommandoen til at skifte til Ressourcestyring tilstand, som vist nedenfor.

        azure config mode arm

    Her er den forventede afgang for kommandoen ovenfor:

        info:    New mode is arm

3. Åbne [parameterfilen](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/IaaS-Story/11-MultiNIC/azuredeploy.parameters.json), vælg dens indhold og gemme den til en fil på computeren. I dette eksempel skal vi har gemt parameterfilen til *parameters.json*.

4. Køre den **`azure group deployment create`** til at installere den nye VNet ved hjælp af skabelonen og parameter-filer, du har downloadet og ændret ovenfor. Listen vises, når output forklares de parametre, bruges.

        azure group create -n IaaSStory-Backend -l westus --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/IaaS-Story/11-MultiNIC/azuredeploy.json -e parameters.json

    Forventet afgang:

        info:    Executing command group create
        + Getting resource group IaaSStory-Backend
        + Creating resource group IaaSStory-Backend
        info:    Created resource group IaaSStory-Backend
        + Initializing template configurations and parameters
        + Creating a deployment
        info:    Created template deployment "azuredeploy"
        data:    Id:                  /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/IaaSStory-Backend
        data:    Name:                IaaSStory-Backend
        data:    Location:            westus
        data:    Provisioning State:  Succeeded
        data:    Tags: null
        data:
        info:    group create command OK
