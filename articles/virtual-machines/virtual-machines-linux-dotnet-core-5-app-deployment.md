<properties
   pageTitle="Automatisere program-installation med virtuelt filtypenavne | Microsoft Azure"
   description="Azure virtuelt DotNet Core selvstudium"
   services="virtual-machines-linux"
   documentationCenter="virtual-machines"
   authors="neilpeterson"
   manager="timlt"
   editor="tysonn"
   tags="azure-service-management"/>

<tags
   ms.service="virtual-machines-linux"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="vm-linux"
   ms.workload="infrastructure"
   ms.date="09/21/2016"
   ms.author="nepeters"/>

# <a name="application-deployment-with-azure-resource-manager-templates"></a>Program-installation med Azure ressourcestyring skabeloner

Når alle Azure infrastructural krav har identificeret og oversættes til en skabelon til distribution, skal den faktiske anvendelse installation behandles. Programmet Installation her refererer til installation af de faktiske anvendelse binære filer til Azure ressourcer. Musik Store eksemplet ved .net Core, NGINX og administratoren skal være installeret og konfigureret på hver virtuelt. De musik Store binære filer skal være installeret på den virtuelle maskine og musik Store databasen allerede oprettet.

Dette dokument indeholder oplysninger om hvordan virtuelt filtypenavne kan automatisere programmet installation og konfiguration Azure virtuelle maskiner. Alle afhængigheder og unikke konfigurationer er fremhævet. Implementere en forekomst af løsningen til Azure-abonnement og arbejde sammen med skabelonen Azure Ressourcestyring for den bedste oplevelse. Skabelonen fuldført kan findes her – [Musik Store installation på Ubuntu](https://github.com/Microsoft/dotnet-core-sample-templates/tree/master/dotnet-core-music-linux).

## <a name="configuration-script"></a>Af konfigurationsscript

Virtuelt udvidelser er specialiserede programmer, der udføres mod virtuelle maskiner til at levere konfiguration automatisering. Filtypenavne er tilgængelige for mange bestemt formål som anti-virus, logføring konfiguration og Docker konfiguration. Filtypenavnet brugerdefineret script kan bruges til at køre en hvilken som helst script mod en virtuel maskine. Det er op til filtypenavnet brugerdefineret script til at konfigurere Ubuntu virtuelle maskiner og installere programmet musik Store med eksempel musik Store.

Undersøge det script, der køres før der beskriver, hvordan virtuelt filtypenavne angives i en skabelon til Azure ressourcestyring. Dette script konfigurerer virtuelt Ubuntu at være vært for programmet musik Store. Når kører, installerer scriptet alle, det er nødvendigt software, installerer programmet musik store fra versionsstyring og forberede databasen. 

Mere at vide om vært for en .net Core-programmet på Linux, skal du se [Udgiv til et Linux produktionsmiljø](https://docs.asp.net/en/latest/publishing/linuxproduction.html). 

> Dette eksempel er til demonstration.

```none
#!/bin/bash

# install dotnet core
sudo sh -c 'echo "deb [arch=amd64] https://apt-mo.trafficmanager.net/repos/dotnet-release/ trusty main" > /etc/apt/sources.list.d/dotnetdev.list'
sudo apt-key adv --keyserver apt-mo.trafficmanager.net --recv-keys 417A0893
sudo apt-get update
sudo apt-get install -y dotnet-dev-1.0.0-preview2-003121

# download application
sudo wget https://raw.github.com/Microsoft/dotnet-core-sample-templates/master/dotnet-core-music-linux/music-app/music-store-azure-demo-pub.tar /
sudo mkdir /opt/music
sudo tar -xf music-store-azure-demo-pub.tar -C /opt/music

# install nginx, update config file
sudo apt-get install -y nginx
sudo service nginx start
sudo touch /etc/nginx/sites-available/default
sudo wget https://raw.githubusercontent.com/Microsoft/dotnet-core-sample-templates/master/dotnet-core-music-linux/music-app/nginx-config/default -O /etc/nginx/sites-available/default
sudo cp /opt/music/nginx-config/default /etc/nginx/sites-available/
sudo nginx -s reload

# update and secure music config file
sed -i "s/<replaceserver>/$1/g" /opt/music/config.json
sed -i "s/<replaceuser>/$2/g" /opt/music/config.json
sed -i "s/<replacepass>/$3/g" /opt/music/config.json
sudo chown $2 /opt/music/config.json
sudo chmod 0400 /opt/music/config.json

# config supervisor
sudo apt-get install -y supervisor
sudo touch /etc/supervisor/conf.d/music.conf
sudo wget https://raw.githubusercontent.com/Microsoft/dotnet-core-sample-templates/master/dotnet-core-music-linux/music-app/supervisor/music.conf -O /etc/supervisor/conf.d/music.conf
sudo service supervisor stop
sudo service supervisor start

# pre-create music store database
/usr/bin/dotnet /opt/music/MusicStore.dll &
```

## <a name="vm-script-extension"></a>VM Script lokalnummer

VM filtypenavne kan kun køres mod en virtuel maskine på build tid ved at medtage lokalnummer ressourcen i skabelonen Azure ressourcestyring. Filtypenavnet kan tilføjes med guiden Tilføj ressource til Visual Studio eller ved at indsætte gyldige JSON i skabelonen. Script lokalnummer ressourcen er indlejret i virtuelt ressource. Dette kan ses i følgende eksempel.

Følg dette link for at få vist eksemplet JSON i skabelonen ressourcestyring – [VM Script filtypenavn](https://github.com/Microsoft/dotnet-core-sample-templates/blob/master/dotnet-core-music-linux/azuredeploy.json#L359). 

Bemærk i den under JSON, scriptet er gemt i GitHub. Dette script gemmes også i Azure Blob-lager. Azure ressourcestyring skabeloner Tillad desuden script udførelse af strengen til opbygget så skabelon parameterværdier kan bruges som parametre til udførelse af script. I dette tilfælde data angives, når du installerer skabelonerne, og kan derefter bruges disse værdier, når du kører scriptet.

```none
{
  "apiVersion": "2015-06-15",
  "type": "extensions",
  "name": "config-app",
  "location": "[resourceGroup().location]",
  "dependsOn": [
    "[concat('Microsoft.Compute/virtualMachines/', concat(variables('vmName'),copyindex()))]"
  ],
  "tags": {
    "displayName": "config-app"
  },
  "properties": {
    "publisher": "Microsoft.Azure.Extensions",
    "type": "CustomScript",
    "typeHandlerVersion": "2.0",
    "autoUpgradeMinorVersion": true,
    "settings": {
      "fileUris": [
        "https://raw.githubusercontent.com/Microsoft/dotnet-core-sample-templates/master/dotnet-core-music-linux/scripts/config-music.sh"
      ]
    },
    "protectedSettings": {
      "commandToExecute": "[concat('sudo sh config-music.sh ',variables('musicStoreSqlName'), ' ', parameters('adminUsername'), ' ', parameters('sqlAdminPassword'))]"
    }
  }
}
```

Du kan finde flere oplysninger om brug af filtypenavnet brugerdefineret script, [brugerdefineret scriptfiltypenavne med ressourcestyring skabeloner](./virtual-machines-linux-extensions-customscript.md).

## <a name="next-step"></a>Næste trin

<hr>

[Udforske mere Azure ressourcestyring skabeloner](https://github.com/Azure/azure-quickstart-templates)
