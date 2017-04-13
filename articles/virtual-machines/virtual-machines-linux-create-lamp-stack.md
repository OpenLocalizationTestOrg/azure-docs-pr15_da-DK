<properties
    pageTitle="Installere lys på en Linux virtuel maskine | Microsoft Azure"
    description="Lær, hvordan du installerer lys stablen på en Linux VM"
    services="virtual-machines-linux"
    documentationCenter="virtual-machines"
    authors="jluk"
    manager="timlt"
    editor=""
    tags="azure-resource-manager"/>

<tags
    ms.service="virtual-machines-linux"
    ms.workload="infrastructure-services"
    ms.tgt_pltfrm="vm-linux"
    ms.devlang="NA"
    ms.topic="article"
    ms.date="06/07/2016"
    ms.author="juluk"/>

# <a name="deploy-lamp-stack-on-azure"></a>Installere lys stak på Azure
I denne artikel fører dig gennem hvordan du kan installere en Apache webserver, MySQL og PHP (lys stablen) på Azure. Du skal bruge en Azure-konto ([hente en gratis prøveversion](https://azure.microsoft.com/pricing/free-trial/)) og [Azure CLI](../xplat-cli-install.md) , der er [knyttet til din Azure konto](../xplat-cli-connect.md).

Der er to metoder til installation af lys, der er dækket i denne artikel:

## <a name="quick-command-summary"></a>Kommandoen hurtige oversigt

1) Installere lys på nye VM

```
# One command to create a resource group holding a VM with LAMP already on it
$ azure group create -n uniqueResourceGroup -l westus --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/lamp-app/azuredeploy.json
```

2) Installere lys på eksisterende VM

```
# Two commands: one updates packages, the other installs Apache, MySQL, and PHP
user@ubuntu$ sudo apt-get update
user@ubuntu$ sudo apt-get install apache2 mysql-server php5 php5-mysql
```

## <a name="deploy-lamp-on-new-vm-walkthrough"></a>Installere lys på nye VM gennemgang

Du kan starte med at oprette en ny [ressourcegruppe](../azure-resource-manager/resource-group-overview.md) , der indeholder VM:

    $ azure group create uniqueResourceGroup westus
    info:    Executing command group create
    info:    Getting resource group uniqueResourceGroup
    info:    Creating resource group uniqueResourceGroup
    info:    Created resource group uniqueResourceGroup
    data:    Id:                  /subscriptions/########-####-####-####-############/resourceGroups/uniqueResourceGroup
    data:    Name:                uniqueResourceGroup
    data:    Location:            westus
    data:    Provisioning State:  Succeeded
    data:    Tags: null
    data:
    info:    group create command OK

