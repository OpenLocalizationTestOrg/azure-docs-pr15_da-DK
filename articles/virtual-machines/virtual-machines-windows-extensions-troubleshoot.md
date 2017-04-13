<properties
   pageTitle="Fejlfinding i forbindelse med Windows VM lokalnummer fejl | Microsoft Azure"
   description="Få mere at vide om fejlfinding i forbindelse med Azure Windows VM lokalnummer fejl"
   services="virtual-machines-windows"
   documentationCenter=""
   authors="kundanap"
   manager="timlt"
   editor=""
   tags="top-support-issue,azure-resource-manager"/>

<tags
   ms.service="virtual-machines-windows"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="vm-windows"
   ms.workload="infrastructure-services"
   ms.date="03/29/2016"
   ms.author="kundanap"/>

# <a name="troubleshooting-azure-windows-vm-extension-failures"></a>Fejlfinding i forbindelse med Azure Windows VM lokalnummer fejl

[AZURE.INCLUDE [virtual-machines-common-extensions-troubleshoot](../../includes/virtual-machines-common-extensions-troubleshoot.md)]

## <a name="viewing-extension-status"></a>Få vist status for lokalnummer
Azure ressourcestyring skabeloner kan udføres fra Azure PowerShell. Når skabelonen, der udføres, kan lokalnummer status ses fra Azure ressource Stifinder eller værktøjerne kommandolinjen.

Her er et eksempel:

Azure PowerShell:

      Get-AzureRmVM -ResourceGroupName $RGName -Name $vmName -Status

Her er et eksempel på output:

      Extensions:  {
      "ExtensionType": "Microsoft.Compute.CustomScriptExtension",
      "Name": "myCustomScriptExtension",
      "SubStatuses": [
        {
          "Code": "ComponentStatus/StdOut/succeeded",
          "DisplayStatus": "Provisioning succeeded",
          "Level": "Info",
          "Message": "    Directory: C:\\temp\\n\\n\\nMode                LastWriteTime     Length Name
              \\n----                -------------     ------ ----                              \\n-a---          9/1/2015   2:03 AM         11
              test.txt                          \\n\\n",
                      "Time": null
          },
        {
          "Code": "ComponentStatus/StdErr/succeeded",
          "DisplayStatus": "Provisioning succeeded",
          "Level": "Info",
          "Message": "",
          "Time": null
        }
    }
  ]

## <a name="troubleshooting-extension-failures"></a>Fejlfinding i forbindelse med filtypenavnet fejl

### <a name="re-running-the-extension-on-the-vm"></a>Køre igen filtypenavnet på VM

Hvis du kører scripts på VM ved hjælp af brugerdefineret Script filtypenavn, kan du nogle gange støde på en fejl, hvor VM blev oprettet, men scriptet mislykkes. Under disse betingelser er den anbefalede måde at gendanne fra denne fejl fjerne filtypenavnet og køre skabelonen igen.
Bemærk: fremover, denne funktion kan forbedres for at fjerne behovet for afinstallation filtypenavnet.


#### <a name="remove-the-extension-from-azure-powershell"></a>Fjerne filtypenavnet fra Azure PowerShell

    Remove-AzureRmVMExtension -ResourceGroupName $RGName -VMName $vmName -Name "myCustomScriptExtension"

Når filtypenavnet er blevet fjernet, kan skabelonen, der udføres igen til at køre scripts på VM.
