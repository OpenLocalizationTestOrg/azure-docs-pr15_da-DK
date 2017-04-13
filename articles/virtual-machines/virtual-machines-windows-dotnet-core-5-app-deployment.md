<properties
   pageTitle="Automatisere program-installation med virtuelt filtypenavne | Microsoft Azure"
   description="Azure virtuelt DotNet Core selvstudium"
   services="virtual-machines-windows"
   documentationCenter="virtual-machines"
   authors="neilpeterson"
   manager="timlt"
   editor="tysonn"
   tags="azure-resource-manager"/>

<tags
   ms.service="virtual-machines-windows"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="vm-windows"
   ms.workload="infrastructure-services"
   ms.date="10/21/2016"
   ms.author="nepeters"/>

# <a name="application-deployment-with-azure-resource-manager-templates"></a>Program-installation med Azure ressourcestyring skabeloner

Når alle Azure infrastructural krav har identificeret og oversættes til en skabelon til distribution, skal den faktiske anvendelse installation behandles. Programmet Installation her refererer til installation af de faktiske anvendelse binære filer til Azure ressourcer. Musik Store eksemplet ved .net Core og IIS skal være installeret og konfigureret på hver virtuelt. De musik Store binære filer skal være installeret på den virtuelle maskine og musik Store databasen allerede oprettet.