Hvis du vil oprette VM selve, kan du bruge en allerede skrevet Azure ressourcestyring skabelon findes [her på GitHub](https://github.com/Azure/azure-quickstart-templates/tree/master/lamp-app).

    $ azure group deployment create --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/lamp-app/azuredeploy.json uniqueResourceGroup uniqueLampName

Du burde se svar spørge nogle flere oplysninger:

    info:    Executing command group deployment create
    info:    Supply values for the following parameters
    storageAccountNamePrefix: lampprefix
    location: westus
    adminUsername: someUsername
    adminPassword: somePassword
    mySqlPassword: somePassword
    dnsLabelPrefix: azlamptest
    info:    Initializing template configurations and parameters
    info:    Creating a deployment
    info:    Created template deployment "uniqueLampName"
    info:    Waiting for deployment to complete
    data:    DeploymentName     : uniqueLampName
    data:    ResourceGroupName  : uniqueResourceGroup
    data:    ProvisioningState  : Succeeded
    data:    Timestamp          :
    data:    Mode               : Incremental
    data:    CorrelationId      : d51bbf3c-88f1-4cf3-a8b3-942c6925f381
    data:    TemplateLink       : https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/lamp-app/azuredeploy.json
    data:    ContentVersion     : 1.0.0.0
    data:    DeploymentParameters :
    data:    Name                      Type          Value
    data:    ------------------------  ------------  -----------
    data:    storageAccountNamePrefix  String        lampprefix
    data:    location                  String        westus
    data:    adminUsername             String        someUsername
    data:    adminPassword             SecureString  undefined
    data:    mySqlPassword             SecureString  undefined
    data:    dnsLabelPrefix            String        azlamptest
    data:    ubuntuOSVersion           String        14.04.2-LTS
    info:    group deployment create command OK

Du har nu oprettet en Linux VM med lys, der allerede er installeret på den. Hvis du vil, kan du kontrollere installationen ved at springe ned til [bekræfte lys installeret].

## <a name="deploy-lamp-on-existing-vm-walkthrough"></a>Installere lys på eksisterende VM gennemgang

Hvis du har brug for hjælp til at oprette en Linux VM kan du hoved [her for at se, hvordan du opretter en Linux VM] (. / virtual-machines-linux-quick-create-cli.md). Derefter skal skal du SSH til Linux VM. Hvis du har brug for hjælp til at oprette en SSH nøgle kan du hoved [her for at se, hvordan du opretter en SSH nøgle på Linux/Mac] (. / virtual-machines-linux-mac-create-ssh-keys.md).
Hvis du allerede har en SSH nøgle, går videre og SSH din Linux VM med `ssh username@uniqueDNS`.

Nu hvor du arbejder i din Linux VM, kan vi fører dig gennem installerer lys stablen på Debian-baseret salgsdistributioner. Kommandoerne nøjagtige være forskellige for andre Linux distros.

#### <a name="installing-on-debianubuntu"></a>Installerer på Debian/Ubuntu

Du skal bruge følgende pakker installeret: `apache2`, `mysql-server`, `php5`, og `php5-mysql`. Du kan installere disse ved direkte fanger disse pakker eller bruge Tasksel. Instruktioner for begge muligheder vises nedenfor.
Før du installerer skal du hente og opdatere pakke lister.

    user@ubuntu$ sudo apt-get update
    
##### <a name="individual-packages"></a>Individuelle pakker
Ved hjælp af Hovedgaden få:

    user@ubuntu$ sudo apt-get install apache2 mysql-server php5 php5-mysql

##### <a name="using-tasksel"></a>Brug af Tasksel
Alternativt kan du hente Tasksel, et Debian/Ubuntu værktøj, der installeres flere relaterede pakker som en koordineret "opgave" på dit system.

    user@ubuntu$ sudo apt-get install tasksel
    user@ubuntu$ sudo tasksel install lamp-server

Når du kører en af indstillingerne ovenfor bliver du bedt om at installere disse pakker og et antal andre afhængigheder. Tryk på "j" og derefter på Enter for at fortsætte, og følg andre instruktionerne til at angive en administratoradgangskode for MySQL. Dette installerer de mindste nødvendige PHP filtypenavne behov for at bruge PHP med MySQL. 

![][1]

Kør følgende kommando for at se andre PHP udvidelser, der er tilgængelige som pakker:

    user@ubuntu$ apt-cache search php5


#### <a name="create-infophp-document"></a>Oprette info.php dokument

Du skulle nu være muligt at kontrollere, hvilken version af Apache, MySQL og PHP du har via kommandolinjen ved at skrive `apache2 -v`, `mysql -v`, eller `php -v`.

Hvis du vil teste yderligere, kan du oprette en hurtig PHP oplysninger side til at få vist i en browser. Oprette en ny fil med nanosystemer tekstredigeringsprogram med denne kommando:

    user@ubuntu$ sudo nano /var/www/html/info.php

Tilføj følgende linjer i tekstredigeringsprogrammet GNU nanosystemer:

    <?php
    phpinfo();
    ?>

Gem og afslut tekstredigeringsprogrammet.

Genstart Apache med denne kommando, så alle nye installationer træder i kraft.

    user@ubuntu$ sudo service apache2 restart

## <a name="verify-lamp-successfully-installed"></a>Bekræfte lys, der er installeret

Nu kan du kontrollere siden PHP oplysninger, du lige har oprettet i din browser ved at gå til http://youruniqueDNS/info.php, skal den ligne dette.

![][2]

Du kan se din Apache installation ved at få vist siden Apache2 Ubuntu standard ved at gå til du http://youruniqueDNS/. Du bør se nogenlunde sådan ud.

![][3]

Tillykke, du har kun konfigureret en lys stak på din Azure VM!

## <a name="next-steps"></a>Næste trin

Se i dokumentationen til Ubuntu på lys stablen:

- [https://help.Ubuntu.com/community/ApacheMySQLPHP](https://help.ubuntu.com/community/ApacheMySQLPHP)

[1]: ./media/virtual-machines-linux-deploy-lamp-stack/configmysqlpassword-small.png
[2]: ./media/virtual-machines-linux-deploy-lamp-stack/phpsuccesspage.png
[3]: ./media/virtual-machines-linux-deploy-lamp-stack/apachesuccesspage.png
