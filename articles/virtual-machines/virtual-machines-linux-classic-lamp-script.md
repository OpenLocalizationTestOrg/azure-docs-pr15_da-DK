<properties
    pageTitle="Brug filtypenavnet CustomScript på en Linux VM | Microsoft Azure"
    description="Lær, hvordan du bruger filtypenavnet CustomScript til at implementere programmer på Linux virtuelle maskiner i Azure, der er oprettet ved hjælp af den klassiske implementeringsmodel."
    editor="tysonn"
    manager="timlt"
    documentationCenter=""
    services="virtual-machines-linux"
    authors="gbowerman"
    tags="azure-service-management"/>

<tags
    ms.service="virtual-machines-linux"
    ms.workload="multiple"
    ms.tgt_pltfrm="linux"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/13/2016"
    ms.author="guybo"/>

#<a name="deploy-a-lamp-app-using-the-azure-customscript-extension-for-linux"></a>Installere en lys app med filtypenavnet Azure CustomScript til Linux#

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]


Microsoft Azure CustomScript-udvidelse til Linux er en måde at tilpasse din virtuelle maskiner (VM'er) ved at køre kode, der er skrevet i en hvilken som helst scriptsproget, der understøttes af VM (for eksempel Python og fest). Dette er en meget fleksibel metode til at automatisere programmet installation til flere computere.

Du kan installere filtypenavnet CustomScript ved hjælp af portalen Azure klassisk, Windows PowerShell eller kommandolinjen Azure (Azure CLI).

I denne artikel, bruger vi oprettet Azure CLI til at implementere en enkelt lys program til en Ubuntu VM ved hjælp af den klassiske implementeringsmodel.

## <a name="prerequisites"></a>Forudsætninger

I dette eksempel skal du først oprette to Azure FOS med Ubuntu 14.04 eller nyere. FOS kaldes *script-vm* og *lys vm*. Brug entydige navne, når du opretter FOS. En bruges til at køre kommandoerne CLI og en bruges til at installere appen lys.

Du kan også bruge en Azure-lager-konto og en tast for at få adgang til den (du kan få ud fra portalen Azure klassisk).

Hvis du har brug for hjælp til at oprette Linux FOS på Azure referere til at [oprette et virtuelt kører Linux](virtual-machines-linux-classic-createportal.md).

Kommandoerne Installer forudsætter Ubuntu, men du kan tilpasse installationen for alle understøttede Linux distro.

Script-vm VM skal have Azure CLI installeret med en fungerende forbindelse til Azure. Se [installere og konfigurere kommandolinjen Azure](../xplat-cli-install.md)for hjælp til dette.

## <a name="upload-a-script"></a>Overføre et script

Vi bruger filtypenavnet CustomScript til at køre et script på en ekstern VM installere lys stablen og oprette en PHP side. For at få adgang til scriptet overalt overføre vi det som en Azure blob.

### <a name="script-overview"></a>Oversigt over script

Eksemplet script installerer en lys stak til Ubuntu (herunder konfiguration af en installation af MySQL), skriver en enkel PHP fil og starter Apache.

    #!/bin/bash
    # set up a silent install of MySQL
    dbpass="mySQLPassw0rd"

    export DEBIAN_FRONTEND=noninteractive
    echo mysql-server-5.6 mysql-server/root_password password $dbpass | debconf-set-selections
    echo mysql-server-5.6 mysql-server/root_password_again password $dbpass | debconf-set-selections

    # install the LAMP stack
    apt-get -y install apache2 mysql-server php5 php5-mysql  

    # write some PHP
    echo \<center\>\<h1\>My Demo App\</h1\>\<br/\>\</center\> > /var/www/html/phpinfo.php
    echo \<\?php phpinfo\(\)\; \?\> >> /var/www/html/phpinfo.php

    # restart Apache
    apachectl restart

### <a name="upload-script"></a>Overføre script

Gem scriptet som en tekstfil, for eksempel *install_lamp.sh*, og Overfør det derefter til Azure-lager. Du kan gøre det nemt med Azure CLI. I følgende eksempel uploader filen til en objektbeholder til lagring med navnet "scripts". Hvis objektbeholderen ikke findes skal du oprette den første.

    azure storage blob upload -a <yourStorageAccountName> -k <yourStorageKey> --container scripts ./install_lamp.sh

Også oprette en JSON-fil, der beskriver, hvordan du henter scriptet fra Azure-lager. Gemme den som *public_config.json* (erstatte "mystorage" med navnet på kontoen lagerplads):

    {"fileUris":["https://mystorage.blob.core.windows.net/scripts/install_lamp.sh"], "commandToExecute":"sh install_lamp.sh" }


## <a name="deploy-the-extension"></a>Installere filtypenavnet

Nu kan du bruge den næste kommando til at udrulle filtypenavnet Linux CustomScript til remote VM ved hjælp af Azure CLI.

    azure vm extension set -c "./public_config.json" lamp-vm CustomScript Microsoft.Azure.Extensions 2.0

Den forrige kommando downloader og kører scriptet *install_lamp.sh* på VM kaldet *lys vm*.

Da appen omfatter en webserver, skal du huske at åbne en HTTP lytte port på remote VM med den næste kommando.

    azure vm endpoint create -n Apache -o tcp lamp-vm 80 80

## <a name="monitoring-and-troubleshooting"></a>Overvågning og fejlfinding

Du kan se, om hvordan godt brugerdefineret script kører ved at kigge på logfilen på remote VM. SSH *lys vm* og hale logfilen med den næste kommando.

    cd /var/log/azure/customscript
    tail -f handler.log

Når du har kørt filtypenavnet CustomScript, kan du gå til siden PHP, du har oprettet for oplysninger. Siden PHP for eksemplet i denne artikel er *http://lamp-vm.cloudapp.net/phpinfo.php*.

## <a name="additional-resources"></a>Yderligere ressourcer

Du kan bruge de samme grundlæggende trin til at udrulle mere komplekse apps. I dette eksempel blev Installer scriptet gemt som en offentlig blob i Azure-lager. Valgmuligheden mere sikker ville være at gemme Installer scriptet som en sikker blob med en [Sikker adgang signatur](https://msdn.microsoft.com/library/azure/ee395415.aspx) (SAS).

Yderligere ressourcer til Azure CLI, Linux og filtypenavnet CustomScript vises næste.

[Automatisere Linux VM tilpasningsopgaver ved hjælp af CustomScript udvidelse](https://azure.microsoft.com/blog/2014/08/20/automate-linux-vm-customization-tasks-using-customscript-extension/)

[Azure Linux Extensions (GitHub)](https://github.com/Azure/azure-linux-extensions)

[Linux og Open Source-Computing på Azure](virtual-machines-linux-opensource-links.md)