Dette dokument indeholder oplysninger om hvordan virtuelt filtypenavne kan automatisere programmet installation og konfiguration Azure virtuelle maskiner. Alle afhængigheder og unikke konfigurationer er fremhævet. Implementere en forekomst af løsningen til Azure-abonnement og arbejde sammen med skabelonen Azure Ressourcestyring for den bedste oplevelse. Skabelonen fuldført kan findes her – [Musik Store installation i Windows](https://github.com/Microsoft/dotnet-core-sample-templates/tree/master/dotnet-core-music-Windows).

## <a name="configuration-script"></a>Af konfigurationsscript

Virtuelt udvidelser er specialiserede programmer, der udføres mod virtuelle maskiner til at levere konfiguration automatisering. Filtypenavne er tilgængelige for mange bestemt formål som anti-virus, logføring konfiguration og Docker konfiguration. Filtypenavnet brugerdefineret Script kan bruges til at køre en hvilken som helst script mod en virtuel maskine. Det er op til filtypenavnet brugerdefineret script til at konfigurere Windows virtuelle maskiner og installere programmet musik Store med eksempel musik Store.

Undersøge det script, der køres før der beskriver, hvordan virtuelt filtypenavne angives i en skabelon til Azure ressourcestyring. Dette script konfigurerer virtuelt Windows for at være vært for programmet musik Store. Når kører, installerer scriptet alle, det er nødvendigt software, installerer programmet musik store fra versionsstyring og forberede databasen. 

> Dette eksempel er til demonstration.

```none
Param (
    [string]$user,
    [string]$password,
    [string]$sqlserver
)

# firewall
netsh advfirewall firewall add rule name="http" dir=in action=allow protocol=TCP localport=80

# folders
New-Item -ItemType Directory c:\temp
New-Item -ItemType Directory c:\music

# install iis
Install-WindowsFeature web-server -IncludeManagementTools

# install dot.net core sdk
Invoke-WebRequest http://go.microsoft.com/fwlink/?LinkID=615460 -outfile c:\temp\vc_redistx64.exe
Start-Process c:\temp\vc_redistx64.exe -ArgumentList '/quiet' -Wait
Invoke-WebRequest https://go.microsoft.com/fwlink/?LinkID=809122 -outfile c:\temp\DotNetCore.1.0.0-SDK.Preview2-x64.exe
Start-Process c:\temp\DotNetCore.1.0.0-SDK.Preview2-x64.exe -ArgumentList '/quiet' -Wait
Invoke-WebRequest https://go.microsoft.com/fwlink/?LinkId=817246 -outfile c:\temp\DotNetCore.WindowsHosting.exe
Start-Process c:\temp\DotNetCore.WindowsHosting.exe -ArgumentList '/quiet' -Wait

# download / config music app
Invoke-WebRequest  https://github.com/Microsoft/dotnet-core-sample-templates/raw/master/dotnet-core-music-windows/music-app/music-store-azure-demo-pub.zip -OutFile c:\temp\musicstore.zip
Expand-Archive C:\temp\musicstore.zip c:\music
(Get-Content C:\music\config.json) | ForEach-Object { $_ -replace "<replaceserver>", $sqlserver } | Set-Content C:\music\config.json
(Get-Content C:\music\config.json) | ForEach-Object { $_ -replace "<replaceuser>", $user } | Set-Content C:\music\config.json
(Get-Content C:\music\config.json) | ForEach-Object { $_ -replace "<replacepass>", $password } | Set-Content C:\music\config.json

# workaround for db creation bug
Start-Process 'C:\Program Files\dotnet\dotnet.exe' -ArgumentList 'c:\music\MusicStore.dll'

# configure iis
Remove-WebSite -Name "Default Web Site"
Set-ItemProperty IIS:\AppPools\DefaultAppPool\ managedRuntimeVersion ""
New-Website -Name "MusicStore" -Port 80 -PhysicalPath C:\music\ -ApplicationPool DefaultAppPool
& iisreset
```

## <a name="vm-script-extension"></a>VM Script lokalnummer

VM filtypenavne kan kun køres mod en virtuel maskine på build tid ved at medtage lokalnummer ressourcen i skabelonen Azure ressourcestyring. Filtypenavnet kan tilføjes med guiden Tilføj ressource til Visual Studio eller ved at indsætte gyldige JSON i skabelonen. Script lokalnummer ressourcen er indlejret i virtuelt ressource. Dette kan ses i følgende eksempel.

Følg dette link for at få vist eksemplet JSON i skabelonen ressourcestyring – [VM Script filtypenavn](https://github.com/Microsoft/dotnet-core-sample-templates/blob/master/dotnet-core-music-windows/azuredeploy.json#L339). 

Bemærk i den under JSON, scriptet er gemt i GitHub. Dette script gemmes også i Azure Blob-lager. Azure ressourcestyring skabeloner Tillad desuden script udførelse af strengen skal anlægges, så skabelon parameterværdier kan bruges som parametre til udførelse af script. I dette tilfælde data angives, når du installerer skabelonerne, og kan derefter bruges disse værdier, når du kører scriptet.

```none
{
  "apiVersion": "2015-06-15",
  "type": "extensions",
  "name": "config-app",
  "location": "[resourceGroup().location]",
  "dependsOn": [
    "[concat('Microsoft.Compute/virtualMachines/', variables('vmName'),copyindex())]",
    "[variables('musicstoresqlName')]"
  ],
  "tags": {
    "displayName": "config-app"
  },
  "properties": {
    "publisher": "Microsoft.Compute",
    "type": "CustomScriptExtension",
    "typeHandlerVersion": "1.4",
    "autoUpgradeMinorVersion": true,
    "settings": {
      "fileUris": [
        "https://raw.githubusercontent.com/Microsoft/dotnet-core-sample-templates/master/dotnet-core-music-windows/scripts/configure-music-app.ps1"
      ]
    },
    "protectedSettings": {
      "commandToExecute": "[concat('powershell -ExecutionPolicy Unrestricted -File configure-music-app.ps1 -user ',parameters('adminUsername'),' -password ',parameters('adminPassword'),' -sqlserver ',variables('musicstoresqlName'),'.database.windows.net')]"
    }
  }
}
```

Du kan finde flere oplysninger om brug af filtypenavnet brugerdefineret script, [brugerdefineret scriptfiltypenavne med ressourcestyring skabeloner](./virtual-machines-windows-extensions-customscript.md).

## <a name="next-step"></a>Næste trin

<hr>

[Udforske mere Azure ressourcestyring skabeloner](https://github.com/Azure/azure-quickstart-templates)
