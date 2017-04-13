<properties
    pageTitle="Oprette forbindelse Azure virtuelle maskiner til Log Analytics | Microsoft Azure"
    description="For Windows og Linux virtuelle maskiner, der kører i Azure, er den anbefalede måde af indsamlede logfiler og målepunkter ved at installere filtypenavnet Log Analytics Azure VM. Du kan bruge Azure portal eller PowerShell til at installere filtypenavnet Log Analytics virtuelt til Azure FOS."
    services="log-analytics"
    documentationCenter=""
    authors="richrundmsft"
    manager="jochan"
    editor=""/>

<tags
    ms.service="log-analytics"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/10/2016"
    ms.author="richrund"/>

# <a name="connect-azure-virtual-machines-to-log-analytics"></a>Oprette forbindelse Azure virtuelle maskiner til Log Analytics

For Windows og Linux-computere er den anbefalede metode til indsamling af logfiler og målepunkter ved at installere Log Analytics-agent.

Den nemmeste måde at installere Log Analytics-agent på Azure virtuelle maskiner er gennem filtypenavnet Log Analytics VM.  Ved hjælp af filtypenavnet forenkler installationsprocessen og konfigurerer automatisk agent for at sende data til arbejdsområdet Log Analytics, du angiver. Agenten er også opgraderes automatisk, at sikre, at du har de nyeste funktioner og løsninger.

Til Windows virtuelle maskiner, kan du aktivere *Microsoft overvågning Agent* virtuelt filtypenavn.
Til Linux virtuelle maskiner, kan du aktivere filtypenavnet *OMS Agent For Linux* virtuelt.

Lær mere om [Azure virtuelt extensions](../virtual-machines/virtual-machines-windows-extensions-features.md) og [Linux agent] (… / virtual-machines/virtual-machines-linux-agent-user-guide.md).

Når du bruger agent-baserede af websteder til logdata, skal du konfigurere [datakilder i Log analyser](log-analytics-data-sources.md) for at angive logfiler og mål, som du vil indsamle.

>[AZURE.IMPORTANT] Hvis du konfigurerer Log Analytics indeksere log data ved hjælp af [Azure diagnosticering](log-analytics-azure-storage.md), og du konfigurerer agent for at indsamle de samme logfiler, samles loggene, to gange. Du betaler for begge datakilder. Hvis du har installeret agenten, og derefter skal du indsamle logdata ved brug af alene agent - konfigurere ikke Log analyser for at indsamle logdata fra Azure diagnosticering.

Der er tre nemme metoder til at aktivere filtypenavnet Log Analytics virtuelt:

+ Ved hjælp af portalen Azure
+ Ved hjælp af Azure PowerShell
+ Ved hjælp af en skabelon til Azure ressourcestyring

## <a name="enable-the-vm-extension-in-the-azure-portal"></a>Aktivere filtypenavnet VM i portalen Azure

