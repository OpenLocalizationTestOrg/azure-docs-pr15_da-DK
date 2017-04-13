<properties
    pageTitle="Oprette en VM med en skabelon for ressourcestyring | Microsoft Azure"
    description="Brug en ressourcestyring skabelon og et PowerShell til nemt for at oprette en ny Windows virtuel maskine."
    services="virtual-machines-windows"
    documentationCenter=""
    authors="davidmu1"
    manager="timlt"
    editor=""
    tags="azure-resource-manager"/>

<tags
    ms.service="virtual-machines-windows"
    ms.workload="na"
    ms.tgt_pltfrm="vm-windows"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/06/2016"
    ms.author="davidmu"/>

# <a name="create-a-windows-virtual-machine-with-a-resource-manager-template"></a>Oprette en Windows virtuel maskine med en skabelon for ressourcestyring

I denne artikel introducerer dig til en skabelon til Azure ressourcestyring og viser, hvordan du bruge PowerShell til at installere den. Skabelonen installerer en enkelt virtuel maskine, der kører Windows Server i et nyt virtuelt netværk med et enkelt undernet.

Det skal tage 20 minutter om ved for at udføre trinnene i denne artikel.

> [AZURE.IMPORTANT] Hvis du vil din VM skal være en del af en tilgængelighed er defineret, skal du føje det til sættet, når du opretter VM. Der i øjeblikket ikke er en måde at tilføje en VM til en tilgængelig Angiv, når den er blevet oprettet.

## <a name="step-1-create-the-template-file"></a>Trin 1: Oprette skabelonfilen

