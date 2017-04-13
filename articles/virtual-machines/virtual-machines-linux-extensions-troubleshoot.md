<properties
   pageTitle="Fejlfinding i forbindelse med Linux VM lokalnummer fejl | Microsoft Azure"
   description="Få mere at vide om fejlfinding i forbindelse med Azure Linux VM lokalnummer fejl"
   services="virtual-machines-linux"
   documentationCenter=""
   authors="kundanap"
   manager="timlt"
   editor=""
   tags="top-support-issue,azure-resource-manager"/>

<tags
   ms.service="virtual-machines-linux"
   ms.devlang="na"
   ms.topic="support-article"
   ms.tgt_pltfrm="vm-linux"
   ms.workload="infrastructure-services"
   ms.date="03/29/2016"
   ms.author="kundanap"/>

# <a name="troubleshooting-azure-linux-vm-extension-failures"></a>Fejlfinding i forbindelse med Azure Linux VM lokalnummer fejl

[AZURE.INCLUDE [virtual-machines-common-extensions-troubleshoot](../../includes/virtual-machines-common-extensions-troubleshoot.md)]

## <a name="viewing-extension-status"></a>Få vist status for lokalnummer
Azure ressourcestyring skabeloner kan udføres fra Azure CLI. Når skabelonen, der udføres, kan lokalnummer status ses fra Azure ressource Stifinder eller værktøjerne kommandolinjen.

Her er et eksempel:

Azure CLI:

      azure vm get-instance-view


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

## <a name="troubleshooting-extenson-failures"></a>Fejlfinding i forbindelse med Extenson-fejl:

### <a name="re-running-the-extension-on-the-vm"></a>Køre igen filtypenavnet på VM

Hvis du kører scripts på VM ved hjælp af brugerdefineret Script filtypenavn, kan du nogle gange støde på en fejl, hvor VM blev oprettet, men scriptet mislykkes. Under disse betingelser er den anbefalede måde at gendanne fra denne fejl fjerne filtypenavnet og køre skabelonen igen.
Bemærk: fremover, denne funktion kan forbedres for at fjerne behovet for afinstallation filtypenavnet.

#### <a name="remove-the-extension-from-azure-cli"></a>Fjerne filtypenavnet fra Azure CLI

      azure vm extension set --resource-group "KPRG1" --vm-name "kundanapdemo" --publisher-name "Microsoft.Compute.CustomScriptExtension" --name "myCustomScriptExtension" --version 1.4 --uninstall

Hvor "publsher-navn" svarer til typen lokalnummer fra outputtet af "azure vm get-forekomst-view" og navn er navnet på ressourcen, lokalnummer fra skabelonen

Når filtypenavnet er blevet fjernet, kan skabelonen, der udføres igen til at køre scripts på VM.
