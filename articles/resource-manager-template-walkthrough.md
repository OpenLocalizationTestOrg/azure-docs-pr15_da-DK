<properties
   pageTitle="Ressourcestyring skabelon gennemgang | Microsoft Azure"
   description="En trinvis gennemgang af en ressource manager-skabelon klargøring af en grundlæggende Azure IaaS arkitektur."
   services="azure-resource-manager"
   documentationCenter="na"
   authors="navalev"
   manager="timlt"
   editor=""/>

<tags
   ms.service="azure-resource-manager"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="08/04/2016"
   ms.author="navale;tomfitz"/>
   
# <a name="resource-manager-template-walkthrough"></a>Ressourcestyring skabelon gennemgang

En af de første spørgsmål, når du opretter en skabelon er "hvordan du starter?". En kan starte med en tom skabelon, efter den grundlæggende struktur, der er beskrevet i [redigering skabelon artikel](resource-group-authoring-templates.md#template-format), og Tilføj ressourcer og relevante parametre og variabler. Et godt alternativ ville være at starte med at gå gennem [Galleri med Hurtig start](https://github.com/Azure/azure-quickstart-templates) , og se efter lignende scenarier til den, du forsøger at oprette. Du kan flette flere skabeloner eller redigere en eksisterende så den passer til dit eget scenarie. 

Lad os se nærmere på en fælles infrastruktur:

* To virtuelle maskiner, der bruger den samme konto lagerplads, er der i det samme sæt tilgængelighed og på samme undernet af et virtuelt netværk.
* En enkelt NIC og VM IP adresse for hver virtuelt.
* Belastningsjustering med en regel for port 80 til justering af belastning

![arkitektur](./media/resource-group-overview/arm_arch.png)

Dette emne fører dig gennem trinnene til oprettelse af en ressourcestyring skabelon til denne infrastruktur. Den endelige skabelon, du opretter er baseret på en hurtig start skabelon, der kaldes [2 FOS i et belastning og regler for justering af belastning](https://azure.microsoft.com/documentation/templates/201-2-vms-loadbalancer-lbrules/).

Men, der er mange at oprette en på én gang, så lad os først oprette en lagerplads konto og installere den. Når du mestrer oprette kontoen lagerplads, skal du føje de andre ressourcer og installere skabelonen for at fuldføre infrastrukturen igen.

>[AZURE.NOTE] Du kan bruge alle typer editor, når du opretter skabelonen. Visual Studio indeholder funktioner, der forenkler skabelon udvikling, men du behøver ikke Visual Studio til at udføre dette selvstudium. Se [oprette og implementere Azure ressourcegrupper via Visual Studio](vs-azure-tools-resource-groups-deployment-projects-create-deploy.md)selvstudium til brug af Visual Studio til at oprette en Web App og SQL-Database installation. 

## <a name="create-the-resource-manager-template"></a>Oprette skabelonen ressourcestyring

Skabelonen er en JSON-fil, der definerer alle de ressourcer, du vil installere. Også mulighed for at definere parametre, der er angivet under installationen, variabler, der er dannet ud fra andre værdier og udtryk, og der oprettes fra installationen. 

Lad os starte med den nemmeste skabelon:

```json
    {
      "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
      "contentVersion": "1.0.0.0",
      "parameters": {  },
      "variables": {  },
      "resources": [  ],
      "outputs": {  }
    }
 ```

Gemme filen som **azuredeploy.json** (Bemærk, at skabelonen, der kan have en hvilken som helst navn, du vil, lige, at den skal være en json-fil).

## <a name="create-a-storage-account"></a>Oprette en lagerplads-konto
Tilføje et objekt, der definerer kontoen lagerplads, i afsnittet **ressourcer** , som vist nedenfor. 

```json
"resources": [
  {
    "type": "Microsoft.Storage/storageAccounts",
    "name": "[parameters('storageAccountName')]",
    "apiVersion": "2015-06-15",
    "location": "[resourceGroup().location]",
    "properties": {
      "accountType": "Standard_LRS"
    }
  }
]
```

Du måske spekulerer du over hvor disse egenskaber og værdier kommer fra. Egenskaber for **type**, **navn**, **apiVersion**og **placering** er standard elementer, der er tilgængelige for alle ressourcetyper. Du kan få mere at vide om de elementer, der er almindelige på [ressourcer](resource-group-authoring-templates.md#resources). **navn** er angivet til en parameterværdi, du sender i under installation og **placering** som den placering, der bruges af ressourcegruppen. Vi vil se på, hvordan du bestemme **typen** og **apiVersion** i afsnittene herunder.

Sektionen **Egenskaber** indeholder alle de egenskaber, der er entydige for en bestemt ressourcetype. Værdier, du angiver i dette afsnit nøjagtigt overens med handlingen læg i REST-API til oprettelse af den pågældende ressource. Når du opretter en konto med lagerplads, skal du angive en **accountType**. Bemærk i [REST-API til at oprette en konto med lagerplads](https://msdn.microsoft.com/library/azure/mt163564.aspx) , afsnittet egenskaber i handlingen RESTEN indeholder også en **accountType** egenskab, og de tilladte værdier er beskrevet. I dette eksempel på kontotypen er indstillet til **Standard_LRS**, men du kan angive en anden værdi eller tillade brugere til at videregive i kontotypen som en parameter.

Nu Lad os gå tilbage til sektionen **parametre** , og se, hvordan du definerer navnet på kontoen, lagerplads. Du kan få mere at vide om brug af parametre på [parametre](resource-group-authoring-templates.md#parameters). 

```json
"parameters" : {
    "storageAccountName": {
      "type": "string",
      "metadata": {
        "description": "Storage Account Name"
      }
    }
}
```
Her kan du defineret en parameter af typen streng, der indeholder navnet på kontoen, lagerplads. Værdien for denne parameter får under installation af skabelon.

## <a name="deploying-the-template"></a>Implementering af skabelonen
Vi har en fuld skabelon til at oprette en ny konto lagerplads. Som du kan huske, er skabelonen gemt i **azuredeploy.json** fil:

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters" : {
    "storageAccountName": {
      "type": "string",
      "metadata": {
        "description": "Storage Account Name"
      }
    }
  },  
  "resources": [
    {
      "type": "Microsoft.Storage/storageAccounts",
      "name": "[parameters('storageAccountName')]",
      "apiVersion": "2015-06-15",
      "location": "[resourceGroup().location]",
      "properties": {
        "accountType": "Standard_LRS"
      }
    }
  ]
}
```

Der findes en hel måder til at installere en skabelon, som du kan se i [ressource installation artikel](resource-group-template-deploy.md). For at installere skabelonen ved hjælp af Azure PowerShell, skal du bruge:

```powershell
# create a new resource group
New-AzureRmResourceGroup -Name ExampleResourceGroup -Location "West Europe"

# deploy the template to the resource group
New-AzureRmResourceGroupDeployment -Name ExampleDeployment -ResourceGroupName ExampleResourceGroup -TemplateFile azuredeploy.json
```

Eller du skal installere skabelonen ved hjælp af Azure CLI, bruge:

```
azure group create -n ExampleResourceGroup -l "West Europe"

azure group deployment create -f azuredeploy.json -g ExampleResourceGroup -n ExampleDeployment
```

Du er nu stolt ejeren af en lagerplads konto!

De næste trin er at tilføje alle de ressourcer, der kræves for at installere den arkitektur, der er beskrevet i starten af dette selvstudium. Du kan tilføje disse ressourcer i den samme skabelon, du har arbejdet på.

## <a name="availability-set"></a>Tilgængelighed sæt
Tilføje en ny angivet for de virtuelle maskiner efter definitionen for kontoen lagerplads. I dette tilfælde er der ingen yderligere egenskaber, der er påkrævet, så definitionen er ganske enkelt. Se [REST-API til oprettelse af en tilgængelighed angive](https://msdn.microsoft.com/library/azure/mt163607.aspx) til sektionen Egenskaber for fuld, hvis du vil definere update domain Tæl og et domæne Tæl værdierne.

```json
{
   "type": "Microsoft.Compute/availabilitySets",
   "name": "[variables('availabilitySetName')]",
   "apiVersion": "2015-06-15",
   "location": "[resourceGroup().location]",
   "properties": {}
}
```

Bemærk, at **navnet** er angivet til værdien af en variabel. For denne skabelon, skal navnet på sættet tilgængelighed bruges på et par forskellige steder. Du kan nemmere vedligeholde din skabelon ved at definere den pågældende værdi én gang og bruge den på flere steder.

Den værdi, du angiver **type** indeholder både provideren ressource og ressourcetypen. Provideren ressource er **Microsoft.Compute** tilgængelighed sæt, og ressourcetypen er **availabilitySets**. Du kan få listen over tilgængelige ressource udbydere ved at køre følgende PowerShell-kommando:

```powershell
    Get-AzureRmResourceProvider -ListAvailable
```

Eller hvis du bruger Azure CLI, kan du køre følgende kommando:
```
    azure provider list
```
Da der i dette emne du opretter med lagerplads firmaer, virtuelle maskiner og virtuelle netværk, kan du arbejde med:

- Microsoft.Storage
- Microsoft.Compute
- Microsoft.Network

For at se ressourcetyper for en bestemt udbyder, skal du køre følgende PowerShell-kommando:

```powershell
    (Get-AzureRmResourceProvider -ProviderNamespace Microsoft.Compute).ResourceTypes
```

Eller til Azure CLI følgende kommando vil returnere de tilgængelige typer i JSON-formatet og gemme den til en fil.

```
    azure provider show Microsoft.Compute --json > c:\temp.json
```

Du bør se **availabilitySets** som et af typerne i **Microsoft.Compute**. Det fulde navn på typen er **Microsoft.Compute/availabilitySets**. Du kan bestemme typen ressourcenavnet på grund af ressourcer i du skabelon.

## <a name="public-ip"></a>Offentlige IP-adresse
Definere en offentlig IP-adresse. Se igen på [REST-API til offentlige IP-adresser](https://msdn.microsoft.com/library/azure/mt163590.aspx) for egenskaber for at angive.

```json
{
  "apiVersion": "2015-06-15",
  "type": "Microsoft.Network/publicIPAddresses",
  "name": "[parameters('publicIPAddressName')]",
  "location": "[resourceGroup().location]",
  "properties": {
    "publicIPAllocationMethod": "Dynamic",
    "dnsSettings": {
      "domainNameLabel": "[parameters('dnsNameforLBIP')]"
    }
  }
}
```

Metoden til fordeling er indstillet til **dynamiske** , men du kan angive den til den værdi, du har brug for eller indstille den til at acceptere en parameterværdi. Du har aktiveret brugerne af din skabelon til at overføre en værdi for etiketten domæne navn.

Nu, Lad os se på, hvordan du bestemme **apiVersion**. Den værdi, du angiver blot svarer til versionen af REST-API, du vil bruge, når du opretter ressourcen. Så kan du se på REST-API dokumentation for den pågældende ressource. Eller du kan køre følgende PowerShell-kommando for en bestemt type.

```powershell
    ((Get-AzureRmResourceProvider -ProviderNamespace Microsoft.Network).ResourceTypes | Where-Object ResourceTypeName -eq publicIPAddresses).ApiVersions
```
Som returnerer følgende værdier:

    2015-06-15
    2015-05-01-preview
    2014-12-01-preview

For at se API versionerne med Azure CLI skal køre samme **azure udbyder vise** kommandoen vist tidligere.

Når du opretter en ny skabelon, Vælg den nyeste version af API.

## <a name="virtual-network-and-subnet"></a>Virtuelt netværk og undernet
Oprette et virtuelt netværk med ét undernet. Se på [REST-API til virtuelle netværk](https://msdn.microsoft.com/library/azure/mt163661.aspx) for alle egenskaber for at angive.

```json
{
   "apiVersion": "2015-06-15",
   "type": "Microsoft.Network/virtualNetworks",
   "name": "[parameters('vnetName')]",
   "location": "[resourceGroup().location]",
   "properties": {
     "addressSpace": {
       "addressPrefixes": [
         "10.0.0.0/16"
       ]
     },
     "subnets": [
       {
         "name": "[variables('subnetName')]",
         "properties": {
           "addressPrefix": "10.0.0.0/24"
         }
       }
     ]
   }
}
```

## <a name="load-balancer"></a>Justering af belastning
Nu kan du oprette en ekstern modstående justering af belastning. Da denne justering af belastning bruger den offentlige IP-adresse, skal du angive en afhængighed på den offentlige IP-adresse i sektionen **dependsOn** . Det betyder, at justering af belastning ikke kan installeres, indtil den offentlige IP-adresse er færdig med at implementere. Uden at definere denne afhængighed, modtager du en fejl, fordi ressourcestyring forsøger at installere ressourcerne, der parallelt og forsøger at angive justering af belastning til offentlige IP-adresse, der ikke findes endnu. 

Du kan også oprette en back end-adresse puljen, et par indgående regler for NAT til RDP til FOS og en regel for justering af belastning med en efterprøvning af tcp af på port 80 i denne ressource definition. Udtjekning [REST-API til justering af belastning](https://msdn.microsoft.com/library/azure/mt163574.aspx) for alle egenskaberne.

```json
{
   "apiVersion": "2015-06-15",
   "name": "[parameters('lbName')]",
   "type": "Microsoft.Network/loadBalancers",
   "location": "[resourceGroup().location]",
   "dependsOn": [
     "[concat('Microsoft.Network/publicIPAddresses/', parameters('publicIPAddressName'))]"
   ],
   "properties": {
     "frontendIPConfigurations": [
       {
         "name": "LoadBalancerFrontEnd",
         "properties": {
           "publicIPAddress": {
             "id": "[variables('publicIPAddressID')]"
           }
         }
       }
     ],
     "backendAddressPools": [
       {
         "name": "BackendPool1"
       }
     ],
     "inboundNatRules": [
       {
         "name": "RDP-VM0",
         "properties": {
           "frontendIPConfiguration": {
             "id": "[variables('frontEndIPConfigID')]"
           },
           "protocol": "tcp",
           "frontendPort": 50001,
           "backendPort": 3389,
           "enableFloatingIP": false
         }
       },
       {
         "name": "RDP-VM1",
         "properties": {
           "frontendIPConfiguration": {
             "id": "[variables('frontEndIPConfigID')]"
           },
           "protocol": "tcp",
           "frontendPort": 50002,
           "backendPort": 3389,
           "enableFloatingIP": false
         }
       }
     ],
     "loadBalancingRules": [
       {
         "name": "LBRule",
         "properties": {
           "frontendIPConfiguration": {
             "id": "[variables('frontEndIPConfigID')]"
           },
           "backendAddressPool": {
             "id": "[variables('lbPoolID')]"
           },
           "protocol": "tcp",
           "frontendPort": 80,
           "backendPort": 80,
           "enableFloatingIP": false,
           "idleTimeoutInMinutes": 5,
           "probe": {
             "id": "[variables('lbProbeID')]"
           }
         }
       }
     ],
     "probes": [
       {
         "name": "tcpProbe",
         "properties": {
           "protocol": "tcp",
           "port": 80,
           "intervalInSeconds": 5,
           "numberOfProbes": 2
         }
       }
     ]
   }
}
```

## <a name="network-interface"></a>Netværksgrænsefladen
Du skal oprette 2 netværksgrænseflader, en for hver VM. I stedet for at skulle omfatter duplikerede poster for netværk grænsefladerne, kan du bruge [copyIndex() funktionen](resource-group-create-multiple.md) til at gentages over den kopi løkke (kaldet nicLoop) og oprette de tal netværksgrænseflader som defineret i den `numberOfInstances` variabler. Grænsefladen for det netværk, afhænger af oprettelse af det virtuelle netværk og belastning. Det undernet, der er defineret i virtuelt netværk oprettelse, og Indlæs belastningsjusteringstjenesten id'et bruges til at konfigurere Indlæs belastningsjusteringstjenesten adresse puljen og indgående NAT regler.
Se på [REST-API til netværksgrænseflader](https://msdn.microsoft.com/library/azure/mt163668.aspx) til alle egenskaberne.

```json
{
   "apiVersion": "2015-06-15",
   "type": "Microsoft.Network/networkInterfaces",
   "name": "[concat(parameters('nicNamePrefix'), copyindex())]",
   "location": "[resourceGroup().location]",
   "copy": {
     "name": "nicLoop",
     "count": "[variables('numberOfInstances')]"
   },
   "dependsOn": [
     "[concat('Microsoft.Network/virtualNetworks/', parameters('vnetName'))]",
     "[concat('Microsoft.Network/loadBalancers/', parameters('lbName'))]"
   ],
   "properties": {
     "ipConfigurations": [
       {
         "name": "ipconfig1",
         "properties": {
           "privateIPAllocationMethod": "Dynamic",
           "subnet": {
             "id": "[variables('subnetRef')]"
           },
           "loadBalancerBackendAddressPools": [
             {
               "id": "[concat(variables('lbID'), '/backendAddressPools/BackendPool1')]"
             }
           ],
           "loadBalancerInboundNatRules": [
             {
               "id": "[concat(variables('lbID'),'/inboundNatRules/RDP-VM', copyindex())]"
             }
           ]
         }
       }
     ]
   }
}
```

## <a name="virtual-machine"></a>Virtuelt
Du skal oprette 2 virtuelle maskiner, ved hjælp af copyIndex() funktion, som du gjorde i oprettelse af [netværksgrænseflader](#network-interface).
VM oprettelse afhænger af kontoen lagerplads netværk interface og tilgængelighed. Denne VM oprettes fra et billede af marketplace, som defineret i den `storageProfile` egenskaben – `imageReference` bruges til at definere billede publisher, tilbud, sku og version. Til sidst skal konfigureres en diagnosticering profil for at aktivere diagnosticering af VM. 

For at finde de relevante egenskaber for et marketplace billede, skal du følge artiklerne [Vælg Linux virtuelt billeder](./virtual-machines/virtual-machines-linux-cli-ps-findimage.md) eller [Vælg Windows virtuelt billeder](./virtual-machines/virtual-machines-windows-cli-ps-findimage.md) .

```json
{
   "apiVersion": "2015-06-15",
   "type": "Microsoft.Compute/virtualMachines",
   "name": "[concat(parameters('vmNamePrefix'), copyindex())]",
   "copy": {
     "name": "virtualMachineLoop",
     "count": "[variables('numberOfInstances')]"
   },
   "location": "[resourceGroup().location]",
   "dependsOn": [
     "[concat('Microsoft.Storage/storageAccounts/', parameters('storageAccountName'))]",
     "[concat('Microsoft.Network/networkInterfaces/', parameters('nicNamePrefix'), copyindex())]",
     "[concat('Microsoft.Compute/availabilitySets/', variables('availabilitySetName'))]"
   ],
   "properties": {
     "availabilitySet": {
       "id": "[resourceId('Microsoft.Compute/availabilitySets',variables('availabilitySetName'))]"
     },
     "hardwareProfile": {
       "vmSize": "[parameters('vmSize')]"
     },
     "osProfile": {
       "computerName": "[concat(parameters('vmNamePrefix'), copyIndex())]",
       "adminUsername": "[parameters('adminUsername')]",
       "adminPassword": "[parameters('adminPassword')]"
     },
     "storageProfile": {
       "imageReference": {
         "publisher": "[parameters('imagePublisher')]",
         "offer": "[parameters('imageOffer')]",
         "sku": "[parameters('imageSKU')]",
         "version": "latest"
       },
       "osDisk": {
         "name": "osdisk",
         "vhd": {
           "uri": "[concat('http://',parameters('storageAccountName'),'.blob.core.windows.net/vhds/','osdisk', copyindex(), '.vhd')]"
         },
         "caching": "ReadWrite",
         "createOption": "FromImage"
       }
     },
     "networkProfile": {
       "networkInterfaces": [
         {
           "id": "[resourceId('Microsoft.Network/networkInterfaces',concat(parameters('nicNamePrefix'),copyindex()))]"
         }
       ]
     },
     "diagnosticsProfile": {
       "bootDiagnostics": {
          "enabled": "true",
          "storageUri": "[concat('http://',parameters('storageAccountName'),'.blob.core.windows.net')]"
       }
     }
}
```

>[AZURE.NOTE] Billeder, der er udgivet af **3 producenter**, skal du angive en anden egenskab med navnet `plan`. Et eksempel kan findes i [denne skabelon](https://github.com/Azure/azure-quickstart-templates/tree/master/checkpoint-single-nic) fra galleriet Hurtig start. 

Du er færdig med at definere ressourcer til din skabelon.

## <a name="parameters"></a>Parametre

Definer de værdier, der kan angives, når du installerer skabelonen, i sektionen parametre. Kun Definer parametre for værdier, som du mener skal ændres under installationen. Du kan angive en standardværdi for en parameter, der bruges, hvis der ikke angives under installationen. Du kan også angive de tilladte værdier, som vist for parameteren **imageSKU** .

```json
"parameters": {
    "storageAccountName": {
      "type": "string",
      "metadata": {
        "description": "Name of storage account"
      }
    },
    "adminUsername": {
      "type": "string",
      "metadata": {
        "description": "Admin username"
      }
    },
    "adminPassword": {
      "type": "securestring",
      "metadata": {
        "description": "Admin password"
      }
    },
    "dnsNameforLBIP": {
      "type": "string",
      "metadata": {
        "description": "DNS for Load Balancer IP"
      }
    },
    "vmNamePrefix": {
      "type": "string",
      "defaultValue": "myVM",
      "metadata": {
        "description": "Prefix to use for VM names"
      }
    },
    "imagePublisher": {
      "type": "string",
      "defaultValue": "MicrosoftWindowsServer",
      "metadata": {
        "description": "Image Publisher"
      }
    },
    "imageOffer": {
      "type": "string",
      "defaultValue": "WindowsServer",
      "metadata": {
        "description": "Image Offer"
      }
    },
    "imageSKU": {
      "type": "string",
      "defaultValue": "2012-R2-Datacenter",
      "allowedValues": [
        "2008-R2-SP1",
        "2012-Datacenter",
        "2012-R2-Datacenter"
      ],
      "metadata": {
        "description": "Image SKU"
      }
    },
    "lbName": {
      "type": "string",
      "defaultValue": "myLB",
      "metadata": {
        "description": "Load Balancer name"
      }
    },
    "nicNamePrefix": {
      "type": "string",
      "defaultValue": "nic",
      "metadata": {
        "description": "Network Interface name prefix"
      }
    },
    "publicIPAddressName": {
      "type": "string",
      "defaultValue": "myPublicIP",
      "metadata": {
        "description": "Public IP Name"
      }
    },
    "vnetName": {
      "type": "string",
      "defaultValue": "myVNET",
      "metadata": {
        "description": "VNET name"
      }
    },
    "vmSize": {
      "type": "string",
      "defaultValue": "Standard_D1",
      "metadata": {
        "description": "Size of the VM"
      }
    }
  }
```

## <a name="variables"></a>Variabler

Du kan angive værdier, der bruges i mere end ét sted i din skabelon eller værdier, der er dannet ud fra andre udtryk eller variabler i sektionen variabler. Variabler bruges ofte til at forenkle syntaksen af din skabelon.

```json
"variables": {
    "availabilitySetName": "myAvSet",
    "subnetName": "Subnet-1",
    "vnetID": "[resourceId('Microsoft.Network/virtualNetworks',parameters('vnetName'))]",
    "subnetRef": "[concat(variables('vnetID'),'/subnets/',variables ('subnetName'))]",
    "publicIPAddressID": "[resourceId('Microsoft.Network/publicIPAddresses',parameters('publicIPAddressName'))]",
    "numberOfInstances": 2,
    "lbID": "[resourceId('Microsoft.Network/loadBalancers',parameters('lbName'))]",
    "frontEndIPConfigID": "[concat(variables('lbID'),'/frontendIPConfigurations/LoadBalancerFrontEnd')]",
    "lbPoolID": "[concat(variables('lbID'),'/backendAddressPools/BackendPool1')]",
    "lbProbeID": "[concat(variables('lbID'),'/probes/tcpProbe')]"
  }
```

Du har fuldført skabelonen! Du kan sammenligne din skabelon mod den fulde skabelon i [galleriet Hurtig start](https://github.com/Azure/azure-quickstart-templates) under [2 FOS med justering af belastning og indlæse belastningsjusteringstjenesten regler skabelon](https://github.com/Azure/azure-quickstart-templates/tree/master/201-2-vms-loadbalancer-lbrules). Skabelonen kan være en smule anderledes baseret på ved hjælp af forskellige version tal. 

Du kan installere skabelonen igen ved hjælp af de samme kommandoer, du brugte, da implementerer kontoen lagerplads. Du behøver ikke at slette kontoen lagerplads før du anvender igen, fordi ressourcestyring springer genskabe ressourcer, der allerede findes, og ikke er blevet ændret.

## <a name="next-steps"></a>Næste trin

- [Azure ressourcestyring skabelon Visualizer (ARMViz)](http://armviz.io/#/) er et godt værktøj til visualisering af ARM skabeloner, efterhånden som de kan bliver for stor til at forstå lige fra læsning af json-filen.
- Hvis du vil vide mere om strukturen i en skabelon, se [redigering Azure ressourcestyring skabeloner](resource-group-authoring-templates.md).
- For at få mere for at vide om installation af en skabelon, se [Implementer en ressourcegruppe med Azure ressourcestyring skabelon](resource-group-template-deploy.md)