Du kan installere agent for Log analyser og forbinde den Azure virtuelle maskine, der køres ved hjælp af [Azure-portalen](https://portal.azure.com).

### <a name="to-install-the-log-analytics-agent-and-connect-the-virtual-machine-to-a-log-analytics-workspace"></a>Installere Log Analytics-agent og forbinde den virtuelle maskine til et Log Analytics-arbejdsområde

1.  Logge på [Azure-portalen](http://portal.azure.com).
2.  Vælg **Gennemse** i venstre side af portalen, og gå til **Log Analytics (OMS)** og vælge den.
3.  Vælg den, du vil bruge med Azure VM i din liste over Log Analytics arbejdsområder.  
    ![OMS arbejdsområder](./media/log-analytics-azure-vm-extension/oms-connect-azure-01.png)
4.  Vælg **virtuelle maskiner**under **Log analytics management**.  
    ![Virtuelle maskiner](./media/log-analytics-azure-vm-extension/oms-connect-azure-02.png)
5.  Vælg den virtuelle maskine, som du vil installere agenten på listen over **virtuelle computere**. VM **OMS forbindelsesstatus** angiver, at det er **ikke forbundet**.  
    ![VM ikke har forbindelse](./media/log-analytics-azure-vm-extension/oms-connect-azure-03.png)
6.  Vælg **Opret forbindelse**på siden oplysninger om din virtuelle maskine. Agenten er automatisk installeret og konfigureret for din Log Analytics-arbejdsområde. Denne proces tager et par minutter, i dette tidsrum OMS forbindelsesstatus er *forbindelsen...*  
    ![Oprette forbindelse VM](./media/log-analytics-azure-vm-extension/oms-connect-azure-04.png)
7.  Når du installerer og forbinde agenten, opdateres **OMS** forbindelsesstatus for at få vist **arbejdsområdet**.  
    ![Forbindelse](./media/log-analytics-azure-vm-extension/oms-connect-azure-05.png)


## <a name="enable-the-vm-extension-using-powershell"></a>Aktivere filtypenavnet VM ved hjælp af PowerShell

Der findes forskellige kommandoer til Azure klassisk virtuelle computere og ressourcestyring virtuelle computere. Følgende er eksempler på både klassisk og ressourcestyring virtuelle maskiner.

Brug følgende PowerShell eksempel til klassisk virtuelle maskiner:

```
Add-AzureAccount

$workspaceId = "enter workspace ID here"
$workspaceKey = "enter workspace key here"
$hostedService = "enter hosted service here"

$vm = Get-AzureVM –ServiceName $hostedService

# For Windows VM uncomment the following line
# Set-AzureVMExtension -VM $vm -Publisher 'Microsoft.EnterpriseCloud.Monitoring' -ExtensionName 'MicrosoftMonitoringAgent' -Version '1.*' -PublicConfiguration "{'workspaceId': '$workspaceId'}" -PrivateConfiguration "{'workspaceKey': '$workspaceKey' }" | Update-AzureVM -Verbose

# For Linux VM uncomment the following line
# Set-AzureVMExtension -VM $vm -Publisher 'Microsoft.EnterpriseCloud.Monitoring' -ExtensionName 'OmsAgentForLinux' -Version '1.*' -PublicConfiguration "{'workspaceId': '$workspaceId'}" -PrivateConfiguration "{'workspaceKey': '$workspaceKey' }" | Update-AzureVM -Verbose
```

Brug følgende PowerShell eksempel til Ressourcestyring virtuelle maskiner:

```
Login-AzureRMAccount
Select-AzureSubscription -SubscriptionId "**"

$workspaceName = "your workspace name"
$VMresourcegroup = "**"
$VMresourcename = "**"

$workspace = (Get-AzureRmOperationalInsightsWorkspace).Where({$_.Name -eq $workspaceName})

if ($workspace.Name -ne $workspaceName)
{
    Write-Error "Unable to find OMS Workspace $workspaceName. Do you need to run Select-AzureRMSubscription?"
}

$workspaceId = $workspace.CustomerId
$workspaceKey = (Get-AzureRmOperationalInsightsWorkspaceSharedKeys -ResourceGroupName $workspace.ResourceGroupName -Name $workspace.Name).PrimarySharedKey

$vm = Get-AzureRmVM -ResourceGroupName $VMresourcegroup -Name $VMresourcename
$location = $vm.Location

# For Windows VM uncomment the following line
# Set-AzureRmVMExtension -ResourceGroupName $VMresourcegroup -VMName $VMresourcename -Name 'MicrosoftMonitoringAgent' -Publisher 'Microsoft.EnterpriseCloud.Monitoring' -ExtensionType 'MicrosoftMonitoringAgent' -TypeHandlerVersion '1.0' -Location $location -SettingString "{'workspaceId': '$workspaceId'}" -ProtectedSettingString "{'workspaceKey': '$workspaceKey'}"

# For Linux VM uncomment the following line
# Set-AzureRmVMExtension -ResourceGroupName $VMresourcegroup -VMName $VMresourcename -Name 'OmsAgentForLinux' -Publisher 'Microsoft.EnterpriseCloud.Monitoring' -ExtensionType 'OmsAgentForLinux' -TypeHandlerVersion '1.0' -Location $location -SettingString "{'workspaceId': '$workspaceId'}" -ProtectedSettingString "{'workspaceKey': '$workspaceKey'}"


```
Når du konfigurerer din virtuelle maskine ved hjælp af PowerShell, skal du angive den **Arbejdsområde-ID** og en **Primærnøgle**. Du kan finde-Id og -tasten på siden **Indstillinger** for portalen OMS eller ved hjælp af PowerShell, som vist i det foregående eksempel.

![Arbejdsområde-ID og primær nøgle](./media/log-analytics-azure-vm-extension/oms-analyze-azure-sources.png)

## <a name="deploy-the-vm-extension-using-a-template"></a>Installere filtypenavnet VM ved hjælp af en skabelon

Ved hjælp af Azure Ressourcestyring, kan du oprette en simpel skabelon (i JSON-format), der definerer til installation og konfiguration af dit program. Denne skabelon, der kendes som en skabelon i ressourcestyring og giver en deklarativ måde til at definere installation. Ved hjælp af en skabelon, kan du gentagne gange installere dit program i hele app livscyklus og har tillid til, at dine ressourcer installeres i en ensartet tilstand.

Ved at medtage Log Analytics-agent som en del af Ressourcestyring skabelonen, kan du sikre dig, at hver virtuelt er allerede konfigureret til at rapportere til arbejdsområdet Log Analytics.

Du kan finde flere oplysninger om ressourcestyring skabeloner, [redigering Azure ressourcestyring skabeloner](../resource-group-authoring-templates.md).

Følgende er et eksempel på en ressourcestyring skabelon, der bruges til at implementere en virtuel maskine, der kører Windows med filtypenavnet Microsoft overvågning Agent installeret. Denne skabelon er en typisk virtuelt skabelon med følgende tilføjelser:

+ workspaceId og workspaceName parametre
+ Microsoft.EnterpriseCloud.Monitoring ressource lokalnummer sektion
+ Output til at finde workspaceId og workspaceSharedKey


```
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "adminUsername": {
      "type": "string",
      "metadata": {
        "description": "Username for the Virtual Machine."
      }
    },
    "adminPassword": {
      "type": "securestring",
      "metadata": {
        "description": "Password for the Virtual Machine."
      }
    },
    "dnsLabelPrefix": {
       "type": "string",
       "metadata": {
          "description": "DNS Label for the Public IP. Must be lowercase. It should match with the following regular expression: ^[a-z][a-z0-9-]{1,61}[a-z0-9]$ or it will raise an error."
       }
    },
    "workspaceId": {
      "type": "string",
      "metadata": {
        "description": "OMS workspace ID"
      }
    },
    "workspaceName": {
      "type": "string",
      "metadata": {
         "description": "OMD workspace name"
      }
    },
    "windowsOSVersion": {
      "type": "string",
      "defaultValue": "2012-R2-Datacenter",
      "allowedValues": [
        "2008-R2-SP1",
        "2012-Datacenter",
        "2012-R2-Datacenter",
        "Windows-Server-Technical-Preview"
      ],
      "metadata": {
        "description": "The Windows version for the VM. This will pick a fully patched image of this given Windows version. Allowed values: 2008-R2-SP1, 2012-Datacenter, 2012-R2-Datacenter, Windows-Server-Technical-Preview."
      }
    }
  },
  "variables": {
    "storageAccountName": "[concat(uniquestring(resourceGroup().id), 'standardsa')]",
    "apiVersion": "2015-06-15",
    "imagePublisher": "MicrosoftWindowsServer",
    "imageOffer": "WindowsServer",
    "OSDiskName": "osdiskforwindowssimple",
    "nicName": "myVMNic",
    "addressPrefix": "10.0.0.0/16",
    "subnetName": "Subnet",
    "subnetPrefix": "10.0.0.0/24",
    "storageAccountType": "Standard_LRS",
    "publicIPAddressName": "myPublicIP",
    "publicIPAddressType": "Dynamic",
    "vmStorageAccountContainerName": "vhds",
    "vmName": "MyWindowsVM",
    "vmSize": "Standard_DS1",
    "virtualNetworkName": "MyVNET",
    "resourceId": "[resourceGroup().id]",
    "vnetID": "[resourceId('Microsoft.Network/virtualNetworks',variables('virtualNetworkName'))]",
    "subnetRef": "[concat(variables('vnetID'),'/subnets/',variables('subnetName'))]"
  },
  "resources": [
    {
      "type": "Microsoft.Storage/storageAccounts",
      "name": "[variables('storageAccountName')]",
      "apiVersion": "[variables('apiVersion')]",
      "location": "[resourceGroup().location]",
      "properties": {
        "accountType": "[variables('storageAccountType')]"
      }
    },
    {
      "apiVersion": "[variables('apiVersion')]",
      "type": "Microsoft.Network/publicIPAddresses",
      "name": "[variables('publicIPAddressName')]",
      "location": "[resourceGroup().location]",
      "properties": {
        "publicIPAllocationMethod": "[variables('publicIPAddressType')]",
        "dnsSettings": {
          "domainNameLabel": "[parameters('dnsLabelPrefix')]"
        }
      }
    },
    {
      "apiVersion": "[variables('apiVersion')]",
      "type": "Microsoft.Network/virtualNetworks",
      "name": "[variables('virtualNetworkName')]",
      "location": "[resourceGroup().location]",
      "properties": {
        "addressSpace": {
          "addressPrefixes": [
            "[variables('addressPrefix')]"
          ]
        },
        "subnets": [
          {
            "name": "[variables('subnetName')]",
            "properties": {
              "addressPrefix": "[variables('subnetPrefix')]"
            }
          }
        ]
      }
    },
    {
      "apiVersion": "[variables('apiVersion')]",
      "type": "Microsoft.Network/networkInterfaces",
      "name": "[variables('nicName')]",
      "location": "[resourceGroup().location]",
      "dependsOn": [
        "[concat('Microsoft.Network/publicIPAddresses/', variables('publicIPAddressName'))]",
        "[concat('Microsoft.Network/virtualNetworks/', variables('virtualNetworkName'))]"
      ],
      "properties": {
        "ipConfigurations": [
          {
            "name": "ipconfig1",
            "properties": {
              "privateIPAllocationMethod": "Dynamic",
              "publicIPAddress": {
                "id": "[resourceId('Microsoft.Network/publicIPAddresses',variables('publicIPAddressName'))]"
              },
              "subnet": {
                "id": "[variables('subnetRef')]"
              }
            }
          }
        ]
      }
    },
    {
      "apiVersion": "2015-06-15",
      "type": "Microsoft.Compute/virtualMachines",
      "name": "[variables('vmName')]",
      "location": "[resourceGroup().location]",
      "dependsOn": [
        "[concat('Microsoft.Storage/storageAccounts/', variables('storageAccountName'))]",
        "[concat('Microsoft.Network/networkInterfaces/', variables('nicName'))]"
      ],
      "properties": {
        "hardwareProfile": {
          "vmSize": "[variables('vmSize')]"
        },
        "osProfile": {
          "computername": "[variables('vmName')]",
          "adminUsername": "[parameters('adminUsername')]",
          "adminPassword": "[parameters('adminPassword')]"
        },
        "storageProfile": {
          "imageReference": {
            "publisher": "[variables('imagePublisher')]",
            "offer": "[variables('imageOffer')]",
            "sku": "[parameters('windowsOSVersion')]",
            "version": "latest"
          },
          "osDisk": {
            "name": "osdisk",
            "vhd": {
              "uri": "[concat('http://',variables('storageAccountName'),'.blob.core.windows.net/',variables('vmStorageAccountContainerName'),'/',variables('OSDiskName'),'.vhd')]"
            },
            "caching": "ReadWrite",
            "createOption": "FromImage"
          }
        },
        "networkProfile": {
          "networkInterfaces": [
            {
              "id": "[resourceId('Microsoft.Network/networkInterfaces',variables('nicName'))]"
            }
          ]
        },
        "diagnosticsProfile": {
          "bootDiagnostics": {
             "enabled": "true",
             "storageUri": "[concat('http://',variables('storageAccountName'),'.blob.core.windows.net')]"
          }
        }
      },
      "resources": [
        {
          "type": "extensions",
          "name": "Microsoft.EnterpriseCloud.Monitoring",
          "apiVersion": "[variables('apiVersion')]",
          "location": "[resourceGroup().location]",
          "dependsOn": [
            "[concat('Microsoft.Compute/virtualMachines/', variables('vmName'))]"
          ],
          "properties": {
            "publisher": "Microsoft.EnterpriseCloud.Monitoring",
            "type": "MicrosoftMonitoringAgent",
            "typeHandlerVersion": "1.0",
            "autoUpgradeMinorVersion": true,
            "settings": {
              "workspaceId": "[parameters('workspaceId')]"
            },
            "protectedSettings": {
              "workspaceKey": "[listKeys(resourceId('Microsoft.OperationalInsights/workspaces', parameters('workspaceName')), '2015-03-20').primarySharedKey]"
            }
          }
        }
      ]
    }
  ],
  "outputs": {
      "sharedKeyOutput": {
         "value": "[listKeys(resourceId('Microsoft.OperationalInsights/workspaces/', parameters('workspaceName')), '2015-03-20').primarySharedKey]",
         "type": "string"
      },
      "workspaceIdOutput": {
         "value": "[reference(concat('Microsoft.OperationalInsights/workspaces/', parameters('workspaceName')), '2015-03-20').customerId]",
        "type" : "string"
      }
  }
}
```

Du kan installere en skabelon ved hjælp af følgende PowerShell-kommando:

```
New-AzureRmResourceGroupDeployment -ResourceGroupName $resourceGroupName -TemplateFile $templateFilePath
```

## <a name="troubleshooting-windows-virtual-machines"></a>Fejlfinding i forbindelse med Windows virtuelle maskiner

Hvis filtypenavnet *Microsoft overvågning Agent* VM agent ikke installeret eller har rapporteret kan du udføre følgende trin for at foretage fejlfinding af problemet.

1. Kontrollér, om Azure VM agent er installeret og fungerer korrekt ved hjælp af trinnene i [KB 2965986](https://support.microsoft.com/kb/2965986#mt1).
  + Du kan også gennemse logfilen VM agent`C:\WindowsAzure\logs\WaAppAgent.log`
  + Hvis logfilen ikke findes, er VM agent ikke installeret.
    - [Installer Azure VM Agent på klassisk FOS](../virtual-machines/virtual-machines-windows-classic-agents-and-extensions.md)
2. Bekræft Microsoft overvågning Agent lokalnummer godkendelse gyldighed opgaven kører at benytte følgende fremgangsmåde:
  + Log på den virtuelle maskine
  + Åbn Opgavestyring, og find den `update_azureoperationalinsight_agent_heartbeat` opgave
  + Bekræfte opgaven er aktiveret og kører alle et minut
  + Kontrollere godkendelse af gyldighed logfilen`C:\WindowsAzure\Logs\Plugins\Microsoft.EnterpriseCloud.Monitoring.MicrosoftMonitoringAgent\heartbeat.log`
3. Gennemse Microsoft overvågning Agent VM lokalnummer logfilerne i`C:\Packages\Plugins\Microsoft.EnterpriseCloud.Monitoring.MicrosoftMonitoringAgent`
3. Sikre, at den virtuelle maskine kan køre PowerShell-scripts
4. Sikre tilladelser på C:\Windows\temp ikke er blevet ændret
5. Få vist status for Microsoft overvågning Agent ved at skrive følgende i et øgede PowerShell-vindue på den virtuelle maskine`  (New-Object -ComObject 'AgentConfigManager.MgmtSvcCfg').GetCloudWorkspaces() | Format-List`
6. Gennemse logfilerne til Microsoft overvågning Agent installation i`C:\Windows\System32\config\systemprofile\AppData\Local\SCOM\Logs`

Se [fejlfinding i forbindelse med Windows filtypenavne](../virtual-machines/virtual-machines-windows-extensions-troubleshoot.md)kan finde flere oplysninger.

## <a name="troubleshooting-linux-virtual-machines"></a>Fejlfinding i forbindelse med Linux virtuelle maskiner

Hvis filtypenavnet *OMS Agent for Linux* VM agent ikke installeret eller har rapporteret kan du udføre følgende trin for at foretage fejlfinding af problemet.

1. Hvis lokalnummer status er *Ukendt* afkrydsningsfelt, hvis Azure VM agent er installeret og fungerer korrekt ved Gennemse logfilen VM agent`/var/log/waagent.log`
  + Hvis logfilen ikke findes, er VM agent ikke installeret.
  - [Installer Azure VM Agent på Linux FOS](../virtual-machines/virtual-machines-linux-agent-user-guide.md)
2. Få vist OMS Agent for andre beskadiget statusangivelser for Linux VM lokalnummer logfilerne `/var/log/azure/Microsoft.EnterpriseCloud.Monitoring.OmsAgentForLinux/*/extension.log` og`/var/log/azure/Microsoft.EnterpriseCloud.Monitoring.OmsAgentForLinux/*/CommandExecution.log`
3. Hvis lokalnummer status er i orden, men data ikke overføres Gennemse OMS Agent for Linux logfiler i`/var/opt/microsoft/omsagent/log/omsagent.log`

Se [fejlfinding i forbindelse med Linux filtypenavne](../virtual-machines/virtual-machines-linux-extensions-troubleshoot.md)kan finde flere oplysninger.


## <a name="next-steps"></a>Næste trin

+ Konfigurere [datakilder i Log analyser](log-analytics-data-sources.md) for at angive logfiler og målepunkter at indsamle.
+ For at indsamle data fra virtuelle maskiner, [tilføje Log Analytics løsninger fra løsningsgalleriet](log-analytics-add-solutions.md).
+ [Indsamle data via Azure diagnosticering](log-analytics-azure-storage.md) til andre ressourcer, der kører i Azure.

Du kan installere Log Analytics-agent for computere, der ikke er i Azure, ved hjælp af de metoder, der er beskrevet i følgende artikler:

+ [Oprette forbindelse Windows-computere til Log Analytics](log-analytics-windows-agents.md)
+ [Forbinde Linux-computere til Log Analytics](log-analytics-linux-agents.md)
