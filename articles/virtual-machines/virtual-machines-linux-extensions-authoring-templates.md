<properties
   pageTitle="Redigering af skabeloner med Linux VM filtypenavne | Microsoft Azure"
   description="Få mere at vide om redigering Azure ressourcestyring skabeloner med udvidelser til Linux FOS"
   services="virtual-machines-linux"
   documentationCenter=""
   authors="kundanap"
   manager="timlt"
   editor=""
   tags="azure-resource-manager"/>

<tags
   ms.service="virtual-machines-linux"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="vm-linux"
   ms.workload="infrastructure-services"
   ms.date="03/29/2016"
   ms.author="kundanap"/>

# <a name="authoring-azure-resource-manager-templates-with-linux-vm-extensions"></a>Redigering Azure ressourcestyring skabeloner med Linux VM filtypenavne

[AZURE.INCLUDE [virtual-machines-common-extensions-authoring-templates](../../includes/virtual-machines-common-extensions-authoring-templates.md)]

Kør følgende commnad fra Azure CLI:

      Azure VM extension list

Denne kommando returnerer publisher navn, filtypenavn og version som følgende:

      Publisher                   : Microsoft.Azure.Extensions  
      ExtensionName               : DockerExtension
      Version                     : 1.0

Disse tre egenskaber Knyt til "publisher", "type" og "typeHandlerVersion" henholdsvis i ovenstående skabelon kodestykket.

>[AZURE.NOTE]Det anbefales altid for at bruge den nyeste version af udvidelse til at få den mest opdaterede funktionalitet.

## <a name="identifying-the-schema-for-the-extension-configuration-parameters"></a>Identificere skemaet for parametrene lokalnummer konfiguration

Næste trin med redigering af en skabelon til udvidelse er at identificere format for at give konfiguration af parametre. Hver udvidelse understøtter sit eget sæt parametre.

Hvis du vil se eksempel konfigurationer for Linux filtypenavne, skal du klikke på i dokumentationen til se [Linux eExtensions eksempler](virtual-machines-linux-extensions-configuration-samples.md).

Se følgende for at få en fuldt fuldført skabelon med VM.

[Brugerdefineret script lokalnummer på en Linux VM](https://github.com/Azure/azure-quickstart-templates/blob/b1908e74259da56a92800cace97350af1f1fc32b/mongodb-on-ubuntu/azuredeploy.json/)

Når du opretter skabelonen, kan du installere det ved hjælp af Azure CLI.
