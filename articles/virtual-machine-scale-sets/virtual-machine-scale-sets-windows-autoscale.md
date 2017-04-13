<properties
    pageTitle="Autoskalering Windows virtuelt skala angiver | Microsoft Azure"
    description="Konfigurere Autoskalering til en Windows virtuelt skala sæt med Azure PowerShell"
    services="virtual-machine-scale-sets"
    documentationCenter=""
    authors="davidmu1"
    manager="timlt"
    editor=""
    tags="azure-resource-manager"/>

<tags
    ms.service="virtual-machine-scale-sets"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/27/2016"
    ms.author="davidmu"/>

# <a name="automatically-scale-machines-in-a-virtual-machine-scale-set"></a>Skalere automatisk computere i et virtuelt skala sæt

Virtuelt skala sæt gør det nemt for dig at installere og administrere identiske virtuelle maskiner som et sæt. Skala sæt giver et meget SVG og kan tilpasses Beregn lag til store programmer, og de understøtter Windows-platform billeder, Linux platform billeder, brugerdefinerede billeder og filtypenavne. Se [Virtuelt skala angiver](virtual-machine-scale-sets-overview.md)kan finde flere oplysninger om skala sæt.

Dette selvstudium viser, hvordan du opretter et skala sæt af Windows virtuelle maskiner og skalere automatisk på computere i dialogboksen Angiv. Du opretter skalaen og konfigurere skalering ved at oprette en skabelon til Azure ressourcestyring og implementerer den med Azure PowerShell. Du kan finde flere oplysninger om skabeloner, [redigering Azure ressourcestyring skabeloner](../resource-group-authoring-templates.md). Hvis du vil vide mere om automatisk skalering af skala sæt, skal du se [automatisk skalering og virtuelt skala angiver](virtual-machine-scale-sets-autoscale-overview.md).

I denne artikel kan installere du de følgende ressourcer og filtypenavne:

- Microsoft.Storage/storageAccounts
- Microsoft.Network/virtualNetworks
- Microsoft.Network/publicIPAddresses
- Microsoft.Network/loadBalancers
- Microsoft.Network/networkInterfaces
- Microsoft.Compute/virtualMachines
- Microsoft.Compute/virtualMachineScaleSets
- Microsoft.Insights.VMDiagnosticsSettings
- Microsoft.Insights/autoscaleSettings

Se [Azure beregne, netværk, og lagerplads udbydere under Azure ressourcestyring](../virtual-machines/virtual-machines-windows-compare-deployment-models.md)kan finde flere oplysninger om ressourcestyring ressourcer.

## <a name="step-1-install-azure-powershell"></a>Trin 1: Installere Azure PowerShell

Se, [hvordan du installerer og konfigurerer Azure PowerShell](../powershell-install-configure.md) for at få oplysninger om at installere den nyeste version af Azure PowerShell, vælge dit abonnement og logge på Azure.

## <a name="step-2-create-a-resource-group-and-a-storage-account"></a>Trin 2: Oprette en ressourcegruppe og en lagerplads-konto

