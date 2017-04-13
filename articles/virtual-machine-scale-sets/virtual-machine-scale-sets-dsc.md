<properties
   pageTitle="Ved hjælp af beskedteksten stat konfiguration med virtuelt skala sæt | Microsoft Azure"
   description="Ved hjælp af Virtual Machine skala angiver med filtypenavnet Azure DTK"
   services="virtual-machine-scale-sets"
   documentationCenter=""
   authors="zjalexander"
   manager="timlt"
   editor=""
   tags="azure-service-management,azure-resource-manager"
   keywords=""/>

<tags
   ms.service="virtual-machine-scale-sets"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="vm-windows"
   ms.workload="na"
   ms.date="09/15/2016"
   ms.author="zachal"/>

# <a name="using-virtual-machine-scale-sets-with-the-azure-dsc-extension"></a>Ved hjælp af Virtual Machine skala angiver med filtypenavnet Azure DTK

[Virtuelt skala sæt (VMSS)](virtual-machine-scale-sets-overview.md) kan bruges med [Azure beskedteksten tilstand konfiguration (DTK)](../virtual-machines/virtual-machines-windows-extensions-dsc-overview.md) lokalnummer handler. VMSS giver en metode til at installere og administrere stort antal virtuelle maskiner og elastically kan skalere ind og ud i svar at indlæse. DTK bruges til at konfigurere FOS som de er online, så de kører fremstilling softwaren.

## <a name="differences-between-deploying-to-vm-and-vmss"></a>Forskelle mellem at implementere VM og VMSS

Den underliggende skabelonstruktur for VMSS er en smule anderledes end en enkelt VM. Nærmere betegnet kan installerer en enkelt VM udvidelser under noden "virtualMachines". Der er en post af typen "filtypenavne" hvor DTK er føjet til skabelonen

```
"resources": [
          {
              "name": "Microsoft.Powershell.DSC",
              "type": "extensions",
              "location": "[resourceGroup().location]",
              "apiVersion": "2015-06-15",
              "dependsOn": [
                  "[concat('Microsoft.Compute/virtualMachines/', variables('vmName'))]"
              ],
              "tags": {
                  "displayName": "dscExtension"
              },
              "properties": {
                  "publisher": "Microsoft.Powershell",
                  "type": "DSC",
                  "typeHandlerVersion": "2.20",
                  "autoUpgradeMinorVersion": false,
                  "forceUpdateTag": "[parameters('dscExtensionUpdateTagVersion')]",
                  "settings": {
                      "configuration": {
                          "url": "[concat(parameters('_artifactsLocation'), '/', variables('dscExtensionArchiveFolder'), '/', variables('dscExtensionArchiveFileName'))]",
                          "script": "DscExtension.ps1",
                          "function": "Main"
                      },
                      "configurationArguments": {
                          "nodeName": "[variables('vmName')]"
                      }
                  },
                  "protectedSettings": {
                      "configurationUrlSasToken": "[parameters('_artifactsLocationSasToken')]"
                  }
              }
          }
      ]
```

En VMSS node har en "" egenskabssektion med "VirtualMachineProfile", "extensionProfile"-attributten. DTK er tilføjet under "udvidelser"

```
"extensionProfile": {
            "extensions": [
                {
                    "name": "Microsoft.Powershell.DSC",
                    "properties": {
                        "publisher": "Microsoft.Powershell",
                        "type": "DSC",
                        "typeHandlerVersion": "2.20",
                        "autoUpgradeMinorVersion": false,
                        "forceUpdateTag": "[parameters('DscExtensionUpdateTagVersion')]",
                        "settings": {
                            "configuration": {
                                "url": "[concat(parameters('_artifactsLocation'), '/', variables('DscExtensionArchiveFolder'), '/', variables('DscExtensionArchiveFileName'))]",
                                "script": "DscExtension.ps1",
                                "function": "Main"
                            },
                            "configurationArguments": {
                                "nodeName": "localhost"
                            }
                        },
                        "protectedSettings": {
                            "configurationUrlSasToken": "[parameters('_artifactsLocationSasToken')]"
                        }
                    }
                }
            ]
```

## <a name="behavior-for-vmss"></a>Funktionsmåden for VMSS

Funktionsmåden for VMSS er identisk med funktionsmåden for en enkelt VM. Når der oprettes en ny VM, er det automatisk klargjort med filtypenavnet DTK. Hvis der kræves en nyere version af WMF ved filtypenavnet skal genstartes VM før kommer online. Når det er online, DTK konfiguration .zip-overførsler og klargøre det på VM. Flere oplysninger kan findes i [Oversigten Azure DTK filtypenavn](../virtual-machines/virtual-machines-windows-extensions-dsc-overview.md).

## <a name="next-steps"></a>Næste trin ##
Undersøge [Azure ressourcestyring skabelon til filtypenavnet DTK](../virtual-machines/virtual-machines-windows-extensions-dsc-template.md).

Få mere at vide hvordan [DTK lokalnummer håndterer sikkert legitimationsoplysninger](../virtual-machines/virtual-machines-windows-extensions-dsc-credentials.md). 

Se [Introduktion til Azure beskedteksten tilstand konfiguration lokalnummer handler](../virtual-machines/virtual-machines-windows-extensions-dsc-overview.md)kan finde flere oplysninger om Azure DTK lokalnummer handler. 

Du kan finde flere oplysninger om PowerShell DTK, [ved at besøge PowerShell dokumentation center](https://msdn.microsoft.com/powershell/dsc/overview). 