Du kan oprette din egen skabelon ved hjælp af oplysningerne i [redigering Azure ressourcestyring skabeloner](../resource-group-authoring-templates.md). Du kan også distribuere skabeloner, der er oprettet for dig fra [Azure Hurtig start-guider skabeloner](https://azure.microsoft.com/documentation/templates/).

1. Åbn din foretrukne teksteditor og Tilføj påkrævede skemaelementet, sammen med elementet påkrævet contentVersion:

        {
          "$schema": "http://schema.management.azure.com/schemas/2014-04-01-preview/deploymentTemplate.json#",
          "contentVersion": "1.0.0.0",
        }
2. [Parametre](../resource-group-authoring-templates.md#parameters) er ikke altid påkrævet, men de er en måde at skrive værdier, når skabelonen, der er installeret. Du kan tilføje elementet parametre og de underordnede elementer efter elementet contentVersion:

        {
          "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json",
          "contentVersion": "1.0.0.0",
          "parameters": {
            "adminUserName": { "type": "string" },
            "adminPassword": { "type": "securestring" }
          },
        }

3. [Variabler](../resource-group-authoring-templates.md#variables) kan bruges i en skabelon til at angive værdier, der kan blive ændret ofte eller værdier, der skal oprettes en kombination af parameterværdier. Du kan tilføje elementet variabler efter sektionen parametre:

        {
          "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json",
          "contentVersion": "1.0.0.0",
          "parameters": {
            "adminUsername": { "type": "string" },
            "adminPassword": { "type": "securestring" }
          },
          "variables": {
            "vnetID":"[resourceId('Microsoft.Network/virtualNetworks','myvn1')]",
            "subnetRef": "[concat(variables('vnetID'),'/subnets/mysn1')]"  
          },
        }
        
4. [Ressourcer](../resource-group-authoring-templates.md#resources) som den virtuelle maskine, det virtuelle netværk og kontoen lagerplads defineres næste i skabelonen. Du kan tilføje afsnittet ressourcer efter sektionen variabler:

        {
          "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json",
          "contentVersion": "1.0.0.0",
          "parameters": {
            "adminUsername": { "type": "string" },
            "adminPassword": { "type": "securestring" }
          },
          "variables": {
            "vnetID":"[resourceId('Microsoft.Network/virtualNetworks','myvn1')]",
            "subnetRef": "[concat(variables('vnetID'),'/subnets/mysn1')]"
          },
          "resources": [
            {
              "type": "Microsoft.Storage/storageAccounts",
              "name": "mystorage1",
              "apiVersion": "2015-06-15",
              "location": "[resourceGroup().location]",
              "properties": { "accountType": "Standard_LRS" }
            },
            {
              "apiVersion": "2016-03-30",
              "type": "Microsoft.Network/publicIPAddresses",
              "name": "myip1",
              "location": "[resourceGroup().location]",
              "properties": {
                "publicIPAllocationMethod": "Dynamic",
                "dnsSettings": { "domainNameLabel": "mydns1" }
              }
            },
            {
              "apiVersion": "2016-03-30",
              "type": "Microsoft.Network/virtualNetworks",
              "name": "myvn1",
              "location": "[resourceGroup().location]",
              "properties": {
                "addressSpace": { "addressPrefixes": [ "10.0.0.0/16" ] },
                "subnets": [ {
                  "name": "mysn1",
                  "properties": { "addressPrefix": "10.0.0.0/24" }
                } ]
              }
            },
            {
              "apiVersion": "2016-03-30",
              "type": "Microsoft.Network/networkInterfaces",
              "name": "mync1",
              "location": "[resourceGroup().location]",
              "dependsOn": [
                "Microsoft.Network/publicIPAddresses/myip1",
                "Microsoft.Network/virtualNetworks/myvn1"
              ],
              "properties": {
                "ipConfigurations": [ {
                  "name": "ipconfig1",
                  "properties": {
                    "privateIPAllocationMethod": "Dynamic",
                    "publicIPAddress": {
                      "id": "[resourceId('Microsoft.Network/publicIPAddresses', 'myip1')]"
                    },
                    "subnet": { "id": "[variables('subnetRef')]" }
                  }
                } ]
              }
            },
            {
              "apiVersion": "2016-03-30",
              "type": "Microsoft.Compute/virtualMachines",
              "name": "myvm1",
              "location": "[resourceGroup().location]",
              "dependsOn": [
                "Microsoft.Network/networkInterfaces/mync1",
                "Microsoft.Storage/storageAccounts/mystorage1"
              ],
              "properties": {
                "hardwareProfile": { "vmSize": "Standard_A1" },
                "osProfile": {
                  "computerName": "myvm1",
                  "adminUsername": "[parameters('adminUsername')]",
                  "adminPassword": "[parameters('adminPassword')]"
                },
                "storageProfile": {
                  "imageReference": {
                    "publisher": "MicrosoftWindowsServer",
                    "offer": "WindowsServer",
                    "sku": "2012-R2-Datacenter",
                    "version" : "latest"
                  },
                  "osDisk": {
                    "name": "myosdisk1",
                    "vhd": {
                      "uri": "https://mystorage1.blob.core.windows.net/vhds/myosdisk1.vhd"
                    },
                    "caching": "ReadWrite",
                    "createOption": "FromImage"
                  }
                },
                "networkProfile": {
                  "networkInterfaces" : [ {
                    "id": "[resourceId('Microsoft.Network/networkInterfaces','mync1')]"
                  } ]
                }
              }
            } ]
          }
          
    >[AZURE.NOTE] I denne artikel opretter en virtuel maskine, der kører en version af Windows Server-operativsystemet. Hvis du vil vide mere om at vælge andre billeder, skal du se [Naviger og vælge Azure virtuelt billeder med Windows PowerShell og Azure CLI](virtual-machines-linux-cli-ps-findimage.md).  
            
2. Gem skabelonfilen som *VirtualMachineTemplate.json*.

## <a name="step-2-create-the-parameters-file"></a>Trin 2: Oprette Parameterfilen

For at angive værdier for ressourceparametrene, der er defineret i skabelonen, skal du oprette en parametre-fil, der indeholder de værdier, der bruges, når skabelonen, der er installeret.

1. Kopiér denne JSON-indhold til en ny fil kaldet *Parameters.json*i tekstredigeringsprogrammet:

        {
          "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json",
          "contentVersion": "1.0.0.0",
          "parameters": {
            "adminUserName": { "value": "mytestacct1" },
            "adminPassword": { "value": "mytestpass1" }
          }
        }

    >[AZURE.NOTE] Se mere om [kravene til brugernavn og din adgangskode](virtual-machines-windows-faq.md#what-are-the-username-requirements-when-creating-a-vm).

2. Gem parameterfilen.

## <a name="step-3-install-azure-powershell"></a>Trin 3: Installer Azure PowerShell

Se, [hvordan du installerer og konfigurerer Azure PowerShell](../powershell-install-configure.md) for at få oplysninger om at installere den nyeste version af Azure PowerShell, vælge dit abonnement og logge på din konto.

## <a name="step-4-create-a-resource-group"></a>Trin 4: Oprette en ressourcegruppe

Alle de ressourcer skal være installeret i en [ressourcegruppe](../azure-resource-manager/resource-group-overview.md).

1. Få en liste over tilgængelige placeringer, hvor ressourcer, der kan oprettes.

        Get-AzureRmLocation | sort DisplayName | Select DisplayName

2. Erstat værdien af **$locName** med en placering på listen, for eksempel **Centrale USA**. Oprette variablen.

        $locName = "location name"
        
3. Erstat værdien af **$rgName** med navnet på den nye ressourcegruppe. Oprette variablen og ressourcegruppen.

        $rgName = "resource group name"
        New-AzureRmResourceGroup -Name $rgName -Location $locName
        
    Du burde se noget i retning af dette eksempel:
    
        ResourceGroupName : myrg1
        Location          : centralus
        ProvisioningState : Succeeded
        Tags              :
        ResourceId        : /subscriptions/{subscription-id}/resourceGroups/myrg1

## <a name="step-5-create-the-resources-with-the-template-and-parameters"></a>Trin 5: Oprettes ressourcerne med skabelon og parametre

Erstat værdien af **$templateFile** med stien og navnet på skabelonfilen. Erstat værdien af **$parameterFile** med stien og navnet på parameterfilen. Oprette variablerne, og derefter installere skabelonen. 

        $templateFile = "template file"
        $parameterFile = "parameter file"
        New-AzureRmResourceGroupDeployment -ResourceGroupName $rgName -TemplateFile $templateFile -TemplateParameterFile $parameterFile

    You will see something like this:

        DeploymentName    : VirtualMachineTemplate
        ResourceGroupName : myrg1
        ProvisioningState : Succeeded
        Timestamp         : 4/14/2016 8:11:37 PM
        Mode              : Incremental
        TemplateLink      :
        Parameters        :
                            Name             Type                       Value
                            ===============  =========================  ==========
                            adminUsername    String                     mytestacct1
                            adminPassword    SecureString

        Outputs           :

>[AZURE.NOTE] Du kan også distribuere skabeloner og parametre fra en Azure-lager-konto. For at få mere for at vide, skal du se [Bruge Azure PowerShell med Azure-lager](../storage/storage-powershell-guide-full.md).

## <a name="next-steps"></a>Næste trin

- Hvis der var problemer med installationen, der ville være et næste trin at kigge på [fejlfinding ressource gruppe-installationer med Azure-portalen](../resource-manager-troubleshoot-deployments-portal.md)
- Få mere at vide, hvordan du administrerer den virtuelle maskine, du har oprettet ved at gennemgå [Administrer virtuelle maskiner ved hjælp af Azure ressourcestyring og PowerShell](virtual-machines-windows-ps-manage.md).