1. **Opret en ressourcegruppe** – alle de ressourcer, der skal installeres til en ressourcegruppe. Brug [Ny AzureRmResourceGroup](https://msdn.microsoft.com/library/mt603739.aspx) til at oprette en ressourcegruppe med navnet **vmsstestrg1**.

2. **Opret en lagerplads konto** – denne lagerplads konto er, hvor skabelonen er gemt. Brug [Ny AzureRmStorageAccount](https://msdn.microsoft.com/library/mt607148.aspx) til at oprette en lagerplads konto med navnet **vmsstestsa**.

## <a name="step-3-create-the-template"></a>Trin 3: Opret skabelonen
En skabelon til Azure ressourcestyring gør det muligt for dig at installere og administrere Azure ressourcer sammen ved hjælp af en JSON beskrivelse af de ressourcer og tilknyttede installation parametre.

1. Oprette filen C:\VMSSTemplate.json i din foretrukne editor, og Tilføj den oprindelige JSON struktur for at understøtte skabelonen.

        {
          "$schema":"http://schema.management.azure.com/schemas/2014-04-01-preview/VM.json",
          "contentVersion": "1.0.0.0",
          "parameters": {
          },
          "variables": {
          },
          "resources": [
          ]
        }

2. Parametre er ikke altid påkrævet, men de er en måde at skrive værdier, når skabelonen, der er installeret. Tilføj disse parametre under det parametre overordnede element, du har føjet til skabelonen.

        "vmName": { "type": "string" },
        "vmSSName": { "type": "string" },
        "instanceCount": { "type": "string" },
        "adminUsername": { "type": "string" },
        "adminPassword": { "type": "securestring" },
        "resourcePrefix": { "type": "string" }

    - Angiv et navn til den separate virtuelle maskine, der bruges til at få adgang til computere i skalaen.
    - Navnet på kontoen lagerplads, hvor skabelonen er gemt.
    - Antallet af virtuelle maskiner til opretter i sættet skala.
    - Brugernavn og adgangskode til administratorkonto på virtuelle maskiner.
    - Angiv et navnepræfiks for de ressourcer, der er oprettet for at understøtte skalaen.

3. Variabler kan bruges i en skabelon til at angive værdier, der kan blive ændret ofte eller værdier, der skal oprettes en kombination af parameterværdier. Tilføje disse variabler under det variabler overordnede element, du har føjet til skabelonen.

        "dnsName1": "[concat(parameters('resourcePrefix'),'dn1')]",
        "dnsName2": "[concat(parameters('resourcePrefix'),'dn2')]",
        "publicIP1": "[concat(parameters('resourcePrefix'),'ip1')]",
        "publicIP2": "[concat(parameters('resourcePrefix'),'ip2')]",
        "loadBalancerName": "[concat(parameters('resourcePrefix'),'lb1')]",
        "virtualNetworkName": "[concat(parameters('resourcePrefix'),'vn1')]",
        "nicName": "[concat(parameters('resourcePrefix'),'nc1')]",
        "lbID": "[resourceId('Microsoft.Network/loadBalancers',variables('loadBalancerName'))]",
        "frontEndIPConfigID": "[concat(variables('lbID'),'/frontendIPConfigurations/loadBalancerFrontEnd')]",
        "storageAccountSuffix": [ "a", "g", "m", "s", "y" ],
        "diagnosticsStorageAccountName": "[concat(parameters('resourcePrefix'), 'a')]",
        "accountid": "[concat('/subscriptions/',subscription().subscriptionId,'/resourceGroups/', resourceGroup().name,'/providers/','Microsoft.Storage/storageAccounts/', variables('diagnosticsStorageAccountName'))]",
          "wadlogs": "<WadCfg> <DiagnosticMonitorConfiguration overallQuotaInMB=\"4096\" xmlns=\"http://schemas.microsoft.com/ServiceHosting/2010/10/DiagnosticsConfiguration\"> <DiagnosticInfrastructureLogs scheduledTransferLogLevelFilter=\"Error\"/> <WindowsEventLog scheduledTransferPeriod=\"PT1M\" > <DataSource name=\"Application!*[System[(Level = 1 or Level = 2)]]\" /> <DataSource name=\"Security!*[System[(Level = 1 or Level = 2)]]\" /> <DataSource name=\"System!*[System[(Level = 1 or Level = 2)]]\" /></WindowsEventLog>",
        "wadperfcounter": "<PerformanceCounters scheduledTransferPeriod=\"PT1M\"><PerformanceCounterConfiguration counterSpecifier=\"\\Processor(_Total)\\% Processor Time\" sampleRate=\"PT15S\" unit=\"Percent\"><annotation displayName=\"CPU utilization\" locale=\"en-us\"/></PerformanceCounterConfiguration></PerformanceCounters>",
        "wadcfgxstart": "[concat(variables('wadlogs'),variables('wadperfcounter'),'<Metrics resourceId=\"')]",
        "wadmetricsresourceid": "[concat('/subscriptions/',subscription().subscriptionId,'/resourceGroups/',resourceGroup().name ,'/providers/','Microsoft.Compute/virtualMachineScaleSets/',parameters('vmssName'))]",
        "wadcfgxend": "[concat('\"><MetricAggregation scheduledTransferPeriod=\"PT1H\"/><MetricAggregation scheduledTransferPeriod=\"PT1M\"/></Metrics></DiagnosticMonitorConfiguration></WadCfg>')]"

  - DNS-navne, der bruges af netværksgrænseflader.
    - IP-adresse navne og præfikser for virtuelt netværk og undernet.
    - Navne og id'erne for det virtuelle netværk og indlæse belastningsjusteringstjenesten og netværksgrænseflader.
    - Lagerplads kontonavne for de konti, der er knyttet til computere i skalaen angive.
    - Indstillinger for filtypenavnet diagnosticering, der er installeret på de virtuelle computere. Se [oprette en Windows virtuel maskine overvågning og diagnosticering ved hjælp af Azure ressourcestyring skabelon](../virtual-machines/virtual-machines-windows-extensions-diagnostics-template.md)kan finde flere oplysninger om filtypenavnet diagnosticering.

4. Tilføje lagerplads konto ressourcen under det overordnede element til ressourcer, som du har føjet til skabelonen. Denne skabelon bruger en løkke til at oprette de anbefalede fem lagerplads konti hvor operativsystemet diske og diagnosticering data er gemt. Dette sæt af konti kan understøtte op til 100 virtuelle maskiner i et sæt af skalering, som er det aktuelle maksimum. Hver lagerplads konto hedder med et bogstav sortering, der er defineret i de variabler, kombineres med præfikset, du giver i parametrene til skabelonen.

        {
          "type": "Microsoft.Storage/storageAccounts",
          "name": "[concat(parameters('resourcePrefix'), variables('storageAccountSuffix')[copyIndex()])]",
          "apiVersion": "2015-06-15",
          "copy": {
            "name": "storageLoop",
            "count": 5
          },
          "location": "[resourceGroup().location]",
          "properties": { "accountType": "Standard_LRS" }
        },

5. Føje virtuelt netværksressourcen. Du kan finde yderligere oplysninger finder [Netværk ressource udbyder](../virtual-network/resource-groups-networking.md).

        {
          "apiVersion": "2015-06-15",
          "type": "Microsoft.Network/virtualNetworks",
          "name": "[variables('virtualNetworkName')]",
          "location": "[resourceGroup().location]",
          "properties": {
            "addressSpace": { "addressPrefixes": [ "10.0.0.0/16" ] },
            "subnets": [
              {
                "name": "subnet1",
                "properties": { "addressPrefix": "10.0.0.0/24" }
              }
            ]
          }
        },

6. Tilføj de offentlige IP-adresse ressourcer, der bruges til justering af belastning og netværksgrænsefladen.

        {
          "apiVersion": "2016-03-30",
          "type": "Microsoft.Network/publicIPAddresses",
          "name": "[variables('publicIP1')]",
          "location": "[resourceGroup().location]",
          "properties": {
            "publicIPAllocationMethod": "Dynamic",
            "dnsSettings": {
              "domainNameLabel": "[variables('dnsName1')]"
            }
          }
        },
        {
          "apiVersion": "2016-03-30",
          "type": "Microsoft.Network/publicIPAddresses",
          "name": "[variables('publicIP2')]",
          "location": "[resourceGroup().location]",
          "properties": {
            "publicIPAllocationMethod": "Dynamic",
            "dnsSettings": {
              "domainNameLabel": "[variables('dnsName2')]"
            }
          }
        },

7. Føje Indlæs belastningsjusteringstjenesten ressourcen, der bruges af sættet skala. Du kan finde yderligere oplysninger finder [Azure ressourcestyring Support til justering af belastning](../load-balancer/load-balancer-arm.md).

        {
          "apiVersion": "2015-06-15",
          "name": "[variables('loadBalancerName')]",
          "type": "Microsoft.Network/loadBalancers",
          "location": "[resourceGroup().location]",
          "dependsOn": [
            "[concat('Microsoft.Network/publicIPAddresses/', variables('publicIP1'))]"
          ],
          "properties": {
            "frontendIPConfigurations": [
              {
                "name": "loadBalancerFrontEnd",
                "properties": {
                  "publicIPAddress": {
                    "id": "[concat('Microsoft.Network/publicIPAddresses/', variables('publicIP1'))]"
                  }
                }
              }
            ],
            "backendAddressPools": [ { "name": "bepool1" } ],
            "inboundNatPools": [
              {
                "name": "natpool1",
                "properties": {
                  "frontendIPConfiguration": {
                    "id": "[variables('frontEndIPConfigID')]"
                  },
                  "protocol": "tcp",
                  "frontendPortRangeStart": 50000,
                  "frontendPortRangeEnd": 50500,
                  "backendPort": 3389
                }
              }
            ]
          }
        },

8. Føje netværk interface ressourcen, der bruges af separat virtuelt. Da computere i et skala sæt ikke er tilgængelige via en offentlig IP-adresse, oprettes en separat virtuel maskine i det samme virtuelle netværk til fra en fjernplacering access på computere.

        {
          "apiVersion": "2016-03-30",
          "type": "Microsoft.Network/networkInterfaces",
          "name": "[variables('nicName')]",
          "location": "[resourceGroup().location]",
          "dependsOn": [
            "[concat('Microsoft.Network/publicIPAddresses/', variables('publicIP2'))]",
            "[concat('Microsoft.Network/virtualNetworks/', variables('virtualNetworkName'))]"
          ],
          "properties": {
            "ipConfigurations": [
              {
                "name": "ipconfig1",
                "properties": {
                  "privateIPAllocationMethod": "Dynamic",
                  "publicIPAddress": {
                    "id": "[resourceId('Microsoft.Network/publicIPAddresses', variables('publicIP2'))]"
                  },
                  "subnet": {
                    "id": "[concat('/subscriptions/',subscription().subscriptionId,'/resourceGroups/',resourceGroup().name,'/providers/Microsoft.Network/virtualNetworks/',variables('virtualNetworkName'),'/subnets/subnet1')]"
                  }
                }
              }
            ]
          }
        },

9. Tilføje separat virtuelt i det samme netværk som sættet skala.

        {
          "apiVersion": "2016-03-30",
          "type": "Microsoft.Compute/virtualMachines",
          "name": "[parameters('vmName')]",
          "location": "[resourceGroup().location]",
          "dependsOn": [
            "storageLoop",
            "[concat('Microsoft.Network/networkInterfaces/', variables('nicName'))]"
          ],
          "properties": {
            "hardwareProfile": { "vmSize": "Standard_A1" },
            "osProfile": {
              "computername": "[parameters('vmName')]",
              "adminUsername": "[parameters('adminUsername')]",
              "adminPassword": "[parameters('adminPassword')]"
            },
            "storageProfile": {
              "imageReference": {
                "publisher": "MicrosoftWindowsServer",
                "offer": "WindowsServer",
                "sku": "2012-R2-Datacenter",
                "version": "latest"
              },
              "osDisk": {
                "name": "[concat(parameters('resourcePrefix'), 'os1')]",
                "vhd": {
                  "uri":  "[concat('https://',parameters('resourcePrefix'),'a.blob.core.windows.net/vhds/',parameters('resourcePrefix'),'os1.vhd')]"
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
            }
          }
        },

10. Føje virtuelt skalaen angive ressource og angive filtypenavnet diagnosticering, der er installeret på alle virtuelle maskiner i sættet skala. Mange af indstillingerne for denne ressource er de samme med virtuelt ressourcen. De vigtigste forskelle er elementet kapacitet, der angiver antallet af virtuelle maskiner i skala sæt og upgradePolicy, der angiver, hvordan opdateringer er foretaget virtuelle computere. Sættet skala er ikke oprettet, indtil alle lagerplads konti er oprettet som angivet med dependsOn element.

            {
              "type": "Microsoft.Compute/virtualMachineScaleSets",
              "apiVersion": "2016-03-30",
              "name": "[parameters('vmSSName')]",
              "location": "[resourceGroup().location]",
              "dependsOn": [
                "storageLoop",
                "[concat('Microsoft.Network/virtualNetworks/', variables('virtualNetworkName'))]",
                "[concat('Microsoft.Network/loadBalancers/', variables('loadBalancerName'))]"
              ],
              "sku": {
                "name": "Standard_A1",
                "tier": "Standard",
                "capacity": "[parameters('instanceCount')]"
              },
              "properties": {
                "upgradePolicy": {
                  "mode": "Manual"
                },
                "virtualMachineProfile": {
                  "storageProfile": {
                    "osDisk": {
                      "vhdContainers": [
                        "[concat('https://', parameters('resourcePrefix'), variables('storageAccountSuffix')[0], '.blob.core.windows.net/vhds')]",
                        "[concat('https://', parameters('resourcePrefix'), variables('storageAccountSuffix')[1], '.blob.core.windows.net/vhds')]",
                        "[concat('https://', parameters('resourcePrefix'), variables('storageAccountSuffix')[2], '.blob.core.windows.net/vhds')]",
                        "[concat('https://', parameters('resourcePrefix'), variables('storageAccountSuffix')[3], '.blob.core.windows.net/vhds')]",
                        "[concat('https://', parameters('resourcePrefix'), variables('storageAccountSuffix')[4], '.blob.core.windows.net/vhds')]"
                      ],
                      "name": "vmssosdisk",
                      "caching": "ReadOnly",
                      "createOption": "FromImage"
                    },
                    "imageReference": {
                      "publisher": "MicrosoftWindowsServer",
                      "offer": "WindowsServer",
                      "sku": "2012-R2-Datacenter",
                      "version": "latest"
                    }
                  },
                  "osProfile": {
                    "computerNamePrefix": "[parameters('vmSSName')]",
                    "adminUsername": "[parameters('adminUsername')]",
                    "adminPassword": "[parameters('adminPassword')]"
                  },
                  "networkProfile": {
                    "networkInterfaceConfigurations": [
                      {
                        "name": "networkconfig1",
                        "properties": {
                          "primary": "true",
                          "ipConfigurations": [
                            {
                              "name": "ip1",
                              "properties": {
                                "subnet": {
                                  "id": "[concat('/subscriptions/',subscription().subscriptionId,'/resourceGroups/',resourceGroup().name,'/providers/Microsoft.Network/virtualNetworks/',variables('virtualNetworkName'),'/subnets/subnet1')]"
                                },
                                "loadBalancerBackendAddressPools": [
                                  {
                                    "id": "[concat('/subscriptions/',subscription().subscriptionId,'/resourceGroups/',resourceGroup().name,'/providers/Microsoft.Network/loadBalancers/',variables('loadBalancerName'),'/backendAddressPools/bepool1')]"
                                  }
                                ],
                                "loadBalancerInboundNatPools": [
                                  {
                                    "id": "[concat('/subscriptions/',subscription().subscriptionId,'/resourceGroups/',resourceGroup().name,'/providers/Microsoft.Network/loadBalancers/',variables('loadBalancerName'),'/inboundNatPools/natpool1')]"
                                  }
                                ]
                              }
                            }
                          ]
                        }
                      }
                    ]
                  },
                  "extensionProfile": {
                    "extensions": [
                      {
                        "name": "Microsoft.Insights.VMDiagnosticsSettings",
                        "properties": {
                          "publisher": "Microsoft.Azure.Diagnostics",
                          "type": "IaaSDiagnostics",
                          "typeHandlerVersion": "1.5",
                          "autoUpgradeMinorVersion": true,
                          "settings": {
                            "xmlCfg": "[base64(concat(variables('wadcfgxstart'),variables('wadmetricsresourceid'),variables('wadcfgxend')))]",
                            "storageAccount": "[variables('diagnosticsStorageAccountName')]"
                          },
                          "protectedSettings": {
                            "storageAccountName": "[variables('diagnosticsStorageAccountName')]",
                            "storageAccountKey": "[listkeys(variables('accountid'), '2015-06-15').key1]",
                            "storageAccountEndPoint": "https://core.windows.net"
                          }
                        }
                      }
                    ]
                  }
                }
              }
            },

11. Føje autoscaleSettings ressourcen, der definerer, hvordan sættet skala justerer baseret på processor brugen på computere i sættet skala.

            {
              "type": "Microsoft.Insights/autoscaleSettings",
              "apiVersion": "2015-04-01",
              "name": "[concat(parameters('resourcePrefix'),'as1')]",
              "location": "[resourceGroup().location]",
              "dependsOn": [
                "[concat('Microsoft.Compute/virtualMachineScaleSets/',parameters('vmSSName'))]"
              ],
              "properties": {
                "enabled": true,
                "name": "[concat(parameters('resourcePrefix'),'as1')]",
                "profiles": [
                  {
                    "name": "Profile1",
                    "capacity": {
                      "minimum": "1",
                      "maximum": "10",
                      "default": "1"
                    },
                    "rules": [
                      {
                        "metricTrigger": {
                          "metricName": "\\Processor(_Total)\\% Processor Time",
                          "metricNamespace": "",
                          "metricResourceUri": "[concat('/subscriptions/',subscription().subscriptionId,'/resourceGroups/',resourceGroup().name,'/providers/Microsoft.Compute/virtualMachineScaleSets/',parameters('vmSSName'))]",
                          "timeGrain": "PT1M",
                          "statistic": "Average",
                          "timeWindow": "PT5M",
                          "timeAggregation": "Average",
                          "operator": "GreaterThan",
                          "threshold": 50.0
                        },
                        "scaleAction": {
                          "direction": "Increase",
                          "type": "ChangeCount",
                          "value": "1",
                          "cooldown": "PT5M"
                        }
                      }
                    ]
                  }
                ],
                "targetResourceUri": "[concat('/subscriptions/',subscription().subscriptionId,'/resourceGroups/', resourceGroup().name,'/providers/Microsoft.Compute/virtualMachineScaleSets/',parameters('vmSSName'))]"
              }
            }

    Disse værdier er vigtige for dette selvstudium:

    - **metricName** - denne værdi er den samme som tælleren ydeevne, som vi definerede i variablen wadperfcounter. Ved hjælp af denne variabel filtypenavnet diagnosticering indsamler den **Processor(_Total)\% processortid** tæller.
    - **metricResourceUri** - denne værdi er resource identifier for sættet virtuelt skala.
    - **timeGrain** – denne værdi er granularitet målene, der er indsamlet. I denne skabelon, er det angivet til et minut.
    - **Statistik** – denne værdi bestemmer, hvordan målene kombineres for at tage højde for den automatiske skalering handling. De mulige værdier er: gennemsnit, Min og Max. I denne skabelon, der indsamles den gennemsnitlige samlede CPU-brug af virtuelle maskiner.
    - **timeWindow** – denne værdi er området af tid, der indsamles forekomst af data. Det skal være mellem 5 minutter og 12 timer.
    - **timeAggregation** – denne værdi bestemmer, hvordan de data, der indsamles skal kombineres med tiden. Standardværdien er gennemsnit. De mulige værdier er: gennemsnit, Minimum, maksimum, sidste, Total, antal.
    - **operatoren** – denne værdi er den operator, der bruges til at sammenligne metriske dataene og i grænseværdi for. De mulige værdier er: er lig med, NotEquals, GreaterThan, GreaterThanOrEqual, LessThan, LessThanOrEqual.
    - **grænseværdi for** – denne værdi er den værdi, der udløser handlingen skala. I denne skabelon føjes maskiner til skalaen Angiv, når den gennemsnitlige CPU-brug mellem computere i sættet er mere end 50%.
    - **retning** – denne værdi angiver den handling, der udføres, når grænseværdien opnås. De mulige værdier er Forøg eller Formindsk. I denne skabelon øges antallet af virtuelle maskiner i sættet skala, hvis grænsen er mere end 50% i den angivne tidsramme.
    - **type** – denne værdi er typen handling, der skal udføres, og skal være indstillet til ChangeCount.
    - **værdi** – denne værdi er antallet af virtuelle maskiner, der er tilføjet eller fjernet fra sættet skala. Denne værdi skal være 1 eller større. Standardværdien er 1. I denne skabelon Angiv antallet computere i skalaen øges ved 1, når grænsen er opfyldt.
    - **cooldown** – denne værdi er mængden tid der skal ventes siden den sidste skalering handling, før den næste handling opstår. Denne værdi skal være mellem et minut og en uge.

12. Gem skabelonfilen.    

## <a name="step-4-upload-the-template-to-storage"></a>Trin 4: Overføre skabelonen til lager

Skabelonen kan overføres, så længe du kender navn og primær nøgle af kontoen lagerplads, du oprettede i trin 1.

1.  Angive en variabel, der angiver navnet på lagerplads-konto, du oprettede i trin 1 i Microsoft Azure PowerShell-vinduet.

            $storageAccountName = "vmstestsa"

2.  Angive en variabel, der angiver den primære nøgle til kontoen, lagerplads.

            $storageAccountKey = "<primary-account-key>"

    Du kan få denne tast ved at klikke på ikonet produktnøglen, når du får vist lagerplads konto ressourcen i portalen Azure.

3.  Oprette objektet lagerplads konto kontekst, der bruges til at validere handlinger med kontoen lagerplads.

            $ctx = New-AzureStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storageAccountKey

4.  Oprette objektbeholder til lagring af skabelonen.

            $containerName = "templates"
            New-AzureStorageContainer -Name $containerName -Context $ctx  -Permission Blob

5.  Overføre skabelonfilen til den nye beholder.

            $blobName = "VMSSTemplate.json"
            $fileName = "C:\" + $BlobName
            Set-AzureStorageBlobContent -File $fileName -Container $containerName -Blob  $blobName -Context $ctx

## <a name="step-5-deploy-the-template"></a>Trin 5: Installere skabelonen

Nu hvor du har oprettet skabelonen, kan du begynde at implementere ressourcerne. Brug denne kommando til at starte processen:

    New-AzureRmResourceGroupDeployment -Name "vmsstestdp1" -ResourceGroupName "vmsstestrg1" -TemplateUri "https://vmsstestsa.blob.core.windows.net/templates/VMSSTemplate.json"

Når du trykker på Indtast, du bliver bedt om at angive værdier for de variabler, du har tildelt. Angive disse værdier:

    vmName: vmsstestvm1
      vmSSName: vmsstest1
      instanceCount: 5
      adminUserName: vmadmin1
      adminPassword: VMpass1
      resourcePrefix: vmsstest

Det skal tage 15 minutter for alle ressourcerne, der skal installeres korrekt.

>[AZURE.NOTE] Du kan også bruge på portalen mulighed for at installere ressourcerne. Brug dette link: "https://portal.azure.com/#create/Microsoft.Template/uri/<link to VM Scale Set JSON template>"

## <a name="step-6-monitor-resources"></a>Trin 6: Overvåge ressourcer

Du kan få oplysninger om virtuelt skala sæt ved hjælp af følgende metoder:

 - Portalen Azure - kan du i øjeblikket få en begrænset mængde oplysninger ved hjælp af portalen.
 - [Azure ressource Explorer](https://resources.azure.com/) - dette værktøj er bedst for udforske den aktuelle tilstand for dit skala sæt. Følg denne sti og skal du se afsnittet visningen forekomst af sættet skala, du har oprettet.

        subscriptions > {your subscription} > resourceGroups > vmsstestrg1 > providers > Microsoft.Compute > virtualMachineScaleSets > vmsstest1 > virtualMachines

 - Azure PowerShell - Brug denne kommando for at få nogle oplysninger:

        Get-AzureRmVmss -ResourceGroupName "resource group name" -VMScaleSetName "scale set name"

        Or

        Get-AzureRmVmss -ResourceGroupName "resource group name" -VMScaleSetName "scale set name" -InstanceView

 - Oprette forbindelse til separat virtuelt, ligesom du ville gøre en hvilken som helst anden computer og derefter kan du få fjernadgang til de virtuelle maskiner i det målestoksforhold, der er angivet til at overvåge individuelle processer.

>[AZURE.NOTE] En komplet REST-API for at få oplysninger om skala sæt kan findes i [Virtuelt skala angiver](https://msdn.microsoft.com/library/mt589023.aspx)

## <a name="step-7-remove-the-resources"></a>Trin 7: Fjerne ressourcen

Fordi du betaler for ressourcer, der bruges i Azure, er det altid er en god ide at slette ressourcer, som ikke længere skal bruges. Du behøver ikke at slette hver ressource separat fra en ressourcegruppe. Du kan slette ressourcegruppen og alle dens ressourcer slettes automatisk.

    Remove-AzureRmResourceGroup -Name vmsstestrg1

Hvis du vil beholde dine ressourcegruppe, kan du slette det målestoksforhold kun er angivet.

    Remove-AzureRmVmss -ResourceGroupName "resource group name" –VMScaleSetName "scale set name"

## <a name="next-steps"></a>Næste trin

- Administrere gruppen, du lige har oprettet ved hjælp af oplysningerne i [administrere virtuelle maskiner i et virtuelt skala angive](virtual-machine-scale-sets-windows-manage.md)skala.
- Få flere oplysninger om lodret skalering ved at gennemgå [lodret Autoskalering med virtuelt skala datasæt](virtual-machine-scale-sets-vertical-scale-reprovision.md)
- Finde eksempler på Azure skærm overvågningsfunktioner i [Azure skærm PowerShell Hurtig start eksempler](../monitoring-and-diagnostics/insights-powershell-samples.md)
- Få mere at vide om besked om funktioner i [Brug af Autoskalering handlinger for at sende mail og webhook påmindelser i Azure skærm](../monitoring-and-diagnostics/insights-autoscale-to-webhook-email.md)
- Lær, hvordan du kan [bruge overvågningslog logfiler til at sende mail og webhook påmindelser i Azure skærm](../monitoring-and-diagnostics/insights-auditlog-to-webhook-email.md)
